---
title: flex
tags: CSS
categories: 很高兴认识你
password: 123321
abbrlink: 59381
date: 2017-11-30 17:36:36
---

![flex](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071321.png)

<!-- more -->



# flex讲解

- 采用Flex布局的元素，称为Flex容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为Flex项目（flex item），简称"项目"。
- 容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`

<img src="http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png">

- 容器的属性

```
flex-direction	(排列方向)				   row | row-reverse | column | column-reverse;
flex-wrap		(默认排列在一行,有三个值)	 nowrap | wrap | wrap-reverse;
justify-content (在主轴上的对齐方式)          flex-start|flex-end|center|space-between|space-around;
align-items     (交叉轴上如何对齐)          flex-start | flex-end | center
```

- 子元素的flex属性:`flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

```
flex-grow	属性定义项目的放大比例
flex-shrink	属性定义了项目的缩小比例
flex-basis	属性定义了在分配多余空间之前,浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto
```

1. basis 基准值 在基准值之内grow分配 之外 多余的分配按grow比例分配
2. 超出按照 不够的量收缩 basis失效 看shrink