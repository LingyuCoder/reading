Abstract Decorator——抽象装饰者
===
事实上，我们可以为将每个装饰者实现成一个装饰者类

##定义原始类
首先我们定义一个接口，里面包含了必须实现的方法：
```javascript
var StudentInterface = new Interface("StudentInterface", ["getCost", "getName"]);
```

然后根据这个接口，定义一个需要装饰的原始类：
```javascript
var Student = function(name) {
    Interface.checkImplements(this, StudentInterface);
    this.name = name;
};

Student.prototype.getName = function() {
    return this.name;
};

Student.prototype.getCost = function() {
    return 5500;
};
```
##定义装饰器基类
然后定义一个装饰器基类，所有的装饰器都继承这个装饰器基类，它也需要实现这些上面的接口，但由于不需要做修改，这些接口直接调用原始类的方法就好：
```javascript
var StudentDecorator = function(student) {
    Interface.checkImplements(student, StudentInterface);
    this.student = student;
};

StudentDecorator.prototype.getName = function() {
    return this.student.getName();
};

StudentDecorator.prototype.getCost = function() {
    return this.student.getCost();
};
```
##创建装饰器
接着就可以定义继承装饰器基类的装饰器了，比如定义一个学生需要宿舍，那么装饰器应该这么定义：
```javascript
//宿舍装饰器
var DormitoryDecorator = function(student) {
    StudentDecorator.call(this, student);
};

extend(DormitoryDecorator.prototype, StudentDecorator.prototype);

DormitoryDecorator.prototype.getCost = function() {
    return this.student.getCost() + 550;
};
```

这里通过extend和调用装饰器基类的构造函数来将基类的属性和方法复制给装饰器类，同理构建两个新的装饰器，书本装饰器和把妹装饰器：
```javascript
//书本装饰器
var BooksDecorator = function(student) {
    StudentDecorator.call(this, student);
};

extend(BooksDecorator.prototype, StudentDecorator.prototype);

BooksDecorator.prototype.getCost = function() {
    return this.student.getCost() + 400;
};

//把妹装饰器
var GirlfriendDecorator = function(student) {
    StudentDecorator.call(this, student);
};

extend(GirlfriendDecorator.prototype, StudentDecorator.prototype);

GirlfriendDecorator.prototype.getCost = function() {
    return this.student.getCost() + 99999;
};
```

这样对装饰器的定义就完成了

##例子
```javascript
var student = new Student("天镶");
console.log(student.getCost());
student = new DormitoryDecorator(student);
console.log(student.getCost());
student = new GirlfriendDecorator(student);
console.log(student.getCost());
student = new BooksDecorator(student);
console.log(student.getCost());
```

查看输出结果：

![输出天镶需要5500,6050，16049,16449](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/11.png)

如果查看student对象，可以看到它是如下结构的：

![student对象结构](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/12.png)

这就是抽象装饰器模式了。抽象装饰者模式可以动态的修改对象，因此更加完美，但内存消耗也要大得多