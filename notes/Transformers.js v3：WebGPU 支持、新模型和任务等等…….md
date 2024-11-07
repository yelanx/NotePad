> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [huggingface.co](https://huggingface.co/blog/transformersjs-v3)

> 我们正致力于通过开源和开放式科学推动人工智能的发展并使其民主化......

[返回文章](/blog)

[](#transformersjs-v3-webgpu-support-new-models--tasks-and-more)Transformers.js v3：WebGPU 支持、新模型和任务等等……
=======================================================================================================

2024年10月22日 [GitHub 上的更新](https://github.com/huggingface/blog/blob/main/transformersjs-v3.md)

 林 54

*   [![](https://cdn-avatars.huggingface.co/v1/production/uploads/5dd96eb166059660ed1ee413/NQtzmrDdbG0H8qkZvRyGk.jpeg)](/julien-c "julien-c")
*   [![](https://cdn-avatars.huggingface.co/v1/production/uploads/5e3aec01f55e2b62848a5217/PMKS0NNB4MJQlTSFzh918.jpeg)](/lysandre "萊桑德爾")
*   [![](https://cdn-avatars.huggingface.co/v1/production/uploads/1642182289584-5e863990b6845d56ef3d4fb9.jpeg)](/dayanruben "达扬鲁本")
*   [![](https://cdn-avatars.huggingface.co/v1/production/uploads/1594214747713-5e9ecfc04957053f60648a3e.png)](/lhoestq "洛斯特克")
*   [![](https://cdn-avatars.huggingface.co/v1/production/uploads/1613511937628-5fb15d1e84389b139cf3b508.jpeg)](/MoritzLaurer "莫里兹·劳雷尔")
*   [![](https://cdn-avatars.huggingface.co/v1/production/uploads/1673701526502-60098a0f460b9e8842d93363.jpeg)](/rajivmehtapy "拉吉夫梅赫塔皮")
*   + 48

 [![](https://cdn-avatars.huggingface.co/v1/production/uploads/61b253b7ac5ecaae3d1efe0c/hwiQ0uvz3t-L5a-NtBIO6.png) 希諾· 約書](/Xenova) 

*   [安装](#installation "安装")
    
*   [WebGPU 支持](#webgpu-support "WebGPU 支持")
    *   [Transformers.js v3 中的用法](#usage-in-transformersjs-v3 "Transformers.js v3 中的用法")
        
*   [新的量化格式 (dtypes)](#new-quantization-formats-dtypes "新的量化格式 (dtypes)")
    *   [基本用法](#basic-usage "基本用法")
        
    *   [每个模块的数据类型](#per-module-dtypes "每个模块的数据类型")
        
*   [120 种支持架构](#120-supported-architectures "120 种支持架构")
    
*   [示例项目和模板](#example-projects-and-templates "示例项目和模板")
    
*   [超过 1200 个预转换模型](#over-1200-pre-converted-models "超过 1200 个预转换模型")
    
*   [Node.js (ESM + CJS)、Deno 和 Bun 兼容性](#nodejs-esm--cjs-deno-and-bun-compatibility "Node.js (ESM + CJS)、Deno 和 Bun 兼容性")
    
*   [NPM 和 GitHub 上的新主页](#a-new-home-on-npm-and-github "NPM 和 GitHub 上的新主页")
    

经过一年多的开发，我们很高兴地宣布发布🤗Transformers.js v3！

亮点包括：

*   [WebGPU支持（比WASM快100倍！）](#webgpu-support)
*   [新的量化格式 (dtypes)](#new-quantization-formats-dtypes)
*   [支持 120 种架构](#120-supported-architectures)
*   [25 个新的示例项目和模板](#example-projects-and-templates)
*   [Hugging Face Hub 上有超过 1200 个预转换模型](#over-1200-pre-converted-models)
*   [Node.js (ESM + CJS)、Deno 和 Bun 兼容性](#nodejs-esm--cjs-deno-and-bun-compatibility)
*   [GitHub 和 NPM 上的新主页](#a-new-home-on-npm-and-github)

[](#installation)安装
-------------------

[您可以通过以下方式从 NPM](https://www.npmjs.com/package/@huggingface/transformers)安装 Transformers.js v3 ：

```
npm我 @huggingface/transformers
```

然后，使用

```
从“ @huggingface /transformers”导入{ pipeline } ；
```

或者通过 CDN

```
从“https://cdn.jsdelivr.net/npm/@huggingface /transformers@3.0.0 ”导入{ pipeline } ；
```

欲了解更多信息，请查看[文档](https://hf.co/docs/transformers.js)。

[](#webgpu-support)WebGPU 支持
----------------------------

WebGPU 是用于加速图形和计算的全新 Web 标准。该[API](https://developer.mozilla.org/en-US/docs/Web/API/WebGPU_API)使 Web 开发人员能够使用底层系统的 GPU 直接在浏览器中执行高性能计算。WebGPU 是[WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)的后继者，可提供显着更好的性能，因为它允许与现代 GPU 进行更直接的交互。最后，它支持通用 GPU 计算，这个协议非常适合机器学习！

> 2024 年 10 月，全球 WebGPU 支持率约为 70%（根据[caniuse.com](https://caniuse.com/webgpu)），这意味着某些用户可能无法使用该 API。
> 
> 如果以下演示在您的浏览器中无效，您可能需要使用功能标志启用它：
> 
> *   Firefox：带有`dom.webgpu.enabled`标记（参见[此处](https://developer.mozilla.org/en-US/docs/Mozilla/Firefox/Experimental_features#:~:text=tested%20by%20Firefox.-,WebGPU%20API,-The%20WebGPU%20API)）。
> *   Safari：带有`WebGPU`功能标志（参见[此处](https://webkit.org/blog/14879/webgpu-now-available-for-testing-in-safari-technology-preview/)）。
> *   旧版 Chromium 浏览器（在 Windows、macOS、Linux 上）：带有`enable-unsafe-webgpu`标志（参见[此处](https://developer.chrome.com/docs/web-platform/webgpu/troubleshooting-tips)）。

### [](#usage-in-transformersjs-v3)Transformers.js v3 中的用法

感谢我们与[ONNX Runtime Web 的](https://www.npmjs.com/package/onnxruntime-web)合作，启用 WebGPU 加速就像`device: 'webgpu'`在加载模型时进行设置一样简单。让我们看一些示例！

**示例：**在WebGPU上计算文本嵌入（[演示](https://v2.scrimba.com/s06a2smeej)）

```
从 “@huggingface/transformers”导入{pipeline} ；

// 创建特征提取管道
const extractor = await pipeline(
   "feature-extraction" ,
   "mixedbread-ai/mxbai-embed-xsmall-v1" ,
  {设备：“webgpu” }，
（英文）：

// 计算嵌入
const texts = [ "Hello world!" , "This is an example sentence." ];
 const embeddings = await extractor(texts, { pooling : "mean" , normalize : true });
 console .log(embeddings.tolist());
 // [ 
// [-0.016986183822155, 0.03228696808218956, -0.0013630966423079371, ... ], 
// [0.09050482511520386, 0.07207386940717697, 0.05762749910354614, ... ], 
// ]
```

**译文：**在 WebGPU 上使用 OpenAI 耳语执行自动语音识别（[演讲](https://v2.scrimba.com/s0oi76h82g)）

```
从 “@huggingface/transformers”导入{pipeline} ；

// 创建自动语音识别管道
const transcriber = await pipeline(
   "automatic-speech-recognition" ,
   "onnx-community/whisper-tiny.en" ,
  {设备：“webgpu” }，
（英文）：

// 从 URL 转录音频
const url = "https://huggingface.co/datasets/Xenova/transformers.js-docs/resolve/main/jfk.wav" ;
 const output = await transcriber(url);
 console .log(output);
 // { text: '所以我的美国同胞们，不要问你的国家能为你做什么，而要问你能为你的国家做什么。' }
```

**示例：**在 WebGPU 上使用 MobileNetV4 执行图像分类（[演示](https://v2.scrimba.com/s0fv2uab1t)）

```
从 “@huggingface/transformers”导入{pipeline} ；

// 创建图像分类管道
const classifier = await pipeline(
   "image-classification" ,
   "onnx-community/mobilenetv4_conv_small.e2400_r224_in1k" ,
  {设备：“webgpu” }，
（英文）：

// 通过 URL 对图像进行分类
const url = "https://huggingface.co/datasets/Xenova/transformers.js-docs/resolve/main/tiger.jpg" ;
 const output = await classifier(url);
 console.log (output);
 // [ 
// { 标签：'老虎，Panthera tigris'，得分：0.6149784922599792 }, 
// { 标签：'虎猫'，得分：0.30281734466552734 }, 
// { 标签：'虎斑猫，虎斑猫'，得分：0.0019135422771796584 }, 
// { 标签：'山猫，美洲山猫'，得分：0.0012161266058683395 }, 
// { 标签：'埃及猫'，得分：0.0011465961579233408 } 
// ]
```

[](#new-quantization-formats-dtypes)新的量化格式 (dtypes)
---------------------------------------------------

在 Transformers.js v3 之前，我们通过分别设置为或`quantized`来指定是否使用模型的量化 (q8) 或全精度 (fp32) 变体。现在，我们添加了使用参数从更大列表中进行选择的功能。`quantized``true``false``dtype`

可用量化的列表取决于模型，但一些常见的量化是：全精度（）`"fp32"`、半精度（`"fp16"`）、8 位（`"q8"`、、`"int8"`）`"uint8"`和 4 位（`"q4"`、、）。`"bnb4"``"q4f16"`

 ![](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/transformersjs-v3/dtypes-dark.jpg) [（例如，mixedbread-ai/mxbai-embed-xsmall-v1）](https://huggingface.co/mixedbread-ai/mxbai-embed-xsmall-v1/tree/main/onnx)

### [](#basic-usage)基本用法

**示例：**以 4 位量化运行 Qwen2.5-0.5B-Instruct（[演示](https://v2.scrimba.com/s0dlcpv0ci)）

```
import { pipeline } from "@huggingface/transformers";

// Create a text generation pipeline
const generator = await pipeline(
  "text-generation",
  "onnx-community/Qwen2.5-0.5B-Instruct",
  { dtype: "q4", device: "webgpu" },
);

// Define the list of messages
const messages = [
  { role: "system", content: "You are a helpful assistant." },
  { role: "user", content: "Tell me a funny joke." },
];

// Generate a response
const output = await generator(messages, { max_new_tokens: 128 });
console.log(output[0].generated_text.at(-1).content);
```

### [](#per-module-dtypes)每个模块的数据类型

一些编码器 - 解码器模型（如 Whisper 或 Florence-2）对量化设置极为敏感：尤其是编码器的量化设置。为此，我们添加了选择每个模块 dtypes 的功能，这可以通过提供从模块名称到 dtype 的映射来实现。

**示例：**在 WebGPU 上运行 Florence-2（[演示](https://v2.scrimba.com/s0pdm485fo)）

```
import { Florence2ForConditionalGeneration } from "@huggingface/transformers";

const model = await Florence2ForConditionalGeneration.from_pretrained(
  "onnx-community/Florence-2-base-ft",
  {
    dtype: {
      embed_tokens: "fp16",
      vision_encoder: "fp16",
      encoder_model: "q4",
      decoder_model_merged: "q4",
    },
    device: "webgpu",
  },
);
```

![](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/transformersjs-v3/florence-2-webgpu.gif)

查看完整代码示例

```
import {
  Florence2ForConditionalGeneration,
  AutoProcessor,
  AutoTokenizer,
  RawImage,
} from "@huggingface/transformers";

// Load model, processor, and tokenizer
const model_id = "onnx-community/Florence-2-base-ft";
const model = await Florence2ForConditionalGeneration.from_pretrained(
  model_id,
  {
    dtype: {
      embed_tokens: "fp16",
      vision_encoder: "fp16",
      encoder_model: "q4",
      decoder_model_merged: "q4",
    },
    device: "webgpu",
  },
);
const processor = await AutoProcessor.from_pretrained(model_id);
const tokenizer = await AutoTokenizer.from_pretrained(model_id);

// Load image and prepare vision inputs
const url = "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg";
const image = await RawImage.fromURL(url);
const vision_inputs = await processor(image);

// Specify task and prepare text inputs
const task = "<MORE_DETAILED_CAPTION>";
const prompts = processor.construct_prompts(task);
const text_inputs = tokenizer(prompts);

// Generate text
const generated_ids = await model.generate({
  ...text_inputs,
  ...vision_inputs,
  max_new_tokens: 100,
});

// Decode generated text
const generated_text = tokenizer.batch_decode(generated_ids, {
  skip_special_tokens: false,
})[0];

// Post-process the generated text
const result = processor.post_process_generation(
  generated_text,
  task,
  image.size,
);
console.log(result);
// { '<MORE_DETAILED_CAPTION>': 'A green car is parked in front of a tan building. The building has a brown door and two brown windows. The car is a two door and the door is closed. The green car has black tires.' }
```

[](#120-supported-architectures)120 种受支持的架构
-------------------------------------------

此版本将支持的架构总数增加到 120 个（查看[完整列表](https://huggingface.co/docs/transformers.js/index#models)），涵盖了广泛的输入模式和任务。值得注意的新名称包括：Phi-3、Gemma & Gemma 2、LLaVa、Moondream、Florence-2、MusicGen、Sapiens、Depth Pro、PyAnnote 和 RT-DETR。

![](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/transformersjs-v3/architectures.png)

新款车型一览

1.  **[Cohere](https://huggingface.co/docs/transformers/main/model_doc/cohere)**（来自 Cohere）发布了论文[《Command-R：Cohere 的生产规模检索增强生成》](https://txt.cohere.com/command-r/)。
2.  **[Decision Transformer](https://huggingface.co/docs/transformers/model_doc/decision_transformer)**（来自伯克利 / Facebook/Google）与论文[《决策变压器：通过序列建模进行强化学习》](https://arxiv.org/abs/2106.01345)一起发布，论文作者是 Lili Chen、Kevin Lu、Aravind Rajeswaran、Kimin Lee、Aditya Grover、Michael Laskin、Pieter Abbeel、Aravind Srinivas 和 Igor Mordatch。
3.  **Depth Pro**（来自 Apple）发布了论文《[Depth Pro：不到一秒的清晰单目度量深度》，](https://arxiv.org/abs/2410.02073)作者是 Aleksei Bochkovskii、Amaël Delaunoy、Hugo Germain、Marcel Santos、Yichao Zhou、Stephan R. Richter 和 Vladlen Koltun。
4.  **Florence2**（来自微软）与论文[《Florence-2：为各种视觉任务提供统一的表示》一同](https://arxiv.org/abs/2311.06242)发布，论文作者为 Bin Xiao、Haiping Wu、Weijian Xu、Xiyang Dai、Houdong Hu、Yumao Lu、Michael Zeng、Ce Liu 和 Lu Yuan。
5.  **[Gemma](https://huggingface.co/docs/transformers/main/model_doc/gemma)**（来自 Google）发布了论文[《Gemma：基于 Gemini 技术和](https://blog.google/technology/developers/gemma-open-models/) Gemma Google 团队研究的开放模型》。
6.  **[Gemma2](https://huggingface.co/docs/transformers/main/model_doc/gemma2)** [（来自 Google）发布了 Gemma2：基于 Gemini 技术和研究的开放模型](https://blog.google/technology/developers/google-gemma-2/)论文。
7.  **[Granite](https://huggingface.co/docs/transformers/main/model_doc/granite)**（来自 IBM）发布了论文《 [Power Scheduler：与批次大小和令牌数量无关的学习率调度程序》，](https://arxiv.org/abs/2408.13359)作者为 Yikang Shen、Matthew Stallone、Mayank Mishra、Gaoyuan Zhang、Shawn Tan、Aditya Prasad、Adriana Meza Soria、David D. Cox 和 Rameswar Panda。
8.  **[GroupViT](https://huggingface.co/docs/transformers/model_doc/groupvit)** (from UCSD, NVIDIA) released with the paper [GroupViT: Semantic Segmentation Emerges from Text Supervision](https://arxiv.org/abs/2202.11094) by Jiarui Xu, Shalini De Mello, Sifei Liu, Wonmin Byeon, Thomas Breuel, Jan Kautz, Xiaolong Wang.
9.  **[Hiera](https://huggingface.co/docs/transformers/model_doc/hiera)** (from Meta) released with the paper [Hiera: A Hierarchical Vision Transformer without the Bells-and-Whistles](https://arxiv.org/pdf/2306.00989) by Chaitanya Ryali, Yuan-Ting Hu, Daniel Bolya, Chen Wei, Haoqi Fan, Po-Yao Huang, Vaibhav Aggarwal, Arkabandhu Chowdhury, Omid Poursaeed, Judy Hoffman, Jitendra Malik, Yanghao Li, Christoph Feichtenhofer.
10.  **JAIS** (from Core42) released with the paper [Jais and Jais-chat: Arabic-Centric Foundation and Instruction-Tuned Open Generative Large Language Models](https://arxiv.org/pdf/2308.16149) by Neha Sengupta, Sunil Kumar Sahu, Bokang Jia, Satheesh Katipomu, Haonan Li, Fajri Koto, William Marshall, Gurpreet Gosal, Cynthia Liu, Zhiming Chen, Osama Mohammed Afzal, Samta Kamboj, Onkar Pandit, Rahul Pal, Lalit Pradhan, Zain Muhammad Mujahid, Massa Baali, Xudong Han, Sondos Mahmoud Bsharat, Alham Fikri Aji, Zhiqiang Shen, Zhengzhong Liu, Natalia Vassilieva, Joel Hestness, Andy Hock, Andrew Feldman, Jonathan Lee, Andrew Jackson, Hector Xuguang Ren, Preslav Nakov, Timothy Baldwin, Eric Xing.
11.  **[LLaVa](https://huggingface.co/docs/transformers/model_doc/llava)** (from Microsoft Research & University of Wisconsin-Madison) released with the paper [Visual Instruction Tuning](https://arxiv.org/abs/2304.08485) by Haotian Liu, Chunyuan Li, Yuheng Li and Yong Jae Lee.
12.  **[MaskFormer](https://huggingface.co/docs/transformers/model_doc/maskformer)** (from Meta and UIUC) released with the paper [Per-Pixel Classification is Not All You Need for Semantic Segmentation](https://arxiv.org/abs/2107.06278) by Bowen Cheng, Alexander G. Schwing, Alexander Kirillov.
13.  **[MusicGen](https://huggingface.co/docs/transformers/model_doc/musicgen)** (from Meta) released with the paper [Simple and Controllable Music Generation](https://arxiv.org/abs/2306.05284) by Jade Copet, Felix Kreuk, Itai Gat, Tal Remez, David Kant, Gabriel Synnaeve, Yossi Adi and Alexandre Défossez.
14.  **MobileCLIP** (from Apple) released with the paper [MobileCLIP: Fast Image-Text Models through Multi-Modal Reinforced Training](https://arxiv.org/abs/2311.17049) by Pavan Kumar Anasosalu Vasu, Hadi Pouransari, Fartash Faghri, Raviteja Vemulapalli, Oncel Tuzel.
15.  **[MobileNetV1](https://huggingface.co/docs/transformers/model_doc/mobilenet_v1)** (from Google Inc.) released with the paper [MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications](https://arxiv.org/abs/1704.04861) by Andrew G. Howard, Menglong Zhu, Bo Chen, Dmitry Kalenichenko, Weijun Wang, Tobias Weyand, Marco Andreetto, Hartwig Adam.
16.  **[MobileNetV2](https://huggingface.co/docs/transformers/model_doc/mobilenet_v2)** (from Google Inc.) released with the paper [MobileNetV2: Inverted Residuals and Linear Bottlenecks](https://arxiv.org/abs/1801.04381) by Mark Sandler, Andrew Howard, Menglong Zhu, Andrey Zhmoginov, Liang-Chieh Chen.
17.  **MobileNetV3** (from Google Inc.) released with the paper [Searching for MobileNetV3](https://arxiv.org/abs/1905.02244) by Andrew Howard, Mark Sandler, Grace Chu, Liang-Chieh Chen, Bo Chen, Mingxing Tan, Weijun Wang, Yukun Zhu, Ruoming Pang, Vijay Vasudevan, Quoc V. Le, Hartwig Adam.
18.  **MobileNetV4** (from Google Inc.) released with the paper [MobileNetV4 - Universal Models for the Mobile Ecosystem](https://arxiv.org/abs/2404.10518) by Danfeng Qin, Chas Leichner, Manolis Delakis, Marco Fornoni, Shixin Luo, Fan Yang, Weijun Wang, Colby Banbury, Chengxi Ye, Berkin Akin, Vaibhav Aggarwal, Tenghui Zhu, Daniele Moro, Andrew Howard.
19.  **Moondream1** released in the repository [moondream](https://github.com/vikhyat/moondream) by vikhyat.
20.  **OpenELM** (from Apple) released with the paper [OpenELM: An Efficient Language Model Family with Open-source Training and Inference Framework](https://arxiv.org/abs/2404.14619) by Sachin Mehta, Mohammad Hossein Sekhavat, Qingqing Cao, Maxwell Horton, Yanzi Jin, Chenfan Sun, Iman Mirzadeh, Mahyar Najibi, Dmitry Belenko, Peter Zatloukal, Mohammad Rastegari.
21.  **[Phi3](https://huggingface.co/docs/transformers/main/model_doc/phi3)** (from Microsoft) released with the paper [Phi-3 Technical Report: A Highly Capable Language Model Locally on Your Phone](https://arxiv.org/abs/2404.14219) by Marah Abdin, Sam Ade Jacobs, Ammar Ahmad Awan, Jyoti Aneja, Ahmed Awadallah, Hany Awadalla, Nguyen Bach, Amit Bahree, Arash Bakhtiari, Harkirat Behl, Alon Benhaim, Misha Bilenko, Johan Bjorck, Sébastien Bubeck, Martin Cai, Caio César Teodoro Mendes, Weizhu Chen, Vishrav Chaudhary, Parul Chopra, Allie Del Giorno, Gustavo de Rosa, Matthew Dixon, Ronen Eldan, Dan Iter, Amit Garg, Abhishek Goswami, Suriya Gunasekar, Emman Haider, Junheng Hao, Russell J. Hewett, Jamie Huynh, Mojan Javaheripi, Xin Jin, Piero Kauffmann, Nikos Karampatziakis, Dongwoo Kim, Mahoud Khademi, Lev Kurilenko, James R. Lee, Yin Tat Lee, Yuanzhi Li, Chen Liang, Weishung Liu, Eric Lin, Zeqi Lin, Piyush Madan, Arindam Mitra, Hardik Modi, Anh Nguyen, Brandon Norick, Barun Patra, Daniel Perez-Becker, Thomas Portet, Reid Pryzant, Heyang Qin, Marko Radmilac, Corby Rosset, Sambudha Roy, Olatunji Ruwase, Olli Saarikivi, Amin Saied, Adil Salim, Michael Santacroce, Shital Shah, Ning Shang, Hiteshi Sharma, Xia Song, Masahiro Tanaka, Xin Wang, Rachel Ward, Guanhua Wang, Philipp Witte, Michael Wyatt, Can Xu, Jiahang Xu, Sonali Yadav, Fan Yang, Ziyi Yang, Donghan Yu, Chengruidong Zhang, Cyril Zhang, Jianwen Zhang, Li Lyna Zhang, Yi Zhang, Yue Zhang, Yunan Zhang, Xiren Zhou.
22.  **[PVT](https://huggingface.co/docs/transformers/main/model_doc/pvt)** (from Nanjing University, The University of Hong Kong etc.) released with the paper [Pyramid Vision Transformer: A Versatile Backbone for Dense Prediction without Convolutions](https://arxiv.org/pdf/2102.12122.pdf) by Wenhai Wang, Enze Xie, Xiang Li, Deng-Ping Fan, Kaitao Song, Ding Liang, Tong Lu, Ping Luo, Ling Shao.
23.  **PyAnnote** released in the repository [pyannote/pyannote-audio](https://github.com/pyannote/pyannote-audio) by Hervé Bredin.
24.  **[RT-DETR](https://huggingface.co/docs/transformers/model_doc/rt_detr)** （来自百度），与赵一安、吕文宇、徐尚亮、魏金曼、王管中、党青青、刘毅、陈杰的论文[《DETRs Beat YOLOs on Real-time ObjectDetection》一起发布。](https://arxiv.org/abs/2304.08069)
25.  **Sapiens**（来自 Meta AI）与论文[《Sapiens：人类视觉模型基础》](https://arxiv.org/pdf/2408.12569)一起发布，论文作者是 Rawal Khirodkar、Timur Bagautdinov、Julieta Martinez、Su Zhaoen、Austin James、Peter Selednik、Stuart Anderson 和 Shunsuke Saito。
26.  **[ViTMAE](https://huggingface.co/docs/transformers/model_doc/vit_mae)** （来自 Meta AI）与 Kaiming He、Xinlei Chen、Saining Xie、Yanghao Li、Piotr Dollár 和 Ross Girshick 撰写的论文[《Masked Autoencoders Are Scalable Vision Learners》一起发布。](https://arxiv.org/abs/2111.06377)
27.  **[ViTMSN](https://huggingface.co/docs/transformers/model_doc/vit_msn)** （来自 Meta AI）与 Mahmoud Assran、Mathilde Caron、Ishan Misra、Piotr Bojanowski、Florian Bordes、Pascal Vincent、Armand Joulin、Michael Rabbat 和 Nicolas Ballas 撰写的论文[《用于标签高效学习的蒙面暹罗网络》一起发布。](https://arxiv.org/abs/2204.07141)

[](#example-projects-and-templates)示例项目和模板
------------------------------------------

作为发布的一部分，我们发布了 25 个新的示例项目和模板，主要侧重于展示 WebGPU 支持！其中包括 [Phi-3.5 WebGPU](https://github.com/huggingface/transformers.js-examples/tree/main/phi-3.5-webgpu) 和 [Whisper WebGPU](https://github.com/xenova/whisper-web/tree/experimental-webgpu) 等演示，如下所示。

> 我们正在将所有示例项目和演示移至 [https://github.com/huggingface/transformers.js-examples](https://github.com/huggingface/transformers.js-examples)，敬请关注最新更新！

<table><thead><tr><th align="center"><img class="" src="https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/transformersjs-v3/phi-3.5-webgpu.gif" style="width: auto;"></th><th align="center"><img class="" src="https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/transformersjs-v3/whisper-turbo-webgpu.gif" style="width: auto;"></th></tr></thead></table>

[](#over-1200-pre-converted-models)超过 1200 个预转换模型
-------------------------------------------------

今天发布，社区已将超过 1200 个模型转换为与 Transformers.js 兼容！您可以[在此处](https://hf.co/models?library=transformers.js)找到可用模型的完整列表。

如果您想转换自己的模型或进行一些配置，您可以使用我们的[转换脚本](https://github.com/huggingface/transformers.js/blob/main/scripts/convert.py)，如下所示：

```
python  -m 脚本.convert  --quantize  --model_id <模型名称或路径>
```

将生成的文件上传到Huging Face Hub后，请记住添加标签，`transformers.js`以便其他人可以轻松找到并使用您的模型！

 ![](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/transformersjs-v3/models-dark.jpg) 

[](#nodejs-esm--cjs-deno-and-bun-compatibility)Node.js (ESM + CJS)、Deno 和 Bun 兼容性
---------------------------------------------------------------------------------

Transformers.js v3 现在与清晰最流行的服务器端 JavaScript 运行时兼容：

<table><thead><tr><th>脚本</th><th>描述</th><th>示例</th></tr></thead><tbody><tr><td><a href="https://nodejs.org/">Node.js</a></td><td>基于 Chrome V8 构建的广泛使用的 JavaScript 运行时。它拥有庞大的生态系统并支持各种库和框架。</td><td><a href="https://github.com/huggingface/transformers.js-examples/tree/main/node-esm">ESM 示例</a>/ <a href="https://github.com/huggingface/transformers.js-examples/tree/main/node-cjs">CJS 示例</a></td></tr><tr><td><a href="https://deno.com/">德諾</a></td><td>JavaScript 和 TypeScript 的现代运行时，默认情况下是安全的。它使用 ES 模块，甚至具有实验性的 WebGPU 支持。</td><td><a href="https://github.com/huggingface/transformers.js-examples/tree/main/deno-embed">Deno 示例</a></td></tr><tr><td><a href="https://bun.sh/">包子</a></td><td>针对性能进行了快速JavaScript运行时的优化。它具有内置的捆绑器、转译器和包管理器。</td><td><a href="https://github.com/huggingface/transformers.js-examples/tree/main/bun">面包示例</a></td></tr></tbody></table>

[](#a-new-home-on-npm-and-github)NPM 和 GitHub 上的新主页
---------------------------------------------------

最后，我们很高兴地宣布，Transformers.js 现在将在 NPM 上以官方 Hugging Face 组织的名称发布（而不是 用于 v1 和 v2 的）。[`@huggingface/transformers`](https://www.npmjs.com/package/@huggingface/transformers)[`@xenova/transformers`](https://www.npmjs.com/package/@xenova/transformers)

我们转型存储库迁移至 GitHub 上的官方 Hugging Face 组织 ( [https://github.com/huggingface/transformers.js](https://github.com/huggingface/transformers.js) )，这将成为我们的新家 — 快来打个招呼吧！我们期待收到您的反馈、回复您的问题并审核您的公关！

这是一个重要的里程碑，我们非常感谢社区帮助我们实现这个长期目标！如果没有你们的所有权，这一切都不可能实现……谢谢！🤗