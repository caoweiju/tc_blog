---
title: 前端通过background实现图片裁剪显示的方法
date: 2019-01-28 23:16:45
tags:
- 图片裁剪
categories: 
- css
---
# 前端图片展示
在前端处理图片是非常常见的，而图片展示又存在一个最大的问题：适应，
可以看看这种情况：
1. 固定空间展示图片
    * 空间是 100px*100px，图片是 60*60
    * 空间是空间是 100px*100px，图片是 160px*240px
    * 空间是空间是 100px*100px，图片是 240px*160px

就图片展示而言，按宽高比进行缩放是最好的结果，宽高1：1的话，放到正方形空间内很好，可是如果是1：1.5的图片需要放到正方形页面容器里面呢？
<!-- more -->
目前需要前端处理的方式是使用background来操作，而不是img元素，需要以下几个属性的配合：
1. background-image ：指定对象的背景图像。可以是真实图片路径或使用渐变创建的“背景图像”
2. background-position ：指定对象的背景图像位置。
3. background-size ：指定对象的背景图像的尺寸大小。
4. background-repeat ：指定对象的背景图像如何铺排填充。
5. background-attachment ：指定对象的背景图像是随对象内容滚动还是固定的。
6. background-origin ：指定对象的背景图像显示的原点。
7. background-clip ：指定对象的背景图像向外裁剪的区域。
8. background-color ：指定对象的背景颜色。

## background的背景图片裁剪
为了使图片能够按照正常的宽高比显示，可以理解成以下几步操作：
1. 固定宽高的容器元素，设置背景图片
2. 把背景图片按照原始的宽高比伸缩，直到能够完全填充住容器元素的背景，容器的宽高比和背景图片的宽高比不一样时，会存在背景图片宽或者高大于容器的情况，；
3. 把背景图片调整，使得多余的图片部分能够相对于容器对称展示，
4. 截去显示在容器外的背景图片，只显示容器内的图片，大功告成；

代码可以如下书写：
````
background-image: url(test.img);
// background-size这个需要注意，大小需要看伸缩时是宽度会有富余还是高度会有富余，有富余者为auto，刚好填充满的就是100%
//比如宽高分别： 容器是100px*100px的，图片是240px*200px，那么在缩小填充容器时图片的高度会先触及到容器的边缘，也就是填满容器时，宽度有富余为auto，高度为100%；
background-size: auto 100%;  // 可以使用cover
background-position: center; //背景图片居中显示，让多余部分相对容器对此展示
background-repeat: no-repeat;
background-origin: border-box; //从border开始填充
background-clip: border-box;  //border外的多余背景图片不展示，截取掉，
````
可以使用background的缩写模式：
````
style="background:url(test.img) center/auto 100% no-repeat border-box border-box"
````
**需要注意的是background-size的书写需要看情况而变**
background-size这个需要注意，大小需要看伸缩时是宽度会有富余还是高度会有富余，有富余者为auto，刚好填充满的就是100%

比如宽高分别： 
容器是100px*100px的，图片是240px*200px，那么在缩小填充容器时图片的高度会先触及到容器的边缘，也就是填满容器时，宽度有富余为auto，高度为100%；
background-size: auto 100%; 
**所以可以考虑使用cover这个值，background-size:cover;**

参考：http://css.doyoe.com/properties/background/background.htm 