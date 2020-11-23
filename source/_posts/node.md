---
title: node
date: 2020-11-05 08:46:40
categories:
    - js
tags:
    - node
---

# Node能作什么

- [x] 具有复杂逻辑的网站
- [x] 基于社交网络的大规模web应用
- [x] Web Socket服务器
- [x] TCP/UDP套接字应用程序
- [x] 命令行工具
- [x] 交互式终端程序
- [x] 带有图形用户界面的本地应用程序
- [x] 单元测试工具
- [x] 客户端javascript编译器

**Node.js**内建了**HTTP**服务器支持，可以轻松实现一个网站和服务器的组合。还可以调用**C/C++**代码，这样可以充分利用已有的诸多函数库，也可以将性能要求非常高的部分用C/C++来实现。

# 异步式I/O与事件驱动

Node.js最大的特点就是采用异步式I/O与事件驱动的架构设计，对于高并发的解决方案，传统的架构是多线程模型，也就是为每个业务逻辑提供一个系统线程，通过系统线程切换来弥补同步式I/O调用时的时间开销。Node.js使用的是单线程模型，对于所有I/O都采用异步式请求方式，避免了频繁的上下文切换，在执行过程中会维护一个事件队列，程序在执行进入事件循环等待下一个事件到来，每个异步I/O请求完成后会被推送到事件队列，等待程序进程进行处理。

```javascript
// 传统的方式查询数据库，执行查询的时候，线程会阻塞，等待数据库返回查询结果，然后再继续处理，（数据库查询可能设计磁盘读写和网络通信，延时可能相当大），对于高并发的访问，一方面线程长期阻塞等待，另一方面为了应付新请求而不断增加线程，将会浪费大量系统资源，同时线程的增多也会占用CPU时间来处理内存上下文切换，而且还容易遭受低速连续攻击
res = db.query('SELECT * FROM SOME_TABLE');
res.output();

// 看下Node.js的解决方式，不会等待执行结果，直到进入事件循环，当数据库查询结果返回时，会将事件发送到事件队列，等到线程进入事件循环以后，才调用之前的回调函数执行后面的逻辑
db.query('SELECT * FROM SOME_TABLE', function(res) {
  res.output();
});
```

# 创建HTTP服务器

```javascript
var http = require('http');
http.createServer(function(req, res) {
	res.writeHead(200, {'Content-Type': 'text/html'});
  res.write('<h1>Node.js</h1>');
  res.end('<p>Hello World</p>');
}).listen(3000);
// 每次修改都需要重启Node.js，可使用supervisor工具监视代码的改动，并自动重启Node.js
```

