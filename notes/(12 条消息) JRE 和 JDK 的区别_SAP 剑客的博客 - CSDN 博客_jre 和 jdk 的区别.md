> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/zhongguomao/article/details/91347743)

**1.** **定义**

**JRE(Java Runtime Enviroment)** **是 Java 的运行环境**。面向 Java 程序的使用者，而不是开发者。如果你仅下载并安装了 JRE，那么你的系统只能运行 Java 程序。JRE 是运行 Java 程序所必须环境的[集合](https://so.csdn.net/so/search?q=%E9%9B%86%E5%90%88&spm=1001.2101.3001.7020)，包含 JVM 标准实现及 Java 核心类库。它包括 Java 虚拟机、Java 平台核心类和支持文件。它不包含开发工具 (编译器、调试器等)。

**JDK(Java Development Kit)** 又称 J2SDK(Java2 Software Development Kit)，是 Java 开发工具包，它提供了 Java 的开发环境 (提供了[编译器](https://so.csdn.net/so/search?q=%E7%BC%96%E8%AF%91%E5%99%A8&spm=1001.2101.3001.7020) javac 等工具，用于将 java 文件编译为 class 文件) 和运行环境 (提 供了 JVM 和 Runtime 辅助包，用于解析 class 文件使其得到运行)。如果你下载并安装了 JDK，那么你不仅可以开发 Java 程序，也同时拥有了运 行 Java 程序的平台。JDK 是整个 Java 的核心，包括了 Java 运行环境 (JRE)，一堆 Java 工具 tools.jar 和 Java 标准类库 (rt.jar)。

**2.** **区别**

JRE 主要包含：java 类库的 class 文件 (都在 lib 目录下打包成了 jar) 和[虚拟机](https://so.csdn.net/so/search?q=%E8%99%9A%E6%8B%9F%E6%9C%BA&spm=1001.2101.3001.7020) (jvm.dll)；JDK 主要包含：java 类库的 class 文件 (都在 lib 目录下打包成了 jar) 并自带一个 JRE。那么为什么 JDK 要自带一个 JRE 呢？而且 jdk/jre/bin 下的 client 和 server 两个文件夹下都包含 jvm.dll(说明 JDK 自带的 JRE 有两个虚拟机)。

记得在环境变量 path 中设置 jdk/bin 路径麽？老师会告诉大家不设置的话 javac 和 java 是用不了的。确实 jdk/bin 目录下包含了所有的命令。可是有没有人想过我们用的 java 命令并不是 jdk/bin 目录下的而是 jre/bin 目录下的呢？不信可以做一个实验，大家可以把 jdk /bin 目录下的 java.exe 剪切到别的地方再运行 java 程序，发现了什么？一切 OK！(JRE 中没有 javac 命令，原因很简单，它不是开发环境) 那么有人会问了？我明明没有设置 jre/bin 目录到环境变量中啊？试想一下如果 java 为了提供给大多数人使用，他们是不需要 jdk 做开发的，只需 要 jre 能让 java 程序跑起来就可以了，那么每个客户还需要手动去设置环境变量多麻烦啊？所以安装 jre 的时候安装程序自动帮你把 jre 的 java.exe 添加到了系统变量中，验证的方法很简单，去 Windows/system32 下面去看看吧，发现了什么？有一个 java.exe。

**3.** **难点**

如果安装了 JDK，你的电脑就有两套 JRE(JRE 本身和 JDK 中的 JRE)，前面这套比后面那套少了 Server 端的 Java 虚拟机。

(1) 为什么 Sun 要让 JDK 安装两套相同的 JRE？这是因为 JDK 里面有很多用 Java 所编写的开发工具 (如 javac.exe、jar.exe 等)，而且都放置在 / lib/tools.jar 里。如果我们将 tools.jar 改名为 tools1.jar，然后运行 javac.exe，显示如下结 果：Exception in thread "main" java.lang.NoClassDefFoundError: com/sun/tools/javac/Main。这个意思是说，你输入 javac.exe 与输入 java -cp c:/jdk/lib/tools.jar com.sun.tools.javac.Main 是一样的，会得到相同的结果。从这里我们可以证明 javac.exe 只是一个包装器（Wrapper），而制作的目的是为了让开发者免于输入太长的指命。 而且可以发现 / lib 目录下的程序都很小，不大于 29K，从这里我们可以得出一个结论。就是 JDK 里的工具几乎是用 Java 所编写，所以也是 Java 应用 程序，因此要使用 JDK 所附的工具来开发 Java 程序，也必须要自行附一套 JRE 才行，所以位于 JDK 目录下的那套 JRE 就是用来运行一般 Java 程序 的。

(2) 如果一台电脑安装两套以上的 JRE，谁来决定呢？这个重大任务就落在 java.exe 身上。java.exe 的工作就是找到合适的 JRE 来运 行 Java 程序。java.exe 依照以下的顺序来查找 JRE：1) 自己的目录下有没有 JRE；2) 父目录有没有 JRE；3) 查询注册表： [HKEY_LOCAL_MACHINE/SOFTWARE/JavaSoft/Java Runtime Environment]。所以 java.exe 的运行结果与你的电脑里面哪个 JRE 被执行有很大的关系。

(3)JDK-->JRE-->Bin 目录下有两个文件夹：server 与 client，这是真正的 [jvm](https://so.csdn.net/so/search?q=jvm&spm=1001.2101.3001.7020).dll 所在。 jvm.dll 无法单独工作，当 jvm.dll 启动后，会使用 explicit 的方法 (就是使用 Win32 API 之中的 LoadLibrary() 与 GetProcAddress()来载入辅助用的动态链接库)，而这些辅助用的动态链接库 (.dll) 都必须位 于 jvm.dll 所在目录的父目录之中。因此想使用哪个 JVM，只需要设置 PATH，指向 JRE 所在目录下的 jvm.dll。

**4.** 最主流的 JDK 是 Sun 公司发布的 JDK，除了 Sun 之外，还有很多公司和组织都开发了自己的 JDK，例如 IBM 公司开发的 JDK，BEA 公司的 Jrocket，还有 GNU 组织开发的 JDK 等等。