#依赖注入
依赖注入会事先自动查找依赖关系，并将注入目标告知被依赖的资源，这样就可以在目标需要时立即将资源注入进去

AngularJS使用`$injector`（注入器服务）来管理依赖关系的查询和实例化

$injetor负责实例化AngularJS中所有的组件，包括应用的模块、指令和控制器等


##推断式注入声明
如果没有明确的声明，AngularJS会假定参数名称就是依赖的名称

> 调用函数对象的toString()方法，分析并提取出函数参数列表，然后通过$injector将这些参数注入进对象实例，这也是不能直接使用JavaScript Minify的原因

##显式注入声明
可以通过$inject属性来实现显式注入声明的功能。函数对象的$inject属性是一个数组，数组元素的类型是字符串，它们的值就是需要被注入的服务的名称。

```javascript
var injector = angular.injector(['ng', 'myApp']),
    controller = injector.get('$controller'),
    rootScope = injector.get('$rootScope');
```

##行内注入声明
可以通过$inject属性来实现显式注入声明的功能。函数对象的$inject属性是一个数组，数组元素的类型是字符串，它们的值就是需要被注入的服务的名称。

行内声明的方式允许我们直接传入一个参数数组而不是一个函数。数组的元素是字符串，它们代表的是可以被注入到对象中的依赖的名字，最后一个参数就是依赖注入的目标函数对象本身

```javascript
angular.module('myApp')
    .controller('MyController', ['$scope', 'greeter', function($scope, greeter) {}]);
```

##$injector API

###annotate()
`annotate()`方法的返回值是一个由服务名称组成的数组，这些服务会在实例化时被注入到目标函数中。

可以帮助`$injector`判断哪些服务会在函数被调用时注入进去

###get()
`get()`方法返回一个服务的实例

###has()
`has()`方法返回一个布尔值，在`$injector`能够从自己的注册列表中找到对应的服务时返回true，否则返回false


###instantiate()
`instantiate()`方法可以创建某个JavaScript类型的实例。它会通过`new`操作符调用构造函数，并将所有参数都传递给构造函数

###invoke()
`invoke()`方法会调用方法并从`$injector`中添加方法参数

##ngMin
ngMin是一个为AngularJS应用设计的预压缩工具，它会遍历整个AngularJS应用并帮助我们设置好依赖注入。使用ngMin这个工具，能够减少我们定义依赖关系所需的工作量。

通过`npm install -g ngmin`安装和使用

> ngMin使用AST遍历源代码，借助astral的AST工具框架的帮助，将必要的声明代码添加进源文件，并用escodegen将转换后的源文件输出。