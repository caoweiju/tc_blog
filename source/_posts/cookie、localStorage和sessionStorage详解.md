---
title: cookie、localStorage和sessionStorage详解
date: 2019-03-11 14:44:13
tags:
- cookie
- Storage
categories: 
- JavaScript
---
# 前端本地存储
目前前端本地存储比较常见的方式有以下三种：
1. cookie
2. localStorage
3. sessionStorage

这里再一次清晰地记录下这其中的一些知识点；当然整个浏览器提供的缓存有很多方式：

1. 浏览器缓存（Browser Caching），通过request header来设置资源缓存
2. websql，不是HTML的规范，也在逐步废弃
3. IndexedDB 浏览器中非关系型数据库，数据存在浏览器安装的地方
4. cookie、localstorage、sessionStorage
5. application cache 通过定义缓存列表来离线缓存资源，逐步废弃中
6. cacheStorage：CacheStorage是在ServiceWorker的规范中定义的。CacheStorage 可以保存每个serverWorker申明的cache对象，cacheStorage有open、match、has、delete、keys五个核心方法，可以对cache对象的不同匹配进行不同的响应。

<!-- more -->

## cookie
 `cookie`是浏览器 提供的一种机制，它将`document` 对象的`cookie`属性提供给`JavaScript`。可以由`JavaScript`对其进行控制，而并不是`JavaScript`本身的性质。`Cookie`意为“甜饼”，是由`W3C`组织提出，最早由`Netscape`社区发展的一种机制。目前`Cookie`已经成为标准，所有的主流浏览器如`IE、Netscape、Firefox、Opera`等都支持`Cookie`。

由于`HTTP`是一种无状态的协议，服务器单从网络连接上无从知道客户身份。怎么办呢？就给客户端们颁发一个通行证吧，每人一个，无论谁访问都必须携带自己通行证。这样服务器就能从通行证上确认客户身份了。这就是`Cookie`的工作原理。

`Cookie`实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个`Cookie`。客户端浏览器会把`Cookie`保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该`Cookie`一同提交给服务器。服务器检查该`Cookie`，以此来辨认用户状态。服务器还可以根据需要修改`Cookie`的内容。

**注意：`cookie`是不可跨域的，不过对于相同顶级域名的二级域名还是可以通过设置`'domain'`的方法可以进行跨域的**

有些Cookie 是有限制的，一旦超过时间限制，就会被系统删除。很多人担心Cookie 会泄露用户的一些信息。但这是多余的，Cookie 是不能通过跨域来访问的，还有一些对象是不能脱离Cookie 来实现的，比如Session。这里还有一个点，就是客户端Cookie 数量最多为300个，每个不能超过4kb, 每个web站点设置的cookie 数量不能超过20个。


     
### 读取`cookie`
对于`cookie`的读取，最基本的就是`document.cookie`；需要注意以下几点：
1. `cookie`是相对于同域而言的，同一个域的`cookie`才是互通的【设置过特殊`domain`的`cookie`可以共享至同顶级域名的二级、三级域名下】；
2. 一般来说，只有服务器操作`Cookie` 才能保证一些必要的安全。但有时候，可能需要前端来增删改查 `Cookie`, 这个时候咱们的主角出现了——`HttpOnly`；`HttpOnly`是包含在`Set-Cookie` HTTP响应头文件中的附加标志。生成`cookie`时使用`HttpOnly`标志有助于降低客户端脚本访问受保护cookie的风险（如果浏览器支持）。如果某一个`Cookie` 选项被设置成 `HttpOnly = true` 的话，那此`Cookie` 只能通过服务器端修改，Js 是操作不了的，对于 `document.cookie` 来说是透明的
3. `document.cookie`获取的是一个字符串【写`cookie`时所有各种类型的值都会被强制转换为字符串】，`document.cookie`是当前域下的所有可读的`cookie`通过【`key=value`】的形式连接在一起，中间使用`;`分号来隔开；【`_app=qunar; _user=cwj; _test=sbsb`】,由于`cookie`中都是默认字符串，所以`key/value`不会存在`单引号/双引号`；

### 写入`cookie`
`cookie`可以通过服务端来设置，也可以通过前端来写入；写入的内容基本一致，只是写入的方式略有不同；最大的区别在于服务端可以设置`'HttpOnly'`这个来限制客户端对其进行修改；

客户端写入：
    document.cookie = newCookie;
`newCookie`是一个键值对形式的字符串。需要注意的是，用这个方法一次只能对一个cookie进行设置或更新。以下可选的cookie属性值可以跟在键值对后，用来具体化对cookie的设定/更新，使用分号以作分隔：
    ;path=path (例如 '/', '/mydir') 如果没有定义，默认为当前文档位置的路径。
    ;domain=domain (例如 'example.com'， '.example.com' (包括所有子域名),'subdomain.example.com') 如果没有定义，默认为当前文档位置的路径的域名部分。
    ;max-age=max-age-in-seconds (例如一年为60*60*24*365),这项设置比expires的优先级高；
    ;expires=date-in-GMTString-format 如果没有定义，cookie会在对话结束时【浏览器关闭后删除】过期，这个值的格式参见Date.toUTCString() 
    ;secure (cookie只通过https协议传输)

cookie的值字符串可以用encodeURIComponent()来保证它不包含任何逗号、分号或空格(cookie值中禁止使用这些值)。

**需要注意的是如果想删除一个`cookie`的话，只能是通过设置有效期为当前时间之前【expires】或者max-age为0**

## localStorage
`localStorage` 本地存储是`HTML5`的新技术，相比`cookie`，它的存储容量大，一般`cookie` 只有几K。给我们前端开发带来了方便，但是在实际的使用中，会发现，它远不是那么简单。有以下几大特性：
1. 永久存储单个域名存储量比较大（推荐5MB，各浏览器不同）总体数量无限制
2. 浏览器关闭不清除。页面之间可以实现共享。同一域名下实现共享，【顶级域名和二级/三级域名不共享】
3. 只要不清除。可以在本地永久储存。

简单读取方法：
    myStorage = localStorage;
返回一个 Storage 可被用于访问当前远端（ origin ）的本地存储空间的对象【包括了存储的键值对和length属性】。
>读取的时候有时候也会返回异常：SecurityError
请求违反了一个策略声明，或者远端（ origin ）不是 a valid scheme/host/port tuple （例如如果origin使用 file: 或者 data: 形式将可能发生）。比如，用户可以有禁用允许对指定的origin存留数据的浏览器配置。

### 使用方法
`localStorage`目前有五个API供使用：
1. `localStorage.getItem(key)` // 推荐的根据键key来读取值vakue的方法
1. `localStorage.setItem(key, value)` // 推荐的写入一键值对的方法
1. `localStorage.removeItem(key)` // 推荐的根据键key来删除某一键值对
1. `localStorage.clear()`   // 清空
1. `localStorage.key(index)`  // 根据索引值返回对应的key

其实`localStorage`可以像JavaScriptd的对象一样来使用，
    localStorage.a、localStorage.['a'] 和 localStorage.getItem(a) 效果一样，推荐使用最后者
    localStorage.a = 'aaa'、localStorage.['a']='aaa' 和 localStorage.setItem('a','aaa') 效果一样，推荐使用最后者
    
**注意：localStorage目前只支持存储字符串，其他格式均会被转换为字符串来存储，想要存储其他可以考虑JSON.parse/JSON.stringify配合使用**

## sessionStorage
>`sessionStorage` 属性允许你访问一个 `session Storage` 对象。它与 `localStorage` 相似，不同之处在于 `localStorage` 里面存储的数据没有过期时间设置，而存储在 `sessionStorage` 里面的数据在页面会话结束时会被清除。
页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会保持原来的页面会话。在新标签或窗口打开一个页面会初始化一个新的会话，这点和` session cookies` 的运行方式不同。
应该注意的是，无论是 `localStorage` 还是` sessionStorage` 中保存的数据都仅限于该页面的协议。

使用方法完全和`localStorage`完全一样；因为都是都是Web Storage API 的接口实现；
>作为 Web Storage API 的接口，Storage 提供了访问特定域名下的会话存储（`session storage`）或本地存储（`local storage`）的功能，例如，可以添加、修改或删除存储的数据项。
如果你想要操作一个域名的会话存储（`session storage`），可以使用 Window.sessionStorage；如果想要操作一个域名的本地存储（`local storage`），可以使用 Window.localStorage。

**注意：和`localStorage`区别仅在于存在的时间，同时刷新和恢复当前页面的话，`sessionStorage`都会保持原有的，但是新开页面就会初始化一个新的`sessionStorage`**