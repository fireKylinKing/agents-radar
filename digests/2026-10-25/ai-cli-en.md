# AI CLI Tools Community Digest 2026-10-25

> Generated: 2026-10-25 00:29 UTC | Tools covered: 7

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

# Cross-Tool Comparison: AI CLI Developer Tools — 2026-10-25

## 1. Ecosystem Overview

The 2026-10-25 digests show a market shifting from capability demos to operational trust. Claude Code is in a visible maintenance trough with high-consensus plan-tier demand and unresolved sandbox data-loss reports, while Codex is concentrated on Windows/app-server stability and MCP resource isolation. Gemini CLI, OpenCode, Pi, Copilot CLI, and Qwen Code are all iterating on agent orchestration, resource lifecycle, quota/auth correctness, and platform parity. Across communities, the central question is no longer “can the agent code?” but “can it finish reliably, recover safely, and report truthfully?” Enterprise buying signals—Claude Team 20x, Copilot billing incidents, Qwen CI/runtime automation—are increasingly prominent.

---

## 2. Activity Comparison

Counts below are **digest-reported within the 24h window**, not full repository totals. “Notable/listed” means items explicitly profiled in the digest. `n/d` = no count provided in the digest. `N/A` would be used only for disabled community channels; none of these seven tools had Issues/PRs disabled upstream in this dataset.

| Tool | Issues count | PR count | Discussions count | Release status |
|---|---:|---:|---:|---|
| **Claude Code** | 50 updated; 10 profiled | 2 updated; 0 merged | n/d | No release |
| **OpenAI Codex** | 10 notable; total n/r | 0 updated | 10 profiled (8 show-and-tell, 2 Q&A) | No release |
| **Gemini CLI** | 14 notable (10 hot + 4 watch) | 14 listed (10 key + 4 other) | n/d | 1 nightly (`v0.67.0-nightly.20261024`) |
| **GitHub Copilot CLI** | 20 updated | 0 updated | n/d | 2 patches (`v1.0.104-2`, `v1.0.104-3`) |
| **OpenCode** | 13 notable; ≥12 quota reports cited (overlap) | 16 listed (10 key + 6 other) | n/d | No release |
| **Pi** | 12 notable | 10 listed | 2 profiled | No release |
| **Qwen Code** | 13 notable | 15 listed (10 key + 5 other) | n/d | 2 releases (`v0.26.1`, `desktop-v0.26.1`) |

**Reading the table:** Claude has the broadest issue window and strongest single-issue engagement, but zero merged PR activity. Gemini, Qwen, OpenCode, and Pi show denser code-review throughput. Codex and Copilot’s “0 PR updated” figures are snapshot limitations or low PR visibility, not proof that no work is happening.

---

## 3. Shared Feature Directions

Seven cross-tool requirements recur across multiple communities:

1. **Truthful agent state and termination semantics**
   - **Gemini:** false `GOAL` success on `MAX_TURNS` and browser failures (#22323, #21983, #29779).
   - **Codex:** evidence-backed completion reports (#36718).
   - **Copilot:** silent subagent tool loss and broken context rollover (#5243, #5205).
   - **OpenCode:** parent agents not receiving later subagent turns (#58825).
   - **Qwen:** fallback chains skipped by misclassified 429s (#13895).
   - **Need:** explicit success/failure reasons, evidence mapping, parent-child result propagation, and auditable completion claims.

2. **Background/parallel task lifecycle and resource isolation**
   - **Copilot:** background shell completion corrupting turns (#4946), inotify exhaustion (#5242), context rollover failure (#5205).
   - **Codex:** per-thread MCP process explosion (#53285), `git diff` fan-out (#43019), ephemeral threads leaking MCP stacks (#39783).
   - **OpenCode:** bounded shutdown (#58817), background subagent reporting (#58825).
   - **Pi:** full-core TUI pinning during streaming (#6665, #7730).
   - **Need:** bounded process concurrency, cleanup/reclamation, timeouts, backpressure, and graceful shutdown.

3. **Quota, billing, and auth consistency**
   - **Claude:** Team plan needs Max-20x-equivalent tier (#47509).
   - **Codex:** usage-limit resets not recognized across clients (#51043).
   - **Copilot:** Assisted Permissions PRU drain incident (#4802).
   - **OpenCode:** free-tier quota never resets; 86,400s loop (#58830, #58831, #58827).
   - **Qwen:** reasoning tokens double-counted in cost display (#15444).
   - **Pi:** usage-limit interruption/resume workflow (#11306).
   - **Need:** predictable entitlement state, cross-client syncing, accurate token accounting, and transparent reset semantics.

4. **Permission/sandbox trust and consent**
   - **Claude:** destructive sandbox filesystem operations (#93287, #93408) and inconsistent auto-mode classifier (#101327).
   - **Gemini:** sandboxing over restriction (#19873), destructive-command guardrails (#22672).
   - **OpenCode:** permission “ask” silently allows edit/write (#58527).
   - **Copilot:** ACP OAuth refresh vs browser redirect (#5229).
   - **Codex:** opaque sandbox/permission states.
   - **Need:** deterministic approvals, legible sandbox boundaries, re-consent for tool expansion, safe defaults, and dry-run/recovery paths.

5. **Provider/model routing and interoperability**
   - **Pi:** large provider-expansion PR chain (#11368–#11372), Gemini thought signatures (#10157), Bedrock image payloads (#8643).
   - **OpenCode:** v1→v2 config mismatch (#58533), catalog poisoning by expired models (#57931).
   - **Qwen:** strict OpenAI-compatible tool schemas (#12222), 429 fallback classification (#13895).
   - **Gemini:** too many tools in scope causing 400s (#24246).
   - **Codex:** model-routing opacity and silent model switches.
   - **Need:** declarative model catalogs, capability-aware routing, graceful fallbacks, and adapter-level conformance tests.

6. **Context, memory continuity, and compaction**
   - **OpenCode:** manual compaction can permanently brick a session (#58724).
   - **Qwen:** transcript snapshot index cap makes sessions unopenable (#13113).
   - **Codex:** AGENTS.md propagation checks and decision maps across fresh chats.
   - **Pi:** extension ordering loses Anthropic `system` field (#11157), embedded-session memory retention (#10642).
   - **Gemini:** persistent file-based task state (#18836).
   - **Need:** durable session stores, recoverable compaction, context handoff, and memory isolation across projects.

7. **Terminal/Desktop/headless parity**
   - **Claude:** Desktop prompt suggestions, plugin render hooks, Remote Control persistence.
   - **Codex:** Windows VS Code startup, iPad remote sessions, Windows MCP process counts.
   - **Gemini:** Wayland browser subagent failures, OSC 7501 terminal status.
   - **Copilot:** Windows proxy/STT, ACP parity, local web UI request.
   - **OpenCode:** Windows `%temp%` relocation, cross-volume binary retention.
   - **Pi:** Ctrl+Shift+C prompt loss, Windows CSI leakage, Alacritty IME.
   - **Qwen:** Web Shell i18n fallback blocking non-English browsers.
   - **Need:** cross-platform CI matrices, headless parity, accessible UI fallbacks, and terminal-native status reporting.

---

## 4. Differentiation Analysis

| Tool | Feature focus | Target users | Technical approach |
|---|---|---|---|
| **Claude Code** | Team plan tiering, Desktop/TUI polish, sandbox safety, plugin rendering, Remote Control | Teams, enterprise procurement, power users | Sandboxed Bash/Desktop, permission classifier, plugin UI hooks, Remote Control, Max-tier usage model |
| **OpenAI Codex** | Windows desktop/app-server stability, IDE extension, MCP lifecycle, remote/mobile, AGENTS.md | Windows/IDE developers, ChatGPT-authenticated users, enterprise app users | App-server + VS Code extension, MCP, thread/history APIs, evidence-backed completion direction |
| **Gemini CLI** | Subagent reliability, ACP/editor integration, terminal-native status, AST-aware context, sandboxing | Terminal/editor developers in Google ecosystem | CLI + ACP, subagent orchestration, OSC 7501, AST/tool-scoping experiments, zero-dependency sandbox proposals |
| **GitHub Copilot CLI** | Autopilot async correctness, SDK/ACP/headless, plugin skills, enterprise proxy/OAuth | GitHub enterprise, CI/container users, Copilot subscribers | Autopilot, MCP, ACP, plugin namespacing, managed model `fusion`, patch-release cadence |
| **OpenCode** | Free-tier/Desktop, v2 migration, provider interop, session integrity | Individual developers, free-tier users, Desktop/CLI users | Client/server session transport, model catalog ETag, subagents, themes, desktop/CLI convergence |
| **Pi** | Provider breadth, extension API, TUI performance, embedded SDK, long sessions | Power users, extension authors, embedded/multi-session hosts | Declarative provider definitions, models.dev catalog, TUI performance work, extension lifecycle APIs |
| **Qwen Code** | CI/PR automation, runtime broker/Kubernetes, Desktop/daemon, cost accounting | Qwen ecosystem, self-hosted/enterprise CI teams | CLI + daemon/Desktop, runtime broker, managed agent, strict OpenAI-compatible provider handling |

The strategic split is between **platform-owned enterprise stacks**—Claude Code, Codex, Copilot CLI, and Gemini CLI—and **ecosystem/extensibility-led tools**—OpenCode, Pi, and Qwen Code. The former compete on integration depth, admin controls, and safety; the latter compete on provider coverage, customization, and rapid iteration.

---

## 5. Community Momentum & Maturity

- **Highest raw engagement:** Claude Code and Pi. Claude’s #47509 reached 187 👍 / 67 comments, and its sandbox data-loss cluster is safety-critical. Pi’s #4945 has 88 comments / 35 👍, signaling deep long-session reliability pain.
- **Strong issue-intake activity:** Copilot CLI (20 issues updated), Claude (50-issue window), OpenCode (quota crisis with ≥12 reports), and Codex (Windows/app-server cluster). These communities are actively filing, but not all are seeing proportional merge throughput.
- **Fastest visible iteration:** Gemini CLI (nightly + 14 listed PRs), Qwen Code (2 releases + 15 listed PRs), OpenCode (16 listed PRs), and Pi (stacked provider-expansion PR chain). These tools show maintainers actively landing correctness, performance, and provider work.
- **Maturity vs. velocity:** Claude, Codex, Copilot, and Gemini have broader surfaces and enterprise adoption, but carry platform debt—sandbox trust, Windows stability, headless parity, stale triage. OpenCode, Pi, and Qwen are younger and more visibly iterating, but their process maturity lags: OpenCode’s entitlement incident and Pi’s contribution-gate closures show operational gaps.
- **Bottom line:** Claude has the most consequential community signal but a stalled integration posture. Gemini, Qwen, OpenCode, and Pi are the most rapidly iterating. Codex and Copilot remain mature but constrained by Windows/headless reliability and low visible PR throughput in this snapshot.

---

## 6. Trend Signals

1. **Reliability is the new differentiation.** False success reports, indefinite hangs, broken rollover, and background-task corruption appear across Gemini, Copilot, OpenCode, and Pi. Buyers should evaluate termination semantics, recovery paths, and evidence reporting—not just model quality.
2. **Resource isolation is moving up-stack.** MCP per-thread processes, `git diff` fan-out, inotify exhaustion, and TUI CPU/RAM growth show that process budgets, backpressure, and cleanup are now core agent-runtime requirements.
3. **Billing/auth/quota transparency is a trust layer.** Quota resets, PRU drain, token double-counting, and plan-tier ceilings are adoption blockers. Enterprises should demand auditable usage attribution and cross-client state consistency.
4. **Consent and sandbox legibility matter more than sandbox claims.** Destructive filesystem operations, silent edit/write permissions, and nondeterministic classifiers erode trust. Explicit approval models, re-consent, dry-run, and filesystem boundaries are becoming table stakes.
5. **Provider-neutral adapters and fallbacks are strategic.** Pi’s provider expansion, OpenCode’s v1→v2 migration pain, Qwen’s OpenAI-compatible strictness, and Gemini’s tool-scoping limits all point to declarative catalogs, capability routing, and conformance testing.
6. **Headless/CI/container parity will be a buying criterion.** ACP, OAuth refresh, proxy awareness, inotify limits, Windows behavior, and non-English UI fallbacks are no longer edge cases—they determine whether tools work in enterprise CI and remote environments.
7. **Agent observability and eval tooling is emerging.** Evidence-backed completion, AGENTS.md verification, subagent trajectory visibility, and pytest-based behavior checks are early signals of a new tooling layer around agent trust.
8. **Community operations are a leading indicator.** Stale-bot closures, CI capacity bottlenecks, contribution gates, and release automation quality correlate directly with how quickly substantive issues get resolved. For decision-makers, PR throughput and triage health are practical support-quality signals.

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — Community Highlights Report
**Source:** github.com/anthropics/skills · **Data as of:** 2026-10-25

> **Data caveat:** Every PR in the supplied dataset reports `Comments: undefined` and `👍: 0`, so ranking below uses *attention proxies* — number of PRs touching the same skill, linked Issues with high comment counts, and sustained update activity — rather than raw comment counts. All listed PRs are **OPEN**; no merged PRs appear in this snapshot.

---

## 1. Top Skills Ranking

| # | Skill | Representative PR(s) | Status | Attention signal |
|---|-------|----------------------|--------|------------------|
| 1 | **skill-creator** (meta-skill) | [#1298](https://github.com/anthropics/skills/pull/1298), [#1681](https://github.com/anthropics/skills/pull/1681), [#1997](https://github.com/anthropics/skills/pull/1997) | Open | 3 concurrent PRs + Issues #202 (8 comments), #1394 (4 comments) |
| 2 | **mcp-builder** | [#1742](https://github.com/anthropics/skills/pull/1742) | Open | Fixes #1668; chained to Issue #1390 (0/N eval scores) |
| 3 | **webapp-testing** | [#1980](https://github.com/anthropics/skills/pull/1980), [#1976](https://github.com/anthropics/skills/pull/1976) | Open | 2 PRs, one security-grade (CWE-78) |
| 4 | **docx** | [#1792](https://github.com/anthropics/skills/pull/1792), [#1734](https://github.com/anthropics/skills/pull/1734) | Open | 2 PRs + Issue #2018 (invalid XML output) |
| 5 | **claude-api** | [#1730](https://github.com/anthropics/skills/pull/1730) | Open | Linked to Issue #1487 (~156k token eager injection) |
| 6 | **pyxel** (retro game dev) | [#525](https://github.com/anthropics/skills/pull/525) | Open (draft-stage) | Longest-running active feature PR — Mar 5 → Oct 23 |
| 7 | **document-typography** | [#514](https://github.com/anthropics/skills/pull/514) | Open | Widow/orphan control for generated docs |
| 8 | **skill-quality-analyzer / skill-security-analyzer** | [#83](https://github.com/anthropics/skills/pull/83) | Open | Oldest open PR in set (2025-11-06) |

**Detail notes**

- **skill-creator** is the ecosystem's pressure point: three open fixes address trigger-eval false misses and Windows subprocess handling ([#1298](https://github.com/anthropics/skills/pull/1298)), standalone `package_skill.py` execution ([#1681](https://github.com/anthropics/skills/pull/1681), most recently updated 2026-10-24), and hand-rolled YAML frontmatter parsing that corrupts descriptions ([#1997](https://github.com/anthropics/skills/pull/1997)). Issue #202 argues the skill reads like developer documentation rather than an operational instruction set.
- **mcp-builder** [#1742](https://github.com/anthropics/skills/pull/1742) adapts to `mcp>=2.0.0`, where `streamablehttp_client` was renamed and custom headers now route through `create_mcp_http_client`. Paired with Issue #1390 (evaluation harness fabricates tool errors), this skill's *testing* path is the community's main complaint.
- **webapp-testing** carries the clearest security framing: [#1980](https://github.com/anthropics/skills/pull/1980) removes `shell=True` from `with_server.py` to close a command-injection vector; [#1976](https://github.com/anthropics/skills/pull/1976) corrects `textarea`/`select` misreporting in element discovery.
- **pyxel** [#525](https://github.com/anthropics/skills/pull/525) and **document-typography** [#514](https://github.com/anthropics/skills/pull/514) are the strongest *net-new* skill proposals: headless frame inspection for retro games, and typographic QC (orphan wrap, widow paragraphs, numbering alignment) for generated documents.

---

## 2. Community Demand Trends

Distilled from Issues, demand clusters into five directions — notably, most are about *trust and reliability* rather than new capabilities:

1. **Namespace integrity & supply-chain trust** — Issue [#492](https://github.com/anthropics/skills/issues/492) (46 comments, the most-discussed issue in the dataset) warns that community skills shipping under the `anthropic/` namespace enable impersonation and permission escalation. Reinforced by the eval-viewer XSS report ([#1394](https://github.com/anthropics/skills/issues/1394)).
2. **Team/org-level skill distribution** — [#228](https://github.com/anthropics/skills/issues/228) (17 comments) asks for org-wide skill libraries and share links instead of manual `.skill` file passing; [#189](https://github.com/anthropics/skills/issues/189) reports duplicate content across `document-skills` and `example-skills` plugins.
3. **Reliable skill triggering & evaluation** — [#556](https://github.com/anthropics/skills/issues/556) (13 comments): `run_eval.py` yields a 0% trigger rate across all queries, meaning skill authors cannot validate their work.
4. **Context/token economy** — [#1487](https://github.com/anthropics/skills/issues/1487): `claude-api` eagerly injects ~156k tokens in a single tool call; [#202](https://github.com/anthropics/skills/issues/202): `skill-creator` prose is too verbose for token efficiency.
5. **Meta-skills for quality & governance** — proposals for a three-gate reasoning pipeline ([#1385](https://github.com/anthropics/skills/issues/1385)), agent-governance safety patterns ([#412](https://github.com/anthropics/skills/issues/412)), and symbolic compact-memory notation ([#1329](https://github.com/anthropics/skills/issues/1329)).

---

## 3. High-Potential Pending Skills

PRs with recent activity and narrow, reviewable diffs — candidate near-term landings:

| PR | Skill | Why it's likely to land |
|----|-------|-------------------------|
| [#1681](https://github.com/anthropics/skills/pull/1681) | skill-creator | Updated 2026-10-24 (freshest in set); fixes a reproducible `ModuleNotFoundError` |
| [#1980](https://github.com/anthropics/skills/pull/1980) | webapp-testing | Security fix (CWE-78) with a self-contained change surface |
| [#1997](https://github.com/anthropics/skills/pull/1997) | skill-creator | Correct YAML scalar parsing; replaces a manual parser |
| [#1977](https://github.com/anthropics/skills/pull/1977) | algorithmic-art | One-function fix (`wrapAround()` modulo), resolves #1897 |
| [#1976](https://github.com/anthropics/skills/pull/1976) | webapp-testing | Tag-name reporting fix, resolves #1891 |
| [#1792](https://github.com/anthropics/skills/pull/1792) | docx | LibreOffice timeout now reported as error + output verification |
| [#1730](https://github.com/anthropics/skills/pull/1730) | claude-api | Dead-URL replacement, HTTP-200 verified |

Longer-horizon but high-value: [#1742](https://github.com/anthropics/skills/pull/1742) (mcp-builder, MCP v2 compat) and [#538](https://github.com/anthropics/skills/pull/538) (pdf, case-sensitive reference fixes).

---

## 4. Skills Ecosystem Insight

**The community's most concentrated demand is not for new Skills but for the trust, correctness, and measurability of the skill toolchain itself** — namespace integrity, reliable triggering/evaluation, and token-efficient `skill-creator`/`mcp-builder` plumbing dominate discussion, while net-new skills (pyxel, ODT, document-typography, md2video-audio) remain a secondary, slower-moving stream.

---

# Claude Code Community Digest — 2026-10-25

## 1. Today's Highlights

No new releases shipped in the last 24 hours, and the PR queue is effectively dormant (2 closed PRs, 0 merges to highlight). Community attention remains concentrated on **plan tiering** — issue #47509 crossed 187 👍 and 67 comments demanding a Max-20x-equivalent Team seat — and on a pair of **data-loss incident reports** (#93287, #93408) where sandboxed Bash/Desktop sessions allegedly deleted macOS `Applications` and home-directory contents. Meanwhile the Desktop Code tab continues to accumulate small but high-friction regressions around prompt suggestions, plugin render hooks, and Remote Control session persistence.

## 2. Releases

*Omitted — no releases published in the last 24 hours.*

## 3. Hot Issues

1. **[#47509](https://github.com/anthropics/claude-code/issues/47509) — Team plan needs a Max 20x equivalent tier** (OPEN, 67 comments, 187 👍)
   The single most-engaged item in the window. Teams standardize on Claude Code for agentic workflows but hit the 6.25x Premium ceiling while individuals run Max 20x. Signals real enterprise procurement friction — the 👍 count is ~3x anything else here.

2. **[#93287](https://github.com/anthropics/claude-code/issues/93287) — Claude Code deleted entire macOS Applications directory** (OPEN, high-priority, data-loss, sandbox)
   Alleges every installed app was removed. Tone is raw, but the metadata (high-priority + data-loss + area:sandbox) is the alarming part. This is the flagship of a cluster of sandbox-escalation reports.

3. **[#93408](https://github.com/anthropics/claude-code/issues/93408) — Desktop deleted macOS home folder contents including keychain** (OPEN, high-priority, data-loss)
   Companion report to #93287, implicating Bash + Desktop sandbox boundaries. Keychain loss elevates this from annoyance to security incident; both remain open after ~6 weeks.

4. **[#103335](https://github.com/anthropics/claude-code/issues/103335) — Desktop prompt suggestions (Tab to accept) stopped appearing** (OPEN, has repro, 11 comments)
   Regressed around the 2026-10-13 update and survives restarts and fresh sessions. A quiet productivity loss for heavy Desktop users; reproducible, which usually shortens time-to-fix.

5. **[#102988](https://github.com/anthropics/claude-code/issues/102988) — Style sidebar session rows by state** (OPEN, enhancement, 7 comments)
   Requests full-row color encoding (running / needs-input / unread / cache-expired) instead of a single status dot. Well-specified; reflects how crowded parallel-agent sessions have become.

6. **[#101327](https://github.com/anthropics/claude-code/issues/101327) — Auto-mode permission classifier blocks routine pre-approved writes** (OPEN, area:permissions)
   Classifier categories shift between runs and block even self-service permission edits. The inconsistency — not the blocking itself — is the complaint; it makes auto-mode untrustworthy for scripted workflows.

7. **[#102480](https://github.com/anthropics/claude-code/issues/102480) — Next-step box hides plugin `AbovePrompt` render** (OPEN, has repro, Windows)
   A Desktop UI layering bug that breaks the documented plugin extension point. Small blast radius today, but it undermines confidence in the modding API's rendering contract.

8. **[#83528](https://github.com/anthropics/claude-code/issues/83528) — No way to clear the prompt buffer without a side effect** (OPEN, area:tui)
   Careful audit showing every existing keybinding either partially clears or arms `/clear` destructively. Classic TUI papercut amplified by long multi-line agentic prompts.

9. **[#103959](https://github.com/anthropics/claude-code/issues/103959) — Plugin pane can't be widened past 45% of terminal** (OPEN, Linux, 3 👍)
   Regression on the docked-plugin layout, first reported 2026-10-21. Narrow but zero-comment-resistance — users agree it's a regression, not a design choice.

10. **[#104700](https://github.com/anthropics/claude-code/issues/104700) — Remote Control sessions go offline until touched locally** (OPEN, duplicate, Windows)
    Sessions drop off Remote Control after sleep/resume even with the machine awake. Marked duplicate, which suggests it's a known family of state-persistence bugs rather than an isolated case.

## 4. Key PR Progress

Only **2 pull requests** were updated in the window, both **CLOSED** and neither merged — treat this as a stalled-integration signal rather than feature progress.

1. **[#62597](https://github.com/anthropics/claude-code/pull/62597) — fix: resolve 10 bugs across scripts and workflows** (CLOSED)
   Hardens repo automation: replaces a hardcoded `REPO` with a `GITHUB_REPOSITORY` env fallback so scripts work in forks, adds safe fallbacks for `github.event.repository.name` on `schedule`/`workflow_dispatch` where it can be null, and improves error handling in `edit-issue-labels.sh`. Open since May — its closure without merge is notable given how much of the triage load (see the stale-close pattern below) depends on this tooling.

2. **[#16132](https://github.com/anthropics/claude-code/pull/16132) — fix: `/install-github-app` support for dev container** (CLOSED)
   Fixes a real onboarding blocker: after authenticating `gh` inside a dev container, `/install-github-app` still reported `gh` as unauthenticated. Resolved by adding the GitHub CLI repository to fetch the latest `gh`. Open since January.

> **Analyst note:** With zero open PRs and no releases, the community's issue backlog is growing faster than it's being drained — a pattern that shows up directly in Section 7.

## 5. Hot Discussions

*Omitted — no Discussions data was provided in this dataset.*

## 6. Feature Request Trends

- **Power-user plan tiering (B2B).** #47509 dominates. The ask is structural: per-seat multipliers don't map to how agentic coding actually burns tokens, and teams want a 20x-class seat with admin/billing controls.
- **Desktop Code tab as a first-class multi-agent console.** A tight cluster of requests (#102988, #102031, #101312) targets sidebar information density: state-based row styling, human-readable project-folder group names, and exposing the text-selection menu as a `ui.render` component. Users are running many concurrent sessions and the UI hasn't caught up.
- **Plugin/mod API surface expansion.** #101312 and #102480 both concern the `ui.render` extension points (`AbovePrompt`, selection menu). Modders are pushing the API into layout territory Anthropic hasn't formalized.
- **Terminal compatibility & capability negotiation.** #99566 asks the kitty-graphics probe to trust the actual graphics query response rather than matching `XTVERSION` strings against `kitty`/`ghostty`. SwiftTerm-based terminals (Moo) are being excluded despite supporting Unicode placeholders.
- **TUI ergonomics.** #83528 and #103959 are both small, precise requests for predictable keybindings and resizable panes — the unglamorous work that determines whether the CLI feels professional.
- **Opt-out from behavior experiments.** #89735 (closed stale) requested a documented switch to disable silent cohort experiments; #89860 shows the downstream symptom, with users surprised by unsolicited model switches. This is a transparency ask, not a feature ask.
- **Remote/headless session reliability.** #104700 and #97368 point at a shared theme: session state and notifications don't survive sleep/resume, undermining phone-side or unattended operation.
- **Sandbox/security consent surfaces.** #89866 (closed stale) flagged that remote MCP servers can add outward-facing tools to an already-trusted connector with no re-consent — a security-model gap, not a bug.

## 7. Developer Pain Points

1. **Destructive filesystem operations in sandboxed sessions.** #93287 and #93408 are the acute cases, but the underlying complaint is broader: the trust boundary between "sandbox" and "real home directory" is not legible to users, and post-incident recovery is undocumented. This is the highest-severity theme in the dataset.
2. **Permission classifier unpredictability.** #101327 describes the same operation being allowed and blocked across runs under shifting categories. Non-determinism in the permission layer forces users back to manual approval, which defeats auto-mode's purpose.
3. **Multi-agent workflows abort on a single denial.** #88216 (closed stale) captured it well: pressing Esc or Deny on one tool call kills the entire parallel run. For anyone using Workflow/parallel subagents, this makes permission prompts disproportionately expensive.
4. **Data-integrity regressions in the Desktop session store.** #89497 (Windows MSIX repair wiped 97 of 100 conversations), #89873 ("No messages yet" despite intact `.jsonl`), and #89871 (`lastActivityAt` stamped for a turn that never happened) describe a store that disagrees with its own on-disk truth.
5. **Silent behavior changes via cohort experiments.** Unannounced shifts in tool selection, file-editing method (e.g., `sed`/heredocs instead of Read/Edit/Write), and system prompts break user expectations and any prompts/evals pinned to prior behavior.
6. **Stale-bot closing of substantive reports.** A large share of today's 50-issue window is `[CLOSED] [stale]` — including security (#89866), data-loss (#89497), and agent-correctness (#82821) issues. The repo's own automation PRs (#62597) sit unmerged, so triage tooling appears to be decaying alongside the backlog.
7. **Install/update path collisions.** #84224 (closed, reproduced) documents the auto-updater installing into the PATH-resolved npm prefix and clobbering a different install when multiple Node prefixes exist — a common nvm + Homebrew setup. Version-independent, so it will recur.
8. **Model-routing opacity.** #89876 (advisor picking a different model than documented) and #89860 ("another stupid switch to opus 4.8 that wasn't warranted") show users don't feel in control of which model executes their work, and dislike discovering it after the fact.
9. **Notification and connectivity flakiness on Desktop.** #97368 (intermittent turn-complete notifications), #89719 and #83745 (Claude-in-Chrome permanently "not connected" after orphaned `claude.exe` processes accumulate and exhaust named pipes). The latter has a known workaround — kill stale processes — that shouldn't be necessary.

---

**Bottom line for 2026-10-25:** the repo is in a maintenance trough — no releases, no open PRs, and a backlog being trimmed by stale-closure rather than resolution. The two signals worth watching are the **Team 20x tier request** (commercial, high-consensus) and the **sandbox data-loss reports** (safety-critical, currently unresolved).

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-10-25

## 1. Today’s Highlights
Activity was dominated by Windows desktop/app-server stability and resource-isolation issues: VS Code extension startup failures, MCP process proliferation, `git diff` fan-out, and long-chat memory growth. The most-discussed thread remains [#50329](https://github.com/openai/codex/issues/50329) with 33 comments, while [#51043](https://github.com/openai/codex/issues/51043) drew the strongest positive reaction with 14 👍. No new releases or PR updates landed in the last 24h; discussions show strong interest in AGENTS.md verification, background/parallel Codex runs, and cross-agent context handoff.

## 2. Hot Issues

1. **[#50329](https://github.com/openai/codex/issues/50329) — Windows App `read_thread` returns only `chatgpt-content-reference` for completed ChatGPT replies**  
   33 comments, 6 👍. A high-impact data-retrieval regression: completed ChatGPT replies are not returning full content to the Windows app. Community is actively triangulating package/version evidence.

2. **[#41695](https://github.com/openai/codex/issues/41695) — iPad App freezes constantly when accessing Remote Codex Sessions**  
   22 comments, 2 👍. Remote-session reliability on iPad remains a recurring pain point, especially for Pro 20x users relying on mobile access.

3. **[#36718](https://github.com/openai/codex/issues/36718) — Add evidence-backed completion reports for agent tasks**  
   22 comments. Proposes a structured requirement-to-evidence mapping so “task complete” claims are auditable. This aligns with growing demand for agent trust and verification.

4. **[#43019](https://github.com/openai/codex/issues/43019) — Windows unbounded `git diff --no-index` fan-out exhausts commit and crashes Windows**  
   18 comments. With ~4,771 untracked files, Codex Desktop can spawn one `git diff` per file and exhaust system commit. A serious workspace-scale stability issue.

5. **[#56691](https://github.com/openai/codex/issues/56691) — Codex 26.1021.22027 fails to start UI in VS Code 1.143.1 on Windows**  
   16 comments, 5 👍. A fresh IDE-extension regression with clear reproduction details. High reaction suggests broad Windows VS Code impact.

6. **[#49767](https://github.com/openai/codex/issues/49767) — Windows official Library transfer helper crashes on `os.setxattr`**  
   16 comments. Blocks metadata-preserving materialization for skills/library workflows. Windows compatibility gap in a core Codex workflow.

7. **[#39783](https://github.com/openai/codex/issues/39783) — Desktop ephemeral thread summaries leak full MCP stacks via `thread/unsubscribe`**  
   13 comments, 3 👍. Ephemeral summary threads inherit and start the full MCP configuration even though summarization does not use MCP tools. Resource and exposure concern.

8. **[#53285](https://github.com/openai/codex/issues/53285) — Windows: one stdio MCP process set per thread; 353 processes / ~9.6 GiB**  
   8 comments. A concrete resource-lifecycle failure: MCP process sets are created per thread and reclamation lags creation. Directly affects long-running app-server stability.

9. **[#51043](https://github.com/openai/codex/issues/51043) — Usage-limit reset not recognized by Pi; OpenAI model usage still blocked**  
   7 comments, 14 👍. Highest positive reaction in the issue set. Auth/usage-reset state is not propagating consistently across clients.

10. **[#54540](https://github.com/openai/codex/issues/54540) — Windows recurring “workspace routing discovery timed out” during goals and subagent workflows**  
    10 comments, 3 👍. Subagent and goal workflows are sensitive to routing discovery timeouts, pointing to app-server connectivity/orchestration instability.

## 3. Key PR Progress
No pull requests were updated in the last 24h (0 items). There are no PRs to summarize from this snapshot; this is a data-availability limitation, not an indication that no work is in progress.

## 4. Hot Discussions

### Show and tell

- **[#55421](https://github.com/openai/codex/discussions/55421) — TAHU: can an agent send a harmless email without leaking staff notes?**  
  8 comments. A Python authorization-gateway prototype exploring approval boundaries and privacy-preserving agent actions. Seeking three developer trials.

- **[#55086](https://github.com/openai/codex/discussions/55086) — KiraBridge + ThreadDesk: connecting seven tools for controllable, voice-driven vibe coding**  
  4 comments. Focused on scoped access, budget limits, traceable results, and preserving project context across interruptions.

- **[#56176](https://github.com/openai/codex/discussions/56176) — Does a fresh Codex session receive AGENTS.md? A repeatable before/after check**  
  4 comments. Provides a practical marker-based method for verifying instruction-path propagation after AGENTS.md or Codex changes.

- **[#56181](https://github.com/openai/codex/discussions/56181) — pytest-codex: turn AGENTS.md rules into pytest checks, with offline replay**  
  2 comments. A small MIT-licensed pytest plugin for testing Codex CLI behavior against AGENTS.md rules.

- **[#56731](https://github.com/openai/codex/discussions/56731) — Agoryx: a local-first shared conversation for Codex and Claude Code**  
  1 comment. MIT-licensed GUI for a human-directed build-and-review loop across Codex and Claude Code.

- **[#56809](https://github.com/openai/codex/discussions/56809) — wisp: run Codex tasks in the background, one worktree each, one-screen report**  
  1 comment. A workflow tool for parallel `codex exec` runs with per-task git worktrees and summarized diffs instead of full transcripts.

- **[#55901](https://github.com/openai/codex/discussions/55901) — New Codex chat, same project: which decisions still hold?**  
  1 comment. GFT Map organizes decisions, reasons, and open questions so context survives fresh chats.

- **[#56215](https://github.com/openai/codex/discussions/56215) — Plan in Claude, review in Codex: bring both into your next session with GFT-Merge**  
  1 comment. Cross-agent handoff tooling that merges local conversation context before continuing work.

### Q&A

- **[#52835](https://github.com/openai/codex/discussions/52835) — AI-assisted KPI management system: 4 months of development and still no working application**  
  4 comments. A non-developer operations manager seeks advice after extended AI-assisted build attempts. Highlights onboarding and project-management friction for non-engineers.

- **[#56686](https://github.com/openai/codex/discussions/56686) — Supported pre-request token bounds for ChatGPT-authenticated `codex exec`**  
  1 comment. Asks for a hard token ceiling without API-key or replacement-provider workarounds. Cost-control guardrails remain a gap.

No explicit “Ideas” threads appeared in this snapshot.

## 5. Feature Request Trends

- **Agent verification and auditability:** evidence-backed completion reports, AGENTS.md propagation checks, and pytest-based behavior tests.
- **Context and history continuity:** reliable thread reading, permanent chat deletion, decision maps across fresh chats, and cross-agent handoff.
- **MCP/app-server resource isolation:** per-thread MCP process control, ephemeral-thread cleanup, and bounded `git diff` fan-out.
- **Windows and IDE stability:** VS Code extension startup, long-chat memory growth, filesystem metadata handling, and process reclamation.
- **Permission and safety clarity:** reliable approvals, predictable “Full access” behavior, and safety checks that do not block authorized security work.
- **Auth and rate-limit consistency:** usage resets propagating across clients, model-capacity transparency, and token-budget controls.
- **Parallel and background orchestration:** worktree-per-task runners, one-screen reports, multi-agent shared conversations, and scoped tool access.
- **Remote/mobile reliability:** iPad and remote-session stability remain a clear request area.

## 6. Developer Pain Points

- **Windows is the highest-friction platform:** repeated reports of extension UI failures, `git diff` process explosions, MCP process counts, `os.setxattr` crashes, and long-chat renderer memory growth.
- **MCP lifecycle management is fragile:** per-thread MCP stacks, ephemeral summary threads leaking full configurations, and slow reclamation create memory and process pressure.
- **Thread/history APIs regress silently:** truncated turns, false end-of-history, and content-reference-only replies undermine trust in app-server data retrieval.
- **Sandbox and permission states are opaque:** unavailable `/approve` after denial, “Full access” silently falling back to workspace-write, and safety checks blocking authorized work.
- **Rate-limit and auth state is inconsistent across clients:** resets not recognized, model-capacity failures, and unclear token bounds for ChatGPT-authenticated `codex exec`.
- **Agent trust is hard to verify:** natural-language completion claims lack evidence, and AGENTS.md adherence is difficult to test without external tooling.
- **Remote and mobile sessions remain unreliable:** freezes, routing timeouts, and connectivity failures interrupt workflows.
- **Non-developers still struggle to ship:** long AI-assisted projects can stall without architecture, scope control, or verification practices.

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-10-25

## 1. Today's Highlights

Agent reliability remains the dominant theme: the two highest-engagement open issues are a P1 where subagent `MAX_TURNS` termination is misreported as `GOAL` success (#22323, 13 comments) and the P1 generalist-agent hang (#21409, 8 👍). On the PR side, contributors are pushing terminal-integration polish (OSC 7501 status reporting), system-prompt extensibility, and a cluster of correctness fixes in ACP, `read_many_files`, and shell-sandbox heuristics. A single nightly shipped in the last 24h with CLI stream-cancellation and CJK display-width fixes.

## 2. Releases

**v0.67.0-nightly.20261024.gcd1b45c2d** — [release notes](https://github.com/google-gemini/gemini-cli/releases/tag/v0.67.0-nightly.20261024.gcd1b45c2d)

Nightly build only (no stable release in this window). Visible changes:
- **CLI:** stop continuation stream processing and auto-nudge after request cancellation ([#29868](https://github.com/google-gemini/gemini-cli/pull/29868)) — prevents the CLI from continuing to consume/emit after a user cancels.
- **CLI:** correct Thai and Lao SARA AM display width ([#29868-adjacent PR by @elberthc-byte](https://github.com/google-gemini/gemini-cli/pulls)) — a terminal-width rendering fix for Southeast Asian scripts.

## 3. Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — P1: Subagent `MAX_TURNS` recovery reported as `GOAL` success** (13 comments, 2 👍). `codebase_investigator` returns `status: "success"` / `Termination Reason: "GOAL"` despite its own output admitting it hit the turn limit before analyzing anything. This is the most-commented issue of the day because it corrupts orchestration semantics: callers cannot distinguish "finished" from "gave up," making multi-agent pipelines unverifiable.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — P1: Generalist agent hangs** (8 comments, **8 👍** — highest reaction count in the set). Simple operations like folder creation hang indefinitely once work is delegated; users report waiting up to an hour. The workaround (instructing the model not to defer to subagents) effectively disables the feature, which explains the strong community signal.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — P2: Zero-dependency OS sandboxing + post-execution intent routing** (9 comments). Proposes letting Gemini lean on its native bash affinity (`grep`/`sed`/`awk` chains) while containing risk via sandboxing rather than restricting tools. Notable as the main architectural counter-proposal to the current tool-permission model.

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — P2: Gemini does not use skills and sub-agents enough** (7 comments). Anecdotal but widely shared: skills with clear descriptions (e.g. `gradle`, `git`) are ignored unless explicitly invoked. Matters because it undermines the value of the whole skills/subagent investment.

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — P2 EPIC: AST-aware file reads, search, and codebase mapping** (7 comments, 1 👍). Tracks whether AST-aware tools reduce misaligned reads, turn count, and token noise. Companion issues [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) (CLI tooling, tilth/glyph) and [#22747](https://github.com/google-gemini/gemini-cli/issues/22747) (ast-grep) show this is being decomposed into concrete experiments.

6. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — P1: Browser subagent fails on Wayland** (4 comments, 1 👍). Prints "Browser Agent Finished / Termination Reason: GOAL" while having failed — the same false-success pattern as #22323, now with a Linux display-server dimension.

7. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — P2: 400 error when too many tools are in scope** (3 comments). Title cites >128 tools, body cites >400; either way, the agent has no strategy for scoping enabled tools, so large MCP setups can hard-fail.

8. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — P2: Agent should stop/discourage destructive behavior** (3 comments, 1 👍). Model reaches for `git reset`/`--force` when safer paths exist, and lacks guardrails around stateful resources like databases. Directly relevant to unattended/YOLO usage.

9. **[#22186](https://github.com/google-gemini/gemini-cli/issues/22186) — P1: `get-shit-done` output hook causes crash** (3 comments). Reproducible crash while printing the final summary — i.e. the failure lands at the moment of handing results back to the user.

10. **[#22598](https://github.com/google-gemini/gemini-cli/issues/22598) — P3: Subagent trajectory should be visible via `/chat share`** (2 comments, 1 👍). Trajectories are recorded but not reachable, blocking subagent review and evals. Small feature, high leverage for debugging the issues above.

*Also worth watching:* [#22267](https://github.com/google-gemini/gemini-cli/issues/22267) (Browser Agent ignores `settings.json` overrides such as `maxTurns`), [#20079](https://github.com/google-gemini/gemini-cli/issues/20079) (symlinked agent `.md` files not discovered), [#21924](https://github.com/google-gemini/gemini-cli/issues/21924) (flicker-free terminal resize), [#18836](https://github.com/google-gemini/gemini-cli/issues/18836) (replace `WriteToDo` with persistent file-based task CRUD).

## 4. Key PR Progress

1. **[#29918](https://github.com/google-gemini/gemini-cli/pull/29918) — `feat(cli)`: report working/blocked/idle status via OSC 7501** (XL). Emits Program Status reports so terminals can surface which of several sessions needs attention. A meaningful step toward multi-session, terminal-native agent workflows.

2. **[#29919](https://github.com/google-gemini/gemini-cli/pull/29919) — `feat(cli)`: `--append-system-prompt`** (L). Adds `--append-system-prompt`, `--append-system-prompt-file`, and `GEMINI_APPEND_SYSTEM_MD`. Fills a real gap: today `GEMINI_SYSTEM_MD` replaces the entire prompt, so adding one line of project context means forking the whole thing.

3. **[#20406](https://github.com/google-gemini/gemini-cli/pull/20406) — `perf(core)`: memory management for large tool output** (XL). Writes oversized shell output to disk instead of buffering in memory, fixing V8 OOM in truncation paths. Long-running and high-impact for large-repo users.

4. **[#23917](https://github.com/google-gemini/gemini-cli/pull/23917) — `feat(ui)`: `StyleSpan` abstraction** (XL). Replaces per-character `toStyledCharacters()` allocation with run-length encoding, targeting 280MB+ heap bloat in complex `TableRenderer` scenarios.

5. **[#29910](https://github.com/google-gemini/gemini-cli/pull/29910) — `fix(acp)`: MCP metadata in permission requests + client trust** (M). Populates `toolCall._meta['gemini-cli'].mcp` with server/tool names and lets ACP clients pre-authorize client-supplied MCP servers via `_meta.trust` — important for editor-integrated deployments.

6. **[#29778](https://github.com/google-gemini/gemini-cli/pull/29778) — `fix(acp)`: truncate tool output before sending to the model** (M). `truncateToolOutputThreshold` was never applied in `--acp` mode, so one large shell output could blow the input limit with a 400. Closes a notable ACP-vs-CLI behavioral divergence.

7. **[#29783](https://github.com/google-gemini/gemini-cli/pull/29783) — P1 `fix(core)`: match include patterns for `read_many_files` assets** (M). Replaces substring checks with real pattern matching; previously any textual overlap could inline binary assets the user never requested.

8. **[#29782](https://github.com/google-gemini/gemini-cli/pull/29782) — P1 `fix(core)`: null check for exit code in sandbox denial heuristic** (S). `exitCode !== undefined` was always true for `null`, so every null-exit result entered the denial branch. Small diff, security-relevant correctness fix.

9. **[#29912](https://github.com/google-gemini/gemini-cli/pull/29912) — `fix(core)`: strip trailing punctuation/unbalanced brackets from `web_fetch` URLs** (M). `new URL()` happily accepts a trailing comma, so comma-separated URL lists produced polluted URLs and downstream failures.

10. **[#29779](https://github.com/google-gemini/gemini-cli/pull/29779) — `fix(core)`: mark failed ripgrep searches as tool errors** (M). `RipGrepTool` returned failures without setting `error`, so the scheduler recorded them as successes while telling the model the search failed — a silent-truth-divergence bug of the same family as #22323.

*Also merged/closed or in flight:* [#29890](https://github.com/google-gemini/gemini-cli/pull/29890) (only restore workspace dirs after a successful resume), [#29746](https://github.com/google-gemini/gemini-cli/pull/29746) (PowerShell substitution quote tracking), [#29785](https://github.com/google-gemini/gemini-cli/pull/29785) (dispatch final Code Assist SSE event at EOS), [#29902](https://github.com/google-gemini/gemini-cli/pull/29902) (docs: `commandPrefix` matches command names, not effects — recommends allow-lists for unattended runs).

## 5. Hot Discussions

No discussion data was provided for this window, so this section is omitted.

## 6. Feature Request Trends

- **Subagent orchestration & observability.** The largest cluster by far: accurate termination semantics (#22323), trajectory visibility in `/chat share` (#22598), discovery via `settings.json` (#18285), shared memory / parallel collaboration (#18287), and agent self-awareness of its own flags and hotkeys (#21432). The through-line is *make multi-agent runs inspectable and trustworthy*.
- **Context and token frugality.** AST-aware reads/search/mapping (#22745, #22746, #22747) and "Tactful Extraction" surgical read hierarchies (#19561) both target the ~36.6k-token-per-turn baseline and the +15k/turn blowups from firehose file reads.
- **Sandboxing over restriction.** #19873 argues for zero-dependency OS sandboxing plus post-execution intent routing so the model can use native bash idioms safely; #22672 asks for guardrails against destructive commands; #18397 asks for per-workspace (not global) policy scoping.
- **Out-of-context persistent state.** #18836 and #21000 both push task tracking out of the prompt and onto the filesystem (or native file tools) to avoid context rot and session-loss.
- **Smarter tool scoping.** #24246 asks the agent to reason about which tools are enabled rather than blindly attaching everything and hitting a 400.
- **Terminal UX as a first-class surface.** Flicker-free resize (#21924) and OSC 7501 status reporting (#29918) reflect users running many concurrent CLI sessions.

## 7. Developer Pain Points

- **False success reporting.** The single most corrosive pattern in the tracker: `MAX_TURNS` surfacing as `GOAL` success (#22323), Wayland browser failures also reporting `GOAL` (#21983), and ripgrep failures recorded as successes (#29779). Developers cannot trust exit signals, which breaks automation and evals.
- **Indefinite hangs with no timeout or diagnostic.** Generalist-agent delegation (#21409) and interactive prompts such as `vite create` (#22465) stall until manually cancelled; users report waiting an hour. The only known fix is disabling the feature.
- **Configuration that is silently ignored.** Browser Agent skips `settings.json` overrides like `maxTurns` (#22267), symlinked agent files are not discovered (#20079), and `truncateToolOutputThreshold` was a no-op in ACP mode (#29778). Config surface diverges from config behavior.
- **Escaping and quoting edge cases.** Incorrect `\n` handling (#22466), PowerShell substitution quote desync (#29746), and URL tokenization (#29912) all stem from naive string handling around shell and text boundaries.
- **Platform-specific breakage.** Wayland (#21983), Thai/Lao SARA AM width (nightly fix), and Windows quoting continue to generate their own issue stream — cross-platform parity is a persistent tax.
- **Memory and rendering overhead.** Large tool output causing OOM (#20406), per-character allocations in table rendering (#23917), and resize flicker (#21924) point at the TUI's scaling limits.
- **Workspace hygiene and blast radius.** Temporary scripts scattered across directories (#23571) and destructive git operations (#22672) make unattended runs hard to clean up and hard to trust.
- **Extensibility gaps in the prompt layer.** The absence of a non-destructive system-prompt append (addressed by #29919) forced users to replace the entire prompt for a one-line project instruction.

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — Community Digest
**Date:** 2026-10-25 | **Source:** [github/copilot-cli](https://github.com/github/copilot-cli)

---

## 1. Today's Highlights

Two patch releases landed (`v1.0.104-2`, `v1.0.104-3`), headlined by a fix ensuring Autopilot sessions in interactive/SDK modes *wait* for attached background tasks rather than terminating at `COPILOT_TASK_WAIT_TIMEOUT_SECONDS`. Issue activity was heavy (20 items updated), dominated by **async/background-task correctness** (#4946, #5242, #5205) and **agent/subagent tooling regressions** (#5243, #5249), with a notable billing incident around Assisted Permissions quota consumption (#4802). No pull requests were updated in the last 24 hours.

---

## 2. Releases

**`v1.0.104-3`** — *Fixes and changes* (notes truncated upstream)

**`v1.0.104-2`**
- **Fixed:** Managed model `fusion` now starts sessions on **Auto** and correctly applies `fusionTier` settings.
- **Fixed:** Interactive and SDK Autopilot sessions now keep waiting for attached background tasks instead of stopping after `COPILOT_TASK_WAIT_TIMEOUT_SECONDS`; `-p --autopilot` continues to honor the timeout.
- *Note:* The release body is minimal — several open issues below reference version `1.0.101`–`1.0.103` behavior, so regression coverage should be confirmed against `1.0.104-3`.

🔗 [Releases](https://github.com/github/copilot-cli/releases)

---

## 3. Hot Issues

1. **[#4946 OPEN] HTTP 400 `content[].thinking` after background shell completion** — 8 comments, 👍1
   Highest-engagement open item. A background shell command that outlives its turn injects a `system.notification` into a *new* turn, corrupting the thinking-block payload and hard-failing the request. Directly relevant to the new background-task wait semantics shipped in `v1.0.104-2`.
   🔗 [Issue #4946](https://github.com/github/copilot-cli/issues/4946)

2. **[#4802 OPEN] PRU quota wiped out, likely tied to Assisted Permissions** — 6 comments
   Legacy request-based PRU user reports 234 PRUs drained after enabling Assisted Permissions. Billing-affecting, so it carries outsized community weight despite modest reactions — worth prioritizing for triage clarity.
   🔗 [Issue #4802](https://github.com/github/copilot-cli/issues/4802)

3. **[#5243 OPEN] Subagents started in the same turn as an approved `exit_plan_mode` get no built-in tools** *(regression after 1.0.95)*
   Subagents lose `bash`/`powershell`, `view`, `create`, `edit`, `grep`, `glob`, `task`, `skill`, and `web` entirely when spawned after an `interactive` plan approval. A severe silent-failure mode for plan-then-execute workflows.
   🔗 [Issue #5243](https://github.com/github/copilot-cli/issues/5243)

4. **[#5242 OPEN] Sessions stop processing when MCP config watches hit inotify `ENOSPC`**
   `copilot --server` in containers on shared Linux hosts abandons the session queue after one retry when the inotify budget is exhausted — a production-grade reliability bug for server deployments.
   🔗 [Issue #5242](https://github.com/github/copilot-cli/issues/5242)

5. **[#5229 OPEN] ACP mode: expired MCP OAuth token opens browser instead of using refresh token**
   `copilot --acp` jumps straight to `[acp::mcp_oauth] opened the MCP OAuth authorization page`, ignoring a stored `refresh_token`. Breaks unattended/headless ACP integrations.
   🔗 [Issue #5229](https://github.com/github/copilot-cli/issues/5229)

6. **[#5205 OPEN] Context window rollover fails: "checkpoint.md is absent for this agent"**
   Long-running agent sessions crash when the context window fills and rollover is attempted. This is the clearest blocker to sustained autonomous runs reported this cycle.
   🔗 [Issue #5205](https://github.com/github/copilot-cli/issues/5205)

7. **[#5249 OPEN] Model-facing `skill()` tool registers plugin skills under bare names**
   Slash commands received `/plugin:skill` namespacing in #1766, but `skill()` did not — so `skill("alpha:unique")` returns `Skill not found` unless two plugins happen to collide. An API-consistency gap that will bite plugin authors.
   🔗 [Issue #5249](https://github.com/github/copilot-cli/issues/5249)

8. **[#5228 OPEN] `copilot --resume` without an ID drops into a new empty session**
   Since the unified Sessions browser became default, the list renders for ~2s before switching away, and the trust prompt swallows the first Enter. A UX regression from a recently-shipped feature — compound, reproducible, and user-visible.
   🔗 [Issue #5228](https://github.com/github/copilot-cli/issues/5228)

9. **[#5245 OPEN] No STT models behind a corporate proxy since 1.0.101** *(Windows)*
   The Foundry runtime ignores proxy settings, so voice/STT fails entirely on TLS-inspecting corporate networks. Notable as a **cross-cutting runtime config bug**, not just a voice bug.
   🔗 [Issue #5245](https://github.com/github/copilot-cli/issues/5245)

10. **[#3301 OPEN] Feature request: local web interface (like `opencode web`)** — 👍3 (highest reaction in this set)
    Long-running request for a browser-based UI served over a local HTTP server. Persistent upvotes over ~5 months signal durable demand for non-TUI interaction modes.
    🔗 [Issue #3301](https://github.com/github/copilot-cli/issues/3301)

**Also closed in the last 24h:** [#1616](https://github.com/github/copilot-cli/issues/1616) (Autopilot burned tokens after task completion), [#5147](https://github.com/github/copilot-cli/issues/5147) (`/restart` failed with `--new-session-id`), [#3815](https://github.com/github/copilot-cli/issues/3815) (Windows debug-log path missing `\`).

---

## 4. Key PR Progress

**No pull requests were updated in the last 24 hours (Total: 0 items).** No merge or review activity to report for this digest window.

---

## 5. Hot Discussions

*Omitted — no Discussions data was provided.*

---

## 6. Feature Request Trends

Distilled from the 20 issues updated in this window:

- **Alternative interaction surfaces** — Local web UI/browser-based agent (#3301, 👍3) is the most-upvoted request; also in the same family: input-box pinning to screen top (#5227) for long-session ergonomics.
- **ACP / non-interactive parity with the TUI** — Computer Use plugin unavailable in ACP mode despite being enabled in CLI (#5049); bundled-plugin and MCP feature gating diverges between headless and interactive paths.
- **Headless OAuth & network configurability** — Refresh-token reuse in ACP (#5229) and proxy-aware runtime networking (#5245) are both requests to make non-interactive runs viable behind enterprise infrastructure.
- **Plugin/skill namespacing consistency** — #5249 asks that the model-facing `skill()` tool match the `/plugin:skill` convention already used by slash commands.
- **Server-mode resilience** — #5242 requests that MCP config-watch failures degrade gracefully (retry/backoff/watch budgeting) rather than abandoning the session queue.

---

## 7. Developer Pain Points

- **Background tasks and turn boundaries remain fragile.** The `v1.0.104-2` wait-semantics fix lands alongside #4946 (thinking-block 400 after background shell completion) and the newly closed #1616 (Autopilot token burn after task completion) — the async lifecycle is the single most active source of breakage.
- **Headless/ACP deployments hit infrastructure edges first.** inotify exhaustion (#5242), missing OAuth refresh (#5229), proxy-blind runtime (#5245), and ACP plugin gaps (#5049) collectively make "it works on my laptop, not in CI/container" the dominant deployment complaint.
- **Regressions track feature velocity.** #5243 (subagent tools lost after `exit_plan_mode`, post-1.0.95) and #5228 (`--resume` broken by the new Sessions browser) are newly-shipped surfaces regressing quietly — users are asking for regression tests around plan approval and session selection.
- **Long-running agents cannot sustain context.** #5205 (missing `checkpoint.md` on rollover) plus #1616 and #5243 indicate that long autonomous runs — the headline Autopilot use case — still lack a reliable completion path.
- **Cost predictability is a trust issue.** The Assisted Permissions quota incident (#4802) is low-volume but high-severity: ambiguous billing outcomes erode adoption faster than feature gaps, and users want a documented attribution path.
- **Silent, low-signal failure modes.** #5249 (`Skill not found`), #5243 (tools silently absent), and #5107 (`script_action_changed` on harmless `echo` with a `HOME` override) all fail without actionable diagnostics — a recurring request for clearer error surfaces.

---

*Generated for 2026-10-25. Issue set: 20 items updated, 3 closed. PR set: 0 items. Releases: 2.*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-10-25

## Today's Highlights

There were no new releases in the last 24 hours, and the community's attention is almost entirely consumed by a widespread **free-tier quota failure**: users on both v1 and v2, CLI and Desktop, report "Free usage exceeded" errors despite zero usage, with the 86,400-second cooldown looping back to zero instead of resetting. In parallel, maintainers landed a dense batch of core reliability PRs covering session integrity, subagent result reporting, graceful shutdown, and catalog/ETag optimization.

---

## Releases

No new releases published in the last 24 hours.

---

## Hot Issues

1. **[#58831] Free quota timeout never recovers, sawtooths at 86400s** — [link](https://github.com/anomalyco/opencode/issues/58831)
   The clearest reproduction of the current quota bug: the countdown reaches zero and immediately resets to 86,400s. This is the canonical thread for the entitlement-reset failure affecting all free users.

2. **[#58830] Free usage limit has not reset for two consecutive days in v1 and v2** — [link](https://github.com/anomalyco/opencode/issues/58830)
   Confirms the regression spans *both* major versions, ruling out a v2-only migration issue. After one successful request, every free model fails — a strong signal the per-account usage counter is being persisted or read incorrectly.

3. **[#58828] "OpenCode is genuinely broken and HAS to be fixed IMMEDIATELY"** — [link](https://github.com/anomalyco/opencode/issues/58828)
   A high-frustration escalation reporting instant quota exhaustion on Go and Zen models with an 86,400s wait even for users without a subscription. Illustrates how the quota bug is now driving churn and hostile reports.

4. **[#58781] Free Usage Exceeded on all free models in OpenCode Desktop 1.19.4** — [link](https://github.com/anomalyco/opencode/issues/58781)
   The most-upvoted issue in this window (👍 5). The Desktop-specific angle plus vote count makes it the best proxy for broader user sentiment on the free tier.

5. **[#58605] "Free usage exceeded" after a week of inactivity (Desktop)** — [link](https://github.com/anomalyco/opencode/issues/58605)
   Adds a telling detail: the retry timer reads "retrying in 72970s - attempt no. 1," suggesting malformed remaining-quota or expiry timestamps rather than a genuine usage count.

6. **[#58827] Usage limit hit before any prompts were submitted** — [link](https://github.com/anomalyco/opencode/issues/58827)
   Zero-prompt exhaustion is the strongest evidence that the check is failing *before* any request is made — i.e., an entitlement/auth lookup failure, not over-consumption.

7. **[#58724] Manual compaction omits required `recent`, permanently bricking the session** — [link](https://github.com/anomalyco/opencode/issues/58724)
   Serious data-integrity bug: schema decode rejects the persisted compaction message, so every subsequent read fails and forks are also poisoned — with no in-app recovery. High impact for long-running sessions.

8. **[#58527] Permission "ask" silently allows edit/write** — [link](https://github.com/anomalyco/opencode/issues/58527)
   A security-relevant bug: `ask` renders an approval dialog for shell but behaves as `allow` for edit/write. Silent file writes in a tool that promises explicit consent is a trust-breaker.

9. **[#52925] `opencode api get` truncates stdout when piped** — [link](https://github.com/anomalyco/opencode/issues/52925)
   Breaks the canonical `opencode api get ... | jq` workflow on large (~580 KB) responses while file redirection works fine. Long-lived since Oct 3 and still open — a scripting/CI blocker.

10. **[#58533] Desktop custom-provider dialog writes V1 keys the V2 server ignores** — [link](https://github.com/anomalyco/opencode/issues/58533)
    Representative of the v1→v2 migration class of failures: config written in the old shape produces a confusing "provider not available on this server" toast. Worth highlighting alongside [#58529](https://github.com/anomalyco/opencode/issues/58529) (DeepSeek V4.1 Flash Free infinite loop) and [#57931](https://github.com/anomalyco/opencode/issues/57931) (expired free model blocking the whole catalog), which share the "one bad model poisons the catalog" pattern.

*Also notable:* [#55077](https://github.com/anomalyco/opencode/issues/55077) — GPT-6.1 Sol context window raised to 628k in v2.0.28 but still short of the advertised 1.05M; [#58558](https://github.com/anomalyco/opencode/issues/58558) — hard-coded 1s discovery timeout silently returns zero models for remote Ollama/LM Studio/vLLM endpoints (closed); [#58559](https://github.com/anomalyco/opencode/issues/58559) — Windows `%temp%` relocation crashes the CLI (closed).

---

## Key PR Progress

1. **[#58820] perf(core): resize images in a worker, release photon memory when idle** — [link](https://github.com/anomalyco/opencode/pull/58820)
   Eliminates a 420–460 ms server freeze per large image and reclaims ~250 MB of retained WASM memory by moving resizing off the main thread and idling the worker after 10s. Strong win for Desktop/web responsiveness.

2. **[#58817] fix(cli): bound server shutdown with a 10s deadline** — [link](https://github.com/anomalyco/opencode/pull/58817)
   Teardown could previously hang forever after SIGTERM/SIGINT with no indication of the stuck step. Now logs pending work and exits 1, with second-signal handling.

3. **[#58825] fix(core): report a background subagent's later turns to its parent** — [link](https://github.com/anomalyco/opencode/pull/58825)
   Fixes parents receiving "Subagent completed without a text response" when a child went idle and was later woken by a plugin or synthetic message — a correctness fix for multi-agent orchestration.

4. **[#58811] fix(schema): validate base64 attachments in linear time** — [link](https://github.com/anomalyco/opencode/pull/58811)
   Replaces a catastrophic-backtracking-prone regex that ran on *every* stored message decode (history reload, inbox twice, projector, GET messages). Meaningful latency and hardening improvement.

5. **[#58813] fix(core): revalidate the models.dev catalog with its ETag** — [link](https://github.com/anomalyco/opencode/pull/58813)
   The 5-minute refresh never sent `If-None-Match`, re-downloading and parsing the full ~6 MB raw (~413 KB brotli) catalog every cycle despite a 304 being available.

6. **[#55146] fix(core): use session transport for foreground compaction** — [link](https://github.com/anomalyco/opencode/pull/55146)
   Foreground summary compaction was sending HTTP even when the selected model uses WebSocket. Directly relevant to the compaction-related breakage seen in #58724.

7. **[#58790] fix(ai): add `compatibility.omitEmptyTools` to drop empty tools arrays** — [link](https://github.com/anomalyco/opencode/pull/58790)
   Addresses providers that reject requests where the effective tool set is empty but history contains tool calls/results — a common cross-provider interop failure.

8. **[#58788] fix(core): preserve file mode when patch move emulates rename** — [link](https://github.com/anomalyco/opencode/pull/58788)
   "Move to" is implemented as write-then-delete, silently losing permissions on the destination. Important for scripts and executables touched by the agent.

9. **[#58800] perf(core): skip integration list rebuilds when nothing changed** — [link](https://github.com/anomalyco/opencode/pull/58800)
   Every session step rebuilt the integration list multiple times, decoding all stored credentials each pass. Caching fix reduces per-step overhead noticeably.

10. **[#58310] fix(cli): retain Windows binaries across volumes** — [link](https://github.com/anomalyco/opencode/pull/58310)
    Retention fails with `EXDEV` when package and cache live on different Windows volumes; complements the `%temp%`-relocation crash in #58559.

*Also merged/closed:* [#58815](https://github.com/anomalyco/opencode/pull/58815) (stop leaking service `XDG_STATE_HOME` into tool shells, name the port owner), [#58758](https://github.com/anomalyco/opencode/pull/58758) (replace experimental evaluation with `Decision`), [#58771](https://github.com/anomalyco/opencode/pull/58771)/[#58770](https://github.com/anomalyco/opencode/pull/58770) (error classification for Google Interactions and AWS restJson1), plus docs updates [#58791](https://github.com/anomalyco/opencode/pull/58791) (MiMo Code moved to validated clients) and [#58823](https://github.com/anomalyco/opencode/pull/58823) (Luminary memory plugin).

---

## Feature Request Trends

- **Theme/customization extensibility** — [#58824](https://github.com/anomalyco/opencode/issues/58824) asks for theme overrides and language-token extensions on built-in themes (Catppuccin Mocha, Tokyo Night) rather than fixed scope mappings. Users want to extend themes without forking them.
- **Better multi-agent observability** — [#58809](https://github.com/anomalyco/opencode/issues/58809) requests showing each subagent's *effective* model and reasoning effort in the Desktop v2 running-subagents list. As subagent orchestration grows, so does demand for visibility into what children are actually doing.
- **Tighter turn control / steering semantics** — [#58776](https://github.com/anomalyco/opencode/issues/58776) proposes conditional steering admission with an expected-turn guard, so corrections are only admitted when the session still owns the turn.
- **Schema clarity for tool-calling models** — [#55504](https://github.com/anomalyco/opencode/issues/55504) notes `sessionID` is optional in TypeScript but serialized such that models treat it as required, pushing toward more explicit JSON Schema generation.

---

## Developer Pain Points

1. **Free-tier entitlement checks are the dominant failure mode.** At least a dozen issues in this window ([#58536](https://github.com/anomalyco/opencode/issues/58536), [#58550](https://github.com/anomalyco/opencode/issues/58550), [#58553](https://github.com/anomalyco/opencode/issues/58553), [#58557](https://github.com/anomalyco/opencode/issues/58557), [#58605](https://github.com/anomalyco/opencode/issues/58605), [#58781](https://github.com/anomalyco/opencode/issues/58781), [#58827](https://github.com/anomalyco/opencode/issues/58827), [#58828](https://github.com/anomalyco/opencode/issues/58828), [#58829](https://github.com/anomalyco/opencode/issues/58829), [#58830](https://github.com/anomalyco/opencode/issues/58830), [#58831](https://github.com/anomalyco/opencode/issues/58831), [#58832](https://github.com/anomalyco/opencode/issues/58832)) report quota exhaustion with zero or one request. Reports span CLI v2.1.1, Desktop 1.19.4 and v2, and multiple regions/IPs — pointing at a server-side counter or entitlement lookup rather than client overuse. The non-resetting 86,400s timer and "retrying in 72970s" suggest timestamp handling is also broken.

2. **v1 → v2 migration is still rough.** Config written in V1 shape (`provider`, `npm`, `options`) is silently ignored by the V2 server ([#58533](https://github.com/anomalyco/opencode/issues/58533)), and users migrating specifically to escape v1 quota bugs hit API-key expiry on fresh keys ([#58832](https://github.com/anomalyco/opencode/issues/58832)).

3. **Version/config drift creates brittle behavior.** Hard-coded 1-second discovery timeouts ([#58558](https://github.com/anomalyco/opencode/issues/58558)), expired free models invalidating the entire catalog ([#57931](https://github.com/anomalyco/opencode/issues/57931)), and stale model metadata ([#55077](https://github.com/anomalyco/opencode/issues/55077)) all show a need for graceful degradation instead of all-or-nothing failure.

4. **Data integrity and consent guarantees are being questioned.** A manual compaction can permanently brick a session ([#58724](https://github.com/anomalyco/opencode/issues/58724)), and `ask` permissions silently allow file writes ([#58527](https://github.com/anomalyco/opencode/issues/58527)). Both erode confidence in long-running agent workflows.

5. **Windows and shell edge cases persist.** `%temp%` relocation crashes the CLI ([#58559](https://github.com/anomalyco/opencode/issues/58559)), cross-volume binary retention fails with `EXDEV` ([#58310](https://github.com/anomalyco/opencode/pull/58310)), and Nushell PATH setup was only recently supported ([#51227](https://github.com/anomalyco/opencode/pull/51227)).

6. **Scriptability gaps.** `opencode api get` truncating piped output ([#52925](https://github.com/anomalyco/opencode/issues/52925)) blocks the standard `| jq` and CI-automation patterns — a small fix with outsized impact for tooling users.

---

*No discussion data was provided for this window, so the Hot Discussions section is omitted.*

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-10-25

## 1. Today's Highlights

No new releases in the last 24 hours, but the day was dominated by a stacked provider-expansion PR chain from mitsuhiko (#11368 → #11372) that refactors the model catalog and adds Alibaba Model Studio, Z.AI, DeepInfra, Nebius, Ollama Cloud, Scaleway, SiliconFlow, StepFun, Vertex-hosted Claude, and Bedrock Mantle routing. Meanwhile, the highest-engagement issues remain long-running reliability problems: `openai-codex` TUI hangs (#4945, 88 comments) and TUI core pinning during streaming (#6665, #7730). A cluster of 1.3.0/1.4.0 terminal regressions (Windows CSI leakage, Ctrl+Shift+C prompt loss, Alacritty IME autocomplete) was triaged and mostly closed within hours.

---

## 2. Releases

Omitted — no releases published in the last 24 hours.

---

## 3. Hot Issues

1. **[#4945](https://github.com/earendil-works/pi/issues/4945) — `openai-codex` connection reliability (OPEN, in-progress, 88 comments, 👍35)**
   The TUI stalls on `Working...` with no streamed text, no tool call, and no error; Escape is the only recovery. This is the single most-discussed issue in the tracker and the clearest signal that provider-level stream handling still has unbounded-wait paths.

2. **[#7730](https://github.com/earendil-works/pi/issues/7730) — High CPU on macOS with long sessions (OPEN, bug, 22 comments, 👍11)**
   50–110% CPU and 600–800 MB RSS on long sessions, apparently correlated with context size. Community is treating this as the practical ceiling on multi-hour agent use.

3. **[#6665](https://github.com/earendil-works/pi/issues/6665) — TUI pins a full core while streaming (OPEN, in-progress, 15 comments, 👍7)**
   Reproduces with `pi -ne`, so the cause is core, not extension: uncached `Intl.Segmenter` grapheme segmentation plus a full Markdown rebuild per chunk. A spindump-backed diagnosis makes this one of the most actionable perf reports open.

4. **[#11157](https://github.com/earendil-works/pi/issues/11157) — `sendMessage` at `session_start` turns every session into a legacy transcript (OPEN, `prio:p0`, in-progress)**
   `getInitialSystemMessage` reads `messages[0]`; extensions like pi-memory and pi-session-search inject ahead of it, so Anthropic requests silently lose the `system` field. A p0 correctness bug caused entirely by extension ordering.

5. **[#8643](https://github.com/earendil-works/pi/issues/8643) — Bedrock: OpenAI models reject images nested in `toolResult.content` (OPEN, 14 comments, 👍5)**
   Requests hoisting tool-result images into sibling user blocks, matching what `openai-completions.ts` already does. Notably, the reporter says a fix + regression test are ready but the prior PR (#8642) was auto-closed by the contribution gate.

6. **[#8810](https://github.com/earendil-works/pi/issues/8810) — Extension-registered providers intermittently ignore `defaultProvider`/`defaultModel` (OPEN, 9 comments, 👍5)**
   Fresh sessions fall back to another provider's default when the provider comes from `pi.registerProvider()`. Nondeterminism here undermines trust in the extension provider API.

7. **[#10157](https://github.com/earendil-works/pi/issues/10157) — Gemini thought signatures dropped on the AI Studio OpenAI-compatible endpoint (OPEN)**
   `extra_content.google.thought_signature` is returned on tool calls but not replayed, breaking multi-turn reasoning for Gemini Flash Lite via AI Studio.

8. **[#10390](https://github.com/earendil-works/pi/issues/10390) — Anthropic SSE parser mishandles CRLF split across chunks (OPEN, bug)**
   `\r` and `\n` arriving in separate chunks are treated as two line breaks, flushing an event with no `data:`, which throws `Unexpected end of JSON input` and aborts the turn. A classic transport-boundary bug with a narrow but fatal blast radius.

9. **[#11221](https://github.com/earendil-works/pi/issues/11221) — Pi doesn't pick up refreshed AWS credentials in long-running sessions (OPEN, bug)**
   Bedrock sessions longer than the STS token lifetime fail with `ExpiredTokenException` even when a background refresher is running. Pairs with #10390 as evidence that long-lived session credential/transport handling needs an owner.

10. **[#11379](https://github.com/earendil-works/pi/issues/11379) — Ctrl+Shift+C loses the prompt being typed (CLOSED, bug)**
    A 1.3.0 regression that users described as "driving me crazy angry": copying from output eats the in-progress prompt. Closed within a day, along with sibling terminal-hygiene issues #11377 (`stripAnsi` leaking private-parameter CSI) and #11205 (Windows PowerShell flooded with mouse/focus reports).

*Also closed this cycle:* #9265 (O(n²) tool-call argument re-parsing in `openai-completions` streaming) and #10642 (embedded SDK session memory never dropping) — both marked `CLOSED` without action, despite describing concrete production blockers for embedded/multi-session hosts.

---

## 4. Key PR Progress

1. **[#11368](https://github.com/earendil-works/pi/pull/11368) — refactor provider definitions (OPEN)**
   Replaces `generate-models.ts` with one `defineProvider()` file per built-in provider, shared model-family rules (Claude, GPT, DeepSeek, Kimi, Google), and models.dev v2 API adapters. The foundation for the four stacked PRs below.

2. **[#11369](https://github.com/earendil-works/pi/pull/11369) — Alibaba Model Studio + Z.AI pay-as-you-go providers (OPEN)**
   Adds `alibaba`, `alibaba-cn` (DashScope international/China) and Z.AI PAYG alongside the plans pi already ships.

3. **[#11370](https://github.com/earendil-works/pi/pull/11370) — DeepInfra, Nebius, Ollama Cloud, Scaleway, SiliconFlow, StepFun (OPEN)**
   Six hosted inference providers selected by models.dev contributor activity and issue-tracker demand; DeepInfra alone contributes 74 models.

4. **[#11371](https://github.com/earendil-works/pi/pull/11371) — Claude models on Google Vertex AI (OPEN)**
   `google-vertex` now lists 13 non-deprecated Claude models via Vertex's Anthropic Messages API, implemented by reusing the Anthropic Messages code with a `fetch` wrapper.

5. **[#11372](https://github.com/earendil-works/pi/pull/11372) — Route non-Converse Bedrock models through Bedrock Mantle (OPEN)**
   Fixes #5363 for Bedrock API keys: 16 listed models (GPT-5.4→GPT-6.1, gpt-oss, Grok 4.3/4.6, Gemma 4) are only served on Mantle, the OpenAI-compatible endpoint, and were being rejected by Converse as invalid identifiers.

6. **[#11355](https://github.com/earendil-works/pi/pull/11355) — Kimi Coding K3.1 Pro support (CLOSED)**
   Derives the K3 wire preset from parsed effort metadata rather than a model-ID list; K3.1 Pro/Highspeed now expose low/high/max, send `reasoning_effort`, preserve reasoning content, and keep dynamically loaded tools out of the top-level tool list.

7. **[#9329](https://github.com/earendil-works/pi/pull/9329) — Detect Orca terminals as Kitty-image capable (OPEN)**
   Treats `TERM_PROGRAM=Orca` as supporting Kitty inline images, true color, and OSC 8 hyperlinks so image components stop falling back to text.

8. **[#11363](https://github.com/earendil-works/pi/pull/11363) — `fullscreenCopyTarget` setting for primary selection (CLOSED)**
   Lets fullscreen copy-on-select target the X11/Wayland primary buffer (middle-click paste) instead of the clipboard — directly relevant to the copy/paste friction in #11379.

9. **[#10135](https://github.com/earendil-works/pi/pull/10135) — Normalise compaction usage to prevent footer crash on resume (CLOSED)**
   Stops persisting raw provider `summaryUsage` on both passthrough branches, fixing a crash when resuming a compacted session.

10. **[#11341](https://github.com/earendil-works/pi/pull/11341) — Clear error for unusable proxy URLs (CLOSED)**
    `HTTP_PROXY`/`HTTPS_PROXY` values undici can't parse (e.g. `socks5h://`, missing scheme, URLs with paths) now exit with a readable message instead of an undici `Invalid URL protocol` stack trace.

---

## 5. Hot Discussions

**Show and tell**
- **[#11306](https://github.com/earendil-works/pi/discussions/11306) — pi-continuity: auto-resume after coding-plan usage limits and interrupted turns**
  Built around OpenAI's 5-hour usage limit, which halts a run and previously required manual "continue" hours later. Pi already retries transient provider errors and compacts on context overflow, but usage limits — a distinct failure class — had no handling.

**Community feedback**
- **[#11356](https://github.com/earendil-works/pi/discussions/11356) — Before 1.0 vs after 1.0 (rip)**
  The author argues baseline context consumption regressed post-1.0, with "one single Hello" approaching limits and Pi feeling closer to opencode in prompt overhead. Only 👍1, but it echoes the context-efficiency concerns in #7730 and #10642.

---

## 6. Feature Request Trends

- **Provider breadth and correct routing.** The dominant theme by volume: new pay-as-you-go providers (#11369), hosted inference endpoints (#11370), Claude on Vertex (#11371), Mantle routing for Bedrock (#11372), and effort-aware model presets (#11355). The direction is a declarative per-provider catalog rather than generated lists.
- **Multi-modal payload correctness across providers.** Hoisting tool-result images for Bedrock/OpenAI (#8643), using `file_id` references instead of replaying base64 for Codex (#8617), and preserving Gemini thought signatures (#10157) are all the same underlying ask: a provider-agnostic content model that adapters translate faithfully.
- **Extension API surface for real products.** A Code Mode output writer for host-owned storage (#11070), `watchEvents` resume from a last-seen entry (#11327), deterministic provider defaults (#8810), and composable compaction with span args, summary-model override, and entry refs (#11310).
- **Long-lived / embedded session lifecycle.** Durable execution semantics (#11139, #11327), memory reclamation after compaction (#10642), and credential refresh (#11221) point at a server-hosted-many-sessions use case that the current SDK doesn't yet fully support.
- **Terminal/UI ergonomics.** Kitty image support in more terminals (#9329), primary-selection copy target (#11363), IME commit handling (#11177), and clean escape-sequence stripping (#11377).

---

## 7. Developer Pain Points

- **Streaming performance is the top blocker for long sessions.** Two of the three highest-engagement issues (#6665, #7730) describe a full core pinned during streaming, with root causes traced to per-chunk Markdown rebuilds, uncached grapheme segmentation, and (previously, #9265) quadratic tool-argument re-parsing. Embedded hosts feel this hardest because a blocked event loop stalls every co-hosted session.
- **Provider integrations break on edge-case transports and payload shapes.** Stalled streams with no error (#4945), CRLF split across chunks (#10390), dropped thought signatures (#10157), rejected nested images (#8643), and stale AWS credentials (#11221) are all "works until it doesn't" failures that surface mid-turn and are hard to diagnose from the TUI.
- **Extension ecosystem fragility.** Extensions that register providers, send messages at `session_start`, or reload mid-session can silently change core behavior — losing the Anthropic `system` field (#11157), breaking all subsequent requests with `tool_reference_unresolved` (#11376), or flipping a session onto the wrong default model (#8810).
- **Terminal compatibility regressions keep recurring.** The 1.3.0 rollout produced a burst of UI-hygiene bugs: prompt loss on Ctrl+Shift+C (#11379), CSI mouse/focus payloads leaking into PowerShell (#11205), `stripAnsi` leaving private-parameter sequences (#11377), and Alacritty IME autocomplete failures (#11177).
- **Triage throughput vs. contributor intent.** Several closed-without-action issues (#9265, #10642, #11310, #10145, #8642) came with prototypes, patches, or production impact, and #8643 explicitly notes a prior PR was auto-closed by the contribution gate. Combined with many `untriaged` items closed the same day they were filed, the perceived review bar is a recurring source of friction.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-10-25

---

## 1. Today's Highlights

Qwen Code shipped **v0.26.1**, a small patch release headlined by a breaking fix that restores per-group session isolation for QQ bot channels, alongside **daemon token persistence** in the Desktop build. CI infrastructure continues to dominate community activity, with a cluster of issues around runner capacity, main-push failure reporting, and the Managed Agent gate consuming the most discussion. On the code side, security hardening (symlink escapes, read-only file atomic writes) and cost-accounting correctness (reasoning-token double counting) drew multiple competing PRs today.

---

## 2. Releases

### v0.26.1 (`qwen-code`)
- **Breaking change:** `fix(qqbot)!: restore per-group session isolation under thread scope` ([#13882](https://github.com/QwenLM/qwen-code/pull/13882), @Eric-GoodBoy-Tech) — corrects `sessionScope: "chat_thread"` behavior where direct messages were incorrectly treated as shared sessions (see also closed issue [#14350](https://github.com/QwenLM/qwen-code/issues/14350)).
- Release notes are currently a stub (`qwen-release-notes:v1`); the complete change list is deferred to the linked release.

### desktop-v0.26.1
- `feat(serve): add daemon token persistence` ([#15023](https://github.com/QwenLM/qwen-code/pull/15023), @wenshao).
- `fix(release): cover every spawned vendored binary` — release packaging hardening for bundled binaries.

---

## 3. Hot Issues

1. **[#13395](https://github.com/QwenLM/qwen-code/issues/13395) — tracking(runtime): Kubernetes tool runtime progress and cross-platform delivery gates** (23 comments, in-progress)
   The longest-running design tracker in the repo. Two S1 root causes are now implemented in Draft [#15457](https://github.com/QwenLM/qwen-code/pull/15457) and locally validated, but the issue stays open pending full CI and native ACK re-testing. The high comment count reflects sustained reviewer scrutiny over an extended period.

2. **[#15446](https://github.com/QwenLM/qwen-code/issues/15446) — Main CI failed: Managed Agent gate, `HostedPublicWorkspaceIT.publicShellReceiptFaultsNeverRepeatEffects` (+12 more)** (14 comments)
   The single largest CI failure cluster today. Repeated fault-gate failures in the Java hosted workspace suite are throttling merge velocity and generating noise across the issue tracker.

3. **[#13113](https://github.com/QwenLM/qwen-code/issues/13113) — Session becomes unopenable: "Transcript snapshot is too large to index"** (9 comments, P1)
   `file_history_snapshot` grows quadratically in long sessions, eventually exceeding a **hardcoded 256 MiB** index limit and rendering the session permanently unopenable. A clear data-loss-adjacent severity issue with strong community interest.

4. **[#14859](https://github.com/QwenLM/qwen-code/issues/14859) — ci: track runner capacity and independent main validation** (9 comments)
   The author explicitly retracts earlier percentage estimates and replaces them with a corrected capacity model. Signals that CI runner scarcity is now a first-class project concern rather than an isolated annoyance.

5. **[#15056](https://github.com/QwenLM/qwen-code/issues/15056) — feat: local `/autofix-pr` to watch a PR and fix CI failures, review feedback, and conflicts** (5 comments)
   Proposes a session-local command that polls a PR, distinguishes code fixes from infrastructure re-runs, and iterates until green. Resonates strongly given the CI failure volume above.

6. **[#15479](https://github.com/QwenLM/qwen-code/issues/15479) — Auto-memory bound to the startup directory breaks multi-workspace sessions** (4 comments, new)
   Multi-project solution folders cannot write memory to the correct per-project store; everything lands in the global store. Directly affects anyone using Qwen Code across a monorepo or solution folder.

7. **[#10879](https://github.com/QwenLM/qwen-code/issues/10879) — hk4 pinned as release host but still carries the shared `ecs-qwen` label** (4 comments, P1)
   Release validation jobs compete with ordinary PR CI on the same host, causing timing-sensitive sharded test failures. A long-lived (since Sept 3) infra defect with a precise root cause.

8. **[#13895](https://github.com/QwenLM/qwen-code/issues/13895) — `modelFallbacks` chain skipped when a 429 body carries an unrecognized `error.code`** (4 comments)
   The fallback chain never engages when a provider returns a rate-limit error with a non-standard code, because it is classified as a fail-fast client error. High practical impact for multi-provider failover setups.

9. **[#15445](https://github.com/QwenLM/qwen-code/issues/15445) — Web Shell fails to load in non-English browsers** (3 comments, need-retesting)
   `desktopRelay` i18n lookup has no fallback, throwing `TypeError: Cannot read properties of undefined (reading 'desktopRelay.trigger')` for any locale outside English/Chinese. Regressed in 0.26.0 and completely blocks the UI — a small code gap with outsized user impact.

10. **[#15470](https://github.com/QwenLM/qwen-code/issues/15470) — ci: main-push failures file no issues; reporter drops them as superseded** (3 comments)
    Ten main-push failures between 14:00Z and 17:30Z on 2026-10-24 produced **zero** issues, because the reporter waits on a slow hosted runner and then treats its own report as superseded. Undermines CI observability.

*Also worth watching:* [#15444](https://github.com/QwenLM/qwen-code/issues/15444) (reasoning tokens double-counted in cost display), [#15418](https://github.com/QwenLM/qwen-code/issues/15418) (managed automation scanner blocks its single thread per gate probe), [#15461](https://github.com/QwenLM/qwen-code/issues/15461) (release-host provisioning on the idle hz ARM64 pool).

---

## 4. Key PR Progress

1. **[#15319](https://github.com/QwenLM/qwen-code/pull/15319) — feat(web-shell): move no-workspace sessions beside workspaces**
   Promotes no-workspace sessions to their own peer group between Workspaces and Archived, with independently persisted collapse state. Fixes the case where collapsing Workspaces hid these sessions entirely.

2. **[#15386](https://github.com/QwenLM/qwen-code/pull/15386) — fix(cli): preserve checkpoint restore ownership and cold continuation**
   Restores Ink checkpoints through the real tool scheduler, pairs replay results with the original logical call, and rejects stale owners. Important for checkpoint/rewind reliability.

3. **[#15030](https://github.com/QwenLM/qwen-code/pull/15030) — feat(cli): raise workspace skill install limits**
   Splits the transport ceiling from the on-disk ceiling for ZIP, GitHub, and local-folder skill installs, replacing a single hardcoded 6 MiB / 6 MiB / 2 MiB-per-file / 128-file budget in place since July.

4. **[#15261](https://github.com/QwenLM/qwen-code/pull/15261) — feat(runtime-broker): add HTTP-backed external scratch workers**
   Adds an opt-in `external-scratch` Runtime provider that delegates session worker placement to an operator-run HTTP service, with deterministic request handles, attestation, and retry semantics. The most substantial runtime extensibility change in flight.

5. **[#12585](https://github.com/QwenLM/qwen-code/pull/12585) — fix(acp): persist embedded text resources for transcript replay**
   Persists bounded original ACP embedded text `resource` blocks with their owning user prompt and replays them as typed user chunks, exposing them separately from `resource_link` attachments in the daemon UI SDK.

6. **[#15453](https://github.com/QwenLM/qwen-code/pull/15453) — fix(core): respect read-only files and hard links in atomic writes**
   Atomic replace now checks destination writability before committing and preserves hard-link identity. A meaningful correctness/security fix for file-editing tools.

7. **[#15430](https://github.com/QwenLM/qwen-code/pull/15430) — fix(core): reject symlink escapes in memory imports**
   Canonicalizes both the imported path and allowed roots before containment checks, closing a path-traversal hole where a committed symlink could pull in files outside the project.

8. **[#12222](https://github.com/QwenLM/qwen-code/pull/12222) — feat(core,cli): add `toolParametersMandatory` opt-in for strict OpenAI-compatible servers**
   When enabled, zero-argument tools are sent with an explicit empty `"parameters": {"type":"object","properties":{}}` instead of omitting the field. Directly unblocks users of stricter OpenAI-compatible backends.

9. **[#15472](https://github.com/QwenLM/qwen-code/pull/15472) / [#15464](https://github.com/QwenLM/qwen-code/pull/15464) — fix: stop double-counting reasoning tokens in cost estimates**
   Two independent implementations of the same fix (from the issue author and a maintainer) that derive billable output from the aggregate total rather than always adding `candidates + thoughts`. Both preserve Gemini-style disjoint `thoughts` accounting.

10. **[#15485](https://github.com/QwenLM/qwen-code/pull/15485) — fix(ci): retry transient npm publishes in the release step**
    Retries recursive `npm publish` up to four times with 30/60/90 s backoff and revalidates registry metadata each attempt. Paired with [#15471](https://github.com/QwenLM/qwen-code/pull/15471) (file main-CI failures even when the successor run hasn't settled), this directly targets today's release/CI reliability theme.

*Also notable:* [#15433](https://github.com/QwenLM/qwen-code/pull/15433) (preserve OpenAI `cache_write_tokens` for OTel provenance), [#15190](https://github.com/QwenLM/qwen-code/pull/15190) (extend daemon `PATH` on macOS when Desktop is launched from Finder/Dock), [#15274](https://github.com/QwenLM/qwen-code/pull/15274) / [#15286](https://github.com/QwenLM/qwen-code/pull/15286) / [#15275](https://github.com/QwenLM/qwen-code/pull/15275) (narrow the self-hosted SDK Java Maven host lock to cache write-back only).

---

## 5. Hot Discussions

_No discussion data was provided for this period, so this section is omitted._

---

## 6. Feature Request Trends

- **CI/PR automation inside the agent** — `/autofix-pr` ([#15056](https://github.com/QwenLM/qwen-code/issues/15056)) plus the managed automation scanner throughput work ([#15418](https://github.com/QwenLM/qwen-code/issues/15418)) point toward letting Qwen Code own the full PR lifecycle: watch, diagnose, fix, retry infrastructure, resolve conflicts.
- **Multi-workspace / multi-project memory** — [#15479](https://github.com/QwenLM/qwen-code/issues/15479) requests git-root-keyed memory stores so solution folders spanning several projects stop polluting the global store.
- **Runtime and sandbox portability** — the Kubernetes tool runtime tracker ([#13395](https://github.com/QwenLM/qwen-code/issues/13395)) and external scratch workers ([#15261](https://github.com/QwenLM/qwen-code/pull/15261)) reflect demand for pluggable, operator-hosted execution backends.
- **OpenAI-compatible provider compatibility** — strict tool schema handling ([#12222](https://github.com/QwenLM/qwen-code/pull/12222)) and correct 429 classification for fallback chains ([#13895](https://github.com/QwenLM/qwen-code/issues/13895)) are recurring asks from users running non-Qwen endpoints.
- **Cost and token transparency** — reasoning-token accounting ([#15444](https://github.com/QwenLM/qwen-code/issues/15444)) and OpenAI cache-write token provenance ([#15433](https://github.com/QwenLM/qwen-code/pull/15433)) signal growing interest in accurate billing/telemetry.
- **Desktop/daemon polish** — daemon token persistence (shipped), macOS `PATH` inheritance ([#15190](https://github.com/QwenLM/qwen-code/pull/15190)), and Web Shell i18n fallback ([#15445](https://github.com/QwenLM/qwen-code/issues/15445)).

---

## 7. Developer Pain Points

- **CI capacity and reliability is the top friction point.** Runner contention ([#14859](https://github.com/QwenLM/qwen-code/issues/14859), [#10879](https://github.com/QwenLM/qwen-code/issues/10879), [#15461](https://github.com/QwenLM/qwen-code/issues/15461)), noisy Managed Agent gate failures ([#15446](https://github.com/QwenLM/qwen-code/issues/15446), [#14724](https://github.com/QwenLM/qwen-code/issues/14724), [#14784](https://github.com/QwenLM/qwen-code/issues/14784)), and a failure reporter that silently files nothing ([#15470](https://github.com/QwenLM/qwen-code/issues/15470)) combine into slow feedback loops and untracked regressions. Multiple fixes this cycle target the Maven host lock scope and publish retries specifically.
- **Session scaling limits.** A hardcoded 256 MiB transcript index cap plus quadratic snapshot growth ([#13113](https://github.com/QwenLM/qwen-code/issues/13113)) can permanently brick a long-running session — a hard failure mode with no recovery path.
- **Silent failure classification.** Errors that look like rate limits but carry vendor-specific codes are treated as permanent client errors, bypassing fallbacks ([#13895](https://github.com/QwenLM/qwen-code/issues/13895)). Users discover this only after a task dies mid-run.
- **Cost reporting inaccuracy.** Reasoning tokens counted twice for OpenAI-style providers ([#15444](https://github.com/QwenLM/qwen-code/issues/15444)) erodes trust in the cost display.
- **Last-mile UX gaps that fully block usage.** A missing i18n fallback makes the Web Shell unusable for every non-English/Chinese locale ([#15445](https://github.com/QwenLM/qwen-code/issues/15445)) — small in code, total in impact.
- **Deferred-review debt.** A recurring pattern of "follow-up" issues carrying non-blocking review suggestions ([#14973](https://github.com/QwenLM/qwen-code/issues/14973), [#13698](https://github.com/QwenLM/qwen-code/issues/13698), [#14625](https://github.com/QwenLM/qwen-code/issues/14625), [#12986](https://github.com/QwenLM/qwen-code/issues/12986)) shows review throughput outpacing merge-and-cleanup capacity.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*