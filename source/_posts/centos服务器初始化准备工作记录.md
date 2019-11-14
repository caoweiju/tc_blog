---
title: centos服务器初始化准备工作记录
date: 2019-11-03 17:44:30
tags:
- 科学上网
categories: 
- tool
---

# 服务器


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

### 服务端nginx使用
安装和配置使用相关可以参考：nginx: https://yq.aliyun.com/articles/699966

可以通过acme来自动生成证书，会需要对域名进行验证，可以设置查看是不是自己的域名，对于阿里云的域名可以通过关联ID来验证；
https证书部分： https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E 
    https://github.com/Neilpang/acme.sh/wiki/How-to-install

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
