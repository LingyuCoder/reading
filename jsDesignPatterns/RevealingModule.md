Revealing Module——揭示模块模式
===
##Revealing Module实现
Revealing Module是Module模式的一个改进版本，主要不同就是将API设为私有成员，然后在return中暴露这些私有成员的引用：

```javascript
var someModule = (function(){
    var privateVar = "private";
    var publicVar = "public";
    function privateFn(){}
    function publicFn(){}
    return {
        aPublicFn: publicFn
        aPublicVar: publicVar
    };
}());
```
##Revealing Module优缺点
使用Revealing Module可以使脚本语法更加一致，从而改善可读性。同时公有方法间的互相调用也更加简单。但如果一个私有函数引用一个公有函数，公有函数是不能被覆盖的，因为return中的只是一个引用。这个私有函数依旧会继续引用原来的函数，而不会引用修改后的函数
