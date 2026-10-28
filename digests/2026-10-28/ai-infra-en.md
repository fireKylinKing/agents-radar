# AI Infrastructure Digest 2026-10-28

> Generated: 2026-10-28 01:35 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project AI Infrastructure Comparison — 2026-10-28

> Scope: vLLM, SGLang, llama.cpp, Ollama, LiteLLM, Unsloth.  
> `n/r` = not reported in the supplied 24h digest. SGLang is the only project with explicit 24h issue/PR counts.

## 1. Ecosystem Overview

The ecosystem is in a hardware-qualification and correctness-hardening phase rather than a major release phase. Serving engines are racing to cover DeepSeek-V4, Qwen3.8, Kimi-K3, GLM-5.x and new accelerator targets such as SM120, ROCm MI355X and GB300, but several high-severity issues are silent-correctness or process-kill bugs rather than throughput regressions. Local runtimes are absorbing the new LiquidAI LFM3 family quickly, especially llama.cpp, while Ollama follows with fixes and MLX concurrency work. The gateway layer is shipping patches but still carries a security backport gap and billing-correctness issues. Fine-tuning tooling is focused on Studio hardening and FP8/Block Swap fixes, with new-model support lagging the serving engines.

## 2. Activity Comparison

| Project | Issues count (24h) | PR count (24h) | Release status | Activity signal |
|---|---:|---:|---|---|
| **vLLM** | n/r | n/r | No releases; no breaking changes | Hardware/model coverage plus critical regressions. #50576 has 121 comments; #56892 is a critical SM120 CUDA-graph/decode issue. |
| **SGLang** | **63** updated | **500** updated | No releases | Roadmap/tracker consolidation and a large `[inactive]` stale sweep. Critical spec-decode crash #43412 and silent Kimi-K3 SM90 bug #45755. |
| **llama.cpp** | n/r | n/r | Continuous builds **b11930–b11944**; no formal release | LFM3 family landed across text/vision/audio/omni; reasoning-effort discovery and router memory ceiling shipped. |
| **Ollama** | n/r | n/r | No releases | LFM3 GGUF fixes, MLX concurrent generation, CUDA/Blackwell startup mitigation. Multiple unresolved macOS/MLX and tool-loop issues. |
| **LiteLLM** | n/r | n/r | **4 patch releases**: v1.106.5, v1.105.6, v1.104.8, v1.103.10; main on 1.109.0; 1.108.0 as RC | Security backport outstanding for 1.106.x images; multiple streaming/billing and slot-leak fixes. |
| **Unsloth** | n/r | n/r | No releases | Studio hardening; FP8/Block Swap fixes for Qwen3.8-Flash-Next; Windows Recycle Bin and Anthropic auto-compaction regressions. |

## 3. Model Support Race

| Project | New model / architecture support today | Assessment |
|---|---|---|
| **vLLM** | DeepSeek-V4-Flash on SM8x is an open request, not yet supported; Kimi NVFP4 PR; Nemotron-Nano tool parser; AMD CDNA W4A16; ROCm Inv-RoPE FP8; Qwen2.5-Omni CPU offload | Broad datacenter coverage, but **Ampere users still cannot run DeepSeek-V4-Flash**. SM120 DeepSeek-V4.1-Flash is a critical risk. |
| **SGLang** | Nemotron-3-Embed native support; hybrid NVFP4/MXFP4 draft experts for DeepSeek-V4; NPU DSpark compact mode; AMD RDNA3/RDNA4 tracking; Apple Silicon RFC | Strong on datacenter/PD and accelerator-specific paths, but Kimi-K3 SM90 MXFP4 has a **silent activation-function substitution** bug. |
| **llama.cpp** | LFM3-1.7B, LFM3-VL-2B, LFM3-Audio-2B, LFM3-Omni-2B; LiquidAI d1-2B/d1-omni-2B; video input for LFM3; `/v1/realtime` tool calls; GigaChat 3.5 432B-A28B proposed | **Furthest ahead on LFM3 today**, including multimodal, audio/omni and realtime tool plumbing. |
| **Ollama** | LFM3 GGUF fixes after `unknown model architecture: 'lfm3'`; Nemotron 3.5 Bolt 74B-A9B; Kolibri 1 MLX; MLX concurrent generation; CUDA/Blackwell startup fix | Following llama.cpp on LFM3; strong local-runtime packaging, but MLX and CUDA startup regressions remain. |
| **LiteLLM** | Not an inference runtime. Databricks OpenJev pricing fix; Cloudflare Workers AI expansion still stale; Tavily passthrough | Model support means **provider/pricing/API coverage**, not architecture enablement. |
| **Unsloth** | LFM3 request only, no implementation PR; Qwen3.8-Flash-Next FP8/Block Swap fixes; Intel Arc XPU request; AMD mixed Linux detection issue; ExLlamaV3 request | Fine-tuning support is **lagging new-model enablement**; Qwen3.8-Flash-Next is the most actively patched architecture. |

**Who is ahead:**  
- **Datacenter serving:** vLLM and SGLang lead on DeepSeek-V4, Qwen3.8, Kimi-K3, GLM-5.x, ROCm MI355X and SM120/GB300 paths. vLLM has broader hardware/model coverage; SGLang is more aggressive on PD disaggregation, cache-aware routing and accelerator-specific kernels.  
- **Local/edge runtime:** llama.cpp leads on LFM3 and local multimodal/realtime; Ollama is second but still fixing LFM3 GGUF/MLX issues.  
- **Fine-tuning:** Unsloth is strong on acceleration but not yet shipping LFM3 support; Qwen3.8-Flash-Next fixes are the main new-architecture work.

## 4. Performance Frontier

| Optimization area | Where effort is concentrated | Representative items |
|---|---|---|
| **KV cache, prefix cache and offload** | Memory pressure, PD stale-KV correctness, prefix sharing and cache-offload scalability | vLLM P2P KV offload fail-fast, KV-pool collapse with speculative decoding, multi-tier/GDS RFC; SGLang HiCache ack-queue drain, MLA+FP8 KV materialization at **43.7% of a decode step**; llama.cpp `--slot-attach` prefix sharing; Unsloth Block Swap offload on Qwen3.8-Flash-Next. |
| **Kernels and quantization** | FP8/FP4/W4A16 kernels, MoE expert paths, small-M GEMMs and backend-specific kernels | vLLM SSM GDN in-place state (**+10–13% throughput**, up to **36 GiB less per-step traffic**), CDNA W4A16, ROCm Inv-RoPE FP8, MI355X trackers; SGLang W8A16 small-M GEMM for GLM-5.2 block-FP8 (**up to 6.8% lower per-token latency**), DeepGEMM `fp8_fp4`, GDN Triton fixes; llama.cpp MoE expert cache (**+6% QFN, +7% GLM**), Hexagon TOP_K, CUDA-graph cluster-dim re-instantiation. |
| **Batching, scheduling and distributed serving** | PD disaggregation, DP/EP coordination, elastic EP, cache-aware routing | vLLM NCCL `reduce_scatter` deadlock with MoE+DP, duplicate CUDA-graph capture in elastic EP, CPU offload PP-rank inconsistency; SGLang PD-disaggregation roadmaps, EP2 illegal memory access on sm_120, agentic cache scheduling; llama.cpp router `--models-memory-max` and RPC hardening; LiteLLM routing/slot leaks. |
| **Low-latency API and streaming** | Guardrail streaming, embedding timeouts, timing headers, reasoning-effort discovery | LiteLLM restores streaming fast path for request-only guardrails and fixes embedding timeout propagation; vLLM proposes opt-in `x-vllm-*` timing headers; llama.cpp exposes reasoning-effort levels; SGLang tightens OpenAI-compatible validation. |
| **Local/edge and fine-tuning memory** | MLX concurrency, CUDA startup, Windows desktop disk usage, explicit quant selection | Ollama MLX parallelism via `OLLAMA_NUM_PARALLEL`, CUDA eager-module startup retry; Unsloth Block Swap/FP8 n-gram fixes, benchmarks page work, Windows Recycle Bin WAL/SHM leak. |

The center of gravity is clear: **KV-cache/memory behavior, FP8/FP4/MoE kernels, and PD/distributed scheduling** dominate engineering effort. Raw throughput work is increasingly coupled to correctness validation, especially around speculative decoding and quantized targets.

## 5. Layer Positioning

| Layer | Projects | Role | Today’s signal |
|---|---|---|---|
| **High-throughput serving engines** | vLLM, SGLang | Datacenter GPU/accelerator inference, OpenAI-compatible APIs, distributed serving, PD disaggregation, advanced quantization | Both are in hardware/model coverage races; performance gains are real, but critical correctness and stability issues remain. vLLM is broader; SGLang is more PD/cache/kernel-aggressive. |
| **Local/edge runtime and server** | llama.cpp, Ollama | Single-node/local inference, broad quantization, CPU/GPU/backend coverage, multimodal/audio, developer-facing model management | llama.cpp is shipping new architectures fastest; Ollama is packaging and hardening, especially MLX/CUDA/LFM3. Neither replaces cluster-scale serving engines. |
| **LLM gateway / proxy** | LiteLLM | Routing, budgets, spend logs, auth, provider translation, guardrails, OpenAI/Anthropic-compatible proxy | High release cadence, but security backport and billing-correctness issues mean pinned versions and spend reconciliation are mandatory. |
| **Fine-tuning / training acceleration** | Unsloth | Fine-tuning, quantized training/export, desktop Studio, benchmarking | Focused on Studio hardening and Qwen3.8-Flash-Next FP8/Block Swap fixes; new-model support and hardware coverage lag serving runtimes. |

## 6. Trend Signals

- **Hardware-quantization qualification is now a first-class production task.** DeepSeek-V4-Flash is still missing on SM8x, SM120 CUDA graphs are broken for DeepSeek-V4.1-Flash, sm_120 + EP2 is unusable for some NVFP4 checkpoints, and Intel XPU / mixed NVIDIA-AMD / consumer Radeon remain gaps. Treat hardware + model + quant as a tested tuple, not a generic “CUDA” or “ROCm” target.
- **Correctness bugs are outpacing performance wins in risk.** SGLang’s Kimi-K3 SM90 MXFP4 **silently computes the wrong activation**, llama.cpp speculative decoding diverges on quantized targets, vLLM greedy output can change across restarts, and SGLang PD prefill had a stale-KV hazard. Output-equality and prefix-sharing regression tests should gate upgrades.
- **Speculative decoding and MoE remain high-leverage but high-risk.** vLLM, SGLang, llama.cpp, Ollama and Unsloth all touch draft models, MTP, expert caches or spec-decode paths today. The pattern is consistent: large potential gains, but crashes, hangs, silent divergence or accuracy collapse appear around quantized/MoE/PD configurations.
- **PD disaggregation and cache-aware routing are moving toward default architecture for agents.** SGLang’s PD and agentic-cache roadmaps, vLLM’s KV offload RFCs, and llama.cpp prefix sharing all target the same problem: long-prefix, multi-turn agent loops are expensive, so KV locality and cache routing become control-plane concerns.
- **Agent/tooling compatibility is fragmenting.** vLLM’s Rust frontend can 500 on `auto` tool parsers; Ollama has tool-loop 500s and silent tool-call drops; LiteLLM’s `/v1/messages` path is the highest-churn surface; llama.cpp is adding `tool_reference` and realtime tool calls. Application developers should validate tool-call ordering, empty-content handling, unknown-tool behavior and parameter bounds against pinned versions.
- **Supply-chain and DoS hardening are back on the agenda.** LiteLLM 1.106.x images still trust a compromised root CA, llama.cpp has an open RPC `ggml_nbytes()` overflow fix, and unbounded sampler parameters can trigger multi-GB allocations. Gateway and server deployments need cosign verification, input caps and untrusted-peer assumptions.
- **Local multimodal and voice agents are becoming practical.** LFM3 text/vision/audio/omni support in llama.cpp, Ollama’s MLX work, and realtime transcription/tool-call endpoints point to local agents that span text, audio and video. Watch co-batching and hybrid-state correctness issues before scaling multi-session local deployments.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-10-28

## 1. Today's Highlights
No releases landed in the last 24 hours. Activity is dominated by hardware coverage and performance work: a high-traffic request to bring DeepSeek-V4-Flash to Ampere SM8x ([#50576](https://github.com/vllm-project/vllm/issues/50576)), a severe SM120 CUDA-graph/decode-throughput problem for DeepSeek-V4.1-Flash ([#56892](https://github.com/vllm-project/vllm/issues/56892)), and several ROCm MI355X optimization trackers for Qwen3.8 models ([#57149](https://github.com/vllm-project/vllm/issues/57149), [#59575](https://github.com/vllm-project/vllm/issues/59575)). The Q4 2026 roadmap ([#61024](https://github.com/vllm-project/vllm/issues/61024)) remains active.

## 2. Releases & Breaking Changes
None. No new versions, API changes, config changes, or migration notes were published in the last 24 hours.

## 3. New Model & Hardware Support
- **DeepSeek-V4-Flash / V4-Flash-0731 on SM8x (Ampere A100/A800, RTX 30xx)** — open feature request with 121 comments and 16 👍; neither checkpoint currently runs on SM8x. [#50576](https://github.com/vllm-project/vllm/issues/50576)
- **Kimi NVFP4 specialized model** — open PR, description pending. [#42458](https://github.com/vllm-project/vllm/pull/42458)
- **NVIDIA Nemotron-Nano-9B-v2 tool parser** — PR closed. [#42255](https://github.com/vllm-project/vllm/pull/42255)
- **AMD CDNA W4A16 linear kernel** — adds `CDNAHybridW4A16LinearKernel` for MI300 gfx942/gfx950, covering GPTQ/AWQ W4A16. [#42640](https://github.com/vllm-project/vllm/pull/42640)
- **ROCm Inv-RoPE FP8 quantization** — Triton implementation of inverse-RoPE FP8 quant. [#42033](https://github.com/vllm-project/vllm/pull/42033)
- **Qwen2.5-Omni multimodal CPU offload** — moves processed multimodal features off GPU to reduce memory pressure. [#41891](https://github.com/vllm-project/vllm/pull/41891)

## 4. Performance & Optimization
- **In-place SSM state for GDN chunk-prefill kernel** (Qwen3.5, Qwen3-Next, OLMo-Hybrid): up to **36 GiB less per-step memory traffic**, **+10–13% throughput** on large Qwen models, **+41–56%** on 0.8B. [#41824](https://github.com/vllm-project/vllm/pull/41824)
- **Qwen3.8-2.4T-A95B on gfx950/MI355X** — ROCm performance optimization tracker; work lands as stacked PRs. [#57149](https://github.com/vllm-project/vllm/issues/57149)
- **Qwen3.8-Flash-Next on gfx950/MI355X** — tracker covering GatedDeltaNet, QuerySparse Attention, HyperConnection residual, and MXFP4 MoE. [#59575](https://github.com/vllm-project/vllm/issues/59575)
- **GLM 5.3 performance optimization** — follow-up to GLM-5.2 work; CUDA and other optimizations under discussion. [#57406](https://github.com/vllm-project/vllm/issues/57406)
- **Dynamic PDL enablement** — proposal to expose PDL flags (`TRTLLM_ENABLE_PDL`, `TORCHINDUCTOR_ENABLE_PDL`) that improve low-latency Hopper/Blackwell performance. [#40543](https://github.com/vllm-project/vllm/issues/40543)
- **Remove duplicate CUDA-graph capture in elastic EP** — cleanup of double capture introduced in #41792. [#42107](https://github.com/vllm-project/vllm/issues/42107)
- **Tokenizer decode deduplication for logprobs** — caches repeated `tokenizer.decode([token_id])` calls. [#42211](https://github.com/vllm-project/vllm/pull/42211)
- **ROCm AITER high-concurrency accuracy collapse** — Qwen3.8-27B-FP8 on MI355X; issue is now closed, but users should validate `VLLM_ROCM_USE_AITER=1` at high concurrency. [#60160](https://github.com/vllm-project/vllm/issues/60160)

## 5. Stability & Regressions
Ranked by severity:

**Critical**
- **SM120 CUDA graphs unusable + extremely low decode throughput** with `--enforce-eager` for DeepSeek-V4.1-Flash on 8× RTX PRO 6000 Blackwell. Fix not yet identified in the issue. [#56892](https://github.com/vllm-project/vllm/issues/56892)
- **NCCL `reduce_scatter` deadlock (~T+1h)** with MoE + DP on ray/mp backends: zero-token-scheduled steps skip `coordinate_batch_across_dp`. [#43547](https://github.com/vllm-project/vllm/issues/43547)
- **Greedy output changes across restarts** even with `VLLM_BATCH_INVARIANT=1`; q/k-norm + RoPE combo kernel selects reduction config by timing. Reproducibility risk. [#58899](https://github.com/vllm-project/vllm/issues/58899)

**High**
- **`EngineDeadError` with Kimi-K2.6** on vLLM 0.20.2 (aarch64). [#42363](https://github.com/vllm-project/vllm/issues/42363)
- **P2P KV offload tier does not fail fast on peer faults** — fault handling can leave the tier in a bad state. [#63602](https://github.com/vllm-project/vllm/issues/63602)
- **OpenShift crash with `gemma4-unified-cu129` image** — immediate `KeyError` on container start. [#44548](https://github.com/vllm-project/vllm/issues/44548)
- **Rust frontend returns 500 on every tool request** when the `auto` tool parser cannot resolve a parser for the model. [#63584](https://github.com/vllm-project/vllm/issues/63584)

**Medium**
- **CPU offloading computes inconsistent `num_cpu_blocks` across PP ranks** (4× B300, PP=4). [#50821](https://github.com/vllm-project/vllm/issues/50821)
- **KV-cache pool collapse with speculative decoding** when a draft model introduces a new cache spec type (hybrid GDN + DFlash); POC for a dedicated draft pool exists. [#59117](https://github.com/vllm-project/vllm/issues/59117)
- **Rust frontend cannot start with official Qwen3-ASR checkpoints** (no `tokenizer.json`, nested `vocab_size`). [#61057](https://github.com/vllm-project/vllm/issues/61057)
- **"System message must be at the beginning"** error with Qwen3.6-27B. [#41114](https://github.com/vllm-project/vllm/issues/41114)

**Resolved / Closed**
- ROCm AITER accuracy collapse on Qwen3.8-27B-FP8. [#60160](https://github.com/vllm-project/vllm/issues/60160)
- MLA `kv_b_proj.weight.dtype` AttributeError with quantized models in chunked prefill. [#43888](https://github.com/vllm-project/vllm/issues/43888)
- V1 scheduler hard-fail on stale `req_id` in `_update_after_schedule`. [#42157](https://github.com/vllm-project/vllm/issues/42157)
- ROCm out-of-bounds Q-load in `paged_attention_ll4mi_QKV_mfma4_kernel`. [#42421](https://github.com/vllm-project/vllm/pull/42421)
- Mamba copy metadata high-bit pointer handling on XPU. [#41995](https://github.com/vllm-project/vllm/pull/41995)

**Fix PRs in flight**
- LoRA metadata binding for non-eager KV-sharing fast prefill (Gemma3n/Gemma4). [#61162](https://github.com/vllm-project/vllm/pull/61162)
- DeepGEMM warmup `RuntimeError` by deferring alignment check for non-FP8 modules. [#42142](https://github.com/vllm-project/vllm/pull/42142)
- Streaming stop handling during tool parsing. [#42213](https://github.com/vllm-project/vllm/pull/42213)
- `prompt_logprobs` non-determinism with prefix caching. [#42245](https://github.com/vllm-project/vllm/pull/42245)
- Abort request on `ValueError`/`VLLMValidationError` in `generate()` and `encode()`. [#42442](https://github.com/vllm-project/vllm/pull/42442)

## 6. What This Means for Application Developers
- **A100/A800 users cannot yet run DeepSeek-V4-Flash** — plan for Hopper/Blackwell capacity or wait for the SM8x enablement tracked in [#50576](https://github.com/vllm-project/vllm/issues/50576).
- **On SM120 Blackwell RTX PRO 6000, avoid `--enforce-eager` for DeepSeek-V4.1-Flash** until CUDA-graph capture and decode throughput are fixed ([#56892](https://github.com/vllm-project/vllm/issues/56892)).
- **Do not assume deterministic greedy decoding across restarts**, even with `VLLM_BATCH_INVARIANT=1`; pin vLLM versions and validate outputs when reproducibility matters ([#58899](https://github.com/vllm-project/vllm/issues/58899)).
- **Configure explicit tool parsers rather than `auto` on the Rust frontend** until parser-resolution 500s are addressed ([#63584](https://github.com/vllm-project/vllm/issues/63584)).
- **Multi-tier/GDS KV offloading is still RFC-stage** ([#38260](https://github.com/vllm-project/vllm/issues/38260), [#48504](https://github.com/vllm-project/vllm/issues/48504), [#59538](https://github.com/vllm-project/vllm/issues/59538)); avoid depending on it in production yet.
- **ROCm MI355X users should benchmark Qwen3.8 workloads** against the active optimization trackers and validate AITER accuracy at high concurrency.
- **Per-request timing headers** (`x-vllm-*`) are proposed as opt-in via [#42198](https://github.com/vllm-project/vllm/pull/42198) — useful for client-side latency attribution once landed.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-10-28

**Scope:** github.com/sgl-project/sglang · 63 issues and 500 PRs updated in the last 24h · no new releases.

---

## 1. Today's Highlights

Activity is dominated by roadmap and tracker consolidation rather than shipped code: the Prefill-Decode Disaggregation Q4 roadmap ([#43699](https://github.com/sgl-project/sglang/issues/43699)), the Diffusion Q4 roadmap ([#43313](https://github.com/sgl-project/sglang/issues/43313)), and the Agentic Cache Scheduling roadmap ([#43098](https://github.com/sgl-project/sglang/issues/43098)) all saw updates, alongside a large automated `[inactive]` stale-issue sweep. The most consequential open defect is a **speculative-decoding crash on the first non-greedy request** in pip-installed bundles ([#43412](https://github.com/sgl-project/sglang/issues/43412)), which sits next to a **silent numerical correctness bug** for Kimi-K3 on SM90 ([#45755](https://github.com/sgl-project/sglang/issues/45755)). On the positive side, a new W8A16 small-M GEMM for GLM-5.2 block-FP8 decode shows up to 6.8% per-token latency reduction on GB300 without the +8.2 GiB weight-copy overhead of the prior approach ([#45862](https://github.com/sgl-project/sglang/pull/45862)).

---

## 2. Releases & Breaking Changes

**No new releases in the last 24h.**

Behavioral / API changes in flight:

- **[#44047](https://github.com/sgl-project/sglang/pull/44047) — OpenAI-compatible API validation tightening (OPEN).** Out-of-range sampling parameters that currently pass request parsing and fail later in the scheduler would now be rejected at the API boundary. Also fixes `max_completion_tokens or max_tokens` so an explicit `0` no longer silently falls back to the legacy limit. Clients sending sloppy parameters should expect 400s.
- **[#36666](https://github.com/sgl-project/sglang/pull/36666) — Comma-separated `--api-key` (CLOSED).** Multiple credentials per server via the existing flag, no new flags. Backward compatible.
- **[#45859](https://github.com/sgl-project/sglang/pull/45859) — New `api.v1` discovery fields in rust/grpc (OPEN).** `GetServerInfoResponse` gains `allow_auto_truncate`, `skip_tokenizer_init`, `num_re...`. Additive, but gateway clients pinning response schemas should verify.
- **[#46904](https://github.com/sgl-project/sglang/pull/46904) — Docs for four undocumented PD-disaggregation `ServerArgs` flags (OPEN).** `server_arguments_undocumented.txt` is a ratchet list that may only shrink; these are user-facing knobs for PD deployments.

> ⚠️ **Note on the `[inactive]` closures:** a large batch of issues (#35148, #34969, #36820, #35564, #36511, #36858, #35811, #36598, #36174, #36822, #35702, #35324, #36841) was closed as `[inactive]` on 2026-10-28. These are stale-issue sweep closures, **not** fixes — several describe real defects. Do not read them as resolved.

---

## 3. New Model & Hardware Support

- **[#45885](https://github.com/sgl-project/sglang/pull/45885) — Nemotron-3-Embed native support (OPEN).** Adds `nvidia/Nemotron-3-Embed-1B-BF16` for retrieval workloads; the `-NVFP4` variant shares the architecture and needs no model-specific changes.
- **[#30599](https://github.com/sgl-project/sglang/issues/30599) — AMD consumer RDNA3/RDNA4 tracking (OPEN, 4 👍).** Proposal to extend the ROCm path beyond datacenter Instinct (`gfx942`/`gfx950`) to `gfx1100`/`gfx1101`/`gfx1200`/`gfx1201`. Today consumer Radeon is effectively unsupported.
- **[#32321](https://github.com/sgl-project/sglang/issues/32321) — Apple Silicon serving redesign RFC (OPEN).** Torch-owned SRT path with an exported whole-model MLX region; implementation tracked in #36164.
- **[#40173](https://github.com/sgl-project/sglang/pull/40173) — Hybrid NVFP4/MXFP4 draft experts via CUTLASS MoE on SM90/SM120 (OPEN).** Routes the MXFP4 DSpark/MTP draft experts in `nvidia/DeepSeek-V4*-NVFP4` checkpoints off the FlashInfer-TRTLLM path, which currently mishandles them under `HybridFp8NvFp4Config`.
- **[#38440](https://github.com/sgl-project/sglang/pull/38440) — NPU DSpark compact mode for DeepSeek-V4 (OPEN).** Removes uniform verify-window and eager post-processing overhead in speculative decoding on Ascend.
- **[#46902](https://github.com/sgl-project/sglang/pull/46902) — AMD mono-kernel version bump (CLOSED).**

---

## 4. Performance & Optimization

**Landed / proposed kernels**

- **[#45862](https://github.com/sgl-project/sglang/pull/45862) — W8A16 small-M GEMM for GLM-5.2 block-FP8 decode projections.** On GB300 (TP4, GLM-5.2-FP8, MTP): **up to 6.8% lower end-to-end latency per token** vs. default W8A8, and **1.9% vs. #41975** — while avoiding #41975's **+8.2 GiB of BF16 weight copies per GPU**. KV pool stays at **1,196,928 tokens instead of 1,014,848**.
- **[#46903](https://github.com/sgl-project/sglang/pull/46903) — HiCache ack-queue drain.** Replaces `O(k·N)` `list.pop(0)` loops with slice deletion in `UnifiedRadixCache.writing_check` / `loading_check` and `DecodeKVCacheOffloadManager._check_offload_progress`. Directly targets cache-offload scalability under high concurrency.
- **[#44350](https://github.com/sgl-project/sglang/pull/44350) — DSA/DSV4 indexers moved to DeepGEMM's `fp8_fp4` MQA-logits API.** `fp8_mqa_logits` / `fp8_paged_mqa_logits` are removed upstream; this refactor also threads the FP4 path through sparse attention.
- **[#46839](https://github.com/sgl-project/sglang/pull/46839) — GDN: honour the fp32 radix track snapshot in Triton extend.** Fixes radix-checkpoint restore for depths off the FLA 64-token chunk grid, where the per-chunk `h` tensor is already rounded to activation dtype (bf16).
- **[#46905](https://github.com/sgl-project/sglang/pull/46905) — Hunyuan3D shape export on GPU.** Builds the `VanillaVolumeDecoder` coordinate grid with `torch.meshgrid` instead of NumPy + host-to-device copy.

**Open performance regressions / trackers**

- **[#35291](https://github.com/sgl-project/sglang/issues/35291) — MLA + fp8 KV materialises the entire per-layer KV pool to bf16 every layer, every forward.** Measured at **43.7% of a decode step** on DeepSeek-R1 / 8×H200 for both `fa3` and `flashinfer_mla` absorbed-MLA paths. Marked `[inactive]` but not fixed — this is the single largest per-token win available on that config.
- **[#44304](https://github.com/sgl-project/sglang/issues/44304) — Qwen3.8-Flash-Next gfx950 / MI355X optimization tracker.** GatedDeltaNet in 36 layers, QuerySparse Attention in 12, HyperConnection gated residual, MXFP4 MoE, native NEXTN MTP.
- **[#45373](https://github.com/sgl-project/sglang/issues/45373) — GLM-5.3-Flash Hopper (SM90) optimization tracker**, part of the Q4 kernel roadmap #42473.
- **[#46812](https://github.com/sgl-project/sglang/pull/46812) — PD prefill bootstrap-queue prefixes now refreshed in the waiting-prefix LRU pass (CLOSED).** Previously requests in `disagg_prefill_bootstrap_queue` were never touched, letting their device prefixes cool.

---

## 5. Stability & Regressions

Ranked by severity:

1. **[#45755](https://github.com/sgl-project/sglang/issues/45755) — Kimi-K3 on SM90: `flashinfer_mxfp4` silently computes GPT-OSS SwiGLU instead of SiTU.** *Silent correctness failure* — no crash, wrong tokens. Reproduces on v0.5.22 and `main` @ `ce66b3ab`. Depends on flashinfer-ai/flashinfer#4209 (still open); prior attempt #32650 closed unmerged. **Treat all Kimi-K3 SM90 MXFP4 output as suspect.** No fix PR yet.
2. **[#43412](https://github.com/sgl-project/sglang/issues/43412) — Speculative decoding kills the server on the first sampled request.** On pip-installed bundles with no system CUDA toolkit, the flashinfer verify-sampling op JIT fails (bundled cccl vs. pip cu13 toolkit mismatch) and SIGKILLs the process tree. Greedy (temp 0) traffic is unaffected. Trivially triggered by any temp>0 request. No fix PR referenced.
3. **[#44291](https://github.com/sgl-project/sglang/issues/44291) — EP2 (`tp=2 + ep=2`) illegal memory access on Qwen3.8-Flash-Next-NVFP4, RTX PRO 6000 / sm_120.** Kernel-level IMA reproduced across two builds. sm_120 + Expert Parallel is not usable for this checkpoint.
4. **[#39300](https://github.com/sgl-project/sglang/issues/39300) — Non-causal sliding-window attention ignored in `TorchNativeAttnBackend`.** The fallback backend silently applies causal masking, producing incorrect attention for non-causal/SWA configs.
5. **[#42752](https://github.com/sgl-project/sglang/issues/42752) — Flaky tests and CI infrastructure failures (134 comments, most active issue).** Tracks recurring NVIDIA `PR Test Base` / `PR Test Extra` flakiness via the `/sglang-pr-babysit` workflow; complements #17050. High comment volume signals ongoing CI instability affecting merge velocity.
6. **[#44070](https://github.com/sgl-project/sglang/issues/44070) — PD prefill early cached-prefix send can transfer KV before HiCache load-back completes (CLOSED).** Decode silently receives stale KV when two requests share a host-resident prefix and `SGLANG_DISAGG_PREFILL_EARLY_SEND_CACHED_PREFIX=1` (default). Worth confirming the fix ordering landed, since the flag remains on by default.
7. **[#27787](https://github.com/sgl-project/sglang/issues/27787) — BF16 RL weight update crash with `moe_runner_backend=flashinfer_trtllm` (CLOSED).** `_load_w13` 64-vs-2048 shape mismatch; padded `w13` layout not restored on the `update_weights_from_tensor` path. Relevant to RL fine-tuning loops.
8. **[#44794](https://github.com/sgl-project/sglang/issues/44794) — AMD `select_experts` casts fp32 MoE correction bias to bf16 (CLOSED).** GLM-5.x on ROCm routed with a bf16 bias despite #37133's fp32 aiter path.

**Also closed, low activity:** #[35148](https://github.com/sgl-project/sglang/issues/35148) (Qwen3.8-27B-FP8 reasoning parse in rust sgl-model-gateway), #[34969](https://github.com/sgl-project/sglang/issues/34969) (HF3FS HiCache `ZeroDivisionError`, DeepSeek-V4), #[35811](https://github.com/sgl-project/sglang/issues/35811) (HiCache IMA, deepseekv4-flash), #[36598](https://github.com/sgl-project/sglang/issues/36598) (DSA `index_topk` override assert), #[35324](https://github.com/sgl-project/sglang/issues/35324) (intermittent CUDA crash, DSPARK on DeepSeek-V4-Flash-0731), #[36822](https://github.com/sgl-project/sglang/issues/36822) (GPTQ Marlin MoE NaNs with BF16 + TP), #[35702](https://github.com/sgl-project/sglang/issues/35702) (`sharded_state` MLA save/load).

---

## 6. What This Means for Application Developers

- **Do not enable speculative decoding on non-greedy traffic in pip-only environments.** [#43412](https://github.com/sgl-project/sglang/issues/43412) is a hard process kill, not a graceful error, and it triggers on the *first* sampled request. Until resolved, either install a matching system CUDA toolkit, pin to greedy sampling, or run without spec decode in production.
- **Validate Kimi-K3 SM90 outputs before trusting them.** [#45755](https://github.com/sgl-project/sglang/issues/45755) is a silent activation-function substitution — benchmark scores and eval harnesses will not flag it. Run a known-good prompt suite against any MXFP4 Kimi-K3 deployment on Hopper.
- **PD disaggregation is the default at scale, and its edge cases are KV-correctness bugs, not perf bugs.** [#44070](https://github.com/sgl-project/sglang/issues/44070) is a stale-KV hazard that was on by default; if you operate PD prefill with HiCache, verify you are on a build containing the fix and re-run prefix-sharing workloads.
- **Expect stricter OpenAI-compat request validation.** [#44047](https://github.com/sgl-project/sglang/pull/44047) moves invalid sampling bounds from "fails deep in the scheduler" to "rejected at the API." Audit client-side parameter ranges and any code passing `max_completion_tokens=0` as an "unset" sentinel.
- **Per-consumer API keys are coming** ([#36666](https://github.com/sgl-project/sglang/pull/36666)), removing the shared-credential workaround for multi-tenant gateways — useful for revoking access per user or per service without a restart.
- **Agent workloads get cache-aware routing.** The Q4 Agentic Cache Scheduling roadmap ([#43098](https://github.com/sgl-project/sglang/issues/43098)) targets supernode-wide L1 (GPU) + L2 (host) KV sharing across engines plus cache-location-aware router placement — the mechanism that makes long-prefix agent loops cheap. Plan for it if you run multi-turn agents against a shared cluster.
- **RAG/embedding is now in scope for the serving layer**, with `nvidia/Nemotron-3-Embed-1B` ([#45885](https://github.com/sgl-project/sglang/pull/45885)) landing natively rather than requiring a sidecar.
- **Hardware caveats:** sm_120 (RTX PRO 6000) + EP2 is currently broken for NVFP4 checkpoints ([#44291](https://github.com/sgl-project/sglang/issues/44291)); consumer Radeon remains unsupported ([#30599](https://github.com/sgl-project/sglang/issues/30599)); Apple Silicon serving is still at RFC stage ([#32321](https://github.com/sgl-project/sglang/issues/32321)).

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-10-28

## 1. Today's Highlights
LiquidAI's LFM3 family landed in force: four model additions across b11938/b11941 (1.7B, VL-2B, Audio-2B, Omni-2B) plus video input support and tool calls on `/v1/realtime` in review. The server layer also got two notable capabilities: chat-template reasoning effort levels are now probed and exposed (b11944), and the router can dynamically unload models via a new device-memory ceiling (`--models-memory-max`, b11930). On the risk side, speculative decoding correctness on quantized targets (#25618) remains the most-discussed open bug, with CUDA-graph hangs on sm_120 close behind.

## 2. Releases & Breaking Changes
- **b11944 — server exposes reasoning effort levels** ([#31271](https://github.com/ggml-org/llama.cpp/pull/31271)): capability probe now renders a user turn for each candidate level (`minimal`, `low`, `medium`, `high`, `xhigh`, `max`) plus an unknown-level control, letting clients enumerate valid effort values. Directly resolves the long-standing 12-👍 request in [#27455](https://github.com/ggml-org/llama.cpp/issues/27455).
- **b11930 — new router memory margin: `--models-memory-max`** ([#21231](https://github.com/ggml-org/llama.cpp/pull/21231)): dynamically unloads models when they would exceed a configured memory threshold, including to-be-loaded model size in the estimate and using `no_alloc` to measure requirements. **Behavioral change for multi-model router deployments** — set this explicitly if you rely on current resident-set behavior.
- **b11940 — deferred tools and `tool_reference` content parts in `common`** ([#31256](https://github.com/ggml-org/llama.cpp/pull/31256)): expands the tool-call parsing surface; downstream chat handlers may need to handle the new content-part type.
- **b11936 — k-pool sparse selection decoupled from `kq_mask` shape** ([#31243](https://github.com/ggml-org/llama.cpp/pull/31243)): qwen4exp (QSA) and glm5-next now index cache cells directly rather than reading `kq_mask` values. Internal, but relevant if you carry custom kernels for these architectures.
- **b11935 — RPC hardening** ([#31204](https://github.com/ggml-org/llama.cpp/pull/31204)): catches `std::length_error` in `recv_msg` instead of terminating.
- Note: [prebuilt Windows CUDA binaries are reported frozen at b11828](https://github.com/ggml-org/llama.cpp/issues/31085) while tags advance — verify the asset you download rather than trusting the release name.

## 3. New Model & Hardware Support
- **LFM3-1.7B and LFM3-VL-2B** ([#31252](https://github.com/ggml-org/llama.cpp/pull/31252), b11938).
- **LFM3-Audio-2B and LFM3-Omni-2B** ([#31257](https://github.com/ggml-org/llama.cpp/pull/31257), b11941) — with `config.json` parsed as released.
- **LiquidAI d1-2B / d1-omni-2B** decision models ([#31275](https://github.com/ggml-org/llama.cpp/pull/31275), merged).
- **Video input for the LFM3 family in mtmd** ([#31283](https://github.com/ggml-org/llama.cpp/pull/31283), open) — includes video+ soundtrack capability matrix.
- **`/v1/realtime` expansion**: tool calls ([#31287](https://github.com/ggml-org/llama.cpp/pull/31287)) and voice chat / interleaved transcription ([#31269](https://github.com/ggml-org/llama.cpp/pull/31269)), both targeting LFM3-Audio/Omni GGUFs.
- **GigaChat 3.5 432B-A28B** (DeepSeek-V3-style MLA + MoE, hybrid attention) proposed in [#25342](https://github.com/ggml-org/llama.cpp/pull/25342).
- **Hexagon backend**: `DSV4_HC_PRE`/`DSV4_HC_POST` ops added ([#30953](https://github.com/ggml-org/llama.cpp/pull/30953)); all `test-backend-ops` pass.
- **Backend build fix**: clang-cl + `-mavx512bf16` build repaired ([#31276](https://github.com/ggml-org/llama.cpp/pull/31276)).
- Gap: **FP4 quantization lacks round-to-even** ([#31012](https://github.com/ggml-org/llama.cpp/issues/31012)) — quantize output differs from reference rounding.

## 4. Performance & Optimization
- **MoE expert cache rework** ([#31233](https://github.com/ggml-org/llama.cpp/pull/31233)): LRFU/MQ-inspired class-ranked cache replacing SLRU with decaying usage history. Measured **+6% decode on QFN, +7% on GLM, 8% fewer expert uploads** with a warm cache. A follow-up adds `--moe-cache-cpu-share auto` with CRF admission for CPU misses ([#31286](https://github.com/ggml-org/llama.cpp/pull/31286)).
- **Hexagon TOP_K kernel** ([#31279](https://github.com/ggml-org/llama.cpp/pull/31279)): backend sampler was the largest draft cost on X2 Elite — **42% of the DFlash draft batch, 25% of DSpark**. Now avoids full per-tile sorting.
- **CUDA graph re-instantiation on cluster-dim changes** ([#31284](https://github.com/ggml-org/llama.cpp/pull/31284)): Hopper cuBLAS F16 GEMMs pick different thread-block clusters per shape (e.g. m=1200 → cluster 5,1,1; m=1040 → 2,2,1); stale graphs previously reused the wrong launch config.
- **SYCL VRAM overcommit guard** ([#31285](https://github.com/ggml-org/llama.cpp/pull/31285)): refuses device buffers that would exceed VRAM, avoiding kernel panics on `-ngl 999` with oversize models.
- **KV/prefix sharing between slots** ([#30970](https://github.com/ggml-org/llama.cpp/pull/30970)): `--slot-attach` lets sessions share a prefix held by another slot instead of duplicating it in cache — meaningful memory savings for multi-session agents.
- **Vulkan MUL_MAT dim-3 broadcast** ([#31239](https://github.com/ggml-org/llama.cpp/pull/31239), b11933): `supports_op` no longer rejects all `MUL_MAT` with differing `ne3`; also unblocks llvmpipe CI.
- **Test harness speedup** ([#31145](https://github.com/ggml-org/llama.cpp/pull/31145), b11934): `save_models` no longer constructs an unneeded `llama_context`, and slow `std::normal_distribution` draws were replaced with a faster splitmix path.
- **Speculative decoding / MTP**: draft-vocabulary trim via `d2t` mapping for qwen35/qwen35moe ([#29143](https://github.com/ggml-org/llama.cpp/pull/29143)); unloaded MTP layers excluded from GPU placement counts ([#31278](https://github.com/ggml-org/llama.cpp/pull/31278)).

## 5. Stability & Regressions
Ranked by severity:

1. **[#25618](https://github.com/ggml-org/llama.cpp/issues/25618) — Speculative decoding diverges from vanilla under greedy sampling on quantized targets** (32 comments). `temperature=0, top_k=1` with draft-model speculation produces different text than non-speculative on Q4_K_M etc., while bf16 targets match. Highest-impact open correctness bug; no fix PR yet.
2. **[#27330](https://github.com/ggml-org/llama.cpp/issues/27330) — CUDA graphs hang the GPU channel on RTX 5090 Laptop / sm_120** (RC watchdog + Xid 8). Workaround is complete: `GGML_CUDA_DISABLE_GRAPHS=1`. No fix PR.
3. **[#29054](https://github.com/ggml-org/llama.cpp/issues/29054) — Vulkan GPU hang + SIGABRT on Intel Gen9.5 (UHD 630) with q8_0 KV cache**; `vk::Error` escapes as `std::terminate`. Deterministic.
4. **[#27560](https://github.com/ggml-org/llama.cpp/issues/27560) — llama-server access violation (0xC0000005) on Windows/Vulkan with default ctx-checkpoints** (Qwen3.8-27B).
5. **[#29494](https://github.com/ggml-org/llama.cpp/issues/29494) — `repeat_last_n` / `dry_penalty_last_n` unbounded**: multi-GB zero-filled sampler allocations, server OOM. Treat as a DoS vector if exposing sampler params to untrusted clients.
6. **[#29002](https://github.com/ggml-org/llama.cpp/issues/29002) — Hybrid LFM2 co-batching corrupts recurrent/short-conv state**: decoding >1 sequence in the same ubatch changes output; greedy output can stop mid-sentence. Directly relevant to the LFM3 hybrid work landing today.
7. **[#28581](https://github.com/ggml-org/llama.cpp/issues/28581) — IQ3_S produces garbage on RTX 5060 Ti (Blackwell)**.
8. **[#31217](https://github.com/ggml-org/llama.cpp/pull/31217) — `ggml_nbytes()` integer overflow in ggml-rpc → OOB read/write** with maliciously shaped tensors. **Fix PR open**; relevant for untrusted RPC peers.
9. **[#31220](https://github.com/ggml-org/llama.cpp/issues/31220) — Speculative decode performance drop** on aarch64 server builds (regression, unconfirmed).
10. **[#31029](https://github.com/ggml-org/llama.cpp/issues/31029) — SYCL bf conversion instruction unsupported on Arrow Lake iGPU**.
11. **[#31163](https://github.com/ggml-org/llama.cpp/issues/31163) — Vulkan `count_experts.comp` `zero_skipped_rows()` loops over every expert id per thread** (perf bug from #26631).
12. Fixed today: **mtmd-cli stale batch after `/clear`** ([#31155](https://github.com/ggml-org/llama.cpp/issues/31155)) → reduced batch-pointer scope in b11932 ([#31165](https://github.com/ggml-org/llama.cpp/pull/31165)); **DSV4.1 GSQ-RCO load failure** ([#31263](https://github.com/ggml-org/llama.cpp/issues/31263)) closed; **Vulkan Navi 10 GPU ring hangs** ([#28807](https://github.com/ggml-org/llama.cpp/issues/28807), [#28808](https://github.com/ggml-org/llama.cpp/issues/28808)) closed stale. Also note `update_slots()` not propagating `batch_view` offset to `ctx_dft` was closed ([#24840](https://github.com/ggml-org/llama.cpp/issues/24840)).
13. Tooling/CI: **MSVC doesn't detect AVX-VNNI** ([#28295](https://github.com/ggml-org/llama.cpp/issues/28295)), and `json-schema-to-grammar` copies raw control bytes verbatim, breaking on NUL in schema patterns — fix in [#31241](https://github.com/ggml-org/llama.cpp/pull/31241), which matters if you accept user-supplied JSON schemas.

## 6. What This Means for Application Developers
- **Reasoning effort is now discoverable, not guessed.** The b11944 probe lets your client ask the server which levels (`minimal`…`max`) the loaded chat template actually honors, so you can map app-level "thinking budget" to template-supported values instead of emitting levels the model silently ignores. Pair with the closed graceful-budget request ([#20632](https://github.com/ggml-org/llama.cpp/issues/20632)) when designing truncation UIs.
- **Agents get better tool plumbing.** `tool_reference` content parts and deferred tools (b11940) plus `/v1/realtime` tool calls ([#31287](https://github.com/ggml-org/llama.cpp/pull/31287)) mean an OpenAI-shaped tool loop can now span text, audio, and video inputs without a side channel. Validate that your parser handles the new content-part type before rolling forward.
- **Voice/omni agents are viable on CPU-class hardware** with LFM3-Audio/Omni-2B and the realtime transcription endpoints — but avoid co-batching multiple sequences of hybrid LFM models until [#29002](https://github.com/ggml-org/llama.cpp/issues/29002) is resolved; serialize those sessions or use separate contexts.
- **Multi-tenant model routers should adopt `--models-memory-max`** (b11930). It converts hard OOM failures into deterministic evictions, but changes residency semantics — audit any latency SLO that assumed warm models stay loaded.
- **Do not ship speculative decoding with quantized draft/target pairs** without output-equality tests against vanilla decoding ([#25618](https://github.com/ggml-org/llama.cpp/issues/25618)). On sm_120 (RTX 50-series) hosts, set `GGML_CUDA_DISABLE_GRAPHS=1` until [#27330](https://github.com/ggml-org/llama.cpp/issues/27330) lands.
- **Cap sampler parameters server-side.** Unbounded `repeat_last_n`/`dry_penalty_last_n` ([#29494](https://github.com/ggml-org/llama.cpp/issues/29494)) turns a single request into gigabytes of allocation — a one-line validation that every hosted deployment should add today.
- **Pin and verify binaries.** Windows CUDA prebuilts lagging at b11828 ([#31085](https://github.com/ggml-org/llama.cpp/issues/31085)) means "latest release" is not a reliable proxy for "latest code" — check the embedded build number, and self-build if you need today's LFM3 or reasoning-effort changes.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-10-28

## 1. Today's Highlights
No releases landed in the last 24 hours. Activity is concentrated on model enablement and runtime hardening: LFM3 support is being fixed in [PR #19344](https://github.com/ollama/ollama/pull/19344) after users hit [issue #19347](https://github.com/ollama/ollama/issues/19347) and [issue #19343](https://github.com/ollama/ollama/issues/19343), while MLX gains concurrent generation in [PR #19197](https://github.com/ollama/ollama/pull/19197) and CUDA startup crash mitigation in [PR #18978](https://github.com/ollama/ollama/pull/18978). High-impact unresolved reports remain around cloud reasoning loops [#17892](https://github.com/ollama/ollama/issues/17892), Qwen3.8 tool-loop 500s [#17778](https://github.com/ollama/ollama/issues/17778), and macOS Metal/MLX crashes [#19278](https://github.com/ollama/ollama/issues/19278), [#18885](https://github.com/ollama/ollama/issues/18885).

## 2. Releases & Breaking Changes
No new releases in the last 24h and no confirmed API/config breaking changes. Compatibility work is in flight but unreleased: stream error forwarding [PR #18953](https://github.com/ollama/ollama/pull/18953), Anthropic thinking-block streaming [PR #19110](https://github.com/ollama/ollama/pull/19110), and OpenAI developer-instruction handling [PR #19199](https://github.com/ollama/ollama/pull/19199) (closed).

## 3. New Model & Hardware Support
- **LFM3 GGUF fixes** — [PR #19344](https://github.com/ollama/ollama/pull/19344) (open) fixes BOS handling for LFM3 GGUFs and adds GGUF text-to-speech support; addresses [issue #19347](https://github.com/ollama/ollama/issues/19347) (`unknown model architecture: 'lfm3'`, closed) and [issue #19343](https://github.com/ollama/ollama/issues/19343) (MLX runtime unavailable).
- **Nemotron 3.5 Bolt 74B-A9B** — [PR #19229](https://github.com/ollama/ollama/pull/19229) (open) adds per-layer expert configurations, LiLiCorr speculative decoding, prequantized affine 3-bit import, and mixed-precision expert stacking.
- **Kolibri 1** — [PR #18780](https://github.com/ollama/ollama/pull/18780) (open) adds MLX support.
- **MLX concurrent generation** — [PR #19197](https://github.com/ollama/ollama/pull/19197) (open) enables multiple simultaneous generation requests via `OLLAMA_NUM_PARALLEL`.
- **CUDA/Blackwell startup** — [PR #18978](https://github.com/ollama/ollama/pull/18978) (open) retries `llama-server` startup with eager CUDA module loading, targeting shared-object initialization failures on RTX 5060 Ti/Blackwell; linked to [issue #19122](https://github.com/ollama/ollama/issues/19122).
- **GPU discovery fix** — [PR #19338](https://github.com/ollama/ollama/pull/19338) (open) corrects selection of the newest library version supporting all GPUs.
- **Feature requests only** — GPU acceleration detection in the desktop UI [issue #19301](https://github.com/ollama/ollama/issues/19301); AMD RX 6750 GRE 10GB support [issue #19329](https://github.com/ollama/ollama/issues/19329).

## 4. Performance & Optimization
- **MLX parallelism** — [PR #19197](https://github.com/ollama/ollama/pull/19197) allows MLX models to serve multiple requests concurrently, reducing time-to-first-token for queued requests. Default remains one request; memory must be provisioned per active request.
- **CUDA startup retry** — [PR #18978](https://github.com/ollama/ollama/pull/18978) mitigates partial-offload warmup crashes with eager module loading. No throughput numbers provided.
- **Scheduler eviction** — [issue #19326](https://github.com/ollama/ollama/issues/19326) reports `findRunnerToUnload` sorts by `sessionDuration` then model path; with uniform `keep_alive`, eviction becomes arbitrary rather than size/recency-based. No fix PR listed.
- **`OLLAMA_NUM_PARALLEL=1` semantics** — [issue #19262](https://github.com/ollama/ollama/issues/19262) reports two sessions still running concurrently despite the setting. No fix PR listed.
- **Vulkan regression** — [issue #13567](https://github.com/ollama/ollama/issues/13567) (closed) reported gpt-oss slow on Vulkan versus CPU/llama.cpp; no fix details in the provided data.

## 5. Stability & Regressions

### High severity
- **macOS Metal crashes / MLX panics** — [issue #19278](https://github.com/ollama/ollama/issues/19278) reports `mlx runner failed: panic: mlx: [METAL] Command buffer execution failed` on Ollama 0.40.9; [issue #18885](https://github.com/ollama/ollama/issues/18885) reports MLX runner panic on `gemma4:e2b-mlx`. No fix PR listed.
- **CUDA shared-object initialization failure** — [issue #19122](https://github.com/ollama/ollama/issues/19122) on RTX 5060 Ti/Blackwell, Windows, with partial offload. Fix PR [PR #18978](https://github.com/ollama/ollama/pull/18978) is open.
- **Cloud thinking loop** — [issue #17892](https://github.com/ollama/ollama/issues/17892): `deepseek-v4-flash:0731` repeated the same reasoning block 221 times over ~1m45s with zero usable output. No fix PR listed.
- **Qwen3.8 tool-loop 500** — [issue #17778](https://github.com/ollama/ollama/issues/17778): `ResponseError during chat streaming: no user query found in messages` during tool-calling loops; 56 comments, no fix PR listed.
- **Windows connection resets** — [issue #19139](https://github.com/ollama/ollama/issues/19139): `/api/chat` resets after ~15s with large prompts on Windows/RTX 3090; marked needs more info.
- **Pull resume corruption** — [issue #19337](https://github.com/ollama/ollama/issues/19337): restarting the server more than an hour into a pull breaks resume with missing partial files or digest mismatch. Fix PR [PR #19339](https://github.com/ollama/ollama/pull/19339) is open.
- **Silent tool-call drop** — [issue #19187](https://github.com/ollama/ollama/issues/19187): calls to functions not in the request’s `tools` are dropped silently, yielding an empty response with `eval_count > 0`. No fix PR listed.
- **Broken low-bit Qwen2.5-Coder artifacts** — [issue #18252](https://github.com/ollama/ollama/issues/18252): `qwen2.5-coder:3b-instruct` `q2_K`/`q3_K_S`/`q3_K_M`/`q3_K_L` score 0/15 on a functional smoke suite while sibling quants pass 87–100%. No fix PR listed.

### Medium severity
- **OpenAI tool-call misattachment** — [issue #19151](https://github.com/ollama/ollama/issues/19151) (closed): assistant `tool_calls` attached to the previous message when `content: []`.
- **Cloud Responses 413** — [issue #19155](https://github.com/ollama/ollama/issues/19155) (closed): ~10.7 MiB coding requests rejected despite being below advertised context limits.
- **`/v1/systemone` decision models** — [issue #19292](https://github.com/ollama/ollama/issues/19292): GGUF models with `CAPABILITY decision` but no `decision.type` are refused since 0.40.4.
- **DNS64 pull failure** — [issue #19345](https://github.com/ollama/ollama/issues/19345): `ollama pull hf.co/...` fails behind DNS64 because NAT64 CDN addresses are treated as non-public.
- **LFM3 runtime mismatch** — [issue #19343](https://github.com/ollama/ollama/issues/19343): `lfm3-audio:latest` requires MLX support that is unavailable; [issue #19347](https://github.com/ollama/ollama/issues/19347) (closed) reported unknown `lfm3` architecture before the fix PR.
- **Scheduler eviction behavior** — [issue #19326](https://github.com/ollama/ollama/issues/19326): arbitrary model eviction when `keep_alive` ties.

### Low severity
- **Modelfile round-trip bug** — [issue #19342](https://github.com/ollama/ollama/issues/19342): `ollama show --modelfile` emits unparseable output when values contain `"""` followed by whitespace.
- **REPL state bugs** — [issue #19340](https://github.com/ollama/ollama/issues/19340): Ctrl+C during multiline input leaves REPL in multiline mode; [issue #19341](https://github.com/ollama/ollama/issues/19341): `/load` does not reset the system prompt consistently.
- **ShieldGemma template** — [issue #19313](https://github.com/ollama/ollama/issues/19313): template classifies “an AI response” when applied to a user prompt.

## 6. What This Means for Application Developers
- **Pin and stage upgrades.** Avoid standardizing on 0.40.x MLX or Blackwell CUDA configurations until [#19278](https://github.com/ollama/ollama/issues/19278), [#18885](https://github.com/ollama/ollama/issues/18885), and [#19122](https://github.com/ollama/ollama/issues/19122) are resolved.
- **Add loop and timeout detection for cloud reasoning models.** [#17892](https://github.com/ollama/ollama/issues/17892) can consume minutes and produce no output; enforce max reasoning/token budgets and retry with a different model.
- **Validate tool calls defensively.** Unknown tool names may be silently dropped ([#19187](https://github.com/ollama/ollama/issues/19187)); check for empty responses with nonzero `eval_count`. Do not assume `tool_calls` survive empty content arrays ([#19151](https://github.com/ollama/ollama/issues/19151)).
- **Handle in-band stream errors.** Until [PR #18953](https://github.com/ollama/ollama/pull/18953) is released, clients may receive blank chunks instead of a propagated error after the first token.
- **Budget memory for MLX concurrency.** [PR #19197](https://github.com/ollama/ollama/pull/19197) can improve TTFT via `OLLAMA_NUM_PARALLEL`, but each parallel request needs additional memory.
- **Avoid known-bad quantizations.** Do not use `q2_K`/`q3_K_*` variants of `qwen2.5-coder:3b-instruct` for code tasks; use higher-bit siblings ([#18252](https://github.com/ollama/ollama/issues/18252)).
- **Make pulls resumable and restart-safe.** Add retry logic and avoid restarting `ollama serve` mid-pull until [PR #19339](https://github.com/ollama/ollama/pull/19339) lands ([#19337](https://github.com/ollama/ollama/issues/19337)).
- **Control model residency explicitly.** With uniform `keep_alive`, scheduler eviction may pick by model path rather than recency ([#19326](https://github.com/ollama/ollama/issues/19326)); set differentiated `keep_alive` values or preload critical models.
- **Private OCI registries remain a gap.** Auth-enabled pull/push is still requested in [#9215](https://github.com/ollama/ollama/issues/9215) and [#4204](https://github.com/ollama/ollama/issues/4204); plan around it for air-gapped/private deployments.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-10-28

## 1. Today's Highlights

Four patch releases shipped across the maintained stable lines (v1.106.5, v1.105.6, v1.104.8, v1.103.10), all cosign-verifiable, while `main` has moved onto the 1.109.0 line with the Admin UI bundle rebuilt. The most urgent open item is a security backport request: 1.106.x Docker images still trust the compromised **emSign ECC Root CA – G3**, with the fix ([#47670](https://github.com/BerriAI/litellm/pull/47670)) landed only on `main`. On the correctness side, a cluster of streaming/`/v1/messages` billing and slot-leak bugs closed today, several of which silently under-billed production traffic.

---

## 2. Releases & Breaking Changes

| Version | Notes |
|---|---|
| [v1.106.5](https://github.com/BerriAI/litellm/releases/tag/v1.106.5) | Patch on the 1.106 stable line. Verify with `cosign verify --key .../cosign.pub ghcr.io/berriai/litellm:v1.106.5` (same for `litellm-database`, `litellm-non_root`, `docker.io/litellm/*`). |
| [v1.105.6](https://github.com/BerriAI/litellm/releases/tag/v1.105.6) | Patch on 1.105. |
| [v1.104.8](https://github.com/BerriAI/litellm/releases/tag/v1.104.8) | Patch on 1.104. |
| [v1.103.10](https://github.com/BerriAI/litellm/releases/tag/v1.103.10) | Patch on 1.103. |

Release-process notes:
- [#48673](https://github.com/BerriAI/litellm/pull/48673) — bumps `main` to the **1.109.0** line (`litellm-enterprise` → 0.1.80). The 1.108 line never got a tag after the cadence stalled; **1.108.0 is going out as an RC from `rc/1.108.0`** and `main` restarts at 1.109. Plan upgrade paths accordingly — 1.108.0 is not a normal stable cut.
- [#48692](https://github.com/BerriAI/litellm/pull/48692) — Admin UI bundle rebuilt from current `main` for 1.109.0; previously shipped UI was built from older dashboard sources and was missing pages (e.g. chat model hub).

⚠️ **Security backport outstanding:** [#47904](https://github.com/BerriAI/litellm/issues/47904) (OPEN) — every published **1.106.x** image still trusts `emSign ECC Root CA - G3`. The fix in [#47670](https://github.com/BerriAI/litellm/pull/47670) is `main`-only as of today. If you run 1.106.x with outbound TLS, track this closely.

---

## 3. New Model & Hardware Support

- **Databricks OpenJev decision model pricing** — [#48694](https://github.com/BerriAI/litellm/pull/48694): `databricks/databricks-openjev-qwen35-4b` was mapped at 0.0 cost, so all `/v1/decisions` and `/v1/systemone` calls logged $0 spend. Now priced at Databricks' published **0.6 DBU per 1M input tokens** ($0.070/DBU).
- **Cloudflare Workers AI expansion (still unmerged)** — [#21115](https://github.com/BerriAI/litellm/issues/21115) (OPEN, stale): provider implementation is ~10 months behind Cloudflare's expanded API. Companion feature requests for image support ([#35055](https://github.com/BerriAI/litellm/issues/35055)) and audio/STT/TTS support ([#35056](https://github.com/BerriAI/litellm/issues/35056)) remain open and stale.
- **Tavily search passthrough** — [#48684](https://github.com/BerriAI/litellm/pull/48684) (OPEN): now forwards `raw_content`, `score`, `favicon`, and the response envelope. Previously callers paid for `include_raw_content` and received only title/url/excerpt.
- **Passthrough model attribution** — [#48275](https://github.com/BerriAI/litellm/pull/48275) (CLOSED): upstreams can report a model via `x-litellm-upstream-model`, so product endpoints without a `model` field no longer collapse into the `unknown` spend bucket.
- **SAP tool-call replay** — [#48696](https://github.com/BerriAI/litellm/pull/48696) (OPEN): replayed assistant tool calls that omit `arguments` now send an empty string instead of failing locally.

No new CUDA/ROCm/Metal backends or quantization formats in this window.

---

## 4. Performance & Optimization

- **Streaming fast path restored for request-only guardrails** — [#47735](https://github.com/BerriAI/litellm/pull/47735) (OPEN): a prompt-only guardrail previously forced per-chunk response inspection on *every* streamed reply, asking the guardrail about each piece. The check is now skipped unless something actually inspects streamed chunks. Meaningful latency win for streams behind guardrails.
- **Multimodal quota estimation** — [#48099](https://github.com/BerriAI/litellm/pull/48099) (OPEN): image-heavy Responses requests were counted as text and hit token-limit rejections, while Chat image/audio requests under-reserved. Media is now estimated separately (image model policies, audio duration) and shared across proxy paths.
- **Rust bridge made mandatory** — [#46346](https://github.com/BerriAI/litellm/pull/46346) (OPEN): `_native` already ships in every wheel and the sdist won't install without it; this removes the optional-load path and dead `None` branches. Stacked on [#46442](https://github.com/BerriAI/litellm/pull/46442) which caches the Rust build for CI.
- **Embedding timeout propagation** — [#48690](https://github.com/BerriAI/litellm/pull/48690) (CLOSED) / [#47305](https://github.com/BerriAI/litellm/pull/47305) (CLOSED): Gemini and Vertex `embed_content` sync/async paths ignored the caller's timeout and used a 300s handler default — a 50 ms timeout still returned real embeddings after ~1s. Now forwarded correctly (also for batch handlers).

---

## 5. Stability & Regressions

Ranked by severity.

**Critical**

- 🔴 **Compromised root CA still trusted in 1.106.x images** — [#47904](https://github.com/BerriAI/litellm/issues/47904) (OPEN). Fix exists on `main` only. No backport PR yet.

**High — billing correctness**

- 🟠 **Streaming requests billed at $0 when `provider_response_model` is a slug absent from the price map** (e.g. dated Anthropic builds) — [#42161](https://github.com/BerriAI/litellm/issues/42161) (CLOSED). Reporter saw **74 of 89 billable calls logged as free**; non-streaming was costed correctly. Fix landed.
- 🟠 **Virtual-key `BudgetExceededError` uses stale spend** while `/key/info` reports spend below `max_budget` — [#27735](https://github.com/BerriAI/litellm/issues/27735) (OPEN, 12 comments, most-discussed issue of the day). Related to #27639; team-scoped virtual keys affected.
- 🟠 **Budget reservation sized with `max_input_tokens`** when `/v1/messages` carries an Anthropic-format `tool_choice` (`{"type":"auto"|"any"|"tool"}`), via a `token_counter` KeyError — [#47396](https://github.com/BerriAI/litellm/issues/47396) (CLOSED). Inflated reservations / spurious rejections.
- 🟠 **SpendLogs silently dropped since v1.99.0** for providers returning `id: null` (Databricks-Gemini) — `request_id` became `"None"` and collided on the primary key — [#39749](https://github.com/BerriAI/litellm/issues/39749) (CLOSED).
- 🟡 **SpendLogs partition boundaries misaligned when DB session TZ ≠ UTC** — [#34364](https://github.com/BerriAI/litellm/issues/34364) (OPEN, enhancement).

**High — resource leaks / routing**

- 🟠 **`websearch_interception` leaks one `max_parallel_requests` slot per streamed `/v1/messages` request** carrying a `web_search` tool — [#46093](https://github.com/BerriAI/litellm/issues/46093) (CLOSED). The v3 limiter's pre-call hook acquires the slot; the agentic loop never releases it, permanently shrinking key capacity.
- 🟡 **Structured provider-error / route-health contract from router cooldown state** — [#33371](https://github.com/BerriAI/litellm/issues/33371) (OPEN, RFC). Cooldown state exists internally but isn't machine-readable.

**Medium — protocol/translation**

- 🟡 **`/v1/messages` adapter emits `message_start` with `usage.input_tokens: 0`** (all four usage counts zero) when streaming through the Chat Completions adapter — [#48481](https://github.com/BerriAI/litellm/issues/48481) (CLOSED). Real counts only arrive in `message_delta`; non-streaming is correct. Breaks early token accounting in clients.
- 🟡 **`reasoning_effort: "none"` removes thinking config instead of disabling it** on default-on Claude models (e.g. `anthropic/claude-haiku-5-5`) — [#47924](https://github.com/BerriAI/litellm/issues/47924) (OPEN). Upstream default wins; cannot currently force thinking off.
- 🟡 **Assistant text order lost around `tool_use`** in the Responses adapter — `[text, tool_use, text]` became `[function_call, message(text+text)]`, replaying pre-tool text after the call — [#45478](https://github.com/BerriAI/litellm/pull/45478) (OPEN).
- 🟡 **Malformed Messages `tool_choice` returns 500 or silently becomes `auto`** — [#46188](https://github.com/BerriAI/litellm/pull/46188) (OPEN). Now rejected with 400; **intentional product change**.
- 🟡 **Decisions transformations materialize omitted probabilities as `0.0`** — [#48449](https://github.com/BerriAI/litellm/issues/48449) (CLOSED). Missing ≠ zero; information loss.
- 🟡 **Responses streaming breaks on omitted `logprobs`** (AttributeError on `event.part.text`) — [#48375](https://github.com/BerriAI/litellm/pull/48375), [#48686](https://github.com/BerriAI/litellm/pull/48686) (CLOSED).

**Medium — proxy/API behavior**

- 🟡 **Token-count input precedence regression from #46839** — `prompt` alongside non-empty `messages`/`contents` now 400s unless `drop_params` is on — [#48693](https://github.com/BerriAI/litellm/pull/48693) (OPEN, restore legacy precedence).
- 🟡 **BYOK / static-auth MCP `tools/list` rejects per-request `x-mcp-{alias}-authorization`** — [#46664](https://github.com/BerriAI/litellm/issues/46664) (OPEN).
- 🟡 **Non-object JSON bodies** (`[]`, `123`, `null`) return **500** instead of **400** in `pre_db_read_auth_checks` — [#43711](https://github.com/BerriAI/litellm/issues/43711) (CLOSED, `AttributeError: 'list' object has no attribute 'get'`).
- 🟡 **Enterprise-only `get_spend_routes` permission yields a bare 500** on `/key/generate` and `/user/new`; the licence message only reaches the log — [#47011](https://github.com/BerriAI/litellm/issues/47011) (CLOSED).
- 🟡 **`mock_response=""` is falsy → real provider request fires** in `completion()`/`acompletion()` — [#48162](https://github.com/BerriAI/litellm/issues/48162) (CLOSED). Test-safety hazard: empty mock strings leaked live traffic.
- 🟡 **`hide-secrets` guardrail ignored `skip_tool_message_in_guardrail`** — [#48689](https://github.com/BerriAI/litellm/pull/48689) (OPEN). Tool results were being scrubbed despite the flag; tool args/user text/system prompts still redacted.

**Low — UI / observability**

- [#23998](https://github.com/BerriAI/litellm/issues/23998) (CLOSED) — LiteLLM Params can't be removed once added in the UI.
- [#32332](https://github.com/BerriAI/litellm/issues/32332) (CLOSED) — Logs UI shows "Request/Response Data Not Available" when `request_id` contains `#`.
- [#34763](https://github.com/BerriAI/litellm/issues/34763) (CLOSED) — MCP server UI omits `authorization` auth_type though the API accepts it.
- [#35064](https://github.com/BerriAI/litellm/issues/35064) (OPEN, stale) — `enable_background_health_checks` not findable in UI.
- [#33690](https://github.com/BerriAI/litellm/issues/33690) (CLOSED) — SSO role value mismatch (`internal_user_view_only` vs `internal_user_viewer`) in docs.
- [#19730](https://github.com/BerriAI/litellm/issues/19730) (OPEN, 15 👍) — i18n / multi-language dashboard, still the most-upvoted open request.
- [#48688](https://github.com/BerriAI/litellm/pull/48688) (OPEN) — Guardrail wizard nav buttons escaping the dialog.
- [#48600](https://github.com/BerriAI/litellm/pull/48600) (OPEN) — `LITELLM_FIPS_MODE=true` startup gate; refuses to boot unless FIPS crypto + TLS verification are actually active. Redo of reverted #42700 with a corrected probe.
- [#42697](https://github.com/BerriAI/litellm/pull/42697) (OPEN) — with `LITELLM_OTEL_V2=true`, key/user/team/org identity now emitted as first-class Langfuse metadata fields on chat, `/v1/messages`, and `/v1/responses` instead of a raw attribute blob.
- [#34734](https://github.com/BerriAI/litellm/issues/34734) (OPEN) — capability-based upstream quota/balance probing in the SDK.

---

## 6. What This Means for Application Developers

1. **Audit streaming spend before trusting your dashboards.** Two independent bugs ([#42161](https://github.com/BerriAI/litellm/issues/42161), [#39749](https://github.com/BerriAI/litellm/issues/39749)) caused streamed calls to log $0 or vanish entirely. If you route most traffic through streaming — especially via Databricks/Gemini or dated Anthropic model slugs — reconcile SpendLogs against provider invoices before the fixes propagate to your pinned version.
2. **Pin and verify, don't float.** Four parallel patch lines are live and 1.108.0 is an RC rather than a stable tag. Use the cosign commands above and treat 1.106.x as security-flagged until the eMudhra root CA backport lands ([#47904](https://github.com/BerriAI/litellm/issues/47904)).
3. **`/v1/messages` remains the highest-churn surface.** Tool-call ordering ([#45478](https://github.com/BerriAI/litellm/pull/45478)), malformed `tool_choice` now returning 400 ([#46188](https://github.com/BerriAI/litellm/pull/46188)), zeroed `message_start` usage ([#48481](https://github.com/BerriAI/litellm/issues/48481)), and budget over-reservation with Anthropic `tool_choice` ([#47396](https://github.com/BerriAI/litellm/issues/47396)) all touch the Anthropic-compatible path. Agents that stream tool use should test against a pinned version explicitly.
4. **Streaming capacity can silently erode.** The `web_search` slot leak ([#46093](https://github.com/BerriAI/litellm/issues/46093)) permanently consumes `max_parallel_requests` per affected stream — if a long-lived key degrades over hours, recycle it and check this class of leak before scaling out.
5. **Guardrail latency tax is going away.** If you disabled streaming or dropped guardrails for latency, [#47735](https://github.com/BerriAI/litellm/pull/47735) restores the streaming fast path for prompt-only guardrails. Similarly, embedding timeouts now actually apply ([#48690](https://github.com/BerriAI/litellm/pull/48690)) — remove any client-side timeout workarounds.
6. **Don't rely on `mock_response=""`.** It routes to the real provider ([#48162](https://github.com/BerriAI/litellm/issues/48162)). Use a non-empty sentinel or `mock_tool_calls`/`mock_timeout` in tests.
7. **FIPS and OTEL v2 are opt-in but landing.** [#48600](https://github.com/BerriAI/litellm/pull/48600) gives you a fail-closed FIPS startup check; [#42697](https://github.com/BerriAI/litellm/pull/42697) makes per-request identity queryable in Langfuse under the OTEL v2 mapper — useful if you're instrumenting multi-tenant agent traffic.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-10-28

## Today's Highlights
No new releases landed in the last 24 hours; activity is concentrated on Unsloth Studio hardening and correctness fixes. The most consequential items are a Windows desktop training bug that floods the Recycle Bin with SQLite WAL/SHM files, an Anthropic-compatibility regression where `/v1/messages` returns a hard context-overflow error instead of auto-compacting, and a pair of FP8/Block Swap fixes for `Qwen3.8-Flash-Next`. Model-support demand is trending toward non-NVIDIA hardware (Intel Arc XPU, mixed NVIDIA/AMD Linux) and the new LiquidAI LFM3 family.

## New Model & Hardware Support
- **LFM3 family request** — [#15588](https://github.com/unslothai/unsloth/issues/15588) asks for `LFM3-1.7B` support following LiquidAI's LFM3 release (2 👍). No implementation PR yet.
- **Intel Arc B580 (XPU) fine-tuning** — [#15583](https://github.com/unslothai/unsloth/issues/15583) reports the desktop app on Windows 11 does not offer a path for Intel XPU training. Still open with 5 comments; no backend PR linked.
- **AMD GPUs invisible on mixed NVIDIA/AMD Linux** — [#15444](https://github.com/unslothai/unsloth/issues/15444) shows the Studio web UI failing to enumerate AMD devices alongside an NVIDIA card, specifically with a custom `llama.cpp` build on Kali. Relevant for heterogenous inference hosts.
- **ExLlamaV3 as an inference engine** — [#15527](https://github.com/unslothai/unsloth/issues/15527) requests `exllamav3` support and EXL3 quant sorting; the requester notes the existing community PR has stalled, so this is not imminent.
- **Gemma 4 26B on Apple MLX (vision)** — [#15473](https://github.com/unslothai/unsloth/issues/15473) reports the vision path emitting `<unused6226>` tokens on macOS 27.1 / MLX, i.e. a tokenizer or multimodal projector mismatch rather than a missing architecture.
- **Qwen3.8-Flash-Next (FP8, `qwen4_exp`)** — loading defects tracked in [#15632](https://github.com/unslothai/unsloth/pull/15632) and [#15633](https://github.com/unslothai/unsloth/pull/15633); this is now the most actively patched new architecture in the repo.

## Performance & Optimization
- **Block Swap `offload_layers` never engages on Qwen3.8-Flash-Next** — [#15633](https://github.com/unslothai/unsloth/pull/15633): the CPU-resident n-gram table makes the device map appear as two devices, so layer offload is silently skipped. Companion fix in [#15632](https://github.com/unslothai/unsloth/pull/15632) (missing `weight_scale` on the FP8 n-gram table causing FP8 input into `key_proj`) and `unsloth-zoo#36` (table excluded from Block Swap's slot pool). Combined, these affect both VRAM footprint and numerical correctness on FP8 Flash-Next.
- **Benchmarks page work in flight** — [#11808](https://github.com/unslothai/unsloth/pull/11808) sweeps speculative decoding (MTP, n-gram, DSpark, DFlash), draft-token counts, KV-cache type, context length, parallel slots and RAM offload against a loaded GGUF; [#12522](https://github.com/unslothai/unsloth/pull/12522) adds a `llama-bench` tab for pp/tg throughput; [#12521](https://github.com/unslothai/unsloth/pull/12521) adds lm-eval-harness scoring per quant. These are the missing measurement surfaces for tuning serving configs.
- **Live monitor rework** — [#15630](https://github.com/unslothai/unsloth/pull/15630) collapses memory reporting into a single unified-memory row and adds a refresh-rate setting; the existing resize grip was non-functional.
- **Observability gaps acknowledged** — [#14582](https://github.com/unslothai/unsloth/issues/14582) requests prefill/load progress, expert/layer offload amounts, and live decode/prefill speed in a verbose UI. Today these numbers are only inferable via benchmarking PRs above.

## Stability & Regressions
Ranked by severity for production users:

1. **Windows Studio training floods the Recycle Bin** — [#15552](https://github.com/unslothai/unsloth/issues/15552): `*.db-wal` / `*.db-shm` files are written to `C:\$Recycle.Bin\<SID>\` roughly once per second; >10,000 entries (~40 MB) in a 90-minute run. Risk of disk exhaustion and looks malware-like to endpoint security. No fix PR yet.
2. **Anthropic passthrough ignores auto-compaction** — [#15417](https://github.com/unslothai/unsloth/issues/15417): `/v1/messages` returns an unrecoverable `400 exceed_context_size_error` when history exceeds `n_ctx`, even with auto-compaction enabled. Directly breaks Claude-SDK-style clients. No fix PR yet.
3. **Residual disk usage per session** — [#14487](https://github.com/unslothai/unsloth/issues/14487): 200–400 MB lost on C: after each desktop use with no cleanup path; open since 2026-10-21.
4. **Studio stack trace during training** — [#15538](https://github.com/unslothai/unsloth/issues/15538): unhandled exception on the Studio web UI training path.
5. **Deep Research not invoked** — [#15581](https://github.com/unslothai/unsloth/issues/15581): Gemma-4-26B-A4B ignores the Deep Research tool and answers from weights, i.e. tool-selection/routing failure rather than a retrieval failure.
6. **MLX vision token corruption** — [#15473](https://github.com/unslothai/unsloth/issues/15473) (see above); output is silently wrong, which is worse than a crash for downstream parsing.

Resolved in the last 24h:
- **SGLang auto-compact fixed** — [#15434](https://github.com/unslothai/unsloth/pull/15434) (closed): long chats on the managed SGLang engine previously stalled permanently after one over-context request.
- **Resume broken for Qwen3.5-2B sharded weights** — [#15405](https://github.com/unslothai/unsloth/issues/15405) (closed): `_MODEL_WEIGHT_RE` did not match `model.safetensors-00001-of-00001.safetensors`.
- **Destructive "Clear all" transcripts** — [#15570](https://github.com/unslothai/unsloth/pull/15570) (closed): desktop skipped the confirmation dialog the browser build had.
- **macOS Flux-3 image edit** — [#15241](https://github.com/unslothai/unsloth/issues/15241) (closed).

CI/tooling: [#15626](https://github.com/unslothai/unsloth/pull/15626) stops pytest 9.2's monkeypatch from deleting `torch._dynamo.config` entries (intermittent backend CI failures); [#15622](https://github.com/unslothai/unsloth/pull/15622) fixes `verify_import_hoist.py` false positives that are currently blocking two refactor PRs.

## What This Means for Application Developers
- **Do not rely on server-side context management for Anthropic-compatible clients.** Until [#15417](https://github.com/unslothai/unsloth/issues/15417) lands, enforce your own token budget and truncation before calling `/v1/messages`; the failure mode is a 400, not a transparent compaction.
- **MCP tool-definition overhead is now a real context cost.** Per [#15629](https://github.com/unslothai/unsloth/pull/15629) / [#15631](https://github.com/unslothai/unsloth/pull/15631), Studio's MCP server exposes 23 tools consuming ~18k tokens of definitions before any work happens. The per-tool switches are the intended mitigation — enable only what an agent needs.
- **Runtime model discovery is coming via OpenCode** — [#15521](https://github.com/unslothai/unsloth/issues/15521) tracks an upstream plugin so `unsloth start opencode` users can enumerate Studio models at runtime instead of hardcoding endpoints.
- **Quant selection is still implicit.** [#14362](https://github.com/unslothai/unsloth/issues/14362) notes API requests without an explicit quant fall back to the model default (e.g. `UD-Q5_K_XL`) with no way to set a preferred default such as `Q8_0` — pin quants explicitly in requests if reproducibility matters.
- **Bandwidth control is missing** — [#15603](https://github.com/unslothai/unsloth/issues/15603) requests a download rate limit; relevant for shared CI runners and provisioning pipelines pulling many GGUF variants.
- **Hardware guidance:** Intel XPU and mixed NVIDIA/AMD Linux remain unsupported/undetected today ([#15583](https://github.com/unslothai/unsloth/issues/15583), [#15444](https://github.com/unslothai/unsloth/issues/15444)); ExLlamaV3 is not an available backend ([#15527](https://github.com/unslothai/unsloth/issues/15527)). Plan deployments around NVIDIA CUDA, single-vendor ROCm, or Apple MLX.
- **Avoid long Windows desktop training runs on constrained system disks** until [#15552](https://github.com/unslothai/unsloth/issues/15552) is fixed; monitor `C:\$Recycle.Bin` growth during training.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*