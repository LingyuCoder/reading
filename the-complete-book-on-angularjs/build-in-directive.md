##内置指令
###一组内置指令
一组和原声HTML标签名称相似的内置指令：
* ng-href：当使用当前作用域中的属性动态创建URL时，应该用`ng-href`代替href。防止插值尚未生效时跳转到错误的页面
* ng-src：在ng-src对应的表达式生效之前不会加载图像
* ng-disabled：可以把disabled属性绑定到以下表单输入字段上，包括input、textarea、select、button等元素
* ng-checked：将某个表达式同是否出现checked属性进行绑定
* ng-readonly：将某个返回真或假的表达式同是否出现readonly属性进行绑定
* ng-selected：对是否出现option标签的selected属性进行绑定
* ng-class：动态设置元素的类，方法是绑定一个代表所有需要添加的类的表达式。重复的类不会添加。当表达式发生变化，先前添加的类会被移除，新类会被添加
* ng-style

###子作用域
任何具有`ng-app`属性的DOM元素将被标记为`$rootScope`的起始点。`$rootScope`是作用域链的起始点，任何嵌套在`ng-app`内的指令都会继承它。

内置指令`ng-controller`的作用是为嵌套在其中的指令创建一个子作用域。子$scope只是一个JavaScript对象，其中含有从父级$scope中通过原型继承得到的方法和属性，包括应用的$rootScope。

> 操作指的是$scope上的标准JavaScript方法。

> 模型指的是$scope上保存的包含瞬时状态数据的JavaScript对象。持久化状态的数据应该保存到服务中，服务的作用是处理模型的持久化。

> 出于技术和架构方面的原因，绝对不要直接将控制器中的$scope赋值为值类型对象（字符串、布尔值或数字）。DOM中应该始终通过点操作符.来访问数据。

以下也会创建子作用域：
* ng-include：加载、编译并包含外部HTML片段到当前的应用中，需要考虑跨域资源共享和同源规则来确保模板可以在任何浏览器中正常加载。使用`ng-include`时会自动创建一个子作用域，可以通过`ng-controller`指定作用域
* ng-switch：和`ng-switch-when`及`on="propertyName"`一起使用，可以在propertyName发生变化时渲染不同指令到视图中
* ng-repeat：遍历一个集合或为集合中的每个元素生成一个模板实例，集合中的每个元素都会被赋予自己的模板和作用域。同时每个模板实例的作用域中都会暴露一些特殊的属性：
    - $index：遍历的进度（0...length-1）。
  - $first：当元素是遍历的第一个时值为true。
  - $middle：当元素处于第一个和最后元素之间时值为true。
  - $last：当元素是遍历的最后一个时值为true。
  - $even：当$index值是偶数时值为true。
  - $odd：当$index值是奇数时值为true。
* ng-view
* ng-controller
* ng-if：可以完全根据表达式的值在DOM中生成或移除一个元素，它不是通过CSS显示或隐藏DOM节点，而是真正生成或移除节点，当一个元素被`ng-if`从DOM中移除，同它关联的作用域也会被销毁。而且当它重新加入DOM中时，会通过原型继承从它的父作用域生成一个新的作用域


`ng-init`指令用来在指令被调用时设置内部作用域的初始状态

`{{ }}`语法是内置的模板语法，它会在内部$scope和视图之间创建绑定。基于这个绑定，只要$scope发生变化，视图就会随之自动更新。事实上它也是指令，是`ng-bind`的简略形式

`ng-cloak`指令会将内部元素隐藏，直到路由调用对应的页面时才显示出来

`ng-bind-template`用来在视图中绑定多个表达式

`ng-model`指令用来将input、select、textarea或自定义表单控件同包含它们的作用域中的属性进行绑定。它可以提供并处理表单验证功能，在元素上设置相关的CSS类（`ng-valid`、`ng-invalid`等），并负责在父表单中注册控件

`ng-show`和`ng-hide`根据所给表达式的值来显示或隐藏HTML元素

`ng-change`会在表单输入发生变化时计算给定表达式的值

`ng-form`用来在一个表单内部嵌套另一个表单，内部所有的子表单都合法时，外部的表单才会合法。要指定提交表单时调用哪个
JavaScript方法，使用下面两个指令中的一个：
* ng-submit
* ng-click

`ng-click`用来指定一个元素被点击时调用的方法或表达式

`ng-select`将数据同HTML的`<select>`元素进行绑定

`ng-submit`用来将表达式同`onsubmit`事件进行绑定

`ng-attr-(suffix)`，有时浏览器会对属性会进行很苛刻的限制，它可以防止属性非法