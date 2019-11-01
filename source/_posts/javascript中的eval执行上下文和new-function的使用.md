---
title: javascript中的eval执行上下文和new function的使用
date: 2019-10-25 15:32:46
tags:
- eval
categories: 
- JavaScript
---

# 简析eval
一般而言，作为前端开发，我们经常听到开发环境尽量不要使用`eval`；
- eval 太神秘了，以至于很多人用错。所以不推荐使用。
- eval的可读性和不容易调试
- 无法优化和性能问题
- 安全问题

## eval使用注意和执行上下文
首先看看一下几个实例：eval运行时的上下文context
````js
var context = 'outside';
(function(){
  var context = 'inside';
  return eval('context');
})();
// inside

var context = 'outside';
(function(){
  var context = 'inside';
  return eval.call(null, 'context');
})();
// outside

var context = 'outside';
(function(){
  var context = 'inside';
  return (1, eval)('context');
})();
// outside

var context = 'outside';
(function(){
  var context = 'inside';
  return (eval)('context');
})();
// inside

var context = 'outside';
(function(){
  var context = 'inside';
  var my_eval = eval;
  return my_eval('context');
})();
// "outside"
````
可以看到这和eval 的调用方式有关， 需要看是 direct or indirect。
### direct vs indirect call


## new function



参考：
[Exploring Javascript's eval() Capabilities And Closure Scoping(https://www.bennadel.com/blog/1926-exploring-javascript-s-eval-capabilities-and-closure-scoping.htm)  
[javascript eval: direct vs indirect call](https://blog.klipse.tech/javascript/2016/06/20/js-eval-secrets.html)  
[Global eval. What are the options?](http://perfectionkills.com/global-eval-what-are-the-options/)  
