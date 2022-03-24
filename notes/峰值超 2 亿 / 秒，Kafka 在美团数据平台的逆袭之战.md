> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/HR2Q19yb62bBTmyGBzgXLg)

 ![](http://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8YRIaicYx5pzj5Cxwick8DamnOgbTJu96QTibKyHEDZt1815yOV1r27oZ6HgoYTEYWYLRz4jIV4iasHgg/0?wx_fmt=png) ** dbaplus 社群 ** 围绕 Database、BigData、AIOps 的企业级专业社群。资深大咖、技术干货，每天精品原创文章推送，每周线上技术分享，每月线下技术沙龙，每季度 Gdevops&DAMS 行业大会. 956 篇原创内容  公众号

本文将介绍 Kafka 在美团数据平台的实践，主要内容包括：

*   Kafka 在美团数据平台的发展现状和面临的挑战，主要是海量数据下如何保证读写延迟的问题，以及大规模的集群管理与优化；
    
*   面对上述挑战，美团所做的优化实践；
    
*   未来美团数据平台 Kafka 的优化方向。
    

**一、现状和挑战**

**

1、现状

**

首先了解一下 Kafka 在美团数据平台的现状。

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEocxb3ky5m68CAzsmM1xb3mq32qmeZGX6bDDcQ64sT4v6KLOial5GjfdA/640?wx_fmt=png)

图 1-1 Kafka 在美团数据平台的现状  

如图 1-1 所示，蓝色部分描述了 Kafka 在数据平台定位为数据存储层。主要的职责是做数据的缓存和分发，它会将收集到的 binlog 日志分发到不同的数据系统里，这些日志来源于业务日志、用户行为日志以及业务的数据库。这里的数据系统包括通过 ODS 入仓提供离线计算使用、直接供实时计算使用、通过 DataLink 同步到日志中心，以及 OLAP 做分析使用。

Kafka 在美团的集群规模总体机器数已经超过了 7500 台，单集群的最大机器数也已经到了 1500 台。数据规模上，天级消息量已经超过了 21P，天级的消息条数达到了 11.3 万亿，天级消息量峰值也达到了 2.46 亿 / 秒。

随着集群规模的增大，数据量的增长，Kafka 面临的挑战也是愈发的严峻。下面看一下具体的挑战有哪些。

**2、挑战**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoxZX8pAWsHPKkWRQTZGse03cOOwbprh8uHsEMInTnAzFhTic57k5KfsQ/640?wx_fmt=png)

图 1-2 Kafka 在美团数据平台面临的挑战

如图 1-2 所示，具体的挑战可以概括为两部分：

**第一部分是慢节点影响读写，**这里慢节点参考了 HDFS 的一个概念，具体定义指的是读写延迟 tp99 大于 300ms 的 broker。影响慢节点的原因有三个，第一个是集群负载不均衡会导致局部热点，就是整个集群的磁盘空间很充裕或者 ioutil 很低，但部分磁盘即将写满或者 ioutil 打满；第二个是 pagecache 容量不足会导致磁盘 IO 瓶颈。比如说，80GB 的 pagecache 在 170MB/s 的写入量下仅能缓存 8 分钟的数据量。那么如果消费的数据是 8 分钟前的数据，就有可能触发磁盘读；第三个是 consumer 线程模型缺陷会导致延时指标的失真。例如当消费的分区处于同一 broker 时，tp90 可能小于 100ms，但是当他们处于不同 broker 时，tp90 可能会大于 100ms。

**第二部分是大规模集群运维的复杂性，**具体表现有四个方面，一是不同 topic 之间会相互影响，某些或某个 topic 的流量突增，或者某些消费者的回溯读会影响整体集群的稳定性；二是 Kafka 原生的 broker 粒度的指标不够健全，问题的根因分析变得很困难；三是故障的感知无法做到及时，故障处理成本很高；四是 Rack 级别的故障会造成部分分区不可用。

**二、读写延迟优化**

接下来介绍一下针对读写延迟问题，美团的数据平台做了哪些优化。首先从宏观上将受影响因素分为应用层和系统层，然后详细介绍应用层和系统层存在的问题，并给出对应的解决方案，包括流水线加速、fetcher 隔离、迁移取消和 cgroup 资源隔离等，下面具体来看一下各种优化方案是如何实现的。

**1、概览**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoNH7fCFmRbH5kV9awLiaU8HZfBfu234CTwgRHqibvrHV2p1vxMHzFpk6Q/640?wx_fmt=png)

图 2-1 Kafka 概览图  

如图 2-1，这张图是针对读写延迟碰到的问题，以及对应优化方案的一个概览图。我们把整个受影响的因素分为应用层和系统层。

**应用层，**主要表现在系统设计的不合理导致，包括消费者端的单线程模型存在缺陷导致运维指标失真，并且单 consumer 消费的分区数是不受限制的，当消费的分区数增多的时候可能会引起回溯读，因为消费能力不足就无法跟上实时最新的数据；其次是 broker 端，broker 端主要表现在负载不均衡上，具体表现是磁盘使用率不均衡方面。

我们针对此做了磁盘均衡，但磁盘均衡需要使用分区迁移，分区迁移又引入了一些新的问题，包括迁移只能按批提交，这存在长尾问题，以及迁移 fetcher 和实时拉取 fetcher 存在资源竞争，分区迁移的 fetcher 会影响实时消费。

**系统层，**主要包括三个方面，一是 pagecache 的容量不足会导致磁盘读写，磁盘读写的性能显著慢于内存，而且容量不足时还会导致 pagecache 污染，pagecache 污染后，磁盘读和回溯读会影响实时读；另一方面，Kafka 目前使用的 disk 主要是 HDD，HDD 是比较符合顺序读写的场景。但是对于随机读写的场景，它的性能是不足的；最后由于 CPU 的资源竞争，在美团这边为了提高资源的利用率，IO 密集型的服务（比如 Kafka）会和 CPU 密集型的服务（比如实时作业）混布，混布其实是存在资源竞争的，也会影响读写的延迟。

针对刚才提到的应用层和系统层存在的各种问题，我们这边分层的去解决。对于应用层提到的每一点问题都会有针对性的解决方案，比如说限流、流水线加速、资源隔离等。针对系统层存在的问题，我们做了 cgroup 的优化以及物理核的隔离来保证当 CPU 实时计算的飙升时不会影响读写延迟。

**2、应用层**

**1）磁盘均衡**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEozEymJN5CwbKhvnGMaYE01yppMDIK07HGmbGwfSOcnXsUvRAjmd2xLA/640?wx_fmt=png)

图 2-2 Kafka 应用层磁盘均衡

下面介绍一下读写延迟在应用层遇到到的问题，磁盘热点导致磁盘利用率不均衡，它会带来两个问题：

*   磁盘实时读写延迟变高，比如说 tp99 超过 300ms 就已经造成慢节点了；
    
*   集群整体利用率不足，虽然集群容量非常充裕，但是部分磁盘已经写满，这个时候甚至会导致某些分区暂时中断服务。
    

针对这两个问题我们做了基于空闲磁盘优先这样一个分区迁移计算计划，整个计划分为 5 个点。如图 2-2 所示，首先会有一个组件叫 rebalancer，rebalancer 通过目标的使用率和 Kafka monitor 组件不断从 Kafka broker 集群上报上来的当前磁盘的使用状况这两类指标持续生成具体的分区迁移计划，执行迁移计划并检查进度；然后 rebalancer 会向 zookeeper 的 reassign 节点提交刚才生成的迁移计划，Kafka 的 controller 收到这个 reassign 事件之后会向整个 Kafka broker 集群提交 reassign 事件，然后 Kafka broker 集群让整体磁盘利用率趋于均衡值这样一个目标执行磁盘迁移计划。

如图 2-2 所示，对于所有的 disk，三个分区属于一个相对均衡的状态，那么如果有一个四个分区的 disk，就会把其中一个分区迁移到另外两个分区的 disk 上，最终尽可能地保证整体磁盘利用率是均衡的。但是 Kafka 的分区迁移只能是按组提交的，在执行分区迁移过程中碰到了许多新的问题，下面会继续介绍这些问题是怎么解决的。

分区迁移存在一个迁移效率不足的问题，因为是按组提交的，在上一批没有完成之前，下一批无法开始提交，这样就会导致整体迁移进度受阻，进而对读写请求造成影响。

针对迁移效率问题以及带来的它带来的影响，我们主要做了三点改进：第一点是做流水线加速，流水线加速能够保证长尾分区不影响整体迁移进度；第二点是迁移取消，在原生 Kafka 版本中，当一个分区迁移被提交后，是无法中断的，只能等他迁移完成，那么如果他在影响一个实时读写请求的时候，如果它迟迟不能完成，可能另一个实时读写的请求一直都会受到影响；第三点是做 fetcher 隔离，Kafka 在做分区迁移的时候会利用 follower 通过最近读去拉数据同步，当发起最近读的迁移请求和某一个实时写请求共享同一个 fetcher 的时候，迁移分区的读请求会影响实时分区的读请求，后面会进一步详细描述具体的问题和对应的解决方案。

**2）迁移优化**

 **① 流水线加速**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoZTpACJIdhZxJ8icwItOlyZc9GBDhUcxiafxpWib8QysCaicbibwV3sGZA5g/640?wx_fmt=png)

图 2-3 流水线加速

针对长尾分区问题，我们主要是做了流水线加速，如图 2-3 所示，箭头以上原生 Kafka 版本只支持按组提交，比如说一批提交了四个分区，当 tp4 这个分区一直卡着无法完成的时候，后续所有分区都无法继续进行。采用流水线加速之后，即使 tp4 这个分区还没有完成，当其它三个分区已经完成的时候，后续就可以继续提交新的分区。可以看出在相同的时间内，原有的方案受阻于 tp4 没有完成后续所有分区都没办法完成，但是在新的方案中，tp4 分区已经迁移到 tp11 分区了。图中虚线代表了一个无序的时间窗口，主要用于控制并发，目的是为了和原有的按组提交的个数保持一致，避免过多的迁移影响读写请求服务。

 **② 迁移取消**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEokLWRf0D6KG9794iaddFCTlY99mQnrRvJmJgFh1gsUJVgRqDbruXk6Vg/640?wx_fmt=png)

图 2-4 迁移取消

如图 2-4 所示，箭头左侧描述了因为迁移影响的三种线上类型。第一种是因为迁移会触发最旧读，同步大量的数据，在这个过程中会首先将数据回刷到 pagecache 上，那么可能会污染 pagecache，进而导致某个实时读的分区发生 cache miss，就会导致实时读触发磁盘度进而影响读写请求；第二类和第三类分别描述的是当存在某些异常节点导致迁移 hang 住的时候，想对 topic 做某些操作，比如对 topic 扩容，例如在午高峰时由于流量上涨想对 topic 扩容，实际上这个时候扩容是无法完成的。因为在 Kafka 迁移过程中这些操作都被限制住。第三个和第二个有些类似，它的主要问题是当目标节点挂了，这个时候 topic 扩容也是无法完成的，用户可能一直忍受读写请求受影响，直到迁移完成。针对这种场景，线上无法忍受由于长时间迁移导致读写延迟变高的。

针对上面提到的各种问题，我们支持了一个功能叫迁移取消。当遇到这类问题时，管理员可以调用迁移取消命令，中断正在迁移的分区，针对第一种场景，pagecache 就不会被污染，实时读得以保证；在二、三类场景中，因为迁移取消，扩容得以完成。

迁移取消必须要删除那些还没有完成的分区，大量的删除会导致磁盘 IO，称为性能瓶颈进而影响读写，因此在这里我们针对迁移取消做了平滑删除，避免因大量删除影响性能问题。

 **③ fetcher 隔离**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEokibPIZ04ngJnzB7TeicUlfXOCSRjglhFwkHL15q2pIkIicSJZDuLBjPjw/640?wx_fmt=png)

图 2-5 fetcher 隔离

如图 2-5，绿色代表实时读，红色代表延时读。当某一个 follower 的实时读和延时读共享同一个 fetcher 时，延时读会影响实时读。因为每一次延时读的数据量是显著大于实时读的，而且延时读容易触发磁盘读，可能数据已经不在 pagecache 中了，显著的拖慢了 fetcher 的拉取效率。

针对这种问题我们做的策略叫 fetcher 隔离。也就是说所有 isr 的 follower 共享 fetcher，所有非 isr 的 follower 共享 fetcher，这样就能保证所有 isr 中的实时读不会被非 isr 的回溯读所影响。

**3）Consumer 异步化**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEocTQGickBicp7mo3ziawhnfoKUeMxVkeohw09AVgCgk4t5QjxxpWRZyQXA/640?wx_fmt=png)

图 2-6 Kafka-broker 分阶段延时统计模型

首先来了解一下 Kafka-broker 分阶段延时统计模型，当一个 Kafka 的 producer 或 consumer 请求进入到 Kafka-broker 时，首先由 processor 将请求写入 RequestQueue 里面，然后 RequestHandler 就会从 RequestQueue 源源不断地去拉取请求进行处理，在 RequestQueue 中的等待时间是 RequestQueueTime，RequestHandler 具体的执行时间为 LocalTime，当 RequestHandler 执行完毕后会将请求扔到 DelayedPurgatory 组件中，这个实际上就是一个延时队列。这个延时队列当触发某一个延时条件完成了以后会把请求塞到 ResponseQueue 中，在 DelayedPurgatory 队列持续的时间为 RemoteTime，processor 会不断的从 ResponseQueue 中将数据拉取出来发往客户端，标红的 ResponseTime 是可能会被客户端影响的，因为如果客户端接收能力不足，那么 ResponseTime 就会一直持续增加，从 Kafka-broker 角度，每一次请求总的延迟叫 RequestTotalTime 包含了刚才所有流程分阶段计时总和。

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEokLuLpdO8NcnKf7giacktOlvYcc1DYZnl84eO0UnVvAZctdeqAAlsNiaw/640?wx_fmt=png)

图 2-7 Consumer 异步化

主要问题是因为 Kafka 原生 consumer 基于 NIO 的单线程模型存在缺陷。如图 2-7 所示，在 phase1，user 首先在调用 poll 请求时，当本地无数据时，同时向 broker1、broker2 和 broker3 发送请求，实际上 broker1 的数据先回来了，Kafka Client 立即将数据写入 CompleteQueue，这个时候立即返回，不会再拉取 broker2 和 broker3 的数据，此时 user 线程会直接从 CompleteQueue 中读取数据，然后直接返回。此时 broker2 和 broker3 服务端可能已经处理好，数据已经准备就绪。user 线程会继续调用 poll，访问下一批请求，可是因为 CompleteQueue 依然存在 broker1 上次拉取的数据，这时 user 线程直接返回了，这样就会导致 broker2 和 broker3 中已就绪的数据一直得不到拉取。如图中 phase2，因为单线程模型存在缺陷导致 waitFetch 这部分时长变大，导致 Kafka-broker 延时指标不断升高。

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoTRdAx4nekKUOVibsmpARZhGtECZDvIaRDxIrJNDmKtUicvbUuY4IOnoA/640?wx_fmt=png)

图 2-8 引入异步拉取线程

针对这个问题我们的改进是引入异步拉取线程。异步拉取线程会及时的拉取就绪的数据，避免服务端延时指标受影响，而且原生 Kafka 并没有限制同时拉取的分区数，我们在这里做了限速，避免 GC 和 OOM 的发生。异步线程在后台持续不断地拉取数据放到 CompleteQueue 中。

**3、系统层**

**1）Raid 卡加速**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoe1DJ5D0XCo3weGPw9sDlqdYPFYMweCNiaz8DFdUz15sZatE6HicibraCg/640?wx_fmt=png)

图 2-9 Raid 卡加速

众所周知，Kafka 的写入借助了 Zero Copy 技术将数据直接写入 pagecache，但是随着随机读写并发量的提升，随机写导致的性能不足问题就会显现出来。表现是随机写入的延时会显著升高，针对这个问题我们引入了 Raid 卡。Raid 卡有一个好处是自带缓存，而且 Raid 卡使用的是 Raid-0 模式，并没有冗余，与 pagecache 类似，在这一层会继续做 merge，把数据 merge 成更大的 block 写入 disk。更加充分利用顺序写 HDD 的带宽，借助 Raid 卡保证了随机写的性能是比较稳定的。

**2）cgroup 隔离优化**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEovake1Scx94aF4Ogk4a0TFf6bPAgATjS5WcSqPlicnho8wYmWxwlnSUA/640?wx_fmt=png)

图 2-10 cgroup 隔离

在介绍 cgroup 的隔离优化之前需要提到的背景是，为了提高资源利用率，美团数据平台将 IO 密集型应用和 CPU 密集型应用混布。IO 密集型应用在这里指的就是 Kafka，CPU 密集型应用在这里指的是 Flink 和 Storm，但是原有的隔离策略存在两个问题：首先是物理核本身会存在资源竞争，在这个物理核下，共享的 L1cache 和 L2cache 都存在竞争，当实时平台 CPU 飙升时会导致 Kafka 读写延时受到影响；另外，Kafka 的 HT 跨 NUMA，增加内存访问耗时，如图 2-10 所示，跨 NUMA 节点是通过 QPI 去做远程访问，而这个远程访问的耗时是 40ns。

针对这两个问题我们改进了隔离策略，针对物理核的资源竞争，我们新的混布策略 Kafka 是独占物理核的，也就是说在新的隔离策略中，不存在同一个物理核被 Kafka 和 Flink 同时使用；然后是保证 Kafka 的所有超线程处于同一侧的 NUMA，避免 Kafka 跨 NUMA 带来的访问延时。通过新的隔离策略，Kafka 的读写延时不再受 Flink CPU 飙升的影响。

**4、混合层 - SSD 新缓存架构**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoECyT7g69DxbC87k3T5FOAK5EW93QsjOF0IJt7x3OvPcv30ibz4ooqTA/640?wx_fmt=png)

图 2-11 混合层 SSD 新缓存架构

首先来了解一下 Kafka 的数据消费模型，Kafka 利用操作系统提供的 ZeroCopy 技术处理数据读取请求，pagecache 容量充裕时数据直接从 pagecache 拷贝到网卡，有效降低了读取延时。但是实际上往往 pagecache 的的容量是不足的，因为它不会超过一个机器的内存，容量不足时，ZeroCopy 就会触发磁盘读，磁盘读不仅显著变慢，还会污染 pagecache 影响其他读写。

如图 2-11 中左半部分所示，当一个延迟消费者去拉取数据时，发现 pagecache 中没有它想要的数据，这个时候就会触发磁盘读，磁盘读后会将数据回写到 pagecache，导致 pagecache 污染，自己读写延迟变慢同时也会导致另一个实时消费受影响，因为对于实时消费而言，它一直读的是最新的数据，最新的数据按正常来说时不应该出发磁盘读的。

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEolxP0CDMOETnb1KwtN6Ms2ibHDSACxggbMGEWKRiaF4KHCIKtHFBUhY7Q/640?wx_fmt=png)

图 2-12 SSD 新缓存架构方案选型

针对这个问题，我们这边在做方案选型时有两种方案，

**方案一：**读磁盘时不回写 pagecache，比如使用 DirectIO，不过 Java 并不支持；

**方案二：**在内存和 HDD 之间引入中间层，比如 SDD，如图 2-12 所示，随着读取并发的增加，SSD 的性能并不会显著降低，非常适合我们的使用场景。

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoWx9tw98Q2gHudfPNicUiaObYBFuFYu0lt0EKrLszbwkKtEadjq81eooA/640?wx_fmt=png)

图 2-13 SSD 新缓存架构决策

**针对 SSD 的方案也有两种选型:**

**方案一：**可以基于操作系统的内核实现，这种方案 SSD 与 HDD 存储空间按照固定大小分块，并且 SSD 与 HDD 建立映射关系，同时会基于数据局部性原理，cache miss 后数据会按 LRU 和 LFU 替换 SSD 中部分数据，业界典型方案包括 OpenCAS 和 FlashCache。优势是数据路由对应用层透明，对应用代码改动量小，并且社区活跃可用性好；但是问题是局部性原理并不满足 Kafka 的读写特性，而且缓存空间污染问题并未得到根本解决，因为它会根据 LRU 和 LFU 去替换 SSD 中的部分数据。

**方案二：**是基于 Kafka 的应用层去实现，具体就是 Kafka 的数据按照时间维度存储在不同设备上，对于近实时数据直接放在 SSD 上，针对较为久远的数据直接放在 HDD 上，然后 leader 直接根据 offset 从对应设备读取数据。这种方案的优势是他的缓存策略充分考虑了 Kafka 的读写特性，确保近实时的数据消费请求全部落在 SSD 上，保证这部分请求处理的低延迟，同时从 HDD 读取的数据不回刷到 SSD 防止缓存污染，同时由于每个日志段都有唯一明确的状态，因此每次请求目的明确，不存在因 cache miss 带来的额外性能开销。同时劣势也很明显，需要在 server 端代码上进行改进，涉及的开发以及测试工作量较多。

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEo88WQnicyNqAOzaa8RYR150l2FbGhk090CxTmVyuMtJfRwEUxmiaKTA1Q/640?wx_fmt=png)

图 2-14 SSD 新缓存架构具体实现

下面来介绍一下 SSD 新缓存架构的**具体实现。**

*   首先新的缓存架构会将 log 内的多个 segment 按时间维度存储在不同的存储设备上，如图 2-14 中的红圈 1，新缓存架构数据会有三种典型状态，一种叫 only cache，指的是数据刚写进 SSD，还未同步到 HDD 上；第 2 个是 cached，指数据既同步到了 HDD 也有一部分缓存在 SSD 上；第三种类型叫 withoutCache，指的是同步到了 HDD 但是 SSD 中已经没有缓存了；
    
*   然后后台异步线程持续地将 SSD 数据同步到 HDD 上；
    
*   随着 SSD 的持续写入，当存储空间达到阈值后，会按时间顺序删除距当前时间最久的数据，因为 SSD 他的数据空间也是有限的；
    
*   副本可根据可用性要求灵活开启是否写入 SSD；
    
*   从 HDD 读取的数据是不会回刷到 SSD 上的，防止缓存污染。
    

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoWBiccuiczCCo12DojMheicyWkkGMPjFs5rQWCcBehJ3nZ437OpicKB2aLg/640?wx_fmt=png)

图 2-15 SSD 新缓存架构细节优化

介绍了具体实现之后，再来看一下**细节优化。**

首先是关于日志段同步，就是刚才说到的 segment，只同步 Inactive 的日志段，Inactive 指的是现在并没有在写的日志段，低成本解决数据一致性问题；

其次是做同步限速优化，在 SSD 向 HDD 同步时是需要限速的，同时保护了两种设备，不会影响其他 IO 请求的处理，向 SSD 写入数据也是需要限速的，因为 SSD 的使用寿命是有限的。

**三、大规模集群管理优化**

了解了读写延迟优化之后，下面来看一下 Kafka 在美团数据平台是如何保证大规模集群的稳定性的。

**1、隔离优化**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEons2Ew6GoHKicMlUfS5YvYlpLSPUx7E51ib2NXfUJC9mQqMxvB50KgibIw/640?wx_fmt=png)

图 3-1 隔离优化

由于 Kafka 服务于多个业务，这些业务的 topic 混布在一起的话很有可能造成不同业务的不同 topic 之间相互影响。例如 broker 如果和 controller 混布在一起，当 broker 负载明显变高的时候，会导致 controller 无法及时处理请求，从而可能会造成整个集群发生故障，因为元数据的变更请求无法发送出去。

针对这些相互影响的问题，我们从业务、角色和优先级三个维度来做隔离优化。

*   第一点是业务隔离，如图 3-1 所示，每一个大的业务会有一个独立的 kafka 集群，比如外卖、团购、优选；
    
*   第二点是分角色隔离，这里 Kafka 的 broker 和 controller 以及他们依赖的组件 zookeeper 是部署在不同机器上的，避免之间相互影响；
    
*   第三点是可以分优先级，有的业务可能它的 topic 可用性等级特别高，那么我们就可以给他划分为 VIP 集群，给他更多的资源冗余去保证可用性。
    

**2、全链路监控**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoOT69icM3qvSmTI21aDXgPIib7icYZbAkhnUNMcmthZ7IWJPiaxEwfd7CoA/640?wx_fmt=png)

图 3-2 全链路优化

随着集群规模的增大分区数变多，读写的客户端也会变多，Kafka 当前提供的 broker 端粒度的延时指标在很多情况下无法真实反映某些客户端是否慢，还有一类问题是当集群发生故障时，如何能及时得到感知和处理。

针对这两个问题，我们做了全链路监控这样一个项目。把 Kafka 核心组件以及指标全部收集起来做了一个全链路的追踪，通过分析上报上来的日志和指标，我们就可以建立细粒度的日志大盘。当某一个读写请求变慢时，我们通过日志大盘很容易就知道他具体是慢在哪个环节。日志和指标的解析服务可以自动实时感知故障还有一些慢节点，这两类故障有一部分我们可以做到自动处理，我们会把他通过事件的方式通知到 Kafka manager，然后 Kafka manager 会根据这个事件自动去处理这些故障。还有一类故障是无法得到自动处理的，比如说僵尸节点，僵尸节点指的是 zookeeper 的临时节点还没有掉线，但是这个节点不管是 controller 也好还是客户端也好，都已经无法访问了，访问就会报错或者超时，这一类故障需要人工介入处理，会直接发给具体的管理员。

**3、服务生命周期管理**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoY2ibzmR8TAkEAUVvosGIDqBoGFpAfb2W5icANBD7Mic4BAUIU02p53OFQ/640?wx_fmt=png)

图 3-3 服务生命周期管理

首先介绍一下当集群规模增大以后存在的一系列问题。之前版本的 Kafka 也有一套自动化运维的系统，但是它存在一些问题，首先是状态语义存在歧义，无法真实反映系统状态，往往需要借助日志和指标去找到真实系统是否健康或者异常；然后是状态不全面，异常 case 需人工介入处理，误操作风险极大。

基于这两点问题，我们引入了生命周期管理机制，保证状态能真实反映系统状态。生命周期管理指的是从服务开始运行到机器报废停止服务的全流程管理。并且做到了服务状态和机器状态联动，无需人工同步变更。而且新的生命周期管理机制的状态变更由特定的自动化运维触发，禁止人工变更。

**4、TOR 容灾**

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoWlAG9ibfzEzk8C1kBQB6XpzZWb8PJOficm0uY6TbckiavI9HwC06FHdSQ/640?wx_fmt=png)

图 3-4 TOR 容灾 - 挑战

最后一个集群管理优化是 TOR 容灾。随着集群规模的变大，Rack 级别的故障变得平凡起来，而我们是无法容忍 Rack 级别的故障的，因为 Rack 级别的故障可能会导致分区不可用，原因是分区的多副本在同一个 rack 下，特别是在流存储环境下，当某些分区不可用时，它会导致收集侧的拥堵，影响其他 topic 的收集上报。并且当实时作业的某个分区出现异常时，它会影响整个链路。

如图 3-4 所示，当 rack1 发生故障时，TopicPartition1 是完全不可用的，因为他的两个副本都在 rack1 上，TopicPartition2 也是不可用的，虽然他有三个副本，但是他的两个副本都已经不可用了。

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8aEibu8LhlWJdmubIx7icuEEoe3Uo4FPdBwcvt1WA5nOZZhlQRiaN1jUMoNsu6LvC1uoYWmpD7UVpdZA/640?wx_fmt=png)

图 3-5 TOR 容灾 - 改进

针对 Rack 级别的故障，我们做了 TOR 容灾。改进了副本的分配算法，保证同一个分区的不同副本不在同一个 rack 下，如图 3-5 所示，即使 rack1 整个发生故障，也能保证所有分区是可用的。

**四、未来展望**

最后介绍一下美团数据平台的 Kafka 未来可以做哪些优化：首先我们会继续去做 Kafka 的高可用建设，比如说客户端主动去做一些故障节点的避让，服务端通过多队列的方式去隔离一些异常请求，避免它们之间相互影响。另外，高可靠方面会去做 quorum write 多数派写优化，因为 Kafka 的 ack 等于 1 时是需要写入所有副本的。我们还会去做流批一体的存储架构，比如 Kafka on HDFS。

作者丨赵海源

来源丨公众号：DataFunTalk（ID：datafuntalk）

dbaplus 社群欢迎广大技术人员投稿，投稿邮箱：editor@dbaplus.cn

![](https://mmbiz.qpic.cn/mmbiz_jpg/ufWcjcomw8bDt93PlffepZLY2Du4CibAMFnRRfeVKrQaFsZbH6tH8n26dFochsnNKLgfqRwl5RzPafLYVYA5gwg/640?wx_fmt=jpeg)

![](https://mmbiz.qpic.cn/mmbiz_png/ufWcjcomw8Zf2jiaBBH3vdgfP4A2rem5YEAHYH074dc4GibhojA5B3lLZrXLDRciaVnficveaHcAUCiaIPoDsTkAduA/640?wx_fmt=png)