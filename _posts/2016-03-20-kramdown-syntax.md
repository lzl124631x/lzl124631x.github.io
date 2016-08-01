---
layout: post
title:  "Kramdown Syntax"
date:   March 20, 2016 11:23 PM
tags: Markdown Kramdown
---

# [Headers](http://kramdown.gettalong.org/syntax.html#headers)

Setext Style (`---`)和atx Style (`#`).

atx Style的`#`前面不允许有空格, 同GFM, 但haroopad允许.

原版MD允许忽略header之前的空行, 但是kramdown觉得这样会导致含义模糊, 所以==强制要求header前有空行==

一个corner case是

```
header
---
para
```

这是一个2级标题和一个段落, 而不是两个被水平线分割的段落.

规则就是: kramdown会先尝试将`---`当做Setext header解析, 剩下的`---`再当做水平线解析.

# [Attribute List Definitions (ALD)](http://kramdown.gettalong.org/syntax.html#attribute-list-definitions)
源自Maruku语法. 示例如下:

```
{:ref-name: #myid .my-class}
{:other: ref-name #id-of-other title="hallo you"}
{:test: key="value \" with quote" and other='quote brace \}'}
```

ALD的结构:

* `{` (前面至多三个空格)
* 被两个`:`括起来的reference name
* 属性定义 (其中可以有任意字符, 但`}`必须用`\`转义)
* `}` (后面任意多空格直到行尾)

reference name需要以字母或数字开头, 之后可以是字母, 数字或连字符`-`.

属性定义有四种类型, 不满足这四种类型的定义都会被忽略. 每个定义之间用至少一个空格分隔.

* references  
必须是有效的reference name. 被引用的ALD的属性会被添加到当前ALD中. 如果找不到到被引用的ALD则忽略.

* key-value pairs  
形如`key="value"`

* ID name  
`#IDNAME`即`id="IDNAME"`的简写

* class names  
`.CLASSNAME`即`class="CLASSNAME"`的简写.

以下写法是等效的:

```
{:id: .cls1 .cls2}
{:id: class="cls1" .cls2}
{:id: class="something" class="cls1" .cls2}
{:id: class="cls1 cls2"}
```

可以看出, 相同`key`的属性, 后定义的会覆盖先定义的.

如果多个ALD使用了同一个reference name, 那么这些ALD的定义会被合并到一起.

# [End-Of-Block Marker](http://kramdown.gettalong.org/syntax.html#eob-marker)
非标准MD语法. EOB就是仅包含`^`的一行, 它可以标志一个块元素的结束. 如果`^`前面没有块元素, `^`会被忽略.

下面这段MD会生成**一个**列表, 包含两个元素.

```
* list item one

* list item two
```
使用EOB记号`^`后可以产生两个列表, 分别包含一个元素.

```
* list one
^
* list two
```

# [Code Blocks](http://kramdown.gettalong.org/syntax.html#code-blocks)

## Standard Code Blocks
4个空格或1个tab的缩进标志着代码块的开始. 接下来的非空代码行无论是否由空格/tab开始, 都会被认为是代码块的一部分 (因为Kram的代码块支持换行, 标准MD不支持). 没有缩进的代码行会被放到上一行的行尾, 以一个空格分隔. 最后, 输出时每行的缩进(4个空格/1个tab)都会被删除.

仅被空白行分隔的两个代码块会被合并.

```
    Here comes some code

    This text belongs to the same code block.
```

如果想分割它们, 可以用EOB标识`^`.

```
    Here comes some code
^
    This one is separate.
```

## Fenced Code Blocks
源自PHP Markdown Extra语法, 非标准MD语法.

kramdown中, 可以使用连续的3个或更多的`~`符号作为代码块的起始行和结束行. 结束行必须至少有起始行有相同多的`~`. 起始行和结束行之间的代码会被作为代码原样输出.

```
~~~~~~~~
Here comes some code.
~~~~~~~~
```

如果你需要由`~`构成的代码行出现在代码块中, 只需要让起始行/结束行中的`~`更多即可.

```
~~~~~~~~~~~~
~~~~~~~
code with tildes
~~~~~~~~
~~~~~~~~~~~~~~~~~~
```

## Language of Code Blocks
使用IAL可以指定代码块所用的语言.

```
~~~
def what?
  42
end
~~~
{: .language-ruby}
```

`language-ruby`标志该代码块所用的语言是Ruby. 你还可以这么写:

```
~~~ ruby
def what?
  42
end
~~~
```

# [Inline Attribute Lists](http://kramdown.gettalong.org/syntax.html#inline-attribute-lists)

## Block Inline Attribute Lists
来自Maruku语法, 非标准MD语法.

Block IAL与ALD有相同的结构, 只是`:reference name:`的部分被替代为了`:`. Block IAL必须被直接放置在被修饰的块元素的前面或后面. 如果一个Block IAL上下紧挨着两个块元素, 则它修饰的是前面的块元素. 其他情况下(如被空白行包围)的Block IAL都会被忽略.

对于同key的键值对, IAL中的优先于ALD中的.

以下是Block IAL的示例:

```
A simple paragraph with an ID attribute.
{: #para-one}

> A blockquote with a title
{:title="The blockquote title"}
{: #myid}

{:.ruby}
    Some code here
```

## Span Inline Attribute Lists
来自Maruku语法, 非标准MD语法.

除了不允许前后有空格, Span IAL与Block IAL结构相同. Span IAL必须紧挨着被修饰的行内元素的前后, 否则会被忽略.

```
This *is*{:.underline} some `code`{:#id}{:.class}.
A [link](test.html){:rel='something'} and some **tools**{:.tools}.
```

一个特殊的Span IAL`{::}`的作用是: 分割那些如果不分割就会被错误解析的行内元素.

```
This *is italic*{::}*marked*{:.special} text
```