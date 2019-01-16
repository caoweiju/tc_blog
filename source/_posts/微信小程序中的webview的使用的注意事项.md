---
title: 微信小程序中的webview的使用的注意事项
date: 2019-01-17 00:08:58
tags:
- webview
categories: 
- 小程序
---
# 小程序
微信小程序是一种全新的连接用户与服务的方式，它可以在微信内被便捷地获取和传播，同时具有出色的使用体验。同时提供一系列工具帮助开发者快速接入并完成小程序开发。关于如何注册配置就不多言了，本文主要还是体验了下`web-view`的功能。

## web-view详解
有了这个组件之后，小程序可以很好的嵌入一些页面，可以环境小程序`size`告急的问题，同样也使开发更加便捷，毕竟小程序开发者基本都对前端开发较为了解。

### web-view能力
说再多还是需要去看官方文档，[web-view文档](https://mp.weixin.qq.com/debug/wxadoc/dev/component/web-view.html),
#### 兼容
首先就需要注意：兼容问题，[版本库和对应版本比例](https://mp.weixin.qq.com/debug/wxadoc/dev/framework/client-lib.html)
>基础库 1.6.4 开始支持，低版本需做兼容处理，
个人类型与海外类型的小程序暂不支持使用。

目前而言，基本`80%`的用户会升级微信，所以其实不必担心版本问题，官方截止`2017-12-01`提供的数据也说明`88%`的用户支持`web-view`。

<!-- more -->

#### 使用
`web-view` 组件是一个可以用来承载网页的容器，会自动铺满整个小程序页面；

属性：`src`	是`String`类型，是一个网站的`url`，默认值是`none`，`webview` 指向网页的链接。需登录小程序管理后台配置域名白名单。
````
<!-- wxml -->
<!-- 指向微信公众平台首页的web-view -->
<web-view src="https://mp.weixin.qq.com/"></web-view>
````
可以配合`Page`实例的`onLoad`方法来获取`url`的具体值，也就是一个微信小程序页面中只有一个`web-view`，但是这个`web-view`的内容可以根据上一个页面传递的参数来获取页面`URL`，后面会讲如何实践，

官方提供如下接口：
* `web-view`和小程序的通信
    1. 由小程序到`web-view`,其实本质上`WEB-VIEW`也是小程序的一个页面，所以小程序到`web-view`是正常的小程序间的通信，通过`wx.navigateTo`、`wx.redirectTo`，带上`url`参数,`query`参数就像正常`url`的参数一样跟着后面，然后在`web-view`的页面的`Page`实例里面通过`onLoad`的方法的参数来获取`url`的值，设置给`web-view`的`src`属性为改值即可。
    2. 由`web-view`到小程序，由于在`web-view`的跳转通常是在`src`对应的网页中的操作来处理的，所以需要结合`jssdk`来处理，不需要`wx.config`配置，直接通过`script`标签来引入`https://res.wx.qq.com/open/js/jweixin-1.3.0.js`，就可以使用`wx.miniProgram.navigateTo`、`wx.miniProgram.navigateBack`、`wx.miniProgram.switchTab`、`wx.miniProgram.reLaunch`、`wx.miniProgram.redirectTo`接口，就像小程序之间的跳转一样，单是只能在当前小程序页面内跳转。
* 支持以下部分JSSDK接口图像、音频、摇一摇、地理位置等信息，具体可以查看[web-view文档](https://mp.weixin.qq.com/debug/wxadoc/dev/component/web-view.html),不过这些需要通过`wx.config`来授权，就和服务号开发类似。
* 用户分享时可获取当前`<web-view/>`的`URL`，即在`onShareAppMessage`回调中返回webViewUrl参数。  
    ````
    Page({
      onShareAppMessage(options) {
        console.log(options.webViewUrl)
      }
    })
    ````

* 在网页内可通过window.__wxjs_environment变量判断是否在小程序环境。
    ````  
    // web-view下的页面内
    console.log(window.__wxjs_environment === 'miniprogram') // true
    ````


### web-view实践
在目前实践了部分`web-view`的功能，
````
//index.js
Page({
    data: {
       url: 'https://test.com'
    },
    onLoad: function(options){
        options.url ? this.setData({url: options.url}) : wx.navigateBack({delta: 2});

    }
});

//index.wxml
<web-view src="{{url}}"></web-view>
````
在这个`web-view`中，指向的就是`https://test.com`的内容，所以在在`https://test.com`中跳转出回到小程序，需要修改`https://test.com`中的`JavaScript`,
````
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>test</title>
    <link rel="stylesheet" href="https://test.com/index.css" />
</head>
<body>
    <div class="app">
        <h1>webview-wechat-detail</h1>
        <p>
            detail
        </p>
        <button type="button" id="btn">返回小程序</button>
    </div>
    <script src="https://test.com/jquery.js"></script>
    <script src="https://res.wx.qq.com/open/js/jweixin-1.3.0.js"></script>
    <script>
        /* eslint-disable */
$(function(){
    doucument.cookie = 'bb=bbbbbb';
    $('#btn').on('click',function(s) {
        document.cookie = 'aa=ssssss';
        wx.miniProgram.navigateTo({
            url:'/pages/index?test=testtest',
            success: function(){
                console.log('success')
            },
            fail: function(){
                console.log('fail');
            },
            complete:function(){
                console.log('complete');
            }

            });
        });
    });
    </script>
</body>
</html>

````
如果需要使用一些其他的的`jssdk`的方法，那就需要参照公众号的开发配置了。
### web-view采坑
由于很多使用中的一些问题
>1.打开的域名没有在小程序管理后台设置业务域名（注意是业务域名，不是服务器域名）
2.打开的页面必须为https服务
3.打开的页面302过去的地址也必须设置过业务域名
4.web-view空白问题，请升级微信客户端到 6.5.16
5.页面可以包含iframe，但是iframe的地址必须为业务域名
6.web-view不支持支付能力，web-view的API能力见web-view的文档说明
7.开发者自己检查自己的https服务是否正常，测试方法：普通浏览器打开对应的地址
8.如果web-view使用了公众号授权的服务，开发者工具提示网页开发者的问题，请见：[公众号开发](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1455784140)
其他的问题注意：
1. 每个页面只能有一个<web-view/>，<web-view/>会自动铺满整个页面，并覆盖其他组件，小程序对webview的监控状态基本没有，只能设置src设置url。
2. 关于小程序和web-view的通信，<web-view/> → 小程序只能通过JSSDK 1.3.0提供的接口返回小程序页面，设置参数来传值，反之，小程序到webview也是一样的，只能是src的路径带上参数；
3. web-view不支持支付能力,是指无法唤起小程序的直接支付窗口，对于h5的那套支付应该是支持的，但是web-view 里边没法使用 微信支付的 JSAPI，也就是可能可以h5的相关的的支付中心来支付；
4. 关于层级，在webview中可以无限跳转，对于导航条返回和物理键返回都会回到上一个页面直到退出webview，就像`history.back`。
5. webview中的html的title会自动放到小程序的头部作为标题；
6. webview中可以正常使用ajax之类的操作。
7. 一些可能的问题[问题汇总](https://developers.weixin.qq.com/blogdetail?action=get_post_info&lang=zh_CN&token=585555149&docid=ebfd9e5ec9986b4f23c41f8d8bbf2730)