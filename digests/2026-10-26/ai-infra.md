# AI 基础设施日报 2026-10-26

> 生成时间: 2026-10-26 00:37 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目 AI 基础设施对比 — 2026-10-26

## 1. 生态概览

这六个项目覆盖 AI 基础设施栈：vLLM 和 SGLang 是数据中心推理/服务引擎；llama.cpp、Ollama 和 Unsloth Studio 面向本地/边缘运行时与工具链；LiteLLM 是 LLM 网关/控制平面。活动量很高，但发布节奏不均衡：过去 24 小时内 vLLM/SGLang/Ollama/Unsloth 均无新版本，而 llama.cpp 发布了九个构建，LiteLLM 发布了四个补丁版本。主要主题是生产环境加固，而非功能扩张：长上下文处理、投机解码、量化内核和混合注意力中的静默正确性回归是最紧迫的问题。Blackwell SM90/SM100/SM120、GB10/GB300、ROCm CDNA/RDNA、Intel XPU、Apple Metal/MLX、SYCL 和 Vulkan 之间的硬件碎片化，仍是后端特定 bug 与调优收益的主要来源。

## 2. 活动对比

| 项目 | 24 小时 issue 数 | 24 小时 PR 数 | 发布状态 |
|---|---:|---:|---|
| **vLLM** | n/d | n/d | 无新版本发布。正在推进多模态 CUDA graphs、投机解码、Blackwell/ROCm 调优。 |
| **SGLang** | n/d | n/d | 无新版本发布。Fast Engine Recovery、Kimi-K3、DeepSeek-v4.1、扩散模型和安全加固方面活动频繁。 |
| **llama.cpp** | 触及 66 | 触及 110 | 9 个构建已落地：b11879–b11889；b11885 未发布。 |
| **Ollama** | n/d | n/d | 无版本发布。MLX/MLX-C 版本升级、拉取限流和 Metal watchdog 缓解正在进行中。 |
| **LiteLLM** | n/d | n/d | 4 个补丁版本：v1.106.4、v1.105.5、v1.104.7、v1.103.9。 |
| **Unsloth** | 触及 28 | 更新 221 | 无新版本标签；Studio 仍停留在 v0.1.910-beta / 包 2026.10.8。 |

**注：**“n/d”表示摘要未披露具体数量；“touched/updated”用于明确报告之处。Issue/PR 数反映活动量，而非未关闭积压总量。

## 3. 模型支持竞赛

本时间窗口内没有项目发布重要的新前沿模型版本；大多数模型支持仍处于 PR、issue 或 beta 构建中。因此，竞赛围绕**在途支持的广度**和**特定硬件的就绪度**展开。

- **vLLM — 在途数据中心覆盖最广。** 正在进行的工作覆盖 Intel XPU 上的 Kimi-Linear、ROCm 上的 GLM-5.3-Flash 和 Qwen3-Next/Qwen3.5、MiniMax-M3.1 MXFP8、gpt-oss MXFP4 后端过滤、SM90 上的 DeepSeek-V4/V4.1，以及 Kimi K3 性能跟踪。它仍拥有最广泛的前沿模型 × 加速器矩阵，但许多尚未发布。
- **SGLang — 在选定的前沿服务路径上最深。** 在 Kimi-K3 megakernel 与 DCP 多节点、带 PD 分离的 GLM DSA/MTP、SpecForge DFlash MLA draft、DeepSeek-v4.1 预填充上下文并行、Beam 模型内核以及扩散模型（`ideogram4`、`JoyEcho` 已完成；`omni-dreams` 待处理）方面有强劲工作。它在 Kimi/DeepSeek/GLM 特定服务深度和权重重载机制上领先。
- **llama.cpp — 本地/边缘与量化广度。** 已落地 Qwen 转换改进（`qwen4exp` 张量合并），提出 `JQ2_K`–`JQ6_K` CPU 量化格式，为量化 KV 新增 SYCL DPAS FlashAttention，重写 HIP/RDNA4 KV 解码，并实现 Metal GQA 感知解码。它在 GGUF/本地量化覆盖和消费级硬件后端上领先。
- **Ollama — 本地运行时模型易用性。** MLX/MLX-C 版本升级旨在提升 gemma/flint/laguna 提示处理速度；F16 safetensors 导入元数据修复了 Gemma4 风格的 `ollama create`；MoE CPU 卸载仍是已请求但未实现的功能。没有已发布版本。
- **LiteLLM — 提供商接口面，而非推理支持。** 进行中的工作新增 Sideren 和 Decision Models 提供商；GPT-5.6 Copilot 和 DeepSeek V4 reasoning-effort 问题已关闭，但未必已发布。它竞争的是提供商归一化，而非模型内核。
- **Unsloth — 微调加 Studio 本地推理。** 新增 FLUX.3 Klein 图像编辑、针对 MiniCPM-V-4.7-1B 的 Vulkan 投机解码修复、针对 DeepSeek-V4.1-Flash sidecar 的 Metal OOM 恢复，以及多 GPU 张量拆分。它在本地模型管理和微调相关工作流上领先。

**谁领先：** vLLM 在前沿模型/硬件广度上领先；SGLang 在 Kimi/DeepSeek/GLM 深度和快速重启上领先；llama.cpp/Ollama/Unsloth 在本地/边缘硬件上领先；LiteLLM 在提供商抽象上领先。这些属于不同层级，并非直接替代品。

## 4. 性能前沿

优化工作集中在五个领域：

- **内核与量化矩阵乘法/注意力。** vLLM 报告了一个经调优的 Blackwell 批不变融合 MoE 配置，声称 **解码吞吐量 +48%**。llama.cpp 已落地用于提示处理的 CUDA MMQ FFN gate/up + GLU 融合，并继续推进 SYCL 向量化反量化存储和 work-group argmax。SGLang 正在调优 Kimi-K3 NVFP4 SM107 路径和融合路由准备。
- **KV 缓存、前缀缓存与投机解码的交互。** SGLang 的 Fast Engine Recovery 报告称，借助权重缓存守护进程，Qwen3-235B FP8 权重加载从 **约 306–327 秒降至 1 秒以内**。vLLM 有一份 KV 压缩 RFC 以及多个前缀缓存/投机解码正确性 bug。llama.cpp 警告称，4 位 KV 缓存可能静默回退到 CPU 预填充，导致 **约 30 倍减速**。Unsloth 正通过渲染后提示计数来收紧 KV 准入。
- **分布式服务与并行。** vLLM 跟踪 PP/TP、ROCm 多节点、长预填充饥饿（DGX Spark TP2 上 **3–7 分钟**）以及流水线并行基准测试。SGLang 正在处理上下文并行、PD 分离、HiCache PP 同步吞吐限制和多节点 GB300 问题。Unsloth 新增跨空闲 GPU 对的张量拆分。
- **本地运行时预填充与内存准入。** Ollama 通过缩小预填充块来针对 MLX 提示速度和 Metal watchdog 崩溃。Unsloth 通过将 block-group 固定迁移到后台 pinner，在 A100 VM 上将惰性流式模型加载缩短了 **约 10.9 秒**，并正在修订 embeddings/查找表的 VRAM 核算。
- **网关控制平面效率。** LiteLLM 没有内核工作；它专注于向量存储分页，以及减少由格式错误的键导致的 `SpendLogs` 行增长。网关开销属于运维层面，而非模型吞吐。

**净结论：** vLLM 和 SGLang 正在推进数据中心内核/分布式性能；llama.cpp、Ollama 和 Unsloth 正在优化本地预填充、内存适配和后端广度；LiteLLM 正在优化控制平面正确性和成本核算。

## 5. 层级定位

| 项目 | 层级 | 主要角色 | 竞争维度 |
|---|---|---|---|
| **vLLM** | 数据中心服务引擎 | 高吞吐 OpenAI 兼容服务、分布式推理、前沿模型/硬件支持 | 模型覆盖、内核、分布式服务、正确性 |
| **SGLang** | 数据中心服务引擎 | 结构化生成、RadixAttention/前缀缓存、PD 分离、高性能前沿服务 | Kimi/DeepSeek/GLM 深度、快速重启、缓存/并行 |
| **llama.cpp** | 本地运行时与 GGUF/内核库 | 本地推理、量化格式、广泛的消费级硬件后端 | 后端广度、GGUF 量化质量、提示处理 |
| **Ollama** | 本地运行时发行/用户体验 | 简便的模型拉取/运行、桌面体验、llama.cpp/MLX 封装 | 开发者易用性、macOS/Metal、模型生命周期 |
| **LiteLLM** | LLM 网关/代理 | 提供商抽象、认证、预算、支出日志、OpenAI/Anthropic 兼容性 | 提供商覆盖、成本控制、请求归一化 |
| **Unsloth** | 微调框架 + Studio 本地推理 | 训练加速、本地模型管理、量化/卸载易用性 | 微调速度、Studio 用户体验、本地 GPU 内存适配 |

vLLM/SGLang 并非 Ollama/llama.cpp/Unsloth Studio 的直接竞争对手：前者服务多租户数据中心流量；后者优化单用户/本地或边缘工作流。LiteLLM 作为网关/控制平面位于它们任何一方之前。

## 6. 趋势信号

1. **静默正确性问题现已成为首要生产风险。** 长上下文截断、解码乱码、激活函数错误、投机解码状态丢失和输出非不变性在 vLLM、SGLang 和 llama.cpp 中均有出现。这些故障表现为看似合理但实际错误的输出，而非明确的报错。
2. **投机解码 + 前缀缓存是危险区。** 多个项目报告称，当 EAGLE/MTP/DFlash 与混合注意力、CUDA graphs 或 radix cache 交互时，会出现静默输出损坏或崩溃。黄金提示回归测试是强制要求。
3. **量化必须按后端锁定。** vLLM 报告在 RDNA 3.5 上 ROCm AWQ **比 BF16 慢约 2.5 倍**；llama.cpp 标记 CUDA Q4_1/Q5_1 出现 NaN logits 以及 4 位 KV CPU 回退；NVFP4 对齐问题在 SM120 上持续存在。“量化”并不是可移植的性能保证。
4. **冷启动与权重重载正成为 SLO 差异化因素。** SGLang 的亚秒级权重重载对自动扩缩容和故障恢复具有重要运维意义；Unsloth 和 Ollama 也在着力解决加载延迟和内存准入问题。
5. **硬件碎片化并未收敛。** Blackwell SM90/SM100/SM120、GB10/GB300、ROCm CDNA/RDNA、Intel XPU、Apple MLX、SYCL 和 Vulkan 都有各自不同的缺口。团队应按后端跟踪已知良好构建，而不是跟随 `main`。
6. **网关成熟度落后于模型服务。** LiteLLM 的问题显示存在支出日志丢失、预算重新播种时少计、大整数 JSON 损坏、嵌入扇出 bug 以及静默 `max_retries` 丢失。应将网关支出日志视为对账数据，而非计费真值。
7. **安全加固正在进入结构化输出路径。** llama.cpp 新增了语法递归深度限制；SGLang 正在增加 JSON Schema 深度/状态边界；LiteLLM 存在未认证端点在非对象 JSON 请求体上崩溃的问题。Agent 栈应防御性地校验 schema 和请求体。

**Agent/应用开发者应关注：** 锁定确切版本；升级后运行长上下文、工具调用和结构化输出的黄金测试；避免不安全的量化/投机解码组合；在 DST OOM 循环修复前，将 LiteLLM 代理 TZ 设为 UTC；断言 embedding 数量与输入匹配；保持 MCP/工具载荷较小；并实现客户端重试、超时和 schema 校验，而不是信任网关或解析器默认值。

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-10-26

## 1. 今日要闻

过去 24 小时内没有新的 vLLM 版本发布，但 issue 和 PR 活动显示，围绕多模态 CUDA graphs、投机解码以及 Blackwell/ROCm 调优的工作仍在活跃进行。最严重的信号是前沿模型服务中的正确性回归：Qwen3.8-Flash-Next 在 GB10 上的长提示被静默截断/不可见（[#62776](https://github.com/vllm-project/vllm/issues/62776)），以及 Qwen3.8-27B GDN 混合模型在 PP=3 下出现非法内存访问/乱码（[#63505](https://github.com/vllm-project/vllm/issues/63505)）。积极的一面是，一个针对 Blackwell 调优的 batch-invariant 融合 MoE 配置声称**解码吞吐提升 +48%**（[#63627](https://github.com/vllm-project/vllm/pull/63627)）。

## 2. 发布与破坏性变更

过去 24 小时内没有新版本发布。未宣布正式的 API 或配置弃用。

值得关注的 PR 级行为变更（尚未发布）：
- Anthropic 兼容服务：在探测聊天模板时传入 `tools=None`，以避免错误的 inline-system 合并（[#61920](https://github.com/vllm-project/vllm/pull/61920)）。
- Jamba + LoRA：在初始化时以描述性 `ValueError` 拒绝 `enable_lora=True`，而不是在后续 profiling/权重加载阶段失败（[#61915](https://github.com/vllm-project/vllm/pull/61915)）。
- Pooling runner 校验：当对不支持的架构请求 `--runner pooling` 时提前抛错（[#55570](https://github.com/vllm-project/vllm/pull/55570)）。

## 3. 新模型与硬件支持

正在进行中的支持工作，尚未进入发布版本：
- **Intel XPU / Kimi-Linear**：修复模型分发，使 Kimi-Linear 不会落入 NVIDIA CUTLASS 路径；依赖单独的 MLA 修复（[#63531](https://github.com/vllm-project/vllm/pull/63531)，[#63530](https://github.com/vllm-project/vllm/pull/63530)）。
- **ROCm / GLM-5.3-Flash**：将预计算的 chunk indices 传入 `chunk_kda`，避免 prefill 期间 GPU→CPU 同步（[#63072](https://github.com/vllm-project/vllm/pull/63072)）；在 MI355X 的 MonoKernel decode 路径内加入 MTP verify（[#63555](https://github.com/vllm-project/vllm/pull/63555)）。
- **ROCm / Qwen3-Next & Qwen3.5**：在 ROCm 上启用融合的 QK-norm + RoPE + gate Triton kernel（[#51406](https://github.com/vllm-project/vllm/pull/51406)）。
- **ROCm / MiniMax-M3.1**：在 MI355X/gfx950 上为 16 < M ≤ 256 启用 MXFP8 linear tiles，并在 RMSNorm 中启用 MXFP8 QKV 输入量化（[#62509](https://github.com/vllm-project/vllm/pull/62509)）。
- **gpt-oss MXFP4**：按平台过滤优先后端，使 CUDA 不会考虑 ROCm/XPU/仅 CPU 后端（[#61923](https://github.com/vllm-project/vllm/pull/61923)）。
- **DeepSeek-V4/V4.1 on SM90**：针对 H100/H20 上 mega-MoE 专家路径的功能请求；当前 Hopper 路径回退到 weight-only Marlin（[#57772](https://github.com/vllm-project/vllm/issues/57772)）。
- **Kimi K3**：性能优化跟踪 issue 仍处于开启状态（[#50587](https://github.com/vllm-project/vllm/issues/50587)）。
- **KV compression**：用于传输/存储的可复用 KV 压缩层 RFC，目标基线为 v0.32.0（[#59538](https://github.com/vllm-project/vllm/issues/59538)）。

硬件缺口：
- DGX Spark / Acer GN100 的 aarch64 上不支持 `sm_121` Blackwell（[#36821](https://github.com/vllm-project/vllm/issues/36821)）。
- 在 SM120 上，DeepSeek-V4.1 的 `enable_adaptive_verification` 被拒绝，因为 device-query-lens 支持仅限 SM90/SM100（[#63343](https://github.com/vllm-project/vllm/issues/63343)）。

## 4. 性能与优化

- **Blackwell 融合 MoE**：调优后的 batch-invariant 配置在 `VLLM_BATCH_INVARIANT=1` 下声称**解码吞吐 +48%**；此前 Triton 融合 MoE kernel 使用单一固定配置（[#63627](https://github.com/vllm-project/vllm/pull/63627)）。
- **Top-k sampling kernel**：单体式 Triton top-k kernel 在真实 MiMo-V2.6 logits 上回退到全词表搜索，因为 outlier gather pivot 对 **6–10% 的行** 来说过高（[#61998](https://github.com/vllm-project/vllm/issues/61998)）。
- **长 prefill 饥饿**：Qwen3.8-Flash-Next 的长 prefill 工作负载在 2 节点 DGX Spark TP2 上会周期性使活跃 decode 饥饿 **3–7 分钟**（[#54919](https://github.com/vllm-project/vllm/issues/54919)）。
- **DeepSeek-V4.1-Flash on 8×H200**：用户报告在并发 8 时出现严重延迟，首 token 时间（time-to-first-token）需要数分钟（[#58233](https://github.com/vllm-project/vllm/issues/58233)）。
- **ROCm AWQ on RDNA 3.5**：AutoAWQ checkpoint 从不使用 RDNA W4A16 kernel；在 gfx1151 上 AWQ 解码比 BF16 **慢约 2.5×**（[#63455](https://github.com/vllm-project/vllm/issues/63455)）。
- **ROCm attention 选择**：已关闭的 issue 报告称，在 CDNA 上尽管 `VLLM_ROCM_USE_AITER=1`，MHA decode 仍未优先选择 `ROCM_AITER_FA` 而非 `ROCM_ATTN`（[#46596](https://github.com/vllm-project/vllm/issues/46596)）。
- **基准测试**：`vllm bench forward` 现在默认包含已配置的 MTP/EAGLE/DSpark/DFlash drafter，并提供 `--exclude-drafter` 用于仅测量 target；新增稳态流水线并行支持（[#63622](https://github.com/vllm-project/vllm/pull/63622)）（[#63616](https://github.com/vllm-project/vllm/pull/63616)）。
- **融合 MoE 稳定性/性能**：限制 float32 tile 大小和 pipeline stage，以避免 Ada/SM8x 上出现共享内存 `OutOfResources`（[#61931](https://github.com/vllm-project/vllm/pull/61931)）。

## 5. 稳定性与回归

按严重程度排序：

1. **严重 — Qwen3.8-Flash-Next 长上下文静默丢失**：在 GB10 上使用 v0.32 nightly 时，超出 `indexer_budget`（约 2048 tokens）的提示内容对模型不可见；模型会像提示在约 2K 处结束一样作答（[#62776](https://github.com/vllm-project/vllm/issues/62776)）。
2. **严重 — Qwen3.8-27B GDN 混合 PP=3 回归**：在 sm_86 上，启用前缀缓存时出现非法内存访问（Xid 31），不启用时输出乱码；自 v0.28.0 起回归，在 v0.31/v0.32 和两个 model runner 上均复现（[#63505](https://github.com/vllm-project/vllm/issues/63505)）。
3. **严重 — `torch.compile` 下 NVFP4 输出乱码**：SM120 上 TP8 且每 rank 的 K 未按 32 对齐时，会导致填充后的 block-scale 列读取陈旧内存；eager/Marlin/对齐后的 TP 均正确（[#48898](https://github.com/vllm-project/vllm/issues/48898)）。
4. **严重 — 投机解码 prefill 误分发**：如果某个 prefill 的已调度 token 数等于 `uniform_decode_query_len * num_reqs`，它可能被分发到 FULL spec-decode CUDA graph，导致混合/Qwen3-Next 模型中 GDN 状态静默丢失并输出乱码（[#53051](https://github.com/vllm-project/vllm/issues/53051)）。
5. **高 — XGrammar 间歇性终止**：V2 DFlash 概率性起草加上块拒绝，会间歇性终止结构化输出请求（[#61383](https://github.com/vllm-project/vllm/issues/61383)）。
6. **高 — 持久 KV offload 缓存未按模型 revision 命名空间隔离**：缓存的 KV 条目可能跨模型 revision，存在复用陈旧或不正确缓存的风险（[#49261](https://github.com/vllm-project/vllm/issues/49261)）。
7. **高 — SM120 自适应验证被拒绝**：DeepSeek-V4.1 的 `enable_adaptive_verification` 在 SM120 上失败，尽管硬件可用（[#63343](https://github.com/vllm-project/vllm/issues/63343)）。
8. **高 — vllm-omni Qwen3-TTS 崩溃**：当并发 TTS 流式请求超过 `--max-num-seqs` 时，会因 `ref_context_size` 不匹配而杀死进程，而不是排队/拒绝（[#44933](https://github.com/vllm-project/vllm/issues/44933)）。
9. **中 — MinimaxM2.7 TP2 回归**：v0.24.0 → v0.25/v0.26 在 GH200NVL2 上破坏 TP2（[#49158](https://github.com/vllm-project/vllm/issues/49158)）。
10. **中 — ROCm AWQ 性能回归**：由于未选择 RDNA W4A16 kernel，在 gfx1151 上比 BF16 慢 2.5×（[#63455](https://github.com/vllm-project/vllm/issues/63455)）。
11. **中 — RDNA2 自 0.22 起回归**：持续存在的性能回归报告（[#45372](https://github.com/vllm-project/vllm/issues/45372)）。
12. **中 — tool-call 解析器行为变更**：已关闭的 issue 指出 vLLM 0.22 与 0.23 之间 `qwen3_coder` tool-call 格式不同，可能破坏下游解析器（[#46493](https://github.com/vllm-project/vllm/issues/46493)）。
13. **中 — KV cache dtype 自动解析**：已关闭的使用问题报告称，在 Blackwell SM120 上为 DeepSeek-V4 使用 `--kv-cache-dtype auto` 会静默失败，而不是解析为 `fp8_ds_mla`（[#47174](https://github.com/vllm-project/vllm/issues/47174)）。

相关进行中的修复 PR：
- Float32 融合 MoE 共享内存 OOR 修复（[#61931](https://github.com/vllm-project/vllm/pull/61931)）。
- 针对多节点启动失败的 KV connector engine-ID 跨 PCP rank 同步（[#62916](https://github.com/vllm-project/vllm/pull/62916)）。
- EAGLE 前缀缓存 lookahead block hash（[#61589](https://github.com/vllm-project/vllm/pull/61589)）。
- MTP 投机解码下的混合 GDN 前缀缓存命中（[#52244](https://github.com/vllm-project/vllm/pull/52244)）。

## 6. 对应用开发者的影响

- **固定并验证前沿模型部署。** Qwen3.8-Flash-Next 的长提示在 GB10 上可能被静默截断；Qwen3.8-27B GDN 混合模型在 PP=3 + prefix caching 下不稳定。每次升级后都运行长上下文黄金提示。
- **将投机解码与前缀缓存结合时要谨慎。** 多个未关闭 bug 涉及 EAGLE/MTP/DFlash 与混合 GDN 状态及 CUDA graph 分发的交互；正确性故障可能表现为看似合理但错误的输出，而不是报错。
- **在传输层将结构化输出视为尽力而为。** XGrammar 的间歇性终止意味着 agent 框架应实现重试、超时和 schema 校验回退。
- **Tool-call 解析对版本敏感。** 如果依赖 `qwen3_coder` 或 Anthropic 兼容端点，请跨 vLLM 次版本验证解析器行为，并优先使用显式解析器配置而非默认值。
- **Blackwell SM120 正在改善但并不均衡。** NVFP4 + `torch.compile` 存在对对齐敏感的正确性问题，`kv-cache-dtype auto` 可能无法最优解析，且部分 DeepSeek-V4.1 验证特性仅限 SM90/SM100。
- **ROCm 性能高度依赖模型/量化/硬件组合。** RDNA 3.5 上的 AWQ 目前比 BF16 慢得多；AITER attention 选择在 CDNA 上可能与文档行为不符。
- **针对 spec-decode 的基准测试正在改善。** 更新后的 `vllm bench forward` 默认包含 drafter，使端到端投机解码延迟测量更具代表性。

---

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-10-26

## 1. 今日亮点
过去 24 小时内没有新版本发布，但项目仍保持高度活跃：**Fast Engine Recovery** 路线图报告称，第一阶段权重缓存守护进程的成果已将 Qwen3-235B FP8 的权重加载时间从 ~306–327s 缩短到 1s 以内。稳定性方面，多个高影响正确性 bug 仍处于活跃状态，包括采样请求下投机解码导致服务器崩溃（#43412）、多节点 GB300 上 Kimi-K3 解码乱码（#45367），以及 SM90 上 `flashinfer_mxfp4` 的静默激活值不匹配（#45755）。安全加固也在推进，涉及 JSON-schema 深度/状态边界校验（#40373）。

## 2. 版本发布与破坏性变更
- **过去 24 小时内无版本发布。**
- 值得关注的配置/API 工作正在进行中（尚未构成破坏性变更）：
  - [#45434](https://github.com/sgl-project/sglang/pull/45434) — 新增 KV-layout 注册表和可选的 `cp_kv_layout` 配置，将上下文并行计算布局与 KV 存储布局分离，并采用带填充、按 rank 本地的容量核算。
  - [#46346](https://github.com/sgl-project/sglang/pull/46346) — Rust 服务器现在会在准入前拒绝 `stop_regex` 重复计数中的空白字符；使匹配窗口边界与 CPython 语义保持一致。
  - 已关闭的 RFC [#36431](https://github.com/sgl-project/sglang/issues/36431)（用于 `/v1/chat/completions` 的可恢复原始 token 流）被标记为不活跃——无 API 变更。

## 3. 新模型与硬件支持
- **[#27214](https://github.com/sgl-project/sglang/issues/27214)** — 扩散模型支持跟踪：`ideogram4`（NF4）和 `JoyEcho` 已完成；`omni-dreams` 仍待处理。
- **[#19137](https://github.com/sgl-project/sglang/issues/19137)** — Apple Silicon 路线图仍开放，尚无原生支持；正在征集贡献者。
- **[#39499](https://github.com/sgl-project/sglang/issues/39499)** — dLLM（block-diffusion）服务路线图继续推进；崩溃/错误结果修复优先于新功能。
- **[#42973](https://github.com/sgl-project/sglang/pull/42973)** — 面向 GLM DSA/MTP 的 KVShard 集成，支持 PD 分离和 CPU HiCache，并保留 target/draft latent KV 与 indexer keys/scales 的页归属。
- **[#44676](https://github.com/sgl-project/sglang/pull/44676)** — 通过目标模型的 DeepSeek MLA attention 服务 SpecForge DFlash MLA draft。
- **[#45254](https://github.com/sgl-project/sglang/pull/45254)** — Beam 模型 kernel 支持（融合 beam attention gate、逐元素操作），是 7 个 PR 系列中的第 3 部分。
- **[#46236](https://github.com/sgl-project/sglang/pull/46236)** — 修复 AMD ROCm 10 下 V4.1 融合 k-norm-rope store 的构建问题（HIP wave64 mask `static_assert`）。
- **[#43465](https://github.com/sgl-project/sglang/pull/43465)** — AMD DeepSeek-v4.1 prefill 上下文并行。
- **[#46479](https://github.com/sgl-project/sglang/pull/46479)** — Kimi-K3 megakernel 绑定扩展，加入固定宽度 DSpark 目标验证状态。

## 4. 性能与优化
- **[#33522](https://github.com/sgl-project/sglang/issues/33522)** — Fast Engine Recovery：按 rank 的权重缓存守护进程通过 CUDA IPC 提供量化后权重；在 Qwen3-235B FP8 上，权重加载时间从 **~306–327s 降至 <1s**（第一阶段已在 #27139 落地）。这与重启和自动扩缩容延迟直接相关。
- **[#46439](https://github.com/sgl-project/sglang/pull/46439)** — 扩散模型：在主机拷贝前将视频帧量化为 `uint8`，避免两条返回路径上进行完整的 FP32 片段传输。
- **[#46399](https://github.com/sgl-project/sglang/pull/46399)** — dsv4.1：候选表写入调用方持有的 prefill graph 缓冲区，减少 prefill graph 中的分配抖动。
- **[#46480](https://github.com/sgl-project/sglang/pull/46480)** — Beam search：在 `BeamGroup.finalize()` 中将 token 物化推迟到 top-k beam，而不是物化每个已完成的 beam。
- **[#46473](https://github.com/sgl-project/sglang/pull/46473)** *(closed)* — Kimi-K3 NVFP4 低延迟 SM107 路径：准备 FP4 路由元数据并融合路由准备，以减少小 batch 下每个 MoE 层的启动开销。
- **[#45351](https://github.com/sgl-project/sglang/issues/45351)** — HiCache PP-sync drain 迫使 prefill 调度器在 PP rank 间进入锁步；被报告为流水线并行吞吐限制因素。
- **[#42248](https://github.com/sgl-project/sglang/pull/42248)** — 原生 tensor 输出与 prefill 打分优化（最高优先级，仍未关闭）。
- **[#43304](https://github.com/sgl-project/sglang/issues/43304)** — 提出用于定位多 tokenizer/多 detokenizer IPC 路径瓶颈的指标。

## 5. 稳定性与回归
按严重程度排序：

1. **[#43412](https://github.com/sgl-project/sglang/issues/43412)** *(OPEN)* — **服务器崩溃（SIGKILL）：** 投机解码在首个非贪婪请求时杀死进程树。FlashInfer verify-sampling JIT 因捆绑的 cccl 与 pip CUDA 13 toolkit 不匹配而失败。贪婪流量不受影响。未列出修复 PR。
2. **[#45755](https://github.com/sgl-project/sglang/issues/45755)** *(OPEN)* — **静默正确性：** SM90 上的 Kimi-K3 `flashinfer_mxfp4` 计算的是 GPT-OSS SwiGLU，而不是 SiTU。已在 v0.5.22 和 main 上复现；受上游 FlashInfer 修复阻塞。
3. **[#45367](https://github.com/sgl-project/sglang/issues/45367)** *(OPEN)* — **解码乱码：** 2 节点 GB300 上的 Kimi-K3 DCP all-to-all；`NCCL_GRAPH_REGISTER=0` 可恢复冒烟测试输出。据报告补丁片段与 #45824 匹配。
4. **[#46259](https://github.com/sgl-project/sglang/issues/46259)** *(OPEN)* — **拆分不变性 bug：** FlashInfer prefill 在 Qwen3.5 混合 GDN 模型上，会根据 radix-cache 命中或 chunked prefill 改变贪婪输出。已在 v0.5.19 和 main 上复现。
5. **[#46476](https://github.com/sgl-project/sglang/issues/46476)** *(OPEN)* — 当 `sample_from_anchor=False` 时，DSpark draft query 宽度在 FA3/FA4 上不正确。已有修复 PR：[#46478](https://github.com/sgl-project/sglang/pull/46478) 和 [#46477](https://github.com/sgl-project/sglang/pull/46477)。
6. **[#38515](https://github.com/sgl-project/sglang/issues/38515)** *(OPEN)* — `sgl_kernel` AOT wheel 在 sm_75（RTX 2080 Ti）上存在逐算子 cubin 缺口：`rmsnorm` 因 `cudaErrorNoKernelImageForDevice` 失败，而 `silu_and_mul`/`gelu_and_mul` 正常工作；没有软回退。
7. **[#40373](https://github.com/sgl-project/sglang/pull/40373)** *(OPEN, security)* — 新增 JSON-schema 深度和状态边界校验，防止语法编译期间 DFA/NFA 状态爆炸以及 CPU 线程挂起/DoS。
8. **[#46346](https://github.com/sgl-project/sglang/pull/46346)** *(OPEN)* — Rust 服务器 `stop_regex` 重复计数空白校验修复。
9. **[#25371](https://github.com/sgl-project/sglang/issues/25371)** *(CLOSED, inactive)* — `mem_cache` 池/分配器重构 RFC 已关闭，未落地。

## 6. 这对应用开发者意味着什么
- **自动扩缩容与冷启动延迟：** 权重缓存守护进程（#33522）是运维意义最大的工作。大型 FP8 模型上的亚秒级权重重载，会实质性改变多租户服务的重启 SLO 以及节点故障恢复。
- **在 #43412 解决前，避免使用带投机解码的采样请求**，或固定到 CUDA toolkit 匹配的软件包。失败模式是进程被强制杀死，而不是优雅报错。
- **SM90/Hopper 上的 Kimi-K3：** 将 #45755 视为静默输出风险——模型会运行，但产生错误的激活值。用已知正常的提示词进行验证。在多节点 GB300 上，`NCCL_GRAPH_REGISTER=0` 是 #45367 当前的规避方案。
- **在某些混合注意力模型上，前缀缓存和 chunked prefill 并非完全输出不变**（#46259）。如果你依赖跨缓存状态的精确贪婪可复现性，请添加回归测试。
- **安全：** 如果你接受用户提供的 JSON schema 用于结构化输出，请跟踪 #40373——目前深层嵌套的 schema 可能导致 CPU 线程挂起。
- **Apple Silicon 仍不受支持**（见 #19137）；请只规划 NVIDIA/AMD 主机。
- **Rust 服务器逐渐成熟：** #45449 和 #46346 表明项目持续投入无 tokenizer/模板的 Rust 前端，这应能减少 OpenAI 兼容聊天服务在 Python 侧的开销。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-10-26

## 1. 今日亮点

过去 24 小时非常繁忙，重点在于加固而非新功能：语法递归现已在全代码库范围内限制深度（b11882、b11888），从而消除了一类长期存在的栈溢出缺陷（[#18988](https://github.com/ggml-org/llama.cpp/issues/18988)）。CUDA 在 MMQ prompt 处理中实现了 FFN gate/up + GLU 融合（[#29948](https://github.com/ggml-org/llama.cpp/pull/29948)），同时量化正确性获得了两项修复（`llama-quant` 中的 4D 张量量化 bug、ue4m3 舍入）。在风险方面，CUDA Q4_1/Q5_1 mat-vec 内核中一个导致 NaN logits 的未修复正确性缺陷（[#30950](https://github.com/ggml-org/llama.cpp/issues/30950)）是 CUDA 算子的最高优先级事项。

## 2. 版本发布与破坏性变更

共合入九个构建（b11879–b11889；b11885 未在此窗口发布）。

- **b11888 / b11882 — 语法递归深度限制现已成为硬性要求**（[#31153](https://github.com/ggml-org/llama.cpp/pull/31153)、[#29495](https://github.com/ggml-org/llama.cpp/pull/29495) / [#29755](https://github.com/ggml-org/llama.cpp/pull/29755)）。语法/peg 解析器中的所有递归函数现在都带有显式限制；超过该限制的语法会被拒绝，而不是导致栈溢出。**迁移说明：** 以前“能用”的深层嵌套 GBNF（例如嵌套重复）现在可能在解析时快速失败。这是针对一类安全缺陷的有意加固。
- **b11881 — 所有内存缓存现在都会遵守 `no_alloc`**（[#31087](https://github.com/ggml-org/llama.cpp/pull/31087)）。对依赖 `no_alloc` 路径的嵌入方而言是行为变更；仍有一个 TODO：将 `no_alloc` 移出 `llama_hparams`。
- **b11880 — `llama-gguf` 合并会拒绝被截断的张量数据**（[#31044](https://github.com/ggml-org/llama.cpp/pull/31044)）。格式错误的 GGUF 合并现在会失败，而不是静默产生损坏输出。
- **b11887 — 调整了 `test-llama-context` 的默认线程数**（[#31157](https://github.com/ggml-org/llama.cpp/pull/31157)）；这也暴露了一个 WebGPU CI 竞态，已在 [#31162](https://github.com/ggml-org/llama.cpp/pull/31162) 中修复。
- **b11889 — CUDA：用 `std::string` 替换 `archName` 的 VLA**（[#30463](https://github.com/ggml-org/llama.cpp/pull/30463)）。Clang `-Wvla-cxx-extension` 清理，无功能变更。

## 3. 新模型与硬件支持

- **Qwen 转换改进** — b11886 在转换期间合并张量，并移除 `qwen4exp` 的预缩放/常量（[#31028](https://github.com/ggml-org/llama.cpp/pull/31028)）。减少转换后的张量数量，并修复 qwen4-exp 转换的边界情况。
- **新量化格式 `JQ2_K`–`JQ6_K`（CPU）** 在 [#30715](https://github.com/ggml-org/llama.cpp/pull/30715) 中提出 — 在 CPU 路径上重新实现 ik_llama.cpp 风格的 IQ 布局；涉及测试、示例、Metal 和 SYCL 标签，因此后端覆盖是部分的。
- **SYCL：面向量化 KV 缓存的 DPAS flash-attention 内核**（[#31125](https://github.com/ggml-org/llama.cpp/pull/31125)）— 在 Xe2（B580、B570、B70、Lunar Lake、Panther Lake）上支持 q4_0/q8_0 KV，head size 为 64/96/128/192，支持 1–31 个 query token，包括 spec-decode 草稿。
- **HIP/RDNA4：KV 缓存解码重写**（[#30803](https://github.com/ggml-org/llama.cpp/pull/30803)）— 每个 lane 使用一个 32 值的 K block，而不是按 query head 重新读取。
- **Metal：GQA 感知的解码路径**（[#30728](https://github.com/ggml-org/llama.cpp/pull/30728)）— 对齐 CUDA 的 `use_gqa_opt`，避免在每个解码步骤中把共享 KV head 重复流式读取 G 次。

## 4. 性能与优化

- **CUDA MMQ FFN 融合已合入**（b11879，[#29948](https://github.com/ggml-org/llama.cpp/pull/29948)）：稠密 FFN 的 gate 和 up matmul 作为单次 `mul_mat_q` 启动运行，并在回写时应用 GLU。直接提升 prompt 处理吞吐量。
- **SYCL 矢量化重排布局反量化存储**（[#30976](https://github.com/ggml-org/llama.cpp/pull/30976)）将 [#30775](https://github.com/ggml-org/llama.cpp/pull/30775) 的 flat-launch/矢量化存储方法扩展到 Q4_0、K-quants、IQ3 和 MXFP4；**SYCL work-group argmax** 用于短 TOP_K 行（[#31110](https://github.com/ggml-org/llama.cpp/pull/31110)）。
- **MoE 内存调优** — 小型 MoE 缓存现在会在低于约 1.8× 一个 token 的专家数时发出警告/下调 SLRU 保护集（[#30848](https://github.com/ggml-org/llama.cpp/pull/30848)）；`llama_moe_cache` 可通过 `--moe-cache-cpu-share` 在 CPU 上执行未命中的专家，并附带一个调优脚本（[#30966](https://github.com/ggml-org/llama.cpp/pull/30966)）。
- **量化自主性** — `--target-size` / `--target-bpw` 用于按张量进行最优量化混合（[#15550](https://github.com/ggml-org/llama.cpp/pull/15550)，长期运行）。
- **需关注的未修复回归：** RDNA1（gfx1010）上的 Vulkan prompt 处理在 b10455 到 b11429 之间约慢 45%（[#30039](https://github.com/ggml-org/llama.cpp/issues/30039)）。
- **静默性能悬崖：** 默认构建仍禁用 `GGML_CUDA_FA_ALL_QUANTS`，因此 4-bit KV 缓存会静默回退到 CPU 预填充——有报告称约 30× 减速且无警告（[#28633](https://github.com/ggml-org/llama.cpp/issues/28633)）。

## 5. 稳定性与回归

按严重程度排序；除非另有说明，均为未关闭状态。

1. **CUDA Q4_1/Q5_1 mat-vec fp16 溢出 → NaN logits**（[#30950](https://github.com/ggml-org/llama.cpp/issues/30950)）。block-offset × Q8_1 块求和溢出 fp16；`gpt-oss` 输出 `"G"`，logits 变为 NaN。已在 b11800/b11538 上复现，master（b11803）上的代码未变。尚无修复 PR——**目前请避免在 CUDA 上使用 Q4_1/Q5_1**。
2. **Windows 发布版上使用 flash attention 时 CUDA 非法内存访问**（[#30436](https://github.com/ggml-org/llama.cpp/issues/30436)）。Gemma 4 12B QAT Q4_0 + mmproj，RTX 4080 Super，b11577/b10268；`-fa off` 可用。尚无修复 PR。
3. **`--split-mode tensor` + DFlash2 草稿 → `ggml-backend-meta.cpp` 中的 `GGML_ASSERT`**（[#28777](https://github.com/ggml-org/llama.cpp/issues/28777)）。`--split-mode layer` 是规避方案。
4. **`ggml_gallocr` 在固定图位置发生身份变化时复用陈旧分配计划**（[#28448](https://github.com/ggml-org/llama.cpp/issues/28448)）— 稀疏 MoE 路由时会造成静默损坏。严重但小众。
5. **MCP stdio 服务器在工具调用 >1–5 KB 时死锁**（[#28723](https://github.com/ggml-org/llama.cpp/issues/28723)），仅限由配置启动的服务器；已在 b10900 和 b10472 上复现。
6. **Vulkan RDNA1 prompt 处理回归（约 45%）**（[#30039](https://github.com/ggml-org/llama.cpp/issues/30039)）。
7. **双 GPU CUDA 输出乱码**，Qwen3.6-27B，RTX 5060 Ti + 3060（[#26257](https://github.com/ggml-org/llama.cpp/issues/26257)）；单 GPU 正常。
8. **Metal + qwen4exp 长上下文截断** — 输出 1 个 token 后即 EOS，阈值会随 quant/KV quant/n_ctx 变化（[#28805](https://github.com/ggml-org/llama.cpp/issues/28805)）。
9. **已修复：** GBNF 嵌套重复导致的栈溢出（[#18988](https://github.com/ggml-org/llama.cpp/issues/18988)）通过 b11882/b11888 修复；`llama-quant` 中 4D 张量量化丢行（b11883，[#30820](https://github.com/ggml-org/llama.cpp/pull/30820)）；dry-sequence-breaker 的 CPU/内存耗尽（b11884，[#31035](https://github.com/ggml-org/llama.cpp/pull/31035)）；`ggml_fp32_to_ue4m3` 饱和/RNE 舍入（[#31020](https://github.com/ggml-org/llama.cpp/pull/31020)）。
10. **已作为陈旧问题关闭，未确认修复：** 按请求的 LoRA prompt 缓存污染（[#26207](https://github.com/ggml-org/llama.cpp/issues/26207)）、Metal `--embeddings` 全 NaN 输出（[#27784](https://github.com/ggml-org/llama.cpp/issues/27784)）、DSpark VRAM 泄漏（[#27155](https://github.com/ggml-org/llama.cpp/issues/27155)）。应视为未验证，而非已解决。

## 6. 对应用开发者的意义

- **如果你使用语法/结构化输出，请升级。** 递归限制（b11882、b11888）是一项安全修复，但在全面推出前，请用新的深度上限验证你的 JSON-schema/GBNF 语法——失败模式将从推理时的栈溢出变为解析时的拒绝。
- **按后端固定量化方案。** CUDA 上的 Q4_1/Q5_1 当前不安全（[#30950](https://github.com/ggml-org/llama.cpp/issues/30950)）；Q4_K/Q6_K 仍是更安全的默认选择。在 CUDA 上，如果你希望 4-bit KV 缓存真正在 GPU 上运行，请显式启用 `GGML_CUDA_FA_ALL_QUANTS`（[#28633](https://github.com/ggml-org/llama.cpp/issues/28633)）。
- **多 GPU + 推测解码：** 在 [#28777](https://github.com/ggml-org/llama.cpp/issues/28777) 解决之前，草稿模型设置请坚持使用 `--split-mode layer`。
- **Agent/工具调用栈：** 上述 MCP stdio 在约 1–5 KB 负载以上会死锁（[#28723](https://github.com/ggml-org/llama.cpp/issues/28723)），这会困扰大型工具 schema——优先使用 HTTP/SSE MCP 传输，或保持工具调用体较小。
- **prompt 处理的收益首先落在 CUDA 上**（MMQ FFN 融合），Metal GQA 和 SYCL 量化 KV FA 随后在 PR 中跟进；如果你运行长系统提示，近期收益在 CUDA 预填充。
- **Qwen 系列模型的转换正在改进**（张量合并、qwen4exp 清理）——升级到 b11886+ 时，应重新转换而不是重新下载，并且如果你围绕 GGUF 布局编写脚本，请重新核对张量数量。
- **运维纪律：** issue 数量很高（24 小时内触及 66 个 issue、110 个 PR），且许多是后端特定回归。按后端跟踪一个已知良好的构建，而不是持续跟随 `master`。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-10-26

## 今日亮点

过去 24 小时内没有新版本发布，但队列很活跃：一次 MLX/MLX-C 版本升级（PR #19291）旨在为 gemma/flint/laguna 带来提示处理加速；长期被请求的 `ollama pull --limit-rate/--limit-streams` 实现（PR #19288）终于处理了 2024 年 1 月的 issue #2006。稳定性方面，影响最大的事项是 Radeon 890M iGPU 上的 ROCm 回归（0.40.6/0.40.7 对比可用的 0.33.3），以及 6-GPU 主机上的误驱逐/陈旧 VRAM bug，后者已有修复 PR 处于打开状态。

---

## 发布与破坏性变更

无 — 过去 24 小时没有发布。没有 API/配置迁移落地。注意 PR #19285 提议为更新后自动重启设置进行 schema 升级（`migrateV19ToV20`），但仍处于打开状态。

---

## 新模型与硬件支持

- **MLX 后端版本升级（PR #19291）** — `mlx` 和 `mlx-c` 已更新以利用新的 `window_size` 特性，目标是提升 gemma/flint/laguna 的提示速度。[ollama/ollama#19291](https://github.com/ollama/ollama/pull/19291)
- **F16 safetensors 导入元数据（PR #19275）** — `inferSafetensorsConfig` 目前对 F16/BF16 导入（例如 Gemma4）将 `ModelFamily`、`ModelFamilies` 和 `FileType` 留空；此 PR 修复非量化模型的 `ollama create` 输出。[ollama/ollama#19275](https://github.com/ollama/ollama/pull/19275)
- **MoE CPU offload（issue #11772，仍开放）** — 请求通过 `OLLAMA_MOE_OFFLOAD=FULL|PARTIAL|NONE` 策略暴露 llama.cpp 的 MoE 层 CPU offload，以减少 VRAM 占用。73 👍，仍未实现。[ollama/ollama#11772](https://github.com/ollama/ollama/issues/11772)

---

## 性能与优化

- **MLX 提示加速** — PR #19291 是今天具体的性能项；`window_size` 变更针对 Apple Silicon 上 gemma/flint/laguna 的预填充。尚未发布基准测试数据。[ollama/ollama#19291](https://github.com/ollama/ollama/pull/19291)
- **长提示的 Metal watchdog 缓解（PR #19280）** — 随着上下文增长缩小预填充块大小，使单个 Metal command buffer 在分析超长输入（例如大型 XML 文件）时不会超过 macOS GPU watchdog。直接关联 #19278 中的崩溃。[ollama/ollama#19280](https://github.com/ollama/ollama/pull/19280)
- **下载吞吐控制（PR #19288）** — 为 `ollama pull` 增加 `--limit-rate` 和 `--limit-streams`，各自都有等价的环境变量；此前并发度会自适应带宽（#2221），但没有用户可设置的上限（#2006）。[ollama/ollama#19288](https://github.com/ollama/ollama/pull/19288)
- **Qwen3.5 4B GGUF 在 16GB M4 上的提示处理（issue #19282）** — 用户报告寻求关于通过 llama.cpp 进行 100% GPU offload 时预期预填充性能的指导；尚未确认缺陷。[ollama/ollama#19282](https://github.com/ollama/ollama/issues/19282)

---

## 稳定性与回归

**高严重性**

- **Radeon 890M（gfx1150 iGPU）上的 ROCm 回归** — 在 0.40.6 和 0.40.7 上出现乱码输出和 `Memory access fault by GPU`；0.33.3 正常。二进制版本回归，尚无修复 PR。[ollama/ollama#19290](https://github.com/ollama/ollama/issues/19290)
- **MLX runner 中的 macOS Metal 崩溃** — 在分析 XML 文件时，0.40.9 上出现 `panic: mlx: [METAL] Command buffer execution failed`。缓解 PR #19280（缩小预填充块）处于打开状态。[ollama/ollama#19278](https://github.com/ollama/ollama/issues/19278) · [PR #19280](https://github.com/ollama/ollama/pull/19280)
- **陈旧 VRAM → 6-GPU 主机上的误驱逐** — 预加载空闲内存刷新达到 3s 截止时间（`GPU discovery watchdog timed out`），导致使用量被高估、runner 被驱逐，甚至为了满足 GPU 预测而驱逐一个仅 CPU 模型。修复 PR #19284 防止驱逐仅 CPU 的 runner。[ollama/ollama#19279](https://github.com/ollama/ollama/issues/19279) · [PR #19284](https://github.com/ollama/ollama/pull/19284)
- **全新安装不可用（0.40.x）** — 用户报告更新后损坏；完全卸载/重装和模型清理均无帮助。12 条评论，未确定根因。[ollama/ollama#19276](https://github.com/ollama/ollama/issues/19276)

**中严重性 — 正确性 / 协议**

- **`/v1/completions` 对原始 prompt 应用聊天模板**（已关闭）— OpenAI 兼容的 completions 端点会把 prompt 包装进模型模板；OpenAI 语义是发送原始内容。变通方法仍是使用 `/api/generate` 并设置 `raw: true`。[ollama/ollama#19289](https://github.com/ollama/ollama/issues/19289)
- **OpenAI 兼容：assistant 的 `tool_calls` 被重新附加到上一条消息** — 当 `content` 为 `[]` 时，assistant 消息被丢弃，其工具调用/推理被附加到上一轮，从而破坏历史。[ollama/ollama#19151](https://github.com/ollama/ollama/issues/19151)
- **gemma4 未闭合列表的工具调用被静默丢弃** — 解析器修复会闭合 `}`，但从不闭合 `]`，因此响应最终既没有 `tool_calls` 也没有 `content`。修复 PR #19274 处于打开状态。[ollama/ollama#19273](https://github.com/ollama/ollama/issues/19273) · [PR #19274](https://github.com/ollama/ollama/pull/19274)
- **qwen3.5 工具调用可能在 thinking 通道关闭前开启**（PR #18624）— 部分标签测试在完整检测到 `<tool_call>` 之前运行，因此落在 `<parameter…` 内部的 chunk 边界会误解析。[ollama/ollama#18624](https://github.com/ollama/ollama/pull/18624)
- **截断的工具调用被交给客户端**（PR #17564）— 因 token 上限而结束的响应可能发出写了一半的工具调用。[ollama/ollama#17564](https://github.com/ollama/ollama/pull/17564)
- **MLX 忽略 `options.stop`**（PR #19267）— MLX runner 上会丢弃停止序列，但 llama.cpp 上会遵守，导致相同请求在不同引擎间产生不同行为。[ollama/ollama#19267](https://github.com/ollama/ollama/pull/19267)
- **Anthropic 流式传输丢弃迟到的 thinking**（PR #19110）— 在流式 `/v1/messages` 响应中，文本/tool_use 之后到达的 thinking 会被丢弃。[ollama/ollama#19110](https://github.com/ollama/ollama/pull/19110)

**低严重性**

- **Cloud：Beam 通过 OMP 返回空响应**，而通过 OpenCode Go 正常 — 客户端特定重试耗尽。[ollama/ollama#19270](https://github.com/ollama/ollama/issues/19270)
- **Claude Code + 云模型：连接被拒绝**，Windows 11、Ollama 0.40.11。[ollama/ollama#19281](https://github.com/ollama/ollama/issues/19281)
- **`x/transfer` 无界认证重试**（PR #18452）— 未变化的 401 会无限递归，并可能被掩盖为缓存未命中。[ollama/ollama#18452](https://github.com/ollama/ollama/pull/18452)
- **`mllama` 未知架构**（Llama3.2-vision，已关闭，#16547）；**Windows 10 桌面启动时出现多余 PowerShell 窗口**（#18415）；**初始加载时的侧边栏动画**（PR #19286）。[ollama/ollama#16547](https://github.com/ollama/ollama/issues/16547) · [ollama/ollama#18415](https://github.com/ollama/ollama/issues/18415) · [ollama/ollama#19286](https://github.com/ollama/ollama/pull/19286)

---

## 这对应用开发者意味着什么

- **工具调用可靠性是目前的主要风险。** 多个未关闭 issue 会影响 agent 循环：gemma4 会丢弃格式错误但可恢复的工具调用（#19273），qwen3.5 可能在 thinking 块中途开启调用（#18624），截断的生成会交给你部分调用（#17564），OpenAI 兼容请求中空数组 `content` 会破坏消息顺序（#19151）。如果你通过 `/v1/chat/completions` 在 Gemma4/Qwen3.5 上运行 agent，请校验 `tool_calls` 的形状，并将空响应视为可重试。
- **不要依赖 `/v1/completions` 进行原始提示。** 它会应用聊天模板（#19289，已关闭）。如需原始文本补全语义，请使用 `/api/generate` 并设置 `raw: true`。
- **MLX 与 llama.cpp 尚未在行为上等价。** 停止序列目前在 MLX 上被忽略（#19267），因此相同请求在不同后端可能生成不同长度。请按平台固定预期，或显式设置 `num_predict`。
- **ROCm iGPU 上升级需谨慎。** 如果你使用 Radeon 890M（gfx1150），0.40.6/0.40.7 会产生乱码输出和 GPU page fault；据报告 0.33.3 正常。在该硬件上暂缓升级，直到 #19290 完成分诊。
- **多 GPU 调度器应留意误驱逐。** 6-GPU 主机上的 3s 空闲内存刷新截止时间可能基于陈旧 VRAM 读数驱逐热 runner——包括仅 CPU 的 runner（#19279）。PR #19284 部分解决此问题；在合并前，预期高负载下会出现冷启动延迟尖峰。
- **macOS 上的长上下文 / 大型文档工作负载。** 如果你向 MLX 模型输入 XML/HTML/大型文件，Metal watchdog 崩溃（#19278）和预填充分块缓解（#19280）直接相关。

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-10-26

## 1. 今日亮点

LiteLLM 在四个维护中的次版本线（v1.106.4、v1.105.5、v1.104.7、v1.103.9）上发布了四个补丁版本，全部提供可通过 cosign 验证的镜像，但信息源中的发布说明正文被截断——升级前请核对 changelog。最紧急的运维信号是 [#48286](https://github.com/BerriAI/litellm/issues/48286)：代理启动循环在夏令时回拨时段被 OOM kill，2026-10-25 影响了一个 Europe/Paris 部署。成本核算正确性是第二个主题，包括花费日志批次丢失、预算 reseed 错误、无效 key 的花费记录，均处于积极讨论或修复中。

## 2. 发布与破坏性变更

过去 24 小时有四个版本，表明这是活跃的 backport 维护，而不是单 head 发布：

| 版本 | 备注 |
|---|---|
| [v1.106.4](https://github.com/BerriAI/litellm/releases) | 1.106 系列最新版本 |
| [v1.105.5](https://github.com/BerriAI/litellm/releases) | Backport 分支 |
| [v1.104.7](https://github.com/BerriAI/litellm/releases) | Backport 分支 |
| [v1.103.9](https://github.com/BerriAI/litellm/releases) | Backport 分支 |

- 四个版本都使用同一密钥（`e82a3c646908ba0a6ee0beddc4310d7a6cac4065/cosign.pub`）发布 cosign 验证说明，并声明该命令同样适用于 `litellm-database`、`litellm-non_root` 和 `docker.io/litellm/*` 镜像——各制品间的供应链验证保持一致。
- **在此窗口内看不到破坏性变更、配置迁移或弃用项。** 然而，每个发布正文都在 `## Wha` 处被截断，因此没有证据并不等于不存在。在生产上线前，请阅读完整发布说明，并将 `config.yaml` schema 与 1.106.x 做 diff。
- 鉴于四条并行线都在接收补丁，请显式固定版本（例如 `1.106.4`），而不是浮动次版本标签；backport 节奏表明修复会异步落到较旧版本线。

## 3. 新模型与硬件支持

此窗口未出现 CUDA/ROCm/Metal/CPU 后端工作、架构新增或量化格式支持。模型/提供方层面的变化：

- **[PR #48356](https://github.com/BerriAI/litellm/pull/48356)（开放）** — 将 Sideren 添加为 JSON 配置的 OpenAI 兼容提供方。仅设置 `SIDEREN_API_KEY` 时，`sideren/<model>` 即可用于聊天补全、`/responses` 和 `/messages`。值得注意的是，它只是一个纯 `providers.json` 条目，无需维护 Python 模块，且模型列表保留在提供方侧。
- **[PR #46901](https://github.com/BerriAI/litellm/pull/46901)（开放）** — 将 Decision Models（`decisionmodels.io`）添加到共享 Jev 决策流：`BaseDecisionsConfig` 子类、注册、价格条目、Add-Model UI 表单、README 行以及测试。
- **[Issue #33893](https://github.com/BerriAI/litellm/issues/33893)（已关闭）** — 请求在 GitHub Copilot 上支持 GPT-5.6，并直接发起上游 `/responses` 调用，而不是回退到 Chat API。已关闭，但关闭并不确认已实现；在依赖 `github_copilot` 的原生 `/responses` 之前，请在 1.106.4 中验证行为。
- **[Issue #27439](https://github.com/BerriAI/litellm/issues/27439)（已关闭）** — DeepSeek V4 Pro/Flash 的分级 `reasoning_effort`（`"high"`/`"max"`）曾被剥离为 `thinking: {"type": "enabled"}`。已关闭；如果你依赖 DeepSeek V4 上按 effort 分级的推理，值得重新测试。
- **[Issue #29367](https://github.com/BerriAI/litellm/issues/29367)（已关闭）** — 将 FunASR / SenseVoice 作为音频转写提供方。已关闭且没有明显合并；视为不支持。
- **[Issue #33670](https://github.com/BerriAI/litellm/issues/33670)（开放）** — opencode zen 和 agnes-ai 的模型覆盖缺失，另有一个模型绑定多个 key 的请求。
- **[PR #48191](https://github.com/BerriAI/litellm/pull/48191)（开放）** — 修复 JSON 定义的 OpenAI 兼容提供方上未映射模型丢失 `tools`、`tool_choice` 等字段的问题，原因是 `supports_function_calling` 默认为 false。影响 `scaleway`、`ano-gpt/z-ai/glm-5.3-flash` 及类似项。
- **[PR #43164](https://github.com/BerriAI/litellm/pull/43164)（开放）** — `nadir/auto` 当前会拒绝 `tools`、`tool_choice`、`parallel_tool_calls`；在 `drop_params: true` 时，tools 会被静默剥离，模型会以纯文本作答。它还阻止将未定价调用按 $0 记录。

## 4. 性能与优化

此窗口没有落地带具体基准数字的吞吐、延迟、内存或内核工作。以下相邻项可降低后端负载或无界增长：

- **[PR #48353](https://github.com/BerriAI/litellm/pull/48353)（开放）** — 向量存储列表返回按 ID 排序的稳定分页，并拒绝非正数页码；仪表盘现在会遍历每一页，因此管理员可以浏览前 100 个存储之后的内容。
- **[PR #48357](https://github.com/BerriAI/litellm/pull/48357)（开放）** — 阻止未知/畸形虚拟 key 创建花费记录。这直接减少扫描或客户端配置错误流量下 `LiteLLM_SpendLogs` 的行增长和基数，同时保持被拒绝流量的计数器不变。
- **[Issue #48286](https://github.com/BerriAI/litellm/issues/48286)** 是内存增长病态问题，而非优化项：在夏令时回拨时段重启时，内存在 `Application startup complete` 之前无界增长，并大约每分钟被 OOM kill 一次。

## 5. 稳定性与回归

按严重程度排序。所有条目均在最近 24 小时内更新。

**严重**

- **[#48286](https://github.com/BerriAI/litellm/issues/48286)（开放）** — 代理启动无限循环，并在夏令时回拨时段被 OOM kill；2026-10-25 02:00 CET 在 Europe/Paris 观察到，与 `scheduled_job_stagger` 有关。每次重启都会无界增长。**未发现修复 PR。** 如果你运行在观察夏令时的时区，这在春季拨快转换时也会复发——缓解措施是禁用 stagger 任务或将容器 TZ 固定为 UTC。
- **[#48304](https://github.com/BerriAI/litellm/issues/48304)（开放）** — 认证失败处理器假定解析后的 JSON body 是 dict 并调用 `.get()`，因此任何合法的非对象 body（`[]`、`null`、`"s"`、`1`、`true`）都会触发未处理的 `AttributeError`，并在**未认证端点上**返回 500。修复 PR：**[#48329](https://github.com/BerriAI/litellm/pull/48329)（开放）**。

**高**

- **[#33873](https://github.com/BerriAI/litellm/issues/33873)（开放）** — 当非传输层数据库写入失败时，花费日志批次会被丢弃，造成静默的计费和审计缺口。2026-08-16 重新划定范围；传输失败那一半已修复，这一半尚未修复。**未发现修复 PR。**
- **[#47947](https://github.com/BerriAI/litellm/issues/47947)（开放）** — 代理在转发前将大于 64 位的 JSON 整数重写为浮点数。工具 schema 中的 `"maximum": 12345678901234567890123` 到达模型时变成 `1.2345678901234568e+22`；请求会静默成功。这是 schema 密集型工具使用场景中的数据损坏类 bug。
- **[#38823](https://github.com/BerriAI/litellm/issues/38823)（开放）** — `vertex_ai/` embeddings 在输入为列表时只返回一个向量，而 `usage.prompt_tokens` 和计费成本覆盖所有输入。HTTP 200，因此调用方丢失 N−1 个向量却为 N 个付费。静默正确性 + 计费 bug。
- **[#48238](https://github.com/BerriAI/litellm/issues/48238)（开放）** — 在 Vertex Model Garden 专用端点上流式调用推理模型（`vertex_ai/openai/<ENDPOINT_ID>`、SGLang 后端、GLM-5.3）会丢弃 `reasoning_content` 增量、额外工具调用和 usage 详情。非流式正确；`hosted_vllm/` 能正确流式输出推理内容。

**中**

- **[#48197](https://github.com/BerriAI/litellm/issues/48197)（已关闭）** — 在 `/v1/messages` 上，客户端在第一个 chunk 之前断开会让上游 LLM 请求继续运行（成本泄漏）。针对 1.106.2 报告；已关闭，请在 1.106.4 中确认修复。
- **[#35570](https://github.com/BerriAI/litellm/issues/35570)（开放）** — 当预留的花费计数器消失或变化时，预留 reseed 将实际成本标记为已应用但不递增；预算执行可能少计。
- **[#34503](https://github.com/BerriAI/litellm/issues/34503)（开放）** — Scaleway 提供方未在 `/v1/embeddings` 上路由，抛出 `LiteLLMUnknownProvider`。聊天路径正常；仅 embeddings 的路由缺口。
- **[#34364](https://github.com/BerriAI/litellm/issues/34364)（开放）** — 当数据库会话时区不是 UTC 时，`LiteLLM_SpendLogs` 分区边界会错位。
- **[#22821](https://github.com/BerriAI/litellm/issues/22821)（已关闭）** — 在 Anthropic 原生路径上，启用 `output_parse_pii: true` 的 Presidio 护栏会遮蔽输入 PII，但从不还原输出，并且通过 Claude Code 使用工具会返回 400。相关开放修复：**[#48334](https://github.com/BerriAI/litellm/pull/48334)（开放）** 解决用户提供的 `<PERSON_1>` token 与 Presidio 生成的占位符发生冲突的问题。
- **[#48324](https://github.com/BerriAI/litellm/pull/48324)（开放）** — Ollama 会拒绝单个字符串形式的 `stop` 序列；该 PR 将字符串或数组归一化。

**较低严重度 / 小问题**

- **[#46335](https://github.com/BerriAI/litellm/issues/46335)（开放）** — 当 `STORE_MODEL_IN_DB` 为 false 时，Admin UI 设置无法编辑。
- **[#46045](https://github.com/BerriAI/litellm/issues/46045)（已关闭）** — 当设置了 tools 且未跳过 MCP handler 时，出现 `ModuleNotFoundError: No module named 'mcp'`。
- **[#34771](https://github.com/BerriAI/litellm/issues/34771)（开放）** — CP OAuth DCR 中继将 LiteLLM 自己的 `/callback` 作为客户端 `redirect_uri` 返回，导致自重定向循环；据报告，先前针对 #33699 的修复在 `litellm-database:v1.95.0-dev` 上并不完整。
- **[#34753](https://github.com/BerriAI/litellm/issues/34753)（开放）** — `metadata.error_information.error_message` 在失败上传时存储完整的 base64 文件载荷，使 `LiteLLM_SpendLogs` 膨胀。
- **[#34763](https://github.com/BerriAI/litellm/issues/34763)（开放）** — MCP 服务器 UI 在下拉菜单中遗漏 `auth_type: "authorization"`，尽管后端支持；目前仅能通过 API 使用。
- **[#32895](https://github.com/BerriAI/litellm/issues/32895)（开放）** — 除 OpenAI 和 Azure 外，所有提供方的 `max_retries` 都会被静默丢弃；不会发生重试，也不会抛出错误。
- **[#48226](https://github.com/BerriAI/litellm/issues/48226)（已关闭）** — 对于 `groq/openai/gpt-oss-*` 响应，`completion_cost()` 会抛出 `ModelNotMappedError`，尽管请求在调用时已正确计价。
- **[#48147](https://github.com/BerriAI/litellm/issues/48147)（已关闭）**、**[#47965](https://github.com/BerriAI/litellm/issues/47965)（已关闭）** — 决策路径中的提供方核算/端点不一致（缺少 usage 时 TypeSafe 零成本；Cloudflare 决策仍使用聊天提供方标记为 legacy 的 `/ai/run` 路径）。

**进行中的成本控制修复**

- **[PR #48355](https://github.com/BerriAI/litellm/pull/48355)（开放）** — 虚拟 key 的软预算告警现在读取当前花费，包括由另一个代理记录的花费，并保持为警告，因此请求会继续。
- **[PR #48354](https://github.com/BerriAI/litellm/pull/48354)（开放）** — 显式 `null` 会清除按模型预算限制而不会产生服务器错误，保留其他限制，并让省略的字段保持不变。
- **[PR #30786](https://github.com/BerriAI/litellm/pull/30786)（开放，长期搁置）** — 在 `/team/member_update` 上接受花费。
- **[PR #34823](https://github.com/BerriAI/litellm/pull/34823)（开放）** — `/key/list` 新增 `user_email` 过滤器，通过用户关系使用 Postgres semi-join 解析。

## 6. 对应用开发者的意义

- **将 1.106.4 作为目标版本，并阅读真实 changelog。** 四条并行补丁线加上被截断的说明意味着你无法从信息源推断安全性。在网关副本之间固定精确版本，并用已发布的 cosign 密钥验证镜像，而不是信任标签。
- **如果你运行在观察夏令时的地区，请将代理容器 TZ 设置为 UTC。** [#48286](https://github.com/BerriAI/litellm/issues/48286) 会把时钟变更变成重启/OOM 循环；目前没有修复，只能规避。
- **在请求到达认证之前验证请求体。** [#48304](https://github.com/BerriAI/litellm/issues/48304) 意味着在未认证端点上发送 `[]`、`null` 或裸字符串会返回 500 而不是 401/422。如果你以编程方式生成 JSON，确保始终发送对象；如果你在 LiteLLM 前面部署 WAF 或 ingress，在 [#48329](https://github.com/BerriAI/litellm/pull/48329) 落地前在那里拒绝非对象 body。
- **还不要将花费日志作为计费的权威记录。** 三个独立缺口会导致少报：非传输层数据库写入失败时批次丢失（[#33873](https://github.com/BerriAI/litellm/issues/33873)）、预算 reseed 未递增实际成本（[#35570](https://github.com/BerriAI/litellm/issues/35570)），以及未定价调用按 $0 记录（[#43164](https://github.com/BerriAI/litellm/pull/43164)）。请与提供方发票对账，并针对 [#34753](https://github.com/BerriAI/litellm/issues/34753) 中的 base64 膨胀过滤或分区 `SpendLogs`。
- **在信任向量数量之前检查 embedding 扇出。** 在 `vertex_ai/` 路由上，列表输入可能只返回一个向量，却按全部 N 个计费（[#38823](https://github.com/BerriAI/litellm/issues/38823)）。在摄取管道中断言 `len(data) == len(input)`，并对账 `usage.prompt_tokens`。
- **暂时避免通过 `vertex_ai/openai/...` 流式调用推理模型。** `reasoning_content` 增量、额外工具调用和 usage 在流式模式下会丢失，但在非流式下正确（[#48238](https://github.com/BerriAI/litellm/issues/48238)）。如果你需要 token 级推理，请在同一后端上使用 `hosted_vllm/`。
- **在 [#48191](https://github.com/BerriAI/litellm/pull/48191) 合并前，JSON 配置提供方上的工具调用不可靠。** 如果你使用 `scaleway`、`sideren` 或其他启用了 `drop_params` 的 `providers.json` 条目，tools 可能被静默剥离，模型会以纯文本作答而不是调用函数。优先选择具有原生 `supports_function_calling` 元数据的提供方。
- **不要依赖 OpenAI/Azure 之外的 `max_retries`。** [#32895](https://github.com/BerriAI/litellm/issues/32895) 显示它对 Anthropic、Bedrock、Vertex 和其他提供方会被静默忽略——请在客户端或网关级策略中实现重试/退避。
- **值得跟踪的小改进：** Ollama 将 `stop` 作为裸字符串目前会导致请求失败（[#48324](https://github.com/BerriAI/litellm/pull/48324)）——始终发送数组；如果你以 `STORE_MODEL_IN_DB=false` 运行，Admin UI 设置是只读的（[#46335](https://github.com/BerriAI/litellm/issues/46335)），因此请通过管理 API 驱动配置。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-10-26

## 1. 今日亮点

过去 24 小时没有新版本发布，但仓库正处于一轮大规模的 Studio 稳定性推进中：共涉及 28 个 issue、更新了 221 个 PR，多个 Windows/Linux `v0.1.910-beta` 模型加载阻塞问题已关闭，另有一批 GGUF 准入、KV 容量计算和多 GPU 调度修复正在推进。推理侧最受关注的开放工作包括 Vulkan 投机解码正确性（[#15258](https://github.com/unslothai/unsloth/pull/15258)）、DSpark sidecar 的 Metal OOM 恢复（[#14431](https://github.com/unslothai/unsloth/pull/14431)），以及跨空闲 GPU 对的张量并行切分（[#14446](https://github.com/unslothai/unsloth/pull/14446)）。

## 2. 发布与破坏性变更

- 过去 24 小时，`unslothai/unsloth` **没有新的 release 标签**。Studio 用户仍停留在 `v0.1.910-beta` / 包 `2026.10.8`。
- **进行中的行为变更 — 运行设置模型：** [#15247](https://github.com/unslothai/unsloth/pull/15247) 将 Run 设置整合为每个模型一个下拉菜单，上次使用的设置将成为加载时采用的设置。如果你针对先前按模型预设的语义编写了脚本，请预期需要迁移。
- **推理保留覆盖（已修复）：** Studio 在启动 `llama-server` 时强制 `preserve_thinking=true`，覆盖用户提供的聊天模板，并使 `--no-reasoning-preserve` 变成空操作。已作为 [#15115](https://github.com/unslothai/unsloth/issues/15115) 关闭——升级后请检查你的启动参数。
- **Agent 集成接口：** [#15155](https://github.com/unslothai/unsloth/pull/15155) 新增 Settings → Agents 的 “Desktop apps” 路径，用于将 Claude Desktop / Codex Desktop 指向 Studio 已加载的模型，包括 MTP 和 keep-alive 处理。

## 3. 新模型与硬件支持

- **FLUX.3 Klein 图像编辑：** [#15263](https://github.com/unslothai/unsloth/pull/15263) 将 `unsloth/flux-3-image-klein-GGUF` 接入 Edit 工作流（此前为灰色不可用）；关闭 [#15241](https://github.com/unslothai/unsloth/issues/15241)。
- **Vulkan + 投机解码：** [#15258](https://github.com/unslothai/unsloth/pull/15258) 当词表使 logits 行错位时，在 CPU 上采样 Vulkan draft，修复使用 `openbmb/MiniCPM-V-4.7-1B-gguf` 时的 `GGML_ASSERT` 崩溃（[#14987](https://github.com/unslothai/unsloth/issues/14987)）。
- **Metal sidecar 尺寸计算：** [#14431](https://github.com/unslothai/unsloth/pull/14431) 在 `--fit-target` 内为 DSpark/DFlash sidecar 预留空间，并增加在 `DeepSeek-V4.1-Flash-1017-GGUF` 上从 `kIOGPUCommandBufferCallbackErrorOutOfMemory` 恢复。
- **AMD 音频栈：** [#13042](https://github.com/unslothai/unsloth/issues/13042) 已关闭——在 AMD GPU 上，Studio 安装的是 `audio.cpp` 的 CPU 构建；请求提供 ROCm/HIP 捆绑包和 Vulkan 回退，以免音频模型在 CPU 上运行（RX 9070 XT / gfx1201 复现）。
- **多 GPU 张量切分：** [#14446](https://github.com/unslothai/unsloth/pull/14446) 当两块匹配的 GPU 空闲时，为稠密模型和 MTP MoE 新增 `--split-mode tensor`。
- **已请求、尚未支持：** Nanbeige4.3-33B 优化（[#15278](https://github.com/unslothai/unsloth/issues/15278)）、原生 hf-mirror.com 下载并支持并行抓取（[#15274](https://github.com/unslothai/unsloth/issues/15274)）。

## 4. 性能与优化

- **模型加载延迟（已关闭）：** [#15297](https://github.com/unslothai/unsloth/pull/15297) 将 MiniMax-H3 block-group pinning 移到后台 pinner——在 A100 VM 上，单线程拷贝占每次惰性流式加载（19.45 GB）约 10.9 秒。
- **解码利用率：** [#14446](https://github.com/unslothai/unsloth/pull/14446) 解决了层切分串行化问题：在双 GPU 主机上，每张卡在解码期间约有半时间空闲。
- **查找表的 VRAM 核算：** [#14465](https://github.com/unslothai/unsloth/pull/14465) 将逐层嵌入和 Engram 查找表按主机/磁盘而非 VRAM 计入（影响 DeepSeek-V4.1 Engram、Qwen3.8-Flash-Next、Gemma 4 PLE、gemma-3n PLE），从而改变哪些量化版本显示为可容纳。
- **卸载规划器（可选启用）：** [#9872](https://github.com/unslothai/unsloth/pull/9872) 将 `UNSLOTH_SMART_OFFLOAD` 保留在标志之后；新增 spill-vs-fitter 成本门控和 sub-FFN spill 阶梯。
- **KV 准入准确性：** [#15294](https://github.com/unslothai/unsloth/pull/15294) 在 OpenAI/Responses/Anthropic 透传路径上，按准备好的提示（工具 + 渲染选项）计数，而不是按字符估算，减少共享 KV 超配。
- **视觉上下文预算：** [#15014](https://github.com/unslothai/unsloth/pull/15014) 在适配和工具结果大小计算中计入图像 token，修复 [#14968](https://github.com/unslothai/unsloth/issues/14968) 中静默少计（十张截图约少计 20k token）的问题。

## 5. 稳定性与回归

按严重程度排序：

1. **Windows 上所有 GGUF 加载被终止（已修复，已关闭）** — [#15022](https://github.com/unslothai/unsloth/issues/15022)：2026-10-23 更新后，Studio 启动了 `llama-server`，`/health` 返回健康的 200，随后却因错误的“我们的子进程没有在监听”端口归属检查将其终止。表现为误导性的“GGUF invalid / not enough memory”错误。
2. **`v0.1.910-beta` 上所有模型加载失败（已修复，已关闭）** — [#15142](https://github.com/unslothai/unsloth/issues/15142)，Windows 11 25H2。
3. **核心转储与静默卡死（开放，无修复 PR）** — [#14411](https://github.com/unslothai/unsloth/issues/14411)：无论 KV 使用情况如何，模型在几轮后停止响应；卸载模型会触发核心转储。报告称这是过去两周出现的回归，没有相关 llama/server 日志。
4. **Linux 桌面崩溃（开放）** — [#15260](https://github.com/unslothai/unsloth/issues/15260)：在 KDE Plasma 上启用 AT-SPI/atk-bridge 时，`WebKitWebProcess` 触发 SIGABRT（AppImage；在 NVIDIA CUDA 和其他后端上均可复现）。
5. **CPU 嵌入卡顿（已关闭）** — [#14519](https://github.com/unslothai/unsloth/issues/14519)：在工具密集的长对话中，llama.cpp 看到请求之前，CPU 对话归档嵌入会耗时数分钟，而且 Stop 不会取消归档。
6. **Vulkan 投机解码崩溃（开放，修复 PR #15258）** — [#14987](https://github.com/unslothai/unsloth/issues/14987)，MiniCPM-V-4.7-1B 打印一个 token 后即崩溃。
7. **自动压缩忽略图像 token（开放，修复 PR #15014）** — [#14968](https://github.com/unslothai/unsloth/issues/14968)：带截图的工具循环会溢出上下文窗口。
8. **聊天模板正确性（开放）** — [#15285](https://github.com/unslothai/unsloth/issues/15285)：`DeepSeek-V4.1-Flash-1017-GGUF` 模板会拒绝关于图像标记的普通讨论，破坏对话历史；该 issue 中已提供补丁。
9. **UI/性能小问题（开放）** — ROCm 下图像编辑说明框输入缓慢（[#15221](https://github.com/unslothai/unsloth/issues/15221)）、编辑画廊中的上下文菜单项失效（[#15272](https://github.com/unslothai/unsloth/issues/15272)）、通过 API 加载时 STT 的 “Load into” 被忽略（[#15280](https://github.com/unslothai/unsloth/issues/15280)）。

## 6. 这对应用开发者意味着什么

- **固定你的 Studio 桌面构建并重新测试模型加载。** `v0.1.910-beta` 的 Windows 加载失败已关闭，但问题严重，且表现为面向用户的内存/文件错误——不要让你的事故排查把它们误判为 OOM。
- **审查 Vulkan 上的投机解码。** 如果你在 Auto（会选择 DSpark sidecar）上以投机解码服务词表未对齐的模型，那么在 [#15258](https://github.com/unslothai/unsloth/pull/15258) 落地前，你就在 [#14987](https://github.com/unslothai/unsloth/issues/14987) 的崩溃路径上；可将禁用投机解码作为规避方案。
- **为多模态 Agent 留出显式上下文余量。** 在 [#15014](https://github.com/unslothai/unsloth/pull/15014) 合并前，本地 GGUF 上的自动压缩会少计图像 token；对于截图密集的工具循环，应保守地做预算，而不是信任报告的 token 数。
- **重新检查你的量化推荐。** [#14465](https://github.com/unslothai/unsloth/pull/14465) 和开放中的卸载规划器（[#9872](https://github.com/unslothai/unsloth/pull/9872)）改变了哪些模型被报告为可容纳于 VRAM——容量徽章和量化选择器将会变化。
- **预期工具 schema 的 KV 准入会更严格。** [#15294](https://github.com/unslothai/unsloth/pull/15294) 用渲染后提示计数替代字符启发式；带有大型工具 schema 的请求可能会比以前更不激进地被准入（但这是正确的）。
- **多 GPU 主机获得一个吞吐量选项。** 对于可放入单卡的模型，张量切分（[#14446](https://github.com/unslothai/unsloth/pull/14446)）在解码上可能优于默认的层切分——值得对你的 Agent 工作负载进行基准测试。
- **外部 Agent 客户端正成为一等公民。** [#15155](https://github.com/unslothai/unsloth/pull/15155) 和按模型运行设置预设（[#15247](https://github.com/unslothai/unsloth/pull/15247)）减少了将 Claude Desktop / Codex Desktop 指向本地服务的 Studio 模型所需的配置胶水。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*