# OpenClaw Ecosystem Digest 2026-10-28

> Issues: 500 | PRs: 500 | Projects covered: 5 | Generated: 2026-10-28 01:35 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-10-28

## 1. Today's Overview
As of 2026-10-28, OpenClaw shows very high throughput: 500 issues and 500 PRs were updated in the last 24h, with 391 active issues, 109 closed issues, 275 open PRs, and 225 merged/closed PRs. Three releases landed across beta, non-beta, and extended-stable channels, indicating active release engineering. However, the issue mix is heavily weighted toward production stability: at least nine open P0 issues appear in the top-50 sample, covering Gateway event-loop blocking, Windows/update failures, session/DB recovery, and memory bootstrap. Overall health is mixed: strong contributor/maintainer throughput and active community reproduction, but elevated stability risk because several high-comment P0/P1 issues still lack visible fix PRs.

## 2. Releases
Three new releases were published in the last 24h:

- [v2026.10.10-beta.4](https://github.com/openclaw/openclaw/releases/tag/v2026.10.10-beta.4) — Beta release. Main stated fix: automations preserve authenticated operator authority for explicit same-conversation scheduled follow-ups, including one-shot `next_check` rearming, while retaining role, session, cancellation, target, and tool-policy revocation boundaries. No breaking changes or migration steps are stated in the supplied notes.
- [v2026.10.8](https://github.com/openclaw/openclaw/releases/tag/v2026.10.8) — Update-recovery focused release. Stated fixes include restarting the Gateway after an ordinary post-update Doctor failure instead of leaving it stopped; preventing an orphaned maintenance worker from blocking CLI commands and Gateway start; and avoiding update aborts on ACL filesystems and Windows-related update paths. No explicit breaking changes or migration notes are stated.
- [v2026.9.35](https://github.com/openclaw/openclaw/releases/tag/v2026.9.35) — Gateway-only `extended-stable` release, described as the current LTS equivalent. It is based on end-of-September 2026 OpenClaw plus critical security updates, reliability/performance fixes, and new model support.

**Migration/breaking-change assessment:** No breaking changes or explicit migration steps are listed in the supplied release text. v2026.10.8 is the most relevant release for operators recovering from failed post-update Gateway states; v2026.9.35 is the conservative extended-stable channel.

## 3. Project Progress
In the last 24h, 225 PRs were merged/closed and 275 remain open. The sample shows progress across security boundaries, update/restart reliability, session recovery, accessibility, and platform-specific fixes.

**Closed/merged PR signals in the sample:**
- [PR #186417](https://github.com/openclaw/openclaw/pull/186417) — Closed security fix: webhook, mapped-hook, plugin-trigger, and join-introduction turns no longer run with owner-only tools.
- [PR #183018](https://github.com/openclaw/openclaw/pull/183018) — Closed UI fix: keeps guest access guidance in Profile without misleading Inbox warnings.
- [PR #178347](https://github.com/openclaw/openclaw/pull/178347) — Closed docs fix: clarifies recovery for image-based hosts that stay down after an upgrade until Doctor runs.

**Notable open/ready PRs advancing the project:**
- [PR #185730](https://github.com/openclaw/openclaw/pull/185730) — Large refactor to resume running sessions after graceful restarts, reducing long restart drains and recovery contention.
- [PR #185939](https://github.com/openclaw/openclaw/pull/185939) — Recovers transcript rewrites from concurrent appends, targeting session/transcript integrity.
- [PR #186420](https://github.com/openclaw/openclaw/pull/186420) and [PR #186426](https://github.com/openclaw/openclaw/pull/186426) — Improve model-fallback behavior on quota-class 429s instead of dead-ending on a pinned model or same-profile auth rotation.
- [PR #186415](https://github.com/openclaw/openclaw/pull/186415) — Prevents open node skill watches from deferring Gateway restarts for the full 300s.
- [PR #186411](https://github.com/openclaw/openclaw/pull/186411) — Fixes update cleanup failing its own destination check.
- [PR #185921](https://github.com/openclaw/openclaw/pull/185921) — Adds TUI screen reader mode, closing accessibility issue #9637 if merged.
- [PR #186416](https://github.com/openclaw/openclaw/pull/186416) — Restores iOS image shares on multi-agent gateways.
- [PR #185028](https://github.com/openclaw/openclaw/pull/185028) — Preserves native CORS preflights in browser navigation guards.
- [PR #184524](https://github.com/openclaw/openclaw/pull/184524) — Fixes quadratic re-scanning when streamed replies quote empty command output.
- [PR #184712](https://github.com/openclaw/openclaw/pull/184712) — Compacts consecutive Telegram progress tool calls to keep previews useful.

**Closed issue signals in the sample:** duplicate transcript writes ([#118185](https://github.com/openclaw/openclaw/issues/118185)), memory index stall after two files ([#130955](https://github.com/openclaw/openclaw/issues/130955)), billing cooldown recovery request ([#115642](https://github.com/openclaw/openclaw/issues/115642)), native Codex aborted-scope completion failure ([#167410](https://github.com/openclaw/openclaw/issues/167410)), memory index SIGTERM orphan issue ([#185902](https://github.com/openclaw/openclaw/issues/185902)), and update-failure reports ([#169937](https://github.com/openclaw/openclaw/issues/169937), [#153094](https://github.com/openclaw/openclaw/issues/153094)).

## 4. Community Hot Topics
PR comment counts were blank in the supplied data, so hot-topic ranking below uses issue comment counts. Reaction counts are generally low; the most visible support in the sample is [#7722](https://github.com/openclaw/openclaw/issues/7722) with 4 👍.

- [#160959](https://github.com/openclaw/openclaw/issues/160959) — **58 comments, P0.** Gateway blocks for minutes while capturing large external/workspace plugins, a 2026.9.6 regression. **Underlying need:** plugin capture isolation, streaming limits, or async preparation so Gateway startup and runtime republishing cannot starve the event loop.
- [#119720](https://github.com/openclaw/openclaw/issues/119720) — **25 comments, P1.** Synchronous agent persistence and transcript maintenance block the Gateway event loop at scale. **Underlying need:** async persistence, backpressure, and bounded transcript-maintenance work.
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — **22 comments, P0.** Windows managed-service installs show slow startup, unclean plugin shutdown, and `schtasks` permission errors, making restarts/updates unreliable. **Underlying need:** stronger Windows service lifecycle handling and update recovery.
- [#157325](https://github.com/openclaw/openclaw/issues/157325) — **19 comments, P0.** A stuck agent-DB resource makes every agent’s replies fail until Gateway restart. **Underlying need:** DB resource health checks, automatic recovery, and per-agent failure isolation.
- [#120385](https://github.com/openclaw/openclaw/issues/120385) — **18 comments, P2.** Code Mode tool catalog is incomplete for scheduled/cron and memory-flush turns, causing “Unknown tool id” failures. **Underlying need:** consistent tool discovery and catalog construction across all turn types.
- Secondary high-engagement issues: [#166634](https://github.com/openclaw/openclaw/issues/166634) (15 comments, P0 reclamation checkpoint failure), [#121953](https://github.com/openclaw/openclaw/issues/121953) (14 comments, P1 DeepSeek cron prefix stall), [#177656](https://github.com/openclaw/openclaw/issues/177656) (14 comments, P1 shared-state integrity error), and [#172837](https://github.com/openclaw/openclaw/issues/172837) (14 comments, P1 Codex native subagent assignments not removed).

## 5. Bugs & Stability
**P0 issues in the top-50 sample — highest severity:**
- [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway event loop blocks for minutes on large plugin capture. No new fix PR visible in provided tags.
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows slow startup, unclean plugin shutdown, and `schtasks` permission errors. No new fix PR visible.
- [#157325](https://github.com/openclaw/openclaw/issues/157325) — Stuck agent-DB resource causes all agent replies to fail until restart. Tagged `needs-live-repro`.
- [#166634](https://github.com/openclaw/openclaw/issues/166634) — Late reclamation checkpoint failure leaves agent unavailable after crash recovery. Tagged `not-repro-on-main`.
- [#161921](https://github.com/openclaw/openclaw/issues/161921) — `state-migrated-no-rollback` leaves `doctor --fix` and `plugins update` mutually blocked. No new fix PR visible.
- [#167406](https://github.com/openclaw/openclaw/issues/167406) — Windows MSIX Codex-enabled Gateway startup starves the main thread. No fix PR visible.
- [#185819](https://github.com/openclaw/openclaw/issues/185819) — Windows quick-start Codex onboarding completes device auth but fails activation with inactive harness runtime artifact. No fix PR visible.
- [#164800](https://github.com/openclaw/openclaw/issues/164800) — Managed update from 2026.9.7 to 2026.9.8 leaves activation driver dead until host reboot. No fix PR visible.
- [#158912](https://github.com/openclaw/openclaw/issues/158912) — `MEMORY.md` no longer injected at bootstrap on 2026.9.6 despite docs. No fix PR visible.

**Selected P1 stability issues:**
- [#119720](https://github.com/openclaw/openclaw/issues/119720) — Synchronous persistence/transcript maintenance blocks event loop.
- [#117262](https://github.com/openclaw/openclaw/issues/117262) — SQLite contention from three concurrent write handles causes ~33s event-loop stalls.
- [#121953](https://github.com/openclaw/openclaw/issues/121953) — Cron agent turns stall on DeepSeek because of the `[cron:...]` prefix.
- [#177656](https://github.com/openclaw/openclaw/issues/177656) — Native host exec returns shared-state integrity-admission error without child result.
- [#172837](https://github.com/openclaw/openclaw/issues/172837) — Codex native subagent assignments are never removed after completion delivery gives up.
- [#181432](https://github.com/openclaw/openclaw/issues/181432) — Excessive baseline RSS (~1.9 GB) and SQLite transaction timeouts in 2026.10.5.
- [#166017](https://github.com/openclaw/openclaw/issues/166017) — MCP session idle TTL default not applied; spawn broker reaches hard cap of 256 children. Has a linked open PR.
- [#169797](https://github.com/openclaw/openclaw/issues/169797) — Gateway memory regression and slower startup/first reply on single-CPU hosts.
- [#181078](https://github.com/openclaw/openclaw/issues/181078) — Agent turns intermittently fail with “Canonical session reader continuation is no longer current.”
- [#184720](https://github.com/openclaw/openclaw/issues/184720) — Windows interrupted update repair leaves empty Doctor custody receipts.
- [#178359](https://github.com/openclaw/openclaw/issues/178359) — `doctor --fix --non-interactive` idles 15+ minutes in plugin-capture stage.
- [#118776](https://github.com/openclaw/openclaw/issues/118776) — Leaf sub-agents keep `sessions_yield` but lose tools that could produce the awaited event.
- [#166964](https://github.com/openclaw/openclaw/issues/166964) — Subagent completion delivery fails permanently when requester authority is revoked.

**Fix-PR visibility:** The highest-comment P0s — [#160959](https://github.com/openclaw/openclaw/issues/160959), [#170956](https://github.com/openclaw/openclaw/issues/170956), [#161921](https://github.com/openclaw/openclaw/issues/161921), [#164800](https://github.com/openclaw/openclaw/issues/164800), and [#185819](https://github.com/openclaw/openclaw/issues/185819) — do not show visible fix PRs in the supplied tags. Related open PRs exist for adjacent areas: transcript rewrite recovery ([#185939](https://github.com/openclaw/openclaw/pull/185939)), model fallback ([#186420](https://github.com/openclaw/openclaw/pull/186420), [#186426](https://github.com/openclaw/openclaw/pull/186426)), restart deferral ([#186415](https://github.com/openclaw/openclaw/pull/186415)), update cleanup ([#186411](https://github.com/openclaw/openclaw/pull/186411)), browser CORS ([#185028](https://github.com/openclaw/openclaw/pull/185028)), and iOS image sharing ([#186416](https://github.com/openclaw/openclaw/pull/186416)).

## 6. Feature Requests & Roadmap Signals
- [#7722](https://github.com/openclaw/openclaw/issues/7722) — Filesystem sandboxing config (`tools.fileAccess`) with allow/deny paths. Security-sensitive, 4 👍, tagged for product and security review. Important but unlikely to land without explicit security design.
- [#13219](https://github.com/openclaw/openclaw/issues/13219) — Per-model usage logging for cost tracking. Clear operator need; currently users must parse session JSONL manually.
- [#9637](https://github.com/openclaw/openclaw/issues/9637) — Accessibility option to disable emojis/unicode in TUI. An open implementation PR exists: [#185921](https://github.com/openclaw/openclaw/pull/185921) adds screen reader mode, making this one of the more likely near-term UX improvements.
- [#52732](https://github.com/openclaw/openclaw/issues/52732) — Per-agent `compaction` and `contextPruning` overrides in `agents.list`. Needs product decision; relevant for multi-agent deployments.
- [#175826](https://github.com/openclaw/openclaw/issues/175826) — Android parity for in-chat optional questions and native approval requests. Requests design acceptance before implementation.
- [#172547](https://github.com/openclaw/openclaw/issues/172547) — Exclude fenced dreaming ranges from memory-search indexing and recall signals. Needs product decision around memory semantics.
- [#184600](https://github.com/openclaw/openclaw/issues/184600) — Skill Workshop auto-mutation of skills without user review/consent. This is both a bug/UX concern and a governance/security roadmap signal.

**Likely next-version candidates based on open PRs:** TUI screen reader mode ([#185921](https://github.com/openclaw/openclaw/pull/185921)), model-fallback/quota handling ([#186420](https://github.com/openclaw/openclaw/pull/186420), [#186426](https://github.com/openclaw/openclaw/pull/186426)), restart/update reliability ([#186415](https://github.com/openclaw/openclaw/pull/186415), [#186411](https://github.com/openclaw/openclaw/pull/186411)), and session-resume/recovery work ([#185730](https://github.com/openclaw/openclaw/pull/185730), [#185939](https://github.com/openclaw/openclaw/pull/185939)). Sandboxing, per-agent pruning, and Android approval parity look more dependent on product/security decisions.

## 7. User Feedback Summary
**Dominant pain points:** production reliability and upgrade safety. Users report Gateway event-loop stalls from plugin capture and persistence ([#160959](https://github.com/openclaw/openclaw/issues/160959), [#119720](https://github.com/openclaw/openclaw/issues/119720), [#117262](https://github.com/openclaw/openclaw/issues/117262)); Windows service/update failures ([#170956](https://github.com/openclaw/openclaw/issues/170956), [#164800](https://github.com/openclaw/openclaw/issues/164800), [#184720](https://github.com/openclaw/openclaw/issues/184720), [#185819](https://github.com/openclaw/openclaw/issues/185819)); and update/doctor/plugin deadlocks ([#161921](https://github.com/openclaw/openclaw/issues/161921)).

**Session and message integrity:** repeated reports of stuck agent DB state ([#157325](https://github.com/openclaw/openclaw/issues/157325)), reclamation checkpoint failures ([#166634](https://github.com/openclaw/openclaw/issues/166634)), canonical session reader errors ([#181078](https://github.com/openclaw/openclaw/issues/181078)), duplicate transcript writes ([#118185](https://github.com/openclaw/openclaw/issues/118185), closed), and subagent completion delivery failures ([#166964](https://github.com/openclaw/openclaw/issues/166964)).

**Memory/indexing:** missing `MEMORY.md` bootstrap ([#158912](https://github.com/openclaw/openclaw/issues/158912)), reindex lock preventing repair and accumulating 19 GB of temp DBs ([#136311](https://github.com/openclaw/openclaw/issues/136311)), full-index publish exhausting SQLite busy timeout ([#143640](https://github.com/openclaw/openclaw/issues/143640)), memory-bloat regressions ([#181432](https://github.com/openclaw/openclaw/issues/181432), [#169797](https://github.com/openclaw/openclaw/issues/169797)), and dreaming ranker/applier mismatch ([#121232](https://github.com/openclaw/openclaw/issues/121232)).

**Security/governance and UX:** owner-only tools exposed to hook/join-introduction turns was fixed in [#186417](https://github.com/openclaw/openclaw/pull/186417); users still ask for filesystem sandboxing ([#7722](https://github.com/openclaw/openclaw/issues/7722)) and governance of Skill Workshop auto-mutation ([#184600](https://github.com/openclaw/openclaw/issues/184600)). UX friction includes Control UI transcript jitter ([#164394](https://github.com/openclaw/openclaw/issues/164394)), webchat notice displacement ([#177094](https://github.com/openclaw/openclaw/issues/177094)), masked config values causing outage misdiagnosis ([#173313](https://github.com/openclaw/openclaw/issues/173313)), Telegram progress-preview crowding ([#184712](https://github.com/openclaw/openclaw/pull/184712)), and accessibility gaps ([#9637](https://github.com/openclaw/openclaw/issues/9637), [#185921](https://github.com/openclaw/openclaw/pull/185921)).

**Sentiment assessment:** no survey data is provided, but issue sentiment is strained around production stability and upgrades. Positive signals are active community reproduction, detailed operator reports, linked PRs, and maintainer triage labels; dissatisfaction is concentrated in repeated P0/P1 reliability failures and insufficient rollback/diagnostic paths.

## 8. Backlog Watch
These items are old or high-impact, have significant discussion, and still need maintainer attention:

- [#160959](https://github.com/openclaw/openclaw/issues/160959) — P0 since 2026-09-29, 58 comments, no new fix PR. Highest-priority Gateway event-loop blocker.
- [#119720](https://github.com/openclaw/openclaw/issues/119720) — P1 since 2026-08-05, 25 comments, no new fix PR. Long-running persistence/event-loop scaling issue.
- [#117262](https://github.com/openclaw/openclaw/issues/117262) — P1 since 2026-08-01, 12 comments. SQLite write-handle contention causing ~33s stalls.
- [#121953](https://github.com/openclaw/openclaw/issues/121953) — P1 since 2026-08-11, 14 comments. DeepSeek cron-prefix stall remains unresolved.
- [#136311](https://github.com/openclaw/openclaw/issues/136311) — P2 since 2026-09-02, 12 comments. Memory reindex lock and orphaned temp DB accumulation.
- [#161921](https://github.com/openclaw/openclaw/issues/161921) — P0 since 2026-09-30, 11 comments, no new fix PR. Update/doctor/plugin deadlock blocks recovery.
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — P0 since 2026-10-13, 22 comments, no new fix PR. Windows restart/update reliability.
- [#7722](https://github.com/openclaw/openclaw/issues/7722) — P2 security enhancement since 2026-02-03, 11 comments, 4 👍. Filesystem sandboxing config needs product/security decision.
- [#13219](https://github.com/openclaw/openclaw/issues/13219) — P2 since 2026-02-10, 9 comments. Per-model usage logging for cost tracking.
- [#52732](https://github.com/openclaw/openclaw/issues/52732) — P2 since 2026-03-23, 7 comments. Per-agent compaction/context-pruning overrides need product decision.

**PR backlog note:** Most high-impact PRs in the sample are recent. The clearest older PR is [#156626](https://github.com/openclaw/openclaw/pull/156626), a test-only relay-matching refactor open since 2026-09-23 and waiting on author; it is low product risk relative to the P0/P1 issue backlog.

---

## Cross-Ecosystem Comparison

## 1. Ecosystem Overview

On 2026-10-28, the personal AI assistant / agent open-source landscape is consolidating around a gateway/runtime core, multi-surface clients (desktop, web, mobile, messaging), and MCP/tool integrations. Activity is high but uneven: OpenClaw operates at hyper-scale, Hermes Agent, QwenPaw, and ZeroClaw iterate in the 22–50 issue/PR range, and IronClaw was inactive in the window. Release engineering is becoming a differentiator—OpenClaw shipped three channels, QwenPaw a beta, ZeroClaw a security patch, while Hermes shipped no release. The common drag is no longer feature breadth but production hardening: session identity, async runtime health, provider compatibility, MCP reliability, and principal-aware security.

## 2. Activity Comparison

*Counts are 24h updated/touched, not repository totals. Health score\* is an analyst heuristic combining activity, release cadence, stability/security, and review throughput; it is not an upstream project metric.*

| Project | Issues count (24h) | PR count (24h) | Release status | Health score\* |
|---|---:|---:|---|---|
| **OpenClaw** | 500 updated: 391 open/active, 109 closed | 500 updated: 275 open, 225 merged/closed | 3 releases: beta, update-recovery stable, extended-stable LTS | **6.5/10** — high throughput, elevated P0 risk |
| **Hermes Agent** | 50 touched: 47 open, 3 closed | 50 touched: 43 open, 7 merged/closed | No release; users reference v0.22.5 stable | **5.8/10** — high triage, session-architecture debt |
| **IronClaw** | No activity | No activity | No release | **N/A** — inactive window |
| **QwenPaw** | 36 touched: 16 open, 20 closed | 50 touched: 39 open, 11 merged/closed | v2.3.0-beta.1 | **7.0/10** — responsive, review backlog |
| **ZeroClaw** | 22 updated: 21 open, 1 closed | 42 updated: 41 open, 1 merged/closed | v0.8.7 security/maintenance | **6.7/10** — release discipline, security/review bottleneck |

## 3. OpenClaw’s Position

**Advantages vs peers.** OpenClaw is the scale leader by an order of magnitude: 500 issues and 500 PRs touched in 24h, with 225 PRs merged/closed—versus 7 for Hermes, 11 for QwenPaw, and 1 for ZeroClaw. It is also the only project in this set with three active release channels, including an extended-stable LTS-equivalent. Its issue engagement is the deepest in the sample: the top Gateway event-loop issue has 58 comments, versus 24 for Hermes’ top item, 16 for ZeroClaw, and 5 for QwenPaw.

**Technical approach differences.** OpenClaw is a horizontal, Gateway-centric platform: sessions, plugins, channels, Doctor/update recovery, and security boundaries are centralized in a runtime that is visibly sensitive to event-loop blocking. Peers are narrower and more vertical: Hermes is building a canonical multi-surface session/protocol layer; QwenPaw is concentrated on desktop/console context, memory, MCP, and provider compatibility; ZeroClaw is security-first with OIDC, principal scoping, and a Rust/Tokio/Wasmtime-style stack. OpenClaw’s advantage is breadth and operator tooling; its risk is that the same central runtime concentrates production stability failures.

**Community size comparison.** OpenClaw’s throughput is not just larger—it is a different operating tier. It closed/merged 225 PRs in 24h, roughly 20–30x the visible merge throughput of Hermes, QwenPaw, and ZeroClaw. That implies a much larger contributor/maintainer base and more production deployment surface, but also a harder triage problem: at least nine open P0s appear in the top-50 sample, and several top P0s lack visible fix PRs.

## 4. Shared Technical Focus Areas

| Shared need | Projects showing it | Specific requirements |
|---|---|---|
| **Session persistence, resumption, and identity** | OpenClaw, Hermes, QwenPaw, ZeroClaw | Resumable sessions across restarts and surfaces; explicit ownership instead of `source`-based locking; recovery from transcript/DB corruption; append-only event history; session load/resume APIs. |
| **Async runtime health and backpressure** | OpenClaw, Hermes, QwenPaw, ZeroClaw | Non-blocking persistence and plugin capture; bounded queues; SQLite contention control; gateway stall detection; per-profile thread/resource limits; MCP connection recovery. |
| **Provider/model compatibility and cost-aware fallback** | OpenClaw, Hermes, QwenPaw, ZeroClaw | Quota-aware 429 fallback; wire-format quirks such as thinking-disable shapes; OAuth/subscription sign-in; Codex streaming behavior; model catalog drift; typed provider errors. |
| **MCP/tool protocol and tool-call integrity** | OpenClaw, Hermes, QwenPaw, ZeroClaw | Legacy MCP negotiation fallback; poisoned-session recovery; consistent tool catalog per turn; schema/argument validation; approval-path safety; principal-aware tool selectors. |
| **Security, governance, and least privilege** | OpenClaw, Hermes, QwenPaw, ZeroClaw | Filesystem sandboxing; audit-log integrity; OIDC/SSO; CSP; owner-only tool boundaries; approval allowlist hardening; protection against subagent/plugin privilege escalation. |
| **Upgrade/install reliability and multi-surface parity** | OpenClaw, Hermes, QwenPaw, ZeroClaw | Self-healing updates and rollback; Windows/Android install robustness; diagnostics that preserve failure causes; mobile keyboard/input; PWA/Linux packaging; accessibility modes. |

IronClaw has no data in this window and is excluded from the shared-need evidence.

## 5. Differentiation Analysis

| Project | Primary feature focus | Target users | Architecture / approach |
|---|---|---|---|
| **OpenClaw** | Broad gateway/plugin platform, multi-channel operations, update recovery, LTS | Operators, power users, production multi-channel deployments | Gateway-centric core; plugins/channels; Doctor/update paths; extended-stable release channel; event-loop-sensitive runtime. |
| **Hermes Agent** | Multi-surface conversation continuity and client protocol | Power users, remote `hermes serve` deployments, third-party client builders | Canonical session authority + gateway; Desktop/Webapp/mobile/TUI surfaces; profile multiplexing; currently paying down session-ownership debt. |
| **QwenPaw** | Desktop/console agent workspace, context compaction, memory, MCP/provider breadth, governance | Desktop and enterprise users needing MCP, memory, audit, and provider flexibility | Python-based console/desktop stack; skills/channels/plugins; SQLite history; model registry; governance audit; Windows/Linux packaging. |
| **ZeroClaw** | Security-first self-hosted gateway/dashboard, identity/access, runtime hardening | Security-conscious self-hosters and enterprise-style deployments | Rust/Tokio/Wasmtime stack; gateway/dashboard; OIDC and principal scoping; append-only session history; v0.9.0 runtime/gateway focus. |
| **IronClaw** | No visible activity in window | Unknown | Unknown from supplied data. |

OpenClaw is the horizontal platform play. Hermes is the multi-surface continuity play. QwenPaw is the desktop/context/provider-compatibility play. ZeroClaw is the security/identity play. That differentiation is healthy, but all four are converging on the same operational requirements: restart-safe sessions, async runtime isolation, provider abstraction, MCP robustness, and least-privilege tool access.

## 6. Community Momentum & Maturity

**Tier 1 — hyper-scale, release-engineered:** **OpenClaw**. Highest activity by far, 225 merged/closed PRs in 24h, three releases including an LTS-equivalent. It is rapidly iterating, but maturity is uneven: strong release engineering and community reproduction coexist with multiple P0 stability issues and missing visible fix PRs on top blockers.

**Tier 2 — high-velocity stabilizers:** **QwenPaw**, **ZeroClaw**, and **Hermes Agent**.
- **QwenPaw** is the most responsive fixer in the sample: 20 issues closed, 11 PRs merged/closed, and several same-day/near-day issue→PR pairs. Its constraint is review bandwidth, with 39 open PRs and several multi-week `size/XXXL` efforts.
- **ZeroClaw** has strong release discipline—v0.8.7 with 81 commits from 19 contributors—and a clear v0.9.0 security/identity theme. The bottleneck is review/security triage: 41 open PRs vs 1 merged/closed, and several security findings have no fix PR yet.
- **Hermes Agent** is triage-heavy rather than merge-heavy: 47 open issues, 43 open PRs, 7 merged/closed, no release. The Webapp post-merge aftershock and session-ownership cluster suggest it is stabilizing under architectural debt rather than shipping net-new surface area.

**Tier 3 — inactive window:** **IronClaw** had no activity in the last 24h; no momentum or maturity judgment is possible from this digest.

## 7. Trend Signals

| Industry trend | Evidence from community feedback | Value for AI agent developers |
|---|---|---|
| **Session continuity is becoming the product** | Hermes’ top issues are “listed but unopenable” sessions across Desktop/mobile/Webapp/TUI; OpenClaw has restart/session/DB recovery work; ZeroClaw loses prompts on reload; QwenPaw sessions break after provider switches. | Design session identity, ownership, and a resumable event log early. Treat “one conversation across surfaces and restarts” as a core contract, not a UI feature. |
| **Reliability is now a feature, not an ops afterthought** | OpenClaw event-loop blocking, SQLite stalls, update/Doctor deadlocks; Hermes gateway stalls under load; QwenPaw compaction aborts; ZeroClaw poisoned MCP sessions. | Build async persistence, bounded queues, health checks, rollback paths, and diagnostic preservation into the runtime from day one. |
| **Provider abstraction must be capability-based, not model-name-based** | OpenClaw quota-aware fallback; QwenPaw thinking-disable/wire-format quirks; ZeroClaw Codex streaming and reasoning effort; Hermes ChatGPT sign-in provider. | Implement typed provider capabilities, quota-aware fallback, OAuth/subscription auth, and per-provider error normalization. |
| **MCP/tool interoperability is the integration battleground** | QwenPaw legacy MCP negotiation failures; ZeroClaw poisoned MCP sessions and stale sockets; OpenClaw tool catalog gaps and MCP spawn caps; Hermes tool-argument schema failures. | Invest in MCP negotiation fallback, session recovery, schema validation, and consistent tool discovery across turn types. |
| **Security is shifting left into the agent runtime** | ZeroClaw’s OIDC/XFF/SOP/principal-scoping findings; OpenClaw sandboxing and owner-tool fixes; Hermes approval allowlist bypass; QwenPaw audit-log integrity and out-of-workspace writes. | Adopt principal-aware tool access, audit trails, sandboxing, CSP, and secure defaults for plugins, hooks, subagents, and messaging surfaces. |
| **Multi-surface parity and accessibility are becoming baseline expectations** | Hermes mobile keyboard/viewport issues; OpenClaw TUI screen-reader mode and iOS sharing; QwenPaw Windows/Linux packaging; ZeroClaw Android shell failure. | Share one protocol across clients, but budget for mobile input, viewport, accessibility, packaging, and platform-specific lifecycle behavior. |
| **Maintainer review bandwidth is the scaling constraint** | QwenPaw’s 39 open PRs and multi-week XXXL work; ZeroClaw’s 41 open PRs; Hermes’ 43:7 open-to-merged ratio; OpenClaw’s P0s without visible fix PRs. | Modular ownership, design-doc gates for large changes, and triage automation matter as much as contributor growth once a project reaches mid-scale. |

**Bottom line:** the ecosystem is moving from “agent demos” to “agent infrastructure.” The winners will be projects that make sessions portable, runtimes non-blocking, providers interchangeable, MCP recoverable, and permissions auditable—without losing multi-surface usability or release velocity.

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest
**Date:** 2026-10-28 · **Repo:** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. Today's Overview

Hermes Agent is in a heavy **stabilization phase**: 100 issues and PRs were touched in the last 24 hours (50 issues — 47 open / 3 closed; 50 PRs — 43 open / 7 merged or closed), with **no new releases**. Activity is overwhelmingly *inbound bug triage* rather than shipping — the open-to-merged ratio (43:7) reflects a maintainer queue that is accumulating faster than it is draining. The dominant signal is a **post-merge aftershock from the browser-hosted Webapp (#141864, merged 2026-10-25)**, which now has both a dedicated tracking issue ([#144648](https://github.com/NousResearch/hermes-agent/issues/144648)) and multiple child PRs in flight. A second, larger structural theme cuts across the whole report: **session identity, ownership and cross-surface resumption are broken in several distinct ways**, suggesting an architectural gap in the canonical session authority rather than a set of isolated defects. Overall health reads as *high-velocity but strained* — strong triage discipline (severity tags, component labels, `sweeper:risk-*` markers), but a growing P2 backlog with few corresponding fix PRs.

---

## 2. Releases

**None.** No new releases in the last 24 hours, and no `Latest Releases` entries in the data window. Users continue to reference current lines as `v0.22.5 (stable, bf0203a794a0)` and `v0.21.5+6653`. No breaking changes, migration notes, or deprecations to report today.

---

## 3. Project Progress

Seven PRs were merged or closed in the last 24h; the visible subset:

| PR | Status | Impact |
|---|---|---|
| [#145770](https://github.com/NousResearch/hermes-agent/pull/145770) | Closed | **Webapp plugin install unblocked** — plugin catalog/Git inspection failed with *"Plugin inspection is unavailable in this environment"* in `hermes webapp`; now routed through the gateway. Affected every agent plugin. |
| [#145753](https://github.com/NousResearch/hermes-agent/pull/145753) | Closed | **Installer race fixed** — a launcher waiting on a reclaimed install-state lock now retries on the live file rather than a deleted one; prevents two installers running in one state. Follow-up to #145527. |
| [#145772](https://github.com/NousResearch/hermes-agent/pull/145772) | Closed | **CI red cleared** — Windows arm64 failure caused by a home rescan re-importing a hosted provider plugin. |
| [#86329](https://github.com/NousResearch/hermes-agent/pull/86329) | Closed (duplicate) | Upstream provider 403 detection in `error_classifier.py`; superseded. |
| [#86612](https://github.com/NousResearch/hermes-agent/pull/86612) | Closed (stale) | Auto-discovery registry mappings for Mistral, Cohere, DeepInfra, SiliconFlow; landed elsewhere. |

**Closed issues of note:** [#143331](https://github.com/NousResearch/hermes-agent/issues/143331) (`hermes update` failing every run on Windows 11 due to stale `gateway.pid` + held lock) and [#143561](https://github.com/NousResearch/hermes-agent/issues/143561) (gateway `/api/status`, tickets and `session.resume` timing out on a load-30–100 host; a patch gist was supplied by the reporter). Both were high-friction operational blockers for existing users.

**Net assessment:** the day's shipping output was *unblocking and cleanup*, not feature delivery. Three of the five visible closures are infrastructure/CI hygiene.

---

## 4. Community Hot Topics

**1. [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — 24 comments, 👍2 · Remote-backend desktop sessions unresumable** *(P2, `sweeper:risk-session-state`)*
Conversations created by Hermes Desktop against a remote `hermes serve` backend are persisted with `source='desktop'`, but once the hosting process restarts, `session.resume` returns `not_found`. This is the single most-discussed item in the window and the anchor of the session-identity cluster below.

**2. [#144648](https://github.com/NousResearch/hermes-agent/issues/144648) — 10 comments · Tracking: Webapp post-merge follow-ups** *(type/security, P2)*
The maintainer-owned umbrella for #141864. Explicitly frames the Webapp as sharing one gateway across browser tabs, CLI, native Desktop and messaging — "every surface sees the same c[onversation]". Child work already visible: [#145788](https://github.com/NousResearch/hermes-agent/pull/145788) (mobile tab picker menu), [#145573](https://github.com/NousResearch/hermes-agent/pull/145573) (CSP for the Webapp page, WA-11), [#145803](https://github.com/NousResearch/hermes-agent/pull/145803) (Android `insertText`).

**3. [#112028](https://github.com/NousResearch/hermes-agent/issues/112028) — 8 comments, 👍1 · Multi-surface access to the same session** *(P2, `needs-decision`)*
Server rejects a second surface (mobile dashboard) writing to a session already open on Desktop. Oldest and most explicit statement of the multi-surface requirement.

**4. [#144814](https://github.com/NousResearch/hermes-agent/issues/144814) — 7 comments, 👍1 · hermes-mobile sessions never resumable from native Desktop**
Transcripts render in the sidebar, but opening them fails with `JsonRpcGatewayError: not_found`. Same root cause, different client pairing.

**5. [#141854](https://github.com/NousResearch/hermes-agent/issues/141854) — 6 comments · Messaging "Needs setup" forever**
Profile-scoped `_platform_enablement` requires a required env var, so token-free platforms (WhatsApp, webhook, A2A) always report `configured: false`.

**Underlying need analysis:** four of the five hottest items describe the *same user expectation* — **one conversation, reachable from any device or surface, under one identity**. Today's implementation treats `source`/`login` as an ownership stamp, so a session written by `desktop`, `hermes-mobile`, `ios`, or a dashboard basic-auth login is visible-but-unopenable elsewhere. Users read this not as a permission model but as data loss. Related: [#145074](https://github.com/NousResearch/hermes-agent/issues/145074) ("one person, several logins" — chat history partitioned into invisible users) and [#143831](https://github.com/NousResearch/hermes-agent/issues/143831) (0.22.4 regression permanently orphaning legacy custom-`source` labels). This is the highest-leverage architectural decision on the board.

---

## 5. Bugs & Stability

Ranked by severity and blast radius. **Note: most P2s below have no linked fix PR.**

### Critical / Data-integrity or silent-failure class
| Issue | Sev | Summary | Fix PR? |
|---|---|---|---|
| [#145155](https://github.com/NousResearch/hermes-agent/issues/145155) | P2 | **cron `context_from` injects days-old runs** — archive frame off-by-one makes `_archive_answer()` return `None`; a job consumed a 7-day-old `(FAILED)` report while believing it was current. Worst kind of failure: silent and confident. | ❌ |
| [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) | P2 | Remote-backend desktop sessions permanently unresumable after `serve` restart (`sweeper:risk-session-state`). | ❌ |
| [#131421](https://github.com/NousResearch/hermes-agent/issues/131421) | P2 | Desktop: send + immediate session switch **silently cancels the send**; text bounces back to composer with no error or toast (`sweeper:risk-session-state`). | ❌ |
| [#143907](https://github.com/NousResearch/hermes-agent/issues/143907) | P2 | Desktop chat unresumable from terminal TUI — login-stamped rows → `not_found`, binding-stamped rows → `permission_denied`; **TUI then silently starts a new session** instead of erroring. | ❌ |
| [#145022](https://github.com/NousResearch/hermes-agent/issues/145022) | P2 | Kanban workers crash on gateway stall: *"Kanban execution unknown; retained by owner"*; one stall killed 4 workers, in-progress work lost. | ❌ |

### High — functional breakage with broad surface impact
| Issue | Sev | Summary | Fix PR? |
|---|---|---|---|
| [#145063](https://github.com/NousResearch/hermes-agent/issues/145063) | P2 | **Tool-call args drop fields / pick wrong action** for `anyOf`-branch or optional-payload params (repro'd on deepseek-v4.1-flash, OpenRouter). Agent-correctness class. | ❌ |
| [#143256](https://github.com/NousResearch/hermes-agent/issues/143256) | P2 | One 5s supervisor thread **per multiplexed profile even with zero rooms** — exhausts read pool, starves event loop. | ❌ |
| [#145761](https://github.com/NousResearch/hermes-agent/issues/145761) | P2 | `gateway_state.json` reports `session_store: unavailable` for hours while all 28 profile DBs are provably readable. | ❌ |
| [#143874](https://github.com/NousResearch/hermes-agent/issues/143874) | P2 | Desktop **discards the cause** of every gateway-ticket mint failure — dead owner, timeout and crash all read `Gateway ticket bootstrap failed` (blame: `apps/desktop/electron/local-gateway.ts:654`). | ❌ |
| [#143831](https://github.com/NousResearch/hermes-agent/issues/143831) | P3 | 0.22.4 **regression**: legacy session adoption rejects custom `source` labels; previously resumable sessions now permanently `not_found`. | ❌ |
| [#145474](https://github.com/NousResearch/hermes-agent/issues/145474) | P3 | iOS (`source='ios'`) sessions cannot be adopted — rename/pin/archive all `not_found` despite resolving via `get_session`. | ❌ |

### Medium — UX / correctness on new surfaces
| Issue | Sev | Summary | Fix PR? |
|---|---|---|---|
| [#145593](https://github.com/NousResearch/hermes-agent/issues/145593) | P2 | Webapp chat: long user messages render fully expanded; on mobile ~90% of viewport, agent reply squeezed to a sliver. | ❌ |
| [#145296](https://github.com/NousResearch/hermes-agent/issues/145296) | P2 | Dashboard chat on Android: soft-keyboard input dropped — `screenReaderMode: true` makes xterm ignore `insertText`. | ✅ [#145803](https://github.com/NousResearch/hermes-agent/pull/145803) |
| [#145183](https://github.com/NousResearch/hermes-agent/issues/145183) | P2 | Linux desktop window is translucent but the Settings row is hidden (`TRANSLUCENCY_SUPPORTED = false`) — **no UI escape hatch**. | ❌ |
| [#142811](https://github.com/NousResearch/hermes-agent/issues/142811) | P2 | `clarify` tool answers disappear from the visible transcript after submission. | ❌ |
| [#145472](https://github.com/NousResearch/hermes-agent/issues/145472) | P2 | Desktop reactions persist but are **never announced to the agent** on the canonical gateway path. | ❌ |
| [#145286](https://github.com/NousResearch/hermes-agent/issues/145286) | P2 | Canonical `session.create` refusals send `data.fields`; CLI reads `data.field` — policy refusal renders as *"couldn't start the model connection"*. | ❌ |
| [#141854](https://github.com/NousResearch/hermes-agent/issues/141854) | P2 | Messaging page shows "Needs setup" forever for token-free platforms. | ❌ |
| [#30989](https://github.com/NousResearch/hermes-agent/issues/30989) | P2 | **Long-lived (since 2026-05-23):** BlueBubbles text+attachment arrive as two events, interrupting the in-progress turn — no debounce/merge like Telegram. | ✅ partial: [#145795](https://github.com/NousResearch/hermes-agent/pull/145795) adjacent |

### Security
| Issue | Sev | Summary | Fix PR? |
|---|---|---|---|
| [#145763](https://github.com/NousResearch/hermes-agent/issues/145763) | **P3 security** | `approval` allowlist shortcut treats single-quoted `$(…)`/backticks inside `test -v` / `[[ -v …]]` subscripts as literal, **but bash re-expands it**. Allowlist is consulted *before* the dangerous-pattern detector (`tools/approval.py:1108`). | ❌ |
| [#144648](https://github.com/NousResearch/hermes-agent/issues/144648) | P2 security | Webapp CSP absent on the main page (WA-11). | ✅ [#145573](https://github.com/NousResearch/hermes-agent/pull/145573) |

### Stability positives
- [#19821](https://github.com/NousResearch/hermes-agent/issues/19821) (**since 2026-05-04**) — QQ Bot WebSocket silently dies; adapter waits on a dead connection for 18+ hours. Zombie-connection class: no close frame, no error, just radio silence.
- [#145664](https://github.com/NousResearch/hermes-agent/issues/145664) — Telegram token redaction **false positive**: masks digit suffixes inside ordinary identifiers, corrupting legitimate tool output.

---

## 6. Feature Requests & Roadmap Signals

| Signal | Issue/PR | Read |
|---|---|---|
| **Multi-surface live session** | [#112028](https://github.com/NousResearch/hermes-agent/issues/112028), [#136319](https://github.com/NousResearch/hermes-agent/issues/136319), [#145074](https://github.com/NousResearch/hermes-agent/issues/145074) | Highest-demand roadmap item. `needs-decision` tag confirms it is unresolved, not rejected. |
| **Installable PWA dashboard** | [#145801](https://github.com/NousResearch/hermes-agent/pull/145801) | Adds manifest + service worker + 192/512px icons so Chrome offers "Install app". Low-risk, high-visibility. |
| **In-app Browser for the Desktop/web build** | [#145798](https://github.com/NousResearch/hermes-agent/pull/145798) | Floating browser, start page and address bar so agent-served output (dev servers, HTML reports) opens in-app instead of kicking to the system browser. Strong daily-driver ergonomics. |
| **Local runtime: LFM3 1.7B** | [#145800](https://github.com/NousResearch/hermes-agent/pull/145800) | Ties to released llama.cpp `b11940` + Desktop engine gates; expands the managed-local-model story. |
| **"Sign in with ChatGPT" provider** | [#128926](https://github.com/NousResearch/hermes-agent/pull/128926) | Opt-in `openai-chatgpt` provider using public-client registration. Carries `sweeper:risk-security-boundary` — auth surface review pending. |
| **i18n completion** | [#145804](https://github.com/NousResearch/hermes-agent/pull/145804) | Managed-workspace snapshots omit `locales/`, so **every user sees raw keys** (`gateway.kanban.ping.completed`) in chat. |

**Prediction for next version:** the i18n snapshot fix, PWA manifest, Android `insertText` fix, and the webapp CSP are all small, self-contained and already posted — strong candidates for the next patch cut. Multi-surface session ownership is a larger design change and unlikely to land as a fix; expect a design doc or `needs-decision` resolution first.

---

## 7. User Feedback Summary

**Satisfaction drivers visible in the data:** users are running genuinely demanding workloads — multi-profile gateways (3 and 28 profile installs appear), load-30–100 macOS hosts, 200+ gateway threads, cron chains, Kanban worker fleets, remote `hermes serve` behind basic auth, and third-party mobile clients built on the protocol (Hy4ri's Android client, an iOS "Cadu" client). The fact that people are building *clients* against Hermes is a strong ecosystem signal.

**Dissatisfaction / pain points, in order of volume:**

1. **"My conversation vanished."** The dominant complaint. Sessions are *listed* but *unopenable*, and several reports note the failure is silent (TUI starts a fresh session; Desktop send is cancelled with no toast). Users perceive this as data loss even when rows exist.
2. **Opaque failure messages.** [#143874](https://github.com/NousResearch/hermes-agent/issues/143874) ("every ticket failure reads the same string"), [#145286](https://github.com/NousResearch/hermes-agent/issues/145286) (policy refusal rendered as a model-connection error), and [#143561](https://github.com/NousResearch/hermes-agent/issues/143561) all describe the same frustration: the system knows *why* it failed and throws that away one line earlier.
3. **Operational fragility on Windows and loaded hosts.** Stale `gateway.pid` breaking `hermes update` every run, plus gateway timeouts under load, were both closed today — but they are exactly the kind of issues that drive users off the stable channel.
4. **Mobile is second-class.** Android soft keyboard drops input, long messages eat 90% of the viewport, per-tab menus are unreachable on touch. Three separate issues, one theme.
5. **First-run / setup confusion.** "Needs setup" pills on platforms that are already enabled undermines trust in the settings surface.

**Engagement caveat:** reaction counts are very low across the board (maximum 👍 is 2 on a 24-comment issue). Discussion volume is carried by a small number of deeply-invested power users rather than broad community voting — worth weighing when prioritizing by 👍.

---

## 8. Backlog Watch

Critical items aging without maintainer response or a linked fix:

| Item | Age | Why it needs attention |
|---|---|---|
| [#19821](https://github.com/NousResearch/hermes-agent/issues/19821) QQ Bot WebSocket zombie | **~177 days** (opened 2026-05-04) | Adapter hangs on dead connections for 18+ hours; still updated today, still unassigned. Same zombie-connection risk class likely applies to other WebSocket platform adapters. |
| [#30989](https://github.com/NousResearch/hermes-agent/issues/30989) BlueBubbles attachment interruption | **~158 days** (opened 2026-05-23) | Telegram already solved this with debounce/merge; the fix pattern is known and unimplemented. Only an adjacent PR ([#145795](https://github.com/NousResearch/hermes-agent/pull/145795)) touches BlueBubbles config, not the event merge. |
| [PR #85041](https://github.com/NousResearch/hermes-agent/pull/85041) Local TTS optimization playbook | **~76 days** (opened 2026-08-13) | Docs-only skill with hard production numbers (377s → 100.2s, RTF 0.53 on Qwen3-TTS, Apple Silicon). Zero friction to merge; currently going stale. |
| [PR #128926](https://github.com/NousResearch/hermes-agent/pull/128926) Sign in with ChatGPT provider | **~28 days** (opened 2026-09-30) | Blocked on `sweeper:risk-security-boundary` review. High user value; a stalled security review is the wrong bottleneck. |
| [PR #136334](https://github.com/NousResearch/hermes-agent/pull/136334) `GET /api/messaging/platforms` config re-parse | **~18 days** (opened 2026-10-10) | Re-parses `config.yaml` once per platform **while holding the profile lock** — direct contributor to the dashboard stalls reported in #143561 and #143256. Author kept authorship after salvaging #124071; deserves priority given the perf cluster. |
| [#136319](https://github.com/NousResearch/hermes-agent/issues/136319) Mobile read-only mirror of live desktop session | 18 days, P3 `needs-decision` | A pragmatic, scoped stepping stone toward #112028 (read-only mirror + input routed as local input) that would satisfy several duplicate reports cheaply. |
| [#144648](https://github.com/NousResearch/hermes-agent/issues/144648) Webapp tracking issue | Active, P2 | Not stale — flagged because it is now the single coordination point for a fast-growing child set (CSP, mobile tab menus, input handling). Worth a maintainer checklist to prevent the follow-ups from scattering. |

---

**Bottom line:** Hermes Agent is technically ambitious and its users are pushing it hard, but the project is currently paying down a session-ownership debt that generates the majority of its top issues, while the freshly-shipped Webapp produces a predictable tail of accessibility and mobile polish work. Velocity on small fixes is good; the risk is that the P2 session-resume cluster keeps compounding without an architectural decision.

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-10-28

## 1. Today's Overview

QwenPaw is in a high-velocity maintenance phase: 36 issues were touched in the last 24h with a **20 closed / 16 open** split, and 50 PRs saw activity with **11 merged or closed** against 39 still open. A new beta, **v2.3.0-beta.1**, shipped with console rendering and tool-credential fixes. Activity skews heavily toward **stability hardening** — context compaction, provider wire-format compatibility, MCP protocol negotiation, and desktop packaging — rather than net-new surface area. Triage responsiveness is strong (several issues reported 2026-10-25/26 already have candidate fix PRs), but the open PR queue contains a cluster of multi-week `size/XXXL` efforts awaiting review. Overall health: **active and responsive, with a growing long-lived PR backlog**.

## 2. Releases

**v2.3.0-beta.1** ([release](https://github.com/agentscope-ai/QwenPaw/releases))

Visible changelog items (excerpt truncated upstream):
- `fix(console)`: restore interactive HTML previews — [PR #8245](https://github.com/agentscope-ai/QwenPaw/pull/8245)
- `feat(tools)`: optional Tavily API key via credential store ([#8174](https://github.com/agentscope-ai/QwenPaw/issues/8174)) — [PR #8204](https://github.com/agentscope-ai/QwenPaw/pull/8204)
- `fix(plugin)`: alignment fix (entry truncated in source data)

**Breaking changes / migration notes:** none stated in the available excerpt. As a **beta on the 2.3.0 line**, it should be treated as pre-stable; the Tavily credential-store change is config-adjacent and users relying on env-var-only Tavily setup should verify behavior. The full changelog is truncated in the provided data, so the above is not exhaustive.

## 3. Project Progress

**Closed/merged today (visible subset of 11):**
- [PR #8458](https://github.com/agentscope-ai/QwenPaw/pull/8458) — desktop CSP now allows embedded `data:` fonts (KaTeX math glyphs were being refused by the webview).
- [PR #8449](https://github.com/agentscope-ai/QwenPaw/pull/8449) — e2e test repair.

**Notable issue closures indicating shipped or resolved work:**
- [PR #8245 / #8204](https://github.com/agentscope-ai/QwenPaw/pull/8245) landed into v2.3.0-beta.1 (console HTML preview, Tavily key).
- **MCP config UX:** [#8405](https://github.com/agentscope-ai/QwenPaw/issues/8405) (modal could not close for `http`/`sse` transports) closed.
- **Context/tool-call integrity:** [#5856](https://github.com/agentscope-ai/QwenPaw/issues/5856) (tool_call structure lost during compaction → 400s) closed; [#2758](https://github.com/agentscope-ai/QwenPaw/issues/2758) (reasoning/tool output ordered after final reply) closed.
- **Provider resilience:** [#7935](https://github.com/agentscope-ai/QwenPaw/issues/7935) (LLM `Request timed out` never auto-recovers) closed as *Cannot Reproduce*.
- **Ecosystem requests accepted/closed:** [#4124](https://github.com/agentscope-ai/QwenPaw/issues/4124) and [#1536](https://github.com/agentscope-ai/QwenPaw/issues/1536) (OAuth login for OpenAI/Codex), [#8199](https://github.com/agentscope-ai/QwenPaw/issues/8199) (Firecrawl as `web_search` provider), [#7079](https://github.com/agentscope-ai/QwenPaw/issues/7079) (PowerContext long-term memory backend).
- **Security-relevant:** [#7727](https://github.com/agentscope-ai/QwenPaw/issues/7727) — out-of-workspace write hard-block was blind to kimi-code's `Write` tool path fields; closed.
- **Runtime stability:** [#8279](https://github.com/agentscope-ai/QwenPaw/issues/8279) (AnyIO thread-limiter exhaustion from ReMe watchers), [#8290](https://github.com/agentscope-ai/QwenPaw/issues/8290) (media blocks permanently wedging sessions), [#8185](https://github.com/agentscope-ai/QwenPaw/issues/8185) (Windows `desktop.log` never rotating), [#7963](https://github.com/agentscope-ai/QwenPaw/issues/7963) (Langfuse tool output never recorded).

## 4. Community Hot Topics

| Item | Comments | State | Link |
|---|---|---|---|
| #1536 OAuth login for Codex | 5 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/1536) |
| #8405 MCP config modal can't close (http/sse) | 5 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/8405) |
| #8312 Context control failure aborts tasks | 4 | **OPEN** | [issue](https://github.com/agentscope-ai/QwenPaw/issues/8312) |
| #7935 LLM timeout never recovers | 4 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/7935) |
| #5856 tool_call structure lost in compaction | 4 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/5856) |
| #2758 Reasoning/tool output shown after reply | 4 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/2758) |
| #4124 OAuth login for OpenAI/Codex (feature) | 4 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/4124) |

**Underlying needs:**
- **Subscription-based auth, not just API keys.** The duplicate OAuth/Codex threads (#1536 question + #4124 feature request) reveal demand for ChatGPT/Codex-plan sign-in — a cost and access issue, not a technical nicety.
- **Long-session reliability is the #1 friction.** Every high-comment bug cluster (#8312, #5856, #7935) is about sessions degrading: compaction breaking tool_call structure, context overflow killing tasks, and timeouts requiring a process restart.
- **MCP interoperability maturity.** #8405 is UI-level, but the #8047/#8424 family is protocol-level — legacy-only MCP servers fail negotiation, blocking enterprise tool integrations.
- **Output transparency.** #2758 (display ordering inverted) reflects users actively reading agent traces and wanting reasoning *before* the answer.

PRs show no comment counts in the dataset (`undefined`), so prioritization below uses scope and review state rather than reactions.

## 5. Bugs & Stability

**Critical**
- [#8312](https://github.com/agentscope-ai/QwenPaw/issues/issues/8312) *(OPEN)* — Context compaction cannot fit the active request (252,565 > 251,904 tokens) and the task is **aborted outright**. Directly blocks long-running agent work. No fix PR visible yet.
- [#8412](https://github.com/agentscope-ai/QwenPaw/issues/8412) *(OPEN)* — `governance/audit.db` silently **stops recording tool calls per-agent** despite `audit_level: all`, and never resumes. Audit-trail integrity risk for enterprise deployments.
- [#7966](https://github.com/agentscope-ai/QwenPaw/issues/7966) *(OPEN)* — After a provider switch, `file://` media URLs in history are rejected with `invalid_parameter_error`, **permanently breaking the session**.

**High**
- [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417) *(OPEN)* — `/compact` always fails with 400 on Volcano Engine `glm-5.3-flash`: compat layer sends both thinking-disable shapes, and one is rejected. **Fix PR: [#8436](https://github.com/agentscope-ai/QwenPaw/pull/8436)** (honor declared thinking-disable wire).
- [#8361](https://github.com/agentscope-ai/QwenPaw/issues/8361) *(OPEN)* — With Automatic Memory Search on, `memory_search` repeats within one turn; after the guard blocks the 4th call, **no final reply is produced**. **Fix PR: [#8457](https://github.com/agentscope-ai/QwenPaw/pull/8457)**.
- [#8424](https://github.com/agentscope-ai/QwenPaw/issues/8424) *(OPEN)* — `server/discover` returning HTTP 404 + JSON-RPC -32601 is hard-failed as "modern server", so legacy MCP servers never fall back to `initialize`. Same family as closed [#8047](https://github.com/agentscope-ai/QwenPaw/issues/8047); the pattern is recurring.
- [#8413](https://github.com/agentscope-ai/QwenPaw/issues/8413) *(OPEN)* — Self-kill guard false positive: any command with `kill` + `$pid` is blocked, including user-started processes.
- [#8440](https://github.com/agentscope-ai/QwenPaw/issues/8440) *(OPEN)* — `cancellable_wait()` leaves the owned tool task running after outer cancellation (reproduced on `2.2.3b1` / `main`).

**Medium**
- [#8265](https://github.com/agentscope-ai/QwenPaw/issues/8265) *(OPEN)* — Plugin hot-install aborts when `importlib.invalidate_caches()` raises on distro-patched Python 3.11; blocks third-party plugin authors.
- [#8406](https://github.com/agentscope-ai/QwenPaw/issues/8406) *(OPEN)* — QQ channel `enabled` flag silently resets to false with no warning.
- [#8306](https://github.com/agentscope-ai/QwenPaw/issues/8306) *(OPEN)* — Atomic writer appends 22 bytes for temp basename, so legal long filenames are rejected.
- [#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415) *(CLOSED)* — `edit_file` converted CRLF→LF and wrote back lossy ISO-8859-1 output. **Fix PR: [#8433](https://github.com/agentscope-ai/QwenPaw/pull/8433)** (preserve line endings and encoding).
- [#8425](https://github.com/agentscope-ai/QwenPaw/issues/8425) *(CLOSED)* — Mail monitor built `name@domain@domain` → `AUTHENTICATIONFAILED`; a **migration/regression** breaking pre-existing configs after upgrade.

**Fixed/closed today (regression watch):** the media-wedge family ([#8290](https://github.com/agentscope-ai/QwenPaw/issues/8290)) is now on its *fourth* variant (#8171, #7015, #7024) — wording-based classifiers keep missing new provider rejection strings, a structural fragility worth an explicit fix.

## 6. Feature Requests & Roadmap Signals

**New today**
- [#8459](https://github.com/agentscope-ai/QwenPaw/issues/8459) *(OPEN)* — Console lacks easy access to **unread push messages**. Low effort, high UX value.

**In flight (PRs)**
- [#8434](https://github.com/agentscope-ai/QwenPaw/pull/8434) — **Quick Add Model** button in Models page ([#4036](https://github.com/agentscope-ai/QwenPaw/issues/4036)): collapses 5 interactions across 3 screens into one.
- [#8444](https://github.com/agentscope-ai/QwenPaw/pull/8444) — **Linux `.deb` + AppImage** desktop packaging.
- [#8443](https://github.com/agentscope-ai/QwenPaw/pull/8443) — Age out `tool_result` blocks after N days: on a production workspace, `blocks` JSON was **~75% of a 976 MB `history.db`**.
- [#7785](https://github.com/agentscope-ai/QwenPaw/pull/7785) — **Realtime voice chat** with interruption and model selection.
- [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) — Nested (categorised) skill directories, `<root>/<category>/<skill>/SKILL.md`.
- [#8187](https://github.com/agentscope-ai/QwenPaw/pull/8187) — Extract channels into plugins with **subprocess isolation**.
- [#7903](https://github.com/agentscope-ai/QwenPaw/pull/7903) — Embedded community feed + inbox with PKCE platform auth.
- [#8447](https://github.com/agentscope-ai/QwenPaw/pull/8447) — Refresh non-OpenAI model catalog and provider configuration.
- [#8453](https://github.com/agentscope-ai/QwenPaw/pull/8453) — DingTalk Open API fallback when `sessionWebhook` expires (errcode 300001).

**Prediction for the 2.3.0 line:** the stable release most likely absorbs the console fixes, `#8436` (thinking-disable wire), `#8457` (memory recall as reference context), `#8433` (edit_file encoding), `#8453` (DingTalk fallback), `#8434` (Quick Add Model), and `#8455` (Windows installer diagnostics). Voice chat, channel plugin isolation, community/inbox, and Linux desktop are larger and likely targeted at 2.4+. OAuth/Codex login (#4124/#1536) is now closed on both entry points — a strong signal it is queued or landed.

## 7. User Feedback Summary

**Pain points, by frequency:**
1. **Long sessions break.** Compaction (#8312, #8417, #5856), session wedging (#7966, #8290), and non-recovering timeouts (#7935) dominate. Users repeatedly note these are **not new** — reported across 2.2.x and earlier.
2. **Provider compatibility is brittle.** Strict-compatible endpoints reject duplicated or unrecognized params (#8417, #8436); provider switches invalidate history (#7966); model catalogs drift (#8447).
3. **MCP is the integration battleground.** Both UI (#8405) and protocol negotiation (#8424, #8047) fail in ways that silently render servers `inactive` with Console 503s.
4. **Enterprise/governance gaps.** Audit logging silently lapsing (#8412) and out-of-workspace write bypasses (#7727) are exactly the failures regulated users cannot tolerate.
5. **Guard rails over-trigger.** Self-kill detection (#8413) blocks legitimate `kill $PID`; long filenames are rejected by atomic-write temp naming (#8306).
6. **Windows desktop remains second-class.** Log rotation (#8185), installer diagnostics (#8455), disappearing responses (#3281), and CSP font blocking (#8458) all cluster here.
7. **Cost/access friction.** The OAuth/Codex thread pair is the clearest "I want to use my existing subscription" signal.

**Satisfaction signals:** fast turnaround is visible — [#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415) reported 10-25, fix PR [#8433](https://github.com/agentscope-ai/QwenPaw/pull/8433) opened 10-26; [#8361](https://github.com/agentscope-ai/QwenPaw/issues/8361) → [#8457](https://github.com/agentscope-ai/QwenPaw/pull/8457); [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417) → [#8436](https://github.com/agentscope-ai/QwenPaw/pull/8436). Reporters submit detailed repros with commit hashes, indicating an engaged, technically sophisticated user base.

**Dissatisfaction signals:** recurrence of the same bug families (media wedging, compaction 400s, MCP negotiation) suggests point fixes rather than structural hardening; "upgrade broke my working config" appears twice (#8425 mail auth, #8406 QQ flag reset).

## 8. Backlog Watch

**Open issues needing maintainer attention:**
- [#8312](https://github.com/agentscope-ai/QwenPaw/issues/8312) — open since 2026-10-18, 4 comments, **no linked fix PR**, and it aborts tasks. Highest priority orphan.
- [#7966](https://github.com/agentscope-ai/QwenPaw/issues/7966) — open since 2026-09-24, session permanently broken after provider switch; no PR.
- [#8265](https://github.com/agentscope-ai/QwenPaw/issues/8265) — open since 2026-10-15; blocks plugin authors on patched Python builds (ecosystem-level impact).
- [#8424](https://github.com/agentscope-ai/QwenPaw/issues/8424) — open, and part of a recurring MCP negotiation family; deserves a consolidated fix rather than per-report patches.
- [#8306](https://github.com/agentscope-ai/QwenPaw/issues/8306) — open since 2026-10-18, low-effort atomic-writer fix with no PR.
- [#7935](https://github.com/agentscope-ai/QwenPaw/issues/7935) — closed as *Cannot Reproduce*, but the reporter described a full process-wide lockup; a repro-hunting note or defensive timeout-reset would prevent recurrence.

**Long-lived PRs awaiting review (scope vs. review bandwidth):**
- [#7874](https://github.com/agentscope-ai/QwenPaw/pull/7874) `size/XXXL` — PawApp SDK/control-plane redesign, open since 2026-09-19.
- [#7785](https://github.com/agentscope-ai/QwenPaw/pull/7785) `size/XXXL` — realtime voice chat, open since 2026-09-15 (oldest open PR in the visible set).
- [#7903](https://github.com/agentscope-ai/QwenPaw/pull/7903) `size/XXXL` `[wip]` — community/inbox, open since 2026-09-20.
- [#8187](https://github.com/agentscope-ai/QwenPaw/pull/8187) `size/XXXL` — channel plugin extraction with subprocess isolation, open since 2026-10-12.
- [#8034](https://github.com/agentscope-ai/QwenPaw/pull/8034) `size/L` — bound inline media per *request*, not per file; directly relevant to the #8290/#8171 media-wedge family, open since 2026-09-29.
- [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) `size/XL`, first-time contributor — nested skill directories, open since 2026-10-11.
- [#8210](https://github.com/agentscope-ai/QwenPaw/pull/8210) `size/S`, first-time contributor — Windows Creator project delete (`WinError 5`), open since 2026-10-13; small and low-risk.

**Recommendation:** the ratio of 39 open PRs to 11 closed in 24h, combined with four multi-week `XXXL` PRs, indicates review bandwidth — not contributor supply — is the current constraint. Triage of `size/S`–`size/M` first-time-contributor PRs (#8210, #8454, #8178) and a consolidated fix for the MCP-negotiation and media-classifier families would yield the highest health-per-hour.

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-10-28

## 1. Today's Overview

ZeroClaw remains highly active: **22 issues and 42 PRs were updated in the last 24 hours**, alongside the **v0.8.7 security release**. The project closed only **one issue (#9101)** and **one PR (#12019)**, while 21 issues and 41 PRs remain open, indicating that contribution volume is outpacing merge/review throughput. Activity is concentrated in security and identity-access hardening, v0.9.0 runtime/gateway work, and reliability fixes for MCP, Android, Discord, and OpenAI Codex. Overall project health is strong on community engagement and release cadence, but the growing open-PR backlog is the main operational constraint.

*Data caveat: PR comment counts were provided as `undefined`, so PR discussion activity cannot be ranked reliably. The v0.8.7 changelog excerpt is truncated.*

---

## 2. Releases

### v0.8.7 — Security and maintenance follow-up to v0.8.6
- **Scope:** 81 commits from 19 contributors.
- **Key changes:**
  - Moves to patched **Wasmtime** and **Rustls certificate verification**.
  - Updates **`tokio`** and **`ctutils`** for advisories affecting v0.8.6.
  - Rebuilds container images.
- **Breaking changes / migration:** None explicitly listed in the provided notes. This appears to be a patch-level security release; operators should upgrade promptly given the advisory fixes.
- **Follow-up work already tracked:** Translation/catalogue fixes in [#12076](https://github.com/zeroclaw-labs/zeroclaw/issues/12076) and the translation pin in [#12079](https://github.com/zeroclaw-labs/zeroclaw/pull/12079).

---

## 3. Project Progress

### Merged / closed today
- **[PR #12019] chore(docs): pin translations to v0.8.7** — [link](https://github.com/zeroclaw-labs/zeroclaw/pull/12019). The sole merged/closed PR in the last 24h; moves the docs catalogue gitlink to the v0.8.7 translations tag.
- **[Issue #9101] Consolidate release attestation mechanisms** — [link](https://github.com/zeroclaw-labs/zeroclaw/issues/9101). Closed after 11 comments; addresses redundant cosign, GitHub attestation, and SLSA signing paths that inflated release assets to ~53.

### Fixes advanced via open PRs
- **Android shell failure:** [#12080](https://github.com/zeroclaw-labs/zeroclaw/pull/12080) adds a fallback when `canonicalize("/system/bin/sh")` hits `EPERM`, addressing [#12075](https://github.com/zeroclaw-labs/zeroclaw/issues/12075).
- **OpenAI Codex streaming:** [#12073](https://github.com/zeroclaw-labs/zeroclaw/pull/12073) retries Codex streams as streams instead of `stream: false`, addressing [#12072](https://github.com/zeroclaw-labs/zeroclaw/issues/12072).
- **Prompt guard false positives:** [#12068](https://github.com/zeroclaw-labs/zeroclaw/pull/12068) stops treating bare `;` or `|` as command injection, addressing [#12066](https://github.com/zeroclaw-labs/zeroclaw/issues/12066).
- **Discord approvals:** [#12065](https://github.com/zeroclaw-labs/zeroclaw/pull/12065) prevents allowlisted peer bots from answering plaintext approvals, addressing [#12064](https://github.com/zeroclaw-labs/zeroclaw/issues/12064).
- **MCP recovery:** [#12061](https://github.com/zeroclaw-labs/zeroclaw/pull/12061) attempts to recover poisoned MCP sessions, addressing [#10807](https://github.com/zeroclaw-labs/zeroclaw/issues/10807).
- **ZeroCode session resume:** [#12071](https://github.com/zeroclaw-labs/zeroclaw/pull/12071) adds `/load-session`, addressing [#12069](https://github.com/zeroclaw-labs/zeroclaw/issues/12069).
- **Runtime prompt ordering:** [#12056](https://github.com/zeroclaw-labs/zeroclaw/pull/12056) keeps identity sections at the head of direct-path system prompts.
- **Channel prompt cap:** [#12070](https://github.com/zeroclaw-labs/zeroclaw/pull/12070) re-applies the system prompt cap after per-turn skill refreshes.

### Larger features in flight
- [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921) — append-only session event history (XL, risk:high).
- [#11414](https://github.com/zeroclaw-labs/zeroclaw/pull/11414) — opt-in focused workspaces for the web dashboard (XL, risk:high).
- [#11880](https://github.com/zeroclaw-labs/zeroclaw/pull/11880) — transport-neutral Goal policy contracts.
- [#11596](https://github.com/zeroclaw-labs/zeroclaw/pull/11596) — native Claude Code account onboarding.
- [#11680](https://github.com/zeroclaw-labs/zeroclaw/pull/11680) — Firecrawl web search provider.
- [#12059](https://github.com/zeroclaw-labs/zeroclaw/pull/12059) — runtime-profile thinking levels for OpenAI.

---

## 4. Community Hot Topics

| Item | Comments | Why it matters |
|---|---:|---|
| [#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432) — Runtime, dashboard sign-in and gateway delivery tracker | 16 | Long-running v0.9.0 tracker. Signals demand for coordinated release allocation and acceptance evidence across runtime/gateway work. |
| [#9101](https://github.com/zeroclaw-labs/zeroclaw/issues/9101) — Consolidate release attestation mechanisms | 11 | Closed today. Reflects maintainer/contributor concern over CI cost and release complexity from three parallel signing mechanisms. |
| [#11517](https://github.com/zeroclaw-labs/zeroclaw/issues/11517) — Web chat reload drops prompt | 3 | Session persistence is still unreliable under mid-turn reloads; directly affects dashboard usability. |
| [#10807](https://github.com/zeroclaw-labs/zeroclaw/issues/10807) — MCP connection permanently poisoned | 3 | A single failed recovery attempt blocks workflows; high-impact for MCP-heavy users. |
| [#11913](https://github.com/zeroclaw-labs/zeroclaw/issues/11913) — Telegram command menu exposed to all users | 2 | Security/privacy concern: `setMyCommands` lacks trusted-user scoping. |
| [#11961](https://github.com/zeroclaw-labs/zeroclaw/issues/11961) — Dashboard sign-in with OIDC | 2 | Identity/access demand for enterprise-style SSO on the web dashboard. |
| [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047) — OIDC bearer verification lacks limiter | 2 | Security hardening gap around outbound token checks. |

**Underlying needs:** The hottest discussions cluster around (1) **release engineering simplification** (#9101), (2) **coordinated v0.9.0 runtime/gateway planning** (#7432), and (3) **security/identity-access correctness** (#11913, #11961, #12047). PR comment counts were not available, but large/risky PRs such as [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921) and [#9745](https://github.com/zeroclaw-labs/zeroclaw/pull/9745) are likely review bottlenecks.

---

## 5. Bugs & Stability

### S1 — Workflow blocked
| Issue | Status / Fix |
|---|---|
| [#10807](https://github.com/zeroclaw-labs/zeroclaw/issues/10807) — MCP connection permanently poisoned by one failed recovery attempt (p1, risk:high) | Fix PR [#12061](https://github.com/zeroclaw-labs/zeroclaw/pull/12061) open. |
| [#12075](https://github.com/zeroclaw-labs/zeroclaw/issues/12075) — Android shell tool always fails; `/system/bin/sh` canonicalization `EPERM` | Fix PR [#12080](https://github.com/zeroclaw-labs/zeroclaw/pull/12080) open. |

### S2 — Degraded behavior
| Issue | Status / Fix |
|---|---|
| [#11517](https://github.com/zeroclaw-labs/zeroclaw/issues/11517) — Web chat reload drops prompt from screen and `localStorage` | No fix PR listed. |
| [#12055](https://github.com/zeroclaw-labs/zeroclaw/issues/12055) — MCP HTTP client has no pool/keep-alive control; stale socket loses tool call | No fix PR listed. |
| [#12072](https://github.com/zeroclaw-labs/zeroclaw/issues/12072) — Codex stream-decode fallback retries without streaming and is rejected | Fix PR [#12073](https://github.com/zeroclaw-labs/zeroclaw/pull/12073) open. |
| [#12064](https://github.com/zeroclaw-labs/zeroclaw/issues/12064) — Discord plaintext approvals can be answered by an allowlisted peer bot | Fix PR [#12065](https://github.com/zeroclaw-labs/zeroclaw/pull/12065) open. |

### S3 — Minor
| Issue | Status / Fix |
|---|---|
| [#12067](https://github.com/zeroclaw-labs/zeroclaw/issues/12067) — `untrusted_guard_sensitivity` moves prompt guard and leak detector in opposite directions | No fix PR listed. |
| [#12066](https://github.com/zeroclaw-labs/zeroclaw/issues/12066) — Prompt guard flags any semicolon or pipe as command injection | Fix PR [#12068](https://github.com/zeroclaw-labs/zeroclaw/pull/12068) open. |

### Security issues reported / active
- [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047) — OIDC bearer verification has no attempt limiter or outbound cap.
- [#12049](https://github.com/zeroclaw-labs/zeroclaw/issues/12049) — Four remote addresses can exhaust the browser sign-in flow store.
- [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) — Gateway limiters key on the first `X-Forwarded-For` entry, enabling loopback-exemption spoofing.
- [#12051](https://github.com/zeroclaw-labs/zeroclaw/issues/12051) — `sop_workshop apply` lets a scoped principal author shared SOPs that later run with operator authority.
- [#12046](https://github.com/zeroclaw-labs/zeroclaw/issues/12046) — Non-admin principals with wildcard tool selectors retain principal-unaware tools.
- [#11913](https://github.com/zeroclaw-labs/zeroclaw/issues/11913) — Telegram command menu exposed globally.

**Assessment:** Fix PRs exist for several high-severity functional bugs, but **most security issues have no linked fix PR yet**. The concentration of identity-access findings from the same reporter suggests an ongoing security audit; these should be prioritized before v0.9.0.

---

## 6. Feature Requests & Roadmap Signals

- **[#11961](https://github.com/zeroclaw-labs/zeroclaw/issues/11961) — Dashboard sign-in with OIDC** (tagged `release:v0.9.0`). Strong candidate for the next minor release, especially given related security work in #12047.
- **[#12069](https://github.com/zeroclaw-labs/zeroclaw/issues/12069) — `/load-session` for Code/ACP sessions.** Already has PR [#12071](https://github.com/zeroclaw-labs/zeroclaw/pull/12071); likely to land in a patch or v0.9.0.
- **[#10705](https://github.com/zeroclaw-labs/zeroclaw/issues/10705) — Support `max` reasoning effort for compatible OpenAI models.** Provider parity request; could ship once transport/config handling is settled.
- **[#12077](https://github.com/zeroclaw-labs/zeroclaw/issues/12077) — Give `fill-translations` page context and add second-reader review.** Process/tooling improvement for docs localization.
- **[#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432) — v0.9.0 runtime/dashboard/gateway tracker.** Defines the release theme: runtime, sign-in, and gateway delivery.

**Prediction:** v0.9.0 is shaping up around **identity/access (OIDC, principal scoping)**, **runtime/gateway delivery**, **session history** ([#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)), and **knowledge-graph scoping** ([#9745](https://github.com/zeroclaw-labs/zeroclaw/pull/9745)). Android shell and Codex streaming fixes are more likely to land as v0.8.x patches.

---

## 7. User Feedback Summary

**Pain points reported in the last 24h:**
- **Android/Termux is effectively broken** for shell tool calls due to `canonicalize` failing on `/system/bin/sh` — a hard S1 blocker for mobile users.
- **MCP reliability under real-world churn** (container restarts, laptop sleep, proxy reloads) is poor; one failed recovery poisons the session, and HTTP pooling can silently lose tool calls.
- **Web dashboard state loss** on reload mid-turn erodes trust in session persistence.
- **Security posture concerns** dominate: Telegram command exposure, OIDC verification limits, proxy-header spoofing, SOP privilege escalation, and wildcard tool selectors all point to gaps in principal-aware access control.
- **Localization quality**: multiple docs issues (#12076, #12078) cite English leftovers and incorrect translations in `fr`, `ja`, `es`, and `zh-CN` catalogues.
- **Usability friction**: resuming Code sessions is cumbersome without `/load-session`; reasoning effort control is missing for some OpenAI-compatible models.

**Satisfaction signals:** Rapid release cadence, a security-focused v0.8.7 with 19 contributors, and quick fix PRs for Android, Codex, Discord, and prompt-guard issues show responsiveness.

**Dissatisfaction signals:** High-severity blockers remain open, many security findings lack fix PRs, and the 41-open-PR backlog may delay user-visible fixes.

---

## 8. Backlog Watch

| Item | Age / Status | Why it needs attention |
|---|---|---|
| [#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432) — v0.9.0 runtime/dashboard/gateway tracker | Created 2026-06-09; 16 comments; updated today | Central v0.9.0 tracker; needs sequencing and acceptance evidence to avoid release drift. |
| [#9745](https://github.com/zeroclaw-labs/zeroclaw/pull/9745) — Per-agent attribution/scoping for knowledge graph | Created 2026-08-04; `needs-author-action`; p1; v0.9.0; XL | Security-relevant and release-gated, but stalled on author action. |
| [#10807](https://github.com/zeroclaw-labs/zeroclaw/issues/10807) — MCP connection poisoning | Created 2026-09-12; p1; S1 | Fix PR #12061 exists but needs review; high user impact. |
| [#10705](https://github.com/zeroclaw-labs/zeroclaw/issues/10705) — `max` reasoning effort for compatible OpenAI models | Created 2026-09-07; p2 | Provider parity request with no linked PR. |
| [#11517](https://github.com/zeroclaw-labs/zeroclaw/issues/11517) — Web chat reload drops prompt | Created 2026-10-04; p2; S2 | No fix PR; affects default `session_persistence` behavior. |
| [#11414](https://github.com/zeroclaw-labs/zeroclaw/pull/11414) — Focused workspaces | Created 2026-10-01; XL; risk:high | Large UX change needing maintainer review. |
| [#11596](https://github.com/zeroclaw-labs/zeroclaw/pull/11596) — Native Claude Code accounts | Created 2026-10-07; `needs-maintainer-review`; risk:high | Strategic onboarding feature; review bottleneck. |
| [#11680](https://github.com/zeroclaw-labs/zeroclaw/pull/11680) — Firecrawl web search provider | Created 2026-10-12; `needs-maintainer-review`; XL | External provider integration awaiting review. |
| [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921) — Append-only session event history | Created 2026-10-21; XL; risk:high | Foundational architecture change; needs careful review before v0.9.0. |

**Bottom line:** ZeroClaw is healthy on activity and release discipline, but the combination of a large open-PR queue, multiple unaddressed security issues, and several S1/S2 bugs means **review capacity and security triage are the critical path** for the next release.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*