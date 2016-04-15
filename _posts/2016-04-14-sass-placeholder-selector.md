---
layout: post
title:  Sass占位符选择器`%`
date:   April 14, 2016 10:44 PM
tags: Sass
---

摘录自[Understanding placeholder selectors](http://thesassway.com/intermediate/understanding-placeholder-selectors).

# `@extend`

`@extend`让你能够在多个选择器中通过继承的方式共享一段样式:

```scss
.icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
}

.error-icon {
  @extend .icon;
  /* error specific styles... */
}

.info-icon {
  @extend .icon;
  /* info specific styles... */
}
```

会被转化为:<!-- more -->

```css
.icon, .error-icon, .info-icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
}

.error-icon {
  /* error specific styles... */
}

.info-icon {
  /* info specific styles... */
}
```

有个问题就是, 如果你不可能用到`icon`这个类呢?

# 占位符选择器`%`

占位符选择器(Placeholder Selector)是以`%`而不是`.`作为开始符的选择器. 它自身不会出现在编译后的CSS文件中, 只会出现在`@extend`了它的那些选择器中.

上例中, 用`%icon`替换`.icon`会得到:

```css
.error-icon, .info-icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
}

.error-icon {
  /* error specific styles... */
}

.info-icon {
  /* info specific styles... */
}
```

CSS中不会出现`.icon`类!

# `@extend` vs. `@include`

乍看下, `%`选择器和无参数mixin差不多, 在浏览器中产生的效果是一样的, 但是编译后的CSS有很大不同.

```scss
@mixin icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
}

.error-icon {
  @include icon;
  /* error specific styles... */
}

.info-icon {
  @include icon;
  /* info specific styles... */
}
```

以上代码会编译成

```scss
.error-icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
  /* error specific styles... */
}

.info-icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
  /* info specific styles... */
}
```

可以看到, `.error-icon`和`.info-icon`中继承自mixin`icon`的部分重复了两次, 有冗余. 但是用`%`选择器配合`@extend`就不会有这样的问题.

# `@extend`的限制

`@extend`有个限制, 就是你不能`@extend`不同`@media`块中的样式. 这个限制同样对`%`选择器有效.

```scss
%icon {
  transition: background-color ease .2s;
  margin: 0 .5em;
}

@media screen {
  .error-icon {
    @extend %icon;
  }
  
  .info-icon {
    @extend %icon;
  }
}
```

这会导致编译错误:

```
You may not @extend an outer selector from within @media.
You may only @extend selectors within the same directive.
From "@extend %icon" on line 8 of icons.scss
```

这是由于`@extend`的实现方式其实是用调用`@extend`的类替换被`@extend`的类, 上例中即用`.error-icon, .info-icon`替换`%icon`. 但是由于这些调用`@extend`的类属于`@media`块, 这样直接替换会导致替换后的规则脱离`@media`块, 因此是非法的.

但是, 反过来就没事儿. 因为`%icon`属性原本就是在`@media`内部生效的, `.error-icon, .info-icon`继承来的这部分规则自然也只应该在该`@media`下生效.

```scss
@media screen {
  %icon {
    transition: background-color ease .2s;
    margin: 0 .5em;
  }
}

.error-icon {
  @extend %icon;
  background-color: red;
}

.info-icon {
  @extend %icon;
  background-color: green;
}
```
会被编译成

```css
@media screen {
  .error-icon, .info-icon {
    transition: background-color ease .2s;
    margin: 0 .5em;
  }
}

.error-icon {
  background-color: red;
}

.info-icon {
  background-color: green;
}
```