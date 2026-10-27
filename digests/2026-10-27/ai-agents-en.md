# OpenClaw Ecosystem Digest 2026-10-27

> Issues: 500 | PRs: 500 | Projects covered: 5 | Generated: 2026-10-27 01:36 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-10-27

## 1. Today's Overview

OpenClaw is operating at very high throughput: 1,000 items were updated in the last 24 hours (500 issues: 400 open/active, 100 closed; 500 PRs: 182 open, 318 merged/closed), with a large share of churn driven by an ongoing internal performance/test-cleanup campaign ("deslop", batch `d64x` test removals, lazy-loading and catalog-reuse optimizations). However, **zero releases were cut**, and the issue tracker shows an accumulation of P0 "ux-release-blocker" reports — most concentrated in Windows update/restart reliability, SQLite/WAL persistence, and memory growth. Community attention is dominated by a small number of high-severity, long-running threads (the top issue accumulated 139 comments). Activity assessment: **high maintainer velocity, but release readiness is constrained by an unresolved Windows/storage reliability cluster**.

## 2. Releases

None. No new releases in the last 24 hours and the "Latest Releases" feed is empty. Given multiple open P0 items tagged `impact:ux-release-blocker` (notably [#143524](https://github.com/openclaw/openclaw/issues/143524), [#160959](https://github.com/openclaw/openclaw/issues/160959), [#170956](https://github.com/openclaw/openclaw/issues/170956), [#166634](https://github.com/openclaw/openclaw/issues/166634)), the absence of a release is consistent with a held release train rather than an idle one. No migration or breaking-change notes to report.

## 3. Project Progress

318 PRs were merged/closed today. Notable landings and advances:

- **Security/UX:** [#185263](https://github.com/openclaw/openclaw/pull/185263) (closed) — stop surfacing sign-in warnings for routine OAuth access-token expiry; Settings → Models now distinguishes expiry from unusable auth.
- **Web UI fixes:** [#185292](https://github.com/openclaw/openclaw/pull/185292) (closed) — saved highlighted-text Side chat comments can be submitted without a second question; [#184617](https://github.com/openclaw/openclaw/pull/184617) (closed) — resize oversized JPEG camera photos before Control UI upload (Android Chrome attach failure).
- **Performance:** [#185287](https://github.com/openclaw/openclaw/pull/185287) (closed) — remove per-field prompt-hook envelope tuples; [#185249](https://github.com/openclaw/openclaw/pull/185249) (closed) — drop unused plugin root load tracking.
- **In-flight high-value fixes (open):** [#185250](https://github.com/openclaw/openclaw/pull/185250) stops prepared-runtime rebuild storms during provider auth cooldowns; [#184293](https://github.com/openclaw/openclaw/pull/184293) puts compaction's hard deadline on the monotonic clock (fixes clock-jump-induced premature compaction failure); [#185293](https://github.com/openclaw/openclaw/pull/185293) prevents long streamed HTML/JSX replies from re-projecting the entire reply on every partial tag; [#185238](https://github.com/openclaw/openclaw/pull/185238) fixes `store` SecretRef auth tokens breaking CLI Gateway calls on PostgreSQL; [#185237](https://github.com/openclaw/openclaw/pull/185237) rebuilds ported memory indexes concurrently to cut migration downtime; [#183595](https://github.com/openclaw/openclaw/pull/183595) and [#184994](https://github.com/openclaw/openclaw/pull/184994)/[#184951](https://github.com/openclaw/openclaw/pull/184951) reduce Control UI reload work and premature chat-route loading.
- **Closed issues indicating resolved defects:** [#184315](https://github.com/openclaw/openclaw/issues/184315) (operator.admin rejected for operator.read — scope semantics), [#162899](https://github.com/openclaw/openclaw/issues/162899) (Windows Control UI managed update freeze in `validating`), [#89430](https://github.com/openclaw/openclaw/issues/89430) (Google Chat media delivery 403 under app auth), [#154517](https://github.com/openclaw/openclaw/issues/154517) (sessions delete/archive `not_found`), [#172091](https://github.com/openclaw/openclaw/issues/172091) (worker SQLite transaction held open behind admission wait), [#150696](https://github.com/openclaw/openclaw/issues/150696) (Codex-native child activity after parent yield).

## 4. Community Hot Topics

| Item | Status | Comments | Signal |
|---|---|---|---|
| [#143524](https://github.com/openclaw/openclaw/issues/143524) SQLite WAL grows to 1.4–2.8 GB, blocks gateway startup (Windows) | CLOSED, P0 | 139 | Largest thread in the project; storage-layer durability on Windows is the single biggest community concern. |
| [#160959](https://github.com/openclaw/openclaw/issues/160959) Gateway blocks for minutes capturing large external plugins (2026.9.6 regression) | OPEN, P0 | 57 | Event-loop starvation introduced by plugin generation capture (#144252); affects startup and runtime republish. |
| [#159662](https://github.com/openclaw/openclaw/issues/159662) `prepared-model-catalog.worker.js` unbounded leak ~4–5 GB/h | OPEN, P0 | 26 | Provider-agnostic, reproduced on cold reboot; forces frequent restarts on small hosts. |
| [#170956](https://github.com/openclaw/openclaw/issues/170956) Windows slow startup, unclean plugin shutdown, `schtasks` permission error | OPEN, P0 | 21 | Compound reliability failure: restarts/updates "only after many retries". |
| [#140010](https://github.com/openclaw/openclaw/issues/140010) Sleep/resume on Windows breaks WebSocket reconnect for 30–60s+ | OPEN, P1 | 16 | Laptop/mobile-host UX; thaw recovery defers behind busy gateway. |
| [#182476](https://github.com/openclaw/openclaw/issues/182476) `update repair` fails with `OPENCLAW_STATE_LEASE_LOST` | OPEN, P1 | 14 | New (Oct 24) and consistent with the broader lifecycle-lease cluster. |
| [#67419](https://github.com/openclaw/openclaw/issues/67419) Bootstrap files re-injected every turn, wasting 20–30% tokens | OPEN, P2, stale | 14 | Oldest high-visibility cost complaint (created Apr 15). |

**Underlying needs:** (a) a trustworthy storage/checkpointing story on Windows (WAL, integrity checks, transactions, leases); (b) predictable startup latency regardless of plugin/model-catalog size; (c) bounded memory on 8–16 GB hosts; (d) token/cost efficiency via stable prompt prefixes and non-redundant context injection.

## 5. Bugs & Stability

**P0 / release-blocking (open)**
- [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway blocks for minutes on large external plugins; regression from 2026.9.6. No fix PR flagged (`no-new-fix-pr`), needs maintainer review.
- [#159662](https://github.com/openclaw/openclaw/issues/159662) — 4–5 GB/h memory leak in prepared-model-catalog worker; no fix PR flagged.
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows startup/shutdown/schtasks reliability; fix shape clear, awaiting maintainer.
- [#166634](https://github.com/openclaw/openclaw/issues/166634) — checkpoint expiry during crash recovery leaves an agent permanently `UNAVAILABLE` for `chat.history`/`models.list`; marked `not-repro-on-main`, needs repro.

**P1 (open, notable)**
- [#182476](https://github.com/openclaw/openclaw/issues/182476) `OPENCLAW_STATE_LEASE_LOST` during `update repair` (Windows).
- [#183786](https://github.com/openclaw/openclaw/issues/183786) Windows beta upgrade stalls querying a stopped foreground Gateway PID.
- [#181432](https://github.com/openclaw/openclaw/issues/181432) Baseline RSS regressed ~300 MB → 1.89 GB after 2026.9.4 → 2026.10.5 (Linux ARM64), with SQLite transaction holds >1.5 s.
- [#145203](https://github.com/openclaw/openclaw/issues/145203) Hung `openai-completions` SSE stream (48.5 min) never recovered; stall watchdog starved by `stream_progress`.
- [#134993](https://github.com/openclaw/openclaw/issues/134993) Gateway pegs one CPU core in filesystem discovery after 2026.8.1 with large skill/agent fleets.
- [#118885](https://github.com/openclaw/openclaw/issues/118885) Redundant full `PRAGMA integrity_check` passes over multi-GB state DB at startup.
- [#172837](https://github.com/openclaw/openclaw/issues/172837) Codex `nativeSubagentAssignments` never removed after delivery gives up — re-pinned/re-sent each new parent thread.
- [#177656](https://github.com/openclaw/openclaw/issues/177656) `Direct shared-state reader requires current integrity admission` on native host exec (≥28 failures on 2026.10.5).

**P2 / correctness and data-integrity**
- [#166017](https://github.com/openclaw/openclaw/issues/166017) `mcp.sessionIdleTtlMs` default not applied → MCP runtimes never evicted, broker hits 256-child cap and all exec/MCP starts fail.
- [#126906](https://github.com/openclaw/openclaw/issues/126906) Denying `write` tool silently disables memory persistence while the agent reports success.
- [#158070](https://github.com/openclaw/openclaw/issues/158070) Preflight compaction voids in-flight delivery ("tool caller authority is no longer active").
- [#178300](https://github.com/openclaw/openclaw/issues/178300) False success after a 1-token `length` stop.
- [#129314](https://github.com/openclaw/openclaw/issues/129314), [#151962](https://github.com/openclaw/openclaw/issues/151962) — internal runtime strings ("phantom user messages") surfacing as real user turns, not persisted to `transcript_events`.
- [#146902](https://github.com/openclaw/openclaw/issues/146902), [#143017](https://github.com/openclaw/openclaw/issues/143017) — prompt-cache defeat via tool-set churn and prefix rewrites (21.3M cache-write tokens measured in one report).

**Fix-PR availability:** Fix-shaped/queueable items include [#168456](https://github.com/openclaw/openclaw/issues/168456) (fallback dies on turn-key assertion for cron `agentTurn`) and [#177094](https://github.com/openclaw/openclaw/issues/177094) (system-notice mirror displaces last answer). Most P0 storage/memory items are explicitly tagged `clawsweeper:no-new-fix-pr`, i.e., **still unowned**.

## 6. Feature Requests & Roadmap Signals

- **Identity/security:** [#162164](https://github.com/openclaw/openclaw/issues/162164) opt-in personal identity on iOS/macOS while preserving Shared owner (active contribution map, multiple merged slices); [#149024](https://github.com/openclaw/openclaw/issues/149024) host-wide pre-execution authorization guard + fail-closed empty allowlist semantics (security review needed).
- **Resource-aware scheduling:** [#161050](https://github.com/openclaw/openclaw/issues/161050) memory-pressure-aware admission for `sessions_spawn`/isolated cron runs — `subagents.maxConcurrent` counts children, not RSS (~250–400 MB per `claude-cli` child).
- **Session/UX continuity:** [#112372](https://github.com/openclaw/openclaw/issues/112372) opt-in origin-bound deferred completion (`wakeOnReply`) for `sessions_send`; [#158800](https://github.com/openclaw/openclaw/issues/158800) browse/fork prior channel-chat segments after `/new`; [#175826](https://github.com/openclaw/openclaw/issues/175826) Android parity for in-chat questions/native approvals.
- **Channel integrations:** [#136271](https://github.com/openclaw/openclaw/issues/136271) dynamic SharePoint site resolution for Teams file uploads.

**Prediction:** the next release (when cut) is most likely dominated by reliability and performance rather than new surface area — Control UI reload/streaming fixes, compilation/clock correctness, PostgreSQL auth-token path, and catalog-reuse optimizations. Memory-pressure admission (#161050) and identity work (#162164) look like the strongest candidates for the following cycle; both are still gated on product/security decisions.

## 7. User Feedback Summary

- **Windows is the dominant pain surface.** Windows-host users report WAL growth to multi-GB (#143524), restart/update failures via `schtasks` and lifecycle leases (#170956, #182476, #183786, #170654), updater freezes (#162899, now closed), and broken sleep/resume reconnect (#140010). Sentiment is frustration with retry-heavy recovery ("only after many retries").
- **Small-host and multi-agent operators hit resource ceilings.** 4–5 GB/h leaks (#159662), 1.9 GB baseline RSS (#181432), MCP broker exhaustion at 256 children (#166017), and one-CPU-core discovery loops (#134993) make always-on gateways impractical on modest hardware.
- **Cost sensitivity is explicit.** Multiple detailed reports quantify token waste: 20–30% of context consumed by bootstrap files (#67419), 21.3M cache-write tokens from prefix rewrites (#143017), and tool-set churn breaking cache reuse (#146902).
- **Trust in agent-reported state is eroded** by silent failures: memory writes reported successful when denied (#126906), false success after truncation (#178300), phantom user messages in WeChat/Slack bridges (#151962, #129314), and agents left permanently unavailable after crash recovery (#166634). Reporters are highly technical, provide wire captures and reproductions, and frequently ship local patches — a strong signal of an invested power-user base.

## 8. Backlog Watch

Long-lived or low-attention items needing maintainer decisions:

- [#67419](https://github.com/openclaw/openclaw/issues/67419) (Apr 15) — context bloat / bootstrap re-injection; 14 comments, 2 👍, tagged `needs-product-decision`. Highest-leverage cost fix still undecided.
- [#118885](https://github.com/openclaw/openclaw/issues/118885) (Aug 3) — redundant startup integrity checks on multi-GB DBs; P1, `needs-product-decision`, `bulk-filed`.
- [#129314](https://github.com/openclaw/openclaw/issues/129314) (Aug 25) — internal context leaking as standalone turns; stale, `needs-info`, `session-state`.
- [#114414](https://github.com/openclaw/openclaw/issues/114414) — dated TODO sweep listing overdue CI/auth and workflow cleanup; a recurring hygiene debt marker.
- [#136271](https://github.com/openclaw/openclaw/issues/136271) and [#112372](https://github.com/openclaw/openclaw/issues/112372) — both have open linked PRs but remain stalled on product decisions.
- PR side: [#144685](https://github.com/openclaw/openclaw/pull/144685) (macOS approval-panel dismissal, created Sep 11) is still in `needs proof`; [#183595](https://github.com/openclaw/openclaw/pull/183595) is explicitly "ready for maintainer review; stop before merge" with one unresolved candidate-only terminal test failure — a candidate for maintainer time.
- `clawsweeper:needs-maintainer-review` is attached to a large fraction of the top-50 issues, indicating review bandwidth, not issue intake, is the current bottleneck.

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — Personal AI Assistant / Agent OSS Ecosystem
**Window:** 2026-10-27 (24h community digests) · **Projects:** OpenClaw, Hermes Agent, QwenPaw, ZeroClaw, IronClaw

---

## 1. Ecosystem Overview

The five projects in this sample have converged on a common architecture — a long-lived gateway/daemon, channel adapters, a tool/plugin layer, a session store, and a memory/retrieval backend — and are now in an **operational hardening phase** rather than a feature-expansion phase: **not a single release was cut across any project** in the window, despite 1,224 combined issue/PR updates. Competitive differentiation has shifted from capability breadth to **durability, diagnosability, and cost control**, with the bug mass concentrated in session/admission semantics, context compaction, memory integrity, and resource backpressure. The ecosystem is sharply bimodal: OpenClaw operates at ~20× the volume of any peer (1,000 items vs 100/72/52), while the mid-sized projects show contributor supply consistently exceeding review capacity. One project (IronClaw) is dormant, confirming that consolidation pressure is already active in this category.

---

## 2. Activity Comparison

| Project | Issues (open/closed) | PRs (open/merged) | Merge/close rate | Release status | Health score |
|---|---|---|---|---|---|
| **OpenClaw** | 500 (400 / 100) | 500 (182 / 318) | **63.6%** PR · 20% issue | **None** — train held by 4 open P0 `ux-release-blocker`s | **7.0 / 10** |
| **QwenPaw** | 24 (17 / 7) | 48 (33 / 15) | 31.3% PR · 29% issue | None — 2.2.3b1 beta in flight | **6.5 / 10** |
| **Hermes Agent** | 50 (split n/d) | 50 (45 / 5) | 10.0% PR | None — latest stable v0.22.5 carries regressions | **5.0 / 10** |
| **ZeroClaw** | 23 (23 / 0) | 29 (28 / 1) | **3.4%** PR · 0% issue | None — v0.8.7 RC prep, v0.9.0 work stacked | **3.5 / 10** |
| **IronClaw** | 0 | 0 | — | None | Dormant |

*Health score rubric (composite, /10): closure throughput 30% · release readiness / P0 burden 30% · review responsiveness 20% · engagement quality 20%. Scores are comparative within this sample, not absolute quality judgments.*

**Key observations:**
- Excluding OpenClaw, the three active peers merged **21 of 127 touched PRs (16.5%)** vs OpenClaw's 63.6% — OpenClaw is the only project closing faster than it accumulates.
- ZeroClaw closed **zero issues** in 24h while opening 8 security issues in a single day.
- **All projects report review bandwidth, not intake, as the binding constraint** — OpenClaw (`clawsweeper:needs-maintainer-review` on most of the top 50), Hermes (4 PRs >60 days old, one blocked on a maintainer CI approval click), QwenPaw (17 open PRs incl. several XL/XXXL), ZeroClaw (5+ `needs-author-action`, 1 `do-not-merge`).

---

## 3. OpenClaw's Position

**Advantages vs peers**
- **Scale of community and throughput:** 1,000 items/24h (~8× QwenPaw, ~10× Hermes, ~19× ZeroClaw); the top thread has 139 comments vs a peer maximum of 22. Reporters ship wire captures and local patches — an invested power-user base.
- **Infrastructure completeness:** the only project in the sample with a PostgreSQL backend option, SecretRef credential abstraction, state leases, prepared-model-catalog worker pools, and monotonic-clock-bounded compaction. Peers are still fixing single-process assumptions (Hermes #139818 remote resume, QwenPaw #8279 thread exhaustion).
- **Release discipline (inverted):** a *held* release train is a stronger signal than an unheld one — the project is gated on its own P0s rather than shipping through them (contrast Hermes, where v0.22.5 stable carries an unresolved admission-conflict regression).

**Technical approach differences**
OpenClaw's failure modes are **infrastructural rather than logical** — multi-GB SQLite/WAL growth (#143524), state-lease loss on `update repair` (#182476), 4–5 GB/h worker leaks (#159662), event-loop starvation during plugin capture (#160959). That profile is the signature of a system that has already solved the *agent logic* and is now paying the tax on being an always-on daemon. Peer projects fail earlier in the stack (aiohttp import crash, thread-pool deadlock, admission literals) because their runtimes are simpler.

**Community size comparison**
Roughly an order of magnitude larger on every engagement metric, but with a corresponding liability: **the highest-severity items are explicitly unowned** — `clawsweeper:no-new-fix-pr` is attached to #159662 and #160959, and #166634 is tagged `not-repro-on-main`. Volume has outrun deep-fix labor.

---

## 4. Shared Technical Focus Areas

| Requirement | Projects | Specific needs |
|---|---|---|
| **Context/token economics & non-destructive compaction** | OpenClaw, ZeroClaw, QwenPaw, Hermes | Bootstrap re-injection costing 20–30% of context (OC #67419); 21.3M cache-write tokens from prefix rewrites (OC #143017); `context_compression.*` config keys silently inert (ZC #10780/#10781); trim *permanently deletes* turns (ZC #11711); compaction cannot fit a request at all (QP #8312, #8417) |
| **Session identity, admission & wake semantics** | Hermes, OpenClaw, ZeroClaw | `admission_conflict` refusal loops at 2,641/day with attempt caps that never fire (Hermes #145261, #143720, #142461); checkpoint expiry leaving an agent permanently `UNAVAILABLE` (OC #166634); failed ACP turns not persisted on the daemon RPC path (ZC #10673) |
| **Durable persistence & data integrity** | All four active | WAL/integrity-check cost and transaction holds (OC #143524, #118885, #172091); remote `session.resume` → `not_found` (Hermes #139818); silent embedding batch drops (QP #8040); lossy CRLF/encoding rewrite reported as success (QP #8415); plugin manifest/binary generation mismatch (ZC #11562) |
| **Silent-failure elimination / diagnosability** | All four active | "126/126 processed" while dropping a batch (QP #8040); zero-log indefinite hangs (QP #8435); `data.fields` vs `data.field` turning a precise refusal into *"couldn't start the model connection"* (Hermes #145286); memory write denied but reported successful (OC #126906); bare "Command not allowed by security policy" causing agent retry loops (ZC #11983) |
| **Resource-bounded concurrency & backpressure** | All four active | Concurrency capped on the wrong dimension — child count instead of RSS (OC #161050, #166017 256-child cap); 1.89 GB baseline RSS (OC #181432); AnyIO thread-limiter exhaustion (QP #8279); webhook cron deadlock at thread-pool size (Hermes #145315); unbounded response buffering (ZC #12045) |
| **Provider/proxy compatibility tolerance** | QwenPaw, Hermes, OpenClaw | Non-conforming OpenAI Responses streams (QP #8162); SGLang misclassified as Ollama, missing Gemini `thought_signature`, missing `x-api-key` path (Hermes #145309, #104345, #145275); auth-cooldown rebuild storms (OC #185250) |
| **Identity & least-privilege as release gate** | ZeroClaw, OpenClaw, Hermes | 8-issue OIDC/tool-scoping sweep incl. SOP privilege escalation, XFF limiter bypass, callback CSRF (ZC #12046–#12053); host-wide pre-exec authz with fail-closed defaults (OC #149024); A2A no-token-mode hardening, HITL approval bypass (Hermes #145310, #131461) |
| **Plugin/channel contract depth** | QwenPaw, ZeroClaw, Hermes | Semantic token theming instead of `colorPrimary` (QP #8071); nested skill dirs, channels-as-subprocesses (QP #8178, #8187); plugin generation integrity (ZC #11562); plugin activation 401 swallowed (Hermes #141878) |

---

## 5. Differentiation Analysis

| Dimension | OpenClaw | Hermes Agent | QwenPaw | ZeroClaw |
|---|---|---|---|---|
| **Primary form factor** | Gateway daemon + Control UI (web), Windows-heavy install base | Gateway + remote `hermes serve` + desktop client + ACP editor integrations (Zed/VS Code/JetBrains/Obsidian) | **Desktop application** (Console, PawApp SDK, `deb`/AppImage, Finder/Explorer integration, Chrome native messaging) | Rust daemon + RPC + web dashboard |
| **Target user** | Power users / small teams running always-on multi-agent gateways | Multi-surface operators: Discord, Mattermost, Feishu, QQ, Google Workspace; plugin & orchestration authors | Desktop end-users, strong CJK/heterogeneous-gateway (Volcengine etc.) and localization sensitivity | Security-conscious self-hosters, multi-principal / multi-tenant operators, sovereign deployments |
| **Architecture signature** | Worker pools, prepared catalog caches, state leases, SQLite/WAL **or PostgreSQL**, SecretRef | Python asyncio/aiohttp, thread-pool offload, MCP + A2A, Kanban orchestration, subagent lifecycle API | Python backend + desktop shell, local ReMe/ReMeLight memory + embeddings + rerankers, channel plugin subprocesses | Rust (cargo), Qdrant vector store, OIDC, XMPP+OMEMO, Forgejo/Gitea forge parity, SOP/`sop_workshop` |
| **Differentiating bet** | Breadth + reliability of a full runtime | Surface-area parity (delegation/ACP/Kanban) | Memory quality + desktop polish + localization | Identity, authorization and supply-chain integrity |
| **Weakest flank** | Windows/storage reliability, small-host memory, release readiness | Session admission layer; PR review capacity | Compaction correctness; context-window failure modes | Merge throughput; 8 unowned security issues pre-v0.9.0 |

**IronClaw** shows no signal and should be treated as inactive for planning purposes.

---

## 6. Community Momentum & Maturity

**Tier 1 — High-velocity, broad surface (OpenClaw).** 63.6% PR merge rate with 318 landings/day, but a large share is internal campaign churn ("deslop", batch test removals, lazy-loading). Functionally the most mature; operationally the least stable on Windows and 8–16 GB hosts. *Rapidly iterating, release-constrained.*

**Tier 2 — High engagement, review-bound (QwenPaw, Hermes).** Both show the classic mid-scale signature: triage keeps pace, deep fixes do not. QwenPaw converts well-scoped reports fast (#8162 filed Oct 10, fix merged Oct 26; #8270 and #4946 closed this window) but has 3 critical no-fix-PR bugs and a privacy-relevant PR (#7835) unreviewed since Sep 17. Hermes advanced 45 PRs but merged 5, closed a feature request same-day, and has **no PR at all** for its three largest stability risks (aiohttp crash-loop, webhook cron deadlock, admission cluster). *Rapidly iterating on surface area, stabilizing on core.* Both have demonstrated maturity signals — production 976 MB history DBs, Docker deployments, daily backup crons.

**Tier 3 — Security hardening, backlog rot risk (ZeroClaw).** 23 issues in, 0 out; 8 security issues filed in one day with no fix PRs; v0.9.0 work stacked several PRs deep on unmerged parents (#11234/#11230 on #11225). *Stabilizing operationally, but accumulating unreviewed risk ahead of a security-themed release.*

**Dormant (IronClaw).**

**Cross-cutting maturity read:** every project's top-engagement metric is now a **trust or reliability thread**, not a capability announcement — OpenClaw's 139-comment WAL thread, Hermes' 22-comment session-resume thread, QwenPaw's 12-comment plugin-contract thread, ZeroClaw's context-compression cluster. The category has crossed from "what can it do" to "can I leave it running."

---

## 7. Trend Signals

1. **Reliability is now the release gate.** Four independent P0 `ux-release-blocker` clusters held OpenClaw's train; ZeroClaw tags security work `release:v0.9.0`/`release-gate`; Hermes' stable v0.22.5 ships with an unresolved session regression. *Implication: latency-to-stable is becoming a competitive metric, not just capability.*

2. **Session/lease/admission is the new hard problem.** Visible in all three gateway-style projects. Concrete guidance: model session state explicitly (owner, lease, TTL, generation), make wakes **idempotent**, cap retries with a dead-letter path, and make caps *reachable* — Hermes #143720 shows a `_MAX_COMPLETION_*` constant that is dead code in the active path.

3. **Compaction must be implemented, removed, or documented — never inert.** ZeroClaw's silent no-op config keys (#10781) and destructive trim (#11711), plus QwenPaw's hard compaction failures (#8312), make this the single highest-leverage trust fix in the ecosystem. Ship a token-budget accounting surface, not just a policy.

4. **Silent failure is the #1 cross-project UX complaint** (4/4 active projects). Two concrete patterns for agent developers: (a) never return success for a denied, truncated, or dropped operation; (b) make refusals **machine-remediable** — reason + hint (#11983) — because autonomous agents loop on opaque errors.

5. **Backpressure must be dimensioned by the consuming resource.** Four projects independently capped the wrong counter: child count instead of RSS (#161050), thread-pool slots instead of event-loop progress (#145315), default AnyIO limiter instead of task count (#8279), response size after buffering instead of before (#12045).

6. **Cost observability is a purchasing criterion.** Quantified waste (20–30% of context on bootstrap re-injection; 21.3M measured cache-write tokens) plus demand for per-runtime thinking levels (ZC #12054) and per-operation model selection for memory writes (QP #7719). Expect token-accounting dashboards to become table stakes.

7. **Strict-spec parsers will fail in the wild.** Every project has at least one bug caused by a non-conforming OpenAI-compatible proxy or gateway. Build tolerant stream parsers with terminal-event fallbacks.

8. **Identity and least-privilege are transitioning from optional to release-gated.** ZeroClaw's OIDC stack, OpenClaw's pre-exec authorization guard and personal-identity work, and Hermes' HITL bypass fixes all point the same way. Principal-aware tool scoping (not just role checks) looks like a 2027 baseline requirement for self-hosted agents.

9. **Plugin supply chains are an emerging attack surface.** Manifest/binary generation mismatch (#11562), plugin-generation capture blocking the gateway (#160959), and an unpinned third-party dashboard script (#12053) are three distinct classes of the same risk. Treat plugin admission as a two-phase commit.

10. **Windows and desktop parity remain an underserved market.** OpenClaw's dominant pain surface is Windows storage/update/sleep-resume; QwenPaw is actively investing in `deb`/AppImage and Wayland portals. For "personal" assistants — where the user's actual machine *is* the deployment target — treating Windows as a secondary platform is a durable competitive gap.

---

**Bottom line for decision-makers:** OpenClaw is the reference implementation and the only project closing faster than it accrues — but it is blocked on a Windows/storage P0 cluster with no assigned owners, and its release readiness is the ecosystem's clearest near-term risk (and opportunity). QwenPaw is the best-managed mid-scale project and the strongest bet for desktop/CJK use cases. Hermes has the richest orchestration surface and the weakest session layer. ZeroClaw is making the correct long-term architectural bet (identity, integrity, sovereignty) but is currently constrained by review throughput. Architecturally, the ecosystem has converged; the differentiator over the next two quarters will be who converts that convergence into trustworthy, bounded, diagnosable always-on behavior first.

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest
**Date:** 2026-10-27 · **Repo:** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. Today's Overview

Activity remains very high: 50 issues and 50 PRs were touched in the last 24 hours, but the merge ratio is thin — 45 PRs remain open against 5 merged/closed, and zero releases shipped. The issue stream is dominated by a single architectural theme: **session identity, wake/admission, and resume semantics** across gateway, cron, Discord, and desktop clients, with four separate reports of `admission_conflict` refusal loops, one reaching 2,641 occurrences in a day. A second cluster covers **canonical API contract mismatches** between the gateway and CLI (`data.fields` vs `data.field`, inherited `HERMES_SESSION_SOURCE` labels), suggesting a schema-governance gap rather than isolated defects. Positively, maintainers closed a same-day feature request (#145313) and three visible PRs landed, and the community is contributing substantive, well-reproduced reports plus their own fix PRs. Overall health: **high engagement, high throughput on triage, but a growing stabilization debt in the session/gateway layer and a review bottleneck on the PR queue.**

---

## 2. Releases

No new releases in the last 24h, and no release exists to summarize. The most recent reported stable in the issue stream is **v0.22.5** (multiple reports cite commit `bf0203a794` / `df722c88648a`), with `v0.21.4` (`v2026.9.21`) still referenced by one reporter still on the older tag (#119805).

> Signal: several of today's highest-impact bugs were filed *against* 0.22.5, indicating the current stable carries unresolved session/admission regressions. A patch release would be valuable once the admission-conflict cluster has a root cause.

---

## 3. Project Progress

**Merged/closed PRs today: 5** (3 visible in the provided sample):

| PR | What it resolved |
|---|---|
| [#145106](https://github.com/NousResearch/hermes-agent/pull/145106) (CLOSED) | Salvage of #144544 — plain-HTTP sends/copies, browser edit menus, rebuild-safe chunks, image links, restart-wait prompts in the webapp. Cherry-picked across 522 commits of drift. |
| [#75981](https://github.com/NousResearch/hermes-agent/pull/75981) (CLOSED) | `projects.create` / `project_create` now rejects creation when `primary_path` already owns a project — fixes duplicate projects for one folder. |
| [#41043](https://github.com/NousResearch/hermes-agent/pull/41043) (CLOSED) | Update-check cache now busts when local HEAD moves, ending stale "N commits behind" banners for up to 6h after a manual `git pull`. |

**Feature work advanced (open PRs, not yet merged):**
- **Orchestration/delegation parity:** [#145316](https://github.com/NousResearch/hermes-agent/pull/145316) adds lookup of a finished delegation by id (durable `async_delegations` ledger becomes queryable, not just TUI-visible); [#145317](https://github.com/NousResearch/hermes-agent/pull/145317) lets orchestrator agents reclaim a *running* Kanban card; [#145305](https://github.com/NousResearch/hermes-agent/pull/145305) preserves native summary/spill/usage fields in `subagent_lifecycle.result()`.
- **Security hardening:** [#145310](https://github.com/NousResearch/hermes-agent/pull/145310) refuses browser-originated and foreign-Host requests to the A2A listener in no-token mode; [#131461](https://github.com/NousResearch/hermes-agent/pull/131461) closes two human-in-the-loop approval bypass/block bugs (awaiting maintainer CI approval).
- **Provider correctness:** [#145309](https://github.com/NousResearch/hermes-agent/pull/145309) stops classifying SGLang endpoints as Ollama; [#104345](https://github.com/NousResearch/hermes-agent/pull/104345) injects a Gemini `thought_signature` sentinel to prevent HTTP 400 on model fallback; [#145307](https://github.com/NousResearch/hermes-agent/pull/145307) stops vision fallback from carrying a pinned model id across vendors.
- **Platform/skills:** [#145312](https://github.com/NousResearch/hermes-agent/pull/145312) (Mattermost thread free-response after @mention), [#145311](https://github.com/NousResearch/hermes-agent/pull/145311) (Google Workspace `calendar freebusy`), [#144346](https://github.com/NousResearch/hermes-agent/pull/144346) (refresh bridged platform settings on YAML change), [#85041](https://github.com/NousResearch/hermes-agent/pull/85041) (local TTS optimization playbook, 377s→100.2s case study).

**Closed issue:** [#145313](https://github.com/NousResearch/hermes-agent/issues/145313) — Composio credential reuse for the plugin catalog, opened and closed the same day.

---

## 4. Community Hot Topics

| Item | Activity | Underlying need |
|---|---|---|
| [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — Remote-backend desktop sessions unresumable; `session.resume` → `not_found` | **22 comments**, 1 👍, open 11 days, tagged `sweeper:risk-session-state` | Session records must survive the process that created them (`source='desktop'` + remote `hermes serve`). Users run long-lived remote backends and expect durable, addressable sessions. |
| [#105758](https://github.com/NousResearch/hermes-agent/issues/105758) — Lifecycle guard false-positive blocks Node.js CLIs | **15 comments**, `needs-decision`, open 7 weeks | The "can this command restart the gateway?" heuristic is too broad and breaks legitimate tooling (`obsidian-headless`). Needs a policy decision, hence stalling. |
| [#142461](https://github.com/NousResearch/hermes-agent/issues/142461) — Automation wakes into auto-created Discord threads refused `admission_conflict` forever | **10 comments, 6 👍** (highest reaction count today) | Parent sessions never resume after children finish; users want automation wakes to be *idempotent and eventually admitted*, not silently requeued forever. |
| [#133212](https://github.com/NousResearch/hermes-agent/issues/133212) — `subagent_lifecycle` v1.1: steer, host-isolation transport, effort/route/follow-up | **8 comments**, `needs-decision` | Plugin authors need `ctx.subagent_lifecycle` to reach parity with `delegate_task` under `plugins.isolation: host`. |
| [#143720](https://github.com/NousResearch/hermes-agent/issues/143720) — Completion wake refused every 60s forever; attempt cap never fires | **6 comments** | `_MAX_COMPLETION_*` cap is dead code in this path; `notify-mode=off` has no effect. |
| [#141878](https://github.com/NousResearch/hermes-agent/issues/141878) — Cross-process plugin activation 401 on login-gated dashboards | **5 comments** | Process tokens vs. provider-authenticated sessions are conflated; failure is swallowed silently. |

**Analysis.** The reaction leader (#142461, 6 👍) and the two highest-comment threads (#139818, #105758) point at the same meta-need: **Hermes' session/wake layer lacks clear ownership and bounded retry semantics.** Users are not asking for new surface area — they are asking for the existing surfaces to fail loudly, retry finitely, and survive restarts. A design note on "admission control contract" (who admits, when to back off, when to drop) would likely close several of these at once.

---

## 5. Bugs & Stability

No issue in today's window is labeled **P1**, but several **P2** items have crash-loop or message-loss blast radius.

### Critical (crash-loop / deadlock / data-plane stall)
1. **[#144560](https://github.com/NousResearch/hermes-agent/issues/14560)** *(P2, gateway/webhook)* — Gateway **crash-loops** when `aiohttp` is absent because `-> web.Response` annotations are evaluated at import time (66 sites, ruff UP037). A degraded environment becomes a hard failure instead of a graceful adapter skip. **No fix PR visible.**
2. **[#145315](https://github.com/NousResearch/hermes-agent/issues/145315)** *(P2, new today)* — Webhook `cron_job` routes **deadlock the whole gateway** once as many runs fire as the default thread pool has threads: `asyncio.to_thread` holds a thread that then waits unboundedly on the owner loop. Clean, reproducible, architectural. **No fix PR visible.**
3. **[#145292](https://github.com/NousResearch/hermes-agent/issues/145292)** *(P2, MCP)* — A transport teardown that never finishes leaves `_serve_session`'s run loop stuck; **MCP tools never come back**. Needs timeouts on `ClientSession`/`transport_cm` exit.
4. **[#145308](https://github.com/NousResearch/hermes-agent/issues/145308)** *(P2, gateway/sessions)* — `clarify` in a `message_agent`-started turn **never renders but holds the session lease**, so `restart --yes` hangs waiting on it.

### High (silent failure / message loss / auth)
5. **Admission-conflict refusal loops** — [#145261](https://github.com/NousResearch/hermes-agent/issues/145261) (**2,641 refusals on 2026-10-26 alone**, multiplex profiles + Discord group session), [#143720](https://github.com/NousResearch/hermes-agent/issues/143720) (494 occurrences in one log, still firing after 6h), [#142461](https://github.com/NousResearch/hermes-agent/issues/142461). Three independent environments, same symptom → systemic, not user misconfiguration.
6. **[#139818](https://github.com/NousResearch/hermes-agent/issues/139818)** *(P2, `sweeper:risk-session-state`)* — `session.resume` → `not_found` for remote-backend desktop sessions once the hosting process is gone.
7. **[#145275](https://github.com/NousResearch/hermes-agent/issues/145275)** *(P2, auth)* — `key_cmd` for `transport: anthropic_messages` is always sent as `Authorization: Bearer`, never `x-api-key`, so an Anthropic Console key can't be supplied.
8. **[#145063](https://github.com/NousResearch/hermes-agent/issues/145063)** *(P2, tools)* — Tool-call arguments **drop fields / pick the wrong action** for params with `anyOf` branches or optional payloads (observed on `deepseek-v4.1-flash`). Model-dependent silent corruption.
9. **[#145286](https://github.com/NousResearch/hermes-agent/issues/145286)** + **[#143653](https://github.com/NousResearch/hermes-agent/issues/143653)** *(P2, CLI/sessions)* — Canonical `session.create` refusals are mis-rendered: gateway sends `data.fields`, CLI reads `data.field`, so a precise policy refusal surfaces as *"couldn't start the model connection."* #143653 is the specific trigger: inherited `HERMES_SESSION_SOURCE=kanban|automation` labels rejected by the canonical `Literal`.

### Medium
10. **[#145314](https://github.com/NousResearch/hermes-agent/issues/145314)** *(P2, new)* — Quiet cron scripts leave a **permanent empty session** every firing on the non-`no_agent` path.
11. **[#123923](https://github.com/NousResearch/hermes-agent/issues/123923)** *(P2)* — Anthropic 400 *"thinking blocks cannot be modified"*: heartbeat continuation duplicates thinking blocks into one assistant message.
12. **[#119529](https://github.com/NousResearch/hermes-agent/issues/119529)** *(P2, duplicate-tagged)* — `web_extract` always fails on fresh installs: autodetect picks search-only `ddgs`. High-frequency onboarding papercut.
13. **[#119805](https://github.com/NousResearch/hermes-agent/issues/119805)** *(P2, qqbot)* — Group sends route to `/v2/users/<group_openid>/messages` after restart → `400 用户/群已注销` until the group has inbound traffic.
14. **[#144438](https://github.com/NousResearch/hermes-agent/issues/144438)** *(P2, ACP)* — `session/list` empty in gateway mode: editor chats stored as `source='local'` but listed only as `source='acp'`. Affects Zed, VS Code, JetBrains, Obsidian.
15. **[#145298](https://github.com/NousResearch/hermes-agent/issues/145298)** *(P2, desktop)* — File drop on messaging-platform sessions always `permission_denied` (`file.attach` requires a LOCAL policy) while the drop affordance stays enabled.
16. **[#143845](https://github.com/NousResearch/hermes-agent/issues/143845)** *(P2)* — Dashboard delete of a finished cron session → 404 while the row still exists; CLI delete of the same id succeeds.

### Low (P3, polish/docs)
17. [#140633](https://github.com/NousResearch/hermes-agent/issues/140633) — Wayland/HDR washed-out UI under NVIDIA SwiftShader (`cause/upstream`).
18. [#145282](https://github.com/NousResearch/hermes-agent/issues/145282) — GNOME lock/unlock permanently removes tray icon.
19. Skill-doc defects: [#145304](https://github.com/NousResearch/hermes-agent/issues/145304) (`ascii-video` undefined `colors`/`resize`, 0/0 on flat frames), [#145306](https://github.com/NousResearch/hermes-agent/issues/145306) (authoring guidance tells users to `git checkout --` and discard work), [#145276](https://github.com/NousResearch/hermes-agent/issues/145276) (`manim-video` mislabels remote TTS as local).

**Fix-PR coverage today is weak for the top of this list.** PRs exist for vision fallback (#145307), SGLang detection (#145309), Gemini fallback (#104345), approvals (#131461), gateway config refresh (#144346) — but **no visible fix PR for the admission-conflict cluster (#145261/#143720/#142461), the aiohttp crash-loop (#144560), or the webhook cron deadlock (#145315).** Those three represent the largest stability risk in the current stable line.

---

## 6. Feature Requests & Roadmap Signals

| Request | Status | Likelihood for next version |
|---|---|---|
| [Subagent lifecycle v1.1](https://github.com/NousResearch/hermes-agent/issues/133212) — steer, host-isolation transport, effort/route/follow-up | Open, 8 comments, `needs-decision`, tied to PRs [#145305](https://github.com/NousResearch/hermes-agent/pull/145305)/[#145316](https://github.com/NousResearch/hermes-agent/pull/145316) by the same author | **High** — steady incremental PR stream from the reporter; the parity gap is well-articulated |
| [Kanban notify subscriptions](https://github.com/NousResearch/hermes-agent/issues/136614) — choose which event kinds wake the agent | Open, P3, `sweeper:risk-message-delivery` | **Medium-high** — strong quantified motivation (681 wake turns/week, 547 avoidable) and PRs #145317 already expanding Kanban orchestration |
| Delegation result lookup by id | PR [#145316](https://github.com/NousResearch/hermes-agent/pull/145316) | **Medium** — small, contained surface |
| A2A no-token-mode hardening | PR [#145310](https://github.com/NousResearch/hermes-agent/pull/145310) | **Medium-high** — security-labeled, low diff |
| Mattermost thread free-response (`MATTERMOST_THREAD_FREE_RESPONSE`) | PR [#145312](https://github.com/NousResearch/hermes-agent/pull/145312) | **Medium** — config-gated default-off, low risk |
| Google Workspace `calendar freebusy` | PR [#145311](https://github.com/NousResearch/hermes-agent/pull/145311) | **High** — skill-only, ported from an existing upstream PR |
| Composio catalog credential reuse | [#145313](https://github.com/NousResearch/hermes-agent/issues/145313) closed same day | Resolved/redirected — treat as maintenance |

**Directional read:** the roadmap is being pulled by *agent orchestration* (Kanban, delegation, subagent lifecycle) and *plugin/platform breadth* (Mattermost, Google Workspace, A2A). Notably absent from the feature queue is any proposal to redesign session admission/retry — a gap worth flagging, since that is where the bug mass is concentrated.

---

## 7. User Feedback Summary

**Pain points, in order of volume:**
1. **Silent, unhelpful failures.** Three independent reports describe the same experience: a mutation is refused, the UI shows generic copy, and retries never stop. From [#145286](https://github.com/NousResearch/hermes-agent/issues/145286): a precise `invalid_params` naming the offending field renders as *"Hermes couldn't start the model connection."* From [#141878](https://github.com/NousResearch/hermes-agent/issues/141878): a 401 on plugin activation is *silently dropped*. From [#145298](https://github.com/NousResearch/hermes-agent/issues/145298): `permission_denied` looks like an OS bug because the drop affordance stays enabled. Users are explicitly asking for error-copy fidelity, not just correctness.
2. **Retry storms with no ceiling.** [#145261](https://github.com/NousResearch/hermes-agent/issues/145261) (2,641 refusals/day), [#143720](https://github.com/NousResearch/hermes-agent/issues/143720) (attempt cap never fires), [#142461](https://github.com/NousResearch/hermes-agent/issues/142461). Fills logs, burns quota, and hides real failures.
3. **Deployment-shape assumptions.** Self-hosters run remote `hermes serve` + desktop client + multiple profiles (#139818, #145261), plus ACP viewers (#144438), plus WSL2 without systemd (#119805). Several bugs only appear in these "always-on / multiplexed" topologies, suggesting the happy path is single-process local.
4. **Trust in bundled content.** The #145276/#145304/#145306 trio — all from one reporter inspecting `skills/` references — shows users *read and run* the bundled examples, and incorrect ones (0/0 division, `git checkout --` destroying work) are treated as product bugs, with `area/i18n` tagged on one.
5. **Legitimate automation being treated as hostile.** #105758: a lifecycle guard intended to prevent self-termination blocks benign Node.js CLIs. The `needs-decision` label shows this is a policy question, and it has now sat 7 weeks with 15 comments.

**Satisfaction signals:** reports are long, well-instrumented (log counts, commits, repros) and often include root-cause analysis — a sign of an invested, technically sophisticated user base. Multiple users submit their own fix PRs (`100yenadmin`, `unsupportedpastels`, `arimu1`, `teknium1`), and one issue was resolved same-day.

---

## 8. Backlog Watch

| Item | Age | Why it needs attention |
|---|---|---|
| [#105758](https://github.com/NousResearch/hermes-agent/issues/105758) — lifecycle guard false-positive | **7 weeks**, 15 comments, `needs-decision` | Highest comment count of any *unresolved* issue not tied to a fresh regression. Blocking real Node.js CLI workflows; decision is being deferred. |
| [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — remote desktop sessions unresumable | **11 days**, 22 comments, `area/auth` + `sweeper:risk-session-state` | Most-commented issue today, no linked fix PR. Session-state risk tag suggests known fragility. |
| [PR #31734](https://github.com/NousResearch/hermes-agent/pull/31734) — fold system prefill into main system prompt | **Open since 2026-05-25 (~5 months)** | Fixes strict-provider rejections from multiple `role: system` messages. Carries three sweeper risk tags; likely needs a compatibility call rather than more code. |
| [PR #62565](https://github.com/NousResearch/hermes-agent/pull/62565) — Feishu legacy websocket kwargs | **Open since 2026-07-11** | Small compatibility shim; `cause/upstream` + `blast-moderate`. Low-risk merge candidate. |
| [PR #85041](https://github.com/NousResearch/hermes-agent/pull/85041) — local TTS optimization skill | **Open since 2026-08-13** | Docs/skill only, with production benchmarks (377s→100.2s). Zero runtime risk; blocked purely on review bandwidth. |
| [PR #94983](https://github.com/NousResearch/hermes-agent/pull/94983) — compaction authority under `checkpoint_required` | **Open since 2026-08-25** | Closes remaining lossy-transcript paths; `sweeper:risk-session-state` + `area/compression`. This is *the* follow-up to #94639 and touches durable data — needs an owner. |
| [PR #131461](https://github.com/NousResearch/hermes-agent/pull/131461) — approval bypass/block fixes | **Open since 2026-10-02**, **CI `action_required`, 0 checks run** | Security-relevant (human-in-the-loop bypass) but **cannot land without a maintainer clicking approve**. A pure process blocker. |
| [#119529](https://github.com/NousResearch/hermes-agent/issues/119529) / [#119805](https://github.com/NousResearch/hermes-agent/issues/119805) | Open since 2026-09-22 / 09-23 | Both hit fresh-install or restart flows — high-frequency user paths, each only 4 comments, risk of being lost under the newer admission-conflict noise. |

**Structural observation for maintainers:** with 45 open vs. 5 merged PRs in 24h and at least four PRs older than 60 days carrying low-risk diffs (#85041, #62565, partial #31734), the bottleneck is **review capacity, not contributor supply**. Conversely, the highest-severity bugs of the day (#144560, #145315, admission-conflict cluster) have **no PR at all** — a signal that triage is keeping pace but deep gateway/session work is under-resourced relative to incoming reports.

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-10-27

## 1. Today's Overview

QwenPaw is in a high-velocity maintenance phase: 24 issues and 48 PRs were touched in the last 24 hours, with 15 PRs merged/closed and 7 issues closed, against a remaining 33 open PRs and 17 active issues. No release was cut in this window, so fixes are accumulating toward the next 2.2.x/2.3 build. Activity clusters around provider compatibility (OpenAI Responses streaming), MCP protocol negotiation, memory/embedding correctness, and desktop platform parity. The issue mix skews toward stability rather than new capability — several high-severity reports (indefinite LLM stream hangs, context-compaction failures) remain without a fix PR, which is the main risk to project health despite strong throughput.

## 2. Releases

No new releases in this window (`Latest Releases: None`). The most recent field versions referenced by users are 2.2.2, 2.2.2.post1, and 2.2.3b1, indicating a beta channel is active ahead of the next stable cut.

## 3. Project Progress

**Closed/merged PRs (visible in the top-20 sample):**

| PR | Change | Impact |
|---|---|---|
| [#8165](https://github.com/agentscope-ai/QwenPaw/pull/8165) | `fix(providers)`: recover Responses output sent only on the terminal `response.completed` event | Fixes [#8162](https://github.com/agentscope-ai/QwenPaw/issues/8162) — sessions no longer abort after 1–3 steps on providers that omit content deltas |
| [#8062](https://github.com/agentscope-ai/QwenPaw/pull/8062) | `fix(memory)`: keep healthy embedding vectors when one chunk exceeds the limit | Partial fix for [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040); per-item fallback on batch failure |
| [#7923](https://github.com/agentscope-ai/QwenPaw/pull/7923) | `feat(scroll)`: age out `tool_result` blocks via `blocks_retention_days` | Addresses unbounded `history.db` growth (~75% of a 976 MB store); a same-titled replacement PR [#8443](https://github.com/agentscope-ai/QwenPaw/pull/8443) is already open, suggesting rework before final merge |

**Closed issues:** [#8162](https://github.com/agentscope-ai/QwenPaw/issues/8162) (Responses API empty stream), [#7476](https://github.com/agentscope-ai/QwenPaw/issues/7476) (cron double-fire inside `misfire_grace`), [#8270](https://github.com/agentscope-ai/QwenPaw/issues/8270) (Windows ZIP filename mojibake), [#4946](https://github.com/agentscope-ai/QwenPaw/issues/4946) (plugin `ImportError` blocking startup 6+ minutes), [#8429](https://github.com/agentscope-ai/QwenPaw/issues/8429) (superseded by [#8439](https://github.com/agentscope-ai/QwenPaw/issues/8439)), [#8286](https://github.com/agentscope-ai/QwenPaw/issues/8286) (thinking-strength slider rendering), [#8333](https://github.com/agentscope-ai/QwenPaw/issues/8333) (sidebar group-header overlap).

**Advancing open PRs:** MCP legacy fallback [#8445](https://github.com/agentscope-ai/QwenPaw/pull/8445), task-cancellation leak [#8441](https://github.com/agentscope-ai/QwenPaw/pull/8441), atomic-write filename bounds [#8446](https://github.com/agentscope-ai/QwenPaw/pull/8446), Linux `deb`/AppImage packaging [#8444](https://github.com/agentscope-ai/QwenPaw/pull/8444), nested skill directories [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178), channel plugin extraction [#8187](https://github.com/agentscope-ai/QwenPaw/pull/8187), ChatGPT subscription sign-in [#8437](https://github.com/agentscope-ai/QwenPaw/pull/8437), PowerContext backend [#8376](https://github.com/agentscope-ai/QwenPaw/pull/8376), local Whisper plugin [#8431](https://github.com/agentscope-ai/QwenPaw/pull/8431), PawApp SDK redesign [#7874](https://github.com/agentscope-ai/QwenPaw/pull/7874).

## 4. Community Hot Topics

All items show 0 👍, so engagement is comment-driven; comment counts below are from the issue set (PR counts were not exposed in this snapshot).

| Item | Comments | Underlying need |
|---|---|---|
| [#8071](https://github.com/agentscope-ai/QwenPaw/issues/8071) Plugin-facing theme extension point | 12 | Plugin authors need parity with built-in Console theming (semantic token overrides), not just `colorPrimary`. Signals the plugin API is the ecosystem bottleneck. |
| [#8162](https://github.com/agentscope-ai/QwenPaw/issues/8162) Responses API empty stream (closed) | 8 | Heterogeneous gateways/proxies don't follow the delta-event contract; users need tolerant streaming parsers. |
| [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) Embedding reindex silently drops a batch | 6 | Silent data loss in memory indexing, specifically CJK chunks over per-item token limits. Trust in long-term memory. |
| [#7476](https://github.com/agentscope-ai/QwenPaw/issues/7476) Cron duplicate scheduling (closed) | 5 | Scheduler exactly-once semantics; duplicated backup scripts are a production-integrity issue. |
| [#8270](https://github.com/agentscope-ai/QwenPaw/issues/8270) Windows ZIP mojibake (closed) | 4 | CJK/legacy-encoding archives via `ZipFile` without `encoding=`; localization quality. |
| [#8312](https://github.com/agentscope-ai/QwenPaw/issues/8312 / [#8424](https://github.com/agentscope-ai/QwenPaw/issues/8424) / [#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276) / [#8279](https://github.com/agentscope-ai/QwenPaw/issues/8279) / [#8405](https://github.com/agentscope-ai/QwenPaw/issues/8405) | 3 each | Context-window management, MCP legacy interop, reranker config validation, AnyIO thread starvation, Console modal lifecycle. |

**Analysis:** the three dominant demand signals are (a) a deeper, more stable plugin contract, (b) defensive handling of non-conforming model/MCP servers, and (c) trustworthy memory and scheduling — i.e., users are pushing QwenPaw from "demo agent" into always-on infrastructure.

## 5. Bugs & Stability

Ranked by severity; "fix PR" indicates a visible open/closed PR addressing it.

| Severity | Item | Symptom | Fix PR |
|---|---|---|---|
| Critical | [#8435](https://github.com/agentscope-ai/QwenPaw/issues/8435) | LLM stream hangs indefinitely with zero logs; rate-limiter semaphore never releases, watchdog never fires (v2.2.2 Docker) | None visible |
| Critical | [#8312](https://github.com/agentscope-ai/QwenPaw/issues/8312) | Context compaction cannot fit request (252,565 > 251,904 tokens) → task interruption (2.2.2) | None visible |
| Critical | [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417) | `/compact` deterministically 400s on Volcengine AgentPlan `glm-5.3-flash`; compat layer sends both `thinking` disable params | None visible |
| High | [#8279](https://github.com/agentscope-ai/QwenPaw/issues/8279) | Long-lived ReMe watchers exhaust AnyIO's default thread limiter, stalling sync HTTP endpoints | None visible |
| High | [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) | One over-limit CJK chunk silently drops its whole embedding batch; loop still logs `processed=126/126` | Partial: [#8062](https://github.com/agentscope-ai/QwenPaw/pull/8062) closed |
| High | [#8440](https://github.com/agentscope-ai/QwenPaw/issues/8440) | `cancellable_wait` leaves owned tool task running after outer cancellation (2.2.3b1, `main`) | [#8441](https://github.com/agentscope-ai/QwenPaw/pull/8441) open |
| High | [#8424](https://github.com/agentscope-ai/QwenPaw/issues/8424) | `server/discover` 404 + `-32601` hard-fails `_negotiate()`, blocking legacy MCP fallback | [#8445](https://github.com/agentscope-ai/QwenPaw/pull/8445) open |
| High | [#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415) | `edit_file` rewrites all CRLF→LF and re-encodes lossy ISO-8859-1 snapshots as UTF-8, reporting success | None visible |
| High | [#8425](https://github.com/agentscope-ai/QwenPaw/issues/8425) | Mail monitor builds `name@domain@domain` when `name` holds a full address → `AUTHENTICATIONFAILED` after upgrade | None visible |
| Medium | [#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276) | Reranker enablement accepts empty `api_key`; surfaces only as "unexpected error" | None visible |
| Medium | [#8405](https://github.com/agentscope-ai/QwenPaw/issues/8405) | MCP config modal cannot be closed when transport is http/sse (stdio works) | None visible |
| Medium | [#8123](https://github.com/agentscope-ai/QwenPaw/issues/8123) | Daily Paper marks whole job failed on truncated model output; no per-paper retry | None visible |

**New today (2026-10-26/27):** #8440, #8435, #8424, #8425, #8420, #8439, #8442. Note the recurring pattern of *silent* failures — #8040, #8276, #8435, #8415 all report success or emit no log while producing wrong/hung state. That is a systemic observability gap, not four isolated bugs.

## 6. Feature Requests & Roadmap Signals

| Request | Signal | Likely timing |
|---|---|---|
| [#8071](https://github.com/agentscope-ai/QwenPaw/issues/8071) Plugin theme extension (semantic token override) | 12 comments, open since Oct 1 — the most-discussed item | Next minor (2.3.x) if API is agreed |
| [#8442](https://github.com/agentscope-ai/QwenPaw/issues/8442) Linux desktop parity: `deb` + AppImage, Computer Use via Wayland portal, Chrome Native Messaging | PR [#8444](https://github.com/agentscope-ai/QwenPaw/pull/8444) already open | 2.3.x; Computer Use/Wayland likely later |
| [#8439](https://github.com/agentscope-ai/QwenPaw/issues/8439) `thinking_level` in cron `JobRuntimeSpec` | Re-filed after #8429 was closed "review later" | Likely next patch/minor; small surface |
| [#8420](https://github.com/agentscope-ai/QwenPaw/issues/8420) Files panel "Open with default app" / "Reveal in Finder/Explorer" | New, desktop UX gap | Patch-friendly |
| [#7912](https://github.com/agentscope-ai/QwenPaw/issues/7912) Authenticated MCP web-research docs example | Community-contributed docs | Docs-only, near-term |
| [#8437](https://github.com/agentscope-ai/QwenPaw/pull/8437) ChatGPT subscription sign-in (OAuth PKCE) | XXXL PR | 2.3.x — auth surface needs review |
| [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) Nested/categorised skill directories | XL, first-time contributor, Under Review | 2.3.x |
| [#8187](https://github.com/agentscope-ai/QwenPaw/pull/8187) Channels as plugin subprocesses | XXXL | Multi-release effort |
| [#7719](https://github.com/agentscope-ai/QwenPaw/pull/7719) Separate model for ReMeLight memory writes | M, open since Sep 12 | Cost-control ask; likely 2.3.x |

**Prediction:** the next patch (2.2.3) will be stability-only — MCP fallback (#8445), cancellation leak (#8441), atomic filename bounds (#8446), scroll block retention (#8443), plus the already-closed Responses fix. 2.3.0 is shaping up as the "platform" release: Linux desktop, plugin theming, nested skills, ChatGPT sign-in, channel isolation.

## 7. User Feedback Summary

**Pain points, by volume:**
- **Provider/gateway compatibility (loudest):** non-conforming OpenAI Responses streams ([#8162](https://github.com/agentscope-ai/QwenPaw/issues/8162)), Volcengine `glm-5.3-flash` rejecting dual thinking-disable params ([#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417)). Users run heterogeneous Chinese and international gateways; strict spec adherence breaks them.
- **Long-term memory trust:** silent batch drops ([#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040)), weak reranker validation ([#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276)), AnyIO exhaustion on long-running deployments ([#8279](https://github.com/agentscope-ai/QwenPaw/issues/8279)).
- **Context management:** compaction failing outright ([#8312](https://github.com/agentscope-ai/QwenPaw/issues/8312), [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417)) — users hit hard task interruption rather than graceful degradation.
- **Localization/data integrity:** Windows ZIP mojibake ([#8270](https://github.com/agentscope-ai/QwenPaw/issues/8270)), CRLF re-encoding ([#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415)) — both disproportionately affect CJK and mixed-encoding workflows.
- **Upgrade regressions:** mail monitor auth break after upgrade ([#8425](https://github.com/agentscope-ai/QwenPaw/issues/8425)) — config migration is not handling pre-existing full-address `name` fields.

**Satisfaction signals:** turnaround is genuinely fast for well-scoped reports — #8162 was filed Oct 10 and its fix PR landed the same day, closing Oct 26; #8270 and #4946 also closed this window. Users are clearly deploying QwenPaw as production infrastructure (976 MB history DBs, Docker deployments, daily backup crons), which is a strong maturity signal.

**Dissatisfaction signals:** "zero logs" hangs (#8435), misleading "unexpected error" surfaces (#8276), and lossy edits reported as "Successfully replaced text" (#8415). The complaint is less about missing features than about *diagnosability* when the agent fails.

## 8. Backlog Watch

| Item | Age | Why it needs attention |
|---|---|---|
| [#7835](https://github.com/agentscope-ai/QwenPaw/pull/7835) `fix(memory)`: stop leaking auto-memory-recall payload to channels | Open since 2026-09-17, Under Review | Privacy/data-exposure issue: synthetic memory traces reach Feishu/QQ and other outbound channels. Highest-priority unreviewed PR in the backlog. |
| [#7719](https://github.com/agentscope-ai/QwenPaw/pull/7719) separate model for ReMeLight memory writes | Open since 2026-09-12 | Direct cost-control request with no visible maintainer response. |
| [#7874](https://github.com/agentscope-ai/QwenPaw/pull/7874) PawApp SDK/control-plane redesign | Open since 2026-09-19, XXXL | Architectural PR with no comment activity — risk of bit-rot and contributor attrition. |
| [#8033](https://github.com/agentscope-ai/QwenPaw/pull/8033), [#8027](https://github.com/agentscope-ai/QwenPaw/pull/8027), [#8034](https://github.com/agentscope-ai/QwenPaw/pull/8034) | Open since 2026-09-29 | Desktop backend reconciliation, blocking FS work in async handler, inline media body bounds — all small/medium, all stalled. |
| [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) embedding reindex incomplete | Open since 2026-09-30 | Only the "per-item fallback" half was addressed by #8062; the CJK token-budget truncation root cause remains. Recurrence of #5950. |
| [#7912](https://github.com/agentscope-ai/QwenPaw/issues/7912) MCP docs example | Open since 2026-09-21 | A contributor is offering documentation work; no maintainer green-light yet. |
| [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) nested skill dirs | Open since 2026-10-11, first-time contributor | Needs timely review to retain a new contributor on an XL change. |
| [#8123](https://github.com/agentscope-ai/QwenPaw/issues/8123) Daily Paper truncation failure | Open since 2026-10-08 | No per-paper retry; whole cron job fails on one bad model output. |

**Health note:** the just-closed [#4946](https://github.com/agentscope-ai/QwenPaw/issues/4946) sat open since 2026-06-03 — a reminder that high-severity startup-blocking issues can linger for months. The current 24h PR throughput is healthy, but the *review* queue (17 open PRs visible, several XL/XXXL) is the real constraint, and #7835 in particular deserves prioritization on privacy grounds.

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-10-27

## 1. Today's Overview

ZeroClaw recorded high inbound activity but almost no closure in the last 24 hours: 23 issues were updated (all 23 still open, 0 closed) and 29 PRs were touched (28 open, only 1 merged/closed). The day's activity is dominated by a coordinated security/identity-access sweep — eight issues (#12046–#12053) filed on 2026-10-26 by the same reporter covering gateway auth, OIDC flows, tool scoping, and URL fetching. A second cluster concerns context/token budgeting and memory correctness (#10780, #10781, #11711, #10921), which has been the project's most persistent theme for over a month. No release was published, though v0.8.7 release-candidate prep (#12019 pins translation catalogues to v0.8.7) and v0.9.0-tagged dashboard sign-in work are visibly in flight. Overall the project is healthy on contribution volume but constrained on review/merge throughput, producing a widening open-issue and open-PR backlog.

## 2. Project Progress

Only **1 of 29 updated PRs was merged/closed** in the window, and that PR is not identifiable from the provided top-20 sample; effectively there were no notable feature landings today. What *advanced* (updated, not merged):

| Area | PR | Status / Signal |
|---|---|---|
| Dashboard OIDC sign-in (v0.9.0) | [#11965](https://github.com/zeroclaw-labs/zeroclaw/pull/11965) | Opt-in `[oidc.<alias>]` dashboard login; moves dashboard from shared pairing-code operator to real identity |
| Scoped dashboard chat (v0.9.0) | [#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986) | Stacked on #11965/#11186/#11964; marked `needs-author-action` |
| OIDC enrollment correctness | [#11968](https://github.com/zeroclaw-labs/zeroclaw/pull/11968) | Form-encode Basic credentials per RFC 6749 §2.3.1 |
| Runtime placement docs | [#11963](https://github.com/zeroclaw-labs/zeroclaw/pull/11963) | Proposes five exception rows ahead of v0.9.0 sign-in/RPC changes |
| RPC authorization hardening | [#11289](https://github.com/zeroclaw-labs/zeroclaw/pull/11289), [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326), [#11234](https://github.com/zeroclaw-labs/zeroclaw/pull/11234), [#11230](https://github.com/zeroclaw-labs/zeroclaw/pull/11230) | Stable denial reasons, log-read gating, queued-session ownership, session-data tool withholding — all `release-gate`/`risk:high` |
| Runtime context controls | [#11910](https://github.com/zeroclaw-labs/zeroclaw/pull/11910), [#12044](https://github.com/zeroclaw-labs/zeroclaw/pull/12044) | Hard `max_system_prompt_chars` gate; opt-in deferred bootstrap sections |
| Channels | [#11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847) (XMPP, XL), [#12003](https://github.com/zeroclaw-labs/zeroclaw/pull/12003) (inbound-only email), [#11651](https://github.com/zeroclaw-labs/zeroclaw/pull/11651), [#11601](https://github.com/zeroclaw-labs/zeroclaw/pull/11601) (Telegram 429/transcription) |
| Plugin integrity | [#12040](https://github.com/zeroclaw-labs/zeroclaw/pull/12040) | Fix for #11562 manifest/component generation pairing |
| Web UI | [#12039](https://github.com/zeroclaw-labs/zeroclaw/pull/12039) | Fix for #12021 stale "unsaved change" highlight |
| Goal V1 proposals | [#11880](https://github.com/zeroclaw-labs/zeroclaw/pull/11880) (3/12), [#11882](https://github.com/zeroclaw-labs/zeroclaw/pull/11882) (5/12) | Explicitly draft: "final review: FAIL," not merge requests |

## 3. Community Hot Topics

**Issues (comment-ranked; PR comment counts were `undefined` in the dataset, so PRs are ranked by risk/size/labels):**

1. **Context/token economics — the dominant cluster (4 comments each)**
   - [#10780](https://github.com/zeroclaw-labs/zeroclaw/issues/10780) — proactive token-budget context compaction removed; `keep_recent`/`collapse_tool_results` inert. `priority:p1`, `status:in-progress`, `risk:high`.
   - [#10781](https://github.com/zeroclaw-labs/zeroclaw/issues/10781) — remove or implement inert `context_compression.*`, `history_pruning.keep_recent`, `collapse_tool_results`, `keep_tool_context_turns`.
   - [#11711](https://github.com/zeroclaw-labs/zeroclaw/issues/11711) — trimming a long conversation *permanently deletes* older turns from the SQLite session store.
   *Underlying need:* operators need predictable, bounded context cost for long-running agents, and they need trimming to be non-destructive. The fact that config keys silently do nothing (#10781) is a trust problem, not just a docs bug.

2. **Transport parity for persisted turns** — [#10673](https://github.com/zeroclaw-labs/zeroclaw/issues/10673) (4 comments, P1/S1). Failed/cancelled ACP turns are persisted for `session/prompt` but not for the daemon RPC path used by ZeroCode's Code pane; remaining slice of #9333. *Need:* identical reliability guarantees regardless of client/transport.

3. **Long-term memory recall correctness** — [#10921](https://github.com/zeroclaw-labs/zeroclaw/issues/10921) (4 comments). Qdrant `recall`/`recall_for_agents` pass `limit` to `/points/search` without `since`/`until`, so time-bounded recall can return fewer results than eligible. *Need:* time filters must be part of the search, not a post-filter.

4. **Plugin supply-chain integrity** — [#11562](https://github.com/zeroclaw-labs/zeroclaw/issues/11562) (3 comments, `risk:high`, `release:v0.8.7`). A `plugin update` publish landing between manifest read and component admission can pair one generation's manifest with another's binary. Fix PR [#12040](https://github.com/zeroclaw-labs/zeroclaw/pull/12040) is open.

5. **Coordination trackers and UX** — [#11013](https://github.com/zeroclaw-labs/zeroclaw/issues/11013) (2 comments) unified file intake/resolver/delivery batch tracker after RFC #9488; [#12021](https://github.com/zeroclaw-labs/zeroclaw/issues/12021) (2 comments) web config "Save all" leaves the group save button highlighted.

**PRs attracting maintainer/CI attention (by risk & size):** [#11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847) native XMPP + optional OMEMO (`size:XL`, `risk:high`); [#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986)/[#11965](https://github.com/zeroclaw-labs/zeroclaw/pull/11965) OIDC dashboard stack (`release:v0.9.0`, `risk:high`, `needs-author-action`); [#11230](https://github.com/zeroclaw-labs/zeroclaw/pull/11230) and [#11234](https://github.com/zeroclaw-labs/zeroclaw/pull/11234) (`size:XL`, `release-gate`, `priority:p1`); Goal V1 proposals [#11882](https://github.com/zeroclaw-labs/zeroclaw/pull/11882)/[#11880](https://github.com/zeroclaw-labs/zeroclaw/pull/11880) (`size:XL`, drafts).

## 4. Bugs & Stability

All issues in the window are open; **no bug was closed today**. Ranked by severity/impact:

| Rank | Issue | Severity | Impact | Fix status |
|---|---|---|---|---|
| 1 | [#10673](https://github.com/zeroclaw-labs/zeroclaw/issues/10673) ACP daemon RPC turns not persisted on failure | **S1 / P1** | Workflow blocked; lost failed-turn history in ZeroCode Code pane | No linked fix PR in sample; 4 comments, open since Sep 7 |
| 2 | [#10780](https://github.com/zeroclaw-labs/zeroclaw/issues/10780) no proactive token-budget compaction | S2 / P1 / `risk:high` | Unbounded context growth, cost blowups | `status:in-progress`, no fix PR listed; #12044 adjacent |
| 3 | [#12051](https://github.com/zeroclaw-labs/zeroclaw/issues/12051) `sop_workshop apply` lets scoped principal author SOPs that run with operator authority | S2 / security | Privilege escalation | No fix PR |
| 4 | [#12046](https://github.com/zeroclaw-labs/zeroclaw/issues/12046) wildcard tool selector retains principal-unaware tools (cron, schedule, peer messaging, SOP) | S2 / security | Privilege escalation for non-admin principals | Related PRs #11230/#11234 open |
| 5 | [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) limiters key on first `X-Forwarded-For` entry | S2 / security | Client behind appending proxy can claim loopback exemption → limiter bypass | No fix PR |
| 6 | [#12045](https://github.com/zeroclaw-labs/zeroclaw/issues/12045) response bodies buffered whole before size cap (`link_enricher`, WeChat, multimodal images) | S2 / security | Memory exhaustion via user/LLM-controlled URLs | No fix PR |
| 7 | [#12050](https://github.com/zeroclaw-labs/zeroclaw/issues/12050) OIDC callback token page has no browser binding | S2 / security | Login CSRF | No fix PR |
| 8 | [#12049](https://github.com/zeroclaw-labs/zeroclaw/issues/12049) 32-slot sign-in flow store exhaustible by 4 addresses, loopback included | S2 / security | Global sign-in denial of service | No fix PR |
| 9 | [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047) OIDC bearer verification lacks attempt limiter/outbound cap | S2 / security | Amplified outbound requests to IdP | No fix PR |
| 10 | [#12053](https://github.com/zeroclaw-labs/zeroclaw/issues/12053) unpinned third-party script on dashboard origin | S2 / security | Supply-chain XSS on dashboard origin | No fix PR |
| 11 | [#11562](https://github.com/zeroclaw-labs/zeroclaw/issues/11562) plugin manifest/component generation mismatch | S2 / `risk:high` | Runs component under replaced generation's manifest | **Fix PR [#12040](https://github.com/zeroclaw-labs/zeroclaw/pull/12040) open** |
| 12 | [#11711](https://github.com/zeroclaw-labs/zeroclaw/issues/11711) history trim permanently deletes older turns | S2 | Silent data loss in long sessions | No fix PR |
| 13 | [#10921](https://github.com/zeroclaw-labs/zeroclaw/issues/10921) Qdrant time-bounded recall omits eligible results | S2 | Degraded recall quality | No fix PR |
| 14 | [#12043](https://github.com/zeroclaw-labs/zeroclaw/issues/12043) `glob_search` drops authorized out-of-workspace matches; rejects absolute patterns under read-only roots | S2 | Tool misreports visibility both ways | No fix PR |
| 15 | [#12021](https://github.com/zeroclaw-labs/zeroclaw/issues/12021) config save button remains highlighted after Save all | S3 | UI state confusion | **Fix PR [#12039](https://github.com/zeroclaw-labs/zeroclaw/pull/12039) open** |
| 16 | [#11825](https://github.com/zeroclaw-labs/zeroclaw/issues/11825) `codex_cli` test empties process `PATH`, breaking parallel `cargo test` | S3 | CI/test isolation flake | No fix PR |

**Assessment:** the security cluster is unusually dense and appears to be a single reviewer's audit pass (#12046–#12053, all S2, all zero comments, all filed 2026-10-26). Most have **no accompanying fix PR yet**, which is the main stability risk entering v0.9.0. Only #11562 and #12021 have fixes in flight.

## 5. Feature Requests & Roadmap Signals

| Request | Link | Signal |
|---|---|---|
| Opt-in external rerank stage for memory recall (`/v1/rerank`, default off, fail-open to local path) | [#12022](https://github.com/zeroclaw-labs/zeroclaw/issues/12022) | Memory quality extensibility; low risk, plausible v0.9.x |
| Honor per-runtime-profile thinking levels for native OpenAI reasoning | [#12054](https://github.com/zeroclaw-labs/zeroclaw/issues/12054) | Multi-agent, single-provider differentiation; small scope |
| Validate OIDC `redirect_uri` against configured dashboard origin instead of Host header | [#12048](https://github.com/zeroclaw-labs/zeroclaw/issues/12048) | Security enhancement, pairs with #11965 stack |
| Gitea/Forgejo parity with GitHub forge channel | [#12041](https://github.com/zeroclaw-labs/zeroclaw/issues/12041) | Self-hosted/sovereign deployment demand |
| Unified file intake/resolver/projection/delivery (post-RFC #9488 tracker) | [#11013](https://github.com/zeroclaw-labs/zeroclaw/issues/11013) | Multi-channel file handling architecture |
| Implement or remove inert context config keys | [#10781](https://github.com/zeroclaw-labs/zeroclaw/issues/10781) | Cleanup tied to #10780 |

**Prediction for the next versions:**
- **v0.8.7 (imminent):** plugin admission fix (#11562/#12040), translation catalogue pin (#12019). Low-risk maintenance release.
- **v0.9.0:** OIDC dashboard sign-in + scoped dashboard chat (#11965, #11986, #11968, #11963), plus RPC/identity-access hardening (#11289, #11326, #11234, #11230) — all explicitly tagged `release:v0.9.0`/`release-gate`. The eight new security issues are likely to be folded into this hardening wave.
- **Post-v0.9.0:** a context-management rework addressing #10780/#10781/#11711, and the Goal V1 supervision proposals (#11880/#11882) once their review gates pass — currently marked "FAIL," so not near-term.

## 6. User Feedback Summary

- **Configuration trust erosion.** Users set `context_compression.*`, `history_pruning.keep_recent`, `collapse_tool_results` expecting token savings; nothing happens (#10781). Combined with #10780 and #11711, this is the strongest dissatisfaction signal: features appear present but are inert or destructive.
- **Data-loss anxiety in long sessions.** #11711 (trim rewrites the transcript, deleting older turns) is the most user-visible correctness risk for anyone running persistent gateway conversations.
- **Memory quality gaps.** #10921 (time-bounded recall omits eligible results) and #12022 (external rerank request) show users pushing memory from "works" toward "predictable and tunable."
- **UI polish matters.** #12021 shows small web-dashboard state bugs are noticed and filed promptly (fix PR within a day), suggesting an engaged dashboard user base.
- **Channel reliability.** Telegram users hit 429 mishandling (#11651) and terminal voice-transcription retry loops that block the long-poll offset (#11601) — both with fix PRs, but both `needs-author-action`.
- **Agent self-correction friction.** #11983 argues bare "Command not allowed by security policy" messages cause agents to retry variations instead of remediating; the fix adds reason + hint. This is a recurring theme: security refusals must be explainable to the model.
- **External validation.** [#12042](https://github.com/zeroclaw-labs/zeroclaw/issues/12042) is an unsolicited Hlido review scoring ZeroClaw **79/100**, with a right-of-reply open. Notable as third-party scrutiny of the project's autonomy/safety posture; also a signal that agent-facing review directories are now targeting the project.
- **Contributor experience.** #11825 (test mutating process-wide `PATH` breaking parallel `cargo test`) is a small but real friction point for contributors.

## 7. Backlog Watch

Items with the longest open age, highest severity, or explicit blocking labels that need maintainer attention:

| Age | Item | Why it needs attention |
|---|---|---|
| 50 days | [#10673](https://github.com/zeroclaw-labs/zeroclaw/issues/10673) P1/S1 | Transport-parity bug open since Sep 7, 4 comments, no fix PR — the oldest unresolved P1 in the window |
| 46 days | [#10780](https://github.com/zeroclaw-labs/zeroclaw/issues/10780) P1 | `status:in-progress` since Sep 11 with no implementation PR; blocks #10781 cleanup and #11711 correctness |
| 40 days | [#10921](https://github.com/zeroclaw-labs/zeroclaw/issues/10921) | Memory recall correctness, 4 comments, no owner visible |
| 37 days | [#11013](https://github.com/zeroclaw-labs/zeroclaw/issues/11013) | Implementation tracker for accepted RFC #9488; trackers rot without milestone assignment |
| 28 days | [#11234](https://github.com/zeroclaw-labs/zeroclaw/pull/11234) and [#11230](https://github.com/zeroclaw-labs/zeroclaw/pull/11230) | `priority:p1`, `release-gate`, `size:XL`, stacked on unmerged #11225 — review chain is the bottleneck |
| 27 days | [#11289](https://github.com/zeroclaw-labs/zeroclaw/pull/11289) | Carries `do-not-merge` while being `priority:p2`/`risk:high`; needs a decision, not just a label |
| 26 days | [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326) | `release-gate` fix for scoped principals reading persisted logs — security-relevant and stalled |
| 20/17 days | [#11601](https://github.com/zeroclaw-labs/zeroclaw/pull/11601), [#11651](https://github.com/zeroclaw-labs/zeroclaw/pull/11651) | Both `needs-author-action`; Telegram reliability fixes idling |
| 8 days | [#11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847) | `size:XL` XMPP channel — large surface needing reviewer bandwidth before it drifts from master |

**Process observation:** across the sample, 5+ PRs carry `needs-author-action` and 1 carries `do-not-merge`, while 0 issues and only 1 PR closed in 24h. The project's health constraint right now is **review/merge throughput and author follow-up**, not contribution volume — worth flagging in the next maintainer sync, particularly with an 8-issue security audit landing in a single day and v0.9.0 tagged work already stacked several PRs deep.

---

*Data caveats: the dataset lists 20 of 29 updated PRs; the single merged/closed PR is not among them, and PR comment counts were supplied as `undefined`, so PR "hotness" is inferred from labels (risk, size, release tags) rather than engagement. All issue comment counts are as reported in the sample.*

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*