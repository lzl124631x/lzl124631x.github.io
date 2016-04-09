---
layout: post
title:  "Knockout绑定audio的pause事件导致音频无法停止"
date:   2015-11-17 00:00:00 +0800
categories: OJ
---


...时间过得真快, 一晃4天已经过去了, 然而自己并没有动笔写什么. 自省.

看了看今天的工作, 感觉好像没什么可写的. 不禁在想是不是一天一篇有点儿难. 再一想, 这分明就是在给自己找理由. 就是再短也应该把当天学到的任何东西整理一下, 怎么都是有的写的! 如果完全没得写, 那我倒是要反思下我今天是不是太虚度了.

废话不多说, 整理下这两天的收获. (最近一直写code但是很少记录, 提醒自己以后坚持记录!)

---

我发现我用knockout给audio元素绑定了`pause`事件, 形如`<audio data-bind="event: { pause: pauseHandler }"...`但是用`$('audio').trigger('pause')`无法暂停audio播放. 很诡异.

于是我为`audio`的`play`和`pause`事件都绑上了`console.log`, 发现`trigger('play')`会输出两次`play`而`trigger('pause')`只会输出一个`pause`. 但是如果用`.play()`和`.pause()`就可以正常地播放和停止音频, 同时只会输出一个`play`和`pause`.

# 为什么$('audio').trigger('play')会出发playHandler两次而$('audio')[0].play()只会触发一次?

搜索一阵无果就在[stackoverflow上问了一下](http://stackoverflow.com/questions/33670514/why-audio-triggerplay-fires-playhandler-twice-while-audio0-play), 得到的解答是:

> `trigger('play')` triggers your handler programmatically, which dispatches the play event on the audio element, once the clip starts playing, the play event is dispatched again, triggering your handler a second time, whereas `$('audio')[0].play()` won't trigger your handler initially, it'll trigger the dom element's native play event, which, when it starts playing will trigger your handler

但是至于能否用`trigger('play')`只触发一次`playHandler`, 并没有得到答复.

# 为什么用Knockout绑定pause事件后无法用`trigger('pause')`暂停音频?

自己摸索出来了答案, 只要给`pauseHandler`加上`return true;`即可. 但是不明白为什么必须要这么加, 于是[问了一记](https://stackoverflow.com/questions/33668117/why-failed-to-pause-audio-if-pause-event-is-registered-using-knockout), 自己才发现[spec](http://knockoutjs.com/documentation/event-binding.html#note-3-allowing-the-default-action)上写得清清楚楚: 

> By default, Knockout will prevent the event from taking any default action....However, if you do want to let the default action proceed, just return `true` from your event handler function.