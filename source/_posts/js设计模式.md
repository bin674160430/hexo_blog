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

