# OpenClaw 生态日报 2026-10-27

> Issues: 500 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-10-27 01:36 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-10-27

## 1. 今日概览

OpenClaw 正以极高吞吐量运行：过去 24 小时更新了 1,000 个条目（500 个 issue：400 个打开/活跃，100 个已关闭；500 个 PR：182 个打开，318 个已合并/关闭），其中很大一部分变更量由正在进行的内部性能/测试清理专项驱动（“deslop”、批量 `d64x` 测试移除、懒加载和 catalog 复用优化）。但是，**没有发布任何版本**，issue 跟踪器显示 P0“ux-release-blocker”报告持续累积——最集中于 Windows 更新/重启可靠性、SQLite/WAL 持久化和内存增长。社区注意力主要集中在少数高严重度、长期运行的讨论串上（最热 issue 已累积 139 条评论）。活动评估：**维护者推进速度很高，但发布就绪度受制于一组尚未解决的 Windows/存储可靠性问题**。

## 2. 发布

无。过去 24 小时没有新发布，“Latest Releases”信息流为空。鉴于多个打开的 P0 项带有 `impact:ux-release-blocker` 标签（尤其是 [#143524](https://github.com/openclaw/openclaw/issues/143524)、[#160959](https://github.com/openclaw/openclaw/issues/160959)、[#170956](https://github.com/openclaw/openclaw/issues/170956)、[#166634](https://github.com/openclaw/openclaw/issues/166634)），没有发布与发布列车被暂缓而非处于空闲状态的情况相符。没有迁移或破坏性变更说明需要报告。

## 3. 项目进展

318 个 PR 于今日被合并/关闭。值得注意的落地与推进：

- **安全/UX：** [#185263](https://github.com/openclaw/openclaw/pull/185263)（已关闭）——不再对常规 OAuth 访问令牌过期显示登录警告；Settings → Models 现在可区分令牌过期与不可用认证。
- **Web UI 修复：** [#185292](https://github.com/openclaw/openclaw/pull/185292)（已关闭）——已保存的高亮文本 Side chat 评论无需二次提问即可提交；[#184617](https://github.com/openclaw/openclaw/pull/184617)（已关闭）——在 Control UI 上传前缩小过大的 JPEG 相机照片（修复 Android Chrome 附件失败）。
- **性能：** [#185287](https://github.com/openclaw/openclaw/pull/185287)（已关闭）——移除逐字段的 prompt-hook envelope 元组；[#185249](https://github.com/openclaw/openclaw/pull/185249)（已关闭）——移除未使用的插件根加载跟踪。
- **进行中的高价值修复（打开）：** [#185250](https://github.com/openclaw/openclaw/pull/185250) 阻止在 provider 认证冷却期间出现 prepared-runtime 重建风暴；[#184293](https://github.com/openclaw/openclaw/pull/184293) 将 compaction 的硬截止时间放到单调时钟上（修复时钟跳变导致的过早 compaction 失败）；[#185293](https://github.com/openclaw/openclaw/pull/185293) 防止长流式 HTML/JSX 回复在每个不完整标签上重新投影整个回复；[#185238](https://github.com/openclaw/openclaw/pull/185238) 修复 `store` SecretRef 认证令牌在 PostgreSQL 上导致 CLI Gateway 调用失败的问题；[#185237](https://github.com/openclaw/openclaw/pull/185237) 并发重建移植的内存索引以减少迁移停机时间；[#183595](https://github.com/openclaw/openclaw/pull/183595) 和 [#184994](https://github.com/openclaw/openclaw/pull/184994)/[#184951](https://github.com/openclaw/openclaw/pull/184951) 减少 Control UI 重载工作和过早的 chat-route 加载。
- **表明缺陷已解决的已关闭 issue：** [#184315](https://github.com/openclaw/openclaw/issues/184315)（operator.admin 因 operator.read 被拒——作用域语义）、[#162899](https://github.com/openclaw/openclaw/issues/162899)（Windows Control UI 托管更新在 `validating` 中冻结）、[#89430](https://github.com/openclaw/openclaw/issues/89430)（应用认证下 Google Chat 媒体投递 403）、[#154517](https://github.com/openclaw/openclaw/issues/154517)（sessions 删除/归档 `not_found`）、[#172091](https://github.com/openclaw/openclaw/issues/172091)（worker SQLite 事务在准入等待期间保持打开）、[#150696](https://github.com/openclaw/openclaw/issues/150696)（父级 yield 后 Codex-native 子活动）。

## 4. 社区热点

| 条目 | 状态 | 评论数 | 信号 |
|---|---|---|---|
| [#143524](https://github.com/openclaw/openclaw/issues/143524) SQLite WAL 增长到 1.4–2.8 GB，阻塞 Gateway 启动（Windows） | 已关闭，P0 | 139 | 项目内最大的讨论串；Windows 上的存储层持久性是社区最大的单一关切。 |
| [#160959](https://github.com/openclaw/openclaw/issues/160959) Gateway 在捕获大型外部插件时阻塞数分钟（2026.9.6 回归） | 打开，P0 | 57 | 插件代际捕获（#144252）引入事件循环饥饿；影响启动和运行时重新发布。 |
| [#159662](https://github.com/openclaw/openclaw/issues/159662) `prepared-model-catalog.worker.js` 无界泄漏约 4–5 GB/h | 打开，P0 | 26 | 与 provider 无关，在冷重启后可复现；迫使小型主机频繁重启。 |
| [#170956](https://github.com/openclaw/openclaw/issues/170956) Windows 启动缓慢、插件关闭不干净、`schtasks` 权限错误 | 打开，P0 | 21 | 复合可靠性故障：重启/更新“只有在多次重试后才成功”。 |
| [#140010](https://github.com/openclaw/openclaw/issues/140010) Windows 睡眠/恢复后 WebSocket 重连中断 30–60 秒以上 | 打开，P1 | 16 | 笔记本电脑/移动主机的 UX；解冻恢复被繁忙的 Gateway 延后。 |
| [#182476](https://github.com/openclaw/openclaw/issues/182476) `update repair` 失败并出现 `OPENCLAW_STATE_LEASE_LOST` | 打开，P1 | 14 | 新出现（10 月 24 日），与更广泛的生命周期租约问题群一致。 |
| [#67419](https://github.com/openclaw/openclaw/issues/67419) Bootstrap 文件每轮重新注入，浪费 20–30% token | 打开，P2，陈旧 | 14 | 最早的高可见度成本抱怨（创建于 4 月 15 日）。 |

**潜在需求：** (a) 在 Windows 上有一套可信的存储/检查点方案（WAL、完整性检查、事务、租约）；(b) 无论插件/模型目录规模如何，启动延迟可预测；(c) 在 8–16 GB 主机上内存有界；(d) 通过稳定的 prompt 前缀和非冗余上下文注入提升 token/成本效率。

## 5. 缺陷与稳定性

**P0 / 发布阻塞（打开）**
- [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway 在大型外部插件上阻塞数分钟；自 2026.9.6 起回归。未标记修复 PR（`no-new-fix-pr`），需要维护者评审。
- [#159662](https://github.com/openclaw/openclaw/issues/159662) — prepared-model-catalog worker 中存在 4–5 GB/h 内存泄漏；未标记修复 PR。
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows 启动/关闭/`schtasks` 可靠性；修复形态明确，等待维护者。
- [#166634](https://github.com/openclaw/openclaw/issues/166634) — 崩溃恢复期间 checkpoint 过期，导致 agent 对 `chat.history`/`models.list` 永久处于 `UNAVAILABLE`；标记为 `not-repro-on-main`，需要复现。

**P1（打开，值得注意）**
- [#182476](https://github.com/openclaw/openclaw/issues/182476) `update repair` 期间出现 `OPENCLAW_STATE_LEASE_LOST`（Windows）。
- [#183786](https://github.com/openclaw/openclaw/issues/183786) Windows beta 升级在查询已停止的前台 Gateway PID 时卡住。
- [#181432](https://github.com/openclaw/openclaw/issues/181432) 2026.9.4 → 2026.10.5 后基线 RSS 从约 300 MB 回退到 1.89 GB（Linux ARM64），且 SQLite 事务保持 >1.5 秒。
- [#145203](https://github.com/openclaw/openclaw/issues/145203) `openai-completions` SSE 流挂起（48.5 分钟）后从未恢复；停滞看门狗因 `stream_progress` 饥饿。
- [#134993](https://github.com/openclaw/openclaw/issues/134993) 2026.8.1 后，在大型 skill/agent 集群下，Gateway 在文件系统发现中占满一个 CPU 核。
- [#118885](https://github.com/openclaw/openclaw/issues/118885) 启动时对多 GB 状态 DB 进行冗余的完整 `PRAGMA integrity_check` 遍历。
- [#172837](https://github.com/openclaw/openclaw/issues/172837) Codex `nativeSubagentAssignments` 在投递放弃后从未移除——每个新父线程都会重新固定/重新发送。
- [#177656](https://github.com/openclaw/openclaw/issues/177656) 原生主机执行时出现 `Direct shared-state reader requires current integrity admission`（2026.10.5 上至少 28 次失败）。

**P2 / 正确性与数据完整性**
- [#166017](https://github.com/openclaw/openclaw/issues/166017) `mcp.sessionIdleTtlMs` 默认值未应用 → MCP 运行时从不被驱逐，broker 达到 256 子进程上限，所有 exec/MCP 启动失败。
- [#126906](https://github.com/openclaw/openclaw/issues/126906) 拒绝 `write` 工具会静默禁用内存持久化，而 agent 报告成功。
- [#158070](https://github.com/openclaw/openclaw/issues/158070) 预检 compaction 使进行中的投递失效（“tool caller authority is no longer active”）。
- [#178300](https://github.com/openclaw/openclaw/issues/178300) 在 1 token 的 `length` 停止后出现虚假成功。
- [#129314](https://github.com/openclaw/openclaw/issues/129314)、[#151962](https://github.com/openclaw/openclaw/issues/151962) — 内部运行时字符串（“phantom user messages”）作为真实用户轮次浮现，未持久化到 `transcript_events`。
- [#146902](https://github.com/openclaw/openclaw/issues/146902)、[#143017](https://github.com/openclaw/openclaw/issues/143017) — 工具集频繁变动和前缀重写导致 prompt-cache 失效（一份报告测得 21.3M 缓存写入 token）。

**修复 PR 可用性：** 可形成修复/可排队的项包括 [#168456](https://github.com/openclaw/openclaw/issues/168456)（cron `agentTurn` 的 turn-key 断言导致 fallback 失败）和 [#177094](https://github.com/openclaw/openclaw/issues/177094)（系统通知镜像挤掉最后答案）。大多数 P0 存储/内存项明确标记为 `clawsweeper:no-new-fix-pr`，即**仍无人负责**。

## 6. 功能请求与路线图信号

- **身份/安全：** [#162164](https://github.com/openclaw/openclaw/issues/162164) 在 iOS/macOS 上可选启用个人身份，同时保留 Shared owner（活跃贡献映射，多个切片已合并）；[#149024](https://github.com/openclaw/openclaw/issues/149024) 主机级执行前授权守卫 + 空 allowlist 的 fail-closed 语义（需要安全评审）。
- **资源感知调度：** [#161050](https://github.com/openclaw/openclaw/issues/161050) 为 `sessions_spawn`/隔离 cron 运行提供内存压力感知准入——`subagents.maxConcurrent` 统计子进程数，而非 RSS（每个 `claude-cli` 子进程约 250–400 MB）。
- **会话/UX 连续性：** [#112372](https://github.com/openclaw/openclaw/issues/112372) 为 `sessions_send` 提供可选的、绑定来源的延迟完成（`wakeOnReply`）；[#158800](https://github.com/openclaw/openclaw/issues/158800) 在 `/new` 后浏览/分叉先前的频道聊天片段；[#175826](https://github.com/openclaw/openclaw/issues/175826) Android 对等支持聊天内问题/原生审批。
- **频道集成：** [#136271](https://github.com/openclaw/openclaw/issues/136271) 为 Teams 文件上传提供动态 SharePoint 站点解析。

**预测：** 下一次发布（一旦切出）最可能以可靠性和性能为主，而不是新增表面功能——Control UI 重载/流式修复、编译/时钟正确性、PostgreSQL 认证令牌路径以及 catalog 复用优化。内存压力准入（#161050）和身份工作（#162164）看起来是下一周期最强的候选；两者仍受产品/安全决策约束。

## 7. 用户反馈摘要

- **Windows 是主要痛点来源。** Windows 主机用户报告 WAL 增长到数 GB（#143524）、通过 `schtasks` 和生命周期租约发生的重启/更新失败（#170956、#182476、#183786、#170654）、更新器冻结（#162899，现已关闭）以及睡眠/恢复后重连中断（#140010）。情绪上是对重试密集恢复的挫败感（“只有在多次重试后才成功”）。
- **小主机和多 agent 运维者触及资源上限。** 4–5 GB/h 泄漏（#159662）、1.9 GB 基线 RSS（#181432）、MCP broker 在 256 个子进程时耗尽（#166017）以及单 CPU 核发现循环（#134993），使常开 Gateway 在普通硬件上不切实际。
- **成本敏感度非常明确。** 多份详细报告量化了 token 浪费：bootstrap 文件消耗 20–30% 上下文（#67419）、前缀重写产生 21.3M 缓存写入 token（#143017），以及工具集频繁变动破坏缓存复用（#146902）。
- **对 agent 报告状态的信任因静默失败而受损**：内存写入被拒绝时却报告成功（#126906）、截断后虚假成功（#178300）、微信/Slack 桥接中的幻影用户消息（#151962、#129314），以及崩溃恢复后 agent 永久不可用（#166634）。报告者技术水平很高，提供抓包记录和复现，并经常提交本地补丁——这是有投入的高级用户群体的强烈信号。

## 8. 待办观察

需要维护者决策的长期存在或低关注事项：

- [#67419](https://github.com/openclaw/openclaw/issues/67419)（4 月 15 日）——上下文膨胀 / bootstrap 重新注入；14 条评论，2 个 👍，标记为 `needs-product-decision`。最高杠杆的成本修复仍未决定。
- [#118885](https://github.com/openclaw/openclaw/issues/118885)（8 月 3 日）——多 GB DB 上冗余的启动完整性检查；P1，`needs-product-decision`，`bulk-filed`。
- [#129314](https://github.com/openclaw/openclaw/issues/129314)（8 月 25 日）——内部上下文作为独立轮次泄漏；陈旧，`needs-info`，`session-state`。
- [#114414](https://github.com/openclaw/openclaw/issues/114414)——带日期的 TODO 清查，列出逾期 CI/auth 和工作流清理；一个反复出现的维护债标记。
- [#136271](https://github.com/openclaw/openclaw/issues/136271) 和 [#112372](https://github.com/openclaw/openclaw/issues/112372)——两者都有打开的关联 PR，但仍因产品决策停滞。
- PR 侧：[#144685](https://github.com/openclaw/openclaw/pull/144685)（macOS 审批面板关闭，创建于 9 月 11 日）仍处于 `needs proof`；[#183595](https://github.com/openclaw/openclaw/pull/183595) 明确为“ready for maintainer review; stop before merge”，且有一个未解决的、仅候选版本出现的终端测试失败——值得维护者投入时间的候选。
- `clawsweeper:needs-maintainer-review` 附加在前 50 个 issue 的很大一部分上，表明当前瓶颈是评审带宽，而不是 issue 接收。

---

## 横向生态对比

# 跨项目对比报告——个人 AI 助手 / Agent 开源生态系统
**时间窗口：** 2026-10-27（24 小时社区摘要） · **项目：** OpenClaw、Hermes Agent、QwenPaw、ZeroClaw、IronClaw

---

## 1. 生态系统概览

本样本中的五个项目已经收敛到一种共同架构——长期运行的网关/守护进程、通道适配器、工具/插件层、会话存储，以及记忆/检索后端——并且现在处于**运行稳定性加固阶段**，而不是功能扩展阶段：在窗口期内，**所有项目都没有发布任何版本**，尽管合计有 1,224 条 issue/PR 更新。竞争差异化已经从能力广度转向**持久性、可诊断性和成本控制**，缺陷主要集中在会话/准入语义、上下文压缩、记忆完整性和资源背压。生态系统呈现明显的双峰分布：OpenClaw 的体量约为任何同行的 20 倍（1,000 项 vs 100/72/52），而中等规模项目则显示贡献者供给持续超过评审能力。一个项目（IronClaw）处于休眠状态，这确认了该品类中的整合压力已经显现。

---

## 2. 活动对比

| 项目 | Issues（开启/关闭） | PRs（开启/合并） | 合并/关闭率 | 发布状态 | 健康评分 |
|---|---|---|---|---|---|
| **OpenClaw** | 500（400 / 100） | 500（182 / 318） | **63.6%** PR · 20% issue | **无**——发布列车被 4 个未关闭的 P0 `ux-release-blocker` 阻塞 | **7.0 / 10** |
| **QwenPaw** | 24（17 / 7） | 48（33 / 15） | 31.3% PR · 29% issue | 无——2.2.3b1 beta 正在推进 | **6.5 / 10** |
| **Hermes Agent** | 50（拆分未披露） | 50（45 / 5） | 10.0% PR | 无——最新稳定版 v0.22.5 带有回归问题 | **5.0 / 10** |
| **ZeroClaw** | 23（23 / 0） | 29（28 / 1） | **3.4%** PR · 0% issue | 无——v0.8.7 RC 准备中，v0.9.0 工作堆积 | **3.5 / 10** |
| **IronClaw** | 0 | 0 | — | 无 | 休眠 |

*健康评分细则（综合评分，满分 10 分）：关闭吞吐量 30% · 发布就绪度 / P0 负担 30% · 评审响应度 20% · 互动质量 20%。评分仅用于本样本内比较，并非绝对质量判断。*

**关键观察：**
- 除 OpenClaw 外，三个活跃同行项目合并了 127 个涉及 PR 中的 21 个（16.5%），而 OpenClaw 为 63.6%——OpenClaw 是唯一关闭速度快于积压速度的项目。
- ZeroClaw 在 24 小时内关闭了 0 个 issue，却在一天内新开 8 个安全 issue。
- **所有项目都表示，约束瓶颈是评审带宽，而不是接收量**——OpenClaw（前 50 个中大多数带有 `clawsweeper:needs-maintainer-review`）、Hermes（4 个 PR 已超过 60 天，其中一个因等待维护者点击 CI 批准而阻塞）、QwenPaw（17 个开启的 PR，其中包括若干 XL/XXXL）、ZeroClaw（5+ 个 `needs-author-action`，1 个 `do-not-merge`）。

---

## 3. OpenClaw 的位置

**相对同行的优势**
- **社区规模与吞吐量：** 24 小时 1,000 项（约为 QwenPaw 的 8 倍、Hermes 的 10 倍、ZeroClaw 的 19 倍）；最热帖有 139 条评论，而同行最高为 22 条。报告者会提交抓包和本地补丁——这是一个投入度很高的高级用户群体。
- **基础设施完整度：** 样本中唯一具备 PostgreSQL 后端选项、SecretRef 凭据抽象、状态租约、预置模型目录工作池，以及受单调时钟约束的压缩的项目。同行仍在修复单进程假设（Hermes #139818 远程恢复，QwenPaw #8279 线程耗尽）。
- **发布纪律（反向）：** 一个*被暂停*的发布列车比未暂停的发布列车是更强的信号——项目以自身 P0 为门禁，而不是带着 P0 发布（对比 Hermes，其 v0.22.5 稳定版带有一个未解决的准入冲突回归）。

**技术路线差异**
OpenClaw 的故障模式是**基础设施层面的，而非逻辑层面的**——多 GB 的 SQLite/WAL 增长（#143524）、执行 `update repair` 时状态租约丢失（#182476）、每小时 4–5 GB 的 worker 泄漏（#159662）、插件捕获期间事件循环饥饿（#160959）。这种画像是一个已经解决了 *agent 逻辑*、现在正为作为常开守护进程付出代价的系统的特征。同行项目在更靠栈底的位置失败（aiohttp 导入崩溃、线程池死锁、准入字面量），因为它们的运行时更简单。

**社区规模对比**
在每个互动指标上大约大一个数量级，但也带来相应负担：**最高严重级别的条目明确无人负责**——`clawsweeper:no-new-fix-pr` 被附加到 #159662 和 #160959，而 #166634 被标记为 `not-repro-on-main`。体量已经超过了深度修复的人力供给。

---

## 4. 共同技术焦点领域

| 需求 | 项目 | 具体需求 |
|---|---|---|
| **上下文/token 经济性与非破坏性压缩** | OpenClaw、ZeroClaw、QwenPaw、Hermes | Bootstrap 重新注入消耗 20–30% 的上下文（OC #67419）；前缀重写产生 21.3M 缓存写入 token（OC #143017）；`context_compression.*` 配置键静默失效（ZC #10780/#10781）；trim *永久删除*轮次（ZC #11711）；压缩后仍完全无法容纳请求（QP #8312、#8417） |
| **会话身份、准入与唤醒语义** | Hermes、OpenClaw、ZeroClaw | `admission_conflict` 拒绝循环达到每天 2,641 次，而尝试上限从未触发（Hermes #145261、#143720、#142461）；检查点过期导致 agent 永久处于 `UNAVAILABLE`（OC #166634）；失败的 ACP 轮次未在守护进程 RPC 路径上持久化（ZC #10673） |
| **持久化与数据完整性** | 所有四个活跃项目 | WAL/完整性检查成本与事务持有（OC #143524、#118885、#172091）；远程 `session.resume` → `not_found`（Hermes #139818）；embedding 批次静默丢失（QP #8040）；有损的 CRLF/编码重写却报告成功（QP #8415）；插件 manifest/二进制生成不匹配（ZC #11562） |
| **消除静默失败 / 可诊断性** | 所有四个活跃项目 | “126/126 processed”却丢弃了一个批次（QP #8040）；零日志的无限期挂起（QP #8435）；`data.fields` 与 `data.field` 的差异把精确拒绝变成*“couldn't start the model connection”*（Hermes #145286）；记忆写入被拒绝却报告成功（OC #126906）；仅有一句“Command not allowed by security policy”导致 agent 重试循环（ZC #11983） |
| **资源有界并发与背压** | 所有四个活跃项目 | 并发限制维度错误——限制子进程数而非 RSS（OC #161050，OC #166017 为 256 子进程上限）；基线 RSS 为 1.89 GB（OC #181432）；AnyIO 线程限制器耗尽（QP #8279）；webhook cron 达到线程池大小后死锁（Hermes #145315）；响应缓冲无界（ZC #12045） |
| **提供商/代理兼容性容忍度** | QwenPaw、Hermes、OpenClaw | 不符合规范的 OpenAI Responses 流（QP #8162）；SGLang 被误分类为 Ollama、缺少 Gemini `thought_signature`、缺少 `x-api-key` 路径（Hermes #145309、#104345、#145275）；认证冷却重建风暴（OC #185250） |
| **身份与最小权限作为发布门禁** | ZeroClaw、OpenClaw、Hermes | 8 个 issue 的 OIDC/工具作用域清查，包括 SOP 权限提升、XFF 限流器绕过、回调 CSRF（ZC #12046–#12053）；主机级执行前授权，采用失败关闭默认值（OC #149024）；A2A 无 token 模式加固、HITL 审批绕过（Hermes #145310、#131461） |
| **插件/通道契约深度** | QwenPaw、ZeroClaw、Hermes | 使用语义 token 主题而非 `colorPrimary`（QP #8071）；嵌套技能目录、通道作为子进程（QP #8178、#8187）；插件生成完整性（ZC #11562）；插件激活 401 被吞掉（Hermes #141878） |

---

## 5. 差异化分析

| 维度 | OpenClaw | Hermes Agent | QwenPaw | ZeroClaw |
|---|---|---|---|---|
| **主要产品形态** | 网关守护进程 + 控制 UI（Web），安装基础以 Windows 为主 | 网关 + 远程 `hermes serve` + 桌面客户端 + ACP 编辑器集成（Zed/VS Code/JetBrains/Obsidian） | **桌面应用**（Console、PawApp SDK、`deb`/AppImage、Finder/Explorer 集成、Chrome 原生消息传递） | Rust 守护进程 + RPC + Web 仪表盘 |
| **目标用户** | 运行常开多 agent 网关的高级用户/小团队 | 多界面运营者：Discord、Mattermost、飞书、QQ、Google Workspace；插件与编排作者 | 桌面终端用户，对 CJK/异构网关（火山引擎等）和本地化高度敏感 | 注重安全的自托管用户、多主体/多租户运营者、主权部署 |
| **架构特征** | 工作池、预置目录缓存、状态租约、SQLite/WAL **或 PostgreSQL**、SecretRef | Python asyncio/aiohttp、线程池卸载、MCP + A2A、Kanban 编排、子 agent 生命周期 API | Python 后端 + 桌面外壳、本地 ReMe/ReMeLight 记忆 + embeddings + 重排器、通道插件子进程 | Rust（cargo）、Qdrant 向量存储、OIDC、XMPP+OMEMO、Forgejo/Gitea forge 对齐、SOP/`sop_workshop` |
| **差异化押注** | 完整运行时的广度 + 可靠性 | 表面能力对齐（委派/ACP/Kanban） | 记忆质量 + 桌面打磨 + 本地化 | 身份、授权与供应链完整性 |
| **最薄弱侧翼** | Windows/存储可靠性、小主机内存、发布就绪度 | 会话准入层；PR 评审能力 | 压缩正确性；上下文窗口故障模式 | 合并吞吐量；v0.9.0 前 8 个无人负责的安全 issue |

**IronClaw** 没有信号，规划时应视为不活跃。

---

## 6. 社区动能与成熟度

**第 1 梯队——高速度、广覆盖（OpenClaw）。** PR 合并率 63.6%，每天 318 个落地，但很大一部分是内部专项搅动（“deslop”、批量删除测试、懒加载）。功能上最成熟；在 Windows 和 8–16 GB 主机上运行上最不稳定。*快速迭代，发布受约束。*

**第 2 梯队——高互动、受评审约束（QwenPaw、Hermes）。** 两者都呈现典型的中等规模特征：分流跟得上，深度修复跟不上。QwenPaw 能快速处理范围明确的报告（#8162 于 10 月 10 日提交，修复于 10 月 26 日合并；#8270 和 #4946 在本窗口关闭），但有 3 个严重且无修复 PR 的 bug，以及一个与隐私相关的 PR（#7835）自 9 月 17 日起未评审。Hermes 推进了 45 个 PR，但只合并了 5 个，同一天关闭了一个功能请求，并且对其三个最大稳定性风险（aiohttp 崩溃循环、webhook cron 死锁、准入问题簇）**完全没有 PR**。*在表面能力上快速迭代，在核心上趋于稳定。* 两者都展示了成熟度信号——生产环境 976 MB 历史数据库、Docker 部署、每日备份 cron。

**第 3 梯队——安全加固，积压腐化风险（ZeroClaw）。** 新进 23 个 issue，关闭 0 个；一天内提交 8 个安全 issue，且没有修复 PR；v0.9.0 的工作在未合并的父 PR 上堆叠了数层（#11234/#11230 基于 #11225）。*运行上趋于稳定，但在以安全为主题的发布前正在累积未评审风险。*

**休眠（IronClaw）。**

**跨项目成熟度判断：** 每个项目互动度最高的主题现在都是**信任或可靠性主题帖**，而不是能力公告——OpenClaw 的 139 条评论 WAL 主题帖、Hermes 的 22 条评论 session-resume 主题帖、QwenPaw 的 12 条评论插件契约主题帖、ZeroClaw 的上下文压缩问题簇。该品类已经从“它能做什么”跨到“我能不能让它一直跑着”。

---

## 7. 趋势信号

1. **可靠性如今是发布门禁。** 四个独立的 P0 `ux-release-blocker` 问题簇拦住了 OpenClaw 的发布列车；ZeroClaw 将安全工作标记为 `release:v0.9.0`/`release-gate`；Hermes 的稳定版 v0.22.5 发布时带有一个未解决的会话回归。*含义：达到稳定版的延迟正成为竞争指标，而不仅仅是能力。*

2. **会话/租约/准入是新的难题。** 这在三个网关型项目中都可见。具体建议：显式建模会话状态（owner、lease、TTL、generation），让唤醒**幂等**，用死信路径限制重试，并让上限*可触达*——Hermes #143720 显示一个 `_MAX_COMPLETION_*` 常量在当前活动路径中是死代码。

3. **压缩必须被实现、移除或记录——绝不能形同虚设。** ZeroClaw 静默 no-op 的配置键（#10781）和破坏性 trim（#11711），加上 QwenPaw 的硬性压缩失败（#8312），使其成为生态系统中信任修复杠杆最高的单点。应交付 token 预算核算界面，而不仅仅是一项策略。

4. **静默失败是跨项目第一号用户体验投诉**（4/4 个活跃项目）。给 agent 开发者的两个具体模式：(a) 对于被拒绝、被截断或被丢弃的操作，绝不返回成功；(b) 让拒绝**可被机器补救**——原因 + 提示（#11983）——因为自主 agent 会在不透明错误上循环。

5. **背压必须按消耗资源来设定维度。** 四个项目各自限制错了计数器：限制子进程数而非 RSS（#161050）、限制线程池槽位而非事件循环进展（#145315）、限制默认 AnyIO 限制器而非任务数（#8279）、在缓冲后而非缓冲前限制响应大小（#12045）。

6. **成本可观测性是采购标准。** 量化浪费（bootstrap 重新注入消耗 20–30% 上下文；实测 21.3M 缓存写入 token）以及对按运行时设置思考级别（ZC #12054）和按操作为记忆写入选择模型（QP #7719）的需求。预计 token 核算仪表盘将成为基本门槛。

7. **严格按规范实现的解析器会在真实环境中失败。** 每个项目至少有一个 bug 由不符合规范的 OpenAI 兼容代理或网关引起。应构建带终止事件回退的宽容流解析器。

8. **身份与最小权限正从可选转向发布门禁。** ZeroClaw 的 OIDC 栈、OpenClaw 的执行前授权防护和个人身份工作、Hermes 的 HITL 绕过修复都指向同一方向。能感知主体的工具作用域（而不只是角色检查）看起来将成为 2027 年自托管 agent 的基线要求。

9. **插件供应链是新兴攻击面。** manifest/二进制生成不匹配（#11562）、插件生成捕获阻塞网关（#160959），以及未固定版本的第三方仪表盘脚本（#12053），是同一风险的三类不同表现。应将插件准入视为两阶段提交。

10. **Windows 与桌面端对齐仍是被服务不足的市场。** OpenClaw 最主要的痛点面是 Windows 存储/更新/睡眠恢复；QwenPaw 正积极投入 `deb`/AppImage 和 Wayland portal。对于“个人”助手而言——用户的实际机器*就是*部署目标——把 Windows 当作次级平台是一个持久的竞争空白。

---

**给决策者的底线结论：** OpenClaw 是参考实现，也是唯一关闭速度快于积压速度的项目——但它被一个无人负责的 Windows/存储 P0 问题簇阻塞，其发布就绪度是生态系统近期最明确的风险（也是机会）。QwenPaw 是管理最好的中等规模项目，也是桌面/CJK 用例最强押注。Hermes 拥有最丰富的编排表面和最弱的会话层。ZeroClaw 做出了正确的长期架构押注（身份、完整性、主权），但目前受限于评审吞吐量。在架构上，生态系统已经趋同；未来两个季度的差异化因素将是：谁最先把这种趋同转化为可信赖、有界、可诊断的常开行为。

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要
**日期：** 2026-10-27 · **仓库：** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日概览

活跃度仍然很高：过去 24 小时内涉及 50 个 issue 和 50 个 PR，但合并比例偏低——45 个 PR 仍处于打开状态，仅 5 个已合并/关闭，且未发布任何版本。issue 流由单一架构主题主导：**会话身份、唤醒/准入与恢复语义**，横跨网关、cron、Discord 和桌面客户端；其中四份独立报告涉及 `admission_conflict` 拒绝循环，有一份一天内达到 2,641 次。第二组问题涉及网关与 CLI 之间的**规范 API 契约不匹配**（`data.fields` 对 `data.field`、继承的 `HERMES_SESSION_SOURCE` 标签），表明这是模式治理缺口，而非孤立缺陷。积极的一面是，维护者当天关闭了一个功能请求（#145313），三项可见 PR 已合入，社区也在提交内容充实、复现良好的报告以及自己的修复 PR。**整体健康度：参与度高、分诊吞吐量高，但会话/网关层的稳定性债务不断累积，PR 队列存在评审瓶颈。**

---

## 2. 版本发布

过去 24 小时没有新版本，也没有可供总结的版本。issue 流中报告的最新稳定版是 **v0.22.5**（多份报告引用 commit `bf0203a794` / `df722c88648a`），另有一位仍停留在旧标签上的报告者引用了 `v0.21.4`（`v2026.9.21`）（#119805）。

> 信号：今日若干影响最大的缺陷都是*针对* 0.22.5 提交的，说明当前稳定版带有未解决的会话/准入回归。一旦准入冲突问题簇找到根因，发布补丁版本会很有价值。

---

## 3. 项目进展

**今日已合并/关闭 PR：5**（所提供的样本中可见 3 个）：

| PR | 解决了什么 |
|---|---|
| [#145106](https://github.com/NousResearch/hermes-agent/pull/145106) (CLOSED) | 抢救 #144544 —— Web 应用中的纯 HTTP 发送/复制、浏览器编辑菜单、可安全重建的 chunk、图片链接、重启等待提示。已跨 522 个提交的漂移完成 cherry-pick。 |
| [#75981](https://github.com/NousResearch/hermes-agent/pull/75981) (CLOSED) | 当 `primary_path` 已归属某个项目时，`projects.create` / `project_create` 现在会拒绝创建——修复同一文件夹出现重复项目的问题。 |
| [#41043](https://github.com/NousResearch/hermes-agent/pull/41043) (CLOSED) | 当本地 HEAD 变化时，更新检查缓存现在会失效，从而消除手动 `git pull` 后最长 6 小时内仍显示过时“落后 N 个提交”横幅的问题。 |

**功能工作推进（打开的 PR，尚未合并）：**
- **编排/委派对齐：** [#145316](https://github.com/NousResearch/hermes-agent/pull/145316) 增加按 id 查询已完成的委派（持久化的 `async_delegations` 账本变得可查询，而不再只是 TUI 可见）；[#145317](https://github.com/NousResearch/hermes-agent/pull/145317) 允许编排器 agent 回收正在运行的 Kanban 卡片；[#145305](https://github.com/NousResearch/hermes-agent/pull/145305) 在 `subagent_lifecycle.result()` 中保留原生 summary/spill/usage 字段。
- **安全加固：** [#145310](https://github.com/NousResearch/hermes-agent/pull/145310) 在无 token 模式下，拒绝浏览器发起以及外部 Host 发往 A2A 监听器的请求；[#131461](https://github.com/NousResearch/hermes-agent/pull/131461) 修复两个 human-in-the-loop 审批绕过/阻塞缺陷（等待维护者 CI 批准）。
- **提供方正确性：** [#145309](https://github.com/NousResearch/hermes-agent/pull/145309) 不再将 SGLang 端点归类为 Ollama；[#104345](https://github.com/NousResearch/hermes-agent/pull/104345) 注入 Gemini `thought_signature` 哨兵，防止模型回退时出现 HTTP 400；[#145307](https://github.com/NousResearch/hermes-agent/pull/145307) 阻止视觉回退在跨供应商时携带固定模型 id。
- **平台/技能：** [#145312](https://github.com/NousResearch/hermes-agent/pull/145312)（Mattermost 线程中 @提及 后自由响应）、[#145311](https://github.com/NousResearch/hermes-agent/pull/145311)（Google Workspace `calendar freebusy`）、[#144346](https://github.com/NousResearch/hermes-agent/pull/144346)（YAML 变更时刷新桥接平台设置）、[#85041](https://github.com/NousResearch/hermes-agent/pull/85041)（本地 TTS 优化 playbook，377s→100.2s 案例研究）。

**已关闭 issue：** [#145313](https://github.com/NousResearch/hermes-agent/issues/145313) —— 插件目录的 Composio 凭据复用，当天开启并关闭。

---

## 4. 社区热门话题

| 条目 | 活跃度 | 底层需求 |
|---|---|---|
| [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — 远程后端桌面会话无法恢复；`session.resume` → `not_found` | **22 条评论**，1 👍，已打开 11 天，标记 `sweeper:risk-session-state` | 会话记录必须在创建它的进程结束后仍然存在（`source='desktop'` + 远程 `hermes serve`）。用户运行长时间存活的远程后端，并期望会话持久且可寻址。 |
| [#105758](https://github.com/NousResearch/hermes-agent/issues/105758) — 生命周期守卫误报阻止 Node.js CLI | **15 条评论**，`needs-decision`，已打开 7 周 | “此命令能否重启网关？”的启发式判断过宽，会破坏合法工具（`obsidian-headless`）。需要策略决策，因此停滞。 |
| [#142461](https://github.com/NousResearch/hermes-agent/issues/142461) — 自动化唤醒进入自动创建的 Discord 线程后被永远拒绝，报 `admission_conflict` | **10 条评论，6 👍**（今日最高反应数） | 父会话在子会话完成后从不恢复；用户希望自动化唤醒是*幂等且最终被准入*的，而不是永远被静默重新入队。 |
| [#133212](https://github.com/NousResearch/hermes-agent/issues/133212) — `subagent_lifecycle` v1.1：steer、主机隔离传输、effort/route/follow-up | **8 条评论**，`needs-decision` | 插件作者需要让 `ctx.subagent_lifecycle` 在 `plugins.isolation: host` 下与 `delegate_task` 达到同等能力。 |
| [#143720](https://github.com/NousResearch/hermes-agent/issues/143720) — 完成唤醒每 60 秒被拒绝一次且永远如此；尝试次数上限从未触发 | **6 条评论** | `_MAX_COMPLETION_*` 上限在此路径中是死代码；`notify-mode=off` 无效。 |
| [#141878](https://github.com/NousResearch/hermes-agent/issues/141878) — 登录门控仪表盘上的跨进程插件激活 401 | **5 条评论** | 进程 token 与经提供方认证的会话被混为一谈；失败被静默吞掉。 |

**分析。** 反应数领先者（#142461，6 👍）和评论数最高的两个线程（#139818、#105758）指向同一个元需求：**Hermes 的会话/唤醒层缺乏清晰归属和有界重试语义。** 用户不是在要求新的功能面，而是在要求现有功能面大声失败、有限重试并能在重启后存活。一份关于“准入控制契约”（谁准入、何时退避、何时丢弃）的设计说明，很可能一次性解决其中多个问题。

---

## 5. 缺陷与稳定性

今日窗口内没有 issue 被标记为 **P1**，但若干 **P2** 项具有崩溃循环或消息丢失的影响范围。

### 严重（崩溃循环 / 死锁 / 数据面停滞）
1. **[#144560](https://github.com/NousResearch/hermes-agent/issues/14560)** *(P2, gateway/webhook)* — 当 `aiohttp` 缺失时，网关会**陷入崩溃循环**，因为 `-> web.Response` 注解会在导入时求值（66 处，ruff UP037）。降级环境变成硬失败，而不是优雅跳过适配器。**未见修复 PR。**
2. **[#145315](https://github.com/NousResearch/hermes-agent/issues/145315)** *(P2，今日新增)* — Webhook `cron_job` 路由在同时触发的运行数达到默认线程池线程数后，会**使整个网关死锁**：`asyncio.to_thread` 占用一个线程，而该线程随后无限等待所属事件循环。干净、可复现、架构性问题。**未见修复 PR。**
3. **[#145292](https://github.com/NousResearch/hermes-agent/issues/145292)** *(P2, MCP)* — 永不完成的传输拆除会让 `_serve_session` 的运行循环卡住；**MCP 工具永远不会恢复**。需要在 `ClientSession`/`transport_cm` 退出时设置超时。
4. **[#145308](https://github.com/NousResearch/hermes-agent/issues/145308)** *(P2, gateway/sessions)* — 在由 `message_agent` 启动的回合中，`clarify` **从不渲染，却持有会话租约**，导致 `restart --yes` 等待它而挂起。

### 高（静默失败 / 消息丢失 / 认证）
5. **准入冲突拒绝循环** — [#145261](https://github.com/NousResearch/hermes-agent/issues/145261)（**仅 2026-10-26 就有 2,641 次拒绝**，多路复用 profile + Discord 群组会话）、[#143720](https://github.com/NousResearch/hermes-agent/issues/143720)（单个日志中 494 次，6 小时后仍在触发）、[#142461](https://github.com/NousResearch/hermes-agent/issues/142461)。三个独立环境出现相同症状 → 系统性问题，而非用户配置错误。
6. **[#139818](https://github.com/NousResearch/hermes-agent/issues/139818)** *(P2, `sweeper:risk-session-state`)* — 一旦托管进程消失，远程后端桌面会话的 `session.resume` → `not_found`。
7. **[#145275](https://github.com/NousResearch/hermes-agent/issues/145275)** *(P2, auth)* — 对于 `transport: anthropic_messages`，`key_cmd` 始终以 `Authorization: Bearer` 发送，从不使用 `x-api-key`，因此无法提供 Anthropic Console 密钥。
8. **[#145063](https://github.com/NousResearch/hermes-agent/issues/145063)** *(P2, tools)* — 对于带 `anyOf` 分支或可选 payload 的参数，工具调用参数会**丢失字段 / 选错动作**（在 `deepseek-v4.1-flash` 上观察到）。依赖模型的静默损坏。
9. **[#145286](https://github.com/NousResearch/hermes-agent/issues/145286)** + **[#143653](https://github.com/NousResearch/hermes-agent/issues/143653)** *(P2, CLI/sessions)* — 规范 `session.create` 拒绝被错误渲染：网关发送 `data.fields`，CLI 读取 `data.field`，因此精确的策略拒绝显示为*“无法启动模型连接。”* #143653 是具体触发点：继承来的 `HERMES_SESSION_SOURCE=kanban|automation` 标签被规范 `Literal` 拒绝。

### 中
10. **[#145314](https://github.com/NousResearch/hermes-agent/issues/145314)** *(P2，新增)* — 在非 `no_agent` 路径上，静默 cron 脚本每次触发都会留下一个**永久空会话**。
11. **[#123923](https://github.com/NousResearch/hermes-agent/issues/123923)** *(P2)* — Anthropic 400 *“thinking blocks cannot be modified”*：心跳续写会把 thinking block 重复放入同一条 assistant 消息。
12. **[#119529](https://github.com/NousResearch/hermes-agent/issues/119529)** *(P2，重复标签)* — 全新安装中 `web_extract` 总是失败：自动检测选中了仅支持搜索的 `ddgs`。高频新手引导小痛点。
13. **[#119805](https://github.com/NousResearch/hermes-agent/issues/119805)** *(P2, qqbot)* — 重启后群组发送会路由到 `/v2/users/<group_openid>/messages` → `400 用户/群已注销`，直到该群有入站流量为止。
14. **[#144438](https://github.com/NousResearch/hermes-agent/issues/144438)** *(P2, ACP)* — 网关模式下 `session/list` 为空：编辑器聊天存储为 `source='local'`，但只按 `source='acp'` 列出。影响 Zed、VS Code、JetBrains、Obsidian。
15. **[#145298](https://github.com/NousResearch/hermes-agent/issues/145298)** *(P2, desktop)* — 在消息平台会话中拖放文件总是 `permission_denied`（`file.attach` 需要 LOCAL 策略），但拖放入口仍保持启用。
16. **[#143845](https://github.com/NousResearch/hermes-agent/issues/143845)** *(P2)* — 在仪表盘中删除已完成的 cron 会话 → 404，而该行仍然存在；用 CLI 删除同一 id 则成功。

### 低（P3，打磨/文档）
17. [#140633](https://github.com/NousResearch/hermes-agent/issues/140633) — NVIDIA SwiftShader 下 Wayland/HDR UI 发白（`cause/upstream`）。
18. [#145282](https://github.com/NousResearch/hermes-agent/issues/145282) — GNOME 锁定/解锁后会永久移除托盘图标。
19. 技能文档缺陷：[#145304](https://github.com/NousResearch/hermes-agent/issues/145304)（`ascii-video` 中未定义的 `colors`/`resize`，纯色帧上出现 0/0）、[#145306](https://github.com/NousResearch/hermes-agent/issues/145306)（编写指南让用户执行 `git checkout --` 并丢弃工作）、[#145276](https://github.com/NousResearch/hermes-agent/issues/145276)（`manim-video` 将远程 TTS 误标为本地）。

**今日修复 PR 对本列表顶部的覆盖较弱。** 已有针对视觉回退（#145307）、SGLang 检测（#145309）、Gemini 回退（#104345）、审批（#131461）、网关配置刷新（#144346）的 PR——但**未见针对准入冲突问题簇（#145261/#143720/#142461）、aiohttp 崩溃循环（#144560）或 webhook cron 死锁（#145315）的修复 PR。** 这三者代表了当前稳定版本线中最大的稳定性风险。

---

## 6. 功能请求与路线图信号

| 请求 | 状态 | 下一版本可能性 |
|---|---|---|
| [Subagent lifecycle v1.1](https://github.com/NousResearch/hermes-agent/issues/133212) — steer、主机隔离传输、effort/route/follow-up | 打开，8 条评论，`needs-decision`，关联同一作者的 PR [#145305](https://github.com/NousResearch/hermes-agent/pull/145305)/[#145316](https://github.com/NousResearch/hermes-agent/pull/145316) | **高**——报告者持续提交增量 PR；能力对齐缺口阐述得很清楚 |
| [Kanban 通知订阅](https://github.com/NousResearch/hermes-agent/issues/136614) — 选择哪些事件类型唤醒 agent | 打开，P3，`sweeper:risk-message-delivery` | **中高**——有强有力的量化动机（每周 681 次唤醒回合，其中 547 次可避免），且 PR #145317 已在扩展 Kanban 编排 |
| 按 id 查询委派结果 | PR [#145316](https://github.com/NousResearch/hermes-agent/pull/145316) | **中**——功能面小且受控 |
| A2A 无 token 模式加固 | PR [#145310](https://github.com/NousResearch/hermes-agent/pull/145310) | **中高**——带安全标签，diff 小 |
| Mattermost 线程自由响应（`MATTERMOST_THREAD_FREE_RESPONSE`） | PR [#145312](https://github.com/NousResearch/hermes-agent/pull/145312) | **中**——由配置门控且默认关闭，风险低 |
| Google Workspace `calendar freebusy` | PR [#145311](https://github.com/NousResearch/hermes-agent/pull/145311) | **高**——仅技能改动，从现有上游 PR 移植 |
| Composio 目录凭据复用 | [#145313](https://github.com/NousResearch/hermes-agent/issues/145313) 当天关闭 | 已解决/已重定向——按维护事项处理 |

**方向性判断：** 路线图正被 *agent 编排*（Kanban、委派、子代理生命周期）和 *插件/平台广度*（Mattermost、Google Workspace、A2A）拉动。值得注意的是，功能队列中没有任何重新设计会话准入/重试的提案——这是一个值得标记的缺口，因为缺陷大量集中在那里。

---

## 7. 用户反馈摘要

**按数量排序的痛点：**
1. **静默、无帮助的失败。** 三份独立报告描述了相同体验：一次变更被拒绝，UI 显示通用文案，而重试永不停止。来自 [#145286](https://github.com/NousResearch/hermes-agent/issues/145286)：一个精确指明出错字段的 `invalid_params` 却显示为*“Hermes 无法启动模型连接。”* 来自 [#141878](https://github.com/NousResearch/hermes-agent/issues/141878)：插件激活时的 401 被*静默丢弃*。来自 [#145298](https://github.com/NousResearch/hermes-agent/issues/145298)：`permission_denied` 看起来像操作系统缺陷，因为拖放入口仍保持启用。用户明确要求错误文案保真，而不仅仅是正确性。
2. **没有上限的重试风暴。** [#145261](https://github.com/NousResearch/hermes-agent/issues/145261)（2,641 次拒绝/天）、[#143720](https://github.com/NousResearch/hermes-agent/issues/143720)（尝试次数上限从未触发）、[#142461](https://github.com/NousResearch/hermes-agent/issues/142461)。填满日志、消耗配额，并掩盖真实失败。
3. **部署形态假设。** 自托管者运行远程 `hermes serve` + 桌面客户端 + 多个 profile（#139818、#145261），外加 ACP 查看器（#144438），以及没有 systemd 的 WSL2（#119805）。若干缺陷只在这些“常开/多路复用”拓扑中出现，说明顺利路径是单进程本地运行。
4. **对捆绑内容的信任。** #145276/#145304/#145306 这一组——均由同一位报告者在检查 `skills/` 引用时提交——表明用户会*阅读并运行*捆绑示例，而错误示例（0/0 除法、`git checkout --` 破坏工作）会被视为产品缺陷，其中一个还打了 `area/i18n` 标签。
5. **合法自动化被当作敌意行为。** #105758：一个旨在防止自我终止的生命周期守卫阻止了无害的 Node.js CLI。`needs-decision` 标签表明这是一个策略问题，而它现已搁置 7 周、有 15 条评论。

**满意信号：** 报告篇幅长、埋点充分（日志计数、提交、复现），且常包含根因分析——这表明用户群体投入且技术成熟。多位用户提交了自己的修复 PR（`100yenadmin`、`unsupportedpastels`、`arimu1`、`teknium1`），且有一个 issue 当天解决。

---

## 8. 积压观察

| 条目 | 已存在时长 | 为什么需要关注 |
|---|---|---|
| [#105758](https://github.com/NousResearch/hermes-agent/issues/105758) — 生命周期守卫误报 | **7 周**，15 条评论，`needs-decision` | 在未与新鲜回归关联的*未解决* issue 中评论数最高。阻塞真实 Node.js CLI 工作流；决策被推迟。 |
| [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — 远程桌面会话无法恢复 | **11 天**，22 条评论，`area/auth` + `sweeper:risk-session-state` | 今日评论最多的 issue，没有关联修复 PR。会话状态风险标签表明已知脆弱性。 |
| [PR #31734](https://github.com/NousResearch/hermes-agent/pull/31734) — 将系统预填充并入主系统提示 | **自 2026-05-25 起打开（约 5 个月）** | 修复因多条 `role: system` 消息导致的严格提供方拒绝。带有三个 sweeper 风险标签；可能需要兼容性决策而非更多代码。 |
| [PR #62565](https://github.com/NousResearch/hermes-agent/pull/62565) — Feishu 旧版 websocket kwargs | **自 2026-07-11 起打开** | 小型兼容 shim；`cause/upstream` + `blast-moderate`。低风险可合并候选。 |
| [PR #85041](https://github.com/NousResearch/hermes-agent/pull/85041) — 本地 TTS 优化技能 | **自 2026-08-13 起打开** | 仅文档/技能，带生产基准（377s→100.2s）。零运行时风险；纯粹因评审带宽受阻。 |
| [PR #94983](https://github.com/NousResearch/hermes-agent/pull/94983) — `checkpoint_required` 下的压缩权威 | **自 2026-08-25 起打开** | 关闭剩余的有损转录路径；`sweeper:risk-session-state` + `area/compression`。这是 #94639 的*那个*后续，且触及持久数据——需要负责人。 |
| [PR #131461](https://github.com/NousResearch/hermes-agent/pull/131461) — 审批绕过/阻塞修复 | **自 2026-10-02 起打开**，**CI `action_required`，运行 0 项检查** | 与安全相关（human-in-the-loop 绕过），但**没有维护者点击批准就无法合入**。纯粹的流程阻塞。 |
| [#119529](https://github.com/NousResearch/hermes-agent/issues/119529) / [#119805](https://github.com/NousResearch/hermes-agent/issues/119805) | 自 2026-09-22 / 09-23 起打开 | 两者都涉及全新安装或重启流程——高频用户路径，各自只有 4 条评论，有被更新的准入冲突噪音淹没的风险。 |

**给维护者的结构性观察：** 过去 24 小时有 45 个打开的 PR 对 5 个已合并 PR，且至少有四个超过 60 天、携带低风险 diff 的 PR（#85041、#62565、部分 #31734），瓶颈是**评审能力，而非贡献者供给**。反过来说，当日最高严重度的缺陷（#144560、#145315、准入冲突问题簇）**完全没有 PR**——这表明分诊跟得上，但深入的网关/会话工作相对于涌入的报告而言资源不足。

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目简报 — 2026-10-27

## 1. 今日概览

QwenPaw 正处于高速维护阶段：过去 24 小时内涉及 24 个 issue 和 48 个 PR，其中 15 个 PR 已合并/关闭、7 个 issue 已关闭，同时仍有 33 个开放 PR 和 17 个活跃 issue。本窗口未发布新版本，因此修复正在累积，面向下一个 2.2.x/2.3 构建。活动集中在提供方兼容性（OpenAI Responses 流式传输）、MCP 协议协商、记忆/嵌入正确性以及桌面平台功能对齐。issue 构成偏向稳定性而非新能力——若干高严重性报告（LLM 流无限期挂起、上下文压缩失败）仍无修复 PR，尽管吞吐强劲，这仍是项目健康的主要风险。

## 2. 发布

本窗口无新版本发布（`Latest Releases: None`）。用户引用的最新现网版本为 2.2.2、2.2.2.post1 和 2.2.3b1，表明在下一个稳定版发布前，beta 渠道处于活跃状态。

## 3. 项目进展

**已关闭/合并的 PR（前 20 个样本中可见）：**

| PR | 变更 | 影响 |
|---|---|---|
| [#8165](https://github.com/agentscope-ai/QwenPaw/pull/8165) | `fix(providers)`：恢复仅在终止事件 `response.completed` 上发送的 Responses 输出 | 修复 [#8162](https://github.com/agentscope-ai/QwenPaw/issues/8162) — 在省略内容增量的提供方上，会话不再于 1–3 步后中止 |
| [#8062](https://github.com/agentscope-ai/QwenPaw/pull/8062) | `fix(memory)`：当一个分块超出限制时保留健康的嵌入向量 | 对 [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) 的部分修复；批处理失败时按条目回退 |
| [#7923](https://github.com/agentscope-ai/QwenPaw/pull/7923) | `feat(scroll)`：通过 `blocks_retention_days` 逐步淘汰 `tool_result` 块 | 解决 `history.db` 无界增长（约占 976 MB 存储的 75%）；同名替代 PR [#8443](https://github.com/agentscope-ai/QwenPaw/pull/8443) 已开放，表明最终合并前需要返工 |

**关闭的 issue：** [#8162](https://github.com/agentscope-ai/QwenPaw/issues/8162)（Responses API 空流）、[#7476](https://github.com/agentscope-ai/QwenPaw/issues/7476)（`misfire_grace` 内 cron 重复触发）、[#8270](https://github.com/agentscope-ai/QwenPaw/issues/8270)（Windows ZIP 文件名乱码）、[#4946](https://github.com/agentscope-ai/QwenPaw/issues/4946)（插件 `ImportError` 阻塞启动 6 分钟以上）、[#8429](https://github.com/agentscope-ai/QwenPaw/issues/8429)（已被 [#8439](https://github.com/agentscope-ai/QwenPaw/issues/8439) 取代）、[#8286](https://github.com/agentscope-ai/QwenPaw/issues/8286)（thinking-strength 滑块渲染）、[#8333](https://github.com/agentscope-ai/QwenPaw/issues/8333)（侧边栏分组标题重叠）。

**推进中的开放 PR：** MCP 旧版回退 [#8445](https://github.com/agentscope-ai/QwenPaw/pull/8445)、任务取消泄漏 [#8441](https://github.com/agentscope-ai/QwenPaw/pull/8441)、原子写入文件名边界 [#8446](https://github.com/agentscope-ai/QwenPaw/pull/8446)、Linux `deb`/AppImage 打包 [#8444](https://github.com/agentscope-ai/QwenPaw/pull/8444)、嵌套技能目录 [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178)、渠道插件抽取 [#8187](https://github.com/agentscope-ai/QwenPaw/pull/8187)、ChatGPT 订阅登录 [#8437](https://github.com/agentscope-ai/QwenPaw/pull/8437)、PowerContext 后端 [#8376](https://github.com/agentscope-ai/QwenPaw/pull/8376)、本地 Whisper 插件 [#8431](https://github.com/agentscope-ai/QwenPaw/pull/8431)、PawApp SDK 重构 [#7874](https://github.com/agentscope-ai/QwenPaw/pull/7874)。

## 4. 社区热门话题

所有条目均显示 0 👍，因此互动由评论驱动；下方评论数来自 issue 集合（PR 计数未在本快照中暴露）。

| 条目 | 评论数 | 潜在需求 |
|---|---|---|
| [#8071](https://github.com/agentscope-ai/QwenPaw/issues/8071) 面向插件的主题扩展点 | 12 | 插件作者需要与内置 Console 主题能力对齐（语义 token 覆盖），而不仅是 `colorPrimary`。这表明插件 API 是生态瓶颈。 |
| [#8162](https://github.com/agentscope-ai/QwenPaw/issues/8162) Responses API 空流（已关闭） | 8 | 异构网关/代理不遵循增量事件契约；用户需要容错型流式解析器。 |
| [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) 嵌入重建索引静默丢弃一批数据 | 6 | 记忆索引中的静默数据丢失，具体是超过单条 token 限制的 CJK 分块。长期记忆的信任问题。 |
| [#7476](https://github.com/agentscope-ai/QwenPaw/issues/7476) Cron 重复调度（已关闭） | 5 | 调度器的恰好一次语义；重复的备份脚本是生产完整性问题。 |
| [#8270](https://github.com/agentscope-ai/QwenPaw/issues/8270) Windows ZIP 乱码（已关闭） | 4 | 通过 `ZipFile` 处理 CJK/旧编码归档时未使用 `encoding=`；本地化质量。 |
| [#8312](https://github.com/agentscope-ai/QwenPaw/issues/8312) / [#8424](https://github.com/agentscope-ai/QwenPaw/issues/8424) / [#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276) / [#8279](https://github.com/agentscope-ai/QwenPaw/issues/8279) / [#8405](https://github.com/agentscope-ai/QwenPaw/issues/8405) | 各 3 | 上下文窗口管理、MCP 旧版互操作、重排器配置校验、AnyIO 线程饥饿、Console 模态框生命周期。 |

**分析：** 三大主导需求信号是：(a) 更深、更稳定的插件契约；(b) 对不符合规范的模型/MCP 服务器进行防御性处理；(c) 可信的记忆与调度——也就是说，用户正推动 QwenPaw 从“演示型 agent”走向始终在线的基础设施。

## 5. 缺陷与稳定性

按严重性排序；“修复 PR”表示存在可见的开放/已关闭 PR 正在处理该问题。

| 严重性 | 条目 | 症状 | 修复 PR |
|---|---|---|---|
| 严重 | [#8435](https://github.com/agentscope-ai/QwenPaw/issues/8435) | LLM 流无限期挂起且零日志；限流器信号量从不释放，看门狗从不触发（v2.2.2 Docker） | 未见 |
| 严重 | [#8312](https://github.com/agentscope-ai/QwenPaw/issues/8312) | 上下文压缩无法装入请求（252,565 > 251,904 tokens）→ 任务中断（2.2.2） | 未见 |
| 严重 | [#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417) | `/compact` 在 Volcengine AgentPlan `glm-5.3-flash` 上稳定返回 400；兼容层同时发送两个 `thinking` 禁用参数 | 未见 |
| 高 | [#8279](https://github.com/agentscope-ai/QwenPaw/issues/8279) | 长生命周期的 ReMe watcher 耗尽 AnyIO 默认线程限制器，导致同步 HTTP 端点停滞 | 未见 |
| 高 | [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) | 一个超限的 CJK 分块会静默丢弃其整个嵌入批次；循环仍记录 `processed=126/126` | 部分：[#8062](https://github.com/agentscope-ai/QwenPaw/pull/8062) 已关闭 |
| 高 | [#8440](https://github.com/agentscope-ai/QwenPaw/issues/8440) | `cancellable_wait` 在外层取消后仍让所属工具任务继续运行（2.2.3b1，`main`） | [#8441](https://github.com/agentscope-ai/QwenPaw/pull/8441) 开放 |
| 高 | [#8424](https://github.com/agentscope-ai/QwenPaw/issues/8424) | `server/discover` 404 + `-32601` 使 `_negotiate()` 硬失败，阻塞旧版 MCP 回退 | [#8445](https://github.com/agentscope-ai/QwenPaw/pull/8445) 开放 |
| 高 | [#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415) | `edit_file` 将所有 CRLF 转换为 LF，并把有损的 ISO-8859-1 快照重新编码为 UTF-8，却报告成功 | 未见 |
| 高 | [#8425](https://github.com/agentscope-ai/QwenPaw/issues/8425) | 当 `name` 保存完整地址时，邮件监控器构造出 `name@domain@domain` → 升级后出现 `AUTHENTICATIONFAILED` | 未见 |
| 中 | [#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276) | 重排器启用时接受空 `api_key`；仅表现为“unexpected error” | 未见 |
| 中 | [#8405](https://github.com/agentscope-ai/QwenPaw/issues/8405) | 当传输类型为 http/sse 时，MCP 配置模态框无法关闭（stdio 正常） | 未见 |
| 中 | [#8123](https://github.com/agentscope-ai/QwenPaw/issues/8123) | Daily Paper 在模型输出被截断时把整个任务标记为失败；没有按论文重试 | 未见 |

**今日新增（2026-10-26/27）：** #8440、#8435、#8424、#8425、#8420、#8439、#8442。注意反复出现的*静默*失败模式——#8040、#8276、#8435、#8415 都报告成功或完全不输出日志，却产生错误/挂起状态。这是系统性的可观测性缺口，而非四个孤立缺陷。

## 6. 功能请求与路线图信号

| 请求 | 信号 | 可能时间 |
|---|---|---|
| [#8071](https://github.com/agentscope-ai/QwenPaw/issues/8071) 插件主题扩展（语义 token 覆盖） | 12 条评论，自 10 月 1 日起开放——讨论最多的条目 | 若 API 达成一致，下一个次要版本（2.3.x） |
| [#8442](https://github.com/agentscope-ai/QwenPaw/issues/8442) Linux 桌面功能对齐：`deb` + AppImage、通过 Wayland portal 使用 Computer Use、Chrome Native Messaging | PR [#8444](https://github.com/agentscope-ai/QwenPaw/pull/8444) 已开放 | 2.3.x；Computer Use/Wayland 可能更晚 |
| [#8439](https://github.com/agentscope-ai/QwenPaw/issues/8439) 在 cron `JobRuntimeSpec` 中加入 `thinking_level` | 在 #8429 以“稍后审查”关闭后重新提交 | 可能下一个补丁/次要版本；影响面小 |
| [#8420](https://github.com/agentscope-ai/QwenPaw/issues/8420) 文件面板“用默认应用打开”/“在 Finder/Explorer 中显示” | 新增，桌面 UX 缺口 | 适合补丁 |
| [#7912](https://github.com/agentscope-ai/QwenPaw/issues/7912) 带认证的 MCP 网络研究文档示例 | 社区贡献的文档 | 仅文档，近期 |
| [#8437](https://github.com/agentscope-ai/QwenPaw/pull/8437) ChatGPT 订阅登录（OAuth PKCE） | XXXL PR | 2.3.x——认证相关面需要审查 |
| [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) 嵌套/分类技能目录 | XL，首次贡献者，审查中 | 2.3.x |
| [#8187](https://github.com/agentscope-ai/QwenPaw/pull/8187) 渠道作为插件子进程 | XXXL | 跨多个版本的工作 |
| [#7719](https://github.com/agentscope-ai/QwenPaw/pull/7719) 为 ReMeLight 记忆写入使用独立模型 | M，自 9 月 12 日起开放 | 成本控制诉求；可能 2.3.x |

**预测：** 下一个补丁（2.2.3）将仅聚焦稳定性——MCP 回退（#8445）、取消泄漏（#8441）、原子文件名边界（#8446）、scroll 块保留（#8443），外加已关闭的 Responses 修复。2.3.0 正逐渐成形为“平台”版本：Linux 桌面、插件主题、嵌套技能、ChatGPT 登录、渠道隔离。

## 7. 用户反馈摘要

**按数量排序的痛点：**
- **提供方/网关兼容性（呼声最高）：** 不符合规范的 OpenAI Responses 流（[#8162](https://github.com/agentscope-ai/QwenPaw/issues/8162)）、Volcengine `glm-5.3-flash` 拒绝两个 thinking 禁用参数（[#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417)）。用户运行异构的国内外网关；严格遵循规范反而会使其失效。
- **长期记忆信任：** 静默批次丢弃（[#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040)）、重排器校验薄弱（[#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276)）、长时间运行部署中的 AnyIO 耗尽（[#8279](https://github.com/agentscope-ai/QwenPaw/issues/8279)）。
- **上下文管理：** 压缩直接失败（[#8312](https://github.com/agentscope-ai/QwenPaw/issues/8312)、[#8417](https://github.com/agentscope-ai/QwenPaw/issues/8417)）——用户遭遇硬性任务中断，而非优雅降级。
- **本地化/数据完整性：** Windows ZIP 乱码（[#8270](https://github.com/agentscope-ai/QwenPaw/issues/8270)）、CRLF 重新编码（[#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415)）——两者都尤其影响 CJK 与混合编码工作流。
- **升级回归：** 升级后邮件监控认证中断（[#8425](https://github.com/agentscope-ai/QwenPaw/issues/8425)）——配置迁移未处理已存在的完整地址 `name` 字段。

**满意信号：** 对于范围明确的报告，处理速度确实很快——#8162 于 10 月 10 日提交，其修复 PR 同日落地，并于 10 月 26 日关闭；#8270 和 #4946 也在本窗口关闭。用户显然正将 QwenPaw 作为生产基础设施部署（976 MB 历史数据库、Docker 部署、每日备份 cron），这是强烈的成熟度信号。

**不满意信号：** “零日志”挂起（[#8435](https://github.com/agentscope-ai/QwenPaw/issues/8435)）、误导性的“unexpected error”提示（[#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276)），以及有损编辑却报告“Successfully replaced text”（[#8415](https://github.com/agentscope-ai/QwenPaw/issues/8415)）。用户的抱怨与其说是缺少功能，不如说是 agent 失败时的*可诊断性*。

## 8. 待办积压观察

| 条目 | 存续时间 | 为何需要关注 |
|---|---|---|
| [#7835](https://github.com/agentscope-ai/QwenPaw/pull/7835) `fix(memory)`：停止将自动记忆召回载荷泄漏到渠道 | 自 2026-09-17 起开放，审查中 | 隐私/数据暴露问题：合成记忆轨迹会到达 Feishu/QQ 及其他出站渠道。积压中优先级最高的未审查 PR。 |
| [#7719](https://github.com/agentscope-ai/QwenPaw/pull/7719) 为 ReMeLight 记忆写入使用独立模型 | 自 2026-09-12 起开放 | 直接的成本控制请求，未见维护者回应。 |
| [#7874](https://github.com/agentscope-ai/QwenPaw/pull/7874) PawApp SDK/控制面重构 | 自 2026-09-19 起开放，XXXL | 架构性 PR，无评论活动——存在腐化和贡献者流失风险。 |
| [#8033](https://github.com/agentscope-ai/QwenPaw/pull/8033)、[#8027](https://github.com/agentscope-ai/QwenPaw/pull/8027)、[#8034](https://github.com/agentscope-ai/QwenPaw/pull/8034) | 自 2026-09-29 起开放 | 桌面后端协调、在异步处理器中执行阻塞式 FS 操作、内联媒体正文边界——均为小/中型改动，全部停滞。 |
| [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) 嵌入重建索引不完整 | 自 2026-09-30 起开放 | #8062 仅处理了“按条目回退”这一半；CJK token 预算截断的根因仍在。是 #5950 的复发。 |
| [#7912](https://github.com/agentscope-ai/QwenPaw/issues/7912) MCP 文档示例 | 自 2026-09-21 起开放 | 有贡献者主动提供文档工作；尚未获得维护者批准。 |
| [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) 嵌套技能目录 | 自 2026-10-11 起开放，首次贡献者 | 需要及时审查，以留住一位参与 XL 改动的新贡献者。 |
| [#8123](https://github.com/agentscope-ai/QwenPaw/issues/8123) Daily Paper 截断失败 | 自 2026-10-08 起开放 | 没有按论文重试；一次糟糕的模型输出就会使整个 cron 任务失败。 |

**健康度说明：** 刚刚关闭的 [#4946](https://github.com/agentscope-ai/QwenPaw/issues/4946) 自 2026-06-03 起一直开放——提醒人们，高严重性的启动阻塞问题可能滞留数月。当前 24 小时 PR 吞吐量健康，但*审查*队列（可见 17 个开放 PR，其中若干为 XL/XXXL）才是真正瓶颈，尤其是 [#7835](https://github.com/agentscope-ai/QwenPaw/pull/7835) 因隐私原因应优先处理。

---

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-10-27

## 1. 今日概览

ZeroClaw 在过去 24 小时内记录到较高的流入活动，但几乎没有关闭事项：23 个 issue 被更新（23 个仍全部打开，0 个关闭），29 个 PR 被触及（28 个打开，仅 1 个已合并/关闭）。当天活动主要由一轮协同进行的安全/身份访问排查主导——同一报告者在 2026-10-26 提交了八个 issue（#12046–#12053），覆盖网关认证、OIDC 流程、工具作用域和 URL 抓取。第二个集群涉及上下文/token 预算与记忆正确性（#10780、#10781、#11711、#10921），这已成为该项目一个多月以来最持续的主题。没有发布版本，但 v0.8.7 候选版本准备工作（#12019 将翻译目录固定到 v0.8.7）和标记为 v0.9.0 的仪表盘登录工作明显正在进行中。总体而言，项目在贡献量上健康，但在评审/合并吞吐上受限，导致开放 issue 和开放 PR 积压不断扩大。

## 2. 项目进展

窗口期内，**29 个有更新的 PR 中只有 1 个被合并/关闭**，且该 PR 无法从提供的 top-20 样本中识别；实际上今天没有值得注意的功能落地。以下项目*有所推进*（已更新，未合并）：

| 领域 | PR | 状态/信号 |
|---|---|---|
| 仪表盘 OIDC 登录（v0.9.0） | [#11965](https://github.com/zeroclaw-labs/zeroclaw/pull/11965) | 可选择加入的 `[oidc.<alias>]` 仪表盘登录；将仪表盘从共享配对码操作员迁移到真实身份 |
| 带作用域的仪表盘聊天（v0.9.0） | [#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986) | 叠加在 #11965/#11186/#11964 之上；标记为 `needs-author-action` |
| OIDC 注册正确性 | [#11968](https://github.com/zeroclaw-labs/zeroclaw/pull/11968) | 按 RFC 6749 §2.3.1 对 Basic 凭据进行表单编码 |
| 运行时放置文档 | [#11963](https://github.com/zeroclaw-labs/zeroclaw/pull/11963) | 在 v0.9.0 登录/RPC 变更之前提出五行例外 |
| RPC 授权加固 | [#11289](https://github.com/zeroclaw-labs/zeroclaw/pull/11289), [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326), [#11234](https://github.com/zeroclaw-labs/zeroclaw/pull/11234), [#11230](https://github.com/zeroclaw-labs/zeroclaw/pull/11230) | 稳定的拒绝原因、日志读取门控、排队会话归属、会话数据工具暂不提供——均为 `release-gate`/`risk:high` |
| 运行时上下文控制 | [#11910](https://github.com/zeroclaw-labs/zeroclaw/pull/11910), [#12044](https://github.com/zeroclaw-labs/zeroclaw/pull/12044) | 硬性 `max_system_prompt_chars` 门控；可选择加入的延迟引导区块 |
| 渠道 | [#11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847)（XMPP，XL）、[#12003](https://github.com/zeroclaw-labs/zeroclaw/pull/12003)（仅入站电子邮件）、[#11651](https://github.com/zeroclaw-labs/zeroclaw/pull/11651)、[#11601](https://github.com/zeroclaw-labs/zeroclaw/pull/11601)（Telegram 429/转录） |
| 插件完整性 | [#12040](https://github.com/zeroclaw-labs/zeroclaw/pull/12040) | 修复 #11562 的清单/组件生成配对问题 |
| Web UI | [#12039](https://github.com/zeroclaw-labs/zeroclaw/pull/12039) | 修复 #12021 陈旧的“未保存更改”高亮 |
| Goal V1 提案 | [#11880](https://github.com/zeroclaw-labs/zeroclaw/pull/11880)（3/12）、[#11882](https://github.com/zeroclaw-labs/zeroclaw/pull/11882)（5/12） | 明确为草稿：“final review: FAIL”，不是合并请求 |

## 3. 社区热点

**Issue（按评论数排序；数据集中 PR 评论数为 `undefined`，因此 PR 按风险/规模/标签排序）：**

1. **上下文/token 经济学——占主导的集群（每个 4 条评论）**
   - [#10780](https://github.com/zeroclaw-labs/zeroclaw/issues/10780) — 主动式 token 预算上下文压缩被移除；`keep_recent`/`collapse_tool_results` 失效。`priority:p1`、`status:in-progress`、`risk:high`。
   - [#10781](https://github.com/zeroclaw-labs/zeroclaw/issues/10781) — 移除或实现失效的 `context_compression.*`、`history_pruning.keep_recent`、`collapse_tool_results`、`keep_tool_context_turns`。
   - [#11711](https://github.com/zeroclaw-labs/zeroclaw/issues/11711) — 裁剪长对话会*永久删除* SQLite 会话存储中的较早轮次。
   *底层需求：* 运维人员需要为长时间运行的 agent 提供可预测、有界的上下文成本，并且需要裁剪是非破坏性的。配置键静默无操作（#10781）这一事实是信任问题，而不仅仅是文档缺陷。

2. **持久化轮次的传输一致性** — [#10673](https://github.com/zeroclaw-labs/zeroclaw/issues/10673)（4 条评论，P1/S1）。失败/取消的 ACP 轮次会为 `session/prompt` 持久化，但不会为 ZeroCode 的 Code 窗格所使用的 daemon RPC 路径持久化；这是 #9333 的剩余部分。*需求：* 无论客户端/传输如何，都提供相同的可靠性保证。

3. **长期记忆召回正确性** — [#10921](https://github.com/zeroclaw-labs/zeroclaw/issues/10921)（4 条评论）。Qdrant `recall`/`recall_for_agents` 将 `limit` 传给 `/points/search` 时未带 `since`/`until`，因此有时间边界的召回可能返回少于符合条件的结果。*需求：* 时间过滤器必须作为搜索的一部分，而不是后置过滤。

4. **插件供应链完整性** — [#11562](https://github.com/zeroclaw-labs/zeroclaw/issues/11562)（3 条评论，`risk:high`、`release:v0.8.7`）。在读取清单与组件准入之间发生的一次 `plugin update` 发布，可能将某一代的清单与另一代的二进制配对。修复 PR [#12040](https://github.com/zeroclaw-labs/zeroclaw/pull/12040) 已打开。

5. **协调跟踪器与 UX** — [#11013](https://github.com/zeroclaw-labs/zeroclaw/issues/11013)（2 条评论）是 RFC #9488 之后的统一文件接收/解析/投递批处理跟踪器；[#12021](https://github.com/zeroclaw-labs/zeroclaw/issues/12021)（2 条评论）Web 配置中的“Save all”会让组保存按钮保持高亮。

**吸引维护者/CI 注意的 PR（按风险与规模）：** [#11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847) 原生 XMPP + 可选 OMEMO（`size:XL`、`risk:high`）；[#11986](https://github.com/zeroclaw-labs/zeroclaw/pull/11986)/[#11965](https://github.com/zeroclaw-labs/zeroclaw/pull/11965) OIDC 仪表盘栈（`release:v0.9.0`、`risk:high`、`needs-author-action`）；[#11230](https://github.com/zeroclaw-labs/zeroclaw/pull/11230) 和 [#11234](https://github.com/zeroclaw-labs/zeroclaw/pull/11234)（`size:XL`、`release-gate`、`priority:p1`）；Goal V1 提案 [#11882](https://github.com/zeroclaw-labs/zeroclaw/pull/11882)/[#11880](https://github.com/zeroclaw-labs/zeroclaw/pull/11880)（`size:XL`，草稿）。

## 4. 缺陷与稳定性

窗口期内所有 issue 均为打开状态；**今天没有关闭任何缺陷**。按严重性/影响排序：

| 排名 | Issue | 严重性 | 影响 | 修复状态 |
|---|---|---|---|---|
| 1 | [#10673](https://github.com/zeroclaw-labs/zeroclaw/issues/10673) ACP daemon RPC 轮次在失败时未持久化 | **S1 / P1** | 工作流被阻塞；ZeroCode Code 窗格中丢失失败轮次历史 | 样本中无关联修复 PR；4 条评论，自 Sep 7 起打开 |
| 2 | [#10780](https://github.com/zeroclaw-labs/zeroclaw/issues/10780) 无主动式 token 预算压缩 | S2 / P1 / `risk:high` | 上下文无界增长，成本激增 | `status:in-progress`，未列出修复 PR；#12044 相关 |
| 3 | [#12051](https://github.com/zeroclaw-labs/zeroclaw/issues/12051) `sop_workshop apply` 允许受限主体编写以操作员权限运行的 SOP | S2 / 安全 | 权限提升 | 无修复 PR |
| 4 | [#12046](https://github.com/zeroclaw-labs/zeroclaw/issues/12046) 通配符工具选择器保留不感知主体的工具（cron、schedule、peer messaging、SOP） | S2 / 安全 | 非管理员主体权限提升 | 相关 PR #11230/#11234 已打开 |
| 5 | [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) 限流器以第一个 `X-Forwarded-For` 条目为键 | S2 / 安全 | 位于追加型代理之后的客户端可声称回环豁免 → 绕过限流器 | 无修复 PR |
| 6 | [#12045](https://github.com/zeroclaw-labs/zeroclaw/issues/12045) 响应体在大小上限之前被整体缓冲（`link_enricher`、WeChat、多模态图像） | S2 / 安全 | 通过用户/LLM 控制的 URL 导致内存耗尽 | 无修复 PR |
| 7 | [#12050](https://github.com/zeroclaw-labs/zeroclaw/issues/12050) OIDC 回调 token 页面没有浏览器绑定 | S2 / 安全 | 登录 CSRF | 无修复 PR |
| 8 | [#12049](https://github.com/zeroclaw-labs/zeroclaw/issues/12049) 32 槽位登录流程存储可被 4 个地址耗尽，包括回环地址 | S2 / 安全 | 全局登录拒绝服务 | 无修复 PR |
| 9 | [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047) OIDC bearer 验证缺少尝试限流器/出站上限 | S2 / 安全 | 放大的发往 IdP 的出站请求 | 无修复 PR |
| 10 | [#12053](https://github.com/zeroclaw-labs/zeroclaw/issues/12053) 仪表盘源上未固定版本的第三方脚本 | S2 / 安全 | 仪表盘源上的供应链 XSS | 无修复 PR |
| 11 | [#11562](https://github.com/zeroclaw-labs/zeroclaw/issues/11562) 插件清单/组件代际不匹配 | S2 / `risk:high` | 在已被替换代际的清单下运行组件 | **修复 PR [#12040](https://github.com/zeroclaw-labs/zeroclaw/pull/12040) 已打开** |
| 12 | [#11711](https://github.com/zeroclaw-labs/zeroclaw/issues/11711) 历史裁剪永久删除较早轮次 | S2 | 长会话中的静默数据丢失 | 无修复 PR |
| 13 | [#10921](https://github.com/zeroclaw-labs/zeroclaw/issues/10921) Qdrant 有时间边界的召回遗漏符合条件的结果 | S2 | 召回质量下降 | 无修复 PR |
| 14 | [#12043](https://github.com/zeroclaw-labs/zeroclaw/issues/12043) `glob_search` 丢弃已授权的跨工作区匹配；在只读根下拒绝绝对模式 | S2 | 工具在两种方向上误报可见性 | 无修复 PR |
| 15 | [#12021](https://github.com/zeroclaw-labs/zeroclaw/issues/12021) 配置保存按钮在 Save all 后仍保持高亮 | S3 | UI 状态混乱 | **修复 PR [#12039](https://github.com/zeroclaw-labs/zeroclaw/pull/12039) 已打开** |
| 16 | [#11825](https://github.com/zeroclaw-labs/zeroclaw/issues/11825) `codex_cli` 测试清空进程 `PATH`，破坏并行 `cargo test` | S3 | CI/测试隔离不稳定 | 无修复 PR |

**评估：** 安全集群异常密集，似乎是单个评审者的一轮审计（#12046–#12053，全部为 S2，全部零评论，全部于 2026-10-26 提交）。大多数**尚无配套修复 PR**，这是进入 v0.9.0 的主要稳定性风险。只有 #11562 和 #12021 有正在进行的修复。

## 5. 功能请求与路线图信号

| 请求 | 链接 | 信号 |
|---|---|---|
| 用于记忆召回的可选外部重排序阶段（`/v1/rerank`，默认关闭，失败时开放到本地路径） | [#12022](https://github.com/zeroclaw-labs/zeroclaw/issues/12022) | 记忆质量可扩展性；低风险，可能在 v0.9.x |
| 对原生 OpenAI reasoning 遵循每个运行时配置档的思考级别 | [#12054](https://github.com/zeroclaw-labs/zeroclaw/issues/12054) | 多 agent、单提供商差异化；范围较小 |
| 根据配置的仪表盘源而非 Host 头验证 OIDC `redirect_uri` | [#12048](https://github.com/zeroclaw-labs/zeroclaw/issues/12048) | 安全增强，与 #11965 栈配套 |
| Gitea/Forgejo 与 GitHub forge 渠道对齐 | [#12041](https://github.com/zeroclaw-labs/zeroclaw/issues/12041) | 自托管/主权部署需求 |
| 统一文件接收/解析/投影/投递（RFC #9488 后跟踪器） | [#11013](https://github.com/zeroclaw-labs/zeroclaw/issues/11013) | 多渠道文件处理架构 |
| 实现或移除失效的上下文配置键 | [#10781](https://github.com/zeroclaw-labs/zeroclaw/issues/10781) | 与 #10780 相关的清理 |

**对下几个版本的预测：**
- **v0.8.7（即将到来）：** 插件准入修复（#11562/#12040）、翻译目录固定（#12019）。低风险维护版本。
- **v0.9.0：** OIDC 仪表盘登录 + 带作用域的仪表盘聊天（#11965、#11986、#11968、#11963），以及 RPC/身份访问加固（#11289、#11326、#11234、#11230）——均明确标记为 `release:v0.9.0`/`release-gate`。八个新的安全 issue 很可能被并入这轮加固。
- **v0.9.0 之后：** 一次上下文管理重构，处理 #10780/#10781/#11711，以及 Goal V1 监督提案（#11880/#11882）在其评审门通过后——目前标记为“FAIL”，因此不是近期事项。

## 6. 用户反馈摘要

- **配置信任侵蚀。** 用户设置 `context_compression.*`、`history_pruning.keep_recent`、`collapse_tool_results` 期望节省 token；但什么都没发生（#10781）。结合 #10780 和 #11711，这是最强烈的不满信号：功能看似存在，但要么失效，要么具有破坏性。
- **长会话中的数据丢失焦虑。** #11711（裁剪重写转录，删除较早轮次）是对任何运行持久网关对话的人来说最用户可见的正确性风险。
- **记忆质量差距。** #10921（有时间边界的召回遗漏符合条件的结果）和 #12022（外部重排序请求）表明用户正推动记忆从“能用”走向“可预测且可调”。
- **UI 打磨很重要。** #12021 表明 Web 仪表盘的小状态缺陷会被注意到并及时提交（一天内出现修复 PR），说明仪表盘用户群体参与度较高。
- **渠道可靠性。** Telegram 用户遇到 429 处理不当（#11651）以及终端语音转录重试循环阻塞长轮询偏移量（#11601）——两者都有修复 PR，但都标记为 `needs-author-action`。
- **Agent 自我纠正摩擦。** #11983 认为仅给出“Command not allowed by security policy”消息会导致 agent 重试变体而不是修复问题；修复方案增加原因 + 提示。这是一个反复出现的主题：安全拒绝必须能向模型解释。
- **外部验证。** [#12042](https://github.com/zeroclaw-labs/zeroclaw/issues/12042) 是一份主动的 Hlido 评审，给 ZeroClaw 打分 **79/100**，并开放回复权。值得注意的是，这是对项目自主性/安全姿态的第三方审视；也表明面向 agent 的评审目录现在正将该项目作为目标。
- **贡献者体验。** #11825（测试修改进程范围的 `PATH`，破坏并行 `cargo test`）对贡献者来说是一个小而真实的摩擦点。

## 7. 积压事项关注

需要维护者关注的事项：打开时间最长、严重性最高或带有明确阻塞标签：

| 时长 | 事项 | 为何需要关注 |
|---|---|---|
| 50 天 | [#10673](https://github.com/zeroclaw-labs/zeroclaw/issues/10673) P1/S1 | 自 Sep 7 起打开的传输一致性缺陷，4 条评论，无修复 PR——窗口期内最古老的未解决 P1 |
| 46 天 | [#10780](https://github.com/zeroclaw-labs/zeroclaw/issues/10780) P1 | 自 Sep 11 起为 `status:in-progress`，没有实现 PR；阻塞 #10781 清理和 #11711 正确性 |
| 40 天 | [#10921](https://github.com/zeroclaw-labs/zeroclaw/issues/10921) | 记忆召回正确性，4 条评论，看不到负责人 |
| 37 天 | [#11013](https://github.com/zeroclaw-labs/zeroclaw/issues/11013) | 已接受 RFC #9488 的实现跟踪器；没有里程碑分配的跟踪器会腐烂 |
| 28 天 | [#11234](https://github.com/zeroclaw-labs/zeroclaw/pull/11234) 和 [#11230](https://github.com/zeroclaw-labs/zeroclaw/pull/11230) | `priority:p1`、`release-gate`、`size:XL`，叠加在未合并的 #11225 上——评审链是瓶颈 |
| 27 天 | [#11289](https://github.com/zeroclaw-labs/zeroclaw/pull/11289) | 带有 `do-not-merge`，同时为 `priority:p2`/`risk:high`；需要一个决定，而不仅仅是一个标签 |
| 26 天 | [#11326](https://github.com/zeroclaw-labs/zeroclaw/pull/11326) | 针对受限主体读取持久化日志的 `release-gate` 修复——与安全相关且停滞 |
| 20/17 天 | [#11601](https://github.com/zeroclaw-labs/zeroclaw/pull/11601)、[#11651](https://github.com/zeroclaw-labs/zeroclaw/pull/11651) | 两者均为 `needs-author-action`；Telegram 可靠性修复闲置 |
| 8 天 | [#11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847) | `size:XL` XMPP 渠道——大面积，需要评审者带宽，以免与 master 偏离 |

**流程观察：** 在整个样本中，5+ 个 PR 带有 `needs-author-action`，1 个带有 `do-not-merge`，而 24 小时内关闭了 0 个 issue 且仅关闭 1 个 PR。项目当前的健康约束是**评审/合并吞吐和作者跟进**，而不是贡献量——值得在下一次维护者同步中提出，尤其是单日落地了 8 个 issue 的安全审计，且标记为 v0.9.0 的工作已经堆叠了数个 PR 之深。

---

*数据说明：数据集列出 29 个有更新的 PR 中的 20 个；唯一合并/关闭的 PR 不在其中，且 PR 评论数提供为 `undefined`，因此 PR 的“热度”是从标签（风险、规模、发布标签）推断的，而不是参与度。所有 issue 评论数均按样本中报告。*

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*