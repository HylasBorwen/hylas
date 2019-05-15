---
title: 实现MVVM
date: 2019-05-15 23:48:28
tags: Vue
categories: Vue 探索与实践
---

> Vue做为一门热门的前端框架，已经成为了前端开发必不可少的一项技能。之前一直懒癌发作只是了解它的原理但是从来没有实践操作，看了些资料最后实现了简单的MVVM功能

## 原理

![mvvm](https://user-gold-cdn.xitu.io/2018/4/10/162ad3d5be3e5105?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

在 `new Vue` 实例化之后会进行 **数据劫持** 与 **模板编译解析** 通过对根节点进行递归遍历添加 `getter` `setter` ，进行赋值操作触发setter中的监听函数通知 **Watch** 进行数据的变更。compile会进行解析将指令与模板中的变量替换成数据并初始化视图，在这个阶段添加 **订阅者** ，数据发生发生改变收到通知进行数据视图变更。**Watcher** 就是一个观察者模式是 Observer 与 compile中间的桥梁。



## 操作

PS: 因为是简单实现所以没有做过多的判断与限制

```js
class View{
 	constructor(opts = {}) {
        this.$el = document.querySelector(opts.el)
        this.$methods = opts.method
        this.$data = opts.data
        
        this._observer(this.$data)
        this._compile(this.$el)
    }
    
    _compile(el) {
        // 解析根节点中的所有元素
    }
    
    _observer(data) {
        // 对所有数据添加getter，setter
    }
}
```

在进行observer时是很关键的一步，需要对每一条数据做到劫持

```js
_observer(data) {
	Object.keys(data).forEach(k => {
        // 判断是不是自有属性
        if(data.hasOwnProperty) {
         	let val = data[k]
            if(typeof val === 'object') {
                this._observer(val)
            }
            
            Object.defineProperty(data, k, {
                configurable: true,
                enumerable: true,
                get() {
                    return val
                },
                set(newValue) {
                    if(newValue !== val) {
                        val = newValue
                    }
                }
            })
        }
    })
}
```

到此observer功能基本是完成了

```js
_compile(el) {
    // 这里应该将所有的节点无论是文本节点还是元素节点全部递归获取 这里因为没去实现{{}}绑定效果所以未做解析
	let nodes = el.children
    for(let i = 0, len = nodes.length; i < len; i++) {
        let node = nodes[i]
        if(node.children) {
            this._compile(node)
        }
        
        if(node.hasAttribute('v-bind')) {
            let attr = node.getAttribute('v-bind')
            // 这里获取到bind指令所对应的数据 怎么样将指令与视图进行关联 需要做一个指令与属性的映射
        }
    }
}
```

修改初始化函数，在数据监听中加入映射关系

```js
class View{
 	constructor(opts = {}) {
        this.$el = document.querySelector(opts.el)
        this.$methods = opts.method
        this.$data = opts.data
        
        // 指令与数据的映射
        this.map = {}
        
        this._observer(this.$data)
        this._compile(this.$el)
    }
    
    _observer(data) {
        Object.keys(data).forEach(k => {
            // 判断是不是自有属性
            if(data.hasOwnProperty) {
                let val = data[k]
                if(typeof val === 'object') {
                    this._observer(val)
                }
                // 添加映射关系 用于存储watch
				this.map[k] = {
                    directives: []
                }
                let _map = this.map[k]
                Object.defineProperty(data, k, {
                    configurable: true,
                    enumerable: true,
                    get() {
                        return val
                    },
                    set(newValue) {
                        if(newValue !== val) {
                            val = newValue
                            _map.directives.forEach(m => {
                                m.updata()
                            })
                        }
                    }
                })
            }
        })
    }
}

class Watch {
    constructor(node, attr, $data, exp) {
        this.el = node   	// dom
        this.attr = attr	// 属性
        this.data = $data	// 数据
        this.exp = exp		// key number  conut
        this.updata()		// 初始化视图
    }

    // 更新 发布
    updata() {
        // input对应value div对应innerHTML
        this.el[this.attr] = this.data[this.exp]
    }
}
```

现在将解析的结果与视图修改进行融合

```js
_compile(el) {
    // 这里应该将所有的节点无论是文本节点还是元素节点全部递归获取 这里因为没去实现{{}}绑定效果所以未做解析
	let nodes = el.children
    for(let i = 0, len = nodes.length; i < len; i++) {
        let node = nodes[i]
        if(node.children) {
            this._compile(node)
        }
        
        if(node.hasAttribute('v-bind')) {
            let attr = node.getAttribute('v-bind')
            this.map[attr].directives.push(new Watch(
                node,
                'innerHTML',
                this.$data,
                attr
            ))
        }
        
        let _this = this
        // v-model 触发input事件修改value
        if (node.hasAttribute('v-model') && node.tagName === 'INPUT') {
            let _this = this
            let attr = node.getAttribute('v-model')
            // let fn = (function(key) {
            // 	 _this.map[attr].directives.push(new Watch(
            //        node,
            //        'value',
            //        _this.$data,
            //        attr
            //    ))
            //    return function () {
            //        _this.$data[attr] = nodes[key].value
            //    }    
            // })(i)
            
            _this.map[attr].directives.push(new Watch(
                node,
                'value',
                _this.$data,
                attr
            ))
            let fn = function () {
                _this.$data[attr] = node.value
            }
            node.value = this.$data[attr]
            node.addEventListener('input', fn)
        }
        
        if(node.hasAttribute('v-click')) {
            let attr = node.getAttribute('v-click')
            let fn = this.$methods[attr].bind(this.$data)
            node.addEventListener('click', fn)
        }
    }
}
```

到此 `v-model` 功能我们就实现了可以尝试一下

```html
<div id='app'>
    <input v-model='number'>
    <p v-bind='number'></p>
</div>
<script>
new View({
    el: '#app',
    data: {
        number: 0
    }
})
</script>
```

实现这个MVVM的简单demo，对于理解Vue中的响应模式有了更加好的理解。总体来说Vue中的响应方式通过数据劫持，模板编译，观察者模式结合。Vue中的视图是异步更新。观察者模式恰好是对于异步执行的操作不去过多的注重过程。数据变更通知dom节点的渲染进行视图更新。

对Vue的使用也有了一段时间了，接下来会对Vue中其它知识点进行总结归纳，知识不在于学的多，更在于积累。共勉~