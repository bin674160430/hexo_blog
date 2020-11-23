---
title: css 
date: 2020-11-18 09:45:10
categories:
    - 布局
tags:
    - css

---



# 流式布局

​	一个等比缩放布局，尽可能地占用所有可用空间，将网页分成几栏，应用比例设定宽度，应对窗口大小缩放的最简单方案。（因为等比缩放布局的内容始终会像水一样，平滑地充满所有空间，所以称为流式布局）

```html
<body>
  <div style="width: 28.6%; float: left;">
    ...
  </div>
  <div style="width: 71.8%; float: left;">
    ...
  </div>
</body>
```

## box-sizing

​	通常，边框会被加在元素外围，在计算布局时就要剪掉边框占用的空间；使用`box-sizing: border-box`边框就位于盒子内侧，盒子的计算方式不用，不影响元素的尺寸，比如宽度是60%，无论边框多粗，宽度始终都是60%

## calc()

​	如果需要混合使用比例和固定单位，假设想把一栏宽度设定为67%，减去5像素（的外边距），可以这样使用`calc(67%-5px)`

注意：IE8以下都不支持，Firefox需要加入`-moz-`，较早的android chrome和safari需要加入`-webkit-`

## 流式图片

​	图片占用的空间取决于其内容，如果窗口太小，图片就会撑开所在的栏，挤掉其他元素，需要限制图片的宽度为容器的最大宽度

```css
img {
  max-width: 100%;
}
/* 如果想给图片周围加一点外边距，必须给`margin-left, margin-right`设定百分比，而且要保证`max-width`等于100%（不能超） */
img {
  max-width: 90%;
  margin-left: 10%;
}
```

## 流式排版

​	百分比，em：都是让文字相对于浏览器默认的文字大小缩放，`110%, 1.1em`的结果一样，常规文字大10%

```css
body {
  font-size: 100%;
}
/*
	百分比、em受容器影响, 看下面div是1.1em放大110%, h1放大200%
	实际上，div已经放大了110%，h1是在这个基础上再放大200%，最终是默认字体的220% (1.1*2)
*/
div {
  font-size: 1.1em;
}
div > h1 {
  font-size: 2em;
}
```

```css
/*
	rem设定的文字大小始终相对于html元素的文字大小计算，不管嵌套在哪一级, h1始终是默认文字的200%
*/
div {
  font-size: 1.1rem;
}
div > h1 {
  font-size: 2rem;
}
```

# viewport

```html
<meta content="initial-scale=1.0" name="viewport">
```

这段代码告诉移动浏览器使用真实的页面比例，不要缩放，单相同的页面在不同的移动设备上，显示将会不同，如下图所示

{% image 1.jpg 图片 %}

# 媒体查询

```css
@media (media-feature-name: value) {
  /*符合条件的样式*/
}
/*
	常用媒体特性
	widht, min-width, max-width
	height, min-height, max-height
	// 设备屏幕或者打印纸面宽度
	device-width, min-device-width, max-device-width
	device-height, min-device-height, max-device-height
	// 方向
	orientation: landscape(横向) portrait(纵向)
	// 宽高比 1/1 正方形，16∶9的宽屏显示器可以写成aspect-ratio:16/9
	device-aspect-ratio, min-device-aspect-ratio, max-device-aspect-ratio
	// 屏幕或打印分辨率，min-resolution: 300dpi，也可以接受每厘米多少点，比如min-resolution: 118dpcm
	resolution
	// 颜色位深，1黑白，目前主流浏览器都是24，每个像素可以显示百万种颜色，检测是否支持彩色输出，支持的颜色数量
	color, min-color, max-color
*/
@media (max-width: 480px) {
  /* 当浏览器窗口缩小到480像素，样式生效 */
}

@media (not max-width: 600px) and (max-width: 700px) { 
 /* 600px - 700px. */ 
}
@media (min-width: 600px) and (max-width: 700px) { 
 /* 600px - 700px. */ 
}
```

```html
<head> 
 ... 
 <!-- Use this stylesheet to display the page onscreen. --> 
 <link rel="stylesheet" media="screen" href="styles.css"> 
 <!-- Use this stylesheet to print the page. --> 
 <link rel="stylesheet" media="print" href="print_styles.css"> 
</head>
```

