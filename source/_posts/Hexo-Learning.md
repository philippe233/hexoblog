---
title: Hexo Learning
date: 2018-04-05 21:56:48
tags:
	- blog
	- hexo
category:
	- blog
---
_作为搭建个人blog最为重要的组件，Hexo可以说是最核心的部分，不仅是因为它提供了基本的框架，而且还有丰富的扩展包，自定义主题格式等丰富功能，最重要的是作为新手来说简洁易懂。_

### what is Hexo?
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

### 安装 Hexo
安装前提
在安装前，已安装下列应用程序：
Node.js
Git

所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo。

	$ npm install -g hexo-cli

### 建站（本地）
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

	$ hexo init <folder>
	$ cd <folder>
	$ npm install

新建完成后，指定文件夹的目录如下：  
>.
>├── _config.yml
>├── package.json
>├── scaffolds
>├── source
>|     ├── _drafts
>|     └── _posts
>└── themes


### 文件和目录
**_config.yml** -- 网站的 配置 信息，您可以在此配置大部分的参数。
**package.json** -- 应用程序的信息。EJS, Stylus 和 Markdown renderer 已默认安装，您可以自由移除。  
  
{% codeblock package.json [lang:json] %}
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": ""
  },
  "dependencies": {
    "hexo": "^3.0.0",
    "hexo-generator-archive": "^0.1.0",
    "hexo-generator-category": "^0.1.0",
    "hexo-generator-index": "^0.1.0",
    "hexo-generator-tag": "^0.1.0",
    "hexo-renderer-ejs": "^0.1.0",
    "hexo-renderer-stylus": "^0.2.0",
    "hexo-renderer-marked": "^0.2.4",
    "hexo-server": "^0.1.2"
  }
}
{% endcodeblock %}

**scaffolds** -- 模版 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。
Hexo的模板是指在新建的markdown文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。

**source** -- 资源文件夹是存放用户资源的地方。除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。
**themes** -- 主题 文件夹。Hexo 会根据主题来生成静态页面。

### Tag Plugins
**Quota**

	{% blockquote [author[, source]] [link] [source_link_title] %}
	content
	{% endblockquote %}

**Code**
  
	{% codeblock [title] [lang:language] [url] [link text] %}
	code snippet
	{% endcodeblock %}

**Image**

	{% img [class names] /path/to/image [width] [height] [title text [alt text]] %}
*path start with the public folder*

### Hexo extension packages
**deploy to git website**  

	$ npm install hexo-deployer-git --save
	$ hexo d -g
**enable search feature**
  
	$ npm install hexo-generator-search  --save
**enable acticle wordcount**

	$ npm install hexo-wordcount --save
**enable generate tag feature and pages**

	$ npm install hexo-generator-tag --save
**enable website search feature**

	$ npm install hexo-generator-searchdb --save

### Hexo documentation
想要学习更详细的Hexo，[传送门](https://hexo.io/docs/index.html)
