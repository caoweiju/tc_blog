---
title: HTML元素的height、offsetHeight、clientHeight、scrollTop等梳理
date: 2020-09-30 11:53:46
tags:
- 元素尺寸
categories: 
- HTML
---

# 关于元素的一些属性
在前端的日常开发中，我们经常无可避免的需要获取或者监听一些页面的属性，那么我们需要经常了解一些属性代表的含义才能更好地使用这些属性。特别是一下这些：

1. 尺寸相关：offsetHeight、clientHeight、scrollHeight；
2. 偏移相关：offsetTop、clientTop、scrollTop、pageYOffset、scrollY；
3. 获取相对视口位置：Element.getBoundingClientRect()；
4. 获取元素的style对象：Window.getComputedStyle(Element)；

<!-- more -->

## 属性的定义
关于尺寸相关的属性定义：
- offsetHeight: Element.offsetHeight是一个只读属性，返回的是元素对应的高度px的值，是一个整数值，不存在小数，
    1. 隐藏元素返回0；
    2. 其他返回：元素的innerHeight + padding + border + margin + 滚动条；但是不包括里面的::before or ::after伪元素；
- clientHeight: Element.clientHeight是一个只读属性，返回的是元素对应的高度px的值，是一个整数值，不存在小数，
    1. 对于没有设置样式或者inline元素而言，返回的是0，
    2. 对于html元素或者怪异模式下的body，返回的是viewport高度，也就是整个页面视口高度
    3. 其他情况下：元素的innerHeight + padding；不包括border、margin、滚动条；
- scrollHeight: 是一个只读属性，返回的是元素对应的高度px的值，是一个整数值，不存在小数，
    1. 在子元素不存在滚动情况下，和Element.clientHeight一样
    2. 在子元素存在滚动情况下，会是所以子元素的clientHeight高度之和 + 自身padding；
- window.innerHeight: (浏览器窗口高度，不包含工具栏，菜单等，仅仅是可视区域dom的height)
- window.outerHeight: (浏览器窗口高度，包含工具栏、菜单等,整个浏览器的height)

关于偏移：
- offsetTop：只读属性，返回元素距离最近一个相对定位的父元素内边线的顶部距离，实际使用时可能存在不同样式引起的相对定位父元素不一致的兼容性问题。
- clientTop：上边框的宽度
- scrollTop：
    1. 对于滚动元素而言，就是已经滚动的距离，
    2. 对于html而言，就是window.scrollY
- window.scrollY，别名：window.pageYOffset，根节点已经垂直滚动的距离

## 开发中所需的相关数据

获取整个页面的可视区高度：【不需要可视区外的高度】

````
const height = window.innerHeight
    || document.documentElement.clientHeight
    || document.body.clientHeight;
````

获取整个页面的高度：【包括可视区外的】

````
const height = document.documentElement.offsetHeight
    || document.body.offsetHeight;
````

获取整个页面的垂直滚动高度：

````
const scrollTop = document.documentElement.scrollTop
    || document.body.scrollTop;
````

获取元素相对根节点顶部的距离：

````
// 对于相对于根节点定位的元素
const top = Element.offsetTop;

// 对于非相对于根节点定位的元素，需要循环获取
getElementTop(element) {
      let actualTop = element.offsetTop
      let current = element.offsetParent

      while (current !== null) {
        actualTop += current.offsetTop
        current = current.offsetParent
      }
      return actualTop
}

// 还有一中方法 滚动距离 + 距离视口上边距
const top = Element.getBoundingClientRect().top + window.scrollY；

````

获取元素相对可视区域顶部距离：

````
const top = Element.getBoundingClientRect().top;
````

设置整个页面的垂直滚动位置：

````
const isCSS1Compat = ((document.compatMode || "") === "CSS1Compat");
if (isCSS1Compat) {
    document.documentElement.scrollTop = 100;
} else {
    document.body.scrollTop = 100;
}

````

