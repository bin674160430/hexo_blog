---
title: web
date: 2020-07-23 09:37:00
categories:
    - 前端
tags:
    - web
---

# 对浏览器内核的理解？

主要分成两部分：`渲染引擎（layout engineer或 rendering engine）`和`js引擎`，最开始渲染引起和js引擎并没有区分得很明确，后来js引擎越来越独立，内核就倾向于渲染引擎。

`渲染引擎`负责取得网页的内容（HTML、XML、图像等）、整理讯息（例如加入CSS）以及计算网页的显示方式，然后会输出到显示器或打印机，内核不同，对网页的解释不同，渲染效果也不相同，所有网页浏览器、电子邮件客户端以及其它需要编辑、显示网络内容的应用程序都需要内核。

`js引擎`解析和执行`javascript`来实现网页的动态效果

# 常见的浏览器内核

| 浏览器  | 内核           | 备注                                                         |
| ------- | -------------- | ------------------------------------------------------------ |
| ie      | trident        | ie、猎豹安全、360极速浏览器、百度浏览器                      |
| firefox | gecko          | firefox这几年没落了，速度慢，升级频繁                        |
| safari  | webkit         | 从safari推出的时候开始，它的渲染引擎就是webkit               |
| chrome  | chromium/blink | webkit分支，内置于chrome浏览中，大部分国产浏览器最新版都采用blink内核 |
| opera   | blink          | 跟随chrome用blink内核                                        |



# Web标准和W3C的理解

**web标准：**以`html页面结构、css页面结构标签表现、js交互行为`组成

**w3c**对网络标准提出了规范化的要求

1. `html`结构要求 `标签字母小写，标签封闭`（标签规范可以提高搜索引擎对页面的抓取效率，对SEO很有帮助）
2. 外链`css`和`js`，符合结构、表现、行为规范，同时提高页面渲染速度，提升用户体验
3. 样式尽量少用行间样式表，使结构和表现分离，标签的`id`和类名等属性命名做到见名知意，标签越少，加载越快，用户体验越高，代码维护简单，方便改版

# 网页验证码是干嘛的，为了解决什么安全问题？

- 可以防止恶意破解密码，刷票，论坛灌水
- 有效防止黑客对某人特定注册用户用特定程序暴力破解方式进行不断的登陆尝试

# 为什么用多个域名存储网站资源更有效

- `CDN`缓存更方便
- 图片浏览器并发限制
- 节约`cookie`限制
- 节约主域名的连接数，优化页面相应速度
- 防止不必要的安全问题，（对于`UGC`的内容和主站隔离，要求用户内容的域名必须不是自己主站的子域名，而是一个完全独立的第三方域名）

# div+css的布局较表布局有什么优点？

- 分离方便改版清晰简洁seo
- 改版的时候更方便只需要改css文件
- 页面加载速度加快，结构化清新，页面显示简洁
- 表现和结构分离
- 轻松优化seo，搜索引擎更友好，排名更靠前

# 微格式 microformats

利用`HTML`的`class`的属性来对网页添加信息的方法，是一种开放式的数据格式，通过语义让内容被人和电脑均可读，网页上允许的微格式包括事件、人物、地点等，可以被其它的软件检测到，并提取相应的信息，以及对信息进行索引、搜索、跨平台参考，例如一个地址坐标`46.32`， `-12.33`

```html
<span class="geo"><span class="latitude">46.32</span><span class="longtitude">-1.89</span></span>
```

## 不同种类的微格式：

`XFN`用于描述社交关系

`hCalendar` 用于描述事件

`hCard` 描述联系信息

`hNews`描述新闻内容

`hProduct`描述商品

`XOXO`描述列表和大纲

# 渐进增强和优雅降级之间有什么不同吗？

渐进增强：保证最基本的功能，再改进和追加功能

优雅降级：重建完整的功能，再针对低版本浏览器进行兼容

# 前端需要注意的[SEO](/blog/2020/07/16/word/#SEO)优化

标题突出重点即可，重要`关键字`靠前且只出现一次，不同页面`title`不同，`description`把页面内容概述，`keywords`列出重要关键词即可

语义化HTML，符合w3c规范，让搜索引擎更容易理解网页

重要内容HTML代码放在前面，搜索引擎抓取HTML顺序是从上到下，有的搜索引擎对抓取长度有限制，保证重要内容一定会被抓取

重要内容不要用js输出，爬虫不会执行js获取内容

少用iframe：搜索引擎不会抓取iframe中的内容

非装饰性图片必须加alt

提供网站速度：网站速度是搜索引擎排序的一个重要指标

# 渐进式渲染 progressive rendering

渐进式呈现是用于提高网页性能（尤其是提高用户感知的加载速度），以尽快呈现页面的技术；

- 图片懒加载-页面上的图片不会一次性全部加载，当用户滚动页面到图片部分时，`javascript`将加载并显示图像；
- 分层级渲染，如SPA应用，页面只包含最小的css、js，接着使用中断加载脚本或监听`DOMContentLoaded/load`事件加载其他资源和内容；
- 初步加载HTML片段，当页面通过后台渲染时，将html拆分，通过异步请求，分块发给浏览器

# JSONP原理

json with padding是一种简单的处理跨域方案，原理是利用script直接请求第三方进行跨域请求的特点，服务端response.body执行callback

```javascript
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
    if(ctx.method === 'GET' && ctx.url.indexOf('?callback') === 1) {
        let callbackFun = ctx.query.callback || 'callback';
        let body = {
            msg: 'jsonp请求成功！'
        }
        ctx.type = 'text/javascript';
        ctx.body = `;${callbackFun}(${JSON.stringify(body)})`;
    } else {
        ctx.body = '测试!!!';
    }
});

app.on('error', err => {
    log.error('server error', err)
});

app.listen(3000, ()=> {
    console.info("node server start...")
});

function jsonp(res) {
    let script = document.createElement('script');
    let url = res.url + '?callback=' + res.callback.name;
    script.src = url;
    document.getElementsByTagName('head')[0].appendChild(script); 
}

function callbackFun(res) {
    console.log('msg', res && res['msg']);
} 
jsonp({
    url: 'otherUrl',
    data: { msg: 'val'},
    callback: callbackFun
});
```

