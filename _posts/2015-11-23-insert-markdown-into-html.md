---
layout: post
title:  "在html中嵌入markdown"
date:   2015-11-23 00:00:00 +0800
categories: Markdown
---


在博客园网页里写markdown的时候, 某些特殊内容想加上自定义的css, 于是用`<div class="xxx">`包裹起来, 但是发现该`<div>`中的markdown就不再被解析.

[Python Markdown: Markdown Inside HTML Blocks](http://stackoverflow.com/questions/1705895/python-markdown-markdown-inside-html-blocks)告诉我**应该用`<span>`而非`<div>`**.

解释见[Markdown: Syntax - Inline HTML](http://daringfireball.net/projects/markdown/syntax#html). 需要注意的有, 其中第2和第4点解释了上面所说的问题.

1. The only restrictions are that block-level HTML elements — e.g. `<div>`, `<table>`, `<pre>`, `<p>`, etc. — must be separated from surrounding content by blank lines, and the start and end tags of the block should not be indented with tabs or spaces. Markdown is smart enough not to add extra (unwanted) `<p>` tags around HTML block-level tags.
2. Note that Markdown formatting syntax is **not** processed within block-level HTML tags. E.g., you can’t use Markdown-style `*emphasis*` inside an HTML block.
3. Span-level HTML tags — e.g. `<span>`, `<cite>`, or `<del>` — can be used anywhere in a Markdown paragraph, list item, or header. If you want, you can even use HTML tags instead of Markdown formatting; e.g. if you’d prefer to use HTML `<a>` or `<img>` tags instead of Markdown’s link or image syntax, go right ahead.
4. Unlike block-level HTML tags, Markdown syntax **is** processed within span-level tags.

<span class="my-comment">我就是一段被加上**自定义**`css`的*Markdown*!</span>