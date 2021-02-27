---
title: css 
date: 2020-11-18 09:45:10
categories:
    - 布局
tags:
    - css
---



# float、position布局

​	css3之前多数是使用float、position进行布局，存在的问题也很明显，整体高度适应、元素底部对齐、多出空白区域；

- 浮动元素的宽度设定百分比，最终计算结果在不同平台上的结果不一样（有的浏览器向上取整，有的向下取整），有些时候某些区块会跑到其他区块底下，有时这些区块一侧又会莫名出现一块明显的间隙。
- 通常都要清除浮动，才能避免父盒子/元素折叠。

# 多栏布局

​	通过`column-count`属性将一个元素的内容分为多栏显示，确保个栏内容的底部对齐；需要指定宽度，如果栏`column-width`不设定宽度，需要容器设定宽度，否则将被浏览器视为未设定。

- `column-count` 栏数
- `column-width` 每一栏的宽度
- `column-gap` 栏与栏之间的间隔距离
- `column-rule` 栏与栏之前的间隔线，与`border`属性相同

```html
<style>
    div {
        width: 40em;
        column-count: 2;
    }
</style>
<div>
    <img src="xxx.jpg">
    <p>长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。
    </p>
    <p>长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。长文字，相对来说比较长的示例文字。
    </p>
</div>
```



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

# flex弹性盒布局

## display: flex | inline-flex

​	设定为`flex`布局后，子元素的`float`、`clear`、`verical-align`属性将失效，所有子元素自动成为容器的成员，简称项目；容器存在两根轴，水平的主轴（main axis）和垂直的交叉轴（cross axios），下面简称为x、y轴；主轴的开始位置（与边框的交叉点）叫做`mian start`，结束位置`main end`；交叉轴的开始位置`cross start`，结束位置 `cross end`；项目默认沿主轴排列，单个项目占据的主轴空间`main size`，占据的交叉轴空间`cross size`。

## 容器属性

| 属性                                                         | 值                                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `flex-direction` x轴项目的排列方向                           | `row` 从左往右水平排列（默认值）<br />`row-reverse` 从右往作水平排列<br />`column` 从上往下垂直排列<br />`column-reverse` 从下往上垂直排列 |
| `flex-wrap` 项目排布方式（如果一条轴线排不下，换行方式）     | `nowrap` 不换行（默认）<br />`wrap` 换行<br />`wrap-reverse` 倒序换行 |
| `flex-flow` 是`flex-direction`和`flex-wrap`的简写            | `row nowrap`（默认值）                                       |
| `justify-content` 主轴（x）上的对齐方式                      | `flex-start` 左对齐（默认值）<br />`flex-end` 右对齐<br />`center` 居中<br />`space-between` 两端对齐，项目之间的间隔相等<br />`space-around` 每个项目两侧的间隔相等（项目之间的间隔与边框的间隔大一倍） |
| `align-item` 交叉轴（y）上的对齐方式                         | `flex-start` 与y轴起点对齐<br />`flex-end` 与y轴终点对齐<br />`center` 与y轴中点对齐<br />`baseline` 项目第一行文字的基线（文字底线）对齐<br />`stretch` 如果项目未设置高度或为auto，将占满整个容器高度（默认值） |
| `align-conten` 多根轴线的对其方式，如果只有一根轴线，该属性无效 | `flex-start` 与y轴起点对齐<br />`flex-end` 与y轴终点对齐<br />`center` 与y轴的中点对齐<br />`space-between` 与y轴两端对齐，轴线之间的间隔平均分布<br />`space-around` 每根轴线两侧的间隔都相等，轴线之间的间隔比轴线与变宽的间距大一倍<br />`stretch` 轴线占满整个交叉轴（默认值） |

## 项目的属性

| 属性                                                         | 值                                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `order: <integer>` 排列序号，数值越小，排列越靠前            | 整数，默认0                                                  |
| `flex-grow: <number>`项目放大比例                            | 数字，默认0 === 如果存在剩余空间，也不放大；1占满剩余空间，当 `flex-grow: 1` 元素的尺寸可能无法适应overflow的尺寸限制，可以把`width设为0`来适应当前剩余空间 |
| `flex-shrink: <number>`项目缩小比例                          | 数字，默认1 === 空间不足，将缩小；如果所有项目的`flex-shrink`都为1，当空间不足，将等比缩小；如果一个项目的`flex-shrink`为0，其它为1，当空间不足，前者不缩小 |
| `flex-basis: <length>`分配多余空间之前，项目占据的x轴空间；浏览根据这个属性，计算x轴是否有多余空间 | `auto`（默认值） === 项目本来大小<br />可以跟`width`、`height`设定一样的固定值（`200px`）说明项目占据固定空间 |
| `flex`是`flex-grow`、`flex-shrink`、`flex-basis`的简写       | `0 1 auto`（默认值），后两个属性可选                         |
| `align-self`允许单个项目与其它项目不一样的对齐方式，可覆盖`align-items` | `auto`（默认值）=== 继承父元素的align-item属性；如果没有父元素，等同于`stretch`，其它值与`align-item`一样 |



# 图片宽度适配

`img { max-width: 100%; }`很有效地处理图片宽度与容器宽度适配的问题，为什么不用width？因为用width之后，图片将按照容器宽度展示，忽略自身原有宽度，即便是logo类一样的小图，也会直接按照容器宽度来适配显示。

# 响应式图片

## 通过srcset切换分辨率

​	假设一张图片有三种分辨率版本，一张小的针对屏幕，一种中等的针对中等屏幕，一张大的针对其他所有屏幕；`src`指定1倍大小的小图片，且在不支持srcset的浏览器中用作后备，所以才用src指定最小图片，让旧浏览器以最快的速度获取；对于支持srcset的浏览器，通过逗号分隔符的图片描述，让浏览器决定选择，下例中的1.5x和2x数字可以是任意整数。*（问题：1440px宽，1x的屏幕会拿跟480px宽、3x屏幕相同的图片，这或许不是想要的结果）*

```html
<img src="scones_samll.jpg" srcset="scones_medium.jpg 1.5x, scones_large.jpg 2x" alt="Scones taste amazing">
```

## srcset、sizes联合切换

​	在响应式设计中，经常看到图片在小屏幕中全屏显示，在大屏幕只显示一半宽；下例中`srcset`添加了w后缀，告诉浏览器图片有多宽，但并不是真实大小，只是对浏览器的一个提示，大致相当于`css`像素大小。`sizes`第一个值告诉浏览器最小宽度为`17em`的设备，想让图片宽度约为`100vw`；第二个值告诉浏览器，如果设备宽度大于等于`40em`，让图片显示`50vw宽`

```html
<img srcset="scones-small.jpg 450w, scones-medium.jpg 900w" sizes="(min-width: 17em) 100vw, (min-width: 40em) 50vw" src="sconessmall.jpg" alt="Scones">
```

## picture元素

​	一个容器，为img指定图片提供便利；`picture`中`img`标签必须提供

```html
<picture>
  <!-- 如果屏幕大于等于30em，替换成cake-table.jpg -->
	<source media="(min-width: 30em)" srcset="cake-table.jpg">
  <source media="(min-width: 60em)" srcset="cake-shop.jpg">
  <img src="scones.jpg" alt="One Way or another, you Will get cake.">
</picture>


<!-- type图片新格式，通常用于指定视频来源 -->
<!-- 如果浏览器支持webp格式图片就显示webp，不支持显示img -->
<picture>
	<source type="image/webp" srcset="scones-baby-yeah.webp">
  <img src="scones-baby-yeah.jpg" aly="again, you will eat cake.">
</picture>
```

# viewport

浏览器中用于呈现网页区域叫viewport视口），viewport通常并不等同于屏幕大小，尤其是可以缩放浏览器窗口的情况下。

```html
<!-- 告诉移动浏览器使用真实的页面比例，不允许缩放user-scalable=no -->
<meta name="viewport" content="initial-scale=1.0, user-scalable=no">

<!-- 告诉移动浏览按照设备宽度device-width渲染网页内容 -->
<meta name="viewport" content="width=device-width">

<!-- 允许用户将页面放大到设备宽度的三倍，最小可以缩小到设备宽度的一半 -->
<meta name="viewport" content="width=device-width, maximum-scale=3, minimum-scale=0.5">
```

# 媒体查询

​	分割媒体查询的好处：针对性强，分别对应不同的样式；
​	坏处有：多一个文件就要多一次HTTP请求，HTTP请求多了会明显影响页面加载速度。

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

/* @import 与媒体查询 */
@import url('phone.css') screen and (max-width: 360px);
```

```html
<head> 
 ... 
 <!-- Use this stylesheet to display the page onscreen. --> 
 <link rel="stylesheet" media="screen" href="styles.css"> 
 <!-- Use this stylesheet to print the page. --> 
 <link rel="stylesheet" media="print" href="print_styles.css"> 
 <!-- 组合媒体查询 -->
 <link rel="stylesheet" media="screen and (orientation: portrait) and (min-width: 800px)" href="800width-portait-screen.css">
</head>
```

## @media 设备类型 and  (设备特性)

| 设备值     | 设备类型                                       |
| ---------- | ---------------------------------------------- |
| all        | 所有设备                                       |
| screen     | 电脑显示器                                     |
| print      | 打印用纸、打印预览图                           |
| handheld   | 便携设备                                       |
| tv         | 电视机类型设备                                 |
| speech     | 语音和音频合成器                               |
| braille    | 盲人用点字法触觉回馈设备                       |
| embossed   | 盲文打印机                                     |
| projection | 各种投影设备                                   |
| tty        | 使用固定密度字母栅格的媒介，如电传打字机和终端 |

| 特性                | 可指定值                            | 是否允许使用min、max前缀 | 特性说明                                                     |
| ------------------- | ----------------------------------- | ------------------------ | ------------------------------------------------------------ |
| width               | 带单位的长度数值，如600px           | 允许                     | 浏览器窗口宽度                                               |
| heigiht             | 带单位的长度数值，如600px           | 允许                     | 浏览器窗口高度                                               |
| device-width        | 带单位的长度数值，如600px           | 允许                     | 设备屏幕分辨率宽度值                                         |
| device-height       | 带单位的长度数值，如600px           | 允许                     | 设备屏幕分辨率高度值                                         |
| orientation         | porttait（纵向）、landspace（横向） | 不允许                   | 浏览器窗口纵横方向，当窗口高度大于宽度，为portrain（纵向），否则landspace（横向） |
| aspect-ratio        | 比例值，如16/9                      | 允许                     | 浏览区窗口纵横比，比例值为浏览器窗口宽度/高度                |
| device-aspect-ratio | 比例值，如16/9                      | 允许                     | 屏幕分辨率纵横比，比例值为设备屏幕分辨率宽度值/高度值        |
| color               | 整数值                              | 允许                     | 设备使用多少位的颜色值，如果不是彩色设备，值为0              |
| color-index         | 整数值                              | 允许                     | 色彩表中的色彩数                                             |
| monochrome          | 整数值                              | 允许                     | 单色帧缓冲器中每像素的字节数                                 |
| resolution          | 分辨率值，如300dpi                  | 允许                     | 设备的分辨率                                                 |
| scan                | progressive、interlace              | 不允许                   | 电视机类型设备的扫描方式，<br />progressive：逐行扫描<br />interlace：隔行扫描 |
| grid                | 0、1                                | 不允许                   | 设备是基于栅格1还是位图0                                     |

