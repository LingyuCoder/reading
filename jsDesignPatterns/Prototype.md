Prototype——原型模式
===
四人帮称Prototype模式是一种基于现有对象模板，通过克隆方式创建对象的模式。而JS中可以认为，Prototype模式是基于原型继承的模式，可以在其中创建对象，作为其他对象的原型。

ECMASript5提供了一个`Object.create(prototype, optionalDescriptorObjects)`方法，用于创建一个对象，使其拥有指定的原型和可选属性：

```javascript
var parent = {
    name: "skyinlayer",
    sayHello: function() {
        return "hello, " + this.name;
    }
};

var child = Object.create(parent, {
    id: {
        value: 696,
        writable: false,
        configurable: false,
        enumerable: false
    },
    age: {
        value: 21,
        writable: true,
        configurable: true,
        enumerable: true
    }
});

console.log(child);
```

可以查看到对象结构如下：

![prototype的child对象结构](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/7.png)

这里第二个参数用于差异继承，具体用法和Object.defineProperties和Object.defineProperty相似

##Polyfill
由于`Object.create`方法在ES5中定义，在不支持的浏览器中需要polyfill，简单的不支持`optionalDescriptorObjects`如下：

```javascript
Object.prototype.create = function(parent){
    function F(){}
    F.prototype = parent;
    return new F();
};
```