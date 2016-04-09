---
layout: post
title:  "Button with Hover Effect (Learned from 百度脑图)"
date:   2015-11-26 00:00:00 +0800
categories: FrontEnd, CSS
---

今天想学学PM的技能, 打开了[百度脑图](http://naotu.baidu.com/)的网站, 看到中间那个按键的hover效果蛮好看, 遂学习一下.

效果如下:
![](/images/2015-11-26-button-hover-effect.gif)

<a href="http://codepen.io/lzl124631x/pen/VvJKgW" class="demo-link">Demo</a>

其实就是利用`:before`绘制了半透明白色的遮罩, 平时用`transform: scale(0, 1)`隐藏掉, `:active`时恢复到`transform: scale(1, 1)`.

另外学到的一点就是按键按下时的缩小效果. 之前自己还在想如何确保缩小之后也是水平竖直居中的, 但是这个demo的实现方法很简单: `transform: scale(0.95)`. 很好用. 看来需要多利用`transform`和想象力!