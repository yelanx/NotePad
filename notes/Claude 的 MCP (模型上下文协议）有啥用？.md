> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [sspai.com](https://sspai.com/post/94360)

> Matrix 首页推荐 Matrix 是少数派的写作社区，我们主张分享真实的产品体验，有实用价值的经验与思考。

**Matrix 首页推荐** 

[Matrix](https://sspai.com/matrix) 是少数派的写作社区，我们主张分享真实的产品体验，有实用价值的经验与思考。我们会不定期挑选 Matrix 最优质的文章，展示来自用户的最真实的体验和观点。 

文章代表作者个人观点，少数派仅对标题和排版略作修改。

**协议**
------

Claude (Anthropic) 最近出了个 MCP (Model Context Protocol，模型上下文协议) 协议，让我朋友圈有刷屏之势，能清晰感受到，大伙儿都非常欣喜。我自己试用之后，决定写下这篇文章，分享给你。

MCP 是一种新的开放标准协议，用来在大模型和数据源之间建立安全双向的链接。这是它的官方示意图。

![](https://cdnfile.sspai.com/2024/11/28/article/c0111e3ae7c1ab68f12d228ed4e716f7.jpeg?imageView2/2/format/webp)

这张图展示了使用 Claude 或其他 IDE 时，通过这种双向沟通协议，模型（目前指 Claude）可以与不同的数据服务器进行连接。每个连接的数据源可能千差万别，比如上图里面前两个连接本地数据，第三个则直接通过互联网操作远程文件。

MCP 有什么用？为什么会让这么多开发者与用户欢欣鼓舞？

MCP 是一种统一的集成方式，交互界面完全一致。如果其他大模型也跟进，那么以后连接数据的感觉，就像给不同的电子设备使用 USB-C 接口，而不用准备那么多种不同的线缆插头。

![](https://cdnfile.sspai.com/2024/11/28/article/caca9e6510372f57f49308769a59b5ad.jpeg?imageView2/2/format/webp)

更重要的是 MCP 的设计目标——提升**安全性与控制力**。因为以前处理数据时，我们通常采用**极端的处理方式**，很不安全。

第一种是将数据上传到大模型的对话中。这会带来两个问题：

1.  数据上传到他人服务器上，存在安全隐患。
2.  上传过程，数据大小和类型有严格限制。后文会提到相应的例子。

另一种方式是让大模型获得本地管理员级别处理权限，帮助我们自动处理本地数据。之前我 [给你介绍过的 Open Interpreter](https://mp.weixin.qq.com/s/zWb3YGB8aufVJkb5LZ6CUA) 就属于这种方式。看起来非常方便、灵活，但 AI 代理在本地以管理员权限进行各种操作，看到所有文件。如果它被植入不安全的代码，控制你的计算机，可能导致隐私和重要数据泄露，后果严重性不言而喻。

为解决上述两种极端数据交互方式带来的问题，Claude 提供了 MCP 作为一种解决方案。作为协议，它是完全开放的。后续其他主流 AI 企业能否跟进，咱们说不准。但是现在就可以用 Claude 来体验一下 MCP 带来的数据交互好处。

![](https://cdnfile.sspai.com/2024/11/28/article/19a7591c813e78f692eb8ff35e641bf9.jpeg?imageView2/2/format/webp)

我们先沿着官方的 [参考资料有快速上手指南](https://sspai.com/link?target=https%3A%2F%2Fmodelcontextprotocol.io%2Fquickstart) 操作一下。指南非常简洁，步骤清晰，跟着做并不难。

**设定**
------

官方教程给出了一个最简单的数据操作样例，是一个 SQLite 数据库。

![](https://cdnfile.sspai.com/2024/11/28/article/29c86ec8b2248fcb608583d67637f515.jpeg?imageView2/2/format/webp)

SQLite 设置非常简单，单文件即可运行。我讲数据库课程超过 10 年，一直用的就是 SQLite。学生不用一上来就去学习架设服务器、权限管理，而是直接拿过来就可以学习 SQL 查询语句。对文科生来说，这都是一个非常简单的界面。

在上手教程里，我们会操作一个本地 SQLite 文件，与 Claude 进行交互。我们需要预先安装一些软件，不过很简单，你照着指南里面这个命令拷贝到终端执行就行。

![](https://cdnfile.sspai.com/2024/11/28/article/238965d4bccf01371bdb0524c9f9c345.jpeg?imageView2/2/format/webp)

下面是在我电脑上执行过程截图。

![](https://cdnfile.sspai.com/2024/11/28/article/2b8745bdda4c7304f711d6a6c1ec0056.jpeg?imageView2/2/format/webp)

当然别忘了，你需要 [下载 Claude Desktop 应用的最新版本](https://sspai.com/link?target=https%3A%2F%2Fclaude.ai%2Fdownload)，这是执行后续操作的前提。

![](https://cdnfile.sspai.com/2024/11/28/article/e58273332fa0ae0fa0fe4927685c10bf.jpeg?imageView2/2/format/webp)

之后，你需要建立一个 SQLite 的数据库样例文件。咱们先按照官方的设定来操作，复制页面上的这段代码，直接在终端执行就能搞定。

![](https://cdnfile.sspai.com/2024/11/28/article/a235e535c7147c400b3098110640d2ad.jpeg?imageView2/2/format/webp)

只要没有报错，你就拥有一个本地的 SQLite 样例数据了。

![](https://cdnfile.sspai.com/2024/11/28/article/e892d7bb4482827364110c6a600feaa5.jpeg?imageView2/2/format/webp)

它存储在你的用户目录下，叫做 test.db .

下面你需要做的，是本次教程里最为不方便的操作——修改 Claude 配置文件。我相信在未来的版本当中，这个操作是能够通过图形化的界面来拖拽完成的。不过现在还是原型系统，你暂且忍耐一下。教程里明确告诉你设定文件的路径，你照着这个来执行就好。

![](https://cdnfile.sspai.com/2024/11/28/article/5a354a26f9591f17810758fa3dcce0ef.jpeg?imageView2/2/format/webp)

你可以用 Visual Studio Code 或者类似的编辑器打开指定的配置文件路径。我这里用的是 [Cursor](https://mp.weixin.qq.com/s/ikkxLNhiN0g82c2GWGx6aw)。打开该文件后，你需要把教程代码段里的内容填进去。

不过这里有一个注意事项——你需要把原先代码中的 **username 换成你自己在 macOS 上实际的用户名**。这个很重要，不然连不上数据，会耽误你很多宝贵时间查错…… 别问我怎么知道的。

![](https://cdnfile.sspai.com/2024/11/28/article/99ae0f52feeccbc72eea8d3377ece8cc.jpeg?imageView2/2/format/webp)

之后注意，你需要在 macOS 上**重启**你的 Claude Desktop App。

到此，设定就算完成了。

**上手**
------

下面，咱们实际看看 Claude 是如何与 test.db 这个数据文件交互。官网给出的流程图是这样的：

![](https://cdnfile.sspai.com/2024/11/28/article/70f4d10cfcea61ea11e7addd0faa3414.jpeg?imageView2/2/format/webp)

如图所示，Claude 先要和我们刚刚搭建的 SQLite MCP 服务之间建立连接，然后可以执行查询的操作。

首先，我们先用提示词来把这二者连接起来。这里的提问我是直接从人家官方的快速开始教程里面照抄的——「你能不能连接我的 SQLite 这个数据库，然后告诉我哪些商品现在可售，以及他们的售价？」

> Can you connect to my SQLite database and tell me what products are available, and their prices?

![](https://cdnfile.sspai.com/2024/11/28/article/7617a22a987e225c3ece195fb7447f45.jpeg?imageView2/2/format/webp)

Claude 立即就会明白需要和 SQLite MCP 沟通。

![](https://cdnfile.sspai.com/2024/11/28/article/af0bf348dcd8b9de6c23848d70a47794.jpeg?imageView2/2/format/webp)

然后它就找我们要权限。我选择这一整次对话都可以给它开放权限（Allow for This Chat）。注意，这就是我刚刚跟你提到的安全性——大模型要做什么操作、找我们要什么样的权限、权限开放的时间范围多大…… 我们都可以自己来控制。

大模型开始与 MCP 通讯，执行一系列的 SQL 语句，通过查询返回结果。

![](https://cdnfile.sspai.com/2024/11/28/article/81d75e393629d6329e3574d8414a39eb.jpeg?imageView2/2/format/webp)

注意，Claude 不像 SQLite 简单给你返回一个表格作为结果，而是用自然语言回答你的问题。这个样例中，它把现在可售商品都给你列出来，并且后面都标上价格。这种交互就显得非常自然。

![](https://cdnfile.sspai.com/2024/11/28/article/a04f027077c1261226d22a330c1a3b15.jpeg?imageView2/2/format/webp)

下面我们来继续提出另一个样例问题——「在你的数据库中，商品平均价格是多少？」

> What's the average price of all products in the database?

这次大模型没有找我们再要权限。因为刚刚已经说明，整轮对话，它都可以获得 MCP 服务数据的操作权限。

![](https://cdnfile.sspai.com/2024/11/28/article/635ac654e536e96b157bc1962d7af609.jpeg?imageView2/2/format/webp)

执行后，Claude 告诉我们，平均值为 82.14 美元。

你会发现我们刚刚一直用英文来提问，这是因为教程是英文的，咱们为了方便拷贝了问题。但对 Claude 来说，中文完全不是问题。用中文来问「你能分析价格分布并提出任何定价的优化建议吗？」Claude 就会用中文来答。当然，背后还是连接 MCP 服务，调用 SQL 进行查询。

当查询遇到问题时，Claude 会自动反思，并且重组查询式，依照 MCP 服务返回的 SQLite 查询表格结果，告诉你不同的价格分布。

![](https://cdnfile.sspai.com/2024/11/28/article/e81edd0fd12708bb694ea33f83562e88.jpeg?imageView2/2/format/webp)

基于这些分析结果，它会给出优化建议，如价格策略、产品组合、促销策略和定价心理学应用等。

![](https://cdnfile.sspai.com/2024/11/28/article/168769559774db575c619030e705501b.jpeg?imageView2/2/format/webp)

注意这是你单独用 SQLite 查询数据库无法直接给出的结果，SQLite 只能给出表格。而根据背景知识对查询结果表格进行解读，才是**大模型的能力体现**。

**测试**
------

既然跑通了官网给出的样例，我们接下来换上我讲数据库课程时常用的样例数据集，叫做 colleges。这个数据集来自斯坦福大学的一门 MOOC，包含学生申请大学的模拟数据。

![](https://cdnfile.sspai.com/2024/11/28/article/e9031b7cb7848ddec8ee89f115a1d5ac.jpeg?imageView2/2/format/webp)

数据集包括三个表格：apply（谁申请了哪个学校的哪个专业，是否被录取）、colleges（所有大学的列表）和 students（所有学生的信息）。

![](https://cdnfile.sspai.com/2024/11/28/article/76ee4fdf5000da851e9373c41de82a96.jpeg?imageView2/2/format/webp)

平时上课时，我在这几个表之间来回操作，教学生如何跨越表格综合信息返回正确的结果。

![](https://cdnfile.sspai.com/2024/11/28/article/a2ed6d9d181b44fdfdc4466977a0e6f7.jpeg?imageView2/2/format/webp)

这次，咱们不用任何的 SQL 命令撰写，而是直接用自然语言来提问。首先，你要确保 MCP 连接成功。注意你需要修改配置文件里，数据库文件的路径，指向 `colleges.db` 。

![](https://cdnfile.sspai.com/2024/11/28/article/f2f4584c01afd7ba40fdf628b3a32105.jpeg?imageView2/2/format/webp)

对了，之后别忘了重启 Claude Desktop。

我的问题为：「你能否连接我的 SQLite 数据库，并告诉我里面有什么？」

> Can you connect to my SQLite database and tell me what's in it?

还是索要了一系列权限后，Claude 告诉我们有三个表：college、student、apply。

![](https://cdnfile.sspai.com/2024/11/28/article/46cd30fc5f907697b6b16711b6927aa4.jpeg?imageView2/2/format/webp)

之后，通过进一步查询，Claude 为我们介绍 college 表中有哪些字段，student 和 apply 表又分别有哪些字段。至此意味着 MCP 数据连接成功。

![](https://cdnfile.sspai.com/2024/11/28/article/fdba1934adbf0cadf72d8e1a61104224.jpeg?imageView2/2/format/webp)

Claude 会给出一些建议，告诉你可以问哪些问题。

![](https://cdnfile.sspai.com/2024/11/28/article/f428e062528b9bee9877cdf8f24edb65.jpeg?imageView2/2/format/webp)

不过我还是用自己的问题好了：「哪些同学报考了 Stanford 并且被录取？」

![](https://cdnfile.sspai.com/2024/11/28/article/c6d2c7c2faae2a7a097bf433648d5022.jpeg?imageView2/2/format/webp)

Claude 通过 MCP 执行查询，告诉我 Amy、Fay、Jay、Helen 这几个学生被斯坦福大学录取，并且说明了他们的 GPA 和专业信息。

![](https://cdnfile.sspai.com/2024/11/28/article/17b01817b3e1d747f57beaee81e9e9bb.jpeg?imageView2/2/format/webp)

Claude 特别指出，**「有意思的是」**被录取的学生中，两名被计算机科学专业录取，两名被历史专业录取，大多数学生 GPA 都很高，3.7 以上，但也有一位学生 GPA 较低，仍被历史专业录取。2.9 的 GPA 也能被斯坦福录取，这确实「很有意思」。

接下来咱们问它第二个问题：「哪些学生没有被任何学校录取，是因为分数太低吗？」

![](https://cdnfile.sspai.com/2024/11/28/article/0e2be1a5183d03863746420c0c6d3e8a.jpeg?imageView2/2/format/webp)

Claude 返回了两个学生的信息，并且说明 Bob 申请了 Berkeley 的生物专业，而 Craig 申请了 MIT 的计算机科学专业。

它总结说，这些没被录取的学生 GPA 其实不低，这表明 GPA 其实不是唯一的录取标准。然后 Claude 甚至还专门给出了报考大学的方法建议。

![](https://cdnfile.sspai.com/2024/11/28/article/6e544887ca1aa404a8e5ee69248d01f6.jpeg?imageView2/2/format/webp)

如果单单使用 SQL 查询，你不可能获得这些建议，这也是利用大模型做数据分析的有趣之处。Claude 通过 MCP 把当前的 SQL 查询结果与申请美国大学的背景知识有机地联系起来，厉害不？

但实际上，**它的回答是错的**。

我教了十多年数据库课，对这个数据集非常熟悉。这里有一个陷阱——这个数据库里，有的学生没有申请任何一所大学。你不申请大学，当然不可能被任何一所大学录取，对吧？因此，在回答这个问题的时候，你的查询不能只看那些全部申请都被拒的学生。

所以我进一步提示它：

> 注意被所有申请的学校拒绝和没有被任何一所学校录取是不一样的。

![](https://cdnfile.sspai.com/2024/11/28/article/c2bf8c77088f33c35b40fc9884ec6b9b.jpeg?imageView2/2/format/webp)

我只提示到这，并没有说「有的学生没有申请学校」。但 Claude 很聪明，马上反应过来。它依然先找出所有提交过申请但没被录取的学生状况。后来它说，「让我们看看数据库中还有哪些学生是完全没有提交任何申请的」。注意这个查询，是它自己总结出来的。

![](https://cdnfile.sspai.com/2024/11/28/article/6b78be9d6fe0082cb0158028ca6a886c.jpeg?imageView2/2/format/webp)

综合分析后，它的答案是：刚才答案中那两个没有问题，是申请后却被所有申请的学校拒绝的学生；但还有若干完全没有提交申请的学生，分别是 Doris、Amy、Gary 和 Edward。

它还补充道，「这确实是两种完全不同的情况。谢谢您的纠正」。

很懂礼貌嘛，孺子可教。

**限制**
------

Claude MCP 给我们带来的，绝不只是查询更简单、结果更全面、数据更安全这样的优势。至少，它**打破了 Claude 处理数据长度和类型的限制**。在 Claude 对话里，你想上传文件，就会看到限制——最多五个文件，每个文件不得超过 30 兆。

![](https://cdnfile.sspai.com/2024/11/28/article/b36d094ff7e448a07d643722602059a3.jpeg?imageView2/2/format/webp)

我找了一个上课时用到的数据库叫 movie.db。这个数据库包含了若干年的电影信息，虽然只有 246.7 兆，但这样的文件想在现在的 Claude 对话当中使用，那断然是不可能的。

![](https://cdnfile.sspai.com/2024/11/28/article/ba493253f57505a8bd09624f5add81af.jpeg?imageView2/2/format/webp)

你上传不上去，不仅仅是因为它体积太大，更是由于这种 `.db` 格式 Claude 就不允许上传，你连选择它都没有机会。

![](https://cdnfile.sspai.com/2024/11/28/article/f9a8b8b086fcf2bf230b6d9691e02429.jpeg?imageView2/2/format/webp)

这些文件都是灰色的，不能点选。但是现在不一样了，我们直接把配置 MCP 路径修改成 movie.db，然后来连接。

![](https://cdnfile.sspai.com/2024/11/28/article/6a4a91531e65c4af0b7ba0b3d28feb26.jpeg?imageView2/2/format/webp)

Claude 找出这里面有三张表，分别包括了电影、演员和他们饰演角色的记录。

![](https://cdnfile.sspai.com/2024/11/28/article/c3ba1ade0b3e03b625c93854459986bf.jpeg?imageView2/2/format/webp)

我问：「有多少女演员同时出演过《哈利・波特》电影的前两部？」你不要小看这个问题，你首先得知道《哈利・波特》电影的前两部都是啥。Claude 查询经过一些波折，但它非常勤恳地重构查询，然后告诉我们，这两部电影分别是《哈利・波特与魔法石》和《哈利・波特与密室》。

![](https://cdnfile.sspai.com/2024/11/28/article/e60c9b8914c50103c9ec61420ecf1fc5.jpeg?imageView2/2/format/webp)

之后它列出了 8 个同时出现在两部电影中女演员的名单，还介绍了这个系列中的主要角色，如赫敏和麦格教授。我觉得这个回答非常好。

如果你在学习 SQL，那么还可以打开它的中间分析过程来查看完整 SQL 语句。

![](https://cdnfile.sspai.com/2024/11/28/article/2351cabb525d5f5c3ea3e7456c0c4687.jpeg?imageView2/2/format/webp)

你可以自己用 SQLite 工具来验证查询结果。但更多时候，你兴许能从它的答案中得到参考和借鉴。

**小结**
------

我必须说明一点——本文所演示的内容，只是 MCP 能力的**冰山一角**。 MCP 现在支持的数据服务，就已包括 GitHub、Google Drive、Slack 等。

![](https://cdnfile.sspai.com/2024/11/28/article/06c4e524b5375ef00ee5a06d5fb22d2d.jpeg?imageView2/2/format/webp)

甚至，你还可以用十几分钟的时间，干脆构建一个自己的 MCP 服务。官网分别提供了 Python 和 Typescript 语言版本的对应教程。

![](https://cdnfile.sspai.com/2024/11/28/article/035587ce2f617382a853024374706454.jpeg?imageView2/2/format/webp)

而仅从 SQLite 的样例看，MCP 目前就可以连接本地数据库，不用像原先那样把整个数据来回上传下载。安全性和控制力比以前显著增强。

Claude 通过 MCP 作为中介，能很好地分析 SQLite 的数据集。在咱们展示的例子中，MCP 的优点是把大模型和数据有机结合起来——通过对外部世界规律的微妙体悟，在真实任务中有效帮助你充分利用自己的数据。

提示词的清晰度依然很重要。例如刚才提到的「申请了学校但没有被录取」和「完全没有申请学校」这样的问题，有时还需要我们引导一下。

试想我们把不同的数据来源综合起来，在一个对话中综合调用，这种感觉像更是一种「化学反应」，想想就让人兴奋。希望 MCP 的出现，能激发你的创意，让你利用多元数据集获得更为深入的洞察。

还是那句话，「临渊羡鱼不如退而结网」。与其看个热闹，不如自己动手试一试。哪怕你只是按照 Claude 官网的教程走一遍也好，相信也能获得更为直接的感悟。

欢迎你把自己尝试 Claude + MCP 的结果分享在留言区，我们一起交流讨论。

祝 AI 辅助数据利用愉快！

如果你觉得本文有用，请**充电**。

如果本文可能对你的朋友有帮助，请**转发**给他们。

欢迎**关注**[我的专栏「科研利器」](https://sspai.com/column/245)，以便及时收到后续的更新内容。

[点击这个链接加入少数派会员](https://sspai.com/prime/subscription?referral=314606736)，立享 9 折优惠！获得专属会员内容、会员播客以及会员定制周边。在更多的领域和方向帮你打开脑洞，找到新的兴趣点。与少数派一起洞悉当下，探索新知。

**延伸阅读**
--------

*   [如何用 Claude 3 Haiku 帮你低成本快速自动分析数据？](https://mp.weixin.qq.com/s/Ap3DKHweoHkHNkI61-_IWg)
*   [如何不写 SQL ，探索和分析数据库？](https://sspai.com/post/58074)
*   [如何用 MOOC 组合掌握机器学习？](https://mp.weixin.qq.com/s/6N8DuxVjA5XvedqQW1juDg)
*   [如何用 Llama 3 免费本地 AI 分析数据和可视化？](https://sspai.com/post/88285)
*   [Claude 3 能辅导你的数学作业了？](https://sspai.com/post/87086)

> 关注 [少数派小红书](https://www.xiaohongshu.com/user/profile/63f5d65d000000001001d8d4)，感受精彩数字生活 🍃

> 实用、好用的 [正版软件](https://sspai.com/mall)，少数派为你呈现 🚀