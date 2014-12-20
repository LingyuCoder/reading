##指令简介
> 内置指令是打包在AngularJS内部的指令。所有内置指令的命名空间都使用ng作为前缀。为了防止命名空间冲突，不要在自定义指令前加ng前缀。

通过AngularJS模块API中的`.directive()`方法，我们可以通过传入一个字符串和一个函数来注册一个新指令。字符串是指令的名称，为驼峰命名，函数返回一个包含定义和配置指令方法和属性的对象

通过template属性能够定义指令的模板，默认情况下AngularJS将模板生成的HTML代码嵌套在自定义标签内部，若replace属性为true，则将直接替换自定义标签

声明指令本质上是在HTML中通过元素、属性、类或注释来添加功能。通过restrict设置来确定指令的声明格式，可以指定以元素（E）、属性（A）、类（C）或注释（M）的格式来调用指令，如：
```
<my-directive></my-directive>
<div my-directive></div>
<div class="my-directive"></div>
<!--directive:my-directive-->
```

> 无论有多少种方式可以声明指令，我们坚持使用属性方式，因为它有比较好的跨浏览器兼容性


声明指令时可以使用表达式：
```
<my-directive="someExpression">
</my-directive>
<div my-directive="someExpression">
</div>
<div class="my-directive:someExpression">
</div>
<!-- directive: my-directive someExpression -->
```
可以通过scope配置来为指令创建隔离作用域让指令拥有自己的`$scope对象`。但隔离作用域无法直接在scope配置中赋值，scope配置中只能添加`@`这样的绑定方式，隔离作用域同当前DOM的作用域是完全分隔开的。为了给这个新的对象设置属性，我们需要显式地通过属性传递数据

内置指令`ng-model`在它自身内部的隔离作用域和DOM的作用域（由
控制器提供）之间创建了一个双向数据绑定