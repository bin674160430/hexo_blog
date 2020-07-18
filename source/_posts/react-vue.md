---
title: React 和 Vue 的区别
date: 2020-07-16 20:20:00
categories:
    - 框架
tags:
    - React
    - Vue
---

# 监听数据变化的实现原理不同

`Vue`通过`getter/setter`以及一些函数的劫持，能精确知道数据变化

> 2.x之前以 `Object.defineProperty`来实现，无法监控到数组下标的变化，从性能和性价比考虑，vue放弃了`Object.defineProperty`监听数组变化的特性，通过操作数组的方法`push, pop, shift, unshift, splice, sort, reverse`来监听数组，递归+遍历data对象来时限数据监控
>
> 3.x `Proxy`取代，可以劫持整个对象，并返回一个新对象，有13种劫持方式
>
> https://www.jianshu.com/p/860418f0785c

`React`通过比较引用的方式`diff`进行，如果不优化可能导致大量不必要得`VDOM`重新渲染

# 设计理念不同

`Vue`使用的是可变数据，双向数据，组件和`DOM`之间通过`v-model`双向绑定，`MVVM`框架，由`MVC`发展而来。

`React` 一直不支持双向绑定，强调数据不可变，提倡单向数据，`onChange/setState`，前端组件化框架，由后端组件化发展而来

# Hoc 和 mixins

`Vue`组合不同功能的方式通过mixin，`Vue`中组件是一个被包装的函数，其中处理了模板编译，`props`传参等

`React`组合不同功能的方式是通过Hoc高阶组件，`React`最早也是实用mixins的，后来觉得这种方式对组件的入侵太强会导致很多问题，就弃用了`mixins`转而使用`Hoc`，高阶组件本质就是高阶函数，`React`的组件是一个纯粹的函数

# 组件通信的区别

{% image 组件通讯.png 示例图 %}

`Vue`中有三种方式实现组件通讯，父组件通过`props`向子组件传递数据或者回调，子组件通过事件向父组件发送数据；`V2.2.9`中新增`provide/inject`来实现父组件向子组件注入数据，可以跨多个层级

`React`中也是三种方式实现组件通讯，父组件通过`props`可以向子组件传递数据或者回调；`context`进行跨层级组件通讯

# 模板渲染方式不同

`React`通过`JSX`渲染模板，但`React`并不依赖`JSX`，通过原生JS语法实现，例如插值、条件、循环等，`render`函数支持闭包特性，`import`内容可以在`render`中直接调用

`Vue`通过一种扩展`HTML`的语法进行渲染，`Vue`和组件`js`代码分离单独的模板中，通过指令来实现，例如`v-if`, `v-for`，`Vue`中得数据必须挂在`this`上进行一次中转，例如`import`组件需要再`components`等中再次声明

# Vuex和Redux的区别

`Vuex` `$store`直接注入到了组件实例中，实用`dispatch`、`commit`提交更新，通过`mapState`或者直接通过`this.$store`来读取数据，`Vuex`直接修改`state`，实现原理和`Vue`一样，通过`getter`/`setter`来比较

`Redux`中，每一个组件都需要用`connect`把需要的`props`和`dispatch`连接起来，每次修改数据用`dispatch`，不能直接调用`reducer`进行修改，且都是用新`state`替换旧的`state`，通过`diff`比较差异