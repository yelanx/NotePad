> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/eyefHGUynDnc9R51qiO49Q)

k8s 云原生时代的操作系统
==============

一、背景
----

*   kvm 主机扩缩容需要运维、开发协同处理效率不高，遇到节假日流量突增处理繁琐
    
*   kvm 主机普遍 cpu、内存利用率不高，造成浪费资源
    
*   监控有 open-falcon、zabbix 组件太多，需要统一到 prometheus 平台
    
*   缺乏中间件、存储的监控和告警
    

二、核心概念
------

![](https://mmbiz.qpic.cn/mmbiz_png/UZibibiaQhjHtnMBnnXh0SMH4FVwBI7O8WWApZCGwTH6ZH2QlD43g9VkM8VPgzYia3FVDCDqzCTAasFZ4aiay7ibTQrQ/640?wx_fmt=png)

### 1、Master 组件  

```
1、kube-apiserver
   Kubernetes API，集群的统一入口，各组件协调者，以RESTful API提供接口服务，所有对象资源的增删改查和监听操作都交给APIServer处理后再提交给Etcd存储。
2、kube-controller-manager
   处理集群中常规后台任务，一个资源对应一个控制器，而ControllerManager就是负责管理这些控制器的。
3、kube-scheduler
   根据调度算法为新创建的Pod选择一个Node节点，可以任意部署,可以部署在同一个节点上,也可以部署在不同的节点上。
4、etcd
   分布式键值存储系统。用于保存集群状态数据，比如Pod、Service等对象信息。
```

### 2、Node 组件

```
1、kubelet
   kubelet是Master在Node节点上的Agent，管理本机运行容器的生命周期，比如创建容器、Pod挂载数据卷、下载secret、获取容器和节点状态等工作。kubelet将每个Pod转换成一组容器。
2、kube-proxy
   在Node节点上实现Pod网络代理，维护网络规则和四层负载均衡工作。
3、docker或rocket
   容器引擎，运行容器。
```

### 3、名词解释

![](https://mmbiz.qpic.cn/mmbiz_png/UZibibiaQhjHtnMBnnXh0SMH4FVwBI7O8WW0yLzR31IUj04QdiczEyOLaibBN89xlibjfOibUfrPkyrKyiblgicTkDatH2g/640?wx_fmt=png)

￼  

```
1、Pod
   最小部署单元、一组容器的集合、一个Pod中的容器共享网络命名空间、 Pod是短暂的 
2、Controllers
   Deployment ：无状态应用部署                                          
   DaemonSet ：确保所有Node运行同一Pod                       
   StatefulSet ：有状态应用部署
   Job ：一次性任务
   Cronjob ：定时任务、更高级层次对象，部署和管理Pod
3、Service
   防止Pod失联
   定义一组Pod的访问策略
4、Label
   标签，附加到某个资源上，用于关联对象、查询和筛选
5、Namespaces
   命名空间，将对象逻辑上隔离
```

### 4、CICD 规划图

![](https://mmbiz.qpic.cn/mmbiz_png/UZibibiaQhjHtnMBnnXh0SMH4FVwBI7O8WWticHjJFGOE0lEKlK8d9IZQuibdE8JW12vMbtWbIIiao67FBqibj7cHl9OQ/640?wx_fmt=png)

￼  

三、踩坑经验分享
--------

1、磁盘故障导致 pod 被 oom？

```
问题：广告、代理层的pod同时触发了pod的重启告警，赶紧登上服务器看看咋回事？

原因：分析发现pod被oom后并未触发node节点的扩容，而是一直处于pengding的状态，这就比较奇怪了，赶紧联系ucloud得知E区的云盘出现问题，而咱们有部分node节点正好在E区，沟通得知硬盘故障pod处于pengding状态这属于k8s官方的bug并未修复。

解决办法：创建node节点尽量选多个可用区分散风险。
```

2、滚动更新之流量的丢失？

```
问题：研发反馈代理层上线期间所有的pod都从consul-server注销了,从而引发服务抖动?

原因：因为yaml文件没有设置钩子，滚动更新默认30秒就删除容器导致业务逻辑处理不过来，而且yaml副本数默认设置为1，导致滚动更新期间只保留1个pod可用。

解决方案：修改yaml的副本数和hpa最小值一致，增加容器删除等待时间为60秒
```

钩子配置

![](https://mmbiz.qpic.cn/mmbiz_png/UZibibiaQhjHtnMBnnXh0SMH4FVwBI7O8WWpNP9iashhTK0L44JHicLP7PUSMkGIE8BPVswY0v2tKpKc58gNG3P9zXQ/640?wx_fmt=png)

副本数配置

![](https://mmbiz.qpic.cn/mmbiz_png/UZibibiaQhjHtnMBnnXh0SMH4FVwBI7O8WWKChNTeyAWGrdkYicqW6qaLk6ZicRSY7L8HibvvbmezRYrBYT2cJHaGxXQ/640?wx_fmt=png)

￼  

3、pod 内的 consul-client 容器内存溢出，导致注册失败？

```
问题：收到告警，详情、流服务、代理层的qps都低于阈值，赶紧登录k8s看看出什么问题了？

原因：排查发现consul-client容器内存溢出超过了200M，导致consul-client容器重启consul-id变更，导致服务用之前的consul-id无法注册到consul-server。

解决方案：代理层、流服务、广告、日志上报的consul-clinet最大内存调整为500M，增加容器内存使用大于90%告警。
```

内存限制

![](https://mmbiz.qpic.cn/mmbiz_png/UZibibiaQhjHtnMBnnXh0SMH4FVwBI7O8WWMTkywdEibGhnBkibkXLFrQ8v1XpG9gRBrbYhKy7ZT8yr2cfQkCbiaLZJQ/640?wx_fmt=png)

内存告警  

![](https://mmbiz.qpic.cn/mmbiz_png/UZibibiaQhjHtnMBnnXh0SMH4FVwBI7O8WWTScvbcvE5z006ibOSDR7lOtYhYRokoMibnMPSR0XUsG40zN2atrhcuCQ/640?wx_fmt=png)

￼