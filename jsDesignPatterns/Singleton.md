Singleton——单例模式
===
Singleton是一种相当出名的模式，笔试面试时面试官特别喜欢让实现一个单例模式。Singleton限制了类只能有一个实例。经典Singleton模式的实现方式是，如果实例不存在，通过一个方法创建一个实例。如果已经存在，则返回实例的引用。Singleton与静态类（对象）不同的是，它可以被延迟生成，只有在需要的时候才会生成实例：
```javascript
var singleton = (function(){
    var instance;
    function init() {
        //define private methods and properties
        //do something
        return {
            //define public methods and properties
        };
    }
    return {
        getInstance: function() {
            if (!instance) {
                instance = init();
            }
            return instance;
        }
    };
}());
```