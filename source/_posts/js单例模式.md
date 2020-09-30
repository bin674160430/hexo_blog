---
title: javascript 单例模式
date: 2020-09-30 10:07:00
categories:
    - js
tags:
    - 设计模式
---

# 实现单例模式

```javascript
var Singleton = function(name) {
    this.name = name;
};

Singleton.prototype.getName = function() {
    return this.name;
};

Singleton.getInstance = (function() {
    var instance = null;
    return function(name) {
        if (!instance) {
            instance = new Singleton(name);
        }
        return instance;
    }
})();

var a = Singleton.getInstance('sven1'); // a.name = sven1
var b = Singleton.getInstance('sven2'); // b.name = sven1
console.log(a === b); // true
```

# 创建提示框dom节点

```javascript
var CreateDiv = (function() {
    var instance;
    var CreateDiv = function(html) {
        if (!instance) {
            this.html = html;
            this.init();
            instance = this;
        }
        return instance;
    };
    CreateDiv.prototype.init = function() {
        var div = document.createElement('div');
        div.innerHTML = this.html;
        document.body.appendChild(div);
    };
    return CreateDiv;
})();

var a = new CreateDiv('a');
var b = new CreateDiv('b');
a === b; // true, html 都是a
/*
这是一种不好的做法，看起来很怪,
如果要让这个类从单例变成一个普通可产生多个实例的类，那必须改写CreateDiv构造函数，把控制创建唯一对象的去掉
*/
```

# 代理实现单例模式

```javascript
var CreateDiv = function(html) {
    this.html = html;
    this.init();
};
CreateDiv.prototype.init = function() {
    var div = document.createElement('div');
    div.innerHTML = this.html;
    document.body.appendChild(div);
};

var ProxySingletonCreateDiv = (function() {
    var instance;
    return function(html) {
    	if(!instance) {
            instance = new CreateDiv(html);
        }
        return instance;
    };
})();

var a = new ProxySingletonCreateDiv('a');
var b = new ProxySingletonCreateDiv('b');
a === b; // true
```

# 惰性单例

```javascript
Singleton.getInstance = (function() {
    var instance = null;
    return function(name) {
        if(!instance) {
            instance = new Signleton(name);
        }
        return instance;
    }
})();
```

以网页登录框为例，很明显这个登录框总是唯一的，不可能同时存在两个登录框

实现方案：

> 一、页面加载完成的时候创建好这个dom，并隐藏起来，当用户点击登录按钮的时候，才显示
>
> ```javascript
> // <button id="loginBtn">登录</button>
> 
> // 全局变量loginLayer
> var loginLayer = (function() {
>     var div = document.createElement('div');
>     div.innerHTML = '登录框';
>     div.style.display = 'none';
>     document.body.appendChild(div);
>     return div;
> })();
> 
> document.getElementById('loginBtn').onclick = function() {
>     loginLayer.style.display = 'block';
> }
> 
> // 这种方式总是一开始就被创建好，如果用户不用登录，将造成资源浪费
> ```
>
> 二、用户点击登录按钮的时候才开始创建登录框
>
> ```javascript
> var createLoginLayer = (function() {
>     var div = null;
>     return function() {
>         if (!div) {
>             div = document.createElement('div');
>             div.innerHTML = '登录框';
>             div.style.display = 'none';
>             document.body.appendChild(div);
>         }
>         return div;
>     }
> })();
> 
> document.getElementById('loginBtn').onclick = function() {
>     var loginLayer = createLoginLayer();
>     loginLayer.style.display = 'block';
> }
> ```

```javascript
// 通用惰性单例
var getSingle = function(fn) {
    var result;
    return function() {
        return result || (result = fn.apply(this, arguments));
    }
};

var createLoginLayer = function(html) {
    var div = document.createElement('div');
    div.innerHTML = html;
    div.style.display = 'none';
    document.body.appendChild(div);
    return div;
};

var createSingleLoginLayer = getSingle(createLoginLayer);

document.getElementBy('loginBtn').onclick = function() {
    var loginLayer = createSingleLoginLayer();
    loginLayer.style.display = 'block';
}
```

