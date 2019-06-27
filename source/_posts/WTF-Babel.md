---
title: Babel 折腾
date: 2019-06-27 21:09:17
tags: Babel
categories: 大前端
thumbnail: https://raw.githubusercontent.com/babel/logo/master/babel.png
---

## Babel 

作为一名前端开发，每当我看到

+ babel-core
+ babel-polyfill
+ babel-plugin-transform-runtime
+ babel-xxx-xxx

WTF ? 双眼一抹黑.都是啥。因为最近要处理 **ie** 的兼容。所以对 `babel` 进行了一点学习在此记录。

环境

+ babel7
+ @babel/preset-env



### babel 是做什么用

随着js的发展es的版本也在不断的更新添加中，但是为了让低端环境能够执行我们所编写的代码，就需要通过 `babel` 进行编译转化到低版本 es5.以便能覆盖大多数的浏览器。

babel的转换形式是以 ”转化功能“ 和 “插件” 的形式出现的，也就是说如果我们 **没有配置** 任何的插件和功能，使用babel之后返回的代码其实和写的是没有区别的。

```js
const fn = () => { //... }
```

通过 `@babel/plugin-transform-arrow-functions` 插件转换之后

```js
var fn = function() { //... }
```

诸如此类的插件还有

+ @babel/plugin-transform-async-to-generator 
+ @babel/plugin-transform-for-of

等等~ 这些插件在安装 `npm install @babel/preset-env -D` 的时候就会安装在我们的本地，当然插件内部的转换机制其实就是 `AST` 语法树的转换过程了。（会编译原理的人真变态！）

知道了这些也就明白了，在项目中我们都会去配置一个叫做 `.babelrc` `babel.config.js` 的配置文件。用来转换我们的代码。

### @babel/preset-env

看以前 `vue-cli` 的脚手架生成的 package.json 可以看到一些诸如此类的 babel插件

+ babel-preset-es2015
+ babel-preset-stage-0
+ ...

es2015，2016代表的就是es的版本，stage0-3代表的是最新语法的在提案的哪个阶段。

babel升级到7之后，`@babel/preset-env` 是所有es的合集，官方也不推荐在使用以前的方式，这个包可以通过配置去自动添加 `polyfill` 

[@babel/preset-env options](https://www.babeljs.cn/docs/babel-preset-env#options)

```js
{
  "presets": [
    ["@babel/preset-env", {
      "useBuiltIns": "usage | entry | false",  // 三种参数
      "targets": ""，
      ”core“: "2 | 3"  						   // 根据core-js版本去选择
    }]
  ]
}
```

`core-js` 是一个标准库 `@babel/polyfill` 其实就是前者的一个wrapper 真正引入的还是 `core-js`

测试 `@babel/cli` 以下测试都用下文件

```js
// index.js
require('@babel/polyfill')

const foo = () => {
  console.log(1)
}

new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1)    
  }, 1000);
}).then(res => {
  console.log(res)
})

[1,2,3,4].findIndex(a => {
  return a === 2
})
```

首先通过配置 `preset` 看结果

```js
{
  "presets": [
    ["@babel/preset-env", {
      "useBuiltIns": "entry",
      "targets": "chrome 40"，
      ”corejs“: "3"
    }]
  ]
}
```

结果和原来的一样。会有一个提示

>  `@babel/polyfill` is deprecated. Please, use required parts of `core-js`
>   and `regenerator-runtime/runtime` separately

意思就是@babel-polyfill被废弃了 本身就是core-js 与 runtime的结合也就是说

```js
require('@babel/polyfill')
// 等同
require('core-js/stable')
require('regenerator-runtime/runtime')
```

再次测试

```js
"use strict";
require("core-js/modules/es.symbol");
require("core-js/modules/es.symbol.description");
require("core-js/modules/es.symbol.async-iterator");
// ...无数个包 会包含一个 require("core-js/modules/es.array.find-index");
var foo = function foo() {
  console.log(1);
};

new Promise(function (resolve, reject) {
  setTimeout(function () {
    resolve(1);
  }, 1000);
}).then(function (res) {
  console.log(1);
})

[(1, 2, 3, 4)].findIndex(function (a) {
  return a === 2;
});
```

好像没太看懂，接着来修改一下 `useBulitIns: 'usage'`

```js
"use strict";
require("core-js/modules/es.array.find-index");
require("core-js/modules/es.object.to-string");
require("core-js/modules/es.promise");
// 设置了 usage 就不需要去引入 自动去检测合适的polyfill
// require("core-js/stable");
// require("regenerator-runtime/runtime");

var foo = function foo() {
  console.log(1);
};

new Promise(function (resolve, reject) {
  setTimeout(function () {
    resolve(1);
  }, 1000);
}).then(function (res) {
  console.log(res);
})

[(1, 2, 3, 4)].findIndex(function (a) {
  return a === 2;
});
```

useBulitIns之前是测试版本现在看来还是没什么问题，两者的区别就是 `entry` 并不会根据我们所使用的去按需引入 *polyfill* 说直白点就是会根据环境去引入 *polyfill* 。当我们切换了 target为更高版本的时候会发现 `require("core-js/modules/es.array.find-index");` 就不会有了，因为chrome已经内置了这个方法。

设置 `useBulitIns: false` 只会进行语法转化，所以没有意义

```js
"use strict";
require("core-js/stable");
require("regenerator-runtime/runtime");

var foo = function foo() {
  console.log(1);
};

new Promise(function (resolve, reject) {
  setTimeout(function () {
    resolve(1);
  }, 1000);
}).then(function (res) {
  console.log(res);
})

[(1, 2, 3, 4)].findIndex(function (a) {
  return a === 2;
});
```



### @babel/runtime

runtime的 `polyfill` 作用是什么呢？通常我们使用的是 `@babel/plugin-transform-runtime` 还有一些其它语法的 *transform*  因为是依赖@babel/runtime 所以项目中使用会同时安装二者

> A plugin that enables the re-use of Babel's injected helper code to save on codesize.
>
> 重复使用Babel注入的代码（好像没 看太明白）

`transform-runtime` 可以帮我们转化诸如箭头函数，for-of等新的js语法形式。但是对于新增在数据类型上的方法新的api是没办法去做兼容的，需要我们引入 `core-js` `@babel/polyfill` 或者配合 `transform-runtime` 去做处理。官方的解释：

> NOTE: Instance methods such as `"foobar".includes("foo")` will not work since that would require modification of existing built-ins (you can use [`@babel/polyfill`](https://babeljs.io/docs/en/babel-polyfill) for that).

runtime还有一个重要的功能是当我们在构建自己的包和应用的时候创建一个环境避免污染全局的内置对象或原型对象。我们发布的包引入Promise polyfill 。其它开发者在使用的时候在本地也有一个写好的Promise。完蛋了，真真假假分不清。

[plugin-transform-runtime options](https://babeljs.io/docs/en/babel-plugin-transform-runtime#options)

测试

```js
{
  "presets": [
    "@babel/preset-env"
  ],
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": false,
        "helpers": true,
        "useESModules": false
      }
    ]
  ]
}
```

结果与 `useBulitIns: false` 的效果是一样的，修改 `corejs: 2 | 3` 

```js
"use strict";

var _interopRequireDefault = require("@babel/runtime-corejs3/helpers/interopRequireDefault");

var _setTimeout2 = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/set-timeout"));

var _promise = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/promise"));

var _findIndex = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/instance/find-index"));

var _context;

var foo = function foo() {
  console.log(1);
}; 

(0, _findIndex["default"])(_context = new _promise["default"](function (resolve, reject) {
  (0, _setTimeout2["default"])(function () {
    resolve(1);
  }, 1000);
}).then(function (res) {
  console.log(res);
})

[(1, 2, 3, 4)]).call(_context, function (a) {
  return a === 2;
});
```

可以看到对构建出来的代码都加上了单独的引用，就不会去污染环境了，但是有疑惑，不是说不会去支持原型polyfill。查看了官网之后发现babel7之后的runtime。

+ 自动的引入 `@babel/runtime/regenerator` 当使用 generator async的时候
+ 当必要的时候会引入core-js进行polyfill
+ 自动的移出内置的inline babel helpers使用 `@babel/runtime/helpers` 代替

**特别注意** ：runtime中设置的corejs 并不是 core-js  而是 `@babel/runtime-corejs3` 

*runtime大法好 ！~*



### 总结

babel7直接 runtime就完事了。

+ `@babel/preset-env` 使用 `usage` 需要安装core-js2,3 进行 polyfill自动引入，说起来vue-cli3已经 内置了2，不支持升级到3，期待的vue-cli4，babel也已经废弃了@babel/polyfill 所以无需在下载。`useBulitIns: entry` 根据大环境polyfill
+ `@babel/runtime` 如果在构建lib是必须要添加的，一方面帮助我们减少重复文件的生成，另一方面避免污染环境，更重要的是可以帮助我们去自动 polyfill

babel7在转换速度有了很大的提升。很大程度进行了整合真的很不错，对于很多分不清啥是啥的同学再也不用担心了。

还需要注意的一点 `babel-loader` 中也可以配置优先级会大于配置文件，所以最好单独配置

```js
export const preset = {
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": "> 5%",
        "useBuiltIns": "usage",
        "corejs": 3
      }
    ]
  ]
}
```

