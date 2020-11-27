---
title: es6
date: 2020-07-27 09:14:10
categories:
    - js
tags:
    - es6
---

# [es6的新特性](https://es6.ruanyifeng.com/)

常用的有：`let, const`命令， `解构赋值`，`模版字符串`，`Set, Map`

`var`变量可以在声明之前使用，值为 `undefined`

## let

`let`不存在变量提升，主要是为了减少运行时错误，防止在变量声明之前使用变量，相同作用域下，不允许重复声明同一个变量；（暂时性死区：一旦进入作用域，需要使用的变量已经声明，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量）

```javascript
// es5只有全局作用域和函数作用域
// 1. 内层变量可能覆盖外层变量
var tmp = new Date();
function f() {
    console.log(tmp);
    if (false) {
        var tmp = 'hello world';
    }
}
f(); // undefined

// 2. 循环计数器变量泄露为全局变量, 如下面例子：循环结束后, i没有消失，泄露成了全局变量
var s = 'hello';
for (var i = 0; i < s.length; i++) {
    console.log(s[i]);
}
console.log(i); //5
```

## const

`const` 声明常量，一旦变量声明，立即初始化，无法改变值，其它特性与`let`一样

## 块级作用域

`块级作用域`，必须有大括号，如果没有大括号，`javascript引擎`就认为不存在块级作用域

## 变量的结构赋值，结构不成功等于 undefined

```javascript
// 数组层级、下边位置对应
let [a, b, c] = [1,2,3]; // a = 1, b = 2, c = 3
let [foo, [[bar], baz]] = [1, [[2], 3]]; // foo = 1, bar = 2, baz = 3
let [head, ...tail] = [1, 2, 3, 4]; // head = 1, tail = [2,3,4]
let [x, y, ...z] = ['a']; // x = 'a', y = undefined, z = []

// 只要数据结构满足Interator接口的，都可以采用数组形式结构
let [x, y, z] = new Set(['a', 'b', 'c']); 

function* fibs() {
    let a = 0;
    let b = 1;
    while(true) {
    	yield a;
        [a, b] = [b, a + b];
  	}
}
// 支持默认值，判断是否有值，只要不是undefined就生效
let [foo = true] = []; // foo = true
let [x = 1] = [undefined]; // x = 1
let [x = 1] = [null]; // x = null

// 对象结构赋值，变量名与属性名一致自动匹配值
let { foo, bar }  = { foo: 'aaa', bar: 'bbb' }; // foo = aaa, bar = bbb
// 变量名与属性不一致，可以参照下面方式
let { first: f, last: l } = { first: 'hello', last: 'world' }; // f = hello, l = world, first 报错，first is not defined, first是匹配模式，f才是变量

// 字符串被转换成数组解构
let [x1, y1, ...z1] = 'ab'; // x1 = 'a', y1 = 'b', z1 = []
```

## 模版字符串

```javascript
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

