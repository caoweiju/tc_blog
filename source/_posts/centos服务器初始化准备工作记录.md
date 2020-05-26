---
title: centos服务器初始化准备工作记录
date: 2019-11-03 17:44:30
tags:
- 科学上网
categories: 
- tool
---

# 服务器

一般而言开发者选择服务器会使用linux系统，常见的是Ubuntu和centos，不过现在的云服务上多得很，根据自己的喜好来选择吧。
## 初始化

- 购买服务器
- 登录服务器
- 初始化设置

### 用户添加
禁止root用户，并新建含有root权限的用户：
- 新增用户：`adduser userName`
- 设置密码：`passwd userName`
- 增加root权限： `vim /etc/sudoers`；添加内容 `userName ALL=(ALL) ALL`
- 修改ssh，禁止root登录，修改 ssh 登录的 端口号 【可以设置密码登录或者私钥登录】
- 阿里云实例安全组设置 打开需要对外的端口号包括 80、443、和ssh对应的端口号

<!-- more -->

### 服务端nginx使用
安装和配置使用相关可以参考：nginx: https://yq.aliyun.com/articles/699966


可以通过acme来自动生成证书，会需要对域名进行验证，可以设置查看是不是自己的域名，对于阿里云的域名可以通过关联ID来验证；
https证书部分： https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E 
    https://github.com/Neilpang/acme.sh/wiki/How-to-install

查看官方的文档[NGINX的文档](http://nginx.org/en/docs/beginners_guide.html)，可以通过`nginx`的可执行文件来启动`nginx`服务；

所以要启动nginx，可以这样:
    $ nginx // 一般安装的时候都会放到系统的启动文件夹里面[环境变量] /usr/local/bin/nginx

在启动之后需要使用`nginx -s signal`来进行操作，其中`signal`可以使用以下一些指令：
1. stop — fast shutdown
2. quit — graceful shutdown
3. reload — reloading the configuration file
4. reopen — reopening the log files

如果要停止服务，可以这样（完成当前的所有请求后停止，和stop的区别是stop会立即停止nginx）：
    $ nginx -s quit
如果修改了配置文件，要重新生效，可以这样：
    $ nginx -s reload

也可以参考：
1.启动 Nginx
systemctl start nginx
2.停止Nginx
systemctl stop nginx
3.重启Nginx
systemctl restart nginx
4.查看Nginx状态
systemctl status nginx
5.启用开机启动Nginx
systemctl enable nginx
6.禁用开机启动Nginx
systemctl disable nginx

nginx -t 查看nginx启动错误日志

### 服务端服务
可以使用任何后端语言和技术来完成后端服务的开发。
