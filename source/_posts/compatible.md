---
title: 兼容
date: 2020-07-02 18:42:10
categories:
    - 前端
tags:
    - 兼容
---

# IE和其他浏览器不一样的特性

- ie的引擎是Trident，MSHTML
- Trident内核曾经几乎与w3c标准脱节(2005年)
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