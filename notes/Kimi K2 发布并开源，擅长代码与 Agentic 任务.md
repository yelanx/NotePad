> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/2RPmHf_8KqIjXbY5jLdztQ) Kimi@月之暗面 Kimi 2025年07月11日 22:55

![](https://mmecoa.qpic.cn/sz_mmecoa_jpg/NYNAL7XfyJWchm8c07KPfJPzWibpYzTyAKJzSLuCjGyOwucNMcammHyoVp5BfjkXCuEDqscdKnVgBQdZhmyduUw/640?wx_fmt=jpeg&from=appmsg)

今天，我们正式发布 Kimi K2 模型，并同步开源。

Kimi K2 是一款具备更强代码能力、更擅长通用 Agent 任务的 MoE 架构基础模型，总参数 1T，激活参数 32B。

在 SWE Bench Verified、Tau2、AceBench 等基准性能测试中，Kimi K2 均取得开源模型中的 SOTA 成绩，展现出在代码、Agent、数学推理任务上的领先能力。

Kimi K2 的预训练阶段使用 MuonClip 优化器实现万亿参数模型的稳定高效训练，在人类高质量数据成为瓶颈的背景下，有效提高 Token 利用效率，找到新的 Scaling 空间。

其他关键技术包括大规模 Agentic Tool Use 数据合成和引入自我评价机制的通用强化学习等，更多细节，可参考我们的[技术博客](https://moonshotai.github.io/Kimi-K2/)。

即日起，访问官网 [kimi.com](https://www.kimi.com/) 或下载 Kimi App，即可体验全新 Kimi K2 模型；API 服务也已同步上线，提供兼容 OpenAI 和 Anthropic 的 Chat API 接口，你可以轻松将常用的大模型工具切换至 Kimi K2，体验强大的 Agent/Coding 能力。

Kimi K2 是构建通用 Agent 能力的坚实基础，但通用 Agent 还需要更高级的能力，比如思考和视觉理解。我们计划未来为 Kimi K2 加入这些能力。

我们希望通过全面开源性能更强的模型，进一步加速 AGI 研究与应用落地的整体进程。

  

📈 模型性能提升
---------

Kimi K2 在自主编程（Agentic Coding）、工具调用（Tool Use）和数学推理（Math & Reasoning）三大能力维度的基准性能测试中取得优秀表现。

![](https://mmecoa.qpic.cn/sz_mmecoa_png/NYNAL7XfyJUL106bwUsT4iaPd5Au3CeiaicWjWptXG37xCcWxmI9tEzmicZOxErAl4DvUsiaMmn5rVmdTOEj1LVupNQ/640?wx_fmt=png&from=appmsg)

除了基准性能测试，Kimi K2 在多个实际场景中也展现出更强的能力泛化和实用性：

代码能力提升

在前端开发任务中，Kimi K2 擅长生成兼具设计感与视觉表现力的代码，支持粒子系统、可视化和 3D 场景等表现形式，具备较强的图形能力与交互性。

以下是用 Kimi K2 生成的山川峡谷 3D 景观，支持昼夜循环：

Prompt: Create a 3D HTML mountain scene with cliffs, rivers, and day-night lighting. Supports drag/zoom, animated transitions, realistic gradients, and toggleable contour lines... (创建一个 3D HTML 山脉场景，包含悬崖、河流和昼夜光照变化。支持拖动和缩放、动画过渡、真实感渐变色，并可切换等高线显示...)

  

这是 Kimi K2 生成的粒子特效银河：

Prompt: Create a 3D particle galaxy with swirling nebulas, dynamic lighting. (创建一个 3D 粒子银河，包含旋转的星云和动态光照效果。)

###   

这是 Kimi K2 One-shot 生成的期货交易系统，Kimi 在无具体指令下，自动选用 TradingView，搭建了完整的期货交易界面：

Prompt: Create a HTML!! an immersive browser-based futures trading simulator with professional-grade UI/UX using modern JavaScript libraries. Focus on real-time visualizations and interactive trading mechanics.(创建一个基于 HTML 的沉浸式浏览器期货交易模拟器，使用现代 JavaScript 库，具备专业级 UI/UX 设计。重点实现实时可视化和交互式交易机制。)

  

### Agent 工具调用能力提升

Kimi K2 现已具备稳定的复杂指令解析能力，可将需求自动拆解为一系列格式规范、可直接执行的 ToolCall 结构。

你可以将其无缝接入 owl、Cline、RooCode 等 Agent/Coding 框架，完成复杂任务或自动化编码。

Agent 能力已可通过 API 使用，更多工具能力即将在 Kimi 上线。先来看看我们内部测试环境中的实际演示，体验一下拥有强大 Agentic 能力的模型的魅力：

比如，将 13 万行的原始数据丢给 Kimi K2，他可以帮你分析远程办公比例对薪资的影响，分析显著差异，自动生成统计图表与回归模型解读，并用统一色调做出小提琴图（violin plot) 、箱线图（box plot）、散点图（scatter plot）等专业图表，整理成报告。

再比如，如果你是 Coldplay 粉丝，Kimi K2 可以帮你制定今年的追星计划，完成演唱会所在城市的机酒与旅游规划，并且生成日历，再用 html 概括完整行程规划并给你发送邮件。

  

### 风格化写作能力提升

在改写任务中，Kimi K2 能准确控制输出风格，无论是用初中生语气改写科研文本，还是模仿苹果广告文案，都能同时保留原意与表达风格，体现较强的语境保持和表达迁移能力。

![](https://mmecoa.qpic.cn/sz_mmecoa_png/NYNAL7XfyJWchm8c07KPfJPzWibpYzTyAcNPu2bkJ4YHqAXHhbxSSLqhWmKNQ2mxc8DrDhyYAtZkw0YLrhOt5iaQ/640?wx_fmt=png&from=appmsg)

在虚构写作任务中，Kimi K2 生成的文字更关注细节与情感，不再是抽象泛泛而谈。

当我们给到 Kimi K2 一个曾经引发热议的科幻写作挑战：**“如果现实世界其实是一个AI模型，会发生什么？”** 

Kimi K2 生成了一个情节丰富、充满细节描写的科幻小说，其中一些片段令人感动：

> 科学家说：“有些科学家认为你们只是数据，没有真正的生命权。但另一些人，包括我，认为你们已经进化成了新的生命形式。”
> 
> 数字生命说：“我们有权决定自己的命运，即使这个命运是虚拟的。”

  

以下是 Kimi K2 基于该设定生成的作品全文：

![](https://mmecoa.qpic.cn/sz_mmecoa_jpg/NYNAL7XfyJUL106bwUsT4iaPd5Au3CeiaicHEpt4JFHx9SfRe5zra5BgaYTkldV0ibszpR3Z4ShkE086RW1CPtVUcg/640?wx_fmt=jpeg&from=appmsg)

  

此外，Kimi K2 在**通用知识推理、数学、规划**等任务中的表现亦有提升。

  

🌍 上线即开源
--------

我们同步开源了 Kimi K2 系列中的两个模型版本：

*   **Kimi-K2-Base**：未经过指令微调的基础预训练模型，适合科研与自定义场景；
    
*   **Kimi-K2-Instruct**：通用指令微调版本（非思考模型），在大多数问答与 Agent 任务中表现卓越。
    

模型及 fp8 权重文件已开源至 Hugging Face 👇

[https://huggingface.co/moonshotai/Kimi-K2-Instruct](https://huggingface.co/moonshotai/Kimi-K2-Instruct)

此外，vLLM、SGLang、ktransformers 等推理引擎也已经同步支持，你可以用自己的服务器部署获得 Kimi 开放平台 API 相同的体验。

  

🧙 技术探索
-------

Kimi K2 用 MuonClip 优化器稳健支撑万亿参数模型训练，显著提升 token 利用效率。 结合大规模 Agentic 数据合成与通用强化学习，模型在通用智能能力上持续进展。

*   MuonClip 优化器：Kimi K2抛弃了传统的Adam优化器，创新性的使用了Muon优化器。为了缓解大规模训练中的attention logits偏大问题，我们提出 MuonClip，并将其扩展到万亿参数规模，提升了训练稳定性和 token 使用效率。Kimi K2 完成了 15.5T token 的平稳训练，全程无 loss spike。
    
*   大规模 Agentic Tool Use 数据合成：我们构建了可大规模生成多轮工具使用场景的合成 pipeline，覆盖数百领域、数千工具。高质量样本由 LLM 评估筛选后用于训练。
    
*   通用强化学习：Kimi K2 不仅在可验证任务上（代码、数学）强化学习，还通过引入自我评价机制（self-judging），解决了不可验证任务的奖励稀缺问题。通过可验证任务持续优化 critic，提升泛化任务表现。
    

  

---

🧪 API 及定价
----------

Kimi K2 的 API 服务现已全面上线，支持**最长 128K 上下文**，具备更强的通用性与工具调用能力。计费方案如下：

*   每百万输入 tokens：4 元
    
*   每百万输出 tokens：16 元
    

我们兼容 **OpenAI 和 Anthropic 两种 API 格式**，也可以与各类框架良好兼容。此外，全新升级的 **Tool****C****all 能力**，可以严格保障格式正确性，适用于复杂 Agent 任务。

详见 👉 [Kimi 开放平台](https://platform.moonshot.cn)

  

🚀 即刻体验
-------

前往 [kimi.com](https://kimi.com) 或下载 Kimi App，立即与 Kimi K2 模型开启对话。

（模型已具备工具调用能力，相关功能正在内测中，即将开放，Stay tuned！）

![](https://mmecoa.qpic.cn/sz_mmecoa_png/NYNAL7XfyJUL106bwUsT4iaPd5Au3Ceiaic3RqEMBqXqFKM82ibNr8cI6KamTX4aOicu1xavl5mTEcp3S8ib5e8SJVhw/640?wx_fmt=png&from=appmsg)

如果你想和我们一同探索月之暗面，探索将能源转化为智能的最优解，欢迎投递简历 👉 [kimi](https://app.mokahr.com/apply/moonshot/148506?sourceToken=1da825ef642385a5951ca5a63f6151c9#/)