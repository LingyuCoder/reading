Observer——观察者模式
===

Observer中，一个对象（subject）维持一系列依赖于它（观察者）的对象，将有关状态的任何变更自动通知给它们。当不再希望某个特定的观察者获得其注册目标发出的通知时，该目标可将它从观察者列表中删除。Observer包括如下组件：
- Subject（目标）：维护一系列的观察者，方便添加或删除观察者
- Observer（观察者）：为那些在目标状态发生改变时需获得通知的对象提供一个更新接口
- ConcreteSubject（具体目标）：状态发生改变时，向Observer发出通知，存储ConcreteObserver的状态
- ConcreteObserver（具体观察者）：存储一个指向ConcreteSubject的引用，实现Observer的更新接口，使其自身与目标的状态保持一致

![observer框架图](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/1.png)


##ObserverList的实现
每一个Subject应当维护一个观察者列表，用于添加、删除或通知观察它的所有观察者，要维护这个观察者列表，自然需要实现一些基础方法，比如CRUD操作等：
```javascript
//构造函数
function ObserverList() {
    this.observerList = [];
}
//向观察者列表中增加观察者对象
ObserverList.prototype.add = function(obj) {
    return this.observerList.push(obj);
};
//清空观察者列表
ObserverList.prototype.empty = function() {
    this.observerList = [];
};
//统计观察者列表中的观察者数量
ObserverList.prototype.count = function() {
    return this.observerList.length;
};
//根据序号获取观察者对象
ObserverList.prototype.get = function(index) {
    if (index >= 0 && index < this.observerList.length) {
        return this.observerList[index];
    }
};
//获取某个观察者对象在列表中的序号
ObserverList.prototype.indexOf = function(obj) {
    return this.observerList.indexOf(obj);
};
//从观察者列表中移除某个观察者对象
ObserverList.prototype.remove = function(obj) {
    var index = this.get(obj);
    if (index != -1) {
        this.removeAt(index);
    }
};
//从观察者列表中移除某个特定序号的观察者对象
ObserverList.prototype.removeAt = function(index) {
    if (index >= 0 && index < this.observerList.length) {
        this.observerList.splice(index, 1);
    }
};
```

##Subject的实现
目标主要就是操作这个观察者列表，以及使用这个观察者列表通知观察者：
```javascript
//Subject构造函数
function Subject() {
    this.observers = new ObserverList();
}
//给这个Subject增加观察者
Subject.prototype.addObserver = function(observer) {
    this.observers.add(observer);
};
//从这个Subject移除观察者
Subject.prototype.removeObserver = function(observer) {
    this.observers.remove(observer);
};
//通知观察者列表中的所有观察者
Subject.prototype.notify = function(context) {
    var observerCount = this.observers.count();
    for (var i = 0; i < observerCount; i++) {
        this.observers.get(i).update(context);
    }
};
```

##Observer的实现
由于Subject中通知观察者需要调用观察者的update方法，所以Observer中只要实现了update方法就行了：
```javascript
function Observer() {
    this.update = function(context) {
        console.log(context);
    };
}
```

##实例
我们可以通过给DOM元素扩展的方式来将DOM节点构造成一个Subject对象，首先实现一个扩展方法：
```javascript
function extend(obj, extension) {
    for (var key in extension) {
        obj[key] = extension[key];
    }
}
```

比如这里有如下HTML：
```html
<input type="text" id="ipt"/>
<button id="btn">点击</button>
```

在点击按钮之后，会将input框中的内容传递给所有观察者。首先需要将按钮扩展成Subject对象，并在其点击事件触发时通知观察者，通知内容为input的内容：
```javascript
var $btn = document.getElementById("btn");
var $ipt = document.getElementById("ipt");

extend($btn, new Subject());

$btn.onclick = function(event){
    this.notify($ipt.value);
    event.stopPropagation();
};
```

这样按钮就是一个Subject对象了，我们可以新建几个观察者，并加入到这个Subject对象的观察者列表当中：
```javascript
var observer1 = new Observer();
observer1.update = function(context){
    console.log("observer1 is notified," + context);
};

var observer2 = new Observer();
observer2.update = function(context){
    console.log("observer2 is notified," + context);
};

$btn.addObserver(observer1);
$btn.addObserver(observer2);
```

我们在input框中输入内容后，点击按钮，两个观察者将会获得input框的内容：

![observer输出结果](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/2.png)