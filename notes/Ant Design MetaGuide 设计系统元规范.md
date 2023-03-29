> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/MymdmGPL7zlMQy_9ZSIWMQ)

> 🙋🏻‍♀️ 编者按：本文是蚂蚁集团体验设计师梓义在 Ant Design 5.0 发布会上的分享内容文字版，《Ant Design MetaGuide 设计系统元规范》，希望将蚂蚁内部建设设计系统的理论、方法分享给大家，也期待业内同行指正与交流～

支付宝体验科技 ，赞 22

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JDTicNSk00Qyc10GWpylGtIzBtk9MXM4ovSgJ2q13P7lrLMr7st0uxfQ/640?wx_fmt=png)

 Part 1：如何确定做什么组件？
==================

回到今天主题的第一部分，如何通过 ADM 元规范来确定设计系统应该要做什么组件呢？

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JviaepAUsjxe9g1yfzX6veictOTB4OKh7X6wopJ84muFjiaoQ0iauxhiaupw/640?wx_fmt=png)

在讲解之前，我想先和大家分享一则小故事。

在我上大学的时候，有一门设计课程，导师要求我们用废弃的瓦楞纸设计一把让人叫好的椅子。起初我心里犯嘀咕，常见的椅子不都是木质、塑料或钢铁材料设计而成的吗，瓦楞纸怎么可能设计出一把好椅子？

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JMZibJoyPynFpbnNVZjxenQlViaX0uvIVWDOT1QHSdvSc8zTNxialKQSgA/640?wx_fmt=png)

老师接着说，所谓椅子的设计，不是要我们去思考要设计 “什么颜色的椅子”、“什么材质的椅子” 抑或 “什么形状的椅子”。他希望我们回归用户去思考，设计椅子的初衷究竟是为了什么？是为了满足用户“坐” 这个行为目的。

因此，所以 “设计一把椅子”，其本质不是设计一把怎样的椅子，而是设计 “坐”。真正的设计者，即使面对瓦楞纸这样一个简单的材料，一样能设计出一把用户心满意足的椅子。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JtANIMtyIJ8EziaCYp23hibfWibmGMrzuzNZXuHhGEvJmzibqEQotgyazYA/640?wx_fmt=png)

初入设计学的我，醍醐灌顶。

其实，为设计系统设计一个组件，一样如此。用户通过界面使用我们的组件，并非为了用而用，而是为了达到他的某种行为目的。如果我们回归到这样一个简单的本质，那么对于组件的定义可能就十分清晰了。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18Jcs8ibC3jjUNmiaG2PVicTg20RF6DFc2Z7lPANo2JyQMNcXXvVvPgJNMgw/640?wx_fmt=png)

回顾一下过去我们都是如何建设一个设计系统的。

我们可能会去看看竞品做没做这类组件、我们可能会听从老板的建议增删某类组件、我们也可能掉入满足所有业务诉求的陷阱当中，让用户之声成为系统建设的依据。诸如此类的判断标准，正是一种从现状和多决策因素出发形成的归纳性思维，在设计系统构建过程当中，它容易让我们优先思考组件的表层属性即其作为物品时的属性，从而作出建设的判断。正因如此，我们才经常会看到类似组件越做越多、越做越不成规范的设计系统。

归纳性建设思维，在建设  0-1 设计系统时很快。但当设计系统伴随企业发展变得越来越成熟，这样的建设思维就会出发很多 “规范不规范” 的难题。

这时，我们要回归本质，去源头寻找组件建设的决策依据，一个确定的能让团队共识的决策依据。这个依据正是 ADM 所倡导的，将建设视角从归纳视角切换成演绎视角，认识到真正的设计规范不是「物的规范」而是「行为的规范」。演绎视角是一种探究本质的思维，这与 “第一性原理” 一样，我们需要去寻根究底何为组件的本质。

想想椅子的故事，组件的本质正是为了满足用户某种行为目的。因此，组件做到和中程度取决于你要多大程度的满足用户的某个行为目的。根据组件满足的行为目的不同，我们就会产生组件的分类，更进一步，根据衍生出来的行为目的，系统会建设对应组件变体。

将设计系统理解为「行为的规范」，我们便能对系统的边界有清晰的认知，当下应该建设到何种程度，未来又该往何处发展。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18Jgnjo0I1CWJYYJKTHsTiaHCLBPib4Gp1z1vibhXz7YuBnYySd3TKIemUsg/640?wx_fmt=png)

理论比较抽象，接下来我将带着一个小工具帮助大家消化这个理论，并真正理解它是怎样在建设过程当中其作用的。这个小工具我称之为「组件行为模式地图」。

以开篇提到的 DatePicker 日期选择器为例，我们使用行为模式地图来对其进行定义。

首先我们知道，使用日期选择器不是用户的行为目的，用户是想通过它来达到「输入时间型数据」这样一个行为目的，根据时间型数据的不同，行为目的可细分为「输入时间点」和「输入时间段」，因此无论如何我们的设计系统，必须提供对应的必备组件，供用户使用（下图蓝色部分是 3.0 之前建设完成的组件）。

伴随企业数字化的发展，用户希望能够更快的达成他的行为目的，因此在原来的行为目的之上产生了衍生的行为目的，如「更快捷的选择某个特定日期的数据」等，因此我们在设计系统里，提供了对应的组件交互变体（下图中绿色部分是 5.0 本次新增的变体组件）。请注意，因为组件交互变体满足的是衍生行为目的，所以理论上我们的设计系统不提供对应的交互变体，用户一样能够完成他的行为目的。

在考虑完组件的定义之后，我们根据对应服务的企业和业务的差异，为组件穿上全局一致的 “衣服”，便完成组件的建设。当然如果企业对于本组件有特殊的样式要求，通过判断这种样式具备一定的通用性，那么我们也可以新增一个组件的样式，以便进一步提效，这样我们便有组件的样式变体。

当然，我们希望样式尽量通过底层 token 灵活性提供给业务自行改造，尽可能克制的在系统中增加样式变体。

通过这样一个行为模式地图的拆解和定义，一个完整的「DataPicker 日期选择器」便定义和建设完成了，而它未来的生长方向也有了清晰的预判。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JgBA2GCMx2VVBB5BMibK9ma6ziar2CPXWVIMCQ5h8QoNV1Hv6nDicTyB6w/640?wx_fmt=png)

再来看一个设计系统里比较复杂的案例，重型表格组件。

在 Ant Design 5.0 当中，我们也将内部使用多年的 6+ 重型组件开放出来给大家使用。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JaOsPS7ver8YdNnXF0j4vcCVSQyrkcGOia6JC5qKxrxGJaibP3YXds13A/640?wx_fmt=png)

任何一个设计系统都有关于表格组件的定义，但各有差异。这种差异的根源也正是从物的规范去理解表格这个资产带来的。过去我们内部也是从物的角度去抽象结构表格的建设范围，会发现越抽象越多，最终只能交给专家感性判断做什么与不做什么。

如果我们转变成 ADM 元规范的视角来指导表格的建设， 我们能得到什么？我们能得到：

1.  一个有内在统一逻辑，可维护，可拓展的表格组件架构图
    
2.  组件不容易重复建设，不会使表格演变的越来越冗余
    
3.  有清晰的表格组件演进路径，容易判断当下应该先做什么
    
4.  建设者认知统一，从生产到消费的产物都能达到内在的统一
    

由于时间原因，大家可自行对比查阅下列两张资产架构图的差异点。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18J0ZCHHjUQicRcq1V97cBd1XZhZxxp8nQmTqTjBHWYBy5maTYpYAqYhfw/640?wx_fmt=png)

我们将视野从组件拔到整个设计系统层面，理论上，如果能对每类组件逐一定义其「行为模式地图」，我们可以便能等到一张完整的设计系统的行为模式大图。这张大图告诉了我们，设计系统的边界在哪里，现状是怎样的，未来可以往哪里发展。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JusvrO1gVB5nWQBufiaO9f7KrU97ib3bNU6JmibLsibzbsCZicfWkHx4uFwQ/640?wx_fmt=png)

从而，让设计系统更「确定」，让基于其发展的领域设计子系统具备「生长性」。

这里也跟大家分享我们的一些判断，如果你所服务的企业还处于中小型初创阶段，那么你可以建设一个满足必要行为目的的「0-1 通用设计系统」，用 ADM 确保组件交互是收敛确定的，用底层 token 的灵活度机制让样式足够的开放，可被灵活定制。如果伴随的企业业务壮大，你可能需要更成熟和大型的设计系统，此时你可以通过满足更多的行为目的和衍生行为目的来构建一个「1-N 通用设计系统」。

更进一步，如果你所服务的企业是像蚂蚁一样具有海量业务场景的平台型企业，那你可能需要依赖通用系统去生长出适用于不同业务场景的领域设计子系统，去进一步深入到不同领域里提供更高效、垂域的设计解决方案。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JTkfHjnz8nbvvDn9XZ4zTor44ibGNMYkic1m5U5Ze9L7jMIWsTEeurqjw/640?wx_fmt=png)

以上就是关于 ADM 元规范第一部分的介绍，分别从意识层和方法层介绍了如何定义组件。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JoQjKZCByLQb3qIrmwoErvnvuJtX3chCOZDPvQckbGbzuOh0aEnVUSA/640?wx_fmt=png)

是不是挺简单的😄？只要意识转变一下，再借助一些小工具，很多疑问就会迎刃而解～

 Part 2：如何评估组件质量？
=================

通过 Part 1 的分享，我们知道，设计系统规范的本质不是定义「物的规范」，而是定义用户的「行为规范」。满足一个「行为目的」可以有千万种组件解决方案，那么哪种方案是最适合成为规范的呢？接下来第二部分就会跟大家分享如何评估组件方案的好坏。

我们知道，设计系统不单纯是个规范，其本质是一个设计工程化的「产品」，它的用户是千千万万的业务。因此对于系统质量的评估必然分为两个层次，第一，系统本身组件的质量；第二，系统被应用到业务后的表现情况。今天今天这里仅与大家交流如何评估组件质量。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JECVGhibHIzShjuwN6APMt8MIIHCDywelb2g0ZSBiaom4Hia5umE1OECTw/640?wx_fmt=png)

在设计研究领域，调研思路可抽象为以下几类。第一 part 提到组件的本质是「行为的规范」，因此对组件的评估，我们同样聚焦到如何评估「组件行为」这个本质事情上。因此我们选择更客观、独立于业务的底层的人机交互模型，通过它评估而来的结果能 100% 客观反馈交互行为的质量。

在人机交互领域、在学术界，这类涉及人机交互底层的评估模型其实有很多，大家所熟知的 Fitts 菲兹定理就是典型代表之一，另外还有非常具备影响力的 GOMS 行为交互模型。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JTIgyqLoAoeGUEcRVNicc20JK44AxwIhQicgjEiaAI6HkoqtedoCmsDLkA/640?wx_fmt=png)

ADM 元规范的组件评估模式正式基于 GOMS 人机交互模型演变而来。GOMS 是 1983 年 Card 等人在《The Psychology of Huamn Computer Interaction》首次提出的一种交互建模方法，它的核心理念是将用户与界面交互行为拆解为行为元动作，无需真实用户介入就能预测交互行为的序列，评估交互的质量。

后来的学者基于此理念不断构建了适用于不同人机交互界面的行为评估模式，包括 GOMS-KLM（用于 PC 端，由外国学者创建），GOMS-HLM（用于移动端，由中国北邮人机交互实验室创建）等。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JRa3kqPJSFAWOKxnBdiat8BadrCPbwQXno6GjwZB2DEqGMBLh7UlSMaA/640?wx_fmt=png)

关于 GOMS 的更多介绍就不在这次演讲里展开，接下来我们通过一个实际的案例来跟大家介绍我们是如何评估组件质量。

我们来看一下设计系统里最简单不过的一个组件——Button。如何评估 Button 的行为质量呢？我们来看下，用户完成一次按钮点击行为，它的具体流程和构成是怎样的：

*   step 1：用户需要意识并辨别按钮在哪里——记为一次心理单元（即认知） M
    
*   step 2：用户需要将鼠标指针移动到按钮上面——记为一次指向单元 P
    
*   step 3：用户点击鼠标，完成一次按钮点击行为——记为一次按键单元 K
    

如此，我们定义了按钮的交互行为序列为 MPK。更进一步，如果我们能测得这些元动作的交互效率，我们就能得到按钮这个组件的交互质量了。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JgB2r0xUpI5TshwdEjm8hepDT6bpCzia8ia0afibfZHXeAPYXibawCMzwyQ/640?wx_fmt=png)  

实际上，确实可以。在每一个基于 GOMS 演变而来的模型，人机交互学者都通过海量的样本测的每一个交互行为元动作的平均耗时，我们称之为典型值。如果我们直接用典型值预测界面的交互效率，其结果与真实用户监测测得的耗时极其接近，几乎所有预测值都在测量值 1 个标准差之内。

这里以 GOMS-KLM 模型为例，展示了对应的元动作，包含物理操作单元、心理反应单元和系统反应单元三大部分，其对应的典型值附在拉单元下方。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JHSlXdwr7AgicTqjM0DdrB5RzLxg5lahp43vtW4ib0cY7DYyZmrOty6Fg/640?wx_fmt=png)

理论讲的有点多，我们直接来看一些案例就知道怎么应用了。

以我们开篇第四个问题提到的组件「平铺式多选器」为例，我们到底该选哪个方案作为规范纳入设计系统当中呢？

首先，该组件的本质是满足用户「选择并输入 x 个数据对象」的行为目的，那么我们对输入 1 个（即单选）和输入多个（即多选，以多选三个标签为例）的两个具体行为过程进行评估。

*   单选场景：二者虽然样式有所差异，但交互行为序列均为 MPK，交互效率均为 2.65 s
    
*   多选场景：前者为 MPKMPKMPK，交互效率为 7.95s，后者为 MPKPKPK 交互效率为 5.25s
    

从结果来看，方案二更好，进一步根据序列分析，方案一由于选中前后的 UI 结构不稳定，导致用户在每个重复的选择行为前面都增加了一个心理反应时，因此综合表现不佳。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JT2ibwkEgXk36cBiccpVyG1Wyr3PnILfS3r0xMfQTdAibAV9F8VFxyrnrQ/640?wx_fmt=png)  

时间原因，就不再讲解更多案例。这边给大家预留一个小作业，我们在 4.0 的时候升级了 3.0 日期选择器的交互，请大家用元规范评估模型评估一下哪个方案更好？

这边先告诉大家答案，实际上是 3.0 的表现更佳～ 说明我们走了些弯路😄。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JoLyQx9aTVLWnztibibSib5iawUV67hps0xtGibNr2jbtYNt0vaeHFqOFvIA/640?wx_fmt=png)

在评估的过程中，我们也发现了一些小规律，这些小规律其实验证了对我们在日常设计中一些感性判断。比如， 让人们选择多次好过让人们在多个选项中选择之类的；比如，尽量不要键鼠切换等等。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JqfkBQvFrudKKmRmQ5zEcN6QDMvicX0arhe1SiaGGoE0ZbbkS4dsCB4mw/640?wx_fmt=png)

在蚂蚁内部，我们还将原规范应用到其它设计系统的建设当中。比如，Ant Design Mobile 5.0 今年引来大版本的更新，从 200+ 的冗余组件收敛成了 68 个通用组件，同时通过评估发现其组件质量也提升了不少，系统中单组件平均交互行为耗时从 3.42 降到了 3.17，高于业界平均。

大家可以设想一下，一个组件虽然只提升了零点几，但当乘以所有组件数量、组件使用频率和用户数，每年能为用户省下的工作时间也相当可观。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JzjYtak4o8ujx6rM81dovPicHrWzTru3ceEozDVdWbKu7iaazPHgMsT6A/640?wx_fmt=png)

最后，总结一下第二 part 的内容，我们分享了如何去评估系统组件的行为交互质量。在 ADM 的评估框架里还包含了系统级层面的评价，包括：

*   系统是否帮助业务提效
    
*   当业务不能直接应用系统，能否灵活改动
    
*   系统是否帮助业务变得更有竞争力、更创新了
    

这部分内容时间原因不展开细讲了，欢迎大家私下找我交流

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JLOfXRtSeRMnEhXxfLe5N9BVITdQc3ZlCB36b578JqmD4waTWSgW4ag/640?wx_fmt=png)

 尾声
===

好的，关于 ADM 元规范的两部分内容，我们就介绍到这里了。如它山开场分享所言，ADM 元规范的诞生有其大的历史背景，也有蚂蚁大规模业务场景的孕育。随着企业业务场景的增长，对 Ant Design 有更高的要求，同时内部又不断基于 Ant Design 孕育出各式各样的场景设计子系统。

这些都倒逼我们去创建更确定的设计系统、去定义解决设计系统本源的问题，授人以渔，帮助各位系统的建设者更好的去构建和发展自己的设计系统。

![](https://mmbiz.qpic.cn/mmbiz_png/M7OtEw9eDKGbkn3a8J80yNwiabpkyn18JwEdiaLzACiafOLhcI1QiaZicrUrghEote9jGgI2RcBTj4e6Sl14xrIRk6A/640?wx_fmt=png)

不足之处，还请谅解，大家如果有任何建议和意见，欢迎随时与我们交流～

*   官网 https://ant-design.antgroup.com
    
*   Github https://github.com/ant-design/ant-design
    
*   语雀专栏 https://www.yuque.com/ant-design/ant-design
    
*   知乎专栏 https://www.zhihu.com/column/c_1564262000561106944
    

![](https://mmbiz.qpic.cn/mmbiz_png/r15dUC0ACU0CrTuOR8xHiarkpbaBicaZU1EicWO6mhoUicnqORteWvYU23BkwD86AvUqVdfFpOHAmtrtlebuzdh7kw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

蚂蚁开源官网  

https://opensource.antgroup.com  

蚂蚁开源 GitHub  

https://github.com/alipay

有点意思，那就点个关注呗 💁🏼‍♀️

👇🏾 点击「阅读原文」，在评论区与我们互动噢