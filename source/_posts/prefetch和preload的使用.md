---
title: prefetch和preload的使用
date: 2019-01-19 23:27:56
tags:
- prefetch
- preload
categories: 
- HTML
---
# 预加载
现在的网络情况虽然很乐观，在我们清楚地知道css/js的加载和解析对于dom解析和渲染的影响：
1. CSS 不会阻塞 DOM 的解析，但会阻塞 DOM 渲染。
2. JS 阻塞 DOM 解析，但浏览器会"偷看"DOM，预先下载相关资源，对于keeplive的链接能够进行复用并行下载资源。
3. 浏览器遇到 script且没有defer或async属性的 标签时，会触发页面渲染，因而如果前面CSS资源尚未加载完毕时，浏览器会等待它加载完毕在执行脚本。

但是如果可以进一步的提前资源的加载岂不是更好，在移动端的开发中预加载可以说有很多的实践；
1. hybird混合开发是可以在空闲时提前加载下一个页面的空壳webview
2. 对于一些必经路径的静态的页面也可以在空闲时提前加载
3. 对于按需加载的资源，也可以在空闲时天前加载

本次主要是考虑关于浏览器中支持的一些预加载和并行加载的处理。

## defer和async
当浏览器碰到 script 脚本的时候：

`<script src="script.js"></script>`

没有 defer 或 async，浏览器会立即加载并执行指定的脚本，“立即”指的是在渲染该 script 标签之下的文档元素之前，也就是说不等待后续载入的文档元素，读到就加载并执行。

`<script async src="script.js"></script>`

有 async，加载和渲染后续文档元素的过程将和 script.js 的加载与执行并行进行（异步）。

`<script defer src="myscript.js"></script>`

有 defer，加载后续文档元素的过程将和 script.js 的加载并行进行（异步），但是 script.js 的执行要在所有元素解析完成之后，DOMContentLoaded 事件触发之前完成。

然后从实用角度来说呢，首先把所有脚本都丢到 </body> 之前是最佳实践，因为对于旧浏览器来说这是唯一的优化选择，此法可保证非脚本的其他一切元素能够以最快的速度得到加载和解析。

<!-- more -->

接着，我们来看一张图咯：

![clipboard.png](http://huananimg.zanmeizhuen.com/blog/img/prefetch.png)

此图告诉我们以下几个要点：

1. defer 和 async 在网络读取（下载）这块儿是一样的，都是异步的（相较于 HTML 解析）
2. 它俩的差别在于脚本下载完之后何时执行，显然 defer 是最接近我们对于应用脚本加载和执行的要求的
3. 关于 defer，此图未尽之处在于它是按照加载顺序执行脚本的，这一点要善加利用
4. async 则是一个乱序执行的主，反正对它来说脚本的加载和执行是紧紧挨着的，所以不管你声明的顺序如何，只要它加载完了就会立刻执行
5. 仔细想想，async 对于应用脚本的用处不大，因为它完全不考虑依赖（哪怕是最低级的顺序执行），不过它对于那些可以不依赖任何脚本或不被任何脚本依赖的脚本来说却是非常合适的，

## preload和refetch
`preload`通常在页面中，我们需要加载一些脚本和样式，而使用 preload 可以对当前页面所需的脚本、样式等资源进行预加载，而无需等到解析到 script 和 link 标签时才进行加载。这一机制使得资源可以更早的得到加载并可用，且更不易阻塞页面的初步渲染，进而提升性能。

使用方式
将 link 标签的 rel 属性的值设为 preload，as 属性的值为资源类型（如脚本为 script，样式表为 style）
````
<head>
  <meta charset="utf-8">
  <title>preload example</title>
  <!-- 对 style.css 和 index.js 进行预加载 -->
  <link rel="preload" href="style.css" as="style">
  <link rel="preload" href="index.js" as="script">

  <link rel="stylesheet" href="style.css">
</head>

<body>
  <div id="app"></div>

  <script src="index.js"></script>
</body>
````
`prefetch`与 `preload` 一样，都是对资源进行预加载，但是 prefetch 加载的资源一般不是用于当前页面的，即未来很可能用到的这样一些资源，简单点说就是其他页面会用到的资源。当然，prefetch 不会像 preload 一样，在页面渲染的时候加载资源，而是利用浏览器空闲时间来下载。当进入下一页面，就可直接从 disk cache 里面取，既不影响当前页面的渲染，又提高了其他页面加载渲染的速度。

使用方式
同 `preload` 很相似，无需指定 as 属性：
````
<head>
  <meta charset="utf-8">
  <title>preload example</title>
  <!-- 对 style.css 和 index.js 进行 preload 预加载 -->
  <link rel="preload" href="style.css" as="style">
  <link rel="preload" href="index.js" as="script">

  <!-- 对资源进行 prefetch 预加载 -->
  <link rel="prefetch" href="next.css">
  <link rel="prefetch" href="next.js">

  <link rel="stylesheet" href="style.css">
</head>

<body>
  <div id="app"></div>

  <script src="index.js"></script>
</body>
````

**总结:对当前页面需要的资源，使用 preload 进行预加载，对其它页面需要的资源进行 prefetch 预加载。**

## Subresource和Prerender
`subresource`可以用来指定资源是最高优先级的。比如，在Chrome和Opera中我们可以加上下面的代码：

`<link rel="subresource" href="styles.css">`
>Chromium的文档这么解释：
和 "Link rel=prefetch"的语义不同，"Link rel=subresource"是一种新的连接关系。rel=prefetch指定了下载后续页面用到资源的低优先级，而rel=subresource则是指定当前页面资源的提前加载。

所以，如果资源是在当前页面需要，或者马上就会用到，则推荐用subresource，否则还是用prefetch。

`prerender`是一个重量级的选项，它可以让浏览器提前加载指定页面的所有资源。

`<link rel="prerender"  href="/thenextpage.html" />`
>Steve Souders的文章详细解释了这个技术：
prerender就像是在后台打开了一个隐藏的tab，会下载所有的资源、创建DOM、渲染页面、执行JS等等。如果用户进入指定的链接，隐藏的这个页面就会进入马上进入用户的视线。Google Search多年前就利用了这个特性实现了Instant Pages功能。微软最近也宣布会让Bing在IE11上用类似prerender的技术。

但是要注意，一定要在十分确定用户回点某个链接时才用这个特性，否则客户端就会无端的下载很多资源和渲染这个页面。
正如任何提前的动作一样，预判总是有一定风险出错。如果提前的动作是昂贵的（比如高CPU、耗电、占用带宽），就要谨慎使用了。虽然不容易预判用户会点进哪个页面，但还是存在一些典型的场景：

如果用户搜索到了一个明显正确的结果时，那么这个页面就很有可能被点入
如果用户在登录页面，那么登录成功后的页面就很可能接下来会被加载了
如果用户在阅读一个多页面的文章或者有页码的内容时，下一页就很可能会马上被点击了
利用Page Visibility API可以用来防止页面在还没真正展示给用户时就触发了JS的执行。

参考：
[defer和async](https://segmentfault.com/q/1010000000640869)
[prefetch与 preload](https://segmentfault.com/a/1190000016949393)
[prefetch预加载](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Link_prefetching_FAQ)
[preload当即加载](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Preloading_content)
[加载技术概述](http://www.alloyteam.com/2015/10/prefetching-preloading-prebrowsing/)
[dnc fetch](https://developer.mozilla.org/zh-CN/docs/Controlling_DNS_prefetching)

Prerender Subresource

