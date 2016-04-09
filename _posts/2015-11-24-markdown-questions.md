---
layout: post
title:  "Markdown各种小问题汇总"
date:   2015-11-24 00:00:00 +0800
categories: Markdown
---

# 如何分割Quote?

[How can I write two separate blockquotes in sequence using markdown?](http://stackoverflow.com/questions/12979577/how-can-i-write-two-separate-blockquotes-in-sequence-using-markdown)

```
> Imagination is more important than knowledge.

<!-- -->
>  Never think of the future. It comes soon enough.

<!-- -->
> Anyone who has never made a mistake has never tried anything new.
```

> Imagination is more important than knowledge.

<!-- -->
>  Never think of the future. It comes soon enough.

<!-- -->
> Anyone who has never made a mistake has never tried anything new.

# ~~删除文字的样式~~怎么搞?

[Is it possible to use strikethrough (`<s>`, `<strike>`, `<del>`) in Trello?](http://webapps.stackexchange.com/questions/24394/is-it-possible-to-use-strikethrough-s-strike-del-in-trello)

用`~~foo~~`.

# 如何添加footnote?

在Daring Fireball的post "[About the footnotes](https://daringfireball.net/2005/07/footnotes)" 中看到可以给文章加footnote. 自己在post "[in an effort to](http://www.cnblogs.com/7z7chn/p/4982833.html)" 中用<a>尝试了一下, 还蛮好用的. 后来发现footnote是[multimarkdown](http://bywordapp.com/markdown/guide.html#section-mmd-footnotes)支持的一个语法, 形如:

```markdown
Clicking this number[^fn-sample_footnote] will lead you to a footnote.

[^fn-sample_footnote]: Handy! Now click the return link to go back.
```

效果:  

Clicking this number<a href="#fn:fn-sample_footnote" id="fnref:fn-sample_footnote" title="see footnote" style="  font-size: 0.8em;vertical-align: super;">2</a> will lead you to a footnote.

<p id="fn:fn-sample_footnote">Handy! Now click the return link to go back.<a href="#fnref:fn-sample_footnote" title="return to article" class="reversefootnote">&nbsp;↩</a></p>

另见: [php Markdown Extra - Footnotes](https://michelf.ca/projects/php-markdown/extra/#footnotes)

**遗憾的是博客园的Markdown不支持Multimarkdown.**

# 子列表
博客园中子列表前面需要加四个空格... 而在很多其他地方只要加一个或两个空格就可以了!