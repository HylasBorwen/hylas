---
title: layout
date: 2017-12-02 23:14:04
tags: layout
categories: 很高兴认识你
password： xq
---

![layout](https://wallpapers.wallhaven.cc/wallpapers/full/wallhaven-375080.jpg)

<!-- more -->

## layout

> 布局是UI设计师将有限的视觉元素进行有机的排列组合。将理性的思维个性化的表现出来是一种具有个人风格和艺术特色的视觉传达方式。

分栏又称为分列，常见的布局有一列、两列、三列、**混合布局**。我们需要通过css中的浮动定位完成UI设计中的布局要求，所以前端工程师就是将艺术与技术完美融合的岗位。



## 多列布局

### 一列布局

单页的应用没有太多的内容，文字内容较少。

```html
<style>
  .header{height: 80px;background: #ddd;}
  /* 主体main高度肯定是要靠内容去撑起来的实际中不用给 */
  .main{width: 800px;height: 800px;background: #111;}
  .footer{height: 80px;background: #333;}
</style>
<body>
  <div class="header"></div>
  <div class="main"></div>
  <div class="footer"></div>
</body>
```



### 两列布局

