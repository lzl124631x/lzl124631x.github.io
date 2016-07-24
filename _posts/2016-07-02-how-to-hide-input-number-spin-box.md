---
layout: post
title: 如何隐藏数字输入框的上下箭头?
date:   July 2, 2016 2:30 PM
tags: css
---

# 方案一:

在Chrome下:

```css
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none !important;
  margin: 0; 
}
```

Firefox下:

```css
input[type="number"] {
  -moz-appearance: textfield;
}
```

# 方案二:

将`type="number"`改为`type="tel"`, 同样是数字键盘, 但是没有箭头.

# 参考:

[Stack Overflow: Can I hide the HTML5 number input’s spin box?](http://stackoverflow.com/questions/3790935/can-i-hide-the-html5-number-input-s-spin-box)
[移除HTML5 input在type="number"时的上下小箭头](http://my.oschina.net/qii/blog/341439)