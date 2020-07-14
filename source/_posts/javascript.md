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

