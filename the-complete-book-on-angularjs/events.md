#事件
##什么是事件
Angular事件系统并不与浏览器的事件系统相通，这意味着，我们只能在作用域上监听Angular事件而不是DOM事件。

##事件传播
###向上传递
通过`$emit()`把事件沿着作用域链向上派送（从子作用域到父作用域）

```javascript
scope.$emit('user:logged_in', scope.user);
```

###向下传递
通过`$broadcast()`把事件向下传递（从父作用域到子作用域）

```javascript
scope.$broadcast('cart:checking_out', scope.cart);
```

###异常处理
所有异常都会交由`$exceptionHandler`服务处理

##事件监听
通过`$on()`方法监听事件，Angular把evt对象（事件对象）作为第一个参数传给正在监听的一切事件，不管它是我们自定义的事件还是内置的Angular服务

```javascript
scope.$on('$routeChangeStart',
    function(evt, next, current) {
        // 一个新的路由被触发了
    });
```

##事件对象
事件对象有如下属性：

1. targetScope（作用域对象）：发送或者广播事件的作用域。
2. currentScope（作用域对象）：当前处理事件的作用域。
3. name（字符串）：事件名称。
4. stopPropagation（函数）：`stopPropagation()`函数取消通过`$emit`触发的事件的进一步传播。
5. preventDefault（函数）：`preventDefault()`把`defaultPrevented`标志设置为`true`。不能停止事件的传播，告诉子作用域无需处理这个事件
6. defaultPrevented（布尔值）：调用`preventDefault()`会把`defaultPrevented`设置为`true`。

> `$on()`返回反注册函数用于取消监听

##事件相关的核心服务
###核心系统的$emit事件

* `$includeContentLoaded`：当ngInclude的内容重新加载时，从ngInclude指令上触发。
* `$includeContentRequested`：从调用ngInclude的作用域上发送。每次ngInclude的内容被请求时，它都会被发送。
* `$viewContentLoaded`：每当ngView内容被重新加载时，从当前ngView作用域上发送。

###核心系统的$broadcast事件

####location相关
* `$locationChangeStart`：通过`$location`服务（如`$location.path()`、`$location.search()`等）对浏览器的地址作更新时触发
* `$locationChangeSuccess`：浏览器的地址成功变更，同时没有阻止`$locationChangeStart`事件的情况下，会从`$rootScope`广播

####route相关
route相关事件在路由改变过程中在`$rootScope`上触发：

* `$routeChangeStart`：路由变更发生之前
* `$routeChangeSuccess`：所有路由依赖项 被解析之后
* `$routeChangeError`：路由对象上任意的resolve属性被拒绝了，就会被触发
* `$routeUpdate`：如果`$routeProvider`上的`reloadOnSearch`属性被设置成`false`，并且使用了控制器的同一个实例，就会触发

####作用域销毁
`$destroy`事件会在作用域被销毁之前广播，用于给子作用域一个机会，在父作用域被真正移除之前清理自身。