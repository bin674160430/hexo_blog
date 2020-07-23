---
title: html 
date: 2020-06-30 09:57:00
categories:
    - 页面
tags:
    - html
---

# <img\> 的 *title* 和 *alt* 有什么区别？

1. title是[golbal attributes(全局属性)](#全局属性)之一，当鼠标在元素上的时候显示。
2. alt是<img\>特有属性，是图片内容的等价描述，用于图片无法加载时显示、读屏器阅读图片，提高图片可访问性，除了纯装饰图片外都必须设置有意义的值，搜索引擎会终点分析。

```html
<img src="..." alt="图片" title="鼠标停留展示">
```



# doctype 是什么

1. `<!doctype>`声明不是一个HTML标签，是一个用于告诉浏览器当前 HTMl 版本的指令，必须处于 HTML 文档的头部，在`<html>`标签之前，现代浏览器的 html 布局引擎通过检查 doctype 决定使用兼容模式还是标准模式对文档进行渲染，一些浏览器有一个接近标准模型
2. 在 HTML4.01 中`<!doctype>`声明指向一个 DTD，由于 HTML4.01 基于 SGML，所以 DTD 指定了标记规则以保证浏览器正确渲染内容
3. HTML5 中不区分大小写，不基于 SGML，所以不用指定 DTD，声明为<!DOCTYPE html>
4. HTML5的文档类型是 `<!DOCTYPE html>`，使用的编码是 `<meta charset="utf-8">`

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

## 可移除元素

纯表现的元素：basefont (基准字体), big(大号字体), center(文本水平居中), font(字体), s(删除线文本), strike(), tt(等宽字体), u(下划线文本)

对可用性产生负面影响的元素：frame, frameset, noframes (特定窗口、框架)

产生混淆的元素：acronym(缩写，被abbr代替), applet(嵌入对象，被object代替), dir(目录列表)

参考：https://www.w3school.com.cn/tags/tag_comment.asp

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