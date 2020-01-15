---
title: 移动端Web元素进入可视区曝光的优雅处理实践
date: 2020-01-15 15:37:11
tags:
- debug
categories: 
- tool
---

## 曝光
在移动端的产品设计中，很多广告和流量都比较关心产品的曝光度，对于开发人员而言，处理曝光的确是一个比较棘手的问题；特别是开发人员对于曝光的定义，常见于以下几种：

- 进入过可视区的数据，无论是否停留，即使快速滑过也需要计算
- 用户视觉上有所停留的数据，必须是在可视区停留过的元素才算

这样不同的要求就需要不同的实现处理；

<!-- more -->

## 常见处理

目前对于web端而言，曝光比较常见的是依赖滑动scroll来处理的；


## IntersectionObserver优雅实践




参考：https://www.zhihu.com/question/67328049 and https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver and https://www.smashingmagazine.com/2018/01/deferring-lazy-loading-intersection-observer-api/
