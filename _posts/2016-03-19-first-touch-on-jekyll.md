---
layout: post
title:  "Jekyll初接触问题汇总"
date:   March 19, 2016 10:46 PM
tags: Jekyll
---

# 在Jekyll中遇到的一些markdown问题

Jekyll的markdown processor默认是kramdown. 经测试, 它:
1. 不支持`$1_2$`这种数学符号
2. `#`与标题之间必须有空格
3. 换行必须要行尾两个空格.
4. 块级元素前面必须要有空行
5. [[笔记] 走进 Pocket，看看只有 20 位员工的 Pocket 是如何搞定 2000 万用户的](/startup/2016/01/24/pocket.html)中, 列表中的引用无法正确显示.
6. [两个有关Knockout自定义拓展方法fn的小技巧](/javascript,/knockout/2016/02/06/two-tips-about-knockout-custom-function.html)中, 列表中的代码块将一个完整的列表分割成了两部分.
7. 在Matrix Zigzag Traversal中, cpp代码中有两个连着的`{`, 结果报错`Liquid syntax error`.

自己用haroopad用得很顺手, 所以这些习惯都是haroopad惯出来的.

第2点, GFM不支持.

# md中必须要两个空格才能换行?
做如下设置即可:
```
kramdown:
  hard_wrap: true
```

从[这里](http://kramdown.gettalong.org/parser/gfm.html)可以看出, kramdown有四个parser, 分别是kramdown, markdown, GFM, 和HTML.
`hard_wrap`是GFM Parser的一个配置选项, 默认值为`true`.
而我发现Jekyll中设置不设置`input: GFM`是一样的, 所以我怀疑Jekyll默认启用了GFM Parser, 并且`hard_wrap`的默认值是`false`.

更多信息参考:
* [Kramdown Options](http://kramdown.gettalong.org/options.html)

[Jekyll Default Configuration](https://jekyllrb.com/docs/configuration/#default-configuration)中提示, Jekyll不支持kramdown中的`remove_block_html_tags`和`remove_span_html_tags`两个参数.

# 块级元素前面必须要有空行?

# 标题后必须要空格?

# 无法使用redcarpet?
_config.yml中设置`markdown: redcarpet`就提示错误:

```
Dependency Error: Yikes! It looks like you don't have redcarpet or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- redcarpet' If you run into trouble, you can find helpful resources at http://jekyllrb.com/help/!
  Conversion error: Jekyll::Converters::Markdown encountered an error while converting '_posts/2016-02-18-my first ps work.md':
                    redcarpet
             ERROR: YOUR SITE COULD NOT BE BUILT:
                    ------------------------------------
                    redcarpet
```

运行`gem install redcarpet`提示:

```
ERROR:  Error installing redcarpet:
        The 'redcarpet' native gem requires installed build tools.

Please update your PATH to include build tools or download the DevKit
from 'http://rubyinstaller.org/downloads' and follow the instructions
at 'http://github.com/oneclick/rubyinstaller/wiki/Development-Kit'
```

从<http://rubyinstaller.org/downloads>中下载DevKit安装exe, 然后按照<http://github.com/oneclick/rubyinstaller/wiki/Development-Kit>中的方法去安装即可. 简单来说就是解压后`ruby dk.rb init`, `ruby dk.rb install`.

# 如何支持`==highlight==`?

# jekyll中的categories和tags有什么区别?

[What's the difference between Categories and Tags in Jekyll?](http://stackoverflow.com/questions/8675841/whats-the-difference-between-categories-and-tags-in-jekyll)

跟我观察的结果一样: categories会导致url变化, 但是tags不会.