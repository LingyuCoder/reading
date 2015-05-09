#测试

##开始测试
###测试工具
Karma是一个测试工具，它从头开始构建，免去了设置测试方面的负担，这样我们就可以将主要精力放在构建核心应用逻辑上。

```shell
$ npm install -g karma #安装karma
$ karma init test/karma.conf.js #创建配置文件
$ karma run test/karma.conf.js #运行测试
$ karma start test/karma.conf.js #代码有变化时自动运行测试
```

##单元测试

单元测试的`karma.conf.js`文件可能看起来像这样：

```javascript
module.exports = function(config) {
    config.set({
        basePath: '..',
        frameworks: ['jasmine'], //可以选择jasmine（默认）、mocha、QUnit
        files: [
            'lib/angular.js',
            'lib/angular-route.js',
            'test/lib/angular-mocks.js',
            'js/**/*.js',
            'test/unit/**/*.js'
        ],
        exclude: [],
        port: 8080,
        logLevel: config.LOG_INFO,
        autoWatch: true,
        browsers: ['Safari'],
        singleRun: false
    });
};
```

##端到端测试
端到端测试要使用ng-scenario框架。端到端测试是跑在服务器上的，ng-scenario框架只需要在浏览器中加载所有这些测试就可以了。

```javascript
module.exports = function(config) {
    config.set({
        basePath: '..',
        frameworks: ['ng-scenario'],
        files: [
            'test/e2e/**/*.js'
        ],
        exclude: [],
        port: 8080,
        logLevel: config.LOG_INFO,
        autoWatch: false,
        browsers: ['Chrome'],
        singleRun: false,
        urlRoot: '/_karma_/',
        proxies: {
            '/': 'http://localhost:9000/'
        }
    });
};
```

##配置选项
###框架
Jasmine是默认的测试框架，但也支持Mocha、QUnit和其他测试框架。需要安装插件：

```shell
$ npm install --save-dev karma-jasmine
```

###RequireJS
如果这个项目使用了RequireJS①库，询问是否要包含RequireJS的，就要回答yes

###浏览器捕获
要自动启动哪个浏览器来捕获测试结果。要使用Karma来启动和运行，每个浏览器都需要安装额外的插件。

```shell
$ npm install --save-dev karma-chrome-launcher
```

###源文件和测试文件
源文件和测试文件存放在哪里，这个数组可以包含简单的字符串和对象。


使用一个对象指定文件的状况如下：

```javascript
{
    pattern: 'public/js/watch-me.js',
    watched: true,
    included: false,
    served: false
}
```

* pattern：一个正则表达式，匹配测试文件
* watched：如果Karma设置成使用autoWatch，这个布尔值用于指定文件是否会被监控，默认为true
* included：这个布尔值告诉Karma在浏览器中使用<script>标签来加载文件。默认为true
* served：这个布尔值告诉Karma通过Karma Web服务器提供这个文件。如果设置成true，这个文件就可以通过Web服务器来访问。默认为true

###顺序
文件列出的顺序很重要，被依赖的应该放前面

###exclude
利用exclude选项可以设置一个不想默认包含的文件列表

###basePath
可以把根路径地址设置为在files和exclude属性中定义的相对路径

###autoWatch
设置为true会让karma在files中的文件发生变更时执行已配置的测试

###captureTimeout
如果浏览器加载时间超过captureTimeout（默认是60秒或者60000毫秒），Karma会把进程杀掉，再试一次。如果它试了三次还是失败，Karma就不再尝试启动浏览器了

###colors
如果不想在终端中显示带颜色的输出，可以把colors属性设置成false来关闭

###hostname
主机名默认是localhost，如果想要改变它，可以设置hostname属性

###logLevel
设置logLevel属性来设置详细输出等级，可能的值有：

* config.LOG_DISABLE
* config.LOG_ERROR
* config.LOG_WARN
* config.LOG_INFO
* config.LOG_DEBUG

###端口
Web服务器使用的端口，默认是9876

###预处理器
以在测试运行之前让Karma预处理文件

###代理
设置HTTP代理，当我们的测试取到一个路由时，它们可以从远程服务器获取

###报表
可以把报表设置成在终端中显示关于所有类型测试状态的有用输出

###singleRun
如果这个布尔值被设置成true，Karma会用所有已配置的浏览器运行这些测试一次；如果它们都通过了，就会看到一个退出代码0，要是有失败的，就会是1

###urlRoot
Karma运行的根URL

##Jasmine
Jasmine是一个用于测试JavaScript代码的行为驱动开发框架

###细则套件
`describe()`函数带有两个参数，一个字符串，一个函数。字符串是待建立的细则（spec）套件名称或者描述，函数封装了测试套件。

可以嵌套这些describe()函数，这样我们可以创建一个测试树来执行那些在测试中设置的不同条件。

```javascript
describe('Unit test: MainController', function() {
    describe('index method', function() {
        // 细则放这里
    });
});
```

###定义细则
通过调用`it()`函数来定义一个细则，it()函数带有两个参数：

1. 字符串：细则的标题或者描述
2. 函数：包含了一个或多个用于测试代码功能的预期。

一个细则通过，也就是测试用例成功，必须所有预期结果都是`true`

```javascript
describe('A spec suite', function() {
    it('contains a passing spec', function() {
        expect(true).toBe(true);
    });
});
```

###预期
使用`expect()`函数来建立预期。`expect()`函数带有一个单值参数。这个参数被称为真实值。给它串联一个带期望值参数的匹配器函数，还可以通过not来创建测试的否定式

```javascript
describe('A spec suite', function() {
    it('contains a passing spec', function() {
        expect(true).toBe(true);
    });
    it('contains another passing spec', function() {
        expect(false).not.toBe(true);
    });
});
```

###内置的匹配器
####toBe
`toBe()`使用JavaScript的严格相等的`===`来比较

####toEqual
`toEqual()`匹配器比较的是值，对简单字面量和变量有效

####toMatch
`toMatch()`匹配器使用正则表达式匹配字符串

####toBeDefined
`toBeDefined()`匹配器将值与undefined进行比较

####toBeUndefined
`toBeUndefined()`跟`toBeDefined()`匹配器相反

####toBeNull
`toBeNull()`匹配器将值与null进行比较

####toBeTruthy
`toBeTruthy()`匹配器把值转换为布尔类型之后与true进行比较

####toBeFalsy
`toBeFalsy()`匹配器把值转换成布尔类型之后与false比较

####toContain
`toContain()`匹配器检测一个条目是否在数组中

####toBeLessThan
`toBeLessThan()`匹配器建立了一个期望，比较一个数值是否小于预期

####toBeGreaterThan
`toBeGreaterThan()`匹配器建立了一个期望，比较一个数值是否大于预期

####toBeCloseTo
`toBeCloseTo()`匹配器在一个指定的精度级别内比较一个值是否接近另一个值

####toThrow
`toThrow()`匹配器验证一个函数是否抛出了异常

###自定义匹配器
可以通过`addMatcher()`来创建自定义的匹配器：

```javascript
describe('A spec suite', function() {
    this.addMatchers({
        toBeLessThanOrEqual: function(expected) {
            return this.actual <= expected;
        }
    });
});
```

###设置测试条件
除了手动在每个测试中设置测试条件，还可以使用beforeEach方法来运行一组设置函数。

`beforeEach()`函数带一个参数：一个函数，在每个细则运行之前被调用一次

```javascript
describe('A spec suite', function() {
    var message;
    beforeEach(function() {
        message = "hello ";
    });
    it('should say hello world', function() {
        expect(message + "world").toEqual("hello world");
    });
    it('should say hello ari', function() {
        expect(message + "ari").toEqual("hello ari");
    });
});
```

也可以通过`afterEach()`重置条件：

```javascript
describe('A spec suite', function() {
    var count;
    afterEach(function() {
        count = 0;
    });
    it('should add one to count', function() {
        count += 1;
        expect(count).toEqual(1);
    });
    it('should check for the reset value', function() {
        expect(count).toEqual(0);
    });
});
```

##端到端的介绍
使用Angular场景运行期来模拟用户交互，运行期会通过嵌入一个iframe的方式来运行，核心API是`browser()`方法

###导航页面
通过`navigateTo()`函数来加载URL

```javascript
browser().navigateTo(url)

//或者获取动态URL
browser().navigateTo(title, function() {
    // 在这里返回动态url;
    return '/';
});
```

###刷新页面
`browser().reload()`

###操作window对象
获取当前页面的超链接：`browser().window().href()`

获取测试frame中当前加载页面的路径：`browser().window().path()`

获取测试frame中当前加载页面的搜索字符串：`browser().window().path()`

获取测试frame里当前加载页面最后一次的hash：`browser().window().hash();`

获取测试frame中当前加载页面的`$location.url()`：`browser().location().url()`

获取测试frame中当前加载页面的`$location.path()`：`browser().location().path()`

获取当前页面的`$location.search()`：`browser().location().search()`

获取到当前页面的hash：`browser().location().hash()`

###建立预期
通过`expect()`建立预期：

```javascript
expect(browser().location().path())
    .toBe('/')
    // 或者用not()来否定这个期望
expect(browser().location().path())
    .not().toBe('/home')
```
###与内容交互
可以选择元素，在输入框中输入值，点击按钮，校验内容是否出现在该出现的地方，遍历循环器，等等：

通过`element()`选择页面上的元素，两个参数：

* 选择器：jQuery HTML元素选择器
* 标签：用于在浏览器或者终端输出的文本字符串

获取到元素后，可以查询或设置元素的各种属性

###指定元素
可以通过`using()`来指定元素

###查询绑定
可以用binding()方法查询作用域中指定的绑定：

```html
<input type="text" ng-model="name" />
```

```javascript
it('should update the name', function() {
    using('.form').input('name').enter('Ari');
    expect(
        using('.form').binding('name')
    ).toBe('Ari');
});
```

###与输入元素交互
通过一些方法能够与输入元素交互，`input()`可以获取页面上的输入元素，并返回一个对象，这个对象有如下接口：

* `enter()`：向一个输入框输入值
* `check()`：检测一个复选框的值
* `select()`：选中一个单选按钮的指定值
* `val()`：获取输入框的当前值

```javascript
<input type="text" ng-model="name" />

input('name').enter('Ari');
```

通过`select()`能够获取页面上的选项列表元素，并返回一个有如下接口的对象：

* `option()`：选中列表中的一个值
* `options()`：选中多选select中的多个值

```html
<select ng-model="color" ng-options="c.name for c in colors">
    <option value="">Pick your favorite color</option>
</select>
```
```javascript
select('color').option('red');
select('color').options('Ghostbusters', 'Titanic');
```

###重复循环元素
repeater()函数自身返回一个对象，有两个参数：

* 选择器（字符串）。jQuery选择器，指向那些我们所关注的元素
* 标签（字符串，可选）。标签是用于测试输出的一个字符串

返回的对象有如下接口：

* `count()`: 返回重复器里有多少行与DOM中的jQuery选择器匹配
* `column()`: 返回一个数组，数组中的元素是列中的值，这些值包含了与DOM中jQuery选择器匹配的重复器中的给定绑定。
* `row()`：返回一个数组，数组中的元素是行中的值，这些值包含了与DOM中给定jQuery选择器匹配的重复器中的给定绑定。

```html
<table id="phonebook">
    <tr ng-repeat="person in people">
        <td>{{ person.name }}</td>
        <td>{{ person.email }}</td>
    </tr>
</table>
```

```javascript
repeater('#phonebook tr').count();
repeater('#phonebook tr').column('person.name');
repeater("#phonebook tr").row(0);
```
