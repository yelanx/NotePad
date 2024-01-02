> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [silaoa.github.io](https://silaoa.github.io/2020/2020-04-25-Cygwin%E7%B3%BB%E5%88%97%EF%BC%88%E5%8D%81%E4%BA%8C%EF%BC%89%EF%BC%9A%E4%BA%86%E8%A7%A3X.html)

> 记录分享 Cygwin、Linux、编程等计算机技术

本文共 4500 余字，预计阅读时间 14 分钟，本文同步发布于知乎（账号 silaoA）和微信公众号平台（账号伪码人）。  
关注学习了解更多的 Cygwin、Linux 技术。

前面连续多篇折腾了终端和命令行，本篇先初步了解 X，并敬请期待下一篇：在 Windows 上折腾 X。

图形环境不是计算机系统与生俱来的，现在习惯了 Windows、Mac OS 的用户，对于图形用户界面（GUI）得心应手，然而早期的计算机系统只有个黑框框，仅少数专业人员能够驾驭。一般认为，是施乐公司帕洛阿尔托研究中心 (Xerox PARC) 在 1973 年首次研发出图形用户界面，具备了现代图形环境基本元素。这个激动人心的发明，还没有被施乐公司好好利用起来挣钱的时候，很快被苹果、微软等多家公司 “借鉴”，伴随着 PC 发展，各家 GUI 系统群雄逐鹿，没有统一标准。

[](#规范 "规范")规范
--------------

1984 年 MIT 大学创造了 X 规范，就类似 TCP/IP 协议栈一样。本义是**在 UNIX 系统构建图形化视窗系统的设计方案**，准确名称叫 “X Window System”，注意拼写**不是** “Windows”，与微软、与 Windows 没任何关系。  
X 规范在 1987 年形成了第 11 版（Version 11），1994 年发布第 11 版的第 6 次发行（Release 6），确切版本名即为 “X11R6”，后来成为 UNIX 上图形视窗系统标准规范，也简称“X11” 或者“X”，目前最新版本是 2012 年发布的“X11R7.7”。  
![](https://pic4.zhimg.com/80/v2-33167403ede56f21e529c1a520ed4487_720w.png)

“X11” 采用了 C/S 的架构，在其设计下，整个图形视窗系统主要分为 3 个部分：

*   X Server（X 服务器）。X Server 一方面负责和设备驱动交互，监听显示器和键盘鼠标，另一方面响应 X Client 需求传递键盘、鼠标事件、（通过设备驱动）绘制图形文字等。**反直觉之一，X Server 运行在本地**。
*   X Client（X 客户端）。X Client 也叫 X 应用程序，负责实现程序逻辑，在收到设备事件后计算出绘图数据，由于本身没有绘制能力，只能向 X Server 发送绘制请求和绘图数据，告诉 X Server 在哪里绘制一个什么样的图形。**X Client 可以和 X Server 在同一个主机上，也可以通过 TCP/IP 网络连接。**
*   Window Manager（窗口管理器，简称 WM），或者叫合成器（Compositor）。多个 X Client 向 X Server 发送绘制请求时，各 X Client 程序并不知道彼此的存在，绘制图形出现重叠、颜色干扰等问题是大概率事件，这就需要一个管理者统一协调，即 Window Manager，它掌管各 X Client 的 Window（窗口）视觉外观，如形状、排列、移动、重叠渲染等。**反直觉之二，Window Manager 并非 X Server 的一部分，而是一个特殊的 X Client 程序**。

3 个部分， **X Server 是整个 X Window System 的中心**，协调 X 客户端和窗口管理器的通信。

![](https://pic2.zhimg.com/80/v2-46b872d09eb863a65d3064dae6cdf67a_720w.png)

“X11” 充分遵循 UNIX 设计哲学，尽可能简单，除非必要绝不新增功能，其实也意味着粗糙、原始；提供机制而非具体策略，如菜单、按钮等元素细节并不做规定，交给窗口管理器、客户端程序等自主实现，于是不同的实现风格各异。任何遵循了 X11 规范的客户端程序和服务器程序配合起来，即可正常运行，X11 得益其开放性，迅速发展，成为 UNIX 系统的事实标准。

假定多个 X 客户端程序及窗口管理器在主机 A 上，某个 X Server（如下文`X.Org Server`）运行在主机 B 上，程序运行过程可简化如下过程。

1.  某个 X 客户端进程启动，向主机 B 发送连接请求，目标地址可通过命令行或配置文件指定，如果给定的地址已有 X Server 正在监听端口，则进行下一步；
2.  主机 B 上的 X Server 返回一个连接正确响应，X Server 也可以配置接受或拒绝某些地址的请求；
3.  X 客户端向 X Server 发送渲染请求及窗口界面数据；
4.  X Server 一方面将窗口界面数据交给显示驱动计算渲染缓冲，另一方面综合各个 X 客户端的渲染请求，计算更新区域，但它并不知道如何将多个窗口 “合成到一起”，于是将更新区域事件发给窗口管理器；
5.  窗口管理器了解到需要在屏幕上重新合成一块区域，再向 X Server 发送整个屏幕的绘制请求和数据；
6.  X Server 将绘图数据交给显示驱动计算所有渲染缓冲，并最终绘制图形；
7.  运行过程中，X Server 可能收到主机 B 上的鼠标、键盘事件，经计算后，X Server 决定发给哪个 X 客户端（即获得焦点）；
8.  X 客户端收到鼠标、键盘事件后，回调事件处理，并计算界面该如何更新；
9.  循环第 3~8，直至 X 客户端收到关闭事件，进程终止、连接断开。

![](https://pic1.zhimg.com/80/v2-c9ea4979bd85c8bc9db0b57fb3358eee_720w.png)  
以上过程，主机 A 和 B 的 CPU 架构、操作系统可能都不相同，若 A 和 B 是同一个主机，就相当于在本地绘图、显示了。

[](#XFree86和X-Org等实现 "XFree86和X.Org等实现")XFree86 和 X.Org 等实现
-----------------------------------------------------------

**X11 只是规范，并不是代码实现。**MIT 另外只给了工具包和参考实现，参考实现的意思是不考虑性能只演示工作原理。

1992 年，`XFree86`项目顺利开展，作为 X Window System 的一个早期开源实现，兼容 IBM PC 机（`XFree86`中 86 的含义），得到广泛应用，项目地址 [http://www.xfree86.org](http://www.xfree86.org/)。至 2004 年，由于项目内部争论和许可证分歧，`XFree86`从 GPL 协议改为 XFree86 License 发布，项目从开放走向封闭。

2004 年，X.Org 基金会成立，开始领导 X 项目，不仅推动 X 规范本身发展，还在`XFree86` 4.4 RC2 版基础上开发了 X 规范的另一个实现——`X.Org Server`，原来`XFree86`上很多开发者也转向了`X.Org Server`，由于更加开放而被多数 GNU/Linux 发行版使用，项目官网 [https://www.x.org](https://www.x.org/)，源代码库地址 [http://cgit.freedesktop.org/xorg/xserver](http://cgit.freedesktop.org/xorg/xserver)，2019 年发布的稳定版为 1.20.5。

*   在 X Client 方面，`X.Org Server`实现了 2 个开发库，`Xlib`和`XCB`，便于开发者编写 X 应用程序。
*   在 X Server 方面，`X.Org Server`实现了 “X Window System” 核心规范及多个扩展组件。
*   在 Window Manager 方面，`X.Org Server`继承`XFree86`项目的`twm`。
*   其他，实现了一个 X Display Manager（X 显示管理器）——`xdm`。通常透过字符终端登录 Linux 主机时，用户仅能获得字符环境，而 X 显示管理器实现 XDMCP 规范（X Display Manager Control Protocol），为多用户提供图形登录、会话管理服务，允许用户登录时自主选择使用哪个 Window Manager 等。

除上 X Server，X11 系统客户端、窗口管理器也有着很多种实现。

*   客户端。`Xlib`及其替代者`XCB`开发库过于靠近底层，于是就出现了进一步封装、抽象级别更高的 GUI 库，如 QT、GTK 等，比方说前者提供绘制点线面的方法，后者提供绘制按钮、滚动条、下拉菜单等控件的方法，更高层次的抽象对于简化开发工作的重要性不言而喻。在众多 X 客户端程序中，有一个比较著名——`xterm`，它是基于 X 的终端模拟器，兼容 VT102，也是 X 研发初期所计划的少数客户端程序之一。GNU/Linux 发行版中设备 / dev/tty1~tty6 对应着 6 个全屏纯字符的终端模拟器，通过 Ctrl-Alt-F1~F6 切换，显示能力有限；如果运行到图形环境（通过 Ctrl-Alt-F7 切换），一般都集成了`xterm`，可配置的特性十分丰富，其他的诸如 Gnome Terminal、Konsole、xfce-term 等终端模拟器，或基于`xterm`，或参考`xterm`，增加了更多高级功能，如 tab 标签页、图片显示等。
*   窗口管理器。常见如`Metacity`、`Mutter`、`KWin`、 `vtwm`、`Xfwm`、`Compiz`等，功能、风格各异，有的注重简洁高效，有的注重外观酷炫。
*   服务器。`X.Org Server`占绝对统治地位，但也有其他衍生变种或移植，如`XQuartz`以及`Cygwin/X`、`Xming`、`WeirdX`，它们所支持的系统平台、底层库也都不一样。

需注意的是，**X Window System 诞生比 Linux 早，也不是 UNIX 或 Linux 内核的一部分，而是用户态的软件组件，用户在系统上可以选择启用或禁用 X**。

[](#桌面环境 "桌面环境")桌面环境
--------------------

X Window System 各个部分都有着多种实现，X 规范本身特别偏底层，加之 GNU/Linux 自由开放的特点，有不少项目在 X 规范基础之上进一步封装，并把上述各组件、图标、主题、及其他特色组件打包在一起，形成开箱即用的桌面环境（Desktop Environment），常见如`Gnome`、`KDE`、`Unity`、`XFCE`、`LXDE`等，不一一列举，每种桌面环境都有不少用户群体，所支持的系统平台、底层库也各不一样。

另值得一提的是，中国武汉深之度科技公司基于 Debian（早先基于 Ubuntu）打造的 GNU/Linux 发行版 Deepin，拥有自己的桌面环境`DDE`，即 Deepin Desktop Environment，Deepin 系统在 [DistroWatch](https://distrowatch.com/) 上排名比较靠前，`DDE`也同时支持其他 GNU/Linux 发行版。

![](https://pic3.zhimg.com/80/v2-cd14804122fc40f2a7d4bd688b463ed4_720w.png)

[](#应用场景 "应用场景")应用场景
--------------------

X11 规范的模块化设计，最大的好处是 X Client 和 X Server 相互独立，并不需要了解彼此所处的硬件、软件环境。常见的 X 应用程序诸如`xterm`、`输入法`、`Web浏览器`、`Office套件`等等，部署在一台 Linux 主机上，如果这个主机也同时部署了 X Server，那么运行这些 X 应用程序毫无障碍，这是作为桌面计算机的常见应用场景。

另一种应用场景，Linux 主机通常作为服务器使用，未配备比较好的图形显卡等硬件，也没有必要部署 X Server，从终端登录到 Linux 主机也仅有字符界面，那 X 应用程序该如何运行呢？可以在图形显卡等硬件设备比较好的 Windows/Mac 主机上部署 X Server，保持与 Linux 主机网络连通，在 Linux 主机上写好配置文件，配置好 X Server 地址和 X11 请求转发（X11 Forwarding），从任意一个终端登录到 Linux 主机并启动 X 客户端应用程序，就可以使程序的图形界面显示在 Windows/Mac 主机上，并与用户交互。事实上，在 1990 年代初期，就有这样的硬件设备，称为 “X 终端机（X terminal）”，专门部署 X Server，将远端 UNIX 主机上的图形界面程序显示出来，这也正是 MIT 研发 X 的初衷之一。

![](https://pic4.zhimg.com/80/v2-368802cb105d57833eb6206501b6ddd5_720w.jpeg)

从场景描述看出，与常规 Client/Server 所不同的是，**通常 X Server 部署在本地用户主机上，监听、调控本地用户主机的硬件设备，而 X Client 可能部署在远端服务器 / 嵌入式设备上，也可能在本地。**

一方面，X 工作在用户空间，X 客户端程序不能直接访问显示驱动、X Server 与 X 客户端及窗口管理器之间繁琐的通信流程，拉低了绘图效率；另一方面，通过扩展、补丁手段允许在本地直接访问显示驱动，又造成稳定性问题。总之，X 的设计随着新技术发展而显得臃肿、过度复杂，背负了沉重的历史包袱。

[](#Wayland "Wayland")Wayland
-----------------------------

2008 年，红帽公司（RedHat）的开发者 Kristian Høgsberg 利用业余时间搞起了 Wayland 项目，2010 年 Wayland 加入了 Freedesktop.org 项目，项目官网为 [https://wayland.freedesktop.org](https://wayland.freedesktop.org/)，代码开源地址 [https://cgit.freedesktop.org/wayland/wayland](https://cgit.freedesktop.org/wayland/wayland)。

Wayland 是一个新的图形窗口系统方案，一套旨在取代 X 的新规范。与 X 最大的不同是，Wayland **将 X 中的 Server 和窗口管理器整合到一起作为服务端，称为合成器（Compositor）**，架构上只分了客户端和合成器两大部件，有没有觉得终于看着舒服了一些。

*   客户端（Wayland Client），直接计算各自界面的渲染缓冲数据，客户端程序需要和渲染库（如 OpenGL）链接。
*   合成器（Wayland Compositor），汇总所有客户端的渲染数据，实现各界面窗口 “合成”，最后交给显示驱动绘图。  
    Wayland 项目提供了两套底层库 libwayland-server 和 libwayland-client，简化图形程序开发，还给了一个 Compositor 参考实现——Weston。

假定多个 Wayland 客户端程序在主机 A 上，某个 Wayland Compositor（如 Weston）运行在主机 B 上。

1.  某个客户端进程启动，向主机 B 发送连接请求，目标地址可通过命令行或配置文件指定，如果给定的地址已有 Compositor 正在监听端口，则进行下一步；
2.  主机 B 上的 Compositor 返回一个连接正确响应，Compositor 也可以配置接受或拒绝某些地址的请求；
3.  客户端自行生成 UI 界面和渲染缓冲，不需要向 Compositor 发送绘制请求，但需要发送更新区域事件，告知渲染缓冲中更新了哪些内容；
4.  Compositor 综合各客户端的区域更新事件，重新合成整个屏幕，并交给显示驱动绘制图形；
5.  运行过程中，Compositor 可能收到主机 B 上的鼠标、键盘事件，经计算后，Compositor 决定发给哪个客户端（即获得焦点）；
6.  客户端收到鼠标、键盘事件后，回调事件处理；
7.  循环第 3~6，直至客户端收到关闭事件，进程终止、连接断开。

![](https://pic3.zhimg.com/80/v2-6e7d3dbf89b077392772be249da37430_720w.png)  
Wayland 适用于桌面设备，也适用于移动设备，原来基于 X 的 GUI 库、桌面环境很快适配了对 Wayland 的支持，如 Qt、Gnome、KDE、Compiz 等，Ubuntu 更是早在 2010 年就将 Unity 切换到适配 Wayland。Wayland 依赖了较多 Linux 内核技术，不易移植到其他系统平台。

[](#XWayland "XWayland")XWayland
--------------------------------

Wayland 开发者在`X.Org Server`基础上打上系列补丁，称为`XWayland`，目的是在 Wayland 规范之上搞成新的 X Server，在 Wayland 环境中运行 X 客户端程序时，Wayland 合成器（如 Weston）调用 XWayland 进行服务，以便在过渡到 Wayland 过程中保持对 X 的兼容性。

[](#Mir "Mir")Mir
-----------------

2013 年，Ubuntu 背后的开发商 Canonical 公司搞出来 Mir 项目，计划用 Mir 取代 X 并兼容 X 客户端程序，并且同时支持桌面设备和移动设备，统一用户体验。这种搞分裂的行为招致了批评，万万没想到的是，2017 年 Canonical 公司又放弃了 Mir 项目并加大对 Wayland 的支持。

[](#其他 "其他")其他
--------------

*   苹果公司在 MacOS 和 iOS 中，使用 Quartz 作为自己的图形窗口系统。
*   Android 系统上，使用 SurfaceFlinger 作为自己的图形窗口系统。
*   历史上还有一些激进的方案，逐渐被淘汰。

尽管有这样那样的问题，X 仍然是 GNU/Linux、UNIX 上的主力，Wayland 作为强有力的竞争对手，在远程桌面方面亦存在问题，重度依赖 Linux 内核技术不易移植到其他系统平台。在微软的 WSL 崛起之时，X 的优势倒可以尽情发挥出来。  
敬请期待下一篇，在 Windows 上折腾 X。

*   [腾讯科技：电脑操作系统图形用户界面 38 年进化史](https://tech.qq.com/a/20100603/000234.htm)
*   [https://en.wikipedia.org/wiki/X_Window_System](https://en.wikipedia.org/wiki/X_Window_System)
*   [http://www.xfree86.org](http://www.xfree86.org/)
*   [https://www.x.org](https://www.x.org/)
*   [https://en.wikipedia.org/wiki/X.Org_Server](https://en.wikipedia.org/wiki/X.Org_Server)
*   [https://invisible-island.net/xterm](https://invisible-island.net/xterm)
*   [https://wayland.freedesktop.org](https://wayland.freedesktop.org/)

*   [Cygwin 系列（十一）：折腾终端 2](https://silaoa.github.io/2020/2020-01-13-Cygwin%E7%B3%BB%E5%88%97%EF%BC%88%E5%8D%81%E4%B8%80%EF%BC%89%EF%BC%9A%E6%8A%98%E8%85%BE%E7%BB%88%E7%AB%AF2.html)
*   [Cygwin 系列（十）：折腾终端 1](https://silaoa.github.io/2019/2019-12-29-Cygwin%E7%B3%BB%E5%88%97%EF%BC%88%E5%8D%81%EF%BC%89%EF%BC%9A%E6%8A%98%E8%85%BE%E7%BB%88%E7%AB%AF1.html)
*   [Cygwin 系列（五）：Shell 命令行初体验](https://silaoa.github.io/2019/2019-03-13-Cygwin%E7%B3%BB%E5%88%97%EF%BC%88%E4%BA%94%EF%BC%89%EF%BC%9AShell%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%88%9D%E4%BD%93%E9%AA%8C.html)
*   [Python 操作 Excel 文件（0）：盘点](https://silaoa.github.io/2019/2019-12-03-Python%E6%93%8D%E4%BD%9CExcel%E6%96%87%E4%BB%B6%EF%BC%880%EF%BC%89%EF%BC%9A%E7%9B%98%E7%82%B9.html)
*   [Cygwin 系列（九）：Cygwin 学习路线](https://silaoa.github.io/2019/2019-06-16-Cygwin%E7%B3%BB%E5%88%97%EF%BC%88%E4%B9%9D%EF%BC%89%EF%BC%9ACygwin%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%BA%BF.html)
*   [微软 WSL——Linux 桌面版未来之光](https://silaoa.github.io/2019/2019-05-08-%E5%BE%AE%E8%BD%AFWSL%E2%80%94%E2%80%94Linux%E6%A1%8C%E9%9D%A2%E6%9C%AA%E6%9D%A5%E4%B9%8B%E5%85%89.html)
*   [伪码人专栏目录导航](https://zhuanlan.zhihu.com/p/102460964)
*   [silaoA 的博客. https://silaoa.github.io](https://silaoa.github.io/)

**如本文对你有帮助，或内容引起极度舒适，欢迎分享转发或点击下方捐赠按钮打赏** ^_^