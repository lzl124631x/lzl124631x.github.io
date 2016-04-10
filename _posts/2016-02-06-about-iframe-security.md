---
layout: post
title:  "iframe的安全问题"
date:   2016-02-06 00:00:00 +0800
tags: HTML
---

今天尝试在`iframe`中嵌入外部网站, 碰到了一些小问题.

# 如何让自己的网站不被其他网站的`iframe`引用?
我测试的时候发现我把`iframe`的`src`指定到github不起作用. 原来是它把`X-Frame-Options`设置为了`DENY`, 这样就禁用了别的网站的`iframe`引用, 避免[点击劫持(clickjacking)](https://en.wikipedia.org/wiki/Clickjacking).

`X-Frame-Options`有三个可能值: `DENY`, `SAMEORIGIN`, `ALLOW-FROM uri`.

详见: [The X-Frame-Options response header](https://developer.mozilla.org/en-US/docs/Web/HTTP/X-Frame-Options)

# 如何禁用自己`iframe`中链接的外部网站的JS?
我发现我把一个外部网站引入到我的`iframe`中时, 该网站的恶心JS会让我的网页跳转到它的网站! 蛋疼. 但是我在测试的是否发现, Codepen就能够避免这个跳转, Console中有信息:

```
Unsafe JavaScript attempt to initiate navigation for frame with URL 'http://codepen.io/lzl124631x/pen/jWeOPL' from frame with URL 'http://keepvid.com/?url=https://www.youtube.com/watch?v=wY2vhSb3cVo'. The frame attempting navigation of the top-level window is sandboxed, but the 'allow-top-navigation' flag is not set.
(anonymous function) @ ?url=https://www.youtube.com/watch?v=wY2vhSb3cVo:108
?url=https://www.youtube.com/watch?v=wY2vhSb3cVo:579 GET http://www.5kplayer.com/kvads/keepvid-728-90.png net::ERR_BLOCKED_BY_CLIENT
```

原来是`iframe`的属性`sandbox`的作用.

`sandbox`属性可以设置的值包括:
`allow-forms`
`allow-modals`
`allow-orientation-lock`
`allow-pointer-lock`
`allow-popups`
`allow-popups-to-escape-sandbox`
`allow-same-origin`
`allow-scripts`
`allow-top-navigation`

 如果给`iframe`加上`sandbox="allow-forms allow-scripts"`, 就会打开`allow-forms`和`allow-scripts`两个选项, 其他的全部禁用.

如果想禁用让外部网站的JS, 不要加`allow-scripts`就好了.

如果想禁用外部网站的JS跳转, 不要加`allow-top-navigation`就可以了.

详见: [&lt;iframe&gt; Sandbox](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#attr-sandbox)