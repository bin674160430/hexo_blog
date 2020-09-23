---
title: javascript 设计模式
date: 2020-09-23 14:28:00
categories:
    - js
tags:
    - 设计模式
---

# 对象

javascript动态类型语言，一切都建立在`鸭子模型`的概念上

> 鸭子模型——如果它走起路来像鸭子，叫起来也是鸭子，那么它就是鸭子。

指导我们只关注对象的行为，而不是关注对象本身，加入合唱团的动物根本不用检查类型，只要保证有`duckSinging`方法，就能加入

```javascript
// 这里以一个合唱团的代码来分析解读一下动态语言的面向对象设计
var duck = {
    duckSinging: function() {
        console.log('嘎嘎嘎');
    }
}

var chicken = {
    duckSinging: function() {
        console.log('咯咯咯')
    }
}

var choir = []; // 合唱团

var joinChoir = function(animal) {
    if (animal && animal.duckSinging === 'function') {
        choir.push(animal);
        console.log('恭喜加入合唱团，' + choir.length);
    }
}

joinChoir(duck); // 恭喜加入合唱团， 1
joinChoir(chicken); // 恭喜加入合唱团， 2
```

# 多态

同一操作作用于不同的对象上面，可以产生不同的解释和不同的执行结果

> 我养了一只猫和一只狗，当向他们发出“叫”的命令后，猫会“喵喵喵”地叫，狗会“汪汪汪”地叫；这两只动物都会以自己的方式来发出叫声。
>
> 重点是：它们同样是动物，并且都会叫，各自发出不同的叫声。
>
> 结论：动物都会叫，这是不变的规律；叫声不同，是可变的。把不变的部分隔离出来，可变的部分封装起来，给予了扩展程序的能力，程序看起来是可生长的，也符合开放-封闭原则

```javascript
// 某一种动物能否发出叫声，取决于有没有sound方法，不取决于它是某种类型的对象，不存在任何程度上的“类型耦合”，javascript对象的多态性是与生俱来的
// 多态的好处是不必再询问对象是什么类型，只管调用该行为就行了，其它的一切多态机制会帮你安排妥当
var makeSound = function(animal) {
    animal.sound();
}

var Cat = function() {}
Cat.prototype.sound = function() {
    console.log('喵');
}

var Dog = function() {}

Dog.prototype.sound = function() {
    console.log('汪');
}

makeSound( new Cat() ); // 喵
makeSound( new Dog() ); // 汪
```

typescript实现多态

```typescript
public abstract class Animal {
    abstract sound(): void;
}

public class Cat extends Animal {
    public sound(): void {
        console.log('喵');
    }
}

public class Dog extends Animal {
    public sound(): void {
        console.log('汪');
    }
}

public class AnimalSound {
    public makeSound(Animal animal): void {
        animal.sound();
    }
}

AnimalSound animalSound = new AnimalSound();
Animal cat = new Cat();
Animal dog = new Dog();
animalSound.makeSound(cat); // 喵
animalSound.makeSound(dog); // 汪
```

# 封装

```javascript
var myObject = (function() {
    var __name = 'lan'; // 私有private变量
    return {
        getName: function() { // 公开public方法
            return __name;
        }
    }
})()

console.log(myObject.getName()) // lan
console.log(myObject.__name) // undefined
```

# this、call、apply

`call(object, ...arguments)`

`apply(object, [...arguments])`
call 、apply最常见的用于是改变函数内部的`this`指向

## 简单例子：`document.getElementById`这个方法名有点长，我们大概尝试用一个短的函数来代替它，如同`prototype.js`等一些框架所做的事情

```javascript
<div id="div1">我是一个 div</div>

var getId = document.getElementById;
getId('div');// 这段代码抛出一个异常，因为许多引擎的document.getElementById方法作为document对象的属性被调用时，方法内部的this确实是指向document的，这里用getId来引用之后就成了普通函数调用，this指向了window，而不是原来的document

// 可以利用apply把document当做this传入getId函数，帮助“修正”this
document.getElementById = (function(func) {
    return function() {
        return func.apply(document, arguments);
    }
})(document.getElementById);
var getId = document.getElementById;
var div = getId('div1');
console.log(div.id);// div1
```

## 借用其它对象的方法

```javascript
var A = function(name) {
    this.name = name;
};

var B = function() {
    A.apply(this, arguments);
}
B.prototype.getName = function() {
    return this.name;
}

var b = new B('lan');
console.log(b.getName()); // lan
```



## Function.prototype.bind

大部分浏览器都实现了内置的`Function.prototype.bind`，用来指定函数内部的`this`指向

```javascript
// 这是一个简化版的bind实现
Function.prototype.bind = function(context) {
    var self = this; //保存原函数
    return function() { // 返回一个新函数
        return self.apply(context, arguments); // 执行新函数的时候，会把之前传入的context，当作新函数内的this
    }
}

var obj = {
    name: 'lan'
}

var func = function() {
    alert(this.name)
}.bind(obj);

func();
```

```javascript
// 通常我们还会把它实现得稍微复杂一点
Function.prototype.bind = function() {
    var self = this, // 保存原函数
        context = [].shift.call(arguments), // 需要绑定的上下文
        args = [].slice.call(arguments); // 剩余的参数转成数组
    return function() {
        // 执行新的函数的时候，会把之前传入的context当做新函数内的this
        // 并且组合两次分别传入的参数，作为新函数的参数
        return self.apply(context, [].contact.call(args, [].slice.call(arguments)));
    }
};

var obj = {
    name: 'lan'
};

var func = function(a, b, c, d) {
    alert(this.name); // lan
    alert([a, b, c, d]); // [1,2,3,4]
}.bind(obj, 1, 2);

func(3, 4);
```



# 原型模式和基于原型继承的javascript对象系统

从设计模式的角度讲，原型模式是用于创建对象的一种模式，找到一个对象，然后通过克隆来创建一个一模一样的对象。

既然原型模式是通过克隆来创建对象的，那么如果需要一个跟某个对象一模一样的对象，就可以使用原型模式。

```javascript
// 英雄联盟中妮蔻W技能可以制作一个分身，当她在使用分身技能的时候，需要创建一些跟她一模一样的模型，包括当前的血量、等级等信息
// 原型模式的实现关键，是语言本身是否提供了clone方法，es5提供了Object.create来克隆对象

var Nikou = function() {
    this.blood = 200;
    this.level = 1;
}

var nikou = new Nikou();
nikou.blood = 600;
nikou.level = 6;

var cloneNikou = Object.create(nikou);
console.log(cloneNikou); // { blood: 600, level: 6 }

// 在不支持Object.create方法的浏览器中，则可以使用一下代码
Object.create = Object.create || function(obj) {
    var F = function() {};
    F.prototype = obj;
    return new F();
}
```

## 原型规则

- 所有的数据都是对象
- 要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它
- 对象会记住它的原型
- 如果对象无法响应某个请求，它会将这个请求委托给它的原型

`javascript`中的根对象是`Object.prototype`，是一个空对象，在`javascript`遇到的每个对象，实际上都是从`Object.prototype`对象克隆而来的, `Object.prototype`就是它们的原型。

对象会记住它的原型，`javascript`给对象提供了一个名为`__proto__`的隐藏属性，默认指向它的构造器的原型对象，`Constructor.prototype`。

```javascript
// new 运算的过程
var objectFactory = function() {
    var obj = new Object(), // 从Object.prototype上克隆一个空的对象
        Constructor = [].shift.call(arguments); // 取得外部传入的构造器
    
    obj.__proto__ = Constructor.prototype; // 指向正确的原型
    var ret = Constructor.apply(obj, arguments); // 借用外部传入的构造器给obj设置属性
    
    return typeof ret === 'object' ? ret : obj; // 确保构造器总是会返回一个对象
}
```

虽然`javascript`的对象最初都是由`Object.prototype`对象克隆而来的，但对象构造器的原型不仅限于`Object.prototype`上，而是可以动态指向其他对象，这样，当对象a需要借用对象b的能力时，可以有选择地把对象a的构造器的原型指向b，从而到达继承的效果

```javascript
// 原型继承方式
var obj = { name: 'lan' };

var A = function() {};
A.prototype = obj;

var a = new A();
console.log(a.name); // lan
```

# 闭包

## 变量的作用域

在函数中声明变量，如果该变量前面没有带上关键字`var`，这个变量就会成为全局变量，这当然是一种容易造成命名冲突的作法；另外一种情况是用`var`关键字在函数中声明变量，这时候的变量是局部变量，只有在该函数内部才能访问到这个变量

```javascript
var func = function() {
    var a = 1;
    alert(a); // 1
}

func();
alert(a); // Uncaught ReferenceError: a is not defined
```

变量的搜索是从内到外的，搜索的过程随着代码执行环境创建的作用域往外逐层搜索，一直搜索到全局对象为止，一下例子说明

```javascript
var a = 1;

var func1 = function() {
    var b = 2;
    var func2 = function() {
        var c = 3;
        console.log(b); // 2
        console.log(a); // 1
    }
    func2();
    console.log(c); // Uncaught ReferenceError: c is not defined
}

func1();
```

## 变量的生命周期

全局变量的生命周期是永久的，除非主动去销毁这个变量。

函数内`var`关键字声明的局部变量，当退出函数时，这些局部变量失去价值，随着函数的结束而被销毁。

```javascript
var func = function() {
    var a = 1; // 退出函数后局部变量a将被销毁
    console.log(a);
}
func();
```

```javascript
var func = function() {
	var a = 1;
    return function() {
        a++;
        console.log(a);
    }
}

var f = func();
f(); //2
f(); //3
f(); //4

// 当退出函数后，局部变量a没有消失，而是一直在某个地方存活着，这是因为当执行var f = func(), f返回了一个匿名函数的引用，它可以访问到func()被调用时产生的环境，而局部变量a一直处于这个环境里；既然局部变量所在的环境还能被外界访问，说明这个变量就有了不被销毁的理由，在这个地方产生了一个闭包结构，局部变量看起来被延续了。
```

下面介绍一个闭包的经典应用

```html
<html>
    <body>
        <div>1</div>
        <div>2</div>
        <div>3</div>
        <div>4</div>
        <div>5</div>
    </body>
</html>
<script>
	var nodes = document.getElementByTagName('div');
    for(var i = 0, len = nodes.length; i < len; i++) {
        nodes[i].onclick = function() {
            console.log(i);
        }
    }
    // 无论点击哪个div，最后都是输出5，因为div的onclick事件是异步触发的，当事件触发的时候，for早就结束，i值已经是5，所以顺着作用域从内往外找i的值总是5
    // 解决方法在闭包的帮助下，把i值封闭起来，当事件函数顺着作用域链从内往外找i，会先找到被封闭在闭包环境中的i
    for(var i = 0, len = nodes.length; i < len; i++) {
        (function(i) {
            nodes[i].onclick = function() {
                console.log(i);
            }
        })(i)
    }
</script>
```

```javascript
// 同样地编写如下一段代码：
var Type = {};

for(var i = 0, type; type = ['String', 'Array', 'Number'][i++];) {
    (function(type){
        Type['is' + type] = function(obj) {
            return Object.prototype.toString.call(obj) === '[object' + type + ']';
        }
    })(type)
}

Type.isArray([]); // true
Type.isString(''); // true
```

## 闭包的更多作用

### 变量封装