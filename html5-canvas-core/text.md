#文本
##方法
有三个与文本有关的方法：

* strokeText(text, x, y)：描边文本
* fillText(text, x, y)：填充文本
* measureText(text)：获取文本的宽度

strokeText和fillText还可以接受可选的第四个参数，用于指定文本的最大宽度

##属性
绘图环境对象中有三个与文本相关的属性：

* font
    * font-style
    * font-variant
    * font-size
    * font-height
    * font-family
* textAlign
* textBaseline

##文本的定位
使用fillText和strokeText绘制文本时，定位根据x、y、textAlign、textBaseline定位。

###textAlign
textAlign可能的取值有：

* start
* center
* end
* left
* right

start和end将根据canvas元素的dir属性（ltr或rtl）来确定，在ltr时，start和end与left和right效果相同


###textBaseline

textBaseline的取值有：

* top：字符方框的顶部
* bottom：字符方框的底部
* middle：字符方框的中间
* alphabetic：默认，用于绘制拉丁字母字符串
* ideographic：用于绘制日文或中文字符串
* hanging：用于绘制印度语字符串

