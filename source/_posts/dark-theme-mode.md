---
title: 暗黑模式
date: 2020-09-17 09:56:30
categories:
    - 样式
tags:
    - css
---

# 暗黑模式支持

原文https://mp.weixin.qq.com/s/1Rr-t-M6E5kJr7_2h1GPCA

```css
html {
    transition: color 300ms, background-color 300ms;
}

html[theme='dark-mode'] {
    filter: invert(1) hue-rotate(180deg);
}

html[theme='dark-mode'] img {
    filter: invert(1) hue-rotate(180deg);
}
```

{% image 2.gif 效果图%}

解析：

`filter`将模糊或颜色偏移等图形效果应用于元素，滤镜通常用于调整图像，背景和边框的渲染
对于暗黑模式，将使用`invert`和`hue-rotate`

**invert**: 反转配色，黑色变为白色，白色变为黑色，所有颜色都是这样处理
**hue-rotate**: 帮助我们处理所有其他非黑色或者白色的颜色，将色相旋转180度，确保应用程序的颜色主题不会改变，而只是减弱其颜色
{% image 1.png 效果图 %}

这个方法的唯一缺点是，它还会反转应用程序中的所有图像，因此，我们将所有的图像添加相同的规则，以逆转效果

```css
html[theme='dark-mode'] img {
    filter: invert(1) hue-rotate(180deg);
}
```

