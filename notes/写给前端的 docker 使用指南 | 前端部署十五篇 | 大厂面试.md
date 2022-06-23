> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [q.shanyue.tech](https://q.shanyue.tech/deploy/docker.html#%E6%9C%AF%E8%AF%AD)

> docker 使应用部署更加轻量，可移植，可扩展，更好的环境隔离也更大程度地避免了生产环境与测试环境不一致的巨大尴尬。

`docker` 使应用部署更加轻量，可移植，可扩展，更好的环境隔离也更大程度地避免了生产环境与测试环境不一致的巨大尴尬。

由于 `docker` 轻便可移植的特点也极大促进了 `CI/CD` 的发展。

[#](#术语) 术语
-----------

`docker` 的架构图如下

![](https://docs.docker.com/engine/images/architecture.svg)

从图中可以看出几个组成部分

*   `docker client`: 即 `docker` 命令行工具
*   `docker host`: 宿主机，`docker daemon` 的运行环境服务器
*   `docker daemon`: `docker` 的守护进程，`docker client` 通过命令行与 `docker daemon` 交互
*   `image`: 镜像，可以理解为一个容器的模板，通过一个镜像可以创建多个容器
*   `container`: 最小型的一个操作系统环境，可以对各种服务以及应用容器化，是镜像的运行实例
*   `registry`: 镜像仓库，存储大量镜像，可以从镜像仓库拉取和推送镜像

[#](#安装-docker) 安装 docker
-------------------------

在本地安装 docker/docker-compose，通过 [Docker Desktop (opens new window)](https://www.docker.com/get-started/) 下载 docker 后，双击安装即可。

![](https://static.shanyue.tech/images/22-05-31/clipboard-3773.19c457.webp)

如果是个人服务器且为 centos，可参考 [安装 docker (opens new window)](https://shanyue.tech/op/docker.html#%E5%AE%89%E8%A3%85-docker)。

[#](#底层原理) 底层原理
---------------

`docker` 底层使用了一些 `linux` 内核的特性，大概有 `namespace`，`cgroups` 和 `ufs`。

### [#](#namespace) namespace

`docker` 使用 `linux namespace` 构建隔离的环境，它由以下 `namespace` 组成

*   `pid`: 隔离进程
*   `net`: 隔离网络
*   `ipc`: 隔离 IPC
*   `mnt`: 隔离文件系统挂载
*   `uts`: 隔离 hostname
*   `user`: 隔离 uid/gid

### [#](#control-groups) control groups

也叫 `cgroups`，限制资源配额，比如某个容器只能使用 `100M` 内存

### [#](#union-file-systems) Union file systems

`UnionFS` 是一种分层、轻量级并且高性能的文件系统，支持对文件系统的修改作为一次提交来一层层的叠加。`docker` 的镜像与容器就是分层存储，可用的存储引擎有 `aufs`，`overlay` 等。

关于分层存储的详细内容可以查看官方文档 [docker: About storage drivers (opens new window)](https://docs.docker.com/storage/storagedriver/)

[#](#镜像) 镜像
-----------

镜像是一份用来创造容器的配置文件，而容器可以视作最小型的一个操作系统。

**`docker` 的镜像和容器都使用了 `unionFS` 做分层存储，镜像作为只读层是共享的，而容器在镜像之上附加了一层可写层，最大程度地减少了空间的浪费，详见下图**

![](https://docs.docker.com/storage/storagedriver/images/sharing-layers.jpg)

### [#](#镜像仓库与拉取) 镜像仓库与拉取

大部分时候，我们不需要自己构建镜像，我们可以在[官方镜像仓库 (opens new window)](https://hub.docker.com/explore/) 拉取镜像

可以简单使用命令 `docker pull` 拉取镜像。拉取镜像后可以使用 `docker inspect` 查看镜像信息

### [#](#构建镜像与发布) 构建镜像与发布

但并不是所有的镜像都可以在镜像仓库中找到，另外我们也需要为我们自己的业务应用去构建镜像。

使用 `docker build` 构建镜像，**`docker build` 会使用当前目录的 `dockerfile` 构建镜像**，至于 `dockerfile` 的配置，参考下节。

`-t` 指定标签

当构建镜像成功后可以使用 `docker push` 推送到镜像仓库

[#](#dockerfile) Dockerfile
---------------------------

在使用 `docker` 部署自己应用时，往往需要自己构建镜像。`docker` 使用 `Dockerfile` 作为配置文件构建镜像，简单看一个 `node` 应用构建的 `dockerfile`

### [#](#from) FROM

基于一个旧有的镜像，格式如下

### [#](#add) ADD

把目录，或者 url 地址文件加入到镜像的文件系统中

### [#](#run) RUN

执行命令，由于 `ufs` 的文件系统，它会在当前镜像的顶层新增一层

### [#](#cmd) CMD

指定容器如何启动

**一个 `Dockerfile` 中只允许有一个 CMD**

[#](#容器) 容器
-----------

镜像与容器的关系，类似于代码与进程的关系。

*   `docker run` 创建容器
*   `docker stop` 停止容器
*   `docker rm` 删除容器

### [#](#创建容器) 创建容器

基于 `nginx` 镜像创建一个最简单的容器：启动一个最简单的 http 服务

使用 `docker run` 来启动容器，`docker ps` 查看容器启动状态

其中:

*   `-d`: 启动一个 `daemon` 进程
*   `--name`: 为容器指定名称
*   `-p host-port:container-port`: 宿主机与容器端口映射，方便容器对外提供服务
*   `nginx:alpine`: 基于该镜像创建容器

此时在宿主机使用 `curl` 测试容器提供的服务是否正常

那如果要进入容器环境中呢？使用 `docker exec -it container-name` 命令

### [#](#容器管理) 容器管理

`docker ps` 列出所有容器

`docker port` 查看容器端口映射

`docker stats` 查看容器资源占用

[#](#作业) 作业
-----------

*   初阶: 了解 docker 常见操作，如构建镜像、运行容器、进入容器执行命令
*   高阶: docker 原理，如何模拟 docker 隔离环境及限制资源
*   面试: Dockerfile、Image、Container 的区别

Last Updated: 6/9/2022, 5:44:32 PM