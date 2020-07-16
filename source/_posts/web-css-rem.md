---
title: web-css-rem 无限适配
date: 2017-12-14 22:04:00
categories:
    - 前端
tags:
    - css
---

# 移动端css-rem使用

<big>***html***</big>
{% link lib-flexible（使用说明） https://github.com/amfe/lib-flexible %}

{% codeblock lang:html %}
<!DOCTYPE html>
<html data-dpr="2" lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta content="yes" name="apple-mobile-web-app-capable">
    <meta content="yes" name="apple-touch-fullscreen">
    <meta name="x5-fullscreen" content="true">
    <meta name="full-screen" content="yes">
    <meta content="telephone=no,email=no" name="format-detection">
    <meta name="App-Config" content="fullscreen=yes,useHistoryState=yes,transition=yes">
    <meta name="viewport" content="width=device-width, initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no">
    <title>document</title>
    <script src='https://github.com/amfe/lib-flexible'></script>
    <!-- 淘宝提供的适配方式js -->
</head>
<body>
</body>
</html>
{% endcodeblock %}

<big>在网页中打开后计算的font-size</big>
{% asset_img index.png %}

*   如上图所示：以iphon6（750 X 1334）作为设计稿像素尺寸，统一排版即可
*   _html font-size: 75px_
*   rem计算在css处理器中处理即可，入下 `sass`：

{% codeblock lang:sass %}
$fontSize: 75;
@function px2rem($px) {
    @return $px / $fontSize * 1rem;
}

.test { width: px2rem(150); }
//编译结果
.test { width: 2rem; }
{% endcodeblock %}

# 声明

* 动态设置html的font-size
* 布局时各元素的尺寸值都是根据设计稿标注的尺寸计算，能够在不同分辨率下页面布局呈现一致效果
* 容器元素的font-size都不用rem，需要额外地对font-size做媒介查询
* 不是所有单位都用rem，而是与px混合使用，如上图所示 body计算出 `font-size: 24px`，rem处理适配距离，em处理文字大小