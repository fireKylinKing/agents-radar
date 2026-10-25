# AI Infrastructure Digest 2026-10-25

> Generated: 2026-10-25 00:29 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project Comparison Report — 2026-10-25

## 1. Ecosystem Overview

The AI infrastructure ecosystem is in a model-specific hardening phase: vLLM and SGLang shipped no new releases, but both are carrying heavy PR traffic around DeepSeek-V4.x, GLM-5.x, Kimi-K3, Qwen3.x, and Minimax-M3. The main engineering battlegrounds are KV-cache memory reduction, speculative decoding/MTP correctness, MoE expert handling, and distributed serving across Blackwell, Hopper, GB300, AMD MI300X/MI355X, Hexagon, RDNA, and NPU targets. Hardware support is broadening but fragmenting, with backend-specific regressions appearing on CUDA, ROCm, SYCL, Metal, Vulkan, and MLX. The gateway layer had a critical supply-chain incident in LiteLLM, while local runtimes improved OpenAI/Anthropic compatibility but still show silent correctness bugs. Fine-tuning is moving toward multi-GPU pipeline/tensor parallelism, but desktop packaging reliability remains a risk.

## 2. Activity Comparison

> Data note: SGLang and Unsloth are the only digests with aggregate activity counts. For other projects, “cited” means unique GitHub issue/PR references in the digest, not full repository totals.

| Project | Layer | Issues (digest) | PRs (digest) | Release status |
|---|---|---:|---:|---|
| **vLLM** | Datacenter serving engine | 25 cited; 12 ranked open stability issues; no aggregate | 15 cited; no aggregate | No release in 24h; latest referenced v0.32.0 |
| **SGLang** | Serving engine / runtime | **36 touched** | **~493 updated** | No release in 24h |
| **llama.cpp** | Local / edge runtime | 19 cited; no aggregate | 20 cited; no aggregate | **b11856–b11869** (14 rolling builds) |
| **Ollama** | Local runtime / developer endpoint | 6 cited issues; 9 ranked stability items incl. PRs | 12 cited PRs | **v0.40.11** — macOS speech support |
| **LiteLLM** | LLM gateway / router | 21 cited; no aggregate; critical #47826 | 16 cited fix PRs | **1.106.3 / 1.105.4 / 1.104.6 / 1.103.8**; 1.107.0 compromised and pulled |
| **Unsloth** | Fine-tuning / Studio | 14 cited; no aggregate | **180 updated** (mostly dependabot); 7 notable cited | No release; Studio Windows GGUF-loading regression |

**Readout:** SGLang has the highest visible PR churn, but much of it is CI flakiness and DeepSeek/GLM/Kimi bug traffic. llama.cpp has the highest release cadence via rolling builds. LiteLLM’s four patch releases are overshadowed by the compromised 1.107.0 artifact. vLLM and Unsloth show high substantive PR activity without tagged releases.

## 3. Model Support Race

| Project | New model/architecture support today | Position |
|---|---|---|
| **vLLM** | NVFP4 KV cache for dense MLA on DeepSeek-V3.1 and Kimi-K3; Qwen3-VL serving on `/v1/systemone`; Qwen2.5/3-Omni `use_audio_in_video`; ROCm MXFP4 W4A16 for DeepSeek-V4.1-Flash on MI300X; Kimi-K3 MI355X recipe; DBO + speculative decoding + DCP | **Ahead in datacenter serving breadth and KV-cache quantization.** DeepSeek/Kimi/Qwen coverage is the strongest, but hybrid-GDN/spec-decode bugs are a sharp edge. |
| **SGLang** | Rust server + Qwen-VL multimodal; NPU PD disaggregation with Mooncake; AMD DeepSeek-V4 shared-expert fusion on gfx950; Quark/Qwen3.8 MXFP4 shared-expert quantization; out-of-tree unified-cache linkers; Beam model kernels; SANA-Sprint diffusion request | **Ahead on AMD MI355X shared-expert fusion, Rust multimodal, and NPU PD disaggregation.** But `qwen4_exp` checkpoint loading is incomplete and DeepSeek/GLM/Kimi serving bugs are clustering. |
| **llama.cpp** | Minimax-M3 NVFP4/MXFP8 quant scales; DeepSeek V4.1 Flash support PR open; Hexagon multicore HMX flash attention; Vulkan MoE expert-cache fix; SYCL rounding fix; Metal `mul_mm_id` rescaling; CUDA Kepler legacy handling | **Ahead in edge/consumer hardware breadth.** Strong Hexagon, Vulkan, Metal, SYCL, and CUDA coverage, but not a datacenter serving competitor. |
| **Ollama** | Nemotron 3.5 Bolt 74B-A9B on MLX; long HuggingFace GGUF model names; Gemma 4 compact vision towers; macOS speech generation via `/v1/audio/speech` | **Ahead in local developer UX and MLX packaging.** But MLX stop-sequence and mixed-precision import correctness gaps remain. |
| **LiteLLM** | Nemotron 3.5 Super VL 120B-A12B W&B pricing; Cerebras Llama 3.1 aliases; uncatalogued OpenAI-compatible models retain tools; uncatalogued ChatGPT via Responses bridge; Bedrock Nova 2 Lite geo fix; OpenAI prompt-cache write pricing; Azure transcription GA endpoint | **Ahead in provider/model catalog and gateway compatibility.** Not an inference engine; its value is abstraction, cost, auth, and routing. |
| **Unsloth** | Not primarily new model support today; pipeline parallelism v1; tensor parallelism v2; Metal OOM recovery; Wan streaming encode; distillation roadmap | **Ahead in multi-GPU fine-tuning ergonomics.** PP/TP work makes 8B LoRA feasible on 2×16 GB, but Studio Windows regressions are critical. |

**Who is ahead overall:** vLLM leads datacenter serving support for current frontier models and KV-cache quantization. SGLang is close and leads on AMD MI355X shared-expert fusion plus Rust/NPU serving paths. llama.cpp and Ollama lead local/edge deployment. LiteLLM leads gateway coverage but is dealing with a severe security incident. Unsloth leads multi-GPU fine-tuning, not serving.

## 4. Performance Frontier

Optimization effort is concentrated in five areas:

- **KV cache and memory footprint** — vLLM’s NVFP4 MLA KV cache cuts DeepSeek/Kimi KV to 352 bytes/token/layer vs 576 FP8 and 1152 BF16. SGLang overlaps CP KV gather with indexer computation and fixes scheduler prefill admission. llama.cpp keeps KV rotation constants GPU-resident and battles Vulkan long-context fragmentation. Unsloth works on KV preemption for parallel chats. **KV cache is the most active optimization frontier.**
- **Quantization** — vLLM adds NVFP4 KV and ROCm MXFP4 W4A16; SGLang adds MXFP4 shared-expert quantization and is fixing diffusion quantization bugs; llama.cpp tunes NVFP4 MMVQ and Minimax-M3 scales; Ollama deals with MLX mixed-precision import failures. Quantization is moving from weights into KV cache and expert paths.
- **Kernel and host-overhead fusion** — vLLM targets DeepSeek-V4.1-Flash decode metadata fusion, ROCm draft-prepare fastpaths, and fused Kimi-K3 MLA chunked-context gather. SGLang adds fused async NaN validation, Beam kernels, and Wan text K/V projection caching. llama.cpp works on Hexagon FA multicore and CUDA FA stream-K. The focus is per-step host overhead and small-kernel collapse.
- **Distributed serving and KV transfer** — vLLM’s GLM-5.3 P/D descriptor overhead on GB200 shows NIXL descriptor submission, not fabric bandwidth, can be the bottleneck. SGLang adds NPU PD disaggregation with Mooncake and DSA CP KV overlap. llama.cpp has open multi-GPU draft-MTP throughput regressions. Distributed serving is increasingly about KV movement and scheduling, not just tensor parallelism.
- **Speculative decoding / MTP correctness** — vLLM has multiple GDN/spec-decode correctness issues; llama.cpp has MTP crashes and multi-GPU regressions; SGLang fixed a rejection-sampling int32 overflow. Spec decode is now a performance feature with a meaningful correctness tax.

## 5. Layer Positioning

- **vLLM — datacenter inference engine.** High-throughput GPU serving, PagedAttention-style KV management, distributed serving, speculative decoding, and model-specific kernels. Best fit for production model serving where model breadth and KV efficiency matter.
- **SGLang — serving engine/runtime with broader frontend and hardware experimentation.** Overlaps vLLM in datacenter serving, but differentiates with Rust server paths, multimodal preprocessing, diffusion support, NPU PD disaggregation, and AMD-specific expert fusion. More exposed to model-specific serving bugs today.
- **llama.cpp — local/edge runtime and backend layer.** GGML-based inference across CUDA, Metal, Vulkan, SYCL, Hexagon, and CPU. Best fit for local, desktop, embedded, and heterogeneous hardware; not a cluster-scale serving engine.
- **Ollama — developer-facing local model runtime.** Packages local inference behind a simple CLI and OpenAI-compatible endpoint, now adding speech. It abstracts llama.cpp/MLX backends and competes on UX, model pulls, and local developer workflows.
- **LiteLLM — LLM gateway/router/proxy.** Provider abstraction, auth, budgets, spend tracking, retries, and OpenAI/Anthropic compatibility. It sits in front of serving engines and cloud APIs; its risks are supply-chain, cost accuracy, and tool-call translation.
- **Unsloth — fine-tuning/training framework and Studio.** Focused on efficient LoRA/fine-tuning, quantization, and now multi-GPU PP/TP. Unsloth Studio also exposes local serving, but its core layer is training and model customization.

## 6. Trend Signals

- **Silent correctness is now more dangerous than crashes.** vLLM’s ngram spec decode on hybrid GDN produces corrupted output; SGLang’s `fast_topk_v2` silently returns wrong top-k sets; Ollama’s MLX path ignores stop sequences; llama.cpp SYCL produces token soup. Agent developers should validate output quality, not just HTTP 200.
- **KV cache correctness and namespace isolation matter for agent loops.** Prefix-cache bugs appear in vLLM hybrid GDN, Ollama Anthropic-compat, and SGLang’s Anthropic adapter. vLLM’s persistent KV offload cache is not namespaced by model revision. For multi-turn agents, cache correctness directly affects latency, cost, and output stability.
- **Agentic tool-calling is a major test surface.** LiteLLM silently drops `tools` for uncatalogued models on 1.106; SGLang has streaming tool-argument bugs across qwen3_coder, hunyuan, and minimax-m2; Ollama renders integer tool args as decimals; GLM-5.3-Flash degenerates on multi-tool prompts. Watch JSON completeness at `finish_reason`, missing `</function>`, and tool-schema handling.
- **Supply-chain security is now a first-class infrastructure concern.** LiteLLM 1.107.0 shipped a credential stealer and `sitecustomize` RCE loader from a lookalike repo. Cosign key rotation will break admission pipelines. Pin by digest, verify signatures, and rotate credentials if `:latest` was pulled on 2026-10-21.
- **Hardware support is broadening but uneven.** AMD MI300X/MI355X/gfx950, Blackwell/SM120, GB300, Hexagon, RDNA, and NPU paths all have active work and active regressions. There is no single “supported” matrix; teams need per-model, per-backend validation.
- **OpenAI/Anthropic compatibility is improving but still leaky.** Ollama fixed UUID IDs and `max_completion_tokens`; LiteLLM fixed Claude gateway discovery and Anthropic stream cleanup; llama.cpp fixed Jinja `selectattr`. But prefix-cache defeats, ignored `think` parameters, and streaming usage loss remain. Application developers should keep compatibility tests in CI.
- **Pin versions aggressively.** llama.cpp shipped 14 rolling builds with behavioral changes; LiteLLM shipped four patches plus a compromised release; Unsloth’s Windows Studio beta breaks GGUF loading; vLLM/SGLang have no release but many open high-severity bugs. For production, use known-good digests and maintain a per-model/backend regression suite.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-10-25

## 1. Today's Highlights

No releases landed in the last 24 hours, but activity is concentrated on the DeepSeek-V4.1-Flash decode path: a tracking PR for eager-metadata kernel fusion (#63308/#63523) and ROCm draft-prepare fastpaths (#63182) both target per-step host overhead in MTP decode. On the memory side, a new PR adds `--kv-cache-dtype nvfp4` for dense MLA models (DeepSeek-V3.1, Kimi-K3), cutting KV footprint to 352 bytes/token/layer versus 576 for FP8. Counterbalancing this, a fresh PP=3 illegal-memory-access regression on Qwen3.8 hybrid GDN (#63505) plus several unresolved hybrid-GDN/spec-decode correctness bugs remain the highest-severity open risks.

## 2. Releases & Breaking Changes

No new versions or config migrations today. One behavior change worth flagging:

- **[CLOSED] `/invocations` now enforces `--api-key`** — [PR #58341](https://github.com/vllm-project/vllm/pull/58341). Previously `/v1/chat/completions` returned 401 without credentials while the identical body posted to `/invocations` returned 200. Deployments using SageMaker-style `/invocations` with an API key configured will now be rejected without credentials. Audit any client that relied on the unauthenticated path.
- **[CLOSED] PP routed-expert capture enabled for R3** — [PR #61333](https://github.com/vllm-project/vllm/pull/61333). Capturing routed experts now works with `PP > 1`; previously the final stage lacked complete routing data.
- **Proposed semantics change:** [Issue #54383](https://github.com/vllm-project/vllm/issues/54383) reports the first boot of a given `(max-num-batched-tokens, max-num-seqs)` shape gets a ~5% smaller KV cache than subsequent identical boots — relevant if you size capacity from first-boot logs.

## 3. New Model & Hardware Support

- **NVFP4 KV cache for dense MLA** — [PR #63203](https://github.com/vllm-project/vllm/pull/63203). Adds `--kv-cache-dtype nvfp4` on the `FLASHINFER_MLA` backend for DeepSeek-V3.1 and Kimi-K3: 352 bytes/token/layer vs 576 (FP8) and 1152 (BF16) — roughly 1.6× and 3.3× reductions. 512 latent dims in NVFP4 with E4M3 block scales.
- **Qwen3-VL serving on `/v1/systemone`** — [PR #62402](https://github.com/vllm-project/vllm/pull/62402). Maps `Qwen3VLForConditionalGeneration` onto the existing `NextTokenStrategy`, following the Gemma 4 opt-in.
- **Qwen2.5-Omni / Qwen3-Omni `use_audio_in_video`** — [PR #63135](https://github.com/vllm-project/vllm/pull/63135). Fixes crashes when the flag is set engine-wide via `--mm-processor-kwargs`; resolves from merged engine + request kwargs at every detection point.
- **ROCm MXFP4 W4A16 for gfx942 (MI300X)** — [PR #63522](https://github.com/vllm-project/vllm/pull/63522). Aligns AITER Triton MXFP4 intermediate size to 128; contributed from a DeepSeek-V4.1-Flash MLPerf Endpoints submission on 8× MI300X.
- **Kimi-K3 MI355X AgentX recipe** — [PR #63520](https://github.com/vllm-project/vllm/pull/63520). Recipe YAML realigned with InferenceX #4393.
- **DBO + speculative decoding + DCP** — [PR #61464](https://github.com/vllm-project/vllm/pull/61464). Lifts the V2 runner's rejection; draft prefill reuses the target's full-batch attention metadata and slot mappings, and its FULL graphs. Prefill CP remains gated.
- **Open hardware gaps:** SM120 is rejected for `enable_adaptive_verification` because device-query-lens support is gated to SM90/SM100 ([#63343](https://github.com/vllm-project/vllm/issues/63343)); gfx942/MI325X Kimi-K3 roadmap remains incomplete ([#52803](https://github.com/vllm-project/vllm/issues/52803)); the RFC to replace hardcoded `torch.cuda` calls with `torch.accelerator` is still stale/open ([#30679](https://github.com/vllm-project/vllm/issues/30679)).

## 4. Performance & Optimization

- **DSv4.1 flash-decode metadata fusion (tracking)** — [PR #63308](https://github.com/vllm-project/vllm/pull/63308) and [PR #63523](https://github.com/vllm-project/vllm/pull/63523). Removes per-step host metadata math from torch dispatches in the eager section around the captured flash-decode forward. Profiled on 8×H200, DP=8, MTP, c16 decode with the stacked decode fusions already applied.
- **ROCm draft-prepare fastpaths for DSv4.1-Flash on gfx950 (MI355X)** — [PR #63182](https://github.com/vllm-project/vllm/pull/63182). Three changes to the DSpark draft-prepare step that runs before every draft CUDA-graph replay: shared-output dedup across draft KV cache groups, parallel pad-tail fix, and KV-only context insert (dummy-Q removal).
- **Fused Kimi-K3 MLA chunked-context gather on AITER** — [PR #53166](https://github.com/vllm-project/vllm/pull/53166). Dense-MLA prefill currently spends four kernels per context chunk (`gather_and_maybe_dequant_cache`, `kv_b_proj` expansion, two `direct_copy` calls); collapsing these is the target.
- **Triton top-k/top-p fallback** — [Issue #61998](https://github.com/vllm-project/vllm/issues/61998). `_topk_topp_kernel` picks its gather pivot from a Gaussian fit over only the first 8192 logits; on MiMo-V2.6 that pivot is too high for 6–10% of rows, forcing a full-vocab search.
- **GLM-5.3 P/D descriptor overhead on GB200** — [Issue #55434](https://github.com/vllm-project/vllm/issues/55434). NIXL transfer under agentic load submits 91k–120k KV descriptors per TP-rank transfer, making MNNVL/cuda_ipc slower than RDMA on this workload — descriptor submission, not fabric bandwidth, is the bottleneck. Follow-on optimization list in [Issue #57406](https://github.com/vllm-project/vllm/issues/57406).
- **KV-offload back-pressure false positive** — [PR #63524](https://github.com/vllm-project/vllm/pull/63524). `fs` tier store jobs re-storing an already-on-disk prefix skipped the write but still reported the full batch in `transfer_bytes`, completing in microseconds and tripping the back-pressure detector.
- **Frontend perf reports:** Rust frontend showing TTFT degradation and poor QPS scaling vs Python on Qwen122B-A10B at 44k avg input ([#50718](https://github.com/vllm-project/vllm/issues/50718)); Rust-vs-PyTorch preprocessing benchmark ([#47601](https://github.com/vllm-project/vllm/issues/47601)).

## 5. Stability & Regressions

Ranked by severity. Note: no confirmed fix PRs yet for the top three.

1. **Qwen3.8-27B (GDN hybrid) PP=3 on sm_86 → illegal memory access (Xid 31)** — [Issue #63505](https://github.com/vllm-project/vllm/issues/63505). With prefix caching the GPU faults; without it, output is gibberish. Regression since v0.28.0, reproduced on v0.31.0 and v0.32.0 across both model runners. Filed today.
2. **Qwen4Exp single prefill crossing the 262,144 native boundary crashes the engine** — [Issue #62383](https://github.com/vllm-project/vllm/issues/62383). CUDA illegal memory access on H200 (TP=2 + EP) in v0.32.0 and nightly; works on v0.31.1. Clearly bisectable.
3. **TP worker hang → EngineDeadError (`sample_tokens` RPC timeout)** — [Issue #41530](https://github.com/vllm-project/vllm/issues/41530). DeepSeek-V4-Pro, TP=8, MTP speculative decoding. Long-running with 18 comments, 7 👍.
4. **Ngram spec decoding produces corrupted output on hybrid GDN (Qwen3.5)** — [Issue #39273](https://github.com/vllm-project/vllm/issues/39273). Reproduced on GH200 across nightlies — silently wrong tokens, worse than a crash.
5. **NaN logits after prefix-cache hit, Qwen3.5/3.8 hybrid GDN** — [Issue #55766](https://github.com/vllm-project/vllm/issues/55766). Triggers when the previous prefill ends 4–10 tokens past a block boundary under `mamba cache mode align`.
6. **Kimi-K3 + `--kv-cache-dtype fp8` unusable on Hopper** — [Issue #51313](https://github.com/vllm-project/vllm/issues/51313). Assertion demands `use_prefill_query_quantization`, but the flag is silently ignored on non-Blackwell, so the assertion re-fires.
7. **Qwen3.5 FP8 on H200 crashes during CUDA graph capture** — [Issue #49311](https://github.com/vllm-project/vllm/issues/49311). v0.25.0 regression; marked stale but still open.
8. **SIGSEGV with NVFP4 + DFlash + torch.compile on SM120** — [Issue #48234](https://github.com/vllm-project/vllm/issues/48234). Fault in `torch::jit::invokeOperatorFromPython` during `THPVariable_transpose`, with CUDA graph PIECEWISE.
9. **Thinking budget corrupts multi-token `reasoning_end_str` under spec decode** — [Issue #58485](https://github.com/vllm-project/vllm/issues/58485). V1-engine-only; affects reasoning parsers.
10. **`ROCM_ATTN` returns wrong attention on RDNA** — [Issue #62697](https://github.com/vllm-project/vllm/issues/62697). Head size ≥ 96 with KV block size ≠ 16, and MHA at 192/256; can fault the GPU.
11. **Mooncake connector crashes after decode timeout** — [Issue #60755](https://github.com/vllm-project/vllm/issues/60755). EngineCore crash in v0.31.0.
12. **Persistent KV offload cache not namespaced by model revision** — [Issue #49261](https://github.com/vllm-project/vllm/issues/49261). Cross-revision cache bleed if you swap weights in place.

Closed today: [#53928](https://github.com/vllm-project/vllm/issues/53928) (XPU `causal_conv1d` rejecting mixed spec/non-spec tokens with MTP + chunked prefill).

## 6. What This Means for Application Developers

- **Hybrid GDN models (Qwen3.5/3.8) plus prefix caching are the sharpest edge right now.** Multiple independent reports of NaNs, gibberish, and illegal memory access cluster on GDN + prefix caching + PP or MTP. If you serve these models, pin to a known-good version, disable prefix caching when using PP, and validate output rather than trusting HTTP 200.
- **KV cache memory is the active optimization frontier.** NVFP4 MLA KV (#63203) could materially raise concurrent context or reduce replica count for DeepSeek/Kimi workloads — but it lands in the sampler/attention path, so treat it as experimental until benchmarks land.
- **Speculative decoding is where the remaining correctness bugs live.** Ngram on GDN, thinking-budget `reasoning_end_str`, and the DeepSeek TP hang all involve spec decode. Agent frameworks that depend on structured reasoning termination should test these paths explicitly.
- **Gateway authors get two relevant fixes.** ORCA `endpoint-load-metrics` under data parallel was reporting only one engine's load, silently skewing load-based routing — see [PR #63445](https://github.com/vllm-project/vllm/pull/63445) and [PR #61899](https://github.com/vllm-project/vllm/pull/61899). KV event correctness/compatibility tracking for Dynamo, llm-d, and vLLM Router is consolidated in [Issue #63149](https://github.com/vllm-project/vllm/issues/63149); programmable KV cache policy for agentic serving is an open RFC in [Issue #57103](https://github.com/vllm-project/vllm/issues/57103).
- **Priority scheduling is still coarse.** [Issue #40004](https://github.com/vllm-project/vllm/issues/40004) notes waiting-queue requests cannot preempt running ones, so a high-priority request can stall behind a long low-priority generation. Budget for head-of-line blocking if you rely on priority tiers for interactive traffic.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-10-25

## 1. Today's Highlights

No new releases in the last 24 hours, but activity remains very high (~493 PRs updated, 36 issues touched), dominated by CI flakiness tracking and a growing cluster of DeepSeek‑V4/GLM‑5.x/Kimi‑K3 serving bugs on Blackwell, Hopper, and GB300. On the performance side, work continues on reducing prefill scheduling overhead, overlapping CP KV gathers with indexer computation, and AMD‑specific shared‑expert fusion for DeepSeek‑V4 on MI355X. Two silent-correctness issues — `fast_topk_v2` returning wrong top‑k sets and a scheduler `double free` that hangs the server — are the most consequential open items for production operators.

## 2. New Model & Hardware Support

- **AMD consumer Radeon (RDNA3/RDNA4) enablement** — Tracking issue for `gfx1100/1101/1200/1201` support; ROCm path today is datacenter‑Instinct only (`gfx942/gfx950`). Community‑backed (4 👍) and includes a concrete enablement plan. [Issue #30599](https://github.com/sgl-project/sglang/issues/30599)
- **SANA‑Sprint 0.6B diffusion** — Feature request for native SGLang‑Diffusion support of the 1–4 step, 1024px text‑to‑image model. [Issue #43671](https://github.com/sgl-project/sglang/issues/43671)
- **Rust server + multimodal (Qwen‑VL)** — `SGLANG_RUST_SERVER=1` now routes image preprocessing through `sglang-processor` on `dynamo-multimodal`, and `/v1/chat/completions` accepts image input (previously refused). [PR #46291](https://github.com/sgl-project/sglang/pull/46291)
- **NPU PD disaggregation: Mooncake transfer backend** — Adds Mooncake as a KV transfer backend for NPU prefill/decode disaggregation. [PR #46223](https://github.com/sgl-project/sglang/pull/46223)
- **Out‑of‑tree unified-cache linkers** — External packages can now supply a `UnifiedCacheLinker` selected by module/class name, mirroring vLLM's KV connector pattern. [PR #40595](https://github.com/sgl-project/sglang/pull/40595)
- **Beam model kernels (3/7)** — Fused attention gate, router GEMMs, and all‑reduce boundary ops landing as part of a stacked series. [PR #45254](https://github.com/sgl-project/sglang/pull/45254)
- **Gap:** `qwen4_exp` checkpoints fail to load because Transformers does not recognize the architecture — new‑model onboarding is incomplete. [Issue #46326](https://github.com/sgl-project/sglang/issues/46326)

## 3. Performance & Optimization

- **Engram: reduce-scatter for uniform prefill shards** — Replaces full all-reduce + scatter of the embedding buffer with a direct local-shard return when attention‑DP shards are equal. [PR #46338](https://github.com/sgl-project/sglang/pull/46338)
- **Wan diffusion: project text K/V once per denoising loop** — Avoids re-projecting 512 text tokens and re-running RMSNorm in every block/step; ~0.07 ms of redundant work per block per step on Wan2.2 A14B 720p (4×B300). [PR #46246](https://github.com/sgl-project/sglang/pull/46246)
- **AMD DeepSeek‑V4 shared‑expert fusion on by default (gfx950)** — On MI355X TP4, median per‑token time is 2.0–6.6% lower at 10 of 13 measurement points (slower at none). [PR #42496](https://github.com/sgl-project/sglang/pull/42496)
- **Quark/Qwen3.8 MXFP4 shared‑expert quantization at load** — BF16 shared expert is quantized to MXFP4 during load and fused into the routed grouped GEMM under `--enforce-shared-experts-fusion` (ROCm). [PR #41899](https://github.com/sgl-project/sglang/pull/41899)
- **DSA: overlap CP KV gather with indexer computation** — Submits the KV all-gather before indexer projection/compression/scoring during interleaved CP prefill, including short prefills that skip MQA/top‑k. [PR #46179](https://github.com/sgl-project/sglang/pull/46179)
- **Scheduler prefill admission: incremental `num_held_rows`** — Removes a per‑request recomputation in the waiting‑queue admission loop (also applied to DLLM incoming requests). [PR #46337](https://github.com/sgl-project/sglang/pull/46337)
- **Fused asynchronous NaN validation kernel** — Collapses separate `isnan`/reduce/negate/device‑assert launches into one read‑only Triton kernel for logits and probability checks. [PR #43720](https://github.com/sgl-project/sglang/pull/43720)
- **Diffusion quantization: 6 confirmed perf/overhead bugs** — Code‑review findings in `multimodal_gen` quantization layer, with a companion 11‑correctness‑bug report (#46314). [PR #46302](https://github.com/sgl-project/sglang/pull/46302), [Issue #46302](https://github.com/sgl-project/sglang/issues/46302)
- **Open benchmarking questions:** SM120 (4× RTX PRO 6000, TP4) DeepSeek‑V4 prefill reported at ~2–7K tok/s vs ~12.5K tok/s via vLLM/marlin — kernel coverage gap, no fix yet. [Issue #33422](https://github.com/sgl-project/sglang/issues/33422). Separate 8× H20‑3e comparison vs. DeepInfer with `--enable-cp-decode-attn-tp`. [Issue #46310](https://github.com/sgl-project/sglang/issues/46310)

## 4. Stability & Regressions

Ranked by severity:

1. **Scheduler `double free or corruption` → permanent server hang** — Abort inside the idle‑loop invariant check (`session_held_tokens` walk) on nightly; no fix PR referenced. [Issue #42508](https://github.com/sgl-project/sglang/issues/42508)
2. **`fast_topk_v2` silently returns wrong top‑k sets** — Radix threshold bucket exceeds its 4096‑entry candidate buffer at k=2048 with long rows; silent correctness rather than a crash, so results are wrong without any signal. [Issue #36807](https://github.com/sgl-project/sglang/issues/36807)
3. **Kimi‑K3 DCP a2a garbled decode on 2‑node GB300** — 12/12 degenerate outputs under CUDA graphs when NCCL graph buffer registration is enabled; workaround `NCCL_GRAPH_REGISTER=0` restores correct output. [Issue #45367](https://github.com/sgl-project/sglang/issues/45367)
4. **GLM‑5.3‑Flash thinking degenerates into repeated `!`** — Native FP8 checkpoint, complex system prompt + multiple tools + `tool_choice=auto`; simple requests unaffected. Directly impacts agentic workloads. [Issue #36669](https://github.com/sgl-project/sglang/issues/36669)
5. **DeepSeek‑V4‑Flash NVFP4 + `flashinfer_megamoe` + dp-attention fails CUDA graph capture** — "transport finalize owes an undeclared sum group" on current `main` (`638374c389`). [Issue #46316](https://github.com/sgl-project/sglang/issues/46316)
6. **DeepSeek AWQ checkpoints fail to load** — `ModuleNotFoundError: sglang.kernel_api_logging` on CUDA; packaging/refactor fallout. [Issue #46312](https://github.com/sgl-project/sglang/issues/46312)
7. **Diffusion quantization layer: 11 correctness bugs** — ModelSlim dual‑scale MXFP4 crashes on every layer, rowwise FP8/gfx1250, MXFP4 ROCm, KitchenW4A4, Nunchaku paths. [Issue #46314](https://github.com/sgl-project/sglang/issues/46314)
8. **Non‑causal sliding‑window attention ignored in `TorchNativeAttnBackend`** — Silent attention-mask correctness issue on the fallback backend. [Issue #39300](https://github.com/sgl-project/sglang/issues/39300)
9. **compressed-tensors: untargeted layers fail with "Unable to find matching target"** — Quantized checkpoint loading. [Issue #46335](https://github.com/sgl-project/sglang/issues/46335)
10. **minimax‑m2 streaming tool args overwritten** — Next invoke's parameters clobber the current call's arguments when a delta spans `</invoke>`. [Issue #46293](https://github.com/sgl-project/sglang/issues/46293)

**Fixes in flight:** MoE block‑scale broadcast with `swap_ab` on Triton 3.9 ([PR #45889](https://github.com/sgl-project/sglang/pull/45889)); qwen3_coder streaming args not closed when `</function>` is omitted ([PR #46218](https://github.com/sgl-project/sglang/pull/46218)); hunyuan streaming string escaping to match `json.dumps` ([PR #46217](https://github.com/sgl-project/sglang/pull/46217)); Anthropic mid‑turn inline system messages attached in place to preserve prefix cache ([PR #42533](https://github.com/sgl-project/sglang/pull/42533)); modelopt‑quantized NextN drafts preserved instead of silently dequantized ([PR #44760](https://github.com/sgl-project/sglang/pull/44760)).

**Already closed/fixed:** rejection‑sampling verify kernel int32 overflow at `bs × draft_tokens × vocab ≥ 2^31` ([Issue #46299](https://github.com/sgl-project/sglang/issues/46299)).

**Closed as inactive (unfixed):** HiCache L3 segfault with nvcc 12.8/CUDA 13 ([#36302](https://github.com/sgl-project/sglang/issues/36302)); DeepEP/MXFP4 MegaMoE Hopper crash with a misleading late error ([#35557](https://github.com/sgl-project/sglang/issues/35557)); hybrid‑GDN `--dtype float16` prefill crash ([#31719](https://github.com/sgl-project/sglang/issues/31719)); MegaMoE multi‑node CUDA illegal address ([#36303](https://github.com/sgl-project/sglang/issues/36303)); `--enable-deterministic-inference` no‑op on NVFP4 + hybrid Mamba + DFlash2 ([#36291](https://github.com/sgl-project/sglang/issues/36291)); `--config` rejecting canonical `mamba-radix-cache-strategy` ([#36326](https://github.com/sgl-project/sglang/issues/36326)).

**CI:** Flaky-test tracker continues to be the highest‑traffic issue (128 comments) ([#42752](https://github.com/sgl-project/sglang/issues/42752)); AMD PR Test failure tracker opened ([#45847](https://github.com/sgl-project/sglang/issues/45847)).

## 5. What This Means for Application Developers

- **Treat agentic tool-calling as a test surface, not a given.** Streaming tool-argument bugs are clustering across model-specific parsers (qwen3_coder, hunyuan, minimax‑m2) and GLM‑5.3‑Flash degrades on multi-tool prompts. If you stream tool calls, validate that `arguments` is complete JSON on `finish_reason`, and test the case where the model ends with `</tool_call>` but omits `</function>`.
- **Prefix-cache stability matters for Claude-compatible clients.** The Anthropic adapter was collapsing mid-conversation `system` messages into the leading system block, invalidating prefix cache — a direct latency/cost hit for multi-turn agents. Worth tracking if you front SGLang with a Claude-style API.
- **Avoid DeepSeek‑V4 NVFP4 + `flashinfer_megamoe` + dp-attention on `main` right now** (CUDA graph capture failure), and avoid DeepEP on Hopper for MXFP4 expert checkpoints — that path is SM100‑only and fails late with a misleading error. Prefer `--moe-a2a-backend deepep` on multi-node Hopper MegaMoE setups.
- **On GB300 multi-node with Kimi‑K3, set `NCCL_GRAPH_REGISTER=0`** until the DCP a2a decode corruption is resolved; the failure mode is garbled text, not a crash, so it can slip through smoke tests.
- **Pin your checkpoints' loader paths.** `sglang.kernel_api_logging` missing breaks DeepSeek AWQ loads, and `qwen4_exp` has no Transformers registration — both fail at startup rather than degrading gracefully.
- **AMD shops get incremental wins:** shared-expert fusion defaults on gfx950 for DeepSeek‑V4 (~2–6.6% lower per-token on MI355X TP4), but consumer Radeon remains unsupported pending the RDNA3/4 enablement plan.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-10-25

## 1. Today's Highlights

llama.cpp shipped a dense run of rolling builds (b11856–b11869), led by a server entrypoint refactor, Hexagon flash-attention work for multicore HMX, and a Minimax-M3 quantization-scale fix. The server also saw multiple security/correctness hardening PRs around `/tools` access, grammar-trigger validation, and deeply nested JSON. On the performance side, MoE expert-cache work, GPU-resident KV rotation constants, and CUDA/NVFP4 kernel tuning are in flight, while several high-severity backend regressions remain open.

## 2. Releases & Breaking Changes

No formal API-breaking changes, but several behavioral changes worth pinning against:

- **b11867 — server entrypoint refactor** introduces `server_role` to separate router, single, and download modes. Internal refactor, but relevant to anyone patching server startup logic. [PR #31113](https://github.com/ggml-org/llama.cpp/pull/31113) · [release b11867](https://github.com/ggml-org/llama.cpp/releases/tag/b11867)
- **b11857 — `MUL_MAT_ID` with index `-1` now skips computation.** Backend implementers must honor the new semantics; Vulkan and WebGPU paths were updated. [PR #26631](https://github.com/ggml-org/llama.cpp/pull/26631) · [release b11857](https://github.com/ggml-org/llama.cpp/releases/tag/b11857)
- **b11858 — `-sm tensor` rejected for architectures using MoE amax rescaling.** Tensor split-state cannot correctly compute the full-row amax reduction. This may break existing multi-GPU launch scripts for affected MoE models. [PR #31081](https://github.com/ggml-org/llama.cpp/pull/31081) · [release b11858](https://github.com/ggml-org/llama.cpp/releases/tag/b11858)
- **b11869 — Jinja `select`/`selectattr` argument handling fixed.** Templates using `selectattr('type', 'defined')` (e.g. Functionary v3.2 with `oneOf`) now behave correctly. [PR #31059](https://github.com/ggml-org/llama.cpp/pull/31059) · [release b11869](https://github.com/ggml-org/llama.cpp/releases/tag/b11869)
- **b11861 — Minimax-M3 quantization scales forwarded to the graph**, fixing missing per-expert NVFP4 `scale2` and MXFP8 dense/shared-expert scales. [PR #31097](https://github.com/ggml-org/llama.cpp/pull/31097) · [release b11861](https://github.com/ggml-org/llama.cpp/releases/tag/b11861)

## 3. New Model & Hardware Support

- **Minimax-M3:** quant-scale plumbing fixed for NVFP4/MXFP8 experts in b11861. [PR #31097](https://github.com/ggml-org/llama.cpp/pull/31097)
- **DeepSeek V4.1 Flash:** model support PR open, touching model, mtmd, CUDA, and conversion paths. [PR #31086](https://github.com/ggml-org/llama.cpp/pull/31086)
- **Hexagon / Snapdragon:** multicore HMX flash attention now uses KV-head team splitting when `n_kv_heads` does not divide core count, avoiding the token-partition fallback where every core walks every KV head. [PR #31055](https://github.com/ggml-org/llama.cpp/pull/31055) · [release b11868](https://github.com/ggml-org/llama.cpp/releases/tag/b11868)
- **Hexagon:** generic dims collapse/reshape for unary, binary, and matmul ops is open, improving HVX/multi-NPU partitioning. [PR #31071](https://github.com/ggml-org/llama.cpp/pull/31071)
- **CUDA:** legacy Kepler cuBLAS compatibility handling proposed. [PR #30727](https://github.com/ggml-org/llama.cpp/pull/30727)
- **Vulkan:** MoE expert-cache load crash on devices lacking `shader_64b_indexing` has a fix PR skipping the `maxStorageBufferRange` pre-check for non-compute tensors. [PR #31114](https://github.com/ggml-org/llama.cpp/pull/31114)
- **SYCL:** f32→q2_0/q4_0/q5_0 rounding corrected to stop intermittent `CPY` test failures. [PR #31120](https://github.com/ggml-org/llama.cpp/pull/31120)
- **Metal:** `mul_mm_id` source rescaling removed; per-expert-token amax scaling now happens in the compute graph. [PR #30924](https://github.com/ggml-org/llama.cpp/pull/30924) · [release b11866](https://github.com/ggml-org/llama.cpp/releases/tag/b11866)
- **Laguna S 2.1 DFlash** support is still only a feature request. [Issue #26669](https://github.com/ggml-org/llama.cpp/issues/26669)

## 4. Performance & Optimization

- **Hexagon FA multicore:** KV-head team split reduces redundant KV traversal in head-split fallback scenarios. [PR #31055](https://github.com/ggml-org/llama.cpp/pull/31055)
- **MoE expert cache:** host page pinning proposed to avoid `cudaMemcpyAsync` from pageable memory during cache fills and large-batch expert uploads. [PR #31122](https://github.com/ggml-org/llama.cpp/pull/31122)
- **`llama-bench`:** `-mcmoe` / `--moe-cache-mib` parameter added so MoE cache sizes can be A/B tested directly. [PR #31121](https://github.com/ggml-org/llama.cpp/pull/31121)
- **KV-cache rotation:** static Hadamard matrices kept GPU-resident instead of host round-trips. Author reports improvements on PCIe-constrained multi-GPU setups. [PR #31073](https://github.com/ggml-org/llama.cpp/pull/31073)
- **CUDA NVFP4 MMVQ:** lane-packed rows for small-K, single-token decode path. [PR #30788](https://github.com/ggml-org/llama.cpp/pull/30788)
- **Vulkan long-context cliff:** report of ~78% decode throughput drop at 131K context due to ~1 GiB suballocation fragmentation; workaround via `GGML_VK_SUBALLOCATION_BLOCK_SIZE=4 GiB`. [Issue #27734](https://github.com/ggml-org/llama.cpp/issues/27734)
- **CUDA FA stream-K on RTX 5090:** block count not a multiple of `ntiles_dst`, reported 10–13% slower long-context prefill than RTX 4090, up to ~40% when fixed. [Issue #30784](https://github.com/ggml-org/llama.cpp/issues/30784)
- **Draft-MTP multi-GPU regression:** prompt processing roughly halved on layer split while single-GPU is unaffected. [Issue #27428](https://github.com/ggml-org/llama.cpp/issues/27428)
- **Draft-MTP over RPC:** 25–30% slower generation with unchanged drafter acceptance. [Issue #28484](https://github.com/ggml-org/llama.cpp/issues/28484)

## 5. Stability & Regressions

Ranked by severity:

1. **CUDA illegal memory access — Gemma 4 31B + draft-MTP after KV rollback/clear.** Crash occurs when MTP drafts immediately after checkpoint restore or full reprocess; MTP off avoids it. No fix PR listed. [Issue #31077](https://github.com/ggml-org/llama.cpp/issues/31077)
2. **CUDA graphs hang GPU channel on RTX 5090 Laptop / sm_120.** RC watchdog plus Xid 8; `GGML_CUDA_DISABLE_GRAPHS=1` is a complete workaround. [Issue #27330](https://github.com/ggml-org/llama.cpp/issues/27330)
3. **Metal abort on `MUL_MAT` with BF16 src1 at K ≥ 64, batch > 8.** No BF16 `mul_mm` kernel; affects Apple Silicon. [Issue #30973](https://github.com/ggml-org/llama.cpp/issues/30973)
4. **SYCL F16 NaN output on dense Qwen3.8-27B Q4_K_M since IQ3 code reorder.** Fused GEMM off avoids it; regression tied to #29107. [Issue #30892](https://github.com/ggml-org/llama.cpp/issues/30892)
5. **SYCL DeltaNet/Gated DeltaNet token soup on long prompts** for Qwen3.6/3.8 and Nemotron on Arc Pro B70. [Issue #29183](https://github.com/ggml-org/llama.cpp/issues/29183) · [Issue #29167](https://github.com/ggml-org/llama.cpp/issues/29167)
6. **Hexagon vision encoder corruption** on HTP v75, image-shape dependent, correct on CPU. [Issue #31104](https://github.com/ggml-org/llama.cpp/issues/31104)
7. **Vulkan `--moe-cache-mib` load crash** on RX 6900 XT / Windows; fix PR exists. [Issue #30697](https://github.com/ggml-org/llama.cpp/issues/30697) · [PR #31114](https://github.com/ggml-org/llama.cpp/pull/31114)
8. **Context checkpoints always invalidated on hybrid/recurrent models.** Long-session agent workloads lose cache reuse. [Issue #24055](https://github.com/ggml-org/llama.cpp/issues/24055)
9. **Server security hardening pending/landed:** `/tools` cross-origin and non-JSON request rejection closed; grammar-trigger type validation and JSON nesting depth caps proposed. [PR #29834](https://github.com/ggml-org/llama.cpp/pull/29834) · [PR #31098](https://github.com/ggml-org/llama.cpp/pull/31098) · [PR #30242](https://github.com/ggml-org/llama.cpp/pull/30242) · [PR #31099](https://github.com/ggml-org/llama.cpp/pull/31099)
10. **Other correctness issues:** Sarvam 30B MoE converter mislabels byte-fallback tokens [Issue #30879](https://github.com/ggml-org/llama.cpp/issues/30879); Flint NVFP4 tool-call XML left in reasoning [Issue #31000](https://github.com/ggml-org/llama.cpp/issues/31000); RISC-V `vfloat32m8_t` compile failure [Issue #28986](https://github.com/ggml-org/llama.cpp/issues/28986); MTP repeated `////` after long sessions [Issue #23577](https://github.com/ggml-org/llama.cpp/issues/23577); OpenVINO NPU segfault [Issue #25904](https://github.com/ggml-org/llama.cpp/issues/25904).

## 6. What This Means for Application Developers

- **Pin builds deliberately.** Rolling tags b11856–b11869 are close together and include behavioral changes (`MUL_MAT_ID -1`, `-sm tensor` rejection). Validate multi-GPU MoE launch scripts before upgrading.
- **Harden exposed servers.** If `llama-server` is reachable by untrusted clients, prioritize builds containing the `/tools` origin/content-type checks and grammar-trigger validation. Deeply nested JSON is a stack-overflow vector until nesting caps land.
- **Speculative decoding/MTP remains the highest-risk feature area.** Multi-GPU layer split, RPC split, and KV rollback scenarios have open crashes and throughput regressions. Keep MTP off for production until your exact model/backend combination is validated.
- **Chat-template compatibility improved.** The Jinja `selectattr` fix unblocks templates using `oneOf` tool schemas, but the `from_json` recursion limit is still only proposed — do not accept unbounded `chat_template_kwargs` from untrusted callers.
- **MoE cache is still experimental.** The Vulkan crash and pending pinning/benchmark work mean cache-size tuning should be treated as backend-specific and benchmarked per platform.
- **Backend parity matters.** SYCL has active NaN/token-soup regressions; Metal has a BF16 `MUL_MAT` abort; Hexagon vision is not yet reliable. Choose CUDA or CPU as conservative defaults for production inference today.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-10-25

## Today's Highlights

Ollama v0.40.11 shipped **speech generation on macOS**, exposing an OpenAI-compatible `/v1/audio/speech` streaming endpoint alongside `ollama run` audio playback. On the reliability side, three long-standing OpenAI-compat gaps were closed: UUID response IDs (replacing a 999-value collision-prone range), support for `max_completion_tokens`, and 96-character model names for long HuggingFace GGUF pulls. Meanwhile, the MLX backend drew several correctness reports (stop sequences ignored, per-layer quantization overrides, mixed-precision memory accounting).

---

## Releases & Breaking Changes

### v0.40.11 — Speech support (macOS)
- New speech capability: `ollama run` plays text or a chat model reply aloud as audio is generated.
- Apps can stream audio through the OpenAI-compatible `POST /v1/audio/speech` endpoint.
- No breaking API changes disclosed in the release notes; additive endpoint only.
- Docs: https://docs.ollama.com/capabilities/speech · Release: [v0.40.11](https://github.com/ollama/ollama/releases/tag/v0.40.11)

### API compatibility fixes merged (closed PRs)
- **UUIDs for OpenAI-compatible response IDs** — replaces `rand.Intn(999)` IDs that collided almost immediately in proxy logs (LiteLLM spend tracking). [#18656](https://github.com/ollama/ollama/pull/18656)
- **`max_completion_tokens` accepted** — newer OpenAI clients that send this field were silently dropping the generation cap; now mapped to `num_predict`. [#18608](https://github.com/ollama/ollama/pull/18608)
- No migration steps required; behavior is backwards-compatible.

---

## New Model & Hardware Support

- **Nemotron 3.5 Bolt 74B-A9B (MLX)** — new model support with per-layer expert configurations, prequantized affine 3-bit import with per-module quantization metadata, and mixed-precision expert stacking via the DFlash runner. [#19229](https://github.com/ollama/ollama/pull/19229)
- **Long HF GGUF model names** — model-name cap raised from 80 to 96 chars so HuggingFace repo/quant suffixes (e.g., `Qwen3.8-...-IQ3_M.gguf`) validate on pull. [#18520](https://github.com/ollama/ollama/pull/18520)
- **Gemma 4 compact vision towers** — small-image grid sizing fixed to preserve the intended 70-token budget, improving detail retention on small images. [#19247](https://github.com/ollama/ollama/pull/19247)

---

## Performance & Optimization

- **Multi-model VRAM accounting fix** — llama-server buffer lines were deduped assuming one model per subprocess, causing a speculative draft's lines to replace the target's; a 30B DFlash model reported ~2.3 GB in `ollama ps` and scheduler accounting. Fix in progress. [#17857](https://github.com/ollama/ollama/pull/17857)
- **Log-chunk memory accounting** — pipe reads can split llama-server log lines, dropping buffer sizes and layer counts; fix retains incomplete lines and parses completed lines under the accounting lock. [#19263](https://github.com/ollama/ollama/pull/19263)
- **MLX vs. llama.cpp throughput regression** — `flint:40b-a2b-gguf-nvfp4` (MLX) measured significantly slower than `flint:40b-a2b-qat-int4` on an M3 MacBook Pro (36 GB); issue closed. [#19254](https://github.com/ollama/ollama/issues/19254)

---

## Stability & Regressions

Ranked by severity:

1. **MLX per-layer quantization overrides ignored on import** — mixed-precision MLX safetensors (4-bit weights + per-layer 8-bit overrides) fail to load with `quantized_matmul` shape mismatch; the runner applies the global `bits` value to tensors built for overrides. No fix PR yet. [#18789](https://github.com/ollama/ollama/issues/18789)
2. **`options.stop` ignored on MLX engine** — generation runs to EOS or `num_predict`, unlike the llama.cpp path. Fix PR open: [#19267](https://github.com/ollama/ollama/pull/19267). Issue: [#19268](https://github.com/ollama/ollama/issues/19268)
3. **Anthropic-compat prefix cache defeated** — `system`-role messages inside `messages` are hoisted to the top system block on `/v1/messages`, breaking prefix caching for Claude Code (which appends a system message after every tool result). [#18431](https://github.com/ollama/ollama/issues/18431)
4. **`OLLAMA_NUM_PARALLEL=1` appears ineffective** — two concurrent sessions still process simultaneously, problematic under tight memory budgets. [#19262](https://github.com/ollama/ollama/issues/19262)
5. **Cloud: Beam empty responses via OMP** — intermittent empty assistant replies when accessing the Ollama Cloud Beam model through OMP, while the same model works via OpenCode Go. [#19270](https://github.com/ollama/ollama/issues/19270)
6. **Tool call integer args rendered as decimals** — JSON args like `1` become `1.0`, altering what the model sees; fix PR preserves original numeric representation. [#19252](https://github.com/ollama/ollama/pull/19252)
7. **Native `think` parameter ignored on OpenAI endpoints** — `"think": false` had no effect for Gemma 4 and other thinking models via `/v1/chat/completions` and `/v1/completions`; fix under review. [#19260](https://github.com/ollama/ollama/pull/19260)
8. **`/compact` failures on large Codex sessions** — request-body 413s abort compaction; retry logic drops images and old history to recover. [#19266](https://github.com/ollama/ollama/pull/19266)
9. **Linux installer false success** — `install.sh` streamed downloads into `sudo tar`; on slow links the sudo cache expired mid-download and failures weren't reported. Fix downloads archive first and checks exit code. [#19243](https://github.com/ollama/ollama/pull/19243)

---

## What This Means for Application Developers

- **Speech is now a first-class modality.** If you're building voice agents or accessibility features, target the OpenAI-compatible `/v1/audio/speech` endpoint for portability. It's macOS-only for now, so plan a fallback for Linux/Windows hosts.
- **OpenAI client compatibility is materially better.** Upgrading past today's merges fixes response-ID collisions in observability/spend proxies, works with clients sending `max_completion_tokens`, and lets long HF GGUF quant suffixes pull cleanly. Check for those fixes in the next tagged release if you're on v0.40.11.
- **If you're on Apple silicon + MLX, be defensive.** Stop sequences are silently ignored ([#19268](https://github.com/ollama/ollama/issues/19268)) and mixed-precision MLX imports can fail to load ([#18789](https://github.com/ollama/ollama/issues/18789)). Enforce turn limits client-side (`num_predict`), and prefer the llama.cpp path where stop-sequence correctness is critical.
- **Anthropic-compat users should avoid `system` messages inside `messages`.** Until [#18431](https://github.com/ollama/ollama/issues/18431) is fixed, that pattern defeats prefix caching and inflates token spend for agent loops like Claude Code. Keep system prompts in the top-level `system` field.
- **Memory reporting for multi-model / speculative decoding is being corrected.** If you're autoscaling or scheduling based on `ollama ps` VRAM figures, expect underreporting (e.g., ~2.3 GB for a 30B DFlash) until [#17857](https://github.com/ollama/ollama/pull/17857) and [#19263](https://github.com/ollama/ollama/pull/19263) land — don't trust those numbers for capacity planning yet.
- **Thinking models need the native field on OpenAI endpoints.** `"think": false` isn't yet honored there; if you need to disable reasoning for latency-sensitive routes, use the native API until [#19260](https://github.com/ollama/ollama/pull/19260) merges.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-10-25

## 1. Today's Highlights

A supply-chain incident dominates the day: **litellm 1.107.0 was published from a lookalike repo (`Berri-AI/litellm`) and shipped a credential stealer plus a `sitecustomize` RCE loader**; PyPI and all Docker tags pointing at that build have been pulled ([#47826](https://github.com/BerriAI/litellm/issues/47826)). Separately, four patch releases landed (1.106.3 / 1.105.4 / 1.104.6 / 1.103.8), all carrying a **cosign image-signing key rotation** that will break existing signature-verification pipelines. A cluster of spend-accounting and budget-enforcement bugs continues to accumulate comments, with several active fix PRs.

## 2. Releases & Breaking Changes

- **v1.106.3, v1.105.4, v1.104.6, v1.103.8** ([releases](https://github.com/BerriAI/litellm/releases)) — backport lines all released within 24h. The only documented change in each is an **image-signing key rotation**: every Docker image and Helm chart is now signed with the new cosign key from commit [`e82a3c6`](https://github.com/BerriAI/litellm/commit/e82a3c646908ba0a6ee0beddc4310d7a6cac4065). **Action:** update `cosign verify` key material / Kyverno or Connaisseur policies before upgrading, or admission control will reject the new images.
- **Do not install 1.107.0.** It is not a legitimate release — see Stability below. 1.106.3 is the current highest trusted tag; pin accordingly and verify the published artifact hash.
- No API or config schema changes announced today beyond the signing key.

## 3. New Model & Hardware Support

- **NVIDIA Nemotron 3.5 Super VL 120B-A12B on W&B** — repriced from the CoreWeave Forge Serverless Inference rates instead of stale OpenRouter CoreWeave endpoint prices ([#48264](https://github.com/BerriAI/litellm/pull/48264)).
- **Cerebras Llama 3.1 hyphenated aliases** — `cerebras/llama-3.1-8b` / `-70b` added to the price/context map ($0.10 per 1M in+out, 128k context) ([#48178](https://github.com/BerriAI/litellm/pull/48178), [#48158](https://github.com/BerriAI/litellm/pull/48158)).
- **Uncatalogued models on JSON-defined OpenAI-compatible providers now retain `tools`** — a fix PR restores tool passing for models absent from the catalog rather than treating missing capability declarations as unsupported ([#48267](https://github.com/BerriAI/litellm/pull/48267) → [#48145](https://github.com/BerriAI/litellm/issues/48145)).
- **Uncatalogued ChatGPT models routed through the Responses bridge** instead of 404ing on Chat Completions ([#48244](https://github.com/BerriAI/litellm/pull/48244)).
- **Bedrock Nova 2 Lite**: invalid `apac.` geo inference ID replaced with `jp.` ([#33583](https://github.com/BerriAI/litellm/pull/33583), stale).
- **OpenAI 12-hour prompt-cache write pricing** parsed for Batch and Ultrafast tiers ([#48259](https://github.com/BerriAI/litellm/pull/48259)).
- **Azure transcription**: realtime transcription sessions moved to the GA `client_secrets` endpoint and corrected path (`/openai/realtimeapi/transcription_sessions`), fixing 404s ([#48067](https://github.com/BerriAI/litellm/pull/48067)).

## 4. Performance & Optimization

No throughput or kernel-level work in today's window. Relevant resource-lifetime and correctness fixes:

- **Anthropic `/v1/messages` streams now close the upstream request on client disconnect** before the first token, preventing leaked upstream sockets/connections ([#48220](https://github.com/BerriAI/litellm/pull/48220) → [#48197](https://github.com/BerriAI/litellm/issues/48197)).
- **Claude gateway model discovery restored** on `/claude_code_gateway` base URLs (previously 404, forcing clients to dual-configure a base URL) ([#48221](https://github.com/BerriAI/litellm/pull/48221), [#48205](https://github.com/BerriAI/litellm/pull/48205)).
- **`mock_response=""`** now short-circuits to the mock path instead of issuing a real provider request — meaningful for test-suite latency and accidental spend ([#48266](https://github.com/BerriAI/litellm/pull/48266)).
- **`/v2/team/list` returns per-team `caller_membership`** (spend, effective budget, reset date), removing N+1 UI queries per team ([#45674](https://github.com/BerriAI/litellm/pull/45674)).

## 5. Stability & Regressions

**Critical — supply chain / security**
- **1.107.0 compromised**: `cook.pth` credential stealer + `sitecustomize` RCE loader, built from lookalike repo `Berri-AI/litellm`. PyPI removed 1.107.0; `latest`, `main-latest`, `main-stable` on `ghcr.io/berriai/litellm{,-database,-non_root}` and `docker.io/litellm/litellm` no longer resolve to it ([#47826](https://github.com/BerriAI/litellm/issues/47826), 33 comments, 19 👍). **If you pulled `:latest` on 2026-10-21, treat host credentials, `~/.aws`, `~/.ssh`, cloud tokens and any env-secret material as compromised and rotate.**

**High — budget enforcement and spend accounting**
- Budget **bypassed** in v1.82.3 for key/user `max_budget` despite spend exceeding it ([#26672](https://github.com/BerriAI/litellm/issues/26672)).
- `BudgetExceededError` raised from **stale spend** while `/key/info` reports under-budget ([#27735](https://github.com/BerriAI/litellm/issues/27735)).
- Keys over budget **re-admitted after ~60s idle** until the batch writer flushes spend ([#43732](https://github.com/BerriAI/litellm/issues/43732)).
- **False 429s under sustained load** ("Current cost" = max_budget + recent spend, no Redis, self-heals in ~2 min) ([#36926](https://github.com/BerriAI/litellm/issues/36926)).
- **Project budgets excluded** from the atomic pre-call reservation path — teams/orgs/keys are covered, projects are read-time only ([#34101](https://github.com/BerriAI/litellm/issues/34101)).
- `max_end_user_budget_id` never persisted to `LiteLLM_EndUserTable`, so budget-reset jobs skip auto-created end users ([#25386](https://github.com/BerriAI/litellm/issues/25386)).

**High — agent tool-calling regression**
- On 1.106, JSON-defined OpenAI-like providers **silently drop `tools`** when the model isn't in the catalog (worked on 1.81 and when re-declared as `openai`). Regression from PR #21244; **fix PR exists** ([#48145](https://github.com/BerriAI/litellm/issues/48145) → [#48267](https://github.com/BerriAI/litellm/pull/48267)).
- `mcp_semantic_tool_filter` stripped **all** tools (N→0) for the standard nested `tools` format — closed ([#31909](https://github.com/BerriAI/litellm/issues/31909)).

**Medium — cost accuracy (billing drift)**
- `completion_cost()` raises `ModelNotMappedError` for OpenAI-aliased custom providers (e.g. `groq/openai/gpt-oss-120b`); two competing fix PRs preserve provider identity and honor explicit args ([#48226](https://github.com/BerriAI/litellm/issues/48226) → [#48262](https://github.com/BerriAI/litellm/pull/48262), [#48263](https://github.com/BerriAI/litellm/pull/48263)).
- Turns billed at **$0** when `provider_response_model` is a slug missing from the price map — 74 of 89 billable streamed calls logged free in one report ([#42161](https://github.com/BerriAI/litellm/issues/42161)).
- Streaming drops upstream `usage` when the final chunk has a **non-empty `choices` array** — `cached_tokens` lost, billed at full input rate ([#36168](https://github.com/BerriAI/litellm/issues/36168)).
- Usage lost entirely when clients disconnect mid-stream ([#14457](https://github.com/BerriAI/litellm/issues/14457)) or during non-streaming Bedrock calls ([#13245](https://github.com/BerriAI/litellm/issues/13245)).
- Response-cache hits write `spend = 0` but replay original token columns — token reports aggregate a basis you must choose deliberately ([#39057](https://github.com/BerriAI/litellm/issues/39057)).
- Reused provider response IDs collide as spend-log primary keys ([#35563](https://github.com/BerriAI/litellm/issues/35563)); `end_user` pinned to the first request's `user` on a shared key — regression in v1.87.0 ([#31441](https://github.com/BerriAI/litellm/issues/31441)).
- Null usage on native decisions calls reported as zero tokens / $0 cost, indistinguishable from unbilled — fix PR keeps usage unknown ([#48261](https://github.com/BerriAI/litellm/pull/48261)).

**Low — translation / compatibility**
- `get_supported_openai_params` still advertises `temperature` for Claude Opus 4.7, which Anthropic rejects with `temperature is deprecated for this model` ([#26444](https://github.com/BerriAI/litellm/issues/26444)).
- OpenRouter cost in `usage.cost` still lost in streaming mode — closed ([#16021](https://github.com/BerriAI/litellm/issues/16021)).
- Router `.acompletion` still does not fire `CustomLogger` hooks — closed as stale, unfixed ([#8842](https://github.com/BerriAI/litellm/issues/8842)).
- **Three-day warning:** OpenCode Go will reject requests lacking `x-opencode-session` starting 09/05; LiteLLM does not send it ([#39503](https://github.com/BerriAI/litellm/issues/39503)).

## 6. What This Means for Application Developers

- **Pin and verify now.** Move to `1.106.3` (or the latest patch on your minor line) by digest, not by tag, and never resolve `:latest` in production. Update your cosign public key first — the rotation at `e82a3c6` will fail closed in most admission pipelines. If you ran 1.107.0 on 2026-10-21, rotate every credential reachable from that container.
- **Budget limits are advisory, not enforcement, on several paths.** Key/team budgets can be exceeded or spuriously tripped; project budgets and auto-created end-user budgets are not in the atomic reservation path. If you resell or gate on spend, add an independent upstream quota (provider-side limits) rather than relying solely on LiteLLM's pre-call check.
- **Don't trust per-request cost for streaming.** Zero-cost rows appear when price-map slugs are missing, when the final chunk carries `choices`, and when clients disconnect. Reconcile against provider invoices, and prefer explicit `model` / `custom_llm_provider` args in `completion_cost()` until the #48262/#48263 fixes land.
- **Agent builders on 1.106 with JSON-defined OpenAI-compatible providers should watch #48145** — tools are silently stripped for uncatalogued models, which fails closed in a confusing way (the model responds, just without tool calls). Until #48267 merges, either declare the model under `openai` or add it to the catalog.
- **On disconnect-heavy workloads** (chat UIs, cancellable agents), #48220 and #48261 are the two fixes worth tracking: one stops upstream socket leakage on Anthropic streams, the other stops silently reporting missing usage as $0.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-10-25

## Today's Highlights
No releases landed in the last 24 hours; activity is dominated by a **serious Studio GGUF loading regression on Windows** (two overlapping reports, both still open) and a **large push on multi-GPU training** with pipeline parallelism v1 and tensor parallelism v2. On the serving side, the KV-preemption work that lets parallel chats share a single llama-server cache continues to evolve, with llama-server taking ownership of parking.

## Releases & Breaking Changes
- **No new releases in the last 24h.** No migration notes.
- ⚠️ **Studio `v0.1.910-beta` is effectively unusable for local model loading on Windows** per multiple reports — see Stability. Treat as a breaking behavior change introduced by the 2026-10-23 update rather than a formal release note.
- **Behavior change (in progress): Studio now forces `preserve_thinking=true` at llama-server launch**, which overrides a user-supplied chat template and makes `--no-reasoning-preserve` a no-op: [Issue #15115](https://github.com/unslothai/unsloth/issues/15115). Relevant to anyone pinning custom templates for reasoning models.
- **API surface note:** embedding models are served on `/embeddings` but do not appear in the GUI or `/v1/models` listing: [Issue #15110](https://github.com/unslothai/unsloth/issues/15110) — a discovery/compat problem for OpenAI-compatible gateways.
- **Docker:** vLLM/SGLang are re-downloaded on every container update; request for persistence via volume or env-var-triggered re-download was closed: [Issue #15016](https://github.com/unslothai/unsloth/issues/15016).

## New Model & Hardware Support
- **Pipeline parallelism v1** — `apply_pipeline_parallel` for dense decoders via `torch.distributed.pipelining` (GPipe and 1F1B schedules), in `unsloth/models/pp_native.py`, mirroring the FSDP2 (`shard_model`) and TP (`apply_tensor_parallel`) APIs with no per-architecture code: [PR #15175](https://github.com/unslothai/unsloth/pull/15175).
- **Tensor parallelism v2** — adds sharded loading, data-parallel × tensor-parallel composition, and Trainer integration; a 16-bit 8B LoRA now loads and trains with TP on **2× 16 GB GPUs**: [PR #15174](https://github.com/unslothai/unsloth/pull/15174).
- **Metal / Apple Silicon robustness** — reserve the DSpark/DFlash sidecar in `--fit-target` and recover from Metal OOM instead of leaving llama-server in a permanently failing state: [PR #14431](https://github.com/unslothai/unsloth/pull/14431).
- **Wan video** — streaming encode now works under model/sequential offload (previously required decoder weights resident on CUDA at render start), extending low-VRAM fp16 paths like T4: [PR #15173](https://github.com/unslothai/unsloth/pull/15173).
- **Distillation roadmap (discussion, no code):** offline top-k teacher logprobs and served teachers to keep DeepSeek/Kimi-K2-class teachers out of training memory: [Issue #11556](https://github.com/unslothai/unsloth/issues/11556).

## Performance & Optimization
- **KV preemption for parallel chats** — one llama-server holds one KV cache; Studio launches with `--parallel`, and llama-server owns parking when it supports `--preempt-ram`, letting concurrent chats share a single cache instead of duplicating it: [PR #10301](https://github.com/unslothai/unsloth/pull/10301) (open, long-running, stacked changes).
- **Wan text-encode caching** — prompt cache now keys positive and negative prompts separately, so an unchanged negative prompt is encoded once instead of on every new prompt; meaningful on streamed-UMT5 setups (T4-class fp16-only cards) where the dense encoder does not fit beside the DiT: [PR #15166](https://github.com/unslothai/unsloth/pull/15166).
- **Streamed VAE decode + encode overlap** for Wan clips under memory pressure: [PR #15173](https://github.com/unslothai/unsloth/pull/15173).
- **Multi-GPU scaling** via PP v1 / TP v2 (above) is the main memory-capacity lever landed this cycle: [PR #15175](https://github.com/unslothai/unsloth/pull/15175), [PR #15174](https://github.com/unslothai/unsloth/pull/15174).

## Stability & Regressions
Ranked by severity; all are user-facing for Studio/Desktop deployments.

1. **CRITICAL — every GGUF load killed by a false port-ownership check (Windows).** After the 2026-10-23 update, Studio spawns llama-server, the server loads the model and answers `/health` 200, then Studio kills it with `llama-server failed to start. Check that the GGUF file is valid…`. Root cause is the new "our child is not listening" check in the backend log. **Open, 4 comments, no fix PR identified:** [Issue #15022](https://github.com/unslothai/unsloth/issues/15022).
2. **CRITICAL — all tested models fail to load on Desktop `v0.1.910-beta` (Windows 11 Insider 25H2).** Likely the same root cause as above; reported separately. **Open:** [Issue #15142](https://github.com/unslothai/unsloth/issues/15142).
3. **HIGH — Windows AMD `gfx1201` ROCm launch failures after the llama.cpp `b11696` bump**, with display flashing and hard system lockup. **Closed**, so presumably addressed — verify against your pinned llama.cpp build: [Issue #14085](https://github.com/unslothai/unsloth/issues/14085).
4. **HIGH — long tool-heavy chats stall for minutes in CPU conversation-archive embedding before llama.cpp receives any inference request**, and **Stop does not cancel archiving**. **Closed:** [Issue #14519](https://github.com/unslothai/unsloth/issues/14519).
5. **MEDIUM — image generation core-dumps** in Studio: [Issue #15172](https://github.com/unslothai/unsloth/issues/15172). **MEDIUM — embedding model disappears from the GUI and `/v1/models`** while still answering on `/embeddings`: [Issue #15110](https://github.com/unslothai/unsloth/issues/15110).
6. **MEDIUM — `preserve_thinking` forced at launch** overrides user chat templates and negates `--no-reasoning-preserve`: [Issue #15115](https://github.com/unslothai/unsloth/issues/15115). Setting also fails to persist across eject/reload: [Issue #14743](https://github.com/unslothai/unsloth/issues/14743).
7. **LOW — Model Hub filter regression** (closed): [Issue #14221](https://github.com/unslothai/unsloth/issues/14221). Assistant messages not editable after refresh: [Issue #15004](https://github.com/unslothai/unsloth/issues/15004).
8. **Fix in flight (low severity, validation only):** reject training `random_seed` outside `[0, 2**32 - 1]` up front instead of after model load — [PR #15176](https://github.com/unslothai/unsloth/pull/15176).

Also notable: 180 PRs updated in 24h, mostly dependabot churn (setuptools, dompurify, vite, katex, tauri, codeql-action, pip group), plus a batch of Studio UI consistency PRs (radius tokens, button padding, storage overview, pinned Run-settings footer, API page fonts).

## What This Means for Application Developers
- **Do not auto-update Unsloth Studio/Desktop on Windows right now.** The 2026-10-23+ builds break local GGUF serving entirely via a bad process-liveness check — pin to a pre-2026-10-23 build until #15022/#15142 close. This affects any app relying on Studio as a local OpenAI-compatible endpoint.
- **Don't trust `/v1/models` for capability discovery.** Embedding models are live on `/embeddings` but absent from model listings; gateways/routers that build routing tables from `/v1/models` will silently drop your embedding tier. Probe `/embeddings` directly or hardcode: [Issue #15110](https://github.com/unslothai/unsloth/issues/15110).
- **Custom upstream headers are still unsupported for remote providers**, which blocks LiteLLM-style per-request tracking and feature-selection headers. If you need attribution or routing metadata, terminate it at your own proxy in front of Unsloth: [Issue #14811](https://github.com/unslothai/unsloth/issues/14811).
- **Reasoning-token handling is not fully under your control.** Studio injects `preserve_thinking=true`, overriding custom chat templates and neutralizing `--no-reasoning-preserve`. If you depend on stripping or preserving thinking blocks, validate the rendered prompt server-side rather than assuming your template won: [Issue #15115](https://github.com/unslothai/unsloth/issues/15115).
- **Long agent contexts are a latency trap.** Conversation-archive embedding runs on CPU and blocks inference, and Stop won't cancel it — budget for multi-minute stalls on long tool-heavy threads or cap history client-side: [Issue #14519](https://github.com/unslothai/unsloth/issues/14519).
- **Multi-GPU fine-tuning is becoming practical without architecture-specific code.** TP v2 + PP v1 mean 8B LoRA fits on 2× 16 GB and deeper pipelines are a single call away — worth prototyping now if you're capacity-constrained on single-card VRAM: [PR #15174](https://github.com/unslothai/unsloth/pull/15174), [PR #15175](https://github.com/unslothai/unsloth/pull/15175).
- **Multi-host scheduling remains unimplemented** (feature request, 8 👍): [Issue #4858](https://github.com/unslothai/unsloth/issues/4858) — plan orchestration at the container/host layer for now.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*