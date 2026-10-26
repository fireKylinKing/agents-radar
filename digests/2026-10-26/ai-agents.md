# OpenClaw 生态日报 2026-10-26

> Issues: 358 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-10-26 00:37 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-10-26

## 1. 今日概览

OpenClaw 正处于高吞吐量的稳定化阶段：过去 24 小时内更新了 **358 个 issue** 和 **500 个 PR**，其中 **270 个 PR 已合并/关闭**、**72 个 issue 已关闭**，但**没有发布新版本**。活动量非常高，但主要信号由可靠性工作而非功能迭代速度主导——评论最多的前三个 issue 都是 Gateway 稳定性问题（SQLite WAL 增长、插件捕获阻塞、WAL checkpoint 被阻塞）。项目同时正在推进广泛性能优化专项（steipete 提交的一大批 `perf(...)` PR），并应对大量 Windows 特有的启动、升级和插件暂存回归。整体健康度可概括为**活跃但承压**：维护者吞吐量强，但多个未关闭 P0 以及反复出现的 2026.9.x/2026.10.x 回归表明，发布质量是主要风险。

## 2. 发布

**无。** 过去 24 小时没有新发布。鉴于已合并/关闭 PR 的数量以及标记为发布阻塞的问题（#143524、#153426、#166634 上的 `impact:ux-release-blocker`），这一点值得注意，意味着修复正在为未来的版本累积，而不是增量发布。

## 3. 项目进展

过去 24 小时**合并或关闭了 270 个 PR**（仍有 230 个开放）。评论最多的集合中可见的关闭与进展：

- **性能优化专项**（steipete）：一系列协同工作，降低各 agent、插件、Gateway、secrets 和 UI 的 CPU 占用与内存分配——例如 [#183968](https://github.com/openclaw/openclaw/pull/183968)（Code Mode 结果序列化）、[#183997](https://github.com/openclaw/openclaw/pull/183997)（Linux CPU 采样）、[#184004](https://github.com/openclaw/openclaw/pull/184004)（数组拷贝消除）、[#184001](https://github.com/openclaw/openclaw/pull/184001)、[#183990](https://github.com/openclaw/openclaw/pull/183990)、[#184002](https://github.com/openclaw/openclaw/pull/184002)、[#183991](https://github.com/openclaw/openclaw/pull/183991)、[#183988](https://github.com/openclaw/openclaw/pull/183988)。
- **已关闭的性能 PR：** [#183979](https://github.com/openclaw/openclaw/pull/183979)（冗余的 PR watcher 源检查）和 [#183977](https://github.com/openclaw/openclaw/pull/183977)（规范投递索引重建）已关闭；[#183354](https://github.com/openclaw/openclaw/pull/183354)（在备份报告中转义终端控制字符）也已关闭。
- **Control UI / 身份：** [#164825](https://github.com/openclaw/openclaw/pull/164825) 在设置期间增加配置文件命名（#162164 身份总项目的一部分）；[#163765](https://github.com/openclaw/openclaw/pull/163765) 推进 iOS 聊天中的个人 Tailscale 身份。
- **正在推进的定向修复：** [#183964](https://github.com/openclaw/openclaw/pull/183964)（Gateway 未应用配置时模型设置挂起）、[#184005](https://github.com/openclaw/openclaw/pull/184005)（静默后台 exec 后 Control UI 误报“空响应”）、[#183023](https://github.com/openclaw/openclaw/pull/183023)（CRLF 导致重复回复）、[#168510](https://github.com/openclaw/openclaw/pull/168510)（没有默认工作区的 `registerFull` 命令）、[#179781](https://github.com/openclaw/openclaw/pull/179781)（已启用的搜索插件未启动）、[#177272](https://github.com/openclaw/openclaw/pull/177272)（Matrix 线程会话播种）、[#162918](https://github.com/openclaw/openclaw/pull/162918)（`--` 之后的 PowerShell 补全）。
- **测试整合：** [#183985](https://github.com/openclaw/openclaw/pull/183985) 和 [#184006](https://github.com/openclaw/openclaw/pull/184006) 移除低价值/重复测试——这是在 PR 激增期间管理 CI 成本的迹象。
- **已关闭 issue：** [#170332](https://github.com/openclaw/openclaw/issues/170332)（实时状态数据库变更后 beta 更新仍在继续）和 [#83342](https://github.com/openclaw/openclaw/issues/83342)（运行时别名下重复的 `/models` 条目）均已关闭。

**注意事项：** 数据源将“merged/closed”报告为合计数量，因此无法根据这些数据确认确切的合并比例。

## 4. 社区热点

| 问题/PR | 评论数 | 信号 |
|---|---|---|
| [#143524](https://github.com/openclaw/openclaw/issues/143524) — SQLite WAL 增长到 1.4–2.8 GB，阻塞网关启动（Windows） | **136** | 主要痛点；P0，发布阻塞 |
| [#160959](https://github.com/openclaw/openclaw/issues/160959) — 网关在捕获大型外部插件时阻塞数分钟 | **54** | 2026.9.6 回归，P0 |
| [#164294](https://github.com/openclaw/openclaw/issues/164294) — 状态数据库 WAL checkpoint 永不完成（被读取器钉住） | **33** | 与 WAL 系列 bug 相关 |
| [#168955](https://github.com/openclaw/openclaw/issues/168955) — 启动耗时约 1030 秒，而 2026.10.1 约为 410 秒（Windows） | **19** | 升级回归，P1 |
| [#159596](https://github.com/openclaw/openclaw/issues/159596) — 内存锯齿；prepared-model-catalog worker 触及堆上限 | **16** | 每天约 200 次严重内存压力事件 |
| [#53408](https://github.com/openclaw/openclaw/issues/53408) — 长对话后 Write/exec 参数被静默丢弃 | **15** | 最早的高互动 bug（2026 年 3 月） |
| [#67413](https://github.com/openclaw/openclaw/issues/67413) — 按 agent 的 dreaming 配置 | **14** | 有真实产品需求的功能 |

**底层需求：** 评论分布高度集中在 **SQLite/WAL 生命周期正确性**（#143524 + #164294 + #166634 + #164427 + #164948 均共享 checkpoint/锁定根因）。用户在这里并不是要求新能力——他们需要网关能够启动、保持运行，并且在 Windows 和小型主机上不损坏或膨胀状态数据。第二类集中在 **启动/升级可靠性**（#160959、#168955、#182476、#183786），表明更新路径本身已成为信任问题。唯一长期存在的行为 bug #53408（工具参数静默丢失）仍是高焦虑项，因为它表现为静默失败，而不是崩溃。

## 5. Bug 与稳定性

按严重程度排序。数据表明存在修复 PR 的地方会注明。

### P0 — 严重
1. **[#143524](https://github.com/openclaw/openclaw/issues/143524)** — Agent SQLite WAL 无界增长（观察到 2.8 GB），在 Windows 上阻塞网关启动；`wal_checkpoint(TRUNCATE)` 只能暂时缓解。**无修复 PR**（`no-new-fix-pr`），需要维护者审查。发布阻塞。
2. **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — 网关事件循环在外部/工作区插件捕获期间阻塞数分钟（2026.9.6 回归）。有源码复现；**无修复 PR**。
3. **[#159596](https://github.com/openclaw/openclaw/issues/159596)** — 内存锯齿：prepared-model-catalog worker 增长到完整堆上限，每天约 200 次严重压力事件。**无修复 PR**。
4. **[#153426](https://github.com/openclaw/openclaw/issues/153426)** — `MEMORY.md`/`USER.md` 被 provenance ratchet 静默且永久排除在 bootstrap 注入之外；没有诊断或恢复路径。标记为 security-review + release-blocker；**无修复 PR**。
5. **[#166634](https://github.com/openclaw/openclaw/issues/166634)** — 延迟回收提交 checkpoint 失败导致 agent 在崩溃恢复后永久处于 `UNAVAILABLE`。发布阻塞；需要复现。

### P1 — 高
6. **[#168955](https://github.com/openclaw/openclaw/issues/168955)** — Windows 启动回归：约 1030 秒 vs 约 410 秒；`sidecars.control-ui-assets` 阻塞 477 秒。无修复 PR。
7. **[#182476](https://github.com/openclaw/openclaw/issues/182476)** — Windows `update repair` 因 `OPENCLAW_STATE_LEASE_LOST` 失败；就绪状态不稳定。需要信息。
8. **[#134993](https://github.com/openclaw/openclaw/issues/134993)** — 2026.8.1 之后，在大型 skill/agent 集群下，网关在文件系统发现中占满一个 CPU 核。需要信息。
9. **[#167876](https://github.com/openclaw/openclaw/issues/167876)** — Talk 咨询在会话建立后丢失调用方权限；已标记 security-review。
10. **[#148292](https://github.com/openclaw/openclaw/issues/148292)** — 会话自动压缩：仅尝试一次、无超时、与主模型耦合 → 挂起 23 分钟。
11. **[#164427](https://github.com/openclaw/openclaw/issues/164427)** — 单次 `cron.history-maintenance` 扫描持有状态数据库写锁 9.35 秒，耗尽 5 秒 busy timeout 并中止 sidecar 启动。
12. **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — 原生主机 exec 遇到共享状态完整性准入错误（2026.10.5 上 28+ 次失败）。
13. **[#181078](https://github.com/openclaw/openclaw/issues/181078)** — Agent 轮次在回复后失败，报错“Canonical session reader continuation is no longer current”（2026.10.5 Docker）。
14. **[#183786](https://github.com/openclaw/openclaw/issues/183786)** — Windows beta 升级反复查询已停止的前台 Gateway PID；升级停滞超过 15 分钟。
15. **[#168456](https://github.com/openclaw/openclaw/issues/168456)** — 模型回退在 cron `agentTurn` 的会话转录 turn-key 断言处失败。**修复形态明确/可入队**（`clawsweeper:fix-shape-clear`、`queueable-fix`）。
16. **[#126950](https://github.com/openclaw/openclaw/issues/126950)** — Telegram 不透明 `tgcb1:` 回调载荷被静默丢弃。**关联 PR 已开放。**
17. **[#149406](https://github.com/openclaw/openclaw/issues/149406)** — `sessions_spawn` 子会话遗漏所有第三方 MCP 服务器。
18. **[#142116](https://github.com/openclaw/openclaw/issues/142116)** — 重启恢复卡住 Telegram 通道：300 秒 claim→adoption 停滞，重新派发时没有回复载荷。
19. **[#144594](https://github.com/openclaw/openclaw/issues/144594)** — 会话间消息卡在 `interrupted`，没有重放或警告。
20. **[#89257](https://github.com/openclaw/openclaw/issues/89257)** — `backup create --verify` 以 13 退出，留下损坏的 `.tmp` 归档（data-loss 标签）。无修复 PR。

### P2 — 值得注意
- **[#164294](https://github.com/openclaw/openclaw/issues/164294)** — 状态数据库 WAL checkpoint 永不完成（进程内读取器钉住）。
- **[#162585](https://github.com/openclaw/openclaw/issues/162585)** — Windows 插件暂存将自身重新物化到 `node_modules`，从 290 MB 增长到 612 MB+ 且不收敛。
- **[#157986](https://github.com/openclaw/openclaw/issues/157986)** — 所有 `agentTurn` 自动化都因 `DataCloneError` 失败。**关联 PR 已开放。**
- **[#158127](https://github.com/openclaw/openclaw/issues/158127)** — 多 agent 运行时发布失败 + Codex 启动超时。
- **[#112313](https://github.com/openclaw/openclaw/issues/112313)** — 进入死信的外发队列条目是永久性的；没有 CLI/RPC/TTL 可以清除它们。
- **[#164948](https://github.com/openclaw/openclaw/issues/164948)** — `doctor --fix` 在状态数据库上自死锁（`busyTimeoutMs=0`）。
- **[#166137](https://github.com/openclaw/openclaw/issues/166137)** — 出口代理哨兵替换间歇性失效；过期/未替换的凭据到达上游（401s）。
- **[#53408](https://github.com/openclaw/openclaw/issues/53408)** — 15+ 轮后工具参数静默丢失。
- **[#165391](https://github.com/openclaw/openclaw/issues/165391)** — Control UI 会话数与 `sessions.list` 不一致。
- **[#166289](https://github.com/openclaw/openclaw/issues/166289)** — `model.usage` 双重触发（相隔 1 毫秒）。
- **[#164279](https://github.com/openclaw/openclaw/issues/164279)** — 后台体验审查完成率 0%（连续 50 次失败）。
- **[#138561](https://github.com/openclaw/openclaw/issues/138561)** — active-memory 插件在 2026.8.2 后停止召回，且没有日志信号。
- **[#162448](https://github.com/openclaw/openclaw/issues/162448)** — Linux 桌面窗口控件与侧边栏重叠。
- **[#155600](https://github.com/openclaw/openclaw/issues/155600)** — 选择评论渲染为原始附件文本。

**稳定性解读：** 这组 bug 异常集中——**SQLite WAL/checkpoint/写锁病理**和 **Windows 升级/启动路径**解释了大多数 P0/P1 项。修复覆盖较弱：大多数 P0 都标记为 `no-new-fix-pr`。

## 6. 功能请求与路线图信号

活跃功能问题及其可能性信号：

- **[#67413](https://github.com/openclaw/openclaw/issues/67413)** — 按 agent 的 dreaming 配置（14 条评论，5 👍）。防止同时进行工作区 dreaming/OOM。标记为 `needs-product-decision` → 不太可能进入紧接着的下一个版本。
- **[#101422](https://github.com/openclaw/openclaw/issues/101422)** — 可配置的记忆召回资格和索引排除路径（10 条评论）。`needs-product-decision`；与 #153426/#138561 的记忆工作高度契合，但未排期。
- **[#162164](https://github.com/openclaw/openclaw/issues/162164)** — 在 iOS/macOS 中选择加入的个人身份，同时保留 Shared owner。**正在积极推进**：[#164825](https://github.com/openclaw/openclaw/pull/164825)（配置文件命名）和 [#163765](https://github.com/openclaw/openclaw/pull/163765)（Tailscale 身份）已开放。#163765 带有 `merge-risk: compatibility` 和 `security-boundary` 标记——很可能分阶段推出，而不是一次性发布。
- **[#159858](https://github.com/openclaw/openclaw/issues/159858)** — 让空闲 worker isolate 释放内存（11–12 个 isolate 常驻约 650 MB）。与 #159596 一致；近期可能作为选择加入项。
- **[#55372](https://github.com/openclaw/openclaw/issues/55372)** — 用于禁用重启哨兵通知的配置选项。范围小，`needs-product-decision`，长期开放。
- **[#140244](https://github.com/openclaw/openclaw/issues/140244)** — 为 `openclaw infer model run` 增加 `--prompt-file`。低风险 CLI 增项；有可能。
- **[#130247](https://github.com/openclaw/openclaw/issues/130247)** — Cron `--until` / `--max-runs` 以及累计运行计数器。简单缺口；没有排期信号。
- **[#126727](https://github.com/openclaw/openclaw/issues/126727)** — 原生子 agent 的 provider 感知并发/背压。
- **[#165685](https://github.com/openclaw/openclaw/issues/165685)** — 在 registry-settled 的 yielded collectors 上提供机器可读的 `reason`。

**预测：** 下一个版本最可能以**稳定性和性能为主导**——WAL/checkpoint 修复、Windows 启动/升级加固，以及已合并的 `perf(...)` 分配/CPU 批次——而不是新功能。身份工作（#162164）是最可能逐步落地的*功能*，而记忆配置功能（#67413、#101422）仍因产品决策而受阻。

## 7. 用户反馈摘要

**报告的痛点：**
- **Windows 上的可靠性是最强烈的抱怨。** 启动时间从约 410 秒退化到约 1030 秒（#168955）、升级停滞（#183786）、修复失败并丢失租约（#182476），以及 WAL 膨胀阻塞启动（#143524）。Windows 主机似乎是最薄弱的平台。
- **静默失败比崩溃更令人担忧。** #53408（工具参数消失）、#153426（记忆文件被永久静默丢弃）、#112313（永久死信行）、#144594（消息卡住且无警告）、#166137（过期凭据）。用户反复强调“没有诊断信息，info 日志级别没有任何信号”。
- **小型/自托管硬件上的内存压力。** #159596（每天约 200 次严重事件）、#159858（空闲 worker 堆约 650 MB）、#164294 和 #164427（WAL/写锁争用）。用户在受限机器上运行 OpenClaw，并触达上限效应。
- **2026.9.x → 2026.10.x 之间的回归。** #160959、#181078、#168955、#159596、#158127、#162448 都明确标记为回归；用户正在跟踪确切的构建哈希和 A/B 基线耗时，表明这是成熟的生产使用受众。
- **积极信号：** 高质量复现（`source-repro` 标签）、详细环境报告，以及用户贡献定向修复 PR（#162918、#166352、#181534、#177272）。社区正在协作诊断，而不是反复空转。

**满意度解读：** 参与度高且技术性强，但对升级路径回归的耐心明显正在变薄——若干报告已持续数周（#143524 自 2026-09-09 起，已有 136 条评论）仍无修复 PR。

## 8. 积压关注

修复势头低或存在时间长、需要维护者关注的重要事项：

1. **[#53408](https://github.com/openclaw/openclaw/issues/53408)**（2026-03-24 打开，15 条评论，2 👍）——长对话后 `write`/`exec` 参数静默丢失。已有七个月，`needs-info`，无修复 PR。由于静默的数据/操作失败，用户影响高。
2. **[#89257](https://github.com/openclaw/openclaw/issues/89257)**（2026-06-01，P1，data-loss）——`backup create --verify` 留下损坏归档。有 `source-repro` 但没有修复 PR；备份完整性是信任关键路径。
3. **[#51245](https://github.com/openclaw/openclaw/issues/51245)**（2026-03-20，security）——Telegram 斜杠会话解析为 `channel=(unknown)`，破坏提权后的 `allowFrom`。关联 PR 已开放，但自 3 月以来未解决。
4. **[#55372](https://github.com/openclaw/openclaw/issues/55372)**（2026-03-26）——禁用重启哨兵通知。面小，`needs-product-decision`。
5. **[#67413](https://github.com/openclaw/openclaw/issues/67413)**（2026-04-15）——按 agent 的 dreaming 配置；5 👍，`needs-product-decision`。
6. **[#101422](https://github.com/openclaw/openclaw/issues/101422)**（2026-07-07）——记忆召回资格/排除路径；`needs-maintainer-review` + `needs-product-decision`。
7. **[#130247](https://github.com/openclaw/openclaw/issues/130247)** 和 **[#126727](https://github.com/openclaw/openclaw/issues/126727)**（8 月）——Cron 结束条件和 provider 背压；两者均为 `needs-product-decision`。
8. **[#143524](https://github.com/openclaw/openclaw/issues/143524)**——尽管有 136 条评论，仍为 `no-new-fix-pr` 且带有 `needs-maintainer-review`。**这是数据集中单一最高优先级的积压风险。**

**维护者负载信号：** `clawsweeper:needs-maintainer-review` 出现在前 50 个问题中的大多数上，而 `needs-product-decision` 在功能项中反复出现——当前瓶颈似乎是审查带宽，而不是问题发现。

---

## 横向生态对比

# 跨项目对比报告——个人 AI 助手 / Agent 开源生态
**统计窗口：** 2026-10-26 · **项目：** OpenClaw、Hermes Agent、IronClaw、QwenPaw、ZeroClaw  
**数据说明：** 计数为来自所提供摘要的 24 小时 GitHub 更新指标，而非绝对社区规模。健康分为分析师按 1–10 分制给出的判断，权重偏向合并吞吐量、发布就绪度、未关闭严重缺陷和审查负载。

---

## 1. 生态概览

个人 AI 助手 / Agent 开源生态正分化为大型通用网关平台与更小的专用运行时。五个项目在统计窗口内均未发布任何版本，活动主要由稳定化、安全加固和合并后遗留问题处理主导，而非功能交付。共同竞争前沿已从“新的 Agent 能力”转向**运营信任**：启动/更新可靠性、状态存储完整性、静默失败防护、多租户隔离，以及本地模型路由正确性。OpenClaw 仍是最大的活动中心，而 Hermes、ZeroClaw、QwenPaw 和 IronClaw 则占据更窄但日益贴近生产形态的细分领域。

---

## 2. 活跃度对比

| 项目 | Issue 更新数（24 小时） | PR 更新数（24 小时） | 发布状态 | 健康分 |
|---|---:|---:|---|---:|
| **OpenClaw** | **358** 个更新；72 个已关闭 | **500** 个更新；270 个合并/关闭；230 个未关闭 | 无；发布阻塞项持续累积 | **6.0/10**——吞吐量高，但承压 |
| **Hermes Agent** | **50**；48 个未关闭 / 2 个已关闭 | **50**；40 个未关闭 / 10 个合并-关闭 | 无；下一次发版可能侧重稳定性 | **6.5/10**——趋于稳定，安全类问题簇未解决 |
| **IronClaw** | **0** | **7**；5 个未关闭 / 2 个已关闭 / 0 个合并 | 无；仅维护窗口 | **6.5/10**——稳定但人工推进速度低 |
| **QwenPaw** | **6**；5 个未关闭 / 1 个已关闭 | **6**；6 个未关闭 / 0 个合并 | 无；报告中基线为 2.2.2.post1 | **6.0/10**——分诊响应及时，但受审查瓶颈限制 |
| **ZeroClaw** | **6**；1 个已关闭 | **50**；49 个未关闭 / 1 个合并-关闭 | 无；v0.9.0 范围正在组建 | **5.5/10**——高流动、低合并吞吐 |

**解读：** OpenClaw 在 Issue 和 PR 更新量上均比同类项目高出一个数量级。Hermes 是第二大活跃面，正处于有意的合并后稳定窗口。ZeroClaw 的 PR 流动量很高，但合并吞吐量非常低。QwenPaw 展现出及时的分诊响应，但审查队列停滞。IronClaw 由自动化驱动，在面向用户的活动上基本沉寂。

---

## 3. OpenClaw 的定位

**相较同类项目的优势**
- **活跃度和合并吞吐量远超同类：** 24 小时内 500 个 PR 更新、270 个合并/关闭，而 Hermes 为 50/10，ZeroClaw 为 50/1，QwenPaw 为 6/0，IronClaw 为 7/0。
- **平台面最广：** 网关、SQLite 状态、插件捕获、Control UI、身份、记忆、iOS/Tailscale、Windows 启动/升级，以及一场协同推进的性能专项。
- **最强的社区规模信号：** 仅 SQLite WAL 这个 Issue 就有 136 条评论；项目展现出高质量复现、source-repro 标签和贡献者修复 PR。
- **核心参考地位：** 同类项目已在从 OpenClaw 及姊妹项目回移修复，进一步巩固其作为参考实现而非小众工具的地位。

**技术路线差异**
- OpenClaw 是**以网关为中心的通用平台**，具有较重的 SQLite/WAL 状态层、插件捕获、Control UI、身份，以及按 Agent 配置的记忆/dreaming 配置。
- Hermes 更接近**统一本地网关 + 桌面端/Web 应用 + profile 多路复用器**。
- ZeroClaw 强调**多渠道部署、SOP 范围化自动化和渠道插件**。
- QwenPaw 强调**工具层治理、审计日志和提供商兼容性**。
- IronClaw 是**Rust/WASM 沙箱与 CI Agent 自动化**技术栈。

**社区规模对比**
- 按活跃度代理指标，OpenClaw 是最大的社区：Issue 更新量约为 Hermes 的 7 倍，PR 更新量约为 10 倍。
- Hermes 拥有数据集中最高的单线程参与度——其网关跟踪帖有 220 条评论——表明其用户群信任度高且生产使用密集。
- ZeroClaw 显示出持续的贡献者兴趣，但审查延迟。
- QwenPaw 拥有规模更小、技术能力很强的运维者社区，他们会在提交 Issue 前阅读源码。
- IronClaw 的 GitHub 跟踪器实际上很沉寂，因此无法从 Issue 活动推断社区规模。

**注意：** OpenClaw 的规模也是其主要风险。多个 P0 问题——SQLite WAL 增长、插件捕获阻塞、内存锯齿、记忆文件被静默排除——仍没有修复 PR，且 Windows 启动/升级回归反复出现。它在广度和吞吐量上领先，但未必在发布质量上领先。

---

## 4. 共同技术重点领域

| 重点领域 | 体现需求的项目 | 具体需求 |
|---|---|---|
| **网关/启动/更新可靠性** | OpenClaw、Hermes、ZeroClaw | OpenClaw：WAL/checkpoint 阻塞启动，Windows 升级卡顿。Hermes：统一网关合并后损坏，`hermes update` 风险，Windows 首次运行失败。ZeroClaw：fail-open 运行时配置与发布门禁 PR。 |
| **静默失败与可观测性** | OpenClaw、Hermes、QwenPaw、ZeroClaw | OpenClaw：工具参数被静默丢弃，记忆文件被排除且无诊断。Hermes：本地模型被静默路由到 OpenRouter，跨 profile 投递。QwenPaw：审计日志静默停止；有损文件编码却报告成功。ZeroClaw：未知记忆后端静默回退；`knowledge_bundles` 被忽略。 |
| **状态/数据完整性** | OpenClaw、Hermes、QwenPaw、ZeroClaw | SQLite WAL/checkpoint 与备份验证；大型 `state.db` 性能；审计 DB 持久性；CRLF/编码保留；记忆后端正确性。 |
| **多租户与安全边界** | Hermes、ZeroClaw、QwenPaw、OpenClaw | Hermes：跨 profile 机器人泄漏、跨安装服务重启、`.env` 受管目录覆盖。ZeroClaw：范围化日志访问、cron 输出泄漏、SOP 范围/工具名不匹配。QwenPaw：合规审计完整性。OpenClaw：带 security-review 标签的网关与身份问题。 |
| **本地/私有模型路由正确性** | Hermes、QwenPaw、OpenClaw | Hermes：自定义提供商静默穿透到 OpenRouter；基于别名的静默误路由。QwenPaw：提供商拒绝 `thinking` 参数并破坏 `/compact`。OpenClaw：模型设置/回退和会话转录断言。 |
| **平台一致性与上手体验** | OpenClaw、Hermes、QwenPaw | OpenClaw：Windows WAL、启动、升级、插件暂存。Hermes：Windows 首次运行安装、Linux 桌面端不透明、仅前端安装请求。QwenPaw：Windows 10 文件保真与桌面审批生命周期。 |
| **规模化性能** | OpenClaw、Hermes、ZeroClaw、QwenPaw | OpenClaw：内存锯齿、插件捕获阻塞、分配/CPU 性能专项。Hermes：在 4,583 个会话 / 5.4 GB 状态上重复扫描会话列表。ZeroClaw：JPEG 解码核算。QwenPaw：任务跟踪器清理。 |
| **插件/渠道隔离与供应链** | OpenClaw、Hermes、QwenPaw、ZeroClaw、IronClaw | 子进程/WASM 隔离、审批卡注入防护、图片 URL 溯源、依赖审计、fail-closed 技能扫描、插件暂存清理。 |

**跨领域模式：** 生态正收敛于 **fail-loud、fail-closed、租户隔离、保留溯源** 的 Agent 基础设施。静默降级如今被视为比崩溃更严重的缺陷。

---

## 5. 差异化分析

| 项目 | 功能重点 | 目标用户 | 架构 / 技术姿态 |
|---|---|---|---|
| **OpenClaw** | 通用 Agent 网关、Control UI、身份、记忆、插件、Windows 支持、性能 | 自托管高级用户、生产运维者、广泛平台集成者 | 单体网关 + SQLite 状态 + 插件捕获 + UI/身份层；平台面最广，但发布风险最高 |
| **Hermes Agent** | 统一网关、桌面端/Web 应用、profile、多机器人渠道、安全加固、本地模型路由 | 多 profile / 多渠道生产用户、远程 Agent 部署、注重隐私的本地模型用户 | 整合式本地网关、profile 多路复用器、浏览器 Web 应用，积极从姊妹项目回移修复 |
| **IronClaw** | Rust/WASM 沙箱、异步运行时、CI Agent 自动化、依赖卫生 | 需要沙箱化工具执行的基础设施团队 | 以 Rust 为中心、WASM 运行时依赖栈、自动化密集型维护；社区可见度低 |
| **QwenPaw** | 工具治理、审计/合规、Desktop/Feishu 渠道、提供商兼容性、渠道插件 | 合规敏感型运维者、真实仓库用户、第三方模型用户 | 基于 AgentScope 的工具层，带防护、审计 DB、渠道插件架构、提供商兼容垫片 |
| **ZeroClaw** | 多渠道助手部署、SOP 范围化自动化、记忆一致性、渠道成熟度 | 多渠道运营团队、插件/SOP 驱动型部署 | 渠道插件、记忆后端抽象、接收 capability 的运行时构造函数、堆叠的 v0.9.0 PR |

**关键区别：** OpenClaw 和 Hermes 在**网关集成广度**上竞争；ZeroClaw 和 QwenPaw 在**渠道/运维工作流与治理**上竞争；IronClaw 在**沙箱化运行时基础设施**上竞争。OpenClaw 的优势是生态覆盖面；其风险在于同样的广度会放大阻塞发布的回归。

---

## 6. 社区动能与成熟度

**活跃度分层**
- **第 1 层——高吞吐平台：** OpenClaw。Issue/PR 量和合并吞吐量都非常高，但存在 P0 稳定性和发布阻塞项。
- **第 2 层——积极稳定化：** Hermes。大量贴近生产的反馈、活跃补丁流水线，但存在安全类问题簇且尚无修复 PR。
- **第 3 层——高流动、低合并：** ZeroClaw。50 个 PR 更新但仅 1 个合并/关闭；堆叠 PR 链和审查带宽是硬约束。
- **第 4 层——分诊响应及时、受审查限制：** QwenPaw。同日关闭 bug 并提交修复 PR，但多个开放 PR 无维护者评论，且存在关键数据完整性缺陷。
- **第 5 层——仅自动化维护：** IronClaw。依赖卫生维护活跃；人工 Issue/功能活动实际上缺席。

**快速迭代**
- OpenClaw：性能专项加上 Windows/网关可靠性工作。
- Hermes：统一网关与 Webapp 加固、安全修复、跨项目移植。
- ZeroClaw：打上 v0.9.0 标签的运行时、渠道和安全 PR。
- QwenPaw：`/steer`、DeepSeek Search、渠道插件隔离。

**趋于稳定**
- OpenClaw 和 Hermes 都在吸收大型架构变更，处于稳定化阶段而非功能扩张模式。
- QwenPaw 正在围绕文件编码、审计日志和提供商兼容性稳定正确性。
- ZeroClaw 正在稳定渠道行为和记忆语义，但受流程约束。
- IronClaw 稳定但速度低。

**成熟度信号：** Hermes 明确从 OpenClaw/nanoclaw/opencode 移植修复，OpenClaw 的性能专项，QwenPaw 的源码级 bug 报告，以及 ZeroClaw 的发布门禁标签，都表明生态正在成熟。主要系统性瓶颈是**审查带宽**，而非 Issue 发现。

---

## 7. 趋势信号

1. **可靠性就是新功能。** 所有主要项目在新增能力之前，都会先接受启动、升级和状态完整性的评判。开发者应投入迁移/回滚路径、状态存储健康检查和 Windows 一致性。

2. **静默失败不可接受。** 在 OpenClaw、Hermes、QwenPaw 和 ZeroClaw 中，最具破坏性的报告涉及静默丢弃、静默重路由、静默审计丢失或虚假成功消息。fail-closed 行为和醒目诊断正成为基线要求。

3. **多租户的到来快于隔离设计。** Hermes 的 profile、ZeroClaw 的作用域 principal 和 QwenPaw 的审计边界表明，用户正把这些系统当作共享基础设施运行。必须显式设计按 profile、按安装、按 principal 的隔离。

4. **本地/私有模型路由正确性是信任问题。** Hermes 的自定义提供商穿透和 QwenPaw 的提供商参数失败表明，“OpenAI 兼容”并不够。Agent 开发者需要提供商身份、能力协商，并且不能静默回退到云端。

5. **记忆正在成为控制平面，而非存储。** OpenClaw 的 dreaming/recall/exclusion、ZeroClaw 的记忆一致性与外部 rerank，以及 Hermes 的文档链溯源，都指向对内容策展、相关性排序和可运维性的需求。

6. **规模化性能正成为差异化因素。** 大型状态数据库、会话列表重复扫描、插件捕获阻塞和内存上限如今都是常见的生产报告。轻量级自托管部署仍需要有界内存和可预测 CPU。

7. **插件/渠道隔离与供应链安全正在加固。** 子进程渠道隔离、WASM 运行时、审批卡注入防护、依赖审计和 fail-closed 技能扫描都是活跃工作流。

8. **生态收敛是真实存在的。** Hermes 正在从 OpenClaw 和姊妹项目回移修复。开发者应设计可移植的基础构件——状态完整性、诊断、租户隔离、路由溯源——而不是项目特定补丁。

**对技术决策者的底线结论：** OpenClaw 是生态的活动与平台面领导者，但承载着最高的发布质量风险。Hermes 是最强的第二平台，正在稳定一次大型网关/Web 应用整合。ZeroClaw 和 QwenPaw 是聚焦运维者的工具，拥有积极参与的用户，但审查队列受限。IronClaw 是稳定的基础设施细分领域，社区可见度低。在全部五个项目中，制胜路线图都是**运营信任优先，功能其次**。

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要
**日期：** 2026-10-26 · **仓库：** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日概览

活动仍然非常活跃——过去 24 小时内有 50 个 issue 和 50 个 PR 被触及——但活动形态是**持续稳定化，而非功能交付**：零发布，50 个更新 issue 中有 48 个仍开放，且两个参与度最高的讨论串都是跟踪合并后遗留问题的 tracking issue。主线仍是**统一网关**（[#106742](https://github.com/NousResearch/hermes-agent/pull/106742)，10 月 14 日合并），其最初的 P0 tracker [#134889](https://github.com/NousResearch/hermes-agent/issues/134889) 最终在 220 条评论后关闭，并立即由 [#143547](https://github.com/NousResearch/hermes-agent/issues/143547)（23 条评论且仍在增长）接续。**第二个大型功能面于 10 月 25 日落地**——浏览器托管的 Desktop “Webapp”（[#141864](https://github.com/NousResearch/hermes-agent/pull/141864)）——它已经有自己的 P2 tracking issue（[#144648](https://github.com/NousResearch/hermes-agent/issues/144648)）和一组后续 PR。健康信号：项目正在同时吸收两项架构变更，补丁管线健康，但 24 小时内 PR 流入速度约为合并速度的 4:1（40 个开放 vs. 10 个关闭），且开放 PR 队列中有相当一部分已积压 3–10 周。

| 指标 | 数值 | 解读 |
|---|---|---|
| 24 小时内更新的 Issue | 50（48 开放 / 2 关闭） | 高，且大多未解决 |
| 24 小时内更新的 PR | 50（40 开放 / 10 合并-关闭） | 流入高，积压增加 |
| 发布 | 0 | 合并后稳定窗口 |
| 主导组件标签 | `comp/gateway`、`comp/desktop`、`area/profiles`、`area/sessions` | 整合阵痛 |
| 今日标记安全项 | 8+ issues，4+ PRs | 积极加固 |

---

## 2. 发布

**无。** 2026-10-26 未发布新版本，快照中也不存在草稿发布说明。

供读者参考的背景：代码库已经领先于上一个 tag（多个 bug 报告引用的是 `v0.22.5`），而 10 月 14 日和 10 月 25 日的两次大型合并尚未纳入发布。鉴于正在推进的 P2 修复数量，下一次切版很可能主要是一个**稳定性发布**（见 §6）。

---

## 3. 项目进展

> **数据说明：**快照报告 24 小时内有 10 个 PR 合并/关闭，但只逐项列出了**开放** PR（列出的 20 个全部为 `[OPEN]`，且其评论数不可用——`Comments: undefined`）。因此无法逐项归因已合并集合；以下分析覆盖的是*管线中正在推进的工作*。

**合并后跟进工作正在收敛为可交付的修复。**

*Webapp（#141864）后续跟进*——[#144741](https://github.com/NousResearch/hermes-agent/pull/144741) 关闭了 #144704 lane 2 中的四个 “WA-5” 缺口：工作区文件和媒体现在会针对会话自身的 Docker 沙箱或 SSH 工作区解析，文件会保存到其预览读取的位置；该 PR 建立在 #144661 的 SSH-down 处理之上。[#144708](https://github.com/NousResearch/hermes-agent/pull/144708) 修复了 `/handoff` 聊天在 Desktop 和 Webapp 中跨 profile 类型重新打开的问题。两个相关缺陷被提交而非修复：登录退出传播失效（[#144668](https://github.com/NousResearch/hermes-agent/issues/144668)）和弹窗拦截器失败被静默吞掉（[#144673](https://github.com/NousResearch/hermes-agent/issues/144673)）。

*性能与启动*——[#144640](https://github.com/NousResearch/hermes-agent/pull/144640) 从 provider-plugin 加载和重启检查中移除了 git 版本遍历，将 `hermes sessions list` 从 1.45 s 降至 0.44 s（15 个 git 子进程 → 2 个）。[#144630](https://github.com/NousResearch/hermes-agent/pull/144630) 让 `sessions optimize-storage` 快了 18×（24 m 39 s → 1 m 25 s），并带有分阶段进度。

*Agent/工具健壮性*——[#144745](https://github.com/NousResearch/hermes-agent/pull/144745) 将工具调用中的退化空白流限制在 4 KiB，并快速重试；[#123900](https://github.com/NousResearch/hermes-agent/pull/123900) 在重复循环时重连流式工具调用，针对观察到的 7–10 分钟 “writing a file…” 冻结。

*安全加固*——[#144738](https://github.com/NousResearch/hermes-agent/pull/144738) 防止 agent 命令关闭审批卡的代码围栏并注入卡片文本；[#130023](https://github.com/NousResearch/hermes-agent/pull/130023) 将回复图片获取限制为具有工具来源的 URL（关闭一条数据外流通道）；[#86189](https://github.com/NousResearch/hermes-agent/pull/86189) 让后台技能写入扫描 fail-closed。

*跨项目对齐*——今日 PR 中有相当一部分明确是从姊妹项目**移植**而来（openclaw、nanoclaw、opencode），例如 #144745、#110440、#144740、#144739、#144738、#131039、#125848。这是成熟度信号：其他地方发现的修复正在被反向传播，而不是被重新发现。

---

## 4. 社区热点话题

| 排名 | 讨论串 | 参与度 | 潜在需求 |
|---|---|---|---|
| 1 | [#134889](https://github.com/NousResearch/hermes-agent/issues/134889) **[CLOSED]** — Tracking：一个网关拥有所有本地会话（P0） | 220 条评论，开放 18 天 | 单个事故讨论串成了网关迁移事实上的**发布说明频道**；用户需要一个地方确认“我的故障是否已知？” |
| 2 | [#143547](https://github.com/NousResearch/hermes-agent/issues/143547) — 统一网关开放后的合并后问题（P2） | 23 条评论 | 接续讨论串；表明 P0 tracker 是**行政性关闭，而非问题结束** |
| 3 | [#143379](https://github.com/NousResearch/hermes-agent/issues/143379) — RFC：文档链基底层网络（读取/治理/压缩） | 20 条评论 | 对持久、保留来源且能在上下文压缩后存活的记忆存在设计诉求 |
| 4 | [#38519](https://github.com/NousResearch/hermes-agent/issues/38519) — Desktop **仅前端安装**（Windows） | 19 条评论，**31 👍**（最高反应） | 远程 agent 拓扑：用户在服务器上运行 agent，并希望本地瘦客户端——不重复安装 |
| 5 | [#143426](https://github.com/NousResearch/hermes-agent/issues/143426) — Linux desktop 以 **6% 不透明度**启动，设置项被隐藏 | 17 条评论，11 👍 | Desktop 视觉回归完全阻塞 GNOME/Wayland 上的可用性；控制项不可发现 |

**分析。** 评论分布异常头重脚轻——一个讨论串（220）的评论数超过其后十九个之和。这种模式是*信任事件*的典型特征：当 P0 架构变更破坏大量工作流时，用户会集中到单个讨论串，因为碎片化让人感觉有风险。#134889 → #143547 的交接是结构上正确的答案，但 #143547 被标记为 `P2`，却承载 `area/auth`、`os/windows` 和 `desktop/connection`，这表明接续讨论串相对于其影响范围而言优先级偏低。

#38519 的反应数（31 👍，数据集中最高）是最清晰的单一未满足产品请求，且自 **June 3** 起一直开放——见 §8。

---

## 5. 缺陷与稳定性

按严重性排序，权衡 `P` 级别、影响范围和是否有修复在途。除注明外均为开放状态。

### 严重 / 影响最大

| 条目 | 严重性 | 详情 | 修复 PR？ |
|---|---|---|---|
| [#143547](https://github.com/NousResearch/hermes-agent/issues/143547) 统一网关合并后问题 | **P2，广泛** | 横切性：gateway、cron、auth、install-update、profiles、Windows、TUI-gateway、desktop connection。P0 tracker 关闭后影响仍在持续 | 部分——修复零散落地（#144708、#144741） |
| [#144437](https://github.com/NousResearch/hermes-agent/issues/144437) 裸 `custom` provider 未被修复 → **落入 OpenRouter 并返回 400** | **P2** | 持久化裸 `custom` 身份的会话会把 LAN/本地模型静默路由到 OpenRouter 默认端点。除功能损坏外，这还是**本应发往本地模型的提示词的潜在数据外流路径** | 无提交 |
| [#143364](https://github.com/NousResearch/hermes-agent/issues/143364) 次级 profile 的进度/通知通过**主 bot** 投递 | P3 但带安全标签 | 4-bot 多路复用网关上的跨 profile 消息泄漏——错误租户看到了另一个 profile 的流 | 无提交 |
| [#135200](https://github.com/NousResearch/hermes-agent/issues/135200) 用户 `.env` 可设置 `HERMES_MANAGED_DIR` 并重定向托管范围 | P3，`risk-security-boundary` | 文档写明仅限管理员且“绝不持久化到 `.env`”——执行层存在缺口 | 无提交 |
| [#144547](https://github.com/NousResearch/hermes-agent/issues/144547) 生命周期防护阻止 `gateway restart`，但**不阻止 `hermes update`** | P3，安全 | `hermes update` 会 drain、SIGKILL 并重新编码正在运行的 gateway——在受监管会话内部不受防护 | 无提交 |

### 高

- **[#143833](https://github.com/NousResearch/hermes-agent/issues/143833) — P2 性能回归。** 会话列表/计数会通过 `exclude_superseded_segments` → `hermes_state_local.local_lineage_index` 对每个请求重新扫描每条 lineage receipt（每次约 130 ms）。在 4,583 个会话 / 5.4 GB `state.db`、5 个客户端的情况下，观察到 3 个 CPU 核心被打满。随真实使用量扩展性很差。*无修复 PR。*
- **[#127105](https://github.com/NousResearch/hermes-agent/issues/127105) — P2 审计回归。** 后台进程通知可能作为用户消息投递到 Discord；先前的全面抑制提案（#127338）被关闭且未合并，因此修复需要更窄的设计。*无修复 PR。*
- **[#144117](https://github.com/NousResearch/hermes-agent/issues/144117) — P2。** Docker 后端后台进程仍在运行时被报告为已退出（`-1`）；完成状态丢失。*无修复 PR。*
- **[#121973](https://github.com/NousResearch/hermes-agent/issues/121973) — P2。** 在 opencode-go `deepseek-v4.1-flash` 上标题生成每次都 400：中继拒绝 `json_schema`，但返回不透明响应体，因此 `_is_structured_output_rejection` 永远匹配不上。*无修复 PR。*
- **[#131199](https://github.com/NousResearch/hermes-agent/issues/131199) — P2，`needs-repro`，Windows。** 首次运行的原生 Windows 安装在 Python 依赖阶段失败（“INSTALL DIDN'T FINISH”）。在该平台上完全阻塞新用户。*无修复 PR。*
- **[#144336](https://github.com/NousResearch/hermes-agent/issues/144336) — P2。** `hermes -p <profile> gateway restart` 跳过插件发现，因此新放置的用户平台插件覆盖会静默不生效。*无修复 PR。*
- **[#140881](https://github.com/NousResearch/hermes-agent/issues/140881) — P2。** 当别处存在 `model_aliases` 条目时，`/model <id> --provider <P>` 可能静默运行在**另一个** provider 上。静默误路由，不暴露错误。*无修复 PR（相关 identity 工作见 [#85861](https://github.com/NousResearch/hermes-agent/pull/85861)）。*
- **[#141275](https://github.com/NousResearch/hermes-agent/issues/141275) — P2。** 在非主安装上执行 `hermes update` 会重启**首个安装的**生产 dashboard（硬编码的账号全局 `hermes-dashboard.service`）。多安装主机会遭受跨租户干扰。*无修复 PR。*

### 中等 / 打磨

- [#143426](https://github.com/NousResearch/hermes-agent/issues/143426) — Linux Desktop 在 XWayland/GNOME 50 下以 6% 不透明度打开；Settings 控制项被隐藏。11 👍。*无修复 PR。*
- [#144728](https://github.com/NousResearch/hermes-agent/issues/144728) — 对活跃但尚未持久化的会话执行 `session.resume` 时缺少 `running`/`status`；Conduit 在重连后锁住 composer。
- [#144716](https://github.com/NousResearch/hermes-agent/issues/144716) — 多 profile 网关上 Bot Mode 出现 `profile_mismatch` toast + PATCH 重试循环。
- [#144694](https://github.com/NousResearch/hermes-agent/issues/144694) — 发往 bot-chat 的 `no_agent` cron 作业永远停留在 `delivery_queued`（settle 路径读取的是此类作业从未写入的 ledger）。
- [#144703](https://github.com/NousResearch/hermes-agent/issues/144703) — `profile delete` 警告 “Gateway is running — it will be stopped,” 但随后什么也没停止（多路复用器下没有按 profile 的 `gateway.pid`）。
- [#144711](https://github.com/NousResearch/hermes-agent/issues/144711) — `profile list` 警告共享 push 凭据，但 adapter 实际上按 profile 正常工作（误报）。
- [#135200](https://github.com/NousResearch/hermes-agent/issues/135200)、[#144735](https://github.com/NousResearch/hermes-agent/issues/144735)、[#144668](https://github.com/NousResearch/hermes-agent/issues/144668) — 见上表。
- [#144736](https://github.com/NousResearch/hermes-agent/issues/144736) — Desktop URL 解析器把尾随全角 CJK 标点吞进 href（i18n 正确性）。
- [#144675](https://github.com/NousResearch/hermes-agent/issues/144675) — Bot Mode 隐藏 toast 指向一个不存在的 “eye button.”
- [#138131](https://github.com/NousResearch/hermes-agent/issues/138131) — `hermes update` 在用户机器上仍应用 `exclude-newer`；`_trust_core_lock` 修复未覆盖此路径。

**评估。** 今日没有崩溃或数据丢失报告，且 P0 tracker 已关闭——这是真正的改善。但**带安全色彩的簇才是最令人担忧的信号**：四个独立 issue 涉及信任边界被跨越（跨 profile 消息投递、跨安装服务重启、未受防护的自更新、`.env` 覆盖管理员范围）。它们都没有关联修复 PR。`sweeper:risk-*` 标签（session-state、message-delivery、compatibility、security-boundary、platform-windows）表明自动 triage 正在捕获这些问题；缺口在于修复吞吐量。

---

## 6. 功能请求与路线图信号

| 请求 | 信号 | 下一版本可能性 |
|---|---|---|
| [#38519](https://github.com/NousResearch/hermes-agent/issues/38519) 仅前端 Desktop 安装（Windows） | **31 👍**，19 条评论，自 Jun 3 起开放，`needs-decision` | **下一次切版不太可能**——`needs-decision` 约 5 个月意味着存在架构阻塞；但 Webapp 工作（#141864）部分*解决了该需求*（指向远程 gateway 的浏览器客户端），可能化解它 |
| [#144574](https://github.com/NousResearch/hermes-agent/pull/144574) 通过 companion-app 前置条件引导插件安装 | PR 开放 | **可能**——UX 面小、作者活跃、填补 Desktop 安装器中的死胡同 |
| [#143346](https://github.com/NousResearch/hermes-agent/issues/143346) Kanban dispatcher：在卡住警告中报告 cap-hold 原因 | P3 | **有可能**——低风险日志改进，对操作者有明显价值 |
| [#143379](https://github.com/NousResearch/hermes-agent/issues/143379) RFC：doc-chain substrate（跨压缩的持久来源） | 20 条评论，`needs-decision` | **不会**——RFC 阶段；会作为后续发布落地，而非下个补丁 |

**下一次发布的预测。** 预计是一个 `v0.22.x` 系列稳定性切版，标题是*“统一网关 + Webapp 加固，”* 由以下内容构成：#144640（启动）、#144630（存储）、#144745 / #123900（流式挂起）、#144741 / #144708（Webapp 文件与 handoff）、#144738 / #130023（审批卡与图片 URL 安全）、#144739 / #144740（Google Chat、代码围栏拆分）。功能内容应接近零。Windows 首次运行（#131199）和 Linux 不透明度回归（#143426）最可能*错过*该切版，因为二者都没有修复 PR。

---

## 7. 用户反馈摘要

**用户满意之处。** 本数据集中没有称赞，但报告的*形态*表明真实的生产采用：用户引用具体版本戳（`v0.22.5+9-g67491b50`）、打包构建、5.4 GB 状态数据库、4,583 个会话、5 个并发客户端、一个多路复用器下的四个 Telegram bot 加 Discord。人们正在高强度地使用它。

**主要痛点（按用户自己的表述）：**

1. **统一器破坏了各功能面之间的接缝。** #134889/#143547 讨论串描述了 CLI、TUI、Desktop、API server、编辑器、消息 bot、cron、Kanban 和 Group Chats 都把轮次交给一个进程——然后失去各功能面的保证。具体表现：`/handoff` 聊天在错误客户端中复活（#144708）、活跃会话在重连后缺少 `status`（#144728）、Bot Mode `profile_mismatch` 循环（#144716）、`profile delete` 什么也不做（#144703）、插件覆盖被静默忽略（#144336）。反复出现的词是**“静默地”。**
2. **多租户正以设计未预料到的方式被使用。** 一个 profile 的进度通知泄漏到另一个 profile 的 bot（#143364），以及一个安装的 `update` 杀掉另一个安装的 dashboard（#141275）。用户正把 Hermes 当作共享基础设施。
3. **本地模型用户感觉自己被二等对待。** #144437（LAN 模型被静默推到 OpenRouter）和 #140881（alias 重写 model id，静默回退）是同一个抱怨：*路由器悄悄选择了一个与我要求不同的后端，而我只能从 400 中才发现。* 对隐私敏感的本地部署来说，这是数据集中最尖锐的信任问题。
4. **上手摩擦存在平台倾斜。** Windows 首次运行安装器失败（#131199）和已存在 145 天的仅前端请求（#38519）；Linux Desktop 在 6% 不透明度下不可用（#143426）。与此同时，macOS 用户报告更微妙的陈旧状态（#144735）。
5. **规模性能问题正在到来。** #143833 在 4,583 个会话的存储上每次调用重扫 130 ms，是这里首次出现的此类报告——随着用户群成熟，预计会更多。

**满意度解读。** 中性偏紧张。没有愤怒，没有弃用信号；用户在提交仔细、可复现、预先检查过的报告（其中几个明确列出了他们首先运行的搜索）。这是一个有投入的用户群在一次性吸收大量变动时的行为。

---

## 8. 积压关注

需要维护者关注的事项——按（存在时长 × 影响 × 缺少负责人）排序。

| 条目 | 存在时长 | 为何重要 |
|---|---|---|
| [#38519](https://github.com/NousResearch/hermes-agent/issues/38519) 仅前端安装 | **约 145 天**（Jun 3） | 数据集中最高反应数（31 👍）且标记 `needs-decision`。哪怕一个*“不做，改用 Webapp”*的答复也能解锁用户。目前无声。 |
| [#85861](https://github.com/NousResearch/hermes-agent/pull/85861) 在 URL 之前按 model 消歧 custom provider 身份 | **约 73 天**（Aug 14） | 直接与今日 P2 #144437 和 #140881 重叠——*同一*静默误路由 bug 类。一个停滞的 PR 正握着仍在被新报告的 bug 的修复。**队列中杠杆最高的合并候选。** |
| [#86189](https://github.com/NousResearch/hermes-agent/pull/86189) 后台技能写入扫描 fail-closed | **约 73 天**（Aug 14） | 安全 PR，标记 `needs-repro` + `risk-security-boundary`。一个 fail-closed 加固变更等两个月太久了。 |
| [#110440](https://github.com/NousResearch/hermes-agent/pull/110440) systemd 不可达时 Linux gateway 分离启动 | **约 42 天**（Sep 14） | 影响整个平台类的安装成功率；当前后果是安装中止并给出 D-Bus 修复文本。 |
| [#127784](https://github.com/NousResearch/hermes-agent/pull/127784) 每个 env creator 使用一个 sandbox target | **约 27 天**（Sep 29） | 按会话 Docker 隔离正确性（原始 host cwd 传给 `docker run -w`）。安全相关，P2。 |
| [#125848](https://github.com/NousResearch/hermes-agent/pull/125848) Egress 设置丢弃无法匹配的 `extra_allowed_hosts` | **约 28 天**（Sep 28） | 把静默 403（无诊断）变成具名警告。低成本、用户可见的胜利，`ci-reviewed`。 |
| [#130023](https://github.com/NousResearch/hermes-agent/pull/130023) 仅获取具有工具来源的回复图片 URL | **约 25 天**（Oct 1） | 关闭一条数据外流通道。安全 PR 不应该搁置一个月。 |
| [#135200](https://github.com/NousResearch/hermes-agent/issues/135200) `.env` 可重定向 `HERMES_MANAGED_DIR` | **约 18 天**（Oct 8） | 托管范围安全边界上的文档与执行不一致；无负责人。 |
| [#134889](https://github.com/NousResearch/hermes-agent/issues/134889) — *已关闭但值得关注* | Closed 10/25 | 220 条评论的宝贵机构知识。在讨论串变冷之前，确认未解决的子项已**迁移到 #143547**，而不是被丢弃。 |

**面向维护者的建议。** 队列的问题不是数量，而是*一致性*：三个独立条目（#85861、#140881、#144437）是同一个 provider 身份 bug，而第四个（#143547）是一个桶而不是一个 bug。闭环 provider 身份簇——合并 #85861，然后据此关闭 #140881/#144437——只需一次 review 就能移除今日数据中最尖锐的信任问题。

---

**数据说明与限制。** 源快照中 PR 的评论数不可用（`undefined`）；10 个合并/关闭的 PR 被计数但未逐项列出；issue/PR 正文被截断，因此上述摘要来自提供的摘录。严重性排名反映项目自身的 `P0`–`P3` 标签，并加权了我对影响范围的评估，而非维护者 triage。

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw 项目摘要 — 2026-10-26

*仓库：[nearai/ironclaw](https://github.com/nearai/ironclaw) · 报告窗口：过去 24 小时*

---

## 1. 今日概览

IronClaw 记录了一个**纯维护日**：未触及任何 issue、零发布，且全部 7 个有更新的 pull request 都是 Dependabot 依赖升级——该窗口内未出现功能、缺陷修复或文档类 PR。7 个 PR 中，**2 个已关闭、0 个已合并**，剩余 5 个仍处于打开状态，且全部是跨 Rust、GitHub Actions 和 WASM 工具链的自动化依赖更新。

活动量中等（7 次 PR 更新），但**完全不面向产品功能**，因此当天的工作没有推进任何用户可见的能力。已关闭的 PR（#8142、#8133）似乎已被 2026-10-25 新打开、范围更广的替代 PR（#8160、#8159）取代——这是一种健康但常规的 Dependabot 重新生成批次模式。

健康状况信号好坏参半：依赖卫生正在被积极维护，但 **issue 跟踪器完全没有任何活动**，以及一个 **已打开 64 天的 PR（#7834）**，都指向合并延迟，以及面向用户的互动渠道可能存在缺口。总体评估：**稳定、低活跃度、自动化驱动的维护**。

---

## 2. 发布

**报告窗口内没有新发布。** 项目仍停留在上一次已发布版本；今天没有变更日志、破坏性变更或迁移说明可报告。

---

## 3. 项目进展

今天没有功能推进，也没有修复交付——所有已关闭项都是未合并即关闭的依赖 PR。

| PR | 状态 | 范围 | 备注 |
|---|---|---|---|
| [#8142](https://github.com/nearai/ironclaw/pull/8142) | **CLOSED** | Rust "everything-else" 组，41 项更新 | 被 [#8160](https://github.com/nearai/ironclaw/pull/8160)（43 项更新）取代，打开于 2026-10-25 |
| [#8133](https://github.com/nearai/ironclaw/pull/8133) | **CLOSED** | GitHub Actions 组，10 项更新 | 被 [#8159](https://github.com/nearai/ironclaw/pull/8159)（11 项更新）取代，打开于 2026-10-25 |

**解读：** 未合并即关闭的模式是 Dependabot 在较新的变基批次取代过时批次时的预期行为。它代表的是*依赖债务整合*，而非功能进展。值得注意的是，替代 PR（#8160、#8159）仅约 1 天前打开，说明尽管人工合并吞吐量落后，机器人仍在跟上节奏。

---

## 4. 社区热点话题

**今天没有可衡量的热点话题。** 全部 7 个 PR 都显示 `Comments: undefined` 和 `👍: 0`，并且跟踪器中 **issue 数量为零**，因此不存在可用于排序的讨论、反应或情绪信号。

以*范围和影响半径*（而非互动量）作为代理指标，最值得关注的条目是：

- **[#8160](https://github.com/nearai/ironclaw/pull/8160)** — 43 个包的 Rust 升级（`thiserror` 2.0.20→2.0.21、`uuid` 1.24→1.28、`base64` 等）。当天影响面最广；回归风险最大。
- **[#7834](https://github.com/nearai/ironclaw/pull/7834)** — WASM 组（`wasmtime`、`wasmtime-wasi`、`wit-component`、`wit-parser`）；带有 `size: L` 和 `risk: medium` 标签。这是进行中后果最严重的更新，也是最难以直接判断其安全性的更新。
- **[#8159](https://github.com/nearai/ironclaw/pull/8159)** — 11 项 Actions 升级，包括 `anthropics/claude-code-action` 1.0.183→**1.0.259**；任何依赖基于 CI 的 agent 自动化的人都应关注。

**潜在需求：** 过去 24 小时内没有任何由人类编写的 PR 或 issue，这表明要么 (a) 项目将讨论引导至 Discord/Discussions 而非 GitHub Issues，要么 (b) 贡献者吞吐量目前集中在小型维护者群体中。值得确认是哪种情况，因为沉默的 issue 跟踪器会使社区需求不可见。

---

## 5. 缺陷与稳定性

**今天没有报告任何缺陷、崩溃或回归**（0 个 issue 更新）。由于没有提交缺陷报告，因此不存在修复 PR。

依赖队列中的潜在稳定性风险，按严重性排序：

| 严重性 | 条目 | 风险 |
|---|---|---|
| **中** | [#7834](https://github.com/nearai/ironclaw/pull/7834) — WASM 技术栈（`wasmtime`/`wit-*`） | 明确标记为 `risk: medium`、`size: L`。运行时/沙箱变更最可能引入行为回归，也最难仅靠 CI 验证。已打开 64 天。 |
| **低–中** | [#8160](https://github.com/nearai/ironclaw/pull/8160) — 一次性升级 43 个 Rust crate | 影响面大且未分批；单个不兼容的传递依赖升级就可能静默破坏工作区构建。 |
| **低** | [#8141](https://github.com/nearai/ironclaw/pull/8141) — tokio 生态（tokio 1.53.1→1.53.3、tokio-util、tokio-test） | 补丁级异步运行时更新；风险低，但若调度/时序回归漏过则会广泛影响。 |
| **低** | [#8140](https://github.com/nearai/ironclaw/pull/8140) — `serde_json` 1.0.151→1.0.154 | 序列化组；补丁级，被广泛依赖。 |
| **低** | [#8159](https://github.com/nearai/ironclaw/pull/8159) — Actions 组，包括近乎大版本的 `setup-node` 4.0.2→7.1.0 | 分组升级中出现大版本跳跃是 CI 流水线风险，而非产品风险。 |

**注：** #7834 的已打开时长（以及 #8140/#8141 已存在 8 天）表明，堆叠的依赖组可能因共享 CI 或评审带宽而彼此阻塞。陈旧的依赖 PR 是缓慢累积的稳定性与安全负债。

---

## 6. 功能请求与路线图信号

**没有提交任何功能请求**——issue 跟踪器完全没有记录到活动，因此没有可直接报告的用户需求信号。

从依赖流中间接推断路线图（推测性，但有数据依据）：

- **沙箱化/插件执行** — 反复出现的 `wasmtime`、`wasmtime-wasi`、`wit-component`、`wit-parser` 更新（[#7834](https://github.com/nearai/ironclaw/pull/7834)）表明 WASM 是关键运行时组件，很可能用于工具/插件隔离。
- **异步并发与 agent 吞吐量** — tokio 生态组（[#8141](https://github.com/nearai/ironclaw/pull/8141)）跟踪核心运行时。
- **agent 驱动的 CI 自动化** — `anthropics/claude-code-action` 在两代 PR（[#8159](https://github.com/nearai/ironclaw/pull/8159)、[#8133](https://github.com/nearai/ironclaw/pull/8133)）中从 1.0.183 跳到 1.0.259，表明项目自身工作流中正在积极使用编码 agent。

**对下一版本的预测：** 近期最可能合并的是 Rust 序列化/tokio 补丁集（#8140、#8141），其次是分组的 `everything-else` 升级（#8160）。鉴于其规模/风险标签和停滞时长，WASM 组（#7834）最不可能接下来落地。

---

## 7. 用户反馈总结

**本窗口内未捕获到任何直接用户反馈**——零 issue、零评论、零反应。这是真实的数据缺口，而非满意度证据。

在适当谨慎的前提下，可以推断：

- **没有明显不满：** 未出现崩溃报告、回归或投诉类 issue。
- **也没有明显欣喜：** 没有功能请求或讨论帖可用于衡量互动。
- **可能的使用场景信号**（仅来自依赖选择）：沙箱化工具执行（WASM 运行时）、高并发 agent 工作负载（tokio），以及 CI 集成的 agent 自动化（claude-code-action）。

**建议：** 如果项目打算让 GitHub 成为反馈渠道，那么空的跟踪器应被视为度量问题。确认 issue 是已禁用、被引导至别处，还是单纯无人使用——否则摘要将持续显示零用户声音。

---

## 8. Backlog 观察

需要维护者关注的条目，按停滞时长和后果排序：

1. **[#7834](https://github.com/nearai/ironclaw/pull/7834)** — WASM 组，4 项更新** · 打开于 2026-08-23，**已 64 天** · `size: L`、`risk: medium`、`scope: dependencies`
   这是最逾期的一项。长期存活的 WASM/运行时依赖 PR 会在每次变基时累积冲突风险，并使沙箱技术栈停留在较旧版本。**行动：** 安排专门的评审窗口，或拆分为按 crate 的 PR，以减少评审面。

2. **[#8141](https://github.com/nearai/ironclaw/pull/8141)** — tokio 生态，5 项更新** · 打开于 2026-10-18，**已 8 天**
   补丁级且低风险；应能快速落地，很可能只是被评审带宽阻塞。

3. **[#8140](https://github.com/nearai/ironclaw/pull/8140)** — `serde_json` 1.0.151→1.0.154** · 打开于 2026-10-18，**已 8 天**
   单包、低风险升级，滞留时间超出预期——适合立即合并的候选。

4. **[#8160](https://github.com/nearai/ironclaw/pull/8160)** 和 **[#8159](https://github.com/nearai/ironclaw/pull/8159)** · 打开于 2026-10-25，**已 1 天**
   两个已关闭 PR 的全新替代项；尚不算积压，但值得关注，以免它们继承与 #7834 相同的多周延迟。

**流程观察：** 同一窗口内同一依赖组出现两代 PR（#8133→#8159、#8142→#8160）是频繁变动信号。降低 Dependabot 分组频率，或为补丁级分组启用自动合并，将减少评审负担并缩短队列。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-10-26

**仓库：** [agentscope-ai/QwenPaw](https://github.com/agentscope-ai/QwenPaw)

---

## 1. 今日概览

QwenPaw 在过去 24 小时内**分诊活动中等，但集成吞吐为零**：6 个 issue 更新（5 个开启、1 个已关闭），6 个拉取请求更新（全部仍开启，**0 个已合并或关闭**），且没有新版本发布。一个 bug（#8416，桌面审批对话框死锁）在报告当天即被关闭，且针对 shell 自终止防护回归（#8413）同日开启了修复 PR（#8419）——两者都是响应式分诊的有力信号。然而，PR 队列现在完全停滞：最老的开启 PR（#8187）已 **14 天**，且没有收到维护者评论，所有六个开启 PR 都没有评审活动。Issue 构成以**正确性和数据完整性缺陷**为主——静默审计日志丢失、有损文件编码和 CRLF 规范化——而非外观问题，这表明用户群体正在成熟，将 QwenPaw 用于真实仓库和合规要求。整体健康度：**稳定但受评审约束**；bug 流入正在被确认，但尚未被吸收。

| 指标 | 值 |
|---|---|
| 过去 24 小时更新的 Issue | 6（5 个开启 / 1 个已关闭） |
| 过去 24 小时更新的 PR | 6（6 个开启 / 0 个已合并） |
| 新版本发布 | 0 |
| 最久未评审的 PR | #8187 — 14 天 |
| 有链接修复 PR 的 Issue | 1/6（#8413 → #8419） |

---

## 2. 发布

报告窗口内没有新版本发布。当前流传的最新用户报告基线是 **QwenPaw 2.2.2.post1**（pip 安装，site-packages），bug 报告也固定到源码修订 `27d22cd18d6e7fe610536ef1375cebf7aea89215`。

---

## 3. 项目进展

**今天没有 PR 被合并或关闭**，因此没有已交付功能。有所*推进*（已更新但未集成）的工作：

- **[PR #8419](https://github.com/agentscope-ai/QwenPaw/pull/8419)** `fix(tools): stop the shell self-kill guard from blocking kill $PID`（size/XS）——与其修复的 bug 同日开启，收窄 `_is_dangerous_self_kill`，使 `PID=$(pgrep mydaemon) && kill $PID` 不再被拒绝。**修复 [#8413](https://github.com/agentscope-ai/QwenPaw/issues/8413)。**
- **[PR #8268](https://github.com/agentscope-ai/QwenPaw/pull/8268)** `fix(task_tracker): bound the run completion callback and always clean up`（size/M）——处理 #8173 中运行清理的那一半：挂起/取消的完成回调会让 `TaskTracker._runs` 条目永远卡在 `running`。
- **[PR #8187](https://github.com/agentscope-ai/QwenPaw/pull/8187)** `feat(channels): extract channels into plugins with subprocess isolation`（size/XXXL）——架构性变更：通过 `channel.json` 发现渠道而无需导入平台 SDK，使用子进程执行以隔离渠道故障。
- **[PR #8318](https://github.com/agentscope-ai/QwenPaw/pull/8318)** `feat(agent): add /steer command for mid-execution guidance`（size/M）——**修复 #1775。**
- **[PR #8314](https://github.com/agentscope-ai/QwenPaw/pull/8314)** `feat(web_search): add DeepSeek Search provider`（size/M，首次贡献者）。
- **[PR #8418](https://github.com/agentscope-ai/QwenPaw/pull/8418)** `docs: note eligibility review for AgentScope Platform deployment`（size/XS，首次贡献者）。

**今日关闭：** [Issue #8416](https://github.com/agentscope-ai/QwenPaw/issues/8416) — 桌面审批对话框重试循环。

---

## 4. 社区热门话题

互动量很低且分布均匀（所有条目最多 2 条评论、0 个 👍），因此这里的“热门”指讨论密度最高，而非病毒式关注。

1. **[Issue #8413](https://github.com/agentscope-ai/QwenPaw/issues/8413)** — 2 条评论 — 自终止防护误报。
   *深层需求：* 用户将 QwenPaw 作为运行在在线主机上的操作员 agent。过度宽泛的安全启发式规则按子串（`kill` + `$pid`）模式匹配，破坏了最常见的守护进程重启惯用法。需求是**能力范围内的防护栏**（解析命令，而不是用正则），而不是移除防护。

2. **[Issue #8412](https://github.com/agentscope-ai/QwenPaw/issues/8412)** — 2 条评论 — `governance/audit.db` 尽管设置了 `audit_level: all`，却静默停止记录每个 agent 的工具调用。
   *深层需求：* 真实的合规/取证用途。失败模式是最糟糕的一种——**静默、单向、按 agent 发生**，工具调用（包括文件写入）继续但不被记录。用户需要审计完整性自检和显式失败，而不是尽力而为的写入。

3. **[Issue #8416](https://github.com/agentscope-ai/QwenPaw/issues/8416)** — 2 条评论，当天关闭 — 桌面审批对话框在发起它的轮次/会话被取消后无法关闭（飞书渠道会话，HIGH 工具防护规则）。
   *深层需求：* **远程渠道与桌面客户端之间的生命周期一致性。** 审批提示比其父轮次活得更久，变成无法关闭的模态债务。

4. **[PR #8419](https://github.com/agentscope-ai/QwenPaw/pull/8419)** — 针对 #8413 的链接修复，由回归贡献者（`suantea`，也是 #8318 的作者）在 24 小时内开启。健康的贡献者闭环：报告 → 修复 → 评审。

---

## 5. Bug 与稳定性

按严重程度排序（影响 × 影响范围 × 可逆性）：

| # | 严重程度 | Issue | 状态 | 修复 PR |
|---|---|---|---|---|
| 1 | **致命（静默数据丢失）** | [#8412](https://github.com/agentscope-ai/QwenPaw/issues/8412) — `audit_events` 永久停止记录每个 agent 的工具调用；在 agent 继续行动时，记录行也永不恢复 | 开启 | ❌ 无 |
| 2 | **高（数据损坏）** | [#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415) — `read_file_safe` 使用 `errors='ignore'` 解码，因此 ISO-8859-1 文件会丢失 `é/ü`；`edit_file` 随后将有损快照以 UTF-8 写回，并报告 "Successfully replaced text" | 开启 | ❌ 无 |
| 3 | **高（硬阻塞，特定提供商）** | [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417) — 每次 `/compact` 在火山引擎 AgentPlan `glm-5.3-flash` 上都返回 `400 BadRequest`：模型拒绝 `thinking.type=disabled`，而兼容层同时发出**两个**禁用思考参数 | 开启 | ❌ 无 |
| 4 | **高（阻断核心工作流）** | [#8413](https://github.com/agentscope-ai/QwenPaw/issues/8413) — 自终止防护拒绝 `kill $PID`，目标是无关的用户启动进程 | 开启 | ✅ [#8419](https://github.com/agentscope-ai/QwenPaw/pull/8419) |
| 5 | **中（仓库级扰动）** | [#8414](https://github.com/agentscope-ai/QwenPaw/issues/8414) — `edit_file` 将所有 `\r\n`/`\r` 规范化为 `\n`，并用 `newline='\n'` 重写整个文件，因此单行编辑会重排每一处行尾 | 开启 | ❌ 无 |
| 6 | **中（UI 死锁）** | [#8416](https://github.com/agentscope-ai/QwenPaw/issues/8416) — 桌面审批对话框重试已取消的请求且无法关闭 | **已关闭** | 不适用 |

**值得注意：** #8415 记录了一次*不忠实的成功报告*——破坏性写入外加一条“Successful”消息。这种组合（损坏 + 虚假确认）可以说是当前没有修复进行中的最高优先级事项。#8414 与 #8415 标题完全相同，但正文描述的是**两个不同缺陷**；应消除标题歧义，以保证搜索/重复检测可用。

**稳定性正面信号：** #8417 的故障被遏制——滚动管理器保留了旧摘要，且 `keep-context-after-compact-failure` 插件保留了会话上下文，因此**没有用户数据丢失**。错误遏制生效；端点能力协商则没有。

---

## 6. 功能请求与路线图信号

今天没有提交新的功能*请求*；路线图信号完全来自开启的 PR：

- **轮次中途引导（`/steer`）** — [PR #8318](https://github.com/agentscope-ai/QwenPaw/pull/8318) 关闭了一个已存在 3 周的 issue（#1775）。填补了一个真实的控制空白：用户目前必须在终止轮次（丢失进度）与任由无关工作累积之间做选择。**下一小版本发布的可能性很高**——范围小/中等，UX 叙事清晰。
- **提供商扩展：DeepSeek Search** — [PR #8314](https://github.com/agentscope-ai/QwenPaw/pull/8314)。将 `web_search` 从 2 个提供商（`tavily` 无密钥、`anysearch`）扩展到 3 个，新增无密钥提供商所缺乏的**发布日期**和**权威层级**元数据。这释放出路线图正走向*结构化、可引用级检索*而非原始结果列表的信号。
- **渠道即插件架构** — [PR #8187](https://github.com/agentscope-ai/QwenPaw/pull/8187)（size/XXXL）。减少宿主进程的 SDK 导入，并隔离渠道故障——与 #8416 这类 bug 直接相关（来自渠道的审批使桌面客户端不稳定）。这是杠杆率最高的路线图事项，但也是评审风险最高的。
- **提供商能力兼容层** — 由 [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417) 隐式要求。预计会有后续功能：为 `thinking` / reasoning-disable 标志提供按提供商划分的参数矩阵，而不是一刀切的兼容垫片。
- **部署诚实性** — [PR #8418](https://github.com/agentscope-ai/QwenPaw/pull/8418) 要求文档停止暗示“一键部署”，因为 AgentScope Platform 需要资格申请并经过 1–3 个工作日审核。

**预测下一版本内容：** 自终止防护修复（#8419）、任务跟踪器清理（#8268）、`/steer`（#8318）、DeepSeek Search（#8314）。鉴于 #8187 规模，可能会推迟到后续专门版本。

---

## 7. 用户反馈摘要

**报告者是谁：** 生产级操作员，而非随意测试者——pip 安装的 `2.2.2.post1`、Windows 10/AMD64、飞书渠道会话、火山引擎第三方模型端点、固定源码修订版本以进行源码级 bug 隔离。报告详细、可复现，并包含根因分析（`_is_dangerous_self_kill`、`read_file_safe`、`newline='\n'`），也就是说用户在**提交前阅读源码**。

**核心痛点：**

1. **过于激进的安全启发式规则**（#8413）——防护阻止自终止，却阻断了合法的守护进程管理。用户感觉 agent 在*质疑一位有能力的操作员*。
2. **静默、不可恢复的状态丢失**（#8412 审计记录；#8415 编码）——两者都静默且永久地失败。用户将合规日志和非 UTF-8 源码树托付给 QwenPaw；静默的单向退化比崩溃更快侵蚀这种信任。
3. **跨平台文件保真**（#8414 CRLF，#8415 ISO-8859-1）——工具层普遍假设 UTF-8/LF，这对 Windows 仓库和遗留代码库是错误的。这是*一类* bug，而不是一次事件。
4. **第三方模型兼容性**（#8417）——兼容 OpenAI 并不够；参数协商在更严格的端点上失败，并永久禁用 `/compact`。
5. **客户端生命周期 bug**（#8416）——来自已取消远程会话的无法关闭模态框。
6. **上手摩擦**（#8418）——“一键部署”与资格审核的现实不符。

**满意信号：** #8416 当天关闭；#8413 当天获得修复 PR；压缩失败可优雅降级，保留上下文并有显式回退插件。**不满意信号：** 贡献者 `LinklyLuck` 在一次测试会话中提交了**三个**不同缺陷（#8413、#8414、#8415）——这是来自活跃高级用户的集中负面信号，且其中两个没有正在进行的修复。

---

## 8. 积压事项观察

需要维护者关注的事项，按风险排序：

1. **[PR #8187](https://github.com/agentscope-ai/QwenPaw/pull/8187)** — `size/XXXL`，已开启 **14 天**，**零评论**。子进程隔离的渠道插件。大型架构 PR 若缺乏早期方向性反馈，衰减最快；要么评审，要么明确界定范围。⚠️ 最高优先级的评审债务。
2. **[Issue #8412](https://github.com/agentscope-ai/QwenPaw/issues/8412)** — 自 2026-10-24 开启，**无修复 PR**，静默合规日志失败且每个 agent 状态永久损坏。这是数据集中最严重的未关闭缺陷。
3. **[PR #8268](https://github.com/agentscope-ai/QwenPaw/pull/8268)** — 已开启 **10 天**，零评论。仅修复 #8173 的清理部分；挂起本身仍未解决。需要决定是否先落地部分修复。
4. **[PR #8314](https://github.com/agentscope-ai/QwenPaw/pull/8314)** — 已开启 **8 天**，首次贡献者，零评论。对首次贡献者的首次响应缓慢是留存风险。
5. **[PR #8318](https://github.com/agentscope-ai/QwenPaw/pull/8318)** — 已开启 **7 天**，关闭长期存在的 issue #1775，零评论。
6. **[Issue #8415](https://github.com/agentscope-ai/QwenPaw/issues/8415)** — 非 UTF-8 文件损坏并伴随虚假成功消息；无修复 PR，且与 #8414 标题冲突，影响分诊。

**数据质量说明：** 所有 PR 都报告 `Comments: undefined`，且 #8414/#8415 为不同 bug 携带相同标题。两者都限制自动分诊，应在工具/issue 元数据中修正。

---

*本摘要基于截至 2026-10-26 的 24 小时窗口内 GitHub 活动生成。所有计数和链接均反映所提供的数据集。*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目简报 — 2026-10-26

## 1. 今日概览

ZeroClaw 在过去 24 小时内出现 **56 次 issue/PR 更新**（6 个 issue，50 个 PR），但只有 **两次状态变更**：一个 issue 关闭，一个 PR 合并/关闭。其余 **49 个 open PR** 受到评审、CI 或 rebase 活动的影响，这表明 **高变更量和低合并吞吐**，而非活跃交付。没有发布新版本。工作集中在四条主线：长期运行的 **persistent-memory parity tracker**（[#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891)）、**渠道成熟度**（reactions、streaming、附件处理）、**安全/运行时加固**，以及一批以 **`release:v0.9.0` 标记的 PR** 正作为堆叠分支进行筹备。整体健康度：**贡献者兴趣很强，但评审带宽似乎是约束瓶颈** —— 多个 PR 带有 `needs-author-action` 或 `needs-maintainer-review`，且已积压 2–5 周。

> 数据说明：PR 评论数报告为 `undefined`，且唯一合并/关闭的 PR 不在 top-20 快照中，因此无法确认其内容。所有可见项均显示 0 👍，所以 reaction 数据今天不是可用信号。

## 2. 发布

过去 24 小时内**无发布**。没有需要报告的迁移或破坏性变更说明。

前瞻信号：至少六个 open PR 带有 `release:v0.9.0` 标签（[#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174)、[#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986)、[#11855](https://github.com/zeroclaw-labs/zeroclaw/pull/11855)、[#11976](https://github.com/zeroclaw-labs/zeroclaw/pull/11976)、[#11980](https://github.com/zeroclaw-labs/zeroclaw/pull/11980)），表明 v0.9.0 范围正在组装，但尚不可落地。

## 3. 项目进展

- **今日合并/关闭 PR：1 个** —— 无法从提供的 top-20 快照中识别；可见的 top 20 全部为 `OPEN`。
- **已关闭 issue：[#12034](https://github.com/zeroclaw-labs/zeroclaw/issues/12034)** —— *RFC：Personal Agent MVP：面向访客的知识与请求子系统*，当天提交并关闭，0 条评论。它引用了配套 PR [#12033](https://github.com/zeroclaw-labs/zeroclaw/pull/12033)（`rfc/personal-agent-mvp`）。未经讨论即关闭，暗示撤回、合并或流程拒绝，而非完成决策。

**正在推进的功能与修复（评审中，尚未合并）：**

| 领域 | 条目 | 实质内容 |
|---|---|---|
| 内存 | [#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209) | 拒绝未知的内存后端，而不是静默回退到 Markdown |
| 运行时 | [#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174) | 所有 turn 入口点采用接收 capability 的构造函数（XL，已 open 30 天） |
| Gateway/Web | [#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986) | 作用域化的 dashboard chat 经核心路由；堆叠在另外 3 个 PR 之上 |
| 渠道 | [#11754](https://github.com/zeroclaw-labs/zeroclaw/pull/11754)、[#11739](https://github.com/zeroclaw-labs/zeroclaw/pull/11739)、[#11744](https://github.com/zeroclaw-labs/zeroclaw/pull/11744) | 可配置的 ack reactions；Telegram reaction 撤回；Telegram/Slack 中的 reasoning streaming |
| 渠道 | [#11267](https://github.com/zeroclaw-labs/zeroclaw/pull/11267)、[#11746](https://github.com/zeroclaw-labs/zeroclaw/pull/11746) | Lark/Feishu 图片解析；入站邮件附件持久化到 workspace |
| 安全 | [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326)、[#11644](https://github.com/zeroclaw-labs/zeroclaw/pull/11644)、[#11672](https://github.com/zeroclaw-labs/zeroclaw/pull/11672)、[#11283](https://github.com/zeroclaw-labs/zeroclaw/pull/11283) | RPC 日志读取作用域控制（p1）、cron shell 输出泄露、JPEG 解码投影、SOP scope→tool-name 匹配 |
| 插件 | [#11855](https://github.com/zeroclaw-labs/zeroclaw/pull/11855) | 清理被弃用的安装暂存目录 |
| CI/Infra | [#11988](https://github.com/zeroclaw-labs/zeroclaw/pull/11988)、[#11976](https://github.com/zeroclaw-labs/zeroclaw/pull/11976)、[#11741](https://github.com/zeroclaw-labs/zeroclaw/pull/11741) | Semgrep SARIF 过滤；CLI-only 构建棘轮；dist-broad 容器镜像 |
| i18n/测试 | [#11774](https://github.com/zeroclaw-labs/zeroclaw/pull/11774)、[#11770](https://github.com/zeroclaw-labs/zeroclaw/pull/11770) | Regional→language locale 回退；与 locale 无关的渠道测试 |

主导性结构模式是 **堆叠的 PR 链**（尤其是来自 JordanTheJet 的 PR，例如 #11986 依赖 #11965/#11186/#11964，而 #11976 依赖 #11975/#11269/#11973）。这提高了评审成本，并且很可能是低合并数量的驱动因素。

## 4. 社区热点

1. **[#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891) — Persistent memory parity tracker** · 14 条评论 · 自 2026-07-09 起 open · `priority:p2`、`risk:high`、`type:tracker`、`topic:memory`
   唯一真正有讨论的条目。追踪一个多 PR 推进，覆盖跨会话记忆的 **策展、相关性和可运维性层面**，根据其自身快照，目前为“7 个具名 open 项：4 个 issue，3 个 PR”。*底层需求：* 用户希望记忆能像成熟的对等 runtime 一样可预测地运行——不只是存储，还包括选择、排序和管理。这是项目最具战略性的单个 open 线程。

2. **[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853) — `npm audit` 失败（2026-10-19）** · 3 条评论 · bot 提交 · `security`、`risk:high`
   一个 high/critical 发现，外加一个 moderate 的 `@tailwindcss/typography` 公告，并关联了一次 Actions 运行。*底层需求：* 自动化供应链门禁正在报警，且 7 天内无人负责修复。考虑到 Web dashboard 的增长（#11986、#12021），前端依赖卫生比工具噪音所暗示的更重要。

3. **PR 讨论量不可观测**（计数为 `undefined`），因此热度只能从标签和存在时长推断：[#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174)（30 天，XL，`distinguished contributor`）、[#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209)（28 天，`needs-author-action`）、[#11056](https://github.com/zeroclaw-labs/zeroclaw/pull/11056)（34 天，`size:XS`）。

**解读：** 社区精力集中在 **runtime 语义**（memory、capabilities）和 **安全** 上，而日常渠道工作产生了 PR，却很少有可见对话。六个 issue 中有三个零评论——要么是快速分诊，要么是评审者参与度低。

## 5. 缺陷与稳定性

按严重程度排序，并附修复 PR 状态：

| 严重程度 | 条目 | 影响 | 修复 PR？ |
|---|---|---|---|
| **高** | [#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853) — npm audit high/critical | 构建门禁失败；Web 依赖存在供应链暴露 | ❌ 未关联 |
| **高** | [#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209) — 未知 `memory.backend` 静默构建 `MarkdownMemory` | **持久记忆静默错路由**：无法识别的 backend 将跨会话记忆写入 workspace Markdown 文件 | ✅ 修复即该 PR 本身，因 `needs-author-action` 停滞 28 天 |
| **高** | [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326) — `logs/query` 和 `logs/get` 缺少 `require_global_stream_access` | 带作用域的主体可以读取其不应读取的持久化日志 | ✅ 修复 PR 已 open，25 天 |
| **高** | [#11644](https://github.com/zeroclaw-labs/zeroclaw/pull/11644) — cron shell 作业将原始 `status/stdout/stderr` 转发到渠道 | 命令文本和 stderr 泄露到投递渠道 | ✅ 修复 PR 已 open，16 天，`needs-maintainer-review` |
| **中** | [#11283](https://github.com/zeroclaw-labs/zeroclaw/pull/11283) — SOP scope 组（`fs`、`web`）与注册工具名（`file_read`、`web_search`）不匹配 | SOP 步骤上的 **权限作用域不匹配** —— 可能限制不足或过度限制 | ✅ 修复 PR 已 open，26 天 |
| **中** | [#11672](https://github.com/zeroclaw-labs/zeroclaw/pull/11672) — JPEG 准入分配 | 解码峰值核算；潜在内存压力 | ✅ 修复 PR 已 open，14 天 |
| **中（静默）** | [#12035](https://github.com/zeroclaw-labs/zeroclaw/issues/12035) — `knowledge_bundles` 已验证但运行时从未消费 | 配置被静默忽略，**没有警告**；没有崩溃，但运维者意图丢失 | ❌ 无 |
| **低（S3）** | [#12021](https://github.com/zeroclaw-labs/zeroclaw/issues/12021) — Web UI 配置保存按钮在 banner 保存后仍保持高亮 | 在 daemon 重载前显示“1 个未保存更改”；外观/UX 问题 | ❌ 无 |

**模式：** 这批中最危险的 bug 是 **fail-open 或 fail-silent** 行为——无法识别的 memory backend、被忽略的 knowledge bundle、未强制执行的日志作用域。今天没有报告崩溃或回归。值得注意的是，一个定义良好的小型 PR（[#11056](https://github.com/zeroclaw-labs/zeroclaw/pull/11056)，`size:XS`）已搁置 34 天，因此延迟并不纯粹是规模问题。

## 6. 功能请求与路线图信号

**今日提交的新请求：**

- **[#12022](https://github.com/zeroclaw-labs/zeroclaw/issues/12022)** —— 记忆召回：选择性启用的 external rerank 阶段。添加 `rerank_strategy = "external"`，将符合条件的候选池 POST 到运维者配置的 `/v1/rerank` 端点，并保持其排序；**默认关闭，任何错误时 fail-open 到本地路径。** 设计上规格明确且低风险——是 **v0.9.x memory 发布** 的有力候选，因为它直接接入 #8891 的相关性层面。
- **[#12035](https://github.com/zeroclaw-labs/zeroclaw/issues/12035)** —— 在 prompt 组装时实际消费 `knowledge_bundles`（或发出警告）。与其说是功能，不如说是正确性缺口；落地成本低，适合作为 first issue 素材。
- **[#12034](https://github.com/zeroclaw-labs/zeroclaw/issues/12034, closed)** —— Personal Agent MVP：面向访客的知识与请求子系统。当天关闭，并根据 RFC #5615 披露为 AI。即使在关闭状态下，它也表明意图走向 **可部署、面向访客的个人 agent**，而非仅面向开发者的 runtime。

**预计 v0.9.0 / 近期内容（基于标签与堆叠关系）：**

1. **Runtime capability plumbing** —— [#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174) + 文档 [#11980](https://github.com/zeroclaw-labs/zeroclaw/pull/11980)。当前进行中最深层的架构变更。
2. **作用域化 dashboard chat** —— [#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986)，取决于其三个基础 PR。
3. **渠道打磨包** —— 可配置的 ack reactions、Telegram/Slack reasoning streaming、Lark 图片、邮件附件。用户可见价值高，大多为 XL 规模。
4. **插件安装健壮性** —— [#11855](https://github.com/zeroclaw-labs/zeroclaw/pull/11855)。
5. **带 artifact ratchet 的 CLI-only 构建目标** —— [#11976](https://github.com/zeroclaw-labs/zeroclaw/pull/11976)，外加 dist-broad 镜像（[#11741](https://github.com/zeroclaw-labs/zeroclaw/pull/11741)）。

**v0.9.0 中不太可能：** external rerank（#12022，全新）、knowledge-bundle 消费（#12035，未分配）以及 npm audit 修复（#11853，无负责人）。

## 7. 用户反馈摘要

**主要痛点：静默错误行为。** 今天或近几天有四份独立报告描述系统接受输入后什么也不做——或做了别的事——却不告知用户：`knowledge_bundles` 验证后被丢弃（[#12035](https://github.com/zeroclaw-labs/zeroclaw/issues/12035)）、未知 memory backend 静默写入 Markdown（[#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209)）、Telegram/Lark 富消息在到达 agent 前被丢弃（[#11267](https://github.com/zeroclaw-labs/zeroclaw/pull/11267)）、邮件附件尽管有文档却从未写入 workspace（[#11746](https://github.com/zeroclaw-labs/zeroclaw/pull/11746)）。这是数据集中最清晰的跨领域不满主题。

**运维者/企业需求浮现：**
- **最小权限强制** —— 带作用域的主体不得读取全局日志（[#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326)）。
- **数据外泄防护** —— cron 诊断信息不得进入渠道（[#11644](https://github.com/zeroclaw-labs/zeroclaw/pull/11644)）。
- **供应链保障** —— `npm audit` 失败会阻塞构建（[#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853)）。

**本地化摩擦：** 非英语 locale 同时破坏测试和 UI 行为（[#11774](https://github.com/zeroclaw-labs/zeroclaw/pull/11774)、[#11770](https://github.com/zeroclaw-labs/zeroclaw/pull/11770)）。贡献者现在正在修复由硬编码英文提示文本导致的测试脆弱性——这是真实非英语使用的迹象。

**可见用例：** 多渠道助手部署（Slack、Telegram、Matrix、Lark/Feishu、Email、WhatsApp、Web）、SOP 驱动的带作用域自动化、基于插件的扩展，以及——通过已关闭的 RFC——**面向访客的个人 agent**。**满意度信号：** 持续的贡献量和细致的文档工作（例如 WhatsApp 语音留言文档，[#11056](https://github.com/zeroclaw-labs/zeroclaw/pull/11056)）表明用户有参与度。**不满信号：** 反复出现的 `needs-author-action` 标签意味着维护者在等待贡献者，而不是相反——这是流程延迟，不是拒绝。

## 8. 积压项观察

需要维护者关注的条目，按存在时长排序：

| 存在时长 | 条目 | 重要性 |
|---|---|---|
| **109d** | [#8891](https://github.com/zeroclaw-labs/zeroclaw/issues/8891) — memory parity tracker | `priority:p2`、`risk:high`、14 条评论，仍在协调 4 个 issue + 3 个 PR。没有可见的完成标准或燃尽。 |
| **34d** | [#11056](https://github.com/zeroclaw-labs/zeroclaw/pull/11056) — WhatsApp voice-note 文档 | `size:XS`、`risk:low`、`needs-author-action`。一个琐碎的文档 PR 卡了一个多月，是最明显的流程异味。 |
| **30d** | [#11174](https://github.com/zeroclaw-labs/zeroclaw/pull/11174) — runtime capability 构造函数 | XL、`distinguished contributor`、`release:v0.9.0`、`risk:high`。**很可能是发布阻塞项**；评审发现缺口（#11980 用于修补文档）。 |
| **28d** | [#11209](https://github.com/zeroclaw-labs/zeroclaw/pull/11209) — 拒绝未知 memory backend | `needs-author-action`，但这关闭了一个静默数据路由 bug。 |
| **26d** | [#11267](https://github.com/zeroclaw-labs/zeroclaw/pull/11267)、[#11283](https://github.com/zeroclaw-labs/zeroclaw/pull/11283) | Lark 图片丢弃；SOP scope/tool-name 不匹配。两者均为 `needs-author-action`。 |
| **25d** | [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326) — RPC 日志作用域 | `priority:p1`、`release-gate`、`domain:security`。**积压项中最高优先级的安全修复。** |
| **16d** | [#11644](https://github.com/zeroclaw-labs/zeroclaw/pull/11644) — cron 泄露 | `needs-maintainer-review`；与安全相关，且卡在评审者处。 |
| **14d** | [#11672](https://github.com/zeroclaw-labs/zeroclaw/pull/11672) — JPEG 投影 | `needs-maintainer-review`。 |
| **7d** | [#11853](https://github.com/zeroclaw-labs/zeroclaw/issues/11853) — npm audit 失败 | Bot 提交，`risk:high`，无 assignee，无修复 PR。无主的自动化安全债务。 |

**评估：** 积压项并不缺工作——缺的是 **评审决策**。两个 `needs-maintainer-review` 安全 PR 和一个 `priority:p1` release-gate PR 正在等待维护者，而五个 `needs-author-action` PR 正在等待贡献者。清理 #11326 并解除 #11174 的阻塞，将实质性改善安全态势和 v0.9.0 时间线；为 #11853 指派负责人并合并 #11056，将是队列正在流动的低成本信号。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*