---
title: RegExp
tags:
  - js
  - RegExp
categories: Javascript
thumbnail: /img/javascript/RegExp.jpg
abbrlink: 65388
date: 2018-02-12 15:22:34
---

> Regular Expression 正则表达式，又称正规表示式。使用单个字符串来描述，匹配一系列符合某个句法规则的字符串。正则表达式通常被用来检索、替换那些符合某个模式(规则)的文本。

<!-- more --> 

正则表达式的应用非常的广泛，比如在dos窗口下 *find* 命令

> dir find *.txt

找到当前目录下所有的 `.txt` 结尾的文件。再比如很多 *IDE* 工具中都有正则替换的功能。再比如替换日期格式，匹配请求头替换等等。正则让一些繁琐的处理方式变得简单。重温了一遍黑魔法正则表达式。

![ide](/img/javascript/ide.png)

# RegExp

JavaScript中正则表达式使用包含两个方面字符方法与正则方法。不同的方法调用方式是不同的。下面列举一下

字符方法中可以接受正则表达式的方法有

> split、match、replace、search

正则方法

> test、exec

标识符

> g（global）、i（ignore）、m（multiple lines）



## 元字符

元字符某些其实就是转义字符，正则表达式中常用的几个元字符

| 字符 | 含义             |
| ---- | ---------------- |
| \d   | 数字             |
| \D   | 非数字           |
| \w   | 数字字母下划线   |
| \W   | 非数字字母下划线 |
| \s   | 空格             |
| \S   | 非空格           |
| \b   | 单词边界         |
| \B   | 非单词边界       |
| ^    | 以xx开头         |
| $    | 以xx结尾         |

```js
var pattern = /\d/;
console.log(pattern.test("abc")); // false
console.log(pattern.test("abc1")); // true

var pattern = /\bis\b/;
console.log('this is a box'.replace(pattern, 'IS')); // this IS a box
```



## 字符集

字符集用来指定某一些符合特性的字符，用 `[]` 来表示可以是单个字符或者是一个范围 例如\[abc\] \[0-9\] 

```js
var pattern = /[0-9a-zA-Z_]/g;        // 与\w意思相同
console.log(pattern.test('*'));      // false
console.log('--abc--'.replace(pattern, 'X')); // --XXX--
```

取反 字符集中使用 `^` 代表非的意思

```js
var pattern = /[^abc]/g;
console.log('abc123'.replace(pattern, 'X')); // XXX123
```

js的正则中 `.` 也代表一个范围和上边的元字符一样代表一个范围 写成字符集可以是 `[^\r\n]` ，`\d` 可以写成 `[0-9]` 。



## 量词

用来判断字符重复出现

| 字符  | 描述                           |
| ----- | ------------------------------ |
| ?     | 出现零次或者一次(最多一次)     |
| +     | 出现一次或者多次(最少出现一次) |
| *     | 出现零次或者多次               |
| {n}   | 出现n次                        |
| {n,m} | 出现n到m次                     |
| {n,}  | 最少出现n次                    |

```js
var pattern = /\d{3}/;
var str = "123";
console.log(pattern.test(str)); // true
```



*贪婪模式*

在满足匹配条件的情况下尽可能的多的去匹配

```js
var pattern = /\d{2,5}/g;
var str = "123456";
console.log(str.replace(pattern, 'x')); // x6

var pattern = /\d{2,5}/;
var str = "123456";
console.log(str.replace(pattern, 'x')); // x6

var pattern = /\d{2,5}/;
var str = "123456";
console.log(str.match(pattern));  // ["12345", index: 0, input: "123456"]
```



*非贪婪模式*

在满足条件的情况下尽可能少的去匹配

```js
var pattern = /\d{2,5}?/g;
var str = "123456";
console.log(str.replace(pattern, 'x')); // xxx

var pattern = /\d{2,5}?/;
var str = "123456";
console.log(str.replace(pattern, 'x')); // x3456

var pattern = /\d{2,5}?/;
var str = "123456";
console.log(str.match(pattern));  // ["12", index: 0, input: "123456"]
```



## 子集

子集也是分组，在正则表达式中分组是一个重要的功能

```js
var pattern = /box{2}/;
var str = "boxx";
console.log(pattern，test(str)); // true  box{2}匹配的是x出现两次 不是box两次

var pattern = /(box){2}/;
var str = /boxbox/;
console.log(pattern.test(str)); // true
```

*捕获性分组*

```js
var str = "2018-2-13";
var pattern = /(2018)-(2)-(13)/;
console.log(str.match(pattern)); // ["2018-2-13", "2018", "2", "13", index: 0, input: "2018-2-13"]
console.log(pattern.exec(str));  // ["2018-2-13", "2018", "2", "13", index: 0, input: "2018-2-13"]
```

`match` 与 `exec` 返回一个数组，匹配成功，返回的数组第一项是匹配成功的字符串，其余项为 *分组项* 即 *子集* 



非捕获性分组*

```js
var pattern = /(?:a)(b)(c)/;
var str = "abc";
console.log(str.match(pattern)); // ["abc", "b", "c", index: 0, input: "abc"]
```

`?:` 这个分组将不会被返回。为了整体的匹配只返回需要的



*前瞻模式*

```js
var str = 'google';    
var pattern = /goo(?=gle)/;
console.log(patter.test(str));  // true
console.log(pattern.exec(str)); // ["goo", index: 0, input: "google"]
```

*goo* 后边必须跟的是 *gle* 。同样捕获返回分组内容，只会返回匹配成功的字符



*负向前瞻*

```js
var str = "ab123";
var pattern = /a(?![a-zA-Z])/g;
console.log(str.replace(pattern, "X")); // aX123

var str = "a123*7vv";
var pattern = /\w(?!\d)/g;
console.log(str.replace(pattern, 'X')); // a12X*XXX
```



