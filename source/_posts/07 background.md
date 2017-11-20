---
title: background
tags: HTML
categories: 很高兴认识你
password: password
abbrlink: 17546
date: 2017-11-20 19:05:34
---

![background](http://www.wzsky.net/img2015/uploadimg/20150902/10060221.jpg)

<!-- more -->



# background

> 背景（background）
>
> 每一个盒子元素都是有图层组成，前景包含文本图片边框，元素的背景层可以使用颜色填充，背景图片层在元素的背景层之上。
>
> 简单的来说在css model中我们用一个图层的概念来理解背景。文本（图片或边框）-- 背景颜色 -- 背景图片



## 背景属性

### background-color

> 背景颜色

没什么说的。只需要思考一个问题，为什么不直接`background:red` 而要 `background-color:red`



### background-image

> 背景图片

和上边的问题一致。



### background-repeat

> 背景重复

控制背景重复的属性有四个值。*repeat*（默认） 、*repeat-x*、*repeat-y*、*no-repeat* 

![repeat](/img/css/bg-repeat.png)

默认重复，x方向重复，y方向重复，不重复。但是在使用的时候可能会碰到一些头疼的情况。比如容器尺寸和背景图片的个数恰好会让边界的背景显示的不全。CSS3提供了另外的两个属性`round`、`space`

* round：确保图片不被剪切，调整图片的大小适应背景区域。
* space：确保图片不被剪切，添加空白来使用背景区域。





### background-size

> 背景尺寸

CSS3规定的属性，用来控制背景图片的尺寸。

```html
<style>
  .bg{
    width: 500px;height: 300px;margin: 100px auto;background-image: url(xxx.jpg);
    background-size: cover;
    /* background-size: contain; */
    /* background-size: 50%; */
    /* background-size: 100% 50%; */
    /* background-size: 500px 300px; */
  }
</style>
<body>
  <div class="bg"></div>
  <div class="bg"></div>
  <div class="bg"></div>
  <div class="bg"></div>
</body>
```

* cover：拉伸，完全填满背景区，保持宽高比。
* contain：缩放，**恰好**适应背景区，保持宽高比。
* 50%：缩放，填充一半，保持宽高比。（给两个就不会保持了）
* 500px 300px：宽度500px，高度300px。





### background-position

> 背景位置

控制背景图片的位置，css中提供了top、left、right、bottom、center属性，当然也可以使用px去调整。雪碧图运用。

可以去组合

*example* 

```css
/* 需要注意的是 top right与right top 是没有区别的 */
/* 只设置一个center代表水平，另一个方向会设置成center所以center center的组合和写一个center是一样的 */
/* 100px 200px则是距离div左上角的位置 left 100px top 200px位置 */
div{background-image: url(url);background-position: center;}
div{background-image: url(url);background-position: top left;}
div{background-image: url(url);background-position: right bottom;}
div{background-image: url(url);background-position: 100px 200px;}
```





### background-attachment

> 背景粘附





### background

> 简写



