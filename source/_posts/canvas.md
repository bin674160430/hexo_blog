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

# 绘制阴影

- shadowOffsetX：阴影横向位移量，默认0
- shadowOffsetY：阴影纵向位移量，默认0
- shadowColor：阴影颜色
- shadowBlur：阴影模糊范围，必须>0，否则将被忽略；一般设定在0-10之间

```javascript
function draw(id) {
    var canvas = document.getElementById(id);
    if (canvas == null) {
        return false;
    }
    var context = canvas.getContext('2d');
    context.fillStyle = '#EEEEFF';
    context.fillRect(0, 0, 400, 300);
    context.shadowOffsetX = 10;
    context.shadowOffsetY = 10;
    context.shadowColor = 'rgba(100, 100, 100, 0.5)';
    context.shadowBlur = 7.5;
    // 图形绘制
    context.translate(0. 50);
    for (var i = 0; i < 3; i++) {
        context.translate(50, 50);
        create5Star(context);
        context.fill();
    }
}
function create5Star(context) {
    var n = 0;
    var dx = 100;
    var dy = 0;
    var s = 50;
    // 创建路径
    context.beginPath();
    context.fillStyle = 'rgba(255, 0, 0, 0.5)';
    var x = Math.sin(0);
    var y = Math.cos(0);
    var dig = Math.PI / 5 * 4;
    for (var i = 0; i < 5; i++) {
        var x = Math.sin(i * dig);
        var y = Math.cos(i * dig);
        context.lineTo(dx + x * s, dy + y * s);
    }
    context.closePath();
}
```

# 绘制图片

​	HTML5中，可以用Canvas将图片绘制在画布中。

```javascript
// 参数image是一个Image对象，xy是绘制时该图像在画布中的起始坐标
context.drawImage(image, x, y);
// wh是绘制图像的宽度和高度
context.drawImage(image, x, y, w, h);
// 将画布中已绘制好的图像全部或者局部区域复制到画布中的另一个位置上
// sx,sy表示源图像被复制区域在画布中的起始坐标
// sw,sh表示被复制渔区的宽度和高度
// dx,dy表示复制后的目标图像在画布中的起始坐标
// dw,dh表示复制后的目标图像在画布中的宽度和高度
context.drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh);

function draw(id) {
    var canvas = document.getElementById(id);
    if (canvas == null) {
        return false;
    }
    var context = canvas.getContext('2d');
    context.fillStyle = '#EEEEFF';
    context.fillRect(0, 0, 400, 300);
    var image = new Image();
    image.src = 'xx.jpg';
    image.onload = function() {
        drawImg(context, image);
    }
}
function drawImg(context, image) {
    for (var i = 0; i < 7; i++) {
        context.drawImage(image, 0 + i * 50, 0 + i *25, 100, 100);
    }
}
```

图像局部特写放大处理

```javascript
function draw(id) {
    var canvas = document.getElementById(id);
    if (canvas == null) {
        return false;
    }
    var context = canvas.getContext('2d');
    context.fillStyle = '#EEEEFF';
    context.fillRect(0, 0, 400, 300);
    var image = new Image();
    image.src = 'xx.jpg';
    image.onload = function() {
        drawImg(context, image);
    }
}
function drawImg(context, image) {
	// 绘制原始图像    
    context.drawImage(image, 0, 0, 100, 100);
    // 绘制将局部区域进行放大后的图像
    context.drawImage(image, 23, 5, 57, 80, 110, 0, 100, 100);
}
```

# 图像平铺

​	按一定比例缩小后的图像将画布填满

```javascript
function draw(id) {
    var canvas = document.getElementById(id);
    if (canvas == null) {
        return false;
    }
    var context = canvas.getContext('2d');
    var image = new Image();
    image.src = 'xx.jpg';
    image.onload = function() {
        drawImg(canvas, context, image);
    }
}
function drawImg(canvas, context, image) {
    // 平铺比例
    var scale = 5;
    // 缩小后的图像宽、高
    var n1 = image.width / scale;
    var n2 = image.height / scale;
    // 平铺横向个数
    var n3 = canvas.width / n1;
    // 平铺纵向个数
    var n4 = canvas.height / n2;
    for (var i = 0; i < n3; i++) {
        for (var j = 0; j < n4; j++) {
            context.drawImage(image, i * n1. j * n2, n1, n2);
        }
    }
}
```

还可以使用 `context.createPattern(image, type)`实现，type值如下：

- no-repeat：不平铺
- repeat-x：横向平铺
- repeat-y：纵向平铺
- repeat：全方向平铺

```javascript
function draw(id) {
    var canvas = document.getElementById(id);
    if (canvas == null) {
        return false;
    }
    var context = canvas.getContext('2d');
    var image = new Image();
    image.src = 'xx.jpg';
    image.onload = function() {
        // 创建填充样式，全方向平铺
        var ptrn = context.createPattern(image, 'repeat');
        // 指定填充样式
        context.fillStyle = ptrn;
        // 填充画布
        context.fillRect(0, 0, 400, 300);
    }
}
```

# 图像裁剪

​	在画布内使用路径，只绘制路径所包括区域内的图像，不绘制路径外部的图像；使用图形上下文对象的不带参数`clip`方法来实现`canvas`裁剪，该方法使用路径来对canvas画布设置一个裁剪区域，所以，必须先创建好路径。
​	裁剪区域一旦设置好，后面绘制的图形就都可以使用这个裁剪区域，如果要取消这个已经设置好的裁剪区域，可以调用`save`保存图形上下文当前的状态，在绘制完经过裁剪的图像后，再调用`restore`恢复之前保存的图形上下文状态，对之后绘制的图像取消裁剪区域。

```javascript
function draw(id) {
    var canvas = document.getElementById(id);
    if (canvas == null) {
        return false;
    }
    var context = canvas.getContext('2d');
    var gr = context.createLinearGradient(0, 400, 300, 0);
    gr.addColorStop(0, 'rgb(255, 255, 0)');
    gr.addColorStop(1, 'rgb(0, 255, 255)');
    context.fillStyle = gr;
    context.fillRect(0, 0, 400, 300);
    var image = new Image();
    image.onload = function() {
        drawImg(context, image);
    };
    image.src = 'xxx.jpg';
}
function drawImg(context, image) {
    create5StarClip(context);
    context.drawImage(image, -50, -150, 300, 300);
}
function create5StarClip(context) {
    var n = 0;
    var dx = 100;
    var dy = 0;
    var s = 150;
    context.beginPath();
    context.translate(100, 150);
    var x = Math.sin(0);
    var y = Math.cos(0);
    var dig = Math.PI / 5 * 4;
    for (var i = 0; i < 5; i++) {
        var x = Math.sin(i * dig);
        var y = Math.cos(i * dig);
        context.lineTo(dx + x * s, dy + y * s);
    }
    context.clip();
}
```

# 像素处理

​	`getImageData(sx, sy, sw, sh): CanvasPixelArray` 获取图像中的像素，sx、sy表示区域起点的坐标，sw、sh表示区域的宽度、高度；返回一个`CanvasPixelArray`对象，具有`height`、`width`、`data`等属性，`data`是一个保存像素数据的数组，具体内容有`[r1, g1, b1, a1, r2, g2, b2, a2...]`，r1、g1、b1、a1为第一个像素的红色值、绿色值、蓝色值、透明度值。

```javascript
var context = canvas.getContext('2d');
var image = new Image();
image.onload = function() {
    context.drawImage(image, 0, 0);
    var imagedata = context.getImageData(0, 0, image.width, image.height);
}
```

​	取得像素后，可以进行例如蒙版处理、面部识别等较复杂的图像处理操作。
​	`context.putImageData(imagedata, dx, dy[, dirtyX, dirtyY, dirtyWidth, dirtyHeight]);` dx、dy表示重绘图像的起点坐标，dirtyX、dirtyY、dirtyWidth、dirtyHeight给出一个矩形的起点坐标、宽高，如果加上这四个参数，只绘制像素数组中矩形范围内容的图像。

```javascript
function draw(id) {
    var canvas = document.getElementById(id);
    var context = canvas.getContext('2d');
    var image = new Image();
    image.src = 'xx.jpg';
    image.onload = function() {
        context.drawImage(image, 0, 0);
        var imagedata = context.getImageData(0, 0, image.width, image.height);
        for (var i = 0, n = image.data.length; i < n; i += 4) {
            imagedata.data[i+0] = 255 - imagedata.data[i+0]; // red
            imagedata.data[i+1] = 255 - imagedata.data[i+2]; // green
            imagedata.data[i+2] = 255 - imagedata.data[i+1]; // blue
        }
        context.putImageData(imagedata, 0, 0);
    }
}
```

# 绘制文字

​	`fillText(text, x, y, [maxWidth]): void`：填充文字绘制字符串， text表示文字；x、y表示坐标；maxWidth表示显示文字时的最大宽度，可以防止文字溢出。
​	`strokeText(text, x, y, [maxWidth]): void`：用轮廓方式绘制字符串。

有关文字绘制的属性：

- `font`：文字字体
- `textAlign`：文字水平对齐方式，值为 `start`（默认值）、`end`、`left`、`right`、`center`
- `textBaseline`：文字垂直对齐方式，值为`top`、`hanging`、`middle`、`alphabetic`（默认值）、`ideographic`、`bottom`

```javascript
function draw(id) {
    var canvas = document.getElementById(id);
    var context = canvas.getContext('2d');
    context.fillStyle = '#00f';
    context.font = 'italic 30px sans-serif';
    context.textBaseline = 'top';
    // 填充字符串
    context.fillText('实例文字', 0, 0);
    context.font = 'bold 30px sans-serif';
    // 轮廓字符串
    context.strokeText('实例文字', 0, 50);
}
```

​	`context.measureText(text): TextMetrics`：图形上下文获取文字宽度

```javascript
function draw(id) {
    var canvas = document.getElementById(id);
    var context = canvas.getContext('2d');
    context.font = 'italic 20px sans-serif';
    // 定义文字
    var txt = '字符串的宽度为：';
    // 获取文字宽度
    var tml = context.measureText(txt);
    // 绘制文字
    context.fillText(txt, 10, 30);
    context.fillText(tml.width, tml.width + 10, 30);
    // 改变字体
    context.font = 'bold 30px sans-serif';
    // 重新获取文字宽度
    var tm2 = context.measureText(txt);
    // 重新绘制文字
    context.fillText(txt, 10, 70);
    context.fillText(tm2.width, tm2.width + 10, 70);
}
```

# 保存与恢复状态

```javascript
var x, y;
for (var j = 1; j > 50; j++) {
    ctx.save();
    // 改变绘画状态，进行想要的操作
    ctx.fillStyle = '#fff';
    x = 75 - Math.floor(Math.random() * 150);
    y = 75 - Math.floor(Math.random() * 150);
    ctx.translate(x, y);
    drawStar(ctx, Math.floor(Math.random() * 4) + 2);
    ctx.restore();
}
```

# 保存文件

​	`canvas.toDataURL(type);`：type的值为图片类型，例如 `image/jpeg`, `image/png`。

# 动画

​	Canvas制作动画实际上就是一个不断擦除、重绘的过程，具体步骤如下：

1. 预先编写好用来绘图的函数，在该函数中先用`clearRect`方法画布整体或局部擦除。
2. 使用`setInterval`方法设置动画的间隔时间。

# point

​	{% image 1.jpg 图片 %}

​	与其他HTML元素一样，左上角坐标原点(0,0)，向右X增加，向下X增加，对于500x300像素的元素来说，右下角坐标是(500,300)