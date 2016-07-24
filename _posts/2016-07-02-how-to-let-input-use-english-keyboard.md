---
layout: post
title: 如何让输入框自动使用英文输入法?
date:   July 2, 2016 2:30 PM
tags: css
---

使用`input[type="email"]`或`input[type="url"]`.

它们对应的键盘还有微小差别:

Email键盘:

![Email Keyboard](/images/2016-07-02-how-to-let-input-use-english-keyboard/email.png)

URL键盘:

![URL Keyboard](/images/2016-07-02-how-to-let-input-use-english-keyboard/url.png)

# 其他参数

此外, 你还可以设置:

* `autocorrect="off"`关闭"自动更正"功能
* `autocapitalize="off"`关闭"首字母自动大写"功能.

详见[autocapticalize和autocorrect](/2016/07/02/autocapitalize-autocorrect.html)

# 参考

[WAP网页输入框的默认键盘类型控制](http://www.cnblogs.com/txw1958/p/3231947.html)