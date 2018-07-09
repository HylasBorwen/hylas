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



#### props

如果把一个文章结构拆成多个组件，组件之间肯定会进行数据传递。通过父组件传递数据的同时需要在子组件中定义接收数据的属性

```js
Vue.component('blog-title', {
    props: ['title']
})
```

```html
<blog-title title='hello'></blog-title>
<blog-title :title='world'></blog-title>  world需要是一个表达式的值
<blog-title :title='123'></blog-title>    可以传递 虽然123是静态的数字
```

`v-bind` 绑定需要一个表达式，bind的数据是动态传递的。如果是 *String* 类型就需要声明出来，如果是数字字符串则可以不需要，会被解析成字符串。

props在子组件中定义的时候可以定制验证

```js
props: {
    propA:{
        type: Number
        default: 0,
        required: true
    },
    propB: {
        type: [String, Number]
    } 
}
```

其中数组， 对象在定义 `default` 的时候需要返回一个函数。自定义验证函数

```js
props: {
    propA: {
        type: Array,
        default: function() {
           return []
        }
    },
    propB: {
        type: String,
        validator(val) {
            return val.length >= 3
        }
    }
}
```

props的验证不是强制的还是会被渲染到页面，但是不满足验证的条件会报出相应的警告。

官网有一个很重要的注意！！

> 注意那些 prop 会在一个组件实例创建**之前**进行验证，所以实例的属性 (如 `data`、`computed` 等) 在 `default` 或 `validator` 函数中是不可用的。 

非props特性

```html
<child class='data-picker'></child>
<script>
    Vue.component('child', {
        template: `<div class='show'></div>`
    })
</script>
```

如果组件中没有定义这个 `data-picker` 会自动添加到child的根元素上。看一个官网的案例非props特性结合 `$attrs` 可以让传递的数据赋予给特定的元素

```html
<div id="app">
    <base-input
      v-model="username"
      class="username-input"
      placeholder="Enter your username"
    ></base-input>
    {{username}}
</div>
<script type="text/javascript">
    Vue.component('base-input', {
      // 禁用特性继承
      inheritAttrs: false, 
      props: ['label', 'value'],
      template: `
        <label>
          {{ label }}
          <input
            v-bind="$attrs"
            :value="value"
            @input="$emit('input', $event.target.value)"
          >
        </label>
      `
    })

    let vm = new Vue({
        el: '#app',
        data: {
            username: 'hylas'
        }
    });
</script>
```

例子中可能有人可能不理解为什么传递的 `username` 可以传递给父级改变父级的数据。其实

 `v-model="username"`  可以替换成 `:value.sync="username"` 对应的触发事件可以替换成 `@input="$emit('update:value', $event.target.value)"` 或者进行自定义事件绑定修改父级的数据， v-model的内容是 *vue2x* 的一个重要使用稍后在组件通信中探讨。



#### 绑定原生事件

有时候我们可能有这样的需求，给一个组件本身添加事件

```html
<child @click="handelClick"></child>
```

结果可能没有效果，因为事件是需要给元素去进行绑定，在组件上绑定事件是子组件自定义事件。`this.$emit('click')` 触发的是父组件中定义的 `handelClick` 并不是原生的 click事件。

```html
<child @click.native="handelClick"></child>
```

通过添加 native （原生）就可以在组件上绑定对应的原生事件了



#### slot

插槽可以实现在组件中分发内容的功能有些场景子组件中的内容可能不确定需要在组件定义的时候进行覆盖

```html
<div id="app">
    <child></child>
    <child>确定</child>
</div>
<script type="text/javascript">
    Vue.component('child', {
      template: `
       <button @click="handel"><slot>提交</slot></button>
      `,
       methods: {
           handel() {
               // ...
           }     
       }
    })

    let vm = new Vue({
        el: '#app'
    });
</script>
```

第一个子组件内容显示提交，在没有写内容的时候slot中的默认内容会直接写在组件中，第二个子组件显示确定覆盖了默认的提交

##### 具名插槽

如果有多个内容需要从子组件定义的时候传入的会出现什么情况呢？

```html
<div id="app">
    <child>
        <button>确定</button>
        <button>取消</button>
    </child>
</div>
<script type="text/javascript">
    Vue.component('child', {
      template: `
      	<div>
      		<div><slot><button>提交</button></slot></div>
      		<div><slot><button>重置</button></slot></div>
      	</div>
      `
    })

    let vm = new Vue({
        el: '#app'
    });
</script>
```

可能结果不尽人意，slot中的内容重复出现了两次确定、取消。当有多个slot的时候就需要给slot起名字了

```html
<div id="app">
    <child>
        <button slot="confirm">确定</button>
        <button slot="cancel">取消</button>
    </child>
</div>
<script type="text/javascript">
    Vue.component('child', {
      template: `
      	<div>
      		<div><slot name="confirm"><button>提交</button></slot></div>
      		<div><slot name="cancel"><button>重置</button></slot></div>
      	</div>
      `
    })

    let vm = new Vue({
        el: '#app'
    });
</script>
```

##### 作用域插槽

可从子组件获取数据的可复用的插槽。也就是父组件可以从子组件接受数据，并且可以选择用什么去渲染数据模板 

```html
<div id="app">
    <child>
        <template slot-scope='props'>
            <ul>
                <li>{{props.item}}</li>
            </ul>
        </template>
    </child>
</div>
<script type="text/javascript">
    Vue.component('child', {
      data() {
         return {
            list: [1,2,3,4]
         }
      },
      template: `
        <div>
            <slot v-for='item of list' :item=item>
            </slot>
        </div>
      `
    })
    let vm = new Vue({
        el: '#app'
    });
</script>
```

固定写法父组件在接受的时候需要通过 `template` 包含内容 `:item` 绑定的数据对应 `slot-scope='props'` 。可以看的出来props把每一个item放到了一个对象中输出。



#### 动态组件 v-once keep-alive

组件之间难免会有切换的情况。`component` `:is` 组合决定显示哪个组件

```html
<div id="app">
    <keep-alive>
        <component :is="type"></component>
    </keep-alive>
    <button @click="toggle">toggle</button>
</div>
<script type="text/javascript">
    Vue.component('one', {
        created() {
            console.log('one')
        },
        template: `<div v-once>one</div>`
    })

    Vue.component('two', {
        created() {
            console.log('two')
        },
        template: `<div v-once>two</div>`
    })

    let vm = new Vue({
        el: '#app',
        data: {
            type: 'one'
        },
        methods: {
            toggle() {
                this.type = (this.type === 'one' ? 'two' : 'one')
            }
        }
    });
</script>
```

`v-once` 只渲染元素和组件**一次**。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。 也就是说这种经常切换的组件一般来说内容不会经常变更可以在组件定义的时候通过这个指令将其变成静态内容。有利于性能。

还有另一种情况则是在组件切换的过程中想要保持组件的状态，例如两个文章内容第一个有滚动条，一个没有当把第一个滚动条滚动到一定位置，在切换到文章二再切换回文章一此时滚动条的位置应该是不变的，更有利于体验。重新创建动态组件都会执行一遍组件的生命周期，vue提供了 `keep-alive` 将组件在创建过一次后缓存到内存中，第二次就不会执行创建组件的过程。在单页应用中保存组件状态尤为明显。

