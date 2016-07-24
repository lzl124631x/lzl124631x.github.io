---
layout: post
title: autocapticalize和autocorrect
date:   July 2, 2016 2:30 PM
tags: css
---

# 首字母自动大写`autocapitalize`

在 iOS 中，用户可以手动开启「首字母自动大写」功能，这样输入英文的时候，首字母便会自动大写。但是，有些时候并不希望一直是首字母大写的。比如用户名这个字段，如果字段本身就是区分大小写的，首字母自动大写往往会给用户带来麻烦。可以通过在表单元素上可以通过设置`autocapitalize="off"`来关闭。

```html
<input type="text" autocapitalize="off" placeholder="用户名-关闭首字母大写">
```

还可以在`form`元素上设置该属性，让里面的每个元素都继承`autocapitalize`的设置。

```html
<form autocapitalize="off">
  <input type="text"  placeholder="继承 form 的设置">
  <textarea></textarea>
</form>
```

iOS 5 开始新增了很多关键字：

`none`等同于`off`。

当设置了`autocapitalize="words"`时，每个单词的开头字母会自动大写。

当设置了`autocapitalize="characters"`时，每个字母都会大写。

当设置了`autocapitalize="sentences"`时，每句开头字母会自动大写。

这里有一个综合示例

```html
<form autocapitalize="words">
  名字: <input name="first-name">
  姓氏: <input name="last-name">
  国家: <input name="state" autocapitalize="characters">
  昵称: <input name="username" autocapitalize="off">
  评论: <textarea name="comment" autocapitalize="sentences"></textarea>
</form>
```

需要注意几点：

`<input type="password" >`始终不会开启自动首字母大写。

如果在设置中没有开启「首字母大写」，即使设定`autocapitalize="on"`也不会有作用。 (设置方法: 在设置→通用→键盘中开启「首字母自动大写」)

如果「自动改正」和「首字母大写」都启用了，那么提示的字母也是首字母大写的。

越狱后安装的中文输入法里自带的英文输入模式可能会不支持「首字母自动大写」功能。

在删除后，再次输入依然是大写的，这一点体验比黑莓好。

# 自动改正`autocorrect`

这是一个布尔属性，可以设置`off`来关闭，同样可以设置在`form`元素上

```html
<input type="text" autocorrect="off" placeholder="用户名-关闭自动改正">
```

# 参考

[为移动而生的 HTML 属性autocapitalize和autocorrect](http://www.oicqzone.com/pc/2015033021322.html)