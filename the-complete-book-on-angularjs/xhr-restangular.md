#同外界通信：XHR和服务器通信——Restangular

##Restangular简介
Restangular是一个专门用来从外部读取数据的AngularJS服务

* Restangular支持promise模式的异步调用
* Restangular支持所有的HTTP方法
* Restangular并不需要事先知道URL或提前指定它们（除基础URL外）
* Restangular可以直接处理嵌套的资源，无需创建新的Restangular实例
* 使用过程中仅需要创建一个Restangular资源对象的实例

##Restangular对象简介
> Restangular依赖Lo-Dash或Underscore，因此为了确保Restangular可以正常运行，需要引入这两个库中的一个

可以为拉取数据的对象设置基础路由，会让所有的HTTP请求将该路径作为根路径来拉取数据:

```javascript
var User = Restangular.all('users');
var allUsers = User.getList(); // GET /users
```

也可以通过单个对象来发送嵌套的请求：

```javascript
var oneUser = Restangular.one('users', 'abc123');
oneUser.get()// GET /users/abc123
    .then(function(user) {
        user.getList('inboxes'); // GET /users/abc123/inboxes
    });
```
> 还可以通过`allUrl()`和`oneUrl()`指定请求的URL


##使用Restangular
可以使用`getList()`来获取所有信息，`getList()`方法返回了一个集合，其中包含了可以用来操作特定集合的方法

Restangular返回增强过的promise对象，我们可以调用promise对象上的方法，还可以调用一些特殊的方法，比如`$object`。`$object`会立即返回一个空数组（或对象），在服务器返回信息后，数组会被用新的数据填充：

```javascript
messages.post(newMessage).then(function(newMsg) {
    $scope.messages = messages.getList().$object;
}, function(errorReason)
    // 出现了一个错误
});
```

> 注意，在修改一个对象之前对其进行复制，然后对复制的对象进行修改和保存是一个好做法。Restangular有自己的复制版本，因此无需对一系列方法重新进行绑定。在更新对象时使用Restangular.copy()是一个比较好的实践。

###支持所有HTTP方法

Restangular支持所有的HTTP方法：

* GET: `.get()`和`.getList('books')`
* PUT: `.put()`
* POST: `.post()`
* DELETE: `.remove()`
* HEAD: `.head()`
* TRACE: `.trace()`
* OPTIONS: `.options()`
* PATCH: `.patch()`

###自定义查询参数和头
可以传入参数和头对象:

```javascript
var queryParamObj = {
        role: 'admin'
    },
    headerObj = {
        'x-user': 'admin'
    };
messages.getList('accounts', queryParamObj, headerObj);
```


##设置Restangular
将RestangularProvider注入到`.config()`函数中，或者将Restangular注入到一个`.run()`函数中，对Restangular进行设置


###设置baseUrl
通过`.setBaseUrl()`方法给所有后端 API 请求设置 baseUrl

```javascript
angular.module('myApp', ['restangular'])
    .config(function(RestangularProvider) {
        RestangularProvider.setBaseUrl('/api/v1');
    });
```

###添加元素转换
使用`elementTransformers`可以在Restangular对象被加载后为其添加自定义方法

```javascript
angular.module('myApp', ['restangular'])
    .config(function(RestangularProvider) {
        RestangularProvider.addElementTransformer('authors', false, function(element) {
            element.fetchedAt = new Date();
            return element;
        });
    });
```

还可以对已有数据模型或集合进行扩展：

```javascript
angular.module('myApp', ['restangular'])
    .config(function(RestangularProvider) {
        RestangularProvider.extendModel('authors', function(element) {
            element.getFullName = function() {
                return element.name + ' ' + element.lastName;
            };
            return element;
        });
    });
```

###设置响应拦截器
Restangular可以设置响应拦截器。`responseInterceptors`在每个响应从服务器返回时被调用。调用时会传入以下参数：

1. data：从服务器取回的数据。
2. operation：使用的HTTP方法。
3. what：所请求的数据模型。
4. url：请求的相对URL。
5. response：完整的服务器响应，包括响应头。
6. deferred：请求的promise对象。

```javascript
angular.module('myApp', ['restangular'])
    .config(function(RestangularProvider) {
        RestangularProvider.setResponseInterceptor(function(data, operation, what) {
            //getList方法应当始终返回数组
            if (operation == 'getList') {
                var list = data[what];
                list.metadata = data.metadata;
                return list;
            }
            return data;
        });
    });
```

> getList方法应当始终返回数组

###设置请求拦截器
`requestInterceptors`在将数据实际发送给服务器之前对其进行操作。使用`setRequestInterceptor()`来设置requestInterceptor。这个方法可以接受下面四个参数:

1. element: 发送给服务器的资源 
2. operation: 所使用的HTTP方法
3. what: 所请求的数据模型
4. url: 所请求的相对URL

```javascript
angular.module('myApp', ['restangular'])
    .config(function(RestangularProvider) {
        RestangularProvider.setRequestInterceptor(function(elem, operation, what) {
            if (operation === 'put') {
                elem._id = undefined;
                return elem;
            }
            return elem;
        });
    });
```

###自定义字段

Restangular支持自定义字段，可以通过`.setRestangularFields()`方法来自定义：

```javascript
angular.module('myApp', ['restangular'])
    .config(function(RestangularProvider) {
        RestangularProvider.setRestangularFields({
            id: '_id.$oid'
        });
    });
```

###通过errorInterceptors来捕获错误
通过设置错误拦截器可以捕获Restangular内的错误。通过`errorInterceptor`可以将错误信息在应用中进行传递

```javascript
angular.module('myApp', ['restangular'])
    .config(function(RestangularProvider) {
        RestangularProvider.setErrorInterceptor(function(resp) {
            displayError();
            return false; // 停止promise链
        });
    });
```

###孤立资源设置
如果我们想加载一个没有嵌套在其他资源中的资源，可以使用`.setParentless()`设置告诉Restangular不要构造嵌套结构的URL

```javascript
angular.module('myApp', ['restangular'])
    .config(function(RestangularProvider) {
        RestangularProvider.setParentless(['cars']);
    });
```

`.setParentless()`接受两种类型的参数：

* true：所有的资源都会被当作孤立资源处理，没有任何URL会进行嵌套
* 数组：只有定义在这个数组中的资源会当作孤立资源处理，数组的元素是字符串，字符串的值是资源的标识

###使用超媒体
在实践中，只通过一个切入点（主URL）来同后端服务器进行通信是非常好的做法，其他数据模型通过链接来指向相关联的资源。 Restangular通过`selfLink`、`oneUrl`和`allUrl`来支持这个有用的做法。

###自定义Restangular服务
强烈建议将Restangular封装在一个自定义服务对象内
