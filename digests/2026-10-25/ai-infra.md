# AI 基础设施日报 2026-10-25

> 生成时间: 2026-10-25 00:29 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目对比报告 — 2026-10-25

## 1. 生态概览

AI 基础设施生态正处于针对特定模型进行加固的阶段：vLLM 和 SGLang 未发布新版本，但二者都围绕 DeepSeek-V4.x、GLM-5.x、Kimi-K3、Qwen3.x 和 Minimax-M3 承载了大量 PR 活动。主要工程战场是 KV 缓存内存占用降低、投机解码/MTP 正确性、MoE 专家处理，以及跨 Blackwell、Hopper、GB300、AMD MI300X/MI355X、Hexagon、RDNA 和 NPU 目标的分布式服务。硬件支持范围不断扩大但日趋碎片化，CUDA、ROCm、SYCL、Metal、Vulkan 和 MLX 上均出现后端相关回归。网关层在 LiteLLM 中发生了一起严重的供应链事件，而本地运行时改善了 OpenAI/Anthropic 兼容性，但仍存在静默正确性缺陷。微调正转向多 GPU 流水线/张量并行，但桌面端打包可靠性仍是风险。

## 2. 活动对比

> 数据说明：SGLang 和 Unsloth 是仅有的两个在摘要中带有汇总活动计数的项目。对于其他项目，“cited”指摘要中唯一的 GitHub issue/PR 引用数，而非仓库总量。

| 项目 | 层级 | Issue（摘要） | PR（摘要） | 发布状态 |
|---|---|---:|---:|---|
| **vLLM** | 数据中心服务引擎 | 引用 25 个；12 个已排序的开放稳定性 Issue；无汇总 | 引用 15 个；无汇总 | 24 小时内无发布；最新引用 v0.32.0 |
| **SGLang** | 服务引擎/运行时 | **36 个被触及** | **约 493 个已更新** | 24 小时内无发布 |
| **llama.cpp** | 本地/边缘运行时 | 引用 19 个；无汇总 | 引用 20 个；无汇总 | **b11856–b11869**（14 个滚动构建） |
| **Ollama** | 本地运行时/开发者端点 | 引用 6 个 Issue；9 个已排序稳定性条目（含 PR） | 引用 12 个 PR | **v0.40.11** — macOS 语音支持 |
| **LiteLLM** | LLM 网关/路由器 | 引用 21 个；无汇总；严重 #47826 | 引用 16 个修复 PR | **1.106.3 / 1.105.4 / 1.104.6 / 1.103.8**；1.107.0 遭污染并已撤回 |
| **Unsloth** | 微调/Studio | 引用 14 个；无汇总 | **180 个已更新**（多为 dependabot）；7 个值得注意的引用 | 无发布；Studio Windows GGUF 加载回归 |

**解读：** SGLang 可见的 PR 变更量最高，但其中很多是 CI 不稳定和 DeepSeek/GLM/Kimi 缺陷相关流量。llama.cpp 通过滚动构建拥有最高发布频率。LiteLLM 的四个补丁版本被受污染的 1.107.0 制品所掩盖。vLLM 和 Unsloth 表现出大量实质性 PR 活动，但没有打标签的发布。

## 3. 模型支持竞赛

| 项目 | 今日新增模型/架构支持 | 定位 |
|---|---|---|
| **vLLM** | 面向 DeepSeek-V3.1 和 Kimi-K3 上稠密 MLA 的 NVFP4 KV 缓存；在 `/v1/systemone` 上提供 Qwen3-VL 服务；Qwen2.5/3-Omni 的 `use_audio_in_video`；MI300X 上面向 DeepSeek-V4.1-Flash 的 ROCm MXFP4 W4A16；Kimi-K3 MI355X recipe；DBO + 投机解码 + DCP | **在数据中心服务广度和 KV 缓存量化方面领先。** DeepSeek/Kimi/Qwen 覆盖最强，但 hybrid-GDN/投机解码缺陷是明显短板。 |
| **SGLang** | Rust 服务器 + Qwen-VL 多模态；基于 Mooncake 的 NPU PD 分离；gfx950 上 AMD DeepSeek-V4 共享专家融合；Quark/Qwen3.8 MXFP4 共享专家量化；树外统一缓存链接器；Beam 模型内核；SANA-Sprint 扩散请求 | **在 AMD MI355X 共享专家融合、Rust 多模态和 NPU PD 分离方面领先。** 但 `qwen4_exp` checkpoint 加载尚不完整，且 DeepSeek/GLM/Kimi 服务缺陷正在聚集。 |
| **llama.cpp** | Minimax-M3 NVFP4/MXFP8 量化 scale；DeepSeek V4.1 Flash 支持 PR 已开放；Hexagon 多核 HMX FlashAttention；Vulkan MoE 专家缓存修复；SYCL 舍入修复；Metal `mul_mm_id` 重新缩放；CUDA Kepler 旧架构处理 | **在边缘/消费级硬件广度方面领先。** 对 Hexagon、Vulkan、Metal、SYCL 和 CUDA 的覆盖强，但不是数据中心服务领域的竞争者。 |
| **Ollama** | MLX 上的 Nemotron 3.5 Bolt 74B-A9B；长 HuggingFace GGUF 模型名；Gemma 4 紧凑视觉塔；通过 `/v1/audio/speech` 进行 macOS 语音生成 | **在本地开发者体验和 MLX 打包方面领先。** 但 MLX 停止序列和混合精度导入的正确性缺口仍然存在。 |
| **LiteLLM** | Nemotron 3.5 Super VL 120B-A12B W&B 定价；Cerebras Llama 3.1 别名；未编目的 OpenAI 兼容模型保留 tools；通过 Responses 桥接未编目的 ChatGPT；Bedrock Nova 2 Lite 地理修复；OpenAI prompt-cache 写入定价；Azure 转录 GA 端点 | **在提供商/模型目录和网关兼容性方面领先。** 它不是推理引擎；其价值在于抽象、成本、认证和路由。 |
| **Unsloth** | 今日主要不是新增模型支持；流水线并行 v1；张量并行 v2；Metal OOM 恢复；Wan 流式编码；蒸馏路线图 | **在多 GPU 微调易用性方面领先。** PP/TP 工作使 8B LoRA 在 2×16 GB 上可行，但 Studio Windows 回归问题严重。 |

**总体领先者：** vLLM 在当前前沿模型的数据中心服务支持和 KV 缓存量化方面领先。SGLang 紧随其后，并在 AMD MI355X 共享专家融合以及 Rust/NPU 服务路径上领先。llama.cpp 和 Ollama 在本地/边缘部署方面领先。LiteLLM 在网关覆盖方面领先，但正应对一起严重安全事件。Unsloth 在多 GPU 微调而非服务方面领先。

## 4. 性能前沿

优化工作集中在五个领域：

- **KV 缓存与内存占用** — vLLM 的 NVFP4 MLA KV 缓存将 DeepSeek/Kimi 的 KV 降至每 token 每层 352 字节，而 FP8 为 576、BF16 为 1152。SGLang 将 CP KV gather 与 indexer 计算重叠，并修复调度器 prefill 准入。llama.cpp 让 KV rotation 常量驻留 GPU，并与 Vulkan 长上下文碎片化作斗争。Unsloth 致力于并行聊天中的 KV 抢占。**KV 缓存是最活跃的优化前沿。**
- **量化** — vLLM 增加 NVFP4 KV 和 ROCm MXFP4 W4A16；SGLang 增加 MXFP4 共享专家量化，并修复扩散量化缺陷；llama.cpp 调优 NVFP4 MMVQ 和 Minimax-M3 缩放系数；Ollama 处理 MLX 混合精度导入失败。量化正从权重转向 KV 缓存和专家路径。
- **内核与主机开销融合** — vLLM 聚焦 DeepSeek-V4.1-Flash 解码元数据融合、ROCm draft-prepare 快速路径，以及融合的 Kimi-K3 MLA 分块上下文 gather。SGLang 增加融合异步 NaN 校验、Beam 内核和 Wan 文本 K/V 投影缓存。llama.cpp 致力于 Hexagon FA 多核和 CUDA FA stream-K。重点在于每步主机开销和小内核合并。
- **分布式服务与 KV 传输** — vLLM 在 GB200 上 GLM-5.3 P/D 描述符开销表明，瓶颈可能是 NIXL 描述符提交，而非互联带宽。SGLang 增加基于 Mooncake 的 NPU PD 分离和 DSA CP KV 重叠。llama.cpp 存在未解决的多 GPU draft-MTP 吞吐回归。分布式服务越来越关乎 KV 移动和调度，而不仅是张量并行。
- **投机解码 / MTP 正确性** — vLLM 存在多个 GDN/投机解码正确性问题；llama.cpp 存在 MTP 崩溃和多 GPU 回归；SGLang 修复了拒绝采样 int32 溢出。投机解码如今是一项性能特性，但也伴随显著的正确性代价。

## 5. 层级定位

- **vLLM — 数据中心推理引擎。** 高吞吐 GPU 服务、PagedAttention 风格 KV 管理、分布式服务、投机解码和模型专用内核。最适合模型广度和 KV 效率至关重要的生产模型服务。
- **SGLang — 具有更广泛前端和硬件实验的服务引擎/运行时。** 在数据中心服务上与 vLLM 重叠，但通过 Rust 服务器路径、多模态预处理、扩散支持、NPU PD 分离和 AMD 专用专家融合实现差异化。如今更容易受到模型专用服务缺陷影响。
- **llama.cpp — 本地/边缘运行时和后端层。** 基于 GGML 的推理，覆盖 CUDA、Metal、Vulkan、SYCL、Hexagon 和 CPU。最适合本地、桌面、嵌入式和异构硬件；不是集群规模的服务引擎。
- **Ollama — 面向开发者的本地模型运行时。** 通过简单的 CLI 和 OpenAI 兼容端点封装本地推理，现在新增语音。它抽象了 llama.cpp/MLX 后端，并在用户体验、模型拉取和本地开发者工作流上竞争。
- **LiteLLM — LLM 网关/路由器/代理。** 提供商抽象、认证、预算、支出跟踪、重试以及 OpenAI/Anthropic 兼容性。它位于服务引擎和云 API 之前；其风险在于供应链、成本准确性和工具调用转换。
- **Unsloth — 微调/训练框架和 Studio。** 专注于高效 LoRA/微调、量化，以及现在的多 GPU PP/TP。Unsloth Studio 也提供本地服务，但其核心层是训练和模型定制。

## 6. 趋势信号

- **静默正确性如今比崩溃更危险。** vLLM 在 hybrid GDN 上的 ngram 投机解码会产生损坏输出；SGLang 的 `fast_topk_v2` 会静默返回错误的 top-k 集合；Ollama 的 MLX 路径忽略停止序列；llama.cpp SYCL 会产生 token 乱码。Agent 开发者应验证输出质量，而不只是 HTTP 200。
- **KV 缓存正确性和命名空间隔离对 Agent 循环很重要。** 前缀缓存缺陷出现在 vLLM hybrid GDN、Ollama Anthropic 兼容层和 SGLang 的 Anthropic 适配器中。vLLM 的持久化 KV 卸载缓存未按模型 revision 划分命名空间。对于多轮 Agent，缓存正确性直接影响延迟、成本和输出稳定性。
- **Agent 工具调用是主要测试面。** 在 1.106 中，LiteLLM 会为未编目模型静默丢弃 `tools`；SGLang 在 qwen3_coder、hunyuan 和 minimax-m2 上存在流式工具参数缺陷；Ollama 将整数工具参数渲染为小数；GLM-5.3-Flash 在多工具提示词上退化。关注 `finish_reason` 处的 JSON 完整性、缺失的 `</function>` 以及工具 schema 处理。
- **供应链安全如今是一等基础设施关切。** LiteLLM 1.107.0 从一个仿冒仓库发布了凭据窃取程序和 `sitecustomize` RCE 加载器。Cosign 密钥轮换将破坏准入流水线。按 digest 固定版本、验证签名，并且如果在 2026-10-21 拉取过 `:latest`，请轮换凭据。
- **硬件支持范围正在扩大，但不均衡。** AMD MI300X/MI355X/gfx950、Blackwell/SM120、GB300、Hexagon、RDNA 和 NPU 路径都有活跃工作和活跃回归。不存在单一的“受支持”矩阵；团队需要按模型、按后端进行验证。
- **OpenAI/Anthropic 兼容性正在改善，但仍有漏洞。** Ollama 修复了 UUID ID 和 `max_completion_tokens`；LiteLLM 修复了 Claude 网关发现和 Anthropic 流清理；llama.cpp 修复了 Jinja `selectattr`。但前缀缓存失效、被忽略的 `think` 参数和流式 usage 丢失仍然存在。应用开发者应在 CI 中保留兼容性测试。
- **积极固定版本。** llama.cpp 发布了 14 个带有行为变更的滚动构建；LiteLLM 发布了四个补丁外加一个受污染版本；Unsloth 的 Windows Studio beta 破坏了 GGUF 加载；vLLM/SGLang 没有发布，但有许多未解决的高严重性缺陷。生产环境中，使用已知良好的 digest，并维护按模型/后端的回归测试套件。

---

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-10-25

## 1. 今日亮点

过去 24 小时内没有版本发布，但活动集中在 DeepSeek-V4.1-Flash 解码路径：一个用于 eager 元数据内核融合的跟踪 PR（#63308/#63523）和 ROCm draft-prepare 快速路径（#63182）都针对 MTP 解码中的每步主机开销。内存方面，一个新 PR 为稠密 MLA 模型（DeepSeek-V3.1、Kimi-K3）新增 `--kv-cache-dtype nvfp4`，将 KV 占用降至 352 字节/token/层，而 FP8 为 576。与此相抵消的是，Qwen3.8 混合 GDN 上新出现的 PP=3 非法内存访问回归（#63505），以及若干未解决的 hybrid-GDN/spec-decode 正确性 bug，仍是最高严重级别的未决风险。

## 2. 发布与破坏性变更

今日无新版本或配置迁移。有一项行为变更值得注意：

- **[CLOSED] `/invocations` 现在强制校验 `--api-key`** — [PR #58341](https://github.com/vllm-project/vllm/pull/58341)。此前，未携带凭据访问 `/v1/chat/completions` 会返回 401，而将相同请求体 POST 到 `/invocations` 却返回 200。对于使用 SageMaker 风格 `/invocations` 且配置了 API key 的部署，现在未携带凭据的请求将被拒绝。请审计任何依赖未认证路径的客户端。
- **[CLOSED] 为 R3 启用 PP 路由专家捕获** — [PR #61333](https://github.com/vllm-project/vllm/pull/61333)。现在在 `PP > 1` 时也能捕获路由专家；此前最终阶段缺少完整的路由数据。
- **提议的语义变更：** [Issue #54383](https://github.com/vllm-project/vllm/issues/54383) 报告称，对于给定的 `(max-num-batched-tokens, max-num-seqs)` 形状，首次启动得到的 KV cache 比后续相同启动小约 5%——如果你根据首次启动日志来规划容量，这一点值得关注。

## 3. 新模型与硬件支持

- **面向稠密 MLA 的 NVFP4 KV cache** — [PR #63203](https://github.com/vllm-project/vllm/pull/63203)。在 DeepSeek-V3.1 和 Kimi-K3 的 `FLASHINFER_MLA` 后端上新增 `--kv-cache-dtype nvfp4`：352 字节/token/层，相比 FP8 的 576 和 BF16 的 1152——分别减少约 1.6 倍和 3.3 倍。NVFP4 中为 512 个 latent 维度，使用 E4M3 块缩放。
- **在 `/v1/systemone` 上提供 Qwen3-VL 服务** — [PR #62402](https://github.com/vllm-project/vllm/pull/62402)。将 `Qwen3VLForConditionalGeneration` 映射到现有的 `NextTokenStrategy`，沿用 Gemma 4 的选择加入方式。
- **Qwen2.5-Omni / Qwen3-Omni 的 `use_audio_in_video`** — [PR #63135](https://github.com/vllm-project/vllm/pull/63135)。修复了通过 `--mm-processor-kwargs` 在引擎范围设置该标志时导致的崩溃；在每个检测点从合并后的引擎 + 请求 kwargs 中解析。
- **面向 gfx942（MI300X）的 ROCm MXFP4 W4A16** — [PR #63522](https://github.com/vllm-project/vllm/pull/63522)。将 AITER Triton MXFP4 的中间大小对齐到 128；由 8× MI300X 上的 DeepSeek-V4.1-Flash MLPerf Endpoints 提交贡献。
- **Kimi-K3 MI355X AgentX recipe** — [PR #63520](https://github.com/vllm-project/vllm/pull/63520)。Recipe YAML 已与 InferenceX #4393 重新对齐。
- **DBO + 投机解码 + DCP** — [PR #61464](https://github.com/vllm-project/vllm/pull/61464)。解除了 V2 runner 的拒绝限制；draft prefill 复用目标模型的全批 attention 元数据和 slot 映射，以及其 FULL 图。Prefill CP 仍受门控。
- **未解决的硬件缺口：** 对于 `enable_adaptive_verification`，SM120 会被拒绝，因为 device-query-lens 支持仅限 SM90/SM100（[#63343](https://github.com/vllm-project/vllm/issues/63343)）；gfx942/MI325X 的 Kimi-K3 路线图仍不完整（[#52803](https://github.com/vllm-project/vllm/issues/52803)）；将硬编码的 `torch.cuda` 调用替换为 `torch.accelerator` 的 RFC 仍处于停滞/开放状态（[#30679](https://github.com/vllm-project/vllm/issues/30679)）。

## 4. 性能与优化

- **DSv4.1 flash-decode 元数据融合（跟踪）** — [PR #63308](https://github.com/vllm-project/vllm/pull/63308) 和 [PR #63523](https://github.com/vllm-project/vllm/pull/63523)。从捕获的 flash-decode 前向周围的 eager 部分中，移除 torch dispatch 中的每步主机端元数据运算。在已应用堆叠解码融合的情况下，于 8×H200、DP=8、MTP、c16 解码上进行了性能分析。
- **gfx950（MI355X）上 DSv4.1-Flash 的 ROCm draft-prepare 快速路径** — [PR #63182](https://github.com/vllm-project/vllm/pull/63182)。对每次 draft CUDA 图重放之前运行的 DSpark draft-prepare 步骤做了三项更改：跨 draft KV cache 组的共享输出去重、并行 pad-tail 修复，以及仅 KV 的上下文插入（移除 dummy-Q）。
- **AITER 上融合的 Kimi-K3 MLA 分块上下文 gather** — [PR #53166](https://github.com/vllm-project/vllm/pull/53166)。稠密 MLA prefill 当前每个上下文块要花费四个 kernel（`gather_and_maybe_dequant_cache`、`kv_b_proj` 扩展，以及两次 `direct_copy` 调用）；目标是合并这些 kernel。
- **Triton top-k/top-p 回退** — [Issue #61998](https://github.com/vllm-project/vllm/issues/61998)。`_topk_topp_kernel` 仅根据前 8192 个 logits 的高斯拟合来选择 gather pivot；在 MiMo-V2.6 上，对于 6–10% 的行，该 pivot 过高，迫使进行全词表搜索。
- **GB200 上 GLM-5.3 P/D 描述符开销** — [Issue #55434](https://github.com/vllm-project/vllm/issues/55434)。在 agentic 负载下，NIXL 传输每次 TP rank 传输提交 91k–120k 个 KV 描述符，使得在此工作负载上 MNNVL/cuda_ipc 比 RDMA 更慢——瓶颈是描述符提交，而不是 fabric 带宽。后续优化列表见 [Issue #57406](https://github.com/vllm-project/vllm/issues/57406)。
- **KV offload 背压误报** — [PR #63524](https://github.com/vllm-project/vllm/pull/63524)。`fs` 层存储任务在重新存储已存在于磁盘上的前缀时跳过了写入，但仍将整个批次报告到 `transfer_bytes` 中，任务在微秒级完成，从而触发背压检测器。
- **前端性能报告：** Rust 前端在 Qwen122B-A10B、平均输入 44k 时，相比 Python 出现 TTFT 退化和 QPS 扩展性不佳（[#50718](https://github.com/vllm-project/vllm/issues/50718)）；Rust 与 PyTorch 预处理基准（[#47601](https://github.com/vllm-project/vllm/issues/47601)）。

## 5. 稳定性与回归

按严重程度排序。注意：前三项尚无确认的修复 PR。

1. **Qwen3.8-27B（GDN 混合）在 sm_86 上 PP=3 → 非法内存访问（Xid 31）** — [Issue #63505](https://github.com/vllm-project/vllm/issues/63505)。启用 prefix caching 时 GPU 出错；不启用时输出为乱码。自 v0.28.0 起出现回归，在 v0.31.0 和 v0.32.0 上跨两个 model runner 均可复现。今日提交。
2. **Qwen4Exp 单次 prefill 跨越 262,144 原生边界导致引擎崩溃** — [Issue #62383](https://github.com/vllm-project/vllm/issues/62383)。在 v0.32.0 和 nightly 中，H200（TP=2 + EP）上发生 CUDA 非法内存访问；在 v0.31.1 上正常。显然可二分定位。
3. **TP worker 挂起 → EngineDeadError（`sample_tokens` RPC 超时）** — [Issue #41530](https://github.com/vllm-project/vllm/issues/41530)。DeepSeek-V4-Pro、TP=8、MTP 投机解码。长期未解决，已有 18 条评论、7 个 👍。
4. **Ngram 投机解码在混合 GDN（Qwen3.5）上产生损坏输出** — [Issue #39273](https://github.com/vllm-project/vllm/issues/39273)。在 GH200 上跨多个 nightly 复现——静默产生错误 token，比崩溃更糟。
5. **Qwen3.5/3.8 混合 GDN 在 prefix-cache 命中后出现 NaN logits** — [Issue #55766](https://github.com/vllm-project/vllm/issues/55766)。在 `mamba cache mode align` 下，当上一次 prefill 结束位置超过块边界 4–10 个 token 时触发。
6. **Kimi-K3 + `--kv-cache-dtype fp8` 在 Hopper 上不可用** — [Issue #51313](https://github.com/vllm-project/vllm/issues/51313)。断言要求 `use_prefill_query_quantization`，但该标志在非 Blackwell 上被静默忽略，因此断言会再次触发。
7. **Qwen3.5 FP8 在 H200 上于 CUDA 图捕获期间崩溃** — [Issue #49311](https://github.com/vllm-project/vllm/issues/49311)。v0.25.0 回归；已标记为 stale 但仍开放。
8. **SM120 上 NVFP4 + DFlash + torch.compile 导致 SIGSEGV** — [Issue #48234](https://github.com/vllm-project/vllm/issues/48234)。在 `THPVariable_transpose` 期间于 `torch::jit::invokeOperatorFromPython` 中出错，伴随 CUDA graph PIECEWISE。
9. **投机解码下 thinking budget 损坏多 token 的 `reasoning_end_str`** — [Issue #58485](https://github.com/vllm-project/vllm/issues/58485)。仅影响 V1 引擎；影响推理解析器。
10. **`ROCM_ATTN` 在 RDNA 上返回错误的 attention** — [Issue #62697](https://github.com/vllm-project/vllm/issues/62697)。head size ≥ 96 且 KV block size ≠ 16，以及 MHA 为 192/256 时；可能导致 GPU 故障。
11. **Mooncake connector 在 decode 超时后崩溃** — [Issue #60755](https://github.com/vllm-project/vllm/issues/60755)。v0.31.0 中 EngineCore 崩溃。
12. **持久化 KV offload cache 未按模型 revision 划分命名空间** — [Issue #49261](https://github.com/vllm-project/vllm/issues/49261)。如果原地替换权重，会发生跨 revision 的 cache 串扰。

今日关闭：[#53928](https://github.com/vllm-project/vllm/issues/53928)（XPU `causal_conv1d` 在使用 MTP + 分块 prefill 时拒绝混合 spec/non-spec token）。

## 6. 对应用开发者的意义

- **混合 GDN 模型（Qwen3.5/3.8）加上 prefix caching 是目前最尖锐的风险点。** 多份独立报告中的 NaN、乱码和非法内存访问都集中在 GDN + prefix caching + PP 或 MTP 上。如果你要服务这些模型，请固定到已知良好的版本，在使用 PP 时禁用 prefix caching，并验证输出，而不是只相信 HTTP 200。
- **KV cache 内存是当前活跃的优化前沿。** NVFP4 MLA KV（#63203）可能显著提升 DeepSeek/Kimi 工作负载的并发上下文容量，或减少副本数量——但它落在 sampler/attention 路径中，因此在基准测试落地前应视为实验性功能。
- **剩余的正确性 bug 主要集中在投机解码。** GDN 上的 Ngram、thinking-budget `reasoning_end_str`，以及 DeepSeek TP 挂起都涉及 spec decode。依赖结构化推理终止的 agent 框架应显式测试这些路径。
- **网关开发者获得两项相关修复。** 数据并行下的 ORCA `endpoint-load-metrics` 仅报告一个引擎的负载，静默地扭曲了基于负载的路由——参见 [PR #63445](https://github.com/vllm-project/vllm/pull/63445) 和 [PR #61899](https://github.com/vllm-project/vllm/pull/61899)。Dynamo、llm-d 和 vLLM Router 的 KV 事件正确性/兼容性跟踪已汇总到 [Issue #63149](https://github.com/vllm-project/vllm/issues/63149)；面向 agentic 服务的可编程 KV cache 策略是 [Issue #57103](https://github.com/vllm-project/vllm/issues/57103) 中的开放 RFC。
- **优先级调度仍然比较粗糙。** [Issue #40004](https://github.com/vllm-project/vllm/issues/40004) 指出，等待队列中的请求无法抢占正在运行的请求，因此高优先级请求可能被长时间的低优先级生成阻塞。如果你依赖优先级分层来处理交互式流量，请为队头阻塞做好准备。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-10-25

## 1. 今日亮点

过去 24 小时没有新版本发布，但活动仍然非常活跃（约 493 个 PR 更新、36 个 issue 有变动），主要由 CI 不稳定测试跟踪以及 Blackwell、Hopper 和 GB300 上不断增多的 DeepSeek‑V4/GLM‑5.x/Kimi‑K3 serving bug 集群所主导。在性能方面，工作继续围绕降低 prefill 调度开销、将 CP KV gather 与 indexer 计算重叠，以及在 MI355X 上为 DeepSeek‑V4 做 AMD 专属共享专家融合展开。两个静默正确性问题——`fast_topk_v2` 返回错误的 top-k 集合，以及调度器 `double free` 导致服务器挂起——是生产运维人员最需要关注的未决项。

## 2. 新模型与硬件支持

- **AMD 消费级 Radeon（RDNA3/RDNA4）支持** —— 用于跟踪 `gfx1100/1101/1200/1201` 支持的 issue；目前 ROCm 路径仅支持数据中心 Instinct（`gfx942/gfx950`）。获得社区支持（4 👍），并包含具体启用计划。[Issue #30599](https://github.com/sgl-project/sglang/issues/30599)
- **SANA‑Sprint 0.6B 扩散** —— 请求 SGLang‑Diffusion 原生支持该 1–4 步、1024px 文本到图像模型。[Issue #43671](https://github.com/sgl-project/sglang/issues/43671)
- **Rust 服务器 + 多模态（Qwen‑VL）** —— `SGLANG_RUST_SERVER=1` 现在在 `dynamo-multimodal` 上通过 `sglang-processor` 路由图像预处理，并且 `/v1/chat/completions` 接受图像输入（此前会拒绝）。[PR #46291](https://github.com/sgl-project/sglang/pull/46291)
- **NPU PD 分离：Mooncake 传输后端** —— 为 NPU prefill/decode 分离新增 Mooncake 作为 KV 传输后端。[PR #46223](https://github.com/sgl-project/sglang/pull/46223)
- **树外统一缓存链接器** —— 外部包现在可按模块/类名提供并选择 `UnifiedCacheLinker`，与 vLLM 的 KV connector 模式类似。[PR #40595](https://github.com/sgl-project/sglang/pull/40595)
- **Beam 模型 kernel（3/7）** —— 融合注意力门、router GEMM 以及 all-reduce 边界算子作为堆叠系列的一部分合入。[PR #45254](https://github.com/sgl-project/sglang/pull/45254)
- **缺口：** `qwen4_exp` checkpoint 因 Transformers 无法识别该架构而加载失败——新模型接入尚不完整。[Issue #46326](https://github.com/sgl-project/sglang/issues/46326)

## 3. 性能与优化

- **Engram：面向均匀 prefill 分片的 reduce-scatter** —— 当 attention‑DP 分片相等时，用直接返回本地分片替代对 embedding buffer 的完整 all-reduce + scatter。[PR #46338](https://github.com/sgl-project/sglang/pull/46338)
- **Wan 扩散：每个去噪循环只投影一次文本 K/V** —— 避免在每个 block/step 中重复投影 512 个文本 token 并重复运行 RMSNorm；在 Wan2.2 A14B 720p（4×B300）上，每个 block 每步约有 0.07 ms 冗余工作。[PR #46246](https://github.com/sgl-project/sglang/pull/46246)
- **AMD DeepSeek‑V4 共享专家融合默认开启（gfx950）** —— 在 MI355X TP4 上，13 个测量点中有 10 个的每 token 中位耗时降低 2.0–6.6%（没有任何测量点变慢）。[PR #42496](https://github.com/sgl-project/sglang/pull/42496)
- **Quark/Qwen3.8 MXFP4 共享专家加载时量化** —— 加载时将 BF16 共享专家量化为 MXFP4，并在 `--enforce-shared-experts-fusion` 下融合进 routed grouped GEMM（ROCm）。[PR #41899](https://github.com/sgl-project/sglang/pull/41899)
- **DSA：将 CP KV gather 与 indexer 计算重叠** —— 在交错 CP prefill 期间，在 indexer 投影/压缩/打分之前提交 KV all-gather，包括跳过 MQA/top-k 的短 prefill。[PR #46179](https://github.com/sgl-project/sglang/pull/46179)
- **调度器 prefill 准入：增量式 `num_held_rows`** —— 移除等待队列准入循环中逐请求的重复计算（同样应用于 DLLM 传入请求）。[PR #46337](https://github.com/sgl-project/sglang/pull/46337)
- **融合异步 NaN 校验 kernel** —— 将原本分离的 `isnan`/reduce/negate/device-assert 启动合并为一个只读 Triton kernel，用于 logits 和概率检查。[PR #43720](https://github.com/sgl-project/sglang/pull/43720)
- **扩散量化：6 个已确认的性能/开销 bug** —— `multimodal_gen` 量化层中的代码审查发现，并附带一份包含 11 个正确性 bug 的配套报告（#46314）。[PR #46302](https://github.com/sgl-project/sglang/pull/46302)，[Issue #46302](https://github.com/sgl-project/sglang/issues/46302)
- **待解决的基准测试问题：** 在 SM120（4× RTX PRO 6000，TP4）上，DeepSeek‑V4 prefill 报告约 2–7K tok/s，而通过 vLLM/marlin 约为 12.5K tok/s —— kernel 覆盖缺口，尚无修复。[Issue #33422](https://github.com/sgl-project/sglang/issues/33422)。另有 8× H20‑3e 与 DeepInfer 在 `--enable-cp-decode-attn-tp` 下的对比。[Issue #46310](https://github.com/sgl-project/sglang/issues/46310)

## 4. 稳定性与回归

按严重程度排序：

1. **调度器 `double free or corruption` → 服务器永久挂起** —— nightly 上在空闲循环不变量检查（遍历 `session_held_tokens`）中 abort；未引用修复 PR。[Issue #42508](https://github.com/sgl-project/sglang/issues/42508)
2. **`fast_topk_v2` 静默返回错误的 top-k 集合** —— 在 k=2048 且行较长时，Radix 阈值桶超出其 4096 项候选缓冲区；这是静默正确性问题而非崩溃，因此结果错误但没有任何信号。[Issue #36807](https://github.com/sgl-project/sglang/issues/36807)
3. **Kimi‑K3 在 2 节点 GB300 上 DCP a2a decode 乱码** —— 启用 NCCL graph buffer 注册时，CUDA graphs 下 12/12 输出退化；规避方法 `NCCL_GRAPH_REGISTER=0` 可恢复正确输出。[Issue #45367](https://github.com/sgl-project/sglang/issues/45367)
4. **GLM‑5.3‑Flash thinking 退化为重复 `!`** —— 原生 FP8 checkpoint、复杂 system prompt + 多个工具 + `tool_choice=auto`；简单请求不受影响。直接影响 agentic 工作负载。[Issue #36669](https://github.com/sgl-project/sglang/issues/36669)
5. **DeepSeek‑V4‑Flash NVFP4 + `flashinfer_megamoe` + dp-attention 导致 CUDA graph capture 失败** —— 在当前 `main`（`638374c389`）上报 "transport finalize owes an undeclared sum group"。[Issue #46316](https://github.com/sgl-project/sglang/issues/46316)
6. **DeepSeek AWQ checkpoint 加载失败** —— CUDA 上出现 `ModuleNotFoundError: sglang.kernel_api_logging`；这是打包/重构引发的后续问题。[Issue #46312](https://github.com/sgl-project/sglang/issues/46312)
7. **扩散量化层：11 个正确性 bug** —— ModelSlim 双尺度 MXFP4 在每一层崩溃，涉及 rowwise FP8/gfx1250、MXFP4 ROCm、KitchenW4A4、Nunchaku 路径。[Issue #46314](https://github.com/sgl-project/sglang/issues/46314)
8. **非因果滑动窗口注意力在 `TorchNativeAttnBackend` 中被忽略** —— 回退后端上的静默 attention mask 正确性问题。[Issue #39300](https://github.com/sgl-project/sglang/issues/39300)
9. **compressed-tensors：未目标化层报错 "Unable to find matching target"** —— 量化 checkpoint 加载问题。[Issue #46335](https://github.com/sgl-project/sglang/issues/46335)
10. **minimax‑m2 流式工具参数被覆盖** —— 当 delta 跨 `</invoke>` 时，下一次 invoke 的参数会覆盖当前调用的 arguments。[Issue #46293](https://github.com/sgl-project/sglang/issues/46293)

**正在修复中：** Triton 3.9 上使用 `swap_ab` 的 MoE block-scale 广播（[PR #45889](https://github.com/sgl-project/sglang/pull/45889)）；省略 `</function>` 时 qwen3_coder 流式参数未关闭（[PR #46218](https://github.com/sgl-project/sglang/pull/46218)）；hunyuan 流式字符串转义以匹配 `json.dumps`（[PR #46217](https://github.com/sgl-project/sglang/pull/46217)）；Anthropic 轮次中内联 system 消息原位附加以保留 prefix cache（[PR #42533](https://github.com/sgl-project/sglang/pull/42533)）；保留 modelopt 量化的 NextN draft，而不是静默反量化（[PR #44760](https://github.com/sgl-project/sglang/pull/44760)）。

**已关闭/已修复：** 当 `bs × draft_tokens × vocab ≥ 2^31` 时 rejection-sampling verify kernel 发生 int32 溢出（[Issue #46299](https://github.com/sgl-project/sglang/issues/46299)）。

**因不活跃而关闭（未修复）：** 使用 nvcc 12.8/CUDA 13 时 HiCache L3 segfault（[#36302](https://github.com/sgl-project/sglang/issues/36302)）；DeepEP/MXFP4 MegaMoE 在 Hopper 上崩溃并给出误导性的较晚错误（[#35557](https://github.com/sgl-project/sglang/issues/35557)）；hybrid‑GDN `--dtype float16` prefill 崩溃（[#31719](https://github.com/sgl-project/sglang/issues/31719)）；MegaMoE 多节点 CUDA 非法地址（[#36303](https://github.com/sgl-project/sglang/issues/36303)）；`--enable-deterministic-inference` 在 NVFP4 + hybrid Mamba + DFlash2 上无效（[#36291](https://github.com/sgl-project/sglang/issues/36291)）；`--config` 拒绝规范写法 `mamba-radix-cache-strategy`（[#36326](https://github.com/sgl-project/sglang/issues/36326)）。

**CI：** flaky-test tracker 仍是流量最高的 issue（128 条评论）（[#42752](https://github.com/sgl-project/sglang/issues/42752)）；AMD PR Test 失败跟踪器已创建（[#45847](https://github.com/sgl-project/sglang/issues/45847)）。

## 5. 这对应用开发者意味着什么

- **把 agentic 工具调用当作需要测试的面，而不是理所当然。** 流式工具参数 bug 正在按模型特定的解析器集中出现（qwen3_coder、hunyuan、minimax‑m2），并且 GLM‑5.3‑Flash 在多工具 prompt 上退化。如果你以流式方式处理工具调用，请在 `finish_reason` 时校验 `arguments` 是完整 JSON，并测试模型以 `</tool_call>` 结尾但省略 `</function>` 的情况。
- **Prefix-cache 稳定性对 Claude 兼容客户端很重要。** Anthropic 适配器曾将对话中段的 `system` 消息折叠进开头的 system block，导致 prefix cache 失效——这会直接增加多轮 agent 的延迟/成本。如果你在 SGLang 前面提供 Claude 风格 API，值得关注。
- **目前应避免在 `main` 上使用 DeepSeek‑V4 NVFP4 + `flashinfer_megamoe` + dp-attention**（CUDA graph capture 失败），并且对于 MXFP4 专家 checkpoint 应避免在 Hopper 上使用 DeepEP——该路径仅支持 SM100，并会在较晚阶段以误导性错误失败。在多节点 Hopper MegaMoE 部署中，优先使用 `--moe-a2a-backend deepep`。
- **在 GB300 多节点运行 Kimi‑K3 时，设置 `NCCL_GRAPH_REGISTER=0`**，直到 DCP a2a decode 损坏问题解决；该故障模式是文本乱码而非崩溃，因此可能漏过 smoke test。
- **固定 checkpoint 的加载器路径。** `sglang.kernel_api_logging` 缺失会破坏 DeepSeek AWQ 加载，而 `qwen4_exp` 没有 Transformers 注册——两者都会在启动时失败，而不是优雅降级。
- **AMD 用户可获得渐进式收益：** 在 gfx950 上 DeepSeek‑V4 的共享专家融合默认开启（MI355X TP4 上每 token 约降低 2–6.6%），但消费级 Radeon 仍不受支持，需等待 RDNA3/4 启用计划。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 简报 — 2026-10-25

## 1. 今日亮点

llama.cpp 发布了一连串密集的滚动构建（b11856–b11869），主要包括服务器入口重构、面向多核 HMX 的 Hexagon flash-attention 工作，以及 Minimax-M3 量化 scale 修复。服务器端还看到多项围绕 `/tools` 访问、grammar-trigger 校验和深层嵌套 JSON 的安全/正确性加固 PR。性能方面，MoE 专家缓存工作、常驻 GPU 的 KV 旋转常量以及 CUDA/NVFP4 kernel 调优正在进行中，同时仍有若干高严重级别的后端回归未解决。

## 2. 版本发布与破坏性变更

没有正式的 API 破坏性变更，但有几项行为变更值得在固定版本时留意：

- **b11867 — 服务器入口重构**引入 `server_role`，用于区分 router、single 和 download 模式。属于内部重构，但对任何修补服务器启动逻辑的人都有影响。[PR #31113](https://github.com/ggml-org/llama.cpp/pull/31113) · [release b11867](https://github.com/ggml-org/llama.cpp/releases/tag/b11867)
- **b11857 — 带索引 `-1` 的 `MUL_MAT_ID` 现在会跳过计算。** 后端实现者必须遵循新语义；Vulkan 和 WebGPU 路径已更新。[PR #26631](https://github.com/ggml-org/llama.cpp/pull/26631) · [release b11857](https://github.com/ggml-org/llama.cpp/releases/tag/b11857)
- **b11858 — 对于使用 MoE amax 重缩放的架构，`-sm tensor` 会被拒绝。** Tensor split-state 无法正确计算整行 amax 归约。这可能破坏受影响 MoE 模型现有的多 GPU 启动脚本。[PR #31081](https://github.com/ggml-org/llama.cpp/pull/31081) · [release b11858](https://github.com/ggml-org/llama.cpp/releases/tag/b11858)
- **b11869 — 修复 Jinja `select`/`selectattr` 参数处理。** 使用 `selectattr('type', 'defined')` 的模板（例如带 `oneOf` 的 Functionary v3.2）现在行为正确。[PR #31059](https://github.com/ggml-org/llama.cpp/pull/31059) · [release b11869](https://github.com/ggml-org/llama.cpp/releases/tag/b11869)
- **b11861 — Minimax-M3 量化 scale 已转发到计算图**，修复了缺失的逐专家 NVFP4 `scale2` 以及 MXFP8 稠密/共享专家 scale。[PR #31097](https://github.com/ggml-org/llama.cpp/pull/31097) · [release b11861](https://github.com/ggml-org/llama.cpp/releases/tag/b11861)

## 3. 新模型与硬件支持

- **Minimax-M3：** b11861 中修复了 NVFP4/MXFP8 专家的量化 scale 传递链路。[PR #31097](https://github.com/ggml-org/llama.cpp/pull/31097)
- **DeepSeek V4.1 Flash：** 模型支持 PR 已开放，涉及模型、mtmd、CUDA 和转换路径。[PR #31086](https://github.com/ggml-org/llama.cpp/pull/31086)
- **Hexagon / Snapdragon：** 当 `n_kv_heads` 不能被核心数整除时，多核 HMX flash attention 现在使用 KV-head 团队拆分，避免回退到 token 分区方案——该方案中每个核心都要遍历每个 KV head。[PR #31055](https://github.com/ggml-org/llama.cpp/pull/31055) · [release b11868](https://github.com/ggml-org/llama.cpp/releases/tag/b11868)
- **Hexagon：** 针对一元、二元和 matmul 运算的通用维度 collapse/reshape 已开放，改善了 HVX/多 NPU 分区。[PR #31071](https://github.com/ggml-org/llama.cpp/pull/31071)
- **CUDA：** 提出了旧版 Kepler cuBLAS 兼容性处理。[PR #30727](https://github.com/ggml-org/llama.cpp/pull/30727)
- **Vulkan：** 在缺少 `shader_64b_indexing` 的设备上，MoE 专家缓存加载崩溃已有修复 PR，该 PR 对非计算张量跳过 `maxStorageBufferRange` 预检查。[PR #31114](https://github.com/ggml-org/llama.cpp/pull/31114)
- **SYCL：** 修正了 f32→q2_0/q4_0/q5_0 的舍入，以消除间歇性 `CPY` 测试失败。[PR #31120](https://github.com/ggml-org/llama.cpp/pull/31120)
- **Metal：** 移除了 `mul_mm_id` 的源重缩放；按专家 token 的 amax 缩放现在在计算图中完成。[PR #30924](https://github.com/ggml-org/llama.cpp/pull/30924) · [release b11866](https://github.com/ggml-org/llama.cpp/releases/tag/b11866)
- **Laguna S 2.1 DFlash** 支持目前仍只是功能请求。[Issue #26669](https://github.com/ggml-org/llama.cpp/issues/26669)

## 4. 性能与优化

- **Hexagon FA 多核：** KV-head 团队拆分减少了 head 拆分回退场景中的冗余 KV 遍历。[PR #31055](https://github.com/ggml-org/llama.cpp/pull/31055)
- **MoE 专家缓存：** 提出对主机页进行 pinning，以避免缓存填充和大批量专家上传期间从可分页内存执行 `cudaMemcpyAsync`。[PR #31122](https://github.com/ggml-org/llama.cpp/pull/31122)
- **`llama-bench`：** 新增 `-mcmoe` / `--moe-cache-mib` 参数，可直接对 MoE 缓存大小进行 A/B 测试。[PR #31121](https://github.com/ggml-org/llama.cpp/pull/31121)
- **KV-cache 旋转：** 静态 Hadamard 矩阵常驻 GPU，而不再往返主机。作者报告在受 PCIe 限制的多 GPU 配置上有改善。[PR #31073](https://github.com/ggml-org/llama.cpp/pull/31073)
- **CUDA NVFP4 MMVQ：** 针对小 K、单 token 解码路径使用 lane-packed 行。[PR #30788](https://github.com/ggml-org/llama.cpp/pull/30788)
- **Vulkan 长上下文性能断崖：** 有报告称 131K 上下文下解码吞吐下降约 78%，原因是约 1 GiB 子分配碎片化；可通过 `GGML_VK_SUBALLOCATION_BLOCK_SIZE=4 GiB` 规避。[Issue #27734](https://github.com/ggml-org/llama.cpp/issues/27734)
- **RTX 5090 上的 CUDA FA stream-K：** 块数量不是 `ntiles_dst` 的倍数，报告称长上下文预填充比 RTX 4090 慢 10–13%，修复后最高约 40%。[Issue #30784](https://github.com/ggml-org/llama.cpp/issues/30784)
- **Draft-MTP 多 GPU 回归：** 层拆分时 prompt 处理大致减半，而单 GPU 不受影响。[Issue #27428](https://github.com/ggml-org/llama.cpp/issues/27428)
- **Draft-MTP over RPC：** 生成速度慢 25–30%，而 drafter 接受率不变。[Issue #28484](https://github.com/ggml-org/llama.cpp/issues/28484)

## 5. 稳定性与回归

按严重程度排序：

1. **CUDA 非法内存访问 — Gemma 4 31B + draft-MTP 在 KV 回滚/清空之后。** 当 MTP 在检查点恢复或完整重处理后立即进行 draft 时发生崩溃；关闭 MTP 可避免。未列出修复 PR。[Issue #31077](https://github.com/ggml-org/llama.cpp/issues/31077)
2. **CUDA graphs 在 RTX 5090 Laptop / sm_120 上挂起 GPU 通道。** 出现 RC watchdog 以及 Xid 8；`GGML_CUDA_DISABLE_GRAPHS=1` 是完整规避方案。[Issue #27330](https://github.com/ggml-org/llama.cpp/issues/27330)
3. **Metal 在 `MUL_MAT` 使用 BF16 src1 且 K ≥ 64、batch > 8 时中止。** 没有 BF16 `mul_mm` kernel；影响 Apple Silicon。[Issue #30973](https://github.com/ggml-org/llama.cpp/issues/30973)
4. **SYCL 在稠密 Qwen3.8-27B Q4_K_M 上输出 F16 NaN，自 IQ3 代码重排后出现。** 关闭 fused GEMM 可避免；该回归与 #29107 相关。[Issue #30892](https://github.com/ggml-org/llama.cpp/issues/30892)
5. **SYCL 在长 prompt 下出现 DeltaNet/Gated DeltaNet token soup**，涉及 Arc Pro B70 上的 Qwen3.6/3.8 和 Nemotron。[Issue #29183](https://github.com/ggml-org/llama.cpp/issues/29183) · [Issue #29167](https://github.com/ggml-org/llama.cpp/issues/29167)
6. **Hexagon 视觉编码器在 HTP v75 上损坏**，与图像形状有关，在 CPU 上正确。[Issue #31104](https://github.com/ggml-org/llama.cpp/issues/31104)
7. **Vulkan `--moe-cache-mib` 在 RX 6900 XT / Windows 上加载崩溃**；已有修复 PR。[Issue #30697](https://github.com/ggml-org/llama.cpp/issues/30697) · [PR #31114](https://github.com/ggml-org/llama.cpp/pull/31114)
8. **上下文检查点在混合/循环模型上总是失效。** 长会话 agent 工作负载会失去缓存复用。[Issue #24055](https://github.com/ggml-org/llama.cpp/issues/24055)
9. **服务器安全加固进行中/已落地：** `/tools` 跨源和非 JSON 请求拒绝已关闭；grammar-trigger 类型校验和 JSON 嵌套深度上限已提出。[PR #29834](https://github.com/ggml-org/llama.cpp/pull/29834) · [PR #31098](https://github.com/ggml-org/llama.cpp/pull/31098) · [PR #30242](https://github.com/ggml-org/llama.cpp/pull/30242) · [PR #31099](https://github.com/ggml-org/llama.cpp/pull/31099)
10. **其他正确性问题：** Sarvam 30B MoE 转换器错误标记 byte-fallback token [Issue #30879](https://github.com/ggml-org/llama.cpp/issues/30879)；Flint NVFP4 工具调用 XML 残留在推理内容中 [Issue #31000](https://github.com/ggml-org/llama.cpp/issues/31000)；RISC-V `vfloat32m8_t` 编译失败 [Issue #28986](https://github.com/ggml-org/llama.cpp/issues/28986)；MTP 在长会话后重复输出 `////` [Issue #23577](https://github.com/ggml-org/llama.cpp/issues/23577)；OpenVINO NPU 段错误 [Issue #25904](https://github.com/ggml-org/llama.cpp/issues/25904)。

## 6. 这对应用开发者意味着什么

- **谨慎固定构建版本。** 滚动标签 b11856–b11869 间隔很近，且包含行为变更（`MUL_MAT_ID -1`、拒绝 `-sm tensor`）。升级前请验证多 GPU MoE 启动脚本。
- **加固对外暴露的服务器。** 如果 `llama-server` 可被不受信任的客户端访问，请优先使用包含 `/tools` origin/content-type 检查以及 grammar-trigger 校验的构建。在嵌套深度上限落地之前，深层嵌套 JSON 是栈溢出攻击向量。
- **投机解码/MTP 仍是风险最高的功能领域。** 多 GPU 层拆分、RPC 拆分和 KV 回滚场景存在未解决的崩溃和吞吐回归。在验证你确切的模型/后端组合之前，生产环境请关闭 MTP。
- **聊天模板兼容性有所改善。** Jinja `selectattr` 修复解除了使用 `oneOf` 工具 schema 的模板阻塞，但 `from_json` 递归限制仍只是提案——不要接受来自不受信任调用者的无界 `chat_template_kwargs`。
- **MoE 缓存仍是实验性的。** Vulkan 崩溃以及尚待完成的 pinning/基准测试工作意味着缓存大小调优应被视为后端特定事项，并按平台进行基准测试。
- **后端一致性很重要。** SYCL 存在活跃的 NaN/token-soup 回归；Metal 存在 BF16 `MUL_MAT` 中止；Hexagon 视觉尚不可靠。当前生产推理请选择 CUDA 或 CPU 作为保守默认值。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-10-25

## 今日亮点

Ollama v0.40.11 发布了 **macOS 上的语音生成功能**，在 `ollama run` 音频播放之外，还提供了 OpenAI 兼容的 `/v1/audio/speech` 流式端点。在可靠性方面，补齐了三个长期存在的 OpenAI 兼容缺口：UUID 响应 ID（取代仅 999 个值、极易冲突的取值范围）、对 `max_completion_tokens` 的支持，以及将模型名上限提高到 96 字符，以支持拉取长 HuggingFace GGUF 模型名。与此同时，MLX 后端收到多项正确性问题报告（停止序列被忽略、逐层量化覆盖、混合精度内存统计）。

---

## 发布与破坏性变更

### v0.40.11 — 语音支持（macOS）
- 新增语音能力：`ollama run` 可在音频生成过程中将文本或聊天模型回复朗读出来。
- 应用可通过 OpenAI 兼容的 `POST /v1/audio/speech` 端点流式传输音频。
- 发布说明未披露破坏性 API 变更；仅为新增端点。
- 文档：https://docs.ollama.com/capabilities/speech · 发布：[v0.40.11](https://github.com/ollama/ollama/releases/tag/v0.40.11)

### 已合并的 API 兼容性修复（已关闭 PR）
- **OpenAI 兼容响应 ID 使用 UUID** — 取代 `rand.Intn(999)` 生成的 ID，这类 ID 在代理日志（LiteLLM 支出跟踪）中几乎立即发生冲突。[#18656](https://github.com/ollama/ollama/pull/18656)
- **接受 `max_completion_tokens`** — 发送该字段的较新 OpenAI 客户端此前会静默丢弃生成上限；现已映射到 `num_predict`。[#18608](https://github.com/ollama/ollama/pull/18608)
- 无需迁移步骤；行为向后兼容。

---

## 新模型与硬件支持

- **Nemotron 3.5 Bolt 74B-A9B（MLX）** — 新增模型支持，包含逐层专家配置、带逐模块量化元数据的预量化 affine 3-bit 导入，以及通过 DFlash runner 实现的混合精度专家堆叠。[#19229](https://github.com/ollama/ollama/pull/19229)
- **长 HF GGUF 模型名** — 模型名上限从 80 个字符提高到 96 个字符，使 HuggingFace 仓库/量化后缀（例如 `Qwen3.8-...-IQ3_M.gguf`）在拉取时能通过校验。[#18520](https://github.com/ollama/ollama/pull/18520)
- **Gemma 4 紧凑视觉塔** — 修复小图像网格尺寸设置，以保留预期的 70 token 预算，从而改善小图像上的细节保留。[#19247](https://github.com/ollama/ollama/pull/19247)

---

## 性能与优化

- **多模型 VRAM 统计修复** — 此前 llama-server 缓冲区行去重时假设每个子进程只有一个模型，导致投机草稿模型的行替换了目标模型的行；一个 30B DFlash 模型在 `ollama ps` 和调度器统计中仅报告约 2.3 GB。修复进行中。[#17857](https://github.com/ollama/ollama/pull/17857)
- **日志分块内存统计** — 管道读取可能拆分 llama-server 日志行，导致缓冲区大小和层数丢失；修复会保留不完整行，并在统计锁下解析完整行。[#19263](https://github.com/ollama/ollama/pull/19263)
- **MLX 与 llama.cpp 吞吐量回退** — 在 M3 MacBook Pro（36 GB）上，`flint:40b-a2b-gguf-nvfp4`（MLX）测得明显慢于 `flint:40b-a2b-qat-int4`；issue 已关闭。[#19254](https://github.com/ollama/ollama/issues/19254)

---

## 稳定性与回归

按严重程度排序：

1. **导入时忽略 MLX 逐层量化覆盖** — 混合精度 MLX safetensors（4-bit 权重 + 逐层 8-bit 覆盖）会因 `quantized_matmul` 形状不匹配而加载失败；runner 将全局 `bits` 值应用到为覆盖项构建的张量上。尚无修复 PR。[#18789](https://github.com/ollama/ollama/issues/18789)
2. **MLX 引擎忽略 `options.stop`** — 与 llama.cpp 路径不同，生成会一直运行到 EOS 或 `num_predict`。修复 PR 已打开：[#19267](https://github.com/ollama/ollama/pull/19267)。Issue：[#19268](https://github.com/ollama/ollama/issues/19268)
3. **Anthropic 兼容前缀缓存失效** — `/v1/messages` 会将 `messages` 内 `system` 角色的消息提升到顶部 system 块，破坏 Claude Code 的前缀缓存（后者在每次工具结果后追加一条 system 消息）。[#18431](https://github.com/ollama/ollama/issues/18431)
4. **`OLLAMA_NUM_PARALLEL=1` 似乎无效** — 两个并发会话仍会同时处理，在内存预算紧张时存在问题。[#19262](https://github.com/ollama/ollama/issues/19262)
5. **云端：通过 OMP 访问 Beam 返回空响应** — 通过 OMP 访问 Ollama Cloud Beam 模型时，间歇出现空的助手回复，而同一模型通过 OpenCode Go 可正常工作。[#19270](https://github.com/ollama/ollama/issues/19270)
6. **工具调用整数参数被渲染为小数** — 诸如 `1` 的 JSON 参数会变成 `1.0`，改变模型看到的内容；修复 PR 保留原始数值表示。[#19252](https://github.com/ollama/ollama/pull/19252)
7. **OpenAI 端点忽略原生 `think` 参数** — 通过 `/v1/chat/completions` 和 `/v1/completions` 访问 Gemma 4 及其他思考模型时，`"think": false` 不生效；修复正在审查中。[#19260](https://github.com/ollama/ollama/pull/19260)
8. **大型 Codex 会话中的 `/compact` 失败** — 请求体 413 会中止压缩；重试逻辑会丢弃图像和旧历史以恢复。[#19266](https://github.com/ollama/ollama/pull/19266)
9. **Linux 安装器误报成功** — `install.sh` 将下载内容流式传输到 `sudo tar`；在慢速链路上，sudo 缓存会在下载中途过期，且失败未被报告。修复会先下载归档并检查退出码。[#19243](https://github.com/ollama/ollama/pull/19243)

---

## 这对应用开发者意味着什么

- **语音现已成为一等模态。** 如果你正在构建语音代理或无障碍功能，请以 OpenAI 兼容的 `/v1/audio/speech` 端点为目标，以获得可移植性。它目前仅限 macOS，因此请为 Linux/Windows 主机规划回退方案。
- **OpenAI 客户端兼容性显著改善。** 升级到包含今日合并项的版本可修复可观测性/支出代理中的响应 ID 冲突，兼容发送 `max_completion_tokens` 的客户端，并让长 HF GGUF 量化后缀顺利拉取。如果你仍在使用 v0.40.11，请在下一个带标签发布中检查这些修复。
- **如果你使用 Apple silicon + MLX，请保持防御性。** 停止序列会被静默忽略（[#19268](https://github.com/ollama/ollama/issues/19268)），混合精度 MLX 导入可能加载失败（[#18789](https://github.com/ollama/ollama/issues/18789)）。请在客户端强制执行轮次限制（`num_predict`），并在停止序列正确性至关重要时优先使用 llama.cpp 路径。
- **Anthropic 兼容用户应避免在 `messages` 内使用 `system` 消息。** 在 [#18431](https://github.com/ollama/ollama/issues/18431) 修复前，这种模式会破坏前缀缓存，并推高 Claude Code 等代理循环的 token 消耗。请将系统提示保留在顶层 `system` 字段中。
- **多模型/投机解码的内存报告正在修正。** 如果你基于 `ollama ps` 的 VRAM 数字进行自动扩缩容或调度，在 [#17857](https://github.com/ollama/ollama/pull/17857) 和 [#19263](https://github.com/ollama/ollama/pull/19263) 落地前，预计会出现低报（例如 30B DFlash 仅约 2.3 GB）——目前不要将这些数字用于容量规划。
- **思考模型在 OpenAI 端点上需要原生字段。** 那里尚不遵守 `"think": false`；如果你需要为延迟敏感路由禁用推理，请在 [#19260](https://github.com/ollama/ollama/pull/19260) 合并前使用原生 API。

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-10-25

## 1. 今日亮点

一起供应链事件占据今日主导：**litellm 1.107.0 从仿冒仓库（`Berri-AI/litellm`）发布，并携带了凭据窃取程序以及 `sitecustomize` RCE 加载器**；PyPI 以及所有指向该构建的 Docker 标签均已被撤下（[#47826](https://github.com/BerriAI/litellm/issues/47826)）。另外，四个补丁版本已发布（1.106.3 / 1.105.4 / 1.104.6 / 1.103.8），它们均包含 **cosign 镜像签名密钥轮换**，这将破坏现有的签名验证流水线。一组支出核算与预算执行缺陷持续累积评论，并有多项活跃修复 PR。

## 2. 发布与破坏性变更

- **v1.106.3、v1.105.4、v1.104.6、v1.103.8**（[发布](https://github.com/BerriAI/litellm/releases)）——所有 backport 分支均在 24 小时内发布。每个版本唯一记录在案的变更是**镜像签名密钥轮换**：现在每个 Docker 镜像和 Helm chart 都使用来自提交 [`e82a3c6`](https://github.com/BerriAI/litellm/commit/e82a3c646908ba0a6ee0beddc4310d7a6cac4065) 的新 cosign 密钥签名。**行动项：**在升级前更新 `cosign verify` 密钥材料 / Kyverno 或 Connaisseur 策略，否则准入控制将拒绝新镜像。
- **不要安装 1.107.0。** 它不是合法发布——见下文“稳定性”。1.106.3 是当前最高可信标签；请据此固定版本并验证已发布产物的哈希。
- 除签名密钥外，今天未宣布任何 API 或配置 schema 变更。

## 3. 新模型与硬件支持

- **W&B 上的 NVIDIA Nemotron 3.5 Super VL 120B-A12B**——重新定价，采用 CoreWeave Forge Serverless Inference 费率，而非过时的 OpenRouter CoreWeave 端点价格（[#48264](https://github.com/BerriAI/litellm/pull/48264)）。
- **Cerebras Llama 3.1 带连字符别名**——将 `cerebras/llama-3.1-8b` / `-70b` 加入价格/上下文映射（每 1M 输入+输出 $0.10，128k 上下文）（[#48178](https://github.com/BerriAI/litellm/pull/48178)，[#48158](https://github.com/BerriAI/litellm/pull/48158)）。
- **JSON 定义的 OpenAI 兼容提供商上的未编目模型现在会保留 `tools`**——一个修复 PR 恢复了目录中不存在的模型的工具传递，而不是将缺失的能力声明视为不支持（[#48267](https://github.com/BerriAI/litellm/pull/48267) → [#48145](https://github.com/BerriAI/litellm/issues/48145)）。
- **未编目的 ChatGPT 模型通过 Responses 桥接路由**，而不是在 Chat Completions 上返回 404（[#48244](https://github.com/BerriAI/litellm/pull/48244)）。
- **Bedrock Nova 2 Lite**：无效的 `apac.` 地理推理 ID 已替换为 `jp.`（[#33583](https://github.com/BerriAI/litellm/pull/33583)，已过期）。
- **OpenAI 12 小时提示缓存写入定价**已为 Batch 和 Ultrafast 层级解析（[#48259](https://github.com/BerriAI/litellm/pull/48259)）。
- **Azure 转写**：实时转写会话迁移到 GA 的 `client_secrets` 端点并修正路径（`/openai/realtimeapi/transcription_sessions`），修复 404（[#48067](https://github.com/BerriAI/litellm/pull/48067)）。

## 4. 性能与优化

今日窗口内没有吞吐量或内核级工作。相关的资源生命周期与正确性修复：

- **Anthropic `/v1/messages` 流现在会在首个 token 前客户端断开时关闭上游请求**，防止上游 socket/连接泄漏（[#48220](https://github.com/BerriAI/litellm/pull/48220) → [#48197](https://github.com/BerriAI/litellm/issues/48197)）。
- **在 `/claude_code_gateway` 基础 URL 上恢复 Claude 网关模型发现**（此前为 404，迫使客户端双重配置基础 URL）（[#48221](https://github.com/BerriAI/litellm/pull/48221)，[#48205](https://github.com/BerriAI/litellm/pull/48205)）。
- **`mock_response=""`** 现在会短路到 mock 路径，而不是发出真实提供商请求——对测试套件延迟和意外支出意义重大（[#48266](https://github.com/BerriAI/litellm/pull/48266)）。
- **`/v2/team/list` 返回每团队的 `caller_membership`**（支出、有效预算、重置日期），消除每个团队的 N+1 UI 查询（[#45674](https://github.com/BerriAI/litellm/pull/45674)）。

## 5. 稳定性与回归

**严重——供应链 / 安全**
- **1.107.0 已被入侵**：`cook.pth` 凭据窃取程序 + `sitecustomize` RCE 加载器，从仿冒仓库 `Berri-AI/litellm` 构建。PyPI 已移除 1.107.0；`ghcr.io/berriai/litellm{,-database,-non_root}` 和 `docker.io/litellm/litellm` 上的 `latest`、`main-latest`、`main-stable` 不再解析到它（[#47826](https://github.com/BerriAI/litellm/issues/47826)，33 条评论，19 👍）。**如果你在 2026-10-21 拉取过 `:latest`，请将宿主机凭据、`~/.aws`、`~/.ssh`、云 token 以及任何 env-secret 材料视为已泄露并轮换。**

**高——预算执行与支出核算**
- 尽管支出已超出，v1.82.3 中 key/user `max_budget` 预算仍被**绕过**（[#26672](https://github.com/BerriAI/litellm/issues/26672)）。
- `BudgetExceededError` 因陈旧支出数据抛出，而 `/key/info` 报告未超预算（[#27735](https://github.com/BerriAI/litellm/issues/27735)）。
- 超预算的 key **在空闲约 60 秒后被重新放行**，直到批处理写入器刷新支出（[#43732](https://github.com/BerriAI/litellm/issues/43732)）。
- **持续负载下误报 429**（“Current cost” = max_budget + 近期支出，无 Redis，约 2 分钟后自愈）（[#36926](https://github.com/BerriAI/litellm/issues/36926)）。
- **项目预算被排除**在原子化调用前预留路径之外——团队/组织/key 已覆盖，项目仅在读取时生效（[#34101](https://github.com/BerriAI/litellm/issues/34101)）。
- `max_end_user_budget_id` 从未持久化到 `LiteLLM_EndUserTable`，因此预算重置任务会跳过自动创建的终端用户（[#25386](https://github.com/BerriAI/litellm/issues/25386)）。

**高——Agent 工具调用回归**
- 在 1.106 上，当模型不在目录中时，JSON 定义的 OpenAI 类提供商**会静默丢弃 `tools`**（在 1.81 上以及重新声明为 `openai` 时正常）。这是 PR #21244 引入的回归；**已有修复 PR**（[#48145](https://github.com/BerriAI/litellm/issues/48145) → [#48267](https://github.com/BerriAI/litellm/pull/48267)）。
- `mcp_semantic_tool_filter` 对标准嵌套 `tools` 格式会剥离**所有**工具（N→0）——已关闭（[#31909](https://github.com/BerriAI/litellm/issues/31909)）。

**中——成本准确性（计费漂移）**
- 对于 OpenAI 别名的自定义提供商（例如 `groq/openai/gpt-oss-120b`），`completion_cost()` 会抛出 `ModelNotMappedError`；两个相互竞争的修复 PR 均保留提供商身份并遵循显式参数（[#48226](https://github.com/BerriAI/litellm/issues/48226) → [#48262](https://github.com/BerriAI/litellm/pull/48262)，[#48263](https://github.com/BerriAI/litellm/pull/48263)）。
- 当 `provider_response_model` 是价格映射中缺失的 slug 时，轮次按 **$0** 计费——一份报告中有 89 次可计费流式调用中的 74 次被记录为免费（[#42161](https://github.com/BerriAI/litellm/issues/42161)）。
- 当最终 chunk 的 **`choices` 数组非空**时，流式处理会丢弃上游 `usage`——`cached_tokens` 丢失，并按完整输入费率计费（[#36168](https://github.com/BerriAI/litellm/issues/36168)）。
- 客户端在流中途断开时（[#14457](https://github.com/BerriAI/litellm/issues/14457)），或非流式 Bedrock 调用期间（[#13245](https://github.com/BerriAI/litellm/issues/13245)），用量会完全丢失。
- 响应缓存命中会写入 `spend = 0`，但重放原始 token 列——token 报告聚合的口径需要你刻意选择（[#39057](https://github.com/BerriAI/litellm/issues/39057)）。
- 复用的提供商响应 ID 会作为支出日志主键发生冲突（[#35563](https://github.com/BerriAI/litellm/issues/35563)）；共享 key 上 `end_user` 被固定为第一个请求的 `user`——这是 v1.87.0 的回归（[#31441](https://github.com/BerriAI/litellm/issues/31441)）。
- 原生 decisions 调用上的 null 用量被报告为零 token / $0 成本，与未计费无法区分——修复 PR 保持用量为未知（[#48261](https://github.com/BerriAI/litellm/pull/48261)）。

**低——转换 / 兼容性**
- `get_supported_openai_params` 仍为 Claude Opus 4.7 宣称支持 `temperature`，而 Anthropic 会以 `temperature is deprecated for this model` 拒绝（[#26444](https://github.com/BerriAI/litellm/issues/26444)）。
- 流式模式下 `usage.cost` 中的 OpenRouter 成本仍会丢失——已关闭（[#16021](https://github.com/BerriAI/litellm/issues/16021)）。
- Router `.acompletion` 仍不会触发 `CustomLogger` 钩子——因过期而关闭，未修复（[#8842](https://github.com/BerriAI/litellm/issues/8842)）。
- **三天警告：**OpenCode Go 将从 09/05 起拒绝缺少 `x-opencode-session` 的请求；LiteLLM 不会发送它（[#39503](https://github.com/BerriAI/litellm/issues/39503)）。

## 6. 这对应用开发者意味着什么

- **立即固定并验证。** 按 digest 而非按 tag 迁移到 `1.106.3`（或你所在次版本线的最新补丁），生产环境绝不要解析 `:latest`。先更新你的 cosign 公钥——`e82a3c6` 处的轮换将在大多数准入流水线中以 fail closed 方式失败。如果你在 2026-10-21 运行过 1.107.0，请轮换该容器可访问的所有凭据。
- **预算限制在若干路径上只是建议性的，而非强制执行。** Key/团队预算可能被超出或被误触发；项目预算和自动创建的终端用户预算不在原子预留路径中。如果你转售或基于支出做门禁，请添加上游独立配额（提供商侧限制），而不要仅依赖 LiteLLM 的调用前检查。
- **不要信任流式场景的每请求成本。** 当价格映射缺失 slug、最终 chunk 带有 `choices`、以及客户端断开时，会出现零成本记录。请与提供商发票对账，并在 #48262/#48263 修复落地前，优先在 `completion_cost()` 中使用显式的 `model` / `custom_llm_provider` 参数。
- **在 1.106 上使用 JSON 定义 OpenAI 兼容提供商的 Agent 构建者应关注 #48145**——未编目模型的工具会被静默剥离，并以令人困惑的方式 fail closed（模型仍会响应，只是没有工具调用）。在 #48267 合并前，要么将模型声明在 `openai` 下，要么将其加入目录。
- **对于断连频繁的工作负载**（聊天 UI、可取消的 Agent），#48220 和 #48261 是两个值得跟踪的修复：一个阻止 Anthropic 流上的上游 socket 泄漏，另一个阻止将缺失用量静默报告为 $0。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 摘要 — 2026-10-25

## 今日亮点
过去 24 小时内没有新版本发布；动态主要由 **Windows 上严重的 Studio GGUF 加载回归**（两份重叠报告，均仍处于开启状态）和 **多 GPU 训练的大规模推进** 主导，其中包括流水线并行 v1 与张量并行 v2。在服务侧，让并行聊天共享单个 llama-server 缓存的 KV 抢占工作仍在持续演进，并由 llama-server 接管 parking 的归属。

## 发布与破坏性变更
- **过去 24 小时内无新版本。** 无迁移说明。
- ⚠️ 根据多份报告，**Studio `v0.1.910-beta` 在 Windows 上加载本地模型时实际上已不可用**——见“稳定性”。应将其视为 2026-10-23 更新引入的破坏性行为变更，而非正式发布说明。
- **行为变更（进行中）：Studio 现在会在启动 llama-server 时强制设置 `preserve_thinking=true`**，这会覆盖用户提供的聊天模板，并使 `--no-reasoning-preserve` 变成空操作：[Issue #15115](https://github.com/unslothai/unsloth/issues/15115)。这会影响所有为推理模型固定自定义模板的用户。
- **API 接口说明：** 嵌入模型通过 `/embeddings` 提供服务，但不会出现在 GUI 或 `/v1/models` 列表中：[Issue #15110](https://github.com/unslothai/unsloth/issues/15110) —— 这对 OpenAI 兼容网关是发现性/兼容性问题。
- **Docker：** 每次容器更新都会重新下载 vLLM/SGLang；关于通过卷或环境变量触发重新下载来实现持久化的请求已被关闭：[Issue #15016](https://github.com/unslothai/unsloth/issues/15016)。

## 新模型与硬件支持
- **流水线并行 v1** — 通过 `torch.distributed.pipelining`（GPipe 和 1F1B 调度）为稠密解码器实现 `apply_pipeline_parallel`，位于 `unsloth/models/pp_native.py`，与 FSDP2（`shard_model`）和 TP（`apply_tensor_parallel`）API 对齐，且无需针对每种架构写代码：[PR #15175](https://github.com/unslothai/unsloth/pull/15175)。
- **张量并行 v2** — 新增分片加载、数据并行 × 张量并行组合以及 Trainer 集成；现在一个 16-bit 8B LoRA 可在 **2× 16 GB GPU** 上使用 TP 加载和训练：[PR #15174](https://github.com/unslothai/unsloth/pull/15174)。
- **Metal / Apple Silicon 稳健性** — 在 `--fit-target` 中预留 DSpark/DFlash sidecar，并在 Metal OOM 后恢复，而不是让 llama-server 永久处于失败状态：[PR #14431](https://github.com/unslothai/unsloth/pull/14431)。
- **Wan 视频** — 流式编码现在可在模型/顺序卸载下工作（此前要求渲染开始时解码器权重常驻 CUDA），从而扩展了 T4 等低显存 fp16 路径：[PR #15173](https://github.com/unslothai/unsloth/pull/15173)。
- **蒸馏路线图（讨论，无代码）：** 离线 top-k teacher logprobs 和服务化教师模型，以避免 DeepSeek/Kimi-K2 级教师模型占用训练内存：[Issue #11556](https://github.com/unslothai/unsloth/issues/11556)。

## 性能与优化
- **并行聊天的 KV 抢占** — 一个 llama-server 持有一个 KV 缓存；Studio 以 `--parallel` 启动，当 llama-server 支持 `--preempt-ram` 时由其接管 parking，让并发聊天共享单个缓存，而不是复制多份：[PR #10301](https://github.com/unslothai/unsloth/pull/10301)（开启中、长期运行、堆叠变更）。
- **Wan 文本编码缓存** — 提示缓存现在分别以正向和负向提示作为键，因此未变化的负向提示只会编码一次，而不是每个新提示都重新编码；这对流式 UMT5 设置（T4 级仅 fp16 显卡）很有意义，因为在这些设置中稠密编码器无法与 DiT 并存放置：[PR #15166](https://github.com/unslothai/unsloth/pull/15166)。
- **流式 VAE 解码 + 编码重叠** 用于内存压力下的 Wan 片段：[PR #15173](https://github.com/unslothai/unsloth/pull/15173)。
- 通过 PP v1 / TP v2（见上文）实现的多 GPU 扩展是本周期落地的主要内存容量杠杆：[PR #15175](https://github.com/unslothai/unsloth/pull/15175), [PR #15174](https://github.com/unslothai/unsloth/pull/15174)。

## 稳定性与回归
按严重程度排序；所有问题都会影响 Studio/Desktop 部署的用户。

1. **严重 — 每次 GGUF 加载都被错误的端口归属检查终止（Windows）。** 在 2026-10-23 更新后，Studio 启动 llama-server，服务器加载模型并返回 `/health` 200，然后 Studio 以 `llama-server failed to start. Check that the GGUF file is valid…` 将其终止。根因是后端日志中新增的 “our child is not listening” 检查。**开启中，4 条评论，尚未确定修复 PR：** [Issue #15022](https://github.com/unslothai/unsloth/issues/15022)。
2. **严重 — 所有测试过的模型都无法在 Desktop `v0.1.910-beta`（Windows 11 Insider 25H2）上加载。** 可能与上述问题根因相同；另行报告。**开启中：** [Issue #15142](https://github.com/unslothai/unsloth/issues/15142)。
3. **高 — 在 llama.cpp `b11696` 升级后，Windows AMD `gfx1201` 出现 ROCm 启动失败**，并伴随显示器闪烁和系统硬锁定。**已关闭**，因此推测已处理——请对照你固定使用的 llama.cpp 构建进行验证：[Issue #14085](https://github.com/unslothai/unsloth/issues/14085)。
4. **高 — 工具密集型长对话会在 CPU 对话归档嵌入阶段卡住数分钟，之后 llama.cpp 才收到任何推理请求**，并且 **Stop 不会取消归档**。**已关闭：** [Issue #14519](https://github.com/unslothai/unsloth/issues/14519)。
5. **中 — Studio 中图像生成出现 core dump：** [Issue #15172](https://github.com/unslothai/unsloth/issues/15172)。**中 — 嵌入模型从 GUI 和 `/v1/models` 中消失**，但仍可通过 `/embeddings` 响应：[Issue #15110](https://github.com/unslothai/unsloth/issues/15110)。
6. **中 — 启动时强制设置 `preserve_thinking`** 会覆盖用户聊天模板，并使 `--no-reasoning-preserve` 失效：[Issue #15115](https://github.com/unslothai/unsloth/issues/15115)。该设置在卸载/重新加载后也无法持久化：[Issue #14743](https://github.com/unslothai/unsloth/issues/14743)。
7. **低 — Model Hub 筛选器回归**（已关闭）：[Issue #14221](https://github.com/unslothai/unsloth/issues/14221)。刷新后助手消息不可编辑：[Issue #15004](https://github.com/unslothai/unsloth/issues/15004)。
8. **修复进行中（低严重性，仅验证）：** 在模型加载前预先拒绝 `[0, 2**32 - 1]` 范围之外的训练 `random_seed`，而不是在模型加载后才拒绝——[PR #15176](https://github.com/unslothai/unsloth/pull/15176)。

另请注意：24 小时内有 180 个 PR 更新，主要是 dependabot 的例行更新（setuptools、dompurify、vite、katex、tauri、codeql-action、pip group），另有一批 Studio UI 一致性 PR（radius tokens、按钮内边距、存储概览、固定 Run-settings 页脚、API 页面字体）。

## 对应用开发者的影响
- **现在不要在 Windows 上自动更新 Unsloth Studio/Desktop。** 2026-10-23+ 的构建通过错误进程存活检查彻底破坏了本地 GGUF 服务——在 #15022/#15142 关闭之前，请固定使用 2026-10-23 之前的构建。这会影响任何依赖 Studio 作为本地 OpenAI 兼容端点的应用。
- **不要依赖 `/v1/models` 进行能力发现。** 嵌入模型在 `/embeddings` 上可用，但没有出现在模型列表中；从 `/v1/models` 构建路由表的网关/路由器会静默丢弃你的嵌入层级。请直接探测 `/embeddings` 或硬编码：[Issue #15110](https://github.com/unslothai/unsloth/issues/15110)。
- **远程提供商仍不支持自定义上游请求头**，这阻碍了 LiteLLM 风格的按请求跟踪和功能选择请求头。如果你需要归属或路由元数据，请在 Unsloth 前置的自有代理中处理掉：[Issue #14811](https://github.com/unslothai/unsloth/issues/14811)。
- **推理 token 的处理并不完全由你控制。** Studio 会注入 `preserve_thinking=true`，覆盖自定义聊天模板并使 `--no-reasoning-preserve` 失效。如果你依赖剥离或保留 thinking 块，请在服务端验证渲染后的提示词，而不要假设你的模板胜出：[Issue #15115](https://github.com/unslothai/unsloth/issues/15115)。
- **长智能体上下文是延迟陷阱。** 对话归档嵌入在 CPU 上运行并阻塞推理，而且 Stop 不会取消它——请为工具密集型长线程上的数分钟卡顿预留预算，或在客户端限制历史长度：[Issue #14519](https://github.com/unslothai/unsloth/issues/14519)。
- **多 GPU 微调正变得实用，且无需架构特定代码。** TP v2 + PP v1 意味着 8B LoRA 可放入 2× 16 GB，更深的流水线也只差一次调用——如果你受限于单卡显存容量，值得现在就开始做原型：[PR #15174](https://github.com/unslothai/unsloth/pull/15174), [PR #15175](https://github.com/unslothai/unsloth/pull/15175)。
- **多主机调度仍未实现**（功能请求，8 👍）：[Issue #4858](https://github.com/unslothai/unsloth/issues/4858) —— 目前请在容器/主机层规划编排。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*