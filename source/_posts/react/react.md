---
title: react初识
tags: react
categories: React 探索与实践
abbrlink: 22935
date: 2019-07-27 00:12:04
---

## React 认识

通过学习不同的框架去感受各自的魅力以及优缺点更加的去丰富自己的知识开阔自己的思维。前端框架的出现从传统的前端过度到webApp模式。Vue让我学习到组件化、局部更新、数据驱动、数据传递、状态管理、虚拟DOM等等一系列东西。通过学习React，感受到Vue更加的符合开发者个人的习惯。React更加的灵活它的 `JSX` 与 `JavaScript` 表达式的结合创造了更多的可能。`JSX` 成就了React。React的思想也对其他的框架有着广泛的影响。



## JSX

React是什么？官网解释React是声明式，组件化的视图框架。React生态包括了 `React Router` 、`redux` `react-thunk`、`react-saga`、`react-redux` 等等。其中JSX是React中最为标志性，代表性的内容

```jsx
class Button extends Component {
    state = {
        text: '按钮'
    }
    render() {
        return <div className='btn' onClick={() => this.handleClick}>{ this.state.text }</div>
    }
}
```

使用JSX是为了将html与逻辑结合到一起，它其实是js的一个语法扩展，像是一个模板但是具有js的功能。编写一个react组件可以使用JSX也可以不使用。

```jsx
function Button () {
  return React.createElement(
    "div",
    { className: "box",onClick={alert('点击')} },
    "按钮"
  );
}
```

相比较肯定JSX的书写方式上更加简洁，实际上JSX最后编译还是按照第二种方式。类似Vue中最后都是通过render函数生成。还可以发现在JSX中可以使用表达式。这也就是说JSX具有js的功能（表达式）。可以在js任何地方使用JSX

```jsx
const Button = () => (<div className='btn'>按钮</div>)
ReactDOM.render(<Button/>, root)
```

这里需要说明一下react中组件名称需要使用驼峰命名，jsx在解析时才会将其识别成为组件。虽然w3c规范中也有`button` 元素。为了避免冲突，应当遵守规范，以及类名的书写使用 `className` 。事件的绑定也采用驼峰方式。会让刚接触人不太适应~

**不能写js语法在JSX中** 例如if...else 相反使用三木运算符表达式是可以的。因为JSX最终还是编译成createElement

没办法去执行js的语法，除了表达式，函数调用。因为在书写js代码的时候更多我们需要进行一个调试但是在jsx中没有办法很好进行调试，就可以将其抽成一个函数。这也是为什么JSX书写表达式与函数是更好的选择。也让JSX更加的灵活。`HOC`、`render prop` 也是基于此。因为也是初学所以很多关于JSX高深的地方还没有发现，以后进行补充。



## 组件

React的组件写法有Component，Functional Component。

```jsx
class Base extends Component {
    constructor(props) {
        super(props)
        this.state = {count: 1}
    }
    
    handleClick() {
        this.setState({
            count: this.state.count + 1
        })
    }
    
    render() {
        return <div onClick={this.handleClick}>{this.state.count}</div>
    }
}

// Functional Component
function Base(props) {
    return <div>{ this.props.count }</div>
}
```

有些组件具有状态，有些组件没有状态纯展示（无状态组件 or UI组件）如果一个组件没有过多的状态之做为容器进行展示，使用ES6的方式书写未免有些冗余。函数式组件只是少了继承Component，会更加的方便。

在React中存在一个组件叫做 `PureComponent` 继承自 `Component` 只是内部使用了 `shouldComponentUpdate` 功能与原来的没什么区别，但是会对属性状态进行渐层比较。

```jsx
class Pure extends Component {
    shouldComponentUpdate(nextProps, nextState) {
        const { state, props } = this
        return this.compare(nextState ,state) && this.compare(nextProps, props)
    }
    
    compare(a, b) {
        return a === b || Object.keys(a).every(k => a[k] === b[k]);
    }
}
```

`shouldComponentUpdate`  是一个生命周期函数，可以理解为每次组件状态属性改变都会去触发render函数，每次进行询问是 **否应该执行组件更新呢** 默认返回true更新，`PureComponent` 的作用就是会减少render函数一些没必要的渲染工作。



### 组件状态

定义组件的时候需要在初始化过程中声明state，react不推荐直接修改state而是使用setState更新  **setState并不都是异步更新**

侯策大佬说，React 控制的事件处理过程，setState 不会同步更新 this.state。而在 React 控制之外的情况， setState 会同步更新 this.state。

```jsx
class Demo extends Component {
    onClick() {
      this.setState({
        count: this.state.count + 1
      })
    }

    componentDidMount() {
      document.querySelectorAll('#btn-raw')
        .addEventListener('click', this.onClick)
    }

    render() {
      return (
        <React.Fragment>
          <button id="btn-raw">
            click out React
          </button>

          <button onClick={this.onClick}>
            click in React
          </button>
        </React.Fragment>
      )
    }
}
```

id 为 btn-raw 的 button 上绑定的事件，是在 componentDidMount 方法中通过 addEventListener 完成的，这是脱离于 React 事件之外的，因此它是同步更新的。反之，代码中第二个 button 所绑定的事件处理函数对应的 setState 是异步更新的。

这里可能会碰到一个问题

```jsx
class Demo extends Component {
    state: { count: 1 }
    
    componentDidMounted() {
		this.setState({
            count: this.state.count + 1
        })
        console.log(this.state.count) // 1
    }    
}
```

因为在React处理范围之内setState是异步的所以并不能及时获取到更新的值，这时候需要存储一下

```jsx
const count = this.state.count + 1
this.setState({ count })
console.log(count) // 2
```

另外setState第二个参数也可以写成函数，可以确保在更新之后可以获取到值

```jsx
this.setState({ count: this.state.count + 1 }, () => {
    console.log(this.state.count)
})
```

但是还会涉及到一个问题，因为是回调。不是很方便使用更新的值，使用promisify

```jsx
const promisifyState = (that, state) => (
  new Promise(resolve => {
    that.setState(state, () => {
      resolve()
    })
  })
)
// 这种做法看似很有意思 还是少用为好
```

react会对多次setState进行合并以提升性能

```jsx
state = {
    count: 1
}

componentDidMounted() {
  this.setState({count: this.state.count + 1})
  this.setState({count: this.state.count + 1})
  this.setState({count: this.state.count + 1})
  console.log(this.state.count) // 1
}
```

关于setState的用法具体可以去官网查看详细的教程



### 事件绑定

react中的事件是合成事件所有的事件都是通过docment代理实现，更好的做到事件的注册与同意销毁更加的节省了性能减少了复杂程度不会频繁的创建销毁事件

```jsx
function onClick(event) {
  console.log(event); // => nullified object.
  console.log(event.type); // => "click"
  const eventType = event.type; // => "click"
  
  // event.persist() 持久化合成事件  
  
  // 不能异步的访问事件对象
  setTimeout(function() {
    console.log(event); // => undefined
    console.log(eventType); // => "click"
  }, 0);

  // 不起作用，this.state.clickEvent 的值将会只包含 null
  this.setState({clickEvent: event});

  // 你仍然可以导出事件属性
  this.setState({eventType: event.type});
}
```

> 如果你想异步访问事件属性，你需在事件上调用 `event.persist()`，此方法会从池中移除合成事件，允许用户代码保留对事件的引用。



另外牵连出来的问题就是react中的事件不能阻止原生的事件冒泡

```jsx
componentDidMount() {
  document.addEventListener('click', () => {
    console.log('document click')
  })
}

handleClick = e => {
  console.log('div click')
  e.stopPropagation() // 无效的
  // e.nativeEvent.stopImmediatePropagation() 通过nativeEvent可以访问原生事件类似Vue中native
}

render() {
  return (
    <div onClick={this.handleClick}>
      click
    </div>
  )
}
```



### this问题

react中事件绑定其实是将事件做为回调传入的会经常碰到一个问题就是this的指向丢失，通常我们更加希望在组件中任何地方this的指向都是当前 `component instance` 

```jsx
class Demo extends Component {
    state = {
        count: 1
    }

	handleClick() {
        console.log(this.state.count) // undefined this是div对象
    }
    
    render() {
        return <div onClick={this.handleClick}></div>
    }
}
```

JSX中写事件处理函数，会将handleClick存起来，当document事件触发执行handleClick内部的this是找不到当前组件实例的，需要通过显示的绑定执行this或者使用箭头函数

```jsx
class Demo extends Component {
    constructor(props) {
        super(props)
        this.state = {
            count: 1
        }
        this.handleClick = this.handleClick.bind(this)
    }

	handleClick() {
        console.log(this.state.count) // undefined this是div对象
    }
    
    render() {
        // return <div onClick={this.handleClick.bind(this)}></div>
        // reutrn <div onClick={() => this.handleClick}></div>
        return <div onClick={this.handleClick}></div>
    }
}
```

其实react是完全可以将this指向组件实例，react应该是为了怕避免污染this原本的指向。这个问题是js的问题与react无关。实现以下这个场景

```js
‘use strict’
class Demo {
    constructor(name) {
        this.name = name
    }
    
    show() {
        console.log(this.name)
    }
}

const d = new Demo('react')
d.show() // react

const otherD = d.show
otherD() // 报错
```

如果修改一下

```js
‘use strict’
class Demo {
    constructor(name) {
        this.name = name
    }
    
    show() {
        console.log(this.name)
    }
}

const d = new Demo('react')
d.show = d.show.bind(d)
const otherD = d.show
otherD() // react
```

```js
‘use strict’
class Demo {
    constructor(name) {
        this.name = name
        this.show = this.show.bind(this)
    }
    
    show() {
        console.log(this.name)
    }
}

const d = new Demo('react')
const otherD = d.show
otherD() // react
```

所以react事件中的this问题其实是js的问题与它无关

```js
document.addEventListener('click', cb) 
```

`cb` 相当于在react注册的事件最终统一通过 `document` 代理 `cb` 的 `this` 指向就是当前的dom对象 简单理解一下 因为react是合成事件具体肯定要去查看源码了 如果react对此 `cb` 中 `this ` 指向进行修改也有点违背事件绑定调用的回调中this确实本该指向当前dom对象



## 总结

通过这段时间的学习对react有了一个基本的认识，也查了很多资料，看文档解释。但是这个过程是很有意思的，其实看了蛮多一次也说不完，重点在于细细品味。毕竟react生态中还是有很多内容的，多总结梳理，未完待续~本次就简单的总结一下几点

+ JSX
+ 组件
  + 状态
  + setState使用
  + 事件绑定
  + this指向

^_^~