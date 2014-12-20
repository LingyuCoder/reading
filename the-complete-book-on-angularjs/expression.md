##表达式
用`{{ }}`符号将一个变量绑定到`$scope`上的写法本质上就是一个表达式：`{{ expression }}`。当用`$watch`进行监听时，AngularJS会对表达式或函数进行运算

表达式与`eval`相似，但表达式由AngularJS处理不同之处在于：
* 所有的表达式都在其所属的作用域内部执行，并有访问本地`$scope`的权限；
* 如果表达式发生了TypeError和ReferenceError并不会抛出异常；
* 不允许使用任何流程控制功能（条件控制，例如if/eles）；
* 可以接受过滤器和过滤器链。

AngularJS在运行`$digest`循环过程中自动解析表达式，也可以通过`$parse`手动解析


###插值字符串
通过`$interpolate`服务能够在字符串模板中做插值操作，来手动编译模板，其接收三个参数：
1. text-字符串-必需：一个包含字符插值标记的字符串。
2. mustHaveExpression-布尔：参数为`true`时，传入字符串不含有表达式时返回`null`.
3. trustedContext（字符串）：AngularJS会对已经进行过字符插值操作的字符串通过`$sec.getTrusted()`方法进行严格的上下文转义

`$interpolate`服务返回一个函数，用来在特定的上下文中运算表达式。


`$watch`函数会监视`$scope`上的某个属性。只要属性发生变化就会调用对应的函数。可以使用`$watch`函数在`$scope`上某个属性发生变化时直接运行一个自定义函数。

如果需要在文本中使用不同于`{{ }}`的符号来标识表达式的开始和结束，可以在`$interpolateProvider`中配置：
* 用startSymbol()方法可以修改标识开始的符号
* 用endSymbol()方法可以修改标识结束的符号