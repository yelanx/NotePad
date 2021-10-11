> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/xXZVhagSw7SZrW4AR2pl6g)

什么是 TCC，TCC 是 Try、Confirm、Cancel 三个词语的缩写，最早是由 Pat Helland 于 2007 年发表的一篇名为《Life beyond Distributed Transactions:an Apostate’s Opinion》的论文提出。

TCC 组成
------

TCC 分为 3 个阶段

*   Try 阶段：尝试执行，完成所有业务检查（一致性）, 预留必须业务资源（准隔离性）
    
*   Confirm 阶段：如果所有分支的 Try 都成功了，则走到 Confirm 阶段。Confirm 真正执行业务，不作任何业务检查，只使用 Try 阶段预留的业务资源
    
*   Cancel 阶段：如果所有分支的 Try 有一个失败了，则走到 Cancel 阶段。Cancel 释放 Try 阶段预留的业务资源。
    

TCC 分布式事务里，有 3 个角色，与经典的 XA 分布式事务一样：

*   AP / 应用程序，发起全局事务，定义全局事务包含哪些事务分支
    
*   RM / 资源管理器，负责分支事务各项资源的管理
    
*   TM / 事务管理器，负责协调全局事务的正确执行，包括 Confirm，Cancel 的执行，并处理网络异常
    

如果我们要进行一个类似于银行跨行转账的业务，转出（TransOut）和转入（TransIn）分别在不同的微服务里，一个成功完成的 TCC 事务典型的时序图如下：

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)

TCC 网络异常
--------

TCC 在整个全局事务的过程中，可能发生各类网络异常情况，典型的是空回滚、幂等、悬挂，由于 TCC 的异常情况，和 SAGA、可靠消息等事务模式有相近的地方，因此我们把所有异常的解决方案统统放在这篇文章《分布式事务最经典的七种解决方案》的子事务屏障环节进行讲解

TCC 实践
------

下面我们进行一个 TCC 事务的具体开发

目前可用于 TCC 的开源框架，主要为 Java 语言，其中以 seata 为代表。我们的例子采用 nodejs，使用的分布式事务框架为 dtm，它对分布式事务的支持非常优雅。下面来详细讲解 TCC 的组成

下面我们来编写具体的 Try/Confirm/Cancel 的处理函数

```
router.post('/api/TransOutTry', (ctx, next) => {
  console.log("TransOutTry")
  ctx.body = { result: "SUCCESS" }
}).post('/api/TransOutConfirm', (ctx, next) => {
  console.log("TransOutConfirm")
  ctx.body = { result: "SUCCESS" }
}).post('/api/TransOutCancel', (ctx, next) => {
  console.log("TransOutCancel")
  ctx.body = { result: "SUCCESS" }
}).post('/api/TransInTry', (ctx, next) => {
  console.log("TransInTry")
  ctx.body = { result: "SUCCESS" }
}).post('/api/TransInConfirm', (ctx, next) => {
  console.log("TransInConfirm")
  ctx.body = { result: "SUCCESS" }
}).post('/api/TransInCancel', (ctx, next) => {
  console.log("TransInCancel")
  ctx.body = { result: "SUCCESS" }
})
```

到此各个子事务的处理函数已经 OK 了，然后是开启 TCC 事务，进行分支调用

```
async function FireTcc() {
  let dtm = "http://localhost:8080/api/dtmsvr" // dtm服务地址
  let svc = "http://localhost:4005/api" // 本地服务前缀
  // 开启一个tcc事务，第二个参数里面，写全局事务的逻辑
  await dtmcli.tccGlobalTransaction(dtm, async (t) => {
    let req = { amount: 30 } // 子事务需要的负荷
    console.log("calling trans out")
    // 注册事务分支，并调用分支中的Try
    await t.callBranch(req, svc + "/TransOutTry", svc + "/TransOutConfirm", svc + "/TransOutCancel")
    console.log("calling trans in")
    // 注册事务分支，并调用分支中的Try
    await t.callBranch(req, svc + "/TransInTry", svc + "/TransInConfirm", svc + "/TransInCancel")
  })
}
```

至此，一个完整的 TCC 分布式事务编写完成。

如果您想要完整运行一个成功的示例，那么参考这个例子 yedf/dtmcli-node-sample，将它运行起来非常简单

```
# 部署启动dtm
# 需要docker版本18以上
git clone https://github.com/yedf/dtm
cd dtm
docker-compose up

# 另起一个命令行
git clone https://github.com/yedf/dtmcli-node-sample
cd dtmcli-node-sample
npm install
node .
```

TCC 的回滚
-------

假如银行将金额准备转入用户 2 时，发现用户 2 的账户异常，返回失败，会怎么样？我们给出事务失败交互的时序图

![](https://mmbiz.qpic.cn/mmbiz_png/9m9OfQX94VvJvhjNGBvp23oCHMicZHFfWSnf5utwia7X1VIzY6HVnDTAVnqHHiahTnT6pvYXicx3nickq8rIy61Sj7A/640?wx_fmt=png)

这个跟成功的 TCC 差别就在于，当某个子事务返回失败后，后续就回滚全局事务，调用各个子事务的 Cancel 操作，保证全局事务全部回滚。

小结
--

在这篇文章里，我们介绍了 TCC 的理论知识，也通过一个例子，完整给出了编写一个 TCC 事务的过程，涵盖了正常成功完成，以及成功回滚的情况。相信读者通过这边文章，对 TCC 已经有了深入的理解。

关于分布式事务更多更全面的知识，请参考《分布式事务最经典的七种解决方案》

文中使用的例子节选自 yedf/dtm，支持多种事务模式：TCC、SAGA、XA、事务消息 跨语言支持，已支持 golang、python、Java、PHP、nodejs 等语言的客户端，参考各语言 SDK。提供子事务屏障功能，优雅解决幂等、悬挂、空补偿等问题。

阅读完此篇干货，欢迎大家访问 https://github.com/yedf/dtm 项目，给颗星星支持！