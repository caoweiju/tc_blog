---
title: web缓存详解
date: 2019-03-15 14:42:03
tags:
- cache
categories: 
- Web
---

# web浏览器缓存
没有缓存的世界，每次请求都是严格意义上的新的请求，那么服务器端的压力可想而知，客户端的响应速度也是大打折扣，就是我们在写程序也知道需要缓存，把一些常用的数据缓存区来，避免每次都要重新读取，特别是前端在进行元素获取时，虽然链式调用让我们很舒服，但是如果每次都需要重新获取这个元素的话，最好还是使用变量缓存起来比较好。

## 设置缓存
目前比较常见的缓存机制包括以下几种[都是设置http header]：
* Expires
* Cache-Control
* Last-Modified/If-Modified-Since：Last-Modified/If-Modified-Since要配合Cache-Control使用。
* Etag/If-None-Match：Etag/If-None-Match也要配合Cache-Control使用

还有一些升级设置缓存内容：
- cookie
- localSatorage、sessionStorage
- indexedDB
- application cache
- service worker + cache storage
<!-- more -->

一个实际资源访问的响应头部部分信息如下：
````
cache-control:max-age=691200
content-encoding:gzip
content-type:application/javascript
date:Sat, 05 Aug 2017 13:27:45 GMT
etag:W/"59845e75-2cfb5"
expires:Sat, 12 Aug 2017 11:47:47 GMT
last-modified:Fri, 04 Aug 2017 11:45:57 GMT
````
### Expires
Expires是http1.0提出的一个表示资源过期时间的header，它描述的是一个绝对时间，由服务器返回，用GMT格式的字符串表示，如：Expires:Thu, 31 Dec 2016 23:55:55 GMT，
````
new Date()
Sat Aug 05 2017 21:39:11 GMT+0800 (CST) //东八区，使用toUTCString转换为GMT格式
new Date().toUTCString()
"Sat, 05 Aug 2017 13:43:39 GMT"   //格林威治时区 0
````
这个时候，发现有个问题，因为时间是服务端返回的，也就是说前端其实很少自己来设置缓存头部的，node作为后端时例外，问题是如果前后端存在时差，或者时间不一致的时候，只能抓瞎了，误差会比较大，这个时候第二种方案出来了；
### Cache-Control
Cache-Control描述的是一个相对时间，在进行缓存命中的时候，都是利用客户端时间进行判断，所以相比较Expires，Cache-Control的缓存管理更有效，安全一些。
    读取缓存数据条件：上次缓存时间（客户端的）+max-age < 当前时间（客户端的）
Cache-Control值可以是public、private、no-cache、no- store、no-transform、must-revalidate、proxy-revalidate、max-age
    各个消息中的指令含义如下：
    Public指示响应可被任何缓存区缓存。
    Private指示对于单个用户的整个或部分响应消息，不能被共享缓存处理。这允许服务器仅仅描述当前用户的部分响应消息，此响应消息对于其他用户的请求无效。
    no-cache指示请求或响应消息不能缓存，该选项并不是说可以设置”不缓存“，而是需要和服务器确认
    no-store在请求消息中发送将使得请求和响应消息都不使用缓存，完全不存下來。
    max-age指示客户机可以接收生存期不大于指定时间（以秒为单位）的响应。上次缓存时间（客户端的）+max-age（64200s）<客户端当前时间
    min-fresh指示客户机可以接收响应时间小于当前时间加上指定时间的响应。
    max-stale指示客户机可以接收超出超时期间的响应消息。如果指定max-stale消息的值，那么客户机可以接收超出超时期指定值之内的响应消息。
注意：这两个header[Cache-Control、Expires]可以只启用一个，也可以同时启用，当response header中，Expires和Cache-Control同时存在时且时间不一致时，Cache-Control优先级高于Expires；
### Last-Modified/If-Modified-Since
If-Modified-Since，和 Last-Modified 一样都是用于记录页面最后修改时间的 HTTP 头信息，只是 Last-Modified 是由服务器往客户端发送的 HTTP 头，而 If-Modified-Since 则是由客户端往服务器发送的头，

在浏览器第一次请求某一个URL时，服务器端的返回状态会是200，内容是你请求的资源，同时有一个Last-Modified的属性标记此文件在服务期端最后被修改的时间，格式类似这样：
Last-Modified: Fri, 12 May 2006 18:53:33 GMT

客户端第二次请求此URL时，根据 HTTP 协议的规定，浏览器会向服务器传送 If-Modified-Since 报头，询问该时间之后文件是否有被修改过：
If-Modified-Since: Fri, 12 May 2006 18:53:33 GMT
如果服务器端的资源没有变化，则自动返回 HTTP 304 （Not Changed.）状态码，内容为空，这样就节省了传输数据量。当服务器端代码发生改变或者重启服务器时，则重新发出资源，返回和第一次请求时类似。从而保证不向客户端重复发出资源，也保证当服务器有变化时，客户端能够得到最新的资源。
    注意：ast-Modified标注的最后修改只能精确到秒级，如果某些文件在1秒钟以内，被修改多次的话，它将不能准确标注文件的修改时间（无法及时更新文件）
    如果某些文件会被定期生成，当有时内容并没有任何变化，但Last-Modified却改变了，导致文件没法使用缓存，有可能存在服务器没有准确获取文件修改时间，或者与代理服务器时间不一致等情形（无法使用缓存）。
当与 If-None-Match 一同出现时，它会被忽略掉，除非服务器不支持 If-None-Match。
### Etag/If-None-Match
ETag是响应头，If-None-Match是请求头。Last-Modified / If-Modified-Since的主要缺点就是它只能精确到秒的级别，一旦在一秒的时间里出现了多次修改，那么Last-Modified / If-Modified-Since是无法体现的。相比较，ETag / If-None-Match没有使用时间作为判断标准，而是使用一个特征串。Etag把Web组件的特征串告诉客户端，客户端在下次请求此Web组件的时候，会把上次服务端响应的特征串作为If-None-Match的值发送给服务端，服务端可以通过这个值来判断是否需要从重新发送，如果不需要，就简单的发送一个304状态码，客户端将从缓存里直接读取所需的Web组件。

HTTP 协议规格说明定义ETag为“被请求变量的实体值” （参见 ------ 章节 14.19）。 另一种说法是，ETag是一个可以与Web资源关联的记号（token）。典型的Web资源可以一个Web页，但也可能是JSON或XML文档。服务器单独负责判断记号是什么及其含义，并在HTTP响应头中将其传送到客户端，以下是服务器端返回的格式：
ETag: "50b1c1d4f775c61:df3"
客户端的查询更新格式是这样的：
If-None-Match: W/"50b1c1d4f775c61:df3"
如果ETag没改变，则返回状态304然后不返回，这也和Last-Modified一样。本人测试Etag主要在断点下载时比较有用。

## 建议
当使用Expires / Cache-Control的时候，尽量给图片，样式表，脚本等设置一个足够大的缓存时间，如果在此期间，缓存文件有过修改，最简单的更新方式是改名或者 设置一个查询参数，比如开始图片名是logo.gif，如果做了一个新的图片，你想更新，可以把图片改名为logo_v2.gif，或者给图片设置一个查 询参数logo.gif?foobar，这样，浏览器就会去请求新的图片了。不过，并不是所有的Web组件都适合有一个大的缓存时间，比如html，就不 适合使用过大的缓存时间，否则你在缓存到期前，就没机会更新任何东西了。

使用Yslow的都知道，它不建议使用ETag，理由是Etag在分布式环境里，会给服务器造成不必要的压力，比如说在Apache里，Etag缺省是由 三个因素决定的：INode Size MTime，而同一个图片，在不同服务器上的INode是不同的，所以在两个服务器上先后请求同一个图片，会得到两次200，虽然我们可以通过设置 FileETag Size MTime来屏蔽INode，从而达到一次200，一次304的效果，但304也是要通过一次条件GET请求验证的，所以说，还是通过Expires / Cache-Control来设置一个足够大的缓存时间更划算一些，如此说来，对图片，样式表，脚本等静态内容而言，设置一个大的过期时间是绝对必要的， 而Etag和Last-Modified则不是必要的。
![图片描述](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/cache/process.png)

参考：https://segmentfault.com/a/1190000006741200
http://www.cnblogs.com/wrmfw/archive/2011/09/05/2166910.html
https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Modified-Since


