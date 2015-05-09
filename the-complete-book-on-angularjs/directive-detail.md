##指令详解

`directive()`用来定义指令的：
```javascript
angular.module('myApp', [])
    .directive('myDirective', function($timeout, UserDefinedService) {
        // 指令定义放在这里
        return {
            // 通过设置项来定义指令，在这里进行覆写
        };
    });
```

`directive()`方法可以接受两个参数：
1. name：指令的名称
2. factory_function：返回一个定义指令的函数，`$compile`服务利用这个方法返回的对象来构造指令的行为

> 为了避免与未来的HTML标准冲突，给自定义的指令加入前缀来代表自定义的命名空间。AngularJS本身已经使用了`ng-`前缀，所以可以选择除此以外的名字。

当AngularJS在DOM中遇到具名的指令时，会去匹配已经注册过的指令，并通过名字在注册过的对象中查找。此时，就开始了一个指令的生命周期，指令的生命周期开始于`$compile`方法并结束于link方法

###restrict（String）
个可选的参数。它告诉AngularJS这个指令在DOM中可以何种形式被声明。默认AngularJS认为restrict的值是A，即以属性的形式来进行声明:
* E（元素）：`<my-directive></my-directive>`
* A（属性，默认值）：`<div my-directive="expression"></div>`
* C（类名）：`<div class="my-directive:expression;"></div>`
* M（注释）：`<--directive:my-directive expression-->`

属性是用来声明指令最常用的方式，因为它能在包括老版本的IE浏览器在内的所有浏览器中正常工作，并且不需要在文档头部注册新的标签。

包含某个组件的核心行为时使用元素型。用额外的行为、状态或者其他内容进行修饰或扩展时使用属性型

###priority（Number）
如果一个元素上具有两个优先级相同的指令，声明在前面的那个会被优先调用。如果其中一个的优先级更高，则不管声明的顺序如何都会被优先调用：具有更高优先级的指令总是优先运行。

默认为0，ngRepeat为1000，是所有内置属性中最高的

###terminal（Boolean）
告诉AngularJS停止运行当前元素上比本指令优先级低的指令。但同当前指令优先级相同的指令还是会被执行

###template（String | Function）
template参数是可选的，必须被设置为以下两种形式之一:
* 一段HTML文本
* 一个可以接受两个参数的函数，参数为`tElement`和`tAttrs`，并返回一个代表模板的字符串

在实际生产中，更好的选择是使用templateUrl参数引用外部模板，因为多行文本阅读和维护起来都是一场噩梦。

###templateUrl（String | Function）
可选的参数，可以是以下类型：
* 一个代表外部HTML文件路径的字符串
* 一个可以接受两个参数的函数，参数为tElement和tAttrs，并返回一个外部HTML文件路径的字符串

模板的URL都将通过AngularJS内置的安全层， 特别是`$getTrustedResourceUrl`，这样可以保护模板不会被不信任的源加载

调用指令时会在后台通过Ajax来请求HTML模板文件，也就是说：
* 需要防止CORS错误
* 编译和链接要暂停，等待模板加载完成

模板加载后，AngularJS会将它默认缓存到`$templateCache`服务中，，可以提前将模板缓存到一个定义模板的JavaScript文件中，这样就不需要通过XHR来加载模板了

###replace（Boolean）
可选，默认为false，默认值意味着模板会被当作子元素插入到调用此指令的元素内部

###scope（Boolean | Object）
scope参数是可选的，默认为false：

* false：直接调用相同的作用域对象；
* true：从当前作用域对象继承一个新的作用域对象；
* 对象：创建一个同当前作用域相隔离的作用域对象。

使用隔离作用域时，可以将指令内部的隔离作用
域，同指令外部的作用域进行数据绑定：
* 本地作用域属性：使用`@`符号将本地作用域同DOM属性的值进行绑定
* 双向绑定：通过`=`可以将本地作用域上的属性同父级作用域上的属性进行双向的数据绑定
* 父级作用域绑定：通过`&`符号可以对父级作用域进行绑定，以便在其中运行函数

###transclude（Boolean）
可选，默认为false

可以将整个模板，包括其中的指令通过嵌入全部传入一个指令中。这样做可以将任意内容和作用域传递给指令。transclude参数就是用来实现这个目的的，指令的内部可以访问外部指令的作用域，并且模板也可以访问外部的作用域对象

> 只有当你希望创建一个可以包含任意内容的指令时，才使用transclude: true

###controller（String | Function）
可选：
* 字符串：以字符串的值为名字，查找注册在应用中的控制器的构造函数
* 函数：直接定义内联的控制器

可以向控制器中注入如下服务：
* $scope: 与指令元素相关联的当前作用域
* $element: 当前指令对应的元素
* $attrs: 由当前元素的属性组成的对象
* $transclude: 嵌入链接函数会与对应的嵌入作用域进行预绑定。transclude链接函数是实际被执行用来克隆元素和操作DOM的函数。

###controllerAs （String）
用来设置控制器的别名，可以以此为名来发布控制器，并且作用域可以访问controllerAs。这样就可以在视图中引用控制器，甚至无需注入$scope。

###require（String | Array）
符串或数组元素的值是会在当前指令的作用域中使用的指令名称。require会将控制器注入到其值所指定的指令中，并作为当前指令的链接函数的第四个参数。

默认情况下，指令只会在自身的元素上查找控制器。可以用下面的前缀进行修饰，改变查找控制器时的行为：
* `?`: 如果在当前指令中没有找到所需要的控制器，会将null作为传给link函数的第四个参数
* `^`: 如果添加了^前缀，指令会在上游的指令链中查找require参数所指定的控制器
* `?^`: 将前面两个选项的行为组合起来，可选择地加载需要的指令并在父指令链中进行查找
* 没有前缀: ，指令将会在自身所提供的控制器中进行查找，如果没有找到任何控制器（或具有指定名字的指令）就抛出一个错误

###compile（Object | Function）
在compile函数内部，只对DOM进行操作，返回函数等效于使用link配置，返回对象的话包含两个函数：

1. preLink会在编译阶段之后、指令连接到子元素之前运行
2. postLink会在所有子元素指令都链接之后才运行

```javascript
compile: function(tElement, tAttrs, transclude) {
    // 一些DOM操作
    return {
        pre: function preLink(scope, iElement, iAttrs, controller) {},
        post: function postLink(scope, iElement, iAttrs, controller) {}
    };
}
```

###link（Function）

link函数会访问scope对象，其返回一个postLink函数。如果在compile中返回了post，那么link选项就会被忽略

```javascript
link: function postLink(scope, iElement, iAttrs){}
```

###compile和link
compile和link有许多异同：

* compile函数只会被调用一次，而link函数的调用次数可能会很多。
* compile用于对模板自身的转换，而link负责模型和视图之间进行动态关联
* link函数能够访问scope作用域对象，而compile不会，因为在编译阶段，scope对象还不存在。
* link和compile都会接收指令声明的DOM元素以及属性列表
* compile可以返回preLink和postLink函数，而link只能返回postLink函数


##AngularJS的生命周期
AngularJS应用启动后会进行编译和链接，作用域会同HTML进行绑定，应用可以对用户在HTML中进行的操作进行实时响应。

###编译三个阶段
1. 首先浏览器会用它的标准API将HTML解析成DOM。模板必须是可被解析的HTML。这是AngularJS和那些“以字符串为基础而非以DOM元素为基础的”模板系统的区别之处。
2. DOM的编译是有`$compile`方法来执行的。这个方法会遍历DOM并找到匹配的指令。一旦找到一个，它就会被加入一个指令列表中，这个列表是用来记录所有和当前DOM相关的指令的。 一旦所有的指令都被确定了，会按照优先级被排序，并且他们的compile方法会被调用。指令的`$compile()`函数能修改DOM结构，并且要负责生成一个link函数（后面会提到）。`$compile`方法最后返回一个合并起来的链接函数，这是链接函数是每一个指令的compile函数返回的链接函数的集合。
3. 通过调用一步所说的链接函数来将模板与作用域链接起来。这会轮流调用每一个指令的链接函数，让每一个指令都能对DOM注册监听事件，和建立对作用域的的监听。这样最后就形成了作用域的DOM的动态绑定。任何一个作用域的改变都会在DOM上体现出来。

大致过程如下：

```javascript
var $compile = ...; // injected into your code
var scope = ...;

var html = '<div ng-bind='exp'></div>';

// Step 1: parse HTML into DOM element
var template = angular.element(html);

// Step 2: compile the template
var linkFn = $compile(template);

// Step 3: link the compiled template with the scope.
linkFn(scope);
```

> 模板之中可能含有指令，指令之中可能又含有模板，模板之中又含有指令，由此形成一棵模板树。只有具有最高优先级的指令中的模板会被编译。如果一个元素已经有一个含有模板的指令了，永远不要对其用另一个指令进行修饰。一个指令会将内部子指令的模板合并在一起成为一个模板函数并返回，它无法查找父指令，只能通过模板函数访问内部子指令


##ngModel
ngModel提供更底层的API来处理控制器内的数据。

1. 为了设置作用域中的视图值，需要调用`ngModel.$setViewValue()`函数，接受一个字符串参数value，表示想要赋予的实际值，然后：
2. `ngModel.$setViewValue()`方法会更新控制器本地的`$viewValue`，然后将值传递给每一个`$parser`函数
3. 值被解析且`$parser`所有函数都完成后，值会赋给`$modeValue`属性，并且传递给指令中`ng-model`属性提供的表达式
4. 所有步骤都完成后，`$viewChangeListeners`中所有的监听器都会被调用

> 单独调用`$setViewValue()`不会唤起一个新的digest循环，因此如果想更新指令，需要在设置`$viewValue`后手动触发digest


ngModel的`$render`方法可以定义视图具体的渲染方式，它在`$parser`完成后被调用

ngModelController中有几个属性可用来检查甚至修改视图：

1. $viewValue： 保存着更新视图所需的实际字符串。
2. $modelValue：由数据模型持有。$modelValue和$viewValue可能是不同的，取决于$parser流水线是否对其进行了操作。
3. $parsers：$parsers的值是一个由函数组成的数组，其中的函数会以流水线的形式被逐一调用。ngModel从DOM中读取的值会被传入$parsers中的函数，并依次被其中的解析器处理。
4. $formatters：$formatters的值是一个由函数组成的数组，其中的函数会以流水线的形式在数据模型的值
发生变化时被逐一调用。它和$parser流水线互不影响，用来对值进行格式化和转换，以便在绑定了这个值的控件中显示。
5. $viewChangeListeners：$viewChangeListeners的值是一个由函数组成的数组，其中的函数会以流水线的形式在视图中的值发生变化时被逐一调用。通过$viewChangeListeners，可以在无需使用$watch的情况下实现类似的行为。由于返回值会被忽略，因此这些函数不需要返回值。