#同外界通信：XHR和服务器通信——$resource

##使用$resource
$resource服务可以创建一个资源对象，我们可以用它非常方便地同支持RESTful的服务端数据源进行交互

ngResource模块是一个可选的AngularJS模块，需要安装并在应用中引用它

##应用$resource
$resource服务本身是一个创建资源对象的工厂，返回包含了几个默认动作的资源类对象。资源类对象本身包含的方法可以同后端服务进行简洁的交互

###基于HTTP GET方法
`get(params, successFn, errorFn)`方法向指定URL发送一个GET请求，并期望一个JSON类型的响应。`get()`请求通常被用来获取单个资源

`query(params, successFn, errorFn)`方法向指定URL发送一个GET请求，并期望返回一个JSON格式的资源对象集合。`query()`请求通常被用来获取多个资源，AngularJS期望它返回数组

###基于非HTTP GET类型的方法
`save(params, payload, successFn, errorFn)`方法向指定URL发送一个POST请求，并用数据体来生成请求体，用来在服务器上生成一个新的资源

`delete(params, payload, successFn, errorFn)`方法会向指定URL发送一个DELETE请求，并用数据体来生成请求体，用来在服务器上删除一个实例

`remove(params, payload, successFn, errorFn)`方法和delete()方法的作用是完全相同的，它存在的意义是因为delete是JavaScript的保留字，在IE浏览器中会导致额外的问题

###$resource实例
实例对象上会被添加下面三个实例方法：

* $save()
* $remove()
* $delete()

这三个方法在调用时$resource对象会立即返回一个空的数据引用。由于所有方法都是异步执行的，所以这个数据是一个空的引用，并不是真实的数据

###附加属性
有两个特殊的属性用来同底层的数据定义进行交互：

####$promise
$promise属性是为$resource生成的原始promise对象。

如果请求成功了，资源实例或集合对象会随promise的resolve一起返回

如果请求失败了，promise被resolve时会返回HTTP响应对象，其中没有resource属性。

####$resolved（布尔型）
在服务器首次响应时会被设置为true（无论请求是否成功）

##自定义$resource方法
可以用自定义方法对资源对象进行扩展。为了在$resource对象中创建自定义方法，需要向包含修改过的$http设置对象的资源类传入第三个参数，它被当作自定义方法。

```javascript
var User = $resource('/api/users/:userId.json', {
    userId: '@id'
    sendEmail: {
        method: 'POST'
    },
    allInboxes: {
        method: 'JSONP',
        isArray: true
    }
});
```

##$resource设置对象
$resource设置对象和$http设置对象十分相似，仅有少量的不同：

* method（字符串） 发送HTTP请求的方法。它必须是以下值之一：‘GET’、‘DELETE’、‘JSONP’、‘POST’、‘PUT’。
* url（字符串）： 一个URL，用来覆盖为该方法的具体路由设置的URL
* params（字符串map或对象）：同$http
* isArray（布尔型）：如果isArray被设置为true，那么这个动作返回的对象会以数组的形式返回
* transformRequest（函数或函数数组）：同$http
* transformResponse（函数或函数数组）：同$http
* cache（布尔型或缓存对象）：同$http
* timeout（数值型或promise对象）：同$http
* withCredentials（布尔型）：同$http
* responseType（字符串）：同$http
* interceptor（对象）：拦截器属性有两个可选的方法：response或responseError。这些拦截器像普通的$http拦截器一样，由$http请求对象调用。

##$resource服务
通过`$resource()`方法来使用$resource服务。这个方法可以接受三个参数：

1. url（字符串）：一个包含所有参数的，用来定位资源的参数化URL字符串模板（参数以`:`符号为前缀）
2. paramDefaults（可选，对象）：包含了发送请求时URL中参数的默认值
3. actions（可选，对象）：动作对象是具有自定义动作，并且可以对默认的资源动作进行扩展的hash对象

```javascript
$resource('/api/users/:id.:format', {
    format: 'json',
    id: '123'
}, {
    update: {
        method: 'PUT'
    }
});
```