---
title: es6
date: 2020-07-27 09:14:10
categories:
    - js
tags:
    - es6
---

# [es6的新特性](https://es6.ruanyifeng.com/)

常用的有：`let, const`块级作用域， `解构赋值`，`模版字符串`，`Set, Map`，`箭头函数`，`函数的默认参数值`

`var`变量可以在声明之前使用，值为 `undefined`

# let

`let`不存在变量提升，主要是为了减少运行时错误，防止在变量声明之前使用变量，相同作用域下，不允许重复声明同一个变量；（暂时性死区：一旦进入作用域，需要使用的变量已经声明，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量）

```javascript
// es5只有全局作用域和函数作用域

// 循环计数器变量泄露为全局变量, 如下面例子：循环结束后, i没有消失，泄露成了全局变量
for (var i = 0; i < 10; i++) {
    setTimeout(function() {
        console.log(i); // 10，只会输出10
    }, 0);
}
console.log(i); // 10

// 换成下面方式， j在let声明之后，生成一块作用域，只在该作用域下有效
for (let j = 0; j < 10; j++) {
    setTimeout(function() {
        console.log(j); // 0,1,2,3,4,5,6,7,8,9
    }, 0);
}
console.log(j); // ReferenceError: j is not defined
```

# const

`const` 声明常量，一旦变量声明，立即初始化，无法改变值，其它特性与`let`一样

# 块级作用域

`块级作用域`，必须有大括号，如果没有大括号，`javascript引擎`就认为不存在块级作用域

# 变量的解构赋值，结构不成功等于 undefined

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
// 默认值赋值，判断是否有值，只要不是undefined就生效
let [foo = true] = []; // foo = true
let [x = 1] = [undefined]; // x = 1
let [x = 1] = [null]; // x = null
let [x = 1] = [2]; // x = 2
let [x = 1] = [0]; // x = 0

// 对象结构赋值，变量名与属性名一致自动匹配值
let { foo, bar }  = { foo: 'aaa', bar: 'bbb' }; // foo = aaa, bar = bbb
// 变量名与属性不一致，可以参照下面方式
let { first: f, last: l } = { first: 'hello', last: 'world' }; // f = hello, l = world, first 报错，first is not defined, first是匹配模式，f才是变量

// 字符串被转换成数组解构
let [x1, y1, ...z1] = 'ab'; // x1 = 'a', y1 = 'b', z1 = []
```

# 模版字符串

```javascript
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

# 箭头函数

```javascript
function circleArea(r) {
    return 3.14 * r * r;
}

// => return
const circleArea = r => 3.14 * r * r;
```



# 声明展开和剩余参数

# export导出

​	没有用`export`的一切都在模块作用域内部保持私有

```javascript
export function foo() {};
export var awesome = 42;
var bar = [1,2,3];
export { bar };
export { bar as rab }; // 重命名导出
export default bar; // 默认导出
export { foo as default };
// 每个模块只能有一个默认导出
export default function foo() {}
export function baz() {}

// 再次导出摸个模块的导出
export { foo, bar } from "baz";
export { foo as FOO, bar as BAR } from "baz";
export * from "baz";
```

# import导入

```javascript
import { foo } from "foo";
import { foo as theFooFunc } from "foo";
import foo from "foo";
import { default as foo } from "foo";
import * as foo from "foo";
```

```javascript
// foo 导出下面两个方法
export default function foo() {}
export function baz() {}

// 引入foo
import foo, { baz } from "foo";
```

# 模块加载器

## 在模块之外加载模块

​	直接与模块加载器交互的一个用法是非模块需要加载一个模块的情况；出于性能考虑，需要尽可能避免动态加载，因为这将会妨碍JavaScript引擎格局静态分析提前获取代码的能力。

```javascript
// Reflect.Loader.import(): Promise
Reflect.Loader.import("foo")
.then(function(foo) {
    foo.bar();
});
```

## 自定义加载

​	通过配置、重定义来自定义其行为

# 类class

​	表示一个块，定义一个函数原型的成员，class本身不是一个真正的实体，而是一个包裹着其它像函数和属性的具体实体并把它们组合到一起的元概念。

```javascript
class Foo {
    constructor(a, b) {
        this.x = a;
        this.x = b;
    }
    gimmeXY {
        return this.x * this.y;
    }
}

function Foo(a, b) {
    this.x = a;
    this.y = b;
}
Foo.prototype.gimmeXY = function() {
    return this.x * this.y;
}
```

如上图所示：`class Foo`和`function Foo`很像，区别却很大：

- `Foo.call(obj)`不能工作，`class Foo`的调用必须通过`new`来实现
- `function Foo`是提升的，而`class Foo`不是，extends...语句指定了一个不能被提升的表达式
- 全局作用域中的`class Foo`创建了这个作用域的一个词法标识符`Foo`，但是和`function Foo`不一样，并没有创建一个同名的全局对象属性

## new.target

​	构造器访问静态属性

```javascript
class Foo {
    constructor() {
        console.log("Foo:", new.target.name);
    }
}
class Bar extends Foo {
    constructor() {
        super();
        console.log('Bar:', new.target.name);
    }
    baz() {
        console.log('baz:', new.target);
    }
}
var a = new Foo(); // Foo: Foo
var b = new Bar(); // Foo: Bar
				  // Bar: Bar
b.baz(); // baz:undefined
```

# 集合

## TypedArray

​	待类型的数组是未来了使用类数组语义结构化访问二进制数据。

```javascript
// ArrayBuffer()构建位集合
var buf = new ArrayBuffer(32);
buf.byteLength; // 32, buf是一个二进制buffer，长32字节（256）位，预先初始化全部为0
// 一个buffer本身除了查看byteLength属性外，并不支持任何其他交互

// 在数组buffer之上，可以放置一个视图（以类型数组的形式存在）
var arr = new Uint16Array(buf);
arr.length; // 16, arr在这个256位buf上映射一个16位无符号整型的类型数组
```

## 大小端（Endianness）

​	多字节数字（16位无符号整型）中的低字节（8位）位于这个数字字节表示中的右侧还是左侧。
​	一个十进制数字`3085`，需要用16位来表示它，如果只是用一个16位数字容器，不管大小端配置如何，它会被表示未二进制`0000110000001101`（十六进制 `0c0d`）；如果用两个8位数组表示数字`3085`，大小端设置就会明显影响它在内存中的存储表示：

- `0000110000001101` / `0c0d`（大端）
- `0000110100001100` / `0d0c` ( 小端）

​    如果接收一个来自小端系统表示`3085`的位序列`0000110100001100`，而在打断系统中为其建立视图，得到的值将是`3340`（十进制）或者`0d0c`（十六进制）

​	**根据MDN，有一个款速检测JavaScript大小端的方法**

```javascript
// 多数浏览器会得到true
var littleEndian = (function() {
    var buffer = new ArrayBuffer(2);
    new DataView(buffer).setInt16(0, 256, true);
    return new Int16Array(buffer)[0] === 256;
})();
```

## 多视图

单个buffer可以关联多个视图

```javascript
var buf = new ArrayBuffer(2);
var view8 = new Uint8Array(buf);
var view16 = new Uint16Array(buf);

view16[0] = 3058;
view8[0]; // 13
view8[1]; // 12

view8[0].toString(16); // d
view8[1].toString(16); // c

// 交换（就像大小端变换）
var tmp = view8[0];
view8[0] = view8[1];
view8[1] = tmp;

view16[0]; // 3340
```

## 带类数组构造器

- `new Uint16Array(buffer, [offset, [length]])`
- `[constructor\](length)`在一个新的穿度为length字节的buffer上创建一个新的视图
- `[constructor\][typeArr]`创建一个新的视图和buffer，把typeArr视图的内容复制进去
- `[constructor\](object)`创建一个新的视图和buffer，并在类数组或对象obj上迭代复制其内容
- `Int8Array`（8位有符号整型），`Uint8Array`（8位无符号整型），`Uint8ClampedArray`（8位无符号整型，每个值会被强制设置为`0-255`内）
- `Int16Array`（16位有符号整型），`Uint16Array`（16位无符号整型）
- `Int32Array`（32位有符号整型），`Uint32Array`（32位无符号整型）
- `Float32Array`（32位浮点数，`IEEE-754`）
- `Float64Array`（64位浮点数，`IEEE-754`）

`TypedArray`中的元素是限制在声明的位数大小中，如果视图给一个`Uint8Array`的某个元素赋值为大于8位的值，将会被折回来适应其位宽

## Set

​	类似数组，成员的值是唯一的，**没有重复**的值。接受数组（或者具有`iterable`接口的数据）作为参数初始化。方法和属性如下：

- `add(value): Set`添加某个值，返回Set结构本身
- `delete(value): boolean`删除某个值，返回一个`boolean`，表示删除是否成功
- `has(value): boolean`该值是否为Set的成员
- `clear()`清除所有成员
- `size` Set成员总数
- `keys()`返回键名遍历器，`Set`没有键名，只有键值，`keys`和`values`是一样的
- `values()`返回键值遍历器
- `entries()`返回键值对遍历器
- `forEach((value, key) => {})`回调遍历每个成员

```javascript
const set = new Set([1,1,2,3]);
[...set]; // [1,2,3]
set.size; // 3
for (let item of set.keys()) {
    console.log(item); // 1 2 3
}

for (let item of set.entries()) {
    console.log(item); // [1, 1] [2, 2] [3, 3]
}

// 去除数组重复成员
[...new Set([1,2,3,3,4])]; // [1,2,3,4]
// 去除字符串重复字符
[...new Set('abababc')].join(''); // abc

// 向Set加入值规则，不会发生类型转换，5和"5"是两个不同值，NaN等于自身
```

## WeakSet

​	`WeakSet`结构与`Set`类似，也是不重复的集合，成员只能是对象，不能是其他类型的值；`WeakSet`中的对象都是弱引用，即垃圾回收机制不考虑`WeakSet`对该对象的引用（如果其他对象都不再引用该对象，垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存于`WeakSet`中）。
​	因为垃圾回收机制依赖引用计数，如果一个值的引用次数不为`0`，垃圾回收机制就不会释放这块内存；直到结束使用该值之后，有时候会忘记取消引用，导致内存无法释放，进而可能会引发内存泄露。`WeakSet`里面的引用，都不计入垃圾回收机制，所以不存在这个问题。所以，`WeakSet`适合临时存放一组对象，以及存放跟对象绑定的信息，只要该对象在外部消失，在`WeakSet`的引用将会自动消失。
​	由于以上特点，`WeakSet`的成员不适合引用，会随时小时，而且`WeakSet`内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后成员个数不同，导致结果不可预测，所以`ES6`规定，`WeakSet`不可遍历。WeakSet只有`add(value)`、`delete(value)`，`has(value)`三个方法可以用。
​	**WeakSet的一个用处——存储DOM节点，而不用担心这些节点从文档移除引发的内存泄露问题。**

## Map

​	键值对集合（Hash结构），类似对象，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当做键，对键的处理：`0`和`-0`，`NaN`和`NaN`都会当做相同；`undefined`和`null`是两个不同的。方法和属性如下：

- `size`*Map*结果的成员总数
- `set(key, value): Map`设置键值对并返回整个*Map*结构
- `get(key)`读取键值，找不到`key`返回`undefined`
- `has(key): boolean`返回布尔值，表示键是否在当前*Map*中
- `delete(key): boolean` 删除某个键，成功返回`true`，失败`false`
- `clear()`清空所有成员
- `keys()`返回键名遍历器
- `values()`返回键值遍历器
- `entries()`返回所有成员遍历器
- `forEach()`遍历所有成员

```javascript
const m = new Map();
const o = { p: 'p' };
m.set(o, 'content');
m.get(o); // content
m.has(o); // true
m.delete(o); // true
m.has(o); // false

const map = new Map([
    ['name', '张三'],
    ['gender', '男']
]);
map
  .set(1, 'a')
  .set(1, 'b'); // 对同一个键赋值，后面的值将覆盖前面的值
map.size; // 3
for (let key of map.keys()) {
    console.log(key); // name gender 1
}
for (let value of map.values()) {
    console.log(value); // 张三 男 b
}
for (let item of map.entries()) {
    console.log(item[0], item[1]);
    // name 张三
    // gender 男
    // 1 b
}

for (let [key, value] of map.entries()) {
    console.log(key, value);
    // name 张三
    // gender 男
    // 1 b
}
// 等同于 map.entries()
for (let [key, value] of map) {}
```

## WeakMap

​	`WeakMap`和`Map`结构类似，用于生成键值对的集合；WeakMap只接受对象作为键名（`null`除外），键名引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内，处理机制和`WeakSet`相似，当键名引用的对象被清除（该对象占用的内存会被垃圾回收机制释放），对应的`WeakMap`记录会被自动移除。所以，WeakMap的键所在引用的对象，可能会在将来消失，有助于防止内存泄露。
​	WeakMap只有`get()`、`set()`、`has()`、`delete()`四个方法可以用。

```javascript
// 应用场景一、DOM节点作为键名使用，一旦DOM删除，键值自动消失，不存在内存泄露
const myWeakmap = new WeakMap();
maWeakmap.set(
	document.getElementById('logo'),
    { timesClicked: 0 }
);
document.getElementById('logo').addEventListener('click', function() {
  let logoData = myWeakmap.get(document.getElementById('logo'));
  logoData.timesClicked++;
}, false);


// 应用场景二、部署私有属性，实例消失，内部属性_counter, _action随之消失
const _counter = new WeakMap();
const _action = new WeakMap();

class Countdown {
    constructor(counter, action) {
        _counter.set(this, counter);
        _action.set(this, action);
    }
    dec() {
        let counter = _counter.get(this);
        if (counter < 1) return;
        counter--;
        _counter.set(this, counter);
        if (counter === 0) {
            _action.get(this)();
        }
    }
}

const c = new Countdown(2, () => console.log('done'));
c.dec();
c.dec();
```

# Array数组

## Array.of()

​	`Array()`构造器如果只传入一个数字，将构造一个空数组，length属性为该数字；`Array.of()`取代`Array()`成为数组推荐函数构造器，不会对单个数字参数特殊处理

```javascript
var a = Array(3);
a.length; // 3
a[0]; // undefined

var b = Array.of(3);
b.length; // 1
b[0]; // 3
```

## Array.from()

​	`JavaScript`中的“类数组对象”指一个有`length`属性，且大于等于`0`的对象，需要把它转换为真正的数组，以便应用`Array.prototype`的方法。
​	`Array.from()`检查第一个参数是否为`iterable`，如果是的话，就使用迭代器来产生值并“复制”进入返回的数组，因为真正的数组有一个这些值之上的迭代器，所以自动使用该迭代器；如果把类数组独享作为第一个参数传给`Array.from()`，它的行为方式和`slice()`没有参数或者apply()是一样的，就是简单地按照数字命名的属性从`0`开始知道`length`值在这些值上循环。

```javascript
// 类数组对象
var arrLike = {
    length: 3,
    0: 'foo',
    1: 'bar'
};
// 常见的是使用slice来复制产生一个真正的数组
var arr = Array.prototype.slice.call(arrLike);
var arr2 = Array.from(arrLike);

// 避免空槽位
var a = Array(4); // 4个空槽位
var b = Array.apply(null, { length: 4 }); // 4个undefined值
var c = Array.from({ length: 4 }); // 4个undefined值

// 第二个参数映射，第三个参数可选，如果设置，该参数作为第二个参数传入的回调指定this绑定，否则，this将会是undefined
Array.from({ length: 4, 2: "foo" }, function mapper(val, idx) {
    if (typeof val == 'string') {
        return val.toUpperCase();
    } else {
        return idx;
    }
});
// [0, 1, "FOO", 3]
```

## 数组子类型

```javascript
class MyArray extends Array {}
MyArray.from([1,2]) instanceof MyArray; // true
// of、from都是访问它们的构造器来构造数组
// 使用基类Array.of()得到的是Array实例, MyArray.of()得到MyArray实例
Array.from(
	MyArray.from([1,2])
) instanceof MyArray; // false

// @@species设置内置类定义，只适合slice()这样的原型方法，of、from不会使用
class MxArray extends Array {
    static get [Symbol.species]() { return Array; }
}
var x = new MxArray(1,2,3);
x.slice(1) instanceof MxArray; // false
x.slice(1) instanceof Array; // true
MxArray.from(x) instanceof MxArray; // true
MxArray.of([2,3]) instanceof MxArray; // true
```

## copyWithin(target, start[, end])

​	从一个数组中复制一部分到同一个数组的另一个位置，覆盖这个文字原来所有的值。参数target（要复制到的索引），start（开始复制的源索引，包括在内）以及可选的end（复制结束的不包含索引）；如果任何一个参数是负数，就被当做是相对数组结束的相对值。

```javascript
[1,2,3,4,5].copyWithin(3, 0); // [1,2,3,1,2]
[1,2,3,4,5].copyWithin(3, 0, 1); // [1,2,3,1,5]
[1,2,3,4,5].copyWithin(0, -2); // [4,5,3,4,5]
[1,2,3,4,5].copyWithin(0, -2, -1); // [4,2,3,4,5]
```

## fill(value[, start, end])

​	用指定值完全（或部分）填充已存在的数组

```javascript
var a = Array(4).fill(undefined); // [undefined, undefined, undefined, undefined]
[null, null, null, null].fill(42, 1,3); // [null, 42, 42, null]
```

## 查找

```javascript
var points = [
    { x: 10, y: 10 },
    { x: 20, y: 20 },
    { x: 30, y: 30 }
];
points.find(point => point.x === 10); // { x: 10, y: 10 }， 找到返回该对象，找不到返回undefined
points.findIndex(point => point.x === 20); // 1, 找到返回所在的索引，找不到返回-1
```

## 迭代器集合

​	只要提供了同样的迭代器方法`entries()`、`values()`、`keys()`，就是一个集合。

# Object

## Object.is()

​	`Object.is()`调用底层`SameValue`算法，`SameValue`算法基本上和`===`严格相等比较算法一样，如果需要识别`NaN`或者`-0`，需要使用`Object.is()`。

```javascript
var x = NaN, y = 0, z = -0;
x === x; // false
y === z; // true

Object.is(x, x); // true
Object.is(y, z); // false
```

## Object.getOwnPropertySymbols()

​	`Symbol`很可能会成为对象最常用的特殊（元）属性，所以引入`Object.getOwnPropertySymbols()`直接从对象上取得所有符号属性。

```javascript
var o = {
    foo: 42,
    [Symbol('bar')]: 'bar',
    baz: true
};
Object.getOwnPropertySymbols(o); // [Symbol(bar)]
```

## Object.setPrototypeOf()

​	设置对象的`[[Prototype]]`用于行为委托

```javascript
var o1 = {
    foo() { console.log('foo'); }
};
var o2 = {};
Object.setPrototypeOf(o2, o1);
// 委托给o1.foo()
o2.foo(); // foo

var o3 = Object.setPrototypeOf({}, o1);
o3.foo(); // foo
```

## Object.assign(target, ...args)

​	args所有源对象属性都将复制到target目标，对于每个源来说，它的可枚举和自己拥有的（不是“继承来的”）键值、包括符号都会通过简单=赋值被赋值到目标target，并返回该目标。

# Number

## 静态属性辅助数字常量

- `EPSILON`任意两个值之间的最小差`2^-52`
- `MAX_SAFE_INTEGER`数字值无歧义“安全”表达的最大整数`2^53-1`
- `MIN_SAFE_INTEGER`数字值无歧义“安全”表达的最小整数`-(2^53-1)`或`(-2)^53+1`

## Number.isNaN()

​	标准全局`isNaN()`对任何非数字都会返回`true`，`Number.isNaN()`针对`NaN`返回`true`。

## Number.isFinite()

​	“非无限的”数字，标准全局`isFinite()`会对参数进行强类型转换，`Number.isFinite()`不会进行类型转换。

## Number.isInteger()

​	`JavaScript`的数字永远都是浮点数`（IEE-754）`，确定数字是否为“整型”的概念并不是检查其类型，`4、4.、4.0000`之前并没有区别，都会被当做“整型”并且从`Number.isInteger()`中返回`true`。

## Number.isSafeInteger()

​	检查一个值为整数并且在Number.MIN_SAFE_INTEGER~Number.MAX_SAFE_INTEGER范围之内。

# 元编程

​	利用语言本身的内省能力使代码的其余部分更具描述性、表达性和灵活性。
​	**注意：不要编写过于机灵的代码，让未来的代码维护者难以理解。**

## 函数名称

​	默认情况下，`name`属性不可写，但可配置，可使用`Object.defineProperty()`手动修改。

```javascript
var abc = function() {};
abc.name; // abc

(function() {}); // name:
(function *() {}); // name:
window.foo = function() {}; // name:

class Awesome {
    constructor() {} // name: Awesome
    funny() {} // name: funny
}

var c = class Awesome {}; // name: Awesome

var o = {
    foo() {}, // name: foo
    *bar() {}, // name: bar
    baz: () => {}, // name: baz
    bam: function() {}, // name: bam
    get qux() {}, // name get qux
    set fuz() {}, // name set fuz
    ['b' + 'iz']: function() {}, // name: biz
    [Symbol('buz')]: function() {}, // name: [buz]
};

var x = o.foo.bind(o); // name: bound foo
(function(){}).bind(o); // name: bound

export default function() {} // name: default

var y = new Function(); // name: anonymous

var GeneratorFunction = function *() {}.__proto__.constructor;
var z = new GeneratorFunction(); // name: anonymous
```

## 元属性

​	元属性以属性访问的形式提供特殊的其他方法无法获取的元信息。

```javascript
class Parent {
    constructor() {
        // 关键字new用作属性防伪上下文，在构造器调用，形成一个虚拟上下文，使得new.target指向调用new的目标构造器
        if (new.target === Parent) {
            console.log('Parent instantiated');
        } else {
            console.log('A child instantiated');
        }
    }
}
class Child extends Parent {}

var a = new Parent(); // Parent is instantiated
var b = new Child(); // A child instantiated
```

## 公开符号

- `Symobl.iterator ` `@@iterator`表示对象上的一个专门属性，语言机制自动在该属性上寻找一个构造一个迭代器来消耗对象的值

  ```javascript
  var arr = [4,5,6,7,8,9];
  for (var v of arr) {
      console.log(v); // 4 5 6 7 8 9
  }
  // 定义只在奇数索引产生值的迭代器
  arr[Symobl.iterator] = function *() {
      var idx = 1;
      do {
          yield this[idx];
      } while ((idx += 2) < this.length);
  };
  for (var v of arr) {
      console.log(v); // 5 7 9
  }
  ```

- `Symbol.toStringTag`与`Symbol.hasInstance`最常见的一个元编程任务，在一个值上进行内省找出它是什么种类，通常用于确定适合执行哪种运算，最常用的内省技术是`toString()`和`instanceof`

  ```javascript
  function Foo() {}
  var a = new Foo();
  a.toString(); // [object object]
  a instanceof Foo; // true
  
  // 操控 toString, instanceof 行为特性
  function Foo(greeting) {
      this.greeting = greeting;
  }
  Foo.prototype[Symbol.toStringTag] = 'Foo'; // @@toStringTag符号指定了字符串化时使用的字符串值
  // @@hasInstance符号是构造器函数上的一个方法，接受实例对象得到值，通过返回true、false指示该值是否可以被认为是一个实例
  // 必须使用object.defineProperty()，因为Function.prototype上的@@hasInstance是writable: false(不可写)
  Object.defineProperty(Foo, Symbol.hasInstance, {
      value: function(inst) {
          return inst.greeting == 'hello';
      }
  });
  
  var a = new Foo('aa');
  var b = new Foo('bb');
  b[Symbol.toStringTag] = 'cool';
  
  a.toString(); // [object Foo]
  String(b); // [object cool]
  
  a instanceof Foo; // true
  b instanceof Foo; // false
  ```

- `Symobl.species` `@@species`符号控制生成新实例时，类的内置方法使用哪一个构造器。

  ```javascript
  class Cool {
      // 把@@species推迟到子类
      static get [Symbol.species]() { return this; }
      // 如果需要定义生成新实例的方法，使用new this.constructor[Symbol.species]()模式元编程
      again() {
          return new this.constructor[Symbol.species]();
      }
  }
  
  class Func extends Cool {}
  
  class Awesome extends Cool {
      // 强行指定@@species为父构造器
      static get [Symbol.species]() { return Cool; }
  }
  
  var a = new Func(),
      b = new Awesome(),
      c = a.again(),
      d = b.again();
  
  c instanceof Fun; // true
  d instanceof Awesome; // false
  d instanceof Cool; // true
  ```

- `Symbol.toPrimitive`对象为了某个操作（如==或者+）必须被强制转换为一个原生类型值

  ```javascript
  var arr = [1,2,3,4,5];
  arr + 10; // 1,2,3,4,510
  
  arr[Symbol.toPrimitive] = function(hint) {
      // 加法+运算没有提示（default），乘法（number），string提示（string）
      if (hint == 'default' || hint == 'number') {
          return this.reduce(function(acc, curr) {
              return acc + durr;
          }, 0);
      }
  };
  arr + 10; // 25
  ```

- `Symbol.isConcatSpreadable` `@@isConcatSpreadable`对象的布尔属性，用来指示如果把它传给一个数组的`concat()`是否将其展开

  ```javascript
  var a = [1,2,3],
      b = [4,5,6];
  b[Symbol.isConcatSpreadable] = false;
  [].concat(a, b); // [1,2,3,[4,5,6]]
  ```

# 代理 Proxy

​	“封装”一个普通对象从而创建一个特殊的**代理对象**，可以在代理对象上注册处理函数，除了把操作转发给原始目标、被封装对象之外，还有执行额外的逻辑。函数如下：

- `get()` 通过`[[Get]]`，在代理上访问一个属性（`Reflect.get()`, `.`属性运算符、`[..]`属性运算符）
- `set()` 通过`[[Set]]`，在代理上设置一个属性值（`Reflect.set()`、赋值运算符`=`、目标为对象属性的解构赋值）
- `deleteProerty()` 通过`[[Delete]]`，从代理对象上删除一个属性（`Reflect.deleteProperty()`、`delete`）
- `apply()`通过`[[Call]]`，将代理作为普通函数、方法调用（`Reflect.apply()`、`call()`、`apply()`、`()`调用运算符）
- `construct()` 通过`[[Construct]]`，将代理作为构造函数调用（`Reflect.construct()`、`new`）
- `getOwnPropertyDescriptor()` 通过`[[GetOwnProperty]]`，从代理中提取一个属性描述符（`Object.getOwnPropertyDescriptor()`、`Reflect.getOwnPropertyDescriptor()`）
- `defineProperty()` 通过`[[DefineOwnProerty]]`，在代理上设置一个属性描述符（`Object.defineProerty()`、`Reflect.defineProperty()`）
- `getPrototypeOf()` 通过`[[GetPrototypeOf]]`，得到代理的`[[Prototype]]`（`Object.getPrototypeOf()`、`Reflect.getPrototypeOf()`、`__proto__`、`Object.isPrototypeOf()`、`instanceof`）
- `setPrototypeOf()` 通过`[[SetPrototypeOf]]`，设置代理的`[[Prototype]]`（`Object.setPrototypeOf()`、`Reflect.setPrototypeOf()`、`__proto__`）
- `preventExtensions()` 通过`[[PreventExtensions]]`，使得代理变成不可扩展的（`Object.preventExtensions`、`Reflect.preventExtensions()`）
- `isExtensible()` 通过`[[IsExtensible]]`，检测代理是否可扩展（`Object.isExtensible()`、`Reflect.isExtensible()`）
- `ownKeys()` 通过`[[OwnPropertyKeys]]`，提取代理自己的属性、符号属性（`Object.keys()`、`Object.getOwnPropertyNames()`、`Object.getOwnSymbolProperties()`、`Reflect.ownKeys()`、`JSON.stringify()`）
- `enumerate()` 通过`[[Enumerate]]`，取得代理拥有的和“继承来的”可枚举属性的迭代器（`Reflect.enumerate()`、`for..in`）
- `has()` 通过`[[HasProperty]]`，检查代理是否拥有或者“继承了”某个属性（`Reflect.has()`、`Object.hasOwnProerty()`、`"prop" in obj`）

```javascript
var obj = { a: 1 },
    handlers = {
        get(target, key, context) {
            // target === obj
            // context === pobj
            console.log('accessing: ', key);
            // 执行相应的元编程任务
            return Reflect.get(target, key, context);
        }
    },
    pobj = new Proxy(obj, handlers);

obj.a; // 1
pobj.a; // accessing: a
	    // 1
```

## 代理的局限性

​	`typeof`、`+`、`==`、`===`底层基本元素是无法拦截的

## 可取消代理

​	`Proxy.revocable(target, handler)`返回一个有两个属性`proxy`、`revoke`的对象, `revoke()`取消代理，任何对它的访问都会抛出`TypeError`

```javascript
var obj = { a: 1 },
    handlers = {
        get(target, key, context) {
            console.log('accessing: ', key);
            return target[key];
        }
    },
    { proxy: pobj, revoke: prevoke } = Proxy.revocable(obj, handlers);
pobj.a; // accessing: a
	    // 1
prevoke();
pobj.a; // TypeError
```

## 代理前后

```javascript
// 代理在前设计
var obj = {
        a: 1,
        foo() {
            console.log("a: ", this.a);
        }
    },
    handlers = {
        get(target, key, context) {
            if (Reflect.has(target, key)) {
                return Reflect.get(target, key, context);
            } else {
                throw "no such property/method"
            }
        },
        set(target, key, val, context) {
            if (Reflect.has(target, key)) {
                return Reflect.set(target, key, val, context);
            } else {
                throw 'no such property/method';
            }
        }
    },
    pobj = new Proxy(obj, handlers);

pobj.a = 3;
pobj.foo(); // a: 3
// 代理对象没有的属性和方法，将抛出错误
pobj.b = 4; // Error: no such property/method
pobj.bar(); // Error: no such property/method


// 代理在后设计
var handler2 = {
        get() {
            throw "no such property/method";
        },
        set() {
            throw "no such property/method";
        }
    },
    pobj2 = new Proxy({}, handler2),
    obj2 = {
        a: 1,
        foo() {
            console.log("a: ", this.a);
        }
    };
Object.setPrototypeOf(obj, pobj);
obj.a = 3;
obj.foo(); // a: 3
// 遍历整个[[Prototype]]没有发现匹配的属性和方法
obj.b = 4; // Error: no such property/method
obj.bar(); // Error: no such property/method
```

## 代理hack[[Prototype]]链

​	`[[Prototype]]`机制原作的主要通道是`[[Get]]`运算，当直接对象中没有找到一个属性的时候，`[[Get]]`会自动把这个运算转给`[[Prototype]]`对象处理。

```javascript
var obj1 = {
        name: "obj-1",
        foo() {
            console.log("obj1.foo: ", this.name);
        }
    },
    obj2 = {
        name: "obj-2",
        foo() {
            console.log("obj2.foo: ", this.name);
        },
        bar() {
            console.log("obj2.bar: ", this.name);
        }
    },
    handlers = {
        get(target, key, context) {
            if (Reflect.has(target, key)) {
                return Reflect.get(target, key, context);
            } else {
                // 伪装多个[[Prototype]]
                for (var p of target[Symbol.for("[[Prototype]]")]) {
                    if (Reflect.has(p, key)) {
                        return Reflect.get(p, key, context);
                    }
                }
            }
        }
    },
    obj3 = new Proxy(
    	{
            name: 'obj-3',
            baz() {
                this.foo();
                this.bar();
            }
        },
        handlers
    );

// 伪装多个[[Prototype]]链接
obj3[Symbol.for("[[Prototype]]")] = [obj1, obj2];
obj3.baz();
// obj1.foo: obj-3
// obj2.bar: obj-3
```

# Reflect

​	平凡对象，持有对应各种可控的元编程任务的静态函数，这些函数一对一对应着代理可以定义的处理函数方法；方法和`Object`上的同名方法类似，区别是：如果第一个参数（`target`目标对象）不是对象的话，`Object`会视图把它转换成一个对象，而`Reflect`只会抛出一个错误。

## 属性排序

1. 按照数字上升排序，枚举所有整数索引拥有的属性；
2. 按照创建顺序枚举其余拥有的字符串属性名；
3. 按照创建顺序枚举拥有的符号属性；

```javascript
var o = {}; 
o[Symbol("c")] = "yay"; 
o[2] = true; 
o[1] = true; 
o.b = "awesome"; 
o.a = "cool"; 
Reflect.ownKeys( o ); // [1,2,"b","a",Symbol(c)] 
Object.getOwnPropertyNames( o ); // [1,2,"b","a"] 
Object.getOwnPropertySymbols( o ); // [Symbol(c)]
```

# 特性测试

FeatureTests.io（https://featuretests.io）测试浏览器是否支持ES6+语法和语义行为特性

# 尾递归调用

​	在一个函数内部调用另外一个函数的时候，会分配第二个栈帧来独立管理第二个函数调用的变量、状态，这个分配不但消耗处理时间，也消耗了额外的内存。通常调用栈链10-15个从一个函数到另外一个函数的跳转，内存使用并不会造成任何实际问题；要考虑的是递归编程，调用栈的深度很容易达到成百上千。**尾调用**的函数调用模式可以避免额外栈帧分配的方式进行优化。
​	**尾调用**是一个return函数调用的语句，除了调用后返回值之外，没有任何其他动作。

```javascript
"use strict"
function foo(x) {
    return x * 2;
}
function bar(x) {
    // 这不是尾调用
    // foo()调用完，还得执行1 + ..， 所以bar()调用的状态需要被保留
    return 1 + foo(x);
}
bar(10); // 21


function baz(x) {
    x = x + 1;
    if (x > 10) {
        return foo(x);
    } else {
        // 递归，x + 1在bar()调用之前求值，在调用结束之后，只有return
        // 不需要对下一个函数调用创建一个新的栈帧，值需要复用已有的栈帧
        // 因为一个函数不需要保留任何当前状态
        return baz(x + 1);
    }
}
```

## 尾调用重写

```javascript
"use strict"
function foo(x) {
    if (x <= 1) return 1;
    // 不是尾调用优化，每次结果在 return 之前都要加上 (x / 2)
    return (x / 2) + foo(x - 1);
}
foo(123456); // RangeError

// 优化一下，使代码更适合ES6引擎TCO(尾调用优化)
var foo = (function() {
    function _foo(acc, x) {
        if (x <= 1) return acc;
        return _foo((x / 2) + acc, x - 1);
    }
    return function(x) {
        return _foo(1, x);
    }
})();
foo(123456); // 3810376848.5
```

## （递归展开）非尾调用优化

​	`trampolining`把每个部分结果用一个函数表示，这些函数或者返回另外一个部分结果函数，或者返回最终结果，然后只需要循环知道得到的结果不是函数，得到的就是最终结果。

```javascript
"use strict"
function trampoline(res) {
    while (typeof res === 'function') {
        res = res();
    }
    return res;
}

var foo = (function() {
    function _foo(acc, x) {
        if (x <= 1) return acc;
        // 每个内部的partial()只是返回到trampoline的while循环，trampolining运行函数并允许下一次的循环迭代
        // partial()不会递归调用自身，只是返回一个函数，栈深度保持不变，可以允许任意长的时间
        return function partial() {
            return _foo((x / 2) + acc, x - 1);
        }
    }
    return function(x) {
        return trampoline(_foo(1, x));
    }
})();

foo(123456); // 3810376848.5
```

# Object.observe()

​	监听数据对象的更新，可观察的改变有6中类型：

- `add` 添加属性
- `update` 更新属性
- `delete` 删除属性
- `reconfigure`一个对象通过`Object.defineProperty()`重新配置属性，比如修改`writable`，就会触发
- `setPrototype`
- `preventExtensions`一个对象通过`Object.preventExtensions()`变为不可扩展，就会触发

```javascript
var obj = { a: 1, b: 2 };
Object.observe(
	obj,
    function(changes) {
        for (var change of changes) {
            console.log(change);
        }
    },
    ['add', 'update', 'delete']
);
obj.c = 3; // { name: 'c', object: obj, type: 'add' }
obj.a = 42; // { name: 'a', object: obj, type: 'update', oldValue: 1 }
delete obj.b; // // { name: 'b', object: obj, type: 'delete', oldValue: 2 }
```

## 自定义改变事件

```javascript
function observe(changes) {
    for (var change of changes) {
        if (change.type == 'recalc') {
            change.object.c =
                change.object.oldValue +
                change.object.a +
                change.object.b;
        }
    }
}

function changeObj(a, b) {
    var notifier = Object.getNotifier(obj);
    obj.a = a * 2;
    obj.b = b * 3;
    // 把事件排到一个集合中
    notifier.notify({
        type: 'recalc',
        name: 'c',
        oldValue: obj.c
    });
    // 还有一种方式处理 performChange
    /*
    notifier.performChange('recalc', function() {
        return {
            name: 'c',
            oldValue: this.c
        }
    });
    */
}

var obj = { a: 1, b: 2, c: 3 };
Object.observe(
	obj,
    observer,
    ['recalc']
);
changeObj(3, 11);
obj.a; // 12
obj.b; // 30
obj.c; // 3
// recalc事件没有发送
// 默认情况下，改变会在当前时间循环的最后发送
// 可以使用 Object.deliverChangeRecords(observer) 立即发送
// 事件发送后
obj.c; // 42
```

## 结束观测

​	`Object.unobserve()`停止观测一个对象的改变时间