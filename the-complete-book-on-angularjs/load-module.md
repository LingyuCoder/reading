#AngularJS模块加载
##配置
在模块的加载阶段，AngularJS会在提供者注册和配置的过程中对模块进行配置。

这个阶段是唯一能够在应用启动前进行修改的部分

```javascript
angular.module('myApp', [])
    .config(function($provide) {

    });
```

`.factory()`、`.directive()`等都是`.config()`的语法糖，如下面这段代码：

```javascript
angular.module('myApp', [])
    .factory('myFactory', function() {
        var service = {};
        return service;
    })
    .directive('myDirective', function() {
        return {
            template: '<button>Click me</button>'
        }
    });
```

等同于：

```javascript
angular.module('myApp', [])
    .config(function($provide, $compileProvider) {
        $provide.factory('myFactory', function() {
            var service = {};
            return service;
        });
        $compileProvider.directive('myDirective', function() {
            return {
                template: '<button>Click me</button>'
            };
        });
    });
```

> 唯一例外的是constant()方法，这个方法总会在所有配置块之前被执行

只有少数几种类型的对象可以被注入到`config()`函数中：提供者和常量。只能注入用`provider()`语法构建的服务，其他的则不行。

`config()`函数接受一个参数-configFunction: （函数）：AngularJS在模块加载时会执行这个函数

##运行块
运行块在注入器创建之后被执行，它是所有AngularJS应用中第一个被执行的方法。运行块通常用来注册全局的事件监听器。这就是`run()`方法

`run()`接受个参数- initializeFn（函数）: AngularJS在注入器创建后会执行这个函数。


