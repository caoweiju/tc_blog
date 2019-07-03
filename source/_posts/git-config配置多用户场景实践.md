---
title: git config配置多用户场景实践
date: 2019-07-03 12:48:04
tags:
- github
categories: 
- tool
---
# git
> git是一个分布式版本控制软件，最初由林纳斯·托瓦兹创作，于2005年以GPL发布。最初目的是为更好地管理Linux内核开发而设计。应注意的是，这与GNU Interactive Tools（一个类似Norton Commander界面的文件管理器）有所不同。git最初的开发动力来自于BitKeeper和Monotone。git最初只是作为一个可以被其他前端（比如Cogito或Stgit）包装的后端而开发的，但后来git内核已经成熟到可以独立地用作版本控制。很多著名的软件都使用git进行版本控制，其中包括Linux内核、X.Org服务器和OLPC内核等项目的开发流程。

[更多相关介绍可以查看百科](https://zh.wikipedia.org/wiki/Git)

## git的基本使用
主要是指一下基本操作：
1. 安装
2. ssh登入配置
3. 命令使用

<!-- more -->

### 安装
根据自己的系统进行选择安装方式，可以使用命令行安装，也可以使用安装包安装，[git官方安装包地址](https://git-scm.com/downloads)
### ssh登入配置
安装完成之后，需要考虑和远程的仓库建立连接，就需要账号和密码，无论是`github`、`gitlab`等，都需要一个账户名，可以注册完成，然后设置：
````
git config --global user.name "yourName"
git config --global user.email "yourEmail"
````
为了避免每次push代码都需要密码之类的，我们通过ssh来配置；执行：
````
ssh-keygen
````
一路enter即可，在`~/.ssh/`下会生成两个文件，复制其中的公钥：
````
pbcopy < ~/.ssh/id_rsa.pub
````
进入远程仓库的个人中心，`https://github.com/settings/profile`；会找到关于`SSH and GPG keys`，有一个`New SSH key`的按钮，点击后，可以黏贴上刚复制的公钥，然后`Add SSH key`即可；

可以pull和push代码测试是否成功。
### git命令使用
1. 对于win10系统，推荐升级后使用好的官方新出的终端，`Windows Terminal`，需要当前最新的win10版本，用起来挺好的，可以再Microsoft的官方商城下载体验。
2. 对于mac或者linux用户，推荐配置 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh), 这个`oh-my-zsh`默认安装上了`git plugin`在`~/.zshrc`配置文件中，可以使用很多的简洁命令，[git简写命令集合](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/git/git.plugin.zsh)

## 多用户配置
[git-config的官网巨长文档](https://git-scm.com/docs/git-config)

### 按工程配置多用户

### 按目录配置多用户
