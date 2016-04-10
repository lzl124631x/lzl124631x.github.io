---
layout: post
title:  "如何让windows版Safari支持H5 audio/video?"
date:   2015-11-25 00:00:00 +0800
tags: FrontEnd
---

今天在windows版Safari上看效果的时候惊奇地发现它竟然不支持HTML5的audio/video, 这样的话就无法复现不少ios上出现的问题. 在同事提醒下, 发现[Safari HTML5 Audio and Video Guide - iOS-Specific Considerations](https://developer.apple.com/library/safari/documentation/AudioVideo/Conceptual/Using_HTML5_Audio_Video/Device-SpecificConsiderations/Device-SpecificConsiderations.html#//apple_ref/doc/uid/TP40009523-CH5-SW1)中提到了:

> Safari on the desktop supports any media the installed version of QuickTime can play.

于是装了QuickTime. 装完之后的确支持audio/video了.