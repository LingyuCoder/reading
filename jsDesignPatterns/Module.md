Module——模块模式
===
模块能够帮助我们清晰地分离和组织项目中的代码单元，JavaScript中，有很多种模块实现：
- Module模式
- AMD模块
- CMD模块

Module模式最初被定义为一种在传统软件工程中为类提供私有和公有封装的方法。JS中，使用模块模式，能够是一个单独的对象拥有公有/私有放发和变量，从而屏蔽来自全局作用域的特殊部分。Module模式使用闭包实现，只需返回一个公有API，模块内部的一切维持在私有闭包里

##创建模块
```javascript
var someModule = (function(){
    var name = "skyinlayer";
    return {
        changeName: function(newName){
            name = newName;
        },
        say: function(){
            console.log(name + " said that JavaScript is the best language!");
        }
    };
})();

someModule.say();
someModule.changeName("Teacher");
someModule.say();
```
通过这种立即执行函数创造作用域，就可以实现的Module模式。模块内部的私有变量name对外界是不可见的，只能通过say方法和changeName方法对其进行操作

##模块的引入和引出
Module模式可以通过给匿名函数传递参数的方式引入其他模块，并给这些模块更换名字：
```javascript
var someModule = (function($, angular){
    //do something
    return {
        //define module API here
    };
}(jQuery, angular));
```

而引出模块则通过将定义模块的匿名函数的返回值赋给全局变量someModule，来实现模块引出

##Dojo的Module实现
Dojo提供了一个`dojo.setObject()`方法，用于创建模块。其第一个参数用点号分割的字符串，如`"myModule.parent.child"`,它在parent对象中引用一个称为child的属性。如果中间对象不存在，则会通过点号分割将中间的字符作为中间对象进行创建

```javascript
require(["dojo/_base/baseModule"], function(baseModule){
    baseModule.setObject("childModule.core", (function(){
        //do something
        return {
            //define module API here
        };
    }()));
});
```

##ExtJS的Module实现
ExtJS则是通过创建命名空间，然后对其填充对象来实现Module的，与纯JavaScript实现Module很相近：

```javascript
Ext.namespace("moduleNameSpace");

moduleNameSpace.app = (function(){
    //do something
    return {
        //define module API here
    };
}());
```

##YUI的Module实现
YUI的Module实现与纯JavaScript截然不同

```javascript
Y.namespace("baseModule.childModule") = (function(){
    //do something
    return {
        //define module API here
        //如果想要访问baseModule中的变量，需要通过this.propertyName来访问
    };
}());
```

##jQuery的Module实现
有很多种方式能够实现jQuery的Module：
```javascript
function library(module) {
    $(function() {
        if (module.init) {
            module.init();
        }
    });
    return module;
}
var myLib = library(function() {
    return {
        init: function(){
            //模块实现
        }
    };
});
```

##Module模式的优缺点
Module模式比较显著的优点就是符合封装的思想，保持私有数据而只公开公有API，实现模块开发。而缺点则是，由于我们访问公有和私有成员的方式不同，当我们想要改变成员的可见性时，需要修改每一个使用过该成员的地方。同时我们无法访问那些之后在方法里添加的私有成员。