---
title: javascript 
date: 2020-07-14 15:37:00
categories:
    - js
tags:
    - javascript
---

附属链接：[javascript基础](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/A_re-introduction_to_JavaScript)

# == 和 === 的区别

`===`为等同符，等号两边类型、值相同返回`true`，等号两边值类型不同或者值不同直接返回`false`

`==` 为等值符，等号两边类型不同将自动转换成相同类型后比较值，具体看下面表格

| 类型x        | 类型y     | 结果                |
| ------------ | --------- | ------------------- |
| null         | undefined | true                |
| 数字         | 字符串    | x == toNumber(y)    |
| 布尔值       | 任何类型  | toNumber(x) == y    |
| 字符串或数字 | 对象      | x == toPrimitive(y) |

```javascript
// 等号两边是boolean, string, number, 优先转换数字进行比较
null == undefined // true
30 == '30' // true, 类型不同，一个数值，一个字符串，字符串转数值再比较
true == '1' // true, 任意值是boolean，需要转数值后再比较 true -> 1, false -> +0
true == '42' // false, 布尔宽松原则， 这里 true -> 1, '42'转换还是42, 条件语句中避免使用 == Boolean
'A' == true // false, true转成数字1, 'A'转成数字NaN, NaN == 1 -> false
new Object() == '[object Object]' // true，对象与数值、字符串，把对象转成基础类型值再比较，利用toString或者valueOf, js 核心内置类，会尝试valueOf先于toString
```

## 强制类型转换

**toString**

```javascript
// ToString 负责处理非字符串到字符串的强制类型转换
null; // "null"
undefined; // "undefined"
true; // "true"
var a = 1.07 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000;
a.toString(); //  "1.07e21"，极小、极大的数字使用指数形式
// 如果对象有自己的toString()方法，字符串化时会调用该方法并使用其返回值
Object.prototype.toString(); // "[object Object]"

// 不安全的JSON值, undefined, function, symbol和包括循环引用（对象之间的相互引用）
// JSON.stringify() 在对象遇到undefined, funtion, symbol会自动忽略，在数组中会返回null
JSON.stringify(undefined); // undefined
JSON.stringify(function() {}); // undefined
JSON.stringify([1, undefined, function() {}, 4]); // [1,null,null,4]
var b = {
    a: 1,
    c: 0,
    d: function() {}
};
// 对象中定义了toJSON方法，JSON字符串化会优先调用该方法，然后用它的返回值来进行序列化
b.toJSON = function() {
    return { b: this.a }
};
JSON.stringify(b); // "{"b":1}"
```

**toNumber**

```javascript
// 非数字值当做数字来使用，对字符串处理失败时返回NaN, 对以0开头的十六进制数并不按十六进制处理，而是十进制
true; // 1
false; // +0
undefined; // NaN
null; // +0
// 对象（包括数组）会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其转换为数字
// 为了将值转换为相应的基本类型值，抽象操作toPrimitive会首先检查是否有valueOf()方法
// 如果有且返回基本类型值，就使用该值进行强制类型转换，如果没有就使用toString()的返回值来进行强制类型转换
// 如果valueOf()和toString()均不返回基本类型值，会产生TypeError错误
var a = {
    valueOf: function() {
        return "1";
    }
};
var b = {
    toString: function() {
        return "2";
    }
};
var c = [1,2];
c.toString = function() {
    return this.join(""); // "12"
};
Number(a); // 1
Number(b); // 2
Number(c); // 12
Number([]); // 0
Number(["abc"]); // NaN
```

**toBoolean**

```javascript
// 1: true, 0: false
// javascript中布尔值和数字不一样，值分为以下两类
// 以下都是假值，布尔强制类型转换结果为false; 假值列表以外的值都是真值
undefined;
null;
false;
+0; -0; NaN;
"";
```

## toPrimitive

​	如果对象的`valueOf()`的结果是基本数据类型，返回原始值；如果`toString()`返回基本数据类型，就返回该值；其它情况返回错误。优先调用`valueOf()`。

## +运算符会将操作数显示强制转为数字

```javascript
+"3.14"; // 3.14
// + 运算符的期中一个操作数是字符串，则执行字符串拼接，否则执行数字加法
[1,2] + [3,4]; // "1,23,4", 数组valueOf()操作无法得到基本类型值，转而调用toString(), 变成了 "1,2"+"3,4"
// - * / 会强制类型转换为数字，只适用于数组
[3] - [1]; // 2
```



# 代码块

```javascript
// {} 出现在+运算符表达式中，被当做一个值（空对象）来处理
// []会被强制类型转换为"", 而{}被强制类型转换为"[object Object]"
[] + {}; // "[object Object]"

// {} 被当做一个独立的空代码块（不执行任何操作），代码块结尾不需要分号
// + [] 将 [] 显示类型转换为0
{} + []; // 0
```

# 运算符优先级 *

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)

| 运算符                             | 描述                                         |
| ---------------------------------- | -------------------------------------------- |
| . [ ] ()                           | 字段访问、数组下标以及函数调用               |
| ++ – - ~ ！ typeof new void delete | 一元运算符、返回数据类型、对象创建、未定义值 |
| * / %                              | 乘法、除法、取模                             |
| + - +                              | 加法、减法、字符串连接                       |
| << >> >>>                          | 移位                                         |
| < <= > >=                          | 小于、小于等于、大于、大于等于               |
| == != === !==                      | 等于、不等于、恒等、不恒等                   |
| &                                  | 按位与                                       |
| ^                                  | 按位异或                                     |
| \|                                 | 按位或                                       |
| &&                                 | 逻辑与                                       |
| \|\|                               | 逻辑或                                       |
| ?:                                 | 条件(三元运算)                               |
| =                                  | 赋值                                         |
| ,                                  | 多重求值                                     |

```javascript
var a = 42; 
var b = "foo"; 
var c = false; 
var d = a && b || c ? c || b ? a : c && b : a; 
d; // 42
// 掌握了优先级和关联等相关知识之后，就能够根据组合规则将上面的代码分解如下：
((a && b) || c) ? ((c || b) ? a : (c && b)) : a

/*
    (1) (a && b) 结果为 "foo" 。 
    (2) "foo" || c 结果为 "foo" 。 
    (3) 第一个 ? 中，"foo" 为真值。 
    (4) (c || b) 结果为 "foo" 。 
    (5) 第二个 ? 中，"foo" 为真值。 
    (6) a 的值为 42 。 因此，最后结果为 42
*/
```



# try...finally

​	`finally`中的代码总是会在`try`之后执行，如果有`catch`的话则在`catch`之后执行，可以将finally中的代码看作是一个回调函数，无论出现什么情况，最后一定会被调用。

```javascript
// 如果try中有return语句，调用该函数并得到返回值的代码在finally前后执行的问题？
// try执行完毕，接着执行finally，最后test()执行完毕，console.log()显示返回值
function test() {
    try {
        return 'test';
    }
    finally {
        console.log('finally');
    }
    console.log('never runs');
}
console.log(test());
// finally
// test

// try 中 throw执行顺序同上
function test2() {
    try {
        throw 2;
    }
    finally {
        console.log('finally');
    }
    console.log('never runs');
}
console.log(test2());
// finally
// Uncaught Exception: 2

// 如果finally中抛出异常，函数就会在finally终止，如果此前try中已经有return值，该值会被丢弃
function test3() {
    try {
        return 3;
    }
    finally {
        throw 'Oops!'
    }
    console.log('never runs');
}
console.log(test3());
// Uncaught Exception: Oops!

// 循环continue break也是一样
for (var i = 0; i < 2; i++) {
    try {
        continue;
    }
    finally {
        console.log(i);
    }
}
// 0 1

// finally 中的 return会覆盖 try 和 catch 中的 return 返回值
```



# 原型链

`prototype`指向构造函数，只有函数才有的属性，用来给子类继承

`__proto__`指向构建该对象的`prototype`，每个对象除了null外都会有的属性

每个构造函数都有一个原型链对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。如果让原型对象等于另一个类型的实例，这个时候原型对象将包含一个指向另外一个原型的指针，相应地，另一个原型中也包含着一个指向另一个构造函数的指针，假如另一个原型又是另一个类的实例，那么上述关系依然成立，照这样层层递进，就构成了实例与原型的链条。

{% image 原型链.png  %}

```javascript
function func() {} // func的prototype对象的另一个属性是constructor，这个属性指向函数func本身
func.prototype // {constructor: ƒ} constructor: ƒ func() __proto__: Object
func === func.prototype.constructor // true
Object.prototype === func.prototype.__proto__ // true
Object.prototype.isPrototypeOf(func) // true
func instanceof Object // true

var a = new func();
a.__proto__ === func.prototype // true, a.__proto__ === func.prototype === constructor
```

# call, apply, bind

相同点：

1. 用来改变函数this的指向
2. 第一个参数都是this要指向的对象（上下文）
3. 都可以利用后续参数传参
4. 三者的参数不限定是 string 类型，允许是各种类型，包括函数 、 object 等等

不同点：

​	bind 返回该函数； apply、 call 立即调用；call 参数和apply参数不同，apply是数组

`call(object, ...arguments)`

`apply(object, [...arguments])`

`bind(object, ...arguments)`

```javascript
var name = '小王', age = 17;
var obj = {
    name: '小张',
    objAge: this.age,
    myFun: function(fn, t) {
        console.log(this.name + ' 年龄 ' + this.age, ' 来自 ' + fn + '去往' + t)
    }
}
var db = {
    name: '德玛',
    age: 12
}
obj.myFun.call(db,'成都','上海')；　　　　 // 德玛 年龄 99  来自 成都去往上海
obj.myFun.apply(db,['成都','上海']);      // 德玛 年龄 99  来自 成都去往上海  
obj.myFun.bind(db,'成都','上海')();       // 德玛 年龄 99  来自 成都去往上海
obj.myFun.bind(db,['成都','上海'])();　　 // 德玛 年龄 99  来自 成都, 上海去往 undefined

```

# 箭头函数

根据外层作用域决定 `this`， 不绑定`this`，相当于普通变量，在作用域中逐级寻找，无法通过`bind, call, apply`来直接修改 `this`

```javascript
// 可以通过改变作用域中的this来改变箭头函数的this
function closure() { () => {...} }
closure.bind(another)();
// demo               
var name = 'window'
var person1 = {
  name: 'person1',
  show1: function () {
    console.log(this.name)
  },
  show2: () => console.log(this.name),
  show3: function () {
    return function () {
      console.log(this.name)
    }
  },
  show4: function () {
    return () => console.log(this.name)
  }
}
var person2 = { name: 'person2' }

person1.show1() // person1，隐式绑定，this指向调用者 person1 
person1.show1.call(person2) // person2，显式绑定，this指向 person2

person1.show2() // window，箭头函数绑定，this指向外层作用域，即全局作用域
person1.show2.call(person2) // window，箭头函数绑定，this指向外层作用域，即全局作用域

person1.show3()() // window，默认绑定，这是一个高阶函数，调用者是window
				  // 类似于`var func = person1.show3()` 执行`func()`
person1.show3().call(person2) // person2，显式绑定，this指向 person2
person1.show3.call(person2)() // window，默认绑定，调用者是window

person1.show4()() // person1，箭头函数绑定，this指向外层作用域，即person1函数作用域
person1.show4().call(person2) // person1，箭头函数绑定，
							  // this指向外层作用域，即person1函数作用域
person1.show4.call(person2)() // person2
```

# [常用继承方案](/blog/2020/07/16/继承/)

> 原文地址：https://juejin.im/post/5bcb2e295188255c55472db0

## 寄生组合式继承 *

结合借用构造函数传递参数和寄生模式实现继承

```javascript
function inheritPrototype(subType, superType){
  var prototype = Object.create(superType.prototype); // 创建对象，创建父类原型的一个副本
  prototype.constructor = subType;                    // 增强对象，弥补因重写原型而失去的默认的constructor 属性
  subType.prototype = prototype;                      // 指定对象，将新创建的对象赋值给子类的原型
}

// 父类初始化实例属性和原型属性
function SuperType(name){
  this.name = name;
  this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){
  alert(this.name);
};

// 借用构造函数传递增强子类实例属性（支持传参和避免篡改）
function SubType(name, age){
  SuperType.call(this, name);
  this.age = age;
}

// 将父类原型指向子类
inheritPrototype(SubType, SuperType);

// 新增子类原型属性
SubType.prototype.sayAge = function(){
  alert(this.age);
}

var instance1 = new SubType("xyc", 23);
var instance2 = new SubType("lxy", 23);

instance1.colors.push("2"); // ["red", "blue", "green", "2"]
instance1.colors.push("3"); // ["red", "blue", "green", "3"]
```

{% image extends6.png 示例图 %}

这个例子的高效率体现在它只调用了一次`SuperType` 构造函数，并且因此避免了在`SubType.prototype` 上创建不必要的、多余的属性。于此同时，原型链还能保持不变；因此，还能够正常使用`instanceof` 和`isPrototypeOf()`

**这是最成熟的方法，也是现在库实现的方法**

# sessionStorage, localStorage, cookie区别

1. `cookie` 一般由服务器生成，在设置的有效期内生效；浏览器不能保存超过300个cookie，单个服务器不能超过20个，每个cookie不能超过4k；会在请求时发送到服务器，作为会话标识
2. `sessionStorage`在窗口关闭前有效，`localStorage`长期有效，大小不能超过5M

# ajax的原理

Asynchronous JavaScript and XML (异步的JavaScript 和 XML)，与服务器端交换数据并更新网页，IE(5-6)浏览器使用 ActiveXObject , 其他浏览器使用 XMLHttpRequest 的 JavaScript内置对象

```javascript
// 1. 创建XMLHttpRequest对象
var xmlHttp = new XMLHttpRequest();
// 2. 设置和服务端请求方式
var url = "http://localhost:8080/JsLearning3/getAjax";
xmlHttp.open("POST", url, true);
// 3. 注册回调信息
xmlHttp.onreadystatechange = function() {
    // readyState: 0-初始化， 1-服务端连接建立， 2-请求已接收， 3-请求处理中， 4-请求已完成
    if (xmlHttp.readyState == 4) {
        if (xmlHttp.status == 200) {
            console.log(xmlHttp.responseText)
        } else {
            console.error('request error');
        }
    }
}
// 4. 设置请求数据，发起请求
xmlHttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded;charset=UTF-8");
xmlHttp.send('data');
```



# 什么是严格模式与合理模式

严格模式：`use strict`以浏览器支持的最高标准运行，消除`javascript`一些不合理、不严谨之处，减少一些怪异行为

合理/普通模式：页面以宽松向下兼容的方式显示，模拟老式浏览器行为

# 递归

函数自己调用自己，如下例子计算1加到n的的结果

```javascript
// 编写递归技巧
// 1. 寻找临界值，即无需计算，获得的值，这里1就是临界值
// 2. 找这一次与上一次的关系
// 3. 假设当前函数可以使用，调用自身计算上一次
function sum (n) {
  if (n === 1) {
    return 1;
  }
  return sum (n - 1) + n;
}

// 有一堆桃子不知数目，猴子第一天吃掉一半，又多吃一个；第二天照样吃掉一半另加一个；
// 天天这样，到第n（n <= 10）天，猴子发现只剩下一个桃子，问这堆桃子原来有多少只
// peach(10) / 2 - 1 = peach(9); 第10天的桃子的一半减去一个等于第九天剩下的桃子数
// peach(10) = (peach(9) + 1) * 2;
// peach(n) = (peach(n - 1) + 1) * 2
function peach (n) {
  if (n === 1) {
    return 1;
  }
  return (peach(n - 1) + 1) * 2;
}
```



主要考察对`内存管理`和`垃圾回收`机制的了解，公司会明文禁止使用递归，很危险，因为函数运行的时候会开启一块内存，递归运行需要大量开内存，虽然现在计算机的速度非常快，假设递归调用到达一亿次，将会在瞬间开启一大块内存，并且在瞬间释放掉，计算机将支撑不住；假设值发生故障，将会开启一串内存，最后可能程序就蓝屏或者死机了！

# 已知的限制

- 字符串常量中允许的最大字符数（并非只是针对字符串值）
- 可以作为参数传递到函数中的数据大小（也称为栈大小，以字节为单位）
- 函数声明中的参数个数
- 未经优化的调用栈（例如递归）的最大层数，即函数调用链的最大长度
- `JavaScript`程序以阻塞方式在浏览器中运行的最长时间（秒）
- 变量名的最大长度

