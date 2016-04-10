---
layout: post
title:  "设置VS2015上关闭和打开tab快捷键"
date:   2015-11-17 00:00:00 +0800
tags: VisualStudio
---


# `Ctrl+W`关闭Tab:

Tools > Options > Environment > Keyboard > File.Close > Use new shortcut in: Text Editor, `Ctrl+W`.

# `Ctrl+Shift+T` 打开最近关闭的Tab:

1. 下载Productivity Power Tools 2015: Tools > Extensions and Updates > Online > Search "Productivity Power Tools 2015" and Download.
2. 设置`快捷键Ctrl+Shift+T`: Tools > Options > Environment > Keyboard > Edit.UndoClose > Use new shortcut in: Text Editor, `Ctrl+Shift+T`.

Ref: [Re-open closed tab (undo close) in Visual Studio 2013?](http://stackoverflow.com/questions/19864290/re-open-closed-tab-undo-close-in-visual-studio-2013)

---

# 禁止Productivity Power Tools修改Tab颜色
	安装完Productivity Power Tools之后我发现Tab的颜色变了, 感觉难看想改回去:

Tools->Options->Productivity Power Tools->Custom Document Well->Color Coding.

Ref: [Random tabs color in Productivity power tools](http://stackoverflow.com/questions/30886209/random-tabs-color-in-productivity-power-tools)