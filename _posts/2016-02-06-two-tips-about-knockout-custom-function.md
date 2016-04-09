---
layout: post
title:  "两个有关Knockout自定义拓展方法fn的小技巧"
date:   2016-02-06 00:00:00 +0800
categories: JavaScript, Knockout
---

# 让observable自增/自减

1. 最简单的方法是`self.num(self.num() + 1)`, 但是这个写起来比较麻烦.

2. 一种方法是拓展`observable`的方法, 增加`increment`和`decrement`方法:

```js
ko.observable.fn.increment = function (value) {
    this(this() + (value || 1));
};
ko.observable.fn.decrement = function (value) {
    this(this() + (value || 1));
};
```
这样用起来简单多了. `self.num.increment(3)`就可以让`self.num`自增3.

3. 还有一种方法, 可以对某些`observable`拓展其方法而不影响其他的`observable`.

```js
ko.extenders['incrementable'] = function (target, enabled) {
    if (enabled) {
        target.increment = function (incValue) {
            this(this() + (incValue || 1));
        }.bind(target);
    }
    return target;
};
var num = ko.observable(0).extend({ incrementable: true });
num.increment();
```

# 在`subscribe`的时候触发`callback`

很多时候我们在给`observable`注册(`subscribe`)回调函数(`callback`)的时候, 想同时触发一次`callback`. 但是`subscribe`只在`observable`变化之后才会触发`callback`, 注册的时候不会触发. 这时我们可以在`subscribable`上创建一个方法来达到这个目的.

```js
ko.subscribable.fn.callAndSubscribe = function (callback) {
    this.subscribe(callback);
    callback();
}
self.num.callAndSubscribe(function() { /*...*/ })
```

# 参考

1. [Adding custom functions using "fn"](http://knockoutjs.com/documentation/fn.html)

1. [Increment a KnockoutJS observable in an good expressive way](http://stackoverflow.com/questions/27804726/increment-a-knockoutjs-observable-in-an-good-expressive-way)
