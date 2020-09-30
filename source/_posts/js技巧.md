---
title: javascript 技巧
date: 2020-09-29 09:29:52
categories:
    - js
tags:
    - javascript
---

[5 Tips to Write Better Conditionals in JavaScript](https://mp.weixin.qq.com/s/3dBg86oVpt1_bFTqUhP1YQ)

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

