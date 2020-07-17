---
title: javascript 
date: 2020-07-14 15:37:00
categories:
    - js
tags:
    - javascript
---

# call, apply, bind

相同点：

1. 用来改变函数this的指向
2. 第一个参数都是this要指向的对象（上下文）
3. 都可以利用后续参数传参
4. 三者的参数不限定是 string 类型，允许是各种类型，包括函数 、 object 等等

不同点：

​	bind 返回该函数； apply、 call 立即调用

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