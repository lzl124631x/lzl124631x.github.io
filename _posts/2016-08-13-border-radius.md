---
layout: post
title: "border-radius的水平和竖直半径"
date: August 13, 2016 2:50 PM
tags: css
---

通常我们设置`border-radius`都只区分四个角的, 如`border-radius: 1em 2em`.

其实每个角的`border-radius`都由两部分组成, 水平半径和竖直半径.

![](/images/2016-08-13-border-radius/corner.png)

要设置水平和竖直半径, 用`border-top-left-radius`设置时应该写成`border-top-left-radius: 1em 2em`, 但是用`border-radius`设置时则应该写成`border-radius: 1em / 2em`.

`_(:3」∠)_`

如果懵逼了请往下看.

# `border-*-radius`

当你直接设置某个角的`border-radius`时, 即设置`border-top-left-radius`, `border-top-right-radius`, `border-bottom-right-radius`, `border-bottom-left-radius`中的一个时, 赋值的形式应为`[ <length> | <percentage> ]{1,2}`.

* 第一个值为水平半径, 第二个值为竖直半径.
* 如果未给出第二个值, 则竖直半径等于水平半径.
* **任意一个值为0, 则该角为直角.**
* 若值为百分比, 则水平半径相对于边框盒(border box)的宽度, 竖直半径相对于边框盒的高度.

举例:

`border-top-left-radius: 5em 40%`意为, 该元素的左上角边框的水平半径为`5em`, 竖直半径为边框盒高度的`40%`.

<div style="border-top-left-radius: 5em 40%; width: 10em; height: 3em; border: .5em solid #aaa; background-color: #f4f4f4; margin: 0 auto;"></div>

# `border-radius`

赋值形式应为`[ <length> | <percentage> ]{1,4} [ / [ <length> | <percentage> ]{1,4} ]?`.

* 如果`/`前后都有值, 则`/`之前的值设置水平半径, 之后的值设定竖直半径.
* 如果没有`/`, 则所赋值同时设置水平和竖直半径.
* 赋值顺序为"左上", "右上", "右下", "左下".
* **取对角值的情况**: 若"左下"未赋值则取"右上"的值; 若"右下"未赋值则取"左上"的值
* **只赋了一个值的情况**: 如果"右上"未赋值则取"左上"的值.

举例:

```css
border-radius: 1em/5em;

/* 相当于 */

border-top-left-radius:     1em 5em;
border-top-right-radius:    1em 5em;
border-bottom-right-radius: 1em 5em;
border-bottom-left-radius:  1em 5em;
```

```css
border-radius: 4px 3px 6px / 2px 4px;

/* 相当于 */

border-top-left-radius:     4px 2px;
border-top-right-radius:    3px 4px;
border-bottom-right-radius: 6px 2px;
border-bottom-left-radius:  3px 4px;
```

利用这些知识, 可以画出四分之一圆或半椭圆.

<style>
.example-item {
width: 10em;
height: 10em;
background-color: #8bc34a;
margin: 1em auto;
}

#quarter {
border-radius: 100% 0 0;
}

#half-left {
border-radius: 100% 0 0 100% / 50%;
}
</style>
<div id="quarter" class="example-item"></div>
<div id="half-left" class="example-item"></div>


# Chrome上的半椭圆问题

在做半椭圆的例子的时候发现了一个Chrome上的异常行为.

如果写成`border-radius: 100% 0 0 100% / 50%`, 则可以正常绘制半椭圆.

如果写成`border-radius: 100% / 50% 0 0 50%`, 绘制出来的却不是半椭圆.

效果图如下:

![](/images/2016-08-13-border-radius/chrome-screenshot.png)

但是按理说它们都应该相当于"左上左下为`100% 50%`, 右上右下为`0`, 即直角".

Chrome中可以看到这两种写法的计算值分别为:

```css
/* 正常半椭圆 */
border-bottom-left-radius:100% 50%;
border-bottom-right-radius:0px 50%;
border-top-left-radius:100% 50%;
border-top-right-radius:0px 50%;
```

```css
/* 错误半椭圆 */
border-bottom-left-radius:100% 50%;
border-bottom-right-radius:100% 0px;
border-top-left-radius:100% 50%;
border-top-right-radius:100% 0px;
```

试验了下, 在IE11和Safari中看到的都是正常的, 但是在Chrome和Firefox中看到的都是这种异常情况.

以下, 上面是`border-radius: 100% 0 0 100% / 50%`写法, 下面是`border-radius: 100% / 50% 0 0 50%`, 可以看看你的浏览器是否区别对待了.

<style>
#half-left2 {
border-radius: 100% / 50% 0 0 50%;
}
</style>
<div id="half-left" class="example-item"></div>
<div id="half-left2" class="example-item"></div>

不清楚这是一个Chromium的bug还是有意为之. 至少根据W3C的标准来看, 应该是个bug, 已经提issue到[chromium](https://bugs.chromium.org/p/chromium/issues/detail?id=637524).

# 参考

1. [用css3绘制你需要的几何图形](http://www.cnblogs.com/wdlhao/p/5751211.html)
1. [border-radius - CSS | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/border-radius)
1. [CSS Backgrounds and Borders Module Level 3 | W3C](https://www.w3.org/TR/css3-background/#border-radius)