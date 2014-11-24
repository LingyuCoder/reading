Decorator——装饰者模式
===
Decorator是一种结构型模式，旨在促进代码复用，它和Mixin相似，同样可以被认为是一个可行的对象子类化的替代方案。Decorator提供了将行为动态添加至系统现有类的的能力

##实现
比如有个Student类，我们需要获得一个学生的一年的总花费。而一个学生这一年可能需要：学费（必交）、住宿舍交宿舍费、买书交书费、交女朋友。那么它的总花费应该是这些的和，但考虑有些人没有女朋友（比如我），那么花费计算就不一样了，可以像如下方式进行定义：
```javascript
//定义学生类
function Student(name) {
    this.name = name;
    this.getName = function() {
        return this.name;
    };
    this.getCost = function() {
        return 5500;//学费5500，必交
    };
}
//定义装饰器，需要宿舍，宿舍费550/年
function NeedDormitory(student) {
    var cost = student.getCost();
    student.getCost = function() {
        return cost + 550;
    };
}
//定义装饰器，需要书本，书本费400/年
function NeedBooks(student) {
    var cost = student.getCost();
    student.getCost = function() {
        return cost + 400;
    };
}
//定义装饰器，有女朋友
function NeedGirlfriend(student) {
    var cost = student.getCost();
    student.getCost = function() {
        return cost + 99999;
    };
}
```

这样就定义好了三个可选的装饰器，分别表示需要宿舍，需要书本，有女朋友。那么创建两个学生对象：
```javascript
var gaofushuai = new Student("高富帅");
NeedDormitory(gaofushuai);
NeedBooks(gaofushuai);
NeedGirlfriend(gaofushuai);
console.log(gaofushuai.getName() + " 每年需要金钱：" + gaofushuai.getCost());

var skyinlayer = new Student("天镶");
NeedDormitory(skyinlayer);
NeedBooks(skyinlayer);
console.log(skyinlayer.getName() + " 每年需要金钱：" + skyinlayer.getCost());
```

具体的输出如下所示：

![高富帅需要钱16449，天镶要钱6450](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/10.png)

这样就实现了装饰者模式了

##JavaScript实现接口
Decorator模式也可以被描述为一种用于在相同接口的其他对象内部透明的包装对象的模式。接口应该是对象定义方法的一种方式，但是它实际上并不直接指定如何实现这些方法。在JavaScript中使用接口，可以促进可复用性。理论上通过确保实现类保持和接口相同的改变，接口可以使代码变得更加稳定
###实现接口
接口实现思路很简单，接口的目的就是保证对象拥有特定的方法。首先我们需要一个接口类，用于存放接口的名字和使用这个接口的对象必须实现的方法名称：

```javascript
//定义接口类
var Interface = function(name, methods) {
    var i;
    this.name = name;
    this.methods = [];
    for (i = methods.length; i--;) {
        if (typeof methods[i] !== 'string') {
            throw Error("接口定义错误：接口的方法名称必须是字符串");
        }
        this.methods.push(methods[i]);
    }
};
```

然后，需要一个静态方法，用来确定某个对象是否实现了其参数内接口的所有方法：
```javascript
//第一个参数为检测对象，后面的参数为接口对象
Interface.checkImplements = function(object) {
    var interfaces = Array.prototype.slice.call(arguments, 1);
    var methods;
    var methodName;
    var i;
    var j;
    for (i = interfaces.length; i--;) {
        if (interfaces[i].constructor !== Interface) {
            throw Error("接口定义错误：必须传递Interface类定义的接口");
        }
        methods = interfaces[i].methods;
        for (j = methods.length; j--;) {
            methodName = methods[j];
            if (!object[methodName] || typeof object[methodName] !== 'function') {
                throw Error("接口定义错误：未实现 " + interfaces[i].name + " 接口的 " + methodName + "方法");
            }
        }
    }
};
```
###试验一下
可以试一试，首先定义一个Student接口，这个接口需要两个方法，getName和getCost：
```javascript
var Student = new Interface("Student", ["getName", "getCost"]);
```

然后是这个Student接口的实现，叫它StudentImpl吧：
```javascript
function StudentImpl(name) {
    Interface.checkImplements(this, Student);
    this.name = name;
}

StudentImpl.prototype.getName = function() {
    return this.name;
};
```

这里只定义了getName方法，缺少了getCost方法，于是乎在创建实例的时候将会报错：
```javascript
console.log(new StudentImpl("天镶"));
//Uncaught Error: 接口定义错误：未实现 Student 接口的 getCost方法 
```

补上getCost方法，再试：
```javascript
StudentImpl.prototype.getCost = function() {
    return 5500;
};
console.log(new StudentImpl("天镶"));
//StudentImpl {name: "天镶", getName: function, getCost: function}
```

对象创建成功
###接口的问题
但JavaScript并没有为接口提供内置的支持，所以模仿并实现是有一定风险的