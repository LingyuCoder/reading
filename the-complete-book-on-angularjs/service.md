#服务
服务提供了一种能在应用的整个生命周期内保持数据的方法，它能够在控制器之间进行通信，并且能保证数据的一致性

**服务是一个单例对象**，在每个应用中只会被实例化一次（被`$injector`实例化），**并且是延迟加载的**（需要时才会被创建）。服务提供了把与特定功能相关联的方法集中在一起的接口。

##注册一个服务

使用`angular.module`的factory API创建服务是最常见也是最灵活的方式：

```javascript
angular.module('myApp', [])
    .factory('UserService', function($http) {
        var current_user;
        return {
            getCurrentUser: function() {
                return current_user;
            },
            setCurrentUser: function(user) {
                current_user = user;
            }
        };
    });
```

##使用服务
可以在控制器、指令、过滤器或另外一个服务中通过依赖声明的方式来使用服务

```javascript
app.controller('SomeController', function($scope, $timeout, myService) {});
```

> 在自定义服务之前注入所有的AngularJS内置服务，这是约定俗成的规则

除了使用服务将类似功能打包在一起，使用服务也是在控制器之间共享数据的典型方法


##创建服务时的设置项
`factory()`方法时用来注册服务的最常规的方式，但同时还有其他的API：

* factory()
* service()
* constant()
* value()
* provider()

###factory()
`factory()`接受两个参数：

1. name-字符串：服务名
2. getFn-函数：在创建服务实例时调用，由于服务是单例，只会被调用一次。在定义服务时也可以注入其他服务

```javascript
angular.module('myApp')
    .factory('githubService', ['$http', function($http) {//注入了$http服务
        return {
            getUserEvents: function(username) {
                // ...
            }
        };
    }]);
```

###service()
使用`service()`可以注册一个支持构造函数的服务，它允许我们为服务对象注册一个构造函数。 

`service()`方法接受两个参数：

1. name-字符串：服务名
2. constructor-函数：构造函数，我们调用它来实例化服务对象

`service()`函数会在创建实例时通过`new`关键字来实例化服务对象。

```javascript
var Person = function($http) {
    this.getName = function() {
        return $http({
            method: 'GET',
            url: '/api/user'
        });
    };
};
angular.service('personService', Person);
```

###provider()

所有服务工厂都是由$provide服务创建的，$provide服务负责在运行时初始化这些提供者

> 提供者是一个具有$get()方法的对象，$injector通过调用$get方法创建服务实例。

`所有创建服务的方法都构建在provider方法之上`。provider()方法负责在$providerCache中注册服务。下面两种创建服务的方法是等价的：

```javascript
angular.module('myApp')
    .factory('myService', function() {
        return {
            'username': 'auser'
        };
    })
    // 这与上面工厂的用法等价
    .provider('myService', {
        $get: function() {//具有$get方法
            return {
                'username': 'auser'
            };
        }
    });
```

**如果希望在config()函数中可以对服务进行配置，必须用provider()来定义服务。**

`provider()`以接受两个参数:

1. name-字符串：服务名
2. aProvider-对象/函数/数组：
    * 对象：应该带有$get方法
    * 函数：通过依赖注入被调用，并负责通过$get方法返回一个对象
    * 数组：带有行内依赖注入声明的函数处理，数组最后一个元素是函数，返回一个带有$get方法的对象


###constant()
`constant()`可以将一个已经存在的变量值注册为服务，并将其注入到应用的其他部分当中

`constant()`函数可以接受两个参数: 

1. name-字符串：需要注册的常量的名字
2. value-常量：需要注册的常量的值（值或者对象）

```javascript
angular.module('myApp') .constant('apiKey','123123123')
```

> 这个常量不能被装饰器拦截

###value()
如果服务的`$get`方法返回的是一个常量，那就没要必要定义一个包含复杂功能的完整服务，可以通过value()函数方便地注册服务

value()方法可以接受两个参数: 

1. name-字符串：同样是需要注册的服务名
2. value-值：将这个值将作为可以注入的实例返回

```javascript
angular.module('myApp').value('apiKey','123123123');
```

value()方法和constant()方法之间最主要的区别是：常量可以注入到配置函数`.config()`中，而值不行。 

通常情况下，可以通过value()来注册服务对象或函数，用constant()来配置数据

```javascript
angular.module('myApp', [])
    .constant('apiKey', '123123123')
    .config(function(apiKey) {
        // 在这里apiKey将被赋值为123123123
        // 就像上面设置的那样
    })
    .value('FBid', '231231231')
    .config(function(FBid) {
        // 这将抛出一个错误，未知的provider: FBid
        // 因为在config函数内部无法访问这个值
    });
```

###decorator()
`$provide`服务提供了在服务实例创建时对其进行拦截的功能，可以对服务进行扩展，或者用另外的内容完全代替它。

通过`$provide.decorator()`来添加装饰器，`decorator()`就是在服务上添加装饰器，来对服务进行拦截、中断甚至替换。

`decorator()`函数可以接受两个参数：

1. name-字符串：要拦截的服务名
2. decoratorFn-函数：在服务实例化时调用该函数，这个函数由injector.invoke调用，可以将服务注入这个函数中

`$delegate`是可以进行装饰的最原始的服务，为了装饰其他服务，需要将其注入进装饰器。

```javascript
var githubDecorator = function($delegate, $log) {
    var events = function(path) {
        var startedAt = new Date();
        var events = $delegate.events(path);
        // 事件是一个promise 
        events.finally(function() {
            $log.info("Fetching events" +
                " took " +
                (new Date() - startedAt) + "ms");
        });
        return events;
    };
    return {
        events: events
    };
};
angular.module('myApp')
    .config(function($provide) {
        $provide.decorator('githubService', githubDecorator);
    });
```