#同外界通信：XHR和服务器通信——$http

##使用$http
可以使用内置的$http服务直接同外部进行通信。$http服务只是简单的封装了浏览器原生的XMLHttpRequest对象。

$http服务返回一个promise对象，具有`success`和`error`两个方法。如果响应状态码在200和299之间，会认为响应成功，调用success回调，否则调用error回调

```javascript
$http({
    method: 'GET',
    url: '/api/users.json'
}).success(function(data, status, headers, config) {
    // 当相应准备就绪时调用
}).error(function(data, status, headers, config) {
    // 当响应以错误状态返回时调用
});
```

###快捷方法
$http服务提供了一些顺手的快捷方法供我们使用，这些方法简化了复杂设置：

* get() 发送GET请求
* delete() 发送DELETE请求
* head() 发送HEAD请求
* jsonp() 发送JSONP请求
* post() 发送POST请求
* put() 发送PUT请求

##设置对象
将$http当作函数来调用时，需要传入一个设置对象，用来说明如何构造XHR对象:


* method（字符串）：发送请求的HTTP方法
* url（字符串）：绝对或相对的URL，请求的目标。
* params（字符串map或对象）：一个字符串map或对象，会被转换成查询字符串追加在URL后面。如果值不是字符串，会被JSON序列化
* data（字符串或对象）：包含了将会被当作消息体发送给服务器的数据
* headers（对象）：一个列表，每一个元素都是一个函数，它会返回代表随请求发送的HTTP头。如果函数的返回值是null，对应的头不会被发送。
* xsrfHeaderName（字符串）：保存XSFR令牌的HTTP头的名称
* xsrfCookieName（字符串）：保存XSFR令牌的cookie的名称。
* transformRequest（函数或函数数组）：这是一个函数或函数数组，用来对HTTP请求的请求体和头信息进行转换，并返回转换后的版本。通常用于在请求发送给服务器之前对其进行序列化
* transformResponse（函数或函数数组）：这是一个函数或函数数组，用来对HTTP响应的响应体和头信息进行转换，并返回转换后的版本。通常用来进行反序列化。
* cache（布尔型或缓存对象）：
    * true：用默认的$http缓存来对GET请求进行缓存
    * $cacheFactory对象的实例：使用这个对象来对GET请求进行缓存
* timeout（数值型或promise对象）：
    * 数值：指定的毫秒数后再发送
    * promise对象：被resolve时请求会被中止
* withCredentials（布尔型）：如果被设置为true，那么XHR请求对象中会设置withCredentials标记。
* responseType（字符串）：在请求中设置XMLHttpRequestResponseType属性，可以是：
    * ""（字符串，默认）
    * "arraybuffer"（ArrayBuffer）
    * "blob"（blob对象）
    * "document"（HTTP文档）
    * "json"（从JSON对象解析而来的JSON字符串）
    * "text"（字符串）
    * "moz-blob"（Firefox的接收进度事件）
    * "moz-chunked-text"（文本流）
    * "moz-chunked-arraybuffer"（ArrayBuffer流）

> 默认情况下，CORS请求不会发送cookie，而withCredentials标记会在请求中加入`Access-Control-Allow-Credentials`头，这样请求就会将目标域的cookie包含在请求中

##响应对象
传给`then()`方法的响应对象包含：

1. data（字符串或对象）：转换过后的响应体（如果定义了转换的话）
2. status（数值型）：响应的HTTP状态码
3. headers（函数）：是头信息的getter函数，可以接受一个参数，用来获取对应名字的值
4. config（对象）：用来生成原始请求的完整设置对象
5. statusText（字符串）：响应的HTTP状态文本

##缓存HTTP请求
默认情况下，$http服务不会对请求进行本地缓存。在发送单独的请求时，我们可以通过向$http请求传入一个布尔值或者一个缓存实例来启用缓存

> 通过`cache: true`启用缓存，默认会使用`$cacheFactory`这个服务

###构建缓存对象
可以通过$cacheFactory来建立LRU的缓存：

```javascript
var lru = $cacheFactory('lru', {
    capacity: 20
});
// $http请求
$http.get('/api/users.json', {
        cache: lru
    })
    .success(function(data) {})
    .error(function(data) {});
```

###为所有请求设置缓存
通过$httpProvider能够在.config()中给所有$http请求设置一个默认的缓存：

```javascript
angular.module('myApp', [])
    .config(function($httpProvider, $cacheFactory) {
        $httpProvider.defaults.cache = $cacheFactory('lru', {
            capacity: 20
        });
    });
```

##拦截器
可以通过拦截器从全局层面对响应进行处理。拦截器的核心是服务工厂，通过向$httpProvider. interceptors数组中添加服务工厂，在$httpProvider中进行注册。

一共有四种拦截器：

* request：请求时调用
* response：响应时调用
* requestError：请求拦截器抛出错误或promise被reject时调用
* responseError：响应拦截器抛出错误或promise被rejiect时调用

通过`.factory()`创建拦截器，并在`.config()`中，使用`$httpProvider.interceptors.push()`添加拦截器

```javascript
angular.module('myApp', [])
    .factory('myInterceptor', function($q) {
        var interceptor = {
            'request': function(config) {
                return config;
            },
            'response': function(response) {
                return response;
            },
            'requestError': function(rejection) {
                return rejection;
                // return $q.reject(rejection);
            },
            'responseError': function(rejection) {
                return rejection;
                // return $q.reject(rejection);
            }
        };
        return interceptor;
    });
angular.module('myApp', [])
    .config(function($httpProvider) {
        $httpProvider.interceptors.push('myInterceptor');//添加拦截器
    });
```

##设置$httpProvider

使用`.config()`可以向所有请求中添加特定的HTTP头，默认的请求头保存在`$httpProvider.defaults.headers.common`对象中，可以对其修改或扩充：

```javascript
angular.module('myApp', [])
    .config(function($httpProvider) {
        $httpProvider.defaults.headers
            .common['X-Requested-By'] = 'MyAngularApp';
    });
```

在运行时通过$http对象的defaults属性也可以修改这些默认值：

```javascript
$http.defaults.common['X-Auth'] = 'RandomString';
```

如果仅需要对特定方法进行修改，如修改POST方法，可以这样：

```javascript
angular.module('myApp', [])
    .config(function($httpProvider) {
        $httpProvider.defaults.headers
            .post['X-Posted-By'] = 'MyAngularApp';
    });
//或者这样
angular.module('myApp', []).config(function($httpProvider) {
    $httpProvider.defaults.headers
        .post['X-Posted-By'] = 'MyAngularApp';
});
```