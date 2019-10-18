---
title: css中互斥、配合和注意使用 的属性
date: 2019-01-23 23:49:45
tags:
- bfc
- stack levels
categories: 
- css
---
# css学习
很少有人会很系统的把`css`学习一遍，大部分都是遍历下。然后然后记住常用的，不常用的也要回查找，前端人很多都认同要学好`css`真的不很难，这里记录下很多场景下使用`css`需要注意的地方，同样，开头页列出一些学习`css`的知识点：
* 基本属性[定位、盒子模型、文本、背景。。。]
* 动画属性[渐变、变化、过渡、动画]
* 堆叠和块格式化上下文
* 布局[table、flex、grid]
* 选择符[权重、包含、子选择、相邻、兄弟、属性选择、伪类、伪元素]
* 媒体查询

<!-- more -->

当然远不止这些，还有什么不同应用场景有不同的样式、厂商前缀、适配等各种让人欲生欲死的问题存在。当自认为可以游刃有余的时候，可以深入学习以下：
* `sass`
* `css`魔法[一本脑洞大开的css书籍]
## 一些需要注意的点
在平时书写`css`的时候需要注意些一些属性的依赖、相关甚至相斥的关系，以下是一些暂时常见的一些坑；
### float
`float`是使用比较平凡的一个布局属性，需要注意：
* `float`在绝对定位和`display`为`none`时不生效。
* `float:right`的时候，第一个绘制的元素在最右边，
* 子元素的`float`属性都是非`none`时，父元素不会被撑开，浮动元素脱离了正常的流式布局，需要在父元素内清除浮动，可以使用`::after`来添加伪元素，为伪元素设置`clear:both`即可。
### z-index
`z-index`也是`css`种较为关键的属性，一般而言，只要有弹窗的地方，`z-index`就会出现，在目前的单页应用流行的时候，弹窗是必不可少的，也是影响层叠上下文的关键因子；

两铁律：
1. **`z-index只能在position属性值为relative或absolute或fixed的元素上有效。【flex子项除外】`**
2. **`子元素的层叠顺序不可能超过父级，如果父级层叠顺序低，子元素再高也不会超过父级同级但是层叠顺序高的的其他元素`**

**`层叠上下文`**
>层叠上下文是HTML元素的三维概念，这些HTML元素在一条假想的相对于面向（电脑屏幕的）视窗或者网页的用户的z轴上延伸，HTML元素依据其自身属性按照优先级顺序占用层叠上下文的空间。

正常布局的时候，整个`html`就是一个层叠上下文，会根据元素绘制的先后顺序来展示，重叠的时候需要根据绘制顺序来进行遮盖，除了在顶层节点会形成层叠上下文时还有下面这些操作会形成层叠上下文：
>* 根元素 (HTML),  
>* z-index 值不为 "auto"的 绝对/相对定位，
>* 一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex，
>* opacity 属性值小于 1 的元素（参考 the specification for opacity），
>* transform 属性值不为 "none"的元素，
>* mix-blend-mode 属性值不为 "normal"的元素，
>* filter值不为“none”的元素，
>* perspective值不为“none”的元素，
>* isolation 属性被设置为 "isolate"的元素，
>* position: fixed
>* 在 will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值
>* -webkit-overflow-scrolling 属性被设置 "touch"的元素
既然形成了新的层叠上下文，那么这个上下文中的层叠顺序如何确定？

**`每个层叠上下文的绘制顺序`**

在每一个层叠上下文中，以下层次按照后来居上的规则绘制
>* 元素的background和borders
>* 拥有负堆叠层级（negative stack levels）的子层叠上下文（child stacking contexts）
>* 在文档流中的（in-flow），非行内级的（non-inline-level），非定位（non-positioned）的后代元素
>* 非定位的浮动元素
>* 在文档流中的（in-flow），行内级的（inline-level），非定位（non-positioned）的后代元素，包括行内块级元素（inline blocks）和行内表格元素（inline tables）
>* 堆叠层级为 0 的子堆叠上下文（child stacking contexts）和堆叠层级为 0 的定位的后代元素
>* 堆叠层级为正的子堆叠上下文

![图片描述](http://pzdgkztjy.bkt.clouddn.com/blog/img/stack_level.png)

重复一下这两铁律：
1. **`z-index只能在position属性值为relative或absolute或fixed的元素上有效。【flex子项除外】`**
2. **`子元素的层叠顺序不可能超过父级，如果父级层叠顺序低，子元素再高也不会超过父级同级但是层叠顺序高的的其他元素`**

`transformZ`和`z-index`最好不要一起使用。
### text-overflow
经常会在一个行内标题等地方，文本如果过多的话希望使用`...`来隐藏，那么`text-overflow`就是必须得来；
>取值：
>clip：当内联内容溢出块容器时，将溢出部分裁切掉。
>ellipsis：当内联内容溢出块容器时，将溢出部分替换为（...）。
要使得 <' text-overflow '> 属性生效，块容器必须显式定义 <' overflow '> 为非visible值，同时显式或者隐式的定义 <' width '> 为非auto值， <' white-space '> 为nowrap值。
    overflow:hidden;
    width:200px;
    white-space:nowrap;
    text-overflow:ellipsis;
**超出不隐藏、或者可以换行或者宽带可以自动延伸的话，均不会出现溢出，所以`text-overflow`会失效**

### flex
在父级元素是`display:flex`的时候，子元素的很多布局就失效了，`float`、`clear`、`vertical-align`这三个属性在子元素上不起作用，需要使用flex相关的布局来完成。

### 伪元素
伪元素其实就是一个子元素，`div::after`是在`div`的最后面添加了一个元素，元素就是`::after`，所以 需要注意在自闭合标签后面不可以添加伪元素，因为自闭合标签不能有子元素，比如`img`、`br`这样的标签。

### 伪类
伪类其实就是一个类似`class`的选择器，在使用的时候注意，`:last-child`、`:first-child`这样的伪类需要注意，必须是父元素中的第一个子元素被命中才可以，所以使用这类伪类的时候，最好是父元素中只有某一类的元素，不含有其他的元素。

### position
在绝对定位的时候，需要注意参考元素是离自身最近的定位祖先元素，
>absolute：对象脱离常规流，此时偏移属性参照的是离自身最近的定位祖先元素，如果没有定位的祖先元素，则一直回溯到body元素。盒子的偏移位置不影响常规流中的任何元素，其margin不与其他任何margin折叠。  

>当position的值为非static时，其层叠级别通过z-index属性定义。 
 
>绝对定位的元素，在top，right，bottom，left属性未设置时，会紧随在其前面的兄弟元素之后，但在位置上不影响常规流中的任何元素。用这个特性你或许会干这样的事

### visibility
`visibility`元素是还在的，只是看不见，位置还是要占住
### background
background是图片的时候，最好加上background-size,不然容易变形或这个平铺
### line-height
line-height很多时候是撑开父级元素的关键，可以结合子元素的居中对齐vertical-align
### table
配套使用：table table-cell
### BFC（block formatting context）
>Formatting context 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。最常见的 Formatting context 有 Block fomatting context (简称BFC)和 Inline formatting context (简称IFC)。

>一个块格式化上下文（block formatting context） 是Web页面的可视化CSS渲染出的一部分。它是块级盒布局出现的区域，也是浮动层元素进行交互的区域。

BFC(Block formatting context)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。
**`bfc布局规则`**
> * 内部的Box会在垂直方向，一个接一个地放置。
> * Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
> * 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
> * BFC的区域不会与float box重叠。
> * BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
> * 计算BFC的高度时，浮动元素也参与计算
**`bfc触发`**
> * 根元素或其它包含它的元素
> * 浮动元素 (元素的 float 不是 none)
> * 绝对定位元素 (元素具有 position 为 absolute 或 fixed)
> * 内联块 (元素具有 display: inline-block)
> * 表格单元格 (元素具有 display: table-cell，HTML表格单元格默认属性)
> * 表格标题 (元素具有 display: table-caption, HTML表格标题默认属性)
> * 具有overflow 且值不是 visible 的块元素，
> * display: flow-root
> * column-span: all 应当总是会创建一个新的格式化上下文，即便具有 column-span: all 的元素并不被包裹在一个多列容器中。
