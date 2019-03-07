---
title: velocity的基本使用和实例学习
date: 2019-03-07 17:12:45
tags:
tags:
- Velocity
categories: 
- tool
---
# velocity的简介
Velocity是一个基于java的模板引擎（template engine）。它允许任何人仅仅简单的使用模板语言（template language）来引用由java代码定义的对象，这样的话其实在前后端分离而言又是一个较为合理的可选方案，
Velocity也可以配合其他的语言来完成使用，Velocity.js就是不错的选择，这样的话可以依靠nodejs和浏览器环境来实现模版的功能。
* 什么是Velocity模版?
    Velocity模版可以是任意的文本文件,只要里面包含专门的模板语言(VTL)标记,就可以被Velocity模板引擎解析;
* Velocity模版的工作机制:
    Velocity可以通过获取Java对象的函数返回值、属性值,用于替换模板文件中的VTL变量标记,从而生成新的文件,模板文件的扩展名可以是".vm"、".htm"、".html"、".asp"、".sql",等等任
#语法简介
大部分的模版引擎都是有自己的书写方式，不过{}这个大括号基本都是他们的通用符号，这里介绍语法的顺序是：
* 变量定义、使用
* 控制语句 if for之类
* 指令
* 宏

<!-- more -->

## 变量
变量是任何语言都不可缺少的；
### 变量定义和使用
 变量的定义和赋值基本方法：#set (<$变量名> = <"变量值">)
 #set ($name = "Sankhya")  ##字符串
 #set ($age = 30)          ##数字
 #set ($list = ["Sankara", ${name}, "Shankara"])  ##数组
 #set ($dict = ["A":"va", "B":234, "C":${value}]) ##HashTable
在双引号之间的字符串将被Velocity引擎解释和重新解释;在velocity中使用$2.5这样的货币标识是没有问题得的，因为velocity中的变量总是以一个大写或者小写的字母开始的。

使用：
${name} ，也可以写成：$name。提倡用前面的写法。
例如：你希望通过一个变量$vice来动态的组织一个字符串。
 Jack is a $vicemaniac.
本来变量是$vice现在却变成了$vicemaniac，这样Veloctiy就不知道您到底要什么了。所以，应该使用规范的格式书写 ： Jack is a ${vice}maniac
现在Velocity知道变量是$vice而不是$vicemaniac。
例如:
 #set ($desc = "${name} is ${age} years old;")
执行这条语句之后,输出$desc的值将是:Sankhya is 30 years old;
同时还需要注意：
(1).Velocity模版中的变量是弱类型的;等号"="右边的值可以是引用、字符串、数字、Array、Vector、HashTable或表达式;
(2).如果等号"="右边的值是null,则左边的变量不会被赋值,且仍然保留以前的值;
(3).模板中没有被定义的变量将被认为是一个字符串;
(4).模板引擎不会将reference解释为对象的实例变量;如:$foo.Name将被解释为对象Foo的getName()方法,而不是Foo对象的Name实例变量;$foo.getBar()就等价于$foo.Bar;

**注释：**
* 单行注释使用“##”开头就可： ## 这好似注释
* 多行注释 #\* 多行注释 \*#
* 文档注释： #\*\* 文档编写 \*\*#

## 控制语句
### if条件控制语句
``` 
#if (<condition>)
  ##statement segment
 #elseif (<condition>)
  ##statement segment
 #else
  ##statement segment
 #end
```
作为condition,Velocity引擎也支持关系运算符(>、>=、<、<=、!=)和逻辑运算符(&&->AND、||->OR、!->NOT);
"!"用来强制把不存在的变量显示为空白。
如当页面中包含$msg，如果msg对象有值，将显示msg的值，如果不存在msg对象同，则在页面中将显示$msg字符。这是我们不希望的，为了把不存 在的变量或变量值为null的对象显示为空白，则只需要在变量名前加一个“!”号即可。
如：$!msg
### 循环控制
```
#foreach ($element in $list)
  ##your statement
  $velocityCount
#end
```
##指令
  #parse和#include指令:
这两者都是导入本地模板文件;
  #parse指令只能导入一个文件,同时,被导入的文件的内容将会被Velocity模板引擎解析,意思就是把被导入的文件的内容copy到当前文件中;该指令也可以被递归调用;

 #include指令可以导入一个或多个文件,当导入多个文件时,可以用逗号分隔;而且,被导入的文件的内容不会被Velocity模版引擎解析;
例如:
 #include ("one.gif", "two.txt", "three.htm") ##不会被模版引擎解释;
 #include (${file1}, ${file2}, ${file3})      ##不会被模版引擎解释;
 #parse ("server.conf")                       ##会被模版引擎解释;
 
 #stop停止执行模板引擎并返回,把它应用于debug是很有帮助的;
Velocity中的转义字符也是使用符号"\";这就意味着执行"\\"将会输出"\",而执行"\$"将会输出"$",执行"\#"会输出"#";

参考：[velocity1.7官方文档](http://velocity.apache.org/engine/1.7/user-guide.html)
http://bdxnote.blog.163.com/blog/static/8444235201322922418485/
   http://www.blogjava.net/alexwan/archive/2008/06/07/206473.html
   http://www.cnblogs.com/likwo/archive/2010/05/13/1734936.html