# AI CLI 工具社区动态日报 2026-10-28

> 生成时间: 2026-10-28 01:35 UTC | 覆盖工具: 7 个

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

# 跨工具对比报告——AI CLI 生态（2026-10-28）

**范围：**Claude Code、OpenAI Codex、Gemini CLI、GitHub Copilot CLI、OpenCode、Pi、Qwen Code。
**数据说明：**下文数据来自提供的摘要，这些摘要回顾的是前 10 个 Issue 列表和关键 PR，而不是完整仓库指标。计数是“窗口期内浮现的条目”，不是总未关闭 Issue 数。如果某个渠道没有产生数据，则标记为 N/A，而不是 0。

---

## 1. 生态系统概览

七款工具已收敛到一种共同产品形态——具备 MCP 扩展性、子代理编排、沙箱执行和权限模型的终端/IDE 代理——如今竞争的是**代理循环的可靠性**，而不是原始能力。发布节奏普遍很高（七款中有 4 款在 24h 内发布；2 款在多个渠道发布），但 Issue 队列显示成熟度并不均衡：状态完整性 bug、静默失败和平台特定故障比缺失功能更突出。第二个更安静的变化也可见：社区越来越希望**验证并审计**代理做了什么（签名运行回执、真实的终止原因、可复现的 `AGENTS.md` 检查），而不是只要求它们做得更多。与此同时，企业约束——身份映射、策略允许列表、提供商路由——已从路线图讨论进入已交付代码和反复出现的 bug 报告。

---

## 2. 活动对比

| 工具 | 窗口期内浮现的 Issue | 评论最多的 Issue | PR（24h） | 讨论 | 发布（24h） |
|---|---|---|---|---|---|
| **Claude Code** | 17（10 个热点 + 7 个跟踪） | #76248 — 41 条评论 | 3 个更新（2 个开放，1 个已关闭） | N/A（未提供） | 1（v2.1.312） |
| **OpenAI Codex** | 13（10 个热点 + 3 个跟踪） | #56655 — 33 条评论，👍38 | 约 14 个已关闭（机器人主导的批量） | 9（2 个问答，7 个展示分享） | 1 个稳定版（v0.165.0）+ 4 个预发布产物 |
| **Gemini CLI** | 10 个热点（+ 关联） | #22323 — 13 条评论 | 约 12 个（10 个列出 + 配套） | N/A（未提供） | 3（稳定版 v0.66.0、preview.0、nightly） |
| **GitHub Copilot CLI** | 10 个热点 | #3709 — 12 条评论，👍34 | **0 个更新** | N/A（未提供） | **4**（v1.0.104–v1.0.105-1） |
| **OpenCode** | 14（10 个热点 + 4 个关注） | #59387 — 35 条评论 | 约 15 个（含 daemon 栈） | N/A（未提供） | 1（v2.2.0） |
| **Pi** | 13（10 个热点 + 3 个值得关注） | #10953 — 11 条评论 | 约 12 个已关闭 | 2（展示分享） | **0** |
| **Qwen Code** | 13（10 个热点 + 3 个值得注意） | #12380 — **72 条评论** | 约 13 个（含 3-PR SDK 系列） | N/A（未提供） | 4（v0.26.2、2 个预览版、TS SDK） |

**解读表格：**
- **Issue 数量 ≠ 健康度。** Claude Code 报告 24h 内有 50 个 Issue 更新，而 PR 只有 3 个——评审积压向 Issue 倾斜约 17:1。Copilot CLI 则相反：4 个发布、0 个 PR，这符合封闭的内部流水线——公开仓库是发布/缺陷渠道，而不是贡献入口。
- **Codex 是自动化程度最高的贡献者。** 几乎所有已关闭的 PR 都归因于 `copyberry[bot]`——这是批量合入流水线，不是社区自然贡献。
- **讨论是次级渠道**；本组中只有 Codex 和 Pi 产生了数据。其他所有工具都以 Issue 跟踪器为中心，因此它们的 N/A 条目反映的是报告范围，而非社区沉默。

---

## 3. 共性功能方向

六项需求在三个或更多工具社区中反复出现：

**3.1 上下文经济性 / 压缩成本**
- *Claude Code：* 工具和连接器的 schema/名称延迟加载（#101371）、按需注入记忆（#78795）。
- *Gemini CLI：* AST 感知读取和精准抽取，以削减约 36.6k token/轮的基线（#22745、#19561）。
- *Pi：* 保留前缀的摘要（PR #11499 修复 #11500）、每个模型支持多种上下文窗口变体（#10893）。
- *Qwen Code：* 压力下动态截断工具输出（#2566）。
- *共性需求：* 默认加载更少，**并且**不再为维护性调用支付完整预填充成本。

**3.2 真实的代理可观测性与终止**
- *Gemini CLI：* `MAX_TURNS` 耗尽却报告为 `GOAL` 成功（#22323）；Wayland 浏览器代理在失败时报告 `GOAL`（#21983）；`/bug` 省略子代理内部信息（#21763）。
- *OpenCode：* 失败的子代理向父代理报告成功（#52378）；幽灵模型条目污染使用统计（PR #59773）。
- *Claude Code：* 用于子代理验证的签名运行回执（#104409）。
- *Qwen Code：* 达到最大会话轮数时误报成功（#15358）；JSON 结果重放上一次答案而不是报错。
- *共性需求：* 调用方必须能以程序化方式区分“完成”与“放弃”。

**3.3 权限与沙箱正确性（而非能力）**
- *Claude Code：* 在 `bypassPermissions` 下，对 `.git/`/`.claude/` 的写入将 `ask` 静默转换为允许（#103994）；VS Code 权限响应在长会话中丢失（#89245）。
- *Codex：* 显式 macOS Git 授权被忽略（#54574）；Windows 自动审查拒绝人工批准（#54962）。
- *OpenCode：* 热重载期间 `provider.use` 拒绝策略可被绕过，将提示路由到第三方提供商（#52333）。
- *Qwen Code：* `sed` 模拟与真实 `sed` 行为偏离（#15675）；`2>&1` 被误分类为文件写入（PR #15681）；bash 分词错误（PR #14670）；重新赋值的 `HOME`（%14677）。
- *Copilot CLI：* 并行工具调用下 `preToolUse` 钩子被绕过（#2893）。
- *共性需求：* 已授予的批准必须被遵守，已拒绝的批准不得泄漏——在每条代码路径中都保持一致。

**3.4 会话持久性与恢复完整性**
- *Pi：* 截断分支后恢复会丢失内置工具（#11460）；写入失败时内存状态与磁盘状态偏离（PR #11486）；会话在上下文上限处永久卡死（#9409）。
- *Qwen Code：* 持久化的 thinking 部分缺少签名，使会话因永久 400 错误而变砖（#15742）；技能目录陈旧（#15645）。
- *Codex：* MXC 环境中缺少 `SYSTEMROOT`，导致本地聊天无法恢复（#56264）。
- *Copilot CLI：* 120s 主机确认超时使会话在恢复前不可用（#5100）；无法取消归档（#3518）。
- *Claude Code：* 桌面会话枚举/后台化请求（#104467）。
- *共性需求：* 会话是持久化产物；损坏应可恢复，而不是终局。

**3.5 多提供商与 BYOK 灵活性**
- *Copilot CLI：* 按会话切换模型，包括本地提供商（#3709，👍34——得票最高的请求）；按代理选择提供商（#4703）；模型选择不持久（#1869）。
- *Codex：* 将 Vertex AI 作为一等提供商（v0.165.0）；`ProviderAuthContext` 在目录和推理之间共享（PR #57607）；为企业预发布环境提供目录 URL 覆盖（PR #57567）。
- *Pi：* 提供商协议保真度——OpenCode 端点（#11434）、LiteLLM 溢出检测（#11494）、OpenRouter 推理预算（#11496）、命名空间正确的 Responses 重放（#11415）。
- *Qwen Code：* io.net 预设（10 👍）、厂商请求头正确性。
- *共性需求：* 模型层应可配置、可持久化且忠实于规范——而不是固定菜单。

**3.6 将 Windows / WSL2 作为一等平台**
- *Codex：* 扩展无法启动（#56655 + 3 个重复项）、MXC 因无关的已锁定 BitLocker 卷而中止（#50915）、提权沙箱 ACL 不一致（#57610）、空闲 Git 扫描占满 CPU（#57111）。
- *OpenCode：* 缺少 `CREATE_NO_WINDOW` 导致控制台约 1 次/秒闪烁（#52266，重复报告）、WSL UNC 路径使桌面↔服务器链路崩溃（#52205）、孤立的 TUI 进程（#52203）。
- *Copilot CLI：* 原生运行时崩溃（#4026）、WSL2 语音段错误（#5197）、SSH+tmux 复制/粘贴（#3260）。
- *Gemini CLI：* CRLF diff 归一化现已发布；Wayland 浏览器会话失败。
- *共性需求：* 对等支持，而不是“能在 macOS 上跑”。

---

## 4. 差异化分析

| 维度 | Claude Code | OpenAI Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **主要用户** | 企业团队，云/Cowork 会话 | 专业开发者 + 企业多云 | 代理架构研究者，Google 技术栈 | 以 GitHub 为中心的组织，CI/自动化 | 运行大量并发会话的高级用户 | 对成本敏感的长会话运营者 | 托管代理 / web-shell 运营者 |
| **技术核心** | 插件 + MCP 渠道模型；网关/SSO | Rust 运行时；解耦的 `codex-exec-server`；MXC 沙箱 | 子代理注册表 + 上下文节俭工具 | Fusion 托管模型；ACP 无头接口 | Bun/TS；进程守护 + 桌面插件宿主 | 提供商适配器纯粹性；压缩优先 | 无沙箱 bash 模拟；双路径托管运行时 |
| **显著押注** | OIDC 声明灵活性（`oidc.user_id_claim`）；市场分发 | 独立执行器二进制；`doctor --network-only` 诊断；按提供商认证 | 将 AST 感知工具作为 token 削减策略 | Autopilot 等待后台任务；ACP 对等 | 桌面 GUI 插件（实验性） | 保留前缀的压缩；持久任务编排 | 按会话 Git worktree；会话权限规则 |
| **当前最薄弱环节** | 云 GitHub 授权与 UI 状态不一致 | Windows 扩展/沙箱/更新器 | 代理挂起 + 误导性成功 | 不透明且不持久的模型选择 | 进程/提供商边界处的静默失败 | 会话卡死与压缩重入 | CI/发布流水线可靠性 |

**值得强调的关键对比：**

- **“代理边界”在哪里。** Codex 正将执行*推出*客户端（独立 `codex-exec-server`、`codex doctor`）；Claude Code 正将会话*推入*托管云平面（Cowork）——并为此付出代价：其授权模型的 UI 与运行时不一致。OpenCode 保留本地守护进程，如今正与状态固定类 bug 斗争（`failed` 的 MCP 服务器永不重试、硬编码端口 49374）。
- **应对上下文压力的方式。** Gemini CLI 用更好的*读取*（AST 感知搜索）来应对；Pi 用更好的*写入*（共享前缀的压缩）来应对。两者互补，且都比“更狠地截断”更具结构性。
- **安全实现方式。** Qwen Code 在 JS 中模拟 bash 以进行权限分析——这很强大，也催生了一整类 bug（`sed` 标志位缺口、`\s` 与 bash 分隔符、`HOME` 重新赋值）。Codex 则收窄*环境*（MXC），这会产生另一类失败（缺少 `SYSTEMROOT`、BitLocker 探测过于积极）。Claude Code 的漏洞是语义层面的（#103994），而非环境层面的。
- **分发策略。** Claude Code 和 Copilot CLI 通过受管理的市场/扩展发布，并采用按发布门控的逐步推送；OpenCode 正在桌面端开放*第三方*插件面（明确为实验性），以稳定性换取生态增长。Pi 是本组中唯一在窗口期内零发布的工具——这是一个分类处置与修复日。

---

## 5. 社区动能与成熟度

**第一梯队——高数量、高影响范围、系统性问题**
- **Claude Code** 的信噪比问题最严重：一个*单一*回归集群（Cowork/GitHub 认证）在一天内产生至少 8 个 Issue 加 7 个跟踪变体，而 PR 吞吐量只有 3 个。其互动量最高的 Issue 从 7 月一直开放至今仍未解决。其表面功能成熟度高，事件响应成熟度低。
- **OpenAI Codex** 展现出最强的工程吞吐——一个连贯的 14 个 PR 批次，涵盖可观测性、打包和持久性——但其最高 Issue（#56655，👍38）是发布阻断级的 Windows 扩展回归，存在重复报告且**没有已发布的回滚指引**。这种发布速度与用户侧恢复能力之间的差距，是值得关注的成熟度信号。

**第二梯队——快速迭代、影响范围更窄**
- **GitHub Copilot CLI** 拥有本组最快的发布节奏（24h 内 4 次），但基本没有公开贡献入口（0 个 PR）。其得票最高的 Issue（👍34）已开放足够久，成为一项定义性限制，其模型选择行为被描述为不透明且不持久——这更像信任问题，而不是功能缺口。
- **Qwen Code** 拥有最高的原始讨论强度（#12380 有 72 条评论，#14724 有 68 条），但其中很大一部分是*自身造成的 CI 内耗*：5 个并发 CI 线程、发布阶段失败，以及不断增长且无人负责的“延后评审发现”清单。速度是真实的；吞吐效率不是。

**第三梯队——更小、信号更强的工程社区**
- **Gemini CLI** 评论数不高（最多 13 条），但每个 Issue 的 👍 密度最高（#21409 关于挂起问题获得 👍8）。其问题偏架构层面（代理委派触发过少或挂起），而非基础设施层面。
- **OpenCode** 既有真正的发布头条（v2.2.0 桌面插件），也有核心编辑路径中的严重正确性 bug（#53011，数字重复最高达 129x）和热重载下的安全漏洞（#52333）。其生态野心跑在安全模型前面。
- **Pi** 度过了本组中最有纪律的一天：零发布，但关闭了恢复时工具丢失、Responses 命名空间重放、Bedrock schema 限制等 P0 问题，并包含一项实质性性能修复（保留前缀的压缩）。评论数低，修复密度高——这是一个维护者主导而非人群驱动的项目。

**动能排名（发布节奏、PR 合入率和 Issue 互动量的综合）：**
1. OpenCode / Qwen Code（持续发布 + 活跃争议）
2. OpenAI Codex（最高工程吞吐）
3. Gemini CLI / Copilot CLI（稳定节奏，PR 面较薄）
4. Claude Code（高互动，吞吐受限）
5. Pi（低节奏，高修复质量）

---

## 6. 趋势信号

**T1——“证明它跑过”正在取代“让它跑起来”。**
签名运行回执（Claude #104409）、真实的终止原因（Gemini #22323/#21983）、可复现的指令传播检查（Codex `Agent Relay Check`），以及报错而非重放过期答案的 JSON 输出（Qwen #15358），都指向同一方向。**参考价值：** 如果你在 CI 或编排中基于这些 CLI 构建，今天不要信任 `status: success` 字段——独立埋点监测，并优先选择能暴露结构化失败原因的工具。

**T2——沙箱是新的兼容性矩阵。**
Windows 上的 MXC、macOS 上的 Seatbelt、Qwen 中的 JS 级 bash 模拟，以及 Claude 的钩子语义，各自产生一类缺陷。**参考价值：** 为无人值守运行选择工具时，应针对你*实际*环境的特殊之处评估沙箱——锁定卷、WSL 路径转换、`SYSTEMROOT`、重新赋值的 `HOME`。预测失败的是环境假设，而不是功能列表。

**T3——静默失败如今被认为比硬拒绝更糟。**
这在 OpenCode（端口 49374 永久挂起；无入口点的插件被静默跳过）、Copilot CLI（丢弃 MCP 图片、`/rename` 假成功）、Pi（执行无回复）和 Claude Code（UI 显示绿色，会话却失败）中反复出现。**参考价值：** 将诊断能力视为产品功能并给予权重。Codex 的 `doctor --network-only` 和脱敏 WebSocket 探测证据是最清晰的正例——投资于能把“它没工作”变成可操作产物的工具。

**T4——压缩正在成为账务问题，而不是摘要问题。**
Pi 的发现——摘要请求与实时轮次不共享任何前缀（每次都完整预填充）——是本数据集中经济意义最大的单项洞察。结合 Gemini 的 token 基线工作和 Claude 的 schema 延迟加载，方向已经清晰。**参考价值：** 衡量每轮缓存命中率和预填充成本，而不仅仅是每请求 token 数；忽略提示缓存前缀对齐的压缩策略，可能静默地将运营成本翻倍。

**T5——BYOK 和按会话切换模型已是入场门槛，但持久化才是真正的诉求。**
Copilot 得票最高的 Issue 不是“更多模型”——而是“别再把我的选择改回去”。Codex 正在构建认证管道（`ProviderAuthContext`），让多提供商变得一致。**参考价值：** 模型配置应当是每个项目持久、可审计的产物，而不是会话级标志。

**T6——企业采用取决于身份与策略管道，而不是能力。**
Claude Code 已交付 OIDC 非 `sub` 声明映射；Codex 增加了用于预发布环境的目录 URL 覆盖；Copilot CLI 的采用阻碍是系统级安装器和 `allowedMcpServers` 匹配；OpenCode 的 `OPENCODE_PASSWORD_FILE` 请求由容器部署驱动。**参考价值：** SSO 声明灵活性、按工作区（而非全局）的策略，以及便于密钥管理器使用的配置，是采购要求，而不是锦上添花。

**T7——代理正在离开终端。**
Codex 的 sports-video-coach 和 Haru Café 讨论，加上 Claude 的开放网络代理身份请求（#101972）以及 Pi 基于分类器的工具范围限定（AY-PI），显示使用场景正漂移到软件工程之外。**参考价值：** 按任务类别限定工具范围（只有 CODE 获得 `edit`/`bash`）是一种值得尽早采用的新兴模式——它同时降低 token 成本和影响范围。

---

### 决策者要点

- **选择 Codex**：适合工程严谨性和多云提供商控制；上线前验证 Windows 方案并固定版本——没有自动回滚。
- **选择 Claude Code**：适合企业 SSO 和插件广度，但在依赖它之前，要按你的仓库拓扑审计云会话 git 授权路径；UI 不是可靠的状态指示器。
- **选择 Gemini CLI**：如果你要基于子代理构建——但要增加外部终止检查，因为 `GOAL` 目前不可信。
- **选择 Copilot CLI**：如果你深度使用 GitHub 且需要 ACP/无头自动化；预计需要自行管理模型持久化。
- **选择 OpenCode**：适合多会话高强度使用和桌面扩展性，但需注意其插件/权限模型仍在加固中。
- **选择 Pi**：当每轮成本和长会话完整性是首要考虑时；它拥有本组中最有纪律的上下文经济性工作。
- **选择 Qwen Code**：适合托管代理/web-shell 部署，前提是能容忍仍在稳定中的发布流水线。

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — 社区亮点（数据截至 2026-10-28）

**数据说明：** 导出的 50 个 PR 标注为“按评论数排序”，但每个 PR 返回的 comment 与 👍 字段均为 `undefined`/`0`。因此，下文排名依据仓库自身的关注排序，再加上可观察的活跃信号（最近更新时间、关联 issue、涉及文件/技能数量、重复贡献者）。Issue 评论数*确实*可用，并在第 2 节中用于定量分析。

---

## 1. 热门 Skills 排名

| # | Skill / PR | 功能 | 讨论亮点 | 状态 |
|---|---|---|---|---|
| 1 | [mcp-builder — `mcp>=2` client fix](https://github.com/anthropics/skills/pull/1742) (#1742) | 修复 MCP 服务器脚手架技能中的 `connections.py`，以适配 `streamable_http_client` 重命名，并通过 `create_mcp_http_client` 注入请求头 | 最易受版本变化影响的技能出现 API 漂移导致的失效；关闭 #1668 | OPEN（更新于 2026-10-08） |
| 2 | [skill-creator — trigger-eval isolation](https://github.com/anthropics/skills/pull/1298) (#1298) | 修复 trigger evaluation 中的假“未命中”：每个 worker 的探测冲突、Windows 管道上的 `select()`、无关工具扫描中止 | 决定*任意*技能是否触发的 eval 循环之正确性；运行时失败此前会被静默计为未触发 | OPEN（更新于 2026-09-16） |
| 3 | [notion-spec-to-implementation + quantitative-resume-auditor](https://github.com/anthropics/skills/pull/1245) (#1245) | 将产品/技术规格转为带验收标准和进度跟踪的 Notion 任务；第二个技能对简历进行数值化审计 | 捆绑式双重提交；规格→backlog 自动化是社区反复提出的需求 | OPEN（更新于 2026-10-15） |
| 4 | [proofcore-contract-auditor](https://github.com/anthropics/skills/pull/1771) (#1771) | 对 Solidity/Rust 合约进行静态分析，并通过零存储 Merkle 协议将加密审计证明锚定在 TON 上 | Web3/垂直领域技能；引发命名空间信任问题（参见 Issue #492） | OPEN（更新于 2026-09-16） |
| 5 | [docx — detect orphaned comments](https://github.com/anthropics/skills/pull/1734) (#1734) | 标记锚定文本已被删除的 `docx` 评论 | 值得注意的是提交内容简短且无摘要——说明核心技能修复的评审摩擦 | OPEN（更新于 2026-09-25） |
| 6 | [md2video-audio](https://github.com/anthropics/skills/pull/1703) (#1703) | 将 Markdown → Marp 幻灯片 → MP4 编译，并配以拟人 TTS 旁白，零成本流水线 | 媒体生成方向；围绕渲染/音频工具链依赖的可行性存在争议 | OPEN（更新于 2026-09-15） |
| 7 | [webapp-testing — drop `shell=True`](https://github.com/anthropics/skills/pull/1980) (#1980) | 替换 `with_server.py` 中的 `subprocess.Popen(..., shell=True)`，以关闭 CWE-78 命令注入路径 | 对执行 agent 所提供命令的测试技能进行安全加固 | OPEN（更新于 2026-10-25） |
| 8 | [docx — report LibreOffice timeout as error](https://github.com/anthropics/skills/pull/1792) (#1792) | `accept_changes.py` 现在在 `soffice` 超时时失败，并在声称成功前验证 `w:ins`/`w:del`/`w:moveFrom` 已移除 | 静默成功的失败模式；属于 docx 可靠性问题簇的一部分 | OPEN（更新于 2026-09-25） |

*另外值得注意：* [#514 document-typography](https://github.com/anthropics/skills/pull/514)（生成文档的孤行/寡行与编号质量控制）和 [#525 pyxel](https://github.com/anthropics/skills/pull/525)（带无头帧检查的复古游戏开发）——两者都是长期存在、持续更新活跃的提交。

**规律：** 前 8 名中有 6 个是*对内置技能的修复*（mcp-builder、skill-creator、docx、webapp-testing），而非新技能。社区关注度最高的活动是官方技能的维护，而不是全新提交。

---

## 2. 社区需求趋势（来自 Issues）

- **已发布技能的信任、安全与治理**——最响亮的一条讨论线。[#492 namespace/trust-boundary abuse](https://github.com/anthropics/skills/issues/492)（46 条评论）认为以 `anthropic/` 名义分发的社区技能冒充了官方技能。并由 [#1394 eval-viewer XSS](https://github.com/anthropics/skills/issues/1394)、[#1487 `claude-api` injecting ~156k tokens](https://github.com/anthropics/skills/issues/1487)、[#2018 invalid XML from `merge_runs.py`](https://github.com/anthropics/skills/issues/2018) 进一步印证。
- **企业分发与共享**——[#228 org-wide skill sharing](https://github.com/anthropics/skills/issues/228)（17 条评论，8 👍）希望有一个共享库，而不是通过 Slack 传文件；[#189 duplicate skills across plugins](https://github.com/anthropics/skills/issues/189)（9 👍）和 [#62 lost skills](https://github.com/anthropics/skills/issues/62) 指向生命周期/注册表缺口。
- **评估与测试基础设施**——[#556 `run_eval.py` 0% trigger rate](https://github.com/anthropics/skills/issues/556)（13 条评论，7 👍）和 [#1390 mcp-builder evaluation scoring 0/N](https://github.com/anthropics/skills/issues/1390) 表明技能评估工具链本身不可靠。这正是 PR #1298、#1997、#1681 背后的需求。
- **技能编写质量/触发元数据**——[#2064 theme-factory description says *what* not *when*](https://github.com/anthropics/skills/issues/2064) 和 [#202 skill-creator should follow best practice](https://github.com/anthropics/skills/issues/202)：对描述编写指南和 token 高效的 SKILL.md 规范存在需求。
- **Agent 记忆与上下文经济性**——[#1329 compact-memory](https://github.com/anthropics/skills/issues/1329)（10 条评论）提出用符号记法压缩持久化 agent 状态。
- **质量门禁与 agent 治理**——[#1385 three-gate reasoning pipeline](https://github.com/anthropics/skills/issues/1385)、[#412 agent-governance](https://github.com/anthropics/skills/issues/412)：任务前校准、对抗性评审、交付验证、审计追踪。

净结论：需求正从“更多技能”转向**信任、评估严谨性和分发**——外加对垂直/企业工作流技能的稳定需求。

---

## 3. 高潜力待处理技能（活跃、未合并）

按最近更新时间排序，均为 OPEN：

1. [pptx — reject `--cols` < 1](https://github.com/anthropics/skills/pull/1975)（#1975，更新于 10-27）— 在 `thumbnail.py` 中快速失败，而不是等到下游 `ValueError`；关闭 #1809。
2. [webapp-testing — element_discovery fix](https://github.com/anthropics/skills/pull/1976)（#1976，更新于 10-18）— textarea/select 被误报为 `text`；关闭 #1891。
3. [algorithmic-art — `wrapAround()` actually wraps](https://github.com/anthropics/skills/pull/1977)（#1977，更新于 10-18）— 带负数处理的取模；关闭 #1897。
4. [webapp-testing — remove `shell=True`](https://github.com/anthropics/skills/pull/1980)（#1980，更新于 10-25）— 安全修复，本批次中影响最大。
5. [skill-creator — direct `package_skill.py` execution](https://github.com/anthropics/skills/pull/1681)（#1681，更新于 10-24）— `ModuleNotFoundError` 和过时的用法路径。
6. [pyxel retro-game skill](https://github.com/anthropics/skills/pull/525)（#525，更新于 10-23）— 无头、输入驱动的运行，帧检查。
7. [skill-creator — YAML frontmatter parsing](https://github.com/anthropics/skills/pull/1997)（#1997，更新于 10-19）— 引号/续行处理会破坏 eval 元数据。
8. [notion-spec-to-implementation + resume auditor](https://github.com/anthropics/skills/pull/1245)（#1245，更新于 10-15）— 仍在进行中的最大全新技能面。

注意其集中度：PR #1975/#1976/#1977 来自同一位贡献者（gerardrecinto），且每个都是小型、关联 issue、低风险修复——最有可能近期合并。

---

## 4. Skills 生态洞察

**社区最集中的需求，是让官方技能本身值得信任且可验证——安全、版本正确、评估可靠——远胜于对更多技能覆盖面的需求。**

---

# Claude Code 社区简报 — 2026-10-28

## 1. 今日亮点

今日的 issue 活动主要围绕一个系统性问题：**Cowork/云会话中的 GitHub 仓库授权**。至少有八个单独的开放 issue 报告了相同的故障模式——git 代理拒绝推送，提示 *"not in this session's authorized repository set"*，`add_repo`/`list_repos` 工具缺失，且用户提供的细粒度 PAT 不再被透传。与此同时，唯一发布版本（v2.1.312）是一个小型体验优化更新，为网关增加了 OIDC claim 灵活性，并在全屏模式下支持对排队消息点击发送。

---

## 2. 发布

**v2.1.312**
- 为 Claude apps 网关添加了 `oidc.user_id_claim`，允许从 `sub` 以外的 `id_token` claim 解析身份（例如 Microsoft Entra ID 的 `oid`）。这适用于企业 SSO 部署中 `sub` 为 pairwise/不稳定值的情况。
- 新增**点击发送**：在全屏模式下，点击排队消息下方的“立即发送”提示即可立即发送，与键盘快捷键一致。

未注明破坏性变更。

---

## 3. 热门 Issue

### 1. [#76248](https://github.com/anthropics/claude-code/issues/76248) — 云/Cowork 会话中 Git 代理阻止所有推送（41 条评论，👍16）
窗口期内互动量最高的 issue，也是今日主导主题的核心。即使用户提供自己的细粒度 PAT（此前可用），向会话“授权仓库集”之外的仓库推送仍会被拒绝。报告者怀疑是 `CCR_TEST_GITPROXY` 的灰度发布。👍 数和从 July 持续到 October 的评论量表明，这是一个影响真实工作流的长期未解决回归。

### 2. [#104695](https://github.com/anthropics/claude-code/issues/104695) — Windows 桌面端 "fizzy_lake" 重设计破坏深色主题（15 条评论，👍26）
今日反响数最高。报告称在 Desktop 3.6556.2 中存在刺眼文本、冷色调、字体/聊天渲染退化、侧边栏反色以及启动变慢。被标记为 `[invalid]`，但显然引发共鸣——一个以 invalid 关闭的 issue 获得 26 个赞，值得设计 QA 注意。

### 3. [#78795](https://github.com/anthropics/claude-code/issues/78795) — 为自动记忆主题文件引入触发式注入（15 条评论）
提议采用 rules 风格 frontmatter，使记忆主题文件按需加载而非始终加载。这与更广泛的上下文预算讨论相关（`/context` 压力、工具 schema 延迟加载）。零赞，但自 July 以来讨论持续。

### 4. [#103488](https://github.com/anthropics/claude-code/issues/103488) — “响应中途连接丢失”被追踪到来自 160.79.104.10 的 TCP reset
一份记录充分的报告：在 Oct 3–18 期间发生 39 次，通过抓包确认是服务端 reset，而非本地网络问题。其价值在于将诊断从猜测推进到证据。

### 5. [#82571](https://github.com/anthropics/claude-code/issues/82571) — 第三方 channel 插件没有面向用户的允许列表
MCP channel 插件只有在硬编码（discord/telegram/fakechat/imessage）时才会投递入站通知。第三方插件可以连接，但会静默投递失败。这是插件生态的阻塞点：从市场安装的插件是二等公民。

### 6. [#103994](https://github.com/anthropics/claude-code/issues/103994) — 写入 `.git/` 或 `.claude/` 时，`PreToolUse` 的 “ask” 在 `bypassPermissions` 下被覆盖
与安全相关：在非交互模式下，安全的 `ask`→`deny` 转换，在同一工具调用还写入 `.git/` 或 `.claude/` 时被特别绕过。触发条件很窄，潜在影响很高。

### 7. [#102874](https://github.com/anthropics/claude-code/issues/102874) — Routines API 在活跃 Projects 上返回 403 “this project is paused”
`POST .../routines/{routine_id}/fire` 失败并报权限错误，该错误与项目实际状态矛盾。这阻碍了程序化/CI 驱动的 routine 调用。

### 8. [#89245](https://github.com/anthropics/claude-code/issues/89245) — VS Code 扩展工具权限流间歇性关闭
当权限响应从未到达扩展时，长会话看起来会冻结。在 2.1.241 上仍可复现，并关联到 #44157——这是最常用 IDE 集成的一个长期可靠性问题。

### 9. [#105196](https://github.com/anthropics/claude-code/issues/105196) — macOS Desktop Code 标签页滚动振荡/文字重影
会话面板在两个相距约半行的滚动位置之间交替，导致渲染重影/重复。这是聚集的 UI 投诉中最新一个桌面端渲染回归。

### 10. [#101371](https://github.com/anthropics/claude-code/issues/101371) — 为 claude.ai 连接器设置仅代码作用域，或延迟工具名直到使用
将工具搜索/schema 延迟加载思路从 schema 扩展到 *名称*，并请求专门从 Claude Code 会话中排除 claude.ai 连接器。这直接针对连接器较多用户的上下文膨胀问题。

**同样值得关注：** [#96075](https://github.com/anthropics/claude-code/issues/96075)、[#97926](https://github.com/anthropics/claude-code/issues/97926)、[#96609](https://github.com/anthropics/claude-code/issues/96609)、[#100164](https://github.com/anthropics/claude-code/issues/100164)、[#98524](https://github.com/anthropics/claude-code/issues/98524)、[#105504](https://github.com/anthropics/claude-code/issues/105504)、[#104120](https://github.com/anthropics/claude-code/issues/104120) —— 均为同一 Cowork/GitHub 授权故障的变体。

---

## 4. 关键 PR 进展

过去 24 小时内只有 **3 个 pull request** 有更新，因此全部列出：

### 1. [#15620](https://github.com/anthropics/claude-code/pull/15620) [OPEN] — `fix(plugin-dev)`: 修正 agent/command 示例中的 tools 格式
修复 YAML 解析 bug：`tools:` 和 `allowed-tools:` 中的 JSON 数组语法（`["Read", "Write"]`）被解释为字面字符串而不是列表。在 `plugin-dev` 插件的 11 个文件中将其转换为逗号分隔的 YAML。这是一个低调但重要的修复——这个模板错误会传播到每个根据示例脚手架生成的插件。

### 2. [#105303](https://github.com/anthropics/claude-code/pull/105303) [CLOSED] — 从 `claude-plugins-official` 安装市场插件
将 `claude-code-plugins` 市场迁移为从官方注册表获取所有插件。值得注意的是，它显式依赖合并顺序：依赖 `claude-plugins-official#6905`（code-review 移植）和 `#6906`（插件版本）——过早合并会发布过时的 `code-review`，并导致更新变成空操作。该 PR 已关闭而非合并。

### 3. [#32753](https://github.com/anthropics/claude-code/pull/32753) [OPEN] — `docs`: 修复过时的文档 URL
关闭 #30628。在 12 个文件中将 `docs.anthropic.com` / `docs.claude.com` 替换为 `code.claude.com`（26 行变更），并有意不动 `CHANGELOG.md` 以保持历史准确性。低风险的文档清理，自 March 起保持开放。

> ⚠️ PR 吞吐量非常低（3 个更新，2 个开放，1 个关闭）。再加上 50-issue/24h 的更新量，评审积压严重偏向 issue 而非贡献。

---

## 5. 功能请求趋势

从本窗口期的 issue 中提炼：

1. **上下文与连接器作用域** — 多个请求指向同一方向：默认少加载。连接器 *schema* 已有延迟加载，但用户现在希望工具 *名称* 也延迟加载（#101371），并希望对记忆主题文件采用触发式注入（#78795）。预计围绕 `/context` 成本的压力会持续。

2. **插件/channel 可扩展性对等** — 第三方 MCP channel 插件需要用户管理的允许列表（#82571）；市场安装需要成为一等公民（#105303）。插件面增长快于其权限模型。

3. **Agent 运行的可验证性与可审计性** — #104409 提议签名运行回执，使 hooks、Mods 和 Agent SDK 能够验证 *某个子代理确实运行过*、使用哪个模型、基于什么输入。这是一个值得注意的转变：从“让 agent 做更多”转向“证明 agent 做了什么”。

4. **桌面端会话管理** — #104467 请求一个受支持的本地接口，用来枚举 Code 会话、检查进程/Remote Control 状态并将它们置于后台，动机是用户运行约 10 个长期会话并从移动端控制。

5. **开放网络上的 Agent 身份** — #101972 请求让 Claude 能作为代表用户行事的经过验证的 agent 通过 Cloudflare 检查。尚处早期，但随着 agentic 浏览扩展，可能会反复出现。

6. **企业身份灵活性** — 本周期交付代码中有所体现（`oidc.user_id_claim`），对非 `sub` claim 映射的需求仍在持续。

---

## 6. 开发者痛点

**P1 — Cowork/云 GitHub 授权损坏且不透明。** 今日最大的单一问题簇。至少八个 issue 中的症状包括：git 代理拒绝推送（*"not in this session's authorized repository set"*）、会话中缺少 `add_repo`/`list_repos` 工具、PAT 透传回归、设置 UI 显示绿色/已连接但会话仍然失败，以及连接器重连无法补救。报告者描述了相同设置下一个账号成功、另一个账号失败（#97926、#104693），以及会话中途损坏（#104120、#76248）。**核心挫败感是诊断性的，而不仅是功能性的：UI 状态与会话状态不一致，导致用户无法自助修复。**

**P2 — 桌面端 UI 回归正在侵蚀信任。** “fizzy_lake” 重设计（#104695，👍26）与滚动振荡/重影（#105196）几乎同时出现。性能（启动变慢）和无障碍（刺眼文本、对比度）方面的投诉表明，在 Windows 和 macOS 上，视觉 QA 已落后于发布节奏。

**P3 — 权限与 hook 语义存在边缘情况漏洞。** #103994 显示在 `bypassPermissions` 下，特定路径写入组合中 `ask` 被静默转换为允许；#89245 显示在长时间 VS Code 会话中权限响应被丢弃。两者都削弱了开发者对无人值守运行所依赖的护栏的信心。

**P4 — API 路径上的连接稳定性。** #103488 的抓包证据表明 TCP reset 源自服务端，而非客户端网络——用户很难绕过，并且会直接截断响应中途的生成。

**P5 — 针对仓库工作的计划/例程执行不可靠。** Routines 在活跃项目上触发 403（#102874），并且经常丢失仓库访问权限（#105504、#98588、#100131）。对于已经自动化夜间构建的用户来说，这会把一个便利功能变成静默失败模式。

**P6 — 第三方插件作者撞上未文档化的壁垒。** 允许列表 channel 插件（#82571）和模板格式 bug（#15620）意味着生态贡献者只有在构建之后才会发现限制。

---

*范围说明：本窗口期未提供 GitHub Discussions 数据，因此省略热门讨论部分。*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区摘要 — 2026-10-28

## 1. 今日亮点

Codex 发布了 **rust-v0.165.0**，内置 Google Cloud Vertex AI 提供商（`model_provider = "gcp"`），模型目录中新增 GPT-6.1 Terra，并带来导航改进（会话选择器中的仓库筛选、智能体命令中心中的目录分组）。与此同时，0.166.0 alpha 线推进至 **alpha.4**，未发布说明。社区关注点主要集中在 **Windows 回归问题**：VS Code 扩展 UI 无法启动（`26.1021.22027`）已成为评论数最多的问题，至少有四份重复报告，此外还有一组 MXC 沙箱和更新器故障。

---

## 2. 发布

### rust-v0.165.0（稳定版）

- **Google Cloud Vertex AI 提供商** — 新增原生提供商，通过 `model_provider = "gcp"` 配置。([#56265](https://github.com/openai/codex/pull/56265))
- **GPT-6.1 Terra 目录条目** — 已加入模型目录。([#57337](https://github.com/openai/codex/pull/57337))
- **会话与智能体导航** — 会话选择器新增仓库筛选，智能体命令中心新增目录分组，以便在大型工作区中更快导航。

### 预发布版

- `rust-v0.166.0-alpha.4`、`-alpha.3`、`-alpha.2` — 已发布，但无变更日志文本。
- `rust-v0.165.0-alpha.1.2` — 0.165 线上的补丁 alpha 版本。

> 注意：GPT-6.1 Terra 进入目录之际，正好有一份活跃缺陷报告（[#52450](https://github.com/openai/codex/issues/52450)），其中 GPT-6.1 Sol 会拒绝无害提示词——随着用户迁移到 6.1 系列，值得关注。

---

## 3. 热点问题

1. **[#56655](https://github.com/openai/codex/issues/56655) — VS Code 扩展 UI 无法启动（Windows，26.51021.22027）** · 33 条评论 · 👍38
   这是仓库中互动量最高的问题。Windows 上扩展升级后，Codex 侧边栏完全无法渲染。高反应数表明影响范围广泛；重复报告（[#57344](https://github.com/openai/codex/issues/57344)、[#57368](https://github.com/openai/codex/issues/57368)）表明这是一个阻塞发布的回归，且没有发布回滚指引。

2. **[#57422](https://github.com/openai/codex/issues/57422) — VS Code / code-server 扩展冻结整个编辑器** · 6 条评论
   同一发布版本的问题升级：在 code-server（linux-x64，自 2026-10-26 起在 Open VSX 上为稳定版）上，扩展会挂起整个编辑器进程，而不仅仅是 Codex 视图。报告称 `26.908.40401` 可正常工作，这给出了清晰的二分定位窗口。

3. **[#51731](https://github.com/openai/codex/issues/51731) — Dot 无法连接 Codex 会话（“unsupported placement format version 3”）** · 25 条评论
   Dot 与 Codex 应用之间的带版本通信格式不匹配，自 2026-10-07 起处于开启状态。长期存在且仍未解决——这是 Dots 集成面的兼容性/版本管理问题。

4. **[#50915](https://github.com/openai/codex/issues/50915) — 当无关的 BitLocker 卷被锁定时，MXC 沙箱失败** · 24 条评论 · 👍8
   只要存在*任意*已锁定的 BitLocker 数据卷，即使工作区并未引用该卷，Windows 沙箱就会在启动任何子进程之前中止。这是环境探测中的正确性缺陷，具有真实的企业影响。

5. **[#56264](https://github.com/openai/codex/issues/56264) — Windows 线程恢复失败，因为 MXC `process.env` 缺少 `SYSTEMROOT`** · 19 条评论
   更新后，现有的本地聊天无法恢复。与上一条相关：MXC 裁剪后的环境过于激进，甚至在读取 `AGENTS.md` 之前就出问题。

6. **[#2379](https://github.com/openai/codex/issues/2379) — 提示词输入撤销/重做（TUI）** · 14 条评论 · 👍37
   自 2025-08 开启，仍是数据集中得票最高的增强需求。用户持续要求在输入框中具备基础终端编辑体验（Cmd-Z / Shift-Cmd-Z）。

7. **[#57610](https://github.com/openai/codex/issues/57610) — Windows 提权沙箱：工作区根目录可写，嵌套目录被拒绝** · 2 条评论（新）
   针对 `26.1021.22027` 的最新报告显示，提权沙箱下的 ACL 传播不一致——这是 Windows 沙箱问题串中的最新数据点。

8. **[#52450](https://github.com/openai/codex/issues/52450) — GPT-6.1 Sol 以 `invalid_prompt` 拒绝无害提示词** · 9 条评论
   在 Plus 账户下，`Reply with exactly: OK` 被标记为违反使用政策，而 GPT-5.6 可以成功。这是模型路由/过滤回归，导致受影响用户无法使用整个 6.x 系列。

9. **[#57111](https://github.com/openai/codex/issues/57111) — Windows 应用在空闲时触发 Git 扫描 → 高 CPU / 过热** · 2 条评论
   在 14900K 上出现空闲资源消耗，对于长期运行的桌面智能体来说是严重的质量信号；这反映出工作区监听的退避不足。

10. **[#54574](https://github.com/openai/codex/issues/54574) — macOS：尽管已显式授权，基本 Git 写入仍被阻止** · 6 条评论
    权限控制没有提供可用的绕过方式——Codex 可以编辑文件，但不能运行 `git init`。这指向权限模型与 macOS 沙箱规则实际应用方式之间的差距。

**也值得关注：** [#57587](https://github.com/openai/codex/issues/57587)（未受管的 Unix app-server 在 0.165.0 中仍保持 1,024 FD 软限制）、[#54962](https://github.com/openai/codex/issues/54962)（Windows 自动审查拒绝显式人工授权），以及 [#50346](https://github.com/openai/codex/issues/50346) — 一个**已关闭**的 Linux 回归，其中 Ctrl+Shift+D 听写被静默移除。

---

## 4. 关键 PR 进展

以下所有条目均在 **2026-10-27/28 关闭**，几乎全部由 `copyberry[bot]` 编写——这表明存在一个高度自动化的落地流水线，以紧凑、可审查的批次运行。

1. **[#57615](https://github.com/openai/codex/pull/57615) — 在子智能体重载之间保留 rollout 追踪**
   此前，被重载/逐出的子智能体会停止追踪，导致后续轮次未被追踪；同时防止 Code Mode 会话跨会话复用 cell ID。直接提升多智能体运行的可观测性。

2. **[#57607](https://github.com/openai/codex/pull/57607) — 与模型提供商和目录共享运行时认证**
   新增 `ProviderAuthContext`，使目录刷新使用与推理请求相同的操作主体/凭据所有者。这是面向新的多提供商（Vertex AI）世界的前置管道工作。

3. **[#57590](https://github.com/openai/codex/pull/57590) + [#57589](https://github.com/openai/codex/pull/57589) — 发布独立的 `codex-exec-server`**
   将执行器启动逻辑抽取到 `codex-exec-server-cli`，在 CLI/app-server 包中打包独立二进制，通过 `codex-package.json` 中的 `execServer` 进行通告，并将其纳入 Cargo/Bazel 打包以及 macOS 签名。这是执行与客户端外壳之间值得注意的架构解耦。

4. **[#57606](https://github.com/openai/codex/pull/57606) — `codex doctor --network-only`**
   聚焦网络诊断：有效配置、网络环境、提供商 HTTP 可达性以及 Responses WebSocket 检查——跳过 DB 和 MCP 扫描。这是对连接性/缺陷报告分诊负担的直接回应。

5. **[#57597](https://github.com/openai/codex/pull/57597) — 保留 WebSocket 探测证据并脱敏诊断信息**
   当后续读取失败时保留升级证据，同时对 URL、服务器错误和关闭原因进行脱敏。与上面的 doctor 工作配套，提升共享诊断输出的安全性。

6. **[#57586](https://github.com/openai/codex/pull/57586) — 宿主预留的 V2 子项创建 API**
   `CodexThread::create_child` / `prepare_child` / `ThreadManager::create_prepared_child`，用于从实时工具调用创建继承设置的空闲子项。这是更确定性地生成子智能体的基础。

7. **[#57569](https://github.com/openai/codex/pull/57569) — 在背压下保留进程输出和控制回复**
   修复进程内事件队列填满时进程输出丢失和控制回复被阻塞的问题，并处理子进程退出后后代进程仍保持 stdout 打开的情况。这是针对长时间运行 exec 会话的真正持久性修复。

8. **[#57568](https://github.com/openai/codex/pull/57568) — 记忆整合的独立服务层级**
   新增 `memories.consolidation_service_tier`，默认值为 `"default"`，因此后台整合不再继承交互式会话的层级。为记忆功能提供成本/延迟控制。

9. **[#57567](https://github.com/openai/codex/pull/57567) — 内置 OpenAI 提供商的目录 URL 覆盖**
   允许在 `[model_providers.openai]` 下设置 `model_catalog_url`，同时通过 `openai_base_url` 保留身份、认证和推理路由；拒绝其他非默认覆盖。适用于企业/预发环境的目录测试。

10. **[#57584](https://github.com/openai/codex/pull/57584) — 在 token 截断过程中保留输出边界**
    新增 `OutputBoundary` API，将条目索引和 UTF-8 偏移映射穿过截断过程，并在被省略文本内部正确重映射。修复长输出中细微的截断/归因缺陷。

**同时落地：** [#57585](https://github.com/openai/codex/pull/57585)（在请求中替换不可用图像引用，同时保留历史）、[#57573](https://github.com/openai/codex/pull/57573)（对拦截的补丁命令进行无文件系统分类）、[#57572](https://github.com/openai/codex/pull/57572)（无需共享快照的 Apps 工具目录）、[#57598](https://github.com/openai/codex/pull/57598)（使用 FS 监听缓存空的远程技能发现结果）、[#57612](https://github.com/openai/codex/pull/57612)（在 VTE < 0.54 上禁用终端状态序列）。

---

## 5. 热门讨论

### 问答

- **[#56686](https://github.com/openai/codex/discussions/56686) — ChatGPT 认证的 `codex exec` 支持的请求前 token 边界**
  询问在诊断工作流限制为 250k 输入 token 的情况下，是否可以在不使用 API key 或替换传输层的前提下，对计入的 token 施加强制上限。实质上未获答复——这对有成本上限的自动化来说是一个真实缺口。

- **[#49965](https://github.com/openai/codex/discussions/49965) — Windows/Dot：本地 Codex 浏览器可用，但 Dot 任务无法控制它**
  寻求一种恢复路径：当 Computer Use 在普通本地聊天中可用，但在 Dot 启动的 Windows 任务中不可用时该怎么办。这与 [#51731](https://github.com/openai/codex/issues/51731) 中看到的 Dot/桌面端割裂相呼应。

### 展示与分享

- **[#56176](https://github.com/openai/codex/discussions/56176) — Agent Relay Check v0.1.4：全新会话是否真的会收到 `AGENTS.md`？**
  一种可重复的 marker-in / marker-out 方法，用于在嵌套 `AGENTS.md` 或覆盖项变更后验证指令路径传播。这是本周期最实用的社区产物。

- **[#57591](https://github.com/openai/codex/discussions/57591) — toli：让 Codex 和 Claude Code 跨机器协作**
  一个 Apache-2.0 工具，支持不同开发者机器之间的智能体直接通信，其动机是共享调试期间上下文出现分歧。

- **[#57524](https://github.com/openai/codex/discussions/57524) — BankBridge：通过远程 MCP + OAuth 进行只读银行/卡/投资访问**
  `codex mcp add bankbridge --url …` —— 演示了 MCP 流程中的动态客户端注册和 OAuth。作为安全远程 MCP 的参考实现而值得注意。

- **[#57463](https://github.com/openai/codex/discussions/57463) — Handoff Kit：继续前选择确切的已保存任务**
  解决同一工作区中有多个未完成任务时“读取我最新的交接”这一歧义——一种用于会话恢复的轻量约定。

- **[#57348](https://github.com/openai/codex/discussions/57348) — Reset NE：Codex 重置通知的追踪器和预测**
  汇总公开的重置更新、日历、历史和实验性预测——表明速率限制重置足够频繁，以至于值得第三方追踪。

- **[#56429](https://github.com/openai/codex/discussions/56429) — sports-video-coach：Codex 审阅你自己的体育视频**
  一个非编程用例，生成带时间戳的片段、训练动作和目标；展示了智能体在软件工程之外的良好使用方式。

- **[#56564](https://github.com/openai/codex/discussions/56564) — Haru Café：先建公司，再开咖啡馆，用 Codex 构建**
  一篇长篇构建故事，探讨智能体在身份和关系上的连续性。

*已过滤：* [#49577](https://github.com/openai/codex/discussions/49577) 是与主题无关的 SEO 内容，并非真正的 Codex 讨论。

---

## 6. 功能请求趋势

1. **一等公民的终端编辑（撤销/重做）** — 14 个月后，[#2379](https://github.com/openai/codex/issues/2379) 仍是得票最高的增强需求（👍37）。用户希望输入框与标准 readline/编辑器行为对齐。
2. **可见的用量和速率限制状态** — [#25561](https://github.com/openai/codex/issues/25561) 要求提供持久显示的剩余用量指示器；像 [#57348](https://github.com/openai/codex/discussions/57348) 这样的社区追踪器的存在证实了这一需求信号。
3. **无摩擦的 MCP 接入** — [#54925](https://github.com/openai/codex/issues/54925) 提议为第三方 MCP 应用提供“Connect with ChatGPT”一键 OAuth 流程；[#57524](https://github.com/openai/codex/discussions/57524) 已手动演示了该模式。
4. **会话/任务连续性与交接** — 多次要求可靠恢复：精确任务选择（[#57463](https://github.com/openai/codex/discussions/57463)）、可复现的 `AGENTS.md` 验证（[#56176](https://github.com/openai/codex/discussions/56176)），以及会话导航（0.165.0 的仓库筛选已部分解决）。
5. **跨智能体互操作性** — [#57591](https://github.com/openai/codex/discussions/57591) 以及子智能体 PR（[#57615](https://github.com/openai/codex/pull/57615)、[#57586](https://github.com/openai/codex/pull/57586)）指向对协调式多智能体、多机器工作流的需求。
6. **细粒度且被遵守的权限控制** — [#54574](https://github.com/openai/codex/issues/54574) 和 [#54962](https://github.com/openai/codex/issues/54962) 都报告显式人工授权被忽略；用户希望批准真正生效且可审计。

---

## 7. 开发者痛点

- **Windows 是主要的可靠性缺口。** 沙箱/MXC 缺陷（[#50915](https://github.com/openai/codex/issues/50915)、[#56264](https://github.com/openai/codex/issues/56264)、[#57610](https://github.com/openai/codex/issues/57610)）、扩展启动失败（[#56655](https://github.com/openai/codex/issues/56655)、[#57344](https://github.com/openai/codex/issues/57344)、[#57368](https://github.com/openai/codex/issues/57368)）以及更新器损坏（[#51588](https://github.com/openai/codex/issues/51588)、[#56788](https://github.com/openai/codex/issues/56788)）占了高评论数问题的大多数。
- **环境探测过于脆弱。** MXC 会在无关的已锁定 BitLocker 卷上失败，并在缺少 `SYSTEMROOT` 时崩溃——沙箱辅助程序假设的环境比它们实际收到的更完整。
- **升级路径没有安全网。** 用户在自行回滚（`26.1007.21434` 可用，`26.1021.22027` 不可用）。对于损坏的扩展版本，没有可见的固定版本或自动回滚指引。
- **重复报告推高分诊成本。** 至少四个问题描述同一个 VS Code 启动失败；社区正在手动完成去重工作——`codex doctor` PR（[#57606](https://github.com/openai/codex/pull/57606)、[#57597](https://github.com/openai/codex/pull/57597)）是朝着更好的首轮诊断迈出的良好一步。
- **权限模型与用户意图不匹配。** 多份报告称显式授予的批准被忽略（macOS Git、Windows 自动审查），这侵蚀了对沙箱的信任——可以说比直接拒绝更糟。
- **桌面应用的资源卫生。** 空闲 Git 扫描占满 CPU（[#57111](https://github.com/openai/codex/issues/57111)）以及 Unix app-server 中的 1,024 FD 软限制（[#57587](https://github.com/openai/codex/issues/57587)）表明，长期运行进程的限制尚未针对真实工作负载进行调优。
- **模型系列过渡存在风险。** [#52450](https://github.com/openai/codex/issues/52450) 使受影响账户无法使用 GPT-6.1，而 0.165.0 发布同时将 GPT-6.1 Terra 推广进目录——这是一个值得在更广泛推出前弥合的不匹配。

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区文摘 — 2026-10-28

## 今日亮点
今日有三个发布通道推进：稳定版 **v0.66.0**、**v0.67.0-preview.0** 以及每夜构建 **v0.67.0-nightly.20261028**，带来了 Android/Termux 下 ripgrep 路径修复以及 CRLF 安全的差异比较。Issue 流量继续集中在 **agent/subagent 可靠性**上——`MAX_TURNS` 后错误报告 `GOAL` 成功、通用型 agent 挂起、以及大量工具场景下的 400 错误占据了队列前列。本周期社区 PR 主要是体验优化类修复：取消处理、`\n` 转义正确性、Unicode 安全截断以及终端调整大小性能。

## 发布

**[v0.67.0-nightly.20261028.gfa5c17365](https://github.com/google-gemini/gemini-cli/releases)**
- `fix(core)`：允许 Android（Termux）上的受信任系统路径用于 ripgrep 解析（[#21836](https://github.com/google-gemini/gemini-cli/pull/29884)，@amelidev）——为此前二进制解析被拒绝的 Termux 用户解除阻塞。
- `fix(core)`：在 `getDiffContextSnippet` 中进行差异比较前规范化 CRLF 行尾（@jesussamuel-byte）——防止 Windows/CRLF 文件上出现虚假的差异上下文。

**[v0.67.0-preview.0](https://github.com/google-gemini/gemini-cli/releases)**
- 汇总了 v0.65.0 的变更日志，将每夜构建基线提升至 `0.67.0-nightly.20261020.gb041de017`，并包含 v0.66.0-preview 的变更日志。

**[v0.66.0](https://github.com/google-gemini/gemini-cli/releases)**
- 更新 `ROADMAP.md`，移除对过时的 2025 路线图的引用（[#29723](https://github.com/google-gemini/gemini-cli/pull/29723)，@DavidAPierce）。
- 为 v0.65.0-preview.0 自动生成变更日志，并进行例行版本号提升。

## 热门 Issue

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — MAX_TURNS 后的子 agent 恢复被报告为 GOAL 成功**（P1，13 条评论，👍2）
   `codebase_investigator` 子 agent 在尚未进行任何分析就耗尽轮次预算时，仍然返回 `status: "success"` / `Termination Reason: "GOAL"`。这是今日评论最多的 Issue，因为它会悄无声息地破坏 agent 层面的可观测性——调用方无法区分“已完成”和“已放弃”。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — 通用型 agent 挂起**（P1，8 条评论，👍8）
   队列中 reaction 最多的 Issue。任何对通用型 agent 的委托都会无限期挂起（甚至包括创建文件夹）；用户通过禁止使用 subagent 来规避。👍 持续增长表明该问题被广泛复现。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — 零依赖操作系统沙箱与执行后意图路由**（P2，9 条评论）
   一项架构提案，旨在充分利用 Gemini 3 原生的 bash 亲和性，同时通过沙箱 + 执行后意图路由来保证安全。这是关于模型应获得多少 shell 自由度的关键设计讨论串。

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini 未充分使用 skills 和子 agent**（P2，7 条评论）
   虽为轶事，但引起广泛共鸣：自定义 skills/subagents 只有在明确指示时才会触发。这直接削弱了团队正在构建的可扩展性叙事。

5. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 大量工具时出现 400 错误**（P2，3 条评论）
   超出工具预算时产生硬性 400 错误，而非优雅地缩小范围。（标题和正文在 128 还是 400 个工具上存在分歧——值得统一，因为该阈值对 MCP 密集型配置至关重要。）

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent 忽略 `settings.json` 覆盖（如 `maxTurns`）**（P2，4 条评论）
   `AgentRegistry` 在初始化时正确合并配置，但 Browser Agent 绕过了它。这是配置信任 bug，而非能力缺失。

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser 子 agent 在 Wayland 上失败**（P1，4 条评论）
   报告在失败时仍返回 `GOAL` 终止——与 #22323 相同的误导性终止模式，此次出现在 Linux/Wayland 桌面上。

8. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent 应停止/劝阻破坏性行为**（P2，3 条评论，👍1）
   模型在存在更安全替代方案时仍倾向于使用 `git reset` / `--force`，且缺乏对数据库等有状态资源的防护措施。

9. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — 评估 AST 感知的文件读取、搜索和映射**（EPIC，7 条评论）
   提议使用 AST 感知工具来减少错位读取、轮次数量和 token 噪音。与 [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) 和 [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)（tilth/glyph/ast-grep 候选方案）配套。

10. **[#22466](https://github.com/google-gemini/gemini-cli/issues/22466) — 错误的 `\n` 转义行为**（P2，2 条评论）
    简单的 `\n` 变通逻辑会破坏字符串字面量；现已有匹配的实现 PR（#29989），因此该 Issue 可能很快关闭。

## 关键 PR 进展

1. **[#29977](https://github.com/google-gemini/gemini-cli/pull/29977) — `fix(core)`：在 `read_background_output` 中遵守取消信号**（P2，需要帮助）
   该工具忽略了中止信号：已取消的调用仍会休眠完整的 `delay_ms` 然后照常读取日志。现在会立即以 `AbortError` 拒绝。

2. **[#29989](https://github.com/google-gemini/gemini-cli/pull/29989) — `fix(core,cli)`：错误的 `\n` 转义**（P2，维护者）
   修复了 core 和 CLI 中多处简单的转义/反转义路径，这些路径在编辑过程中会破坏字符串字面量、正则表达式和代码片段中合法的 `\n` 序列。

3. **[#29982](https://github.com/google-gemini/gemini-cli/pull/29982) — `fix(core)`：识别带点号的 Gemini 3 模型**
   `supportsMultimodalFunctionResponse()` 使用了 `startsWith('gemini-3-')`，在禁用动态模型配置时无法匹配带点号的模型名称——悄无声息地降级了多模态函数响应。

4. **[#29980](https://github.com/google-gemini/gemini-cli/pull/29980) — `feat(cli)`：`gemini extensions validate <path>`**
   新增 `gemini-extension.json` 的安装前/发布前验证，包括上下文文件路径安全检查，以及评估测试。

5. **[#29987](https://github.com/google-gemini/gemini-cli/pull/29987) — `fix(core)`：规范化 `@` 自动补全中的前导斜杠**
   使 `@/`、`@/src` 和 `@./package.json` 能正确解析，而非返回空补全。

6. **[#29993](https://github.com/google-gemini/gemini-cli/pull/29993) — `fix(ui)`：`sanitizeForDisplay` 中的代理对**
   按 UTF-16 代码单元截断可能拆分 emoji 的代理对并丢失字符；现在按码点边界截断。

7. **[#29965](https://github.com/google-gemini/gemini-cli/pull/29965) — `fix(cli)`：ExpandableText 截断与切换行为**
   配套的 Unicode 修复，以及折叠标签的切换状态正确性。

8. **[#29904](https://github.com/google-gemini/gemini-cli/pull/29904) — `fix(cli)`：限制终端宽度变化时的内联历史重渲染**
   通过限制内联渲染器中的调整大小工作，直接针对 [#21924](https://github.com/google-gemini/gemini-cli/issues/21924) 的闪烁/性能投诉。

9. **[#29988](https://github.com/google-gemini/gemini-cli/pull/29988) — `fix(vscode-ide-companion)`：追踪所有 Disposables**（另见 [#29994](https://github.com/google-gemini/gemini-cli/pull/29994)）
   一对多余的括号将两个注册变成了逗号表达式，泄漏了 `gemini.diff.accept` 和工作区文件夹监听器。两个独立的社区 PR 汇聚到同一修复。

10. **[#29995](https://github.com/google-gemini/gemini-cli/pull/29995) — `docs(readme)`：明确范围、限制和工具全景**
    将三个独立的 `[!NOTE]` 横幅整合为一个可快速浏览的免责声明，外加工具选择指南——随着 Google CLI 界面扩展，这是有用的定位工作。

## 功能请求趋势

- **Agent 可观测性与控制。** 反复出现的诉求包括：真实的终止原因、通过 `/chat share` 暴露 subagent 轨迹（[#22598](https://github.com/google-gemini/gemini-cli/issues/22598)），以及将 subagent 上下文纳入 `/bug` 报告（[#21763](https://github.com/google-gemini/gemini-cli/issues/21763)）。
- **上下文节俭。** AST 感知的读取/搜索/映射（[#22745](https://github.com/google-gemini/gemini-cli/issues/22745)、#22746、#22747）以及“Tactful Extraction”精准读取层级（[#19561](https://github.com/google-gemini/gemini-cli/issues/19561)）旨在缩减每轮约 36.6k token 的基线。
- **持久化、上下文之外的任务状态。** 用基于文件的 CRUD（[#18836](https://github.com/google-gemini/gemini-cli/issues/18836)）替代 `WriteToDo`，并试验为追踪器使用原生文件工具（[#21000](https://github.com/google-gemini/gemini-cli/issues/21000)）。
- **安全与沙箱。** 带意图路由的沙箱 bash（#19873）、劝阻破坏性 git/DB 操作（#22672），以及按工作区而非全局的策略（[#18397](https://github.com/google-gemini/gemini-cli/issues/18397)）。
- **Subagent 生态成熟度。** 基于 settings 的 subagent 发现（[#18285](https://github.com/google-gemini/gemini-cli/issues/18285)）、共享内存/并行协作（[#18287](https://github.com/google-gemini/gemini-cli/issues/18287)），以及符号链接的 agent 定义（[#20079](https://github.com/google-gemini/gemini-cli/issues/20079)）。
- **终端 UI 打磨。** 无闪烁调整大小（#21924）、歧义宽度字符处理，以及 Unicode 安全渲染。
- **自我认知。** CLI 应准确描述自身的标志、快捷键和自我调用方式（[#21432](https://github.com/google-gemini/gemini-cli/issues/21432)）。

## 开发者痛点

- **挂起与误导性成功状态。** 通用型 agent 无限期挂起（#21409），加上 `MAX_TURNS` 被报告为 `GOAL` 成功（#22323）以及 Wayland 浏览器 agent 在失败时报告 `GOAL`（#21983），构成一个集群：失败既是阻塞性的，又是不可见的。
- **Agent 委托在两个方向上都不靠谱。** Subagent 要么从不自发触发（#21968），要么在触发时挂起（#21409）；Browser Agent 的 settings 覆盖被完全忽略（#22267）。
- **工具/配置扩展限制。** 大量 MCP/工具清单触发硬性 400（#24246），模型用临时脚本弄乱工作区（#23571）。
- **跨平台小毛病。** Termux/Android ripgrep 解析、Wayland 浏览器会话、Windows CRLF 差异、东亚歧义宽度终端，以及 emoji 截断——本周期持续出现的环境特定故障流。
- **文本转义正确性。** 简单的 `\n` 处理（#22466）是高频、低容忍度的一类 bug，因为它会在编辑过程中悄无声息地改变用户代码。
- **可调试性差距。** `/bug` 报告省略了 subagent 内部信息（#21763），使得仅凭用户报告难以对上述故障进行分类排查。

---
*注：本数据集未包含 GitHub Discussions 数据，因此该部分省略。今日条目的 PR 评论数报告为 `undefined`，故未引用。*

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI 社区摘要 — 2026-10-28

## 1. 今日亮点

GitHub 在 24 小时内发布了四个版本（v1.0.104–v1.0.105-1），其中最引人注目的修复是可在长上下文会话中可靠选择 Copilot Fusion High/Max。v1.0.104 还更改了 Fusion 托管模型的默认设置，并修复了 Autopilot 会话在仍有后台任务挂载时提前停止的问题。与此同时，问题追踪器仍主要被以下诉求占据：真正的多模型/BYOK 会话、ACP 功能对齐，以及 Windows 和 WSL2 上持续存在的平台特定稳定性问题。

## 2. 版本发布

**v1.0.105 / v1.0.105-0 / v1.0.105-1**（2026-10-27）
- 在 `/model` 中选择 **Copilot Fusion High 或 Max**，现在可在长上下文会话中正常工作。`-0` 和 `-1` 构建似乎是同一修复的增量重新构建（“Fixes and changes”）。

**v1.0.104**（2026-10-27）
- 托管模型 `fusion` 现在会以 **Auto** 启动会话，并应用 `fusionTier` 设置。
- 交互式和 SDK Autopilot 会话现在会**继续等待已挂载的后台任务**，而不是在超过 `COPILOT_TASK_WAIT_TIMEOUT_SECONDS` 后停止；`-p --autopilot` 仍会遵守该超时。
- 保存的 Auto 偏好处理方式有所变更（源发布说明已截断）。

> 要点：围绕 Fusion 模型和 Autopilot 生命周期，补丁发布节奏很快。对于 SDK/自动化用户而言，Autopilot 后台任务方面的变更在行为上影响最大。

## 3. 热门 Issue

1. **[#3709](https://github.com/github/copilot-cli/issues/3709) — 允许 `/model` 在单个会话中在多个模型之间切换，包括 BYOK/本地提供商**（OPEN，12 条评论，👍34）
   这是该时间窗口内获赞最多、讨论最多的 issue。BYOK 会通过 `COPILOT_MODEL` 将会话固定到单一模型，而选择器只列出 GitHub 托管的模型。社区反应强烈：用户希望能够在托管提供商和本地提供商之间按会话切换模型。

2. **[#2760](https://github.com/github/copilot-cli/issues/2760) — 为 429 响应实现正确的 HTTP 重试逻辑**（CLOSED，10 条评论，👍2）
   记录了无退避的激进重试循环（每分钟 20+ 次请求）。现已关闭——值得关注关联修复，因为它很可能也会影响 BYOK 的限流行为。

3. **[#3260](https://github.com/github/copilot-cli/issues/3260) — 通过 SSH + tmux 复制/粘贴到 Windows Server 2025 时失效**（OPEN，9 条评论，👍1）
   这是一个范围较窄但令远程开发者痛苦的平台回归问题（自 v1.0.47 起）。持续有评论活动，表明问题仍可复现且尚未解决。

4. **[#310](https://github.com/github/copilot-cli/issues/310) — Copilot CLI 中反复出现网络错误**（CLOSED，7 条评论，👍1）
   长提示词会因“Connection error”而失败，除非用户运行 `/clear`。这是一个长期存在的 issue，现已关闭——这是一个有用信号，表明大型提示词的传输可靠性正在得到处理。

5. **[#1869](https://github.com/github/copilot-cli/issues/1869) — `gpt-5-mini` 无法为后续会话持久设置**（OPEN，7 条评论，👍1）
   重启后，`/model` 的选择会回退到 `claude-sonnet-4.6`。这是一个常见的配置小毛病，会削弱用户对模型设置的信任。

6. **[#4275](https://github.com/github/copilot-cli/issues/4275) — ACP：将 `contextTier` 暴露为会话配置选项**（OPEN，6 条评论，👍4）
   交互式 `/model` 可以在会话中途更改上下文层级，但 ACP 客户端只能在启动时设置。这是编辑器/智能体集成中一个明显的能力对齐缺口。

7. **[#505](https://github.com/github/copilot-cli/issues/505) — 支持 MCP 服务器提示词**（OPEN，5 条评论，👍2）
   请求支持类似 VS Code 的 `/mcp.<server>.<prompt>`。近一年后仍处于 OPEN，反映出对一等公民式 MCP 提示词处理的稳定需求。

8. **[#5197](https://github.com/github/copilot-cli/issues/5197) — 1.0.100 之后 WSL2 上语音服务器发生段错误（signal 11）**（OPEN，5 条评论，👍2）
   `/voice` 会卡在“Fetching voice models”，因为语音运行时在 WSL2 上崩溃。这是一个与特定版本相关的新回归，并附有复现细节。

9. **[#4026](https://github.com/github/copilot-cli/issues/4026) — Copilot CLI 在 Windows 上反复崩溃（原生运行时）**（OPEN，5 条评论）
   自 2026 年 5 月以来，至少在四个版本中可复现。长期存在的 Windows 稳定性问题持续侵蚀着大量用户的信心。

10. **[#4994](https://github.com/github/copilot-cli/issues/4994) — ACP：尽管令牌有效，每个会话仍会重新进行 MCP OAuth 授权**（OPEN，4 条评论，👍3）
    从 1.0.88 → 1.0.89 的回归：每个 `--acp` 会话都会重新打开 OAuth 浏览器页面。这会阻塞无头/自动化 ACP 工作流，是快速修复的有力候选。

## 4. 关键 PR 进展

过去 24 小时内没有拉取请求更新（`Total: 0 items`）。今天没有 PR 级别的变更可总结。

## 5. 热门讨论

本时间窗口未提供讨论数据，因此省略本节。

## 6. 功能请求趋势

- **BYOK 和多提供商灵活性** 是最突出的主题：单个会话内多模型切换（[#3709](https://github.com/github/copilot-cli/issues/3709)）、按智能体选择提供商（[#4703](https://github.com/github/copilot-cli/issues/4703)）、在 `providers.json`/托管设置中使用凭据命令（[#4936](https://github.com/github/copilot-cli/issues/4936)），以及在 BYOK 下保留 MCP 工具图像（[#4600](https://github.com/github/copilot-cli/issues/4600)）。
- **ACP / 无头模式与交互式 CLI 的对齐**：`contextTier` 配置（[#4275](https://github.com/github/copilot-cli/issues/4275)）、ACP 会话中的插件技能（[#5269](https://github.com/github/copilot-cli/issues/5269)）、可用的 `/rename`（[#5115](https://github.com/github/copilot-cli/issues/5115)），以及 OAuth 静默解析（[#4994](https://github.com/github/copilot-cli/issues/4994)）。
- **企业部署与策略控制**：系统级 Windows 安装程序（[#5129](https://github.com/github/copilot-cli/issues/5129)）、`allowedMcpServers` 匹配修复（[#4989](https://github.com/github/copilot-cli/issues/4989)），以及组织级自定义智能体可见性（[#3572](https://github.com/github/copilot-cli/issues/3572)）。
- **MCP 可扩展性**：服务器提供的提示词（[#505](https://github.com/github/copilot-cli/issues/505)）和可靠的多模态工具结果（[#4600](https://github.com/github/copilot-cli/issues/4600)）。
- **会话生命周期管理**：取消归档/恢复会话（[#3518](https://github.com/github/copilot-cli/issues/3518)，👍11）、内联计划注释（[#4563](https://github.com/github/copilot-cli/issues/4563)），以及持久事件投递（[#5100](https://github.com/github/copilot-cli/issues/5100)）。
- **模型路由透明度与调优**：为 Auto 配置可调推理强度（[#4560](https://github.com/github/copilot-cli/issues/4560)），以及可配置的重试/退避次数（[#3679](https://github.com/github/copilot-cli/issues/3679)）。

## 7. 开发者痛点

- **模型选择不透明且无法持久化。** 设置会在不同会话之间回退（[#1869](https://github.com/github/copilot-cli/issues/1869)），Auto 会强制设为 `reasoningEffort: null`（[#4560](https://github.com/github/copilot-cli/issues/4560)），而 Auto 选择 GPT-6.1 Sol 可能导致 Plan → Implementation 转换卡住（[#5270](https://github.com/github/copilot-cli/issues/5270)）。
- **重试/限流行为浪费时间和配额。** 激进的 429 重试（[#2760](https://github.com/github/copilot-cli/issues/2760)）以及 Azure BYOK 在约 0.15 秒内耗尽五次重试（[#3679](https://github.com/github/copilot-cli/issues/3679)）是反复出现的抱怨。
- **平台可靠性缺口。** Windows 原生崩溃跨多个版本（[#4026](https://github.com/github/copilot-cli/issues/4026)）、WSL2 语音段错误（[#5197](https://github.com/github/copilot-cli/issues/5197)），以及通过 SSH + tmux 时复制/粘贴失效（[#3260](https://github.com/github/copilot-cli/issues/3260)）。
- **静默失败比错误更糟。** 并行工具调用下 `preToolUse` 钩子被绕过（[#2893](https://github.com/github/copilot-cli/issues/2893)）、MCP 图像丢失（[#4600](https://github.com/github/copilot-cli/issues/4600)）、缺少 `task_complete` 输出（[#2482](https://github.com/github/copilot-cli/issues/2482)），以及 `/rename` 报告虚假成功（[#5115](https://github.com/github/copilot-cli/issues/5115)）。
- **企业限制阻碍采用。** 用户配置文件安装与安全策略冲突（[#5129](https://github.com/github/copilot-cli/issues/5129)），并且允许列表匹配和组织智能体发现仍不可靠（[#4989](https://github.com/github/copilot-cli/issues/4989)、[#3572](https://github.com/github/copilot-cli/issues/3572)）。
- **会话持久性很脆弱。** 单次 120 秒主机确认超时即可使会话在恢复前不可用（[#5100](https://github.com/github/copilot-cli/issues/5100)），而已归档会话无法恢复（[#3518](https://github.com/github/copilot-cli/issues/3518)）。

---

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区简报 — 2026-10-28

## 1. 今日亮点

OpenCode **v2.2.0** 发布，带来实验性的**桌面端插件**，首次向社区构建的扩展开放桌面客户端。Issue 活动仍由**免费额度限制大型讨论帖**（#59387，35 条评论）和一组高严重级别的 Windows/WSL 回归问题主导。PR 方面，核心贡献者合入了进程守护进程就绪状态重构系列，并正在从 `packages/core` 中裁剪未使用的 AI SDK provider 包。

---

## 2. 发布

### v2.2.0
- **桌面端插件（实验性）** — 创建、安装并分享桌面 GUI 插件。通过 [#55246](https://github.com/anomalyco/opencode/pull/55246)、[#59469](https://github.com/anomalyco/opencode/pull/59469)、[#59489](https://github.com/anomalyco/opencode/pull/59489) 合并。
- 文档：https://opencode.ai/v2/docs/build/plugins/gui
- 值得注意：API 接口明确为实验性，因此插件作者应预期在稳定前会有破坏性变更。

---

## 3. 热门 Issue

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — 免费额度问题：先读这里（35 条评论，👍7）**  
   针对 `Free usage exceeded`、每日限额不重置以及 Desktop 版本门控的权威分诊帖。评论量大但未解决，说明免费额度配额路径仍缺乏清晰的诊断信息——这是目前仓库中最活跃的 issue。

2. **[#52237](https://github.com/anomalyco/opencode/issues/52237) — 远程 MCP 服务器一旦失败便不再重试（13 条评论）**  
   在一次瞬时故障后（尤其是 macOS 睡眠/唤醒终止长连接 HTTP 连接），后台服务会将 MCP 服务器固定在 `failed` 状态，直到重启。重启 TUI 也无济于事，因为状态保存在守护进程中。对于任何依赖远程 MCP 工具的人来说，这是真正的可靠性阻碍。

3. **[#52266](https://github.com/anomalyco/opencode/issues/52266) — Windows：控制台窗口约每秒闪烁一次（8 条评论，👍3）**  
   启动子进程时缺少 `CREATE_NO_WINDOW`，导致每次调用 `git.exe` 都会出现可见的控制台闪烁。这是 #22414 和 #31629 的重复报告——一个长期存在、反复出现的回归问题，因此正在获得 👍。

4. **[#53011](https://github.com/anomalyco/opencode/issues/53011) — `edit` 工具会重复数字替换值（8 条评论）**  
   将 `640` 替换为 `900` 会得到 `900900`，观察到的重复倍数从 2 倍到 129 倍不等。核心编辑路径中的静默文件损坏——状态为 `[triaging]`，但显然是列表中风险最高的正确性 bug。

5. **[#52378](https://github.com/anomalyco/opencode/issues/52378) — 失败的子智能体被作为成功上报给父级（8 条评论）**  
   `finish:"error"` 且带 `MALFORMED_FUNCTION_CALL` 和空内容时，却作为子任务成功完成上报。标记为 `[reproduced]`；这会破坏任何构建多智能体工作流者的编排语义。

6. **[#52363](https://github.com/anomalyco/opencode/issues/52363) — 连接 Zen 或 Go 后 OpenAI 模型消失（8 条评论，`severity:high`）**  
   ChatGPT OAuth 登录成功且凭据正确存储，但 `openai` provider 从未注册——`/models` 中没有任何可选内容。影响所有认证方式（浏览器和无头），因此这对 OpenAI 用户来说是完整的 provider 注册表故障。

7. **[#52234](https://github.com/anomalyco/opencode/issues/52234) — 硬编码端口 49374 不可用时静默挂起（7 条评论）**  
   没有回退端口，也没有绑定错误——TUI 永远停在 `Starting background server...`。在 WSL2/Hyper-V 上尤其痛苦，因为宿主会不可预测地保留端口范围。修复成本低，用户影响大。

8. **[#52154](https://github.com/anomalyco/opencode/issues/52154) — 消息历史未按会话隔离（7 条评论，👍2）**  
   上箭头会召回全局最近会话的最后一条消息，而不是当前会话的。对于运行许多并发会话的用户来说，这是一个会不断累积的小痛点——而这正是 OpenCode 鼓励的工作流。

9. **[#52333](https://github.com/anomalyco/opencode/issues/52333) — 热重载期间 `provider.use` 拒绝策略被绕过（`severity:high`）**  
   在配置/插件重载期间存在一个窗口，被拒绝的 provider 会变为可选；如果提示正好落入该窗口，就会静默地把系统提示 + 会话历史路由到第三方免费 provider。这是安全问题，而不只是正确性问题。

10. **[#52205](https://github.com/anomalyco/opencode/issues/52205) — Windows Desktop 将 WSL UNC 路径传给 Linux 服务器（5 条评论）**  
    选择 WSL 文件夹会返回 `\\wsl.localhost\...`，Linux 侧服务器无法处理——导致 HTTP 500 和持续启动崩溃。`[reproduced]`；完全阻塞 Windows-Desktop + WSL-server 组合。

**同样值得关注：** [#52203](https://github.com/anomalyco/opencode/issues/52203)（Windows TUI 在终端关闭后仍存活，泄漏 CPU/内存）、[#52122](https://github.com/anomalyco/opencode/issues/52122)（Nix 桌面版启用了生产自动更新器）、[#52178](https://github.com/anomalyco/opencode/issues/52178)（Zen API CORS 仅作用于 `/models`，所有推理预检返回 404）。

---

## 4. 关键 PR 进展

1. **[#59771](https://github.com/anomalyco/opencode/pull/59771) — 移除捆绑的 Cohere、Vercel、Perplexity AI SDK 包**  
   移除 `@ai-sdk/cohere`、`@ai-sdk/vercel`、`@ai-sdk/perplexity` 及其现已冗余的 provider 插件和未使用的 `createProviderPlugin` 辅助函数。有意义的包体积/锁文件瘦身，并简化 provider 插件层。

2. **[#59700](https://github.com/anomalyco/opencode/pull/59700) / [#59701](https://github.com/anomalyco/opencode/pull/59701) / [#59702](https://github.com/anomalyco/opencode/pull/59702) — 进程守护进程控制系列（jlongster）**  
   通过 `Control` 路由停止/所有权操作，用就绪信号替代轮询，并在 Windows 上每 25 ms 轮询一次。从 `opencode-pty` 移植；直接针对守护进程生命周期不稳定问题，而这也是上述多个 issue 的根源。

3. **[#53233](https://github.com/anomalyco/opencode/pull/53233) — 遵守压缩保留文本预算**  
   通过强制保留文本不变式来修复 [#43250](https://github.com/anomalyco/opencode/issues/43250)，在没有完整交换可容纳时允许空的近期尾部。长期修复（2026-10-04 开启）针对上下文压缩正确性。

4. **[#59765](https://github.com/anomalyco/opencode/pull/59765) — 将 provider 的 `model()` 设置改为可选，统一 `Config` 类型**  
   移除必需的 `{}` 参数，并统一各 provider 中 `Config` / `LanguageModelOptions` / `Options` 的命名。对编写自定义 provider 的人是小幅 DX 提升。

5. **[#59773](https://github.com/anomalyco/opencode/pull/59773) — 统计：忽略路由前被拒绝的请求**  
   停止统计 `attempts = 0` 的 `generation.completed` 事件，这些事件曾用幻影模型污染 `/data` 的模型维度。清理了公开使用分析。

6. **[#59437](https://github.com/anomalyco/opencode/pull/59437) — `feat(core): add /goal with a client-side done check`**  
   作为 #58508 的替代方案：相同的空闲钩子、`/goal` 命令和 TUI，但不新增 `session.settled` 服务端事件，也不在压缩钩子上加 `native` 标志。客户端从已同步数据推导“完成”——一种侵入性更低的智能体循环控制设计。

7. **[#59403](https://github.com/anomalyco/opencode/pull/59403) — `refactor(nix): fetch dependencies from bun.lock`**  
   关闭 [#49063](https://github.com/anomalyco/opencode/issues/49063)；消除手工维护的平台特定 `node_modules` 哈希。移除了一项反复出现的 Nix 打包维护负担。

8. **[#59784](https://github.com/anomalyco/opencode/pull/59784) / [#59783](https://github.com/anomalyco/opencode/pull/59783) / [#59785](https://github.com/anomalyco/opencode/pull/59785) — 会话标题过滤、认证列表账户标签、从评审中复制完整文件**  
   来自 `lancy69` 的三项社区 UX 贡献，各自关闭一个请求（#56843、#59040、#58878）。均标记为草稿，等待设计批准——值得在对应 issue 中发表意见。

9. **[#59630](https://github.com/anomalyco/opencode/pull/59630) — 将 Effect 升级到 4.0.6**  
   包含 SQLite 雪花 ID 舍入修复（Effect-TS/effect#9485）。低风险依赖升级，并带来具体的数据完整性收益。

10. **[#59770](https://github.com/anomalyco/opencode/pull/59770) — TUI：使用小键盘 Home/End 导航会话记录**  
    关闭 [#55533](https://github.com/anomalyco/opencode/issues/55533)（在 #57114 关闭后重新提交）。修复 kitty 键盘协议下的小键盘按键处理。

---

## 5. 功能请求趋势

- **端点与密钥的可配置性** — `OPENCODE_PASSWORD_FILE`（[#52156](https://github.com/anomalyco/opencode/issues/52156)）以及每个 provider 可编辑的 base/console API URL（[#52218](https://github.com/anomalyco/opencode/issues/52218)）。用户希望配置对容器/密钥管理器友好，并支持自托管/替代网关。
- **多会话体验** — 按会话隔离消息历史（[#52154](https://github.com/anomalyco/opencode/issues/52154)）以及按标题过滤会话列表（PR [#59784](https://github.com/anomalyco/opencode/pull/59784)）。明确信号：重度用户现在会运行许多并发会话。
- **CLI 表面打磨** — 在 `auth list` 中区分已存储账户（[#59040](https://github.com/anomalyco/opencode/issues/59040)），并让 `opencode mini` 的进程控制与主 TUI 对齐：可配置退出和 `ctrl+z` 挂起（[#52695](https://github.com/anomalyco/opencode/issues/52695)）。
- **智能体循环控制** — 带客户端完成检查的 `/goal`（[#59437](https://github.com/anomalyco/opencode/pull/59437)），外加用于关闭标签页的浏览器 API 操作（[#59787](https://github.com/anomalyco/opencode/issues/59787)，已关闭），以便智能体可以自行清理。
- **评审/UI 便利性** — 从评审中复制完整文件内容（[#58878](https://github.com/anomalyco/opencode/issues/58878)）以及图片预览中的画廊导航（#58295）。
- **构建来源 / 可观测性** — 在桌面调试导出中记录固定的 Electron 版本（[#52125](https://github.com/anomalyco/opencode/issues/52125)），以便区分 Nix 构建与上游二进制。

---

## 6. 开发者痛点

- **Windows 是摩擦的主要来源。** 控制台窗口闪烁（[#52266](https://github.com/anomalyco/opencode/issues/52266)）、终端关闭后 TUI 进程成为孤儿（[#52203](https://github.com/anomalyco/opencode/issues/52203)）、包含空格的 `EDITOR` 路径在 Git-Bash 下出错（[#52265](https://github.com/anomalyco/opencode/issues/52265)），以及 WSL UNC 路径导致 Desktop↔server 链路崩溃（[#52205](https://github.com/anomalyco/opencode/issues/52205)）。其中多项是重复报告（例如 #22414、#31629），这更像是优先级不足，而不是新的故障。
- **Provider/model 注册表不一致。** 尽管凭据有效，模型仍从选择器中消失（[#52363](https://github.com/anomalyco/opencode/issues/52363), [#52118](https://github.com/anomalyco/opencode/issues/52118)）——认证成功但注册失败，且没有暴露错误。
- **静默失败而不是错误消息。** 硬编码端口 `49374` 无限挂起（[#52234](https://github.com/anomalyco/opencode/issues/52234)）；没有根入口点的本地插件被跳过且不发出警告（[#52300](https://github.com/anomalyco/opencode/issues/52300)）；失败的子智能体被报告为成功（[#52378](https://github.com/anomalyco/opencode/issues/52378)）。反复出现的主题是失败边界处缺少诊断信息。
- **长连接脆弱性。** MCP 服务器在一次失败后永不重试（[#52237](https://github.com/anomalyco/opencode/issues/52237)）——睡眠/唤醒是常见触发因素。这与 PR #59700–#59702 中的进程守护进程就绪状态工作相关。
- **重载下的安全策略缺口。** 热重载期间可绕过拒绝列表（[#52333](https://github.com/anomalyco/opencode/issues/52333)），削弱了任何运行不可信插件者对 `experimental.policies` 的信任。
- **模板 `$` 序列处理。** 格式化器参数和提示模板会破坏包含 `$&`、`$'`、`` $` ``、`$$` 的路径（[#52314](https://github.com/anomalyco/opencode/issues/52314)）——一个在多个调用点重复出现的 `String.replace` 陷阱。
- **免费额度困惑。** 一个 35 条评论的巨型讨论帖（[#59387](https://github.com/anomalyco/opencode/issues/59387)）针对似乎不会重置的配额，说明在更多用户遇到之前，消息提示和/或重置逻辑需要改进。

---

*注意：本周期未提供 Discussions 数据，因此该部分省略。*

---

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区摘要 — 2026-10-28

## 1. 今日亮点
过去 24 小时内没有新版本发布，但维护者经历了繁忙的分诊日：一批 P0 和 `durable` Issue 被关闭，涉及会话恢复丢失内置工具、OpenAI Responses 工具重放以及 Bedrock schema 限制。在开放问题方面，两个长期存在的可靠性问题仍然突出——推理模型在上下文上限处导致会话永久卡死（#9409），以及 Anthropic 流式传输/工具注册脆弱性（#10390、#11376）。

## 2. 发布
_过去 24 小时无新发布。_

## 3. 热门 Issue

1. **#10953 — OpenAI 登录在 ChatGPT Business 下失败，旧版 Codex 登录可用** `[CLOSED] [no-action]` · 11 条评论 · 👍3
   今日评论最多的 Issue：`3p_delegated_access_policy_denied` 阻止 Business 工作区的常规 OpenAI 订阅登录，而旧版 Codex 路径则成功。以 no-action 关闭，但企业工作区的认证/策略不匹配问题很可能再次出现。
   [Link](https://github.com/earendil-works/pi/issues/10953)

2. **#9409 — 推理模型在上下文上限处导致会话永久卡死** `[OPEN]` · 10 条评论 · 👍3
   每个请求都返回 `stopReason: "length"`，输出 16 个 token，自动压缩也无法恢复。这是影响最大的开放 bug 之一，影响任何长时间运行的推理模型会话。
   [Link](https://github.com/earendil-works/pi/issues/9409)

3. **#10390 — Anthropic SSE 解析器错误处理跨 chunk 拆分的 CRLF** `[OPEN] [bug]` · 7 条评论
   当 `\r` 和 `\n` 分别到达不同的网络 chunk 时，解析器会刷出一个空事件并中止该轮次。一个典型的流式边界情况，但对 Anthropic 用户影响范围极大。
   [Link](https://github.com/earendil-works/pi/issues/10390)

4. **#11376 — Anthropic：会话中途移除初始工具会破坏后续所有请求** `[OPEN] [inprogress, prio:p0]` · 4 条评论
   重新加载一个不再注册某工具的扩展后，之后每次调用都会产生 `tool_reference_unresolved`。凸显了扩展生命周期与提供商工具声明状态之间的缺口。
   [Link](https://github.com/earendil-works/pi/issues/11376)

5. **#11460 — 会话分支被截断后恢复会丢弃所有内置工具（ENOSPC 后产生孤立的 parentId）** `[CLOSED] [prio:p0, fix-available]` · 4 条评论
   磁盘已满的情况可能使会话图留下孤立父节点，从而在恢复的会话中静默移除 `read/bash/edit/...`。这是一起严重的数据完整性类故障，现已提供修复。
   [Link](https://github.com/earendil-works/pi/issues/11460)

6. **#11415 — openai-responses：来自其他模型的 function_call 项在重放时缺少命名空间** `[CLOSED] [prio:p0]` · 3 条评论
   通过 `additional_tools` 声明的工具导致 OpenAI 400，因为重放的调用缺少命名空间。已由 agent 针对 `api.openai.com` 实时复现；这是一项高价值的提供商兼容性修复。
   [Link](https://github.com/earendil-works/pi/issues/11415)

7. **#11416 — Bedrock：可选参数超过 16 个时请求失败** `[CLOSED] [prio:p0]` · 2 条评论
   Bedrock 会拒绝包含超过 16 个 union/可选参数的 schema，使原本有效的工具定义失效。值得关注，因为它给 Bedrock 用户施加了硬性的工具 schema 预算。
   [Link](https://github.com/earendil-works/pi/issues/11416)

8. **#11500 — 压缩/摘要请求与实时轮次不共享前缀** `[CLOSED] [untriaged]` · 2 条评论
   每次压缩都要支付完整 prefill 成本，因为摘要器从全新提示开始，不带系统提示/工具声明。这直接促成了 PR #11499；对长会话有重大成本/延迟影响。
   [Link](https://github.com/earendil-works/pi/issues/11500)

9. **#11493 — Codex：尽管有持久缓存保证，空闲后仍完全缓存未命中** `[CLOSED] [untriaged]` · 2 条评论
   这与 OpenAI 对已登录 Codex 用户的明确保证相矛盾，表明 Pi 可能在恢复过程中改变了请求形状或标识符。缓存行为是恢复会话中反复出现的成本问题。
   [Link](https://github.com/earendil-works/pi/issues/11493)

10. **#11433 — pi-env：管道读取器或等待线程启动失败时 exec 挂起** `[OPEN] [bug]` · 2 条评论
    线程启动失败（例如 cgroup 任务数限制）会触发 panic 而不是返回错误，导致 exec 没有回复且取消机制失效。一种罕见但严重的 daemon 级挂起。
    [Link](https://github.com/earendil-works/pi/issues/11433)

_荣誉提名：_ #7738（并发压缩共享一个 `AbortController` → 崩溃）、#11497（`SqliteStorage` 在多个 opener 下生成 ID）、#11489（`pi update` 误判 vite+ 安装）。

## 4. 关键 PR 进展

1. **#11499 — feat(compaction)：保留前缀的摘要调用** `[CLOSED]`
   重构摘要逻辑，传递系统提示和工具声明，按原样重放对话，使压缩与实时轮次共享 token 前缀。#11500 的直接修复，也是当天影响最大的性能变更。
   [Link](https://github.com/earendil-works/pi/pull/11499)

2. **#11498 — docs(coding-agent)：恢复完整的扩展指南** `[CLOSED]`
   恢复约 3,100 行扩展 API 参考和工具指南——此前的文档刷新将其删减到 130 行——并依据当前源码进行校正。这对扩展生态很重要。
   [Link](https://github.com/earendil-works/pi/pull/11498)

3. **#11496 — fix(ai)：限制 OpenRouter 推理 token** `[CLOSED]`
   发送嵌套的 `reasoning.max_tokens`，在有预算时省略 `reasoning.effort`（OpenRouter 会同时拒绝二者），并进行钳制以保留 ≥1,024 个回答 token。
   [Link](https://github.com/earendil-works/pi/pull/11496)

4. **#11494 — fix(ai)：将 LiteLLM ContextWindowExceededError 识别为上下文溢出** `[CLOSED]`
   将 LiteLLM 调用前的 400（`Max Input Tokens=…, Got=…`）识别为溢出，以便触发自动压缩，而不是暴露原始提供商错误。
   [Link](https://github.com/earendil-works/pi/pull/11494)

5. **#11491 — fix(coding-agent)：检测 vite+ 全局安装并通过 vp 自更新** `[CLOSED]`
   新增 `vite-plus` 安装方式，使 `pi update` 不再在 vite+ 的哈希键目录下创建第二个分叉的全局安装。解决 #11489。
   [Link](https://github.com/earendil-works/pi/pull/11491)

6. **#11434 — fix(ai)：使用 OpenCode 协议专用端点** `[CLOSED]`
   将 `opencode` / `opencode-go` 流量从共享推理基础地址切换到文档化的按协议端点（与 models.dev 匹配）。关闭 #11132。
   [Link](https://github.com/earendil-works/pi/pull/11434)

7. **#11486 — fix(coding-agent)：写入失败时保持会话内存与文件一致** `[CLOSED]`
   在磁盘写入失败（ENOSPC/EBUSY/ETIMEDOUT）时，防止内存中的条目被提交为当前叶子节点，避免恢复时出现孤立父节点。补充 #11460。
   [Link](https://github.com/earendil-works/pi/pull/11486)

8. **#11471 — feat(coding-agent)：内联 `ctx.ui.custom()` 组件上报阻塞的程序状态** `[CLOSED]`
   允许自定义对话框选择启用 OSC 7501 阻塞状态（例如审批提示），这样终端在等待用户时不再显示 `working`。关闭 #11428。
   [Link](https://github.com/earendil-works/pi/pull/11471)

9. **#11482 — fix(coding-agent)：将思考级别传递给分支摘要** `[CLOSED]`
   `/tree` → Summarize 忽略了会话思考级别；现在 OpenAI Responses/Azure 推理模型会包含推理内容。
   [Link](https://github.com/earendil-works/pi/pull/11482)

10. **#11487 — fix(tui)：显示数学超出内容宽度时回退到原始 LaTeX** `[CLOSED]`
    修复多行数学渲染错误：此前只有溢出的行会换行，后续行会错位。这是更广泛的 TUI 正确性推进的一部分，同时还包括 #11422（Kitty F 键解码）和 #11477（滚动条性能）。
    [Link](https://github.com/earendil-works/pi/pull/11487)

_其他值得关注：_ #11348（所有滚动列表支持 page-key，仍为开放状态）和 #10528（Nix 打包重构，进行中）。

## 5. 热门讨论

**展示与分享**
- **#11488 — AY-PI：按提示词分层路由与工具作用域（虚拟模型 + Jev 分类器）** · 1 条评论 · 👍1
  一个展示 Pi 虚拟模型和分类器 API 的扩展：将每个提示分类为 CHAT/SUGGEST/REVIEW/PLAN/CODE，并附加 low/medium/high 层级，然后限定工具作用域，使只有 CODE 保留 `edit`/`write`/`bash`。
  [Link](https://github.com/earendil-works/pi/discussions/11488)
- **#11476 — 面向编码 agent 的 Cairn 风格共享看板（为 omp 构建）** · 👍1
  将问题求解建模为共享看板上的状态空间搜索，agent 通过事实、意图和提示进行协作；据报告在 Tencent Cloud AI Penetration Testing Challenge 中获得第三名。
  [Link](https://github.com/earendil-works/pi/discussions/11476)

## 6. 功能请求趋势

- **压缩与上下文经济学** — 请求集中在让压缩更便宜、更智能：保留前缀的摘要（#11500 / PR #11499）、按模型暴露多个上下文窗口变体（#10893），以及从上下文上限卡死中恢复（#9409）。
- **持久任务编排** — 持续出现一批 `pi-durable` 请求：从最后看到的 ID 恢复 `watchEvents`（#11327）、让扩展按轮次启动任务（#11141），以及让任务占用会话使其保持忙碌（#11332）。
- **提供商协议保真度** — 多项请求要求匹配各提供商文档化端点和错误语义：OpenCode 协议端点（#11132）、LiteLLM 溢出检测（#11494）、OpenRouter 推理预算（#11496），以及命名空间正确的 OpenAI Responses 重放（#11415）。
- **TUI 交互质量** — 软换行的选择/复制语义（#8019）、在所有位置遵循可配置 page-key（#11348）、Kitty 功能键解码（#11422），以及内联自定义对话框上报阻塞状态（#11428）。
- **安装/更新正确性** — 检测非 npm 全局安装（vite+，#11489），并在 `pi install` 期间抑制 npm `audit`/`fund` 噪音（#11469）。
- **扩展沙箱/权限清晰度** — 即使有显式 `AbortSignal`，父级取消是否始终通过 `ctx.executeTool()` 传播（#11484）。

## 7. 开发者痛点

- **无法恢复的会话。** 反复出现的主题是状态完整性：在上下文上限处卡死（#9409）、分支被截断后恢复时丢弃内置工具（#11460）、写入失败时内存中的会话状态与磁盘不一致（#11486），以及多个 opener 下 `SqliteStorage` ID 冲突（#11497）。开发者会丢失工作，或被迫重启会话。
- **提供商特定脆弱性。** Anthropic SSE chunk 边界（#10390）、扩展重载后无法解析的工具引用（#11376）、Bedrock 的 16 个可选参数上限（#11416），以及 OpenAI Business 认证策略拒绝（#10953），都表现为远离根因的不透明失败。
- **缓存与成本意外。** Codex 空闲后完全缓存未命中（#11493）和完整 prefill 的压缩（#11500）意味着用户为恢复或长时间运行的会话支付远超预期的成本。
- **压缩/并发 bug。** 并发压缩共享单个 `AbortController`（#7738）以及主机重启后 durable 生成无限循环（#11139）表明关键路径上的重入和生命周期防护仍然薄弱。
- **安装/更新歧义。** 误判安装方式（#11489）会静默产生重复全局安装，而 npm 自身的安全公告输出（#11469）增加了用户无法安全处理的噪音。
- **环境/daemon 挂起。** `pi-env` 在线程启动失败时 panic（#11433），使 exec 没有回复且取消机制损坏——一类低频但难以诊断的故障。

---

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区简报 — 2026-10-28

## 1. 今日亮点

发布节奏依然紧凑：`v0.26.2` 发布，对 Managed Agent Web Shell 控件带来了破坏性变更；随后 `v0.26.3-preview.0` 新增了可选的记忆提取节奏，并修复了 XML 工具调用恢复问题。与此同时，追踪器中记录了两个 P1 级正确性 bug——一个是因 thinking 部分缺少签名而导致的持续性 Anthropic 400 错误，另一个是 shell 工具中 `sed` 模拟行为不一致。CI/发布可靠性问题继续占据讨论量榜首：Managed Agent 门禁问题已有 68 条评论，双路径架构提案则有 72 条。

---

## 2. 发布

**[v0.26.3-preview.0](https://github.com/QwenLM/qwen-code/releases)**
- `feat(memory)`：在空操作运行后可选触发的提取节奏（[#13571](https://github.com/QwenLM/qwen-code/pull/13571)）
- `fix(core)`：恢复带引号候选的外层 XML 调用——延续了 [#13787](https://github.com/QwenLM/qwen-code/issues/13787) 中跟踪的 XML 工具调用恢复工作

**[v0.26.2](https://github.com/QwenLM/qwen-code/releases)**
- **破坏性变更：** `fix(managed-agent)!` 简化了 Web Shell 控件并停用了 cwd 提交（[#15616](https://github.com/QwenLM/qwen-code/pull/15616)）——依赖 cwd 提交语义的调用方必须迁移。

**[v0.26.2-preview.1](https://github.com/QwenLM/qwen-code/releases)**
- `feat(managed)`：会话权限规则与 Auto 审批（[#15448](https://github.com/QwenLM/qwen-code/pull/15448)）
- `fix(cli)`：加固 serve token 解析

**[sdk-typescript-v0.1.24](https://github.com/QwenLM/qwen-code/releases)**
- 捆绑 CLI `0.26.2`（与 SDK 从同一分支/ref 构建）。注意发布说明中还引用了一个捆绑的 `0.26.1`，暗示这可能是一次被跳过或重新编号的裁剪。

**发布流程说明：** `v0.26.2-preview.0` 发布失败（[#15672](https://github.com/QwenLM/qwen-code/issues/15672)，已关闭），修复正在 [PR #15730](https://github.com/QwenLM/qwen-code/pull/15730) 中审核。

---

## 3. 热点 Issue

1. **[#15742](https://github.com/QwenLM/qwen-code/issues/15742) — P1：当持久化的 thinking 部分丢失签名后，原生 Anthropic 会话永久返回 400。** 单个缺少 `thoughtSignature` 的 assistant 轮次就会让整个会话陷入不可恢复的 `invalid_request_error`；完全没有自愈路径。对任何使用原生 Anthropic 端点的用户而言都是高严重度问题。

2. **[#15675](https://github.com/QwenLM/qwen-code/issues/15675) — P1：`sed` 编辑模拟与真实 `sed` 产生分歧。** 进程内模拟遗漏了 `s` 和 `u` 标志，导致 `.` 无法匹配 `\r` 和完整 emoji。这是 shell 工具快速路径的正确性 bug，而不仅仅是表面上的不一致。

3. **[#12380](https://github.com/QwenLM/qwen-code/issues/12380) — proposal(serve)：Managed Agent 双路径架构与分阶段交付。** 本统计窗口内流量最高的 issue（72 条评论），截至 2026-10-24 带有已验证的 Stage F 交付状态。实质上这是 managed-agent 路线图的设计脊梁。

4. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724) — 主 CI 失败：Managed Agent 门禁，未识别出测试。** 68 条评论和 `autofix/skip`，自 2026-10-19 起每日更新。持续出现的“未识别出测试”失败正在侵蚀该门禁作为信号的可信度。

5. **[#15686](https://github.com/QwenLM/qwen-code/issues/15686) — P2：Active Goal 阻塞普通用户输入。** Enter 引导的消息和粘贴的文本会排队直到 Goal 离开活跃状态，这与文档所述的引导行为相矛盾。已有六条评论；这是一个 UX/状态机冲突，而非简单的 bug。

6. **[#15741](https://github.com/QwenLM/qwen-code/issues/15741) — P2：MCP 工具名重复导致整个服务器被丢弃。** 一次冲突就会静默丢弃该服务器上的所有工具，并报告误导性的“无工具”错误。在 0.26.2 上用 Unity MCP 服务器复现。

7. **[#15738](https://github.com/QwenLM/qwen-code/issues/15738) — P2：ACP 模式下 team-mode 消息永远无法到达 leader。** 使用 `qwen serve --web` 和 `experimental.agentTeam` 时，没有任何队友消息被投递，团队随短生命周期的 `--acp` 子进程一起消亡。阻塞了 Web Shell 中的多智能体场景。

8. **[#15690](https://github.com/QwenLM/qwen-code/issues/15690) — P2：Homebrew 0.26.1 无法重写 `libopentui.dylib` 的安装名。** `@opentui/core-darwin-arm64` 缺少 `-headerpad_max_install_names`，导致 Apple Silicon 上 `brew upgrade` 报出链接错误。这是影响 macOS 用户的打包层损坏。

9. **[#15645](https://github.com/QwenLM/qwen-code/issues/15645) — P2：磁盘上创建技能后守护进程技能目录保持陈旧。** 运行时技能端点能看到新项目技能，但配置端点看不到——这种分裂状态会令管理 UI 困惑。

10. **[#15697](https://github.com/QwenLM/qwen-code/issues/15697) — P2：web-shell 历史视口冒烟测试耗尽 120 秒预算。** 同一 spec 和断言在两个不涉及 `packages/web-shell/` 的无关 PR 中均失败。具体的 flake 数据让这个问题变得可操作。

*另需注意：* [#13024](https://github.com/QwenLM/qwen-code/issues/13024)（IO Intelligence provider 预设）是集合中唯一获得有意义正面反应的问题，有 **10 👍**；[#13078](https://github.com/QwenLM/qwen-code/issues/13078)（依赖 CVE 审计失败，33 条评论）仍然开放，需要分诊；[#15499](https://github.com/QwenLM/qwen-code/issues/15499) 跟踪 managed-runtime `v0.26.1` 预检停止问题。

---

## 4. 关键 PR 进展

1. **[#15745](https://github.com/QwenLM/qwen-code/pull/15745) — feat(managed)：主会话的独立 worktree。** Managed 主会话可以选择使用按会话独立的 Git worktree；准入时会冻结一个本地提交并在 Runtime 启动前记录工作目录。这是实现并行 managed 工作的赋能原语。

2. **[#15730](https://github.com/QwenLM/qwen-code/pull/15730) — fix(release)：发布检出使用 job token。** 直接针对发布阶段的发布失败，仅在分支推送和 GitHub release 步骤中保留 PAT。

3. **[#15746](https://github.com/QwenLM/qwen-code/pull/15746) — fix(verify-pr)：将报告绑定到其轮次快照。** 防止另一个产物目录中更新的验证轮次追溯性地改变更早报告的评估方式。这是审查完整性修复。

4. **[#15388](https://github.com/QwenLM/qwen-code/pull/15388) — feat(ci)：以轮次预算限制 autofix 接管。** `@qwen-code /takeover for N`（1–99）记录一个窗口范围内的轮次预算。应对“autofix 永不收敛”的失败模式。

5. **[#15435](https://github.com/QwenLM/qwen-code/pull/15435) — feat(web-shell)：在展开卡片中显示工具参数和结果。** 展开后的工具卡片现在会为缺少内置详情视图的工具渲染调用参数和完整结果；JSON 会美化打印。这是转录中意义重大的透明度改进。

6. **[#14613](https://github.com/QwenLM/qwen-code/pull/14613) — feat(cli)：通过用户作用域设置和环境变量对启用 LSP。** 无需 CLI 标志即可访问实验性的 LSP 代码智能服务；禁用开关优先于启用开关。

7. **[#15681](https://github.com/QwenLM/qwen-code/pull/15681) — fix(core)：不将 fd 复制（`2>&1`）视为文件写入。** 修复了对这一极为常见的 shell 惯用法误报权限提示的问题。

8. **[#14670](https://github.com/QwenLM/qwen-code/pull/14670) — fix(core)：按 bash 的分隔符而非 JS 空白字符拆分 shell 命令。** `/\s/` 会匹配 bash 视为普通字词字符的字符（`\r`、`\v`、`\f`、`\u00a0`），可能错误拆分用于权限分析的命令。

9. **[#14677](https://github.com/QwenLM/qwen-code/pull/14677) — fix(core)：将重新赋值的 `HOME` 视为动态，用于 `cd` 和 `~`。** 堵住了一个权限分析漏洞：`HOME=/x; cd` 会被按照错误的路径认证。

10. **[#15358](https://github.com/QwenLM/qwen-code/pull/15358) — fix(cli)：在 JSON 结果中报告最大会话轮次，而非重放上一个答案。** `BaseJsonOutputAdapter` 现在会锁存 `MaxSessionTurns` 并发出真实错误，而非虚假成功。对解析 JSON 输出的 CI 消费者至关重要。

*相关进行中：* sdk-java 锁作用域系列（[#15274](https://github.com/QwenLM/qwen-code/pull/15274)、[#15275](https://github.com/QwenLM/qwen-code/pull/15275)、[#15286](https://github.com/QwenLM/qwen-code/pull/15286)）收敛于同一设计——为每个 job 预置局部 Maven 仓库，仅在缓存回写时持有主机锁。合并它们将减少审查负担。

---

## 5. 热点讨论

本统计窗口未提供 GitHub Discussions 数据，故本节省略。

---

## 6. 功能请求趋势

- **Provider 预设扩展与兼容性。** [#13024](https://github.com/QwenLM/qwen-code/issues/13024)（io.net 预设，10 👍）和 [#12072](https://github.com/QwenLM/qwen-code/issues/12072)（`X-OpenRouter-Title` vs `X-Title`）指向对一等公民预设以及正确厂商特定请求头的持续需求。
- **Managed Agent / 多智能体成熟度。** 双路径架构（[#12380](https://github.com/QwenLM/qwen-code/issues/12380)）、独立会话 worktree（[#15745](https://github.com/QwenLM/qwen-code/pull/15745)）、会话权限规则（[#15448](https://github.com/QwenLM/qwen-code/pull/15448)），以及 ACP 中的 team-mode 投递（[#15738](https://github.com/QwenLM/qwen-code/issues/15738)）。这是最大且最连贯的功能集群。
- **Web Shell 交互设计。** 从固定侧栏中移出归档会话（[#15549](https://github.com/QwenLM/qwen-code/issues/15549)）、可配置的简单输入模式（[#15647](https://github.com/QwenLM/qwen-code/issues/15647)）、输入框打磨（[#15691](https://github.com/QwenLM/qwen-code/issues/15691)），以及工具卡片详情（[#15435](https://github.com/QwenLM/qwen-code/pull/15435)）。
- **上下文与 token 管理。** 上下文压力下的动态工具输出截断（[#2566](https://github.com/QwenLM/qwen-code/issues/2566)）和记忆提取节奏（#13571）都在强调：应当有意识地花费上下文，而非被动应对。
- **Shell 工具保真度与权限正确性。** `sed` 模拟一致性（[#15675](https://github.com/QwenLM/qwen-code/issues/15675)）、fd 复制（[#15681](https://github.com/QwenLM/qwen-code/pull/15681)）、bash 字词拆分（[#14670](https://github.com/QwenLM/qwen-code/pull/14670)），以及 `HOME` 重新赋值（[#14677](https://github.com/QwenLM/qwen-code/pull/14677)）构成清晰的子主题：要么精确模拟 bash，要么干脆不要模拟。
- **发布/CI 自托管。** 在空闲 ARM64 池上预置仅用于发布的宿主机（[#15461](https://github.com/QwenLM/qwen-code/issues/15461)）和按 SHA 的 E2E 验证（[#15052](https://github.com/QwenLM/qwen-code/issues/15052)）。

---

## 7. 开发者痛点

- **CI 信号嘈杂且解读成本高。** 本统计窗口至少有五条并行的 CI 相关线程：[#14724](https://github.com/QwenLM/qwen-code/issues/14724)（68 条评论，未识别出测试）、[#14796](https://github.com/QwenLM/qwen-code/issues/14796)（`Install dependencies` 在任何测试前就失败）、[#15697](https://github.com/QwenLM/qwen-code/issues/15697)（超时 flake）、[#15677](https://github.com/QwenLM/qwen-code/issues/15677)（`darwin-x64` 上 6 个中 5 个失败），以及 [#15052](https://github.com/QwenLM/qwen-code/issues/15052)（替换待处理运行）。贡献者正把审查带宽花在基础设施分诊上，而非代码上。
- **发布流水线止步于 publish。** [#15499](https://github.com/QwenLM/qwen-code/issues/15499)（不可变发布预检）、[#15672](https://github.com/QwenLM/qwen-code/issues/15672)（preview 发布失败），以及 [#15461](https://github.com/QwenLM/qwen-code/issues/15461)（publish 与 GitHub 托管容量竞争）描述了一个系统性瓶颈：`publish` 是最后一个没有专用容量的 job。
- **Shell 模拟的边界情况既导致误批也导致误提示。** `sed` 正则标志缺失、`/\s/` 与 bash 分隔符之争、`HOME` 重新赋值，以及 `2>&1` 被当作“写入”，全部源于在 JavaScript 中近似实现 shell。每个修复都很小；但这整类 bug 可不小。
- **持久且不可恢复的会话状态故障。** [#15742](https://github.com/QwenLM/qwen-code/issues/15742)（缺失 `thoughtSignature` → 永久 400）和 [#15645](https://github.com/QwenLM/qwen-code/issues/15645)（陈旧的技能目录）都会让用户陷入一个不手动干预就无法修复的会话。
- **“延迟审查发现”正作为一种积压模式累积。** [#13268](https://github.com/QwenLM/qwen-code/issues/13268)、[#11056](https://github.com/QwenLM/qwen-code/issues/11056)、[#15674](https://github.com/QwenLM/qwen-code/issues/15674)、[#15707](https://github.com/QwenLM/qwen-code/issues/15707)，以及 [#15527](https://github.com/QwenLM/qwen-code/issues/15527) 都是因 PR 超出审查轮次或规模上限而创建的账目。1500 行新增的巡查规则有效，但延迟工作无人负责。
- **macOS 分发仍然脆弱。** [#15690](https://github.com/QwenLM/qwen-code/issues/15690)（`@opentui/core-darwin-arm64` 缺少 `-headerpad_max_install_names`）是一处依赖归属缺口，会破坏所有 Apple Silicon 用户在 0.26.1 上的 Homebrew 升级。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*