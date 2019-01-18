---
title: linux的开关机脚本执行
date: 2019-01-18 12:22:00
tags:
- shell
categories: 
- linux
---
# linux
>`Linux`是一种开源电脑操作系统内核。它是一个用C语言写成，符合`POSIX`标准的`类Unix`操作系统。

>`90年代初期Linux`开始出现的时候，仅仅是以源代码形式出现，用户需要在其他操作系统下进行编译才能使用。后来出现了一些正式版本。目前最流行的几个正式版本有：`SUSE、RedHat、Fedora、Debian、Ubuntu、CentOS、Gentoo`，等等。用户可根据自己的经验和喜好选用合适的`Linux`发行版。
原先`Linus Torvalds`将`Linux`置于一个禁止任何商业行为的条例之下，但之后改用`GNU`通用公共许可证第二版。该协议允许任何人对软件进行修改或发行，包括商业行为，只要其遵守该协议，所有基于`Linux`的软件也必须以该协议的形式发表，并提供源代码。

对于开发人员而言，很多时候都会接触到`LINUX`系统，甚至要去维护和开发，所以对`开机/关机`过程需要一定的认识，特别是服务器大部分都是`linux系统`的时候，我们重启服务、切换服务等都需要保障服务的功能不能中断，这就更需要对`linux`的运行过程有深入的认识，本文只是记录一小部分过程。

<!-- more -->

## Linux操作系统运行级别
Linux 使用的是基于运行级(`run-levels`) 概念的称为 `SysVinit` 的专用启动工具。 它在不同的系统上可能是完全不一样的， 所以不能认为一个脚本在某个 Linux 发行版上工作正常。

`SysVinit`以运行级的模式来工作，一般有 7 （从 0 到 6）个运行级 (实际上可以有更多的运行级，但都是用于特殊情况而且一般使用不到。 参见 init(8)以获得更多信息)，每个运行级对应于一套设定好的任务， 当启动一个运行级的时候， 计算机就需要执行相应的任务。 默认的运行级是 3，下面是对不同运行级的描述：
````
0: 关闭计算机
1: 单用户模式
2: 无网络多用户模式
3: 有网络多用户模式
4: 保留作自定义，否则同运行级 3 
5: 同运行级 4，一般用于图形界面(GUI)登录(如 X的 xdm 或 KDE的 kdm) 
6: 重启动计算机
````

可以使用`runlevel`查看，也可以在`/etc/inittab`文件中看到以下：
````
# Default runlevel. The runlevels used by RHS are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
# 
id:3:initdefault:

# System initialization.
si::sysinit:/etc/rc.d/rc.sysinit
````
也就是说我们系统的运行级别目前是3，那么需要看的就是`/etc/rc3.d`下的内容，其实里面都是软连接，所有的软链指向/etc/init.d/中的脚本文件，命名规范如下：
````
[K|S][number][service name] --> ../init.d/servicename
      S 表示 Start，开启服务
      [number]表示的是该脚本的运行优先级，number越小，脚本的运行优先级就越高
      [service name]表示的是服务的名称。
      因此我们如果需要定义一个服务启动的优先级（顺序），则需指定脚本的number即可。
````


## 开机过程
1. 加载内核
    >打开电脑电源，开始读取 BIOS 并进行主机的自我测试；透过 BIOS 取得第一个可开机装置，读取主要开机区 (MBR) 取得开机管理程式；  
    LILO启动之后，如果你选择了Linux作为准备引导的操作系统，第一个被加载的东西就是内核。请记住此时的计算机内存中还不存在任何操作系统，PC(因为它们天然的设计缺陷)也还没有办法存取机器上全部的内存。因此，内核就必须完整地加载到可用RAM的第一个兆字节之内。为了实现这个目的，内核是被压缩了的。这个文件的头部包含着必要的代码，先设置CPU进入安全模式(以此解除内存限制)，再对内核的剩余部分进行解压缩。

2. 执行内核
    >内核在内存中解压缩之后，就可以开始运行了。此时的内核只知道它本身内建的各种功能，也就是说被编译为模块的内核部分还不能使用。最基本的是，内核必须有足够的代码设置自己的虚拟内存子系统和根文件系统(通常就是ext2文件系统)。一旦内核启动运行，对硬件的检测就会决定需要对哪些设备驱动程序进行初始化。从这里开始，内核就能够挂装根文件系统(这个过程类似于Windows识别并存取C盘的过程)。内核挂装了根文件系统之后，将启动并运行一个叫做init的程序。
3. init进程
    >`init `程式开始执行系统初始化,最先读取`/etc/inittab`文件中的配置，配置中一般是先执行下 `/etc/rc.d/rc.sysinit`,然后执行`/etc/init.d/functions` ，设置环境变量等，同时读取`runlevel`的配置级别，*对于嵌入式开发而言，在执行`rcN.d`之前会有`/etc/init.d/rcS` 单用户模式启动脚本的执行*。  
    依据 `init `的设定的`initdefault`进行 `daemon start` (`/etc/rc.d/rc[0-6].d/*`),依次执行脚本，启动服务。关机时依次关闭服务。
    `rc0.d~rc.6.d`文件夹下分别对应的是操作系统`0-6`级运行的状态下需要执行的脚本。在这些文件夹下的文件，都是软链文件，指向指定位置的脚本,这些软链都是指向同一个文件夹`/etc/init.d/`的脚本文件，

4. 载入本机设定 (/etc/rc.d/rc.local)
    >`rc.local`文件，

`/etc/profile`文件。这个文件是系统启动时，任何用户登录时执行的文件。任何用户登录前，root用户也会执行一遍。

 `~/.bash_profile`文件。这个文件每个用户都有。它在用户登录时自动执行，拥有用户的权限。它export的环境变量对该用户随后启动的进程都有效。



## 自启动脚本实践
* 如果用户需要开机自动以`root`权限执行一些脚本，那么最好的方法是编辑`/etc/rc.d/rc.local`文件。
* 如果每一个用户登录时都应该执行的脚本，如设置一些环境变量。那么应该修改`/etc/profile`文件。
* 如果某一个特定用户登录时应该执行特定的脚本，如设置该用户特定的环境变量，应该修改`~/.bash_profile`文件。
````
============
/etc/profile
============
此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行.
并从/etc/profile.d目录的配置文件中搜集shell的设置.

===========
/etc/bashrc
===========
为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.

===============
~/.bash_profile
===============
每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该
文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件.

=========
~/.bashrc
=========
该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该文件被读取.

==========
~/.profile
==========
在Debian中使用.profile文件代 替.bash_profile文件
.profile(由Bourne Shell和Korn Shell使用)和.login(由C Shell使用)两个文件是.bash_profile的同义词，目的是为了兼容其它Shell。在Debian中使用.profile文件代 替.bash_profile文件。

==============
~/.bash_logout
==============当每次退出系统(退出bash shell)时,执行该文件. 
````

[Linux如何实现开机启动程序详解](http://www.cnblogs.com/gzggyy/archive/2012/08/07/2626574.html)