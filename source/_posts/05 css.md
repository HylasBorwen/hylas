---
title: 认识css
tags: HTML
categories: 很高兴认识你
abbrlink: 35262
date: 2017-11-15 19:38:29
---

![css](https://udemy-images.udemy.com/course/750x422/792484_cc98_3.jpg)

<!-- more -->

> 认识 CSS --- Cascading Style Sheets
>
> CSS的世界是神奇的。随着浏览器WEB标准的日趋统一，CSS在WEB世界中的扮演的角色也越来越重要。
>
> 我们从HTML开始，因为CSS的用途就是为了给HTML标记添加样式，所以我们要先知道怎么去写HTML标签



### 1. 什么是CSS

* HTML标记内容是为了给网页赋予纯粹的**语义**。换而言之就是为了让用户可以去理解里边的含义。每一个标签都是对所包含的内容的一种诠释，描述。所以请记住*HTML* 就是`文本`+`标记`的一个文档结构（请不要参杂CSS）。当我们给内容都打上标记，就可以使用CSS给标记添加样式了。添加样式的过程根据标签名、标签属性、标签等等的一些关系来给相对应的标签添加样式，*so！* 先有 **结构**后有**样式**。
* 简单的来说CSS相当于一个神奇的化妆师，它可以去操作文档的整体表现形式，针对布局、文字、颜色、背景、动画效果等等实现精确的控制，让文档的表现更加的美观好看，它的组成是由一系列有含义的单词和数值所构成。
* CSS样式可以直接存储于HTML网页或者单独的样式单文件。无论哪一种方式，样式单包含将样式应用到指定类型的元素的规则。外部使用时，样式单规则被放置在一个带有文件扩展名`.css`的外部样式单文档中。简单的了解这个概念之后我们来学习一些基础知识。





### 2. 添加样式的三种方式

有三种方法可以把CSS样式添加到网页中，分别是行内样式、嵌入样式、链接样式

```html
<!-- 行内样式 -->
<p style="font-size:12px;font-weight:bold;color:red;background:yellow">Hello world</p>
```

```html
<!-- 嵌入样式 -->
<head>
  <style>
    p{font-size:12px;color:red;background:yellow}
  </style>
</head>
```

```html
<!-- 链接样式 -->
<link href="style.css" rel="stylesheet" type="text/css">

<!-- 要注意的是@import指令必须出现在样式表其它样式之前，否则不会被加载 -->
<style>
  @import url(style.css); /* 注意看这里有一个分号 */
</style>
```

`ps` ：网页的解析是从上到下，从左至右。当浏览器遇到开标签&lt;style&gt;时，就会有解析HTML代码切换成为解析CSS样式代码。再次遇到 &lt;/style&gt;时，浏览器会再次切换成为解析HTML代码





### 3. CSS规则

> 构成CSS规则有很多，主要就是选择器。这里我们只需要掌握常用的选择器，碰到特殊的再去查询。



#### 3.1 命名惯例

​	选择符 {属性：值；}

​	p { color : red; }



#### 3.2 选择器的分类

* 上下文选择器
  * 一般上下文选择器
  * 特殊上下文选择器
    * 子选择器 &gt;
    * 紧邻同胞 +
    * 一般同胞 ~
* id和class选择器
  * \#id{属性：值；}
  * \.class{属性：值；}
  * tag.class{属性：值；}
* 属性选择器
  * tag[属性名]
  * tag[属性名="属性值"]

> `ps`： 什么时候使用id、class、属性选择器？
>
> 1. id 的用途是在页面标记中唯一地标识一个特定的元素。 
> 2. 类是可以应用给任意多个页面中的任意多个 HTML 元素的公共标识符 。简单来说具有相同的特征的元素
> 3. 基于属性名和属性的其它特征选择元素，区别对待相同标签，通过不同的标记找到适合的元素。



#### 3.3 CSS选择器图解

> 我们可以通过图形来理解一下

1. 一般选择器

   ```html
   <style>
     /* 选择以p为祖先的 所有 span元素 */
     p span{color: red;}
   </style>
   <body>
     <h1><b>一般选择器</b></h1>
     <p>
       <span>this is span</span>
       <a><span>this is a</span></a>
       <span>this is anthor span</span>
     </p>
   </body>
   ```

   ![](/img/css/选择器.png)

2. 特殊选择器

   * 子选择器 &gt;

     ```html
     <style>
       /* 标签1 > 标签2 也就是说1必须是2的父元素 */
       p > span{color: red;}
     </style>
     <body>
       <h1><b>一般选择器</b></h1>
       <p>
         <span>this is span</span>
         <a><span>this is a</span></a>
         <span>this is anthor span</span>
       </p>
     </body>
     ```

     ![](/img/css/子代选择器.png)

   * 紧邻同胞

     ```html
     <style>
       /* 标签1 + 标签2  标签2必须紧跟在标签1后边 */
       h1 + p{color: red;}
     </style>
     <body>
         <h1>紧邻同胞</h1>
         <p>1</p>
         <p>2</p>
         <p>3</p>
     </body>
     ```

     ![](/img/css/紧邻同胞选择器.png)

   * 一般同胞

     ```html
     <style>
       /* 标签1 ~ 标签2  标签2必须跟( 不一定紧跟 )在标签1后边 */
       h1 ~ p{color: red;}
     </style>
     <body>
         <h1>紧邻同胞</h1>
         <p>1</p>
         <p>2</p>
         <p>3</p>
     </body>
     ```

     ![](/img/css/一般同胞选择器.png)

   * id、class选择器 相当于警察叔叔直接查你的身份证（唯一性）、和查你的学生证（你有很多张从小学到大学做为一个特征，你是一个学生）

     > `ps` :  
     >
     > 1. 只不过有一个标签带类选择器 更加精确的定位特定的标签元素 (同理id选择器也具有同样的功能)
     > 2. 多类选择 eg: &lt;p class="a b"&gt;&lt;/p&gt; 可以这样子去写 .a.b{color: red}




未完待续~




