---
layout: post
title: "如何设置table的border-radius?"
date:   July 3, 2016 11:01 PM
tags: css
---

遇到一个诡异的问题, 为`table`添加`border-radius`不起作用. 示例如下:

<style>
#table1 {
  border-collapse: collapse;
  border-radius: 1em;
  border: solid .3em #dfdfdf;
}
.centered {
  margin-left: auto;
  margin-right: auto;
}
</style>
<table id="table1" class="centered">
  <tr>
    <td>1</td>
    <td>2</td>
  </tr>
  <tr>
    <td>3</td>
    <td>4</td>
  </tr>
</table>

```css
#table1 {
  border-collapse: collapse;
  border-radius: 1em;
  border: solid .3em #dfdfdf;
}
```

`#table1`设置了`border-collapse: collapse`以消除单元格之间的空白, 但也正这个设置导致了`border-radius`的失效.

解决方法很简单, 使用`border-collapse: separate`同时配合`border-spacing: 0`即可. 这样既确保单元格之间没有空白, 同时`table`也有了`border-radius`.

<style>
#table2 {
  border-collapse: separate;
  border-spacing: 0;
  border-radius: 1em;
  border: solid .3em #dfdfdf;
}
</style>
<table id="table2" class="centered">
  <tr>
    <td>1</td>
    <td>2</td>
  </tr>
  <tr>
    <td>3</td>
    <td>4</td>
  </tr>
</table>

```css
#table2 {
  border-collapse: separate;
  border-spacing: 0;
  border-radius: 1em;
  border: solid .3em #dfdfdf;
}
```

# 参考

[CSS3's border-radius property and border-collapse:collapse don't mix. How can I use border-radius to create a collapsed table with rounded corners?](http://stackoverflow.com/questions/628301/css3s-border-radius-property-and-border-collapsecollapse-dont-mix-how-can-i)