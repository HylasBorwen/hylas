---
title: DOM 节点操作
date: 2018-03-03 21:26:47
tags: DOM
categories: DOM
---

DOM的操作，包括节点的获取、创建节点属性、添加节点、设置节点标签属性、类名操作

### 节点的获取

- document.getElementById(id)　　　　 　　
- document.getElementsByName(name)　　　　　　
- document.getElementsByTagName(tag)　
- document.getElementsByClassName(class)
- document.querySelector(选择器)
- document.querySelectorAll(选择器)

最上边的三个方法无兼容问题，其余的获取方式都为 *IE9+* 这里说一下其它的一点小区别

表单中通过getElementsByName获取

```html
<form>
    <input type="text" name="txt" />
    <input type="password" name="pwd" />
</form>
<script>
	var txt = document.getElementsByName("txt")[0];
    var pwd = document.getElementsByName("pwd")[0];
</script>
```

*class* 与 *tag* 获取为动态获取，区别 querySelectorAll

```html
<style>
    .red{color: red;}
    .green{color: green;}
</style>
<div class="green">1</div>
<div class="green">2</div>
<div class="green">3</div>
<div class="green">4</div>
<script>
    // 1
	var divDoms = document.getElementsByClassName("green");
    for(var i=0;i<divDoms.length;i++){
        if(divDoms[i].className === 'green'){
            divDoms[i].className = 'red'
        }
    }
    
    // 2
    var divDoms = document.querySelectorAll(".green")
    for(var i=0;i<divDoms.length;i++){
        if(divDoms[i].className === 'green'){
            divDoms[i].className = 'red'
        }
    }
</script>
```

第一个只有1，3变色。第二个全部变色。querySelectorAll返回的是节点快照，在此获取也不会改变。然而通过class获取的获取的获取都随着节点的改变而改变。二者皆有兼容问题，探讨一下 getElementsByClassName兼容问题。

```js
function getClass(name){
    if(document.getElementsByClassName){
		return document.getElementsByClassName(name);
    }else{
    	var all = document.getElementsByTagName("*");
        var res = [];
        // 在所有的标签里边去筛选
        for(var i=0;i<all.length;i++){
            // 类名可以是单个 也可以是多个
            var clsName = all[i].className.split(' ');
            for(var j=0;j<clsName.length;j++){
                if(clsName[j] == name){
                    // all[i] 满足条件的
                    res.push(all[i]);
                };
            }
        };
        return res;
    }
};
```

```js
// 另一种思路
function getClass(name){
    var all = document.getElementsByTagName("*");
    return [].slice.call(all).filter(function(item){
        // 把每一项的类名 切开 只要有 name这一项 返回
        return item.className.split(' ').indexOf(name) != -1;
    });
};
```

顺便在说一下addClass，removeClass

```js
function unique(arr){
    var res = [];
    for(var i=0;i<arr.length;i++){
        for(var j=0;j<res.length;j++){
            if(arr[i] == res[j]){
                break;
            }
        }
        //
        if(j == res.length){
            res.push(arr[i]);
        }
    }
    return res;
}
function addClass(ele,cname){
    var cArr = cname.split(' ');
    // 1. 判断和原来是否相等 去重 保留一个数组
    // 2. 传递的和原来全都放到一起 直接去重 在拼接
    var newArr = [];
    var arr = ele.className.split(' ').concat(cArr);
    ele.className = unique(newArr).join(' ');
}
```

换一个思路

```js
function addClass(ele, name){
    var arr = name.split(' ').concat(ele.className.split(' '));
    ele.className = Array.from(new Set(arr)).join(' ');
}

// h5 api classList 局限在于只能添加一个 并且不会重复添加
function addClass(ele, name){
    name.split(' ').forEach(item=>{
        ele.classList.add(item);
    })
}
```

removeClass

```js
function removeClass(ele, name){
    // 判断原有的和 要删除的
    var arr = ele.className.split(' ');
    var na = name.split(' ');
    for(var i=arr.length;i>=0;i--){
        for(var j=0,naLen = na.length;j<naLen;j++){
            if(arr[i] === na[j]){
                arr.splice(i,1);
            }
        }
    };
    ele.className = arr.join(' ');
}
```

```js
// h5 api 删除只能删除一个
function removeClass(ele, name){
    var na = name.split(' ');
    if(na.length === 1){
        ele.classList.remove(name);
    }else{
        na.forEach(function(item){
            ele.classList.remove(item);
        })
    }
}
```

以上仅仅是做一个参考，希望大家可以学以致用。不同的思路。实际中课以使用，只是做的不健壮，如果想要更加的完善可以做一些判断。



### 创建节点,属性

- document.createElement(eName);　　
- document.createAttribute(attrName); 
- document.createTextNode(text);　



### 添加节点

- document.insertBefore(newNode,referenceNode);
- parentNode.appendChild(newNode);　
- parentNode.append(text/newNode, ...)

这里介绍一下 *append*

1. ParentNode.append() 允许你也追加  DOMString 对象，而 Node.appendChild() 只接受 Node 对象。
2. ParentNode.append() 没有返回值，而 Node.appendChild() 返回追加的 Node 对象。
3. ParentNode.append() 可以追加几个节点和字符串，而 Node.appendChild() 只能追加一个节点。

```js
// 添加文本
var cDiv = document.createElement("div");
cDiv.append('div element');

// 添加多个 节点与字符串
var cDiv = document.createElement("p");
var cSpan = document.createElement("span");
cDiv.append(cSpan, "span文本");
```



### 设置节点标签属性,类名

- hasAttribute(attr)
- getAttribute(attr)
- setAttribute(attr, value)
- removeAttribute(attr)
- dataset.attr
- classList.remove(name)
- classList.add(name)

attr 设置标签属性

```js
function attr(ele, value){
    if(typeof value == 'string'){
        return ele.getAttribute(value);
    }else if(typeof value == 'object'){
        for(var k in value){
            ele.setAttribute(k, value[k]);
        }
    }
}
```

removeAttr 删除标签属性

```js
function removeAttr(ele, value){
    if(typeof value == 'string'){
        if(ele.hasAttribute(value)){
            ele.removeAttribute(value);
        }
    }
}
```

设置data-attr属性

```js
function dataAttr(ele, key, value){
    var args = arguments;
    var len = args.length;
    switch(len){
        case 3:
            if(typeof value == 'string'){
        		ele.dataset[key] = value;
    		}
            break;
        case 2:
            if(typeof args[1] == 'object'){
                for(var k in args[1]){
                    ele.dataset[k] = args[1][k]
                }
    		}
            break;
        default:
            throw new Error('param error');
    }
}
```

