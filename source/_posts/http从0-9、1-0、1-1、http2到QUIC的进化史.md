---
title: http从0.9、1.0、1.1、http2到QUIC的进化史
date: 2019-06-18 14:47:04
tags:
- http
categories: 
- tool
---
# http
> HTTP协议（HyperText Transfer Protocol，超文本传输协议）是因特网上应用最为广泛的一种网络传输协议，所有的WWW文件都必须遵守这个标准。HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件, 图片文件, 查询结果等）。

在七层OSI模型中，HTTP处于应用层的位置。常见分类列表如下：
````
1.  物理层physical layer 
    例如线路、无线电、光纤
2.  数据链路层data link layer 
    例如以太网、令牌环、HDLC、帧中继、ISDN、ATM、IEEE 802.11、FDDI、PPP
3.  网络层network layer 
    例如IP、ICMP、IPX、BGP、OSPF、RIP、IGRP、EIGRP、ARP、RARP、X.25
4.  传输层transport layer 
    例如TCP、UDP、RTP、SCTP、SPX、ATP、IL
5.  会话层session layer 
    例如ASAP、ISO 8327 / CCITT X.225、RPC、NetBIOS、ASP、IGMP、Winsock、BSD sockets
6.  表示层presentation layer 
    例如XDR、ASN.1、SMB、AFP、NCP
7.  应用层application layer 
    例如HTTP、SMTP、SNMP、FTP、Telnet、SIP、SSH、NFS、RTSP、XMPP、Whois、ENRP、TLS
````

<!-- more -->

## http0.9
> 该协议于 1989 年首次曝光，以 HTTP 0.9 的形式面世。Timothy Berners-Lee 在瑞士日内瓦附近的 CERN 上首次提到它时，它仅包含 1 行代码。唯一的方法是 GET，还有一个像下面这个示例这样简单的请求：GET /index.html。响应同样很简单，仅包含所请求的文件。

**HTTP 0.9 不是一个正式标准，通过这种方式引用它是为了将它与随后的正式版本区分开。**

## http1.0
1996 年，推出了 HTTP 1.0 作为 IEFT 标准[依据 RFC 1945](https://tools.ietf.org/html/rfc1945); 差不多六十页左右吧，比较完整的定义了HTTP请求的基本标准，包括但不限于：
1. 基础语法
2. 协议参数
3. 报文消息格式
4. 请求和响应定义
5. body、请求方法、状态码
6. 请求头

基本上是目前我们最为常见的http的基础内容，不过后面有慢慢的进行补充和优化；就产生了http1.1；

## http1.1
1999 年，在 [RFC 2616](https://www.ietf.org/rfc/rfc2616.txt) 中发布了 HTTP 1.1。第一个主要版本中的缺点促使人们在 1999 年进行了一次小幅修订，引入了大量可选特性和零碎细节 — 并消除了一些不好的方面。

和http1.0对比，主要使用有以下一些方面的优化：
1. 长连接
    HTTP 1.1支持长连接（PersistentConnection）和请求的流水线（Pipelining）处理，在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟，在HTTP1.1中默认开启Connection： keep-alive，这个在http1.0中需要手动开启，一定程度上弥补了HTTP1.0每次请求都要创建连接的缺点。
2. host域--请求头
    在HTTP1.0中认为每台服务器都绑定一个唯一的IP地址，因此，请求消息中的URL并没有传递主机名（hostname）。但随着虚拟主机技术的发展，在一台物理服务器上可以存在多个虚拟主机（Multi-homed Web Servers），并且它们共享一个IP地址。 HTTP1.1的请求消息和响应消息都应支持Host头域，且请求消息中如果没有Host头域会报告一个错误（400 Bad Request）。此外，服务器应该接受以绝对路径标记的资源请求。
3. 缓存数据
    HTTP/1.1中引入了一个ETag头域用于重激活机制，它的值entity tag可以用来唯一的描述一个资源。请求消息中可以使用If-None-Match头域来匹配资源的entitytag是否有变化。
4. 新增状态码
    [新增部分](https://www.rfc-editor.org/rfc/rfc2616.html#section-10.1)的状态码，
5. 断点续传功能
    HTTP1.1支持传送内容的一部分。比方说，当客户端已经有内容的一部分，为了节省带宽，可以只向服务器请求一部分。HTTP/1.0中，存在一些浪费带宽的现象，例如客户端只是需要某个对象的一部分，而服务器却将整个对象送过来了。例如，客户端只需要显示一个文档的部分内容，又比如下载大文件时需要支持断点续传功能，而不是在发生断连后不得不重新下载完整的包。HTTP/1.1中在请求消息中引入了range头域，它允许只请求资源的某个部分。在响应消息中Content-Range头域声明了返回的这部分对象的偏移值和长度。如果服务器相应地返回了对象所请求范围的内容，则响应码为206（Partial Content），它可以防止Cache将响应误以为是完整的一个对象
6. 请求方法的增加
    HTTP1.1增加了OPTIONS,PUT, DELETE, TRACE, CONNECT这些Request方法.

还有一些小的细节的修改，总体而言，是对http1.0的一些改善处理。

不过目前而言，还是有一些主要被诟病的地方：
1. 请求报文的冗余，cookie每次请求都默认会带上【跨域需要单独设置】
2. 多个请求的链接建立开销RRT
3. 服务器的主动推送缺失

这也是http2.0所做的主要修改。

## http2.0
> 2012年google如一声惊雷提出了SPDY的方案，优化了HTTP1.X的请求延迟，解决了HTTP1.X的安全性，HTTP2.0可以说是SPDY的升级版（其实原本也是基于SPDY设计的），但是，HTTP2.0 跟 SPDY 仍有不同的地方。

相比较于http1.1，http2.0的主要特性包括：
1. 新升级路径
    SPDY 需要一个安全连接，虽然社区迫于压力会建立这样的连接，但 HTTP/2 规范没有强制要求这么做。但是，所有主要浏览器供应商都仅在 TLS 上实现 HTTP/2，而且不支持不安全的连接。这实际上会迫使 Web 应用程序实现者对所有 HTTP/2 流量使用 TLS。
2. 二进制分帧、流优先化、流控制
     HTTP/2 的最重要改变是转换为二进制协议。对于开发人员，这可以说是性能增强的焦点。新协议称为二进制分帧层（binary framing layer），它重新设计了编码机制，而没有修改方法、动词和标头的熟悉语义。新的二进制分帧机制改变了客户端与服务器之间交换数据的方式。 为了说明这个过程，我们需要了解 HTTP/2 的三个概念：
    * 数据流：已建立的连接内的双向字节流，可以承载一条或多条消息。
    * 消息：与逻辑请求或响应消息对应的完整的一系列帧。
    * 帧：HTTP/2 通信的最小单位，每个帧都包含帧头，至少也会标识出当前帧所属的数据流。
    这些概念的关系总结如下：
    * 所有通信都在一个 TCP 连接上完成，此连接可以承载任意数量的双向数据流。
    * 每个数据流都有一个唯一的标识符和可选的优先级信息，用于承载双向消息。
    * 每条消息都是一条逻辑 HTTP 消息（例如请求或响应），包含一个或多个帧。
    * 帧是最小的通信单位，承载着特定类型的数据，例如 HTTP 标头、消息负载等等。 来自不同数据流的帧可以交错发送，然后再根据每个帧头的数据流标识符重新组装。
    将 HTTP 消息分解为很多独立的帧之后，我们就可以复用多个数据流中的帧，客户端和服务器交错发送和传输这些帧的顺序就成为关键的性能决定因素。 为了做到这一点，HTTP/2 标准允许每个数据流都有一个关联的权重和依赖关系：
    * 可以向每个数据流分配一个介于 1 至 256 之间的整数。
    * 每个数据流与其他数据流之间可以存在显式依赖关系。
    数据流依赖关系和权重的组合让客户端可以构建和传递“优先级树”，表明它倾向于如何接收响应。 反过来，服务器可以使用此信息通过控制 CPU、内存和其他资源的分配设定数据流处理的优先级，在资源数据可用之后，带宽分配可以确保将高优先级响应以最优方式传输至客户端。
    流控制是一种阻止发送方向接收方发送大量数据的机制，以免超出后者的需求或处理能力：发送方可能非常繁忙、处于较高的负载之下，也可能仅仅希望为特定数据流分配固定量的资源。 例如，客户端可能请求了一个具有较高优先级的大型视频流，但是用户已经暂停视频，客户端现在希望暂停或限制从服务器的传输，以免提取和缓冲不必要的数据。 再比如，一个代理服务器可能具有较快的下游连接和较慢的上游连接，并且也希望调节下游连接传输数据的速度以匹配上游连接的速度来控制其资源利用率；等等。
3. 请求/响应复用
    在 HTTP/2 的新范例中，仅在客户端与服务器之间建立了一个 TCP 连接，而且该连接在交互持续期间一直处于打开状态。在此连接上，消息是通过逻辑流进行传递的。一条消息包含一个完整的帧序列。在经过整理后，这些帧表示一个响应或请求。将消息分解为帧，为每帧分配一个流标识符，然后在一个 TCP 连接上独立发送它们。此技术实现了完全双向的请求和响应消息复用，
4. 报头压缩
    每个 HTTP 传输都承载一组标头，这些标头说明了传输的资源及其属性。 在 HTTP/1.x 中，此元数据始终以纯文本形式，通常会给每个传输增加 500–800 字节的开销。如果使用 HTTP Cookie，增加的开销有时会达到上千字节。 （请参阅测量和控制协议开销。） 为了减少此开销和提升性能，HTTP/2 使用 HPACK 压缩格式压缩请求和响应标头元数据，这种格式采用两种简单但是强大的技术：
    * 这种格式支持通过静态霍夫曼代码对传输的标头字段进行编码，从而减小了各个传输的大小。
    * 这种格式要求客户端和服务器同时维护和更新一个包含之前见过的标头字段的索引列表（换句话说，它可以建立一个共享的压缩上下文），此列表随后会用作参考，对之前传输的值进行有效编码。
    利用霍夫曼编码，可以在传输时对各个值进行压缩，而利用之前传输值的索引列表，我们可以通过传输索引值的方式对重复值进行编码，索引值可用于有效查询和重构完整的标头键值对。
5. 服务器推送
    HTTP/2 新增的另一个强大的新功能是，服务器可以对一个客户端请求发送多个响应。 换句话说，除了对最初请求的响应外，服务器还可以向客户端推送额外资源，而无需客户端明确地请求。

## quic--http3
Google开发QUIC协议，[QUIC -Quick UDP Internet Connections](https://docs.google.com/document/d/1g5nIXAIkN_Y-7XJW5K45IblHd_L2f5LTaDUDwvZ5L6g/edit)，QUIC协议集成了TCP可靠传输机制、TLS安全加密、HTTP /2 流量复用技术，IETF觉得QUIC是一个好东西，但是希望QUIC不仅可以运输HTTP，还可以运输其它协议，把QUIC与HTTP分离。

整体思想更像是在应用层用UDP协议重写TCP协议，把原本TCP协议里拥塞控制之类的在UDP上重新实现，在此基础上保留HTTP2.0的优化实现，并加入新的优化。

Key advantages of QUIC over TCP+TLS+HTTP2 include:
* Connection establishment latency
* Improved congestion control
* Multiplexing without head-of-line blocking
* Forward error correction
* Connection migration

**目前HTTP3是在QUIC基础上做的修改，还处于未完成状态，虽然有部分测试实践，但是后面也可能会有变动，所以先了解然后静观其变**

参考：
1. [谷歌开发者文档http2](https://developers.google.com/web/fundamentals/performance/http2)
2. [ibm文档关http2的优化](https://www.ibm.com/developerworks/cn/web/wa-http2-under-the-hood/index.html)
3. [quic的简单草案](https://quicwg.org/base-drafts/draft-ietf-quic-http.html)
4. [quic的谷歌记录](https://www.chromium.org/quic)
