---
layout: post
title:  "关于fastclick.js"
date:   2016-02-16 00:00:00 +0800
tags: JavaScript Fastclick
---

官网： [Fastclick on Github](https://github.com/ftlabs/fastclick)

# fastclick.js解决了什么问题?
自己接触WebApp开发的前期, 总感觉WebApp上的按键操作不如NativeApp的灵敏, 好像有那么一小点延迟.

后来才知道, 这是移动端浏览器特意设计的延迟 -- 在移动端, "用户的点击"和"点击生效"之间会有300ms的延迟.

这一设计是为了支持双击操作(比如双击放大页面). 如果用户在300ms内点击了第二下, 浏览器则会认为用户想要双击而不是单击.

移动端浏览器是这么处理这300ms延迟的.

1. 用户点击屏幕, 触发`touchstart`事件.
2. 用户停止点击, 触发`touchend`事件.
3. 浏览器等待300ms, 看用户是否会点击第二次.
4. 如果没有第二次点击, 则触发`click`事件.

fastclick.js做的事情就是消除这300ms的延迟, 让你的WebApp体验更加像是NativeApp.

#如何使用fastclick.js?
非常简单, 照着[Fastclick](https://github.com/ftlabs/fastclick)的README做就好了.

除了直接通过`<script>`标签引入, 它还支持AMD, CommonJS模块系统.

如果你jQuery的话, 引入fastclick后, 只需要下面这样就OK了.

```
$(function() {
    FastClick.attach(document.body);
});
```

# 我遇到的一个fastclick的坑.

其实这才是本文的主要目的. 今天碰到一个奇怪的问题: 我发现在桌面版Chrome上, `click`可以正常触发`focus`事件; 但是打开Mobile Device Mode之后, `click`后就无法再触发`focus`事件了!

最后把问题定位到了fastclick, 如果不用它就 不会出现这个问题.

另外, 我还发现, 在我的iPhone上, 只有时间大于100ms, 小于1s的`click`才可以触发`focus`, 时间更短或更长的`click`则都不会.

具体为什么会有这个问题, 其实我还不清楚=,.=. 但是我做了不少尝试.

首先是`needsclick`, 这个README里面有写. 后来我又在源代码里找到了`needsfocus`. 这两个类可以禁用fastclick的hack, 但是有个缺陷:
```
div.needsclick
  div.inner
```
这样的结构中, 用户点击`.needsclick`可以正常触发`.needsclick`的`focus`事件, 但是如果点到了`.inner`就无法触发`.needsclick`的`focus`事件了.

最后我还是选用了简单粗暴的方法: 给元素绑上`click`事件, 在`click`中触发`focus`.

#参考
1. [Hybrid Apps and the Curse of the 300ms Delay](http://blog.ionic.io/hybrid-apps-and-the-curse-of-the-300ms-delay/)