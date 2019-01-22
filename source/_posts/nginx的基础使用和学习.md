---
title: nginx的基础使用和学习
date: 2019-01-22 23:45:17
tags:
- nginx
categories: 
- nginx
---
# nginx
>Nginx("engine x")是一款是由俄罗斯的程序设计师Igor Sysoev所开发高性能的 Web和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 代理服务器。在高连接并发的情况下，Nginx是Apache服务器不错的替代品。

nginx的出现可以说对于那些在windows上使用IIS，linux上使用apache2的人提供了更多的选择，使用nginx的情况主要是满足了以下的一些功能：
1. 本地代理，对于前端开发人员而言，需要把很多的请求代理到本地，本质上还是在本地使用nginx起了web服务，进而完成一些重定向工作；
2. web服务器，nginx可以在服务器上承担整个web服务的分发和响应，其中反向代理、负载均衡是它很重要的功能。

本文主要是介绍了一些基础的nginx的使用，环境是mac10.13.2。

<!-- more -->

## 安装 镜像brew

在mac上可以使用两种方法来进行:
1. brew命令安装
2. nginx源码编译安装

本文没有尝试`./configure make make install`的方式，不过可以看看这个[安装NGINX](https://www.rails365.net/articles/nginx-de-an-zhuang)；本文只是尝试使用`brew`来进行安装。

homebrew主要分两部分：git repo（位于GitHub）和二进制bottles（位于bintray），这两者在国内访问都不太顺畅。可以替换成国内的镜像，

替换git源：
````
替换brew.git:
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git

替换homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
````
替换二进制bottles源[bash和zsh需要区分启动文件]：
````
//对于bash用户：
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile

//对于zsh用户
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
````
如此便可以执行安装：
    brew install nginx
    
执行完成的话那么就可以查看结果如何：`nginx -h`或者`nginx -v`看看结果
````
nginx -h   
nginx version: nginx/1.12.2
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/local/Cellar/nginx/1.12.2_1/)
  -c filename   : set configuration file (default: /usr/local/etc/nginx/nginx.conf)
  -g directives : set global directives out of configuration file

nginx -v
nginx version: nginx/1.12.2
````

## 配置文件
>The way nginx and its modules work is determined in the configuration file. By default, the configuration file is named nginx.conf and placed in the directory /usr/local/nginx/conf, /etc/nginx, or /usr/local/etc/nginx

`nginx`的命令比较的少，大部分配置都是在配置文件当做，配置文件的路径`/usr/local/nginx/conf, /etc/nginx, or /usr/local/etc/nginx`之中，`#`可以作为注释符来注释掉改行；

下面对这里的部分配置内容做个整体的了解和分类：

**从形式上分类：简单指令和块级指令集**
配置文件中主要是存在一些`simple directives and block directives`；可以认为是简单的指令和块级指令集，简单指令就是：
    worker_processes  1;
块级指令集就是一个块级指令名加上`{}`,里面包含很多简单指令集，块级指令集可以嵌套；
    events {
        worker_connections  1024;
    }

**从功能模块分类：主模块、事件模块、其他基本模块**
* [主模块](http://www.nginx.cn/doc/core/mainmodule.html)是控制`nginx`的一些基本指令集合，包含了类似上述的简单指令`worker_processes  1;`在内的一些基本指令；
* [事件模块](http://www.nginx.cn/doc/core/events.html)设置Nginx处理连接请求；
* [其他基本模块](http://www.nginx.cn/doc/)包括常用地`http`模块；

先看一个初始状态的配置文件：
````
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8080;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}

````

开始分析配置文件中的一些指令：
### 简单指令【本文例举了主模块的部分指令】
在默认的生成的配置文件的头部，有这么几行简单的指令，虽然大部分是被注释掉的，但是这里简单的说下其中的意义，这些简单指令都属于主模块的指令，用于控制基本的`nginx`的功能：
````
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;
````
1. `user`这个指令名代表的是执行`worker processes`的本机用户，默认是`nobody`,那么如果需要读写一些`roort`或者其他用户所有权的文件时，如果当前配置文件填写的`user`这个指令名对应的用户又不具有`r+w+x`的权限时，就会出现一些权限问题；
    >语法: user user [group]
    缺省值: nobody nobody
    指定Nginx Worker进程运行用户，默认是nobody帐号。
2. `worker_processes`这个指令名是指配置`worker_processes`的数量，`nginx`启动时会有一个主进程和若干的工作进程，这个指令就是来规定工作进程的数量的，对应的是一个数值
    >nginx has one master process and several worker processes. The main purpose of the master process is to read and evaluate configuration, and maintain worker processes. Worker processes do actual processing of requests.  
    语法: worker_processes number
    缺省值: 1
3. `error_log`这个指令是来记录`nginx`的运行出行的一些异常，可以指定异常级别
    >语法: error_log file [ debug | info | notice | warn | error | crit ]
    缺省值: ${prefix}/logs/error.log
4. `pid`这个是用来指定运行`nginx`的进程ID的；
    >语法: pid file

### 块级指令集(本文例举了http模块的部分功能)
由于很多模块都是块级指令集的形式的存在，本文拿出来`http`模块的部分指令来进行简单的解析；后面的第二篇会考虑把一些实用的、常用的、很有用的功能进行进一步讲解。[http核心模块的指令集合](http://www.nginx.cn/doc/standard/httpcore.html)、[http核心模块的指令集合](http://tool.oschina.net/apidocs/apidoc?api=nginx-zh)
````
http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8080;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    
}

````
1. `include`指令是主模块的指令，可以用在http的块级指令集中，是防止单个配置文件过大，可以直接引用其他的配置文件，而例子中的` mime.types`是一个文件，里面主要是比较全面的MIME信息，能包含文本、图像、音频、视频以及其他应用程序专用的数据和文件后缀名的映射
2. `sendfile`指令是指是否开启linux2+的一个`sendfile`的功能，[sendfile详解](http://xiaorui.cc/2015/06/24/%E6%89%AF%E6%B7%A1nginx%E7%9A%84sendfile%E9%9B%B6%E6%8B%B7%E8%B4%9D%E7%9A%84%E6%A6%82%E5%BF%B5/)
3. `server`是http模块的重要指令，其响应http链接的关键，一般而言会包含`listen server_name location`这三部分。
4. `localtion`指令是起到了一个路由的效果，只能在`server`块级中使用，对于各路径和结果进行响应的设置。

至于`https`和一写其他的指令将会留到下文进行详细的学习分析。其中可以使用的指令和变量如下：



## 运行
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

[一个nginx的中文翻译网站](http://tengine.taobao.org/nginx_docs/cn/)

  [1]: /img/bV4tyJ
  [2]: /img/bV4tyM