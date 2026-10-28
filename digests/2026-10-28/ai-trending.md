# AI 开源趋势日报 2026-10-28

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-10-28 01:35 UTC

---

范围：仅包含与 AI/ML 相关的仓库；通用工具、非 AI 课程列表以及无关工具均已排除。对于仅出现在趋势榜中的仓库，信息流将总 Star 数报告为 0；今日新增 Star 按原文照录。

## 1. 今日亮点

Agent harness 与技能层项目主导了今日新增 Star 势头：[Nutlope/hallmark](https://github.com/Nutlope/hallmark)（+2,830）和 [pbakaus/impeccable](https://github.com/pbakaus/impeccable)（+1,449）面向编码 agent 的设计质量，而 [makecindy/cindy](https://github.com/makecindy/cindy)（+1,465）和 [nightly-labs/openbot](https://github.com/nightly-labs/openbot)（+777）推动开箱即用、本地优先的 AI 队友。[affaan-m/ECC](https://github.com/affaan-m/ECC)（+1,165）表明 harness 性能、记忆与安全正在成为独立的优化层。在垂直应用中，[HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor)（+1,598）是个性化辅导领域最突出的亮点。与此同时，RAG/记忆栈按总 Star 数仍是积累最深的，其中领先的是 [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl)（196,096）、[thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)（103,741）和 [infiniflow/ragflow](https://github.com/infiniflow/ragflow)（92,975）。

## 2. 各分类热门项目

### 🔧 AI 基础设施

| 项目 | 语言 | Star 数（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 185,031 | 面向 Kimi、GLM、MiniMax、DeepSeek、gpt-oss、Qwen、Gemma 等模型的本地模型运行器。它仍是自托管 LLM 的默认入口，也是一个高吸引力基础设施项目。 |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,257 | 面向最先进文本、视觉、音频和多模态模型的模型定义框架，用于推理与训练。随着 agent 栈需要覆盖多种模型，其广度使其保持核心地位。 |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 290,911 (+1,165) | 面向 Claude Code、Codex、OpenCode、Cursor 等的 agent harness 性能优化系统，覆盖技能、直觉、记忆、安全与 research-first 开发。今日 +1,165 表明 harness 优化正在成为独立基础设施层。 |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,290 | 面向 agent 与生成式 UI 的前端栈，覆盖 React、Angular、移动端、Slack 和 AG-UI Protocol。随着 agent UX 超越纯聊天界面，它值得关注。 |
| [JetBrains/thinkrail](https://github.com/JetBrains/thinkrail) | TypeScript | 0 (+1,204) | 基于 pi 的轻量级真实 IDE，用于 vibe coding，可围绕开发者工作流进行自我定制。JetBrains 的入局今日吸引 +1,204，显示 agentic IDE 领域竞争加剧。 |
| [pytorch/pytorch](https://github.com/pytorch/pytorch) | Python | 105,132 | 提供张量与动态神经网络，并具备强大的 GPU 加速能力。它仍是支撑训练与推理基础设施的核心 ML 框架。 |
| [tensorflow/tensorflow](https://github.com/tensorflow/tensorflow) | C++ | 201,314 | 面向所有人的开源机器学习框架。即便以 agent 为中心的栈持续演进，其庞大的安装基数仍使其保持相关性。 |

### 🤖 AI Agents / 工作流

| 项目 | 语言 | Star 数（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [morluto/rea](https://github.com/morluto/rea) | TypeScript | 397,556 | 用 agent 逆向工程任何东西，从应用行为一直到原生二进制。它是信息流中总 Star 数最高的 agent 项目，并凸显安全/逆向工程 agent 是重要方向。 |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 257,764 | 被称为“与你共同成长的 agent”的 agent 框架。其极高的总 Star 数使其成为 agent 生态中的重要参照点。 |
| [langchain-ai/langchain](https://github.com/langchain-ai/langchain) | Python | 148,574 | 用于构建 LLM 驱动工作流的 agent 工程平台。尽管出现了更新、更轻量的 agent harness，它仍是高吸引力的基础层。 |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Python | 119,318 | 面向 AI 浏览器 agent 的开源 Python 库，并提供带隐身浏览器和 agent API 的托管云服务。浏览器控制仍是实用 agent 的核心能力。 |
| [Nutlope/hallmark](https://github.com/Nutlope/hallmark) | CSS | 0 (+2,830) | 面向 Claude Code、Cursor 和 Codex 的反 AI 垃圾设计技能。它是今日新增 Star 最多的项目，显示出对编码 agent 设计质量护栏的需求。 |
| [makecindy/cindy](https://github.com/makecindy/cindy) | TypeScript | 0 (+1,465) | 旨在开箱即用的开源 AI agent，定位为“想到即做到”。今日 +1,465 表明市场青睐可直接运行的 agent，而非 DIY 框架。 |
| [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | Rust | 0 (+1,449) | 让 AI harness 更擅长设计的设计语言。基于 Rust 的 agent 设计工具链值得关注，今日 +1,449 使其成为最强的技能层新发布之一。 |
| [nightly-labs/openbot](https://github.com/nightly-labs/openbot) | TypeScript | 0 (+777) | 面向持久 AI 队友的本地优先桌面工作区，可运行 Codex、Claude 和 Grok，并提供工作区、任务队列、文件共享、浏览器控制以及 agent 间协作。它指向持久化多 agent 桌面环境这一产品方向。 |

### 📦 AI 应用

| 项目 | 语言 | Star 数（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [open-webui/open-webui](https://github.com/open-webui/open-webui) | Python | 156,979 | 用户友好的 AI 界面，支持 Ollama、OpenAI API 等。它仍是自托管 AI 聊天与模型访问的高 Star 默认选择。 |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 135,865 | 根据主题或关键词生成高清短视频的 AI 工作流。它显示出垂直媒体自动化的持久需求。 |
| [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) | Python | 117,347 | 面向金融交易的多 agent LLM 框架。金融仍是 Star 数最高的垂直 agent 类别之一。 |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 45,970 (+1,598) | 终身个性化辅导应用。今日 +1,598 使其成为教育 agent 发布中的突出项目，且与 RAG 领域相关。 |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 77,922 | 开源 AI 求职 agent，可扫描招聘板、根据简历为职位打分、定制简历与求职信，并在 AI 编码 CLI 中本地跟踪申请。它是 agentic 垂直工作流自动化的具体示例。 |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 69,605 | 将文档或主题转换为原生 PowerPoint 演示文稿的 AI 工具，支持形状、切换、动画、图表和旁白。它面向高价值办公生产力，并输出真实文件。 |
| [CherryHQ/cherry-studio](https://github.com/CherryHQ/cherry-studio) | TypeScript | 53,033 | AI 生产力工作室，提供智能聊天、自主 agent、300+ 助手以及对前沿 LLM 的统一访问。它显示桌面 AI 工作区品类正在成熟。 |
| [gitroomhq/postiz-app](https://github.com/gitroomhq/postiz-app) | TypeScript | 0 (+951) | Agentic 社交媒体排期工具。今日 +951 表明 agentic 垂直 SaaS 持续吸引关注。 |

### 🧠 LLMs / 训练

| 项目 | 语言 | Star 数（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [jingyaogong/minimind](https://github.com/jingyaogong/minimind) | Python | 64,502 | 约两小时从零训练 64M 参数 LLM 的项目。它仍是理解 LLM 训练的热门动手入口。 |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,551 | LLM 评估平台，支持 OpenAI、Anthropic、Gemini、Qwen、GLM、DeepSeek 等，覆盖 100+ 数据集。随着模型与提供商选择增多，评估正变得越来越关键。 |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 4,790 | 面向 Apple Silicon 的教育性推理系统，使用 Qwen 构建一个微型 vLLM 风格栈。它对学习本地 LLM 推理的系统工程师很有用。 |
| [rohitg00/ai-engineering-from-scratch](https://github.com/rohitg00/ai-engineering-from-scratch) | Python | 74,710 | AI 工程学习资源：学习它、构建它并交付它。高 Star 数显示对实用 AI 工程技能提升的持续需求。 |
| [microsoft/AI-For-Beginners](https://github.com/microsoft/AI-For-Beginners) | Jupyter Notebook | 70,303 | 面向初学者的 12 周、24 课 AI 课程。它仍是 AI 教育的主流入口。 |
| [labmlai/annotated_deep_learning_paper_implementations](https://github.com/labmlai/annotated_deep_learning_paper_implementations) | Python | 67,605 | 60+ 深度学习论文实现与教程，配有并排注释，包括 transformer、优化器、GAN、RL 等。它是深入理解模型的宝贵从业者参考。 |
| [datamllab/OpenEBFM](https://github.com/datamllab/OpenEBFM) | Python | 179 | 用于训练和扩展基于能量的基础模型的开源代码。它处于早期阶段，但代表了超越主流 LLM 架构的基础模型研究。 |

### 🔍 RAG / 知识

| 项目 | 语言 | Star 数（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl) | TypeScript | 196,096 | 面向 AI agent 的 Web 数据平台，将 Web 内容转化为 agent 可用数据。其高总 Star 数表明 Web 获取仍是核心上下文层。 |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 137,013 | 将代码库、文档、SQL schema、配置和 PDF 转化为可查询知识图谱，并提供面向 Claude Code、Cursor、Codex 和 Gemini CLI 的技能。其确定性的 AST 解析与无向量存储方案使其成为值得注意的无向量 RAG 信号。 |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 103,741 | 面向 agent 的持久上下文层，可捕获会话活动、用 AI 压缩，并将相关上下文注入未来的会话，覆盖 Claude Code、Codex、Gemini、Copilot、OpenCode 等。记忆正成为 agent 必备子系统。 |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 92,975 | 开源 RAG 引擎，将检索增强生成与 agent 能力融合。它是领先的上下文层项目，总 Star 增长势头强劲。 |
| [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) | Python | 86,414 | 面向 LLM 和 AI agent 的 Web 爬虫与抓取器，可将网站转换为干净、LLM 可用的 Markdown。它是 RAG 与 agent 的实用数据获取层。 |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 78,052 | 在工具输出、日志、文件和 RAG 分块到达 LLM 之前进行压缩——编码 agent 可减少 20% token，JSON 可减少 60–95%。它针对上下文成本与延迟这一日益增长的 RAG/agent 瓶颈。 |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 67,941 | 面向 AI agent 的记忆层，提供可即插即用的持久上下文基础设施。它面向生产级记忆，而非临时聊天历史。 |
| [milvus-io/milvus](https://github.com/milvus-io/milvus) | Go | 46,506 | 高性能云原生向量数据库，用于可扩展的 ANN 搜索。它仍是检索密集型 AI 系统的核心基础设施。 |

## 3. 趋势信号分析

今日热榜显示，重心正从模型访问转向 agent 工效学。上升最快的项目并非新的基础模型，而是技能、设计护栏、本地工作区以及面向编码 agent 的 harness 优化。社区正在奖励输出质量、持久上下文和工作流契合度，而非原始模型能力。这是一个值得注意的转变：agent 产品的评判标准，正变成它们在真实开发循环中能否可靠运行。

新的技术栈信号已经显现。Rust 正出现在 agent/设计工具和终端原生 agent 中，而 TypeScript 主导本地优先桌面工作区与 agent 前端。技能与方法论层正通过基于 shell 的框架、反 AI 垃圾设计技能和可复用 agent 指令逐步形式化。在检索领域，重点已较少放在再添加一个向量数据库上，而更多放在上下文工程上：知识图谱、会话记忆、Web 数据管道以及 token/上下文压缩。无向量与基于推理的 RAG 也在出现，同时还有端侧推理，指向成本与隐私优化。

多模型现实已经明确。Agent 通常面向 Codex、Claude、Grok、Gemini、Qwen、DeepSeek 以及本地运行器。近期模型激增——Kimi、GLM、MiniMax、gpt-oss、Qwen、Gemma——使提供商无关的 harness 和本地优先工作区更有价值。金融、求职、辅导和社交媒体排期领域的垂直 agent 正在成熟，但今日最强的信号是面向 agent 质量、记忆、编排和上下文控制的基础设施。预计竞争将更多集中在 harness 性能、设计质量和持久记忆上，而不仅仅是模型封装。

## 4. 社区热点

- **Agent 设计质量与反 AI 垃圾护栏** — [Nutlope/hallmark](https://github.com/Nutlope/hallmark) (+2,830) 与 [pbakaus/impeccable](https://github.com/pbakaus/impeccable) (+1,449) 是今日最清晰的信号，表明设计品味正成为编码 agent 的差异化因素。开发者正在投入那些约束输出质量、而不只是生成更多代码的技能。

- **Agent harness 优化** — [affaan-m/ECC](https://github.com/affaan-m/ECC) (290,911, +1,165) 将技能、直觉、记忆、安全与 research-first 开发打包为性能层。这值得关注，因为 harness 行为正变得与底层模型同样重要。

- **本地优先的持久队友** — [makecindy/cindy](https://github.com/makecindy/cindy) (+1,465) 与 [nightly-labs/openbot](https://github.com/nightly-labs/openbot) (+777) 推动开箱即用的 agent，具备工作区、任务队列、浏览器控制和 agent 间协作。方向是持久化桌面/工作区 agent，而非单会话聊天。

- **持久记忆与上下文工程** — [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) (103,741) 与 [mem0ai/mem0](https://github.com/mem0ai/mem0) (67,941) 表明记忆正成为 agent 的必需子系统。结合 Web 数据与压缩工具，RAG 栈正从“检索文档”转向“随时间管理上下文”。

- **垂直教育 agent** — [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) (45,970, +1,598) 是今日势头强劲的个性化终身辅导 agent 示例。它表明，即便通用助手竞争激烈，具有清晰用户成果的垂直 agent 仍能突围。

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*