Factory——工厂模式
===
Factory是一种创建型模式，它不显式地要求使用一个构造函数。而Factory可以提供一个通用的接口来创建对象，我们只需要告诉工厂需要创建的对象类型

##栗子
比如有水平菜单和垂直菜单的构造函数，分别为HorizontalMenu和VerticalMenu。我们可以定义一个菜单工厂MenuFactory来进行菜单创建：
```javascript
//水平菜单构造函数
function HorizontalMenu(options) {
	this.color = options.color || "black";
	this.items = options.items || [];
	this.curItem = options.curItem || "";
}
//垂直菜单构造函数
function VerticalMenu(options) {
	this.color = options.color || "black";
	this.items = options.items || [];
	this.curItem = options.curItem || "";
}
//菜单工厂
function MenuFactory() {}
//确定默认情况下创造的菜单类型
MenuFactory.prototype.menuClass = VerticalMenu;
//工厂创建菜单的方法
MenuFactory.prototype.createMenu = function(options) {
	if (options.type === "vertical") {
		this.menuClass = VerticalMenu;
	} else {
		this.menuClass = HorizontalMenu;
	}
	return new this.menuClass(options);
};
```

可以手动创造两个菜单对象试试：

```javascript
//创建工厂实例
var menuFactory = new MenuFactory();
//用工厂方法创建垂直菜单
var menu = menuFactory.createMenu({
	type: "vertical",
	color: "blue",
	items: ["主页", "文章", "玩意", "其他"],
	curItem: "主页"
});
//输出
console.log(menu);
console.log(menu instanceof HorizontalMenu);
console.log(menu instanceof VerticalMenu);
//用工厂方法创建水平菜单
var menu2 = menuFactory.createMenu({
	type: "horizontal",
	color: "blue",
	items: ["主页", "文章", "玩意", "其他"],
	curItem: "主页"
});
//输出
console.log(menu2);
console.log(menu2 instanceof HorizontalMenu);
console.log(menu2 instanceof VerticalMenu);
```

可以看到输出结果：

![工厂创建对象成功](http://skyinlayerblog.qiniudn.com/img/gitbook/jsDesignPatterns/8.png)

而工厂方法最大的好处就是提供了通用接口创建实例，比如新增一个动画菜单AnimationMenu类如下：
```javascript
function AnimationMenu(options) {
	this.color = options.color || "black";
	this.items = options.items || [];
	this.curItem = options.curItem || "";
}
```

那么只需要在工厂方法中添加一个`if`就行了:
```javascript
//工厂创建菜单的方法
MenuFactory.prototype.createMenu = function(options) {
	if (options.type === "vertical") {
		this.menuClass = VerticalMenu;
	} else if (options.type === "animation"){
		this.menuClass = AnimationMenu;
	} else {
	    this.menuClass = HorizontalMenu;
	}
	return new this.menuClass(options);
};
```

##何时使用Factory
Factory模式在如下场景时特别有用：
- 当对象或组件设置涉及高复杂性时
- 当需要根据所在的不同环境轻松生成对象的不同实例时
- 当处理很多共享相同属性的小型对象或组件时
- 在编写只满足一个API契约（亦称鸭子模型）的其他对象的实例对象时

这些情况下对解耦是很有帮助的。除非为创建对象提供一个接口是我们正在编写的库或框架的设计目标，否则不要使用Factory而显式使用构造函数