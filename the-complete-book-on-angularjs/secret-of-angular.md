#揭秘Angular
当浏览器触发DOMContentLoaded事件时，Angular就开始工作。它首先寻找ng-app指令。

如果document.readyState被设置为complete，Angular也会启动初始化

还可以通过`bootstrap()`方法手动启动，但只允许启动一次

Angular会使用ng-app指令的值配置$injector服务，一旦应用程序加载完成，$injector会创建$rootScope和$compile服务。$rootScope创建后，$compile服务就会将$rootScope与现有的DOM连接起来，然后从将ng-app指令设置为祖先开始编译DOM。

##视图的工作原理
> DOMContentLoaded事件会在HTML文档加载完成并开始解析时触发

###编译阶段
$compile服务遍历DOM树并搜集所有指令，然后将这些指令的链接函数合并为一个。这个链接函数会将编译好的模板链接到$rootScope中


$compile服务查找指令时，当碰到带有一个或多个
指令的DOM元素时，它对指令按照优先级排序，然后使用$injector服务查找和收集指令的compile函数并执行它，指令中的compile函数会在适当的时候处理所有DOM转换或者内联模板。每个节点的编译方法运行之后，$compile服务就会调用链接函数。这个链接函数为绑定了封闭作用域的指令设置监控


###运行时
指令会注册事件监听器。当事件被触发时，指令函数就会运行在AngularJS的$digest循环中。

Angular在$rootScope上启动$digest循环时开始整个过程，并且还会传播到所有子作用域中。

一旦$digest循环稳定下来，并且检测到没有潜在的变化了，执行过程就会离开Angular上下文并且通常会回到浏览器中，DOM将会被渲染到这里。