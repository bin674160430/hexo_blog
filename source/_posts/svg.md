---
title: css 
date: 2021-02-26 08:24:50
categories:
    - 图像
tags:
    - svg

---

# SVG

​	SVG是XML[XML1.0]中用于描述而为图形的语言，支持三种图形对象：矢量图形形状（如有直线和曲线组成的路径）、图像和文本。
​	SVG允许在代码中使用矢量点来描述二维图像，这种优势被广泛应用到图标、线条图和图标的展示中。
​	由于矢量图是使用相对点来保存数据的，所以缩放到任何大小都不会损失清晰度，相比同等尺寸的jpeg、gif、png文件大小更小。
​	*（andorid2.3和ie9以上都支持，ie不允许通过css为svg添加动画，只能用JavaScript实现）*

# SVG根元素

`viewBox="min-x min-y width height"`视框，定义了SVG中所有形状需要遵循的坐标系，min-x、y用于描述左上角位置。

```html
<svg width="198px" height="188px" viewBox="0 0 198 188"></svg>
```

## 命名空间

`xmlns:sketch="http://www.bohemlancoding.com/sketch/ns"`（xmlns是XML命名空间的缩写）这些命名空间往往只是在生成SVG的程序中使用，在WEB展示SVG的时候不是必须的，所以在优化流程中，为了减少SVG大小，通常会把它们去掉。

## 标题和描述标签

提高SVG文档的可读性，可以用来图像不可见的情况下描述图像内容，当SVG被应用背景图的时候，可以去除这些标签减少文件大小。

```html
<title>start</title>
<desc>create with sketch</desc>
```

## defs

`<defs></defs>`尽管是空的标签，但仍然是很重要的元素，用于储存所有可以复用元素定义的地方，如梯度、符号、路径等。

## g

g元素能把其他元素捆绑在一起，如画一辆车的SVG，把用来构成车轮的形状用g标签集合起来，g标签中的命名空间，有助于图形编辑软件再次打开该图像，对在其他地方展示并没有影响

```html
<g id="page" stroke="none" stroke-width="1" fill="none" fillrule="evenodd" sketch:type="MSPage"></g>
```

## 形状元素

`path`、`rect`、`circle`、`ellipse`、`line`、`polyline`、`polygon`

```html
<polygon id="star" stroke="#989898" stroke-width="3" fill="#f8f8f8" sketch:type="MSShapeGroup" points="99 154 40.2214748 184.901699 
51.4471742 119.45085 3.89434837 73.0983006 69.6107374 63.5491503 99 
4 128.389263 63.5491503 194.105652 73.0983006 146.552826 119.45085 
157.778525 184.901699"></polygon>
```

# 利用SVG图标服务

https://icomoon.io

# Web页面中插入SVG

- 通过img标签插入
- 通过object标签插入
- 设置为背景图像
- 内联

| 特征            | img标签插入 | object标签插入              | 内联                                            | 背景图片 |
| --------------- | ----------- | --------------------------- | ----------------------------------------------- | -------- |
| SMIL动画        | Y           | Y                           | Y                                               | Y        |
| 外部css控制     | N           | 必须在svg文件里引入该样式表 | Y                                               | N        |
| 内部css修改     | Y           | Y                           | Y                                               | Y        |
| JavaScript控制  | N           | Y                           | Y                                               | N        |
| 缓存            | Y           | Y                           | 外部资源可以缓存，IE默认不支持                  | Y        |
| SVG内部媒体查询 | Y           | Y                           | 媒介查询作用是所在文档的大小（而非SVG本身大小） | Y        |
| 使用use         | N           | Y                           | Y                                               | N        |



```html
<!-- img -->
<img src="mySconeVector.svg" alt="amazing line art of a scone">

<!-- object, data:链接svg资源的方式，type:MIME类型，还可以添加width、height约束大小 -->
<object data="img/svgfile.svg" type="image/svg+xml">
  <span class="fallback-info">Your browser doesn't support SVG</span>
</object>

<!-- 背景图像插入 -->
<style>
  .div {
    background-image: url('image.svg');
  }
</style>

<!-- 插入svg标签 -->
<svg width="198" height="188" viewBox="0 0 198 188" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
	<title>star</title>
  <g fill="none" fill-rule="evenodd">
  	<path stroke="#979779" strokewidth="3" fill="#f8f81c" d="M99 154l-58.78 30.902 11.227-65.45L3.894 
73.097l65.717-9.55L99 4l29.39 59.55 65.716 9.548-47.553 46.353 11.226 
65.452z"></path>
  </g>
</svg>
```

## SMIL动画

`synchronized multimedia integration language`一种在SVG文档内部定义SVG动画的方法，**IE并不支持SMIL，CHrome放弃SMIL**

```html
<g fill="none" fill-rule="evenodd">
  <!-- SMIL动画，2秒内把星形从黄色渐变为绿色 -->
	<animate xlink:href="#star_Path" attributeName="fill" attributeType="XML" begin="0s" dur="2s" fill="freeze" from="#f8e81c"></animate>
  <path stroke="#979797" stroke-width="3" fill="#f8e81c" d="M99 154l-58.78 30.902 11.227-65.45L3.894 
73.097l65.717-9.55L99 4l29.39 59.55 65.716 9.548-47.553 46.353 11.226 
65.452z"></path>
</g>
```

## 外部样式表

```html
<!-- 通常在defs中添加 -->
<link href="styles.css" type="text/css" rel="stylesheet">

<!-- ?xml是官方做法-->
<?xml-stylesheet href="style.css" type="text/css"?>
<svg width="198" height="188" viewBox="0 0 198 188" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"></svg>
```

## 内联样式

SVG样式属性：https://www.w3.org/TR/SVG/styling.html

```html
<defs>
	<style type="text/css">
  	<![CDATA[
    	#star_Path {
        stroke: red;
    	}
    ]]>
  </style>
</defs>
```

## 动画效果SVG

```html
<div class="wrapper">
  <svg width="198" height="188" viewBox="0 0 220 200" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  	<title>start</title>
    <defs>
    	<style type="text/css">
        	<![CDATA[
          @keyframes spin {
            0% {
              transform: rotate(0deg);
            },
            100% {
              transform: rotate(360deg);
            }
          }
          .star_Wrapper {
						animation: spin 2s 1s;
            transform-origin: 50% 50%;
          }
        	.wrapper {
						padding: 2rem;
            margin: 2rem;
          }
        	]]>
      </style>
      <g id="shape">
        <path fill="#14805e" d="M50 50h50v50H50z"></path>
        <circle fill="#ebebeb" cx="50" cy="50" r="50"></circle>
      </g>
    </defs>
    <g ckass="star_Wrapper" fill="none" fill-rule="evenodd">
    	<path id="star_Path" stroke="#333" stroke-width="3" fill="#f8f81c" d="M99 154l-58.78 30.902 11.227-65.45L3.894 
73.097l65.717-9.55L99 4l29.39 59.55 65.716 9.548-47.553 46.353 11.226 
65.453z"></path>
    </g>
  </svg>
</div>
```

## 使用JavaScript添加SVG动画

# 利用符号复用图形对象

```html
<svg display="none" width="0" height="0" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <defs>
    <!-- 定义了一个形状以供后面使用 -->
  	<symbol id="icon-drag-left-right" viewBox="0 0 1344 1024">
      <title>drag-left-right</title>
      <path d="M256 192v-160l-224 224 224 
224v-160h256v-128z"></path>
    </symbol>
  </defs>
</svg>

<svg class="icon-drag-left-right">
  <!-- use 结合 symbol id 引入对象 -->
  <!-- use可以复用所有svg内容：梯度、形状、符号等 -->
	<use xlink:href="#icon-drag-left-right"></use>
</svg>
```

# 颜色

设置`color`、`fill`设定颜色

# 复用外部图形对象资源

`herf`，链接到外部`svg`文件（`defs.svg`），然后确定文件使用的符号`id`，资源将会被浏览器缓存（和其他外部图像一样），并且防止我们的代码被一堆SVG符号定义塞满；不足的地方是和直接内联`defs`不一样，对`defs.svg`的变动（如`JavaScript`改变其中一条路径）不会被更新到`use`标签中。

# 滤镜

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
	<defs>
  	<filter id="myfilter" x="0" y="0">
      	<feColorMatrix in="SourceGraphic" type="hubRotate" values="90" result="A" />
      	<feGaussianBlur in="A" stdDeviation="6"/>
    </filter>
  </defs>
</svg>
```



# 优化SVG

自动化压缩工具：

- SVGO：https://github.com/svg/svgo
- SVGOMG：https://jakearchibald.github.io/svgomg