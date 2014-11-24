Abstract Factory——抽象工厂
===
Abstract Factory用于封装一组具有共同目标的单个工厂。它能够将一组对象的实现细节从一般用法中分离出来。当一个系统必须独立于它所创建的对象的生成方式，或它需要与多种对象类型一起工作时，应当使用抽象工厂

##实现
以之前的菜单工厂为例，这里定义一个抽象菜单工厂：
```javascript
var AbstractMenuFactory = (function() {
    var constructors = {};
    //根据type创建实例
    function createMenu(type, options) {
        var menuConstructor = constructors[type];
        return menuConstructor ? new menuConstructor(options) : null;
    }
    //在抽象工厂中注册新菜单类型
    function registerMenu(type, constructor) {
        constructors[type] = constructor;
        return this;
    }
    return {
        createMenu: createMenu,
        registerMenu: registerMenu
    };
}());
```

还是之前的三个构造函数，首先将他们注册到抽象工厂中：
```javascript
AbstractMenuFactory
    .registerMenu("vertical", VerticalMenu)
    .registerMenu("horizontal", HorizontalMenu)
    .registerMenu("animation", AnimationMenu);
```

然后就可以使用抽象工厂创建实例了：
```javascript
var options = {
    color: "blue",
    items: ["主页", "文章", "玩意", "其他"],
    curItem: "主页"
};

var menu1 = AbstractMenuFactory.createMenu("vertical", options);
console.log(menu1 instanceof VerticalMenu);//true
var menu2 = AbstractMenuFactory.createMenu("horizontal", options);
console.log(menu2 instanceof HorizontalMenu);//true
var menu3 = AbstractMenuFactory.createMenu("animation", options);
console.log(menu3 instanceof AnimationMenu);//true
```