---
title: 常见兼容问题
date: 2020-07-02 18:42:10
categories:
    - 前端
tags:
    - 兼容
---

# 上下相邻元素margin重叠问题

上下分布两个块级元素，上面元素设置`margin-bottom`，下面元素设置`margin-top`，只能识别期中较大的值，父级元素高度填充欠缺——解决方案：相邻元素的`margin-top``margin-bottom`只设置其中一个属性的值；给其中一个元素再包裹一层盒子，并设置`overflow: hidden`

```html
<div id="test">
    <div style="height: 20px; margin-bottom: 10px;">上面元素</div>
    <div style="height: 20px; margin-top: 20px;">下面元素</div>
</div>
<!--
	#test 高度是 20 + 20 + 20 = 60
	margin-bottom: 10 去哪里了？
	由于下面元素设置的margin-top = 20 > 上面元素 margin-bottom = 10
	只能识别大的值，所以margin-top = 20 计算
-->
<!--
	怎么得到想要的结果？ 20 + 10 + 20 + 20 = 70
	上面元素与下面元素的外边距margin-bottom: 10，margin-top: 20，其实就是30，设置其中一个值即可
	20 + 20 + 30 = 70
	或者其中一个元素包裹多一层，并加入overflow: hidden;
-->
<div id="test">
    <div style="height: 20px;">上面元素</div>
    <div style="height: 20px; margin-top: 30px;">下面元素</div>
</div>
<div id="test">
    <div style="overflow: hidden;">
        <div style="height: 20px; margin-bottom: 10px;">上面元素</div>
    </div>
    <div style="height: 20px; margin-top: 20px;">下面元素</div>
</div>
```

# 给子元素设置margin-top，应用到了父元素

子元素设置了`margin-top`，父元素高度并没有被撑开，而子元素的`margin-top`作用到了父元素

解决方案：

1. 把子元素设置的`margin-top`改为父元素设置`padding-top`
2. 给父元素设置`border-top: 1px solid transparent`
3. 给父元素设置`overflow: hidden`
4. 给父元素设置`float: left`

```html
<div>-----------------</div>
<div id="parent" style="background-color: red;">
    <div style="margin-top: 30px; background-color: yellow;">
        子元素
    </div>
</div>
<div>-----------------</div>
<!--
	从代码结构看，#parent的子元素与父元素存在30的外边距,
	再看看元素的背景颜色，
	但是#parent高度并没有被子元素撑开，而子元素的外边距做到#parent上了
-->
```



# IE和其他浏览器不一样的特性

- ie的引擎是Trident，MSHTML
- 低端ie盒子模型不同，宽高是内边距+边界+内容区，标准盒子模型的宽高就是内容区的宽高
- Trident内核的大量bug等安全性问题没有得到及时解决
- js有很多独立的方法，例如绑定事件的attachEvent, 创建事件createEventObject，ajax的ActiveXObject等
- css有独立的处理方式，例如透明，底版本ie使用滤镜的方式

# 移动端伪装:active生效

css伪类`:active` 生效，只需要给`document`绑定`touchstart`或`touchend`事件

```html
<style>
a {
  color: #000;
}
a:active {
  color: #fff;
}
</style>
<a herf=foo >bar</a>
<script>
  document.addEventListener('touchstart',function(){},false);
</script>
```

# 消除 transition闪屏

```css
-webkit-transform-style: preserve-3d;
/*设置内嵌的元素在 3D 空间如何呈现：保留 3D*/
-webkit-backface-visibility: hidden;
/*（设置进行转换的元素的背面在面对用户时是否可见：隐藏）*/
```

# 消除 IE10 里面的那个叉号

{% image input-ms-clear.jpg 示例图 %}

```css
input::-ms-clear { display:none; }
```

# ios 与 os x端字体横竖屏会出现字体加粗不一致

ios 浏览器横屏时会重置字体大小，设置 `text-size-adjust: none;` 可以解决ios上的问题，但osx的 safari的字体缩放功能会失效，最佳方案 `text-size-adjust: 100%;`

```css
-webkit-text-size-adjust: 100%;
-ms-text-size-adjust: 100%;
text-size-adjust: 100%;
```

# click事件延迟300ms

```html
<meta name="viewport" content="width=device-width, user-scalable=no">

<style>
html {
    -ms-touch-action: manipulation;
    touch-action: manipulation;
}
</style>
```

[https://stackoverflow.com/questions/12238587/eliminate-300ms-delay-on-click-events-in-mobile-safari](https://stackoverflow.com/questions/12238587/eliminate-300ms-delay-on-click-events-in-mobile-safari)

可以使用`tap`（`touchstart, touchmove, touchend`）事件处理

# 不让Android手机识别邮箱

```html
<meta content="email=no" name="format-detection" />
```

# 禁止 iOS 识别长串数字为电话

```html
<meta content="telephone=no" name="format-detection" />
```

# 禁止 iOS 弹出各种操作窗口

```css
-webkit-touch-callout:none
```

# 禁止用户选中文字

```css
-webkit-user-select:none
```

# 动画效果中，使用 translate 比使用定位性能高

https://www.paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/

# 获取滚动条

```js
window.scrollY
window.scrollX

// 比如要绑定一个 touchmove 的事件，正常的情况下类似这样
$('div').on('touchmove', function(){
   //.….code
});

// 而如果中间的 code 需要处理的东西多的话，FPS 就会下降影响程序顺滑度，而如果改成这样
$('div').on('touchmove', function(){
   setTimeout(function(){
     //.….code
   },0);
});
```

https://developers.google.cn/web/fundamentals/design-and-ux/input/touch/#_7

# android与ios滚动条

ios滚动条自动隐藏，不占据物理空间像素

android滚动条占据约1像素，具体看浏览器滚动条样式尺寸

# new Image

```javascript
var img = new Image();
// new Image 的 onload 在部分android webview不会执行
// 需要将new Image 换成 document.createElement('img');
img.onload = function() {
    console.log('image load success');
}
img.src = '';
```



# 移动端html5 audio autoplay失效

由于自动播放网页中的音频或者视频，会给用户带来一些困扰或者不必要的流量消耗，所以苹果系统和安卓系统通常都会禁止自动播放和使用 JS 的触发播放，必须由用户来触发才可以播放

解决方法思路：先通过用户 touchstart 触碰，触发播放并暂停（音频开始加载，后面用 JS 再操作就没问题了）

```javascript
document.addEventListener('touchstart', function () {
    document.getElementsByTagName('audio')[0].play();
    document.getElementsByTagName('audio')[0].pause();
});
```

# css实现原生效果1px线框

```css
.border-1px {
    position: relative;
}
.border-1px:after {
    position: absolute;
    content: '';
    top: -50%;
    bottom: -50%;
    left: -50%;
    right: -50%;
    -webkit-transform: scale(0.5);
    transform: scale(0.5);
    border-top: 1px solid #666;
    border-bottom: 1px solid #666;
}
```

# 移动手势表

{% image 手势操作表.jpg 示例图 %}

原文：https://github.com/AlloyTeam/Mars/blob/master/issues

# js double数字计算问题

```javascript
// javascript中，带小数的浮点型数据运算都是以2进制计算的
1.2 + 1.4 = 2.5999999999999996
// 这个时候不得不这样使用
(1.2*10 + 1.4*10) / 10 = 2.6
```

# a超链接访问过后，样式混乱，hover样式不在出现，最好按一下排序 L-V-H-A

```css
a:link{}	a:visited{}	a:hover{}	a:active{}
```

# new Date()

​	参数是日期字符串格式，需要是`YYYY/MM/DD`，如 `new Date('2020/11/19')`
​	`YYYY-MM-DD`在android可以转换为正确的日期，在ios不行

# parseInt

​	ES5之前，没有第二个参数来指定转换的基数，会根据第一个字符来自行决定基数；
如果第一个字符是x或者X，则转为十六进制数字；如果是0，则转为八进制数字。
​	从ES5开始，`parseInt()` 默认转换为十进制数

# ES5-Shim

https://github.com/es-shims/es5-shim

一个完整的`shim/polyfill`集合，能够为项目提供`ES5`基本规范支持

# ES6-Shim

https://github.com/es-shims/es6-shim

提供了对ES6基本规范的支持

# Traceur

https://github.com/google/traceur-compiler/wiki/GettingStarted

实现旧语法之间的转换