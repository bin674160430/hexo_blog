---
title: css 
date: 2020-11-18 17:58:30
categories:
    - 画布
tags:
    - canvas


---

# canvas

​	一定要通过`width`和`height`属性设置`canvas`的宽高，css的width和height会把画布拉伸或压缩到设置的大小

```html
<canvas id="canvas"></canvas>
<script>
  window.onload = function() {
    var canvas = document.getElementById('canvas');
  	var context = canvas.getContext('2d'); // 获取二维绘图上下文
  }
</script>
```

## point

​	{% image 1.jpg 图片 %}

​	与其他HTML元素一样，左上角坐标原点(0,0)，向右X增加，向下X增加，对于500x300像素的元素来说，右下角坐标是(500,300)