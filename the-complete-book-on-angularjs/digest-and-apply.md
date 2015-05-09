#digest循环和$apply

$digest循环主要由两个部分组成：

* $watch列表
* $evalAsync列表

##$watch列表
Angular通过向$watch列表中增加监控函数来追踪作用域上的属性变化

$watch列表会在$digest循环中通过“脏检查”来执行

##脏检查
脏检查：检查值是否发生了变化，而整个应用还没同步该变化

Angular应用会遍历$watch列表，，如果从旧值更新后的值没有发生变化，它会继续遍历监控列表。如果值发生了变化，该应用会启用新值并继续遍历$watch列表。


由于$watch列表中可能存在某个值，会变更另一个值。所以遍历完整个$watch列表后，只要有任何值发生变化，应用将会退回到$watch循环中，直到检测到不再有任何变化。

如果循环运行了10次以上，就会抛出异常并停止运行，防止由于值之间的相互变更而导致的无线循环。

> 若支持Object.observe()可以大大加速脏检查速度

##$watch
$watch函数本身接受两个必要参数和一个可选的参数：

1. watchExpression（String | Function）：
    * 字符串：在$scope上下文中对它求值
    * 函数：返回的就是被监控的值
2. listener/callback（Function）：作为回调的监听器函数，它只会在watchExpression的当前值与先前值不相等（除了首次运行初始化期间）时调用。
3. objectEquality（Bool，可选）：是否检查严格相等

$watch函数返回一个注销函数，能够取消监控

```javascript
//...
var unregisterWatch =
    $scope.$watch('newUser.email',
        function(newVal, oldVal) {
            if (newVal === oldVal) return; // 初始化
        });
// ...
// 稍后，可以通过调用这个注销函数来注销这个监控器
unregisterWatch();
```

> 永远不要在控制器中使用$watch，因为它会使控制器难以测试

##$watchCollection
Angular允许使用`$watchCollection()`监控对象的属性和数组的元素。`$watchCollection()`的行为与`$digest`循环中标准的`$watch()`的行为一样

$watchCollectiion()函数接受2个参数：

1. obj（String | Function）：监控的对象
    * 字符串：当做Angular表达式求值
    * 函数：返回要监控的值
2. listener（Function）：当前值与先前值不相等时调用

`$watchConllection()`函数也返回一个注销函数

```javascript
$scope.$watchCollection('names',
    function(newNames, oldNames, scope) {
        // names集合已经发生了变化
    });
```

##$evalAsync列表
`$digest`循环运行的第二个操作是执行`$$asyncQueue`队列。可以使用`$evalAsync()`方法访问这个工作队列

使用`$evalAsync()`来调用任何函数都会发生两件事情：

1. 函数会在这个方法被调用的某个时刻之后执行
2. 表达式求值之后至少会执行一次$digest循环

`$evalAsync()`方法接受一个唯一参数：

* expression（String | Function）：想要在当前作用域上执行的东西
    * 字符串：在当前作用域上使用`$eval()`求值
    * 函数：传入scope对象，执行函数求值

```javascript
$scope.$evalAsync('attribute',
    function(scope) {
        scope.foo = "Executed"
    });
```

`$evalAsync()`会在操作DOM之后、浏览器渲染之前运行，如果想要在一个行为的执行上下文外部执行另一个行为，就应该使用`$evalAsync()`函数。

##$apply
`$apply()`函数可以从Angular的外部让表达式在Angular上下文内部执行

`$apply()`函数接受一个可选的参数：

* expression（String | Function）：想要在当前作用域上执行的东西
    * 字符串：在当前作用域上使用`$eval()`求值
    * 函数：传入scope对象，执行函数求值

> `$exceptionHandler`服务会捕获和处理`$eval()`方法抛出的所有异常

`$apply()`会触发$digest循环

```javascript
// 使用要eval的字符串调用$apply示例
$scope.$apply('message = "Hello World"');
// 使用函数的方式并给函数传入一个作用域
$scope.$apply(function(scope) {
    // 然后在函数中使用传入作用域
    scope.message = "Hello World";
});
// 使用函数时忽略作用域
$scope.$apply(function() {
    $scope.message = "Hello World";
});
// 或者通过在操作的尾部调用$apply()以强制运行$digest循环
$scope.$apply();
```

##何时使用$apply
当我们将jQuery和Angular集成在一起时（这通常被视为一个肮脏的行为），就需要使用`$apply()`，因为Angular不会察觉到执行在Angular上下文外部的事件。