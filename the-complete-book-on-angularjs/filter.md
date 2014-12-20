##过滤器
###调用过滤器
过滤器用来格式化需要展示给用户的数据，在HTML中的模板绑定符号`{{ }}`内通过`|`符号来调用过滤器，在JavaScript代码中可以通过`$filter`来调用过滤器。

###内置过滤器
AngularJS内置了如下过滤器：
1. currency: 将一个数值格式化为货币格式
2. date: 将日期格式化成需要的格式
3. filter: 从给定数组中选择一个子集
4. json: 将一个JSON或JavaScript对象转换成字符串
5. limitTo: 根据传入的参数生成一个新的数组或字符串，新的数组或字符串的长度取决于传入的参数，通过传入参数的正负值来控制从前面还是从后面开始截取
6. lowercase: 将字符串转为小写
7. uppercase: 将字符串转为大写
8. number: 将数字格式化成文本
9. orderBy: 用表达式对指定的数组进行排序

###自定义过滤器
可以这样自定义一个过滤器：
```javascript
angular.module('myApp.filters', [])
    .filter('capitalize', function() {
        return function(input) {};
    });
```

###表单验证
AngularJS提供了一些表单验证指令：
1. 必填项：required
2. 最小长度：ng-minlength
3. 最大长度：ng-maxlength
4. 模式匹配：ng-pattern
5. 电子邮件：input类型为email即可
6. 数字：input类型为number
7. URL：input类型为url


通过`formName.inputFieldName.property`能够访问表单字段的属性，包括
    * 未修改的表单：`$pristine`
    * 修改过的表单：`$dirty`
    * 合法的表单：`$valid`
    * 不合法的表单：`$invalid`
    * 错误：`$error`，包含当前表单的所有验证内容，以及它们是否合法的信息

AngularJS会根据表单的状态来添加一些CSS类，有
    * `.ng-pristine {}`
    * `.ng-dirty {}`
    * `.ng-valid {}`
    * `.ng-invalid {}`
    
ngModelController中的`$setViewValue()`方法被调用时，`$parsers`数组中的函数会逐个调用，这些函数可以对值进行转换，或通过`$setValidity()`来设置表单的合法性

当绑定的ngModel值发生了变化，并经过`$parsers`数组中解析器的处理后，这个值会被传递
给`$formatters`流水线。$formatters中的函
数也可以修改并格式化这些值。

**在指令的链接阶段，也就是link函数中向ngModel的$formatters和$parsers中加入处理函数**

> 不要忘记给输入字段添加name属性。给输入字段添加name属性非常重要：这决定了我们将验证信息展示给用户时如何引用表单输入字段。


当用户视图提交表单，可以在作用域捕获到一个`submitted`值，然后对表单内容进行验证并显示错误信息

通过`ng-message`可以很方便的为验证创建自定义消息