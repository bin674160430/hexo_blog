---
title: css 
date: 2021-02-20 16:00:00
categories:
    - 样式
tags:
    - css
---

> 自动加前缀（PostCSS）：https://github.com/postcss/autoprefixer

# 模块与模块化结构

​	CSS 3中，没有采用总体结构，而是采用了分工协助的模块化结构，如下所示：

| 模块名称                          | 功能描述                                                     |
| --------------------------------- | ------------------------------------------------------------ |
| basic box model                   | 与盒相关的样式                                               |
| Line                              | 与直线相关的样式                                             |
| Lists                             | 与列表相关的样式                                             |
| Hyperlink Presentation            | 与超链接相关的样式，如锚的显示方式、激活时的视觉效果         |
| Presentation Levels               | 元素的不同样式级别                                           |
| Speech                            | 与语音相关的样式，如音量、音速、说话间歇时间等属性           |
| Background and border             | 与背景和边框相关的样式                                       |
| Text                              | 与文字相关的样式                                             |
| Color                             | 与颜色相关的样式                                             |
| Font                              | 与字体相关的样式                                             |
| Paged Media                       | 页眉、页脚、页数等页面元数据的样式                           |
| Cascading and inheritance         | 对属性进行赋值                                               |
| Value and Units                   | 将页面上各种各样的值和单位进行统一定义，以供其它模块使用     |
| Image Values                      | 对image的赋值方式                                            |
| 2D Transforms                     | 实现二维空间上的变形效果                                     |
| 3D Transforms                     | 实现三维空间上的变形效果                                     |
| Transitions                       | 实现平滑过渡的视觉效果                                       |
| Animations                        | 实现动画                                                     |
| CSSOM View                        | 查看管理页面或页面中的视觉效果，处理元素的位置信息           |
| Syntax                            | CSS样式表的基本结构、样式表中的一些语法细节、浏览器对于样式表的分析规则 |
| Generated and Replaced Content    | 定义怎么样在元素中插入内容                                   |
| Marquee                           | 当一些元素内容太大，超出指定的元素尺寸时，怎么显示溢出部分   |
| Ruby                              | 用于显示拼音文字的样式                                       |
| Writing Models                    | 文本数据的布局样式                                           |
| Basic User Interface              | 屏幕、纸张上进行输出时页面的渲染方式                         |
| Namespaces                        | 使用命名空间时的语法                                         |
| Media Queries                     | 根据媒体类型来实现不同的样式                                 |
| 'Reader' Media Type               | 用于屏幕阅读器之类的阅读程序时的样式                         |
| Multi-column Layout               | 使用多栏布局                                                 |
| Template Layout                   | 使用特殊布局                                                 |
| Flexible Box Layout               | 创建自适应浏览器窗口的流动布局或自适应字体大小的弹性布局     |
| Grid Position                     | 使用网格布局                                                 |
| Generated Content for Paged Media | 使用印刷时使用的布局方式                                     |

**为什么要分那么多模块来管理？**这是为了避免产生浏览器对于某个模块支持不完全的情况；如果只有一个总体结构，这个总体结构会过于庞大，在对其支持的时候很容易造成支持不完全的情况。把总体结构分成多个模块，各浏览器可以选择对哪个模块进行支持，哪个模块不支持，后续再慢慢支持，以减少支持不完全的可能。

# 选择器

## 属性选择器

- `[att*=val]`：属性值中包含val
- `[att^=val]`：属性值以val开头
- `[att$=val]`：属性值以val结尾

## 伪元素选择器

​	CSS中已经定义好的伪元素使用的选择器

- `:first-line`：某个元素中的第一行文字

- `:first-letter`：某个元素中的文字的首字母（英文）或第一个字（汉字）

- `:before`：某个元素之前插入一些内容

  ```css
  p:before {
      context: '';
  }
  ```

- `:after`：某个元素之后插入一些内容

- `:root`：绑定页面根元素，<html>

- `:not`：排除元素，如排斥body下的h1元素`body *:not(h1) {}`

- `:empty`：元素内容为空白时使用的样式

- `:target`：用户点击了超链接，并且跳转到target元素后起作用

- `:first-child`：某个元素中的第一个子元素

- `:last-child`：某个元素中的最后一个子元素

- `:nth-child(n)`：指定序号的子元素，从1开始计算

- `:nth-child(odd)`, `:nth-child(2n+1)`：奇数子元素

- `:nth-child(even)`,`:nth-child(2n+2)`：偶数子元素

- `:nth-last-child(n)`：倒数指定序号的子元素， odd、even奇偶同上，倒叙排列的

- `:nth-of-type`：类型序号子元素，odd、even同样生效

- `:nth-last-of-type`：同上，倒序

  ```css
  /*
  	:nth-child不关心子元素的类型，
  	h2元素与p元素位置相互交错，就无法满足h2奇偶的样式
  */
  h2:nth-child(odd) {
      background-color: yellow;
  }
  h2:nth-child(even) {
      background-color: skyblue;
  }
  
  /*
  	:nth-of-type会针对同类型的子元素进行计算
  */
  h2:nth-of-type(odd) {
      background-color: yellow;
  }
  h2:nth-of-type(even) {
      background-color: skyblue;
  }
  ```

- `:only-child`, `:nth-child(1):nth-last-child(1)`：只有一个元素

## UI元素状态伪类选择器

- `:hover`鼠标指针移动到元素上时使用的样式
- `:active`元素被激活（鼠标在元素上按下还没有松开）的样式
- `:focus`获取光标焦点的样式
- `:enabled`表单输入元素处于可用状态的样式
- `:disabled`表单输入元素处于不可用状态的样式
- `:read-only`表单输入元素处于只读状态的样式
- `:read-write`表单输入元素处于非只读状态的样式
- `:checked`单选框、复选框处于选取状态的样式
- `:default`页面打开时默认处于选取状态的单选框、复选框样式
- `:indeterminate`页面当开始，一组单选框中任何一个单选框都没有被选取时整组单选框的样式
- `:selection`元素处于选中时的样式

## 兄弟选择器

`<子元素> - <子元素之后的同级兄弟元素> {}`

## 选择器插入内容

```css
h2::before {
    content: 'COLUMN';
}

/*
	content: none; 不插入内容
	只有使用before, after时，content: normal === content: none
*/
h2.sample:before {
    content: none;
}

/*
	url 插入图像文件，
	如果打印的时候，设定不打印背景，使用before追加的图像能够正常打印；但是元素追加的背景图像将不会打印
*/
h2::before {
    content: url(xxx.png);
}

/*
	content通过attr(属性名)来指定attr属性值，将某个属性值显示出来
*/
img::after {
    content: attr(alt);
}
```

## 多个标题前加上连续编号

```css
/*
	<元素>:before { content: counter(计数器名); }
	<元素> { counter-increment:before选择器或after选择器中指定的计数器名 }
*/

/*
	<h1>大标题</h1>
	<p>...</p>
	<h1>大标题</h1>
	<p>...</p>
	--- 将显示为
	1大标题
	...
	2大标题
	...
*/
h1::before {
    content: counter(mycounter);
}
h1 {
    counter-increment: mycounter;
}
```

```css
/*
	<h1>大标题</h1>
	<p>...</p>
	<h1>大标题</h1>
	<p>...</p>
	--- 将显示为
	第1章大标题
	...
	第2章大标题
	...
*/
h1::before {
    content: '第'counter(mycounter)'章';
}
h1 {
    counter-increment: mycounter;
}
```

```css
/*
	指定编号种类, upper-alpha：大写字母编号；upper-roman：大写罗马字母
	content: counter(计数器名, 编号种类)
*/
/*
	<h1>大标题</h1>
	<p>...</p>
	<h1>大标题</h1>
	<p>...</p>
	--- 将显示为
	A.大标题
	...
	B.大标题
	...
*/
h1::before {
    content: counter(mycounter, upper-alpha)'.';
    color: blue;
    font-size: 42px;
}
h1 {
    counter-increment: mycounter;
}
```

```css
/*
	编号嵌套
*/
/*
	<h1>大标题</h1>
	<h2>中标题</h2>
	<h2>中标题</h2>
	<h1>大标题</h1>
	<h2>中标题</h2>
	<h2>中标题</h2>
	--- 将显示为（如下图所示，标题的序号都是连续，只按照标题级别区分）
	1.大标题
		1.中标题
		2.中标题
	2.大标题
		4.中标题
		5.中标题
	--- 如果子标题需要重新计算，需要父标题添加counter-reset， 
	--- 如下面h1样式中注释的 counter-reset: subcounter;
	--- 将得到的结果是
	1.大标题
		1.中标题
		2.中标题
	2.大标题
		1.中标题
		2.中标题
*/
h1::before {
    content: counter(mycounter)'.';
}
h1 {
    counter-increment: mycounter;
    /* counter-reset: subcounter; */
}
h2::before {
    content: counter(subcounter)'.';
    /* content: counter(mycounter) '-' counter(subcounter) */
    /* 可以显示标题连接, 如 1 - 1, 1 - 2 */
}
h2 {
    counter-increment: subcounter;
    margin-left: 40px;
}
```

## 字符串两边添加嵌套文字符号

```css
/*
	使用content属性的open-quote、close-quote在字符串两边添加括号、单引号、双引号之类的前台文字符号
	open-quote开始嵌套的文字符号
	close-quote结尾嵌套文字符号
*/
h1::before {
    content: open-quote;
}
h1::after {
    content: close-quote;
}
h1 {
    quotes:"（""）";
    /* 当需要用双引号时，需要使用\转义字符, quotes: "\"" "\""; */
}
/*
	<h1>标题</h1>
	--- 将显示
	（标题）
*/
```

# 文字添加阴影

```css
/* text-shadow: x y blur color; */
div {
    text-shadow: 5px 5px 5xp gray;
}
/* 指定多个阴影，文字支持多个阴影 */
div {
    text-shadow: 10px 10px #f39800,
        		40px 35px #fff100,
        		70px 60px #c0ff00;
}
```

# 文本自动换行

​	处理英文这类西方文字，浏览器会在半角空格或连字符的地方自动换行，不会在单词中突然换行；而中文汉字可以在任何一个汉字后面进行换行，当中文含有标点符号，浏览器不会让标点符号位于一行文字的行首，将标点符号以及前面的一个文字作为一个整体来统一换行。

## word-break

​	使用`word-break`属性决定自动换行的方式，属性值如下所示：

- `normal`：浏览器默认换行规则
- `keep-all`：只能在半角空格或连字符处换行
- `break-all`：允许在单词内换行

## word-wrap

​	长单词与URL地址自动换行，属性值如下所示：

- break-word：在长单词或URL地址内部进行换行
- normal：保持浏览器默认处理

# 使用服务端字体

## @font-face

- `font-family`：声明字体变量名

- `src`：指定字体文字位置

  - `url()`加载服务端的字体

  - `local()`显示客户端本地字体；如客户端已安装的Arial字体

    ```css
    @font-face {
        font-family: Arial;
        src: local('Arial');
    }
    @font-face {
        font-family: Arial;
        font-weight: bold;
        src: local('Arial Black');
    }
    
    /* 浏览器优先在客户端寻找是否存在Helvetica Neue字体，存在就直接使用；不存在使用服务单的MyHelvetica字体
    @font-face {
    	font-family: MyHelvetica;
    	src: local('Helvetica Neue'),
    		url(MyOpenModernaRegular.ttf);
    }
    */
    ```

  - `format()`字体文件格式 `OpenType(文件扩展名.oft)`、`TrueType(.ttf)`；对应`format`属性值为 `opentype`, `truetype`；*ie中只能用Embedded OpenType(.eot)字体，不需要使用format属性值*

- `font-style`：字体样式

  - `normal`：不使用斜体
  - `italic`：使用斜体
  - `oblique`：使用倾斜提
  - `inherit`：从父元素继承

- `font-variant`：字体的大小写

  - `normal`：浏览器默认值
  - `small-caps`：小型大写字母
  - `inherit`：从父元素继承

- `font-weight`：字体粗细

  - `normal`：浏览器默认值
  - `bold`：粗体字符
  - `bolder`：更粗的字符
  - `lighter`：更细的字符
  - `100~900`：从细到粗，400 === normal，700 === bold

- `font-stretch`：字体是否伸缩变形

  - `normal`：默认值，标准缩放比例
  - `wider`：伸展比例设置为更进一步的伸展值
  - `narrower`：收缩比例设置为更进一步的收缩值
  - `*-expand, *-condensed...`， `ultra-condensed`最宽，`ultra-expanded`最窄

- `font-size`：字体大小

```css
/*
	font-family使用WebFont声明使用服务端字体
	src指定exljbris字体公司免费提供的Fontin Sans字体，字体文字格式为OpenType
*/
@font-face {
    font-family: WebFont;
    src: url('font/Fontin_Sans_R_45b.otf') format('opentype');
    font-weight: normal;
}
h1 {
    font-family: WebFont;
    font-size: 60px;
}
```

## 定义斜体、粗体文字

```css
@font-face {
    font-family: WebFont;
    font-style: italic;
    src: url('Fontin_Sans_R_45b.otf') format("opentype");
}
@font-face {
    font-family: WebFont;
    font-style: italic;
    src: url('Fontin_Sans_I_45b.otf') format("opentype");
}
@font-face {
    font-family: WebFont;
    font-weight: bold;
    src: url('Fontin_Sans_B_45b.otf') format("opentype");
}
@font-face {
    font-family: WebFont;
    font-style: italic;
    font-weight: bold;
    src: url('Fontin_Sans_BI_45b.otf') format("opentype");
}
div {
    font-family: WebFont;
    font-size: 40px;
}
div#div1 {
    font-style: normal;
    font-weight: normal;
}
div#div2 {
    font-style: italic;
    font-weight: bold;
}
```

# 修改字体种类保持字体尺寸不变 font-size-adjust

​	页面使用多种不同字体类型存在的尺寸差异，需要使用`aspect`值用来在将字体修改为其它字体时保持字体大小基本不变，计算方式为 `x-height 除以 字体尺寸`，`x-height`是字体书写小写x的高度（像素单位），假设字体的尺寸为100px，x-height为58px，那么该字体的`aspect = 0.58`，常用的西方字体`aspect`值如下：

| 字体种类           | aspect值 |
| ------------------ | -------- |
| Verdana            | 0.58     |
| Comic Sans MS      | 0.54     |
| Trebuchet MS       | 0.53     |
| Geogia             | 0.5      |
| Myriad Web         | 0.48     |
| Minion Web         | 0.47     |
| Times New Roman    | 0.46     |
| Gill Sans          | 0.46     |
| Bernhard Modern    | 0.4      |
| Gafisch Script Web | 0.37     |
| Fjemish Script     | 0.28     |

​	在`font-size-adjust`指定`aspect`值，并且修改为其它字体后，浏览器对字体尺寸的计算公式：`c = (a / b) s`，`a`实际使用字体的`aspect`值，`b`修改前字体的`aspect`值，`s`字体尺寸，`c`浏览器实际显示的字体尺寸；如果`font-size-adjust`设定为`none`，将按照字体原来大小显示。

# 盒模型 display

## block 块宽度占满整个浏览器

## inline 内容宽度

## inline-block 内容块

​	属于`block`类型的一种，显示时具有`inline`的特点；与`inline`的区别在于可以使用`width、height`指定元素的宽度和高度，因为`width、height`只能用在block类型的元素上。
​	**经典案例——实现并列显示的元素：**

```html
<!-- 使用float， 通常需要用第三个子元素clear清除float -->
<style>
    div {
        width: 200px;
        float: left;
    }
</style>

<!-- 
	inline-block 直接将两个元素并列显示,
	默认情况下inline-block垂直对齐是底部对齐，需要vertical-align调整为顶部对齐，
	要让两个元素中没有缝隙，需要出去元素之间的换行符
-->
<style>
    div {
        display: inline-block;
        width: 200px;
        vertical-align: top;
        background-color: red;
    }
</style>
<div>AAAAAAAAAAAAAAA</div><div>BBBBBBBBBBBBBBB</div>
```

## inline-table 内容表格

​	table属于block类型，不能和其它文字处于同一行；如果将table元素改为inline-table类型，就可以让表格和其它文字处于并列在一行。

## list-item 元素列表

​	`list-style-type`属性为`list-item`设置列表标记 === li

## run-in 、compact根据上下文块级元素内联显示

​	`run-in`或`compact`类型的元素，后面还有block类型的元素，run-in类型的元素将被包含在block类型的元素内部，compact类型将被放置在block元素左边。

## none 元素不显示

## 表格相关类型

| 元素     | 类型               | 说明      |
| -------- | ------------------ | --------- |
| table    | table              | 表格block |
| table    | inline-table       | 内容表格  |
| tr       | table-row          | 一行      |
| td       | table-cell         | 单元格    |
| th       | table-cell         | 列标题    |
| tbody    | table-row-group    | 所有行    |
| thead    | table-header-group | 表头      |
| tfoot    | table-footer-group | 脚注      |
| col      | table-column       | 一列      |
| colgroup | table-column-group | 所有列    |
| caption  | table-caption      | 标题      |

# overflow, overflow-x, overflow-y

​	盒中容纳不下内容的显示方法，属性值如下所示：

- hidden：超出的部分将被隐藏
- scroll：出现固定滚动条
- auto：超出部分出现滚动条
- visible：效果与不适用overflow一样，超出文字按原样显示

# text-overflow

​	使用`overflow: hidden`将盒中超出部分异常，使用`text-overflow: ellipsis`可以在末尾显示代表符号，如...

# box-shadow 盒阴影

​	`box-shadow: x y blur color` -> `box-shadow: 2px 2px 2px gray;`

## 第一个文字、第一行使用阴影

```css
div:first-letter, div:first-line {
    box-shadow: 5px 5px 5px gray;
}
```

# box-sizing

​	指定用width、height分别指定宽度、高度是否包含内部的补白区域、边框的大小

- content-box：元素的宽度、高度不包括内部补白区域、边框的大小（默认使用）
- border-box：元素的宽度、高度包括内部补白区域、边框的大小

```css
div {
    width: 300px;
    padding: 30px;
    margin: 20px auto;
    border: 30px solid gray;
}
div.content-box {
    /* 元素内容300px, 总宽度 = 300 + 30 * 2(padding-left, right 30) + 30 * 2(border-left, right 30) = 420 */
    box-sizing: content-box;
}
div.border-box {
    /* 元素总宽度300px, 内容宽度 = 300 - 30 * 2 - 30 * 2 = 180 */
    box-sizing: border-box;
}
```

# 背景相关新增属性

## background-clip：背景的显示范围

- `border-box`背景范围包括边框区域在内
- `padding-box`不包括边框区域，边框在外
- `content-box`不包内部括补白区域

## background-origin：绘制背景图像时的起点

- `border-box`从边框的左上角开始绘制
- `padding-box`从内部补白区域左上角开始绘制
- `content-box`从内容的左上角开始绘制

## background-size：背景图像尺寸

`background-size: 40px 20px;`40px图像宽度 20px图像高度，如果需要维持图像比例，可以在设定宽高度的同时，将另一个参数设定为`auto`，如`background-size: auto 20px;`高度20px，宽度的等比适应；只设定第一个参数宽度，第二个参数高度在`safari3`中宽度和高度都使用该参数值，在`chrome8`、`Opera10`以上是宽度使用该参数，高度使用`auto`。

- `auto`图片保持原声大小
- `cover`保持图片比例，拓展至覆盖整个元素
- `contain`保持图片比例，拓展图片让其最长边保持在元素内部

## background-break：内联元素的背景图像进行平铺时的循环方式

- `bounding-box`在整个内联元素中进行平铺
- `each-box`在每一行进行平铺
- `continuous`下一行中的图像紧接着上一行中的图像进行平铺

## 一个元素多个背景图

```html
<style>
    div {
        background-image: url(red.png), url(green.png), url(blue.png);
        backgournd-repeat: no-repeat, repeat-x, no-repeat;
        background-position: 3% 98%, 85%, center center, top;
        widthL 300px;
        padding: 90px 0px;
    }
</style>
```

## 线性渐变

```css
div {
  /* 从底部左侧开始往顶部右侧渐变，由红色到蓝色 */
 	background: linear-gradient(to top right, red, blue);
  /* 指向顶部右侧45度的渐变 */
  background: linear-gradient(45deg, red, blue);
  /* 从不可见的区域开始渐变 */
  background: linear-gradient(red - 50%, blue);
  
  background: linear-gradient(#f90 0, #f90 2%, #555 2%, #eee 50%, #555 98%, #f90 98%, #f90 100%);
}
```

## 径向渐变

参数1

- `closest-side`渐变形状为圆形的情况下，渐变形状会与距离中心最近的边框相切；在椭圆形的情况下，会与距离中心最近的两个边框相切。
- `closest-corner`渐变形状会与距离中心最近的角相切。
- `farthest-side`和`closest-side`相反；在圆形的情况下，与距离中心最远的边相切；在椭圆的情况下，与距离中心最远的两边相切。
- `cover`等价于`farthest-corner`。
- `contain`等价于`closest-side`。

参数2

- 设置`5em`（只提供大小）生成一个直径为5em的圆形渐变效果
- 设置`circle`生成一个沾满整个容器的圆形渐变效果（直径为最长边）
- `40px 30px`生成一个宽40px，高30px的椭圆形
- `ellipse`生成和容器大小一致的椭圆形

参数3

- `at top right`径向渐变的中心在右上方
- `at right 100px top 20px`径向渐变的中心在距右边宽100px，上边框20px处
- `at center left`径向渐变的中心在左边框中间处

```css
div{
  /* 直径12rem的圆形渐变 */
  background: radial-gradient(12rem circle at bottom, yellow, orange, red);
  background: radial-gradient(closest-side circle at center, #333);
}
```

https://projects.verou.me/css3patterns/

# transform变形

## scale(x, y) 缩放

`transform: scale(0.5)` 水平垂直缩放50%

## skew(x, y) 倾斜

`transform: skew(30deg, 60deg)`水平倾斜30度，垂直倾斜60度，若只使用一个参数，将视为只在水平方向进行倾斜

## translate(x, y) 移动

`transform: translate(30px, 50px)`水平移动30像素，垂直移动50像素，若只使用一个参数，将视为只在水平方向上移动

## transform-origin(x, y) 变形焦点

默认以元素的中心作为焦点进行变形，水平值有left、center、right；垂直值有top、center、bottom

## matrix()

相当于translate, scale, skew的组合使用

# Transition 平滑过渡

`transition: property duration timing-function`

```css
div {
    color: #000;
    background-color: #fff;
    transition
}
div:hover {
    transition: color 1s linear 0s, background-color 1s linear;
    /* transition: all 1s linear 0s; */
}
```

# Animation 动画

```css
@keyframes myfirst {
    from { background-color: red; }
    to { background-color: yellow; }
}
@keyframes mycolor {
    0% {
        color: red;
    }
    100% {
        color: yellow;
    }
}
div {
    color: red;
    animation: mycolor 5s linear;
}
```

# 颜色

- `rgba(r, g, b, a)` rgb分别对应红色、绿色、蓝色值范围（0-255），a透明度（0-1）
- `hsla(h, s, l, a)` h色调、s饱和度、l亮度（0%-100%），a透明度(0-1)

## alpha通道和opacity属性的区别

`alpha`只针对元素的颜色透明度
`opacity`整个元素的透明度

## transparent 完全透明

​	相当于使用`alpha: 0`通道

# 轮廓

## outline

​	`outline: outline-color outline-style outline-width`在元素周围绘制一条轮廓线，起到突出作用，属性值和`border`类似

## out-offset

​	`outline`将紧贴着边框外围绘制一条轮廓线，`outline-offset: 2px;`可以调整outline向外偏离距离

# resize属性

​	允许用户通过拖动的方式修改元素尺寸，目前主要用于可以使用`overflow`属性的元素中

- `none`不能修改元素尺寸
- `both`可以修改元素的宽、高
- `horizontal`可以修改元素宽度
- `vertical`可以修改元素高度
- `inherit`继承父元素`resize`

# initial取消对元素的样式指定

​	initial将使用属性设定的默认值，不考虑浏览器对元素追加了什么样式。

# 特性查询

​	*IE11之前和Safari（IOS8之前）不支持*

# calc()

​	计算函数（加减乘除），android 4.3及以下版本的chrome不支持，其他都支持。

`.thing { width: calc(50% - 10px) }`

# 相对视口viewport长度

- vw：viewport宽度
- vh：viewport高度
- vmin：viewport最小值，等于vw、vh中较小的值
- vmax：viewport最大值，等于vw或vh中较大的值

# 滤镜filter

*ie不支持* https://www.runoob.com/cssref/css3-pr-filter.html

- `filter: url('./img/filters.svg#filterRed')`定义一个svg滤镜使用。
- `filter: blur(3px)` 使用一个简单的长度值，高斯模糊。
- `filter: brightness(2)` 使用从0到1值或者从0%到100%的值，0/0%全黑，1/100%没有变化，任何更高的值意味着更高的亮度。
- `filter: contrast(2)` 使用从0到1、0%到100%的值，0/0%全黑，1/100%没有变化，更高的值意味着更高的对比度。
- `filter: drop-shadow(4px 4px 6px #ccc)` 阴影。
- `filter: grayscale(0.8)` 使用从0到1、0%到100%的值来表示元素灰度化的程度，0表示没有灰度化，1表示完全灰度化。
- `filter: hue-rotate(25deg)` 使用从0度到360度表示颜色在色轮上的变化角度。
- `filter: invert(75%)` 使用从0到1、0%到100%的值表示元素反色程度。
- `filter: opacity(50%)` 使用从0到1、0%到100%的值表示元素的透明度。
- `filter: saturate(15%)` 使用从0到1、0%到100%的值表示元素的饱和度，高于1/100%会增加额外的饱和度。
- `filter: sepia(0.74)` 使用从0到1、0%到100%来为元素添加褐色滤镜，0/0%没有变化，更高的值表示褐色化程度提升，1/100%最高效果。
- 使用多个滤镜，用空格隔开 `filter: opacity(10%) blur(2px) sepia(35%);`

