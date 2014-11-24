Constructor——构造器模式
===
在传统面向对象语言中，Constructor是一种在内存已分配给该对象的情况下，用于初始化新创建对象的特殊方法

而在JavaScript中，当我们定义一个构造函数，然后通过new去调用构造函数创建对象时，就已经在使用Constructor模式了

```javascript
function Student(name, age, id){
    this.name = name;
    this.age = age;
    this.id = id;
}
Student.prototype.getId = function(){
    return this.id;
};
var student = new Student("skyinlayer", 21, 696);
```

这就是典型的带原型的构造器，可以使构造函数所创建出来的实例共用getId方法