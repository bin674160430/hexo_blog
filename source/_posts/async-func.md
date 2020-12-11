---
title: 异步代码
date: 2020-12-09 09:51:05
categories:
    - js
tags:
    - 异步

---

> 参考书籍：《你不知道的Javascript（中卷）》

# 异步：现在与将来

> 指程序的一部分**现在**运行，另一部分在**将来**运行；**现在**与**将来**的这段缝隙中**（异步编程的核心）**，可能是在等待用户输入、从数据库或文件系统中请求数据、通过网络发送数据并等待相应，或者以固定的时间间隔执行重复任务（如动画）等
>
> 1. 一旦有事件需要运行，事件循环就会运行，直到队列清空
> 2. 事件循环的每一轮成为一个tick
> 3. 用户交互、IO和定时器会向事件队列中加入事件
>
> 任意时刻，一次只能从队列中处理一个事件。执行事件的时候，可能直接或间接地引发一个或多个后续事件。

## 分块的程序

​	把一段代码包装成一个函数，并指定它在响应某个事件（定时器、鼠标点击、Ajax响应等）时执行，等同于在代码中创建了一个**将来**执行的块，也由此在这个程序中引入了异步机制

```javascript
// 常见的ajax回调
ajax("url", function callback(data) {
  console.log(data); // 获取数据
});
```

## 事件循环

​	程序通常分成了很多小块，在事件循环队列中一个接一个地执行（和你的程序不直接相关的其他事件也会插入到队列中）。

```javascript
// 一段极简的代码展示事件循环概念
var eventLoop = []; // 先进先出
var event;

// 永远执行
while (true) {
  if (eventLoop.length > 0) {
    // 拿到队列中的下一个事件
    event = eventLoop.shift();
    
    // 执行
    try {
      event();
    }
    catch (err) {
      reportError(err);
    }
  }
}
```

## 并行线程

​	并发执行的“进程”（有别于操作系统中的进程）进行某种形式的交互协调，比如需要确保执行顺序或者需要防止竞态出现。这些“进程”可以通过自己分割为更小的块，以便其他“进程”插入进来。

​	**异步**是关于现在和将来的时间间隙，而**并行**是能够同时发生的事情。
并行计算最常见的工具就是**进程**和**线程**，进程和线程独立运行，并可能同时运行；多个线程能够共享进程的内存。
​	与之相对的是，事件循环把自身工作分成一个个任务并顺序执行，不允许对共享内存的并行访问和修改，通过分立线程中彼此合作的事件循环，并行和顺序执行可以共享内存。
​	并行线程的交替执行和异步事件中交替调度，粒度是完全不同的。

```javascript
var a = 2;
function one() {
  a = a + 1;
  a = a + 2;
}
function two() {
  a = a * 2;
  a = a * 3;
}

ajax('url1', one);
ajax('url2', two);
// 由于javascript的单线程特性，一旦one开始运行，所有的代码都会在two中的任意代码运行之前完成；反之一样；这就是函数完整运行特性
// one不会被two中断，two也不会被one中断，哪个先运行，另一个函数以上一个函数得出的a结果继续处理
// 这仅仅是执行顺序的问题，并不会出现多线程中的one在执行到a = a + 1的时候，two穿插执行(a = a * 2)，改了a的值
```

```javascript
var res = [];
function response(data) {
  res.push(data);
}
ajax('url1', response);
ajax('url2', response);
// 由于响应速度的不确定性，将导致res数组的顺序不确定，js中的并发指的是处理响应顺序
// 比如下拉onscroll事件，发起的顺序是对的，但是如果每一个onscroll都会发起一个ajax，那么在处理response的时候将会出现这类顺序错乱，尤其是有处理相同作用域下的变量
```

## 并发协作

取到一个长期运行的“进程”，并将其分割成多个步骤或多批任务，使得其他并发“进程”有机会将自己的运算插入到事件循环队列中交替运行

```javascript
// 假设一个请求响应体里面有千万条数据，这样的“进程”运行时，页面上的其他代码都不能运行
// 所以，要创建一个协作性更强更友好且不会霸占事件循环队列的并发系统，可以异步分批处理这些结果
// 每次处理完返回事件循环，让其他等待事件有机会运行
var res = [];
function response(data) {
	var chunk = data.splice(0, 1000);
  res = res.concat(
  	chunk.map(function(val) {
      return val * 2;
    })
  );
  if (data.length > 0) {
    // 异步调度，“把这个函数插入到当前事件循环队列的尾部”
    // 严格地说，定时器会在有机会的时候插入事件
    setTimeout(function() {
      response(data);
    }, 0);
  }
}
// 这样确保“进程”运行时间很短，事件循环的交替运行会提高站点/app的（响应）性能
```

## 任务

​	任务队列，`Promise`的异步特性，挂在事件循环的每个tick之后的一个队列；在事件循环的每个tick中，可能会出现的异步动作不会导致一个完整的新事件添加到事件循环队列中，而会在每个tick的任务队列末端添加一个任务。
​	事件循环类似一个游乐园游戏：玩过了一个游戏之后，需要重新排队才能再玩一次。而任务队列就类似于玩过游戏之后，插队接着继续玩。
​	一个任务可能引起更多任务被添加到同一个队列末尾，任务循环可能无限循环，进而导致程序饿死，无法转移到下一个事件循环tick，和代码中的无限循环几乎一样。
​	任务和setTimeout的思路类似，但是其实现方式的定义更加良好，对顺序的保证性更强；尽可能早的将来。

```javascript
// 假设一个调度任务为 schedule()
console.log('A');
setTimeout(function() {
  console.log("B");
}, 0);

schedule(function() {
  console.log("C");
  schedule(function() {
    console.log("D");
  });
});
// A C D B
```

# 回调函数

回调编码的信任问题

- 调用回调过早（一个任务有时同步完成，有时异步完成，可能会导致竞态条件）
- 调用回调过晚（或不被调用）
- 调用回调次数过少或过多
- 未能传递所需的环境和参数
  - 吞掉可能出现的错误和异常

```javascript
listen( 'click', function handler(evt) {
  setTimeout( function request() {
    ajax('url', function response(text) {
      if (text === 'hello') {
        handler();
      }
      else if (text === 'world') {
        request();
      }
    } );
  }, 500);
} );
// 回调地狱
// 在顺序地追踪这段代码的过程中，不得不从一个函数跳到下一个，在整个代码中挑来挑去以查看流程
// 一旦指定了所有的可能事件，代码就会变得非常复杂，以至于无法维护和更新，回调地狱的问题所在；
// 我们顺序阻塞式的大脑计划行为无法很好地映射到面向回调的异步代码，对于代码中表达异步的方式，
// 我们的大脑需要努力才能同步得上
```

回调最大的问题就是控制反转，导致信任链的完全断裂。
如果代码中使用了回调，尤其是但也不限于使用第三方工具，而且你还没有应用某种逻辑来解决这些控制反转导致信任问题，那代码已经有了bug。

```javascript
// 有些API设计了分离回调，一个用于成功通知，一个用于出错通知
function success(data) {
  console.log(data);
}
function failure(error) {
  console.error(error);
}
ajax('url', success, failure);

// 还有一种是error-first风格，回调的第一个参数保留作为错误对象，如果成功的话，这个参数会被清空
function response(err, data) {
  if (err) {
    console.error(err);
  }
  else {
    console.log(data);
  }
}
ajax('url', response);

// 以上处理并没有处理信任问题，也没有设计阻止和过滤不想要的重复回调问题；
// 可能同时得到成功或者失败的结果，或者没有，并且不得不编码处理所有这些情况
// 尽管这是一种标准模式，但它更加冗长和模式化，可复用行不高，每个回调都需要添加这样的代码
// 还需要谁一个超时来取消事件
function timeoutify( fn, delay ) {
	var intv = setTimeout( function() {
					intv = null;
    			fn( new Error('Timeout') );
        }, delay ); 
  
  return function() {
  	if ( intv ) {
      clearTimeout( intv );
      fn.apply( this, arguments );
    } 
  }
}

function test( err, data ) {
  if ( err ) {
    console.error( err );
  }
  else {
    console.log( data );
  }
}
ajax( 'url', timeoutify( test, 500 ) );
```

下面案例不确定会不会异步执行

```javascript
function result(data) {
  console.log(a);
}
var a = 0;
ajax('url', result); //不确定打印出0还是1, 不确定是同步回调，还是异步回调
ajax('url', asyncify(result)); // 总会输出1，意味着a++在result之前运行，但这样只会加重代码
a++;

// 验证异步
function asyncify(fn) {
  var orig_fn = fn,
      intv = setTimeout(function() {
        intv = null;
        if (fn) fn();
      }, 0);
  
  fn = null;
  
  return function() {
    // 触发太快，在定时器intv触发异步转换发生之前？
    if (intv) {
      fn = orig_fn.bind.apply(
      	orig_fn,
        // 把封装器的this添加到bind调用参数中
        // 以及柯里化所有传入参数
        [this].concat([].slice.call(arguments))
      )
    }
    else {
      // 调用原来函数
      orig_fn.apply(this, arguments);
    }
  };
}
```

# Promise

​	`Promise`是一种封装和组合未来值的易于复用的机制。
​	由于封装了依赖于时间的状态——等待底层的完成或拒绝，所以`Promise`本身于时间无关，可以按照可预测的方式组成，而不用关心时序或底层的结果。
​	一旦`Promise`决议，就永远保持在这个状态，这个时候就成为了**不变值**（immutable value）。
​	可以从另一个角度看待`Promise`决议：一种在异步任务重作为两个或更多步骤的流程控制机制，时序上的`this-then-that`

```javascript
function foo(x) {
  return new Promise(function(resolve, reject) {
    resolve(x);
  });
}

function bar(fooPromise) {
  fooPromise.then(
  	function(x) {
      // 完成
      console.log('ok', x);
    },
    function(e) {
      // 错误
      console.log('no', e);
    }
  );
}

function baz(fooPromise) {
  fooPromise.then(
  	function(x) {
      // 完成
      console.log('ok', x);
    },
    function(e) {
      // 错误
      console.log('no', e);
    }
  );
}

// baz 和 bar一样

var p = foo(42);
// 不管foo成功与否，bar, baz都会被调用，接收foo的决议通知，跟订阅者模式很响
bar(p); // ok 42
baz(p); // ok 42

// 另一种方式
function bar1() {
  console.log('bar1');
}
function oopsBar1() {
  console.log('oopsBar1');
}
function baz1() {
  console.log('baz1');
}
function oopsBaz1() {
  console.log('oopsBaz1');
}
var p1 = foo(42);
// 只有成功才调用，失败调用oops..
p1.then(bar1, oopsBar1); // bar1
p1.then(baz1, oopsBaz1); // baz1
// 一旦p决议，不论现在还是将来，下一个步骤总是相同的
```

## 具有then方法的鸭子模型

​	识别`Promise`就是定义某种成为`thenable`的东西，任何具有then()方法的对象和函数，就是Promise一致的`thenbale`，当`Promise`调用then的时候，即使这个`Promise`已经决议，提供给`then`的回调也总是会被异步调用。