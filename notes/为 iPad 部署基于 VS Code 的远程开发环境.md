> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [sspai.com](https://sspai.com/post/60456)

> code-server 实际上就是专门为浏览器设计优化的 VS Code，可以直接通过浏览器访问。

### **Matrix 精选**

[Matrix](https://sspai.com/matrix) 是少数派的写作社区，我们主张分享真实的产品体验，有实用价值的经验与思考。我们会不定期挑选 Matrix 最优质的文章，展示来自用户的最真实的体验和观点。

文章代表作者个人观点，少数派仅对标题和排版略作修改。

Apple Pencil 和 Procreate 让我们可以用 iPad 创作富有艺术感的画作，GoodNotes 和 Notability 让我们能高效整理课堂笔记，Bear、Ulysses 配合 Magic Keyboard 还能帮我们随时随地撰写文章。但是，基于 ARM 架构的 iPad 让开发者在 iPad 上**借助桌面级工具进行本地开发工作**几乎无法实现。  

不过，基于远程服务器的云端开发环境最近如雨后春笋般涌现，这为用 iPad 作为本地工具，连接远程服务器的 Linux 环境进行开发的愿望成为可能。微软在去年就发布了基于 Azure 云服务器的 [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/)（改名前曾叫做 Visual Studio Online），前几天 GitHub 同样也发布了类似的 [GitHub Codespaces](https://github.com/features/codespaces)…… 而 iPad Pro 配合最新带有触控板的 Magic Keyboard 的码字体验，让我们用 iPad 写工程级代码指日可待！

![](https://cdn.sspai.com/editor/u_spencerwoo/15893040424486.gif)来自 GitHub Codespaces 官网的 Demo 介绍动画

微软和 GitHub 的两款在线开发环境，都是基于备受开发者喜爱的 VS Code 的。不过，目前前者 Visual Studio Codespaces 需要付费订阅昂贵的 Azure 云服务，后者 GitHub Codespaces 尚在内测中，像我们这种普通用户现在就想使用 iPad 尝鲜该怎么办？如果我们有自己的服务器，那么，现在我们就可以去上面部署开源的 [cdr/code-server](https://github.com/cdr/code-server)，本文我就来简单介绍一下怎样**只使用 iPad** 来部署我们的 `code-server`，并借助 **VSApp** 来改善连接 `code-server` 使用在线版 VS Code 的体验。🚀

工作原理
----

在开始之前，为了帮助大家理解，我先来简单介绍借助 `code-server` 使用 iPad 进行远程开发的具体工作原理。

![](https://cdn.sspai.com/editor/u_spencerwoo/15893040424531.png)使用 iPad 连接远程服务器上部署的 code-server

首先，我们前面提到的 `code-server` 实际上就是专门为浏览器设计优化的 VS Code，可以直接通过浏览器访问。在远程 Linux 服务器上面部署 `code-server` 之后，`code-server` 实际上就相当于一个「中转站」，为我们提供一个直接在 Linux 服务器上面修改文件、访问数据库、执行指令等等操作的「窗口」，而由于这一「窗口」同样在 Linux 服务器上面运行，使用任何一个浏览器都可以打开，所以 iPad 也不例外。这样，我们就能够借助在远程服务器上运行的 `code-server`，直接使用远程服务器上面的各种资源（包括服务器上强大的原生 Linux 环境、CPU / GPU 等计算资源、「无尽」的电量等等）进行开发工作。（当然，这也要求我们 iPad 端全程拥有网络连接。）

可以发现，我们这样进行部署之后，不仅 iPad 上面可以直接用它来进行开发，我们更换任意一台电脑，都可以直接连接自己服务器上的在线版 VS Code，在完全一致的环境下进行开发，**实现我们自己的 Codespaces。**

在远程服务器上部署 code-server
---------------------

### 准备工作

下面我们直接开始，全程使用 iPad 在服务器上部署 `code-server`。我们将需要：

*   一台有网络连接的远程服务器（我使用的是阿里云位于北京的服务器，系统是 Ubuntu 18.04 LTS）
*   在 iPad 上能够登录远程服务器的 SSH 工具（推荐使用 [Blink Shell](https://blink.sh/)）

[](/app/Blink%20Shell%3A%20Mosh%20%26%20SSH%20Client)[Blink Shell: Mosh & SSH Client](/app/Blink%20Shell%3A%20Mosh%20%26%20SSH%20Client)[相关文章](/app/Blink%20Shell%3A%20Mosh%20%26%20SSH%20Client)[](/app/Blink%20Shell%3A%20Mosh%20%26%20SSH%20Client)[](/d/nL0VZn)

这样就 OK 了。我们使用后者登录远程服务器，开始操作。

### 下载 code-server

首先，我们需要下载 `code-server`，**为了后面和 VSApp 更好的配合，我们这里需要下载 [3.1.1 版本的 code-server](https://github.com/cdr/code-server/releases/tag/3.1.1)。**

![](https://cdn.sspai.com/editor/u_spencerwoo/15893040424544.png)下载 Linux x86_64 版本的 code-server

我们可以直接在远程服务器上执行下面的命令来下载 `.tar.gz` 压缩包，请注意命令末尾的 URL 地址，请记得按照你在 `code-server` Release 页面实际复制的地址进行填写：

```
curl -o code-server-3.1.1.tar.gz https://github.com/cdr/code-server/releases/download/3.1.1/code-server-3.1.1-linux-x86_64.tar.gz
```

之后，我们使用 `tar` 工具解压下载得到的压缩包，之后进入解压好的文件目录中：

```
# 解压缩 tar.gz 压缩包
tar xf code-server-3.1.1.tar.gz

# 进入解压得到的文件夹
cd code-server-3.1.1-linux-x86_64
```

![](https://cdn.sspai.com/editor/u_spencerwoo/15893040424559.PNG)在当前目录下解压缩下载得到的 code-server

我们将得到这样的一个文件夹，其中 `code-server` 是 Linux 可执行文件，我们直接运行即可在远程服务器上开启 `code-server`。  

### 配置运行 code-server

为了后面能够更加方便的用 VSApp 使用 `code-server`，我们这里为我们的 `code-server` 设置一个密码。直接在服务器上运行下面的命令：

```
export PASSWORD="{YOUR_CODE_SERVER_PASSWORD}"
```

将上面命令中的 `{YOUR_CODE_SERVER_PASSWORD}` 替换为你的密码即可。这里我们是在服务器上面设置了名为 `PASSWORD` 的环境变量为 `{YOUR_CODE_SERVER_PASSWORD}`，这样后面 `code-server` 启动后就会直接读取这一环境变量并将之作为你的登录密码。

接下来，我们执行下面的命令启动 `code-server` 并开放给公网允许使用服务器地址访问：

```
./code-server --host "0.0.0.0"
```

![](https://cdn.sspai.com/editor/u_spencerwoo/15893040424572.PNG)使用自定义密码运行 code-server

￼之后，`code-server` 进程就在远程服务器上启动并开始监听 `8080` 端口了，同时可以看到 `code-server` 正在使用我们自定义的密码（custom password）作为认证密码。**这里，我们还需要将 `code-server` 默认监听的 8080 端口在服务器防火墙中设置开放**，之后我们才能直接通过 `{服务器 IP 地址}:{code-server 端口}` 的 URL 地址在浏览器中直接访问 `code-server`。

如果一切顺利，此时我们在 iPad 上面用浏览器直接访问 `{服务器 IP 地址}:{code-server 端口}`，输入刚刚设置的密码，就能够打开一个完整的在线版 VS Code：

![](https://cdn.sspai.com/editor/u_spencerwoo/15893040424587.png)直接在 iPad 上用 Safari 打开在线版 VS Code

￼这一在线版 VS Code（`code-server`）可以直接打开服务器上的任何文件夹，使用 VS Code 的终端执行各种命令，甚至可以将服务器上面运行的程序监听端口映射到本地，让 Safari 直接打开。另外，`code-server` 是一个原生的可执行文件，所以你可以直接将这一文件放在服务器中的任何位置、加入系统 `$PATH`、设置 `systemd` 服务等等。如果你拥有已经备案的域名，或服务器在国外，你也可以用 Nginx 将 `code-server` 进行反向代理，使用自己的域名绑定，并添加 HTTPS 支持……

不过，虽然我们现在可以直接访问 `code-server` 了，但直接在 Safari 浏览器里面使用 `code-server` 体验还是比较糟糕，上面地址栏和下面外接键盘出现的菜单栏都让直接在浏览器里使用 `code-server` 非常不顺畅，**因此我们最好使用下面介绍的 VSApp 来管理、连接 `code-server` 使用。**

使用 VSApp 优化 iPad 上使用 code-server 的体验
------------------------------------

VSApp 是一款专门为 iPad 优化的用来连接 `code-server` 的 iOS 原生应用。我们使用 VSApp 可以直接连接我们自己的 `code-server` 实例，自动登录，打造畅快的在线 VS Code 体验。同学们可以直接前往 [VSApp - code-server 官网](https://vseditor.app/) 下载 iPadOS 版本的 VSApp。

[](/app/VSApp%20-%20code-server)[VSApp - code-server](/app/VSApp%20-%20code-server)[相关文章](/app/VSApp%20-%20code-server)[](/app/VSApp%20-%20code-server)[](/d/n9OPoK)

VSApp 原本是仅支持连接至它的开发者所部署的 `code-server` 上面的，因此也包含了 App 内购，最近 VSApp 才更新支持自部署服务器的连接。既然我们都已经自己部署了自己的 `code-server`，那么我们直接在菜单中选择「Self Hosted Server」，并依次填写：

*   我们刚刚部署 `code-server` 的：
    *   `code-server URL` 刚刚在 Safari 中打开的 URL：`{服务器 IP 地址}:{code-server 端口}`
    *   `Instance password` 刚刚开启 `code-server` 时设置的密码
*   以及，我们服务器自己的：
    *   `Host`：服务器 IP 地址
    *   `Username`：默认登录用户名
    *   `Port`：登录 SSH 端口（默认 22 端口）
    *   `Authentication`：登录服务器时使用的密码或公钥私钥对

再点击保存即可。

![](https://cdn.sspai.com/editor/u_spencerwoo/15893040424600.png)在 VSApp 中填入我们自己部署的服务器信息

￼之后，我们就应该看到 VSApp 为我们加载了和刚刚 Safari 中访问时一样的在线版 VS Code。由于 VSApp 是一个独立的 iOS 应用，并专门为 `code-server` 进行了优化，因此我们不仅能够直接进入 App 即可登录，还不会出现像 Safari 直接访问时的出现的工具栏下滑问题。

![](https://cdn.sspai.com/editor/u_spencerwoo/15893040424613.png)在 VSApp 中加载的我们自己部署的在线版 VS Code

小结
--

VS Code 是一个强大的多功能代码编辑器，它不仅广受开发者的喜爱，许许多多普通用户也直接使用 VS Code 进行 [Markdown 文章撰写](https://sspai.com/post/53327)、用来管理协作、进行数据分析，甚至可以用来 [听网易云音乐歌曲](https://marketplace.visualstudio.com/items?item>发表知乎文章</a>、<a href=) 等等。

**关联阅读：**[一款工具搞定 5 个应用场景：VS Code 上手指南](https://sspai.com/post/60117)

现在，iPad 开放了触控板 cursor 支持，发布了 Magic Keyboard 键盘，越来越强调电脑属性的 iPadOS 也逐渐成为许多用户的日常「笔记本」。使用本文方法部署 `code-server`，让 iPad 增加了更多的可能性，同时由于服务器掌握在我们自己手中，我们的开发环境还能够保证处处一致。相信未来会有更多类似 GitHub Codespaces、Visual Studio Codespaces 以及 `code-server` 的产品，让我们能够不拘泥于设备本身的性能、环境、硬件条件，随时随地用熟悉的环境进行工作。感谢阅读。

> 下载少数派 [客户端](https://sspai.com/page/client) 、关注 [少数派公众号](https://sspai.com/s/J71e) ，了解更精彩的数字生活 🍃