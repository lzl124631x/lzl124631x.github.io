---
layout: post
title:  "Scope in JavaScript"
date:   March 26, 2016 9:28 PM
categories: JavaScript
---

本文为[JS核心系列：浅谈函数的作用域](http://www.cnblogs.com/onepixel/p/5036369.html)的笔记

# 作用域 (Scope)

> 在javascript中，`if`, `while`, `for` 等代码块不能形成独立的作用域。因此，javascript中没有块级作用域，只有函数作用域。
> 但是，在JS中有一种特殊情况：
> 如果一个变量没有使用`var`声明，window便拥有了该属性，因此这个变量的作用域不属于某一个函数体,而是window对象。

小纠正, 更准确的说法应该是"如果一个变量**在作用域链上**没有`var`声明, window便拥有该属性". 下例中`i`所在的scope是第一个匿名函数.

```
+function() {
    var i;
    +function() {
        i = 1; // 内层匿名函数的scope中没有声明i, 但是i所属的scope是外层匿名函数, 而不是window
    }();
    console.log(i); // 1
}();
console.log(i); // Uncaught ReferenceError: i is not defined
```

## `for`无法形成独立的作用域

这一点跟C/C++不一样, 所以不禁想验证下. 下例说明, `for`的确不会创建新的scope.

```
console.log(i); // undefined
for (var i = 0; i < 10; ++i) { }
console.log(i, window.i); // 10 10
```

可以看出, `for`中定义的`var`存在于`for`所在的scope (上例中即为`window`).

上例中, `for`中写不写`var`的效果是一样的, 都相当于:

```
var i;
console.log(i);
for (i = 0; i < 10; ++i) {}
console.log(i, window.i);
```

# 作用域链 (Scope Chain)

JS引擎会将当前函数的作用域放置在链表的首部, 随后是外层函数的作用域, ..., 最后, 会把`window`对象放置在链表尾部.

> 特殊情况：`with`语句
> JS中的`with`语句主要用来临时扩展作用域链，将语句中的对象添加到作用域的头部。`with`语句结束后，作用域链恢复正常。

```
foo = "window";
function first(){
    var foo = "first";
    function second(){
       var foo = "second";
       console.log(foo); // second
    }
    second();
    function third(obj){
       console.log(foo); //first
       with (obj){
           console.log(foo); //obj
       }
       console.log(foo); //first
    }
    var obj = {foo:'obj'};
    third(obj);
}
first();
```

# this关键字

==在一个函数中，this总是指向当前函数的所有者对象，this总是在运行时才能确定其具体的指向, 也才能知道它的调用对象。==

```
var foo = "window";
var obj = {
    foo : "obj",
    getFoo : function(){
        return function(){
            return this.foo;
        };
    }
};
var f = obj.getFoo();
f(); //window
```

```
var foo = "window";
var obj = {
    foo : "obj",
    getFoo : function(){
        var that = this;
        return function(){
            return that.foo;
        };
    }
};
var f = obj.getFoo();
f(); //obj
```

代码段1: 即`window.f = function() { return this.foo; }`, 调用`window.f()`时`this`指向`window`, `this.foo`即字符串\"window\".

代码段2: 首先调用`obj.getFoo()`, `this`指向`obj`, 所以`that`也指向`obj`. `var f = obj.getFoo()`相当于`window.f = function() { return obj.foo; }`, 因此最终返回的是字符串\"obj\".

