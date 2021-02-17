---
title: css 
date: 2020-11-18 17:58:30
categories:
    - 画布
tags:
    - canvas
---

# canvas

​	HTML5新增的一个重要元素，可以在其中进行图形的描绘。坐标原点时Canvas画布的最左上角。

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

# 绘制矩形

```javascript
function draw(id) {
  // 1. 取得canvas对象
  var canvas = document.getElementId(id);
  if (canvas == null) {
    return false;
  }
  // 2. 取得上下文context
  var context = canvas.getContext('2d');
  // 3. fill填充与stroke绘制边框
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  // 4. 设定绘图样式style， fillStyle填充的样式， strokeStyle图形边框的样式
  context.fillStyle = 'red';
  context.strokeStyle = 'blue';
  // 5. 指定线框
  context.lineWidth = 1;
  // 6. 绘制矩形
  context.fillRect(50, 50, 100, 100);
  context.strokeRect(50, 50, 100, 100);
  // 擦除指定的巨星区域中的图形
  // context.clearRect(x, y, width, height);
}
```

# 使用路径

1. `context.beginPath()`：开始创建路径。

2. `context.arc(x, y, radius, startAngle, endAngle, anticlockwise: boolean)`：创建圆形路径，x为绘制圆形的起点横坐标，y为绘制圆形的起点纵坐标，radius为圆形半径，startAngle为开始角度，endAngle为结束角度，anticlockwise：true顺时针绘制；false逆时针绘制。

3. `context.closePath()`：关闭路径。（如果不关闭路径，已经创建的路径会永远保留着，就算用fill和stroke在页面上将图形绘制完毕，路径都不会消失，创建的图形反复绘制和重叠）

4. 设定绘制样式，进行图形绘制。

   ```javascript
   context.fillStyle = 'rgba(255, 0, 0, 0.25)';
   context.fill();
   ```

```javascript
function draw(id) {
  var canvas = document.getElementById(id);
  if (canvas == null) {
    return false;
  }
  var context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  var n = 0;
  for (var i = 0; i < 10; i++) {
    context.beginPath();
    context.arc(i * 25, i * 25, i * 10, 0, Math.PI * 2, true);
    context.closePath();
    context.fillStyle = 'rgba(255, 0, 0, 0.25)';
    context.fill();
  }
}
```

# 绘制直线

## moveTo(x, y)

​	将光标移动到指定坐标点，绘制直线的时候以这个坐标点为起点。

## lineTo(x, y)

​	lineTo在moveTo中指定的直线起点与参数中指定的直线终点之间绘制一条直线。x、y表示终点坐标，绘制完直线后，光标自动移到lineTo参数所指定的直线终点。

# bezierCurveTo绘制贝济埃曲线

​	`bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)`相当于lineTo的曲线版，将从当前坐标点到指定坐标点中间的贝济艾曲线追加到路径中。该方法有六个参数，需要两个控制点，cp1x, cp1y为第一个控制点的坐标；cp2x, cp2y作为第二个控制点的坐标；x、y为曲线终点坐标。
​	可以使用`quadraticCurveTo(cpx, cpy, x, y)`绘制二次样条曲线，参数只需要一个控制点和终点坐标。

```javascript
function draw(id) {
  var canvas = document.getElementById(id);
  if (canvas == null) {
    return false;
  }
  var context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  var n = 0;
  var dx = 150;
  var dy = 150;
  var s = 100;
  context.beginPath();
  context.globalCompositeOperation = 'and';
  context.fillStyle = 'rgb(100, 255, 100)';
  var x = Math.sin(0);
  var y = Math.cos(0);
  var dig = Math.PI / 15 * 11;
  context.moveTo(dx, dy);
  for (var i = 0; i < 30; i++) {
    var x = Math.sin(i * dig);
    var y = Math.cos(i * dig);
    context.bezierCurveTo(dx + x * s, dy + y * s - 100, dx + x * s + 100, dy + y * s, dx + x * s, dy + y *s);
  }
  context.closePath();
  context.fill();
  context.stroke();
}
```



# 渐变

## createLinearGradient(xStart, yStart, xEnd, yEnd)

​	两点之间的线性渐变，创建一个使用两个坐标点的`LinearGradient`对象后，使用`addColorStop(offset, color)`进行设定，offset设定的颜色离开渐变起始点的偏移量，该参数的范围是0-1之间的浮点值，渐变起始点的偏移量为0，渐变结束点的偏移量为1。因为是渐变，至少需要使用两次addColorStop追加两个颜色（开始颜色和结束颜色）。

```javascript
// 从黄色到青涩的渐变
function draw(id) {
  var canvas = document.getElementById(id);
  if (canvas == null) {
    return false;
  }
  var context = canvas.getContext('2d');
  var g1 = context.createLinearGradient(0, 0, 0, 300);
  g1.addColorStop(0, 'rgb(255, 255, 0)');
  g1.addColorStop(1, 'rgb(0, 255, 255)');
  context.fillStyle = g1;
  context.fillRect(0, 0, 400, 300);
  var n = 0;
  var g2 = context.createLinearGradient(0, 0, 300, 0);
  g2.addColorStop(0, 'rgba(0, 0, 255, 0.5)');
  g2.addColorStop(1, 'rgba(255, 0, 0, 0.5)');
  for (var i = 0; i < 10; i++) {
    context.beginPath();
    context.fillStyle = g2;
    context.arc(i * 25, i * 25, i * 10, 0, Math.PI * 2, true);
    context.closePath();
    context.fill();
  }
}
```

## createRadialGradient(xStart, yStart, radiusStart, xEnd, yEnd, radiusEnd)

​	径向渐变，参数指定了两个圆的大小与未知，从第一个圆的圆心向外进行扩散渐变，一直扩散到第二个圆的外轮廓处。

```javascript
function draw(id) {
  var canvas = document.getElementById(id);
  if (canvas == null) {
    return false;
  }
  var context = canvas.getContext('2d');
  var g1 = context.createRadialGradient(400, 0, 0, 400, 0, 400);
  g1.addColorStop(0, 'rgb(255, 255, 0)');
  g1.addColorStop(0.3, 'rgb(255, 0, 255)');
  g1.addColorStop(1, 'rgb(0, 255, 255)');
  context.fillStyle = g1;
  context.fillRect(0, 0, 400, 300);
  var n = 0;
  var g2 = context.createRadialGradient(250, 250, 0, 250, 250, 300);
  g2.addColorStop(0, 'rgba(255, 0, 0, 0.5)');
  g2.addColorStop(0.7, 'rgba(255, 255, 0, 0.5)');
  g2.addColorStop(1, 'rgba(0, 0, 255, 0.5)');
  for (var i = 0; i < 10; i++) {
    context.beginPath();
    context.fillStyle = g2;
    context.arc(i * 25, i * 25, i * 10, 0, Math.PI * 2, true);
    context.closePath();
    context.fill();
  }
}
```

# 绘制变形图形

## 坐标变换

- `context.translate(x, y)` 移动坐标轴原点
- `context.scale(x, y)` 将图形放大
- `context.rotate(angle)`旋转中心点事坐标轴的原点

```javascript
function draw(id) {
  var canvas = document.getElementById(id);
  if (canvas == null) {
    return false;
  }
  var context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  // 图形绘制
  context.translate(200, 50);
  context.fillStyle = 'rgba(255, 0, 0, 0.25)';
  for (var i = 0; i < 50; i++) {
    context.translate(25, 25);
    context.scale(0.95, 0.95);
    context.rotate(Math.PI / 10);
    context.fillRect(0, 0, 100, 50);
  }
}
```

## 矩阵变换

`conetxt.transform(m11, m12, m21, m22, dx, dy)`使用一个新的变换矩阵与当前变换矩阵进行乘法运算

| m11  | m21  | dx   |
| ---- | ---- | ---- |
| m12  | m22  | dy   |
| 0    | 0    | 1    |

dx、dy参数移动坐标原点；以下提供类似方法说明变形矩阵的使用

- `translate(x, y)`，可以使用`context.transform(1, 0, 0, 1, x, y)`或`context.transform(0, 1, 1, 0, x, y)`方法进行代替，前四个参数1，0，0，1或0，1，1，0表示不对图形进行缩放操作，变形操作，将dx设为x坐标原点向右移动x个单位，dy设为y从坐标原点向下移动y个单位。

- `scale(x, y)`，可以使用`context.transform(x, 0, 0, y, 0, 0)`或`context.transform(0, y, x, 0, 0, 0)`方法代替，前面四个参数x，0，0，y或0，y，x，0表示将图形横向扩大x倍，纵向扩大y倍，dx、dy为0表示不移动坐标原点。

- `rotate(angle)`，替换方法如下：

  ```javascript
  context.transform(
  	Math.cos(angle * Math.PI / 180),
    Math.sin(angle * Math.PI / 180),
    -Math.sin(angle * Math.PI / 180),
    Math.cos(angle * Math.PI / 180),
    0,
    0
  );
  // 或
  context.transform(
  	-Math.sin(angle * Math.PI / 180),
    Math.cos(angle * Math.PI / 180),
    Math.cos(angle * Math.PI / 180),
    Main.sin(angle * Math.PI / 180),
    0,
    0
  );
  ```

  ```javascript
  function draw(id) {
    var canvas = document.getElementById(id);
    if (canvas == null) {
      return false;
    }
    var context = canvas.getContext('2d');
    // 绘制红色矩形
    context.strokeStyle = 'red';
    context.strokeRect(30, 10, 60, 20);
    // 绘制顺时针旋转45度后的蓝色长方形
    // 绘制45度圆弧
    var rad = 45 * Math.PI / 180;
    // 定义顺时针45度变换矩阵
    context.setTransform(Math.cos(rad), Math.sin(rad), -Math.sin(rad), Math.cos(rad), 0, 0);
    // 绘制图形
    context.strokeStyle = 'blue';
    context.strokeRect(30, 10, 60, 20);
    // 绘制放大2.5倍后的绿色长方形
    // 绘制放大2.5倍后的变形矩阵
    context.setTransform(2.5, 0, 0, 2.5, 0, 0);
    // 绘制图形
    context.strokeStyle = "green";
    context.strokeRect(30, 10, 60, 20);
    // 将坐标原点向右移动40像素，向下移动80像素后绘制灰色长方形
    // 定义将坐标原点向右移动40像素，向下移动80像素的矩阵
    context.setTransform(1, 0, 0, 1, 40, 80);
    // 绘制图形
    context.strokeStyle = "gray";
    context.strokeRect(30, 10, 60, 20);
  }
  ```

# 图形组合

​	`context.globalCompositeOperation = source-over | destination-over | source-in | destination-in | source-out | destination-out | source-atop | destination-atop | llighter | xor | copy`

- source-over （默认值），表示新图形覆盖在原有图形之上。
- destination-over：在原有图形之下绘制新图形。
- source-in：新图形与原有图形作in运算，只显示新图形中与原有图形相重叠的部分，新图形与原有图形的其他部分均变成透明。
- destination-in：原有图形与新图形作in运算，只显示原有图形中与新图形相重叠的部分，新图形与原有图形的其他部分均变成透明。
- source-out：新图形与原有图形作out运算，只显示新图形中与原有图形不重叠的部分，新图形与原有图形的其他部分均变成透明。
- destination-out：原有图形与新图形作out运算，只显示原有图形中与新图形不重叠的部分，新图形与原有图形的其他部分均变成透明。
- source-atop：只绘制新图形与原有图形重叠的部分与未被重叠覆盖的原有图形，新图形的其他部分变成透明。
- destination-atop：只绘制原有图形中被新图形重叠覆盖的部分与新图形的其他部分，原有图形中的其他部分变成透明，不绘制新图形中与原有图形重叠的部分。
- lighter：原有图形与新图形均绘制，重叠部分做加色处理。
- xor：只绘制新图形中与原有图形不重叠的部分，重叠部分变成透明。
- copy：只绘制新图形，原有图形中未与新图形重叠的部分变成透明。

```javascript
function draw(id) {
  var canvas = document.getElementById(id);
  if (canvas == null) {
    return false;
  }
  var context = canvas.getContext('2d');
  var options = new Array(
  	'source-atop',
    'source-in',
    'source-out',
    'source-over',
    'destination-atop',
    'destination-in',
    'destination-out',
    'destination-over',
    'lighter',
    'copy',
    'xor'
  );
  var i = 8; // 自行修改该参数来显示想要查看的组合效果
  // 绘制原有图形（蓝色长方形）
  context.fillStyle = 'blue';
  context.fillRect(10, 10, 60, 60);
  // 设置组合方式
  context.globalCompositeOperation = options[i];
  // 设置新图形（红色圆形）
  context.beginPath();
  context.fillStyle = 'red';
  context.arc(60, 60, 30, 0, Math.PI * 2, false);
  context.fill();
}
```



# point

​	{% image 1.jpg 图片 %}

​	与其他HTML元素一样，左上角坐标原点(0,0)，向右X增加，向下X增加，对于500x300像素的元素来说，右下角坐标是(500,300)