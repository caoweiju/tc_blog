---
title: HTML的演进和html5的自闭合标签规范
date: 2019-01-18 22:49:51
tags:
- 自闭合标签
- 伪元素
categories: 
- HTML
- css
---

# html简史
>HTML 是 Web 统一语言，这些容纳在尖括号里的简单标签，构成了如今的 Web。1991 年，`Tim Berners-Lee` 编写了一份叫做 “HTML 标签”的文档，里面包含了大约20个用来标记网页的 HTML 标签。他直接借用` SGML `的标记格式，也就是后来我们看到的 HTML 标记的格式。本文讲述了 HTML 这门 Web 标记语言的发展简史。

<!-- more -->

## 从 IETF 到 W3C: HTML 4 之路
* `HTML 1 `并不曾存在，
* `HTML `的第一个官方版本就是由` IETF （互联网工程任务组） `推出的 `HTML 2.0`。问世之前，这个版本中的很多细则已经被实现，比如，1994年的 `Mosaic 浏览器`已经实现了在文档中嵌入图片的方法，后来 `HTML 2.0 `便吸纳了 `img `这个标签。
* 后来，`W3C` 取代 `IETF `的角色，成为 HTML 的标准组织，
* `90年代的后半叶`，HTML 的版本被频繁修改，直到1999年的 `HTML 4.01`，至此，HTML 到达了它的第一个拐点。

`HTML4`之后，出现了一些分歧。

## XHTML 1: XML 风格的 HTML
`HTML `在` HTML 4.01 `之后的第一个修订版本就是 `XHTML 1.0`，其中 X 代表 `“eXtensible” `，扩展，当然也有人将之解读为 `“eXtreme”`，极端。`XHTML 1.0` 是基于` HTML 4.01 `的，并没有引入任何新标签或属性，唯一的区别是语法，`HTML `对语法比较随便，而 `XHTML` 则要求 `XML` 般的严格语法。

　　使用严格的语法规范并非坏事，要求开发者使用单一的代码风格，比如，`HTML 4.01 `允许你使用大写或小写字母标识标记元素和属性，`XHTML` 则只允许小写字母。`XHTML 1.0` 的推出刚好碰上了 `CSS `的崛起，Web 开发设计者们开始意识到 Web 标准问题，基于 `XHTML` 的严格语法规范被视为编写` HTML` 代码的最佳实践。

　　于是，W3C 推出 `XHTML 1.1`。

　　如果说` XHTML 1.0` 是 XML 风格的 HTML，`XHTML 1.1 `则是货真价实的 `XML`。这意味着 `XHTML 1.1` 无法使用 `text/html mime-type `直接输出，然而，如果 Web 开发者使用 `XML mime-type`，则当时的主流浏览器，IE 则压根不支持。看上去，W3C 似乎正在与当时的 Web 脱节。

## 出力不讨好的 XHTML 2
　　对 W3C 而言，到了 `HTML 4` 已经是功德圆满，他们的下一步工作是 `XHTML 2`，希望将 Web 带向 XML 的光明未来。虽然 `XHTML 2` 听上去和` XHTML 1 `类似，它们却有很多差别，`XHTML 2` 不向前兼容，甚至不兼容之前的 HTML。它是一种全新的语言，赤条条来去无牵挂。这实在是一场灾难。

## WHATWG：与 W3C 决裂
　　W3C 闭门造车的作风引起了一些人的不满，来自 `Opera, Apple, 以及 Mozilla` 的代表开始表达反对声音。2004年，Opera 的 `Ian Hickson` 提议在 HTML 基础上进行扩展以适应新的 Web 应用，该提议遭到 W3C 的拒绝。于是，他们自发组织成立了超文本应用技术工作组，就是 `WHATWG`。

## 从 Web Apps 1.0 到 HTML5
　　从一开始，`WHATWG `就和` W3C `走不同的路线，W3C 对问题的讨论是集体投票，而 `WHATWG `则由主笔` Ian Hickson `定度。表面上看，W3C 更民主，然而事实上，各种内部纷争会使一些决议限于泥潭，在` WHATWG`，事情的进展会更容易，不过，主笔的权力并非无限大，他们的委员会可以对那些过于偏执的主笔进行弹劾。

　　一开始，`WHATWG `的主要工作包括两部分，`Web Forms 2.0 `和 `Web Apps 1.0`，它们都是 HTML 的扩展，后来，他们合并到一起成为现在的 `HTML5` 规范。

## 言归于好
　　在` WHATWG` 致力于 `HTML5 `的同时，W3C 继续他们的 `XHTML 2.0`，然而，他们慢慢地陷入困境。

　　2006年10月，Web 之父 `Tim Berners-Lee `发表了一篇博客文章，表示，从 HTML 走向 XML 的路是行不通的，几个月后，W3C 组建了一个新的` HTML` 工作组，他们非常明智地选择了` WHATWG `的成果作为基础。这一转变带来一些困惑，W3C 同时进行这两套规范，`XHTML 2 `和` HTML 5` （`注意，W3C 的 HTTML 5 在 5 之前有个空格，而 WHATWG 的 HTML5 则没有空格`），而 WHATWG 也在进行着同样的工作。

## XHTML 已死: XHTML 语法永存
　　这一混乱局面到了 2009 年开始变得清晰，W3C 宣布终止 `XHTML 2 `的工作，这是一份关于` XHTML 2 `的迟到的讣告。这一消息被那些 XML 的反对者视为珍宝，他们借此嘲笑那些使用 XHTML 1 规范的人，然而他们似乎忘记了，`XHTML 1 `和 `XHTML 2` 是截然不同的东西。于此同时，`XHTML 1` 规范的制定者担心，`XHTML 1 `中的严格语法规范会被 `HTML5 `弃用，这种担心后来证明是多余的，`HTML5 `既支持松散语法，也支持` XHTML 1 `般的严格语法。

## HTML5 路线图
　　`HTML5` 的现状是，它不再象以前那样让人困惑，然而仍不够明朗。有两个组织在同时制定它的规范，这两个组织有着完全不同的行事风格，`WHATWG` 是先买后尝，W3C 是先尝后买，他们形成了一个不太靠谱的联姻，最终人们必将面临一个` HTML5` 还是` HTML 5` 的问题。`html5`和其他的不一致的地方，可以查看：[查看HTML5的区别](https://www.w3.org/TR/html5-diff/)

## <!DOCTYPE> 声明
`<!DOCTYPE>` 声明位于文档中的最前面的位置，处于 `<html> `标签之前。此标签可告知浏览器文档使用哪种 `HTML `或 `XHTML` 规范。
` HTML4`的三种模式：`HTML 4.01 规定了三种文档类型：Strict、Transitional 以及 Frameset。`

同样`XHTML1`也有三种模式：`XHTML 1.0 规定了三种 XML 文档类型：Strict、Transitional 以及 Frameset。`

不过目前而言，在 `HTML5` 中只有一种声明：`<!DOCTYPE html>`，

目前的标准已经到了`HTML5`的第二版，查看[HTML5.3](https://www.w3.org/TR/html53/)；


## 自闭和标签正确书写
一般标签由于有开始符号和结束符号，可以在标签内部插入其他标签或文字；自闭合标签由于没有结束符号，没办法在内部插入其他标签或文字，只能定义自身的一些属性。

常见自闭和标签：
````
1、<meta/>
定义页面的说明信息，供搜索引擎查看。

2、<link/>
用于连接外部的CSS文件或脚本文件。

3、<base/>
定义页面所有链接的基础定位。

4、<br/>
用于换行。

5、<hr/>
水平线。

6、<input/>
用于定义表单元素

7、<img/>
图片
````

目前的HTML都是准守`html5`的标准，所以自闭和标签也是需要准守`html5`的标准写法，也就是推翻了`xhtml`中的严格模式，自闭和标签不需要加斜杠结束。

图片实例：
    <img src="" ><img /> // 不规范
    <img src="" >  // html规范写法


## 伪元素 `::placeholder`
伪元素添加到选择器，但不是描述特殊状态，它们允许您为元素的某些部分设置样式；最为常见的就是`::before`-`::after`，
* ::after
* ::before
* ::first-letter
* ::first-line
* ::selection
试验性_内嵌
* ::backdrop 
* ::placeholder 
* ::marker 
* ::spelling-error 
* ::grammar-error 
对于很多自闭和标签，其实应该是不支持伪元素，不过`input`元素有一个特殊的伪元素，可以设置占位符的样式；
````
::-webkit-input-placeholder
::-moz-placeholder
:-ms-input-placeholder
::placeholder
````

## ios的弹性滚动和滚动条的问题
`-webkit-overflow-scrolling: touch`; 是用在移动端 `webkit` 内核浏览器的一个滚动条效果，通常我们的页面滚动（body 会默认采用这种方案无需代码声明）当手指触摸滑动时，它是不会以一种惯性，带回弹效果的滚动，而这段代码即是让它拥有这种像 App 一样的效果

使用这段代码的时候它随即带来了一个问题，`-webkit-overflow-scrolling: touch`; 所带来的滚动条在未滚动的时候是隐藏状态，而当手指尝试去滑动滚动条就会显示出来，然而这个控件的样式并非像桌面浏览器可以自定义，也就是说无法隐藏。

也就是这两个属性展不兼容。
````
-webkit-scrollbar{ display: none;} 
-webkit-overflow-scrolling: touch;
```

目前的解决方案：父容器设置固定高度h,并`overflow：hidden`，滚动容器高出父容器h+10px；滚动容器的子内容高度和父容器一样，比滚动容器小10px，然后滚动条就会被父容器隐藏。
````
<div class="father">
    <div class="scroller">
        <div class="son">子元素</div>
        <div class="son">子元素</div>
        <div class="son">子元素</div>
    </div>
</div>
````
