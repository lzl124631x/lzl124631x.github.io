---
layout: post
title:  有关checkbox的checked属性和change事件
date: July 18, 2016 8:24 PM
tags: JavaScript
---

# jquery中的`attr`和`prop`有什么区别?

> To retrieve and change DOM properties such as the checked, selected, or disabled state of form elements, use the .prop() method.

根据官方的建议：具有 `true` 和 `false` 两个属性的属性，如 `checked`, `selected` 或者 `disabled` 使用`prop()`，其他的使用 `attr()`.

设置`check`等属性的时候也应该用`prop`方法.

# 利用`prop()`修改`checked`后如何触发`change`事件?

`prop`本身不自动触发`change`事件, 需要手动触发.

法1:

```js
$elem.prop('checked', true).change(); // i.e. trigger('change');
```

法2:

```js
$.propHooks.checked = {
  set: function(elem, value, name) {
    var ret = (elem[ name ] = value);
    $(elem).trigger("change");
    return ret;
  }
};
```

# knockout中如何在`checked`改变时自动触发`change`?

knockout中的`checked`事件不自动触发`change`事件, 可以自定义事件绑定.

```js
ko.bindingHandlers.checkedChange = {
    update: function (elem, val) {
        $(elem).prop('checked', ko.unwrap(val())).change();
    }
};
```

# 参考

1. [jQuery 中 attr() 和 prop() 方法的区别](http://wenzhixin.net.cn/2013/05/24/jquery_attr_prop)

2. [Should $('#chk').prop('checked', true) trigger change event?](https://forum.jquery.com/topic/should-chk-prop-checked-true-trigger-change-event)