# OpenClaw 生态日报 2026-10-28

> Issues: 500 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-10-28 01:35 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-10-28

## 1. 今日概览
截至 2026-10-28，OpenClaw 展现出极高吞吐：过去 24 小时有 500 个 issue 和 500 个 PR 更新，其中 391 个活跃 issue、109 个已关闭 issue、275 个开放 PR、225 个已合并/关闭 PR。三个版本分别落地到 beta、非 beta 和 extended-stable 通道，表明发布工程活跃。然而，issue 构成严重偏向生产稳定性：在前 50 个抽样中至少出现 9 个开放 P0 issue，涵盖 Gateway 事件循环阻塞、Windows/更新失败、会话/DB 恢复以及 memory 引导。整体健康度喜忧参半：贡献者/维护者吞吐强劲且社区复现活跃，但稳定性风险偏高，因为若干高评论数 P0/P1 issue 仍没有可见的修复 PR。

## 2. 发布
过去 24 小时发布了三个新版本：

- [v2026.10.10-beta.4](https://github.com/openclaw/openclaw/releases/tag/v2026.10.10-beta.4) — Beta 版本。主要声明修复：自动化在显式的同一会话定时后续操作中保留已认证操作员权限，包括一次性 `next_check` 重新激活，同时保留角色、会话、取消、目标和工具策略的撤销边界。提供的说明中未声明破坏性变更或迁移步骤。
- [v2026.10.8](https://github.com/openclaw/openclaw/releases/tag/v2026.10.8) — 聚焦更新恢复的版本。声明修复包括：普通更新后 Doctor 失败时重启 Gateway，而不是让其保持停止状态；防止孤立的维护 worker 阻塞 CLI 命令和 Gateway 启动；避免在 ACL 文件系统和 Windows 相关更新路径上中止更新。未明确声明破坏性变更或迁移说明。
- [v2026.9.35](https://github.com/openclaw/openclaw/releases/tag/v2026.9.35) — 仅 Gateway 的 `extended-stable` 版本，被描述为当前 LTS 等效版本。它基于 2026 年 9 月底的 OpenClaw，加上关键安全更新、可靠性/性能修复和新模型支持。

**迁移/破坏性变更评估：** 提供的发布说明中未列出破坏性变更或显式迁移步骤。对于要从更新后 Gateway 失败状态中恢复的运维人员，v2026.10.8 是最相关的版本；v2026.9.35 是保守的 extended-stable 通道。

## 3. 项目进展
过去 24 小时，225 个 PR 已合并/关闭，275 个仍开放。样本显示在安全边界、更新/重启可靠性、会话恢复、可访问性和平台特定修复方面均有进展。

**样本中已关闭/合并 PR 信号：**
- [PR #186417](https://github.com/openclaw/openclaw/pull/186417) — 已关闭的安全修复：webhook、mapped-hook、plugin-trigger 和 join-introduction 轮次不再使用 owner-only 工具运行。
- [PR #183018](https://github.com/openclaw/openclaw/pull/183018) — 已关闭的 UI 修复：将访客访问指引保留在 Profile 中，同时避免 Inbox 中出现误导性警告。
- [PR #178347](https://github.com/openclaw/openclaw/pull/178347) — 已关闭的文档修复：澄清基于镜像的主机在升级后若在 Doctor 运行前持续宕机，如何恢复。

**推进项目的重要开放/就绪 PR：**
- [PR #185730](https://github.com/openclaw/openclaw/pull/185730) — 大型重构：在优雅重启后恢复正在运行的会话，减少长时间重启排空和恢复争用。
- [PR #185939](https://github.com/openclaw/openclaw/pull/185939) — 从并发追加中恢复 transcript 重写，针对会话/transcript 完整性。
- [PR #186420](https://github.com/openclaw/openclaw/pull/186420) 和 [PR #186426](https://github.com/openclaw/openclaw/pull/186426) — 改进配额类 429 时的模型回退行为，而不是在固定模型或同 profile 认证轮换上走入死胡同。
- [PR #186415](https://github.com/openclaw/openclaw/pull/186415) — 防止打开的 node skill 监视将 Gateway 重启推迟满 300 秒。
- [PR #186411](https://github.com/openclaw/openclaw/pull/186411) — 修复更新清理未通过其自身目标检查的问题。
- [PR #185921](https://github.com/openclaw/openclaw/pull/185921) — 添加 TUI 屏幕阅读器模式；若合并，将关闭可访问性 issue #9637。
- [PR #186416](https://github.com/openclaw/openclaw/pull/186416) — 恢复多 agent Gateway 上的 iOS 图片分享。
- [PR #185028](https://github.com/openclaw/openclaw/pull/185028) — 在浏览器导航防护中保留原生 CORS 预检请求。
- [PR #184524](https://github.com/openclaw/openclaw/pull/184524) — 修复流式回复引用空命令输出时二次方级重复扫描的问题。
- [PR #184712](https://github.com/openclaw/openclaw/pull/184712) — 压缩连续的 Telegram 进度工具调用，以保持预览有用。

**样本中已关闭 issue 信号：** 重复 transcript 写入（[#118185](https://github.com/openclaw/openclaw/issues/118185)）、两个文件后 memory 索引停滞（[#130955](https://github.com/openclaw/openclaw/issues/130955)）、计费冷却恢复请求（[#115642](https://github.com/openclaw/openclaw/issues/115642)）、原生 Codex 中止作用域完成失败（[#167410](https://github.com/openclaw/openclaw/issues/167410)）、memory 索引 SIGTERM 孤儿问题（[#185902](https://github.com/openclaw/openclaw/issues/185902)），以及更新失败报告（[#169937](https://github.com/openclaw/openclaw/issues/169937)、[#153094](https://github.com/openclaw/openclaw/issues/153094)）。

## 4. 社区热门话题
提供的 PR 评论数为空，因此下面热门话题排名使用 issue 评论数。反应数总体较低；样本中最明显的支持是 [#7722](https://github.com/openclaw/openclaw/issues/7722) 的 4 个 👍。

- [#160959](https://github.com/openclaw/openclaw/issues/160959) — **58 条评论，P0。** Gateway 在捕获大型外部/工作区插件时阻塞数分钟，这是 2026.9.6 的回归。**底层需求：** 插件捕获隔离、流式限制或异步准备，使 Gateway 启动和运行时重新发布不会耗尽事件循环。
- [#119720](https://github.com/openclaw/openclaw/issues/119720) — **25 条评论，P1。** 同步 agent 持久化和 transcript 维护在规模化时阻塞 Gateway 事件循环。**底层需求：** 异步持久化、背压以及有界的 transcript 维护工作。
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — **22 条评论，P0。** Windows 托管服务安装表现为启动缓慢、插件关闭不干净以及 `schtasks` 权限错误，使重启/更新不可靠。**底层需求：** 更强的 Windows 服务生命周期处理和更新恢复。
- [#157325](https://github.com/openclaw/openclaw/issues/157325) — **19 条评论，P0。** 卡住的 agent-DB 资源会使每个 agent 的回复都失败，直到 Gateway 重启。**底层需求：** DB 资源健康检查、自动恢复以及按 agent 隔离故障。
- [#120385](https://github.com/openclaw/openclaw/issues/120385) — **18 条评论，P2。** Code Mode 工具目录对定时/cron 和 memory-flush 轮次不完整，导致 “Unknown tool id” 失败。**底层需求：** 在所有轮次类型中保持一致的工具发现和目录构建。
- 次级高互动 issue：[#166634](https://github.com/openclaw/openclaw/issues/166634)（15 条评论，P0 回收检查点失败）、[#121953](https://github.com/openclaw/openclaw/issues/121953)（14 条评论，P1 DeepSeek cron 前缀停滞）、[#177656](https://github.com/openclaw/openclaw/issues/177656)（14 条评论，P1 共享状态完整性错误），以及 [#172837](https://github.com/openclaw/openclaw/issues/172837)（14 条评论，P1 Codex 原生子 agent 分配未移除）。

## 5. 缺陷与稳定性
**前 50 个样本中的 P0 issue——最高严重性：**
- [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway 事件循环在大型插件捕获时阻塞数分钟。提供的标签中未看到新的修复 PR。
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows 启动缓慢、插件关闭不干净以及 `schtasks` 权限错误。未看到新的修复 PR。
- [#157325](https://github.com/openclaw/openclaw/issues/157325) — 卡住的 agent-DB 资源导致所有 agent 回复失败，直到重启。标记为 `needs-live-repro`。
- [#166634](https://github.com/openclaw/openclaw/issues/166634) — 晚期回收检查点失败导致 agent 在崩溃恢复后不可用。标记为 `not-repro-on-main`。
- [#161921](https://github.com/openclaw/openclaw/issues/161921) — `state-migrated-no-rollback` 使 `doctor --fix` 和 `plugins update` 相互阻塞。未看到新的修复 PR。
- [#167406](https://github.com/openclaw/openclaw/issues/167406) — Windows MSIX 启用 Codex 的 Gateway 启动耗尽主线程。未看到修复 PR。
- [#185819](https://github.com/openclaw/openclaw/issues/185819) — Windows 快速启动中的 Codex 入门流程完成设备认证，但因 harness 运行时制品未激活而激活失败。未看到修复 PR。
- [#164800](https://github.com/openclaw/openclaw/issues/164800) — 从 2026.9.7 到 2026.9.8 的托管更新使激活驱动失效，直到主机重启。未看到修复 PR。
- [#158912](https://github.com/openclaw/openclaw/issues/158912) — 尽管文档如此，`MEMORY.md` 在 2026.9.6 的引导时不再注入。未看到修复 PR。

**部分 P1 稳定性 issue：**
- [#119720](https://github.com/openclaw/openclaw/issues/119720) — 同步持久化/transcript 维护阻塞事件循环。
- [#117262](https://github.com/openclaw/openclaw/issues/117262) — 来自三个并发写句柄的 SQLite 争用导致约 33 秒事件循环停顿。
- [#121953](https://github.com/openclaw/openclaw/issues/121953) — 由于 `[cron:...]` 前缀，cron agent 轮次在 DeepSeek 上停滞。
- [#177656](https://github.com/openclaw/openclaw/issues/177656) — 原生主机 exec 返回共享状态完整性准入错误，且无子进程结果。
- [#172837](https://github.com/openclaw/openclaw/issues/172837) — Codex 原生子 agent 分配在完成投递放弃后从未移除。
- [#181432](https://github.com/openclaw/openclaw/issues/181432) — 2026.10.5 中基线 RSS 过高（约 1.9 GB）且 SQLite 事务超时。
- [#166017](https://github.com/openclaw/openclaw/issues/166017) — MCP 会话空闲 TTL 默认值未应用；spawn broker 达到 256 个子进程硬上限。有关联的开放 PR。
- [#169797](https://github.com/openclaw/openclaw/issues/169797) — 单 CPU 主机上 Gateway 内存回归以及启动/首条回复变慢。
- [#181078](https://github.com/openclaw/openclaw/issues/181078) — Agent 轮次间歇性失败，报 “Canonical session reader continuation is no longer current.”
- [#184720](https://github.com/openclaw/openclaw/issues/184720) — Windows 中断更新修复留下空的 Doctor 保管回执。
- [#178359](https://github.com/openclaw/openclaw/issues/178359) — `doctor --fix --non-interactive` 在插件捕获阶段空转 15 分钟以上。
- [#118776](https://github.com/openclaw/openclaw/issues/118776) — 叶子子 agent 保留 `sessions_yield`，但丢失了本可产生所等待事件的工具。
- [#166964](https://github.com/openclaw/openclaw/issues/166964) — 当请求方权限被撤销时，子 agent 完成投递永久失败。

**修复 PR 可见性：** 评论数最高的 P0——[#160959](https://github.com/openclaw/openclaw/issues/160959)、[#170956](https://github.com/openclaw/openclaw/issues/170956)、[#161921](https://github.com/openclaw/openclaw/issues/161921)、[#164800](https://github.com/openclaw/openclaw/issues/164800) 和 [#185819](https://github.com/openclaw/openclaw/issues/185819)——在提供的标签中未显示可见修复 PR。相邻领域存在相关开放 PR：transcript 重写恢复（[#185939](https://github.com/openclaw/openclaw/pull/185939)）、模型回退（[#186420](https://github.com/openclaw/openclaw/pull/186420)、[#186426](https://github.com/openclaw/openclaw/pull/186426)）、重启推迟（[#186415](https://github.com/openclaw/openclaw/pull/186415)）、更新清理（[#186411](https://github.com/openclaw/openclaw/pull/186411)）、浏览器 CORS（[#185028](https://github.com/openclaw/openclaw/pull/185028)），以及 iOS 图片分享（[#186416](https://github.com/openclaw/openclaw/pull/186416)）。

## 6. 功能请求与路线图信号
- [#7722](https://github.com/openclaw/openclaw/issues/7722) — 文件系统沙箱配置（`tools.fileAccess`），支持允许/拒绝路径。安全敏感，4 个 👍，已标记供产品和安全审查。重要，但若无明确安全设计不太可能落地。
- [#13219](https://github.com/openclaw/openclaw/issues/13219) — 按模型的使用量日志，用于成本跟踪。运维需求明确；目前用户必须手动解析 session JSONL。
- [#9637](https://github.com/openclaw/openclaw/issues/9637) — 禁用 TUI 中 emoji/unicode 的可访问性选项。已有开放实现 PR：[#185921](https://github.com/openclaw/openclaw/pull/185921) 添加屏幕阅读器模式，使其成为较可能近期落地的 UX 改进之一。
- [#52732](https://github.com/openclaw/openclaw/issues/52732) — 在 `agents.list` 中按 agent 覆盖 `compaction` 和 `contextPruning`。需要产品决策；与多 agent 部署相关。
- [#175826](https://github.com/openclaw/openclaw/issues/175826) — Android 在聊天内可选问题和原生审批请求方面的功能对齐。需要在实现前完成设计验收。
- [#172547](https://github.com/openclaw/openclaw/issues/172547) — 从 memory-search 索引和召回信号中排除围栏式 dreaming 范围。需要围绕 memory 语义做出产品决策。
- [#184600](https://github.com/openclaw/openclaw/issues/184600) — Skill Workshop 未经用户审查/同意自动变更技能。这既是缺陷/UX 问题，也是治理/安全路线图信号。

**基于开放 PR 的可能下一版本候选：** TUI 屏幕阅读器模式（[#185921](https://github.com/openclaw/openclaw/pull/185921)）、模型回退/配额处理（[#186420](https://github.com/openclaw/openclaw/pull/186420)、[#186426](https://github.com/openclaw/openclaw/pull/186426)）、重启/更新可靠性（[#186415](https://github.com/openclaw/openclaw/pull/186415)、[#186411](https://github.com/openclaw/openclaw/pull/186411)），以及会话续跑/恢复工作（[#185730](https://github.com/openclaw/openclaw/pull/185730)、[#185939](https://github.com/openclaw/openclaw/pull/185939)）。沙箱、按 agent 修剪和 Android 审批对齐看起来更依赖产品/安全决策。

## 7. 用户反馈摘要
**主要痛点：** 生产可靠性和升级安全。用户报告插件捕获和持久化导致 Gateway 事件循环停顿（[#160959](https://github.com/openclaw/openclaw/issues/160959)、[#119720](https://github.com/openclaw/openclaw/issues/119720)、[#117262](https://github.com/openclaw/openclaw/issues/117262)）；Windows 服务/更新失败（[#170956](https://github.com/openclaw/openclaw/issues/170956)、[#164800](https://github.com/openclaw/openclaw/issues/164800)、[#184720](https://github.com/openclaw/openclaw/issues/184720)、[#185819](https://github.com/openclaw/openclaw/issues/185819)）；以及更新/Doctor/插件死锁（[#161921](https://github.com/openclaw/openclaw/issues/161921)）。

**会话与消息完整性：** 反复报告 agent DB 状态卡住（[#157325](https://github.com/openclaw/openclaw/issues/157325)）、回收检查点失败（[#166634](https://github.com/openclaw/openclaw/issues/166634)）、canonical 会话读取器错误（[#181078](https://github.com/openclaw/openclaw/issues/181078)）、重复 transcript 写入（[#118185](https://github.com/openclaw/openclaw/issues/118185)，已关闭），以及子 agent 完成投递失败（[#166964](https://github.com/openclaw/openclaw/issues/166964)）。

**Memory/索引：** 缺少 `MEMORY.md` 引导（[#158912](https://github.com/openclaw/openclaw/issues/158912)）、重建索引锁阻止修复并累积 19 GB 临时 DB（[#136311](https://github.com/openclaw/openclaw/issues/136311)）、全量索引发布耗尽 SQLite busy timeout（[#143640](https://github.com/openclaw/openclaw/issues/143640)）、memory 膨胀回归（[#181432](https://github.com/openclaw/openclaw/issues/181432)、[#169797](https://github.com/openclaw/openclaw/issues/169797)），以及 dreaming ranker/applier 不匹配（[#121232](https://github.com/openclaw/openclaw/issues/121232)）。

**安全/治理与 UX：** 向 hook/join-introduction 轮次暴露 owner-only 工具的问题已在 [#186417](https://github.com/openclaw/openclaw/pull/186417) 中修复；用户仍然要求文件系统沙箱（[#7722](https://github.com/openclaw/openclaw/issues/7722)）和 Skill Workshop 自动变更的治理（[#184600](https://github.com/openclaw/openclaw/issues/184600)）。UX 摩擦包括 Control UI transcript 抖动（[#164394](https://github.com/openclaw/openclaw/issues/164394)）、webchat 通知错位（[#177094](https://github.com/openclaw/openclaw/issues/177094)）、掩码配置值导致故障误诊（[#173313](https://github.com/openclaw/openclaw/issues/173313)）、Telegram 进度预览拥挤（[#184712](https://github.com/openclaw/openclaw/pull/184712)），以及可访问性缺口（[#9637](https://github.com/openclaw/openclaw/issues/9637)、[#185921](https://github.com/openclaw/openclaw/pull/185921)）。

**情绪评估：** 未提供调查数据，但 issue 情绪在生产稳定性和升级方面较为紧张。积极信号包括社区积极复现、详细的运维报告、关联 PR 和维护者分诊标签；不满集中在反复出现的 P0/P1 可靠性失败，以及回滚/诊断路径不足。

## 8. 积压项观察
这些条目陈旧或影响重大、讨论较多，且仍需维护者关注：

- [#160959](https://github.com/openclaw/openclaw/issues/160959) — 自 2026-09-29 起为 P0，58 条评论，无新修复 PR。最高优先级的 Gateway 事件循环阻塞项。
- [#119720](https://github.com/openclaw/openclaw/issues/119720) — 自 2026-08-05 起为 P1，25 条评论，无新修复 PR。长期存在的持久化/事件循环扩展问题。
- [#117262](https://github.com/openclaw/openclaw/issues/117262) — 自 2026-08-01 起为 P1，12 条评论。SQLite 写句柄争用导致约 33 秒停顿。
- [#121953](https://github.com/openclaw/openclaw/issues/121953) — 自 2026-08-11 起为 P1，14 条评论。DeepSeek cron 前缀停滞仍未解决。
- [#136311](https://github.com/openclaw/openclaw/issues/136311) — 自 2026-09-02 起为 P2，12 条评论。Memory 重建索引锁和孤立临时 DB 累积。
- [#161921](https://github.com/openclaw/openclaw/issues/161921) — 自 2026-09-30 起为 P0，11 条评论，无新修复 PR。更新/Doctor/插件死锁阻塞恢复。
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — 自 2026-10-13 起为 P0，22 条评论，无新修复 PR。Windows 重启/更新可靠性。
- [#7722](https://github.com/openclaw/openclaw/issues/7722) — 自 2026-02-03 起为 P2 安全增强，11 条评论，4 个 👍。文件系统沙箱配置需要产品/安全决策。
- [#13219](https://github.com/openclaw/openclaw/issues/13219) — 自 2026-02-10 起为 P2，9 条评论。用于成本跟踪的按模型使用量日志。
- [#52732](https://github.com/openclaw/openclaw/issues/52732) — 自 2026-03-23 起为 P2，7 条评论。按 agent 的 compaction/context-pruning 覆盖需要产品决策。

**PR 积压说明：** 样本中的大多数高影响力 PR 都是近期的。最明显的较旧 PR 是 [#156626](https://github.com/openclaw/openclaw/pull/156626)，这是一个仅测试的 relay 匹配重构，自 2026-09-23 起开放并等待作者；相对于 P0/P1 issue 积压，其产品风险较低。

---

---

## 横向生态对比

## 1. 生态系统概览

2026-10-28，个人 AI 助手/代理开源格局正围绕网关/运行时核心、多端客户端（桌面、Web、移动端、消息平台）以及 MCP/工具集成收敛。活动量很高但不均衡：OpenClaw 处于超大规模运营，Hermes Agent、QwenPaw 和 ZeroClaw 在 22–50 个 issue/PR 区间迭代，而 IronClaw 在本窗口期不活跃。发布工程正成为差异化因素——OpenClaw 发布了三个通道，QwenPaw 发布了一个 beta，ZeroClaw 发布了一个安全补丁，而 Hermes 没有发布。共同拖累不再是功能广度，而是生产环境加固：会话身份、异步运行时健康、供应商兼容性、MCP 可靠性和 principal 感知的安全。

## 2. 活动对比

*计数为 24 小时更新/触及数，并非仓库总数。健康度\* 是分析人员结合活跃度、发布节奏、稳定性/安全性和评审吞吐的启发式指标；它不是上游项目指标。*

| 项目 | Issue 数（24 小时） | PR 数（24 小时） | 发布状态 | 健康度\* |
|---|---:|---:|---|---|
| **OpenClaw** | 500 更新：391 开启/活跃，109 已关闭 | 500 更新：275 开启，225 已合并/关闭 | 3 个发布：beta、更新恢复稳定版、扩展稳定 LTS | **6.5/10** — 吞吐量高，P0 风险偏高 |
| **Hermes Agent** | 50 触及：47 开启，3 已关闭 | 50 触及：43 开启，7 已合并/关闭 | 无发布；用户参考 v0.22.5 稳定版 | **5.8/10** — 分诊量大，会话架构债务 |
| **IronClaw** | 无活动 | 无活动 | 无发布 | **N/A** — 窗口期不活跃 |
| **QwenPaw** | 36 触及：16 开启，20 已关闭 | 50 触及：39 开启，11 已合并/关闭 | v2.3.0-beta.1 | **7.0/10** — 响应及时，评审积压 |
| **ZeroClaw** | 22 更新：21 开启，1 已关闭 | 42 更新：41 开启，1 已合并/关闭 | v0.8.7 安全/维护版 | **6.7/10** — 发布纪律好，安全/评审瓶颈 |

## 3. OpenClaw 的定位

**相较同行的优势。** OpenClaw 以数量级优势领先规模：24 小时内触及 500 个 issue 和 500 个 PR，其中 225 个 PR 已合并/关闭——相比之下 Hermes 为 7，QwenPaw 为 11，ZeroClaw 为 1。它也是本组中唯一拥有三个活跃发布通道的项目，其中包括一个等同于 LTS 的扩展稳定通道。其 issue 互动深度为样本中最高：排名第一的 Gateway 事件循环 issue 有 58 条评论，而 Hermes 的头号条目为 24 条，ZeroClaw 为 16 条，QwenPaw 为 5 条。

**技术路线差异。** OpenClaw 是一个横向的、以 Gateway 为中心的平台：会话、插件、通道、Doctor/更新恢复和安全边界都集中在一个明显对事件循环阻塞敏感的运行时中。同行更聚焦、更纵向：Hermes 正在构建规范化的多端会话/协议层；QwenPaw 集中于桌面/控制台上下文、记忆、MCP 和供应商兼容性；ZeroClaw 以安全为先，采用 OIDC、principal 作用域以及 Rust/Tokio/Wasmtime 风格的技术栈。OpenClaw 的优势在于广度和运维工具；风险在于同一个中心化运行时集中了生产稳定性故障。

**社区规模对比。** OpenClaw 的吞吐量不仅更大——它处在不同的运营层级。它在 24 小时内关闭/合并了 225 个 PR，约为 Hermes、QwenPaw 和 ZeroClaw 可见合并吞吐量的 20–30 倍。这意味着大得多的贡献者/维护者基数和更多生产部署面，但也带来更难的分诊问题：前 50 个样本中至少出现九个开启的 P0，且若干头部 P0 缺少可见修复 PR。

## 4. 共同技术关注领域

| 共同需求 | 涉及项目 | 具体要求 |
|---|---|---|
| **会话持久化、恢复与身份** | OpenClaw、Hermes、QwenPaw、ZeroClaw | 跨重启和跨端可恢复会话；显式所有权，而非基于 `source` 的锁定；从对话记录/DB 损坏中恢复；仅追加的事件历史；会话加载/恢复 API。 |
| **异步运行时健康与背压** | OpenClaw、Hermes、QwenPaw、ZeroClaw | 非阻塞持久化和插件捕获；有界队列；SQLite 争用控制；网关停滞检测；按 profile 的线程/资源限制；MCP 连接恢复。 |
| **供应商/模型兼容性与成本感知回退** | OpenClaw、Hermes、QwenPaw、ZeroClaw | 配额感知的 429 回退；如 thinking-disable 请求形态等传输格式的特殊行为；OAuth/订阅登录；Codex 流式行为；模型目录漂移；类型化供应商错误。 |
| **MCP/工具协议与工具调用完整性** | OpenClaw、Hermes、QwenPaw、ZeroClaw | 旧版 MCP 协商回退；中毒会话恢复；每轮一致的工具目录；schema/参数校验；审批路径安全；principal 感知的工具选择器。 |
| **安全、治理与最小权限** | OpenClaw、Hermes、QwenPaw、ZeroClaw | 文件系统沙箱；审计日志完整性；OIDC/SSO；CSP；仅所有者可用的工具边界；审批允许列表加固；防止子代理/插件权限提升。 |
| **升级/安装可靠性与多端一致性** | OpenClaw、Hermes、QwenPaw、ZeroClaw | 自愈更新与回滚；Windows/Android 安装稳健性；保留故障原因的诊断信息；移动端键盘/输入；PWA/Linux 打包；无障碍模式。 |

IronClaw 在本窗口期没有数据，未纳入共同需求证据。

## 5. 差异化分析

| 项目 | 主要功能重点 | 目标用户 | 架构/路线 |
|---|---|---|---|
| **OpenClaw** | 广泛的网关/插件平台、多渠道运营、更新恢复、LTS | 运维人员、高级用户、生产级多渠道部署 | 以 Gateway 为中心的核心；插件/通道；Doctor/更新路径；扩展稳定发布通道；对事件循环敏感的运行时。 |
| **Hermes Agent** | 多端对话连续性与客户端协议 | 高级用户、远程 `hermes serve` 部署、第三方客户端构建者 | 规范化会话权威 + 网关；Desktop/Webapp/mobile/TUI 多端；profile 多路复用；当前正在偿还会话所有权债务。 |
| **QwenPaw** | 桌面/控制台代理工作区、上下文压缩、记忆、MCP/供应商广度、治理 | 需要 MCP、记忆、审计和供应商灵活性的桌面及企业用户 | 基于 Python 的控制台/桌面技术栈；技能/通道/插件；SQLite 历史；模型注册表；治理审计；Windows/Linux 打包。 |
| **ZeroClaw** | 安全优先的自托管网关/仪表盘、身份/访问、运行时加固 | 重视安全的自托管用户和企业风格部署 | Rust/Tokio/Wasmtime 技术栈；网关/仪表盘；OIDC 与 principal 作用域；仅追加会话历史；v0.9.0 聚焦运行时/网关。 |
| **IronClaw** | 窗口期内无可见活动 | 未知 | 根据所给数据未知。 |

OpenClaw 是横向平台型打法。Hermes 是多端连续性打法。QwenPaw 是桌面/上下文/供应商兼容性打法。ZeroClaw 是安全/身份打法。这种差异化是健康的，但四者都在收敛到相同的运维要求：重启安全的会话、异步运行时隔离、供应商抽象、MCP 稳健性和最小权限工具访问。

## 6. 社区动能与成熟度

**第一梯队——超大规模、发布工程化：** **OpenClaw**。活动量遥遥领先，24 小时内 225 个 PR 已合并/关闭，三次发布（包括一个等同 LTS 的版本）。它迭代迅速，但成熟度不均衡：强大的发布工程和社区复现能力，与多个 P0 稳定性问题以及头部阻塞项缺少可见修复 PR 并存。

**第二梯队——高速稳定器：** **QwenPaw**、**ZeroClaw** 和 **Hermes Agent**。
- **QwenPaw** 是样本中响应最快的修复者：关闭 20 个 issue，合并/关闭 11 个 PR，并有若干同日/近日的 issue→PR 配对。其约束是评审带宽，有 39 个开启 PR 和若干持续数周的 `size/XXXL` 工作。
- **ZeroClaw** 发布纪律强——v0.8.7 包含来自 19 位贡献者的 81 次提交——以及清晰的 v0.9.0 安全/身份主题。瓶颈是评审/安全分诊：41 个开启 PR 对 1 个已合并/关闭，且若干安全发现尚无修复 PR。
- **Hermes Agent** 分诊重而非合并重：47 个开启 issue，43 个开启 PR，7 个已合并/关闭，无发布。Webapp 合并后余震和会话所有权集群表明，它正在架构债务下趋于稳定，而不是交付净新增面。

**第三梯队——窗口期不活跃：** **IronClaw** 在过去 24 小时没有活动；无法根据本摘要判断动能或成熟度。

## 7. 趋势信号

| 行业趋势 | 来自社区反馈的证据 | 对 AI 代理开发者的价值 |
|---|---|---|
| **会话连续性正在成为产品本身** | Hermes 的头号 issue 是 Desktop/mobile/Webapp/TUI 上“列出但无法打开”的会话；OpenClaw 有重启/会话/DB 恢复工作；ZeroClaw 在重新加载时丢失提示词；QwenPaw 会话在切换供应商后损坏。 | 尽早设计会话身份、所有权和可恢复事件日志。将“跨端、跨重启的一段对话”视为核心契约，而非 UI 功能。 |
| **可靠性如今是功能，而非运维事后补充** | OpenClaw 事件循环阻塞、SQLite 停滞、更新/Doctor 死锁；Hermes 网关在负载下停滞；QwenPaw 压缩中止；ZeroClaw 中毒 MCP 会话。 | 从第一天起，将异步持久化、有界队列、健康检查、回滚路径和诊断保留构建进运行时。 |
| **供应商抽象必须基于能力，而非模型名称** | OpenClaw 配额感知回退；QwenPaw 禁用 thinking/传输格式怪癖；ZeroClaw Codex 流式与推理强度；Hermes ChatGPT 登录供应商。 | 实现类型化供应商能力、配额感知回退、OAuth/订阅认证，以及按供应商的错误归一化。 |
| **MCP/工具互操作性是集成战场** | QwenPaw 旧版 MCP 协商失败；ZeroClaw 中毒 MCP 会话和过期套接字；OpenClaw 工具目录缺口和 MCP 启动上限；Hermes 工具参数 schema 失败。 | 投资于 MCP 协商回退、会话恢复、schema 校验，以及跨轮次类型的一致工具发现。 |
| **安全正在左移到代理运行时** | ZeroClaw 的 OIDC/XFF/SOP/principal 作用域发现；OpenClaw 沙箱与所有者工具修复；Hermes 审批允许列表绕过；QwenPaw 审计日志完整性和工作区外写入。 | 采用 principal 感知的工具访问、审计轨迹、沙箱、CSP，以及面向插件、钩子、子代理和消息端的安全默认值。 |
| **多端一致性和无障碍正在成为基线期望** | Hermes 移动端键盘/视口问题；OpenClaw TUI 屏幕阅读器模式和 iOS 分享；QwenPaw Windows/Linux 打包；ZeroClaw Android shell 失败。 | 在客户端之间共享一套协议，但要为移动端输入、视口、无障碍、打包和平台特定生命周期行为预留预算。 |
| **维护者评审带宽是扩展约束** | QwenPaw 的 39 个开启 PR 和持续数周的 XXXL 工作；ZeroClaw 的 41 个开启 PR；Hermes 的 43:7 开启对已合并比例；OpenClaw 中缺少可见修复 PR 的 P0。 | 当项目达到中等规模后，模块化所有权、大型变更的设计文档门禁和分诊自动化，与贡献者增长同样重要。 |

**结论：** 生态系统正从“代理演示”走向“代理基础设施”。赢家将是那些让会话可移植、运行时非阻塞、供应商可互换、MCP 可恢复、权限可审计——同时不牺牲多端可用性或发布速度的项目。

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要
**日期：** 2026-10-28 · **仓库：** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日概览

Hermes Agent 正处于深度**稳定化阶段**：过去 24 小时内共有 100 个 issue 和 PR 被处理（50 个 issue——47 个开放 / 3 个已关闭；50 个 PR——43 个开放 / 7 个已合并或关闭），**无新版本发布**。活动绝大多数是*入站缺陷分诊*而非功能交付——开放与合并比（43:7）反映出维护者队列的积压速度快于消化速度。最主要的信号是**浏览器托管 Webapp（#141864，2026-10-25 合并）合并后的余震**，目前已有专门的跟踪 issue（[#144648](https://github.com/NousResearch/hermes-agent/issues/144648)）和多个子 PR 在进行中。贯穿整份报告的第二个更大的结构性主题是：**会话身份、归属和跨界面恢复在多个不同层面上出现故障**，这表明问题出在规范会话权威的架构缺口，而非一组孤立缺陷。整体健康度读作*高速度但承压*——分诊纪律良好（严重性标签、组件标签、`sweeper:risk-*` 标记），但 P2 积压不断增长，相应的修复 PR 却寥寥无几。

---

## 2. 发布

**无。** 过去 24 小时内无新版本发布，数据窗口内也没有 `Latest Releases` 条目。用户继续将当前版本线引用为 `v0.22.5 (stable, bf0203a794a0)` 和 `v0.21.5+6653`。今日无破坏性变更、迁移说明或弃用项需要报告。

---

## 3. 项目进展

过去 24 小时内有 7 个 PR 被合并或关闭；可见子集如下：

| PR | 状态 | 影响 |
|---|---|---|
| [#145770](https://github.com/NousResearch/hermes-agent/pull/145770) | 已关闭 | **Webapp 插件安装解除阻塞**——`hermes webapp` 中插件目录/Git 检查失败，报错*"Plugin inspection is unavailable in this environment"*；现已经由网关路由。影响所有 agent 插件。 |
| [#145753](https://github.com/NousResearch/hermes-agent/pull/145753) | 已关闭 | **安装器竞态修复**——等待被回收的安装状态锁的启动器现在会在活动文件而非已删除文件上重试；防止两个安装器在同一状态下运行。为 #145527 的后续修复。 |
| [#145772](https://github.com/NousResearch/hermes-agent/pull/145772) | 已关闭 | **CI 红灯清除**——Windows arm64 失败由 home 重扫描重新导入托管提供商插件引起。 |
| [#86329](https://github.com/NousResearch/hermes-agent/pull/86329) | 已关闭（重复） | `error_classifier.py` 中的上游提供商 403 检测；已被取代。 |
| [#86612](https://github.com/NousResearch/hermes-agent/pull/86612) | 已关闭（过期） | Mistral、Cohere、DeepInfra、SiliconFlow 的自动发现注册表映射；已在别处落地。 |

**值得关注的已关闭 issue：** [#143331](https://github.com/NousResearch/hermes-agent/issues/143331)（`hermes update` 在 Windows 11 上每次运行都失败，原因是陈旧的 `gateway.pid` + 被持有的锁）和 [#143561](https://github.com/NousResearch/hermes-agent/issues/143561)（网关 `/api/status`、tickets 和 `session.resume` 在负载 30–100 的主机上超时；报告者提供了补丁 gist）。两者都是现有用户面临的高摩擦运维阻塞。

**净评估：** 当日的交付产出是*解除阻塞和清理*，而非功能交付。可见的五个关闭项中有三个是基础设施/CI 卫生类。

---

## 4. 社区热点话题

**1. [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — 24 条评论，👍2 · 远程后端桌面会话无法恢复** *（P2，`sweeper:risk-session-state`）*
Hermes Desktop 针对远程 `hermes serve` 后端创建的对话以 `source='desktop'` 持久化，但一旦托管进程重启，`session.resume` 返回 `not_found`。这是窗口期内讨论最多的单项，也是下文会话身份集群的核心。

**2. [#144648](https://github.com/NousResearch/hermes-agent/issues/144648) — 10 条评论 · 跟踪：Webapp 合并后后续工作** *（type/security，P2）*
维护者拥有的 #141864 总括 issue。明确将 Webapp 定位为跨浏览器标签页、CLI、原生 Desktop 和消息共享一个网关——"每个界面看到的是同一段对话"。子工作已可见：[#145788](https://github.com/NousResearch/hermes-agent/pull/145788)（移动端标签页选择器菜单）、[#145573](https://github.com/NousResearch/hermes-agent/pull/145573)（Webapp 页面的 CSP，WA-11）、[#145803](https://github.com/NousResearch/hermes-agent/pull/145803)（Android `insertText`）。

**3. [#112028](https://github.com/NousResearch/hermes-agent/issues/112028) — 8 条评论，👍1 · 多界面访问同一会话** *（P2，`needs-decision`）*
服务器拒绝第二个界面（移动端仪表盘）写入已在 Desktop 上打开的会话。这是对多界面需求最古老、最明确的表述。

**4. [#144814](https://github.com/NousResearch/hermes-agent/issues/144814) — 7 条评论，👍1 · hermes-mobile 会话永远无法从原生 Desktop 恢复**
对话记录在侧边栏中渲染，但打开时失败，报错 `JsonRpcGatewayError: not_found`。同一根因，不同客户端配对。

**5. [#141854](https://github.com/NousResearch/hermes-agent/issues/141854) — 6 条评论 · 消息页面永远显示"Needs setup"**
配置文件作用域的 `_platform_enablement` 要求必填环境变量，因此无令牌平台（WhatsApp、webhook、A2A）始终报告 `configured: false`。

**底层需求分析：** 五个最热条目中有四个描述的是*同一个用户期望*——**一段对话，可在任何设备或界面上访问，归属同一身份**。当前实现将 `source`/`login` 视为归属戳记，因此由 `desktop`、`hermes-mobile`、`ios` 或仪表盘 basic-auth 登录写入的会话在其他地方可见但不可打开。用户不将其理解为权限模型，而是数据丢失。相关：[#145074](https://github.com/NousResearch/hermes-agent/issues/145074)（"一个人，多个登录"——聊天记录被分割到不可见的用户中）和 [#143831](https://github.com/NousResearch/hermes-agent/issues/143831)（0.22.4 回归永久孤立遗留自定义 `source` 标签）。这是看板上杠杆率最高的架构决策。

---

## 5. 缺陷与稳定性

按严重性和影响范围排序。**注意：以下大多数 P2 没有关联的修复 PR。**

### 严重 / 数据完整性或静默失败类
| Issue | 严重性 | 摘要 | 修复 PR？ |
|---|---|---|---|
| [#145155](https://github.com/NousResearch/hermes-agent/issues/145155) | P2 | **cron `context_from` 注入数天前的运行结果**——归档帧差一错误使 `_archive_answer()` 返回 `None`；某任务消费了一份 7 天前的 `(FAILED)` 报告，却以为是最新的。最糟糕的失败类型：静默且自信。 | ❌ |
| [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) | P2 | 远程后端桌面会话在 `serve` 重启后永久无法恢复（`sweeper:risk-session-state`）。 | ❌ |
| [#131421](https://github.com/NousResearch/hermes-agent/issues/131421) | P2 | Desktop：发送 + 立即切换会话会**静默取消发送**；文本弹回输入框，无错误或提示（`sweeper:risk-session-state`）。 | ❌ |
| [#143907](https://github.com/NousResearch/hermes-agent/issues/143907) | P2 | Desktop 聊天无法从终端 TUI 恢复——登录戳记行 → `not_found`，绑定戳记行 → `permission_denied`；**TUI 随后静默启动新会话**而非报错。 | ❌ |
| [#145022](https://github.com/NousResearch/hermes-agent/issues/145022) | P2 | 看板工作器在网关卡顿时崩溃：*"Kanban execution unknown; retained by owner"*；一次卡顿杀死 4 个工作器，进行中的工作丢失。 | ❌ |

### 高 — 影响广泛界面的功能破损
| Issue | 严重性 | 摘要 | 修复 PR？ |
|---|---|---|---|
| [#145063](https://github.com/NousResearch/hermes-agent/issues/145063) | P2 | **工具调用参数丢弃字段 / 选错动作**，针对 `anyOf` 分支或可选载荷参数（在 deepseek-v4.1-flash、OpenRouter 上复现）。Agent 正确性类。 | ❌ |
| [#143256](https://github.com/NousResearch/hermes-agent/issues/143256) | P2 | 每个多路复用配置文件**一个 5 秒监管线程，即使零房间**——耗尽读池，饿死事件循环。 | ❌ |
| [#145761](https://github.com/NousResearch/hermes-agent/issues/145761) | P2 | `gateway_state.json` 报告 `session_store: unavailable` 长达数小时，而所有 28 个配置文件数据库均可证明可读。 | ❌ |
| [#143874](https://github.com/NousResearch/hermes-agent/issues/143874) | P2 | Desktop **丢弃每次网关票据铸造失败的起因**——所有者死亡、超时和崩溃都读作 `Gateway ticket bootstrap failed`（归咎：`apps/desktop/electron/local-gateway.ts:654`）。 | ❌ |
| [#143831](https://github.com/NousResearch/hermes-agent/issues/143831) | P3 | 0.22.4 **回归**：遗留会话收养拒绝自定义 `source` 标签；先前可恢复的会话现在永久 `not_found`。 | ❌ |
| [#145474](https://github.com/NousResearch/hermes-agent/issues/145474) | P3 | iOS（`source='ios'`）会话无法被收养——重命名/固定/归档全部 `not_found`，尽管通过 `get_session` 能解析。 | ❌ |

### 中 — 新界面上的 UX / 正确性
| Issue | 严重性 | 摘要 | 修复 PR？ |
|---|---|---|---|
| [#145593](https://github.com/NousResearch/hermes-agent/issues/145593) | P2 | Webapp 聊天：长用户消息完全展开渲染；移动端约占 90% 视口，agent 回复被挤成一条缝。 | ❌ |
| [#145296](https://github.com/NousResearch/hermes-agent/issues/145296) | P2 | Android 上仪表盘聊天：软键盘输入被丢弃——`screenReaderMode: true` 使 xterm 忽略 `insertText`。 | ✅ [#145803](https://github.com/NousResearch/hermes-agent/pull/145803) |
| [#145183](https://github.com/NousResearch/hermes-agent/issues/145183) | P2 | Linux 桌面窗口是半透明的，但设置行被隐藏（`TRANSLUCENCY_SUPPORTED = false`）——**没有 UI 逃生通道**。 | ❌ |
| [#142811](https://github.com/NousResearch/hermes-agent/issues/142811) | P2 | `clarify` 工具的回答在提交后从可见对话记录中消失。 | ❌ |
| [#145472](https://github.com/NousResearch/hermes-agent/issues/145472) | P2 | Desktop 反应会持久化，但在规范网关路径上**从不通知 agent**。 | ❌ |
| [#145286](https://github.com/NousResearch/hermes-agent/issues/145286) | P2 | 规范 `session.create` 拒绝发送 `data.fields`；CLI 读取 `data.field`——策略拒绝被渲染为*"couldn't start the model connection"*。 | ❌ |
| [#141854](https://github.com/NousResearch/hermes-agent/issues/141854) | P2 | 消息页面对无令牌平台永远显示"Needs setup"。 | ❌ |
| [#30989](https://github.com/NousResearch/hermes-agent/issues/30989) | P2 | **长期存在（自 2026-05-23）：** BlueBubbles 文本+附件作为两个事件到达，打断进行中的回合——没有像 Telegram 那样的防抖/合并。 | ✅ 部分：[#145795](https://github.com/NousResearch/hermes-agent/pull/145795) 相邻 |

### 安全
| Issue | 严重性 | 摘要 | 修复 PR？ |
|---|---|---|---|
| [#145763](https://github.com/NousResearch/hermes-agent/issues/145763) | **P3 安全** | `approval` 允许列表快捷方式将 `test -v` / `[[ -v …]]` 下标内的单引号 `$(…)`/反引号视为字面量，**但 bash 会重新展开它**。允许列表在危险模式检测器*之前*被查询（`tools/approval.py:1108`）。 | ❌ |
| [#144648](https://github.com/NousResearch/hermes-agent/issues/144648) | P2 安全 | 主页缺少 Webapp CSP（WA-11）。 | ✅ [#145573](https://github.com/NousResearch/hermes-agent/pull/145573) |

### 稳定性正面项
- [#19821](https://github.com/NousResearch/hermes-agent/issues/19821)（**自 2026-05-04**）——QQ Bot WebSocket 静默死亡；适配器在死连接上等待 18+ 小时。僵尸连接类：无关闭帧、无错误，只有无线电静默。
- [#145664](https://github.com/NousResearch/hermes-agent/issues/145664) — Telegram 令牌脱敏**误报**：遮蔽普通标识符内的数字后缀，损坏合法工具输出。

---

## 6. 功能请求与路线图信号

| 信号 | Issue/PR | 解读 |
|---|---|---|
| **多界面实时会话** | [#112028](https://github.com/NousResearch/hermes-agent/issues/112028)、[#136319](https://github.com/NousResearch/hermes-agent/issues/136319)、[#145074](https://github.com/NousResearch/hermes-agent/issues/145074) | 需求最高的路线图项。`needs-decision` 标签确认其未解决，而非被拒绝。 |
| **可安装 PWA 仪表盘** | [#145801](https://github.com/NousResearch/hermes-agent/pull/145801) | 添加 manifest + service worker + 192/512px 图标，使 Chrome 提供"Install app"。低风险、高可见性。 |
| **Desktop/web 构建的应用内浏览器** | [#145798](https://github.com/NousResearch/hermes-agent/pull/145798) | 浮动浏览器、起始页和地址栏，使 agent 提供的输出（开发服务器、HTML 报告）在应用内打开，而非踢到系统浏览器。强大的日常使用人机工程学。 |
| **本地运行时：LFM3 1.7B** | [#145800](https://github.com/NousResearch/hermes-agent/pull/145800) | 与已发布的 llama.cpp `b11940` + Desktop 引擎门控挂钩；扩展托管本地模型叙事。 |
| **"Sign in with ChatGPT" 提供商** | [#128926](https://github.com/NousResearch/hermes-agent/pull/128926) | 可选的 `openai-chatgpt` 提供商，使用公共客户端注册。带有 `sweeper:risk-security-boundary`——认证表面审查待定。 |
| **i18n 完成** | [#145804](https://github.com/NousResearch/hermes-agent/pull/145804) | 托管工作区快照遗漏 `locales/`，因此**每个用户都在聊天中看到原始键名**（`gateway.kanban.ping.completed`）。 |

**对下一版本的预测：** i18n 快照修复、PWA manifest、Android `insertText` 修复和 webapp CSP 都是小型、自包含且已发布的——是下一个补丁版本的强有力候选。多界面会话归属是更大的设计变更，不太可能作为修复落地；预计先有设计文档或 `needs-decision` 决议。

---

## 7. 用户反馈摘要

**数据中可见的满意度驱动因素：** 用户正在运行真正苛刻的工作负载——多配置文件网关（出现 3 和 28 个配置文件安装）、负载 30–100 的 macOS 主机、200+ 网关线程、cron 链、看板工作器集群、basic auth 后面的远程 `hermes serve`，以及基于协议构建的第三方移动客户端（Hy4ri 的 Android 客户端、iOS "Cadu" 客户端）。人们正在针对 Hermes 构建*客户端*这一事实是强烈的生态系统信号。

**不满意 / 痛点，按数量排序：**

1. **"我的对话消失了。"** 最主要的抱怨。会话被*列出*但*无法打开*，多份报告指出失败是静默的（TUI 启动新会话；Desktop 发送被取消且无提示）。即使用户行存在，用户也将其感知为数据丢失。
2. **不透明的失败消息。** [#143874](https://github.com/NousResearch/hermes-agent/issues/143874)（"每次票据失败都读作同一字符串"）、[#145286](https://github.com/NousResearch/hermes-agent/issues/145286)（策略拒绝被渲染为模型连接错误）和 [#143561](https://github.com/NousResearch/hermes-agent/issues/143561) 都描述了同样的挫败感：系统知道*为什么*失败，却在上一行将其丢弃。
3. **Windows 和负载主机上的运维脆弱性。** 陈旧的 `gateway.pid` 每次运行都破坏 `hermes update`，加上负载下的网关超时，今天都已关闭——但这正是驱使

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-10-28

## 1. 今日概览

QwenPaw 正处于高速维护阶段：过去 24 小时内涉及 36 个 issue，其中 **20 个已关闭 / 16 个仍开启**；50 个 PR 有活动，其中 **11 个已合并或关闭**，39 个仍开启。新的 beta 版本 **v2.3.0-beta.1** 已发布，包含控制台渲染和工具凭据修复。活动明显偏向**稳定性加固**——上下文压缩、provider wire 格式兼容性、MCP 协议协商和桌面打包——而非全新功能面。分诊响应很快（若干 2026-10-25/26 报告的 issue 已有候选修复 PR），但开放 PR 队列中聚集了一批等待审查、持续数周的 `size/XXXL` 工作。总体健康状况：**活跃且响应及时，但长期存活的 PR 积压正在增长**。

## 2. 发布

**v2.3.0-beta.1**（[release](https://github.com/agentscope-ai/QwenPaw/releases)）

可见的 changelog 条目（上游摘录已截断）：
- `fix(console)`：恢复交互式 HTML 预览 — [PR #8245](https://github.com/agentscope-ai/QwenPaw/pull/8245)
- `feat(tools)`：通过凭据存储提供可选的 Tavily API key（[#8174](https://github.com/agentscope-ai/QwenPaw/issues/8174)）— [PR #8204](https://github.com/agentscope-ai/QwenPaw/pull/8204)
- `fix(plugin)`：对齐修复（条目在源数据中截断）

**破坏性变更 / 迁移说明：** 可用摘录中未说明。作为 **2.3.0 系列上的 beta**，它应被视为预稳定版；Tavily 凭据存储改动与配置相邻，依赖仅通过环境变量配置 Tavily 的用户应验证行为。所给数据中的完整 changelog 已截断，因此上述内容并非穷尽。

## 3. 项目进展

**今日关闭/合并（可见的 11 个中的子集）：**
- [PR #8458](https://github.com/agentscope-ai/QwenPaw/pull/8458) — 桌面端 CSP 现在允许内嵌的 `data:` 字体（KaTeX 数学字形此前被 webview 拒绝）。
- [PR #8449](https://github.com/agentscope-ai/QwenPaw/pull/8449) — e2e 测试修复。

**值得注意的 issue 关闭，表明工作已发布或已解决：**
- [PR #8245 / #8204](https://github.com/agentscope-ai/QwenPaw/pull/8245) 已进入 v2.3.0-beta.1（控制台 HTML 预览、Tavily key）。
- **MCP 配置体验：** [#8405](https://github.com/agentscope-ai/QwenPaw/issues/8405)（`http`/`sse` 传输下模态框无法关闭）已关闭。
- **上下文/工具调用完整性：** [#5856](https://github.com/agentscope-ai/QwenPaw/issues/5856)（压缩期间 tool_call 结构丢失 → 400s）已关闭；[#2758](https://github.com/agentscope-ai/QwenPaw/issues/2758)（推理/工具输出被排到最终回复之后）已关闭。
- **Provider 韧性：** [#7935](https://github.com/agentscope-ai/QwenPaw/issues/7935)（LLM `Request timed out` 从不自动恢复）以 *Cannot Reproduce* 关闭。
- **生态请求已接受/关闭：** [#4124](https://github.com/agentscope-ai/QwenPaw/issues/4124) 和 [#1536](https://github.com/agentscope-ai/QwenPaw/issues/1536)（面向 OpenAI/Codex 的 OAuth 登录）、[#8199](https://github.com/agentscope-ai/QwenPaw/issues/8199)（Firecrawl 作为 `web_search` provider）、[#7079](https://github.com/agentscope-ai/QwenPaw/issues/7079)（PowerContext 长期记忆后端）。
- **安全相关：** [#7727](https://github.com/agentscope-ai/QwenPaw/issues/7727) — 工作区外写入硬阻止未识别 kimi-code 的 `Write` 工具路径字段；已关闭。
- **运行时稳定性：** [#8279](https://github.com/agentscope-ai/QwenPaw/issues/8279)（ReMe watcher 导致 AnyIO 线程限制器耗尽）、[#8290](https://github.com/agentscope-ai/QwenPaw/issues/8290)（媒体块永久卡住会话）、[#8185](https://github.com/agentscope-ai/QwenPaw/issues/8185)（Windows `desktop.log` 从不轮转）、[#7963](https://github.com/agentscope-ai/QwenPaw/issues/7963)（Langfuse 工具输出从未记录）。

## 4. 社区热点

| 条目 | 评论数 | 状态 | 链接 |
|---|---|---|---|
| #1536 面向 Codex 的 OAuth 登录 | 5 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/1536) |
| #8405 MCP 配置模态框无法关闭（http/sse） | 5 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/8405) |
| #8312 上下文控制失败导致任务中止 | 4 | **OPEN** | [issue](https://github.com/agentscope-ai/QwenPaw/issues/8312) |
| #7935 LLM 超时从不恢复 | 4 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/7935) |
| #5856 tool_call 结构在压缩中丢失 | 4 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/5856) |
| #2758 推理/工具输出显示在回复之后 | 4 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/2758) |
| #4124 面向 OpenAI/Codex 的 OAuth 登录（功能） | 4 | CLOSED | [issue](https://github.com/agentscope-ai/QwenPaw/issues/4124) |

**底层需求：**
- **基于订阅的认证，而不仅是 API key。** 重复的 OAuth/Codex 讨论串（#1536 提问 + #4124 功能请求）表明对 ChatGPT/Codex 套餐登录的需求——这是成本与访问问题，不是技术上的小便利。
- **长会话可靠性是头号摩擦点。** 每个高评论 bug 集群（#8312、#5856、#7935）都关乎会话劣化：压缩破坏 tool_call 结构、上下文溢出杀死任务、超时后需要重启进程。
- **MCP 互操作性成熟度。** #8405 是 UI 层面，但 #8047/#8424 系列是协议层面——仅支持旧版协议的 MCP 服务器协商失败，阻塞企业工具集成。
- **输出透明度。** #2758（显示顺序颠倒）反映出用户正在主动阅读 agent 轨迹，并希望推理出现在答案*之前*。

数据集中 PR 未显示评论数（`undefined`），因此下文的优先级依据范围和审查状态，而非反应数。

## 5. Bug 与稳定性

**严重**
- [#8312](https://github.com/agentscope-ai/QwenPaw/issues/issues/8312) *(OPEN)* — 上下文压缩无法容纳当前请求（252,565 > 251,904 tokens），任务被**直接中止**。直接阻塞长时间运行的 agent 工作。尚未看到修复 PR。
- [#8412](https://github.com/agentscope-ai/QwenPaw/issues/8412) *(OPEN)* — `governance/audit.db` 尽管设置了 `audit_level: all`，仍会静默**停止按 agent 记录工具调用**，且不会恢复。企业部署面临审计追踪完整性风险。
- [#7966](https://github.com/agentscope-ai/QwenPaw/issues/7966) *(OPEN)* — 切换 provider 后，历史记录中的 `file://` 媒体 URL 被以 `invalid_parameter_error` 拒绝，**永久破坏会话**。

**高**
- [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417) *(OPEN)* — 在火山引擎 `glm-5.3-flash` 上，`/compact` 总是以 400 失败：兼容层同时发送两种 thinking-disable 形态，其中一种被拒绝。**修复 PR：[#8436](https://github.com/agentscope-ai/QwenPaw/pull/8436)**（遵循声明的 thinking-disable wire 格式）。
- [#8361](https://github.com/agentscope-ai/QwenPaw/issues/8361) *(OPEN)* — 开启 Automatic Memory Search 后，`memory_search` 在同一轮内重复；守卫阻止第 4 次调用后，**不会产生最终回复**。**修复 PR：[#8457](https://github.com/agentscope-ai/QwenPaw/pull/8457)**。
- [#8424](https://github.com/agentscope-ai/QwenPaw/issues/8424) *(OPEN)* — `server/discover` 返回 HTTP 404 + JSON-RPC -32601 时被硬判为“现代服务器”，因此旧版 MCP 服务器永远不会回退到 `initialize`。与已关闭的 [#8047](https://github.com/agentscope-ai/QwenPaw/issues/8047) 同属一族；该模式反复出现。
- [#8413](https://github.com/agentscope-ai/QwenPaw/issues/8413) *(OPEN)* — 自终止防护误报：任何包含 `kill` + `$pid` 的命令都会被阻止，包括用户启动的进程。
- [#8440](https://github.com/agentscope-ai/QwenPaw/issues/8440) *(OPEN)* — `cancellable_wait()` 在外部取消后仍让所属工具任务继续运行（在 `2.2.3b1` / `main` 上复现）。

**中**
- [#8265](https://github.com/agentscope-ai/QwenPaw/issues/8265) *(OPEN)* — 在发行版修补过的 Python 3.11 上，`importlib.invalidate_caches()` 抛错会导致插件热安装中止；阻塞第三方插件作者。
- [#8406](https://github.com/agentscope-ai/QwenPaw/issues/8406) *(OPEN)* — QQ 渠道 `enabled` 标志会静默重置为 false，且无警告。
- [#8306](https://github.com/agentscope-ai/QwenPaw/issues/8306) *(OPEN)* — 原子写入器为临时 basename 追加 22 字节，导致合法的长文件名被拒绝。
- [#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415) *(CLOSED)* — `edit_file` 将 CRLF→LF 转换，并写回有损的 ISO-8859-1 输出。**修复 PR：[#8433](https://github.com/agentscope-ai/QwenPaw/pull/8433)**（保留行尾和编码）。
- [#8425](https://github.com/agentscope-ai/QwenPaw/issues/8425) *(CLOSED)* — 邮件监控构造出 `name@domain@domain` → `AUTHENTICATIONFAILED`；这是升级后破坏既有配置的**迁移/回归问题**。

**今日修复/关闭（回归观察）：** 媒体卡死家族（[#8290](https://github.com/agentscope-ai/QwenPaw/issues/8290)）现在已出现*第四个*变体（#8171、#7015、#7024）——基于措辞的分类器持续漏掉新的 provider 拒绝字符串，这是一种结构性脆弱点，值得做显式修复。

## 6. 功能请求与路线图信号

**今日新增**
- [#8459](https://github.com/agentscope-ai/QwenPaw/issues/8459) *(OPEN)* — 控制台缺少便捷访问**未读推送消息**的方式。实现成本低，UX 价值高。

**进行中（PR）**
- [#8434](https://github.com/agentscope-ai/QwenPaw/pull/8434) — Models 页面中的**快速添加模型**按钮（[#4036](https://github.com/agentscope-ai/QwenPaw/issues/4036)）：将跨 3 个页面的 5 次交互压缩为一次。
- [#8444](https://github.com/agentscope-ai/QwenPaw/pull/8444) — **Linux `.deb` + AppImage** 桌面打包。
- [#8443](https://github.com/agentscope-ai/QwenPaw/pull/8443) — 按 N 天淘汰 `tool_result` 块：在一个生产工作区中，`blocks` JSON 占 **976 MB `history.db` 的约 75%**。
- [#7785](https://github.com/agentscope-ai/QwenPaw/pull/7785) — 支持打断和模型选择的**实时语音聊天**。
- [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) — 嵌套（分类）技能目录，`<root>/<category>/<skill>/SKILL.md`。
- [#8187](https://github.com/agentscope-ai/QwenPaw/pull/8187) — 将渠道抽离为插件，并具备**子进程隔离**。
- [#7903](https://github.com/agentscope-ai/QwenPaw/pull/7903) — 内嵌社区信息流 + 收件箱，使用 PKCE 平台认证。
- [#8447](https://github.com/agentscope-ai/QwenPaw/pull/8447) — 刷新非 OpenAI 模型目录和 provider 配置。
- [#8453](https://github.com/agentscope-ai/QwenPaw/pull/8453) — `sessionWebhook` 过期（errcode 300001）时回退到钉钉 Open API。

**对 2.3.0 系列的预测：** 稳定版最有可能吸收控制台修复、`#8436`（thinking-disable wire）、`#8457`（将记忆召回作为参考上下文）、`#8433`（edit_file 编码）、`#8453`（钉钉回退）、`#8434`（快速添加模型）和 `#8455`（Windows 安装器诊断）。语音聊天、渠道插件隔离、社区/收件箱和 Linux 桌面范围更大，可能面向 2.4+。OAuth/Codex 登录（#4124/#1536）现已在两个入口上都关闭——强烈表明它已排队或已落地。

## 7. 用户反馈摘要

**痛点，按频率排列：**
1. **长会话会坏。** 压缩（#8312、#8417、#5856）、会话卡死（#7966、#8290）和无法恢复的超时（#7935）占主导。用户反复指出这些**并非新问题**——在 2.2.x 及更早版本中就有报告。
2. **Provider 兼容性脆弱。** 严格兼容的端点会拒绝重复或无法识别的参数（#8417、#8436）；provider 切换会使历史记录失效（#7966）；模型目录漂移（#8447）。
3. **MCP 是集成战场。** UI（#8405）和协议协商（#8424、#8047）的失败方式都会静默地将服务器置为 `inactive`，并伴随 Console 503。
4. **企业/治理缺口。** 审计日志静默失效（#8412）和工作区外写入绕过（#7727）正是受监管用户无法容忍的故障。
5. **防护栏过度触发。** 自终止检测（#8413）会阻止合法的 `kill $PID`；长文件名会因原子写入的临时命名而被拒绝（#8306）。
6. **Windows 桌面仍是二等公民。** 日志轮转（#8185）、安装器诊断（#8455）、响应消失（#3281）和 CSP 字体阻止（#8458）都集中在这里。
7. **成本/访问摩擦。** OAuth/Codex 的一组讨论串是最清晰的“我想使用现有订阅”信号。

**满意度信号：** 快速周转可见——#8415 于 10-25 报告，修复 PR #8433 于 10-26 提交；#8361 → #8457；#8417 → #8436。报告者提交了包含 commit hash 的详细复现，表明用户群体参与度高且技术成熟。

**不满信号：** 同一批 bug 家族反复出现（媒体卡死、压缩 400、MCP 协商），说明只是点修复而非结构性加固；“升级破坏了我原本可用的配置”出现两次（#8425 邮件认证、#8406 QQ 标志重置）。

## 8. 积压观察

**需要维护者关注的开放 issue：**
- [#8312](https://github.com/agentscope-ai/QwenPaw/issues/8312) — 自 2026-10-18 开放，4 条评论，**没有关联的修复 PR**，且会中止任务。最高优先级的孤儿项。
- [#7966](https://github.com/agentscope-ai/QwenPaw/issues/7966) — 自 2026-09-24 开放，provider 切换后会话永久损坏；无 PR。
- [#8265](https://github.com/agentscope-ai/QwenPaw/issues/8265) — 自 2026-10-15 开放；在修补过的 Python 构建上阻塞插件作者（生态级影响）。
- [#8424](https://github.com/agentscope-ai/QwenPaw/issues/8424) — 开放，属于反复出现的 MCP 协商家族；值得做一个统一修复，而非按报告逐个打补丁。
- [#8306](https://github.com/agentscope-ai/QwenPaw/issues/8306) — 自 2026-10-18 开放，低成本的原子写入器修复，但无 PR。
- [#7935](https://github.com/agentscope-ai/QwenPaw/issues/7935) — 以 *Cannot Reproduce* 关闭，但报告者描述了整个进程范围的锁死；添加复现排查说明或防御性超时重置可防止再次发生。

**等待审查的长期 PR（范围 vs. 审查带宽）：**
- [#7874](https://github.com/agentscope-ai/QwenPaw/pull/7874) `size/XXXL` — PawApp SDK/控制平面重设计，自 2026-09-19 开放。
- [#7785](https://github.com/agentscope-ai/QwenPaw/pull/7785) `size/XXXL` — 实时语音聊天，自 2026-09-15 开放（可见集合中最古老的开放 PR）。
- [#7903](https://github.com/agentscope-ai/QwenPaw/pull/7903) `size/XXXL` `[wip]` — 社区/收件箱，自 2026-09-20 开放。
- [#8187](https://github.com/agentscope-ai/QwenPaw/pull/8187) `size/XXXL` — 带子进程隔离的渠道插件抽离，自 2026-10-12 开放。
- [#8034](https://github.com/agentscope-ai/QwenPaw/pull/8034) `size/L` — 按*请求*而非按文件限制内联媒体；与 #8290/#8171 媒体卡死家族直接相关，自 2026-09-29 开放。
- [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) `size/XL`，首次贡献者——嵌套技能目录，自 2026-10-11 开放。
- [#8210](https://github.com/agentscope-ai/QwenPaw/pull/8210) `size/S`，首次贡献者——Windows Creator 项目删除（`WinError 5`），自 2026-10-13 开放；规模小且风险低。

**建议：** 24 小时内 39 个开放 PR 对 11 个关闭 PR 的比例，加上四个持续数周的 `XXXL` PR，说明当前瓶颈是审查带宽，而不是贡献者供给。优先分诊 `size/S`–`size/M` 的首次贡献者 PR（#8210、#8454、#8178），并为 MCP 协商和媒体分类器家族做统一修复，将带来最高的每小时健康收益。

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-10-28

## 1. 今日概览

ZeroClaw 仍保持高度活跃：**过去 24 小时内有 22 个 issue 和 42 个 PR 更新**，同时发布了 **v0.8.7 安全版本**。项目仅关闭了 **1 个 issue（#9101）** 和 **1 个 PR（#12019）**，而仍有 21 个 issue 和 41 个 PR 处于打开状态，这表明贡献量正在超过合并/评审吞吐能力。活动集中在安全与身份访问加固、v0.9.0 运行时/网关工作，以及 MCP、Android、Discord 和 OpenAI Codex 的可靠性修复。整体项目健康度在社区参与度和发布节奏方面表现强劲，但不断增长的 open PR 积压是主要运营约束。

*数据说明：PR 评论数提供为 `undefined`，因此无法可靠地对 PR 讨论活跃度进行排名。v0.8.7 变更日志摘录被截断。*

---

## 2. 发布

### v0.8.7 — v0.8.6 的安全与维护跟进版本
- **范围：** 来自 19 位贡献者的 81 个提交。
- **关键变更：**
  - 迁移到已修补的 **Wasmtime** 和 **Rustls 证书验证**。
  - 更新 **`tokio`** 和 **`ctutils`**，以应对影响 v0.8.6 的安全公告。
  - 重新构建容器镜像。
- **破坏性变更 / 迁移：** 提供的说明中未明确列出。这似乎是一个补丁级安全版本；鉴于已修复的安全公告，运维人员应尽快升级。
- **已跟踪的后续工作：** [#12076](https://github.com/zeroclaw-labs/zeroclaw/issues/12076) 中的翻译/目录修复，以及 [#12079](https://github.com/zeroclaw-labs/zeroclaw/pull/12079) 中的翻译版本固定。

---

## 3. 项目进展

### 今日已合并 / 已关闭
- **[PR #12019] chore(docs): pin translations to v0.8.7** — [链接](https://github.com/zeroclaw-labs/zeroclaw/pull/12019)。过去 24 小时内唯一合并/关闭的 PR；将文档目录 gitlink 移动到 v0.8.7 翻译标签。
- **[Issue #9101] 整合发布证明机制** — [链接](https://github.com/zeroclaw-labs/zeroclaw/issues/9101)。在 11 条评论后关闭；解决了冗余的 cosign、GitHub attestation 和 SLSA 签名路径导致发布资产膨胀到约 53 个的问题。

### 通过 open PR 推进的修复
- **Android shell 失败：** [#12080](https://github.com/zeroclaw-labs/zeroclaw/pull/12080) 在 `canonicalize("/system/bin/sh")` 遇到 `EPERM` 时增加回退处理，解决 [#12075](https://github.com/zeroclaw-labs/zeroclaw/issues/12075)。
- **OpenAI Codex 流式传输：** [#12073](https://github.com/zeroclaw-labs/zeroclaw/pull/12073) 将 Codex 流作为流重试，而不是使用 `stream: false`，解决 [#12072](https://github.com/zeroclaw-labs/zeroclaw/issues/12072)。
- **Prompt guard 误报：** [#12068](https://github.com/zeroclaw-labs/zeroclaw/pull/12068) 不再将单独的 `;` 或 `|` 视为命令注入，解决 [#12066](https://github.com/zeroclaw-labs/zeroclaw/issues/12066)。
- **Discord 审批：** [#12065](https://github.com/zeroclaw-labs/zeroclaw/pull/12065) 防止白名单中的对等机器人应答明文审批，解决 [#12064](https://github.com/zeroclaw-labs/zeroclaw/issues/12064)。
- **MCP 恢复：** [#12061](https://github.com/zeroclaw-labs/zeroclaw/pull/12061) 尝试恢复被污染的 MCP 会话，解决 [#10807](https://github.com/zeroclaw-labs/zeroclaw/issues/10807)。
- **ZeroCode 会话恢复：** [#12071](https://github.com/zeroclaw-labs/zeroclaw/pull/12071) 添加 `/load-session`，解决 [#12069](https://github.com/zeroclaw-labs/zeroclaw/issues/12069)。
- **运行时提示词排序：** [#12056](https://github.com/zeroclaw-labs/zeroclaw/pull/12056) 将身份部分保留在直接路径系统提示词的开头。
- **通道提示词上限：** [#12070](https://github.com/zeroclaw-labs/zeroclaw/pull/12070) 在每轮技能刷新后重新应用系统提示词上限。

### 进行中的较大功能
- [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921) — 仅追加的会话事件历史（XL，risk:high）。
- [#11414](https://github.com/zeroclaw-labs/zeroclaw/pull/11414) — Web 仪表板的可选聚焦工作区（XL，risk:high）。
- [#11880](https://github.com/zeroclaw-labs/zeroclaw/pull/11880) — 传输无关的 Goal 策略契约。
- [#11596](https://github.com/zeroclaw-labs/zeroclaw/pull/11596) — 原生 Claude Code 账户接入。
- [#11680](https://github.com/zeroclaw-labs/zeroclaw/pull/11680) — Firecrawl 网页搜索提供方。
- [#12059](https://github.com/zeroclaw-labs/zeroclaw/pull/12059) — OpenAI 的 runtime-profile 思考等级。

---

## 4. 社区热门话题

| 条目 | 评论数 | 为何重要 |
|---|---:|---|
| [#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432) — 运行时、仪表板登录与网关交付跟踪器 | 16 | 长期运行的 v0.9.0 跟踪器。表明需要在运行时/网关工作中协调发布分配并提供验收证据。 |
| [#9101](https://github.com/zeroclaw-labs/zeroclaw/issues/9101) — 整合发布证明机制 | 11 | 今日关闭。反映维护者/贡献者对三个并行签名机制带来的 CI 成本和发布复杂度的担忧。 |
| [#11517](https://github.com/zeroclaw-labs/zeroclaw/issues/11517) — Web 聊天刷新会丢失提示词 | 3 | 会话持久化在回合中途刷新时仍不可靠；直接影响仪表板可用性。 |
| [#10807](https://github.com/zeroclaw-labs/zeroclaw/issues/10807) — MCP 连接被永久污染 | 3 | 一次恢复尝试失败就会阻塞工作流；对重度 MCP 用户影响很大。 |
| [#11913](https://github.com/zeroclaw-labs/zeroclaw/issues/11913) — Telegram 命令菜单暴露给所有用户 | 2 | 安全/隐私问题：`setMyCommands` 缺少受信任用户范围限制。 |
| [#11961](https://github.com/zeroclaw-labs/zeroclaw/issues/11961) — 使用 OIDC 登录仪表板 | 2 | 对 Web 仪表板企业级 SSO 的身份/访问需求。 |
| [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047) — OIDC bearer 验证缺少限流器 | 2 | 出站令牌检查方面的安全加固缺口。 |

**潜在需求：** 最热门的讨论集中在（1）**发布工程简化**（#9101），（2）**协调 v0.9.0 运行时/网关规划**（#7432），以及（3）**安全/身份访问正确性**（#11913、#11961、#12047）。PR 评论数不可用，但诸如 [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921) 和 [#9745](https://github.com/zeroclaw-labs/zeroclaw/pull/9745) 这样的大型/高风险 PR 很可能是评审瓶颈。

---

## 5. Bug 与稳定性

### S1 — 工作流受阻
| Issue | 状态 / 修复 |
|---|---|
| [#10807](https://github.com/zeroclaw-labs/zeroclaw/issues/10807) — 一次恢复尝试失败导致 MCP 连接被永久污染（p1，risk:high） | 修复 PR [#12061](https://github.com/zeroclaw-labs/zeroclaw/pull/12061) 已打开。 |
| [#12075](https://github.com/zeroclaw-labs/zeroclaw/issues/12075) — Android shell 工具始终失败；`/system/bin/sh` 规范化 `EPERM` | 修复 PR [#12080](https://github.com/zeroclaw-labs/zeroclaw/pull/12080) 已打开。 |

### S2 — 行为降级
| Issue | 状态 / 修复 |
|---|---|
| [#11517](https://github.com/zeroclaw-labs/zeroclaw/issues/11517) — Web 聊天刷新会从屏幕和 `localStorage` 中丢失提示词 | 未列出修复 PR。 |
| [#12055](https://github.com/zeroclaw-labs/zeroclaw/issues/12055) — MCP HTTP 客户端没有连接池/keep-alive 控制；陈旧 socket 会丢失工具调用 | 未列出修复 PR。 |
| [#12072](https://github.com/zeroclaw-labs/zeroclaw/issues/12072) — Codex 流解码回退在非流式模式下重试并被拒绝 | 修复 PR [#12073](https://github.com/zeroclaw-labs/zeroclaw/pull/12073) 已打开。 |
| [#12064](https://github.com/zeroclaw-labs/zeroclaw/issues/12064) — Discord 明文审批可被白名单中的对等机器人应答 | 修复 PR [#12065](https://github.com/zeroclaw-labs/zeroclaw/pull/12065) 已打开。 |

### S3 — 次要
| Issue | 状态 / 修复 |
|---|---|
| [#12067](https://github.com/zeroclaw-labs/zeroclaw/issues/12067) — `untrusted_guard_sensitivity` 使 prompt guard 和泄漏检测器朝相反方向变化 | 未列出修复 PR。 |
| [#12066](https://github.com/zeroclaw-labs/zeroclaw/issues/12066) — Prompt guard 将任何分号或管道符标记为命令注入 | 修复 PR [#12068](https://github.com/zeroclaw-labs/zeroclaw/pull/12068) 已打开。 |

### 已报告 / 活跃的安全问题
- [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047) — OIDC bearer 验证没有尝试次数限制器或出站上限。
- [#12049](https://github.com/zeroclaw-labs/zeroclaw/issues/12049) — 四个远程地址即可耗尽浏览器登录流程存储。
- [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) — 网关限流器以第一个 `X-Forwarded-For` 条目为键，从而可伪造回环豁免。
- [#12051](https://github.com/zeroclaw-labs/zeroclaw/issues/12051) — `sop_workshop apply` 允许有范围限定的主体编写共享 SOP，而这些 SOP 之后会以操作员权限运行。
- [#12046](https://github.com/zeroclaw-labs/zeroclaw/issues/12046) — 拥有通配符工具选择器的非管理员主体仍保留不感知主体的工具。
- [#11913](https://github.com/zeroclaw-labs/zeroclaw/issues/11913) — Telegram 命令菜单全局暴露。

**评估：** 若干高严重度功能 bug 已有修复 PR，但**大多数安全问题尚未有链接的修复 PR**。同一报告者集中提交身份访问类发现，表明正在进行安全审计；这些问题应在 v0.9.0 之前优先处理。

---

## 6. 功能请求与路线图信号

- **[#11961](https://github.com/zeroclaw-labs/zeroclaw/issues/11961) — 使用 OIDC 登录仪表板**（标记为 `release:v0.9.0`）。是下一个次版本发布的有力候选，尤其是考虑到 #12047 中的相关安全工作。
- **[#12069](https://github.com/zeroclaw-labs/zeroclaw/issues/12069) — 面向 Code/ACP 会话的 `/load-session`。** 已有 PR [#12071](https://github.com/zeroclaw-labs/zeroclaw/pull/12071)；很可能在补丁版本或 v0.9.0 中落地。
- **[#10705](https://github.com/zeroclaw-labs/zeroclaw/issues/10705) — 为兼容的 OpenAI 模型支持 `max` 推理力度。** 提供方对等性请求；一旦传输/配置处理确定，就可能发布。
- **[#12077](https://github.com/zeroclaw-labs/zeroclaw/issues/12077) — 为 `fill-translations` 提供页面上下文并增加第二读者评审。** 面向文档本地化的流程/工具改进。
- **[#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432) — v0.9.0 运行时/仪表板/网关跟踪器。** 定义发布主题：运行时、登录和网关交付。

**预测：** v0.9.0 正围绕**身份/访问（OIDC、主体范围限定）**、**运行时/网关交付**、**会话历史**（[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)）以及**知识图谱范围限定**（[#9745](https://github.com/zeroclaw-labs/zeroclaw/pull/9745)）成形。Android shell 和 Codex 流式传输修复更可能作为 v0.8.x 补丁落地。

---

## 7. 用户反馈摘要

**过去 24 小时报告的痛点：**
- 由于 `canonicalize` 在 `/system/bin/sh` 上失败，**Android/Termux 的 shell 工具调用实际上已不可用**——对移动用户而言是硬性 S1 阻塞项。
- **真实环境频繁变动下的 MCP 可靠性**（容器重启、笔记本休眠、代理重载）较差；一次恢复失败就会污染会话，HTTP 连接池还可能静默丢失工具调用。
- **Web 仪表板在回合中途刷新时状态丢失**会削弱对会话持久化的信任。
- **安全态势担忧**占主导：Telegram 命令暴露、OIDC 验证限制、代理头伪造、SOP 权限提升以及通配符工具选择器，都指向主体感知访问控制的缺口。
- **本地化质量**：多个文档 issue（#12076、#12078）指出 `fr`、`ja`、`es` 和 `zh-CN` 目录中存在英文残留和错误翻译。
- **可用性摩擦**：没有 `/load-session` 时恢复 Code 会话很繁琐；部分 OpenAI 兼容模型缺少推理力度控制。

**满意信号：** 快速发布节奏、由 19 位贡献者参与的安全导向 v0.8.7，以及针对 Android、Codex、Discord 和 prompt-guard 问题的快速修复 PR，体现了响应能力。

**不满意信号：** 高严重度阻塞项仍未关闭，许多安全发现缺少修复 PR，41 个 open PR 的积压可能延迟用户可见的修复。

---

## 8. 积压事项观察

| 条目 | 存在时长 / 状态 | 为何需要关注 |
|---|---|---|
| [#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432) — v0.9.0 运行时/仪表板/网关跟踪器 | 创建于 2026-06-09；16 条评论；今日更新 | v0.9.0 核心跟踪器；需要排序和验收证据，以避免发布漂移。 |
| [#9745](https://github.com/zeroclaw-labs/zeroclaw/pull/9745) — 知识图谱的按 agent 归因/范围限定 | 创建于 2026-08-04；`needs-author-action`；p1；v0.9.0；XL | 与安全相关且受发布门控，但停滞于作者操作。 |
| [#10807](https://github.com/zeroclaw-labs/zeroclaw/issues/10807) — MCP 连接污染 | 创建于 2026-09-12；p1；S1 | 修复 PR #12061 已存在但需要评审；用户影响大。 |
| [#10705](https://github.com/zeroclaw-labs/zeroclaw/issues/10705) — 兼容 OpenAI 模型的 `max` 推理力度 | 创建于 2026-09-07；p2 | 提供方对等性请求，没有链接的 PR。 |
| [#11517](https://github.com/zeroclaw-labs/zeroclaw/issues/11517) — Web 聊天刷新会丢失提示词 | 创建于 2026-10-04；p2；S2 | 无修复 PR；影响默认 `session_persistence` 行为。 |
| [#11414](https://github.com/zeroclaw-labs/zeroclaw/pull/11414) — 聚焦工作区 | 创建于 2026-10-01；XL；risk:high | 大型用户体验变更，需要维护者评审。 |
| [#11596](https://github.com/zeroclaw-labs/zeroclaw/pull/11596) — 原生 Claude Code 账户 | 创建于 2026-10-07；`needs-maintainer-review`；risk:high | 战略性接入功能；评审瓶颈。 |
| [#11680](https://github.com/zeroclaw-labs/zeroclaw/pull/11680) — Firecrawl 网页搜索提供方 | 创建于 2026-10-12；`needs-maintainer-review`；XL | 外部提供方集成，等待评审。 |
| [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921) — 仅追加的会话事件历史 | 创建于 2026-10-21；XL；risk:high | 基础架构变更；在 v0.9.0 之前需要仔细评审。 |

**结论：** ZeroClaw 在活跃度和发布纪律方面是健康的，但大量 open PR 队列、多个未解决的安全问题以及若干 S1/S2 bug 共同意味着，**评审能力和安全分诊是下一个版本的关键路径**。

---

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*