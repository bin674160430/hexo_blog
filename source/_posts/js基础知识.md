---
title: javascript 基础知识
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

闭包可以帮助把一些不需要暴露在全局的变量封装成“私有变量”

```javascript
// 假设一个计算乘积的简单函数
var mult = function() {
    var a = 1;
    for (var i = 0, l = arguments.length; i < l; i++) {
        a = a*arguments[i];
    }
    return a;
}

// mult函数接受一些number类型的参数，并返回这些参数的乘积，现在我觉得对于那些相同的参数来说，每次都进行计算是一种浪费，可以加入缓存机制，并升级一下这个函数的性能
var mult = (function() {
    var cache = {};
    return function() {
        var args = Array.prototype.join.call(arguments, ',');
        if (args in cache) {
            return cache[args];
        }
        var a = 1;
        for (var i = 0, l = arguments.length; i < l; i++) {
            a = a * arguments[i];
        }
        return cache[args] = a;
    }
})();
```

提炼函数式代码重构中的一种常见技巧，如果在一个大函数中有一些代码能够独立出来，我们常常把这些代码块封装在独立的小函数里面。独立出来的小函数有助于代码复用，如果这些小函数有一个良好的命名，它们本身也起到了注释的作用。如果这些小函数不需要在程序的其他地方使用，最好是把它们用闭包封闭起来

```javascript
var mult = (function() {
    var cache = {};
    var calculate = function() { // 封闭calculate函数
        var a = 1;
        for (var i = 0, l = arguments.length; i < l; i++) {
            a = a * arguments[i];
        }
        return a;
    };
    
    return function() {
        var args = Array.prototype.join.call(arguments, ',');
        if (args in cache) {
            return cache[args];
        }
        return cache[args] = calculate.apply(null, arguments);
    }
})();
```

### 延续局部变量的寿命

`img`对象经常用于进行数据上报，如下所示:

```javascript
var report = function(src) {
    var img = new Image();
    img.src = src;
}

report('http://xxx');
//通过查询后台的记录得知，因为一些低版本的浏览器的实现存在bug，使用report函数进行数据上报会丢失30%左右的数据，也就是说report函数并不是每次都成功发起http请求；丢失数据的原因是img是report函数中的局部变量，当report函数执行完毕后，img局部变量随着被销毁，而这个时候可能还没来记得发出http请求，所以这次请求就会丢失掉


// 把img变量闭包封闭起来，解决请求丢失问题
var report = (function() {
    var imgs = [];
    return function(src) {
        var img = new Image();
        imgs.push(img);
        img.src = src;
    }
})();
```

### 闭包和面向对象设计

过程和数据的结合是形容面向对象中“对象”时常使用的表达。对象以方法的形式包含了过程，而闭包则是在过程中以环境的形式包含了数据，通常用面向对象思想能实现的功能，用闭包也能实现

```javascript
// 闭包的方式
var extent = function() {
    var value = 0;
    return {
        call: function() {
            value++;
            console.log(value);
        }
    }
};

var extent = extent();
extent.call(); // 1
extent.call(); // 2
extent.call(); // 3

// 面向对象
var extent = {
    value: 0,
    call: function() {
        this.value++;
        console.log(this.value);
    }
}
extent.call(); // 1
extent.call(); // 2
extent.call(); // 3

var Extent = function() {
    this.value = 0;
}
Extent.prototype.call = function() {
    this.value++;
    console.log(this.value);
}
var extent = new Extent();
extent.call(); // 1
extent.call(); // 2
extent.call(); // 3
```

### 用闭包实现命令模式

```html
<html>
    <body>
        <button id="execude">命令：打开lol客户端</button>
        <button id="undo">命令：关闭lol客户端</button>
    </body>
</html>
<script>
	var LOL = {
        open: function() {
            console.log('打开LOL');
        },
        close: function() {
            console.log('关闭LOL');
        }
    };
    
    var OpenLolCommand = function(receiver) {
        this.receiver = receiver;
    };
    
    OpenLolCommand.prototype.execude = function() {
        this.receiver.open();
    };
    
    OpenLolCommand.prototype.undo = function() {
        this.receiver.close();
    };
    
    var setCommand = function(command) {
        document.getElementById('execude').onclick = function() {
            command.execude(); // 打开LOL
        }
        document.getElementById('undo').onclick = function() {
            command.undo(); // 关闭LOL
        }
    };
    
    setCommand(new OpenLolCommand(LOL));
</script>
```

命令模式的意图是把请求封装为对象，从而分离请求的发起者和请求的接收者（执行者）之间的耦合关系，在命令执行之前，可以预先往命令对象中植入命令的接收者

但在`javascript`中，函数作为一等对象，本身就可以四处传递，用函数对象而不是普通对象来封装请求显得更加简单自然。在面向对象版本的命令模式中，预先植入的命令接收者被当成对象的属性保存起来；而在闭包版本的命令模式中，命令接受者会被封闭在闭包形成的环境中

```javascript
var LOL = {
    open: function() {
    	console.log('打开LOL');  
    },
    close: function() {
        console.log('关闭LOL');
    }
};

var createCommand = function(receiver) {
    
    var execute = function() {
        receiver.open();
    }
    
    var undo = function() {
        receiver.close();
    }
    
    return {
        execute: execute,
        undo: undo
    }
    
}

var setCommand = function(command) {
    document.getElementById('execute').onclick = function() {
        command.execute();
    };
    document.getElementById('undo').onclick = function() {
        command.undo();
    }
};

setCommand(createCommand(LOL));
```

# 闭包与内存管理

​	闭包是一个非常大的特性，但是内存泄露的问题一直在强调，所以要减少闭包的使用。这其实是一个误解，局部变量本来应该在函数退出的时候被解除引用，但如果局部变量被封闭在闭包形成的环境中，那么这个变量将一直生存下去，从这个意义上看，闭包的确会使一些数据无法被及时销毁。使用闭包的一部分原因是我们主动选择把一些变量封闭在闭包中，因为可能在以后还需要使用这些变量，把这些变量放在闭包中和放在全局作用域，对内存方面的影响是一致的，这里并不能说成内存泄露。如果在将来需要回收这些变量，可以手动把变量设置为`null`

​	根闭包和内存泄露有关系的地方是，使用闭包的同时比较容易形成循环引用，如果闭包的作用于保存一些`DOM`节点，这时候可能造成内存泄露，但这本身不是闭包的问题，也不是`javascript`的问题。
​	在IE浏览器中，由于`BOM`和`DOM`中的对象是使用`C++`以`DOM`对象的形式实现的，而`COM`对象的方式实现，而DOM对象的垃圾收集机制采用的是引用计数策略。在基于引用计数策略的垃圾回收机制中，如果两个对象之间形成了循环引用，那么这两个对象都无法被回收，但循环引用造成的内存泄露在本质上也不是闭包造成的。

​	同样，如果要解决循环引用带来的内存泄露问题，我们只需要把循环引用中的变量设为`null`即可。将变量设置为`null`意味着切断变量与它之前引用的值之间的连接，当垃圾收集器下次运行时，将会删除这些值并回收它们占用的内存。

# 高阶函数

高阶函数是指至少满足下列条件之一的函数

- 函数作为参数被传递
- 函数作为返回值输出

## 回调函数

```javascript
var getUserInfo = function(userId, callback) {
    $.ajax('http://xxx?' + userId, function(data) {
        if(typeof callback === 'function') {
            callback(data);
        }
    });
}

getUserInfo(13157, function(data) {
    console.log(data.userName);
});
// 回调函数的应用不仅只是在异步请求中，当一个函数不适合执行一些请求时，可以把这些请求封装成一个函数，并把它作为参数传递给另外一个函数，“委托”给另外一个函数来执行

var appendDiv = function(callback) {
    for(var i = 0; i < 100; i++) {
        var div = document.createElemnt('div');
        div.innerHTML = i;
        document.body.appendChild(div);
        if (typeof callback === 'function') {
            callback(div);
        }
    }
};

appendDiv(function(node) {
    node.style.display = 'none';
});
// 隐藏节点的请求实际上是由客户发起，但是客户并不知道节点什么时候会创建好，于是把隐藏节点的逻辑放在回调函数中，“委托”给appendDiv方法，appendDiv方法当然知道节点什么时候创建好，所以在节点创建好的时候，appendDiv会执行之前客户传入的回调函数


// Array.prototype.sort的目的是对数组元素进行排序，这是不变的部分，具体用什么规则排序，是可变的部分，把可变的部分分装在函数参数里，动态传入Array.prototype.sort，使Array.prototype.sort方法成为一个非常灵活的方法

// 从小到大, [1,3,4]
[1,4,3].sort(function(a, b) {
    return a - b;
});

// 从大到小, [4,3,1]
[1,4,3].sort(function(a, b) {
    return b - a;
});
```

## 函数作为返回值输出

相比把函数当做参数传递，函数当做返回值输出的应用场景更多，也更能体现函数式编程的巧妙。让函数继续返回一个可执行的函数，意味着运算过程是可延续的。

```javascript
// 判断一个数据是否是数组，在以往的实现中，如果基于鸭子模型的概念来判断，就是判断这个数据有没有length属性，有没有sort或者slice方法等。但是更好的方式是用Object.prototype.toString返回的字符串来计算

var isType = function(type) {
    return function(obj) {
        return Object.prototype.toString.call(obj) === '[object ' + type + ']';
    }
};

var isString = isType('String');
var isArray = isType('Array');
var isNumber = isType('Number');

console.log(isArray([1,2,3])); // true

// 还可以利用循环，来批量注册这些isType函数
var Type = {};

for(var i = 0, type; type = ['String', 'Array', 'Number'][i++]; ) {
    (function(type) {
        Type['is' + type] = function(obj) {
            return Object.prototype.toString.call(obj) === '[object' + type + ']';
        }
    })(type)
};

Type.isArray([]); // true
Type.isString(''); // true


// 单例模式demo
var getSingle = function(fn) {
    var ret;
    return function() {
        return ret || (ret = fn.apply(this, arguments));
    }
};

var getScript = getSingle(function() {
    return document.createElement('script');
});

var script1 = getScript();
var script2 = getScript();
console.log(script1 === script2); // true
```

# 高阶函数实现AOP

`AOP`（面向切面编程）的主要作用是把一些跟核心业务逻辑模块无关的功能抽取出来，这些业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。把这些功能抽离出来之后，再通过"动态织入"的方式参入业务逻辑模块中。这样做的好处首先是保持业务逻辑模块的纯净和高内聚性，其次是可以很方便地复用日志统计等功能模块。

在`javascript`中实现`AOP`，都是把一个函数"动态织入"到另外一个函数中，例如

```javascript
Function.prototype.before = function(beforefn) {
    var __self = this; // 保存原函数的引用
    return function() { // 保存包含了原函数和新函数的“代理”函数
        beforefn.apply(this, arguments); // 执行新函数，修正this
        return __self.apply(this, arguments); // 执行原函数
    }
};

Function.prototype.after = function(afterfn) {
    var __self = this;
    return function() {
        var ret = __selft.apply(this, arguments);
        afterfn.apply(this, arguments);
        return ret;
    }
};

var func = function() {
    console.log('func');
};

func = func.before(function() {
    console.log('before');
}).after(function() {
    console.log('after');
});

func();
// before
// func
// after
```

# 高阶函数的其它应用

## currying 函数柯里化，部分求值

一个`currying`函数首先会接收一些参数，该函数并不会立即求值，而是继续返回另外一个函数，刚才传入的参数在函数形成的闭包中被保存起来。等待函数被真正需要求值的时候，之前传入的所有参数都会一次性用于求值。

例如：编写一个计算每月开销的函数。

> 1. 记录每天花费多少钱
>
>    ```javascript
>    var monthlyCost = 0;
>    
>    var cost = function(money) {
>        monthlyCost += money;
>    };
>    
>    cost(100); // 第一天100
>    cost(200); // 第二天200
>    cost(300); // 第三天300
>    console.log(monthlyCost); //三天花费600
>    ```
>
> 2. 上面代码可以看到，每天结束后都会记录并计算到今天为止花掉的钱，但事实上并不需要关心每天花掉多少钱，而只想知道月底一共花费了多少钱，实际上只要在月底计算一次
>
>    ```javascript
>    var cost = (function() {
>        var args = [];
>        return function() {
>            if(arguments.length === 0) {
>                var money = 0;
>                for (var i = 0, l = args.length; i < l; i++) {
>                    money += args[i];
>                }
>                return money;
>            } else {
>                [].push.apply(args, arguments);
>            }
>        }
>    })();
>    
>    cost(100); // 未真正求值
>    cost(200); // 未真正求值
>    cost(300); // 未真正求值
>    cost(); // 求值: 600
>    ```
>
> 3. 再改进一下，编写一个通用的`currying`函数
>
>    ```javascript
>    // 接受并处理参数
>    var currying = function(fn) {
>        var args = [];
>        return function() {
>            if (arguments.length === 0) {
>                return fn.apply(this, args);
>            } else {
>                [].push.apply(args, arguments);
>                return arguments.callee;
>            }
>        }
>    }
>    
>    // 计算总额
>    var costFn = (function() {
>        var money = 0;
>        return function() {
>            for (var i = 0, l = arguments.length; i < l; i++) {
>                money += arguments[i];
>            }
>            return money;
>        }
>    })();
>    
>    var cost = currying(costFn); // 转换成currying函数
>    cost(100);
>    cost(200);
>    cost(300);
>    cost(); // 600
>    ```
>

## uncurrying

把泛化`this`的过程提取出来

```javascript
Function.prototype.uncurrying = function() {
    var self = this;
    return function() {
        var obj = Array.prototype.shilft.call(arguments);
        return self.apply(obj, arguments);
    }
}
```

