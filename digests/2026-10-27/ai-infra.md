# AI 基础设施日报 2026-10-27

> 生成时间: 2026-10-27 01:36 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目对比报告——AI 基础设施 Digest，2026-10-27

## 1. 生态系统概览

生态系统正处于**正确性加固阶段**，而非单纯的吞吐竞赛。今天最严重的问题不是基准回归，而是**数据隔离与静默正确性故障**：vLLM 在 PD 分离 + 前缀缓存下的跨请求 KV 污染、SGLang 对 Kimi-K3 MXFP4 激活的静默替换，以及 LiteLLM 的支出计数器重复计数导致 422 锁定。与此同时，**KV 缓存生命周期与冷启动成本**正成为主要性能杠杆：SGLang 的 Weight Cache Daemon 将 Qwen3-235B FP8 权重加载从约 306–327s 降至 **<1s**，而 vLLM、llama.cpp 和 Ollama 都报告了与缓存相关的回归。硬件碎片化在 Blackwell/SM120、ROCm、SYCL、Apple MLX、Ascend NPU 和 Hexagon 之间仍然严重。Agentic 工作负载现在是明确的设计目标：工具调用可靠性、上下文压缩、MCP GA、可编程 KV 会话和本地定时任务都出现在今天的活动中。

## 2. 活动对比

Digest 可见的唯一 issue/PR 标识符，而非完整跟踪器总量。Issue 计数包括 open、closed 和 referenced 的 issue。

| 项目 | 层级 | 引用的 issue 数 | 引用的 PR 数 | 发布状态（24h） |
|---|---:|---:|---:|---|
| **vLLM** | 服务端推理引擎 | 25 | 14 | 无发布；堆叠式 PP 传输重构进行中 |
| **SGLang** | 服务端推理引擎 | 22 | 20 | 无发布；Weight Cache Daemon Phase 1 已落地 |
| **llama.cpp** | 本地/边缘运行时 | 21 | 24 | 10 个构建 `b11901`–`b11913` |
| **Ollama** | 本地运行时 | 21 | 13 | `v0.40.12-rc0` |
| **LiteLLM** | LLM 网关 | 13 | 18 | 无发布；MCP/Anthropic handler GA 推进中 |
| **Unsloth** | 微调 / Studio | 20 | 18 | `v0.1.911-beta` |

**值得注意的活动：** vLLM 今天有最高严重级别的问题——PD 分离式服务中的租户隔离故障。SGLang 有最强的冷启动结果，但也有严重的推测解码崩溃。llama.cpp 是构建最活跃的项目，有 10 个构建和广泛的后端工作。LiteLLM 正在将实验性界面重构为 GA。Unsloth 正从训练扩展到定时任务和生成式媒体。

## 3. 模型支持竞赛

| 项目 | 新增模型 / 架构 / 硬件支持 | 定位 |
|---|---|---|
| **vLLM** | Decision 3.0 `d3-turbo` 27B prefix_bidir 注意力；Qwen3.8-Flash-Next 在 Model Runner V2 上的原生 x86 CPU 支持；ROCm/Qwen4Exp fp8 KV + indexer 缓存；SM120 DSv4.1 稀疏 MLA block size 修复；ASR VAD；ViT CUDA graph 跟踪；MoE 路由专家捕获布局。路线图：GLM-5.3、Kimi K3。 | **生产服务模型覆盖最广。** 在 NVIDIA/AMD/x86 和新的注意力变体上很强。 |
| **SGLang** | DeepSeek V4.1 mHC 清理；Ascend 950 上用于 DSV4 的 NPU DSpark；用于 MLA/DSV4 的 NPU HiCache 层优先传输；gfx950 上 AMD Qwen3.8 hyper-connections；与 MiniMax-H3 的 Diffusion Q4 路线图。Apple Silicon 仍仅处于路线图阶段；DGX Spark/SM121 和 GGUF I-quant 缺口以 inactive 关闭。 | **DeepSeek + NPU/AMD 深度最深。** 尤其在 Ascend 和分离式服务方面很强。 |
| **llama.cpp** | Laguna MoE amax 重缩放；GigaChat 3.5 432B-A28B MoE 进行中；DeepSeek-V4.1-Flash 进行中/预览；CUDA 上 vision-tower head size 40/72；Metal 多 GPU；Hexagon VTCM 和 `MUL_MAT_ID`；AMD XDNA 请求处于 open。 | **本地/边缘硬件覆盖最广。** 最适合异构客户端和工作站后端。 |
| **Ollama** | DFlash2 MLX draft 支持；用于 Qwen3/Qwen3.5 的 YaRN RoPE；Gemma 4 紧凑视觉塔；MLX/MLX-C prompt 速度提升。DeepSeek-V4.1-Flash 可下载文件仍是一个请求。 | **Apple Silicon 用户体验领先。** 模型覆盖面窄于 vLLM/SGLang/llama.cpp。 |
| **LiteLLM** | DashScope 实时 WebSocket handler；Vertex 图像尺寸/宽高比映射；`azure_ai/gpt-image-2` 定价元数据；`/model/info` 中的 `context_window`；已知缺口：Scaleway embeddings 和带自定义 `api_base` 的 Gemini 显式缓存。 | **提供商抽象层，而非模型运行时。** 正在扩展多模态/实时提供商覆盖。 |
| **Unsloth** | MiniMax-M3.1-Flash；FLUX-3 图像编辑；Qwen-Image-2.1 确定性；Wan2.1/Wan2.2/HunyuanVideo-1.5/MiniMax-H3 视频路径；SDXL + ControlNet；gpt-oss 专家布局探测；Blackwell fp32 注意力重路由。 | **微调与生成式媒体领先。** 对训练、LoRA、扩散和视频工作流覆盖最佳。 |

**谁领先？** 按层来看：**vLLM** 在生产模型服务广度上领先；**SGLang** 在 DeepSeek/NPU/AMD 深度和冷启动恢复上领先；**llama.cpp** 在本地/边缘后端广度上领先；**Ollama** 在 Apple Silicon 打包和用户体验上领先；**LiteLLM** 在网关/提供商归一化上领先；**Unsloth** 在微调和生成式媒体工具上领先。

## 4. 性能前沿

优化工作集中在五个方面：

1. **KV 缓存与内存生命周期**——主导前沿。
   - vLLM：严重的前缀缓存污染；Programmable KV Cache RFC；KV 压缩层 RFC。
   - SGLang：Weight Cache Daemon 将 Qwen3-235B FP8 加载从约 306–327s 降至 **<1s**；NPU 稀疏卸载在 HBM 不变的情况下将池容量从 **26,880 → 99,840 tokens** 提升。
   - llama.cpp：统一 KV + `-np 2` 从第二个长请求开始导致 **42–54% 的 prompt 处理下降**。
   - Ollama：AMD RADV + Vulkan prompt 缓存在第二个相同请求上出现 **约 29.7× 的生成吞吐崩塌**。
   - LiteLLM：vLLM `cached_tokens` 未被计费，导致支出被高估。

2. **后端特定内核。**
   - vLLM：DiffKV prefill 分块在 SM80 上对 **200K 上下文分块带来 3.0×**；融合 conv1d；SM120 稀疏 MLA block size 修复。
   - SGLang：QSA prefill 移除 D2D 拷贝；通过 AITER FlyDSL 实现 AMD QSA decode 融合；融合 NaN 校验；针对 FP32 残差的融合 RMSNorm。
   - llama.cpp：Vulkan RMSNorm 子组归约；SYCL 有界 reorder scratch 和模板化 MMVQ；Hexagon 向量化清零；CPU `concat` 单行内核。
   - Unsloth：ROCm 注意力跟踪修复；Blackwell fp32 注意力重路由；确定性扩散/视频解码。

3. **量化正确性现已与量化速度同等重要。**
   - vLLM：当 per-rank K 未按 32 对齐时，`torch.compile` 下 NVFP4 输出乱码。
   - SGLang：SM90 上的 Kimi-K3 MXFP4 静默计算错误的激活；NVFP4 MoE + EP>1 bug 以 inactive 关闭，但未修复。
   - llama.cpp：`IQ4_NL`/`IQ4_XS` 创建在输出位级一致的前提下获得加速。
   - Unsloth：int8 W8A8 编译内存回归已修复；MiniMax-M3.1 NVFP4 在 Apple Silicon 上回退到 CPU。

4. **分布式服务与并行。**
   - vLLM：流水线并行传输重构；PD 分离隔离问题。
   - SGLang：EP>1 每次启动都会丢弃 FlashInfer 自动调优缓存；NPU NCCL EP 解码；Ascend 上的 P/D 分离路线图。
   - llama.cpp：Qwen3.8-Flash-Next 上 CUDA tensor-split 中止；Metal 多 GPU PR。
   - Unsloth：FSDP2 `reshard_after_forward` 和 fp16 grad-scaler 修复。

5. **批处理/采样与调度。**
   - vLLM：top-k/topp Triton 采样在 6–10% 的行上回退到全词表搜索；GDN 混合回归在 c32 下损失 **约 24% 聚合吞吐**。
   - SGLang：EP>1 下 FlashInfer 自动调优缓存失效。
   - llama.cpp：统一 KV 下的缓存 prompt 恢复重试。

## 5. 层级定位

| 层级 | 项目 | 角色 |
|---|---|---|
| **服务端推理引擎** | **vLLM、SGLang** | 高吞吐、多 GPU 模型服务。两者都面向 PD 分离、前缀缓存、分布式并行和 Agentic 服务。vLLM 在模型/后端上更广；SGLang 在 DeepSeek、NPU、AMD 和冷启动恢复上更深。 |
| **本地/边缘运行时** | **llama.cpp、Ollama** | 跨 CPU、GPU、Apple Silicon、Vulkan、SYCL、Hexagon 的单节点和桌面推理。llama.cpp 是后端广度层；Ollama 是打包用户体验层，并内嵌 llama.cpp。 |
| **LLM 网关 / 控制平面** | **LiteLLM** | 路由、提供商归一化、预算、限流、认证、MCP、模型元数据。它位于服务引擎和外部 API 之前，而不是执行模型内核。 |
| **训练 / 微调 / Studio** | **Unsloth** | LoRA/微调、FSDP2、量化感知训练、扩散/视频生成、桌面 Studio 工作流。它可以在微调后暴露 OpenAI 兼容服务，但其重心是训练和生成式工具。 |

**重叠：** LiteLLM 可以作为 vLLM/SGLang/Ollama/Unsloth 端点的前端。Ollama 和 llama.cpp 共享运行时血统。vLLM 和 SGLang 在生产服务中直接竞争，但 SGLang 的 NPU/DeepSeek 专业化和 vLLM 的模型广度形成了不同的采用画像。

## 6. 趋势信号

1. **Agentic 服务是设计中心。**
   - 工具调用可靠性现已成为一等基础设施关注点：vLLM 的 Rust 前端在 `auto` 解析器解析时返回 500，Ollama 静默丢弃无法识别的工具调用，SGLang 修复 `tool_choice="auto"`，LiteLLM 将 MCP 推向 GA。
   - 上下文压缩和长时间运行的 Agent 很脆弱：Ollama 在云模型上存在自动压缩失败；Unsloth 的 Anthropic `/v1/messages` 溢出不会触发压缩。
   - **关注：** 工具调用解析器覆盖、空内容 `200` 响应、MCP 路径迁移和上下文溢出行为。

2. **正确性和租户隔离优先于原始吞吐。**
   - vLLM 的 PD + 前缀缓存污染是数据隔离故障，而非性能假象。
   - SGLang 的静默激活替换会产生流畅但错误的输出——这是最糟糕的 Agent 失效模式。
   - LiteLLM 的支出重复计数会用 422 将预算内客户锁在门外。
   - **关注：** 固定版本、对非贪心/推测路径做健康检查，并验证输出，而不是信任 HTTP 200。

3. **KV 缓存正在成为可编程控制平面。**
   - vLLM 的 Programmable KV Cache RFC 和 KV 压缩 RFC。
   - SGLang 的 Weight Cache Daemon 和 NPU HiCache。
   - llama.cpp 的统一 KV 回归和缓存 prompt 恢复重试。
   - **关注：** 会话生命周期、缓存保留/迁移、前缀缓存隔离，以及重启后的预热行为。

4. **硬件碎片化并未缓解。**
   - Blackwell SM120 在 vLLM 中仍有按模型处理的 workaround，在 SGLang 中仍表现不佳。
   - ROCm 存在注意力正确性问题；SYCL 存在 TDR 重置和 token 乱码 bug；Apple MLX 正在改进，但有 NVFP4 CPU 回退；Ascend NPU 在推进，但仍以路线图为主。
   - **关注：** 各后端的资格认证矩阵；不要假设“GPU 支持”就意味着“所有内核正确”。

5. **冷启动和自动扩缩容经济性正在改善——但有选择性。**
   - SGLang 的 <1s 权重加载是自动扩缩容/蓝绿部署的重要使能因素。
   - LiteLLM 改进了数据库生命周期和流式超时语义。
   - Unsloth 增加了本地/API 定时 prompt 执行。
   - **关注：** 重启成本、权重缓存覆盖范围，以及 KV/HiCache 预热是包含在内还是单独进行。

6. **推测解码和量化 KV 仍然脆弱。**
   - SGLang：推测解码会在第一个非贪心请求上硬性杀死仅 pip 安装的部署。
   - vLLM：结构化输出 + 推测解码尚未达到分布安全。
   - llama.cpp：SYCL draft 模型会导致 TDR 重置。
   - **关注：** 将推测解码置于非贪心健康检查之后；避免在 Agent 生产路径中盲目启用。

**给技术决策者的结论：** 按模型/硬件匹配度和正确性记录选择服务引擎，而不是只看头条吞吐。对于多租户或 Agentic 生产环境，优先处理 vLLM/SGLang 的缓存隔离修复，按模型验证 SGLang/Unsloth 的量化路径，并将 LiteLLM 的 MCP/GA 和预算修复视为网关控制平面升级。本地运行时部署应固定 llama.cpp/Ollama 构建，并在重复长请求下测试多槽位和 prompt 缓存行为。

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 文摘 — 2026-10-27

## 今日亮点

过去 24 小时内没有新版本发布，但代码树非常活跃：2026 年 Q4 SIG 路线图已开放征求意见（[#61024](https://github.com/vllm-project/vllm/issues/61024)），并且一项流水线并行传输重构正以堆叠 PR 系列的形式合入（[#63897](https://github.com/vllm-project/vllm/pull/63897)、[#63898](https://github.com/vllm-project/vllm/pull/63898)、[#63899](https://github.com/vllm-project/vllm/pull/63899)）。影响最大的事项是一份关于在启用自动前缀缓存的 PD 分离部署中出现**跨请求 KV 缓存污染**的报告（[#61160](https://github.com/vllm-project/vllm/issues/61160)）——这是正确性/数据隔离问题，而非单纯的性能缺陷。

## 版本发布与破坏性变更

过去 24 小时内未发布任何版本。未宣布任何配置/API 弃用。请注意，PP 传输相关工作引入了新标志（`--pp-group-cls`、按实例的流水线阶段），这些标志仍处于 OPEN 状态，可能会发生变化。

## 新模型与硬件支持

- **Decision 3.0（`vllm-sr/d3-turbo`，27B）prefix_bidir attention** — 该模型支持 PR 使得无需额外标志即可运行 `vllm serve vllm-sr/d3-turbo`（[#62584](https://github.com/vllm-project/vllm/pull/62584)）。
- **Qwen3.8-Flash-Next 原生 x86 CPU 后端**：在 Model Runner V2 上，覆盖 Qwen4Exp、HyperConnection、QSA、PLE 和 N-gram 嵌入路径（[#57294](https://github.com/vllm-project/vllm/pull/57294)）。
- **ROCm/Qwen4Exp fp8 KV + indexer 缓存**现已在 AMD QSA 路径启用，与 NVIDIA 保持一致（[#61529](https://github.com/vllm-project/vllm/pull/61529)）。
- **SM120（RTX PRO 6000 Blackwell）DSv4.1 sparse-MLA** 现在声明 64 token 内核块，修复了使用 `--block-size 64` 时的块大小协商失败（[#60762](https://github.com/vllm-project/vllm/pull/60762)）。
- **ASR：集成语音活动检测**，以减少 Whisper 风格的长噪声段幻觉（[#46126](https://github.com/vllm-project/vllm/pull/46126)）。
- **ViT Full CUDA Graph** 多模态编码器跟踪项（Qwen3-VL、GLM-V、Kimi K2.5）仍是讨论热度最高的问题，已有 45 条评论（[#38175](https://github.com/vllm-project/vllm/issues/38175)）。
- **MoE routed-expert 捕获布局** — 后端现在可以描述物理路由行，而无需替换共享捕获器（[#62441](https://github.com/vllm-project/vllm/pull/62441)）。

## 性能与优化

- **SM80 上的 DiffKV 预填充分块**：`BLOCK_M=128` + KV tile 64 在 200K 上下文分块上带来 **3.0× 提升**，适用于不具备 FA3/FA4 的 GPU（[#61095](https://github.com/vllm-project/vllm/issues/61095)）。
- **GLM-5.3 KDA 预填充**：将预计算的 `chunk_indices` 传入 `chunk_kda`，移除了 `prepare_chunk_indices` 中每次调用都会产生的 GPU→CPU 同步（[#63072](https://github.com/vllm-project/vllm/pull/63072)）。配套修复将 q|k|v conv1d 融合为一个参数，以阻止 RL 重拟合后出现陈旧的卷积读取（[#60991](https://github.com/vllm-project/vllm/pull/60991)）。
- **Top-k/topp Triton 采样**：单体内核在 6–10% 的行上回退到全词表搜索，因为其 gather 枢轴仅由前 8192 个 logits 推导而来（[#61998](https://github.com/vllm-project/vllm/issues/61998)）。一个相关的直方图 bin 丢弃 bug 已关闭（[#51782](https://github.com/vllm-project/vllm/issues/51782)）。
- **GDN 混合回归**：Qwen3.8-27B（INT4、fp8 KV、TP2）**在 c32 下损失约 24% 的聚合吞吐量**，当剖析得到的 KV 池降至约 190K tokens/≤116 个块以下时；c8 和单流不受影响（[#55580](https://github.com/vllm-project/vllm/issues/55580)）。
- **Rust 前端 gRPC**：对 `output_text=false` 的请求完全跳过去分词化，与 Python 的 `detokenize=False` 语义一致（[#63789](https://github.com/vllm-project/vllm/pull/63789)）。
- **路线图进行中事项**：GLM-5.3（[#57406](https://github.com/vllm-project/vllm/issues/57406)）、Kimi K3（[#50587](https://github.com/vllm-project/vllm/issues/50587)）、MoE “oracle” 统一（[#37753](https://github.com/vllm-project/vllm/issues/37753)），以及 KV 缓存布局标准化（[#42082](https://github.com/vllm-project/vllm/issues/42082)）。

## 稳定性与回归

按严重程度排序：

1. **跨请求上下文污染（严重，数据隔离）** — 在 DeepSeek-V4.1-Flash 上，PD 分离 + 自动前缀缓存（默认）会间歇性返回*其他请求*的内容。使用 `--no-enable-prefix-caching` 确认无污染；已有可脚本化复现器。尚无直接修复 PR；相关缓解措施包括连接器无关的 KV 校验和（[#62211](https://github.com/vllm-project/vllm/pull/62211)）与 KV 丢失报告（[#63899](https://github.com/vllm-project/vllm/pull/63899)）（[#61160](https://github.com/vllm-project/vllm/issues/61160)）。
2. **SM120 CUDA graphs 无法用于 DSv4.1-Flash** — 在 RTX PRO 6000 上 8×TP 会强制使用 `--enforce-eager`，导致解码吞吐量极低；已请求优先修复 graph capture（[#56892](https://github.com/vllm-project/vllm/issues/56892)）。1M 上下文的现场报告配置已可用（[#56700](https://github.com/vllm-project/vllm/issues/56700)）。
3. **在 `torch.compile` 下 NVFP4 输出乱码**，当 per-rank K 未按 32 对齐时（TP8/SM120）——填充后的 block-scale 列会读取过期内存；eager/Marlin/aligned-TP 均正确（[#48898](https://github.com/vllm-project/vllm/issues/48898)）。
4. **logprob 构造中的 `IndexError`** 会在 `top_logprobs` 短于 `token_ids` 时，使 `/v1/chat/completions` 和 `/v1/completions` 返回 500（[#63751](https://github.com/vllm-project/vllm/issues/63751)）。
5. **Rust 前端工具调用**：当 `auto` 工具解析器无法为模型解析出解析器时，每个请求都会返回 500（[#63584](https://github.com/vllm-project/vllm/issues/63584)）。
6. **ROCm `ROCM_ATTN`** 在 RDNA 上，当 head size ≥96 且 KV block size ≠16，以及 MHA head 为 192/256 时，会返回错误的注意力结果并可能导致 GPU 故障（[#62697](https://github.com/vllm-project/vllm/issues/62697)）。
7. **结构化输出**：格式错误的 spec 会返回 HTTP 500 且错误文本为空；自引用 `$ref` 会通过校验，然后在生成过程中终止请求（[#57725](https://github.com/vllm-project/vllm/issues/57725)）。
8. **P2P KV 卸载层级在对端故障时不会快速失败**（[#63602](https://github.com/vllm-project/vllm/issues/63602)）；当空闲内存*增长*时，启动时内存剖析断言会中止，破坏了文档所述的 GPU 共租（[#56830](https://github.com/vllm-project/vllm/issues/56830)）。
9. **已解决/已关闭**：`persistent_topk` 候选丢弃（[#51782](https://github.com/vllm-project/vllm/issues/51782)）、ROCm gfx1030 性能回归（[#38107](https://github.com/vllm-project/vllm/issues/38107)）、FlashAttnMLA + fp8 KV 融合测试失败（[#46581](https://github.com/vllm-project/vllm/issues/46581)）。

## 对应用开发者的意义

- **如果你运行 PD 分离式服务，请立即禁用前缀缓存。** 串扰报告（[#61160](https://github.com/vllm-project/vllm/issues/61160)）属于租户隔离失效，而不只是性能问题——对于多租户 agent 或使用共享系统提示的 RAG 服务而言不可接受。在修复落地前，添加 `--no-enable-prefix-caching`，或保持 P/D 同置部署。
- **SM120 工作站部署仍处于二等公民地位。** DeepSeek-V4.1-Flash、NVFP4 和 sparse-MLA 路径都需要针对每个模型的变通方案（eager 模式、TP 对齐、显式块大小）。请为启动调试预留时间，并关注现场报告（[#56700](https://github.com/vllm-project/vllm/issues/56700)）。
- **Rust 前端尚未为工具调用工作负载做好生产准备**——`auto` 解析器解析缺口会让每个请求返回 500（[#63584](https://github.com/vllm-project/vllm/issues/63584)）。engine-client 抽象（[#63887](https://github.com/vllm-project/vllm/pull/63887)）体现了投入，但面向 agent 时仍应使用 Python OpenAI 服务器。
- **Agentic 服务是本周期明确的设计目标。** Programmable KV Cache RFC（[#57103](https://github.com/vllm-project/vllm/issues/57103)）提出由路由器提供会话生命周期 + 可组合的保留/迁移策略；可复用 KV 压缩层 RFC（[#59538](https://github.com/vllm-project/vllm/issues/59538)）则针对传输/存储成本。预计下一季度 KV 连接器配置面会发生实质性变化；避免硬编码连接器内部实现。
- **结构化输出 + 推测解码尚不具备分布安全性**——语法位掩码和草稿验证的交互方式可能改变输出分布（[#62818](https://github.com/vllm-project/vllm/issues/62818)）。如果你依赖 JSON schema 进行工具调用，优先使用非推测解码，或对输出进行防御性校验。
- **如果你采用 DiffKV 分块工作（[#61095](https://github.com/vllm-project/vllm/issues/61095)），Ampere 上的长上下文预填充现在显著更便宜**——在 200K 分块上带来 3× 收益，足以改变文档/agent 上下文工作负载的容量规划。

---

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-10-27

## 1. 今日亮点

**Fast Engine Recovery** 路线图正在展示真实的生产数据：Weight Cache Daemon 的 Phase 1 已在 #27139 合入，通过每 rank 的 CUDA IPC daemon，将 Qwen3-235B FP8 的权重加载时间从 ~306–327s 降至 1s 以内。与此同时，issue 跟踪器进行了一轮大规模清理——过去 24 小时关闭了 16 个陈旧 issue（大多标记为 `inactive`），其中包括若干 NVFP4、Kimi-K3 和 ROCm bug。风险方面，首个非贪心请求触发的投机解码崩溃（#43412）以及 Kimi-K3 MXFP4 解码中静默的激活函数替换（#45755），是今天最值得工程团队关注的两项。

---

## 2. 版本发布与破坏性变更

**过去 24 小时无版本发布。**

没有 API 或配置破坏性变更合入。正在进行的、会影响构建的变更：[PR #46519](https://github.com/sgl-project/sglang/pull/46519) 将 `sglang-kernel-xpu` 改为通过 `uv` 进行版本固定解析——仅与 XPU 构建者相关，但会改变 XPU kernel wheel 的选择方式。

---

## 3. 新模型与硬件支持

- **Apple Silicon（路线图，Q2）：** [Issue #19137](https://github.com/sgl-project/sglang/issues/19137) — SGLang 仍不支持 Apple Silicon；维护者正在征集贡献者进行初始移植。30 条评论，尚无代码。
- **NPU（2026 H2 路线图）：** [Issue #25598](https://github.com/sgl-project/sglang/issues/25598) — 目标是在 Ascend 上全面兼容 P/D 分离、所有并行模式、投机解码、分层缓存和负载均衡。
- **DeepSeek V4.1：** [Issue #42170](https://github.com/sgl-project/sglang/issues/42170) — mHC 清理已完成；`q_rope_store` 已并入 `fused_q_norm_rope`（#41657）；剩余工作是 prefill 侧 mHC SP + engram 融合（[#43065](https://github.com/sgl-project/sglang/pull/43065)）。
- **Ascend 950 上的 NPU DSpark（用于 DSV4）：** [PR #39947](https://github.com/sgl-project/sglang/pull/39947) 在 Ascend 950 后端增加投机解码支持。
- **NPU HiCache layer-first 主机传输：** [PR #46604](https://github.com/sgl-project/sglang/pull/46604) 解决 MLA / DeepSeek V4 的 `layer_first_kv_split` 布局，并使用 Memfabric 作为后端的主机 all-to-all。
- **AMD Qwen3.8 hyper-connections：** [PR #41880](https://github.com/sgl-project/sglang/pull/41880) — 针对 gfx950 的 AITER FlyDSL `hc_mix`（依赖 ROCm/aiter#5993，以可导入性为前置条件）。
- **扩散模型 Q4 路线图：** [Issue #43313](https://github.com/sgl-project/sglang/issues/43313) — 以 MiniMax-H3 作为压力测试，并包含已验证的 GB200/GB300 配置方案，以及覆盖 Blackwell 的有损“高”质量档位。
- **硬件缺口——DGX Spark / SM121：** [Issue #36558](https://github.com/sgl-project/sglang/issues/36558) 已以 `inactive` 关闭；QSA 解码在 GB10 上仍无 kernel 路径，因为 `_resolve_trtllm_sparse_decode()` 通过 `is_sm100_supported()` 门控拒绝了 SM121。
- **硬件缺口——GGUF I-quant：** [Issue #35019](https://github.com/sgl-project/sglang/issues/35019) 已以 `inactive` 关闭；`IQ1_*`–`IQ4_*` 没有 MMQ kernel，因此这些检查点仍走慢路径。

---

## 4. 性能与优化

- **权重缓存 daemon——最亮眼的数据：** [Issue #33522](https://github.com/sgl-project/sglang/issues/33522)。量化后权重由每 rank 的 daemon 持有，并通过 CUDA IPC 提供；Qwen3-235B FP8 的权重加载从 **306–327s → <1s**。Phase 1 已在 #27139 合入；路线图现在覆盖 daemon 生命周期与恢复语义。这是跟踪器中影响冷启动/重启时间的最大单一杠杆。
- **EP>1 时每次启动都会丢弃 FlashInfer autotune 缓存：** [Issue #40320](https://github.com/sgl-project/sglang/issues/40320)。`_drop_diverged_autotune_cache()` 在每 rank 的 MoE GEMM 形状不一致时丢弃整个缓存——也就是说，在专家并行下总是如此。融合 MoE 策略每次启动都会重新调优。尚无修复 PR；明显方向是按 rank 对缓存做命名空间隔离，而不是全局失效。
- **DeepSeek-V4 在 SM120 上的 prefill 性能不足：** [Issue #33422](https://github.com/sgl-project/sglang/issues/33422) 报告称在 4× RTX PRO 6000（TP=4）上为 **~2–7K tok/s，而 vLLM/marlin 约为 12.5K**。仍未解决，无根因；更可能是 SM120 上的 kernel 覆盖问题，而非配置问题。
- **QSA prefill D2D 拷贝移除：** [PR #45753](https://github.com/sgl-project/sglang/pull/45753) 在量化稀疏注意力的 prefill KV gather 中消除一次设备到设备拷贝。
- **AMD QSA 解码融合：** [PR #45196](https://github.com/sgl-project/sglang/pull/45196) 在 gfx950 上用 AITER 的融合 FlyDSL 稀疏解码替换两遍 Triton gather + 按（行、KV head）解码（每行最多 2051 个选中位置）。
- **融合 NaN 校验：** [PR #43720](https://github.com/sgl-project/sglang/pull/43720) 将独立的 `isnan` / reduction / negation / device-assert 启动合并为一个只读 Triton kernel——降低 logits 和概率检查的开销，包括非连续 vocab 切片。
- **用于 FP32 残差的融合 RMSNorm：** [PR #46622](https://github.com/sgl-project/sglang/pull/46622)（Muse Spark）将有效增益提升为 FP32 以命中 PyTorch 的融合路径。
- **NPU NCCL EP 解码：** [PR #39546](https://github.com/sgl-project/sglang/pull/39546) 已关闭——用于 TBO 解码的单计算流 + 两个独立通信流。
- **NPU KV/indexer 预算：** [PR #42076](https://github.com/sgl-project/sglang/pull/42076) 已关闭——按后端实际字节速率确定池大小（pre-A5 为 1,024B BF16 行，A5 上为 640B 对齐 FP8），而不是采用固定的 584B/132B 假设。
- **NPU 稀疏 offload 预算：** [PR #45302](https://github.com/sgl-project/sglang/pull/45302) — 在 512 MiB 索引预算下，池容量在 HBM 成本不变的情况下从 **26,880 → 99,840 tokens**。

---

## 5. 稳定性与回归

按严重程度排序：

1. **[严重] 投机解码在首个采样请求时杀死服务器。** [Issue #43412](https://github.com/sgl-project/sglang/issues/43412) — 在没有系统 CUDA 工具包的 pip 安装包中，FlashInfer verify-sampling 算子 JIT 失败（捆绑 cccl 与 pip cu13 工具包不匹配），并向整个进程树发送 SIGKILL。贪心/temperature=0 流量正常；任何 `temperature > 0` 都会崩溃。无修复 PR。规避方法：禁用投机解码，或安装匹配的系统 CUDA 工具包。
2. **[严重，静默] SM90 上的 Kimi-K3 计算了错误的激活。** [Issue #45755](https://github.com/sgl-project/sglang/issues/45755) — `flashinfer_mxfp4` 静默地计算 GPT-OSS SwiGLU 而非 SiTU。已在 v0.5.22 和 `main@ce66b3ab` 上确认。受阻于 flashinfer-ai/flashinfer#4209（仍开放）；此前尝试（#32650）已关闭且未合并。**请将 SM90 上的 Kimi-K3 MXFP4 输出视为未验证。**
3. **[高] Prefill 可断 CUDA graph 产生错误的贪心输出 / IMA。** [Issue #37606](https://github.com/sgl-project/sglang/issues/37606) — `breakable_cuda_graph.py` 仅以弱引用保留 graph-break 输入；共享 mempool 固定了池但未固定块，因此一旦 PyTorch 释放块，图就会读取陈旧内存。#37448 的验证是修复路径。
4. **[高] 非因果滑动窗口注意力被忽略。** [Issue #39300](https://github.com/sgl-project/sglang/issues/39300) — `TorchNativeAttnBackend` 丢弃了非因果 SWA 路径。影响原生后端上 bidirectional/SWA-hybrid 模型的正确性。
5. **[中] 在 FA3/FA4 上、`sample_from_anchor=False` 时 DSpark draft query 宽度错误。** [Issue #46476](https://github.com/sgl-project/sglang/issues/46476) — 新问题，无修复 PR。
6. **[中] DeepSeek V4 / V4.1 请求渲染返回 400。** [PR #46706](https://github.com/sgl-project/sglang/pull/46706) 修复了空消息内容、末尾工具调用轮次，以及针对 `deepseek-recipe` 的数值字符串 `effort` 值（#45315 引入的回归）。修复 PR 已开放。
7. **[中] 扩散模型量化：11 个正确性 bug。** [PR #46319](https://github.com/sgl-project/sglang/pull/46319) 修复了 `multimodal_gen/runtime/layers/quantization/` 中 MXFP4 dual-scale、FP8、KitchenW4A4 和 Nunchaku 路径上的问题。
8. **[低 / 已以 `inactive` 关闭] 陈旧 issue 清扫。** 24 小时内关闭了 16 个 issue， notably：Qwen3.8 DSpark 强制拒绝并非无损——GDN 状态累积漂移（[#35150](https://github.com/sgl-project/sglang/issues/35150)）；NVFP4 MoE + EP>1 输入 scale 形状不匹配（[#36597](https://github.com/sgl-project/sglang/issues/36597)）；`deepseek_nextn` 为 `modelopt_fp4` 硬编码 `quant_config=None`（[#36599](https://github.com/sgl-project/sglang/issues/36599)）；`is_layer_excluded` 缺少融合名称/带 `model.` 前缀的名称（[#36596](https://github.com/sgl-project/sglang/issues/36596)）；Qwen3.5 MTP 权重在 compressed-tensors 检查点上被静默丢弃（[#35797](https://github.com/sgl-project/sglang/issues/35797)）；CPU `topk_sigmoid_cpu` 越界读取（[#36600](https://github.com/sgl-project/sglang/issues/36600)）；`store_cache_4d` 中 ROCm 7.2.0 Triton 编译失败（[#29864](https://github.com/sgl-project/sglang/issues/29864)）；`--tokenizer-worker-num > 1` 时动态 LoRA 损坏（[#31084](https://github.com/sgl-project/sglang/issues/31084)）。这些是以陈旧为由关闭，**并非已修复**——不要把关闭视为问题已解决。

---

## 6. 这对应用开发者意味着什么

- **重启成本即将不再是你的问题——但仅限于权重加载。** Weight Cache Daemon 在大型 FP8 模型上将冷启动从约 5 分钟缩短到亚秒级。如果你在 200B+ 级模型上运行自动扩缩容或蓝绿发布，这是值得跟踪的功能；注意 Phase 1 仅覆盖量化后权重，KV/HiCache 预热是独立路径。
- **在仅 pip 安装的环境中，不应盲目启用投机解码。** 如果你的镜像没有系统 CUDA 工具包，投机解码会在首个采样请求时硬杀死进程（[#43412](https://github.com/sgl-project/sglang/issues/43412)）。在承接流量前，通过健康检查发出一个 `temperature=0.7` 请求来门控投机解码，或将 CUDA 工具包固定进镜像。
- **在信任 SM90 上的 Kimi-K3 MXFP4 输出前，先做验证。** 静默激活函数替换（[#45755](https://github.com/sgl-project/sglang/issues/45755)）意味着你会得到流畅、格式良好但错误的答案——这是 agent 最糟糕的失败模式。请固定使用较旧镜像，或对该模型/硬件组合使用非 MXFP4 检查点。
- **EP>1 部署每次启动都要支付调优税。** 由于每 rank 形状不一致时 FlashInfer autotune 缓存就会被丢弃（[#40320](https://github.com/sgl-project/sglang/issues/40320)），每次重启后的首批请求会命中未预热的融合 MoE 路径。请预留一次预热突发，而不是立即路由用户流量。
- **工具调用可靠性正在改善。** [PR #45272](https://github.com/sgl-project/sglang/pull/45272) 阻止 `tool_choice="auto"` 在只有一个工具为 `strict: true` 时约束所有工具，[PR #46706](https://github.com/sgl-project/sglang/pull/46706) 修复 DeepSeek V4/V4.1 拒绝空内容和末尾工具调用轮次的问题——二者都直接影响 agent 循环稳定性。
- **关注“已关闭但未修复”清单。** 若干正确性 bug（EP>1 下的 NVFP4 MoE、Qwen3.5 MTP 权重加载、跨 tokenizer worker 的动态 LoRA）被以 `inactive` 关闭，而非修复。如果你的技术栈涉及这些代码路径，请将其视为已知损坏并在本地验证。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-10-27

## 1. 今日亮点

过去 24 小时后端工作密集落地，重点包括 Hexagon（VTCM 大小调整、`MUL_MAT_ID` 行跳过）、类型通用的 CPU `concat` 内核、Vulkan RMSNorm 子组归约，以及 SYCL 启动/内存调优。正确性修复也已发布：OpenCL KV-cache 状态恢复和 Metal 上的 Laguna MoE 溢出。与此同时，issue 流量主要由统一 KV 提示词处理回归、SYCL 设备/输出故障，以及 `llama-server` 路由器/工具注册功能的持续成熟所占据。

## 2. 发布与破坏性变更

共发布十个构建（`b11901`–`b11913`；`b11900`、`b11902`、`b11903`、`b11911` 未出现在发布信息流中）。

| 构建 | 变更 | 链接 |
|---|---|---|
| b11913 | Hexagon：新增 `GGML_HEXAGON_VTCM_SIZE` 构建/运行时开关（由 `VTCM_MB` 重命名而来；取消 `NTHREADS` 上限） | [#31221](https://github.com/ggml-org/llama.cpp/pull/31221) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11913) |
| b11912 | Laguna：启用 MoE amax f16 重缩放（修复 prefill 微批 ≥ 32 时的 Metal 溢出） | [#31147](https://github.com/ggml-org/llama.cpp/pull/31147) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11912) |
| b11910 | OpenCL：KV-cache 视图写入 `set_tensor`，因此状态恢复不再被静默丢弃 | [#30471](https://github.com/ggml-org/llama.cpp/pull/30471) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11910) |
| b11909 | Hexagon：`MUL_MAT_ID` 的跳过行清零已向量化，并加入融合的 `MM_ID_NX` | [#31169](https://github.com/ggml-org/llama.cpp/pull/31169) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11909) |
| b11908 | `no_alloc` 从 `llama_hparams` 中移出（内部结构体布局变更） | [#31141](https://github.com/ggml-org/llama.cpp/pull/31141) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11908) |
| b11907 | 模型图构建器现在返回输出结构体，而不是 `ggml_tensor**` 出参 | [#31142](https://github.com/ggml-org/llama.cpp/pull/31142) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11907) |
| b11906 | CPU `concat` 重写为覆盖所有类型的单行内核（memcpy 快速路径，包含量化类型） | [#31179](https://github.com/ggml-org/llama.cpp/pull/31179) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11906) |
| b11905 | Vulkan RMSNorm 通过子组归约优化（包含 AMD 厂商路径与快速路径） | [#29882](https://github.com/ggml-org/llama.cpp/pull/29882) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11905) |
| b11904 | SYCL 普通 MMVQ 启动三元组模板化 | [#31013](https://github.com/ggml-org/llama.cpp/pull/31013) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11904) |
| b11901 | SYCL 重排临时内存限制为 32 MiB | [#31062](https://github.com/ggml-org/llama.cpp/pull/31062) · [发布](https://github.com/ggml-org/llama.cpp/releases/tag/b11901) |

**集成者注意事项：** `b11907`/`b11908` 是内部 C++ API 变更——树外模型实现，或改动 graph builder/hparams 的 fork，需要 rebase。`b11910` 改变了行为（OpenCL 状态恢复现在真正生效），因此请验证会话保存/恢复路径。一项从 memory 实例中移除废弃 `n_pad` 的后续清理已在 [#31200](https://github.com/ggml-org/llama.cpp/pull/31200) 中开放。

## 3. 新模型与硬件支持

- **Laguna（MoE）** — amax 重缩放扩展至 `LLM_ARCH_LAGUNA`，与 Mistral4/Mellum/Flint 一致；随 `b11912` 发布（[#31147](https://github.com/ggml-org/llama.cpp/pull/31147)）。
- **GigaChat 3.5（432B-A28B MoE）** — 模型/转换/测试 PR 已开放；采用 DeepSeek-V3 风格主干（MLA + DeepSeek MoE），带混合注意力（[#25342](https://github.com/ggml-org/llama.cpp/pull/25342)）。
- **DeepSeek-V4.1-Flash** — 模型 PR `#30817` 被 issue `#30944` 引用；feed 中还有一个已合并的 DSV4.1 flash PR（[#31086](https://github.com/ggml-org/llama.cpp/pull/31086)）。按进行中/预览状态对待。
- **CUDA 上 Vision-tower head 尺寸 40/72** — 此前被排除在所有 tensor-core FA 调度之外（Gemma 3/4、Qwen3-VL 视觉塔），现在在 MMA 片段中填充到下一个 16 的倍数（[#30285](https://github.com/ggml-org/llama.cpp/pull/30285)）。
- **Metal 多 GPU** — 每个物理 GPU 一个后端设备，并增加跨设备拷贝保护（修复 Intel Mac + eGPU 选择问题）；PR 已开放（[#28568](https://github.com/ggml-org/llama.cpp/pull/28568)）。
- **Hexagon** — VTCM 大小可配置与 `MUL_MAT_ID` 改进已合入（[#31221](https://github.com/ggml-org/llama.cpp/pull/31221)、[#31169](https://github.com/ggml-org/llama.cpp/pull/31169)）。
- **AMD XDNA** — 后端请求仍开放，获得强力支持（39 👍）（[#21725](https://github.com/ggml-org/llama.cpp/issues/21725)）。

## 4. 性能与优化

**已合入**
- **Vulkan RMSNorm** 通过子组归约实现，包含 AMD 专用路径（[#29882](https://github.com/ggml-org/llama.cpp/pull/29882)）。
- **SYCL**：将重排暂存限制为 32 MiB（[#31062](https://github.com/ggml-org/llama.cpp/pull/31062)）；MMVQ 启动三元组模板化（[#31013](https://github.com/ggml-org/llama.cpp/pull/31013)）。
- **Hexagon**：在 `MUL_MAT_ID` 中向量化跳过行的清零并移除标量除法（[#31169](https://github.com/ggml-org/llama.cpp/pull/31169)）。
- **CPU `concat`**：面向所有类型的单行内核，为稠密与量化行提供 memcpy 快速路径（[#31179](https://github.com/ggml-org/llama.cpp/pull/31179)）。

**进行中 / 已报告数据**
- **SYCL `ggml_sycl_comm_exchange()`**：在双 Arc Pro B70 上使用 `-sm tensor` 报告 **+16.7% 至 +20.6%（均值 ≈ +19%）prompt-eval t/s**，PPL 零变化（[#30956](https://github.com/ggml-org/llama.cpp/pull/30956)）。
- **Vulkan CM1 attention**：softmax P 以行主序存储，片段在 V tile 间复用；更新所有 CM1 设备的共享内存估算（[#31197](https://github.com/ggml-org/llama.cpp/pull/31197)）。
- **Vulkan FA 中的 Q4_0 KV cache**：dequant+transpose 快速路径，针对 GCN1/D700 上量化 KV 极慢的行为（[#30738](https://github.com/ggml-org/llama.cpp/pull/30738)）。
- **Vulkan Intel FA prefill shader**，以及为 prefill/decode 合并的 push-constant 定义（[#29357](https://github.com/ggml-org/llama.cpp/pull/29357)）。
- **CPU 大型单行操作**：当 nrows < nth 且行大小超过阈值时动态 reshape/拆分（[#31218](https://github.com/ggml-org/llama.cpp/pull/31218)）。
- **量化工具**：通过预计算 15 个中点而非二分查找，使 `IQ4_NL`/`IQ4_XS` 创建提速且输出逐位一致（[#31201](https://github.com/ggml-org/llama.cpp/pull/31201)）。
- **Vulkan coopmat1 int8 matmul**：将 A 预取钳制到 `end_k`（修复 [#29342](https://github.com/ggml-org/llama.cpp/issues/29342)）（[#30283](https://github.com/ggml-org/llama.cpp/pull/30283)）。
- **Server KV 复用**：在统一 KV 下清理空闲槽位后重试缓存提示词恢复，避免完整重处理（[#30997](https://github.com/ggml-org/llama.cpp/pull/30997)）。

## 5. 稳定性与回归

按严重程度排序：

1. **SYCL GPU 驱动 TDR 重置** — 在双 Arc Pro B70 上加载 DFlash2 草稿模型（`--model-draft`）会触发 `VIDEO_TDR_TIMEOUT_DETECTED`；进程死亡且无应用层错误。开放中，无修复 PR（[#28778](https://github.com/ggml-org/llama.cpp/issues/28778)）。
2. **统一 KV 提示词处理回归** — 使用 `-np 2` 和 `--kv-unified` 时，PP 从第二次长请求开始下降 **42–54%**。已定位根因：CUDA/HIP FA 内核只跳过 mask 尾部（`KV_max`），而不跳过内部全为 `-INF` 的块，因此序列会为属于其他槽位的单元付出代价。开放中（[#28495](https://github.com/ggml-org/llama.cpp/issues/28495)）。
3. **SYCL DeltaNet token 乱码（token soup）** — 在 Arc Pro B70 上长提示词（Qwen3.6、Qwen3.8、Nemotron）出现。开放中（[#29183](https://github.com/ggml-org/llama.cpp/issues/29183)）。
4. **SYCL 在 Qwen3.6 35B A3B（B580）上输出异常**。开放中（[#28728](https://github.com/ggml-org/llama.cpp/issues/28728)）。
5. **Vulkan SWA 中止** — 在混合/SWA 模型、2× Arc Pro B50 上，2.40/2.41 相比 2.39 出现 `GGML_ABORT cache_k_l0` 回归。开放中（[#29082](https://github.com/ggml-org/llama.cpp/issues/29082)）。
6. **DeepSeek-V4.1-Flash 前缀损坏** — 复用缓存提示词前缀（Engram n-gram 历史）时出现单 token 输出损坏，两种 `--lazy-mode` 设置下均可复现；针对 `deepseek41` 分支，而非 master（[#30944](https://github.com/ggml-org/llama.cpp/issues/30944)）。
7. **Vulkan/RADV Radeon 680M** — `--load-mode auto` 在大型 Gemma 上导致设备丢失/OOM；`--load-mode mmap` 可绕过。开放中（[#27360](https://github.com/ggml-org/llama.cpp/issues/27360)）。
8. **CUDA tensor split 中止** — Qwen3.8-Flash-Next 在 2× RTX 5090 上使用 `--split-mode tensor` 时以 `SPLIT_AXIS_UNKNOWN` 中止。开放中（[#27964](https://github.com/ggml-org/llama.cpp/issues/27964)）。
9. **qwen4exp 在合并张量下断言失败** — 来自 `#31028`，在 Vulkan / Ryzen AI Max+ 395 上。新增，开放中（[#31189](https://github.com/ggml-org/llama.cpp/issues/31189)）。
10. **安全：通过 WebUI `?q=` 查询参数进行提示词注入** — `llama-server` UI 中存在未认证注入向量。开放中，标记为安全（[#25790](https://github.com/ggml-org/llama.cpp/issues/25790)）。
11. **RX 6900 XT 上 Vulkan 设备丢失**（[#27076](https://github.com/ggml-org/llama.cpp/issues/27076)）；**AMD dGPU 上禁用 Resizable BAR 时 token 生成缓慢**（[#27097](https://github.com/ggml-org/llama.cpp/issues/27097)）。

**今日修复/关闭：** OpenCL KV 状态恢复（[#30471](https://github.com/ggml-org/llama.cpp/pull/30471)，随 `b11910` 发布）；Laguna MoE 溢出（[#31147](https://github.com/ggml-org/llama.cpp/pull/31147)，`b11912`）；`b10780` 后 Vulkan RDNA3 PP 回归（[#28752](https://github.com/ggml-org/llama.cpp/issues/28752)，已关闭）；SYCL `-sm tensor` 在双 B70 上的内存泄漏报告（[#27845](https://github.com/ggml-org/llama.cpp/issues/27845)，已关闭）；ngram-cache 推测缓存在请求间串扰，接受率 **86% → 11%**（[#27852](https://github.com/ggml-org/llama.cpp/issues/27852)，已关闭）。

## 6. 对应用开发者的意义

- **服务端功能正在整合。** 今日关闭的若干跟踪 issue 指向能力更强的 `llama-server`：服务端工具注册表使 MCP/tools 可在 `llama-cli` 及 WebUI 中使用（[#20673](https://github.com/ggml-org/llama.cpp/issues/20673)）；模型管理 API（`GET /models?reload=1`、`POST /models/download`）（[#21779](https://github.com/ggml-org/llama.cpp/issues/21779)）；路由模式下每个已加载模型支持多个预设（[#23704](https://github.com/ggml-org/llama.cpp/issues/23704)）；以及 MCP 服务器激活状态在重启后持久化（[#25349](https://github.com/ggml-org/llama.cpp/issues/25349)）。请为这些 API 做规划，但在稳定前固定构建版本。
- **多槽位服务仍有棘手问题。** `-np 2` + `--kv-unified` 的提示词处理下降（[#28495](https://github.com/ggml-org/llama.cpp/issues/28495)）是真实的容量规划风险：吞吐量在后续长请求上下降，而非第一个。如果你运行并发长上下文工作负载，请对首个请求之后的每个请求做基准测试，或暂时避免使用统一 KV。缓存提示词恢复重试 PR（[#30997](https://github.com/ggml-org/llama.cpp/pull/30997)）可直接降低该模式下的重处理成本。
- **SYCL（Intel Arc）在推测解码与混合模型上需谨慎。** 草稿模型导致的 TDR 重置（[#28778](https://github.com/ggml-org/llama.cpp/issues/28778)）与长提示词下 DeltaNet token 乱码（[#29183](https://github.com/ggml-org/llama.cpp/issues/29183)）仍未解决。与此同时，`-sm tensor` 吞吐量正在改善（[#30956](https://github.com/ggml-org/llama.cpp/pull/30956) 报告 prompt eval 约 +19%）。
- **在 OpenCL 上，`b11910` 之后的会话/状态持久化更安全；** 请在该后端重新测试任何保存/恢复或基于快照的热启动逻辑。
- **量化流水线成本更低：** `IQ4_NL`/`IQ4_XS` 创建更快且输出逐位一致（[#31201](https://github.com/ggml-org/llama.cpp/pull/31201)）——合并后可安全用于 CI 工具链。
- **对于 agent 框架，** 开放的 `?q=` 注入 issue（[#25790](https://github.com/ggml-org/llama.cpp/issues/25790)）提醒：在没有反向代理和认证的情况下，内置 WebUI 不应暴露到 localhost 之外。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-10-27

## 1. 今日亮点

Ollama 发布了 **v0.40.12-rc0**，聚焦 MLX/Apple Silicon 路径：提升 Gemma/Flint/Laguna 的 prompt 处理速度、支持 DFlash2 草稿，并改善 Gemma 4 视觉塔中的小图像保真度。稳定性方面，一个导致生成速度变慢约 30× 的 AMD Vulkan prompt 缓存回归仍未解决，而 `ollama launch opencode` 在 OpenCode v2 上不可用，目前已有多个修复 PR 正在进行中。工具调用解析也受到关注，包括保留未识别的工具调用，以及修复 Gemma 4 未闭合列表解析器。

## 2. 发布与破坏性变更

- **v0.40.12-rc0**（候选版本）— 聚焦 MLX 的变更：
  - 保留紧凑型 Gemma 4 视觉塔的小图像细节 — [PR #19247](https://github.com/ollama/ollama/pull/19247)
  - 升级 MLX/MLX-C 版本，以提升 Gemma/Flint/Laguna 的 prompt 速度 — [PR #19291](https://github.com/ollama/ollama/pull/19291)
  - 在 MLX 中新增 DFlash2 支持 — [PR #19149](https://github.com/ollama/ollama/pull/19149)
- **上游 llama.cpp 升级至 b11901** — 已在本时间窗口关闭；属于例行运行时同步，未显示 API/迁移影响 — [PR #19311](https://github.com/ollama/ollama/pull/19311)
- 所提供数据中**未显示 Ollama API 或配置破坏性变更**。请注意，OpenCode v2 启动失败是由 OpenCode CLI 标志变更引起的，并非 Ollama API 变更；修复已在 [PR #19310](https://github.com/ollama/ollama/pull/19310)、[PR #19307](https://github.com/ollama/ollama/pull/19307) 和 [PR #19298](https://github.com/ollama/ollama/pull/19298) 中开放。

## 3. 新模型与硬件支持

- **面向 MLX 的 DFlash2 投机/草稿支持** — 通过块局部动态卷积和候选选择器扩展 DFlash 块草稿；适用于 Apple Silicon 推理 — [PR #19149](https://github.com/ollama/ollama/pull/19149)
- **面向 Qwen3 和 Qwen3.5 的 YaRN RoPE 缩放** — 在受支持的配置 schema 中遵循显式 RoPE 缩放元数据，应用于文本和 mRoPE，同时保留非旋转维度 — [PR #19231](https://github.com/ollama/ollama/pull/19231)
- **Gemma 4 视觉塔** — 改善紧凑型 MLX 视觉配置中的小图像细节保留 — [PR #19247](https://github.com/ollama/ollama/pull/19247)
- **MLX / MLX-C 运行时升级** — 利用新的窗口大小能力提升 prompt 速度 — [PR #19291](https://github.com/ollama/ollama/pull/19291)

尚未落地的请求：
- 为 `phonon:40m-multilingual` TTS 提供韩语支持 — [Issue #19295](https://github.com/ollama/ollama/issues/19295)
- 原生 HarmonyOS PC 支持，先从 CPU 推理开始 — [Issue #19294](https://github.com/ollama/ollama/issues/19294)
- 为 Intel Mac 提供 macOS 12/13 支持（已提供 v0.40.11 的社区构建） — [Issue #19308](https://github.com/ollama/ollama/issues/19308)
- 可下载的 `deepseek-v4.1-flash` 模型文件 — [Issue #18379](https://github.com/ollama/ollama/issues/18379)

## 4. 性能与优化

- 通过 MLX/MLX-C 版本升级，为 Gemma、Flint 和 Laguna 带来 **MLX prompt 速度提升**；所提供说明中未包含确切基准数据 — [PR #19291](https://github.com/ollama/ollama/pull/19291)
- MLX 中的 **DFlash2 草稿路径**是一项推理速度特性；预计可在受支持的 Apple Silicon 硬件上提升生成吞吐量 — [PR #19149](https://github.com/ollama/ollama/pull/19149)
- 面向 Qwen3/Qwen3.5 的 **YaRN 缩放**改善长上下文表现，并提升扩展上下文配置下 prefill/decode 的正确性 — [PR #19231](https://github.com/ollama/ollama/pull/19231)
- **需关注的回归：** 在启用 prompt 缓存时，AMD RADV + Vulkan 在第二次相同请求中出现可复现的**约 29.7× 生成吞吐量骤降**（RX 6600 XT，Ollama 0.40.8） — [Issue #19201](https://github.com/ollama/ollama/issues/19201)
- **空闲功耗回归：** 当存在两个或更多 runner 进程驻留时，AMD Strix Halo（gfx1151）会将 GPU 固定在最高频率（约 25 W），即使没有正在处理的请求 — [Issue #17958](https://github.com/ollama/ollama/issues/17958)
- **大上下文窗口下 VRAM 估算偏低**（有报告称 262k 上下文的 Qwen2.5 7B 使用了约 10 GB） — [Issue #10128](https://github.com/ollama/ollama/issues/10128)

## 5. 稳定性与回归

按严重程度排序：

**高**
- **AMD Vulkan prompt 缓存变慢（约 30×）** — 在启用主机内存 prompt 缓存时，第二次相同请求会使生成吞吐量骤降；模型已完全 GPU 卸载。本时间窗口内未见修复 PR — [Issue #19201](https://github.com/ollama/ollama/issues/19201)
- **macOS Metal 崩溃** — `mlx runner failed: panic: mlx: [METAL] Command buffer execution failed`，在 Ollama 0.40.9 上返回 HTTP 500；未见修复 PR — [Issue #19278](https://github.com/ollama/ollama/issues/19278)
- **长时间流式回复后 `/api/chat` 500** — `llama-server apply-template error … write: broken pipe`，在 macOS 上的 0.40.8 和 0.40.11 可复现；重试可成功。未见修复 PR — [Issue #19293](https://github.com/ollama/ollama/issues/19293)
- **Windows 上大 prompt 约 15 秒后连接重置** — 在搭载 RTX 3090 的 Windows 上，`/api/chat` 会重置；标记为 needs-more-info — [Issue #19139](https://github.com/ollama/ollama/issues/19139)

**中**
- **GPT-OSS 20B 在 AMD Radeon Pro 5700 XT（Vulkan）上返回空响应**，而 CPU 推理正常；标记为 needs-more-info — [Issue #19060](https://github.com/ollama/ollama/issues/19060)
- **Claude Code + 云模型连接被拒绝**，出现在 Windows 11 / Ollama 0.40.11 — [Issue #19281](https://github.com/ollama/ollama/issues/19281)
- **`ollama launch opencode` 在 OpenCode v2 上不可用** — 两个不同故障：裸命令上 `--model` 标志被拒绝，以及 `opencode debug config` 现在返回数组而非对象。修复 PR 已开放 — [Issue #19296](https://github.com/ollama/ollama/issues/19296)、[Issue #19303](https://github.com/ollama/ollama/issues/19303)、[PR #19310](https://github.com/ollama/ollama/pull/19310)、[PR #19307](https://github.com/ollama/ollama/pull/19307)、[PR #19298](https://github.com/ollama/ollama/pull/19298)
- **未识别的工具调用被静默丢弃** — 解析器会为请求工具列表中不存在的函数缓存带标签的调用，但对于非 JSON 标签，`Content()` 返回空，最终产生一个既无内容也无工具调用的成功响应。修复 PR 已开放 — [Issue #19187 referenced by PR #19240](https://github.com/ollama/ollama/pull/19240)
- **Gemma 4 工具调用参数中的未闭合列表** — `gemma4:e4b` 在长字符串编辑时省略 `]`/`}`；解析器修复失败，调用会被记录而非执行。修复 PR 已开放 — [PR #19274](https://github.com/ollama/ollama/pull/19274)
- **Kimi CLI 仅工具调用的助手轮次** — `content: []` 未像 `null`/空字符串那样处理；修复 PR 已在本时间窗口关闭 — [PR #19312](https://github.com/ollama/ollama/pull/19312)

**较低严重性 / 正确性**
- **ShieldGemma 模板不匹配** — 在分类用户 prompt 时，库模板询问的是“an AI response”是否违反策略，涉及 2b/9b/27b 标签 — [Issue #19313](https://github.com/ollama/ollama/issues/19313)
- **Ornith 35B renderer/parser 在 Claude Code / OpenAI 兼容客户端中输出原始特殊 token 和未解析的工具调用 XML** — [Issue #16992](https://github.com/ollama/ollama/issues/16992)
- **Claude Code 自动压缩失败**，涉及 `deepseek-v4.1-flash:1017-cloud`；长时间运行的 `/goal` 循环耗尽上下文而不进行压缩 — [Issue #19249](https://github.com/ollama/ollama/issues/19249)
- **压缩正文恢复工作正在进行** — 在 413 恢复期间保留所留存的图像与工具配对；相关 PR 已开放/关闭 — [PR #19309](https://github.com/ollama/ollama/pull/19309)、[PR #19304](https://github.com/ollama/ollama/pull/19304)
- **功能请求：在 VRAM 不足时禁用 CPU 回退** — 本组中需求最高的开放请求（22 👍） — [Issue #10707](https://github.com/ollama/ollama/issues/10707)
- **在 ollama.com 模型库中公开 `renderer`、`parser`、`template_source`** — CLI `ollama show` 已展示这些信息 — [Issue #19306](https://github.com/ollama/ollama/issues/19306)
- **桌面 UI 中的 GPU 加速检测/配置** — Intel Arc iGPU/Vulkan 用户缺乏清晰状态 — [Issue #19301](https://github.com/ollama/ollama/issues/19301)

## 6. 这对应用开发者意味着什么

- **Apple Silicon 推理是当前活跃的优化目标。** v0.40.12-rc0 通过 MLX 带来 prompt 速度提升和 DFlash2 支持。如果你在 Mac 上服务 Gemma/Flint/Laguna，这个 RC 值得做基准测试，但在生产环境中仍应将其视为 RC。
- **在 [Issue #19201](https://github.com/ollama/ollama/issues/19201) 中的约 30× 变慢问题解决前，避免在 AMD RADV/Vulkan 上启用 prompt 缓存**；固定使用已知良好的版本，或为延迟敏感型工作负载切换后端。
- **加固工具调用客户端，以应对空内容响应。** 在 [PR #19240](https://github.com/ollama/ollama/pull/19240) 落地前，模型对未声明函数发出调用时，可能产生一个既无 `content` 也无 `tool_calls` 的 `200`。应将这种组合视为可重试的异常，而不是有效轮次。
- **固定使用 OpenCode v1，或推迟升级到 OpenCode v2。** `ollama launch opencode` 目前在 v2 上失败；应跟踪 [PR #19310](https://github.com/ollama/ollama/pull/19310) 和 [PR #19307](https://github.com/ollama/ollama/pull/19307) 中的 `--standalone` / 内联配置修复。
- **长时间运行的 agent 应考虑压缩边界情况。** 云模型（例如 `deepseek-v4.1-flash`）的自动压缩，以及 413 恢复期间的图像/工具配对保留仍在稳定中 — 参见 [Issue #19249](https://github.com/ollama/ollama/issues/19249) 和 [PR #19309](https://github.com/ollama/ollama/pull/19309)。
- **Windows + 大 prompt 仍然脆弱**（[Issue #19139](https://github.com/ollama/ollama/issues/19139) 中约 15 秒重置）。保持 prompt 有界，启用带幂等请求 ID 的客户端重试，并优先使用具有可恢复状态的流式响应。
- **Qwen3/Qwen3.5 长上下文用户**应关注 [PR #19231](https://github.com/ollama/ollama/pull/19231) 中的 YaRN 元数据支持，它提升了扩展上下文部署的正确性。

---

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-10-27

## 1. 今日亮点

过去 24 小时内没有发布新版本，但项目正处于一场广泛的“从实验性功能毕业”重构中：MCP server/client、Anthropic `/v1/messages` handler 和 Admin UI bundle 都在迁移到 GA 路径，并带有向后兼容的兼容层（[#48513](https://github.com/BerriAI/litellm/pull/48513)、[#48510](https://github.com/BerriAI/litellm/pull/48510)、[#48512](https://github.com/BerriAI/litellm/pull/48512)）。在正确性方面，一个高风险的花费计数器重复计数问题——可能让受预算约束的客户因 422 被锁定——已有修复正在审核中（[#47676](https://github.com/BerriAI/litellm/pull/47676)），同时还有针对 Anthropic 思考模式 400 错误以及 Responses/Gemini 转换中媒体丢失的修复。成本映射元数据还新增了 `context_window` 字段，并通过 `/model/info` 暴露（[#48516](https://github.com/BerriAI/litellm/pull/48516)）。

## 2. 发布与破坏性变更

**过去 24 小时内没有新发布。**

为升级规划值得跟踪的进行中变更：

- **成本映射 schema 新增** — 每个模型都新增了 `context_window`，并从 `/model/info` 返回；已在 164 条 Claude、Gemini 和 OpenAI 记录中预置。属于新增字段，但 `/model/info` 的消费方应能容忍这个新键。（[#48516](https://github.com/BerriAI/litellm/pull/48516)）
- **MCP 现已成为 GA** — `litellm/proxy/mcp_server` 和 `litellm/mcp_client` 取代 `_experimental` 路径；旧包保留为已弃用的兼容层。（[#48513](https://github.com/BerriAI/litellm/pull/48513)）
- **Anthropic handler 重命名** — `llms/anthropic/pass_through` → `llms/anthropic/messages`（原生 handler 位于 `messages/native`）；仅为内部路径移动。（[#48510](https://github.com/BerriAI/litellm/pull/48510)）
- **行为变更：500 → 403** — `/key/generate` 及相关路径上仅限企业版的 `get_spend_routes` 现在会返回明确的 `403` 并说明许可证要求，而不是裸 `500`。（[#47112](https://github.com/BerriAI/litellm/pull/47112)）
- **CI 治理** — 新增 `Spend logs guard`，在获得 `kerry-berri` 批准前阻止对 `LiteLLM_SpendLogs` schema/迁移的修改（[#48518](https://github.com/BerriAI/litellm/pull/48518)）；`@kerry-berri` 被添加为三份 `schema.prisma` 副本的代码所有者（[#48515](https://github.com/BerriAI/litellm/pull/48515)）。
- **文件重命名** — 以模型版本命名的转换文件重命名为系列名称；所有导出符号保持不变。（[#48509](https://github.com/BerriAI/litellm/pull/48509)）

## 3. 新模型与硬件支持

- **DashScope 实时 WebSocket** — 在共享的 OpenAI realtime 基类上新增 `DashScopeRealtime` handler，处理 DashScope 扁平的 `session.update` 格式；否则 GA 重映射会破坏该格式。审核中。（[#40579](https://github.com/BerriAI/litellm/pull/40579)）
- **Vertex 图像尺寸** — 请求的尺寸现在会映射到受支持的宽高比，而不是静默生成方形图像，并将匹配的分辨率传给 Gemini；畸形尺寸和 `auto` 保持不变。（[#48463](https://github.com/BerriAI/litellm/pull/48463)）
- **`azure_ai/gpt-image-2`** 的定价/上下文条目已添加到 `model_prices_and_context_window.json` 及其备份中。（[#26765](https://github.com/BerriAI/litellm/issues/26765)）
- **已知提供商缺口（待解决）：** Scaleway 未在 `embedding()` 端点上路由，并抛出 `LiteLLMUnknownProvider`（[#34503](https://github.com/BerriAI/litellm/issues/34503)）；Gemini 显式上下文缓存在使用自定义 `api_base` 时失败，原因是缓存 URL 和认证头无效（[#34872](https://github.com/BerriAI/litellm/issues/34872)）。
- 本时间窗口内没有新的硬件后端（CUDA/ROCm/Metal/CPU）或量化格式。

## 4. 性能与优化

- **限流器减少 Redis 写入** — v3 token 计数器现在只写入处于打开状态的限流窗口。此前，一个仅设置 RPM 限制的 key 会为无人读取的计数器付出**每次请求最多五次额外 Redis 写入**的代价。（[#47521](https://github.com/BerriAI/litellm/pull/47521)）
- **统一多模态配额核算** — 为 TPM 准入采用同一套媒体计量，支持按模型估算图像和按时长估算音频。修复了聊天图像/音频在仅按文本计数时仍通过准入的问题，以及 Responses 将 base64 媒体误计为文本的问题。（[#48099](https://github.com/BerriAI/litellm/pull/48099)）
- **数据库生命周期整合** — 由单一 lifespan 所有者负责数据库关闭，包括启动失败和取消场景；在断开连接前先排空请求，且不会打开第二个连接池。（[#48506](https://github.com/BerriAI/litellm/pull/48506)、[#48507](https://github.com/BerriAI/litellm/pull/48507)）
- **流式响应头延迟回归已关闭** — v1.105.0 中 `GZipBufferedResponseMiddleware` 将 SSE 响应头缓冲到首个 chunk 的问题已解决，恢复 `/mcp/<server>` 的 `tools/call` 流式响应及时发送响应头（此前会卡住直到 15s 的 sse-starlette ping）。（[#46659](https://github.com/BerriAI/litellm/issues/46659)）
- **静默上游超时** — 当上游接受 TCP 连接但从未写入任何字节时，`litellm_settings.request_timeout` 现在会触发。（[#38358](https://github.com/BerriAI/litellm/issues/38358)）
- 本周期没有发布吞吐量/延迟基准测试。

## 5. 稳定性与回归

**高**

- **[risk:high] 花费计数器重复计数 → 422 锁定。** 在花费计数器刚变冷后同时到达的请求各自重新加上了客户的完整花费，使未超预算的客户触发 422 预算错误。现在冷计数器会被*提升*到缓存的花费，而不是加和。修复 PR 已开启。（[#47676](https://github.com/BerriAI/litellm/pull/47676)）
- **仅数据库部署的成本映射被清空。** 当 `model_list` 为空且 `STORE_MODEL_IN_DB=True` 时，周期性价格重载（包括启动后的首次轮询）会清空每个数据库部署的运行时成本映射注册。开放中，暂无关联修复 PR。（[#43444](https://github.com/BerriAI/litellm/issues/43444)）
- **Anthropic 思考重试返回 400。** 当 LiteLLM 禁用扩展思考时，仍保留了 `clear_thinking` 上下文管理编辑，而 Anthropic 会拒绝该编辑——最常见于错误思考签名后的自动重试。修复 PR 已开启。（[#48514](https://github.com/BerriAI/litellm/pull/48514)）

**中**

- **转换过程中媒体丢失。** 音频、PDF 工具结果和模型历史媒体可能在 Responses ↔ Gemini 转换以及最终 Gemini 序列化程序中静默丢失。修复 PR 已开启。（[#48484](https://github.com/BerriAI/litellm/pull/48484)）
- **企业版门禁返回 500。** 已修复为 `403`。（[#47112](https://github.com/BerriAI/litellm/pull/47112)）
- **针对 GitHub 的 MCP OAuth 在成功时失败。** aiohttp 默认的 8190 字节 header 限制会拒绝 GitHub token 响应中约 9KB 的 CSP header；已提高出站会话限制。（[#48517](https://github.com/BerriAI/litellm/pull/48517)）
- **Responses 流式错误被掩盖。** `LiteLLMCompletionStreamingIterator` 缺少 `completed_response`，导致错误处理器自身因 `AttributeError` 崩溃，并隐藏了真实的上游故障。已关闭。（[#38511](https://github.com/BerriAI/litellm/issues/38511)）
- **vLLM `cached_tokens` 未计入成本。** Token 成本计算器忽略来自 vLLM 的 prompt 缓存命中，导致花费被高估。开放中，5 👍。（[#22984](https://github.com/BerriAI/litellm/issues/22984)）
- **Claude Opus 4.7 的参数声明错误。** `get_supported_openai_params()` 仍列出 `temperature`，而 Anthropic 在请求时会拒绝该参数。开放中，4 👍。（[#26444](https://github.com/BerriAI/litellm/issues/26444)）

**低**

- `litellm.Timeout: ... after None seconds` — 消息中的超时时间从未填充。已关闭。（[#14635](https://github.com/BerriAI/litellm/issues/14635)）
- UI 无法从模型中移除先前添加的 LiteLLM 参数。开放中。（[#23998](https://github.com/BerriAI/litellm/issues/23998)）
- `/ui/chat` 与托管 vLLM 一起使用时，在后续轮次会失败并报 `Response with id ... not found`（playground 正常）。开放中。（[#26147](https://github.com/BerriAI/litellm/issues/26147)）
- `alert_type_config.llm_requests_hanging.digest: true` 仍会在每次检查运行时发送一条 Slack 消息。开放中。（[#22753](https://github.com/BerriAI/litellm/issues/22753)）

## 6. 这对应用开发者意味着什么

- **预算执行值得尽快打补丁。** 如果你在并发流量下运行按客户预算，[#47676](https://github.com/BerriAI/litellm/pull/47676) 中的冷计数器重复计数会让未超预算的用户产生虚假 422——这是客户可见的故障，而不仅仅是指标不准确。
- **流式超时现在可信了。** 随着 [#38358](https://github.com/BerriAI/litellm/issues/38358) 关闭，`request_timeout` 真正能约束静默上游；对于“连接已接受、零字节”的情况，你不再需要自己的看门狗。[#46659](https://github.com/BerriAI/litellm/issues/46659) 中的 SSE 响应头缓冲回归也已解决，因此客户端侧依赖响应头的逻辑（请求 ID、追踪）在流式响应上重新可用。
- **将 MCP 作为一等接口来规划。** `_experimental` 路径现已成为弃用兼容层。新集成应面向 `litellm/proxy/mcp_server` 和 `litellm/mcp_client`（[#48513](https://github.com/BerriAI/litellm/pull/48513)）；MCP + GitHub OAuth 部署应升级到 [#48517](https://github.com/BerriAI/litellm/pull/48517)。
- **容量规划变得更容易。** `/model/info` 中的 `context_window`（[#48516](https://github.com/BerriAI/litellm/pull/48516)）让网关和路由器无需维护并行的模型注册表即可确定 prompt 大小——如果你基于该端点驱动路由或截断，这会很有用。
- **多模态 TPM 预算将发生变化。** [#48099](https://github.com/BerriAI/litellm/pull/48099) 中的统一媒体核算使图像/音频请求*更*可能被准入（此前被少计），并使 Responses 载荷*更不*可能被误拒。多模态流量的有效 TPM 余量预计会发生变化。
- **Anthropic 思考模式重试目前较脆弱**（[#48514](https://github.com/BerriAI/litellm/pull/48514)）。如果你依赖带自动重试的扩展思考，请固定行为或添加回退路径，直到修复落地。
- **数据库支持的模型部署：** 在 [#43444](https://github.com/BerriAI/litellm/issues/43444) 修复前，避免依赖跨重启的运行时成本映射注册，或者在 `config.yaml` 中设置显式成本，而不是仅设置在数据库中。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 摘要 — 2026-10-27

## 1. 今日亮点

Unsloth 发布了 **v0.1.911-beta**，新增定时提示词执行（本地模型或 API）、**MiniMax-M3.1-Flash** 支持、FLUX-3 图像编辑，以及又一轮性能提升，包括更快的图像生成和 Mac 聊天。同时，一大批 Studio PR 已合入，涉及显存回收、确定性扩散输出（相同种子 → 跨进程得到相同图像/片段）、ROCm attention 正确性以及 FSDP2 分布式训练修复。问题跟踪器主要被 Studio/桌面端报告占据，其中影响最大的未解决问题是混合 NVIDIA-AMD Linux 主机上的 AMD GPU 检测，以及 Apple Silicon 上 MiniMax-M3.1 的 NVFP4 CPU 回退。

## 2. 发布与破坏性变更

- **v0.1.911-beta — "Scheduled + MiniMax-M3.1-Flash + Perf Gains"**（[发布](https://github.com/unslothai/unsloth/releases/tag/v0.1.911-beta)）
  - **定时任务**：按计划针对本地模型或由 API 支持的提供方运行提示词。
  - 支持 **MiniMax-M3.1-Flash** 模型。
  - **支持 FLUX-3 编辑**，并为生成图像提供面部修饰控制。
  - 图像生成更快，Mac 聊天性能提升。
  - 本次发布未标明破坏性 API/配置变更或迁移说明。

## 3. 新模型与硬件支持

- **MiniMax-M3.1-Flash** — 已在 v0.1.911-beta 中加入；GGUF 变体现已出现在 Studio 中，首日就有 Apple Silicon 问题提交（[#15409](https://github.com/unslothai/unsloth/issues/15409)）。
- **FLUX-3 图像编辑** — 本次发布新增编辑流水线；使用 `unsloth/flux-3-image-klein-GGUF` 的 macOS 编辑路径曾损坏，现已关闭（[#15241](https://github.com/unslothai/unsloth/issues/15241)）。
- **Qwen-Image-2.1** — 正在加固扩散路径以实现跨进程可复现性（[#15455](https://github.com/unslothai/unsloth/pull/15455)）。
- **视频模型** — Wan2.1、Wan2.2-TI2V-5B、HunyuanVideo-1.5 和 dense MiniMax-H3 现在在再次加载时会获得预置种子的去噪器编译特化（[#15406](https://github.com/unslothai/unsloth/pull/15406)）。
- **T4 上的 SDXL + ControlNet** — 15–16 GB 显卡上的常驻提升（resident promotion）仍正常工作；已修复 ControlNet 的预算核算回归（[#15446](https://github.com/unslothai/unsloth/pull/15446)）。
- **Vulkan 上的 MiniCPM-V-4.7-1B** — 已报告文本生成失败并关闭（[#14987](https://github.com/unslothai/unsloth/issues/14987)）。
- **Cohere Transcribe（conformer ASR）** — 托管请求已关闭（[#5789](https://github.com/unslothai/unsloth/issues/5789)）。
- **gpt-oss** — 专家布局探测现在在选择 fused 与 split 的 bitsandbytes 专家时，会先读取 `config.transformers_weights`，再读取标准文件名（[#15386](https://github.com/unslothai/unsloth/pull/15386)）。
- **Blackwell（sm_100/sm_120）** — 将打包的 fp32 attention 重路由到 xformers 时，现在会保留 fp32 输出，而不再静默降级为 bf16（[#15410](https://github.com/unslothai/unsloth/pull/15410)）。

## 4. 性能与优化

- **原生缓存写入期间的显存回收**：int8 视频构建不再在整個后台写入期间持有 `transformer.state_dict()`；权重从活动模块读取，因此 offload 可以真正释放内存（[#15461](https://github.com/unslothai/unsloth/pull/15461)）。
- **扩散 int8 W8A8 编译回归**：编译后的 linear 使用 `1 << 27` 的分块预算，而 eager 使用 `1 << 25`，导致峰值内存高于 eager；修复后恢复 eager 峰值并遵循 `force-eager`（[#15456](https://github.com/unslothai/unsloth/pull/15456)）。
- **确定性扩散**：Qwen-Image-2.1 对相同种子在每个新进程中生成不同图像（3 个进程 × 5 次渲染）；固定 reduction-config 过滤器修复了该问题（[#15455](https://github.com/unslothai/unsloth/pull/15455)）。类似地，视频 VAE 解码路径（untiled / wide tiles / stock tiles）现在按 latent 形状锁定，因此空闲显存变化不会改变解码帧（[#15462](https://github.com/unslothai/unsloth/pull/15462)）。
- **iGPU 主机固定（pinning）**：现在按*目标*设备而非当前 GPU 进行分类，修复了 offload 钩子期间的误分类（[#15463](https://github.com/unslothai/unsloth/pull/15463)）。
- **ROCm attention**：self 与 cross attention 检测不再在被追踪的计算图内评估 query-length/key-length 保护条件，从而避免编译期比较失败（[#15458](https://github.com/unslothai/unsloth/pull/15458)）。
- **FSDP2**：`native_fsdp2_unsharded` 现在会先执行 gather，再清除 `post_forward_mesh_info`，修复了 int 型 `reshard_after_forward` 场景（[#15426](https://github.com/unslothai/unsloth/pull/15426)）；fp16 `_ShardedGradScaler` 现在无需显式 `unscale_` 即可在各 rank 间就溢出判断保持一致，并为融合优化器在每个设备上提供单个 0/1 标志（[#15416](https://github.com/unslothai/unsloth/pull/15416)）。
- **CI 成本**：`scripts/enforce_kwargs_spacing.py` 在该仓库高 PR 量下频繁冲击 pre-commit.ci；已关闭（[#14174](https://github.com/unslothai/unsloth/issues/14174)）。
- **UI 响应性**：在 ROCm 上，图像生成期间在 Edit 的 “Instructions” 框中输入极其缓慢；已关闭（[#15221](https://github.com/unslothai/unsloth/issues/15221)）。

## 5. 稳定性与回归

**高**
- **NVIDIA + AMD 混合 Linux 主机**：在使用自定义 llama.cpp 构建时，`unsloth studio` 下完全无法枚举 AMD GPU —— 仍开放，未引用修复（[#15444](https://github.com/unslothai/unsloth/issues/15444)）。
- **Apple Silicon 上的 MiniMax-M3.1 GGUF**：尽管 61/61 层报告已卸载到 GPU，NVFP4 仍回退到 CPU —— 仍开放（[#15409](https://github.com/unslothai/unsloth/issues/15409)）。
- **Anthropic `/v1/messages` 直通**：上下文溢出会返回不可恢复的 `400 exceed_context_size_error`，而不是触发自动压缩 —— 仍开放（[#15417](https://github.com/unslothai/unsloth/issues/15417)）。
- **DeepSeek-V4.1-Flash GGUF 聊天模板**：该模板会拒绝普通讨论图像标记的内容，并破坏对话历史；问题中已提供补丁 —— 仍开放（[#15285](https://github.com/unslothai/unsloth/issues/15285)）。

**中**
- **可能的磁盘泄漏**：每次使用 Studio 后 `C:` 会丢失 200–400 MB，且没有清理路径 —— 仍开放（[#14487](https://github.com/unslothai/unsloth/issues/14487)）。
- **模型 hub 加载所有 “deepseek 4.1 1017 uncensored” 结果均失败** —— 仍开放（[#15412](https://github.com/unslothai/unsloth/issues/15412)）。
- **Qwen3.5-2B 的 Studio 恢复功能损坏**：`_MODEL_WEIGHT_RE` 无法匹配 `model.safetensors-00001-of-00001.safetensors` —— 仍开放（[#15405](https://github.com/unslothai/unsloth/issues/15405)）。
- **Linux 桌面 SIGABRT**：在 KDE Plasma 上启用 AT-SPI/atk-bridge 时 `WebKitWebProcess` 崩溃 —— 仍开放（[#15260](https://github.com/unslothai/unsloth/issues/15260)）。
- **微调模型服务**：微调任务完成后，使用 `fine_tuned_model` 的聊天补全静默地从已加载的基础模型返回答案；修复 PR 仍开放（[#15449](https://github.com/unslothai/unsloth/pull/15449)）。
- **Codex MCP 服务器被丢弃**：`unsloth start codex` 启动的 Codex 未包含用户配置的 MCP 服务器；修复 PR 仍开放（[#15452](https://github.com/unslothai/unsloth/pull/15452)）。
- **文件回退深度上限**：历史仅记录 4 层文件夹和最多前 2,000 个文件；修复 PR 仍开放（[#15439](https://github.com/unslothai/unsloth/pull/15439)）。

**低 / 已关闭**
- 自动压缩在本地 GGUF 聊天中忽略了图像 token，导致截图工具循环中上下文溢出 —— 已关闭（[#14968](https://github.com/unslothai/unsloth/issues/14968)）。
- 编辑图库中的 “Open image in new window” / “Copy image” 无操作 —— 已关闭（[#15272](https://github.com/unslothai/unsloth/issues/15272)）。
- Project 中浏览器预览不可用 —— 已关闭（[#15046](https://github.com/unslothai/unsloth/issues/15046)）。
- 从 API 加载 STT 模型时，“Load into” 设置被忽略 —— 仍开放（[#15280](https://github.com/unslothai/unsloth/issues/15280)）。
- `formatKvRate` 在内存栏中显示 `1024 KiB` 而非 `1 MiB`；修复 PR 仍开放（[#15460](https://github.com/unslothai/unsloth/pull/15460)）。
- `main` 上的测试/lint 清洁度：已修复 spoof-safety lint 失败（[#15454](https://github.com/unslothai/unsloth/pull/15454)）和不稳定的 FIFO 准入测试（[#15457](https://github.com/unslothai/unsloth/pull/15457)）。

## 6. 这对应用开发者意味着什么

- **本地调度现在是一等公民。** 定时任务可以指向本地模型或远程 API，因此 cron 风格的 agent 任务不再需要外部编排器 —— 但请注意，可靠性取决于桌面应用保持运行，并且在长时间运行的主机上值得关注磁盘使用问题（[#14487](https://github.com/unslothai/unsloth/issues/14487)）。
- **目前，OpenAI 兼容接口是比 Anthropic 接口更安全的集成路径。** `/v1/messages` 在溢出时不会应用自动压缩（[#15417](https://github.com/unslothai/unsloth/issues/15417)），因此如果你用 Anthropic 风格的 SDK 在前面接入 Unsloth，请在客户端侧规划好上下文预算。
- **验证微调路由。** 在 [#15449](https://github.com/unslothai/unsloth/pull/15449) 合并前，请求一个新微调模型名称可能会静默返回基础模型 —— 这种故障模式看起来像“训练没有生效”。在你的评估框架中校验模型身份。
- **生成式流水线的可复现性正在改善。** 跨进程的种子稳定图像和视频输出（[#15455](https://github.com/unslothai/unsloth/pull/15455)、[#15462](https://github.com/unslothai/unsloth/pull/15462)）在你缓存或对比生成资产时很重要；等这些改动合入后，请固定到相应构建。
- **网关灵活性仍然有限。** 尚不支持为 OpenAI 兼容远程提供方设置自定义请求头（例如 LiteLLM 的 tenant/feature 路由）（[#14811](https://github.com/unslothai/unsloth/issues/14811)），ACP 支持仍是待处理请求（[#15356](https://github.com/unslothai/unsloth/issues/15356)）。如果需要按请求传递元数据，请在前面规划一个代理。
- **混合厂商 GPU 主机实际上不受支持。** 如果你在同一台机器上运行 NVIDIA + AMD，Studio 只会看到 NVIDIA（[#15444](https://github.com/unslothai/unsloth/issues/15444)）；请使用容器化或按厂商隔离。
- **训练可靠性**：一项拟议的 `unsloth dataset check` 预检（[#15332](https://github.com/unslothai/unsloth/issues/15332)）针对两种最常见的静默失败 —— 聊天模板/行不匹配，以及在 `max_seq_length` 处未告知的截断。在该功能出现之前，请在提交任务前验证数据集。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*