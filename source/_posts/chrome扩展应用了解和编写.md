---
title: chrome扩展应用了解和编写
date: 2019-01-14 23:30:03
tags:
- plugin
- chrome
categories: 
- tool
---
# chrome浏览器
1. `Chrome `浏览器追求的是全方位的快速体验。它不仅能飞快地从桌面上启动，而且能瞬间完成网页加载，还能以闪电般的速度运行网络应用。
2. ` Chrome` 浏览器整洁且直观。您可在同一位置进行搜索和导航，可随意排列标签页，既快捷又轻松。
3. 您不必成为安全专家即可放心地浏览网络。`Chrome `默认会为用户提供安全保护，并可供所有人轻松且安全地使用。
## 内置地址
所有内置地址列表：`chrome://chrome-urls/`
开发者常使用列表：
1. 开发者高级选项：`chrome://flags/`
2. `dns`缓存清除： `chrome://net-internals/#dns`
3. 扩展应用程序：`chrome://extensions/`
4. 侦测页面[包括外接安卓设备]：`chrome://inspect/#devices`

<!-- more -->

比较全的的列表：
````
（1）chrome://accessibility/
可达性分析，默认是关闭的，点击accessibility off后变成accessibility on|show accessibility tree，点击show accessibility tree显示分析树
（2）chrome://appcache-internals/
应用程序缓存，显示所有的应用程序缓存路径列表
（3）chrome://apps/
当前chrome安装的应用列表
（4）chrome://blob-internals/
当前内部的blob文件列表
（5）chrome://bookmarks/ 
书签管理器
（6）chrome://cache/
当前缓存文件的url列表，点击url可以看到对应的缓存文件内容，包括类型 编码 过期时间等概要信息，以及文件内容等具体信息，以二进制方式显示
（7）chrome://chrome/
chrome关于页面，显示当前chrome的版本信息。
（8）chrome://chrome-urls/
显示chrome可用的伪url列表
（9）chrome://components/
显示chrome的组件列表，可以点击"检测是否有更新"来检测当前组件是否有新版本，如果有，可以直接下载更新。
（10）chrome://conflicts/
模块冲突检测，会列出当前已加载到主进程中的所有模块，包括模块的数量、签名方，版本以及模块所在的位置
（11）chrome://copresence/
Google共存信息列表，显示共存信息，包括有效指令、传输的令牌、收到的令牌
（12）chrome://crashes/
显示当前chrome的崩溃报告，需要启用崩溃报告后才会显示。
https://support.google.com/chrome/answer/96817中说明了如何启用崩溃报告。
设置后需要重启chrome才能生效
（13）chrome://credits/
类似于演职员列表，是一份chrome使用的一些开源组件或工具的列表，包括各种工具的主页和授权文件。
但第一行的“Accessibility Audit library, from Accessibility Developer Tools”的主页居然链接的是一份未压缩的js源码，不知道是啥情况
（14）chrome://device-log/
设备日志，chrome://device-log/?refresh=秒数 可以自动刷新
（15）chrome://devices/
显示当前在网络中注册的设备，可以添加打印机到云打印
（16）chrome://discards/
丢弃的标签页面，标签页面按照感兴趣的程度由高到低排序。如果当前的物理内存超出运行内存后，最不感兴趣的tab页面可能被丢弃掉。
（17）chrome://dns/
如果当前显示DNS pre-resolution and TCP pre-connection is disabled.
则打开设置，勾选“预提取资源，以便更快速地加载网页”
然后刷新当前页面就可以看到相关页面预加载的分析数据列表了
（18）chrome://downloads/
下载内容页面
（19）chrome://extensions/
扩展程序列表
（20）chrome://flags/
实验性功能列表，可以在这里启用这些实验性的功能
（21）chrome://flash/
显示flash插件的版本信息，安装位置及显卡的一些具体信息，包括显卡版本号，GPU
（22）chrome://gcm-internals/
GCM（Google Cloud Messaging ）内部构建信息，包括签到、连接、注册、发送、接收相关的日志信息。
（23）chrome://gpu/
显示当前的GPU信息，包括图像功能的状态（各种功能的硬件加速）、Gpu内存buffer的状态
（24）chrome://help/
chrome关于页面，与chrome://chrome/是同样的效果
（25）chrome://histograms/
直方图，柱状图，显示了浏览器启动到上一个页面加载的状态统计数据，重新加载可以获取到当前页面加载数据。
（26）chrome://history/
历史记录页面，显示所有的浏览记录
（27）chrome://indexeddb-internals/
显示chrome的内部数据库的实例列表，包括所有的内部数据所在的路径，最后修改时间及数据库大小
（28）chrome://inspect/
检测设备，页面，扩展插件，应用程序等，在Pages标签中显示当前打开的所有tab页面，点击inspect直接跳转到该页面，并调起开发者工具。
（29）chrome://invalidations/
失效的调试信息，失效调试服务状态，注册的失效服务处理器等信息
（30）chrome://local-state/
本地状态，显示的是一个json格式的文件，包括了浏览器的很多状态信息，插件的详细信息
（31）chrome://media-internals/
当前的多媒体内部构建信息，如果当前使用的是浏览器内部的播放器而不是flash播放器的话，会在Players、Audio、Video Capture显示播放文件的详细信息，在Player中可以显示当前播放的适配的详细信息，包括音频、视频及播放器的内部状态信息。
（32）chrome://memory
会自动跳转到chrome://memory-redirect/，显示浏览器占用的总内存以及各个内部进程占用的内存，包括各个Tab页面占用的物理内存和虚拟内存数，包括私有内存，共享内存及总内存
（33）chrome://memory-internals/
内存内部详细信息，点击Update后可以获取到当前浏览内存的使用信息，包括一个json格式的数据。
下发有一个列表，可以显示出所有进程所占用的内存，以及V8引擎使用的和分配的内存。
（34）chrome://nacl/
NaCl的版本信息，包括NaCl的插件位置，版本信息
（35）chrome://net-internals
网络内部构件的信息。包括代理信息、时间信息、DNS信息。其中的DNS可以显示当前浏览器中缓存的DNS信息，包括过期时间
（36）chrome://newtab
打开一个新的标签页
（37）chrome://omnibox/
omnibox 的测试工具 ，omnibox API https://developer.chrome.com/extensions/omnibox
（38）chrome://password-manager-internals/
显示捕获到的密码管理日志。当这个页面被关闭掉，则已有的记录会被清除，并且不再捕获。
（39）chrome://plugins/
显示当前chrome中所使用的插件及版本信息，可以在该页面点击 停用 来停用某个插件
（40）chrome://policy/
显示已设置的政策，默认是没有的
（41）chrome://predictors/
显示预测列表，包括自动完成动作的预取列表，也就是在地址栏输入某些字母后，出现对应的完整地址的列表，还包括
（42）chrome://print/
浏览器打印页面
（43）chrome://profiler/
分析器，可以按照不同的条件分组及排序，主要应该是用于分析chrome的各种进程的内部信息
（44）chrome://quota-internals/
存储空间的配额信息，包括了三个tab页面，Summary、Usage&Quota、Data
Summary标签页显示的是一个磁盘总的空闲空间大小以及混合统计的一些数值
Usage&Quota 标签页显示的是临时的、持久化的和同步的数据库中的具体数据项
Data标签中点击Dump按钮可以生成一个json格式的文件，包含了前面两个tab中的统计信息和分类信息。
（45）chrome://serviceworker-internals/
serviceworker的内部组件，可以勾选选择框，进行调试
ServiceWorker的项目主页： https://www.chromium.org/blink/serviceworker
介绍视频：https://youtu.be/4uQMl7mFB6g
视频中介绍者的的代码：https://github.com/jakearchibald/simple-serviceworker-tutorial
w3c ServiceWorker的Demo：https://github.com/w3c-webmob/ServiceWorkersDemos
（46）chrome://settings/
chrome的设置页面
（47）chrome://signin-internals/
当前chrome登录者信息，包括账户状态的各种信息
（48）chrome://suggestions/
推荐信息，也就是打开新的tab页是默认显示的推荐网站的缩略图页面的那些网站，包含了网站的信息和过期时间。
（49）chrome://sync-internals/
chrome账户同步信息，包含了多个tab页面，首页显示的是同步的概要信息，包含同步相关的各种信息，包括上一次同步的时间，Token请求时间，接收时间等。
后面的tab按照同步的不同类型展示了不同信息，包括了同步的分类、事件等
（50）chrome://system/
系统的诊断数据，包括当前Chrome的版本信息、操作系统的版本信息、同步数据信息、数据压缩代理是否启用、内存使用概况信息、usb键盘检测信息。
（51）chrome://terms/
chrome服务条款
（52）chrome://thumbnails/
顶级网站的url，分为包含缩略图的和未包含的、聚焦和未聚焦，估计也是用于在新开tab页面做推荐时使用
（52）chrome://tracing/
可以在该页面录制chrome的跟踪信息，也可以使用监控
（53）chrome://translate-internals
翻译内部组件信息，此处包含了对于chrome翻译的设置，包括用户自定义的不需要自动翻译的网站列表，可以在此处进行编辑，删除
包括了当前翻译引擎的设置，以及翻译组件的日志信息，日志包括了检测日志、事件日志和错误日志，其中检测日志可以dump为json格式的文件。
追踪日志会将打开该页面后的浏览器访问的网页内容抓取出来，包括要翻译的文字内容列表
（54）chrome://user-actions/
用户操作列表，包括操作类型和发生时间。
（55）chrome://version/
显示当前chrome的版本信息，包括版本号、JavaScript 引擎版本号，Flash插件版本号，用户代理信息等
（56）chrome://view-http-cache/
显示当前http缓存的url列表，点击对应的url，可以打开缓存的文件，包含了文件的具体信息和二进制内容信息
（57）chrome://webrtc-internals/
webrtc内部组件信息。点击Create Dump按钮，可以下载当前的webrtc连接的数据信息和状态信息快照数据
（58）chrome://webrtc-logs/
webrtc日志信息
````
## 应用商店 
[扩展应用商店](https://chrome.google.com/webstore/category/extensions?hl=zh-CN)
包含了很多实用的扩展应用，
* `adblock`，广告过滤`开发人员小心你的本地调试代码也被拦截了`
* `Momentum` 新开页面的美化
* `Proxy SwitchyOmega` 浏览器代理设置
* 二维码小助手 当前页面的二维码生成器
* 捕捉网页截图 - `FireShot` 滚动全局页面截图


## 插件编写
参考[chrome扩展入门](https://developer.chrome.com/extensions/getstarted)编一个入门的插件；

扩展由不同但关联的组件构成。组件可以包括后台脚本，内容脚本，选项页面，UI元素和各种逻辑文件，根据扩展的具体功能加入需要的功能组件模块，其中使用的技术栈就是`HTML、JS、CSS`，而最后的扩展文件就是压缩的`HTML，CSS，JavaScript，图像和Web平台中使用的其他文件`得出的包，

扩展具有广泛的功能。他们不仅可以修改用户查看、交互的Web页面内容，甚至可以扩展和更改浏览器本身的行为【所以每个浏览器的扩展可能有点不一样】，

一个常见的`chrome扩展`会包含一下的一些内容：
* `manifest.json`清单文件【类似配置文件，表明权限、版本、名称、介绍、配置入口等信息】
* `ico` 【应用扩展商店和chrome浏览器上的工具栏展示的图标】
* `Background Script` 【后台脚本是扩展的事件处理程序;它包含对扩展重要的浏览器事件的侦听器。它处于休眠状态，直到事件被触发然后执行脚本逻辑。有效的后台脚本仅在需要时加载，在空闲时卸载】
* `UI Elements` 【UI界面就是正常的html页面，主要是交互中的弹窗等】
* `Content Script` 【这个就是操作用户打开的页面】
* `Options Page` 【扩展可供用户配置的页面，用于修改扩展中的一些默认配置参数】


![clipboard.png](http://huananimg.zanmeizhuen.com/blog/img/chrome_ext.png)

### 一个改变当前查看页面的背景色的扩展插件实现

想要实现这个功能，很简单就知道需要一下模块：
* `manifest.json` 【配置】
* `ico` 【图标】
* `Background Script` 【图标按钮点击交互】
* `UI Elements` 【点击图标是展示的弹窗页面】
* `Options Page` 【可供用户选择修改默认的背景色的页面】
* `Content Script` 【修改用户查看的页面的背景色】

#### `manifest.json`
配置文件，权限、弹窗页面、ico位置等信息。
````
{
    "name": "Getting Started Example",
    "version": "1.0",
    "description": "Build an Extension!222",
    "permissions": ["activeTab", "declarativeContent", "storage"],
    "background": {
        "scripts": ["background.js"],
        "persistent": false
    },
    "options_page": "options.html",
    "page_action": {
        "default_popup": "popup.html",
        "default_icon": {
            "16": "fh-icon.png",
            "32": "fh-icon.png",
            "48": "fh-icon.png",
            "128": "fh-icon.png"
        }
    },
    "icons": {
        "16": "fh-icon.png",
        "32": "fh-icon.png",
        "48": "fh-icon.png",
        "128": "fh-icon.png"
    },
    "manifest_version": 2
}
````
#### `background.js`
后台脚本，
````
chrome.runtime.onInstalled.addListener(function() {
    chrome.storage.sync.set({color: '#f00'}, function() {
      console.log("The color is green.");
    });
    chrome.declarativeContent.onPageChanged.removeRules(undefined, function() {
        chrome.declarativeContent.onPageChanged.addRules([{
          conditions: [new chrome.declarativeContent.PageStateMatcher({
            pageUrl: {},
          })
          ],
              actions: [new chrome.declarativeContent.ShowPageAction()]
        }]);
      });
});
````
#### `popup.html`
点击ico的时候弹出的页面，只有一个可被点击的色块按钮。
````
<!DOCTYPE html>
  <html>
    <head>
      <style>
        button {
          height: 30px;
          width: 30px;
          outline: none;
        }
      </style>
    </head>
    <body>
      <button id="changeColor"></button>
      <script src="popup.js"></script>
    </body>
  </html>
````
#### `popup.js`
弹出页面的交互，点击弹窗页面中的按钮时，需要设置当前展示页面的背景色为`chrome.storage`存储的默认色值，
````
let changeColor = document.getElementById('changeColor');

chrome.storage.sync.get('color', function(data) {
    changeColor.style.backgroundColor = data.color;
    changeColor.setAttribute('value', data.color);
});

changeColor.onclick = function(element) {
    let color = element.target.value;
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
        chrome.tabs.executeScript(
            tabs[0].id,
            {code: 'document.body.style.backgroundColor = "' + color + '";'});
    });
};
````
#### `options.html`
[扩展应用页面](chrome://extensions/)可以查看每个扩展的详细介绍，这个页面就是扩展程序的`扩展程序选项`页面，用户可以自由配置扩展程序需要的参数【前提是扩展应用开发把配置参数放在这个页面供使用者修改了】。本案例是可供选择的背景色值。
````
<!DOCTYPE html>
<html>
  <head>
    <style>
      button {
        height: 30px;
        width: 30px;
        outline: none;
        margin: 10px;
      }
    </style>
  </head>
  <body>
    <div id="buttonDiv">
    </div>
    <div>
      <p>Choose a different background color!</p>
    </div>
  </body>
  <script src="options.js"></script>
</html>
````
#### `options.js`
`扩展程序选项`页面的交互脚本，在用户选择 `options.html`页面中的颜色块的时候，处理设置`chrome.storage`存储的默认色值，
````
let page = document.getElementById('buttonDiv');
  const kButtonColors = ['#3aa757', '#e8453c', '#f9bb2d', '#4688f1'];
  function constructOptions(kButtonColors) {
    for (let item of kButtonColors) {
      let button = document.createElement('button');
      button.style.backgroundColor = item;
      button.addEventListener('click', function() {
        chrome.storage.sync.set({color: item}, function() {
          console.log('color is ' + item);
        })
      });
      page.appendChild(button);
    }
  }
  constructOptions(kButtonColors);
````

上面实例是chrome官方提供的基础实例，还有很多API需要在使用的时候查看。不能科学上网的话，只能参考一下国内的其他浏览器的扩展开发，其实是类似的，基本都是借鉴的Chrome的方式。

参考：
[chrome扩展入门](https://developer.chrome.com/extensions/getstarted)
[chrome扩展综述](https://developer.chrome.com/extensions/overview)