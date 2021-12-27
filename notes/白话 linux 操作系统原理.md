> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247486330&idx=1&sn=e85e201059c1c5575481e03531b06212&chksm=fafde1d4cd8a68c235ef3cbed83bf0b6ac3a80052c0a492d8c2a217006cf4fa530f2f74f01c3&token=519132419&lang=zh_CN#rd)

虽然计算机相关专业，操作系统和计算机组成原理是必修课。但是大学时和真正从事相关专业工作之后，对于知识的认知自然会发生变化。还很有可能，一辈子呆在学校的老师们只是照本宣科，自己的理解也不深。所以今天我站在真正排查解决问题时的需要层面，用白话说一说 linux 操作系统的那些知识。  

本文整体采用的是类似递归调用的递进式结构。

**linux 内核的本质**  

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRlicaj3NoVtl5MyZZIr39JD2AMOgVhppo9nIVOwEfYgQxsoYzC9henrAF2OpC7Hib69vDuHNNVh0QYqQ/640?wx_fmt=png)

简单来说如上图所示，咱们平时的应用程序或者 linux 命令要和操作系统打交道，都要经过一个叫做 linux 内核的软件。所有的硬件操作都需要通过他。就像古代女子出嫁，都要有媒人。  

虽然现在都是婚姻自由，没有父母之命媒妁之言那一套。其实在古代，媒人对于整个古代历史上的婚姻而言，起到的正向的作用。现在社会欺骗的婚姻也不少。而古代媒人都要是对两家熟悉，或者做过背调，掌握了情况的人。多数都是门当户对，除了个别婚前就心有所属的，大多数还是过得很幸福的。毕竟多数人的标准都差不多：好看就行。我觉得自己老公长得就挺好看，只是出去打听了一下，95% 的人不这么认为![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRlicaj3NoVtl5MyZZIr39JD2AvMD6TgATfCrR9qDnxaeU7RtoKpbwUnaEia52TJziaqgKlCSVBIzD7Plg/640?wx_fmt=png)

本文不是为封装制度正名。想表达的是：媒人最初是一种保护机制。而 linux 之父林纳斯最初设计 linux 内核也是设计成一种保护机制。

![](https://mmbiz.qpic.cn/mmbiz_jpg/2tk5ianItRlicaj3NoVtl5MyZZIr39JD2AHgz1Pspv1V6SDD2ZWSUjSClVPWawgcBSoicoOfCdwl5TVciao8NdAYuQ/640?wx_fmt=jpeg)

为什么说 linux 内核是一种保护机制呢，这要从冯 · 诺依曼体系结构说起。

**冯 · 诺依曼体系结构**

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRliccfY8uUicHoibfkj1TpT97Gxdruu2L1uHemAp0RRUOaAyoGM5NkGkvOOiaiahnz297KJF3596EicpcGXQ/640?wx_fmt=png)

冯 · 诺依曼体系结构的要点是：  

  
计算机的数制采用二进制。计算机应该按照程序顺序执行。它采用存储程序方式，指令和数据不加区别，混合存储在同一个存储器中。数据和程序在内存中是没有区别的，它们都是内存中的数据。当 EIP 指针指向哪，CPU 就加载哪段内存中的数据。如果是不正确的指令格式，CPU 就会发生错误中断。

这里提到计算机是在顺序的执行指令，但是咱们明明可以一边听歌一边敲代码。这个除了在目前多 CPU 架构下可以实现，之前单 CPU 下也可以实现。因为有时钟分片。如果没有插入优先级高的任务，cpu 会在均匀的执行多项任务分片。因为 CPU 执行速度快，人可能完全感觉不到实际上是断续执行的。

不过我记得上大学的时候，03 年我买了一台笔记本用来打魔兽。当时可是用的市面上的顶配呢。有时候还是会一卡一卡的。可以切身感受到时钟分片。

如果出现错误，可以通过中断来处理。中断也需要等待时钟分片。好在 linux 内核的分片十分合理，让中断可以及时响应。  

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRliccfY8uUicHoibfkj1TpT97GxpSASVa1s0zY2e03iawfvXgzYynRowMrp4P2ClicYSI4CuxRVOBRSBkFw/640?wx_fmt=png)

在现代 CPU 的保护模式中，每个内存段都有其描述符。这个描述符记录着这个内存段的访问权限。在[《接下来一段时间会对大家进行网络通信的魔鬼训练 - 理解 socket》](http://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247486166&idx=1&sn=08a24c175c02a4b146dc52088d410599&chksm=fafde078cd8a696e616aebcb72aeef444e1e0008bf950a368933195f13c0a268face0d3ee371&scene=21#wechat_redirect)里我就提到过文件描述符。这里稍详细的解释下。

**文件描述符**

linux 系统中，一切皆文件。文件描述符是一个索引值，指向内核为每一个进程所维护的该进程打开文件的记录表。当程序打开一个现有文件或者创建一个新文件时，内核向进程返回一个文件描述符。在程序设计中，一些涉及底层的程序编写往往会围绕着文件描述符展开。

### **文件描述符、文件、进程间的关系**

#### 1. 描述：

*   每个文件描述符会与一个打开的文件相对应
    
*   不同的文件描述符也可能指向同一个文件
    
*   相同的文件可以被不同的进程打开，也可以在同一个进程被多次打开
    

#### 2. 系统为维护文件描述符，建立了三个表

*   进程级的文件描述符表
    
*   系统级的文件描述符表
    
*   文件系统的 i-node 表
    

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRliccfY8uUicHoibfkj1TpT97GxfnKWaCMPHPVAZVmicXOXibE1gQVehZRZibO4VpNzh8WpSFLiaO0nQjqXfA/640?wx_fmt=png)

#### 3. 通过这三个表，认识文件描述符

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRliccfY8uUicHoibfkj1TpT97Gxc3bzeK7qPPDh8pqIQoHqvT7bAvjBibibuB7fzXu8nPuIxUmIsgr0SfdA/640?wx_fmt=png)

*   在进程 A 中，文件描述符 1 和 30 都指向了同一个打开的文件句柄（#23），这可能是该进程多次对执行打开操作
    
*   进程 A 中的文件描述符 2 和进程 B 的文件描述符 2 都指向了同一个打开的文件句柄（#73），这种情况有几种可能，1. 进程 A 和进程 B 可能是父子进程关系; 2. 进程 A 和进程 B 打开了同一个文件，且文件描述符相同（低概率事件 =_=）；3.A、B 中某个进程通过 UNIX 域套接字将一个打开的文件描述符传递给另一个进程。
    
*   进程 A 的描述符 0 和进程 B 的描述符 3 分别指向不同的打开文件句柄，但这些句柄均指向 i-node 表的相同条目（#1936），换言之，指向同一个文件。发生这种情况是因为每个进程各自对同一个文件发起了打开请求。同一个进程两次打开同一个文件，也会发生类似情况。
    

### **文件描述符限制**

有资源的地方就有战争，文件描述符也是一种资源，系统中的每个进程都需要有文件描述符才能进行改变世界的宏图霸业。世界需要秩序，于是就有了 “文件描述符限制” 的规定。

#### 如下表：

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRliccfY8uUicHoibfkj1TpT97GxFYdtSuoiaIjHqNrpia0RHs80ibyBWjnhvGazicF1OlTGT6n22dnoDziaKtw/640?wx_fmt=png)

查看文件描述符限制也可以使用 linux 命令。  

*   找到需要检查的进程 id
    
*   查看该进程的资源，比如 fd 是描述符，limits 是限制。  
    
*   查看该进程的限制，如图，在 Max open files 那一行，可以看到当前设置中 soft 最大文件描述符的数量为 1024。
    

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRlicaj3NoVtl5MyZZIr39JD2AM3ibG6JzLicC26ptnSpWCz2MIZY0P4K7czmQ5abeMI2n9HWc0cDI90CA/640?wx_fmt=png)

在《提供一个排查性能问题的思路》里，我就提到过解决过 too many open files 问题。咱们今天来实际理解一下。执行下面命令：

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRliccfY8uUicHoibfkj1TpT97GxxItgOGn7iahWcBpzCl2jcKIIqopGmLjkOIjroKtMPXQ4DZQKMqmmnCQ/640?wx_fmt=png)

这就是一个进程实际占用的文件描述符和文件描述符数。超过系统设定值就会发生 too many open files 异常。这里大家应该可以切实理解一个文件描述符就是一个文件，文件描述符占用超限就是 too many open files 啦。  

**特殊的文件描述符**  

有三个特殊的文件描述符，分别是 0、1、2，对应每个进程的标准输入、标准输出和错误输出。每个进程启动时，操作系统就会给它分配这三个标准的文件描述符。咱们平时用的 console 控制台就是通过读写这三个文件来实现滴。

linux 基础里有介绍怎么重定向，下面一条命令可以将标准输出 1 和错误输出 2 重定向到一个文件：

![](https://mmbiz.qpic.cn/mmbiz_png/2tk5ianItRliccfY8uUicHoibfkj1TpT97GxUcl1LrdafVyItWics8HvY5T6K9vU3qOGqdmErQQtiaoOBRicWm5nwu9Wg/640?wx_fmt=png)

**总结**  

linux 的内存分段、中断机制和文件描述符限制都是内核的保护机制。当然这并不全面，还有其他机制。

本篇内容和[《网络通信之 Session 的历史血脉》](http://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247486245&idx=1&sn=494e71a356ad4bd26633b5ae5278215f&chksm=fafde18bcd8a689de3cfa45c0bbe629bc098d26a85106caff62b0bfa069055374515865a3b26&scene=21#wechat_redirect)、[《深入理解 MQ 生产端的底层通信过程 - 理解 channel》](http://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247486186&idx=1&sn=f21e17b21beb4c99cef0e076071ac859&chksm=fafde044cd8a69526aa3ec9edbe71ea5aa41de2ef236494201dc2f20701ed8abc6be1a72344f&scene=21#wechat_redirect)、[《接下来一段时间会对大家进行网络通信的魔鬼训练 - 理解 socket》](http://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247486166&idx=1&sn=08a24c175c02a4b146dc52088d410599&chksm=fafde078cd8a696e616aebcb72aeef444e1e0008bf950a368933195f13c0a268face0d3ee371&scene=21#wechat_redirect)、[《网络字节序列 - 大端序和小端序》](http://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247486210&idx=1&sn=fd7a60ca9b4e41f942dd0b2653228774&chksm=fafde1accd8a68ba4f41f19d3543984f47756e884eb395b295cc7f4f1cd0575bc0305fba5431&scene=21#wechat_redirect)、[《https 引起的跨域问题 - COE&casestudy》](http://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247486242&idx=1&sn=483566d3fd97f333d3ea114dbbe8f7d1&chksm=fafde18ccd8a689abfcb9959079a3cbfc4ecf98e52d34318bf9490422622bddb0c4b81ef2288&scene=21#wechat_redirect)、[《懂得三境界 - 使用 dubbo 时请求超过问题》](http://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247486103&idx=1&sn=943c5b7f3558df78164321806f273c8f&chksm=fafde039cd8a692f86f0ac97b0e1e05ece075de3e4f33e6a5ec90b269c51aed1963ff3107e0e&scene=21#wechat_redirect)、[《一个 http 请求进来都经过了什么 (2021 版)》](http://mp.weixin.qq.com/s?__biz=MzUzNjAxODg4MQ==&mid=2247485988&idx=1&sn=efad5a030bc0a933ac5af2ee09f3c6c4&chksm=fafde08acd8a699c198d0d65c413273f05f2796cfd873160c2ba81ed3f33aebfaccb62234cb7&scene=21#wechat_redirect)是一个系列。等全部串联起来，之前很多让人望而却步的生产问题，会有了大致的排查方向吧~

参考文章：https://segmentfault.com/a/1190000009724931