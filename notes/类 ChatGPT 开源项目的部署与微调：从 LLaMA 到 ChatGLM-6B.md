> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [e.betheme.net](http://e.betheme.net/article/show-1348235.html?action=onClick)

> 前言 近期 ， 除了研究 ChatGPT 背后的各种技术细节 不断看论文 (至少 100 篇 ，100 篇目录见此 ：ChatGPT 相关技术必读论文 100 篇)， 还开......

近期，除了研究 ChatGPT 背后的各种技术细节 不断看论文 (至少 100 篇，100 篇目录见此：ChatGPT 相关技术必读论文 100 篇)，还开始研究一系列开源模型 (包括各自对应的模型架构、训练方法、训练数据、本地私有化部署、硬件配置要求、微调等细节) 

本文一开始是作为此文《ChatGPT 技术原理解析：从 RL 之 PPO 算法、RLHF 到 GPT4、instructGPT》的第 4 部分，但随着研究深入 为避免该文篇幅又过长，将把『第 4 部分 开源项目』抽取出来 独立成本文，然后不断续写本文到最终 2 万字左右 (3.22 日 7000 余字)

毕竟我上半年的目标之一，便是把 ChatGPT 涉及的所有一切关键技术细节，以及相关的开源项目都研究的透透的，故过程中会不断产出一篇篇新文章出来，比如：

1.  微积分和概率统计极简入门
2.  一文通透优化算法
3.  强化学习极简入门：通俗理解 MDP、DP MC TC 和 Q 学习、策略梯度、PPO
4.  ChatGPT 技术原理解析 (本系列核心主体，也是同类解读里最清晰、全面、细致的一篇)
5.  ChatGPT 相关技术论文 100 篇
6.  类 ChatGPT 开源项目
7.  CV 多模态模型发展史 (23 年 4 月发布)，详述 GPT4 背后多模态的能力起源与发展历史，包括但不限于 DTER、DDPM、Vision Transformer、CLIP、Swin Transformer、DALL·E 2、Stable Diffusion、BEiT-3、Visual ChatGPT、GPT4 等.

 虽说 GPT3 在 2020 年就出来了，但 OpenAI 并未开源，所以直到一年半后以后才有国内外各个团队比如 DeepMind 等陆续复现出来，这些大厂的复现代码我们自然无法窥知一二，毕竟人家也未开源出来

再到后来基于 GPT3 的 InstructGPT、基于 GPT3.5ChatGPT 初版 (GPT3.5 的参数规模也尚无准确定论)、GPT4 均未开源，OpenAI 不再 open，好在 Meta 等公司或研究者开源出了一系列类 ChatGPT 项目，本部分针对其中部分做下简要推荐 (根据发布顺序排序)

1.1 基于 Colossal-AI 低成本实现类 ChatGPT 迷你版的训练过程
------------------------------------------

2.15，很多朋友在 GitHub 上发现了一个基于 Colossal-AI 低成本实现类 ChatGPT 迷你版训练过程的开源项目 (基于 OPT + RLHF + PPO)，虽**是类似 GPT3 的开源项目与 RLHF 的结合**，但可以增进我们对 ChatGPT 的理解，该项目有几个不错的特点

1.  很多同学一看到 DL，便会想到大数据，而数据量一大，还用 CPU 处理的话很可能训练一个小任务都得半天，而如果用 GPU 跑，可能一两分钟就出来了。于此，在深度学习大火的那几年，特别是 AlphaGo 出来的 16 年起，我司七月在线便分别为 VIP、AI 系统大课、在职提升大课、求职 / 论文 / 申博 / 留学 1V1 辅导提供 GPU 云平台进行实战训练
    
    但如果想训练那种千亿参数规模的开源模型，就不只是有 GPU 就完事了，比如 1750 亿参数规模这种得用 64 张 AI 100(即便经过一系列内存开销上的优化，也得至少 32 张 AI 100，单张 AI 100 售价 10 万以上，且现在还经常没货)，这样的硬件要求是大部分个人是无法具备的，所以该开源项目提供了单 GPU、独立 4/8-GPUs 的版本
    
2.  如下代码所示，启动简单
    
    ```
    from chatgpt.nn import GPTActor, GPTCritic, RewardModel
    from chatgpt.trainer import PPOTrainer
    from chatgpt.trainer.strategies import ColossalAIStrategystrategy = ColossalAIStrategy(stage=3, placement_policy='cuda')with strategy.model_init_context():actor = GPTActor().cuda()critic = GPTCritic().cuda()initial_model = deepcopy(actor).cuda()reward_model = RewardModel(deepcopy(critic.model)).cuda()trainer = PPOTrainer(strategy, actor, critic, reward_model, initial_model, ...)
    trainer.fit(prompts)
    ```
    
3.  训练过程明确清晰，如下图 (由于上文已经详细介绍过 ChatGPT 的训练步骤，故不再赘述)
    
    ![](https://img-blog.csdnimg.cn/1ecb75833281415497f94e0cbe0279bd.png)
    
    ​此外，据钟博士在我所维护的『Machine Learning 读书会群』里所说，Colossal-AI 的并行效率确实不错，是新加坡的一个初创团队推出的，但目前尚没有团队采用 Colossal-AI 框架来做主训练框架训练 175b 级别的超大模型，可以再了解下 Meta 家训练 OPT 用的 Metaseq
    

1.2 PaLM-rlhf-pytorch、Open-Assistant 等项目
----------------------------------------

此外，GitHub 上还有这些项目

*   PaLM-rlhf-pytorch  
    该项目是在 PaLM 架构之上实现 RLHF，可以理解为基于 PaLM 的 ChatGPT
*   Open-Assistant  
    它旨在让每一个人都可以访问基于聊天的大语言模型，项目作者希望借此在语言创新方面掀起一场革命，就像 stable diffusion 帮助世界以新的方式创造艺术和图像一样  
    项目作者计划收集高质量人工生成指令执行样本（指示 + 响应），目标大于 50k，对于收集到的每个指示，他们将采样多个补全结果，之后进入基于指示和奖励模型的 RLHF 训练阶段

2.1 Meta 发布大语言模型 LLaMA：参数少但多数任务的效果好于 GPT3
-----------------------------------------

一直致力于 LLM 模型研究的国外 TOP 3 大厂除了 OpenAI、Google，便是 Meta(原来的 Facebook)

Meta 曾第一个发布了基于 LLM 的聊天机器人——BlenderBot 3，但输出不够安全，很快下线。再后来，Meta 发布一个专门为科学研究设计的模型 Galactica，但用户期望过高，发布三天后又下线

2.24 日，Meta 通过论文《LLaMA: Open and Efficient Foundation Language Models》发布了自家的大型语言模型 LLaMA，有多个参数规模的版本 (7B 13B 33B 65B)

LLaMA 只使用公开的数据 (CommonCrawl 的数据占比 67%，C4 数据占比 15%，Github Wikipedia Books 这三项数据均各自占比 4.5%，ArXiv 占比 2.5%，StackExchange 占比 2%)，论文中提到

> When training a 65B-parameter model, our code processes around 380 tokens/sec/GPU on _2048 A100 GPU_ with 80GB of RAM.
> 
> This means that training over our dataset containing 1.4T tokens takes approximately 21 days

且试图证明小模型在足够多的的数据上训练后，也能达到甚至超过大模型的效果

*   比如 13B 参数的版本在多项基准上测试的效果好于 2020 年的参数规模达 175B 的 GPT-3
*   而对于 65B 参数的 LLaMA，则可与 DeepMind 的 Chinchilla(70B 参数) 和谷歌的 PaLM(540B 参数) 旗鼓相当
*   且 Meta 还尝试使用了论文「Scaling Instruction-Finetuned Language Models」中介绍的指令微调方法，由此产生的模型 LLaMA-I，在 MMLU(_Massive Multitask Language Understanding，大型多任务语言理解_) 上要优于 Google 的指令微调模型 Flan-PaLM-cont(62B)

模型结构上，除了继续基于 Transformer 这个架构外

*   为了提高训练的稳定性，对每个 transformer 子层的输入进行归一化，而不是对输出进行归一化  
    且使用由 Zhang 和 Sennrich(2019) 提出的 RMSNorm 归一化函数
*   用 Shazeer(2020) 提出的 SwiGLU 替代 ReLU
*   删除 absolute positional embeddings, 在网络的每一层添加 RoPE

LLaMA 发布不久后，一些研究者基于它做了不少工作

*   一开始最小参数 7B 的模型也需要近 30GB 的 GPU 才能运行，但通过比特和字节库进行浮点优化，能够让模型在单个 NVIDIA RTX 3060 上运行
*   之后，GitHub 上的一名研究人员甚至能够在 Ryzen 7900X CPU 上运行 LLM 的 7B 版本，每秒能推断出几个单词
*   再之后，有研究者推出了 llama.cpp，无需 GPU，就能运行 LLaMA  
    llama.cpp 项目实现了在 MacBook 上运行 LLaMA，还有开发者成功的在 4GB RAM 的树莓派上运行了 LLaMA 7B，总结而言，即使开发者没有 GPU ，也能运行 LLaMA 模型
*   再之后**，**初创公司 Nebuly AI 开源了 RLHF 版的 LLaMA，即 **ChatLLaMA** 的训练方法  
    由于 LLaMA 没有使用 RLHF 方法，因此 ChatLLaMA 的训练过程类似 ChatGPT，该项目允许基于预训练的 LLaMA 模型构建 ChatGPT 形式的服务  
    与 ChatGPT 相比，LLaMA 架构更小，但训练过程和单 GPU 推理速度更快，成本更低  
    且该库还支持所有的 LLaMA 模型架构（7B/13B/33B/65B），因此用户可以根据训练时间和推理性能偏好对模型进行微调

2.2 Stanford Alpaca：结合英文语料通过 Self Instruct 方式微调 LLaMA 7B
--------------------------------------------------------

3 月中旬，斯坦福发布 Alpaca：号称只花 100 美元，人人都可微调 Meta 家 70 亿参数的 LLaMA 大模型 (即 LLaMA 7B)，具体做法是通过 52k 指令数据，然后在 8 个 80GB A100 上训练 3 个小时，最终性能比肩 GPT-3.5(text-davinci-003)

*   论文《Alpaca: A Strong Open-Source Instruction-Following Model》
*   代码地址：https://github.com/tatsu-lab/stanford_alpaca

而斯坦福团队微调 LLaMA 7B 所用的 52K 指令数据，便是通过 Self-Instruct『Self-Instruct _是来自华盛顿大学 Yizhong Wang 等 22 年 12 月通过这篇论文《SELF-INSTRUCT: Aligning Language Model with Self Generated Instructions》提出的_』提示 GPT3 的 API 拿到的

![](https://img-blog.csdnimg.cn/f77f5886c54a47779b0d463b6e49fa93.png)

​具体而言，论文中提出

1.  人工设计 175 个任务，每个任务都有对应的 {指令 输入 输出 / 实例} 或{指令 输出 / 实例}，将这 175 个任务数据作为种子集
2.  然后提示模型比如 GPT3 对应的 text-davinci-001 (原论文中没用 text-davinci-003，because their newer engines are trained with the latest user data and are likely to already see the SUPERNI evaluation set，但实际应用时比如斯坦福 Alpaca 可以指定 text-davinci-003 生成指令)，使用种子集作为上下文示例来生成更多新的指令
3.  对该模型生成的指令判断是否分类任务
4.  使用模型生成实例
5.  对上述模型生成的数据 {指令 输入 输出 / 实例} 过滤掉低质量或相似度高的
6.  将经过过滤和后处理的数据添加到种子池中  
    一直重复上述 2-6 步直到种子池有足够多的数据

而斯坦福的 Alpaca，就是花了不到 500 美元使用 OpenAI API 生成了 5.2 万个这样的示例微调 LLaMA 搞出来的，个人觉得可以取名为 _instructLLaMA-7B_，^_^

2.3 BELLE(中文版 Stanford Alpaca)：通过 Self Instruct 方式微调 BLOOMZ-7B
--------------------------------------------------------------

Stanford Alpaca 的种子任务都是英语，收集的数据也都是英文，因此训练出来的模型未对中文优化。为了提升对话模型在中文上的效果，开源中文对话大模型 70 亿参数的 BELLE（Bloom-Enhanced Large Language model Engine）来了 (项目地址)。

它基于 Stanford Alpaca 完成，但进行了中文优化，并对生成代码进行了一些修改，不仅如此，模型调优也仅使用由 GPT3.5 (默认使用模型 text-davinci-003，如果想使用 ChatGPT 的 API 比如 gpt-3.5-turbo 模型，可通过参数控制) 生产的数据（不包含任何其他数据）。

在数据方面，该项目开源了基于 Stanford Alpaca 的数据收集代码，基于这段代码生成了约 100 万条中文数据，结合 Alpaca 的 5.2 万条英文数据，在 BLOOMZ-7B 模型训练得到的 checkpoint 上传在 Hugging Face

> BLOOM 是由 HuggingFace 于 2023 年 3 月中旬推出的大模型 (HuggingFace 是著名开源工具 Transformers 的开发公司，很多推理工具都会支持 Transformers 中的模型)，规模最大版本的参数量达到 176B(GPT-3 是 175B)， 对应的论文为《BLOOM: A 176B-Parameter Open-Access Multilingual Language Model》(翻译之一，解读之一)
> 
> * * *
> 
> 截至 23 年 3 月中旬，超过 100B 参数量且能够支持中文的开源大模型只有 BLOOM 和 GLM-130B

该项目主要包含以下三部分内容：

*   175 个中文种子任务，斯坦福 Alpaca 一样，每个任务都包含对应的指令 / 任务、prompt、输出  
    ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)  zh_seed_tasks.jsonl：样例如下  
     {  "id": "seed_task_20", "name": "horror_movie_opening",  
    "instruction": "你需要为一部恐怖电影写一个创意的开场场景。",  
    "instances": [{"input": "","output":" 太阳已经落山，留下了一个黑暗的小镇。微风吹拂空荡的街道，让每一个冒险走出门外的人感到一阵寒意。唯一的声音是被风吹动的树叶发出的轻微沙沙声。突然，一声令人毛骨悚然的尖叫声划破了寂静，随后是玻璃破碎的声音。一所房子亮起了灯光，可以看到一个人影朝镇中心奔跑。当> 那个人影越来越靠近时，清楚地看到那是一个年轻女子，她浑身血迹斑斑。"}],  
    "is_classification": false  }
*   ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)  prompt_cn.txt: 生成所使用的提示语  
    ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)  0.5M 生成的数据
*   生成数据及其代码  
    沿用 Alpaca 的方式：  
    pip install -r requirements.txt  
    export OPENAI_API_KEY=YOUR_API_KEY  
    python generate_instruction.py generate_instruction_following_data
    
    默认使用 Completion API，模型 text-davinci-003。如果想使用 Chat API 并使用 gpt-3.5-turbo 模型，可通过参数控制：  
    python generate_instruction.py generate_instruction_following_data \  
      --api=chat --model_name=gpt-3.5-turbo
    
    输出文件在 Belle.train.json，可以人工筛选后再使用
    
*   基于 BLOOMZ-7B1-mt 模型和 Belle.train.json 训练模型

3.1 GLM: General Language Model Pretraining with Autoregressive Blank Infilling
-------------------------------------------------------------------------------

在 2022 年上半年，当时主流的预训练框架可以分为三种：

*   autoregressive，**自回归模型**的代表是单向的 **GPT**，本质上是一个从左到右的语言模型，常用于无条件生成任务（unconditional generation），缺点是无法利用到下文的信息
*   autoencoding，**自编码模型**是通过某个降噪目标 (如掩码语言模型，简单理解就是通过挖洞，训练模型做完形填空的能力) 训练的语言编码器，如双向的 **BERT**、ALBERT、RoBERTa、DeBERTa  
    自编码模型擅长自然语言理解任务（natural language understanding tasks），常被用来生成句子的上下文表示，缺点是不适合生成任务
*   encoder-decoder，则是一个完整的 Transformer 结构，包含一个编码器和一个解码器，以 **T5、BART** 为代表，常用于有条件的生成任务 （conditional generation）  
    细致来说，T5 的编码器中的注意力是双向，解码器中的注意力是单向的，因此可同时应用于自然语言理解任务和生成任务。但 T5 为了达到和 RoBERTa 和 DeBERTa 相似的性能，往往需要更多的参数量

这三种预训练模型各自称霸一方，那么问题来了，可否结合三种预训练模型，以成天下之一统？这便是 2022 年 5 月发表的这篇论文《GLM: General Language Model Pretraining with Autoregressive Blank Infilling》的出发点，它提出了 GLM 架构 (这是张义策关于 GLM 论文的解读之一，下面三小节的内容主要参考自该篇解读)

### 3.1.1 如何将生成模型 GPT 和掩码语言模型 BERT 结合在一起

首先，考虑到三类预训练模型的训练目标

*   GPT 的训练目标是从左到右的文本生成
*   BERT 的训练目标是对文本进行随机掩码，然后预测被掩码的词
*   T5 则是接受一段文本，从左到右的生成另一段文本

为了大一统，我们必须在结构和训练目标上兼容这三种预训练模型。如何实现呢？文章给出的解决方法

*   **结构**上，只需要 GLM 中同时存在单向注意力和双向注意力即可  
    在原本的 Transformer 模型中，这两种注意力机制是通过修改 attention mask 实现的  
    当 attention_mask 是全 1 矩阵的时候，这时注意力是双向的  
    当 attention_mask 是三角矩阵的时候（如下图），注意力就是单向
    
    类似地，我们可以在只使用 Transformer 编码器的情况下，自定义 attention mask 来兼容三种模型结构
    
    ![](https://img-blog.csdnimg.cn/img_convert/2b0dd7de576fded8b3e3baff2ff0dcb1.png)
    
*   **训练目标**上，这篇文章提出一个自回归空格填充的任务（Autoregressive Blank Infifilling），来兼容三种预训练目标  
    自回归填充有些类似掩码语言模型，首先采样输入文本中部分片段，将其替换为 [MASK] 标记，然后预测 [MASK] 所对应的文本片段，与掩码语言模型不同的是，预测的过程是采用自回归的方式
    
    ![](https://img-blog.csdnimg.cn/3b381991172f4828bc99040ffe1169e3.png)
    
    具体来说，  
    ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)  当被掩码的片段长度为 1 的时候，空格填充任务等价于掩码语言建模，类似 BERT  
    ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)  当将文本 1 和文本 2 拼接在一起，然后将文本 2 整体掩码掉，空格填充任务就等价于条件语言生成任务，类似 T5/BART  
    ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)  当全部的文本都被掩码时，空格填充任务就等价于无条件语言生成任务，类似 GPT

### 3.1.2 如何理解 GLM 的自回归空格填充任务

假设原始的文本序列为![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)，采样的两个文本片段为 ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==) 和 ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==) ，那么掩码后的文本序列为 ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)（以下简称 Part A），如上图所示，拆解图中的三块分别可得

![](https://img-blog.csdnimg.cn/477772cbff6c4c93ac77c25bafe350c5.png)

需要说明的是，Part B 包含所有被掩码的文本片段，但是文本片段的相对顺序是随机打乱的

### 3.1.3 GLM 的预训练和微调

作者使用了两个预训练目标来优化 GLM，两个目标交替进行：

*   文档级别的预测 / 生成：从文档中随机采样一个文本片段进行掩码，片段的长度为文档长度的 50%-100%
*   句子级别的预测 / 生成：从文档中随机掩码若干文本片段，每个文本片段必须为完整的句子，被掩码的词数量为整个文档长度的 15%

尽管 GLM 是 BERT、GPT、T5 三者的结合，但是在预训练时，为了适应预训练的目标，作者还是选择掩码较长的文本片段，以确保 GLM 的文本生成能力，并在微调的时候将自然语言理解任务也转化为生成任务，如情感分类任务转化为填充空白的任务

*   输入：{Sentence}，prompt：It is really ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==) ，对应的标签为 good 和 bad

![](https://img-blog.csdnimg.cn/b76c01936f1b4823a822058aa8b84ed8.png)

此外，模型架构层面，除了整体基于 Transformer 之外

1.  重新排列了层归一化和残差连接的顺序
2.  针对 token 的输出预测使用单一线性层
3.  用 GeLU 替换 ReLU 激活函数

3.2 GLM-130B：国内为数不多的可比肩 GPT3 的大模型之一 
------------------------------------

2022 年 8 月，清华背景的智谱 AI 基于 GLM 框架，正式推出拥有 1300 亿参数的中英双语稠密模型 GLM-130B(论文地址、代码地址，论文解读之一，GLM-130B is trained on a cluster of 96 DGX-A100 GPU (8×40G) servers with a 60-day，可以较好的支持 2048 个 token 的上下文窗口)

其在一些任务上的表现优于 GPT3-175B，是国内与 2020 年 5 月的 GPT3 在综合能力上差不多的模型之一 (即便放到 23 年年初也并不多)，这是它的一些重要特点

![](https://img-blog.csdnimg.cn/img_convert/07073ab3208748b0a3ce3d17fcc99c31.png)

3.3 类 ChatGPT 开源项目 ChatGLM-6B 的训练框架与部署步骤
----------------------------------------

ChatGLM-6B(介绍页面、代码地址)，是智谱 AI 开源、支持中英双语的对话语言模型，其

*   基于 General Language Model(GLM) 架构，具有 62 亿参数，支持在单张 2080Ti 上进行推理使用 (且 INT4 量化级别下最低只需 6GB 显存)
*   ChatGLM-6B 参考了 ChatGPT 的设计思路，在千亿基座模型 GLM-130B 中注入了代码预训练，通过监督微调 (Supervised Fine-Tuning)、反馈自助 (Feedback Bootstrap)、人类反馈强化学习（Reinforcement Learning from Human Feedback） 等方式等技术实现人类意图对齐，并针对中文问答和对话进行优化
*   最终经过约 1T 标识符的中英双语训练，生成符合人类偏好的回答

虽尚有很多不足 (比如因为 6B 的大小限制，导致模型的记忆能力、编码、推理能力皆有限)，但在 6B 这个参数量级下不错了，部署也非常简单，我七月在线的同事朝阳花了一两个小时即部署好了 (主要时间花在模型下载上，实际的部署操作很快)

![](https://img-blog.csdnimg.cn/dacd342e555044c984778378e3ad2e7a.png)

以下是具体的部署过程 (机器用的七月的 GPU 服务器，显存大小为 16G 的 P100，最终占用 13G)

1.  配置环境：pip install -r requirements.txt（特别注意 torch 版本大于 1.10，transformers 大于 4.23）  
    torch 的安装命令参考 pytorch 官网：https://pytorch.org/
2.  下载项目仓库：  
    git clone https://github.com/THUDM/ChatGLM-6B  
    cd ChatGLM-6B
3.  下载 ChatGLM-6B 模型文件  
    下载地址：https://huggingface.co/THUDM/chatglm-6b
4.  有两种运行方式，一种是基于 Gradio，一种是基于 streamlit  
    基于 Gradio：  
    运行 web_demo.py 即可（注意可以设置 share=True，便于公网访问）：python web_demo.py（注意运行前确认下模型文件路径）
    
    基于 streamlit：  
    pip install streamlit  
    pip install streamlit-chat  
    streamlit run web_demo2.py --server.port 6006（可以将 6006 端口放出，便于公网访问）
    

此外，据介绍，GLM 团队正在内测 130B 参数的 ChatGLM，相信从 6B 到 130B，效果应该能提升很多

3.4 如何微调 ChatGLM-6B：基于 Stanford Alpaca 的 52K 数据集 + LoRA
-------------------------------------------------------

从上文可知，Stanford Alpaca 的 52K 数据集是通过 Self Instruct 方式提示 GPT3 对应的 API 产生的指令数据，然后通过这批指令数据微调 Meta 的 LLaMA 7B

通过 GitHub 上的这个微调 ChatGLM-6B 项目 (作者：mymusise)，可知

### 3.4.1 环境准备

*   显卡: 显存 >= 16G (最好 24G 或者以上)
*   环境：
    *   python>=3.8
    *   cuda>=11.6, cupti, cuDNN, TensorRT 等深度学习环境
    *   pip3 install -r requirements.txt

### 3.4.2 数据预处理

转化 alpaca 数据集为 jsonl

```
python cover_alpaca2jsonl.py \--data_path data/alpaca_data.json \--save_path data/alpaca_data.jsonl \
```

tokenization

```
python tokenize_dataset_rows.py \--jsonl_path data/alpaca_data.jsonl \--save_path data/alpaca \--max_seq_length 320
```

*   `--jsonl_path` 微调的数据路径, 格式 jsonl, 对每行的 ['context'] 和['target']字段进行 encode
*   `--save_path` 输出路径
*   `--max_seq_length` 样本的最大长度

### 3.4.3 训练

```
python finetune.py \--dataset_path data/alpaca \--lora_rank 8 \--per_device_train_batch_size 2 \--gradient_accumulation_steps 1 \--max_steps 52000 \--save_steps 1000 \--save_total_limit 2 \--learning_rate 2e-5 \--fp16 \--remove_unused_columns false \--logging_steps 50 \--output_dir output
```

### 3.4.4 推理

参考 infer.ipynb

利用 Alpaca 数据集合对 ChatGLM-6B Finetune 后，在 Alpaca 数据集上表现得更好:

*   `Answer:` 是模型的输出
*   `#### Answer:` 是原答案

![](https://img-blog.csdnimg.cn/img_convert/deb17be1ccb95044b0dd2fd648d67e92.jpeg)