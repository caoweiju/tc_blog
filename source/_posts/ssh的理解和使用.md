---
title: ssh的理解和使用
date: 2019-01-16 23:54:59
tags:
- ssh
categories: 
- tool
---
````
ls ~/.ssh/            
config   id_rsa    id_rsa.pub   known_hosts   test.crt   test.key   testenc.key
````

# SSH
>SSH 为 `Secure Shell` 的缩写，由 IETF 的网络小组（`Network Working Group`）所制定；SSH 为建立在应用层基础上的安全协议。SSH 是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 `SSH` 协议可以有效防止远程管理过程中的信息泄露问题。SSH最初是UNIX系统上的一个程序，后来又迅速扩展到其他操作平台。`SSH`在正确使用时可弥补网络中的漏洞。SSH客户端适用于多种平台。几乎所有`UNIX`平台—包括`HP-UX、Linux、AIX、Solaris、Digital UNIX、Irix`，以及其他平台，都可运行SSH。

`SSH`只是一种协议，存在多种实现，既有商业实现，也有开源实现。本文针对的实现是`OpenSSH`，它是自由软件，应用非常广泛。SSH在`Linux Shell`中的用法。如果要在`Windows`系统中使用SSH，会用到另一种软件`PuTTY`。

<!-- more -->

## 基本的用法

`SSH`主要用于远程登录。假定你要以用户名`user`，登录远程主机`host`，只要一条简单命令就可以了。

    $ ssh user@host

如果本地用户名与远程用户名一致，登录时可以省略用户名。

    $ ssh host

`SSH`的默认端口是22，也就是说，你的登录请求会送进远程主机的22端口。使用`p`参数，可以修改这个端口。

    $ ssh -p 2222 user@host

上面这条命令表示，`ssh`直接连接远程主机的`2222`端口。

## 连接方式

`SSH`之所以能够保证安全，原因在于它采用了公钥加密。

整个过程是这样的：
1. 远程主机收到用户的登录请求，把自己的公钥发给用户。
2. 用户使用这个公钥，将登录密码加密后，发送回来。
3. 远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。

## 安全隐患
上面所述的 `SSH`登入过程本身是安全的，但是实施的时候存在一个风险：如果有人截获了登录请求，然后冒充远程主机，将伪造的公钥发给用户，那么用户很难辨别真伪。因为不像`https`协议，`SSH`协议的公钥是没有证书中心`（CA）`公证的，也就是说，都是自己签发的。

可以设想，如果攻击者插在用户与远程主机之间（比如在公共的`wifi`区域），用伪造的公钥，获取用户的登录密码。再用这个密码登录远程主机，那么`SSH`的安全机制就荡然无存了。这种风险就是著名的`"中间人攻击"`（`Man-in-the-middle attack`）。

### 第一次登入
为了应对上述的`中间人攻击`，在用户第一次登录对方主机，系统会出现下面的提示：
````
$ ssh user@host

　　The authenticity of host 'host (12.18.429.21)' can't be established.

　　RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.

　　Are you sure you want to continue connecting (yes/no)?
````
这段话的意思是，无法确认`host`主机的真实性，只知道它的公钥指纹，问你还想继续连接吗？

所谓`"公钥指纹"`，是指公钥长度较长（这里采用`RSA`算法，长达`1024`位），很难比对，所以对其进行`MD5`计算，将它变成一个`128`位的指纹。上例中是`98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d`，再进行比较，就容易多了。

很自然的一个问题就是，用户怎么知道远程主机的公钥指纹应该是多少？回答是没有好办法，远程主机必须在自己的网站上贴出公钥指纹，以便用户自行核对。

假定经过风险衡量以后，用户决定接受这个远程主机的公钥。

    Are you sure you want to continue connecting (yes/no)? yes

系统会出现一句提示，表示host主机已经得到认可。

    Warning: Permanently added 'host,12.18.429.21' (RSA) to the list of known hosts.

然后，会要求输入密码。

    Password: (enter password)

如果密码正确，就可以登录了。

当远程主机的公钥被接受以后，它就会被保存在文件`$HOME/.ssh/known_hosts`之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。

每个`SSH`用户都有自己的`known_hosts`文件，此外系统也有一个这样的文件，通常是`/etc/ssh/ssh_known_hosts`，保存一些对所有用户都可信赖的远程主机的公钥。

## `config`配置
在通过`known_hosts`的记录远程主机的公钥可信度之后，每次登陆都需要书写一堆 登陆命令 ，为了简而书写，可以通过`config`文件来进行配置，配置前我们登陆：
    ssh username@hostname -p port
然后输入密码；在建立配置文件，`touch ~/.ssh/config`配置后，我们只需要输入连接账户的别名即可
    ssh 别名
配置方法：
>在.ssh/config中配置，如果没有config，创建一个即可，然后将下面的(别名，主机名，端口，用户名替换即可)，端口如果默认22可以不要，删除那行即可。
````
Host 别名
    Hostname 主机名
    Port 端口
    User 用户名
    IdentifyFile 指定的私钥地址
````
配置好之后，就可以直接ssh 别名连接了，不需要输入一长串，但是还是有一个问题，就是还是需要输入密码。解决办法就是将我们的公钥拷贝到连接的机器上来免密登陆就可以了【`config`文件中的字段`IdentifyFile`就是制定了和公钥配对的私钥，已达到免密登入的便捷】。

## 免密登入【公钥登录】
使用密码登录，每次都必须输入密码，非常麻烦。好在SSH还提供了公钥登录，可以省去输入密码的步骤。

所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录`shell`，不再要求密码。

这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接用`ssh-keygen`生成一个：

    $ ssh-keygen

运行上面的命令以后，系统会出现一系列提示，可以一路回车。其中有一个问题是，要不要对私钥设置口令（`passphrase`），如果担心私钥的安全，这里可以设置一个。

运行结束以后，在`$HOME/.ssh/`目录下，会新生成两个文件：`id_rsa.pub`和`id_rsa`。前者是你的公钥，后者是你的私钥。

这时再输入下面的命令，将公钥传送到远程主机host上面：

    $ ssh-copy-id user@host

好了，从此你再登录，就不需要输入密码了【`ssh-copy-id`这个命令可能有问题，[ssh-copy-id可能的问题](https://superuser.com/questions/189376/ssh-copy-id-does-not-work)】。

如果还是不行，就打开远程主机的`/etc/ssh/sshd_config`这个文件，检查下面几行前面"#"注释是否取掉。

    RSAAuthentication yes
    PubkeyAuthentication yes
    AuthorizedKeysFile .ssh/authorized_keys

然后，重启远程主机的ssh服务。
````
　　// ubuntu系统
　　service ssh restart

　　// debian系统
　　/etc/init.d/ssh restart
````
### authorized_keys文件

远程主机将用户的公钥，保存在登录后的用户主目录的`$HOME/.ssh/authorized_keys`文件中。公钥就是一段字符串，只要把它追加在`authorized_keys`文件的末尾就行了。

这里不使用上面的`ssh-copy-i`d命令，改用下面的命令，解释公钥的保存过程：

    $ ssh user@host 'mkdir -p .ssh && chmod 700 ~/.ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub

这条命令由多个语句组成，依次分解开来看：
1. `"$ ssh user@host"`，表示登录远程主机；
2. 单引号中的`mkdir .ssh && cat >> .ssh/authorized_keys`，表示登录后在远程shell上执行的命令：
3. `"$ mkdir -p .ssh"`的作用是，如果用户主目录中的`.ssh`目录不存在，就创建一个；
4. `'cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub`的作用是，将本地的公钥文件`~/.ssh/id_rsa.pub`，重定向追加到远程文件`authorized_keys`的末尾【需要注意，` .ssh/authorized_keys`这个文件可能没有，执行`touch .ssh/authorized_keys`，有了`authorized_keys`之后，权限可能需要修改，`chmod 640 ~/.ssh/authorized_keys`，然后才是把公钥追加进去】。
>最好把权限设置上
$ chmod 600 ~/.ssh/authorized_keys
$ chmod 700 ~/.ssh

写入`authorized_keys`文件后，公钥登录的设置就完成了。

## 跳板机登入
在很多时候，我们的本地机器是无法直接访问远程主机的，而是通过跳板机来完成的，`A`是本地机器，`B`是跳板机，`C`是线上机器。
````
+-----+             +-----+             +-----+
|     |             |     |             |     |
|  A  +-----------> |  B  +-----------> |  C  |
|     |             |     |             |     |
+-----+             +-----+             +-----+
````
而在这种过程当中，我们不得不输入两次密码【可能密码还不一样】，无论是`scp`获取文件还是登入都变得繁琐，为了能够直接`A->C`，
````
+-----+             +-----+             +-----+ 
|     |             |     |             |     | 
|  A  +-------------------------------> |  C  |
|     |             |     |             |     | 
+-----+             +-----+             +-----+   
````
这就是通过`“ProxyCommand”`选项，机器A能够灵活使用任意代理机制与机器C上的`SSH Server`端口建立连接，接着机器A上的`SSH Client`再与该连接进行数据交互，从而机器A上的`SSH Client`与机器C上的`SSH Server`之间建立了与一般“直接SSH连接”不太一样的“间接SSH连接”。不过由于“间接SSH连接”的透明性，逻辑上可认为机器A上的`SSH Client`与机器C上的`SSH Server`建立了“直接SSH连接”。

![clipboard.png](http://huananimg.zanmeizhuen.com/blog/img/ssh.png)

本机、跳板机、目标机器三者已经做过公钥认证(如果不做密钥认证就会提示分别输入跳板机和目标机器的密码，需要多输入两次密码比较繁琐);下面的案例前提都是我们可以免密登入跳板机；
````
$ mkdir -p ~/.ssh/persist
$ scp 用户名@跳板机:~/.ssh/id_rsa ~/.ssh/id_rsa.cn6  #拷贝跳板机的密钥到本地，不同的跳板机可以起不同的名字，比如id_rsa.cn8
# 可以是删除跳板机上的秘钥对设置的密码，重新生成无密码秘钥对，也可以跳过这一步。
$ cd .ssh
$ ssh-keygen -p
Enter file in which the key is : id_rsa.cn6 # 输入刚才的拷贝的密钥文件名
Enter old passphrase: # 输入passphrase
Key has comment 'id_rsa.cn6'
Enter new passphrase (empty for no passphrase): # 直接回车
Enter same passphrase again: # 直接回车
Your identification has been saved with the new passphrase.
````
方案都是使用`ssh ProxyCommand`:
````
ProxyCommand ssh proxyserver -W [%h]:%p

ProxyCommand ssh proxyserver nc -q0 %h %p 2> /dev/null

ProxyCommand ssh proxyserver exec nc -q0 %h %p 2> /dev/null
````
**％h`[host]`和％p`[port]`被SSH客户端替换以指示预期目的地，ProxyCommand基本思路：`SSH`可以调用外部应用程序，期望它与目标主机建立连接。建立连接后，应用程序必须使用`stdin / stdout`公开连接。**
### 代理机制一`"ssh -W" mode`
`ProxyCommand`选项值形式为[`OpenSSH 5.3以上才支持`]
    ssh -W C:CPort -l USER -i PRIVATE_KEY -p BPort B
    
原理：`ssh`命令自提供的代理机制，在机器A上另外单独建立与B的`SSH`连接（使用`-l USER -i PRIVATE_KEY -p BPort B`这些参数），并在B端建立端口映射监听终端信息，而后B端与机器C上的`SSH Server`端口（即“C:CPort”）建立ssh连接，在C上建立端口映射监听终端信息，并在C端发起和B端的其他链接【tcp/socket】,将C端的标准输入输入映射到B端，供B和A的ssh连接使用。

假定A上ssh_config配置文件内容如下：
````
Host B
    HostName %h
    User dsl
    Port 1046
    IdentityFile ~/.ssh/id_rsa.cn6

Host C
    HostName %h
    User dsl 
    Port 1046
    IdentityFile ~/.ssh/id_rsa.cn6
    ProxyCommand ssh -W %h:%p B
````
在A上执行ssh C命令，发现A与C成功建立SSH连接。根据以上所述，A机器通过ssh连接了B机器，然后B机器链接C机器，同时，需要C机器通过连接在`stdin/stdout`上与B建立通信，然后将通信结果返回给A。

### 常使用代理制剂二`netcat `
在最开始的时候，`-W`参数还没有引入，可以使用nc实现【所以两个很类似，之是方式略有不同，特别是B端和C端的nc的链接是由B端发起的，而不是C端发起】，`ProxyCommand`选项值形式为
    nc -X 5 -x B:BPort C CPort
    ProxyCommand nc %h %p

原理：利用`nc | netcat`命令，在登录代理机器B后，B机器需要通过nc建立和机器C的链接，通过连接在`stdin/stdout`上与C建立通信后，然后将通信结果返回给A。

假定A上ssh_config配置文件内容如下：
````
Host B
    HostName %h
    User dsl
    Port 1046
    IdentityFile ~/.ssh/id_rsa.cn6
Host C
    HostName %h
    User dsl 
    Port 1046
    IdentityFile ~/.ssh/id_rsa.cn6
    ProxyCommand ssh B exec nc %h %p 2> /dev/null
````


全文参考： 
[SSH原理与运用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html), 
[proxycommand](https://github.com/kennylevinsen/sshmuxd/wiki/ProxyCommand)
[支持跳板机间接链接线上机](https://dslztx.github.io/blog/2017/05/19/ssh%E5%91%BD%E4%BB%A4%E4%B9%8BProxyCommand%E9%80%89%E9%A1%B9/), 
[SSH穿越跳板机：一条命令跨越跳板机直接登陆远程计算机](http://mingxinglai.com/cn/2015/07/ssh-proxycommand/)
[使用SSH ProxyCommand 配置跳板机登录生产环境机器](https://timlentse.github.io/2016/09/19/SSH-ProxyCommand-%E9%85%8D%E7%BD%AE%E8%B7%B3%E6%9D%BF%E6%9C%BA%E7%99%BB%E5%BD%95%E7%94%9F%E6%88%90%E7%8E%AF%E5%A2%83%E6%9C%BA%E5%99%A8.html)
[ssh 如何快速免密码登录服务器](https://github.com/hyatzh/classroom/wiki/ssh-%E5%A6%82%E4%BD%95%E5%BF%AB%E9%80%9F%E5%85%8D%E5%AF%86%E7%A0%81%E7%99%BB%E5%BD%95%E6%9C%8D%E5%8A%A1%E5%99%A8)
[What is the difference between ssh proxycommand -W, nc, exec nc](https://stackoverflow.com/questions/22635613/what-is-the-difference-between-ssh-proxycommand-w-nc-exec-nc)
[如何透过 SSH 代理穿越跳板机](https://www.hi-linux.com/posts/929.html)
[Netcat with SSH Port Forwarding](https://jtway.co/netcat-with-ssh-port-forwarding-148177b2e850)





