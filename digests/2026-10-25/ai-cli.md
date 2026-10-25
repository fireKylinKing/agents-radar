# AI CLI 工具社区动态日报 2026-10-25

> 生成时间: 2026-10-25 00:29 UTC | 覆盖工具: 7 个

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

# 跨工具对比：AI CLI 开发者工具 — 2026-10-25

## 1. 生态概览

2026-10-25 的各份摘要显示，市场正从能力演示转向运营信任。Claude Code 正处于明显的维护低谷，套餐层级需求高度一致，但沙箱数据丢失报告仍未解决；Codex 则集中于 Windows/app-server 稳定性与 MCP 资源隔离。Gemini CLI、OpenCode、Pi、Copilot CLI 和 Qwen Code 都在围绕智能体编排、资源生命周期、配额/认证正确性和平台一致性进行迭代。在各社区中，核心问题已不再是“智能体能不能写代码？”，而是“它能否可靠完成、安全恢复并如实汇报？”企业采购信号——Claude Team 20x、Copilot 计费事件、Qwen CI/运行时自动化——正日益显著。

---

## 2. 活动对比

下表计数为摘要所报告的 24 小时窗口内数据，并非完整仓库总量。“Notable/listed（值得注意/已列出）”指摘要中明确剖析的条目。`n/d` = 摘要未提供计数。`N/A` 仅用于已禁用的社区渠道；本数据集中这七款工具均未在上游禁用 Issues/PRs。

| 工具 | Issues 数 | PR 数 | Discussions 数 | 发布状态 |
|---|---:|---:|---:|---|
| **Claude Code** | 50 条更新；10 条已剖析 | 2 条更新；0 条合并 | n/d | 无发布 |
| **OpenAI Codex** | 10 条值得注意；总数 n/r | 0 条更新 | 10 条已剖析（8 条展示分享，2 条问答） | 无发布 |
| **Gemini CLI** | 14 条值得注意（10 条热门 + 4 条关注） | 14 条列出（10 条关键 + 4 条其他） | n/d | 1 个每日构建（`v0.67.0-nightly.20261024`） |
| **GitHub Copilot CLI** | 20 条更新 | 0 条更新 | n/d | 2 个补丁（`v1.0.104-2`、`v1.0.104-3`） |
| **OpenCode** | 13 条值得注意；引用 ≥12 条配额报告（有重叠） | 16 条列出（10 条关键 + 6 条其他） | n/d | 无发布 |
| **Pi** | 12 条值得注意 | 10 条列出 | 2 条已剖析 | 无发布 |
| **Qwen Code** | 13 条值得注意 | 15 条列出（10 条关键 + 5 条其他） | n/d | 2 个发布（`v0.26.1`、`desktop-v0.26.1`） |

**如何解读表格：** Claude 的 Issue 窗口最广，单一 Issue 互动也最强，但已合并 PR 活动为零。Gemini、Qwen、OpenCode 和 Pi 的代码审查吞吐更密集。Codex 和 Copilot 的“0 条 PR 更新”数字属于快照限制或 PR 可见性偏低，并不证明没有工作在进行。

---

## 3. 共同功能方向

七项跨工具需求在多个社区中反复出现：

1. **真实的智能体状态与终止语义**
   - **Gemini：** 在 `MAX_TURNS` 下误报 `GOAL` 成功，以及浏览器失败（#22323、#21983、#29779）。
   - **Codex：** 有证据支撑的完成报告（#36718）。
   - **Copilot：** 子智能体工具静默丢失与上下文轮转损坏（#5243、#5205）。
   - **OpenCode：** 父智能体收不到后续子智能体回合（#58825）。
   - **Qwen：** 因 429 被误分类而跳过回退链（#13895）。
   - **需求：** 明确的成功/失败原因、证据映射、父子结果传播，以及可审计的完成声明。

2. **后台/并行任务生命周期与资源隔离**
   - **Copilot：** 后台 shell 完成事件破坏回合（#4946）、inotify 耗尽（#5242）、上下文轮转失败（#5205）。
   - **Codex：** 每线程 MCP 进程爆炸（#53285）、`git diff` 扇出（#43019）、临时线程泄漏 MCP 栈（#39783）。
   - **OpenCode：** 有界关闭（#58817）、后台子智能体汇报（#58825）。
   - **Pi：** 流式输出期间 TUI 占满全部核心（#6665、#7730）。
   - **需求：** 有界进程并发、清理/回收、超时、背压和优雅关闭。

3. **配额、计费与认证一致性**
   - **Claude：** Team 计划需要等同 Max-20x 的层级（#47509）。
   - **Codex：** 使用上限重置未跨客户端识别（#51043）。
   - **Copilot：** Assisted Permissions PRU 耗尽事件（#4802）。
   - **OpenCode：** 免费层级配额永不重置；86,400 秒循环（#58830、#58831、#58827）。
   - **Qwen：** 推理 token 在成本显示中被重复计算（#15444）。
   - **Pi：** 使用上限中断/恢复工作流（#11306）。
   - **需求：** 可预测的权益状态、跨客户端同步、准确的 token 核算，以及透明的重置语义。

4. **权限/沙箱信任与同意**
   - **Claude：** 破坏性沙箱文件系统操作（#93287、#93408）以及不一致的自动模式分类器（#101327）。
   - **Gemini：** 沙箱化优先于限制（#19873）、破坏性命令护栏（#22672）。
   - **OpenCode：** 权限“询问”静默允许编辑/写入（#58527）。
   - **Copilot：** ACP OAuth 刷新与浏览器重定向冲突（#5229）。
   - **Codex：** 沙箱/权限状态不透明。
   - **需求：** 确定性审批、清晰可读的沙箱边界、工具扩展时重新征得同意、安全默认值，以及试运行/恢复路径。

5. **提供商/模型路由与互操作性**
   - **Pi：** 大规模提供商扩展 PR 链（#11368–#11372）、Gemini 思维签名（#10157）、Bedrock 图像载荷（#8643）。
   - **OpenCode：** v1→v2 配置不匹配（#58533）、过期模型污染目录（#57931）。
   - **Qwen：** 严格的 OpenAI 兼容工具 schema（#12222）、429 回退分类（#13895）。
   - **Gemini：** 作用域内工具过多导致 400（#24246）。
   - **Codex：** 模型路由不透明与静默模型切换。
   - **需求：** 声明式模型目录、能力感知路由、优雅回退，以及适配器级一致性测试。

6. **上下文、记忆连续性与压缩**
   - **OpenCode：** 手动压缩可能永久损坏会话（#58724）。
   - **Qwen：** 会话记录快照索引上限导致会话无法打开（#13113）。
   - **Codex：** AGENTS.md 传播检查与跨新会话的决策图。
   - **Pi：** 扩展排序丢失 Anthropic `system` 字段（#11157）、嵌入式会话记忆保留（#10642）。
   - **Gemini：** 持久化基于文件的任务状态（#18836）。
   - **需求：** 持久会话存储、可恢复压缩、上下文交接，以及跨项目记忆隔离。

7. **终端/桌面/无头模式一致性**
   - **Claude：** 桌面端提示建议、插件渲染钩子、Remote Control 持久化。
   - **Codex：** Windows VS Code 启动、iPad 远程会话、Windows MCP 进程计数。
   - **Gemini：** Wayland 浏览器子智能体失败、OSC 7501 终端状态。
   - **Copilot：** Windows 代理/STT、ACP 一致性、本地 Web UI 请求。
   - **OpenCode：** Windows `%temp%` 重定位、跨卷二进制文件保留。
   - **Pi：** Ctrl+Shift+C 提示丢失、Windows CSI 泄漏、Alacritty 输入法。
   - **Qwen：** Web Shell i18n 回退阻挡非英语浏览器。
   - **需求：** 跨平台 CI 矩阵、无头模式一致性、可访问的 UI 回退，以及终端原生状态报告。

---

## 4. 差异化分析

| 工具 | 功能重点 | 目标用户 | 技术路线 |
|---|---|---|---|
| **Claude Code** | Team 计划分层、桌面/TUI 打磨、沙箱安全、插件渲染、Remote Control | 团队、企业采购、高级用户 | 沙箱化 Bash/桌面端、权限分类器、插件 UI 钩子、Remote Control、Max 层级用量模型 |
| **OpenAI Codex** | Windows 桌面端/app-server 稳定性、IDE 扩展、MCP 生命周期、远程/移动端、AGENTS.md | Windows/IDE 开发者、通过 ChatGPT 认证的用户、企业应用用户 | App-server + VS Code 扩展、MCP、线程/历史 API、有证据支撑的完成方向 |
| **Gemini CLI** | 子智能体可靠性、ACP/编辑器集成、终端原生状态、AST 感知上下文、沙箱化 | Google 生态中的终端/编辑器开发者 | CLI + ACP、子智能体编排、OSC 7501、AST/工具作用域实验、零依赖沙箱提案 |
| **GitHub Copilot CLI** | Autopilot 异步正确性、SDK/ACP/无头模式、插件技能、企业代理/OAuth | GitHub 企业用户、CI/容器用户、Copilot 订阅者 | Autopilot、MCP、ACP、插件命名空间、托管模型 `fusion`、补丁发布节奏 |
| **OpenCode** | 免费层级/桌面端、v2 迁移、提供商互操作、会话完整性 | 个人开发者、免费层级用户、桌面端/CLI 用户 | 客户端/服务器会话传输、模型目录 ETag、子智能体、主题、桌面端/CLI 融合 |
| **Pi** | 提供商广度、扩展 API、TUI 性能、嵌入式 SDK、长会话 | 高级用户、扩展作者、嵌入式/多会话宿主 | 声明式提供商定义、models.dev 目录、TUI 性能工作、扩展生命周期 API |
| **Qwen Code** | CI/PR 自动化、运行时代理/Kubernetes、桌面端/守护进程、成本核算 | Qwen 生态、自托管/企业 CI 团队 | CLI + 守护进程/桌面端、运行时代理、托管智能体、严格的 OpenAI 兼容提供商处理 |

战略分野在于**平台自有的企业级栈**——Claude Code、Codex、Copilot CLI 和 Gemini CLI——与**生态/扩展性驱动的工具**——OpenCode、Pi 和 Qwen Code。前者在集成深度、管理控制和安全方面竞争；后者则在提供商覆盖、定制化和快速迭代方面竞争。

---

## 5. 社区动能与成熟度

- **最高原始互动量：** Claude Code 和 Pi。Claude 的 #47509 达到 187 👍 / 67 条评论，其沙箱数据丢失问题簇属于安全关键问题。Pi 的 #4945 有 88 条评论 / 35 👍，表明长会话可靠性存在深层痛点。
- **强劲的 Issue 接收活动：** Copilot CLI（20 个 Issue 更新）、Claude（50 个 Issue 窗口）、OpenCode（配额危机，≥12 份报告）和 Codex（Windows/app-server 问题簇）。这些社区在积极提交，但并非所有社区都获得成比例的合并吞吐。
- **可见迭代最快：** Gemini CLI（每日构建 + 14 个列出的 PR）、Qwen Code（2 个发布 + 15 个列出的 PR）、OpenCode（16 个列出的 PR）和 Pi（堆叠的提供商扩展 PR 链）。这些工具显示维护者正在积极落地正确性、性能和提供商相关工作。
- **成熟度 vs. 速度：** Claude、Codex、Copilot 和 Gemini 拥有更广的功能面和企业采用，但背负平台债——沙箱信任、Windows 稳定性、无头模式一致性、分诊滞后。OpenCode、Pi 和 Qwen 更年轻、迭代更可见，但流程成熟度落后：OpenCode 的权益事件和 Pi 的贡献门禁关闭显示存在运营缺口。
- **结论：** Claude 拥有最具影响力的社区信号，但集成姿态停滞。Gemini、Qwen、OpenCode 和 Pi 迭代最快。Codex 和 Copilot 依然成熟，但受限于 Windows/无头模式可靠性，以及本快照中可见 PR 吞吐偏低。

---

## 6. 趋势信号

1. **可靠性是新的差异化因素。** 虚假成功报告、无限挂起、轮转损坏和后台任务损坏在 Gemini、Copilot、OpenCode 和 Pi 中均有出现。采购方应评估终止语义、恢复路径和证据汇报——而不仅仅是模型质量。
2. **资源隔离正在向上层迁移。** 每线程 MCP 进程、`git diff` 扇出、inotify 耗尽以及 TUI CPU/RAM 增长表明，进程预算、背压和清理如今已是智能体运行时的核心要求。
3. **计费/认证/配额透明度是一层信任。** 配额重置、PRU 耗尽、token 重复计算和计划层级上限都是采用障碍。企业应要求可审计的用量归因和跨客户端状态一致性。
4. **同意与沙箱可读性比沙箱宣称更重要。** 破坏性文件系统操作、静默编辑/写入权限和非确定性分类器会侵蚀信任。显式审批模型、重新征得同意、试运行和文件系统边界正成为基本门槛。
5. **提供商中立的适配器与回退具有战略意义。** Pi 的提供商扩展、OpenCode 的 v1→v2 迁移痛点、Qwen 的 OpenAI 兼容严格性，以及 Gemini 的工具作用域限制，都指向声明式目录、能力路由和一致性测试。
6. **无头/CI/容器一致性将成为采购标准。** ACP、OAuth 刷新、代理感知、inotify 限制、Windows 行为和非英语 UI 回退不再是边缘情况——它们决定工具能否在企业 CI 和远程环境中工作。
7. **智能体可观测性与评估工具正在兴起。** 有证据支撑的完成、AGENTS.md 验证、子智能体轨迹可见性，以及基于 pytest 的行为检查，都是围绕智能体信任的新工具层的早期信号。
8. **社区运营是领先指标。** stale-bot 自动关闭、CI 容量瓶颈、贡献门禁和发布自动化质量，与实质性问题得到解决的速度直接相关。对决策者而言，PR 吞吐和分诊健康度是实用的支持质量信号。

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — 社区亮点报告
**来源：** github.com/anthropics/skills · **数据截至：** 2026-10-25

> **数据说明：** 提供的数据集中，每个 PR 都报告 `Comments: undefined` 和 `👍: 0`，因此下文的排名采用*关注度代理指标*——触及同一 skill 的 PR 数量、评论数较高的关联 Issue，以及持续的更新活跃度——而非原始评论数。所列 PR 均为 **OPEN**；此快照中没有已合并 PR。

---

## 1. 热门 Skills 排名

| # | Skill | 代表性 PR | 状态 | 关注度信号 |
|---|-------|----------------------|--------|------------------|
| 1 | **skill-creator**（元 skill） | [#1298](https://github.com/anthropics/skills/pull/1298)、[#1681](https://github.com/anthropics/skills/pull/1681)、[#1997](https://github.com/anthropics/skills/pull/1997) | Open | 3 个并发 PR + Issue #202（8 条评论）、#1394（4 条评论） |
| 2 | **mcp-builder** | [#1742](https://github.com/anthropics/skills/pull/1742) | Open | 修复 #1668；关联至 Issue #1390（0/N 评测得分） |
| 3 | **webapp-testing** | [#1980](https://github.com/anthropics/skills/pull/1980)、[#1976](https://github.com/anthropics/skills/pull/1976) | Open | 2 个 PR，其中一个为安全级（CWE-78） |
| 4 | **docx** | [#1792](https://github.com/anthropics/skills/pull/1792)、[#1734](https://github.com/anthropics/skills/pull/1734) | Open | 2 个 PR + Issue #2018（XML 输出无效） |
| 5 | **claude-api** | [#1730](https://github.com/anthropics/skills/pull/1730) | Open | 关联至 Issue #1487（约 156k token 的提前注入） |
| 6 | **pyxel**（复古游戏开发） | [#525](https://github.com/anthropics/skills/pull/525) | Open（草稿阶段） | 运行时间最长的活跃功能 PR —— 3 月 5 日 → 10 月 23 日 |
| 7 | **document-typography** | [#514](https://github.com/anthropics/skills/pull/514) | Open | 生成文档的孤行/寡行控制 |
| 8 | **skill-quality-analyzer / skill-security-analyzer** | [#83](https://github.com/anthropics/skills/pull/83) | Open | 数据集中最早的开放 PR（2025-11-06） |

**详细说明**

- **skill-creator** 是生态中的压力点：三个开放修复分别处理 trigger-eval 漏报与 Windows 子进程处理（[#1298](https://github.com/anthropics/skills/pull/1298)）、独立执行 `package_skill.py`（[#1681](https://github.com/anthropics/skills/pull/1681)，最近更新于 2026-10-24），以及手写 YAML frontmatter 解析会破坏描述（[#1997](https://github.com/anthropics/skills/pull/1997)）。Issue #202 认为该 skill 读起来像开发者文档，而不是可操作的指令集。
- **mcp-builder** [#1742](https://github.com/anthropics/skills/pull/1742) 适配 `mcp>=2.0.0`：其中 `streamablehttp_client` 已重命名，自定义请求头现在通过 `create_mcp_http_client` 路由。结合 Issue #1390（评测框架会伪造工具错误）来看，该 skill 的*测试*路径是社区的主要痛点。
- **webapp-testing** 带有最清晰的安全议题设定：[#1980](https://github.com/anthropics/skills/pull/1980) 从 `with_server.py` 中移除 `shell=True`，以封堵命令注入攻击面；[#1976](https://github.com/anthropics/skills/pull/1976) 修正元素发现中对 `textarea`/`select` 的误报。
- **pyxel** [#525](https://github.com/anthropics/skills/pull/525) 和 **document-typography** [#514](https://github.com/anthropics/skills/pull/514) 是最强的*净新增* skill 提案：前者为复古游戏提供无头帧检查，后者为生成文档提供排版 QC（孤行换行、寡行段落、编号对齐）。

---

## 2. 社区需求趋势

从 Issue 中提炼，需求集中在五个方向——值得注意的是，多数关乎*信任与可靠性*，而非新能力：

1. **命名空间完整性与供应链信任**——Issue [#492](https://github.com/anthropics/skills/issues/492)（46 条评论，数据集中讨论最多的 Issue）警告称，社区 skill 以 `anthropic/` 命名空间发布会导致冒充与权限提升。eval-viewer XSS 报告（[#1394](https://github.com/anthropics/skills/issues/1394)）进一步印证了这一点。
2. **团队/组织级 skill 分发**——[#228](https://github.com/anthropics/skills/issues/228)（17 条评论）希望提供组织级 skill 库和分享链接，而不是手动传递 `.skill` 文件；[#189](https://github.com/anthropics/skills/issues/189) 报告 `document-skills` 与 `example-skills` 插件之间存在重复内容。
3. **可靠的 skill 触发与评测**——[#556](https://github.com/anthropics/skills/issues/556)（13 条评论）：`run_eval.py` 在所有查询中的触发率均为 0%，导致 skill 作者无法验证自己的工作。
4. **上下文/token 经济性**——[#1487](https://github.com/anthropics/skills/issues/1487)：`claude-api` 在单次工具调用中提前注入约 156k token；[#202](https://github.com/anthropics/skills/issues/202)：`skill-creator` 的说明文字过于冗长，不利于 token 效率。
5. **面向质量与治理的元 skill**——包括三闸门推理流水线（[#1385](https://github.com/anthropics/skills/issues/1385)）、agent 治理安全模式（[#412](https://github.com/anthropics/skills/issues/412)）以及符号化紧凑记忆表示法（[#1329](https://github.com/anthropics/skills/issues/1329)）的提案。

---

## 3. 高潜力待处理 Skills

近期有活动、且 diff 范围窄、易于评审的 PR——有望近期合入的候选：

| PR | Skill | 为何有望合入 |
|----|-------|-------------------------|
| [#1681](https://github.com/anthropics/skills/pull/1681) | skill-creator | 更新于 2026-10-24（数据集中最新）；修复可复现的 `ModuleNotFoundError` |
| [#1980](https://github.com/anthropics/skills/pull/1980) | webapp-testing | 安全修复（CWE-78），变更面自包含 |
| [#1997](https://github.com/anthropics/skills/pull/1997) | skill-creator | 修正 YAML 标量解析；替换手动解析器 |
| [#1977](https://github.com/anthropics/skills/pull/1977) | algorithmic-art | 单函数修复（`wrapAround()` 取模），解决 #1897 |
| [#1976](https://github.com/anthropics/skills/pull/1976) | webapp-testing | 修复标签名报告，解决 #1891 |
| [#1792](https://github.com/anthropics/skills/pull/1792) | docx | LibreOffice 超时现在会报告为错误 + 输出验证 |
| [#1730](https://github.com/anthropics/skills/pull/1730) | claude-api | 失效 URL 替换，HTTP-200 已验证 |

周期更长但高价值：[#1742](https://github.com/anthropics/skills/pull/1742)（mcp-builder，MCP v2 兼容）和 [#538](https://github.com/anthropics/skills/pull/538)（pdf，大小写敏感的引用修复）。

---

## 4. Skills 生态洞察

**社区最集中的需求并非新 Skills，而是 skill 工具链本身的信任、正确性与可度量性**——命名空间完整性、可靠的触发/评测，以及 token 高效的 `skill-creator`/`mcp-builder` 管线主导了讨论；而净新增 skill（pyxel、ODT、document-typography、md2video-audio）仍是次要且进展较慢的支流。

---

# Claude Code 社区摘要 — 2026-10-25

## 1. 今日亮点

过去 24 小时内没有发布新版本，PR 队列实际上处于休眠状态（2 个已关闭 PR，0 个值得强调的合并）。社区注意力仍集中在 **套餐分层** —— Issue #47509 的 👍 已超过 187，并有 67 条评论要求提供与 Max-20x-equivalent 对等的 Team 席位 —— 以及一对 **数据丢失事故报告**（#93287、#93408），其中沙箱化的 Bash/Desktop 会话据称删除了 macOS `Applications` 和主目录内容。与此同时，Desktop Code 标签页继续积累围绕提示建议、插件渲染钩子和 Remote Control 会话持久化的小而高摩擦的回归。

## 2. 发布

*省略——过去 24 小时内未发布任何版本。*

## 3. 热点 Issue

1. **[#47509](https://github.com/anthropics/claude-code/issues/47509) — 团队计划需要与 Max 20x 对等的档位**（OPEN，67 条评论，187 👍）
   本时间窗口内互动量最高的条目。团队在 agentic 工作流中统一采用 Claude Code，却撞上 6.25x Premium 上限，而个人用户可以运行 Max 20x。这释放出真实的企业采购摩擦信号——其 👍 数约为这里其他任何条目的 3 倍。

2. **[#93287](https://github.com/anthropics/claude-code/issues/93287) — Claude Code 删除了整个 macOS Applications 目录**（OPEN，high-priority，data-loss，sandbox）
   声称所有已安装应用都被移除。语气激烈，但元数据（high-priority + data-loss + area:sandbox）才是令人警觉之处。这是多起沙箱提权报告中的典型代表。

3. **[#93408](https://github.com/anthropics/claude-code/issues/93408) — Desktop 删除了 macOS 主文件夹内容，包括钥匙串**（OPEN，high-priority，data-loss）
   与 #93287 相伴的报告，牵涉 Bash + Desktop 的沙箱边界。钥匙串丢失使问题从烦扰升级为安全事件；两者约 6 周后仍处于开启状态。

4. **[#103335](https://github.com/anthropics/claude-code/issues/103335) — Desktop 提示建议（Tab 接受）不再出现**（OPEN，has repro，11 条评论）
   在 2026-10-13 更新前后出现回归，且重启和全新会话后仍然存在。对重度 Desktop 用户而言是无声的生产力损失；可复现通常能缩短修复时间。

5. **[#102988](https://github.com/anthropics/claude-code/issues/102988) — 按状态为侧边栏会话行设置样式**（OPEN，enhancement，7 条评论）
   请求用整行颜色编码（running / needs-input / unread / cache-expired）取代单一状态圆点。描述清晰；反映出并行 agent 会话已变得多么拥挤。

6. **[#101327](https://github.com/anthropics/claude-code/issues/101327) — 自动模式权限分类器阻止常规的预先批准写入**（OPEN，area:permissions）
   分类器类别在多次运行间发生变化，甚至阻止自助权限编辑。投诉点在于不一致——而非阻止本身；这让自动模式对脚本化工作流不可信。

7. **[#102480](https://github.com/anthropics/claude-code/issues/102480) — 下一步框隐藏插件 `AbovePrompt` 渲染**（OPEN，has repro，Windows）
   一个 Desktop UI 层级 bug，破坏了文档中记录的插件扩展点。目前影响范围小，但削弱了对 modding API 渲染契约的信心。

8. **[#83528](https://github.com/anthropics/claude-code/issues/83528) — 无法在不产生副作用的情况下清空提示缓冲区**（OPEN，area:tui）
   细致的审查表明，现有每个快捷键要么只部分清空，要么以破坏性方式触发 `/clear`。典型的 TUI 小痛点，被冗长的多行 agentic 提示放大。

9. **[#103959](https://github.com/anthropics/claude-code/issues/103959) — 插件窗格宽度无法超过终端的 45%**（OPEN，Linux，3 👍）
   停靠式插件布局的回归，首次报告于 2026-10-21。范围窄但零评论反对——用户一致认为这是回归，而非设计选择。

10. **[#104700](https://github.com/anthropics/claude-code/issues/104700) — Remote Control 会话在本地触碰前会一直离线**（OPEN，duplicate，Windows）
    即使在机器保持唤醒的情况下，会话也会在睡眠/恢复后从 Remote Control 掉线。被标记为 duplicate，说明这是一类已知的状态持久化 bug，而非孤立案例。

## 4. 关键 PR 进展

本时间窗口内仅有 **2 个 pull request** 更新，二者均为 **CLOSED** 且均未合并——应将其视为集成停滞的信号，而非功能进展。

1. **[#62597](https://github.com/anthropics/claude-code/pull/62597) — 修复：解决脚本和工作流中的 10 个 bug**（CLOSED）
   强化仓库自动化：将硬编码的 `REPO` 替换为 `GITHUB_REPOSITORY` 环境变量回退，使脚本能在 fork 中工作；为 `schedule`/`workflow_dispatch` 上可能为 null 的 `github.event.repository.name` 添加安全回退；并改进 `edit-issue-labels.sh` 中的错误处理。自 5 月起开启——考虑到大量分类负载（见下文 stale-close 模式）依赖这套工具，其未合并即关闭值得注意。

2. **[#16132](https://github.com/anthropics/claude-code/pull/16132) — 修复：`/install-github-app` 对 dev container 的支持**（CLOSED）
   修复了一个真实的入门阻碍：在 dev container 内完成 `gh` 认证后，`/install-github-app` 仍报告 `gh` 未认证。通过添加 GitHub CLI 仓库以获取最新 `gh` 解决。自 1 月起开启。

> **分析师注：** 在零开启 PR、零发布的情况下，社区 Issue 积压的增长速度快于消化速度——这一模式直接体现在第 7 节。

## 5. 热门讨论

*省略——本数据集未提供 Discussions 数据。*

## 6. 功能请求趋势

- **高级用户套餐分层（B2B）。** #47509 占主导。这一诉求是结构性的：按席位倍数并不能映射 agentic 编码实际消耗 token 的方式，团队想要一个具备管理/计费控制的 20x 级席位。
- **将 Desktop Code 标签页打造为一流的多 agent 控制台。** 一组紧密相关的请求（#102988、#102031、#101312）针对侧边栏信息密度：基于状态的行样式、人类可读的项目文件夹分组名称，以及将文本选择菜单暴露为 `ui.render` 组件。用户正在运行许多并发会话，而 UI 尚未跟上。
- **插件/mod API 表面扩展。** #101312 和 #102480 都涉及 `ui.render` 扩展点（`AbovePrompt`、选择菜单）。Mod 开发者正把 API 推向 Anthropic 尚未正式定义的布局领域。
- **终端兼容性与能力协商。** #99566 要求 kitty-graphics 探测信任实际的图形查询响应，而不是将 `XTVERSION` 字符串与 `kitty`/`ghostty` 匹配。基于 SwiftTerm 的终端（Moo）尽管支持 Unicode 占位符，却被排除在外。
- **TUI 人体工学。** #83528 和 #103959 都是小而精确的请求，要求可预测的快捷键和可调整大小的窗格——这项不起眼的工作决定了 CLI 是否显得专业。
- **退出行为实验。** #89735（closed stale）请求提供一个有文档记录的开关，以禁用静默队列实验；#89860 展示了下游症状：用户对未经请求的模型切换感到惊讶。这是透明度诉求，而非功能诉求。
- **远程/无头会话可靠性。** #104700 和 #97368 指向一个共同主题：会话状态和通知无法在睡眠/恢复后保留，削弱了手机侧或无人值守操作。
- **沙箱/安全同意界面。** #89866（closed stale）指出，远程 MCP 服务器可以向已受信任的连接器添加面向外部的工具而无需重新同意——这是安全模型缺口，而非 bug。

## 7. 开发者痛点

1. **沙箱会话中的破坏性文件系统操作。** #93287 和 #93408 是急性案例，但底层投诉更广泛：“沙箱”与“真实主目录”之间的信任边界对用户不可读，且事故后恢复没有文档。这是数据集中严重性最高的主题。
2. **权限分类器不可预测。** #101327 描述了同一操作在不同运行中、在变化的类别下被允许或被阻止。权限层的非确定性迫使用户回到手动批准，这违背了自动模式的目的。
3. **多 agent 工作流因单次拒绝而中止。** #88216（closed stale）说得很好：在一个工具调用上按 Esc 或 Deny 会终止整个并行运行。对于任何使用 Workflow/并行子 agent 的人来说，这使权限提示的代价不成比例地高。
4. **Desktop 会话存储中的数据完整性回归。** #89497（Windows MSIX 修复清除了 100 个对话中的 97 个）、#89873（`.jsonl` 完好却显示“No messages yet”）以及 #89871（为从未发生的一轮打上 `lastActivityAt` 时间戳）描述了一个与自身磁盘真实状态不一致的存储。
5. **通过队列实验静默改变行为。** 工具选择、文件编辑方式（例如用 `sed`/heredoc 代替 Read/Edit/Write）和系统提示的未宣布变化，破坏了用户预期以及任何固定在先前行为上的提示/评估。
6. **stale-bot 关闭实质性报告。** 今天 50 个 Issue 的窗口中很大一部分是 `[CLOSED] [stale]`——包括安全（#89866）、数据丢失（#89497）和 agent 正确性（#82821）问题。仓库自身的自动化 PR（#62597）仍未合并，因此分类工具似乎正随积压一起衰败。
7. **安装/更新路径冲突。** #84224（closed，已复现）记录了自动更新器安装到 PATH 解析出的 npm prefix，并在存在多个 Node prefix 时覆盖另一个安装——这是常见的 nvm + Homebrew 配置。与版本无关，因此会再次出现。
8. **模型路由不透明。** #89876（advisor 选择了与文档不同的模型）和 #89860（“又一次毫无理由的愚蠢切换到 opus 4.8”）表明，用户感觉自己无法控制由哪个模型执行工作，也不喜欢事后才发现。
9. **Desktop 上的通知与连接不稳定。** #97368（间歇性的轮次完成通知）、#89719 和 #83745（在孤儿 `claude.exe` 进程累积并耗尽命名管道后，Claude-in-Chrome 永久“未连接”）。后者有一个已知的变通方法——杀掉陈旧进程——但这本不应成为必要操作。

---

**2026-10-25 底线：** 该仓库正处于维护低谷——没有发布、没有开启的 PR，积压正通过 stale 关闭而非解决来削减。值得关注的两个信号是 **Team 20x 档位请求**（商业性、高度共识）和 **沙箱数据丢失报告**（安全关键、当前未解决）。

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区摘要 — 2026-10-25

## 1. 今日亮点
活动主要围绕 Windows 桌面端/app-server 稳定性与资源隔离问题展开：VS Code 扩展启动失败、MCP 进程激增、`git diff` 扇出，以及长聊天内存增长。讨论最多的主题仍是 [#50329](https://github.com/openai/codex/issues/50329)，共 33 条评论；而 [#51043](https://github.com/openai/codex/issues/51043) 获得了最强的正向反馈，共 14 👍。过去 24 小时内没有新版本发布或 PR 更新；讨论显示，社区对 AGENTS.md 验证、后台/并行 Codex 运行，以及跨 Agent 上下文交接有强烈兴趣。

## 2. 热门 Issue

1. **[#50329](https://github.com/openai/codex/issues/50329) — Windows App 的 `read_thread` 对已完成的 ChatGPT 回复仅返回 `chatgpt-content-reference`**  
   33 条评论，6 👍。一个高影响的数据检索回归：已完成的 ChatGPT 回复未能向 Windows App 返回完整内容。社区正在积极交叉核对包/版本证据。

2. **[#41695](https://github.com/openai/codex/issues/41695) — iPad App 在访问 Remote Codex Sessions 时频繁卡死**  
   22 条评论，2 👍。iPad 上的远程会话可靠性仍是反复出现的痛点，尤其是依赖移动端访问的 Pro 20x 用户。

3. **[#36718](https://github.com/openai/codex/issues/36718) — 为 Agent 任务增加有证据支撑的完成报告**  
   22 条评论。提议建立结构化的需求到证据映射，使“任务完成”声明可审计。这与对 Agent 信任和验证日益增长的需求一致。

4. **[#43019](https://github.com/openai/codex/issues/43019) — Windows 上无限制的 `git diff --no-index` 扇出耗尽提交内存并导致 Windows 崩溃**  
   18 条评论。在约 4,771 个未跟踪文件的情况下，Codex Desktop 可能为每个文件启动一个 `git diff` 并耗尽系统提交内存。一个严重的工作区规模稳定性问题。

5. **[#56691](https://github.com/openai/codex/issues/56691) — Codex 26.1021.22027 在 Windows 上的 VS Code 1.143.1 中无法启动 UI**  
   16 条评论，5 👍。一个新出现的 IDE 扩展回归，带有清晰复现细节。高点赞表明影响面可能覆盖广泛的 Windows VS Code 用户。

6. **[#49767](https://github.com/openai/codex/issues/49767) — Windows 官方 Library 传输辅助程序在 `os.setxattr` 处崩溃**  
   16 条评论。阻塞了 skills/library 工作流中保留元数据的物化流程。核心 Codex 工作流中的 Windows 兼容性缺口。

7. **[#39783](https://github.com/openai/codex/issues/39783) — Desktop 临时线程摘要通过 `thread/unsubscribe` 泄露完整 MCP 栈**  
   13 条评论，3 👍。临时摘要线程会继承并启动完整 MCP 配置，尽管摘要过程并不使用 MCP 工具。这带来了资源与暴露风险。

8. **[#53285](https://github.com/openai/codex/issues/53285) — Windows：每个线程一组 stdio MCP 进程；353 个进程 / 约 9.6 GiB**  
   8 条评论。一个具体的资源生命周期故障：MCP 进程组按线程创建，而回收速度落后于创建速度。直接影响长时间运行的 app-server 稳定性。

9. **[#51043](https://github.com/openai/codex/issues/51043) — Pi 未识别用量限制重置；OpenAI 模型用量仍被阻塞**  
   7 条评论，14 👍。本组 Issue 中点赞最高。认证/用量重置状态未能在各客户端间一致传播。

10. **[#54540](https://github.com/openai/codex/issues/54540) — Windows 上在 goals 与 subagent 工作流中反复出现“workspace routing discovery timed out”**  
    10 条评论，3 👍。Subagent 与 goal 工作流对路由发现超时敏感，指向 app-server 连接/编排不稳定。

## 3. 关键 PR 进展
过去 24 小时内没有拉取请求更新（0 项）。本次快照中没有可总结的 PR；这是数据可用性限制，并不表示没有工作正在进行。

## 4. 热门讨论

### 展示与分享

- **[#55421](https://github.com/openai/codex/discussions/55421) — TAHU：Agent 能否在不泄露员工备注的情况下发送一封无害邮件？**  
  8 条评论。一个 Python 授权网关原型，探索审批边界与隐私保护的 Agent 操作。正在招募三名开发者试用。

- **[#55086](https://github.com/openai/codex/discussions/55086) — KiraBridge + ThreadDesk：连接七个工具，实现可控、语音驱动的 vibe coding**  
  4 条评论。聚焦于限定范围访问、预算限制、可追踪结果，以及跨中断保留项目上下文。

- **[#56176](https://github.com/openai/codex/discussions/56176) — 新的 Codex 会话会接收到 AGENTS.md 吗？一个可重复的前后检查**  
  4 条评论。提供一种实用的基于标记的方法，用于验证 AGENTS.md 或 Codex 变更后的指令路径传播。

- **[#56181](https://github.com/openai/codex/discussions/56181) — pytest-codex：将 AGENTS.md 规则转化为 pytest 检查，并支持离线回放**  
  2 条评论。一个小型 MIT 许可的 pytest 插件，用于依据 AGENTS.md 规则测试 Codex CLI 行为。

- **[#56731](https://github.com/openai/codex/discussions/56731) — Agoryx：面向 Codex 与 Claude Code 的本地优先共享对话**  
  1 条评论。一个 MIT 许可的 GUI，用于在 Codex 与 Claude Code 之间进行由人类主导的构建-审查循环。

- **[#56809](https://github.com/openai/codex/discussions/56809) — wisp：在后台运行 Codex 任务，每个任务一个 worktree，一屏报告**  
  1 条评论。一个用于并行 `codex exec` 运行的工作流工具，为每个任务使用独立 git worktree，并汇总 diff 而非完整记录。

- **[#55901](https://github.com/openai/codex/discussions/55901) — 新的 Codex 聊天，同一项目：哪些决策仍然成立？**  
  1 条评论。GFT Map 组织决策、理由与待决问题，使上下文能在新聊天中延续。

- **[#56215](https://github.com/openai/codex/discussions/56215) — 在 Claude 中规划，在 Codex 中审查：用 GFT-Merge 将两者带入下一次会话**  
  1 条评论。跨 Agent 交接工具，在继续工作前合并本地对话上下文。

### 问答

- **[#52835](https://github.com/openai/codex/discussions/52835) — AI 辅助的 KPI 管理系统：开发 4 个月仍无可用应用**  
  4 条评论。一位非开发者运营经理在长期尝试 AI 辅助构建后寻求建议。凸显了非工程师在入门和项目管理上的摩擦。

- **[#56686](https://github.com/openai/codex/discussions/56686) — ChatGPT 认证的 `codex exec` 支持哪些请求前 token 边界**  
  1 条评论。询问如何在不使用 API 密钥或替换提供商变通方案的情况下设置硬性 token 上限。成本控制护栏仍存在缺口。

本次快照中未出现明确的“Ideas”主题帖。

## 5. 功能请求趋势

- **Agent 验证与可审计性：** 有证据支撑的完成报告、AGENTS.md 传播检查，以及基于 pytest 的行为测试。
- **上下文与历史连续性：** 可靠的线程读取、永久删除聊天、跨新聊天的决策图谱，以及跨 Agent 交接。
- **MCP/app-server 资源隔离：** 按线程控制 MCP 进程、临时线程清理，以及有界的 `git diff` 扇出。
- **Windows 与 IDE 稳定性：** VS Code 扩展启动、长聊天内存增长、文件系统元数据处理，以及进程回收。
- **权限与安全清晰度：** 可靠的审批、可预测的“Full access”行为，以及不会阻塞获授权安全工作的安全检查。
- **认证与速率限制一致性：** 用量重置跨客户端传播、模型容量透明度，以及 token 预算控制。
- **并行与后台编排：** 每任务一个 worktree 的运行器、一屏报告、多 Agent 共享对话，以及限定范围的工具访问。
- **远程/移动端可靠性：** iPad 与远程会话稳定性仍是明确的需求领域。

## 6. 开发者痛点

- **Windows 是摩擦最高的平台：** 反复出现扩展 UI 失败、`git diff` 进程爆炸、MCP 进程数量、`os.setxattr` 崩溃，以及长聊天渲染器内存增长的报告。
- **MCP 生命周期管理脆弱：** 按线程的 MCP 栈、临时摘要线程泄露完整配置，以及缓慢回收造成内存与进程压力。
- **线程/历史 API 静默回归：** 截断的轮次、错误的“历史结束”，以及仅含内容引用的回复，削弱了对 app-server 数据检索的信任。
- **沙箱与权限状态不透明：** 拒绝后 `/approve` 不可用、“Full access”静默回退到 workspace-write，以及安全检查阻塞获授权工作。
- **速率限制与认证状态跨客户端不一致：** 重置未被识别、模型容量失败，以及 ChatGPT 认证的 `codex exec` token 边界不清晰。
- **Agent 信任难以验证：** 自然语言完成声明缺乏证据，且在没有外部工具的情况下难以测试对 AGENTS.md 的遵循情况。
- **远程与移动会话仍不可靠：** 卡死、路由超时与连接失败会中断工作流。
- **非开发者仍难以交付：** 长期 AI 辅助项目在缺乏架构、范围控制或验证实践时可能停滞。

---

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区摘要 — 2026-10-25

## 1. 今日亮点

代理可靠性仍是主导主题：两个互动量最高的开放 issue 分别是——一个 P1，其中子代理 `MAX_TURNS` 终止被误报为 `GOAL` 成功（#22323，13 条评论）；以及 P1 的通用代理挂起（#21409，8 👍）。在 PR 方面，贡献者正在推进终端集成打磨（OSC 7501 状态上报）、系统提示词可扩展性，以及 ACP、`read_many_files` 和 shell 沙箱启发式规则的一组正确性修复。过去 24 小时内仅发布了一个 nightly 版本，包含 CLI 流取消和 CJK 显示宽度修复。

## 2. 发布

**v0.67.0-nightly.20261024.gcd1b45c2d** — [发布说明](https://github.com/google-gemini/gemini-cli/releases/tag/v0.67.0-nightly.20261024.gcd1b45c2d)

仅 nightly 构建（本时间窗内无稳定版发布）。可见变更：
- **CLI：** 在请求取消后停止继续流处理并自动提示（[#29868](https://github.com/google-gemini/gemini-cli/pull/29868)）——防止 CLI 在用户取消后继续消费/输出。
- **CLI：** 修正泰语和老挝语 SARA AM 的显示宽度（[#29868-adjacent PR by @elberthc-byte](https://github.com/google-gemini/gemini-cli/pulls)）——针对东南亚文字的终端宽度渲染修复。

## 3. 热门 Issue

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — P1：子代理 `MAX_TURNS` 恢复被报告为 `GOAL` 成功**（13 条评论，2 👍）。`codebase_investigator` 返回 `status: "success"` / `Termination Reason: "GOAL"`，尽管其自身输出承认在分析任何内容之前就已达到轮次上限。这是当天评论最多的 issue，因为它破坏了编排语义：调用方无法区分“已完成”和“已放弃”，导致多代理流水线无法验证。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — P1：通用代理挂起**（8 条评论，**8 👍**——本组中最高反应数）。一旦工作被委派，诸如创建文件夹之类的简单操作就会无限期挂起；用户报告等待长达一小时。变通方法（指示模型不要委派给子代理）实际上禁用了该功能，这解释了社区为何反应强烈。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — P2：零依赖操作系统沙箱 + 执行后意图路由**（9 条评论）。提议让 Gemini 依赖其原生的 bash 亲和力（`grep`/`sed`/`awk` 链），同时通过沙箱而非限制工具来控制风险。值得注意的是，这是对当前工具权限模型的主要架构性反提案。

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — P2：Gemini 没有充分使用技能和子代理**（7 条评论）。虽是轶事但引发广泛共鸣：描述清晰的技能（例如 `gradle`、`git`）除非显式调用，否则会被忽略。这很重要，因为它削弱了整个技能/子代理投入的价值。

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — P2 EPIC：AST 感知的文件读取、搜索和代码库映射**（7 条评论，1 👍）。跟踪 AST 感知工具是否能减少错位读取、轮次数量和 token 噪声。配套 issue [#22746](https://github.com/google-gemini/gemini-cli/issues/22746)（CLI 工具，tilth/glyph）和 [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)（ast-grep）表明这正被拆解为具体实验。

6. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — P1：浏览器子代理在 Wayland 上失败**（4 条评论，1 👍）。在已经失败的情况下打印 “Browser Agent Finished / Termination Reason: GOAL”——与 #22323 相同的虚假成功模式，如今还带有 Linux 显示服务器维度。

7. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — P2：作用域内工具过多时出现 400 错误**（3 条评论）。标题提到 >128 个工具，正文提到 >400 个；无论如何，代理没有对启用工具进行作用域划分的策略，因此大型 MCP 配置可能硬失败。

8. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — P2：代理应停止/劝阻破坏性行为**（3 条评论，1 👍）。在存在更安全路径时，模型会动用 `git reset`/`--force`，并且对数据库等有状态资源缺乏护栏。这与无人值守/YOLO 使用直接相关。

9. **[#22186](https://github.com/google-gemini/gemini-cli/issues/22186) — P1：`get-shit-done` 输出钩子导致崩溃**（3 条评论）。打印最终摘要时可复现崩溃——也就是说，失败发生在将结果交还给用户的时刻。

10. **[#22598](https://github.com/google-gemini/gemini-cli/issues/22598) — P3：子代理轨迹应可通过 `/chat share` 查看**（2 条评论，1 👍）。轨迹已被记录但无法访问，阻碍了子代理审查和评估。小功能，却能极大帮助调试上述问题。

*同样值得关注：* [#22267](https://github.com/google-gemini/gemini-cli/issues/22267)（浏览器代理忽略 `settings.json` 覆盖项，例如 `maxTurns`）、[#20079](https://github.com/google-gemini/gemini-cli/issues/20079)（符号链接的代理 `.md` 文件未被发现）、[#21924](https://github.com/google-gemini/gemini-cli/issues/21924)（无闪烁终端调整大小）、[#18836](https://github.com/google-gemini/gemini-cli/issues/18836)（用基于文件的持久化任务 CRUD 替换 `WriteToDo`）。

## 4. 关键 PR 进展

1. **[#29918](https://github.com/google-gemini/gemini-cli/pull/29918) — `feat(cli)`：通过 OSC 7501 上报 working/blocked/idle 状态**（XL）。发出 Program Status 报告，使终端能够呈现多个会话中哪一个需要关注。这是迈向多会话、终端原生代理工作流的有意义一步。

2. **[#29919](https://github.com/google-gemini/gemini-cli/pull/29919) — `feat(cli)`：`--append-system-prompt`**（L）。新增 `--append-system-prompt`、`--append-system-prompt-file` 和 `GEMINI_APPEND_SYSTEM_MD`。填补了一个真实空白：目前 `GEMINI_SYSTEM_MD` 会替换整个提示词，因此要添加一行项目上下文就意味着得 fork 整个提示词。

3. **[#20406](https://github.com/google-gemini/gemini-cli/pull/20406) — `perf(core)`：大型工具输出的内存管理**（XL）。将过大的 shell 输出写入磁盘而不是缓存在内存中，修复截断路径中的 V8 OOM。该 PR 推进时间长，且对大型仓库用户影响很大。

4. **[#23917](https://github.com/google-gemini/gemini-cli/pull/23917) — `feat(ui)`：`StyleSpan` 抽象**（XL）。用游程编码替代逐字符 `toStyledCharacters()` 分配，针对复杂 `TableRenderer` 场景中 280MB+ 的堆膨胀。

5. **[#29910](https://github.com/google-gemini/gemini-cli/pull/29910) — `fix(acp)`：权限请求中的 MCP 元数据 + 客户端信任**（M）。将服务器/工具名称填充到 `toolCall._meta['gemini-cli'].mcp`，并允许 ACP 客户端通过 `_meta.trust` 预授权客户端提供的 MCP 服务器——这对编辑器集成部署很重要。

6. **[#29778](https://github.com/google-gemini/gemini-cli/pull/29778) — `fix(acp)`：在发送给模型前截断工具输出**（M）。`truncateToolOutputThreshold` 在 `--acp` 模式下从未生效，因此一次大型 shell 输出就可能以 400 错误突破输入限制。消除了一处显著的 ACP 与 CLI 行为差异。

7. **[#29783](https://github.com/google-gemini/gemini-cli/pull/29783) — P1 `fix(core)`：为 `read_many_files` 资源匹配 include 模式**（M）。用真正的模式匹配替代子串检查；此前任何文本重叠都可能将用户从未请求的二进制资源内联。

8. **[#29782](https://github.com/google-gemini/gemini-cli/pull/29782) — P1 `fix(core)`：沙箱拒绝启发式中的退出码空值检查**（S）。对于 `null`，`exitCode !== undefined` 始终为 true，因此每个退出码为 null 的结果都会进入拒绝分支。小改动，但与安全相关的正确性修复。

9. **[#29912](https://github.com/google-gemini/gemini-cli/pull/29912) — `fix(core)`：从 `web_fetch` URL 中去除尾部标点/不配对括号**（M）。`new URL()` 会欣然接受尾随逗号，因此逗号分隔的 URL 列表会生成受污染的 URL 并导致下游失败。

10. **[#29779](https://github.com/google-gemini/gemini-cli/pull/29779) — `fix(core)`：将失败的 ripgrep 搜索标记为工具错误**（M）。`RipGrepTool` 返回失败时未设置 `error`，因此调度器将其记录为成功，同时又告诉模型搜索失败——这是与 #22323 同类的静默事实偏差 bug。

*另已合并/关闭或进行中：* [#29890](https://github.com/google-gemini/gemini-cli/pull/29890)（仅在成功恢复后恢复工作区目录）、[#29746](https://github.com/google-gemini/gemini-cli/pull/29746)（PowerShell 替换引号跟踪）、[#29785](https://github.com/google-gemini/gemini-cli/pull/29785)（在 EOS 时分发最终的 Code Assist SSE 事件）、[#29902](https://github.com/google-gemini/gemini-cli/pull/29902)（文档：`commandPrefix` 匹配命令名称而非效果——建议为无人值守运行使用允许列表）。

## 5. 热门讨论

本时间窗未提供讨论数据，因此省略本节。

## 6. 功能请求趋势

- **子代理编排与可观测性。** 目前最大的集群：准确的终止语义（#22323）、`/chat share` 中的轨迹可见性（#22598）、通过 `settings.json` 发现（#18285）、共享内存/并行协作（#18287），以及代理对自身标志和快捷键的自我感知（#21432）。贯穿始终的主线是*让多代理运行可检查且可信*。
- **上下文与 token 节俭。** AST 感知的读取/搜索/映射（#22745、#22746、#22747）和 “Tactful Extraction” 外科手术式读取层级（#19561）都针对每轮约 36.6k token 的基线，以及因不加筛选的文件读取而导致的每轮 +15k 膨胀。
- **沙箱化优先于限制。** #19873 主张零依赖操作系统沙箱加上执行后意图路由，使模型能够安全地使用原生 bash 惯用法；#22672 要求为破坏性命令设置护栏；#18397 要求按工作区（而非全局）划分策略作用域。
- **上下文之外的持久状态。** #18836 和 #21000 都将任务跟踪从提示词中推到文件系统（或原生文件工具）上，以避免上下文腐化和会话丢失。
- **更智能的工具作用域划分。** #24246 要求代理推理哪些工具已启用，而不是盲目挂载所有工具并触发 400。
- **将终端 UX 作为一等界面。** 无闪烁调整大小（#21924）和 OSC 7501 状态上报（#29918）反映了用户正在运行许多并发 CLI 会话。

## 7. 开发者痛点

- **虚假成功报告。** 跟踪器中腐蚀性最强的单一模式：`MAX_TURNS` 以 `GOAL` 成功的形式出现（#22323）、Wayland 浏览器失败也报告 `GOAL`（#21983）、ripgrep 失败被记录为成功（#29779）。开发者无法信任退出信号，这破坏了自动化和评估。
- **无超时或诊断信息的无限期挂起。** 通用代理委派（#21409）和 `vite create` 等交互式提示（#22465）会一直停滞，直到手动取消；用户报告等待一小时。唯一已知的修复方法是禁用该功能。
- **被静默忽略的配置。** 浏览器代理跳过 `settings.json` 中诸如 `maxTurns` 的覆盖项（#22267），符号链接的代理文件未被发现（#20079），并且 `truncateToolOutputThreshold` 在 ACP 模式下不起作用（#29778）。配置面与配置行为出现分歧。
- **转义和引号边界情况。** 错误的 `\n` 处理（#22466）、PowerShell 替换引号失步（#29746）和 URL 分词（#29912）都源于围绕 shell 和文本边界的简单字符串处理。
- **平台特定故障。** Wayland（#21983）、泰语/老挝语 SARA AM 宽度（nightly 修复）和 Windows 引号处理继续产生各自的问题流——跨平台一致性是一项持续成本。
- **内存与渲染开销。** 大型工具输出导致 OOM（#20406）、表格渲染中的逐字符分配（#23917）以及调整大小闪烁（#21924）都指向 TUI 的扩展极限。
- **工作区卫生与影响范围。** 散落在各个目录中的临时脚本（#23571）和破坏性 git 操作（#22672）使无人值守运行难以清理且难以信任。
- **提示词层的可扩展性缺口。** 缺少非破坏性的系统提示词追加（由 #29919 解决），迫使用户为了一行项目指令而替换整个提示词。

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — 社区摘要
**日期：** 2026-10-25 | **来源：** [github/copilot-cli](https://github.com/github/copilot-cli)

---

## 1. 今日亮点

两个补丁版本发布（`v1.0.104-2`、`v1.0.104-3`），其头条是一项修复，确保交互式/SDK 模式下的 Autopilot 会话*等待*关联的后台任务，而不是在达到 `COPILOT_TASK_WAIT_TIMEOUT_SECONDS` 时终止。Issue 活动很多（20 项更新），主要集中在**异步/后台任务正确性**（#4946、#5242、#5205）和 **agent/子代理工具链回归**（#5243、#5249），另有一个围绕 Assisted Permissions 配额消耗的值得注意的计费事件（#4802）。过去 24 小时内没有 Pull Request 更新。

---

## 2. 发布

**`v1.0.104-3`** — *修复与变更*（上游说明被截断）

**`v1.0.104-2`**
- **已修复：** 托管模型 `fusion` 现在会在 **Auto** 上启动会话，并正确应用 `fusionTier` 设置。
- **已修复：** 交互式和 SDK Autopilot 会话现在会持续等待关联的后台任务，而不是在 `COPILOT_TASK_WAIT_TIMEOUT_SECONDS` 后停止；`-p --autopilot` 仍会遵守该超时。
- *注：* 发布正文非常简短——下面多个未关闭 issue 引用了 `1.0.101`–`1.0.103` 的行为，因此应针对 `1.0.104-3` 确认回归测试覆盖情况。

🔗 [Releases](https://github.com/github/copilot-cli/releases)

---

## 3. 热门 Issue

1. **[#4946 OPEN] 后台 shell 完成后出现 HTTP 400 `content[].thinking`** — 8 条评论，👍1
   互动量最高的未关闭条目。后台 shell 命令在所属轮次结束后仍然存活，会将 `system.notification` 注入到*新的*轮次中，破坏 thinking 块负载，并导致请求硬失败。与 `v1.0.104-2` 中交付的新后台任务等待语义直接相关。
   🔗 [Issue #4946](https://github.com/github/copilot-cli/issues/4946)

2. **[#4802 OPEN] PRU 配额被耗尽，可能与 Assisted Permissions 有关** — 6 条评论
   一位基于请求计费的旧版 PRU 用户报告称，在启用 Assisted Permissions 后，234 个 PRU 被耗尽。这会影响计费，因此尽管反应不多，仍在社区中具有超常分量——值得优先处理，以明确分诊判断。
   🔗 [Issue #4802](https://github.com/github/copilot-cli/issues/4802)

3. **[#5243 OPEN] 在批准 `exit_plan_mode` 的同一轮次中启动的子代理无法获得内置工具** *(1.0.95 之后的回归)*
   子代理在 `interactive` 计划批准后派生时，会完全失去 `bash`/`powershell`、`view`、`create`、`edit`、`grep`、`glob`、`task`、`skill` 和 `web`。这对“先计划后执行”工作流而言是一种严重的静默失败模式。
   🔗 [Issue #5243](https://github.com/github/copilot-cli/issues/5243)

4. **[#5242 OPEN] 当 MCP 配置监视命中 inotify `ENOSPC` 时会话停止处理**
   在共享 Linux 主机上的容器中运行 `copilot --server` 时，如果 inotify 预算耗尽，它会在一次重试后丢弃会话队列——这是面向服务器部署的生产级可靠性 bug。
   🔗 [Issue #5242](https://github.com/github/copilot-cli/issues/5242)

5. **[#5229 OPEN] ACP 模式：过期的 MCP OAuth token 会打开浏览器，而不是使用 refresh token**
   `copilot --acp` 会直接跳到 `[acp::mcp_oauth] opened the MCP OAuth authorization page`，忽略已存储的 `refresh_token`。破坏了无人值守/无头 ACP 集成。
   🔗 [Issue #5229](https://github.com/github/copilot-cli/issues/5229)

6. **[#5205 OPEN] 上下文窗口轮转失败："checkpoint.md is absent for this agent"**
   长时间运行的 agent 会话在上下文窗口已满并尝试轮转时会崩溃。这是本周期报告的、对持续自主运行最明确的阻碍。
   🔗 [Issue #5205](https://github.com/github/copilot-cli/issues/5205)

7. **[#5249 OPEN] 面向模型的 `skill()` 工具以裸名称注册插件技能**
   斜杠命令在 #1766 中获得了 `/plugin:skill` 命名空间，但 `skill()` 没有——因此除非两个插件恰好发生命名冲突，`skill("alpha:unique")` 会返回 `Skill not found`。这是一个会困扰插件作者的 API 一致性问题。
   🔗 [Issue #5249](https://github.com/github/copilot-cli/issues/5249)

8. **[#5228 OPEN] 不带 ID 的 `copilot --resume` 会落入新的空会话**
   自统一 Sessions 浏览器成为默认项以来，列表会先渲染约 2 秒再切换走，而信任提示会吞掉第一次 Enter。这是最近交付功能带来的 UX 回归——复合、可复现且用户可见。
   🔗 [Issue #5228](https://github.com/github/copilot-cli/issues/5228)

9. **[#5245 OPEN] 自 1.0.101 起，企业代理环境下没有可用的 STT 模型** *(Windows)*
   Foundry 运行时会忽略代理设置，因此在执行 TLS 检查的企业网络上，语音/STT 会完全失败。值得注意的是，这是一个**横切运行时配置 bug**，而不仅仅是语音 bug。
   🔗 [Issue #5245](https://github.com/github/copilot-cli/issues/5245)

10. **[#3301 OPEN] 功能请求：本地 Web 界面（类似 `opencode web`）** — 👍3（本组中反应最高）
    一项长期存在的请求，希望通过本地 HTTP 服务器提供基于浏览器的 UI。约 5 个月来持续获得点赞，表明对非 TUI 交互模式存在持久需求。
    🔗 [Issue #3301](https://github.com/github/copilot-cli/issues/3301)

**过去 24 小时内还关闭了：** [#1616](https://github.com/github/copilot-cli/issues/1616)（Autopilot 在任务完成后继续消耗 token）、[#5147](https://github.com/github/copilot-cli/issues/5147)（`/restart` 因 `--new-session-id` 失败）、[#3815](https://github.com/github/copilot-cli/issues/3815)（Windows 调试日志路径缺少 `\`）。

---

## 4. 关键 PR 进展

**过去 24 小时内没有 Pull Request 更新（总计：0 项）。** 本摘要窗口内没有可报告的合并或审查活动。

---

## 5. 热门讨论

*已省略——未提供 Discussions 数据。*

---

## 6. 功能请求趋势

根据本窗口内更新的 20 个 issue 提炼：

- **替代交互界面** — 本地 Web UI/基于浏览器的 agent（#3301，👍3）是获赞最多的请求；同一类还有：为改善长时间会话使用体验将输入框固定到屏幕顶部（#5227）。
- **ACP / 非交互模式与 TUI 的对等性** — Computer Use 插件在 CLI 中已启用，但在 ACP 模式下不可用（#5049）；捆绑插件和 MCP 功能的门控在无头路径与交互路径之间不一致。
- **无头 OAuth 与网络可配置性** — ACP 中的 refresh token 复用（#5229）和运行时网络支持代理感知（#5245）都是为了让非交互运行在企业基础设施环境下可行。
- **插件/技能命名空间一致性** — #5249 要求面向模型的 `skill()` 工具与斜杠命令已使用的 `/plugin:skill` 约定保持一致。
- **服务器模式韧性** — #5242 请求 MCP 配置监视失败时优雅降级（重试/退避/watch 预算），而不是放弃会话队列。

---

## 7. 开发者痛点

- **后台任务与轮次边界仍然脆弱。** `v1.0.104-2` 的等待语义修复与 #4946（后台 shell 完成后 thinking 块 400）以及新关闭的 #1616（Autopilot 在任务完成后消耗 token）同时出现——异步生命周期是当前最活跃的单一故障来源。
- **无头/ACP 部署最先撞上基础设施边界。** inotify 耗尽（#5242）、缺少 OAuth refresh（#5229）、无法感知代理的运行时（#5245）以及 ACP 插件缺口（#5049）共同使“在我笔记本上能跑，在 CI/容器里不行”成为最主要的部署抱怨。
- **回归问题与功能迭代速度相伴。** #5243（`exit_plan_mode` 后子代理工具丢失，1.0.95 之后）和 #5228（新 Sessions 浏览器导致 `--resume` 损坏）是新交付的功能面在悄然回归——用户要求围绕计划批准和会话选择增加回归测试。
- **长时间运行的 agent 无法维持上下文。** #5205（轮转时缺少 `checkpoint.md`）连同 #1616 和 #5243 表明，长时间自主运行——Autopilot 的标志性用例——仍然缺乏可靠的完成路径。
- **成本可预测性是信任问题。** Assisted Permissions 配额事件（#4802）量小但严重性高：模糊的计费结果比功能缺口更快侵蚀采用率，用户希望有文档化的归因路径。
- **静默、低信号的失败模式。** #5249（`Skill not found`）、#5243（工具静默缺失）和 #5107（在覆盖 `HOME` 时对无害 `echo` 触发 `script_action_changed`）都会在缺乏可操作诊断信息的情况下失败——这是对更清晰错误呈现的一再请求。

---

*生成于 2026-10-25。Issue 集合：20 项更新，3 项关闭。PR 集合：0 项。发布：2 个。*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区摘要 — 2026-10-25

## 今日亮点

过去 24 小时内没有新版本发布，社区注意力几乎完全被一个广泛存在的 **免费层配额故障** 占据：v1 和 v2、CLI 和 Desktop 用户均报告在零使用量情况下出现 “Free usage exceeded” 错误，86,400 秒冷却时间会归零后反复循环，而不是正常重置。与此同时，维护者合并了一大批核心可靠性 PR，涵盖会话完整性、子代理结果上报、优雅关闭以及目录/ETag 优化。

---

## 发布

过去 24 小时内未发布新版本。

---

## 热门 Issue

1. **[#58831] 免费配额超时永远无法恢复，在 86400s 处呈锯齿状波动** — [link](https://github.com/anomalyco/opencode/issues/58831)
   当前配额 bug 最清晰的复现：倒计时归零后立即重置为 86,400s。这是影响所有免费用户的权益重置故障的标志性讨论帖。

2. **[#58830] v1 和 v2 的免费使用限额连续两天未重置** — [link](https://github.com/anomalyco/opencode/issues/58830)
   证实该回归同时波及 *两个* 大版本，排除了仅限 v2 的迁移问题。在一次成功请求后，所有免费模型都失败——这强烈表明按账户的使用计数器被错误持久化或读取。

3. **[#58828] “OpenCode 真的坏了，必须立刻修复”** — [link](https://github.com/anomalyco/opencode/issues/58828)
   一条情绪激烈的升级投诉，报告 Go 和 Zen 模型立即出现配额耗尽，即使没有订阅的用户也要等待 86,400s。说明该配额 bug 正在导致用户流失和敌意反馈。

4. **[#58781] OpenCode Desktop 1.19.4 中所有免费模型均显示 Free Usage Exceeded** — [link](https://github.com/anomalyco/opencode/issues/58781)
   本时段内获赞最多的 issue（👍 5）。Desktop 特定视角加上票数，使其成为反映免费层更广泛用户情绪的最佳风向标。

5. **[#58605] 闲置一周后出现 “Free usage exceeded”（Desktop）** — [link](https://github.com/anomalyco/opencode/issues/58605)
   补充了一个关键细节：重试计时器显示 “retrying in 72970s - attempt no. 1”，这表明剩余配额或过期时间戳格式异常，而不是真实的使用计数。

6. **[#58827] 尚未提交任何提示词就触发了使用限制** — [link](https://github.com/anomalyco/opencode/issues/58827)
   零提示词即耗尽是最有力证据，说明检查在发出任何请求 *之前* 就已失败——即权益/认证查找失败，而非过度消耗。

7. **[#58724] 手动压缩遗漏必需的 `recent`，永久损坏会话** — [link](https://github.com/anomalyco/opencode/issues/58724)
   严重的数据完整性 bug：schema 解码拒绝已持久化的压缩消息，导致后续每次读取都失败，fork 出的会话也被污染——且应用内无法恢复。对长时间运行的会话影响很大。

8. **[#58527] 权限 “ask” 静默允许 edit/write** — [link](https://github.com/anomalyco/opencode/issues/58527)
   一个与安全相关的 bug：`ask` 对 shell 会渲染审批对话框，但对 edit/write 却表现得像 `allow`。一个承诺显式同意的工具却静默写入文件，这会破坏信任。

9. **[#52925] `opencode api get` 在管道输出时截断 stdout** — [link](https://github.com/anomalyco/opencode/issues/52925)
   在较大（约 580 KB）响应下破坏了标准的 `opencode api get ... | jq` 工作流，而文件重定向正常。自 Oct 3 以来长期存在且仍未关闭——是脚本/CI 的阻塞问题。

10. **[#58533] Desktop 自定义提供商对话框写入 V1 密钥，V2 服务器忽略** — [link](https://github.com/anomalyco/opencode/issues/58533)
    代表了 v1→v2 迁移类故障：以旧格式写入的配置会产生令人困惑的 “provider not available on this server” toast 提示。值得与 [#58529](https://github.com/anomalyco/opencode/issues/58529)（DeepSeek V4.1 Flash Free 无限循环）和 [#57931](https://github.com/anomalyco/opencode/issues/57931)（过期的免费模型阻塞整个目录）一起强调，它们都具有“一个坏模型污染整个目录”的模式。

*其他值得关注：* [#55077](https://github.com/anomalyco/opencode/issues/55077) — GPT-6.1 Sol 上下文窗口在 v2.0.28 中提升到 628k，但仍未达到宣传的 1.05M；[#58558](https://github.com/anomalyco/opencode/issues/58558) — 硬编码的 1s 发现超时会让远程 Ollama/LM Studio/vLLM 端点静默返回零模型（已关闭）；[#58559](https://github.com/anomalyco/opencode/issues/58559) — Windows `%temp%` 重定位会导致 CLI 崩溃（已关闭）。

---

## 关键 PR 进展

1. **[#58820] perf(core): 在 worker 中调整图像尺寸，空闲时释放 photon 内存** — [link](https://github.com/anomalyco/opencode/pull/58820)
   通过将图像大小调整移出主线程并在 10s 后让 worker 空闲，消除了每张大图导致的 420–460 ms 服务器冻结，并回收约 250 MB 保留的 WASM 内存。显著提升 Desktop/web 响应性。

2. **[#58817] fix(cli): 用 10s 截止时间限制服务器关闭** — [link](https://github.com/anomalyco/opencode/pull/58817)
   此前在 SIGTERM/SIGINT 后，清理过程可能永远挂起，且不提示卡在哪一步。现在会记录待处理工作并以状态码 1 退出，同时处理第二次信号。

3. **[#58825] fix(core): 将后台子代理后续轮次上报给其父级** — [link](https://github.com/anomalyco/opencode/pull/58825)
   修复了当子代理进入空闲、随后被插件或合成消息唤醒时，父级收到 “Subagent completed without a text response” 的问题——这是面向多代理编排的正确性修复。

4. **[#58811] fix(schema): 以线性时间校验 base64 附件** — [link](https://github.com/anomalyco/opencode/pull/58811)
   替换了一个容易发生灾难性回溯的正则表达式，该正则会在 *每次* 已存储消息解码时运行（历史重载、收件箱两次、投影器、GET messages）。显著改善延迟并增强健壮性。

5. **[#58813] fix(core): 使用 ETag 重新校验 models.dev 目录** — [link](https://github.com/anomalyco/opencode/pull/58813)
   5 分钟刷新从未发送 `If-None-Match`，导致每个周期都重新下载并解析完整的约 6 MB 原始（约 413 KB brotli）目录，即使有可用的 304。

6. **[#55146] fix(core): 前台压缩使用会话传输** — [link](https://github.com/anomalyco/opencode/pull/55146)
   前台摘要压缩即使在所选模型使用 WebSocket 时也通过 HTTP 发送。与 #58724 中出现的压缩相关故障直接相关。

7. **[#58790] fix(ai): 添加 `compatibility.omitEmptyTools` 以丢弃空 tools 数组** — [link](https://github.com/anomalyco/opencode/pull/58790)
   解决某些提供商拒绝“有效工具集为空但历史记录包含工具调用/结果”的请求的问题——这是常见的跨提供商互操作故障。

8. **[#58788] fix(core): 当 patch 移动模拟重命名时保留文件模式** — [link](https://github.com/anomalyco/opencode/pull/58788)
   “Move to” 实现为先写入再删除，会静默丢失目标位置的权限。对于代理操作到的脚本和可执行文件很重要。

9. **[#58800] perf(core): 无变化时跳过集成列表重建** — [link](https://github.com/anomalyco/opencode/pull/58800)
   每个会话步骤都会多次重建集成列表，每次遍历都解码所有已存储的凭据。缓存修复显著降低了每步开销。

10. **[#58310] fix(cli): 跨卷保留 Windows 二进制文件** — [link](https://github.com/anomalyco/opencode/pull/58310)
    当包和缓存位于不同的 Windows 卷时，保留操作会因 `EXDEV` 失败；这与 #58559 中的 `%temp%` 重定位崩溃互为补充。

*其他已合并/关闭：* [#58815](https://github.com/anomalyco/opencode/pull/58815)（停止将服务 `XDG_STATE_HOME` 泄漏到工具 shell，命名端口所有者）、[#58758](https://github.com/anomalyco/opencode/pull/58758)（用 `Decision` 替换实验性评估）、[#58771](https://github.com/anomalyco/opencode/pull/58771)/[#58770](https://github.com/anomalyco/opencode/pull/58770)（针对 Google Interactions 和 AWS restJson1 的错误分类），以及文档更新 [#58791](https://github.com/anomalyco/opencode/pull/58791)（MiMo Code 移入已验证客户端）和 [#58823](https://github.com/anomalyco/opencode/pull/58823)（Luminary 记忆插件）。

---

## 功能请求趋势

- **主题/自定义可扩展性** — [#58824](https://github.com/anomalyco/opencode/issues/58824) 请求为内置主题（Catppuccin Mocha、Tokyo Night）提供主题覆盖和语言 token 扩展，而不是固定的作用域映射。用户希望在不 fork 主题的情况下扩展主题。
- **更好的多代理可观测性** — [#58809](https://github.com/anomalyco/opencode/issues/58809) 请求在 Desktop v2 的运行中子代理列表中显示每个子代理的 *实际生效* 模型和推理强度。随着子代理编排增长，了解子代理实际在做什么的可见性需求也在增长。
- **更严格的轮次控制/引导语义** — [#58776](https://github.com/anomalyco/opencode/issues/58776) 提出带预期轮次保护的条件式引导准入，只有当会话仍拥有该轮次时才接纳纠正。
- **面向工具调用模型的 Schema 清晰度** — [#55504](https://github.com/anomalyco/opencode/issues/55504) 指出 `sessionID` 在 TypeScript 中是可选的，但序列化后会让模型将其视为必需，推动生成更明确的 JSON Schema。

---

## 开发者痛点

1. **免费层权益检查是主要故障模式。** 本时段至少有十几个 issue（[#58536](https://github.com/anomalyco/opencode/issues/58536)、[#58550](https://github.com/anomalyco/opencode/issues/58550)、[#58553](https://github.com/anomalyco/opencode/issues/58553)、[#58557](https://github.com/anomalyco/opencode/issues/58557)、[#58605](https://github.com/anomalyco/opencode/issues/58605)、[#58781](https://github.com/anomalyco/opencode/issues/58781)、[#58827](https://github.com/anomalyco/opencode/issues/58827)、[#58828](https://github.com/anomalyco/opencode/issues/58828)、[#58829](https://github.com/anomalyco/opencode/issues/58829)、[#58830](https://github.com/anomalyco/opencode/issues/58830)、[#58831](https://github.com/anomalyco/opencode/issues/58831)、[#58832](https://github.com/anomalyco/opencode/issues/58832)）报告在零次或一次请求后就出现配额耗尽。报告覆盖 CLI v2.1.1、Desktop 1.19.4 和 v2，以及多个地区/IP——指向服务器端计数器或权益查找问题，而非客户端过度使用。不重置的 86,400s 计时器和 “retrying in 72970s” 表明时间戳处理也已损坏。

2. **v1 → v2 迁移仍然粗糙。** 以 V1 格式（`provider`、`npm`、`options`）写入的配置会被 V2 服务器静默忽略（[#58533](https://github.com/anomalyco/opencode/issues/58533)），而专门为避开 v1 配额 bug 而迁移的用户，会在全新密钥上遇到 API 密钥过期（[#58832](https://github.com/anomalyco/opencode/issues/58832)）。

3. **版本/配置漂移导致脆弱行为。** 硬编码的 1 秒发现超时（[#58558](https://github.com/anomalyco/opencode/issues/58558)）、过期免费模型使整个目录失效（[#57931](https://github.com/anomalyco/opencode/issues/57931)），以及过时的模型元数据（[#55077](https://github.com/anomalyco/opencode/issues/55077)），都表明需要优雅降级，而不是全有或全无的失败。

4. **数据完整性和同意保证正受到质疑。** 手动压缩可能永久损坏会话（[#58724](https://github.com/anomalyco/opencode/issues/58724)），而 `ask` 权限会静默允许文件写入（[#58527](https://github.com/anomalyco/opencode/issues/58527)）。两者都在侵蚀人们对长时间运行代理工作流的信心。

5. **Windows 和 shell 边界情况仍然存在。** `%temp%` 重定位会导致 CLI 崩溃（[#58559](https://github.com/anomalyco/opencode/issues/58559)），跨卷二进制保留会因 `EXDEV` 失败（[#58310](https://github.com/anomalyco/opencode/pull/58310)），而 Nushell PATH 设置直到最近才得到支持（[#51227](https://github.com/anomalyco/opencode/pull/51227)）。

6. **脚本化能力缺口。** `opencode api get` 截断管道输出（[#52925](https://github.com/anomalyco/opencode/issues/52925)）会阻塞标准的 `| jq` 和 CI 自动化模式——一个小修复对工具用户影响巨大。

---

*本时段未提供讨论数据，因此省略“热门讨论”部分。*

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区摘要 — 2026-10-25

## 1. 今日亮点

过去 24 小时没有新发布，但当天主要被 mitsuhiko 提交的一连串堆叠的 provider 扩展 PR（#11368 → #11372）占据：该链重构模型目录，并新增 Alibaba Model Studio、Z.AI、DeepInfra、Nebius、Ollama Cloud、Scaleway、SiliconFlow、StepFun、Vertex 托管的 Claude，以及 Bedrock Mantle 路由。与此同时，参与度最高的 issue 仍是长期存在的可靠性问题：`openai-codex` TUI 卡死（#4945，88 条评论）和流式输出期间 TUI 核心占满（#6665、#7730）。一组 1.3.0/1.4.0 终端回归（Windows CSI 泄漏、Ctrl+Shift+C 提示词丢失、Alacritty IME 自动补全）已完成分诊，且大多在数小时内关闭。

---

## 2. 发布

略 — 过去 24 小时没有发布。

---

## 3. 热门 Issue

1. **[#4945](https://github.com/earendil-works/pi/issues/4945) — `openai-codex` 连接可靠性（OPEN, in-progress, 88 条评论, 👍35）**
   TUI 卡在 `Working...`，没有流式文本、没有工具调用、也没有错误；按 Escape 是唯一恢复方式。这是跟踪器中讨论最多的单个 issue，也是最清晰的信号：provider 级流处理仍存在无界等待路径。

2. **[#7730](https://github.com/earendil-works/pi/issues/7730) — 长时间会话下 macOS CPU 占用高（OPEN, bug, 22 条评论, 👍11）**
   长会话下 CPU 占用 50–110%、RSS 为 600–800 MB，似乎与上下文大小相关。社区将其视为多小时 agent 使用场景的实际上限。

3. **[#6665](https://github.com/earendil-works/pi/issues/6665) — 流式输出时 TUI 占满一个完整核心（OPEN, in-progress, 15 条评论, 👍7）**
   用 `pi -ne` 可复现，因此原因在核心而非扩展：未缓存的 `Intl.Segmenter` 字素分割，以及每块都完整重建 Markdown。基于 spindump 的诊断使其成为当前开放的最可操作的性能报告之一。

4. **[#11157](https://github.com/earendil-works/pi/issues/11157) — `session_start` 时的 `sendMessage` 会让每个会话变成 legacy transcript（OPEN, `prio:p0`, in-progress）**
   `getInitialSystemMessage` 读取 `messages[0]`；pi-memory 和 pi-session-search 等扩展会在其前面注入内容，因此 Anthropic 请求会静默丢失 `system` 字段。一个完全由扩展顺序导致的 p0 正确性 bug。

5. **[#8643](https://github.com/earendil-works/pi/issues/8643) — Bedrock：OpenAI 模型拒绝嵌套在 `toolResult.content` 中的图片（OPEN, 14 条评论, 👍5）**
   请求应将工具结果中的图片提升到同级的 user 块中，与 `openai-completions.ts` 现有行为一致。值得注意的是，报告者称修复和回归测试已就绪，但此前的 PR（#8642）被贡献门禁自动关闭。

6. **[#8810](https://github.com/earendil-works/pi/issues/8810) — 扩展注册的 provider 间歇性忽略 `defaultProvider`/`defaultModel`（OPEN, 9 条评论, 👍5）**
   当 provider 来自 `pi.registerProvider()` 时，新会话会回退到另一个 provider 的默认值。这种不确定性会削弱对扩展 provider API 的信任。

7. **[#10157](https://github.com/earendil-works/pi/issues/10157) — AI Studio 的 OpenAI 兼容端点上 Gemini thought signatures 丢失（OPEN）**
   工具调用会返回 `extra_content.google.thought_signature`，但没有被回放，导致通过 AI Studio 使用 Gemini Flash Lite 的多轮推理中断。

8. **[#10390](https://github.com/earendil-works/pi/issues/10390) — Anthropic SSE 解析器错误处理跨 chunk 拆分的 CRLF（OPEN, bug）**
   `\r` 和 `\n` 分别到达不同 chunk 时会被当作两个换行，从而冲刷出一个没有 `data:` 的事件，抛出 `Unexpected end of JSON input` 并中止该轮。一个典型的传输边界 bug，影响范围窄但后果致命。

9. **[#11221](https://github.com/earendil-works/pi/issues/11221) — Pi 在长时间运行的会话中不会获取刷新后的 AWS 凭证（OPEN, bug）**
   超过 STS 令牌生命周期的 Bedrock 会话会以 `ExpiredTokenException` 失败，即使后台刷新器正在运行。与 #10390 一起表明，长生命周期会话的凭证/传输处理需要明确负责人。

10. **[#11379](https://github.com/earendil-works/pi/issues/11379) — Ctrl+Shift+C 会丢失正在输入的提示词（CLOSED, bug）**
    一个 1.3.0 回归，用户形容它“快把我气疯了”：从输出中复制会吞掉正在输入的提示词。一天内关闭，同时关闭的还有同类终端卫生问题 #11377（`stripAnsi` 泄漏 private-parameter CSI）和 #11205（Windows PowerShell 被鼠标/焦点报告刷屏）。

*本周期还关闭了：* #9265（`openai-completions` 流式处理中工具调用参数的 O(n²) 重复解析）和 #10642（嵌入式 SDK 会话内存从不释放）——两者都在未采取行动的情况下标记为 `CLOSED`，尽管它们描述了嵌入式/多会话宿主的具体生产阻塞。

---

## 4. 关键 PR 进展

1. **[#11368](https://github.com/earendil-works/pi/pull/11368) — 重构 provider 定义（OPEN）**
   用每个内置 provider 一个 `defineProvider()` 文件、共享模型家族规则（Claude、GPT、DeepSeek、Kimi、Google）以及 models.dev v2 API 适配器，替换 `generate-models.ts`。这是下方四个堆叠 PR 的基础。

2. **[#11369](https://github.com/earendil-works/pi/pull/11369) — Alibaba Model Studio + Z.AI 按量付费 provider（OPEN）**
   在 pi 已提供的套餐之外，新增 `alibaba`、`alibaba-cn`（DashScope 国际/中国）和 Z.AI PAYG。

3. **[#11370](https://github.com/earendil-works/pi/pull/11370) — DeepInfra、Nebius、Ollama Cloud、Scaleway、SiliconFlow、StepFun（OPEN）**
   根据 models.dev 贡献者活跃度和 issue tracker 需求选出的六个托管推理 provider；仅 DeepInfra 就贡献 74 个模型。

4. **[#11371](https://github.com/earendil-works/pi/pull/11371) — Google Vertex AI 上的 Claude 模型（OPEN）**
   `google-vertex` 现在通过 Vertex 的 Anthropic Messages API 列出 13 个未废弃的 Claude 模型，实现方式是复用 Anthropic Messages 代码并加一层 `fetch` 包装器。

5. **[#11372](https://github.com/earendil-works/pi/pull/11372) — 将非 Converse 的 Bedrock 模型路由到 Bedrock Mantle（OPEN）**
   针对 Bedrock API key 修复 #5363：列出的 16 个模型（GPT-5.4→GPT-6.1、gpt-oss、Grok 4.3/4.6、Gemma 4）只在 Mantle 这个 OpenAI 兼容端点上提供服务，之前被 Converse 以无效标识符拒绝。

6. **[#11355](https://github.com/earendil-works/pi/pull/11355) — 支持 Kimi Coding K3.1 Pro（CLOSED）**
   根据解析出的 effort 元数据推导 K3 wire preset，而不是依赖 model-ID 列表；K3.1 Pro/Highspeed 现在暴露 low/high/max，发送 `reasoning_effort`，保留推理内容，并将动态加载的工具排除在顶层工具列表之外。

7. **[#9329](https://github.com/earendil-works/pi/pull/9329) — 将 Orca 终端识别为支持 Kitty 图像（OPEN）**
   将 `TERM_PROGRAM=Orca` 视为支持 Kitty 内联图像、真彩色和 OSC 8 超链接，从而让图像组件不再回退为文本。

8. **[#11363](https://github.com/earendil-works/pi/pull/11363) — 用于 primary selection 的 `fullscreenCopyTarget` 设置（CLOSED）**
   让全屏下的选择即复制目标改为 X11/Wayland primary 缓冲区（中键粘贴），而不是剪贴板——与 #11379 中的复制/粘贴摩擦直接相关。

9. **[#10135](https://github.com/earendil-works/pi/pull/10135) — 规范化压缩用量，防止恢复时页脚崩溃（CLOSED）**
   不再在两个直通分支上持久化原始 provider `summaryUsage`，修复恢复已压缩会话时的崩溃。

10. **[#11341](https://github.com/earendil-works/pi/pull/11341) — 对不可用代理 URL 给出清晰错误（CLOSED）**
    undici 无法解析的 `HTTP_PROXY`/`HTTPS_PROXY` 值（例如 `socks5h://`、缺少 scheme、带路径的 URL）现在会以可读消息退出，而不是输出 undici `Invalid URL protocol` 堆栈跟踪。

---

## 5. 热门讨论

**展示与分享**
- **[#11306](https://github.com/earendil-works/pi/discussions/11306) — pi-continuity：在 coding-plan 用量限制和中断轮次后自动恢复**
  围绕 OpenAI 的 5 小时用量限制构建：该限制会中止一次运行，此前需要在数小时后手动“继续”。Pi 已经会重试瞬时 provider 错误，并在上下文溢出时压缩，但用量限制——一类不同的失败——此前没有处理。

**社区反馈**
- **[#11356](https://github.com/earendil-works/pi/discussions/11356) — 1.0 之前 vs 1.0 之后（rip）**
  作者认为 1.0 之后基线上下文消耗出现回退，“仅仅一个 Hello”就接近限制，且 Pi 在 prompt 开销上感觉更接近 opencode。只有 👍1，但它呼应了 #7730 和 #10642 中对上下文效率的担忧。

---

## 6. 功能请求趋势

- **Provider 广度与正确路由。** 按数量看最主要的主题：新的按量付费 provider（#11369）、托管推理端点（#11370）、Vertex 上的 Claude（#11371）、Bedrock 的 Mantle 路由（#11372），以及感知 effort 的模型预设（#11355）。方向是声明式的逐 provider 目录，而不是生成列表。
- **跨 provider 的多模态负载正确性。** 为 Bedrock/OpenAI 提升工具结果图片（#8643）、对 Codex 使用 `file_id` 引用而非重放 base64（#8617），以及保留 Gemini thought signatures（#10157），本质上都是同一个诉求：一个与 provider 无关、由适配器忠实转换的内容模型。
- **面向真实产品的扩展 API 接口面。** 用于宿主自有存储的 Code Mode 输出写入器（#11070）、`watchEvents` 从最后可见条目恢复（#11327）、确定性的 provider 默认值（#8810），以及支持 span 参数、summary-model 覆盖和 entry 引用的可组合压缩（#11310）。
- **长生命周期 / 嵌入式会话生命周期。** 持久执行语义（#11139、#11327）、压缩后的内存回收（#10642）和凭证刷新（#11221）指向服务器托管多会话的用例，而当前 SDK 尚未完全支持。
- **终端/UI 人体工学。** 更多终端支持 Kitty 图像（#9329）、primary-selection 复制目标（#11363）、IME 提交处理（#11177），以及干净地剥离转义序列（#11377）。

---

## 7. 开发者痛点

- **流式性能是长会话的首要阻塞项。** 参与度最高的三个 issue 中有两个（#6665、#7730）描述了流式输出时占满一个完整核心，根因追溯到每块重建 Markdown、未缓存字素分割，以及（此前的 #9265）二次方级工具参数重复解析。嵌入式宿主感受最深，因为事件循环被阻塞会拖住每一个共同托管的会话。
- **Provider 集成会在边缘场景的传输和负载形状上出问题。** 无错误但停滞的流（#4945）、跨 chunk 拆分的 CRLF（#10390）、丢失的 thought signatures（#10157）、被拒绝的嵌套图片（#8643）和过期的 AWS 凭证（#11221），都是“一直能用直到突然不能用”的失败，会在轮次中途暴露，且很难从 TUI 诊断。
- **扩展生态脆弱性。** 注册 provider、在 `session_start` 发送消息或会话中途重载的扩展，可能静默改变核心行为——丢失 Anthropic `system` 字段（#11157）、用 `tool_reference_unresolved` 破坏所有后续请求（#11376），或把会话切到错误的默认模型（#8810）。
- **终端兼容性回归反复出现。** 1.3.0 的发布带来一波 UI 卫生 bug：Ctrl+Shift+C 丢失提示词（#11379）、CSI 鼠标/焦点负载泄漏到 PowerShell（#11205）、`stripAnsi` 残留 private-parameter 序列（#11377），以及 Alacritty IME 自动补全失败（#11177）。
- **分诊吞吐量与贡献者意图。** 若干未采取行动即关闭的 issue（#9265、#10642、#11310、#10145、#8642）附带了原型、补丁或生产影响，且 #8643 明确指出此前的 PR 被贡献门禁自动关闭。再加上许多 `untriaged` 条目在提交当天就被关闭，感知到的评审门槛成为反复出现的摩擦来源。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区摘要 — 2026-10-25

---

## 1. 今日亮点

Qwen Code 发布了 **v0.26.1**，这是一个小型补丁版本，以一项破坏性修复为重点，恢复了 QQ 机器人频道的按群组会话隔离，同时 Desktop 构建中加入了 **daemon token 持久化**。CI 基础设施继续主导社区活动，围绕 runner 容量、main 推送失败上报以及 Managed Agent 门禁的一批 issue 占据了最多讨论。代码方面，安全加固（符号链接逃逸、只读文件原子写入）与成本核算正确性（推理 token 重复计数）今天吸引了多个相互竞争的 PR。

---

## 2. 发布

### v0.26.1 (`qwen-code`)
- **破坏性变更：** `fix(qqbot)!: restore per-group session isolation under thread scope`（[#13882](https://github.com/QwenLM/qwen-code/pull/13882), @Eric-GoodBoy-Tech）—— 修正了 `sessionScope: "chat_thread"` 行为，其中私信被错误地当作共享会话处理（另见已关闭 issue [#14350](https://github.com/QwenLM/qwen-code/issues/14350)）。
- 发布说明目前仍是占位内容（`qwen-release-notes:v1`）；完整变更列表推迟到链接的发布中。

### desktop-v0.26.1
- `feat(serve): add daemon token persistence`（[#15023](https://github.com/QwenLM/qwen-code/pull/15023), @wenshao）。
- `fix(release): cover every spawned vendored binary` —— 针对捆绑二进制的发布打包加固。

---

## 3. 热门 Issue

1. **[#13395](https://github.com/QwenLM/qwen-code/issues/13395) — tracking(runtime): Kubernetes 工具运行时进展与跨平台交付门禁**（23 条评论，进行中）
   这是仓库中运行时间最长的设计跟踪 issue。两个 S1 根因现已在 Draft [#15457](https://github.com/QwenLM/qwen-code/pull/15457) 中实现并在本地验证，但该 issue 仍保持打开，等待完整 CI 和原生 ACK 复测。评论数高反映了评审者在较长时期内持续严格的审查。

2. **[#15446](https://github.com/QwenLM/qwen-code/issues/15446) — Main CI 失败：Managed Agent 门禁，`HostedPublicWorkspaceIT.publicShellReceiptFaultsNeverRepeatEffects`（另有 +12 项）**（14 条评论）
   这是今天最大的单一 CI 失败集群。Java 托管工作区套件中反复出现的 fault-gate 失败正在拖慢合并速度，并在整个 issue tracker 中制造噪音。

3. **[#13113](https://github.com/QwenLM/qwen-code/issues/13113) — 会话无法打开：“Transcript snapshot is too large to index”**（9 条评论，P1）
   `file_history_snapshot` 在长会话中呈二次增长，最终超过 **硬编码的 256 MiB** 索引上限，使会话永久无法打开。这是一个明显接近数据丢失级别的严重问题，社区关注度很高。

4. **[#14859](https://github.com/QwenLM/qwen-code/issues/14859) — ci: 跟踪 runner 容量与独立 main 验证**（9 条评论）
   作者明确撤回早先的百分比估算，并用修正后的容量模型取而代之。这表明 CI runner 稀缺现在已成为项目的一等关切，而不再是一个孤立的烦扰。

5. **[#15056](https://github.com/QwenLM/qwen-code/issues/15056) — feat: 本地 `/autofix-pr`，用于监视 PR 并修复 CI 失败、评审反馈和冲突**（5 条评论）
   提出一个会话本地命令，轮询 PR，区分代码修复与基础设施重跑，并迭代直至变绿。鉴于上述 CI 失败量，这一提议引发强烈共鸣。

6. **[#15479](https://github.com/QwenLM/qwen-code/issues/15479) — 自动记忆绑定到启动目录，破坏多工作区会话**（4 条评论，新）
   多项目解决方案文件夹无法将记忆写入正确的按项目存储；所有内容都落到全局存储中。这直接影响任何在 monorepo 或解决方案文件夹中使用 Qwen Code 的人。

7. **[#10879](https://github.com/QwenLM/qwen-code/issues/10879) — hk4 被固定为发布主机，但仍带有共享的 `ecs-qwen` 标签**（4 条评论，P1）
   发布验证任务与普通 PR CI 在同一主机上竞争，导致对时序敏感的分片测试失败。这是一个长期存在（自 9 月 3 日起）的基础设施缺陷，根因明确。

8. **[#13895](https://github.com/QwenLM/qwen-code/issues/13895) — 当 429 响应体携带未识别的 `error.code` 时，`modelFallbacks` 链被跳过**（4 条评论）
   当提供商返回带有非标准 code 的限流错误时，回退链从不启用，因为它被归类为快速失败的客户端错误。对多提供商故障转移配置有很高的实际影响。

9. **[#15445](https://github.com/QwenLM/qwen-code/issues/15445) — Web Shell 在非英文浏览器中加载失败**（3 条评论，需要复测）
   `desktopRelay` 的 i18n 查找没有回退，对于英语/中文以外的任何语言环境都会抛出 `TypeError: Cannot read properties of undefined (reading 'desktopRelay.trigger')`。该问题在 0.26.0 中回归，并完全阻塞 UI —— 一个小代码缺口却造成巨大的用户影响。

10. **[#15470](https://github.com/QwenLM/qwen-code/issues/15470) — ci: main 推送失败未创建任何 issue；报告器将其视为已被取代而丢弃**（3 条评论）
    2026-10-24 14:00Z 至 17:30Z 之间发生了十次 main 推送失败，却产生了 **零** 个 issue，因为报告器等待缓慢的托管 runner，然后将其自己的报告视为已被取代。这削弱了 CI 可观测性。

*另值得关注：* [#15444](https://github.com/QwenLM/qwen-code/issues/15444)（成本显示中推理 token 被重复计数）、[#15418](https://github.com/QwenLM/qwen-code/issues/15418)（托管自动化扫描器在每次门禁探测时阻塞其单线程）、[#15461](https://github.com/QwenLM/qwen-code/issues/15461)（在空闲的 hz ARM64 池上配置发布主机）。

---

## 4. 关键 PR 进展

1. **[#15319](https://github.com/QwenLM/qwen-code/pull/15319) — feat(web-shell): 将无工作区会话移到工作区旁边**
   将无工作区会话提升为独立同级分组，位于 Workspaces 与 Archived 之间，并具有独立持久化的折叠状态。修复了折叠 Workspaces 时这些会话被完全隐藏的问题。

2. **[#15386](https://github.com/QwenLM/qwen-code/pull/15386) — fix(cli): 保留检查点恢复所有权与冷继续**
   通过真实工具调度器恢复 Ink 检查点，将重放结果与原始逻辑调用配对，并拒绝过期的所有者。这对检查点/回退的可靠性很重要。

3. **[#15030](https://github.com/QwenLM/qwen-code/pull/15030) — feat(cli): 提高工作区技能安装限制**
   将 ZIP、GitHub 和本地文件夹技能安装的传输上限与磁盘上限拆分，取代自 7 月以来一直采用的一套硬编码 6 MiB / 6 MiB / 每文件 2 MiB / 128 文件预算。

4. **[#15261](https://github.com/QwenLM/qwen-code/pull/15261) — feat(runtime-broker): 增加由 HTTP 支持的外部 scratch worker**
   新增一个可选的 `external-scratch` Runtime 提供程序，将会话 worker 的放置委托给由运营方运行的 HTTP 服务，并具有确定性的请求句柄、证明和重试语义。这是当前进行中的最重要的运行时扩展性变更。

5. **[#12585](https://github.com/QwenLM/qwen-code/pull/12585) — fix(acp): 持久化嵌入文本资源以用于 transcript 重放**
   将有界的原始 ACP 嵌入文本 `resource` 块与其所属用户提示一起持久化，并将其作为类型化用户块重放，在 daemon UI SDK 中与 `resource_link` 附件分开暴露。

6. **[#15453](https://github.com/QwenLM/qwen-code/pull/15453) — fix(core): 在原子写入中尊重只读文件和硬链接**
   原子替换现在在提交前检查目标可写性，并保留硬链接身份。这是对文件编辑工具的一次有意义的正确性/安全性修复。

7. **[#15430](https://github.com/QwenLM/qwen-code/pull/15430) — fix(core): 在记忆导入中拒绝符号链接逃逸**
   在包含性检查之前，对导入路径和允许的根目录都进行规范化，堵住了一个路径遍历漏洞：已提交的符号链接可能引入项目外部的文件。

8. **[#12222](https://github.com/QwenLM/qwen-code/pull/12222) — feat(core,cli): 为严格的 OpenAI 兼容服务器新增 `toolParametersMandatory` 可选启用项**
   启用后，零参数工具会发送显式的空 `"parameters": {"type":"object","properties":{}}`，而不是省略该字段。这直接为使用更严格的 OpenAI 兼容后端的用户扫清了障碍。

9. **[#15472](https://github.com/QwenLM/qwen-code/pull/15472) / [#15464](https://github.com/QwenLM/qwen-code/pull/15464) — fix: 停止在成本估算中重复计算推理 token**
   同一修复的两个独立实现（分别来自 issue 作者和一名维护者），它们从聚合总量推导可计费输出，而不是始终加上 `candidates + thoughts`。两者都保留了 Gemini 风格的不相交 `thoughts` 核算。

10. **[#15485](https://github.com/QwenLM/qwen-code/pull/15485) — fix(ci): 在发布步骤中重试瞬态 npm 发布**
    将递归 `npm publish` 最多重试四次，退避时间为 30/60/90 秒，并在每次尝试时重新校验注册表元数据。与 [#15471](https://github.com/QwenLM/qwen-code/pull/15471)（即使后续运行尚未稳定也记录 main-CI 失败）配合，直接针对今天的发布/CI 可靠性主题。

*另值得注意：* [#15433](https://github.com/QwenLM/qwen-code/pull/15433)（为 OTel 溯源保留 OpenAI `cache_write_tokens`）、[#15190](https://github.com/QwenLM/qwen-code/pull/15190)（当 Desktop 从 Finder/Dock 启动时，在 macOS 上扩展 daemon 的 `PATH`）、[#15274](https://github.com/QwenLM/qwen-code/pull/15274) / [#15286](https://github.com/QwenLM/qwen-code/pull/15286) / [#15275](https://github.com/QwenLM/qwen-code/pull/15275)（将自托管 SDK Java Maven 主机锁收窄到仅缓存回写）。

---

## 5. 热门讨论

_本期未提供讨论数据，因此省略本节。_

---

## 6. 功能请求趋势

- **代理内部的 CI/PR 自动化** —— `/autofix-pr`（[#15056](https://github.com/QwenLM/qwen-code/issues/15056)）加上托管自动化扫描器吞吐量工作（[#15418](https://github.com/QwenLM/qwen-code/issues/15418)）指向让 Qwen Code 接管完整 PR 生命周期：监视、诊断、修复、重试基础设施、解决冲突。
- **多工作区 / 多项目记忆** —— [#15479](https://github.com/QwenLM/qwen-code/issues/15479) 请求按 git 根目录键控的记忆存储，使跨多个项目的解决方案文件夹不再污染全局存储。
- **运行时与沙箱可移植性** —— Kubernetes 工具运行时跟踪器（[#13395](https://github.com/QwenLM/qwen-code/issues/13395)）和外部 scratch worker（[#15261](https://github.com/QwenLM/qwen-code/pull/15261)）反映了对可插拔、由运营方托管的执行后端的需求。
- **OpenAI 兼容提供商兼容性** —— 严格的工具 schema 处理（[#12222](https://github.com/QwenLM/qwen-code/pull/12222)）以及为回退链正确分类 429（[#13895](https://github.com/QwenLM/qwen-code/issues/13895)）是运行非 Qwen 端点的用户反复提出的需求。
- **成本与 token 透明度** —— 推理 token 核算（[#15444](https://github.com/QwenLM/qwen-code/issues/15444)）和 OpenAI 缓存写入 token 溯源（[#15433](https://github.com/QwenLM/qwen-code/pull/15433)）表明对准确计费/遥测的兴趣日益增长。
- **Desktop/daemon 打磨** —— daemon token 持久化（已发布）、macOS `PATH` 继承（[#15190](https://github.com/QwenLM/qwen-code/pull/15190)）以及 Web Shell i18n 回退（[#15445](https://github.com/QwenLM/qwen-code/issues/15445)）。

---

## 7. 开发者痛点

- **CI 容量与可靠性是最大的摩擦点。** Runner 争用（[#14859](https://github.com/QwenLM/qwen-code/issues/14859)、[#10879](https://github.com/QwenLM/qwen-code/issues/10879)、[#15461](https://github.com/QwenLM/qwen-code/issues/15461)）、嘈杂的 Managed Agent 门禁失败（[#15446](https://github.com/QwenLM/qwen-code/issues/15446)、[#14724](https://github.com/QwenLM/qwen-code/issues/14724)、[#14784](https://github.com/QwenLM/qwen-code/issues/14784)），以及一个静默地不创建任何 issue 的失败报告器（[#15470](https://github.com/QwenLM/qwen-code/issues/15470)），共同导致反馈循环缓慢和回归未被跟踪。本周期多个修复专门针对 Maven 主机锁范围和发布重试。
- **会话扩展限制。** 硬编码的 256 MiB transcript 索引上限加上快照二次增长（[#13113](https://github.com/QwenLM/qwen-code/issues/13113)）可能永久性地毁掉一个长时间运行的会话 —— 一种没有恢复路径的硬失败模式。
- **静默失败分类。** 看起来像限流但带有厂商特定 code 的错误被当作永久性客户端错误处理，从而绕过回退（[#13895](https://github.com/QwenLM/qwen-code/issues/13895)）。用户只有在任务运行中途死亡后才会发现这一点。
- **成本报告不准确。** 对 OpenAI 风格提供商重复计算推理 token（[#15444](https://github.com/QwenLM/qwen-code/issues/15444)）会侵蚀对成本显示的信任。
- **完全阻塞使用的最后一公里 UX 缺口。** 缺少 i18n 回退使 Web Shell 对英语/中文以外的所有语言环境都不可用（[#15445](https://github.com/QwenLM/qwen-code/issues/15445)）—— 代码上很小，影响上却是彻底的。
- **延期评审债务。** 一种反复出现的模式：承载非阻塞评审建议的“后续”issue（[#14973](https://github.com/QwenLM/qwen-code/issues/14973)、[#13698](https://github.com/QwenLM/qwen-code/issues/13698)、[#14625](https://github.com/QwenLM/qwen-code/issues/14625)、[#12986](https://github.com/QwenLM/qwen-code/issues/12986)）表明评审吞吐量超过了合并与清理能力。

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*