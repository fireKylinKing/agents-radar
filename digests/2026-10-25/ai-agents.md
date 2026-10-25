# OpenClaw 生态日报 2026-10-25

> Issues: 500 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-10-25 00:29 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-10-25

> 数据说明：所提供数据集覆盖过去 24 小时内更新的 500 个 Issue 和 500 个 PR。PR 评论数未填充，因此下文 PR 排名使用标签、状态和所述影响，而非评论量。

## 1. 今日概览

OpenClaw 仍是高速迭代项目：过去 24 小时内有 500 个 Issue 和 500 个 PR 更新，其中 92 个 Issue 关闭、259 个 PR 合并或关闭。发布了两个版本——一个聚焦可重放安全会话恢复的补丁版本，以及一个扩展稳定/LTS 网关版本，包含安全与可靠性回移。活动重心明显偏向回归与稳定性工作，而非净新增功能，尤其集中在插件源码捕获、SQLite/状态管理、会话恢复和 Windows 更新流程。项目健康度喜忧参半：维护者吞吐强劲，且存在 LTS 通道，但仍有多个 P0/P1 回归未关闭，包括数据集中评论数最高的 Issue。`stale` 与 `needs-maintainer-review` 标签的数量表明，主要瓶颈是评审带宽，而非贡献者兴趣。

## 2. 发布

### v2026.10.7
- **重点：** 可重放安全的会话恢复。
- **值得注意的变更：**
  - 中断的会话会保留恢复来源信息与未完成的工具结果。
  - 已停止的对话在接受下一条消息前会清理终端恢复残留。
  - 被拒绝的 Claude CLI 草稿不会进入最终回复。
  - 更多大型变更说明在提供的数据中被截断。
- **破坏性变更 / 迁移：** 摘录中未提供明确的破坏性变更或迁移步骤。鉴于会话状态与恢复逻辑的变更，运维人员应在升级后验证中断会话与工具结果行为。
- 链接：[OpenClaw releases](https://github.com/openclaw/openclaw/releases)

### v2026.9.34
- **通道：** 仅 Gateway 的 `extended-stable`，被描述为当前 LTS 等价通道。
- **内容：** 2026 年 9 月底的 OpenClaw，加上关键安全更新、可靠性与性能修复，以及新模型支持。
- **建议：** 适合优先考虑稳定性、而非 2026.10 功能线的生产/LTS 用户。
- 链接：[OpenClaw releases](https://github.com/openclaw/openclaw/releases)

**Beta 信号：** PR [#182951](https://github.com/openclaw/openclaw/pull/182951) 引用了 `v2026.10.10-beta.3`，表明此处列出的最新稳定版之外仍有活跃的 beta 通道。

## 3. 项目进展

过去 24 小时有 259 个 PR 合并或关闭，进展集中在可靠性、性能和 UI 协作：

- **会话/状态性能：** [#182934](https://github.com/openclaw/openclaw/pull/182934)（已关闭）跨投影共享常驻 actor 条目，减少重复的解码会话条目。
- **提及/收件箱架构：** [#155419](https://github.com/openclaw/openclaw/pull/155419)（已关闭）将 Inbox 数据库工作移出 Gateway 线程；[#153812](https://github.com/openclaw/openclaw/pull/153812)（已关闭）在 UI 中新增会话提及通知。
- **打包/更新可靠性：** [#182982](https://github.com/openclaw/openclaw/pull/182982)（已关闭）恢复打包后 update、MCP 和文件传输 helper 的独立启动。
- **CLI 卡顿修复：** [#182984](https://github.com/openclaw/openclaw/pull/182984)（已关闭）避免 CLI 打开已被 Gateway 持有的 agent 数据库时进行长达数分钟的 SQLite `integrity_check` 扫描。
- **待合并 P0 修复：** [#182951](https://github.com/openclaw/openclaw/pull/182951) 将 `config set/patch/unset` 路由到正在运行的 Gateway，修复 `v2026.10.10-beta.3` 中退出码 1 的回归。
- **性能 PR 队列：** [#183034](https://github.com/openclaw/openclaw/pull/183034) 减少 transcript turn 数据库往返；[#183042](https://github.com/openclaw/openclaw/pull/183042) 在冷启动期间延迟休眠 Chat 工作；[#182976](https://github.com/openclaw/openclaw/pull/182976) 在冷历史核算中复用已验证的条目事实。
- **通道可靠性：** [#183028](https://github.com/openclaw/openclaw/pull/183028) 修复 Telegram `ask_user` 问题在进度草稿退役期间被丢弃的问题。
- **Provider/运行时：** [#176567](https://github.com/openclaw/openclaw/pull/176567) 修复 Bun 上 OpenAI 实时语音 socket 绕过托管代理的问题；[#180599](https://github.com/openclaw/openclaw/pull/180599) 使 Code Mode 运行过期时间保持在单调时钟上。

**主题：** 合并/开放的 PR 集合显示出明确方向：减少数据库往返、改善冷启动 UI 行为，并加固会话/提及投递。功能工作相对于稳定 2026.9/2026.10 回归而言是次要的。

## 4. 社区热点

### 最活跃 Issue
1. **[#160959 — P0: Gateway blocks for minutes while capturing large external plugins](https://github.com/openclaw/openclaw/issues/160959)** — 50 条评论，崩溃循环，`no-new-fix-pr`。
   - 自 2026.9.6 引入插件生成捕获以来，带大型外部/工作区插件的 Gateway 会在启动时以及准备好的模型运行时重新发布时阻塞事件循环数分钟。
   - **底层需求：** 插件捕获必须异步/有界，并支持复用与原生二进制处理。这是数据集中最清晰的子系统级回归。

2. **[#164294 — P2: Gateway state DB WAL auto-checkpoint never completes](https://github.com/openclaw/openclaw/issues/164294)** — 30 条评论。
   - WAL checkpoint 被进程内读取器永久阻塞；`checkpointedFrames=866` 对 `logFrames=17091`。
   - **底层需求：** SQLite 读取器生命周期管理与可观测的 checkpoint 诊断。

3. **[#159356 — P2: llama.cpp manager reports ready while embedding child exits](https://github.com/openclaw/openclaw/issues/159356)** — 29 条评论。
   - 子进程退出后，嵌入请求返回 HTTP 500；怀疑存在内存压力，但尚未确认。
   - **底层需求：** 本地模型子进程的进程监督与就绪语义。

4. **[#157989 — P1: Plugin source capture rewrites 1.1–1.4 GB per CLI command and ~6.5 GB per Gateway start](https://github.com/openclaw/openclaw/issues/157989)** — 23 条评论。
   - 对外部插件重复进行字节复制和 SHA-256 哈希造成严重 SSD 磨损。
   - **底层需求：** 捕获缓存/复用，并排除捆绑的原生二进制文件。

5. **[#53408 — P2: Write/exec tool parameters silently dropped after long conversations](https://github.com/openclaw/openclaw/issues/53408)** — 15 条评论，2 👍，自 2026 年 3 月起 stale。
   - 在 15+ 轮大量工具使用后，`write` 和 `exec` 到达时参数对象为空。
   - **底层需求：** 长会话工具调用序列化完整性——一个长期存在的信任问题。

### 值得关注的活跃 PR
- [#182951 — P0: Route config mutations through a running Gateway](https://github.com/openclaw/openclaw/pull/182951)
- [#176567 — P1: OpenAI realtime voice proxy on Bun](https://github.com/openclaw/openclaw/pull/176567)
- [#182066 — perf(cron): reuse source validation at transcript target checks](https://github.com/openclaw/openclaw/pull/182066) — 可供维护者查看
- [#183034 — improve(sessions): reduce transcript turn DB round trips](https://github.com/openclaw/openclaw/pull/183034)

**分析：** 前四大 Issue 中有三个（#160959、#157989、#162585）共享同一个插件源码捕获根因。SQLite/状态数据库生命周期问题构成第二集群（#164294、#164948、#175926、#181660）。社区受到少量子系统的过度影响，因此定向修复具有高杠杆效应。

## 5. Bug 与稳定性

### P0 / 发布阻断
- **[#160959](https://github.com/openclaw/openclaw/issues/160959) — OPEN。** Gateway 捕获大型外部插件时阻塞数分钟；崩溃循环；50 条评论。无修复 PR 标记。
- **[#181660](https://github.com/openclaw/openclaw/issues/181660) — CLOSED。** 2026.10.6 上 SQLite “Session read batch cannot reenter an active SQLite writer admission”；P0 UX 发布阻断项。
- **[#153426](https://github.com/openclaw/openclaw/issues/153426) — OPEN。** 精选 `MEMORY.md`/`USER.md` 根目录被来源棘轮静默且永久排除在 bootstrap 注入之外；安全/会话状态；P0。
- **[#166634](https://github.com/openclaw/openclaw/issues/166634) — OPEN。** 延迟回收 checkpoint 失败导致崩溃恢复后 agent 不可用；P0。
- **[#182951](https://github.com/openclaw/openclaw/pull/182951) — OPEN PR。** 只要 Gateway 正在运行，配置变更就因 exclusive-offline-state 错误失败；`v2026.10.10-beta.3` 中的回归；P0 修复 PR 已开放。

### P1
- **[#157989](https://github.com/openclaw/openclaw/issues/157989) — OPEN。** 插件捕获造成 SSD 磨损；无修复 PR。
- **[#181619](https://github.com/openclaw/openclaw/issues/181619) — CLOSED。** 2026.10.5 → 2026.10.6 后 Codex app-server 执行预算超时回归；每个主回合都失败并静默回退。
- **[#135858](https://github.com/openclaw/openclaw/issues/135858) — CLOSED。** opencode-go 本地目录快照未投影 `provider.npm→api` 覆盖；导致 `muse-spark-1.2-contributor` 失效并遗漏 `mimo-v2.5`。
- **[#182476](https://github.com/openclaw/openclaw/issues/182476) — OPEN。** Windows 更新修复因 `OPENCLAW_STATE_LEASE_LOST` 失败；Gateway 就绪缓慢/不稳定。
- **[#150822](https://github.com/openclaw/openclaw/issues/150822) — OPEN。** 原生 Codex 子进程宣告 OpenClaw 工具但没有执行处理器；分离的失败投递也失败；安全敏感。
- **[#154299](https://github.com/openclaw/openclaw/issues/154299) — OPEN。** 2026.9.5 上子代理完成投递的最终文本静默丢弃；没有队列条目或失败记录。
- **[#167876](https://github.com/openclaw/openclaw/issues/167876) — OPEN。** Talk 咨询在会话建立后丢失调用方权限；安全/其他。
- **[#148292](https://github.com/openclaw/openclaw/issues/148292) — OPEN。** 会话自动压缩仅一次尝试且无超时；出现 23 分钟挂起。
- **[#158154](https://github.com/openclaw/openclaw/issues/158154) — OPEN。** Telegram 子代理结果公告在 30 分钟后过期未投递；重试使主题通道停滞数小时。
- **[#178300](https://github.com/openclaw/openclaw/issues/178300) — OPEN。** 1 token 的 `length` 停止后，已结算回合的最终化会投递虚假成功。
- **[#178000](https://github.com/openclaw/openclaw/issues/178000) — CLOSED。** 会话状态竞态触发跨 provider 模型回退，原因为 `reason=unknown`。

### P2 集群
- **插件捕获 / Windows：** [#162585](https://github.com/openclaw/openclaw/issues/162585)（暂存将插件重新物化到自身，290 MB → 612 MB+/24 分钟），[#157812](https://github.com/openclaw/openclaw/issues/157812)（Windows 自动更新失败模式）。
- **SQLite/状态：** [#164294](https://github.com/openclaw/openclaw/issues/164294)（WAL checkpoint），[#164948](https://github.com/openclaw/openclaw/issues/164948)（`doctor --fix` 自死锁，`busyTimeoutMs=0`），[#175926](https://github.com/openclaw/openclaw/issues/175926)（快照回收每个周期都跳过）。
- **会话/工具完整性：** [#159789](https://github.com/openclaw/openclaw/issues/159789)（一次原生关闭失败会永久锁定 agent 执行），[#157986](https://github.com/openclaw/openclaw/issues/157986)（Automations `agentTurn` 出现 `DataCloneError`），[#146902](https://github.com/openclaw/openclaw/issues/146902)（工具集频繁变化使 prompt 缓存复用失效）。
- **UI/UX：** [#164394](https://github.com/openclaw/openclaw/issues/164394)（WebChat transcript 抖动），[#149912](https://github.com/openclaw/openclaw/issues/149912)（文本渲染两次/三次），[#165041](https://github.com/openclaw/openclaw/issues/165041)（内部上下文注入可见），[#137188](https://github.com/openclaw/openclaw/issues/137188)（过时进度卡片仍显示活动 spinner）。
- **Provider/缓存/成本：** [#161208](https://github.com/openclaw/openclaw/issues/161208)（心跳从不复用 Anthropic prompt 缓存；每次心跳约 $0.40–0.95），[#162768](https://github.com/openclaw/openclaw/issues/162768)（Codex 模型发现硬编码 `client_version=0.158.0`），[#144797](https://github.com/openclaw/openclaw/issues/144797)（复用的 `claude-cli` 会话保留已删除的 `--mcp-config`，工具以 401 失败）。
- **安全：** [#158966](https://github.com/openclaw/openclaw/issues/158966)（带凭据的 CDP WebSocket URL 进入面向模型的标签页结果）。
- **平台：** [#154104](https://github.com/openclaw/openclaw/issues/154104)（空闲 Matrix E2EE 约 50% CPU，约 52 MB/分钟写入），[#153622](https://github.com/openclaw/openclaw/issues/153622)（macOS Peekaboo 瞬态 UUID 快照）。

**进行中的修复 PR：** [#183052](https://github.com/openclaw/openclaw/pull/183052)（agent 数据库完整性检查）、[#183002](https://github.com/openclaw/openclaw/pull/183002)（跨时钟跳变的单调时钟）、[#183028](https://github.com/openclaw/openclaw/pull/183028)（Telegram 问题）、[#180850](https://github.com/openclaw/openclaw/pull/180850)（doctor 畸形 UTF-8）、[#183000](https://github.com/openclaw/openclaw/pull/183000)（PostgreSQL 配置报告）、[#183015](https://github.com/openclaw/openclaw/pull/183015)（PostgreSQL 受限有序读取）。最高优先级 P0 Issue #160959 仍缺少修复 PR。

## 6. 功能请求与路线图信号

- **[#182712 — Limit-aware model fallback](https://github.com/openclaw/openclaw/issues/182712)**（P2，7 条评论）：跳过 provider 配额池已耗尽的候选，并优先选择其他 provider。是近期模型路由工作的强候选。
- **[#155633 — Add Databricks Unity Gateway as an official model provider](https://github.com/openclaw/openclaw/issues/155633)**（P2）：已有实现 PR [#155634](https://github.com/openclaw/openclaw/pull/155634)。企业级 provider 扩展可能性较高。
- **[#139477 — Honor `tools.toolSearch` for bundle-MCP CLI backends](https://github.com/openclaw/openclaw/issues/139477)**（P2，diamond lobster）：loopback MCP 配置硬编码 `alwaysLoad: true`，会急切展开每个工具 schema。与 prompt/token 效率直接相关。
- **[#150598 — Completion-wake delivery semantics](https://github.com/openclaw/openclaw/issues/150598)**（P2）：针对多会话编排中仅终端、新回合、合并以及失败按分诊处理的投递语义，提出基于现场数据的方案。
- **[#141004 — Record observed runtime skill usage](https://github.com/openclaw/openclaw/pull/141004)**（自 9 月 7 日起开放的 PR）：为技能读取/调用提供持久审计面。
- **[#137613 — Pre-compaction memory flush on CLI backends](https://github.com/openclaw/openclaw/issues/137613)**（P2，diamond lobster）：`claude-cli` 的内存刷新被关闭，因此 CLI 支持的会话在压缩前会丢失持久笔记。
- **[#96675 — Owner-signed responsibility gates for memory, actions, skills, evidence reuse](https://github.com/openclaw/openclaw/issues/96675)**（已关闭增强）：表明用户持续要求在对持久记忆/动作复用前进行显式确认。
- **[#153812 — Show session mention notifications](https://github.com/openclaw/openclaw/pull/153812)**（已关闭）：UI 协作功能，可能出现在近期版本中。

**预测：** 下一个版本可能重点包括会话恢复/来源信息改进、通过正在运行的 Gateway 路由配置、插件捕获性能、限额感知的模型回退，以及提及/Telegram 投递可靠性。新模型支持已通过 extended-stable 通道流入。

## 7. 用户反馈摘要

**主要痛点**
- **运营成本与硬件磨损：** 插件源码捕获导致每条命令和每次 Gateway 启动产生 GB 级写入（[#157989](https://github.com/openclaw/openclaw/issues/157989)、[#162585](https://github.com/openclaw/openclaw/issues/162585)）。Matrix E2EE 空闲时约 50% CPU 和约 52 MB/分钟写入（[#154104](https://github.com/openclaw/openclaw/issues/154104)）。
- **Windows 上更新/安装脆弱：** 反复自动更新失败、未展开的 `$OPENCLAW_STATE_DIR`、被放弃的更新记录，以及修复期间租约丢失（[#157812](https://github.com/openclaw/openclaw/issues/157812)、[#182476](https://github.com/openclaw/openclaw/issues/182476)）。
- **会话可靠性与消息丢失：** 原生关闭失败后 agent 执行被锁定（[#159789](https://github.com/openclaw/openclaw/issues/159789)）；子代理最终文本丢失（[#154299](https://github.com/openclaw/openclaw/issues/154299)）；Telegram 公告过期（[#158154](https://github.com/openclaw/openclaw/issues/158154)）；崩溃恢复可能使 agent 不可用（[#166634](https://github.com/openclaw/openclaw/issues/166634)）。
- **信任/安全担忧：** 精选记忆文件可能被永久静默排除（[#153426](https://github.com/openclaw/openclaw/issues/153426)）；带凭据的 CDP URL 进入面向模型的输出（[#158966](https://github.com/openclaw/openclaw/issues/158966)）；Talk 通话丢失调用方权限（[#167876](https://github.com/openclaw/openclaw/issues/167876)）。
- **成本可见性：** 心跳从不复用 Anthropic prompt 缓存，每次成本约 $0.40–0.95（[#161208](https://github.com/openclaw/openclaw/issues/161208)）。
- **UI 打磨：** WebChat transcript 抖动（[#164394](https://github.com/openclaw/openclaw/issues/164394)）、重复/三次渲染（[#149912](https://github.com/openclaw/openclaw/issues/149912)）、可见的内部上下文注入（[#165041](https://github.com/openclaw/openclaw/issues/165041)）、过时的进度 spinner（[#137188](https://github.com/openclaw/openclaw/issues/137188)）。

**体现的使用场景**
- 使用子代理、长时间运行后台任务和协调器会话的重度多会话编排。
- Telegram、Matrix 和 QQ 通道部署，包括论坛主题和 E2EE。
- CLI 支持的模型（`claude-cli`、Codex app-server）和本地嵌入栈（`llama.cpp`）。
- Windows 桌面/服务器安装和 npm 全局部署。
- 带原生二进制文件的大型外部插件树。

**满意度/不满意度：** 用户正在提供详细、高质量的复现和运维变通方案，这表明参与度很高。不满主要集中在 2026.9.x 线引入的回归、Windows 更新可靠性以及长期 stale 的 Issue。extended-stable/LTS 发布对生产用户是一个积极信号。

## 8. 积压事项关注

这些事项很重要，但长期未解决或没有明确维护者负责：

- **[#53408 — Write/exec parameters silently dropped after long conversations](https://github.com/openclaw/openclaw/issues/53408)** — 于 2026-03-24 打开；15 条评论，2 👍；stale。数据集中最古老的高影响工具完整性问题。
- **[#141172 — Run dies before first session commit leaks `agent.run.started` forever](https://github.com/openclaw/openclaw/issues/141172)** — 于 2026-09-07 打开；stale；轨迹刷新外键失败且缺少 `agent.run.finished`。
- **[#137613 — Pre-compaction memory flush disabled on CLI backends](https://github.com/openclaw/openclaw/issues/137613)** — 于 2026-09-03 打开；diamond lobster；10 条评论；持久记忆丢失风险。
- **[#146902 — Same-session tool-set churn defeats prompt-cache reuse](https://github.com/openclaw/openclaw/issues/146902)** — 于 2026-09-13 打开；11 条评论；直接影响成本/性能。
- **[#149631 — PR preflight throws at 121 jobs against 120 Node matrix cap](https://github.com/openclaw/openclaw/issues/149631)** — 于 2026-09-16 打开；CI 门禁在测试开始前失败。
- **[#150598 — Completion-wake delivery semantics proposal](https://github.com/openclaw/openclaw/issues/150598)** — 于 2026-09-17 打开；需要产品决策。
- **[#148292 — Auto-compaction 23-minute hangs](https://github.com/openclaw/openclaw/issues/148292)** — 于 2026-09-14 打开；P1；会话状态。
- **[#154104 — Idle Matrix E2EE ~50% CPU and ~52 MB/min writes](https://github.com/openclaw/openclaw/issues/154104)** — 于 2026-09-20 打开；运营成本。
- **[#145937 — QQ channel left without a fully working plugin on 2026.9.x](https://github.com/openclaw/openclaw/issues/145937)** — 于 2026-09-12 打开；第一方插件在入站时崩溃；后继者收到零个 chunk。
- **[#144976 — Gateway auto-restarts after slow explicit `stop` on systemd](https://github.com/openclaw/openclaw/issues/144976)** — 于 2026-09-11 打开；生命周期正确性。
- **[#141004 — Audit observed runtime skill usage](https://github.com/openclaw/openclaw/pull/141004)** — 自 2026-09-07 起开放的 PR；可观测性缺口。
- **[#155633 — Databricks Unity Gateway provider](https://github.com/openclaw/openclaw/issues/155633)** / **[#155634](https://github.com/openclaw/openclaw/pull/155634)** — 于 2026-09-22 打开；企业级 provider 请求，且实现已开放。
- **[#180850](https://github.com/openclaw/openclaw/pull/180850)** 与 **[#183002](https://github.com/openclaw/openclaw/pull/183002)** — 标记为 `needs proof`；小型但相关的可靠性修复，等待验证。

**整体健康评估：** OpenClaw 非常活跃，PR 吞吐强劲，并有可信的 LTS 流程，但项目当前正在偿还插件捕获、SQLite/状态生命周期、会话恢复和 Windows 更新方面的显著回归债务。最高杠杆行动是针对插件源码捕获集群（#160959、#157989、#162585）的协调修复，其次是 SQLite WAL/读取器生命周期清理，以及关闭 P0 记忆来源缺口（#153426）。

---

## 横向生态对比

# 跨项目对比报告——个人 AI 助手 / Agent OSS 生态

**窗口：** 2026-10-25 社区摘要。**项目：** OpenClaw、Hermes Agent、IronClaw、QwenPaw、ZeroClaw。  
**健康分：** 分析师综合评分（0–10），权重包括发布节奏、Issue/PR 吞吐量、回归/安全负担、评审积压和流程成熟度。并非正式基准。

---

## 1. 生态概览

个人 AI 助手 / Agent 开源生态正进入可靠性与治理阶段：项目交付的净新增能力减少，更多精力投入到会话恢复、状态存储生命周期、插件治理、投递保证和升级安全。OpenClaw 仍以网关为中心架构和 LTS 通道保持规模领先，而 Hermes Agent、ZeroClaw 和 QwenPaw 分别围绕桌面会话、SOP/安全工作流和多 Agent 治理占据更窄但可信的位置。跨项目来看，社区反馈主要集中在静默失败、数据持久性、插件/扩展信任、平台特定更新故障以及成本/延迟可见性。IronClaw 在本窗口无活动。最强差异化因素已不再是模型访问，而是运维信任——即会话、记忆、插件、通道和升级能否在生产负载下可预测地运行。

---

## 2. 活跃度对比

除非另有说明，所有计数均为 24 小时窗口内更新的条目。

| 项目 | Issue 数 | PR 数 | 发布状态 | 健康分 |
|---|---:|---:|---|---:|
| **OpenClaw** | 500 更新；92 关闭 | 500 更新；259 合并/关闭 | 两次发布：**v2026.10.7** 稳定版 + **v2026.9.34** 延长稳定版/LTS；beta 信号 **v2026.10.10-beta.3** | **7.0 / 10** |
| **Hermes Agent** | 50 更新；43 开启，约 7 关闭 | 50 更新；41 开启，9 合并/关闭（约 18%） | 窗口内**无发布**；现场版本 v0.22.5 / v0.22.4 / v0.22.2 | **6.0 / 10** |
| **IronClaw** | 0 | 0 | 无活动 | **N/A — 不活跃** |
| **QwenPaw** | 4 更新；3 开启，1 关闭 | 11 更新；10 开启，1 关闭**且未合并** | **无发布**；最新现场报告版本 v2.2.1 | **5.5 / 10** |
| **ZeroClaw** | 18 更新；6 关闭 | 50 更新；47 开启，3 合并/关闭 | **无已发布版本**，但 v0.8.7 版本号提升 PR 已关闭；发布在即 | **6.5 / 10** |

**解读：** OpenClaw 的更新条目量约为 Hermes 的 10 倍、ZeroClaw 的约 15 倍，以及 QwenPaw 的约 66 倍。Hermes 和 ZeroClaw 活跃但受评审制约。QwenPaw 拥有参与度较高的生产用户，但落地吞吐量很低。IronClaw 没有信号。

---

## 3. OpenClaw 的定位

**相较同行的优势**
- **规模与吞吐量：** 24 小时内更新 1,000 个 Issue/PR，259 个 PR 合并/关闭，92 个 Issue 关闭。这比 Hermes 和 ZeroClaw 高一个数量级，也远高于 QwenPaw。
- **发布成熟度：** 唯一具备可见的稳定版 + LTS/延长稳定版流程以及活跃 beta 线的项目。Hermes 和 QwenPaw 未发布任何版本；ZeroClaw 的 v0.8.7 已准备但尚未发布。
- **广度：** 网关、提供商、插件、通道、会话恢复、Windows 更新流程、SQLite 状态管理、本地嵌入和命令行工具都在同一生态中活跃。
- **生态引力：** 大型外部插件树、多会话编排、Telegram/Matrix/QQ 部署、CLI 后端模型以及 Windows/npm 安装都在反馈中有所体现。

**相较同行的劣势**
- **回归债务：** P0 插件源码捕获阻塞（#160959，50 条评论）和 P1 SSD 磨损捕获（#157989）是影响力大但尚未解决的聚类问题；#160959 没有修复 PR。
- **评审带宽：** `stale` 和 `needs-maintainer-review` 标签表明瓶颈是评审容量，而非贡献者兴趣。
- **SQLite/状态与 Windows 更新脆弱性：** 仍有若干 P0/P1 Issue 围绕 WAL checkpointing、reader lifecycle、崩溃恢复和 `OPENCLAW_STATE_LEASE_LOST`。

**技术路线差异**
- OpenClaw 是**以网关为中心**的：长期运行的 Gateway 拥有配置、会话、状态数据库、插件捕获和通道路由。近期工作将配置变更路由到运行中的 Gateway，并改进可重放安全的会话恢复。
- 同行项目范围更窄：Hermes 偏重桌面/会话生命周期；ZeroClaw 是 Rust/Tauri，带有 SOP、策略和记忆后端；QwenPaw 偏重治理/审计和多 Agent 编排。
- OpenClaw 的 LTS 通道赋予其生产采用优势，当前数据集中没有同行能匹敌。

---

## 4. 共同技术重点领域

| 重点领域 | 表现出需求的项目 | 正在浮现的具体需求 |
|---|---|---|
| **会话/状态生命周期与恢复** | OpenClaw、Hermes、QwenPaw、ZeroClaw | 可重放安全恢复、中断会话溯源、确定性会话关闭、关闭时刷盘恢复、可归因取消、仅追加的会话事件。OpenClaw 存在 SQLite WAL/reader 生命周期问题；Hermes 存在会话标识/标题冲突和无界 DM 会话；QwenPaw 存在 Agent 间静默取消；ZeroClaw 正在探索规范会话事件历史。 |
| **消除静默失败/可观测性** | QwenPaw、Hermes、OpenClaw、ZeroClaw | 配置变更必须记录日志；审计日志不得静默停止；取消必须记录发起者/原因；模型回退不得静默；策略拒绝应指明规则；健康检查不得误报。 |
| **记忆持久性与信任** | ZeroClaw、OpenClaw、QwenPaw | ZeroClaw 在 Markdown 记忆中存有 S0 级并发写入数据丢失；OpenClaw 存在记忆溯源排除和压缩前刷盘缺口；QwenPaw 存在记忆召回轨迹泄漏和一次 ReMe 升级。共同需求：非破坏性长期召回、溯源、同意门控和压缩安全。 |
| **插件治理与扩展安全** | Hermes、OpenClaw、ZeroClaw | Hermes 需要目录准入规则和已验证分级；OpenClaw 需要有界/异步插件源码捕获和原生二进制处理；ZeroClaw 需要清理孤立的插件安装。共同需求：插件 API 策略、沙箱化、准入评审和扩展面决策。 |
| **更新/安装可靠性** | OpenClaw、Hermes、QwenPaw/ZeroClaw（间接） | OpenClaw：Windows 租约丢失和网关就绪缓慢。Hermes：约 1GB 的 `state.db` 通过固定 30 秒超时阻塞更新；Windows 更新后网关重启未被纳入处理。共同需求：可随用户数据规模扩展且跨平台原子的升级路径。 |
| **通道投递保证** | OpenClaw、Hermes、QwenPaw、ZeroClaw | Telegram 公告会过期；Hermes Bot Chat 投递在标题冲突时拒绝，澄清答案在重连时丢失；QwenPaw 需要 Matrix E2EE 恢复和 QQ 流式传输；ZeroClaw 正在增加邮件附件/XMPP。共同需求：确定性投递、重试语义、重连处理和附件功能对等。 |
| **安全与供应链加固** | ZeroClaw、Hermes、OpenClaw、QwenPaw | ZeroClaw 关闭了 RUSTSEC/CA-bundle 问题；Hermes 修复了脱敏和 MCP 工具集扩大问题；OpenClaw 存在携带凭据的 CDP URL 泄漏和调用方权限问题；QwenPaw 存在跨通道记忆轨迹泄漏。共同需求：密钥脱敏、权限边界、依赖安全公告和通道安全输出。 |
| **成本/延迟/提示词效率** | OpenClaw、Hermes、ZeroClaw | OpenClaw：提示词缓存复用失败、心跳成本、工具集频繁变动。Hermes：提示词 token 裁剪 PR。ZeroClaw：上下文窗口回退到 32k 时拒绝有效轮次。共同需求：缓存感知的提示词构建、模型元数据归一化和限制感知路由。 |
| **多 Agent 编排语义** | OpenClaw、Hermes、QwenPaw、ZeroClaw | OpenClaw：子 Agent 投递和完成唤醒语义。Hermes：Kanban 生成重复副作用。QwenPaw：多 Agent 集群取消可归因到发起者。ZeroClaw：SOP 运行完整性。共同需求：幂等副作用、故障分诊和终态投递契约。 |

---

## 5. 差异化分析

| 项目 | 功能重点 | 目标用户 | 技术架构/路线 |
|---|---|---|---|
| **OpenClaw** | 通用 Agent 网关、插件、提供商、通道、会话恢复、LTS | 生产运维人员、插件生态开发者、多渠道部署 | 以网关为中心；SQLite/状态数据库；插件源码捕获；会话恢复/溯源；通过运行中的 Gateway 路由配置；稳定版 + LTS + beta 通道 |
| **Hermes Agent** | 桌面会话可见性/生命周期、Windows 安装/更新、网关投递、插件目录治理 | 桌面优先的个人助手用户、多会话用户、插件集成者 | 桌面 + 网关；带验证分级的插件目录；提示词精简；会话/标题身份模型仍在设计中 |
| **IronClaw** | 无可观察活动 | 数据不足 | 数据不足 |
| **QwenPaw** | 多 Agent 编排、治理/审计、团队/自托管默认配置、Matrix/QQ/Feishu 通道 | 自托管团队、多 Agent 集群、合规敏感型部署 | `policy.yaml` + `governance/audit.db`；hooks；通道集成；ReMe 记忆升级；Console 默认配置 |
| **ZeroClaw** | SOP 引擎、安全/供应链、记忆正确性、shell 策略、邮件/XMPP 通道 | 注重安全的自托管者、工作流/SOP 操作者、Windows shell 自动化 | Rust/Tauri 工作区；策略解析器；SOP 引擎；记忆后端；RUSTSEC/CA 卫生；发布稳定化模式 |

**关键对比：** OpenClaw 是广泛平台型选手；Hermes 以桌面/会话体验为主导；ZeroClaw 以安全/工作流为主导；QwenPaw 以多 Agent/治理为主导。OpenClaw 的架构最集中、最面向生产；ZeroClaw 的最偏向安全/控制面；QwenPaw 的最偏向合规/审计；Hermes 的最偏向桌面人体工学。

---

## 6. 社区动能与成熟度

| 梯队 | 项目 | 动能评估 | 成熟度评估 |
|---|---|---|---|
| **第一梯队——超大规模/发布驱动** | **OpenClaw** | 速度极高；两次发布；活跃 beta；259 个 PR 合并/关闭 | 最成熟的发布流程和 LTS 姿态，但存在显著回归债务和评审积压。在快速迭代的同时偿还稳定性债务。 |
| **第二梯队——活跃但受评审制约** | **Hermes Agent**、**ZeroClaw** | Hermes：100 个条目，PR 关闭率 18%，无发布。ZeroClaw：68 个条目，6 个 Issue 关闭，发布在即，安全响应强。 | Hermes 需要就会话身份和插件扩展面做出设计裁决。ZeroClaw 有可信的发布列车，但 PR 开启/关闭比为 47/3，且存在 S0 记忆数据丢失风险。 |
| **第三梯队——低吞吐、用户参与度高** | **QwenPaw** | 15 个条目，实际零代码落地，1 个 PR 关闭未合并。 | 有类生产用户和详细报告，但评审带宽是瓶颈；静默失败缺陷和治理审计失败是信任风险。 |
| **休眠** | **IronClaw** | 无活动 | N/A |

**动能解读：** OpenClaw 正在同时快速迭代和稳定化。ZeroClaw 处于发布稳定化模式，安全响应与处置能力强。Hermes 处于高吞吐分诊中，但需要两项架构决策。QwenPaw 拥有社区好感，但合并吞吐量低。IronClaw 不活跃。

---

## 7. 趋势信号

| 趋势 | 跨项目证据 | 对 AI Agent 开发者的价值 |
|---|---|---|
| **可靠性成为新功能** | OpenClaw/Hermes/ZeroClaw/QwenPaw 中的会话恢复、WAL/reader 生命周期、可重放安全事件、关闭刷盘恢复、原子更新 | 尽早投入幂等副作用、可重放会话日志、状态存储生命周期管理和升级安全迁移。 |
| **静默失败不可接受** | QwenPaw 配置重置/审计停止/无原因取消；Hermes 静默模型回退和未投递的澄清；OpenClaw 伪成功和内部上下文可见；ZeroClaw 不透明策略拒绝 | 将结构化审计事件、用户可见诊断、变更日志和显式失败契约构建到 Agent 运行时中。 |
| **记忆必须持久且可治理** | ZeroClaw S0 Markdown 记忆数据丢失；OpenClaw 溯源排除和压缩前刷盘；QwenPaw 记忆轨迹泄漏 | 将记忆视为事务型子系统：并发控制、溯源、同意门控、压缩和跨通道输出卫生。 |
| **插件生态需要先治理再规模化** | Hermes 目录准入/验证分级；OpenClaw 插件捕获性能；ZeroClaw 插件安装清理 | 尽早定义插件 API 边界、准入策略、沙箱化、扩展面决策以及捕获/缓存行为。 |
| **更新/安装可靠性是信任门槛** | Hermes `state.db` 预检阻塞更新；OpenClaw Windows 租约/更新失败；两者均存在 Windows 回归 | 使用真实用户数据量和平台特定安装程序测试升级；让更新路径原子化且可恢复。 |
| **通道投递正成为产品关键** | Telegram 过期、Bot Chat 标题冲突、Matrix E2EE 恢复、QQ 流式传输、邮件附件、XMPP | 抽象传输层并明确投递语义：重试、重连、去重、附件功能对等和终态确认。 |
| **成本/延迟优化正进入核心** | OpenClaw 提示词缓存未命中和心跳成本；Hermes 提示词裁剪；ZeroClaw 上下文窗口元数据回退 | 将缓存感知的提示词组装、模型元数据归一化、限制感知回退以及 token/成本可观测性构建到路由中。 |
| **安全加固横跨供应链与运行时** | ZeroClaw 中的 RUSTSEC/CA bundle；Hermes 中的脱敏/MCP 工具集扩大；OpenClaw 中的 CDP 凭据泄漏和调用方权限；QwenPaw 中的审计/治理 | 将依赖安全公告、密钥脱敏、权限边界和审计日志视为一等 Agent 平台功能。 |
| **多 Agent 语义需要契约** | OpenClaw 子 Agent 投递；Hermes Kanban 重复回调；QwenPaw 集群取消归因；ZeroClaw SOP 完整性 | 在扩展 Agent 集群前，定义取消归因、副作用幂等性、完成投递语义和失败即分诊行为。 |

**总结：** OpenClaw 是该生态的规模与发布流程领导者，但其下一步质量提升取决于解决插件捕获和 SQLite/状态问题簇。Hermes 和 ZeroClaw 是可信的第二梯队项目，拥有强用户参与度，但路线图受评审制约，且尚有未解决的基础设计问题。QwenPaw 拥有真实的生产用户，但必须提升合并吞吐量和静默失败可观测性。在所有活跃项目中，胜出的 Agent 平台将是那些让会话、记忆、插件、通道和升级**可观测、可恢复且可信**的平台——而不仅仅是功能丰富。

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要，2026-10-25

**数据范围说明：** 以下数字涵盖最近 24 小时内更新的条目。Issue 评论数与表情回应按原样报告；PR 评论数不可用（`undefined`），因此 PR 按时间新近度/影响排序，而非按互动量。50 个 Issue 中仅列出前 30 个、50 个 PR 中仅列出前 20 个，因此下文的合计为部分数据。

---

## 1. 今日概览

Hermes Agent 正处于高吞吐量的维护阶段：**24 小时内触及 100 个条目**（50 个 Issue、50 个 PR），而**发布数为零**，表明当前以持续的分诊与审查为主，而非冲刺发布。待办列表仍以未关闭项为主——**更新的 50 个 Issue 中有 43 个仍未关闭**，**50 个 PR 中有 41 个仍处于打开状态**，仅 9 个 PR（18%）被合并或关闭。活动集中在四个主题上：**桌面端会话状态/生命周期**、**Windows 安装与更新可靠性**、**网关消息投递与会话隔离**，以及**插件目录治理**。有五个值得注意的关闭项落地（两个安全修复、两个 Windows 稳定性修复、一个功能请求），同时一个大型插件钩子 PR 在 `needs-decision` 挂起后被关闭。整体健康状况**喜忧参半偏正面**：维护者的响应速度和外部贡献都很强，但不断增多的 P2 桌面端/Windows 缺陷和长期挂起的 P3 功能，指向正在累积的稳定化债务。

---

## 2. 发布

**最近 24 小时内无新发布。** 新进报告中引用最多的版本是 **v0.22.5**（v0.22.4/v0.22.2 仍有用户在使用），因此下文提到的安装更新缺陷对当前用户而言确实存在。

---

## 3. 项目进展

**已关闭 Issue（可见 7 个中的 5 个）：**

| 条目 | 类型 | 解决的问题 |
|---|---|---|
| [Issue #142576](https://github.com/NousResearch/hermes-agent/issues/142576) | 安全 | 脱敏现在可识别 Context7 `ctx7sk-` 密钥；裸密钥不再以未掩码形式通过 |
| [Issue #141468](https://github.com/NousResearch/hermes-agent/issues/141468) | 安全 | Cron 的 `enabled_toolsets: mcp-<name>` 不再悄悄将任务扩大到**每一个**已启用的 MCP 服务器 |
| [Issue #142528](https://github.com/NousResearch/hermes-agent/issues/142528) | Windows 稳定性 | 已清理废弃的 pm-runtime 租约文件（累计达 1,546 个）；Python 删除钩子不再强制 `hermes cron`/`gateway` 以退出码 1 退出 |
| [Issue #142515](https://github.com/NousResearch/hermes-agent/issues/142515) | Windows CLI | `hermes gateway restart` 会写入计划重启标记，恢复“Gateway online”提示 |
| [Issue #144071](https://github.com/NousResearch/hermes-agent/issues/144071) | 功能请求 | 插件选择性回复审批/澄清请求——未交付功能即关闭 |

**已合并/关闭的 PR（可见 9 个中的 3 个）：**

- [PR #144203](https://github.com/NousResearch/hermes-agent/pull/144203) — 插件目录中 **CoreWeave Inference 层级设为 `verified`**（已验证所有权、非 fork、域名匹配的提交）。仅涉及目录变更；建立了一套可复用的验证标准。
- [PR #144174](https://github.com/NousResearch/hermes-agent/pull/144174) — **技能索引清理**（salvage 自 #130552）：从 `<available_skills>` 系统提示块中移除类别简介、重复名称和词中断片——直接减少提示词膨胀。
- [PR #134556](https://github.com/NousResearch/hermes-agent/pull/134556) — **`pre_send_message` 网关插件钩子**在 `needs-decision` 状态下被关闭。这是今天影响最大的关闭项：一个通用的出站消息重写/丢弃钩子本可解锁 cron、看板和审批渠道的自定义能力。其关闭表明维护者**尚未准备好承诺该扩展面**。

**净进展：** 安全加固（2 项修复）、Windows 运行稳定性（2 项修复）、目录信任模型（1 项）、提示词效率（1 项）。未交付新的用户可见能力。

---

## 4. 社区热门话题

**1. [Issue #50718](https://github.com/NousResearch/hermes-agent/issues/50718) — 桌面端会话可见性与通知**（12 条评论，3 👍，数据集中互动量最高）
用户无法分辨哪个会话正在运行、已完成还是被阻塞。报告将其拆解为三个独立的根因：后台轮次完成时没有未读提示、没有需要输入的信号，以及没有操作系统级徽标。**深层需求：** 会话必须成为一等、可观测的对象——当前 UI 是为单一前台聊天优化的，而真实使用场景是多会话且后台化的。

**2. [Issue #133253](https://github.com/NousResearch/hermes-agent/issues/133253) — 桌面端更新被 30 秒的 `state.db` 预检阻塞**（8 条评论，标记为 `duplicate`）
约 1GB 的 `state.db` 无法通过硬编码的 `spawnSync` 超时，因此“立即更新”在触及任何内容之前就被取消。**深层需求：** 升级路径必须能随用户累积数据而扩展；固定超时对每个长期用户都是潜在故障。

**3. [Issue #48997](https://github.com/NousResearch/hermes-agent/issues/48997) — 显式关闭被弃用的运行时会话及其拥有的后端**（6 条评论）
新建聊天、`/new`、归档、删除、关闭次级窗口以及退出，都会让运行时会话和后端继续存活。**深层需求：** 定义明确的会话生命周期，并具备确定性的资源回收——这与 [#136012](https://github.com/NousResearch/hermes-agent/issues/136012) 中表现为 DM 无界增长的缺口是同一个问题。

**4. [Issue #143053](https://github.com/NousResearch/hermes-agent/issues/143053) — 插件目录准入指引**（4 条评论）
一位维护完整工作流/源码包的第三方维护者找不到受支持的准入途径——并明确表示*不是*要求绕过安装扫描器。**深层需求：** 插件生态的发展速度已超过其已发布的治理机制；CoreWeave 验证 PR（#144203）是同一问题从维护者视角的体现。

**5. [Issue #143715](https://github.com/NousResearch/hermes-agent/issues/143715) — Bot Chat 投递被拒 `not_found`**（4 条评论）
一个规范 `Bot Chat` 标题被未绑定的 `tui` 来源行占用，导致 profile 投递被永久阻塞。**深层需求：** 标题/会话键冲突需要一套解决规则，而不是失败即关闭的拒绝。

**6. [Issue #142839](https://github.com/NousResearch/hermes-agent/issues/142839) — `model.default` 的斜杠前缀被原样转发 → 503 + 静默回退**（4 条评论）
当 `model.provider` 已指明提供方时，`<provider>/<model>` 仍被原样传给上游，产生 `model_not_found` 和一个不可见的降级。**深层需求：** 配置归一化加上**禁止静默回退**——用户必须知道所选模型未被使用。

**7. [Issue #18964](https://github.com/NousResearch/hermes-agent/issues/18964) — Reticulum/LXMF 消息网关**（4 条评论，3 👍）
可见集合中运行时间最长的请求（2026-05-02 开启）：一种与链路无关、无服务器的传输方式。**深层需求：** 在断网/离网或抗审查环境中的部署。

**8. [Issue #136189](https://github.com/NousResearch/hermes-agent/issues/136189) — P1：关机刷写文件写入了错误的会话键**（4 条评论）
关机时排队的消息按适配器隔离标志生成键，而恢复时却通过网关级标志解析，因此当标志不一致时**重启恢复永远无法映射到它们**。这是数据集中最严重的投递保证缺陷。

---

## 5. 缺陷与稳定性

**严重程度排序（P1 → P2）及修复状态：**

| 严重度 | Issue | 故障模式 | 修复 PR？ |
|---|---|---|---|
| **P1** | [#136189](https://github.com/NousResearch/hermes-agent/issues/136189) | 当适配器与网关的 `group_sessions_per_user` 标志不一致时，排队消息在重启后无法恢复——**永久消息丢失** | 未见 |
| P2 | [#133253](https://github.com/NousResearch/hermes-agent/issues/133253) | 约 1GB 的 `state.db` 导致桌面端无法更新（30 秒预检） | 无；标记为 `duplicate` |
| P2 | [#136012](https://github.com/NousResearch/hermes-agent/issues/136012) | 网关 DM 会话无界增长；`session_reset` 的 idle/at_hour 在路由中**没有消费者** | 未见 |
| P2 | [#143715](https://github.com/NousResearch/hermes-agent/issues/143715) | 标题冲突时 Bot Chat 投递被永久拒绝（`not_found`） | 未见 |
| P2 | [#142839](https://github.com/NousResearch/hermes-agent/issues/142839) | 带提供方前缀的模型字符串 → 503 → **静默回退** | 未见 |
| P2 | [#133026](https://github.com/NousResearch/hermes-agent/issues/133026) | `config set agent.reasoning_effort none` 保存为 `null`，导致仍使用默认 effort（overrides/MoA 槽位同样如此） | 部分：仅 #92468 覆盖了顶层键 |
| P2 | [#144140](https://github.com/NousResearch/hermes-agent/issues/144140) | 仪表盘列出了无法打开的 `serve`/桌面端会话（`not_found`，记录为空） | 未见 |
| P2 | [#144181](https://github.com/NousResearch/hermes-agent/issues/144181) | 唯一不可重试的启动适配器冲突忽略了 `on_all_adapters_down: stay_alive`，以退出码 78 退出 → **因一个已登出的平台导致整个进程重启循环** | ✅ [PR #144198](https://github.com/NousResearch/hermes-agent/pull/144198) |
| P2 | [#144158](https://github.com/NousResearch/hermes-agent/issues/144158) / [#144159](https://github.com/NousResearch/hermes-agent/issues/144159) | `clarify` 错误帧和问题中途重连返回 `undelivered`；已锁定的答案被丢弃 | 未见 |
| P2 | [#144200](https://github.com/NousResearch/hermes-agent/issues/144200) | 看板 spawn 回调在其函数体抛出 `TypeError`/`ValueError` 时被调用**两次**——产生重复副作用 | 未见 |
| P2 | [#144150](https://github.com/NousResearch/hermes-agent/issues/144150) | `hermes verify` 空操作却报告 `ok: true` 并记录通过证据 | 未见 |
| P2 | [#144003](https://github.com/NousResearch/hermes-agent/issues/144003) | Python 3.14：uv 多环境锁定无法解析 `onnxruntime`（无 android wheel）→ mnemosyne 插件在所有环境失败即关闭 | 未见 |
| P2 | [#144199](https://github.com/NousResearch/hermes-agent/issues/144199) | 并发的 `hermes chat -q` 唤醒运行在模型连接阶段以 `invalid_params` 失败（19/331） | 未见 |
| P2 | [#144183](https://github.com/NousResearch/hermes-agent/issues/144183) | 辅助密钥轮换后的重试重新发送了失败的**主**密钥，使一个健康的池条目被搁置 | 未见 |
| P2 | [#144165](https://github.com/NousResearch/hermes-agent/issues/144165) | Windows 上 `hermes update` 从 v0.22.2→v0.22.5 后网关重启未被纳入管理；启动恢复阻塞桌面端 | 无；与 Windows 更新问题群相关 |
| P2 | [#144166](https://github.com/NousResearch/hermes-agent/issues/144166) | `cron.load_jobs()` 在 Windows 临时共享冲突时抛出异常（读取端无重试，与 `atomic_replace` 不同） | 未见 |
| P2 | [#144168](https://github.com/NousResearch/hermes-agent/issues/144168) | Linux/X11 + NVIDIA：设置界面悬停过渡导致 GPU 利用率大幅飙升 | 未见 |
| P2 | [#141521](https://github.com/NousResearch/hermes-agent/issues/141521) | 桌面端按启发式将围栏代码降级为普通文本——PowerShell 代码块失去复制框，`$vars` 被渲染为数学公式 | 未见 |
| P2 | [#131786](https://github.com/NousResearch/hermes-agent/issues/131786) | 网关完成尾部对较长的单行 JSON 丢弃输出（2000 字符截断 + 换行丢弃） | 未见 |
| P3 | [#144204](https://github.com/NousResearch/hermes-agent/issues/…) | 视觉预描述提示要求“约 200 个汉字”，强制输出中文 | ✅ [PR #144204](https://github.com/NousResearch/hermes-agent/pull/144204) |

**其他无关联 Issue 的进行中修复 PR：** [PR #144201](https://github.com/NousResearch/hermes-agent/pull/144201)（放弃流式 worker 时，过期的 kill 无法回收——2.6 小时内出现 39 对相同日志）、[PR #142372](https://github.com/NousResearch/hermes-agent/pull/142372)（所有预览路径上将 `tool_preview_length: 0` 视为无限制）、[PR #140594](https://github.com/NousResearch/hermes-agent/pull/140594)（记录管道验证运行；停止重复提醒）、[PR #129837](https://github.com/NousResearch/hermes-agent/pull/129837)（Nix `extraPythonPackages` 会静默丢弃所有提供方/集成依赖）。

**评估：** 修复覆盖率偏低——**约 20 个 P2 缺陷中只有 1 个有对应的修复 PR**。20 个可见缺陷中有 5 个来自 Windows，是单一最密集的回归面。有两类问题在结构上令人担忧，而非一次性偶发：(a) **会话/标题键冲突**（#143715、#144140、#136189、#136012 描述的其实是同一个薄弱会话身份模型的不同症状）；(b) **更新/升级可靠性**（#133253、#144165、#144003）。

---

## 6. 功能请求与路线图信号

**下个周期高概率落地：**

1. **桌面端会话生命周期 + 可观测性** — [#50718](https://github.com/NousResearch/hermes-agent/issues/50718)（未读标记、需要输入的提示、OS 徽标）和 [#48997](https://github.com/NousResearch/hermes-agent/issues/48997)（显式关闭被弃用的会话/后端）。它们属于同一子系统，互动量最高，且也能缓解 [#136012](https://github.com/NousResearch/hermes-agent/issues/136012)。预计会先落地一个范围受限的子集（未读提示 + 需要输入）。
2. **插件目录治理与分级** — [#143053](https://github.com/NousResearch/hermes-agent/issues/143053)（准入途径）加上今天的 verified 层级 PR（#144203）以及待处理条目：[Frihet ERP #134790](https://github.com/NousResearch/hermes-agent/pull/134790) / pin [#144210](https://github.com/NousResearch/hermes-agent/pull/144210)、[Conxt memory #144192](https://github.com/NousResearch/hermes-agent/pull/144192)、[KAME 1.8.2.9 #141137](https://github.com/NousResearch/hermes-agent/pull/141137)。目录正成为主要分发渠道；预计会发布准入文档，并继续按 SHA 锁定进行层级审查。
3. **网关启动韧性** — [#144181](https://github.com/NousResearch/hermes-agent/issues/144181) 及其修复 [#144198](https://github.com/NousResearch/hermes-agent/pull/144198)：让 `stay_alive` 在全部三条致命路径上具有权威性。改动小、范围明确，很可能落地。
4. **将成本/延迟降低作为一项特性** — [PR #144177](https://github.com/NousResearch/hermes-agent/pull/144177)（一次性运行每次调用减少 1,570 个提示 token，每个会话减少 302 个）和 [PR #144174](https://github.com/NousResearch/hermes-agent/pull/144174)（技能索引瘦身）表明维护者正在主动精简系统提示词。提示词效率正被视为一流的路线图事项。

**概率较低 / 需要设计：**

- [#18964](https://github.com/NousResearch/hermes-agent/issues/18964) Reticulum/LXMF 网关 — 受众小，176 天内无维护者表态；走插件/连接器路线比核心支持更有可能。
- [#98004](https://github.com/NousResearch/hermes-agent/issues/98004) Bot Mode 的按组自动继续预算 — 明确排在 #89545 之后，但属于 P3 且已闲置 2 个月。
- [PR #144207](https://github.com/NousResearch/hermes-agent/pull/144207) `terminal.process_priority: low`（移植自 OpenHands）和 [PR #144211](https://github.com/NousResearch/hermes-agent/pull/144211) 可续传的模型/工具下载 — 两者都小、可选、低风险；是快速落地的好候选。
- 在 [#134556](https://github.com/NousResearch/hermes-agent/pull/134556)（`pre_send_message`）和 [#144071](https://github.com/NousResearch/hermes-agent/issues/144071)（插件人工输入回复）双双关闭后，**扩展面方向不明**。集成方短期内不应指望出站重写或跨前端审批路由。

---

## 7. 用户反馈摘要

**真实痛点，归类如下：**

- **“我看不出我的 agent 在干什么。”** 多会话桌面端用户会丢失后台工作的线索——这是互动量最高的问题。用户想要操作系统级的提示（徽标、未读红点），而不只是应用内状态。
- **“我升级不了。”** 持有大体积 `state.db` 的长期用户在结构上被“立即更新”拒之门外（#133253）；Windows 用户报告在官方 `hermes update` 之后安装变得*“又不可用了”*（#144165）。这类反馈会**侵蚀信任**，因为它阻断了对修复的获取。
- **Windows 在实践中是二等平台。** 租约泄漏（1,546 个文件）、缺少重启标记、cron 文件共享竞争，以及桌面端启动恢复阻塞——在同一个时间窗口内出现了四种不同的 Windows 独有故障。
- **多 profile 网关运维者触及扩展上限。** DM 会话无界增长（#136012）、关机刷写不可恢复（#136189）以及看板重复副作用（#144200），描述的都是服务大量 profile/任务的生产网关，其中正确性在无声退化。
- **澄清/审批流程在重连时不可靠。** 用户丢失已锁定的答案，并看到虚假的 `undelivered` 结果（#144158、#144159）——对会进入后台的移动客户端尤其代价高昂。
- **插件开发者面临接入摩擦。** 一位维护者找不到目录准入途径（#143053）；另一位被 Python 3.14 依赖解析失败阻塞（#144003）；第三位的语音插件卡在等待认证策略审查（#143585）。
- **桌面端在 Linux/NVIDIA 上资源占用高。** 悬停设置按钮会导致 GPU 利用率飙升（#144168）。

**满意信号：** 报告的质量异常高（二分定位的 SHA、诸如“331 次唤醒运行中 19 次失败”的生产指标、39 次重复的日志证据），用户还主动表达感谢并提供详细复现——善意浓厚、调查投入很高。**不满集中在更新路径的可靠性和平台特定故障上**，而非核心 agent 能力。

---

## 8. 待办观察

| 条目 | 存在时长 | 为何需要维护者关注 |
|---|---|---|
| [Issue #18964](https://github.com/NousResearch/hermes-agent/issues/18964) — Reticulum/LXMF 网关 | **176 天**（2026-05-02） | 可见集合中最古老的请求；3 👍；无维护者回应，无 PR。明确裁定“接受为插件”或“不在核心中实现”即可闭环。 |
| [Issue #50718](https://github.com/NousResearch/hermes-agent/issues/50718) — 会话可见性与通知 | **125 天**（2026-06-22） | 数据集中互动量最高（12 条评论，3 👍）却标记为 **P3**。要么提升优先级并界定范围，要么拆分三个根因，让成本较低的未读提示修复不必等待 OS 徽标工作。 |
| [Issue #48997](https://github.com/NousResearch/hermes-agent/issues/48997) — 显式运行时会话生命周期 | **128 天**（2026-06-19） | 是 #50718 和 #136012 的基础；仍未指派。需要的是设计决策，而非补丁。 |
| [PR #129837](https://github.com/NousResearch/hermes-agent/pull/129837) — Nix `extraPythonPackages` 丢弃默认依赖组 | 已开启 **24 天** | 在 NixOS/Home Manager 下静默破坏每一个提供方/集成依赖；一个范围可控、价值很高的修复，等待审查。 |
| [Issue #133253](https://github.com/NousResearch/hermes-agent/issues/133253) — 1GB 的 `state.db` 阻塞更新 | 20 天，`duplicate` | 尽管标记为重复，仍有 8 条评论；应明确指认规范 Issue，以免升级扩展性修复被遗漏。 |
| [PR #143585](https://github.com/NousResearch/hermes-agent/pull/143585) — Hermes Live 语音插件（草稿） | 2 天，明确阻塞 | 作者要求在认证策略审查（规则 11）完成前*不要*按现状合并。需要一项策略裁定，否则草稿 PR 会越积越多。 |
| [Issue #98004](https://github.com/NousResearch/hermes-agent/issues/98004) — 按组自动继续预算 | 57 天（2026-08-29） | #89545 的第 4 项；排序已有记录但无 ETA。值得确认它是否仍在路线图上。 |
| [#134556](https://github.com/NousResearch/hermes-agent/pull/134556)（已关闭）/ [#144071](https://github.com/NousResearch/hermes-agent/issues/144071)（已关闭） | — | 两者都涉及插件对网关消息/审批的控制。应记录**拒绝的理由**，以免集成方反复提出相同的扩展点。 |

**观察清单建议：** 维护者在审查上显然带宽受限（PR 关闭率 18%），并正在使用 **salvage PR**（#144211 来自 #143453，#144174 来自 #130552）来高效吸收社区工作。下一步杠杆最高的行动不是更多分诊，而是**两项设计裁定**——会话身份/生命周期，以及插件扩展面——因为今天开放的 P2 中约三分之一是这两个未决模型的下游症状。

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-10-25

*代码仓库：[agentscope-ai/QwenPaw](https://github.com/agentscope-ai/QwenPaw)*

---

## 1. 今日概览

在 2026-10-24 的统计窗口内，QwenPaw 呈现出**中高强度的流入活动与较低的落地吞吐**：共触及 15 个条目（4 个 issue、11 个 PR），但仅有 **1 个 PR 关闭、0 个发布**，最终留下 10 个 PR 和 3 个 issue 处于打开状态。被关闭的条目（#8411）带有 `Close-and-review-later` 标签——即未合并直接关闭——因此实际上**今日没有任何代码落地**。活动集中在渠道/集成工作（Matrix、QQ）、治理/审计可靠性，以及 Console 体验默认值上，这与一个面向自托管和团队部署的项目定位相符。最突出的主题是一组**静默失败报告**（#8252、#8406、#8412）——配置与审计状态在无任何告警的情况下发生变化——这说明问题指向的是可观测性欠债，而非单纯的功能缺口。当前瓶颈似乎是评审带宽，而非贡献量。

---

## 2. 发布

**无。** 过去 24 小时内没有发布新版本。issue 中由用户现场报告的最新版本为 **2.2.1**（QwenPaw Desktop，Windows 10 AMD64），见 [#8252](https://github.com/agentscope-ai/QwenPaw/issues/8252)——没有需要报告的迁移或破坏性变更说明。

---

## 3. 项目进展

仅有一个 PR 离开待处理队列，而且是被**关闭而非合并**：

- **[#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411) `[CLOSED] [Close-and-review-later, size/L]` fix(matrix): recover cached sessions and missing device keys** — 修复 [#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365)。解决密码认证的 Matrix 渠道在每次重启时重新登录的问题，以及 homeserver 已移除某设备的加密密钥、而本地加密存储仍将其标记为已共享的情况——这会导致启动时永远不会重新上传密钥，同时健康检查却错误地报告渠道正常。鉴于 `Close-and-review-later` 标签，Matrix E2EE 的恢复缺口在实践中应视为**仍然存在**。

今日没有确认合并任何功能或修复，因此本窗口内没有用户可见能力的推进。

---

## 4. 社区热点

按评论数排序（数据集中未暴露 PR 评论数）：

1. **[Issue #8252](https://github.com/agentscope-ai/QwenPaw/issues/8252) — 4 条评论，`[CLOSED] [Bug]`** — 智能体间（`ChatWithAgent`）会话被静默取消，抛出 `CancelledError`；取消成对发生在同一秒内，不产生任何产物，也不记录发起方或原因。报告者运营着一套位于飞书渠道入口之后的多智能体集群（Orchestrator / Reasoner / Executor / Reviewer / Defender），并引用了 `qwenpaw/hooks/error/error_hook.py:90` 与 `qwenpaw/runtime/run...`。**潜在需求：** 多智能体编排用户需要取消操作是*可归因的*——谁取消的、为什么取消、丢失了什么——否则集群调试将无从下手。
2. **[Issue #8406](https://github.com/agentscope-ai/QwenPaw/issues/8406) — 2 条评论，`[OPEN] [Bug]`** — QQ 渠道的 `enabled` 标志被静默重置为 `false`，且没有任何告警。**潜在需求：** 配置必须具备权威性，任何变更都必须记录日志；静默的配置回滚会破坏对长期运行部署的信任。*（数据质量注记：该 issue 正文渲染为未展开的模板占位符 `$(cat /tmp/issue_body.md)`，限制了分诊。）*
3. **[Issue #8412](https://github.com/agentscope-ai/QwenPaw/issues/8412) — 1 条评论，`[OPEN] [Bug]`** — 尽管每个 `governance/<agent>_<hash>/policy.yaml` 中都设置了 `audit_level: all`，`governance/audit.db`（`audit_events`）仍停止记录各智能体的工具调用；一旦记录停止，即使智能体持续调用工具（包括文件写入），也再不会恢复。
4. **[Issue #8407](https://github.com/agentscope-ai/QwenPaw/issues/8407) — 1 条评论，`[OPEN] [Feature]`** — 请求为 Console 的“助手消息显示模式”提供服务器端默认值（目前仅有 `localStorage` 键 `qwenpaw_assistant_message_display_mode`）。

**信号：** 排名前三的话题都在描述*状态静默变化或静默失败*。这是本周期社区最主要的关切。

---

## 5. 缺陷与稳定性

按严重程度排序：

| 严重程度 | 条目 | 状态 |
|---|---|---|
| **严重** | [#8252](https://github.com/agentscope-ai/QwenPaw/issues/8252) — 智能体间会话被静默取消；产物归零；未记录发起方/原因。影响核心多智能体编排，造成全部工作丢失。 | **已关闭，但本数据集中无关联修复 PR** — 关闭原因未经核实；根本原因可能仍未解决。 |
| **高** | [#8412](https://github.com/agentscope-ai/QwenPaw/issues/8412) — 尽管设置了 `audit_level: all`，各智能体审计日志仍永久停止。治理/合规控制静默失效（fail-open-but-silent）可以说比大声失败更糟；文件写入类工具调用不再被记录。 | 打开，无修复 PR。 |
| **高** | [#7835](https://github.com/agentscope-ai/QwenPaw/pull/7835) — 自动记忆召回向 LLM 上下文注入一对合成的 `ToolCallBlock` + `ToolResultBlock`；Console 通过 `MemorySearchCard` 将其隐藏，但**其他所有出站渠道（飞书等）都会泄漏它**。属于隐私/体验泄漏。 | 打开，自 2026-09-17 起为 `Under Review`。 |
| **中** | [#8406](https://github.com/agentscope-ai/QwenPaw/issues/8406) — QQ 渠道 enabled 标志被静默重置为 `false`；未触发任何告警。静默配置回归。 | 打开，无修复 PR。 |
| **中** | [#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411) / [#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365) — Matrix 缓存会话与设备密钥缺失的恢复；健康检查报告渠道健康，而 E2EE 密钥从未被重新上传。**健康信号误报。** | PR **未合并即关闭** — 回归风险依然存在。 |
| **中** | [#7211](https://github.com/agentscope-ai/QwenPaw/pull/7211) — `HookContext.inject_context()` 的载荷会作为用户轮次/可搜索历史持久化，而非保持在请求内局部。造成上下文污染与历史污染。 | 打开，自 2026-08-21 起为 `ready-for-human-review`。 |

**修复 PR 覆盖情况：** 6 个中有 1 个具备关联修复 PR（#8411，未合并即关闭）。其余 5 个均无。

---

## 6. 功能请求与路线图信号

- **团队/自托管可配置默认值** — [#8407](https://github.com/agentscope-ai/QwenPaw/issues/8407) 请求将助手消息显示模式从浏览器 `localStorage` 迁移到服务器提供的默认值，与现有 `/config/theme` 模式（`chat_display.assistant_message_display_mode`）保持一致。**已配套实现：[#8408](https://github.com/agentscope-ai/QwenPaw/pull/8408) `size/M`。** 改动小、自包含、遵循既有模式 → **极有可能进入下个版本。**
- **QQ C2C 流式输出** — [#8409](https://github.com/agentscope-ai/QwenPaw/pull/8409) `size/L` 通过 `POST /v2/users/{user_openid}/stream_messages` 实现 `on_streaming_start/delta/end`，由 `streaming_enabled`（`QQ_STREAMING_ENABLED`）控制开启。关闭功能请求 [#8307](https://github.com/agentscope-ai/QwenPaw/issues/8307)。属于渠道对齐工作；若评审容量允许，是下个周期的合理候选。
- **模型池生命周期** — [#8410](https://github.com/agentscope-ai/QwenPaw/pull/8410) `size/M` 为 provider 托管的条目新增移除操作（带确认），修复 [#8195](https://github.com/agentscope-ai/QwenPaw/issues/8195)。风险低；可能较早合入。
- **记忆子系统升级** — [#8347](https://github.com/agentscope-ai/QwenPaw/pull/8347) `size/XXXL` 将 ReMe 从 `0.4.1.11 → 0.4.2.0`，适配后台索引 / Auto-Dream 整合，并新增**需显式开启、默认关闭**的决策压缩、对话图像记忆与实体标注。所有新行为默认关闭 → 用户侧风险低，但 PR 体量本身构成评审瓶颈。现实来看更适合**次版本/较大版本，而非下个补丁版**。

**预测：** 下个补丁版可能包含 **#8408 + #8410**，或许还有三个琐碎的文档锚点 PR（#8401–#8403）；#8409 和 #8347 更可能顺延一个周期。

---

## 7. 用户反馈汇总

- **部署形态相当严肃，接近生产环境：** 具备角色分离（Orchestrator/Reasoner/Executor/Reviewer/Defender）的多智能体集群、飞书/Lark 入口渠道、QQ 渠道、密码认证的 Matrix homeserver，以及由管理员管理策略文件的自托管团队部署。这不是业余玩家式的用法。
- **主要痛点——静默失败：** 四个 issue 中有三个使用了“静默”一词，或描述了无任何告警的状态变化/停止（#8252 取消无发起方/原因、#8406 标志被重置、#8412 审计日志死掉）。用户不是在要功能；他们是在要求**出问题时被告知**。
- **可审计性与治理是承重结构：** `policy.yaml` + `governance/audit.db` 被用作各智能体的合规控制；它们的静默失效会动摇整个治理层的信任模型。
- **多设备/团队易用性：** 绑定 `localStorage` 的 Console 偏好被描述为在清理缓存或更换设备后丢失，且管理员无法为成员设置默认值——这是一个具体的自托管摩擦点。
- **满意度信号：** 报告详实，附带版本和文件/行号引用可复现，并伴有社区 PR（#8408 的作者同时提交了 #8407）。这表明有投入度高的用户在为项目做贡献——但同时也意味着**维护者对静默失败类缺陷的响应速度**是主要的满意度风险。

---

## 8. 积压项观察

长期未响应或高重要性、需要维护者关注的条目：

1. **[PR #7211](https://github.com/agentscope-ai/QwenPaw/pull/7211)** — `first-time-contributor`、`ready-for-human-review`、`size/L`，自 **2026-08-21 起打开（约 9 周）**。修复被注入的上下文持久化进对话历史的问题（#6360、#6358）。一位首次贡献者的 PR 在 `ready-for-human-review` 停留 2 个多月，属于社区留存风险。
2. **[PR #7390](https://github.com/agentscope-ai/QwenPaw/pull/7390)** — `size/XS` 纯测试的回归防护，针对 Aliyun Coding Plan 目录漂移（#6551），自 **2026-08-28 起打开（约 8 周）**。体量极小、价值明确，却仍未合并——值得升级处理的评审队列异常。
3. **[PR #7835](https://github.com/agentscope-ai/QwenPaw/pull/7835)** — 自 **2026-09-17 起处于 `Under Review`（约 5 周）**。修复一个真实的跨渠道隐私泄漏（记忆召回轨迹逃逸到飞书及其他渠道）。基于隐私理由应优先处理。
4. **[PR #8347](https://github.com/agentscope-ai/QwenPaw/pull/8347)** — `size/XXXL` 的 ReMe 升级。需要维护者的评审带宽，并与 ReMe [#672](https://github.com/agentscope-ai/ReMe/pull/672) 协调；停放越久，合并冲突风险越高。
5. **[Issue #8252](https://github.com/agentscope-ai/QwenPaw/issues/8252)** — 在本数据集中**无关联修复 PR** 即被关闭。已关闭但未解决的关键编排缺陷；建议维护者补一条关闭后说明，确认修复版本或根本原因。
6. **[Issue #8412](https://github.com/agentscope-ai/QwenPaw/issues/8412)** — 治理审计数据库静默死掉，属于合规级缺陷，却零修复 PR；建议在本周期内打上分诊标签并指定负责人。
7. **[Issue #8406](https://github.com/agentscope-ai/QwenPaw/issues/8406)** — 正文仍包含未展开的 `$(cat /tmp/issue_body.md)` 占位符；需要先请求复现信息才能推进。

---

*本摘要基于截至 2026-10-24 的 24 小时窗口内更新的 4 个 issue 和 11 个 pull request 编制。源数据中 PR 评论数与 👍 总数不可用（`undefined`），故未纳入排序。*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-10-25

---

## 1. 今日概览

ZeroClaw 正处于**发布稳定化阶段**：过去 24 小时内有 6 个 issue 关闭，其中包括一个 P0 级 SOP 引擎正确性缺陷和两项高风险安全事项；PR #12011（`chore(release): bump version to v0.8.7`）已关闭，同时若干缺陷明确带有 `release:v0.8.7` 标签。活动量很高——触及 18 个 issue 和 50 个 PR——但结构失衡：**50 个更新的 PR 中有 47 个仍处于开放状态**，而仅 3 个已合并/关闭，这表明评审积压正在扩大，而非以合并为主的冲刺。尚未发布任何 GitHub Release，因此 v0.8.7 版本号提升已准备就绪但尚未交付。质量信号喜忧参半但偏正面：安全响应能力强（RUSTSEC 公告当日关闭），同时内存子系统中持续存在 S0 数据丢失风险（#10797），该问题已开放 6 周。

> *数据说明：* PR 数据流中所有条目均报告 `Comments: undefined`，因此无法对 PR 评论数进行排名。下方“社区热门话题”按 issue 评论数和标签权重排名。

---

## 2. 发布

**过去 24 小时内未发布新版本。**

不过，新版本显然即将发布：

- **PR #12011 [已关闭]** — `chore(release): bump version to v0.8.7` — 对 `Cargo.toml`、`Cargo.lock`（30 个工作区包）、Tauri 配置、`setup.bat` 以及 crates.io/Discord 元数据进行协同工作区版本提升 0.8.6 → 0.8.7。
  https://github.com/zeroclaw-labs/zeroclaw/pull/12011
- 今日关闭的带有 `release:v0.8.7` 标签的 issue：**#10066**（SOP schema 拒绝排序）、**#11899**（重载后网关继续服务陈旧连接）、**#12009**（RUSTSEC-2026-0385 / -0386 阻塞 Security 作业）。

**v0.8.7 预计内容：** SOP 运行完整性修复、网关重载生命周期修复、容器 CA bundle 刷新，以及依赖安全公告修复。数据中未显示破坏性变更或迁移说明。

---

## 3. 项目进展

**已合并/关闭的 PR（3 个）：** 前 20 条样本中仅可见 **#12011**（版本号提升）；其余两个已合并/关闭 PR 不在所提供的数据切片中。

**已关闭 issue——实际落地或已解决的内容（6 个）：**

| Issue | 严重级别 | 结果 |
|---|---|---|
| [#10066](https://github.com/zeroclaw-labs/zeroclaw/issues/10066) — SOP 引擎在记录输出 schema 拒绝之前就推进后续步骤 | S1, P0 | 已关闭；阻塞工作流的排序缺陷已在 v0.8.7 中修复 |
| [#12009](https://github.com/zeroclaw-labs/zeroclaw/issues/12009) — `cargo deny` 因 RUSTSEC-2026-0385（ctutils）/ -0386（tokio）失败 | S1 | 报告当日关闭；CI 解除阻塞 |
| [#11932](https://github.com/zeroclaw-labs/zeroclaw/issues/11932) — 容器 CA bundle 信任已不受信任的 emSign 证书链 | S0 安全 | 已关闭；原生 CA bundle 已修正 |
| [#11899](https://github.com/zeroclaw-labs/zeroclaw/issues/11899) — 已停止的网关在重载后仍继续服务已接受的连接 | 网关/配置生命周期 | 已关闭；已处理重载后的配置代际泄漏 |
| [#9592](https://github.com/zeroclaw-labs/zeroclaw/issues/9592) — provider 探测读取更新前配置快照 | S2 | 已关闭；`model_routing_config` 现在探测已保存的别名 |
| [#12016](https://github.com/zeroclaw-labs/zeroclaw/issues/12016) — 允许嵌入二进制在聊天和 cron 轮次中添加钩子 | 功能 | 提交后一天内关闭（处理迅速） |

**开放 PR 中推进的功能：** 会话事件历史（#11921）、Goal Mode 命令语法（#11879，仅为休眠配置）、cron 到会话绑定（#11441）、XMPP/OMEMO 通道（#11847）、仅入站电子邮件（#12003）、`send_via` 附件（#11997），以及两个 RPC/TUI 签名加固 PR（#11422、#12013）。

---

## 4. 社区热门话题

按评论量排名（仅 issue；PR 评论数据不可用）。

1. **[#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891) — [Tracker] 持久内存：将策展、相关性和可操作性平面接通至对等水平** — 12 条评论，自 2026-07-09 开放。
   该项目中讨论量最高的条目。一个明确标注的路线图/史诗跟踪器，协调多 PR 分批落地；2026-08-29 的实时快照列出 7 个未完成项（4 个 issue，3 个 PR）。*潜在需求：* 用户希望跨会话内存达到成熟同类运行时水平——这是 ZeroClaw 感知到的最大能力缺口，也是持续时间最长的协调负担。

2. **[#10066](https://github.com/zeroclaw-labs/zeroclaw/issues/10066) — SOP 引擎排序缺陷** — 5 条评论，现已关闭。
   对一个 P0 正确性 issue 参与度高；讨论集中在运行状态完整性，以及验证拒绝与步骤推进如何交错。

3. **[#10797](https://github.com/zeroclaw-labs/zeroclaw/issues/10797) — Markdown 内存后端在重叠 `store()` 时丢失条目** — 3 条评论，仍开放。
   由 agent 上报的路径提交；S0 严重级别。*潜在需求：* 为最简单、采用最广的内存后端提供持久性保证。

4. **[#9592](https://github.com/zeroclaw-labs/zeroclaw/issues/9592) — 路由更新后的 provider 别名探测** — 3 条评论，已关闭。

5. **[#12014](https://github.com/zeroclaw-labs/zeroclaw/issues/12014)**、**[#12005](https://github.com/zeroclaw-labs/zeroclaw/issues/12005)**、**[#11799](https://github.com/zeroclaw-labs/zeroclaw/issues/11799)**、**[#11992](https://github.com/zeroclaw-labs/zeroclaw/issues/11992)**、**[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853)** — 各有 2 条评论。

**信号：** 社区注意力集中在**内存正确性**和**provider/配置保真度**，而非新的通道集成。贡献者 `mov-xound-glitch` 非常高产（本时间窗口内 5 个 issue + 4 个 PR），`JordanTheJet` 则主导 SOP/完整性及带发布标签的工作流。

---

## 5. 缺陷与稳定性

按严重级别排序：

**S0 — 数据丢失**
- **[#10797](https://github.com/zeroclaw-labs/zeroclaw/issues/10797) [开放]** — `MarkdownMemory::store` 会从陈旧快照重写文件；没有任何机制串行化并发写入者或验证写入已落盘。重叠的 `store()` 调用会静默丢失条目。*已开放 6 周，本窗口内无关联修复 PR——最高优先级缺口。* 相关开放 PR [#11663](https://github.com/zeroclaw-labs/zeroclaw/pull/11663) 涉及内存冲突处理，但不涉及此写入路径。

**S1 — 工作流受阻**
- **[#12014](https://github.com/zeroclaw-labs/zeroclaw/issues/12014) [开放]** — 自定义 OpenAI 兼容 provider 在缺少 `context_window` 时硬回退到 **32,000 tokens**（`CompatibilityFallback`），随后在本地拒绝有效轮次。报告并更新于 2026-10-24。**已有修复 PR：[#12017](https://github.com/zeroclaw-labs/zeroclaw/pull/12017)**——"将上下文窗口视为模型元数据，而非配置。" 下一版本的有力候选。
- **[#12005](https://github.com/zeroclaw-labs/zeroclaw/issues/12005) [开放]** — PowerShell 方言解析器拒绝**任何包含裸 `@` 的命令**，导致一个已加入允许列表的邮件 CLI 不可用。作为 `zeroclaw-config/src/policy.rs` 中的**回归**提交，影响 Windows x86_64 上的 v0.8.6。无关联修复 PR。
- **[#12009](https://github.com/zeroclaw-labs/zeroclaw/issues/12009) [已关闭]** — 两个 RUSTSEC 公告（发布于 2026-10-23）导致每次 `master` 构建上的 `cargo deny check` 失败。一天内解决。

**安全 / 供应链**
- **[#11932](https://github.com/zeroclaw-labs/zeroclaw/issues/11932) [已关闭]** — 已不受信任的 emSign 中间证书仍保留在已发布的容器 CA bundle 中；仅修补 Rust 验证器并不足够。
- **[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853) [开放]** — 自动化 `npm audit` 失败（high/critical：1）影响 `@tailwindcss/typography` 等；自 2026-10-19 开放，由 bot 提交，2 条评论。用户影响低，但构成反复出现的 CI 噪音。

**S3 — 次要**
- **[#12006](https://github.com/zeroclaw-labs/zeroclaw/issues/12006) [开放]** — `tool_search` 从注册表构建时的副本提供延迟工具 schema，因此由实时配置驱动的 schema 在重载后变得陈旧。**修复 PR：[#12007](https://github.com/zeroclaw-labs/zeroclaw/pull/12007)。**
- **[#11792](https://github.com/zeroclaw-labs/zeroclaw/issues/11792) [开放]** — 中断的 `plugin install` 可能留下孤立的 `.<name>.installing-v1-<id>` 暂存目录，且没有任何命令回收（磁盘泄漏）。标记为 `release:v0.9.0`。
- **[#11946](https://github.com/zeroclaw-labs/zeroclaw/issues/11946) / [#11945](https://github.com/zeroclaw-labs/zeroclaw/issues/11945) [开放]** — 来自 #11861 的后续事项：陈旧的确定性 SOP 检查点副本不得覆盖所有者的 `{run}.state.json`，另缺少这些分支的测试覆盖。

**已关闭的稳定性成果：** [#11899](https://github.com/zeroclaw-labs/zeroclaw/issues/11899)（网关重载后连接/配置代际泄漏）与 [#10066](https://github.com/zeroclaw-labs/zeroclaw/issues/10066)（SOP schema 拒绝排序）都代表对长生命周期运行有意义的完整性改进。

---

## 6. 功能请求与路线图信号

**本窗口内明确的用户请求：**

| 请求 | Issue | 近期交付可能性 |
|---|---|---|
| Shell 策略拒绝应指明拒绝规则并接受操作员提示 | [#11799](https://github.com/zeroclaw-labs/zeroclaw/issues/11799) | 中等——小型、范围明确、贴近安全的 DX 修复 |
| `send_via` 显式 `attachments` 在读取策略下检查 | [#11996](https://github.com/zeroclaw-labs/zeroclaw/issues/11996) | **高**——实现已作为 PR [#11997](https://github.com/zeroclaw-labs/zeroclaw/pull/11997) 开放 |
| 电子邮件通道：操作员模板 + 净化后的 HTML 正文 | [#11992](https://github.com/zeroclaw-labs/zeroclaw/issues/11992) | 中等——与 #11997 自然配套 |
| 嵌入二进制为聊天/cron 轮次添加钩子 | [#12016](https://github.com/zeroclaw-labs/zeroclaw/issues/12016) | **已关闭**（2026-10-24） |
| 无 SMTP 的仅入站电子邮件通道 | PR [#12003](https://github.com/zeroclaw-labs/zeroclaw/pull/12003) | 评审中——降低只读邮箱访问门槛 |

**路线图信号：**
- **Goal Mode V1** 正以 12 部分提案系列分阶段推进；[#11879](https://github.com/zeroclaw-labs/zeroclaw/pull/11879) 是“提案 2/12”，并且刻意保持**休眠**（仅配置 + 命令语法，无可执行行为）。预计 Goal Mode 将跨多个版本落地，而非在 v0.8.7 中。
- **会话事件历史**（[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)，size:XL）提出仅追加的规范会话事件，以实现副作用安全重放——这是架构变更，而非单点功能。
- **持久内存对等**（[#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891)）仍是路线图的首要承诺。
- **消息广度**：带可选 OMEMO 的 XMPP/Prosody（[#11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847)）表明对自托管/联邦传输的持续需求。

**预测：** v0.8.7 将随上述 SOP/网关/安全修复发布，且很可能不含新功能。v0.9.0 范围正围绕插件安装健壮性（#11792）、`send_via` 附件（#11997）、上下文窗口语义（#12017）以及持续的内存工作（#11663、#8891）收敛。

---

## 7. 用户反馈摘要

**痛点，按证据强度排序：**

1. **内存尚不可信。** 参与度最高的跟踪器（#8891，12 条评论，已 3.5 个月）加上 S0 并发写入数据丢失缺陷（#10797）以及 supersession 评分缺陷（[#11663](https://github.com/zeroclaw-labs/zeroclaw/pull/11663)——BM25 归一化可能给无关行赋予 `1.0`，导致整合时将其软隐藏）都指向同一个未满足需求：*可靠、非破坏性的长期召回*。
2. **平台/区域设置边缘情况影响严重。** PowerShell `@` 回归（#12005）使整个已加入允许列表的工作流在 Windows 上不可用——这是安全加固破坏合法操作员的案例。类似地，provider 配置缺口会静默退化为本地拒绝的轮次（#12014）。
3. **不透明的安全拒绝令操作员沮丧。** #11799 明确要求说明命令为何被拒绝，并提供可由操作员配置的提示。这是对安全层可用性的抱怨，而非要求削弱它。
4. **电子邮件仍是二等公民。** 自 #9993 起，发送侧完全无法附加文件（#11996），而仅入站邮箱甚至无法表达（#12003）。用户正推动电子邮件达到生产级对等。
5. **问题归因/响应速度是一个积极信号。** #12016 在 24 小时内关闭；#12009（破坏 CI 的公告）在提交当天关闭；#10066 与 #11932（均为高风险）今日关闭。本窗口内的不满来自能力缺口，而非维护者忽视。

**数据中可见的使用场景：** 将 ZeroClaw 作为库嵌入更大的二进制（钩子，#12016）；使用邮件 CLI 的 Windows 原生 shell 自动化；具有非常规上下文大小的 OpenAI 兼容自托管/第三方端点；只读合规邮箱；通过 XMPP 的联邦聊天。

---

## 8. 待办积压观察

需要维护者关注的事项，按风险 × 陈旧度排序：

| 事项 | 存在时长 | 为何重要 |
|---|---|---|
| **[#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891)** — 持久内存对等跟踪器 | 约 3.5 个月（创建于 2026-07-09） | 参与度最高的条目；快照日期为 2026-08-29，现已陈旧约 8 周。需要刷新状态表。 |
| **[#10797](https://github.com/zeroclaw-labs/zeroclaw/issues/10797)** — Markdown 后端数据丢失（S0） | 约 6 周（2026-09-12） | S0 且无关联修复 PR。数据集中持续时间最长的严重开放缺陷。 |
| **[#9420](https://github.com/zeroclaw-labs/zeroclaw/pull/9420)** — Anthropic 存储的 OAuth 配置档案 | 约 3 个月（2026-07-26） | Size:XL、`risk:high`、`trusted contributor`；触及 core/provider/gateway 与安全域。高价值、高评审成本——很可能缺乏评审者带宽。 |
| **[#10446](https://github.com/zeroclaw-labs/zeroclaw/pull/10446)** — 拒绝泄漏进普通文本的工具调用信封 | 约 2 个月（2026-08-29） | `domain:security`、`risk:high`、`size:XL`；描述了 `gpt-5.6`/codex 上间歇性生产发生的情况。据作者称可干净合并——阻塞在评审，而非冲突。 |
| **[#11422](https://github.com/zeroclaw-labs/zeroclaw/pull/11422)** — TUI 签名绑定到配置拥有的密钥 | 约 3 周（2026-10-02） | `size:XL`，阻塞于 #11411/#11576；#12013 已从中提取为更小、可测试的切片——这是值得延续的良好解除阻塞模式。 |
| **[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853)** — `npm audit` 失败（bot） | 6 天 | 反复出现的自动化安全发现需要分诊/轮换策略，而非一次性修复。 |
| **[#11792](https://github.com/zeroclaw-labs/zeroclaw/issues/11792)** — 孤立的插件安装暂存目录 | 9 天 | 标记为 v0.9.0 且无关联 PR；一个小型、自包含的修复，可清除一类已知磁盘泄漏。 |

**积压健康度结论：** **47 个开放 / 3 个关闭的 PR 比例**是本摘要中最重要的结构性数字。若干大型、高风险 PR（#9420、#10446、#11422、#11921）在技术上已就绪，但受限于评审，而快速收益（#12007、#12013、#11997）则在推进。瓶颈是对 XL/`risk:high` 变更的评审能力，而非贡献者供给。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*