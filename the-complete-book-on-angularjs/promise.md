#promise
##什么是promise
promise是一种用异步方式处理值（或者非值）的方法。promise是对象，代表了一个函数最终可能的返回值或者抛出的异常。

```javascript
User.get(fromId)
    .then(function(user) {
        return user.friends.find(toId);
    }, function(err) {
        // 没找到用户
    })
    .then(function(friend) {
        return user.sendMessage(friend, message);
    }, function(err) {
        // 用户的朋友返回了异常
    })
    .then(function(success) {
        // user was sent the message
    }, function(err) {
        // 发生错误了
    });
```
##为什么使用promise
使用promise的目的是：获得功能组合和错误冒泡（error bubbling）能力的同时，保持代码异步运行的能力。同时，可以摆脱回调地狱

promise是头等对象，自带了一些约定：

* 只有一个resolve或者reject会被调用到：
    * resolve被调用时，带有一个履行值；
    * reject被调用时要带一个拒绝原因。
* 如果promise被执行或者拒绝了，依赖于它们的处理程序仍然会被调用；
* 处理程序总是会被异步调用。

可以把promise串起来，并且允许代码以通常运行的方式来处理。从一个promise冒出的异常会贯穿整个promise链。

##Angular中的promise
Angular的事件循环给予了Angular特有的能力，能在`$rootScope.$evalAsync`阶段中执行promise。promise会坐等$digest运行循环结束。

可以使用内置的$q服务创建promise。通过`defer()`创建

```javascript
var deferred = $q.defer();
```

创建的对象有如下三个方法：

* resolve(value)：用值来执行deferred promise
* reject(reason)：用一个原因来拒绝deferred promise。它等同于使用一个“拒绝”来执行一个promise。
* notify(value)：用promise的执行状态来进行响应

```javascript
deferred.reject("Can't update user");
// 等同于
deferred.resolve($q.reject("Can't update user"));
```

可以通过以下方法与promise交互：

* `then(successFn, errFn, notifyFn)`：总是返回一个新的promise，可以通过successFn或者errFn这样的返回值执行或者被拒绝。它也能通过notifyFn提供通知。
* `catch(errFn)`：只是个帮助函数，能让我们用.catch(function(reason){})取代err回调
* `finally(callback)`：允许我们观察promise的履行或者拒绝，而无需修改结果的值，不管promise是成功还是失败

> 需要注意在IE中finally是关键字，所以需要通过`promise['finally'](function() {});`的方式调用

> $http的拦截器就是用promise实现的

##链式请求
then方法在初始promise被执行之后，返回一个新的派生promise。因此可以创建一个执行链，并在链中传递结果

```javascript
GithubService.then(function(data) {
    var events = [];
    for (var i = 0; i < data.length; i++) {
        events.push(data[i].events);
    }
    return events;//events被传递到下一个then
}).then(function(events) {
    $scope.events = events;
});
```

同时$q库还进行了流程控制，提供了几个有用的方法

###all(promises)
使用$q.all(promises)方法来把多个promise合并成一个

* promises：一个promise数组或promise对象

all()方法返回单个promise，会执行一个数组或者一个散列的值。每个值会响应promise散列中的相同序号或者键。如果任意一个promise被拒绝了，结果的promise也会被拒绝。

###defer()
创建了一个deferred对象

###reject(reason)
创建了一个promise，被以某一原因拒绝执行了。它专门用于让我们能在一个promise链中转发拒绝的promise，类似于throw

* reason（常量、字符串、异常、对象）：拒绝的原因

###when(value)
把一个可能是值或者能接着then的promise包装成一个$q的promise

* value：该参数是个值，或者是promise

`when()`函数返回了一个promise