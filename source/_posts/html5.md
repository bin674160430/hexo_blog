---
title: html 
date: 2021-02-06 14:39:00
categories:
    - 页面
tags:
    - html5
---

> 参考资料：
> 《HTML5与CSS3权威指南》、《HTML5与CSS3实战_第二版》

# 腻子脚本

​	所有现代浏览器都理解HTML5中新的语义元素（新的结构化元素、视频和音频标签），甚至老版本的IE（IE9以下）可以通过引入一段“腻子脚本”正确渲染新元素。https://modernizr.com/

# doctype

​	`<!DOCTYPE html>`（大小写一样）告诉浏览器文档类型，如果没有这一行，浏览器将不知道如何处理后面的内容

# HTML标签与lang属性

​	`<html lang="en"></html>`文档根标签，lang定义文档语言，语言列表https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry

# 字符编码

​	`<meta charset="utf-8">`多数是utf-8，https://www.w3.org/International/questions/qa-html-encoding-declarations#html5charset

# HTML5新增的元素

## 一、用于构建页面的语意元素

## main

​	`<mian>`页面主体，web中的主要部分*（导航链接、版本信息、站点标志、广告等多个文档中重复出现的不算主内容）*，都应该放到main中，每个页面的主内容只能有一个，而且不能作为`article`、`aside`、`header`、`footer`、nav元素的后代

## article

​	`<article>`代表文档、页面或应用程序中独立、完整的，可以独自被外部引用的内容。它可以是一篇博客或报刊中的文章、一篇论坛帖子、一段用户评论或独立的插件，或其它任何独立的内容。通常一个aricle元素有自己的标题（一般放在一个header元素里面），有时还有自己的脚注。

```html
<article>
	<header>
    	<h1>苹果</h1>
        <p>发表日期：<time pubdate="pubdate">2010/10/09</time></p>
    </header>
    <p><b>苹果</b>，植物类水果，多次花果。。。（“苹果”文章正文）</p>
    <footer>
    	<p>
            <small>著作权归****公司所有。</small>
        </p>
    </footer>
</article>
```

​	artilce是可以嵌套使用的，内层的内容在原则上需要与外层的内容相关联。例如：一篇博客文章中，针对该文字的评论就可以使用嵌套article元素的方式；用来呈现评论的article元素被包含在表示整体内容的article元素里面。

```html
<article>
	<header>
    	<h1>苹果</h1>
        <p>发表日期：
            <time pubdate datetime="2010/10/09">2010/10/09</time>
        </p>
    </header>
    <p><b>苹果</b>，植物类水果，多次花果。。。（“苹果”文章正文）</p>
    <section>
    	<h2>评论</h2>
        <article>
        	<header>
            	<h3>发表者：xx</h3>
                <p>
                    <time pubdate datetime="2010-10-10T19:10-08:00">1小时前</time>
                </p>
            </header>
            <p>我喜欢苹果，我最喜欢的品种是红富士。</p>
        </article>
        <article>
        	<header>
            	<h3>发表者：xxx</h3>
                <p>
                    <time pubdate datetime="2010-10-10T19:15-08:00">1小时前</time>
                </p>
            </header>
            <p>苹果？我不喜欢，我喜欢吃橘子。</p>
        </article>
    </section>
</article>
```

​	article表示插件，使插件看起来好像内嵌在页面中

```html
<article>
	<h1>My Fruit Spinner</h1>
    <object>
        <param name="allowFullScreen" value="true">
        <embed src="#" width="600" height="395"></embed>
    </object>
</article>
```



## aside

`<aside>`表示article元素的内容之外的、与article元素内容相关的辅助信息；可以包含与当前页面或主要内容相关的引用、侧边栏、广告、导航条，以及其它类似的有别于主要内容的部分。

```html
<!DOCTYPE html>
<head>
    <meta charset="utf-8">
    <title>aside元素实例</title>
</head>
<body>
    <header>
    	<h1>F#入门</h1>
    </header>
    <article>
    	<h1>第四节 词法闭包</h1>
        <p>lambda表达式可以创建词法闭包。。。（文章正文）</p>
        <aside>
        	<!-- 因为这个aside被放置在一个article内部，所以分析器将这个aside元素的内容理解成是article的内容相关联 -->
            <h1>名词解释</h1>
            <dl>
                <dt>F#</dt>
                <dd>F#为.Net2010中引入的新型函数型编程语言</dd>
            </dl>
            <dl>
                <dt>词法闭包</dt>
                <dd>词法闭包是指，将创建lambda表达式时的环境保存起来。。。（详细解释）</dd>
            </dl>
        </aside>
    </article>
</body>
<!--
	网页的标题放在了header，在header的后面将所有文章的部分放在了一个article，将文章的正文部分放在了一个p中，但是该文章还有一个名词解释的附属部分，用来解释该文章的一些名词，而p下部又放置了一个aside，用来存放名词解释部分的内容。
-->
```

```html
<aside>
	<nav>
    	<h2>评论</h2>
        <ul>
            <li>
            	<a href="http://blog.sina.com.cn/1683">erway</a> 10-24 14:25
            </li>
            <li>
            	<a href="http://blog.sina.com.cn/u/1345">太阳雨</a>10-22 23:48<br/>
                <a href="http://blog.sina.com.cn/s/blog_6a9kv8f.html#comment">顶，拜读一下老牛的文章</a>
            </li>
            <li>
            	<a href="http://blog.sina.com.cn/u/1252955385">新浪微博</a>08-12 08:50<br/>
                <a href="#">恭喜！您已经成功开通了博客</a>
            </li>
        </ul>
    </nav>
</aside>
```

## figure

​	`<figure>`一种元素的组合（图片、统计图、代码示例、音频插件、视频插件、统计表格等），带有可选标题，表示网页上一块独立的内容，将其从网页上移除后不会对网页上的其它内容产生影响。figcation表示figure的标题，必须写在figure内部，一个figure最多只允许防止一个figcaption。

```html
<figure>
	<figcaption>title</figcaption>
  	<p>content</p>
</figure>
```

## footer

​	`<footer>`整个页面或页面中的一个内容区块的脚注，通常会包含原创作者的姓名、创作日期以及创作者的联系信息；一个页面没有限制footer个数；可以为article、section添加footer。

## header

​	`<header>`页面中的一个内容区域或者整个页面的标题，通常用来放置整个页面或页面内的一个内容区块的标题，也可以包含其他内容（数据表格、搜索表单和相关的logo图片），一个网页并未限制header元素的个数，可以拥有多个，可以为每个内容区块加一个header；一个header通常包括至少一个h1~h6，也可以包括hgroup。

```html
<header>
	<h1>网页标题</h1>
</header>
<article>
	<header>
    	<h1>文章标题</h1>
    </header>
    <p>文章正文</p>
</article>
```

```html
<header>
    <hgroup>
    	<h1>IT新技术</h1>
        <a href="http://blog.sina.com.cn/itnewtech">http://blog.sina.com.cn/itnewtech</a>
        <a href="#">[订阅]</a>
        <a href="#">[手机订阅]</a>
    </hgroup>
    <nav>
    	<ul>
            <li>首页</li>
            <li><a href="http://blog.sina.com.cn/articlelist1.html">博文目录</a></li>
            <li><a href="#">图片</a></li>
            <li><a href="#">关于我</a></li>
        </ul>
    </nav>
</header>
```

## nav

​	`<nav>`页面中导航链接的部分，期中导航元素链接到其它页面或当前页面的其它部分。只需要将主要的、基本的连接放进nav元素即可。一个页面可以拥有多个nav，作为页面整体或不同部分的导航。

```html
<body>
    <h1>技术资料</h1>
    <nav>
    	<ul>
            <li><a href="/">主页</a></li>
            <li><a href="/events">开发文档</a></li>
            ...more...
        </ul>
    </nav>
    <article>
    	<header>
        	<h1>HTML5 与 CSS 3的历史</h1>
            <nav>
            	<ul>
                    <li><a href="#HTML 5">HTML 5的历史</a></li>
                    <li><a href="#CSS 3">CSS 3的历史</a></li>
                    ...more...
                </ul>
            </nav>
        </header>
        <section id="HTML 5">
        	<h1>HTML 5的历史</h1>
            <p>讲述HTML 5的历史正文</p>
        </section>
        <section id="CSS 3">
        	<h1>CSS 3的历史</h1>
            <p>讲述CSS 3的历史正文</p>
        </section>
        ...more...
        <footer>
        	<p>
                <a href="?edit">编辑</a>
                <a href="?delete">删除</a>
                <a href="?rename">重命名</a>
            </p>
        </footer>
    </article>
    <footer>
    	<p><small>版权所有：陆凌牛</small></p>
    </footer>
</body>
<!--
	以上内容，第一个nav用于页面导航，将页面跳转到其它页面上去；第二个nav元素放在article中，用作这篇文章中两个组成部分的页内导航。
	常用于：传统导航条、侧边导航栏、页内导航、翻页操作。
-->
```

## section

​	`<section>`用于对网站或应用程序中页面上的内容进行分块，一个section元素通常由内容及其标题组成。但section不是一个普通的容器，当一个容器需要被直接定义样式或通过脚本定义行为时，推荐使用div。不推荐为那些没有标题的section。

```html
<article>
	<h1>苹果</h1>
    <p><b>苹果</b>，植物类水果，多次花果。。。</p>
    <section>
    	<h2>红富士</h2>
        <p>红富士是从普通富士的芽（枝）变中选育出的着色系富的统称。。。</p>
    </section>
    <section>
    	<h2>国光</h2>
        <p>国光苹果品，又名小国光、万寿。原产美国，1600年发现的偶然实生苗。。。</p>
    </section>
</article>
<!--
	以上内容是一段独立、完整的内容，因此使用article，该内容是一篇关于苹果的文章，分为三段，每一段都有一个独立的标题，这里使用了两个section元素；对文章分段的工作也是使用section元素完成的。
	这里有个问题？为什么没有对第一段使用section，其实是可以使用section的，但是由于结构比较清晰，分析器可以识别第一段内容在一个section，所以也可以将第一个section略，但是如果第一个section还要包含子section、article，那么就必须写明第一个section。
-->
```

```html
<section>
	<h1>水果</h1>
    <article>
    	<h2>苹果</h2>
        <p>苹果，植物类水果，多次花果。。。</p>
    </article>
    <article>
    	<h2>橘子</h2>
        <p>橘子，是芸香柑橘属的一种水果。。。</p>
    </article>
    <article>
    	<h2>香蕉</h2>
        <p>香蕉，属于芭蕉科芭蕉属植物，又名其果实，热带地区广泛栽培食用。。。</p>
    </article>
</section>
<!--
	这段有几块独立的内容，article可以看成是一种特殊种类的section，比section更强调独立性；section强调分段或分块；article强调独立性；div是容器
-->
```

关于食用section的禁忌：

1. 不要将section用作设置样式的页面容器，那是div的工作；
2. 如果article元素、aside元素或nav元素更符合使用条件，不要使用section；
3. 不要为没有标题的内容区块使用section。

## details

​	`<details>`表示用户要求得到并且可以得到的细节信息。它可以与summary元素配合使用，summary元素提供标题或图例，标题是可见的，用户点击标题时，会显示细节信息。summary元素应该是detail元素的第一个子元素。（目前只有Chrome和Safari6支持，样式会添加一个黑色小三角图标，`summary::-webkit-details-marker{ display: none; }`禁用）

```html
<details>
	<summary>HTML 5</summary>
  This document teaches you everything you have to learn about HTML5.
</details>
```

## hgroup

`<hgroup>`整个页面或页面中一个内容区块的标题进行组合

```html
<!-- 文章只有一个主标题，是不需要hgroup -->
<article>
	<header>
    	<h1>文章标题</h1>
        <p><time datetime="2010-03-20">2010年10月29日</time></p>
    </header>
</article>

<article>
	<header>
    	<hgroup>
        	<h1>文章主标题</h1>
            <h2>文章子标题</h2>
        </hgroup>
        <p><time datetime="2010-03-20">2010年10月29日</time></p>
    </header>
    <p>文章正文</p>
</article>
```

## summary

​	`<summary>`（以前支持，现在正式列入规范）

## 二、用于标识文本的语意元素

## mark

​	`<mark>`主要用来在视觉上向用户呈现哪些需要突出显示或高亮显示的文字，mark的一个比较典型的应用就是在搜索结果中向用户高亮显示搜索关键词；mark是对原内容补充，用于一段原文作者不认为重要，但为了与原文作者不相关的其它目的而需要突出显示或高亮显示的文字上面。

```html
<!DOCTYPE html>
<meta charset="UTF-8"/>
<title>mark元素应用再网页检索时的实例</title>
<h1>搜索“<mark>HTML5</mark>”，找到相关网页约10,200,000篇，用时0.041秒</h1>
<section id="search-results">
	<article>
    	<h2>
            <a href="http://developer.51cto.com/art/200907/133407.htm">
            	专题：<mark>HTML5</mark>下一代web开发标准详解_51CTO.COM - 技术成就梦想...
            </a>
        </h2>
        <p><mark>HTML5</mark>是近十年来web开发标准最巨大的飞跃</p>
    </article>
    <article>
    	<h2>
            <a href="http://paranimage.com/list-of-html-5">
            	<mark>HTML5</mark>一览 | 帕兰映像
            </a>
            <p><mark>html5</mark>最近被讨论的越来越多，越来越烈...</p>
        </h2>
    </article>
    <article>
    	<h2>
            <a href="http://www.chinabyte.com/keyword/HTML+5">
            	<mark>html5</mark>_比特网
            </a>
        </h2>
        <p><mark>HTML 5</mark>提供了一些新的元素和属性，反映典型...</p>
    </article>
    <article>
    	<h2>
            <a href="http://www.slideshare.net/mienflying/HTML 5-4921810">
            	<mark>HTML 5</mark>表单
            </a>
        </h2>
        <p>about <mark>HTML 5</mark> From, the web from 2.0 tech</p>
    </article>
</section>
```

​	`mark`与`em`、`strong`的区别：`mark`标识的元素是与原文作者无关，或者说不是原文作用用来标示文字的，而是在后来引用时添加上去的，目的是吸引当前用户的注意力，提供给用户参考；而`strong`是用来强调一段文字，比如警告信息、错误信息，`em`元素是为了突出文章重点而使用的。

## time

​	`<time>`表示日期或时间，微格式——利用HTML的class属性来对网页添加附加信息的方法（如新闻事件发生的日期和事件、个人电话号码、企业邮箱等）。

```html
<time datetime="2010-11-13">2010年11月13日</time>
<time datetime="2010-11-13">11月13日</time>
<time datetime="2010-11-13">生日</time>
<time datetime="2010-11-13T20:00">生日晚上8点</time>
<time datetime="2010-11-13T20:00z">生日晚上8点</time>
<time datetime="2010-11-13T20:00+09:00">生日晚上8点的美国时间</time>
```

​	编码时机器读到的部分在datetime属性里，datetime中日期和事件之间要用T文字分隔，“T”表示时间，时间加上Z文字表示给机器编码时使用UTC标准时间，+后面是时差，表示向机器编码另一地区时间，如果编码本地时间，则不需要添加时差。
`pubdate: boolean`，可以用到article中的time，意思是time代表了文章（article）或整个网页的发布日期

```html
<article>
	<header>
    	<h1>苹果</h1>
        <p>发布日期
            <time datetime="2010-10-29" pubdate>2010年10月29日</time>
        </p>
    </header>
    <p>苹果，植物类水果，多次花果。。。（“苹果”文章正文）</p>
    。。。
</article>
```

​	为什么需要用到pubdate属性？不能将time直接表示文章或网页的发布日期？

```html
<article>
	<header>
    	<h1>关于<time datetime=2010-10-29>10月29日</time>的舞会通知</h1>
        <p>发布日期：
            <time datetime=2010-10-11 pubdate>2010年10月11日</time>
        </p>
    </header>
    <p>大家好：我是法律系3年级学生代表。。。（关于舞会的通知）</p>
</article>
<!--
	以上有两个time，分别定义了两个日期，一个舞会日期，一个通知发布日期；所以需要使用pubdate属性声明哪个time代表了通知的发布日期。
-->
```

## address

​	呈现文档中联系信息，包括作者或文档维护者的名字、网站链接、电子邮箱、真实地址、电话号码等。

```html
<address>
	<a href=#>陆凌牛</a>
    <a href=#>张玉</a>
    <a href=#>白权立</a>
</address>

<footer>
	<div>
        <address>
        	<a title="文章作者：陆凌牛" href="#">陆凌牛</a>
        </address>
        发表于<time datetime="2010-10-04">2010年10月4日</time>
    </div>
</footer>
```



## wbr

​	`<wbr>`软换行，wbr和br的区别是：br表示此处必须换行，wbr元素的意思是浏览器窗口或父级元素的宽度足够宽时（没必要换行时），不进行换行，而宽度不够时，主动在此处进行换行。*wbr元素对于中文没多大用处。*

## 三、web表单及交互

## input

`<input>`（增加类型）
	email：输入e-mail地址
	url：输入url地址
	number：输入数值，具有min、max、step属性
	range：输入一定范围内数字值，具有min、max属性，默认（0-100），还有step属性
	date：选取日、月、年
	month：选取月、年
	week：选取周和年
	time：选取时间（小时和分钟）
	datetime：选取时间、日、月、年（UTC时间）
	datetime-local：选取时间、日、月、年（本地时间）
	search：Safari4中的外观为操作系统默认的圆角矩形文本框，可以用css样式改写；其它浏览器中的外观与text相同， `input[type=search] { -webkit-appearance: textfield; }`
	tel：输入电话号码专用文本框，没有特殊的校验规则，不强制输入数字，如020-000000，但开发者可以通过pattern指定对于输入的电话号码格式验证。
	color：颜色选取器，只有Chrome和Opera支持。

## datalist

​	`<datalist>`可选数据的列表，与input元素配合使用，可以制作出输入框的下拉列表

## datagrid

​	`<datagrid>`可选数据的列表，以树形列表的形式来显示

## keygen

​	`<keygen>`生成密钥

## meter

​	`<meter>`表示规定范围内的数量值，例如：磁盘使用量，对某个候选者的投票人数占总投票人数的比例等。提供如下六个属性：

- value：实际值；默认0，可以是浮点小数值。
- min：最小值，默认0，不能小于0。
- max：最大值，如果小于min，那么min会被视为最大值，默认1。
- low：下限值，必须小于等于high，如果low小于min，那么min会被视为low的值。
- high：上限值，如果小于low，那么low会被视为high；如果大于max，那么max视为high。
- optimum：最佳值，必须在min与max之间，可以大于high。

```html
<p>磁盘使用量：<meter value="40" min="0" max="160">40/160</meter>GB</p>
<p>你的得分是：
    <meter value="91" min="0" max="100" low="40" high="90" optimum="100">A+</meter>
</p>
<!-- 不是一定要使用属性 -->
<meter>80%</meter>
<meter>3/4</meter>s
```

## progress

​	`<progress>`运行中的进程，可以用`progress`来显示`javascript`中耗费时间的函数的进程；`value`属性表示已经完成了多少工作量，`max`属性表示总共有多少工作量。工作量的单位是随意的，不用指定。在属性设定的时候，`value`和`max`只能指定为有效的浮点数，value必须大于0，且小于等于`max`，`max`值必须大于0。

```html
<!DOCTYPE html>
<meta charset="UTF-8"/>
<title>progress元素的使用示例</title>
<script>
var progressBar = document.getElementById('p');
function button_onclick() {
    var progressBar = document.getElementById('p');
    progressBar.getElementByTagName('span')[0].textContent = '0';
    for (var i = 0; i <= 100; i++) {
        updateProcess(i);
    }
}
function updateProgress(newValue) {
    var progressBar = document.getElementById('p');
    progressBar.value = newValue;
    progressBar.getElementsByTagName('span')[0].textContent = newValue;
}
</script>
<section>
	<h2>process元素的使用示例</h2>
    <p>完成百分比：<progress id="p" max=100><span>0</span>%</progress></p>
    <input type="button" onclick="button_onclick()" value="请点击"/>
</section>
```



## command

​	`<command>`命令按钮，比如单选按钮、复选框、按钮（目前主流浏览器不支持）

```html
<command onclick="cut()" label="cut">
```

## menu

​	`<menu>`菜单列表，当希望列出表单控件时使用该标签（目前主流浏览器不支持）

```html
<menu>
	<li><input type="checkbox" />Red</li>
    <li><input type="checkbox" />Blues</li>
</menu>
```

## output

​	`<output>`不同类型的输出，比如脚本的输出，output必须从属于某个表单。

```html
<!DOCTYPE html>
<head>
    <meta charset="utf-8">
    <title>output元素示例</title>
</head>
<form id="testform">
    请选择一个数值：
    <input name="range1" type=range min=0 max=100 step=5/>
    <output onforminput="value=range1.value">50</output>
</form>
```

## 四、音频、视频及插件

## audio

​	`<audio>`定义音频或其他音频流

```html
<audio src="someaudio.wav">
	您的浏览器不支持audio<!-- 不支持的audio元素的浏览器会显示的替代文字 -->
</audio>
```

## video

​	`<video>`定义视频，比如电影片段或其他视频流

```html
<video height="360" width="640" src="movie.ogg" controls="controls">
	您的浏览器不支持video
</video>
```

## source

​	`<source>`媒介元素，定义媒介资源，为同一个媒体数据指定多个播放格式与编码方式，以确保浏览器可以从中选择一种支持的格式进行播放，从上往下选择到支持的格式为止。

```html
<video>
    <!-- 在Ogg theora格式、Quicktime格式与mp4格式之间，挑选支持的播放格式 -->
    <source src='sample.ogv' type='video/ogg; codecs="theora, vorbis"'>
    <source src='sample.mov' type='video/quicktime'>
    <!-- type标识媒体类型，不要省略type属性，否则浏览器会从上往下选择时，无法判断自己能不能播放而先下载一小段视频（或音频），这样就有可能浪费带宽和时间 -->
</video>
```

浏览器支持的格式：

- IE9：支持H.264、vp8视频编码格式，MP3、WAV音频编码格式
- Firefox4：支持Ogg Theora、vp8视频编码格式，Ogg vorbis、WAV音频编码格式
- Opera10以上：支持Ogg Theora、vp8视频编码格式，Ogg vorbis、WAV音频编码格式
- Chrome6以上：支持H.264、Ogg Theora、vp8视频编码格式，Ogg vorbis、MP3音频编码格式

## auido和video属性

- `src`：指定媒体数据钟的URL地址

- `autoplay`：指定媒体是否在页面加载后自动播放

- `preload`：指定视频、音频数据是否预加载，如果使用预加载，浏览器会预先将视频、音频数据进行缓冲，加快播放速度。属性值为 `none`-不进行预加载，`metadata`-只预加载媒体的元数据（媒体字节数、第一帧、播放列表、持续时间等），`auto`（默认值）-预加载全部视频或音频。

- `poster`（video元素独有属性），当视频不可用，可以使用该元素向用户展示一张替代用的图片。

  ```html
  <video src="sample.mov" poster="cannotuse.jpg"></video>
  ```

- `loop`：指定是否循环播放视频或音频

- `controls`：为视频或音频添加浏览器自带播放用的控制条，控制条具有播放、暂停等按钮，也可以自定义控制条。

  ```html
  <video src="sample.mov" controls></video>
  ```

- `width`、`height`（`video`独有属性）：指定视频的宽度与高度，单位为像素。

- `error`：在读取、使用媒体数据过程中，只要出现错误，error属性将返回一个MediaError对象，该对象的code返回对应的错误状态，如下所示：

  - `MEDIA_ERR_ABORTED`：1，媒体数据下载过程由于用户的操作原因而被中止
  - `MEDIA_ERR_NETWORK`：2，确认媒体资源可用，但是在下载时出现网络错误，媒体数据的下载过程被中止
  - `MEDIA_ERR_DECODE`：3，确认媒体资源可用，但是解码时发生错误
  - `MEDIA_ERR_SRC_NOT_SUPPORTED`：4，媒体格式不支持

  ```html
  <video id="videoElement" src="sample.mov"></video>
  <script>
  	var video = document.getElementById('vedio');
      video.addEventListener('error', function() {
          var error = video.error;
          switch(error.code) {
              case 1:
                  alert('视频下载过程被中止');
                  break;
              case 2:
                  alert('网络发生故障，视频下载过程被中止');
                  break;
              case 3:
                  alert('解码失败');
                  break;
              case 4:
                  alert('不支持播放视频格式');
                  break;
          }
      }, false);
  </script>
  ```

- `networkState`：（只读）读取当前网络状态，如下所示：

  - `NETWORK_EMPTY`：0，元素处于初始状态
  - `NETWORK_IDLE`：1，浏览器已选择好用什么编码格式来播放媒体，但尚未建立网络连接
  - `NETWORK_LOADING`：2，媒体数据加载中
  - `NETWORK_NO_SOURCE`：3，没有支持的编码格式，不执行加载

  ```javascript
  var video = document.getElementById('video');
  video.addEventListener('progress', function(e) {
      var networkStateDisplay = document.getElementById('networkState');
      if (video.networkState == 2) {
          networkStateDisplay.innerHTML = '加载中...[' + e.loaded + ' / ' + e.total + ' byte]';
      } else if (video.networkState == 3) {
          networkStateDisplay.innerHTML = '加载失败';
      }
  }, false);
  ```

- `currentSrc`：（只读）读取播放中的媒体数据URL地址

- `buffered`：（只读）该属性返回一个实现TimeRanges接口的对象，以确认浏览器是否已缓存媒体数据，`TimeRanges`表示一段时间范围，时间范围是一个单一的以0开始的范围，但是如果浏览器发出`Range Requests`请求，这时`TimeRanges`表示的时间范围是多个时间范围。具有一个length，表示多少个时间范围，存在时间范围值为1，不存在为0；具有两个方法——`TimeRanges.start(index)`、`TimeRanges.end(index)`，多数情况下index为0，当用`videoElement.buffered`来实现`TimeRanges`接口，`TimeRanges.start(0)`表示当前缓存区从媒体数据的什么时间开始进行缓存，`TimeRanges.end(0)`表示当前缓存区域内的结束时间。

- `readyState`：（只读）返回媒体当前播放位置的就绪状态，值如下

  - `HAVE_NOTHING`：0，没有获取到媒体的任何信息，当前播放位置没有可播放数据
  - `HAVE_METADATA`：1，获取的媒体数据无效，不能播放
  - `HAVE_CURRENT_DATA`：2，当前帧的数据已获得，但还没有获取到下一帧的数据，或者当前帧已经是最后一帧
  - `HAVE_FUTURE_DATA`：3，当前帧的数据已获得，而且也获取到了下一帧的数据，当前帧是最后一帧时，不可能是`HAVE_FUTURE_DATA`
  - `HAVA_ENOUGH_DATA`：4，当前播放位置已经有数据可以播放，同时也获取到了可以让播放器前进的数据，而且浏览器确认媒体数据以某一种速度进行加载，可以保证有足够的后续数据进行播放

- `seeking: boolean`，（只读）表示浏览器是否正在请求某一特定播放位置的数据，true表示浏览器正在请求数据，false表示浏览器已停止请求。

- `seekable: TimeRanges`，（只读）请求到的数据的时间范围，开始时间为请求视频数据第一帧的时间，结束时间为请求到视频数据最后一帧的时间。

- `currentTime`：读取媒体的当前播放位置，可以修改currentTime来修改当前播放位置；如果修改的位置上没有可用的媒体数据，抛出`INVALID_STATE_ERR`；如果修改的位置超出了浏览器在一次请求中可以请求的数据范围，将抛出`INDEX_SIZE_ERR`。（单位：秒）

- `startTime`：（只读）读取媒体播放的开始时间，通常为0。（单位：秒）

- `duration`：（只读）读取媒体文件总的播放时间。（单位：秒）

- `played: TimeRanges`，（只读）读取媒体文件的已播放部分的时间段；开始时间为已播放部分的开始时间，结束时间为已播放部分的结束时间。

- `paused: boolean`，（只读）true暂停播放，false正在播放。

- `end: boolean`，（只读）true播放完毕，false没有播放完毕。

- `defaultPlaybackRate`：读取或修改媒体默认的播放速率。

- `playbackRate`：读取或修改媒体当前的播放速率。

- `volume: number`，读取或修改媒体的播放音量，范围0~1。

- `muted: boolean`，读取或修改媒体的静音状态，true静音，false非静音。

## audio和video方法

- `play()`，播放媒体，自动将元素的`paused`设置为`false`。

- `pause()`，暂停播放，自动将元素的`paused`设置为`true`。

- `load()`，重新载入媒体进行播放，自动将`playbackRate`设置为`defaultPlaybackRate`的值，自动将元素`error`设置为`null`。

- `canPlayType(type): string`，测试浏览器是否支持指定的媒体类型。

  ```javascript
  var support = videoElement.canPlayType(type);
  // 返回的值如下
  // 空字符串：表示浏览器不支持该媒体类型
  // maybe：表示浏览器可能支持该媒体类型
  // probably：表示浏览器确定支持该媒体类型
  ```

## audio和video事件

​	`videoElement.addEventListener(type, listener, useGapture)`：`type`为事件名称，`listener`表示绑定的函数，`useGapture`是一个布尔值，表示该事件的响应顺序，`true`浏览器采用`Capture`相应方式；`false`浏览器采用`bubbing`相应方式。

- `loadstart`：浏览器开始在网上寻找媒体数据。
- `progress`：浏览器正在获取媒体数据。
- `suspend`：浏览器暂停获取媒体数据，但是下载过程并没有正常结束。
- `abort`：浏览器在下载完全部媒体数据之前中止获取媒体数据，但是并不是由错误引起的。
- `error`：获取媒体数据过程中出错。
- `emptied`：video、audio所在网络突然变为初始化状态，可能引起的原因下面两点
  1. 载入媒体过程中濡染发生一个致命错误
  2. 在浏览器正在选择支持的播放格式时，又调用了load方法重新载入媒体
- `stalled`：浏览器尝试获取媒体数据失败。
- `play`：即将开始播放，当执行了play方法时出发，或数据下载后元素被设为autoplay（自动播放）属性。
- `pause`：播放暂停，当执行了pause方法时出发。
- `loadedmetadata`：浏览器获取完毕媒体的时间长和字节数。
- `loadeddata`：浏览器已加载完毕当前播放位置的媒体数据，准备播放。
- `waiting`：播放过程由于得不到下一帧而暂停播放（例如下一帧尚未加载完毕），但很快就能够得到下一帧。
- `playing`：正在播放。
- `canplay`：浏览器能够播放媒体，但估计以当前播放速率不能直接将媒体播放完毕，播放期间需要缓冲。
- `canplaythrough`：浏览器能够播放媒体，而且以当前播放速率能够将媒体播放完毕，不再需要进行缓冲。
- `seeking`：seeking属性变为true，浏览器正在请求数据。
- `seeked`：seeking属性变为false，浏览器停止请求数据。
- `timeupdate`：当前播放位置被改变，可能是播放过程中的自然改变，也可能是被认为地改变，或由于播放不能连续而发生的跳变。
- `ended`：播放结束后停止播放。
- `ratechange`：defaultplaybackRate属性（默认播放速率）或playbackRate属性（当前播放速率）被改变。
- `durationchange`：播放时长被改变。
- `volumechange`：volume、muted属性被改变。

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8"/>
        <title>视频播放器</title>
        <script>
        	function playOrPauseVideo() {
                var videoUrl = document.getElementById('videoUrl').value;
                var video = document.getElementById('video');
                video.addEventListener('timeupdate', function() {
                    var timeDisplay = document.getElementById('time');
                    timeDisplay.innerHTML = Math.floor(video.currentTime) + '/' + Math.floor(video.duration) + '（秒）';
                }, false);
                
                if (video.paused) {
                    if (videoUrl != video.src) {
                        video.src = videoUrl;
                        video.load();
                    } else {
                        video.play();
                    }
                    document.getElementById('playButton').value = '暂停';
                } else {
                    video.pause();
                    document.getElementById('playButton').value = '播放';
                }
            }
        </script>
    </head>
    <body>
        <video id="video" width="400" height="300" autoplay loop="loop"></video>
        <br />
        视频地址：<input type="text" id="videoUrl" />
        <input id="playButton" type="button" onclick="playOrPauseVideo()" value="播放" />
        <span id="time"></span>
    </body>
</html>
```



## embed

​	`<embed>`用来插入各种多媒体，格式可以是Midi、Wav、AIFF、AU、MP3(列入规范)

```html
<embed src="horse.wav"/>
```



## Canvas

这个元素本身没有行为，仅提供一块画布，把一个绘图api展现给客户端JavaScript，绘制内容到画布上。

## 非英语支持

- `<bdo>`

- `<rp>`ruby注释中使用，以定义不支持ruby元素的浏览器所显示的内容

- `<rt>`表示字符（中文注音或字符）的解释或发音

- `<ruby>`表示ruby注释（中文注音或字符）

  ```html
  <ruby>漢<rt><rp>(</rp>厂 乃'<rp>)</rp></rt></ ruby>
  ```

## 改良的ol列表

​	`HTML5`为`ol`添加了`start`(编号从几开始，默认1)和`reversed`（倒叙——目前只有Chrome和Safart6支持）。

## 改良的dl列表

​	HTML5对dl进行重新定义，dl列表包含多个带名字的列表项，每一项包含一条或多条待名字的dt元素，用来表示术语，dt后面紧跟一个或多个dd，用来表示定义；在一个元素内，不允许有相同名字的dt，不允许有重复的术语。
​	dt列表可以用来定义文章或网页上的术语解释

```html
<!DOCTYPE html>
<meta charset="UTF-8"/>
<title>用于术语解释的dl列表示例</title>
<h3>用于术语解释的dl列表示例</h3>
<article>
	<h1>article元素</h1>
    <p>一块独立的内容，可以用来表示RRS中一块独立的内容，也可以用来表示博客中独立的一篇文章...</p>
    <aside>
    	<h2>术语解释</h2>
        <dl>
            <dt><dfn>RSS</dfn></dt>
            <dd>RSS也叫聚合RSS是在线共享内容的一种简易方式（也叫聚合内容）...</dd>
            <dt><dfn>博客</dfn></dt>
            <dd>博客，又译为网络日志、部落格等，是一种通常有个人管理...</dd>
        </dl>
    </aside>
</article>

<!-- 辅助信息 -->
<dl>
    <dt>作者</dt>
    <dd>xxx</dd>
    <dt>出版社</dt>
    <dd>机械工业出版社</dd>
    <dt>类别</dt>
    <dd>网络开发</dd>
</dl>
```

## 加以严格限制的cite

​	HTML5规定不能用cite表示作者在内的任何人名，因为人名不是标题；cite表示作品（已被书、一篇文章、一首歌曲等）的标题。

```html
<!DOCTYPE html>
<meta charset="UTF-8"/>
<title>cite元素示例</title>
<h3>cite元素示例</h3>
<p>我最喜欢的电影是有甄子丹主演的<cite>精武风云</cite>。</p>
```

## 重新定义small

​	HTML5对samll重新定义，专门用来标识“小字印刷体”的元素，例如：免责声明、注意事项、法律规定、与版权相关等的法律性声明为中，同时不允许被应用在页面主内容中，只需要被当做辅助信息用online方式内嵌在页面上使用。同时，small元素不意味着字体会变小，需要配合css使用。

# HTML5删除元素

1. 纯表现的元素：~~basefont~~ (基准字体), ~~big~~(大号字体), ~~center~~(文本水平居中), ~~font~~(字体), ~~s~~(删除线文本), ~~strike~~(), ~~tt~~(等宽字体), ~~u~~(下划线文本)——由于它们的功能仅仅是为画面展示服务的，HTML5提倡把画面展示性功能放在CSS样式表中统一编辑，所以将这些元素删除了。

2. 对可用性产生负面影响的元素：~~frame~~, ~~frameset~~, ~~noframes~~ (特定窗口、框架)——由于~~frame~~框架对网页可用性存在负面影响，在HTML5中已不支持~~frame~~框架，只支持iframe框架。
3. 只有部分浏览器支持的元素：~~applet~~（embed、object替代）、~~bgsound~~（audio替代）、~~blink~~、~~marquee~~（JavaScript编程替代）等元素。
4. 其它被废除的元素（括号表示替代元素）
   - ~~rb~~（ruby）
   - ~~acronym~~（abbr）
   - ~~dir~~（ul）
   - ~~isindex~~（form、input相结合替代）
   - ~~listing~~（pre）
   - ~~xmp~~（code）
   - ~~nextid~~（GUIDS）
   - ~~plaintext~~（text/plian）MIME类型替代

参考：https://www.w3school.com.cn/tags/tag_comment.asp

# HTML5新增的属性

## 表单

- `input[type=text]`、`select`、`textarea`、`button`指定`autofocus`属性，它以指定属性的方式让元素再画面打开时自动获取焦点。
- `input[type=text]`、`textarea`指定`placeholder`，会对用户的输入进行提示，提示用户可以输入的内容。
- `input`、`output`、`select`、`textarea`、`button`、`fieldset`指定`from`属性，声明它属于哪个表单，然后将其放置在页面上的任何位置，而不是表单之内。
- `input[type=text]`、`textarea`元素指定`required`属性，表示用户提交的时候进行检查，检查该元素内一定要有输入内容。
- `input`增加了几个新的属性：`autocomplete`、`min`、`max`、`multiple`、`pattern`、`step`；同时还有一个新的`list`元素与`datalist`元素配合使用，`datalist`元素与`autocomplete`属性配合使用；`multiple`属性允许再上传文件时一次性上传多个文件。
- `input`、`button`添加新属性`formaction`、`formenctype`、`formmethod`、`formnovalidate`、`formtarget`，可以重载`form`元素的`action`、`enctype`、`method`、`novalidate`、`target`属性；为`fieldset`元素增加disabled属性，可以把它的子元素设为`disabled`（无效）状态。
- `input`、`button`、`form`添加了`novaldate`属性，该属性可以取消提交时进行有关的检查，表单可以被无条件地提交。

## form: string

​	form属性用来给input等表单元素指向某个表单id；除了IE，几乎所有的主流浏览器都支持form属性；

```html
<!-- input属于testform表单，被写在表单内容，不需要再指定form属性。textarea写在表单之外，但又属于testform，需要指定form；表单元素不再需要一定放置在表单层级性 -->
<form id="testform">
    <input type="text">
</form>
<textarea form="testform"></textarea>
```

## formaction: string

​	表单中所有的提交按钮增加不同的formaction属性，使得点击不同的按钮，可以将表单提交到不同的页面；IE9及之前不支持该属性。

```html
<form id="testform" action="server.action">
    <input type="submit" name="s1" value="s1" formaction="s1.action">提交到s1
     <input type="submit" name="s2" value="s2" formaction="s2.action">提交到s2
     <input type="submit" name="s3" value="s3" formaction="s3.action">提交到s3
</form>
```

## formmethod: post | get | put | delete...string

​	使用formaction对每个表单元素分别指向不同的提交页面，同时formmethod属性来对每个表单元素分别指定不同的提交方法；IE9及之前不支持该属性。

```html
<form id="testform" action="server.action">
    <input type="submit" name="s1" value="s1" formaction="s1.action" formmethod="post">post提交到s1
     <input type="submit" name="s2" value="s2" formaction="s2.action" formmethod="get">get提交到s2
</form>
```

## placeholder: string

​	当文本框input[type=text]、textarea处于未输入状态并且未获取光标焦点时文本框中显示的输入提示；IE9及之前不支持该属性。

```html
<input type="text" placeholder="input me">
```

## autofocus

​	当页面打开时，该控件自动获得光标焦点；一个页面上只能有一个控件具有该属性，不能随便滥用该属性；IE9及之前不支持该属性。

```html
<input type="text" autofocus>
```

## list: string

​	为单行文本框input[type=text]添加一个list属性，该属性的值为某个datalist的id，类似于选择框；Safari和IE9及之前不支持。

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>list属性示例</title>
</head>
test: <input type="text" name="greeting" list="greetings">
<!-- 将datalist设定为不显示 -->
<datalist id="greetings" style="display: none;">
	<option value="Good Morning">Good Morning</option>
    <option value="Hello">Hello</option>
    <option value="Good Afternoon">Good Afternoon</option>
</datalist>
```

## autocomplete: on | off

​	辅助输入所有的自动完成功能；所有浏览器都支持；在某些浏览器，可能需要手动启用完成该功能（例如在浏览器菜单的参数设置）

## required

​	required可以应用到大多数输入元素上（除了隐藏元素、图片元素按钮上）。在提交时，如果元素中内容为空白，则不允许提交，同时在浏览器中显示信息提示文字，提示用户这个元素中必须输入内容。

## pattern

​	根据新增input[type=email、number、url]等，要求输入内容符合一定的格式，值为某个格式的正则表达式，在提交时会检查内容是否符合格式；不符合格式的时候，则不允许提交，同时在浏览器中显示信息提示文字。

```html
<input pattern="[0-9][A-Z]{3}" name=part placeholder="输入内容：一个数字与三个大写字母。">
```

## min、max

​	数值、日期类型的input专用属性，限制输入的范围。

## step

​	控制input中值的增加、减少时的步幅；例如输入的值0与100之间，但必须是5的倍数，可以指定step为5。

## accept

​	选择文件窗口时，只能选的文件类型；如果选择其他类型文件，file控件也能正常接受。

## 显示验证

​	除了以上required、pattern的自动验证外，form、input、select、textarea都具有一个checkValidity，可以显示地对元素进行验证；form和input还存在一个validity属性，返回一个ValidityState对象，该对象具有很多属性，但最简单、最重要的是valid属性，代表表单内所有元素内容是否有效或单个input内容是否有效。

```html
<!DOCTYPE html>
<meta charset=UTF-8 />
<title>checkValidity实例</title>
<script language="javascript">
function check() {
    var email = document.getElementById("email");
    if (email.value === "") {
        alert("请输入Email地址");
        return false;
    }
    else if (!email.checkValidity()) {
        alert("请输入正确的Email地址");
    }
    else {
        alert("您输入的Email地址有效");
    }
}
</script>
<form id=testform onsumbit="return check()">
    <label for=email>Email</label>
    <input name=email id=email type=email><br/>
    <input type=submit>
</form>
```

## 取消验证

​	利用form、input、sumbit的formnovalidate可以关闭表单的验证。

## 自定义错误信息

​	使用JavaScript调用各input的setCustomValidity来自定义错误信息。



## 链接

- `a`、`area`添加了`media`，规定目标URL是什么类型的媒介/设备进行优化的，只能在`href`属性存在时使用。
- `area`添加`hreflang`、`rel`，以保持与`a`、`link`元素一致。
- `link`添加了`sizes`，该属性可以与`icon`元素结合使用（通过`rel`属性），用于指定关联图标（`icon`元素）的大小。
- `base`添加`target`属性，保持与a元素一致。

## 其它属性

- `ol`添加`reversed`，指定列表倒序显示
- `meta`添加`charset`，该属性已经被广泛支持了，而且为文档的字符编码指定提供了一种比较良好的方式。
- `menu`添加`type`、`label`，`label`为菜单定义一个可见的标注，`type`让菜单可以以上下文菜单、工具条与列表菜单的三种形式出现。
- `style`添加`scoped`属性，用来规定样式的作用范围，比如只对页面上某个数起作用。
- `script`添加`async`属性，定义脚本是否异步执行。
- `html`添加`manifest`，开发离线web应用程序时它与api结合使用，定义一个URL，在这个URL上描述文档的缓存信息。
- `iframe`元素添加三个属性`sandbox`、`seamless`、`srcdoc`，用来提供页面安全性，防止不信任的`web`页面执行某些操作。

## 废除的属性

| HTML4使用的属性                                              | 使用该属性的元素                     | HTML5替代方案                                                |
| ------------------------------------------------------------ | ------------------------------------ | ------------------------------------------------------------ |
| rev                                                          | link、a                              | rel                                                          |
| charset                                                      | link、a                              | 在被链接的资源中使用HTTP Content-type头元素                  |
| shape、coords                                                | a                                    | 使用area元素代替a元素                                        |
| longdesc                                                     | img、iframe                          | 使用a元素链接到较长描述                                      |
| target                                                       | link                                 | 多余属性，被省略                                             |
| nohref                                                       | area                                 | 多余属性，被省略                                             |
| profile                                                      | head                                 | 多余属性，被省略                                             |
| version                                                      | html                                 | 多余属性，被省略                                             |
| name                                                         | img                                  | id                                                           |
| scheme                                                       | meta                                 | 只为某个表单域使用scheme                                     |
| archive、classid、codebase、codetype、declare、sttandby      | object                               | 使用data与type属性类调用插件，需要使用这些属性来设置参数时，使用param属性 |
| valuetype、type                                              | param                                | 使用name与value属性，不声明值的MIME类型                      |
| axis、abbr                                                   | td、th                               | 使用以明确简洁的文字开头、后跟详述文字的形式。可以对更详细的内容使用title属性，来使单元格的内容变得简短 |
| scope                                                        | td                                   | 在被链接的资源中使用HTTP Content-type头元素                  |
| align                                                        | caption、input legend、div、h1~h6、p | 使用css样式替代                                              |
| alink、link、text、vlink、background、bgcolor                | body                                 | 使用css样式替代                                              |
| align、bgcolor、border、cellpadding、cellspacing、frame、rules、width | table                                | 使用css样式替代                                              |
| align、char、charoff、height、nowrap、valign                 | tbody、thead、tfoot                  | 使用css样式替代                                              |
| align、bgcolor、char、charoff、height、nowrap、valign、width | td、th                               | 使用css样式替代                                              |
| align、bgcolor、char、charoff、valign                        | tr                                   | 使用css样式替代                                              |
| align、char、charoff、valign、width                          | col、colgroup                        | 使用css样式替代                                              |
| align、border、hspace、vspace                                | object                               | 使用css样式替代                                              |
| clear                                                        | br                                   | 使用css样式替代                                              |
| compact、type                                                | ol、ul、li                           | 使用css样式替代                                              |
| compact                                                      | dl、menu                             | 使用css样式替代                                              |
| width                                                        | pre                                  | 使用css样式替代                                              |
| align、hspace、vspace                                        | img                                  | 使用css样式替代                                              |
| align、noshade、size、width                                  | hr                                   | 使用css样式替代                                              |
| align、frameborder、scrolling、marginheight、marginwidth     | iframe                               | 使用css样式替代                                              |
| autosubmit                                                   | menu                                 |                                                              |

# 全局属性

## contentEditable: boolean

​	由微软开发、被其它浏览器反编译并投入应用的一个全局属性，允许用户编辑元素的内容；如果父元素是可编辑的，那么该元素也是可编辑的。除了contentEditable属性外，元素还具有一个isContentEditable属性，当元素可编辑，该属性为true；不可编辑为false。
​	支持contentEditable属性的元素：defaults、a、abbr、acronym、address、b、bdo、big、blockquote、body、button、center、cite、code、custom、dd、del、dfn、dir、div、dl、dt、em、fieldset、font、form、hn、i、input[type=button、password、radio、reset、submit、text、ins、isindex、kbd、label]。

## designMode: on | off

​	制定真个页面是否可编辑，当页面可编辑时，页面中任何支持contentEditable属性的元素都变成了可编辑状态；designMode属性只能在JavaScript脚本里被编辑修改， on可编辑；off不可编辑。

```javascript
document.designMode = 'on';
```

​	兼容性：

- IE8：出于安全考略，不允许使用designMode让页面进入编辑状态。
- IE8：允许使用。
- Chrome3、Safari：使用内嵌frame的方式，该frame是可编辑的。
- Firefox、Opera：允许使用。

## hidden: boolean

​	所有元素都允许使用hidden，类似于input中的hidden，true元素处于不可见状态；false处于可见状态。

## spellcheck: boolean

​	HTML5针对input[type=text]与textarea这两个文本框提供的新属性，对用户输入的文本内容进行拼写和语法检查。在书写的时候必须声明属性值是true、false；如果元素的readOnly属性或diabled属性为true，则不执行拼写检查。

```html
<textarea spellcheck="true">
<input type=text spellcheck=false>
```

## tabIndex

​	tabindex是开发中的一个基本概念，当不断敲击tab键让窗口或页面中的空间获得焦点，对窗口或页面中的所有控件进行遍历的时候，每个空间的tabindex表示该控件时第几个被访问到的。过去这个属性在编辑网页非常拥有，但是现在空间的遍历顺序是由元素在页面上所处的位置决定的，所以就不再需要了。
​	tabindex还有另外一个作用，在默认情况下，只有链接元素与表单元素可以通过按键获得焦点。如果对其它元素使用tabindex后，也能让元素获得焦点，那么当脚本执行focus()时，就可以让该元素获得焦点了。可会产生一个副作用：该元素也可以通过tab键获得焦点，而这有时候可能不是开发者想要的结果。把tabindex设为负数（-1）后就可以解决这个问题，当tabindex为负数，仍然可以通过编程的方式让元素获得焦点，但按下tab该元素就并不能获得焦点了。

# 显示编排内容区块

​	明确使用section等元素创建文档结果，在每个内容区内使用标题h1~h6、hgroup等。显示编排更加清晰、易读。

```html
<body>
    <h1>网页级内容区块标题</h1>
    <p>网页级内容区块的正文</p>
    <section>
    	<h2>section级内容区块的标题</h2>
        <p>section级内容的正文</p>
    </section>
</body>
```

# 隐式编排内容区块

​	不明确使用section等元素，而是根据页面中所写的各级标题h1~h6、hgroup等把内容区块自动创建出来；HTML5分析器只要看到某个级别的标题，就会判断存在相对应的内容区块。由于隐式编排容易引起文档结构混乱，尽量使用显示编排。

```html
<body>
    <h1>网页级内容区块标题</h1>
    <p>网页级内容区块的正文</p>
    <!-- 分析器根据h2等元素判断生成内容区块 -->
    <h2>section级内容区块的标题</h2>
    <p>section级内容区块的正文</p>
</body>
```

# 标题分级

​	不同的标题有不同的级别；隐式编排时按如下规则自动生成内容区块

- 如果新出现的标题比上一个标题级别低，生成下级内容区块
- 如果新出现的标题比上一个标题级别高或相等，生成新的内容区块

```html
<body>
    <section>
    	<h2>section级别的内容区块的标题</h2>
        <p>section级别的内容区块的正文</p>
        <!-- 下面的标题级别比上一个标题级别高，自动创建新的内容区块 -->
        <h1>新的section级别的内容区块的标题</h1>
        <p>新的section级别的内容区块的正文</p>
    </section>
</body>

<!-- 上面如下显示编排 -->
<body>
    <section>
    	<h2>section级别的内容区块的标题</h2>
        <p>section级别的内容区块的正文</p>
    </section>
    <section>
        <h1>新的section级别的内容区块的标题</h1>
        <p>新的section级别的内容区块的正文</p>
    </section>
</body>
```

# 不同的内容区块可以使用相同级别的标题

​	父与子内容区块可以使用相同级别的标题h1，这样做的好处是：每个级别的标题都可以单独设计，如果既需要“整个网页的标题”，又需要“文章的标题”（比如书写文档时），这样做将会带来很大的便利。

```html
<body>
    <h1>网页的标题</h1>
    <article>
    	<header>
        	<hgroup>
            	<h1>文章标题</h1>
                <h2>文章子标题</h2>
            </hgroup>
            <p>文章正文</p>
        </header>
    </article>
</body>
```

# 网页编排实例

```html
<!DOCTYPE html>
<head>
    <title>网页编排实例</title>
    <meta charset="UTF-8">
</head>
<body>
    <!-- 网页标题 -->
    <header>
    	<h1>网页标题</h1>
        <nav>
        	<ul>
                <li><a href="#">首页</a></li>
                <li><a href="#">帮助</a></li>
            </ul>
        </nav>
    </header>
    <!-- 文章正文 -->
    <article>
    	<hgroup>
        	<h1>文章主标题</h1>
            <h2>文章子标题</h2>
        </hgroup>
        <p>文章正文</p>
        <!-- 文章评论 -->
        <section class="comments">
        	<article>
            	<h1>评论标题</h1>
                <p>评论正文</p>
            </article>
        </section>
    </article>
    <!-- 版权信息 -->
    <footer>
    	<small>版权所有：xxx</small>
    </footer>
</body>
```

# 对新的结构元素使用样式

```css
article, aside, dialog, figure, footer, header, legend, nav, section { display: block; }
```

IE 8及之前的浏览器不支持css的方法来使用这些尚未支持的结构元素，需要使用JavaScript

```html
<script>
document.createElement('header');
document.createElement('nav');
document.createElement('article');
document.createElement('footer');
</script>
```

# 文件API

## FileList、file

​	HTML5，input[type=file]添加multiple属性，允许一次防止多个文件，用户选择的文件都是一个file对象，而FileList是file对象的列表，代表用户选择的所有文件，file有两个属性：name表示文件名，不包括路径；lastModifiedDate标识文件的最后修改日期。

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>FileList与file示例</title>
</head>
<script>
    function ShowFileName() {
        var file;
        for(var i = 0; i < document.getElementById('file').files.length; i++) {
            file = document.getElementById('file').files[i];
            alert(file.name);
        }
    }
</script>
选择文件：
<input type="file" id="file" multiple size="80">
<input type="button" onclick="ShowFileName()" value="文件上传">
```

## Blob

​	`Blob`表示二进制原始数据，它提供一个`slice`方法，可以通过该方法访问到字节内部的原始数据块。上面的`file`对象也继承了`Blob`。
​	`Blob`有两个属性：`size`-字节长度，`type`-`Blob`的`MIME`类型，如果是未知类型，返回一个空字符串。

## FileReader接口

​	FIleReader接口主要用来把文件读入内存，并且读取文件中的数据。提供一个异步api，可以在浏览器主线程中异步访问文件系统，读取文件中的数据。
​	提供的方法如下，3个用以读取文件，另一个用来读取过程中断。无论读取成功或失败，方法并不会返回读取结果，这一结果存储在result属性中。

- readAsBinaryString(file)：将文件读取为二进制码，二进制字符串。
- readAsText(file, [encoding])：将文件读取为文本，第二个参数是文本的编码方式，默认为UTF-8。
- readAsDataURL(file)：将文件读取为DataURL，将小文件以一种特殊格式的URL地址形式直接读入页面，通常是指图像、html等。
- abort()：中断读取操作。

​	FileReader包含了一套完整的事件模型，用于捕获读取文件时的状态：

- onabort：数据读取中断时触发
- onerror：数据读取出错时触发
- onloadstart：数据读取开始时触发
- onprogress：数据读取中
- onload：数据读取成功完成时触发
- onloadend：数据读取完成时触发，无论成功或失败

# 拖放API

​	HTML5提供了支持拖放操作的API，且支持在浏览器与其他应用程序之间的数据相互拖动，同时也大大简化了有关拖放方面的代码。

## 实现拖放

1. 将想要拖放的元素draggable属性设为true；img和a（必须指定href）默认允许拖放。
2. 编写与拖放有关的事件处理代码。相关事件如下：

| 事件      | 产生事件的元素           | 描述                               |
| --------- | ------------------------ | ---------------------------------- |
| dragstart | 被拖放的元素             | 开始拖放操作                       |
| drag      | 被拖放的元素             | 拖放过程中                         |
| dragenter | 拖放过程中鼠标经过的元素 | 被拖放的元素开始进入本元素的范围内 |
| dragover  | 拖放过程中鼠标经过的元素 | 被拖放的元素离开本元素的范围       |
| dragleave | 拖放过程中鼠标经过的元素 | 被拖放的元素离开本元素的范围       |
| drop      | 拖放的目标元素           | 有其他元素被拖放到了本元素中       |
| dragend   | 拖放的对象元素           | 拖放操作结束                       |

```html
<!DOCTYPE html>
<head>
  <meta charset="UTF-8">
  <title>拖放实例</title>
  <script>
  function init() {
    var source = document.getElementById('dragme');
    var dest = document.getElementById('text');
    // 拖放开始
    source.addEventListener('dragstart', function(ev) {
      // 把要拖动的数据存入DataTransfer
      var dt = ev.dataTransfer;
      dt.effectAllowed = 'all';
      // setData，第一个参数表述数据种类的字符串，只能填入类似'text/plain'/'text/html'表示的MIME文字
      // 支持拖动处理的MIME类型有以下几种：
      // 	text/plain: 文本文字；
      // 	text/html: HTML文字；
      //  text/xml: XML文字；
      //  text/uri-list: URL列表，每个URL为一行
      // 如果设置为dt.setData('text/plain', this.id); 把被拖动元素的id当成了参数，携带的数据就是被拖动元素中的数据
      // 浏览器中使用getData()方法读取数据时会自动读取该元素中的数据
      dt.setData('text/plain', '你好');
    }, false);
    // 拖放结束
    dest.addEventListener('dragend', function(ev) {
      // 不执行默认处理（拒绝被拖放）
      // 针对拖放的目标元素，必须在dragend、dragover事件内调用preventDefault()
      // 因为默认情况下，拖放的目标元素是不允许接受元素的，为了把元素拖放到其中，必须把默认处理给关闭掉
      ev.preventDefault();
    }, false);
    // drop 被拖放
    dest.addEventListener('drop', function(ev) {
      var dt = ev.dataTransfer;
      var text = dt.getData('text/plain');
      dest.textContent += text;
      // 不执行默认处理（拒绝被拖放）
      ev.preventDefault();
      // 停止事件传播
      ev.stopPropagation();
    }, false);
    // 设置页面属性，不执行默认处理（拒绝被拖放）
    // 实现拖放过程，必须设定整个页面为不执行默认处理（拒绝被拖放），否则拖放处理将不能被实现；因为页面是先于其他元素接受释放的，如果页面上拒绝释放，那么页面上其他元素就都不能接受释放了。
    // 要使元素可以被拖放，首先必须把该元素的draggable属性设为true；接着，为了让这个实例在所有支持拖放API的浏览器中都能正常运行，需要指定 '-webkit-user-drag: element'
    document.ondragover = function(e) { e.preventDefault(); };
    document.ondrop = function(e) { e.preventDefault(); };
  }
  </script>
</head>
<body onload="init()">
  <h1>简单拖放实例</h1>
  <div id="dragme" draggable="true" style="width: 200px; border: 1px solid gray;">
    	请拖放
  </div>
  <div id="text" style="width: 200px; height: 200px; border: 1px solid gray;"></div>
</body>
```

## DataTransfer对象

- dropEffect：表示拖放操作的视觉效果，允许对其进行值的设定；该效果必须在用effectAllowed属性指定允许的视觉效果范围内，允许指定的值为none、copy、link、move
- effectAllowed：用来指定当元素被拖放时所允许的视觉效果，可以指定的值为none、copy、copyLink、copyMove、link、linkMove、move、all、unintialize
- types：存入数据的种类，字符串的伪数组
- void clearData(DOMString format)：清除DataTransfer对象中存放的数据，如果省略format，则清除全部数据
- void setData(DOMString format, DOMStrung data)：向DataTransfer对象内存入数据
- DOMString getData(DOMString format)：从DataTransfer对象中读数据
- void setDragImage(Element image, long x, long y)：用img元素来设置拖放图标（部分浏览器中可以用canvas等其他元素来设置）

## 设定拖放时的视觉效果

​	dropEffect、effectAllowed结合可以设定拖放的视觉效果；
​	effectAllowed属性表示当一个元素被拖动时所允许的视觉效果，多数在ondragstart事件中设定，值为none、copy、copyLink、copyMove、link、linkMove、move、all、unintialize。
​	dropEffect属性表示实际拖放时的视觉效果，多数在ondragover中指定，允许设定的值为none、copt、link、move。
​	dropEffect所表示的视觉效果必须在effectAllowed所表示的允许的视觉效果范围内。规则如下所示：

- 如果effectAllowed为none，则不允许拖放元素。
- 如果dropEffect为none，则不允许被拖放到目标元素中。
- 如果effectAllowed为all或不设定，则dropEffect属性允许被设定为任何值，并且按指定的视觉效果进行。
- 如果effectAllowed设定为具体效果（不为none、all），dropEffect也设定了具体视觉效果，则两个具体效果值必须完全相等，否则不允许将拖放元素拖放到目标元素中。

```javascript
source.addEventListener('dragstart', function(ev) {
  var dt = ev.dataTransfer;
  dt.effectAllowed = 'copy';
  dt.setData('text/plain', '你好');
}, false);
dest.addEventListener('dragover', function(ev) {
  var dt = ev.dataTransfer;
  dt.dropEffect = 'copy';
  ev.preventDefault();
}, false);
```

## 自定义拖放图标

​	setDragImage(Element image, long x, long y)：鼠标拖动元素过程中，位于鼠标指针下部的小图标。
第一个参数image设定为拖放图标的图标元素；第二个参数x为拖放图标离鼠标指针x轴距离；第三个参数y为拖放图标离鼠标指针y轴距离。

```javascript
var dragIcon = document.createElement('img');
dragIcon.src = 'http://twivatar.org/twitter/mini';
source.addEventListener('dragstart', function(ev) {
  var dt = ev.dataTransfer;
  dt.setDragImage(dragIcon, -10, -10);
  dt.setData('text/plain', 'aaa');
}, false);
```

# 本地存储

​	Web Storage与本地数据库，Web Storage存储机制是对HTML 4中cookies存储机制的一个改善，由于cookies存储机制有很多缺点，HTML 5中不再使用它，转而使用改良后的Web Storage存储机制。

## cooloes储存永久数据存在的问题

- 大小：cookies的大小被限制在4KB。
- 带宽：cookies是随HTTP事务一起被发送的，因此会浪费一部分发送cookies时使用的带宽。
- 复杂性：要正确地操作cookies是很困难的。

## sessionStorage

​	临时保存从用户进入网站到关闭浏览器窗口这段时间内保存的任何数据。

```javascript
sessionStorage.setItem(key: string, value: string): void;
sessionStorage.getItem(key: string): string;
```

## localStorage

​	将数据永久地保存在客户端本地的硬件设备中，即使浏览器关闭了，数据仍然存在。

```javascript
localStorage.setItem(key: string, value: string): void;
localStorage.getItem(key: string): string;
```

# 本地数据库

​	在HTML4中，数据库只能放在服务端，但在HTML 5中，可以像访问本地文件那样轻松地对内置数据库进行直接访问。这种本地数据库被称为“SQLLite”的文件型SQL数据库；实际应用中有两个必要的步骤：1. 创建访问数据库的对象；2. 使用事务处理。

```javascript
/*
	@param1 数据库名
	@param2 版本号
	@param3 数据库描述
	@param4 数据库大小
*/
var db = openDatabase('mydb', '1.0', 'Test DB', 2 * 1024 * 1024);
// transaction执行事务处理
db.transaction(function (tx) {
    tx.executeSql('CREATE TABLE IF NOT EXISTS LOGS (id unique, Log)');
});
```

## 创建数据表

`CREATE TABLE IF NOT EXISTS MsgData(name TEXT, message TEXT, time INTEGER)`

## executeSql 查询

```javascript
/*
	@param1 需要执行的SQL语句
	@param2 SQL语句中使用到的参数数组，第一个参数SQL语句中参数占位符"?"，依次对应第二个参数的入参
	@param3 执行SQL成功回调
	@param4 执行SQL出错回调
*/
transaction.executeSql(sqlquery, [], dataHandler, errorHandler);
// demo
transaction.executeSql(
    'UPDATE people set age=? where name=?;',
    [age, name],
    function(transaction, results) {
        
    },
    function(transaction, errmsg) {
        
    }
);
```

# 离线Web应用程序

​	HTML 5新增了让Web应用在离线状态的时候也能正常工作，把web应用的HTML、CSS、JavaScript等放到本地缓存，当服务器没有和Internet建立连接的时候，可以利用本地缓存中的资源文件来正常运行web应用程序。

## 本地缓存与浏览器缓存的区别

​	**本地缓存**是整个Web应用程序的，只缓存那些指定缓存的网页，本地缓存是可靠的，可以控制对哪些内容进行缓存，开发人员还可以用编程来控制缓存的更新，利用缓存对象的各种属性、状态和时间来开发出更为强大的离线应用程序。
​	**浏览器缓存**只是单个网页，任何网页都具有网页缓存；网页缓存是不安全、不可靠的，因为我们不知道在网站中到底缓存了哪些网页，缓存了网页的哪些资源；

## manifest文件

​	Web应用程序的本地缓存是通过每个页面的`manifest`文件来管理的，配置内容如下：

```sh
CACHE MANIFEST
# 文件的开头必须要书写 CACHE MANIFEST，把本间的作用告知浏览器
version 7
# 最好添加一个版本号，更新manifest文件的时候也会更新版本号
CACHE
# CACHE 指定需要被缓存在本地的资源文件，为某个页面指定需要本地缓存的资源文件，不需要把这个文件本身指定在CACHE类别中，如果一个页面具有manifest文件，浏览器会自动对这个页面进行本地缓存
other.html
hello.js
images/myphoto.jpg
NETWORK
# NETWORK 显示指定不进行本地缓存的资源文件，这些资源文件只有当客户端与服务端建立连接的时候才能访问，下面示例中的*为通配符，标识没有在manifest中指定的资源文件都不进行本地缓存
http://xxx/xxx
Notoffline.asp
*
FALLBACK
# FALLBACK 每行指定两个资源文件，第一个资源文件是能够在线访问时使用的资源文件，第二个资源文件是不能在线访问时使用的备用资源文件
online.js locale.js
CACHE
# 允许重复书写同一类别，追加CACHE类别中的内容
newhello.html
newhello.js

# 每个类别都是可选的，但是如果文件开头没有指定类别而直接书写资源文件的时候，浏览器把这些资源文件视为CACHE类别，直到看见文件中第一个被书写出来的类别为止
```

​	同时，真正运行或测试离线`Web`应用程序的时候，需要对服务器进行设置，让服务器支持`text/cache-manifest`这个`MIME`类型（HTML 5中规定manifest文件的MIME类型是 `text/cache-manifest`）；例如对Apache服务器进行配置的时候，需要找到`{apache_home}/conf/mime.types`文件，并在文件最后添加 `text/cache-manifest manifest`。
​	在微软的IIS服务器中的步骤如下：
1）右键选择默认网站或需要添加类型的网站，弹出属性对话框
2）选择“HTTP头”标签
3）在MIME映射下，单机文件类型按钮
4）在打开的MIME类型对话框中单机新建按钮
5）在关联扩展名文本框中输入“manifest”，在内容类型文本框中输入“text/cache-manifest”，然后单击确定按钮

需要在Web应用程序页面上的html标签的manifest属性指定manifest文件的URL地址，让浏览器能够正常阅读该文本文件

```html
<!-- 可以为每个页面单独指定一个manifest文件 -->
<html manifest="hello.manifest">
    
</html>

<!-- 也可以为整个Web应用程序指定一个总的manifest文件 -->
<html manifest="global.manifest">
    
</html>
```

通过以上步骤，将资源文件保存到本地缓存区。只要修改manifest文件，浏览器可以自动检查manifest文件，并自动更新本地缓存区中的内容。

# 浏览器与服务器的交互过程

1. 浏览器请求访问网址：http://xxx.com。
2. 服务器返回index.html。
3. 浏览器解析index.html，请求网页上所有资源文件，包括HTML、图像、css、JavaScript、manifest文件。
4. 服务器返回所有资源文件。
5. 浏览器处理manifest文件，请求manifest中所有要求本地缓存的文件，包括index.html页面本身，即使刚才已经请求过这些文件；如果要求本地缓存所有文件，这将是一个比较大的重复请求过程。
6. 服务器返回要求本地缓存的文件。
7. 浏览器对本地缓存进行更新，存入包括页面本身在内容的所有要求本地缓存的资源文件，并且触发一个事件，通知本地缓存被更新。

现在浏览器已经把本地缓存更新完毕，再次打开http://xxx.com，在manifest文件没有被修改过的情况下，交互过程如下：

1. 浏览器再次请求访问网址：http://xxx.com。
2. 浏览器发现这个页面被本地缓存，于是使用本地缓存中index.html。
3. 浏览器解析index.html。使用所有本地缓存中的资源文件。
4. 浏览器向服务器请求manifest文件。
5. 服务器返回一个304，通知浏览器manifest没有发生变化。

如果manifest文件被更新过了，那么交互过程如下：

1. 浏览器再次请求访问网址：http://xxx.com。
2. 浏览器发现这个页面被本地缓存，于是使用本地缓存中index.html。
3. 浏览器解析index.html，使用所有本地缓存中的资源文件。
4. 浏览器向服务器请求manifest文件。
5. 服务器返回更新过的manifest文件。
6. 浏览器处理manifest文件，发现有更新，于是请求所有要求进行本地缓存的资源文件，包括index.html。
7. 浏览器返回要求进行本地缓存的资源文件。
8. 浏览器对本地缓存进行更新，存入所有新的资源文件，并且触发一个事件，通知本地缓存被更新。

注意：即使资源文件被修改过了，在页面上已装入的资源文件是不会发生变化的，如图片不会突然变成新的图片，脚本文件也不会突然视同新的脚本文件，更新后的本地缓存中的内容还不能被使用，只有重新打开页面的时候才会使用更新后的资源文件。如果不想修改manifest文件中对于资源文件的设置，但是对服务器上请求缓存的资源文件进行了修改，那么可以通过修改版本号的方式让浏览器认为manifest文件已经被更新过了，重新下载修改过的资源文件。

# applicationCache

​	本地缓存，当浏览器对本地缓存进行更新，装入新的资源文件时，会触发applicationCache对象的updateready事件，通知本地缓存已被更新；可以利用这个事件告诉用户本地缓存已经被更新，用户需要手工刷新页面来得到最新版本的应用程序。

```javascript
// updateReady只有服务器上的manifest文件被更新，并且把manifest文件中要求的资源文件下载到本地后触发
// 相当于本地缓存准备被更新
applicationCache.onUpdateReady = function() {
    alert('本地缓存已更新，您可以刷新页面来得到最新版本');
}
```

浏览器与服务器交互触发的`applicationCache`事件

1. 浏览器请求访问网址：http://xxx.com。
2. 服务器返回`index.html`。
3. 浏览器发现网页具有`manifest`，触发`checking`事件，检查`manifest`文件是否存在；不存在触发`error`事件，表示manifest未找到，将不执行第6步开始的交互过程。
4. 浏览器解析`index.html`，请求页面上的所有资源文件。
5. 服务器返回所有资源文件。
6. 浏览器处理`manifest`文件，请求manifest中所有要求本地缓存的文件，包括`index.html`，即使是刚请求过的文件；如果要求本地缓存所有文件，这将是一个比较大的重复请求过程。
7. 服务器返回所有要求本地缓存的文件。
8. 浏览器触发`downloading`事件，开始下载资源，在下载的同事，周期性地触发`progress`事件，可以用编程获取多少文件已被下载，多少文件仍然处于下载队列等消息。
9. 下载结束后触发`cached`事件，表示首次缓存成功，存入所有要求本地缓存的资源文件。

再次访问http://xxx.com，步骤1-5同上，在步骤5执行完之后，浏览器将核对manifest文件是否被更新，若没有被更新，触发`noupdate`事件，步骤6开始的交互过程不被执行。如果被更新了，将继续执行后面的操作，在步骤9中不触发`cached`事件，而是触发`updatgeready`，表示下载结束，可以通过刷新页面来使用更新后的本地缓存，或调用`swapCache`理科使用更新后的本地缓存。

在访问缓存名单时如果返回一个HTTP404（页面未找到），或者410（永久消失），将触发`obsolete`事件。
整个过程中，如果任何与本地缓存有关的处理中发生错误的话，都会触发error事件，具体如下：

- 缓存名单返回一个HTTP 404（页面未找到），或410（永久消失）
- 缓存名单被找到且没有更改，但引用缓存名单的HTML页面不能正确下载
- 缓存名单被找到且被更改，但浏览器不能下载某个缓存名单中列出的资源
- 开始更新本地缓存时，缓存名单再次被更改

```html
<!DOCTYPE html>
<html manifest="applicationCacheEvent.manifest">
    <head>
        <meta charset="UTF-8">
        <title>applicationCache事件流程</title>
        <script>
    	function init() {
            applicationCache.addEventListener('checking', function() {
                console.log('checking');
            }, true);
            applicationCache.addEventListener('noupdate', function() {
                console.log('noupdate');
            }, true);
            applicationCache.addEventListener('downloading', function() {
                console.log('downloading');
            }, true);
            applicationCache.addEventListener('progress', function() {
                console.log('progress');
            }, true);
            applicationCache.addEventListener('updateready', function() {
                console.log('updateready');
            }, true);
            applicationCache.addEventListener('cached', function() {
                console.log('cached');
            }, true);
            applicationCache.addEventListener('error', function() {
                console.log('error');
            }, true);
        }
    </script>
    </head>
	<body onload="init()">
        
    </body>    
</html>
```



# swapCache

​	手动执行本地缓存的更新，只能在applicationCache对象的updateReady事件被触发时调用；如果本地缓存的容量非常大（超过100MB），本地缓存的更新工作将需要相对长的时间，而且还会把浏览器锁住；需要给一个提示说明

```javascript
applicationCache.onUpdateReady = function() {
    alert('正在更新本地缓存...');
    applicationCache.swapCache();
    alert('本地缓存已被更新，您可以刷新页面来得到最新版本');
}
```

```html
<!DOCTYPE html>
<html manifest="swapCache.manifest">
	<head>
    	<meta charset="UTF-8">
        <title>swapCache</title>
        <script>
        	function init() {
                setInterval(function() {
                    // 手工检查是否有更新
                    applicationCache.update();
                }, 5000);
                applicationCache.addEventListener('updateready', function() {
                    if (confirm('本地缓存已被更新，需要刷新页面来获取最新版本，是否刷新？')) {
                        applicationCache.swapCache();
                        // 重载
                        location.reload();
                    }
                }, true)
            }
        </script>
    </head>
</html>
```

# 跨文档消息传输

​	只要获取网页所在窗口对象的实例，同源（域+端口）web网页之间可以互相通信，以及跨域通信。

```javascript
// 对窗口对象message事件监听
window.addEventListener("message", function() {}, false);
// 向其他窗口发送消息
window.postMessage("message", targetOrigin);
```

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>跨域通信</title>
        <script>
        window.addEventListener("message", function(ev) {
            // 忽略url地址之外的页面传过来的信息
            if (ev.origin !== 'http://www.blue-butterfly.net') {
                return;
            }
            alert('从' + ev.origin + '传过来的消息:\n' + ev.data);
        }, false);
        function hello() {
            var iframe = window.frames[0];
            iframe.postMessage('您好', 'http://www.blue-butterfly.net/test/');
        }
        </script>
    </head>
    <body>
        <h1>
            跨域通信
        </h1>
        <iframe width="400" src="http://www.blue-butterfly.net/test/" onload="hello()"></iframe>
    </body>
</html>

<!-- iframe http://www.blue-butterfly.net/test/ 页面代码如下 -->
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <script>
        window.addEventListener("message", function(ev) {
            if (ev.origin !== 'http://xxx') {
                return;
            }
            document.body.innerHTML = '从' + ev.origin + '传递过来的消息<br>' + ev.data;
            // 向主页面发送消息
            ev.source.postMessage('你好，这里是' + this.location, ev.origin);
        }, false);
        </script>
    </head>
</html>
```

# Web Sockets

​	服务器与客户端之间建立一个非HTTP的双向连接，这个连接是实时的，也是永久的，除非被显示关闭。

```javascript
// 建立通信连接，必须以ws、wss（加密通信）开头
var webSocket = new WebSocket('ws://localhost:8080/socket');
// send发送数据，只能发送文本数据
webSocket.send('data');
// 接收服务器传递数据事件
webSocket.onmessage = function(event) {
    console.log(event.data);
};
// 监听socket打开事件
webSocket.onopen = function(event) {
    // 开始通信时处理
};
// 监听socket关闭事件
webSocket.onclose = function(event) {
    // 通信结束时处理
};
// 通过close关闭socket，切断通信连接
webSocket.close();
```

可以通过`readyState`获取`WebSocket`状态，`readyState`值如下所示：

- `CONNECTING`：0，正在连接
- `OPEN`：1，已建立连接
- `CLOSING`：2，正在关闭连接
- `CLOSED`：3，已关闭连接

# Web Wrokers处理线程

​	Web Workers实现Web的多线程处理功能，将创建一个不影响前台处理的后台进程，并且在这个后台线程中创建多个子线程，将耗时较长的处理交给后台线程去运行，解决HTML 5因为某个处理耗时过长而卡住的状态。

```javascript
// 将执行的脚本文件当做URL地址作为参数，创建Worker对象
var worker = new Worker('worker.js');
// 后台线程是不能访问页面或者窗口对象的，可以通过发送和接收消息互相传递数据
worker.onmessage = function(event) {
    // 处理收到的消息
};
// 发送消息
worker.possMessage('message');
```

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <script>
    	var worker = new Worker('SumCalculate.js');
    	worker.onmessage = function(event) {
            console.log(event.data);
        };
        function calculate() {
            worker.postMessage(+document.getElementById('num').value);
        };
    </script>
</head>
<body>
    <h1>
        从1到定数值的求和示例
    </h1>
    输入数值：<input type="text" id="num">
    <button onclick="calculate()">计算</button>
</body>

<!--SumCalculate.js-->
onmessage = function(event) {
	var num = event.data;
	var result = 0;
	for (var i = 0; i <= num; i++) {
        result += i;      
    }
    postMessage(result);
    close(); // 关闭子线程
}
```

## 线程嵌套，多个子线程数据交互

1. 创建发送数据的子线程
2. 执行子线程中的任务，把要传递的数据发送给主线程
3. 在主线程接受子线程传回来的消息，创建接受数据的子线程，然后把发送数据的子线程中返回的消息传递给接受数据的子线程
4. 执行接收数据子线程中的代码

```javascript
onmessage = function(event) {
    var worker = new Worker('worker1.js');
    worker.postMessage('');
    worker.onmessage = function(ev) {
        console.log(ev.data);
    }
    close();
}
```

## 线程中可用的变量、函数与类

- `self`：`self`关键词便是本线程范围内的作用域。
- `postMessage(message)`：向创建线程的源窗口发送消息。
- `onmessage`：获取接受消息的事件句柄。
- `importScripts(urls)`：导入其它JavaScript，可以导入多个`importScripts('script1.js', 'scripts2.js')`。
- `navigator`：与window.navigator对象类似，具有appName、platform、userAgent、appVersion这些属性。
- `sessionStorage`、`localStorage`
- `XMLHttpRequest`
- `Web Workers`：线程中嵌套线程。
- `setTimeout()`、`setInterval()`：定时器。
- `close()`：结束本线程。
- `eval()`、`isNaN()`、`escape()`等。
- 所有`JavaScript`核心函数。
- `object`：创建和使用本地对象。
- `WebSockets`：向服务器发送和接收消息。

# 地理位置信息

`window.navigator.geolocation`：Geolocation API

```javascript
// getCurrentPosition(onSuccess, onError, options): void 获取用户当前地理位置信息
navigator.geolocation.getCurrentPosition(
    function(position) {
        // 获取当前地理位置成功回调
    	console.log(position);
	},
    function(error) {
        // 获取当前地理位置失败回调
        console.error(error);
        // error.code有三个值：1-用户拒绝了位置服务；2-获取不到位置信息；3-获取信息超时错误
        // error.message错误信息
    },
    {
        // enableHighAccuracy： 是否要求高精度的地理位置信息，在很多设备上设置了都没用，因为是被需要结合电量，具体地理情况来综合考虑，默认即可。
        // timeout：超时，（毫秒单位），该时间内未获取到地理信息，返回错误
        timeout: 5 * 1000,
        // maximumAge：缓存有效时间（毫秒单位），假设缓存2分钟，10点获取过一次地理位置信息，10点01分再次调用，将返回10点的缓存数据，超过该时间后缓存信息被废弃，尝试从新获取位置信息；如果值设定为0，则无条件重新获取新的地理位置信息
        maximumAge: 2 * 60 * 1000
    }
)
```

## 持续监听当前地理位置信息

```javascript
// 参数和使用方法与getCurrentPosition相同
// 返回一个数字，使用方法与setInterval类似，可以被clearWatch停止使用
watchCurrentPosition(onSuccess, onError, options): int

clearWatch(watchId): void
```

## position对象

- `latitude`：纬度
- `longitude`：经度
- `altitude`：海拔高度（不能获取时为null）
- `accuracy`：经纬度的精度（以米为单位）
- `altitudeAccurancy`：海拔高度的精度（以米为单位）
- `heading`：设备的前进方向，以面向正北方向的顺时针旋转角度来表示（不能获取时为null）
- `speed`：设备的前进速度（以米/秒为单位，不能获取时为null）
- `timestamp`：获取位置信息时的时间

