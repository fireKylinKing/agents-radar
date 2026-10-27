# AI Infrastructure Digest 2026-10-27

> Generated: 2026-10-27 01:36 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project Comparison Report — AI Infrastructure Digests, 2026-10-27

## 1. Ecosystem Overview

The ecosystem is in a **correctness-hardening phase** rather than a pure throughput race. The most serious items today are not benchmark regressions but **data-isolation and silent-correctness failures**: vLLM’s cross-request KV contamination under PD disaggregation + prefix caching, SGLang’s silent Kimi-K3 MXFP4 activation substitution, and LiteLLM’s spend-counter double-count causing 422 lockouts. At the same time, **KV cache lifecycle and cold-start cost** are becoming primary performance levers: SGLang’s Weight Cache Daemon cut Qwen3-235B FP8 weight load from ~306–327s to <1s, while vLLM, llama.cpp, and Ollama all report cache-related regressions. Hardware fragmentation remains acute across Blackwell/SM120, ROCm, SYCL, Apple MLX, Ascend NPU, and Hexagon. Agentic workloads are now the explicit design target: tool-calling reliability, context compaction, MCP GA, programmable KV sessions, and scheduled local jobs all appeared in today’s activity.

## 2. Activity Comparison

Digest-visible unique issue/PR identifiers, not full tracker totals. Issue counts include open, closed, and referenced issues.

| Project | Layer | Issues cited | PRs cited | Release status (24h) |
|---|---:|---:|---:|---|
| **vLLM** | Server inference engine | 25 | 14 | No release; stacked PP transport refactor in flight |
| **SGLang** | Server inference engine | 22 | 20 | No release; Weight Cache Daemon Phase 1 landed |
| **llama.cpp** | Local/edge runtime | 21 | 24 | 10 builds `b11901`–`b11913` |
| **Ollama** | Local runtime | 21 | 13 | `v0.40.12-rc0` |
| **LiteLLM** | LLM gateway | 13 | 18 | No release; MCP/Anthropic handler GA moves in flight |
| **Unsloth** | Fine-tuning / Studio | 20 | 18 | `v0.1.911-beta` |

**Notable activity:** vLLM has the highest severity item today — a tenant-isolation failure in PD-disaggregated serving. SGLang has the strongest cold-start result but also a critical speculative-decoding crash. llama.cpp is the most build-active project, with 10 builds and broad backend work. LiteLLM is refactoring experimental surfaces to GA. Unsloth is expanding from training into scheduled jobs and generative media.

## 3. Model Support Race

| Project | New models / architectures / hardware support | Position |
|---|---|---|
| **vLLM** | Decision 3.0 `d3-turbo` 27B prefix_bidir attention; Qwen3.8-Flash-Next native x86 CPU on Model Runner V2; ROCm/Qwen4Exp fp8 KV + indexer caches; SM120 DSv4.1 sparse-MLA block-size fix; ASR VAD; ViT CUDA graph tracking; MoE routed-expert capture layouts. Roadmap: GLM-5.3, Kimi K3. | **Broadest production-serving model coverage.** Strong on NVIDIA/AMD/x86 and new attention variants. |
| **SGLang** | DeepSeek V4.1 mHC cleanup; NPU DSpark for DSV4 on Ascend 950; NPU HiCache layer-first transfer for MLA/DSV4; AMD Qwen3.8 hyper-connections on gfx950; Diffusion Q4 roadmap with MiniMax-H3. Apple Silicon still roadmap-only; DGX Spark/SM121 and GGUF I-quant gaps closed inactive. | **Deepest DeepSeek + NPU/AMD depth.** Especially strong for Ascend and disaggregated serving. |
| **llama.cpp** | Laguna MoE amax rescaling; GigaChat 3.5 432B-A28B MoE in flight; DeepSeek-V4.1-Flash in-flight/preview; vision-tower head sizes 40/72 on CUDA; Metal multi-GPU; Hexagon VTCM and `MUL_MAT_ID`; AMD XDNA request open. | **Broadest local/edge hardware reach.** Best for heterogeneous client and workstation backends. |
| **Ollama** | DFlash2 MLX draft support; YaRN RoPE for Qwen3/Qwen3.5; Gemma 4 compact vision towers; MLX/MLX-C prompt-speed bump. DeepSeek-V4.1-Flash downloadable files still a request. | **Apple Silicon UX leader.** Narrower model breadth than vLLM/SGLang/llama.cpp. |
| **LiteLLM** | DashScope realtime WebSocket handler; Vertex image sizing/aspect-ratio mapping; `azure_ai/gpt-image-2` pricing metadata; `context_window` in `/model/info`; known gaps for Scaleway embeddings and Gemini explicit caching with custom `api_base`. | **Provider-abstraction layer, not a model runtime.** Expanding multimodal/realtime provider coverage. |
| **Unsloth** | MiniMax-M3.1-Flash; FLUX-3 image editing; Qwen-Image-2.1 determinism; Wan2.1/Wan2.2/HunyuanVideo-1.5/MiniMax-H3 video paths; SDXL + ControlNet; gpt-oss expert-layout probing; Blackwell fp32 attention reroute. | **Fine-tuning and generative-media leader.** Best coverage for training, LoRA, diffusion, and video workflows. |

**Who is ahead?** By layer: **vLLM** leads breadth of production model serving; **SGLang** leads DeepSeek/NPU/AMD depth and cold-start recovery; **llama.cpp** leads local/edge backend breadth; **Ollama** leads Apple Silicon packaging and UX; **LiteLLM** leads gateway/provider normalization; **Unsloth** leads fine-tuning and generative-media tooling.

## 4. Performance Frontier

Optimization effort is concentrated in five areas:

1. **KV cache and memory lifecycle** — the dominant frontier.
   - vLLM: critical prefix-cache contamination; Programmable KV Cache RFC; KV compression layer RFC.
   - SGLang: Weight Cache Daemon cuts Qwen3-235B FP8 load from ~306–327s to **<1s**; NPU sparse offload raises pool capacity from **26,880 → 99,840 tokens** at unchanged HBM.
   - llama.cpp: unified KV + `-np 2` causes **42–54% prompt-processing drop** from the second long request onward.
   - Ollama: AMD RADV + Vulkan prompt cache shows **~29.7× generation throughput collapse** on the second identical request.
   - LiteLLM: vLLM `cached_tokens` not costed, overstating spend.

2. **Backend-specific kernels.**
   - vLLM: DiffKV prefill tiling gives **3.0× on 200K-context chunks** on SM80; fused conv1d; SM120 sparse-MLA block-size fixes.
   - SGLang: QSA prefill D2D-copy removal; AMD QSA decode fusion via AITER FlyDSL; fused NaN validation; fused RMSNorm for FP32 residuals.
   - llama.cpp: Vulkan RMSNorm subgroup reductions; SYCL bounded reorder scratch and templated MMVQ; Hexagon vectorized zeroing; CPU `concat` single-row kernel.
   - Unsloth: ROCm attention tracing fix; Blackwell fp32 attention reroute; deterministic diffusion/video decode.

3. **Quantization correctness is now as important as quantization speed.**
   - vLLM: NVFP4 garbage output under `torch.compile` when per-rank K is not 32-aligned.
   - SGLang: Kimi-K3 MXFP4 on SM90 silently computes wrong activation; NVFP4 MoE + EP>1 bug closed inactive, not fixed.
   - llama.cpp: `IQ4_NL`/`IQ4_XS` creation gets bit-identical speedup.
   - Unsloth: int8 W8A8 compile memory regression fixed; MiniMax-M3.1 NVFP4 falls back to CPU on Apple Silicon.

4. **Distributed serving and parallelism.**
   - vLLM: pipeline-parallel transport refactor; PD disaggregation isolation issue.
   - SGLang: EP>1 throws away FlashInfer autotune cache every boot; NPU NCCL EP decode; P/D disaggregation on Ascend roadmap.
   - llama.cpp: CUDA tensor-split abort on Qwen3.8-Flash-Next; Metal multi-GPU PR.
   - Unsloth: FSDP2 `reshard_after_forward` and fp16 grad-scaler fixes.

5. **Batching/sampling and scheduling.**
   - vLLM: top-k/topp Triton sampling falls back to full-vocab search on 6–10% of rows; GDN hybrid regression loses **~24% aggregate throughput at c32**.
   - SGLang: FlashInfer autotune cache invalidation under EP>1.
   - llama.cpp: cached-prompt restoration retry under unified KV.

## 5. Layer Positioning

| Layer | Projects | Role |
|---|---|---|
| **Server inference engine** | **vLLM, SGLang** | High-throughput, multi-GPU model serving. Both target PD disaggregation, prefix caching, distributed parallelism, and agentic serving. vLLM is broader across models/backends; SGLang is deeper on DeepSeek, NPU, AMD, and cold-start recovery. |
| **Local / edge runtime** | **llama.cpp, Ollama** | Single-node and desktop inference across CPUs, GPUs, Apple Silicon, Vulkan, SYCL, Hexagon. llama.cpp is the backend breadth layer; Ollama is the packaged UX layer and embeds llama.cpp. |
| **LLM gateway / control plane** | **LiteLLM** | Routing, provider normalization, budgets, rate limits, auth, MCP, model metadata. It sits in front of serving engines and external APIs rather than executing model kernels. |
| **Training / fine-tuning / Studio** | **Unsloth** | LoRA/fine-tuning, FSDP2, quantization-aware training, diffusion/video generation, desktop Studio workflows. It can expose OpenAI-compatible serving after fine-tuning, but its center of gravity is training and generative tooling. |

**Overlap:** LiteLLM can front vLLM/SGLang/Ollama/Unsloth endpoints. Ollama and llama.cpp share runtime lineage. vLLM and SGLang compete directly in production serving, but SGLang’s NPU/DeepSeek specialization and vLLM’s model breadth create different adoption profiles.

## 6. Trend Signals

1. **Agentic serving is the design center.**
   - Tool-calling reliability is now a first-class infra concern: vLLM’s Rust frontend 500s on `auto` parser resolution, Ollama silently drops unrecognized tool calls, SGLang fixes `tool_choice="auto"`, and LiteLLM moves MCP to GA.
   - Context compaction and long-running agents are fragile: Ollama has auto-compaction failures with cloud models; Unsloth’s Anthropic `/v1/messages` overflow does not trigger compaction.
   - **Watch:** tool-call parser coverage, empty-content `200` responses, MCP path migrations, and context-overflow behavior.

2. **Correctness and tenant isolation outrank raw throughput.**
   - vLLM’s PD + prefix-cache contamination is a data-isolation failure, not a perf artifact.
   - SGLang’s silent activation substitution produces fluent but wrong output — the worst agent failure mode.
   - LiteLLM’s spend double-count locks under-budget customers out with 422s.
   - **Watch:** pin versions, health-check non-greedy/speculative paths, and validate outputs rather than trusting HTTP 200.

3. **KV cache is becoming a programmable control plane.**
   - vLLM’s Programmable KV Cache RFC and KV compression RFC.
   - SGLang’s Weight Cache Daemon and NPU HiCache.
   - llama.cpp unified-KV regressions and cached-prompt restoration retry.
   - **Watch:** session lifecycle, cache retention/movement, prefix-cache isolation, and warmup behavior after restart.

4. **Hardware fragmentation is not resolving.**
   - Blackwell SM120 still has per-model workarounds in vLLM and underperformance in SGLang.
   - ROCm has attention correctness issues; SYCL has TDR resets and token-soup bugs; Apple MLX is improving but has NVFP4 CPU fallback; Ascend NPU is progressing but still roadmap-heavy.
   - **Watch:** per-backend qualification matrices; do not assume “GPU supported” means “all kernels correct.”

5. **Cold start and autoscaling economics are improving — selectively.**
   - SGLang’s <1s weight load is a major autoscaling/blue-green enabler.
   - LiteLLM improves DB lifecycle and streaming timeout semantics.
   - Unsloth adds scheduled local/API prompt execution.
   - **Watch:** restart cost, weight-cache coverage, and whether KV/HiCache warmup is included or separate.

6. **Speculative decoding and quantized KV remain fragile.**
   - SGLang: spec decode hard-kills pip-only installs on the first non-greedy request.
   - vLLM: structured output + speculative decoding is not yet distribution-safe.
   - llama.cpp: SYCL draft model causes TDR resets.
   - **Watch:** gate speculative decoding behind a non-greedy health check; avoid enabling it blindly in agent production paths.

**Bottom line for technical decision-makers:** choose serving engines by model/hardware fit and correctness track record, not headline throughput alone. For multi-tenant or agentic production, prioritize vLLM/SGLang cache-isolation fixes, validate SGLang/Unsloth quantized paths per model, and treat LiteLLM’s MCP/GA and budget fixes as gateway-control-plane upgrades. Local-runtime deployments should pin llama.cpp/Ollama builds and test multi-slot and prompt-cache behavior under repeated long requests.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-10-27

## Today's Highlights

No new release landed in the last 24 hours, but the tree is highly active: the Q4 2026 SIG roadmap is open for comment ([#61024](https://github.com/vllm-project/vllm/issues/61024)), and a pipeline-parallel transport refactor is landing as a stacked PR series ([#63897](https://github.com/vllm-project/vllm/pull/63897), [#63898](https://github.com/vllm-project/vllm/pull/63898), [#63899](https://github.com/vllm-project/vllm/pull/63899)). The most consequential item is a **cross-request KV cache contamination** report in PD-disaggregated deployments with automatic prefix caching enabled ([#61160](https://github.com/vllm-project/vllm/issues/61160)) — a correctness/data-isolation issue rather than a pure perf bug.

## Releases & Breaking Changes

No releases published in the last 24 hours. No config/API deprecations announced. Note that the PP transport work introduces new flags (`--pp-group-cls`, per-instance pipeline stage) that are still OPEN and subject to change.

## New Model & Hardware Support

- **Decision 3.0 (`vllm-sr/d3-turbo`, 27B) prefix_bidir attention** — model support PR enabling `vllm serve vllm-sr/d3-turbo` without extra flags ([#62584](https://github.com/vllm-project/vllm/pull/62584)).
- **Qwen3.8-Flash-Next native x86 CPU backend** on Model Runner V2, covering Qwen4Exp, HyperConnection, QSA, PLE and N-gram embedding paths ([#57294](https://github.com/vllm-project/vllm/pull/57294)).
- **ROCm/Qwen4Exp fp8 KV + indexer caches** now enabled on the AMD QSA path, mirroring NVIDIA ([#61529](https://github.com/vllm-project/vllm/pull/61529)).
- **SM120 (RTX PRO 6000 Blackwell) DSv4.1 sparse-MLA** now declares 64-token kernel blocks, fixing block-size negotiation failures with `--block-size 64` ([#60762](https://github.com/vllm-project/vllm/pull/60762)).
- **ASR: integrated Voice Activity Detection** to reduce Whisper-style hallucination on long noise segments ([#46126](https://github.com/vllm-project/vllm/pull/46126)).
- **ViT Full CUDA Graph** tracker for multimodal encoders (Qwen3-VL, GLM-V, Kimi K2.5) remains the top-discussed issue at 45 comments ([#38175](https://github.com/vllm-project/vllm/issues/38175)).
- **MoE routed-expert capture layouts** — backends can now describe physical routing rows instead of replacing the shared capturer ([#62441](https://github.com/vllm-project/vllm/pull/62441)).

## Performance & Optimization

- **DiffKV prefill tiling on SM80**: `BLOCK_M=128` + KV tile 64 yields **3.0× on 200K-context chunks** for GPUs without FA3/FA4 ([#61095](https://github.com/vllm-project/vllm/issues/61095)).
- **GLM-5.3 KDA prefill**: precomputed `chunk_indices` passed into `chunk_kda`, removing a per-call GPU→CPU sync from `prepare_chunk_indices` ([#63072](https://github.com/vllm-project/vllm/pull/63072)). Companion fix fuses q|k|v conv1d into one parameter to stop stale conv reads after RL refit ([#60991](https://github.com/vllm-project/vllm/pull/60991)).
- **Top-k/topp Triton sampling**: monolithic kernel falls back to full-vocab search on 6–10% of rows because its gather pivot is derived from only the first 8192 logits ([#61998](https://github.com/vllm-project/vllm/issues/61998)). A related histogram-bin dropout bug was closed ([#51782](https://github.com/vllm-project/vllm/issues/51782)).
- **GDN hybrid regression**: Qwen3.8-27B (INT4, fp8 KV, TP2) loses **~24% aggregate throughput at c32** when the profiled KV pool drops below ~190K tokens/≤116 blocks; c8 and single-stream are unaffected ([#55580](https://github.com/vllm-project/vllm/issues/55580)).
- **Rust frontend gRPC**: skips detokenization entirely for requests with `output_text=false`, matching Python's `detokenize=False` semantics ([#63789](https://github.com/vllm-project/vllm/pull/63789)).
- **Roadmap items in progress**: GLM-5.3 ([#57406](https://github.com/vllm-project/vllm/issues/57406)), Kimi K3 ([#50587](https://github.com/vllm-project/vllm/issues/50587)), MoE "oracle" unification ([#37753](https://github.com/vllm-project/vllm/issues/37753)), and standardizing KV-cache layouts ([#42082](https://github.com/vllm-project/vllm/issues/42082)).

## Stability & Regressions

Ranked by severity:

1. **Cross-request context contamination (critical, data isolation)** — PD disaggregation + automatic prefix caching (default) on DeepSeek-V4.1-Flash intermittently returns content from *other requests*. Confirmed clean with `--no-enable-prefix-caching`; scriptable reproducer available. No direct fix PR yet; adjacent mitigations are connector-agnostic KV checksums ([#62211](https://github.com/vllm-project/vllm/pull/62211)) and KV-loss reporting ([#63899](https://github.com/vllm-project/vllm/pull/63899)) ([#61160](https://github.com/vllm-project/vllm/issues/61160)).
2. **SM120 CUDA graphs unusable for DSv4.1-Flash** — 8×TP on RTX PRO 6000 forces `--enforce-eager`, producing extremely low decode throughput; graph capture fix requested with priority ([#56892](https://github.com/vllm-project/vllm/issues/56892)). Field-report config for 1M context is available ([#56700](https://github.com/vllm-project/vllm/issues/56700)).
3. **NVFP4 garbage output under `torch.compile`** with per-rank K not 32-aligned (TP8/SM120) — padded block-scale column reads stale memory; eager/Marlin/aligned-TP are correct ([#48898](https://github.com/vllm-project/vllm/issues/48898)).
4. **`IndexError` in logprob construction** returns 500s on `/v1/chat/completions` and `/v1/completions` when `top_logprobs` is shorter than `token_ids` ([#63751](https://github.com/vllm-project/vllm/issues/63751)).
5. **Rust frontend tool calling**: every request 500s when the `auto` tool parser cannot resolve a parser for the model ([#63584](https://github.com/vllm-project/vllm/issues/63584)).
6. **ROCm `ROCM_ATTN`** returns wrong attention and can fault the GPU on RDNA for head size ≥96 with KV block size ≠16, and MHA head 192/256 ([#62697](https://github.com/vllm-project/vllm/issues/62697)).
7. **Structured output**: malformed specs return HTTP 500 with empty error text, and self-referential `$ref` passes validation then kills the request mid-generation ([#57725](https://github.com/vllm-project/vllm/issues/57725)).
8. **P2P KV offload tier does not fail fast** on peer faults ([#63602](https://github.com/vllm-project/vllm/issues/63602)); startup memory-profiling assertion aborts when free memory *grows*, breaking documented GPU co-tenancy ([#56830](https://github.com/vllm-project/vllm/issues/56830)).
9. **Resolved/closed**: `persistent_topk` candidate dropping ([#51782](https://github.com/vllm-project/vllm/issues/51782)), ROCm gfx1030 perf regression ([#38107](https://github.com/vllm-project/vllm/issues/38107)), FlashAttnMLA + fp8 KV fusion test failure ([#46581](https://github.com/vllm-project/vllm/issues/46581)).

## What This Means for Application Developers

- **If you run PD-disaggregated serving, disable prefix caching today.** The crosstalk report ([#61160](https://github.com/vllm-project/vllm/issues/61160)) is a tenant-isolation failure, not just a perf artifact — unacceptable for multi-tenant agents or RAG serving with shared system prompts. Add `--no-enable-prefix-caching` until a fix lands, or keep P/D colocated.
- **SM120 workstation deployments remain a second-class citizen.** DeepSeek-V4.1-Flash, NVFP4, and sparse-MLA paths all require per-model workarounds (eager mode, aligned TP, explicit block sizes). Budget bring-up time and follow the field report ([#56700](https://github.com/vllm-project/vllm/issues/56700)).
- **Rust frontend is not production-ready for tool-calling workloads** — the `auto` parser resolution gap 500s every request ([#63584](https://github.com/vllm-project/vllm/issues/63584)). The engine-client abstraction ([#63887](https://github.com/vllm-project/vllm/pull/63887)) shows investment, but stay on the Python OpenAI server for agents.
- **Agentic serving is the clear design target this cycle.** The Programmable KV Cache RFC ([#57103](https://github.com/vllm-project/vllm/issues/57103)) proposes router-supplied session lifecycle + composable retention/movement policies, and the reusable KV compression layer RFC ([#59538](https://github.com/vllm-project/vllm/issues/59538)) targets transfer/storage cost. Expect the KV connector configuration surface to change materially next quarter; avoid hard-coding connector internals.
- **Structured output + speculative decoding is not yet distribution-safe** — grammar bitmask and draft validation interact in ways that can shift the output distribution ([#62818](https://github.com/vllm-project/vllm/issues/62818)). If you rely on JSON schemas for tool calls, prefer non-speculative decoding or validate outputs defensively.
- **Long-context prefill on Ampere is now materially cheaper** if you pick up the DiffKV tiling work ([#61095](https://github.com/vllm-project/vllm/issues/61095)) — a 3× win on 200K chunks is large enough to change capacity planning for document/agent-context workloads.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-10-27

## 1. Today's Highlights

The **Fast Engine Recovery** roadmap is showing real production numbers: Phase 1 of the Weight Cache Daemon landed in #27139, cutting weight load time on Qwen3-235B FP8 from ~306–327s to under 1s via a per-rank CUDA IPC daemon. Meanwhile the issue tracker took a large housekeeping pass — 16 stale issues were closed in the last 24h (mostly tagged `inactive`), including several NVFP4, Kimi-K3 and ROCm bugs. On the risk side, a **speculative-decoding crash on the first non-greedy request** (#43412) and a **silent activation-function substitution in Kimi-K3 MXFP4 decode** (#45755) are the two items most worth engineering attention today.

---

## 2. Releases & Breaking Changes

**No releases in the last 24 hours.**

No API or config breaks landed. Build-affecting change in flight: [PR #46519](https://github.com/sgl-project/sglang/pull/46519) moves `sglang-kernel-xpu` to version-pinned resolution via `uv` — relevant only to XPU builders, but it changes how the XPU kernel wheel is selected.

---

## 3. New Model & Hardware Support

- **Apple Silicon (roadmap, Q2):** [Issue #19137](https://github.com/sgl-project/sglang/issues/19137) — SGLang still has no Apple Silicon support; maintainers are soliciting contributors for the initial port. 30 comments, no code yet.
- **NPU (2026 H2 roadmap):** [Issue #25598](https://github.com/sgl-project/sglang/issues/25598) — target is full compatibility for P/D disaggregation, all parallelism modes, speculative decoding, hierarchical cache and load balancing on Ascend.
- **DeepSeek V4.1:** [Issue #42170](https://github.com/sgl-project/sglang/issues/42170) — mHC cleanup done; `q_rope_store` folded into `fused_q_norm_rope` (#41657); remaining work is prefill-side mHC SP + engram fusion ([#43065](https://github.com/sgl-project/sglang/pull/43065)).
- **NPU DSpark for DSV4 on Ascend 950:** [PR #39947](https://github.com/sgl-project/sglang/pull/39947) adds speculative decoding support on the Ascend 950 backend.
- **NPU HiCache layer-first host transfer:** [PR #46604](https://github.com/sgl-project/sglang/pull/46604) resolves `layer_first_kv_split` layout for MLA / DeepSeek V4, with Memfabric-backed host all-to-all.
- **AMD Qwen3.8 hyper-connections:** [PR #41880](https://github.com/sgl-project/sglang/pull/41880) — AITER FlyDSL `hc_mix` for gfx950 (depends on ROCm/aiter#5993, gated on importability).
- **Diffusion Q4 roadmap:** [Issue #43313](https://github.com/sgl-project/sglang/issues/43313) — MiniMax-H3 as the stress test, plus validated GB200/GB300 recipes and a lossy "high" quality tier across Blackwell.
- **Hardware gap — DGX Spark / SM121:** [Issue #36558](https://github.com/sgl-project/sglang/issues/36558) closed `inactive`; QSA decode still has no kernel path on GB10 because `_resolve_trtllm_sparse_decode()` rejects SM121 via the `is_sm100_supported()` gate.
- **Hardware gap — GGUF I-quant:** [Issue #35019](https://github.com/sgl-project/sglang/issues/35019) closed `inactive`; no MMQ kernel for `IQ1_*`–`IQ4_*`, so those checkpoints stay on the slow path.

---

## 4. Performance & Optimization

- **Weight cache daemon — the headline number:** [Issue #33522](https://github.com/sgl-project/sglang/issues/33522). Post-quantized weights held by a per-rank daemon and served over CUDA IPC; weight load on Qwen3-235B FP8 drops from **306–327s → <1s**. Phase 1 shipped in #27139; the roadmap now covers daemon lifecycle and recovery semantics. This is the single biggest lever on cold-start/restart time in the tracker.
- **FlashInfer autotune cache thrown away every boot under EP>1:** [Issue #40320](https://github.com/sgl-project/sglang/issues/40320). `_drop_diverged_autotune_cache()` discards the entire cache whenever per-rank MoE GEMM shapes differ — i.e. always, with expert parallelism. The fused-MoE tactic is re-tuned on every start. No fix PR yet; the obvious direction is per-rank cache namespacing rather than global invalidation.
- **DeepSeek-V4 prefill on SM120 underperforming:** [Issue #33422](https://github.com/sgl-project/sglang/issues/33422) reports **~2–7K tok/s vs ~12.5K** with vLLM/marlin on 4× RTX PRO 6000 (TP=4). Still open, no root cause; likely kernel coverage on SM120 rather than a config issue.
- **QSA prefill D2D copy removal:** [PR #45753](https://github.com/sgl-project/sglang/pull/45753) eliminates a device-to-device copy in prefill KV gather for quantized sparse attention.
- **AMD QSA decode fusion:** [PR #45196](https://github.com/sgl-project/sglang/pull/45196) replaces the two-pass Triton gather + per-(row, KV-head) decode (up to 2051 selected positions per row) with AITER's fused FlyDSL sparse decode on gfx950.
- **Fused NaN validation:** [PR #43720](https://github.com/sgl-project/sglang/pull/43720) collapses separate `isnan` / reduction / negation / device-assert launches into one read-only Triton kernel — cuts overhead on logits and probability checks, including non-contiguous vocab slices.
- **Fused RMSNorm for FP32 residuals:** [PR #46622](https://github.com/sgl-project/sglang/pull/46622) (Muse Spark) promotes effective gain to FP32 to hit PyTorch's fused path.
- **NPU NCCL EP decode:** [PR #39546](https://github.com/sgl-project/sglang/pull/39546) closed — single compute stream + two independent comm streams for TBO decode.
- **NPU KV/indexer budgeting:** [PR #42076](https://github.com/sgl-project/sglang/pull/42076) closed — sizes pools by actual backend byte rates (1,024B BF16 rows pre-A5 vs 640B aligned FP8 on A5) rather than a flat 584B/132B assumption.
- **NPU sparse offload budgeting:** [PR #45302](https://github.com/sgl-project/sglang/pull/45302) — with a 512 MiB index budget, pool capacity rises **26,880 → 99,840 tokens** at unchanged HBM cost.

---

## 5. Stability & Regressions

Ranked by severity:

1. **[Critical] Speculative decoding kills the server on first sampled request.** [Issue #43412](https://github.com/sgl-project/sglang/issues/43412) — on pip-installed bundles with no system CUDA toolkit, the FlashInfer verify-sampling op JIT-fails (bundled cccl vs pip cu13 toolkit) and SIGKILLs the whole process tree. Greedy/temperature-0 traffic is fine; any `temperature > 0` dies. No fix PR. Workaround: disable spec decode, or install a matching system CUDA toolkit.
2. **[Critical, silent] Kimi-K3 on SM90 computes the wrong activation.** [Issue #45755](https://github.com/sgl-project/sglang/issues/45755) — `flashinfer_mxfp4` silently evaluates GPT-OSS SwiGLU instead of SiTU. Confirmed on v0.5.22 and `main@ce66b3ab`. Blocked on flashinfer-ai/flashinfer#4209 (still open); a prior attempt (#32650) was closed unmerged. **Treat Kimi-K3 MXFP4 output on SM90 as unverified.**
3. **[High] Prefill breakable CUDA graph produces wrong greedy output / IMA.** [Issue #37606](https://github.com/sgl-project/sglang/issues/37606) — `breakable_cuda_graph.py` retains graph-break inputs only as weak refs; the shared mempool pins the pool but not the blocks, so once PyTorch frees the block the graph reads stale memory. Validation of #37448 is the fix path.
4. **[High] Non-causal sliding-window attention ignored.** [Issue #39300](https://github.com/sgl-project/sglang/issues/39300) — `TorchNativeAttnBackend` drops the non-causal SWA path. Affects correctness for bidirectional/SWA-hybrid models on the native backend.
5. **[Medium] DSpark draft query width wrong on FA3/FA4 with `sample_from_anchor=False`.** [Issue #46476](https://github.com/sgl-project/sglang/issues/46476) — new, no fix PR.
6. **[Medium] DeepSeek V4 / V4.1 request rendering returns 400s.** [PR #46706](https://github.com/sgl-project/sglang/pull/46706) fixes null message content, trailing tool-call turns, and numeric-string `effort` values against `deepseek-recipe` (regression from #45315). Fix is open.
7. **[Medium] Diffusion quantization: 11 correctness bugs.** [PR #46319](https://github.com/sgl-project/sglang/pull/46319) fixes issues across MXFP4 dual-scale, FP8, KitchenW4A4 and Nunchaku paths in `multimodal_gen/runtime/layers/quantization/`.
8. **[Low / closed `inactive`] Stale-issue sweep.** 16 issues closed in 24h, notably: Qwen3.8 DSpark forced-reject not lossless — accumulated GDN state drift ([#35150](https://github.com/sgl-project/sglang/issues/35150)); NVFP4 MoE + EP>1 input-scale shape mismatch ([#36597](https://github.com/sgl-project/sglang/issues/36597)); `deepseek_nextn` hardcoding `quant_config=None` for `modelopt_fp4` ([#36599](https://github.com/sgl-project/sglang/issues/36599)); `is_layer_excluded` missing fused/`model.`-prefixed names ([#36596](https://github.com/sgl-project/sglang/issues/36596)); Qwen3.5 MTP weights silently dropped on compressed-tensors checkpoints ([#35797](https://github.com/sgl-project/sglang/issues/35797)); CPU `topk_sigmoid_cpu` OOB read ([#36600](https://github.com/sgl-project/sglang/issues/36600)); ROCm 7.2.0 Triton compile failure in `store_cache_4d` ([#29864](https://github.com/sgl-project/sglang/issues/29864)); Dynamic LoRA broken with `--tokenizer-worker-num > 1` ([#31084](https://github.com/sgl-project/sglang/issues/31084)). These are closed as stale, **not fixed** — do not read the closure as a resolution.

---

## 6. What This Means for Application Developers

- **Restart cost is about to stop being your problem — but only for weight loading.** The Weight Cache Daemon takes cold start from ~5 minutes to sub-second on large FP8 models. If you run autoscaling or blue/green rollouts on 200B+ class models, this is the feature to track; note that Phase 1 covers post-quantized weights only, and KV/HiCache warmup is a separate path.
- **Speculative decoding is not safe to enable blindly on pip-only installs.** If your image has no system CUDA toolkit, spec decode will hard-kill the process on the first sampled request ([#43412](https://github.com/sgl-project/sglang/issues/43412)). Gate speculative decoding behind a health check that fires a `temperature=0.7` request before taking traffic, or pin a CUDA toolkit into the image.
- **Validate Kimi-K3 MXFP4 output on SM90 before trusting it.** A silent activation-function substitution ([#45755](https://github.com/sgl-project/sglang/issues/45755)) means you get fluent, well-formed, wrong answers — the worst failure mode for agents. Pin to an older image or use a non-MXFP4 checkpoint for that model/hardware pair.
- **EP>1 deployments pay a tuning tax on every boot.** Because the FlashInfer autotune cache is dropped whenever per-rank shapes diverge ([#40320](https://github.com/sgl-project/sglang/issues/40320)), your first requests after each restart hit an un-warmed fused-MoE path. Budget a warmup burst rather than routing user traffic immediately.
- **Tool-calling reliability is improving.** [PR #45272](https://github.com/sgl-project/sglang/pull/45272) stops `tool_choice="auto"` from constraining every tool when only one tool is `strict: true`, and [PR #46706](https://github.com/sgl-project/sglang/pull/46706) fixes DeepSeek V4/V4.1 rejection of null content and trailing tool-call turns — both directly affect agent loop stability.
- **Watch the closed-but-unfixed list.** Several correctness bugs (NVFP4 MoE with EP>1, Qwen3.5 MTP weight loading, dynamic LoRA across tokenizer workers) were closed as `inactive` rather than repaired. If your stack touches those code paths, treat them as known-broken and validate locally.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-10-27

## 1. Today's Highlights

A dense 24 hours of backend work landed, led by Hexagon (VTCM sizing, `MUL_MAT_ID` row-skipping), a type-generic CPU `concat` kernel, Vulkan RMSNorm subgroup reductions, and SYCL launch/memory tuning. Correctness fixes also shipped for OpenCL KV-cache state restore and Laguna MoE overflow on Metal. Meanwhile, issue traffic is dominated by unified-KV prompt-processing regressions, SYCL device/output failures, and continued maturation of `llama-server` router/tool-registry features.

## 2. Releases & Breaking Changes

Ten builds published (`b11901`–`b11913`; `b11900`, `b11902`, `b11903`, `b11911` not present in the feed).

| Build | Change | Link |
|---|---|---|
| b11913 | Hexagon: new `GGML_HEXAGON_VTCM_SIZE` build/runtime knob (renamed from `VTCM_MB`; `NTHREADS` cap dropped) | [#31221](https://github.com/ggml-org/llama.cpp/pull/31221) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11913) |
| b11912 | Laguna: MoE amax f16 rescaling enabled (fixes Metal overflow at prefill microbatch ≥ 32) | [#31147](https://github.com/ggml-org/llama.cpp/pull/31147) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11912) |
| b11910 | OpenCL: KV-cache views written in `set_tensor` so state restore is no longer silently dropped | [#30471](https://github.com/ggml-org/llama.cpp/pull/30471) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11910) |
| b11909 | Hexagon: `MUL_MAT_ID` skipped-row zeroing vectorized, added to fused `MM_ID_NX` | [#31169](https://github.com/ggml-org/llama.cpp/pull/31169) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11909) |
| b11908 | `no_alloc` moved out of `llama_hparams` (internal struct layout change) | [#31141](https://github.com/ggml-org/llama.cpp/pull/31141) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11908) |
| b11907 | Model graph builders now return output structs instead of `ggml_tensor**` out-params | [#31142](https://github.com/ggml-org/llama.cpp/pull/31142) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11907) |
| b11906 | CPU `concat` rewritten as a single row kernel covering all types (memcpy fast path, quantized included) | [#31179](https://github.com/ggml-org/llama.cpp/pull/31179) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11906) |
| b11905 | Vulkan RMSNorm optimized with subgroup reductions (with AMD vendor path and fast path) | [#29882](https://github.com/ggml-org/llama.cpp/pull/29882) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11905) |
| b11904 | SYCL plain MMVQ launch triples templated | [#31013](https://github.com/ggml-org/llama.cpp/pull/31013) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11904) |
| b11901 | SYCL reorder temp memory bounded to 32 MiB | [#31062](https://github.com/ggml-org/llama.cpp/pull/31062) · [release](https://github.com/ggml-org/llama.cpp/releases/tag/b11901) |

**Notes for integrators:** `b11907`/`b11908` are internal C++ API changes — out-of-tree model implementations or forks touching graph builders/hparams need a rebase. `b11910` changes behavior (OpenCL state restore now actually applies), so validate session save/restore paths. A follow-up cleanup removing obsolete `n_pad` from memory instances is open in [#31200](https://github.com/ggml-org/llama.cpp/pull/31200).

## 3. New Model & Hardware Support

- **Laguna (MoE)** — amax rescaling extended to `LLM_ARCH_LAGUNA`, matching Mistral4/Mellum/Flint; lands in `b11912` ([#31147](https://github.com/ggml-org/llama.cpp/pull/31147)).
- **GigaChat 3.5 (432B-A28B MoE)** — model/conversion/testing PR open; DeepSeek-V3-style backbone (MLA + DeepSeek MoE) with hybrid attention ([#25342](https://github.com/ggml-org/llama.cpp/pull/25342)).
- **DeepSeek-V4.1-Flash** — model PR `#30817` referenced by issue `#30944`; a merged DSV4.1 flash PR is also in the feed ([#31086](https://github.com/ggml-org/llama.cpp/pull/31086)). Treat as in-flight/preview.
- **Vision-tower head sizes 40/72 on CUDA** — previously excluded from all tensor-core FA dispatch (Gemma 3/4, Qwen3-VL vision towers), now padded to the next multiple of 16 in MMA fragments ([#30285](https://github.com/ggml-org/llama.cpp/pull/30285)).
- **Metal multi-GPU** — one backend device per physical GPU plus cross-device copy guard (fixes Intel Mac + eGPU selection); PR open ([#28568](https://github.com/ggml-org/llama.cpp/pull/28568)).
- **Hexagon** — VTCM size configurability and `MUL_MAT_ID` improvements landed ([#31221](https://github.com/ggml-org/llama.cpp/pull/31221), [#31169](https://github.com/ggml-org/llama.cpp/pull/31169)).
- **AMD XDNA** — backend request remains open with strong support (39 👍) ([#21725](https://github.com/ggml-org/llama.cpp/issues/21725)).

## 4. Performance & Optimization

**Landed**
- **Vulkan RMSNorm** via subgroup reductions, including an AMD-specific path ([#29882](https://github.com/ggml-org/llama.cpp/pull/29882)).
- **SYCL**: bounded reorder scratch to 32 MiB ([#31062](https://github.com/ggml-org/llama.cpp/pull/31062)); templated MMVQ launch triples ([#31013](https://github.com/ggml-org/llama.cpp/pull/31013)).
- **Hexagon**: vectorized skipped-row zeroing and scalar-div removal in `MUL_MAT_ID` ([#31169](https://github.com/ggml-org/llama.cpp/pull/31169)).
- **CPU `concat`**: one row kernel for all types, memcpy fast path for dense and quantized rows ([#31179](https://github.com/ggml-org/llama.cpp/pull/31179)).

**In progress / reported numbers**
- **SYCL `ggml_sycl_comm_exchange()`**: reported **+16.7% to +20.6% (mean ≈ +19%) prompt-eval t/s** with `-sm tensor` on dual Arc Pro B70, zero PPL change ([#30956](https://github.com/ggml-org/llama.cpp/pull/30956)).
- **Vulkan CM1 attention**: softmax P stored row-major and fragments reused across V tiles; updated shared-memory estimate for all CM1 devices ([#31197](https://github.com/ggml-org/llama.cpp/pull/31197)).
- **Vulkan Q4_0 KV cache in FA**: dequant+transpose fast path targeting very slow quantized-KV behavior on GCN1/D700 ([#30738](https://github.com/ggml-org/llama.cpp/pull/30738)).
- **Vulkan Intel FA prefill shader** plus consolidated push-constant definitions for prefill/decode ([#29357](https://github.com/ggml-org/llama.cpp/pull/29357)).
- **CPU large single-row ops**: dynamic reshape/splitting when `nrows < nth` and row size exceeds a threshold ([#31218](https://github.com/ggml-org/llama.cpp/pull/31218)).
- **Quantization tooling**: bit-identical speedup for `IQ4_NL`/`IQ4_XS` creation by precomputing 15 halfway points instead of binary search ([#31201](https://github.com/ggml-org/llama.cpp/pull/31201)).
- **Vulkan coopmat1 int8 matmul**: clamp A prefetch to `end_k` (fix for [#29342](https://github.com/ggml-org/llama.cpp/issues/29342)) ([#30283](https://github.com/ggml-org/llama.cpp/pull/30283)).
- **Server KV reuse**: retry cached-prompt restoration after clearing idle slots under unified KV, avoiding full reprocessing ([#30997](https://github.com/ggml-org/llama.cpp/pull/30997)).

## 5. Stability & Regressions

Ranked by severity:

1. **SYCL GPU driver TDR reset** — loading a DFlash2 draft model (`--model-draft`) on dual Arc Pro B70 causes `VIDEO_TDR_TIMEOUT_DETECTED`; process dies with no application-level error. Open, no fix PR ([#28778](https://github.com/ggml-org/llama.cpp/issues/28778)).
2. **Unified KV prompt-processing regression** — with `-np 2` and `--kv-unified`, PP drops **42–54%** from the second long request onward. Root cause identified: CUDA/HIP FA kernels skip only mask tails (`KV_max`), not interior all-`-INF` blocks, so sequences pay for cells belonging to other slots. Open ([#28495](https://github.com/ggml-org/llama.cpp/issues/28495)).
3. **SYCL DeltaNet token soup** on long prompts (Qwen3.6, Qwen3.8, Nemotron) on Arc Pro B70. Open ([#29183](https://github.com/ggml-org/llama.cpp/issues/29183)).
4. **SYCL bad output on Qwen3.6 35B A3B** (B580). Open ([#28728](https://github.com/ggml-org/llama.cpp/issues/28728)).
5. **Vulkan SWA abort** — `GGML_ABORT cache_k_l0` regression in 2.40/2.41 vs 2.39 on hybrid/SWA models, 2× Arc Pro B50. Open ([#29082](https://github.com/ggml-org/llama.cpp/issues/29082)).
6. **DeepSeek-V4.1-Flash prefix corruption** — single-token output corruption when a cached prompt prefix is reused (Engram n-gram history), reproducible with both `--lazy-mode` settings; against the `deepseek41` branch, not master ([#30944](https://github.com/ggml-org/llama.cpp/issues/30944)).
7. **Vulkan/RADV Radeon 680M** — `--load-mode auto` causes device loss/OOM on large Gemma; `--load-mode mmap` works around it. Open ([#27360](https://github.com/ggml-org/llama.cpp/issues/27360)).
8. **CUDA tensor split abort** — Qwen3.8-Flash-Next with `--split-mode tensor` aborts with `SPLIT_AXIS_UNKNOWN` on 2× RTX 5090. Open ([#27964](https://github.com/ggml-org/llama.cpp/issues/27964)).
9. **qwen4exp assert with merged tensors** from `#31028` on Vulkan / Ryzen AI Max+ 395. New, open ([#31189](https://github.com/ggml-org/llama.cpp/issues/31189)).
10. **Security: prompt injection via WebUI `?q=` query parameter** — unauthenticated injection vector in `llama-server` UI. Open, tagged security ([#25790](https://github.com/ggml-org/llama.cpp/issues/25790)).
11. **Vulkan device lost** on RX 6900 XT ([#27076](https://github.com/ggml-org/llama.cpp/issues/27076)); **slow token gen with Resizable BAR disabled** on AMD dGPU ([#27097](https://github.com/ggml-org/llama.cpp/issues/27097)).

**Fixed / closed today:** OpenCL KV state restore ([#30471](https://github.com/ggml-org/llama.cpp/pull/30471), shipped in `b11910`); Laguna MoE overflow ([#31147](https://github.com/ggml-org/llama.cpp/pull/31147), `b11912`); Vulkan RDNA3 PP regression after `b10780` ([#28752](https://github.com/ggml-org/llama.cpp/issues/28752), closed); SYCL `-sm tensor` memory-leak report on dual B70 ([#27845](https://github.com/ggml-org/llama.cpp/issues/27845), closed); ngram-cache speculation cache bleed across requests, acceptance **86% → 11%** ([#27852](https://github.com/ggml-org/llama.cpp/issues/27852), closed).

## 6. What This Means for Application Developers

- **Server-side features are consolidating.** Several tracking issues closed today point to a more capable `llama-server`: a server-side tool registry enabling MCP/tools in `llama-cli` as well as the WebUI ([#20673](https://github.com/ggml-org/llama.cpp/issues/20673)), a model management API (`GET /models?reload=1`, `POST /models/download`) ([#21779](https://github.com/ggml-org/llama.cpp/issues/21779)), multiple presets per loaded model in router mode ([#23704](https://github.com/ggml-org/llama.cpp/issues/23704)), and persisted MCP server activation across restarts ([#25349](https://github.com/ggml-org/llama.cpp/issues/25349)). Plan for these APIs but pin builds until they stabilize.
- **Multi-slot serving still has sharp edges.** The `-np 2` + `--kv-unified` prompt-processing drop ([#28495](https://github.com/ggml-org/llama.cpp/issues/28495)) is a real capacity-planning hazard: throughput degrades on subsequent long requests, not the first. If you run concurrent long-context workloads, benchmark per-request-after-the-first, or avoid unified KV for now. The cached-prompt restoration retry PR ([#30997](https://github.com/ggml-org/llama.cpp/pull/30997)) directly reduces reprocessing cost in this mode.
- **SYCL (Intel Arc) needs caution for speculative decoding and hybrid models.** TDR resets with draft models ([#28778](https://github.com/ggml-org/llama.cpp/issues/28778)) and DeltaNet token soup on long prompts ([#29183](https://github.com/ggml-org/llama.cpp/issues/29183)) are unresolved. Meanwhile `-sm tensor` throughput is improving (~+19% prompt eval reported in [#30956](https://github.com/ggml-org/llama.cpp/pull/30956)).
- **Session/state persistence is safer on OpenCL** after `b11910`; re-test any save/restore or snapshot-based warm-start logic on that backend.
- **Quantization pipelines get cheaper**: `IQ4_NL`/`IQ4_XS` creation is faster with bit-identical output ([#31201](https://github.com/ggml-org/llama.cpp/pull/31201)) — safe to adopt in CI tooling once merged.
- **For agent frameworks**, the open `?q=` injection issue ([#25790](https://github.com/ggml-org/llama.cpp/issues/25790)) is a reminder that the built-in WebUI should not be exposed beyond localhost without a reverse proxy and auth.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-10-27

## 1. Today's Highlights

Ollama published **v0.40.12-rc0**, focused on the MLX/Apple Silicon path: faster prompt processing for Gemma/Flint/Laguna, DFlash2 draft support, and better small-image fidelity in Gemma 4 vision towers. On the stability side, an AMD Vulkan prompt-cache regression causing a ~30× generation slowdown remains open, while `ollama launch opencode` is broken against OpenCode v2 with multiple fix PRs now in flight. Tool-call parsing also received attention, including preservation of unrecognized tool calls and a Gemma 4 unclosed-list parser repair.

## 2. Releases & Breaking Changes

- **v0.40.12-rc0** (release candidate) — MLX-focused changes:
  - Preserve small-image detail for compact Gemma 4 vision towers — [PR #19247](https://github.com/ollama/ollama/pull/19247)
  - MLX/MLX-C version bump for prompt-speed improvements in Gemma/Flint/Laguna — [PR #19291](https://github.com/ollama/ollama/pull/19291)
  - Add DFlash2 support in MLX — [PR #19149](https://github.com/ollama/ollama/pull/19149)
- **Upstream llama.cpp bump to b11901** — closed in this window; routine runtime sync, no API/migration impact indicated — [PR #19311](https://github.com/ollama/ollama/pull/19311)
- **No Ollama API or config breaking changes** are indicated in the provided data. Note that the OpenCode v2 launch failures are caused by OpenCode CLI flag changes, not an Ollama API change; fixes are open in [PR #19310](https://github.com/ollama/ollama/pull/19310), [PR #19307](https://github.com/ollama/ollama/pull/19307), and [PR #19298](https://github.com/ollama/ollama/pull/19298).

## 3. New Model & Hardware Support

- **DFlash2 speculative/draft support for MLX** — extends the DFlash block draft with block-local dynamic convolutions and a candidate selector; relevant for Apple Silicon inference — [PR #19149](https://github.com/ollama/ollama/pull/19149)
- **YaRN RoPE scaling for Qwen3 and Qwen3.5** — honors explicit RoPE scaling metadata across supported config schemas, applied to text and mRoPE while preserving non-rotary dimensions — [PR #19231](https://github.com/ollama/ollama/pull/19231)
- **Gemma 4 vision towers** — improved small-image detail preservation in compact MLX vision configurations — [PR #19247](https://github.com/ollama/ollama/pull/19247)
- **MLX / MLX-C runtime bump** — new window-size capability leveraged for prompt-speed gains — [PR #19291](https://github.com/ollama/ollama/pull/19291)

Requests not yet landed:
- Korean language support for `phonon:40m-multilingual` TTS — [Issue #19295](https://github.com/ollama/ollama/issues/19295)
- Native HarmonyOS PC support, starting with CPU inference — [Issue #19294](https://github.com/ollama/ollama/issues/19294)
- macOS 12/13 support for Intel Macs (community build of v0.40.11 offered) — [Issue #19308](https://github.com/ollama/ollama/issues/19308)
- Downloadable `deepseek-v4.1-flash` model files — [Issue #18379](https://github.com/ollama/ollama/issues/18379)

## 4. Performance & Optimization

- **MLX prompt-speed improvements** for Gemma, Flint, and Laguna via the MLX/MLX-C version bump; exact benchmark numbers were not included in the provided notes — [PR #19291](https://github.com/ollama/ollama/pull/19291)
- **DFlash2 draft path** in MLX is an inference-speed feature; expected to improve generation throughput on supported Apple Silicon hardware — [PR #19149](https://github.com/ollama/ollama/pull/19149)
- **YaRN scaling for Qwen3/Qwen3.5** improves long-context behavior and prefill/decode correctness under extended context configurations — [PR #19231](https://github.com/ollama/ollama/pull/19231)
- **Regression to watch:** AMD RADV + Vulkan with prompt cache enabled shows a reproducible **~29.7× generation throughput collapse** on the second identical request (RX 6600 XT, Ollama 0.40.8) — [Issue #19201](https://github.com/ollama/ollama/issues/19201)
- **Idle power regression:** AMD Strix Halo (gfx1151) pins the GPU at max clock (~25 W) when two or more runner processes are resident, even with zero in-flight requests — [Issue #17958](https://github.com/ollama/ollama/issues/17958)
- **VRAM estimation underreporting** with large context windows (reported ~10 GB used for a 262k-context Qwen2.5 7B) — [Issue #10128](https://github.com/ollama/ollama/issues/10128)

## 5. Stability & Regressions

Ranked by severity:

**High**
- **AMD Vulkan prompt-cache slowdown (~30×)** — second identical request collapses generation throughput with host-memory prompt cache enabled; fully GPU-offloaded model. No fix PR visible in this window — [Issue #19201](https://github.com/ollama/ollama/issues/19201)
- **macOS Metal crash** — `mlx runner failed: panic: mlx: [METAL] Command buffer execution failed` returning HTTP 500 on Ollama 0.40.9; no fix PR visible — [Issue #19278](https://github.com/ollama/ollama/issues/19278)
- **`/api/chat` 500 after long streamed replies** — `llama-server apply-template error … write: broken pipe`, reproducible on 0.40.8 and 0.40.11 on macOS; retry succeeds. No fix PR visible — [Issue #19293](https://github.com/ollama/ollama/issues/19293)
- **Windows connection resets after ~15 seconds with large prompts** — `/api/chat` resets on Windows with RTX 3090; marked needs-more-info — [Issue #19139](https://github.com/ollama/ollama/issues/19139)

**Medium**
- **GPT-OSS 20B empty responses on AMD Radeon Pro 5700 XT (Vulkan)** while CPU inference works; marked needs-more-info — [Issue #19060](https://github.com/ollama/ollama/issues/19060)
- **Claude Code + cloud model connection refused** on Windows 11 / Ollama 0.40.11 — [Issue #19281](https://github.com/ollama/ollama/issues/19281)
- **`ollama launch opencode` broken on OpenCode v2** — two distinct failures: `--model` flag rejected on bare command, and `opencode debug config` now returns an array instead of an object. Fix PRs are open — [Issue #19296](https://github.com/ollama/ollama/issues/19296), [Issue #19303](https://github.com/ollama/ollama/issues/19303), [PR #19310](https://github.com/ollama/ollama/pull/19310), [PR #19307](https://github.com/ollama/ollama/pull/19307), [PR #19298](https://github.com/ollama/ollama/pull/19298)
- **Unrecognized tool calls silently dropped** — parser buffers tagged calls for functions absent from the request tool list, but `Content()` returns empty for non-JSON tags, yielding a successful response with neither content nor tool calls. Fix PR open — [Issue #19187 referenced by PR #19240](https://github.com/ollama/ollama/pull/19240)
- **Gemma 4 unclosed list in tool-call arguments** — `gemma4:e4b` omits `]`/`}` on long-string edits; parser repair fails and the call is logged rather than executed. Fix PR open — [PR #19274](https://github.com/ollama/ollama/pull/19274)
- **Kimi CLI tool-only assistant turns** — `content: []` not handled like `null`/empty string; fix PR closed in this window — [PR #19312](https://github.com/ollama/ollama/pull/19312)

**Lower severity / correctness**
- **ShieldGemma template mismatch** — library template asks whether “an AI response” violates policy when classifying a user prompt, across 2b/9b/27b tags — [Issue #19313](https://github.com/ollama/ollama/issues/19313)
- **Ornith 35B renderer/parser emits raw special tokens and unparsed tool-call XML** in Claude Code / OpenAI-compatible clients — [Issue #16992](https://github.com/ollama/ollama/issues/16992)
- **Claude Code auto-compaction failure** with `deepseek-v4.1-flash:1017-cloud`; long-running `/goal` loops exhaust context without compacting — [Issue #19249](https://github.com/ollama/ollama/issues/19249)
- **Compaction body-recovery work in progress** — preserve retained images and tool pairing during 413 recovery; related PRs open/closed — [PR #19309](https://github.com/ollama/ollama/pull/19309), [PR #19304](https://github.com/ollama/ollama/pull/19304)
- **Feature request: disable CPU fallback on insufficient VRAM** — highest-demand open request in this set (22 👍) — [Issue #10707](https://github.com/ollama/ollama/issues/10707)
- **Expose `renderer`, `parser`, `template_source` in the ollama.com model library** — CLI `ollama show` already surfaces them — [Issue #19306](https://github.com/ollama/ollama/issues/19306)
- **GPU acceleration detection/configuration in desktop UI** — Intel Arc iGPU/Vulkan users lack clear status — [Issue #19301](https://github.com/ollama/ollama/issues/19301)

## 6. What This Means for Application Developers

- **Apple Silicon inference is the active optimization target.** v0.40.12-rc0 brings prompt-speed gains and DFlash2 support via MLX. If you serve Gemma/Flint/Laguna on Mac, the RC is worth benchmarking, but treat it as an RC for production.
- **Avoid enabling prompt cache on AMD RADV/Vulkan** until the ~30× slowdown in [Issue #19201](https://github.com/ollama/ollama/issues/19201) is resolved; pin a known-good version or switch backends for latency-sensitive workloads.
- **Harden tool-calling clients against empty-content responses.** Until [PR #19240](https://github.com/ollama/ollama/pull/19240) lands, models emitting calls to undeclared functions can produce a `200` with neither `content` nor `tool_calls`. Treat that combination as a retryable anomaly rather than a valid turn.
- **Pin OpenCode v1 or defer upgrades to OpenCode v2.** `ollama launch opencode` currently fails on v2; the `--standalone` / inline-config fixes in [PR #19310](https://github.com/ollama/ollama/pull/19310) and [PR #19307](https://github.com/ollama/ollama/pull/19307) are the ones to track.
- **Long-running agents should account for compaction edge cases.** Auto-compaction with cloud models (e.g., `deepseek-v4.1-flash`) and image/tool-pair preservation during 413 recovery are still being stabilized — see [Issue #19249](https://github.com/ollama/ollama/issues/19249) and [PR #19309](https://github.com/ollama/ollama/pull/19309).
- **Windows + large prompts remains fragile** (~15 s reset in [Issue #19139](https://github.com/ollama/ollama/issues/19139)). Keep prompts bounded, enable client-side retries with idempotent request IDs, and prefer streamed responses with resumable state.
- **Qwen3/Qwen3.5 long-context users** should watch for YaRN metadata support in [PR #19231](https://github.com/ollama/ollama/pull/19231), which improves correctness for extended-context deployments.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-10-27

## 1. Today's Highlights

No releases shipped in the last 24 hours, but the project is in the middle of a broad "graduate from experimental" refactor: MCP server/client, the Anthropic `/v1/messages` handler, and the Admin UI bundle are all being moved to GA paths with backward-compatible shims ([#48513](https://github.com/BerriAI/litellm/pull/48513), [#48510](https://github.com/BerriAI/litellm/pull/48510), [#48512](https://github.com/BerriAI/litellm/pull/48512)). On the correctness side, a high-risk spend-counter double-count that can lock budgeted customers out with 422s has a fix in review ([#47676](https://github.com/BerriAI/litellm/pull/47676)), alongside fixes for Anthropic thinking-mode 400s and media loss in Responses/Gemini conversions. Cost-map metadata is also gaining a `context_window` field exposed through `/model/info` ([#48516](https://github.com/BerriAI/litellm/pull/48516)).

## 2. Releases & Breaking Changes

**No new releases in the last 24h.**

In-flight changes worth tracking for upgrade planning:

- **Cost map schema addition** — `context_window` added per model and returned from `/model/info`; seeded on 164 Claude, Gemini, and OpenAI rows. Additive, but consumers of `/model/info` should tolerate the new key. ([#48516](https://github.com/BerriAI/litellm/pull/48516))
- **MCP is now GA** — `litellm/proxy/mcp_server` and `litellm/mcp_client` replace the `_experimental` paths; old packages remain as deprecated shims. ([#48513](https://github.com/BerriAI/litellm/pull/48513))
- **Anthropic handler rename** — `llms/anthropic/pass_through` → `llms/anthropic/messages` (native handler under `messages/native`); internal path move only. ([#48510](https://github.com/BerriAI/litellm/pull/48510))
- **Behavior change: 500 → 403** — Enterprise-only `get_spend_routes` on `/key/generate` and related paths now returns a proper `403` naming the license requirement instead of a bare `500`. ([#47112](https://github.com/BerriAI/litellm/pull/47112))
- **CI governance** — new `Spend logs guard` blocks `LiteLLM_SpendLogs` schema/migration edits pending `kerry-berri` approval ([#48518](https://github.com/BerriAI/litellm/pull/48518)); `@kerry-berri` added as code owner of all three `schema.prisma` copies ([#48515](https://github.com/BerriAI/litellm/pull/48515)).
- **File renames** — model-version-named transformation files renamed to family names; all exported symbols unchanged. ([#48509](https://github.com/BerriAI/litellm/pull/48509))

## 3. New Model & Hardware Support

- **DashScope realtime WebSocket** — new `DashScopeRealtime` handler on the shared OpenAI realtime base, handling DashScope's flat `session.update` format that the GA remap would otherwise break. In review. ([#40579](https://github.com/BerriAI/litellm/pull/40579))
- **Vertex image sizing** — requested dimensions now map to a supported aspect ratio instead of silently producing square images, with matching resolution passed to Gemini; malformed sizes and `auto` unchanged. ([#48463](https://github.com/BerriAI/litellm/pull/48463))
- **`azure_ai/gpt-image-2`** pricing/context entry added to `model_prices_and_context_window.json` and its backup. ([#26765](https://github.com/BerriAI/litellm/issues/26765))
- **Known provider gaps (open):** Scaleway is not routed on the `embedding()` endpoint and raises `LiteLLMUnknownProvider` ([#34503](https://github.com/BerriAI/litellm/issues/34503)); Gemini explicit context caching fails with a custom `api_base` due to an invalid cache URL and auth header ([#34872](https://github.com/BerriAI/litellm/issues/34872)).
- No new hardware backends (CUDA/ROCm/Metal/CPU) or quantization formats in this window.

## 4. Performance & Optimization

- **Rate limiter Redis write reduction** — v3 token counters are now written only into open rate-limit windows. Previously a key with only an RPM limit paid up to **five extra Redis writes per request** for counters nobody reads. ([#47521](https://github.com/BerriAI/litellm/pull/47521))
- **Unified multimodal quota accounting** — one shared media measurement for TPM admission, with model-aware image estimates and duration-based audio estimates. Fixes chat image/audio passing admission when only text was counted, and Responses overcounting base64 media as text. ([#48099](https://github.com/BerriAI/litellm/pull/48099))
- **DB lifecycle consolidation** — single lifespan owner for database shutdown, including startup failure and cancellation, with request draining ahead of disconnect and no second pool opened. ([#48506](https://github.com/BerriAI/litellm/pull/48506), [#48507](https://github.com/BerriAI/litellm/pull/48507))
- **Streaming header latency regression closed** — the v1.105.0 `GZipBufferedResponseMiddleware` buffering of SSE response headers until first chunk is resolved, restoring prompt header delivery on `/mcp/<server>` `tools/call` streams (previously stalled until the 15s sse-starlette ping). ([#46659](https://github.com/BerriAI/litellm/issues/46659))
- **Silent-upstream timeouts** — `litellm_settings.request_timeout` now fires when an upstream accepts the TCP connection but never writes a byte. ([#38358](https://github.com/BerriAI/litellm/issues/38358))
- No published throughput/latency benchmarks this period.

## 5. Stability & Regressions

**High**

- **[risk:high] Spend counter double-count → 422 lockouts.** Simultaneous requests arriving right after a spend counter went cold each re-added the customer's entire spend, pushing under-budget customers into 422 budget errors. Cold counters are now *raised* to the cached spend rather than added. Fix PR open. ([#47676](https://github.com/BerriAI/litellm/pull/47676))
- **Cost-map wipe for DB-only deployments.** With an empty `model_list` and `STORE_MODEL_IN_DB=True`, the periodic price reload (including the first poll after boot) wipes every DB deployment's runtime cost-map registration. Open, no fix PR linked. ([#43444](https://github.com/BerriAI/litellm/issues/43444))
- **Anthropic thinking retry returns 400.** When LiteLLM disables extended thinking it kept the `clear_thinking` context-management edit, which Anthropic rejects — most commonly hit on the automatic retry after a bad thinking signature. Fix PR open. ([#48514](https://github.com/BerriAI/litellm/pull/48514))

**Medium**

- **Media dropped in conversions.** Audio, PDF tool results, and model-history media could be silently lost across Responses ↔ Gemini conversion and the final Gemini serializer. Fix PR open. ([#48484](https://github.com/BerriAI/litellm/pull/48484))
- **Enterprise gate returned 500.** Fixed to `403`. ([#47112](https://github.com/BerriAI/litellm/pull/47112))
- **MCP OAuth against GitHub fails on success.** aiohttp's default 8190-byte header limit rejects GitHub's ~9KB CSP header on the token response; outbound session limits raised. ([#48517](https://github.com/BerriAI/litellm/pull/48517))
- **Responses streaming errors masked.** `LiteLLMCompletionStreamingIterator` lacked `completed_response`, so the error handler itself crashed with `AttributeError` and hid the real upstream failure. Closed. ([#38511](https://github.com/BerriAI/litellm/issues/38511))
- **vLLM `cached_tokens` not costed.** Token cost calculator ignores prompt-cache hits from vLLM, overstating spend. Open, 5 👍. ([#22984](https://github.com/BerriAI/litellm/issues/22984))
- **Wrong param advertisement for Claude Opus 4.7.** `get_supported_openai_params()` still lists `temperature`, which Anthropic rejects at request time. Open, 4 👍. ([#26444](https://github.com/BerriAI/litellm/issues/26444))

**Low**

- `litellm.Timeout: ... after None seconds` — timeout duration never populated in the message. Closed. ([#14635](https://github.com/BerriAI/litellm/issues/14635))
- UI cannot remove a previously added LiteLLM param from a model. Open. ([#23998](https://github.com/BerriAI/litellm/issues/23998))
- `/ui/chat` with hosted vLLM fails on follow-up turns with `Response with id ... not found` (playground works). Open. ([#26147](https://github.com/BerriAI/litellm/issues/26147))
- `alert_type_config.llm_requests_hanging.digest: true` still emits one Slack message per check run. Open. ([#22753](https://github.com/BerriAI/litellm/issues/22753))

## 6. What This Means for Application Developers

- **Budget enforcement is worth patching promptly.** If you run per-customer budgets with concurrent traffic, the cold-counter double-count in [#47676](https://github.com/BerriAI/litellm/pull/47676) produces spurious 422s for users who are under budget — a customer-visible failure, not just a metrics inaccuracy.
- **Streaming timeouts are now trustworthy.** With [#38358](https://github.com/BerriAI/litellm/issues/38358) closed, `request_timeout` actually bounds a silent upstream; you no longer need your own watchdog for the "connection accepted, zero bytes" case. The SSE header-buffering regression in [#46659](https://github.com/BerriAI/litellm/issues/46659) is also resolved, so client-side header-driven logic (request IDs, tracing) works again on streamed responses.
- **Plan for MCP as a first-class surface.** The `_experimental` paths are now deprecated shims. New integrations should target `litellm/proxy/mcp_server` and `litellm/mcp_client` ([#48513](https://github.com/BerriAI/litellm/pull/48513)); MCP + GitHub OAuth deployments should pick up [#48517](https://github.com/BerriAI/litellm/pull/48517).
- **Capacity planning gets easier.** `context_window` in `/model/info` ([#48516](https://github.com/BerriAI/litellm/pull/48516)) lets gateways and routers size prompts without maintaining a parallel model registry — useful if you drive routing or truncation off that endpoint.
- **Multimodal TPM budgets will shift.** The unified media accounting in [#48099](https://github.com/BerriAI/litellm/pull/48099) makes image/audio requests *more* likely to be admitted (previously undercounted) and Responses payloads *less* likely to be falsely rejected. Expect your effective TPM headroom for multimodal traffic to change.
- **Anthropic thinking-mode retries are currently fragile** ([#48514](https://github.com/BerriAI/litellm/pull/48514)). If you depend on extended thinking with automatic retry, either pin behavior or add a fallback path until the fix lands.
- **DB-backed model deployments:** avoid relying on runtime cost-map registrations across restarts until [#43444](https://github.com/BerriAI/litellm/issues/43444) is fixed, or set explicit costs in `config.yaml` rather than only in the DB.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-10-27

## 1. Today's Highlights

Unsloth shipped **v0.1.911-beta**, adding scheduled prompt execution (local model or API), **MiniMax-M3.1-Flash** support, FLUX-3 image editing, and another round of performance gains including faster image generation and Mac chat. In parallel, a large batch of Studio PRs landed around VRAM reclamation, deterministic diffusion output (same seed → same image/clip across processes), ROCm attention correctness, and FSDP2 distributed-training fixes. The issue tracker is dominated by Studio/desktop reports, with the most consequential open items being AMD GPU detection on mixed NVIDIA-AMD Linux hosts and an NVFP4 CPU fallback on Apple Silicon for MiniMax-M3.1.

## 2. Releases & Breaking Changes

- **v0.1.911-beta — "Scheduled + MiniMax-M3.1-Flash + Perf Gains"** ([release](https://github.com/unslothai/unsloth/releases/tag/v0.1.911-beta))
  - **Scheduled tasks**: run a prompt on a schedule against either a local model or an API-backed provider.
  - **MiniMax-M3.1-Flash** model support.
  - **FLUX-3 edit support**, plus face touch-up controls for generated images.
  - Faster image generation and improved Mac chat performance.
  - No breaking API/config changes or migration notes were called out in this release.

## 3. New Model & Hardware Support

- **MiniMax-M3.1-Flash** — added in v0.1.911-beta; GGUF variant now appearing in Studio, with an Apple Silicon issue filed on day one ([#15409](https://github.com/unslothai/unsloth/issues/15409)).
- **FLUX-3 image editing** — new edit pipeline in the release; macOS edit path with `unsloth/flux-3-image-klein-GGUF` was broken and has been closed ([#15241](https://github.com/unslothai/unsloth/issues/15241)).
- **Qwen-Image-2.1** — diffusion path being hardened for cross-process reproducibility ([#15455](https://github.com/unslothai/unsloth/pull/15455)).
- **Video models** — Wan2.1, Wan2.2-TI2V-5B, HunyuanVideo-1.5 and dense MiniMax-H3 now get pre-seeded denoiser compile specialisations on returning loads ([#15406](https://github.com/unslothai/unsloth/pull/15406)).
- **SDXL + ControlNet on T4** — resident promotion on 15–16 GB cards kept working; a budget-accounting regression for ControlNet was fixed ([#15446](https://github.com/unslothai/unsloth/pull/15446)).
- **MiniCPM-V-4.7-1B on Vulkan** — text generation failure reported and closed ([#14987](https://github.com/unslothai/unsloth/issues/14987)).
- **Cohere Transcribe (conformer ASR)** — hosting request closed ([#5789](https://github.com/unslothai/unsloth/issues/5789)).
- **gpt-oss** — expert-layout probing now reads `config.transformers_weights` before standard filenames when choosing fused vs. split bitsandbytes experts ([#15386](https://github.com/unslothai/unsloth/pull/15386)).
- **Blackwell (sm_100/sm_120)** — packed fp32 attention reroute to xformers now preserves fp32 output instead of silently downcasting to bf16 ([#15410](https://github.com/unslothai/unsloth/pull/15410)).

## 4. Performance & Optimization

- **VRAM reclaim during native cache writes**: the int8 video build no longer holds `transformer.state_dict()` for the whole background write; weights are read from the live module so offload can actually free memory ([#15461](https://github.com/unslothai/unsloth/pull/15461)).
- **Diffusion int8 W8A8 compile regression**: the compiled linear used a `1 << 27` chunk budget vs. the eager `1 << 25`, pushing peak memory above eager; the fix restores the eager peak and honors `force-eager` ([#15456](https://github.com/unslothai/unsloth/pull/15456)).
- **Deterministic diffusion**: Qwen-Image-2.1 produced different images per fresh process for the same seed (3 processes × 5 renders); a pinned reduction-config filter fixes it ([#15455](https://github.com/unslothai/unsloth/pull/15455)). Similarly, the video VAE decode route (untiled / wide tiles / stock tiles) is now locked per latent shape so free-VRAM movement doesn't change decoded frames ([#15462](https://github.com/unslothai/unsloth/pull/15462)).
- **iGPU host pinning**: pinning is now classified against the *target* device rather than the current GPU, fixing mis-classification during offload hooks ([#15463](https://github.com/unslothai/unsloth/pull/15463)).
- **ROCm attention**: self vs. cross attention detection no longer evaluates a query-length/key-length guard inside traced graphs, avoiding a compile-time comparison failure ([#15458](https://github.com/unslothai/unsloth/pull/15458)).
- **FSDP2**: `native_fsdp2_unsharded` now gathers before clearing `post_forward_mesh_info`, fixing int `reshard_after_forward` cases ([#15426](https://github.com/unslothai/unsloth/pull/15426)); fp16 `_ShardedGradScaler` now agrees on overflow across ranks without an explicit `unscale_` and gives fused optimizers a single 0/1 flag per device ([#15416](https://github.com/unslothai/unsloth/pull/15416)).
- **CI cost**: `scripts/enforce_kwargs_spacing.py` was hammering pre-commit.ci across the repo's high PR volume; closed ([#14174](https://github.com/unslothai/unsloth/issues/14174)).
- **UI responsiveness**: typing in the Edit "Instructions" box during image generation was extremely slow on ROCm; closed ([#15221](https://github.com/unslothai/unsloth/issues/15221)).

## 5. Stability & Regressions

**High**
- **Mixed NVIDIA + AMD Linux hosts**: AMD GPUs are not enumerated at all under `unsloth studio` with a custom llama.cpp build — open, no fix referenced ([#15444](https://github.com/unslothai/unsloth/issues/15444)).
- **MiniMax-M3.1 GGUF on Apple Silicon**: NVFP4 falls back to CPU despite 61/61 layers reporting as offloaded to GPU — open ([#15409](https://github.com/unslothai/unsloth/issues/15409)).
- **Anthropic `/v1/messages` passthrough**: context overflow returns an unrecoverable `400 exceed_context_size_error` instead of triggering auto-compaction — open ([#15417](https://github.com/unslothai/unsloth/issues/15417)).
- **DeepSeek-V4.1-Flash GGUF chat template**: the template rejects ordinary discussion of the image marker and corrupts conversation history; a patch is supplied in the issue — open ([#15285](https://github.com/unslothai/unsloth/issues/15285)).

**Medium**
- **Possible disk leak**: 200–400 MB lost on `C:` after each Studio use with no cleanup path — open ([#14487](https://github.com/unslothai/unsloth/issues/14487)).
- **Model hub loads fail for all "deepseek 4.1 1017 uncensored" results** — open ([#15412](https://github.com/unslothai/unsloth/issues/15412)).
- **Studio resume broken for Qwen3.5-2B**: `_MODEL_WEIGHT_RE` doesn't match `model.safetensors-00001-of-00001.safetensors` — open ([#15405](https://github.com/unslothai/unsloth/issues/15405)).
- **Linux desktop SIGABRT**: `WebKitWebProcess` crashes with AT-SPI/atk-bridge active on KDE Plasma — open ([#15260](https://github.com/unslothai/unsloth/issues/15260)).
- **Fine-tuned model serving**: after a fine-tune job, chat completions with `fine_tuned_model` silently answered from the already-loaded base model; fix PR open ([#15449](https://github.com/unslothai/unsloth/pull/15449)).
- **Codex MCP servers dropped**: `unsloth start codex` opens a Codex without the user's configured MCP servers; fix PR open ([#15452](https://github.com/unslothai/unsloth/pull/15452)).
- **File rewind depth cap**: history only records 4 folders deep and the first 2,000 files; fix PR open ([#15439](https://github.com/unslothai/unsloth/pull/15439)).

**Low / Closed**
- Auto-compact ignored image tokens on local GGUF chats, overflowing context in screenshot tool loops — closed ([#14968](https://github.com/unslothai/unsloth/issues/14968)).
- Edit-gallery "Open image in new window" / "Copy image" were no-ops — closed ([#15272](https://github.com/unslothai/unsloth/issues/15272)).
- Browser preview unavailable in Project — closed ([#15046](https://github.com/unslothai/unsloth/issues/15046)).
- "Load into" setting ignored when STT model is loaded from an API — open ([#15280](https://github.com/unslothai/unsloth/issues/15280)).
- `formatKvRate` displayed `1024 KiB` instead of `1 MiB` in the memory bar; fix PR open ([#15460](https://github.com/unslothai/unsloth/pull/15460)).
- Test/lint hygiene on `main`: spoof-safety lint failures ([#15454](https://github.com/unslothai/unsloth/pull/15454)) and a flaky FIFO admission test ([#15457](https://github.com/unslothai/unsloth/pull/15457)) were fixed.

## 6. What This Means for Application Developers

- **Local scheduling is now first-class.** Scheduled tasks can target either a local model or a remote API, so cron-style agent jobs no longer need an external orchestrator — but note that reliability depends on the desktop app staying up, and the disk-usage issue ([#14487](https://github.com/unslothai/unsloth/issues/14487)) is worth monitoring on long-running hosts.
- **The OpenAI-compatible surface is the safer integration path than the Anthropic one right now.** `/v1/messages` does not apply auto-compaction on overflow ([#15417](https://github.com/unslothai/unsloth/issues/15417)), so budget context client-side if you're fronting Unsloth with Anthropic-style SDKs.
- **Verify fine-tune routing.** Until [#15449](https://github.com/unslothai/unsloth/pull/15449) merges, requesting a newly fine-tuned model name may silently return the base model — a failure mode that looks like "training did nothing." Assert on model identity in your eval harness.
- **Reproducibility is improving for generative pipelines.** Seed-stable image and video output across processes ([#15455](https://github.com/unslothai/unsloth/pull/15455), [#15462](https://github.com/unslothai/unsloth/pull/15462)) matters if you cache or diff generated assets; pin to a build after these land.
- **Gateway flexibility is still limited.** Custom headers for OpenAI-compatible remote providers (e.g., LiteLLM tenant/feature routing) are not supported yet ([#14811](https://github.com/unslothai/unsloth/issues/14811)), and ACP support is a pending request ([#15356](https://github.com/unslothai/unsloth/issues/15356)). Plan a proxy in front if you need per-request metadata.
- **Mixed-vendor GPU hosts are unsupported in practice.** If you run NVIDIA + AMD in one box, Studio will only see NVIDIA ([#15444](https://github.com/unslothai/unsloth/issues/15444)); containerize or isolate by vendor.
- **Training reliability**: a proposed `unsloth dataset check` preflight ([#15332](https://github.com/unslothai/unsloth/issues/15332)) targets the two most common silent failures — chat-template/row mismatch and unannounced truncation at `max_seq_length`. Until it exists, validate datasets before submitting jobs.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*