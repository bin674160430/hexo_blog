---
title: css 
date: 2020-06-30 11:17:10
categories:
    - 样式
tags:
    - css
---



# 介绍一下标准的CSS盒子模型，与低版本的ie盒子模型有什么不同？

`css盒子模型`由`外边距 margin`，`内边距 padding`，`边界 border`，`内容区 width+height`四个属性组成

标准css盒子模型和低端ie盒子模型不同，宽高不一样；标准css盒子模型的宽高就是内容区宽高；低端ie盒子模型宽高是内边距+边界+内容区

{% image css-box.jpg 图片 %}

# CSS选择器有哪些？哪些属性可以继承？优先级算法怎么计算？

- `#id` id选择器
- `.class` 类选择器
- `div, p` 标签选择器
- `div+p` 相邻选择器
- `ul>li` 子选择器
- `ul li` 后代选择器
- `*` 通配符选择器
- `input[type=text]` 属性选择器
- `a:hover, li:nth-child` 伪类选择器

可继承的属性：`font-size, font-framily, color, ul, li, dl, dd, dt`

优先级算法计算：

1. 优先级就近原则，同权重情况下样式定义最近者为准
2. 载入样式以最后载入的定位为准
3. `!important`> `id`>`class`>`tag`
4. `important`比内联优先级高，但内联比`id`高

# css3新增的伪类

`:first-child, :first-of-type, :last-child, :last-of-type, :only-child, :only-of-type, :nth-child(n), :nth-last-child(n), :nth-of-type, :nth-last-of-type`

`input:enabled`

# css3有哪些新特性？

- css3实现圆角`border-radius`, 阴影`box-shadow`
- 文字特效`text-shadow`，线性渐变`linear-gradient`，强制文本换行`word-wrap` 边框图片`border-image`
- 旋转、缩放、定位、倾斜`transform`
- 增加了更多的选择器、多背景、rgba
- 在css3中唯一引入的伪元素是`::selection`
- 媒体查询`@media`, `flex`盒子弹性布局

# 用纯CSS实现三角形是什么原理？

让块级元素的宽高为0，相当于设置了一个中心点，然后设置`border-width`来控制三角形，起始点就相当于一个点

```css
.triangle {
    display: block;
    width: 0;
    height: 0;
    border: 10px solid transparent;
    border-top-color: red;
    border-right-color: yellow;
    border-bottom-color: blue;
    border-left-color: black;
}
```

# position

默认值： `static`

相对定位: `relative`

绝对定位: `absolute`, `fixed`

# relative 和 absolute的定位原点是？

`relative` 定位原点是元素本身所在的位置

`absolute` 定位原点离自己元素最近的`absolute`或者`relative`的父元素的左上角为原点

# 解释一下css3的FLexbox弹性盒布局模型以及适用场景？

设置`display: flex;`作为容器，存在水平主轴`main axis`和垂直的交叉轴`cross axis`，主轴的开始位置（与边框的交叉点）为`main start`，结束位置为`main end`；交叉轴的开始位置为`cross start`，结束位置为`cross end`；子集元素自动成为`flex item`项目，项目默认沿主轴排列，单个项目占据的主轴空间叫做`main size`, 占据的交叉轴叫做`corss size`，项目中的`float, clear, vertical-align`属性将失效，适用于移动端、当代浏览器，ie10+

## 容器属性：

`flex-direction: row|row-reverse|column|column-reverse` 主轴方向，项目的排列方向，水平、垂直方向

`flex-wrap: nowrap|wrap|wrap-reverse`项目排满的情况下换行方式

`flex-flow: row nowrap`flex-direction 和 flex-wrap 属性简写

`justify-content: flex-start|flex-end|center|space-between|space-around`项目主轴对齐方式，start沿着主轴起点对齐；end沿着主轴终点对齐；center居中；space-between两端对齐，项目之间的间隔相等；space-around: 每个项目两侧的间隔相等

`align-items: stretch|flex-start|flex-end|center|baseline`项目交叉轴对齐方式，start起点对齐；end终点对齐；center中点对齐；baseline项目第一行文字的基线对齐；stretch默认值，项目高度没有设置成auto将占满整个容器高度

`align-content: stretch|flex-start|flex-end|center|space-between|space-around`定义多个轴线的对齐方式，如果项目只有一个轴线，不起作用

## 项目属性：

`order: 0`排列顺序，数值越小越靠前

`flex-grow: 0`放大比例，默认0，存在剩余空间，也不放大，如果所有项目`flex-grow`都是1，将平分盒子剩余空间；如果一个项目的`flex-grow:2`，其他都是1，那么前者占据的剩余空间比其他项多一倍

`flex-shrink: 1`缩小比例，如果空间不足，项目缩小，如果所有项目的`flex-shrink: 1`，空间不足时，都将等比缩小；如果一个项目的`flex-shrink: 0`，其他都是1，那么空间不足时，前者不缩小

`flex-basis: auto`定义在分配多余空间之前，项目占据的主轴空间`main size`，浏览器根据这个属性，计算主轴是否有多余空间，默认值`auto`，即项目本来的大小

`flex: 0 1 auto` `flew-grow, flex-shrink, flex-basis`的简写，后面两个属性可选

`align-self: auto`运行单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`，默认`auto`继承父元素的`align-items`属性，如果没有父元素，等同于`stretch`


# css sprite 是什么,优缺点?

将多个小图片拼接到一个图片中。通过 background-position 和元素尺寸调节需要显示的背景图案。

优点：

1. 减少 HTTP 请求数，极大地提高页面加载速度
2. 增加图片信息重复度，提高压缩比，减少图片大小
3. 更换风格方便，只需在一张或几张图片上修改颜色或样式即可实现

缺点：

1. 图片合并麻烦
2. 维护麻烦，修改一个图片可能需要重新布局整个图片，样式

# *display: none;*与*visibility: hidden;*

联系：它们都能让元素不可见

区别：

1. `display:none;`会让元素完全从渲染树中消失，渲染的时候不占据任何空间；`visibility: hidden;`不会让元素从渲染树消失，渲染时元素继续占据空间，只是内容不可见。
2. `display: none;`是非继承属性，子孙节点消失由于元素从渲染树消失造成，通过修改子孙节点属性无法显示；`visibility: hidden;`是继承属性，子孙节点由于继承了 `hidden` 而消失，通过设置 `visibility: visible`，可以让子孙节点显示。
3. 修改常规流中元素的 `display` 通常会造成文档重排。修改 `visibility` 属性只会造成本元素的重绘。
4. 读屏器不会读取 `display: none;`元素内容；会读取 `visibility: hidden;`元素内容。

# *display: block;*和*display: inline;*

`block`元素特点：

1.处于常规流中时，如果`width`没有设置，会自动填充满父容器 

2.可以应用`margin/padding` 

3.在没有设置高度的情况下会扩展高度以包含常规流中的子元素 

4.处于常规流中时布局时在前后元素位置之间（独占一个水平空间） 

5.忽略`vertical-align`

`inline`元素特点

1.水平方向上根据`direction`依次布局 

2.不会在元素前后进行换行 

3.受`white-space`控制

4.`margin/padding`在竖直方向上无效，水平方向上有效 

5.`width/height`属性对非替换行内元素无效，宽度由元素内容决定 

6.非替换行内元素的行框高由`line-height`确定，替换行内元素的行框高由`height`,`margin`,`padding`,`border`决定 

6.浮动或绝对定位时会转换为`block` 

7.`vertical-align`属性生效

# specified value,computed value,used value 计算方法

- `specified value:` 计算方法如下：
  1. 如果样式表设置了一个值，使用这个值
  2. 如果没有设值，且这个属性是继承属性，从父元素继承
  3. 如果没有设值，并且不是继承属性，则使用 css 规范指定的初始值
- `computed value`: 以 `specified value` 根据规范定义的行为进行计算，通常将相对值计算为绝对值，例如 `em` 根据 `font-size` 进行计算。一些使用百分数并且需要布局来决定最终值的属性，如 `width`，`margin`。百分数就直接作为 computed value。line-height 的无单位值也直接作为 `computed value`。这些值将在计算 used value 时得到绝对值。**computed value 的主要作用是用于继承**
- `used value`：属性计算后的最终值，对于大多数属性可以通过 window.getComputedStyle 获得，尺寸值单位为像素。以下属性依赖于布局，
  - `background-position`
  - `bottom, left, right, top`
  - `height, width`
  - `margin-bottom, margin-left, margin-right, margin-top`
  - `min-height, min-width`
  - `padding-bottom, padding-left, padding-right, padding-top`
  - `text-indent`

# 容器包含若干浮动元素时如何清理(包含)浮动

1. 容器元素闭合标签前添加额外元素并设置`clear: both`
2. 父元素触发块级格式化上下文(见块级可视化上下文部分)
3. 设置容器元素伪元素进行清理[推荐的清理浮动方法](http://nicolasgallagher.com/micro-clearfix-hack/)

```css
/**
* 在标准浏览器下使用
* 1 content内容为空格用于修复opera下文档中出现
*   contenteditable属性时在清理浮动元素上下的空白
* 2 使用display使用table而不是block：可以防止容器和
*   子元素top-margin折叠,这样能使清理效果与BFC，IE6/7
*   zoom: 1;一致
**/

.clearfix:before,
.clearfix:after {
    content: " "; /* 1 */
    display: table; /* 2 */
}

.clearfix:after {
    clear: both;
}

/**
* IE 6/7下使用
* 通过触发hasLayout实现包含浮动
**/
.clearfix {
    *zoom: 1;
}
```

# 什么是 FOUC?如何避免

`Flash Of Unstyled Content`：用户定义样式表加载之前浏览器使用默认样式显示文档，用户样式加载渲染之后再从新显示文档，造成页面闪烁。**解决方法**：把样式表放到文档的`head`



# 如何创建块级格式化上下文(block formatting context),BFC 有什么用

创建规则：

1. 根元素
2. 浮动元素（`float`不是`none`）
3. 绝对定位元素（`position`取值为`absolute`或`fixed`）
4. `display`取值为`inline-block`,`table-cell`, `table-caption`,`flex`, `inline-flex`之一的元素
5. `overflow`不是`visible`的元素

作用：

1. 可以包含浮动元素
2. 不被浮动元素覆盖
3. 阻止父子元素的 margin 折叠



# stacking context（层叠上下文），布局规则

z 轴上的默认层叠顺序如下（从下到上）：

1. 根元素的边界和背景
2. 常规流中的元素按照 html 中顺序
3. 浮动块
4. positioned 元素按照 html 中出现顺序

如何创建 stacking context：

1. 根元素
2. z-index 不为 auto 的定位元素
3. a flex item with a z-index value other than 'auto'
4. opacity 小于 1 的元素
5. 在移动端 webkit 和 chrome22+，z-index 为 auto，position: fixed 也将创建新的 stacking context



# 如何水平居中一个元素

- ###### 居中的元素为**inline 元素**，为父元素设置`text-align: center;`

  ------

  

- 居中的元素为**block 元素**
  1）为元素设置宽度
  2）设置左右 margin 为 auto
  3）IE6 下需在父元素上设置`text-align: center;`

{% image horizontal.jpg 示例图 %}

```html
<body>
    body
    <h1>水平居中</h1>
    <div class="content">
        content
    </div>
</body>

<style>
    body {
        text-align: center; /* 3 */
    }
    .content {
        width: 500px;      /* 1 */
        text-align: left;  /* 3 */
        margin: 0 auto;    /* 2 */
        color: white;
        padding: 30px 15px;
        background: purple;
    }
</style>
```

------



- 如果需要居中的元素为**浮动元素**
  1）为元素设置宽度
  2）`position: relative;`
  3）浮动方向偏移量（left 或者 right）设置为 50%
  4）浮动方向上的 margin 设置为元素宽度一半乘以-1

  {% image horizontal-1.png 示例图 %}

```html
<body>
    <div class="content1">
    	aaaaaa aaaaaa a a a a a a a a
    </div>
</body>

<style>
    .content1 {
        width: 500px;         /* 1 */
        float: left;

        position: relative;   /* 2 */
        left: 50%;            /* 3 */
        margin-left: -250px;  /* 4 */

        background-color: purple;
    }
</style>
```

------



- 如果需要居中的元素为**绝对定位元素**
  1）为元素设置宽度
  2）偏移量设置为 50%
  3）偏移方向外边距设置为元素宽度一半乘以-1

{% image horizontal-2.png 示例图 %}

```html
<body>
    <div class="content2">
    aaaaaa aaaaaa a a a a a a a a
    </div>
</body>

<style>
    body {
        position: relative;
    }
    .content2 {
        width: 800px;
        position: absolute;
        left: 50%;
        margin-left: -400px;
        background-color: purple;
        color: white;
        padding: 30px 15px;
    }
</style>
```

------



- 如果需要居中的元素为**绝对定位元素**
  1）为元素设置宽度
  2）设置左右偏移量都为 0
  3）设置左右外边距都为 auto

{% image horizontal-3.png 示例图 %}

```html
<body>
    <div class="content3">
    aaaaaa aaaaaa a a a a a a a a
    </div>
</body>

<style>
    body {
        position: relative;
    }
    .content3 {
        width: 800px;
        position: absolute;
        margin: 0 auto;
        left: 0;
        right: 0;
        background-color: purple;
    }
</style>
```

------

- 弹性盒子

```css
body {
    display: flex;
    justify-content: center;
    align-items: center;
}
```



# 如何竖直居中一个元素

- 绝对定位居中 `absolute`跳出内容流，其余部分渲染时绝对定位部分不进行渲染
- `margin:auto` 的效果等同于 `margin-top:0; margin-bottom:0``
- ``top:0;left:0;bottom:0;right:0`将给浏览器重新分配一个边界框，该块`block`将填充其父元素的所有空间
- 父元素一般为`body`或者声明为`position:relative`的内容，给内容块设置一个高度`height`或宽度`width`，能够防止内容块占据所有的可用空间，促使浏览器根据新的边界框重新计算`margin:auto`
- 唯一支持`resize: both`允许用户拉伸元素大小尺寸(手机浏览器和IE8-IE10浏览器不支持resize属性)

```css
/*
优点：
    1.支持跨浏览器，包括IE8-IE10.
    2.无需其他特殊标记，CSS代码量少
    3.支持百分比%属性值和min-/max-属性
    4.只用这一个类可实现任何内容块居中
    5.不论是否设置padding都可居中（在不使用box-sizing属性的前提下）
    6.内容块可以被重绘。
    7.完美支持图片居中。

缺点：
    1.必须声明高度（查看可变高度Variable Height）。
    2.建议设置overflow:auto来防止内容越界溢出。（查看溢出Overflow）。
    3.在Windows Phone设备上不起作用。
*/
.Absolute-Center {
  margin: auto;
  position: absolute;
  top: 0; left: 0; bottom: 0; right: 0;
}
```

- 内容块的父容器设置为`position: relative`，可以使内容居中显示于父容器

{% image vertical.png 示例图 %}

```css
.Center-Container {
  position: relative;
  width: 800px;
  height: 800px;
}
 
.Absolute-Center {
  width: 50%;
  height: 50%;
  overflow: auto;
  margin: auto;
  position: absolute;
  top: 0; left: 0; bottom: 0; right: 0;
}
```

将内容块设置为 `position: fixed`， 并设置一个较大的 `z-index`层级，让内容块一直停留在可视区内，不受页面滚动影响

```css
.Absolute-Center {
    position: fixed;
    z-index: 999;
}
```

# 如何居中一个浮动元素？

```html
<div class="outerbox">
    <div class="innerbox">
        我是浮动的
    </div>
</div>

<style>
    .outerbox {
        float: left;
        position: relative;
        left: 50%;
    }
    .innerbox {
        float: right;
        position: relative;
        right: 50%;
    }
</style>
```

# display有哪些值，有什么作用？

常用的有`none 不显示`, `block 块级元素，前后带换行符`, `inline 默认值，内联元素，前后没有换行符`, `inline-block 行内块元素`, `flex 弹性盒子布局`

| display 值         | 描述                                       |
| ------------------ | ------------------------------------------ |
| none               | 不显示                                     |
| block              | 块级元素，前后带换行符                     |
| inline             | 默认值，被显示为内联元素，前后没有带换行符 |
| inline-block       | 行内块元素                                 |
| list-item          | 列表                                       |
| run-in             | 根据上下文作为块级元素或内联元素           |
| table              | 块级表格，类似`<table>`，前后带换行符      |
| inline-table       | 内联表格，类似`<table>`, 前后没有换行符    |
| table-row-group    | 一个或多个行的分组，类似`<tbody>`          |
| table-header-group | 一个或多个行分组，类似`<thead>`            |
| table-footer-group | 一个或多个行分组，类似`<tfoot>`            |
| table-row          | 一个表格行，类似`<tr>`                     |
| table-column-group | 一个或多个列的分组，类似`colgroup`         |
| table-column       | 一个单元格列，类似`col`                    |
| table-cell         | 一个单元格，类似`<th><td>`                 |
| table-caption      | 一个表格标题，类似`<caption>`              |
| inherit            | 继承父元素`display`值                      |

# [li与li之间有看不见的空白间隔符是什么原因引起的？有什么解决方法？](https://blog.csdn.net/sjinsa/article/details/70919546)

浏览器的默认行为是把`inline`元素间的空白字符（空格换tab）渲染成一个空格，也就是我们上面代码换行后产生换行字符，而它会变成一个空格，空格占用一个字符的宽度

```html
<!-- 解决方法一，将li代码全部写在一排 -->
<ul>
    <li></li><li></li><li></li>
</ul>

<!-- 解决方法二，将ul内字符的间距空白-->
<style>
    ul { letter-spacing: -5px; }
    ul li { letter-spacing: normal; }
</style>
```

# 为什么要初始化css样式

不同浏览器对有些标签的默认值是不同的，降低浏览器差异，减少重复样式，提高代码质量

# absolute的containing block（容器块）计算方式跟正常流有什么不同？

无论属于哪种，都要先找到其祖先元素中最近的`position`值不为`static`的元素，然后再判断；

1. 如果`absolute`元素为`inline`元素，`containing block`为能够包含这个元素生成的第一个和最后一个`inline box`的`padding box`（除`margin, border`外的区域）的最小矩形
2. 否则，则由这个祖先元素的`padding box`构成，如果都找不到，则为`inital containing block`

`static`（默认的）/`relative`：简单说就是它的父元素的内容框（即去掉padding的部分）

`absolute`：向上找最近的定位为`absolute/relative`元素

`fixed`：它的`containing block`一律为根元素`html/body`，根元素也是`inital containing block`

# css中的visibility属性的collapse值有什么用？在不同的浏览器下有什么区别？

| `visibility`属性值 | 属性值描述                                                   |
| ------------------ | ------------------------------------------------------------ |
| visible            | 默认值，元素是可见的                                         |
| hidden             | 元素不可见，但仍然占用页面空间                               |
| collapse           | 作用在`table`相关元素，例如`tr, tbody, thead, tfoot`表现得跟`display:none`一样，不可见且不占用空间；如果用在其他元素上，与`hidden`一样 |
| inherit            | 规定从父元素继承visibility属性的值                           |

在谷歌浏览器里，使用`collapse`和使用`hidden`没有什么区别；在火狐浏览器、Operate和IE11里，使用collapse值的效果就如它的字面意思：`table`的行会消失，下一行会补充它的位置

# [position跟display、margin collapse、overflow、float这些特性相互叠加后会怎么样？](https://www.cnblogs.com/jackyWHJ/p/3756087.html)

可以看做一个类似优先级的机制

`display: none`的时候，`position`, `float`不起作用，在这种情况下，元素不产生框，因此浮动和定位无效

`position`的值是`absolute`, `fixed`，绝对定位下，浮动失效

如果`float`的值不是`none`，那么`display`会被转换，基本上都会转为`block`

`margin collapse`外边距折叠，指的是相邻普通流中的的两个或多个块元素垂直方向上的`margin`会折叠

1). 参与折叠的`margin`都是正值，取其中`margin`较大的值为最终`margin`值

```html
<div style="height: 50px; margin-bottom: 50px; width: 50px; background-color: red;">A</div>
<div style="height: 50px; margin-top: 100px; width: 50px; background-color: green;">B</div>
```

{% image margin-0.png 示例图 %}

2). 参与折叠的`margin`都是负值，取绝对值较大的，反向位移

```html
<div style="height: 100px; margin-bottom: -75px; width: 100px; background-color: red;">A</div>
<div style="height: 100px; margin-top: -50px; width: 100px; background-color: green;">B</div>
```

{% image margin-1.png 示例图 %}

3). 参与的`margin`有正、负值，先取出负`margin`中绝对值最大的，然后，和正`margin`值中最大的`margin`相加

```html
<div style="height: 50px; margin-bottom: -50px; width: 50px; background-color: red;">A</div>
<div style="height: 50px; margin-top: 100px; width: 50px; background-color: green;">B</div>
<!-- margin = 100 + (-50) = 50 -->
```

{% image margin-2.png 示例图 %}

4). 相邻的`margin`要一起参与计算，不得分布计算

```html
<div style="margin: 50px 0; background-color: green; width: 50px;">
    <div style="margin: -60px 0;">
        <div style="margin: 150px 0;">
            A
        </div>
    </div>
</div>
<div style="margin: -100px 0; background-color: green; width: 50px;">
    <div style="margin: -120px 0;">
        <div style="margin: 200px 0;">
            B
        </div>
    </div>
</div>
<!--
	A和B之间的margin折叠产生的margin,是6个相邻margin折叠的结果，将margin值分为两组：
	正值: 50px 150px 200px
	负值: -60px -100px -120px
	根据有正负值的计算规则，正值的最大值200px，负值绝对值最大是-120px，最终折叠后的margin应该是200+(-120)=80
-->
```

浮动元素、inline-block元素、绝对定位元素的margin不会和垂直方向上的其他元素的margin折叠

创建了块级格式化上下文元素，不和它的子元素发生margin折叠，`overflow: hidden`

# 文字超出N行部分缩略显示

```css
text-overflow: -o-ellipsis-lastline;
overflow: hidden;
text-overflow: ellipsis;
display: -webkit-box;
-webkit-line-clamp: 2;
-webkit-box-orient: vertical;
```

