# AI Infrastructure Digest 2026-10-26

> Generated: 2026-10-26 00:37 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project AI Infrastructure Comparison — 2026-10-26

## 1. Ecosystem Overview

The six projects span the AI infrastructure stack: vLLM and SGLang are datacenter inference/serving engines; llama.cpp, Ollama and Unsloth Studio target local/edge runtimes and tooling; LiteLLM is the LLM gateway/control plane. Activity is high but release cadence is uneven: no vLLM/SGLang/Ollama/Unsloth release in the last 24h, while llama.cpp shipped nine builds and LiteLLM shipped four patch releases. The dominant theme is production hardening rather than feature expansion: silent correctness regressions in long-context handling, speculative decoding, quantized kernels and hybrid attention are the most urgent issues. Hardware fragmentation across Blackwell SM90/SM100/SM120, GB10/GB300, ROCm CDNA/RDNA, Intel XPU, Apple Metal/MLX, SYCL and Vulkan remains the main source of backend-specific bugs and tuning wins.

## 2. Activity Comparison

| Project | Issues count, 24h | PR count, 24h | Release status |
|---|---:|---:|---|
| **vLLM** | n/d | n/d | No new release. Active work on multimodal CUDA graphs, speculative decoding, Blackwell/ROCm tuning. |
| **SGLang** | n/d | n/d | No new release. High activity across Fast Engine Recovery, Kimi-K3, DeepSeek-v4.1, diffusion and security hardening. |
| **llama.cpp** | 66 touched | 110 touched | 9 builds landed: b11879–b11889; b11885 not published. |
| **Ollama** | n/d | n/d | No release. MLX/MLX-C bump, pull rate limiting and Metal watchdog mitigation in flight. |
| **LiteLLM** | n/d | n/d | 4 patch releases: v1.106.4, v1.105.5, v1.104.7, v1.103.9. |
| **Unsloth** | 28 touched | 221 updated | No new release tags; Studio remains on v0.1.910-beta / package 2026.10.8. |

**Note:** “n/d” means the digest did not disclose a numeric count; “touched/updated” is used where explicitly reported. Issue/PR counts reflect activity, not total open backlog.

## 3. Model Support Race

No project shipped a major new frontier-model release in this window; most model support is in PRs, issues or beta builds. The race is therefore about **breadth of in-flight support** and **hardware-specific readiness**.

- **vLLM — broadest datacenter coverage in flight.** Active work covers Kimi-Linear on Intel XPU, GLM-5.3-Flash and Qwen3-Next/Qwen3.5 on ROCm, MiniMax-M3.1 MXFP8, gpt-oss MXFP4 backend filtering, DeepSeek-V4/V4.1 on SM90, and Kimi K3 performance tracking. It remains the widest matrix of frontier models × accelerators, but much is not yet released.
- **SGLang — deepest on selected frontier serving paths.** Strong work on Kimi-K3 megakernel and DCP multi-node, GLM DSA/MTP with PD disaggregation, SpecForge DFlash MLA drafts, DeepSeek-v4.1 prefill context parallelism, Beam model kernels, and diffusion models (`ideogram4`, `JoyEcho` completed; `omni-dreams` pending). It is ahead on Kimi/DeepSeek/GLM-specific serving depth and weight-reload mechanics.
- **llama.cpp — local/edge and quantization breadth.** Landed Qwen conversion improvements (`qwen4exp` tensor merging), proposed `JQ2_K`–`JQ6_K` CPU quant formats, added SYCL DPAS flash attention for quantized KV, HIP/RDNA4 KV decode rewrite, and Metal GQA-aware decode. It is ahead on GGUF/local quant coverage and consumer hardware backends.
- **Ollama — local runtime model ergonomics.** MLX/MLX-C bump targets gemma/flint/laguna prompt speed; F16 safetensors import metadata fixes Gemma4-style `ollama create`; MoE CPU offload remains a requested but unimplemented feature. No shipped release.
- **LiteLLM — provider surface, not inference support.** Open work adds Sideren and Decision Models providers; GPT-5.6 Copilot and DeepSeek V4 reasoning-effort issues are closed but not necessarily released. It competes on provider normalization, not model kernels.
- **Unsloth — fine-tuning plus Studio local inference.** Adds FLUX.3 Klein image editing, Vulkan speculative-decoding fix for MiniCPM-V-4.7-1B, Metal OOM recovery for DeepSeek-V4.1-Flash sidecar, and multi-GPU tensor split. Ahead on local model management and fine-tuning-adjacent workflows.

**Who is ahead:** vLLM by frontier model/hardware breadth; SGLang by depth on Kimi/DeepSeek/GLM and fast restart; llama.cpp/Ollama/Unsloth on local/edge hardware; LiteLLM on provider abstraction. These are different layers, not direct substitutes.

## 4. Performance Frontier

Optimization effort is concentrated in five areas:

- **Kernels and quantized matmul/attention.** vLLM reports a tuned Blackwell batch-invariant fused MoE config claiming **+48% decode throughput**. llama.cpp landed CUDA MMQ FFN gate/up + GLU fusion for prompt processing, and continues SYCL vectorized dequant stores and work-group argmax. SGLang is tuning Kimi-K3 NVFP4 SM107 paths and fused route preparation.
- **KV cache, prefix caching and speculative decoding interactions.** SGLang’s Fast Engine Recovery reports Qwen3-235B FP8 weight load dropping from **~306–327s to under 1s** via a weight-cache daemon. vLLM has a KV compression RFC and multiple prefix-cache/spec-decode correctness bugs. llama.cpp warns that 4-bit KV caches can silently fall back to CPU prefill, causing **~30× slowdown**. Unsloth is tightening KV admission via rendered-prompt counting.
- **Distributed serving and parallelism.** vLLM tracks PP/TP, ROCm multi-node, long-prefill starvation (**3–7 minutes** on DGX Spark TP2) and pipeline-parallel benchmarking. SGLang works on context parallelism, PD disaggregation, HiCache PP-sync throughput limits and multi-node GB300 issues. Unsloth adds tensor split across idle GPU pairs.
- **Local runtime prefill and memory admission.** Ollama targets MLX prompt speed and Metal watchdog crashes via prefill chunk shrinking. Unsloth reduced lazy-streamed model load by **~10.9s** on an A100 VM by moving block-group pinning to a background pinner, and is revising VRAM accounting for embeddings/lookup tables.
- **Gateway control-plane efficiency.** LiteLLM has no kernel work; it focuses on vector-store pagination and reducing `SpendLogs` row growth from malformed keys. Gateway overhead is operational, not model throughput.

**Net:** vLLM and SGLang are pushing datacenter kernel/distributed performance; llama.cpp, Ollama and Unsloth are optimizing local prefill, memory fit and backend breadth; LiteLLM is optimizing control-plane correctness and cost accounting.

## 5. Layer Positioning

| Project | Layer | Primary role | Competitive axis |
|---|---|---|---|
| **vLLM** | Datacenter serving engine | High-throughput OpenAI-compatible serving, distributed inference, frontier model/hardware support | Model coverage, kernels, distributed serving, correctness |
| **SGLang** | Datacenter serving engine | Structured generation, RadixAttention/prefix cache, PD disaggregation, high-performance frontier serving | Kimi/DeepSeek/GLM depth, fast restart, cache/parallelism |
| **llama.cpp** | Local runtime and GGUF/kernel library | Local inference, quantization formats, broad consumer hardware backends | Backend breadth, GGUF quant quality, prompt processing |
| **Ollama** | Local runtime distribution/UX | Easy model pull/run, desktop experience, llama.cpp/MLX wrapping | Developer ergonomics, macOS/Metal, model lifecycle |
| **LiteLLM** | LLM gateway/proxy | Provider abstraction, auth, budgets, spend logs, OpenAI/Anthropic compatibility | Provider coverage, cost controls, request normalization |
| **Unsloth** | Fine-tuning framework + Studio local inference | Training acceleration, local model management, quantization/offload ergonomics | Fine-tuning speed, Studio UX, local GPU memory fit |

vLLM/SGLang are not direct competitors to Ollama/llama.cpp/Unsloth Studio: the former serve multi-tenant datacenter traffic; the latter optimize single-user/local or edge workflows. LiteLLM sits in front of any of them as a gateway/control plane.

## 6. Trend Signals

1. **Silent correctness is now the top production risk.** Long-context truncation, garbled decode, wrong activation functions, spec-decode state loss and output non-invariance appear across vLLM, SGLang and llama.cpp. These fail as plausible-but-wrong output, not clean errors.
2. **Speculative decoding + prefix caching is a hazard zone.** Multiple projects report silent output corruption or crashes when EAGLE/MTP/DFlash interact with hybrid attention, CUDA graphs or radix cache. Golden-prompt regression tests are mandatory.
3. **Quantization must be pinned per backend.** vLLM reports ROCm AWQ **~2.5× slower than BF16** on RDNA 3.5; llama.cpp flags CUDA Q4_1/Q5_1 NaN logits and 4-bit KV CPU fallback; NVFP4 alignment issues persist on SM120. “Quantized” is not a portable performance guarantee.
4. **Cold start and weight reload are becoming SLO differentiators.** SGLang’s sub-second weight reload is operationally significant for autoscaling and failure recovery; Unsloth and Ollama are also attacking load latency and memory admission.
5. **Hardware fragmentation is not consolidating.** Blackwell SM90/SM100/SM120, GB10/GB300, ROCm CDNA/RDNA, Intel XPU, Apple MLX, SYCL and Vulkan all have distinct gaps. Teams should track a known-good build per backend rather than following `main`.
6. **Gateway maturity lags model serving.** LiteLLM issues show dropped spend logs, budget reseed under-counting, large-integer JSON corruption, embedding fan-out bugs and silent `max_retries` drops. Treat gateway spend logs as reconciliation data, not billing truth.
7. **Security hardening is landing in structured-output paths.** llama.cpp added grammar recursion depth limits; SGLang is adding JSON-schema depth/state bounds; LiteLLM has an unauthenticated-endpoint crash on non-object JSON bodies. Agent stacks should validate schemas and request bodies defensively.

**What agent/application developers should watch:** pin exact versions; run long-context, tool-call and structured-output golden tests after upgrades; avoid unsafe quant/spec-decode combinations; set LiteLLM proxy TZ to UTC until the DST OOM loop is fixed; assert embedding counts match inputs; keep MCP/tool payloads small; and implement client-side retries, timeouts and schema validation rather than trusting gateway or parser defaults.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-10-26

## 1. Today's Highlights

No new vLLM releases landed in the last 24 hours, but issue and PR activity shows active work on multimodal CUDA graphs, speculative decoding, and Blackwell/ROCm tuning. The most serious signals are correctness regressions around frontier-model serving: long prompts being silently truncated/invisible on GB10 for Qwen3.8-Flash-Next ([#62776](https://github.com/vllm-project/vllm/issues/62776)), and PP=3 illegal memory access/gibberish with Qwen3.8-27B GDN hybrid models ([#63505](https://github.com/vllm-project/vllm/issues/63505)). On the positive side, a tuned batch-invariant fused MoE config for Blackwell claims **+48% decode throughput** ([#63627](https://github.com/vllm-project/vllm/pull/63627)).

## 2. Releases & Breaking Changes

No new releases in the last 24 hours. No formal API or configuration deprecations were announced.

PR-level behavior changes to watch, not yet released:
- Anthropic-compatible serving: pass `tools=None` when probing chat templates to avoid incorrect inline-system merging ([#61920](https://github.com/vllm-project/vllm/pull/61920)).
- Jamba + LoRA: reject `enable_lora=True` at initialization with a descriptive `ValueError` instead of failing later during profiling/weight load ([#61915](https://github.com/vllm-project/vllm/pull/61915)).
- Pooling runner validation: raise early when `--runner pooling` is requested for an unsupported architecture ([#55570](https://github.com/vllm-project/vllm/pull/55570)).

## 3. New Model & Hardware Support

In-flight support work, not yet in a release:
- **Intel XPU / Kimi-Linear**: fix model dispatch so Kimi-Linear does not fall through to NVIDIA CUTLASS paths; depends on a separate MLA fix ([#63531](https://github.com/vllm-project/vllm/pull/63531), [#63530](https://github.com/vllm-project/vllm/pull/63530)).
- **ROCm / GLM-5.3-Flash**: pass precomputed chunk indices into `chunk_kda`, avoiding a GPU→CPU sync during prefill ([#63072](https://github.com/vllm-project/vllm/pull/63072)); add MTP verify inside the MonoKernel decode path on MI355X ([#63555](https://github.com/vllm-project/vllm/pull/63555)).
- **ROCm / Qwen3-Next & Qwen3.5**: enable the fused QK-norm + RoPE + gate Triton kernel on ROCm ([#51406](https://github.com/vllm-project/vllm/pull/51406)).
- **ROCm / MiniMax-M3.1**: MXFP8 linear tiles for 16 < M ≤ 256 and MXFP8 QKV input quantization in RMSNorm on MI355X/gfx950 ([#62509](https://github.com/vllm-project/vllm/pull/62509)).
- **gpt-oss MXFP4**: filter priority backends by platform so CUDA does not consider ROCm/XPU/CPU-only backends ([#61923](https://github.com/vllm-project/vllm/pull/61923)).
- **DeepSeek-V4/V4.1 on SM90**: feature request for a mega-MoE expert path on H100/H20; current Hopper path falls back to weight-only Marlin ([#57772](https://github.com/vllm-project/vllm/issues/57772)).
- **Kimi K3**: performance optimization tracker remains open ([#50587](https://github.com/vllm-project/vllm/issues/50587)).
- **KV compression**: RFC for a reusable KV compression layer for transfer/storage, targeting v0.32.0 baseline ([#59538](https://github.com/vllm-project/vllm/issues/59538)).

Hardware gaps:
- No `sm_121` Blackwell support on aarch64 for DGX Spark / Acer GN100 ([#36821](https://github.com/vllm-project/vllm/issues/36821)).
- DeepSeek-V4.1 `enable_adaptive_verification` is rejected on SM120 because device-query-lens support is gated to SM90/SM100 ([#63343](https://github.com/vllm-project/vllm/issues/63343)).

## 4. Performance & Optimization

- **Blackwell fused MoE**: tuned batch-invariant configs claim **+48% decode throughput** with `VLLM_BATCH_INVARIANT=1`; previously the Triton fused MoE kernel used one fixed config ([#63627](https://github.com/vllm-project/vllm/pull/63627)).
- **Top-k sampling kernel**: monolithic Triton top-k kernel falls back to full-vocab search on real MiMo-V2.6 logits because the outlier gather pivot is too high for **6–10% of rows** ([#61998](https://github.com/vllm-project/vllm/issues/61998)).
- **Long-prefill starvation**: Qwen3.8-Flash-Next long-prefill workloads periodically starve active decode for **3–7 minutes** on 2-node DGX Spark TP2 ([#54919](https://github.com/vllm-project/vllm/issues/54919)).
- **DeepSeek-V4.1-Flash on 8×H200**: user report of severe latency at concurrency 8, with time-to-first-token taking minutes ([#58233](https://github.com/vllm-project/vllm/issues/58233)).
- **ROCm AWQ on RDNA 3.5**: AutoAWQ checkpoints never use the RDNA W4A16 kernel; AWQ decode is **~2.5× slower than BF16** on gfx1151 ([#63455](https://github.com/vllm-project/vllm/issues/63455)).
- **ROCm attention selection**: closed issue reports `ROCM_AITER_FA` not selected over `ROCM_ATTN` for MHA decode on CDNA despite `VLLM_ROCM_USE_AITER=1` ([#46596](https://github.com/vllm-project/vllm/issues/46596)).
- **Benchmarking**: `vllm bench forward` now includes the configured MTP/EAGLE/DSpark/DFlash drafter by default, with `--exclude-drafter` for target-only timing ([#63622](https://github.com/vllm-project/vllm/pull/63622)); steady-state pipeline parallelism support was added ([#63616](https://github.com/vllm-project/vllm/pull/63616)).
- **Fused MoE stability/perf**: cap float32 tile sizes and pipeline stages to avoid shared-memory `OutOfResources` on Ada/SM8x ([#61931](https://github.com/vllm-project/vllm/pull/61931)).

## 5. Stability & Regressions

Ranked by severity:

1. **Critical — silent long-context loss on Qwen3.8-Flash-Next**: on GB10 with v0.32 nightly, prompt content beyond `indexer_budget` (~2048 tokens) is invisible to the model; it answers as if the prompt ended at ~2K ([#62776](https://github.com/vllm-project/vllm/issues/62776)).
2. **Critical — Qwen3.8-27B GDN hybrid PP=3 regression**: illegal memory access (Xid 31) with prefix caching, gibberish output without it on sm_86; regression since v0.28.0, reproduced on v0.31/v0.32 and both model runners ([#63505](https://github.com/vllm-project/vllm/issues/63505)).
3. **Critical — NVFP4 garbage output under `torch.compile`**: TP8 on SM120 with per-rank K not 32-aligned causes padded block-scale columns to read stale memory; eager/Marlin/aligned-TP are correct ([#48898](https://github.com/vllm-project/vllm/issues/48898)).
4. **Critical — speculative-decode prefill misdispatch**: a prefill whose scheduled token count equals `uniform_decode_query_len * num_reqs` can be dispatched into the FULL spec-decode CUDA graph, causing silent GDN state loss and garbage output in hybrid/Qwen3-Next models ([#53051](https://github.com/vllm-project/vllm/issues/53051)).
5. **High — XGrammar intermittent termination**: V2 DFlash probabilistic drafting plus block rejection can terminate structured-output requests intermittently ([#61383](https://github.com/vllm-project/vllm/issues/61383)).
6. **High — persistent KV offload cache not namespaced by model revision**: cached KV entries can cross model revisions, risking stale or incorrect cache reuse ([#49261](https://github.com/vllm-project/vllm/issues/49261)).
7. **High — SM120 adaptive verification rejection**: DeepSeek-V4.1 `enable_adaptive_verification` fails on SM120 despite available hardware ([#63343](https://github.com/vllm-project/vllm/issues/63343)).
8. **High — vllm-omni Qwen3-TTS crash**: concurrent TTS streaming above `--max-num-seqs` kills the process with a `ref_context_size` mismatch instead of queueing/rejecting ([#44933](https://github.com/vllm-project/vllm/issues/44933)).
9. **Medium — MinimaxM2.7 TP2 regression**: v0.24.0 → v0.25/v0.26 breaks TP2 on GH200NVL2 ([#49158](https://github.com/vllm-project/vllm/issues/49158)).
10. **Medium — ROCm AWQ performance regression**: 2.5× slower than BF16 on gfx1151 due to missing RDNA W4A16 kernel selection ([#63455](https://github.com/vllm-project/vllm/issues/63455)).
11. **Medium — RDNA2 regression since 0.22**: ongoing performance regression report ([#45372](https://github.com/vllm-project/vllm/issues/45372)).
12. **Medium — tool-call parser behavior change**: closed issue notes different `qwen3_coder` tool-call formatting between vLLM 0.22 and 0.23, which can break downstream parsers ([#46493](https://github.com/vllm-project/vllm/issues/46493)).
13. **Medium — KV cache dtype auto-resolution**: closed usage issue reports `--kv-cache-dtype auto` failing silently on Blackwell SM120 for DeepSeek-V4 instead of resolving to `fp8_ds_mla` ([#47174](https://github.com/vllm-project/vllm/issues/47174)).

Relevant fix PRs in flight:
- Float32 fused MoE shared-memory OOR fix ([#61931](https://github.com/vllm-project/vllm/pull/61931)).
- KV connector engine-ID synchronization across PCP ranks for multi-node startup failures ([#62916](https://github.com/vllm-project/vllm/pull/62916)).
- EAGLE prefix-caching lookahead block hashes ([#61589](https://github.com/vllm-project/vllm/pull/61589)).
- Hybrid GDN prefix-cache hits under MTP speculative decoding ([#52244](https://github.com/vllm-project/vllm/pull/52244)).

## 6. What This Means for Application Developers

- **Pin and validate frontier-model deployments.** Qwen3.8-Flash-Next long prompts can be silently truncated on GB10, and Qwen3.8-27B GDN hybrid models are unstable with PP=3 + prefix caching. Run long-context golden prompts after every upgrade.
- **Be cautious combining speculative decoding with prefix caching.** Several open bugs involve EAGLE/MTP/DFlash interactions with hybrid GDN state and CUDA graph dispatch; correctness failures can appear as plausible but wrong output rather than errors.
- **Treat structured output as best-effort at the transport layer.** Intermittent XGrammar terminations mean agent frameworks should implement retries, timeouts, and schema-validation fallbacks.
- **Tool-call parsing is version-sensitive.** If you rely on `qwen3_coder` or Anthropic-compatible endpoints, verify parser behavior across vLLM minor versions and prefer explicit parser configuration over defaults.
- **Blackwell SM120 is improving but not uniform.** NVFP4 + `torch.compile` has alignment-sensitive correctness issues, `kv-cache-dtype auto` may not resolve optimally, and some DeepSeek-V4.1 verification features are gated to SM90/SM100.
- **ROCm performance depends heavily on model/quantization/hardware pairing.** AWQ on RDNA 3.5 is currently much slower than BF16; AITER attention selection may not match documented behavior on CDNA.
- **Benchmarking is getting better for spec-decode.** The updated `vllm bench forward` includes drafters by default, making end-to-end speculative-decoding latency measurements more representative.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-10-26

## 1. Today's Highlights
No new releases landed in the last 24 hours, but the project remains highly active: the **Fast Engine Recovery** roadmap reports Phase 1 weight-cache daemon results cutting Qwen3-235B FP8 weight load from ~306–327s to under 1s. On the stability side, several high-impact correctness bugs are active, including a speculative-decoding server crash on sampled requests (#43412), Kimi-K3 garbled decode on multi-node GB300 (#45367), and a silent activation mismatch in `flashinfer_mxfp4` on SM90 (#45755). Security hardening is also in flight via JSON-schema depth/state bounds validation (#40373).

## 2. Releases & Breaking Changes
- **No releases in the last 24h.**
- Notable config/API work in progress (not yet breaking):
  - [#45434](https://github.com/sgl-project/sglang/pull/45434) — adds a KV-layout registry and optional `cp_kv_layout` configuration, separating context-parallel compute layout from KV storage layout with padded, rank-local capacity accounting.
  - [#46346](https://github.com/sgl-project/sglang/pull/46346) — Rust server now rejects whitespace inside `stop_regex` repeat counts before admission; aligns match-window bounds with CPython semantics.
  - Closed RFC [#36431](https://github.com/sgl-project/sglang/issues/36431) (resumable raw token stream for `/v1/chat/completions`) was marked inactive — no API change.

## 3. New Model & Hardware Support
- **[#27214](https://github.com/sgl-project/sglang/issues/27214)** — Diffusion model support tracking: `ideogram4` (NF4) and `JoyEcho` completed; `omni-dreams` still pending.
- **[#19137](https://github.com/sgl-project/sglang/issues/19137)** — Apple Silicon roadmap remains open with no native support yet; contributors requested.
- **[#39499](https://github.com/sgl-project/sglang/issues/39499)** — dLLM (block-diffusion) serving roadmap continues; crash/wrong-result fixes take priority over new features.
- **[#42973](https://github.com/sgl-project/sglang/pull/42973)** — KVShard integration for GLM DSA/MTP with PD disaggregation and CPU HiCache, preserving page ownership for target/draft latent KV and indexer keys/scales.
- **[#44676](https://github.com/sgl-project/sglang/pull/44676)** — Serve SpecForge DFlash MLA drafts through the target's DeepSeek MLA attention.
- **[#45254](https://github.com/sgl-project/sglang/pull/45254)** — Beam model kernel support (fused beam attention gate, elementwise ops), part 3 of a 7-PR series.
- **[#46236](https://github.com/sgl-project/sglang/pull/46236)** — AMD ROCm 10 build fix for the V4.1 fused k-norm-rope store (HIP wave64 mask `static_assert`).
- **[#43465](https://github.com/sgl-project/sglang/pull/43465)** — AMD DeepSeek-v4.1 prefill context parallelism.
- **[#46479](https://github.com/sgl-project/sglang/pull/46479)** — Kimi-K3 megakernel binding extended with fixed-width DSpark target-verification state.

## 4. Performance & Optimization
- **[#33522](https://github.com/sgl-project/sglang/issues/33522)** — Fast Engine Recovery: per-rank weight-cache daemon serves post-quantized weights over CUDA IPC; weight load drops from **~306–327s to <1s** on Qwen3-235B FP8 (Phase 1 landed in #27139). Directly relevant to restart and autoscaling latency.
- **[#46439](https://github.com/sgl-project/sglang/pull/46439)** — Diffusion: quantize video frames to `uint8` before host copy, avoiding full FP32 clip transfers on two return paths.
- **[#46399](https://github.com/sgl-project/sglang/pull/46399)** — dsv4.1: candidate table written into caller-held prefill graph buffers, reducing allocation churn in the prefill graph.
- **[#46480](https://github.com/sgl-project/sglang/pull/46480)** — Beam search: defer token materialization to top-k beams in `BeamGroup.finalize()` instead of materializing every completed beam.
- **[#46473](https://github.com/sgl-project/sglang/pull/46473)** *(closed)* — Kimi-K3 NVFP4 low-latency SM107 path: prepared FP4 routing metadata and fused route preparation to cut per-MoE-layer launch overhead at small batch.
- **[#45351](https://github.com/sgl-project/sglang/issues/45351)** — HiCache PP-sync drain forces the prefill scheduler into lock-step across PP ranks; reported as a pipeline-parallel throughput limiter.
- **[#42248](https://github.com/sgl-project/sglang/pull/42248)** — Native tensor output and prefill scoring optimizations (highest-priority, still open).
- **[#43304](https://github.com/sgl-project/sglang/issues/43304)** — Proposed metrics to localize bottlenecks in the multi-tokenizer/multi-detokenizer IPC path.

## 5. Stability & Regressions
Ranked by severity:

1. **[#43412](https://github.com/sgl-project/sglang/issues/43412)** *(OPEN)* — **Server crash (SIGKILL):** speculative decoding kills the process tree on the first non-greedy request. FlashInfer verify-sampling JIT fails due to bundled cccl vs pip CUDA 13 toolkit mismatch. Greedy traffic is unaffected. No fix PR listed.
2. **[#45755](https://github.com/sgl-project/sglang/issues/45755)** *(OPEN)* — **Silent correctness:** Kimi-K3 on SM90 `flashinfer_mxfp4` computes GPT-OSS SwiGLU instead of SiTU. Reproduced on v0.5.22 and main; blocked on an upstream FlashInfer fix.
3. **[#45367](https://github.com/sgl-project/sglang/issues/45367)** *(OPEN)* — **Garbled decode:** Kimi-K3 DCP all-to-all on 2-node GB300; `NCCL_GRAPH_REGISTER=0` restores smoke-test output. Patch hunks reportedly match #45824.
4. **[#46259](https://github.com/sgl-project/sglang/issues/46259)** *(OPEN)* — **Split-invariance bug:** FlashInfer prefill flips greedy output on a Qwen3.5 hybrid GDN model depending on radix-cache hit or chunked prefill. Reproduced on v0.5.19 and main.
5. **[#46476](https://github.com/sgl-project/sglang/issues/46476)** *(OPEN)* — DSpark draft query width incorrect on FA3/FA4 when `sample_from_anchor=False`. Fix PRs exist: [#46478](https://github.com/sgl-project/sglang/pull/46478) and [#46477](https://github.com/sgl-project/sglang/pull/46477).
6. **[#38515](https://github.com/sgl-project/sglang/issues/38515)** *(OPEN)* — `sgl_kernel` AOT wheel has per-op cubin gaps on sm_75 (RTX 2080 Ti): `rmsnorm` fails with `cudaErrorNoKernelImageForDevice` while `silu_and_mul`/`gelu_and_mul` work; no soft fallback.
7. **[#40373](https://github.com/sgl-project/sglang/pull/40373)** *(OPEN, security)* — Adds JSON-schema depth and state bounds validation to prevent DFA/NFA state explosion and CPU-thread hang/DoS during grammar compilation.
8. **[#46346](https://github.com/sgl-project/sglang/pull/46346)** *(OPEN)* — Rust server `stop_regex` repeat-count whitespace validation fix.
9. **[#25371](https://github.com/sgl-project/sglang/issues/25371)** *(CLOSED, inactive)* — `mem_cache` pool/allocator restructure RFC closed without landing.

## 6. What This Means for Application Developers
- **Autoscaling and cold-start latency:** The weight-cache daemon (#33522) is the most operationally significant work. Sub-second weight reload on large FP8 models materially changes restart SLOs for multi-tenant serving and recovery from node failures.
- **Avoid sampled requests with speculative decoding** until #43412 is resolved, or pin to a bundle with a matched CUDA toolkit. The failure mode is a hard process kill, not a graceful error.
- **Kimi-K3 on SM90/Hopper:** treat #45755 as a silent-output hazard — the model runs but produces wrong activations. Validate with known-good prompts. On GB300 multi-node, `NCCL_GRAPH_REGISTER=0` is the current workaround for #45367.
- **Prefix caching and chunked prefill are not fully output-invariant** on some hybrid-attention models (#46259). If you depend on exact greedy reproducibility across cache states, add regression tests.
- **Security:** if you accept user-supplied JSON schemas for structured outputs, track #40373 — deeply nested schemas can currently hang CPU threads.
- **Apple Silicon remains unsupported** per #19137; plan for NVIDIA/AMD hosts only.
- **Rust server maturing:** #45449 and #46346 show continued investment in a tokenizer/template-free Rust front end, which should reduce Python-side overhead for OpenAI-compatible chat serving.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-10-26

## 1. Today's Highlights

A busy 24 hours centered on hardening rather than new features: grammar recursion is now depth-limited across the codebase (b11882, b11888), closing a long-standing stack-overflow class of bugs ([#18988](https://github.com/ggml-org/llama.cpp/issues/18988)). CUDA got the FFN gate/up + GLU fusion for MMQ prompt processing ([#29948](https://github.com/ggml-org/llama.cpp/pull/29948)), while quantization correctness received two fixes (4D tensor quant bug in `llama-quant`, ue4m3 rounding). On the risk side, an open correctness bug in CUDA Q4_1/Q5_1 mat-vec kernels causing NaN logits ([#30950](https://github.com/ggml-org/llama.cpp/issues/30950)) is the highest-priority item for CUDA operators.

## 2. Releases & Breaking Changes

Nine builds landed (b11879–b11889; b11885 not published in this window).

- **b11888 / b11882 — Grammar recursion depth limit is now a hard requirement** ([#31153](https://github.com/ggml-org/llama.cpp/pull/31153), [#29495](https://github.com/ggml-org/llama.cpp/pull/29495) / [#29755](https://github.com/ggml-org/llama.cpp/pull/29755)). All recursive functions in the grammar/peg parser now carry an explicit limit; grammars exceeding it are rejected rather than overflowing the stack. **Migration note:** deeply nested GBNF (e.g. nested repetition) that previously "worked" may now fail fast at parse time. This is intentional hardening for a security-class bug.
- **b11881 — `no_alloc` now respected in all memory caches** ([#31087](https://github.com/ggml-org/llama.cpp/pull/31087)). Behavioral change for embedders relying on the `no_alloc` path; a TODO remains to move `no_alloc` out of `llama_hparams`.
- **b11880 — `llama-gguf` merge rejects truncated tensor data** ([#31044](https://github.com/ggml-org/llama.cpp/pull/31044)). Malformed GGUF merges now fail instead of silently producing corrupt output.
- **b11887 — default thread count adjusted for `test-llama-context`** ([#31157](https://github.com/ggml-org/llama.cpp/pull/31157)); this also exposed a WebGPU CI race, fixed in [#31162](https://github.com/ggml-org/llama.cpp/pull/31162).
- **b11889 — CUDA: `std::string` replaces VLA for `archName`** ([#30463](https://github.com/ggml-org/llama.cpp/pull/30463)). Clang `-Wvla-cxx-extension` cleanup, no functional change.

## 3. New Model & Hardware Support

- **Qwen conversion improvements** — b11886 merges tensors during convert and removes pre-scaling/constants for `qwen4exp` ([#31028](https://github.com/ggml-org/llama.cpp/pull/31028)). Reduces post-convert tensor count and fixes qwen4-exp conversion edge cases.
- **New quantization formats `JQ2_K`–`JQ6_K` (CPU)** proposed in [#30715](https://github.com/ggml-org/llama.cpp/pull/30715) — ik_llama.cpp-style IQ layouts reimplemented on the CPU path; spans testing, examples, Metal and SYCL tags, so backend coverage is partial.
- **SYCL: DPAS flash-attention kernel for quantized KV cache** ([#31125](https://github.com/ggml-org/llama.cpp/pull/31125)) — q4_0/q8_0 KV on Xe2 (B580, B570, B70, Lunar Lake, Panther Lake), head sizes 64/96/128/192, 1–31 query tokens including spec-decode drafts.
- **HIP/RDNA4: KV-cache decode rewrite** ([#30803](https://github.com/ggml-org/llama.cpp/pull/30803)) — one 32-value K block per lane instead of re-reading per query head.
- **Metal: GQA-aware decode path** ([#30728](https://github.com/ggml-org/llama.cpp/pull/30728)) — mirrors CUDA's `use_gqa_opt` to stop re-streaming a shared KV head G times per decode step.

## 4. Performance & Optimization

- **CUDA MMQ FFN fusion landed** (b11879, [#29948](https://github.com/ggml-org/llama.cpp/pull/29948)): gate and up matmuls for dense FFNs run as a single `mul_mat_q` launch with GLU applied in write-back. Directly targets prompt-processing throughput.
- **SYCL vectorized reordered-layout dequant stores** ([#30976](https://github.com/ggml-org/llama.cpp/pull/30976)) extends [#30775](https://github.com/ggml-org/llama.cpp/pull/30775)'s flat-launch/vectorized-store approach to Q4_0, K-quants, IQ3 and MXFP4; **SYCL work-group argmax** for short TOP_K rows ([#31110](https://github.com/ggml-org/llama.cpp/pull/31110)).
- **MoE memory tuning** — small MoE caches now warn/ramp down the SLRU protected set below ~1.8× a token's experts ([#30848](https://github.com/ggml-org/llama.cpp/pull/30848)); `llama_moe_cache` can execute missed experts on CPU via `--moe-cache-cpu-share`, shipped with a tuning script ([#30966](https://github.com/ggml-org/llama.cpp/pull/30966)).
- **Quantization autonomy** — `--target-size` / `--target-bpw` for per-tensor optimal quant mixing ([#15550](https://github.com/ggml-org/llama.cpp/pull/15550), long-running).
- **Open regression to watch:** Vulkan prompt processing on RDNA1 (gfx1010) is ~45% slower between b10455 and b11429 ([#30039](https://github.com/ggml-org/llama.cpp/issues/30039)).
- **Silent cliff:** default builds still disable `GGML_CUDA_FA_ALL_QUANTS`, so 4-bit KV caches quietly fall back to CPU prefill — reported as a ~30× slowdown with no warning ([#28633](https://github.com/ggml-org/llama.cpp/issues/28633)).

## 5. Stability & Regressions

Ranked by severity; all are open unless noted.

1. **CUDA Q4_1/Q5_1 mat-vec fp16 overflow → NaN logits** ([#30950](https://github.com/ggml-org/llama.cpp/issues/30950)). Block-offset × Q8_1 block sum overflows fp16; `gpt-oss` emits `"G"` and logits go NaN. Reproduced on b11800/b11538, code unchanged on master (b11803). No fix PR yet — **avoid Q4_1/Q5_1 on CUDA for now**.
2. **CUDA illegal memory access with flash attention on Windows releases** ([#30436](https://github.com/ggml-org/llama.cpp/issues/30436)). Gemma 4 12B QAT Q4_0 + mmproj, RTX 4080 Super, b11577/b10268; `-fa off` works. No fix PR.
3. **`--split-mode tensor` + DFlash2 draft → `GGML_ASSERT` in `ggml-backend-meta.cpp`** ([#28777](https://github.com/ggml-org/llama.cpp/issues/28777)). `--split-mode layer` is the workaround.
4. **`ggml_gallocr` reuses stale allocation plan on identity change at fixed graph position** ([#28448](https://github.com/ggml-org/llama.cpp/issues/28448)) — silent corruption with sparse MoE routing. Serious but niche.
5. **MCP stdio server deadlock on tool calls >1–5 KB** ([#28723](https://github.com/ggml-org/llama.cpp/issues/28723)), config-spawned servers only; reproduced on b10900 and b10472.
6. **Vulkan RDNA1 prompt-processing regression (~45%)** ([#30039](https://github.com/ggml-org/llama.cpp/issues/30039)).
7. **Dual-GPU CUDA garbled output**, Qwen3.6-27B, RTX 5060 Ti + 3060 ([#26257](https://github.com/ggml-org/llama.cpp/issues/26257)); single-GPU fine.
8. **Metal + qwen4exp long-context truncation** — 1 token then EOS, threshold moves with quant/KV quant/n_ctx ([#28805](https://github.com/ggml-org/llama.cpp/issues/28805)).
9. **Fixed:** GBNF nested-repetition stack overflow ([#18988](https://github.com/ggml-org/llama.cpp/issues/18988)) via b11882/b11888; 4D tensor quantization dropping rows in `llama-quant` (b11883, [#30820](https://github.com/ggml-org/llama.cpp/pull/30820)); dry-sequence-breaker CPU/memory exhaustion (b11884, [#31035](https://github.com/ggml-org/llama.cpp/pull/31035)); `ggml_fp32_to_ue4m3` saturation/RNE rounding ([#31020](https://github.com/ggml-org/llama.cpp/pull/31020)).
10. **Closed as stale, not confirmed fixed:** per-request LoRA prompt-cache contamination ([#26207](https://github.com/ggml-org/llama.cpp/issues/26207)), Metal `--embeddings` all-NaN output ([#27784](https://github.com/ggml-org/llama.cpp/issues/27784)), DSpark VRAM leak ([#27155](https://github.com/ggml-org/llama.cpp/issues/27155)). Treat as unverified rather than resolved.

## 6. What This Means for Application Developers

- **Upgrade if you use grammars / structured output.** The recursion limits (b11882, b11888) are a security fix, but validate your JSON-schema/GBNF grammars against the new depth cap before rolling out — failure mode changes from stack overflow at inference time to parse-time rejection.
- **Pin quantization per backend.** Q4_1/Q5_1 on CUDA is currently unsafe ([#30950](https://github.com/ggml-org/llama.cpp/issues/30950)); Q4_K/Q6_K remain the safer defaults. On CUDA, explicitly enable `GGML_CUDA_FA_ALL_QUANTS` if you want 4-bit KV caches to actually run on GPU ([#28633](https://github.com/ggml-org/llama.cpp/issues/28633)).
- **Multi-GPU + speculative decoding:** stick to `--split-mode layer` for draft-model setups until [#28777](https://github.com/ggml-org/llama.cpp/issues/28777) is resolved.
- **Agent/tool-call stacks:** the MCP stdio deadlock above ~1–5 KB payloads ([#28723](https://github.com/ggml-org/llama.cpp/issues/28723)) will bite large tool schemas — prefer HTTP/SSE MCP transports or keep tool-call bodies small.
- **Prompt-processing gains are landing on CUDA first** (MMQ FFN fusion) with Metal GQA and SYCL quantized-KV FA following in PRs; if you run long system prompts, CUDA prefill is where the near-term win is.
- **Conversion is improving for Qwen-family models** (tensor merging, qwen4exp cleanup) — reconvert rather than re-download when moving to b11886+, and re-check tensor counts if you script around GGUF layout.
- **Operational discipline:** issue volume is high (66 issues, 110 PRs touched in 24h) with many backend-specific regressions. Track a known-good build per backend rather than following `master` continuously.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-10-26

## Today's Highlights

No new release shipped in the last 24h, but the queue is active: an MLX/MLX-C version bump (PR #19291) targets prompt-processing speedups for gemma/flint/laguna, and a long-requested `ollama pull --limit-rate/--limit-streams` implementation (PR #19288) finally addresses issue #2006 from January 2024. On the stability side, the most consequential items are a ROCm regression on Radeon 890M iGPUs (0.40.6/0.40.7 vs. working 0.33.3) and a spurious-eviction/stale-VRAM bug on 6-GPU hosts, which already has a fix PR open.

---

## Releases & Breaking Changes

None — no releases in the last 24h. No API/config migrations landed. Note that PR #19285 proposes a schema bump (`migrateV19ToV20`) for an auto-restart-after-update setting, but it is still open.

---

## New Model & Hardware Support

- **MLX backend version bump (PR #19291)** — `mlx` and `mlx-c` are updated to leverage the new `window_size` feature, aimed at gemma/flint/laguna prompt speed. [ollama/ollama#19291](https://github.com/ollama/ollama/pull/19291)
- **F16 safetensors import metadata (PR #19275)** — `inferSafetensorsConfig` currently leaves `ModelFamily`, `ModelFamilies`, and `FileType` blank for F16/BF16 imports (e.g., Gemma4); this PR fixes `ollama create` output for non-quantized models. [ollama/ollama#19275](https://github.com/ollama/ollama/pull/19275)
- **MoE CPU offload (issue #11772, open)** — request to expose llama.cpp's MoE-layer CPU offload via an `OLLAMA_MOE_OFFLOAD=FULL|PARTIAL|NONE` strategy to cut VRAM usage. 73 👍, still unimplemented. [ollama/ollama#11772](https://github.com/ollama/ollama/issues/11772)

---

## Performance & Optimization

- **MLX prompt speedup** — PR #19291 is the concrete perf item of the day; the `window_size` change targets prefill for gemma/flint/laguna on Apple Silicon. No benchmark numbers published yet. [ollama/ollama#19291](https://github.com/ollama/ollama/pull/19291)
- **Metal watchdog mitigation for long prompts (PR #19280)** — shrinks prefill chunk size as context grows so a single Metal command buffer doesn't exceed the macOS GPU watchdog while analyzing very long inputs (e.g., large XML files). Directly tied to the crash in #19278. [ollama/ollama#19280](https://github.com/ollama/ollama/pull/19280)
- **Download throughput control (PR #19288)** — adds `--limit-rate` and `--limit-streams` to `ollama pull`, each with an env-var equivalent; previously concurrency adapted to bandwidth (#2221) but there was no user-settable ceiling (#2006). [ollama/ollama#19288](https://github.com/ollama/ollama/pull/19288)
- **Qwen3.5 4B GGUF prompt-processing on 16GB M4 (issue #19282)** — user report seeking guidance on expected prefill performance with 100% GPU offload via llama.cpp; no confirmed defect yet. [ollama/ollama#19282](https://github.com/ollama/ollama/issues/19282)

---

## Stability & Regressions

**High severity**

- **ROCm regression on Radeon 890M (gfx1150 iGPU)** — garbage output and `Memory access fault by GPU` on 0.40.6 and 0.40.7; 0.33.3 worked. Binary-version regression with no fix PR yet. [ollama/ollama#19290](https://github.com/ollama/ollama/issues/19290)
- **macOS Metal crash in MLX runner** — `panic: mlx: [METAL] Command buffer execution failed` on 0.40.9 when analyzing XML files. Mitigation PR #19280 (prefill chunk shrinking) is open. [ollama/ollama#19278](https://github.com/ollama/ollama/issues/19278) · [PR #19280](https://github.com/ollama/ollama/pull/19280)
- **Stale VRAM → spurious eviction on 6-GPU hosts** — the pre-load free-memory refresh hits its 3s deadline (`GPU discovery watchdog timed out`), causing overestimated usage, eviction of runners, and even a CPU-only model being evicted to satisfy a GPU prediction. Fix PR #19284 prevents evicting CPU-only runners. [ollama/ollama#19279](https://github.com/ollama/ollama/issues/19279) · [PR #19284](https://github.com/ollama/ollama/pull/19284)
- **Fresh install non-functional (0.40.x)** — user reports breakage after update; full uninstall/reinstall and model cleanup did not help. 12 comments, no root cause identified. [ollama/ollama#19276](https://github.com/ollama/ollama/issues/19276)

**Medium severity — correctness / protocol**

- **`/v1/completions` applies chat template to raw prompts** (CLOSED) — the OpenAI-compatible completions endpoint wraps prompts in the model template; OpenAI semantics send raw. Workaround remains `/api/generate` with `raw: true`. [ollama/ollama#19289](https://github.com/ollama/ollama/issues/19289)
- **OpenAI compat: assistant `tool_calls` reattached to previous message** — when `content` is `[]`, the assistant message is dropped and its tool calls/reasoning attach to the prior turn, corrupting history. [ollama/ollama#19151](https://github.com/ollama/ollama/issues/19151)
- **gemma4 unclosed-list tool calls silently dropped** — parser repair closes `}` but never `]`, so the response ends with no `tool_calls` and no `content`. Fix PR #19274 open. [ollama/ollama#19273](https://github.com/ollama/ollama/issues/19273) · [PR #19274](https://github.com/ollama/ollama/pull/19274)
- **qwen3.5 tool call can open before thinking channel closes** (PR #18624) — partial-tag test runs before complete `<tool_call>` detection, so chunk boundaries landing inside `<parameter…` mis-parse. [ollama/ollama#18624](https://github.com/ollama/ollama/pull/18624)
- **Truncated tool calls handed to clients** (PR #17564) — a response ending on the token limit can emit a half-written tool call. [ollama/ollama#17564](https://github.com/ollama/ollama/pull/17564)
- **MLX ignores `options.stop`** (PR #19267) — stop sequences are dropped on the MLX runner but honored on llama.cpp, producing divergent behavior across engines for identical requests. [ollama/ollama#19267](https://github.com/ollama/ollama/pull/19267)
- **Anthropic streaming drops late thinking** (PR #19110) — thinking arriving after text/tool_use in a streamed `/v1/messages` response is discarded. [ollama/ollama#19110](https://github.com/ollama/ollama/pull/19110)

**Low severity**

- **Cloud: Beam returns empty responses via OMP** while working through OpenCode Go — client-specific retry exhaustion. [ollama/ollama#19270](https://github.com/ollama/ollama/issues/19270)
- **Claude Code + cloud model: connection refused** on Windows 11, Ollama 0.40.11. [ollama/ollama#19281](https://github.com/ollama/ollama/issues/19281)
- **`x/transfer` unbounded auth retries** (PR #18452) — unchanged 401s recurse without limit and can be masked as a cache miss. [ollama/ollama#18452](https://github.com/ollama/ollama/pull/18452)
- **`mllama` unknown architecture** for Llama3.2-vision (CLOSED, #16547); **Windows 10 stray PowerShell window** on desktop launch (#18415); **sidebar animation on initial load** (PR #19286). [ollama/ollama#16547](https://github.com/ollama/ollama/issues/16547) · [ollama/ollama#18415](https://github.com/ollama/ollama/issues/18415) · [ollama/ollama#19286](https://github.com/ollama/ollama/pull/19286)

---

## What This Means for Application Developers

- **Tool-calling reliability is the main risk right now.** Multiple open issues affect agent loops: gemma4 drops malformed-but-recoverable tool calls (#19273), qwen3.5 can open a call mid-thinking-block (#18624), truncated generations hand you partial calls (#17564), and empty-array `content` in OpenAI-compatible requests corrupts message ordering (#19151). If you run agents on Gemma4/Qwen3.5 through `/v1/chat/completions`, validate `tool_calls` shape and treat empty responses as retryable.
- **Don't rely on `/v1/completions` for raw prompting.** It applies the chat template (#19289, closed). For raw text completion semantics, use `/api/generate` with `raw: true`.
- **MLX and llama.cpp are not yet behaviorally equivalent.** Stop sequences are currently ignored on MLX (#19267), so identical requests can generate different lengths per backend. Pin expectations per platform or set `num_predict` explicitly.
- **Upgrade caution on ROCm iGPUs.** If you're on a Radeon 890M (gfx1150), 0.40.6/0.40.7 produce garbage output and GPU page faults; 0.33.3 is reported working. Hold upgrades on that hardware until #19290 is triaged.
- **Multi-GPU schedulers should watch for false evictions.** The 3s free-memory refresh deadline on 6-GPU hosts can evict warm runners — including CPU-only ones — based on stale VRAM readings (#19279). PR #19284 partially addresses this; until merged, expect cold-start latency spikes under load.
- **Long-context / large-document workloads on macOS.** The Metal watchdog crash (#19278) and the prefill-chunking mitigation (#19280) are directly relevant if you feed XML/HTML/large files into MLX models.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-10-26

## 1. Today's Highlights

LiteLLM shipped four patch releases across four maintained minor lines (v1.106.4, v1.105.5, v1.104.7, v1.103.9), all with cosign-verifiable images, but the release-note bodies in the feed are truncated — verify the changelog before upgrading. The most urgent operational signal is [#48286](https://github.com/BerriAI/litellm/issues/48286): a proxy startup loop that gets OOM-killed during the DST fall-back hour, which hit a Europe/Paris deployment on 2026-10-25. Cost-accounting correctness is the second theme, with dropped spend-log batches, budget reseed errors, and invalid-key spend records all under active discussion or fix.

## 2. Releases & Breaking Changes

Four releases in the last 24h, indicating active backport maintenance rather than a single-head release:

| Version | Notes |
|---|---|
| [v1.106.4](https://github.com/BerriAI/litellm/releases) | Newest in the 1.106 line |
| [v1.105.5](https://github.com/BerriAI/litellm/releases) | Backport branch |
| [v1.104.7](https://github.com/BerriAI/litellm/releases) | Backport branch |
| [v1.103.9](https://github.com/BerriAI/litellm/releases) | Backport branch |

- All four publish cosign verification instructions using the same key (`e82a3c646908ba0a6ee0beddc4310d7a6cac4065/cosign.pub`) and state the command applies equally to `litellm-database`, `litellm-non_root`, and the `docker.io/litellm/*` images — supply-chain verification is consistent across artifacts.
- **No breaking changes, config migrations, or deprecations are visible in this window.** However, every release body is cut off at `## Wha`, so absence of evidence is not evidence of absence. Read the full release notes and diff `config.yaml` schema against 1.106.x before rolling to production.
- Given four parallel lines receiving patches, pin explicitly (e.g. `1.106.4`) rather than floating a minor tag; the backport cadence suggests fixes land on older lines asynchronously.

## 3. New Model & Hardware Support

No CUDA/ROCm/Metal/CPU backend work, architecture additions, or quantization-format support appeared in this window. Model/provider surface changes:

- **[PR #48356](https://github.com/BerriAI/litellm/pull/48356) (OPEN)** — Adds Sideren as a JSON-configured OpenAI-compatible provider. `sideren/<model>` works on chat completions, `/responses`, and `/messages` with only `SIDEREN_API_KEY` set. Notable because it is a pure `providers.json` entry with no Python module to maintain, and the model list stays provider-side.
- **[PR #46901](https://github.com/BerriAI/litellm/pull/46901) (OPEN)** — Adds Decision Models (`decisionmodels.io`) to the shared Jev decision flow: `BaseDecisionsConfig` subclass, registrations, price entries, Add-Model UI form, README row, and tests.
- **[Issue #33893](https://github.com/BerriAI/litellm/issues/33893) (CLOSED)** — Request for GPT-5.6 on GitHub Copilot with direct upstream `/responses` calls instead of Chat API fallback. Closed, but closure does not confirm implementation; verify behavior in 1.106.4 before relying on native `/responses` for `github_copilot`.
- **[Issue #27439](https://github.com/BerriAI/litellm/issues/27439) (CLOSED)** — DeepSeek V4 Pro/Flash graded `reasoning_effort` (`"high"`/`"max"`) was being stripped to `thinking: {"type": "enabled"}`. Closed; worth re-testing if you depend on effort-tiered reasoning on DeepSeek V4.
- **[Issue #29367](https://github.com/BerriAI/litellm/issues/29367) (CLOSED)** — FunASR / SenseVoice as an audio-transcriptions provider. Closed without an obvious merge; treat as unsupported.
- **[Issue #33670](https://github.com/BerriAI/litellm/issues/33670) (OPEN)** — Missing model coverage for opencode zen and agnes-ai, plus a request for one model bound to multiple keys.
- **[PR #48191](https://github.com/BerriAI/litellm/pull/48191) (OPEN)** — Fixes unmapped models on JSON-defined OpenAI-compatible providers losing `tools`, `tool_choice`, etc. because `supports_function_calling` defaults false. Affects `scaleway`, `ano-gpt/z-ai/glm-5.3-flash`, and similar.
- **[PR #43164](https://github.com/BerriAI/litellm/pull/43164) (OPEN)** — `nadir/auto` currently rejects `tools`, `tool_choice`, `parallel_tool_calls`; with `drop_params: true` tools are silently stripped and the model answers in prose. Also stops logging unpriced calls at $0.

## 4. Performance & Optimization

No throughput, latency, memory, or kernel work with concrete benchmark numbers landed in this window. Adjacent items that reduce backend load or unbounded growth:

- **[PR #48353](https://github.com/BerriAI/litellm/pull/48353) (OPEN)** — Vector store listing returns a stable ID-ordered page and rejects non-positive page numbers; the dashboard now follows every page so admins can browse past the first 100 stores.
- **[PR #48357](https://github.com/BerriAI/litellm/pull/48357) (OPEN)** — Stops unknown/malformed virtual keys from creating spend records. This directly reduces `LiteLLM_SpendLogs` row growth and cardinality under scanning or misconfigured-client traffic, while keeping rejected-traffic counters intact.
- **[Issue #48286](https://github.com/BerriAI/litellm/issues/48286)** is a memory-growth pathology rather than an optimization: restarts during the DST fall-back hour grow without bound before `Application startup complete` and get OOM-killed roughly once a minute.

## 5. Stability & Regressions

Ranked by severity. All items updated within the last 24h.

**Critical**

- **[#48286](https://github.com/BerriAI/litellm/issues/48286) (OPEN)** — Proxy startup loops forever and is OOM-killed during the DST fall-back hour; observed 2026-10-25 02:00 CET in Europe/Paris, tied to `scheduled_job_stagger`. Every restart grows unbounded. **No fix PR visible.** If you run in a DST-observing timezone, this recurs at the spring-forward transition too — mitigation is disabling the stagger job or pinning the container TZ to UTC.
- **[#48304](https://github.com/BerriAI/litellm/issues/48304) (OPEN)** — The auth failure handler assumes the parsed JSON body is a dict and calls `.get()`, so any valid non-object body (`[]`, `null`, `"s"`, `1`, `true`) triggers an unhandled `AttributeError` and a 500 **on unauthenticated endpoints**. Fix PR: **[#48329](https://github.com/BerriAI/litellm/pull/48329) (OPEN)**.

**High**

- **[#33873](https://github.com/BerriAI/litellm/issues/33873) (OPEN)** — Spend-log batches are dropped when a non-transport database write fails, causing silent billing and audit gaps. Rescoped 2026-08-16; the transport-failure half is fixed, this half is not. **No fix PR visible.**
- **[#47947](https://github.com/BerriAI/litellm/issues/47947) (OPEN)** — Proxy rewrites JSON integers larger than 64 bits as floats before forwarding. A tool-schema `"maximum": 12345678901234567890123` reaches the model as `1.2345678901234568e+22`; request succeeds silently. Data-corruption class bug for schema-heavy tool use.
- **[#38823](https://github.com/BerriAI/litellm/issues/38823) (OPEN)** — `vertex_ai/` embeddings with a list input returns only one vector while `usage.prompt_tokens` and billed cost cover all inputs. HTTP 200, so callers lose N−1 vectors and are charged for N. Silent correctness + billing bug.
- **[#48238](https://github.com/BerriAI/litellm/issues/48238) (OPEN)** — Streaming a reasoning model on a Vertex Model Garden dedicated endpoint (`vertex_ai/openai/<ENDPOINT_ID>`, SGLang backend, GLM-5.3) drops `reasoning_content` deltas, extra tool calls, and usage details. Non-streaming is correct; `hosted_vllm/` streams reasoning correctly.

**Medium**

- **[#48197](https://github.com/BerriAI/litellm/issues/48197) (CLOSED)** — On `/v1/messages`, a client disconnect before the first chunk leaves the upstream LLM request running (cost leak). Reported against 1.106.2; closed, confirm the fix in 1.106.4.
- **[#35570](https://github.com/BerriAI/litellm/issues/35570) (OPEN)** — Reservation reseed marks actual cost as applied without incrementing it when a reserved spend counter disappears or changes; budget enforcement can under-count.
- **[#34503](https://github.com/BerriAI/litellm/issues/34503) (OPEN)** — Scaleway provider is not routed on `/v1/embeddings`, raising `LiteLLMUnknownProvider`. Chat path works; embeddings-only routing gap.
- **[#34364](https://github.com/BerriAI/litellm/issues/34364) (OPEN)** — `LiteLLM_SpendLogs` partition boundaries are misaligned when the DB session timezone is not UTC.
- **[#22821](https://github.com/BerriAI/litellm/issues/22821) (CLOSED)** — Presidio guardrail with `output_parse_pii: true` on the Anthropic native path masks input PII but never unmasks output, and tool use via Claude Code returns 400. Related open fix: **[#48334](https://github.com/BerriAI/litellm/pull/48334) (OPEN)** addresses placeholder collision where user-supplied `<PERSON_1>` tokens collide with Presidio-generated ones.
- **[#48324](https://github.com/BerriAI/litellm/pull/48324) (OPEN)** — Ollama rejects a single string `stop` sequence; PR normalizes string-or-array.

**Lower severity / papercuts**

- **[#46335](https://github.com/BerriAI/litellm/issues/46335) (OPEN)** — Admin UI settings cannot be edited when `STORE_MODEL_IN_DB` is false.
- **[#46045](https://github.com/BerriAI/litellm/issues/46045) (CLOSED)** — `ModuleNotFoundError: No module named 'mcp'` when tools are set and the MCP handler is not skipped.
- **[#34771](https://github.com/BerriAI/litellm/issues/34771) (OPEN)** — CP OAuth DCR relay returns LiteLLM's own `/callback` as the client `redirect_uri`, causing a self-redirect loop; a prior fix for #33699 is reportedly incomplete on `litellm-database:v1.95.0-dev`.
- **[#34753](https://github.com/BerriAI/litellm/issues/34753) (OPEN)** — `metadata.error_information.error_message` stores full base64 file payloads on failed uploads, bloating `LiteLLM_SpendLogs`.
- **[#34763](https://github.com/BerriAI/litellm/issues/34763) (OPEN)** — MCP server UI omits `auth_type: "authorization"` from the dropdown though the backend supports it; API-only today.
- **[#32895](https://github.com/BerriAI/litellm/issues/32895) (OPEN)** — `max_retries` is silently dropped for all providers except OpenAI and Azure; no retries occur and no error is raised.
- **[#48226](https://github.com/BerriAI/litellm/issues/48226) (CLOSED)** — `completion_cost()` raised `ModelNotMappedError` for `groq/openai/gpt-oss-*` responses even though the request was priced correctly at call time.
- **[#48147](https://github.com/BerriAI/litellm/issues/48147) (CLOSED)**, **[#47965](https://github.com/BerriAI/litellm/issues/47965) (CLOSED)** — Provider accounting/endpoint inconsistencies in the decisions path (TypeSafe zero-cost on missing usage; Cloudflare decisions still on the `/ai/run` path the chat provider labels legacy).

**Cost-control fixes in flight**

- **[PR #48355](https://github.com/BerriAI/litellm/pull/48355) (OPEN)** — Soft-budget alerts for virtual keys now read current spend, including spend recorded by another proxy, and remain warnings so requests continue.
- **[PR #48354](https://github.com/BerriAI/litellm/pull/48354) (OPEN)** — Explicit `null` clears per-model budget limits without a server error, preserving other limits and leaving omitted fields unchanged.
- **[PR #30786](https://github.com/BerriAI/litellm/pull/30786) (OPEN, long-stale)** — Accept spend on `/team/member_update`.
- **[PR #34823](https://github.com/BerriAI/litellm/pull/34823) (OPEN)** — `/key/list` gains a `user_email` filter resolved via the user relation with a Postgres semi-join.

## 6. What This Means for Application Developers

- **Treat 1.106.4 as the target and read the real changelog.** Four parallel patch lines plus truncated notes means you cannot infer safety from the feed. Pin exact versions across gateway replicas and verify images with the published cosign key rather than trusting tags.
- **Set the proxy container TZ to UTC if you run in a DST-observing region.** [#48286](https://github.com/BerriAI/litellm/issues/48286) turns the clock change into a restart/OOM loop; there is no fix yet, only avoidance.
- **Validate request bodies before they reach auth.** [#48304](https://github.com/BerriAI/litellm/issues/48304) means `[]`, `null`, or a bare string on an unauthenticated endpoint returns 500 instead of 401/422. If you emit JSON programmatically, ensure you always send an object; if you front LiteLLM with a WAF or ingress, reject non-object bodies there until [#48329](https://github.com/BerriAI/litellm/pull/48329) lands.
- **Do not trust spend logs as a system of record for billing yet.** Three independent gaps can under-report: dropped batches on non-transport DB write failure ([#33873](https://github.com/BerriAI/litellm/issues/33873)), budget reseed not incrementing actual cost ([#35570](https://github.com/BerriAI/litellm/issues/35570)), and unpriced calls logged at $0 ([#43164](https://github.com/BerriAI/litellm/pull/43164)). Reconcile against provider invoices, and filter or partition `SpendLogs` for the base64 bloat in [#34753](https://github.com/BerriAI/litellm/issues/34753).
- **Check embedding fan-out before trusting vector counts.** On `vertex_ai/` routes, a list input can return one vector while billing all N ([#38823](https://github.com/BerriAI/litellm/issues/38823)). Assert `len(data) == len(input)` in your ingestion pipeline and reconcile `usage.prompt_tokens`.
- **Avoid streaming reasoning models through `vertex_ai/openai/...` for now.** `reasoning_content` deltas, extra tool calls, and usage are dropped in streaming but correct in non-streaming ([#48238](https://github.com/BerriAI/litellm/issues/48238)). Use `hosted_vllm/` on the same backend if you need token-level reasoning.
- **Tool calling on JSON-configured providers is unreliable until [#48191](https://github.com/BerriAI/litellm/pull/48191) merges.** If you use `scaleway`, `sideren`, or other `providers.json` entries with `drop_params` enabled, tools may be silently stripped and the model will answer in prose rather than calling functions. Prefer providers with first-class `supports_function_calling` metadata.
- **Do not rely on `max_retries` outside OpenAI/Azure.** [#32895](https://github.com/BerriAI/litellm/issues/32895) shows it is silently ignored for Anthropic, Bedrock, Vertex, and others — implement retry/backoff in your client or a gateway-level policy.
- **Smaller wins worth tracking:** Ollama `stop` as a bare string currently fails requests ([#48324](https://github.com/BerriAI/litellm/pull/48324)) — always send an array; and if you run with `STORE_MODEL_IN_DB=false`, Admin UI settings are read-only ([#46335](https://github.com/BerriAI/litellm/issues/46335)), so drive configuration through the management API.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-10-26

## 1. Today's Highlights

No new releases shipped in the last 24 hours, but the repo is in the middle of a large Studio stabilization push: 28 issues were touched and 221 PRs updated, with several Windows/Linux `v0.1.910-beta` model-load blockers closed and a cluster of GGUF admission, KV-sizing and multi-GPU scheduling fixes in flight. On the inference side, the highest-interest open work is Vulkan speculative-decoding correctness ([#15258](https://github.com/unslothai/unsloth/pull/15258)), Metal OOM recovery for the DSpark sidecar ([#14431](https://github.com/unslothai/unsloth/pull/14431)), and tensor-parallel splitting across idle GPU pairs ([#14446](https://github.com/unslothai/unsloth/pull/14446)).

## 2. Releases & Breaking Changes

- **No new release tags** for `unslothai/unsloth` in the last 24h. Studio users remain on `v0.1.910-beta` / package `2026.10.8`.
- **Behavior change in flight — run settings model:** [#15247](https://github.com/unslothai/unsloth/pull/15247) consolidates Run settings into one dropdown per model, with the last-used setting becoming what loads. If you scripted against the previous per-model preset semantics, expect a migration.
- **Reasoning-preservation override (fixed):** Studio was forcing `preserve_thinking=true` at `llama-server` launch, overriding user-supplied chat templates and making `--no-reasoning-preserve` a no-op. Closed as [#15115](https://github.com/unslothai/unsloth/issues/15115) — verify your launch flags after upgrading.
- **Agent integration surface:** [#15155](https://github.com/unslothai/unsloth/pull/15155) adds a Settings → Agents "Desktop apps" path to point Claude Desktop / Codex Desktop at the model Studio has loaded, including MTP and keep-alive handling.

## 3. New Model & Hardware Support

- **FLUX.3 Klein image editing:** [#15263](https://github.com/unslothai/unsloth/pull/15263) wires `unsloth/flux-3-image-klein-GGUF` into the Edit workflow (previously greyed out); closes [#15241](https://github.com/unslothai/unsloth/issues/15241).
- **Vulkan + speculative decoding:** [#15258](https://github.com/unslothai/unsloth/pull/15258) samples Vulkan drafts on CPU when the vocab misaligns logits rows, fixing the `GGML_ASSERT` crash with `openbmb/MiniCPM-V-4.7-1B-gguf` ([#14987](https://github.com/unslothai/unsloth/issues/14987)).
- **Metal sidecar sizing:** [#14431](https://github.com/unslothai/unsloth/pull/14431) reserves the DSpark/DFlash sidecar inside `--fit-target` and adds recovery from `kIOGPUCommandBufferCallbackErrorOutOfMemory` on `DeepSeek-V4.1-Flash-1017-GGUF`.
- **AMD audio stack:** [#13042](https://github.com/unslothai/unsloth/issues/13042) closed — Studio installs the CPU build of `audio.cpp` on AMD GPUs; request for ROCm/HIP bundles and Vulkan fallback so audio models don't run on CPU (RX 9070 XT / gfx1201 repro).
- **Multi-GPU tensor split:** [#14446](https://github.com/unslothai/unsloth/pull/14446) adds `--split-mode tensor` for dense models and MTP MoE when two matching GPUs are idle.
- **Requested, not yet supported:** Nanbeige4.3-33B optimization ([#15278](https://github.com/unslothai/unsloth/issues/15278)), native hf-mirror.com downloads with parallel fetching ([#15274](https://github.com/unslothai/unsloth/issues/15274)).

## 4. Performance & Optimization

- **Model load latency (closed):** [#15297](https://github.com/unslothai/unsloth/pull/15297) moves MiniMax-H3 block-group pinning to a background pinner — the single-threaded copy was ~10.9 s of every lazy-streamed load (19.45 GB) on an A100 VM.
- **Decode utilization:** [#14446](https://github.com/unslothai/unsloth/pull/14446) addresses layer-split serialization where each card sat idle ~half of decode on two-GPU hosts.
- **VRAM accounting for lookup tables:** [#14465](https://github.com/unslothai/unsloth/pull/14465) prices per-layer embeddings and Engram lookup tables as host/disk rather than VRAM (affects DeepSeek-V4.1 Engram, Qwen3.8-Flash-Next, Gemma 4 PLE, gemma-3n PLE), changing which quants are shown as fitting.
- **Offload planner (opt-in):** [#9872](https://github.com/unslothai/unsloth/pull/9872) keeps `UNSLOTH_SMART_OFFLOAD` behind a flag; adds a spill-vs-fitter cost gate and sub-FFN spill ladder.
- **KV admission accuracy:** [#15294](https://github.com/unslothai/unsloth/pull/15294) counts prepared prompts (tools + render options) instead of character estimates on the OpenAI/Responses/Anthropic passthrough paths, reducing shared-KV overcommit.
- **Context budgeting for vision:** [#15014](https://github.com/unslothai/unsloth/pull/15014) charges image tokens in fit and tool-result sizing, fixing silent undercounting (~20k tokens across ten screenshots) in [#14968](https://github.com/unslothai/unsloth/issues/14968).

## 5. Stability & Regressions

Ranked by severity:

1. **All GGUF loads killed on Windows (fixed, closed)** — [#15022](https://github.com/unslothai/unsloth/issues/15022): after the 2026-10-23 update, Studio spawned `llama-server`, got a healthy `/health` 200, then killed it on a false "our child is not listening" port-ownership check. Surfaced as a misleading "GGUF invalid / not enough memory" error.
2. **All models fail to load on `v0.1.910-beta` (fixed, closed)** — [#15142](https://github.com/unslothai/unsloth/issues/15142), Windows 11 25H2.
3. **Core dumps and silent stalls (open, no fix PR)** — [#14411](https://github.com/unslothai/unsloth/issues/14411): models stop responding after a few turns regardless of KV usage; ejecting the model core-dumps. Reported as a regression from the last two weeks, no relevant llama/server logs.
4. **Linux desktop crash (open)** — [#15260](https://github.com/unslothai/unsloth/issues/15260): `WebKitWebProcess` SIGABRT with AT-SPI/atk-bridge active on KDE Plasma (AppImage; repros on NVIDIA CUDA and other backends).
5. **CPU embedding stall (closed)** — [#14519](https://github.com/unslothai/unsloth/issues/14519): long tool-heavy chats spend minutes in CPU conversation-archive embedding before llama.cpp sees the request, and Stop does not cancel archiving.
6. **Vulkan spec-decode crash (open, fix PR #15258)** — [#14987](https://github.com/unslothai/unsloth/issues/14987), MiniCPM-V-4.7-1B prints one token then dies.
7. **Auto-compact ignores image tokens (open, fix PR #15014)** — [#14968](https://github.com/unslothai/unsloth/issues/14968): tool loops with screenshots overflow the context window.
8. **Chat template correctness (open)** — [#15285](https://github.com/unslothai/unsloth/issues/15285): the `DeepSeek-V4.1-Flash-1017-GGUF` template rejects ordinary discussion of the image marker, corrupting conversation history; patch supplied in the issue.
9. **UI/perf nits (open)** — slow typing in the image-edit instructions box under ROCm ([#15221](https://github.com/unslothai/unsloth/issues/15221)), dead context-menu items in the edit gallery ([#15272](https://github.com/unslothai/unsloth/issues/15272)), STT "Load into" ignored when loaded via API ([#15280](https://github.com/unslothai/unsloth/issues/15280)).

## 6. What This Means for Application Developers

- **Pin your Studio desktop build and re-test model loads.** The `v0.1.910-beta` Windows load failures are closed but were severe and presented as user-facing memory/file errors — don't let them be mistaken for OOM in your incident triage.
- **Audit speculative decoding on Vulkan.** If you serve vocab-misaligned models with spec decode on Auto (which selects the DSpark sidecar), you are on the crash path in [#14987](https://github.com/unslothai/unsloth/issues/14987) until [#15258](https://github.com/unslothai/unsloth/pull/15258) lands; disable spec decode as a workaround.
- **Give multimodal agents explicit context headroom.** Auto-compact under-counts image tokens on local GGUF until [#15014](https://github.com/unslothai/unsloth/pull/15014) merges; budget screenshot-heavy tool loops conservatively rather than trusting the reported token count.
- **Re-check your quant recommendations.** [#14465](https://github.com/unslothai/unsloth/pull/14465) and the open offload planner ([#9872](https://github.com/unslothai/unsloth/pull/9872)) change which models are reported as fitting in VRAM — capacity badges and the quant picker will shift.
- **Expect tighter KV admission for tool schemas.** [#15294](https://github.com/unslothai/unsloth/pull/15294) replaces character heuristics with rendered-prompt counting; requests with large tool schemas may be admitted less aggressively (correctly) than before.
- **Multi-GPU hosts get a throughput option.** For models that fit a single card, tensor split ([#14446](https://github.com/unslothai/unsloth/pull/14446)) may beat the default layer split on decode — worth benchmarking your agent workload.
- **External agent clients are becoming first-class.** [#15155](https://github.com/unslothai/unsloth/pull/15155) and per-model run-setting presets ([#15247](https://github.com/unslothai/unsloth/pull/15247)) reduce the config glue needed to point Claude Desktop / Codex Desktop at a locally served Studio model.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*