# 基础知识
## canvas元素
### canvas元素的大小
* 设置canvas的宽度与高度时，不能使用px。使用px不符合Canvas规范，虽然浏览器普遍支持，但最好只设置非负整数
* 默认情况下，canvas的大小事300像素宽，150像素高，可以通过width和height来改变。但**如果通过CSS来改变，将会产生意外后果**。canvas本身有两套尺寸，一套是元素本身的大小，还有一个是元素绘图表面的大小。设置width和height属性时，两者都会改变。而**通过CSS设置时，只会改变元素本身的大小，而不会改变绘图表面大小**。**当canvas元素大小和绘图表面大小不符时，浏览器会对绘图表面进行缩放**

### canvas元素的属性
* width
* height

### canvas元素的方法
* getContext()：获取绘图环境对象
* toDataURL(type, quality)：转dataurl
* toBlob(callback, type, args)：转二进制

## 绘图环境
### 简介
* canvas元素仅仅作为绘图环境对象的容器而存在，真正绘图是通过绘图环境对象来实现，也就是context

### 绘图环境对象的属性
* canvas：指向canvas对象，可以通过它获取宽高
* fillstyle：后续图形填充操作中使用的颜色、渐变或团
* font：fillText（）或trokeText（）时使用的字型
* globalAlpha：全局透明度
* globalCompsiteOperation：浏览器将某个物体绘制在其他物体之上时采用的绘制方式
* lineCap：如何绘制线段端点，butt或round或square，默认为butt
* lineWidth：线段的宽度，默认为1
* lineJoin：两条线段的交点，bevel、round、miter，默认为miter
* miterLimit：如何绘制miter形式的线段交点
* shadowBlur：如何延伸阴影效果，值越高，阴影越远
* shadowColor：阴影颜色
* shadowOffsetX、shadowOffsetY：阴影偏移
* strokeStyle：对路径进行描边时所用的绘制风格，颜色、渐变色或图案
* textAlign：fillText或strokeText绘制时，文本的水平对齐方式
* textBaseline： fillText或strokeText绘制时，垂直对齐方式

### 扩展功能
可以通过给canvas绘图环境对象编写方法的方式来提供扩展功能

### 状态的保存和恢复
canvas的api中提供了两个方法：

* save()：保存当前canvas绘图环境的**所有属性**
* restore()：恢复当前canvas绘图环境的所有属性

这里只是保存属性，不是保存画布

save和restore如同一个栈，可以嵌套式的调用

## 学习HTML5
### 浏览器
如果canvas需要支持IE6~8，有两种选择：

*   explorecanvas：在老版本的IE浏览器中增加canvas支持
*   Google Chrome Frame：将IE引擎替换成Google Chrome浏览器引擎

## 事件处理
### 鼠标事件
canvas和其他元素一样可以通过`addEventListener()`方法来注册事件处理函数，可以监听如`mousedown`,`mousemove`,`mouseup`,`mouseout`等事件

浏览器通过事件对象的`clientX`和`clientY`传递给处理函数的坐标是窗口坐标，而不是相对于canvas自身的坐标，所以需要转换。canvas提供一个`getBoundingClientRect()`方法来获取canvas边界值，如下方法可以获取鼠标在canvas之中的坐标：

```javascript
function windowToCanvas(canvas, x, y) {
    var bbox = canvas.getBoundingClientRect();
    return {
        x: (x - bbox.left) * (canvas.width / bbox.width),
        y: (y - bbox.top) * (canvas.height / bbox.height)
    };

}
```
当前支持HTML5的浏览器，都支持clientX和clientY

### 键盘事件

按下按键时，如果焦点在元素上，就会触发该元素的键盘事件。canvas是一个**不可获取焦点**的元素，所以需要在document或window上增加键盘事件的处理函数。键盘事件一共三种：

* keydown
* keypress
* keyup

绝大部分按键的敲击，包括alt、esc等按键，能够通过keydown和keyup捕获，他们是底层事件。而keypress则捕获可打印的字符。其触发顺序为：keydown -&gt; keypress -&gt; keyup，长按在会在keydown和keyup之间产生一系列的keypress事件。

一般通过keycode来判断按下的值，事件对象中也有一些Boolean属性判断特殊按键：

* altKey
* ctrlKey
* metaKey
* shiftKey

## 绘制表面的保存与恢复

canvas上拖动的实现：检测到mousedown事件后，应用程序将绘图表面保存起来，在接下来的mousemove过程中，先绘制保存的内容，再绘制需要被拖动的内容和辅助线，在mouseup的时候最后一次绘制。

保存通过`getImageData()`方法，恢复用`putImageData()`方法

## 在canvas中使用HTML元素
一般很少单独使用canvas元素，绝大多说情况下，都会将一个或多个canvas元素和其他HTML控件结合起来使用，以便用户可以通过输入数值或其它方式来控制应用程序

canvas的背景是不透明的

canvas规范中说，应当首先考虑使用内置的HTML控件，而不是使用canvas api来从头实现控件

可以通过toDataURL方法将canvas的数据放在img中进行打印

canvas可以离屏操作，display设为none就行了
