Mixin——混入模式
===
##子类化
子类化是指针对一个新对象，从一个基础或超类对象中继承相关的属性。比如B继承A，那么B是A的子类，A是B的超类（父类）。所以B的所有实例从A处继承了相关方法，但B仍然能够定义自己的方法，以及重写A所定义的方法。

JavaScript中的一种实现方式如下：
```javascript
var Person = function(name, age, gender) {
	this.name = name;
	this.age = age;
	this.gender = gender;
};

var Student = function(name, age, gender, id, score) {
	Person.call(this, name, age, gender);
	this.id = id;
	this.score = score;
};
Student.prototype = Object.create(Person.prototype);

var student = new Student("天镶", "23", "男", 696, 60);
console.log(student);
```

这里超类为Person类，子类为Student类，在Student类实例化时，首先会调用超类构造函数，将超类的属性写入到子类中，而超类的原型也存在于子类的原型链上，超类原型上的方法也将被子类继承。可以看到输入结果如下：

![混入创建对象成功](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/9.png)

##Mixin——混入
在JavaScript中，我们可以将继承Mixin看做一种通过扩展收集功能的方式。我们可以通过向构造函数的prototype中扩展mixin对象来实现：

```javascript
//实现扩展方法
function extend(obj, mixin) {
	for (var item in mixin) {
		obj[item] = mixin[item];
	}
}
//创建需要扩展的功能集合
var myMixin = {
	sayHello: function() {
		console.log(this.name + " says: Hello!");
	},
	sayBye: function() {
		console.log(this.name + " says: Bye bye!");
	}
};
//将扩展应用到构造函数prototype中
extend(Student.prototype, myMixin);

var student = new Student("天镶", "23", "男", 696, 60);
//新建的实例拥有扩展方法了
student.sayHello();//天镶 says: Hello! 
student.sayBye();//天镶 says: Bye bye!
```

##优点和缺点
Mixin有助于减少系统中的重复功能及增加函数复用，但它会带来原型污染和函数起源方面的不确定性