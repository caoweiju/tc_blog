---
title: flex布局的温故学习
date: 2019-03-27 16:06:27
tags:
- flex
categories: 
- css
---
# flex的简介
在flex的容器中默认存在两条轴，水平主轴`main axis`和垂直交叉轴`cross axis`，这是默认的设置，不过我们可以通过设置将主轴的方向变成垂直方向，交叉轴变成水平方向。  
在一个被设置为`flex`的容器中，每个直接子元素都被称之为`flex item`，每个`flex item`占据着主轴空间是`main size`，占据交叉轴上的空间叫做`corss size`;  

*需要注意的是：主轴和交叉轴的方向是可以设置的，默认的是主轴为水平方向、交叉轴为垂直方向；当然RN的表现和浏览器相反，默认主轴是垂直方向*

<!-- more -->

## flex容器
为了实践一个`flex`布局，我们首先需要一个容器，也就是父元素，并指定为`flex`布局，这样，容器就具备了上述的`flex`的特性，其直接子元素也将成为`flex item`；设置为`flex`容器也很简单：
````css
.root{
    display: flex | inline-flex;  // 两个值都可以使用
}
````
上述的两个值可以分别生成一个块级`flex`容器盒子、一个行内`flex`容器盒子，简单说来，如果你使用块元素如 div，你就可以使用 flex，而如果你使用行内元素，你可以使用 inline-flex。
**需要注意的是：当时设置 flex 布局之后，子元素的 float、clear、vertical-align 的属性将会失效。**

设置为`flex`的容器，有以下六种可以设置的属性：
1. flex-direction
2. flex-wrap
3. flex-flow
4. justify-content
5. align-items
6. align-content

这里会对这几个属性进行分别的解释

### flex-direction 设置flex容器的主轴方向
 `flex-direction` 决定主轴的方向(即项目的排列方向)，
````css
.root{
    display: flex | inline-flex;  // 两个值都可以使用
    flex-direction: row | row-reverse | column | column-reverse; // 四个值可选
}
````
1. `row`：主轴与行内轴方向作为默认的书写模式。即横向从左到右排列（左对齐）。
2. `row-reverse`：对齐方式与row相反。[右对齐]
3. `column`：主轴与块轴方向作为默认的书写模式。即纵向从上往下排列（顶对齐）。
4. `column-reverse`：对齐方式与column相反。【底对齐】

伪代码和伪展示分别对应如下的方式：
````html
<div class="root">
    <div class="item">1</div>
    <div class="item">2</div>
    <div class="item">3</div>
<div>

//   flex-direction: row  水平靠左排列 第一个元素在左边
【1，2，3  】
//   flex-direction: row-reverse  水平靠右排列 第一个元素在右边
【  3，2，1】
//   flex-direction: column  垂直靠顶部排列 第一个元素在最顶部
【
  1，
  2，
  3，
  】
//   flex-direction: column-reverse  垂直靠底部排列 第一个元素在最底部
【
  3，
  2，
  1，
  】
````

**注意：该属性通过定义flex容器的主轴方向来决定felx子项在flex容器中的位置。这将决定flex需要如何进行排列，不仅是对齐方向，还有是子元素的排列顺序，第一个元素或者order靠前的将会出现在对齐方向的第一个位置处**

### flex-wrap 设置flex容器的子项目在主轴方向是否可以换行展示
默认的情况下，项目都应展开在主轴线上，通过设置`flex-wrap`可以达到主轴线上换行展示
````css
.root{
    flex-wrap: nowrap | wrap | wrap-reverse; // 三个值选其一
}
````
三个取值分别是：
1. `nowrap`默认值，也就是即使容器的空间不足，也不会换行，而是调整子元素的尺寸。
2. `wrap`是指如果子元素超出了容器的空间，那么需要换行，而且需要按`Z`字顺序排开，即第一行在最上方；
3. `wrap-reverse`是指换行展示，但是按照逆序的`Z`排开，即第一排在最下方，每一排的主轴方向的排序还是按照`flex-direction`的设置展示。

### flex-flow 是一个类似的语法糖，是前面所诉的两者的结合体
`flex-flow：<' flex-direction '> || <' flex-wrap '>`，可以看到这是一个复合属性。设置或检索弹性盒模型对象的子元素排列方式。
````css
.root{
    flex-flow: row nowrap;
}
// 等同于以下
.root{
    flex-direction: row;
    flex-wrap: nowrap;
}
````

### justify-content 设置主轴方向上的对齐方式
`justify-content`；定义了项目在主轴的对齐方式。
````css
.root{
    justify-content：flex-start | flex-end | center | space-between | space-around; // 五个值选其一
}
````
这个属性的设置和`flex-direction`属性有关，下面的值都将是以`flex-direction: row;`进行判断的，其中的五个值分别是：
1. `flex-start`: 是指靠主轴方向的起始位置对齐，`flex-direction: row;`条件下就是子元素靠左边对齐，尽可能空出右边；`[1,2,3, _ _ _ _ _ _]`
2. `flex-end`: 是指靠主轴方向的结束位置对齐，`flex-direction: row;`条件下就是子元素靠右对齐；尽可能空出左边。`[_ _ _ _ _ _,1,2,3]`
3. `center`: 是指靠主轴的中心位置对齐，`flex-direction: row;`条件下就是子元素居中对齐；尽可能左右两边空出相同空间；`[_ _ _1,2,3, _ _ _]`
4. `space-between`: 是指在主轴上左右两边不留空隙，并使子元素之间的间隙相等；`[1,_ _ _,2,_ _ _,3]`
5. `space-around`: 是指主轴上左右两边也留空隙，所有的子项目两侧自身边距相等，均匀分布在主轴上，所以项目之间的间隔比项目与边缘的间隔大一倍。`[_,1,_ _,2,_ _,3,_]`

### align-items 定义了项目在交叉轴上的对齐方式
````
.root {
    align-items: flex-start | flex-end | center | baseline | stretch;
}
````
默认值是`stretch`,这五个值：
1.  `flex-start`是指在交叉轴方向上的各子元素之间的对齐方式是在交叉轴的起点对齐；
2. `flex-end` 是指在交叉轴上的对齐方式是以交叉轴的重点对齐方式；
3.  `center` 是在交叉轴方向上是以交叉轴方向上的居中方式对齐
4.  `baseline` 是在交叉轴的方向上的第一行文字的基线对齐
5. `stretch` 是在交叉轴上的子项目未设置高度或者设为 auto，将占满整个容器的高度。

### align-content
定义了多根轴线的对齐方式，如果项目只有一根轴线，那么该属性将不起作用;当你 flex-wrap 设置为 nowrap 的时候，容器仅存在一根轴线，因为项目不会换行，就不会产生多条轴线。

当你 flex-wrap 设置为 wrap 的时候，容器可能会出现多条轴线，这时候你就需要去设置多条轴线之间的对齐方式了。

建立在主轴为水平方向时测试，即` flex-direction: row, flex-wrap: wrap`
````
.root{
    align-content：flex-start | flex-end | center | space-between | space-around | stretch
}
````
1. flex-start：各行向弹性盒容器的起始位置堆叠。弹性盒容器中第一行的侧轴起始边界紧靠住该弹性盒容器的侧轴起始边界，之后的每一行都紧靠住前面一行。
2. flex-end：各行向弹性盒容器的结束位置堆叠。弹性盒容器中最后一行的侧轴起结束界紧靠住该弹性盒容器的侧轴结束边界，之后的每一行都紧靠住前面一行。
3. center：各行向弹性盒容器的中间位置堆叠。各行两两紧靠住同时在弹性盒容器中居中对齐，保持弹性盒容器的侧轴起始内容边界和第一行之间的距离与该容器的侧轴结束内容边界与第最后一行之间的距离相等。（如果剩下的空间是负数，则各行会向两个方向溢出的相等距离。）
4. space-between：各行在弹性盒容器中平均分布。如果剩余的空间是负数或弹性盒容器中只有一行，该值等效于'flex-start'。在其它情况下，第一行的侧轴起始边界紧靠住弹性盒容器的侧轴起始内容边界，最后一行的侧轴结束边界紧靠住弹性盒容器的侧轴结束内容边界，剩余的行则按一定方式在弹性盒窗口中排列，以保持两两之间的空间相等。
5. space-around：各行在弹性盒容器中平均分布，两端保留子元素与子元素之间间距大小的一半。如果剩余的空间是负数或弹性盒容器中只有一行，该值等效于'center'。在其它情况下，各行会按一定方式在弹性盒容器中排列，以保持两两之间的空间相等，同时第一行前面及最后一行后面的空间是其他空间的一半。
6. stretch：各行将会伸展以占用剩余的空间。如果剩余的空间是负数，该值等效于'flex-start'。在其它情况下，剩余空间被所有行平分，以扩大它们的侧轴尺寸。

## 子容器flex-item
有六种属性可运用在 item 项目上：
1. order
2. flex-basis
3. flex-grow
4. flex-shrink
5. flex
6. align-self

### order:
定义项目在容器中的排列顺序，数值越小，排列越靠前，默认值为 0
````
.item {
    order: <integer>;
}
````
###  flex-basis:
定义了在分配多余空间之前，项目占据的主轴空间，浏览器根据这个属性，计算主轴是否有多余空间
````
.item {
    flex-basis: <length> | auto;
}
````
默认值：auto，即项目本来的大小, 这时候 item 的宽高取决于 width 或 height 的值。

当主轴为水平方向的时候，当设置了 flex-basis，项目的宽度设置值会失效，flex-basis 需要跟 flex-grow 和 flex-shrink 配合使用才能发挥效果。

当 flex-basis 值为 0 % 时，是把该项目视为零尺寸的，故即使声明该尺寸为 140px，也并没有什么用。
当 flex-basis 值为 auto 时，则跟根据尺寸的设定值(假如为 100px)，则这 100px 不会纳入剩余空间。

### flex-grow 定义项目的放大比例
````
.item {
    flex-grow: <number>;
}
````
默认值为 0，即如果存在剩余空间，也不放大;

当所有的项目都以 flex-basis 的值进行排列后，仍有剩余空间，那么这时候 flex-grow 就会发挥作用了。
如果所有项目的 flex-grow 属性都为 1，则它们将等分剩余空间。(如果有的话)

如果一个项目的 flex-grow 属性为 2，其他项目都为 1，则前者占据的剩余空间将比其他项多一倍。

当然如果当所有项目以 flex-basis 的值排列完后发现空间不够了，且 flex-wrap：nowrap 时，此时 flex-grow 则不起作用了，这时候就需要接下来的这个属性。

### flex-shrink
````
.item {
    flex-shrink: <number>;
}
````
默认值: 1，即如果空间不足，该项目将缩小，负值对该属性无效。

###  flex
flex-grow, flex-shrink 和 flex-basis的简写
````
.item{
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
} 
````
这里可以看出，虽然每个项目都设置了宽度为 50px，但是由于自身容器宽度只有 200px，这时候每个项目会被同比例进行缩小，因为默认值为 1。
同理可得：

如果所有项目的 flex-shrink 属性都为 1，当空间不足时，都将等比例缩小。 

如果一个项目的 flex-shrink 属性为 0，其他项目都为 1，则空间不足时，前者不缩小。

* auto (1 1 auto) 
* none (0 0 auto)
* 1 (1,,1, 0%)
* 0 (0,1,0%)
* 当 flex 取值为一个长度或百分比，则视为 flex-basis 值，flex-grow 取 1，flex-shrink 取 1，
* 当 flex 取值为两个非负数字，则分别视为 flex-grow 和 flex-shrink 的值，flex-basis 取 0%，
* 当 flex 取值为一个非负数字和一个长度或百分比，则分别视为 flex-grow 和 flex-basis 的值，flex-shrink 取 1

###  align-self
 允许单个项目有与其他项目不一样的对齐方式
单个项目覆盖 align-items 定义的属性

默认值为 auto，表示继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch。
````
.item {
     align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
````
这个跟 align-items 属性时一样的，只不过 align-self 是对单个项目生效的，而 align-items 则是对容器下的所有项目生效的。