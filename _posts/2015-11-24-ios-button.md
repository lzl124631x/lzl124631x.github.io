---
layout: post
title:  "iOS设备下添加button的按下效果"
date:   2015-11-24 00:00:00 +0800
categories: FrontEnd
---

在触屏上, button的按下效果用`:active`触发, 但是我发现iOS设备上`:active`效果并没有生效. [:active pseudo-class doesn't work in mobile safari](http://stackoverflow.com/questions/3885018/active-pseudo-class-doesnt-work-in-mobile-safari)告诉我只要:

```html
<body ontouchstart="">
...
</body>
```

即可.

<span class="my-comment">其实这个问题以前搜过但是忘记了, 后来发现`body`上加了`ontouchstart=""`但就是想不起来为什么加了... 还好这次又碰到了.</span>

第二个问题是: 当点击iOS设备上的button的时候, 按键周围会有一个半透明的黑色框一闪而过. 想要去掉这个黑色框, 参考[iPad Safari: How to disable the quick blinking effect when a link has been hit](http://stackoverflow.com/questions/3516173/ipad-safari-how-to-disable-the-quick-blinking-effect-when-a-link-has-been-hit), 只需要给button加上属性`-webkit-tap-highlight-color: transparent`即可.