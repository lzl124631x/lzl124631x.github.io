---
layout: post
title:  "微信JSSDK与录音相关的坑"
date: April 15, 2016 23:00 PM
tags: WeChat
---

最近一直在做微信JSSDK与录音相关的功能开发, 遇到了各种奇尺大坑, 时不时冷不丁地被坑一道, 让我时常想嘶吼: "微信JSSDK就是个大腊鸡!!!!!!!!!!"

现在工作得到阶段性成果, 有时间休息总结下, 故来整理一下这段时间碰到的bug, 希望做个前车之鉴, 劝大家谨慎入坑.

# checkJsApi

功能: 判断当前客户端版本是否支持指定JS接口

我遇到的一个不大不小的坑是: `onVoiceRecordEnd`和`onVoicePlayEnd`总是返回`false`, 即使这两个接口是被支持的!

还有要注意的是, 这个API只是检查当前客户端版本是否**支持**该API, 与该API是否开启并没有卵关系! 所以当你check某个API发现是`true`, 但是又怎么调用都不起作用的时候, 记得检查下`wx.config`中的`jsApiList`参数.

# startRecord, stopRecord

微信JSSDK的API都有一个大问题, 就是如果调用时间间隔过短, 就非常可能产生无效调用. 无效调用的意思是, 虽然你调用了某个API, 但是相当于没调用, **它不会触发`success`, `fail`或`complete`中的任何一个callback!**

打比方说, 你做一个"按下录音, 抬手停止录音"的功能, 如果用户点击了一下录音按钮, 相当于快速地`startRecord`然后`stopRecord`, 那么`stopRecord`是极有可能是无效的, 不会执行任何callback.

然而问题还不止这个, 微信JSSDK的调用是异步的. 举例来说就是, 你调用`startRecord`的时间, 和`startRecord`的`success`的callback被执行的时间可能间隔了若干毫秒甚至秒. 这意味着, 用户点击按钮可能会造成: 虽然是先调用`startRecord`再调用`stopRecord`, 但是可能`stopRecord`先于`startRecord`调用成功!

我做了各种尝试后意识到, 微信JSSDK太脆弱, 频繁操作就会被玩儿坏. 所以我最终的结论是: ==一定要在微信JSSDK外面包一层, 控制API的调用频率.==

以录音为例, (我曾经的解决方案见[这里](http://www.cnblogs.com/7z7chn/p/4973596.html), 但是后来发现频繁地调用`stopRecord`还是会有问题) 我现在的解决方案是: 当用户按下录音时调用`startRecord`, 然后一秒之内抬手都会提示用户"录音太短", 然后在第一秒结束时再调用`stopRecord`, 这样可以确保两个API之间的调用间隔至少一秒, 不会崩掉.

最靠谱的方法还是用状态机来做, 我目前的状态机如下图. 图中没有包含`uploadVoice`, `uploadVoice`期间应该不允许用户操作按钮.

![States](\images\2016-04-15-wechat-record-bugs.png)

## WinPhone上无法录音

通过WinPhone访问微信官方测试页面<http://demo.open.weixin.qq.com/jssdk>尝试调用录音接口, 然而并没有卵用!!!! (WinPhone用户再次受到一万点伤害)

好, 接下来只讨论iPhone和Android用户...

## iPhone中的假录音状态

机型: iPhone

症状: 微信显示"录音中", 但是其实没有在录音! 此时, `startRecord`会失败, 错误信息`startRecord:fail`; `stopRecord`不调用任何callback, 仍然显示录音中. 那就这么挂了? 不...

触发方式:

1. 录音中按Home返回桌面
2. 录音中从屏幕底部上划打开iPhone的设置菜单, 再关闭菜单.

解法: 此时你需要先调用`stopRecord`, 虽然不会触发任何callback, 但微信内部一定设置了某个flag, 然后就可以正常调用`startRecord`了.

呵呵... 一口鲜血喷向屏幕.

你无法知道是否进入了假录音状态, 所以这个问题暂时无解!

## 不`startRecord`, 直接`stopRecord`有什么效果?

我的测试结果是, 三个callback都不会被触发!

我觉得这应该算是一个bug, 如果没有在录音但是调用了`stopRecord`, 那应该失败并且`errMsg`为`NotRecording`.

## 录音中按Home返回桌面

iPhone: 录音中断, 回到微信进入**假录音状态**.

Android: 录音继续, 回到微信可以正常`stopRecord`.

## 录音中打开设置菜单

iPhone: 从屏幕底部上滑可以打开设置菜单, 关闭菜单后进入**假录音状态**.

Android: 从屏幕上部下滑可以打开设置菜单, 关闭菜单后可以正常停止录音.

## 录音中关闭WebApp

iPhone: 录音结束, 回到WebApp可以正常开启录音.

Android: 录音继续, 回到WebApp调用`startRecord`失败提示`recording`.

## 录音中刷新WebApp

iPhone: 微信不再显示"录音中", 但是实际上此时在录音... `startRecord`失败, 错误信息`startRecord:fail`; `stopRecord`会成功.

Android: 我没有刷新按钮... (Android用户起立鼓掌)

## iPhone上录音后播放audio声音变得特别小

这是一个微信JSSDK炒鸡恶心的BUG... 只在iPhone出现: 在你使用的是**外放**(就是声音是从手机下方的小音箱里面放出来的)的前提下, 录音之后, 播放audio/video声音会转而从**听筒**(就是不插耳机打电话是耳朵对着的位置)播放出来. 如果你不知道的话, 会以为录音之后播放audio/video声音变得特别小.

但是, 如果你用耳机的话则不会受到影响, 因为声音会始终从耳机里播放出来. (难道你要我告诉用户, "请带上耳机使用本产品"嘛?!)

后来我们发现`playVoice`一次之后, audio/video的声音就会, 神奇地, 又从外放里播放出来了... *无语凝噎.jpg*

所以, 有一个丑, 但是管用的workaround... 就是, 每次`stopRecord`成功后之后立即调用`playVoice`和`stopVoice`... 这样至少听筒模式的bug解了.

但是还有个问题是, 如果你播放的是用户刚刚录的音, 有可能播放录音时会发出"噗"的一声... 这是录音时录进去的杂音. 想要解决这个问题你就必须要上传一个空白的语音到微信, 然后在WebApp启动的时候通过serverId下载这个语音, 然后每次`stopRecord`的时候播放这个空白语音...

然后还有个问题就是, 你上传的这个语音**必须是临时素材**, 这样下载下来才会是localId, 可以用`playVoice`播放; 如果你上传语音作为用旧素材的话, 下载下来是二进制, 不能用`playVoice`播放...

然后还有个问题就是, 临时素材只会被微信保留三天... 三天后过期... 所以你必须每三天上传一段新的空白语音, 更新serverId...

*抱头痛哭.jpg*

## Android上无法实现长按录音的功能?

经过测试发现, (很多?)Android上无法实现长按录音, 为什么?

因为我发现, 只要调用了`startRecord`就会触发按钮的`touchcancel`事件. 于是, 之后的`touch`相关事件就不会再被监听了. (本人猜测, 这和下面的`uploadVoice`导致UI卡住是一个问题, 在Android上微信JSSDK的执行会卡住UI导致触发了按键的`touchcancel`事件)

这意味着即使你手还按在按键上, 浏览器已经认为你松手了; 等你真的松手的时候, 浏览器并没有在监听`touchend`事件了...

所以目前, 虽然是一套代码运行在iPhone和Android上, 但是iPhone上就是长按录音, 但是Android上需要用户点击开始(响应`touchstart`), 点击结束(响应`touchend`)...

事实上, 两次点击录音比长按录音更容易出bug, 因为第一次和第二次点击之间用户可以随便乱操作, 很容易出现各种问题; 但是长按期间, 用户乱操作的可能性会低得多.

(突然想到, 可不可以在Android系统上, 当调用`startRecord`后触发`touchcancel`时, 手动触发一下按键的`touchstart`事件, 这样让按钮能够监听`touchend`? 之后做做实验)

## 初次以及未知时间间隔之后, 提示用户"是否开启录音"

新用户进入WebApp后第一次调用`startRecord`的时候, 微信会弹出一个对话框, 询问用户"是否开启录音".

这对于"长按录音"操作来说, 非常影响体验, 因为用户按到一半需要松手去点对话框.

如果用户一不小心点了"否", 那你可以去哭了, 接下的录音API调用会一直失败.

如果用户点了"是", 接下来, 至少一段时间内, 用户可以安心地录音了, 不会弹出对话框.

但是, 恶心的是, 在不确定的(至少目前我还没找到规律)时间之后(比如4天之后)用户再次打开WebApp时微信可能会重新询问"是否开启录音"...

有一个不完美的解决方法是: 在刚刚打开WebApp的时候就尝试录音一下. 如果微信弹出对话框, 用户可以在这个时候点选是/否, 而不至于影响真正录音时的体验.

# 吐槽`onVoiceRecordEnd`和`onVoicePlayEnd`

先说`onVoiceRecordEnd`: 微信录音的最长时间是1分钟, 超过这个时间录音会自动停止. `onVoiceRecordEnd`的作用就是注册一个callback, 当录音超时的时候执行.

我想吐槽的是这个API的设计, 我觉得更合理的方式是应该在`startRecord`的时候就注册进去, 因为这个超时逻辑和`stopRecord`逻辑有很大程度上是重叠的, 比如错误处理, 重置录音按钮样式等, 而这些逻辑在`startRecord`的时候就是已知的了.

就是说这个API应该设计成这样:

```js
wx.startRecord({
  success: // ...
  fail: //...
  complete: //...
  onVoiceRecordEnd: //...
})
```

但是现在的`onVoiceRecordEnd`却是一个独立的API:

```js
wx.onVoiceRecordEnd({
    // 录音时间超过一分钟没有停止的时候会执行 complete 回调
    complete: function (res) {
        var localId = res.localId;
    }
});
```

微信是觉得我的WebApp里面只会有一种`onVoiceRecordEnd`逻辑么? **事实上同一个WebApp的不同页面很可能有不同的`onVoiceRecordEnd`逻辑.**

所以我现在只能在`startRecord`的技术上由封装了一层, 当`startRecord`成功的时候将当前的`onVoiceRecordEnd`逻辑注入到`wx.onVoiceRecordEnd`中.

`onVoicePlayEnd`的作用是注册一个callback, 当用户录音播放结束时执行. 它和`onVoiceRecordEnd`非常类似, 也是一个被处理出来的API. 它应该在`playVoice`的时候就被注册.

# `uploadVoice`

录音结束后, 你是无法直接访问录音数据的, 需要先调用`uploadVoice`将数据上传到微信服务器, 然后让你的后台再从微信服务器下载数据... 这个设计直接导致了处理录音数据时间的增加.

微信这么做, 应该是希望保护用户隐私, 但是如果能提供一个新的授权方式(目前只支持两种授权方式, `snsapi_base`和`snsapi_userinfo`), 在用户允许录音并且可以直接上传语音到对方服务器, 那就太好了.

## Android上, 上传/下载录音时UI会卡住

`uploadVoice`提供了一个选项`isShowProgressTips`, 默认为1, 表示显示进度提示. 吐槽1: 但是并没有说不显示进度应该设置几, 虽然0是常用的falsy. 文档不细致, 还要人去试. 吐槽2: 这变量名起的... 我觉得语法有问题, `showProgressTip`就行了.

这都不重要, 恶心的是: 在Android上, 即使设置了`isShowProgressTips`为0, 即不显示进度提示, UI依然会被卡住不动, 待上传完之后UI才能继续正常运行. 这个问题在iPhone上就没有.

所以, 比较好的解决方法是, 如果是iPhone则根据需要设置`isShowProgressTips`, 但Android上最好设置`isShowProgressTips`为1.

# `playVoice`, `stopVoice`

这对接口有着和`startRecord`和`stopRecord`一样的问题--调用不能过于频繁否则会玩儿坏微信, 需要在微信的接口上再封装一层以控制调用频率.

假设你录制了多个语音, 在播放其中一个的时候, 播放另一个会产生不确定的结果. 有的时候第二个录音正常播放, 但是有的时候第二个播放不出来. 当你连续播放三个乃至更多的音频的时候问题更多.

还有一个问题就是`onVoicePlayEnd`在这种情况下也是不稳定的, 连续播放N个音频时, `onVoicePlayEnd`是不一定触发的...

即使你每次播放录音B之前调用`stopVoice`停掉正在播放的录音A, 这行为也是不可靠的... 有时候能停下来, 有时候会直接播放录音B的后半截, 并且随后的播放录音行为会乱掉.

这些恶心的bug意味着你必须要**限制UI上的操作频率和顺序**(比方说当播放一个录音的时候, 其他播放录音按钮是被禁用的), 也就无法创造一个操作流畅(比如随便点击某个录音按钮就可以停掉正在播放的录音而播放新的录音)的用户体验了.

## `stopVoice`的小bug

这个bug跟其他bug比起来可以忽略, 但是仍然蛮影响用户体验, 就是: 在录音播放的最后几秒钟调用`stopVoice`一次, 会显示`ok`说明停止录音成功, **但是声音仍然在播放**, 继续调用`stopVoice`会失败, 错误信息为`not playing`.

(这位处女座请把你手里的板砖放下, 谢谢.)

# 结语

总之, 微信JSSDK还只是提供了基本功能, 但是**健壮性远不足以支撑丰富交互的WebApp**. 这种基于微信JSSDK开发的WebApp会有诸多限制, 远无法达到NativeApp的效果.

但是, 基于微信的WebApp有着**易访问, 易传播**的巨大优势, 可以作为NativeApp开发之前的一个试点项目. 最终为了确保良好的用户体验, NativeApp还是必不可少的.

