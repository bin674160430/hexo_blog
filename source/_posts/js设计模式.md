---
title: javascript 设计模式
date: 2020-09-30 18:32:32
categories:
    - js
tags:
    - 设计模式
---

# 行为委托

​	只有对象来设计代码，可以让语法更加简洁、代码结构更加清晰，对象之间是兄弟关系，互相委托，而不是父类和子类的关系

```javascript
Task = {
    setId: function(ID) { this.id = ID; },
    outputID: function() { console.log(this.id); }
};
// 让XYZ委托Task
XYZ = Object.create(Task);
XYZ.prepareTask = function(ID, Label) {
    this.setID(ID);
    this.label = Label;
};
XYZ.outputTaskDetails = function() {
    this.outputID();
    console.log(this.label);
};
// Task和XYZ并不是类（或者函数），它们是对象。
// XYZ通过Object.create()创建，它的Prototype委托了Task对象。
// 相比于面向类（或者说面向对象），这种编码风格称为“对象关联”，只需要关心XYZ对象委托了Task对象
// 不同之处
// 1. id和label数据成员都是直接存储在XYZ上，而不是Task，在Prototype委托中最好把状态保存在委托者XYZ，而不是委托目标Task
// 2. 在类设计模式中，父类（Task）和子类（XYZ）都有outputTask方法，这样就可以利用重写（多态）的优势。
// 在委托中恰好相反，尽量避免在Prototype链的不同级别中使用相同的命名，否则就需要使用笨拙且脆弱的语法来消除引用分歧
```

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
  var imgNode = document.createElement('img');
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

定义对象之间的一种一对多的依赖关系，当一个对象的形状发生改变时，所有依赖它的对象都将得到通知，如最常用的`DOM`事件机制`addEventListener`

优点：时间上的解耦、对象之间的解耦

缺点：消耗一定的时间和内存，当订阅一个消息后，也许消息最后都没有发生，但这个订阅者会始终存在于内存中；弱化对象之间的联系，导致程序难以维护和理解，特别是多个发布者和订阅者嵌套到一起的时候，要跟中一个bug不是件轻松的事情

实现发布——订阅，售楼处与客户的房源信息通知

- 指定好谁充当发布者（比如售楼处）

- 然后给发布者添加一个缓存列表，用于存放回调函数以便通知订阅者（售楼处的花名册）

- 最后发布消息的时候，发布者会遍历这个缓存列表，依次触发存放的订阅者回调函数（遍历花名册，挨个发短信）

- ```javascript
  var salesOffices = {}; // 定义售楼处
  salesOffices.clientList = []; // 缓存列表，存放订阅者的回调函数
  salesOffices.listen = function(fn) { // 添加订阅者
      this.clientList.push(fn); // 订阅的消息添加进缓存列表
  };
  salesOffices.trigger = function() {
      for(var i = 0, fn; fn = this.clinentList[i++];) {
          fn.apply(this, arguments);
      }
  };
  
  // 小明订阅消息
  salesOffices.listen(function(price, squareMeter) {
      console.log('价格= ' + price);
      console.log('面积= ' + squareMeter);
  });
  // 小红订阅消息
  salesOffices.listen(function(price, squareMeter) {
      console.log('价格= ' + price);
      console.log('面积= ' + squareMeter);
  });
  salesOffices.trigger(20000000, 88);
  salesOffices.trigger(30000000, 110);
  ```

- 上面实现了一个简单的发布-订阅模式，还存在一些问题，订阅者接收到发布者发布的每个消息，假如小明只想买88方的房，而发布者把110方的消息也推送给了小明，这将造成不必要的资源浪费和困扰小明，所以有必要增加一个key，让订阅者只订阅自己感兴趣的信息

- ```javascript
  var salesOffices = {};
  salesOffices.clientList = {};
  salesOffices.listen = function(key, fn) {
      if (!this.clientList[key]) {
          this.clientList[key] = [];
      }
      this.chilentList[key].push(fn);
  };
  salesOffices.trigger = function() {
      var key = Array.prototype.shift.call(arguments),
          fns = this.clientList[key];
      if (!fns || fns.length === 0) {
          return false;
      }
      for(var i = 0, fn; fn = fns[i++];) {
          fn.apply(this, arguments);
      }
  };
  
  salesOffices.listen('squareMeter88', function(price) {
      console.log('价格= ' + price);
  });
  
  salesOffices.listen('squareMeter110', function(price) {
      console.log('价格= ' + price);
  });
  
  salesOffices.trigger('squareMeter88', 2000000); // 发布88方的房子价格
  salesOffices.trigger('squareMeter110', 30000000); // 发布110方的房子价格
  ```

## 发布-订阅模式通用实现

```javascript
// 提取发布——订阅功能
var event = {
    clientList: {},
    // 添加订阅事件
    listen: function(key, fn) {
        if (!this.clientList[key]) {
            this.clientList[key] = [];
        }
        this.clientList[key].push(fn);
    },
    // 取消订阅
    remove: function(key, fn) {
        var fns = this.clientList[key];
        if (!fns) {
            return false;
        }
        if (!fn) { // 如果没有传入具体的函数，需要取消key对应消息的所有订阅
            fns && (fns.length = 0);
        } else {
            for(var l = fns.length - 1; l >= 0; l--) {
                var _fn = fns[l];
                if (_fn === fn) {
                    fns.splice(l, 1); // 删除订阅者的回调函数
                }
            }
        }
    },
    trigger: function() {
        var key = Array.prototype.shift.call(arguments),
            fns = this.clientList[key];
        for(var i = 0, fn; fn = fns[i++];) {
            fn.apply(this, arguments);
        }
    }
};
// 定义动态安装发布——订阅功能
var installEvent = function(obj) {
    for(var key in event) {
        obj[key] = event[key];
    }
};

// 给售楼对象salesOffices动态增加发布——订阅功能
var salesOffices = {};
installEvent(salesOffices);
salesOffices.listen('squareMeter88', function(price) {
    console.log(price);
});
salesOffices.trigger('squareMeter88', 2000000);
```

## 网站登录案例

假如正在开发一个商城网站，有header头部、nav导航、消息列表、购物车等模块，这几个模块的渲染有一个共同的前提条件，必须先用ajax异步请求获取用户的登录后返回的信息，而且将来还有哪些模块也需要使用这些用户信息，如果它们和用户信息模块产生了强耦合，如下代码

```javascript
login.succ(function() {
    header.setAvatar(data.avatar);
    nav.setAvatar(data.avatar);
    message.refresh();
    cart.refresh();
});
```

等到有一天，项目中又新增了一个收货地址管理的模块，需要在登录之后刷新收货地址列表，于是翻开了之前写的登录模块，加上这段代码

```javascript
login.succ(function() {
    header.setAvatar(data.avatar);
    nav.setAvatar(data.avatar);
    message.refresh();
    cart.refresh();
    address.refresh(); // 增加刷新收货地址
});
```

将会越来越疲于应付这些突如起来的业务需求

用发布——订阅模式重写，对用户信息感兴趣的业务模块将自行订阅登录成功的消息事件，当登录成功时，登录模块只需要发布登录成功的消息，而业务方接受消息之后，就会开始进行各自的业务处理，登录模块并不关系业务方究竟要做什么，也不想去了解它们的内部细节

```javascript
$.ajax('http://xxx.com/login', function(data) {
    login.trigger('loginSucc', data);
});

// 各模块监听登录成功的消息
var header = (function() {
    login.listen('loginSucc', function(data) {
        header.setAvatar(data.avatar);
    });
    return {
        setAvatar: function(data) {
            console.log('设置header模块头像');
        }
    }
})();

var nav = (function() {
    login.listen('loginSucc', function(data) {
        nav.setAvatar(data.avatar);
    });
    return {
        setAvatar: function(avatar) {
            console.log('设置nav模块头像');
        }
    }
})();
```

## 全局的发布——订阅对象

前面实现的发布-订阅模式，存在两个小问题

- 每个发布者对象都添加了`listen`和`trigger`方法，以及一个缓存列表`clientList`，这其实是一种资源浪费
- 还存在一定的耦合性，客户至少要知道售楼处对象的名字是`salesOffices`，才能顺利的订阅到事件，如果房源信息来自多个房产公司，那么客户得去订阅多个房产发布者消息，实际上客户是不用关系消息来自哪个房产发布者，在意的是能否顺利收到消息，只需要把订阅的请求交给中介，订阅者和发布者都必须要知道这个中介公司

```javascript
var Event = (function() {
    
    var clientList = {},
        listen,
        trigger,
        remove;
    
    listen = function(key, fn) {
        if (!clientList[key]) {
            clientList[key] = [];
        }
        clientList[key].push(fn);
    };
    
    trigger = function() {
        var key = Array.prototype.shift.call(arguments),
            fns = clientList[key];
        if (!fns || fns.length === 0) {
            return false;
        }
        for(var i = 0, fn; fn = fns[i++];) {
            fn.apply(this, arguments);
        }
    };
    
    remove = function(key, fn) {
        var fns = clientList[key];
        if(!fns) {
            return false;
        }
        if (!fn) {
            fns && (fns.length = 0);
        } else {
            for(var l = fns.length - 1; l >= 0; l--) {
                var _fn = fns[l];
                if (_fn === fn) {
                    fns.splice(l, 1);
                }
            }
        }
    };
    
    return {
        listen: listen,
        trigger: trigger,
        remove: remove
    }
    
})();

Event.listen('squareMeter88', function(price) {
    console.log(price);
});
Event.trigger('squareMeter88', 2000000);
```

## 必须先订阅再发布吗？

发布——订阅模式，都是订阅者必须先订阅一个消息，随后才能接收到发布者发布的消息；如果是发布者先发布一条消息，而在此之前并没有对象来订阅它，这条消息将消失在宇宙中；

- 在某些情况下，需要先将这条消息保存下来，等到有对象来订阅它的时候，再重新把消息发布给订阅者，跟qq中的离线消息一样，离线消息被保存在服务器中，接受人下次登录上线之后，可以重新收到这条消息
- 例如前面的网站中，ajax请求成功返回用户登录成功之后发布一个事件，在此之前订阅了这个事件的模块可以接受到这些用户信息；但这只是理想的情况，因为异步的问题，不能保证ajax请求返回的时间，有可能返回得比较快，在模块代码还没加载好之前（还没来得及去订阅事件），特别是用了一些模块化惰性加载的技术后，我们需要发布—订阅对象拥有先发布后订阅的能力
- 为了满足这个需求，要建立一个存放离线事件的堆栈，当事件发布的时候，如果此时还没有订阅者来订阅这个事件，暂时把发布事件的动作包裹在一个函数里，这些函数将被存入堆栈中，等到有对象来订阅事件的时候，依次遍历堆栈执行这些函数，也就是重新发布里面的事件。当然离线事件的生命周期只有一次，就像qq的未读消息只会被重新阅读一次，所以刚才的操作只能进行一次

## 全局事件的命名冲突

随着使用的频繁，难免会出现事件名冲突的情况，所以还可以给Event提供创建命名空间的功能

在提供最终代码之前，先感受一下怎么使用新增的功能

```javascript
// 先发布后订阅
Event.trigger('click', 1);
Event.listen('click', function(a) {
    console.log(a); // 1
});

// 使用命名空间
Event.create('namespace').listen('click', function(a) {
    console.log(a);
});
Event.create('namespace').trigger('click', 1);
```

```javascript
var Event = (function() {
    
    var global = this,
        Event,
        _default = 'default';
    
    Event = function () {
        
        var _listen,
            _trigger,
            _remove,
            _slice = Array.prototype.slice, // 从某个已有的数组返回选定的元素
            _shift = Array.prototype.shift, // 删除并返回数组的第一个元素
            _unshift = Array.prototype._unshift, // 向数组的开头添加一个或更多元素，并返回新的长度
            namespaceCache = {},
            find,
            each = function ( ary, fn ) {
                var ret;
                for( var i = 0, l = ary.length; i < l; i++ ) {
                    var n = ary[i];
                    ret = fn.call(n, i, n);
                }
                return ret;
            };
        
        _listen = function ( key, fn, cache ) {
            if( !cache[key] ) {
                cache[key] = [];
            }
            cache[key].push(fn);
        };
        
        _remove = function ( key, fn, cache ) {
            if( cache[key] ) {
                if( fn ) {
                    for( var l = cache[key].length - 1; l >= 0; l-- ) {
                        if (cache[key][l] === fn) {
                            cache[key].splice(l, 1);
                        }
                    }
                } else {
                    cache[key] = [];
                }
            }
        };
        
        _trigger = function () {
            var key = _shift.call(arguments),
                cache = _shift.call(arguments),
                args = arguments,
                _self = this,
                ret,
                stack = cache[key];
            
            if (!stack || !stack.length) {
                return;
            }
            return each(stack, function() {
                return this.apply(_self, args);
            });
        };
        
        _create = function (namespace) {
            var namespace = namespace || _default;
            var cache = {},
                offlineStack = [], // 离线事件
                ret = {
                    listen: function (key, fn, last) {
                        _listen( key, fn, cache );
                        if ( offlineStack === null ) {
                            return;
                        }
                        if ( last === 'last' ) {
                            offlineStack.length && offlineStack.pop()();
                        } else {
                            each(offineStack, function() {
                                this();
                            });
                        }
                        
                        offlineStack = null;
                    },
                    one: function (key, fn, last) {
                        _remove( key, null, cache );
                    },
                    remove: function ( key, fn ) {
                        _remove(key, fn, cache);
                    },
                    trigger: function () {
                        var fn,
                            args,
                            _self = this;
                        
                        _unshift.call(arguments, cache);
                        args = arguments;
                        fn = function () {
                            return _trigger.apply( _self, args );
                        };
                        // 离线堆栈存在，还没有订阅者
                        if (offlineStack) {
                            return offlineStack.push( fn );
                        }
                        return fn();
                    }
                };
            	
            	return namespaceCache[namespace] || (namespaceCache[namespace] = ret);
            	/* return namespace ? 
                    ( namespaceCache[namespace] ? namespaceCache[namespace] : namespaceCache[namespace] = ret )
            			: ret; */
        };
        
        return {
            create: _create,
            one: function(key, fn, last) {
                var event = this.create();
                event.one(key, fn, last);
            },
            remove: function(key, fn) {
                var event = this.create();
                event.remove(key, fn);
            },
            listen: function(key, fn, last) {
                var event = this.create();
                event.listen(key, fn, last);
            },
            trigger: function() {
                var event = this.create();
                event.trigger.apply(this, arguments);
            }
        }
        
    };
    
    return Event;
    
})();
```

# 命令模式

最简单和优雅的模式之一，有时候需要向某些对象发送请求，但是并不知道请求的接收者是谁，也不知道被请求的操作是什么，希望用一种松耦的方式来设计程序，使得请求发送者和接收者能够消除彼此之间的耦合关系。

## 菜单程序

编写一个用户界面程序，至少有10个按钮，因为项目复杂，会有一定的分工，某个程序员负责绘制按钮（完全不知道按钮将用来干什么，只知道完成这个按钮的绘制之后，应该怎么给它绑定`onclick`），另外一些程序员负责编写点击按钮后的具体行为，这些行为都将被封装在对象里。

```javascript
// setCommand负责往按钮上安装命令，执行命令的动作被约定为调用command对象execute方法
var setCommand = function(button, command) {
    button.onclick = function() {
        command.execute();
    }
};

// 具体菜单操作
var MenuBar = {
    refresh: function() {
        console.log('刷新菜单目录');
    }
};

var SubMenu = {
    add: function() {
        console.log('增加子菜单');
    },
    del: function() {
        console.log('删除子菜单');
    }
};

// 在让button变得有用起来之前，先把这些行为都封装在命令类中
var RefreshMenuBarCommand = function(receiver) {
    this.receiver = receiver;
};
RefreshMenuBarCommand.prototype.execute = function() {
    this.receiver.refresh();
};

var AddSubMenuCommand = function(receiver) {
    this.receiver = receiver;
};
AddSubMenuCommand.prototype.execute = function() {
    this.receiver.add();
};

var DelSubMenuCommand = function(receiver) {
    this.receiver = receiver;
};
DelSubMenuCommand.prototype.execute = function() {
    console.log('删除子菜单');
};

// 把命令接受者传入到command中，并且把command对象安装到button上面
var button1 = document.getElementById('button1');
var button2 = document.getElementById('button2');
var button3 = document.getElementById('button3');

var refreshMenuBarCommand = new RefreshMenuBarCommand(MenuBar);
var addSubMenuCommand = new AddSubMenuCommand(SubMenu);
var delSubMenuCommand = new DelSubMenuCommand(SubMenu);

setCommand(button1, refreshMenuBarCommand);
setCommand(button2, addSubMenuCommand);
setCommand(button3, delSubMenuCommand);
```

## 撤消和重做

游戏中把用户在键盘的输入都封装成命令，执行过的命令将被存放到堆栈中，播放录像的时候只需要从头开始依次执行这些命令即可

```javascript
var Ryu = {
    attack: function() {
        console.log('攻击');
    },
    defense: function() {
        console.log('防御')；
    },
    jump: function() {
        console.log('跳跃');
    },
    crouch: function() {
        console.log('蹲下');
    }
};

var makeCommand = function(receiver, state) {
    return function() {
        receiver[state]();
    }
};

var commands = {
    "119": "jump",
    "115": "crouch",
    "97": "defense",
    "100": "attack"
};

var commandStack = []; // 保存命令的堆栈
document.onkeypress = function(ev) {
    var keyCode = ev.keyCode,
        command = makeCommand(Ryu, commands[keyCode]);
    if(command) {
        command(); // 执行命令
        commandStack.push(command); // 将刚刚执行过的命令保存进堆栈
    }
};

// 点击播放录像
document.getElementById('replay').onclick = function() {
    var command;
    while(command = commandStack.shift()) { // 从堆栈里依次去除命令并执行
        command();
    }
}
```

## 宏命令

一组命令的集合，通过执行宏命令的方式，可以一次执行一批命令；想象一下，有一个王能遥控器，每天回家的时候，只要按一个特别的按钮，它就会关上房间门，顺便打开电脑登录QQ

```javascript
var closeDoorCommand = {
    execute: function() {
        console.log('关门');
    }
};

var openPcCommand = {
    execute: function() {
        console.log('开电脑');
    }
};

var openQQCommand = {
    execute: function() {
        console.log('登录qq');
    }
};

//宏命令
var MacroCommand = function() {
    return {
        commandsList: [],
        add: function(command) {
            this.commandsList.push(command);
        },
        execute: function() {
            for(var i = 0, command; command = this.commandsList[i++];) {
                command.execute();
            }
        }
    }
};

var macroCommand = MacroCommand();
macroCommand.add(closeDoorCommand);
macroCommand.add(openPcCommand);
macroCommand.add(openQQCommand);

macroCommand.execute();
```

