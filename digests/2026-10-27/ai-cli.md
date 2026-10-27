# AI CLI 工具社区动态日报 2026-10-27

> 生成时间: 2026-10-27 01:36 UTC | 覆盖工具: 7 个

- [Claude Code](https://github.com/anthropics/claude-code)
- [OpenAI Codex](https://github.com/openai/codex)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [GitHub Copilot CLI](https://github.com/github/copilot-cli)
- [OpenCode](https://github.com/anomalyco/opencode)
- [Pi](https://github.com/earendil-works/pi)
- [Qwen Code](https://github.com/QwenLM/qwen-code)
- [Claude Code Skills](https://github.com/anthropics/skills)

---

## 横向对比

# 跨工具对比报告 — AI CLI 生态（2026-10-27）

## 1. 生态概览

七个纳入跟踪的工具均处于活跃开发中，但社区讨论重心已明确从“agent 能否完成任务”转向**“我们能否在大规模场景下信任、观测并运维它。”** 可靠性工程——会话连续性、沙箱/策略正确性、真实状态上报与成本治理——现已主导每个仓库的 issue 流量。可扩展性是第二战场：各厂商都在推出自己的插件/钩子契约（Mods、IPC 宿主、ACP、GUI 插件），跨工具可移植性正成为明确诉求。发布节奏高度不均——从每天多个 tag/nightly tag（Qwen Code、Codex alpha、Gemini nightly）到不透明的补丁构建（Copilot CLI）——而平台一致性（Windows、Wayland、Termux）与企业配置仍是普遍技术债。

## 2. 活跃度对比

计数反映各摘要中呈现的内容，不一定代表仓库全量。

| 工具 | Issues（呈现/更新） | PRs（呈现/更新） | Discussions | 发布状态（过去 24 小时） |
|---|---|---|---|---|
| **Claude Code** | 10 个热门 issue（最热讨论串 274 条评论，最高反应 955 👍） | ≥20 个呈现，10 个详细 | N/A（未提供） | **v2.1.311** 已发布（空闲会话控制、启动冻结修复） |
| **OpenAI Codex** | **50 个更新** | **50 个更新**（大多已关闭，由 bot 编写） | **8 个更新** | 3 个 Rust alpha（`0.166.0-alpha.1`、`0.165.0-alpha.3`、`0.165.0-alpha.1.1`）；无 changelog |
| **Gemini CLI** | 10 个热门 issue | 10 个关键 PR | N/A（未提供） | **v0.67.0-nightly.20261027**（配额分类、ACP MCP 元数据） |
| **GitHub Copilot CLI** | **50 个更新** | **0 个更新** | N/A（未提供） | v1.0.104-4 — 仅有“修复与变更”；无法追溯到公开 PR |
| **OpenCode** | 10 个热门 issue（剪贴板 #4283 以 159 条评论 / 134 👍 关闭） | 10 个关键 PR（GUI 插件 SDK 已落地） | N/A（未提供） | 未记录发布；插件/定价 PR 仍在推进 |
| **Pi** | 10 个热门 issue（Windows 大型讨论串 90 条评论） | 10 个关键 PR | **1 个 discussion**（show & tell） | 过去 24 小时无发布 |
| **Qwen Code** | 10 个精选 issue（Kubernetes 跟踪 25 条评论） | 10 个精选 PR | N/A（未提供） | **3 个发布**：v0.26.2-preview.0、v0.26.1-nightly、sdk-typescript-v0.1.23 |

*注：本集合中没有任何仓库在上游禁用了 Issues/PRs。Discussions 为“N/A”表示该时段未提供 discussion 数据，而非该渠道不活跃。*

## 3. 共同功能方向

**1. 会话生命周期持久性与恢复正确性** — 跨工具中最大的问题簇。
- Claude Code：`--continue`/`--resume` 绕过活跃会话注册表（#69364）；重试将 `parentUuid` 写入从未持久化的行，导致历史丢失（#96628）。
- Copilot CLI：恢复时 OOM 与约 70 分钟的核心线程被钉住（#4251）、会话锁超时（#5253）、被遮蔽计数器导致会话无法恢复（#5023）、模型前缀不匹配（#4282）。
- Pi：长会话中 Bedrock 凭据过期（#11221）、压缩文件列表无界增长（#11297）。
- Codex：远程/移动端归属仲裁（#39547、#54591）。

**2. 子 agent 可观测性与真实状态** — 用户正在自动化 agent，并对静默截断零容忍。
- Gemini CLI：`MAX_TURNS` 被误报为 `status: "success"` / `Termination Reason: GOAL`（#22323）；Wayland 浏览器失败也报告 `GOAL`（#21983）；通过 `/chat share` 导出轨迹（#22598）。
- Qwen Code：1k 子 agent 提示词被报告为 105k 起始上下文（#15660）。
- Claude Code：`PreCompact`/`PostCompact` 在缺少 agent 字段时触发；误报 `SubagentStop`（#91910）。
- Codex：失败子 agent 被不当提升为“Needs you”（#57315）。

**3. 稳定的可扩展性契约** — 每个工具都在定义自己的契约，可移植性现已被明确提出。
- Claude Code：Mods（#91870，274 条评论）以及插件 UI 渲染冲突（#102480）。
- Codex：带受限 IPC、256 KiB 帧、就绪握手的插件进程宿主（#57290）；`PreToolUse` 丢弃 `exec_command` 的 workdir（#32360）。
- OpenCode：Desktop GUI 插件 SDK 已落地（#55246），后续原语（#59462）；响应式 TUI 插件槽位（#59458）。
- Pi：公开的工具呈现 API / 单行工具模式（#10259）；重复的 `session_start` 事件（#10470）；assistant 消息转换器（#11450）。
- Qwen Code：Claude Code 风格 Mod 运行时兼容性提案（#13749）；`FileChanged` 钩子（#13969）。
- Gemini CLI：ACP 权限请求现在携带 MCP 服务器/工具元数据（#29910）。

**4. 沙箱/策略透明性与正确性** — 没有审计轨迹的误报会侵蚀信任。
- Codex：BitLocker 卷阻止子进程启动（#50915）；Markdown 写入在执行前被“策略阻止”，且没有审查条目（#45986）。
- Qwen Code：无头 `-p` 的隐式 `edit` 拒绝覆盖作用域允许（#15597）；`2>&1` 被解析为对 `&1` 的写入（#15600）；进程内 `sed` 与真实 sed 行为不一致（#15675）。
- Gemini CLI：破坏性 `git reset`/`--force` 缺少护栏（#22672）。
- Copilot CLI：ACP 模式再次自动批准工具调用；未发送 `session/request_permission`（#4537）。

**5. 跨平台一致性（Windows 优先）**
- Claude Code：Windows 上控制台窗口抢焦点（#91264）。
- Codex：渲染进程内存泄漏至 4–7 GB（#46690）；组织设置加载失败（#57051）；Windows 原生运行时自 5 月起崩溃（#4026）。
- OpenCode：剪贴板（#4283）、Ctrl+C 后鼠标跟踪未关闭（#6912）、官方应用被误判为非官方（#56324）。
- Pi：90 条评论的 Windows 大型讨论串（#7547）；`find` glob 失败（#6817）。
- Gemini CLI：Windows CRLF diff 规范化（#29966）、Termux ripgrep 信任问题（#29884）、歧义宽度渲染（#29905）。

**6. 成本/配额透明性与 token 记账** — 每个摘要中都会出现。
- Codex：促销额度在每周配额之前被耗尽（#52039）。
- Copilot CLI：Opus 5.5 在 32k 输出上限下的空轮次仍计费（#5149）；BYOK Azure 429 重试在约 0.15 秒内消耗配额（#3679）。
- OpenCode：免费层配额/重置大型讨论串（#59387）、计数器卡在 61k–84k 秒（#59116）、Desktop 与 CLI 不一致（#59112）。
- Gemini CLI：支出上限错误现归类为终止性错误，以停止重试浪费（#29961）。

**7. 模型/提供商灵活性与路由**
- Copilot CLI：按 agent 选择提供商（#4703）、ACP 模型列表（#4880）、`auto` 强制 `reasoningEffort: null`（#4560）。
- Pi：OpenCode 按协议端点（#11132）、Anthropic Vertex（#5262）、自定义 OpenAI 兼容 `/login` 流程（#11456）。
- OpenCode：Claude Agent 提供商通过用户的 Claude Code 登录运行 Claude 模型（#58782，受合规约束）。
- Codex：GPT-6.1 Terra 目录新增覆盖 Bedrock/Vertex（#57293）；网关 OAuth bearer 投递默认开启（#57313）。

**8. 企业/MCP 就绪度**
- Copilot CLI：自定义 MCP registry 在 VS Code 中可达，但在 CLI 中不可达（#4949）；使用有效存储 token 的 OAuth 探测返回 HTTP 400（#5014）。
- Codex：托管 `requirements.toml` 中的 `network_proxy`（#57286）。
- Pi：MCP HTTP JSON 绕过 `maxMessageBytes`，而 SSE 会强制执行（#11335）；MCP OAuth 用 CIMD 替代 DCR（#11426）。
- Claude Code：MCP elicitation 被一个从未绘制提示的控制器自动拒绝（#89858）。

## 4. 差异化分析

| 维度 | Claude Code | OpenAI Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **主要生态** | Anthropic | OpenAI | Google | GitHub/Microsoft | 开源/社区 | 提供商无关 | 阿里巴巴/Qwen |
| **核心关注** | 可扩展性（Mods）、Desktop 一致性、认证/身份 | 沙箱/策略正确性、桌面+移动连续性 | 子 agent 编排、ACP、AST 感知工具链 | ACP、MCP/Azure/BYOK、无头一致性 | 免费层记账、v1→v2 迁移、GUI 插件 | 长会话持久性、TUI 保真度、Nix | 托管 agent/Hosted 运行时、Web Shell |
| **目标用户** | 在个人/团队/客户身份间切换的专业开发者；插件作者 | 跨界面用户（桌面/CLI/iOS）；企业策略管理员 | GCP 生态中以终端为中心的开发者；自动化构建者 | 以 GitHub 为中心的开发者；企业 Azure 团队；无头/CI | 开源开发者与多提供商折腾者 | 高级用户、TUI 纯粹主义者、Bedrock/Vertex/Nix 用户 | Qwen 用户；托管/受管部署；Web Shell 用户 |
| **技术路径** | 市场插件 + 钩子；Desktop/IDE 优先的回归 | Rust 核心；进程隔离的插件 IPC；高频 alpha tag，伴随大量 bot PR 自动化 | Nightly TS CLI；ACP 优先；AST 感知工具链 EPIC | 公开 PR 可见性稀疏；ACP 模式；带本地 ASR 的语音模式 | 基于 Bun 的服务器/客户端协议；插件覆盖 server/TUI/GUI | TypeScript TUI；提供商适配器（Bedrock、Vertex、OpenCode） | 托管 agent 服务器 + SDK（TS、Java）+ Web Shell |
| **显著区别** | 最高反应数（多账号问题 955 👍） | 最强的沙箱/策略工程与自动化 PR 吞吐 | 最干净的 ACP 集成；最明确的子 agent 可观测性路线图 | **零 PR 呈现**，尽管有 50 个 issue 更新——发布可追溯性缺口 | 显式复用 Claude Code 登录（#58782，受合规约束） | 最佳多提供商中立性与打包严谨性 | 唯一交付 Anthropic 兼容性的工具（Mod 运行时、移除 Co-authored-by） |

## 5. 社区动能与成熟度

**最活跃/互动量最大**
- **Claude Code** — 数据集中原始互动量最高：#91870（274 条评论）、#18435（955 👍）、#1757（一年前的认证摩擦仍每日更新）。成熟，但背负显著可靠性欠账。
- **OpenCode** — 免费层支持流量密集，另有一个 159 条评论/134 👍 的剪贴板 issue；社区规模已大到维护者创建聚合讨论串（#59387）。
- **Pi** — 规模较小但高级用户参与度很高；90 条评论的 Windows 大型讨论串是优先级信号，而非 bug 队列。

**迭代最快**
- **Qwen Code** — 24 小时内 3 个发布，且托管 agent 方向功能丰富，但 CI 噪音主导评论数（#14724 有 64 条评论；10 次失败未提交任何 issue，#15470），消耗维护者带宽。
- **OpenAI Codex** — 24 小时内 3 个 alpha tag 和 50 个 PR，大多自动化（`copyberry[bot]`），表明 CI/自动化流水线成熟。不过发布说明缺席。
- **Gemini CLI** — 稳定的 nightly 节奏，修复范围收紧且描述良好（#29961、#29910、#29905、#29966）；是本集合中可读性最一致的 changelog。

**迭代最不透明**
- **GitHub Copilot CLI** — 50 个更新的 issue、**0 个更新的 PR**，以及仅包含“修复与变更”的发布说明。实际上，429 重试相关问题关闭（#2760）无法映射到某个构建。这并非不活跃——而是公开可追溯性缺口，使固定使用 1.0.103 的团队更难二分定位。

**按维度的成熟度信号**
- 可扩展性成熟度：Codex（IPC 隔离）> Claude Code（Mods）> OpenCode（GUI SDK）> Gemini CLI（ACP）> Pi/Qwen Code（推进中）。
- 运营成熟度：Gemini CLI 与 Codex 展现出有纪律的自动化 PR/发布卫生；Qwen Code 与 Pi 仍吸收大量人工/分诊开销。
- 信任成熟度：七个工具中尚无一个端到端解决真实 agent 终止上报；Gemini 的 #22323 是最清晰例子，但该模式反复出现。

## 6. 趋势信号

**1. 信任与可观测性成为新的能力前沿。** Gemini CLI 中评论最多的 issue 是一个上报 bug，而非缺失功能。Copilot CLI 的主导主题是静默失败（空的语音转录、OAuth 无弹窗、权限请求无人应答且无日志）。预计“agent 审计日志”和“终止原因 API”将成为基本门槛。

**2. 可扩展性碎片化将带来整合压力。** 每个工具现在都有独特的钩子/插件模型：Mods、IPC 宿主、ACP 元数据、GUI 插件 SDK、`FileChanged` 钩子。Qwen Code 明确要求兼容 Claude Code（#13749），这是早期迹象：插件可移植性——而不仅是模型质量——将影响采用。

**3. 企业加固正从文档转向配置文件。** 托管的 `requirements.toml`、`network_proxy`、网关 OAuth 投递默认值、按工作区策略，以及“拒绝并附审查条目”，正汇聚为事实上的企业管理员契约。

**4. 长会话工作负载未被充分服务。** 凭据刷新、无界压缩状态、积压的发送队列、恢复 OOM，以及活跃会话注册表竞态，都指向同一个未被充分服务的用例：agent 连续运行数小时或数天。这是设计缺口，而非 bug 队列。

**5. 成本治理正成为一等 UX 关注点。** 产生费用的空输出轮次、未退避的 429 重试、卡在 61k 秒的配额计数器，以及促销额度不匹配，都会在支出无人值守的场景（CI/无头）精准侵蚀信任。预计按会话预算控制和可检查的 token 账本将成为标配。

**6. 平台一致性仍是战略决策，而非战术决策。** Pi 的大型讨论串将 Windows 框定为投资分配问题；Codex 与 OpenCode 显示 Windows 是最高严重度 bug 簇。Wayland 与 Termux 正成为下一个一致性前沿。

**7. 自动化 PR 流水线是竞争优势。** Codex 由 bot 编写的大量 PR，以及 Gemini 的 nightly 纪律，与 Copilot CLI 不可见的合并路径形成对比。对决策者而言，**发布可追溯性**（我们能否将回归二分定位到某个构建？）是与功能集并列的正当评估标准。

### 对开发者的参考价值

- **构建插件/钩子：** 面向载荷最完整、生命周期保证最强的契约；关注 Claude Code #91910（agent 字段）、Codex #57290（IPC 分帧/背压），以及 Qwen Code #13749 的潜在 Claude Code 兼容性。
- **为企业/受监管场景选型：** 优先考虑沙箱/策略透明度（Codex、Qwen Code 修复推进中）与可观测性；将“被策略阻止且无审查条目”视为受审计工作流的淘汰项。
- **运行无头/CI：** 仔细审查非交互模式下的权限语义（Qwen Code #15597/#15600 是最清晰的警示案例）、输出 token 上限与重试行为（Copilot CLI #5149、#3679），以及会话恢复保证（Copilot CLI 问题簇、Claude Code #96628）。
- **避免锁定：** Pi 与 OpenCode 提供最广泛的提供商中立性；ACP 与 MCP 标准化是值得关注的机制，不过 MCP 传输校验不一致（Pi #11335）表明该规范尚未被统一执行。
- **二分定位回归：** 优先选择有逐项 changelog 和公开 PR 关联的工具；Copilot CLI 不透明的补丁节奏是真实的运营成本，而非表面抱怨。

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — 社区亮点报告
**来源：** github.com/anthropics/skills · **数据截至：** 2026-10-27

> **数据说明：** 所列 20 个 PR 均报告 `Comments: undefined` 和 `👍: 0`，因此下方排名使用代理关注度信号——更新新近度、讨论串存续时长、与高评论 Issue 的交叉链接——而不是原始评论数。Issue 评论数*确实*可用，并在第 2 节中使用。

---

## 1. 热门 Skills 排名

| # | Skill / PR | 功能 | 讨论要点 | 状态 |
|---|---|---|---|---|
| 1 | [#1742](https://github.com/anthropics/skills/pull/1742) `mcp-builder` | 搭建 MCP 服务器（连接、评估测试框架） | 修复 #1668：`mcp>=2` 将 `streamablehttp_client` 重命名为 `streamable_http_client`；headers 现在需要 `create_mcp_http_client`/`http_client`。这是更广泛的 `mcp-builder` 故障群的一部分（参见 Issue #1390） | OPEN · 更新于 2026-10-08 |
| 2 | [#1980](https://github.com/anthropics/skills/pull/1980) `webapp-testing` | 无头浏览器测试框架 | 移除 `with_server.py` 中的 `shell=True`（CWE-78 命令注入），改为替换 `cd && cmd` 链——对广泛使用的 Skill 进行安全加固 | OPEN · 更新于 2026-10-25（最近） |
| 3 | [#1997](https://github.com/anthropics/skills/pull/1997) `skill-creator` | 用于编写/优化 Skill 的元 Skill | 手写 YAML frontmatter 解析器会丢弃引号/转义，导致评估和描述优化读取到的元数据与 `SKILL.md` 不同 | OPEN · 更新于 2026-10-19 |
| 4 | [#1245](https://github.com/anthropics/skills/pull/1245) 新增：`notion-spec-to-implementation`、`quantitative-resume-auditor` | 规格→Notion 任务拆解；简历量化审计 | 打包提交的两个 Skill；仍在持续收到更新的运行时间最长的*新 Skill* PR（创建于 2026-06） | OPEN · 更新于 2026-10-15 |
| 5 | [#1681](https://github.com/anthropics/skills/pull/1681) `skill-creator` | 打包 CLI（`package_skill.py`） | 直接执行时出现 `ModuleNotFoundError: scripts.quick_validate`；docstring/帮助路径过时 | OPEN · 更新于 2026-10-24 |
| 6 | [#1298](https://github.com/anthropics/skills/pull/1298) `skill-creator` | Skill 描述触发评估 | 误漏触发：每个 worker 竞争性探测、管道上的 `select()` 在 Windows 上失败、运行时失败被错误计为未触发（抬高负例数量） | OPEN · 更新于 2026-09-16 |
| 7 | [#1792](https://github.com/anthropics/skills/pull/1792) `docx` | Word 文档编辑 / 变更接受 | LibreOffice 超时被报告为成功；现在会报错并验证 `w:ins`/`w:del`/`w:moveFrom` 已移除——与 Issue #2018（无效 XML）属于同一文件系列 | OPEN · 更新于 2026-09-25 |
| 8 | [#525](https://github.com/anthropics/skills/pull/525) 新增：`pyxel` | 用 Python 进行复古游戏开发/验证 | 存续时间最长的活跃提交（2026-03 → 2026-10）；无头输入驱动运行、帧检查、状态检查 | OPEN · 更新于 2026-10-23 |

*其他值得注意：* [#1771](https://github.com/anthropics/skills/pull/1771) `proofcore-contract-auditor`（Web3/Solidity+TON 公证）、[#1730](https://github.com/anthropics/skills/pull/1730) `claude-api`/`academy-guide` 中的失效 URL 修复、[#514](https://github.com/anthropics/skills/pull/514) `document-typography`（孤行/寡行/编号）。

---

## 2. 社区需求趋势

根据 Issue 评论量和反应提炼：

1. **信任、来源与命名空间治理** — Issue [#492](https://github.com/anthropics/skills/issues/492)（46 条评论，是讨论量最高的单项）：社区 Skill 以 `anthropic/` 命名空间发布，冒充官方 Skill，从而可导致权限升级。相关：[#189](https://github.com/anthropics/skills/issues/189)（`document-skills`/`example-skills` 中重复 Skill，抬高上下文占用）。
2. **分发与组织级共享** — Issue [#228](https://github.com/anthropics/skills/issues/228)（17 条评论，8 👍）：希望有共享的组织 Skill 库 / 分享链接，而不是通过 Slack 手动交换 `.skill` 文件。
3. **评估 harness 可靠性** — Issue [#556](https://github.com/anthropics/skills/issues/556)（13 条评论：`run_eval.py` 中触发率为 0%）、[#1390](https://github.com/anthropics/skills/issues/1390)（`mcp-builder` 对真实服务器的评估得分为 0/N）、[#1394](https://github.com/anthropics/skills/issues/1394)（eval-viewer XSS）。现在，测试 *Skill 本身* 已成为首要诉求。
4. **上下文/token 效率** — Issue [#1487](https://github.com/anthropics/skills/issues/1487)：`claude-api` 在一次工具调用中急切换入约 156k token；[#202](https://github.com/anthropics/skills/issues/202)：`skill-creator` 读起来像人类文档，违反 token 效率规范。渐进式披露与按预算加载。
5. **Agent 记忆与治理元 Skill** — [#1329](https://github.com/anthropics/skills/issues/1329) `compact-memory`（符号化状态表示）、[#412](https://github.com/anthropics/skills/issues/412) `agent-governance`（策略、威胁检测、审计轨迹）、[#1385](https://github.com/anthropics/skills/issues/1385) 推理质量门禁流水线、[#83](https://github.com/anthropics/skills/pull/83) `skill-quality-analyzer` + `skill-security-analyzer`。
6. **文档流水线正确性** — [#2018](https://github.com/anthropics/skills/issues/2018)（docx 命名空间被剥离 → 无效 XML）、[#1868](https://github.com/anthropics/skills/issues/1868) 渐变插值差一错误、[#2064](https://github.com/anthropics/skills/issues/2064) 描述只说明是什么，未说明何时使用。

**总体判断：** 需求已从“更多 Skill”转向**让现有 Skill 可信、可评估且 token 成本低**，并治理 Skill 的发布方式。

---

## 3. 高潜力待合并 Skills

近期有维护者可见活动、可能很快落地的活跃未合并 PR：

- **[#1980](https://github.com/anthropics/skills/pull/1980)** — `webapp-testing` 的 shell 注入修复；diff 最小、出于安全动机，是本组中最近被触碰的 PR。
- **[#1997](https://github.com/anthropics/skills/pull/1997)** — `skill-creator` frontmatter 解析；影响范围小，可解除对可靠评估/优化的阻塞。
- **[#1681](https://github.com/anthropics/skills/pull/1681)** — `skill-creator` 独立脚本执行 + 文档；易于验证。
- **[#1742](https://github.com/anthropics/skills/pull/1742)** — `mcp-builder` 客户端导入/header 迁移；时间敏感（`mcp>=2` 破坏）。
- **[#1245](https://github.com/anthropics/skills/pull/1245)** — `notion-spec-to-implementation` + `quantitative-resume-auditor`；持续 4 个月的迭代表明作者响应积极。
- **[#525](https://github.com/anthropics/skills/pull/525)** — `pyxel`；成熟、范围窄，作者持续投入。
- **[#1771](https://github.com/anthropics/skills/pull/1771)** — `proofcore-contract-auditor`；填补 Web3 垂直领域空白，但外部服务依赖可能拖慢审查。
- **[#514](https://github.com/anthropics/skills/pull/514)** — `document-typography`；用确定性规则解决普遍存在的输出质量问题。

*注意：* [#1245](https://github.com/anthropics/skills/pull/1245)–[#1771](https://github.com/anthropics/skills/pull/1771) 这类多 Skill 或厂商耦合型提交，历史上常因范围和第三方依赖审查而停滞；单一目的修复推进更快。

---

## 4. Skills 生态洞察

**社区最集中的需求不是新增 Skill 的广度，而是加固现有核心——安全性、正确性、评估完整性和上下文成本——并为社区贡献的 Skill 建立可信的发布/命名空间模型。**

---

# Claude Code 社区文摘 — 2026-10-27

## 1. 今日亮点

Claude Code 发布了 **v2.1.311**，为自托管运行器新增空闲会话控制，并修复了启动卡死问题。社区关注仍集中在两个长期请求上：**Mods 可扩展性**计划（#91870，274 条评论）和 **Claude Desktop 多账号支持**（#18435，955 👍）。可靠性是主要缺陷主题，有多份报告称响应中途连接中断及反复出现认证提示。

## 2. 版本发布

**v2.1.311** — [发布说明](https://github.com/anthropics/claude-code/releases)
- 为 `self-hosted-runner` 新增 `--hold-idle-file` 和 `--max-hold-min`，允许在宿主机报告其遗留运行进程时保留空闲会话。
- 为 **Explanatory** 输出风格新增提示，在请求 Claude 解释时显示。
- 修复启动时及之后长时间卡死……*（提供的变更日志已截断）*。

## 3. 热门 Issue

1. **[#91870](https://github.com/anthropics/claude-code/issues/91870) — Mods：让 Claude 可扩展性提升 10 倍** · 274 条评论 · 131 👍 · OPEN  
   最活跃的设计讨论串。维护者发布了社区微更新，表明可扩展性接口已上线，反馈正在处理中。这很可能是跟踪器中最重要的插件/钩子 API 变更。

2. **[#18435](https://github.com/anthropics/claude-code/issues/18435) — Desktop 多 Claude 账号及便捷配置文件切换** · 220 条评论 · 955 👍 · OPEN  
   数据集中反应数最高。强烈信号表明开发者需要兼顾个人、团队和客户身份，希望有一流的配置文件管理而非重新认证。

3. **[#1757](https://github.com/anthropics/claude-code/issues/1757) — Claude Code 要求用户频繁登录** · 88 条评论 · 76 👍 · 自 2025-06 起 OPEN  
   一年前的认证摩擦问题仍每日更新。持久会话仍是首要可用性投诉，并与 MCP OAuth/令牌存储缺陷交织。

4. **[#88391](https://github.com/anthropics/claude-code/issues/88391) — Desktop 中提示建议悄然消失（2.1.229 回归）** · 16 条评论 · 16 👍 · 有复现  
   清晰的回归报告并附复现。重要因为 Desktop 用户体验回归会侵蚀对自动更新的信任。

5. **[#91910](https://github.com/anthropics/claude-code/issues/91910) — 子代理压缩及内部摘要器调用相关的钩子载荷缺口** · 13 条评论 · 4 👍 · 有复现  
   `PreCompact`/`PostCompact`/`SessionStart(compact)` 触发时缺少代理字段，且 `SubagentStop` 对内部摘要器调用触发时带有从未创建的转录路径。对基于钩子构建的插件作者很重要。

6. **[#69364](https://github.com/anthropics/claude-code/issues/69364) — `--continue`/`--resume` 不检查活动会话注册表** · 12 条评论 · 1 👍  
   两个进程可追加到同一会话 JSONL。影响多终端工作流的并发/正确性问题。

7. **[#89858](https://github.com/anthropics/claude-code/issues/89858) — MCP 引导被从未绘制提示的控制客户端自动拒绝** · 7 条评论 · 2 👍 · 有复现  
   在 Desktop、`--input-format stream-json` 和 Agent SDK 中复现。此前相关报告被以重复为由关闭而非按实质处理，使其成为关键 SDK 集成缺陷。

8. **[#103488](https://github.com/anthropics/claude-code/issues/103488) — “响应中途连接丢失”追溯至来自 160.79.104.10 的 TCP 重置** · 6 条评论  
   数据包捕获证据指向服务端重置。有价值因为将讨论推进到超越泛泛的网络归咎。

9. **[#102480](https://github.com/anthropics/claude-code/issues/102480) — Desktop Code 标签页：下一步框遮挡插件的 `AbovePrompt` 渲染** · 6 条评论 · 3 👍  
   具体的插件 UI 集成冲突：`on('ui.render', { component: 'AbovePrompt' })` 输出被内置下一步框遮挡。

10. **[#91264](https://github.com/anthropics/claude-code/issues/91264) — PowerShell/Bash 工具调用在 Windows 上生成抢焦点的控制台窗口** · 5 条评论  
    没有 schema 暴露的选项可抑制该窗口。高烦扰度的 Windows 打磨问题，打断代理运行时的流程。

## 4. 关键 PR 进展

1. **[#105303](https://github.com/anthropics/claude-code/pull/105303) — 从 claude-plugins-official 安装市场插件** · OPEN  
   将 `claude-code-plugins` 市场迁移为从 `claude-plugins-official` 安装所有插件。明确依赖于 `anthropics/claude-plugins-official#6905` 和 `#6906` 的合并顺序。

2. **[#96434](https://github.com/anthropics/claude-code/pull/96434) — security-guidance：将拒绝及机密文件排除在审查者触及范围之外** · CLOSED  
   审查子代理现在遵守 `Read` 拒绝/询问规则，排除 `.env`、密钥和凭据存储；获得与 `disallowed_tools` 相同的规则且无 shell。`SG_SKIP_SECRET_FILES=0` 可选择退出。

3. **[#87079](https://github.com/anthropics/claude-code/pull/87079) — fix(security-guidance)：使 `**` 通配模式匹配零深度路径** · CLOSED  
   此前 `**/*.ts` 会静默将顶层文件排除在安全规则之外——安全敏感路径中的静默假阴性。

4. **[#85409](https://github.com/anthropics/claude-code/pull/85409) — 将默认模型引用从 Opus 4.7/Sonnet 4.6 更新为 Opus 5/Sonnet 5** · CLOSED  
   使 security-guidance README 和钩子代码与当前模型默认值对齐。

5. **[#81426](https://github.com/anthropics/claude-code/pull/81426) — security-guidance：为代理式审查者支持 Windows venv 布局** · CLOSED  
   移除 `SKIP_WIN32` 提前返回，使最强审查层在 Windows 上无需系统安装 `claude_agent_sdk` 即可工作。

6. **[#79237](https://github.com/anthropics/claude-code/pull/79237) — 新增 `_is_isolated_worktree` 守卫，防止 spawn 修改父仓库检出** · CLOSED  
   解决严重故障模式：`spawn_task`/chips 导致 `git checkout -b` 针对共享主检出运行。

7. **[#76640](https://github.com/anthropics/claude-code/pull/76640) — 为 Bun 运行时加载 macOS 系统证书并处理 `NO_PROXY`** · CLOSED  
   修复 v2.1.17+ 引入 Bun 运行时后出现的“检测到自签名证书”失败，包括代理配置。

8. **[#76274](https://github.com/anthropics/claude-code/pull/76274) — 针对仓库根解析审查路径并强化发现数组契约** · CLOSED  
   在审查者提示中规范化相对、根锚定和外部绝对路径——减少遗漏或错误归因的发现。

9. **[#74021](https://github.com/anthropics/claude-code/pull/74021) — 允许 StructuredOutput schema 中的 `null` 发现** · CLOSED  
   模型有时输出 `null` 而非 `[]`，导致 schema 拒绝和浪费重试轮次；在 7 个仓库的 31 个会话中观察到。

10. **[#65875](https://github.com/anthropics/claude-code/pull/65875) — 将 `ANTHROPIC_BASE_URL` 转发至 agentic_review 子进程** · CLOSED  
    修复代理/网关用户（LiteLLM、Bifrost）的认证失败，其审查子进程默认使用 `api.anthropic.com`。

*注：20 个 surfaced PR 中许多是同一窗口关闭的 security-guidance 修复，表明插件积压的清理扫荡。*

## 5. 热门讨论

本期未提供讨论数据；此节省略。

## 6. 功能请求趋势

- **身份与账号管理** — Desktop 多配置文件切换（#18435）和持久认证（#1757）。
- **可扩展性成熟度** — Mods API、带代理身份的完整钩子载荷（#91910）、Team 组织中的插件优先级规则（#104236），以及不截断的 `plugin validate` 输出（#105020）。
- **Desktop/代理视图编排** — 对 chip 生成任务状态的可见性（#95554）、PR 合并/关闭事件传递至监视会话（#98319），以及任务 chip 跨压缩/会话的持久性（#93880、#97100）。
- **会话生命周期控制** — 仅手动归档（#91607）、恢复前检查活动会话注册表（#69364），以及重试后可恢复的转录（#96628）。
- **输出详细度控制** — 反复请求减少默认解释量（#96645）。
- **Cowork 组织** — 按最后活动而非创建日期排序项目聊天（#87723）。

## 7. 开发者痛点

- **网络可靠性主导缺陷数量。** 响应中途连接中断跨平台出现：#103488（服务端 TCP 重置）、#103058（7 天内 291 个错误，276 个在同一点）、#104603、#104527、#89475（首块后 2–8 毫秒流重置），以及已关闭的 #87500。用户越来越多地提供数据包捕获和转录统计。
- **认证不稳定。** 持续重新登录（#1757）和 macOS Keychain 写入失败/超时下的 MCP OAuth 令牌丢失（#99471）仍未解决。
- **Windows 仍是最粗糙的平台。** 抢焦点的控制台窗口（#91264）、`/v1/messages` 上的流 `ECONNRESET`（#89475），以及 stream-json 模式下 MCP 引导自动拒绝（#89858）。
- **钩子/插件契约规格不足。** 压缩钩子缺少代理字段、虚假 `SubagentStop` 调用（#91910）、静默安全默认值覆盖（#104236），以及截断的验证输出（#105020）使插件行为难以调试。
- **Desktop 回归和隐藏状态。** 提示建议消失（#88391）、插件渲染输出被遮挡（#102480）、PR 合并时会话自动归档（#91607），以及压缩后 chips 不可见（#93880）。
- **后台会话以最小 PATH 启动**（`/usr/bin:/bin:/usr/sbin:/sbin`），破坏 nvm/pyenv 安装的 MCP 服务器和状态栏命令（#104181）。
- **恢复可能丢失历史**，当失败的工具调用重试写入指向从未持久化行的 `parentUuid`（#96628）——数据丢失类问题而非表面缺陷。

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区摘要 — 2026-10-27

快照：50 个更新的议题、50 个更新的 PR、8 个更新的讨论。议题流量高度集中在桌面稳定性、沙箱/策略正确性以及远程会话连续性上。

## 1. 今日亮点

Codex Rust 发布仍保持快速的 alpha 节奏，新增三个标签——`0.166.0-alpha.1`、`0.165.0-alpha.3` 和 `0.165.0-alpha.1.1`——但没有提供详细的发布说明。议题队列主要由平台可靠性问题占据：Windows 沙箱/策略失败和组织设置加载问题，以及反复出现的 macOS Computer Use worker 泄漏和内存相关崩溃。PR 活动重点包括插件生命周期/IPC 工作、Guardian 上下文重构、企业认证/网络配置，以及通过 GPT-6.1 Terra 扩展模型目录。

## 2. 发布

- [rust-v0.166.0-alpha.1](https://github.com/openai/codex/releases/tag/rust-v0.166.0-alpha.1) — 新的 Rust alpha 标签；所提供数据中没有变更日志详情。
- [rust-v0.165.0-alpha.3](https://github.com/openai/codex/releases/tag/rust-v0.165.0-alpha.3) — 新的 Rust alpha 标签；所提供数据中没有变更日志详情。
- [rust-v0.165.0-alpha.1.1](https://github.com/openai/codex/releases/tag/rust-v0.165.0-alpha.1.1) — 补丁级 alpha 标签；所提供数据中没有变更日志详情。

## 3. 热门议题

1. **[#50915 — Windows MXC sandbox fails when an unrelated BitLocker volume is locked](https://github.com/openai/codex/issues/50915)**  
   一个已锁定且无关的数据卷会阻止子进程启动，即使工作区位于其他位置。这是一个高影响的 Windows 沙箱缺陷；21 条评论和 8 个 👍 使其成为队列中最活跃的议题之一。

2. **[#38760 — macOS Computer Use spawn storm triggers WindowServer watchdog panic](https://github.com/openai/codex/issues/38760)**  
   `SkyComputerUseService` 大约每秒生成 5–8 个进程，且没有复用或清理，导致严重的系统不稳定。该议题有 15 条评论，并有 #40153、#39732 和 #39151 等相关报告，看起来像是系统性的桌面运行时泄漏。

3. **[#32360 — `PreToolUse` drops `exec_command` workdir and reports the turn cwd](https://github.com/openai/codex/issues/32360)**  
   Hook 负载报告了错误的工作目录，削弱了策略执行和可审计性。该问题自 7 月以来仍未关闭，有 13 条评论和 5 个 👍，表明工具调用正确性方面存在持续缺口。

4. **[#46690 — Windows renderer memory leak grows to 4–7 GB and crashes repeatedly](https://github.com/openai/codex/issues/46690)**  
   桌面渲染器即使在轻负载下也会泄漏内存，而据报道较旧的构建是稳定的。九条评论表明这是一个严重的 Windows 桌面回归。

5. **[#45986 — Windows policy rejects a user-authorized Markdown QA report write](https://github.com/openai/codex/issues/45986)**  
   该命令在执行前被以“blocked by policy”阻止，且没有审查记录。这一点很重要，因为沙箱误报既会造成干扰，又难以审计；9 条评论。

6. **[#39547 — Remote Control ownership collision returns HTTP 409](https://github.com/openai/codex/issues/39547)**  
   桌面端/CLI 的所有权冲突导致 iOS 无法打开 CLI 聊天。8 个 👍 和 7 条评论表明，用户对可靠的跨设备会话交接有强烈兴趣。

7. **[#52039 — Pro 200 promotional credits depleted while weekly allowance remained](https://github.com/openai/codex/issues/52039)**  
   计费/额度记账不一致导致促销 Work/Codex 额度在包含的每周配额之前就被耗尽。八条评论反映出用户对配额透明度的敏感。

8. **[#54591 — Desktop messages stay queued with spinners for 5–10 minutes](https://github.com/openai/codex/issues/54591)**  
   即使会话处于空闲状态，消息也会停滞，直接影响桌面端的核心可用性。六条评论表明这是一个反复出现的发送路径或队列处理问题。

9. **[#55650 — MCP App first launch stalls with Browser Control attached](https://github.com/openai/codex/issues/55650)**  
   MCP 应用停滞，同时已部署的渲染确认仍然缺失。这表明 MCP 与浏览器集成中存在生命周期/就绪性缺口；5 条评论。

10. **[#57051 — Windows Desktop cannot load organization settings; bundled CLI CDN needs explicit proxy](https://github.com/openai/codex/issues/57051)**  
   一个新的 Windows 回归会阻止进入工作区，而独立 CLI 和捆绑 CLI 仍可执行。已有四条评论，该议题值得注意，因为它完全阻止了桌面端使用。

## 4. 关键 PR 进展

以下所有条目均为过去 24 小时内更新的已关闭 PR；大多数来自 `copyberry[bot]`。

1. **[#57314 — Format all Rust sources and clean up repository checks](https://github.com/openai/codex/pull/57314)**  
   将 Rust 格式化扩展到 Cargo 无法发现的已跟踪和未跟踪文件，同时将命令行保持在 Windows 限制范围内。

2. **[#57315 — Keep failed subagents from marking tasks as needing attention](https://github.com/openai/codex/pull/57315)**  
   将失败的 subagent 归类为无需用户处理，以免父任务被提升到 `Needs you` 分组中。

3. **[#57317 — Add opt-in concise hints for explicitly selected plugins](https://github.com/openai/codex/pull/57317)**  
   新增默认关闭的 `features.simplified_selection_hints`，用简洁的开发者消息替代冗长的插件说明。

4. **[#57313 — Default gateway OAuth delivery to Authorization bearer tokens](https://github.com/openai/codex/pull/57313)**  
   允许省略 `gateway_oauth.delivery`，并将令牌作为 `Authorization: Bearer …` 发送，同时保留自定义请求头/Cookie。

5. **[#57312 — Centralize Guardian root context preparation](https://github.com/openai/codex/pull/57312)**  
   将根消息准备和交接上下文选择移入 `codex-guardian-context`，简化共享上下文处理。

6. **[#57311 — Preserve tool output versions and history order in rollout traces](https://github.com/openai/codex/pull/57311)**  
   修复 trace 归约问题：该问题会将多个工具输出或压缩内容视为冲突版本。

7. **[#57304 — Surface refresh-token rejection errors during workspace routing](https://github.com/openai/codex/pull/57304)**  
   传播永久性 refresh token 失败，让用户看到重新登录的指引，而不是泛化的发现错误。

8. **[#57293 — Add GPT-6.1 Terra to model catalogs](https://github.com/openai/codex/pull/57293)**  
   将 `gpt-6.1-terra` 添加到捆绑目录和 GCP Vertex 目录，并添加 Amazon Bedrock 全球及美国跨区域变体。

9. **[#57290 — Add a plugin process host with bounded IPC and lifecycle management](https://github.com/openai/codex/pull/57290)**  
   引入一个辅助进程宿主，具备清空的环境、就绪握手、分帧的 stdin/stdout 管道、256 KiB 帧限制以及背压。

10. **[#57286 — Support `network_proxy` in managed requirements](https://github.com/openai/codex/pull/57286)**  
   在 `requirements.toml` 中添加 `[network_proxy]`，同时保留 `[experimental_network]` 作为受管环境的兼容别名。

## 5. 热门讨论

### 展示与分享

- **[#56176 — Does a fresh Codex session receive AGENTS.md? A repeatable before/after check](https://github.com/openai/codex/discussions/56176)**  
  Agent Relay Check v0.1.3 使用临时随机标记和全新会话，验证嵌套的 `AGENTS.md` 指令是否确实被加载。

- **[#57022 — two-engines-gate: Codex reviews a Claude Code commit read-only](https://github.com/openai/codex/discussions/57022)**  
  一个本地合并门禁要求：在合并前，针对确切的提交同时通过测试并获得 Codex 明确的 `ACCEPT`。

- **[#56564 — Haru Café: a company before a café, built with Codex](https://github.com/openai/codex/discussions/56564)**  
  一个展示与分享项目，探索 AI 智能体如何帮助创立一家公司，同时保留既有身份和关系。

- **[#57250 — Open dataset of real coding-agent tool-call failures, including Codex cases](https://github.com/openai/codex/discussions/57250)**  
  一个采用 CC BY 4.0 许可的工具调用失败数据集，包含轨迹、预期与实际行为，以及通过/部分通过/失败评分标准。

- **[#57011 — Blocking hallucinated and typosquatted packages with a PreToolUse hook](https://github.com/openai/codex/discussions/57011)**  
  `pkg-guard` 在执行前检查 `Bash` 和 `apply_patch` 调用，以阻止安装不存在或仿冒名称的 Python/npm 包。

- **[#57001 — AEGIS for coding-agent activity on Windows](https://github.com/openai/codex/discussions/57001)**  
  一个 Windows 桌面工作区，用于检查检测到的编码智能体活动、进程树、文件活动和 TCP 端点。

### 问答

- **[#57025 — Can app-server apply ToolPolicy when starting a thread?](https://github.com/openai/codex/discussions/57025)**  
  一位开发者希望全新的交接审查线程在没有任何工具的情况下运行，使用 `ToolPolicy.allowed_tools`，而不是依赖提示词指令。

- **[#57029 — Using a work email with Gmail plugins](https://github.com/openai/codex/discussions/57029)**  
  两个 Gmail 账户似乎已连接，但助手无法确认对第二个邮箱的访问权限，并且没有显示账户选择器。

### 想法

所提供数据中没有出现明确的 Ideas 讨论。

## 6. 功能请求趋势

这组议题主要由缺陷驱动，但反复出现的方向很明确：

- **可预测的沙箱与策略行为**——用户希望消除误报、保留有效工作目录，并对被阻止的命令给出可操作的审查路径说明。
- **远程/移动会话连续性**——更好的所有权仲裁、长会话重试，以及更清晰的跨桌面端、CLI 和 iOS 的授权行为。
- **有界的桌面资源生命周期**——对 Computer Use worker、subagent 扇出、渲染器内存以及插件/MCP 辅助进程进行更强控制。
- **跨界面一致性与诊断**——Windows 桌面端/CLI/CDN 行为、VS Code 扩展启动、认证/令牌错误，以及 trace/可观测性正确性。
- **更安全的可扩展性**——MCP 就绪握手、浏览器标签页所有权、插件 IPC 隔离，以及用于包安全的 PreToolUse hooks。
- **企业配置与模型可用性**——受管代理设置、OAuth 投递默认值，以及 GPT-6.1 Terra 等目录更新。

## 7. 开发者痛点

- **Windows 桌面可靠性仍然是最大的问题集群**：组织设置失败、EPERM/运行时错误、CDN/代理可达性、渲染器内存泄漏，以及命令执行前的沙箱阻止。
- **macOS Computer Use 和内存压力是反复出现的阻塞因素**：反复出现的报告描述了 `SkyComputerUseService` 生成风暴、`CODEX_HOME` 符号链接泄漏、subagent 扇出、V8 OOM 以及系统级 watchdog 崩溃。
- **Remote Control 和移动端访问很脆弱**：HTTP 409 所有权冲突、iOS 会话加载超时且无重试，以及移动端授权门控造成跨设备摩擦。
- **沙箱/策略不透明会侵蚀信任**：命令有时在执行前被拒绝，却没有审查记录或清晰解释，同时 hooks 可能收到错误的工作目录上下文。
- **核心桌面 UI 状态可能静默失败或响应缓慢**：消息会排队数分钟，已完成的响应会消失，审批请求会变成“no longer active”。
- **插件/MCP/浏览器生命周期问题正在显现**：首次启动停滞、渲染确认缺失，以及浏览器标签页所有权冲突，使集成显得不可靠。
- **计费和配额透明度很重要**：促销额度和每周配额可能显得不一致，促使用户提交详细报告。
- **IDE 扩展启动也不能幸免**：据报道，VS Code 扩展在 Windows 上 30 秒后出现 `renderer_ready_timeout`。

---

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区摘要 — 2026-10-27

## 1. 今日亮点
nightly 渠道发布了 **v0.67.0-nightly.20261027**，头条修复是将 AI Studio 项目支出上限错误归类为终态配额错误（使其不再伪装为瞬时故障），以及一项 ACP 改进：在权限请求中展示 MCP 服务器/工具元数据。活动仍高度集中在**代理可靠性**上：子代理轮次上限报告、通用型代理挂起和浏览器代理失败继续占据评论最多议题的前列。在 PR 方面，一批涉及终端渲染、ACP 输出和后台日志正确性的修复正在推进。

## 2. 发布
**v0.67.0-nightly.20261027.g363a13119**
- **`fix(core)`：将项目支出上限错误归类为终态配额错误** — [#29961](https://github.com/google-gemini/gemini-cli/pull/29961)。当 AI Studio 返回 HTTP 429/400、`details` 数组为空且带有支出上限消息时，`classifyGoogleError` 现在会将其视为终态错误，而不是无限重试。
- **`fix(acp)`：在权限请求中包含 MCP 服务器和工具元数据** — [#29910](https://github.com/google-gemini/gemini-cli/pull/29910)。将 `toolCall._meta['gemini-cli'].mcp` 填充为 `{ server_name, tool_name }`，并在权限选项标签中展示具体名称，改善 ACP 客户端的授权确认体验。
- 另有自动版本号提升：[#29972](https://github.com/google-gemini/gemini-cli/pull/29972)。

## 3. 热点议题

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — 子代理 MAX_TURNS 恢复被误报为 GOAL 成功（P1，13 条评论，👍2）。** `codebase_investigator` 子代理返回 `status: "success"` / `Termination Reason: "GOAL"`，但其自身结果却表明它在分析任何内容之前就达到了轮次上限。这是今日互动量最高的议题，对任何将代理自动化的人来说都是严重的信任问题——静默截断比可见的失败更糟。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — 通用型代理挂起（P1，8 条评论，👍8）。** 每当 CLI 交由通用型代理处理时，它就会无限期挂起，甚至创建文件夹这类琐碎操作也不例外；变通方法是让模型不要委派。这是本组中反响最高的议题，表明影响面很广。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — 零依赖操作系统沙箱与执行后意图路由（P2，9 条评论）。** 提议充分利用 Gemini 3 对 bash 的原生亲和性（`grep`、`sed`、`awk`），同时通过沙箱保持安全性。这是 CLI 在能力与安全之间取得平衡的一个重要架构方向。

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini 对技能和子代理的使用不足（P2，7 条评论）。** 虽是轶事，但引起广泛共鸣：自定义技能和子代理只有在明确指示时才会被调用，从不会主动使用，即使任务明显匹配（例如 `gradle` 技能）。这说明问题在于路由/选择，而非能力缺失。

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — EPIC：评估 AST 感知的文件读取、搜索与映射（P2，7 条评论）。** 跟踪 AST 感知工具能否减少因错位读取而产生的轮次，并降低 token 噪声。配套议题 [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) 和 [#22747](https://github.com/google-gemini/gemini-cli/issues/22747) 探索 `ast-grep`、`tilth` 和 `glyph` 作为候选方案。

6. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — 浏览器子代理在 Wayland 下失败（P1，4 条评论）。** 浏览器代理在 Wayland 会话中直接失败，却报告 `Termination Reason: GOAL`——这一平台兼容性缺口与 #22323 中的报告准确性问题如出一辙。

7. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 工具数超过 128 时出现 400 错误（P2，3 条评论）。** 当作用域内工具过多时，CLI 会触发 HTTP 400；报告者希望代理能更智能地限制启用的工具。随着 MCP 服务器采用率上升，这一点值得关注。

8. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — 代理应停止/劝阻破坏性行为（3 条评论，👍1）。** 在存在更安全路径时，模型仍会动用 `git reset` 或 `--force`，并且围绕数据库/资源修改缺乏护栏。这是一个安全提醒请求，而非硬性阻断。

9. **[#23571](https://github.com/google-gemini/gemini-cli/issues/23571) — 模型在随机位置创建临时脚本（P2，3 条评论）。** 仅使用 Shell 的配置会导致模型将编辑脚本散落在各个目录中，在干净提交前增加清理开销。这是关于工作区整洁性的抱怨，且具有实际 CI 影响。

10. **[#22465](https://github.com/google-gemini/gemini-cli/issues/22465) — 创建 Vite 应用时卡在交互式提示符（P2，2 条评论）。** 代理在脚手架提示处停滞；提议的修复是新增行为评估并调整提示词。这体现了反复出现的“非交互式代理遇到交互式 CLI”一类失败。

*同样值得关注：* [#22598](https://github.com/google-gemini/gemini-cli/issues/22598)（通过 `/chat share` 暴露子代理轨迹）、[#21763](https://github.com/google-gemini/gemini-cli/issues/21763)（`/bug` 报告遗漏子代理上下文）、[#20079](https://github.com/google-gemini/gemini-cli/issues/20079)（符号链接的代理文件未被发现）、[#22267](https://github.com/google-gemini/gemini-cli/issues/22267)（浏览器代理忽略 `settings.json` 覆盖配置）。

## 4. 关键 PR 进展

1. **[#29961](https://github.com/google-gemini/gemini-cli/pull/29961) — 将支出上限错误归类为终态（已合并至 nightly）。** 为 AI Studio 用户避免无效重试和含糊的配额失败。

2. **[#29910](https://github.com/google-gemini/gemini-cli/pull/29910) — ACP 权限请求携带 MCP 服务器/工具元数据（已合并）。** 为集成 ACP 的客户端提供更好的授权提示；元数据缺失时可优雅回退。

3. **[#29958](https://github.com/google-gemini/gemini-cli/pull/29958) — 为 `AnsiOutput` 结果向 ACP 客户端发送 shell 输出。** 此前由于 `shell.ts` 优先使用 PTY 快照，`toToolCallContent` 会返回 `null`，导致 ACP 客户端收到空的 `tool_call_update` 事件。

4. **[#29884](https://github.com/google-gemini/gemini-cli/pull/29884) — 在 Android/Termux 上为 ripgrep 允许受信任的系统路径。** 扩展 `isTrustedSystemPath()`，使 `pkg install ripgrep` 安装的二进制文件能在 `grep_search` 下解析并执行。这对移动端/嵌入式开发者支持是一个积极信号。

5. **[#29960](https://github.com/google-gemini/gemini-cli/pull/29960) — 遥测 `SERVICE_VERSION` 使用 CLI 版本，而非 Node 运行时版本。** 修复 [#29893](https://github.com/google-gemini/gemini-cli/issues/29893)；对任何基于 OpenTelemetry 数据做按版本分段可观测性的人都至关重要。

6. **[#29905](https://github.com/google-gemini/gemini-cli/pull/29905) — 处理将歧义宽度字符渲染为宽字符的终端。** 修复 [#24181](https://github.com/google-gemini/gemini-cli/issues/24181)，覆盖 macOS Terminal.app、iTerm2、VTE、WezTerm 和 `screen cjkwidth` 配置。

7. **[#29904](https://github.com/google-gemini/gemini-cli/pull/29904) — 限制终端宽度变化时内联历史记录的重渲染。** 直接针对 [#21924](https://github.com/google-gemini/gemini-cli/issues/21924) 中跟踪的闪烁/性能问题。

8. **[#29947](https://github.com/google-gemini/gemini-cli/pull/29947) 与 [#29948](https://github.com/google-gemini/gemini-cli/pull/29948) — 后台日志跟踪修复。** 第一个 PR 在单行超过 64 KB 窗口时保留部分尾部内容（此前所有输出都会被静默丢弃）；第二个 PR 让后台输出读取遵循取消操作，而不是在中止后仍继续完成。

9. **[#29966](https://github.com/google-gemini/gemini-cli/pull/29966) — 在 `getDiffContextSnippet` 中先规范化 CRLF 再执行 diff。** 解决编辑 Windows/CRLF 文件时出现的 diff 不匹配问题：其中 `currentContent` 被规范化为 LF，而 `finalContent` 又被转换回去。

10. **[#29959](https://github.com/google-gemini/gemini-cli/pull/29959) — 可配置的基于数值评分的模型路由。** 新增用户自定义的评分区间到模型的映射，同时保留默认路由——这是迈向成本/延迟调优的显著一步。

*另有进行中的 PR：* [#29951](https://github.com/google-gemini/gemini-cli/pull/29951)（P0：在集成测试中重试瞬时 VS Code 下载失败）、[#29965](https://github.com/google-gemini/gemini-cli/pull/29965)（`ExpandableText` 中的 Unicode 代理对截断）、[#29886](https://github.com/google-gemini/gemini-cli/pull/29886)（从 steering-signature 评估检测中排除测试文件）。

## 5. 热点讨论
本期未提供讨论数据，因此省略本节。

## 6. 功能请求趋势

- **子代理可观测性与控制** — 到目前为止最大的聚类：通过 `/chat share` 暴露轨迹（[#22598](https://github.com/google-gemini/gemini-cli/issues/22598)）、在 `/bug` 报告中包含子代理上下文（[#21763](https://github.com/google-gemini/gemini-cli/issues/21763)）、纠正误导性的终止原因（[#22323](https://github.com/google-gemini/gemini-cli/issues/22323)），并让子代理发现机制可通过 `settings.json` 配置（[#18285](https://github.com/google-gemini/gemini-cli/issues/18285)、[#20079](https://github.com/google-gemini/gemini-cli/issues/20079)）。
- **通过 AST 感知工具提升上下文效率** — 一个协同 EPIC（[#22745](https://github.com/google-gemini/gemini-cli/issues/22745)、[#22746](https://github.com/google-gemini/gemini-cli/issues/22746)、[#22747](https://github.com/google-gemini/gemini-cli/issues/22747)）加上“得体抽取”（tactful extraction）式精准读取（[#19561](https://github.com/google-gemini/gemini-cli/issues/19561)），目标是对标每轮约 36.6k token 的基线。
- **持久化任务跟踪** — 用基于文件的 CRUD 取代上下文内的 `WriteToDo`，以抵御上下文腐化和会话边界（[#18836](https://github.com/google-gemini/gemini-cli/issues/18836)、[#21000](https://github.com/google-gemini/gemini-cli/issues/21000)）。
- **沙箱化但不牺牲 bash 流畅性** — 零依赖操作系统沙箱与执行后意图路由（[#19873](https://github.com/google-gemini/gemini-cli/issues/19873)）。
- **作用域策略与配置** — 按工作区设置策略而非全局策略（[#18397](https://github.com/google-gemini/gemini-cli/issues/18397)）；可配置的数值模型路由（[#29959](https://github.com/google-gemini/gemini-cli/pull/29959)）。
- **代理自我认知** — 准确了解 CLI 标志、快捷键和自我执行（[#21432](https://github.com/google-gemini/gemini-cli/issues/21432)）。
- **浏览器代理韧性** — 会话接管和锁恢复而非快速失败（[#22232](https://github.com/google-gemini/gemini-cli/issues/22232)），并正确遵循 `settings.json`（[#22267](https://github.com/google-gemini/gemini-cli/issues/22267)）。

## 7. 开发者痛点

- **代理挂起且无诊断信息。** 通用型代理委派会无限期停滞（[#21409](https://github.com/google-gemini/gemini-cli/issues/21409)），浏览器子代理在 Wayland 上失败却仍报告 `GOAL`（[#21983](https://github.com/google-gemini/gemini-cli/issues/21983)）。用户得不到任何可操作的信号。
- **误导性的成功语义。** 评论最多的单一议题是一个报告错误：达到 `MAX_TURNS` 却显示为 `status: "success"`（[#22323](https://github.com/google-gemini/gemini-cli/issues/22323)）。再结合失败时出现 `GOAL` 终止原因，这会侵蚀对代理输出的信任。
- **技能/子代理利用不足。** 能力已经具备，但很少被自主选择（[#21968](https://github.com/google-gemini/gemini-cli/issues/21968)）——用户必须微观管理调用。
- **工具数量上限。** 超过约 128 个工具会触发 HTTP 400，且没有优雅降级（[#24246](https://github.com/google-gemini/gemini-cli/issues/24246)），随着 MCP 服务器增多，这种情况越来越可能发生。
- **破坏性命令卫生。** 不必要的 `git reset` / `--force` 使用和无防护的资源变更（[#22672](https://github.com/google-gemini/gemini-cli/issues/22672)），以及散落工作区的临时脚本（[#23571](https://github.com/google-gemini/gemini-cli/issues/23571)）。
- **交互式提示破坏自动化。** 脚手架创建 Vite 应用时在提示处死锁（[#22465](https://github.com/google-gemini/gemini-cli/issues/22465)）——这是非交互式代理流程中反复出现的模式。
- **跨平台粗糙边缘。** Termux 上 ripgrep 的信任问题（[#29884](https://github.com/google-gemini/gemini-cli/pull/29884)）、Windows CRLF diff（[#29966](https://github.com/google-gemini/gemini-cli/pull/29966)）、东亚歧义宽度渲染（[#29905](https://github.com/google-gemini/gemini-cli/pull/29905)）以及终端尺寸调整闪烁（[#21924](https://github.com/google-gemini/gemini-cli/issues/21924)）共同表明，终端/操作系统可移植性仍然是用户持续承担的负担。
- **评估不稳定。** 不稳定的 steering 和内部项目评估（“bleed”）降低了对质量趋势跟踪的信心（[#23313](https://github.com/google-gemini/gemini-cli/issues/23313)、[#23166](https://github.com/google-gemini/gemini-cli/issues/23166)）。

---

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — 社区文摘
**日期：** 2026-10-27 · **来源：** [github/copilot-cli](https://github.com/github/copilot-cli)

---

## 1. 今日亮点

今天的活动以可靠性工程为主，而非新增功能面：发布了一个精简的补丁版本（**v1.0.104-4**），但未附逐项变更日志；过去 24 小时内有 50 个 issue 更新，而 **零个 pull request** 有动静。最突出的两个主题是**会话生命周期脆弱性**（恢复时 OOM、锁获取超时、会话无法恢复）以及跨桌面、无头和企业注册表配置的 **MCP/OAuth 集成故障**。一项长期悬而未决的问题终于解决：激进 HTTP 429 重试 bug（#2760）在超过六个月后已**关闭**。

---

## 2. 发布

**v1.0.104-4** —— 在过去 24 小时内发布。
- 变更日志仅限于"修复与改动"；未附逐项 bug 列表或 PR 引用。
- 过去 24 小时窗口内未出现对应的 PR，因此具体修复内容无法从本数据中佐证。
- 实际上：可以将其视为一个不透明的补丁构建。锁定在 1.0.103 的团队（参见 [#5253](https://github.com/github/copilot-cli/issues/5253) 会话锁超时）或许可以测试 104-4 作为候选缓解方案，但发布说明并未提供确认。

---

## 3. 热点 Issue

1. **[#4024](https://github.com/github/copilot-cli/issues/4024) — 语音模式：所有内置 ASR 模型静默失败（14 条评论）** `OPEN` `area:models`
   本窗口内互动量最高的线程。`/voice` 能正确录制音频，但选择器中的每个模型都返回空的转写结果，问题追溯到 Foundry Local Core 中 `nemotron_speech`（RNNT）的 MultiModalProcessor 路由 bug。值得注意的是重现质量很高（以原始 PulseAudio 捕获作为证据），且属于静默失败而非崩溃。

2. **[#2760](https://github.com/github/copilot-cli/issues/2760) — 为 429 响应实现正确的 HTTP 重试逻辑（8 条评论，2 👍）—— 已关闭**
   今日最受瞩目的关闭项。Copilot CLI 之前会立即重试 429 而没有退避，造成每分钟 20 次以上的重试风暴。关闭意味着确实有修复，但缺少关联 PR 或发布说明，用户只能猜测哪个构建包含该修复。

3. **[#4905](https://github.com/github/copilot-cli/issues/4905) — 桌面应用：会话在启动后几分钟内死亡（7 条评论，4 👍）** `OPEN` `triage`
   "GitHub credential registration is no longer available for this session" 使 `github-mcp-server` 目录失效，并变为致命错误。影响很大，因为它命中了默认的桌面路径，而报告者的证据（有效的 `gh auth status`、有效的已导出 `GH_TOKEN`）使其难以被归为环境错误。

4. **[#4251](https://github.com/github/copilot-cli/issues/4251) — 恢复大型会话时 OOM／占满一个核心约 70 分钟（5 条评论，1 👍）** `OPEN` `area:sessions`
   一个隔离得非常干净的回退：在相同机器和会话上进行受控 A/B 对比，显示 1.0.74 的内存占用约为 1.0.73 的 3–4 倍。这是回退二分定位的典型范例，也是长期会话用户的直接阻碍。

5. **[#5253](https://github.com/github/copilot-cli/issues/5253) — CLI 1.0.103：持续性会话锁超时与任务无响应（4 条评论）** `OPEN` `area:sessions` `area:agents`
   `session lock unavailable during is_session_generation_current` 导致会话仍然存活但 UI 无响应。提交于 2026-10-25，是本窗口内最新的高严重度回退，也可能是 1.0.104-4 补丁的最强候选。

6. **[#4017](https://github.com/github/copilot-cli/issues/4017) — 桌面端 MCP OAuth：非第一方 HTTP 服务器取消宿主令牌，从不启动浏览器流程（4 条评论，3 👍）** `OPEN` `triaged`
   Atlassian、incident.io 等类似服务器根本无法连接——没有弹窗、没有报错。"无认证、无报错、无弹窗"的失败模式正是企业团队调试成本高昂的原因。

7. **[#3976](https://github.com/github/copilot-cli/issues/3976) — 原生 `tgrep` 索引器在大型 monorepo 上 OOM 杀死宿主（4 条评论）** `OPEN` `area:tools`
   这个 Rust trigram 索引器守护进程在会话启动时生成，且没有内存上限。对 monorepo 团队至关重要，也值得注意，因为这是一个实验性开关（`copilot_cli_tgrep`）却影响到了生产用户。

8. **[#4026](https://github.com/github/copilot-cli/issues/4026) — Windows：原生运行时反复崩溃，自 2026 年 5 月起未解决（4 条评论）** `OPEN` `area:platform-windows`
   至少在四个版本中都有不可预测的崩溃（1.0.15 → 1.0.53+）。长期存在、平台特有、且仍未被分类——这是 Windows 对等性落后于 macOS/Linux 的持续信号。

9. **[#4537](https://github.com/github/copilot-cli/issues/4537) — ACP 模式再次自动批准工具调用；自 1.0.81-1 起未发送 `session/request_permission`（2 条评论，2 👍）** `OPEN` `area:permissions`
   这是早期修复（#845）的回退：在 `--acp` 模式下，shell 命令、编辑和删除操作会在无人值守下执行。本组中最具安全相关性的未关闭 issue——客户端没有机会提示，也没有任何日志记录。

10. **[#5149](https://github.com/github/copilot-cli/issues/5149) — Claude Opus 5.5：32k `maxOutputTokens` 在 max/xhigh 推理下产生空轮次；自动续跑对每次计费（2 条评论）** `OPEN` `area:non-interactive` `area:models`
    推理 token 计入硬性 32k 上限，因此长任务返回空轮次，而自动续跑又对此计费。一个成本正确性 bug，对 CI／无头用户的配额有直接影响。

*同样活跃但不在前十的：* [#4486](https://github.com/github/copilot-cli/issues/4486) 编辑权限提示超时、[#4392](https://github.com/github/copilot-cli/issues/4392) 认证重建后遗留孤儿 stdio MCP 进程、[#3679](https://github.com/github/copilot-cli/issues/3679) BYOK Azure 429 在约 0.15 秒内耗尽重试、[#5242](https://github.com/github/copilot-cli/issues/5242) inotify `ENOSPC` 放弃会话队列。

---

## 4. 关键 PR 进展

**过去 24 小时内没有 pull request 更新（0 项）。** 本窗口内无可报告的合并或审查活动。

作为代码变动的替代指标，唯一的发布信号是 **[#2760](https://github.com/github/copilot-cli/issues/2760)**（正确的 429 重试/退避）的关闭，它是重试行为系列中的最后一项，并与仍未关闭的 BYOK 变体 **[#3679](https://github.com/github/copilot-cli/issues/3679)** 相呼应。issue 关闭与可见 PR 活动之间的落差也解释了为何 v1.0.104-4 的变更日志缺乏信息量——该发布在本数据集中无法追溯到公开 PR。

---

## 5. 热门讨论

本窗口未提供讨论数据，故此节省略。

---

## 6. 功能需求趋势

- **模型与提供方灵活性（数量最高）。** 按 agent 选择提供方，使同一会话中的两个 agent 可以访问不同端点（[#4703](https://github.com/github/copilot-cli/issues/4703)）；ACP 客户端完全无法列出或选择模型（[#4880](https://github.com/github/copilot-cli/issues/4880)）；`auto` 模型静默强制 `reasoningEffort: null` 且无法覆盖（[#4560](https://github.com/github/copilot-cli/issues/4560)）；橡皮鸭审查应选择非同家族的审查者（[#4380](https://github.com/github/copilot-cli/issues/4380)）。
- **重试与限流策略应可配置。** 除了已关闭的 #2760 之外，用户希望 BYOK/Azure OpenAI 的重试次数和退避可调，而不是硬编码的 5 次近乎即时重试（[#3679](https://github.com/github/copilot-cli/issues/3679)）。
- **无头（`-p`）与 ACP 模式与交互式 TUI 的对等性。** 仓库级 `enabledPlugins` 在 `-p` 模式下被忽略（[#4507](https://github.com/github/copilot-cli/issues/4507)）；`skill` 工具在无头运行中偶发无法解析（[#4838](https://github.com/github/copilot-cli/issues/4838)）；`session/prompt` 无条件中止后台子 agent（[#4555](https://github.com/github/copilot-cli/issues/4555)）；ACP 中权限请求出现回退（[#4537](https://github.com/github/copilot-cli/issues/4537)）。
- **MCP 企业就绪度。** 自定义 MCP 注册表在 VS Code 中可访问，但 CLI 无法访问（[#4949](https://github.com/github/copilot-cli/issues/4949)）；有状态服务器的 OAuth 探测在持有有效存储令牌时返回 HTTP 400（[#5014](https://github.com/github/copilot-cli/issues/5014)）；Windows 上 OAuth 初始化失控（[#5134](https://github.com/github/copilot-cli/issues/5134)）。
- **能在空闲会话中存活的权限 UX。** 编辑批准提示在过夜或并行会话中超时（[#4486](https://github.com/github/copilot-cli/issues/4486)）。

---

## 7. 开发者痛点

- **会话持久性是第一大系统性风险。** 前十中有四个 issue 涉及同一生命周期：恢复时 OOM（[#4251](https://github.com/github/copilot-cli/issues/4251)）、锁获取死锁（[#5253](https://github.com/github/copilot-cli/issues/5253)）、因字符串计数器被掩码而无法恢复的会话（[#5023](https://github.com/github/copilot-cli/issues/5023)），以及模型前缀不匹配阻止恢复（[#4282](https://github.com/github/copilot-cli/issues/4282)）。对于任何运行长期会话或编程式会话的人来说，这是工具与负担之间的差别。
- **静默失败是默认的失败模式。** 空的语音转写（[#4024](https://github.com/github/copilot-cli/issues/4024)）、无弹窗无报错的 MCP OAuth（[#4017](https://github.com/github/copilot-cli/issues/4017)），以及无人应答且日志中毫无记录的权限请求（[#4537](https://github.com/github/copilot-cli/issues/4537)），都会因错误归因而浪费数小时调试时间。
- **后台守护进程导致的本地资源耗尽。** `tgrep` OOM 杀死宿主（[#3976](https://github.com/github/copilot-cli/issues/3976)）、每次由认证驱动的客户端重建都会遗留孤儿 stdio MCP 子进程（[#4392](https://github.com/github/copilot-cli/issues/4392)），以及共享 Linux 主机上 inotify `ENOSPC` 静默放弃队列（[#5242](https://github.com/github/copilot-cli/issues/5242)）。
- **回退随版本发布却无可二分定位的变更日志。** 多份报告将故障精确定位到具体版本（1.0.73→1.0.74、1.0.81-1、1.0.90-0、1.0.103），但像 v1.0.104-4 的"修复与改动"这样的发布说明，让锁定版本的团队无从评估。
- **平台对等性差距。** 自 2026 年 5 月起未关闭的 Windows 原生运行时崩溃（[#4026](https://github.com/github/copilot-cli/issues/4026)）以及 Windows 特有的 OAuth 循环（[#5134](https://github.com/github/copilot-cli/issues/5134)）持续存在，而 macOS/Linux 的修复却不断落地。
- **输出 token 上限带来的成本意外。** Opus 5.5 的空轮次在自动续跑时仍会计费（[#5149](https://github.com/github/copilot-cli/issues/5149)），而未退避的 429 重试则消耗配额（[#3679](https://github.com/github/copilot-cli/issues/3679)）——两者都在侵蚀对无头/CI 使用场景的信任，因为这些场景的支出无人看管。

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区简报 — 2026-10-27

## 今日亮点

免费层级访问问题主导了今日讨论：一个关于配额/重置失败的汇总帖（[#59387](https://github.com/anomalyco/opencode/issues/59387)）正在收集重复报告，同时两个高关注度主题帖已关闭——长期存在的剪贴板缺陷（[#4283](https://github.com/anomalyco/opencode/issues/4283)，159 条评论）以及基于 VPN 的免费模型漏洞利用（[#34344](https://github.com/anomalyco/opencode/issues/34344)）。平台方面，Desktop GUI 插件 SDK 已落地（[#55246](https://github.com/anomalyco/opencode/pull/55246)），并附带后续基础原语（[#59462](https://github.com/anomalyco/opencode/pull/59462)），同时还有更丰富的模型定价元数据（[#59460](https://github.com/anomalyco/opencode/pull/59460)）。服务器用户名认证的回滚（[#59444](https://github.com/anomalyco/opencode/pull/59444)）表明 v1→v2 兼容性调整仍在持续。

## 热点 Issue

1. **[#4283](https://github.com/anomalyco/opencode/issues/4283) — 复制到剪贴板无法正常工作（已关闭）** — 本批次中参与度最高的 issue（159 条评论，134 👍），自 2025 年 11 月起开放，最终关闭。TUI 中的选择/复制是核心工作流；反应数量表明单个平台级剪贴板缺陷会带来多大摩擦。

2. **[#56324](https://github.com/anomalyco/opencode/issues/56324) — 官方 TUI 中免费模型失败，提示 "free tier can only be used from within OpenCode"（已关闭，严重性：高）** — 71 条评论，30 👍。v2.0.32 上的 Windows 用户在官方应用内部遭遇 entitlement/telemetry 检查失败而被阻断。该问题已关闭，但“官方客户端被判定为非官方”的失败模式对于门控逻辑而言是一个严重的信任问题。

3. **[#34344](https://github.com/anomalyco/opencode/issues/34344) — 免费模型无限使用漏洞（已关闭）** — 64 条评论。按 IP 设置的速率限制意味着 VPN 轮换会重置配额；报告者将其自动化。这与当前配额收紧及由此产生的免费层级故障相关。

4. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — [zen] 免费层级问题：请先阅读本文，然后在此发布你的报告（开放）** — 维护者创建的聚合帖（10 条评论，3 👍），涵盖“Free usage exceeded”、永不重置的限制以及 Desktop 版本不匹配。该帖的存在证实了重复报告的数量。

5. **[#6912](https://github.com/anomalyco/opencode/issues/6912) — Ctrl+C 在 Windows 上无法禁用鼠标跟踪（已关闭）** — 14 条评论，21 👍。退出后终端仍继续发送 SGR 鼠标序列，导致 shell 无法使用。这是经典的终端卫生缺陷，但用户影响巨大。

6. **[#32825](https://github.com/anomalyco/opencode/issues/32825) — OPENCODE_CONFIG_DIR 在 v2 中替换全局配置（开放）** — 10 条评论。v2/core 加载器将该变量视为 XDG 全局配置的替代项，而不是附加目录。这是迁移过程中一次静默的配置语义变更。

7. **[#45856](https://github.com/anomalyco/opencode/issues/45856) — v2 serve：Basic Auth 凭据始终返回 401（已关闭）** — 9 条评论。`OPENCODE_SERVER_USERNAME`/`PASSWORD` 被拒绝，导致浏览器登录提示无限循环。随后的回滚（[#59444](https://github.com/anomalyco/opencode/pull/59444)）从 `ServerOptions` 中移除了 `username`，因此预计会有后续破坏。

8. **[#36990](https://github.com/anomalyco/opencode/issues/36990) — core：恢复 v2 中非实验性环境变量兼容性（开放）** — 由机器人维护的总括 issue（8 条评论），跟踪 `flag.ts` 中声明但 v2 没有活跃使用方的 v1 环境变量。这是 v2 对等性债务正在被系统性跟踪的最清晰信号。

9. **[#54603](https://github.com/anomalyco/opencode/issues/54603) — npm 包在 Linux 和 macOS 上将原生二进制文件打包为 `bin/opencode.exe`（开放）** — 7 条评论，已复现。看似只是外观问题，但会破坏按名称解析二进制文件的工具；官方 `install.sh` 已经使用无后缀名称。

10. **[#58354](https://github.com/anomalyco/opencode/issues/58354) — 包含 NUL 字节的 Shell 工具输出会导致会话变砖（开放，已复现）** — 3 条评论。工具输出中的 NUL 字节会产生提供方 400（“String must not contain null bytes”），使会话无法恢复。这是工具输出边界上的清洗缺口。

## 关键 PR 进展

1. **[#55246](https://github.com/anomalyco/opencode/pull/55246) — feat(plugin)：添加 OpenCode Desktop GUI 插件（已关闭/已落地）** — 插件可以在服务器和 `./tui` 条目之外提供 `./gui-experimental` 导出；OpenCode 自身功能也作为基于同一 SDK 的插件构建，并且 **Settings > Plugins** 会同时列出两者。

2. **[#59462](https://github.com/anomalyco/opencode/pull/59462) — feat(plugin)：GUI toast、ask 对话框、shell 标签页和 composer 文本（开放）** — 补齐将 TUI 插件（blindfold、agent-meetup、quick-links、pr-link、keep-going）移植到 Desktop 所需的 SDK 缺口。

3. **[#59460](https://github.com/anomalyco/opencode/pull/59460) — feat(core)：在模型上携带 models.dev v2 定价（开放）** — 扩展 `Model.Info.cost`，以覆盖音频/图像/视频定价、网络搜索费用、推理输出、按 TTL 的缓存写入、缓存存储，以及区域/日期/服务乘数。

4. **[#59190](https://github.com/anomalyco/opencode/pull/59190) — feat(protocol)：实验性图像与决策生成端点（开放）** — 契约优先：请求可被解码，但随后会明确以未实现错误失败，这样客户端便可基于稳定的 HTTP 形状进行构建。

5. **[#58782](https://github.com/anomalyco/opencode/pull/58782) — feat(core)：添加 Claude Agent 提供方（开放，needs:compliance）** — 通过 Agent SDK 使用用户自己的 Claude Code 登录来运行 Claude 模型，可在 `/connect` 中选择。因合规审查而受阻。

6. **[#59457](https://github.com/anomalyco/opencode/pull/59457) — feat(core)：从 agents、/schedule 和实验性 API 调度提示词（开放）** — 支持“每小时检查 CI，持续 8 小时”这类会话；受 `experimental.schedules` 控制，堆叠在 #59401 的存储/计时器工作之上。

7. **[#58586](https://github.com/anomalyco/opencode/pull/58586) — perf(app)：打开会话时无需下载 read 工具图像（开放）** — 时间线通过路径加载图像，而不是为每个读取的图像传输 base64，使用新的实验性端点。直接针对大型会话的加载时间。

8. **[#59459](https://github.com/anomalyco/opencode/pull/59459) — fix(server)：将响应编码失败报告为服务器错误（开放）** — 模式不匹配此前会表现为 `400 InvalidRequestError`（`kind: "Body"`）；现在会正确归类为 5xx，而不是归咎于客户端。

9. **[#59444](https://github.com/anomalyco/opencode/pull/59444) — 回滚“fix(server): honor OPENCODE_SERVER_USERNAME for serve and --server”（已关闭）** — 回滚 #59379；从 `ServerOptions` 中移除 `username`，并停止遵循该环境变量。明确不向后兼容——值得对照 #45856 和 #36990 跟踪。

10. **[#59403](https://github.com/anomalyco/opencode/pull/59403) — refactor(nix)：从 bun.lock 获取依赖（开放）** — 从 lockfile 构建 bun2nix 的离线缓存，消除按平台维护 `node_modules` 哈希的工作。

**同时进行中：** [#59072](https://github.com/anomalyco/opencode/pull/59072) 将实验性 SDK 方法归组到 `client.experimental` 下；[#59445](https://github.com/anomalyco/opencode/pull/59445) 向 Promise 客户端添加异步工具和 `generateObject`；[#59448](https://github.com/anomalyco/opencode/pull/59448) 使模型重试预算可通过环境变量配置；[#59446](https://github.com/anomalyco/opencode/pull/59446) 在系统提示中声明输入模态；[#59447](https://github.com/anomalyco/opencode/pull/59447) 将 76 个 `dev` 提交合并到 `beta` 以解除 e2e 阻塞。

## 功能请求趋势

- **免费层级透明度与计量** — [#57540](https://github.com/anomalyco/opencode/issues/57540) 要求文档化空闲时间行为、池范围以及剩余额度；持续不断的重置投诉进一步表明，配额机制实际上没有文档。
- **V1→V2 配置对等性** — [#36990](https://github.com/anomalyco/opencode/issues/36990)（环境变量）和 [#32825](https://github.com/anomalyco/opencode/issues/32825)（配置目录语义）指向更广泛的期望：现有设置在 v2 过渡期间应继续工作。
- **可扩展性与 UI 定制** — Desktop GUI 插件（#55246、#59462）、响应式 TUI 插件槽位（[#59458](https://github.com/anomalyco/opencode/issues/59458)）以及 Claude Agent 提供方（#58782）都扩展了第三方可构建的内容。
- **本地状态运维工具** — [#54618](https://github.com/anomalyco/opencode/issues/54618) 请求为 v2 存储提供离线 SQLite 检查、经验证的备份以及 vacuum。
- **更丰富的模型元数据** — 定价深度（#59460）、模态声明（#59446）以及图像/决策端点（#59190）反映了对一等公民多模态和成本感知路由的需求。
- **自动化与调度** — #59457 引入提示词调度，这是交互式会话之外的新方向。

## 开发者痛点

- **免费层级配额与重置逻辑是最大的挫败来源。** 报告涵盖“首条消息就超限”（[#58550](https://github.com/anomalyco/opencode/issues/58550)）、计数器卡在 61k–84k 秒（[#59116](https://github.com/anomalyco/opencode/issues/59116)、[#59118](https://github.com/anomalyco/opencode/issues/59118)、[#59307](https://github.com/anomalyco/opencode/issues/59307)）、Desktop 与 CLI 不一致（[#59112](https://github.com/anomalyco/opencode/issues/59112)）以及非英语报告（[#59111](https://github.com/anomalyco/opencode/issues/59111)）。反复的机器人套话回复加剧了不满。
- **Windows 终端/桌面可靠性** 仍是反复出现的主题：剪贴板（#4283）、Ctrl+C 后鼠标跟踪仍启用（#6912），以及官方应用授权检查失败（#56324）。
- **V2 迁移回归与 API 变动** — 环境变量语义（#36990）、配置目录处理（#32825）、Basic Auth（#45856）以及认证回滚（#59444）意味着团队必须两次重新验证部署配置。
- **列表命令中的冷启动竞态条件** — [#54654](https://github.com/anomalyco/opencode/issues/54654)（`plugin list` 首次调用为空）是 `mcp.list`、`skill.list`、`agent.list` 和 `model.list` 中同类模式的最新案例，暗示存在共享的缓存/生命周期缺陷。
- **长时间运行的 agent 会话失去可观测性** — [#58570](https://github.com/anomalyco/opencode/issues/58570)：助手在任务中途停止渲染文本，继续调用工具，并且不产生最终报告。
- **工具 I/O 健壮性** — NUL 字节使会话变砖（#58354），并且内部编码失败在 #59459 之前被误报为客户端错误。
- **打包/安装正确性** — Unix 上的 `.exe` 后缀（#54603）以及一个 404 的 MCP 包引用（[#58571](https://github.com/anomalyco/opencode/issues/58571)）表明安装路径仍然脆弱。
- **Issue 跟踪器中的支持流量** — 账单/退款请求（[#59117](https://github.com/anomalyco/opencode/issues/59117)、[#59436](https://github.com/anomalyco/opencode/issues/59436)）表明缺少自助升级渠道。

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区文摘 — 2026-10-27

## 1. 今日亮点

过去 24 小时没有新版本发布，但仓库经历了大量维护工作：长期存在的 Windows UX 讨论帖（#7547）仍以 90 条评论位居互动量最高的问题，同时一批 bug 修复 PR 落地，涉及 MCP 生命周期安全、工具搜索排名、undici 安全版本锁定和 TUI 渲染。两个 `prio:p0` 事项仍处于开放状态——Bedrock 的 16 个可选参数限制（#11416）和 OpenCode 协议专用端点（#11132）——与此同时，对无界压缩状态（#11297）和长会话凭据陈旧（#11221）的担忧也在增加。

## 2. 发布

过去 24 小时无发布。

## 3. 热门 Issue

1. **[#7547](https://github.com/earendil-works/pi/issues/7547) — [Windows] 你如何在 Windows 上使用 Pi？遇到了哪些问题？**（90 条评论，2 👍）
   事实上的 Windows 集中讨论帖。作者认为 Windows 开发者数量“多到爆炸”，但受支持的运行路径太多，难以判断该把精力投向哪里——修 bug、写文档，还是把某些路径从核心推入扩展。社区互动量遥遥领先，也最清楚地表明：Windows 功能对齐是一项战略决策，而不只是一个 bug 队列。

2. **[#11297](https://github.com/earendil-works/pi/issues/11297) — 压缩文件列表在多次压缩之间无界增长**（5 条评论）
   `extractFileOperations` 会从上一轮压缩的 `details.readFiles`/`modifiedFiles` 初始化每一轮新压缩的 `fileOps`，因此 `<read-files>`/`<modified-files>` 列表会无限累积。这是一个缓慢发酵的上下文膨胀 bug：长会话会在质量和成本上悄然退化。仍未关闭，未关联修复 PR。

3. **[#11221](https://github.com/earendil-works/pi/issues/11221) — Pi 在长时间运行的会话中不会获取刷新后的 AWS 凭据**（4 条评论）
   即使后台脚本已在磁盘上刷新凭据，Bedrock 会话仍会在数小时后因 `ExpiredTokenException` 失败。这直接阻碍了重度用户依赖的“让 agent 全天运行”工作流。目前还没有 👍，但对企业/Bedrock 用户有很高的实际影响。

4. **[#11335](https://github.com/earendil-works/pi/issues/11335) — MCP HTTP JSON 响应绕过 `maxMessageBytes`**（4 条评论）
   在限制为 512 字节时，2 KB 的工具描述会通过 JSON 路径被接受，但在 SSE 路径上会被正确拒绝。安全限制上的不一致是 MCP 服务器的正确性和 DoS 攻击面问题；这种不对称表明两种传输方式在验证代码上出现了分歧。

5. **[#10470](https://github.com/earendil-works/pi/issues/10470) — RPC `new_session`/`switch_session`/`fork`/`clone` 会两次发出 `session_start`**（7 条评论）
   在 RPC 模式下，扩展会为新会话收到重复的 `session_start` 事件。重复的生命周期事件是扩展状态被双重初始化的经典来源；这是本组中评论最多的开放 API 正确性问题。

6. **[#10259](https://github.com/earendil-works/pi/issues/10259) — 公开工具展示 API，或内置单行工具模式**（6 条评论，2 👍）
   请求提供仅用于展示的 API，以控制*所有*工具行的渲染（内置工具、MCP、codemode、自定义工具），或者提供紧凑的单行模式，显示名称、调用摘要和状态。强烈表明扩展希望在不必 fork TUI 的情况下获得展示层控制。

7. **[#11416](https://github.com/earendil-works/pi/issues/11416) — [prio:p0] Bedrock：当工具的可选参数超过 16 个时请求失败**（2 条评论）
   Bedrock 会拒绝包含超过 16 个联合类型参数的 schema（“Schemas contains too many parameters with union types”）。由于 Pi 自带工具集加上 MCP 工具很容易超过该阈值，这是提供商层面的硬上限，需要在上游进行 schema 展平或拆分。

8. **[#11132](https://github.com/earendil-works/pi/issues/11132) — [prio:p0] 使用 OpenCode 的协议专用端点**（3 条评论）
   所有 OpenCode Zen/Go 流量都发往共享基础 URL，而不是文档中按协议区分的端点，而 models.dev 也是按模型列出这些端点的。将用户路由到错误端点可能导致能力不匹配以及计费/遥测方面的意外。已有 PR（#11434）处于开放状态。

9. **[#11383](https://github.com/earendil-works/pi/issues/11383) — 粘贴 ANSI 会留下转义序列残骸；未终止的括号粘贴会使编辑器卡死**（2 条评论，1 👍）
   粘贴终端输出会将字面转义内容注入提示符，而被截断的括号粘贴会使编辑器无响应——包括 Ctrl+C 也无法恢复。在 `pi-tui` 1.4.0 的 `handlePaste()` 中可复现。不可恢复的编辑器状态是最糟糕的 TUI 故障模式之一。

10. **[#11413](https://github.com/earendil-works/pi/issues/11413) — [prio:p0, fix-available] 将锁定的 undici 从 8.10.2 升级到 ≥8.12.2**（2 条评论）
    截至 1.4.0 的每个已发布版本都附带一个 `undici` 版本锁定，其范围落在多个已发布安全公告（内容编码错误、EventSource 内存耗尽、SRI 绕过）之内。已关闭，修复可通过 #11435 获得——这是供应链卫生方面的一项胜利，值得任何在 CI 中锁定 Pi 的人关注。

## 4. 关键 PR 进展

1. **[#11434](https://github.com/earendil-works/pi/pull/11434) [OPEN] fix(ai)：使用 OpenCode 协议专用端点**
   关闭 #11132。将 `openai-completions`/`openai-responses` 及 Go 变体映射到其文档化端点，而不是共享基础地址。改动很小，但这是当天 p0 级的提供商路由修复。

2. **[#5262](https://github.com/earendil-works/pi/pull/5262) [OPEN] feat(ai)：新增 Anthropic Vertex 提供商**
   长期存在的 PR，新增内置 `anthropic-vertex` 提供商——一个轻量适配器，构造 `AnthropicVertex` 并复用现有 Anthropic Messages 的流式/请求/工具路径。对于无法使用 Anthropic 第一方密钥的 GCP 团队很有意义。

3. **[#11435](https://github.com/earendil-works/pi/pull/11435) [CLOSED, inprogress] fix(coding-agent)：将 undici 更新到 8.12.3**
   由机器人编写的安全升级，针对锁定的 `undici`，引用了三项安全公告。与 #11413 配套；预计补丁版本将作为交付方式。

4. **[#11427](https://github.com/earendil-works/pi/pull/11427) [CLOSED] fix(mcp)：保留取消所有权并关闭生命周期**
   使 MCP 关闭/取消具备竞态安全性：在回调前发布关闭所有权、释放所拥有的操作、防护延迟初始化、保留入站取消控制器，并抑制关闭后的 stdio 写入。解决了 #11430 中的 disposer 故障提案。

5. **[#11440](https://github.com/earendil-works/pi/pull/11440) [CLOSED] fix(coding-agent)：当搜索查询就是工具名时优先排名**
   修复 BM25 长度归一化从精确名称匹配手中抢走排名的问题（例如 `thread_read` 输给更短的 `queue_read` 描述）。关闭 `tool_search` 精确名称 bug #11331；#11437 是一个并行的机器人编写变体。

6. **[#11438](https://github.com/earendil-works/pi/pull/11438) [CLOSED] ci：在并行任务中运行测试并移除未使用的 apt 包**
   拆分耗时 8–11 分钟的 `build-check-test` 任务，该任务主要受 coding-agent（约 140 秒）和 durable（约 128 秒，运行两次）拖累。纯粹的开发者体验提升——每个 PR 都能更快获得反馈。

7. **[#11422](https://github.com/earendil-works/pi/pull/11422) [OPEN] fix(tui)：修正 Kitty 功能键解码**
   解码 F1–F4 的 CSI/SS3 序列以及 F5–F12 的波浪号序列，包括修饰键、重复和释放；阻止私有使用区功能码点被作为可打印文本插入。修复 #11345，并继续终端兼容性清理。

8. **[#10718](https://github.com/earendil-works/pi/pull/10718) [CLOSED] fix(coding-agent)：在 `--export` HTML 中包含系统提示词**
   `pi --export` 会省略系统提示词，而交互式 `/export` 会包含它。使用会话当前分支上记录的提示词对齐两条导出路径——这对可审计性和可分享的转录记录很重要。

9. **[#11411](https://github.com/earendil-works/pi/pull/11411) [CLOSED] fix(tui)：在全屏模式下保留 iTerm2 内联图像**
   全屏模式在备用屏幕激活时禁用了 iTerm2 图像协议，使图像工具结果降级为 `[Image: ...]` 文本。恢复了 `TuiAltScreen` 中的内联渲染。

10. **[#10528](https://github.com/earendil-works/pi/pull/10528) [OPEN, inprogress] 重构 Nix 部分**
    重做 Nix 打包：清理 `package.nix`，改用 `makeBinaryWrapper`，并在 Nix 下跳过版本检查。自 10 月初以来仍处于开放状态——这是可复现/NixOS 安装的主要阻塞项。

## 5. 热门讨论

**展示与分享**
- **[#11418](https://github.com/earendil-works/pi/discussions/11418) — 答谢提议：为 pi 提供免费的多智能体 PR 审查**（1 条评论，1 👍）
  一位基于 Pi 的 GitHub 应用维护者将其多智能体审查机器人作为开源贡献提供给该仓库，并提到自己在智能体工作流中大量使用 Pi。值得注意的生态互惠：下游构建者把基础设施回馈上游，而不只是提交 issue。

## 6. 功能请求趋势

- **可扩展性接口面是首要诉求。** 反复出现的请求集中在生命周期和展示钩子，而非核心模型行为：公开的工具展示 API / 单行工具模式（#10259）、助手消息转换器（#11450）、内联 `ctx.ui.custom()` 对话框的阻塞状态上报（#11428）、技能和提示模板的可选包命名空间（#8834），以及一致的仅触发一次 `session_start` 事件（#10470）。
- **提供商覆盖与路由正确性。** 通过引导式 `/login` 流程配置自定义 OpenAI 兼容端点（#11456）、OpenCode 协议专用端点（#11132）、Anthropic Vertex（#5262），以及 MCP OAuth 中 CIMD 自动优先于 DCR（#11426）。用户越来越期望提供商配置可被发现，而不是手写 `models.json`。
- **跨平台功能对等，Windows 优先。** Windows 集中讨论帖（#7547）以及 `find` 在路径模式上失败（#6817）等具体 bug，表明用户需要一条经过官方认可、且权衡取舍有文档记录的 Windows 路径。
- **长会话持久性。** 凭据刷新（#11221）、有界压缩状态（#11297），以及 `IN_IGNORED` 后重新启用 watch 监视（#11342），都指向同一个主题：运行数小时或数天的会话没有得到充分支持。
- **终端/UI 保真度。** Kitty 按键解码（#11345）、全屏下的 iTerm2 内联图像（#11411），以及 ANSI 粘贴处理（#11383）——用户希望 TUI 在各种终端模拟器中都能正确表现，而不只是在常见路径上。

## 7. 开发者痛点

- **Windows 仍是二等公民。** 碎片化的运行路径（WSL、原生、Git Bash）意味着 `find` 对 `src/**/*.ts` 返回“No files found”这类 bug（#6817）持续存在，而文档和修复分散在不同方案中。这个 90 条评论的讨论帖既是一份 bug 报告，也是一次关于优先级的呼吁。
- **长时间运行的会话会累积静默故障。** 过期的 Bedrock 凭据、不断增长的压缩文件列表，以及丢失的文件系统监视，都会在数小时后悄悄失败——这是最难复现、也最损害信任的一类 bug。
- **不安全或不一致的默认值。** 会话 JSONL 以 `0644` 对所有用户可读（#11417）、过时的 `undici` 版本锁定一直发布到 1.4.0（#11413），以及依赖传输方式的 `maxMessageBytes` 强制执行（#11335），都表明默认安全性审查尚未系统化。
- **TUI 边界情况可能让提示符卡死。** ANSI 转义残骸，以及会令 Ctrl+C 失效的未终止括号粘贴（#11383），如果不重启进程就无法恢复。
- **提供商 schema 的怪癖会泄漏到 agent 中。** Bedrock 的联合类型参数上限（#11416），以及某些 OpenAI 兼容提供商缺少 `finish_reason`（#6226），迫使用户通过 Pi 来调试上游验证错误。
- **文档与 CLI 漂移。** `pi update --models` 在 `docs/cli.md` 中有文档记录，却被当作未知选项拒绝（#11453），这是一个虽小但会侵蚀信任的不一致；`--export` 与 `/export` 的分歧（#10718）也是同一模式。
- **分类处理速度不均衡。** 许多条目在当天就以 `untriaged`/`no-action` 关闭（#11455、#11442、#11417），这保持了队列整洁，但在首次报告者看来可能像是被敷衍——随着社区规模增长，值得关注。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区摘要 — 2026-10-27

## 1. 今日亮点

托管 Agent 方向继续占据主导：新的会话权限规则与 Auto-approval 功能已在 preview 和 nightly 两个渠道落地，同时 Kubernetes/Hosted 运行时跟踪 issue 报告两项 S1 原生回归门禁通过。缺陷方面，围绕**无头 `-p` 审批语义**（#15597、#15600）以及 **shell 工具进程内 `sed` 模拟**（#15675）的一批 P1/P2 报告表明，隐式 edit/write 拒绝规则现已成为用户可见故障的首要来源。Web Shell 打磨也在加速，过去 24 小时内，会话命名、重命名响应性以及简洁 composer 变体的 PR 均已提交。

---

## 2. 发布版本

过去 24 小时内有三个版本发布。值得注意的是，preview 与 nightly 共享完全相同的变更日志条目。

| 版本 | 说明 |
|---|---|
| **v0.26.2-preview.0** | `feat(managed): add session permission rules and Auto approval`（[#15448](https://github.com/QwenLM/qwen-code/pull/15448)）；`fix(cli): harden serve token resolution` |
| **v0.26.1-nightly.20261026.34769f9d41** | 与上文相同的两项变更（[#15448](https://github.com/QwenLM/qwen-code/pull/15448)） |
| **sdk-typescript-v0.1.23** | 捆绑 CLI 0.26.1，使用与 SDK 相同的 ref 从源码构建。发布说明还引用了捆绑 CLI 0.26.0 的一节，疑似复制/粘贴残留，值得维护者检查。 |

**要点：**“会话权限规则 + Auto approval”工作是本周期的主打功能，并且与今天提交的审批模式缺陷直接相关（见 #15597 / #15600）。

---

## 3. 热门 Issue（精选 10 条）

1. **[#15597](https://github.com/QwenLM/qwen-code/issues/15597) — P1：托管 auto-memory 在无头 `-p` 中永远无法保存**（7 条评论）
   在非交互模式下使用默认/Auto approval 时，隐式的 `edit`/`write_file` 拒绝会覆盖 memory agent 的作用域允许，导致每次记忆写入都被拒绝、索引保持为空——而本次运行仍然要消耗 token。这是新权限栈自相冲突的最明显例子；对于当天提交的 issue 来说，社区互动量很高。

2. **[#15600](https://github.com/QwenLM/qwen-code/issues/15600) — P2：无头 `-p` 拒绝使用 `2>&1` 的只读命令**（6 条评论）
   文件描述符复制被解析为对名为 `&1` 的文件写入，产生 `Matching deny rule: "edit"`。同一条命令去掉 `2>&1` 后可正常运行。一个极其常见的 shell 惯用法在安全层下失效，立即引发关注，且修复 PR（[#15681](https://github.com/QwenLM/qwen-code/pull/15681)）已经打开。

3. **[#15675](https://github.com/QwenLM/qwen-code/issues/15675) — P1：`sed` 编辑模拟与真实 sed 行为不一致**（3 条评论）
   进程内的 `sed -i` 模拟构建 JS `RegExp` 时未带 `s`/`u` 标志，因此 `.` 无法匹配 `\r` 和多字节 emoji，悄悄生成与真实 `sed` 不同的文件。这是一个正确性问题，会削弱对 shell 工具优化路径的信任。

4. **[#13395](https://github.com/QwenLM/qwen-code/issues/13395) — Kubernetes 工具运行时追踪**（25 条评论）
   本时间窗口内持续最久的讨论串。2026-10-27 更新：两项 S1 原生回归通过，验收 fixture 修复（[#15585](https://github.com/QwenLM/qwen-code/pull/15585)）已合并；维护者指出完整 SDK CI 门禁仍是独立缺口，因此该 issue 保持开启。这是平台分发路线图的锚点讨论串。

5. **[#15526](https://github.com/QwenLM/qwen-code/issues/15526) — P2：warn 模式循环提醒只触发一次，随后 59 次相同的无防护调用**（4 条评论）
   一个本地 OpenAI 兼容模型发出了 70 次字节完全相同的 `read_file` 调用；在单次警告之后，仍有 59 次未被拦截。这直接质疑护栏在 `warn` 模式下的有效性——对自主运行而言是真实的成本/token 消耗。

6. **[#15660](https://github.com/QwenLM/qwen-code/issues/15660) — P2：子 Agent 以约 105k 的 prompt 上下文启动**（3 条评论）
   父会话使用 42k（启动 21k），子 Agent prompt 为 1k，但引擎报告起始 prompt 为 105k——而且每增加一个子 Agent 还会增长。子 Agent 的 token 核算不透明；需要分诊信息。

7. **[#15686](https://github.com/QwenLM/qwen-code/issues/15686) — P2：Active Goal 吞掉普通用户输入**（3 条评论，今日更新）
   按 Enter 引导的消息和粘贴的消息会排队，而不是在下一个安全采样边界投递，除非加上 `/goal` 前缀。这是一个交互性回归，破坏了文档中说明的引导快捷键。

8. **[#15682](https://github.com/QwenLM/qwen-code/issues/15682) — P2：面向 Agent Collaboration 的用户级 Agent 与 squad**（2 条评论）
   请求提供一份全局 Agent/squad 名册，在每个受信任工作区中都可用。这代表了社区正在推动的多 Agent 方向。

9. **[#13749](https://github.com/QwenLM/qwen-code/issues/13749) — Claude Code 风格的 Mod 运行时**（6 条评论）
   提议兼容 `register(on, options)` / `on(event, matcher?, handler)` / `($, event, next)`，以便现有 Claude mods 能在 Qwen 的工具与 hook 系统上运行。这是一个持续存在的请求，具有真实的生态可移植性价值。

10. **[#15470](https://github.com/QwenLM/qwen-code/issues/15470) — CI：main 推送失败未提交任何 issue**（6 条评论）
    2026-10-24 的 14:00Z 至 17:30Z 之间，十次 main 推送失败产生了零个自动提交的 issue；报告者在托管 runner 上等待 20–100 分钟，随后因被标记为 superseded 而丢弃。这是一个元基础设施问题，影响维护者的响应时间。

*另值得关注：* **[#15613](https://github.com/QwenLM/qwen-code/issues/15613)**（已关闭）——交错推理在 TUI 中被重排，并在 Chat Completions 回放中被扁平化；**[#1379](https://github.com/QwenLM/qwen-code/issues/1379)**（已关闭）——长期存在的“不要再添加 `Co-authored-by: Qwen-Coder`”请求。

---

## 4. 关键 PR 进展（精选 10 条）

1. **[#15681](https://github.com/QwenLM/qwen-code/pull/15681) — fix(core)：不要将 fd 复制（`2>&1`）视为文件写入**
   阻止 shell 权限检查器将后跟 `&n` 或 `&-` 的 `>`、`1>`、`2>`、`<` 解读为文件写入。直接修复 #15600；这是本时间窗口内影响最大的正确性补丁。

2. **[#15458](https://github.com/QwenLM/qwen-code/pull/15458) — feat(core)：面向全新 Linux 安装的 XDG 配置目录**
   对于全新安装，将全局状态从 `~/.qwen` 移至 `$XDG_CONFIG_HOME/qwen`（`~/.config/qwen`），遵循 XDG Base Directory 规范。与 #15628 中的 UI 文案修复配套。

3. **[#15564](https://github.com/QwenLM/qwen-code/pull/15564) — feat(managed-agent)：预检 Harness 兼容性、残留 worker、实时服务**
   `qwen-managed-agent-server --check` 新增三项面向运维人员的检查，包括在探测 `/capabilities` 后构建 `HostedHarnessClient`，使协商与运行时行为一致。对托管部署而言是意义重大的升级安全性改进。

4. **[#15689](https://github.com/QwenLM/qwen-code/pull/15689) — feat(web-shell)：根据首条 prompt 为新会话命名**
   在 attach 之前，从单行、4–40 个码点的首条 prompt（不含裸斜杠命令）推导标题，然后乐观地修补侧边栏行。消除了满屏“未命名会话”的情况。

5. **[#15688](https://github.com/QwenLM/qwen-code/pull/15688) — fix(web-shell)：保持会话重命名响应流畅**
   内联重命名编辑器保留本地草稿，因此输入时只重新渲染输入框，而不是每个已挂载的会话行；打开编辑器也不再展开整个目录。这是针对大型会话列表的明确性能修复。

6. **[#15547](https://github.com/QwenLM/qwen-code/pull/15547) — feat(cli)：在 `review build-test` 中只运行与 diff 相关的测试**
   对于 `test` 脚本只是 `vitest run` 的工作区，将 diff 文件及其依赖项交给 `vitest related`，而不是运行完整测试套件。为 review 工作流带来可观的 CI 时间收益。

7. **[#15673](https://github.com/QwenLM/qwen-code/pull/15673) — fix(release)：延长独立运行时下载重试**
   每次运行时下载最多重试五次，退避至约 75 秒，并用回归测试固定第五次尝试。提升发布流水线可靠性。

8. **[#13969](https://github.com/QwenLM/qwen-code/pull/13969) — feat(hooks)：面向本地会话的 `FileChanged` 通知**
   为非托管会话新增 `FileChanged` hook，支持字面文件名，以及通过 `SessionStart`/`FileChanged` 输出注册的绝对文件和递归目录。这是正在进行中的主要可扩展性新增。

9. **[#15663](https://github.com/QwenLM/qwen-code/pull/15663) — feat(web-shell)：可配置的简洁 composer**
   可选启用 `composerVariant="simple"` 展示形态，在主聊天、分屏和 side tasks 之间共享现有编辑器、附件和会话操作；默认情况下现有宿主不受影响。

10. **[#15670](https://github.com/QwenLM/qwen-code/pull/15670) — fix(serve)：Hosted 本地状态与前台 Shell 校验**
    Hosted 文本轮次、工作流、hook 和上下文压缩使用其远程拥有的 Session 身份，而不是本地转录；Hosted 启动会跳过分支 worktree 恢复和普通 Shell 路径。这是 Hosted/managed 拆分的关键。

*另已合并/关闭：* **[#15668](https://github.com/QwenLM/qwen-code/pull/15668)**（已关闭）——Web Shell 中 `/autofix-pr` 的监视可见性与取消，这是四篇 PR 系列的最后一部分；**[#15555](https://github.com/QwenLM/qwen-code/pull/15555)**——在测试套件失败后保持 sdk-java 缓存回写继续运行。

---

## 5. 热门讨论

*本数据集中未提供 Discussion 数据——本节省略。*

---

## 6. 功能需求趋势

- **Web Shell 作为一等界面。** 最大的单一需求簇：内联重命名预选中（[#15629](https://github.com/QwenLM/qwen-code/issues/15629)）、滚动时固定轮次 prompt（[#15683](https://github.com/QwenLM/qwen-code/issues/15683)）、Session Overview / Split View 的键盘快捷键（[#13175](https://github.com/QwenLM/qwen-code/issues/13175)），以及 composer 变体（[#15663](https://github.com/QwenLM/qwen-code/pull/15663)）。需求越来越聚焦于*规模化的人体工学*，而不是缺失功能。
- **多 Agent / 团队级配置。** [#15682](https://github.com/QwenLM/qwen-code/issues/15682)（跨工作区的用户级 Agent 与 squad）与 Agent Collaboration 路线图标签以及子 Agent 上下文问题（#15660）并列。
- **聊天平台与企业集成。** Microsoft Teams（[#15618](https://github.com/QwenLM/qwen-code/issues/15618)）和 QQ bot 主动循环语义（[#15631](https://github.com/QwenLM/qwen-code/issues/15631)）都要求定义良好的投递语义，而不仅仅是适配器。
- **与 Claude Code 的可扩展性对齐。** Mod 运行时提案（[#13749](https://github.com/QwenLM/qwen-code/issues/13749)）和 `FileChanged` hook（[#13969](https://github.com/QwenLM/qwen-code/pull/13969)）指向跨工具、hook 和事件的一致编写契约。
- **Hosted / 外部运行时拓扑。** [#15512](https://github.com/QwenLM/qwen-code/issues/15512)（外部 Hosted Workspace profile，第 2 阶段）和 [#13395](https://github.com/QwenLM/qwen-code/issues/13395)（Kubernetes 工具运行时）代表平台分发路线图的下一步：厂商中立的 worker 供应。
- **平台约定合规。** XDG 目录（[#15458](https://github.com/QwenLM/qwen-code/pull/15458)）以及设置/UI 中如实呈现路径（[#15628](https://github.com/QwenLM/qwen-code/issues/15628)）。

---

## 7. 开发者痛点

1. **非交互模式下的权限/审批语义是头号摩擦来源。** P1 [#15597](https://github.com/QwenLM/qwen-code/issues/15597) 和 P2 [#15600](https://github.com/QwenLM/qwen-code/issues/15600) 都追溯到*隐式* `edit`/`write_file` 拒绝规则覆盖作用域允许，并错误分类 shell 语法。新的会话权限规则与 Auto approval（#15448）使问题更加关键：作用域允许必须真正生效，shell 解析器需要理解重定向语法，而不是对 `>` 做模式匹配。
2. **循环护栏被认为无效。** [#15526](https://github.com/QwenLM/qwen-code/issues/15526) 显示 `warn` 模式发送一次提醒后，又允许了 59 次相同调用。在本地模型上运行自主任务的用户想要强制执行，而不是一次轻推。
3. **子 Agent 的 token 核算不透明。** [#15660](https://github.com/QwenLM/qwen-code/issues/15660)——1k 的子 Agent prompt 被报告为 105k prompt——削弱了对成本估算和上下文预算的信任。
4. **CI 噪声正在淹没信号。** 由机器人提交的 CI issue 按评论数占据 issue 列表前列（[#14724](https://github.com/QwenLM/qwen-code/issues/14724) 有 64 条评论，[#15173](https://github.com/QwenLM/qwen-code/issues/15173) 有 17 条，[#13009](https://github.com/QwenLM/qwen-code/issues/13009)、[#14796](https://github.com/QwenLM/qwen-code/issues/14796)），其中多个未识别出测试且带有 `autofix/skip`。雪上加霜的是，[#15470](https://github.com/QwenLM/qwen-code/issues/15470) 记录了十次失败却提交零个 issue，[#15677](https://github.com/QwenLM/qwen-code/issues/15677) 报告新的 darwin-x64 TUI 对等性检查项在 30 秒就绪 prompt 预算下 6 次运行中失败 5 次。净效果：不稳定的基础设施正在消耗维护者本可用于上述 P1 的精力。
5. **模拟层与真实工具发生漂移。** [#15675](https://github.com/QwenLM/qwen-code/issues/15675) 是最清楚的案例——进程内的 `sed` 快路径会悄悄产生与 `sed` 不同的输出。每当 Qwen Code 模拟某个工具而不是实际调用它时，行为对等性都需要回归 fixture。
6. **交互性与输入路由回归。** [#15686](https://github.com/QwenLM/qwen-code/issues/15686)（Active Goal 扣住按 Enter 引导的输入）和 [#15613](https://github.com/QwenLM/qwen-code/issues/15613)（交错推理在 TUI 中被重排）都破坏了文档化 UX 设定的预期——值得进行一次针对性的交互模式测试。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*