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

​	识别`Promise`就是定义某种成为`thenable`的东西，任何具有then()方法的对象和函数，就是Promise一致的`thenbale`，当`Promise`调用`then`的时候，即使这个`Promise`已经决议，提供给`then`的回调也总是会被异步调用。

## 链式流程控制可行的Promise固有特性

- 调用`Promise`的`then()`会自动创建一个新的`Promise`从调用返回
- 在完成或拒绝处理函数内部，如果返回一个值或者抛出一个异常，新返回的（可链接的）`Promise`就相应地决议
- 如果完成或拒绝处理函数返回一个`Promise`，它将会被展开，这样一来，不管它的决议值是什么，都会成为当前`then()`返回的链接`Promise`的决议值

## new Promise(...)

​	接受两个函数`(resolve, reject)`回调，用以支持`promise`决议。
​	`resolve()`既可能完成`promise`，也可能拒绝，根据传入的参数而定，如果传入的是一个非`promise`、非`thenable`的立即值，这个`promise`就会完成；如果是一个`promise`、`thenable`，这个值会被递归展开，并且`promise`将取用其最终决议值或状态。
​	`reject()`拒绝`promise`。

## resolve

​	`Promise.resolve()`会将传入的真正Promise直接返回，对传入的`thenable`则会展开，假如`thenable`展开得到一个拒绝状态，那么从`Promise.resolve()`返回的`Promise`实际上就是这同一个拒绝状态；所以它实际的结果可能是完成或拒绝。

```javascript
var rejectedTh = {
  then: function() {
    rejected("Oops");
  }
};
var rejectedPr = Promise.resolve(rejectedTh);

var rejectedPr1 = new Promise(function(resolve, reject) {
  // 用一个被拒绝的promise完成这个promise
  resolve(Promise.reject("Oops"));
});
rejectedPr1.then(
	function fulfiled() {
    // 不会执行到这里，promise已经是reject
  },
  function rejected(err) {
    console.log(err); // Oops
  }
);
```

## reject

`reject()`不会像`resolve()`一样进行展开，如果向`reject()`传入一个`Promise/thenable`值，它就会把这个值原封不动地设置为拒绝理由，后续的拒绝函数接收到的是你实际转给`reject()`的那个`Promise/thenable`，而不是其底层的立即值。

## es6规范命名onFulfilled, onRejected

```javascript
// resolve不太准确
function onFulfilled() {
  
}
function onRejected() {
  
}
new Promise(onFulfilled, onRejected);
```

## 错误处理

`try...catch`只能是同步的，无法用于异步代码模式

```javascript
var p = Promise.resolve(1);
p.then(
	function fulfilled(msg) {
    // number 没有 sring函数，抛出异常
  	console.log(msg.toLowerCase()); 
  }
)
.catch(function(err) {
  
});
// 以catch结束
```

## then(...) 

​	为`promise`注册完成和拒绝处理函数。接受两个参数，第一个用于完成回调，第二个用于拒绝回调。如果两个参数中的任何一个被省略或者作为非函数值传入的话，就会替换为响应的默认回调。默认完成回调只是把消息传递下去，而默认拒绝回调只是重新抛出（传播）其接收到的出错原因。

## catch(...)

​	catch只接受一个拒绝回调参数，并自动替换默认完成回调。
​	等价于`then(null, ..)`

then()和catch()也会创建并返回一个promise，可以用于实现promise链式流程。

## Promise.all([...])

​	只有传入的所有`promise`都完成，返回`promise`才能完成，并得到一个数组，包含所有传入`promise`的完成值；
一旦有一个拒绝，就会得到这个拒绝的`promise`的值。

## Promise.race([...])

​	只要有一个`promise`决议，`Promise.race()`就会被决议。

```javascript
// 1. 超时处理
Promise.race([
  request(), // 网络请求
  timeourPromise(3000) // 3秒限时
])
.then(
	function() {
    // request 在3秒内完成
  },
  function(err) {
    // 要么 request 被拒绝，要么超时
  }
);
```

## Promise.any([...])

​	与all类似，但是会忽略拒绝，只要有一个完成。

## Promise.reject(...)

​	创建一个已被拒绝的`promise`的快捷方式

```javascript
// p1 和 p2是相同的
var p1 = new Promise(function(resolve, reject) {
  reject("Oops");
});
var p2 = Promise.reject("Oops");
```

## Promise.resolve(...)

​	创建一个已完成的promise，由于Promise.resolve()也能展开thenable值，在这种情况下，返回的promise采用传入的这个thenable的最终决议值，可能完成，可能拒绝。
​	如果传入的是promise，Promise.resolve()什么都不会做，只会直接把这个值返回，不会有额外的开销。

```javascript
var fulfilledTh = {
  then: function(cb) { cb(1); }
};
var rejectedTh = {
  then: function(cb, errCb) {
    errCb("Oops");
  }
};

var p1 = Promise.resolve(fulfilledTh); // p1是完成的promise
var p2 = Promise.resolve(rejectedTh); // p2是拒绝的promise
```

```javascript
// ajax 转换为promise
if (!Promise.wrap) {
  Promise.wrap = function(fn) {
    return function() {
      var args = [].slice.call(arguments);
      return new Promise(function(resolve, reject) {
        fn.apply(
        	null,
          args.concat(function (err, v) {
            if (err) {
              reject(err);
            } else {
              resolve(v);
            }
          });
        );
      });
    }
  }
}

var request = Promise.wrap(ajax);
request('url')
.then(..)
..
```

# Generator

​	生成迭代器函数，定义函数前面加个`*`，第一个`next()`总是启动一个迭代器，并运行到`yeild`处；第二个`next()`调用完成第一个被暂停的`yeild`表达式，第三个`next()`调用完成第二个yeild表达式，以此类推。
​	执行生成器，将产生一个迭代器控制这个生成器执行其代码。
​	生成器会在每个`yield`处暂停，函数的状态（作用域）会被保持，即意味着不需要闭包在调用之间保持变量状态，通过`yield`返回到主程序或事件循环队列中。*（yield优先级很低，几乎yield之后的任何表达式都会优先计算，只有解构...和逗号运算符优先级底，会在yield已经被求值之后才能进行绑定）*

```javascript
function *test(x) {
  var y = x * (yield);
  return y;
}
var it = test(2); // 传入2作为参数x
it.next(); // 启动test, 执行到 var y = x * (yeild); 遇到yeild表达式暂停
var res = it.next(3); // 调用yeild表达式，并将3赋予被暂停的yeild表达式的结果
res.value; // 6, 2 * 3 = 6
```

```javascript
function *test(x) {
  var y = x * (yield 'Hello');
  return y;
}
var it = test(1);
var res = it.next(); // 第一个next，不传入参数
res.value; // Hello
res = it.next(2); // 向等待的yeild传入2，相当于代码块(yeild) = 2
res.value; // 2, 1*2=2
```

`yeild` 和 `next` 组合起来，在生成器的执行过程中构成了一个双向消息传递系统

```javascript
// 多个同时运行
function *foo () {
    var x = yield 2;
    z++;
    var y = yield (x * z);
    console.log(x, y, z);
}

var z = 1;

var it1 = foo();
var it2 = foo();

var val1 = it1.next().value; // val1 = 2
var val2 = it2.next().value; // val2 = 2

val1 = it1.next(val2 * 10).value; // val1 = 40, x(yield 2) -> 10*2 = 20, z -> 2; 20 * 2
val2 = it2.next(val1 * 5).value; // val2 = 600, x(yield 2) -> 40 * 5 = 200, z -> 3; 200 * 3

it1.next(val2 / 2); // y = 300; y(yield x*z) -> 600/2;  x: 20, y: 300; z: 3
it2.next(val1 / 4); // y = 10; y(yield x*z) -> 40/4; x: 200, y: 10; z: 3
// 如果next有参数，yeild代码块将被代替
// 没有参数，yeild 采用后面执行的语句结果
```

## iterable

​	必须支持一个函数，函数名是`Symbol.iterator`，调用这个函数的时候，会返回一个迭代器。`for...of`循环自动调用它的`Symbol.iterator`函数来构建一个迭代器

```javascript
var a = [1,3,5,7,9];
var it = a[Symbol.iterator]();
it.next().value; // 1
it.next().value; // 3
it.next().value; // 5
// ...
```

标准的**迭代器**接口：

```javascript
var something = (function() {
    var nextVal;
    return {
        [Symbol.iterator]: function() {
            return this;
        },
        next: function() {
            if (nextVal === undefined) {
                nextVal = 1;
            }
            else {
                nextVal = (3 * nextVal) + 6;
            }
            // done: boolean, 标识迭代器的完成状态, false表示没执行完
            // value: 迭代值
            return { done: false, value: nextVal };
        }
    };
})();
something.next().value; // 1
something.next().value; // 9
something.next().value; // 33
something.next().value; // 105
```

## 生成器迭代器

​	执行一个生成器，就得到了一个迭代器

```javascript
function *something() {
    var nextVal;
    while (true) {
        if (nextVal === undefined) {
            nextVal = 1;
        }
        else {
            nextVal = (3 * nextVal) + 6;
        }
        // 生成器会在每个yield处暂停，函数*something()的状态（作用域）会被保持
        yield nextVal;
    }
}

for (var v of something()) {
    console.log(v);
    if (v > 500) {
        break;
    }
}
// 1 9 33 105 321 969
```

## 停止迭代器

​	生成器的迭代器实例在调用之后，永远留在了挂起状态。其实有个隐藏的特性会帮助管理，`for..of`循环的终止，通常由`break`、`return`或者未捕获异常引起的，会想生成器的迭代器发送一个信号使其终止；在循环正常结束之后，`for..of`循环也会向迭代器发送这个信号。
​	这里可以在外部通过`return(..)`手动终止生成器的迭代实例：

```javascript
var it = something();
for (var v of it) {
    console.log(v);
    if (v > 500) {
        console.log('清理');
        // it.return会立即终止生成器，还会把返回的值设置为传入return(..)的内容
        // 生成器的迭代器已经被设置为 done: true，会在下一个迭代终止
        console.log(it.return("Hello World").value);
        // 不需要break
    }
}
// 1 9 33 105 321 969
// 清理
// Hello World
```

## 提前完成

```javascript
function *foo() {
    yield 1;
    yield 2;
    yield 3;
}
var it = foo();
it.next(); // { value: 1, done: false }
it.return(42); // { value: 42, done: true }
it.next(); // { value: undefined, done: true }

function *foo1() {
    try {
        yield 1;
        yield 2;
        yield 3;
    }
    finally {
        console.log('cleanup');
    }
}
for (var v of foo1()) {
    console.log(v); // 1 2 3 cleanup
}
var it = foo1();
it.next(); // { value: 1, done: false }
it.return(42); // cleanup { value: 42, done: true }
// return相当于在生成器中当前暂停点插入了一个return x
// 不要把yield放在finally内部，会延后return调用的完成

// throw相当于在当前暂停点插入一个throw x，throw同样引起提前完成，在当前暂停点终止生成器运行
var it2 = foo();
it2.next(); // { value: 1, done: false }
try {
    it2.throw('Oops');
} catch(err) {
    console.log(err); // Exception: Oops
}
it2.next(); // { value: undefined, done: true }
```

## 错误处理

```javascript
function *foo() {
    try {
        yield 1;
    } catch(err) {
        console.log(err);
    }
    yield 2;
    throw "Hello";
}
var it = foo();
it.next(); // { value: 1, done: false }
try {
    it.throw("Hi"); // Hi { value: 2, done: false }
    it.next();
    console.log('never gets here');
} catch (err) {
    console.log(err); // Hello
}
```

```javascript
function *foo() {
    try {
        yield 1;
    } catch(err) {
        console.log(err);
    }
    yield 2;
    throw "foo: e2";
}
function *bar() {
    try {
        yield *foo();
        console.log('never gets here');
    } catch (err) {
        console.log(err);
    }
}
var it = bar();
try {
    it.next(); // { value: 1, done: false }
    it.throw('e1'); // e1 { value: 2, done: false }
    it.next(); // foo: e2 { value: undefined, done: true }
} catch (err) {
    console.log('never gets here');
}
it.next(); // { value: undefined, done: true }
```

## 异步迭代生成器

```javascript
function foo (x, y) {
    ajax('url', function(err, data) {
        if (err) {
            // 向 *main()抛出一个错误
            it.throw(err);
        }
        else {
            // 用收到的data恢复*main()，并赋值到yield
            it.next(data);
        }
    });
}

function *main() {
    // 同步错误处理， yield暂停也使得生成器能够捕获错误
    try {
        var text = yield foo(11, 31); // 进入等待
        console.log(text);
    }
    catch (err) {
        console.error(err);
    }
}

var it = main();
it.next(); // 启动
```



# Generator+Promise

```javascript
function foo (x, y) {
    // request(..)是一个支持Promise的Ajax工具
    return request("url");
}

function *main() {
    try {
        var text = yield foo(1, 2);
        console.log(text);
    }
    catch(err) {
        console.error(err);
    }
}

var it = main();
var p = it.next().value;
p.then(
	function(text) {
        it.next(text);
    },
    function(err) {
        it.throw(err);
    }
);
// promise分离了完成和拒绝，通过生成器捕获异常，不用编写不同的Promise链
```

## 支持Promise的Genterator Runner

```javascript
// 自动异步运行传给参数的生成器
function run(gen) {
    var args = [].slice.call(arguments, 1), it;
    // 在当前上下文中初始化生成器
    it = gen.applay(this, args);
    // 返回一个promise用于生成器完成
    return Promise.resolve()
    	.then(function handleNext(value) {
        	// 对下一个yeild的值运行
        	var next = it.next(value);
        	return (function handleResult(next) {
                // 生成器运行完毕
                if (next.done) {
                    return next.value;
                }
                // 没运行完毕，继续执行
                else {
                    return Promise.resolve(next.value)
                    	.then(
                        	// 成功就恢复异步循环，把决议的值返回生成器
                    		handleNext,
                        	// 如果value是被拒绝的promise，把错误传回生成器进行错误处理
                        	function handleErr(err) {
                                return Promise.resolve(it.throw(err))
                                	.then(handleResult);
                            }
                    	)
                }
            })(next);
    	});
}
```

## es7: async / await

​	`await`了一个`promise`，`async`函数就会自动获知要做什么，它会暂停这个函数（跟`Generator`生成器一样），直到`promise`决议。

```javascript
function foo (x, y) {
    // request(..)是一个支持Promise的Ajax工具
    return request('url');
}

async function main() {
    try {
        var text = await foo(11, 31);
        console.log(text);
    }
    catch (err) {
        console.error(err);
    }
}

main();
```

## 生成器中Promise并发

```javascript
// 假设有3个请求，第三个必须等前面两个执行完毕才能发起，由于前两个请求没有限制先后关系，可以同时进行，怎么处理？
function *foo() {
    var results = yield Promise.all([
        // request(..)是一个支持Promise的Ajax工具
        request('url1'),
        request('url2')
    ]);
    var r1 = result[0];
    var r2 = result[1];
    
    var r3 = yield request('url3' + r1 + ',' + r2);
    console.log(r3);
}
// 执行前面定义的runner
run(foo);
```

## 隐藏的Promise

​	生成器实现异步的方法：创建简单、顺序、看似同步的代码，将异步的细节尽可能地隐藏起来。
​	想要实现一系列高级流程控制的话，非常有用的作法是：把Promise逻辑隐藏在一个只从生成器代码调用的函数内部。

## 生成器委托

​	从一个生成器调用另一个生成器，具体语法是 `yield *`,暂停了迭代控制，而不是生成器控制，然后委托到了*的迭代器上。
​	`yield`委托的主要目的是代码组织，以达到和普通函数调用的对称。

```javascript
function *foo() {
    console.log("*foo() starting");
    yield 1;
    yield 2;
    console.log("*foo() finished");
}

function *bar() {
    yield 'a';
    yield 'b';
    yield *foo();
    yield 'c';
}

var it = bar();
it.next().value; // a
it.next().value; // b
it.next().value; // *foo()starting
			    // 1
it.next().value; // 2
it.next().value; // *foo() finished
			    // c
```

## 消息委托

```javascript
function *foo() {
    console.log("inside *foo():", yield "B");
    console.log("inside *foo():", yield "C");
    return "D";
}

function *bar() {
    console.log("inside *bar():", yield "A");
    console.log("inside *bar():", yield *foo());
    console.log("inside *bar():", yield "E");
    return "F";
}

var it = bar();
console.log('outside:', it.next().value);
// outside: A

console.log('outside:', it.next(1).value);
// inside *bar(): 1
// outside: B

console.log('outside:', it.next(2).value);
// inside *foo(): 2
// outside: C

console.log('outside:', it.next(3).value);
// inside *foo(): 3
// inside *bar(): D
// outside: E

console.log('outside:', it.next(4).value);
// inside *bar(): 4
// outside: F
```

`yield`委托不要求必须转到另一个生成器，可以转到一个非生成器的`iterable`

```javascript
function *bar() {
    console.log("inside *bar():", yield "A");
    console.log("inside *bar():", yield *["B", "C", "D"]);
    console.log("inside *bar():", yield "E");
    return "F";
}

var it = bar();
console.log("outside:", it.next().value);
// outside: A

console.log("outside:", it.next(1).value);
// inside *bar(): 1
// outside: B

console.log("outside:", it.next(2).value);
// outside: C

console.log("outside:", it.next(3).value);
// outside: D

console.log("outside:", it.next(4).value);
// inside *bar(): undefined  (由于数组没有返回值)
// outside: E

console.log("outside:", it.next(5).value);
// inside *bar(): 5
// outside: F
```

## 异常也被委托

```javascript
function *foo() {
    try {
        yield 'B';
    }
    catch (err) {
        console.log('error caught inside *foo():', err);
    }
    yield 'C';
    throw 'D';
}

function *bar() {
    yield 'A';
    try {
        yield *foo();
    }
    catch (err) {
        console.log('error caught inside *bar():', err);
    }
    yield 'E';
    yield *baz();
    
    // 不会到达这里， baz抛出异常，这里没有捕获，往外抛，所以baz和bar都被设置为完成状态
    // 继续调用next只会给value返回undefined
    yield 'G';
}

function *baz() {
    throw 'F';
}

var it = bar();

console.log('outside:', it.next().value);
// outside: A

console.log('outside:', it.next(1).value);
// outside: B

console.log('outside:', it.throw(2).value);
// error caught inside *foo()
// outside: C
// foo有捕获异常，可以继续执行

console.log('outside:', it.next(3).value);
// error caught inside *bar(): D
// outside: E
// bar有捕获异常，可以继续执行

try {
    console.log('outside:', it.next(4).value);
}
catch (err) {
    console.log('error caught outside:', err);
}
// error caught outside: F
```

## 异步委托

```javascript
function *foo() {
    // request(..)是一个支持Promise的Ajax工具
    var r2 = yield request('url2');
    var r3 = yield request('url3?' + r2);
    return r3;
}

function *bar() {
    var r1 = yield request('url1');
    var r3 = yield *foo();
    console.log(r3);
}

run(bar);
```

## 递归委托

```javascript
function *foo(val) {
    if (val > 1) {
        val = yield *foo(val - 1);
    }
    // request(..)是一个支持Promise的Ajax工具
    return yield request('url?' + val);
}

function *bar() {
    var r1 = yield *foo(3);
    console.log(r1);
}

run(bar);
```

## 生成器并发

```javascript
var res = [];

function *reqData(url) {
    // request(..)是一个支持Promise的Ajax工具
    var data = yield request(url);
    yield;
    res.push(data);
}

var it1 = reqData('url1');
var it2 = reqData('url2');

var p1 = it1.next();
var p2 = it2.next();

p1.then(function(data) {
    it1.next(data);
});
p2.then(function(data) {
    it2.next(data);
});

// 处理顺序
Promise.all([p1, p2])
.then(function() {
    it1.next();
    it2.next();
});
```

```javascript
// runAll工具，实现生成器并发，并保障结果顺序正确
var res = [];
// 执行方式
// 第一个生成器来自url1的ajax响应得到一个promise，然后把控制yield回runAll()工具
// 第二个生成器对伊url2实现同上的操作
// 第一个生成器恢复运行，通过yield传出promise p1，等待这个promise决议，恢复到同一个生成器，最后将值放在res[0]
// 第二个生成器恢复执行方式同上
runAll(
	function *() {
        var p1 = request('url1');
        yield;
        res.push(yield p1);
    },
    function *() {
        var p2 = request('url2');
        yield;
        res.push(yield p2);
    }
);
```

## 通信顺序进程

```javascript
// 通过data.res和yield的消息交换url1和url2的值，实现通信顺序进程
runAll(
	function *(data) {
        data.res = [];
        // 控制转移（以及消息传递）
        var url1 = yield 'url1';
        var p1 = request(url1);
        // 控制转移
        yield;
        data.res.push(yield p1);
    },
    function *(data) {
        var url2 = yield 'url2';
        var p2 = request(url2);
        yield;
        data.res.push(yield p2);
    }
);
```

## 形实转换程序

​	用一个函数定义封装函数调用，包括需要的任何参数，来定义这个调用的执行，那么这个封装函数就是一个形实转换程序。之后在执行这个thunk时，最终就是调用了原始的函数。

```javascript
function foo(x, y) {
    return x + y;
}
function fooThunk() {
    return foo(3,4);
}

console.log(fooThunk()); // 7
```

```javascript
// 包含异步
function foo(x, y, cb) {
    setTimtout(function() {
        cb(x + y);
    }, 1000);
}

function fooThunk(cb) {
    foo(3, 4, cb);
}

fooThunk(function (sum) {
    console.log(sum);
});

// 接收foo()函数引用，以及需要的任意参数，并返回thunk本身(fooThunk())
function thunkify(fn) {
    var args = [].slice.call(arguments, 1);
    return function(cb) {
        args.push(cb);
        return fn.apply(null, args);
    }
}
var fooThunk = thunkify(foo, 3, 4);
fooThunk(function (sum) {
    console.log(sum); // 7
});
```

```javascript
// thunkify产生一个生成thunk的函数
function thunkify(fn) {
    return function() {
        var args = [].slice.call(arguments);
        return function(cb) {
            args.push(cb);
            return fn.apply(null, args);
        }
    }
}

// 调用区别
var whatIsThis = thinkify(foo);
var fooThunk = whatIsThis(3, 4);
fooThunk(function (sum) {
    console.log(sum); // 7
});

// 这将很有用，在程序开头构造thunkory来封装已有的API方法，并在需要thunk的时候可以传递和调用这些thunkory
```

对比`thunkify()`和`promisify()`*前面实现的Promise.wrap将异步转换为promise*

```javascript
function foo(x, y, cb) {
    setTimeout(function() {
        cb(null, x + y);
    }, 1000);
}

var fooThunkory = thunkify(foo);
var fooPromisory = promisify(foo);

var fooThunk = fooThunkory(3, 4);
var fooPromise = fooPromise(3, 4);

fooThunk(function(err, sum) {
    if (err) {
        console.error(err);
    }
    else {
        console.log(sum); // 7
    }
});

fooPromise
.then(
	function (sum) {
        console.log(sum); // 7
    },
    function (err) {
        console.error(err);
    }
);
```

这里可以看出 `yield` 出 `Promise` 以获得异步生成器，也可以为异步的 `yield thunk`,需要`run()`工具不但能寻找和链接`yield` 出来的`promise`，还能够向`yield` 出来的thunk提供回调

```javascript
function *foo() {
	var val = yield request('url');
    console.log(val);
}
run(foo);

// request 可能是Promise，或者 thunk
var request = Promise.warp(ajax);
var request = thunkify(ajax);
```

`run()`支持`thunk`

```javascript
// 自动异步运行传给参数的生成器， 可以调用 promisory 来 yield Promise， 也可以调用 thunkory 来 yield thunk
function run(gen) {
    var args = [].slice.call(arguments, 1), it;
    // 在当前上下文中初始化生成器
    it = gen.applay(this, args);
    // 返回一个promise用于生成器完成
    return Promise.resolve()
    	.then(function handleNext(value) {
        	// 对下一个yeild的值运行
        	var next = it.next(value);
        	return (function handleResult(next) {
                // 生成器运行完毕
                if (next.done) {
                    return next.value;
                }
                // 支持thunk补丁
                else if (typeof next.value === 'function') {
                	return new Promise(function(resolve, reject) {
                        // 用error-first回调调用这个thunk
                        next.value(function(err, msg) {
                            if (err) {
                                reject(err);
                            }
                            else {
                              	resolve(msg);
                            }
                        });
                    })
                    .then(
                    	handleNext,
                        function handleErr(err) {
                            return Promise.resolve(
                                	it.throw(err)
                            	)
                            	.then(handleResult);
                        }
                    );
                }
                // 没运行完毕，继续执行
                else {
                    return Promise.resolve(next.value)
                    	.then(
                        	// 成功就恢复异步循环，把决议的值返回生成器
                    		handleNext,
                        	// 如果value是被拒绝的promise，把错误传回生成器进行错误处理
                        	function handleErr(err) {
                                return Promise.resolve(
                                    	it.throw(err)
                                	)
                                	.then(handleResult);
                            }
                    	)
                }
            })(next);
    	});
}
```

## ES6之前的生成器

​	ES6之前没有Generator，怎么实现？

```javascript
// request(..)是一个支持Promise的Ajax工具
function *foo (url) {
    // 状态1
    try {
        console.log('requesting:', url);
        var TMP1 = request(url);
        
        // 状态2
        var val = yield TMP1;
        console.log(val);
    }
    catch (err) {
        // 状态3
        console.log('Oops:', err);
        return false;
    }
}
```

下面就是手动实现这个foo

```javascript
function foo(url) {
    // 管理生成状态
    var state;
    // 生成器变量范围声明
    var val;
    
    function process(v) {
        switch (state) {
            case 1:
                console.log('requesting:', url);
                return request(url);
            case 2:
                val = v;
                console.log(val);
                return;
            case 3:
                var err = v;
                console.log('Oops:', err);
                return false;
        }
    }
    
    // 构造并返回一个生成器
    return {
        next: function(v) {
            // 初始状态
            if (!state) {
                state = 1;
                return {
                    done: false,
                    value: process()
                }
            }
            // yield 成功恢复
            else if (state === 1) {
                state = 2;
                return {
                    done: true,
                    value: process(v);
                }
            }
            // 生成器已经完成
            else {
                return {
                    done: true,
                    value: undefined
                }
            }
        },
        "throw": function(e) {
            // 唯一的显示错误处理在状态1
            if (state === 1) {
                state = 3;
                return {
                    done: true,
                    value: process(e);
                }
            }
            // 否则不处理，直接抛出
            else {
                throw e;
            }
        }
    }
}
```

自动转换，http://facebook.github.io/regenerator/



