> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [dbwu.tech](https://dbwu.tech/posts/network/what-is-tcp-fast-open/)

> 2019-01-05 计算机网络

2019-01-05 [计算机网络](https://dbwu.tech/tags/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C)

*   [概述](#%E6%A6%82%E8%BF%B0)
*   [TFO](#tfo)
    *   [实现原理：](#%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86)
        *   [1. 首次连接](#1-%E9%A6%96%E6%AC%A1%E8%BF%9E%E6%8E%A5)
        *   [2. 后续连接](#2-%E5%90%8E%E7%BB%AD%E8%BF%9E%E6%8E%A5)
    *   [优点](#%E4%BC%98%E7%82%B9)
    *   [局限性](#%E5%B1%80%E9%99%90%E6%80%A7)
*   [模拟环境](#%E6%A8%A1%E6%8B%9F%E7%8E%AF%E5%A2%83)
    *   [内核参数调整](#%E5%86%85%E6%A0%B8%E5%8F%82%E6%95%B0%E8%B0%83%E6%95%B4)
*   [程序代码](#%E7%A8%8B%E5%BA%8F%E4%BB%A3%E7%A0%81)
    *   [接收方 (服务端) 代码](#%E6%8E%A5%E6%94%B6%E6%96%B9-%E6%9C%8D%E5%8A%A1%E7%AB%AF-%E4%BB%A3%E7%A0%81)
    *   [发送方 (客户端) 代码](#%E5%8F%91%E9%80%81%E6%96%B9-%E5%AE%A2%E6%88%B7%E7%AB%AF-%E4%BB%A3%E7%A0%81)
*   [运行程序实验](#%E8%BF%90%E8%A1%8C%E7%A8%8B%E5%BA%8F%E5%AE%9E%E9%AA%8C)
    *   [1. 启动服务端程序，并确认监听状态](#1-%E5%90%AF%E5%8A%A8%E6%9C%8D%E5%8A%A1%E7%AB%AF%E7%A8%8B%E5%BA%8F%E5%B9%B6%E7%A1%AE%E8%AE%A4%E7%9B%91%E5%90%AC%E7%8A%B6%E6%80%81)
    *   [2. 客户端开始抓包](#2-%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%BC%80%E5%A7%8B%E6%8A%93%E5%8C%85)
    *   [3. 运行客户端程序](#3-%E8%BF%90%E8%A1%8C%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%A8%8B%E5%BA%8F)
    *   [4. 查看客户端 TCP 连接状态](#4-%E6%9F%A5%E7%9C%8B%E5%AE%A2%E6%88%B7%E7%AB%AF-tcp-%E8%BF%9E%E6%8E%A5%E7%8A%B6%E6%80%81)
*   [WireShark 抓包结果分析](#wireshark-%E6%8A%93%E5%8C%85%E7%BB%93%E6%9E%9C%E5%88%86%E6%9E%90)
    *   [第一次建立连接](#%E7%AC%AC%E4%B8%80%E6%AC%A1%E5%BB%BA%E7%AB%8B%E8%BF%9E%E6%8E%A5)
    *   [后续建立连接](#%E5%90%8E%E7%BB%AD%E5%BB%BA%E7%AB%8B%E8%BF%9E%E6%8E%A5)
*   [Reference](#reference)

概述
--

在之前的文章 [为什么 TCP 建立连接需要三次握手](https://dbwu.tech/posts/network/why-tcp-does-needs-three-way-handshake/#fqa) 中，关于下面 3 个问题给出了简单的回答:

1.  第一次握手时可以携带应用数据吗？
2.  第二次握手时可以携带应用数据吗？
3.  第三次握手时可以携带应用数据吗？

简单来说，传统的 TCP 建立连接时需要三次握手，而且这三次握手只是发送简单的 `SYN` 和 `ACK` 报文。

从网络带宽的资源利用的角度来看，传输层的 TCP 头部 + 网络层的 IP 头部，最少有 40 个字节，为了发送几个字节的报文数据包，而额外组装了 40 个字节的头部，这有点类似 [所谓的 “糊涂窗口综合症”](https://dbwu.tech/posts/network/what-is-tcp-sliding-window/#%E7%B3%8A%E6%B6%82%E7%AA%97%E5%8F%A3%E7%BB%BC%E5%90%88%E7%97%87)。

从应用优化的角度来看，因为要等到 TCP 经过三次握手建立连接之后才能发送应用层数据，所以会造成应用程序首次发送数据时存在一定的延迟，尤其是短连接、移动设备等场景中，这种副作用会加剧。

那么这种问题如何解决呢？使用 `TFO` 解决方案。

📝 PS: 因为 TCP 协议栈不同版本间存在差异，所以**本文的前提是 TCP 三次握手时不传输数据 (传统的三次握手)**，事实上，很多云计算服务商提供的 Linux 发行版本都对网络协议栈进行了优化，会在 TCP 第三次握手时直接发送数据，读者自己抓包验证时，可能会和本文结果存在一定差异。

TFO
---

**TCP Fast Open (TFO) 是在传统的三次握手基础上进行优化，允许在握手过程中发送数据，从而减少首次发送数据的延迟，提升网络应用性能**。

### 实现原理：

`TFO` 的核心原理是在发送方和接收方通信中，引入 1 个 **Cookie** 机制，这样使发送方在后续重连接收方时，能够简化 TCP 三次握手。

> 顾名思义，TFO Cookie 中的 Cookie 和 Web 应用层 中的 Cookie 机制一样，第一次访问时，需要登录验证，然后由服务端验证后，后续访问中可以直接携带，无需再次登录。

#### 1. 首次连接

*   当发送方第一次和接收方建立 TCP 连接时，发送 1 个 `SYN` 报文
*   接收方返回 `SYN-ACK` 报文的同时，附带一个随机生成的名为 `TFO Cookie` 的标识符给发送方
*   发送方收到 `SYN-ACK` 报文后，保存 `TFO Cookie`，发送 `ACK` 报文给接收方，完成三次握手，开始传输数据

![](https://dbwu.tech/images/network/tcp-fast-open/9.png)

#### 2. 后续连接

*   当发送方再次连接同一个接收方时，可以在 `SYN` 报文中携带上次保存的 `TFO Cookie`，同时在 `SYN` 报文中附带应用层数据 (也就是第一次握手时就直接发送数据)
*   接收方验证发送方的 `TFO Cookie` 后，将数据发送给应用层处理，并返回 `SYN-ACK` 报文 (同时也可以发送数据)
*   发送方收到 `SYN-ACK` 报文后，发送 `ACK` 报文给接收方，完成三次握手

![](https://dbwu.tech/images/network/tcp-fast-open/10.png)

### 优点

通过 `TFO`，发送方在发送 `SYN` 报文时就可以直接携带数据，接收方可以在第一次握手时直接处理数据，并且在第二次握手时直接发送数据，最终:

*   **发送方第一次发送数据，减少了 1.5 个 RTT 延迟**
*   **接收方第一次发送数据，减少了 1 个 RTT 延迟**

![](https://dbwu.tech/images/network/tcp-fast-open/12.png)

![](https://dbwu.tech/images/network/tcp-fast-open/11.png)

### 局限性

1.  兼容性

需要通信双方都支持 TFO, 如果其中一方不支持，连接自动回退到传统的 TCP 连接建立过程，此外，通信链路中的转发设备 (NAT, 防火墙) 也会执行这个兼容性机制。

2.  安全性

虽然 `TFO` 的 Cookie 是由接收方生成并发送给发送方的，并且每个 Cookie 都与发送方关联，但是增加了接收方的安全攻击面，可能引发诸如 [“TCP SYN Flood” 放大攻击](https://dbwu.tech/posts/network/what-is-ddos/) 等安全风险。

如果攻击者从被入侵主机获取到有效的 `TFO Cookie`，进而伪造了大量的携带数据报文，那么接收方就需要大量的内存来临时存储应用数据，最终导致内存耗尽。

3.  部署环境要求

对内核版本有要求，且需要修改内核参数。

4.  应用数据过大

如果发送方第一次要发送的数据大于 [TCP 的 MSS](https://dbwu.tech/posts/network/mtu-with-mss/), 依然需要拆包进行多次发送，当应用数据过大时，`TCP Fast Open` 带来的优势 (RTT 减少) 几乎可以忽略。

模拟环境
----

`TFO` 需要发送方和接收方同时支持，如果任意一方不支持 TFO，连接会自动回退到传统的三次握手方式。

为了演示效果，笔者使用了 2 个 Linux 服务器作为通信发送方和接收方，对应的发行版本和内核版本参数如下。

> TFO 对 Linux 内核版本要求: >= 3.7。

```
# 发送方

## 发行版本 (WSL2 环境)
$ cat /etc/os-release 

PRETTY_

VERSION_ID="11"
VERSION="11 (bullseye)"
VERSION_CODENAME=bullseye
ID=debian

## 内核版本
$ uname -r

5.10.0-21-amd64

# 接收方

## 发行版本
$ cat /etc/os-release


VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_

## 内核版本
$ uname -r

3.10.0-1160.53.1.el7.x86_64
```

### 内核参数调整

`TFO` 启用需要修改默认内核参数:

*   `0`：关闭 TFO
*   `1`：启用发送方模式 TFO
*   `2`：启用接收方模式 TFO
*   `3`：同时启用发送方和接收方模式 TFO

```
# 发送方启用 TFO
$ echo 1 | sudo tee /proc/sys/net/ipv4/tcp_fastopen

# 接收方启用 TFO
# 写入 3 表示既启用发送方 TFO 也启用接收方 TFO
$ echo 3 | sudo tee /proc/sys/net/ipv4/tcp_fastopen
```

作为模拟实验，笔者只是临时修改了参数，可以采用如下步骤进行配置永久生效:

1.  编辑 `/etc/sysctl.conf` 文件，添加配置项

2.  运行 `sysctl -p` 命令生效，重启之后仍然有效

程序代码
----

如果读者使用主机的 `curl` 版本较高，可以直接使用如下方式直接开启 `TFO` 机制方式访问:

```
net.ipv4.tcp_fastopen=3
```

因为笔者所使用的服务器中的 `curl` 版本较低，所以这里编写 `Python` 脚本代码，核心代码其实就是 2 个套接字的参数的设置而已。

### 接收方 (服务端) 代码

将接收方作为服务端程序的方式来实现，绑定 / 监听指定端口，然后接收来自发送方 (客户端) 的 TCP 连接。

```
$ curl --tcp-fastopen http://example.com

# 可以使用如下方式确认 curl 版本是否支持 TFO
$ curl -V | grep -i TFO
```

### 发送方 (客户端) 代码

```
# service.py

import socket

def listen():
    # 初始化服务端监听对象
    listener = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    # 参数 32 表示 TCP 协议栈
    # 未完成的 Fast Open 队列长度
    listener.setsockopt(socket.SOL_TCP, socket.TCP_FASTOPEN, 32)

    # 监听 12345 端口号
    # 为了模拟，所以不用主流端口号了 :-)
    listener.bind(('0.0.0.0', 12345))
    # 最大连接数设置为 1024
    listener.listen(1024)

    print("Server is listening on port 12345...")

    # 轮询接收新的 TCP 连接
    while True:
        conn, addr = listener.accept()
        print(f"Accepted connection from {addr}")
        print(f"Received data: {conn.recv(1024)}")

        conn.send(b"Hello, Client")
        conn.close()

        print(f"Closed connection with {addr}")


if __name__ == "__main__":
    try:
        # 启动监听
        listen()
    except KeyboardInterrupt:
        # 捕获 Ctrl + C 终止程序
        print("Server shutting down...")
```

运行程序实验
------

程序核心代码 (~总共 2 行~) 准备就绪，接下来开始运行程序，验证 `TCP Fast Open` 过程。

服务端公网 IP: `104.21.71.166`

### 1. 启动服务端程序，并确认监听状态

```
# client.py

import socket

# 初始化客户端监听对象
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 参数 32 表示 TCP 协议栈
# 未完成的 Fast Open 队列长度
sock.setsockopt(socket.SOL_TCP, socket.TCP_FASTOPEN, 32)

# 向服务端发送数据时
# 设置 Fast Open 选项
sock.sendto(b"Hello, Server", socket.MSG_FASTOPEN, ("104.21.71.166", 12345))

print(f"Received data: {conn.recv(1024)}")

sock.close()
```

### 2. 客户端开始抓包

打开 WireShark, 监听对应的网卡设备。

### 3. 运行客户端程序

```
# 在 1 个终端启动服务端程序

$ python3 server.py

# 在另外 1 个终端查看程序监听状态是否正常

$ netstat -ant | grep 12345 | grep LISTEN

tcp        0      0 0.0.0.0:12345            0.0.0.0:*               LISTEN
```

### 4. 查看客户端 TCP 连接状态

```
# 为了验证效果，这里可以连续运行几次
# 每次运行间隔 3 - 5 秒即可
$ python3 client.py

# 输出省略
...
```

一切运行正常，接下来就可以去看 WireShark 的抓包结果了。

WireShark 抓包结果分析
----------------

首先使用 `tcp.options.tfo` 过滤条件，快速筛选出和 `TCP Fast Open` 有关的 TCP 报文。

![](https://dbwu.tech/images/network/tcp-fast-open/1.png)

下面对 WireShark 抓包结果展开分析一下。

### 第一次建立连接

当发送方第一次和接收方建立 TCP 连接时，发送 1 个 `SYN` 报文，以及设置 TCP Options 字段 `TCP Fast Open` 。

**此时并没有发送任何数据**，所以 WireShark 抓包结果中的 `Len = 0`。

![](https://dbwu.tech/images/network/tcp-fast-open/3.png)

![](https://dbwu.tech/images/network/tcp-fast-open/2.png)

接收方返回 `SYN-ACK` 报文的同时，附带一个随机生成的名为 `TFO Cookie` 的标识符给发送方。

![](https://dbwu.tech/images/network/tcp-fast-open/5.png)

![](https://dbwu.tech/images/network/tcp-fast-open/4.png)

发送方收到 `SYN-ACK` 报文后，保存 `TFO Cookie`，发送 `ACK` 报文给接收方，完成三次握手。

其中 `TFO Cookie` 的值为: `d82d9074a6105a13`。

三次握手完成后，开始传输数据。

### 后续建立连接

![](https://dbwu.tech/images/network/tcp-fast-open/6.png)

通过截图可以看到，后续客户端和服务端建立 TCP 连接时，会在第一次握手时携带 `FTO Cookie` 并且直接发送数据，所以 WireShark 抓包结果中的 `Len = 13`。

那么这个 13 是什么？就是客户端发送的数据，正好是 13 个字节。

```
netstat -ant | grep 12345 | grep TIME_WAIT

# 输出如下
# 连续运行了多少次 client.py 
# 就会产生多少 TIME_WAIT 状态的 TCP 连接
# 10.0.0.53 为客户端的内网 IP 地址


tcp        0      0 10.0.0.53:38084         104.21.71.166:12345       TIME_WAIT  
tcp        0      0 10.0.0.53:37530         104.21.71.166:12345       TIME_WAIT  
tcp        0      0 10.0.0.53:37528         104.21.71.166:12345       TIME_WAIT  
tcp        0      0 10.0.0.53:38076         104.21.71.166:12345       TIME_WAIT  
tcp        0      0 10.0.0.53:38078         104.21.71.166:12345       TIME_WAIT  

...
```

后续 TCP 连接建立 (第一次握手) 时就可以直接发送数据 (篇幅所限，这里只截图 2 个数据抓包详情):

![](https://dbwu.tech/images/network/tcp-fast-open/7.png)

![](https://dbwu.tech/images/network/tcp-fast-open/8.png)

每个数据包中的 `TFO Cookie` 的值都是 `d82d9074a6105a13`，也就是第一次建立 TCP 连接时，服务端发送 `SYN-ACK` 报文时携带的值。

Reference
---------

*   [TCP Fast Open](https://datatracker.ietf.org/doc/html/rfc7413)