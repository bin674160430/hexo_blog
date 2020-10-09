---
title: javascript 设计模式
date: 2020-09-30 18:32:32
categories:
    - js
tags:
    - 设计模式
---

# 单例模式

保证一个类仅有一个实例，并提供一个访问它的全局访问点，例如登录浮窗

```javascript
// 有多种方式实现，比如拥有命名空间的全局变量，如 window
// 下面提供一个通用的单例模式方法，为惰性加载，需要的时候生成对应的类别
var getSingle = (function() {
  var result;
  return function(fn) {
    return result || (result = fn.apply(this, arguments));
  }
});

var createDOM = function(html) {
  var div = document.createElement('div');
  div.innerHTML = html;
  div.style.dispaly = 'none';
  document.body.appendChild(html);
  return div;
};

var singleCreateDOM = getSingle(createDOM);
var dom = singleCreateDOM('a');
dom.style.display = 'block';
```

# 策略模式

定义一系列的算法，把它们封装起来，将算法的使用与算法的实现分离开来

优点：
	1、利用组合、委托和多态等技术和思想，可以有效地避免多重条件选择语句
	2、提供了对开放-封闭原则的完美支持，将算法封装在独立的strategy中，并使得易于理解、扩展
	3、实现的算法也复用在其它地方，从而避免许多重复的复制粘贴工作
	4、利用组合和委托来让context拥有执行算法的能力，这也是继承的一种更轻便的替代方案

## LOL中四个技能的释放，攻击数值的计算

```javascript
var skill = {
  'Q': function(output) {
    return output * 2;
  },
  'W': function(output) {
    return output * 3;
  },
  'E': function(output) {
    return output * 4;
  },
  'R': function(output) {
    return output * 5;
  }
};

var heroBouns = function(keyword, output) {
  return skill[keyword](output);
};
```

## 表单校验

```html
<html>
  <body>
    <form action="http://xxx.com/register" id="registerForm" method="post">
      userName: <input type="text" name="userName" />
      password: <input type="password" name="password" />
      phone: <input type="text" name="phone" />
      <button>提交</button>
    </form>
  </body>
</html>
<script>
	// 这是一种很常见的编码方式，
  // 1. 函数比较庞大，包含了很多if-else语句，这些条件需要覆盖所有的校验规则
  // 2. 函数缺乏弹性，如果增加了一种新的校验规则，或者想把密码的长度从6改成8，必须深入onsubmit内部实现，违反开放-封闭原则
  // 3. 复用性差，在程序中增加另外一个表单，这个表单也需要进行一些类似的校验，很可能把这些逻辑复制得满天遍野
  var registerForm = document.getElementById('registerForm');
  registerForm.onsubmit = function() {
    if (registerForm.userName.value === '') {
      alert('用户名不能为空');
      return false;
    }
    if (registerForm.password.value.length < 6) {
      alert('密码长度不能少于6位');
      return false;
    }
    if (!/(^1[3|5|8][0-9]{9}$)/.test(registerForm.phone.value)) {
      alert('手机号码格式不正确');
      return false;
    }
  }
</script>
<script>
	// 用策略模式来重构表单校验的代码
  // 第一步，封装策略对象——校验逻辑
  var strategies = {
    // 不为空
    isNonEmpty: function(value, errorMsg) {
      if (value === '') {
        return errorMsg;
      }
    },
    // 限制最小长度
    minLength: function(value, length, errorMsg) {
      if (value.length < length) {
        return errorMsg;
      }
    },
    // 手机号码格式
    isMobile: function(value, errorMsg) {
      if(!/^(1[3|5|8][0-9]{9}$)/.test(value)) {
        return errorMsg;
      }
    }
  };
  // 第二步，准备实现Validator类，在这里作为Context，负责接收用户的请求并委托给strategy对象，在给出Validator类的代码之前，有必要提前了解用户是如何向Validator类发送请求的，这有助于我们知道如何去编写Validator类的代码
  var validataFunc = function() {
    var validator = new Validator();
    validator.add(registerForm.userName, 'isNonEmpty', '用户名不能为空');
    validator.add(registerForm.password, 'minLength:6', '密码长度不能少于6位');
    validator.add(registerForm.phone, 'isMobile', '手机号码格式不正确');
    var errorMsg = validator.start();
    return errorMsg;
  };
  
  var registerForm = document.getElementById('registerForm');
  registerForm.onsubmit = function() {
    var errorMsg = validataFunc();
    if (errorMsg) {
      alert(errorMsg);
      return false;
    }
  }
  // 第三步，从上面代码可以看出，先创建了一个Validator对象，通过validator.add方法，往validator对象中添加一些校验规则，再通过通过validator.start方法来启动校验，如果返回了一个确切的errorMsg字符串，说明这次验证没通过
  var Validator = function() {
    this.cache = []; // 保存校验规则
  };
  Validator.prototype.add = function(dom, rule, errorMsg) {
    var ary = rule.split(':');
    this.cache.push(function() { // 把校验的步骤用空函数包装起来，并且放入cache
      var strategy = ary.shift();
      ary.unshift(dom.value);
      ary.push(errorMsg);
      return strategies[strategy].apply(dom, ary);
    });
  };
  Validator.prototype.start = function() {
    for (var i = 0, validatorFunc; validatorFunc = this.cache[i++];) {
      var msg = validatorFunc();
      if (msg) {
        return msg;
      }
    }
  };
</script>
```

# 代理模式

代理模式是为一个对象提供一个代用品或占位符，以便控制对它的访问

javascript中最常用的是虚拟代理和缓存代理，虽然代理模式非常有用，但是在编写业务代码的时候，往往不需要去预先猜测是否需要使用代理模式，当真正发现不方便直接访问某个对象的时候，再编写代理也不迟。

## 虚拟代理实现图片预加载

在web开发中，图片预加载是一种常用的技术，如果直接给某个`img`节点设置`src`属性，由于图片过大或者网络不佳，图片的位置往往有段时间会是一片空白，常见的做法是先用一张`loading`图片占位，然后用异步的方式加载图片，等图片加载好了再把它填充到`img`节点

```javascript
var myImage = (function() {
  var imgNode = document.createElement('div');
  document.body.appendChild(imgNode);
  return function(src) {
    imgNode.src = src;
  }
})();

var proxyImage = (function() {
  var img = new Image;
  img.onload = function() {
    myImage(this.src);
  }
  return function(src) {
    myImage('file:// /C:/Users/loading.gif');
    img.src = src;
  }
})();

proxyImage('http://xxx.jpg');
```

## 虚拟代理合并http请求

`checkbox`勾选同步文件请求，如果每次勾选或者取消勾选都需要发起请求，那么一秒钟之内频繁的操作将会带来相当大的网络开销，可以通过代理的方式搜集一段时间之内的请求，最后一次性发给服务器，比如等待2秒之后才把这2秒内需要同步的文件ID打包给服务器，如果不是实时性要求非常高的系统，2秒的延迟不会带来太大的副作用，却能大大减轻服务器压力

```javascript
var synchronousFile = function(id) {
    console.log('开始同步文件: ' + id);
};

var proxySynchronousFile = (function() {
    var cache = [],
        timer;
    return function(id) {
        cache.push(id);
        if (timer) {
            return;
        }
        timer = setTimeout(function() {
            sychronousFile(cache.join(','));
            clearTimeout(timer);
            timer = null;
            cache.length = 0;
        }, 2000);
    }
})();

var checkbox = document.getElementsByTagName('input');
for( var i = 0, c; c = checkbox[i++]; ) {
    c.onclick = function() {
        if (this.checked === true) {
            proxySynchronousFile(this.id);
        }
    }
}
```

## 虚拟代理在惰性加载中的应用

​	`miniConsole.js`调试浏览器输出日志的，并不想在一开始就加载这么一份`js`文件，因为不是每个用户都需要`log`，在必要的时候才开始加载，比如当用户按下F12主动召唤出控制台的时候。

​	在`miniConsole.js`加载之前，为了能够让用户正常地使用里面的api，通常的解决方案是用一个占位的`miniConsole`代理对象来给用户提前使用，这个代理对象提供给用户的接口，跟实际的`miniConsole`是一样的。用户使用这个代理打印log的时候，并不是真正在控制台打印日志，更不会在页面中创建任何`DOM`节点，因为真正的`miniConsole.js`还没加载。

​	于是，把打印`log`的请求都包裹在一个函数里面，（相当于其他语言命令模式中的Command对象）。随后这些函数将全部被放到缓存队列中，这些逻辑都是`miniConsole`代理对象中完成的，等用户按下`F12`召唤出控制台的时候，才开始加载真正的`miniConsole.js`，加载完之后将遍历`miniConsole`代理对象中的缓存函数队列，同时以此执行它们。

未加载真正的`miniConsole.js`之前的代码

```javascript
var cache = [];
var miniConsole = {
   log: function() {
       var args = arguments;
       cache.push(function() {
           return miniConsole.log.apply(miniConsole, args);
       });
   }
};

miniConsole.log(1);
```

按下`F12`，加载真正的`miniConsole.js`

```javascript
var miniConsole = (function() {
    var cache = [];
    var handler = function(ev) {
        if (ev.keyCode === 113) {
            var script = document.createElement('script');
            script.onload = function() {
                for(var i = 0, fn; fn = cache[i++];) {
                    fn();
                }
            };
            script.src = 'miniConsole.js';
            document.getElementsByTagName('head')[0].appendChild(script);
            document.body.removeEventListener('keydown', handler); // 只加载一次miniConsole.js
        }
    };
    document.body.addEventListener('keydown', handler, false);
    return {
        log: function() {
            var args = arguments;
            cache.push(function() {
                return miniConsole.log.apply(miniConsole, args);
            });
        }
    }
})();

// 真正加载到的miniConsole.js
miniConsole = {
    log: function() {
        console.log(Array.prototype.join.call(arguments));
    }
};
```

# 迭代器模式

提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露该对象的内部表示，即使不关心对象的内部构造，也能按顺序访问其中的各个元素。

```javascript
var each = function(ary, callback) {
    for(var i = 0, l = ary.length; i < l; i++) {
        callback.call(ary[i], i);
    }
};

each([1,2,3], function(i, n) {
    console.log(i, n);
});
```

## 内部迭代器

​	例如上面的`each`函数，函数内部已经定义好迭代规则，它完全接手整个迭代过程，外部只需要一次调用。

​	内部迭代器在调用的时候非常方便，外部不需要关心迭代器内部的实现，跟迭代器的交互也仅仅是一次调用而已，但这刚好也是内部迭代器的缺点：由于内部迭代器已经被提前规定，上面的`each`函数就无法同时迭代2个数组。

​	加入现在有个需求，要判断2个数组里的元素是否完全相等，如果不改写`each`函数本身的代码，能够快速入手的只有`each`的回调函数了

```javascript
var compare = function(ary1, ary2) {
    if (ary1.length !== ary2.length) {
        throw new Error('ary1和ary2不相等');
    }
    each(ary1, function(i, n) {
        if (n !== ary2[i]) {
            throw new Error('ary1和ary2不相等');
        }
    });
    alert('ary1和ary2相等');
};
```

## 外部迭代器

必须显示地请求迭代下一个元素，增加了调用的复杂度，但相对也增强了迭代器的灵活度，可以手动控制迭代器的过程和顺序

```javascript
var Iterator = function(obj) {
    var current = 0;
    var next = function() {
        current += 1;
    };
    var isDone = function() {
        return current >= obj.length;
    };
    var getCurrentItem = function() {
        return obj[current];
    };
    return {
        next: next,
        isDone: isDone,
        getCurrentItem: getCurrentItem
    }
};

// 对比数组的写法
var compare = function(iterator1, iterator2) {
    while(!iterator1.isDone() && !iterator2.isDone()) {
        if (iterator1.getCurrentItem() !== iterator2.getCurrentItem()) {
            throw new Error('iterator1和iterator2不相等');
        }
        iterator1.next();
        iterator2.next();
    }
    alert('iterator1和iterator2相等');
};

compare(Iterator([1,2,3]), Iterator([3,2,1]));
```

## 中止迭代器

回调函数返回`false`，终止循环

```javascript
var reverseEach = function(ary, callback) {
    for (var l = ary.length - 1; l >= 0; l--) {
        if (callback(ary[l], l) === false) break;
    }
}
```

## 应用例子

根据不同浏览器获取相应的上传组件对象

```javascript
var getUploadObj = function() {
    try {
        return new ActiveXObject("TXFTNActiveX.FTNUpload"); // IE上传控件
    } catch (e) {
        if (supportFlash()) { // supportFlash函数未提供
            var str = '<object type="application/x-shockwave-flash"></object>';
            return $(str).appendTo($('body'));
        } else {
            var str = '<input name="file" type="file">';
            return $(str).appendTo($('body'));
        }
    }
}
```

由上面代码为了得到一个`upload`对象，函数里充满了try，catch，if，很难阅读，严重违反开闭原则，下面使用迭代获取`upload`，并约定获取函数中的`upload`可用，返回该对象，否则返回`false`

```javascript
var getActiveUploadObj = function() {
    try {
        return new ActiveXObject("TXFINActiveX.FTNUpload");
    } catch (e) {
        return false;
    }
};

var getFlashUploadObj = function() {
    if (supportFlash()) {
        var str = '<object type="application/x-shockwave-flash"></object>';
        return $(str).appendTo($('body'));
    }
    return false;
};

var getFormUploadObj = function() {
    var str = '<input name="file" type="file">';
    return $(str).appendTo($('body'));
};

var iteratorUploadObj = function() {
    for(var i = 0, fn; fn = arguments[i++];) {
        var uploadObj = fn();
        if (uploadObj !== false) {
            return uploadObj;
        }
    }
};

var uploadObj = iteratorUploadObj(getActiveUploadObj, getFlashUploadObj, getFormUploadObj);
```

# 发布——订阅模式（观察者模式）

定义对象之间的一种一对多的依赖关系，当一个对象的形状发生改变时，所有依赖它的对象都将得到通知