title: "Git 学习笔记"
date: 2015-11-02 17:02:23
categories: Git
tags: [Git, 版本管理]
---

根据廖雪峰的[Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)学习耳闻已久的`Git版本控制`，总是寻找借口一直处在忙碌状态的我，也终于踏上的Git的学习之路。

## Git概念
首先，理解什么是Git。
Git是目前最先进也是最流行的`分布式`版本控制系统。
而同样比较常见的SVN则是`集中式`版本控制系统。

## 集中式版本控制系统与分布式版本控制系统的区别

+ 集中式版本控制系统需要一台中央服务器来保存版本库，需要联网才能够工作。
+ 分布式版本控制系统则不需要中央服务器，每个用户的电脑里都有保存有完整的版本库，当自己修改了内容以后，只需要把修改的内容推送给同事即可。

## 安装Git
* Linux：安装前，首先试着在终端输入git，查看是否已经安装过git了。如果没有，输入``sudo apt-get install git``完成安装。
* Mac：
	1. 安装Homebrew，然后通过Homebrew安装Git，具体方法参考：[Homebrew官网](http://brew.sh/)
	2. 安装Xcode，Xcode中集成了Git，不过没有默认安装。
	3. 需要在Preference->Downloads下安装`Command Line Tools`。
* Windows：Windows下要使用Linux/Unix的工具时，需要`Cygwin`这样的模拟环境。然后Cygwin安装和配置比较复杂，已经有大神把模拟环境和Git打包好了，叫做[msysgit](http://msysgit.github.io/)，只需下载安装就好。

安装完成后，需要配置一下：
```
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```
使用这两个命令配置你的名字和邮箱地址

<span style="color:#e73751">Tips:</span> `git config`命令的`--global`参数是配置这台机器上的`所有Git仓库`，属于全局设置。当然不加参数也可以对`某个仓库`指定名字和邮箱。

## 创建版本库(repository)
```
git init
```
创建出一个空的版本库，在当前目录下会多出一个.git目录。

## 把文件添加到版本库
```
git add 文件名.后缀
```

## 提交到版本库
```
git commit -m "版本备注"
```
`-m`表示后面输入的是本次提交的说明，方便大家知道这次提交的代码是做什么的，也方便版本回退查找。

## 查看状态
```
git status
```
查看版本库实时的状态，哪些修改过，哪些没提交等。

## 查看修改内容
```
git diff
```
修改过后要进行版本提交，还需要`git add`一下，才能进行提交。

## 版本回退
```
git reset --hard HEAD^
```
HEAD为版本指针，指向当前版本，上一个版本就是HEAD^，上两个版本就是HEAD^^，上100个版本就是HEAD~100

## 查看提交历史
```
git log
```
查看提交历史以便确定需要回退到哪个版本。会显示版本号和版本说明信息。

```
git log --pretty=oneline
```
可以比较干净整洁的查看提交历史
     

## 查看命令历史
```
git reflog
```
查看命令历史以便找到回退前的版本，方便做撤销回退使用。
因为回退之后，`git log`就只能查找到回退的当前版本了。

## 跳跃到某个版本
```
git reset --hard commit_id
```
commit_id为版本号(不用完整，但需要保证唯一，一般六位就可以确认)，通常用于做撤销版本回退。