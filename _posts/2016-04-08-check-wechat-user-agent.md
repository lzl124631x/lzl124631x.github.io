---
layout: post
title:  "判断微信内置浏览器的UserAgent"
date: April 8, 2016 11:23 PM
categories: blog
---

要区分用户是通过"微信内置浏览器"还是"原生浏览器"打开的WebApp, 可以通过`navigator.userAgent`来进行判断.

以下是对各种平台上微信内置浏览器和原生浏览器UA的对比

---

PC版微信:

```
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36 MicroMessenger/6.5.2.501 NetType/WIFI WindowsWechat
```

PC Chrome:

```
Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.110 Safari/537.36
```

---

iPhone版微信:

```
Mozilla/5.0 (iPhone; CPU iPhone OS 9_3 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Mobile/13E233 MicroMessenger/6.3.15 NetType/WIFI Language/zh_CN
```

iPhone Safari:

```
Mozilla/5.0 (iPhone; CPU iPhone OS 9_3 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13E233 Safari/601.1
```

---

Android版微信:

```
Mozilla/5.0 (Linux; Android 5.0.1; GT-I9502 Build/LRX22C; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/43.0.2357.121 Mobile Safari/537.36 MicroMessenger/6.1.0.78_r1129455.543 NetType/WIFI
```

Android (Samsung S4) 默认浏览器:

```
Mozilla/5.0 (Linux; Android 5.0.1; SAMSUNG GT-I9502 Build/LRX22C) AppleWebKit/537.36 (KHTML, like Gecko) SamsungBrowser/2.1 Chrome/34.0.1847.76 Mobile Safari/537.36
```

---

注: Mi4刷的WinPhone系统...

WinPhone版微信:

```
Mozilla/5.0 (Windows Phone 8.1; ARM; Trident/8.0; Touch; rv:11.0; WebBrowser/8.1; IEMobile/11.0; XIAOMITEST; MI4) like Gecko false
```

WinPhone 默认浏览器:

```
Mozilla.5.0 (Windows Phone 10.0; Android 4.2.1; XIAOMITEST; MI4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2486.0 Mobile Safari/537.36 Edge/13.10586 false
```

---

综上, 在iPhone和Android上, 可以用`micromessenger`来判断, 而在WinPhone上, 可以用`webbrowser`来判断.

```js
var isInWeChat = /(micromessenger|webbrowser)/.test(navigator.userAgent.toLocaleLowerCase());
```

如果你感兴趣的话, 可以通过打开 <http://whatsmyuseragent.com/> 查看你的UA.

参考: [微信内置浏览器UserAgent的判断](https://dearb.me/archive/2013-10-30/weixin-browser-user-agent/)