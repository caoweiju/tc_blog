---
title: JavaScript的this指向、setTimeout、setInterval、ajax-callback
date: 2019-02-21 17:30:34
tags:
- this
categories: 
- JavaScript
---
# JavaScript的this
在JavaScript中this是一个永恒的话题，正在能够完全掌握其实不易，本文主要讲解下一下几种情况下的判定：
* 全局情况下
* 对象调用下
* call、apply、bind方法
* dom事件绑定
* setTimeout、setInterval
* ajax等异步操作
## 全局window/global
一个在模块或者全局的情况下调用一个方法时【不在使用 'use strict'的情况，否则不会默认将this指向全局】
````
    var x = 9;
　　function test(){
　　　　this.x = 6;
　　}
　　test();
　　alert(x); //输出6
````
可以知道，在这种情况下的this一般是window或者node中的global；

<!-- more -->

## 对象调用
这里可能是方法作为构造函数来使用，那么这个方法中的this会指向这个构造方法的实例，
````
　function test(){
　　　　this.x = 1;
　　}
　　var o = new test();
　　alert(o.x); // 1
````
可以看到，在一些情况下如果出现了类似的调用写法的话，this一般指向的就是调用者本身，形如【object.fn()】,这种情况下的fn里面的this一般指向前面调用fn方法的object对象；
## call、apply、bind方法
apply()和call()是函数对象的一个方法，它的作用是改变函数的调用对象，它的第一个参数就表示改变后的调用这个函数的对象。因此，this指的就是这第一个参数。这里就不讲述两者的不同了【基本就是两个函数的参数使用不一致】；
````
var x = 0;
　　function test(){
　　　　alert(this.x);
　　}
　　var o={};
　　o.x = 1;
　　o.m = test;
　　o.m.apply(); //0
    o.m.apply(o); //1
````
ECMAScript 5 引入了 Function.prototype.bind。调用f.bind(someObject)会创建一个与f具有相同函数体和作用域的函数，但是在这个新函数中，this将永久地被绑定到了bind的第一个参数，无论这个函数是如何被调用的。也和call、apply用相同作用；
以上参考了：http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this
## dom事件绑定
在使用js操作dom的时候，很常见的会绑定事件；比如下面的将元素p绑定click事件，然后事件的回调函数中就可以使用this；this指向这个时间的目标元素，也就是绑定事件的具体元素；
````
function bluify(e){
  console.log(this === e.currentTarget); // 总是 true
  // 当 currentTarget 和 target 是同一个对象是为 true
  console.log(this === e.target);        
  this.style.backgroundColor = '#A5D9F3';
}
// 获取文档中的所有元素的列表
var elements = document.getElementsByTagName('p');
// 将bluify作为元素的点击监听函数，当元素被点击时，就会变成蓝色
for(var i=0 ; i<elements.length ; i++){
  elements[i].addEventListener('click', bluify, false);
}
````
而在jQuery的使用中我们基本就是使用$(this)来表示事件的目标元素；因为this就是e.target；而把一个dom元素变为jQuery元素就是使用$把他抱起来就好了；
## setTimeout、setInterval的this
在js中，window实现了WindowOrWorkerGlobalScope这个mixin的所有方法，其中最被经常使用的就是setTimeout、setInterval，因为这是一个异步的过程：
````
var x = 33;
var test = { 
    x: 22,
    test:function(){
        console.log(this.x)
    },
    time:function(){
        setTimeout(this.test,1000);
    }
}
test.time(); //输出33，不是22
````
在使用异步的setTimeout、setInterval时候，由于需要等待特定时间之后在进行执行，所以这个就和setTimeout、setInterval的执行有关，在https://zhuanlan.zhihu.com/p/24460769 和 https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/ 中可以知道，其实就是event loop的概念，所以其实在使用setTimeout、setInterval的时候会出现等待的时长并不是精准的参数中写入的时间，原因如下：
* setTimeout、setInterval会把参数中的函数单独放在macrotask中，这里面是需要执行的event队列；
* 当计时到达时，从macrotask提前需要定时执行的函数，等待当前正在执行的事件队列执行完成【这里还有当前macrotask后面的mairotask】这里的当前正在执行的就是引起计时不准确的原因；
* 当执行完当前的macrotask+microtask以后，就开始执行定时函数，这个时候由于是新的全局下执行这个函数【这个时候就和本文第一种说的全局下调用function一样了，this指向window】，所以this会被window覆盖掉，故而不是在编写setTimeout时的执行上下文，函数的[[scrope]]时候的this，
* 具体的macrotask+microtask可以参考https://zhuanlan.zhihu.com/p/24460769 和 https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/

故而解决方法如下：
* 把this重命名保存：self=this；这样self还是函数[[scrope]]中的this；而不会被window替换
* 使用bind把this绑定
* 使用立即执行函数来函数再包一层
````
    this.intervalID = setInterval(
        (function(self) {     //Self-executing func which takes 'this' as self
            return function() {    //Return a function in the context of 'self'
                   self.retrieve_rate();   //Thing you wanted to run as non-window 'this'
               }
         })(this),
     this.INTERVAL     //normal interval, 'this' scope not impacted here.
 ); 
````
## ajax中的this
在ajax中我们都会非常谨慎的使用this；这个异步操作在this上也是和setTimeout差不多，肯定是会被覆盖的，但是并不是被window覆盖，相反而是被XMLHttpRequest对象覆盖：
````
{
    "url": "demo_test.txt",
    "type": "GET",
    "isLocal": false,
    "global": true,
    "processData": true,
    "async": true,
    "contentType": "application/x-www-form-urlencoded; charset=UTF-8",
    "accepts": {
        "*": "*/*",
        "text": "text/plain",
        "html": "text/html",
        "xml": "application/xml, text/xml",
        "json": "application/json, text/javascript",
        "script": "text/javascript, application/javascript, application/ecmascript, application/x-ecmascript"
    },
    "contents": {
        "xml": {},
        "html": {},
        "json": {},
        "script": {}
    },
    "responseFields": {
        "xml": "responseXML",
        "text": "responseText",
        "json": "responseJSON"
    },
    "converters": {
        "text html": true
    },
    "flatOptions": {
        "url": true,
        "context": true
    },
    "jsonp": "callback",
    "dataTypes": [
        "text"
    ],
    "crossDomain": false,
    "hasContent": false
}
````
上面是一个ajax中的this的json格式，测试地址：http://www.runoob.com/try/try.php?filename=tryjquery_ajax_ajax  只需要在ajax的回调中打印this【console.log(JSON.stringify(this))】就可以在开发者工具的console中看到this这面目，也就是上面的格式的数据。