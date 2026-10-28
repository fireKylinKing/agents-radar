# ArXiv AI Research Digest 2026-10-28

> Source: [ArXiv](https://arxiv.org/) (cs.AI, cs.CL, cs.LG) | 50 papers | Generated: 2026-10-28 01:35 UTC

---

## Today’s Highlights

Today’s submissions center on making LLM state cheaper and more reusable: DCKV learns to code prompt KV caches, self-keyed memorization stores dialogue recall in frozen parameters, and Select-TTT selectively updates fast weights at inference. Agent work is shifting toward closed-loop self-improvement, with evolving teams, co-evolving rubrics/harnesses, and rubric mining for deep-research evaluation. Efficient action selection is also a theme: GUI-JEPA scores candidate GUI actions with latent JEPA policies, while policy-compatible joint decoding targets hard reasoning when all rollouts fail. Evaluation and calibration remain active, from SetGRPO’s treatment of noisy graders to LLM-based Olympiad proof grading. Finally, multimodal and embodied systems are reusing pretrained generative priors—TAPESTRY for video-to-robot control and Tap2Listen for instant speaker switching.

## Key Papers

### 🧠 Large Language Models (architecture, training, alignment, evaluation)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [DCKV: Learned Transform Coding of the Prompt KV Cache in Hybrid Language Models](http://arxiv.org/abs/2610.36950v1) | Egor Maximov, Aleksei Merkelov, Vasiliy Alekseev et al. | DCKV introduces a differentiable codec for prompt KV caches in hybrid language models, targeting the cache that still grows with prompt length in full-attention layers. It matters because cheaper, learned KV-cache storage and movement can reduce memory and latency for prompt reuse in hybrid architectures. |
| [Self-Keyed In-Parameter Memorization for External Memory-Free Dialogue Recall in Frozen Language Models](http://arxiv.org/abs/2610.36948v1) | XinYu Piao, Myunghoon Ryu, Joongheon Kim et al. | Proposes self-keyed in-parameter memorization so frozen language models can recall dialogue content without external memory or prior knowledge of future questions. This matters for privacy-constrained or storage-limited deployments where conversational recall must live inside model parameters. |
| [Select-TTT: Query-Conditioned Selective Fast Weight Updates for In-Place Test-Time Training](http://arxiv.org/abs/2610.36794v1) | Yue Wu, Yang Zhang, Weinan Gan et al. | Select-TTT makes in-place test-time training query-conditioned by selectively updating fast-weight parameters when relevant to the incoming query. It improves the practicality of adapting deployed LLMs to context without treating all context as transient attention state. |
| [SetGRPO: Calibrating Grader-Induced Policy Optimization](http://arxiv.org/abs/2610.36807v1) | Jiale Huang, Zixu Li, Zhiwei Chen et al. | SetGRPO analyzes how grader randomness perturbs GRPO updates and proposes calibration for rubric-based reinforcement learning with noisy grader feedback. It matters for open-ended LLM training, where score discrepancies alone do not capture how grader noise changes policy optimization. |
| [Can LLMs Reliably Grade Olympiad Proofs? A Study of Mathematical Verification with LLMs](http://arxiv.org/abs/2610.36767v1) | Azim Ospanov, Zijin Feng, Ding Ding et al. | Studies whether LLMs can reliably verify natural-language Olympiad proofs, a harder setting than solving competition math because proofs require checking logical structure and rigor. The findings matter for using LLMs as mathematical judges and for benchmark design around automated proof evaluation. |

### 🤖 Agents & Reasoning (planning, tool use, multi-agent, chain-of-thought)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Vision-Language Agents Can Learn in Their Own Latent Worlds](http://arxiv.org/abs/2610.36925v1) | Haoxi Li, Sikai Bai, Jianfei Ma et al. | Shows that pretrained vision-language agents can improve policies through online interaction in learned latent worlds rather than relying only on external rewards or hand-designed environments. This matters for continual agent learning because it leverages VLM world knowledge while reducing dependence on costly real-world interaction. |
| [RubricAtlas: Mining Type-Level Rubrics from Expert Reports to Evaluate Deep Research Agents](http://arxiv.org/abs/2610.36878v1) | Delin Wang, Sinuo Wang, Rui Sun et al. | Mines type-level rubrics from expert reports to evaluate deep research agents beyond fact presence, making report-level analytical requirements explicit. It matters because deep-research evaluation needs scalable, query-specific criteria that capture what analyses an agent actually delivers. |
| [GUI-JEPA: Candidate-Conditioned JEPA Latents as a Fast Action Policy for Mobile GUI Agents](http://arxiv.org/abs/2610.36877v1) | Bingchao Wang, Yongcheng Jing, Shunyu Liu et al. | Proposes candidate-conditioned JEPA latents as a fast action policy for mobile GUI agents, avoiding token-by-token VLM decoding and keeping large models out of the inner action loop. It matters for latency- and memory-constrained GUI automation where many candidate actions must be scored quickly. |
| [When All Rollouts Fail: Policy-Compatible Joint Decoding for Hard-Problem Reasoning](http://arxiv.org/abs/2610.36849v1) | Zehao Wang, Hanzhu Chen, Runquan Gui et al. | Addresses RLVR’s learning cliff when every on-policy rollout fails by introducing policy-compatible joint decoding for hard-problem reasoning. It matters because it offers a way to extract learning signal and better reasoning when binary verifiable rewards are uniformly zero. |
| [Evolving Agent Teams](http://arxiv.org/abs/2610.36848v1) | Shiyi Cao, Ziming Mao, Dacheng Li et al. | Introduces Evolving Agent Teams to overcome local optima in coding agents on expert-written GPU kernels such as FlashInfer. It matters because team-level evolution and experience sharing can improve open-ended software engineering beyond single-agent iteration. |
| [When the Critic Falls Behind: Co-Evolving Rubrics and Harnesses for Continual Agent Self-Improvement](http://arxiv.org/abs/2610.36826v1) | Hao Chen, Yukun Yan, Ziyu Han et al. | Studies continual agent self-improvement when supervision lags behind evolving harnesses, proposing co-evolution of rubrics and harnesses. It matters because static critics can misguide agents that adapt their memory, tools, and execution policies over time. |

### 🔧 Methods & Frameworks (new techniques, benchmarks, efficiency improvements)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Batching the Reflective Optimization Loop: Parallel Proposals Make GEPA Faster and Better](http://arxiv.org/abs/2610.36774v1) | Jialin Zhang, Lakshya A Agrawal, Shangyin Tan et al. | Extends the GEPA reflective optimizer to propose and evaluate batches of candidates in parallel, making the optimization loop faster and better. It matters for agentic systems where reflective text optimization is resource-hungry and sequential evaluation leaves capacity idle. |
| [God Does Not Play Dice: Enforcing Batch Invariance in Agentic Kernel Generation](http://arxiv.org/abs/2610.36623v1) | Youwei Bao, Chengpeng Wang, Simin Chen | Analyzes batch invariance in LLM-generated GPU kernels and proposes enforcing invariance so unchanged requests produce unchanged outputs under dynamic batching. It matters for serving correctness and reproducibility, where batch context can otherwise alter numerical results. |

### 📊 Applications (domain-specific, multimodal, code generation)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [TAPESTRY: A Single Backbone for Video Generation and Robot Control](http://arxiv.org/abs/2610.36823v1) | Jeremy A. Collins, Seungjae Lee, Rachanon Wachakorn et al. | TAPESTRY introduces a single backbone for both video generation and robot control, avoiding test-time video generation or scratch action heads. It matters because pretrained video priors can be reused more directly for embodied control. |
| [Tap2Listen: Instant Target Switching for Streaming Audio-Visual Speaker Extraction](http://arxiv.org/abs/2610.36643v1) | Hansol Park, Minkyung Song, Zinu Kim et al. | Presents a streaming audio-visual target speaker extraction system that supports immediate target switching instead of fixing one speaker per clip. It matters for real conversational agents and assistive audio, where speaker focus must change rapidly. |

## Research Trend Signal

Across today’s submissions, the dominant signal is a shift from scaling context windows toward managing model state more intelligently. KV-cache codecs, in-parameter dialogue memory, and query-conditioned test-time fast weights all try to make reuse cheaper and more selective, especially for hybrid and frozen models. Agent research is similarly moving from single-run prompting to closed-loop self-improvement: evolving teams, co-evolving rubrics, harness refinement, and rubric mining all treat evaluation and verifier feedback as first-class training signals. At the same time, work on GUI agents and hard-problem decoding shows growing interest in fast, policy-compatible action selection when full VLM decoding or all-rollout RL is too slow or brittle. Embodied and multimodal applications are converging on pretrained video/vision-language backbones, while reliability concerns—such as batch invariance in LLM-generated kernels—are becoming explicit engineering targets. Overall, efficiency, verifier-grounded adaptation, and deployment robustness are replacing raw capability as the central research axes.

## Worth Deep Reading

1. **[DCKV: Learned Transform Coding of the Prompt KV Cache in Hybrid Language Models](http://arxiv.org/abs/2610.36950v1)** — The KV cache remains one of the most concrete memory and bandwidth bottlenecks in LLM serving. A differentiable codec for hybrid-model caches could translate directly into better prompt reuse, lower serving cost, and new design choices for hybrid attention architectures.

2. **[Evolving Agent Teams](http://arxiv.org/abs/2610.36848v1)** — This paper tackles a realistic failure mode of coding agents: convergence to local optima on expert-written GPU kernels. The team-evolution framing is important because it moves agent self-improvement beyond single-agent iterative refinement toward population-level experience sharing.

3. **[TAPESTRY: A Single Backbone for Video Generation and Robot Control](http://arxiv.org/abs/2610.36823v1)** — Reusing pretrained video-generation priors for robot control without costly test-time video generation is a promising path for embodied AI. The single-backbone design is worth reading closely for its implications on multimodal policy learning and compute-efficient robotics.

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*