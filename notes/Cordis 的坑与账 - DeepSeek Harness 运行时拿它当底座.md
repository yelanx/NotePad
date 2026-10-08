> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.v2ex.com](https://www.v2ex.com/t/1246841#reply4)

> 坑是陷人的地方，账是未来要承担的后果。
> 
> 读不完的话（估计很少有人会读完的）：第 2 节讲它真正做了什么（其实只有一个栈），第 4 节讲它哪里长错了，第 8 节讲你该不该用。

0. 两个主角
-------

### 被评估的：Cordis

Koishi 聊天机器人框架的内核，后来 DeepSeek 的 DSH （ DeepSeek Harness ，Agent 运行时）拿它当底座。

类型上是**插件元框架**：本身不含任何业务能力，只管插件怎么装进一个长期运行的进程、怎么互相依赖、怎么卸载。Koishi 社区插件 4000+，DSH 第三方索引收录 6000+。

*   仓库：[https://github.com/cordiverse/cordis](https://github.com/cordiverse/cordis)
*   官方文档：[https://deepseek-harness.github.io/deepseek-harness/en/reference/cordis-primer](https://deepseek-harness.github.io/deepseek-harness/en/reference/cordis-primer)
*   上手教程：[https://deepseek-harness.github.io/deepseek-harness/en/develop/cordis-tutorial](https://deepseek-harness.github.io/deepseek-harness/en/develop/cordis-tutorial)

### 用来做参照的：tool-func 系列

isdk 的一组小库，从下往上是一条链：

*   [`tool-func`](https://github.com/isdk/tool-func.js)：函数注册表，让函数能自描述、被别人发现
*   [`tool-rpc`](https://github.com/isdk/tool-rpc.js)：把注册的函数暴露到网络上，本地 / 远程对调用方透明
*   [`tool-event`](https://github.com/isdk/tool-event.js)：把实时事件也当作 "一个工具"，复用 RPC 的生态

第 4 节会拿它当尺子：**同一个需求，另一种长法是什么样子。**

1. 它解决什么问题
----------

**一个 bot 进程要 7×24 运行，插件要能不重启进程地装卸和升级。**

这句话听起来平平无奇，把它变成画面：

> 你的机器人已经连续跑了半年，群里几千人在用。现在你要给 "天气查询" 插件修一个 bug 。 重启意味着全体下线半分钟。 你要的是：改完代码、保存、这个插件自己卸掉旧版本、装上新版本，群里的对话一次都没断。

要做到这件事，必须解决四个问题：

<table><thead><tr><th>需求</th><th>需要什么</th></tr></thead><tbody><tr><td>插件能装进进程</td><td>一个注册表，按名字找到插件</td></tr><tr><td>插件能卸载且不留垃圾</td><td>卸载时清理它注册过的所有东西：定时器、监听器、临时文件</td></tr><tr><td>清理不能乱序</td><td>后创建的先释放</td></tr><tr><td>插件之间有依赖</td><td>依赖失效时下游跟着拆，依赖回来时跟着装回去</td></tr></tbody></table>

**这四条是主干。** 主干之外 Cordis 还提供了：五种事件分发方法、配置系统、声明合并式的服务查找。那些是否必要、代价是什么，第 4 节和第 5 节逐项说明。

时间线上：2020 年 Koishi 先跑起来，Cordis 是后来从它内核剥离出的独立包，论文出现在 DSH 采用之后——工程在前，理论在后。

2. 核心机制：一个栈
-----------

第 1 节里第 2 、3 条需求（能清理、不乱序），实现出来就是一个**函数栈**。

### 2.1 不用框架时会怎么写

插件启动做了三件事：

```
const pool = openPool()                              // 1. 开一个数据库连接池
const timer = setInterval(() => pool.query(), 1000)  // 2. 每秒用连接池查一次
ctx.on('message', handler)                           // 3. 挂一个消息监听器
```

卸载时你得手动写三行，而且顺序不能错：

```
ctx.off('message', handler)   // 先摘监听器
clearInterval(timer)          // 再停定时器
pool.close()                  // 最后关连接池
```

**为什么必须是这个顺序？** 假设你先关连接池、后停定时器——在 "池已关、定时器还在" 的那几毫秒里，定时器可能又触发一次 `pool.query()`，拿一个已关闭的连接池查数据，必然报错。反过来，先停定时器再关池，怎么都不会错。

### 2.2 Cordis 的方案

它做的全部事情，是把这个顺序交给一个栈：

```
// 注册时：acquire 立即执行，返回值和清理函数成对入栈
function effect<T>(acquire: () => T, release: (value: T) => void): T {
  const value = acquire()
  stack.push([value, release])     // 资源和"怎么拆它"存在一起
  return value
}

// 卸载时：从栈顶往下弹
while (stack.length) {
  const [value, release] = stack.pop()
  release(value)
}
```

用 `ctx.effect()` 写刚才那三件事：

```
const pool  = ctx.effect(() => openPool(),               p => p.close())
const timer = ctx.effect(() => setInterval(...),         t => clearInterval(t))
             ctx.effect(() => ctx.on('message', handler), () => ctx.off('message', handler))
```

卸载时一行 `dispose()`，栈自己从顶往下弹。**你不再需要记住顺序。**

### 2.3 为什么 "反着来" 总是对的

规律是：**后创建的东西往往依赖先创建的东西。**

这个 "往往" 为什么可靠到不需要分析依赖？因为**变量作用域**已经替你保证了：定时器回调里引用了 `pool` 这个变量，那么 `openPool()` 那一行就必然写在 `setInterval` 前面——"先定义后使用" 和 "依赖在前、依赖者在后" 是同一个顺序。

LIFO （后进先出）把注册顺序一反转，恰好反转了依赖顺序。**它不是猜对了依赖，它是复用了语言几十年的 "先定义后使用" 规则。**

准确的说法是：**LIFO 不分析依赖，它只是沿用注册顺序的逆序。** 刻意违反时不成立：

```
let pool: Pool
ctx.effect(() => setInterval(() => pool.query(), 1000), t => clearInterval(t))  // 先注册
pool = ctx.effect(() => openPool(), p => p.close())                             // 后才拿到值
// 卸载时 LIFO：先关池、后停定时器 —— 顺序错了，且没有任何报错
```

这段代码能通过编译（`let` 的声明被提升了）。机制的全部实现是一个数组加一个 `pop()`。

**这个 `effect` 业界早有现成的名字**：Haskell 的 `bracket`、Effect-TS 的 `acquireRelease`，中文就是 "成对的资源获取与释放"。C++ 的 RAII 也算近亲，但它绑在词法作用域退出（离开大括号就析构），而 Cordis 这个不绑作用域、绑运行时——所以更准确的名字是**运行时可组合的 acquireRelease**。

**所以 Cordis 在这里的真实增量只有一步**：把 "谁后获取谁先释放" 从写在代码里的语法结构（`try/finally`），变成运行时可组合的数据结构（栈）。互不相识的插件各自往栈里压一对 "资源 + 清理函数"，卸载时统一弹出。这一步有价值，没有更多了。

### 2.4 插件与插件之间，也是这个顺序

A 插件提供数据库服务，B 插件依赖它、起定时器用它的池。

*   加载时：B 等 A 就绪才开始（ Cordis 里这个状态叫 **PENDING**）
*   卸载时：A 要下线，B 先被拆

**激活 A→B ，拆解 B→A**，和 2.3 是同一个顺序。函数粒度用栈，插件粒度用依赖图；两处都不做分析，都依赖顺序本身。

热重载也同样朴素：配置变了 → 旧插件清理 → 新插件带新配置初始化。

### 2.5 四条边界

机制能管到哪儿，全部写在这四条里。**管住的东西自动正确，没管住的东西彻底没人管，没有中间地带。**

**边界一：栈只管通过 effect 注册的东西。** 裸的 `setInterval`、裸的 `openPool()`，框架看不见也不清理——卸载后定时器继续每秒触发，查一个再没人用的连接池，**没有报错，静默泄漏**。Cordis 为此提供了包装过的 `ctx.setInterval`、`ctx.on`；第三方资源必须你自己套一层 `ctx.effect`。

**边界二：顺序只沿用注册先后。** 2.3 的反例已经演示了：倒序注册，LIFO 安静地给出错误顺序。

**边界三："可逆" 只对成对的注册类操作成立。** `on/off`、`open/close`、`register/unregister` 是真互逆；消息已发出、数据已写库，不可撤销——清理函数顶多补偿。框架保证清理的**时机和顺序**，不保证时间倒流。

**边界四：异步清理不保证按逆序完成。** 这一条是官方指南明文写的，很关键：

> 如果清理函数包含异步操作，框架并不保证它们按逆序完成。

同步的栈弹出是严格的逆序，但每个 disposer 一旦返回 Promise ，弹出就只是 "发起"，完成顺序由事件循环决定。于是卸载期间存在一个**半卸载窗口**：服务已注销、定时器还在跑。

官方给的应对是**合并**：如果卸载必须先停轮询、再关连接、最后释放文件，这三步不要拆成三个独立 disposer ，写进同一条清理链。

### 2.6 Cordis 已经做到的几件事

以下三件是它真实的加固：

*   **部分回滚是有的。** `ctx.effect()` 接受生成器形式，可以逐个 `yield` 清理函数，运行时记录 "前面完成了哪些"，加载失败时只撤销已执行的部分。另有 `armed` 标志保证一个 effect 至多被 dispose 一次。
*   **终态一致性有形式化保证。** 论文给了 confluence （汇合性）：无论以什么顺序加载或卸载，最终状态相同。
*   **服务不是全局单例。** `isolate()` 能让不同插件组各自持有同名服务的独立实例，互不干扰。

confluence 保证的是 "最终一致"，不保证 "过程中一致"。半卸载窗口依然存在，卸载也没有跨插件的事务语义——A 拆完、B 重装失败，没有补偿协议。

3. 造词：论文里的故作高深，工程里的学生气
----------------------

第 2 节的机制都很朴素。看 Cordis 给它们起的名字：

<table><thead><tr><th>实际机制</th><th>Cordis 的叫法</th><th>这个词原本属于哪里</th></tr></thead><tbody><tr><td>按相反顺序清理</td><td>逆序回放（ reverse replay ）</td><td>事件溯源、日志重放</td></tr><tr><td>执行清理函数</td><td>回滚（ rollback ）</td><td>数据库事务</td></tr><tr><td>作用域嵌套</td><td>空间维可组合性</td><td>物理学</td></tr><tr><td>相反顺序的装卸</td><td>时间维可组合性</td><td>物理学</td></tr></tbody></table>

**滥造词的判别式：一个词的字面含义指向这套机制不具备的性质，读者按字面理解必然产生错误预期。**

*   按 "回滚" 的字面义，你以为有版本记录、能回到之前的状态——没有，就是调用一个清理函数。
*   按 "回放" 的字面义，你以为存了操作日志能重放——没有，就是栈的 `pop()`。

这些词不是同一个毛病，它们来自两种完全不同的动机。

### 3.1 论文里的：为了显得高深

顶点是那篇论文《 A Programming Paradigm for Spatiotemporal Composability 》（[arXiv:2608.25512](https://arxiv.org/abs/2608.25512)，88 页）。逐项还原：

*   **空间维可组合性** = 作用域（ scope ）：ctx 嵌套、原型链 shadow ，五十年的旧物
*   **时间维可组合性** = 析构顺序：C++ 的 RAII 、Haskell 的 `bracket`、Effect-TS 的 `acquireRelease`，都是现成的
*   形式化骨架 `Γ→Γ×(Γ→Γ)`（每次变更返回 "新环境 + 逆函数"）= `effect(acquire, release)` 的类型签名，换了一套数学记法

把一个栈写成 `Γ→Γ×(Γ→Γ)`，把作用域写成 "空间维"，这不是为了说清楚，是为了**让它看起来不像已经存在的东西**。新造的术语天然不可比对：读者没法拿 "时空可组合性" 去搜，也就没法发现它其实就是作用域加析构顺序。

问题在于这组词进了论文，** 没有一个人回头补一句 "这就是作用域加析构顺序"**。

### 3.2 工程里的：学到哪用到哪

另一批词没有那么强的野心，它们只是**学生气**——作者刚学会什么，就用什么来命名眼前的东西：

*   刚读完数据库事务 → 清理函数叫 "回滚"
*   刚看完事件溯源 → 栈的弹出叫 "回放"

这不是欺骗，是词汇量跟不上表达欲的正常阶段：手里只有这几把锤子，看什么都像钉子。人人经过。

### 3.3 这两年的新词：用含糊换掉精确

这股风气现在更盛，AI 领域按周产词。举两个例子：

**harness**——原意是马具、线束、安全带。现在指 "Agent 的运行时外壳"。字面义完全不指向实际含义，第一次见只能靠猜。DSH 里的 H 就是它。

**上下文工程**（ context engineering ）——毛病不在 "工程" 两个字。"工程" 用得其所：把 prompt 从一次性的字符串，变成可复用、可组装、可版本管理的输入构造流程，确实是在做工程。

毛病在**用一个含糊的词换掉一个精确的词**：

*   `prompt` 在 IT 里此前没有别的意思，一说就知道是喂给 LLM 的那段输入，不会歧义。所以 "提示词工程" 四个字已经说清楚了：对喂给 LLM 的输入做工程管理。
*   `context` 不一样。进程上下文、React Context 、HTTP context 、Cordis 自己的 Context——它在 IT 里早就被用烂了，每个领域一个意思。

于是这次造词的净效果是：**凭空多了一个同义词，同时把一个精确的词换成了一个含糊的词。** 两边都亏。

### 3.4 形式化本身没有错

形式化有正当价值——一条规则管住全部行为、可机器检查。给已完成的工程补写形式化说明，业界常见。

Cordis 的真实贡献是把 acquire/release 与运行时插件装卸、依赖重连**组合**了起来——有分量的工程组合。但组合不是发明，"范式" 和 "时空" 这两个名字夸大了它。

4. 加东西的两种加法
-----------

第 2 节把 Cordis 的核心还原成了一个栈。但 Cordis 对外提供的远不止一个栈——五个事件方法、一套配置系统、一套服务查找机制。

于是有一个绕不开的问题：**这些东西该不该有？**

直觉的回答是 "太多了"。但这个回答没有区分力，因为有的多是必要的，有的多不是。区分的办法是看它们怎么长出来的。

软件里加东西只有两种加法：

*   **收窄**：在已有的东西上加一条限制，得到它的一个特例。就像 "正方形" 是 "矩形" 加一条限制（四边相等）——**正方形是矩形的一种**，矩形还在，你随时可以直接用矩形。
*   **铺开**：把各种用法并排列出来，每项一个名字。就像把圆形、三角形、正方形平铺成一排按钮——**谁也不是谁的一种**，你按哪个就是哪个。

**只有第一种多了 "一层"。第二种只是把自由度提前花掉了。**

### 4.1 先看收窄的样子

第 0 节介绍过的 tool-func 系列，就是一条靠收窄长出来的链，每层是下层的一个特例。从下往上：

```
// ① ToolFunc：自描述、自管理的函数工具集
new ToolFunc({ name: 'ping', func: () => 'pong' }).register()

// ② ServerTools：单个可远程调用的函数
new ServerTools({ name: 'ping', func: () => 'pong' }).register()

// ③ RpcMethodsServerTool：把相关方法组织成一个服务对象
const users = new RpcMethodsServerTool({ name: 'users' })
users.$createUser = async ({ name }) => { /* ... */ }   // $ 前缀 = 自动注册为 RPC 方法
// 客户端调用时传 act: '$createUser'

// ④ ResServerTools：映射标准 REST 动词
class Users extends ResServerTools {
  async get({ id })   { /* ... */ }
  async list()        { /* ... */ }
  async post({ val }) { /* ... */ }
}
// GET  /users/:id → get({ id })
// GET  /users     → list()
// POST /users     → post({ val })

// ⑤ tool-event：把实时事件当作"另一种工具"
await eventClient.subscribe('server-time')   // 订阅本身就是一次 RPC 调用
EventServer.setPubSubTransport(new SseServerPubSubTransport())  // 唯一的全新抽象
```

每往上一层，就多一层限制：

<table><thead><tr><th>层</th><th>新增限制</th><th>丢掉的自由度</th><th>换来什么</th></tr></thead><tbody><tr><td>ToolFunc</td><td>要有名字、进注册表</td><td>匿名函数、临时闭包</td><td>可被发现、可被别人调用</td></tr><tr><td>ServerTools</td><td>要能跨网络</td><td>闭包、外部变量、<code>this</code></td><td>远端能调</td></tr><tr><td>RpcMethodsServerTool</td><td>方法要 <code>$</code> 前缀、扁平</td><td>任意嵌套的 API 形状</td><td>一组相关操作有组织</td></tr><tr><td>ResServerTools</td><td>只剩 5 个动词 + <code>id</code></td><td>任意方法名</td><td>CRUD 一行路由都不用写</td></tr><tr><td>tool-event</td><td>从本地事件总线，到远程事件，控制面必须走 RPC 、要 transport</td><td>裸事件总线</td><td>复用整个 RPC 生态：发现、代理、传输</td></tr></tbody></table>

三个特征让这条链成立：

1.  **上层是下层的一种**：每层都是下面那层加限制得到的特例，是一条线，不是一堆
2.  **选择发生在声明处**：定义这个 tool 时选一次，且**按对象**——`/users` 用 `ResServerTools`，`/ping` 用 `ServerTools`，同一个进程里混着用
3.  **可绕过**：你不用 REST 语义，就根本不 `import` 它，下面那层永远对你开着

`tool-event` 是这条链上最克制的一环。Pub/Sub 本身是个完整范式（订阅、广播、双工、断线重连），它没有发明新模型，而是把控制面（`subscribe`/`unsubscribe`/`publish`）映射回标准 RPC 调用，复用 `tool-rpc` 已有的发现、代理、传输生态；**唯一的新抽象**是数据面的一个可插拔 transport 。

新增的概念数是 **1**。

### 4.2 再看 Cordis 的五个事件方法

Cordis 的事件系统有两头。一头是**注册监听**：

```
ctx.on('message', handler)   // "有人发消息时叫我"
```

另一头是**派发**（也就是触发这个事件）：

```
ctx.emit('message', session)   // "现在有人发消息了，都起来干活"
```

派发这一头有五个方法：

```
ctx.emit(name, ...args)       // 广播。返回值既不 await 也不收集
await ctx.parallel(...)       // 并发执行，等全部完成
await ctx.serial(...)         // 串行执行，第一个非空返回值即停
ctx.bail(...)                 // serial 的同步版本
await ctx.waterfall(...)      // 环绕中间件，可包装、可短路
```

<table><thead><tr><th>模式</th><th>等异步?</th><th>监听器的返回值给你吗?</th><th>顺序</th><th>短路</th></tr></thead><tbody><tr><td>emit</td><td>否</td><td><strong>不给</strong></td><td>注册序</td><td>否</td></tr><tr><td>parallel</td><td>是</td><td>不给（只留错误）</td><td>并发</td><td>否</td></tr><tr><td>serial</td><td>是</td><td>给</td><td>注册序</td><td>是</td></tr><tr><td>bail</td><td>否</td><td>给</td><td>注册序</td><td>是</td></tr><tr><td>waterfall</td><td>是</td><td>给</td><td>注册序</td><td>是（不调 <code>next</code>）</td></tr></tbody></table>

（表格来源：[Cordis Primer · Dispatch Modes](https://deepseek-harness.github.io/deepseek-harness/en/reference/cordis-primer)，[教程第 4 章 Events](https://deepseek-harness.github.io/deepseek-harness/en/develop/cordis-tutorial/04-events)）

把它们按 "是不是对方的限制版本" 排一下：

```
waterfall        ← 限制最多（环绕包装）
  ↑
serial ──── bail ← bail 就是 serial 去掉 await
  ↑
emit             ← 限制最少（什么都不收集）

parallel （并发，与上面不可比）
```

*   `emit` ⊂ `serial`（ serial = emit + 收集返回值 + 短路）
*   `serial` ⊂ `waterfall`（ waterfall 里写 `if (own) return; return next()` 就精确等于 serial ）
*   `bail` = `serial` **去掉 await**，是反向限制
*   `parallel` 与其余**不可比**——真并发是正交的

**这不是收窄，是铺开。** 其中 `parallel` 这条分支根本不在这条线上。

菜单上还留着两个洞，暴露出它不是推导出来的：

*   `serial` 和 `bail` **只差一个 await 布尔**，却占了两个方法名
*   `parallel` 用了 `Promise.allSettled` 却把值全丢了，只留错误——**并发又想拿返回值，在这个框架里没有出路**

### 4.3 `emit` 既是地基，又是五个方法之一

Cordis 事件系统 ** 最底下那个方法是 `ctx.emit`**。它本该是中性的——就像 `ToolFunc` 不预设工具跑在本地还是远端，好让上面想怎么盖就怎么盖。

但 `emit` 不是中性的，它自己就是一个具体用法：**广播，返回值一律丢弃**。官方文档的原话是 returned promises and values are **not awaited or collected**（既不等待，也不收集）。

拿 "监听器的返回值你拿得到吗" 给五个方法排个队：

<table><thead><tr><th>方法</th><th>监听器的返回值，你拿得到吗</th></tr></thead><tbody><tr><td><code>emit</code></td><td>拿不到（连等都不等）</td></tr><tr><td><code>parallel</code></td><td>只告诉你有没有出错</td></tr><tr><td><code>serial</code> / <code>bail</code></td><td>拿到第一个非空的结果，后面的监听器不再执行</td></tr><tr><td><code>waterfall</code></td><td>全部拿到，还能一层层改掉</td></tr></tbody></table>

`emit` 排在最后一名。

现在反过来设想：假如 `emit` 改成 "把每个监听器的返回值收成一个数组交给你"（ events-ex 就是这么做的，[events-ex.js](https://github.com/snowyu/events-ex.js)："The emit return the result of listeners's callback function"），会怎么样？

```
const isBailed = v => v !== null && v !== false && v !== undefined   // Cordis 自己的定义

// 想要 serial 的效果？一行就能自己写出来
const serial = (n, ...a) => ee.emit(n, ...a).find(isBailed)
```

五个方法会全部变成 "你那一行代码怎么写" 的事——想加就加，想改就改，想扔就扔，每个项目还能按自己的习惯定制。

但 Cordis 的 `emit` 不给。地基省掉的这点东西，上面就必须长出五个方法去补回来。

对照 4.1 那条链就能看清差别：`ToolFunc` 不预设这个工具跑在本地还是远端，所以 `tool-rpc` 能从它上面长出来。假如 `ToolFunc` 从一开始就写死 "只能在本地调用"，`tool-rpc` 根本不可能存在——**Cordis 的 `emit` 就是这个处境。**

由此得到一条可以拿去审任何 API 的判据：

> **最底下那层，不能是它自己某个用法。** 是，就说明上面只能顺着这个用法长，长不出别的东西。

*   `ToolFunc` 不是 `ServerTools` 的某种用法 → 中立 ✓
*   events-ex 的 `emit`（返回全部返回值）不是任何派发策略的某种用法 → 中立 ✓
*   Cordis 的 `emit` = 五个方法之一 → **地基上刻了用法** ✗

### 4.4 API 一旦公开发布，就收不回来

把五个方法降级成 helper ，能不能绕开这个问题？

加容易，收难。这不是审美判断，是有名字的规律——**Hyrum's Law**（ Hyrum Wright ，Google ，[hyrumslaw.com](https://www.hyrumslaw.com/)）：

> 当一个 API 有了足够多的使用者，**你在契约里承诺什么已经不重要了**：你系统的**所有可观测行为**都会被某个人依赖。

文档契约是你**希望自己有的**契约；**被观测到的行为才是你真正拥有的**契约。返回的列表碰巧是插入序？有人断言了这个顺序。错误信息里某个词？有人 grep 它来决定是否重试。

**所以收回的成本不是线性的——每个方法都在积累看不见的依赖。** Cordis 自己是例证：五种模式被 4000+ 插件使用，4.0 想收敛也做不到。

配套的两条原则，一起构成完整的立场：

*   **Rule of Least Power**（ Berners-Lee & Mendelsohn ，W3C TAG ，[原文](https://www.w3.org/2001/tag/doc/leastPower-2006-2-13.html)）：_Powerful languages inhibit information reuse... the less powerful the language, the more you can do with the data._ 底层原语越弱，能被越多场景复用。
*   **Mechanism, not Policy**（ X Window 设计原则，见《 Unix 编程艺术》第 1 章）：_policy tends to have a short lifetime, mechanism a long one._ 策略短命，机制长命。把策略焊进长命的层里，等于把短命的东西放进了不该放的地方。

这套论证的结论不是 "少即是美"——4.1 那条链有五层，没人抱怨它臃肿。判据是三条：**是不是收窄、是不是在声明处选、能不能绕过**。Cordis 违反了第一条（是铺开）和第三条（`emit` 不给返回值，绕过去也重建不了）。

5. 坑
----

先分清两个词：

*   **坑：误导或静默失效。** 按常理预期写代码，结果不对，过程中没有报错。
*   **成本：交换。** 得到什么、付出什么；有的当场付清，有的长期承担。

第 3 节的滥造词是坑（词面误导）。下面五条也是坑。

有一类东西常被算成坑，其实不是：**每个插件都要写的固定格式**（插件名、`apply` 入口、工具的说明和参数表）。它不会误导你，也不静默失效，你写的时候就知道自己在写什么。它是**成本**——用 "框架替你管理装卸" 换来的。第 7 节单独算这笔账。

### 坑一：没有故障隔离

所有插件跑在同一个 Node.js 进程里，共享内存和事件循环：一个插件死循环，整个进程卡死；一个插件泄漏，整个进程内存增长；不能单独部署、单独伸缩。

**它隔离的只有生命周期**——整体装上、整体拆下、拆下时清理干净。

定位：不是微服务（无独立进程和故障域），不是浏览器扩展（无沙箱），最接近**操作系统的可加载内核模块**——可以动态装卸，但装载后与内核共享地址空间和特权级，模块里的 bug 同样能让整个系统崩溃。

### 坑二：拦截能不能生效，取决于一段不在你代码里的调用

这是 4.3 那个结构问题的具体后果。

先看一个结构性事实：**事件怎么被派发，是派发那一行代码定的，不是你写监听器的时候定的。**

你写 `ctx.on('message', listener)` 时，无法指定 "我这个监听器能不能拦截消息"。你的监听器返回什么、返回值被如何对待，取决于**别人**在派发这个事件时调了五个方法里的哪一个。

**完整看一次失效。** 黑名单插件，想拦截被禁用户的消息：

```
// 插件 A：黑名单
export function apply(ctx: Context) {
  ctx.on('message', (session) => {
    if (banned.has(session.userId)) {
      return true     // 意图：返回 true 表示"拦截"
    }
  })
}
```

而框架内部分发这个事件用的是 `emit`：

```
ctx.emit('message', session)   // emit：逐个调用监听器，返回值全部丢弃
```

接下来逐步发生什么：

1.  被禁用户发了一条消息
2.  框架调用 `emit` → 你的监听器被调用，执行到 `return true`
3.  **这个 `true` 在这里被丢弃**——`emit` 的语义就是广播，不消费返回值
4.  下一个监听器（比如自动回复插件）照常被调用、照常运行
5.  bot 回复了被禁用户

**全程没有任何报错。** TypeScript 编译通过（回调多返回一个值，类型上合法）；运行通过（监听器确实执行了）；单测甚至通过（"监听器被调用" 这个断言是真的）。只有端到端行为不对：黑名单没生效。

失效发生在第 3 步——返回值被丢弃的那一刻，那里没有一行日志、一个警告。

要真正拦截，派发处必须换成 `bail`：

```
ctx.bail('message', session)   // 监听器返回真值时，后续监听器不再调用
```

**病灶这时才显形**：你的拦截能否生效，取决于一段不在你代码里的派发调用。你写了 `return true`，但决定这个返回值命运的是框架或另一个插件在别处选了五个方法中的哪一个。读你自己的插件代码看不出来，要去翻派发处的源码。

> **选择权和后果被拆在两个地方，而承受后果的一方看不见那个决定。**

DSH 已经部分修复了这个问题：官方 primer 写明 "dispatch mode is **part of the event's public contract**"，新事件用 `@mode` 标签标注，生成的目录会**比对声明与派发点**。

但它是**工具层契约，不是类型层契约**：`interface Events` 的声明合并里只有监听器签名，没有 mode 。写错 mode 不会被 `tsc` 拦住，只在生成的目录对不上时被发现。

而底层的结构问题没变：地基 `emit` 依然丢弃返回值，所以 "绕过去自己实现" 这条路仍然不通。

**还有一个具体伤害**：`emit` 不 `await`。异步监听器如果 reject ，**这个 rejection 没有任何人接收**——同步抛错至少会传播并中断后续，异步的会变成 unhandled rejection 。

### 坑三：贯穿插件存活期的资源，要自己包一层

`effect` 的签名是 `effect(acquire, release)`——它假定资源是 acquire 的返回值，同步取得，清理时关掉这个返回值。瞬时资源都合身，但有两类资源天生违反：

*   **acquire 是异步的**：连接池要等握手，客户端要等鉴权
*   **没有本体**：`setup()` 做的事是把服务注册进注册表等着被查——dispose 撤销的是一段注册，不是关一个返回值

这类资源在插件整个存活期都要在，只能写成闭包递值：

```
let pool: Pool
ctx.effect(() => { pool = openPool(); return pool }, p => p.close())
// pool 的声明、effect 的注册、使用 pool 的业务代码，位置互相牵制；资源一多，交错成网
```

成熟的解法是加一层封装，把跨存活期的状态圈进一个有 setup/dispose 的单元：

```
const db = (() => {
  let pool: Pool
  return {
    async setup()  { pool = openPool(); await pool.connect() },
    dispose()      { pool.close() },
    query(sql: string) { return pool.query(sql) },
  }
})()
ctx.effect(() => db.setup(), () => db.dispose())
// 业务代码只面对 db.query()，交错消失
```

资源种类多就提基类，各生态都收敛到这个形态（ Koishi 的 `Service`、RxJS 的 `Disposable`、Effect-TS 的 `Scope`）。

这件事要从两层看：

*   **难写是签名层面的必然**——异步、无本体的资源，`effect(acquire, release)` 这个类型装不下，换任何实现都一样（ React 的 effect 不能是异步函数、Promise 一旦 resolve 不可撤销，是同一问题的不同表现）
*   **好写是封装层面的可得**——IIFE 或基类，一次封装全队复用

还有第三条路：把生命周期单元切得更细——每个资源自己就是一个带 setup/dispose 的条目，依赖交给依赖图，封装摩擦在更细的粒度上自然消失。代价记在账单二。

### 坑四：没有远程方案

`ctx.database` 永远是进程内对象，没有传输层抽象。不能把服务透明地换成远程实现，也不能同一服务有时本地跑、有时独立部署。

对照 `tool-rpc`：它用 "注册表同名代理" 做到本地 / 远程透明——业务代码不改，注册时决定这个 tool 跑在本地还是远端。需要这种弹性的场景，Cordis 只覆盖本地一半。

### 坑五："插件" 一词的歧义

Cordis/Koishi 的 "插件" 是**后端服务单元**，不是浏览器扩展、VS Code 扩展那种带界面的东西。

判别式不在界面，在**运行时可加载、向宿主暴露标准接口、可独立分发**。但日常语境里 "插件" 强烈暗示前者——**每次有新成员加入、或和别的团队协作**，都要先确认对方说的是哪一种，这个成本反复发生。给自己的项目立个命名约定（后端的叫 Service / ServicePlugin ）能省掉。

6. 亮点
-----

一句话：**一个 effect 原语，加一条 "全部副作用 API 都走它" 的规范。**

原语本身第 2 节讲过了，规范才是它真正值钱的地方。

框架里每一个会产生副作用的 API——注册命令、添加监听、挂载服务、起定时器——不管名字叫什么，内部实现都是同一件事：执行获取、把返回值和清理函数成对入栈。类比文件操作：`read`、`write`、`append` 表面上是三个功能，本质都是 "打开 - 使用 - 关闭"。

### 6.1 这条规范为什么不是可选项

第 2 节的四条边界划出了机制的管辖范围：**管住的东西自动正确，没管住的东西彻底没人管，没有中间地带。** 裸写的资源静默泄漏，倒序注册静默错序——框架不清理，也不报错。

所以 "全部副作用 API 无一例外走原语" 不是风格偏好，是机制能工作的前提。

### 6.2 回报在哪

**只要原语正确（清理必执行、LIFO ），全部 API 的卸载行为自动正确。**

查问题只需要查一个原语；新增 API 只要走了原语，自动继承正确性。C++ 的 RAII 也是如此——机制一行讲得完，价值在于整个类型系统无一例外地执行它。

亮点不等于不可替代——这条规范谁都能抄走，抄走的代价在第 8 节算。

7. 成本：每个插件要写的固定格式
-----------------

第 5 节把 "坑" 和 "成本" 分开了：坑是误导，成本是交换。现在算第一笔成本，也是唯一一笔**写第一行代码时就能看清**的。

工具要让别人调用，就必须描述自己。这不是 Cordis 的特殊要求——任何提供发现和校验的注册表，都要求三样东西：**名字、说明、参数形状**。OpenAI 的 function calling 、MCP 、JSON Schema 表单，最后都收敛到这三样。

**调用方看不见的工具，无法被调用。**

同一件事的两种写法。`ToolFunc`：

```
new ToolFunc({
  name: 'get_weather',
  func: async ({ city }) => {
    const res = await fetch(`https://api.weather.com/${city}`)
    return res.json()
  },
  params: { city: { type: 'string' } },   // 可选
  result: { type: 'object' },             // 可选
}).register()
```

写成 DSH 插件：

```
export default definePlugin({
  name: 'weather',                    // ①
  apply(ctx) {                        // ②
    ctx.tools.register({
      name: 'get_weather',            // ③
      description: '查询城市天气',      // ④
      parameters: {                   // ⑤
        city: { type: 'string' },
      },
      async execute({ city }) {
        const res = await fetch(`https://api.weather.com/${city}`)  // 业务
        return res.json()                                          // 业务
      },
    })
  },
})
```

十四行，业务两行，描述十二行。多出来的每一行，各自服务于哪个调用方：

<table><thead><tr><th>行</th><th>内容</th><th>服务的调用方</th></tr></thead><tbody><tr><td>①</td><td>插件名</td><td>框架：卸载、热重载时按名定位；日志归因</td></tr><tr><td>②</td><td>apply 入口</td><td>框架：整体装上、整体拆下</td></tr><tr><td>③</td><td>工具名</td><td>代码、LLM：在工具列表里发现它</td></tr><tr><td>④</td><td>说明文字</td><td>LLM：判断何时调用——没有这行，工具对 LLM 不存在</td></tr><tr><td>⑤</td><td>参数形状</td><td>LLM：正确传参；框架：校验</td></tr></tbody></table>

**所以差异只在调用：调用方是谁，决定描述写到哪一档。**

*   代码按名调用：`name` + `func` 就够——`ToolFunc` 的最小形态
*   LLM 或人调用：再加说明和参数形状——`ToolFunc` 写上 `params` / `result` 即可
*   框架装卸：再加入口结构（ apply ）——只有 DSH 这一档提供

**进程是否长期运行，决定第三档是否必要**：一个插件、重启无所谓时，装卸能力没有意义；五十个插件 7×24 运行时，重启一次等于全体下线一次，装卸变成必需。

选择因此还原为两个问题：**调用方是谁、进程要不要长期跑。** 这里没有对错，只有匹配。

8. 账：两张账单
---------

成本是当场付清的，写的时候就知道。账不一样——它要在真实环境用下去才开始显现，而且**利滚利**。

### 8.1 账单一：用 Cordis

<table><thead><tr><th>账目</th><th>何时发生</th><th>代价</th></tr></thead><tbody><tr><td>API 无法收回</td><td>想改五种事件模式 / Schema / 声明合并中任何一个</td><td>永久——4000+ 插件在用（ Hyrum's Law ，见 4.4 ）</td></tr><tr><td>rc 迁移</td><td>当前版本 4.0.0-rc.10 ，API 未冻结</td><td>每次 rc 更新，全量回归</td></tr><tr><td>单进程故障</td><td>任一插件死循环或泄漏</td><td>全进程停机；用重启兜底，则放弃 "不重启" 的卖点</td></tr><tr><td>拆分重构</td><td>哪天要把某个服务挪出进程</td><td>重写所有 <code>ctx.database</code> 直连调用，随插件数线性增长</td></tr><tr><td>术语误解</td><td>每个新成员入职</td><td>每人一次 "回滚不是回滚" 的澄清，此后沟通中反复发生</td></tr><tr><td>工具描述</td><td>每写一个新插件</td><td>DSH 十四行起步，约十二行是描述（第 7 节）；按调用方可减</td></tr><tr><td>资源封装</td><td>每个贯穿存活期的资源</td><td>一次 IIFE 或基类封装（坑三）</td></tr><tr><td>异步清理</td><td>每个有异步 disposer 的插件</td><td>半卸载窗口存在，需手动合并清理链（边界四）</td></tr><tr><td>退出成本</td><td>决定离开那天</td><td>与停留时长成正比，所有写过 <code>ctx.*</code> 的代码都要重写</td></tr></tbody></table>

### 8.2 账单二：自己组合

思路接近 Unix 哲学：组件小、职责单一，通过组合完成系统。

**用什么：**

*   `tool-func`（[isdk/tool-func.js](https://github.com/isdk/tool-func.js)）：注册表与生命周期
*   `tool-rpc`（[isdk/tool-rpc.js](https://github.com/isdk/tool-rpc.js)）：本地 / 远程透明，注册表同名代理，业务代码不改
*   `tool-event`（[isdk/tool-event.js](https://github.com/isdk/tool-event.js)）：事件与控制面映射回 RPC ，唯一新抽象是可插拔 transport
*   `events-ex`（[snowyu/events-ex.js](https://github.com/snowyu/events-ex.js)）：事件，控制流在监听器手里（`emit` 返回监听器结果）
*   `custom-ability`（[snowyu/custom-ability.js](https://github.com/snowyu/custom-ability.js)）：能力注入

**缺什么：**

<table><thead><tr><th>账目</th><th>何时发生</th><th>代价</th></tr></thead><tbody><tr><td>纪律</td><td>每次新增会副作用的 API</td><td>没有框架强制，靠 review 和守卫测试；漏一次就是一个泄漏</td></tr><tr><td>能力缺口</td><td>需要嵌套作用域（会话级批量清理）</td><td>context 树已替换为扁平注册表，要自己实现</td></tr><tr><td>粒度代价</td><td>设计阶段</td><td>原本内聚的东西拆成多个条目，互相调用要走注册表——拆对了是解耦，拆错了要合回去</td></tr><tr><td>Koishi 插件适配</td><td>想用 Koishi 插件时</td><td>每个要用的插件都是一次独立适配</td></tr></tbody></table>

参考链接
----

**Cordis 与 DSH**

*   Cordis 仓库：[https://github.com/cordiverse/cordis](https://github.com/cordiverse/cordis)
*   论文《 A Programming Paradigm for Spatiotemporal Composability 》（ arXiv:2608.25512 ）：[https://arxiv.org/abs/2608.25512](https://arxiv.org/abs/2608.25512)
*   Cordis Primer：[https://deepseek-harness.github.io/deepseek-harness/en/reference/cordis-primer](https://deepseek-harness.github.io/deepseek-harness/en/reference/cordis-primer)
*   Cordis 教程：[https://deepseek-harness.github.io/deepseek-harness/en/develop/cordis-tutorial](https://deepseek-harness.github.io/deepseek-harness/en/develop/cordis-tutorial)
*   教程第 4 章 · Events：[https://deepseek-harness.github.io/deepseek-harness/en/develop/cordis-tutorial/04-events](https://deepseek-harness.github.io/deepseek-harness/en/develop/cordis-tutorial/04-events)
*   事件系统：[https://deepseek-harness.github.io/deepseek-harness/en/develop/framework/events](https://deepseek-harness.github.io/deepseek-harness/en/develop/framework/events)
*   服务与依赖：[https://deepseek-harness.github.io/deepseek-harness/en/develop/framework/service](https://deepseek-harness.github.io/deepseek-harness/en/develop/framework/service)
*   Koishi：[https://github.com/koishijs/koishi](https://github.com/koishijs/koishi)

**tool-func 系列与对照物**

*   tool-func.js：[https://github.com/isdk/tool-func.js](https://github.com/isdk/tool-func.js)
*   tool-rpc.js：[https://github.com/isdk/tool-rpc.js](https://github.com/isdk/tool-rpc.js)
*   tool-event.js：[https://github.com/isdk/tool-event.js](https://github.com/isdk/tool-event.js)
*   events-ex.js：[https://github.com/snowyu/events-ex.js](https://github.com/snowyu/events-ex.js)
*   custom-ability.js：[https://github.com/snowyu/custom-ability.js](https://github.com/snowyu/custom-ability.js)

**被引用的原则**

*   Hyrum's Law：[https://www.hyrumslaw.com/](https://www.hyrumslaw.com/)
*   The Rule of Least Power （ W3C TAG ）：[https://www.w3.org/2001/tag/doc/leastPower-2006-2-13.html](https://www.w3.org/2001/tag/doc/leastPower-2006-2-13.html)
*   《 The Art of Unix Programming 》第 1 章（ mechanism, not policy ）：[https://book.huihoo.com/the-art-of-unix-programming/ch01s04.html](https://book.huihoo.com/the-art-of-unix-programming/ch01s04.html)