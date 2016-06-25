---
layout: post
title:  "关于JS加载优化"
date: June 25, 2016 11:12 PM
tags: JavaScript
---

# HEAD中的脚本

最初, 我们这么引入脚本

```html
<head>
<script src=”core.js” type=”text/javascript”></script>
<script src=”more.js” type=”text/javascript”></script>
</head>
```

这有个问题是, 古老的浏览器(现在主要是指IE 6 & 7)会**顺序**加载这两个JS, 即先下载并执行core.js, 然后下载并执行more.js.

除此之外, 旧的浏览器还会在JS全部顺序加载完之后才开始加载其他的资源, 如样式表, 图片等.

从IE 8, Firefox 3.6, Chrome 2和Safari 4开始, 这些新的浏览器基本上都会并行加载JS以及其他资源, 但是它们对并行加载的支持不尽相同. 举个栗子, 以IE 8和9 beta, 加载JS的过程中不能加载图片; Firefox 3对于那些通过`document.write`写入页面的JS会**顺序而非并行**加载.

更严重的问题是, 任何在`<script>`标签后面的DOM元素都要等到JS加载完成之后才会开始渲染, 即使浏览器已经将HTML文件完整地下载下来了. 如果你把`<script>`放在`<head>`里面, 那么整个页面的渲染都会被阻塞.

# 把脚本移到底部

回到


# 参考

1. [Evolution of Script Loading](http://www.stevesouders.com/blog/2010/12/06/evolution-of-script-loading/)