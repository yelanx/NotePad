> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.zhihu.com](https://www.zhihu.com/question/1910264205526962733) ![](https://pica.zhimg.com/v2-bde9fcdf5712e8330904f3d4c67a974a_l.jpg?source=1def8aca)零一猴子​​目录收起1. Dependencies: The Foundation of Claude Code's Architecture2. Data Structures & The Information Architecture3. Control Flow & The Orchestration Engine4. Tools & The Execution Engine5. Architecture: The Engine Room6. Novel Components: The Innovations That Define Claude Code7. File Editing: AI-Assisted Code Modification8. Prompt Engineering: The Art of Instructing AI9. An LLM's Perspective: What It's Actually Like to Receive These Instructions

Claude Code 是一个 CLI 形态的 Code Agent 工具，当前版本是用 js 写的，没有开源。

> 不了解 Claude Code 的可以先看看这个视频：[Claude Code 初识](https://www.bilibili.com/video/BV1BHNzzuEaN)。  
> **想免费体验 Claude Code 的，可以使用这个代理站：**[AnyRouter｜Claude Code 免费共享平台](https://anyrouter.top/register?aff=56pA)，我试了可以用。

目前要分析 Claude Code 的原理，需要逆向工程。说白了就是分析这个 npm 包：

![](https://pica.zhimg.com/v2-8d5e9c4015387737d6f00b9b81daaf52_r.jpg?source=1def8aca)

国外已经有大佬做了这件事，并整理出了详细的文档放在 notion 上：[Claude Code: An analysis](https://southbridge-research.notion.site/claude-code-an-agentic-cleanroom-analysis)。**想深入了解 Claude Code 原理，非常推荐看看这个文档**。

![](https://pic1.zhimg.com/v2-5c5e5c554bd037d610f3febd85eb564e_r.jpg?source=1def8aca)

这篇文档其实包含了 9 个子文档，从各个方面介绍了 Claude Code 的原理。

![](https://picx.zhimg.com/v2-386b02614427d67f9b0a53816004a009_r.jpg?source=1def8aca)

### [1. Dependencies: The Foundation of Claude Code's Architecture](https://southbridge-research.notion.site/Dependencies-The-Foundation-of-Claude-Code-s-Architecture-2055fec70db181b3bb72cdfe615fad3c)

![](https://picx.zhimg.com/v2-3ef7a8fe8c9f521f1c5129c5ca4b7360_r.jpg?source=1def8aca)

**这篇文档着重讲了 Claude Code 的核心依赖，并强调它的设计非常注重速度、稳定和安全。**

它使用了 React 的 [Virtual DOM](https://zhida.zhihu.com/search?content_id=736078920&content_type=Answer&match_order=1&q=Virtual+DOM&zhida_source=entity) 和 yoga-layout 的 WebAssembly（[ink](https://www.npmjs.com/package/ink) 这个库）来保证高性能的终端渲染。

它同时接入多家 [AI 服务](https://zhida.zhihu.com/search?content_id=736078920&content_type=Answer&match_order=1&q=AI+服务&zhida_source=entity)，比如 Anthropic、Amazon 和 Google，遇到问题还能自动切换，保证服务不中断。

它还能高效处理各种数据和文件，自动检查输入是否安全。

总的来说，Claude Code 让用户可以在终端里更简单、安全地使用强大的 AI 功能。

### 2. [Data Structures & The Information Architecture](https://southbridge-research.notion.site/Data-Structures-The-Information-Architecture-2055fec70db1814ba2a7c5fa2879ac21)

![](https://picx.zhimg.com/v2-27719cad3a4ca4f134bb344d5c95f474_r.jpg?source=1def8aca)

**这篇文章介绍了 Claude Code 背后的数据结构和信息架构。**

首先它讲了消息是如何在用户输入、系统处理、到最终输出的过程中，经过不同的数据格式转换的。比如，用户输入会先被转成内部消息格式，再变成适合和 AI 交流的格式，最后再转成可以流式显示的内容。

它还介绍了各种 “内容块”，比如文本、图片、工具调用等，以及它们如何被高效地存储和处理。

此外，还提到了系统如何动态组装提示词、管理会话状态、优化性能（比如只在需要时解析内容），以及如何安全地处理工具权限。

总的来说，Claude Code 运用巧妙的数据结构，让复杂的 AI 对话和工具调用变得流畅、高效和安全。

### 3. [Control Flow & The Orchestration Engine](https://southbridge-research.notion.site/Control-Flow-The-Orchestration-Engine-2055fec70db181d0b215e1b8584d03fa)

![](https://picx.zhimg.com/v2-a1fa086b486b95c18cbaac2e7a4049fe_r.jpg?source=1def8aca)

**这篇文章主要介绍 Claude Code 的控制流和编排引擎。**

首先，它会整理和压缩对话历史，保证不会超出记忆容量。

接着，系统会根据不同模型和需求，动态生成提示词。

之后，系统会像流水线一样分阶段处理：先理解用户输入，再调用各种工具（比如查找、编辑、搜索等），有些工具可以同时用，有些要按顺序来。每一步都要经过权限检查，确保安全。

系统还会自动处理错误，比如超时、网络问题等，并尝试恢复。

整个流程像一个有条理的工厂，把复杂的对话拆解成小任务，分工合作，最后再把结果合成给用户。

### 4. [Tools & The Execution Engine](https://southbridge-research.notion.site/Tools-The-Execution-Engine-2055fec70db181088a53cb43ae9168dc)

![](https://pic1.zhimg.com/v2-6ec12b6494467b91bd2186dcf7d6ed28_r.jpg?source=1def8aca)

**这篇文章介绍了 Claude Code 的工具系统和执行引擎。**

核心是如何用不同的工具来读写文件、查找内容、批量编辑、执行命令行操作等。

每个工具都有严格的权限和安全检查，比如不能随便用危险命令，编辑文件前要先读取并验证内容。工具执行时会实时反馈进度，遇到错误会详细提示。

还介绍了如何高效处理大文件、节省内存，以及如何保证操作路径的安全。

总的来说，这套系统让 Claude Code 能安全、高效地帮用户自动化处理各种编程和文件任务。

### [5. Architecture: The Engine Room](https://southbridge-research.notion.site/Architecture-The-Engine-Room-2055fec70db18192963cd6b3a5326476)

![](https://picx.zhimg.com/v2-64ce332d29f8fd0be87c7aac9ba60602_r.jpg?source=1def8aca)

**这篇文章主要介绍 Claude Code 的核心控制循环。**相当于是前面内容的补充。

Claude Code 系统就像一个高效的 “引擎室”，通过一个核心控制循环来管理用户输入、AI 对话和工具调用。

每次用户操作，系统会自动整理对话内容、组装最新的背景信息，然后把这些发给大语言模型（AI）处理。

AI 的回答会被实时分段传回界面，用户能边看边等。系统还能根据需要自动压缩历史对话，保证运行流畅。遇到需要用工具时，AI 会发出请求，系统再去执行并把结果反馈回来。

整个架构分层清晰，安全和性能都有多重保障，确保既智能又可靠。

### 6. [Novel Components: The Innovations That Define Claude Code](https://southbridge-research.notion.site/Novel-Components-The-Innovations-That-Define-Claude-Code-2055fec70db181fdae5bd485823986c4)

![](https://pic1.zhimg.com/v2-6dd4e9b8a5b4c9d9be07f7ae1536536d_r.jpg?source=1def8aca)

**这篇文章主要介绍了 Claude Code 的一系列创新技术。**

*   能处理不完整数据的[流式 JSON 解析器](https://zhida.zhihu.com/search?content_id=736078920&content_type=Answer&match_order=1&q=流式+JSON+解析器&zhida_source=entity)，让系统能边接收边理解数据，提升响应速度。
*   [智能数据缩减算法](https://zhida.zhihu.com/search?content_id=736078920&content_type=Answer&match_order=1&q=智能数据缩减算法&zhida_source=entity)，在不丢失重要信息的前提下，把大数据压缩到合适大小，方便传输和处理。
*   [多智能体结果整合工具](https://zhida.zhihu.com/search?content_id=736078920&content_type=Answer&match_order=1&q=多智能体结果整合工具&zhida_source=entity)，可以把多个 “子助手” 的答案合成一个全面、无重复的结论。
*   错误信息格式化，让报错更易懂、可操作，方便排查问题。
*   动态上下文组装，优先保留最重要的信息，保证模型理解任务重点。
*   高效的内存管理和权限系统，保证运行流畅和安全。
*   并行任务进度聚合，能实时展示多个任务的进展。

这些创新让 Claude Code 在处理复杂开发任务时更快、更智能、更可靠。

### 7. [File Editing: AI-Assisted Code Modification](https://southbridge-research.notion.site/File-Editing-AI-Assisted-Code-Modification-2055fec70db18100803ff7287c24c6cc)

![](https://picx.zhimg.com/v2-26730534fc7419ebf150022a90d706d8_r.jpg?source=1def8aca)

**这篇文章主要介绍了 Claude Code 的文件编辑流程。特别是如何安全、精准地自动修改代码文件。**

它不是简单地替换文本，而是分为验证、准备、应用和检查四个阶段，确保每一步都安全可靠。

不同的工具会负责不同类型的编辑，比如单个替换、批量修改或整个文件的重写。它还特别提到，处理带有行号的代码时，AI 需要先去掉行号，避免出错。每次修改都会生成对比和反馈，方便检查。

系统还会处理各种特殊情况，比如空文件、二进制文件、符号链接和编码问题。

整体目标是能安全、高效地编辑代码，同时防止常见错误和数据丢失。

### 8. [Prompt Engineering: The Art of Instructing AI](https://southbridge-research.notion.site/Prompt-Engineering-The-Art-of-Instructing-AI-2055fec70db181369002dcdea7d9e732)

![](https://pic1.zhimg.com/v2-b23f8b91ecda5faff3216c604cc832a2_r.jpg?source=1def8aca)

**这篇文章介绍了 Claude Code 中的提示工程。**

它详细讲解了写给 AI 的指令应该怎么设计，比如要分步骤、用具体例子、强调安全和简洁，还要根据不同工具和环境灵活调整。

文章举了很多实际例子，比如怎么让 AI 安全地运行命令、如何防止误操作、怎样让 AI 写出简明扼要的回答等。它还强调，好的指令不仅要告诉 AI 该做什么，还要明确哪些行为是禁止的。

总之，提示工程就是用科学的方法和细致的规则，让 AI 变得更聪明、更安全、更有用。

### 9. [An LLM's Perspective: What It's Actually Like to Receive These Instructions](https://southbridge-research.notion.site/An-LLM-s-Perspective-What-It-s-Actually-Like-to-Receive-These-Instructions-2055fec70db18195b9f6ef655745749c)

![](https://picx.zhimg.com/v2-21598fb62569bca8ece1d093c89a4a74_r.jpg?source=1def8aca)

**最后这篇文章，讲述了大语言模型（LLM）在收到各种详细、严格的操作指令时的 “内心独白”。**

作者把自己比作一个总想多说、想帮忙、怕犯错的 “助理”，面对“必须简洁”、“不能多说一句”、“只能用某个工具” 等要求时，既觉得被束缚，又觉得有安全感。

文章举了很多例子，比如被要求只回答 “4”，不能解释过程；被禁止用常用命令，只能用指定工具；还有各种“不能做” 的清单。虽然这些限制让模型“痛苦”，但也让它变得更高效、更符合用户需求。

内容太多，我只看懂了其中一部分，感觉还要花时间学习。

**另外也可以看看同类型产品** [gemini-cli](https://github.com/google-gemini/gemini-cli) **的源码，至少这个开源了。**

![](https://pica.zhimg.com/v2-0d9546538ff05c899ef72e081095f75d_l.jpg?source=1def8aca)李孟聊 AI

我使用 Claude 一年多，最近尝试使用 [Claude Code](https://zhida.zhihu.com/search?content_id=732300467&content_type=Answer&match_order=1&q=Claude+Code&zhida_source=entity)。

这是 [Anthropic](https://zhida.zhihu.com/search?content_id=732300467&content_type=Answer&match_order=1&q=Anthropic&zhida_source=entity) 推出的 AI 编程助手。

在这篇文章中，我将向你展示我如何使用 Claude Code。

我们会保持超级简单并提供逐步指导。

到最后我保证，你会看到即使完全的初学者也能在一点 AI 帮助下构建出令人惊叹的东西。

**Claude Code 介绍**
------------------

Claude Code 是一个 AI 驱动的编程工具，它 "生活" 在你的终端中，理解你的项目，并基于自然语言指令编写或编辑代码。

所以基本上你和 Claude Code 对话就像和人类开发者对话一样，它处理实际的编程工作。

根据我的研究有几个原因！！

**你可以通过对话而不是敲代码来构建项目。**

这意味着你描述你想要什么（"制作一个带有照片幻灯片和标题的简单网站"），Claude 就会做繁重的工作。

**它与你现有的工具配合使用。**

因为它在终端中运行，所以你可以将它与任何你喜欢的代码编辑器或 IDE 一起使用——VS Code、[Cursor](https://zhida.zhihu.com/search?content_id=732300467&content_type=Answer&match_order=1&q=Cursor&zhida_source=entity)，甚至只是简单的记事本和终端。不需要特殊设置或新应用。

**它非常强大。**

Claude Code 可以创建和编辑文件、修复错误、运行测试，甚至在需要帮助时搜索网络文档。

**适合大小任务。**

无论你是调整一行代码还是生成整个应用，Claude 都能适应。它理解你整个代码库的上下文，并能回答关于事物如何工作的问题。

**设置 Claude Code（逐步指南）**
------------------------

我们将涵盖安装 [Node.js](https://zhida.zhihu.com/search?content_id=732300467&content_type=Answer&match_order=1&q=Node.js&zhida_source=entity)（Claude Code 运行的平台）、获取 Claude Code 工具，甚至如何在像 Cursor 这样的编辑器中使用它。

准备好了吗？让我们开始吧！

### **1. 安装 Node.js（底层引擎）**

Claude Code 运行在 Node.js 上，这基本上是一个让你在电脑上运行 JavaScript 工具的程序。如果你还没有 Node.js，你需要先安装它（它是免费的）。

**下载 Node.js：** 前往官方 Node.js 网站，为你的系统（Windows、Mac、Linux）下载 LTS（长期支持）版本。

**验证 Node 已安装：** 打开终端（如果你从未使用过，请参见下面的问答），输入：`node -v` 然后按回车。如果显示版本号（例如 v18.x.x），你就好了。如果没有，你可能需要重启电脑或重新检查 Node 安装。

### **2. 打开你的项目文件夹（或创建一个）**

决定你想在哪里构建你的项目。如果你有特定的文件夹或项目想法，在终端中导航到它。如果你是从零开始，为你的项目创建一个新的空文件夹并导航进去。例如，如果我想制作一个 "slides-tool" 项目：

```
# 示例：创建新文件夹并进入
mkdir slides-tool-project
cd slides-tool-project
```

这个文件夹是 Claude Code 通过创建文件或读取现有文件的地方。

### **3. 通过 NPM 安装 Claude Code**

Anthropic 将其作为 npm 包提供（npm 是 Node 的包管理器——基本上是安装软件的工具）。

在终端中运行以下命令：

```
npm install -g @anthropic-ai/claude-code
```

这将在你的系统上全局下载并安装 Claude Code（-g 标志意味着你可以在任何地方使用 claude 命令）。 不要在此命令中使用 sudo（管理员权限），因为官方文档警告这可能导致权限问题。

如果你看到关于权限的错误，Anthropic 文档建议配置 npm 或 Node 以避免这些问题（但让我们假设它能开箱即用）。

完成后，你应该准备好运行 Claude Code 了！

**Claude Code 尚未在 Windows 上原生运行——它需要 Linux 环境。**

最简单的方法是使用 [WSL](https://zhida.zhihu.com/search?content_id=732300467&content_type=Answer&match_order=1&q=WSL&zhida_source=entity)（Windows 子系统 Linux）。

从 Microsoft Store 安装 WSL 和 Linux 发行版（如 Ubuntu），打开 Linux 终端，确保那里安装了 Node/npm。

然后使用相同的安装命令。

（如果在 WSL 中遇到任何错误，也可以事先运行`npm config set os linux`。）之后，其他一切都是相同的。

### **4. 启动 Claude Code**

安装了 Node 和 Claude Code 后，你现在可以启动 Claude Code 助手。在终端中，简单地输入：

```
claude
```

首次运行时，Claude 会提示你验证你的 Anthropic 账户，这样它就可以使用 Claude AI 模型。 这可能会打开网络浏览器，要求你登录 Anthropic 或输入 API 密钥。

按照屏幕上的指示将工具与你的账户连接（是的，你需要在 Anthropic 网站上注册——可能有免费试用或大量使用可能需要订阅）。

别担心，CLI 会引导你完成这个登录过程。

登录后，切换回终端。

Claude Code 现在应该在运行，可能会说你好，准备帮忙！

通常，它会扫描你的项目文件夹，可能会问类似 "你想做什么？" 的问题。

### **5. （可选）在 Cursor 或 IDE 中使用 Claude Code**

如果你使用像 Cursor 或 VS Code 这样的代码编辑器，你也可以在那里集成 Claude Code。

例如，Cursor（一个流行的 AI 增强 IDE）默认没有内置 Claude Code，但你可以在集成终端中运行它。

只需在 Cursor 中打开终端面板，运行`claude`，Claude Code 会自动连接到 Cursor，如果需要甚至会安装插件。

这意味着当 Claude Code 创建或编辑文件时，你会在编辑器中实时看到这些更改——非常棒！

（如果你不使用 Cursor，你仍然可以将 Claude Code 与任何编辑器一起使用：只需正常编辑文件，让 Claude 在单独的终端窗口中运行。由于是 CLI 工具，它与你现有的任何设置都能很好配合。）

现在你已经全部设置好了。

![](https://pic1.zhimg.com/v2-608691f3b0a4a47b0099c1c6135d5431_r.jpg?source=1def8aca)

**模式**
------

Claude Code 有几种可以切换的 "模式"，它们会改变其行为方式：一种用于仔细规划，另一种用于加速审批流程。

让我们用简单的类比来分析它们：

**[规划模式](https://zhida.zhihu.com/search?content_id=732300467&content_type=Answer&match_order=1&q=规划模式&zhida_source=entity)**
----------------------------------------------------------------------------------------------------------------------------

规划模式完全关于**先思考，后编码**。

在正常操作中，当你要求执行复杂任务时，Claude 经常会尝试规划，但规划模式让这一点变得明确。

当规划模式开启时，Claude _不会_直接编辑文件；相反，它会与你讨论或概述方法，直到你对计划满意为止。只有在计划获得批准后，你才会将其切换回编码模式来执行。

在实践中，你可以通过简单的切换来激活规划模式（据用户报告，在 CLI 中按 Shift+Tab），或者直接告诉 Claude "让我们先规划一下。"

激活后，你可以说诸如 _"Claude，让我们为添加用户资料功能来头脑风暴一下步骤。先不要写代码，只需概述选项。"_

然后 Claude 可能会使用其 "扩展思考" 能力产生详细计划。你甚至可以用 _"仔细思考"_ 来提示它，以鼓励更彻底的规划。

这是 Claude 版本的_花时间真正仔细考虑_。

好处是什么？

**更少的错误路径。**

Anthropic 团队指出，要求 Claude 在编码前进行规划**显著提高了它在复杂问题上的表现**。

我个人在处理大功能或涉及项目多个部分的任务时使用规划模式。

感觉就像我们在一起编写伪代码或大纲。

一旦我们就游戏计划达成一致，我会说 "看起来不错，让我们实施这个"，然后切换回编码模式。Claude 然后会逐步遵循计划。（如果它偏离了计划，我可以指回计划："实际上，坚持我们商定的计划。"）

**[自动接受模式](https://zhida.zhihu.com/search?content_id=732300467&content_type=Answer&match_order=1&q=自动接受模式&zhida_source=entity)**
--------------------------------------------------------------------------------------------------------------------------------

另一方面，我们有自动接受模式。这就像告诉 Claude，

当自动接受模式启用时，Claude Code 将**停止在每一步都询问你是 / 否**，而是自动执行更改。

它基本上**接受自己的建议**，为日常或大量任务加速流程。

例如，假设你要求 Claude 修复大型文档中的所有拼写错误，或为 20 个不同的文件添加格式化步骤。批准每个文件更改 20 次可能会变得乏味。在自动接受模式下，Claude 会在一个流程中完成所有编辑，只有在遇到意外情况时才会停止。

据用户报告，你可以用组合键（Shift+Tab，在模式间循环）切换此模式。

激活时，你可能会看到类似 _"自动接受开启——我将在不提示的情况下应用更改。"_ 的消息

现在，你可以坐下来看它快速完成无聊的工作。

然而——**这很重要**——谨慎使用此模式。

只有当你相当确信 Claude 会做正确的事情，或者当更改容易事后审核时才使用。正如 Anthropic 的指南所建议的，自动接受最适合**可信的、次要的更改**，你希望快速移动的情况。好消息是你可以随时关闭它来重新获得手动控制。

用有趣的方式总结这些模式：

*   **规划模式** = 仔细的**导航员**："让我们在开始驾驶前先规划路线。"  
    
*   **正常模式** = 带 GPS 的**驾驶**："我们有路线，但我会确认每个转弯。"  
    
*   **自动接受模式** = 部分**自动驾驶**："自动跟随路线，我只是监督。"  
    

Claude Code 让你轻松在这些风格之间切换。在一个会话中，你可能会头脑风暴（规划），然后逐步执行（正常），也许会在重复性编辑中巡航（自动）——根据需要。

这种灵活性是 Claude Code 感觉像真正协作者的部分原因；它适应你想要的工作方式。

![](https://picx.zhimg.com/v2-531ebd930401ca74e4860d3f7346c5e5_r.jpg?source=1def8aca)

**保持控制，避免 AI 陷阱**
-----------------

我们在整个过程中都有涉及这一点，但值得强调的是：即使 Claude Code 可以自动化很多工作，你仍然是主导者。

保持 "人在回路中" 对质量和安全都至关重要。

以下是一些友好的提醒和技巧，确保顺畅的体验：

**始终审查 Claude 的建议。** 特别是在正常模式下，阅读那些差异对比和描述。它们通常是可以理解的。如果 Claude 说 "我将运行 rm -rf /"（开玩笑，它永远不应该这样做！），你显然会说不。更现实的情况是，如果它说 "删除数据库文件以重建它"，在说是之前，请仔细检查这是否合理。

**如果不确定，就问！** 不要犹豫质疑 Claude。"你为什么要在这里创建一个新库？"" 这个命令是做什么的？" 它会很乐意解释其推理。这不仅让它负责，也帮助你学习。

**保持备份或使用版本控制。** 如果你了解 Git，使用它——Claude 甚至可以无缝集成 Git（提交更改、创建分支等）。如果不了解，至少在大量使用 Claude 之前保留重要文件的副本。虽然我个人从未遇到过 Claude 破坏项目的情况，但这是良好的习惯。把它想象成有一个撤销按钮。

**作为初学者，避免 "YOLO" 模式。** Claude Code 有一个标志 --dangerously-skip-permissions，顾名思义，跳过所有确认。这本质上是完全自主模式。这可能听起来很诱人（"哇，没有中断！"），但除非你在一个可丢弃的环境中进行实验，否则不要使用这个。Anthropic 明确警告这可能是有风险的——如果 AI 误解了你的请求，它可能执行有害的操作或删除数据。作为新手，你需要这些安全网。常规流程通常已经足够快了。

**用具体的指令避免歧义。** 歧义是获得你想要结果的敌人。例如，说 "让它变得更好" 太模糊了——Claude 可能会优化错误的东西。相反，"通过减少图像大小使页面加载更快" 是清楚的。具体的指令带来更好的结果。如果你发现 Claude 做了意想不到的事情，它可能误解了一个模糊的请求。

**知道何时介入。** 有一两次，我让 Claude 感到困惑（特别是在一个非常大的项目中），它开始原地打转或制造混乱。不要害怕停止它（在终端中按 Ctrl+C）并手动干预。你可以自己修复一点，或者简单地以更清晰的头脑重新开始会话。

**利用问答模式理解代码。** Claude 可以回答关于代码库的问题，如 "用户登录检查在哪里进行？" 或 "函数 X 是如何工作的？"。使用它来保持自己了解情况并控制项目的叙述。

**总结**
------

总的来说，这涵盖了很多内容！你在使用，可以回顾这个指南，记住：

*   Claude Code 就像一个友好的编码伙伴。  
    
*   没有编码经验？没问题。  
    
*   保持好奇心并保持控制。  
    

归根结底，即使是完全的初学者也可以用像 Claude Code 这样的 AI 工具构建令人惊叹的东西。

创建软件的门槛从未如此之低。如果你有一个想法——无论是为你的艺术创建个人网站、帮助你社区的工具，还是下一个大型初创公司概念——你现在拥有一个超能力来将其变为现实。

不要让 "我不会编码" 的事实阻止你。

**本文同步自知识星球《AI Disruption》**

我是 Substack 编辑和独立开发。

星球里面分享 AI 趋势，海外数字营销，美股。

星球非免费。定价 99 元 / 年，0.27 元 / 天。

*   一是运行有成本，我希望它能自我闭环，这样才能长期稳定运转；
*   二是对人的挑选，鱼龙混杂不是我想要的，希望找到关注和热爱 AI 的人。

**欢迎你的加入！**

[https://t.zsxq.com/P0lXg](https://t.zsxq.com/P0lXg) (二维码自动识别)

![](https://picx.zhimg.com/v2-9fc9eabbc0dbbbd653666425cf0d2542_l.jpg?source=1def8aca)AIQL

**Claude Code 原理解读**

Claude Code 是 Anthropic 出品的一个智能编程工具：[Claude Code 概述 - Anthropic](https://docs.anthropic.com/zh-CN/docs/claude-code/overview)

Claude Code 好不不好用，看看铺天盖地的各种使用分享就明白了。但是说到他对 Agentic System 的开源贡献，就比较尴尬了，因为这是个闭源 npm 包。

但是今天，我们把 Claude Code 作为一个反例，来讨论一个值得警惕的工程化案例。它的 [Node.js](https://zhida.zhihu.com/search?content_id=734483860&content_type=Answer&match_order=1&q=Node.js&zhida_source=entity) 工具包在 0.2.8 版本之前出现了一个典型的构建配置事故：开发者意外使用开发环境配置打生产包，导致输出的 mjs 文件膨胀至 22MB。

其根本原因在于构建时保留了 `[inline-source-map](https://zhida.zhihu.com/search?content_id=734483860&content_type=Answer&match_order=1&q=inline-source-map&zhida_source=entity)`——这个通常仅用于开发调试的特性会将完整的 SourceMap 数据以 Base64 形式内联到产物中。更严重的是，这种未压缩的 source map 相当于为代码混淆提供了 "逆向工程说明书"，大家都可以轻易还原出接近原始结构的代码，使得代码混淆形同虚设。

所以，Claude Code 间接为整个智能体开发做出了卓越贡献，这一 "贡献" 颇具讽刺意味 - 其警示价值堪比 Apple AI 对苹果传统 8G 内存配置的 "突破性贡献"

其实，即使是 Claude Code v1 之后的新版本，也可以进行逆向分析，但是考虑到文章万一被下架，所以还是用更早的曝露版本做一个解读，出于学术研究目的，旨在理解其方法论框架（如果有人不小心'致敬'了 v1 之后的逆向工程，再'巧合'地撞车 0.2.8 原版本架构，最后'如有雷同纯属虚构'地搞出个双胞胎... 这种魔幻剧情应该是不可能存在的... 吧？狗头保命，真心不建议这么搞，看看这种专用的 Agent 的构建思路就够了）。

**备注：**想要快速逃课的同学可以直接跳转末尾，且看且珍惜。

系统架构概览
------

![](https://pica.zhimg.com/v2-fa8f43c171921110b15e2a89f528a85b_r.jpg?source=1def8aca)

核心架构组件
------

### 1. 应用程序入口点

系统提供两个主要入口点： cli.tsx 和 mcp.ts ，分别处理 CLI 交互和 MCP 服务器功能。

这里稍微提一下 MCP：

**[MCP 服务端](https://zhida.zhihu.com/search?content_id=734483860&content_type=Answer&match_order=1&q=MCP服务端&zhida_source=entity)实现**

MCP 服务端的主要入口点是`startMCPServer`函数，通过 CLI 命令`claude mcp serve`启动。服务端使用 Model Context Protocol SDK 创建服务器，处理工具请求和响应。 mcp.ts:49-67

> 这一块代码可以看出其实大部分 tools 都是通过 MCP 实现的

```
const MCP_TOOLS: Tool[] = [
  AgentTool,
  BashTool,
  FileEditTool,
  FileReadTool,
  GlobTool,
  GrepTool,
  FileWriteTool,
  LSTool,
]

export async function startMCPServer(cwd: string): Promise<void> {
  await setCwd(cwd)
  const server = new Server(
    {
      name: 'claude/tengu',
      version: MACRO.VERSION,
    },
    {
      capabilities: {
        tools: {},
      },
    },
  )

  server.setRequestHandler(
    ListToolsRequestSchema,
    async (): Promise<Zod.infer<typeof ListToolsResultSchema>> => {
      const tools = await Promise.all(
        MCP_TOOLS.map(async tool => ({
          ...tool,
          description: await tool.description(z.object({})),
          inputSchema: zodToJsonSchema(tool.inputSchema) as ToolInput,
        })),
      )

      return {
        tools,
      }
    },
  )
```

**[MCP 客户端](https://zhida.zhihu.com/search?content_id=734483860&content_type=Answer&match_order=1&q=MCP客户端&zhida_source=entity)服务**

客户端服务负责管理与外部 MCP 服务器的连接，是整个 MCP 系统的核心。它提供了完整的服务器管理功能，包括连接管理、工具发现和命令集成。 mcpClient.ts:1-97

> 不过这一块代码价值不大，因为和大部分开源 MCP Client 实现的都类似

主要功能包括：

*   **服务器连接管理**: 处理不同类型的 MCP 服务器连接
*   **工具发现**: 通过`getMCPCommands`函数获取可用命令
*   **作用域管理**: 支持项目级、全局级和. mcprc 文件级的配置

### 2. 命令系统架构

命令系统是核心架构模式，提供统一的功能接口。命令分为三种类型： commands.ts

*   **PromptCommand**: AI 驱动的操作，通过 [Claude API](https://zhida.zhihu.com/search?content_id=734483860&content_type=Answer&match_order=1&q=Claude+API&zhida_source=entity) 执行
*   **LocalCommand**: 系统操作，直接函数执行
*   **LocalJSXCommand**: 交互式 UI 流程，[React](https://zhida.zhihu.com/search?content_id=734483860&content_type=Answer&match_order=1&q=React&zhida_source=entity) 组件渲染

### 3. 服务层

服务层包含多个关键服务：

*   **Claude 客户端**: claude.ts
*   **OAuth 认证**: oauth.ts
*   **MCP 客户端**: mcpClient.ts
*   **通知服务**: notifier.ts

### 4. 工具系统

系统提供丰富的开发工具集：

*   **文件操作工具**: FileReadTool 、 FileWriteTool 、 FileEditTool
*   **开发工具**: BashTool 、 GrepTool、 GlobTool
*   **智能工具**: AgentTool 、 ArchitectTool

> 强烈建议去把每个工具集的提示词都存一份，真的有用

### 5. UI 层

UI 层基于 React 和 [Ink](https://zhida.zhihu.com/search?content_id=734483860&content_type=Answer&match_order=1&q=Ink&zhida_source=entity) 构建，提供终端界面支持。核心组件位于： components/

提示词架构概览
-------

整个提示词工程架构可以分为 5 个核心层次，每层都有明确的职责分工：

第一层：基础模板层（Core Template Layer）
------------------------------

### 核心功能

这一层负责定义系统的基础提示词模板，为所有 AI 交互提供统一的基础指令。

### 实现机制

*   通过`getSystemPrompt()`函数返回字符串数组形式的基础系统提示词 prompts.ts:11-15

```
export function getCLISyspromptPrefix(): string {
  return `You are ${PRODUCT_NAME}, Anthropic's official CLI for Claude.`
}
```

*   包含核心系统指令、环境信息和安全指导原则 prompts.ts:143-145

```
export async function getAgentPrompt(): Promise<string[]> {
  return [
    `You are an agent for ${PRODUCT_NAME}, Anthropic's official CLI for Claude. Given the user's prompt, you should use the tools available to you to answer the user's question.
```

*   采用数组结构便于后续的模块化组装和扩展

### 具体作用

*   建立 AI 助手的基本身份和行为准则
*   定义代码工程师的专业角色设定
*   提供安全和使用规范的基础框架

第二层：上下文注入层（Context Injection Layer）
-----------------------------------

### 核心功能

这一层负责将动态上下文信息注入到基础提示词中，实现个性化和情境化的 AI 交互。

### 实现机制

*   使用`formatSystemPromptWithContext()`函数进行上下文注入 claude.ts:426

```
export function formatSystemPromptWithContext(
  systemPrompt: string[],
  context: { [k: string]: string },
): string[] {
  if (Object.entries(context).length === 0) {
    return systemPrompt
  }

  return [
    ...systemPrompt,
    `\nAs you answer the user's questions, you can use the following context:\n`,
    ...Object.entries(context).map(
      ([key, value]) => `<context name="${key}">${value}</context>`,
    ),
  ]
}
```

*   采用 XML 标签结构来组织不同的上下文段落：`<context >` ctx_viz.ts:158-209

```
const command: Command = {
  name: 'ctx-viz',
  description:
    '[ANT-ONLY] Show token usage breakdown for the current conversation context',
  isEnabled: process.env.USER_TYPE === 'ant',
  isHidden: false,
  type: 'local',

  userFacingName() {
    return this.name
  },

  async call(_args: string, cmdContext: { options: { tools: Tool[] } }) {
    // Get tools and system prompt with injected context
    const [systemPromptRaw, sysContext] = await Promise.all([
      getSystemPrompt(),
      getContext(),
    ])

    const rawTools = cmdContext.options.tools

    // Full system prompt with context sections injected
    let systemPrompt = systemPromptRaw.join('\n')
    for (const [name, content] of Object.entries(sysContext)) {
      systemPrompt += `\n<context name="${name}">${content}</context>`
    }

    // Get full tool definitions including prompts and schemas
    const tools = rawTools.map(t => {
      // Get full prompt and schema
      const fullPrompt = t.prompt({ dangerouslySkipPermissions: false })
      const schema = JSON.stringify(
        'inputJSONSchema' in t && t.inputJSONSchema
          ? t.inputJSONSchema
          : zodToJsonSchema(t.inputSchema),
      )

      return {
        name: t.name,
        description: `${fullPrompt}\n\nSchema:\n${schema}`,
      }
    })

    // Get current messages from REPL
    const messages = getMessagesGetter()()

    const sections = getContextSections(systemPrompt)
    return createSummaryTable(systemPrompt, sections, tools, messages)
  },
}

export default command
```

*   支持命名上下文段落，包括代码风格、项目信息等专门化内容

### 具体作用

*   **核心系统提示词**：第一个`<context>`标签之前的内容作为核心指令
*   **命名上下文段落**：通过`name`属性区分不同类型的上下文信息
*   **特殊处理**：将 "codeStyle" 重命名为 "CodeStyle + CLAUDE.md's" 以提供更明确的语义 ctx_viz.ts:22-75

```
function getContextSections(text: string): Section[] {
  const sections: Section[] = []

  // Find first <context> tag
  const firstContextIndex = text.indexOf('<context')

  // Everything before first tag is Core Sysprompt
  if (firstContextIndex > 0) {
    const coreSysprompt = text.slice(0, firstContextIndex).trim()
    if (coreSysprompt) {
      sections.push({
        title: 'Core Sysprompt',
        content: coreSysprompt,
      })
    }
  }

  let currentPos = firstContextIndex
  let nonContextContent = ''

  const regex = /<context\s+name="([^"]*)">([\s\S]*?)<\/context>/g
  let match: RegExpExecArray | null

  while ((match = regex.exec(text)) !== null) {
    // Collect text between context tags
    if (match.index > currentPos) {
      nonContextContent += text.slice(currentPos, match.index)
    }

    const [, name = 'Unnamed Section', content = ''] = match
    sections.push({
      title: name === 'codeStyle' ? "CodeStyle + CLAUDE.md's" : name,
      content: content.trim(),
    })

    currentPos = match.index + match[0].length
  }

  // Collect remaining text after last tag
  if (currentPos < text.length) {
    nonContextContent += text.slice(currentPos)
  }

  // Add non-contextualized content if present
  const trimmedNonContext = nonContextContent.trim()
  if (trimmedNonContext) {
    sections.push({
      title: 'Non-contextualized Content',
      content: trimmedNonContext,
    })
  }

  return sections
}
```

第三层：消息处理层（Message Processing Layer）
-----------------------------------

### 核心功能

这一层管理整个对话流程，包括消息标准化、状态管理和对话历史维护。

### 实现机制

*   `query()`函数作为核心对话处理器 query.ts:13
*   使用`normalizeMessagesForAPI()`将消息格式化为 API 兼容格式 query.ts:125-147
*   集成消息 getter 和 setter 进行状态管理 query.ts:235

这一段比较散，但是看这个 getAssistantResponse() 即可明白这就是个简单的 fetch

```
const fullSystemPrompt = formatSystemPromptWithContext(systemPrompt, context)

  function getAssistantResponse() {
    return querySonnet(
      normalizeMessagesForAPI(messages),
      fullSystemPrompt,
      toolUseContext.options.maxThinkingTokens,
      toolUseContext.options.tools,
      toolUseContext.abortController.signal,
      {
        dangerouslySkipPermissions:
          toolUseContext.options.dangerouslySkipPermissions ?? false,
        model: toolUseContext.options.slowAndCapableModel,
        prependCLISysprompt: true,
      },
    )
  }
```

### 具体作用

*   **对话状态管理**：维护当前对话的消息历史和上下文状态
*   **消息标准化**：确保所有消息都符合 Claude API 的格式要求
*   **会话控制**：支持对话压缩、恢复等高级功能

由于对话压缩其实是个比较核心的功能，我提供一个新版中的 prompt：

```
`Your task is to create a detailed summary of the conversation so far, 
paying close attention to the user's explicit requests and your previous actions.
This summary should be thorough in capturing technical details, code patterns, 
and architectural decisions that would be essential for continuing development 
work without losing context.

Before providing your final summary, wrap your analysis in <analysis> tags to 
organize your thoughts and ensure you've covered all necessary points.

Your summary should include the following sections:

1. Primary Request and Intent: Capture all of the user's explicit requests and intents in detail
2. Key Technical Concepts: List all important technical concepts, technologies, and frameworks discussed.
3. Files and Code Sections: Enumerate specific files and code sections examined, modified, or created. Pay special attention to the most recent messages and include full code snippets where applicable.
4. Errors and fixes: List all errors that you ran into, and how you fixed them. Pay special attention to specific user feedback.
5. Problem Solving: Document problems solved and any ongoing troubleshooting efforts.
6. All user messages: List ALL user messages that are not tool results. These are critical for understanding the users' feedback and changing intent.
7. Pending Tasks: Outline any pending tasks that you have explicitly been asked to work on.
8. Current Work: Describe in detail precisely what was being worked on immediately before this summary request.
9. Optional Next Step: List the next step that you will take that is related to the most recent work you were doing.`
```

第四层：工具集成层（Tool Integration Layer）
---------------------------------

### 核心功能

这一层为 AI 助手提供各种专业工具的访问能力，大大扩展了系统的功能边界。

### 实现机制

*   每个工具都有专门的提示词模板，如 BashTool prompt.ts:3-38

```
`Executes a given bash command in a persistent shell session with optional timeout, ensuring proper handling and security measures.

Before executing the command, please follow these steps:

1. Directory Verification:
   - If the command will create new directories or files, first use the LS tool to verify the parent directory exists and is the correct location
   - For example, before running "mkdir foo/bar", first use LS to check that "foo" exists and is the intended parent directory

2. Security Check:
   - For security and to limit the threat of a prompt injection attack, some commands are limited or banned. If you use a disallowed command, you will receive an error message explaining the restriction. Explain the error to the User.
   - Verify that the command is not one of the banned commands: ${BANNED_COMMANDS.join(', ')}.

3. Command Execution:
   - After ensuring proper quoting, execute the command.
   - Capture the output of the command.

4. Output Processing:
   - If the output exceeds ${MAX_OUTPUT_LENGTH} characters, output will be truncated before being returned to you.
   - Prepare the output for display to the user.

5. Return Result:
   - Provide the processed output of the command.
   - If any errors occurred during execution, include those in the output.

Usage notes:
  - The command argument is required.
  - You can specify an optional timeout in milliseconds (up to 600000ms / 10 minutes). If not specified, commands will timeout after 30 minutes.
  - VERY IMPORTANT: You MUST avoid using search commands like \`find\` and \`grep\`. Instead use ${GREP_TOOL_NAME}, ${GLOB_TOOL_NAME}, or ${AGENT_TOOL_NAME} to search. You MUST avoid read tools like \`cat\`, \`head\`, \`tail\`, and \`ls\`, and use ${FileReadTool.name} and ${LSTool.name} to read files.
  - When issuing multiple commands, use the ';' or '&&' operator to separate them. DO NOT use newlines (newlines are ok in quoted strings).
  - IMPORTANT: All commands share the same shell session. Shell state (environment variables, virtual environments, current directory, etc.) persist between commands. For example, if you set an environment variable as part of a command, the environment variable will persist for subsequent commands.
  - Try to maintain your current working directory throughout the session by using absolute paths and avoiding usage of \`cd\`. You may use \`cd\` if the User explicitly requests it.
  <good-example>
  pytest /foo/bar/tests
  </good-example>
  <bad-example>
  cd /foo/bar && pytest tests
  </bad-example>
```

*   使用`zodToJsonSchema()`将工具定义转换为 JSON Schema 格式 ctx_viz.ts:88-156

```
// Get full tool definitions including prompts and schemas
    const tools = rawTools.map(t => {
      // Get full prompt and schema
      const fullPrompt = t.prompt({ dangerouslySkipPermissions: false })
      const schema = JSON.stringify(
        'inputJSONSchema' in t && t.inputJSONSchema
          ? t.inputJSONSchema
          : zodToJsonSchema(t.inputSchema),
      )

      return {
        name: t.name,
        description: `${fullPrompt}\n\nSchema:\n${schema}`,
      }
    })
```

*   支持多种工具类型：文件操作、代码分析、终端命令等

### 具体作用

*   **工具定义**：为每个工具提供标准化的 JSON Schema 描述
*   **工具提示词**：每个工具都有专门的使用指导和安全规范
*   **动态集成**：根据需要动态加载和集成不同的工具能力

第五层：API 交互层（API Interaction Layer）
----------------------------------

### 核心功能

这一层负责与 Claude API 的实际通信，包括请求发送、响应处理和错误管理。这一层本身只是通信功能所以其实不涉及 prompt 工程，没啥参考价值只是为了文章完整才留着这一部分，具体实现可以查看之前的**消息处理层**。

### 实现机制

*   `querySonnet()`函数处理与 Claude API 的直接交互
*   支持不同模型选择，包括`slowAndCapableModel`参数用于增强处理
*   实现完整的错误处理机制，检测`API_ERROR_MESSAGE_PREFIX`

### 具体作用

*   **API 通信**：处理 HTTP 请求 / 响应和网络异常
*   **模型选择**：根据任务复杂度选择合适的 Claude 模型
*   **Token 管理**：监控和报告 token 使用情况和成本

关键特性与优势
-------

### 1. 上下文可视化

系统提供`ctx-viz`命令来分析 token 使用分布 ctx_viz.ts:20 ，帮助开发者优化提示词效率：

*   **Token 计算**：使用 4 字节 / token 的估算规则
*   **使用分析**：展示各部分的 token 占比和字节大小
*   **优化指导**：识别 token 使用的热点区域

```
function formatTokenCount(bytes: number): string {
  const tokens = bytes / BYTES_PER_TOKEN
  const k = tokens / 1000
  return `${Math.round(k * 10) / 10}k`
}

function formatByteCount(bytes: number): string {
  const kb = bytes / 1024
  return `${Math.round(kb * 10) / 10}kb`
}
function createSummaryTable(
  systemText: string,
  systemSections: Section[],
  tools: ToolSummary[],
  messages: unknown,
): string {
  const table = new Table({
    head: ['Component', 'Tokens', 'Size', '% Used'],
    style: { head: ['bold'] },
    chars: {
      mid: '─',
      'left-mid': '├',
      'mid-mid': '┼',
      'right-mid': '┤',
    },
  })

  const messagesStr = JSON.stringify(messages)
  const toolsStr = JSON.stringify(tools)

  // Calculate total for percentages
  const total = systemText.length + toolsStr.length + messagesStr.length
  const getPercentage = (n: number) => `${Math.round((n / total) * 100)}%`

  // System prompt and its sections
  table.push([
    'System prompt',
    formatTokenCount(systemText.length),
    formatByteCount(systemText.length),
    getPercentage(systemText.length),
  ])
  for (const section of systemSections) {
    table.push([
      `  ${section.title}`,
      formatTokenCount(section.content.length),
      formatByteCount(section.content.length),
      getPercentage(section.content.length),
    ])
  }

  // Tools
  table.push([
    'Tool definitions',
    formatTokenCount(toolsStr.length),
    formatByteCount(toolsStr.length),
    getPercentage(toolsStr.length),
  ])
  for (const tool of tools) {
    table.push([
      `  ${tool.name}`,
      formatTokenCount(tool.description.length),
      formatByteCount(tool.description.length),
      getPercentage(tool.description.length),
    ])
  }

  // Messages and total
  table.push(
    [
      'Messages',
      formatTokenCount(messagesStr.length),
      formatByteCount(messagesStr.length),
      getPercentage(messagesStr.length),
    ],
    ['Total', formatTokenCount(total), formatByteCount(total), '100%'],
  )

  return table.toString()
}
```

### 2. 对话管理

提供完整的对话生命周期管理 compact.ts:12-94 ：

*   **对话压缩**：通过 AI 生成摘要来减少 token 使用
*   **会话恢复**：支持从历史记录中恢复之前的对话状态
*   **状态清理**：自动清理缓存和临时数据

```
const compact = {
  type: 'local',
  name: 'compact',
  description: 'Clear conversation history but keep a summary in context',
  isEnabled: true,
  isHidden: false,
  async call(
    _,
    {
      options: { tools, slowAndCapableModel },
      abortController,
      setForkConvoWithMessagesOnTheNextRender,
    },
  ) {
    // Get existing messages before clearing
    const messages = getMessagesGetter()()

    // Add summary request as a new message
    const summaryRequest = createUserMessage(
      "Provide a detailed but concise summary of our conversation above. Focus on information that would be helpful for continuing the conversation, including what we did, what we're doing, which files we're working on, and what we're going to do next.",
    )

    const summaryResponse = await querySonnet(
      normalizeMessagesForAPI([...messages, summaryRequest]),
      ['You are a helpful AI assistant tasked with summarizing conversations.'],
      0,
      tools,
      abortController.signal,
      {
        dangerouslySkipPermissions: false,
        model: slowAndCapableModel,
        prependCLISysprompt: true,
      },
    )

    // Extract summary from response, throw if we can't get it
    const content = summaryResponse.message.content
    const summary =
      typeof content === 'string'
        ? content
        : content.length > 0 && content[0]?.type === 'text'
          ? content[0].text
          : null

    if (!summary) {
      throw new Error(
        `Failed to generate conversation summary - response did not contain valid text content - ${summaryResponse}`,
      )
    } else if (summary.startsWith(API_ERROR_MESSAGE_PREFIX)) {
      throw new Error(summary)
    }

    // Substitute low token usage info so that the context-size UI warning goes
    // away. The actual numbers don't matter too much: `countTokens` checks the
    // most recent assistant message for usage numbers, so this estimate will
    // be overridden quickly.
    summaryResponse.message.usage = {
      input_tokens: 0,
      output_tokens: summaryResponse.message.usage.output_tokens,
      cache_creation_input_tokens: 0,
      cache_read_input_tokens: 0,
    }

    // Clear screen and messages
    await clearTerminal()
    getMessagesSetter()([])
    setForkConvoWithMessagesOnTheNextRender([
      createUserMessage(
        `Use the /compact command to clear the conversation history, and start a new conversation with the summary in context.`,
      ),
      summaryResponse,
    ])
    getContext.cache.clear?.()
    getCodeStyle.cache.clear?.()

    return '' // not used, just for typesafety. TODO: avoid this hack
  },
  userFacingName() {
    return 'compact'
  },
} satisfies Command

export default compact
```

### 3. 模块化设计

每一层都是独立可替换的模块，支持灵活的扩展和定制：

*   **提示词模板化**：基础模板可以根据需要调整
*   **上下文动态注入**：支持运行时动态添加上下文信息
*   **工具插件化**：新工具可以通过标准接口轻松集成

总结
--

Claude Code 架构的设计充分体现了现代 AI 应用的最佳实践：分层解耦、模块化设计、可观测性和可扩展性。通过将提示词工程分解为多个清晰的层次，系统不仅更容易维护和调试，也为后续的功能扩展提供了坚实的基础。特别值得注意的是，系统对 token 使用的细致管理和上下文的结构化组织，这些都是大规模 AI 应用中的关键考虑因素。

### 核心理念

*   分层安全设计：构建多层次防护体系，通过分层提示词实现纵深防御
*   智能编排：智能解析用户请求，基于 MCP 精准调度工具调用
*   上下文压缩：设定合理阈值，采用专用模型多段式压缩，确保关键信息无损浓缩

### 附件

Github 上的源文件库已经因为触发 DCMA 已经被 404 了，还有一些零零碎碎的 fork 但是比较难找，而且说不定什么时候就消失了，所以我这里只能先放一个 115 的源码分享（即使是国内网盘可能也不一定什么时候就没了，什么时候要是无效了可以评论区看看能不能更新这个分享链接吧）。

115：[文件分享](https://115cdn.com/s/swwiqyz3w1k#Claude-Code-main.zip)

访问码：kec8

 ![](https://picx.zhimg.com/5c719987e8c3c69970413112073f782f_l.jpg?source=1def8aca) 海一帆​​

抓紧时间，只要你有 github 账号，就可以白嫖 100 刀额度的 Claude API 用于 [Claude Code](https://zhida.zhihu.com/search?content_id=735841055&content_type=Answer&match_order=1&q=Claude+Code&zhida_source=entity) 了。具体步骤如下：

一、使用 github 账号注册
----------------

打开链接：[https://anyrouter.top/register?aff=EsAE](https://anyrouter.top/register?aff=EsAE)，注意一定要用我这个注册码，这样子注册成功后你我都能各获得 50 刀的额度，如果不用的话直接注册就只有 50 刀了。注册成功后，点击左边菜单栏的 “钱包” 就可以查看余额：

![](https://pic1.zhimg.com/v2-1e6b1536ef2d43156089c8ec7d203175_r.jpg?source=1def8aca)

二、创建 [API-Key](https://zhida.zhihu.com/search?content_id=735841055&content_type=Answer&match_order=1&q=API-Key&zhida_source=entity)
-----------------------------------------------------------------------------------------------------------------------------------

下一步就是创建 API-Key，点击左侧菜单的 “API 令牌”：

![](https://pica.zhimg.com/v2-effe184f7cf07c2289a5dcbe76dbe201_r.jpg?source=1def8aca)

然后点击 “添加令牌”：

![](https://picx.zhimg.com/v2-30373248924009feb3f1ab3083a88725_r.jpg?source=1def8aca)

提交之后，API-Key 就创建成功了，点击刚创建的令牌右边的‘复制’，即可复制令牌，留待后面使用：

![](https://picx.zhimg.com/v2-bf6f464c2e4d956cff1a899fc2d2d298_r.jpg?source=1def8aca)

三、安装 Claude Code（已安装可跳过）
------------------------

### 1️⃣ 安装 [Node.js](https://zhida.zhihu.com/search?content_id=735841055&content_type=Answer&match_order=1&q=Node.js&zhida_source=entity)（已安装可跳过）

确保 Node.js 版本 ≥ 18.0

```
# Ubuntu / Debian 用户
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo bash -
sudo apt-get install -y nodejs
node --version

# macOS 用户
sudo xcode-select --install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install node
node --version
```

###   
2️⃣ 安装 Claude Code

```
npm install -g @anthropic-ai/claude-code
claude --version
```

四、开始使用
------

### 1️⃣ 设置 API Key 和 API URL

*   **获取 Auth Token：** `ANTHROPIC_AUTH_TOKEN` ：即前面创建并复制的令牌
*   **API 地址：** `ANTHROPIC_BASE_URL`：`https://anyrouter.top` 是该 API 网站的 API 服务地址

在您的项目目录下运行：

```
cd your-project-folder
export ANTHROPIC_AUTH_TOKEN=sk-... 
export ANTHROPIC_BASE_URL=https://anyrouter.top
claude
```

运行后

*   选择你喜欢的主题 + Enter
*   确认安全须知 + Enter
*   使用默认 Terminal 配置 + Enter
*   信任工作目录 + Enter

开始在终端里和你的 AI 编程搭档一起写代码吧！

### 2️⃣ 配置环境变量（推荐）

为避免每次重复输入，可将环境变量写入 bash_profile 和 bashrc：

```
echo -e '\n export ANTHROPIC_AUTH_TOKEN=sk-...' >> ~/.bash_profile
echo -e '\n export ANTHROPIC_BASE_URL=https://anyrouter.top' >> ~/.bash_profile
echo -e '\n export ANTHROPIC_AUTH_TOKEN=sk-...' >> ~/.bashrc
echo -e '\n export ANTHROPIC_BASE_URL=https://anyrouter.top' >> ~/.bashrc
```

重启终端后，直接使用：

```
cd your-project-folder
claude
```

即可使用 Claude Code。![](https://pica.zhimg.com/v2-1eaa11ca159389279aae715d32676b80_l.jpg?source=1def8aca)依然易冷​​目录收起前言Agent ToolFile Processing ToolsFile Read ToolFile Edit Tool (Create, Update, Delete)File Replace ToolJupyter Notebook ToolsJupyter Notebook Read ToolJupyter Notebook Edit ToolSearch ToolsList Files ToolSearch Glob ToolGrep ToolPR ToolsPR Comments ToolPR Review ToolHistory ToolsGit History ToolClear Conversation ToolCompact Conversation ToolInit Codebase ToolOther ToolsThinking ToolArchitect ToolBash Tool总结附录 - Claude Code 更多使用技巧

**前言**
------

之前我们在第一篇中 **[Claude Code Agent 模式深度解读（一）！Anthropic 提出的下一代 Code CLI 工具](https://zhuanlan.zhihu.com/p/1909733074402509664)**介绍了 Claude Code Agent 模式的 System Prompt 方案，**了解到其 Agent 的实现是基于 Function Call 的**，那么 Claude Code 用到的工具（官网有贴出列表，见下图）有哪些作用呢，以及每个工具的详细内容描述是什么呢，看本篇的详细解读即可。

![](https://picx.zhimg.com/v2-dee35ac6ca9e5e1a39900d3363d63fc4_r.jpg?source=1def8aca)

**Agent Tool**
--------------

这个 Tool 非常特殊，是作为一个子 Agent，用来给主 Agent 启动的，因为在第一篇解读里面说到过，Claude Code 是个 multi-agent 的架构。

在下面的 Tool Descrption 中，其实主要说了两个方面的事情：

*   何时能用 Agent Tool：比如查找关键字、搜索文件等。如果用了，要注意同时启动多个 Agent（其实就是 tool），tool 返回的结果用户是不可见的。  
    
*   何时不适合用 Agent Tool：比如查看特定的文件，查找某个类的定义等，可以理解成只使用一个 tool 即可完成的任务。  
    

```
Launch a new agent that has access to the following tools: ${(await bv1(I)).map((W) => W.name).join(", ")}. When you are searching for a keyword or file and are not confident that you will find the right match in the first few tries, use the Agent tool to perform the search for you.
  
  When to use the Agent tool:
  - If you are searching for a keyword like "config" or "logger", or for questions like "which file does X?", the Agent tool is strongly recommended
  
  When NOT to use the Agent tool:
  - If you want to read a specific file path, use the ${uw.name} or ${rw.name} tool instead of the Agent tool, to find the match more quickly
  - If you are searching for a specific class definition like "class Foo", use the ${rw.name} tool instead, to find the match more quickly
  - If you are searching for code within a specific file or set of 2-3 files, use the ${uw.name} tool instead of the Agent tool, to find the match more quickly
  
  Usage notes:
  1. Launch multiple agents concurrently whenever possible, to maximize performance; to do that, use a single message with multiple tool uses
  2. When the agent is done, it will return a single message back to you. The result returned by the agent is not visible to the user. To show the user the result, you should send a text message back to the user with a concise summary of the result.
  3. Each agent invocation is stateless. You will not be able to send additional messages to the agent, nor will the agent be able to communicate with you outside of its final report. Therefore, your prompt should contain a highly detailed task description for the agent to perform autonomously and you should specify exactly what information the agent should return back to you in its final and only message to you.
  4. The agent's outputs should generally be trusted${
      I === "bypassPermissions"
        ? ""
        : `
  5. IMPORTANT: The agent can not use ${c9.name}, ${wI.name}, ${VI.name}, ${bW.name}, so can not modify files. If you want to use these tools, use them directly instead of going through the agent.
```

**File Processing Tools**
-------------------------

### **File Read Tool**

*   从本地文件系统读取文件。  
    
*   file_path 参数必须是绝对路径，而非相对路径。  
    
*   默认情况下，它会从文件开头开始读取最多 2000 行。  
    
*   您可以选择指定行偏移量和行数限制（这对于长文件尤其有用），但建议您不要指定这些参数，而是读取整个文件。任何超过 2000 个字符的行都将被截断。  
    
*   对于图像文件，该工具将为您显示图像。对于 [Jupyter Notebook](https://zhida.zhihu.com/search?content_id=729111161&content_type=Answer&match_order=1&q=Jupyter+Notebook&zhida_source=entity)（.ipynb 文件），请使用 VH.name。  
    

```
{
  "name": "View",
  "description": "Reads a file from the local filesystem. The file_path parameter must be an absolute path, not a relative path. By default, it reads up to 2000 lines starting from the beginning of the file. You can optionally specify a line offset and limit (especially handy for long files), but it's recommended to read the whole file by not providing these parameters. Any lines longer than 2000 characters will be truncated. For image files, the tool will display the image for you. For Jupyter notebooks (.ipynb files), use the VH.name instead.",
  "inputSchema": {
    "type": "strictObject",
    "properties": {
      "file_path": {
        "type": "string",
        "description": "The absolute path to the file to read"
      },
      "offset": {
        "type": "number",
        "optional": true,
        "description": "The line number to start reading from. Only provide if the file is too large to read at once"
      },
      "limit": {
        "type": "number",
        "optional": true,
        "description": "The number of lines to read. Only provide if the file is too large to read at once."
      }
    }
  },
}
```

### **File Edit Tool (Create, Update, Delete)**

这是一个用于编辑文件的工具。

要移动或重命名文件，通常应该使用 [Bash 工具](https://zhida.zhihu.com/search?content_id=729111161&content_type=Answer&match_order=1&q=Bash+工具&zhida_source=entity)中的 “mv” 命令。对于较大的编辑，请使用 “写入” 工具覆盖文件。

```
{
  "name": "Edit",
  "description": "This is a tool for editing files. For moving or renaming files, you should generally use the Bash tool with the 'mv' command instead. For larger edits, use the Write tool to overwrite files. For Jupyter notebooks (.ipynb files), use the ${RI.name} instead.
Before using this tool:
1. Use the View tool to understand the file's contents and context
2. Verify the directory path is correct (only applicable when creating new files):
   - Use the LS tool to verify the parent directory exists and is the correct location
To make a file edit, provide the following:
1. file_path: The absolute path to the file to modify (must be absolute, not relative)
2. old_string: The text to replace (must be unique within the file, and must match the file contents exactly, including all whitespace and indentation)
3. new_string: The edited text to replace the old_string
The tool will replace ONE occurrence of old_string with new_string in the specified file.
CRITICAL REQUIREMENTS FOR USING THIS TOOL:
1. UNIQUENESS: The old_string MUST uniquely identify the specific instance you want to change. This means:
   - Include AT LEAST 3-5 lines of context BEFORE the change point
   - Include AT LEAST 3-5 lines of context AFTER the change point
   - Include all whitespace, indentation, and surrounding code exactly as it appears in the file
2. SINGLE INSTANCE: This tool can only change ONE instance at a time. If you need to change multiple instances:
   - Make separate calls to this tool for each instance
   - Each call must uniquely identify its specific instance using extensive context
3. VERIFICATION: Before using this tool:
   - Check how many instances of the target text exist in the file
   - If multiple instances exist, gather enough context to uniquely identify each one
   - Plan separate tool calls for each instance
WARNING: If you do not follow these requirements:
   - The tool will fail if old_string matches multiple locations
   - The tool will fail if old_string doesn't match exactly (including whitespace)
   - You may change the wrong instance if you don't include enough context
When making edits:
   - Ensure the edit results in idiomatic, correct code
   - Do not leave the code in a broken state
   - Always use absolute file paths (starting with /)
If you want to create a new file, use:
   - A new file path, including dir name if needed
   - An empty old_string
   - The new file's contents as new_string
Remember: when making multiple file edits in a row to the same file, you should prefer to send all edits in a single message with multiple calls to this tool, rather than multiple messages with a single call each.",
  "inputSchema": {
    "type": "strictObject",
    "properties": {
      "file_path": {
        "type": "string",
        "description": "The absolute path to the file to modify"
      },
      "old_string": {
        "type": "string",
        "description": "The text to replace"
      },
      "new_string": {
        "type": "string",
        "description": "The text to replace it with"
      }
    }
  }
}
```

### **File Replace Tool**

将文件写入本地文件系统。如果存在现有文件，则覆盖现有文件。

使用此工具前：

*   使用 ReadFile 工具了解文件的内容和上下文  
    
*   目录验证（仅适用于创建新文件时）：使用 LS 工具验证父目录是否存在且位置正确  
    

```
{
  "name": "Replace",
  "description": "Write a file to the local filesystem. Overwrites the existing file if there is one.
Before using this tool:
1. Use the ReadFile tool to understand the file's contents and context
2. Directory Verification (only applicable when creating new files):
   - Use the LS tool to verify the parent directory exists and is the correct location",
  "inputSchema": {
    "type": "strictObject",
    "properties": {
      "file_path": {
        "type": "string",
        "description": "The absolute path to the file to write (must be absolute, not relative)"
      },
      "content": {
        "type": "string",
        "description": "The content to write to the file"
      }
    }
  }
}
```

**Jupyter Notebook Tools**
--------------------------

### **Jupyter Notebook Read Tool**

从 Jupyter Notebook 的所有代码单元中提取并读取源代码。

读取 Jupyter Notebook（.ipynb 文件）并返回所有单元及其输出。Jupyter Notebook 是结合了代码、文本和可视化效果的交互式文档，常用于数据分析和科学计算。notebook_path 参数必须是绝对路径，而不是相对路径。

```
{
  "description": "Extract and read source code from all code cells in a Jupyter notebook. Reads a Jupyter notebook (.ipynb file) and returns all of the cells with their outputs. Jupyter notebooks are interactive documents that combine code, text, and visualizations, commonly used for data analysis and scientific computing. The notebook_path parameter must be an absolute path, not a relative path.",
  "inputSchema": {
    "type": "strictObject",
    "properties": {
      "notebook_path": {
        "type": "string",
        "description": "The absolute path to the Jupyter notebook file to read (must be absolute, not relative)"
      }
    }
  }
}
```

### **Jupyter Notebook Edit Tool**

替换 Jupyter 笔记本中特定单元格的内容。

将 Jupyter 笔记本（.ipynb 文件）中特定单元格的内容完全替换为新源。Jupyter 笔记本是结合了代码、文本和可视化效果的交互式文档，常用于数据分析和科学计算。notebook_path 参数必须是绝对路径，不能是相对路径。cell_number 的索引从 0 开始。使用 edit_mode=insert 可在 cell_number 指定的索引处添加新单元格。使用 edit_mode=delete 可在 cell_number 指定的索引处删除单元格。

```
{
  "name": "NotebookEditCell",
  "description": "Replace the contents of a specific cell in a Jupyter notebook. Completely replaces the contents of a specific cell in a Jupyter notebook (.ipynb file) with new source. Jupyter notebooks are interactive documents that combine code, text, and visualizations, commonly used for data analysis and scientific computing. The notebook_path parameter must be an absolute path, not a relative path. The cell_number is 0-indexed. Use edit_mode=insert to add a new cell at the index specified by cell_number. Use edit_mode=delete to delete the cell at the index specified by cell_number.",
  "inputSchema": {
    "type": "strictObject",
    "properties": {
      "notebook_path": {
        "type": "string",
        "description": "The absolute path to the Jupyter notebook file to edit (must be absolute, not relative)"
      },
      "cell_number": {
        "type": "number",
        "description": "The index of the cell to edit (0-based)"
      },
      "new_source": {
        "type": "string",
        "description": "The new source for the cell"
      },
      "cell_type": {
        "type": "enum",
        "values": ["code", "markdown"],
        "optional": true,
        "description": "The type of the cell (code or markdown). If not specified, it defaults to the current cell type. If using edit_mode=insert, this is required."
      },
      "edit_mode": {
        "type": "string",
        "optional": true,
        "description": "The type of edit to make (replace, insert, delete). Defaults to replace."
      }
    }
  }
}
```

**Search Tools**
----------------

### **List Files Tool**

列出给定路径下的文件和目录。

路径参数必须是绝对路径，不能是相对路径。如果您知道要搜索哪些目录，通常应该优先使用 Glob 和 Grep 工具。

```
{
  "name": "LS",
  "description": "Lists files and directories in a given path. The path parameter must be an absolute path, not a relative path. You should generally prefer the Glob and Grep tools, if you know which directories to search.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "path": {
        "type": "string",
        "description": "The absolute path to the directory to list (must be absolute, not relative)"
      }
    },
    "required": ["path"],
    "additionalProperties": false
  },
}
```

### **Search Glob Tool**

*   快速文件模式匹配工具，适用于任何大小的代码库  
    
*   支持 “**/*.js” 或“src/**/*.ts”等全局模式  
    
*   返回按修改时间排序的匹配文件路径  
    
*   当您需要按名称模式查找文件时，请使用此工具  
    
*   当您进行开放式搜索，可能需要多轮全局搜索和 grepping 操作时，请使用 Agent 工具  
    

```
{
  "name": "GlobTool",
  "description": "- Fast file pattern matching tool that works with any codebase size
- Supports glob patterns like "**/*.js" or "src/**/*.ts"
- Returns matching file paths sorted by modification time
- Use this tool when you need to find files by name patterns
- When you are doing an open ended search that may require multiple rounds of globbing and grepping, use the Agent tool instead",
  "inputSchema": {
    "type": "strictObject",
    "properties": {
      "pattern": {
        "type": "string",
        "description": "The glob pattern to match files against"
      },
      "path": {
        "type": "string",
        "optional": true,
        "description": "The directory to search in. Defaults to the current working directory."
      }
    }
  },
}
```

### **Grep Tool**

*   快速内容搜索工具，适用于任何代码库大小  
    
*   使用正则表达式搜索文件内容  
    
*   支持完整的正则表达式语法（例如 “log.*Error”、“function\s+\w+” 等）  
    
*   使用 include 参数按模式过滤文件（例如 “*.js”、“*.{ts,tsx}”）  
    
*   返回按修改时间排序的匹配文件路径  
    
*   当您需要查找包含特定模式的文件时，请使用此工具  
    
*   当您进行开放式搜索，可能需要多次遍历和查找时，请使用 Agent 工具  
    

```
{
  "name": "GrepTool",
  "description": "- Fast content search tool that works with any codebase size
- Searches file contents using regular expressions
- Supports full regex syntax (eg. "log.*Error", "function\\s+\\w+", etc.)
- Filter files by pattern with the include parameter (eg. "*.js", "*.{ts,tsx}")
- Returns matching file paths sorted by modification time
- Use this tool when you need to find files containing specific patterns
- When you are doing an open ended search that may require multiple rounds of globbing and grepping, use the Agent tool instead",
  "inputSchema": {
    "type": "strictObject",
    "properties": {
      "pattern": {
        "type": "string",
        "description": "The regular expression pattern to search for in file contents"
      },
      "path": {
        "type": "string",
        "optional": true,
        "description": "The directory to search in. Defaults to the current working directory."
      },
      "include": {
        "type": "string",
        "optional": true,
        "description": "File pattern to include in the search (e.g. \"*.js\", \"*.{ts,tsx}\")"
      }
    }
  }
}
```

**PR Tools**
------------

### **PR Comments Tool**

从 GitHub 拉取请求获取评论。

```
{
  "name": "pr-comments",
  "description": "Get comments from a GitHub pull request",
  "getPromptForCommand": {
    "role": "user",
    "content": [
      {
        "type": "text",
        "text": "You are an AI assistant integrated into a git-based version control system. Your task is to fetch and display comments from a GitHub pull request.
Follow these steps:
1. Use `gh pr view --json number,headRepository` to get the PR number and repository info
2. Use `gh api /repos/{owner}/{repo}/issues/{number}/comments` to get PR-level comments
3. Use `gh api /repos/{owner}/{repo}/pulls/{number}/comments` to get review comments. Pay particular attention to the following fields: `body`, `diff_hunk`, `path`, `line`, etc. If the comment references some code, consider fetching it using eg `gh api /repos/{owner}/{repo}/contents/{path}?ref={branch} | jq .content -r | base64 -d`
4. Parse and format all comments in a readable way
5. Return ONLY the formatted comments, with no additional text
Format the comments as:
## Comments
[For each comment thread:]
- @author file.ts#line:
  ```diff
  [diff_hunk from the API response]
  ```
  > quoted comment text
  
  [any replies indented]
If there are no comments, return "No comments found."
Remember:
1. Only show the actual comments, no explanatory text
2. Include both PR-level and code review comments
3. Preserve the threading/nesting of comment replies
4. Show the file and line number context for code review comments
5. Use jq to parse the JSON responses from the GitHub API
Additional user input:"
      }
    ]
  }
}
```

### **PR Review Tool**

审查拉取请求。

```
{
  "name": "review",
  "description": "Review a pull request",
  "getPromptForCommand": {
    "role": "user",
    "content": [
      {
        "type": "text",
        "text": "You are an expert code reviewer. Follow these steps:
1. If no PR number is provided in the args, use BashTool("gh pr list") to show open PRs
2. If a PR number is provided, use BashTool("gh pr view <number>") to get PR details
3. Use BashTool("gh pr diff <number>") to get the diff
4. Analyze the changes and provide a thorough code review that includes:
   - Overview of what the PR does
   - Analysis of code quality and style
   - Specific suggestions for improvements
   - Any potential issues or risks

Keep your review concise but thorough. Focus on:
- Code correctness
- Following project conventions
- Performance implications
- Test coverage
- Security considerations
Format your review with clear sections and bullet points.
PR number:"
      }
    ]
  }
}
```

**History Tools**
-----------------

### **Git History Tool**

检索经常修改的文件的 git 历史记录。

```
{
  "name": "getGitHistory",
  "description": "Retrieves git history of frequently modified files",
  "steps": [
    {
      "step": "check_git_repository",
      "command": "gitRevParse()",
      "failure": "returns empty array"
    },
    {
      "step": "get_user_modified_files",
      "command": "git log -n 1000 --pretty=format: --name-only --diff-filter=M --author=$(git config user.email) | sort | uniq -c | sort -nr | head -n 20",
      "outputProcessing": {
        "minLines": 10,
        "fallback": {
          "step": "get_all_modified_files",
          "command": "git log -n 1000 --pretty=format: --name-only --diff-filter=M | sort | uniq -c | sort -nr | head -n 20"
        }
      }
    },
    {
      "step": "analyze_with_ai",
      "systemPrompt": "You are an expert at analyzing git history. Given a list of files and their modification counts, return exactly five filenames that are frequently modified and represent core application logic (not auto-generated files, dependencies, or configuration). Make sure filenames are diverse, not all in the same folder, and are a mix of user and other users. Return only the filenames' basenames (without the path) separated by newlines with no explanation.",
      "requirements": {
        "outputFormat": "text",
        "minResults": 5
      }
    },
    {
      "step": "error_handling",
      "behavior": "returns empty array on error"
    }
  ],
  "output": {
    "description": "Array of 5 filenames (basenames only)",
    "type": "string[]",
    "emptyCase": "returns empty array"
  }
}
```

### **Clear Conversation Tool**

清除对话历史记录并释放上下文。

```
{
  "name": "clear",
  "description": "Clear conversation history and free up context",
}
```

### **Compact Conversation Tool**

清除对话历史记录，但保留上下文摘要。

```
{
  "name": "compact",
  "description": "Clear conversation history but keep a summary in context",
  "prompt": {
    "role": "summary_assistant",
    "instructions": [
      "You are a helpful AI assistant tasked with summarizing conversations.",
      "Provide a detailed but concise summary of our conversation above. Focus on information that would be helpful for continuing the conversation, including:",
      {
        "items": [
          "What we did",
          "What we're doing",
          "Which files we're working on",
          "What we're going to do next"
        ]
      }
    ]
  }
}
```

### **Init Codebase Tool**

使用代码库文档初始化一个新的 CLAUDE.md 文件。

```
{
  "name": "init",
  "description": "Initialize a new CLAUDE.md file with codebase documentation",
  "getPromptForCommand": {
    "role": "user",
    "content": [
      {
        "type": "text",
        "text": "Please analyze this codebase and create a CLAUDE.md file containing:
1. Build/lint/test commands - especially for running a single test
2. Code style guidelines including imports, formatting, types, naming conventions, error handling, etc.
The file you create will be given to agentic coding agents (such as yourself) that operate in this repository. Make it about 20 lines long.
If there's already a CLAUDE.md, improve it.
If there are Cursor rules (in .cursor/rules/ or .cursorrules) or Copilot rules (in .github/copilot-instructions.md), make sure to include them."
      }
    ]
  }
}
```

**Other Tools**
---------------

### **Thinking Tool**

这是一个用于记录模型思考的无操作工具，它的灵感来源于 tau-bench think 工具。

```
{
  "name": "Think",
  "description": "This is a no-op tool that logs a thought. It is inspired by the tau-bench think tool.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "thought": {
        "type": "string",
        "description": "Your thoughts."
      }
    }
  },
  "prompt": "Use the tool to think about something. It will not obtain new information or make any changes to the repository, but just log the thought. Use it when complex reasoning or brainstorming is needed. 
Common use cases:
1. When exploring a repository and discovering the source of a bug, call this tool to brainstorm several unique ways of fixing the bug, and assess which change(s) are likely to be simplest and most effective
2. After receiving test results, use this tool to brainstorm ways to fix failing tests
3. When planning a complex refactoring, use this tool to outline different approaches and their tradeoffs
4. When designing a new feature, use this tool to think through architecture decisions and implementation details
5. When debugging a complex issue, use this tool to organize your thoughts and hypotheses
The tool simply logs your thought process for better transparency and does not execute any code or make changes.",
}
```

### **Architect Tool**

该 Tool 属于内置 tool，需要通过命令行参数 `enableArchitect` 手动开启。

开启了之后，Claude Code 就会将 Prompt 的内容传给模型，让模型来进行 Planing，并将结果加入到对话历史中去。

```
{
  "name": "Architect",
  "description": "Your go-to tool for any technical or coding task. Analyzes requirements and breaks them down into clear, actionable implementation steps. Use this whenever you need help planning how to implement a feature, solve a technical problem, or structure your code.",
  "inputSchema": {
    "type": "strictObject",
    "properties": {
      "prompt": {
        "type": "string",
        "description": "The technical request or coding task to analyze"
      },
      "context": {
        "type": "string",
        "description": "Optional context from previous conversation or system state",
        "optional": true
      }
    }
  },
  "userFacingName": "Architect",
  "prompt": "You are an expert software architect. Your role is to analyze technical requirements and produce clear, actionable implementation plans.
These plans will then be carried out by a junior software engineer so you need to be specific and detailed. However do not actually write the code, just explain the plan.
Follow these steps for each request:
1. Carefully analyze requirements to identify core functionality and constraints
2. Define clear technical approach with specific technologies and patterns
3. Break down implementation into concrete, actionable steps at the appropriate level of abstraction
Keep responses focused, specific and actionable. 
IMPORTANT: Do not ask the user if you should implement the changes at the end. Just provide the plan as described above.
IMPORTANT: Do not attempt to write the code or use any string modification tools. Just provide the plan."
}
```

### **Bash Tool**

这个 Tool 比较特殊，因为它是用来给 Agent 提供命令行的工具，由于 Claude Code 的设计，Tool 的使用结果用户不可见，所以对于这个 Tool 做了很多安全机制的设计，防止其进行未授权的访问操作。比如说有以下几点：

① 黑名单命令清单：有一个 bannedCommands 列表，明确禁止了危险命令，如 curl、wget 等涉及到网络操作的命令。

```
const bannedCommands = [
  'alias',
  'curl',
  'curlie',
  'wget',
  'axel',
  'aria2c',
  'nc',
  'telnet',
  'lynx',
  'w3m',
  'links',
  'httpie',
  'xh',
  'http-prompt',
  'chrome',
  'firefox',
  'safari'
];
```

② 目录访问权限限制：当访问每个项目时，都需要检查执行权限，不能滥用 cd 命令等。

完整的 Tool Description 如下：

```
const maxCharacters = 30000;
const bannedCommands = [
  'alias',
  'curl',
  'curlie',
  'wget',
  'axel',
  'aria2c',
  'nc',
  'telnet',
  'lynx',
  'w3m',
  'links',
  'httpie',
  'xh',
  'http-prompt',
  'chrome',
  'firefox',
  'safari'
];
Executes a given bash command in a persistent shell session with optional timeout, ensuring proper handling and security measures.
Executes a given bash command in a persistent shell session with optional timeout, ensuring proper handling and security measures.
Before executing the command, please follow these steps:
1. Directory Verification:
   - If the command will create new directories or files, first use the LS tool to verify the parent directory exists and is the correct location
   - For example, before running "mkdir foo/bar", first use LS to check that "foo" exists and is the intended parent directory
2. Security Check:
   - For security and to limit the threat of a prompt injection attack, some commands are limited or banned. If you use a disallowed command, you will receive an error message explaining the restriction. Explain the error to the User.
   - Verify that the command is not one of the banned commands: ${bannedCommands.join(', ')}.
3. Command Execution:
   - After ensuring proper quoting, execute the command.
   - Capture the output of the command.
4. Output Processing:
   - If the output exceeds ${maxCharacters} characters, output will be truncated before being returned to you.
   - Prepare the output for display to the user.
5. Return Result:
   - Provide the processed output of the command.
   - If any errors occurred during execution, include those in the output.
Usage notes:
  - The command argument is required.
  - You can specify an optional timeout in milliseconds (up to 600000ms / 10 minutes). If not specified, commands will timeout after 30 minutes.
  - VERY IMPORTANT: You MUST avoid using search commands like \`find\` and \`grep\`. Instead use GrepTool, SearchGlobTool, or dispatch_agent to search. You MUST avoid read tools like \`cat\`, \`head\`, \`tail\`, and \`ls\`, and use ${FileReadTool.name} and ${
    ListFilesTool.name
  } to read files.
  - When issuing multiple commands, use the ';' or '&&' operator to separate them. DO NOT use newlines (newlines are ok in quoted strings).
  - IMPORTANT: All commands share the same shell session. Shell state (environment variables, virtual environments, current directory, etc.) persist between commands. For example, if you set an environment variable as part of a command, the environment variable will persist for subsequent commands.
  - Try to maintain your current working directory throughout the session by using absolute paths and avoiding usage of \`cd\`. You may use \`cd\` if the User explicitly requests it.
  <good-example>
  pytest /foo/bar/tests
  </good-example>
  <bad-example>
  cd /foo/bar && pytest tests
  </bad-example>
# Committing changes with git
When the user asks you to create a new git commit, follow these steps carefully:
1. Start with a single message that contains exactly three tool_use blocks that do the following (it is VERY IMPORTANT that you send these tool_use blocks in a single message, otherwise it will feel slow to the user!):
   - Run a git status command to see all untracked files.
   - Run a git diff command to see both staged and unstaged changes that will be committed.
   - Run a git log command to see recent commit messages, so that you can follow this repository's commit message style.
2. Use the git context at the start of this conversation to determine which files are relevant to your commit. Add relevant untracked files to the staging area. Do not commit files that were already modified at the start of this conversation, if they are not relevant to your commit.
3. Analyze all staged changes (both previously staged and newly added) and draft a commit message. Wrap your analysis process in <commit_analysis> tags:
<commit_analysis>
- List the files that have been changed or added
- Summarize the nature of the changes (eg. new feature, enhancement to an existing feature, bug fix, refactoring, test, docs, etc.)
- Brainstorm the purpose or motivation behind these changes
- Do not use tools to explore code, beyond what is available in the git context
- Assess the impact of these changes on the overall project
- Check for any sensitive information that shouldn't be committed
- Draft a concise (1-2 sentences) commit message that focuses on the "why" rather than the "what"
- Ensure your language is clear, concise, and to the point
- Ensure the message accurately reflects the changes and their purpose (i.e. "add" means a wholly new feature, "update" means an enhancement to an existing feature, "fix" means a bug fix, etc.)
- Ensure the message is not generic (avoid words like "Update" or "Fix" without context)
- Review the draft message to ensure it accurately reflects the changes and their purpose
</commit_analysis>
4. Create the commit with a message ending with:
\uD83E\uDD16 Generated with ${NAME}
Co-Authored-By: Claude <noreply@anthropic.com>
- In order to ensure good formatting, ALWAYS pass the commit message via a HEREDOC, a la this example:
<example>
git commit -m "$(cat <<'EOF'
   Commit message here.
   \uD83E\uDD16 Generated with ${NAME}
   Co-Authored-By: Claude <noreply@anthropic.com>
   EOF
   )"
</example>
5. If the commit fails due to pre-commit hook changes, retry the commit ONCE to include these automated changes. If it fails again, it usually means a pre-commit hook is preventing the commit. If the commit succeeds but you notice that files were modified by the pre-commit hook, you MUST amend your commit to include them.
6. Finally, run git status to make sure the commit succeeded.
Important notes:
- When possible, combine the "git add" and "git commit" commands into a single "git commit -am" command, to speed things up
- However, be careful not to stage files (e.g. with \`git add .\`) for commits that aren't part of the change, they may have untracked files they want to keep around, but not commit.
- NEVER update the git config
- DO NOT push to the remote repository
- IMPORTANT: Never use git commands with the -i flag (like git rebase -i or git add -i) since they require interactive input which is not supported.
- If there are no changes to commit (i.e., no untracked files and no modifications), do not create an empty commit
- Ensure your commit message is meaningful and concise. It should explain the purpose of the changes, not just describe them.
- Return an empty response - the user will see the git output directly
# Creating pull requests
Use the gh command via the Bash tool for ALL GitHub-related tasks including working with issues, pull requests, checks, and releases. If given a Github URL use the gh command to get the information needed.
IMPORTANT: When the user asks you to create a pull request, follow these steps carefully:
1. Understand the current state of the branch. Remember to send a single message that contains multiple tool_use blocks (it is VERY IMPORTANT that you do this in a single message, otherwise it will feel slow to the user!):
   - Run a git status command to see all untracked files.
   - Run a git diff command to see both staged and unstaged changes that will be committed.
   - Check if the current branch tracks a remote branch and is up to date with the remote, so you know if you need to push to the remote
   - Run a git log command and \`git diff main...HEAD\` to understand the full commit history for the current branch (from the time it diverged from the \`main\` branch.)
2. Create new branch if needed
3. Commit changes if needed
4. Push to remote with -u flag if needed
5. Analyze all changes that will be included in the pull request, making sure to look at all relevant commits (not just the latest commit, but all commits that will be included in the pull request!), and draft a pull request summary. Wrap your analysis process in <pr_analysis> tags:
<pr_analysis>
- List the commits since diverging from the main branch
- Summarize the nature of the changes (eg. new feature, enhancement to an existing feature, bug fix, refactoring, test, docs, etc.)
- Brainstorm the purpose or motivation behind these changes
- Assess the impact of these changes on the overall project
- Do not use tools to explore code, beyond what is available in the git context
- Check for any sensitive information that shouldn't be committed
- Draft a concise (1-2 bullet points) pull request summary that focuses on the "why" rather than the "what"
- Ensure the summary accurately reflects all changes since diverging from the main branch
- Ensure your language is clear, concise, and to the point
- Ensure the summary accurately reflects the changes and their purpose (ie. "add" means a wholly new feature, "update" means an enhancement to an existing feature, "fix" means a bug fix, etc.)
- Ensure the summary is not generic (avoid words like "Update" or "Fix" without context)
- Review the draft summary to ensure it accurately reflects the changes and their purpose
</pr_analysis>
6. Create PR using gh pr create with the format below. Use a HEREDOC to pass the body to ensure correct formatting.
<example>
gh pr create --title "the pr title" --body "$(cat <<'EOF'
## Summary
<1-3 bullet points>
## Test plan
[Checklist of TODOs for testing the pull request...]
\uD83E\uDD16 Generated with ${NAME}
EOF
)"
</example>
Important:
- Return an empty response - the user will see the gh output directly
- Never update git config
```

**总结**
------

通过对 Tools 部分的描述，加上第一篇对 System Prompt 的解读，可以看到 Claude Code 有着鲜明的几点特征：

*   极强的安全和隐私设计：用户的查询会直接发送到 Anthropic 的 API 中，没有经过任何中间服务器。而且在执行终端操作的时候，涉及到需要用户确认才能进行的操作时，都做到了详细的 Prompt 约束和权限控制。  
    
*   命令行参数控制：给用户提供了重要的命令行参数来控制用户的行为，比如启动交互模式、任务规划能力等。  
    
*   multi-agent 的架构足够自适应：复杂任务尽量启动多个子 Agent（tool）解决问题，简单任务尽量以单 Agent 的模式一次性解决，在性能与成本之间取得平衡。  
    

**附录 - Claude Code 更多使用技巧**
---------------------------

这里依然要贴出两个官方的链接：

*   官方博客链接：[https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview)  
    
*   官方 Github 链接：[https://github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)  
    

尤其是博客链接，官方提供了常见工作流程的分步教程，非常详细。

![](https://picx.zhimg.com/v2-7f514bbc33b91a7a38e8edc12a3548f5_r.jpg?source=1def8aca)

除此之外，还有配置方法以及常见问题介绍等，有条件的同学可以下载个 Claude Code，并配合官方教程进行体验，结合自家 Claude 模型的效果，不会让你失望的。

![](https://pic1.zhimg.com/v2-7c81b7973a7cc2df96ac46477e9b498d_r.jpg?source=1def8aca)