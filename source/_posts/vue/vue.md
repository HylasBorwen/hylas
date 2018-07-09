---
title: vue初始化
tags: Vue
categories: Vue 探索与实践
abbrlink: 24839
date: 2018-06-15 14:56:30
---

# Vue 初步认识

特点

- 对浏览器兼容要求不高，不需要兼容至IE6-8
- SPA( Signle page web application ) 单页web应用开发
- 对性能较高要求
- 组件化

需要具备一定的 HTML CSS Javascript 知识以及一些ES6的语法, 了解Vue可以做什么,有什么东西是我们的目的, 然后在深入的去学习.



### vue-cli 使用

安装了node环境, npm是随着node安装的包管理工具, webpack模块加载器, 对我们应用程序中每一个模块进行打包

* 安装vue-cli 全局安装

  > npm install vue-cli -g

* 创建项目

  > vue init webpack-simple name (英文)

  这个时候应该显示了一个关于项目的描述 没有特别需要一路回车下去就好了, 只是有两个地方需要注意就是测试和Eslint语法检测, Eslint语法检测可能很多人不习惯, 可以选择不配置.

* 安装依赖

  因为npm 可能安装比较慢可以将npm源设置成国内的淘宝镜像

  运行指令 npm install -g cnpm --registry=https://registry.npm.taobao.org 之后就可以使用cnpm执行npm的指令了

  > cd name (项目文件中)
  >
  > cnpm install ( 2.x 会显示是否安装vue-router )
  >
  > npm run dev 
  >
  > 打开localhost:8080 至此项目创建完成



### 项目文件夹的各个部分

- build：最终发布代码的存放位置。里边有webpack的配置文件
- config：配置目录，包括端口号等。我们初学可以使用默认的。
- node_modules：这是执行 npm install 后产生的，里面包含了 Node.js 和 npm 依赖的文件以及后续安装的第三方组件或者第三方功能。
- src:我们要开发的目录，用于存放页面相关的文件，组件,引入的 *Resourse* 都会放到这里
  - assets: 放置一些图片，如：Logo等；( 完全可以删除 )
  - components: 存放了一个组件文件，( 完全可以删除 )
  - App.vue：是我们的主文件，项目入口文件
  - main.js: 项目的核心文件, 最终生成的文件
  - router：其中包括的index.js，则是项目的路由
- static：一般用于存放静态资源，如图片、字体等。里边还有一个 `.gitkeep` 保证可以执行git
- .babelrc文件：用来设置转码的规则和插件的，一般情况不需要设置。
- .editorconfig：编码设置, 代码缩进, 空格, 末尾插入新行, 去掉两端空格等
- .eslintignore：eslint代码风格检测忽略的文件
- .eslintrc：eslint的配置
- .gitignore文件：如果你需要将项目放到 git 目录里，你又不想提交其中的某一部分文件，那么你就可以在.gitignore文件中添加它们。 
- index.html：主页
- package.json：项目的描述,依赖管理所安装的包，里面包含项目的各类信息，如第三方版本,主要关注一下script里边的配置
- README.md：说明文档



### Vue 实例中的成员

如果不是通过vue-cli创建的项目,创建一个Vue的实例

```js
var vm = new Vue({
    // 成员
});
```

官网描述

> 每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置**数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等**。同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，这给了用户在不同阶段添加自己的代码的机会。 

![vue生命周期](https://cn.vuejs.org/images/lifecycle.png)

**1.beforeCreate**

在实例初始化之后，数据观测（data observer）和 event/watcher 事件配置之前被调用。

**2.created**

在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测（data observer）、属性和方法的运算、watch/event 事件回调。然而，挂载阶段还没开始，$el 属性目前不可见。

**3.beforeMount**

在挂载开始之前被调用，相关的 render 函数将首次被调用。

注意：该钩子在服务器端渲染期间不被调用。

**4.mounted**

el 被新创建的 vm.el替换，并挂载到实例上去之后调用该钩子。如果root实例挂载了一个文档内元素，当mounted被调用时vm.el替换，并挂载到实例上去之后调用该钩子。如果root实例挂载了一个文档内元素，当mounted被调用时vm.el 也在文档内。页面渲染完成后初始化的处理都可以放在这里。

注意：mounted 不会承诺所有的子组件也都一起被挂载。

**5.beforeUpdate**

数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前。

你可以在这个钩子中进一步地更改状态，这不会触发附加的重渲染过程。

注意：该钩子在服务器端渲染期间不被调用。

**6.updated**

由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用计算属性或 watcher 取而代之。

注意：updated 不会承诺所有的子组件也都一起被重绘。

**7.activated**

keep-alive 组件激活时调用。

注意：该钩子在服务器端渲染期间不被调用。

**8.deactivated**

keep-alive 组件停用时调用。

注意：该钩子在服务器端渲染期间不被调用。

**9.beforeDestroy**

实例销毁之前调用。在这一步，实例仍然完全可用。

注意：该钩子在服务器端渲染期间不被调用。

**10.destroyed**

Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。

注意：该钩子在服务器端渲染期间不被调用。

以上这些生命周期钩子函数需要在不断的实践中才可以体会. 除了这些Vue实例中还有其它的一些元素

```js
new Vue({
    el: '#app',
    data: {
        msg: 'Hello Vue.js'
    },
    methods: {
        
    },
    watch: {
        
    },
    computed: {
        
    }
})
```

**methods watch computed** 这是那个都是函数区别有哪些呢?

* methods 方法

  通过DOM操作触发这些函数 进行 `v-on` 的事件绑定, 也可以在当前Vue实例中调用this.FunName调用, 需要手动执行

* watch 侦听器

  *watch* 与 *computed* 预先定义函数, 可以理解为预备队 两者都是在数据发生变化之后进行相应的处理, 会自动调用

  watch中的配置可以在官网看到详细的说明  `hander` ,  `deep` , `immediate ` 

* *computed* 计算属性 依赖其它的数据发生改变而调用, **计算属性是基于它们的依赖进行缓存的** ,只要依赖数据没有改变,访问数据都会立马返回原有数据, 如果使用普通的方法, 多次执行都会重新调用方法

* 对比 

  *watch* 擅长处理的场景: 一个数据影响多个数据

  *computed* 擅长处理的场景: 一个数据受多个数据影响



### Vue 指令

渲染区别? 原生的与Vue的渲染都是在满足一定条件进行渲染,. 原生 `visibility: hidden ` `display:none` ,前者针对元素的可见性, 布局不会改变, 后者会重新进行布局,多的不谈. 但是无论是前者后者DOM都已经存在并且占用的资源.

#### 条件渲染 与 列表渲染

关于**v-show,  v-if** 官网解释 很详细了.

> `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 CSS 属性 `display` 
>
> `v-show` 不支持 `<template>` 元素，也不支持 `v-else`。 
>
> `v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。
>
> `v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
>
> 相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
>
> 一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。
>
> 当 `v-if` 与 `v-for` 一起使用时，`v-for` 具有比 `v-if` 更高的优先级。 

```html
<div id="app">
    <template v-if="flag"> <!-- v-show 不行 -->
        <div >1</div>
    </template>
    <template v-else="flag">
        <div >2</div>
    </template>
</div>
<script type="text/javascript">
    new Vue({
        el: '#app',
        data: {
            flag: true
        }
    })
</script>
```

**v-for **指令根据一组数组的选项列表进行渲染

**v-bind** 动态的改变一些属性 `class` `src` `id`等, vue 语法风格简写 `:attr`  **bind**结合**filter**味道更好

```html
<!-- 单个class -->
<div v-bind:class="{ active: isActive }"></div>

<!-- 条件 -->
<div v-bind:class="{'active' : count > 0}"></div>
                                           
<!-- 多个class -->
<div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>

<!-- 计算属性 -->
<div v-bind:class="computedClass"></div>

<!-- 三目运算 注意是数组 不习惯替换成计算属性 -->
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>

<!-- src -->
<img :src="data.icon">
```



### Vue 模板语法

官网对Vue的介绍

> Vue.js 使用了基于 HTML 的模版语法，允许开发者声明式地将 DOM 绑定至底层 Vue.js 实例的数据。 Vue.js 的核心是一个允许你采用简洁的模板语法来声明式的将数据渲染进 DOM 的系统。 结合响应系统，在应用状态改变时， Vue.js 能够智能地计算出重新渲染组件的最小代价并应用到 DOM 操作上。 

```vue
<template>
	<!-- html -->
</template>

<script>
    // js
</script>

<style>
    /* css */
</style>
```

最终渲染到页面其实还是html,css,js的页面
