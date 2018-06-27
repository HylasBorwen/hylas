---
title: component
tags: Vue
categories: Vue 探索与实践
abbrlink: 54713
date: 2018-06-21 16:58:48
---

日常开发中碰到重复的代码都会自然而然的去想到复用, 封装. Vue中最主要的思想就是组件化, 将一个页面不断的拆分成多个单独的组件,有利于后期的维护与更改. 高内聚, 低耦合, 一起来学习一下Vue中的组件 *component*

![component](https://cn.vuejs.org/images/components.png)

#### Vue 组件基本使用

```js
// 定义一个名为 button-counter 的新组件 
// 定义组件名的方式有两种 kebab-case PascalCase 推荐前一种, 后一种可能会在使用的时候失效
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

这样定义的组件是在全局注册的, 也就是说在任何一个Vue实例中都可以使用这个组件. 这里边需要注意的地方在于 `data` 在正常的Vue实例中, data是一个对象, 但是在组件中**一定要写成函数的形式**, 组件都是会被复用的每一份都应该是独立的对象, 如果不是函数,那么组件调用的时候会影响到其它的Vue实例

组件局部注册了就只能在当前注册的Vue实例中使用

```js
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }
new Vue({
  el: '#app'
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

官网中还有这么一段[描述](https://cn.vuejs.org/v2/api/#name)关于组件的, 大致的意思就是组件的名字最好是显式的写出来, 具有更好的语义信息, 方便在  [vue-devtools](https://github.com/vuejs/vue-devtools) 中查看