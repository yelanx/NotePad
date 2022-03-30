> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7078924628525056007)

事情是这样的，直接开讲
-----------

面试官：npm run xxx 的时候，发生了什么？讲的越详细越好。

我（心想，简单啊）： 首先，DNS 解析, 将域名解析成 IP 地址，然后 TCP 连接, TCP 三次握手...

面试官：停停，我问的不是从 URL 输入到页面展现到底发生什么？，是 npm run xxx 的时候，发生了什么。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7dba997e203b42659441c575576ae9f6~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

我（尴尬，条件反射地以为是问的八股文）：emmmm，我记得 npm run xxx 的时候，首先会去项目的 package.json 文件里找 scripts 里找对应的 xxx，然后执行 xxx 的命令，例如启动 vue 项目 npm run serve 的时候，实际上就是执行了 vue-cli-service serve 这条命令。(好险，幸好这点常识我还是懂的)

**package.json 文件**

```
{
  "name": "h5",
  "version": "1.0.7",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve"
   },
}

复制代码
```

面试官：嗯，不错，那 为什么 不直接执行`vue-cli-service serve`而要执行`npm run serve` 呢？

我（支支吾吾）：emm，因为 `npm run serve` 比较简短，比较好写。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/16bc179eeb3d4afd9a428ab0bfd0dc58~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

面试官：你再想想。

我（啊？不对吗，对哦，我想起来了）： 因为 直接执行`vue-cli-service serve`，会报错，因为操作系统中没有存在`vue-cli-service`这一条指令

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1af585a1261341be8d2a81ade704eda2~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/70a9d2e89c424035940f55d42c1cf687~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

面试官： 哦，对对对，不错不错，哟西哟西！

我（嘿嘿，稳了，这次我要 30k）: 嘻嘻！

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab2c235ff7c64cd1a006adab53d9733a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

面试官：那既然`vue-cli-service`这条指令不存在操作系统中，为什么执行`npm run serve`的时候，也就是相当于执行了`vue-cli-service serve` ，为什么这样它就能成功，而且不报指令不存在的错误呢？

我（啊？要不你还是把我鲨了吧，不想再勉强作回答）：不好意思，这个我还没了解过。

面试官：emmm，好吧，没关系，我们做下一道算法题吧：....

....

后面无关此次文章的内容，就省略过了。

面试官: 好的，此处面试到此结束，我们会在一周内回复您的面试结果

哔哔哔...（电话挂断）

唉。看来是凉了

为什么执行`npm run serve`的时候，这样它就能成功，而且不报指令不存在的错误呢？

我赶紧问问了大佬朋友这一过程到底是发生了什么

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8a8c6ac1c434c00830da041ff642bf6~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

经过一番讨论，终于找到了答案。

不服输的我，赶紧回拨了面试官的电话号码。

我：喂，面试官，您好，我已经找到答案了，可以麻烦您再听一下吗？

面试官：嗯，可以啊，请讲。

我：我们在安装依赖的时候，是通过 npm i xxx 来执行的，例如 `npm i @vue/cli-service`，npm 在 安装这个依赖的时候，就会`node_modules/.bin/` 目录中创建 好`vue-cli-service` 为名的几个可执行文件了。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e13e40deb844d8283adaf92d2815441~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8a38ee7871fc425e859e2d3fa7e0cb86~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

.bin 目录，这个目录不是任何一个 npm 包。目录下的文件，表示这是一个个软链接，打开文件可以看到文件顶部写着 `#!/bin/sh` ，表示这是一个脚本。

由此我们可以知道，当使用 `npm run serve` 执行 `vue-cli-service serve` 时，虽然没有安装 `vue-cli-service`的全局命令，但是 npm 会到 `./node_modules/.bin` 中找到 `vue-cli-service` 文件作为 脚本来执行，则相当于执行了 `./node_modules/.bin/vue-cli-service serve`（最后的 serve 作为参数传入）。

面试官：可以啊，真不错，但是我还想继续问问，你说. bin 目录下的文件表示软连接，那这个 bin 目录下的那些软连接文件是哪里来的呢？它又是怎么知道这条软连接是执行哪里的呢？

我（窃喜，这个我们刚刚也讨论了）：我们可以直接在新建的 vue 项目里面搜索 vue-cli-service

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/43bf5cf7d27e40d98e7c87ddabdda765~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

可以看到，它存在项目最外层的 **package-lock.json** 文件中

从 package-lock.json 中可知，当我们 npm i 整个新建的 vue 项目的时候，npm 将 bin/vue-cli-service.js 作为 bin 声明了。

所以在 npm install 时，npm 读到该配置后，就将该文件软链接到 ./node_modules/.bin 目录下，而 npm 还会自动把 node_modules/.bin 加入 $PATH，这样就可以直接作为命令运行依赖程序和开发依赖程序，不用全局安装了。

假如我们在安装包时，使用 `npm install -g xxx` 来安装，那么会将其中的 bin 文件加入到全局，比如 create-react-app 和 vue-cli ，在全局安装后，就可以直接使用如 vue-cli projectName 这样的命令来创建项目了。

面试官：搜噶，也就是说，npm i 的时候，npm 就帮我们把这种软连接配置好了，其实这种软连接相当于一种映射，执行 npm run xxx 的时候，就会到 node_modules/bin 中找对应的映射文件，然后再找到相应的 js 文件来执行。

我（疯狂点头）：嗯嗯，是的，就是这样

面试官：我有点好奇。刚刚看到在 node_modules/bin 中 有三个 vue-cli-service 文件。为什么会有三个文件呢？

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f743c3ed17ff4a7fa5ec3006f16f26dc~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

我：如果我们在 cmd 里运行的时候，windows 一般是调用了 `vue-cli-service.cmd`，这个文件，这是 windows 下的批处理脚本：

```
@ECHO off
GOTO start
:find_dp0
SET dp0=%~dp0
EXIT /b
:start
SETLOCAL
CALL :find_dp0

IF EXIST "%dp0%\node.exe" (
  SET "_prog=%dp0%\node.exe"
) ELSE (
  SET "_prog=node"
  SET PATHEXT=%PATHEXT:;.JS;=;%
)

endLocal & goto #_undefined_# 2>NUL || title %COMSPEC% & "%_prog%"  "%dp0%\..\@vue\cli-service\bin\vue-cli-service.js" %*
复制代码
```

所以当我们运行`vue-cli-service serve`这条命令的时候，就相当于运行 `node_modules/.bin/vue-cli-service.cmd serve`。

然后这个脚本会使用 node 去运行 `vue-cli-service.js`这个 js 文件

由于 node 中可以使用一系列系统相关的 api ，所以在这个 js 中可以做很多事情，例如读取并分析运行这条命令的目录下的文件，根据模板生成文件等。

```
# unix 系默认的可执行文件，必须输入完整文件名
vue-cli-service

# windows cmd 中默认的可执行文件，当我们不添加后缀名时，自动根据 pathext 查找文件
vue-cli-service.cmd

# Windows PowerShell 中可执行文件，可以跨平台
vue-cli-service.ps1
复制代码
```

面试官：原来如此，不错嘛小伙子，短短时间内就掌握清楚了，看来学习能力很强，不错不错，我很看好你，我会催 hr 尽快回复你的。先这样了，拜拜

我（欣喜若狂，功夫不负有心人啊）：好啊，好啊，拜拜

哔哔哔...（电话挂断）

过了三十分钟....

今天是个好日子, 心想的事儿都能成, 今天是个好日子, 打开了家门咱迎春风...(手机铃声响起)。

我：喂，您好。

hr：您好，我是 xxx 公司的 hr，根据你面试的优秀表现，恭喜你获得了我司的 offer，经过我最大的努力，我给你争取到了最大的薪资，薪资是月薪 3500，您看满意吗？

我：....

哔哔哔....(电话挂断)

tmd，c

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d69ed630f18b4b91897351c1662f22af~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

总结
--

1.  运行 npm run xxx 的时候，npm 会先在当前目录的 node_modules/.bin 查找要执行的程序，如果找到则运行；
2.  没有找到则从全局的 node_modules/.bin 中查找，npm i -g xxx 就是安装到到全局目录；
3.  如果全局目录还是没找到，那么就从 path 环境变量中查找有没有其他同名的可执行程序。

参考文章
----

[blog.51cto.com/u_15077533/…](https://link.juejin.cn?target=https%3A%2F%2Fblog.51cto.com%2Fu_15077533%2F4531157 "https://blog.51cto.com/u_15077533/4531157") [juejin.cn/post/697172…](https://juejin.cn/post/6971723285138505765 "https://juejin.cn/post/6971723285138505765")