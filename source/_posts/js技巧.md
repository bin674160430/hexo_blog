---
title: javascript 技巧
date: 2020-09-29 09:29:52
categories:
    - js
tags:
    - javascript
---

# 标签实现跳转

```javascript
foo: for (var i = 0; i < 4; i++) {
    for (var j = 0; j < 4; j++) {
        if (j == i) {
            // 跳转到foo的下一个循环
            continue foo;
        }
        if (j * i % 2 == 1) {
            continue;
        }
        console.log(i, j);
    }
}
// 1 0
// 2 0
// 2 1
// 3 0
// 3 2

foo: for (var i = 0; i < 4; i++) {
    for (var j = 0; j < 4; j++) {
        if (i * j >= 3) {
            console.log('stopping!', i, j);
            break foo; // 跳出标签 foo 所在的循环 / 代码块，继续执行后面的代码
        }
        console.log(i, j);
    }
}
// 0 0
// 0 1
// 0 2
// 0 3
// 1 0
// 1 1
// 1 2
// stoping! 1 3
```



# for声明变量指向数组长度

```javascript
const arr = [1,2,3];
// 为什么不直接用 i < arr.length 作为条件？
// 假设在遍历的时候有修改数据长度，那么将不好处理之前的数组；
// 常见场景——数据监听变更前后
for (let i = 0, l = arr.length; i < l; i++) {
    
}
```

# 多重判断Array.includes

```javascript
// 判断当前的英雄是不是刺客
function heroIsBravo(name) {
    const bravos = ['劫', '泰隆'];
    // Array.includes 代替 if (name === '劫' || name === '泰隆')
    // 减少||重复判断，直接用一个数组代替更简洁
    if(bravos.includes(name)) {
        console.log('刺客');
    }
}
```

# 更少的嵌套，尽早Return

```javascript
// 如果没有传入参数name，抛出异常
// 这里通过if/else筛选出无效语句
function heroIsBravo(name) {
    const bravos = ['劫', '泰隆'];
    if (name) {
        if (bravos.includes(name)) {
            console.log('刺客');
        }
    } else {
        throw new Error('No hero!');
    }
}

// 遵循规则发现无效条件的时候, 尽早return, 减少if语句多层嵌套, 往后执行的代码只需处理正常情况，减少思考负担
function heroIsBravo(name) {
    // 没有条件, 抛出异常, 
    if (!name) throw new Error('No hero!');
    
    const bravos = ['劫', '泰隆'];
    if (bravos.includes(name)) {
        console.log('刺客');
    }
}
```

# 使用默认参数和解构

```javascript
// 如果参数没有传入，默认值为劫
function heroIsBravo(name = '劫') {
    // 相比起 name = name || '劫'; 来初始默认值更为直观
  	// ...
}

// 如果参数是对象类型，只需要期中某个字段，解构
function heroIsBravo2({name} = {}) {
    
}
heroIsBravo2({name: '劫'});
```

# 遍历对象属性取代switch语句

```javascript
function heroPos(pos) {
    switch() {
        case 'adc':
            return ['EZ', 'VN'];
        case 'bravo':
            return ['劫', '泰隆'];
        default:
            return [];
    }
}

// 遍历对象
function heroPos(pos) {
    const posObj = {
        adc: ['EZ', 'VN'],
        bravo: ['劫', '泰隆']
    };
    return posObj[pos] || [];
}

// 遍历map
function heroPos(pos) {
    const posMap = new Map()
    	.set('adc', ['EZ', 'VN'])
    	.set('bravo', ['劫', '泰隆']);
    return posMap.get(pos) || [];
}
```

# 对所有/部分判断使用

```javascript
const heroList = [
    { name: '泰隆', pos: 'bravo' },
    { name: 'EZ', pos: 'adc' }
];

// 所有的英雄都是 bravo 刺客
function test() {
    // 代码有点长
    let allHeroIsBravo = false;
    for (let hero of heroList) {
        if (allHeroIsBravo) break;
        allHeroIsBravo = (hero.pos === 'bravo');
    }
    return allHeroIsBravo; // false
}

const allHeroIsBravo = heroList.every(hero => hero.pos === 'bravo'); // false

// 同样，是否存在 bravo 刺客
const hasBravoHero = heroList.some(hero => hero.pos === 'bravo'); // true
```

# 字符串借用数组方法

```javascript
const a = 'hero';
a.join; // undefined
a.map; // undefined
const c = Array.prototype.join.call(a, '-'); // 'h-e-r-o'
const d = Array.prototype.map.call(a, function(v) {
    return v.toUpperCase()
}).join("."); // H.E.R.O
```

# 有效数位

```javascript
// 无效语法， 因为 . 被视为常量number 42.的一部分
42.toFixed(3); // SyntaxError

(42).toFixed(3); // "42.000"
0.42.toFixed(3); // "0.420"
42..toFixed(3); // "42.000"
42 .toFixed(3); // "42.000"
```

# 较小数值

​	二进制浮点数最大的问题（所有遵循IEEE754规范的语言都这样）

```javascript
0.1 + 0.2 === 0.3; // false
// 二进制浮点数中的0.1和0.2并不是十分精确，相加后的结果是0.30000000000000004
```

​	很多程序只需要处理整数，最大不超过百万或者万亿，此时用数字类型是绝对安全的。
在处理带有效数的数字时需要特别注意，设置无法范围值（机器精度），这个值通常是`2^- 52 (2.220446049250313e-16)`
从ES6开始，该值就定义在`Number.EPSILON`中，也可以为ES6之前的版本写`polyfill`:

```javascript
if (!Number.EPSILON) {
    Number.EPSILON = Math.pow(2, -52);
}
// 可以用Number.EPSILON来比较两个数字是否相等
function numbersCloseEnoughToEqual(n1, n2) {
    return Math.abs(n1 - n2) < Number.EPSILON;
}
```

​	能够出现的最大浮点数大约是 `1.798e+308`，定义在 `Number.MAX_VALUE`；最小浮点数定义在`Number.MIN_VALUE`，大约是 `5e-324`；能够被安全呈现的最大整数是`2^53 - 1`，即`9007199254740991`，在ES6中被定义为`Number.MAX_SAFE_INTEGER`；最小正式是`-9007199254740991`，在ES6中定义为`Number.MIN_SAFE_INTEGER`

# NaN和Infinity

​	`NaN`不是数字，`Infinity`无穷数，计算结果溢出

```javascript
window.isNaN(2/"foo"); // true
window.isNaN("foo"); // true, 错误bug来着
Number.isNaN("foo"); // false
// 一定要使用 Number.isNaN， ES6提供, 也可以实现以下polyfill
if (!Number.isNaN) {
    Number.isNaN = function(n) {
        return n !== n; // 利用NaN不等于自身的特点
    };
}
```

# ~运算符

​	首先将值强制类型转换为32位数字，然后执行字位操作“非”（对每一个字位进行反转）；
​	另一种诠释——~返回2的补码， `~x` 大致等同于 `-(x+1)`

```javascript
~42; // -(42+1) === -43
// 在 -(x+1) 中唯一能够得到0（或者-0）的x值是-1， 也就是说，~和一些数字值在一起会返回假值0，其它情况则返回真值
// 字符串的indexOf()所搜指定的子字符串，周到返回子字符的位置（从0开始），否则返回-1
var a = "test";
if (a.indexOf('e') === -1) {
    console.log('匹配不到');
} else {
    console.log('匹配到了');
}
// 利用~规则，可以实现
~a.indexOf('a'); // 0
!~a.indexOf('a'); // true

// ~~x 能将值截为一个32为整数, x | 0 也可以
1.1+1.22; // 2.3200000000000003
~~(1.1+1.22); // 2
(1.1+1.22) | 0; // 2
```

# !!显示强制类型转换为布尔值

​	第二个`!`会将结果反转回原值，如果需要转换布尔值作运算，一定使用`Boolean()`或者`!!`来显示强制类型转换，让代码更加清晰。

```javascript
!!"0"; // true
!!""; // false
!!null; // false
!!undefined; // false
!!0; // false
!!NaN; // false
```

# 安全运用隐式强制类型转换

- 如果两边的值中有`true`或者`false`，不要使用`==`
- 如果两边的值有`[]`、`""`或者`0`，不要使用`==`

# 运算符短路

​	&& || 如果左边的操作数能够得出结果，就可以忽略右边的操作数（执行最短路径）

```javascript
// a && b;
// a 是假值，结果是false，不用判断b的值

// a || b
// a 是真值，结果就是a，不用判断b
```

# 字符串运算（支持Unicode）

```javascript
// es6 字符串内置的跌大气，可以识别 Unicode
var gclef = " ";
[...gclef].length;
Array.from(gclef).length;

// 组合音标符号不适用
var s1 = "\xE9", 
 s2 = "e\u0301";
console.log( s1 ); // "é"   字符长度1
console.log( s2 ); // "é"   字符疮长度2

// 可以使用ES6的String.normalize()
s2.normalize().length; // 1
// 如果符号多，存在问题的
```

# 变量比较

```javascript
// 限定年龄在6岁及以上，18岁之间，或者65岁及以上
var age;
if ((6 <= age && age < 18) || 65 <= age) {
    // 小于变量的值放在变量左边，大于的在右边，阅读起来更为清晰
}
```

# 字符串字节长度

`String`对象的`length`属性，不管字符是单字节，还是双字节，都作为一个来计算，如果需要计算字节全长，用一下方式判断

```javascript
const str = 'a中文s'
let charLength = 0;
for (let i = 0, l = str.length; i < l; i++) {
    const char = str.charAt(i);
    if (/^[\u0000-\u00ff]$/.test(char)) {
        charLength ++;
    } else {
        // escape(char).length > 4;
        // str.charCodeAt(i) > 255;
        charLength += 2;
    }
}
```

