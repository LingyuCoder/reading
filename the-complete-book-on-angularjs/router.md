#多重视图和路由
##安装
使用ngRoutes模块可以构建前端路由，将模板分解到视图中，并在布局模板内进行组装

可以在`$route`服务的提供者`$routeProvider`中通过声明路由来实现

##布局模板
将`ng-view`指令和路由组合到一起，指定当前路由所对应的模板在DOM中的渲染位置，`ng-view`给`$route`对应的视图内容占位，它会创建自己的作用域并将模板嵌套在内部

> ng-view是一个优先级为1000的终极指令。AngularJS不会运行同一个元素上的低优先级指令（例如<div ng-view></div>元素上其他指令都是没有意义的）。


ngView指令遵循以下规则：

* 每次触发`$routeChangeSuccess`事件，视图都会更新。  
* 如果某个模板同当前的路由相关联： 
    * 创建一个新的作用域； 
    * 移除上一个视图，同时上一个作用域也会被清除；  
    * 将新的作用域同当前模板关联在一起；  
    * 如果路由中有相关的定义，那么就把对应的控制器同当前作用域关联起来
    * 触发`$viewContentLoaded`事件
    * 如果提供了onload属性，调用该属性所指定的函数


##路由
可以使用AngularJS提供的`when()`和`otherwise()`两个方法来定义应用的路由。

###when
when方法来添加一个特定的路由，接受两个参数：

1. 路由路径：与`$location.path`进行匹配，`$location.path`也就是当前URL的路径
2. 配置对象: 决定了当第一个参数中的路由能够匹配时具体做些什么。配置对象中可以进行设置的属性包括:
    * controller
    * template
    * templateURL
    * resolve
    * redirectTo
    * reloadOnSearch


####controller
指定控制器，这个控制器会与路由所创建的新作用域关联在一起，接受两种类型：

* 字符串：在模块中所有注册过的控制器中查找对应的内容，然后与路由关联在一起
* 函数：这个函数会作为模板中DOM元素的控制器并与模板进行关联

####template
将HTML模板渲染到对应的具有`ng-view`指令的DOM元素中

####templateUrl
根据templateUrl属性所指定的路径通过XHR读取视图（或者从`$templateCache`中读取）。如果能够找到并读取这个模板，会将模板的内容渲染到具有ng-view指令的DOM元素中。

####resolve
如果设置了resolve属性，会将列表中的元素都注入到控制器中。

如果这些依赖是promise对象，它们在控制器加载以及`$routeChangeSuccess`被触发之前，会被resolve并设置成一个值。 

列表对象可以是：

* 键：键值是会被注入到控制器中的依赖的名字 
* 工厂：既可以是一个服务的名字，也可以是一个返回值，它是会被注入到控制器中的函数或可以被resolve的promise对象

####redirectTo
redirectTo可以接受两种参数：

1. 字符串：路径会被替换成这个值，并根据这个目标路径触发路由变化
2. 函数：路径会被替换成函数的返回值，并根据这个目标路径触发路由变化。Angular会向函数传递三个参数：
    * 从当前路径中提取出的路由参数
    * 当前路径
    * 当前URL中的查询串

####reloadOnSearch
如果被设置为true（默认），当`$location.search()`发生变化时会重新加载路由。

对路由嵌套和原地分页等需求非常有用

###$routeParams
如果我们在路由参数的前面加上`:`，AngularJS就会把它解析出来并传递给$routeParams

##$location服务

`$location`服务对JavaScript中的`window.location`对象的API进行了更优雅地封装

`$location`服务没有刷新整个页面的能力，需要使用`$window.location`对象替代


* path()：用来获取页面当前的路径：
* replace()：跳转后用户不能点击后退按钮
* absUrl()：用来获取编码后的完整URL：
* hash()：用来获取URL中的hash片段：
* host()：用来获取URL中的主机：
* port()：用来获取URL中的端口号
* protocol()：用来获取URL中的协议
* search()：用来获取URL中的查询串，可以向这个方法中传入新的查询参数，来修改URL中的查询串部分
* url()：用来获取当前页面的URL

##HTML5模式
不同的路由模式在浏览器的地址栏中会以不同的URL格式呈现。$location服务默认会使用标签模式来进行路由

`$location`服务通过HTML5历史API让应用能够使用普通的URL路径来路由。当浏览器不支持HTML5历史API时，会自动使用标签模式的URL作为替代方案。

###标签模式
URL路径会以#符号开头

###HTML5模式
URL看起来和普通的URL一样

###路由事件
用`$rootScope`来监听这些事件：

* 路由变化之前会广播`$routeChangeStart`事件
* 路由的依赖被加载后广播`$routeChangeSuccess`事件
* 任何一个promise被拒绝或者失败时广播`$routeChangeError`事件。
* reloadOnSearch属性被设置为false的情况下，重新使用某个控制器的实例时，会广播`$routeUpdate`事件。

###异步的地址变化
想重新加载整个页面，需要用$window服务来设置地址: `$window.location.href = "/reload/page"`
