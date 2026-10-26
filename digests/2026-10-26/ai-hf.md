# Hugging Face 热门模型周报 2026-10-26

> 数据来源: [Hugging Face Hub](https://huggingface.co/) | 共 30 个模型 | 生成时间: 2026-10-26 00:37 UTC

---

## 今日亮点

Qwen/Qwen3.8-27B 以 18,394 个点赞和 7,762,014 次下载主导本周信号，使官方 Qwen 多模态大语言模型持续处于关注中心。视频生成是另一个爆发点：Lightricks/LTX-2.5 获得 9,335 个点赞和 2,107,108 次下载，而可直接用于 ComfyUI 的 Flux-3 Klein 和 MOSS-Image 等图像模型发布，继续将社区兴趣转化为下载量。DeepSeek-V4.1-Flash 的下载量不如 Qwen，但催生了一大批 GGUF、FP8、abliterated 和无审查衍生模型，使其成为本周生态系统放大效应最强的基座模型。SAM3、EmbeddingGemma 2、OCR、时间序列和 DNA 模型等专用发布，为这一周画上句号；本周的互动热度由开放权重成果和本地部署格式驱动。

## 热门模型

### 🧠 语言模型（LLM、聊天模型、指令微调）

| 模型 | 作者 | 点赞 | 下载量 | 简介 |
| :--- | :--- | ---: | ---: | :--- |
| [Qwen/Qwen3.8-27B](https://huggingface.co/Qwen/Qwen3.8-27B) | Qwen | 18,394 | 7,762,014 | Qwen 的 27B 图文转文本对话模型是本摘要中的明显领先者，获得 18,394 个点赞和 7,762,014 次下载。其广泛的多模态文本生成定位以及 Qwen 官方支持，解释了它为何能获得榜首互动量。 |
| [deepseek-ai/DeepSeek-V4.1-Flash-1017](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash-1017) | deepseek-ai | 2,982 | 85,275 | DeepSeek V4.1 Flash 官方发布，面向图文转文本和文本生成工作负载。它以 2,982 个点赞强劲上榜，并在生态中锚定了一批 GGUF、FP8 和 abliterated 衍生模型。 |
| [RecursiveSI/flint](https://huggingface.co/RecursiveSI/flint) | RecursiveSI | 858 | 6,444 | 一个带有 flint 标签、面向聊天式用途的对话文本生成模型。它在仅 6,444 次下载的情况下获得 858 个点赞，表明早期兴趣异常高，并且已经催生出 unsloth GGUF 变体。 |
| [XHToken/Spark-X2.6-4B](https://huggingface.co/XHToken/Spark-X2.6-4B) | XHToken | 495 | 3,488 | XHToken 的紧凑型 4B Spark-X2.6 文本生成模型面向实用的本地 LLM 使用场景。凭借 495 个点赞和 3,488 次下载，它在一份由更大规模和多模态系统主导的摘要中，作为小模型发布而显得突出。 |
| [Nanbeige/Nanbeige4.3-33B](https://huggingface.co/Nanbeige/Nanbeige4.3-33B) | Nanbeige | 365 | 0 | Nanbeige 推出的 33B、基于 Llama 的文本生成 LLM。其 365 个点赞与列表中 0 次下载并存，表明这是一个仍处于发现阶段的全新发布。 |
| [MiniMaxAI/MiniMax-M3.1-Flash](https://huggingface.co/MiniMaxAI/MiniMax-M3.1-Flash) | MiniMaxAI | 352 | 7,355 | MiniMax 的 Flash 品牌多模态模型在其 minimax_m3_vl 系列下处理图文转文本生成。它以 352 个点赞和 7,355 次下载上榜，反映出人们对高效多模态聊天模型的兴趣。 |
| [InternScience/Sage-A1.5](https://huggingface.co/InternScience/Sage-A1.5) | InternScience | 289 | 3,875 | InternScience 的 Sage-A1.5 是一个基于 Qwen3.5-MoE、带有文本生成和图文转文本标签的模型。它获得 289 个点赞和 3,875 次下载，表明人们对由 MoE 支持的高效推理模型存在小众兴趣。 |
| [KRAFTON/Raon-1-mini-preview](https://huggingface.co/KRAFTON/Raon-1-mini-preview) | KRAFTON | 186 | 1,177 | KRAFTON 的 Raon-1-mini-preview 是 Raon 系列中的小型文本生成预览模型。它有 186 个点赞和 1,177 次下载，使其成为一个早期信号型发布，而非高下载量领先者。 |
| [ornith-ai/Ornith-1.5-122B](https://huggingface.co/ornith-ai/Ornith-1.5-122B) | ornith-ai | 177 | 48,477 | Ornith-1.5-122B 是一个大型、基于 Qwen3.5-MoE 的文本生成模型，并带有图文转文本标签。尽管只有 177 个点赞，它却有 48,477 次下载，表明实际试用使用量超过了公开互动量。 |

### 🎨 多模态与生成（图像、视频、音频、文本到 X）

| 模型 | 作者 | 点赞 | 下载量 | 简介 |
| :--- | :--- | ---: | ---: | :--- |
| [Lightricks/LTX-2.5](https://huggingface.co/Lightricks/LTX-2.5) | Lightricks | 9,335 | 2,107,108 | Lightricks 的 LTX-2.5 是一个单文件扩散模型，支持图生视频、文生视频和视频生视频工作流。它以 9,335 个点赞和 2,107,108 次下载领跑生成组，凸显出对易用视频生成的强劲需求。 |
| [black-forest-labs/flux-3-image-klein-comfy](https://huggingface.co/black-forest-labs/flux-3-image-klein-comfy) | black-forest-labs | 1,603 | 660,874 | Black Forest Labs 的 Flux 3 Klein 包是一个可直接用于 ComfyUI 的单文件扩散模型，用于图像生成和编辑。其 1,603 个点赞和 660,874 次下载表明，对原生工作流图像模型的需求持续存在。 |
| [fishaudio/drama-3](https://huggingface.co/fishaudio/drama-3) | fishaudio | 1,012 | 21,375 | fishaudio 的 drama-3 是一个基于 fish_omni 技术栈构建的多说话人文本转语音模型。它作为本摘要中的音频生成条目而突出，获得 1,012 个点赞和 21,375 次下载。 |
| [OpenMOSS-Team/MOSS-Image-SFT](https://huggingface.co/OpenMOSS-Team/MOSS-Image-SFT) | OpenMOSS-Team | 727 | 10,757 | OpenMOSS-Team 的 MOSS-Image-SFT 是一个 diffusers 文生图模型，针对有监督图像生成进行了调优。作为更广泛的 MOSS-Image 系列的一部分，它吸引了 727 个点赞和 10,757 次下载。 |
| [OpenMOSS-Team/MOSS-Image-Base](https://huggingface.co/OpenMOSS-Team/MOSS-Image-Base) | OpenMOSS-Team | 554 | 7,641 | 基础 MOSS-Image 模型为该系列的 SFT 和社区衍生模型提供了 diffusers 文生图基础。它有 554 个点赞和 7,641 次下载，下游的 ComfyUI 打包进一步放大了其触达范围。 |
| [Comfy-Org/MOSS-Image](https://huggingface.co/Comfy-Org/MOSS-Image) | Comfy-Org | 451 | 2,161,961 | Comfy-Org 将 MOSS-Image 基础模型打包为单文件 ComfyUI 扩散格式。其 451 个点赞和 2,161,961 次下载凸显出，大量采用是通过可直接用于 ComfyUI 的重新打包实现的。 |
| [Comfy-Org/Fibo-2.0](https://huggingface.co/Comfy-Org/Fibo-2.0) | Comfy-Org | 178 | 124,557 | Comfy-Org 的 Fibo-2.0 是一个单文件 ComfyUI 扩散模型发布，派生自 briaai 的 Fibo-2.0-dev。它有 178 个点赞和 124,557 次下载，表明人们对精选 ComfyUI 模型包保持着稳定兴趣。 |

### 🔧 专用模型（代码、数学、医疗、嵌入）

| 模型 | 作者 | 点赞 | 下载量 | 简介 |
| :--- | :--- | ---: | ---: | :--- |
| [facebook/sam3](https://huggingface.co/facebook/sam3) | facebook | 5,108 | 1,849,059 | Facebook 的 SAM3 是一个面向分割工作流的掩码生成和特征提取基础模型。它以 5,108 个点赞和 1,849,059 次下载成为此处的专用模型领先者，反映出对视觉分割工具的强劲需求。 |
| [google/embeddinggemma-2](https://huggingface.co/google/embeddinggemma-2) | google | 2,921 | 212,769 | Google 的 EmbeddingGemma 2 是一个用于嵌入的特征提取模型。它获得 2,921 个点赞和 212,769 次下载，使其成为本摘要中最明确的以嵌入为核心的发布。 |
| [datalab-to/indra-ocr](https://huggingface.co/datalab-to/indra-ocr) | datalab-to | 425 | 4,636 | datalab-to 的 indra-ocr 是一个 ONNX/safetensors OCR 模型，专为图像到文本提取而设计，包括 CPU 部署。它有 425 个点赞和 4,636 次下载，表明对文档和 OCR 流水线存在实际需求。 |
| [mistralai/Timestral-209M](https://huggingface.co/mistralai/Timestral-209M) | mistralai | 367 | 9,765 | Mistral AI 的 Timestral-209M 是一个紧凑的时间序列预测基础模型。凭借 367 个点赞和 9,765 次下载，它表明人们对非文本专用基础模型的持续兴趣。 |
| [orcarouter/OrcaCyber-Nano-1.0](https://huggingface.co/orcarouter/OrcaCyber-Nano-1.0) | orcarouter | 254 | 30,330 | OrcaCyber-Nano-1.0 是一个 GGUF 网络安全视觉语言模型，面向安全和逆向工程任务。它有 254 个点赞和 30,330 次下载，表明从业者存在小众但活跃的兴趣。 |
| [HuggingFaceBio/Carbon-X](https://huggingface.co/HuggingFaceBio/Carbon-X) | HuggingFaceBio | 183 | 807 | HuggingFaceBio 的 Carbon-X 是一个基于 Qwen3 的文本生成模型，带有 DNA 工作标签。它有 183 个点赞和 807 次下载，代表了热门集合中对生物序列建模的早期探索。 |

### 📦 微调与量化（社区微调、GGUF、AWQ）

| 模型 | 作者 | 点赞 | 下载量 | 简介 |
| :--- | :--- | ---: | ---: | :--- |
| [abenzerps/Qwen-Image-2.1-Uncensored-Quantized](https://huggingface.co/abenzerps/Qwen-Image-2.1-Uncensored-Quantized) | abenzerps | 5,040 | 2,235,846 | 一个面向无审查图像生成的社区 GGUF/ComfyUI Qwen-Image 2.1 量化版本。它以 5,040 个点赞和 2,235,846 次下载成为微调/量化类的榜首条目，显示出对本地图像工作流的巨大需求。 |
| [jialinyyzz/humanizer](https://huggingface.co/jialinyyzz/humanizer) | jialinyyzz | 2,154 | 143,152 | jialinyyzz/humanizer 是一个社区 Gemma4-unified 发布，同时以 GGUF 和 safetensors 格式打包，用于图文转文本。它有 2,154 个点赞和 143,152 次下载，使其成为一个值得注意的、以人性化为重点的衍生模型。 |
| [DavidAU/Qwen3.8-27B-TURBO-Fable-Cold-Fusion-735-882-Heretic-Uncensored-NEO-CODER-MAX-MTP-GGUF](https://huggingface.co/DavidAU/Qwen3.8-27B-TURBO-Fable-Cold-Fusion-735-882-Heretic-Uncensored-NEO-CODER-MAX-MTP-GGUF) | DavidAU | 2,091 | 1,671,015 | DavidAU 那个名字很长的 Qwen3.8-27B GGUF 是一个经过大量社区调优、无审查且面向代码的变体。它吸引了 2,091 个点赞和 1,671,015 次下载，说明人们对高度定制化本地 LLM 有强烈需求。 |
| [dealignai/DeepSeek-V4.1-Flash-1017-UNCENSORED-FP8](https://huggingface.co/dealignai/DeepSeek-V4.1-Flash-1017-UNCENSORED-FP8) | dealignai | 365 | 6,792 | dealignai 的无审查 FP8 发布将 DeepSeek-V4.1-Flash 适配用于图文转文本。它有 365 个点赞和 6,792 次下载，表明社区对新基座模型的量化无审查变体持续感兴趣。 |
| [ISTA-DASLab/DeepSeek-V4.1-Flash-1017-GSQ-RCO-GGUF](https://huggingface.co/ISTA-DASLab/DeepSeek-V4.1-Flash-1017-GSQ-RCO-GGUF) | ISTA-DASLab | 280 | 412,392 | ISTA-DASLab 的 GSQ-RCO GGUF 是 DeepSeek-V4.1-Flash 的混合精度量化版本。它有 280 个点赞，却有 412,392 次下载，这强烈表明量化本地版本的使用量可能远远超过其点赞数。 |
| [lodestones/Kromoss](https://huggingface.co/lodestones/Kromoss) | lodestones | 260 | 23,972 | lodestones/Kromoss 是一个与 MOSS-Image 系列相关的社区文生图 diffusers 模型。它有 260 个点赞和 23,972 次下载，反映出围绕 MOSS-Image 开放发布的衍生创作活动。 |
| [unsloth/flint-GGUF](https://huggingface.co/unsloth/flint-GGUF) | unsloth | 248 | 103,369 | unsloth/flint-GGUF 将 RecursiveSI 的 flint 模型打包为优化后的 GGUF，用于本地文本生成。它有 248 个点赞和 103,369 次下载，展示了量化衍生模型能多快推动采用。 |
| [orcarouter/DeepSeek-V4.1-Flash-1017-abliterated-GGUF](https://huggingface.co/orcarouter/DeepSeek-V4.1-Flash-1017-abliterated-GGUF) | orcarouter | 216 | 7,293 | orcarouter 的 abliterated GGUF 重新打包了 DeepSeek-V4.1-Flash，供本地 llama.cpp 使用，并修改了安全对齐。它有 216 个点赞和 7,293 次下载，属于 DeepSeek Flash 发布当天衍生模型浪潮的一部分。 |

## 生态信号

本周的格局由系列级势能而非孤立发布塑造。Qwen 是这一集合中最强的官方品牌，Qwen3.8-27B 在点赞和下载量上领先，并吸引了下游 GGUF 变体；DeepSeek-V4.1-Flash 则呈现出不同模式：基座模型的点赞量不高，但量化、FP8、abliterated 和无审查衍生模型形成了广泛的长尾。MOSS-Image 和 Flux/LTX 在生成领域指向同样的动态：ComfyUI 单文件打包和社区微调是主要采用渠道，有时下载量甚至超过原始仓库。开放权重生态在这份热门榜单上仍占主导；专有式托管访问并非主要信号。量化尤为显眼：来自 unsloth、ISTA-DASLab、DavidAU 和 orcarouter 的 GGUF 发布表明，用户优先考虑本地、内存高效的部署。与此同时，面向嵌入、分割、OCR、时间序列、网络安全和 DNA 的专用基础模型表明，Hugging Face 的关注范围正在超越聊天 LLM。

## 值得探索

- [Qwen/Qwen3.8-27B](https://huggingface.co/Qwen/Qwen3.8-27B)：凭借点赞和下载量成为本周明显领先者，是研究当前官方多模态 LLM 定位和社区衍生创作活动的最佳默认样本。
- [Lightricks/LTX-2.5](https://huggingface.co/Lightricks/LTX-2.5)：本摘要中最强的视频生成信号，具有多种视频到视频风格工作流，下载量超过 210 万次。
- [ISTA-DASLab/DeepSeek-V4.1-Flash-1017-GSQ-RCO-GGUF](https://huggingface.co/ISTA-DASLab/DeepSeek-V4.1-Flash-1017-GSQ-RCO-GGUF)：值得作为量化案例研究：280 个点赞对 412,392 次下载，表明本地 GGUF 部署可以带来远超公开互动所暗示的使用量。

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*