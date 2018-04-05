---
title: Tech Blog最佳实践
date: 2018-03-30 12:49:52
tags:
- blog
- git
- hexo
- github
categories:
- blog
---

*本文主要说明构建个人博客需要的所有工具和条件，以及每一个组件在构建blog的说明和作用，并不是完整的操作手册。在搭建博客前通过阅读本文，完整的明白各个组件承担的响应的角色，当遇到问题时可以清楚地确定问题可能发生的原因。推荐完整看完本文后再进入各个组件深入学习。本文阅读时间1小时。*

### Tools 
	Git + Github Pages + Node.js + Hexo + NexT Theme

### knowledge Base	
* __Git__ -- 熟悉Git的工作原理和怎样使用Git。 推荐时间：2天
* __Github__ -- 练习注册一个 账号[github](https://www.github.com) , 创建并练习使用repository，搭建Github pages。推荐时间： 1天
* __Hexo__ -- 安装Hexo, 设置基本的Hexo配置练习。推荐时间： 1天
* __NexT Theme__ --从Hexo 默认Theme切换到NexT后，练习自定义主题主要的元素。推荐时间： 0.5天
* __Markdown__ -- 学会使用Markdown的语法编辑文档，通过Markdown编辑器练习使用各种标签。推荐时间：0.5天

### Git
Git是目前世界上最先进的分布式版本控制系统（没有之一）。那什么是版本控制系统？ 
 
在编辑文档时，如果有一个软件，不但能自动帮我记录每次文件的改动，还可以让同事协作编辑，这样就不用自己管理一堆类似的文件了，也不需要把文件传来传去。如果想查看某次改动，只需要在软件里瞄一眼就可以，岂不是很方便？  

这样，你就结束了手动管理多个“版本”的史前时代，进入到版本控制的20世纪，这个软件就是Git。

还不明白，可以进入廖雪峰老师的文档继续深入学习：[Git introduction](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)  

Git下载地址：[Git Download](https://git-scm.com/download)

### Node.js
Node.js并不需要我们了解怎样使用它。我们只需要知道下面的Hexo能够正常工作需要依赖Node.js。所以只需要知道如何安装：

下载并安装nvm.（什么是nvm？不需要知道，只需要知道他可以方便安装node.js）

* Download nvm 
  
		$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh	

* install nvm

		$ cd .nvm/
		$ ./install.sh
* set bash profile

		$ export NVM_DIR="$HOME/.nvm"
		$ [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
* install Node.js

		$ nvm install stable

### GitHub Page
Github做为Git的云端版本库，可以放任何人随时随地边编辑和发布文档，而且完全开源免费。Github Page作为Github的一个功能，**只需要将站点文件上传到Github上，你可以拥有自己的免费Blog平台，不需要托管主机，不需要域名**。

account: _yourusername_  
通过ssh方式连接Github：  

	git@github.com:_yourusername_/
通过http方式连接Github：  
 
	https://github.com/_yourusername_/  

	$ git config --global user.name "Your Name"  
	$ git config --global user.email "email@example.com"

### Hexo
[Hexo](https://hexo.io/zh-cn/docs/)  是高效的静态站点生成框架，她基于 Node.js。 通过 Hexo 你可以轻松地使用 Markdown 编写文章，除了 Markdown 本身的语法之外，还可以使用 Hexo 提供的 标签插件 来快速的插入特定形式的内容。什么意思呢？

我们知道静网页文件都是_.html_文件，需要很多标签对格式进行控制。如果我们有Hexo后，我们可以将可读性很高的文本档使用Markdown的语法通过Hexo转换成_.html_文件，并且生成完整的完整框架。我们只需要编辑文本文件，通hexo生成网站的框架和内容，并且部署生成自己的网站。

* Install hexo

		$npm install hexo-cli -g
* set up hexo  
 
		$hexo init blog
		$cd blog
		$npm install
		$hexo server

### NexT
如果说Hexo为一个人的躯体，那么NexT就是这个躯体的衣服，让Hexo变得更美，这就是NexT。

在 Hexo 中有两份主要的配置文件，其名称都是 _config.yml。 其中，一份位于站点根目录下，主要包含 Hexo 本身的配置；另一份位于主题目录下，这份配置由主题作者提供，主要用于配置主题相关的选项。

为了描述方便，在以下说明中，将前者称为 站点配置文件， 后者称为 主题配置文件。

安装 NexT  
Hexo 安装主题的方式非常简单，只需要将主题文件拷贝至站点目录的 themes 目录下， 然后修改下配置文件即可。具体到 NexT 来说，安装步骤如下。  
  
下载主题  

	$ cd your-hexo-site
	$ git clone https://github.com/iissnan/hexo-theme-next themes/next

启用主题  

	#编辑系统配置文件_config.yml,找到Theme设置并将值改成next
	theme: next
验证主题  
	
	$hexo s --debug  
	INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.

### Markdown
Markdown 是一个 Web 上使用的文本到HTML的转换工具，可以通过简单、易读易写的文本格式生成结构化的HTML文档。目前 github、Stackoverflow 等网站均支持这种格式.  
[Markdown](http://www.markdown.cn/#acknowledgement)	

ENDING  
 
1. <http://zhaoweihao.me/2017/04/29/Hexo-Github-pages-%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E5%B1%9E%E4%BA%8E%E8%87%AA%E5%B7%B1%E7%9A%84%E5%8D%9A%E5%AE%A2/>

2. <https://www.cnblogs.com/fengxiongZz/p/7707219.html>

