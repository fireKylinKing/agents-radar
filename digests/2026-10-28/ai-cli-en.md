# AI CLI Tools Community Digest 2026-10-28

> Generated: 2026-10-28 01:35 UTC | Tools covered: 7

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

# Cross-Tool Comparison Report — AI CLI Ecosystem (2026-10-28)

**Scope:** Claude Code, OpenAI Codex, Gemini CLI, GitHub Copilot CLI, OpenCode, Pi, Qwen Code.
**Data caveat:** Figures below are derived from the supplied digests, which recap top-10 issue lists and key PRs rather than full repo metrics. Counts are "items surfaced in window," not total open issues. Where a channel produced no data, it is marked N/A rather than zero.

---

## 1. Ecosystem Overview

The seven tools have converged on a common product shape — a terminal/IDE agent with MCP extensibility, subagent orchestration, sandboxed execution, and a permission model — and are now competing on **reliability of the agent loop** rather than raw capability. Release velocity is high across the board (four of seven shipped within 24h; two shipped multiple channels), but the issue queues reveal that maturity is uneven: state-integrity bugs, silent failures, and platform-specific breakage dominate over missing features. A second, quieter shift is visible: communities are increasingly asking to **verify and audit** what agents did (signed run receipts, truthful termination reasons, reproducible `AGENTS.md` checks) rather than simply asking them to do more. Meanwhile, enterprise constraints — identity mapping, policy allowlists, provider routing — have moved from roadmap talk into shipped code and recurring bug reports.

---

## 2. Activity Comparison

| Tool | Issues surfaced (window) | Highest-comment issue | PRs (24h) | Discussions | Releases (24h) |
|---|---|---|---|---|---|
| **Claude Code** | 17 (10 hot + 7 tracked) | #76248 — 41 comments | 3 updated (2 open, 1 closed) | N/A (none supplied) | 1 (v2.1.312) |
| **OpenAI Codex** | 13 (10 hot + 3 tracked) | #56655 — 33 comments, 👍38 | ~14 closed (bot-dominated batch) | 9 (2 Q&A, 7 show-and-tell) | 1 stable (v0.165.0) + 4 pre-release artifacts |
| **Gemini CLI** | 10 hot (+ linked) | #22323 — 13 comments | ~12 (10 listed + companions) | N/A (none supplied) | 3 (stable v0.66.0, preview.0, nightly) |
| **GitHub Copilot CLI** | 10 hot | #3709 — 12 comments, 👍34 | **0 updated** | N/A (none supplied) | **4** (v1.0.104–v1.0.105-1) |
| **OpenCode** | 14 (10 hot + 4 watch) | #59387 — 35 comments | ~15 (incl. daemon stack) | N/A (none supplied) | 1 (v2.2.0) |
| **Pi** | 13 (10 hot + 3 honorable) | #10953 — 11 comments | ~12 closed | 2 (show-and-tell) | **0** |
| **Qwen Code** | 13 (10 hot + 3 notable) | #12380 — **72 comments** | ~13 (incl. 3-PR SDK series) | N/A (none supplied) | 4 (v0.26.2, 2 previews, TS SDK) |

**Reading the table:**
- **Issue volume ≠ health.** Claude Code reports a 50-issue/24h update rate against 3 PRs — a review backlog skewed ~17:1 toward issues. Copilot CLI shows the inverse: 4 releases and 0 PRs, consistent with a closed internal pipeline where the public repo is a release/bug channel, not a contribution surface.
- **Codex is the most automated contributor.** Nearly all closed PRs are attributed to `copyberry[bot]` — a batched landing pipeline, not organic community contribution.
- **Discussions are a secondary channel** for this cohort; only Codex and Pi produced data. All others are issue-tracker-centric, so their N/A entries reflect reporting scope, not community silence.

---

## 3. Shared Feature Directions

Six requirements recur across three or more tool communities:

**3.1 Context economics / compaction cost**
- *Claude Code:* schema/name deferral for tools and connectors (#101371), on-demand memory injection (#78795).
- *Gemini CLI:* AST-aware reads and surgical extraction to cut the ~36.6k-token/turn baseline (#22745, #19561).
- *Pi:* prefix-preserving summarization (PR #11499 fixing #11500), multiple context-window variants per model (#10893).
- *Qwen Code:* dynamic tool-output truncation under pressure (#2566).
- *Shared need:* load less by default **and** stop paying full prefill for housekeeping calls.

**3.2 Truthful agent observability and termination**
- *Gemini CLI:* `MAX_TURNS` exhaustion reported as `GOAL` success (#22323); Wayland browser agent reports `GOAL` on failure (#21983); `/bug` omits subagent internals (#21763).
- *OpenCode:* failed subagent reported to parent as success (#52378); phantom model entries polluting usage stats (PR #59773).
- *Claude Code:* signed run receipts for subagent verification (#104409).
- *Qwen Code:* false success on max session turns (#15358); JSON result replaying last answer instead of erroring.
- *Shared need:* callers must distinguish "done" from "gave up," programmatically.

**3.3 Permission and sandbox correctness (not capability)**
- *Claude Code:* `ask` silently converted to allow on `.git/`/`.claude/` writes under `bypassPermissions` (#103994); VS Code permission responses dropped in long sessions (#89245).
- *Codex:* explicit macOS Git authorization ignored (#54574); Windows auto-review rejects human approval (#54962).
- *OpenCode:* `provider.use` deny policies bypassable during hot-reload, routing prompts to a third-party provider (#52333).
- *Qwen Code:* `sed` simulation diverging from real `sed` (#15675); `2>&1` misclassified as a file write (PR #15681); bash word-splitting errors (PR #14670); reassigned `HOME` (%14677).
- *Copilot CLI:* `preToolUse` hooks bypassed under parallel tool calls (#2893).
- *Shared need:* an approval that is granted must be honored, and an approval that is denied must not leak — consistently, in every code path.

**3.4 Session durability and resume integrity**
- *Pi:* resume drops built-in tools after a truncated branch (#11460); in-memory state diverging from disk on write failure (PR #11486); sessions permanently wedged at the context ceiling (#9409).
- *Qwen Code:* persisted thinking part without signature bricks the session with permanent 400s (#15742); stale skills catalog (#15645).
- *Codex:* `SYSTEMROOT` absent from MXC env makes local chats unresumable (#56264).
- *Copilot CLI:* 120s host-ack timeout renders a session unusable until resume (#5100); no unarchive (#3518).
- *Claude Code:* desktop session enumeration/backgrounding request (#104467).
- *Shared need:* a session is a durable artifact; corruption should be recoverable, not terminal.

**3.5 Multi-provider and BYOK flexibility**
- *Copilot CLI:* per-session model switching incl. local providers (#3709, 👍34 — the top-voted request); per-agent provider selection (#4703); non-persistent model selection (#1869).
- *Codex:* Vertex AI as a first-class provider (v0.165.0); `ProviderAuthContext` shared across catalogs and inference (PR #57607); catalog URL overrides for enterprise staging (PR #57567).
- *Pi:* provider protocol fidelity — OpenCode endpoints (#11434), LiteLLM overflow detection (#11494), OpenRouter reasoning budgets (#11496), namespace-correct Responses replay (#11415).
- *Qwen Code:* io.net preset (10 👍), vendor header correctness.
- *Shared need:* the model layer should be configurable, persistent, and spec-faithful — not a fixed menu.

**3.6 Windows / WSL2 as a first-class platform**
- *Codex:* extension fails to start (#56655 + 3 duplicates), MXC aborts on unrelated locked BitLocker volumes (#50915), elevated-sandbox ACL inconsistency (#57610), idle Git scans pegging CPU (#57111).
- *OpenCode:* console flash ~1/sec from missing `CREATE_NO_WINDOW` (#52266, a re-report), WSL UNC paths crashing the desktop↔server link (#52205), orphaned TUI processes (#52203).
- *Copilot CLI:* native runtime crashes (#4026), WSL2 voice segfault (#5197), SSH+tmux copy/paste (#3260).
- *Gemini CLI:* CRLF diff normalization now shipping; Wayland browser sessions failing.
- *Shared need:* parity, not "works on macOS."

---

## 4. Differentiation Analysis

| Dimension | Claude Code | OpenAI Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **Primary user** | Enterprise teams, cloud/Cowork sessions | Pro devs + enterprise multi-cloud | Agent-architecture researchers, Google stack | GitHub-centric orgs, CI/automation | Power users running many concurrent sessions | Cost-sensitive long-session operators | Managed-agent / web-shell operators |
| **Technical core** | Plugin + MCP channel model; gateway/SSO | Rust runtime; decoupled `codex-exec-server`; MXC sandbox | Subagent registry + context-frugal tooling | Fusion managed models; ACP headless surface | Bun/TS; process-daemon + desktop plugin host | Provider-adapter purity; compaction-first | Sandbox-free bash emulation; dual-path managed runtime |
| **Notable bets** | OIDC claim flexibility (`oidc.user_id_claim`); marketplace distribution | Standalone executor binary; `doctor --network-only` diagnostics; per-provider auth | AST-aware tooling as a token-reduction strategy | Autopilot waiting on background tasks; ACP parity | Desktop GUI plugins (experimental) | Prefix-preserving compaction; durable task orchestration | Per-session Git worktrees; session permission rules |
| **Weakest seam today** | Cloud GitHub authorization vs. UI state disagreement | Windows extension/sandbox/updater | Agent hangs + misleading success | Opaque, non-persistent model selection | Silent failures at process/provider boundaries | Session wedging and compaction re-entrancy | CI/release pipeline reliability |

**Key contrasts worth flagging:**

- **Where the "agent boundary" lives.** Codex is pushing execution *out* of the client (standalone `codex-exec-server`, `codex doctor`); Claude Code is pushing sessions *in* to a managed cloud plane (Cowork) — and paying for it with an authorization model whose UI and runtime disagree. OpenCode keeps a local daemon and is now fighting state-pinning bugs (`failed` MCP servers never retried, hardcoded port 49374).
- **Approaches to context pressure.** Gemini CLI attacks it with better *reads* (AST-aware search); Pi attacks it with better *writes* (prefix-sharing compaction). These are complementary and both more structural than "truncate harder."
- **Approaches to safety.** Qwen Code emulates bash in JS for permission analysis — powerful, and the source of an entire bug class (`sed` flag gaps, `\s` vs bash separators, `HOME` reassignment). Codex instead narrows the *environment* (MXC), which produces its own class of failures (missing `SYSTEMROOT`, over-eager BitLocker probing). Claude Code's holes are semantic (#103994), not environmental.
- **Distribution strategy.** Claude Code and Copilot CLI ship through managed marketplaces/extensions with release-gated rollouts; OpenCode is opening a *third-party* plugin surface on desktop (explicitly experimental), trading stability for ecosystem growth. Pi is the only tool in the set with zero releases in the window — a triage-and-fix day.

---

## 5. Community Momentum & Maturity

**Tier 1 — High volume, high blast radius, systemic issues**
- **Claude Code** has the largest signal-to-noise problem: a *single* regression cluster (Cowork/GitHub auth) generated at least eight issues plus seven tracked variants in one day, while PR throughput sat at three. Its highest-engagement issue has run since July with no resolution. Maturity is high in surface area, low in incident response.
- **OpenAI Codex** shows the strongest engineering throughput — a coherent 14-PR batch spanning observability, packaging, and durability — but its top issue (#56655, 👍38) is a release-blocking Windows extension regression with duplicate reports and **no published rollback guidance**. That gap between ship velocity and user-side recovery is the maturity signal to watch.

**Tier 2 — Fast iteration, narrower blast radius**
- **GitHub Copilot CLI** has the fastest release cadence in the cohort (4 in 24h) but essentially no public contribution surface (0 PRs). Its top-voted issue (👍34) has been open long enough to become a defining limitation, and its model-selection behavior is described as opaque and non-persistent — a trust problem more than a feature gap.
- **Qwen Code** has the highest raw discussion intensity (#12380 at 72 comments, #14724 at 68) but a large share of it is *self-inflicted CI churn*: five concurrent CI threads, publish-stage failures, and a growing ledger of "deferred review findings" with no owner. Velocity is real; throughput efficiency is not.

**Tier 3 — Smaller, higher-signal engineering communities**
- **Gemini CLI** posts modest comment counts (13 max) but the highest 👍 density per issue (#21409 at 👍8 for a hang). Its problems are architectural (agent delegation fires too rarely or hangs) rather than infrastructural.
- **OpenCode** combines a genuine release headline (v2.2.0 desktop plugins) with a serious correctness bug in the core edit path (#53011, numeric duplication up to 129x) and a security hole under hot-reload (#52333). Ecosystem ambition is running ahead of its safety model.
- **Pi** had the most disciplined day in the set: no releases, but P0 closures on tool-loss-on-resume, Responses namespace replay, and Bedrock schema limits, plus a substantive performance fix (prefix-preserving compaction). Low comment counts, high fix density — a maintainer-led project rather than a crowd-driven one.

**Momentum ranking (composite of release cadence, PR landing rate, and issue engagement):**
1. OpenCode / Qwen Code (shipping + actively contested)
2. OpenAI Codex (highest engineering throughput)
3. Gemini CLI / Copilot CLI (steady cadence, thin PR surface)
4. Claude Code (high engagement, constrained throughput)
5. Pi (low cadence, high fix quality)

---

## 6. Trend Signals

**T1 — "Prove it ran" is replacing "make it run."**
Signed run receipts (Claude #104409), truthful termination reasons (Gemini #22323/#21983), reproducible instruction-propagation checks (Codex `Agent Relay Check`), and JSON outputs that error instead of replaying a stale answer (Qwen #15358) all point the same way. **Reference value:** if you are building on these CLIs in CI or orchestration, do not trust a `status: success` field today — instrument independently, and prefer tools that expose structured failure reasons.

**T2 — The sandbox is the new compatibility matrix.**
MXC on Windows, Seatbelt on macOS, JS-level bash emulation in Qwen, and Claude's hook semantics each generate their own defect class. **Reference value:** when selecting a tool for unattended runs, evaluate the sandbox against your *actual* environment quirks — locked volumes, WSL path translation, `SYSTEMROOT`, reassigned `HOME`. Environmental assumptions, not feature lists, predict failure.

**T3 — Silent failure is now judged worse than hard denial.**
Recurring across OpenCode (port 49374 hangs forever; plugins without entrypoints skipped silently), Copilot CLI (dropped MCP images, false `/rename` success), Pi (exec with no reply), and Claude Code (green UI, failing session). **Reference value:** weight diagnostics as a product feature. Codex's `doctor --network-only` and redacted WebSocket probe evidence are the clearest positive example — invest in tooling that turns "it didn't work" into an actionable artifact.

**T4 — Compaction is becoming an accounting problem, not a summarization problem.**
Pi's finding that summarization requests share no prefix with live turns (full prefill each time) is the most economically significant single insight in this dataset. Combined with Gemini's token-baseline work and Claude's schema deferral, the direction is clear. **Reference value:** measure cache hit rate and prefill cost per turn, not just tokens per request; a compaction strategy that ignores prompt-cache prefix alignment can silently double operating cost.

**T5 — BYOK and per-session model switching are table stakes, but persistence is the actual ask.**
Copilot's top-voted issue isn't "more models" — it's "stop reverting my choice." Codex is building the auth plumbing (`ProviderAuthContext`) to make multi-provider coherent. **Reference value:** model configuration should be a durable, auditable artifact per project, not a session-scoped flag.

**T6 — Enterprise adoption is gated on identity and policy plumbing, not capability.**
OIDC non-`sub` claim mapping shipped in Claude Code; Codex added catalog URL overrides for staging; Copilot CLI's adoption blockers are a system-wide installer and `allowedMcpServers` matching; OpenCode's `OPENCODE_PASSWORD_FILE` request is container-deployment driven. **Reference value:** SSO claim flexibility, per-workspace (not global) policy, and secret-manager-friendly config are procurement requirements, not nice-to-haves.

**T7 — Agents are leaving the terminal.**
Codex's sports-video-coach and Haru Café discussions, plus Claude's agent-identity-on-the-open-web request (#101972) and Pi's classifier-based tool scoping (AY-PI), show usage drifting outside software engineering. **Reference value:** tool-scoping per task class (only CODE gets `edit`/`bash`) is an emerging pattern worth adopting early — it reduces both token cost and blast radius.

---

### Bottom line for decision-makers

- **Choose Codex** for engineering rigor and multi-cloud provider control; verify your Windows story before rollout and pin versions — there is no automatic rollback.
- **Choose Claude Code** for enterprise SSO and plugin breadth, but audit the cloud-session git authorization path against your repo topology before depending on it; the UI is not a reliable status indicator.
- **Choose Gemini CLI** if you are building on subagents — but add external termination checks, since `GOAL` is not trustworthy today.
- **Choose Copilot CLI** if you live in GitHub and need ACP/headless automation; expect to manage model persistence yourself.
- **Choose OpenCode** for multi-session power use and desktop extensibility, with the caveat that the plugin/permission model is still being hardened.
- **Choose Pi** where per-turn cost and long-session integrity dominate; it has the most disciplined context-economics work in the cohort.
- **Choose Qwen Code** for managed-agent/web-shell deployments, contingent on tolerating a release pipeline that is still stabilizing.

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — Community Highlights (data as of 2026-10-28)

**Data note:** the export lists 50 PRs "sorted by comments," but the comment and 👍 fields returned `undefined`/`0` for every PR. Rankings below therefore follow the repository's own attention ordering plus observable activity signals (update recency, linked issues, number of files/skills touched, repeat contributors). Issue comment counts *were* available and are used quantitatively in Section 2.

---

## 1. Top Skills Ranking

| # | Skill / PR | What it does | Discussion highlights | Status |
|---|---|---|---|---|
| 1 | [mcp-builder — `mcp>=2` client fix](https://github.com/anthropics/skills/pull/1742) (#1742) | Repairs the MCP-server scaffolding skill's `connections.py` for the `streamable_http_client` rename and header injection via `create_mcp_http_client` | API-drift breakage in the most version-sensitive skill; closes #1668 | OPEN (upd. 2026-10-08) |
| 2 | [skill-creator — trigger-eval isolation](https://github.com/anthropics/skills/pull/1298) (#1298) | Fixes false "misses" in trigger evaluation: per-worker probe collisions, `select()` on Windows pipes, unrelated-tool scan aborts | Correctness of the eval loop that decides whether *any* skill fires; runtime failures were silently scoring as non-triggers | OPEN (upd. 2026-09-16) |
| 3 | [notion-spec-to-implementation + quantitative-resume-auditor](https://github.com/anthropics/skills/pull/1245) (#1245) | Turns product/tech specs into Notion tasks with acceptance criteria and progress tracking; second skill audits resumes numerically | Bundled dual submission; spec→backlog automation is a recurring community ask | OPEN (upd. 2026-10-15) |
| 4 | [proofcore-contract-auditor](https://github.com/anthropics/skills/pull/1771) (#1771) | Static analysis of Solidity/Rust contracts with cryptographic audit proofs anchored on TON via a zero-storage Merkle protocol | Web3/vertical-domain skill; raises namespace-trust questions (cf. Issue #492) | OPEN (upd. 2026-09-16) |
| 5 | [docx — detect orphaned comments](https://github.com/anthropics/skills/pull/1734) (#1734) | Flags `docx` comments whose anchor text was deleted | Notable for a terse, summary-less submission — illustrates review friction on core-skill fixes | OPEN (upd. 2026-09-25) |
| 6 | [md2video-audio](https://github.com/anthropics/skills/pull/1703) (#1703) | Compiles Markdown → Marp slides → MP4 with human-like TTS voiceover, zero-cost pipeline | Media-generation direction; feasibility debated around render/audio toolchain dependencies | OPEN (upd. 2026-09-15) |
| 7 | [webapp-testing — drop `shell=True`](https://github.com/anthropics/skills/pull/1980) (#1980) | Replaces `subprocess.Popen(..., shell=True)` in `with_server.py` to close a CWE-78 command-injection path | Security hardening of a testing skill that executes agent-supplied commands | OPEN (upd. 2026-10-25) |
| 8 | [docx — report LibreOffice timeout as error](https://github.com/anthropics/skills/pull/1792) (#1792) | `accept_changes.py` now fails on `soffice` timeout and verifies `w:ins`/`w:del`/`w:moveFrom` removal before claiming success | Silent-success failure mode; part of a docx reliability cluster | OPEN (upd. 2026-09-25) |

*Also noteworthy:* [#514 document-typography](https://github.com/anthropics/skills/pull/514) (widow/orphan and numbering QC for generated docs) and [#525 pyxel](https://github.com/anthropics/skills/pull/525) (retro game dev with headless frame inspection) — both long-lived submissions with sustained update activity.

**Pattern:** 6 of the top 8 are *fixes to bundled skills* (mcp-builder, skill-creator, docx, webapp-testing) rather than new skills. The community's most-watched activity is maintenance of official skills, not net-new submissions.

---

## 2. Community Demand Trends (from Issues)

- **Trust, security and governance of shipped skills** — the single loudest thread. [#492 namespace/trust-boundary abuse](https://github.com/anthropics/skills/issues/492) (46 comments) argues community skills distributed under `anthropic/` impersonate official ones. Reinforced by [#1394 eval-viewer XSS](https://github.com/anthropics/skills/issues/1394), [#1487 `claude-api` injecting ~156k tokens](https://github.com/anthropics/skills/issues/1487), [#2018 invalid XML from `merge_runs.py`](https://github.com/anthropics/skills/issues/2018).
- **Enterprise distribution & sharing** — [#228 org-wide skill sharing](https://github.com/anthropics/skills/issues/228) (17 comments, 8 👍) wants a shared library instead of Slack-file passing; [#189 duplicate skills across plugins](https://github.com/anthropics/skills/issues/189) (9 👍) and [#62 lost skills](https://github.com/anthropics/skills/issues/62) point at lifecycle/registry gaps.
- **Evaluation & testing infrastructure** — [#556 `run_eval.py` 0% trigger rate](https://github.com/anthropics/skills/issues/556) (13 comments, 7 👍) and [#1390 mcp-builder evaluation scoring 0/N](https://github.com/anthropics/skills/issues/1390) show the skill-evaluation harness itself is unreliable. This is the demand behind PRs #1298, #1997, #1681.
- **Skill authoring quality / trigger metadata** — [#2064 theme-factory description says *what* not *when*](https://github.com/anthropics/skills/issues/2064) and [#202 skill-creator should follow best practice](https://github.com/anthropics/skills/issues/202): demand for description-writing guidance and token-efficient SKILL.md conventions.
- **Agent memory & context economy** — [#1329 compact-memory](https://github.com/anthropics/skills/issues/1329) (10 comments) proposes symbolic notation to shrink persistent agent state.
- **Quality gates & agent governance** — [#1385 three-gate reasoning pipeline](https://github.com/anthropics/skills/issues/1385), [#412 agent-governance](https://github.com/anthropics/skills/issues/412): pre-task calibration, adversarial review, delivery verification, audit trails.

Net: demand is shifting from "more skills" toward **trust, evaluation rigor, and distribution** — plus a steady appetite for vertical/enterprise workflow skills.

---

## 3. High-Potential Pending Skills (active, unmerged)

Ranked by most recent update, all OPEN:

1. [pptx — reject `--cols` < 1](https://github.com/anthropics/skills/pull/1975) (#1975, upd. 10-27) — fail fast in `thumbnail.py` instead of a downstream `ValueError`; closes #1809.
2. [webapp-testing — element_discovery fix](https://github.com/anthropics/skills/pull/1976) (#1976, upd. 10-18) — textarea/select misreported as `text`; closes #1891.
3. [algorithmic-art — `wrapAround()` actually wraps](https://github.com/anthropics/skills/pull/1977) (#1977, upd. 10-18) — modulo with negative handling; closes #1897.
4. [webapp-testing — remove `shell=True`](https://github.com/anthropics/skills/pull/1980) (#1980, upd. 10-25) — security fix, highest-impact of the batch.
5. [skill-creator — direct `package_skill.py` execution](https://github.com/anthropics/skills/pull/1681) (#1681, upd. 10-24) — `ModuleNotFoundError` and stale usage paths.
6. [pyxel retro-game skill](https://github.com/anthropics/skills/pull/525) (#525, upd. 10-23) — headless input-driven runs, frame inspection.
7. [skill-creator — YAML frontmatter parsing](https://github.com/anthropics/skills/pull/1997) (#1997, upd. 10-19) — quote/continuation-line handling corrupts eval metadata.
8. [notion-spec-to-implementation + resume auditor](https://github.com/anthropics/skills/pull/1245) (#1245, upd. 10-15) — largest net-new skill surface still in flight.

Note the concentration: PRs #1975/#1976/#1977 come from one contributor (gerardrecinto) and are each small, issue-linked, low-risk fixes — the likeliest near-term merges.

---

## 4. Skills Ecosystem Insight

**The community's most concentrated demand is for the official skills themselves to be trustworthy and verifiable — secure, correctly-versioned, and reliably evaluated — far more than for additional skill coverage.**

---

# Claude Code Community Digest — 2026-10-28

## 1. Today's Highlights

Today's issue activity is dominated by a single systemic problem: **GitHub repository authorization inside Cowork/cloud sessions**. At least eight separate open issues report the same failure mode — the git proxy rejects pushes with *"not in this session's authorized repository set"*, `add_repo`/`list_repos` tools are missing, and user-supplied fine-grained PATs no longer pass through. Meanwhile, the sole release (v2.1.312) is a small quality-of-life update adding OIDC claim flexibility for the gateway and click-to-send for queued messages in fullscreen mode.

---

## 2. Releases

**v2.1.312**
- Added `oidc.user_id_claim` to the Claude apps gateway, allowing identity to be resolved from an `id_token` claim other than `sub` (e.g. Microsoft Entra ID's `oid`). Relevant for enterprise SSO deployments where `sub` is pairwise/unstable.
- Added **click to send**: in fullscreen mode, clicking the "to send now" hint under a queued message dispatches it immediately, matching the keyboard shortcut.

No breaking changes noted.

---

## 3. Hot Issues

### 1. [#76248](https://github.com/anthropics/claude-code/issues/76248) — Git proxy blocks all pushes in cloud/Cowork sessions (41 comments, 👍16)
The highest-engagement issue in the window and the anchor for today's dominant theme. Pushes to repos outside the session's "authorized repository set" are rejected even when the user supplies their own fine-grained PAT, which previously worked. Reporter suspects a `CCR_TEST_GITPROXY` rollout. The 👍 count and sustained comment volume from July through October signal a long-running, unresolved regression affecting real workflows.

### 2. [#104695](https://github.com/anthropics/claude-code/issues/104695) — Windows Desktop "fizzy_lake" redesign wrecks dark theme (15 comments, 👍26)
Highest reaction count today. Reports glare-heavy text, cold palette, degraded fonts/chat rendering, inverted sidebar, and slower launch in Desktop 3.6556.2. Marked `[invalid]` but clearly resonating — 26 upvotes against a closed-as-invalid label is a signal worth noting for design QA.

### 3. [#78795](https://github.com/anthropics/claude-code/issues/78795) — Triggered injection for auto-memory topic files (15 comments)
Proposes rules-style frontmatter so memory topic files load on demand rather than always. Ties into the broader context-budget conversation (`/context` pressure, tool schema deferral). Zero upvotes but persistent discussion since July.

### 4. [#103488](https://github.com/anthropics/claude-code/issues/103488) — "Connection lost mid-response" traced to TCP resets from 160.79.104.10
A well-instrumented report: 39 occurrences between Oct 3–18, confirmed via packet capture as server-side resets rather than local network issues. Valuable because it moves the diagnosis from speculation to evidence.

### 5. [#82571](https://github.com/anthropics/claude-code/issues/82571) — No user-facing allowlist for third-party channel plugins
MCP channel plugins only deliver inbound notifications if hard-coded (discord/telegram/fakechat/imessage). Third-party plugins connect but silently fail to deliver. A plugin-ecosystem blocker: marketplace-installed plugins are second-class.

### 6. [#103994](https://github.com/anthropics/claude-code/issues/103994) — `PreToolUse` "ask" overridden in `bypassPermissions` when writing into `.git/` or `.claude/`
Security-relevant: the safe `ask`→`deny` conversion in non-interactive mode is bypassed specifically when the same tool call also writes into `.git/` or `.claude/`. Narrow trigger, high potential impact.

### 7. [#102874](https://github.com/anthropics/claude-code/issues/102874) — Routines API returns 403 "this project is paused" on active Projects
`POST .../routines/{routine_id}/fire` fails with a permission error contradicting the project's actual state. Blocks programmatic/CI-driven routine invocation.

### 8. [#89245](https://github.com/anthropics/claude-code/issues/89245) — VS Code extension tool-permission stream closes intermittently
Long sessions appear frozen when permission responses never reach the extension. Still reproducing on 2.1.241 and linked to #44157 — a chronic reliability issue for the most-used IDE integration.

### 9. [#105196](https://github.com/anthropics/claude-code/issues/105196) — macOS Desktop Code tab scroll oscillation / ghosted text
Conversation pane alternates between two scroll positions ~half a line apart, producing doubled/ghosted rendering. Newest desktop rendering regression in a cluster of UI complaints.

### 10. [#101371](https://github.com/anthropics/claude-code/issues/101371) — Code-only scope for claude.ai connectors, or defer tool names until used
Extends the tool-search/schema-deferral idea to *names* as well as schemas, plus a request to exclude claude.ai connectors from Claude Code sessions specifically. Directly addresses context bloat for users with many connectors.

**Also worth tracking:** [#96075](https://github.com/anthropics/claude-code/issues/96075), [#97926](https://github.com/anthropics/claude-code/issues/97926), [#96609](https://github.com/anthropics/claude-code/issues/96609), [#100164](https://github.com/anthropics/claude-code/issues/100164), [#98524](https://github.com/anthropics/claude-code/issues/98524), [#105504](https://github.com/anthropics/claude-code/issues/105504), [#104120](https://github.com/anthropics/claude-code/issues/104120) — all variants of the same Cowork/GitHub authorization failure.

---

## 4. Key PR Progress

Only **3 pull requests** were updated in the last 24h, so all are listed:

### 1. [#15620](https://github.com/anthropics/claude-code/pull/15620) [OPEN] — `fix(plugin-dev)`: correct tools format in agent/command examples
Fixes a YAML parsing bug where JSON-array syntax in `tools:` and `allowed-tools:` (`["Read", "Write"]`) is interpreted as a literal string instead of a list. Converts to comma-separated YAML across 11 files in the `plugin-dev` plugin. A quietly important fix — this template error propagates into every plugin scaffolded from the examples.

### 2. [#105303](https://github.com/anthropics/claude-code/pull/105303) [CLOSED] — Install marketplace plugins from `claude-plugins-official`
Migrates the `claude-code-plugins` marketplace to source all plugins from the official registry. Notably carries an explicit merge-order dependency on `claude-plugins-official#6905` (code-review port) and `#6906` (plugin versions) — merging early would ship a stale `code-review` and no-op updates. Closed rather than merged.

### 3. [#32753](https://github.com/anthropics/claude-code/pull/32753) [OPEN] — `docs`: fix stale documentation URLs
Closes #30628. Replaces `docs.anthropic.com` / `docs.claude.com` with `code.claude.com` across 12 files (26 line changes), deliberately leaving `CHANGELOG.md` untouched for historical accuracy. Low-risk docs hygiene, open since March.

> ⚠️ PR throughput is very low (3 updated, 2 open, 1 closed). Combined with a 50-issue/24h update volume, the review backlog skews heavily toward issues over contributions.

---

## 5. Feature Request Trends

Distilled from issues in this window:

1. **Context and connector scoping** — Multiple requests push in the same direction: load less by default. Deferred connector *schemas* exist, but users now want deferred tool *names* (#101371) and triggered injection for memory topic files (#78795). Expect continued pressure on `/context` economics.

2. **Plugin/channel extensibility parity** — Third-party MCP channel plugins need a user-managed allowlist (#82571); marketplace installs need to be first-class (#105303). The plugin surface is growing faster than its permission model.

3. **Verifiability and auditability of agent runs** — #104409 proposes signed run receipts so hooks, Mods, and the Agent SDK can verify *that* a subagent ran, under which model, on which input. A notable shift from "make agents do more" to "prove what agents did."

4. **Desktop session management** — #104467 asks for a supported local interface to enumerate Code sessions, inspect process/Remote Control state, and background them, motivated by running ~10 long-lived sessions controlled from mobile.

5. **Agent identity on the open web** — #101972 requests that Claude be able to pass Cloudflare checks as a verified agent acting for a user. Early-stage but likely to recur as agentic browsing expands.

6. **Enterprise identity flexibility** — Reflected in shipping code this cycle (`oidc.user_id_claim`), continuing demand for non-`sub` claim mapping.

---

## 6. Developer Pain Points

**P1 — Cowork/cloud GitHub authorization is broken and opaque.** The single largest cluster today. Symptoms across at least eight issues: git proxy rejects pushes (*"not in this session's authorized repository set"*), `add_repo`/`list_repos` tools absent from sessions, PAT pass-through regressed, settings UI shows green/connected while sessions still fail, and connector reconnect does not remediate. Reporters describe successes on one account and failures on another with identical setup (#97926, #104693), and breakage mid-session (#104120, #76248). **The core frustration is diagnostic, not just functional: the UI's state and the session's state disagree, leaving no path to self-serve a fix.**

**P2 — Desktop UI regressions are eroding trust.** The "fizzy_lake" redesign (#104695, 👍26) and scroll-oscillation/ghosting (#105196) arrive close together. Performance (slower launch) and accessibility (glaring text, contrast) complaints suggest visual QA is lagging behind ship cadence on Windows and macOS.

**P3 — Permission and hook semantics have edge-case holes.** #103994 shows `ask` being silently converted to allow in a specific path-write combination under `bypassPermissions`; #89245 shows permission responses getting dropped in long VS Code sessions. Both undermine confidence in the guardrails developers rely on for unattended runs.

**P4 — Connection stability on the API path.** #103488's packet-capture evidence of server-originated TCP resets points to infrastructure, not client networking — hard for users to work around and directly truncates generations mid-response.

**P5 — Scheduled/routine execution is unreliable for repo work.** Routines fire 403s on active projects (#102874) and routinely lose repo access (#105504, #98588, #100131). For users who've automated overnight builds, this converts a convenience feature into a silent failure mode.

**P6 — Third-party plugin authors hit undocumented walls.** Allowlisted channel plugins (#82571) and template-format bugs (#15620) mean ecosystem contributors discover constraints only after building.

---

*Scope note: no GitHub Discussions data was supplied for this window, so the Hot Discussions section is omitted.*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-10-28

## 1. Today's Highlights

Codex shipped **rust-v0.165.0** with a built-in Google Cloud Vertex AI provider (`model_provider = "gcp"`), GPT-6.1 Terra added to the model catalogs, and navigation improvements (repository filter in the session picker, directory grouping in the agent command center). Meanwhile the 0.166.0 alpha line advanced to **alpha.4** with no published notes. Community attention is dominated by **Windows regressions**: the VS Code extension UI failing to start (`26.1021.22027`) has become the single most-commented issue with at least four duplicate reports, alongside a cluster of MXC sandbox and updater failures.

---

## 2. Releases

### rust-v0.165.0 (stable)

- **Google Cloud Vertex AI provider** — new first-class provider configured via `model_provider = "gcp"`. ([#56265](https://github.com/openai/codex/pull/56265))
- **GPT-6.1 Terra catalog entry** — added to the model catalogs. ([#57337](https://github.com/openai/codex/pull/57337))
- **Session & agent navigation** — repository filter in the session picker plus directory grouping in the agent command center for faster navigation across large workspaces.

### Pre-releases

- `rust-v0.166.0-alpha.4`, `-alpha.3`, `-alpha.2` — published with no changelog text.
- `rust-v0.165.0-alpha.1.2` — patch alpha on the 0.165 line.

> Note: GPT-6.1 Terra landing in the catalog coincides with a live bug report ([#52450](https://github.com/openai/codex/issues/52450)) where GPT-6.1 Sol rejects benign prompts — worth watching as users migrate onto the 6.1 family.

---

## 3. Hot Issues

1. **[#56655](https://github.com/openai/codex/issues/56655) — VS Code extension UI doesn't start (Windows, 26.51021.22027)** · 33 comments · 👍38
   The highest-engagement issue in the repo. Extension upgrades on Windows leave the Codex sidebar unable to render at all. High reaction count signals broad blast radius; duplicates ([#57344](https://github.com/openai/codex/issues/57344), [#57368](https://github.com/openai/codex/issues/57368)) suggest a release-blocking regression with no published rollback guidance.

2. **[#57422](https://github.com/openai/codex/issues/57422) — VS Code / code-server extension freezes the entire editor** · 6 comments
   Escalation of the same release: on code-server (linux-x64, stable on Open VSX since 2026-10-26) the extension hangs the whole editor process, not just the Codex view. `26.908.40401` is reported working, giving a clear bisect window.

3. **[#51731](https://github.com/openai/codex/issues/51731) — Dot cannot connect to Codex conversations ("unsupported placement format version 3")** · 25 comments
   A versioned wire-format mismatch between Dot and the Codex app, open since 2026-10-07. Long-running and still unresolved — a compatibility/versioning concern for the Dots integration surface.

4. **[#50915](https://github.com/openai/codex/issues/50915) — MXC sandbox fails when an unrelated BitLocker volume is locked** · 24 comments · 👍8
   The Windows sandbox aborts before launching any child process if *any* BitLocker data volume is present but locked, even when the workspace doesn't reference it. A correctness bug in environment probing with real enterprise impact.

5. **[#56264](https://github.com/openai/codex/issues/56264) — Windows thread resume fails because MXC `process.env` lacks `SYSTEMROOT`** · 19 comments
   Existing local chats become unresumable after an update. Related to the above: MXC's reduced environment is too aggressive, and breaks before `AGENTS.md` is even read.

6. **[#2379](https://github.com/openai/codex/issues/2379) — Undo/redo for prompt typing (TUI)** · 14 comments · 👍37
   Open since 2025-08 and still the most-upvoted enhancement in the dataset. Persistent demand for baseline terminal-editing ergonomics (Cmd-Z / Shift-Cmd-Z) in the composer.

7. **[#57610](https://github.com/openai/codex/issues/57610) — Windows elevated sandbox: workspace root writable, nested directories denied** · 2 comments (new)
   Fresh report on `26.1021.22027` showing inconsistent ACL propagation under the elevated sandbox — the newest data point in the Windows sandbox thread.

8. **[#52450](https://github.com/openai/codex/issues/52450) — GPT-6.1 Sol rejects benign prompts as `invalid_prompt`** · 9 comments
   `Reply with exactly: OK` is flagged as a usage-policy violation under a Plus account, while GPT-5.6 succeeds. A model-routing/filtering regression that blocks the entire 6.x family for affected users.

9. **[#57111](https://github.com/openai/codex/issues/57111) — Windows app spawns Git scans while idle → high CPU / overheating** · 2 comments
   Idle resource burn on a 14900K is a serious quality signal for a long-running desktop agent; reflects insufficient backoff on workspace watching.

10. **[#54574](https://github.com/openai/codex/issues/54574) — macOS: basic Git writes remain blocked despite explicit authorization** · 6 comments
    Permission controls offer no usable escape hatch — Codex can edit files but not run `git init`. Points at a gap between the permission model and how macOS sandbox rules are applied.

**Also worth tracking:** [#57587](https://github.com/openai/codex/issues/57587) (unmanaged Unix app-server keeps a 1,024 FD soft limit in 0.165.0), [#54962](https://github.com/openai/codex/issues/54962) (Windows auto-review rejects explicit human authorization), and [#50346](https://github.com/openai/codex/issues/50346) — a **closed** Linux regression where Ctrl+Shift+D dictation was silently removed.

---

## 4. Key PR Progress

All items below were **closed on 2026-10-27/28**, nearly all authored by `copyberry[bot]` — evidence of a heavily automated landing pipeline operating in tight, reviewable batches.

1. **[#57615](https://github.com/openai/codex/pull/57615) — Preserve rollout tracing across sub-agent reloads**
   Reloaded/evicted sub-agents previously stopped tracing, leaving later turns untraced; also prevents Code Mode sessions from reusing cell IDs across sessions. Directly improves observability of multi-agent runs.

2. **[#57607](https://github.com/openai/codex/pull/57607) — Share runtime authentication with model providers and catalogs**
   Adds `ProviderAuthContext` so catalog refreshes use the same actor/credential owner as inference requests. Prerequisite plumbing for the new multi-provider (Vertex AI) world.

3. **[#57590](https://github.com/openai/codex/pull/57590) + [#57589](https://github.com/openai/codex/pull/57589) — Ship a standalone `codex-exec-server`**
   Extracts executor startup into `codex-exec-server-cli`, bundles a standalone binary in CLI/app-server packages, advertises it via `execServer` in `codex-package.json`, and includes it in Cargo/Bazel packaging plus macOS signing. Notable architectural decoupling of execution from the client shell.

4. **[#57606](https://github.com/openai/codex/pull/57606) — `codex doctor --network-only`**
   Focused network diagnostics: effective config, network environment, provider HTTP reachability, and Responses WebSocket checks — skipping DB and MCP scans. A direct response to the connectivity/bug-report triage load.

5. **[#57597](https://github.com/openai/codex/pull/57597) — Preserve WebSocket probe evidence and redact diagnostics**
   Keeps upgrade evidence when a subsequent read fails, while redacting URLs, server errors, and close reasons. Pairs with the doctor work above and improves the safety of shared diagnostic output.

6. **[#57586](https://github.com/openai/codex/pull/57586) — Host-reserved V2 child creation APIs**
   `CodexThread::create_child` / `prepare_child` / `ThreadManager::create_prepared_child` for creating idle children from live tool invocations with inherited settings. The foundation for more deterministic sub-agent spawning.

7. **[#57569](https://github.com/openai/codex/pull/57569) — Preserve process output and control replies under backpressure**
   Fixes lost process output and blocked control replies when the in-process event queue fills, and handles descendants keeping stdout open after child exit. A real durability fix for long-running exec sessions.

8. **[#57568](https://github.com/openai/codex/pull/57568) — Independent service tiers for memory consolidation**
   New `memories.consolidation_service_tier` defaulting to `"default"`, so background consolidation no longer inherits the interactive session's tier. Cost/latency control for memory features.

9. **[#57567](https://github.com/openai/codex/pull/57567) — Catalog URL overrides for the built-in OpenAI provider**
   Allows `model_catalog_url` under `[model_providers.openai]` while preserving identity, auth, and inference routing via `openai_base_url`; rejects other non-default overrides. Useful for enterprise/staging catalog testing.

10. **[#57584](https://github.com/openai/codex/pull/57584) — Preserve output boundaries through token truncation**
    New `OutputBoundary` APIs mapping item indices and UTF-8 offsets through truncation, with correct remapping inside omitted text. Fixes subtle truncation/attribution bugs in long outputs.

**Also landed:** [#57585](https://github.com/openai/codex/pull/57585) (replace unavailable image references in requests while preserving history), [#57573](https://github.com/openai/codex/pull/57573) (filesystem-free classification of intercepted patch commands), [#57572](https://github.com/openai/codex/pull/57572) (Apps tool catalogs without shared snapshots), [#57598](https://github.com/openai/codex/pull/57598) (cache empty remote skill discovery with FS watches), [#57612](https://github.com/openai/codex/pull/57612) (disable terminal status sequences on VTE < 0.54).

---

## 5. Hot Discussions

### Q&A

- **[#56686](https://github.com/openai/codex/discussions/56686) — Supported pre-request token bounds for ChatGPT-authenticated `codex exec`**
  Asks whether a hard ceiling on accounted tokens can be enforced without an API key or replacement transport, for a diagnostic workflow capped at 250k input tokens. Unanswered in substance — a real gap for cost-bounded automation.

- **[#49965](https://github.com/openai/codex/discussions/49965) — Windows/Dot: local Codex browser works, Dot task cannot control it**
  Looking for a recovery path when Computer Use works in a normal local chat but not in Dot-started Windows tasks. Echoes the Dot/desktop split seen in [#51731](https://github.com/openai/codex/issues/51731).

### Show and tell

- **[#56176](https://github.com/openai/codex/discussions/56176) — Agent Relay Check v0.1.4: does a fresh session actually receive `AGENTS.md`?**
  A repeatable marker-in / marker-out methodology for verifying instruction-path propagation after nested `AGENTS.md` or override changes. The most practically useful community artifact this cycle.

- **[#57591](https://github.com/openai/codex/discussions/57591) — toli: let Codex and Claude Code collaborate across machines**
  Apache-2.0 tool enabling direct agent-to-agent communication across different developers' machines, motivated by divergent context during shared debugging.

- **[#57524](https://github.com/openai/codex/discussions/57524) — BankBridge: read-only bank/card/investment access via remote MCP + OAuth**
  `codex mcp add bankbridge --url …` — demonstrates dynamic client registration and OAuth in the MCP flow. Notable as a reference implementation for secure remote MCP.

- **[#57463](https://github.com/openai/codex/discussions/57463) — Handoff Kit: select the exact saved task before continuing**
  Addresses the "read my latest handoff" ambiguity when multiple tasks are unfinished in one workspace — a lightweight convention for session resumption.

- **[#57348](https://github.com/openai/codex/discussions/57348) — Reset NE: tracker and forecast for Codex reset notices**
  Aggregates public reset updates, calendar, history, and an experimental forecast — indicates rate-limit resets are frequent enough to warrant third-party tracking.

- **[#56429](https://github.com/openai/codex/discussions/56429) — sports-video-coach: Codex reviews your own sports video**
  A non-coding use case producing timestamped clips, drills, and targets; shows the agent being used well outside software engineering.

- **[#56564](https://github.com/openai/codex/discussions/56564) — Haru Café: a company before a café, built with Codex**
  Long-form build story exploring agent continuity of identity and relationships.

*Filtered:* [#49577](https://github.com/openai/codex/discussions/49577) is off-topic SEO content, not a genuine Codex discussion.

---

## 6. Feature Request Trends

1. **First-class terminal editing (undo/redo)** — [#2379](https://github.com/openai/codex/issues/2379) remains the top-voted enhancement (👍37) after 14 months. Users want composer parity with standard readline/editor behavior.
2. **Visible usage and rate-limit state** — [#25561](https://github.com/openai/codex/issues/25561) asks for a persistent remaining-usage indicator; the existence of community trackers like [#57348](https://github.com/openai/codex/discussions/57348) confirms the demand signal.
3. **Frictionless MCP onboarding** — [#54925](https://github.com/openai/codex/issues/54925) proposes a "Connect with ChatGPT" one-click OAuth flow for third-party MCP apps; [#57524](https://github.com/openai/codex/discussions/57524) already demonstrates the pattern manually.
4. **Session/task continuity and handoff** — Repeated asks for reliable resumption: precise task selection ([#57463](https://github.com/openai/codex/discussions/57463)), reproducible `AGENTS.md` verification ([#56176](https://github.com/openai/codex/discussions/56176)), and session navigation (partially addressed by 0.165.0's repo filter).
5. **Cross-agent interoperability** — [#57591](https://github.com/openai/codex/discussions/57591) and the sub-agent PRs ([#57615](https://github.com/openai/codex/pull/57615), [#57586](https://github.com/openai/codex/pull/57586)) point at demand for coordinated multi-agent, multi-machine workflows.
6. **Granular, honored permission controls** — [#54574](https://github.com/openai/codex/issues/54574) and [#54962](https://github.com/openai/codex/issues/54962) both report explicit human authorization being ignored; users want approvals that actually take effect and are auditable.

---

## 7. Developer Pain Points

- **Windows is the dominant reliability gap.** Sandbox/MXC defects ([#50915](https://github.com/openai/codex/issues/50915), [#56264](https://github.com/openai/codex/issues/56264), [#57610](https://github.com/openai/codex/issues/57610)), extension startup failures ([#56655](https://github.com/openai/codex/issues/56655), [#57344](https://github.com/openai/codex/issues/57344), [#57368](https://github.com/openai/codex/issues/57368)), and updater breakage ([#51588](https://github.com/openai/codex/issues/51588), [#56788](https://github.com/openai/codex/issues/56788)) account for the majority of high-comment issues.
- **Environment probing is too brittle.** MXC fails on unrelated locked BitLocker volumes and dies when `SYSTEMROOT` is absent — sandbox helpers assume a fuller environment than they actually receive.
- **Upgrade path has no safety net.** Users are self-serving rollbacks (`26.1007.21434` works, `26.1021.22027` doesn't). There's no visible guidance on pinning or automatic rollback for a broken extension release.
- **Duplicate reports inflate triage cost.** At least four issues describe the same VS Code startup failure; the community is doing the deduplication work manually — the `codex doctor` PRs ([#57606](https://github.com/openai/codex/pull/57606), [#57597](https://github.com/openai/codex/pull/57597)) are a good step toward better first-pass diagnostics.
- **Permission model doesn't match user intent.** Multiple reports of explicitly granted approvals being ignored (macOS Git, Windows auto-review) erode trust in the sandbox — arguably worse than a hard denial.
- **Resource hygiene in the desktop app.** Idle Git scans pegging CPU ([#57111](https://github.com/openai/codex/issues/57111)) and a 1,024 FD soft limit in the Unix app-server ([#57587](https://github.com/openai/codex/issues/57587)) suggest long-running process limits haven't been tuned for real workloads.
- **Model-family transitions carry risk.** [#52450](https://github.com/openai/codex/issues/52450) blocks GPT-6.1 for affected accounts while the 0.165.0 release simultaneously promotes GPT-6.1 Terra into the catalogs — a mismatch worth closing before wider rollout.

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-10-28

## Today's Highlights
Three release channels advanced today: stable **v0.66.0**, **v0.67.0-preview.0**, and nightly **v0.67.0-nightly.20261028**, bringing Android/Termux ripgrep path fixes and CRLF-safe diffing. Issue traffic continues to concentrate on **agent/subagent reliability** — false `GOAL` success after `MAX_TURNS`, generalist-agent hangs, and 400 errors under large tool counts dominate the top of the queue. Community PRs this cycle are largely quality-of-life fixes: cancellation handling, `\n` escaping correctness, Unicode-safe truncation, and terminal resize performance.

## Releases

**[v0.67.0-nightly.20261028.gfa5c17365](https://github.com/google-gemini/gemini-cli/releases)**
- `fix(core)`: allow trusted system paths on Android (Termux) for ripgrep resolution ([#21836](https://github.com/google-gemini/gemini-cli/pull/29884), @amelidev) — unblocks Termux users whose binary resolution was previously rejected.
- `fix(core)`: normalize CRLF line endings before diffing in `getDiffContextSnippet` (@jesussamuel-byte) — prevents spurious diff context on Windows/CRLF files.

**[v0.67.0-preview.0](https://github.com/google-gemini/gemini-cli/releases)**
- Rolls up the v0.65.0 changelog, bumps the nightly baseline to `0.67.0-nightly.20261020.gb041de017`, and includes the v0.66.0-preview changelog.

**[v0.66.0](https://github.com/google-gemini/gemini-cli/releases)**
- `ROADMAP.md` updated to drop references to the outdated 2025 roadmap ([#29723](https://github.com/google-gemini/gemini-cli/pull/29723), @DavidAPierce).
- Auto-generated changelog for v0.65.0-preview.0 plus routine version bumps.

## Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent recovery after MAX_TURNS reported as GOAL success** (P1, 13 comments, 👍2)
   The `codebase_investigator` subagent returns `status: "success"` / `Termination Reason: "GOAL"` even when it exhausted its turn budget before doing any analysis. This is the most-commented issue of the day because it silently corrupts agent-level observability — callers can't distinguish "done" from "gave up."

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs** (P1, 8 comments, 👍8)
   Highest-reaction issue in the queue. Any deferral to the generalist agent hangs indefinitely (even folder creation); users work around it by forbidding subagent use. Sustained 👍 growth signals broad reproduction.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing & post-execution intent routing** (P2, 9 comments)
   Architectural proposal to lean into Gemini 3's native bash affinity while keeping safety via sandbox + intent routing after execution. A key design thread for how much shell freedom the model gets.

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini does not use skills and sub-agents enough** (P2, 7 comments)
   Anecdotal but widely echoed: custom skills/subagents only fire when explicitly instructed. Directly undermines the extensibility story the team is building.

5. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with large tool counts** (P2, 3 comments)
   Exceeding the tool budget produces a hard 400 instead of graceful scoping. (Title and body disagree on 128 vs. 400 tools — worth reconciling, since the threshold matters for MCP-heavy setups.)

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides (e.g. `maxTurns`)** (P2, 4 comments)
   The `AgentRegistry` merges config correctly at init, but the Browser Agent bypasses it. A config-trust bug rather than a capability gap.

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland** (P1, 4 comments)
   Reports a `GOAL` termination despite failure — the same misleading-termination pattern as #22323, now on Linux/Wayland desktops.

8. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should stop/discourage destructive behavior** (P2, 3 comments, 👍1)
   Model reaches for `git reset` / `--force` where safer alternatives exist, and lacks guardrails around stateful resources like databases.

9. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — Assess AST-aware file reads, search, and mapping** (EPIC, 7 comments)
   Proposes AST-aware tools to cut misaligned reads, turn count, and token noise. Paired with [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) and [#22747](https://github.com/google-gemini/gemini-cli/issues/22747) (tilth/glyph/ast-grep candidates).

10. **[#22466](https://github.com/google-gemini/gemini-cli/issues/22466) — Incorrect `\n` escape behavior** (P2, 2 comments)
    Naive `\n` workaround logic corrupts string literals; now has a matching implementation PR (#29989), so this one is likely to close soon.

## Key PR Progress

1. **[#29977](https://github.com/google-gemini/gemini-cli/pull/29977) — `fix(core)`: honor cancellation in `read_background_output`** (P2, help wanted)
   The tool ignored its abort signal: cancelled calls kept sleeping for the full `delay_ms` and then read the log anyway. Now rejects with `AbortError` up front.

2. **[#29989](https://github.com/google-gemini/gemini-cli/pull/29989) — `fix(core,cli)`: incorrect `\n` escaping** (P2, maintainer)
   Fixes multiple naive escape/unescape paths across core and CLI that corrupted valid `\n` sequences inside string literals, regexes, and code spans during edits.

3. **[#29982](https://github.com/google-gemini/gemini-cli/pull/29982) — `fix(core)`: recognize dotted Gemini 3 models**
   `supportsMultimodalFunctionResponse()` used `startsWith('gemini-3-')`, missing dotted model names when dynamic model configuration is disabled — silently degrading multimodal function responses.

4. **[#29980](https://github.com/google-gemini/gemini-cli/pull/29980) — `feat(cli)`: `gemini extensions validate <path>`**
   Adds pre-install/pre-publish validation of `gemini-extension.json`, including context file path security checks, plus evaluation tests.

5. **[#29987](https://github.com/google-gemini/gemini-cli/pull/29987) — `fix(core)`: normalize leading slashes in `@` autocomplete**
   Makes `@/`, `@/src`, and `@./package.json` resolve correctly instead of returning no completions.

6. **[#29993](https://github.com/google-gemini/gemini-cli/pull/29993) — `fix(ui)`: surrogate pairs in `sanitizeForDisplay`**
   Truncation by UTF-16 code units could split an emoji's surrogate pair and drop the character; now truncates on code-point boundaries.

7. **[#29965](https://github.com/google-gemini/gemini-cli/pull/29965) — `fix(cli)`: ExpandableText truncation and toggle behavior**
   Companion Unicode fix plus toggle-state correctness in collapsed labels.

8. **[#29904](https://github.com/google-gemini/gemini-cli/pull/29904) — `fix(cli)`: bound inline history re-render on terminal width change**
   Directly targets [#21924](https://github.com/google-gemini/gemini-cli/issues/21924)'s flicker/perf complaints by bounding resize work in the inline renderer.

9. **[#29988](https://github.com/google-gemini/gemini-cli/pull/29988) — `fix(vscode-ide-companion)`: track all Disposables** (see also [#29994](https://github.com/google-gemini/gemini-cli/pull/29994))
   A stray pair of parentheses turned two registrations into a comma expression, leaking the `gemini.diff.accept` and workspace-folder listeners. Two independent community PRs converge on the same fix.

10. **[#29995](https://github.com/google-gemini/gemini-cli/pull/29995) — `docs(readme)`: clarify scope, limitations, and tool landscape**
    Consolidates three separate `[!NOTE]` banners into one scannable disclaimer plus a tool-selection guide — useful positioning work as the Google CLI surface expands.

## Feature Request Trends

- **Agent observability and control.** Recurring asks for truthful termination reasons, subagent trajectories surfaced via `/chat share` ([#22598](https://github.com/google-gemini/gemini-cli/issues/22598)), and subagent context included in `/bug` reports ([#21763](https://github.com/google-gemini/gemini-cli/issues/21763)).
- **Context frugality.** AST-aware reads/search/mapping ([#22745](https://github.com/google-gemini/gemini-cli/issues/22745), #22746, #22747) and "Tactful Extraction" surgical read hierarchies ([#19561](https://github.com/google-gemini/gemini-cli/issues/19561)) aim to shrink the ~36.6k-token per-turn baseline.
- **Persistent, out-of-context task state.** Replace `WriteToDo` with file-based CRUD ([#18836](https://github.com/google-gemini/gemini-cli/issues/18836)) and experiment with native file tools for the tracker ([#21000](https://github.com/google-gemini/gemini-cli/issues/21000)).
- **Safety and sandboxing.** Sandboxed bash with intent routing (#19873), discouraging destructive git/DB operations (#22672), and per-workspace rather than global policy ([#18397](https://github.com/google-gemini/gemini-cli/issues/18397)).
- **Subagent ecosystem maturity.** Settings-based subagent discovery ([#18285](https://github.com/google-gemini/gemini-cli/issues/18285)), shared memory / parallel collaboration ([#18287](https://github.com/google-gemini/gemini-cli/issues/18287)), and symlinked agent definitions ([#20079](https://github.com/google-gemini/gemini-cli/issues/20079)).
- **Terminal UI polish.** Flicker-free resize (#21924), ambiguous-width character handling, and Unicode-safe rendering.
- **Self-awareness.** The CLI should accurately describe its own flags, hotkeys, and self-invocation ([#21432](https://github.com/google-gemini/gemini-cli/issues/21432)).

## Developer Pain Points

- **Hangs and misleading success states.** The generalist agent hanging indefinitely (#21409) plus `MAX_TURNS` reported as `GOAL` success (#22323) and the Wayland browser agent reporting `GOAL` on failure (#21983) form a cluster: failures are both blocking *and* invisible.
- **Agent delegation is unreliable in both directions.** Subagents either never trigger spontaneously (#21968) or hang when they do (#21409); Browser Agent settings overrides are ignored entirely (#22267).
- **Tool/config scaling limits.** Large MCP/tool inventories trigger hard 400s (#24246), and models litter workspaces with ad-hoc temp scripts (#23571).
- **Cross-platform paper cuts.** Termux/Android ripgrep resolution, Wayland browser sessions, Windows CRLF diffs, East Asian ambiguous-width terminals, and emoji truncation — a steady stream of environment-specific breakage this cycle.
- **Text-escaping correctness.** Naive `\n` handling (#22466) is a high-frequency, low-tolerance class of bug because it silently mutates user code during edits.
- **Debuggability gap.** `/bug` reports omit subagent internals (#21763), making the above failures hard to triage from user reports alone.

---
*Note: no GitHub Discussions data was included in this dataset, so that section is omitted. PR comment counts were reported as `undefined` for today's items and are not cited.*

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI Community Digest — 2026-10-28

## 1. Today's Highlights

GitHub shipped four releases in 24 hours (v1.0.104–v1.0.105-1), with the headline fix being reliable selection of Copilot Fusion High/Max from long-context sessions. v1.0.104 also changes Fusion managed-model defaults and fixes Autopilot sessions stopping prematurely while background tasks are still attached. Meanwhile, the issue tracker remains dominated by demand for true multi-model/BYOK sessions, ACP feature parity, and persistent platform-specific stability problems on Windows and WSL2.

## 2. Releases

**v1.0.105 / v1.0.105-0 / v1.0.105-1** (2026-10-27)
- Selecting **Copilot Fusion High or Max** in `/model` now works from long-context sessions. The `-0` and `-1` builds appear to be incremental re-cuts of the same fix ("Fixes and changes").

**v1.0.104** (2026-10-27)
- Managed model `fusion` now starts sessions on **Auto** and applies `fusionTier` settings.
- Interactive and SDK Autopilot sessions now **keep waiting for attached background tasks** instead of stopping after `COPILOT_TASK_WAIT_TIMEOUT_SECONDS`; `-p --autopilot` continues to honor the timeout.
- Saved Auto preference handling changes (release note truncated in source).

> Takeaway: a rapid patch cadence around the Fusion model and Autopilot lifecycle. The Autopilot background-task change is the most behaviorally significant for SDK/automation users.

## 3. Hot Issues

1. **[#3709](https://github.com/github/copilot-cli/issues/3709) — Allow `/model` to switch between multiple models, including BYOK/local providers, in one session** (OPEN, 12 comments, 👍34)
   The most-upvoted and most-discussed issue in the window. BYOK pins a session to a single model via `COPILOT_MODEL`, and the picker only lists GitHub-hosted models. Community reaction is strong: users want per-session model switching across hosted and local providers.

2. **[#2760](https://github.com/github/copilot-cli/issues/2760) — Implement proper HTTP retry logic for 429 responses** (CLOSED, 10 comments, 👍2)
   Documents aggressive retry loops (20+ requests/minute) with no backoff. Now closed — worth watching the linked fix, since it likely also affects BYOK throttling behavior.

3. **[#3260](https://github.com/github/copilot-cli/issues/3260) — Copy/Paste broken with SSH + tmux into Windows Server 2025** (OPEN, 9 comments, 👍1)
   A narrow but painful platform regression (since v1.0.47) for remote developers. Sustained comment activity indicates it is still reproducible and unresolved.

4. **[#310](https://github.com/github/copilot-cli/issues/310) — Recurring network error in Copilot CLI** (CLOSED, 7 comments, 👍1)
   Long prompts fail with "Connection error" unless the user runs `/clear`. A long-running issue now closed — useful signal that transport reliability for large prompts is being addressed.

5. **[#1869](https://github.com/github/copilot-cli/issues/1869) — `gpt-5-mini` is not persistently set for future sessions** (OPEN, 7 comments, 👍1)
   `/model` selection reverts to `claude-sonnet-4.6` after restart. A common configuration paper-cut that undermines trust in model settings.

6. **[#4275](https://github.com/github/copilot-cli/issues/4275) — ACP: expose `contextTier` as a session config option** (OPEN, 6 comments, 👍4)
   Interactive `/model` can change context tier mid-session, but ACP clients can only set it at spawn time. A clear parity gap for editor/agent integrations.

7. **[#505](https://github.com/github/copilot-cli/issues/505) — Support MCP server prompts** (OPEN, 5 comments, 👍2)
   Requests `/mcp.<server>.<prompt>` support similar to VS Code. Still open after nearly a year, reflecting steady demand for first-class MCP prompt handling.

8. **[#5197](https://github.com/github/copilot-cli/issues/5197) — Voice server segfaults (signal 11) on WSL2 after 1.0.100** (OPEN, 5 comments, 👍2)
   `/voice` hangs on "Fetching voice models" because the voice runtime crashes on WSL2. A fresh regression tied to a specific release, with reproduction details.

9. **[#4026](https://github.com/github/copilot-cli/issues/4026) — Copilot CLI crashes repeatedly (native runtime) on Windows** (OPEN, 5 comments)
   Reproducible across at least four versions since May 2026. Chronic Windows stability issues continue to erode confidence for a large user segment.

10. **[#4994](https://github.com/github/copilot-cli/issues/4994) — ACP: MCP OAuth re-authorization on every session despite valid token** (OPEN, 4 comments, 👍3)
    Regression from 1.0.88 → 1.0.89: each `--acp` session reopens the OAuth browser page. Blocks headless/automated ACP workflows and is a strong candidate for a quick fix.

## 4. Key PR Progress

No pull requests were updated in the last 24 hours (`Total: 0 items`). No PR-level changes to summarize today.

## 5. Hot Discussions

No discussion data was provided for this window, so this section is omitted.

## 6. Feature Request Trends

- **BYOK and multi-provider flexibility** is the loudest theme: multi-model switching within one session ([#3709](https://github.com/github/copilot-cli/issues/3709)), per-agent provider selection ([#4703](https://github.com/github/copilot-cli/issues/4703)), credential commands in `providers.json`/managed settings ([#4936](https://github.com/github/copilot-cli/issues/4936)), and preserving MCP tool images under BYOK ([#4600](https://github.com/github/copilot-cli/issues/4600)).
- **ACP / headless parity with the interactive CLI**: `contextTier` config ([#4275](https://github.com/github/copilot-cli/issues/4275)), plugin skills in ACP sessions ([#5269](https://github.com/github/copilot-cli/issues/5269)), working `/rename` ([#5115](https://github.com/github/copilot-cli/issues/5115)), and silent OAuth resolution ([#4994](https://github.com/github/copilot-cli/issues/4994)).
- **Enterprise deployment and policy controls**: system-wide Windows installer ([#5129](https://github.com/github/copilot-cli/issues/5129)), `allowedMcpServers` matching fixes ([#4989](https://github.com/github/copilot-cli/issues/4989)), and org-level custom agent visibility ([#3572](https://github.com/github/copilot-cli/issues/3572)).
- **MCP extensibility**: server-provided prompts ([#505](https://github.com/github/copilot-cli/issues/505)) and reliable multimodal tool results ([#4600](https://github.com/github/copilot-cli/issues/4600)).
- **Session lifecycle management**: unarchive/restore sessions ([#3518](https://github.com/github/copilot-cli/issues/3518), 👍11), inline plan annotations ([#4563](https://github.com/github/copilot-cli/issues/4563)), and durable event delivery ([#5100](https://github.com/github/copilot-cli/issues/5100)).
- **Model routing transparency and tuning**: configurable reasoning effort for Auto ([#4560](https://github.com/github/copilot-cli/issues/4560)) and configurable retry/backoff counts ([#3679](https://github.com/github/copilot-cli/issues/3679)).

## 7. Developer Pain Points

- **Model selection is opaque and non-persistent.** Settings revert between sessions ([#1869](https://github.com/github/copilot-cli/issues/1869)), Auto forces `reasoningEffort: null` ([#4560](https://github.com/github/copilot-cli/issues/4560)), and Auto picking GPT-6.1 Sol can hang the Plan → Implementation transition ([#5270](https://github.com/github/copilot-cli/issues/5270)).
- **Retry/throttling behavior wastes time and quota.** Aggressive 429 retries ([#2760](https://github.com/github/copilot-cli/issues/2760)) and Azure BYOK exhausting five retries in ~0.15s ([#3679](https://github.com/github/copilot-cli/issues/3679)) are recurring complaints.
- **Platform reliability gaps.** Windows native crashes across multiple versions ([#4026](https://github.com/github/copilot-cli/issues/4026)), WSL2 voice segfaults ([#5197](https://github.com/github/copilot-cli/issues/5197)), and broken copy/paste over SSH + tmux ([#3260](https://github.com/github/copilot-cli/issues/3260)).
- **Silent failures are worse than errors.** `preToolUse` hooks bypassed under parallel tool calls ([#2893](https://github.com/github/copilot-cli/issues/2893)), dropped MCP images ([#4600](https://github.com/github/copilot-cli/issues/4600)), missing `task_complete` output ([#2482](https://github.com/github/copilot-cli/issues/2482)), and `/rename` reporting false success ([#5115](https://github.com/github/copilot-cli/issues/5115)).
- **Enterprise constraints block adoption.** User-profile installs conflict with security policy ([#5129](https://github.com/github/copilot-cli/issues/5129)), and allow-list matching plus org agent discovery remain unreliable ([#4989](https://github.com/github/copilot-cli/issues/4989), [#3572](https://github.com/github/copilot-cli/issues/3572)).
- **Session durability is fragile.** A single 120s host-ack timeout can render a session unusable until resume ([#5100](https://github.com/github/copilot-cli/issues/5100)), and archived sessions cannot be restored ([#3518](https://github.com/github/copilot-cli/issues/3518)).

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-10-28

## 1. Today's Highlights

OpenCode **v2.2.0** shipped with experimental **Desktop plugins**, opening the desktop client to community-built extensions for the first time. Issue activity remains dominated by the **free-tier limit mega-thread** (#59387, 35 comments) and a cluster of high-severity Windows/WSL regressions. On the PR side, core contributors landed a process-daemon readiness refactor stack and are trimming unused AI SDK provider bundles from `packages/core`.

---

## 2. Releases

### v2.2.0
- **Desktop plugins (experimental)** — create, install, and share desktop GUI plugins. Merged via [#55246](https://github.com/anomalyco/opencode/pull/55246), [#59469](https://github.com/anomalyco/opencode/pull/59469), [#59489](https://github.com/anomalyco/opencode/pull/59489).
- Docs: https://opencode.ai/v2/docs/build/plugins/gui
- Notable: API surface is explicitly experimental, so plugin authors should expect breaking changes before stabilization.

---

## 3. Hot Issues

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — Free tier issues: read this first (35 comments, 👍7)**  
   Canonical triage thread for `Free usage exceeded`, non-resetting daily limits, and Desktop version gating. High comment volume without resolution suggests the free-tier quota path still lacks clear diagnostics — the most active issue in the repo right now.

2. **[#52237](https://github.com/anomalyco/opencode/issues/52237) — Remote MCP servers that fail once are never retried (13 comments)**  
   After a transient failure (notably macOS sleep/wake killing long-lived HTTP connections), the background service pins the MCP server in `failed` state until restart. Restarting the TUI doesn't help because state lives in the daemon. This is a real reliability blocker for anyone depending on remote MCP tooling.

3. **[#52266](https://github.com/anomalyco/opencode/issues/52266) — Windows: console window flashes ~1/sec (8 comments, 👍3)**  
   Missing `CREATE_NO_WINDOW` on spawn causes a visible console flash for every `git.exe` invocation. A re-report of #22414 and #31629 — a long-lived regression that keeps resurfacing, which is why it's drawing 👍.

4. **[#53011](https://github.com/anomalyco/opencode/issues/53011) — `edit` tool duplicates numeric replacement values (8 comments)**  
   Replacing `640` with `900` produced `900900`, with duplication factors observed from 2x to 129x. Silent file corruption in the core edit path — `[triaging]` status but clearly the highest-risk correctness bug in the list.

5. **[#52378](https://github.com/anomalyco/opencode/issues/52378) — Failed subagent reported to parent as success (8 comments)**  
   `finish:"error"` with `MALFORMED_FUNCTION_CALL` and empty content is surfaced as a successful child completion. Marked `[reproduced]`; this corrupts orchestration semantics for anyone building multi-agent workflows.

6. **[#52363](https://github.com/anomalyco/opencode/issues/52363) — OpenAI models disappear when Zen or Go is connected (8 comments, `severity:high`)**  
   ChatGPT OAuth login succeeds and credentials store correctly, but the `openai` provider never registers — nothing selectable in `/models`. Affects all auth methods (browser and headless), so this is a full provider-registry break for OpenAI users.

7. **[#52234](https://github.com/anomalyco/opencode/issues/52234) — Hardcoded port 49374 hangs silently when unavailable (7 comments)**  
   No fallback port, no bind error — the TUI sits on `Starting background server...` forever. Especially painful on WSL2/Hyper-V where the host unpredictably reserves ranges. Cheap fix, high user impact.

8. **[#52154](https://github.com/anomalyco/opencode/issues/52154) — Message history is not isolated per session (7 comments, 👍2)**  
   Up-arrow recalls the last message from whichever session was most recent globally, not the current one. A papercut that compounds for users running many concurrent sessions — exactly the workflow OpenCode encourages.

9. **[#52333](https://github.com/anomalyco/opencode/issues/52333) — `provider.use` deny policies bypassed during hot-reload (`severity:high`)**  
   There is a window during config/plugin reload where denied providers become selectable, and a prompt landing in that window silently routes system prompt + session history to a third-party free provider. A security issue, not just a correctness one.

10. **[#52205](https://github.com/anomalyco/opencode/issues/52205) — Windows Desktop passes WSL UNC paths to Linux server (5 comments)**  
    Selecting a WSL folder returns `\\wsl.localhost\...`, which the Linux-side server can't handle — HTTP 500s and persistent startup crashes. `[reproduced]`; blocks the Windows-Desktop + WSL-server combo entirely.

**Also worth watching:** [#52203](https://github.com/anomalyco/opencode/issues/52203) (Windows TUI survives terminal close, leaks CPU/memory), [#52122](https://github.com/anomalyco/opencode/issues/52122) (Nix desktop arms the production auto-updater), [#52178](https://github.com/anomalyco/opencode/issues/52178) (Zen API CORS only on `/models`, all inference preflights 404).

---

## 4. Key PR Progress

1. **[#59771](https://github.com/anomalyco/opencode/pull/59771) — Remove bundled Cohere, Vercel, Perplexity AI SDK packages**  
   Drops `@ai-sdk/cohere`, `@ai-sdk/vercel`, `@ai-sdk/perplexity` plus their now-redundant provider plugins and the unused `createProviderPlugin` helper. Meaningful bundle/lockfile slimming and a simplification of the provider plugin layer.

2. **[#59700](https://github.com/anomalyco/opencode/pull/59700) / [#59701](https://github.com/anomalyco/opencode/pull/59701) / [#59702](https://github.com/anomalyco/opencode/pull/59702) — process-daemon control stack (jlongster)**  
   Routes stop/ownership through `Control`, replaces polling with readiness signals, and polls every 25 ms on Windows. Ported from `opencode-pty`; directly targets daemon lifecycle flakiness that underlies several of the issues above.

3. **[#53233](https://github.com/anomalyco/opencode/pull/53233) — Honor compaction retained-text budget**  
   Fixes [#43250](https://github.com/anomalyco/opencode/issues/43250) by enforcing the retained-text invariant, permitting an empty recent tail when no complete exchange fits. Long-lived fix (opened 2026-10-04) for context-compaction correctness.

4. **[#59765](https://github.com/anomalyco/opencode/pull/59765) — Make provider `model()` settings optional, unify `Config` type**  
   Removes the mandatory `{}` argument and normalizes `Config` / `LanguageModelOptions` / `Options` naming across providers. Small DX win for anyone writing custom providers.

5. **[#59773](https://github.com/anomalyco/opencode/pull/59773) — Stats: ignore requests rejected before routing**  
   Stops counting `generation.completed` events with `attempts = 0`, which were polluting the `/data` model dimension with phantom models. Cleans up public usage analytics.

6. **[#59437](https://github.com/anomalyco/opencode/pull/59437) — `feat(core): add /goal with a client-side done check`**  
   Alternative to #58508: same idle hook, `/goal` command, and TUI, but no new `session.settled` server event and no `native` flag on the compaction hook. Clients derive "done" from already-synced data — a less invasive design for agentic loop control.

7. **[#59403](https://github.com/anomalyco/opencode/pull/59403) — `refactor(nix): fetch dependencies from bun.lock`**  
   Closes [#49063](https://github.com/anomalyco/opencode/issues/49063); eliminates hand-maintained platform-specific `node_modules` hashes. Removes a recurring Nix packaging maintenance burden.

8. **[#59784](https://github.com/anomalyco/opencode/pull/59784) / [#59783](https://github.com/anomalyco/opencode/pull/59783) / [#59785](https://github.com/anomalyco/opencode/pull/59785) — session title filter, auth list account labels, copy full file from review**  
   Three community UX contributions from `lancy69`, each closing a request (#56843, #59040, #58878). All marked draft pending design approval — worth weighing in on the issues.

9. **[#59630](https://github.com/anomalyco/opencode/pull/59630) — Upgrade Effect to 4.0.6**  
   Picks up the SQLite snowflake-ID rounding fix (Effect-TS/effect#9485). Low-risk dependency bump with a concrete data-integrity payoff.

10. **[#59770](https://github.com/anomalyco/opencode/pull/59770) — TUI: navigate transcript with keypad Home/End**  
    Closes [#55533](https://github.com/anomalyco/opencode/issues/55533) (re-filed after #57114 was closed). Fixes keypad-key handling under the kitty keyboard protocol.

---

## 5. Feature Request Trends

- **Configurability of endpoints and secrets** — `OPENCODE_PASSWORD_FILE` ([#52156](https://github.com/anomalyco/opencode/issues/52156)) and editable base/console API URLs per provider ([#52218](https://github.com/anomalyco/opencode/issues/52218)). Users want container/secret-manager-friendly configuration and self-hosted/alternate gateways.
- **Multi-session ergonomics** — per-session message history ([#52154](https://github.com/anomalyco/opencode/issues/52154)) and filtering session lists by title (PR [#59784](https://github.com/anomalyco/opencode/pull/59784)). A clear signal that heavy users now run many concurrent sessions.
- **CLI surface polish** — distinguishing stored accounts in `auth list` ([#59040](https://github.com/anomalyco/opencode/issues/59040)) and aligning `opencode mini`'s process controls with the main TUI: configurable exit and `ctrl+z` suspend ([#52695](https://github.com/anomalyco/opencode/issues/52695)).
- **Agentic loop control** — `/goal` with a client-side done check ([#59437](https://github.com/anomalyco/opencode/pull/59437)), plus a browser API operation to close tabs ([#59787](https://github.com/anomalyco/opencode/issues/59787), closed) so agents can clean up after themselves.
- **Review/UI affordances** — copy full file contents from review ([#58878](https://github.com/anomalyco/opencode/issues/58878)) and gallery navigation in image preview (#58295).
- **Build provenance / observability** — recording the pinned Electron version in the desktop debug export ([#52125](https://github.com/anomalyco/opencode/issues/52125)) so Nix builds can be distinguished from upstream binaries.

---

## 6. Developer Pain Points

- **Windows is the dominant source of friction.** Console-window flashing ([#52266](https://github.com/anomalyco/opencode/issues/52266)), orphaned TUI processes after terminal close ([#52203](https://github.com/anomalyco/opencode/issues/52203)), `EDITOR` paths containing spaces breaking under Git-Bash ([#52265](https://github.com/anomalyco/opencode/issues/52265)), and WSL UNC paths crashing the Desktop↔server link ([#52205](https://github.com/anomalyco/opencode/issues/52205)). Several are re-reports (e.g. #22414, #31629), which reads as insufficient prioritization rather than novel breakage.
- **Provider/model registry inconsistency.** Models vanish from the picker despite valid credentials ([#52363](https://github.com/anomalyco/opencode/issues/52363), [#52118](https://github.com/anomalyco/opencode/issues/52118)) — auth succeeds but registration doesn't, with no surfaced error.
- **Silent failure instead of an error message.** Hardcoded port `49374` hangs indefinitely ([#52234](https://github.com/anomalyco/opencode/issues/52234)); local plugins without a root entrypoint are skipped without warning ([#52300](https://github.com/anomalyco/opencode/issues/52300)); failed subagents are reported as successes ([#52378](https://github.com/anomalyco/opencode/issues/52378)). The recurring theme is missing diagnostics at failure boundaries.
- **Long-lived connection fragility.** MCP servers never retried after one failure ([#52237](https://github.com/anomalyco/opencode/issues/52237)) — sleep/wake is the common trigger. Related to the process-daemon readiness work in PRs #59700–#59702.
- **Security policy gaps under reload.** Deny-lists bypassable during hot-reload ([#52333](https://github.com/anomalyco/opencode/issues/52333)) erodes trust in `experimental.policies` for anyone running untrusted plugins.
- **Template `$`-sequence handling.** Formatter args and prompt templates corrupt paths containing `$&`, `$'`, `` $` ``, `$$` ([#52314](https://github.com/anomalyco/opencode/issues/52314)) — a `String.replace` footgun repeated across several call sites.
- **Free-tier confusion.** A 35-comment mega-thread ([#59387](https://github.com/anomalyco/opencode/issues/59387)) for quotas that appear not to reset suggests the messaging and/or the reset logic needs work before more users hit it.

---

*Note: no Discussions data was provided for this period, so that section is omitted.*

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-10-28

## 1. Today's Highlights
No releases landed in the last 24 hours, but maintainers had a heavy triage day: a cluster of P0 and `durable` issues were closed around session resume losing built-in tools, OpenAI Responses tool replay, and Bedrock schema limits. On the open side, two long-running reliability concerns remain prominent — permanent session wedging at the context ceiling on reasoning models (#9409) and Anthropic streaming/tool-registration fragility (#10390, #11376).

## 2. Releases
_No new releases in the last 24h._

## 3. Hot Issues

1. **#10953 — OpenAI login fails with ChatGPT Business, legacy Codex login works** `[CLOSED] [no-action]` · 11 comments · 👍3
   The most-commented issue of the day: `3p_delegated_access_policy_denied` blocks regular OpenAI subscription login for Business workspaces, while the legacy Codex path succeeds. Closed as no-action, but auth/policy mismatch for enterprise workspaces is likely to resurface.
   [Link](https://github.com/earendil-works/pi/issues/10953)

2. **#9409 — Sessions wedge permanently at the context ceiling on reasoning models** `[OPEN]` · 10 comments · 👍3
   Every request returns `stopReason: "length"` with 16 output tokens and auto-compaction cannot recover. This is one of the highest-impact open bugs, affecting any long-running reasoning-model session.
   [Link](https://github.com/earendil-works/pi/issues/9409)

3. **#10390 — Anthropic SSE parser mishandles CRLF split across chunks** `[OPEN] [bug]` · 7 comments
   When `\r` and `\n` arrive in separate network chunks, the parser flushes an empty event and aborts the turn. A classic streaming edge case with outsized blast radius for Anthropic users.
   [Link](https://github.com/earendil-works/pi/issues/10390)

4. **#11376 — Anthropic: removing an initial tool mid-session breaks all later requests** `[OPEN] [inprogress, prio:p0]` · 4 comments
   Reloading an extension that stops registering a tool produces `tool_reference_unresolved` on every subsequent call. Highlights a gap between extension lifecycle and provider tool declaration state.
   [Link](https://github.com/earendil-works/pi/issues/11376)

5. **#11460 — Resume drops all built-in tools when the session branch is truncated (orphan parentId after ENOSPC)** `[CLOSED] [prio:p0, fix-available]` · 4 comments
   Disk-full conditions could leave the session graph with an orphan parent, silently removing `read/bash/edit/...` from resumed sessions. A serious data-integrity-style failure with a fix now available.
   [Link](https://github.com/earendil-works/pi/issues/11460)

6. **#11415 — openai-responses: function_call items from other models replayed without namespace** `[CLOSED] [prio:p0]` · 3 comments
   Tools declared via `additional_tools` caused OpenAI 400s because replayed calls lacked their namespace. Reproduced live by an agent against `api.openai.com`; a high-signal provider-compat fix.
   [Link](https://github.com/earendil-works/pi/issues/11415)

7. **#11416 — Bedrock: request fails with more than 16 optional parameters** `[CLOSED] [prio:p0]` · 2 comments
   Bedrock rejects schemas with >16 union/optional parameters, breaking otherwise valid tool definitions. Worth tracking because it imposes a hard tool-schema budget on Bedrock users.
   [Link](https://github.com/earendil-works/pi/issues/11416)

8. **#11500 — Compaction/summarization requests share no prefix with live turns** `[CLOSED] [untriaged]` · 2 comments
   Every compaction pays a full prefill because the summarizer starts from a fresh prompt without system prompt/tool declarations. Directly motivates PR #11499; big cost/latency implication for long sessions.
   [Link](https://github.com/earendil-works/pi/issues/11500)

9. **#11493 — Codex: full cache miss after idle despite persistent-cache guarantee** `[CLOSED] [untriaged]` · 2 comments
   Contradicts OpenAI's stated guarantee for logged-in Codex users, suggesting Pi may be altering request shape or identifiers across resumes. Cache behavior is a recurring cost concern for resumed sessions.
   [Link](https://github.com/earendil-works/pi/issues/11493)

10. **#11433 — pi-env: exec hangs when a pipe-reader or waiter thread fails to start** `[OPEN] [bug]` · 2 comments
    A thread-start failure (e.g., cgroup task limit) panics instead of returning an error, leaving the exec without a reply and cancellation broken. A rare but severe daemon-level hang.
    [Link](https://github.com/earendil-works/pi/issues/11433)

_Honorable mentions:_ #7738 (concurrent compactions share one `AbortController` → crash), #11497 (`SqliteStorage` ID minting across multiple openers), #11489 (`pi update` misdetects vite+ installs).

## 4. Key PR Progress

1. **#11499 — feat(compaction): prefix-preserving summarization calls** `[CLOSED]`
   Reworks summarization to pass the system prompt and tool declarations, replaying conversation as-is so compaction shares a token prefix with live turns. The direct fix for #11500 and the most consequential perf change of the day.
   [Link](https://github.com/earendil-works/pi/pull/11499)

2. **#11498 — docs(coding-agent): restore the full extensions guide** `[CLOSED]`
   Restores ~3,100 lines of extension API reference and tool guidance that a prior docs refresh cut down to 130 lines, corrected against current source. Important for the extension ecosystem.
   [Link](https://github.com/earendil-works/pi/pull/11498)

3. **#11496 — fix(ai): cap OpenRouter reasoning tokens** `[CLOSED]`
   Sends nested `reasoning.max_tokens`, omits `reasoning.effort` when a budget is present (OpenRouter rejects both), and clamps to preserve ≥1,024 answer tokens.
   [Link](https://github.com/earendil-works/pi/pull/11496)

4. **#11494 — fix(ai): detect LiteLLM ContextWindowExceededError as context overflow** `[CLOSED]`
   Recognizes LiteLLM's pre-call 400 (`Max Input Tokens=…, Got=…`) as overflow so auto-compaction can trigger instead of surfacing a raw provider error.
   [Link](https://github.com/earendil-works/pi/pull/11494)

5. **#11491 — fix(coding-agent): detect vite+ global installs and self-update via vp** `[CLOSED]`
   Adds a `vite-plus` install method so `pi update` stops creating a second divergent global install under vite+'s hash-keyed directory. Resolves #11489.
   [Link](https://github.com/earendil-works/pi/pull/11491)

6. **#11434 — fix(ai): use OpenCode protocol-specific endpoints** `[CLOSED]`
   Switches `opencode` / `opencode-go` traffic from the shared inference base to documented per-protocol endpoints (matching models.dev). Closes #11132.
   [Link](https://github.com/earendil-works/pi/pull/11434)

7. **#11486 — fix(coding-agent): keep session memory in line with the file when a write fails** `[CLOSED]`
   Prevents in-memory entries from being committed as the current leaf when the disk write fails (ENOSPC/EBUSY/ETIMEDOUT), avoiding orphaned parents on resume. Complements #11460.
   [Link](https://github.com/earendil-works/pi/pull/11486)

8. **#11471 — feat(coding-agent): inline `ctx.ui.custom()` components report blocked program status** `[CLOSED]`
   Lets custom dialogs opt into OSC 7501 blocked status (e.g., approval prompts), so terminals no longer show `working` while waiting on the user. Closes #11428.
   [Link](https://github.com/earendil-works/pi/pull/11471)

9. **#11482 — fix(coding-agent): pass thinking level to branch summarization** `[CLOSED]`
   `/tree` → Summarize was ignoring the session thinking level; reasoning is now included for OpenAI Responses/Azure reasoning models.
   [Link](https://github.com/earendil-works/pi/pull/11482)

10. **#11487 — fix(tui): fall back to raw LaTeX when display math exceeds content width** `[CLOSED]`
    Fixes broken multi-row math rendering where only the overflowing row wrapped and subsequent rows misaligned. Part of a broader TUI correctness push alongside #11422 (Kitty F-key decoding) and #11477 (scrollbar perf).
    [Link](https://github.com/earendil-works/pi/pull/11487)

_Also notable:_ #11348 (page-key support across all scrolling lists, still open) and #10528 (Nix packaging refactor, in progress).

## 5. Hot Discussions

**Show and tell**
- **#11488 — AY-PI: per-prompt tier routing and tool scoping (virtual model + Jev classifier)** · 1 comment · 👍1
  An extension demonstrating Pi's virtual model and classifier APIs: classifies each prompt into CHAT/SUGGEST/REVIEW/PLAN/CODE plus a low/medium/high tier, then scopes tools so only CODE keeps `edit`/`write`/`bash`.
  [Link](https://github.com/earendil-works/pi/discussions/11488)
- **#11476 — A Cairn-style shared board for coding agents (built for omp)** · 👍1
  Models problem-solving as state-space search over a shared board where agents coordinate via facts, intents, and hints; reports a 3rd-place finish in the Tencent Cloud AI Penetration Testing Challenge.
  [Link](https://github.com/earendil-works/pi/discussions/11476)

## 6. Feature Request Trends

- **Compaction & context economics** — Requests center on making compaction cheaper and smarter: prefix-preserving summarization (#11500 / PR #11499), surfacing multiple context-window variants per model (#10893), and recovery from context-ceiling wedges (#9409).
- **Durable task orchestration** — A steady stream of `pi-durable` requests: resume `watchEvents` from a last-seen ID (#11327), let extensions start tasks per turn (#11141), and let tasks hold the conversation busy (#11332).
- **Provider protocol fidelity** — Multiple asks to match each provider's documented endpoints and error semantics: OpenCode protocol endpoints (#11132), LiteLLM overflow detection (#11494), OpenRouter reasoning budgets (#11496), and namespace-correct OpenAI Responses replay (#11415).
- **TUI interaction quality** — Selection/copy semantics for soft wraps (#8019), configurable page keys honored everywhere (#11348), Kitty function-key decoding (#11422), and inline custom dialogs reporting blocked status (#11428).
- **Install/update correctness** — Detect non-npm global installs (vite+, #11489) and suppress npm `audit`/`fund` noise during `pi install` (#11469).
- **Extension sandbox/authority clarity** — Whether parent cancellation always propagates through `ctx.executeTool()` even with an explicit `AbortSignal` (#11484).

## 7. Developer Pain Points

- **Sessions that cannot recover.** The recurring theme is state integrity: wedging at the context ceiling (#9409), resume dropping built-in tools after a truncated branch (#11460), in-memory session state diverging from disk on write failure (#11486), and `SqliteStorage` ID collisions with multiple openers (#11497). Developers lose work or are forced to restart sessions.
- **Provider-specific fragility.** Anthropic SSE chunk boundaries (#10390), unresolvable tool references after extension reload (#11376), Bedrock's 16-optional-parameter ceiling (#11416), and OpenAI Business auth policy denials (#10953) all present as opaque failures far from the root cause.
- **Cache and cost surprises.** Full cache misses after idle on Codex (#11493) and full-prefill compactions (#11500) mean users pay significantly more than expected for resumed or long-running sessions.
- **Compaction/concurrency bugs.** Concurrent compactions sharing a single `AbortController` (#7738) and durable generation infinite loops after host restart (#11139) indicate re-entrancy and lifecycle guards are still thin in critical paths.
- **Install/update ambiguity.** Misdetected install methods (#11489) silently produce duplicate global installs, and npm's own advisory output (#11469) adds noise users can't safely act on.
- **Environment/daemon hangs.** `pi-env` panicking on thread-start failure (#11433) leaves execs with no reply and broken cancellation — a low-frequency but hard-to-diagnose class of failure.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-10-28

## 1. Today's Highlights

Release cadence remains high: `v0.26.2` shipped with a breaking change to Managed Agent Web Shell controls, followed by `v0.26.3-preview.0` adding opt-in memory extraction cadence and XML tool-call recovery fixes. Meanwhile, two P1 correctness bugs landed in the tracker — a persistent Anthropic 400 caused by a thinking part with a missing signature, and a `sed` simulation mismatch in the shell tool. CI/release reliability continues to dominate discussion volume, with the Managed Agent gate issue at 68 comments and the dual-path architecture proposal at 72.

---

## 2. Releases

**[v0.26.3-preview.0](https://github.com/QwenLM/qwen-code/releases)**
- `feat(memory)`: opt-in extraction cadence after a no-op run ([#13571](https://github.com/QwenLM/qwen-code/pull/13571))
- `fix(core)`: recover outer XML calls with quoted candidates — continues the XML tool-call recovery work tracked in [#13787](https://github.com/QwenLM/qwen-code/issues/13787)

**[v0.26.2](https://github.com/QwenLM/qwen-code/releases)**
- **Breaking:** `fix(managed-agent)!` simplifies Web Shell controls and retires cwd submissions ([#15616](https://github.com/QwenLM/qwen-code/pull/15616)) — callers relying on cwd submission semantics must migrate.

**[v0.26.2-preview.1](https://github.com/QwenLM/qwen-code/releases)**
- `feat(managed)`: session permission rules and Auto approval ([#15448](https://github.com/QwenLM/qwen-code/pull/15448))
- `fix(cli)`: harden serve token resolution

**[sdk-typescript-v0.1.24](https://github.com/QwenLM/qwen-code/releases)**
- Bundles CLI `0.26.2` (built from the same branch/ref as the SDK). Note the release notes also reference a bundled `0.26.1`, suggesting a skipped/renumbered cut.

**Release-process note:** `v0.26.2-preview.0` publish failed ([#15672](https://github.com/QwenLM/qwen-code/issues/15672), closed), and the fix is in review as [PR #15730](https://github.com/QwenLM/qwen-code/pull/15730).

---

## 3. Hot Issues

1. **[#15742](https://github.com/QwenLM/qwen-code/issues/15742) — P1: native Anthropic session 400s forever when a persisted thinking part lost its signature.** A single assistant turn without `thoughtSignature` bricks the session with an unrecoverable `invalid_request_error`; there is no self-healing path. High severity for anyone on the native Anthropic endpoint.

2. **[#15675](https://github.com/QwenLM/qwen-code/issues/15675) — P1: `sed` edit simulation diverges from real `sed`.** The in-process simulation omits the `s` and `u` flags, so `.` fails to match `\r` and whole emoji. This is a correctness bug in the shell tool's fast path, not just a cosmetic mismatch.

3. **[#12380](https://github.com/QwenLM/qwen-code/issues/12380) — proposal(serve): Managed Agent dual-path architecture and staged delivery.** The highest-traffic issue in the window (72 comments), carrying a verified Stage F delivery status as of 2026-10-24. Effectively the design spine for the managed-agent roadmap.

4. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724) — Main CI failed: Managed Agent gate, no test identified.** 68 comments and `autofix/skip`, updated daily since 2026-10-19. Persistent "no test identified" failures are eroding trust in the gate as a signal.

5. **[#15686](https://github.com/QwenLM/qwen-code/issues/15686) — P2: Active Goal blocks ordinary user input.** Enter-steered messages and pasted text queue until the Goal leaves active state, contradicting the documented steering behavior. Six comments already; a UX/state-machine conflict rather than a simple bug.

6. **[#15741](https://github.com/QwenLM/qwen-code/issues/15741) — P2: MCP duplicate tool names discard the entire server.** One collision silently drops every tool from that server and reports a misleading "no tools" error. Reproduced with a Unity MCP server on 0.26.2.

7. **[#15738](https://github.com/QwenLM/qwen-code/issues/15738) — P2: team-mode messages never reach the leader in ACP mode.** With `qwen serve --web` and `experimental.agentTeam`, no teammate message is delivered and the team dies with the short-lived `--acp` child. Blocks the multi-agent story in Web Shell.

8. **[#15690](https://github.com/QwenLM/qwen-code/issues/15690) — P2: Homebrew 0.26.1 cannot rewrite `libopentui.dylib` install name.** `@opentui/core-darwin-arm64` lacks `-headerpad_max_install_names`, so `brew upgrade` emits a linkage error on Apple Silicon. Packaging-level breakage for macOS users.

9. **[#15645](https://github.com/QwenLM/qwen-code/issues/15645) — P2: daemon skills catalog stays stale after on-disk skill creation.** The runtime skills endpoint sees the new project skill but the configuration endpoint does not — a split-brain that will confuse management UIs.

10. **[#15697](https://github.com/QwenLM/qwen-code/issues/15697) — P2: web-shell history-viewport smoke test burns its 120s budget.** Same spec and assertion failing across two unrelated PRs that don't touch `packages/web-shell/`. Concrete flake data makes this one actionable.

*Also notable:* [#13024](https://github.com/QwenLM/qwen-code/issues/13024) (IO Intelligence provider preset) is the only issue in the set with meaningful positive reaction at **10 👍**; [#13078](https://github.com/QwenLM/qwen-code/issues/13078) (dependency CVE audit failure, 33 comments) remains open and needs triage; [#15499](https://github.com/QwenLM/qwen-code/issues/15499) tracks the managed-runtime `v0.26.1` preflight stop.

---

## 4. Key PR Progress

1. **[#15745](https://github.com/QwenLM/qwen-code/pull/15745) — feat(managed): independent worktrees for main sessions.** Managed main Sessions can opt into a per-session Git worktree; admission freezes a local commit and records the working directory before Runtime start. The enabling primitive for parallel managed work.

2. **[#15730](https://github.com/QwenLM/qwen-code/pull/15730) — fix(release): use the job token for the publish checkout.** Directly targets the publish-stage release failures, keeping the PAT only on the branch push and GitHub release steps.

3. **[#15746](https://github.com/QwenLM/qwen-code/pull/15746) — fix(verify-pr): bind reports to their round snapshot.** Prevents a newer verification round in another artifact directory from retroactively changing how an earlier report is evaluated. Review-integrity fix.

4. **[#15388](https://github.com/QwenLM/qwen-code/pull/15388) — feat(ci): bound autofix takeover with a round budget.** `@qwen-code /takeover for N` (1–99) records a window-scoped round budget. Addresses the "autofix never converges" failure mode.

5. **[#15435](https://github.com/QwenLM/qwen-code/pull/15435) — feat(web-shell): show tool arguments and results in expanded cards.** Expanded tool cards now render call arguments and full results for tools without a built-in detail view; JSON is pretty-printed. Meaningful transparency improvement in the transcript.

6. **[#14613](https://github.com/QwenLM/qwen-code/pull/14613) — feat(cli): enable LSP through a user-scoped setting and env pair.** Makes the experimental LSP code-intelligence service reachable without a CLI flag; the disable switch takes precedence over enable.

7. **[#15681](https://github.com/QwenLM/qwen-code/pull/15681) — fix(core): don't treat fd duplication (`2>&1`) as a file write.** Fixes false-positive permission prompts for a very common shell idiom.

8. **[#14670](https://github.com/QwenLM/qwen-code/pull/14670) — fix(core): split shell commands on bash's separators, not JS whitespace.** `/\s/` matches characters bash treats as ordinary word characters (`\r`, `\v`, `\f`, `\u00a0`), which could mis-split commands used in permission analysis.

9. **[#14677](https://github.com/QwenLM/qwen-code/pull/14677) — fix(core): treat a reassigned `HOME` as dynamic for `cd` and `~`.** Closes a permission-analysis hole where `HOME=/x; cd` was certified against the wrong path.

10. **[#15358](https://github.com/QwenLM/qwen-code/pull/15358) — fix(cli): report max session turns in JSON result instead of replaying the last answer.** `BaseJsonOutputAdapter` now latches `MaxSessionTurns` and emits a real error rather than a false success. Important for CI consumers parsing JSON output.

*Related in flight:* the sdk-java lock-scoping series ([#15274](https://github.com/QwenLM/qwen-code/pull/15274), [#15275](https://github.com/QwenLM/qwen-code/pull/15275), [#15286](https://github.com/QwenLM/qwen-code/pull/15286)) converges on the same design — seed a job-local Maven repo, hold the host lock only for cache write-back. Consolidating them would reduce review load.

---

## 5. Hot Discussions

No GitHub Discussions data was provided for this window, so this section is omitted.

---

## 6. Feature Request Trends

- **Provider preset expansion and compatibility.** [#13024](https://github.com/QwenLM/qwen-code/issues/13024) (io.net preset, 10 👍) and [#12072](https://github.com/QwenLM/qwen-code/issues/12072) (`X-OpenRouter-Title` vs `X-Title`) point at a steady demand for first-class presets plus correct vendor-specific headers.
- **Managed Agent / multi-agent maturity.** Dual-path architecture ([#12380](https://github.com/QwenLM/qwen-code/issues/12380)), independent session worktrees ([#15745](https://github.com/QwenLM/qwen-code/pull/15745)), session permission rules ([#15448](https://github.com/QwenLM/qwen-code/pull/15448)), and team-mode delivery in ACP ([#15738](https://github.com/QwenLM/qwen-code/issues/15738)). This is the largest coherent feature cluster.
- **Web Shell interaction design.** Archived conversations out of the persistent sidebar ([#15549](https://github.com/QwenLM/qwen-code/issues/15549)), a configurable simple input mode ([#15647](https://github.com/QwenLM/qwen-code/issues/15647)), composer polish ([#15691](https://github.com/QwenLM/qwen-code/issues/15691)), and tool-card detail ([#15435](https://github.com/QwenLM/qwen-code/pull/15435)).
- **Context and token management.** Dynamic tool-output truncation under context pressure ([#2566](https://github.com/QwenLM/qwen-code/issues/2566)) and memory extraction cadence (#13571) are both about spending context deliberately rather than reactively.
- **Shell-tool fidelity and permission correctness.** `sed` simulation parity ([#15675](https://github.com/QwenLM/qwen-code/issues/15675)), fd duplication ([#15681](https://github.com/QwenLM/qwen-code/pull/15681)), bash word splitting ([#14670](https://github.com/QwenLM/qwen-code/pull/14670)), and `HOME` reassignment ([#14677](https://github.com/QwenLM/qwen-code/pull/14677)) form a clear sub-theme: emulate bash exactly, or don't emulate it at all.
- **Release/CI self-hosting.** Provisioning a release-only host on the idle ARM64 pool ([#15461](https://github.com/QwenLM/qwen-code/issues/15461)) and per-SHA E2E validation ([#15052](https://github.com/QwenLM/qwen-code/issues/15052)).

---

## 7. Developer Pain Points

- **CI signal is noisy and expensive to interpret.** At least five concurrent CI-related threads this window: [#14724](https://github.com/QwenLM/qwen-code/issues/14724) (68 comments, no test identified), [#14796](https://github.com/QwenLM/qwen-code/issues/14796) (`Install dependencies` failing before any test), [#15697](https://github.com/QwenLM/qwen-code/issues/15697) (timeout flake), [#15677](https://github.com/QwenLM/qwen-code/issues/15677) (5 of 6 failing on `darwin-x64`), and [#15052](https://github.com/QwenLM/qwen-code/issues/15052) (replaced pending runs). Contributors are spending review bandwidth triaging infra rather than code.
- **The release pipeline stops at publish.** [#15499](https://github.com/QwenLM/qwen-code/issues/15499) (immutable-publication preflight), [#15672](https://github.com/QwenLM/qwen-code/issues/15672) (preview publish failure), and [#15461](https://github.com/QwenLM/qwen-code/issues/15461) (publish competing for GitHub-hosted capacity) describe one systemic bottleneck: `publish` is the last job without dedicated capacity.
- **Shell emulation edge cases cause both false approvals and false prompts.** The `sed` regex flag gap, `/\s/` vs bash separators, `HOME` reassignment, and `2>&1` as a "write" all stem from approximating a shell in JavaScript. Each fix is small; the class of bug is not.
- **Persistent, unrecoverable session state failures.** [#15742](https://github.com/QwenLM/qwen-code/issues/15742) (missing `thoughtSignature` → permanent 400) and [#15645](https://github.com/QwenLM/qwen-code/issues/15645) (stale skills catalog) both leave users with a session they cannot repair without manual intervention.
- **"Deferred review findings" is accumulating as a backlog pattern.** [#13268](https://github.com/QwenLM/qwen-code/issues/13268), [#11056](https://github.com/QwenLM/qwen-code/issues/11056), [#15674](https://github.com/QwenLM/qwen-code/issues/15674), [#15707](https://github.com/QwenLM/qwen-code/issues/15707), and [#15527](https://github.com/QwenLM/qwen-code/issues/15527) are all ledgers created because PRs exceeded review-round or size ceilings. The 1500-addition patrol rule works, but the deferred work has no owner.
- **macOS distribution remains fragile.** [#15690](https://github.com/QwenLM/qwen-code/issues/15690) (missing `-headerpad_max_install_names` in `@opentui/core-darwin-arm64`) is a dependency-ownership gap that breaks Homebrew upgrades for every Apple Silicon user on 0.26.1.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/fireKylinKing/agents-radar).*