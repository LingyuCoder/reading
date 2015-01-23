# 绘制
## 坐标系统
canvas的坐标系统以左上角为原点，X坐标向右增长，Y坐标向下增长，默认为300*150。基于坐标来做如下变换：

* 平移
* 旋转
* 缩放
* 自定义变换

## 绘制模型
要了解canvas如何绘制图形、图像和文本，需要了解阴影、alpha通道、剪辑区域和图像合成等内容。

向canvas上绘制图像，浏览器需要经过如下步骤：

1. 将图形或图像绘制到一个**无限大的透明位图**中，绘制时遵从当前的填充模式、描边模式及线条样式
2. 将图形或图像的阴影会知道**另外一幅位图**中，在绘制时使用当前绘图环境的阴影设置
3. 将阴影中的每一个像素的alpha值分量乘以绘图环境对象的globalAplha属性值
4. 将绘有阴影的位图和经过剪辑区域剪切过的canvas图像进行合成。在合成时使用当前的合成模式参数
5. 将图形或图像的每一个像素颜色分量，乘以绘图环境对象的globalAlpha属性值
6. 将会有图形或图像的位图，合成到当前经过剪辑区域剪切过的canvas位图之上，使用当前的合成模式参数

只有启用阴影时，才会有2~4步

## 矩形绘制

三个方法：

* clearRect(x, y, w, h)：清除矩形区域
* strokeRect(x, y, w, h)：描边矩形区域，影响属性有：
    * strokeStyle
    * lineWidth
    * lineJoin
    * miterLimit
* fillRect(x, y, w, h)：填充矩形区域

可以通过lineJoin属性来绘制圆角矩形，但是如果想要控制如圆角半径之类的属性，就得自己绘制这些圆角了

## 颜色和透明度

在strokeStyle和fillStyle中可以指定CSS颜色，支持RGB、RGBA、HSL、HSLA、16进制RGB，或者颜色名称。

RGB方式指定颜色，有两个缺陷：

1. 它是以硬件为导向的，它基于阴极射线管
2. 它不直观

HSL三个分量是色相、饱和度、亮度

除了在RGBA或HSLA中来指定透明度，也可以通过globalAlpha来设定全局透明度

另外，strokeStyle和fillStyle不只可以设置颜色，还可以设置渐变色或图案

## 渐变色或图案

canvas支持线性（linear）渐变和放射渐变（radial）。

线性渐变需要指定渐变线，可以通过`createLinearGradient()`来创建线性渐变，它会返回一个**CanvasGradient实例**。CanvasGradient唯一的方法就是`addColorStop()`，可以添加颜色停止点。

放射渐变需要指定两个圆形。表示渐变的起始位置和终止位置。通过`createRadialGradient()`方法创建放射渐变，这个方法同样返回一个**CanvasGradient实例**。放射渐变的填充范围仅仅局限于那两个圆形所定义的圆锥区域。

canvas也允许使用图案来对毒性或文本进行描边与填充。可以使用三种：

* image元素
* canvas元素
* video元素

通过`createPattern()`创建图案，它接受两个参数，图案本身和重复规则，重复规则可以为：repeat、repeat-x、repeat-y、no-repeat

## 阴影

通过四个值来指定阴影效果：

* shadowColor：阴影颜色
* shadowOffsetX：阴影X轴偏移
* shadowOffsetY：阴影Y轴偏移
* shadowBlur：高斯模糊方程中的参数，控制阴影模糊程度

通常来说都通过半透明来绘制阴影，这样就可以显示背景了

根据canvas规范，只有符合如下情形时，才会绘制阴影：

1. 指定了一个非完全透明的shadowColor值
2. 在其余的三个属性至少有一个不是0

最简单的禁用阴影方式，就是将shadowColor设为undefined，但目前只有webkit能用

内嵌阴影实现：通过负偏移量绘制阴影，然后通过clip方法裁剪，把阴影局限在图形内

需要注意的是：阴影的绘制可能很耗时间，它需要先渲染到一个辅助位图中，然后还需要合成

## 路径、描边与填充

### 路径

SVG、Apple的Cocoa框架，Adobe Illustrator等，都是基于路径的，然后对其进行描边。无论一个路径是开放或是封闭，都可以对其进行填充，当填充某个开放路径时，浏览器也会把它当成封闭路径来填充

使用`beginPath()`来开始一段新的路径，`rect()`与`arc()`方法分别用于创建矩形及弧形路径，然后在绘图环境对象调用`stroke()`与`fill()`方法来进行描边或填充。

描边与填充操作的效果取决于当前的绘图属性，包括lineWidth、strokeStyle、fillStyle以及阴影属性

rect方法绘制的路径是封闭的，而arc方法若创建的不是圆形路径，就是不封闭的。要封闭路径，需要调用`closePath()`方法

### 子路径

在同一时刻，canvas只能有一条路劲存在，这条路局可以包含许多子路径。子路径可以由两个或更多的点组成。

### 非零环绕规则

如果当前路径是循环的，或是包含多个相交的子路径，那么canvas的绘图环境变量就需要判断，调用`fill()`时，应当如何进行填充，填充规则符合非零环绕规则

**非零环绕规则**：对于路径中的任意给定区域，从该区域内部画一条足够长的线段，是此线段的终点完全落在路径范围之外。接下来，将计数器初始化为0，然后，每当这条线段与路径上的直线或曲线相交时，就改变计数器的值。如果是与路径的顺时针部分相交，则加1，如果与路径的逆时针部分相交，则减1。若计算器的最终值不是0，那么此区域就在路径里面，在调用`fill()`方法时，浏览器就会对其进行填充。如果最终值是0，那么此区域就不在路径内部，浏览器也就不会对其进行填充。

### 剪纸效果

圆环：两条圆形路径，外面为逆时针，里面为顺时针。

**rect方法总是按照顺时针方向来创建路径**，所以需要绘制逆时针路径就需要手动去配置

## 线段

### 绘制

有两个用来创建线性路径的方法：`moveTo()`和`lineTo()`，可以用来绘制线段

如果在某两个像素的边界处绘制一条1像素宽的线段，但实际上会占据两个像素的宽度。此时，canvas的绘图环境对象会试着将半个像素华仔边界中线的右边，另外半个像素在左边。但是半个像素宽的线段不可能，所以会占据两个像素。但目前很多浏览器都是使用了“抗锯齿”技术，以便创建出“亚像素的绘制效果”

### 画图中的线段实现

画图中的线段绘制实现：

1. mousedown时通过getImageData暂存当前数据，并记录当前点为起始点
2. mousemove的时候，先putImageData写入之前的数据，然后以起点和当前点绘制一条线段
3. mouseup的时候最后一次绘制

### 虚线

虚线的绘制：计算虚线的总长度，然后根据每条短划线的长度，算出整个虚线中应该含有多少个这样的短划线。代码根据计算出的短划线数量，通过反复绘制来实现虚线。绘制虚线时，必须要知道上一次调用`moveTo()`时所传入的位置参数，通过拦截moveTo方法来记录即可

### 端点

通过`lineCap`属性能够控制线段端点的样式，round在端点处多画一个半圆，半径为线段宽度的一半。square则多花一个矩形，长度与线宽一致，宽度为线宽的一半

线段连接点的绘制是由`lineJoin`属性控制，bevel在相交的时候，用一条直线来连接拐角外部的点，构成一个三角形，然后fill。miter则会多绘制一个三角形，变成一个矩形，另外可以设置miterLimit。为round则会花上一段填充好的圆弧

## 圆弧与圆形的绘制

### arc

圆弧和圆形都用`arc()`方法绘制，有6个参数：

1. x
2. y
3. radius
4. startAngle
5. endAngle
6. counterClockwise

arc方法的最后一个参数用来决定绘制方向是顺时针还是逆时针。它是可选的参数，但最好不要省略

arc方法会将上一条子路径的终点与圆弧路径的起点相连

### arcTo

另外还可以使用`arcTo()`方法创建点到点之间的圆弧，有5个参数：

1. x1
2. y1
3. x2
4. y2
5. radius

这个方法非常适合用来挥之矩形的圆角

## 贝塞尔曲线

贝塞尔曲线分为两种：平方贝塞尔曲线及立方贝塞尔曲线。

平方贝塞尔曲线由三个点来定义，两个锚点及一个控制点。而立方贝塞尔曲线则是一种三次曲线，由两个锚点两个控制点组成。

可以通过`quadraticCurveTo()`方法来绘制二次贝塞尔曲线。

可以通过`bezierCurveTo()`方法来绘制三次贝塞尔曲线

## 多边形

canvas并没有提供绘制多边形的方法，所以需要通过lineTo和moveTo来绘制

## 高级路径操作

canvas提供了一个`pointInPath()`的方法，如果某个点在当前路径中，那么该方法就返回true

## 坐标变换

canvas提供了如下方法进行坐标变换：

* rotate(double angleInRadians)：旋转
* scale(double x, double y)：缩放
* translate(double x, double y)：平移

scale(-1, 1)能够绘制水平镜像，scale(1, -1)能够绘制垂直镜像

### 自定义变换

除了使用上面的三个方法，这三个方法提供简便的手段，用于操作绘图环境对象的**变换矩阵**。在默认情况下，这个变换矩阵是“单位矩阵”。

可以通过`transform()`和`setTransform()`，前者在之前的变换矩阵上叠加，后者则是直接运用。

### 变换所用的代数方程

`translate(dx, dy)`平移后的新坐标等式：

```javascript
x = x + dx;
y = x + dy;
```

`scale(sx, sy)`缩放后的新坐标等式：

```javascript
x = x * sx;
y = y * sy;
```
    
`rotate(angle)`旋转后的新坐标等式：

```javascript
x = x * cos(angle) - y * sin(angle);
y = y * cos(angle) + x * sin(angle);
```

`transform(a, b, c, d, e, f)`和`setTransform(a, b, c, d, e, f)`变换后的新坐标等式：

```javascript
x = ax + cy + e;
y = bx + dy + f;
```

通过transform实现平移：

1. a = 1
2. b = 0
3. c = 0
4. d = 1
5. e = dx
6. f = dy

通过transform实现缩放：

1. a = sx
2. b = 0
3. c = 0
4. d = sy
5. e = 0
6. f = 0

通过transform实现旋转：

1. a = cos(angle)
2. b = sin(angle)
3. c = -sin(angle)
4. d = cos(angle)
5. e = 0
6. f = 0

通过transform实现斜切：

横向斜切：

1. a = 1
2. b = 0
3. c = k
4. d = 1
5. e = 0
6. f = 0

纵向斜切：

1. a = 0
2. b = 1
3. c = 1
4. d = k
5. e = 0
6. f = 0

## 图像合成

通过设置`globalCompositeOperation`属性，能够改变默认图像的合成行为，可以为如下几种值：

* source-atop
* source-in
* source-out
* source-over
* destination-atop
* destination-in
* destination-out
* destination-over
* lighter
* copy
* xor

## 剪辑区域

可以通过`clip()`方法显式定义剪辑区域，否则，默认情况下剪辑区域与canvas大小一致。橡皮擦可以通过`clip()`或者`clearRect()`来实现