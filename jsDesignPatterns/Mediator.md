Mediator——中介者模式
===
中介者是一种行为设计模式，允许我们公开统一的接口，系统的不同部分可以通过这个接口进行通信

如果一个系统各个组件之间有太多的直接关系，两两之间若有一条边，解耦将会非常困难。但如果我们在其中加入一个中心控制点，各个组件都经过这个中心控制点进行通信，那么每个模块只要关注自己和中心控制点的通信就行了。Mediator就是这个意思。

![mediator架构图](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/5.png)

##实现方式
Mediator模式本质上是Oberver模式的共享目标，把中心控制点当做Subject，而每个组件当做Observer：

```javascript
var mediator = (function() {
    //保存事件和订阅者列表
    var events = {};
    //在事件上添加订阅者
    function subscribe(eventName, callback) {
        events[eventName] = events[eventName] || [];
        events[eventName].push({
            context: this,
            callback: callback
        });
        return this;
    }
    //发布事件
    function publish(eventName) {
        var args = Array.prototype.slice.call(arguments, 1);
        var i;
        var subscribers = events[eventName];
        var subscriber;
        if (!subscribers) {
            return false;
        }
        for (i = subscribers.length; i--;) {
            subscriber = subscribers[i];
            subscriber.callback.call(subscriber.context, args);
        }
        return this;
    }
    return {
        publish: publish,
        subscribe: subscribe,
        install: function(obj) {
            obj.publish = publish;
            obj.subscribe = subscribe;
            return this;
        }
    };
}());
```

基本上和Observer模式的思路没有太大区别

##实例
上面这段代码,可以写个例子感受一下,比如有三个模块,分别是colleague1,colleague2,colleague3，而1和2需要监听3的发送的数据，而1和3需要监听2发送的数据，可以这样实现：
```javascript
//模块构造函数
function Colleague(name) {
    this.name = name;
}
//创建三个模块
var colleague1 = new Colleague("col1");
var colleague2 = new Colleague("col2");
var colleague3 = new Colleague("col3");
//为每个模块创建获得数据后的处理，这里只要输出看结果就好
function callback() {
    console.log(this.name + ": " + Array.prototype.slice.call(arguments));
}
//将中介者模式应用到三个模块上，使他们拥有订阅和发布的接口
mediator.install(colleague1).install(colleague2).install(colleague3);
//模块3发布消息，模块1和模块2获取消息并输出
colleague1.subscribe("pub3", callback);
colleague2.subscribe("pub3", callback);
colleague3.publish("pub3", "天镶", "求", "offer");
//模块2发布消息，模块1和模块3获取消息并输出
colleague1.subscribe("pub2", callback);
colleague3.subscribe("pub2", callback);
colleague2.publish("pub2", "天镶", "想", "吃串");
```

输出的结果如下：

![mediator输出](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/6.png)

信息传递都成功了

##Mediator的优缺点
Mediator最大的好处就是将系统组件间的复杂的多对多关系简化到了一对多。这样解耦程度较高，添加发布者和订阅者也较为简单。而缺点则是，Mediator本身在通信的中心，其故障将导致所有组件瘫痪。同时模块间间接的通信，也会导致性能下降。而由于松耦合的性质，很难通过仅关注官兵共来确定系统如何做出反应

##Mediator与其他模式
###Mediator与Observer
四人帮对两者区别的解释：
> 在Observer中，不存在封装约束的单一对象。Observer和Subject必须合作才能维持玉树。通信由观察者和目标互联的方式所决定：单一目标通常有很多观察者，又是一个目标的观察者是另一个观察者的目标

Mediator和Observer都能促进松耦合，但Mediator严格限定必须通过Mediator进行通信
###Mediator与Facade（外观模式）
Facade模式主要是为模块或系统定义一个较为简单的接口，而不会添加任何额外的功能，可以说是单向的。而Mediator则是一个多向的通信