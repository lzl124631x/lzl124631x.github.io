---
layout: post
title: 2016阿里前端开发实习生面试经历 - 读后笔记
date:   April 14, 2016 10:44 PM
tags: JavaScript
---

本文为[2016阿里前端开发实习生面试经历](http://www.cnblogs.com/yxy99/p/5398617.html)的读后笔记.

# 如何让一个函数调用自身?

我的第一反应是, 直接调用就好了啊, 如:

```js
function f() {
  console.log(1);
  f();
}
f();
```

有用`argument.callee`的必要么?

[arguments对象的callee的属性](https://www.w3cmm.com/javascript/arguments-callee.html)中指出, 利用`arguments.callee`可以解耦合, keep you DRY.

[arguments.callee - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/callee)中指出了它最有用的的情况是"当前函数是匿名函数的时候". 同时指出ES5在严格模式下禁止使用`arguments.callee`, 推荐使用有名函数来避免使用`arguments.callee`.

# 如何实现数组的浅拷贝和深拷贝?

# 如何高效地进行递归操作?

尾递归, memoize(备忘录)

## 尾递归(Tail Recursion)

[What is tail recursion?](http://stackoverflow.com/questions/33923/what-is-tail-recursion)

# 事件流

先说说IE的事件流吧，是采用了事件冒泡机制，而DOM标准事件流则分为三个阶段:事件捕获阶段，处理事件阶段，事件冒泡阶段。通常我们需要写一个函数来兼容两种浏览器的事件。然后balabala把函数用口头实现了一次。。。

# 事件委托

就是把所有子节点的事件都委托给其父节点进行监听，在函数中可以swtich某个节点。

键盘事件可以监听它的keyCode

# React, 虚拟DOM, diff算法

# Web性能优化

大概有css精灵图，http缓存，cdn，脚本合并。。。然后一个一个详细讲解。

