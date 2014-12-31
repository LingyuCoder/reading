#XHR实践

##跨域和同源策略
浏览器在全局层面禁止了页面加载或执行与自身来源不同的域的任何脚本。**同源策略**允许页面从同一个站点加载和执行特定的脚本。浏览器通过对比每一个资源的协议、主机名和端口号来判断资源是否与页面同源。站外其他来源的脚本同页面的交互则被严格限制。

**跨域资源共享**（Cross Origin Resource Sharing，CORS）是一个解决跨域问题的好方法，从而
可以使用XHR从不同的源加载数据和资源，此外还有JSONP和服务器代理等。

##JSONP
JSONP的原理是通过`<script>`标签发起一个GET请求来取代XHR请求。JSONP生成一个`<script>`标签并插到DOM中，然后浏览器会接管并向`src`属性所指向的地址发送请求。

当服务器返回请求时，响应结果会被包装成一个JavaScript函数，并由该请求所对应的回调函数调用。

```javascript
$http
    .jsonp("https://api.github.com?callback=JSON_CALLBACK").success(function(data) {
        // 数据
    });
```

> JSONP的后端服务要确保响应的数据被包含在请求所
指定的回调函数中

使用JSONP需要意识到潜在的安全风险：

1. 服务器会完全开放，允许后端服务调用应用中的任何JavaScript。
2. 外部站点（或者蓄意攻击者）可以随时更改脚本，使整个站点变得脆弱
3. 请求是由`<script>`标签发送的，所以只能通过JSONP发送GET请求

##使用CORS
CORS规范简单地扩展了标准的XHR对象，以允许JavaScript发送跨域的XHR请求。它会通过**预检查**（preflight）来确认是否有权限向目标服务器发送请求。

###设置
通过`.config()`在`$httpProvider`上，告诉AngularJS使用`XDomain`，并从所有的请求中把`X-Request-With`头移除掉

```javascript
angular.module('myApp', [])
    .config(function($httpProvider) {
        $httpProvider.defaults.useXDomain = true;
        delete $httpProvider.defaults.headers
            .common['X-Requested-With'];
    });
```

###服务器端CORS支持
支持CORS的服务器必须在响应中加入几个访问控制相关的头：

* `Access-Control-Allow-Origin`: 可以是与请求头的值相呼应的值，也可以是`*`，从而允许接收从任何来源发来的请求
* `Access-Control-Allow-Credentials`（可选）: 默认情况下，CORS请求不会发送cookie。如果服务器返回了这个头，那么就可以通过将`withCredentials`设置为true来将cookie同请求一同发送出去
* 后端服务器必须能处理OPTIONS方法的HTTP请求

> 如果将$http发送的请求中的withCredentials设置为true，但服务器没有返回`Access-Control-Allow-Credentials`，请求就会失败，反之亦然

###简单请求
使用下面HTTP方法的就是简单请求：

* HEAD
* GET
* POST

如果请求除了下面中的HTTP头外，没有其他头，也是简单请求：

* Accept
* Accept-Language
* Content-Language
* Last-Event-ID
* Content-Type
    * application/x-www-form-urlencoded
    * multipart/form-data
    * text/plain

归类为简单请求，因为浏览器可以不需要使用CORS就发送这类请求。简单请求不要求浏览器和服务器之间有任何的特殊通信


###非简单请求
不符合简单请求标准的请求被称为非简单请求，浏览器会以不同的方式处理它们。浏览器实际上会发送两个请求：预请求和请求。浏览器首先会向服务器发送**预请求**来获得发送请求的许可，只有许可通过了，浏览器才会发送真正的请求。浏览器会给预请求和请求都加上Origin头。

####预请求
浏览器发送的**预请求是OPTIONS类型的**，预请求中包含以下头信息：

* `Access-Control-Request-Method`：请求所使用的HTTP方法
* `Access-Control-Request-Headers`（可选)：以逗号分隔的非简单头列表，列表中的每个头都会包含在这个请求中

服务器接受这个请求并检查合法性，并在返回中添加下面的头：

* `Access-Control-Allow-Origin`：值必须和请求的来源相同，或者是`*`符号，以允许接受来自任何来源的请求
* `Access-Control-Allow-Methods`：可以接受的HTTP方法列表，对在客户端缓存响应结果很有帮助，并且未来发送的请求可以不必总是发送预请求
* `Access-Control-Allow-Headers`：如果设置了`Access-Control-Request-Headers`头，服务器必须在响应中添加同一个头

服务器返回了`200`状态码，真正的请求才会发出

##服务器端代理
服务器和页面处在同一个域中（或者不在同一个域中但支持CORS），做为所有远程资源的代理。为了实现服务器端代理，需要架设一个本地服务器来处理我们所有的请求，并负责向第三方发送实际的请求。

##使用AngularJS 进行身份验证

通过令牌授权来实现客户端身份验证，服务器需要做的是给客户端应用提供授权令牌。

当用户登录到我们的站点后，服务器会生成一个随机的令牌，并将用户会话同令牌之间建立关联，用户无需将ID或其他身份验证信息发送给服务器。

客户端发送的每个请求都应该包含此令牌，这样服务器才能根据令牌来对请求的发送者进行身份验证。

> 令牌本身是一个由服务器端生成的随机字符串，由数字和字母组成，它与特定的用户会话相关联。uuid库是用来生成令牌的好选择。