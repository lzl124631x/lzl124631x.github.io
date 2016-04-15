微信JSSDK与录音相关的bug

最近一直在做微信JSSDK与录音相关的功能开发, 遇到了各种奇尺大坑, 各种冷不丁地被坑一道. 大有一种让我想狂骂"微信JSSDK就是一坨shit"的想法.

现在工作得到阶段性成果, 故总结一下这段时间来碰到的微信JSSDK在录音方面的bug, 希望做个前车之鉴, 大家谨慎入坑.

# checkJsApi

功能: 判断当前客户端版本是否支持指定JS接口

但是我遇到的一个不大不小的坑是: `onVoiceRecordEnd`和`onVoicePlayEnd`总是返回`false`, 即使这两个接口是被支持的!

还有要注意的是, 这个API只是检查当前客户端版本是否**支持**该API, 与该API是否开启没有卵关系. 所以当你check某个API发现是`true`, 但是又怎么调用都不起作用的时候, 记得检查下`wx.config`中的`jsApiList`参数.

# startRecord, stopRecord

还有一个`onVoiceRecordEnd`稍后再说.

微信JSSDK的API都有一个大问题, 就是如果调用时间间隔过短, 就非常可能调用失败. 但是失败也就算了, **最!可!恨!的!是!不会触发`success`, `fail`或`complete`中的任何一个callback, 导致UI卡住!**

打比方说, 你做一个"按下录音, 抬手停止录音"的功能, 如果用户点击了一下录音按钮, 相当于快速地`startRecord`然后`stopRecord`, 那么`stopRecord`是极有可能失败的, 而且任何callback不会执行.

然而问题还不止这个, 微信JSSDK的调用是异步的, 也就是你调用`startRecord`和`startRecord`的`success`的callback被调用是有时间间隔的. 这意味着, 用户点击按钮可能会造成: 虽然是先调用`startRecord`再调用`stopRecord`, 但是可能`stopRecord`先于`startRecord`调用成功!

我做了各种尝试, 微信JSSDK太脆弱, 频繁操作就会玩儿坏. 所以我最终的结论是: ==一定要在微信JSSDK外面包一层, 控制API的调用频率.==

以录音为例, 我曾经的解决方案见[这里](http://www.cnblogs.com/7z7chn/p/4973596.html), 但是后来发现频繁地调用`stopRecord`还是会有问题.

最靠谱的方法还是用状态机来做. 我现在的解决方案是: 当用户按下录音时调用`startRecord`, 然后一秒之内抬手都会提示用户"录音太短", 然后在第一秒结束时再调用`stopRecord`, 这样可以确保两个API之间的调用间隔至少一秒, 不会崩掉.

![States](D:\Project\lzl124631x.github.io\images\states.png)

## WinPhone上无法录音

通过WinPhone访问微信官方测试页面<http://demo.open.weixin.qq.com/jssdk>尝试调用录音接口, 然而没卵用!!!! (WinPhone用户再次受到一万点伤害)

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

# iPhone上录音后播放audio声音变得特别小

微信JSSDK在iPhone上有个bug: 录音之后, 播放声音(如audio, video)都会通过**听筒**(earphone. 如果没插耳机, 就是通过iPhone上部的听筒; 否则就是通过耳机), 而不是**外放**(speaker).

如果你之前一直在用外放听audio/video, 那么录音之后你会突然感觉audio/video的声音变小了很多. 但是如果你用耳机的话则不会受到影响.

恶心的BUG... 万恶的JSSDK...

后来发现`playRecord`一次后audio/video的声音就会正常地从外放里播放出来了...

所以, 有一个丑, 但是管用的workaround... 就是, 每次`stopRecord`成功后之后立即调用`playVoice`和`stopVoice`.


#
