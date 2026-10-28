# AI 基础设施日报 2026-10-28

> 生成时间: 2026-10-28 01:35 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目 AI 基础设施对比 — 2026-10-28

> 范围：vLLM、SGLang、llama.cpp、Ollama、LiteLLM、Unsloth。  
> `n/r` = 提供的 24 小时摘要中未报告。SGLang 是唯一提供明确 24 小时 issue/PR 数的项目。

## 1. 生态概览

生态系统目前处于硬件认证与正确性加固阶段，而非大版本发布阶段。推理服务引擎正在竞相覆盖 DeepSeek-V4、Qwen3.8、Kimi-K3、GLM-5.x，以及 SM120、ROCm MI355X 和 GB300 等新加速器目标，但若干高严重度问题属于静默正确性缺陷或进程终止类 bug，而非吞吐量回退。本地运行时正在快速吸收新的 LiquidAI LFM3 系列，尤其是 llama.cpp；Ollama 随后跟进修复和 MLX 并发工作。网关层仍在发布补丁，但仍存在安全补丁回溯缺口和计费正确性问题。微调工具聚焦于 Studio 加固和 FP8/Block Swap 修复，新模型支持落后于服务引擎。

## 2. 活跃度对比

| Project | Issues count (24h) | PR count (24h) | Release status | Activity signal |
|---|---:|---:|---|---|
| **vLLM** | n/r | n/r | 无发布；无破坏性变更 | 硬件/模型覆盖增加，同时存在严重回归。#50576 有 121 条评论；#56892 是严重的 SM120 CUDA-graph/decode 问题。 |
| **SGLang** | **63** 个已更新 | **500** 个已更新 | 无发布 | 路线图/跟踪器整合，以及大规模 `[inactive]` 陈旧项清理。#43412 是严重的 spec-decode 崩溃，#45755 是 Kimi-K3 SM90 静默 bug。 |
| **llama.cpp** | n/r | n/r | 持续构建 **b11930–b11944**；无正式发布 | LFM3 系列已在文本/视觉/音频/omni 上落地；reasoning-effort 发现和 router 内存上限已交付。 |
| **Ollama** | n/r | n/r | 无发布 | LFM3 GGUF 修复、MLX 并发生成、CUDA/Blackwell 启动缓解。多个 macOS/MLX 和工具循环问题尚未解决。 |
| **LiteLLM** | n/r | n/r | **4 个补丁发布**：v1.106.5、v1.105.6、v1.104.8、v1.103.10；main 为 1.109.0；1.108.0 为 RC | 1.106.x 镜像的安全补丁回溯仍未完成；多项流式/计费和 slot 泄漏修复。 |
| **Unsloth** | n/r | n/r | 无发布 | Studio 加固；Qwen3.8-Flash-Next 的 FP8/Block Swap 修复；Windows 回收站和 Anthropic 自动压缩回归。 |

## 3. 模型支持竞赛

| Project | New model / architecture support today | Assessment |
|---|---|---|
| **vLLM** | SM8x 上的 DeepSeek-V4-Flash 仍是开放请求，尚未支持；Kimi NVFP4 PR；Nemotron-Nano 工具解析器；AMD CDNA W4A16；ROCm Inv-RoPE FP8；Qwen2.5-Omni CPU offload | 数据中心覆盖广泛，但 **Ampere 用户仍无法运行 DeepSeek-V4-Flash**。SM120 上的 DeepSeek-V4.1-Flash 是严重风险。 |
| **SGLang** | Nemotron-3-Embed 原生支持；DeepSeek-V4 的混合 NVFP4/MXFP4 draft experts；NPU DSpark 紧凑模式；AMD RDNA3/RDNA4 跟踪；Apple Silicon RFC | 在数据中心/PD 和加速器特定路径上较强，但 Kimi-K3 SM90 MXFP4 存在 **静默激活函数替换** bug。 |
| **llama.cpp** | LFM3-1.7B、LFM3-VL-2B、LFM3-Audio-2B、LFM3-Omni-2B；LiquidAI d1-2B/d1-omni-2B；LFM3 的视频输入；`/v1/realtime` 工具调用；GigaChat 3.5 432B-A28B 已提议 | **目前 LFM3 支持最领先**，包括多模态、音频/omni 和实时工具链路。 |
| **Ollama** | 在 `unknown model architecture: 'lfm3'` 后修复 LFM3 GGUF；Nemotron 3.5 Bolt 74B-A9B；Kolibri 1 MLX；MLX 并发生成；CUDA/Blackwell 启动修复 | 在 LFM3 上跟随 llama.cpp；本地运行时打包能力强，但 MLX 和 CUDA 启动回归仍然存在。 |
| **LiteLLM** | 不是推理运行时。Databricks OpenJev 定价修复；Cloudflare Workers AI 扩展仍停滞；Tavily 透传 | 模型支持意味着 **provider/pricing/API 覆盖**，而非架构启用。 |
| **Unsloth** | 仅有 LFM3 请求，无实现 PR；Qwen3.8-Flash-Next FP8/Block Swap 修复；Intel Arc XPU 请求；AMD 混合 Linux 检测问题；ExLlamaV3 请求 | 微调支持 **落后于新模型启用**；Qwen3.8-Flash-Next 是修补最活跃的架构。 |

**领先者：**  
- **数据中心服务：** vLLM 和 SGLang 在 DeepSeek-V4、Qwen3.8、Kimi-K3、GLM-5.x、ROCm MI355X 和 SM120/GB300 路径上领先。vLLM 的硬件/模型覆盖更广；SGLang 在 PD 分离、缓存感知路由和加速器特定内核上更激进。  
- **本地/边缘运行时：** llama.cpp 在 LFM3 和本地多模态/实时方面领先；Ollama 位居第二，但仍在修复 LFM3 GGUF/MLX 问题。  
- **微调：** Unsloth 在加速方面较强，但尚未交付 LFM3 支持；Qwen3.8-Flash-Next 修复是主要的新架构工作。

## 4. 性能前沿

| Optimization area | Where effort is concentrated | Representative items |
|---|---|---|
| **KV 缓存、前缀缓存与卸载** | 内存压力、PD 陈旧 KV 正确性、前缀共享与缓存卸载可扩展性 | vLLM P2P KV 卸载快速失败、投机解码下的 KV-pool 崩溃、multi-tier/GDS RFC；SGLang HiCache ack 队列排空、MLA+FP8 KV 物化占 **decode step 的 43.7%**；llama.cpp `--slot-attach` 前缀共享；Qwen3.8-Flash-Next 上的 Unsloth Block Swap 卸载。 |
| **内核与量化** | FP8/FP4/W4A16 内核、MoE 专家路径、small-M GEMM 和后端特定内核 | vLLM SSM GDN 原地状态（**+10–13% 吞吐**，每步流量最多减少 **36 GiB**）、CDNA W4A16、ROCm Inv-RoPE FP8、MI355X 跟踪项；SGLang 面向 GLM-5.2 block-FP8 的 W8A16 small-M GEMM（**每 token 延迟最多降低 6.8%**）、DeepGEMM `fp8_fp4`、GDN Triton 修复；llama.cpp MoE 专家缓存（**+6% QFN，+7% GLM**）、Hexagon TOP_K、CUDA-graph cluster-dim 重新实例化。 |
| **批处理、调度与分布式服务** | PD 分离、DP/EP 协调、弹性 EP、缓存感知路由 | vLLM 在 MoE+DP 下的 NCCL `reduce_scatter` 死锁、弹性 EP 中重复 CUDA-graph 捕获、CPU offload 的 PP-rank 不一致；SGLang PD 分离路线图、sm_120 上 EP2 非法内存访问、agentic 缓存调度；llama.cpp router `--models-memory-max` 与 RPC 加固；LiteLLM 路由/slot 泄漏。 |
| **低延迟 API 与流式传输** | Guardrail 流式传输、embedding 超时、计时头、reasoning-effort 发现 | LiteLLM 为仅请求型 guardrail 恢复流式快速路径，并修复 embedding 超时传播；vLLM 提议可选启用的 `x-vllm-*` 计时头；llama.cpp 暴露 reasoning-effort 级别；SGLang 收紧 OpenAI 兼容校验。 |
| **本地/边缘与微调内存** | MLX 并发、CUDA 启动、Windows 桌面磁盘占用、显式量化选择 | Ollama 通过 `OLLAMA_NUM_PARALLEL` 实现 MLX 并行、CUDA eager-module 启动重试；Unsloth Block Swap/FP8 n-gram 修复、基准测试页面工作、Windows 回收站 WAL/SHM 泄漏。 |

重心很清楚：**KV-cache/内存行为、FP8/FP4/MoE 内核以及 PD/分布式调度**主导工程投入。原始吞吐量工作越来越与正确性验证耦合，尤其是在投机解码和量化目标周围。

## 5. 层级定位

| Layer | Projects | Role | Today’s signal |
|---|---|---|---|
| **高吞吐服务引擎** | vLLM、SGLang | 数据中心 GPU/加速器推理、OpenAI 兼容 API、分布式服务、PD 分离、高级量化 | 两者都处于硬件/模型覆盖竞赛中；性能收益是真实的，但关键正确性和稳定性问题仍然存在。vLLM 更广；SGLang 在 PD/缓存/内核上更激进。 |
| **本地/边缘运行时与服务器** | llama.cpp、Ollama | 单节点/本地推理、广泛量化、CPU/GPU/后端覆盖、多模态/音频、面向开发者的模型管理 | llama.cpp 交付新架构最快；Ollama 在做打包和加固，尤其是 MLX/CUDA/LFM3。两者都不能替代集群级服务引擎。 |
| **LLM 网关/代理** | LiteLLM | 路由、预算、支出日志、认证、provider 转换、guardrail、OpenAI/Anthropic 兼容代理 | 发布节奏快，但安全补丁回溯和计费正确性问题意味着必须固定版本并核对支出。 |
| **微调/训练加速** | Unsloth | 微调、量化训练/导出、桌面 Studio、基准测试 | 聚焦 Studio 加固和 Qwen3.8-Flash-Next FP8/Block Swap 修复；新模型支持和硬件覆盖落后于服务运行时。 |

## 6. 趋势信号

- **硬件-量化认证现已成为首要生产任务。** DeepSeek-V4-Flash 在 SM8x 上仍然缺失，SM120 CUDA graph 对 DeepSeek-V4.1-Flash 已损坏，sm_120 + EP2 对某些 NVFP4 checkpoint 不可用，Intel XPU / 混合 NVIDIA-AMD / 消费级 Radeon 仍是空白。应将硬件 + 模型 + 量化视为经过测试的元组，而不是笼统的“CUDA”或“ROCm”目标。
- **在风险层面，正确性 bug 正在超过性能收益。** SGLang 的 Kimi-K3 SM90 MXFP4 **静默计算错误的激活**，llama.cpp 投机解码在量化目标上发生偏离，vLLM 贪心输出可能在重启之间变化，SGLang PD prefill 存在陈旧 KV 风险。输出一致性和前缀共享回归测试应作为升级门槛。
- **投机解码和 MoE 仍是高杠杆但高风险。** vLLM、SGLang、llama.cpp、Ollama 和 Unsloth 目前都在涉及 draft 模型、MTP、专家缓存或 spec-decode 路径。模式一致：潜在收益很大，但在量化/MoE/PD 配置周围会出现崩溃、挂起、静默偏离或精度骤降。
- **PD 分离和缓存感知路由正成为 agent 的默认架构方向。** SGLang 的 PD 和 agentic-cache 路线图、vLLM 的 KV offload RFC，以及 llama.cpp 的前缀共享，都指向同一个问题：长前缀、多轮 agent 循环成本高昂，因此 KV 局部性和缓存路由成为控制面关注点。
- **Agent/工具兼容性正在碎片化。** vLLM 的 Rust 前端在 `auto` 工具解析器上可能返回 500；Ollama 存在工具循环 500 和静默丢弃工具调用；LiteLLM 的 `/v1/messages` 路径是变动最频繁的表面；llama.cpp 正在添加 `tool_reference` 和实时工具调用。应用开发者应针对固定版本验证工具调用顺序、空内容处理、未知工具行为和参数边界。
- **供应链和 DoS 加固重新提上日程。** LiteLLM 1.106.x 镜像仍信任一个已泄露的根 CA，llama.cpp 有一个待处理的 RPC `ggml_nbytes()` 溢出修复，未设边界的 sampler 参数可触发数 GB 分配。网关和服务器部署需要 cosign 验证、输入上限和不可信对端假设。
- **本地多模态和语音 agent 正变得实用。** llama.cpp 中的 LFM3 文本/视觉/音频/omni 支持、Ollama 的 MLX 工作，以及实时转写/工具调用端点，都指向跨越文本、音频和视频的本地 agent。在扩展多会话本地部署之前，关注 co-batching 和混合状态正确性问题。

---

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-10-28

## 1. 今日亮点
过去 24 小时内没有版本发布。活动主要集中在硬件支持覆盖和性能优化工作：一项高关注度请求，希望让 DeepSeek-V4-Flash 支持 Ampere SM8x（[#50576](https://github.com/vllm-project/vllm/issues/50576)）；DeepSeek-V4.1-Flash 在 SM120 上存在严重的 CUDA graph/解码吞吐问题（[#56892](https://github.com/vllm-project/vllm/issues/56892)）；以及多个针对 Qwen3.8 模型的 ROCm MI355X 优化跟踪 issue（[#57149](https://github.com/vllm-project/vllm/issues/57149)、[#59575](https://github.com/vllm-project/vllm/issues/59575)）。2026 年第四季度路线图（[#61024](https://github.com/vllm-project/vllm/issues/61024)）仍在推进。

## 2. 版本发布与破坏性变更
无。过去 24 小时内没有发布新版本、API 变更、配置变更或迁移说明。

## 3. 新模型与硬件支持
- **SM8x 上的 DeepSeek-V4-Flash / V4-Flash-0731（Ampere A100/A800、RTX 30xx）**——开放功能请求，已有 121 条评论和 16 个 👍；两个 checkpoint 目前均无法在 SM8x 上运行。[#50576](https://github.com/vllm-project/vllm/issues/50576)
- **Kimi NVFP4 专用模型**——开放 PR，描述待补充。[#42458](https://github.com/vllm-project/vllm/pull/42458)
- **NVIDIA Nemotron-Nano-9B-v2 工具解析器**——PR 已关闭。[#42255](https://github.com/vllm-project/vllm/pull/42255)
- **AMD CDNA W4A16 线性 kernel**——为 MI300 gfx942/gfx950 增加 `CDNAHybridW4A16LinearKernel`，覆盖 GPTQ/AWQ W4A16。[#42640](https://github.com/vllm-project/vllm/pull/42640)
- **ROCm Inv-RoPE FP8 量化**——inverse-RoPE FP8 量化的 Triton 实现。[#42033](https://github.com/vllm-project/vllm/pull/42033)
- **Qwen2.5-Omni 多模态 CPU offload**——将处理后的多模态特征移出 GPU，以降低内存压力。[#41891](https://github.com/vllm-project/vllm/pull/41891)

## 4. 性能与优化
- **GDN chunk-prefill kernel 的原地 SSM 状态**（Qwen3.5、Qwen3-Next、OLMo-Hybrid）：每步内存流量最多减少 **36 GiB**，大型 Qwen 模型吞吐量提升 **10–13%**，0.8B 上提升 **41–56%**。[#41824](https://github.com/vllm-project/vllm/pull/41824)
- **gfx950/MI355X 上的 Qwen3.8-2.4T-A95B**——ROCm 性能优化跟踪 issue；相关工作以堆叠 PR 形式合入。[#57149](https://github.com/vllm-project/vllm/issues/57149)
- **gfx950/MI355X 上的 Qwen3.8-Flash-Next**——跟踪 issue，涵盖 GatedDeltaNet、QuerySparse Attention、HyperConnection 残差和 MXFP4 MoE。[#59575](https://github.com/vllm-project/vllm/issues/59575)
- **GLM 5.3 性能优化**——GLM-5.2 工作的后续；正在讨论 CUDA 及其他优化。[#57406](https://github.com/vllm-project/vllm/issues/57406)
- **动态启用 PDL**——提议暴露可提升 Hopper/Blackwell 低延迟性能的 PDL 标志（`TRTLLM_ENABLE_PDL`、`TORCHINDUCTOR_ENABLE_PDL`）。[#40543](https://github.com/vllm-project/vllm/issues/40543)
- **移除弹性 EP 中重复的 CUDA graph 捕获**——清理 #41792 引入的双重捕获。[#42107](https://github.com/vllm-project/vllm/issues/42107)
- **针对 logprobs 的 tokenizer decode 去重**——缓存重复的 `tokenizer.decode([token_id])` 调用。[#42211](https://github.com/vllm-project/vllm/pull/42211)
- **ROCm AITER 高并发精度崩溃**——MI355X 上的 Qwen3.8-27B-FP8；该 issue 现已关闭，但用户应在高并发下验证 `VLLM_ROCM_USE_AITER=1`。[#60160](https://github.com/vllm-project/vllm/issues/60160)

## 5. 稳定性与回归
按严重程度排序：

**严重**
- **SM120 CUDA graph 不可用 + 解码吞吐极低**：DeepSeek-V4.1-Flash 在 8× RTX PRO 6000 Blackwell 上使用 `--enforce-eager` 时出现。该 issue 中尚未确定修复方案。[#56892](https://github.com/vllm-project/vllm/issues/56892)
- **NCCL `reduce_scatter` 死锁（约 T+1 小时）**：在 ray/mp 后端上使用 MoE + DP 时，零 token 调度的步骤会跳过 `coordinate_batch_across_dp`。[#43547](https://github.com/vllm-project/vllm/issues/43547)
- **即使设置 `VLLM_BATCH_INVARIANT=1`，贪心输出在重启后仍会变化**；q/k-norm + RoPE 组合 kernel 会根据计时选择归约配置。存在可复现性风险。[#58899](https://github.com/vllm-project/vllm/issues/58899)

**高**
- **Kimi-K2.6 出现 `EngineDeadError`**：vLLM 0.20.2（aarch64）。[#42363](https://github.com/vllm-project/vllm/issues/42363)
- **P2P KV offload 层在对端故障时不会快速失败**——故障处理可能使该层处于异常状态。[#63602](https://github.com/vllm-project/vllm/issues/63602)
- **使用 `gemma4-unified-cu129` 镜像时 OpenShift 崩溃**——容器启动即出现 `KeyError`。[#44548](https://github.com/vllm-project/vllm/issues/44548)
- **当 `auto` 工具解析器无法为模型确定解析器时，Rust 前端会对每个工具请求返回 500**。[#63584](https://github.com/vllm-project/vllm/issues/63584)

**中**
- **CPU offloading 在不同 PP rank 之间计算出的 `num_cpu_blocks` 不一致**（4× B300，PP=4）。[#50821](https://github.com/vllm-project/vllm/issues/50821)
- **投机解码时 KV cache 池崩溃**：当 draft 模型引入新的 cache spec 类型（hybrid GDN + DFlash）时发生；已有专用 draft 池的 POC。[#59117](https://github.com/vllm-project/vllm/issues/59117)
- **Rust 前端无法使用官方 Qwen3-ASR checkpoint 启动**（缺少 `tokenizer.json`，`vocab_size` 嵌套）。[#61057](https://github.com/vllm-project/vllm/issues/61057)
- **Qwen3.6-27B 出现 “System message must be at the beginning” 错误**。[#41114](https://github.com/vllm-project/vllm/issues/41114)

**已解决 / 已关闭**
- Qwen3.8-27B-FP8 上的 ROCm AITER 精度崩溃。[#60160](https://github.com/vllm-project/vllm/issues/60160)
- chunked prefill 中使用量化模型时 MLA 出现 `kv_b_proj.weight.dtype` AttributeError。[#43888](https://github.com/vllm-project/vllm/issues/43888)
- V1 调度器在 `_update_after_schedule` 中遇到过期 `req_id` 时硬失败。[#42157](https://github.com/vllm-project/vllm/issues/42157)
- ROCm 在 `paged_attention_ll4mi_QKV_mfma4_kernel` 中发生 Q-load 越界。[#42421](https://github.com/vllm-project/vllm/pull/42421)
- XPU 上 Mamba copy metadata 高位指针处理。[#41995](https://github.com/vllm-project/vllm/pull/41995)

**进行中的修复 PR**
- 非 eager KV 共享快速 prefill 的 LoRA 元数据绑定（Gemma3n/Gemma4）。[#61162](https://github.com/vllm-project/vllm/pull/61162)
- 通过推迟非 FP8 模块的对齐检查，修复 DeepGEMM warmup `RuntimeError`。[#42142](https://github.com/vllm-project/vllm/pull/42142)
- 工具解析期间的流式停止处理。[#42213](https://github.com/vllm-project/vllm/pull/42213)
- 前缀缓存下 `prompt_logprobs` 的不确定性。[#42245](https://github.com/vllm-project/vllm/pull/42245)
- 在 `generate()` 和 `encode()` 中遇到 `ValueError`/`VLLMValidationError` 时中止请求。[#42442](https://github.com/vllm-project/vllm/pull/42442)

## 6. 这对应用开发者意味着什么
- **A100/A800 用户目前仍无法运行 DeepSeek-V4-Flash**——请规划 Hopper/Blackwell 容量，或等待 [#50576](https://github.com/vllm-project/vllm/issues/50576) 中跟踪的 SM8x 支持。
- **在 SM120 Blackwell RTX PRO 6000 上，DeepSeek-V4.1-Flash 应避免使用 `--enforce-eager`**，直到 CUDA graph 捕获和解码吞吐问题修复（[#56892](https://github.com/vllm-project/vllm/issues/56892)）。
- **不要假定重启后贪心解码仍具有确定性**，即使设置了 `VLLM_BATCH_INVARIANT=1`；当可复现性很重要时，请固定 vLLM 版本并验证输出（[#58899](https://github.com/vllm-project/vllm/issues/58899)）。
- **在 Rust 前端上，应配置显式工具解析器，而不是使用 `auto`**，直到解析器解析导致的 500 错误得到解决（[#63584](https://github.com/vllm-project/vllm/issues/63584)）。
- **多级/GDS KV offloading 仍处于 RFC 阶段**（[#38260](https://github.com/vllm-project/vllm/issues/38260)、[#48504](https://github.com/vllm-project/vllm/issues/48504)、[#59538](https://github.com/vllm-project/vllm/issues/59538)）；尚应避免在生产环境中依赖它。
- **ROCm MI355X 用户应参照活跃的优化跟踪 issue 对 Qwen3.8 工作负载进行基准测试**，并在高并发下验证 AITER 精度。
- **按请求的计时头（`x-vllm-*`）已通过 [#42198](https://github.com/vllm-project/vllm/pull/42198) 提议作为可选功能**——落地后有助于客户端侧延迟归因。

---

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-10-28

**范围：** github.com/sgl-project/sglang · 过去 24 小时有 63 个 issue 和 500 个 PR 更新 · 无新版本发布。

---

## 1. 今日亮点

今日活动主要集中在路线图和跟踪项的整合，而非已交付代码：Prefill-Decode 分离（PD 分离）Q4 路线图（[#43699](https://github.com/sgl-project/sglang/issues/43699)）、Diffusion Q4 路线图（[#43313](https://github.com/sgl-project/sglang/issues/43313)）和 Agentic Cache Scheduling 路线图（[#43098](https://github.com/sgl-project/sglang/issues/43098)）均有更新，同时还有一轮大规模自动化的 `[inactive]` 陈旧 issue 清理。影响最大的未修复缺陷是 pip 安装包中**在首个非贪心请求上导致的投机解码崩溃**（[#43412](https://github.com/sgl-project/sglang/issues/43412)），紧随其后的是 Kimi-K3 在 SM90 上的**静默数值正确性 bug**（[#45755](https://github.com/sgl-project/sglang/issues/45755)）。积极的一面是，面向 GLM-5.2 block-FP8 解码的新 W8A16 small-M GEMM 在 GB300 上实现了最高 6.8% 的单 token 延迟降低，且没有先前方案 +8.2 GiB 权重拷贝开销（[#45862](https://github.com/sgl-project/sglang/pull/45862)）。

---

## 2. 版本发布与破坏性变更

**过去 24 小时无新版本发布。**

进行中的行为/API 变更：

- **[#44047](https://github.com/sgl-project/sglang/pull/44047) — OpenAI 兼容 API 校验收紧（OPEN）。** 当前能通过请求解析、但稍后在调度器中失败的越界采样参数，现在会在 API 边界就被拒绝。同时修复 `max_completion_tokens or max_tokens`，使显式传入的 `0` 不再静默回退到旧版限制。发送不规范参数的客户端应预期收到 400。
- **[#36666](https://github.com/sgl-project/sglang/pull/36666) — 逗号分隔的 `--api-key`（CLOSED）。** 通过现有 flag 支持每个服务器多个凭据，未新增 flag。向后兼容。
- **[#45859](https://github.com/sgl-project/sglang/pull/45859) — rust/grpc 中新增 `api.v1` 发现字段（OPEN）。** `GetServerInfoResponse` 新增 `allow_auto_truncate`、`skip_tokenizer_init`、`num_re...`。属于增量变更，但固定响应 schema 的网关客户端应进行验证。
- **[#46904](https://github.com/sgl-project/sglang/pull/46904) — 四个未文档化的 PD-disaggregation `ServerArgs` flag 文档（OPEN）。** `server_arguments_undocumented.txt` 是一个只允许缩短的棘轮清单；这些是面向用户的 PD 部署开关。

> ⚠️ **关于 `[inactive]` 关闭的说明：** 2026-10-28，一大批 issue（#35148、#34969、#36820、#35564、#36511、#36858、#35811、#36598、#36174、#36822、#35702、#35324、#36841）被以 `[inactive]` 关闭。这些是陈旧 issue 清理式关闭，**并非**修复——其中若干描述的是真实缺陷。不要将其视为已解决。

---

## 3. 新模型与硬件支持

- **[#45885](https://github.com/sgl-project/sglang/pull/45885) — Nemotron-3-Embed 原生支持（OPEN）。** 为检索工作负载新增 `nvidia/Nemotron-3-Embed-1B-BF16`；`-NVFP4` 变体共享同一架构，无需模型特定改动。
- **[#30599](https://github.com/sgl-project/sglang/issues/30599) — AMD 消费级 RDNA3/RDNA4 跟踪（OPEN，4 👍）。** 提议将 ROCm 路径从数据中心 Instinct（`gfx942`/`gfx950`）扩展到 `gfx1100`/`gfx1101`/`gfx1200`/`gfx1201`。目前消费级 Radeon 实际上不受支持。
- **[#32321](https://github.com/sgl-project/sglang/issues/32321) — Apple Silicon 服务重构 RFC（OPEN）。** 由 Torch 接管的 SRT 路径，并导出整模型 MLX 区域；实现跟踪见 #36164。
- **[#40173](https://github.com/sgl-project/sglang/pull/40173) — 通过 CUTLASS MoE 在 SM90/SM120 上支持混合 NVFP4/MXFP4 draft experts（OPEN）。** 将 `nvidia/DeepSeek-V4*-NVFP4` checkpoint 中的 MXFP4 DSpark/MTP draft experts 从 FlashInfer-TRTLLM 路径分流出来，该路径目前在 `HybridFp8NvFp4Config` 下处理它们有误。
- **[#38440](https://github.com/sgl-project/sglang/pull/38440) — 面向 DeepSeek-V4 的 NPU DSpark 紧凑模式（OPEN）。** 移除 Ascend 上投机解码中的统一 verify-window 和 eager 后处理开销。
- **[#46902](https://github.com/sgl-project/sglang/pull/46902) — AMD mono-kernel 版本升级（CLOSED）。**

---

## 4. 性能与优化

**已合入 / 提议的内核**

- **[#45862](https://github.com/sgl-project/sglang/pull/45862) — 面向 GLM-5.2 block-FP8 解码投影的 W8A16 small-M GEMM。** 在 GB300（TP4、GLM-5.2-FP8、MTP）上：相比默认 W8A8，**每 token 端到端延迟最高降低 6.8%**，相比 #41975 **降低 1.9%**——同时避免了 #41975 每 GPU **+8.2 GiB 的 BF16 权重拷贝**。KV pool 保持为 **1,196,928 tokens，而非 1,014,848**。
- **[#46903](https://github.com/sgl-project/sglang/pull/46903) — HiCache ack 队列排空。** 在 `UnifiedRadixCache.writing_check` / `loading_check` 和 `DecodeKVCacheOffloadManager._check_offload_progress` 中，用切片删除替代 `O(k·N)` 的 `list.pop(0)` 循环。直接针对高并发下的缓存卸载可扩展性。
- **[#44350](https://github.com/sgl-project/sglang/pull/44350) — DSA/DSV4 索引器迁移到 DeepGEMM 的 `fp8_fp4` MQA-logits API。** 上游已移除 `fp8_mqa_logits` / `fp8_paged_mqa_logits`；本次重构还将 FP4 路径贯穿到稀疏注意力中。
- **[#46839](https://github.com/sgl-project/sglang/pull/46839) — GDN：在 Triton extend 中遵循 fp32 radix track 快照。** 修复深度不在 FLA 64-token chunk 网格上时的 radix-checkpoint 恢复；此时每个 chunk 的 `h` tensor 已被舍入到激活 dtype（bf16）。
- **[#46905](https://github.com/sgl-project/sglang/pull/46905) — Hunyuan3D 在 GPU 上导出形状。** 使用 `torch.meshgrid` 构建 `VanillaVolumeDecoder` 坐标网格，替代 NumPy + 主机到设备拷贝。

**未解决的性能回退 / 跟踪项**

- **[#35291](https://github.com/sgl-project/sglang/issues/35291) — MLA + fp8 KV 在每一层、每次前向时都会将整个逐层 KV pool 物化为 bf16。** 在 DeepSeek-R1 / 8×H200 上，对 `fa3` 和 `flashinfer_mla` 的 absorbed-MLA 路径测得占 **解码步骤的 43.7%**。已标记为 `[inactive]` 但未修复——这是该配置下可获得的单项最大 per-token 收益。
- **[#44304](https://github.com/sgl-project/sglang/issues/44304) — Qwen3.8-Flash-Next gfx950 / MI355X 优化跟踪项。** 36 层中的 GatedDeltaNet、12 层中的 QuerySparse Attention、HyperConnection 门控残差、MXFP4 MoE、原生 NEXTN MTP。
- **[#45373](https://github.com/sgl-project/sglang/issues/45373) — GLM-5.3-Flash Hopper（SM90）优化跟踪项**，属于 Q4 内核路线图 #42473 的一部分。
- **[#46812](https://github.com/sgl-project/sglang/pull/46812) — PD prefill bootstrap 队列前缀现在会在 waiting-prefix LRU 流程中刷新（CLOSED）。** 此前，`disagg_prefill_bootstrap_queue` 中的请求从未被触碰，导致其设备前缀冷却。

---

## 5. 稳定性与回归

按严重程度排序：

1. **[#45755](https://github.com/sgl-project/sglang/issues/45755) — Kimi-K3 在 SM90 上：`flashinfer_mxfp4` 静默计算 GPT-OSS SwiGLU 而非 SiTU。** *静默正确性失败*——不崩溃，但 token 错误。在 v0.5.22 和 `main` @ `ce66b3ab` 上可复现。依赖 flashinfer-ai/flashinfer#4209（仍为 open）；此前尝试 #32650 已关闭且未合并。**将所有 Kimi-K3 SM90 MXFP4 输出视为可疑。** 暂无修复 PR。
2. **[#43412](https://github.com/sgl-project/sglang/issues/43412) — 投机解码在首个被采样请求上杀死服务器。** 在无系统 CUDA toolkit 的 pip 安装包中，flashinfer verify-sampling op 的 JIT 失败（捆绑 cccl 与 pip cu13 toolkit 不匹配），并对进程树执行 SIGKILL。贪心（temp 0）流量不受影响。任何 temp>0 请求都会轻易触发。未引用修复 PR。
3. **[#44291](https://github.com/sgl-project/sglang/issues/44291) — EP2（`tp=2 + ep=2`）在 Qwen3.8-Flash-Next-NVFP4、RTX PRO 6000 / sm_120 上出现非法内存访问。** 内核级 IMA 在两个构建中均可复现。对于该 checkpoint，sm_120 + Expert Parallel 不可用。
4. **[#39300](https://github.com/sgl-project/sglang/issues/39300) — `TorchNativeAttnBackend` 中忽略非因果滑动窗口注意力。** 回退后端静默应用因果掩码，导致非因果/SWA 配置产生错误注意力。
5. **[#42752](https://github.com/sgl-project/sglang/issues/42752) — 测试不稳定与 CI 基础设施故障（134 条评论，最活跃 issue）。** 通过 `/sglang-pr-babysit` 工作流跟踪 NVIDIA `PR Test Base` / `PR Test Extra` 的反复不稳定；补充 #17050。高评论量表明 CI 持续不稳定，正在影响合并速度。
6. **[#44070](https://github.com/sgl-project/sglang/issues/44070) — PD prefill 提前发送缓存前缀可能在 HiCache 回载完成前传输 KV（CLOSED）。** 当两个请求共享主机驻留前缀且 `SGLANG_DISAGG_PREFILL_EARLY_SEND_CACHED_PREFIX=1`（默认）时，Decode 会静默收到陈旧 KV。值得确认修复顺序已落地，因为该 flag 仍默认开启。
7. **[#27787](https://github.com/sgl-project/sglang/issues/27787) — 使用 `moe_runner_backend=flashinfer_trtllm` 时 BF16 RL 权重更新崩溃（CLOSED）。** `_load_w13` 出现 64 与 2048 的形状不匹配；`update_weights_from_tensor` 路径未恢复填充后的 `w13` 布局。与 RL 微调循环相关。
8. **[#44794](https://github.com/sgl-project/sglang/issues/44794) — AMD `select_experts` 将 fp32 MoE 校正 bias 转换为 bf16（CLOSED）。** 尽管 #37133 提供了 fp32 aiter 路径，ROCm 上的 GLM-5.x 仍使用 bf16 bias 进行路由。

**同样已关闭、活动较少：** #[35148](https://github.com/sgl-project/sglang/issues/35148)（rust sgl-model-gateway 中的 Qwen3.8-27B-FP8 推理解析）、#[34969](https://github.com/sgl-project/sglang/issues/34969)（HF3FS HiCache `ZeroDivisionError`，DeepSeek-V4）、#[35811](https://github.com/sgl-project/sglang/issues/35811)（HiCache IMA，deepseekv4-flash）、#[36598](https://github.com/sgl-project/sglang/issues/36598)（DSA `index_topk` override 断言）、#[35324](https://github.com/sgl-project/sglang/issues/35324)（DeepSeek-V4-Flash-0731 上 DSPARK 的间歇性 CUDA 崩溃）、#[36822](https://github.com/sgl-project/sglang/issues/36822)（BF16 + TP 下 GPTQ Marlin MoE 出现 NaN）、#[35702](https://github.com/sgl-project/sglang/issues/35702)（`sharded_state` MLA 保存/加载）。

---

## 6. 对应用开发者的意义

- **不要在仅有 pip 的环境中为非贪心流量启用投机解码。** [#43412](https://github.com/sgl-project/sglang/issues/43412) 是硬性杀死进程，而非优雅错误，并且会在*首个*被采样请求上触发。在问题解决前，要么安装匹配的系统 CUDA toolkit，要么固定使用贪心采样，要么在生产中不启用 spec decode。
- **在信任 Kimi-K3 SM90 输出前先进行验证。** [#45755](https://github.com/sgl-project/sglang/issues/45755) 是静默的激活函数替换——基准分数和 eval harness 不会将其标记出来。对任何 Hopper 上的 MXFP4 Kimi-K3 部署运行一组已知良好的 prompt 套件。
- **PD 分离在大规模场景下已是默认，而其边缘情况是 KV 正确性 bug，而非性能 bug。** [#44070](https://github.com/sgl-project/sglang/issues/44070) 是一个默认开启的陈旧 KV 风险；如果你运行带 HiCache 的 PD prefill，请确认所用构建包含该修复，并重新运行前缀共享负载。
- **预期 OpenAI 兼容请求校验会更严格。** [#44047](https://github.com/sgl-project/sglang/pull/44047) 将无效采样边界从“在调度器深处失败”改为“在 API 处拒绝”。审计客户端参数范围，以及任何将 `max_completion_tokens=0` 作为“未设置”哨兵传递的代码。
- **按使用方划分的 API key 即将到来**（[#36666](https://github.com/sgl-project/sglang/pull/36666)），消除多租户网关共享凭据的变通方案——有助于在不重启的情况下按用户或按服务撤销访问。
- **Agent 工作负载将获得缓存感知路由。** Q4 Agentic Cache Scheduling 路线图（[#43098](https://github.com/sgl-project/sglang/issues/43098)）目标是跨引擎的超级节点级 L1（GPU）+ L2（host）KV 共享，以及感知缓存位置的路由器放置——这一机制可降低长前缀 agent 循环的成本。如果你在共享集群上运行多轮 agent，应为此做规划。
- **RAG/embedding 现已纳入服务层范围**，`nvidia/Nemotron-3-Embed-1B`（[#45885](https://github.com/sgl-project/sglang/pull/45885)）原生落地，而不再需要 sidecar。
- **硬件注意事项：** sm_120（RTX PRO 6000）+ EP2 目前对 NVFP4 checkpoint 不可用（[#44291](https://github.com/sgl-project/sglang/issues/44291)）；消费级 Radeon 仍不受支持（[#30599](https://github.com/sgl-project/sglang/issues/30599)）；Apple Silicon 服务仍处于 RFC 阶段（[#32321](https://github.com/sgl-project/sglang/issues/32321)）。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-10-28

## 1. 今日亮点
LiquidAI 的 LFM3 系列强势落地：在 b11938/b11941 中新增四个模型（1.7B、VL-2B、Audio-2B、Omni-2B），此外还有视频输入支持以及 `/v1/realtime` 上的工具调用（正在评审中）。服务器层还获得了两项值得关注的能力：聊天模板的推理强度等级现在会被探测并暴露（b11944），路由器可通过新的设备内存上限（`--models-memory-max`，b11930）动态卸载模型。风险方面，量化目标上的投机解码正确性（#25618）仍是讨论最多的未决缺陷，紧随其后的是 sm_120 上的 CUDA graph 挂起。

## 2. 发布与破坏性变更
- **b11944 — 服务器暴露推理强度等级**（[#31271](https://github.com/ggml-org/llama.cpp/pull/31271)）：能力探测现在会为每个候选等级（`minimal`、`low`、`medium`、`high`、`xhigh`、`max`）渲染一个用户轮次，并额外提供一个未知等级对照项，使客户端可以枚举有效的推理强度值。直接解决了 #27455 中由来已久、获得 12 个 👍 的请求（[#27455](https://github.com/ggml-org/llama.cpp/issues/27455)）。
- **b11930 — 新的路由器内存余量：`--models-memory-max`**（[#21231](https://github.com/ggml-org/llama.cpp/pull/21231)）：当模型将超过配置的内存阈值时会动态卸载模型，估算时会纳入待加载模型的大小，并使用 `no_alloc` 测量需求。**这对多模型路由器部署是行为变更**——如果你依赖当前常驻集行为，请显式设置此项。
- **b11940 — `common` 中的延迟工具与 `tool_reference` 内容部分**（[#31256](https://github.com/ggml-org/llama.cpp/pull/31256)）：扩展了工具调用解析面；下游聊天处理程序可能需要处理新的内容部分类型。
- **b11936 — k-pool 稀疏选择与 `kq_mask` 形状解耦**（[#31243](https://github.com/ggml-org/llama.cpp/pull/31243)）：qwen4exp（QSA）和 glm5-next 现在直接索引缓存单元，而不是读取 `kq_mask` 值。这属于内部改动，但如果你为这些架构维护自定义 kernel，则值得关注。
- **b11935 — RPC 加固**（[#31204](https://github.com/ggml-org/llama.cpp/pull/31204)）：在 `recv_msg` 中捕获 `std::length_error`，而不是直接终止。
- 注意：[据报告，预构建的 Windows CUDA 二进制文件停留在 b11828](https://github.com/ggml-org/llama.cpp/issues/31085)，而标签仍在推进——请校验你下载的资产，而不要轻信发布名称。

## 3. 新模型与硬件支持
- **LFM3-1.7B 与 LFM3-VL-2B**（[#31252](https://github.com/ggml-org/llama.cpp/pull/31252)，b11938）。
- **LFM3-Audio-2B 与 LFM3-Omni-2B**（[#31257](https://github.com/ggml-org/llama.cpp/pull/31257)，b11941）——并按发布版本解析 `config.json`。
- **LiquidAI d1-2B / d1-omni-2B** 决策模型（[#31275](https://github.com/ggml-org/llama.cpp/pull/31275)，已合并）。
- **mtmd 中为 LFM3 系列新增视频输入**（[#31283](https://github.com/ggml-org/llama.cpp/pull/31283)，待合并）——包含视频+音轨能力矩阵。
- **`/v1/realtime` 扩展**：工具调用（[#31287](https://github.com/ggml-org/llama.cpp/pull/31287)）与语音聊天/交错转写（[#31269](https://github.com/ggml-org/llama.cpp/pull/31269)），二者均面向 LFM3-Audio/Omni GGUFs。
- **GigaChat 3.5 432B-A28B**（DeepSeek-V3 风格 MLA + MoE，混合注意力）已在 [#25342](https://github.com/ggml-org/llama.cpp/pull/25342) 中提出。
- **Hexagon 后端**：新增 `DSV4_HC_PRE`/`DSV4_HC_POST` 算子（[#30953](https://github.com/ggml-org/llama.cpp/pull/30953)）；所有 `test-backend-ops` 均通过。
- **后端构建修复**：修复 clang-cl + `-mavx512bf16` 构建（[#31276](https://github.com/ggml-org/llama.cpp/pull/31276)）。
- 缺口：**FP4 量化缺少 round-to-even**（[#31012](https://github.com/ggml-org/llama.cpp/issues/31012)）——量化输出与参考舍入结果不同。

## 4. 性能与优化
- **MoE 专家缓存重构**（[#31233](https://github.com/ggml-org/llama.cpp/pull/31233)）：采用受 LRFU/MQ 启发的按类别排序缓存，以衰减的使用历史取代 SLRU。在缓存预热时测得 **QFN 上解码 +6%，GLM 上 +7%，专家上传减少 8%**。后续改动新增 `--moe-cache-cpu-share auto`，并为 CPU 未命中引入 CRF 准入（[#31286](https://github.com/ggml-org/llama.cpp/pull/31286)）。
- **Hexagon TOP_K kernel**（[#31279](https://github.com/ggml-org/llama.cpp/pull/31279)）：后端采样器曾是 X2 Elite 上最大的草稿阶段开销——**占 DFlash 草稿批次的 42%，占 DSpark 的 25%**。现在避免了对每个 tile 进行完整排序。
- **cluster 维度变化时重新实例化 CUDA graph**（[#31284](https://github.com/ggml-org/llama.cpp/pull/31284)）：Hopper cuBLAS F16 GEMM 会针对不同形状选择不同的线程块 cluster（例如 m=1200 → cluster 5,1,1；m=1040 → 2,2,1）；此前陈旧的 graph 会复用错误的启动配置。
- **SYCL VRAM 超额分配防护**（[#31285](https://github.com/ggml-org/llama.cpp/pull/31285)）：拒绝会超出 VRAM 的设备缓冲区，从而避免在 `-ngl 999` 加载超大模型时发生内核崩溃。
- **槽位间共享 KV/前缀**（[#30970](https://github.com/ggml-org/llama.cpp/pull/30970)）：`--slot-attach` 允许会话共享由另一个槽位持有的前缀，而不是在缓存中复制一份——对多会话 agent 可显著节省内存。
- **Vulkan MUL_MAT 第 3 维广播**（[#31239](https://github.com/ggml-org/llama.cpp/pull/31239)，b11933）：`supports_op` 不再拒绝所有 `ne3` 不同的 `MUL_MAT`；这也解除了 llvmpipe CI 的阻塞。
- **测试框架提速**（[#31145](https://github.com/ggml-org/llama.cpp/pull/31145)，b11934）：`save_models` 不再构造不需要的 `llama_context`，缓慢的 `std::normal_distribution` 抽取已替换为更快的 splitmix 路径。
- **投机解码 / MTP**：通过 `d2t` 映射为 qwen35/qwen35moe 裁剪草稿词表（[#29143](https://github.com/ggml-org/llama.cpp/pull/29143)）；已卸载的 MTP 层不再计入 GPU 放置数量（[#31278](https://github.com/ggml-org/llama.cpp/pull/31278)）。

## 5. 稳定性与回归
按严重程度排序：

1. **[#25618](https://github.com/ggml-org/llama.cpp/issues/25618) — 在量化目标上，投机解码在贪婪采样下与常规解码结果不一致**（32 条评论）。在 Q4_K_M 等目标上，使用草稿模型投机并设置 `temperature=0, top_k=1` 时，生成的文本与非投机解码不同，而 bf16 目标则一致。这是影响最大的未决正确性缺陷；尚无修复 PR。
2. **[#27330](https://github.com/ggml-org/llama.cpp/issues/27330) — CUDA graph 在 RTX 5090 Laptop / sm_120 上挂起 GPU 通道**（RC 看门狗 + Xid 8）。已有完整规避方案：`GGML_CUDA_DISABLE_GRAPHS=1`。尚无修复 PR。
3. **[#29054](https://github.com/ggml-org/llama.cpp/issues/29054) — 在 Intel Gen9.5（UHD 630）上使用 q8_0 KV 缓存时，Vulkan GPU 挂起并触发 SIGABRT**；`vk::Error` 逃逸为 `std::terminate`。可稳定复现。
4. **[#27560](https://github.com/ggml-org/llama.cpp/issues/27560) — 在 Windows/Vulkan 上使用默认 ctx-checkpoints 时，llama-server 发生访问冲突（0xC0000005）**（Qwen3.8-27B）。
5. **[#29494](https://github.com/ggml-org/llama.cpp/issues/29494) — `repeat_last_n` / `dry_penalty_last_n` 无界**：会分配数 GB 的零填充采样器内存，导致服务器 OOM。如果将采样器参数暴露给不受信任的客户端，应将其视为 DoS 向量。
6. **[#29002](https://github.com/ggml-org/llama.cpp/issues/29002) — 混合 LFM2 合批会破坏循环/短卷积状态**：在同一个 ubatch 中解码超过 1 个序列会改变输出；贪婪输出可能在一句话中间停止。这与今天落地的 LFM3 混合模型工作直接相关。
7. **[#28581](https://github.com/ggml-org/llama.cpp/issues/28581) — IQ3_S 在 RTX 5060 Ti（Blackwell）上产生垃圾输出**。
8. **[#31217](https://github.com/ggml-org/llama.cpp/pull/31217) — ggml-rpc 中的 `ggml_nbytes()` 整数溢出 → 越界读写**，可由恶意构造形状的张量触发。**修复 PR 已开放**；对不受信任的 RPC 对端很重要。
9. **[#31220](https://github.com/ggml-org/llama.cpp/issues/31220) — aarch64 服务器构建上的投机解码性能下降**（回归，尚未确认）。
10. **[#31029](https://github.com/ggml-org/llama.cpp/issues/31029) — Arrow Lake iGPU 不支持 SYCL bf 转换指令**。
11. **[#31163](https://github.com/ggml-org/llama.cpp/issues/31163) — Vulkan `count_experts.comp` 的 `zero_skipped_rows()` 中，每个线程都会遍历每一个 expert id**（源自 #26631 的性能缺陷）。
12. 今日修复：**mtmd-cli 在 `/clear` 后批次陈旧**（[#31155](https://github.com/ggml-org/llama.cpp/issues/31155)）→ 在 b11932 中缩小了批次指针作用域（[#31165](https://github.com/ggml-org/llama.cpp/pull/31165)）；**DSV4.1 GSQ-RCO 加载失败**（[#31263](https://github.com/ggml-org/llama.cpp/issues/31263)）已关闭；**Vulkan Navi 10 GPU ring 挂起**（[#28807](https://github.com/ggml-org/llama.cpp/issues/28807)、[#28808](https://github.com/ggml-org/llama.cpp/issues/28808)）已作为陈旧问题关闭。另请注意，`update_slots()` 未将 `batch_view` 偏移传播到 `ctx_dft` 的问题已关闭（[#24840](https://github.com/ggml-org/llama.cpp/issues/24840)）。
13. 工具/CI：**MSVC 无法检测 AVX-VNNI**（[#28295](https://github.com/ggml-org/llama.cpp/issues/28295)），并且 `json-schema-to-grammar` 会逐字复制原始控制字节，在 schema 模式中出现 NUL 时出错——修复见 [#31241](https://github.com/ggml-org/llama.cpp/pull/31241)，如果你接受用户提供的 JSON schema，这一点很重要。

## 6. 这对应用开发者意味着什么
- **推理强度现在可被发现，而不再靠猜。** b11944 的探测让客户端可以询问服务器，已加载的聊天模板实际支持哪些等级（`minimal`…`max`），从而将应用层的“思考预算”映射到模板支持的值，而不是发出模型会静默忽略的等级。设计截断 UI 时，可结合已关闭的优雅预算请求（[#20632](https://github.com/ggml-org/llama.cpp/issues/20632)）。
- **Agent 的工具链路更完善。** `tool_reference` 内容部分与延迟工具（b11940），加上 `/v1/realtime` 工具调用（[#31287](https://github.com/ggml-org/llama.cpp/pull/31287)），意味着 OpenAI 形态的工具循环现在可以横跨文本、音频和视频输入，而无需旁路通道。在推进上线前，请验证你的解析器能处理新的内容部分类型。
- **借助 LFM3-Audio/Omni-2B 与实时转写端点，语音/全模态 agent 在 CPU 级硬件上已可行**——但在 [#29002](https://github.com/ggml-org/llama.cpp/issues/29002) 解决前，避免对混合 LFM 模型的多个序列进行合批；请将这些会话串行化，或使用独立上下文。
- **多租户模型路由器应采用 `--models-memory-max`**（b11930）。它把硬 OOM 失败转化为确定性的驱逐，但会改变常驻语义——请审计任何假设热模型会保持加载的延迟 SLO。
- **不要在没有与常规解码进行输出一致性测试的情况下，发布使用量化草稿/目标对的投机解码**（[#25618](https://github.com/ggml-org/llama.cpp/issues/25618)）。在 sm_120（RTX 50 系列）主机上，请设置 `GGML_CUDA_DISABLE_GRAPHS=1`，直到 [#27330](https://github.com/ggml-org/llama.cpp/issues/27330) 落地。
- **在服务端为采样器参数设置上限。** 无界的 `repeat_last_n`/`dry_penalty_last_n`（[#29494](https://github.com/ggml-org/llama.cpp/issues/29494)）会让单个请求分配数 GB 内存——每个托管部署今天都应添加这一行校验。
- **固定并校验二进制文件。** Windows CUDA 预构建版本停留在 b11828（[#31085](https://github.com/ggml-org/llama.cpp/issues/31085)），意味着“最新发布”并不是“最新代码”的可靠代名词——请检查内嵌的构建号；如果你需要今天的 LFM3 或推理强度变更，请自行构建。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-10-28

## 1. 今日亮点
过去 24 小时内没有版本发布。活动集中在模型接入与运行时加固上：LFM3 支持正在 [PR #19344](https://github.com/ollama/ollama/pull/19344) 中修复，此前用户遇到了 [issue #19347](https://github.com/ollama/ollama/issues/19347) 和 [issue #19343](https://github.com/ollama/ollama/issues/19343)；与此同时，MLX 在 [PR #19197](https://github.com/ollama/ollama/pull/19197) 中获得并发生成支持，CUDA 启动崩溃缓解在 [PR #18978](https://github.com/ollama/ollama/pull/18978) 中。高影响未解决问题仍集中在云端推理循环 [#17892](https://github.com/ollama/ollama/issues/17892)、Qwen3.8 工具循环 500 [#17778](https://github.com/ollama/ollama/issues/17778) 以及 macOS Metal/MLX 崩溃 [#19278](https://github.com/ollama/ollama/issues/19278)、[#18885](https://github.com/ollama/ollama/issues/18885)。

## 2. 发布与破坏性变更
过去 24 小时无新版本发布，也没有确认的 API/配置破坏性变更。兼容性工作正在进行但尚未发布：流式错误转发 [PR #18953](https://github.com/ollama/ollama/pull/18953)、Anthropic 思考块流式传输 [PR #19110](https://github.com/ollama/ollama/pull/19110)，以及 OpenAI 开发者指令处理 [PR #19199](https://github.com/ollama/ollama/pull/19199)（已关闭）。

## 3. 新模型与硬件支持
- **LFM3 GGUF 修复** — [PR #19344](https://github.com/ollama/ollama/pull/19344)（开放）修复 LFM3 GGUF 的 BOS 处理，并新增 GGUF 文本转语音支持；解决 [issue #19347](https://github.com/ollama/ollama/issues/19347)（`unknown model architecture: 'lfm3'`，已关闭）和 [issue #19343](https://github.com/ollama/ollama/issues/19343)（MLX 运行时不可用）。
- **Nemotron 3.5 Bolt 74B-A9B** — [PR #19229](https://github.com/ollama/ollama/pull/19229)（开放）添加逐层专家配置、LiLiCorr 推测解码、预量化 affine 3-bit 导入以及混合精度专家堆叠。
- **Kolibri 1** — [PR #18780](https://github.com/ollama/ollama/pull/18780)（开放）添加 MLX 支持。
- **MLX 并发生成** — [PR #19197](https://github.com/ollama/ollama/pull/19197)（开放）通过 `OLLAMA_NUM_PARALLEL` 支持多个同时生成请求。
- **CUDA/Blackwell 启动** — [PR #18978](https://github.com/ollama/ollama/pull/18978)（开放）使用 eager CUDA 模块加载重试 `llama-server` 启动，针对 RTX 5060 Ti/Blackwell 上的共享对象初始化失败；关联到 [issue #19122](https://github.com/ollama/ollama/issues/19122)。
- **GPU 发现修复** — [PR #19338](https://github.com/ollama/ollama/pull/19338)（开放）修正对支持所有 GPU 的最新库版本的选择。
- **仅功能请求** — 桌面 UI 中的 GPU 加速检测 [issue #19301](https://github.com/ollama/ollama/issues/19301)；AMD RX 6750 GRE 10GB 支持 [issue #19329](https://github.com/ollama/ollama/issues/19329)。

## 4. 性能与优化
- **MLX 并行** — [PR #19197](https://github.com/ollama/ollama/pull/19197) 允许 MLX 模型并发处理多个请求，降低排队请求的首 token 时间。默认仍为一个请求；必须为每个活跃请求预留内存。
- **CUDA 启动重试** — [PR #18978](https://github.com/ollama/ollama/pull/18978) 通过 eager 模块加载缓解部分卸载预热崩溃。未提供吞吐量数据。
- **调度器驱逐** — [issue #19326](https://github.com/ollama/ollama/issues/19326) 报告 `findRunnerToUnload` 先按 `sessionDuration`、再按模型路径排序；当 `keep_alive` 一致时，驱逐会变得任意，而不是基于大小/最近使用。未列出修复 PR。
- **`OLLAMA_NUM_PARALLEL=1` 语义** — [issue #19262](https://github.com/ollama/ollama/issues/19262) 报告尽管设置了该值，仍有两个会话并发运行。未列出修复 PR。
- **Vulkan 回归** — [issue #13567](https://github.com/ollama/ollama/issues/13567)（已关闭）报告 gpt-oss 在 Vulkan 上相比 CPU/llama.cpp 较慢；所提供数据中没有修复详情。

## 5. 稳定性与回归

### 高严重性
- **macOS Metal 崩溃 / MLX panic** — [issue #19278](https://github.com/ollama/ollama/issues/19278) 报告在 Ollama 0.40.9 上出现 `mlx runner failed: panic: mlx: [METAL] Command buffer execution failed`；[issue #18885](https://github.com/ollama/ollama/issues/18885) 报告 `gemma4:e2b-mlx` 上出现 MLX runner panic。未列出修复 PR。
- **CUDA 共享对象初始化失败** — [issue #19122](https://github.com/ollama/ollama/issues/19122) 出现在 Windows 上的 RTX 5060 Ti/Blackwell、部分卸载场景。修复 PR [PR #18978](https://github.com/ollama/ollama/pull/18978) 处于开放状态。
- **云端思考循环** — [issue #17892](https://github.com/ollama/ollama/issues/17892)：`deepseek-v4-flash:0731` 在约 1 分 45 秒内重复同一推理块 221 次，且没有任何可用输出。未列出修复 PR。
- **Qwen3.8 工具循环 500** — [issue #17778](https://github.com/ollama/ollama/issues/17778)：工具调用循环期间出现 `ResponseError during chat streaming: no user query found in messages`；56 条评论，未列出修复 PR。
- **Windows 连接重置** — [issue #19139](https://github.com/ollama/ollama/issues/19139)：在 Windows/RTX 3090 上，大提示词会使 `/api/chat` 在约 15 秒后重置；标记为需要更多信息。
- **拉取恢复损坏** — [issue #19337](https://github.com/ollama/ollama/issues/19337)：在拉取开始超过一小时后重启服务器，会因部分文件缺失或摘要不匹配而破坏恢复。修复 PR [PR #19339](https://github.com/ollama/ollama/pull/19339) 处于开放状态。
- **工具调用被静默丢弃** — [issue #19187](https://github.com/ollama/ollama/issues/19187)：对不在请求 `tools` 中的函数的调用会被静默丢弃，产生空响应且 `eval_count > 0`。未列出修复 PR。
- **低比特 Qwen2.5-Coder 产物损坏** — [issue #18252](https://github.com/ollama/ollama/issues/18252)：`qwen2.5-coder:3b-instruct` 的 `q2_K`/`q3_K_S`/`q3_K_M`/`q3_K_L` 在功能冒烟测试套件中得分为 0/15，而同系列量化版本通过率为 87–100%。未列出修复 PR。

### 中等严重性
- **OpenAI 工具调用错误附加** — [issue #19151](https://github.com/ollama/ollama/issues/19151)（已关闭）：当 `content: []` 时，助手的 `tool_calls` 被附加到上一条消息。
- **云端 Responses 413** — [issue #19155](https://github.com/ollama/ollama/issues/19155)（已关闭）：约 10.7 MiB 的编码请求被拒绝，尽管低于宣称的上下文限制。
- **`/v1/systemone` 决策模型** — [issue #19292](https://github.com/ollama/ollama/issues/19292)：自 0.40.4 起，带有 `CAPABILITY decision` 但没有 `decision.type` 的 GGUF 模型会被拒绝。
- **DNS64 拉取失败** — [issue #19345](https://github.com/ollama/ollama/issues/19345)：在 DNS64 环境下，`ollama pull hf.co/...` 失败，因为 NAT64 CDN 地址被视为非公网地址。
- **LFM3 运行时不匹配** — [issue #19343](https://github.com/ollama/ollama/issues/19343)：`lfm3-audio:latest` 需要不可用的 MLX 支持；[issue #19347](https://github.com/ollama/ollama/issues/19347)（已关闭）在修复 PR 之前报告了未知的 `lfm3` 架构。
- **调度器驱逐行为** — [issue #19326](https://github.com/ollama/ollama/issues/19326)：当 `keep_alive` 相同时，模型驱逐变得任意。

### 低严重性
- **Modelfile 往返 bug** — [issue #19342](https://github.com/ollama/ollama/issues/19342)：当值包含后跟空白的 `"""` 时，`ollama show --modelfile` 会输出无法解析的内容。
- **REPL 状态 bug** — [issue #19340](https://github.com/ollama/ollama/issues/19340)：在多行输入期间按 Ctrl+C 会使 REPL 停留在多行模式；[issue #19341](https://github.com/ollama/ollama/issues/19341)：`/load` 不能一致地重置系统提示词。
- **ShieldGemma 模板** — [issue #19313](https://github.com/ollama/ollama/issues/19313)：模板在应用于用户提示词时会分类出“an AI response”。

## 6. 这对应用开发者意味着什么
- **锁定版本并分阶段升级。** 在 [#19278](https://github.com/ollama/ollama/issues/19278)、[#18885](https://github.com/ollama/ollama/issues/18885) 和 [#19122](https://github.com/ollama/ollama/issues/19122) 解决之前，避免统一采用 0.40.x MLX 或 Blackwell CUDA 配置。
- **为云端推理模型添加循环和超时检测。** [#17892](https://github.com/ollama/ollama/issues/17892) 可能消耗数分钟且不产生输出；强制执行最大推理/token 预算，并用其他模型重试。
- **防御性地校验工具调用。** 未知工具名称可能被静默丢弃（[#19187](https://github.com/ollama/ollama/issues/19187)）；检查 `eval_count` 非零的空响应。不要假设 `tool_calls` 在空内容数组后仍然保留（[#19151](https://github.com/ollama/ollama/issues/19151)）。
- **处理带内流式错误。** 在 [PR #18953](https://github.com/ollama/ollama/pull/18953) 发布之前，客户端在首个 token 之后可能收到空白块，而不是传播的错误。
- **为 MLX 并发预留内存。** [PR #19197](https://github.com/ollama/ollama/pull/19197) 可通过 `OLLAMA_NUM_PARALLEL` 改善 TTFT，但每个并行请求都需要额外内存。
- **避免已知有问题的量化。** 不要在代码任务中使用 `qwen2.5-coder:3b-instruct` 的 `q2_K`/`q3_K_*` 变体；使用更高比特的同系列版本（[#18252](https://github.com/ollama/ollama/issues/18252)）。
- **让拉取可恢复且可安全重启。** 添加重试逻辑，并在 [PR #19339](https://github.com/ollama/ollama/pull/19339) 合入之前避免在拉取过程中重启 `ollama serve`（[#19337](https://github.com/ollama/ollama/issues/19337)）。
- **显式控制模型驻留。** 当 `keep_alive` 一致时，调度器驱逐可能按模型路径而不是最近使用情况选择（[#19326](https://github.com/ollama/ollama/issues/19326)）；设置差异化的 `keep_alive` 值或预加载关键模型。
- **私有 OCI 仓库仍是缺口。** 启用认证的拉取/推送仍在 [#9215](https://github.com/ollama/ollama/issues/9215) 和 [#4204](https://github.com/ollama/ollama/issues/4204) 中被请求；在隔离网络/私有部署中需要围绕这一点做规划。

---

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-10-28

## 1. 今日亮点

四个补丁版本已发布到维护中的稳定线（v1.106.5、v1.105.6、v1.104.8、v1.103.10），全部可通过 cosign 验证；同时 `main` 已推进到 1.109.0 线，Admin UI 包已重新构建。最紧急的未决项是一个安全回移植请求：1.106.x Docker 镜像仍信任已失陷的 **emSign ECC Root CA – G3**，修复（[#47670](https://github.com/BerriAI/litellm/pull/47670)）仅在 `main` 上合入。在正确性方面，今天关闭了一组流式/`/v1/messages` 计费与槽位泄漏缺陷，其中数个会静默少计生产流量。

---

## 2. 发布与破坏性变更

| 版本 | 备注 |
|---|---|
| [v1.106.5](https://github.com/BerriAI/litellm/releases/tag/v1.106.5) | 1.106 稳定分支上的补丁版本。使用 `cosign verify --key .../cosign.pub ghcr.io/berriai/litellm:v1.106.5` 验证（`litellm-database`、`litellm-non_root`、`docker.io/litellm/*` 同理）。 |
| [v1.105.6](https://github.com/BerriAI/litellm/releases/tag/v1.105.6) | 1.105 上的补丁版本。 |
| [v1.104.8](https://github.com/BerriAI/litellm/releases/tag/v1.104.8) | 1.104 上的补丁版本。 |
| [v1.103.10](https://github.com/BerriAI/litellm/releases/tag/v1.103.10) | 1.103 上的补丁版本。 |

发布流程说明：
- [#48673](https://github.com/BerriAI/litellm/pull/48673) — 将 `main` 提升到 **1.109.0** 线（`litellm-enterprise` → 0.1.80）。1.108 线在发布节奏停滞后从未获得标签；**1.108.0 将以 RC 形式从 `rc/1.108.0` 发布**，而 `main` 从 1.109 重新开始。请相应规划升级路径——1.108.0 不是正常的稳定版本切出。
- [#48692](https://github.com/BerriAI/litellm/pull/48692) — 为 1.109.0 从当前 `main` 重新构建 Admin UI 包；此前发布的 UI 是用较旧的 dashboard 源构建的，缺少页面（例如 chat model hub）。

⚠️ **安全回移植待完成：** [#47904](https://github.com/BerriAI/litellm/issues/47904) (OPEN) — 所有已发布的 **1.106.x** 镜像仍信任 `emSign ECC Root CA - G3`。截至今日，[#47670](https://github.com/BerriAI/litellm/pull/47670) 中的修复仅存在于 `main`。如果你在 1.106.x 上使用出站 TLS，请密切关注。

---

## 3. 新模型与硬件支持

- **Databricks OpenJev 决策模型定价** — [#48694](https://github.com/BerriAI/litellm/pull/48694)：`databricks/databricks-openjev-qwen35-4b` 此前映射为 0.0 成本，因此所有 `/v1/decisions` 和 `/v1/systemone` 调用都记录为 $0 花费。现在按 Databricks 公布的 **每 100 万输入 token 0.6 DBU**（$0.070/DBU）计价。
- **Cloudflare Workers AI 扩展（仍未合并）** — [#21115](https://github.com/BerriAI/litellm/issues/21115) (OPEN，陈旧)：提供商实现比 Cloudflare 扩展后的 API 落后约 10 个月。图像支持（[#35055](https://github.com/BerriAI/litellm/issues/35055)）和音频/STT/TTS 支持（[#35056](https://github.com/BerriAI/litellm/issues/35056)）的配套功能请求仍处于 OPEN 且陈旧状态。
- **Tavily 搜索透传** — [#48684](https://github.com/BerriAI/litellm/pull/48684) (OPEN)：现在转发 `raw_content`、`score`、`favicon` 以及响应信封。此前调用方为 `include_raw_content` 付费，却只收到 title/url/excerpt。
- **透传模型归因** — [#48275](https://github.com/BerriAI/litellm/pull/48275) (CLOSED)：上游可以通过 `x-litellm-upstream-model` 报告模型，因此没有 `model` 字段的产品端点不再归入 `unknown` 花费桶。
- **SAP 工具调用重放** — [#48696](https://github.com/BerriAI/litellm/pull/48696) (OPEN)：重放的 assistant 工具调用若省略 `arguments`，现在会发送空字符串，而不是在本地失败。

本时间窗口内没有新的 CUDA/ROCm/Metal 后端或量化格式。

---

## 4. 性能与优化

- **仅请求侧护栏的流式快速路径已恢复** — [#47735](https://github.com/BerriAI/litellm/pull/47735) (OPEN)：仅提示词护栏此前会强制对每个流式回复做逐块响应检查，就每一段询问护栏。现在除非确有组件检查流式块，否则跳过该检查。对位于护栏后的流有显著延迟收益。
- **多模态配额估算** — [#48099](https://github.com/BerriAI/litellm/pull/48099) (OPEN)：图片较多的 Responses 请求此前按文本计数并触发 token 限制拒绝，而 Chat 图片/音频请求预留不足。媒体现在单独估算（图像模型策略、音频时长），并在各代理路径间共享。
- **Rust 桥接层改为强制** — [#46346](https://github.com/BerriAI/litellm/pull/46346) (OPEN)：`_native` 已随每个 wheel 发布，且 sdist 没有它就无法安装；此改动移除了可选加载路径和无用的 `None` 分支。基于 [#46442](https://github.com/BerriAI/litellm/pull/46442) 叠加，后者为 CI 缓存 Rust 构建。
- **嵌入超时传播** — [#48690](https://github.com/BerriAI/litellm/pull/48690) (CLOSED) / [#47305](https://github.com/BerriAI/litellm/pull/47305) (CLOSED)：Gemini 和 Vertex 的 `embed_content` 同步/异步路径忽略了调用方超时，使用 300 秒处理器默认值——50 毫秒超时仍在约 1 秒后返回真实嵌入。现在会正确转发（批处理处理器也一样）。

---

## 5. 稳定性与回归

按严重程度排序。

**严重**

- 🔴 **1.106.x 镜像仍信任已失陷的根 CA** — [#47904](https://github.com/BerriAI/litellm/issues/47904) (OPEN)。修复仅存在于 `main`。尚无回移植 PR。

**高 — 计费正确性**

- 🟠 **当 `provider_response_model` 是价格映射中不存在的 slug（例如带日期的 Anthropic 构建）时，流式请求按 $0 计费** — [#42161](https://github.com/BerriAI/litellm/issues/42161) (CLOSED)。报告者看到 **89 次可计费调用中有 74 次被记录为免费**；非流式计费正确。修复已合入。
- 🟠 **虚拟密钥 `BudgetExceededError` 使用过期花费，而 `/key/info` 报告的花费低于 `max_budget`** — [#27735](https://github.com/BerriAI/litellm/issues/27735) (OPEN，12 条评论，当日讨论最多的 issue)。与 #27639 相关；影响团队范围的虚拟密钥。
- 🟠 **当 `/v1/messages` 携带 Anthropic 格式的 `tool_choice`（`{"type":"auto"|"any"|"tool"}`）时，预算预留会因 `token_counter` KeyError 而按 `max_input_tokens` 确定大小** — [#47396](https://github.com/BerriAI/litellm/issues/47396) (CLOSED)。导致预留膨胀 / 误拒绝。
- 🟠 **自 v1.99.0 起，对于返回 `id: null` 的提供商（Databricks-Gemini），SpendLogs 被静默丢弃** — `request_id` 变为 `"None"` 并在主键上冲突 — [#39749](https://github.com/BerriAI/litellm/issues/39749) (CLOSED)。
- 🟡 **当数据库会话时区 ≠ UTC 时，SpendLogs 分区边界错位** — [#34364](https://github.com/BerriAI/litellm/issues/34364) (OPEN，增强项)。

**高 — 资源泄漏 / 路由**

- 🟠 **`websearch_interception` 对每个携带 `web_search` 工具的流式 `/v1/messages` 请求泄漏一个 `max_parallel_requests` 槽位** — [#46093](https://github.com/BerriAI/litellm/issues/46093) (CLOSED)。v3 限流器的调用前钩子获取该槽位；智能体循环从不释放它，永久缩小密钥容量。
- 🟡 **基于路由器冷却状态的结构化提供商错误 / 路由健康契约** — [#33371](https://github.com/BerriAI/litellm/issues/33371) (OPEN，RFC)。冷却状态在内部存在，但不可机读。

**中 — 协议/转换**

- 🟡 **通过 Chat Completions 适配器流式传输时，`/v1/messages` 适配器发出的 `message_start` 中 `usage.input_tokens: 0`（四项 usage 计数全为零）** — [#48481](https://github.com/BerriAI/litellm/issues/48481) (CLOSED)。真实计数仅在 `message_delta` 中到达；非流式正确。会破坏客户端的早期 token 记账。
- 🟡 **在默认开启思考的 Claude 模型（例如 `anthropic/claude-haiku-5-5`）上，`reasoning_effort: "none"` 会移除思考配置，而不是禁用它** — [#47924](https://github.com/BerriAI/litellm/issues/47924) (OPEN)。上游默认值生效；目前无法强制关闭思考。
- 🟡 **Responses 适配器中围绕 `tool_use` 的 assistant 文本顺序丢失** — `[text, tool_use, text]` 变成 `[function_call, message(text+text)]`，在调用之后重放工具调用前的文本 — [#45478](https://github.com/BerriAI/litellm/pull/45478) (OPEN)。
- 🟡 **格式错误的 Messages `tool_choice` 返回 500 或静默变为 `auto`** — [#46188](https://github.com/BerriAI/litellm/pull/46188) (OPEN)。现在以 400 拒绝；**有意的产品变更**。
- 🟡 **Decisions 转换将省略的概率实体化为 `0.0`** — [#48449](https://github.com/BerriAI/litellm/issues/48449) (CLOSED)。缺失 ≠ 零；信息丢失。
- 🟡 **Responses 流式传输在省略 `logprobs` 时中断**（`event.part.text` 上的 AttributeError）— [#48375](https://github.com/BerriAI/litellm/pull/48375)、[#48686](https://github.com/BerriAI/litellm/pull/48686) (CLOSED)。

**中 — 代理/API 行为**

- 🟡 **来自 #46839 的 token 计数输入优先级回归** — 当 `prompt` 与非空 `messages`/`contents` 同时出现时，现在会返回 400，除非开启 `drop_params` — [#48693](https://github.com/BerriAI/litellm/pull/48693) (OPEN，恢复旧有优先级)。
- 🟡 **BYOK / 静态认证 MCP `tools/list` 拒绝每请求的 `x-mcp-{alias}-authorization`** — [#46664](https://github.com/BerriAI/litellm/issues/46664) (OPEN)。
- 🟡 **非对象 JSON 请求体（`[]`、`123`、`null`）在 `pre_db_read_auth_checks` 中返回 **500** 而不是 **400** — [#43711](https://github.com/BerriAI/litellm/issues/43711) (CLOSED，`AttributeError: 'list' object has no attribute 'get'`)。
- 🟡 **仅企业版可用的 `get_spend_routes` 权限在 `/key/generate` 和 `/user/new` 上产生裸 500；许可证消息仅进入日志** — [#47011](https://github.com/BerriAI/litellm/issues/47011) (CLOSED)。
- 🟡 **`mock_response=""` 为假值 → 在 `completion()`/`acompletion()` 中触发真实提供商请求** — [#48162](https://github.com/BerriAI/litellm/issues/48162) (CLOSED)。测试安全性隐患：空 mock 字符串泄漏了真实流量。
- 🟡 **`hide-secrets` 护栏忽略了 `skip_tool_message_in_guardrail`** — [#48689](https://github.com/BerriAI/litellm/pull/48689) (OPEN)。尽管有该标志，工具结果仍被擦除；工具参数/用户文本/系统提示词仍被脱敏。

**低 — UI / 可观测性**

- [#23998](https://github.com/BerriAI/litellm/issues/23998) (CLOSED) — 在 UI 中添加 LiteLLM Params 后无法移除。
- [#32332](https://github.com/BerriAI/litellm/issues/32332) (CLOSED) — 当 `request_id` 包含 `#` 时，Logs UI 显示 "Request/Response Data Not Available"。
- [#34763](https://github.com/BerriAI/litellm/issues/34763) (CLOSED) — MCP 服务器 UI 省略 `authorization` 认证类型，尽管 API 接受它。
- [#35064](https://github.com/BerriAI/litellm/issues/35064) (OPEN，陈旧) — 在 UI 中找不到 `enable_background_health_checks`。
- [#33690](https://github.com/BerriAI/litellm/issues/33690) (CLOSED) — 文档中 SSO 角色值不匹配（`internal_user_view_only` 与 `internal_user_viewer`）。
- [#19730](https://github.com/BerriAI/litellm/issues/19730) (OPEN，15 👍) — i18n / 多语言仪表盘，仍是最高赞的开启请求。
- [#48688](https://github.com/BerriAI/litellm/pull/48688) (OPEN) — 护栏向导导航按钮溢出对话框。
- [#48600](https://github.com/BerriAI/litellm/pull/48600) (OPEN) — `LITELLM_FIPS_MODE=true` 启动门禁；除非 FIPS 加密 + TLS 验证实际启用，否则拒绝启动。用修正后的探针重做已回滚的 #42700。
- [#42697](https://github.com/BerriAI/litellm/pull/42697) (OPEN) — 启用 `LITELLM_OTEL_V2=true` 时，key/user/team/org 身份现在作为一等 Langfuse 元数据字段在 chat、`/v1/messages` 和 `/v1/responses` 上发出，而不是原始属性 blob。
- [#34734](https://github.com/BerriAI/litellm/issues/34734) (OPEN) — SDK 中基于能力的上游配额/余额探测。

---

## 6. 这对应用开发者意味着什么

1. **在信任仪表盘之前审计流式花费。** 两个独立缺陷（[#42161](https://github.com/BerriAI/litellm/issues/42161)、[#39749](https://github.com/BerriAI/litellm/issues/39749)）导致流式调用记录为 $0 或完全消失。如果你将大部分流量走流式——尤其是通过 Databricks/Gemini 或带日期的 Anthropic 模型 slug——在修复传播到你固定的版本之前，请将 SpendLogs 与提供商发票对账。
2. **固定版本并验证，不要浮动。** 四条并行补丁线已上线，且 1.108.0 是 RC 而不是稳定标签。使用上述 cosign 命令，并将 1.106.x 视为有安全标记，直到 eMudhra 根 CA 回移植落地（[#47904](https://github.com/BerriAI/litellm/issues/47904)）。
3. **`/v1/messages` 仍是变动最频繁的接口面。** 工具调用排序（[#45478](https://github.com/BerriAI/litellm/pull/45478)）、格式错误的 `tool_choice` 现在返回 400（[#46188](https://github.com/BerriAI/litellm/pull/46188)）、`message_start` usage 归零（[#48481](https://github.com/BerriAI/litellm/issues/48481)），以及使用 Anthropic `tool_choice` 时的预算过度预留（[#47396](https://github.com/BerriAI/litellm/issues/47396)）都涉及 Anthropic 兼容路径。流式使用工具的 agent 应显式针对固定版本进行测试。
4. **流式容量可能静默侵蚀。** `web_search` 槽位泄漏（[#46093](https://github.com/BerriAI/litellm/issues/46093)）会为每个受影响的流永久消耗 `max_parallel_requests`——如果长期存活的密钥在数小时内性能下降，请回收它，并在扩容前检查此类泄漏。
5. **护栏延迟税正在消失。** 如果你因延迟而禁用流式或放弃护栏，[#47735](https://github.com/BerriAI/litellm/pull/47735) 为仅提示词护栏恢复了流式快速路径。同样，嵌入超时现在真正生效（[#48690](https://github.com/BerriAI/litellm/pull/48690)）——请移除任何客户端超时规避方案。
6. **不要依赖 `mock_response=""`。** 它会路由到真实提供商（[#48162](https://github.com/BerriAI/litellm/issues/48162)）。在测试中使用非空哨兵值或 `mock_tool_calls`/`mock_timeout`。
7. **FIPS 和 OTEL v2 为可选启用，但正在落地。** [#48600](https://github.com/BerriAI/litellm/pull/48600) 提供失败关闭的 FIPS 启动检查；[#42697](https://github.com/BerriAI/litellm/pull/42697) 在 OTEL v2 映射器下使每请求身份可在 Langfuse 中查询——如果你正在为多租户 agent 流量做埋点，这会很有用。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 摘要 — 2026-10-28

## 今日要闻
过去 24 小时内没有新版本发布；活动集中在 Unsloth Studio 的加固和正确性修复上。影响最大的事项包括：一个 Windows 桌面端训练 bug 会向回收站灌入大量 SQLite WAL/SHM 文件；一个 Anthropic 兼容性回归，`/v1/messages` 返回硬性上下文溢出错误而不是自动压缩；以及针对 `Qwen3.8-Flash-Next` 的两项 FP8/Block Swap 修复。模型支持需求正转向非 NVIDIA 硬件（Intel Arc XPU、NVIDIA/AMD 混合 Linux）和新的 LiquidAI LFM3 系列。

## 新模型与硬件支持
- **LFM3 系列请求** — [#15588](https://github.com/unslothai/unsloth/issues/15588) 请求在 LiquidAI 发布 LFM3 后支持 `LFM3-1.7B`（2 👍）。尚无实现 PR。
- **Intel Arc B580 (XPU) 微调** — [#15583](https://github.com/unslothai/unsloth/issues/15583) 报告称 Windows 11 上的桌面应用未提供 Intel XPU 训练路径。仍处于打开状态，有 5 条评论；未关联后端 PR。
- **NVIDIA/AMD 混合 Linux 上 AMD GPU 不可见** — [#15444](https://github.com/unslothai/unsloth/issues/15444) 显示 Studio Web UI 无法在存在 NVIDIA 卡时枚举 AMD 设备，具体是在 Kali 上使用自定义 `llama.cpp` 构建时。这对异构推理主机很重要。
- **ExLlamaV3 作为推理引擎** — [#15527](https://github.com/unslothai/unsloth/issues/15527) 请求支持 `exllamav3` 和 EXL3 量化排序；请求者指出现有社区 PR 已停滞，因此不会很快实现。
- **Apple MLX 上的 Gemma 4 26B（视觉）** — [#15473](https://github.com/unslothai/unsloth/issues/15473) 报告称视觉路径在 macOS 27.1 / MLX 上输出 `<unused6226>` token，即分词器或多模态投影器不匹配，而不是缺少架构。
- **Qwen3.8-Flash-Next（FP8，`qwen4_exp`）** — 加载缺陷追踪于 [#15632](https://github.com/unslothai/unsloth/pull/15632) 和 [#15633](https://github.com/unslothai/unsloth/pull/15633)；这现在是仓库中修补最活跃的新架构。

## 性能与优化
- **Block Swap 的 `offload_layers` 在 Qwen3.8-Flash-Next 上从未生效** — [#15633](https://github.com/unslothai/unsloth/pull/15633)：CPU 驻留的 n-gram 表使设备映射看起来像两个设备，因此层卸载被静默跳过。配套修复位于 [#15632](https://github.com/unslothai/unsloth/pull/15632)（FP8 n-gram 表缺少 `weight_scale`，导致 FP8 输入进入 `key_proj`）和 `unsloth-zoo#36`（该表被排除在 Block Swap 的槽池之外）。合并来看，这些问题同时影响 FP8 Flash-Next 的显存占用和数值正确性。
- **基准测试页面工作正在进行中** — [#11808](https://github.com/unslothai/unsloth/pull/11808) 针对已加载的 GGUF 扫描投机解码（MTP、n-gram、DSpark、DFlash）、草稿 token 数、KV 缓存类型、上下文长度、并行槽和 RAM 卸载；[#12522](https://github.com/unslothai/unsloth/pull/12522) 添加用于 pp/tg 吞吐量的 `llama-bench` 标签页；[#12521](https://github.com/unslothai/unsloth/pull/12521) 添加按量化分组的 lm-eval-harness 评分。这些是调优服务配置时缺失的测量界面。
- **实时监控重做** — [#15630](https://github.com/unslothai/unsloth/pull/15630) 将内存报告合并为单个统一内存行，并新增刷新率设置；现有的大小调整手柄无法使用。
- **可观测性缺口已确认** — [#14582](https://github.com/unslothai/unsloth/issues/14582) 请求在详细 UI 中显示预填充/加载进度、专家/层卸载量以及实时解码/预填充速度。目前这些数字只能通过上面的基准测试 PR 推断。

## 稳定性与回归
按对生产用户的严重程度排序：

1. **Windows 上 Studio 训练灌满回收站** — [#15552](https://github.com/unslothai/unsloth/issues/15552)：`*.db-wal` / `*.db-shm` 文件大约每秒写入一次 `C:\$Recycle.Bin\<SID>\`；90 分钟的运行会产生 >10,000 个条目（~40 MB）。存在磁盘耗尽风险，并且在端点安全软件看来类似恶意软件。尚无修复 PR。
2. **Anthropic 透传忽略自动压缩** — [#15417](https://github.com/unslothai/unsloth/issues/15417)：当历史超过 `n_ctx` 时，`/v1/messages` 返回不可恢复的 `400 exceed_context_size_error`，即使已启用自动压缩。这会直接破坏 Claude-SDK 风格的客户端。尚无修复 PR。
3. **每个会话残留磁盘占用** — [#14487](https://github.com/unslothai/unsloth/issues/14487)：每次使用桌面端后 C: 盘会损失 200–400 MB，且没有清理路径；自 2026-10-21 起处于打开状态。
4. **Studio 训练期间出现堆栈跟踪** — [#15538](https://github.com/unslothai/unsloth/issues/15538)：Studio Web UI 训练路径上出现未处理异常。
5. **Deep Research 未被调用** — [#15581](https://github.com/unslothai/unsloth/issues/15581)：Gemma-4-26B-A4B 忽略 Deep Research 工具并从权重作答，即工具选择/路由失败，而不是检索失败。
6. **MLX 视觉 token 损坏** — [#15473](https://github.com/unslothai/unsloth/issues/15473)（见上文）；输出静默出错，对下游解析来说比崩溃更糟。

过去 24 小时内已解决：
- **SGLang 自动压缩已修复** — [#15434](https://github.com/unslothai/unsloth/pull/15434)（已关闭）：托管 SGLang 引擎上的长聊天此前在一次超上下文请求后会永久卡住。
- **Qwen3.5-2B 分片权重恢复功能损坏** — [#15405](https://github.com/unslothai/unsloth/issues/15405)（已关闭）：`_MODEL_WEIGHT_RE` 无法匹配 `model.safetensors-00001-of-00001.safetensors`。
- **破坏性“清除全部”转录记录** — [#15570](https://github.com/unslothai/unsloth/pull/15570)（已关闭）：桌面端跳过了浏览器构建中已有的确认对话框。
- **macOS Flux-3 图像编辑** — [#15241](https://github.com/unslothai/unsloth/issues/15241)（已关闭）。

CI/工具链：[#15626](https://github.com/unslothai/unsloth/pull/15626) 阻止 pytest 9.2 的 monkeypatch 删除 `torch._dynamo.config` 条目（间歇性后端 CI 失败）；[#15622](https://github.com/unslothai/unsloth/pull/15622) 修复 `verify_import_hoist.py` 的误报，这些误报目前阻塞两个重构 PR。

## 这对应用开发者意味着什么
- **对于 Anthropic 兼容客户端，不要依赖服务端上下文管理。** 在 [#15417](https://github.com/unslothai/unsloth/issues/15417) 落地之前，请在调用 `/v1/messages` 前自行执行 token 预算和截断；失败模式是 400，而不是透明压缩。
- **MCP 工具定义开销现在是一项真实的上下文成本。** 根据 [#15629](https://github.com/unslothai/unsloth/pull/15629) / [#15631](https://github.com/unslothai/unsloth/pull/15631)，Studio 的 MCP 服务器暴露 23 个工具，在任何工作开始前就消耗 ~18k token 的定义。按工具开关是预期的缓解措施——只启用智能体所需的内容。
- **运行时模型发现将通过 OpenCode 提供** — [#15521](https://github.com/unslothai/unsloth/issues/15521) 跟踪一个上游插件，使 `unsloth start opencode` 用户可以在运行时枚举 Studio 模型，而无需硬编码端点。
- **量化选择仍然是隐式的。** [#14362](https://github.com/unslothai/unsloth/issues/14362) 指出，未显式指定量化的 API 请求会回退到模型默认值（例如 `UD-Q5_K_XL`），无法设置偏好的默认值（如 `Q8_0`）——如果可复现性很重要，请在请求中显式固定量化。
- **缺少带宽控制** — [#15603](https://github.com/unslothai/unsloth/issues/15603) 请求增加下载速率限制；这对拉取许多 GGUF 变体的共享 CI 运行器和预配流水线很有意义。
- **硬件指引：** Intel XPU 和 NVIDIA/AMD 混合 Linux 目前仍不受支持/无法检测（[#15583](https://github.com/unslothai/unsloth/issues/15583)、[#15444](https://github.com/unslothai/unsloth/issues/15444)）；ExLlamaV3 不是可用后端（[#15527](https://github.com/unslothai/unsloth/issues/15527)）。请围绕 NVIDIA CUDA、单一厂商 ROCm 或 Apple MLX 规划部署。
- **在空间受限的系统盘上，避免长时间运行 Windows 桌面端训练**，直到 [#15552](https://github.com/unslothai/unsloth/issues/15552) 修复；训练期间监控 `C:\$Recycle.Bin` 的增长。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*