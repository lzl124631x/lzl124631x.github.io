---
layout: post
title:  "JavaScript中的自调用函数"
date:  March 30, 2016 4:58 PM
categories: JavaScript
---

# 自调用函数
自调用函数(self-invoking funciton)就是函数定义完之后会立即运行的函数. 最常见的写法是:

```js
(function() {
  // function body...
}());
// or
(function() {
  // function body...
})();
```

# 自调用函数的另一种写法
不过最近看某个库的源代码(暂时忘了是哪个了=,.=)时发现有如下写法:

```js
+function() {
  // function body
}();
```

感到神奇, 于是[SO了一下](http://stackoverflow.com/questions/13341698/javascript-plus-sign-in-front-of-function-name), 原来这是另一种自调用函数的写法.

`+`号使得js解释器认为它现在在处理的是一个表达式, 而非函数定义. 如果不写这个`+`号, 解释器会按照函数定义去处理, 并认为后面的`()`是语法错误.

其实除了`+`号, `-`, `!`, `~`以及其他一元操作符都可以产生相同的效果.

与要在用`()`把函数包起来的常用写法相比, 这种写法的好处(也许)就是只要在前面加个`+`就行了, 更省事儿. 当然, 最后的`();`是一定不能少的.

# 需要注意的问题

主要注意的是, 一定不能省略自调用函数前面赋值语句的`;`.

如果自调用函数前面是函数定义, 一切正常:

```js
function x() { console.log(1); } +function () { console.log(2); }();
// 2
```

但是如果它前面是个赋值语句, 你的代码非常容易出bug, 即使是用`()`包裹自调用函数也不行.

```js
var s = {}; s.a = function() { console.log(1); } + function() { s.a(); }();
// Uncaught TypeError: s.a is not a function(…)
var s = {}; s.a = function() { console.log(1); } (function() { s.a(); }());
// Uncaught TypeError: s.a is not a function(…)
var a = function() { console.log(1); } + function() { console.log(2); }();
// 2
// a is “function () { console.log(1); }undefined”
var a = function() { console.log(1); } (function() { console.log(2); }());
// 2 1
// a is `undefined`!!
var a = function() { console.log(1); } (+ function() { console.log(2); }());
// 2 1
// a is `undefined`!!
```

我以前曾经遇到这个bug, 但是并不知道发生了什么, 只是很奇怪为什么自调用函数前面的那个函数会自动执行... 后来我无奈用了下面这种弱鸡的形式

```js
function init() {
  // function body
}
init();
```

正确的做法应该是:

```js
var x = function() {
  console.log(1);
}; // <---------------- here! DO NOT omit this semicolon!
+ function() {
  console.log(2);
}();
```

我还听说有minify之前运行得好好的, minify之后就出bug, 也是因为少了`;`.

综上, **在每句赋值语句后面加上`;`号是一个很重要的好习惯**!

# 参考

1. [Does omitting semicolons affect performance in JavaScript?](http://stackoverflow.com/questions/14379946/does-omitting-semicolons-affect-performance-in-javascript)
2. [Why should I use a semicolon after every function in javascript?](http://stackoverflow.com/questions/1834642/why-should-i-use-a-semicolon-after-every-function-in-javascript)