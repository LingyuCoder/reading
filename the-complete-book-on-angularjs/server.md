#服务器通信

##使用Amazon AWS 的无服务器应用
Amazon的基于浏览器的（服务端的是用NodeJS）SDK能让我们安心地组织应用，并且与工业级的后端服务进行交互。通过使用S3来存放应用和文件，将DynamoDB用作NoSQL存储，以及其他的海量服务，将应用全部存放于Amazon基础架构中现在已经成为可能。

* DynamoDB：快速且完全受控的NoSQL数据库服务
* 简单通知服务（SNS）：能让我们把消息推送到移动设备和其他服务
* 简单队列服务（SQS，Simple Queue Service）：能让我们以一种良好管理的方式创建巨型队列
* 简单存储服务（S3）：能让我们存储无限数量的大对象（上限是5T），对象数量不限
* 安全令牌服务（STS）：允许我们为IAM用户请求临时的受限权限认证

##标准模板

```html
<!doctype html>
<html>
    <head>
        <script
        src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.13/angular.min.js">
        </script>
        <script
        src="http://code.angularjs.org/1.2.13/angular-route.min.js"></script>
        <link rel="stylesheet" href="styles/bootstrap.min.css">
    </head>
    <body>
        <div ng-view></div>
        <script src="scripts/app.js"></script>
        <script src="scripts/controllers.js"></script>
        <script src="scripts/services.js"></script>
        <script src="scripts/directives.js"></script>
    </body>
</html>
```

##手动启动angular应用

可以通过如下代码手动启动应用：

```javascript
angular.element(document).ready(function() {
    // 启动oauth2库
    gapi.client.load('oauth2', 'v2', function() {
        // 最后，启动我们的Angular应用
        angular.bootstrap(document, ['myApp']);
    });
});
```
