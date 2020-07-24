---
title: css 
date: 2020-06-30 11:17:10
categories:
    - 样式
tags:
    - css
---



# CSS 选择器有哪些

**\*通用选择器**：选择所有元素，不参与计算优先级，兼容性 IE6+
**#X id选择器**：选择 id 值为 X 的元素，兼容性：IE6+
**.X 类选择器**： 选择 class 包含 X 的元素，兼容性：IE6+
**X Y 后代选择器**： 选择满足 X 选择器的后代节点中满足 Y 选择器的元素，兼容性：IE6+
**X 元素选择器**： 选择标所有签为 X 的元素，兼容性：IE6+
**:link，:visited，:focus，:hover，:active 链接状态**： 选择特定状态的链接元素，顺序 LoVe HAte，兼容性: IE4+
**X + Y 直接兄弟选择器**：在X 之后第一个兄弟节点中选择满足 Y 选择器的元素，兼容性： IE7+
**X > Y 子选择器**： 选择 X 的子元素中满足 Y 选择器的元素，兼容性： IE7+
**X ~ Y 兄弟**： 选择X 之后所有兄弟节点中满足 Y 选择器的元素，兼容性： IE7+
**[attr]**：选择所有设置了 attr 属性的元素，兼容性 IE7+
**[attr=value]**：选择属性值刚好为 value 的元素
**[attr~=value]**：选择属性值为空白符分隔，其中一个的值刚好是 value 的元素
**[attr|=value]**：选择属性值刚好为 value 或者 value-开头的元素
**[attr^=value]**：选择属性值以 value 开头的元素
**[attr$=value]**：选择属性值以 value 结尾的元素
**[attr=value]***：选择属性值中包含 value 的元素
**[:checked]**：选择单选框，复选框，下拉框中选中状态下的元素，兼容性：IE9+
**X:after, X::after**：after 伪元素，选择元素虚拟子元素（元素的最后一个子元素），CSS3 中::表示伪元素。兼容性:after 为 IE8+，::after 为 IE9+
**:hover**：鼠标移入状态的元素，兼容性 a 标签 IE4+， 所有元素 IE7+
**:not(selector)**：选择不符合 selector 的元素。不参与计算优先级，兼容性：IE9+
**::first-letter**：伪元素，选择块元素第一行的第一个字母，兼容性 IE5.5+
**::first-line**：伪元素，选择块元素的第一行，兼容性 IE5.5+
**:nth-child(an + b)**：伪类，选择前面有 an + b - 1 个兄弟节点的元素，其中 n >= 0， 兼容性 IE9+
**:nth-last-child(an + b)**：伪类，选择后面有 an + b - 1 个兄弟节点的元素 其中 n >= 0，兼容性 IE9+
**X:nth-of-type(an+b)**：伪类，X 为选择器，解析得到元素标签，选择前面有 an + b - 1 个相同标签兄弟节点的元素。兼容性 IE9+
**X:nth-last-of-type(an+b)**：伪类，X 为选择器，解析得到元素标签，选择后面有 an+b-1 个相同标签兄弟节点的元素。兼容性 IE9+
**X:first-child**：伪类，选择满足 X 选择器的元素，且这个元素是其父节点的第一个子元素。兼容性 IE7+
**X:last-child**：伪类，选择满足 X 选择器的元素，且这个元素是其父节点的最后一个子元素。兼容性 IE9+
**X:only-child**：伪类，选择满足 X 选择器的元素，且这个元素是其父元素的唯一子元素。兼容性 IE9+
**X:only-of-type**：伪类，选择 X 选择的元素，解析得到元素标签，如果该元素没有相同类型的兄弟节点时选中它。兼容性 IE9+
**X:first-of-type**：伪类，选择 X 选择的元素，解析得到元素标签，如果该元素 是此此类型元素的第一个兄弟。选中它。兼容性 IE9+



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

