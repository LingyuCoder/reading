Facade——外观模式
===
Facade模式是一种结构型模式，它为更大的代码体提供了一个方便的高层次接口，能够隐藏其底层的真实复杂性。可以想象成是简化API来展示给其他开发人员，来提高可用性


##实现
这种模式在用于屏蔽浏览器差异的时候经常会出现，比如绑定事件：
```javascript
function bindEvent(ele, event, callback) {
    if (ele.attachEvent) {
        ele.attachEvent("on" + event, function(event) {
            event = event || window.event;
            event.target = event.target || event.srcElement;
            callback(event);
        });
    } else {
        ele.addEventListener(event, callback, false);
    }
};
```
这样暴露给开发者的就是bindEvent方法，上层开发者只需要去使用这个方法来完成事件绑定，而不用关注浏览器兼容

##优点和缺点
Facade模式能够简化类和模块的接口，也能从这个类或模块从使用它的代码解耦。而且Facade易于使用，且占用空间小。

但Facade也有一些缺点，它可能会影响到性能，在抽象时需要考虑是否包含隐性成本