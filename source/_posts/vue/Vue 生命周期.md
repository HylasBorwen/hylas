---
title: Vue 生命周期
tags: Vue
categories: Vue 探索与实践
abbrlink: 57065
date: 2018-09-29 11:26:19
---

## Vue 生命周期

又好久没有写总结了~本着一探究竟总结一下使用Vue过程中碰到的问题。

先放一张Vue官网的LifeCycle生命周期图

![生命周期](https://cn.vuejs.org/images/lifecycle.png)

Vue每一个组件都有自己完整的生命周期，每一个钩子函数是什么含义先了解一下

+ beforeCreate

  创建前：在Vue实例被创建时 init初始化 event事件，加载LiftCycle调用该函数，在这个生命周期函数中是无法获取到vm实例中的props与data的

+ created

  创建后：在beforeCreate与created之间会执行一些函数injections 与 reactivity 、initState动作会将vm中的props与data进行绑定，其中的细节可以不用去考虑，需要理解的是在这两个hook之间发生了什么事情，也就是说这两个hook区别就在于 不要在 *beforeCreate*  中去修改data数据因为访问不到。但是有一点在*beforeCreate* 这个hook在执行的过程中会有 vue-router、vuex的逻辑这个等下解释

+ beforeMount

  挂载前：初始化完成动作结束，需要将render渲染的VDOM挂载到 `vm.el` 对象上，从上边的图可以看的到在beforeMount动作之前会去检测是否传入了 el、template而执行相对应的操作以及会选择编译模式执行不同的渲染方式。
  **Runtime Only** 与 **Runtime+Compiler** 什么区别呢？简单来说前者是会通过vue-loader将 `.vue` 文件编译成为一个对象把其中的template标签编译为render函数，后者new Vue中传入template参数，vue2都是最终通过render函数去渲染，效率可能没有前者快。但是这个编译方式就是完整版的如果没有特殊要求通常使用前者就好。

+ mounted

  挂载后：这时是 **可以访问到DOM对象**，数据的改变也会触发响应的更新hook函数。

+ beforeUpdate 更新前

+ updated 更新后

+ beforeDestroy 销毁前

+ destroyed 销毁后

*tips*：需要注意的一点是VDOM的变化都是异步所以如果有些操作需要在挂载之前就完成的通常会在created中执行，例如通过$ref获取的节点但是执行了相应的操作却没有生效。记得加 `$nextTick` 在VDOM构建完成之后执行。

Vue的每一个组件都具有完整的生命周期，组件与组件之间的数据操作，传递，响应就要看清楚应该在什么阶段去执行了。组件的之间的关系无非就是子父组件，父父组件。



> 在做项目的过程中并未使用vuex（不是很大，没必要），通过Bus, router去操作一些数据的传递发现了一些问题

### 子父组件与父父组件生命周期对比

子父组件

在beforeMount与mounted之间会执行render，也就是说子组件做为父组件一部分会在这个阶段完成自己的声明周期过程。

```vue
<template>
  <div>
    <child></child>
  </div>
</template>

<script>
import child from './B'
import {group} from '@/common/config/log'
export default {
  name: 'A',
  beforeCreate() {
    group('red', 'beforeCreate 创建 A')
  },
  created() {
    group('red', 'created 创建完成 A')
  },
  beforeMount() {
    group('red', 'beforeMount 挂载状态 A')
  },
  mounted() {
    group('red', 'mounted 挂载完成 A')
  },
  beforeUpdate() {
    group('red', 'beforeUpdate 更新状态 A')
  },
  updated() {
    group('red', 'beforeUpdate 更新完成 A')
  },
  beforeDestroy() {
    group('red', 'beforeUpdate 销毁状态 A')
  },
  destroyed() {
    group('red', 'beforeUpdate 销毁 A')
  },
  components:{
    child
  }
}
</script>
```

子组件与父组件的代码一样不贴了，看效果

![lifecycle](\img\javascript\lifecycle.png)

可以看到子组件的生命周期在父组件的beforeMount与mounted之间触发。在子父组件通信方式有很多通过Bus的方式，例如子组件在 created 或 mounted中 emit一个事件传递数据，父组件在创建与挂载的hook中都是可以监听到子组件传递的数据，监听始终都会在emit之前触发。只不过有一个小问题（重复绑定）稍后说。

父父组件

之间看结果从A→B切换需要传递数据

![lifecycle](\img\javascript\lifecycle2.png)

在A中的mounted中定义Bus.$emit传递数据在B中的created中接受，会发现根本接受不到数据，原因通过上边的图可以看到 **A在mounted中emit自定义事件的时候**， **B的created中Bus.$on的事件是没有被触发的**。打个比方你要去窃听别人说话的内容是要在别人说话前就把装置安装好还是说完了你再去安装！

解决方案：可以发现组件A的destroyed过程是在组件B的created之后触发的也就是A组件emit的数据应该放到销毁的hook中，而在B组件中在created中去监听传递的数据。mounted阶段也不行因为同样A已经销毁了。



### Bus的坑

在小的数据传递中通常用Bus或者router都可以解决一些数据传递的问题，在上边说hook函数的时候提到了router的逻辑会在beforeCreate中去执行，也就是说在beforeCreate虽然获取不到data但是可以获取到router的params、query参数，这个自行测试。同样也是传递数据。

上边的例子中通过子父组件、父父组件中使用Bus传递数据貌似完美解决了问题。其实不然，回到这句话**A在mounted中emit自定义事件的时候**， **B的created中Bus.$on的事件是没有被触发的**。但是当我们返回A组件（未刷新页面）在到B组件的时候发现B组件监听到了A的emit事件，反复会发现每次都会重复触发多次。这是因为 **Bus.$on并不会随着组件销毁而解除因为触发监听了所以第二次进入B的时候会触发。** 问题就是如何不让其重复的去监听emit。尤大在[issue ](https://github.com/vuejs/vue/issues/3399)给出了解决方式。在B组件beforeDestroy 或 destroyed中解除Bus.$off绑定的事件就不会重复触发了。

引出一个问题：如果在多个页面都会触发同一个emit事件岂不是在每一个页面的销毁hook中都要写一段Bus.$off.其实在issue中尤大提出了一点通过mixin方式也可以。



### mixin

介绍看官网详情[mixin ](https://cn.vuejs.org/v2/guide/mixins.html)即可，使用mixin的目的在于如果多个组件都会复用某个逻辑功能就可以在全局进行方法或hook的混入，但是需要注意的是混入对象的钩子将在组件自身钩子**之前**调用。data、props、methods合并策略与钩子函数不同。

```js
// 假设B、C页面都会接受A传递来的数据 通过Bus使用了解到，需要在组件销毁时注销监听
const mixin = {
    destoryed() {
        // 需要接触的事件
        Bus.$off('params')
    }
}
new Vue({
    mixins: [mixin]
})
```

混入使用的好可以对代码的复用起到很好的帮助

总结一下Vue生命周期

+ Vue的生命周期函数就是在`new Vue()` 初始化以及数据更新过程中各个阶段执行的特定函数（钩子函数）
+ 在不同的生命周期函数中执行相对应的操作，在created获取data，在mounted访问DOM，destoryed销毁事件解除定时器等等操作。

取乎其上，得乎其中；取乎其中，得乎其下；取乎其下，则无所得矣。列出来几个问题。未来补充~

+ Vue生命周期
+ Vue如何实现双向绑定
+ 使用CSS Module，原理Vue中的使用
+ 开启CSS Module后怎么与第三方样式库
+ Vue的安装包版本，自定义构建工具的使用（vue-cli官方的，手动构建会碰到什么问题）
+ Vue-router 原理
+ webpack中 dev-server原理
+ webpack中loader实现，如何做任务管理（npm scripts）

