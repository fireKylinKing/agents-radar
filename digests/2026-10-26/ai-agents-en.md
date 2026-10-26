# OpenClaw Ecosystem Digest 2026-10-26

> Issues: 358 | PRs: 500 | Projects covered: 5 | Generated: 2026-10-26 00:37 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-10-26

## 1. Today's Overview

OpenClaw is in a high-throughput stabilization phase: **358 issues** and **500 PRs** were updated in the last 24 hours, with **270 PRs merged/closed** and **72 issues closed**, yet **no new release** was cut. Activity is very high, but the signal is dominated by reliability work rather than feature velocity — the top three most-commented issues are all gateway stability problems (SQLite WAL growth, plugin capture blocking, and blocked WAL checkpoints). The project is simultaneously running a broad performance-optimization campaign (a large batch of `perf(...)` PRs from steipete) and fielding a heavy cluster of Windows-specific startup, upgrade, and plugin-staging regressions. Overall health reads as **active but under strain**: strong maintainer throughput, but multiple open P0s and repeated 2026.9.x/2026.10.x regressions suggest release quality is the primary risk.

## 2. Releases

**None.** No new releases in the last 24 hours. This is notable given the volume of merged/closed PRs and the number of release-blocker-tagged issues (`impact:ux-release-blocker` on #143524, #153426, #166634), implying fixes are accumulating toward a future cut rather than shipping incrementally.

## 3. Project Progress

**270 PRs were merged or closed** in the last 24 hours (230 remain open). Visible closures and advances in the top-comment set:

- **Performance campaign** (steipete): a coordinated series reducing CPU/allocation across agents, plugins, gateway, secrets, and UI — e.g. [#183968](https://github.com/openclaw/openclaw/pull/183968) (Code Mode result serialization), [#183997](https://github.com/openclaw/openclaw/pull/183997) (Linux CPU sampling), [#184004](https://github.com/openclaw/openclaw/pull/184004) (array copy elimination), [#184001](https://github.com/openclaw/openclaw/pull/184001), [#183990](https://github.com/openclaw/openclaw/pull/183990), [#184002](https://github.com/openclaw/openclaw/pull/184002), [#183991](https://github.com/openclaw/openclaw/pull/183991), [#183988](https://github.com/openclaw/openclaw/pull/183988).
- **Closed perf PRs:** [#183979](https://github.com/openclaw/openclaw/pull/183979) (redundant PR watcher source checks) and [#183977](https://github.com/openclaw/openclaw/pull/183977) (canonical delivery index rebuilds) were closed; [#183354](https://github.com/openclaw/openclaw/pull/183354) (escape terminal controls in backup reports) also closed.
- **Control UI / identity:** [#164825](https://github.com/openclaw/openclaw/pull/164825) adds profile naming during setup (part of the #162164 identity umbrella); [#163765](https://github.com/openclaw/openclaw/pull/163765) advances personal Tailscale identity in iOS chat.
- **Targeted fixes in flight:** [#183964](https://github.com/openclaw/openclaw/pull/183964) (model setup hangs when Gateway doesn't apply config), [#184005](https://github.com/openclaw/openclaw/pull/184005) (Control UI false "empty response" after silent background exec), [#183023](https://github.com/openclaw/openclaw/pull/183023) (CRLF duplicate replies), [#168510](https://github.com/openclaw/openclaw/pull/168510) (`registerFull` commands without default workspace), [#179781](https://github.com/openclaw/openclaw/pull/179781) (enabled search plugin not started), [#177272](https://github.com/openclaw/openclaw/pull/177272) (Matrix thread session seeding), [#162918](https://github.com/openclaw/openclaw/pull/162918) (PowerShell completion after `--`).
- **Test consolidation:** [#183985](https://github.com/openclaw/openclaw/pull/183985) and [#184006](https://github.com/openclaw/openclaw/pull/184006) remove low-value/duplicated tests — a sign of CI cost management amid the PR surge.
- **Issues closed:** [#170332](https://github.com/openclaw/openclaw/issues/170332) (beta update continuing after live state DB change) and [#83342](https://github.com/openclaw/openclaw/issues/83342) (duplicate `/models` entries under runtime alias) both closed.

**Caveat:** the feed reports "merged/closed" as a combined count, so exact merge ratios cannot be confirmed from this data.

## 4. Community Hot Topics

| Issue/PR | Comments | Signal |
|---|---|---|
| [#143524](https://github.com/openclaw/openclaw/issues/143524) — SQLite WAL grows to 1.4–2.8 GB, blocks gateway startup (Windows) | **136** | Dominant pain point; P0, release-blocker |
| [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway blocks for minutes capturing large external plugins | **54** | 2026.9.6 regression, P0 |
| [#164294](https://github.com/openclaw/openclaw/issues/164294) — State DB WAL checkpoint never completes (pinned by reader) | **33** | Related to WAL family of bugs |
| [#168955](https://github.com/openclaw/openclaw/issues/168955) — Startup ~1030s vs ~410s in 2026.10.1 (Windows) | **19** | Upgrade regression, P1 |
| [#159596](https://github.com/openclaw/openclaw/issues/159596) — Memory sawtooth; prepared-model-catalog worker hits heap ceiling | **16** | ~200 critical memory-pressure events/day |
| [#53408](https://github.com/openclaw/openclaw/issues/53408) — Write/exec parameters silently dropped after long conversations | **15** | Oldest high-engagement bug (March 2026) |
| [#67413](https://github.com/openclaw/openclaw/issues/67413) — Per-agent dreaming configuration | **14** | Feature with real product demand |

**Underlying needs:** the comment distribution is heavily concentrated on **SQLite/WAL lifecycle correctness** (#143524 + #164294 + #166634 + #164427 + #164948 all share checkpoint/locking root causes). Users are not asking for new capability here — they need the gateway to start, stay up, and not corrupt or bloat state on Windows and small hosts. The second cluster is **startup/upgrade reliability** (#160959, #168955, #182476, #183786), indicating the update path itself has become a trust issue. The single long-lived behavioral bug #53408 (silent tool-parameter loss) remains a high-anxiety item because it manifests as silent failure, not a crash.

## 5. Bugs & Stability

Ranked by severity. Fix PRs noted where the data indicates one exists.

### P0 — Critical
1. **[#143524](https://github.com/openclaw/openclaw/issues/143524)** — Agent SQLite WAL grows unbounded (2.8 GB observed), blocks gateway startup on Windows; `wal_checkpoint(TRUNCATE)` only temporary. **No fix PR** (`no-new-fix-pr`), needs maintainer review. Release blocker.
2. **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — Gateway event loop blocks for minutes during external/workspace plugin capture (2026.9.6 regression). Source repro available; **no fix PR**.
3. **[#159596](https://github.com/openclaw/openclaw/issues/159596)** — Memory sawtooth: prepared-model-catalog worker grows to full heap ceiling, ~200 critical pressure events/day. **No fix PR**.
4. **[#153426](https://github.com/openclaw/openclaw/issues/153426)** — `MEMORY.md`/`USER.md` silently and permanently excluded from bootstrap injection by provenance ratchet; no diagnostics or recovery path. Tagged security-review + release-blocker; **no fix PR**.
5. **[#166634](https://github.com/openclaw/openclaw/issues/166634)** — Late reclamation commit checkpoint failure leaves agent permanently `UNAVAILABLE` after crash recovery. Release-blocker; needs repro.

### P1 — High
6. **[#168955](https://github.com/openclaw/openclaw/issues/168955)** — Windows startup regression: ~1030s vs ~410s; `sidecars.control-ui-assets` blocks 477s. No fix PR.
7. **[#182476](https://github.com/openclaw/openclaw/issues/182476)** — Windows `update repair` fails with `OPENCLAW_STATE_LEASE_LOST`; unstable readiness. Needs info.
8. **[#134993](https://github.com/openclaw/openclaw/issues/134993)** — Gateway pegs one CPU core in filesystem discovery after 2026.8.1 with large skill/agent fleet. Needs info.
9. **[#167876](https://github.com/openclaw/openclaw/issues/167876)** — Talk consultations lose caller authority after session setup; security-review tagged.
10. **[#148292](https://github.com/openclaw/openclaw/issues/148292)** — Session auto-compaction: single attempt, no timeout, coupled to primary model → 23-minute hangs.
11. **[#164427](https://github.com/openclaw/openclaw/issues/164427)** — Single `cron.history-maintenance` sweep holds state-DB writer lock 9.35s, exhausting 5s busy timeout and aborting sidecar startup.
12. **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — Native host exec hits shared-state integrity-admission error (28+ failures on 2026.10.5).
13. **[#181078](https://github.com/openclaw/openclaw/issues/181078)** — Agent turns fail post-reply with "Canonical session reader continuation is no longer current" (2026.10.5 Docker).
14. **[#183786](https://github.com/openclaw/openclaw/issues/183786)** — Windows beta upgrade repeatedly queries stopped foreground Gateway PID; upgrade stalls >15 min.
15. **[#168456](https://github.com/openclaw/openclaw/issues/168456)** — Model fallback dies on session-transcript turn-key assertion for cron `agentTurn`. **Fix-shaped/queueable** (`clawsweeper:fix-shape-clear`, `queueable-fix`).
16. **[#126950](https://github.com/openclaw/openclaw/issues/126950)** — Telegram opaque `tgcb1:` callback payloads silently dropped. **Linked PR open.**
17. **[#149406](https://github.com/openclaw/openclaw/issues/149406)** — `sessions_spawn` children omit all third-party MCP servers.
18. **[#142116](https://github.com/openclaw/openclaw/issues/142116)** — Restart recovery wedges Telegram lane: 300s claim→adoption stall, re-dispatch without reply payload.
19. **[#144594](https://github.com/openclaw/openclaw/issues/144594)** — Inter-session messages stuck in `interrupted` with no replay or warning.
20. **[#89257](https://github.com/openclaw/openclaw/issues/89257)** — `backup create --verify` exits 13, leaves corrupt `.tmp` archive (data-loss tag). No fix PR.

### P2 — Notable
- **[#164294](https://github.com/openclaw/openclaw/issues/164294)** — State DB WAL checkpoint never completes (in-process reader pin).
- **[#162585](https://github.com/openclaw/openclaw/issues/162585)** — Windows plugin staging re-materializes self into `node_modules`, growing 290 MB → 612 MB+ without converging.
- **[#157986](https://github.com/openclaw/openclaw/issues/157986)** — All `agentTurn` automations fail with `DataCloneError`. **Linked PR open.**
- **[#158127](https://github.com/openclaw/openclaw/issues/158127)** — Multi-agent runtime publication failures + Codex startup timeouts.
- **[#112313](https://github.com/openclaw/openclaw/issues/112313)** — Dead-lettered outbound queue entries are permanent; no CLI/RPC/TTL can clear them.
- **[#164948](https://github.com/openclaw/openclaw/issues/164948)** — `doctor --fix` self-deadlocks on state DB (`busyTimeoutMs=0`).
- **[#166137](https://github.com/openclaw/openclaw/issues/166137)** — Egress proxy sentinel substitution intermittently ineffective; stale/unsubstituted credentials reach upstream (401s).
- **[#53408](https://github.com/openclaw/openclaw/issues/53408)** — Silent tool-parameter drops after 15+ turns.
- **[#165391](https://github.com/openclaw/openclaw/issues/165391)** — Control UI session count diverges from `sessions.list`.
- **[#166289](https://github.com/openclaw/openclaw/issues/166289)** — `model.usage` double-fires (1 ms apart).
- **[#164279](https://github.com/openclaw/openclaw/issues/164279)** — Background experience review 0% completion (50 consecutive failures).
- **[#138561](https://github.com/openclaw/openclaw/issues/138561)** — Active-memory plugin stops recalling after 2026.8.2 with no log signal.
- **[#162448](https://github.com/openclaw/openclaw/issues/162448)** — Linux desktop window controls overlap sidebar.
- **[#155600](https://github.com/openclaw/openclaw/issues/155600)** — Selection comments render as raw attachment text.

**Stability read:** the bug set is unusually coherent — **SQLite WAL/checkpoint/writer-lock pathology** and **Windows upgrade/startup path** account for most P0/P1 items. Fix coverage is weak: the majority of P0s are tagged `no-new-fix-pr`.

## 6. Feature Requests & Roadmap Signals

Active feature issues, with likelihood signals:

- **[#67413](https://github.com/openclaw/openclaw/issues/67413)** — Per-agent dreaming configuration (14 comments, 5 👍). Prevents simultaneous workspace dreaming/OOM. Tagged `needs-product-decision` → unlikely in the immediate next release.
- **[#101422](https://github.com/openclaw/openclaw/issues/101422)** — Configurable memory recall eligibility and index exclusion paths (10 comments). `needs-product-decision`; strong fit with #153426/#138561 memory work but not scheduled.
- **[#162164](https://github.com/openclaw/openclaw/issues/162164)** — Opt-in personal identity in iOS/macOS while preserving Shared owner. **Actively advancing**: [#164825](https://github.com/openclaw/openclaw/pull/164825) (profile naming) and [#163765](https://github.com/openclaw/openclaw/pull/163765) (Tailscale identity) are open. #163765 carries `merge-risk: compatibility` and `security-boundary` flags — phased rollout likely, not a single drop.
- **[#159858](https://github.com/openclaw/openclaw/issues/159858)** — Let idle worker isolates release memory (~650 MB resident across 11–12 isolates). Aligns with #159596; plausible near-term opt-in.
- **[#55372](https://github.com/openclaw/openclaw/issues/55372)** — Config option to disable restart sentinel notification. Small, `needs-product-decision`, long-open.
- **[#140244](https://github.com/openclaw/openclaw/issues/140244)** — `--prompt-file` for `openclaw infer model run`. Low-risk CLI addition; plausible.
- **[#130247](https://github.com/openclaw/openclaw/issues/130247)** — Cron `--until` / `--max-runs` and cumulative run counter. Simple gap; no scheduling signal.
- **[#126727](https://github.com/openclaw/openclaw/issues/126727)** — Provider-aware concurrency/backpressure for native subagents.
- **[#165685](https://github.com/openclaw/openclaw/issues/165685)** — Machine-readable `reason` on registry-settled yielded collectors.

**Prediction:** the next version is most likely **stability- and performance-led** — WAL/checkpoint fixes, Windows startup/upgrade hardening, and the merged `perf(...)` allocation/CPU batch — rather than new features. The identity work (#162164) is the most likely *feature* to land incrementally, while memory configuration features (#67413, #101422) remain blocked on product decisions.

## 7. User Feedback Summary

**Pain points reported:**
- **Reliability on Windows is the loudest complaint.** Startup times degrading from ~410s to ~1030s (#168955), upgrade stalls (#183786), repair failures with lost leases (#182476), and WAL bloat blocking startup (#143524). Windows hosts appear to be the weakest platform.
- **Silent failure is feared more than crashes.** #53408 (tool params vanish), #153426 (memory files silently dropped forever), #112313 (permanent dead-letter rows), #144594 (messages stuck with no warning), #166137 (stale credentials). Users repeatedly emphasize "no diagnostics, no signal at info log level."
- **Memory pressure on small/self-hosted hardware.** #159596 (~200 critical events/day), #159858 (~650 MB idle worker heap), #164294 and #164427 (WAL/writer-lock contention). Users are running OpenClaw on constrained machines and hitting ceiling effects.
- **Regressions across 2026.9.x → 2026.10.x.** #160959, #181078, #168955, #159596, #158127, #162448 all explicitly labeled regressions; users are tracking exact build hashes and A/B baseline timings, indicating a sophisticated, production-usage audience.
- **Positive signals:** high-quality reproductions (`source-repro` tags), detailed environment reports, and users contributing targeted fix PRs (#162918, #166352, #181534, #177272). The community is cooperating on diagnosis rather than churning.

**Satisfaction read:** engagement is high and technical, but patience with upgrade-path regressions is visibly thinning — several reports span weeks (#143524 since 2026-09-09 with 136 comments) without a fix PR.

## 8. Backlog Watch

Important items with low fix momentum or long age needing maintainer attention:

1. **[#53408](https://github.com/openclaw/openclaw/issues/53408)** (opened 2026-03-24, 15 comments, 2 👍) — Silent `write`/`exec` parameter loss after long conversations. Seven months old, `needs-info`, no fix PR. High user impact due to silent data/action failure.
2. **[#89257](https://github.com/openclaw/openclaw/issues/89257)** (2026-06-01, P1, data-loss) — `backup create --verify` leaves corrupt archive. `source-repro` available yet no fix PR; backup integrity is a trust-critical path.
3. **[#51245](https://github.com/openclaw/openclaw/issues/51245)** (2026-03-20, security) — Telegram slash sessions resolve to `channel=(unknown)`, breaking elevated `allowFrom`. Linked PR open but unresolved since March.
4. **[#55372](https://github.com/openclaw/openclaw/issues/55372)** (2026-03-26) — Disable restart sentinel notifications. Small surface, `needs-product-decision`.
5. **[#67413](https://github.com/openclaw/openclaw/issues/67413)** (2026-04-15) — Per-agent dreaming config; 5 👍, `needs-product-decision`.
6. **[#101422](https://github.com/openclaw/openclaw/issues/101422)** (2026-07-07) — Memory recall eligibility/exclusion paths; `needs-maintainer-review` + `needs-product-decision`.
7. **[#130247](https://github.com/openclaw/openclaw/issues/130247)** and **[#126727](https://github.com/openclaw/openclaw/issues/126727)** (August) — Cron end conditions and provider backpressure; both `needs-product-decision`.
8. **[#143524](https://github.com/openclaw/openclaw/issues/143524)** — Despite 136 comments, remains `no-new-fix-pr` with `needs-maintainer-review`. **This is the single highest-priority backlog risk in the dataset.**

**Maintainer load signal:** `clawsweeper:needs-maintainer-review` appears on the majority of the top-50 issues, and `needs-product-decision` recurs across feature items — review bandwidth, not issue discovery, appears to be the current bottleneck.

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — Personal AI Assistant / Agent OSS Ecosystem
**Window:** 2026-10-26 · **Projects:** OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw  
**Data note:** Counts are 24-hour GitHub update metrics from the supplied digests, not absolute community size. Health scores are analyst judgments on a 1–10 scale, weighted toward merge throughput, release readiness, open critical defects, and review load.

---

## 1. Ecosystem Overview

The personal AI assistant / agent OSS landscape is bifurcating into large general-purpose gateway platforms and smaller specialized runtimes. All five projects shipped **zero releases** in the window, and activity is dominated by stabilization, security hardening, and post-merge fallout rather than feature delivery. The common competitive frontier has shifted from “new agent capability” to **operational trust**: startup/update reliability, state-store integrity, silent-failure prevention, multi-tenant isolation, and local-model routing correctness. OpenClaw remains the largest activity center, while Hermes, ZeroClaw, QwenPaw, and IronClaw occupy narrower but increasingly production-shaped niches.

---

## 2. Activity Comparison

| Project | Issues updated (24h) | PRs updated (24h) | Release status | Health score |
|---|---:|---:|---|---:|
| **OpenClaw** | **358** updated; 72 closed | **500** updated; 270 merged/closed; 230 open | None; release blockers accumulating | **6.0/10** — high throughput, under strain |
| **Hermes Agent** | **50**; 48 open / 2 closed | **50**; 40 open / 10 merged-closed | None; next cut likely stability-focused | **6.5/10** — stabilizing, security cluster unresolved |
| **IronClaw** | **0** | **7**; 5 open / 2 closed / 0 merged | None; maintenance-only window | **6.5/10** — stable but low human velocity |
| **QwenPaw** | **6**; 5 open / 1 closed | **6**; 6 open / 0 merged | None; 2.2.2.post1 baseline in reports | **6.0/10** — responsive triage, review-constrained |
| **ZeroClaw** | **6**; 1 closed | **50**; 49 open / 1 merged-closed | None; v0.9.0 scope being assembled | **5.5/10** — high churn, low merge throughput |

**Readout:** OpenClaw is an order of magnitude more active than peers on both issue and PR updates. Hermes is the second-largest active surface and is in a deliberate post-merge stabilization window. ZeroClaw has high PR churn but very low merge throughput. QwenPaw shows responsive triage but a stalled review queue. IronClaw is automation-driven and largely silent on user-facing activity.

---

## 3. OpenClaw’s Position

**Advantages vs peers**
- **Largest activity and merge throughput by far:** 500 PR updates and 270 merged/closed in 24h, versus Hermes 50/10, ZeroClaw 50/1, QwenPaw 6/0, IronClaw 7/0.
- **Broadest platform surface:** gateway, SQLite state, plugin capture, Control UI, identity, memory, iOS/Tailscale, Windows startup/upgrade, and a coordinated performance campaign.
- **Strongest community-scale signal:** the SQLite WAL issue alone has 136 comments; the project shows high-quality reproductions, source-repro tags, and contributor fix PRs.
- **Core-reference position:** peers are already back-porting fixes from OpenClaw and sibling projects, reinforcing it as a reference implementation rather than a niche tool.

**Technical approach differences**
- OpenClaw is a **gateway-centric general-purpose platform** with a heavy SQLite/WAL state layer, plugin capture, Control UI, identity, and per-agent memory/dreaming configuration.
- Hermes is closer to a **unified local gateway + desktop/webapp + profile multiplexer**.
- ZeroClaw emphasizes **multi-channel deployment, SOP-scoped automation, and channel plugins**.
- QwenPaw emphasizes **tool-layer governance, audit logging, and provider compatibility**.
- IronClaw is a **Rust/WASM sandbox and CI-agent automation** stack.

**Community size comparison**
- By activity proxies, OpenClaw is the largest community: ~7× Hermes on issue updates and ~10× on PR updates.
- Hermes has the highest single-thread engagement in the dataset — 220 comments on its gateway tracker — indicating a high-trust, production-heavy user base.
- ZeroClaw shows sustained contributor interest but review latency.
- QwenPaw has a smaller, highly technical operator community that reads source before filing.
- IronClaw’s GitHub tracker is effectively silent, so community size cannot be inferred from issue activity.

**Caveat:** OpenClaw’s scale is also its main risk. Multiple P0 issues — SQLite WAL growth, plugin capture blocking, memory sawtooth, silent memory-file exclusion — remain without fix PRs, and Windows startup/upgrade regressions are recurring. It leads on breadth and throughput, not necessarily on release quality.

---

## 4. Shared Technical Focus Areas

| Focus area | Projects showing demand | Specific needs |
|---|---|---|
| **Gateway/startup/update reliability** | OpenClaw, Hermes, ZeroClaw | OpenClaw: WAL/checkpoint blocking startup, Windows upgrade stalls. Hermes: unified-gateway post-merge breakage, `hermes update` hazards, Windows first-run failure. ZeroClaw: fail-open runtime config and release-gate PRs. |
| **Silent failure and observability** | OpenClaw, Hermes, QwenPaw, ZeroClaw | OpenClaw: silently dropped tool params, memory files excluded without diagnostics. Hermes: local model silently routed to OpenRouter, cross-profile delivery. QwenPaw: audit logging stops silently; lossy file encoding reports success. ZeroClaw: unknown memory backend silently falls back; `knowledge_bundles` ignored. |
| **State/data integrity** | OpenClaw, Hermes, QwenPaw, ZeroClaw | SQLite WAL/checkpoint and backup verification; large `state.db` performance; audit DB durability; CRLF/encoding preservation; memory backend correctness. |
| **Multi-tenancy and security boundaries** | Hermes, ZeroClaw, QwenPaw, OpenClaw | Hermes: cross-profile bot leakage, cross-install service restarts, `.env` managed-dir override. ZeroClaw: scoped log access, cron output leakage, SOP scope/tool-name mismatch. QwenPaw: compliance audit integrity. OpenClaw: security-review-tagged gateway and identity issues. |
| **Local/private model routing correctness** | Hermes, QwenPaw, OpenClaw | Hermes: custom provider silently falls through to OpenRouter; alias-based silent misrouting. QwenPaw: provider rejects `thinking` parameters and breaks `/compact`. OpenClaw: model setup/fallback and session-transcript assertions. |
| **Platform parity and onboarding** | OpenClaw, Hermes, QwenPaw | OpenClaw: Windows WAL, startup, upgrade, plugin staging. Hermes: Windows first-run install, Linux desktop opacity, frontend-only install request. QwenPaw: Windows 10 file fidelity and desktop approval lifecycle. |
| **Performance at scale** | OpenClaw, Hermes, ZeroClaw, QwenPaw | OpenClaw: memory sawtooth, plugin capture blocking, allocation/CPU perf campaign. Hermes: session-list rescans on 4,583 sessions / 5.4 GB state. ZeroClaw: JPEG decode accounting. QwenPaw: task-tracker cleanup. |
| **Plugin/channel isolation and supply chain** | OpenClaw, Hermes, QwenPaw, ZeroClaw, IronClaw | Subprocess/WASM isolation, approval-card injection prevention, image-URL provenance, dependency audits, fail-closed skill scans, plugin staging cleanup. |

**Cross-cutting pattern:** the ecosystem is converging on **fail-loud, fail-closed, tenant-isolated, provenance-preserving** agent infrastructure. Silent degradation is now treated as a more serious defect than a crash.

---

## 5. Differentiation Analysis

| Project | Feature focus | Target users | Architecture / technical posture |
|---|---|---|---|
| **OpenClaw** | General-purpose agent gateway, Control UI, identity, memory, plugins, Windows support, performance | Self-hosted power users, production operators, broad platform integrators | Monolithic gateway + SQLite state + plugin capture + UI/identity layers; broadest but highest release-risk surface |
| **Hermes Agent** | Unified gateway, desktop/webapp, profiles, multi-bot channels, security hardening, local-model routing | Multi-profile / multi-channel production users, remote-agent deployments, privacy-sensitive local-model users | Consolidated local gateway, profile multiplexer, browser webapp, active back-porting from sibling projects |
| **IronClaw** | Rust/WASM sandbox, async runtime, CI agent automation, dependency hygiene | Infrastructure teams needing sandboxed tool execution | Rust-centric, WASM runtime dependency stack, automation-heavy maintenance; low community visibility |
| **QwenPaw** | Tool governance, audit/compliance, Desktop/Feishu channels, provider compatibility, channel plugins | Compliance-sensitive operators, real-repo users, third-party model users | AgentScope-based tool layer with guards, audit DB, channel-plugin architecture, provider compat shims |
| **ZeroClaw** | Multi-channel assistant deployment, SOP-scoped automation, memory parity, channel maturity | Multi-channel operations teams, plugin/SOP-driven deployments | Channel plugins, memory backend abstraction, capability-taking runtime constructors, stacked v0.9.0 PRs |

**Key distinction:** OpenClaw and Hermes compete on **breadth of gateway integration**; ZeroClaw and QwenPaw compete on **channel/operator workflows and governance**; IronClaw competes on **sandboxed runtime infrastructure**. OpenClaw’s advantage is ecosystem surface; its risk is that the same breadth multiplies release-blocking regressions.

---

## 6. Community Momentum & Maturity

**Activity tiers**
- **Tier 1 — high-throughput platform:** OpenClaw. Very high issue/PR volume and merge throughput, but P0 stability and release blockers.
- **Tier 2 — active stabilization:** Hermes. Large production-shaped feedback, active patch pipeline, but a security-flavored cluster with no fix PRs.
- **Tier 3 — high churn, low merge:** ZeroClaw. 50 PR updates but 1 merged/closed; stacked PR chains and review bandwidth are binding constraints.
- **Tier 4 — responsive triage, review-limited:** QwenPaw. Same-day bug closure and fix PRs, but multiple open PRs with no maintainer comments and critical data-integrity defects.
- **Tier 5 — automation-only maintenance:** IronClaw. Dependency hygiene is active; human issue/feature activity is effectively absent.

**Rapidly iterating**
- OpenClaw: performance campaign plus Windows/gateway reliability work.
- Hermes: unified gateway and Webapp hardening, security fixes, cross-project ports.
- ZeroClaw: v0.9.0-tagged runtime, channel, and security PRs.
- QwenPaw: `/steer`, DeepSeek Search, channel-plugin isolation.

**Stabilizing**
- OpenClaw and Hermes are both absorbing large architectural changes and are in stabilization rather than feature-expansion mode.
- QwenPaw is stabilizing correctness around file encoding, audit logging, and provider compatibility.
- ZeroClaw is stabilizing channel behavior and memory semantics but is process-constrained.
- IronClaw is stable but low-velocity.

**Maturity signal:** Hermes’s explicit porting of fixes from OpenClaw/nanoclaw/opencode, OpenClaw’s performance campaign, QwenPaw’s source-level bug reports, and ZeroClaw’s release-gate labels all indicate a maturing ecosystem. The main systemic bottleneck is **review bandwidth**, not issue discovery.

---

## 7. Trend Signals

1. **Reliability is the new feature.** All major projects are judged on startup, upgrade, and state integrity before new capabilities. Developers should invest in migration/rollback paths, state-store health checks, and Windows parity.

2. **Silent failure is unacceptable.** Across OpenClaw, Hermes, QwenPaw, and ZeroClaw, the most damaging reports involve silent drops, silent rerouting, silent audit loss, or false success messages. Fail-closed behavior and loud diagnostics are becoming baseline requirements.

3. **Multi-tenancy is arriving faster than isolation design.** Hermes profiles, ZeroClaw scoped principals, and QwenPaw audit boundaries show users running these systems as shared infrastructure. Per-profile, per-install, and per-principal isolation must be designed explicitly.

4. **Local/private model routing correctness is a trust issue.** Hermes’s custom-provider fallthrough and QwenPaw’s provider-parameter failures show that “OpenAI-compatible” is insufficient. Agent developers need provider identity, capability negotiation, and no silent cloud fallback.

5. **Memory is becoming a control plane, not a store.** OpenClaw dreaming/recall/exclusion, ZeroClaw memory parity and external rerank, and Hermes doc-chain provenance all point to demand for curation, relevance ranking, and operability.

6. **Performance at scale is emerging as a differentiator.** Large state databases, session-list rescans, plugin capture blocking, and memory ceilings are now common production reports. Lightweight self-hosted deployments still need bounded memory and predictable CPU.

7. **Plugin/channel isolation and supply-chain security are hardening.** Subprocess channel isolation, WASM runtimes, approval-card injection prevention, dependency audits, and fail-closed skill scans are all active workstreams.

8. **Ecosystem convergence is real.** Hermes is back-porting fixes from OpenClaw and sibling projects. Developers should design portable primitives — state integrity, diagnostics, tenant isolation, routing provenance — rather than project-specific patches.

**Bottom line for technical decision-makers:** OpenClaw is the ecosystem’s activity and surface-area leader but carries the highest release-quality risk. Hermes is the strongest second platform and is stabilizing a large gateway/webapp consolidation. ZeroClaw and QwenPaw are focused operator tools with engaged users but review-constrained queues. IronClaw is a stable infrastructure niche with low community visibility. Across all five, the winning roadmap is **operational trust first, features second**.

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest
**Date:** 2026-10-26 · **Repo:** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. Today's Overview

Activity remains very high — 50 issues and 50 PRs touched in the last 24 hours — but the shape of that activity is **sustained stabilization, not feature delivery**: zero releases, 48 of 50 updated issues still open, and the two highest-engagement threads are both tracking issues for post-merge fallout. The dominant story is still the **unified gateway** ([#106742](https://github.com/NousResearch/hermes-agent/pull/106742), merged Oct 14), whose original P0 tracker [#134889](https://github.com/NousResearch/hermes-agent/issues/134889) finally closed at 220 comments and was immediately continued by [#143547](https://github.com/NousResearch/hermes-agent/issues/143547) (23 comments and climbing). A **second large surface landed on Oct 25** — the browser-hosted Desktop "Webapp" ([#141864](https://github.com/NousResearch/hermes-agent/pull/141864)) — which already has its own P2 tracking issue ([#144648](https://github.com/NousResearch/hermes-agent/issues/144648)) and a cluster of follow-up PRs. Health signal: the project is absorbing two architectural changes at once with a healthy patch pipeline, but PR intake is outpacing merges roughly 4:1 over 24h (40 open vs. 10 closed), and a meaningful slice of the open PR queue is 3–10 weeks old.

| Metric | Value | Read |
|---|---|---|
| Issues updated (24h) | 50 (48 open / 2 closed) | High, mostly unresolved |
| PRs updated (24h) | 50 (40 open / 10 merged-closed) | High intake, backlog-building |
| Releases | 0 | Post-merge stabilization window |
| Dominant component tags | `comp/gateway`, `comp/desktop`, `area/profiles`, `area/sessions` | Consolidation pain |
| Security-flagged items today | 8+ issues, 4+ PRs | Active hardening |

---

## 2. Releases

**None.** No new releases were published on 2026-10-26, and no draft notes are present in the snapshot.

Context for readers: the codebase is running ahead of the last tag (`v0.22.5` is the version cited in several bug reports), with the two large merges of Oct 14 and Oct 25 not yet cut into a release. Given the volume of P2 fixes in flight, the next cut is likely to be predominantly a **stability release** (see §6).

---

## 3. Project Progress

> **Data caveat:** the snapshot reports 10 PRs merged/closed in 24h but only itemizes **open** PRs (all 20 listed are `[OPEN]`, and their comment counts are unavailable — `Comments: undefined`). The merged set therefore cannot be attributed item-by-item; the analysis below covers what is *advancing in the pipeline*.

**Post-merge follow-up work is converging into shippable fixes.**

*Webapp (#141864) follow-through* — [#144741](https://github.com/NousResearch/hermes-agent/pull/144741) closes four "WA-5" gaps from #144704 lane 2: workspace files and media now resolve against the session's own Docker sandbox or SSH workspace, and a file saves where its preview read it; it builds on SSH-down handling from #144661. [#144708](https://github.com/NousResearch/hermes-agent/pull/144708) fixes `/handoff` chats re-opening from Desktop and the Webapp across profile types. Two related defects were filed rather than fixed: broken sign-out propagation ([#144668](https://github.com/NousResearch/hermes-agent/issues/144668)) and silent popup-blocker failures ([#144673](https://github.com/NousResearch/hermes-agent/issues/144673)).

*Performance and startup* — [#144640](https://github.com/NousResearch/hermes-agent/pull/144640) removes the git version walk from provider-plugin loading and the restart check, cutting `hermes sessions list` from 1.45 s → 0.44 s (15 git subprocesses → 2). [#144630](https://github.com/NousResearch/hermes-agent/pull/144630) makes `sessions optimize-storage` 18× faster (24 m 39 s → 1 m 25 s) with per-phase progress.

*Agent/tool robustness* — [#144745](https://github.com/NousResearch/hermes-agent/pull/144745) caps degenerate whitespace streaming in tool calls at 4 KiB with fast retry; [#123900](https://github.com/NousResearch/hermes-agent/pull/123900) reconnects streaming tool calls on repetition loops, targeting the observed 7–10 minute "writing a file…" freezes.

*Security hardening* — [#144738](https://github.com/NousResearch/hermes-agent/pull/144738) prevents an agent command from closing an approval card's code fence and injecting card text; [#130023](https://github.com/NousResearch/hermes-agent/pull/130023) restricts reply-image fetching to tool-provenance URLs (closes an exfiltration channel); [#86189](https://github.com/NousResearch/hermes-agent/pull/86189) makes background skill-write scans fail-closed.

*Cross-course parity* — a notable share of today's PRs are explicitly **ports** from sibling projects (openclaw, nanoclaw, opencode), e.g. #144745, #110440, #144740, #144739, #144738, #131039, #125848. This is a maturity signal: fixes discovered elsewhere are being back-propagated rather than rediscovered.

---

## 4. Community Hot Topics

| Rank | Thread | Engagement | Underlying need |
|---|---|---|---|
| 1 | [#134889](https://github.com/NousResearch/hermes-agent/issues/134889) **[CLOSED]** — Tracking: one gateway owns every local session (P0) | 220 comments, 18 days open | A single incident thread became the de-facto **release-notes channel** for the gateway migration; users needed a place to confirm "is my breakage known?" |
| 2 | [#143547](https://github.com/NousResearch/hermes-agent/issues/143547) — Unified gateway open post-merge problems (P2) | 23 comments | Continuation thread; signals the P0 tracker closed **administratively, not because problems ended** |
| 3 | [#143379](https://github.com/NousResearch/hermes-agent/issues/143379) — RFC: doc-chain substrate network (read/governance/compaction) | 20 comments | Design appetite for durable, provenance-preserving memory that survives context compression |
| 4 | [#38519](https://github.com/NousResearch/hermes-agent/issues/38519) — Desktop **frontend-only install** (Windows) | 19 comments, **31 👍** (top reaction) | Remote-agent topology: users run the agent on a server and want a thin local client — no duplicate install |
| 5 | [#143426](https://github.com/NousResearch/hermes-agent/issues/143426) — Linux desktop launches at **6% opacity**, setting hidden | 17 comments, 11 👍 | Desktop visual regression entirely blocking usability on GNOME/Wayland; control not discoverable |

**Analysis.** The comment distribution is unusually top-heavy — one thread (220) outnumbered the next nineteen combined. That pattern is characteristic of a *trust event*: when a P0 architectural change breaks many workflows, users converge on a single thread because fragmentation feels risky. The #134889 → #143547 handoff is the right structural answer, but the fact that #143547 is tagged `P2` while carrying `area/auth`, `os/windows`, and `desktop/connection` suggests the successor thread is under-prioritized for its blast radius.

The #38519 reaction count (31 👍, the highest in the dataset) is the clearest single unmet product request and has been open since **June 3** — see §8.

---

## 5. Bugs & Stability

Ranked by severity, weighing `P` level, blast radius, and whether a fix is inbound. All open unless noted.

### Critical / highest impact

| Item | Severity | Detail | Fix PR? |
|---|---|---|---|
| [#143547](https://github.com/NousResearch/hermes-agent/issues/143547) Unified gateway post-merge problems | **P2, broad** | Cross-cutting: gateway, cron, auth, install-update, profiles, Windows, TUI-gateway, desktop connection. Effects persist after the P0 tracker closed | Partial — fixes landing piecemeal (#144708, #144741) |
| [#144437](https://github.com/NousResearch/hermes-agent/issues/144437) Bare `custom` provider not healed → **falls through to OpenRouter and 400s** | **P2** | A session persisting bare `custom` identity silently routes a LAN/local model to the OpenRouter default endpoint. Beyond breakage, this is a **potential data-egress path for prompts intended for a local model** | None filed |
| [#143364](https://github.com/NousResearch/hermes-agent/issues/143364) Secondary profile's progress/notices delivered via **primary bot** | P3 but security-tagged | Cross-profile message leakage on a 4-bot multiplexed gateway — wrong tenant sees another profile's stream | None filed |
| [#135200](https://github.com/NousResearch/hermes-agent/issues/135200) User `.env` can set `HERMES_MANAGED_DIR` and repoint managed scope | P3, `risk-security-boundary` | Documented as administrator-only and "never persisted to `.env`" — enforcement gap | None filed |
| [#144547](https://github.com/NousResearch/hermes-agent/issues/144547) Lifecycle guard blocks `gateway restart` but **not `hermes update`** | P3, security | `hermes update` drains, SIGKILLs and re-codes the running gateway — unguarded from inside a supervised session | None filed |

### High

- **[#143833](https://github.com/NousResearch/hermes-agent/issues/143833) — P2 perf regression.** Session list/count rescans every lineage receipt per request (~130 ms each) via `exclude_superseded_segments` → `hermes_state_local.local_lineage_index`. Observed 3 CPU cores saturated on 4,583 sessions / 5.4 GB `state.db` with 5 clients. Scales badly with real usage. *No fix PR.*
- **[#127105](https://github.com/NousResearch/hermes-agent/issues/127105) — P2 audit regression.** Background process notifications can be delivered as user messages to Discord; the earlier blanket-suppression proposal (#127338) was closed unmerged, so the fix needs a narrower design. *No fix PR.*
- **[#144117](https://github.com/NousResearch/hermes-agent/issues/144117) — P2.** Docker-backend background process reported exited (`-1`) while still running; completion state is lost. *No fix PR.*
- **[#121973](https://github.com/NousResearch/hermes-agent/issues/121973) — P2.** Title generation 400s every time on opencode-go `deepseek-v4.1-flash`: relay rejects `json_schema` but returns an opaque body, so `_is_structured_output_rejection` never matches. *No fix PR.*
- **[#131199](https://github.com/NousResearch/hermes-agent/issues/131199) — P2, `needs-repro`, Windows.** First-run native Windows install dies at Python-dependency stage ("INSTALL DIDN'T FINISH"). Blocks new users entirely on that platform. *No fix PR.*
- **[#144336](https://github.com/NousResearch/hermes-agent/issues/144336) — P2.** `hermes -p <profile> gateway restart` skips plugin discovery, so a newly placed user platform-plugin override silently doesn't apply. *No fix PR.*
- **[#140881](https://github.com/NousResearch/hermes-agent/issues/140881) — P2.** `/model <id> --provider <P>` can silently run on a **different** provider when a `model_aliases` entry exists elsewhere. Silent misrouting, no error surfaced. *No fix PR (related identity work in [#85861](https://github.com/NousResearch/hermes-agent/pull/85861)).*
- **[#141275](https://github.com/NousResearch/hermes-agent/issues/141275) — P2.** `hermes update` on a non-primary install restarts the **first install's** production dashboard (hardcoded account-global `hermes-dashboard.service`). Multi-install hosts get cross-tenant disruption. *No fix PR.*

### Medium / polish

- [#143426](https://github.com/NousResearch/hermes-agent/issues/143426) — Linux Desktop opens at 6% opacity under XWayland/GNOME 50; the Settings control is hidden. 11 👍. *No fix PR.*
- [#144728](https://github.com/NousResearch/hermes-agent/issues/144728) — `session.resume` on a live, not-yet-persisted session omits `running`/`status`; Conduit locks the composer after reconnect.
- [#144716](https://github.com/NousResearch/hermes-agent/issues/144716) — Bot Mode `profile_mismatch` toast + PATCH retry loop on multi-profile gateways.
- [#144694](https://github.com/NousResearch/hermes-agent/issues/144694) — `no_agent` cron jobs to bot-chat stay `delivery_queued` forever (settle path reads a ledger never written for this class).
- [#144703](https://github.com/NousResearch/hermes-agent/issues/144703) — `profile delete` warns "Gateway is running — it will be stopped," then stops nothing (no per-profile `gateway.pid` under multiplexer).
- [#144711](https://github.com/NousResearch/hermes-agent/issues/144711) — `profile list` warns about shared push credential, but the adapter actually works per profile (false positive).
- [#135200](https://github.com/NousResearch/hermes-agent/issues/135200), [#144735](https://github.com/NousResearch/hermes-agent/issues/144735), [#144668](https://github.com/NousResearch/hermes-agent/issues/144668) — see table above.
- [#144736](https://github.com/NousResearch/hermes-agent/issues/144736) — Desktop URL parser swallows trailing full-width CJK punctuation into the href (i18n correctness).
- [#144675](https://github.com/NousResearch/hermes-agent/issues/144675) — Bot Mode hide-toast points at a nonexistent "eye button."
- [#138131](https://github.com/NousResearch/hermes-agent/issues/138131) — `hermes update` still applies `exclude-newer` on user machines; the `_trust_core_lock` fix didn't cover this path.

**Assessment.** No crashes or data-loss reports today, and the P0 tracker closed — a genuine improvement. But the **security-flavored cluster is the most concerning signal**: four separate issues involve a trust boundary being crossed (cross-profile message delivery, cross-install service restart, unguarded self-update, `.env` overriding admin scope). None has an associated fix PR. The `sweeper:risk-*` labels (session-state, message-delivery, compatibility, security-boundary, platform-windows) show automated triage is catching these; the gap is remediation throughput.

---

## 6. Feature Requests & Roadmap Signals

| Request | Signal | Likelihood in next version |
|---|---|---|
| [#38519](https://github.com/NousResearch/hermes-agent/issues/38519) Frontend-only Desktop install (Windows) | **31 👍**, 19 comments, open since Jun 3, `needs-decision` | **Unlikely for the next cut** — `needs-decision` for ~5 months implies an architectural blocker; but the Webapp work (#141864) partly *solves the need* (browser client to a remote gateway) and may defuse it |
| [#144574](https://github.com/NousResearch/hermes-agent/pull/144574) Guide plugin installs through companion-app prerequisites | PR open | **Likely** — small UX surface, active author, fills a dead-end in the Desktop installer |
| [#143346](https://github.com/NousResearch/hermes-agent/issues/143346) Kanban dispatcher: report cap-hold reason in stuck warning | P3 | **Possible** — low-risk logging improvement with clear operator value |
| [#143379](https://github.com/NousResearch/hermes-agent/issues/143379) RFC: doc-chain substrate (durable provenance across compression) | 20 comments, `needs-decision` | **No** — RFC-stage; would land as a follow-on release, not the next patch |

**Prediction for the next release.** Expect a `v0.22.x`-line stability cut whose headline is *"unified gateway + Webapp hardening,"* built from: #144640 (startup), #144630 (storage), #144745 / #123900 (streaming hangs), #144741 / #144708 (Webapp files & handoff), #144738 / #130023 (approval-card and image-URL security), #144739 / #144740 (Google Chat, code-fence splitting). Feature content should be near-zero. Windows first-run (#131199) and the Linux opacity regression (#143426) are the most likely candidates to *miss* the cut, since neither has a fix PR.

---

## 7. User Feedback Summary

**Where users are happy.** Nothing in this dataset is a compliment, but the *shape* of the reports indicates real production adoption: users cite specific version stamps (`v0.22.5+9-g67491b50`), packaged builds, 5.4 GB state databases, 4,583 sessions, 5 concurrent clients, four Telegram bots plus Discord under one multiplexer. People are running this hard.

**Principal pain points, in the users' own framing:**

1. **The unifier broke the seams between surfaces.** The #134889/#143547 threads describe CLI, TUI, Desktop, API server, editors, messaging bots, cron, Kanban and Group Chats all handing turns to one process — and then losing per-surface guarantees. Concrete manifestations: `/handoff` chats resurrecting in the wrong client (#144708), live sessions missing `status` after reconnect (#144728), Bot Mode `profile_mismatch` loops (#144716), `profile delete` doing nothing (#144703), plugin overrides silently ignored (#144336). The recurring word is **"silently."**
2. **Multi-tenancy is being used in ways the design didn't anticipate.** A profile's progress notices leaking to another profile's bot (#143364), and one install's `update` killing another install's dashboard (#141275). Users are treating Hermes as shared infrastructure.
3. **Local-model users feel second-class.** #144437 (LAN model silently pushed to OpenRouter) and #140881 (alias rewrites model id, falls back silently) are the same complaint: *the router quietly chooses a different backend than I asked for, and I only find out from a 400.* For privacy-sensitive local deployments this is the sharpest trust issue in the dataset.
4. **Onboarding friction is platform-skewed.** Windows first-run installer failure (#131199) and the 145-day-old frontend-only request (#38519); Linux Desktop unusable at 6% opacity (#143426). Meanwhile macOS users report subtler staleness (#144735).
5. **Scale performance is arriving.** #143833's 130 ms-per-call session rescan on a 4,583-session store is the first report of its kind here — expect more as the user base matures.

**Satisfaction read.** Neutral-to-strained. No rage, no abandonment signals; users are filing careful, reproducible, pre-flight-checked reports (several explicitly list the searches they ran first). That is the behavior of an invested user base being asked to absorb a lot of churn at once.

---

## 8. Backlog Watch

Items needing maintainer attention — ranked by (age × impact × absence of owner).

| Item | Age | Why it matters |
|---|---|---|
| [#38519](https://github.com/NousResearch/hermes-agent/issues/38519) Frontend-only install | **~145 days** (Jun 3) | Highest reaction count in the dataset (31 👍) and tagged `needs-decision`. Even a *"won't do, use Webapp instead"* answer would unblock users. Currently silent. |
| [#85861](https://github.com/NousResearch/hermes-agent/pull/85861) Disambiguate custom provider identity by model before URL | **~73 days** (Aug 14) | Directly overlaps today's P2 #144437 and #140881 — the *same* silent-misrouting bug class. A stalled PR is holding the fix for a bug still being reported fresh. **Highest-leverage merge candidate in the queue.** |
| [#86189](https://github.com/NousResearch/hermes-agent/pull/86189) Fail-closed background skill-write scans | **~73 days** (Aug 14) | Security PR, tagged `needs-repro` + `risk-security-boundary`. Two months for a fail-closed hardening change is long. |
| [#110440](https://github.com/NousResearch/hermes-agent/pull/110440) Linux gateway detached-start when systemd unreachable | **~42 days** (Sep 14) | Affects setup success on a whole platform class; consequence today is an aborted setup with D-Bus remediation text. |
| [#127784](https://github.com/NousResearch/hermes-agent/pull/127784) One sandbox target for every env creator | **~27 days** (Sep 29) | Per-session Docker isolation correctness (raw host cwd passed to `docker run -w`). Security-adjacent, P2. |
| [#125848](https://github.com/NousResearch/hermes-agent/pull/125848) Egress setup drops unmatchable `extra_allowed_hosts` | **~28 days** (Sep 28) | Turns a silent 403 (no diagnostic) into a named warning. Cheap, user-visible win, `ci-reviewed`. |
| [#130023](https://github.com/NousResearch/hermes-agent/pull/130023) Only fetch reply image URLs with tool provenance | **~25 days** (Oct 1) | Closes a data-exfiltration channel. Security PRs shouldn't sit a month. |
| [#135200](https://github.com/NousResearch/hermes-agent/issues/135200) `.env` can repoint `HERMES_MANAGED_DIR` | **~18 days** (Oct 8) | Documented-vs-enforced mismatch on a managed-scope security boundary; no owner. |
| [#134889](https://github.com/NousResearch/hermes-agent/issues/134889) — *closed but worth watching* | Closed 10/25 | 220 comments of hard-won institutional knowledge. Confirm the unresolved sub-items were **migrated into #143547**, not dropped, before the thread goes cold. |

**Maintainer-facing recommendation.** The queue's problem isn't volume, it's *coherence*: three separate items (#85861, #140881, #144437) are the same provider-identity bug, and a fourth (#143547) is a bucket rather than a bug. Closing the loop on the provider-identity cluster — merge #85861, then close #140881/#144437 against it — would remove the sharpest trust issue in today's data with one review.

---

**Data notes & limitations.** Comment counts for PRs are unavailable in the source snapshot (`undefined`); the 10 merged/closed PRs are counted but not itemized; issue/PR bodies are truncated, so summaries above derive from the supplied excerpts. Severity rankings reflect the project's own `P0`–`P3` labels weighted by my assessment of blast radius, not maintainer triage.

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw Project Digest — 2026-10-26

*Repository: [nearai/ironclaw](https://github.com/nearai/ironclaw) · Reporting window: last 24 hours*

---

## 1. Today's Overview

IronClaw recorded a **maintenance-only day**: zero issues touched, zero releases, and all 7 updated pull requests are Dependabot dependency bumps — no feature, bug-fix, or documentation PRs appeared in the window. Of the 7 PRs, **2 were closed and 0 merged**, leaving 5 open, all of them automated dependency updates across Rust, GitHub Actions, and WASM toolchains.

Activity volume is moderate (7 PR updates) but **entirely non-product-facing**, so the day's work does not advance any user-visible capability. The closed PRs (#8142, #8133) appear to have been superseded by freshly opened, broader-scope replacements (#8160, #8159) on 2026-10-25 — a healthy but routine Dependabot re-roll pattern.

Health signal is mixed: dependency hygiene is being actively maintained, but the **complete absence of issue-tracker activity** and a **64-day-old open PR (#7834)** point to merge latency and a possible gap in user-facing engagement channels. Overall assessment: **stable, low-velocity, automation-driven maintenance**.

---

## 2. Releases

**No new releases in the reporting window.** The project remains at its last published version; there are no changelogs, breaking changes, or migration notes to report today.

---

## 3. Project Progress

No features advanced and no fixes shipped today — all closed items were dependency PRs closed without merge.

| PR | Status | Scope | Note |
|---|---|---|---|
| [#8142](https://github.com/nearai/ironclaw/pull/8142) | **CLOSED** | Rust "everything-else" group, 41 updates | Superseded by [#8160](https://github.com/nearai/ironclaw/pull/8160) (43 updates), opened 2026-10-25 |
| [#8133](https://github.com/nearai/ironclaw/pull/8133) | **CLOSED** | GitHub Actions group, 10 updates | Superseded by [#8159](https://github.com/nearai/ironclaw/pull/8159) (11 updates), opened 2026-10-25 |

**Interpretation:** the close-without-merge pattern is expected Dependabot behaviour when a newer rebased batch replaces a stale one. It represents *dependency debt consolidation* rather than functional progress. Notably, the replacement PRs (#8160, #8159) are only ~1 day old, suggesting the bot is keeping pace even though human merge throughput is lagging.

---

## 4. Community Hot Topics

**There are no measurable hot topics today.** All 7 PRs report `Comments: undefined` and `👍: 0`, and there are **zero issues in the tracker**, so no discussion, reaction, or sentiment signal exists to rank.

By proxy of *scope and blast radius* (not engagement), the items most worth attention are:

- **[#8160](https://github.com/nearai/ironclaw/pull/8160)** — 43-package Rust bump (`thiserror` 2.0.20→2.0.21, `uuid` 1.24→1.28, `base64`, …). Broadest surface area of the day; largest regression risk.
- **[#7834](https://github.com/nearai/ironclaw/pull/7834)** — WASM group (`wasmtime`, `wasmtime-wasi`, `wit-component`, `wit-parser`); carries `size: L` and `risk: medium` labels. This is the highest-consequence update in flight and the least obviously safe.
- **[#8159](https://github.com/nearai/ironclaw/pull/8159)** — 11 Actions bumps, including `anthropics/claude-code-action` 1.0.183→**1.0.259**; relevant to anyone relying on CI-based agent automation.

**Underlying need:** the absence of any human-authored PR or issue in 24h suggests either (a) the project routes discussion to Discord/Discussions rather than GitHub Issues, or (b) contributor throughput is currently concentrated in a small maintainer group. Worth verifying which, because a silent issue tracker makes community demand invisible.

---

## 5. Bugs & Stability

**No bugs, crashes, or regressions were reported today** (0 issues updated). No fix PRs exist because no defect reports were filed.

Latent stability risks from the dependency queue, ranked by severity:

| Severity | Item | Risk |
|---|---|---|
| **Medium** | [#7834](https://github.com/nearai/ironclaw/pull/7834) — WASM stack (`wasmtime`/`wit-*`) | Explicitly labelled `risk: medium`, `size: L`. Runtime/sandbox changes are the most likely source of behavioural regressions and are hardest to validate via CI alone. Open 64 days. |
| **Low–Medium** | [#8160](https://github.com/nearai/ironclaw/pull/8160) — 43 Rust crates at once | Large, unbatched blast radius; a single incompatible transitive bump can break the workspace build silently. |
| **Low** | [#8141](https://github.com/nearai/ironclaw/pull/8141) — tokio ecosystem (tokio 1.53.1→1.53.3, tokio-util, tokio-test) | Patch-level async runtime updates; low risk but pervasive if a scheduling/timing regression slips through. |
| **Low** | [#8140](https://github.com/nearai/ironclaw/pull/8140) — `serde_json` 1.0.151→1.0.154 | Serialization group; patch-level, widely depended upon. |
| **Low** | [#8159](https://github.com/nearai/ironclaw/pull/8159) — Actions group, incl. major-ish `setup-node` 4.0.2→7.1.0 | Major version jump inside a grouped bump is a CI-pipeline risk, not a product risk. |

**Note:** the age of #7834 (and the 8-day age of #8140/#8141) suggests stacked dependency groups may be blocking each other on shared CI or review bandwidth. Stale dependency PRs are a slow-accumulating stability and security liability.

---

## 6. Feature Requests & Roadmap Signals

**No feature requests were filed** — the issue tracker registered no activity at all, so there is no direct user-demand signal to report.

Indirect roadmap inference from the dependency stream (speculative, but data-grounded):

- **Sandboxed/plugin execution** — repeated `wasmtime`, `wasmtime-wasi`, `wit-component`, `wit-parser` updates ([#7834](https://github.com/nearai/ironclaw/pull/7834)) indicate WASM is a load-bearing runtime component, likely for tool/plugin isolation.
- **Async concurrency & agent throughput** — the tokio-ecosystem group ([#8141](https://github.com/nearai/ironclaw/pull/8141)) tracks the core runtime.
- **Agent-driven CI automation** — `anthropics/claude-code-action` jumping from 1.0.183 to 1.0.259 across two PR generations ([#8159](https://github.com/nearai/ironclaw/pull/8159), [#8133](https://github.com/nearai/ironclaw/pull/8133)) suggests active use of coding agents in the project's own workflow.

**Prediction for next version:** the most likely near-term merge is the Rust serialization/tokio patch set (#8140, #8141), followed by the grouped `everything-else` bump (#8160). The WASM group (#7834) is the least likely to land next given its size/risk labels and staleness.

---

## 7. User Feedback Summary

**No direct user feedback was captured in this window** — zero issues, zero comments, zero reactions. This is a genuine data gap rather than evidence of satisfaction.

What can be inferred with appropriate caution:

- **No evident dissatisfaction:** no crash reports, regressions, or complaint-style issues surfaced.
- **No evident delight either:** no feature requests or discussion threads to gauge engagement.
- **Possible use case signals** (from dependency choices only): sandboxed tool execution (WASM runtime), high-concurrency agent workloads (tokio), and CI-integrated agent automation (claude-code-action).

**Recommendation:** if the project intends GitHub to be a feedback surface, the empty tracker should be treated as a measurement problem. Confirm whether issues are disabled, routed elsewhere, or simply unused — otherwise the digest will keep showing zero user voice.

---

## 8. Backlog Watch

Items needing maintainer attention, ranked by staleness and consequence:

1. **[#7834](https://github.com/nearai/ironclaw/pull/7834) — WASM group, 4 updates** · opened 2026-08-23, **64 days old** · `size: L`, `risk: medium`, `scope: dependencies`
   The single most overdue item. Long-lived WASM/runtime dependency PRs accumulate conflict risk with every rebase and keep the sandbox stack pinned to older versions. **Action:** schedule a dedicated review window or split into per-crate PRs to reduce review surface.

2. **[#8141](https://github.com/nearai/ironclaw/pull/8141) — tokio ecosystem, 5 updates** · opened 2026-10-18, **8 days old**
   Patch-level and low risk; should be quick to land and is likely blocked only by review bandwidth.

3. **[#8140](https://github.com/nearai/ironclaw/pull/8140) — `serde_json` 1.0.151→1.0.154** · opened 2026-10-18, **8 days old**
   Single-package, low-risk bump lingering longer than expected — a candidate for immediate merge.

4. **[#8160](https://github.com/nearai/ironclaw/pull/8160)** and **[#8159](https://github.com/nearai/ironclaw/pull/8159)** · opened 2026-10-25, **1 day old**
   Fresh replacements for the two closed PRs; not yet backlog, but worth watching so they don't inherit the same multi-week latency as #7834.

**Process observation:** two PR generations for the same dependency groups (#8133→#8159, #8142→#8160) within the window is a churn signal. Reducing Dependabot grouping frequency or enabling auto-merge for patch-level groups would cut review load and shorten the queue.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-10-26

**Repository:** [agentscope-ai/QwenPaw](https://github.com/agentscope-ai/QwenPaw)

---

## 1. Today's Overview

QwenPaw saw **moderate triage activity but zero integration throughput** in the last 24 hours: 6 issues updated (5 open, 1 closed), 6 pull requests updated (all still open, **0 merged or closed**), and no new releases. One bug (#8416, Desktop approval dialog deadlock) was closed the same day it was reported, and a same-day fix PR (#8419) was opened for the shell self-kill guard regression (#8413) — both strong signals of responsive triage. However, the PR queue is now entirely stalled: the oldest open PR (#8187) is **14 days old** and has received no maintainer comments, and all six open PRs show no review activity. The issue mix is dominated by **correctness and data-integrity defects** — silent audit-log loss, lossy file encoding, and CRLF normalization — rather than cosmetic problems, indicating a maturing user base running QwenPaw against real repos and compliance requirements. Overall health: **stable but review-constrained**; bug inflow is being acknowledged, not yet absorbed.

| Metric | Value |
|---|---|
| Issues updated (24h) | 6 (5 open / 1 closed) |
| PRs updated (24h) | 6 (6 open / 0 merged) |
| New releases | 0 |
| Oldest unreviewed PR | #8187 — 14 days |
| Issues with a linked fix PR | 1 of 6 (#8413 → #8419) |

---

## 2. Releases

No new releases in the reporting window. The most recent user-reported baseline in circulation is **QwenPaw 2.2.2.post1** (pip install, site-packages), with bug reports also pinned to source revision `27d22cd18d6e7fe610536ef1375cebf7aea89215`.

---

## 3. Project Progress

**No PRs were merged or closed today**, so no shipped functionality. Work that *advanced* (updated, not integrated):

- **[PR #8419](https://github.com/agentscope-ai/QwenPaw/pull/8419)** `fix(tools): stop the shell self-kill guard from blocking kill $PID` (size/XS) — opened same day as the bug it fixes, narrows `_is_dangerous_self_kill` so that `PID=$(pgrep mydaemon) && kill $PID` is no longer rejected. **Fixes [#8413](https://github.com/agentscope-ai/QwenPaw/issues/8413).**
- **[PR #8268](https://github.com/agentscope-ai/QwenPaw/pull/8268)** `fix(task_tracker): bound the run completion callback and always clean up` (size/M) — addresses the run-cleanup half of #8173, where a hung/cancelled completion callback leaves `TaskTracker._runs` entries stuck in `running` forever.
- **[PR #8187](https://github.com/agentscope-ai/QwenPaw/pull/8187)** `feat(channels): extract channels into plugins with subprocess isolation` (size/XXXL) — architectural: channel discovery via `channel.json` without importing platform SDKs, subprocess execution to isolate channel failures.
- **[PR #8318](https://github.com/agentscope-ai/QwenPaw/pull/8318)** `feat(agent): add /steer command for mid-execution guidance` (size/M) — **Fixes #1775.**
- **[PR #8314](https://github.com/agentscope-ai/QwenPaw/pull/8314)** `feat(web_search): add DeepSeek Search provider` (size/M, first-time contributor).
- **[PR #8418](https://github.com/agentscope-ai/QwenPaw/pull/8418)** `docs: note eligibility review for AgentScope Platform deployment` (size/XS, first-time contributor).

**Closed today:** [Issue #8416](https://github.com/agentscope-ai/QwenPaw/issues/8416) — Desktop approval dialog retry loop.

---

## 4. Community Hot Topics

Engagement volume is low and evenly distributed (max 2 comments, 0 👍 across all items), so "hot" here means *highest discussion density*, not viral attention.

1. **[Issue #8413](https://github.com/agentscope-ai/QwenPaw/issues/8413)** — 2 comments — self-kill guard false positive.
   *Underlying need:* users run QwenPaw as an operator agent on live hosts. Over-broad safety heuristics that pattern-match on substrings (`kill` + `$pid`) break the single most common daemon-restart idiom. The need is **capability-scoped guardrails** (parse the command, don't regex it), not guard removal.

2. **[Issue #8412](https://github.com/agentscope-ai/QwenPaw/issues/8412)** — 2 comments — `governance/audit.db` silently stops recording per-agent tool calls despite `audit_level: all`.
   *Underlying need:* real compliance/forensics use. The failure mode is the worst kind — **silent, one-way, and per-agent**, with tool calls (including file writes) continuing unlogged. Users need an audit-integrity self-check and a loud failure, not best-effort writes.

3. **[Issue #8416](https://github.com/agentscope-ai/QwenPaw/issues/8416)** — 2 comments, closed same day — Desktop approval dialog cannot be dismissed after the originating turn/session is cancelled (Feishu-channel session, HIGH tool-guard rule).
   *Underlying need:* **lifecycle coherence between remote channels and the desktop client.** Approval prompts outlive their parent turn and become unkillable modal debt.

4. **[PR #8419](https://github.com/agentscope-ai/QwenPaw/pull/8419)** — linked fix for #8413, opened within 24h by a returning contributor (`suantea`, also author of #8318). Healthy contributor loop: report → fix → review.

---

## 5. Bugs & Stability

Ranked by severity (impact × blast radius × reversibility):

| # | Severity | Issue | Status | Fix PR |
|---|---|---|---|---|
| 1 | **Critical (data loss, silent)** | [#8412](https://github.com/agentscope-ai/QwenPaw/issues/8412) — `audit_events` permanently stops recording per-agent tool calls; rows never resume while the agent keeps acting | OPEN | ❌ None |
| 2 | **High (data corruption)** | [#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415) — `read_file_safe` decodes with `errors='ignore'`, so ISO-8859-1 files lose `é/ü`; `edit_file` then writes the lossy snapshot back as UTF-8 and reports "Successfully replaced text" | OPEN | ❌ None |
| 3 | **High (hard blocker, provider-specific)** | [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417) — every `/compact` returns `400 BadRequest` on Volcano Engine AgentPlan `glm-5.3-flash`: the model rejects `thinking.type=disabled`, and the compat layer emits **both** thinking-disable parameters at once | OPEN | ❌ None |
| 4 | **High (blocks core workflow)** | [#8413](https://github.com/agentscope-ai/QwenPaw/issues/8413) — self-kill guard rejects `kill $PID` targeting an unrelated user-started process | OPEN | ✅ [#8419](https://github.com/agentscope-ai/QwenPaw/pull/8419) |
| 5 | **Medium (repo-wide churn)** | [#8414](https://github.com/agentscope-ai/QwenPaw/issues/8414) — `edit_file` normalizes all `\r\n`/`\r` → `\n` and rewrites the whole file with `newline='\n'`, so a one-line edit reflows every line ending | OPEN | ❌ None |
| 6 | **Medium (UI deadlock)** | [#8416](https://github.com/agentscope-ai/QwenPaw/issues/8416) — Desktop approval dialog retries a cancelled request and cannot be dismissed | **CLOSED** | n/a |

**Notable:** #8415 documents an *unfaithful success report* — destructive write plus a "Successful" message. That combination (corruption + false confirmation) is arguably the highest-priority item without a fix in flight. #8414 and #8415 share an identical title although their bodies describe **two distinct defects**; the titles should be disambiguated to keep search/duplicate detection usable.

**Stability positives:** #8417's failure was contained — the scroll manager retained the old summary and the `keep-context-after-compact-failure` plugin preserved session context, so **no user data was lost**. Error containment worked; the endpoint capability negotiation did not.

---

## 6. Feature Requests & Roadmap Signals

No new feature *requests* were filed today; roadmap signal comes entirely from open PRs:

- **Mid-turn steering (`/steer`)** — [PR #8318](https://github.com/agentscope-ai/QwenPaw/pull/8318) closes a 3-week-old issue (#1775). Fills a genuine control gap: users must currently choose between killing a turn (losing progress) or letting irrelevant work accumulate. **High likelihood for next minor release** — small/medium scope, clear UX story.
- **Provider expansion: DeepSeek Search** — [PR #8314](https://github.com/agentscope-ai/QwenPaw/pull/8314). Moves `web_search` from 2 providers (`tavily` keyless, `anysearch`) to 3, adding **publication date** and **authority tier** metadata that keyless providers lack. Signals a roadmap toward *structured, citation-grade retrieval* rather than raw result lists.
- **Channels-as-plugins architecture** — [PR #8187](https://github.com/agentscope-ai/QwenPaw/pull/8187) (size/XXXL). Reduces host-process SDK imports and isolates channel failures — directly relevant to the #8416 class of bug (channel-sourced approvals destabilizing the Desktop client). This is the highest-leverage roadmap item but also the highest review risk.
- **Provider capability compatibility layer** — implicitly requested by [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417). Expect a follow-up feature: per-provider parameter matrices for `thinking` / reasoning-disable flags instead of a one-size compat shim.
- **Deployment honesty** — [PR #8418](https://github.com/agentscope-ai/QwenPaw/pull/8418) asks docs to stop implying "one-click deployment" when AgentScope Platform requires an eligibility application with 1–3 business day review.

**Predicted next-version contents:** self-kill guard fix (#8419), task-tracker cleanup (#8268), `/steer` (#8318), DeepSeek Search (#8314). #8187 likely slips to a later, dedicated release given its size.

---

## 7. User Feedback Summary

**Who is reporting:** production-grade operators, not casual testers — pip-installed `2.2.2.post1`, Windows 10/AMD64, Feishu-channel sessions, Volcano Engine third-party model endpoints, pinned source revisions for source-level bug isolation. Reports are detailed, reproducible, and include root-cause analysis (`_is_dangerous_self_kill`, `read_file_safe`, `newline='\n'`), i.e. users are **reading the source before filing**.

**Core pain points:**

1. **Over-aggressive safety heuristics** (#8413) — the guard protects against self-termination but blocks legitimate daemon management. Perceived as the agent *second-guessing a competent operator*.
2. **Silent, unrecoverable state loss** (#8412 audit rows; #8415 encoding) — both fail quietly and permanently. Users trust QwenPaw with compliance logs and non-UTF-8 source trees; silent one-way degradation erodes that trust faster than a crash would.
3. **Cross-platform file fidelity** (#8414 CRLF, #8415 ISO-8859-1) — the tool layer assumes UTF-8/LF universally, which is wrong for Windows repos and legacy codebases. This is a *class* of bug, not an incident.
4. **Third-party model compatibility** (#8417) — OpenAI-compatible is not sufficient; parameter negotiation fails on stricter endpoints and permanently disables `/compact`.
5. **Client lifecycle bugs** (#8416) — undismissable modal from an already-cancelled remote session.
6. **Onboarding friction** (#8418) — "one-click deploy" doesn't match the eligibility-review reality.

**Satisfaction signals:** #8416 closed same-day; #8413 got a fix PR same-day; compaction failures degrade gracefully with context preserved and explicit fallback plugins. **Dissatisfaction signal:** contributor `LinklyLuck` filed **three** distinct defects (#8413, #8414, #8415) from one testing session — a concentrated negative signal from an engaged power user, and two of the three have no fix in flight.

---

## 8. Backlog Watch

Items needing maintainer attention, ordered by risk:

1. **[PR #8187](https://github.com/agentscope-ai/QwenPaw/pull/8187)** — `size/XXXL`, open **14 days**, **zero comments**. Subprocess-isolated channel plugins. Large architectural PRs decay fastest without early directional feedback; either review or explicitly scope it. ⚠️ Highest-priority review debt.
2. **[Issue #8412](https://github.com/agentscope-ai/QwenPaw/issues/8412)** — open since 2026-10-24, **no fix PR**, silent compliance-log failure with permanent per-agent state. This is the most severe open defect in the dataset.
3. **[PR #8268](https://github.com/agentscope-ai/QwenPaw/pull/8268)** — open **10 days**, zero comments. Fixes only the *cleanup half* of #8173; the hang itself remains unresolved. Needs a decision on whether to land partial.
4. **[PR #8314](https://github.com/agentscope-ai/QwenPaw/pull/8314)** — open **8 days**, first-time contributor, zero comments. Slow first-response on a first-time contributor is a retention risk.
5. **[PR #8318](https://github.com/agentscope-ai/QwenPaw/pull/8318)** — open **7 days**, closes long-standing issue #1775, zero comments.
6. **[Issue #8415](https://github.com/agentscope-ai/QwenPaw/issues/8415)** — corrupted non-UTF-8 files with a false success message; no fix PR and title collision with #8414 impairs triage.

**Data quality note:** all PRs report `Comments: undefined`, and #8414/#8415 carry identical titles for different bugs. Both limit automated triage and should be corrected in tooling / issue metadata.

---

*Digest generated from GitHub activity for the 24-hour window ending 2026-10-26. All counts and links reflect the supplied dataset.*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-10-26

## 1. Today's Overview

ZeroClaw saw **56 issue/PR updates in the last 24 hours** (6 issues, 50 PRs) but only **two state transitions**: one issue closed and one PR merged/closed. The remaining **49 open PRs** were touched by review, CI, or rebase activity, which points to **high churn and low merge throughput** rather than active delivery. No releases were cut. Work is concentrated in four streams: the long-running **persistent-memory parity tracker** ([#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891)), **channel maturity** (reactions, streaming, attachment handling), **security/runtime hardening**, and a cluster of **`release:v0.9.0`-tagged PRs** being staged as stacked branches. Overall health: **contributor interest is strong, but review bandwidth appears to be the binding constraint** — multiple PRs carry `needs-author-action` or `needs-maintainer-review` and are 2–5 weeks old.

> Data note: PR comment counts were reported as `undefined`, and the single merged/closed PR is not among the top-20 snapshot, so its content cannot be confirmed. All visible items show 0 👍, so reaction data is not a usable signal today.

## 2. Releases

**None in the last 24 hours.** No migration or breaking-change notes to report.

Forward signal: at least six open PRs carry the `release:v0.9.0` label ([#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174), [#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986), [#11855](https://github.com/zeroclaw-labs/zeroclaw/pull/11855), [#11976](https://github.com/zeroclaw-labs/zeroclaw/pull/11976), [#11980](https://github.com/zeroclaw-labs/zeroclaw/pull/11980)), indicating v0.9.0 scope is being assembled but is not yet landable.

## 3. Project Progress

- **Merged/closed PRs today: 1** — not identifiable from the provided top-20 snapshot; the visible top 20 are all `OPEN`.
- **Closed issue: [#12034](https://github.com/zeroclaw-labs/zeroclaw/issues/12034)** — *RFC: Personal Agent MVP: visitor-facing knowledge-and-request subsystem*, filed and closed the same day with 0 comments. It referenced companion PR [#12033](https://github.com/zeroclaw-labs/zeroclaw/pull/12033) (`rfc/personal-agent-mvp`). Closure without discussion suggests withdrawal, consolidation, or process rejection rather than a completed decision.

**Features and fixes advancing (in review, not merged):**

| Area | Item | Substance |
|---|---|---|
| Memory | [#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209) | Rejects unknown memory backends instead of silently falling back to Markdown |
| Runtime | [#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174) | Capability-taking constructors for all turn entry points (XL, 30 days open) |
| Gateway/Web | [#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986) | Scoped dashboard chat routed through the core; stacked on 3 other PRs |
| Channels | [#11754](https://github.com/zeroclaw-labs/zeroclaw/pull/11754), [#11739](https://github.com/zeroclaw-labs/zeroclaw/pull/11739), [#11744](https://github.com/zeroclaw-labs/zeroclaw/pull/11744) | Configurable ack reactions; Telegram reaction withdrawal; reasoning streaming in Telegram/Slack |
| Channels | [#11267](https://github.com/zeroclaw-labs/zeroclaw/pull/11267), [#11746](https://github.com/zeroclaw-labs/zeroclaw/pull/11746) | Lark/Feishu image parsing; email inbound attachments persisted to workspace |
| Security | [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326), [#11644](https://github.com/zeroclaw-labs/zeroclaw/pull/11644), [#11672](https://github.com/zeroclaw-labs/zeroclaw/pull/11672), [#11283](https://github.com/zeroclaw-labs/zeroclaw/pull/11283) | RPC log-read scoping (p1), cron shell-output leak, JPEG decode projection, SOP scope→tool-name matching |
| Plugins | [#11855](https://github.com/zeroclaw-labs/zeroclaw/pull/11855) | Cleans abandoned install staging directories |
| CI/Infra | [#11988](https://github.com/zeroclaw-labs/zeroclaw/pull/11988), [#11976](https://github.com/zeroclaw-labs/zeroclaw/pull/11976), [#11741](https://github.com/zeroclaw-labs/zeroclaw/pull/11741) | Semgrep SARIF filtering; CLI-only build ratchet; dist-broad container image |
| i18n/Tests | [#11774](https://github.com/zeroclaw-labs/zeroclaw/pull/11774), [#11770](https://github.com/zeroclaw-labs/zeroclaw/pull/11770) | Regional→language locale fallback; locale-independent channel tests |

The dominant structural pattern is **stacked PR chains** (notably from JordanTheJet, e.g. #11986 depending on #11965/#11186/#11964, and #11976 on #11975/#11269/#11973). This raises review cost and is a plausible driver of the low merge count.

## 4. Community Hot Topics

1. **[#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891) — Persistent memory parity tracker** · 14 comments · open since 2026-07-09 · `priority:p2`, `risk:high`, `type:tracker`, `topic:memory`
   The only genuinely discussed item. Tracks a multi-PR rollout covering **curation, relevance, and operability planes** for cross-session memory, currently at "7 named open items: 4 issues, 3 PRs" per its own snapshot. *Underlying need:* users want memory that behaves predictably against mature peer runtimes — not just storage, but selection, ranking, and management. This is the project's single most strategic open thread.

2. **[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853) — `npm audit` failed (2026-10-19)** · 3 comments · bot-filed · `security`, `risk:high`
   One high/critical finding plus a moderate `@tailwindcss/typography` advisory, with a linked Actions run. *Underlying need:* automated supply-chain gates are firing and nobody has owned remediation for 7 days. Given the web dashboard's growth (#11986, #12021), front-end dependency hygiene matters more than the tooling noise suggests.

3. **PR discussion volume is not observable** (counts `undefined`), so hotness must be inferred from labels and age: [#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174) (30 days, XL, `distinguished contributor`), [#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209) (28 days, `needs-author-action`), [#11056](https://github.com/zeroclaw-labs/zeroclaw/pull/11056) (34 days, `size:XS`).

**Read:** community energy is concentrated in **runtime semantics** (memory, capabilities) and **security**, while day-to-day channel work generates PRs but little visible dialogue. Three of six issues have zero comments — either fast triage or low reviewer engagement.

## 5. Bugs & Stability

Ranked by severity, with fix-PR status:

| Severity | Item | Impact | Fix PR? |
|---|---|---|---|
| **High** | [#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853) — npm audit high/critical | Build gate failing; supply-chain exposure in web dependencies | ❌ None linked |
| **High** | [#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209) — unknown `memory.backend` silently built `MarkdownMemory` | **Silent durable-memory misrouting**: unrecognized backend wrote cross-session memory into workspace Markdown files | ✅ Fix is the PR itself, stalled 28 days on `needs-author-action` |
| **High** | [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326) — `logs/query` and `logs/get` lacked `require_global_stream_access` | Scoped principals could read persisted logs they shouldn't | ✅ Fix PR open, 25 days |
| **High** | [#11644](https://github.com/zeroclaw-labs/zeroclaw/pull/11644) — cron shell jobs forwarded raw `status/stdout/stderr` to channels | Command text and stderr leakage into delivery channels | ✅ Fix PR open, 16 days, `needs-maintainer-review` |
| **Medium** | [#11283](https://github.com/zeroclaw-labs/zeroclaw/pull/11283) — SOP scope groups (`fs`, `web`) didn't match registered tool names (`file_read`, `web_search`) | **Permission scope mismatches** on SOP steps — potential under- or over-restriction | ✅ Fix PR open, 26 days |
| **Medium** | [#11672](https://github.com/zeroclaw-labs/zeroclaw/pull/11672) — JPEG admission allocation | Decode-peak accounting; potential memory pressure | ✅ Fix PR open, 14 days |
| **Medium (silent)** | [#12035](https://github.com/zeroclaw-labs/zeroclaw/issues/12035) — `knowledge_bundles` validated but never consumed at runtime | Config silently ignored, **no warning**; nothing crashes but operator intent is lost | ❌ None |
| **Low (S3)** | [#12021](https://github.com/zeroclaw-labs/zeroclaw/issues/12021) — Web UI config save button stays highlighted after banner save | Says "1 unsaved change" until daemon reload; cosmetic/UX | ❌ None |

**Pattern:** the most dangerous bugs in this batch are **fail-open or fail-silent** behaviors — unrecognized memory backend, ignored knowledge bundles, unenforced log scoping. There are no crashes or regressions reported today. Notably, a well-defined small PR ([#11056](https://github.com/zeroclaw-labs/zeroclaw/pull/11056), `size:XS`) has sat for 34 days, so latency is not purely a size problem.

## 6. Feature Requests & Roadmap Signals

**New requests filed today:**

- **[#12022](https://github.com/zeroclaw-labs/zeroclaw/issues/12022)** — Memory recall: opt-in external rerank stage. Add `rerank_strategy = "external"` that POSTs the eligible candidate pool to an operator-configured `/v1/rerank` endpoint and keeps its ordering; **default off, fail-open to the local path on any error.** Well-specified and low-risk by design — a strong candidate for a **v0.9.x memory release**, since it plugs directly into the #8891 relevance plane.
- **[#12035](https://github.com/zeroclaw-labs/zeroclaw/issues/12035)** — Actually consume `knowledge_bundles` at prompt-assembly time (or warn). More a correctness gap than a feature; cheap to land, good first-issue material.
- **[#12034](https://github.com/zeroclaw-labs/zeroclaw/issues/12034, closed)** — Personal Agent MVP: visitor-facing knowledge-and-request subsystem. Closed same-day and AI-disclosed per RFC #5615. Even in closure it signals intent toward **deployable, visitor-facing personal agents** rather than developer-only runtimes.

**Predicted v0.9.0 / near-term contents (from labels and stacking):**

1. **Runtime capability plumbing** — [#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174) + docs [#11980](https://github.com/zeroclaw-labs/zeroclaw/pull/11980). The deepest architectural change in flight.
2. **Scoped dashboard chat** — [#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986), contingent on its three base PRs.
3. **Channel polish bundle** — configurable ack reactions, Telegram/Slack reasoning streaming, Lark images, email attachments. High user-visible value, mostly XL-sized.
4. **Plugin install robustness** — [#11855](https://github.com/zeroclaw-labs/zeroclaw/pull/11855).
5. **CLI-only build target with artifact ratchet** — [#11976](https://github.com/zeroclaw-labs/zeroclaw/pull/11976), plus the dist-broad image ([#11741](https://github.com/zeroclaw-labs/zeroclaw/pull/11741)).

**Unlikely in v0.9.0:** external rerank (#12022, brand new), knowledge-bundle consumption (#12035, unassigned), and the npm audit remediation (#11853, no owner).

## 7. User Feedback Summary

**Dominant pain point: silent misbehavior.** Four separate reports today or near-today describe the system accepting input and doing nothing — or something else — without telling the user: `knowledge_bundles` validated then dropped ([#12035](https://github.com/zeroclaw-labs/zeroclaw/issues/12035)), unknown memory backends silently writing Markdown ([#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209)), Telegram/Lark rich messages dropped before reaching the agent ([#11267](https://github.com/zeroclaw-labs/zeroclaw/pull/11267)), email attachments never written to the workspace despite documentation ([#11746](https://github.com/zeroclaw-labs/zeroclaw/pull/11746)). This is the clearest cross-cutting dissatisfaction theme in the dataset.

**Operator/enterprise needs surfacing:**
- **Least-privilege enforcement** — scoped principals must not read global logs ([#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326)).
- **Data exfiltration prevention** — cron diagnostics must not reach channels ([#11644](https://github.com/zeroclaw-labs/zeroclaw/pull/11644)).
- **Supply-chain assurance** — a failing `npm audit` blocks builds ([#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853)).

**Localization friction:** non-English locales break both tests and UI behavior ([#11774](https://github.com/zeroclaw-labs/zeroclaw/pull/11774), [#11770](https://github.com/zeroclaw-labs/zeroclaw/pull/11770)). Contributors are now fixing test brittleness caused by hardcoded English notice text — a sign of real non-English usage.

**Use cases visible:** multi-channel assistant deployment (Slack, Telegram, Matrix, Lark/Feishu, Email, WhatsApp, Web), SOP-driven scoped automation, plugin-based extension, and — via the closed RFC — **visitor-facing personal agents**. **Satisfaction signal:** sustained contribution volume and careful docs work (e.g. WhatsApp voice-note documentation, [#11056](https://github.com/zeroclaw-labs/zeroclaw/pull/11056)) indicate engaged users. **Dissatisfaction signal:** repeated `needs-author-action` labels mean maintainers are waiting on contributors, not the reverse — process latency, not rejection.

## 8. Backlog Watch

Items needing maintainer attention, sorted by age:

| Age | Item | Why it matters |
|---|---|---|
| **109d** | [#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891) — memory parity tracker | `priority:p2`, `risk:high`, 14 comments, still coordinating 4 issues + 3 PRs. No visible completion criteria or burn-down. |
| **34d** | [#11056](https://github.com/zeroclaw-labs/zeroclaw/pull/11056) — WhatsApp voice-note docs | `size:XS`, `risk:low`, `needs-author-action`. A trivial docs PR stuck for over a month is the clearest process smell. |
| **30d** | [#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174) — runtime capability constructors | XL, `distinguished contributor`, `release:v0.9.0`, `risk:high`. **Likely a release blocker**; review found gaps (#11980 exists to patch documentation). |
| **28d** | [#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209) — reject unknown memory backends | `needs-author-action`, but this closes a silent data-routing bug. |
| **26d** | [#11267](https://github.com/zeroclaw-labs/zeroclaw/pull/11267), [#11283](https://github.com/zeroclaw-labs/zeroclaw/pull/11283) | Lark image drops; SOP scope/tool-name mismatch. Both `needs-author-action`. |
| **25d** | [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326) — RPC log scoping | `priority:p1`, `release-gate`, `domain:security`. **Highest-priority security fix in the backlog.** |
| **16d** | [#11644](https://github.com/zeroclaw-labs/zeroclaw/pull/11644) — cron leak | `needs-maintainer-review`; security-relevant and stalled on reviewers. |
| **14d** | [#11672](https://github.com/zeroclaw-labs/zeroclaw/pull/11672) — JPEG projection | `needs-maintainer-review`. |
| **7d** | [#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853) — npm audit failure | Bot-filed, `risk:high`, no assignee, no fix PR. Automated security debt with no owner. |

**Assessment:** the backlog is not short of work — it is short of **review decisions**. Two `needs-maintainer-review` security PRs and one `priority:p1` release-gate PR are waiting on maintainers, while five `needs-author-action` PRs are waiting on contributors. Clearing #11326 and unblocking #11174 would materially improve both security posture and the v0.9.0 timeline; assigning an owner to #11853 and merging #11056 would be low-cost signals that the queue is moving.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*