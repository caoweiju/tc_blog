---
title: gitbook和netlify的结合部署
date: 2019-07-07 16:34:02
tags:
- github
categories: 
- tool
---

# gitbook
>GitBook 是一个基于 Node.js 的命令行工具，可使用 Github/Git 和 Markdown 来制作精美的电子书，GitBook 并非关于 Git 的教程。

## 一步一步部署

可以参考，[官方的一步一步的部署教程](https://www.netlify.com/blog/2015/12/08/a-step-by-step-guide-gitbook-on-netlify/)，

生成步骤如下：
1. 需要安装node
2. 创建项目根文件夹，并进入文件夹
3. 使用npm管理相关包 `npm init`
4. 安装gitbook， `npm install -g gitbook-cli --save-dev`
5. 在根目录下初始化book `gitbook init`
6. 修改生成的SUMMARY.md文件， 
    ````
    # Summary
    * [Introduction](README.md)
    * [The Jabberwocky](chapter1.md)
    * [A Modest Proposal](chapter2.md)
    ````
7. 启动服务。本地查看：`gitbook serve`

<!-- more -->

结合github使用：
1. 创建`.gitignore`，并添加内容
    ````
    # Node rules:
    ## Grunt intermediate storage (http://gruntjs.com/creating-plugins#storing-task-files)
    .grunt

    ## Dependency directory
    ## Commenting this out is preferred by some people, see
    ## https://docs.npmjs.com/misc/faq#should-i-check-my-node_modules-folder-into-git
    node_modules/

    # Book build output
    _book/

    # eBook build output
    *.epub
    *.mobi
    *.pdf
    ````
2. 项目变更为git项目`git init`
3. 项目变更为git项目并添加修改`git add .`，然后进行commit
4. 在github上面添加新的仓库工程，并提交


服务部署：
1. 正常部署
2. 构建的时候填写服务目录时和构建时的命令不一样
    ````
    Build command : 构建指令
    npm install -g gitbook-cli && gitbook install && gitbook build
    Publish directory: 服务目录
    _book/
    ````


## 使用实践
待更新。。。
