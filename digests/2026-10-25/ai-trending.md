# AI 开源趋势日报 2026-10-25

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-10-25 00:29 UTC

---

# AI 开源趋势报告 — 2026-10-25

*仅应用 AI/ML 过滤；Star 数按所提供快照原样复制。*

## 1. 今日亮点

今天最强的 AI 信号来自智能体工具：[cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) 新增 2,264 个 Star，而 [NVIDIA/OpenShell](https://github.com/NVIDIA/OpenShell) 新增 896 个，这表明市场对可审计、沙箱化的编码智能体工作流存在需求。智能体运行框架的设计与质量控制相关项目同样热度上升，[pbakaus/impeccable](https://github.com/pbakaus/impeccable)（+619）和 [Nutlope/hallmark](https://github.com/Nutlope/hallmark)（+455）均呈上升趋势。开箱即用的智能体依然受欢迎：[makecindy/cindy](https://github.com/makecindy/cindy)（+477）以及终端工作流中的 [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) 保持热度。在检索侧，[firecrawl/firecrawl](https://github.com/firecrawl/firecrawl)（195k）和 [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify)（134k）等高 Star 存量项目表明，网页摄取和基于图的上下文仍在扩张。

## 2. 按类别划分的热门项目

### 🔧 AI 基础设施

| 项目 | 语言 | Star 数（总计 / 今日） | 概要 |
| :--- | :--- | ---: | :--- |
| [NVIDIA/OpenShell](https://github.com/NVIDIA/OpenShell) | Rust | 0 (+896) | OpenShell 是面向自主 AI 智能体的安全、私密运行时。今天新增 896 个 Star，强烈表明沙箱化智能体执行正成为优先事项。 |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 184,660 | 用于 Kimi、GLM、MiniMax、DeepSeek、gpt-oss、Qwen、Gemma 等模型的本地运行时。其 184,660 个 Star 使其成为开放模型实验的默认本地推理层。 |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,214 | 面向智能体和生成式 UI 的前端技术栈，覆盖 React、Angular、移动端、Slack 和 AG-UI Protocol。其重要性在于，智能体 UX 正从聊天窗口转向嵌入式生成式界面。 |
| [aws/agent-toolkit-for-aws](https://github.com/aws/agent-toolkit-for-aws) | Python | 0 (+13) | AWS 官方支持的 MCP 服务器、技能和插件，用于在 AWS 上构建 AI 智能体。虽然今天仅新增 13 个 Star，但随着云厂商推动智能体集成标准化，该项目具有战略重要性。 |
| [langchain4j/langchain4j](https://github.com/langchain4j/langchain4j) | Java | 13,290 | 面向 JVM 上 LLM 驱动应用的惯用 Java 库，支持工具调用、MCP、智能体和 RAG。其 13,290 个 Star 表明企业 Java 团队正在采用智能体和 RAG 模式。 |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 8,964 | 用于模块化、可扩展 LLM 应用的 Rust 框架。它的出现表明，Rust 在生产级 LLM 服务中的采用率正在增长。 |
| [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | Rust | 0 (+619) | 一种设计语言，旨在让 AI 运行框架更擅长设计。今天新增 619 个 Star，表明质量和设计护栏正成为智能体的一等关注点。 |
| [Nutlope/hallmark](https://github.com/Nutlope/hallmark) | CSS | 0 (+455) | 面向主流编码智能体的反 AI 垃圾设计技能。今天新增 455 个 Star，表明用户希望为智能体生成的输出提供有明确主张的设计控制。 |

### 🤖 AI 智能体 / 工作流

| 项目 | 语言 | Star 数（总计 / 今日） | 概要 |
| :--- | :--- | ---: | :--- |
| [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) | JavaScript | 0 (+2,264) | 一种编码智能体技能，用于多阶段安全审计，并给出可独立验证、机器可读的发现。它是今天新增 Star 最多的 AI 趋势仓库，表明安全审计正成为爆发式增长的智能体用例。 |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 288,087 | 智能体运行框架性能优化系统，覆盖技能、本能、记忆、安全以及研究优先的开发方式，适用于主流编码智能体。其 288,087 个总 Star 使其成为快照中最受关注的运行框架优化项目之一。 |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 256,814 | “与你共同成长的智能体”，定位为可适应的个人智能体行为。凭借 256,814 个总 Star，它仍是通用智能体架构的高关注度参考点。 |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Python | 119,061 | 用于 AI 浏览器智能体的开源 Python 库，并提供具备隐身浏览器和托管智能体 API 的云服务。其 119,061 个 Star 反映出对网页原生智能体执行的持续需求。 |
| [Panniantong/Agent-Reach](https://github.com/Panniantong/Agent-Reach) | Python | 101,763 | 让 AI 智能体无需 API 费用即可读取/搜索 Twitter、Reddit、YouTube、GitHub、Bilibili 和小红书的 CLI。其 101,763 个 Star 凸显了智能体低摩擦访问多源数据的需求。 |
| [makecindy/cindy](https://github.com/makecindy/cindy) | TypeScript | 0 (+477) | 开箱即用的开源 AI 智能体。今天新增 477 个 Star，表明对开箱即用智能体体验的兴趣仍在延续。 |
| [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) | TypeScript | 0 (+44) | 将 Gemini 带入终端的开源 AI 智能体。它正登上趋势榜，但今天仅新增 44 个 Star，说明这是既有兴趣而非突然爆发。 |

### 📦 AI 应用

| 项目 | 语言 | Star 数（总计 / 今日） | 概要 |
| :--- | :--- | ---: | :--- |
| [morluto/rea](https://github.com/morluto/rea) | TypeScript | 378,728 | 用智能体逆向工程任何东西，从应用行为一直到原生二进制文件。其 378,728 个 Star 使智能体化逆向工程成为快照中最大的应用类别之一。 |
| [open-webui/open-webui](https://github.com/open-webui/open-webui) | Python | 156,656 | 用户友好的 AI 界面，支持 Ollama、OpenAI API 等。它仍是本地和托管模型的领先自托管前端。 |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 134,869 | 基于主题或关键词，通过自动化 AI 工作流生成高清短视频。其 134,869 个 Star 反映出对开箱即用生成式媒体流水线的强劲需求。 |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 77,025 | 开源 AI 求职智能体，可扫描招聘网站、根据简历给职位打分，并定制简历和求职信。其 77,025 个 Star 表明带有提交环节人工介入的垂直智能体工作流受到关注。 |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 67,425 | AI 将文档或主题转化为原生 PowerPoint 演示文稿，包含形状、切换、图表、旁白和模板。其 67,425 个 Star 凸显办公文档生成是实用的智能体用例。 |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 67,003 | LLM 驱动的多市场股票分析系统，提供实时新闻、仪表盘和定时通知。其 67,003 个 Star 表明对金融垂直智能体应用的强烈兴趣。 |
| [CherryHQ/cherry-studio](https://github.com/CherryHQ/cherry-studio) | TypeScript | 52,952 | AI 生产力工作室，具备智能聊天、自主智能体和 300+ 助手。其 52,952 个 Star 表明对统一的多模型桌面生产力中心存在需求。 |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 42,531 | 终身个性化辅导项目。其 42,531 个 Star 反映出对 AI 教育应用的持续兴趣。 |

### 🧠 LLM / 训练

| 项目 | 语言 | Star 数（总计 / 今日） | 概要 |
| :--- | :--- | ---: | :--- |
| [tensorflow/tensorflow](https://github.com/tensorflow/tensorflow) | C++ | 201,230 | 面向所有人的开源机器学习框架。尽管出现了更新的 LLM 专用技术栈，其 201,230 个 Star 仍体现出持久的基础设施引力。 |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,099 | 面向最先进文本、视觉、音频和多模态模型的模型定义框架。它仍是开放模型实现和微调的中心枢纽。 |
| [pytorch/pytorch](https://github.com/pytorch/pytorch) | Python | 105,047 | 提供张量和动态神经网络，并具备强大 GPU 加速。它仍是研究到生产训练的基础底座。 |
| [scikit-learn/scikit-learn](https://github.com/scikit-learn/scikit-learn) | Python | 67,897 | Python 中的经典机器学习库。其 67,897 个 Star 表明，在以 LLM 为中心的工作之外，实用 ML 流水线和基线仍具有持续相关性。 |
| [keras-team/keras](https://github.com/keras-team/keras) | Python | 64,385 | 面向人类的深度学习 API。其 64,385 个 Star 表明对高层模型构建的持续需求。 |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,542 | LLM 评估平台，覆盖 OpenAI、Anthropic、Gemini、Qwen、GLM、DeepSeek 以及 100+ 数据集。随着模型选择增多，评估正变得越来越关键。 |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 4,788 | 在 Apple Silicon 上通过构建一个微型 vLLM + Qwen 来学习 LLM 推理系统。它反映出系统工程师正通过动手实现进入 LLM 基础设施领域。 |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 317 | 由 X-bit 量化驱动的端侧 LLM 推理。其较低的 Star 数指向一个早期但重要的边缘/隐私推理方向。 |

### 🔍 RAG / 知识

| 项目 | 语言 | Star 数（总计 / 今日） | 概要 |
| :--- | :--- | ---: | :--- |
| [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl) | TypeScript | 195,018 | 通过将网站转化为 LLM 就绪的内容，用网页数据增强 AI 智能体。其 195,018 个 Star 使其成为 RAG 和智能体领域信号最强的网页摄取项目之一。 |
| [langchain-ai/langchain](https://github.com/langchain-ai/langchain) | Python | 148,392 | 智能体工程平台，拥有广泛的 RAG 和检索生态。其 148,392 个 Star 表明它仍是 LLM 应用开发的核心入口。 |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 134,468 | 将代码库、文档、SQL schema、配置和 PDF 转化为可查询的知识图谱，无需向量存储。其 134,468 个 Star 凸显基于图、可解释的检索正成为上升中的替代方案。 |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 102,825 | 为每个智能体提供跨会话持久上下文，压缩会话历史并将相关上下文重新注入未来会话。其 102,825 个 Star 表明记忆和上下文是与 RAG 相邻的核心层。 |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 92,813 | 开源 RAG 引擎，将检索与智能体能力融合，为 LLM 创建上下文层。其 92,813 个 Star 印证了生产级 RAG 需求。 |
| [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) | Python | 86,188 | 面向 LLM 和智能体的开源网页爬虫与抓取器，可输出干净的 Markdown。其 86,188 个 Star 表明数据获取仍是主要瓶颈。 |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 67,751 | AI 智能体的记忆层，提供即插即用的持久上下文基础设施。其 67,751 个 Star 反映出对智能体记忆的生产级兴趣。 |
| [milvus-io/milvus](https://github.com/milvus-io/milvus) | Go | 46,469 | 高性能云原生向量数据库，用于可扩展的 ANN 搜索。其 46,469 个 Star 表明向量数据库仍是 RAG 的基础。 |

## 3. 趋势信号分析

最清晰的信号是，AI 智能体正从演示框架转向运营工具。当日榜首 [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) 不是模型或聊天机器人，而是用于独立验证安全审计的智能体技能；[NVIDIA/OpenShell](https://github.com/NVIDIA/OpenShell) 提供安全运行时，而 [pbakaus/impeccable](https://github.com/pbakaus/impeccable) 和 [Nutlope/hallmark](https://github.com/Nutlope/hallmark) 则瞄准设计质量。这表明社区正在投资编码智能体周边的护栏——安全、运行时隔离、设计品味和可复用技能——而不仅仅是模型访问。第二个信号是 RAG 向上下文的转变。[Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) 推广无需向量存储的知识图谱，[thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) 和 [mem0ai/mem0](https://github.com/mem0ai/mem0) 打包持久记忆，而 [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl) 与 [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) 则聚焦网页到 LLM 的摄取。检索正被重新定义为持久上下文工程。从技术栈看，Rust 出现在智能体运行时和终端智能体中，TypeScript 主导智能体 UX/CLI 界面，Java 正通过 [langchain4j/langchain4j](https://github.com/langchain4j/langchain4j) 被拉入企业 LLM 工作。MCP 和提供商无关集成也体现在 [aws/agent-toolkit-for-aws](https://github.com/aws/agent-toolkit-for-aws) 和 [ollama/ollama](https://github.com/ollama/ollama) 的多模型支持中。在应用侧，[career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) 和 [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) 等高 Star 垂直智能体表明，窄领域、工作流特定的智能体比通用助手更容易被采用。评估和本地推理项目——[open-compass/opencompass](https://github.com/open-compass/opencompass) 和 [ollama/ollama](https://github.com/ollama/ollama)——补全了整个技术栈，表明团队既想要模型选择，也想要可衡量的质量。这份快照读起来像是模型发布后的生态成熟期：多模型路由、评估、记忆和安全是新的差异化要点，而不仅仅是原始模型访问。

## 4. 社区热点

- **智能体安全与运行时隔离** — [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill)（今日 +2,264）和 [NVIDIA/OpenShell](https://github.com/NVIDIA/OpenShell)（+896）是当天最强的信号，表明可审计、沙箱化的智能体执行正成为一个产品类别。
- **编码智能体的设计与质量技能** — [pbakaus/impeccable](https://github.com/pbakaus/impeccable)（+619）和 [Nutlope/hallmark](https://github.com/Nutlope/hallmark)（+455）表明编码智能体工作流对有明确主张的设计护栏存在需求。
- **持久记忆与图上下文** — [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)（102,825）、[mem0ai/mem0](https://github.com/mem0ai/mem0)（67,751）和 [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify)（134,468）指向记忆和知识图谱作为下一层 RAG。
- **网页到 LLM 的数据流水线** — [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl)（195,018）和 [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai)（86,188）仍是高信号项目，因为干净的网页摄取仍是智能体和 RAG 的瓶颈。
- **云与企业智能体集成** — 随着 MCP、Java 和提供商无关工具将智能体带入企业技术栈，[aws/agent-toolkit-for-aws](https://github.com/aws/agent-toolkit-for-aws) 和 [langchain4j/langchain4j](https://github.com/langchain4j/langchain4j) 值得关注。

---

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*