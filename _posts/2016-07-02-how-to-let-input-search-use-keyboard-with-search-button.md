---
layout: post
title: 如何让搜索框的键盘显示搜索按键?
date:   July 2, 2016 2:30 PM
tags: css
---

在`input[type="search"]`的外面套一层`form`, 其中`form`必须有`action`属性.

例如:

```html
<form action="">
  <input type="search" name="search" placeholder="search">
</form>
```

# 参考
[[网页设计]H5 input type=search 不显示搜索 解决方法](http://www.shaoqun.com/a/141396.aspx)
[怎么设置h5移动端的搜索键盘？](https://segmentfault.com/q/1010000002720680)