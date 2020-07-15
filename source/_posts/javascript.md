---
title: javascript 
date: 2020-07-14 15:37:00
categories:
    - 前端面试
tags:
    - javascript
    - 面试
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



