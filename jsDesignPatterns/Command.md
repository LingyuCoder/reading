Command——命令模式
===
Command模式旨在将方法调用、请求或操作封装到单一对象中，从而根据不同的请求对客户进行参数化和传递可供执行的方法调用。此外，这种模式将调用操作的对象与知道如何实现该操作的对象解耦，并在交换出具体类（对象）方面提更大的整体灵活性

Command模式背后的主题思想是：为我们提供一种分离职责的手段，这些职责包括执行命令的任意地方发布命令以及将该职责转而委托给不同对象

```javascript
var studentManager = (function() {
    var studentList = [];

    function add(id, name) {
        studentList.push({
            id: id,
            name: name
        });
        return this;
    }

    function remove(id) {
        var i;
        for (i = studentList.length; i--;) {
            if (studentList[i].id === id) {
                studentList.splice(i, 1);
                break;
            }
        }
        return this;
    }

    function get(id) {
        var i;
        for (i = studentList.length; i--;) {
            if (studentList[i].id === id) {
                return studentList[i];
            }
        }
    }

    function count() {
        return studentList.length;
    }

    function getAt(index) {
        return studentList[index];
    }

    return {
        add: add,
        remove: remove,
        get: get,
        getAt: getAt,
        count: count,
        execute: function(name) {
            return this[name] && this[name].apply(this, Array.prototype.slice.call(arguments, 1));
        }
    };
}());
```

这里通过在传统的模块模式上增加了个excute方法，用于实现Command模式，而修改后的调用就可以变成这个样子：

```javascript
studentManager.execute("add", 696, "skyinlayer");
console.log(studentManager.execute("get", 696));
console.log(studentManager.execute("count"));
studentManager.execute("remove", 696);
console.log(studentManager.execute("count"));
```
这样我们就可以通过单一的方法进行参数化的调用了，拥有更强大的灵活性