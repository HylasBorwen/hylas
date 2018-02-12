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

元字符也可以称为转义字符，正则表达式中常用的几个元字符

| 字符 | 含义             |
| ---- | ---------------- |
| \d   | 数字             |
| \D   | 非数字           |
| \w   | 数字字母下划线   |
| \W   | 非数字字母下划线 |
| \s   | 空格             |
| \S   | 非空格           |
| \b   | 单词边界         |

```js
var pattern = /\d/;
console.log(pattern.test("abc")); // false
console.log(pattern.test("abc1")); // true

var pattern = /\bis\b/;
console.log('this is a box'.replace(pattern, 'IS')); // this IS a box
```



## 字符集

字符集用来指定某一些符合特性的字符，用 `[]` 来表示

```js
var pattern = /[0-9a-zA-Z_]/;        // 与\w意思相同
console.log(pattern.test('*'));      // false
console.log('--abc--'.replace(pattern, 'X')); // true
```

