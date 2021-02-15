---
title: html 
date: 2020-06-30 09:57:00
categories:
    - 页面
tags:
    - html
---

# HTML5与HTML5.1的区别

​	最初指定`HTML5`的人（WHATWG）对版本号不太关注，认为`HTML5`是一门"活的"语言，鼓励`web`开发者关注浏览器的支持情况，而非版本号。

​	后来，WHATWG把HTML5交给W3C官方，W3C是一个严谨、细致的组织，希望区分最初发布的HTML5和后来经过改进的版本，因此，W3C决定将最初发布的HTML5命名为HTML 5.0，第二次发布的版本是 HTML 5.1，第三次发布 HTML 5.2，所有这些版本都还是HTML5。

​	后续的HTML5标准不大可能再有大的改动了，如果有新功能，也会以独立、补充性的规范形式发布。这样，少数几个人就可以快速制定新的HTML5功能规范，而不必等待整个语言的修订。

# DOCTYPE 是什么？严格模式与混杂模式区分？

1. `<!DOCTYPE>`声明在`<html>`标签之前，告知浏览器的解析器，用什么文档类型规范来解析
2. 在 HTML4.01 中`<!DOCTYPE>`声明指向一个 DTD，由于 HTML4.01 基于 SGML，所以 DTD 指定了标记规则以保证浏览器正确渲染内容
4. HTML5的文档类型是 `<!DOCTYPE html>`，（HTML5 中不区分大小写，不基于 SGML，所以不用指定 DTD），使用的编码是 `<meta charset="utf-8">`

`严格模式`的排版和`javascript`运作模式是以浏览器支持的最高标准运行

`混杂模式`页面以宽松向后兼容的方式显示，模拟老式浏览器的行为以防止站点无法工作（浏览器会尝试根据有点不那么正常的规则呈现网页），`DOCTYPE`不存在或者格式不正确会导致文档以混杂模式呈现——问题是不同浏览器的混杂模式也不一样，不是字体大小不一样，就是布局上有瑕疵，或者出现其他不一致的问题

常见 *doctype*：

```html
<!-- HTML4.01 strict：不允许使用表现性、废弃元素（如 font）以及 frameset -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

<!-- HTML4.01 Transitional:允许使用表现性、废弃元素（如 font），不允许使用 frameset -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

<!-- HTML4.01 Frameset:允许表现性元素，废气元素以及 frameset -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">

<!-- XHTML1.0 Strict:不使用允许表现性、废弃元素以及 frameset，文档必须是结构良好的 XML 文档 -->
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">

<!-- XHTML1.0 Transitional:允许使用表现性、废弃元素，不允许 frameset，文档必须是结构良好的 XMl 文档 -->
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<!-- XHTML 1.0 Frameset:允许使用表现性、废弃元素以及 frameset，文档必须是结构良好的 XML 文档 -->
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
```

# XHTML回归

想强制使用XHTML语法，担心自己（或同事）在不经意间“堕落”到使用过去HTML的松懈语法，可以使用XHTML5，给HTML5加上XML限制，必须在html元素中明确添加XHMTL命名空间、关闭每一个元素、所有标签都要小写....

```html
<!DOCTYPE html>
<html lang="en" xmls="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8"/>
        <title>HTML Document</title>
        <link href="style.css" ref="stylesheet"/>
        <script src="script.js"></script>
    </head>
    <body>
        <p>Let's rock the browser, XHTML5 style.</p>
    </body>
</html>
```

如果页面有什么错误，浏览器只会显示处理了一部分的页面

> 技巧：只要把文件的扩展名改为.xhtml或.xht，多数浏览器（包括Firefox、Chrome、IE9+）都会认为该页面是从web服务器下载下来的，而且MIME类型为XML

# HTML5元素标记

- 不允许写结束标记的元素有：`area` `base` `br` `col` `command` `embed` `hr` `img` `input` `keygen` `link` `meta` `param` `source` `track` `wbr`——（写法是<input/>）
- 可以省略结束标记的元素有：`li` `dt` `dd` `p` `rt` `rp` `optgroup` `option` `colgroup` `thead` `tbody` `tfoot` `tr` `td` `th`
- 可以省略全部标记的元素有：`html` `head` `body` `colgroup` `tbody`——（改元素只是标记被省略，实际还是以隐士的方式存在）

# 具有boolean的属性值

​	具有boolean值的属性，例如disabled、readonly等，只写属性而不指定属性值时，表示属性值为true（也可以将属性名、空字符串设定为属性值）；如果想要将属性值设为false，可以不使用改属性。

```html
<input type="checkbox" checked>
<input type="checkbox">
<input type="checkbox" checked="checked">
<input type="checkbox" checked="">
```

# 省略引号

````html
<input type="text">
<input type='text'>
<input type=text>
````

# 改变的元素

将旧元素用于新的目的

`<small>`不再是减少字体的大小，表示附属细则，虽然删除了`<big>`，但保留了`<small>`

`<hr>`用于在两个区域间画一条水平线，表示主题的转换，从一个主题变为另一个主题

`<s>`删除的文本，表示不再准确或不再相关的内容

`<strong>`粗体，重要的文本，需要在周围文本中突出的文本

`<b>`粗体文本，但该文本并不比其它文本更重要，比如关键字、产品名称等需要粗体表示可以用

`<em>`重读文本，就是朗读的时候要大声读出来

`<i>`斜体文本，但该文本并不比其它文本更重要，比如外文单词、技术术语等需要斜体表示的文本可以使用

# 调整的元素

调整元素使用规则

不常用的`<address>`元素并不适合标注邮政地址，这个元素只有一个目的，提供HTML文档作者的联系信息，比如电子邮件地址或网站链接：

```html
Our website is managed by:
<address>
	<a href="mailto:jsolo@mysite.com">John Solo</a>,
    <a href="mailto:lcheng@mysite.com">Lisa Cheng</a>, and
    <a href="mailto:rpavane@mysite.com">Ryan Pavane</a>.
</address>
```

`<ol>`元素（有序列表）现在有了`reversed`属性，用于反转序号，目前只有Chrome和Safari支持

# 标准化的元素

HTML5把一些浏览器支持，但没有得到之前HTML和XHTML规范承认的元素加入了标准

`<wdr>`断行，某个词太长了，一行放不下，浏览器就会在`<wdr>`标注的地方断开

```html
<p>Many linguists remain unconvinced that 
<b>supercali<wbr>fragilistic<wbr>expialidocious</b> is indeed a word.</p>
```

如果需要在小空间（表格、小方块）放长名字，可以用`<wdr>`来标注可以在名字的什么地方断行，只有在相应空间盛不下长名字才会断行，上面代码将出现下图三种情况

{% image wdr.png 图片 %}

# 对付旧版本浏览器

## 平稳退化

例如`<video>`元素提供了一种后背机制，可以为老的浏览器提供代替内容（`Flash`插件播放器）

## 借助JavaScript

HTML5的某些功能完全可以用优秀的Javscrtipt库来实现，比较成熟的称为“腻子脚本”

## [了解浏览器支持情况](http://caniuse.com) http://caniuse.com

## [浏览器装机情况统计 http://gs.statcounter.com](http://gs.statcounter.com)

## [网页跟踪服务Google Analyics http://www.google.com/analytics](http://www.google.com/analytics)

## 检测功能

http://modernizr.com/download 下载js文件

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>HTML5 Feature Dection</title>
        <script src="modernizr-latest.js"></script>
    </head>
    <body>
        <script>
        	if (Modernizr.draganddrop) {
                console.log('支持拖拽');
            } else {
                console.log('不支持拖拽');
            }
        </script>
    </body>
</html>
```

## 腻子脚本 [http://tinyurl. com/polyfill](http://tinyurl. 
com/polyfill)

# 行内元素、块级元素、空元素有哪些？

行内元素：a, b, span, img, input, strong, select, label, em, button, textarea

块级元素：div, ul, li, dl, dt, dd, p, h1-h6, blockquote

# 页面导入样式时，link和@import有什么不同？

`link`属于`XHTML`标签，无兼容问题，页面被加载时，`link`会同时被加载

`@import`只有ie5以上才能被识别，由`css`提供，需要等到页面被加载完再加载

`link` 权重 > `@import`

# *HTML*全局属性(*global attribute*)有哪些?

参考资料：[MDN: html global attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes)或者[W3C HTML global-attributes](http://www.w3.org/TR/html-markup/global-attributes.html#common.attrs.core)

- `accesskey`:设置快捷键，提供快速访问元素如[aaa](https://github.com/qiu-deqing/FE-interview#)在 windows 下的 firefox 中按`alt + shift + a`可激活元素
- `class`:为元素设置类标识，多个类名用空格分开，CSS 和 javascript 可通过 class 属性获取元素
- `contenteditable`: 指定元素内容是否可编辑
- `contextmenu`: 自定义鼠标右键弹出菜单内容
- `data-*`: 为元素增加自定义属性
- `dir`: 设置元素文本方向
- `draggable`: 设置元素是否可拖拽
- `dropzone`: 设置元素拖放类型： copy, move, link
- `hidden`: 表示一个元素是否与文档。样式上会导致元素不显示，但是不能用这个属性实现样式效果
- `id`: 元素 id，文档内唯一
- `lang`: 元素内容的的语言
- `spellcheck`: 是否启动拼写和语法检查
- `style`: 行内 css 样式
- `tabindex`: 设置元素可以获得焦点，通过 tab 可以导航
- `title`: 元素相关的建议信息
- `translate`: 元素和子孙节点内容是否需要本地化

# 什么是*web*语义化，有什么好处？

web 语义化是指通过 HTML 标记表示页面包含的信息，包含了 HTML 标签的语义化和 css 命名的语义化。 HTML 标签的语义化是指：通过使用包含语义的标签（如 h1-h6）恰当地表示文档结构 css 命名的语义化是指：为 html 标签添加有意义的 class，id 补充未表达的语义，如[Microformat](http://en.wikipedia.org/wiki/Microformats)通过添加符合规则的 class 描述信息 为什么需要语义化：

- 去掉样式后页面呈现清晰的结构
- 搜索引擎更好地理解页面，有利于收录
- 便团队项目的可持续运作及维护

# h5有什么新特性，可移除元素有哪些？

## 新特性：

- 拖拽
- 语义化更好的内容标签（页眉、导航、页脚、旁边、文章、部分）
- 音频、视频
- 画布
- 地理位置
- 本地离线存储localStorage长期缓存数据，浏览器关闭后数据不丢失；sessionStroage的数据在浏览器关闭后自动删除
- 表单控件，日历、日期、时间、电子邮件、网址、搜素
- 新的技术webworker、websocket

## 处理h5新标签的兼容问题

```html
<!-- 1、使用静态资源的 html5shiv 包 -->
<!--[if lt IE9]>
<script src="http://cdn.static.runoob.com/libs/html5shiv/3.7/html5shiv.min.js"></script>
<![endif]-->
<!-- 2、载入完毕后，初始化新标签的css -->
<style>
    header, section, footer, aside, nav, main, atricle, figure { display: block; }
</style>
```

# H5的离线存储

客户端没有联网，可以正常访问站点或应用，在用户与互联网连接时，更新用户机器上的缓存文件，因为大部分网站都有交互功能，所以离线存储应用不是很广泛。

h5离线存储是一个基于.appcache文件的缓存机制（不是存储技术），通过这个文件的解析列表离线存储资源，这个资源会像cookie一样被存储下来，之后当网络处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示

```javascript
// 检测网络是否在线
if (navigator.onLine == true) {
  　　alert("正常上网")
}
else {
　　  alert("无法连接网络") 
}
```

```html
<!-- html添加manifest, 文件扩展名为.appcache, 首次访问网页缓存在本地的文件，如果下一次没有网络，就不走服务端，取该文件清单 -->
<! DOCTYPE html>
<html manifest="manifest.appcache">
    
</html>

manifest.appcache 文件内容

CACHE MANIFEST

CACHE:
# 需要缓存的文件列表
style.css
1.jpg
01.js
http://localhost/applicationcache/02.js
http://localhost/applicationcache/zepto.js

NETWORK:
# 不需要缓存的
4.jpg

FALLBACK:
# 访问缓存失败后，备用访问的资源，第一个是访问源，第二个是替换文件*.html /offline.html
2.jpg/3.jpg
```

服务端添加配置正确的 `MIME-TYPE`, 即`text/cache-manifest`，必须在web服务器上进行配置

# <img\> 的 *title* 和 *alt* 有什么区别？

1. title是[golbal attributes(全局属性)](#全局属性)之一，当鼠标在元素上的时候显示。
2. alt是<img\>特有属性，是图片内容的等价描述，用于图片无法加载时显示、读屏器阅读图片，提高图片可访问性，除了纯装饰图片外都必须设置有意义的值，搜索引擎会终点分析。

```html
<img src="..." alt="图片" title="鼠标停留展示">
```

# canvas 和 svg有什么区别？

## canvas

- 通过`javascript`来绘制2D图形
- 逐像素进行绘制的
- 位置发生改变，会重新进行绘制

依赖分辨率，不支持事件处理器，弱的文本渲染能力，能够以`.png/.jpg`格式保存结果图像，适合图像密集型游戏，其中许多对象会被频繁重绘

## svg

- 使用`XML`描述2D图形的语言
- `svg`基于`xml`意味着，`svg DOM`中的每个元素都是可用的，可以为某个元素附加`javascript`事件处理器
- `svg`中，每个被绘制的图形均被视为对象，如果`svg`对象的属性发生变化，浏览器能够自动重现图形

不依赖分辨率，支持事件处理器，最适合带有大型渲染区域的应用程序（如谷歌地图），复杂度高会减慢渲染速度（任何过度使用`DOM`的应用都不快），不适合游戏应用

# iframe的优缺点

优点：

- iframe能够原封不动的把嵌入的网页展现出来，分离开网页内容
- 如果有多个网页引用iframe，那么只需要修改iframe的内容，就可以实现调用的每一个页面内容的更改
- 网页如果为了统一风格，头部和版本都是一样的，就可以写成一个页面，用iframe来嵌套，可以增加代码的重用
- 如果遇到加载缓慢的第三方内容如图标和广告，可以由iframe来解决

缺点：

- 会产生很多页面，不容易管理
- iframe框架结构有时会让人感到迷惑，如果框架个数多的话，可能出现上下、左右滚动条，会分散访问者的注意力，用户体验度差
- 代码复杂，无法被一些搜索引擎搜到，不利于搜索引擎优化，这一点很关键，现在的搜索引擎爬虫还不能很好的处理iframe中的内容
- 很多移动设备（PDA手机）无法完全显示框架，设备兼容性差
- iframe框架页面会增加服务器的http请求，对于大型网站是不可取的，现在基本上都是用Ajax来代替iframe，iframe已经渐渐地退出了前端开发

# Label的作用是什么？怎么用的？

label标签是用来定义表单控制间的关系，当用户选择该标签时，浏览器会自动将焦点转到相关的表单控件上

```html
<!-- for 与 id对应点击label才能触发input焦点 -->
<label for="number">number:</label>
<input type="text" name="" id="number" />
```

# form自动完成功能

`html5`有个新属性`autocomplete`，规定表单是否应该启用自动完成功能，它自动完成允许浏览器预测对字段的输入，当用户在字段开始键入时，浏览器基于之前键入过的值，应该显示出在字段中填写的选项

`autocomplete="on"`会被记录下来，而 `email autocomplete="off"`再次输入不会被记录

```html
<form autocomplete="on">
    First name: <input type="text" name="firstName" />
    Last name: <input type="text" name="lastName" />
    E-mail: <input type="text" name="email" autocomplete="off" /><br/>
    <input type="submit" />
</form>
```

## 怎么关闭自动完成功能

- 在IE的internet选项菜单里的内容，自动完成里面设置
- 设置`form`、`input`的`autocomplete`为`on`、`off`来开启关闭自动完成功能（关闭整个表单自动提示功能、关闭密码域的自动完成）

# 怎么实现浏览器内多个标签页之间通信？

1. 使用`localStorage`存储数据，通过`storage`事件监听， A页面调用`localStorage`保存或者删除数据触发`storage`事件，B页面监听`storage`事件获取信息

   ```javascript
   window.addEventListener("storage", function(event) {
       console.log(event.key, event.newValue);
   });
   ```

2. 使用`cookie`+`setInterval`， A页面将需要传递的消息存储在cookie， B页面设置`setInterval`间隔读取`cookie`

# 页面可见性(page visibility api)有哪些用途？

浏览器打开多个标签页，始终只有一个页面正在看着，如果把浏览器缩小，所有的页面都不可见了；动画、视频、音频可以在页面显示时开启，隐藏时关闭

```javascript
// visibilitychange 监听页面显示、隐藏
// document.hidden(boolean) true 隐藏，false 显示
// document.visibilityState(string) hidden隐藏， visible显示，prerender预渲染，preview预览
document.addEventListener("visibilitychange", function() {
    if(document.hidden) {
        document.title = 'hidden';
    } else {
        document.title = 'visibile';
    }
})
```

# 怎么在页面上实现一个圆形的可点击区域

1. `css border-radius: 50%`

```html
<div class="round"></div>
<style>
    .round {
        width: 100px;
        height: 100px;
        border-radius: 50%;
        background-color: red;
    }
</style>
```

2. `<img>`通过`usemap`映射到`<map>`的`circle`-`<area>`

```html
<img src="" width="100" height="100" usemap="#map" />
<map name="map">
    <!-- area在被点击后呈现outline -->
    <area shape="circle" coords="50,50,50"></area>
</map>
```

3. js获取鼠标点击位置左边，判断到圆点的距离是否大于圆半径，判断点击位置是否在圆内

```javascript
document.onclick = function(e) {
    var r = 50;
    var x1 = 100;
    var y1 = 100;
    var x2 = e.clientX;
    var y2 = e.clientY;
    var distance = Math.abs(Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2)));
    if (distance <= 5) console.log('...')
}
```

# 实现不同用border画出1px高的线，在不同浏览器的Quirksmode和CSSCompat模式下能保持同一效果

```html
<div style="height:1px;overflow:hidden;background:red;"></div>
```

# title和h1，b和strong，i和em区别？

`title`网站标题，概括网站信息，可以直接告诉搜索引擎和用户这个网站是关于什么主题和内容的，显示在网页的tab栏

`h1`突出文章主题，面对用户，更突出其视觉效果，指向页面主体信息，显示在网页中

`b`实体标签，用来给文字加粗，没有实际含义，常用来表达无强调或着重意味的粗体文字，比如文章摘要中的关键词、评测文章中的产品名称、文章的导言，为符合规范，尽量少用`b`，改用`strong`

`strong`标签内字符重要，用以强调，默认格式加粗

`i`实体标签，斜体字，没有实际含义，常用来表达无强调或着重意味的斜体，比如生物学名、术语、外来语，为符合规范，尽量少用`i`，改用`em`

`em`逻辑标签，强调文本内容，用以强调，默认格式是斜体