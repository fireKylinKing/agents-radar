# AI CLI Tools Community Digest 2026-10-26

> Generated: 2026-10-26 00:37 UTC | Tools covered: 7

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

# Cross-Tool Comparison Report — AI CLI Tools, 2026-10-26

## 1. Ecosystem Overview

The AI CLI ecosystem is in a production-hardening phase: most tools shipped no stable release in the last 24 hours, and issue traffic is dominated by reliability, quota/accounting, permissions, MCP/plugin lifecycle, and cross-platform correctness rather than net-new features. PR velocity is concentrated in Gemini CLI, OpenCode, Pi, and Qwen Code, while Claude Code, Codex, and Copilot CLI show higher issue engagement with little or no PR throughput in this window. The common competitive front is no longer raw model access but trust: predictable cost, auditable agent behavior, long-session stability, and headless/protocol parity. Community data also shows increasing demand for scoped security controls and actionable diagnostics instead of blanket blocks or silent failures.

## 2. Activity Comparison

> Counts are 24-hour “updated” counts as disclosed in the digests, not total repository size. **N/A** means not disclosed or not applicable in the provided snapshot; it does not mean inactive.

| Tool | Issues updated | PRs updated | Discussions | Release status |
|---|---:|---:|---:|---|
| **Claude Code** | 50 (top 30 shown) | 1 (docs-only, closed) | N/A | No release |
| **OpenAI Codex** | 50 | 0 | 8 | No release or PR activity |
| **Gemini CLI** | N/A (10 hot issues profiled) | 10+ profiled | N/A | Nightly: `v0.67.0-nightly.20261025` |
| **GitHub Copilot CLI** | 24 | 0 | N/A | No release |
| **OpenCode** | N/A (10 hot issues profiled; >12 quota-related reports) | 10+ profiled | N/A | No release |
| **Pi** | 50 | 9 | 1 | No release |
| **Qwen Code** | N/A (10 hot issues profiled) | 10+ profiled | N/A | Nightly: `v0.26.1-nightly.20261025`; stable `v0.26.1` shipped but SDK/managed-runtime fan-out blocked |

## 3. Shared Feature Directions

- **Cost, quota, and rate-limit transparency.** Claude Code has headless-vs-interactive cost disparity and weekly-limit depletion concerns; Codex users request non-TUI usage inspection and more sustainable Plus usage; Copilot CLI requests ACP `usage_update` and machine-readable prompt-budget errors; OpenCode has a major free-quota reset incident; Pi reports silent cache-warming failure. The shared need is predictable, auditable accounting before spend occurs.

- **Sandbox, approval, and permission clarity.** Claude Code users ask for permission-gated password entry in test environments; Codex reports delegated tasks ignoring user sandbox defaults and granular policies rejecting operations without prompts; Gemini proposes OS-level sandboxing and safer destructive-command behavior; OpenCode requests workspace locks and auto-allowing read-only tools; Pi requests `inheritEnv: false` for MCP stdio servers. Users want scoped, enforceable controls—not blanket blocks.

- **MCP/plugin lifecycle hardening.** Codex needs reconnect/refresh, OAuth persistence, and tool rediscovery; Copilot CLI reports custom agents disabling deferred MCP loading and Entra-broker MCP auth failures; Pi reports transport-inconsistent message limits, OAuth hangs, and env inheritance; OpenCode is unifying GUI/TUI/server plugin surfaces; Gemini has extension-gallery discovery issues; Qwen Code needs MCP child stderr in diagnostics. The common requirement is a reliable, observable plugin runtime.

- **Silent failure and observability.** Gemini’s top issues include `MAX_TURNS` reported as `GOAL` success and generalist-agent hangs; OpenCode subagents return empty results to parents; Copilot `/rename` reports success without renaming; Pi cache warming and MCP OAuth fail silently; Qwen loop guards are advisory and retry reasons are opaque; Claude reports silently corrupted commands and dropped cross-session messages. Communities increasingly treat diagnostics as a product surface, not telemetry.

- **Cross-platform reliability.** Windows is the largest regression surface across Claude Code, Codex, Gemini, Copilot CLI, OpenCode, and Pi: path separators, MSIX installs, VS Code startup, Entra auth, page-size assumptions, and shell/pipeline compatibility. macOS and Linux also surface Wayland browser failures, tmux OSC 52 handling, iTerm2 image rendering, and ARM64 packaging issues. Cross-platform correctness is now table stakes.

- **Long-session and agent-runtime reliability.** Claude Code reports Remote Control drops and cross-session message loss; Codex reports premature turn completion and capacity errors; Gemini reports indefinite hangs; Copilot CLI reports session-lock timeouts and 37 GiB memory growth; OpenCode reports subagent data loss; Pi reports extension reloads poisoning Anthropic sessions; Qwen Code reports compaction token double-counting and loop-guard gaps. Multi-hour autonomous use is exposing runtime fragility.

- **Context, memory, and prompt efficiency.** Claude Code users want persistent memory and enforceable context injection; Gemini is evaluating AST-aware reads/search and file-based task state; Codex users want AGENTS.md diagnostics; OpenCode is optimizing long-session history loading and media blobs; Pi reports compaction file-list growth and cache-warming bugs; Qwen Code proposes threshold-triggered handover into fresh contexts. The frontier is deterministic context engineering, not just larger windows.

- **Headless/ACP/CI parity.** Copilot CLI has the clearest ACP parity demand; Claude Code’s headless cost gap matters for CI; Codex users want non-interactive limit inspection; Qwen Code needs better headless retry classification. Protocol consumers increasingly expect typed errors, usage reporting, and parity with interactive mode.

## 4. Differentiation Analysis

- **Claude Code** targets professional developers and teams using desktop/TUI workflows. Its focus is safety guardrails, shell-environment fidelity, plugin/skill authoring, and quota transparency. The community is highly engaged on permissions and cost accounting, but PR throughput is low in this snapshot.

- **OpenAI Codex** has the broadest desktop/IDE surface and the strongest Windows/Computer Use focus. Its technical direction emphasizes sandbox/approval policy, MCP/plugin lifecycle, and remote/cross-device workflows. The community is large and issue-driven, with severe platform-specific regressions and strong demand for rate-limit visibility.

- **Gemini CLI** is iterating fastest on agent-subsystem hardening: subagent observability, cancellation, remote-session isolation, AST-aware context, and OS sandboxing. It targets Gemini-ecosystem developers and extension authors, with nightly releases and a high volume of surgical correctness PRs.

- **GitHub Copilot CLI** is differentiated by enterprise auth, managed policy, and ACP/headless integration. Its target users are GitHub-centric teams and ACP clients such as Zed. The main risk is release quality: long-session memory/session-lock regressions and Entra auth fragility are undermining trust in the fast release cadence.

- **OpenCode** is an open-source, provider-agnostic desktop/TUI tool with a free tier. It targets individual developers and self-hosters, with heavy V2 performance work, plugin-surface unification, and media/attachment optimization. The free-quota incident is currently its dominant community and trust issue.

- **Pi** is terminal-first and extension/MCP-oriented, targeting power users and extension authors. Its engineering style is small, surgical fixes: Windows paths, UTF-8 chunking, symlinked agent dirs, provider-id parsing, and MCP transport consistency. It has high triage velocity but also visible duplicate/closed-as-no-action friction.

- **Qwen Code** is the most enterprise-runtime-oriented: managed sessions, Kubernetes harnesses, durable journals/snapshots, web-shell embedding, and release automation. Its target is managed/operator-grade deployments and embedders. The weak link is release fan-out and CI observability: stable `v0.26.1` shipped, but SDK and managed-runtime publication were blocked.

## 5. Community Momentum & Maturity

**Momentum:** Gemini CLI, OpenCode, Pi, and Qwen Code show the strongest visible iteration velocity, with 9–10+ PRs profiled each and active hardening work. Gemini and Qwen are shipping nightlies; OpenCode is deep in V2 performance and plugin architecture; Pi is closing small correctness fixes at high throughput.

**Engagement:** Claude Code and Codex show the highest issue engagement signals, including high comment counts and upvote clusters (e.g., Claude’s shell configurability request at 131 👍; Codex’s VS Code startup regression at 14 👍). Copilot CLI has lower issue volume but disproportionately severe regressions: session-lock timeouts and 37 GiB memory growth in long sessions.

**Maturity:** Claude Code, Codex, and Copilot CLI are the most established distribution surfaces, but all show production-trust gaps around quota economics, platform regressions, and long-session stability. Gemini CLI and Qwen Code are rapidly maturing but still show release/CI brittleness. OpenCode and Pi behave like fast-moving open-source projects: active contributors, quick fixes, and visible triage noise.

**Caveat:** The disclosed metrics are not normalized. Claude, Codex, and Pi report 50 updated issues; Copilot reports 24; Gemini, OpenCode, and Qwen do not disclose total updated issues. Comparisons should be read as directional, not as precise market-share measures.

## 6. Trend Signals

- **Trust is the new feature battleground.** Quota accounting, permission behavior, and silent failures now generate more community heat than new capabilities. Developers need to predict cost and agent behavior before execution.

- **Headless and protocol-first usage is growing.** ACP, JSON-RPC, SDKs, and CI pipelines are first-class consumers. Tools that treat headless mode as second-class will accumulate integration friction.

- **Security models are shifting from blocks to scoped overrides.** Users want permission-gated escape hatches, sandboxed execution, per-workspace policy, and MCP env isolation—not removal of guardrails.

- **Windows is the largest quality gap.** Installation, IDE startup, shell parsing, auth, and Computer Use failures recur across vendors. Cross-platform correctness is a competitive requirement, not a polish item.

- **MCP/plugin ecosystems are maturing into lifecycle problems.** Reconnect, OAuth cancellation, tool rediscovery, transport-consistent limits, and plugin-surface unification are the next infrastructure layer.

- **Context engineering is replacing raw context size.** AST-aware reads, compaction correctness, cache warming, prompt debuggability, and fresh-context handover are active design areas.

- **Release engineering is a bottleneck.** Six-hour SDK tests, nightly fan-out failures, and fast-cadence regressions are driving demand for stable channels and better release observability.

- **Diagnostics must be user-facing.** Machine-readable error codes, actionable MCP stderr, retry reasons, and explicit success/failure signals are increasingly expected as part of the developer experience.

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

**Data caveat:** The supplied extract shows PR comment counts as `undefined` and all PR 👍 as `0`. Therefore, the PR ranking below uses attention proxies—linked Issue discussion volume, recency, and severity—rather than a verified PR-comment count. All listed PRs are **OPEN**; no merged/draft state is indicated.

## 1. Top Skills Ranking

| Rank | Skill / PR | Functionality | Discussion highlights | Status |
|---|---|---|---|---|
| 1 | [skill-creator — PR #1298](https://github.com/anthropics/skills/pull/1298) | Meta-skill for creating and evaluating Claude Skills | Trigger-eval false misses, Windows subprocess-pipe handling, runtime failures; tied to active issues [#556](https://github.com/anthropics/skills/issues/556) (13 comments), [#202](https://github.com/anthropics/skills/issues/202) (8), [#1394](https://github.com/anthropics/skills/issues/1394) (4). Sibling fixes: #1681, #1997. | OPEN |
| 2 | [mcp-builder — PR #1742](https://github.com/anthropics/skills/pull/1742) | Builds and evaluates MCP servers; fixes `mcp>=2` imports and custom headers | `streamablehttp_client` rename in `mcp>=2`, header configuration via `create_mcp_http_client`; related evaluation failures in [#1390](https://github.com/anthropics/skills/issues/1390) (4 comments). | OPEN |
| 3 | [docx — PR #1792](https://github.com/anthropics/skills/pull/1792) | Word document processing and revision acceptance | LibreOffice timeout was reported as success; now verifies output lacks revision marks. Related docx integrity issue [#2018](https://github.com/anthropics/skills/issues/2018) (4 comments); orphaned-comments PR #1734. | OPEN |
| 4 | [webapp-testing — PR #1980](https://github.com/anthropics/skills/pull/1980) | Browser/web-app testing scripts and server orchestration | Removes `shell=True` from `with_server.py` to mitigate command injection (CWE-78); recent security-focused update on 2026-10-25. Companion fix #1976 for element discovery. | OPEN |
| 5 | [claude-api — PR #1730](https://github.com/anthropics/skills/pull/1730) | Claude API guidance, tool-use concepts, and academy references | Replaces three dead documentation URLs; related context-window issue [#1487](https://github.com/anthropics/skills/issues/1487) (4 comments) reports `claude-api` injecting ~156k tokens. | OPEN |
| 6 | [notion-spec-to-implementation + quantitative-resume-auditor — PR #1245](https://github.com/anthropics/skills/pull/1245) | Converts product/tech specs into Notion implementation tasks; audits quantitative resumes | Multi-skill submission; active update on 2026-10-15. Represents demand for domain workflow automation and document-output review. | OPEN |
| 7 | [proofcore-contract-auditor — PR #1771](https://github.com/anthropics/skills/pull/1771) | Static analysis for Solidity/Rust contracts plus TON-blockchain audit proof anchoring | New Web3/security-oriented Skill; narrow domain but high-stakes use case. | OPEN |
| 8 | [pyxel — PR #525](https://github.com/anthropics/skills/pull/525) | Retro game development in Python with headless input-driven runs and frame inspection | Long-running PR, recently updated 2026-10-23; indicates sustained interest in verifiable creative/game-dev Skills. | OPEN |

## 2. Community Demand Trends

- **Security, provenance, and governance:** The highest-comment Issue is [#492](https://github.com/anthropics/skills/issues/492) (46 comments) on community Skills impersonating the `anthropic/` namespace. Related demand appears in [#412](https://github.com/anthropics/skills/issues/412) (agent governance), [#1394](https://github.com/anthropics/skills/issues/1394) (eval-viewer XSS), and [#1487](https://github.com/anthropics/skills/issues/1487) (context exhaustion). Expected Skill direction: security analyzers, provenance checks, permission scoping, and safe distribution.
- **Reliable Skill evaluation and trigger testing:** [#556](https://github.com/anthropics/skills/issues/556) (13 comments) reports `run_eval.py` never triggering Skills; [#202](https://github.com/anthropics/skills/issues/202) (8) critiques `skill-creator` best practices; [#1390](https://github.com/anthropics/skills/issues/1390) (4) reports MCP evaluation scoring 0/N; [#2064](https://github.com/anthropics/skills/issues/2064) (4) flags weak “when to use” metadata. Demand: eval harnesses, trigger calibration, description linting, and quality gates.
- **Org-wide sharing and distribution:** [#228](https://github.com/anthropics/skills/issues/228) (17 comments, 8 👍) asks for shared Skill libraries and direct sharing links instead of manual `.skill` uploads. Demand: team-level Skill registries and installable shared collections.
- **Context and memory efficiency:** [#1329](https://github.com/anthropics/skills/issues/1329) (10 comments) proposes `compact-memory` symbolic notation; [#1487](https://github.com/anthropics/skills/issues/1487) (4) shows a single Skill can consume ~156k tokens. Demand: lazy loading, token budgets, and compact persistent state.
- **Document/XML integrity and Office automation:** [#2018](https://github.com/anthropics/skills/issues/2018) (4 comments) reports `docx` merge output with invalid XML; [#1868](https://github.com/anthropics/skills/issues/1868) (4) reports gradient interpolation bugs. Demand: robust docx/ODT/PDF validators and idempotent document transforms.
- **Reasoning and agent-quality pipelines:** [#1385](https://github.com/anthropics/skills/issues/1385) (6 comments) proposes pre-task calibration, adversarial review, and delivery verification. Demand: workflow-level quality gates rather than isolated content Skills.

## 3. High-Potential Pending Skills

These are recently updated, narrowly scoped, or clearly linked to open issues—signals that they may land sooner than broad new-Skill submissions.

- [PR #1980 — webapp-testing: remove `shell=True`](https://github.com/anthropics/skills/pull/1980) — security fix with clear CWE-78 rationale; updated 2026-10-25. **OPEN**
- [PR #1681 — skill-creator: direct `package_skill.py` execution](https://github.com/anthropics/skills/pull/1681) — fixes `ModuleNotFoundError` and outdated usage paths; updated 2026-10-24. **OPEN**
- [PR #1997 — skill-creator: YAML frontmatter scalar parsing](https://github.com/anthropics/skills/pull/1997) — narrow parser correctness fix; updated 2026-10-19. **OPEN**
- [PR #1977 — algorithmic-art: `wrapAround()` correctness](https://github.com/anthropics/skills/pull/1977) — small bug fix, fixes #1897; updated 2026-10-18. **OPEN**
- [PR #1976 — webapp-testing: textarea/select element discovery](https://github.com/anthropics/skills/pull/1976) — targeted bug fix, fixes #1891; updated 2026-10-18. **OPEN**
- [PR #1245 — notion-spec-to-implementation + quantitative-resume-auditor](https://github.com/anthropics/skills/pull/1245) — active multi-Skill submission; updated 2026-10-15. **OPEN**
- [PR #1742 — mcp-builder: `mcp>=2` import and headers](https://github.com/anthropics/skills/pull/1742) — compatibility fix tied to a specific upstream API change; updated 2026-10-08. **OPEN**

## 4. Skills Ecosystem Insight

The community’s most concentrated demand is not for more domain-specific Skills, but for **trustworthy, evaluable, shareable, and context-efficient Skill infrastructure**—especially security/provenance, reliable trigger evaluation, org distribution, and token-aware loading.

---

# Claude Code Community Digest — 2026-10-26

**Source:** [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code) · 0 releases · 50 issues updated (top 30 by comments shown) · 1 PR updated

---

## 1. Today's Highlights

No releases shipped in the last 24 hours, so community attention stayed on cost accounting and platform-level tooling bugs. The two cost-related threads — headless `claude -p` burning ~1.8× more of the 5-hour window per token than interactive mode (#97074, 50 👍) and a suspected weekly-limit depletion regression (#102667) — are converging into a broader "quota transparency" concern. Meanwhile the long-running shell configurability request (#7490, 131 👍) remains the single most-upvoted open enhancement in this batch, and the closed desktop terminal font request (#48805, 63 👍) shows UI polish requests do get resolved.

---

## 2. Releases

None in the last 24 hours.

---

## 3. Hot Issues

1. **[#7490](https://github.com/anthropics/claude-code/issues/7490) — Allow configuring which shell the Bash tool uses** · OPEN · 40 comments · 131 👍
   The highest-engagement item in this window. Users want to inherit their initial shell (or explicitly pick one) rather than having the Bash tool impose its own. High upvote count plus a `has repro` label makes this the strongest candidate for near-term roadmap attention.

2. **[#97074](https://github.com/anthropics/claude-code/issues/97074) — Headless `claude -p` (sdk-cli) uses ~1.8× more of the 5-hour window per token than interactive `cli`** · OPEN · 29 comments · 50 👍
   A reproducible cost discrepancy between entrypoints for the same workload. This matters because headless mode is exactly what CI and automation pipelines use, so the penalty scales silently across teams. Heavy comment volume suggests multiple users are independently confirming the ratio.

3. **[#78160](https://github.com/anthropics/claude-code/issues/78160) — Hard block on typing passwords breaks legitimate dev/test workflows** · OPEN · 18 comments · 25 👍
   Claude Code refuses to type credentials even for documented test accounts on localhost. The request is for a permission-gated opt-in rather than removing the guardrail — a nuanced security-vs-ergonomics tradeoff that the community is debating rather than simply downvoting.

4. **[#102667](https://github.com/anthropics/claude-code/issues/102667) — Possible usage accounting regression: weekly limit depletes much faster** · OPEN · 5 comments · 2 👍
   Anecdotal but consistent: a 5-hour window that used to consume ~10% of the weekly quota now consumes materially more. Paired with #97074, this is the strongest signal in today's data that billing/quota accounting is the top trust issue right now.

5. **[#100063](https://github.com/anthropics/claude-code/issues/100063) — Windows: Bash tool halves every backslash pair before Git Bash parses it** · OPEN · 4 comments · 2 👍 · `has repro`
   Affects heredocs and single-quoted strings; the PowerShell tool is unaffected. This is a correctness bug that silently corrupts commands rather than failing loudly — the worst class of bug for an agentic tool.

6. **[#104907](https://github.com/anthropics/claude-code/issues/104907) — VS Code: Cmd+C does nothing in `/btw` panel, Cmd+V dead in session-name field** · OPEN · 3 comments
   Menu-bar Edit actions still work, which points at keybinding interception rather than a broken clipboard. Fresh report (filed 10-24) on extension 2.1.309, already picking up confirmations.

7. **[#104410](https://github.com/anthropics/claude-code/issues/104410) — Remote Control sessions drop and don't self-recover; cross-session messages lost silently** · OPEN · 2 comments
   Reported across Windows, macOS, and WSL. Because cross-session messaging rides on Remote Control, silent message loss makes multi-session setups unreliable as a backbone — a reliability concern rather than a UI nit.

8. **[#104283](https://github.com/anthropics/claude-code/issues/104283) — Desktop Code tab: let users unpin the task-plan block** · OPEN · 2 comments · 2 👍
   The pinned todo block sits mid-screen and content scrolls behind it, which is painful on smaller displays. Small, well-scoped UX ask with a clear reproduction.

9. **[#104760](https://github.com/anthropics/claude-code/issues/104760) — Stale temporary leases pin orphaned plugin versions** · OPEN · 1 comment · `has repro`
   Dead-process lease files prevent orphan cleanup, so old plugin versions persist. The author carefully distinguishes it from #95420 — useful triage hygiene for the plugins area.

10. **[#48805](https://github.com/anthropics/claude-code/issues/48805) — Terminal font family setting in desktop app** · **CLOSED** · 14 comments · 63 👍
    Second-highest upvote count in the batch and now closed. Notable as evidence that accumulated UI-customization requests do eventually land, which is worth citing when triaging the newer desktop asks above.

---

## 4. Key PR Progress

Only **one** pull request was updated in the last 24 hours, so there is no meaningful PR pipeline to rank this cycle.

1. **[#29757](https://github.com/anthropics/claude-code/pull/29757) — docs(plugin-dev): clarify skill naming in skill-development guide** · CLOSED · by webwww123
   Docs-only change to the plugin developer guide: the frontmatter example now uses a lowercase hyphenated skill name, a metadata note states the skill name should match the kebab-case directory identifier, and a reviewer checklist item was added to catch mismatches. Closes #24556. Low risk, improves correctness of third-party skill authoring — closed rather than merged in this window.

---

## 5. Hot Discussions

No discussion data was provided for this period; section omitted.

---

## 6. Feature Request Trends

Distilled from the 30 visible issues:

- **Environment & shell control** — Configurable/inherited shell for the Bash tool (#7490) is the flagship; the underlying theme is users wanting Claude Code to operate inside *their* configured environment instead of a normalized one.
- **Cost and quota observability** — Two independent threads (#97074, #102667) ask, in effect, for the same thing: predictable, auditable accounting so teams can reason about spend before it happens.
- **Granular permissioning instead of blanket rules** — Password entry on owned dev environments (#78160) and the safety-classifier false positives surfacing in stale-closed reports (#89961, #89957, #89951) all request scoped, opt-in overrides rather than global blocks.
- **Desktop/TUI ergonomics** — Unpinning the task-plan block (#104283), terminal font family (#48805), and agent-view affordances (#84075, #82282, #82052) form a steady stream of small polish requests.
- **Memory that actually persists** — Auto-memory reliability (#75334, closed stale) and rules-style triggered injection for memory topic files (#78795) point at a desire for deterministic, inspectable context injection.
- **Agent observability** — Showing which model a background agent runs on (#82282) and timestamping session history (#84075) reflect background agents becoming production infrastructure.

---

## 7. Developer Pain Points

- **Quota economics feel opaque and possibly regressed.** The combination of a measurable headless-vs-interactive cost gap and a perceived faster weekly burn is the highest-severity trust issue in this window.
- **Platform-specific integration bugs persist.** Windows backslash mangling in the Bash tool (#100063), broken Cmd+C/Cmd+V in the VS Code extension (#104907), and the GNOME Wayland Quick Entry never opening (#104069) each break core interaction on a specific platform.
- **Safety guardrails produce false positives on legitimate work.** Password entry for own test environments, smart-home debugging, air-gapped lab sysadmin, and read-only MCP servers named `*-production` are all being blocked — and users are asking for permission-gated escape hatches, not removal.
- **Silent failures over loud ones.** Backslash halving, dropped cross-session messages, and stale plugin leases all fail without clear error surfaces, which erodes trust faster than hard failures.
- **Triage backlog is visible in the data.** A large share of updated issues are `stale`/`CLOSED`, including ones with real signal (#84981's 30-minute background-task SIGTERM, #75334's ineffective auto-memory). Users read stale-closure as "not being worked on."
- **Memory and context behavior remains hard to predict.** Reported as "memories are written but not acted on" — developers want enforceable constraints, not best-effort recall.

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

## OpenAI Codex Community Digest — 2026-10-26

### Today's Highlights
No releases or PRs landed in the last 24h; activity was issue-heavy, with 50 updated issues and 8 discussions. Windows and Computer Use reliability dominated, led by [#51524](https://github.com/openai/codex/issues/51524) browser URL detection, [#56691](https://github.com/openai/codex/issues/56691) VS Code extension startup failure, and [#53132](https://github.com/openai/codex/issues/53132) MSIX install failures. Recurring themes include sandbox/approval mismatches, MCP/plugin lifecycle fragility, and rate-limit transparency; discussions also flagged a bundled-marketplace staging-directory disk leak.

### Hot Issues
- [#51524](https://github.com/openai/codex/issues/51524) — **[Windows] Computer Use stops at `get_window_state`: cannot determine current browser URL** (39 comments, 3 👍). Blocks core browser automation on Windows; `get_window_state` stops the turn before clicks or typing. Highest-comment issue in the provided set.
- [#56691](https://github.com/openai/codex/issues/56691) — **Codex 26.1021.22027 fails to start UI in VS Code 1.143.1 on Windows** (30 comments, 14 👍). A high-impact IDE extension regression; strongest upvote signal in the dataset.
- [#53132](https://github.com/openai/codex/issues/53132) — **Windows 11 MSIX installation repeatedly fails with `0x80073CF6` / `0x80070005`** (15 comments). Install/upgrade blocker tied to stale packaged service registration.
- [#50737](https://github.com/openai/codex/issues/50737) — **Fresh delegated tasks use `workspace-write`/`auto_review` despite user sandbox defaults** (12 comments, 3 👍). Security and control concern: users cannot tell which setting actually governs delegated-task execution.
- [#50728](https://github.com/openai/codex/issues/50728) — **macOS Computer Use typing/paste duplication; Claude keyboard taps report 16–56s latency** (11 comments). Disrupts manual workflows and undermines trust in Computer Use.
- [#54956](https://github.com/openai/codex/issues/54956) — **Improve Codex Plus usage efficiency across all models and enable affordable experimentation** (10 comments). Reflects recurring concern over rate-limit economics and model experimentation costs.
- [#53177](https://github.com/openai/codex/issues/53177) — **ChatGPT Work Cloud `list_threads` is exposed with local execution but fails** (9 comments). Cloud/local execution boundary issue that breaks thread listing in Work Cloud.
- [#34946](https://github.com/openai/codex/issues/34946) — **Allow Scheduled tasks to target SSH-connected hosts and surface runs in the controlling app** (8 comments, 11 👍). Strong support for cross-machine automation and remote task orchestration.
- [#56874](https://github.com/openai/codex/issues/56874) — **`write_stdin` rejected by granular sandbox approval policy, blocking Sites publication** (4 comments). Approval-policy bug with no approval prompt, blocking publishing workflows.
- [#56951](https://github.com/openai/codex/issues/56951) — **Codex ends turns after review/checkpoint milestones while acknowledging unfinished work** (4 comments). Model/session reliability issue affecting long-running CLI tasks.

### Key PR Progress
No pull requests were updated in the last 24h (0 items), so there is no PR progress to summarize.

### Hot Discussions

**General**
- [#42049](https://github.com/openai/codex/discussions/42049) — **Codex bundled-marketplace refresh leaks staging directories and can consume tens of gigabytes** (3 comments, 4 👍). Reports 323 abandoned staging directories and ~31 GiB consumed on macOS; strong operational impact.
- [#56932](https://github.com/openai/codex/discussions/56932) — **Tracking 5h/weekly Codex usage outside the TUI** (0 comments, 1 👍). Workaround using `account/rateLimits/read` and rollout `token_count` history, connected to recurring limit-drop questions.

**Q&A**
- [#52835](https://github.com/openai/codex/discussions/52835) — **AI-assisted KPI management system: 4 months of development and still no working application** (5 comments, 1 👍). Practical advice thread on non-developer expectations and AI-assisted business app delivery.
- [#56069](https://github.com/openai/codex/discussions/56069) — **Is picture-in-picture supported for MCP Apps on macOS, and how to enable it?** (2 comments, 1 👍). Extensibility question for third-party MCP App UIs.
- [#55553](https://github.com/openai/codex/discussions/55553) — **Remote execution preflight `EACCES` on process executable link** (1 comment, 1 👍). Requests attribution and a support route for remote Linux execution failures.

**Show and tell**
- [#56176](https://github.com/openai/codex/discussions/56176) — **Agent Relay Check v0.1.3: repeatable before/after check for `AGENTS.md`** (4 comments, 1 👍). Practical verification tool for instruction-path freshness.
- [#56731](https://github.com/openai/codex/discussions/56731) — **Agoryx: local-first shared conversation for Codex and Claude Code** (2 comments, 1 👍). MIT-licensed GUI for multi-agent build-and-review loops.
- [#56856](https://github.com/openai/codex/discussions/56856) — **MarketMirror: test ads, prices, and sales pitches on simulated buyers from Codex** (1 comment, 1 👍). AGPL-3.0 plugin/MCP server for pre-spend marketing tests.

### Feature Request Trends
- **Computer Use and browser automation reliability**: URL/state detection, browser control, input duplication, and latency remain the top cross-platform concerns ([#51524](https://github.com/openai/codex/issues/51524), [#50728](https://github.com/openai/codex/issues/50728), [#56822](https://github.com/openai/codex/issues/56822), [#55529](https://github.com/openai/codex/issues/55529)).
- **Windows desktop/IDE installation and startup resilience**: MSIX installer failures, VS Code renderer timeouts, and extension startup regressions recur ([#53132](https://github.com/openai/codex/issues/53132), [#56691](https://github.com/openai/codex/issues/56691), [#56780](https://github.com/openai/codex/issues/56780)).
- **Sandbox and approval policy clarity**: users need predictable defaults and actionable approval behavior for delegated tasks, `write_stdin`, and `apply_patch` ([#50737](https://github.com/openai/codex/issues/50737), [#56874](https://github.com/openai/codex/issues/56874), [#54947](https://github.com/openai/codex/issues/54947)).
- **MCP/plugin lifecycle management**: reconnect/refresh, tool rediscovery, OAuth persistence, and unavailable-plugin messaging are frequently requested ([#56970](https://github.com/openai/codex/issues/56970), [#56985](https://github.com/openai/codex/issues/56985), [#53854](https://github.com/openai/codex/issues/53854), [#56987](https://github.com/openai/codex/issues/56987)).
- **Remote and cross-device workflows**: scheduled tasks on SSH hosts, remote thread rendering, and shared project threads are clear demand areas ([#34946](https://github.com/openai/codex/issues/34946), [#37913](https://github.com/openai/codex/issues/37913), [#56801](https://github.com/openai/codex/issues/56801)).
- **Usage/rate-limit observability and efficiency**: users want non-interactive limit inspection and more sustainable Plus usage ([#54956](https://github.com/openai/codex/issues/54956), [#56932](https://github.com/openai/codex/discussions/56932)).
- **Agent/session reliability**: premature turn completion, capacity errors, and unfinished long-running tasks are recurring complaints ([#56951](https://github.com/openai/codex/issues/56951), [#43864](https://github.com/openai/codex/issues/43864), [#55443](https://github.com/openai/codex/issues/55443)).
- **Extensibility and local-first collaboration**: `AGENTS.md` diagnostics, MCP App UI support, and multi-agent/shared-conversation tooling are gaining attention ([#56176](https://github.com/openai/codex/discussions/56176), [#56069](https://github.com/openai/codex/discussions/56069), [#56731](https://github.com/openai/codex/discussions/56731), [#56856](https://github.com/openai/codex/discussions/56856)).

### Developer Pain Points
- **Windows is the largest regression surface**: installs, IDE startup, Computer Use, browser availability, and file patching all have active Windows-specific blockers.
- **Computer Use remains flaky**: URL detection, input duplication, scrolling/control loss, and latency make automation unreliable across macOS and Windows.
- **Sandbox/approval behavior is opaque**: delegated tasks can ignore user defaults, and granular policies can silently reject operations without prompting.
- **MCP/plugin state is fragile**: tools disappear, OAuth flows duplicate, plugins return 404, and there is no simple in-chat reconnect/rediscovery path.
- **Diagnostics are insufficient**: reports repeatedly cite missing native diagnostics, unavailable reason codes, and hard-to-attribute remote execution failures.
- **Agents stop mid-task or hit capacity**: premature finalization and “selected model is at capacity” errors disrupt multi-step workflows.
- **Rate-limit visibility is weak**: users still rely on workarounds to understand 5h/weekly usage and why limits drop quickly.
- **Cross-device workflows are incomplete**: SSH-host scheduling, remote thread rendering, and shared connected-computer project threads remain feature gaps.

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-10-26

## Today's Highlights
Activity is dominated by a hardening sweep across the agent subsystem: the last 24h landed a cluster of fixes from a single contributor targeting background tool cancellation, remote-agent session isolation, log-tail truncation, and plan-file parsing. Meanwhile, the issue tracker's top slots remain occupied by long-running P1 agent-reliability workstreams — most notably subagents reporting false success after hitting `MAX_TURNS`, and the generalist agent hanging indefinitely. A secondary but persistent theme is cross-platform correctness, with Windows path/tilde resolution, shell-free linting, and Wayland browser-agent failures all receiving attention.

## Releases
- **v0.67.0-nightly.20261025.gcd1b45c2d** — automated nightly build; no curated release notes supplied. [Full changelog](https://github.com/google-gemini/gemini-cli/compare/v0.67.0-nightly.20261024.gcd1b45c2d...v0.67.0-nightly.20261025.gcd1b45c2d) against the prior nightly. No stable release in the last 24h.

## Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent recovery after MAX_TURNS reported as GOAL success (P1, 13 comments)** The single most-discussed issue today. `codebase_investigator` returns `status: "success"` / `Termination Reason: "GOAL"` despite its own output admitting it hit the turn cap before doing any analysis. This is a correctness-of-signal problem: orchestration logic and users both consume that status, so silent truncation is indistinguishable from success.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs (P1, 8 comments, 8 👍)** Highest-engagement bug by reactions. Any deferral to the generalist agent — even trivial operations like folder creation — can hang indefinitely; suppressing subagent delegation is the reported workaround. The reaction count signals broad reproducibility.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing & post-execution intent routing (P2, 9 comments)** A design-scale proposal arguing Gemini 3 is natively bash-oriented and should be allowed to chain POSIX tools freely, with safety enforced by an OS sandbox plus intent routing after execution rather than pre-execution tool gating.

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini does not use skills and sub-agents enough (P2, 7 comments)** Anecdotal but widely echoed: custom skills and subagents are only invoked when explicitly instructed. This undercuts the whole agent-extension story — capabilities exist but the model doesn't reach for them.

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — Assess AST-aware file reads, search, and mapping (P2, 7 comments)** An EPIC investigating whether AST-aware tooling reduces misaligned reads and token noise. Paired with [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) (mapping, evaluating `tilth`/`glyph`) and [#22747](https://github.com/google-gemini/gemini-cli/issues/22747) (AST-grep for search), this is the most concrete context-efficiency bet in flight.

6. **[#29791](https://github.com/google-gemini/gemini-cli/issues/29791) — Extension missing from gallery after crawl (P2, 6 comments)** `holt-os/holt` meets documented requirements and passes validation but is absent from `geminicli.com/extensions.json` (last modified 16 Oct). Points at crawl cadence/eligibility opacity in the extension distribution pipeline.

7. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides (P2, 4 comments)** `AgentRegistry` merges settings correctly at init, but the browser agent discards them — e.g. `maxTurns`. Configuration silently not applying is a trust issue for anyone tuning agent behavior.

8. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland (P1, 4 comments)** Fails while still emitting `Termination Reason: GOAL`, compounding the false-success problem in #22323. Linux/Wayland users are effectively locked out of browser automation.

9. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with >128 tools (P2, 3 comments)** Title says 128, body says 400 — either way, there's no scoping logic to keep the active toolset under model limits. Likely to bite harder as the extension ecosystem grows.

10. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should stop/discourage destructive behavior (P2, 3 comments)** Model reaches for `git reset` or `--force` when safer paths exist, and lacks guardrails around mutating DBs. A safety/steering issue, not just a quality one.

*Also worth watching:* [#22186](https://github.com/google-gemini/gemini-cli/issues/22186) (P1 crash on hook output), [#20079](https://github.com/google-gemini/gemini-cli/issues/20079) (symlinked agent files ignored), [#23571](https://github.com/google-gemini/gemini-cli/issues/23571) (model scatters temp scripts across the workspace).

## Key PR Progress

1. **[#29838](https://github.com/google-gemini/gemini-cli/pull/29838) [CLOSED] — Resolve relative paths against active IDE workspace root (P1)** Fixes `WriteFileTool`, `EditTool`, `ReadFileTool`, and `correctPath` to consult `ideContextStore` instead of always falling back to `config.getTargetDir()`. Important for multi-root/nested-Git IDE workflows; merged-and-closed within the window.

2. **[#29948](https://github.com/google-gemini/gemini-cli/pull/29948) — Respect cancellation during background output reads** Aborting a delayed invocation previously still let it complete and read the log. Reuses the abort-aware delay helper so cancellation is prompt.

3. **[#29946](https://github.com/google-gemini/gemini-cli/pull/29946) — Isolate remote conversations by parent session and endpoint** Stops remote-agent conversation IDs leaking across independent parent sessions, and fixes identifier reuse between same-named agents on different endpoints.

4. **[#29947](https://github.com/google-gemini/gemini-cli/pull/29947) — Preserve oversized background log tails** A 70,001-byte single-line log previously returned only `Full Log Output:` and dropped the entire tail. Now retains a partial fragment when no complete line fits the 64 KB window.

5. **[#29942](https://github.com/google-gemini/gemini-cli/pull/29942) — Scan complete plan content and report read failures** Fixes the 1 KB sample rejecting valid plans that begin with ≥1,024 spaces, and distinguishes unreadable plans from empty ones.

6. **[#29871](https://github.com/google-gemini/gemini-cli/pull/29871) — Keep unsent request when loop recovery starts before a turn** `_recoverFromLoop` was dropping the pending `functionResponse`, closing tool calls as "Response was lost or interrupted." A subtle but high-impact agent-loop correctness fix.

7. **[#29931](https://github.com/google-gemini/gemini-cli/pull/29931) — Expand Windows home-relative paths** `~\policies\custom.toml` and local extension registry paths now resolve under the configured home directory instead of keeping a literal tilde.

8. **[#29935](https://github.com/google-gemini/gemini-cli/pull/29935) — Preserve whitespace in `@file` references** Drops the extra `.trim()`, so `    print("hello")\n` no longer reaches the model as `print("hello")` — a quiet source of indentation and newline corruption.

9. **[#29940](https://github.com/google-gemini/gemini-cli/pull/29940) — Run YAML lint without Unix shell pipelines** Removes the `'grep' is not recognized` failure on native Windows and works around a `UnicodeDecodeError` under legacy Windows code pages.

10. **[#29929](https://github.com/google-gemini/gemini-cli/pull/29929) — Show accurate output limits in model list** Reports the documented 65,536 output-token limit for Gemini 2.5 Pro/Flash and stops emitting a misleading 8,192 for models with unknown limits.

*Also in the queue:* [#29912](https://github.com/google-gemini/gemini-cli/pull/29912) (strip trailing punctuation from `web_fetch` URLs), [#29933](https://github.com/google-gemini/gemini-cli/pull/29933) (correct MCP examples in README, clarify checkpointing vs. resume), [#26844](https://github.com/google-gemini/gemini-cli/pull/26844) (missing `CustomTheme` schema keys causing startup Zod errors), [#19013](https://github.com/google-gemini/gemini-cli/pull/19013) (Windows `EBUSY` on extension update), and a large P1 dependabot batch in [#29664](https://github.com/google-gemini/gemini-cli/pull/29664) (74 npm updates).

## Hot Discussions
No discussion data was provided for this window; this section is omitted.

## Feature Request Trends
- **Subagent observability and control.** A clear cluster wants agent internals surfaced and steerable: shareable subagent trajectories via `/chat share` ([#22598](https://github.com/google-gemini/gemini-cli/issues/22598)), subagent context included in `/bug` reports ([#21763](https://github.com/google-gemini/gemini-cli/issues/21763)), and backgroundable local agents via `Ctrl+B` ([#22741](https://github.com/google-gemini/gemini-cli/issues/22741)).
- **Context and token efficiency.** AST-aware read/search/mapping EPICs ([#22745](https://github.com/google-gemini/gemini-cli/issues/22745), [#22746](https://github.com/google-gemini/gemini-cli/issues/22746), [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)), "tactful extraction" surgical reads ([#19561](https://github.com/google-gemini/gemini-cli/issues/19561)), and replacing in-context `WriteToDo` with persistent file-based task CRUD ([#18836](https://github.com/google-gemini/gemini-cli/issues/18836)).
- **Proactive capability use and safer behavior.** The model should reach for skills/subagents unprompted ([#21968](https://github.com/google-gemini/gemini-cli/issues/21968)) while avoiding destructive git and DB operations ([#22672](https://github.com/google-gemini/gemini-cli/issues/22672)) and not littering the workspace with temp scripts ([#23571](https://github.com/google-gemini/gemini-cli/issues/23571)).
- **Security and sandboxing as a first-class layer.** Zero-dependency OS sandboxing with post-execution intent routing ([#19873](https://github.com/google-gemini/gemini-cli/issues/19873)) and per-workspace rather than global policy scoping ([#18397](https://github.com/google-gemini/gemini-cli/issues/18397)).
- **Agent resilience.** Automatic browser-session takeover and lock recovery ([#22232](https://github.com/google-gemini/gemini-cli/issues/22232)), plus smarter tool scoping under model limits ([#24246](https://github.com/google-gemini/gemini-cli/issues/24246)).
- **Terminal UX polish.** Flicker-free, high-performance resize behavior requiring a `Static` → `RenderStatic` migration ([#21924](https://github.com/google-gemini/gemini-cli/issues/21924)).

## Developer Pain Points
- **Silent failure is the dominant frustration.** The top three issues all describe the agent failing while reporting success or not reporting at all: `MAX_TURNS` masked as `GOAL` (#22323), the generalist agent hanging forever (#21409), and browser-agent failure under Wayland still emitting `GOAL` (#21983). Users cannot distinguish "done" from "gave up."
- **Configuration that doesn't take effect.** `settings.json` overrides being ignored by the browser agent (#22267) and symlinked agent files not being discovered (#20079) erode confidence in the config surface.
- **Windows remains a second-class platform.** In one 24h window: tilde paths not expanded (#29931), preflight failing on missing `grep` plus a code-page `UnicodeDecodeError` (#29940), and `EBUSY` during extension updates (#19013). Progress is being made, but the fixes are piecemeal.
- **Content fidelity bugs.** Whitespace stripping on `@file` references (#29935), naive `\n` escape handling (#22466), and punctuation-polluted `web_fetch` URLs (#29912) all corrupt input in ways that are hard to diagnose from the agent's output.
- **Tooling scale limits.** Hard failures once the active toolset exceeds model limits (#24246) will worsen as extensions proliferate — and the extension gallery's opaque crawl cadence (#29791) makes new extensions hard to discover in the first place.
- **Interactive prompts block automation.** The agent getting stuck on a Vite scaffolder's interactive prompt (#22465) is a recurring class of failure for any CLI that expects TTY input.

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — Community Digest (2026-10-26)

> **Data snapshot:** 24 issues updated in the last 24h · no new releases · no PR activity · no discussions provided.
> Sections for Releases, PR Progress, and Hot Discussions are omitted accordingly.

---

## 1. Today's Highlights

No new releases shipped in the last 24 hours, but the issue tracker is dominated by two severe **reliability and resource-consumption regressions in 1.0.103** — persistent session-lock timeouts (#5253) and a main process ballooning to 37 GiB (#5254) — both reported by the same user during long parallel-agent sessions. Alongside that, **ACP (Agent Client Protocol) parity** remains the loudest feature theme, with three separate requests covering usage reporting, session renaming, and mid-turn steering. Enterprise auth and managed-policy handling (#5236, #5250, #5109, #5130) also continues to generate steady friction across Windows and macOS.

---

## 2. Releases

None in the last 24h. Note that users are actively requesting a slower, **stable release channel** (#5203) precisely because of regressions like those below landing in the fast-moving line.

---

## 3. Hot Issues

1. **[#5253](https://github.com/github/copilot-cli/issues/5253) — `[triage]` CLI 1.0.103: persistent session-lock timeouts and unresponsive tasks** · 2 comments
   Long-lived sessions become largely unresponsive with `session lock unavailable during is_session_generation_current: acquisition timed out`. Tool activity continues in the background while the UI stalls — a hard blocker for anyone running multi-hour sessions. Fresh report, already in triage.

2. **[#5254](https://github.com/github/copilot-cli/issues/5254) — `[triage]` Main process grows to 37 GiB; 133M malloc allocations, 10.7 GiB fragmentation** · 0 comments
   A 6h19m resumed session on a 32 GiB Apple Silicon Mac exceeded physical RAM (37.3 GiB footprint) with system swap saturation. The allocation/fragmentation numbers make this a credible memory-management bug rather than user error, and it pairs suspiciously with #5253.

3. **[#4233](https://github.com/github/copilot-cli/issues/4233) — `[ACP]` Emit `usage_update` in `--acp` mode (context window + AI credits)** · 3 comments · 👍 4
   The most-upvoted issue in this window. The CLI already computes context/credit data for `/context` and `/usage`, but never surfaces it over ACP, so Zed and other ACP clients cannot render a usage indicator. Low-cost, high-visibility parity gap.

4. **[#5195](https://github.com/github/copilot-cli/issues/5195) — Selecting a custom agent disables deferred MCP tool loading after the first model call** · 1 comment
   With tool search enabled, MCP tools are correctly deferred for the default agent — but selecting a custom agent (`--agent` / `/agent`) loads every tool from every MCP server into context from the second call onward. A silent token-burn bug that directly undermines the deferred-loading feature.

5. **[#5236](https://github.com/github/copilot-cli/issues/5236) — Windows: Entra broker token cache stays 0 bytes after MCP sign-in** · 1 comment
   Every new process re-prompts for account selection; `/restart` in-process does not. Affects anyone using Entra-protected HTTP MCP servers on Windows — repeated interactive auth is a significant workflow tax in headless/automation contexts.

6. **[#5250](https://github.com/github/copilot-cli/issues/5250) — macOS: Entra-broker MCP servers fail at startup with concurrent sign-ins rejected** · 0 comments
   Two Entra-protected remote MCP servers (Azure DevOps remote MCP, WorkIQ) fail to connect at startup with "sign-in is already in progress," and a rejected cached token is not recovered. The macOS counterpart to #5236 — the native Entra broker path appears to lack concurrency handling on both platforms.

7. **[#5201](https://github.com/github/copilot-cli/issues/5201) — Prompt-budget rejection is not machine-identifiable** · 1 comment
   In headless JSON-RPC mode, exceeding the model prompt budget returns only `errorType: "query"` with no `errorCode` and no remediation hint. Automation authors cannot distinguish "compact and retry" from a genuine query failure — a real gap for CI and agent-orchestration use cases.

8. **[#5115](https://github.com/github/copilot-cli/issues/5115) — `[ACP]` `/rename` reports success but doesn't rename the session** · 2 comments
   The command is advertised via `available_commands_update`, returns `end_turn` in ~1 ms with a success reply, and makes no model call — yet the session name is unchanged. Silent no-op commands are especially damaging in protocol mode where clients trust the response.

9. **[#4977](https://github.com/github/copilot-cli/issues/4977) — Bundled ripgrep aborts with jemalloc "Unsupported system page size" on 16KB-page ARM64 (Asahi Linux)** · 2 comments
   The bundled `rg` binary hard-codes a 4 KB page-size assumption, aborting immediately on Asahi Linux and any 16 KB-page ARM64 kernel. A packaging-level fix (non-jemalloc build or runtime page-size detection) that currently makes the CLI unusable on those distros.

10. **[#5203](https://github.com/github/copilot-cli/issues/5203) — Feature Request: Stable Release Channel for Copilot CLI** · 2 comments
    Argues the rapid release cadence introduces regressions that break daily use, and proposes Stable/Beta channels. Gains relevance as the 1.0.103 issues (#5253, #5254) accumulate.

*Also worth watching:* [#5198](https://github.com/github/copilot-cli/issues/5198) (ACP mid-turn steering), [#5147](https://github.com/github/copilot-cli/issues/5147) (`/restart` fails with `--new-session-id`), [#4401](https://github.com/github/copilot-cli/issues/4401) (skill tool can't find `~/.agents/skills` on Windows), and [#5109](https://github.com/github/copilot-cli/issues/5109) / [#5130](https://github.com/github/copilot-cli/issues/5130) (constant "Managed account policy could not be refreshed" errors).

---

## 4. Key PR Progress

None — no pull requests were updated in the last 24h.

---

## 5. Hot Discussions

No discussion data was provided for this window.

---

## 6. Feature Request Trends

- **ACP / headless parity is the top feature direction (3 of 24 issues).** Requests consistently frame themselves as "parity with the interactive CLI or SDK mode": usage/context reporting (#4233), working session commands (#5115), mid-turn steering (#5198), and machine-readable error codes for headless JSON-RPC (#5201). ACP clients (Zed et al.) are clearly a growing integration surface that is currently second-class.
- **Token/context economy.** Two related asks target the ~40k tokens of fixed injected context per session (#5251, with a duplicate closed as #5252), plus the deferred-MCP-loading regression (#5195). Users increasingly treat context budget as a first-class cost.
- **Release engineering / stability.** A stable release channel (#5203) is the clearest process-level request, implicitly reinforced by the 1.0.103 regression reports.
- **Configurability of model-adjacent defaults.** Voice transcription language is hard-coded to `"auto"` (#5226), forcing non-English speakers to re-speak their first sentence every session — a small but concrete example of a broader "make hard-coded defaults configurable" theme.
- **Cross-platform packaging correctness.** ARM64 page-size support (#4977) and Windows skill/agent discovery (#4401, #3815) reflect a recurring pattern of Linux/macOS-first assumptions leaking into platform-specific code paths.

---

## 7. Developer Pain Points

- **Long-session stability is the sharpest pain right now.** Session-lock acquisition timeouts (#5253), 37 GiB memory growth with heavy allocator fragmentation (#5254), and unresponsive tasks during long parallel-agent runs undermine the core value proposition of an agentic CLI that is expected to run for hours.
- **`/restart` is a recurring fault line.** It fails with `--new-session-id` (#5147), historically failed with `-w` worktrees (#4493, now closed), and does not clear Entra auth state (#5236). Recovery from a bad session is itself unreliable.
- **Enterprise managed-policy noise.** "Managed account policy could not be refreshed" appears repeatedly and continuously (#5109, #5130) with retry loops, in some cases rendering the CLI effectively unusable for managed accounts.
- **Entra broker authentication is fragile on both desktop platforms.** Empty token caches on Windows (#5236) and rejected concurrent sign-ins on macOS (#5250) mean repeated interactive prompts and failed MCP startups — painful in scripted or multi-server setups.
- **Silent failures over protocol boundaries.** `/rename` returning success without renaming (#5115) and prompt-budget errors lacking an error code (#5201) both force integrators to guess. Protocol consumers need explicit, typed failure signals.
- **Perceived release-quality erosion.** Multiple reports cite regressions "since at least 1.0.94" and "the rapid release cycle tends to introduce regressions," driving the demand for a stable channel and more conservative rollout of changes to core session/auth paths.

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-10-26

## 1. Today's Highlights

No new release landed in the last 24 hours. The dominant theme is a free-tier quota incident: more than a dozen issues report that the daily free-usage allowance either never resets or restarts its countdown after hitting zero, with several reporters linking it to per-IP accounting (and to the still-open VPN-rotation exploit report). Meanwhile, core engineering activity is heavy: V2 reliability and performance PRs (empty-output turn retries, provider connection warming, long-session history loading, off-main-thread image resizing) alongside a new experimental media-blob API that stops re-sending screenshots as base64.

---

## 2. Hot Issues

1. **[#34344](https://github.com/anomalyco/opencode/issues/34344) — Unlimited usage exploit on free models (CLOSED, 56 comments)**  
   The most-discussed issue in the window: free-model rate limits are bound to IP, so rotating VPN endpoints resets the quota indefinitely. The author automated rotation and ran DeepSeek v4 Flash and Mimo v2.5 continuously. This is the structural root of today's complaint cluster — per-IP limiting is both bypassable and unfair to shared/NAT addresses.

2. **[#58781](https://github.com/anomalyco/opencode/issues/58781) — "Free Usage Exceeded" on all free models in Desktop 1.19.4 (13 comments, 👍9)**  
   Highest-reaction issue: all free models stopped working at once with a "Subscribe to Go" prompt. The 9 upvotes and 13 comments make it the community's canonical thread for the incident; tagged `pending close, triaging`.

3. **[#58831](https://github.com/anomalyco/opencode/issues/58831) — Free quota timeout never recovers, sawtooths at 86400s (6 comments, 👍2)**  
   The clearest reproduction of the bug: the countdown hits zero and immediately jumps back to 86400s. Also requests a humanized `hh:mm:ss` timer — a small UX complaint bundled with a real state-machine defect.

4. **[#59038](https://github.com/anomalyco/opencode/issues/59038) — Countdown restarts after reaching zero (4 comments)**  
   Independent confirmation of the same reset-loop on a different account/region, counted down from 22947s. Cross-report corroboration is what turns this from user error into a server-side bug.

5. **[#58933](https://github.com/anomalyco/opencode/issues/58933) — 59,769-second cooldown on Windows 11 (5 comments, 👍1)**  
   Shows the cooldown duration is wildly inconsistent (16.6h here vs. 24h elsewhere), suggesting the allowance window is being computed from inconsistent anchors.

6. **[#56057](https://github.com/anomalyco/opencode/issues/56057) — Desktop file tree unusable out of the box (4 comments, updated today)**  
   `showFileTree` defaults to `false` and no Settings UI exists to flip it; the setter has zero call sites. A desktop 2.0.30 regression that makes a core navigation surface unreachable without editing config.

7. **[#59051](https://github.com/anomalyco/opencode/issues/59051) — Subagent returns empty result to parent on v2.1.4 (3 comments)**  
   Child session reports `idle_outcome: succeeded` with real token usage and cost, but the parent receives "Subagent completed without a text response." Silent data loss for anyone orchestrating subagents — one of the more serious V2 correctness reports.

8. **[#57603](https://github.com/anomalyco/opencode/issues/57603) — Delegated subagent write silently denied on `ask` permission (CLOSED, 5 comments)**  
   A permission set to `ask` produces no prompt anywhere in Desktop, so the write fails with no approval path. Worked on v1, broken on v2 — a security-adjacent UX regression where the safe default becomes a dead end.

9. **[#39986](https://github.com/anomalyco/opencode/issues/39986) — Support reactive external TSX plugins in packaged CLI (7 comments)**  
   The packaged Bun CLI fails to apply OpenTUI's runtime Solid transform to external TSX plugins, and can load duplicate `solid-js`/`@opentui/solid` copies — initial frame renders, signals don't update. Blocks the third-party TUI plugin ecosystem.

10. **[#53623](https://github.com/anomalyco/opencode/issues/53623) — "Code Mode" instructions confuse Gemma-4-31B (6 comments, 👍2)**  
    Smaller models over-apply the `execute` wrapper on every tool call. The real ask is a way to inspect and override prompts — prompt debuggability as a first-class feature.

---

## 3. Key PR Progress

1. **[#56673](https://github.com/anomalyco/opencode/pull/56673) — Retry, then fail, turns that finish with no output**  
   Addresses Gemini-style SSE streams that close with `finishReason: STOP`/`MAX_TOKENS` and zero content. Directly relevant to the silent-empty-result class of bugs (#59051).

2. **[#58803](https://github.com/anomalyco/opencode/pull/58803) — Warm the model provider connection at prompt admission**  
   Opens DNS/TCP/TLS while the prompt is admitted and context loads, including the provider you switch to. Cuts first-token latency after idle periods.

3. **[#59103](https://github.com/anomalyco/opencode/pull/59103) — Experimental `GET /api/experimental/media/:hash` + `experimental-media-uri` capability**  
   Lets clients fetch attachment bytes on demand instead of re-reading inline base64 every session load. Opt-in on both ends (`experimental.media_blob` off by default), with #59104 wiring Desktop/web to request `media:<hash>` refs.

4. **[#59091](https://github.com/anomalyco/opencode/pull/59091) — Load a step's history once**  
   Removes per-step work that scales with session length: prompt sizing was done 2–4×, base64 measured by rescanning, and every `read` hitting an `AGENTS.md` decoded the full transcript.

5. **[#58820](https://github.com/anomalyco/opencode/pull/58820) — Resize images in a worker; release photon memory when idle**  
   Large-image resizes were blocking the server 430–480 ms each and pinning ~250 MB of WASM memory for process lifetime. Now one shared worker per process that shuts down after idle.

6. **[#55246](https://github.com/anomalyco/opencode/pull/55246) — Ship GUI plugins through `./gui-experimental` exports**  
   A plugin in `opencode.json` can ship a UI part beside its server and `./tui` entries; the desktop app's built-in features become built-in plugins. The biggest architectural step toward a unified plugin surface.

7. **[#59072](https://github.com/anomalyco/opencode/pull/59072) — Nest experimental endpoints under `client.experimental`**  
   Makes SDK stability legible: `client.experimental.session.stats()` instead of sitting next to `client.session.get()` in the generated Promise and Effect clients.

8. **[#59102](https://github.com/anomalyco/opencode/pull/59102) — Publish MCP tool progress notifications**  
   `McpClient.callTool` now surfaces server-side progress. Core half only — rendering on the TUI tool card is deliberately split out.

9. **[#58297](https://github.com/anomalyco/opencode/pull/58297) — Port the GitHub integration to v2**  
   Brings `opencode github install` / `opencode github run` forward from v1 (closes #57911), a missing piece for teams running review automation on v2.

10. **[#59046](https://github.com/anomalyco/opencode/pull/59046) — Separate release builds from signing and publishing**  
    Splits `.github/workflows/release.yml`, scopes all secret-using jobs to the `release` environment, tightens permissions, and drops the VS Code publish workflow. Supply-chain hygiene for a project shipping signed binaries.

Also worth noting: **[#58531](https://github.com/anomalyco/opencode/pull/58531)** (Bedrock endpoint region now resolved from provider settings — closes #49391), **[#59105](https://github.com/anomalyco/opencode/pull/59105)** (retry service-file renames on Windows to stop concurrent-write corruption), and **[#59107](https://github.com/anomalyco/opencode/pull/59107)** (list OpenCode Console before Go in the TUI connect dialog).

---

## 4. Feature Request Trends

- **Free-tier transparency and accounting.** [#57540](https://github.com/anomalyco/opencode/issues/57540) asks for documented semantics for idle time, pool scope, and remaining allowance — the single most-referenced gap behind today's incident. [#58831](https://github.com/anomalyco/opencode/issues/58831) adds humanized countdowns to the same ask.
- **Permission-system ergonomics.** [#27076](https://github.com/anomalyco/opencode/issues/27076) proposes auto-allowing read-only tools (`read`, `glob`, `grep`, `todowrite`); [#58241](https://github.com/anomalyco/opencode/issues/58241) requests a workspace lock so OpenCode cannot modify files. Both point at approval fatigue without weakening write safety.
- **Desktop UI configurability.** The file-tree toggle has both a bug report ([#56057](https://github.com/anomalyco/opencode/issues/56057), [#59047](https://github.com/anomalyco/opencode/issues/59047)) and an adjacent sidebar request ([#58797](https://github.com/anomalyco/opencode/issues/58797)) — panel visibility and layout are not user-controllable.
- **Prompt observability.** [#53623](https://github.com/anomalyco/opencode/issues/53623) asks for a way to debug and modify built-in prompts, aligning with the "Code Mode" confusion on smaller models.
- **Alternative payments.** [#58826](https://github.com/anomalyco/opencode/issues/58826) requests USDC for Go subscriptions, noting all four current methods (card, Link, Alipay, UPI) route through traditional rails.
- **Plugin surface unification.** External reactive TSX plugins ([#39986](https://github.com/anomalyco/opencode/issues/39986)) and GUI plugin exports (#55246) are converging on one plugin model across TUI, server, and desktop.

---

## 5. Developer Pain Points

- **The free-quota state machine is broken, and it dominates the tracker.** Roughly a dozen reports in 24h describe the same failure: the cooldown hits zero and restarts (#58831, #59038), never resets across days (#58814, #58830), or fires before any prompt is sent (#58827). Durations vary from 22,947s to 86,400s (#58933), which reads as inconsistent window anchoring rather than a single stale counter. Most are already labeled `pending close, triaging`.
- **Per-IP limiting is the wrong axis.** It penalizes shared fiber, CGNAT, and campus addresses ([#58805](https://github.com/anomalyco/opencode/issues/58805)) while being trivially bypassed by VPN rotation ([#34344](https://github.com/anomalyco/opencode/issues/34344)). The two reports are the same design flaw viewed from opposite ends.
- **Silent failures are the worst failure mode.** Subagents report success while returning nothing to the parent (#59051); `ask`-permission writes are denied with no prompt shown anywhere (#57603); plugin-registered agents are absent from `GET /agent` right after server start (#51282). Each leaves the developer with no signal about what went wrong.
- **V2 parity gaps still bite.** Shell output bypassing the 50 KiB tool limit and stranding sessions ([#45099](https://github.com/anomalyco/opencode/issues/45099)), the GitHub integration only now being ported (#58297), and restart semantics for foreground/background shells still undefined ([#36348](https://github.com/anomalyco/opencode/issues/36348)).
- **Long-session and large-attachment performance.** History re-sizing, repeated base64 scans, and full-transcript decoding in the `read` path (#59091) plus main-thread image resizes blocking 430–480 ms (#58820) are the acknowledged hot spots; the media-blob line of work (#59103/#59104/#58742) is the structural fix for attachment weight.
- **Platform papercuts.** macOS TUI clipboard races native and OSC 52 writes simultaneously ([#59052](https://github.com/anomalyco/opencode/issues/59052)); Windows service config writes shared a temp file and could corrupt it (#59105).

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-10-26

**Repo:** [earendil-works/pi](https://github.com/earendil-works/pi) · **Activity window:** last 24h · **Issues updated:** 50 · **PRs updated:** 9 · **Discussions updated:** 1 · **Releases:** none

---

## 1. Today's Highlights

No release landed in the last 24 hours, but triage velocity stayed high: ~50 issues and 9 PRs were touched, with most new PRs being small, surgical correctness fixes (Windows path separators, symlinked agent dirs, split UTF-8 in `pi.exec`, `--model` references whose provider id contains `/`). The dominant theme is **MCP maturity and hardening** — four separate MCP issues are live (size-limit bypass, env inheritance, OAuth hangs, elicitation support), alongside two compaction/caching correctness bugs that silently degrade long sessions. On the provider side, a `prio:p0` Anthropic report shows that an extension reload removing a tool can poison every subsequent request in the session.

---

## 2. Releases

None in the last 24 hours. *(Omitted per digest policy — latest shipped version referenced in issues is 1.4.0.)*

---

## 3. Hot Issues

1. **[#10956](https://github.com/earendil-works/pi/issues/10956) — `cacheWarming: "idle"` silently stops firing** · OPEN · 10 comments · 👍2
   Idle warm never triggers once an extension handles `turn_end` and the context holds a custom/compaction message; 507 `cache_warming_decision` events all at `continuationProbability: 1`, zero at 0.15. Silent degradation with direct cost/latency impact — the highest-engagement issue of the day.

2. **[#10628](https://github.com/earendil-works/pi/issues/10628) — OSC 52 copy swallowed by tmux** · OPEN · 10 comments · 👍5
   Copy-on-select emits raw OSC 52, which tmux intercepts into its paste buffer instead of relaying to the client — yet Pi still flashes "Copied!". Most-reacted issue in the window; hits every headless/SSH-over-tmux user.

3. **[#11376](https://github.com/earendil-works/pi/issues/11376) — `[prio:p0]` Removing an initial tool mid-session breaks all later Anthropic requests** · OPEN · 3 comments
   An extension reload that stops registering tools yields `400 invalid_request_error: tool_reference_unresolved` on every subsequent call to `anthropic/claude-opus-5-5`. Session-fatal, and the only P0 in the window.

4. **[#10885](https://github.com/earendil-works/pi/issues/10885) — llama.cpp context overflow not detected → auto-compaction never fires** · OPEN · 5 comments · 👍1
   llama-server's `"Context size has been exceeded."` error isn't recognized, so compaction never kicks in with a 262k-context model. Illustrates that overflow detection is provider-string-dependent rather than structural.

5. **[#11335](https://github.com/earendil-works/pi/issues/11335) — MCP HTTP JSON responses bypass `maxMessageBytes`** · OPEN · 4 comments
   A 2,048-byte tool description is accepted over the JSON path while identical JSON-RPC content over SSE is rejected. Inconsistent enforcement of a safety limit between transports.

6. **[#11297](https://github.com/earendil-works/pi/issues/11297) — Compaction file lists grow without bound** · OPEN · 3 comments
   `extractFileOperations` seeds each compaction from the previous one's `readFiles`/`modifiedFiles`, so `<read-files>`/`<modified-files>` accumulate indefinitely — context bloat that compounds in long sessions.

7. **[#11311](https://github.com/earendil-works/pi/issues/11311) — MCP OAuth hangs on "Connecting…", cancellation does nothing** · OPEN · 3 comments
   Successful browser auth is followed by a 2–3 minute stall; `Ctrl+C` is unresponsive. Auth flows with no cancel path are a hard blocker for MCP adoption.

8. **[#11377](https://github.com/earendil-works/pi/issues/11377) — `stripAnsi` leaves private-parameter CSI payloads in output** · OPEN · 3 comments
   `<`, `=`, `>` are treated as CSI final bytes, so mouse reports and `ESC[>…c` replies leak parameter text into tool output/model context. Only 3 comments but a clean, high-value correctness fix.

9. **[#11361](https://github.com/earendil-works/pi/issues/11361) — Expose `inheritEnv: false` for MCP stdio servers** · OPEN · 2 comments
   Native MCP stdio servers currently inherit the full Pi process environment even when `env` declares a tight allowlist. Opt-in isolation request with clear security rationale.

10. **[#11375](https://github.com/earendil-works/pi/issues/11375) — `-e npm:` packages never update** · OPEN · 2 comments
    `pi -e npm:pi-web-access` caches the first download forever with no refresh path — contradicts the documented "temp use" semantics of `-e`.

*Also worth watching:* [#9534](https://github.com/earendil-works/pi/issues/9534) OpenRouter dropping signature-only Claude reasoning chunks (open since Sep 12), [#11331](https://github.com/earendil-works/pi/issues/11331) `tool_search` failing on exact tool names, [#11409](https://github.com/earendil-works/pi/issues/11409) `pi update --extensions` printing unactionable npm audit output.

---

## 4. Key PR Progress

All 9 PRs updated in the window are listed — most closed same-day.

1. **[#11411](https://github.com/earendil-works/pi/pull/11411) — fix(tui): keep iTerm2 inline images in fullscreen** · CLOSED
   Stops disabling the iTerm2 image capability while the alt screen is active; tool-result image blocks were falling back to `[Image: …]` text in fullscreen.

2. **[#11397](https://github.com/earendil-works/pi/pull/11397) — fix: resolve `provider/id` when the provider id contains a slash** · CLOSED
   `splitProviderPrefix()` split on the first `/`, breaking URL-derived ids like `llama-server=http://host:10800`. (Duplicate of contributor PR #11393 — see pain points.)

3. **[#11393](https://github.com/earendil-works/pi/pull/11393) — fix(coding-agent): same `/`-in-provider-id resolution** · CLOSED
   Community-authored fix for [#11394](https://github.com/earendil-works/pi/issues/11394); landed alongside the bot-authored #11397.

4. **[#11396](https://github.com/earendil-works/pi/pull/11396) — fix: pass real path as npm prefix when agent dir is a symlink** · CLOSED
   `~/.pi` symlinked into a dotfiles repo caused npm to write `../../../dotfiles/…` lock entries. Common dotfiles setup, clean fix.

5. **[#11399](https://github.com/earendil-works/pi/pull/11399) — fix: normalize path separators in `check-entry-graphs` on Windows** · CLOSED
   `path.relative()` returns `\` on Windows while allow/forbid patterns use `/`, so the `packages/telemetry/src/index.ts` allow rule never matched.

6. **[#11395](https://github.com/earendil-works/pi/pull/11395) — fix: unset all provider env vars in `pi-test.sh --no-env`** · CLOSED
   The hand-maintained `unset` list had drifted from `env-api-keys.ts`; `DEEPSEEK_API_KEY`, `MOONSHOT_API_KEY`, `ANTHROPIC_AUTH_TOKEN` still leaked into "no-env" test runs.

7. **[#11330](https://github.com/earendil-works/pi/pull/11330) — fix(coding-agent): preserve split UTF-8 output in `pi.exec`** · CLOSED
   Per-chunk `Buffer.toString()` corrupted characters spanning chunk boundaries (`你好` → `���好`). Now uses separate `StringDecoder`s for stdout/stderr.

8. **[#10718](https://github.com/earendil-works/pi/pull/10718) — fix(coding-agent): include system prompt in `--export` HTML** · OPEN
   CLI `--export` diverged from interactive `/export`. Open since Oct 9 — the only non-bot PR still awaiting merge.

9. **[#11408](https://github.com/earendil-works/pi/pull/11408) — docs: add source-code learning path (zh)** · CLOSED
   Chinese-language contribution guide for reading the codebase; signals growing non-English contributor interest.

---

## 5. Hot Discussions

**Q&A**
- **[#11404](https://github.com/earendil-works/pi/discussions/11404) — Official Pi code-agent sandbox by Docker?** · 👍1 · 0 comments
  Asks whether the four `sbx/pi*` images on Docker Hub (`pi`, `pi-mixin`, `pi-image`, `pi-kit`) are official or community-built. Unanswered — a good candidate for an official statement on supported sandboxing/containerization paths.

---

## 6. Feature Request Trends

- **MCP as a first-class, hardened client surface.** Requests cluster around parity and safety: transport-consistent `maxMessageBytes` ([#11335](https://github.com/earendil-works/pi/issues/11335)), opt-in env isolation via `inheritEnv: false` ([#11361](https://github.com/earendil-works/pi/issues/11361)), working OAuth with cancellation ([#11311](https://github.com/earendil-works/pi/issues/11311)), and elicitation support (closed as no-action, [#10274](https://github.com/earendil-works/pi/issues/10274)).
- **Provider-agnostic normalization layer.** Recurring asks to coerce and normalize non-conforming provider behavior: stringified JSON array/null tool args ([#11389](https://github.com/earendil-works/pi/issues/11389)), `developer` role on Anthropic-compatible endpoints ([#11365](https://github.com/earendil-works/pi/issues/11365)), Bedrock `reasoning_effort` for GLM-5.3 ([#11354](https://github.com/earendil-works/pi/issues/11354)), signature-only reasoning chunks ([#9534](https://github.com/earendil-works/pi/issues/9534)).
- **Deterministic compaction and cache behavior.** Unbounded file-list growth ([#11297](https://github.com/earendil-works/pi/issues/11297)), provider-string-dependent overflow detection ([#10885](https://github.com/earendil-works/pi/issues/10885)), idle cache warming silently disabled ([#10956](https://github.com/earendil-works/pi/issues/10956)).
- **Extension API guidance and hygiene.** Canonical patterns for persistent preferences ([#11410](https://github.com/earendil-works/pi/issues/11410)), avoiding crashes on non-prompt events ([#11366](https://github.com/earendil-works/pi/issues/11366)), lazy-compiling typebox validators ([#11367](https://github.com/earendil-works/pi/issues/11367)), correct display names for local-path packages ([#11392](https://github.com/earendil-works/pi/issues/11392)).
- **Terminal-emulator correctness as an explicit contract.** tmux OSC 52 passthrough ([#10628](https://github.com/earendil-works/pi/issues/10628)), private-parameter CSI in `stripAnsi` ([#11377](https://github.com/earendil-works/pi/issues/11377)), OSC 133 vs OSC 7501 conflicts ([#11017](https://github.com/earendil-works/pi/issues/11017)), iTerm2 fullscreen images ([#11411](https://github.com/earendil-works/pi/pull/11411)).
- **Sandboxed / containerized agent execution** — Docker Hub `sbx/pi*` discussion ([#11404](https://github.com/earendil-works/pi/discussions/11404)) plus `inheritEnv` ([#11361](https://github.com/earendil-works/pi/issues/11361)) point at demand for a documented isolation story.

---

## 7. Developer Pain Points

- **Silent failures with no diagnostic surface.** Idle cache warming stops without a warning ([#10956](https://github.com/earendil-works/pi/issues/10956)); MCP OAuth stalls for minutes with `Ctrl+C` ignored ([#11311](https://github.com/earendil-works/pi/issues/11311)); `-e npm:` reuses stale caches forever ([#11375](https://github.com/earendil-works/pi/issues/11375)). Users discover these only by inspecting internal event logs.
- **Cross-platform and filesystem edge cases dominate bug volume.** Windows path separators ([#11399](https://github.com/earendil-works/pi/pull/11399)), symlinked agent dirs ([#11396](https://github.com/earendil-works/pi/pull/11396)), standalone-CR grep offsets ([#11398](https://github.com/earendil-works/pi/issues/11398)), split UTF-8 at chunk boundaries ([#11330](https://github.com/earendil-works/pi/pull/11330)), local-path package display ([#11392](https://github.com/earendil-works/pi/issues/11392)).
- **Terminal-integration fragility.** Multiple independent reports of copy/paste and escape-sequence handling breaking under tmux, iTerm2, and `Ctrl+Shift+C` — including typed-prompt loss after upgrading to 1.3.0 ([#11379](https://github.com/earendil-works/pi/issues/11379)). This is the single largest cluster of user-visible irritation.
- **Fast release cadence vs. regression risk.** The prompt-loss report landed right after 1.3.0, and mid-session extension reloads can poison an entire Anthropic session ([#11376](https://github.com/earendil-works/pi/issues/11376)). Users are effectively asking for session-level resilience against extension churn.
- **Triage throughput is high but confusing.** Many issues closed `[no-action]` / `[last-read, no-action]` within a day, and duplicate PRs were opened for the same `/`-in-provider-id bug ([#11393](https://github.com/earendil-works/pi/pull/11393) vs [#11397](https://github.com/earendil-works/pi/pull/11397)). External contributors lack visibility into what's actually accepted — a `good-first-issue` / "accepted, PRs welcome" label convention would reduce wasted effort.
- **Tooling noise erodes trust.** `pi update --extensions` reports npm vulnerabilities that the suggested `npm audit fix` cannot resolve — and may install a second copy of Pi into the extension tree ([#11409](https://github.com/earendil-works/pi/issues/11409)).

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-10-26

## 1. Today's Highlights

The nightly line advanced to **v0.26.1-nightly.20261025**, headlined by managed session permission rules with an Auto-approval mode and a hardened `serve` token path. Meanwhile, the **v0.26.1 release fan-out remains incomplete**: the SDK `0.1.23` publication was cancelled after ~6 hours of integration tests, and the managed runtime release stopped at an immutable-publication preflight. CI health continues to dominate activity, with the top three issues by comment count all being main-branch gate failures or runtime delivery tracking.

---

## 2. Releases

**v0.26.1-nightly.20261025.8e28d5380c**

- `feat(managed)`: session permission rules and **Auto approval** ([#15448](https://github.com/QwenLM/qwen-code/pull/15448)) — lets managed sessions declare permission policy up front instead of prompting per tool call.
- `fix(cli)`: harden serve token handling — tightens how the daemon's auth token is passed and validated.

> Note: this is a nightly. The stable `v0.26.1` release itself shipped, but downstream publication artifacts (SDK, managed runtime) are still blocked — see Issues [#15498](https://github.com/QwenLM/qwen-code/issues/15498) and [#15499](https://github.com/QwenLM/qwen-code/issues/15499).

---

## 3. Hot Issues

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724) — Main CI failed: Managed Agent gate, no test identified (38 comments, OPEN)**
   The single most active thread in the repo. A bot-filed, `autofix/skip` failure that no test can be attributed to, updated daily since Oct 19. Symptom of a deeper problem: the gate cannot localize its own failures, so the issue accumulates comment noise instead of converging.

2. **[#13395](https://github.com/QwenLM/qwen-code/issues/13395) — tracking(runtime): Kubernetes tool runtime progress and cross-platform delivery gate (23 comments)**
   The long-running delivery ledger for the K8s tool runtime. Latest update flags two S1 fixes sitting in [#15457](https://github.com/QwenLM/qwen-code/pull/15457), not yet merged or ACKed, with six CI failures attributed to that PR. Effectively the release-blocking umbrella for managed runtime.

3. **[#15498](https://github.com/QwenLM/qwen-code/issues/15498) — [P1] SDK 0.1.23 publication blocked by integration timeouts (11 comments)**
   `@qwen-code/sdk@0.1.23` never published: the Release SDK job was cancelled after roughly six hours stuck in *Run SDK Integration Tests*. This is the clearest signal that the release pipeline's test budget has outgrown its timeout configuration.

4. **[#15499](https://github.com/QwenLM/qwen-code/issues/15499) — [P2] managed runtime v0.26.1 stops at immutable-publication preflight (4 comments)**
   Companion failure to the above: the managed runtime release run passed its release job but halted before publication. Two independent publication gates failing on the same version suggests the fan-out chain lacks a single point of observability.

5. **[#15486](https://github.com/QwenLM/qwen-code/issues/15486) — [P1] After auto-compaction, next request double-counts pending tool result + 20K pad (3 comments)**
   A correctness bug with real user impact: compaction can fire while a tool result is still pending, so the next request's size estimate counts it twice plus a 20K pad — ending the turn or forcing `max_tokens: 1`. High severity relative to its low comment count.

6. **[#15542](https://github.com/QwenLM/qwen-code/issues/15542) — [P1] Anthropic provider: JSON side queries 400 on Sonnet 5.5 / Opus 5.5 (3 comments)**
   Side queries send `tool_choice: {"type":"any"}`, which the newer Claude models reject. A clean, reproducible provider-compat break in 0.26.1 that blocks anyone using Anthropic for structured side tasks.

7. **[#15526](https://github.com/QwenLM/qwen-code/issues/15526) — [P2] warn mode sends loop reminder once, then 59 identical calls run unguarded (3 comments)**
   Reporter observed 70 byte-identical `read_file` calls in one session, 59 of them after the single warning. Paired with [#15537](https://github.com/QwenLM/qwen-code/issues/15537), which shows the warning can be dropped entirely before model send while telemetry records it as delivered. Strong evidence for the community that loop detection needs enforcement, not just advisories.

8. **[#15545](https://github.com/QwenLM/qwen-code/issues/15545) — [P3] feat(context): threshold-triggered handover continuation (5 comments)**
   Proposes writing a handover report at a configurable context fill level and continuing in a fresh context, instead of in-place summarization. Argues auto-compaction causes "silent quality decay" on long autonomous runs — well-articulated and likely to shape the roadmap.

9. **[#15567](https://github.com/QwenLM/qwen-code/issues/15567) — [P2] MCP startup failure omits child stderr from diagnostics (4 comments)**
   A missing shebang interpreter surfaces only as "disconnected connection." The actionable error is swallowed. Small fix, outsized developer-experience payoff — MCP setup is a common first-run stumbling block.

10. **[#8432](https://github.com/QwenLM/qwen-code/issues/8432) — [P2] Bailian Personal Token Plan model list out of sync; image/video generation fails (6 comments, 1 👍)**
    Open since August and still the only issue in the top set with a reaction. Built-in `/auth` model catalog for Alibaba ModelStudio drifts from the console, breaking image/video generation. Reflects the maintenance burden of hardcoded provider catalogs.

**Also worth watching:** [#15513](https://github.com/QwenLM/qwen-code/issues/15513) (headless retry classification — currently just "Retrying provider attempt" with no reason or counter), [#15552](https://github.com/QwenLM/qwen-code/issues/15552) (catalog snapshot displaces `UserPromptSubmit` dedup receipt after #14129), and [#15527](https://github.com/QwenLM/qwen-code/issues/15527) (post-merge residues from the automation scanner row pool).

---

## 4. Key PR Progress

1. **[#15296](https://github.com/QwenLM/qwen-code/pull/15296) — feat(web-shell): allow embedding hosts to pick workspace directories**
   Embedders can supply their own directory picker for the connected daemon via an optional callback, with an abort signal, taking precedence over native selection. Unblocks third-party hosts that need custom FS abstractions.

2. **[#15566](https://github.com/QwenLM/qwen-code/pull/15566) — feat(core): durable terminal identity lookup**
   Adds an immutable derived index for completed local activation and inbox records, with the journal staying authoritative and index records rebuildable from validated journal state. Foundational for exactly-once semantics.

3. **[#15569](https://github.com/QwenLM/qwen-code/pull/15569) — feat(managed-agent): native journal snapshot storage**
   The PR2 snapshot protocol: a writer publishes a named snapshot of an exact committed head, retries idempotently, and reads verified bounded pages after a writer takeover.

4. **[#15394](https://github.com/QwenLM/qwen-code/pull/15394) — fix(managed-agent): pass the pool Harness the Broker pair serve removes**
   The Kubernetes Harness pool now injects `QWEN_MANAGED_RUNTIME_BROKER_URL` / `_TOKEN`, matching what `qwen serve` reads when the flags are absent. Directly addresses a config-wiring gap in the K8s delivery path.

5. **[#15564](https://github.com/QwenLM/qwen-code/pull/15564) — feat(managed-agent): preflight Harness compatibility, leftover workers, live service**
   `--check` gains three first-install/upgrade checks, including building and closing a real `HostedHarnessClient` after the `/capabilities` 200 to negotiate exactly as the runtime will.

6. **[#15466](https://github.com/QwenLM/qwen-code/pull/15466) — feat(serve): user-scope Skill activation routes**
   `PUT /skills/:name/activation` plus a batch variant (up to 100 names), mirroring `PUT /extensions/activation`. Fills a gap where Skills could only be toggled per-workspace.

7. **[#15030](https://github.com/QwenLM/qwen-code/pull/15030) — feat(cli): raise workspace skill install limits**
   Splits the transport ceiling from the on-disk ceiling (previously a single 6 MiB / 6 MiB / 2 MiB-per-file / 128-file budget since July). A long-standing practical limit for real-world skill packages.

8. **[#15547](https://github.com/QwenLM/qwen-code/pull/15547) — feat(cli): run only diff-related tests in review build-test**
   `qwen review build-test` now hands `vitest related` the changed files plus their referrers, for any workspace whose `test` script is a plain `vitest run`. Directly attacks review-loop latency.

9. **[#15578](https://github.com/QwenLM/qwen-code/pull/15578) — feat(autofix-pr): run unattended watch's fix agent in the background**
   Third part of the `/autofix-pr` usability series after #15510 and #15548; keeps the session usable while the fix agent works. Notable as the project automates its own maintenance.

10. **[#14230](https://github.com/QwenLM/qwen-code/pull/14230) — feat(web-shell): browse managed auto-memory in /memory**
    Adds an "Auto memory" tab listing entries grouped by scope. Complements the memory work landing in core and gives users visibility into what the agent has persisted.

**Also merged/closed this window:** [#15490](https://github.com/QwenLM/qwen-code/pull/15490) (pinned managed extension packages — closed), [#15543](https://github.com/QwenLM/qwen-code/pull/15543) (bootstrap sets service account + MySQL with correct owners/modes — closed), [#15393](https://github.com/QwenLM/qwen-code/pull/15393) (web-shell workspace directories settings — closed).

---

## 5. Hot Discussions

*No discussion data was provided for this period.*

---

## 6. Feature Request Trends

- **Context lifecycle beyond compaction.** [#15545](https://github.com/QwenLM/qwen-code/issues/15545) is the leading edge of a cluster (with [#15486](https://github.com/QwenLM/qwen-code/issues/15486), [#15552](https://github.com/QwenLM/qwen-code/issues/15552)) arguing that in-place summarization silently degrades long autonomous runs. The ask is shifting from "compact better" to "hand off cleanly into a fresh context."
- **Managed-agent / Kubernetes runtime hardening.** Issues [#13395](https://github.com/QwenLM/qwen-code/issues/13395), [#15499](https://github.com/QwenLM/qwen-code/issues/15499) and PRs [#15394](https://github.com/QwenLM/qwen-code/pull/15394), [#15564](https://github.com/QwenLM/qwen-code/pull/15564), [#15569](https://github.com/QwenLM/qwen-code/pull/15569) form a coherent push toward operator-grade deployment: preflight checks, snapshot storage, durable identity, pool wiring.
- **Observability of internal machinery.** Requests to surface *why* the agent retried ([#15513](https://github.com/QwenLM/qwen-code/issues/15513)), *why* MCP failed ([#15567](https://github.com/QwenLM/qwen-code/issues/15567)), and whether a loop warning was actually delivered ([#15537](https://github.com/QwenLM/qwen-code/issues/15537)) — a consistent theme that diagnostics should be user-facing, not telemetry-only.
- **Ecosystem extensibility.** Skill install limits ([#15030](https://github.com/QwenLM/qwen-code/pull/15030)), user-scope skill activation ([#15466](https://github.com/QwenLM/qwen-code/pull/15466)), pinned extension packages ([#15490](https://github.com/QwenLM/qwen-code/pull/15490)), and external context providers ([#15541](https://github.com/QwenLM/qwen-code/issues/15541)) all point at making Qwen Code a host for third-party capabilities.
- **Embeddability.** [#15296](https://github.com/QwenLM/qwen-code/pull/15296) and [#14230](https://github.com/QwenLM/qwen-code/pull/14230) show sustained investment in Web Shell as something other products embed, not just a UI.

---

## 7. Developer Pain Points

- **Release fan-out is the weakest link.** `v0.26.1` shipped, but the SDK and managed runtime did not. Six-hour integration-test runs that get cancelled ([#15498](https://github.com/QwenLM/qwen-code/issues/15498)), preflight aborts ([#15499](https://github.com/QwenLM/qwen-code/issues/15499)), and 37–50 minute post-publish fan-out on GitHub-hosted runners ([#15465](https://github.com/QwenLM/qwen-code/issues/15465)) compound into a release process maintainers visibly don't trust.
- **CI noise is crowding out real work.** [#14724](https://github.com/QwenLM/qwen-code/issues/14724) (38 comments), [#15173](https://github.com/QwenLM/qwen-code/issues/15173), [#13009](https://github.com/QwenLM/qwen-code/issues/13009), [#13015](https://github.com/QwenLM/qwen-code/issues/13015), [#14796](https://github.com/QwenLM/qwen-code/issues/14796), [#14104](https://github.com/QwenLM/qwen-code/issues/14104) — a large fraction of open issues are bot-filed main-branch failures, several with "no test identified." Flaky assertions under host contention ([#15341](https://github.com/QwenLM/qwen-code/issues/15341), 1/44,637) and Maven Central rate-limiting the shared `ecs-qwen` pool ([#15546](https://github.com/QwenLM/qwen-code/issues/15546)) are named root causes.
- **Token accounting after compaction is brittle.** [#15486](https://github.com/QwenLM/qwen-code/issues/15486) is a P1 correctness bug; the interaction between compaction, pending tool results, and size estimation appears under-specified rather than merely buggy.
- **Provider-specific behavior keeps breaking.** Forced `tool_choice` on newer Anthropic models ([#15542](https://github.com/QwenLM/qwen-code/issues/15542)) and drifting Bailian model catalogs ([#8432](https://github.com/QwenLM/qwen-code/issues/8432)) both stem from assumptions baked into the client about provider capabilities.
- **Agent loop guardrails are advisory, not enforced.** [#15526](https://github.com/QwenLM/qwen-code/issues/15526) and [#15537](https://github.com/QwenLM/qwen-code/issues/15537) together describe a guard that fires once, may be silently dropped, and records success regardless — users running long autonomous tasks are the ones who notice.
- **Diagnostics hide the actionable error.** MCP child stderr ([#15567](https://github.com/QwenLM/qwen-code/issues/15567)) and headless provider retries ([#15513](https://github.com/QwenLM/qwen-code/issues/15513)) both surface generic messages while the useful detail stays in internals.

---

*Generated from github.com/QwenLM/qwen-code activity for 2026-10-26. Comment counts and statuses reflect the snapshot provided.*

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*