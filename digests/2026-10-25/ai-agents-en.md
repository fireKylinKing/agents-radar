# OpenClaw Ecosystem Digest 2026-10-25

> Issues: 500 | PRs: 500 | Projects covered: 5 | Generated: 2026-10-25 00:29 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-10-25

> Data note: the supplied dataset covers 500 issues and 500 PRs updated in the last 24 hours. PR comment counts were not populated, so PR rankings below use labels, status, and stated impact rather than comment volume.

## 1. Today’s Overview

OpenClaw remains a very high-velocity project: 500 issues and 500 PRs were updated in the last 24 hours, with 92 issues closed and 259 PRs merged or closed. Two releases shipped — a patch release focused on replay-safe session recovery and an extended-stable/LTS gateway release with security and reliability backports. Activity is heavily weighted toward regression and stability work rather than net-new features, especially around plugin source capture, SQLite/state management, session recovery, and Windows update flows. Project health is mixed: maintainer throughput is strong and an LTS channel exists, but several P0/P1 regressions remain open, including the highest-comment issue in the dataset. The volume of `stale` and `needs-maintainer-review` labels suggests review bandwidth, not contributor interest, is the main bottleneck.

## 2. Releases

### v2026.10.7
- **Focus:** Replay-safe session recovery.
- **Noted changes:**
  - Interrupted sessions retain recovery provenance and unfinished tool outcomes.
  - Stopped conversations retire terminal recovery residue before accepting the next message.
  - Rejected Claude CLI drafts are kept out of final replies.
  - Additional large-change notes are truncated in the supplied data.
- **Breaking changes / migration:** No explicit breaking changes or migration steps were provided in the excerpt. Given the session-state and recovery changes, operators should validate interrupted-session and tool-outcome behavior after upgrade.
- Link: [OpenClaw releases](https://github.com/openclaw/openclaw/releases)

### v2026.9.34
- **Channel:** Gateway-only `extended-stable`, described as the current LTS equivalent.
- **Contents:** OpenClaw from the end of September 2026 plus critical security updates, reliability and performance fixes, and new model support.
- **Recommendation:** Appropriate for production/LTS users who prioritize stability over the 2026.10 feature line.
- Link: [OpenClaw releases](https://github.com/openclaw/openclaw/releases)

**Beta signal:** PR [#182951](https://github.com/openclaw/openclaw/pull/182951) references `v2026.10.10-beta.3`, indicating an active beta channel beyond the latest stable release listed here.

## 3. Project Progress

With 259 PRs merged or closed in the last 24 hours, progress was concentrated in reliability, performance, and UI collaboration:

- **Session/state performance:** [#182934](https://github.com/openclaw/openclaw/pull/182934) (closed) shared resident actor entries across projections, reducing duplicate decoded session entries.
- **Mention/Inbox architecture:** [#155419](https://github.com/openclaw/openclaw/pull/155419) (closed) moved Inbox database work off the Gateway thread; [#153812](https://github.com/openclaw/openclaw/pull/153812) (closed) added session mention notifications in the UI.
- **Packaging/update reliability:** [#182982](https://github.com/openclaw/openclaw/pull/182982) (closed) restored standalone helper launches for update, MCP, and file-transfer helpers after packaging.
- **CLI stall fix:** [#182984](https://github.com/openclaw/openclaw/pull/182984) (closed) avoided minutes-long SQLite `integrity_check` scans when a CLI opens an agent database already held by the Gateway.
- **Open P0 fix:** [#182951](https://github.com/openclaw/openclaw/pull/182951) routes `config set/patch/unset` through a running Gateway, fixing an exit-1 regression in `v2026.10.10-beta.3`.
- **Performance PR queue:** [#183034](https://github.com/openclaw/openclaw/pull/183034) reduces transcript turn DB round trips; [#183042](https://github.com/openclaw/openclaw/pull/183042) defers dormant Chat work during cold boot; [#182976](https://github.com/openclaw/openclaw/pull/182976) reuses validated entry facts for cold history accounting.
- **Channel reliability:** [#183028](https://github.com/openclaw/openclaw/pull/183028) fixes Telegram `ask_user` questions being dropped during progress-draft retirement.
- **Provider/runtime:** [#176567](https://github.com/openclaw/openclaw/pull/176567) fixes OpenAI realtime voice sockets bypassing the managed proxy on Bun; [#180599](https://github.com/openclaw/openclaw/pull/180599) keeps Code Mode run expiry on the monotonic clock.

**Theme:** The merged/open PR set shows a clear push toward reducing database round trips, improving cold-start UI behavior, and hardening session/mention delivery. Feature work is secondary to stabilizing the 2026.9/2026.10 regressions.

## 4. Community Hot Topics

### Most active issues
1. **[#160959 — P0: Gateway blocks for minutes while capturing large external plugins](https://github.com/openclaw/openclaw/issues/160959)** — 50 comments, crash-loop, `no-new-fix-pr`.
   - Since plugin generation capture landed in 2026.9.6, a Gateway with a large external/workspace plugin blocks its event loop for minutes at startup and when the prepared model runtime republishes.
   - **Underlying need:** Plugin capture must be asynchronous/bounded, with reuse and native-binary handling. This is the clearest subsystem-level regression in the dataset.

2. **[#164294 — P2: Gateway state DB WAL auto-checkpoint never completes](https://github.com/openclaw/openclaw/issues/164294)** — 30 comments.
   - WAL checkpoint is permanently blocked by an in-process reader; `checkpointedFrames=866` vs `logFrames=17091`.
   - **Underlying need:** SQLite reader lifecycle management and observable checkpoint diagnostics.

3. **[#159356 — P2: llama.cpp manager reports ready while embedding child exits](https://github.com/openclaw/openclaw/issues/159356)** — 29 comments.
   - Embedding requests return HTTP 500 after the child process exits; memory pressure is suspected but not confirmed.
   - **Underlying need:** process supervision and readiness semantics for local model children.

4. **[#157989 — P1: Plugin source capture rewrites 1.1–1.4 GB per CLI command and ~6.5 GB per Gateway start](https://github.com/openclaw/openclaw/issues/157989)** — 23 comments.
   - Severe SSD wear from repeated byte-copying and SHA-256 hashing of external plugins.
   - **Underlying need:** capture caching/reuse and exclusion of bundled native binaries.

5. **[#53408 — P2: Write/exec tool parameters silently dropped after long conversations](https://github.com/openclaw/openclaw/issues/53408)** — 15 comments, 2 👍, stale since March 2026.
   - After 15+ turns with heavy tool use, `write` and `exec` arrive with empty argument objects.
   - **Underlying need:** long-session tool-call serialization integrity — a long-standing trust issue.

### Active PRs worth watching
- [#182951 — P0: Route config mutations through a running Gateway](https://github.com/openclaw/openclaw/pull/182951)
- [#176567 — P1: OpenAI realtime voice proxy on Bun](https://github.com/openclaw/openclaw/pull/176567)
- [#182066 — perf(cron): reuse source validation at transcript target checks](https://github.com/openclaw/openclaw/pull/182066) — ready for maintainer look
- [#183034 — improve(sessions): reduce transcript turn DB round trips](https://github.com/openclaw/openclaw/pull/183034)

**Analysis:** Three of the top four issues (#160959, #157989, #162585) share the same plugin source-capture root cause. SQLite/state DB lifecycle issues form a second cluster (#164294, #164948, #175926, #181660). The community is disproportionately affected by a small number of subsystems, which makes targeted fixes high-leverage.

## 5. Bugs & Stability

### P0 / release-blocking
- **[#160959](https://github.com/openclaw/openclaw/issues/160959) — OPEN.** Gateway blocks for minutes capturing large external plugins; crash-loop; 50 comments. No fix PR tagged.
- **[#181660](https://github.com/openclaw/openclaw/issues/181660) — CLOSED.** SQLite “Session read batch cannot reenter an active SQLite writer admission” on 2026.10.6; P0 UX release blocker.
- **[#153426](https://github.com/openclaw/openclaw/issues/153426) — OPEN.** Curated `MEMORY.md`/`USER.md` roots silently and permanently excluded from bootstrap injection by the provenance ratchet; security/session-state; P0.
- **[#166634](https://github.com/openclaw/openclaw/issues/166634) — OPEN.** Late reclamation checkpoint failure leaves agent unavailable after crash recovery; P0.
- **[#182951](https://github.com/openclaw/openclaw/pull/182951) — OPEN PR.** Config mutations fail with exclusive-offline-state error whenever a Gateway is running; regression in `v2026.10.10-beta.3`; P0 fix PR open.

### P1
- **[#157989](https://github.com/openclaw/openclaw/issues/157989) — OPEN.** Plugin capture SSD wear; no fix PR.
- **[#181619](https://github.com/openclaw/openclaw/issues/181619) — CLOSED.** Codex app-server execution-budget timeout regression after 2026.10.5 → 2026.10.6; every primary turn failed and fell back silently.
- **[#135858](https://github.com/openclaw/openclaw/issues/135858) — CLOSED.** opencode-go local catalog snapshot did not project `provider.npm→api` override; broke `muse-spark-1.2-contributor` and omitted `mimo-v2.5`.
- **[#182476](https://github.com/openclaw/openclaw/issues/182476) — OPEN.** Windows update repair fails with `OPENCLAW_STATE_LEASE_LOST`; gateway readiness slow/unstable.
- **[#150822](https://github.com/openclaw/openclaw/issues/150822) — OPEN.** Native Codex children advertise OpenClaw tools without an execution handler; detached failure delivery also fails; security-sensitive.
- **[#154299](https://github.com/openclaw/openclaw/issues/154299) — OPEN.** Subagent completion-delivery final text silently drops on 2026.9.5; no queue entry or failure record.
- **[#167876](https://github.com/openclaw/openclaw/issues/167876) — OPEN.** Talk consultations lose caller authority after session setup; security/other.
- **[#148292](https://github.com/openclaw/openclaw/issues/148292) — OPEN.** Session auto-compaction single attempt with no timeout; 23-minute hangs.
- **[#158154](https://github.com/openclaw/openclaw/issues/158154) — OPEN.** Telegram subagent result announcements expire undelivered after 30 minutes; retries stall topic lanes for hours.
- **[#178300](https://github.com/openclaw/openclaw/issues/178300) — OPEN.** Settled-turn finalization after a 1-token `length` stop delivers false success.
- **[#178000](https://github.com/openclaw/openclaw/issues/178000) — CLOSED.** Session-state races trigger cross-provider model fallback as `reason=unknown`.

### P2 clusters
- **Plugin capture / Windows:** [#162585](https://github.com/openclaw/openclaw/issues/162585) (staging re-materializes plugin into itself, 290 MB → 612 MB+/24 min), [#157812](https://github.com/openclaw/openclaw/issues/157812) (Windows auto-update failure modes).
- **SQLite/state:** [#164294](https://github.com/openclaw/openclaw/issues/164294) (WAL checkpoint), [#164948](https://github.com/openclaw/openclaw/issues/164948) (`doctor --fix` self-deadlock, `busyTimeoutMs=0`), [#175926](https://github.com/openclaw/openclaw/issues/175926) (snapshot reclamation skips every cycle).
- **Session/tool integrity:** [#159789](https://github.com/openclaw/openclaw/issues/159789) (one failed native close permanently locks agent execution), [#157986](https://github.com/openclaw/openclaw/issues/157986) (Automations `agentTurn` `DataCloneError`), [#146902](https://github.com/openclaw/openclaw/issues/146902) (tool-set churn defeats prompt-cache reuse).
- **UI/UX:** [#164394](https://github.com/openclaw/openclaw/issues/164394) (WebChat transcript jitter), [#149912](https://github.com/openclaw/openclaw/issues/149912) (text rendered twice/thrice), [#165041](https://github.com/openclaw/openclaw/issues/165041) (internal context injection visible), [#137188](https://github.com/openclaw/openclaw/issues/137188) (stale progress card live spinner).
- **Provider/cache/cost:** [#161208](https://github.com/openclaw/openclaw/issues/161208) (heartbeats never reuse Anthropic prompt cache; ~$0.40–0.95 per heartbeat), [#162768](https://github.com/openclaw/openclaw/issues/162768) (Codex model discovery hardcodes `client_version=0.158.0`), [#144797](https://github.com/openclaw/openclaw/issues/144797) (reused `claude-cli` session keeps deleted `--mcp-config`, tools die with 401).
- **Security:** [#158966](https://github.com/openclaw/openclaw/issues/158966) (credentialed CDP WebSocket URLs reach model-facing tab results).
- **Platform:** [#154104](https://github.com/openclaw/openclaw/issues/154104) (idle Matrix E2EE ~50% CPU, ~52 MB/min writes), [#153622](https://github.com/openclaw/openclaw/issues/153622) (macOS Peekaboo transient UUID snapshot).

**Fix PRs in flight:** [#183052](https://github.com/openclaw/openclaw/pull/183052) (agent DB integrity check), [#183002](https://github.com/openclaw/openclaw/pull/183002) (monotonic clock across clock jumps), [#183028](https://github.com/openclaw/openclaw/pull/183028) (Telegram questions), [#180850](https://github.com/openclaw/openclaw/pull/180850) (doctor malformed UTF-8), [#183000](https://github.com/openclaw/openclaw/pull/183000) (PostgreSQL config reporting), [#183015](https://github.com/openclaw/openclaw/pull/183015) (PostgreSQL limited ordered reads). The top P0 issue #160959 still lacks a fix PR.

## 6. Feature Requests & Roadmap Signals

- **[#182712 — Limit-aware model fallback](https://github.com/openclaw/openclaw/issues/182712)** (P2, 7 comments): skip candidates in an exhausted provider quota pool and prefer a different provider. Strong candidate for near-term model-routing work.
- **[#155633 — Add Databricks Unity Gateway as an official model provider](https://github.com/openclaw/openclaw/issues/155633)** (P2): implementation PR [#155634](https://github.com/openclaw/openclaw/pull/155634) exists. Enterprise provider expansion is likely.
- **[#139477 — Honor `tools.toolSearch` for bundle-MCP CLI backends](https://github.com/openclaw/openclaw/issues/139477)** (P2, diamond lobster): loopback MCP config hardcodes `alwaysLoad: true`, eagerly expanding every tool schema. Directly relevant to prompt/token efficiency.
- **[#150598 — Completion-wake delivery semantics](https://github.com/openclaw/openclaw/issues/150598)** (P2): field-data proposal for terminal-only, new-turn, coalesced, and failure-as-triage delivery in multi-session orchestration.
- **[#141004 — Record observed runtime skill usage](https://github.com/openclaw/openclaw/pull/141004)** (open PR since Sept 7): durable audit surface for skill reads/invocations.
- **[#137613 — Pre-compaction memory flush on CLI backends](https://github.com/openclaw/openclaw/issues/137613)** (P2, diamond lobster): memory flush is gated off for `claude-cli`, so CLI-backed sessions lose durable notes before compaction.
- **[#96675 — Owner-signed responsibility gates for memory, actions, skills, evidence reuse](https://github.com/openclaw/openclaw/issues/96675)** (closed enhancement): signals continued demand for explicit user confirmation before persistent memory/action reuse.
- **[#153812 — Show session mention notifications](https://github.com/openclaw/openclaw/pull/153812)** (closed): UI collaboration feature likely to appear in a near-term release.

**Prediction:** The next release likely emphasizes session recovery/provenance refinements, config routing through a running Gateway, plugin-capture performance, limit-aware model fallback, and mention/Telegram delivery reliability. New model support is already flowing through the extended-stable channel.

## 7. User Feedback Summary

**Main pain points**
- **Operational cost and hardware wear:** Plugin source capture causes GB-scale writes per command and per Gateway start ([#157989](https://github.com/openclaw/openclaw/issues/157989), [#162585](https://github.com/openclaw/openclaw/issues/162585)). Matrix E2EE idles at ~50% CPU and ~52 MB/min writes ([#154104](https://github.com/openclaw/openclaw/issues/154104)).
- **Update/install fragility on Windows:** Repeated auto-update failures, unexpanded `$OPENCLAW_STATE_DIR`, abandoned update records, and lease loss during repair ([#157812](https://github.com/openclaw/openclaw/issues/157812), [#182476](https://github.com/openclaw/openclaw/issues/182476)).
- **Session reliability and message loss:** Agent execution locks after a failed native close ([#159789](https://github.com/openclaw/openclaw/issues/159789)); subagent final text drops ([#154299](https://github.com/openclaw/openclaw/issues/154299)); Telegram announcements expire ([#158154](https://github.com/openclaw/openclaw/issues/158154)); crash recovery can leave an agent unavailable ([#166634](https://github.com/openclaw/openclaw/issues/166634)).
- **Trust/security concerns:** Curated memory files can be silently excluded forever ([#153426](https://github.com/openclaw/openclaw/issues/153426)); credentialed CDP URLs reach model-facing output ([#158966](https://github.com/openclaw/openclaw/issues/158966)); Talk calls lose caller authority ([#167876](https://github.com/openclaw/openclaw/issues/167876)).
- **Cost visibility:** Heartbeats never reuse Anthropic prompt cache, costing roughly $0.40–0.95 each ([#161208](https://github.com/openclaw/openclaw/issues/161208)).
- **UI polish:** WebChat transcript jitter ([#164394](https://github.com/openclaw/openclaw/issues/164394)), duplicate/thrice rendering ([#149912](https://github.com/openclaw/openclaw/issues/149912)), visible internal context injection ([#165041](https://github.com/openclaw/openclaw/issues/165041)), stale progress spinners ([#137188](https://github.com/openclaw/openclaw/issues/137188)).

**Use cases represented**
- Heavy multi-session orchestration with subagents, long-running background jobs, and coordinator sessions.
- Telegram, Matrix, and QQ channel deployments, including forum topics and E2EE.
- CLI-backed models (`claude-cli`, Codex app-server) and local embedding stacks (`llama.cpp`).
- Windows desktop/server installs and npm global deployments.
- Large external plugin trees with native binaries.

**Satisfaction/dissatisfaction:** Users are providing detailed, high-quality reproductions and operational workarounds, which indicates strong engagement. Dissatisfaction is concentrated in regressions introduced during the 2026.9.x line, Windows update reliability, and long-lived stale issues. The extended-stable/LTS release is a positive signal for production users.

## 8. Backlog Watch

These items are important but have gone long without resolution or clear maintainer ownership:

- **[#53408 — Write/exec parameters silently dropped after long conversations](https://github.com/openclaw/openclaw/issues/53408)** — opened 2026-03-24; 15 comments, 2 👍; stale. The oldest high-impact tool-integrity issue in the dataset.
- **[#141172 — Run dies before first session commit leaks `agent.run.started` forever](https://github.com/openclaw/openclaw/issues/141172)** — opened 2026-09-07; stale; trajectory flush FK failures and missing `agent.run.finished`.
- **[#137613 — Pre-compaction memory flush disabled on CLI backends](https://github.com/openclaw/openclaw/issues/137613)** — opened 2026-09-03; diamond lobster; 10 comments; durable-memory loss risk.
- **[#146902 — Same-session tool-set churn defeats prompt-cache reuse](https://github.com/openclaw/openclaw/issues/146902)** — opened 2026-09-13; 11 comments; direct cost/performance impact.
- **[#149631 — PR preflight throws at 121 jobs against 120 Node matrix cap](https://github.com/openclaw/openclaw/issues/149631)** — opened 2026-09-16; CI gate fails before tests start.
- **[#150598 — Completion-wake delivery semantics proposal](https://github.com/openclaw/openclaw/issues/150598)** — opened 2026-09-17; product-decision needed.
- **[#148292 — Auto-compaction 23-minute hangs](https://github.com/openclaw/openclaw/issues/148292)** — opened 2026-09-14; P1; session-state.
- **[#154104 — Idle Matrix E2EE ~50% CPU and ~52 MB/min writes](https://github.com/openclaw/openclaw/issues/154104)** — opened 2026-09-20; operational cost.
- **[#145937 — QQ channel left without a fully working plugin on 2026.9.x](https://github.com/openclaw/openclaw/issues/145937)** — opened 2026-09-12; first-party plugin crashes on inbound; successor receives zero chunks.
- **[#144976 — Gateway auto-restarts after slow explicit `stop` on systemd](https://github.com/openclaw/openclaw/issues/144976)** — opened 2026-09-11; lifecycle correctness.
- **[#141004 — Audit observed runtime skill usage](https://github.com/openclaw/openclaw/pull/141004)** — open PR since 2026-09-07; observability gap.
- **[#155633 — Databricks Unity Gateway provider](https://github.com/openclaw/openclaw/issues/155633)** / **[#155634](https://github.com/openclaw/openclaw/pull/155634)** — opened 2026-09-22; enterprise provider request with implementation already open.
- **[#180850](https://github.com/openclaw/openclaw/pull/180850)** and **[#183002](https://github.com/openclaw/openclaw/pull/183002)** — marked `needs proof`; small but relevant reliability fixes awaiting validation.

**Overall health assessment:** OpenClaw is highly active with strong PR throughput and a credible LTS process, but the project is currently paying down a significant regression debt in plugin capture, SQLite/state lifecycle, session recovery, and Windows updates. The highest-leverage action is a coordinated fix for the plugin source-capture cluster (#160959, #157989, #162585), followed by SQLite WAL/reader lifecycle cleanup and closing the P0 memory-provenance gap (#153426).

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — Personal AI Assistant / Agent OSS Ecosystem

**Window:** 2026-10-25 community digests. **Projects:** OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw.  
**Health score:** analyst composite (0–10) weighting release cadence, issue/PR throughput, regression/security burden, review backlog, and process maturity. Not a formal benchmark.

---

## 1. Ecosystem Overview

The personal AI assistant / agent OSS landscape is entering a reliability-and-governance phase: projects are shipping fewer net-new capabilities and spending more effort on session recovery, state-store lifecycle, plugin governance, delivery guarantees, and upgrade safety. OpenClaw remains the scale leader with a gateway-centric architecture and an LTS channel, while Hermes Agent, ZeroClaw, and QwenPaw occupy narrower but credible positions around desktop sessions, SOP/security workflows, and multi-agent governance respectively. Across projects, community feedback is dominated by silent failures, data durability, plugin/extension trust, platform-specific update breakage, and cost/latency visibility. IronClaw is inactive in this window. The strongest differentiator is no longer model access; it is operational trust — whether sessions, memory, plugins, channels, and upgrades behave predictably under production load.

---

## 2. Activity Comparison

All counts are items updated in the 24h window unless noted.

| Project | Issues count | PR count | Release status | Health score |
|---|---:|---:|---|---:|
| **OpenClaw** | 500 updated; 92 closed | 500 updated; 259 merged/closed | Two releases: **v2026.10.7** stable + **v2026.9.34** extended-stable/LTS; beta signal **v2026.10.10-beta.3** | **7.0 / 10** |
| **Hermes Agent** | 50 updated; 43 open, ~7 closed | 50 updated; 41 open, 9 merged/closed (~18%) | **No release** in window; field versions v0.22.5 / v0.22.4 / v0.22.2 | **6.0 / 10** |
| **IronClaw** | 0 | 0 | No activity | **N/A — inactive** |
| **QwenPaw** | 4 updated; 3 open, 1 closed | 11 updated; 10 open, 1 closed **without merge** | **No release**; latest field-reported v2.2.1 | **5.5 / 10** |
| **ZeroClaw** | 18 updated; 6 closed | 50 updated; 47 open, 3 merged/closed | **No published release**, but v0.8.7 version-bump PR closed; release imminent | **6.5 / 10** |

**Readout:** OpenClaw operates at roughly 10× Hermes’s and ~15× ZeroClaw’s updated-item volume, and ~66× QwenPaw’s. Hermes and ZeroClaw are active but review-bound. QwenPaw has engaged production users but very low landing throughput. IronClaw provides no signal.

---

## 3. OpenClaw’s Position

**Advantages vs peers**
- **Scale and throughput:** 1,000 updated issues/PRs in 24h, 259 PRs merged/closed, and 92 issues closed. This is an order of magnitude above Hermes and ZeroClaw and far above QwenPaw.
- **Release maturity:** Only project with a visible stable + LTS/extended-stable process and an active beta line. Hermes and QwenPaw shipped nothing; ZeroClaw’s v0.8.7 is prepared but unpublished.
- **Breadth:** Gateway, providers, plugins, channels, session recovery, Windows update flows, SQLite state management, local embeddings, and command-line tooling all active in one ecosystem.
- **Ecosystem gravity:** Large external plugin trees, multi-session orchestration, Telegram/Matrix/QQ deployments, CLI-backed models, and Windows/npm installs are all represented in feedback.

**Weaknesses vs peers**
- **Regression debt:** P0 plugin source-capture blockage (#160959, 50 comments) and P1 SSD-wear capture (#157989) are high-leverage unresolved clusters; #160959 has no fix PR.
- **Review bandwidth:** `stale` and `needs-maintainer-review` labels indicate review capacity, not contributor interest, is the bottleneck.
- **SQLite/state and Windows update fragility:** Several P0/P1 issues remain around WAL checkpointing, reader lifecycle, crash recovery, and `OPENCLAW_STATE_LEASE_LOST`.

**Technical approach differences**
- OpenClaw is **gateway-centric**: a long-running Gateway owns config, sessions, state DB, plugin capture, and channel routing. Recent work routes config mutations through a running Gateway and improves replay-safe session recovery.
- Peers are more narrowly scoped: Hermes is Desktop/session-lifecycle-heavy; ZeroClaw is Rust/Tauri with SOP, policy, and memory backends; QwenPaw is governance/audit and multi-agent orchestration-heavy.
- OpenClaw’s LTS channel gives it a production-adoption advantage that no peer currently matches in this dataset.

---

## 4. Shared Technical Focus Areas

| Focus area | Projects showing demand | Specific requirements emerging |
|---|---|---|
| **Session/state lifecycle and recovery** | OpenClaw, Hermes, QwenPaw, ZeroClaw | Replay-safe recovery, interrupted-session provenance, deterministic session close, shutdown-flush recovery, attributable cancellation, append-only session events. OpenClaw has SQLite WAL/reader lifecycle issues; Hermes has session-identity/title collisions and unbounded DM sessions; QwenPaw has silent agent-to-agent cancellation; ZeroClaw is exploring canonical session event history. |
| **Silent-failure elimination / observability** | QwenPaw, Hermes, OpenClaw, ZeroClaw | Config mutations must be logged; audit logging must not die silently; cancellations must record initiator/reason; model fallback must not be silent; policy refusals should name the rule; health checks must not false-positive. |
| **Memory durability and trust** | ZeroClaw, OpenClaw, QwenPaw | ZeroClaw has S0 concurrent-write data loss in Markdown memory; OpenClaw has memory-provenance exclusion and pre-compaction flush gaps; QwenPaw has memory-recall trace leakage and a ReMe upgrade. Shared need: non-destructive long-term recall, provenance, consent gates, and compaction safety. |
| **Plugin governance and extension safety** | Hermes, OpenClaw, ZeroClaw | Hermes needs catalog admission rules and verified tiers; OpenClaw needs bounded/async plugin source capture and native-binary handling; ZeroClaw needs orphaned plugin-install cleanup. Shared need: plugin API policy, sandboxing, admission review, and extension-surface decisions. |
| **Update/install reliability** | OpenClaw, Hermes, QwenPaw/ZeroClaw indirectly | OpenClaw: Windows lease loss and slow gateway readiness. Hermes: ~1GB `state.db` blocks update via fixed 30s timeout; Windows update leaves gateway restart unaccounted. Shared need: upgrade paths that scale with user data and are atomic across platforms. |
| **Channel delivery guarantees** | OpenClaw, Hermes, QwenPaw, ZeroClaw | Telegram announcements expire; Hermes Bot Chat delivery refuses on title collision and clarify answers drop on reconnect; QwenPaw needs Matrix E2EE recovery and QQ streaming; ZeroClaw is adding email attachments/XMPP. Shared need: deterministic delivery, retry semantics, reconnect handling, attachment parity. |
| **Security and supply-chain hardening** | ZeroClaw, Hermes, OpenClaw, QwenPaw | ZeroClaw closed RUSTSEC/CA-bundle issues; Hermes fixed redaction and MCP toolset widening; OpenClaw has credential-bearing CDP URL leakage and caller-authority issues; QwenPaw has cross-channel memory-trace leakage. Shared need: secret redaction, permission boundaries, dependency advisories, channel-safe output. |
| **Cost/latency/prompt efficiency** | OpenClaw, Hermes, ZeroClaw | OpenClaw: prompt-cache reuse failures, heartbeat cost, tool-set churn. Hermes: prompt-token trimming PRs. ZeroClaw: context-window fallback to 32k rejecting valid turns. Shared need: cache-aware prompt construction, model metadata normalization, limit-aware routing. |
| **Multi-agent orchestration semantics** | OpenClaw, Hermes, QwenPaw, ZeroClaw | OpenClaw: subagent delivery and completion-wake semantics. Hermes: kanban spawn duplicate side effects. QwenPaw: multi-agent fleet cancellation attributable to initiator. ZeroClaw: SOP run integrity. Shared need: idempotent side effects, failure triage, and terminal-state delivery contracts. |

---

## 5. Differentiation Analysis

| Project | Feature focus | Target users | Technical architecture / approach |
|---|---|---|---|
| **OpenClaw** | General-purpose agent gateway, plugins, providers, channels, session recovery, LTS | Production operators, plugin ecosystem developers, multi-channel deployments | Gateway-centric; SQLite/state DB; plugin source capture; session recovery/provenance; config routing through running Gateway; stable + LTS + beta channels |
| **Hermes Agent** | Desktop session visibility/lifecycle, Windows install/update, gateway delivery, plugin catalog governance | Desktop-first personal assistant users, multi-session users, plugin integrators | Desktop + gateway; plugin catalog with verification tiers; prompt-slimming; session/title identity model still under design |
| **IronClaw** | No observable activity | Insufficient data | Insufficient data |
| **QwenPaw** | Multi-agent orchestration, governance/audit, team/self-hosted defaults, Matrix/QQ/Feishu channels | Self-hosted teams, multi-agent fleets, compliance-conscious deployments | `policy.yaml` + `governance/audit.db`; hooks; channel integrations; ReMe memory upgrade; Console defaults |
| **ZeroClaw** | SOP engine, security/supply chain, memory correctness, shell policy, email/XMPP channels | Security-conscious self-hosters, workflow/SOP operators, Windows shell automation | Rust/Tauri workspace; policy parser; SOP engine; memory backends; RUSTSEC/CA hygiene; release-stabilization mode |

**Key contrast:** OpenClaw is the broad platform play; Hermes is desktop/session-experience-led; ZeroClaw is security/workflow-led; QwenPaw is multi-agent/governance-led. OpenClaw’s architecture is the most centralized and production-oriented; ZeroClaw’s is the most security/control-plane-oriented; QwenPaw’s is the most compliance/audit-oriented; Hermes’s is the most desktop-ergonomics-oriented.

---

## 6. Community Momentum & Maturity

| Tier | Projects | Momentum assessment | Maturity assessment |
|---|---|---|---|
| **Tier 1 — Hyperscale / release-led** | **OpenClaw** | Very high velocity; two releases; active beta; 259 PRs merged/closed | Most mature release process and LTS posture, but significant regression debt and review backlog. Rapidly iterating while paying down stability debt. |
| **Tier 2 — Active but review-bound** | **Hermes Agent**, **ZeroClaw** | Hermes: 100 items, 18% PR close rate, no release. ZeroClaw: 68 items, 6 issues closed, release imminent, strong security responsiveness. | Hermes needs design rulings on session identity and plugin extension surface. ZeroClaw has a credible release train but a 47-open/3-closed PR ratio and S0 memory data-loss exposure. |
| **Tier 3 — Low throughput, engaged users** | **QwenPaw** | 15 items, effectively zero code landed, 1 PR closed unmerged. | Production-like users and detailed reports, but review bandwidth is the bottleneck; silent-failure bugs and governance audit failure are trust risks. |
| **Dormant** | **IronClaw** | No activity | N/A |

**Momentum readout:** OpenClaw is rapidly iterating and stabilizing simultaneously. ZeroClaw is in release-stabilization mode with strong security turnaround. Hermes is in high-throughput triage but needs two architectural decisions. QwenPaw has community goodwill but low merge throughput. IronClaw is inactive.

---

## 7. Trend Signals

| Trend | Evidence across projects | Value for AI agent developers |
|---|---|---|
| **Reliability is the new feature** | Session recovery, WAL/reader lifecycle, replay-safe events, shutdown-flush recovery, atomic updates across OpenClaw/Hermes/ZeroClaw/QwenPaw | Invest early in idempotent side effects, replayable session logs, state-store lifecycle management, and upgrade-safe migrations. |
| **Silent failure is unacceptable** | QwenPaw config reset/audit stop/cancel without reason; Hermes silent model fallback and undelivered clarify; OpenClaw false success and visible internal context; ZeroClaw opaque policy refusals | Build structured audit events, user-visible diagnostics, mutation logging, and explicit failure contracts into the agent runtime. |
| **Memory must be durable and governable** | ZeroClaw S0 Markdown-memory data loss; OpenClaw provenance exclusion and pre-compaction flush; QwenPaw memory-trace leakage | Treat memory as a transactional subsystem: concurrency control, provenance, consent gates, compaction, and cross-channel output hygiene. |
| **Plugin ecosystems need governance before scale** | Hermes catalog admission/verified tiers; OpenClaw plugin capture performance; ZeroClaw plugin-install cleanup | Define plugin API boundaries, admission policy, sandboxing, extension-surface decisions, and capture/caching behavior early. |
| **Update/install reliability is a trust gate** | Hermes `state.db` preflight blocks update; OpenClaw Windows lease/update failures; Windows regressions in both | Test upgrades with realistic user data volumes and platform-specific installers; make update paths atomic and resumable. |
| **Channel delivery is becoming product-critical** | Telegram expiration, Bot Chat title collisions, Matrix E2EE recovery, QQ streaming, email attachments, XMPP | Abstract transport with explicit delivery semantics: retry, reconnect, dedupe, attachment parity, and terminal-state acknowledgement. |
| **Cost/latency optimization is moving into core** | OpenClaw prompt-cache misses and heartbeat cost; Hermes prompt trimming; ZeroClaw context-window metadata fallback | Build cache-aware prompt assembly, model metadata normalization, limit-aware fallback, and token/cost observability into routing. |
| **Security hardening spans supply chain and runtime** | RUSTSEC/CA bundles in ZeroClaw; redaction/MCP widening in Hermes; CDP credential leakage and caller authority in OpenClaw; audit/governance in QwenPaw | Treat dependency advisories, secret redaction, permission boundaries, and audit logs as first-class agent-platform features. |
| **Multi-agent semantics need contracts** | OpenClaw subagent delivery; Hermes kanban duplicate callbacks; QwenPaw fleet cancellation attribution; ZeroClaw SOP integrity | Define cancellation attribution, side-effect idempotency, completion delivery semantics, and failure-as-triage behavior before scaling agent fleets. |

**Bottom line:** OpenClaw is the ecosystem’s scale and release-process leader, but its next quality step depends on resolving the plugin-capture and SQLite/state clusters. Hermes and ZeroClaw are credible Tier-2 projects with strong user engagement but review-bound roadmaps and unresolved foundational design questions. QwenPaw has serious production users but must improve merge throughput and silent-failure observability. Across all active projects, the winning agent platforms will be those that make sessions, memory, plugins, channels, and upgrades **observable, recoverable, and trustworthy** — not merely feature-rich.

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest, 2026-10-25

**Data scope note:** Figures cover items updated in the last 24h. Issue comment counts and reactions are as reported; PR comment counts were not available (`undefined`), so PRs are ranked by recency/impact rather than engagement. Only the top 30 of 50 issues and top 20 of 50 PRs are itemized, so totals below are partial.

---

## 1. Today's Overview

Hermes Agent is in a high-throughput maintenance phase: **100 items touched in 24h** (50 issues, 50 PRs) with **zero releases**, indicating sustained triage and review rather than a release push. The backlog remains open-heavy — **43/50 updated issues are still open** and **41/50 PRs remain open**, with only 9 PRs (18%) merged or closed. Activity clusters around four themes: **Desktop session state/lifecycle**, **Windows install-update reliability**, **gateway message delivery and session isolation**, and **plugin-catalog governance**. Five notable closures landed (two security fixes, two Windows stability fixes, one feature request), while a large plugin hook PR was closed after a `needs-decision` hold. Overall health is **mixed-positive**: maintainer responsiveness and external contribution are strong, but a growing tail of P2 Desktop/Windows defects and long-lived P3 features points to accumulating stabilization debt.

---

## 2. Releases

**No new releases in the last 24h.** The most-referenced version in incoming reports is **v0.22.5** (with v0.22.4/v0.22.2 still in the field), so the install-update bugs below are live for current users.

---

## 3. Project Progress

**Closed issues (5 of 7 visible):**

| Item | Type | What it resolves |
|---|---|---|
| [Issue #142576](https://github.com/NousResearch/hermes-agent/issues/142576) | Security | Redaction now recognizes Context7 `ctx7sk-` keys; bare keys no longer pass unmasked |
| [Issue #141468](https://github.com/NousResearch/hermes-agent/issues/141468) | Security | Cron `enabled_toolsets: mcp-<name>` no longer silently widens jobs to **every** enabled MCP server |
| [Issue #142528](https://github.com/NousResearch/hermes-agent/issues/142528) | Windows stability | Abandoned pm-runtime lease files are pruned (1,546 had accumulated); Python delete hook no longer forces `hermes cron`/`gateway` to exit 1 |
| [Issue #142515](https://github.com/NousResearch/hermes-agent/issues/142515) | Windows CLI | `hermes gateway restart` writes the planned-restart marker, restoring the "Gateway online" notice |
| [Issue #144071](https://github.com/NousResearch/hermes-agent/issues/144071) | Feature request | Opt-in plugin replies to approval/clarify requests — closed without a shipped feature |

**Merged/closed PRs (3 of 9 visible):**

- [PR #144203](https://github.com/NousResearch/hermes-agent/pull/144203) — **CoreWeave Inference tier set to `verified`** in the plugin catalog (ownership, non-fork, domain-matched commits validated). Catalog-only change; establishes a repeatable verification rubric.
- [PR #144174](https://github.com/NousResearch/hermes-agent/pull/144174) — **Skills index cleanup** (salvage of #130552): removes category blurbs, duplicate names, and mid-word fragments from the `<available_skills>` system-prompt block — directly reduces prompt bloat.
- [PR #134556](https://github.com/NousResearch/hermes-agent/pull/134556) — **`pre_send_message` gateway plugin hook closed** under `needs-decision`. This is the most consequential closure today: a generic outbound-message rewrite/drop hook would have unblocked cron, kanban, and approval-channel customization. Its closure signals the maintainers are **not yet ready to commit to that extension surface**.

**Net progress:** security hardening (2 fixes), Windows operational stability (2 fixes), catalog trust model (1), prompt efficiency (1). No new user-facing capability shipped.

---

## 4. Community Hot Topics

**1. [Issue #50718](https://github.com/NousResearch/hermes-agent/issues/50718) — Desktop session visibility & notifications** (12 comments, 3 👍, highest engagement in the dataset)
Users cannot tell which session is working, done, or blocked. The report decomposes this into three independent root causes: no unread cue when a background turn completes, no needs-input signal, and no OS-level badge. **Underlying need:** sessions must become first-class, observable objects — the UI is currently optimized for a single foreground chat while real usage is multi-session and backgrounded.

**2. [Issue #133253](https://github.com/NousResearch/hermes-agent/issues/133253) — Desktop update blocked by 30s `state.db` pre-flight** (8 comments, labeled `duplicate`)
A ~1GB `state.db` cannot pass a hard-coded `spawnSync` timeout, so "Update now" cancels before touching anything. **Underlying need:** upgrade paths must scale with accumulated user data; a fixed timeout is a latent failure for every long-term user.

**3. [Issue #48997](https://github.com/NousResearch/hermes-agent/issues/48997) — Explicitly close abandoned runtime sessions and owned backends** (6 comments)
New Chat, `/new`, archive, delete, closing a secondary window, and quit all leave runtime sessions and backends alive. **Underlying need:** a defined session lifecycle with deterministic resource reclamation — the same gap that surfaces as unbounded DM growth in [#136012](https://github.com/NousResearch/hermes-agent/issues/136012).

**4. [Issue #143053](https://github.com/NousResearch/hermes-agent/issues/143053) — Plugin catalog admission guidance** (4 comments)
A third-party maintainer of a complete workflow/source bundle cannot find a supported admission route — explicitly *not* asking to bypass the install scanner. **Underlying need:** the plugin ecosystem has grown faster than its published governance; the CoreWeave verification PR (#144203) is the same problem seen from the maintainer side.

**5. [Issue #143715](https://github.com/NousResearch/hermes-agent/issues/143715) — Bot Chat delivery refused `not_found`** (4 comments)
A canonical `Bot Chat` title held by an unbound `tui`-source row permanently blocks profile delivery. **Underlying need:** title/session-key collisions need a resolution rule, not a fail-closed refusal.

**6. [Issue #142839](https://github.com/NousResearch/hermes-agent/issues/142839) — `model.default` slash prefix forwarded verbatim → 503 + silent fallback** (4 comments)
`<provider>/<model>` is passed upstream unchanged when `model.provider` already names the provider, producing `model_not_found` and an invisible downgrade. **Underlying need:** configuration normalization plus **no silent fallback** — users must know when their chosen model wasn't used.

**7. [Issue #18964](https://github.com/NousResearch/hermes-agent/issues/18964) — Reticulum/LXMF message gateway** (4 comments, 3 👍)
Longest-running request in the visible set (opened 2026-05-02): a link-agnostic, serverless transport. **Underlying need:** deployment in disconnected/off-grid or censorship-resistant environments.

**8. [Issue #136189](https://github.com/NousResearch/hermes-agent/issues/136189) — P1: shutdown flush files written under the wrong session key** (4 comments)
Messages queued at shutdown are keyed by adapter isolation flags while recovery resolves them via gateway-wide flags, so **restart recovery can never map them** when the flags differ. This is the highest-severity delivery-guarantee defect in the dataset.

---

## 5. Bugs & Stability

**Severity ranking (P1 → P2), with fix status:**

| Sev | Issue | Failure mode | Fix PR? |
|---|---|---|---|
| **P1** | [#136189](https://github.com/NousResearch/hermes-agent/issues/136189) | Queued messages unrecoverable across restart when adapter vs. gateway `group_sessions_per_user` flags differ — **permanent message loss** | None visible |
| P2 | [#133253](https://github.com/NousResearch/hermes-agent/issues/133253) | Desktop update impossible with ~1GB `state.db` (30s pre-flight) | None; labeled `duplicate` |
| P2 | [#136012](https://github.com/NousResearch/hermes-agent/issues/136012) | Gateway DM sessions grow unbounded; `session_reset` idle/at_hour has **no consumer** in routing | None visible |
| P2 | [#143715](https://github.com/NousResearch/hermes-agent/issues/143715) | Bot Chat delivery permanently refused (`not_found`) on title collision | None visible |
| P2 | [#142839](https://github.com/NousResearch/hermes-agent/issues/142839) | Provider-prefixed model string → 503 → **silent fallback** | None visible |
| P2 | [#133026](https://github.com/NousResearch/hermes-agent/issues/133026) | `config set agent.reasoning_effort none` saves `null`, leaving default effort (overrides/MoA slots too) | Partial: #92468 covers top-level key only |
| P2 | [#144140](https://github.com/NousResearch/hermes-agent/issues/144140) | Dashboard lists `serve`/Desktop sessions it cannot open (`not_found`, empty transcript) | None visible |
| P2 | [#144181](https://github.com/NousResearch/hermes-agent/issues/144181) | Sole non-retryable startup adapter conflict ignores `on_all_adapters_down: stay_alive`, exits 78 → **whole-process restart loop over one logged-out platform** | ✅ [PR #144198](https://github.com/NousResearch/hermes-agent/pull/144198) |
| P2 | [#144158](https://github.com/NousResearch/hermes-agent/issues/144158) / [#144159](https://github.com/NousResearch/hermes-agent/issues/144159) | `clarify` error frames and mid-question reconnects return `undelivered`; locked answers dropped | None visible |
| P2 | [#144200](https://github.com/NousResearch/hermes-agent/issues/144200) | Kanban spawn callback invoked **twice** when its body raises `TypeError`/`ValueError` — duplicate side effects | None visible |
| P2 | [#144150](https://github.com/NousResearch/hermes-agent/issues/144150) | `hermes verify` no-op reports `ok: true` and records passing evidence | None visible |
| P2 | [#144003](https://github.com/NousResearch/hermes-agent/issues/144003) | Python 3.14: uv multi-env lock cannot resolve `onnxruntime` (no android wheels) → mnemosyne plugin fail-closed everywhere | None visible |
| P2 | [#144199](https://github.com/NousResearch/hermes-agent/issues/144199) | Concurrent `hermes chat -q` wake runs fail at model-connect with `invalid_params` (19/331) | None visible |
| P2 | [#144183](https://github.com/NousResearch/hermes-agent/issues/144183) | Aux post-rotation retry re-sends the failed **main** key, benching a healthy pool entry | None visible |
| P2 | [#144165](https://github.com/NousResearch/hermes-agent/issues/144165) | Windows `hermes update` v0.22.2→v0.22.5 leaves gateway restart unaccounted; startup recovery blocks Desktop | None; related Windows-update cluster |
| P2 | [#144166](https://github.com/NousResearch/hermes-agent/issues/144166) | `cron.load_jobs()` raises on transient Windows sharing violation (no reader-side retry, unlike `atomic_replace`) | None visible |
| P2 | [#144168](https://github.com/NousResearch/hermes-agent/issues/144168) | Linux/X11 + NVIDIA: Settings hover transitions cause large GPU utilization spikes | None visible |
| P2 | [#141521](https://github.com/NousResearch/hermes-agent/issues/141521) | Desktop demotes fenced code to prose by heuristic — PowerShell blocks lose copy box, `$vars` render as math | None visible |
| P2 | [#131786](https://github.com/NousResearch/hermes-agent/issues/131786) | Gateway completion tail discards output for long single-line JSON (2000-char cut + newline drop) | None visible |
| P3 | [#144204](https://github.com/NousResearch/hermes-agent/issues/…) | Vision pre-description prompt requests "~200 Chinese characters," forcing Chinese output | ✅ [PR #144204](https://github.com/NousResearch/hermes-agent/pull/144204) |

**Additional in-flight fix PRs without a linked issue:** [PR #144201](https://github.com/NousResearch/hermes-agent/pull/144201) (abandon streaming workers a stale kill cannot unwind — 39 identical log pairs over 2.6h), [PR #142372](https://github.com/NousResearch/hermes-agent/pull/142372) (`tool_preview_length: 0` honored as unlimited on all preview paths), [PR #140594](https://github.com/NousResearch/hermes-agent/pull/140594) (record piped verification runs; stop double nudge), [PR #129837](https://github.com/NousResearch/hermes-agent/pull/129837) (Nix `extraPythonPackages` silently drops all provider/integration deps).

**Assessment:** Fix coverage is thin — **1 of ~20 P2 bugs has a linked fix PR**. Windows accounts for 5 of the 20 visible bugs and is the single densest regression surface. Two classes are structurally concerning rather than one-off: (a) **session/title key collisions** (#143715, #144140, #136189, #136012 all describe different symptoms of the same weak session-identity model), and (b) **update/upgrade reliability** (#133253, #144165, #144003).

---

## 6. Feature Requests & Roadmap Signals

**High-probability for the next cycle:**

1. **Desktop session lifecycle + observability** — [#50718](https://github.com/NousResearch/hermes-agent/issues/50718) (unread markers, needs-input cues, OS badges) and [#48997](https://github.com/NousResearch/hermes-agent/issues/48997) (explicit close of abandoned sessions/backends). These are the same subsystem, have the highest engagement, and would also mitigate [#136012](https://github.com/NousResearch/hermes-agent/issues/136012). Expect a scoped subset (unread cue + needs-input) first.
2. **Plugin catalog governance and tiering** — [#143053](https://github.com/NousResearch/hermes-agent/issues/143053) (admission route) plus today's verified-tier PR (#144203) and pending entries: [Frihet ERP #134790](https://github.com/NousResearch/hermes-agent/pull/134790) / pin [#144210](https://github.com/NousResearch/hermes-agent/pull/144210), [Conxt memory #144192](https://github.com/NousResearch/hermes-agent/pull/144192), [KAME 1.8.2.9 #141137](https://github.com/NousResearch/hermes-agent/pull/141137). The catalog is becoming the primary distribution channel; expect a published admission document and continued SHA-pinned tier review.
3. **Gateway startup resilience** — [#144181](https://github.com/NousResearch/hermes-agent/issues/144181) with fix [#144198](https://github.com/NousResearch/hermes-agent/pull/144198): making `stay_alive` authoritative across all three fatal paths. Small, well-scoped, likely to land.
4. **Cost/latency reduction as a feature** — [PR #144177](https://github.com/NousResearch/hermes-agent/pull/144177) (−1,570 prompt tokens/call for one-shot runs, −302 for every session) and [PR #144174](https://github.com/NousResearch/hermes-agent/pull/144174) (skills index slimming) show maintainers actively trimming the system prompt. Prompt efficiency is being treated as a first-class roadmap item.

**Lower probability / needs design:**

- [#18964](https://github.com/NousResearch/hermes-agent/issues/18964) Reticulum/LXMF gateway — small audience, no maintainer signal in 176 days; a plugin/connector route is more likely than core support.
- [#98004](https://github.com/NousResearch/hermes-agent/issues/98004) per-group auto-continue budget for Bot Mode — clearly sequenced behind #89545, but P3 and 2 months idle.
- [PR #144207](https://github.com/NousResearch/hermes-agent/pull/144207) `terminal.process_priority: low` (OpenHands port) and [PR #144211](https://github.com/NousResearch/hermes-agent/pull/144211) resumable model/tool downloads — both small, opt-in, low-risk; good candidates to land quickly.
- **Extension-surface direction is unclear** after [#134556](https://github.com/NousResearch/hermes-agent/pull/134556) (`pre_send_message`) and [#144071](https://github.com/NousResearch/hermes-agent/issues/144071) (plugin human-input replies) were both closed. Integrators should not plan on outbound rewrite or cross-frontend approval routing in the near term.

---

## 7. User Feedback Summary

**Real pain points, grouped:**

- **"I can't tell what my agent is doing."** Multi-session Desktop users lose track of backgrounded work — the top issue by engagement. Users want OS-level affordances (badges, unread dots), not just in-app state.
- **"I can't upgrade."** Long-term users with large `state.db` files are structurally locked out of "Update now" (#133253); Windows users report the install becoming *"unusable again"* after an official `hermes update` (#144165). This is a **trust-eroding** class of feedback because it blocks access to fixes.
- **Windows is a second-class platform in practice.** Lease leakage (1,546 files), missing restart markers, cron file-sharing races, and Desktop startup recovery blocking are four distinct Windows-only failures in one window.
- **Multi-profile gateway operators hit scaling limits.** Unbounded DM sessions (#136012), unrecoverable shutdown flush (#136189), and duplicate kanban side effects (#144200) all describe production gateways serving many profiles/jobs where correctness degrades silently.
- **Clarify/approval flows are unreliable across reconnects.** Users lose locked answers and see spurious `undelivered` outcomes (#144158, #144159) — particularly costly for mobile clients that background.
- **Plugin developers face onboarding friction.** One maintainer cannot find the catalog admission route (#143053); another is blocked by a Python 3.14 dependency resolution failure (#144003); a third's voice plugin is stuck pending authentication-policy review ([#143585](https://github.com/NousResearch/hermes-agent/pull/143585)).
- **Desktop is heavy on Linux/NVIDIA.** Hovering Settings buttons spikes GPU utilization (#144168).

**Satisfaction signals:** Reports are unusually well-engineered (bisected SHAs, production metrics like "19 failures out of 331 wake-runs," 39-repetition log evidence), and users volunteer thanks and detailed repros — high goodwill and high investigative effort. **Dissatisfaction concentrates on reliability of the update path and on platform-specific breakage**, not on core agent capability.

---

## 8. Backlog Watch

| Item | Age | Why it needs maintainer attention |
|---|---|---|
| [Issue #18964](https://github.com/NousResearch/hermes-agent/issues/18964) — Reticulum/LXMF gateway | **176 days** (2026-05-02) | Oldest visible request; 3 👍; no maintainer response, no PR. A clear "accepted as plugin" or "won't do in core" ruling would close the loop. |
| [Issue #50718](https://github.com/NousResearch/hermes-agent/issues/50718) — Session visibility & notifications | **125 days** (2026-06-22) | Highest engagement in the dataset (12 comments, 3 👍) but labeled **P3**. Either promote and scope it, or split the three root causes so the cheap unread-cue fix isn't gated on the OS-badge work. |
| [Issue #48997](https://github.com/NousResearch/hermes-agent/issues/48997) — Explicit runtime-session lifecycle | **128 days** (2026-06-19) | Foundational for #50718 and #136012; still unassigned. Needs a design decision, not a patch. |
| [PR #129837](https://github.com/NousResearch/hermes-agent/pull/129837) — Nix `extraPythonPackages` drops default dependency groups | **24 days** open | Silent breakage of every provider/integration dep under NixOS/Home Manager; a contained, high-value fix awaiting review. |
| [Issue #133253](https://github.com/NousResearch/hermes-agent/issues/133253) — 1GB `state.db` blocks update | 20 days, `duplicate` | 8 comments despite duplicate label; the canonical issue should be identified explicitly so the upgrade-scaling fix isn't lost. |
| [PR #143585](https://github.com/NousResearch/hermes-agent/pull/143585) — Hermes Live voice plugin (draft) | 2 days, explicitly blocked | Author asks *not* to merge as-is pending authentication-policy review (rule 11). Needs a policy ruling, otherwise draft PRs will accumulate. |
| [Issue #98004](https://github.com/NousResearch/hermes-agent/issues/98004) — Per-group auto-continue budget | 57 days (2026-08-29) | Item 4 of #89545; sequencing is documented but no ETA. Worth confirming it's still on the roadmap. |
| [#134556](https://github.com/NousResearch/hermes-agent/pull/134556) (closed) / [#144071](https://github.com/NousResearch/hermes-agent/issues/144071) (closed) | — | Both touch plugin control of gateway messages/approvals. The **rationale for declining** should be documented so integrators stop re-proposing the same extension points. |

**Watch-list recommendation:** the maintainers are clearly bandwidth-constrained on review (18% PR close rate) and are using **salvage PRs** ([#144211](https://github.com/NousResearch/hermes-agent/pull/144211) from #143453, [#144174](https://github.com/NousResearch/hermes-agent/pull/144174) from #130552) to absorb community work efficiently. The highest-leverage next action is not more triage but **two design rulings** — session identity/lifecycle, and the plugin extension surface — since roughly a third of today's open P2s are downstream symptoms of those two undecided models.

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-10-25

*Repository: [agentscope-ai/QwenPaw](https://github.com/agentscope-ai/QwenPaw)*

---

## 1. Today's Overview

QwenPaw shows **moderate-to-high inbound activity with low landing throughput** for the 2026-10-24 window: 15 total items touched (4 issues, 11 PRs), but only **1 PR closed and 0 releases**, leaving 10 PRs and 3 issues open. The closed item (#8411) carries a `Close-and-review-later` label — i.e., closed without merge — so effectively **zero code landed today**. Activity is concentrated in channel/integration work (Matrix, QQ), governance/audit reliability, and Console UX defaults, consistent with a project serving self-hosted and team deployments. The most notable theme is a cluster of **silent-failure reports** (#8252, #8406, #8412) — config and audit state changing without warning — which points to observability debt rather than raw feature gaps. Review bandwidth, not contribution volume, appears to be the current bottleneck.

---

## 2. Releases

**None.** No new versions published in the last 24h. Latest field-reported version in issues is **2.2.1** (QwenPaw Desktop, Windows 10 AMD64) in [#8252](https://github.com/agentscope-ai/QwenPaw/issues/8252) — no migration or breaking-change notes to report.

---

## 3. Project Progress

Only one PR left the open queue, and it was **closed rather than merged**:

- **[#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411) `[CLOSED] [Close-and-review-later, size/L]` fix(matrix): recover cached sessions and missing device keys** — Fixes [#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365). Addresses password-authenticated Matrix channels re-logging in on every restart, and the case where a homeserver removes a device's encryption keys while the local crypto store still marks them as shared — causing startup to never re-upload keys while health checks falsely report a working channel. Given the `Close-and-review-later` label, the Matrix E2EE recovery gap should be treated as **still open in practice**.

No features or fixes were confirmed as merged today, so no user-visible capability advanced in this window.

---

## 4. Community Hot Topics

Ranked by comment volume (PR comment counts not exposed in the dataset):

1. **[Issue #8252](https://github.com/agentscope-ai/QwenPaw/issues/8252) — 4 comments, `[CLOSED] [Bug]`** — Agent-to-agent (`ChatWithAgent`) sessions silently cancelled with `CancelledError`; cancellations occur in same-second pairs, produce zero artifacts, and record neither the initiating party nor the reason. Reporter runs a multi-agent fleet (Orchestrator / Reasoner / Executor / Reviewer / Defender) behind a Feishu channel entry, and cites `qwenpaw/hooks/error/error_hook.py:90` and `qwenpaw/runtime/run...`. **Underlying need:** multi-agent orchestration users need cancellation to be *attributable* — who cancelled, why, and what was lost — otherwise fleet debugging is intractable.
2. **[Issue #8406](https://github.com/agentscope-ai/QwenPaw/issues/8406) — 2 comments, `[OPEN] [Bug]`** — QQ channel `enabled` flag silently reset to `false` with no warning. **Underlying need:** configuration must be authoritative and any mutation must be logged; silent config rollback breaks trust in long-running deployments. *(Data-quality note: the issue body renders as an unexpanded template placeholder `$(cat /tmp/issue_body.md)`, limiting triage.)*
3. **[Issue #8412](https://github.com/agentscope-ai/QwenPaw/issues/8412) — 1 comment, `[OPEN] [Bug]`** — `governance/audit.db` (`audit_events`) stops recording per-agent tool calls despite `audit_level: all` in each `governance/<agent>_<hash>/policy.yaml`; once rows stop, they never resume while the agent keeps calling tools, including file writes.
4. **[Issue #8407](https://github.com/agentscope-ai/QwenPaw/issues/8407) — 1 comment, `[OPEN] [Feature]`** — Requests a server-side default for the Console "assistant message display mode" (currently only `localStorage` key `qwenpaw_assistant_message_display_mode`).

**Signal:** the top three threads all describe *state changing or failing silently*. This is the dominant community concern this cycle.

---

## 5. Bugs & Stability

Ranked by severity:

| Severity | Item | Status |
|---|---|---|
| **Critical** | [#8252](https://github.com/agentscope-ai/QwenPaw/issues/8252) — Agent-to-agent sessions silently cancelled; artifacts zeroed; no initiator/reason recorded. Affects core multi-agent orchestration and produces total work loss. | **CLOSED, but no linked fix PR in this dataset** — closure reason unverified; root cause may be unaddressed. |
| **High** | [#8412](https://github.com/agentscope-ai/QwenPaw/issues/8412) — Per-agent audit logging permanently stops despite `audit_level: all`. A governance/compliance control failing open-but-silent is arguably worse than failing loudly; file-write tool calls go unlogged. | OPEN, no fix PR. |
| **High** | [#7835](https://github.com/agentscope-ai/QwenPaw/pull/7835) — Auto-memory-recall injects a synthetic `ToolCallBlock` + `ToolResultBlock` pair into LLM context; Console hides it via `MemorySearchCard`, but **every other outbound channel (Feishu, etc.) leaks it**. Privacy/UX leak. | OPEN, `Under Review` since 2026-09-17. |
| **Medium** | [#8406](https://github.com/agentscope-ai/QwenPaw/issues/8406) — QQ channel enabled flag silently reset to `false`; no warning surfaced. Silent config regression. | OPEN, no fix PR. |
| **Medium** | [#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411) / [#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365) — Matrix cached-session and missing-device-key recovery; health check reports healthy channel while E2EE keys are never re-uploaded. **False-positive health signal.** | PR **closed without merge** — regression risk persists. |
| **Medium** | [#7211](https://github.com/agentscope-ai/QwenPaw/pull/7211) — `HookContext.inject_context()` payload persists as a user turn / searchable history instead of staying request-local. Context pollution and history contamination. | OPEN, `ready-for-human-review` since 2026-08-21. |

**Fix-PR coverage:** 1 of 6 has an associated fix PR (#8411, closed unmerged). Five have none.

---

## 6. Feature Requests & Roadmap Signals

- **Team/self-hosted configurable defaults** — [#8407](https://github.com/agentscope-ai/QwenPaw/issues/8407) asks to move the assistant message display mode from browser `localStorage` to server-provided defaults, matching the existing `/config/theme` pattern (`chat_display.assistant_message_display_mode`). **Already paired with an implementation: [#8408](https://github.com/agentscope-ai/QwenPaw/pull/8408) `size/M`.** Small, self-contained, follows an established pattern → **high likelihood for the next release.**
- **QQ C2C streaming** — [#8409](https://github.com/agentscope-ai/QwenPaw/pull/8409) `size/L` implements `on_streaming_start/delta/end` via `POST /v2/users/{user_openid}/stream_messages`, gated behind `streaming_enabled` (`QQ_STREAMING_ENABLED`). Closes feature request [#8307](https://github.com/agentscope-ai/QwenPaw/issues/8307). Channel-parity work; plausible next-cycle candidate if review capacity exists.
- **Model pool lifecycle** — [#8410](https://github.com/agentscope-ai/QwenPaw/pull/8410) `size/M` adds a Remove action (with confirmation) for provider-managed entries, fixing [#8195](https://github.com/agentscope-ai/QwenPaw/issues/8195). Low risk; likely to land early.
- **Memory subsystem upgrade** — [#8347](https://github.com/agentscope-ai/QwenPaw/pull/8347) `size/XXXL` bumps ReMe `0.4.1.11 → 0.4.2.0`, adapts background indexing / Auto-Dream consolidation, and adds **opt-in, default-off** decision compaction, conversation image memory, and entity tagging. All new behavior is default-off → low user-facing risk, but the PR size itself is a review bottleneck. Realistic for a **minor/larger release, not the next patch**.

**Prediction:** next patch likely ships **#8408 + #8410**, possibly the three trivial docs-anchor PRs (#8401–#8403); #8409 and #8347 are more likely to slip a cycle.

---

## 7. User Feedback Summary

- **Deployment profiles are serious and production-like:** multi-agent fleets with role separation (Orchestrator/Reasoner/Executor/Reviewer/Defender), Feishu/Lark entry channels, QQ channels, password-authenticated Matrix homeservers, and self-hosted team deployments with admin-managed policy files. This is not hobbyist usage.
- **Primary pain point — silent failure:** three of four issues use "silently" or describe state that changes/stops with no warning (#8252 cancel with no initiator/reason, #8406 flag reset, #8412 audit logging dead). Users are not asking for features; they are asking to **be told when something goes wrong**.
- **Auditability and governance are load-bearing:** `policy.yaml` + `governance/audit.db` are used as compliance controls per agent; their silent failure undermines the trust model of the whole governance layer.
- **Multi-device / team ergonomics:** Console preferences tied to `localStorage` are described as lost on cache clear or new device, and admins cannot set defaults for members — a concrete self-hosting friction point.
- **Satisfaction signal:** reports are detailed, reproduced with versions and file/line references, and accompanied by community PRs (#8408 author also filed #8407). That indicates engaged users investing in the project — but also that **maintainer responsiveness on silent-failure bugs** is the main satisfaction risk.

---

## 8. Backlog Watch

Long-unanswered or high-importance items needing maintainer attention:

1. **[PR #7211](https://github.com/agentscope-ai/QwenPaw/pull/7211)** — `first-time-contributor`, `ready-for-human-review`, `size/L`, open since **2026-08-21 (~9 weeks)**. Fixes injected context persisting into conversation history (#6360, #6358). A first-time contributor sitting 2+ months in `ready-for-human-review` is a community-retention risk.
2. **[PR #7390](https://github.com/agentscope-ai/QwenPaw/pull/7390)** — `size/XS` test-only regression guard for Aliyun Coding Plan catalog drift (#6551), open since **2026-08-28 (~8 weeks)**. Trivial size, clear value, still unmerged — a review-queue anomaly worth escalating.
3. **[PR #7835](https://github.com/agentscope-ai/QwenPaw/pull/7835)** — `Under Review` since **2026-09-17 (~5 weeks)**. Fixes a genuine cross-channel privacy leak (memory-recall trace escaping to Feishu and other channels). Should be prioritized on privacy grounds.
4. **[PR #8347](https://github.com/agentscope-ai/QwenPaw/pull/8347)** — `size/XXXL` ReMe upgrade. Needs maintainer review bandwidth plus coordination with ReMe [#672](https://github.com/agentscope-ai/ReMe/pull/672); high merge-conflict risk the longer it sits.
5. **[Issue #8252](https://github.com/agentscope-ai/QwenPaw/issues/8252)** — closed **without a linked fix PR** in this dataset. Closed-but-unresolved critical orchestration bug; recommend a maintainer post-closure note confirming fix version or root cause.
6. **[Issue #8412](https://github.com/agentscope-ai/QwenPaw/issues/8412)** — governance audit DB silently dying is a compliance-class defect with zero fix PRs; recommend a triage label and owner within this cycle.
7. **[Issue #8406](https://github.com/agentscope-ai/QwenPaw/issues/8406)** — body still contains an unexpanded `$(cat /tmp/issue_body.md)` placeholder; needs a request-for-repro before it can be actioned.

---

*Digest compiled from 4 issues and 11 pull requests updated in the 24h window ending 2026-10-24. PR comment counts and 👍 totals were unavailable (`undefined`) in the source data and are excluded from rankings.*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-10-25

---

## 1. Today's Overview

ZeroClaw is in a **release-stabilization phase**: six issues were closed in the last 24 hours, including a P0 SOP-engine correctness bug and two high-risk security items, and PR #12011 (`chore(release): bump version to v0.8.7`) has closed out, with several bugs explicitly tagged `release:v0.8.7`. Activity volume is high — 18 issues and 50 PRs touched — but the shape is lopsided: **47 of 50 updated PRs remain open** versus only 3 merged/closed, indicating a widening review backlog rather than a merge-heavy sprint. No GitHub release has been published yet, so the v0.8.7 bump is prepared but not shipped. Quality signals are mixed-positive: strong security responsiveness (RUSTSEC advisories closed same-day) alongside persistent S0 data-loss exposure in the memory subsystem (#10797) that has been open 6 weeks.

> *Data note:* the PR feed reports `Comments: undefined` for all items, so PR comment counts cannot be ranked. Community Hot Topics below ranks by issue comment count and label weight.

---

## 2. Releases

**No new releases published in the last 24 hours.**

However, a release is clearly imminent:

- **PR #12011 [CLOSED]** — `chore(release): bump version to v0.8.7` — coordinated workspace bump 0.8.6 → 0.8.7 across `Cargo.toml`, `Cargo.lock` (30 workspace packages), Tauri config, `setup.bat`, and crates.io/Discord metadata.
  https://github.com/zeroclaw-labs/zeroclaw/pull/12011
- Issues tagged `release:v0.8.7` and closed today: **#10066** (SOP schema-rejection ordering), **#11899** (gateway serving stale connections after reload), **#12009** (RUSTSEC-2026-0385 / -0386 blocking the Security job).

**Predicted v0.8.7 contents:** SOP run-integrity fixes, gateway reload lifecycle fix, container CA bundle refresh, and dependency advisory remediation. No breaking changes or migration notes are signaled in the data.

---

## 3. Project Progress

**Merged/closed PRs (3):** Only **#12011** (version bump) is visible in the top-20 sample; the remaining two merged/closed PRs are not in the supplied slice.

**Closed issues — what actually landed or was resolved (6):**

| Issue | Severity | Outcome |
|---|---|---|
| [#10066](https://github.com/zeroclaw-labs/zeroclaw/issues/10066) — SOP engine promotes later steps before recording output-schema rejection | S1, P0 | Closed; workflow-blocking ordering bug fixed for v0.8.7 |
| [#12009](https://github.com/zeroclaw-labs/zeroclaw/issues/12009) — `cargo deny` fails on RUSTSEC-2026-0385 (ctutils) / -0386 (tokio) | S1 | Closed same-day as report; CI unblocked |
| [#11932](https://github.com/zeroclaw-labs/zeroclaw/issues/11932) — container CA bundles trust distrusted emSign chains | S0 security | Closed; native CA bundle corrected |
| [#11899](https://github.com/zeroclaw-labs/zeroclaw/issues/11899) — stopped gateway keeps serving already-accepted connections after reload | Gateway/config lifecycle | Closed; config-generation leak after reload addressed |
| [#9592](https://github.com/zeroclaw-labs/zeroclaw/issues/9592) — provider probe reads pre-update config snapshot | S2 | Closed; `model_routing_config` now probes the saved alias |
| [#12016](https://github.com/zeroclaw-labs/zeroclaw/issues/12016) — let embedding binaries add hooks to chat and cron turns | Feature | Closed within one day of filing (fast turnaround) |

**Features advancing in open PRs:** session event history (#11921), goal-mode command grammar (#11879, dormant config only), cron-to-conversation binding (#11441), XMPP/OMEMO channel (#11847), inbound-only email (#12003), `send_via` attachments (#11997), and two RPC/TUI signing-hardening PRs (#11422, #12013).

---

## 4. Community Hot Topics

Ranked by comment volume (issues only; PR comment data unavailable).

1. **[#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891) — [Tracker] Persistent memory: wire curation, relevance, and operability planes to parity** — 12 comments, open since 2026-07-09.
   The single most-discussed item in the project. An explicitly labeled roadmap/epic tracker coordinating a multi-PR rollout; the live snapshot from 2026-08-29 lists 7 open items (4 issues, 3 PRs). *Underlying need:* users want cross-session memory that matches mature peer runtimes — this is ZeroClaw's largest perceived capability gap and its longest-running coordination burden.

2. **[#10066](https://github.com/zeroclaw-labs/zeroclaw/issues/10066) — SOP engine ordering bug** — 5 comments, now closed.
   High engagement on a P0 correctness issue; the discussion centered on run-state integrity and how validation rejections interleave with step promotion.

3. **[#10797](https://github.com/zeroclaw-labs/zeroclaw/issues/10797) — Markdown memory backend loses entries on overlapping `store()`** — 3 comments, open.
   Filed by an agent-reported path; S0 severity. *Underlying need:* durability guarantees for the simplest, most-adopted memory backend.

4. **[#9592](https://github.com/zeroclaw-labs/zeroclaw/issues/9592) — provider alias probe after routing update** — 3 comments, closed.

5. **[#12014](https://github.com/zeroclaw-labs/zeroclaw/issues/12014)**, **[#12005](https://github.com/zeroclaw-labs/zeroclaw/issues/12005)**, **[#11799](https://github.com/zeroclaw-labs/zeroclaw/issues/11799)**, **[#11992](https://github.com/zeroclaw-labs/zeroclaw/issues/11992)**, **[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853)** — 2 comments each.

**Signal:** the community's attention is concentrated on **memory correctness** and **provider/config fidelity**, not on new channel integrations. Contributor `mov-xound-glitch` is highly prolific (5 issues + 4 PRs in this window) and `JordanTheJet` dominates the SOP/integrity and release-tagged workstream.

---

## 5. Bugs & Stability

Ranked by severity:

**S0 — Data loss**
- **[#10797](https://github.com/zeroclaw-labs/zeroclaw/issues/10797) [OPEN]** — `MarkdownMemory::store` rewrites the file from a stale snapshot; nothing serializes concurrent writers or verifies the write landed. Overlapping `store()` calls silently lose entries. *Open 6 weeks, no linked fix PR in this window — highest-priority gap.* Related open PR [#11663](https://github.com/zeroclaw-labs/zeroclaw/pull/11663) touches memory conflict handling but not this write path.

**S1 — Workflow blocked**
- **[#12014](https://github.com/zeroclaw-labs/zeroclaw/issues/12014) [OPEN]** — Custom OpenAI-compatible provider without `context_window` hard-falls back to **32,000 tokens** (`CompatibilityFallback`) and then rejects valid turns locally. Reported and updated 2026-10-24. **Fix PR exists: [#12017](https://github.com/zeroclaw-labs/zeroclaw/pull/12017)** — "treat the context window as model metadata, not configuration." Strong candidate for the next release.
- **[#12005](https://github.com/zeroclaw-labs/zeroclaw/issues/12005) [OPEN]** — PowerShell dialect parser rejects **any command containing a bare `@`**, making an allowlisted mail CLI unusable. Filed as a **regression** in `zeroclaw-config/src/policy.rs`, affecting v0.8.6 on Windows x86_64. No linked fix PR.
- **[#12009](https://github.com/zeroclaw-labs/zeroclaw/issues/12009) [CLOSED]** — Two RUSTSEC advisories (published 2026-10-23) broke `cargo deny check` on every `master` build. Resolved within a day.

**Security / supply chain**
- **[#11932](https://github.com/zeroclaw-labs/zeroclaw/issues/11932) [CLOSED]** — distrusted emSign intermediates remained in published container CA bundles; patching the Rust verifier alone was insufficient.
- **[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853) [OPEN]** — automated `npm audit` failure (high/critical: 1) affecting `@tailwindcss/typography` and others; open since 2026-10-19, bot-filed, 2 comments. Low user impact but recurring CI noise.

**S3 — Minor**
- **[#12006](https://github.com/zeroclaw-labs/zeroclaw/issues/12006) [OPEN]** — `tool_search` serves deferred tool schemas from registry-build-time copies, so live-config-driven schemas go stale after reload. **Fix PR: [#12007](https://github.com/zeroclaw-labs/zeroclaw/pull/12007).**
- **[#11792](https://github.com/zeroclaw-labs/zeroclaw/issues/11792) [OPEN]** — an interrupted `plugin install` can leave an orphaned `.<name>.installing-v1-<id>` stage directory that no command reclaims (disk leak). Tagged `release:v0.9.0`.
- **[#11946](https://github.com/zeroclaw-labs/zeroclaw/issues/11946) / [#11945](https://github.com/zeroclaw-labs/zeroclaw/issues/11945) [OPEN]** — follow-ups from #11861: a stale deterministic SOP checkpoint copy must not overwrite the owner's `{run}.state.json`, plus missing test coverage for those branches.

**Closed stability wins:** [#11899](https://github.com/zeroclaw-labs/zeroclaw/issues/11899) (gateway post-reload connection/config-generation leak) and [#10066](https://github.com/zeroclaw-labs/zeroclaw/issues/10066) (SOP schema-rejection ordering) both represent meaningful integrity improvements to long-lived runs.

---

## 6. Feature Requests & Roadmap Signals

**Explicit user requests in this window:**

| Request | Issue | Likelihood of near-term delivery |
|---|---|---|
| Shell policy refusals should name the refusing rule and accept an operator hint | [#11799](https://github.com/zeroclaw-labs/zeroclaw/issues/11799) | Medium — small, well-scoped, security-adjacent DX fix |
| `send_via` explicit `attachments` checked under read policy | [#11996](https://github.com/zeroclaw-labs/zeroclaw/issues/11996) | **High** — implementation already open as PR [#11997](https://github.com/zeroclaw-labs/zeroclaw/pull/11997) |
| Email channel: operator templates + sanitized HTML body | [#11992](https://github.com/zeroclaw-labs/zeroclaw/issues/11992) | Medium — natural companion to #11997 |
| Embedding binaries add hooks to chat/cron turns | [#12016](https://github.com/zeroclaw-labs/zeroclaw/issues/12016) | **Closed already** (2026-10-24) |
| Inbound-only email channels without SMTP | PR [#12003](https://github.com/zeroclaw-labs/zeroclaw/pull/12003) | In review — lowers the bar for read-only mailbox access |

**Roadmap signals:**
- **Goal Mode V1** is being staged as a 12-part proposal series; [#11879](https://github.com/zeroclaw-labs/zeroclaw/pull/11879) is "proposal 2/12" and is deliberately **dormant** (config + command grammar only, no executable behavior). Expect Goal Mode to land across multiple releases, not in v0.8.7.
- **Session event history** ([#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921), size:XL) proposes append-only canonical session events for effect-safe replay — an architectural change, not a point feature.
- **Persistent memory parity** ([#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891)) remains the headline roadmap commitment.
- **Messaging breadth**: XMPP/Prosody with optional OMEMO ([#11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847)) signals continued demand for self-hosted/federated transports.

**Prediction:** v0.8.7 ships with the SOP/gateway/security fixes above and likely no new features. v0.9.0 scope is coalescing around plugin-install robustness (#11792), `send_via` attachments (#11997), context-window semantics (#12017), and continued memory work (#11663, #8891).

---

## 7. User Feedback Summary

**Pain points, ranked by evidence strength:**

1. **Memory is not yet trustworthy.** The tracker with the most engagement (#8891, 12 comments, 3.5 months old) plus an S0 concurrent-write data-loss bug (#10797) and a superseession-scoring defect ([#11663](https://github.com/zeroclaw-labs/zeroclaw/pull/11663) — BM25 normalization can assign `1.0` to unrelated rows, causing consolidation to soft-hide them) all point at the same unmet need: *reliable, non-destructive long-term recall*.
2. **Platform/locale edge cases bite hard.** The PowerShell `@` regression (#12005) makes an entire allowlisted workflow unusable on Windows — a case of security hardening breaking legitimate operators. Similarly, provider config gaps silently degrade into locally-rejected turns (#12014).
3. **Opaque security refusals frustrate operators.** #11799 explicitly asks for *why* a command was denied, plus an operator-configurable hint. This is a usability complaint about the security layer, not a request to weaken it.
4. **Email remains second-class.** Send-side cannot attach files at all since #9993 (#11996), and inbound-only mailboxes could not even be expressed (#12003). Users are pushing email toward production-grade parity.
5. **Attribution/responsiveness is a positive signal.** #12016 closed within 24 hours; #12009 (CI-breaking advisories) closed the same day it was filed; #10066 and #11932 (both high-risk) closed today. Dissatisfaction in this window is about *capability gaps*, not maintainer neglect.

**Use cases visible in the data:** embedding ZeroClaw as a library inside a larger binary (hooks, #12016); Windows-native shell automation with mail CLIs; OpenAI-compatible self-hosted/third-party endpoints with unusual context sizes; read-only compliance mailboxes; federated chat via XMPP.

---

## 8. Backlog Watch

Items needing maintainer attention, ordered by risk × staleness:

| Item | Age | Why it matters |
|---|---|---|
| **[#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891)** — Persistent memory parity tracker | ~3.5 months (created 2026-07-09) | Highest-engagement item; snapshot is dated 2026-08-29 and is now ~8 weeks stale. Needs a refreshed status table. |
| **[#10797](https://github.com/zeroclaw-labs/zeroclaw/issues/10797)** — Markdown backend data loss (S0) | ~6 weeks (2026-09-12) | S0 with **no linked fix PR**. Longest-lived severe open bug in the dataset. |
| **[#9420](https://github.com/zeroclaw-labs/zeroclaw/pull/9420)** — Anthropic stored OAuth profiles | ~3 months (2026-07-26) | Size:XL, `risk:high`, `trusted contributor`; touches core/provider/gateway and security domain. High-value, high-review-cost — likely starved for reviewer bandwidth. |
| **[#10446](https://github.com/zeroclaw-labs/zeroclaw/pull/10446)** — Reject tool-call envelopes leaked into prose | ~2 months (2026-08-29) | `domain:security`, `risk:high`, `size:XL`; describes intermittent production occurrences on `gpt-5.6`/codex. Merges cleanly per author — blocked on review, not conflicts. |
| **[#11422](https://github.com/zeroclaw-labs/zeroclaw/pull/11422)** — TUI signing bound to config-owned key | ~3 weeks (2026-10-02) | `size:XL`, blocked on #11411/#11576; #12013 was extracted from it as a smaller testable slice — good unblocking pattern worth continuing. |
| **[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853)** — `npm audit` failure (bot) | 6 days | Recurring automated security findings need a triage/rotation policy, not one-off fixes. |
| **[#11792](https://github.com/zeroclaw-labs/zeroclaw/issues/11792)** — orphaned plugin install stage dirs | 9 days | Tagged v0.9.0 with no linked PR; a small, self-contained fix that would clear a known disk-leak class. |

**Backlog health verdict:** the **47-open / 3-closed PR ratio** is the most important structural number in this digest. Several large, high-risk PRs (#9420, #10446, #11422, #11921) are technically ready but review-bound, while quick wins (#12007, #12013, #11997) move. The bottleneck is review capacity on XL/`risk:high` changes, not contributor supply.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*