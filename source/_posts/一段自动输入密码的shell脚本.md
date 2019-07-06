---
title: 一段自动输入密码的shell脚本
date: 2019-06-18 20:32:02
tags:
- shell
categories: 
- tool
---

# shell
> shell是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。Ken Thompson的sh是第一种Unix Shell，Windows Explorer是一个典型的图形界面Shell。

shell脚本（shell script），是一种为shell编写的脚本程序。业界所说的shell通常都是指shell脚本，但读者朋友要知道，shell和shell script是两个不同的概念。由于习惯的原因，简洁起见，本文出现的“shell编程”都是指shell脚本编程，不是指开发shell自身（如Windows Explorer扩展开发）。

<!-- more -->

> Shell脚本（英语：Shell script），又称Shell命令稿、程序化脚本，是一种计算机程序使用的文本文件，内容由一连串的shell命令组成，经由Unix Shell直译其内容后运作。被当成是一种脚本语言来设计，其运作方式与解释型语言相当，由Unix shell扮演命令行解释器的角色，在读取shell脚本之后，依序运行其中的shell命令，之后输出结果。利用shell脚本可以进行系统管理，文件操作等。

在Unix及所有的类Unix系统中，如Linux、FreeBSD等操作系统，都存在shell脚本。依照Unix shell的各种不同类型，shell脚本也有各种不同方言。在DOS、OS/2、Microsoft Windows中的批处理文件，跟shell脚本有类似的功能。

当前主流的操作系统都支持shell编程，:
1. Linux
    Linux默认安装就带了shell解释器。
2. Mac OS
    Mac OS不仅带了sh、bash这两个最基础的解释器，还内置了ksh、csh、zsh等不常用的解释器。
3. Windows上的模拟器
    windows出厂时没有内置shell解释器，需要自行安装，为了同时能用grep, awk, curl等工具，最好装一个cygwin或者mingw来模拟linux环境。
    * cygwin
    * mingw

> 理论上讲，只要一门语言提供了解释器（而不仅是编译器），这门语言就可以胜任脚本编程，常见的解释型语言都是可以用作脚本编程的，如：Perl、Tcl、Python、PHP、Ruby。Perl是最老牌的脚本编程语言了，Python这些年也成了一些linux发行版的预置解释器。编译型语言，只要有解释器，也可以用作脚本编程，如C shell是内置的（/bin/csh），Java有第三方解释器Jshell，Ada有收费的解释器AdaScript。

## 自动输入密码的shell脚本实践
```shell
#! /bin/bash 
cd work-space/
sudo -S httpclient server << EOF
yourpassword
EOF
````
第1行：指定脚本解释器，这里是用/bin/bash做解释器的
第2行：进入工作目录
第3行：sudo权限启动本地文件夹的http服务，并开始接受用户交互数据，【因为sudo执行会提示输入密码】
第4行：自动输入sudo所需密码
第5行：结束交互

这就完成了，在需要的时候直接运行这个脚本文件就可以了。