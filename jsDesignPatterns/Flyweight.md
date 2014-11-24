Flyweight——享元模式
===
享元模式是一种结构型解决方案，用于优化重复、缓慢及数据共享效率低的代码。它旨在通过与相关对象共享尽可能多的数据来减少应用程序中的内存使用。享元模式的应用有两种，第一种是用户数据层，处理内存中保存的大量相似对象的共享数据。第二种是用于DOM层，享元可以作为中央事件管理器，来避免将事件处理程序附加到父容器中的每个子元素上，而是将事件处理程序附加到这个父容器上

##享元模式中的组件
享元模式中，需要利用三种类型的组件：
1. Flyweight（享元）：描述一个接口，通过这个接口flyweight可以接受并作用于外部状态
2. Concrete Flyweight（具体享元）：实现Flyweight接口，并存储内部状态。Concrete Flyweight对象必须是可共享的，并能够控制外部状态
3. Flyweight factory（享元工厂）：创建并管理Flyweight对象，确保合理的共享Flyweight，并将它们当做一组对象进行管理，并且如果我们需要单个实例时，可以查询这些对象。如果该对象已经存在，则直接返回，否则创建新对象并返回

##实现
考虑如下场景：

实验室有很多同学，每天要打卡签到上班，如果把每条签到记录都当做一个对象，那么同一个学生的不同天的签到记录都是一个新对象。但其中学生信息数据完全可以脱离出来，成为享元。我们可以创建一个学生工厂用于管理这个享元：

```javascript
//定义学生接口
var StudentInterface = new Interface("StudentInterface", ["sayHello", "work", "sayBye"]);
//创建学生类
function Student(name, id) {
    Interface.checkImplements(this, StudentInterface);
    this.name = name;
    this.id = id;
}
//实现学生接口中的方法
Student.prototype.work = function() {
    console.log(this.name + "正在干活...");
};

Student.prototype.sayHello = function() {
    console.log(this.name + "来到实验室...");
};

Student.prototype.sayBye = function() {
    console.log(this.name + "离开实验室...");
};
//创建学生工厂
function StudentFactory() {
    var students = {};

    function createStudent(id, name) {
        if (!students[id]) {
            students[id] = new Student(name, id);
        }
        return students[id];
    }
    return {
        createStudent: createStudent
    };
}
```

于是乎,我们的签到管理系统,可以用如下方式实现:

```javascript
var StudentManager = (function() {
    //签到数据库
    var attendance = {};
    //学生工厂
    var studentFactory = new StudentFactory();
    //签到
    function signIn(id, name) {
        var student = studentFactory.createStudent(id, name);
        attendance[id] = attendance[id] || [];
        attendance[id].push({
            student: student,
            signInTime: new Date(),
            state: "signIn"
        });
        student.sayHello();
    }
    //干活
    function work(id, name) {
        var attend = attendance[id];
        for (var i = attend.length; i--;) {
            if (attend[i].state === "signIn") {
                attend[i].student.work();
                attend[i].state = "worked";
            }
        }
    }
    //签退
    function signOut(id, name) {
        var attend = attendance[id];
        for (var i = attend.length; i--;) {
            if (attend[i].state === "worked") {
                attend[i].state = "signOut";
                attend[i].signOutTime = new Date();
                attend[i].student.sayBye();
            }
        }

    }
    return {
        signIn: signIn,
        work: work,
        signOut: signOut
    };
}());
```

这样在每次签到的时候，会去学生工厂申请学生信息。学生工厂中如果没有这个学生的信息，将创建，否则直接返回学生信息的引用。所有同一学生的出勤公用一个学生信息对象

##Flyweight与DOM
实际上事件代理也是使用享元模式，至于事件代理，之前的博客已经讲了太多，这里就不赘述了