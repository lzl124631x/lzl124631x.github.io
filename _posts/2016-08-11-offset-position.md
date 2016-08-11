---
layout: post
title:  区分jquery中的offset和position
date: August 11, 2016 2:02 PM
tags: JavaScript
---

一次又一次地碰到需要获取元素位置的问题, 然后一次又一次地查`offset`和`position`的区别. 忍不了了, 这次一定得想办法记下来.

`position`是元素相对于父元素的位置. 这个好记, `parent`和`position`的首字母都是P.

`offset`是元素相对于当前视口(viewport)的位置. 想到的记忆方法比较牵强, `offset`谐音`off-site`, 经常指代公司组织员工一起出去玩儿, 看风景 > `view` > `viewport`.