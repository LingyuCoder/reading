Publish/Subscribe（发布/订阅）模式
===
一种比较常见的实现Observer模式的方式是将ObserverList和Subject相分离，使用一个Event Channel来管理ObserverList，Subject将需要发布的数据交给Event Channel，而观察者也只需要观察Event Channel就行了

![pubsub架构图](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/3.png)


发布/订阅方式很适合JavaScript，因为ECMAScript本身是基于事件驱动的。很多流行的JavaScript库都有一套发布/订阅实现，比如jQuery：
```javascript
//订阅
$(dom).on("channel", callback);
//发布
$(dom).trigger("channel", [arg1, arg2]);
//取消订阅
$(dom).off("channel", callback);
```

##Observer模式和Publish/Subscribe（发布/订阅）模式的区别：
Observer模式要求希望接到主题通知的观察者（或对象）必须订阅内容改变的事件，而Publish/Subscribe模式使用了一个事件通道，这个通道介于希望收到通知的对象（订阅者）和激活事件的对象（发布者）之间。与Observer模式不同的是，它允许任何订阅者执行适当的时间处理程序来注册和接受发布者发出的通知

```javascript
var pubsub = (function() {
    //订阅者列表
    var events = {};
    var subUid = 0;
    //发布
    function publish(eventName, args) {
        if (!events[eventName]) {
            return false;
        }
        var subscribers = events[eventName];
        var len = subscribers.length;
        while (len--) {
            subscribers[len].callback(eventName, args);
        }
    }
    //订阅
    function subscribe(eventName, callback) {
        events[eventName] = events[eventName] || [];
        var id = subUid++;
        events[eventName].push({
            callback: callback,
            id: id
        });
        return id;
    }
    //取消订阅
    function unsubscribe(id) {
        var eventName;
        var i;
        var len;
        var subscribers;
        for (eventName in events) {
            subscribers = events[eventName];
            if (subscribers) {
                for (i = 0, len = subscribers.length; i < len; i++) {
                    if (subscribers[i].id === id) {
                        subscribers.splice(i, 1);
                        return id;
                    }
                }
            }
        }
        return this;
    }
    //利用Revealing Module模式隐藏内部实现，提供接口
    return {
        publish: publish,
        subscribe: subscribe,
        unsubscribe: unsubscribe
    };
}());
```

这上面就是一个简单的Pubsub实现了，可以通过例子验证一下：
```javascript
//在hello事件生增加一个订阅
var subscription = pubsub.subscribe("hello", function(eventName, data) {
    console.log("Event:" + eventName + "/data:" + data);
});
//向订阅者发送消息
pubsub.publish("hello", ["天镶"]);
//向订阅者发送消息
pubsub.publish("hello", ["天镶", "求", "offer"]);
//取消订阅
pubsub.unsubscribe(subscription);
//向订阅者发送消息
pubsub.publish("hello", ["天镶", "再求", "offer"]);
```

结果可以看到，取消之后，订阅者不会再收到消息了：

![pubsub输出结果](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/4.png)

##优点和缺点
Observer模式和Publish/Subscribe模式都能将应用程序分解成更小、更松散耦合的块，以改进代码管理和潜在的复用。并且可以动态的去更改不同部分的关系，有很大的灵活性

在Observer和Publish/Subscribe中，发布者或Subject不会看到订阅者或观察者的错误，且订阅者和观察者相互之间无视彼此的存在，对变化发布者产生的成本也视而不见