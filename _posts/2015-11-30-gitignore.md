---
layout: post
title:  "文件已经加入.gitignore但是vs并没有显示文件处于ignore状态"
date:   2015-11-30 00:00:00 +0800
categories: git
---

在VS2015的项目文件中看到某些文件的状态比较特殊, 前面被标记了红色的标志, 如下图.

![](/images/2015-11-30-gitignore.png)

本来以为这是通过VS修改文件属性做到的, 但是光标移到文件上发现显示的是Ignore, 才知道是被git所忽略的文件.

我将某个文件, 记作a.js加入到.gitignore中后, checkin了.gitignore, 但是发现a.js前面的标志还是锁, 不是红色的图标. [Stackoverflow - .gitignore not working](http://stackoverflow.com/questions/11451535/gitignore-not-working)中告诉了我怎么做:

```
git rm -r --cached . // 注意这个点
git add .
git commit -m "fixed untracked files"
```

其中, `-r`是递归操作, 用于删除文件夹内部的文件; `--cached`的作用是将已经被track的文件从git中删除, 不再track.

[Git ignore](https://git-scm.com/docs/gitignore):
> The purpose of gitignore files is to ensure that certain files not tracked by Git remain untracked.
> To stop tracking a file that is currently tracked, use git rm --cached.