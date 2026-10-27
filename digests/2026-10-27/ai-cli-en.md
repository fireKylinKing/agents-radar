# AI CLI Tools Community Digest 2026-10-27

> Generated: 2026-10-27 01:36 UTC | Tools covered: 7

- [Claude Code](https://github.com/anthropics/claude-code)
- [OpenAI Codex](https://github.com/openai/codex)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [GitHub Copilot CLI](https://github.com/github/copilot-cli)
- [OpenCode](https://github.com/anomalyco/opencode)
- [Pi](https://github.com/earendil-works/pi)
- [Qwen Code](https://github.com/QwenLM/qwen-code)
- [Claude Code Skills](https://github.com/anthropics/skills)

---

## Cross-Tool Comparison

# Cross-Tool Comparison Report — AI CLI Ecosystem (2026-10-27)

## 1. Ecosystem Overview

All seven tracked tools are under active development, but the community conversation has shifted decisively from "can the agent do the task" to **"can we trust, observe, and operate it at scale."** Reliability engineering—session continuity, sandbox/policy correctness, truthful status reporting, and cost governance—now dominates issue traffic across every repo. Extensibility is the second battleground: each vendor is shipping its own plugin/hook contract (Mods, IPC hosts, ACP, GUI plugins), and cross-tool portability is emerging as an explicit ask. Release cadence is highly uneven—from multiple daily/nightly tags (Qwen Code, Codex alphas, Gemini nightly) to opaque patch builds (Copilot CLI)—while platform parity (Windows, Wayland, Termux) and enterprise configuration remain universal debt.

## 2. Activity Comparison

Counts reflect what was surfaced in each digest, not necessarily full repo totals.

| Tool | Issues (surfaced/updated) | PRs (surfaced/updated) | Discussions | Release status (last 24h) |
|---|---|---|---|---|
| **Claude Code** | 10 hot issues (top thread 274 comments, top reaction 955 👍) | ≥20 surfaced, 10 detailed | N/A (none provided) | **v2.1.311** shipped (idle-session controls, startup-freeze fixes) |
| **OpenAI Codex** | **50 updated** | **50 updated** (mostly closed, bot-authored) | **8 updated** | 3 Rust alphas (`0.166.0-alpha.1`, `0.165.0-alpha.3`, `0.165.0-alpha.1.1`); no changelogs |
| **Gemini CLI** | 10 hot issues | 10 key PRs | N/A (none provided) | **v0.67.0-nightly.20261027** (quota classification, ACP MCP metadata) |
| **GitHub Copilot CLI** | **50 updated** | **0 updated** | N/A (none provided) | v1.0.104-4 — "Fixes and changes" only; not traceable to public PRs |
| **OpenCode** | 10 hot issues (clipboard #4283 closed at 159 comments / 134 👍) | 10 key PRs (GUI plugin SDK landed) | N/A (none provided) | No release noted; plugin/pricing PRs in flight |
| **Pi** | 10 hot issues (Windows megathread 90 comments) | 10 key PRs | **1 discussion** (show & tell) | No release in 24h |
| **Qwen Code** | 10 selected issues (Kubernetes tracking 25 comments) | 10 selected PRs | N/A (none provided) | **3 releases**: v0.26.2-preview.0, v0.26.1-nightly, sdk-typescript-v0.1.23 |

*Note: No repo in this set has Issues/PRs disabled upstream. "N/A" for Discussions means no discussion data was supplied for the period, not that the channel is inactive.*

## 3. Shared Feature Directions

**1. Session lifecycle durability and resume correctness** — The single largest cross-tool cluster.
- Claude Code: `--continue`/`--resume` bypasses the live-session registry (#69364); retry writes a `parentUuid` to a never-persisted row, losing history (#96628).
- Copilot CLI: resume OOM and ~70-min core pin (#4251), session-lock timeouts (#5253), unresumable sessions from masked counters (#5023), model-prefix mismatch (#4282).
- Pi: stale Bedrock credentials in long sessions (#11221), unbounded compaction file lists (#11297).
- Codex: remote/mobile ownership arbitration (#39547, #54591).

**2. Subagent observability and truthful status** — Users are automating agents and punishing silent truncation.
- Gemini CLI: `MAX_TURNS` misreported as `status: "success"` / `Termination Reason: GOAL` (#22323); Wayland browser failure also reports `GOAL` (#21983); trajectory export via `/chat share` (#22598).
- Qwen Code: 1k subagent prompt reported as 105k starting context (#15660).
- Claude Code: `PreCompact`/`PostCompact` fire without agent fields; spurious `SubagentStop` (#91910).
- Codex: failed subagents improperly promoted to "Needs you" (#57315).

**3. Stable extensibility contracts** — Every tool is defining one, and portability is now requested explicitly.
- Claude Code: Mods (#91870, 274 comments) plus plugin-UI render conflicts (#102480).
- Codex: plugin process host with bounded IPC, 256 KiB frames, readiness handshake (#57290); `PreToolUse` drops `exec_command` workdir (#32360).
- OpenCode: Desktop GUI plugin SDK landed (#55246) with follow-up primitives (#59462); reactive TUI plugin slots (#59458).
- Pi: public tool-presentation API / one-line tool mode (#10259); duplicate `session_start` events (#10470); assistant message transformers (#11450).
- Qwen Code: Claude Code-style Mod runtime compatibility proposal (#13749); `FileChanged` hooks (#13969).
- Gemini CLI: ACP permission requests now carry MCP server/tool metadata (#29910).

**4. Sandbox/policy transparency and correctness** — False positives without audit trails erode trust.
- Codex: BitLocker volume blocks child-process launch (#50915); Markdown write "blocked by policy" before execution with no review entry (#45986).
- Qwen Code: headless `-p` implicit `edit` deny overrides scoped allows (#15597); `2>&1` parsed as a write to `&1` (#15600); in-process `sed` diverges from real sed (#15675).
- Gemini CLI: destructive `git reset`/`--force` without guardrails (#22672).
- Copilot CLI: ACP mode auto-approves tool calls again; `session/request_permission` not sent (#4537).

**5. Cross-platform parity (Windows first)**
- Claude Code: focus-stealing console windows on Windows (#91264).
- Codex: renderer memory leak to 4–7 GB (#46690); org settings fail to load (#57051); Windows native-runtime crashes since May (#4026).
- OpenCode: clipboard (#4283), mouse tracking left on after Ctrl+C (#6912), official app rejected as unofficial (#56324).
- Pi: 90-comment Windows megathread (#7547); `find` glob failures (#6817).
- Gemini CLI: Windows CRLF diff normalization (#29966), Termux ripgrep trust (#29884), ambiguous-width rendering (#29905).

**6. Cost/quota transparency and token accounting** — Appears in every digest.
- Codex promotional credits drained ahead of weekly allowance (#52039).
- Copilot CLI: Opus 5.5 empty turns under 32k output cap still billed (#5149); BYOK Azure 429 retries burn quota in ~0.15s (#3679).
- OpenCode: free-tier quota/reset mega-thread (#59387), counter stuck at 61k–84k seconds (#59116), Desktop-vs-CLI inconsistency (#59112).
- Gemini CLI: spending-cap errors now classified as terminal to stop retry waste (#29961).

**7. Model/provider flexibility and routing**
- Copilot CLI: per-agent provider selection (#4703), ACP model listing (#4880), `auto` forcing `reasoningEffort: null` (#4560).
- Pi: OpenCode per-protocol endpoints (#11132), Anthropic Vertex (#5262), custom OpenAI-compatible `/login` flow (#11456).
- OpenCode: Claude Agent provider running Claude models through the user's Claude Code login (#58782, compliance-gated).
- Codex: GPT-6.1 Terra catalog additions across Bedrock/Vertex (#57293); gateway OAuth bearer delivery default (#57313).

**8. Enterprise/MCP readiness**
- Copilot CLI: custom MCP registries reachable in VS Code but not CLI (#4949); OAuth probes returning HTTP 400 with valid stored token (#5014).
- Codex: `network_proxy` in managed `requirements.toml` (#57286).
- Pi: MCP HTTP JSON bypasses `maxMessageBytes` while SSE enforces it (#11335); CIMD over DCR for MCP OAuth (#11426).
- Claude Code: MCP elicitation auto-declined by a controller that never drew a prompt (#89858).

## 4. Differentiation Analysis

| Dimension | Claude Code | OpenAI Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **Primary ecosystem** | Anthropic | OpenAI | Google | GitHub/Microsoft | Open-source/community | Provider-agnostic | Alibaba/Qwen |
| **Core focus** | Extensibility (Mods), Desktop parity, auth/identity | Sandbox/policy correctness, desktop+mobile continuity | Subagent orchestration, ACP, AST-aware tooling | ACP, MCP/Azure/BYOK, headless parity | Free-tier accounting, v1→v2 migration, GUI plugins | Long-session durability, TUI fidelity, Nix | Managed-agent/Hosted runtime, Web Shell |
| **Target user** | Professional devs juggling personal/team/client identities; plugin authors | Cross-surface users (desktop/CLI/iOS); enterprise policy admins | Terminal-centric devs in GCP orbit; automation builders | GitHub-centric devs; enterprise Azure shops; headless/CI | OSS devs and multi-provider tinkerers | Power users, TUI purists, Bedrock/Vertex/Nix users | Qwen users; hosted/managed deployments; Web Shell users |
| **Technical approach** | Marketplace plugins + hooks; Desktop/IDE-first regressions | Rust core; process-isolated plugin IPC; high-frequency alpha tags with heavy bot PR automation | Nightly TS CLI; ACP-first; AST-aware tooling EPIC | Sparse public PR visibility; ACP mode; voice mode with local ASR | Bun-based server/client protocol; plugins across server/TUI/GUI | TypeScript TUI; provider adapters (Bedrock, Vertex, OpenCode) | Managed-agent server + SDKs (TS, Java) + Web Shell |
| **Notable distinction** | Highest reaction counts (955 👍 on multi-account) | Strongest sandbox/policy engineering and automated PR throughput | Cleanest ACP integration; most explicit subagent-observability roadmap | **Zero PRs surfaced** despite 50 issue updates — release traceability gap | Explicit Claude Code login reuse (#58782, compliance-gated) | Best multi-provider neutrality and packaging rigor | Only tool shipping Anthropic-compatibility (Mod runtime, Co-authored-by removal) |

## 5. Community Momentum & Maturity

**Most active / largest engagement**
- **Claude Code** — highest raw engagement in the dataset: #91870 (274 comments), #18435 (955 👍), #1757 (year-old auth friction still updated daily). Mature but carrying significant reliability debt.
- **OpenCode** — intense free-tier support traffic plus a 159-comment/134-👍 clipboard issue; the community is large enough that maintainers created an aggregation thread (#59387).
- **Pi** — smaller but highly engaged power-user base; the 90-comment Windows megathread is a prioritization signal, not a bug queue.

**Rapidest iteration**
- **Qwen Code** — 3 releases in 24h and a feature-rich managed-agent track, but CI noise dominates comment counts (#14724 at 64 comments; 10 failures filing zero issues, #15470), consuming maintainer bandwidth.
- **OpenAI Codex** — 3 alpha tags and 50 PRs in 24h, mostly automated (`copyberry[bot]`), indicating a mature CI/automation pipeline. Release notes are absent, though.
- **Gemini CLI** — steady nightly cadence with tightly scoped, well-described fixes (#29961, #29910, #29905, #29966); the most consistently legible changelog of the set.

**Least transparent iteration**
- **GitHub Copilot CLI** — 50 updated issues, **0 updated PRs**, and a release note consisting only of "Fixes and changes." Practically, the 429-retry closure (#2760) cannot be mapped to a build. This is not inactivity—it is a public traceability gap that complicates bisection for teams pinned to 1.0.103.

**Maturity signals by dimension**
- Extensibility maturity: Codex (IPC isolation) > Claude Code (Mods) > OpenCode (GUI SDK) > Gemini CLI (ACP) > Pi/Qwen Code (in-flight).
- Operational maturity: Gemini CLI and Codex show disciplined automated PR/release hygiene; Qwen Code and Pi still absorb significant manual/triage overhead.
- Trust maturity: none of the seven yet solves truthful agent termination reporting end-to-end; Gemini's #22323 is the clearest example, but the pattern recurs.

## 6. Trend Signals

**1. Trust and observability are the new capability frontier.** The most-commented issue in Gemini CLI is a reporting bug, not a missing feature. Copilot CLI's dominant theme is *silent* failure (empty voice transcriptions, OAuth with no popup, unanswered permission requests with nothing logged). Expect "agent audit log" and "termination reason API" to become table stakes.

**2. Extensibility fragmentation will drive consolidation pressure.** Each tool now has a distinct hook/plugin model: Mods, IPC hosts, ACP metadata, GUI plugin SDKs, `FileChanged` hooks. Qwen Code's explicit Claude Code compatibility ask (#13749) is an early sign that plugin-portability—not just model quality—will influence adoption.

**3. Enterprise hardening is moving from docs to config files.** Managed `requirements.toml`, `network_proxy`, gateway OAuth delivery defaults, per-workspace policy, and denial-with-review-entry are converging on a de facto enterprise admin contract.

**4. Long-session workloads are under-served.** Credential refresh, unbounded compaction state, backlogged send queues, resume OOM, and live-session registry races all point at the same underserved use case: agents left running for hours or days. This is a design gap, not a bug queue.

**5. Cost governance is becoming a first-class UX concern.** Empty output turns that bill, unbacked-off 429 retries, quota counters stuck at 61k seconds, and promotional-credit mismatches all erode trust precisely where spend is unattended (CI/headless). Expect per-session budget controls and inspectable token ledgers.

**6. Platform parity remains a strategic, not tactical, decision.** Pi's megathread frames Windows as an investment-allocation question; Codex and OpenCode show Windows as the highest-severity bug cluster. Wayland and Termux are emerging as the next parity frontier.

**7. Automated PR pipelines are a competitive advantage.** Codex's bot-authored PR volume and Gemini's nightly discipline contrast with Copilot CLI's invisible merge path. For decision-makers, **release traceability** (can we bisect a regression to a build?) is a legitimate evaluation criterion alongside feature set.

### Reference value for developers

- **Building plugins/hooks:** target the contracts with the most complete payloads and lifecycle guarantees; watch Claude Code #91910 (agent fields), Codex #57290 (IPC framing/backpressure), and Qwen Code #13749 for potential Claude Code compatibility.
- **Selecting for enterprise/regulated use:** prioritize sandbox/policy transparency (Codex, Qwen Code fixes in flight) and observability; treat "blocked by policy with no review entry" as a disqualifier for audited workflows.
- **Running headless/CI:** scrutinize permission semantics in non-interactive mode (Qwen Code #15597/#15600 is the clearest cautionary case), output-token caps and retry behavior (Copilot CLI #5149, #3679), and session resume guarantees (Copilot CLI cluster, Claude Code #96628).
- **Avoiding lock-in:** Pi and OpenCode offer the broadest provider neutrality; ACP and MCP standardization are the mechanisms to watch, though MCP transport-validation inconsistencies (Pi #11335) show the spec is not yet uniformly enforced.
- **Bisecting regressions:** prefer tools with itemized changelogs and public PR linkage; Copilot CLI's opaque patch cadence is a real operational cost, not a cosmetic complaint.

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — Community Highlights Report
**Source:** github.com/anthropics/skills · **Data as of:** 2026-10-27

> **Data caveat:** All 20 listed PRs report `Comments: undefined` and `👍: 0`, so ranking below uses proxy attention signals — update recency, thread longevity, and cross-linking to high-comment Issues — rather than raw comment counts. Issue comment counts *are* available and are used in Section 2.

---

## 1. Top Skills Ranking

| # | Skill / PR | Function | Discussion Highlights | Status |
|---|---|---|---|---|
| 1 | [#1742](https://github.com/anthropics/skills/pull/1742) `mcp-builder` | Scaffolds MCP servers (connections, evaluation harness) | Fixes #1668: `mcp>=2` renamed `streamablehttp_client` → `streamable_http_client`; headers now require `create_mcp_http_client`/`http_client`. Part of a broader `mcp-builder` breakage cluster (cf. Issue #1390) | OPEN · updated 2026-10-08 |
| 2 | [#1980](https://github.com/anthropics/skills/pull/1980) `webapp-testing` | Headless browser testing harness | Removes `shell=True` in `with_server.py` (CWE-78 command injection) by replacing `cd && cmd` chains — security hardening of a widely used skill | OPEN · updated 2026-10-25 (most recent) |
| 3 | [#1997](https://github.com/anthropics/skills/pull/1997) `skill-creator` | Meta-skill for authoring/optimizing Skills | Hand-rolled YAML frontmatter parser drops quotes/escapes, so eval and description-optimization read metadata different from `SKILL.md` | OPEN · updated 2026-10-19 |
| 4 | [#1245](https://github.com/anthropics/skills/pull/1245) New: `notion-spec-to-implementation`, `quantitative-resume-auditor` | Spec→Notion task decomposition; resume quantification audit | Bundled two-skill submission; longest-running *new skill* PR still receiving updates (created 2026-06) | OPEN · updated 2026-10-15 |
| 5 | [#1681](https://github.com/anthropics/skills/pull/1681) `skill-creator` | Packaging CLI (`package_skill.py`) | `ModuleNotFoundError: scripts.quick_validate` on direct execution; outdated docstrings/help paths | OPEN · updated 2026-10-24 |
| 6 | [#1298](https://github.com/anthropics/skills/pull/1298) `skill-creator` | Trigger evaluation for Skill descriptions | False misses: competing per-worker probes, `select()` on pipes fails on Windows, runtime failures mis-scored as non-triggers (inflating negative examples) | OPEN · updated 2026-09-16 |
| 7 | [#1792](https://github.com/anthropics/skills/pull/1792) `docx` | Word document editing / change acceptance | LibreOffice timeout reported as success; now errors out and verifies `w:ins`/`w:del`/`w:moveFrom` removal — same file family as Issue #2018 (invalid XML) | OPEN · updated 2026-09-25 |
| 8 | [#525](https://github.com/anthropics/skills/pull/525) New: `pyxel` | Retro game dev/verification in Python | Longest-lived active submission (2026-03 → 2026-10); headless input-driven runs, frame inspection, state checks | OPEN · updated 2026-10-23 |

*Also notable:* [#1771](https://github.com/anthropics/skills/pull/1771) `proofcore-contract-auditor` (Web3/Solidity+TON notarization), [#1730](https://github.com/anthropics/skills/pull/1730) dead-URL fixes in `claude-api`/`academy-guide`, [#514](https://github.com/anthropics/skills/pull/514) `document-typography` (widows/orphans/numbering).

---

## 2. Community Demand Trends

Distilled from Issue comment volume and reactions:

1. **Trust, provenance & namespace governance** — Issue [#492](https://github.com/anthropics/skills/issues/492) (46 comments, the single most-discussed item): community skills shipped under an `anthropic/` namespace impersonate official Skills, enabling permission escalation. Related: [#189](https://github.com/anthropics/skills/issues/189) (duplicate skills across `document-skills`/`example-skills` inflating context).
2. **Distribution & org-level sharing** — Issue [#228](https://github.com/anthropics/skills/issues/228) (17 comments, 8 👍): demand for a shared org Skill library / share links instead of manual `.skill` file exchange via Slack.
3. **Evaluation-harness reliability** — Issues [#556](https://github.com/anthropics/skills/issues/556) (13 comments: 0% trigger rate in `run_eval.py`), [#1390](https://github.com/anthropics/skills/issues/1390) (`mcp-builder` eval scores 0/N against real servers), [#1394](https://github.com/anthropics/skills/issues/1394) (eval-viewer XSS). Testing *the Skills themselves* is now a first-class ask.
4. **Context/token efficiency** — Issue [#1487](https://github.com/anthropics/skills/issues/1487): `claude-api` eagerly injects ~156k tokens in one tool call; [#202](https://github.com/anthropics/skills/issues/202): `skill-creator` reads like human docs, violating token-efficiency norms. Progressive disclosure and budgeted loading.
5. **Agent memory & governance meta-skills** — [#1329](https://github.com/anthropics/skills/issues/1329) `compact-memory` (symbolic state notation), [#412](https://github.com/anthropics/skills/issues/412) `agent-governance` (policy, threat detection, audit trails), [#1385](https://github.com/anthropics/skills/issues/1385) reasoning quality-gate pipeline, [#83](https://github.com/anthropics/skills/pull/83) `skill-quality-analyzer` + `skill-security-analyzer`.
6. **Document-pipeline correctness** — [#2018](https://github.com/anthropics/skills/issues/2018) (docx namespace stripping → invalid XML), [#1868](https://github.com/anthropics/skills/issues/1868) off-by-one gradient interpolation, [#2064](https://github.com/anthropics/skills/issues/2064) descriptions stating *what* but not *when to use*.

**Net read:** demand has shifted from "more Skills" to **making existing Skills trustworthy, evaluable, and token-cheap**, plus governance over how Skills are published.

---

## 3. High-Potential Pending Skills

Active, unmerged PRs with recent maintainer-visible activity that could land soon:

- **[#1980](https://github.com/anthropics/skills/pull/1980)** — `webapp-testing` shell-injection fix; minimal, security-motivated diff, most recently touched PR in the set.
- **[#1997](https://github.com/anthropics/skills/pull/1997)** — `skill-creator` frontmatter parsing; small blast radius, unblocks reliable eval/optimization.
- **[#1681](https://github.com/anthropics/skills/pull/1681)** — `skill-creator` standalone script execution + docs; trivially verifiable.
- **[#1742](https://github.com/anthropics/skills/pull/1742)** — `mcp-builder` client import/header migration; time-sensitive (`mcp>=2` breakage).
- **[#1245](https://github.com/anthropics/skills/pull/1245)** — `notion-spec-to-implementation` + `quantitative-resume-auditor`; sustained 4-month iteration suggests author responsiveness.
- **[#525](https://github.com/anthropics/skills/pull/525)** — `pyxel`; mature, narrowly scoped, persistent author.
- **[#1771](https://github.com/anthropics/skills/pull/1771)** — `proofcore-contract-auditor`; fills an empty Web3 vertical, though external-service dependency may slow review.
- **[#514](https://github.com/anthropics/skills/pull/514)** — `document-typography`; addresses universal output-quality complaints with deterministic rules.

*Watch-outs:* [#1245](https://github.com/anthropics/skills/pull/1245)–[#1771](https://github.com/anthropics/skills/pull/1771)-style multi-skill or vendor-coupled submissions historically stall on scope and third-party dependency review; single-purpose fixes are moving faster.

---

## 4. Skills Ecosystem Insight

**The community's most concentrated demand is not new Skill breadth but hardening the existing core — security, correctness, evaluation integrity, and context cost — together with a trustworthy publishing/namespace model for community-contributed Skills.**

---

# Claude Code Community Digest — 2026-10-27

## 1. Today's Highlights

Claude Code shipped **v2.1.311**, adding idle-session controls for self-hosted runners plus startup-freeze fixes. Community attention remains concentrated on two long-running requests: the **Mods extensibility** initiative (#91870, 274 comments) and **multi-account support in Claude Desktop** (#18435, 955 👍). Reliability is the dominant bug theme, with multiple reports of mid-response connection drops and recurring authentication prompts.

## 2. Releases

**v2.1.311** — [Release notes](https://github.com/anthropics/claude-code/releases)
- Added `--hold-idle-file` and `--max-hold-min` to `self-hosted-runner`, allowing an idle session to be retained while the host reports processes it left running.
- Added a tip for the **Explanatory** output style, surfaced when asking Claude for explanations.
- Fixed long freezes at startup and after r… *(provided changelog is truncated)*.

## 3. Hot Issues

1. **[#91870](https://github.com/anthropics/claude-code/issues/91870) — Mods: make Claude 10x more extensible** · 274 comments · 131 👍 · OPEN  
   The largest active design thread. Maintainers posted community micro-updates indicating the extensibility surface is live and feedback is being worked through. This is likely the most consequential plugin/hook API change in the tracker.

2. **[#18435](https://github.com/anthropics/claude-code/issues/18435) — Multiple Claude accounts in Desktop with easy profile switching** · 220 comments · 955 👍 · OPEN  
   Highest reaction count in the dataset. Strong signal that developers juggle personal, team, and client identities and want first-class profile management rather than re-authentication.

3. **[#1757](https://github.com/anthropics/claude-code/issues/1757) — Claude Code requires users to constantly log in** · 88 comments · 76 👍 · OPEN since 2025-06  
   A year-old auth-friction issue still updated daily. Persistent sessions remain a top usability complaint and intersect with MCP OAuth/token-storage bugs.

4. **[#88391](https://github.com/anthropics/claude-code/issues/88391) — Prompt suggestions silently stopped appearing in Desktop (regression in 2.1.229)** · 16 comments · 16 👍 · has repro  
   Clear regression report with reproduction. Matters because Desktop UX regressions erode trust in automatic updates.

5. **[#91910](https://github.com/anthropics/claude-code/issues/91910) — Hook payload gaps around subagent compaction and internal summarizer calls** · 13 comments · 4 👍 · has repro  
   `PreCompact`/`PostCompact`/`SessionStart(compact)` fire without agent fields, and `SubagentStop` fires for internal summarizer calls with a never-created transcript path. Important for plugin authors building on hooks.

6. **[#69364](https://github.com/anthropics/claude-code/issues/69364) — `--continue`/`--resume` does not check the live-session registry** · 12 comments · 1 👍  
   Two processes can append to the same session JSONL. A concurrency/correctness issue affecting multi-terminal workflows.

7. **[#89858](https://github.com/anthropics/claude-code/issues/89858) — MCP elicitation auto-declined by a controlling client that never drew a prompt** · 7 comments · 2 👍 · has repro  
   Reproduces with Desktop, `--input-format stream-json`, and Agent SDK. Prior related reports were closed as duplicates rather than on the merits, making this a key SDK-integration bug.

8. **[#103488](https://github.com/anthropics/claude-code/issues/103488) — “Connection lost mid-response” traced to TCP resets from 160.79.104.10** · 6 comments  
   Packet-capture evidence pointing at server-side resets. Valuable because it moves the discussion beyond generic network blame.

9. **[#102480](https://github.com/anthropics/claude-code/issues/102480) — Desktop Code tab: next-step box hides a plugin’s `AbovePrompt` render** · 6 comments · 3 👍  
   A concrete plugin-UI integration conflict: `on('ui.render', { component: 'AbovePrompt' })` output is obscured by the built-in next-step box.

10. **[#91264](https://github.com/anthropics/claude-code/issues/91264) — PowerShell/Bash tool calls spawn a focus-stealing console window on Windows** · 5 comments  
    No schema-exposed option suppresses the window. A high-annoyance Windows polish issue that breaks flow during agent runs.

## 4. Key PR Progress

1. **[#105303](https://github.com/anthropics/claude-code/pull/105303) — Install marketplace plugins from claude-plugins-official** · OPEN  
   Migrates the `claude-code-plugins` marketplace to install every plugin from `claude-plugins-official`. Explicit merge-order dependency on `anthropics/claude-plugins-official#6905` and `#6906`.

2. **[#96434](https://github.com/anthropics/claude-code/pull/96434) — security-guidance: keep denied and secret files out of the reviewer’s reach** · CLOSED  
   Review sub-agent now honors `Read` deny/ask rules and excludes `.env`, keys, and credential stores; gets the same rules as `disallowed_tools` and no shell. `SG_SKIP_SECRET_FILES=0` opts out.

3. **[#87079](https://github.com/anthropics/claude-code/pull/87079) — fix(security-guidance): make `**` glob patterns match zero-depth paths** · CLOSED  
   Previously `**/*.ts` silently excluded top-level files from security rules — a silent false-negative in a security-sensitive path.

4. **[#85409](https://github.com/anthropics/claude-code/pull/85409) — Update default model refs from Opus 4.7/Sonnet 4.6 to Opus 5/Sonnet 5** · CLOSED  
   Aligns the security-guidance README and hook code with current model defaults.

5. **[#81426](https://github.com/anthropics/claude-code/pull/81426) — security-guidance: support Windows venv layout for the agentic reviewer** · CLOSED  
   Removes the `SKIP_WIN32` early return so the strongest review layer works on Windows without a system-installed `claude_agent_sdk`.

6. **[#79237](https://github.com/anthropics/claude-code/pull/79237) — Add `_is_isolated_worktree` guard to prevent spawn from mutating the parent repo checkout** · CLOSED  
   Addresses a serious failure mode where `spawn_task`/chips caused `git checkout -b` to run against the shared main checkout.

7. **[#76640](https://github.com/anthropics/claude-code/pull/76640) — Load macOS system certificates and handle `NO_PROXY` for Bun runtime** · CLOSED  
   Fixes “Self-signed certificate detected” failures introduced with the Bun runtime in v2.1.17+, including proxy configurations.

8. **[#76274](https://github.com/anthropics/claude-code/pull/76274) — Resolve review paths against repo root and harden findings-array contract** · CLOSED  
   Normalizes relative, root-anchored, and foreign absolute paths in reviewer prompts — reduces missed or misattributed findings.

9. **[#74021](https://github.com/anthropics/claude-code/pull/74021) — Allow `null` findings in StructuredOutput schema** · CLOSED  
   Models sometimes emit `null` rather than `[]`, causing schema rejection and wasted retry turns; observed across 31 sessions in 7 repos.

10. **[#65875](https://github.com/anthropics/claude-code/pull/65875) — Forward `ANTHROPIC_BASE_URL` to the agentic_review child process** · CLOSED  
    Fixes auth failures for proxy/gateway users (LiteLLM, Bifrost) whose reviewer subprocess defaulted to `api.anthropic.com`.

*Note: many of the 20 surfaced PRs are security-guidance fixes closed in the same window, indicating a cleanup sweep of the plugin backlog.*

## 5. Hot Discussions

No discussion data was provided for this period; this section is omitted.

## 6. Feature Request Trends

- **Identity and account management** — multi-profile switching in Desktop (#18435) and persistent authentication (#1757).
- **Extensibility maturity** — Mods APIs, complete hook payloads with agent identity (#91910), plugin precedence rules in Team orgs (#104236), and non-truncated `plugin validate` output (#105020).
- **Desktop/agent-view orchestration** — visibility into chip-spawned task status (#95554), PR merge/close events delivered to watching sessions (#98319), and task-chip persistence across compaction/sessions (#93880, #97100).
- **Session lifecycle control** — manual-only archiving (#91607), live-session registry checks before resume (#69364), and recoverable transcripts after retries (#96628).
- **Output verbosity controls** — repeated requests to reduce default explanation volume (#96645).
- **Cowork organization** — sort project chats by last activity rather than creation date (#87723).

## 7. Developer Pain Points

- **Network reliability dominates bug volume.** Mid-response connection drops appear across platforms: #103488 (server TCP resets), #103058 (291 errors in 7 days, 276 at the same point), #104603, #104527, #89475 (stream reset 2–8 ms after first chunk), and closed #87500. Users are increasingly supplying packet captures and transcript statistics.
- **Authentication instability.** Constant re-login (#1757) and MCP OAuth token loss under macOS Keychain write failures/timeouts (#99471) remain unresolved.
- **Windows remains the roughest platform.** Focus-stealing console windows (#91264), stream `ECONNRESET` on `/v1/messages` (#89475), and MCP elicitation auto-decline in stream-json mode (#89858).
- **Hook/plugin contracts are underspecified.** Missing agent fields on compaction hooks, spurious `SubagentStop` calls (#91910), silent security-default overrides (#104236), and truncated validation output (#105020) make plugin behavior hard to debug.
- **Desktop regressions and hidden state.** Prompt suggestions disappearing (#88391), plugin render output obscured (#102480), sessions auto-archiving on PR merge (#91607), and chips invisible after compaction (#93880).
- **Background sessions start with a minimal PATH** (`/usr/bin:/bin:/usr/sbin:/sbin`), breaking nvm/pyenv-installed MCP servers and status-line commands (#104181).
- **Resume can lose history** when a failed tool-call retry writes a `parentUuid` pointing at a row that is never persisted (#96628) — a data-loss class issue rather than a cosmetic bug.

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-10-27

Snapshot: 50 updated issues, 50 updated PRs, 8 updated discussions. Issue traffic is heavily focused on desktop stability, sandbox/policy correctness, and remote-session continuity.

## 1. Today’s Highlights

Codex Rust releases remain on a fast alpha cadence with three new tags—`0.166.0-alpha.1`, `0.165.0-alpha.3`, and `0.165.0-alpha.1.1`—though no detailed release notes were provided. The issue queue is dominated by platform reliability: Windows sandbox/policy failures and organization-settings loading, plus recurring macOS Computer Use worker leaks and memory-related crashes. PR activity highlights plugin lifecycle/IPC work, Guardian context refactoring, enterprise auth/network configuration, and model catalog expansion with GPT-6.1 Terra.

## 2. Releases

- [rust-v0.166.0-alpha.1](https://github.com/openai/codex/releases/tag/rust-v0.166.0-alpha.1) — new Rust alpha tag; no changelog details in the provided data.
- [rust-v0.165.0-alpha.3](https://github.com/openai/codex/releases/tag/rust-v0.165.0-alpha.3) — new Rust alpha tag; no changelog details in the provided data.
- [rust-v0.165.0-alpha.1.1](https://github.com/openai/codex/releases/tag/rust-v0.165.0-alpha.1.1) — patch-level alpha tag; no changelog details in the provided data.

## 3. Hot Issues

1. **[#50915 — Windows MXC sandbox fails when an unrelated BitLocker volume is locked](https://github.com/openai/codex/issues/50915)**  
   A locked, unrelated data volume prevents child-process launch even when the workspace is elsewhere. This is a high-impact Windows sandbox bug; 21 comments and 8 👍 make it one of the most active issues in the queue.

2. **[#38760 — macOS Computer Use spawn storm triggers WindowServer watchdog panic](https://github.com/openai/codex/issues/38760)**  
   `SkyComputerUseService` spawns at roughly 5–8 processes/second without reuse or cleanup, leading to severe system instability. With 15 comments and related reports such as #40153, #39732, and #39151, this looks like a systemic desktop-runtime leak.

3. **[#32360 — `PreToolUse` drops `exec_command` workdir and reports the turn cwd](https://github.com/openai/codex/issues/32360)**  
   Hook payloads report the wrong working directory, weakening policy enforcement and auditability. Still open since July with 13 comments and 5 👍, indicating a persistent tool-call correctness gap.

4. **[#46690 — Windows renderer memory leak grows to 4–7 GB and crashes repeatedly](https://github.com/openai/codex/issues/46690)**  
   The desktop renderer leaks memory even under light workloads, while an older build is reported stable. Nine comments point to a serious Windows desktop regression.

5. **[#45986 — Windows policy rejects a user-authorized Markdown QA report write](https://github.com/openai/codex/issues/45986)**  
   The command is blocked before execution with “blocked by policy” and no review entry. This matters because sandbox false positives are both disruptive and hard to audit; 9 comments.

6. **[#39547 — Remote Control ownership collision returns HTTP 409](https://github.com/openai/codex/issues/39547)**  
   Desktop/CLI ownership conflicts prevent iOS from opening CLI chats. The 8 👍 and 7 comments suggest strong interest in reliable cross-device session handoff.

7. **[#52039 — Pro 200 promotional credits depleted while weekly allowance remained](https://github.com/openai/codex/issues/52039)**  
   A billing/credits accounting mismatch caused promotional Work/Codex credits to drain before the included weekly allowance. Eight comments reflect user sensitivity around quota transparency.

8. **[#54591 — Desktop messages stay queued with spinners for 5–10 minutes](https://github.com/openai/codex/issues/54591)**  
   Messages stall even when the session is idle, directly affecting core desktop usability. Six comments indicate a recurring send-path or queue-processing issue.

9. **[#55650 — MCP App first launch stalls with Browser Control attached](https://github.com/openai/codex/issues/55650)**  
   The MCP app stalls while the deployed render acknowledgment remains absent. This points to lifecycle/readiness gaps in MCP and browser integrations; 5 comments.

10. **[#57051 — Windows Desktop cannot load organization settings; bundled CLI CDN needs explicit proxy](https://github.com/openai/codex/issues/57051)**  
   A new Windows regression blocks workspace entry while standalone and bundled CLIs still execute. Four comments already, and the issue is notable because it prevents desktop use entirely.

## 4. Key PR Progress

All items below are closed PRs updated in the last 24 hours; most are from `copyberry[bot]`.

1. **[#57314 — Format all Rust sources and clean up repository checks](https://github.com/openai/codex/pull/57314)**  
   Expands Rust formatting to tracked and untracked files that Cargo cannot discover, while keeping command lines within Windows limits.

2. **[#57315 — Keep failed subagents from marking tasks as needing attention](https://github.com/openai/codex/pull/57315)**  
   Classifies failed subagents as non-user-actionable so parent tasks are not promoted into the `Needs you` group.

3. **[#57317 — Add opt-in concise hints for explicitly selected plugins](https://github.com/openai/codex/pull/57317)**  
   Adds `features.simplified_selection_hints`, disabled by default, to replace verbose plugin instructions with concise developer messages.

4. **[#57313 — Default gateway OAuth delivery to Authorization bearer tokens](https://github.com/openai/codex/pull/57313)**  
   Allows `gateway_oauth.delivery` to be omitted and sends the token as `Authorization: Bearer …`, while preserving custom headers/cookies.

5. **[#57312 — Centralize Guardian root context preparation](https://github.com/openai/codex/pull/57312)**  
   Moves root-message preparation and handoff-context selection into `codex-guardian-context`, simplifying shared context handling.

6. **[#57311 — Preserve tool output versions and history order in rollout traces](https://github.com/openai/codex/pull/57311)**  
   Fixes trace reduction issues where multiple tool outputs or compacted content were treated as conflicting versions.

7. **[#57304 — Surface refresh-token rejection errors during workspace routing](https://github.com/openai/codex/pull/57304)**  
   Propagates permanent refresh-token failures so users see guidance to sign in again instead of a generic discovery error.

8. **[#57293 — Add GPT-6.1 Terra to model catalogs](https://github.com/openai/codex/pull/57293)**  
   Adds `gpt-6.1-terra` to bundled and GCP Vertex catalogs, plus Amazon Bedrock global and US cross-region variants.

9. **[#57290 — Add a plugin process host with bounded IPC and lifecycle management](https://github.com/openai/codex/pull/57290)**  
   Introduces a helper process host with cleared environment, readiness handshake, framed stdin/stdout pipes, a 256 KiB frame limit, and backpressure.

10. **[#57286 — Support `network_proxy` in managed requirements](https://github.com/openai/codex/pull/57286)**  
   Adds `[network_proxy]` to `requirements.toml` while retaining `[experimental_network]` as a compatibility alias for managed environments.

## 5. Hot Discussions

### Show and tell

- **[#56176 — Does a fresh Codex session receive AGENTS.md? A repeatable before/after check](https://github.com/openai/codex/discussions/56176)**  
  Agent Relay Check v0.1.3 uses a temporary random marker and a fresh session to verify that nested `AGENTS.md` instructions are actually loaded.

- **[#57022 — two-engines-gate: Codex reviews a Claude Code commit read-only](https://github.com/openai/codex/discussions/57022)**  
  A local merge gate requires both tests and an explicit `ACCEPT` from Codex for the exact commit before merge.

- **[#56564 — Haru Café: a company before a café, built with Codex](https://github.com/openai/codex/discussions/56564)**  
  A show-and-tell project exploring how an AI agent can help found a company while preserving existing identity and relationships.

- **[#57250 — Open dataset of real coding-agent tool-call failures, including Codex cases](https://github.com/openai/codex/discussions/57250)**  
  A CC BY 4.0 dataset of tool-call failures with trajectories, expected vs. actual behavior, and pass/partial/fail rubrics.

- **[#57011 — Blocking hallucinated and typosquatted packages with a PreToolUse hook](https://github.com/openai/codex/discussions/57011)**  
  `pkg-guard` checks `Bash` and `apply_patch` calls before execution to block nonexistent or typosquatted Python/npm package installs.

- **[#57001 — AEGIS for coding-agent activity on Windows](https://github.com/openai/codex/discussions/57001)**  
  A Windows desktop workspace for inspecting detected coding-agent activity, process trees, file activity, and TCP endpoints.

### Q&A

- **[#57025 — Can app-server apply ToolPolicy when starting a thread?](https://github.com/openai/codex/discussions/57025)**  
  A developer wants a fresh handoff-review thread to run without any tools, using `ToolPolicy.allowed_tools` rather than relying on prompt instructions.

- **[#57029 — Using a work email with Gmail plugins](https://github.com/openai/codex/discussions/57029)**  
  Two Gmail accounts appear connected, but the assistant cannot confirm access to the second mailbox and no account selector is shown.

### Ideas

No explicit Ideas discussion appeared in the provided data.

## 6. Feature Request Trends

The issue set is primarily defect-driven, but recurring directions are clear:

- **Predictable sandbox and policy behavior** — users want false positives eliminated, effective working directories preserved, and blocked commands explained with actionable review paths.
- **Remote/mobile session continuity** — better ownership arbitration, retries for long sessions, and clearer entitlement behavior across desktop, CLI, and iOS.
- **Bounded desktop resource lifecycle** — stronger controls for Computer Use workers, subagent fan-out, renderer memory, and plugin/MCP helper processes.
- **Cross-surface parity and diagnostics** — Windows desktop/CLI/CDN behavior, VS Code extension startup, auth/token errors, and trace/observability correctness.
- **Safer extensibility** — MCP readiness handshakes, browser-tab ownership, plugin IPC isolation, and PreToolUse hooks for package safety.
- **Enterprise configuration and model availability** — managed proxy settings, OAuth delivery defaults, and catalog updates such as GPT-6.1 Terra.

## 7. Developer Pain Points

- **Windows desktop reliability remains the biggest cluster**: organization settings failures, EPERM/runtime errors, CDN/proxy reachability, renderer memory leaks, and sandbox blocks before command execution.
- **macOS Computer Use and memory pressure are recurring blockers**: repeated reports describe `SkyComputerUseService` spawn storms, `CODEX_HOME` symlink leaks, subagent fan-out, V8 OOM, and system-level watchdog crashes.
- **Remote Control and mobile access are brittle**: HTTP 409 ownership collisions, iOS session-loading timeouts without retry, and mobile entitlement gating create cross-device friction.
- **Sandbox/policy opacity erodes trust**: commands are sometimes rejected before execution, with no review entry or clear explanation, while hooks can receive incorrect working-directory context.
- **Core desktop UI state can fail silently or slowly**: messages queue for minutes, completed responses disappear, and approval requests become “no longer active.”
- **Plugin/MCP/browser lifecycle issues are emerging**: first-launch stalls, missing render acknowledgments, and browser-tab ownership conflicts make integrations feel unreliable.
- **Billing and quota transparency matters**: promotional credits and weekly allowances can appear inconsistent, prompting detailed user reports.
- **IDE extension startup is not immune**: the VS Code extension has a reported `renderer_ready_timeout` after 30 seconds on Windows.

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-10-27

## 1. Today's Highlights
The nightly channel shipped **v0.67.0-nightly.20261027**, headlined by a fix that classifies AI Studio project spending-cap errors as terminal quota errors (so they no longer masquerade as transient failures) and an ACP improvement that surfaces MCP server/tool metadata inside permission requests. Activity remains heavily concentrated on **agent reliability**: subagent turn-limit reporting, generalist-agent hangs, and browser-agent failures continue to dominate the top-commented issues. On the PR side, a broad set of terminal-rendering, ACP output, and background-log correctness fixes are in flight.

## 2. Releases
**v0.67.0-nightly.20261027.g363a13119**
- **`fix(core)`: classify project spending cap errors as terminal quota errors** — [#29961](https://github.com/google-gemini/gemini-cli/pull/29961). When AI Studio returns HTTP 429/400 with an empty `details` array and a spending-cap message, `classifyGoogleError` now treats it as terminal rather than retrying indefinitely.
- **`fix(acp)`: include MCP server and tool metadata in permission requests** — [#29910](https://github.com/google-gemini/gemini-cli/pull/29910). Populates `toolCall._meta['gemini-cli'].mcp` with `{ server_name, tool_name }` and surfaces specific names in permission option labels, improving consent UX for ACP clients.
- Plus the automated version bump: [#29972](https://github.com/google-gemini/gemini-cli/pull/29972).

## 3. Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent MAX_TURNS recovery misreported as GOAL success (P1, 13 comments, 👍2).** The `codebase_investigator` subagent returns `status: "success"` / `Termination Reason: "GOAL"` even though its own result states it hit the turn limit before analyzing anything. This is the highest-engagement issue today and a serious trust problem for anyone automating agents — silent truncation is worse than a visible failure.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs (P1, 8 comments, 👍8).** Whenever the CLI defers to the generalist agent it hangs indefinitely, even for trivial operations like creating a folder; the workaround is instructing the model not to delegate. The highest-reaction issue in the set, signaling broad user impact.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing & post-execution intent routing (P2, 9 comments).** Proposes leaning into Gemini 3's native bash affinity (`grep`, `sed`, `awk`) while keeping security intact via sandboxing. A significant architectural direction for how the CLI reconciles capability with safety.

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini does not use skills and sub-agents enough (P2, 7 comments).** Anecdotal but widely shared: custom skills and subagents are only invoked when explicitly instructed, never proactively, even for clearly matching tasks (e.g., a `gradle` skill). Points at a routing/selection problem rather than a capability gap.

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — EPIC: assess AST-aware file reads, search, and mapping (P2, 7 comments).** Tracks whether AST-aware tooling can cut turns from misaligned reads and reduce token noise. Companion issues [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) and [#22747](https://github.com/google-gemini/gemini-cli/issues/22747) explore `ast-grep`, `tilth`, and `glyph` as candidates.

6. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland (P1, 4 comments).** Browser agent reports `Termination Reason: GOAL` while failing outright on Wayland sessions — a platform-compatibility gap that mirrors the reporting-accuracy problem in #22323.

7. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with >128 tools (P2, 3 comments).** The CLI hits HTTP 400 when too many tools are in scope; the reporter expects the agent to be smarter about limiting enabled tools. Relevant as MCP server adoption grows.

8. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should stop/discourage destructive behavior (3 comments, 👍1).** The model reaches for `git reset` or `--force` when safer paths exist, and lacks guardrails around DB/resource modifications. A safety-nudge request rather than a hard block.

9. **[#23571](https://github.com/google-gemini/gemini-cli/issues/23571) — Model creates tmp scripts in random spots (P2, 3 comments).** Shell-only configurations cause the model to scatter edit scripts across directories, creating cleanup overhead before a clean commit. A workspace-hygiene complaint with practical CI implications.

10. **[#22465](https://github.com/google-gemini/gemini-cli/issues/22465) — Stuck at interactive prompt creating a Vite app (P2, 2 comments).** The agent stalls on scaffolding prompts; the proposed fix is a new behavioral eval plus prompt adjustment. Illustrates the recurring "non-interactive agent meets interactive CLI" class of failure.

*Also worth watching:* [#22598](https://github.com/google-gemini/gemini-cli/issues/22598) (expose subagent trajectories via `/chat share`), [#21763](https://github.com/google-gemini/gemini-cli/issues/21763) (`/bug` reports omit subagent context), [#20079](https://github.com/google-gemini/gemini-cli/issues/20079) (symlinked agent files not discovered), [#22267](https://github.com/google-gemini/gemini-cli/issues/22267) (Browser Agent ignores `settings.json` overrides).

## 4. Key PR Progress

1. **[#29961](https://github.com/google-gemini/gemini-cli/pull/29961) — classify spending-cap errors as terminal (merged into nightly).** Prevents wasted retries and ambiguous quota failures for AI Studio users.

2. **[#29910](https://github.com/google-gemini/gemini-cli/pull/29910) — ACP permission requests carry MCP server/tool metadata (merged).** Better consent prompts for ACP-integrated clients; falls back gracefully when metadata is absent.

3. **[#29958](https://github.com/google-gemini/gemini-cli/pull/29958) — send ACP clients shell output for `AnsiOutput` results.** Previously `toToolCallContent` returned `null` because `shell.ts` prefers PTY snapshots, leaving ACP clients with empty `tool_call_update` events.

4. **[#29884](https://github.com/google-gemini/gemini-cli/pull/29884) — allow trusted system paths on Android/Termux for ripgrep.** Extends `isTrustedSystemPath()` so `pkg install ripgrep` binaries resolve and execute under `grep_search`. Good signal for mobile/embedded developer support.

5. **[#29960](https://github.com/google-gemini/gemini-cli/pull/29960) — telemetry `SERVICE_VERSION` uses CLI version, not Node runtime.** Fixes [#29893](https://github.com/google-gemini/gemini-cli/issues/29893); essential for anyone doing version-segmented observability on OpenTelemetry data.

6. **[#29905](https://github.com/google-gemini/gemini-cli/pull/29905) — handle terminals rendering ambiguous-width chars as wide.** Fixes [#24181](https://github.com/google-gemini/gemini-cli/issues/24181) across macOS Terminal.app, iTerm2, VTE, WezTerm, and `screen cjkwidth` setups.

7. **[#29904](https://github.com/google-gemini/gemini-cli/pull/29904) — bound inline history re-render on terminal width changes.** Directly targets the flicker/performance problem tracked in [#21924](https://github.com/google-gemini/gemini-cli/issues/21924).

8. **[#29947](https://github.com/google-gemini/gemini-cli/pull/29947) & [#29948](https://github.com/google-gemini/gemini-cli/pull/29948) — background log tailing fixes.** The first preserves a partial tail when a single line exceeds the 64 KB window (previously all output was silently discarded); the second makes background output reads honor cancellation instead of completing after abort.

9. **[#29966](https://github.com/google-gemini/gemini-cli/pull/29966) — normalize CRLF before diffing in `getDiffContextSnippet`.** Addresses mismatched diffs when editing Windows/CRLF files where `currentContent` is LF-normalized but `finalContent` is converted back.

10. **[#29959](https://github.com/google-gemini/gemini-cli/pull/29959) — configurable numeric score-based model routing.** Adds user-defined score ranges mapped to models while preserving default routing — a notable step toward cost/latency tuning.

*Also in flight:* [#29951](https://github.com/google-gemini/gemini-cli/pull/29951) (P0: retry transient VS Code download failures in integration tests), [#29965](https://github.com/google-gemini/gemini-cli/pull/29965) (Unicode surrogate-pair truncation in `ExpandableText`), [#29886](https://github.com/google-gemini/gemini-cli/pull/29886) (exclude test files from steering-signature eval detection).

## 5. Hot Discussions
No discussion data was provided for this period, so this section is omitted.

## 6. Feature Request Trends

- **Subagent observability & control** — The largest cluster by far: expose trajectories via `/chat share` ([#22598](https://github.com/google-gemini/gemini-cli/issues/22598)), include subagent context in `/bug` reports ([#21763](https://github.com/google-gemini/gemini-cli/issues/21763)), correct misleading termination reasons ([#22323](https://github.com/google-gemini/gemini-cli/issues/22323)), and make subagent discovery configurable via `settings.json` ([#18285](https://github.com/google-gemini/gemini-cli/issues/18285), [#20079](https://github.com/google-gemini/gemini-cli/issues/20079)).
- **Context efficiency via AST-aware tooling** — A coordinated EPIC ([#22745](https://github.com/google-gemini/gemini-cli/issues/22745), [#22746](https://github.com/google-gemini/gemini-cli/issues/22746), [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)) plus "tactful extraction" surgical reads ([#19561](https://github.com/google-gemini/gemini-cli/issues/19561)) target the ~36.6k-token-per-turn baseline.
- **Persistent task tracking** — Replace in-context `WriteToDo` with file-based CRUD to survive context rot and session boundaries ([#18836](https://github.com/google-gemini/gemini-cli/issues/18836), [#21000](https://github.com/google-gemini/gemini-cli/issues/21000)).
- **Sandboxing without losing bash fluency** — Zero-dependency OS sandboxing and post-execution intent routing ([#19873](https://github.com/google-gemini/gemini-cli/issues/19873)).
- **Scoped policy & configuration** — Per-workspace policy instead of global ([#18397](https://github.com/google-gemini/gemini-cli/issues/18397)); configurable numeric model routing ([#29959](https://github.com/google-gemini/gemini-cli/pull/29959)).
- **Agent self-awareness** — Accurate knowledge of CLI flags, hotkeys, and self-execution ([#21432](https://github.com/google-gemini/gemini-cli/issues/21432)).
- **Browser agent resilience** — Session takeover and lock recovery instead of fail-fast ([#22232](https://github.com/google-gemini/gemini-cli/issues/22232)) and correct honoring of `settings.json` ([#22267](https://github.com/google-gemini/gemini-cli/issues/22267)).

## 7. Developer Pain Points

- **Agent hangs without diagnostics.** Generalist-agent delegation stalls indefinitely ([#21409](https://github.com/google-gemini/gemini-cli/issues/21409)), and the browser subagent fails on Wayland while still reporting `GOAL` ([#21983](https://github.com/google-gemini/gemini-cli/issues/21983)). Users are left with no actionable signal.
- **Misleading success semantics.** The single most-commented issue is a reporting bug: hitting `MAX_TURNS` is surfaced as `status: "success"` ([#22323](https://github.com/google-gemini/gemini-cli/issues/22323)). Combined with `GOAL` termination reasons on failures, this erodes trust in agent output.
- **Under-utilization of skills/subagents.** Capabilities exist but are rarely selected autonomously ([#21968](https://github.com/google-gemini/gemini-cli/issues/21968)) — users must micromanage invocation.
- **Tool-count ceilings.** More than ~128 tools triggers HTTP 400 with no graceful degradation ([#24246](https://github.com/google-gemini/gemini-cli/issues/24246)), increasingly likely as MCP servers multiply.
- **Destructive command hygiene.** Unnecessary `git reset` / `--force` usage and unguarded resource mutation ([#22672](https://github.com/google-gemini/gemini-cli/issues/22672)), plus stray temp scripts littering workspaces ([#23571](https://github.com/google-gemini/gemini-cli/issues/23571)).
- **Interactive prompts break automation.** Scaffolding a Vite app deadlocks at a prompt ([#22465](https://github.com/google-gemini/gemini-cli/issues/22465)) — a recurring pattern for non-interactive agent flows.
- **Cross-platform rough edges.** Termux ripgrep trust ([#29884](https://github.com/google-gemini/gemini-cli/pull/29884)), Windows CRLF diffs ([#29966](https://github.com/google-gemini/gemini-cli/pull/29966)), East Asian ambiguous-width rendering ([#29905](https://github.com/google-gemini/gemini-cli/pull/29905)), and terminal resize flicker ([#21924](https://github.com/google-gemini/gemini-cli/issues/21924)) collectively show that terminal/OS portability remains a steady tax on users.
- **Eval instability.** Flaky steering and internal project evals ("bleed") reduce confidence in quality trend tracking ([#23313](https://github.com/google-gemini/gemini-cli/issues/23313), [#23166](https://github.com/google-gemini/gemini-cli/issues/23166)).

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — Community Digest
**Date:** 2026-10-27 · **Source:** [github/copilot-cli](https://github.com/github/copilot-cli)

---

## 1. Today's Highlights

Activity today is dominated by reliability engineering rather than new surface area: a sparse patch release (**v1.0.104-4**) shipped with no enumerated changelog, while 50 issues were updated in the last 24 hours and **zero pull requests** moved. The two loudest themes are **session lifecycle fragility** (resume OOMs, lock acquisition timeouts, unresumable sessions) and **MCP/OAuth integration breakage** across desktop, headless, and enterprise registry configurations. One long-standing win: the aggressive HTTP 429 retry bug (#2760) has been **closed** after more than six months.

---

## 2. Releases

**v1.0.104-4** — published within the last 24h.
- Changelog is limited to "Fixes and changes"; no itemized bug list or PR references were attached.
- No corresponding PRs appear in the last-24h window, so the specific fixes cannot be corroborated from this data.
- Practically: treat this as an opaque patch build. Teams pinned to 1.0.103 (see [#5253](https://github.com/github/copilot-cli/issues/5253) session-lock timeouts) may want to test 104-4 as a candidate mitigation, but the release notes provide no confirmation.

---

## 3. Hot Issues

1. **[#4024](https://github.com/github/copilot-cli/issues/4024) — Voice mode: all bundled ASR models fail silently (14 comments)** `OPEN` `area:models`
   The highest-engagement thread in the window. `/voice` records audio correctly but every model in the picker returns empty transcriptions, traced to a MultiModalProcessor routing bug for `nemotron_speech` (RNNT) in Foundry Local Core. Notable for the quality of the repro (raw PulseAudio capture as evidence) and for being silent-failure rather than a crash.

2. **[#2760](https://github.com/github/copilot-cli/issues/2760) — Implement proper HTTP retry logic for 429 responses (8 comments, 2 👍) — CLOSED**
   The marquee closure of the day. Copilot CLI was retrying 429s immediately without backoff, producing 20+ retries/minute storms. Closure signals a real fix, though the lack of a linked PR or release note leaves users guessing which build carries it.

3. **[#4905](https://github.com/github/copilot-cli/issues/4905) — Desktop app: sessions die minutes after spawn (7 comments, 4 👍)** `OPEN` `triage`
   "GitHub credential registration is no longer available for this session" invalidates the `github-mcp-server` catalog and turns it fatal. High-impact because it hits the default desktop path, and the reporter's evidence (valid `gh auth status`, valid exported `GH_TOKEN`) makes this hard to dismiss as environment error.

4. **[#4251](https://github.com/github/copilot-cli/issues/4251) — Resume of a large session OOMs / pins one core for ~70 min (5 comments, 1 👍)** `OPEN` `area:sessions`
   A cleanly isolated regression: controlled A/B on identical machine and session shows 1.0.74 at ~3–4× memory versus 1.0.73. Worked example of regression bisection and a direct blocker for long-lived session users.

5. **[#5253](https://github.com/github/copilot-cli/issues/5253) — CLI 1.0.103: persistent session-lock timeouts and unresponsive tasks (4 comments)** `OPEN` `area:sessions` `area:agents`
   `session lock unavailable during is_session_generation_current` leaves sessions alive but UI-unresponsive. Filed 2026-10-25, so it is the freshest high-severity regression in the window and likely the strongest candidate for the 1.0.104-4 patch.

6. **[#4017](https://github.com/github/copilot-cli/issues/4017) — MCP OAuth in Desktop: non-first-party HTTP servers cancel host token, never launch browser flow (4 comments, 3 👍)** `OPEN` `triaged`
   Atlassian, incident.io and similar servers simply never connect — no popup, no error. The "no auth, no error, no popup" failure mode is what makes this expensive to debug for enterprise teams.

7. **[#3976](https://github.com/github/copilot-cli/issues/3976) — Native `tgrep` indexer OOM-kills the host on large monorepos (4 comments)** `OPEN` `area:tools`
   The Rust trigram indexer daemon spawns at session start with no upper memory bound. Critical for monorepo shops, and notable because it's an experimental flag (`copilot_cli_tgrep`) reaching production users.

8. **[#4026](https://github.com/github/copilot-cli/issues/4026) — Windows: repeated native-runtime crashes, unresolved since May 2026 (4 comments)** `OPEN` `area:platform-windows`
   Unpredictable crashes across at least four versions (1.0.15 → 1.0.53+). Long-lived, platform-specific, and still untriaged — a persistent signal that Windows parity lags macOS/Linux.

9. **[#4537](https://github.com/github/copilot-cli/issues/4537) — ACP mode auto-approves tool calls again; `session/request_permission` not sent since 1.0.81-1 (2 comments, 2 👍)** `OPEN` `area:permissions`
   A regression of an earlier fix (#845): shell commands, edits, and deletions execute unattended in `--acp` mode. The most security-relevant open issue in the set — clients are given no opportunity to prompt and nothing is logged.

10. **[#5149](https://github.com/github/copilot-cli/issues/5149) — Claude Opus 5.5: 32k `maxOutputTokens` yields empty turns at max/xhigh reasoning; auto-continue bills each one (2 comments)** `OPEN` `area:non-interactive` `area:models`
    Reasoning tokens count against a hard 32k cap, so long tasks return empty turns that auto-continue then bills. A cost-correctness bug with direct quota impact for CI/headless users.

*Also active but outside the top 10:* [#4486](https://github.com/github/copilot-cli/issues/4486) edit-permission prompt timeouts, [#4392](https://github.com/github/copilot-cli/issues/4392) orphaned stdio MCP processes after auth rebuild, [#3679](https://github.com/github/copilot-cli/issues/3679) BYOK Azure 429s exhausting retries in ~0.15s, [#5242](https://github.com/github/copilot-cli/issues/5242) inotify `ENOSPC` abandoning the session queue.

---

## 4. Key PR Progress

**No pull requests were updated in the last 24 hours (0 items).** There is no merge or review activity to report for this window.

As a proxy for code movement, the only shipping signal is the closure of **[#2760](https://github.com/github/copilot-cli/issues/2760)** (proper 429 retry/backoff), which is the last item in the retry-behavior cluster and pairs with the still-open BYOK variant **[#3679](https://github.com/github/copilot-cli/issues/3679)**. The gap between issue closures and visible PR activity also explains why v1.0.104-4's changelog is uninformative — the release is not traceable to public PRs in this dataset.

---

## 5. Hot Discussions

No discussion data was provided for this window, so this section is omitted.

---

## 6. Feature Request Trends

- **Model and provider flexibility (highest volume).** Per-agent provider selection so two agents in one session can hit different endpoints ([#4703](https://github.com/github/copilot-cli/issues/4703)); ACP clients cannot list or select models at all ([#4880](https://github.com/github/copilot-cli/issues/4880)); `auto` model silently forces `reasoningEffort: null` with no override ([#4560](https://github.com/github/copilot-cli/issues/4560)); rubber-duck review should pick an out-of-family reviewer ([#4380](https://github.com/github/copilot-cli/issues/4380)).
- **Retry and throttling policy should be configurable.** Beyond the closed #2760, users want tunable retry counts and backoff for BYOK/Azure OpenAI rather than a hard-coded 5 near-instant retries ([#3679](https://github.com/github/copilot-cli/issues/3679)).
- **Headless (`-p`) and ACP parity with the interactive TUI.** Repo-level `enabledPlugins` ignored in `-p` mode ([#4507](https://github.com/github/copilot-cli/issues/4507)); `skill` tool intermittently unresolved in headless runs ([#4838](https://github.com/github/copilot-cli/issues/4838)); `session/prompt` unconditionally aborts background sub-agents ([#4555](https://github.com/github/copilot-cli/issues/4555)); permission requests regressed in ACP ([#4537](https://github.com/github/copilot-cli/issues/4537)).
- **MCP enterprise readiness.** Custom MCP registries reachable from VS Code but not the CLI ([#4949](https://github.com/github/copilot-cli/issues/4949)); stateful-server OAuth probes returning HTTP 400 with a valid stored token ([#5014](https://github.com/github/copilot-cli/issues/5014)); OAuth initialization runaway on Windows ([#5134](https://github.com/github/copilot-cli/issues/5134)).
- **Permission UX that survives idle sessions.** Edit-approval prompts timing out on overnight or parallel sessions ([#4486](https://github.com/github/copilot-cli/issues/4486)).

---

## 7. Developer Pain Points

- **Session durability is the #1 systemic risk.** Four of the top ten issues concern the same lifecycle: OOM on resume ([#4251](https://github.com/github/copilot-cli/issues/4251)), lock-acquisition deadlocks ([#5253](https://github.com/github/copilot-cli/issues/5253)), unresumable sessions from masked string counters ([#5023](https://github.com/github/copilot-cli/issues/5023)), and model-prefix mismatches blocking resume ([#4282](https://github.com/github/copilot-cli/issues/4282)). For anyone running long-lived or programmatic sessions, this is the difference between a tool and a liability.
- **Silent failure is the default failure mode.** Empty voice transcriptions ([#4024](https://github.com/github/copilot-cli/issues/4024)), MCP OAuth with no popup and no error ([#4017](https://github.com/github/copilot-cli/issues/4017)), and unanswered permission requests with nothing written to the log ([#4537](https://github.com/github/copilot-cli/issues/4537)) all waste hours on misattributed debugging.
- **Local resource exhaustion from background daemons.** `tgrep` OOM-killing the host ([#3976](https://github.com/github/copilot-cli/issues/3976)), orphaned stdio MCP children on every auth-driven client rebuild ([#4392](https://github.com/github/copilot-cli/issues/4392)), and inotify `ENOSPC` silently abandoning the queue on shared Linux hosts ([#5242](https://github.com/github/copilot-cli/issues/5242)).
- **Regressions ship without bisectable changelogs.** Multiple reports pin breakage to exact versions (1.0.73→1.0.74, 1.0.81-1, 1.0.90-0, 1.0.103), yet release notes like v1.0.104-4's "Fixes and changes" give pinned teams nothing to evaluate against.
- **Platform parity gap.** Windows native-runtime crashes open since May 2026 ([#4026](https://github.com/github/copilot-cli/issues/4026)) and Windows-specific OAuth loops ([#5134](https://github.com/github/copilot-cli/issues/5134)) persist while macOS/Linux fixes land.
- **Cost surprises from output-token capping.** Opus 5.5 empty turns that auto-continue still bills ([#5149](https://github.com/github/copilot-cli/issues/5149)) and unbacked-off 429 retries burn quota ([#3679](https://github.com/github/copilot-cli/issues/3679)) — both erode trust in headless/CI usage where spend is unattended.

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-10-27

## Today's Highlights

Free-tier access dominated the day: a mega-thread for quota/reset failures ([#59387](https://github.com/anomalyco/opencode/issues/59387)) is collecting duplicate reports, while two high-profile threads closed — the long-running clipboard bug ([#4283](https://github.com/anomalyco/opencode/issues/4283), 159 comments) and the VPN-based free-model exploit ([#34344](https://github.com/anomalyco/opencode/issues/34344)). On the platform side, the Desktop GUI plugin SDK landed ([#55246](https://github.com/anomalyco/opencode/pull/55246)) with follow-up primitives ([#59462](https://github.com/anomalyco/opencode/pull/59462)), alongside richer model pricing metadata ([#59460](https://github.com/anomalyco/opencode/pull/59460)). A revert of server username auth ([#59444](https://github.com/anomalyco/opencode/pull/59444)) signals ongoing v1→v2 compatibility churn.

## Hot Issues

1. **[#4283](https://github.com/anomalyco/opencode/issues/4283) — Copy to Clipboard is not working (CLOSED)** — The most-engaged issue in this batch (159 comments, 134 👍), open since Nov 2025 and finally closed. Selection/copy in the TUI is a core workflow; the reaction volume shows how much friction a single platform-level clipboard bug creates.

2. **[#56324](https://github.com/anomalyco/opencode/issues/56324) — Free models fail in official TUI with "free tier can only be used from within OpenCode" (CLOSED, severity:high)** — 71 comments, 30 👍. Windows users on v2.0.32 were blocked by an entitlement/telemetry check failing inside the official app itself. Closed, but the "official client rejected as unofficial" failure mode is a serious trust issue for gating logic.

3. **[#34344](https://github.com/anomalyco/opencode/issues/34344) — Unlimited usage exploit on free models (CLOSED)** — 64 comments. Rate limits keyed to IP meant VPN rotation reset quotas; the reporter automated it. Relevant context for the current quota tightening and collateral free-tier failures.

4. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — [zen] Free tier issues: read this first, then post your report here (OPEN)** — Maintainer-created aggregation thread (10 comments, 3 👍) covering "Free usage exceeded", limits that never reset, and Desktop version mismatch. Its existence confirms the volume of duplicate reports.

5. **[#6912](https://github.com/anomalyco/opencode/issues/6912) — Ctrl+C does not disable mouse tracking on Windows (CLOSED)** — 14 comments, 21 👍. Terminal left emitting SGR mouse sequences after exit, rendering the shell unusable. Classic terminal-hygiene bug with outsized user impact.

6. **[#32825](https://github.com/anomalyco/opencode/issues/32825) — OPENCODE_CONFIG_DIR replaces global config in v2 (OPEN)** — 10 comments. The v2/core loader treats the variable as a replacement for XDG global config rather than an additional directory. A silent configuration-semantics change during migration.

7. **[#45856](https://github.com/anomalyco/opencode/issues/45856) — v2 serve: Basic Auth credentials always return 401 (CLOSED)** — 9 comments. `OPENCODE_SERVER_USERNAME`/`PASSWORD` were rejected, causing endless browser login prompts. The subsequent revert ([#59444](https://github.com/anomalyco/opencode/pull/59444)) removes `username` from `ServerOptions`, so expect follow-on breakage.

8. **[#36990](https://github.com/anomalyco/opencode/issues/36990) — core: restore non-experimental environment variable compatibility in v2 (OPEN)** — Bot-maintained umbrella (8 comments) tracking v1 env vars declared in `flag.ts` with no active v2 consumer. The clearest signal that v2 parity debt is being tracked systematically.

9. **[#54603](https://github.com/anomalyco/opencode/issues/54603) — npm package ships the native binary as `bin/opencode.exe` on Linux and macOS (OPEN)** — 7 comments, reproduced. Cosmetic-looking but breaks tooling that resolves the binary by name; the official `install.sh` already uses the unsuffixed name.

10. **[#58354](https://github.com/anomalyco/opencode/issues/58354) — Shell tool output containing NUL bytes bricks the session (OPEN, reproduced)** — 3 comments. NUL bytes in tool output produce a provider 400 ("String must not contain null bytes"), leaving the session unrecoverable. A sanitization gap at the tool-output boundary.

## Key PR Progress

1. **[#55246](https://github.com/anomalyco/opencode/pull/55246) — feat(plugin): add OpenCode Desktop GUI plugins (CLOSED/landed)** — Plugins can ship a `./gui-experimental` export alongside server and `./tui` entries; OpenCode's own features are built as plugins on the same SDK, and **Settings > Plugins** lists both.

2. **[#59462](https://github.com/anomalyco/opencode/pull/59462) — feat(plugin): GUI toast, ask dialogs, shell tabs and composer text (OPEN)** — Fills the SDK gaps needed to port TUI plugins (blindfold, agent-meetup, quick-links, pr-link, keep-going) to Desktop.

3. **[#59460](https://github.com/anomalyco/opencode/pull/59460) — feat(core): carry models.dev v2 pricing on models (OPEN)** — Expands `Model.Info.cost` to cover audio/image/video pricing, web search fees, reasoning output, cache writes per TTL, cache storage, and region/date/service multipliers.

4. **[#59190](https://github.com/anomalyco/opencode/pull/59190) — feat(protocol): experimental image and decision generation endpoints (OPEN)** — Contract-first: requests decode, then fail explicitly as unimplemented, so clients can build against a stable HTTP shape.

5. **[#58782](https://github.com/anomalyco/opencode/pull/58782) — feat(core): add Claude Agent provider (OPEN, needs:compliance)** — Runs Claude models through the user's own Claude Code login via the Agent SDK, selectable in `/connect`. Blocked on compliance review.

6. **[#59457](https://github.com/anomalyco/opencode/pull/59457) — feat(core): schedule prompts from agents, /schedule and the experimental API (OPEN)** — Enables "check CI every hour for 8 hours" style sessions; gated behind `experimental.schedules`, stacked on storage/timer work in #59401.

7. **[#58586](https://github.com/anomalyco/opencode/pull/58586) — perf(app): open sessions without downloading read-tool images (OPEN)** — The timeline loads images by path instead of shipping base64 for every read image, via a new experimental endpoint. Directly targets large-session load time.

8. **[#59459](https://github.com/anomalyco/opencode/pull/59459) — fix(server): report response encoding failures as server errors (OPEN)** — Schema mismatches were surfacing as `400 InvalidRequestError` (`kind: "Body"`); now correctly classified as 5xx instead of blaming the client.

9. **[#59444](https://github.com/anomalyco/opencode/pull/59444) — Revert "fix(server): honor OPENCODE_SERVER_USERNAME for serve and --server" (CLOSED)** — Reverts #59379; removes `username` from `ServerOptions` and stops honoring the env var. Explicitly not backwards compatible — worth tracking against #45856 and #36990.

10. **[#59403](https://github.com/anomalyco/opencode/pull/59403) — refactor(nix): fetch dependencies from bun.lock (OPEN)** — Builds bun2nix's offline cache from the lockfile, eliminating per-platform `node_modules` hash maintenance.

**Also in flight:** [#59072](https://github.com/anomalyco/opencode/pull/59072) groups experimental SDK methods under `client.experimental`; [#59445](https://github.com/anomalyco/opencode/pull/59445) adds async tools and `generateObject` to the Promise client; [#59448](https://github.com/anomalyco/opencode/pull/59448) makes the model retry budget env-configurable; [#59446](https://github.com/anomalyco/opencode/pull/59446) declares input modalities in the system prompt; [#59447](https://github.com/anomalyco/opencode/pull/59447) merges 76 `dev` commits into `beta` to unbreak e2e.

## Feature Request Trends

- **Free-tier transparency and accounting** — [#57540](https://github.com/anomalyco/opencode/issues/57540) asks for documented idle-time behavior, pool scope, and remaining allowance; the steady stream of reset complaints reinforces that quota mechanics are effectively undocumented.
- **V1→V2 configuration parity** — [#36990](https://github.com/anomalyco/opencode/issues/36990) (env vars) and [#32825](https://github.com/anomalyco/opencode/issues/32825) (config dir semantics) point to a broader expectation that existing setups keep working across the v2 transition.
- **Extensibility and UI customization** — Desktop GUI plugins (#55246, #59462), reactive TUI plugin slots ([#59458](https://github.com/anomalyco/opencode/issues/59458)), and the Claude Agent provider (#58782) all extend what third parties can build.
- **Operational tooling for local state** — [#54618](https://github.com/anomalyco/opencode/issues/54618) requests offline SQLite inspection, verified backups, and vacuum for v2 storage.
- **Richer model metadata** — pricing depth (#59460), modality declaration (#59446), and image/decision endpoints (#59190) reflect demand for first-class multimodal and cost-aware routing.
- **Automation and scheduling** — #59457 introduces prompt scheduling, a new direction beyond interactive sessions.

## Developer Pain Points

- **Free-tier quota and reset logic is the dominant frustration.** Reports span "exceeded on first message" ([#58550](https://github.com/anomalyco/opencode/issues/58550)), counters stuck at 61k–84k seconds ([#59116](https://github.com/anomalyco/opencode/issues/59116), [#59118](https://github.com/anomalyco/opencode/issues/59118), [#59307](https://github.com/anomalyco/opencode/issues/59307)), Desktop-vs-CLI inconsistency ([#59112](https://github.com/anomalyco/opencode/issues/59112)), and non-English reports ([#59111](https://github.com/anomalyco/opencode/issues/59111)). Repeated canned bot responses amplify dissatisfaction.
- **Windows terminal/desktop reliability** remains a recurring theme: clipboard (#4283), mouse tracking left enabled after Ctrl+C (#6912), and the official-app entitlement check failing (#56324).
- **V2 migration regressions and API churn** — env var semantics (#36990), config directory handling (#32825), Basic Auth (#45856), and the auth revert (#59444) mean teams must re-verify deployment config twice.
- **Cold-start race conditions in list commands** — [#54654](https://github.com/anomalyco/opencode/issues/54654) (`plugin list` empty on first call) is the latest in a pattern across `mcp.list`, `skill.list`, `agent.list`, and `model.list`, suggesting a shared caching/lifecycle defect.
- **Long-running agent sessions lose observability** — [#58570](https://github.com/anomalyco/opencode/issues/58570): the assistant stops rendering text mid-task, keeps calling tools, and produces no final report.
- **Tool I/O robustness** — NUL bytes brick a session (#58354), and internal encoding failures were misreported as client errors until #59459.
- **Packaging/install correctness** — the `.exe` suffix on Unix (#54603) and a 404-ing MCP package reference ([#58571](https://github.com/anomalyco/opencode/issues/58571)) show install paths are still brittle.
- **Support traffic in the issue tracker** — billing/refund requests ([#59117](https://github.com/anomalyco/opencode/issues/59117), [#59436](https://github.com/anomalyco/opencode/issues/59436)) indicate a missing self-serve escalation channel.

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-10-27

## 1. Today's Highlights

No new releases shipped in the last 24 hours, but the repo saw a heavy maintenance day: the long-running Windows UX thread (#7547) remains the highest-engagement issue at 90 comments, while a wave of bug-fix PRs landed around MCP lifecycle safety, tool-search ranking, undici security pinning, and TUI rendering. Two `prio:p0` items stay open — Bedrock's 16-optional-parameter limit (#11416) and OpenCode protocol-specific endpoints (#11132) — alongside growing concern about unbounded compaction state (#11297) and long-session credential staleness (#11221).

## 2. Releases

No releases in the last 24 hours.

## 3. Hot Issues

1. **[#7547](https://github.com/earendil-works/pi/issues/7547) — [Windows] How do you use Pi on Windows? What issues are you seeing?** (90 comments, 2 👍)
   The de facto Windows megathread. The author argues there are "gazillion" Windows developers but too many supported run paths to know where to invest — fixing bugs, writing docs, or pushing paths out of core into extensions. Highest community engagement by a wide margin and the clearest signal that Windows parity is a strategic decision, not just a bug queue.

2. **[#11297](https://github.com/earendil-works/pi/issues/11297) — Compaction file lists grow without bound across compactions** (5 comments)
   `extractFileOperations` seeds each new compaction's `fileOps` from the previous compaction's `details.readFiles`/`modifiedFiles`, so `<read-files>`/`<modified-files>` lists accumulate indefinitely. This is a slow-burn context-bloat bug: long sessions silently degrade in quality and cost. Still open, no fix PR linked.

3. **[#11221](https://github.com/earendil-works/pi/issues/11221) — Pi doesn't pick up refreshed AWS credentials in a long-running session** (4 comments)
   Bedrock sessions fail with `ExpiredTokenException` after a few hours even though a background script refreshes credentials on disk. Directly blocks the "leave the agent running all day" workflow that heavy users rely on. No 👍 yet, but high practical impact for enterprise/Bedrock users.

4. **[#11335](https://github.com/earendil-works/pi/issues/11335) — MCP HTTP JSON responses bypass `maxMessageBytes`** (4 comments)
   With a 512-byte limit, a 2 KB tool description is accepted over the JSON path but correctly rejected over SSE. An inconsistency in a safety limit is a correctness and DoS-surface issue for MCP servers; the asymmetry suggests the two transports diverge in validation code.

5. **[#10470](https://github.com/earendil-works/pi/issues/10470) — RPC `new_session`/`switch_session`/`fork`/`clone` emit `session_start` twice** (7 comments)
   Extensions receive duplicate `session_start` events for the new session in RPC mode. Duplicate lifecycle events are the classic source of double-initialized extension state; this is the most-commented open API-correctness issue in the set.

6. **[#10259](https://github.com/earendil-works/pi/issues/10259) — Public tool-presentation API or built-in one-line tool mode** (6 comments, 2 👍)
   Requests either a display-only API to control rendering of *all* tool rows (built-ins, MCP, codemode, custom) or a compact one-line mode with name, call summary, and status. Strong signal that extensions want presentation-layer control without forking the TUI.

7. **[#11416](https://github.com/earendil-works/pi/issues/11416) — [prio:p0] Bedrock: request fails when tools have more than 16 optional parameters** (2 comments)
   Bedrock rejects schemas with >16 union-typed parameters ("Schemas contains too many parameters with union types"). Since Pi's own toolset plus MCP tools can easily cross that threshold, this is a hard provider-level ceiling that needs schema flattening or splitting upstream.

8. **[#11132](https://github.com/earendil-works/pi/issues/11132) — [prio:p0] Use OpenCode's protocol-specific endpoints** (3 comments)
   All OpenCode Zen/Go traffic goes to the shared base URL instead of the documented per-protocol endpoints, which is also what models.dev lists per model. Routing users to the wrong endpoint risks capability mismatches and billing/telemetry surprises. A PR (#11434) is already open.

9. **[#11383](https://github.com/earendil-works/pi/issues/11383) — Pasted ANSI leaves escape-sequence corpses; unterminated bracketed paste wedges the editor** (2 comments, 1 👍)
   Pasting terminal output injects literal escape bodies into the prompt, and a truncated bracketed paste leaves the editor unresponsive — including Ctrl+C. Reproducible in `pi-tui` 1.4.0's `handlePaste()`. An unrecoverable editor state is among the worst TUI failure modes.

10. **[#11413](https://github.com/earendil-works/pi/issues/11413) — [prio:p0, fix-available] Bump pinned undici from 8.10.2 to ≥8.12.2** (2 comments)
    Every published version through 1.4.0 ships a `undici` pin inside the range of multiple published advisories (content-encoding errors, EventSource memory exhaustion, SRI bypass). Closed with a fix available via #11435 — a supply-chain hygiene win worth noting for anyone pinning Pi in CI.

## 4. Key PR Progress

1. **[#11434](https://github.com/earendil-works/pi/pull/11434) [OPEN] fix(ai): use OpenCode protocol-specific endpoints**
   Closes #11132. Maps `openai-completions`/`openai-responses` and Go variants to their documented endpoints instead of the shared base. Small diff, but it's the p0 provider-routing fix of the day.

2. **[#5262](https://github.com/earendil-works/pi/pull/5262) [OPEN] feat(ai): add Anthropic Vertex provider**
   Long-lived PR adding a built-in `anthropic-vertex` provider — a thin adapter that constructs `AnthropicVertex` and reuses the existing Anthropic Messages streaming/request/tool path. Relevant for GCP shops that can't use first-party Anthropic keys.

3. **[#11435](https://github.com/earendil-works/pi/pull/11435) [CLOSED, inprogress] fix(coding-agent): update undici to 8.12.3**
   Bot-authored security bump for the pinned `undici`, citing three advisories. Paired with #11413; expect a patch release to be the delivery vehicle.

4. **[#11427](https://github.com/earendil-works/pi/pull/11427) [CLOSED] fix(mcp): preserve cancellation ownership and close lifecycle**
   Makes MCP close/cancel race-safe: publishes close ownership before callbacks, disposes owned operations, guards late initialization, preserves inbound cancellation controllers, and suppresses post-close stdio writes. Addresses the disposer-failure proposal in #11430.

5. **[#11440](https://github.com/earendil-works/pi/pull/11440) [CLOSED] fix(coding-agent): rank tools first when the search query is their name**
   Fixes BM25 length-normalization stealing rank from an exact-name match (e.g. `thread_read` losing to a shorter `queue_read` description). Closes the `tool_search` exact-name bug #11331; #11437 is a parallel bot-authored variant.

6. **[#11438](https://github.com/earendil-works/pi/pull/11438) [CLOSED] ci: run tests in parallel jobs and drop unused apt packages**
   Splits the 8–11 minute `build-check-test` job, dominated by coding-agent (~140s) and durable (~128s, run twice). Pure developer-experience win — faster feedback on every PR.

7. **[#11422](https://github.com/earendil-works/pi/pull/11422) [OPEN] fix(tui): correct Kitty function key decoding**
   Decodes F1–F4 CSI/SS3 and F5–F12 tilde sequences including modifiers, repeat, and release; stops private-use functional codepoints from being inserted as printable text. Fixes #11345 and continues the terminal-compatibility cleanup.

8. **[#10718](https://github.com/earendil-works/pi/pull/10718) [CLOSED] fix(coding-agent): include system prompt in `--export` HTML**
   `pi --export` omitted the system prompt while interactive `/export` included it. Aligns the two export paths using the prompt recorded on the session's current branch — important for auditability and shareable transcripts.

9. **[#11411](https://github.com/earendil-works/pi/pull/11411) [CLOSED] fix(tui): keep iTerm2 inline images in fullscreen**
   Fullscreen mode disabled the iTerm2 image protocol while the alt screen was active, degrading image tool results to `[Image: ...]` text. Restores inline rendering in `TuiAltScreen`.

10. **[#10528](https://github.com/earendil-works/pi/pull/10528) [OPEN, inprogress] refactor nix part**
    Reworks Nix packaging: cleans `package.nix`, switches to `makeBinaryWrapper`, and skips the version check under Nix. Still open since early October — the main gating item for reproducible/NixOS installs.

## 5. Hot Discussions

**Show and tell**
- **[#11418](https://github.com/earendil-works/pi/discussions/11418) — A thank-you offer: free multi-agent PR reviews for pi** (1 comment, 1 👍)
  A maintainer of a Pi-based GitHub app offers their multi-agent review bots to the repo as an open-source contribution, citing heavy personal use of Pi in agentic workflows. Notable as ecosystem reciprocity: downstream builders offering infrastructure back upstream rather than just filing issues.

## 6. Feature Request Trends

- **Extensibility surface area is the top ask.** Recurring requests target lifecycle and presentation hooks rather than core model behavior: a public tool-presentation API / one-line tool mode (#10259), assistant message transformers (#11450), blocked-status reporting for inline `ctx.ui.custom()` dialogs (#11428), opt-in package namespacing for skills and prompt templates (#8834), and consistent single-fire `session_start` events (#10470).
- **Provider coverage and routing correctness.** Custom OpenAI-compatible endpoints via a guided `/login` flow (#11456), OpenCode protocol-specific endpoints (#11132), Anthropic Vertex (#5262), and CIMD auto-preference over DCR for MCP OAuth (#11426). Users increasingly expect provider config to be discoverable rather than hand-written `models.json`.
- **Cross-platform parity, Windows first.** The Windows megathread (#7547) plus concrete bugs like `find` failing on path patterns (#6817) show demand for a single blessed Windows path with documented tradeoffs.
- **Long-session durability.** Credential refresh (#11221), bounded compaction state (#11297), and watch re-arming after `IN_IGNORED` (#11342) all point at the same theme: sessions that run for hours or days are under-served.
- **Terminal/UI fidelity.** Kitty key decoding (#11345), iTerm2 inline images in fullscreen (#11411), and ANSI paste handling (#11383) — users want the TUI to behave correctly across emulators, not just the common path.

## 7. Developer Pain Points

- **Windows remains a second-class citizen.** Fragmented run paths (WSL, native, Git Bash) mean bugs like `find` returning "No files found" for `src/**/*.ts` (#6817) persist while docs and fixes scatter across approaches. The 90-comment thread is as much a prioritization plea as a bug report.
- **Long-running sessions accumulate silent breakage.** Stale Bedrock credentials, growing compaction file lists, and dropped filesystem watches all fail quietly hours in — the hardest class of bug to reproduce and the most damaging to trust.
- **Insecure or inconsistent defaults.** World-readable session JSONL at `0644` (#11417), an outdated `undici` pin shipped through 1.4.0 (#11413), and transport-dependent `maxMessageBytes` enforcement (#11335) suggest default-safety review is not yet systematic.
- **TUI edge cases can brick the prompt.** ANSI escape corpses and an unterminated bracketed paste that kills Ctrl+C (#11383) are unrecoverable without restarting the process.
- **Provider schema quirks leak into the agent.** Bedrock's union-type parameter ceiling (#11416) and missing `finish_reason` from some OpenAI-compatible providers (#6226) force users to debug upstream validation errors through Pi.
- **Docs and CLI drift.** `pi update --models` being rejected as an unknown option (#11453) while documented in `docs/cli.md` is a small but trust-eroding mismatch; `--export` vs `/export` divergence (#10718) is the same pattern.
- **Triage velocity is uneven.** Many items closed same-day as `untriaged`/`no-action` (#11455, #11442, #11417), which keeps the queue clean but can read as dismissal to first-time reporters — worth watching as community size grows.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-10-27

## 1. Today's Highlights

The managed-agent track continues to dominate: a new session permission-rules and Auto-approval feature landed across both the preview and nightly channels, while the Kubernetes/Hosted runtime tracking issue reports two passing S1 native regression gates. On the bug side, a cluster of P1/P2 reports around **headless `-p` approval semantics** (#15597, #15600) and the **shell tool's in-process `sed` simulation** (#15675) signals that the implicit edit/write deny rules are now the top source of user-visible breakage. Web Shell polish also accelerated, with session naming, rename responsiveness, and a simple composer variant all shipping PRs in the last 24h.

---

## 2. Releases

Three releases landed in the last 24h. Notably, the preview and nightly share an identical changelog entry.

| Release | Notes |
|---|---|
| **v0.26.2-preview.0** | `feat(managed): add session permission rules and Auto approval` ([#15448](https://github.com/QwenLM/qwen-code/pull/15448)); `fix(cli): harden serve token resolution` |
| **v0.26.1-nightly.20261026.34769f9d41** | Same two changes as above ([#15448](https://github.com/QwenLM/qwen-code/pull/15448)) |
| **sdk-typescript-v0.1.23** | Bundles CLI 0.26.1, built from source at the same ref as the SDK. Release notes also reference a bundled CLI 0.26.0 section, suggesting a copy/paste artifact worth a maintainer check. |

**Takeaway:** the "session permission rules + Auto approval" work is the headline feature of this cycle, and it directly interacts with the approval-mode bugs filed today (see #15597 / #15600).

---

## 3. Hot Issues (10 selected)

1. **[#15597](https://github.com/QwenLM/qwen-code/issues/15597) — P1: managed auto-memory can never save in headless `-p`** (7 comments)
   In non-interactive mode with default/Auto approval, the implicit `edit`/`write_file` deny overrides the memory agents' scoped allow, so every memory write is denied and the index stays empty — while the run still pays for the tokens. This is the clearest example of the new permission stack conflicting with itself; high community engagement for a same-day issue.

2. **[#15600](https://github.com/QwenLM/qwen-code/issues/15600) — P2: headless `-p` refuses read-only commands using `2>&1`** (6 comments)
   FD duplication is parsed as a write to a file literally named `&1`, producing `Matching deny rule: "edit"`. Same command without `2>&1` runs fine. A trivially common shell idiom breaking under the safety layer draws immediate attention, and a fix PR ([#15681](https://github.com/QwenLM/qwen-code/pull/15681)) is already open.

3. **[#15675](https://github.com/QwenLM/qwen-code/issues/15675) — P1: `sed` edit simulation diverges from real sed** (3 comments)
   The in-process `sed -i` simulation builds a JS `RegExp` without `s`/`u` flags, so `.` fails to match `\r` and multi-byte emoji, silently producing different files than real `sed`. A correctness issue that undermines trust in the shell tool's optimization path.

4. **[#13395](https://github.com/QwenLM/qwen-code/issues/13395) — Kubernetes tool runtime tracking** (25 comments)
   The longest-running thread in this window. Update for 2026-10-27: two S1 native regressions pass and the acceptance fixture fix ([#15585](https://github.com/QwenLM/qwen-code/pull/15585)) merged; maintainers note the full SDK CI gate remains an independent gap, so the issue stays open. This is the anchor thread for the platform-distribution roadmap.

5. **[#15526](https://github.com/QwenLM/qwen-code/issues/15526) — P2: warn-mode loop reminder fires once, then 59 unguarded identical calls** (4 comments)
   A local OpenAI-compatible model issued 70 byte-identical `read_file` calls; after a single warning, 59 went unchecked. Directly questions the guardrail's effectiveness in `warn` mode — a real cost/token burn for autonomous runs.

6. **[#15660](https://github.com/QwenLM/qwen-code/issues/15660) — P2: subagents start with ~105k prompt context** (3 comments)
   Parent session uses 42k (21k startup), subagent prompt is 1k, yet the engine reports a 105k starting prompt — and it grows per subagent. Token accounting for subagents is opaque; needs triage info.

7. **[#15686](https://github.com/QwenLM/qwen-code/issues/15686) — P2: Active Goal swallows ordinary user input** (3 comments, updated today)
   Enter-steered and pasted messages queue instead of being delivered at the next safe sampling boundary unless prefixed with `/goal`. An interactivity regression that breaks the documented steering shortcut.

8. **[#15682](https://github.com/QwenLM/qwen-code/issues/15682) — P2: user-level agents and squads for Agent Collaboration** (2 comments)
   Requests a global roster of agents/squads available in every trusted workspace. Representative of the multi-agent direction the community is pushing toward.

9. **[#13749](https://github.com/QwenLM/qwen-code/issues/13749) — Claude Code-style Mod runtime** (6 comments)
   Proposes `register(on, options)` / `on(event, matcher?, handler)` / `($, event, next)` compatibility so existing Claude mods run on Qwen's tool and hook system. A persistent request with real ecosystem-portability value.

10. **[#15470](https://github.com/QwenLM/qwen-code/issues/15470) — CI: main-push failures file no issues** (6 comments)
    Between 14:00Z and 17:30Z on 2026-10-24, ten main-push failures produced zero auto-filed issues; the reporter waits 20–100 min on a hosted runner and is then dropped as superseded. Meta-infrastructure issue affecting maintainer response time.

*Also worth watching:* **[#15613](https://github.com/QwenLM/qwen-code/issues/15613)** (closed) — interleaved reasoning reordered in TUI and flattened in Chat Completions replay; **[#1379](https://github.com/QwenLM/qwen-code/issues/1379)** (closed) — long-standing "stop adding `Co-authored-by: Qwen-Coder`" request.

---

## 4. Key PR Progress (10 selected)

1. **[#15681](https://github.com/QwenLM/qwen-code/pull/15681) — fix(core): don't treat fd duplication (`2>&1`) as a file write**
   Stops the shell permission checker from reading `>`, `1>`, `2>`, `<` followed by `&n` or `&-` as a file write. Direct fix for #15600; the highest-impact correctness patch in this window.

2. **[#15458](https://github.com/QwenLM/qwen-code/pull/15458) — feat(core): XDG config directory for new Linux installs**
   Moves global state to `$XDG_CONFIG_HOME/qwen` (`~/.config/qwen`) instead of `~/.qwen` for new installs, following the XDG Base Directory spec. Pairs with the UI-text fix in #15628.

3. **[#15564](https://github.com/QwenLM/qwen-code/pull/15564) — feat(managed-agent): preflight Harness compatibility, leftover workers, live service**
   `qwen-managed-agent-server --check` gains three operator-facing checks, including building a `HostedHarnessClient` after the `/capabilities` probe so negotiation matches runtime behavior. Meaningful upgrade-safety improvement for hosted deployments.

4. **[#15689](https://github.com/QwenLM/qwen-code/pull/15689) — feat(web-shell): name new sessions from the first prompt**
   Derives a title from a single-line 4–40 code-point first prompt (excluding bare slash commands) before attach, then patches the sidebar row optimistically. Removes the "Untitled session" wall.

5. **[#15688](https://github.com/QwenLM/qwen-code/pull/15688) — fix(web-shell): keep session rename responsive**
   The inline rename editor keeps a local draft so typing re-renders only the input instead of every mounted session row; opening the editor no longer expands the whole catalog. A clear perf fix for large session lists.

6. **[#15547](https://github.com/QwenLM/qwen-code/pull/15547) — feat(cli): run only diff-related tests in `review build-test`**
   For workspaces whose `test` script is a plain `vitest run`, hands `vitest related` the diff files plus dependents instead of running the full suite. Big CI-time win for the review workflow.

7. **[#15673](https://github.com/QwenLM/qwen-code/pull/15673) — fix(release): extend standalone runtime download retries**
   Retries each runtime download up to five times with backoff to ~75s, with a regression test pinning the fifth attempt. Release-pipeline reliability.

8. **[#13969](https://github.com/QwenLM/qwen-code/pull/13969) — feat(hooks): `FileChanged` notifications for local sessions**
   Adds `FileChanged` hooks for non-managed sessions, with literal filenames plus absolute files and recursive directories registered via `SessionStart`/`FileChanged` output. The main extensibility addition in flight.

9. **[#15663](https://github.com/QwenLM/qwen-code/pull/15663) — feat(web-shell): configurable simple composer**
   Opt-in `composerVariant="simple"` presentation sharing the existing editor, attachments, and session actions across main chat, split panes, and side tasks; existing hosts unaffected by default.

10. **[#15670](https://github.com/QwenLM/qwen-code/pull/15670) — fix(serve): Hosted local state and foreground Shell validation**
    Hosted text turns, workflows, hooks and context compression use their remotely owned Session identity rather than local transcripts; Hosted startup skips branch-worktree recovery and the ordinary Shell path. Core to the Hosted/managed split.

*Also merged/closed:* **[#15668](https://github.com/QwenLM/qwen-code/pull/15668)** (closed) — `/autofix-pr` watch visibility and cancellation in Web Shell, the final part of a four-PR series; **[#15555](https://github.com/QwenLM/qwen-code/pull/15555)** — keeps the sdk-java cache write-back running after a failed suite.

---

## 5. Hot Discussions

*No Discussion data was provided in this dataset — section omitted.*

---

## 6. Feature Request Trends

- **Web Shell as a first-class surface.** The largest single cluster: inline rename pre-select ([#15629](https://github.com/QwenLM/qwen-code/issues/15629)), pinned turn prompt while scrolling ([#15683](https://github.com/QwenLM/qwen-code/issues/15683)), keyboard shortcuts for Session Overview / Split View ([#13175](https://github.com/QwenLM/qwen-code/issues/13175)), and composer variants ([#15663](https://github.com/QwenLM/qwen-code/pull/15663)). Requests are increasingly about *ergonomics at scale* rather than missing features.
- **Multi-agent / team-level configuration.** [#15682](https://github.com/QwenLM/qwen-code/issues/15682) (user-level agents and squads across workspaces) sits alongside the Agent Collaboration roadmap label and subagent context concerns (#15660).
- **Chat-platform and enterprise integrations.** Microsoft Teams ([#15618](https://github.com/QwenLM/qwen-code/issues/15618)) and QQ bot proactive-loop semantics ([#15631](https://github.com/QwenLM/qwen-code/issues/15631)) both ask for well-defined delivery semantics, not just adapters.
- **Extensibility parity with Claude Code.** The Mod runtime proposal ([#13749](https://github.com/QwenLM/qwen-code/issues/13749)) and `FileChanged` hooks ([#13969](https://github.com/QwenLM/qwen-code/pull/13969)) point at a consistent authoring contract across tools, hooks and events.
- **Hosted / external runtime topologies.** [#15512](https://github.com/QwenLM/qwen-code/issues/15512) (external Hosted Workspace profile, phase 2) and [#13395](https://github.com/QwenLM/qwen-code/issues/13395) (Kubernetes tool runtime) represent the platform-distribution roadmap's next step: vendor-neutral worker provisioning.
- **Platform-convention compliance.** XDG directories ([#15458](https://github.com/QwenLM/qwen-code/pull/15458)) and honest path rendering in settings/UI ([#15628](https://github.com/QwenLM/qwen-code/issues/15628)).

---

## 7. Developer Pain Points

1. **Permission/approval semantics in non-interactive mode are the #1 friction source.** Both P1 [#15597](https://github.com/QwenLM/qwen-code/issues/15597) and P2 [#15600](https://github.com/QwenLM/qwen-code/issues/15600) trace to the *implicit* `edit`/`write_file` deny rule overriding scoped allows and misclassifying shell syntax. The new session permission rules and Auto approval (#15448) raise the stakes: scoped allows need to actually win, and the shell parser needs to understand redirection grammar rather than pattern-matching `>`.
2. **Loop guardrails are perceived as ineffective.** [#15526](https://github.com/QwenLM/qwen-code/issues/15526) shows `warn` mode sending one reminder and then allowing 59 identical calls. Users running autonomous tasks on local models want enforcement, not a single nudge.
3. **Token accounting for subagents is opaque.** [#15660](https://github.com/QwenLM/qwen-code/issues/15660) — a 1k subagent prompt reported as a 105k prompt — erodes trust in cost estimates and context budgeting.
4. **CI noise is drowning signal.** Bot-filed CI issues dominate the top of the issue list by comment count ([#14724](https://github.com/QwenLM/qwen-code/issues/14724) at 64 comments, [#15173](https://github.com/QwenLM/qwen-code/issues/15173) at 17, [#13009](https://github.com/QwenLM/qwen-code/issues/13009), [#14796](https://github.com/QwenLM/qwen-code/issues/14796)), several with no test identified and `autofix/skip`. Compounding this, [#15470](https://github.com/QwenLM/qwen-code/issues/15470) documents ten failures filing zero issues, and [#15677](https://github.com/QwenLM/qwen-code/issues/15677) reports the new darwin-x64 TUI-parity leg failing 5 of 6 runs on a 30s ready-prompt budget. Net effect: flaky infrastructure is consuming maintainer bandwidth that would otherwise go to the P1s above.
5. **Simulation layers drifting from real tools.** [#15675](https://github.com/QwenLM/qwen-code/issues/15675) is the clearest case — an in-process `sed` fast path that silently produces different output than `sed`. Whenever Qwen Code simulates a tool instead of invoking it, behavioral parity needs a regression fixture.
6. **Interactivity and input routing regressions.** [#15686](https://github.com/QwenLM/qwen-code/issues/15686) (Active Goal holding Enter-steered input) and [#15613](https://github.com/QwenLM/qwen-code/issues/15613) (interleaved reasoning reordered in TUI) both break expectations set by the documented UX — worth a targeted interactive-mode test pass.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*