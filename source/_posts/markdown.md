---
title: Markdown Quick Guide
date: 2018-03-29 18:01:39
tags:
	- blog
	- HTML
	- markdown
category:
	- blog
---

*本文主要介绍Markdown的基本使用方法，基本不用了解HTML的语法。按照本文的说明编辑文本后，只要通过Markdown编辑器，就能生成一个html文件。本文阅读时间大约1-2个小时。*

## 概览  
 
### 宗旨
Markdown 的目标是实现「易读易写」。

### 兼容 HTML
Markdown 语法的目标是：成为一种适用于网络的书写语言。

### 特殊字符自动转换
在 HTML 文件中，有两个字符需要特殊处理： < 和 & 。 < 符号用于起始标签，& 符号则用于标记 HTML 实体，如果你只是想要显示这些字符的原型，你必须要使用实体的形式，像是 &lt; 和 &amp;。

## 区块元素

### 段落和换行
在插入处先按入两个以上的空格然后回车

### 标题
在行首插入 1 到 6 个 #，对应到标题 1 到 6 阶，例如：
	# 这是 H1
	
	## 这是 H2
	
	###### 这是 H6
	
### 区块引用blockquotes
用 > 的引用方式，例如：
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.

区块引用可以嵌套，例如：
>
> > This is nested blockquote.
>
> Back to the first level

### 列表
序列表使用星号、加号或是减号作为列表标记：
	*   Red
	*   Green
	*   Blue

有序列表则使用数字接着一个英文句点：
	1.  Bird
	2.  McHale
	3.  Parish

* 列表项目标记通常是放在最左边，但是其实也可以缩进，最多 3 个空格，项目标记后面则一定要接着至少一个空格或制表符。
* 列表项目可以包含多个段落，每个项目下的段落都必须缩进 4 个空格或是 1 个制表符：
* 如果要放代码区块的话，该区块就需要缩进两次，也就是 8 个空格或是 2 个制表符：
* 在行首出现数字-句点-空白:
	1986\. What a great season.

### 代码区块
要在 Markdown 中建立代码区块很简单，只要简单地缩进 4 个空格或是 1 个制表符就可以，例如，下面的输入：

这是一个普通段落：

    这是一个代码区块。
一个代码区块会一直持续到没有缩进的那一行（或是文件结尾）。

### 分隔线
你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线

	* * *

	***

	*****

	- - -

	---------------------------------------

## 区段元素
### 链接
行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可:

	This is [an example](http://example.com/ "Title") inline link.
	
	[This link](http://example.net/) has no title attribute.
	
	See my [About](/about/) page for details.
参考式的链接是在链接文字的括号后面再接上另一个方括号，而在第二个方括号里面要填入用以辨识链接的标记：

	This is [an example][id] reference-style link.
链接内容定义的形式为：
	* 方括号（前面可以选择性地加上至多三个空格来缩进），里面输入链接文字
	* 接着一个冒号
	* 接着一个以上的空格或制表符
	* 接着链接的网址
	* 选择性地接着 title 内容，可以用单引号、双引号或是括弧包着
下面是一个参考式链接的范例：

	I get 10 times more traffic from [Google] [1] than from
	[Yahoo] [2] or [MSN] [3].
	
		[1]: http://google.com/        "Google"
		[2]: http://search.yahoo.com/  "Yahoo Search"
		[3]: http://search.msn.com/    "MSN Search"

### 强调
Markdown 使用星号（\*）和底线（_）作为标记强调字词的符号，被 \* 或 _ 包围的字词会被转成用 <em\> 标签包围，用两个 \* 或 _ 包起来的话，则会被转成 <strong\>，例如：

	*single asterisks*
*single asterisks*

	_single underscores_
_single underscores_

	**double asterisks**
**double asterisks**

	__double underscores__
__double underscores__

### 代码
如果要标记一小段行内代码，你可以用反引号把它包起来（`），例如：

	Use the `printf()` function.
如果要在代码区段内插入反引号，你可以用多个反引号来开启和结束代码区段：

### 图片
Markdown 使用一种和链接很相似的语法来标记图片，同样也允许两种样式： 行内式和参考式。
行内式的图片语法看起来像是：

	![Alt text](/path/to/img.jpg)

	![Alt text](/path/to/img.jpg "Optional title")
参考式的图片语法则长得像这样：

	![Alt text][id]
	[id]: url/to/image  "Optional title attribute"

	
## 其它
### 反斜杠
利用反斜杠来插入一些在语法中有其它意义的符号
Markdown 支持以下这些符号前面加上反斜杠来帮助插入普通的符号：

	\   反斜线
	`   反引号
	*   星号
	_   底线
	{}  花括号
	[]  方括号
	()  括弧
	#   井字号
	+   加号
	-   减号
	.   英文句点
	!   惊叹号

### 自动链接
只要是用方括号包起来， Markdown 就会自动把它转成链接，一般网址的链接文字就和链接地址一样，例如：

	<http://example.com/>


## Markdown 免费编辑器
Windows 平台
* [MarkdownPad](http://markdownpad.com/) 
* [MarkPad](http://code52.org/DownmarkerWPF/)

Linux 平台
* [ReText](http://sourceforge.net/p/retext/home/ReText/)

Mac 平台
* [Mou](http://mouapp.com/)

在线编辑器
* [Markable.in](http://markable.in/)
* [Dillinger.io](http://dillinger.io/)

浏览器插件
* [MaDe (Chrome)](https://chrome.google.com/webstore/detail/oknndfeeopgpibecfjljjfanledpbkog)

高级应用(Sublime Text 2 + MarkdownEditing 教程)
* [Sublime Text 2](http://www.sublimetext.com/2)
* [MarkdownEditing](http://ttscoff.github.com/MarkdownEditing/)
* [教程](http://lucifr.com/2012/07/12/markdownediting-for-sublime-text-2/)


## 感谢
感谢: <http://daringfireball.net/projects/markdown/>

原文：<http://www.markdown.cn/#overview> 















