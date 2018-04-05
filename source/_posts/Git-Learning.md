---
title: Git Learning
date: 2018-04-05 16:46:15
tags:
	- blog
	- git
category:
	- blog
---

### reopsitory
库，最基本的概念,需要做版本控制的目录或文件的集合都会保存在一个Repo中，通过Git命令可以看到每一个之前修改过的版本。

local Repo与Remote Repo可以建立映射关系
local repository <<====>> remote repository

目录c:/user/admin/git/下文件加入到Repo后后，local file可以与remote file可以形成相互备份关系。 
local directory(c:/user/admin/git/*) <<====>> https://github.com/username/repository  


### Branch
分支就是科幻电影里面的平行宇宙，当你正在电脑前努力学习Git的时候，另一个你正在另一个平行宇宙里努力学习SVN。

如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，结果，你既学会了Git又学会了SVN！
{% img "multiple world" /2018/04/05/Git-Learning/multiple-world.png %}

### Github

{% img "diagram of Repository" /2018/04/05/Git-Learning/repository.png %}

### Useful Command
#### 初始化 repo
    admin@admin-PC MINGW64 ~   
    $ mkdir git  
    $ cd git  
    $ pwd  
    /c/Users/admin/git  
    $ git init   

#### 添加到版本控制
	$ git add README.txt  
	$ git commit -m "discript what have done in files" //提交确认修改`

为什么Git添加文件需要add，commit一共两步呢？因为commit可以一次提交很多文件，所以你可以多次add不同的文件
{% img "work around" /2018/04/05/Git-Learning/workround.png %}

#### 查看git状态
	$ git status 

#### 查看修改的内容
	$ git diff file 


#### 查看commit log
	$ git log 		
很明显这查看表示历史修改版本记录，最主要是看commit id。

#### reset到指定的版本
	$ git reset --hard HEAD^ 
表示回退， 至于HEAD主要是与commit id映射，HEAD^表示上一个版本，HEAD~100，表示上100版本！这个鬼记得往上100个版本做了什么... 当然也可以直接写commit id， 更深的理解，commit id保存所有历史版本，**HEAD只是一个指针**。


#### reform log
	$ git reflog 
这个做什么呢，记录commit id - HEAD - actions的映射关系。方便吃后悔药。

	$ git reflog
	a944eee (HEAD -> master) HEAD@{0}: commit: vertion 2: added time.
	be90283 HEAD@{1}: commit (initial): edit the readme file

#### checkout
	$ git checkout -- file   
只要没有把版本推送到远程库，一切误操作都可以清除。包括是git reset HEAD file。

####remove file

	$ git rm file 
删除文件，误删除怎么办，上一步刚说过，checkout

#### remote repository
Git的天敌是SVN，不过SVN已经在沙滩上了，Git是怎么做到的？ K.O技能：remote repository。 Now，请注册一个Github账号---完全免费共享的remote repository。如果有秘密项目，那么可以自己搭建一个git server。

	$ git remote add origin git@github.com:username/repository   //添加remote git库
	$ git remote add origin  https://github.com/username/repositor 	//另一种方式添加remote repo

#### push
	$ git push -u origin master 	//将本地库推送到remote

#### clone
	$ git clone git@github.com:username/repository 		//同步remote库到本地，通过SSH协议
	$ git clone https://github.com/username/repository 		//通过https协议

#### swich branch
	$ git checkout -b branchname 		
创建并切换分支。相当于
	$ git branch branchname
	$ git checkout branchname

#### check branch
	$ git branch
#### merge branch
	$ git merge branchname (--no-ff)
合并branch，no off 表示关闭fast forward模式，将命令中的branch merge到当前workaround的branch。

#### delete branch
	$ git branch -d branchname

### Git learning review
廖雪峰最简单易懂Git学习[Git Learning](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)
