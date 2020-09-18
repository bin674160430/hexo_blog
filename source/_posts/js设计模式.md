---
title: javascript 设计模式
date: 2020-07-16 20:20:00
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
    var __name = 'sven'; // 私有private变量
    return {
        getName: function() { // 公开public方法
            return __name;
        }
    }
})()

console.log(myObject.getName()) // sven
console.log(myObject.__name) // undefined
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

虽然javascript的对象最初都是由Object.prototype对象克隆而来的，但对象构造器的原型不仅限于Object.prototype上，而是可以动态指向其他对象，这样，当对象a需要借用对象b的能力时，可以有选择地把对象a的构造器的原型指向b，从而到达继承的效果

```javascript
// 原型继承方式
var obj = { name: 'sven' };

var A = function() {};
A.prototype = obj;

var a = new A();
console.log(a.name); // sven
```

