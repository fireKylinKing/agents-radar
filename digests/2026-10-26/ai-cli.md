# AI CLI 工具社区动态日报 2026-10-26

> 生成时间: 2026-10-26 00:37 UTC | 覆盖工具: 7 个

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

# 跨工具对比报告 — AI CLI 工具，2026-10-26

## 1. 生态概览

AI CLI 生态正处于生产环境加固阶段：过去 24 小时内大多数工具未发布稳定版，issue 流量主要集中于可靠性、配额/计费、权限、MCP/插件生命周期和跨平台正确性，而非全新功能。PR 速度集中在 Gemini CLI、OpenCode、Pi 和 Qwen Code，而 Claude Code、Codex 和 Copilot CLI 表现出更高的 issue 参与度，但本窗口内 PR 吞吐量很少或没有。共同竞争前线不再是原始模型访问，而是信任：可预测成本、可审计的 agent 行为、长会话稳定性，以及无头/协议一致性。社区数据还显示，对细粒度安全控制和可操作诊断的需求日益增长，而非一刀切阻断或静默失败。

## 2. 活跃度对比

> 计数为摘要中披露的 24 小时“updated”计数，并非仓库总规模。**N/A** 表示在提供的快照中未披露或不适用；并不意味着不活跃。

| 工具 | Issue 更新数 | PR 更新数 | 讨论数 | 发布状态 |
|---|---:|---:|---:|---|
| **Claude Code** | 50（展示前 30 条） | 1（仅文档，已关闭） | N/A | 无发布 |
| **OpenAI Codex** | 50 | 0 | 8 | 无发布或 PR 活动 |
| **Gemini CLI** | N/A（剖析 10 个热点 issue） | 剖析 10+ 个 | N/A | Nightly：`v0.67.0-nightly.20261025` |
| **GitHub Copilot CLI** | 24 | 0 | N/A | 无发布 |
| **OpenCode** | N/A（剖析 10 个热点 issue；>12 个配额相关报告） | 剖析 10+ 个 | N/A | 无发布 |
| **Pi** | 50 | 9 | 1 | 无发布 |
| **Qwen Code** | N/A（剖析 10 个热点 issue） | 剖析 10+ 个 | N/A | Nightly：`v0.26.1-nightly.20261025`；稳定版 `v0.26.1` 已发布，但 SDK/托管运行时分发受阻 |

## 3. 共同功能方向

- **成本、配额与速率限制透明度。** Claude Code 存在无头与交互式成本差异，以及每周限额耗尽担忧；Codex 用户请求非 TUI 的用量检查与更可持续的 Plus 用量；Copilot CLI 请求 ACP `usage_update` 和机器可读的 prompt 预算错误；OpenCode 发生重大免费配额重置事件；Pi 报告静默缓存预热失败。共同需求是在支出发生前提供可预测、可审计的计费。

- **沙箱、审批与权限清晰度。** Claude Code 用户要求在测试环境中提供权限门控的密码输入；Codex 报告委派任务忽略用户沙箱默认值，且细粒度策略在未提示的情况下拒绝操作；Gemini 提议 OS 级沙箱和更安全的破坏性命令行为；OpenCode 请求工作区锁和自动允许只读工具；Pi 请求为 MCP stdio 服务器提供 `inheritEnv: false`。用户想要的是范围明确、可执行的管控——而非一刀切阻断。

- **MCP/插件生命周期加固。** Codex 需要重连/刷新、OAuth 持久化和工具重新发现；Copilot CLI 报告自定义 agent 禁用延迟 MCP 加载，以及 Entra broker MCP 认证失败；Pi 报告传输不一致的消息限制、OAuth 挂起和环境变量继承；OpenCode 正在统一 GUI/TUI/server 插件面；Gemini 存在扩展画廊发现 issue；Qwen Code 需要将 MCP 子进程 stderr 纳入诊断。共同要求是一个可靠、可观测的插件运行时。

- **静默失败与可观测性。** Gemini 的热点 issue 包括将 `MAX_TURNS` 报告为 `GOAL` 成功，以及通用 agent 挂起；OpenCode 子 agent 向父级返回空结果；Copilot `/rename` 报告成功但未重命名；Pi 缓存预热和 MCP OAuth 静默失败；Qwen 循环防护仅为建议性，且重试原因不透明；Claude 报告命令静默损坏和跨会话消息丢失。社区日益将诊断视为产品界面，而非遥测。

- **跨平台可靠性。** Windows 是 Claude Code、Codex、Gemini、Copilot CLI、OpenCode 和 Pi 上最大的回归面：路径分隔符、MSIX 安装、VS Code 启动、Entra 认证、页大小假设以及 shell/管道兼容性。macOS 和 Linux 也暴露出 Wayland 浏览器失败、tmux OSC 52 处理、iTerm2 图像渲染和 ARM64 打包问题。跨平台正确性如今已是基本要求。

- **长会话与 agent 运行时可靠性。** Claude Code 报告 Remote Control 断连和跨会话消息丢失；Codex 报告回合过早完成和容量错误；Gemini 报告无限挂起；Copilot CLI 报告会话锁超时和 37 GiB 内存增长；OpenCode 报告子 agent 数据丢失；Pi 报告扩展重载污染 Anthropic 会话；Qwen Code 报告压缩 token 重复计数和循环防护缺口。多小时自主使用正在暴露运行时脆弱性。

- **上下文、记忆与 prompt 效率。** Claude Code 用户想要持久记忆和可强制执行的上下文注入；Gemini 正在评估 AST 感知读取/搜索和基于文件的任务状态；Codex 用户想要 AGENTS.md 诊断；OpenCode 正在优化长会话历史加载和媒体 blob；Pi 报告压缩文件列表增长和缓存预热缺陷；Qwen Code 提议阈值触发式交接至新上下文。前沿是确定性的上下文工程，而不仅是更大的窗口。

- **无头/ACP/CI 一致性。** Copilot CLI 的 ACP 一致性需求最明确；Claude Code 的无头成本差距对 CI 很重要；Codex 用户想要非交互式限额检查；Qwen Code 需要更好的无头重试分类。协议消费方日益期待类型化错误、用量上报，以及与交互模式的一致性。

## 4. 差异化分析

- **Claude Code** 面向使用桌面/TUI 工作流的专业开发者和团队。其重点是安全护栏、shell 环境保真、插件/技能编写以及配额透明度。社区在权限和成本核算方面高度活跃，但本快照中 PR 吞吐量较低。

- **OpenAI Codex** 拥有最广泛的桌面/IDE 覆盖面，并最侧重 Windows/Computer Use。其技术方向强调沙箱/审批策略、MCP/插件生命周期以及远程/跨设备工作流。社区规模大且由 issue 驱动，存在严重的平台特定回归，并对速率限制可见性有强烈需求。

- **Gemini CLI** 在 agent 子系统加固方面迭代最快：子 agent 可观测性、取消、远程会话隔离、AST 感知上下文和 OS 沙箱。它面向 Gemini 生态开发者和扩展作者，采用 nightly 发布，并有大量精准的正确性 PR。

- **GitHub Copilot CLI** 以企业认证、托管策略和 ACP/无头集成为差异化。其目标用户是以 GitHub 为中心的团队和 Zed 等 ACP 客户端。主要风险是发布质量：长会话内存/会话锁回归和 Entra 认证脆弱性正在削弱对快速发布节奏的信任。

- **OpenCode** 是开源、供应商无关的桌面/TUI 工具，并提供免费层。它面向个人开发者和自托管者，重度投入 V2 性能工作、插件面统一以及媒体/附件优化。免费配额事件目前是其社区和信任方面的主导问题。

- **Pi** 以终端优先，面向扩展/MCP，目标用户是高级用户和扩展作者。其工程风格是小而精准的修复：Windows 路径、UTF-8 分块、符号链接的 agent 目录、provider-id 解析以及 MCP 传输一致性。它的分诊速度快，但也存在可见的重复项/以“无需处理”关闭带来的摩擦。

- **Qwen Code** 最偏向企业运行时：托管会话、Kubernetes harness、持久日志/快照、web-shell 嵌入和发布自动化。其目标是托管/运维级部署和嵌入方。薄弱环节是发布分发和 CI 可观测性：稳定版 `v0.26.1` 已发布，但 SDK 与托管运行时发布受阻。

## 5. 社区势头与成熟度

**势头：** Gemini CLI、OpenCode、Pi 和 Qwen Code 展现出最强的可见迭代速度，各剖析了 9–10+ 个 PR，并有活跃的加固工作。Gemini 和 Qwen 正在发布 nightly；OpenCode 正深入 V2 性能和插件架构；Pi 正以高吞吐量关闭小型正确性修复。

**参与度：** Claude Code 和 Codex 显示出最高的问题参与信号，包括高评论数和点赞聚集（例如 Claude 的 shell 可配置性请求获 131 👍；Codex 的 VS Code 启动回归获 14 👍）。Copilot CLI 的 issue 量较低，但回归严重程度不成比例：长会话中的会话锁超时和 37 GiB 内存增长。

**成熟度：** Claude Code、Codex 和 Copilot CLI 是最成熟的发行面，但都在配额经济性、平台回归和长会话稳定性方面存在生产信任缺口。Gemini CLI 和 Qwen Code 正在快速成熟，但仍表现出发布/CI 脆弱性。OpenCode 和 Pi 的行为像快速迭代的开源项目：贡献者活跃、修复迅速，且分诊噪声可见。

**注意事项：** 所披露的指标未归一化。Claude、Codex 和 Pi 报告 50 个更新 issue；Copilot 报告 24 个；Gemini、OpenCode 和 Qwen 未披露更新 issue 总数。对比应解读为方向性，而非精确市场份额指标。

## 6. 趋势信号

- **信任是新的功能战场。** 配额核算、权限行为和静默失败如今比新能力引发更多社区热度。开发者需要在执行前预测成本和 agent 行为。

- **无头与协议优先的使用正在增长。** ACP、JSON-RPC、SDK 和 CI 管道是一等消费方。将无头模式视为二等公民的工具将累积集成摩擦。

- **安全模型正从阻断转向范围化覆盖。** 用户想要权限门控的逃生通道、沙箱化执行、按工作区策略和 MCP 环境隔离——而不是移除护栏。

- **Windows 是最大的质量缺口。** 安装、IDE 启动、shell 解析、认证和 Computer Use 失败在各供应商处反复出现。跨平台正确性是竞争需求，而非打磨项。

- **MCP/插件生态正成熟为生命周期问题。** 重连、OAuth 取消、工具重新发现、传输一致的限制以及插件面统一是下一层基础设施。

- **上下文工程正在取代原始上下文大小。** AST 感知读取、压缩正确性、缓存预热、prompt 可调试性和新上下文交接是活跃设计领域。

- **发布工程是瓶颈。** 六小时 SDK 测试、nightly 分发失败和快速节奏回归正推动对稳定通道和更好发布可观测性的需求。

- **诊断必须面向用户。** 机器可读错误码、可操作的 MCP stderr、重试原因以及明确的成功/失败信号，正日益成为开发者体验的一部分。

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

**数据说明：** 所提供摘录显示 PR 评论计数为 `undefined`，所有 PR 的 👍 均为 `0`。因此，下文的 PR 排名使用关注度代理指标——关联 Issue 的讨论量、时效性和严重程度——而非经核实的 PR 评论数。所有列出的 PR 均为 **OPEN**；未标明 merged/draft 状态。

## 1. 热门 Skills 排名

| 排名 | Skill / PR | 功能 | 讨论要点 | 状态 |
|---|---|---|---|---|
| 1 | [skill-creator — PR #1298](https://github.com/anthropics/skills/pull/1298) | 用于创建和评估 Claude Skills 的元 Skill | 触发评估漏报、Windows 子进程管道处理、运行时失败；关联活跃 Issue [#556](https://github.com/anthropics/skills/issues/556)（13 条评论）、[#202](https://github.com/anthropics/skills/issues/202)（8 条）、[#1394](https://github.com/anthropics/skills/issues/1394)（4 条）。同类修复：#1681、#1997。 | OPEN |
| 2 | [mcp-builder — PR #1742](https://github.com/anthropics/skills/pull/1742) | 构建并评估 MCP 服务器；修复 `mcp>=2` 导入和自定义请求头 | `mcp>=2` 中 `streamablehttp_client` 重命名，通过 `create_mcp_http_client` 配置请求头；相关评估失败见 [#1390](https://github.com/anthropics/skills/issues/1390)（4 条评论）。 | OPEN |
| 3 | [docx — PR #1792](https://github.com/anthropics/skills/pull/1792) | Word 文档处理和修订接受 | LibreOffice 超时曾被报告为成功；现在会验证输出不含修订标记。相关 docx 完整性问题 [#2018](https://github.com/anthropics/skills/issues/2018)（4 条评论）；孤立批注 PR #1734。 | OPEN |
| 4 | [webapp-testing — PR #1980](https://github.com/anthropics/skills/pull/1980) | 浏览器/Web 应用测试脚本与服务器编排 | 从 `with_server.py` 中移除 `shell=True`，以缓解命令注入（CWE-78）；最近一次以安全为重点的更新在 2026-10-25。配套修复 #1976 针对元素发现。 | OPEN |
| 5 | [claude-api — PR #1730](https://github.com/anthropics/skills/pull/1730) | Claude API 指南、工具使用概念和 Academy 参考资料 | 替换三个失效的文档 URL；相关上下文窗口 Issue [#1487](https://github.com/anthropics/skills/issues/1487)（4 条评论）报告 `claude-api` 注入约 156k tokens。 | OPEN |
| 6 | [notion-spec-to-implementation + quantitative-resume-auditor — PR #1245](https://github.com/anthropics/skills/pull/1245) | 将产品/技术规格转换为 Notion 实施任务；审计量化简历 | 多 Skill 提交；于 2026-10-15 有活跃更新。体现对领域工作流自动化和文档输出审查的需求。 | OPEN |
| 7 | [proofcore-contract-auditor — PR #1771](https://github.com/anthropics/skills/pull/1771) | 面向 Solidity/Rust 合约的静态分析，以及 TON 区块链审计证明锚定 | 新的 Web3/安全导向 Skill；领域较窄，但属于高风险用例。 | OPEN |
| 8 | [pyxel — PR #525](https://github.com/anthropics/skills/pull/525) | 使用 Python 开发复古游戏，支持无头输入驱动运行和帧检查 | 长期运行的 PR，最近更新于 2026-10-23；表明对可验证的创意/游戏开发 Skills 有持续兴趣。 | OPEN |

## 2. 社区需求趋势

- **安全、来源与治理：** 评论最多的 Issue 是 [#492](https://github.com/anthropics/skills/issues/492)（46 条评论），涉及社区 Skills 冒充 `anthropic/` 命名空间。相关需求还出现在 [#412](https://github.com/anthropics/skills/issues/412)（Agent 治理）、[#1394](https://github.com/anthropics/skills/issues/1394)（eval-viewer XSS）和 [#1487](https://github.com/anthropics/skills/issues/1487)（上下文耗尽）中。预期的 Skill 方向：安全分析器、来源检查、权限范围限定和安全分发。
- **可靠的 Skill 评估与触发测试：** [#556](https://github.com/anthropics/skills/issues/556)（13 条评论）报告 `run_eval.py` 从不触发 Skills；[#202](https://github.com/anthropics/skills/issues/202)（8 条）批评 `skill-creator` 最佳实践；[#1390](https://github.com/anthropics/skills/issues/1390)（4 条）报告 MCP 评估得分为 0/N；[#2064](https://github.com/anthropics/skills/issues/2064)（4 条）指出“when to use”元数据薄弱。需求：评估框架、触发校准、描述 lint 和质量门禁。
- **组织级共享与分发：** [#228](https://github.com/anthropics/skills/issues/228)（17 条评论，8 👍）请求提供共享 Skill 库和直接共享链接，而不是手动上传 `.skill`。需求：团队级 Skill 注册表和可安装的共享集合。
- **上下文与内存效率：** [#1329](https://github.com/anthropics/skills/issues/1329)（10 条评论）提出 `compact-memory` 符号表示法；[#1487](https://github.com/anthropics/skills/issues/1487)（4 条）表明单个 Skill 可消耗约 156k tokens。需求：惰性加载、token 预算和紧凑的持久状态。
- **文档/XML 完整性与 Office 自动化：** [#2018](https://github.com/anthropics/skills/issues/2018)（4 条评论）报告 `docx` 合并输出包含无效 XML；[#1868](https://github.com/anthropics/skills/issues/1868)（4 条）报告渐变插值 bug。需求：稳健的 docx/ODT/PDF 校验器和幂等文档转换。
- **推理与 Agent 质量流水线：** [#1385](https://github.com/anthropics/skills/issues/1385)（6 条评论）提出任务前校准、对抗性审查和交付验证。需求：工作流级质量门禁，而非孤立的内容 Skills。

## 3. 高潜力待定 Skills

这些 Skills 最近有更新、范围较窄，或明确关联未关闭 Issue——表明它们可能比宽泛的新 Skill 提交更早落地。

- [PR #1980 — webapp-testing: remove `shell=True`](https://github.com/anthropics/skills/pull/1980) — 安全修复，具有清晰的 CWE-78 理由；更新于 2026-10-25。**OPEN**
- [PR #1681 — skill-creator: direct `package_skill.py` execution](https://github.com/anthropics/skills/pull/1681) — 修复 `ModuleNotFoundError` 和过时的用法路径；更新于 2026-10-24。**OPEN**
- [PR #1997 — skill-creator: YAML frontmatter scalar parsing](https://github.com/anthropics/skills/pull/1997) — 范围很窄的解析器正确性修复；更新于 2026-10-19。**OPEN**
- [PR #1977 — algorithmic-art: `wrapAround()` correctness](https://github.com/anthropics/skills/pull/1977) — 小型 bug 修复，修复 #1897；更新于 2026-10-18。**OPEN**
- [PR #1976 — webapp-testing: textarea/select element discovery](https://github.com/anthropics/skills/pull/1976) — 针对性 bug 修复，修复 #1891；更新于 2026-10-18。**OPEN**
- [PR #1245 — notion-spec-to-implementation + quantitative-resume-auditor](https://github.com/anthropics/skills/pull/1245) — 活跃的多 Skill 提交；更新于 2026-10-15。**OPEN**
- [PR #1742 — mcp-builder: `mcp>=2` import and headers](https://github.com/anthropics/skills/pull/1742) — 针对特定上游 API 变更的兼容性修复；更新于 2026-10-08。**OPEN**

## 4. Skills 生态洞察

社区最集中的需求并不是更多领域特定的 Skills，而是**可信、可评估、可共享且上下文高效的 Skill 基础设施**——尤其是安全/来源、可靠的触发评估、组织级分发和 token 感知加载。

---

# Claude Code 社区摘要 — 2026-10-26

**来源：** [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code) · 0 个版本发布 · 50 个 issue 有更新（按评论数显示前 30 个） · 1 个 PR 有更新

---

## 1. 今日亮点

过去 24 小时内没有发布任何版本，因此社区关注点仍集中在成本核算和平台级工具链 bug 上。两个与成本相关的讨论串——无头模式 `claude -p` 每 token 消耗的 5 小时窗口额度约为交互模式的 1.8 倍（#97074，50 👍），以及疑似每周限额消耗回归（#102667）——正汇聚成更广泛的“配额透明度”关切。与此同时，长期存在的 shell 可配置性请求（#7490，131 👍）仍是本批中获赞最多的开放增强项，而已关闭的桌面端终端字体请求（#48805，63 👍）表明 UI 打磨类请求确实会得到解决。

---

## 2. 发布

过去 24 小时内无。

---

## 3. 热门 Issue

1. **[#7490](https://github.com/anthropics/claude-code/issues/7490) — 允许配置 Bash 工具使用哪个 shell** · OPEN · 40 条评论 · 131 👍
   本时间窗口内互动量最高的条目。用户希望继承其初始 shell（或显式选择一个），而不是让 Bash 工具强加自己的 shell。高赞数加上 `has repro` 标签，使其成为近期路线图关注的最强候选。

2. **[#97074](https://github.com/anthropics/claude-code/issues/97074) — 无头模式 `claude -p`（sdk-cli）每 token 消耗的 5 小时窗口额度约为交互式 `cli` 的 1.8 倍** · OPEN · 29 条评论 · 50 👍
   同一工作负载在不同入口之间存在可复现的成本差异。这件事很重要，因为无头模式正是 CI 和自动化流水线使用的模式，所以这种额外开销会在各团队中无声地放大。评论量很大，表明多名用户正在独立确认这一比例。

3. **[#78160](https://github.com/anthropics/claude-code/issues/78160) — 硬性阻止输入密码破坏了合法的开发/测试工作流** · OPEN · 18 条评论 · 25 👍
   Claude Code 即便面对 localhost 上已有文档记录的测试账号，也拒绝输入凭据。请求方希望的是由权限门控的可选开启，而不是移除安全护栏——这是一种安全与易用性之间的微妙权衡，社区正在讨论，而不是简单地反对。

4. **[#102667](https://github.com/anthropics/claude-code/issues/102667) — 可能存在用量核算回归：每周限额消耗得快得多** · OPEN · 5 条评论 · 2 👍
   虽有轶事性质但情况一致：过去一个 5 小时窗口约消耗每周配额的 10%，现在消耗明显更多。与 #97074 结合来看，这是今日数据中最强的信号，表明计费/配额核算是当前最大的信任问题。

5. **[#100063](https://github.com/anthropics/claude-code/issues/100063) — Windows：Bash 工具在 Git Bash 解析之前把每一对反斜杠减半** · OPEN · 4 条评论 · 2 👍 · `has repro`
   影响 heredoc 和单引号字符串；PowerShell 工具不受影响。这是一个正确性 bug，会静默损坏命令而不是大声失败——对智能体工具来说是最糟糕的一类 bug。

6. **[#104907](https://github.com/anthropics/claude-code/issues/104907) — VS Code：在 `/btw` 面板中 Cmd+C 无反应，会话名称字段中 Cmd+V 失效** · OPEN · 3 条评论
   菜单栏的编辑操作仍然有效，这说明问题指向快捷键拦截，而不是剪贴板损坏。这是新报告（10-24 提交），针对扩展 2.1.309，已经开始获得确认。

7. **[#104410](https://github.com/anthropics/claude-code/issues/104410) — Remote Control 会话掉线且不会自行恢复；跨会话消息静默丢失** · OPEN · 2 条评论
   在 Windows、macOS 和 WSL 上均有报告。由于跨会话消息传递依赖 Remote Control，静默消息丢失使多会话设置作为骨干不可靠——这是可靠性问题，而不是 UI 小毛病。

8. **[#104283](https://github.com/anthropics/claude-code/issues/104283) — 桌面端 Code 标签页：允许用户取消固定任务计划区块** · OPEN · 2 条评论 · 2 👍
   固定的待办区块位于屏幕中部，内容会从其后方滚过，在较小显示器上很痛苦。这是一个范围明确的小型 UX 请求，且有清晰的复现步骤。

9. **[#104760](https://github.com/anthropics/claude-code/issues/104760) — 陈旧的临时租约会钉住孤立插件版本** · OPEN · 1 条评论 · `has repro`
   死进程留下的租约文件会阻止孤立项清理，因此旧插件版本持续存在。作者仔细将其与 #95420 区分开来——这对插件领域的分诊规范很有用。

10. **[#48805](https://github.com/anthropics/claude-code/issues/48805) — 桌面应用中的终端字体族设置** · **CLOSED** · 14 条评论 · 63 👍
    本批中获赞数第二高，现已关闭。值得注意的是，它证明积累的 UI 自定义请求最终确实会落地，在分诊上文较新的桌面端请求时值得引用。

---

## 4. 关键 PR 进展

过去 24 小时内只有 **一个** pull request 有更新，因此本轮没有有意义的 PR 流水线可供排名。

1. **[#29757](https://github.com/anthropics/claude-code/pull/29757) — docs(plugin-dev): 澄清技能开发指南中的技能命名** · CLOSED · 作者：webwww123
   仅文档变更，针对插件开发者指南：frontmatter 示例现在使用小写连字符技能名，一条元数据说明指出技能名应匹配 kebab-case 目录标识符，并新增了一项评审者检查清单条目以捕捉不匹配。关闭 #24556。风险低，提升第三方技能编写的正确性——在本时间窗口内被关闭而非合并。

---

## 5. 热门讨论

本时间段未提供讨论数据；本节省略。

---

## 6. 功能请求趋势

从 30 个可见 issue 中提炼：

- **环境与 shell 控制** — Bash 工具的可配置/可继承 shell（#7490）是旗舰项；底层主题是用户希望 Claude Code 在他们*自己*配置的环境中运行，而不是在一个规范化环境中运行。
- **成本与配额可观测性** — 两个独立讨论串（#97074、#102667）实际上都在要求同一件事：可预测、可审计的核算，让团队能在支出发生前进行评估。
- **细粒度权限而非一刀切规则** — 自有开发环境中的密码输入（#78160），以及在因 stale 关闭的报告中浮现的安全分类器误报（#89961、#89957、#89951），都要求有作用域、需选择加入的覆盖，而不是全局阻止。
- **桌面端/TUI 人体工学** — 取消固定任务计划区块（#104283）、终端字体族（#48805）以及智能体视图可供性（#84075、#82282、#82052）构成了一连串稳定的小型打磨请求。
- **真正持久化的记忆** — 自动记忆可靠性（#75334，以 stale 关闭）以及针对记忆主题文件的规则式触发注入（#78795）表明用户渴望确定性、可检查的上下文注入。
- **智能体可观测性** — 显示后台智能体运行在哪个模型上（#82282）以及为会话历史加时间戳（#84075），反映出后台智能体正在成为生产基础设施。

---

## 7. 开发者痛点

- **配额成本机制显得不透明，且可能已经回归。** 可测量的无头模式与交互模式成本差距，加上用户感知到的每周额度消耗更快，是本时间窗口内最高严重度的信任问题。
- **平台特定集成 bug 持续存在。** Bash 工具中的 Windows 反斜杠错乱（#100063）、VS Code 扩展中失效的 Cmd+C/Cmd+V（#104907），以及 GNOME Wayland Quick Entry 从不打开（#104069），每一项都会破坏特定平台上的核心交互。
- **安全护栏在合法工作上产生误报。** 为自有测试环境输入密码、智能家居调试、气隙隔离实验室的系统管理，以及名为 `*-production` 的只读 MCP 服务器，都被阻止——用户要求的是权限门控的逃生通道，而不是移除防护。
- **静默失败多于显式失败。** 反斜杠减半、跨会话消息丢失和过期插件租约都在没有清晰错误界面的情况下失败，这比硬失败更快侵蚀信任。
- **分诊积压在数据中可见。** 大量有更新的 issue 是 `stale`/`CLOSED`，其中一些带有真实信号（#84981 的 30 分钟后台任务 SIGTERM，#75334 的无效自动记忆）。用户会把因 stale 关闭解读为“没人处理”。
- **记忆与上下文行为仍然难以预测。** 有报告称“记忆被写入但未被实际采用”——开发者想要可强制执行的约束，而不是尽力而为的召回。

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

## OpenAI Codex 社区摘要 — 2026-10-26

### 今日亮点
过去 24 小时内没有新版本发布或 PR 合入；活动主要集中在 issue 上，共有 50 个 issue 更新和 8 个讨论。Windows 和 Computer Use 的可靠性问题占据主导，其中以 [#51524](https://github.com/openai/codex/issues/51524) 浏览器 URL 检测、[#56691](https://github.com/openai/codex/issues/56691) VS Code 扩展启动失败和 [#53132](https://github.com/openai/codex/issues/53132) MSIX 安装失败为首。反复出现的主题包括沙箱/审批不匹配、MCP/插件生命周期脆弱性以及限流透明度问题；讨论区还指出了捆绑市场暂存目录的磁盘泄漏问题。

### 热门 Issue
- [#51524](https://github.com/openai/codex/issues/51524) — **[Windows] Computer Use 在 `get_window_state` 处停止：无法确定当前浏览器 URL**（39 条评论，3 👍）。阻断了 Windows 上的核心浏览器自动化；`get_window_state` 在点击或输入之前就终止了当前轮次。是所提供数据集中评论数最多的 issue。
- [#56691](https://github.com/openai/codex/issues/56691) — **Codex 26.1021.22027 在 Windows 上的 VS Code 1.143.1 中无法启动 UI**（30 条评论，14 👍）。高影响的 IDE 扩展回归问题；是数据集中点赞信号最强的 issue。
- [#53132](https://github.com/openai/codex/issues/53132) — **Windows 11 MSIX 安装反复失败，报错 `0x80073CF6` / `0x80070005`**（15 条评论）。与过期打包服务注册相关的安装/升级阻塞问题。
- [#50737](https://github.com/openai/codex/issues/50737) — **新建的委托任务使用 `workspace-write`/`auto_review`，无视用户沙箱默认设置**（12 条评论，3 👍）。安全与控制方面的顾虑：用户无法判断哪个设置真正管控委托任务的执行。
- [#50728](https://github.com/openai/codex/issues/50728) — **macOS Computer Use 输入/粘贴重复；Claude 键盘敲击报告 16–56 秒延迟**（11 条评论）。干扰手动工作流，削弱对 Computer Use 的信任。
- [#54956](https://github.com/openai/codex/issues/54956) — **提升 Codex Plus 在所有模型上的使用效率，并支持低成本实验**（10 条评论）。反映了对限流经济性和模型实验成本的持续关注。
- [#53177](https://github.com/openai/codex/issues/53177) — **ChatGPT Work Cloud `list_threads` 在本地执行环境下暴露但调用失败**（9 条评论）。云/本地执行边界问题，导致 Work Cloud 中的线程列表功能失效。
- [#34946](https://github.com/openai/codex/issues/34946) — **允许定时任务以 SSH 连接的主机为目标，并在控制应用中展示运行情况**（8 条评论，11 👍）。对跨机器自动化和远程任务编排的强烈支持。
- [#56874](https://github.com/openai/codex/issues/56874) — **`write_stdin` 被细粒度沙箱审批策略拒绝，阻断 Sites 发布**（4 条评论）。审批策略缺陷，不弹出审批提示，阻断发布工作流。
- [#56951](https://github.com/openai/codex/issues/56951) — **Codex 在审查/检查点里程碑后结束轮次，同时承认工作尚未完成**（4 条评论）。影响长时间运行的 CLI 任务的模型/会话可靠性问题。

### 关键 PR 进展
过去 24 小时内没有拉取请求更新（0 项），因此没有 PR 进展可总结。

### 热门讨论

**综合**
- [#42049](https://github.com/openai/codex/discussions/42049) — **Codex 捆绑市场刷新泄漏暂存目录，可能占用数十 GB 空间**（3 条评论，4 👍）。报告称在 macOS 上有 323 个被遗弃的暂存目录，占用约 31 GiB；运维影响显著。
- [#56932](https://github.com/openai/codex/discussions/56932) — **在 TUI 之外追踪 5 小时/每周 Codex 用量**（0 条评论，1 👍）。使用 `account/rateLimits/read` 和 rollout `token_count` 历史的变通方案，与反复出现的限额骤降问题相关。

**问答**
- [#52835](https://github.com/openai/codex/discussions/52835) — **AI 辅助 KPI 管理系统：开发 4 个月仍无可用的应用**（5 条评论，1 👍）。关于非开发者预期和 AI 辅助业务应用交付的实用建议帖。
- [#56069](https://github.com/openai/codex/discussions/56069) — **macOS 上的 MCP Apps 是否支持画中画，如何启用？**（2 条评论，1 👍）。关于第三方 MCP App UI 的可扩展性问题。
- [#55553](https://github.com/openai/codex/discussions/55553) — **远程执行预检在进程可执行文件链接上出现 `EACCES`**（1 条评论，1 👍）。请求对远程 Linux 执行失败进行归因并提供支持渠道。

**展示与分享**
- [#56176](https://github.com/openai/codex/discussions/56176) — **Agent Relay Check v0.1.3：针对 `AGENTS.md` 的可重复前后检查**（4 条评论，1 👍）。用于验证指令路径新鲜度的实用工具。
- [#56731](https://github.com/openai/codex/discussions/56731) — **Agoryx：面向 Codex 和 Claude Code 的本地优先共享对话**（2 条评论，1 👍）。MIT 许可的 GUI，用于多智能体构建-审查循环。
- [#56856](https://github.com/openai/codex/discussions/56856) — **MarketMirror：在 Codex 的模拟买家上测试广告、价格和推销话术**（1 条评论，1 👍）。AGPL-3.0 插件/MCP 服务器，用于支出前的营销测试。

### 功能请求趋势
- **Computer Use 和浏览器自动化可靠性**：URL/状态检测、浏览器控制、输入重复和延迟仍是跨平台的首要关注点（[#51524](https://github.com/openai/codex/issues/51524)、[#50728](https://github.com/openai/codex/issues/50728)、[#56822](https://github.com/openai/codex/issues/56822)、[#55529](https://github.com/openai/codex/issues/55529)）。
- **Windows 桌面/IDE 安装与启动韧性**：MSIX 安装程序失败、VS Code 渲染器超时和扩展启动回归反复出现（[#53132](https://github.com/openai/codex/issues/53132)、[#56691](https://github.com/openai/codex/issues/56691)、[#56780](https://github.com/openai/codex/issues/56780)）。
- **沙箱与审批策略清晰度**：用户需要可预测的默认值和可操作的审批行为，用于委托任务、`write_stdin` 和 `apply_patch`（[#50737](https://github.com/openai/codex/issues/50737)、[#56874](https://github.com/openai/codex/issues/56874)、[#54947](https://github.com/openai/codex/issues/54947)）。
- **MCP/插件生命周期管理**：重连/刷新、工具重新发现、OAuth 持久化和不可用插件提示被频繁请求（[#56970](https://github.com/openai/codex/issues/56970)、[#56985](https://github.com/openai/codex/issues/56985)、[#53854](https://github.com/openai/codex/issues/53854)、[#56987](https://github.com/openai/codex/issues/56987)）。
- **远程与跨设备工作流**：SSH 主机上的定时任务、远程线程渲染和共享项目线程是明显的需求领域（[#34946](https://github.com/openai/codex/issues/34946)、[#37913](https://github.com/openai/codex/issues/37913)、[#56801](https://github.com/openai/codex/issues/56801)）。
- **用量/限流可观测性与效率**：用户希望非交互式地检查限额，并获得更可持续的 Plus 用量（[#54956](https://github.com/openai/codex/issues/54956)、[#56932](https://github.com/openai/codex/discussions/56932)）。
- **智能体/会话可靠性**：过早完成轮次、容量错误和长时间运行任务未完成是反复出现的抱怨（[#56951](https://github.com/openai/codex/issues/56951)、[#43864](https://github.com/openai/codex/issues/43864)、[#55443](https://github.com/openai/codex/issues/55443)）。
- **可扩展性与本地优先协作**：`AGENTS.md` 诊断、MCP App UI 支持和多智能体/共享对话工具正获得关注（[#56176](https://github.com/openai/codex/discussions/56176)、[#56069](https://github.com/openai/codex/discussions/56069)、[#56731](https://github.com/openai/codex/discussions/56731)、[#56856](https://github.com/openai/codex/discussions/56856)）。

### 开发者痛点
- **Windows 是最大的回归面**：安装、IDE 启动、Computer Use、浏览器可用性和文件补丁均存在活跃的 Windows 专属阻塞问题。
- **Computer Use 仍然不稳定**：URL 检测、输入重复、滚动/控制丢失和延迟使得自动化在 macOS 和 Windows 上都不可靠。
- **沙箱/审批行为不透明**：委托任务可能忽略用户默认设置，细粒度策略可能在不提示的情况下静默拒绝操作。
- **MCP/插件状态脆弱**：工具消失、OAuth 流程重复、插件返回 404，且没有简单的聊天内重连/重新发现路径。
- **诊断能力不足**：报告反复提及缺少原生诊断、原因码不可用以及远程执行故障难以归因。
- **智能体中途停止或遇到容量限制**：过早终结和“所选模型已满容量”错误干扰多步骤工作流。
- **限流可见性薄弱**：用户仍依赖变通方案来了解 5 小时/每周用量以及限额为何快速下降。
- **跨设备工作流不完整**：SSH 主机调度、远程线程渲染和共享连接计算机项目线程仍是功能空白。

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区摘要 — 2026-10-26

## 今日亮点
活动主要围绕 agent 子系统的加固扫尾展开：过去 24 小时内，一位贡献者集中合入了一组修复，涉及后台工具取消、远程 agent 会话隔离、日志尾部截断以及计划文件解析。与此同时，Issue 跟踪器榜首仍被长期运行的 P1 agent 可靠性工作流占据——最突出的是子 agent 在触达 `MAX_TURNS` 后虚报成功，以及通用 agent 无限期挂起。另一个次要但持续存在的主题是跨平台正确性，Windows 路径/波浪号解析、无 shell 的 lint 以及 Wayland 下浏览器 agent 失败均受到关注。

## 发布
- **v0.67.0-nightly.20261025.gcd1b45c2d** — 自动化 nightly 构建；未提供精选发布说明。[完整变更日志](https://github.com/google-gemini/gemini-cli/compare/v0.67.0-nightly.20261024.gcd1b45c2d...v0.67.0-nightly.20261025.gcd1b45c2d) 与前一 nightly 对比。过去 24 小时内无稳定版发布。

## 热门 Issue

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — 子 agent 在 MAX_TURNS 后恢复却被报告为 GOAL 成功（P1，13 条评论）** 今天讨论量最高的单个 Issue。`codebase_investigator` 返回 `status: "success"` / `Termination Reason: "GOAL"`，尽管其自身输出承认在未进行任何分析前就已触达回合上限。这是一个信号正确性问题：编排逻辑和用户都会消费该状态，因此静默截断与成功无法区分。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — 通用 agent 挂起（P1，8 条评论，8 👍）** 按表情回应计，互动量最高的 bug。任何转交给通用 agent 的操作——甚至创建文件夹这类简单操作——都可能无限期挂起；据报告，抑制子 agent 委托是可行的规避方案。表情回应数量表明该问题可广泛复现。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — 零依赖 OS 沙箱与执行后意图路由（P2，9 条评论）** 一个设计层面的提案，主张 Gemini 3 原生面向 bash，应允许其自由串联 POSIX 工具，并通过 OS 沙箱加上执行后的意图路由来保障安全，而不是在执行前对工具进行门控。

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini 未充分使用技能和子 agent（P2，7 条评论）** 虽属个例，但引发广泛共鸣：自定义技能和子 agent 只有在明确指示时才会被调用。这削弱了整个 agent 扩展叙事——能力确实存在，但模型不会主动调用它们。

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — 评估 AST 感知的文件读取、搜索与映射（P2，7 条评论）** 一个 EPIC，研究 AST 感知工具是否能减少错位读取和 token 噪声。它与 [#22746](https://github.com/google-gemini/gemini-cli/issues/22746)（映射，评估 `tilth`/`glyph`）和 [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)（使用 AST-grep 进行搜索）配套，是当前在推进的最具体的上下文效率押注。

6. **[#29791](https://github.com/google-gemini/gemini-cli/issues/29791) — 扩展在爬取后未出现在图库中（P2，6 条评论）** `holt-os/holt` 满足文档要求并通过验证，但未出现在 `geminicli.com/extensions.json` 中（最后修改于 10 月 16 日）。这指向扩展分发流水线中爬取节奏/资格判定不透明的问题。

7. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — 浏览器 Agent 忽略 `settings.json` 覆盖项（P2，4 条评论）** `AgentRegistry` 在初始化时正确合并了设置，但浏览器 agent 将其丢弃——例如 `maxTurns`。配置静默不生效，对任何调优 agent 行为的人来说都是信任问题。

8. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — 浏览器子 agent 在 Wayland 下失败（P1，4 条评论）** 失败时仍输出 `Termination Reason: GOAL`，加剧了 #22323 中的虚报成功问题。Linux/Wayland 用户实际上被挡在浏览器自动化之外。

9. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 工具数超过 128 时出现 400 错误（P2，3 条评论）** 标题说 128，正文说 400——无论如何，都没有将活动工具集控制在模型限制内的作用域逻辑。随着扩展生态增长，这个问题很可能愈发严重。

10. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent 应停止/劝阻破坏性行为（P2，3 条评论）** 在存在更安全路径时，模型仍会动用 `git reset` 或 `--force`，并且缺少围绕修改数据库的护栏。这是一个安全性/引导问题，而不仅仅是质量问题。

*同样值得关注：* [#22186](https://github.com/google-gemini/gemini-cli/issues/22186)（P1 钩子输出崩溃）、[#20079](https://github.com/google-gemini/gemini-cli/issues/20079)（符号链接的 agent 文件被忽略）、[#23571](https://github.com/google-gemini/gemini-cli/issues/23571)（模型在工作区各处散落临时脚本）。

## 关键 PR 进展

1. **[#29838](https://github.com/google-gemini/gemini-cli/pull/29838) [CLOSED] — 按活动 IDE 工作区根目录解析相对路径（P1）** 修复 `WriteFileTool`、`EditTool`、`ReadFileTool` 和 `correctPath`，使其查询 `ideContextStore`，而不是始终回退到 `config.getTargetDir()`。这对多根/嵌套 Git 的 IDE 工作流很重要；已在统计窗口内合并并关闭。

2. **[#29948](https://github.com/google-gemini/gemini-cli/pull/29948) — 在后台输出读取期间遵守取消** 此前，中止一个延迟调用后，它仍会完成并读取日志。复用具备中止感知的延迟辅助函数，使取消能够及时生效。

3. **[#29946](https://github.com/google-gemini/gemini-cli/pull/29946) — 按父会话和端点隔离远程对话** 阻止远程 agent 会话 ID 在相互独立的父会话之间泄漏，并修复不同端点上同名 agent 之间的标识符复用问题。

4. **[#29947](https://github.com/google-gemini/gemini-cli/pull/29947) — 保留超大的后台日志尾部** 此前，一个 70,001 字节的单行日志只会返回 `Full Log Output:`，并丢弃整个尾部。现在，当 64 KB 窗口内放不下任何完整行时，会保留部分片段。

5. **[#29942](https://github.com/google-gemini/gemini-cli/pull/29942) — 扫描完整计划内容并报告读取失败** 修复 1 KB 采样会拒绝以 ≥1,024 个空格开头的有效计划的问题，并区分不可读计划与空计划。

6. **[#29871](https://github.com/google-gemini/gemini-cli/pull/29871) — 当循环恢复在一轮开始前启动时保留未发送请求** `_recoverFromLoop` 会丢弃待处理的 `functionResponse`，导致工具调用以“Response was lost or interrupted.”关闭。一个微妙但影响重大的 agent 循环正确性修复。

7. **[#29931](https://github.com/google-gemini/gemini-cli/pull/29931) — 展开 Windows 用户主目录相对路径** `~\policies\custom.toml` 和本地扩展注册表路径现在会在配置的主目录下解析，而不是保留字面波浪号。

8. **[#29935](https://github.com/google-gemini/gemini-cli/pull/29935) — 保留 `@file` 引用中的空白** 移除多余的 `.trim()`，因此 `    print("hello")\n` 不再以 `print("hello")` 的形式到达模型——这是缩进和换行损坏的一个隐蔽来源。

9. **[#29940](https://github.com/google-gemini/gemini-cli/pull/29940) — 无需 Unix shell 管道运行 YAML lint** 移除原生 Windows 上 `'grep' is not recognized` 失败，并规避旧版 Windows 代码页下的 `UnicodeDecodeError`。

10. **[#29929](https://github.com/google-gemini/gemini-cli/pull/29929) — 在模型列表中显示准确的输出限制** 为 Gemini 2.5 Pro/Flash 报告文档记载的 65,536 输出 token 限制，并停止为限制未知的模型输出误导性的 8,192。

*队列中还有：* [#29912](https://github.com/google-gemini/gemini-cli/pull/29912)（从 `web_fetch` URL 中去除末尾标点）、[#29933](https://github.com/google-gemini/gemini-cli/pull/29933)（修正 README 中的 MCP 示例，澄清 checkpointing 与 resume 的区别）、[#26844](https://github.com/google-gemini/gemini-cli/pull/26844)（缺失的 `CustomTheme` schema 键导致启动时 Zod 错误）、[#19013](https://github.com/google-gemini/gemini-cli/pull/19013)（Windows 上扩展更新时出现 `EBUSY`），以及 [#29664](https://github.com/google-gemini/gemini-cli/pull/29664) 中一大批 P1 dependabot 更新（74 个 npm 更新）。

## 热门讨论
本窗口未提供讨论数据；本节省略。

## 功能请求趋势
- **子 agent 可观测性与控制。** 一个明显的集群希望将 agent 内部状态暴露出来并可控：通过 `/chat share` 提供可共享的子 agent 轨迹（[#22598](https://github.com/google-gemini/gemini-cli/issues/22598)）、将子 agent 上下文纳入 `/bug` 报告（[#21763](https://github.com/google-gemini/gemini-cli/issues/21763)），以及通过 `Ctrl+B` 将本地 agent 转入后台（[#22741](https://github.com/google-gemini/gemini-cli/issues/22741)）。
- **上下文与 token 效率。** AST 感知的读取/搜索/映射 EPIC（[#22745](https://github.com/google-gemini/gemini-cli/issues/22745)、[#22746](https://github.com/google-gemini/gemini-cli/issues/22746)、[#22747](https://github.com/google-gemini/gemini-cli/issues/22747)）、“有分寸的提取”式精准读取（[#19561](https://github.com/google-gemini/gemini-cli/issues/19561)），以及用持久化的基于文件的任务 CRUD 替代上下文内的 `WriteToDo`（[#18836](https://github.com/google-gemini/gemini-cli/issues/18836)）。
- **主动使用能力与更安全的行为。** 模型应无需提示就主动使用技能/子 agent（[#21968](https://github.com/google-gemini/gemini-cli/issues/21968)），同时避免破坏性的 git 和数据库操作（[#22672](https://github.com/google-gemini/gemini-cli/issues/22672)），并且不要在工作区乱丢临时脚本（[#23571](https://github.com/google-gemini/gemini-cli/issues/23571)）。
- **将安全与沙箱作为一等层。** 零依赖 OS 沙箱与执行后意图路由（[#19873](https://github.com/google-gemini/gemini-cli/issues/19873)），以及按工作区而非全局划定策略作用域（[#18397](https://github.com/google-gemini/gemini-cli/issues/18397)）。
- **Agent 韧性。** 自动接管浏览器会话和锁恢复（[#22232](https://github.com/google-gemini/gemini-cli/issues/22232)），以及在模型限制下更智能地划定工具作用域（[#24246](https://github.com/google-gemini/gemini-cli/issues/24246)）。
- **终端 UX 打磨。** 无闪烁、高性能的调整大小行为，需要进行 `Static` → `RenderStatic` 迁移（[#21924](https://github.com/google-gemini/gemini-cli/issues/21924)）。

## 开发者痛点
- **静默失败是主要挫败感来源。** 排名前三的 Issue 都描述了 agent 失败却报告成功或完全不报告：`MAX_TURNS` 被伪装成 `GOAL`（#22323）、通用 agent 永远挂起（#21409），以及 Wayland 下浏览器 agent 失败仍输出 `GOAL`（#21983）。用户无法区分“完成”与“放弃”。
- **配置不生效。** 浏览器 agent 忽略 `settings.json` 覆盖项（#22267），以及符号链接的 agent 文件无法被发现（#20079），都会削弱对配置面的信心。
- **Windows 仍是二等平台。** 在一个 24 小时窗口内：波浪号路径未展开（#29931）、预检因缺少 `grep` 以及代码页 `UnicodeDecodeError` 而失败（#29940），以及扩展更新期间出现 `EBUSY`（#19013）。正在取得进展，但修复仍是零敲碎打。
- **内容保真 bug。** `@file` 引用中的空白被剥离（#29935）、朴素的 `\n` 转义处理（#22466），以及被标点污染的 `web_fetch` URL（#29912），都会以难以从 agent 输出中诊断的方式损坏输入。
- **工具规模限制。** 一旦活动工具集超过模型限制就会硬失败（#24246），随着扩展激增，这一问题会恶化——而扩展图库不透明的爬取节奏（#29791）从一开始就让新扩展难以被发现。
- **交互式提示阻塞自动化。** agent 卡在 Vite 脚手架交互式提示上（#22465），对于任何期望 TTY 输入的 CLI 来说都是一类反复出现的失败。

---

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — 社区摘要（2026-10-26）

> **数据快照：** 过去 24 小时有 24 个 issue 更新 · 无新版本发布 · 无 PR 活动 · 未提供讨论数据。
> 因此相应省略“版本发布”“PR 进展”和“热门讨论”部分。

---

## 1. 今日亮点

过去 24 小时没有发布新版本，但 issue 跟踪器主要被 **1.0.103 中的两个严重可靠性与资源消耗回归**占据：持续的会话锁超时（#5253）以及主进程膨胀到 37 GiB（#5254）——二者均由同一名用户在长时间并行 agent 会话期间报告。与此同时，**ACP（Agent Client Protocol）能力对齐**仍是最突出的功能主题，共有三项独立请求，分别涉及用量上报、会话重命名和轮次中途引导（mid-turn steering）。企业认证与托管策略处理（#5236、#5250、#5109、#5130）在 Windows 和 macOS 上仍持续产生摩擦。

---

## 2. 版本发布

过去 24 小时无新版本。请注意，用户正积极呼吁提供节奏更慢的 **稳定发布通道**（#5203），正是因为下文这类回归落入了快速迭代线。

---

## 3. 热门 Issue

1. **[#5253](https://github.com/github/copilot-cli/issues/5253) — `[triage]` CLI 1.0.103：持续会话锁超时与任务无响应** · 2 条评论
   长时间运行的会话会变得基本无响应，并出现 `session lock unavailable during is_session_generation_current: acquisition timed out`。工具活动在后台继续进行，而 UI 卡住——对于任何运行数小时会话的人来说都是硬性阻塞。最新报告，已进入 triage。

2. **[#5254](https://github.com/github/copilot-cli/issues/5254) — `[triage]` 主进程增长至 37 GiB；133M 次 malloc 分配，10.7 GiB 碎片化** · 0 条评论
   在 32 GiB 内存的 Apple Silicon Mac 上，一个恢复运行的 6h19m 会话超出了物理内存（占用 37.3 GiB），并导致系统交换分区饱和。这些分配/碎片化数字使其看起来更像是可信的内存管理 bug，而非用户错误，并且它与 #5253 同时出现，颇为可疑。

3. **[#4233](https://github.com/github/copilot-cli/issues/4233) — `[ACP]` 在 `--acp` 模式下发出 `usage_update`（上下文窗口 + AI 额度）** · 3 条评论 · 👍 4
   本时间窗口内获赞最多的 issue。CLI 已经为 `/context` 和 `/usage` 计算上下文/额度数据，但从未通过 ACP 暴露这些数据，因此 Zed 和其他 ACP 客户端无法渲染用量指示器。这是一个低成本、高可见度的能力对齐缺口。

4. **[#5195](https://github.com/github/copilot-cli/issues/5195) — 选择自定义 agent 会在首次模型调用后禁用延迟 MCP 工具加载** · 1 条评论
   启用工具搜索后，默认 agent 的 MCP 工具会被正确延迟加载——但选择自定义 agent（`--agent` / `/agent`）后，从第二次调用开始，会将每个 MCP 服务器中的每个工具都加载到上下文中。这是一个静默消耗 token 的 bug，直接削弱了延迟加载功能。

5. **[#5236](https://github.com/github/copilot-cli/issues/5236) — Windows：MCP 登录后 Entra broker token 缓存仍为 0 字节** · 1 条评论
   每个新进程都会重新提示选择账户；进程内 `/restart` 则不会。影响所有在 Windows 上使用受 Entra 保护的 HTTP MCP 服务器的人——在无头/自动化场景中，反复进行交互式认证是显著的工作流负担。

6. **[#5250](https://github.com/github/copilot-cli/issues/5250) — macOS：Entra-broker MCP 服务器在启动时因并发登录被拒绝而失败** · 0 条评论
   两个受 Entra 保护的远程 MCP 服务器（Azure DevOps remote MCP、WorkIQ）在启动时因“sign-in is already in progress”而连接失败，且被拒绝的缓存 token 不会被恢复。这是 #5236 的 macOS 对应问题——原生 Entra broker 路径在两个平台上似乎都缺少并发处理。

7. **[#5201](https://github.com/github/copilot-cli/issues/5201) — 提示词预算拒绝无法被机器识别** · 1 条评论
   在无头 JSON-RPC 模式下，超出模型提示词预算时只会返回 `errorType: "query"`，没有 `errorCode`，也没有修复提示。自动化开发者无法区分“压缩后重试”与真正的查询失败——对于 CI 和 agent 编排用例来说是一个真实缺口。

8. **[#5115](https://github.com/github/copilot-cli/issues/5115) — `[ACP]` `/rename` 报告成功但并未重命名会话** · 2 条评论
   该命令通过 `available_commands_update` 对外公布，在约 1 ms 内返回 `end_turn` 并给出成功回复，且不发起任何模型调用——但会话名称仍未改变。在客户端信任响应的协议模式下，静默无操作命令尤其有害。

9. **[#4977](https://github.com/github/copilot-cli/issues/4977) — 捆绑的 ripgrep 在 16KB 页的 ARM64（Asahi Linux）上因 jemalloc“Unsupported system page size”而中止** · 2 条评论
   捆绑的 `rg` 二进制将 4 KB 页大小假设硬编码，在 Asahi Linux 以及任何 16 KB 页的 ARM64 内核上会立即中止。这需要一项打包层面的修复（非 jemalloc 构建或运行时页大小检测）；目前该问题使 CLI 在这些发行版上不可用。

10. **[#5203](https://github.com/github/copilot-cli/issues/5203) — 功能请求：为 Copilot CLI 提供稳定发布通道** · 2 条评论
    该请求认为快速发布节奏引入了破坏日常使用的回归，并提议设立 Stable/Beta 通道。随着 1.0.103 相关问题（#5253、#5254）积累，这一点愈发重要。

*另需关注：* [#5198](https://github.com/github/copilot-cli/issues/5198)（ACP 轮次中途引导）、[#5147](https://github.com/github/copilot-cli/issues/5147)（`/restart` 在 `--new-session-id` 下失败）、[#4401](https://github.com/github/copilot-cli/issues/4401)（skill 工具在 Windows 上找不到 `~/.agents/skills`），以及 [#5109](https://github.com/github/copilot-cli/issues/5109) / [#5130](https://github.com/github/copilot-cli/issues/5130)（持续出现“Managed account policy could not be refreshed”错误）。

---

## 4. 关键 PR 进展

无——过去 24 小时没有 pull request 更新。

---

## 5. 热门讨论

本时间窗口未提供讨论数据。

---

## 6. 功能请求趋势

- **ACP / 无头模式功能对齐是首要功能方向（24 个 issue 中占 3 个）。** 请求始终以“与交互式 CLI 或 SDK 模式对等”为框架：用量/上下文上报（#4233）、可正常工作的会话命令（#5115）、轮次中途引导（#5198），以及无头 JSON-RPC 的机器可读错误码（#5201）。ACP 客户端（Zed 等）显然是一个不断增长的集成面，但目前仍处于二等公民地位。
- **Token/上下文经济性。** 两项相关请求针对每个会话固定注入的约 40k token 上下文（#5251，另有一个重复项以 #5252 关闭），再加上延迟 MCP 加载回归（#5195）。用户越来越将上下文预算视为首要成本。
- **发布工程 / 稳定性。** 稳定发布通道（#5203）是最清晰的流程层面请求，1.0.103 的回归报告也隐含强化了这一点。
- **模型相关默认值的可配置性。** 语音转写语言被硬编码为 `"auto"`（#5226），迫使非英语使用者在每个会话中重新说第一句话——这是更广泛的“让硬编码默认值可配置”主题中一个小而具体的例子。
- **跨平台打包正确性。** ARM64 页大小支持（#4977）以及 Windows 的 skill/agent 发现（#4401、#3815）反映出一种反复出现的模式：以 Linux/macOS 为先的假设渗入了平台特定代码路径。

---

## 7. 开发者痛点

- **长时间会话稳定性是当前最尖锐的痛点。** 会话锁获取超时（#5253）、37 GiB 内存增长并伴随严重的分配器碎片化（#5254），以及长时间并行 agent 运行中的任务无响应，削弱了 agentic CLI 的核心价值主张——人们期望它能连续运行数小时。
- **`/restart` 是一条反复出现的故障点。** 它在 `--new-session-id` 下失败（#5147），过去在 `-w` worktree 下也失败过（#4493，现已关闭），并且不会清除 Entra 认证状态（#5236）。从异常会话中恢复本身就不可靠。
- **企业托管策略噪音。** “Managed account policy could not be refreshed”反复且持续出现（#5109、#5130），伴随重试循环，在某些情况下使 CLI 对托管账户实际上不可用。
- **Entra broker 认证在两个桌面平台上都很脆弱。** Windows 上的空 token 缓存（#5236）和 macOS 上被拒绝的并发登录（#5250）意味着反复的交互式提示和 MCP 启动失败——在脚本化或多服务器环境中尤其痛苦。
- **协议边界上的静默失败。** `/rename` 未重命名却返回成功（#5115），以及提示词预算错误缺少错误码（#5201），都迫使集成方猜测。协议使用方需要明确、带类型的失败信号。
- **可感知的发布质量下滑。** 多份报告提到“至少从 1.0.94 起”就存在回归，以及“快速发布周期往往会引入回归”，这推动了对稳定通道的需求，并要求更保守地推出对核心会话/认证路径的更改。

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区简报 — 2026-10-26

## 1. 今日亮点

过去 24 小时没有新版本发布。主导话题是免费套餐额度事件：十多个 issue 报告每日免费使用额度要么从不重置，要么在归零后重新开始倒计时，数名报告者将其与按 IP 计量联系起来（也与仍未关闭的 VPN 轮换漏洞报告有关）。与此同时，核心工程活动密集：V2 可靠性与性能 PR（空输出轮次重试、提供商连接预热、长会话历史加载、主线程外图片缩放），以及一项新的实验性 media-blob API，它不再以 base64 重新发送截图。

---

## 2. 热门 Issue

1. **[#34344](https://github.com/anomalyco/opencode/issues/34344) — 免费模型存在无限使用漏洞（CLOSED，56 条评论）**  
   本时段讨论最多的 issue：免费模型的速率限制绑定到 IP，因此轮换 VPN 端点就能无限重置额度。作者自动化轮换，并持续运行 DeepSeek v4 Flash 和 Mimo v2.5。这是今日投诉集群的结构性根源——按 IP 限制既可以被绕过，也对共享/NAT 地址不公平。

2. **[#58781](https://github.com/anomalyco/opencode/issues/58781) — Desktop 1.19.4 中所有免费模型均提示“Free Usage Exceeded”（13 条评论，👍9）**  
   反响最大的 issue：所有免费模型同时停止工作，并弹出“Subscribe to Go”提示。9 个赞和 13 条评论使其成为该事件的社区主帖；标记为 `pending close, triaging`。

3. **[#58831](https://github.com/anomalyco/opencode/issues/58831) — 免费额度超时后永不恢复，在 86400s 处呈锯齿状（6 条评论，👍2）**  
   该 bug 最清晰的重现：倒计时归零后立刻跳回 86400 秒。还要求提供更人性化的 `hh:mm:ss` 计时器——一个小的 UX 抱怨，却捆绑着真实的状态机缺陷。

4. **[#59038](https://github.com/anomalyco/opencode/issues/59038) — 倒计时归零后重新开始（4 条评论）**  
   在另一个账号/地区独立确认了同样的重置循环，从 22947 秒开始倒计时。跨报告相互印证，才使这件事从用户错误变为服务端 bug。

5. **[#58933](https://github.com/anomalyco/opencode/issues/58933) — Windows 11 上出现 59,769 秒冷却（5 条评论，👍1）**  
   表明冷却时长极不一致（此处 16.6 小时，别处 24 小时），暗示额度窗口是基于不一致的锚点计算的。

6. **[#56057](https://github.com/anomalyco/opencode/issues/56057) — Desktop 文件树开箱即不可用（4 条评论，今日更新）**  
   `showFileTree` 默认值为 `false`，且不存在可切换它的设置 UI；该 setter 没有任何调用点。这是 desktop 2.0.30 的回归，导致不编辑配置就无法访问核心导航界面。

7. **[#59051](https://github.com/anomalyco/opencode/issues/59051) — 在 v2.1.4 上 Subagent 向父级返回空结果（3 条评论）**  
   子会话报告 `idle_outcome: succeeded`，并带有真实 token 用量和成本，但父级收到“Subagent completed without a text response.”对任何编排 subagent 的人来说都是静默数据丢失——这是较严重的 V2 正确性报告之一。

8. **[#57603](https://github.com/anomalyco/opencode/issues/57603) — 在 `ask` 权限下，委托的 subagent 写入被静默拒绝（CLOSED，5 条评论）**  
   设置为 `ask` 的权限在 Desktop 中任何地方都不会产生提示，因此写入失败且没有审批路径。v1 上可用，v2 上损坏——这是一个与安全密切相关的 UX 回归，安全默认值变成了死胡同。

9. **[#39986](https://github.com/anomalyco/opencode/issues/39986) — 在打包 CLI 中支持响应式外部 TSX 插件（7 条评论）**  
   打包后的 Bun CLI 无法将 OpenTUI 的运行时 Solid transform 应用于外部 TSX 插件，并且可能加载重复的 `solid-js`/`@opentui/solid` 副本——初始帧会渲染，但 signals 不会更新。这阻碍了第三方 TUI 插件生态。

10. **[#53623](https://github.com/anomalyco/opencode/issues/53623) — “Code Mode”指令让 Gemma-4-31B 困惑（6 条评论，👍2）**  
    较小的模型会在每次工具调用时过度套用 `execute` 包装器。真正的诉求是提供一种检查和覆盖提示词的方式——将提示词可调试性作为一等功能。

---

## 3. 关键 PR 进展

1. **[#56673](https://github.com/anomalyco/opencode/pull/56673) — 对无输出结束的轮次先重试，再失败**  
   处理 Gemini 风格 SSE 流以 `finishReason: STOP`/`MAX_TOKENS` 结束且内容为零的情况。与静默空结果这一类 bug（#59051）直接相关。

2. **[#58803](https://github.com/anomalyco/opencode/pull/58803) — 在提示词准入时预热模型提供商连接**  
   在提示词被准入、上下文加载的同时打开 DNS/TCP/TLS，包括你切换到的提供商。降低空闲期后的首 token 延迟。

3. **[#59103](https://github.com/anomalyco/opencode/pull/59103) — 实验性 `GET /api/experimental/media/:hash` + `experimental-media-uri` 能力**  
   让客户端按需获取附件字节，而不是在每次会话加载时重新读取内联 base64。两端均需主动启用（`experimental.media_blob` 默认关闭），#59104 将 Desktop/web 接上以请求 `media:<hash>` 引用。

4. **[#59091](https://github.com/anomalyco/opencode/pull/59091) — 每步历史只加载一次**  
   移除了随会话长度扩展的每步工作：提示词大小计算做了 2–4 次，base64 通过重新扫描来测量，且每次 `read` 命中 `AGENTS.md` 都会解码完整转录记录。

5. **[#58820](https://github.com/anomalyco/opencode/pull/58820) — 在 worker 中缩放图片；空闲时释放 photon 内存**  
   大图缩放每次会阻塞服务器 430–480 毫秒，并在进程生命周期内固定占用约 250 MB WASM 内存。现在每个进程使用一个共享 worker，并在空闲后关闭。

6. **[#55246](https://github.com/anomalyco/opencode/pull/55246) — 通过 `./gui-experimental` 导出发布 GUI 插件**  
   `opencode.json` 中的插件可以在其 server 和 `./tui` 条目之外，同时发布一个 UI 部分；桌面应用的内置功能变成内置插件。这是迈向统一插件界面的最大架构步骤。

7. **[#59072](https://github.com/anomalyco/opencode/pull/59072) — 将实验性端点嵌套到 `client.experimental` 下**  
   让 SDK 稳定性更清晰：使用 `client.experimental.session.stats()`，而不是在生成的 Promise 和 Effect 客户端中与 `client.session.get()` 并列。

8. **[#59102](https://github.com/anomalyco/opencode/pull/59102) — 发布 MCP 工具进度通知**  
   `McpClient.callTool` 现在会暴露服务端进度。仅核心部分——TUI 工具卡片上的渲染被有意拆分出去。

9. **[#58297](https://github.com/anomalyco/opencode/pull/58297) — 将 GitHub 集成移植到 v2**  
   将 `opencode github install` / `opencode github run` 从 v1 前向移植（关闭 #57911），这是团队在 v2 上运行代码评审自动化的缺失环节。

10. **[#59046](https://github.com/anomalyco/opencode/pull/59046) — 将发布构建与签名和发布分离**  
    拆分 `.github/workflows/release.yml`，将所有使用 secret 的任务限定到 `release` 环境，收紧权限，并移除 VS Code 发布工作流。这是发布签名二进制的项目在供应链卫生方面的改进。

另外值得注意：**[#58531](https://github.com/anomalyco/opencode/pull/58531)**（Bedrock 端点区域现在从提供商设置解析——关闭 #49391）、**[#59105](https://github.com/anomalyco/opencode/pull/59105)**（在 Windows 上重试服务文件重命名，以阻止并发写入损坏）以及 **[#59107](https://github.com/anomalyco/opencode/pull/59107)**（在 TUI 连接对话框中把 OpenCode Console 列在 Go 之前）。

---

## 4. 功能请求趋势

- **免费套餐透明度与计量。** [#57540](https://github.com/anomalyco/opencode/issues/57540) 要求为闲置时间、额度池范围和剩余额度提供有文档说明的语义——这是今日事件背后被引用最多的单一缺口。[#58831](https://github.com/anomalyco/opencode/issues/58831) 在同一诉求中增加了人性化倒计时。
- **权限系统易用性。** [#27076](https://github.com/anomalyco/opencode/issues/27076) 提议自动允许只读工具（`read`、`glob`、`grep`、`todowrite`）；[#58241](https://github.com/anomalyco/opencode/issues/58241) 请求提供工作区锁，使 OpenCode 无法修改文件。两者都指向审批疲劳，同时又不削弱写入安全。
- **Desktop UI 可配置性。** 文件树开关既有 bug 报告（[#56057](https://github.com/anomalyco/opencode/issues/56057)、[#59047](https://github.com/anomalyco/opencode/issues/59047)），也有一项相邻的侧边栏请求（[#58797](https://github.com/anomalyco/opencode/issues/58797)）——面板可见性和布局不可由用户控制。
- **提示词可观测性。** [#53623](https://github.com/anomalyco/opencode/issues/53623) 要求提供调试和修改内置提示词的方法，这与较小模型上的“Code Mode”困惑相呼应。
- **替代支付方式。** [#58826](https://github.com/anomalyco/opencode/issues/58826) 请求为 Go 订阅支持 USDC，并指出当前四种方式（银行卡、Link、支付宝、UPI）都走传统支付轨道。
- **插件界面统一。** 外部响应式 TSX 插件（[#39986](https://github.com/anomalyco/opencode/issues/39986)）和 GUI 插件导出（#55246）正汇聚为横跨 TUI、server 和 desktop 的统一插件模型。

---

## 5. 开发者痛点

- **免费额度状态机已损坏，且在问题跟踪器中占据主导。** 24 小时内大约十几份报告描述了同一故障：冷却时间归零后重新开始（#58831、#59038），跨天从不重置（#58814、#58830），或在发送任何提示词之前就触发（#58827）。时长从 22,947 秒到 86,400 秒不等（#58933），这看起来更像窗口锚定不一致，而不是单一计数器陈旧。大多数已被标记为 `pending close, triaging`。
- **按 IP 限制是错误的维度。** 它惩罚共享光纤、CGNAT 和校园网地址（[#58805](https://github.com/anomalyco/opencode/issues/58805)），同时又能被 VPN 轮换轻易绕过（[#34344](https://github.com/anomalyco/opencode/issues/34344)）。这两份报告是同一设计缺陷从相反两端看的结果。
- **静默失败是最糟糕的失败模式。** Subagent 报告成功，却未向父级返回任何内容（#59051）；`ask` 权限写入被拒绝，且任何地方都不显示提示（#57603）；插件注册的 agent 在服务器启动后立即从 `GET /agent` 中缺失（#51282）。每一种都让开发者没有任何关于出错原因的信号。
- **V2 功能对等缺口仍然带来麻烦。** Shell 输出绕过 50 KiB 工具限制并使会话搁浅（[#45099](https://github.com/anomalyco/opencode/issues/45099)）、GitHub 集成直到现在才被移植（#58297），以及前台/后台 shell 的重启语义仍未定义（[#36348](https://github.com/anomalyco/opencode/issues/36348)）。
- **长会话和大附件性能。** 历史记录反复计算大小、重复 base64 扫描、`read` 路径中解码完整转录记录（#59091），以及主线程图片缩放阻塞 430–480 毫秒（#58820），都是已被确认的热点；media-blob 方向的工作（#59103/#59104/#58742）是针对附件重量的结构性修复。
- **平台小痛点。** macOS TUI 剪贴板同时与原生写入和 OSC 52 写入竞争（[#59052](https://github.com/anomalyco/opencode/issues/59052)）；Windows 服务配置写入共享同一个临时文件，可能将其损坏（#59105）。

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区文摘 — 2026-10-26

**仓库：** [earendil-works/pi](https://github.com/earendil-works/pi) · **活动窗口：** 最近 24 小时 · **更新的 Issue：** 50 · **更新的 PR：** 9 · **更新的 Discussion：** 1 · **发布：** 无

---

## 1. 今日亮点

过去 24 小时内没有发布新版本，但问题分诊速度依然很高：约 50 个 Issue 和 9 个 PR 被处理，大多数新 PR 都是小而精准的正确性修复（Windows 路径分隔符、符号链接的 agent 目录、`pi.exec` 中拆分的 UTF-8、provider id 含 `/` 的 `--model` 引用）。主导主题是 **MCP 的成熟与加固** —— 有四个独立的 MCP Issue 仍活跃（大小限制绕过、环境变量继承、OAuth 挂起、elicitation 支持），另有两个压缩 / 缓存正确性 bug，会在不报错的情况下劣化长会话。在 provider 侧，一份 `prio:p0` 的 Anthropic 报告显示，扩展重载时移除某个 tool 会污染该会话后续的每一个请求。

---

## 2. 发布

过去 24 小时内无发布。*（按文摘规范省略 — Issue 中提到的最新已发布版本为 1.4.0。）*

---

## 3. 热门 Issue

1. **[#10956](https://github.com/earendil-works/pi/issues/10956) — `cacheWarming: "idle"` 静默停止触发** · 开放 · 10 条评论 · 👍2
   一旦某个扩展处理了 `turn_end` 且上下文中含有自定义 / 压缩消息，闲置预热就再也不会触发；507 个 `cache_warming_decision` 事件全部位于 `continuationProbability: 1`，位于 0.15 的为零。静默劣化，直接影响成本和延迟 —— 当日互动量最高的 Issue。

2. **[#10628](https://github.com/earendil-works/pi/issues/10628) — OSC 52 复制被 tmux 吞掉** · 开放 · 10 条评论 · 👍5
   选中即复制会发出原始 OSC 52，tmux 将其拦截到自己的粘贴缓冲区，而不是转发给客户端 —— 但 Pi 仍然会闪出"已复制！"。本窗口内反应最多的 Issue；影响所有无头 / 通过 tmux 使用 SSH 的用户。

3. **[#11376](https://github.com/earendil-works/pi/issues/11376) — `[prio:p0]` 会话中途移除初始 tool 会破坏之后所有 Anthropic 请求** · 开放 · 3 条评论
   扩展重载后不再注册 tool，会导致之后每次调用 `anthropic/claude-opus-5-5` 都返回 `400 invalid_request_error: tool_reference_unresolved`。会话级致命，也是本窗口内唯一的 P0。

4. **[#10885](https://github.com/earendil-works/pi/issues/10885) — llama.cpp 上下文溢出未被检测到 → 自动压缩从不触发** · 开放 · 5 条评论 · 👍1
   llama-server 的 `"Context size has been exceeded."` 错误未被识别，因此在使用 262k 上下文的模型时压缩永远不会启动。说明溢出检测依赖 provider 的字符串而非结构性判断。

5. **[#11335](https://github.com/earendil-works/pi/issues/11335) — MCP HTTP JSON 响应绕过 `maxMessageBytes`** · 开放 · 4 条评论
   通过 JSON 路径可以接受 2,048 字节的 tool 描述，而通过 SSE 的相同 JSON-RPC 内容却被拒绝。同一安全限制在不同传输方式下执行不一致。

6. **[#11297](https://github.com/earendil-works/pi/issues/11297) — 压缩文件列表无限增长** · 开放 · 3 条评论
   `extractFileOperations` 每次压缩都以上一次压缩的 `readFiles`/`modifiedFiles` 作为种子，导致 `<read-files>`/`<modified-files>` 无限累积 —— 长会话中上下文膨胀会不断叠加。

7. **[#11311](https://github.com/earendil-works/pi/issues/11311) — MCP OAuth 卡在"连接中…"，取消无效** · 开放 · 3 条评论
   浏览器授权成功后卡顿 2–3 分钟；`Ctrl+C` 无响应。没有取消路径的授权流程是 MCP 采用的硬阻塞。

8. **[#11377](https://github.com/earendil-works/pi/issues/11377) — `stripAnsi` 在输出中残留私有参数 CSI 载荷** · 开放 · 3 条评论
   `<`、`=`、`>` 被当作 CSI 终止字节，导致鼠标报告和 `ESC[>…c` 回复将参数文本泄漏到 tool 输出 / 模型上下文。仅 3 条评论，但是一个干净、高价值的正确性修复。

9. **[#11361](https://github.com/earendil-works/pi/issues/11361) — 为 MCP stdio 服务器暴露 `inheritEnv: false`** · 开放 · 2 条评论
   原生 MCP stdio 服务器目前会继承完整的 Pi 进程环境，即使 `env` 声明了严格的允许列表。带有明确安全理由的选择性隔离请求。

10. **[#11375](https://github.com/earendil-works/pi/issues/11375) — `-e npm:` 包永不更新** · 开放 · 2 条评论
    `pi -e npm:pi-web-access` 会永久缓存首次下载，没有任何刷新路径 —— 与文档中 `-e` 的"临时使用"语义相矛盾。

*同样值得关注：* [#9534](https://github.com/earendil-works/pi/issues/9534) OpenRouter 丢弃仅含签名的 Claude reasoning chunk（自 9 月 12 日起开放）、[#11331](https://github.com/earendil-works/pi/issues/11331) `tool_search` 在精确 tool 名称上失败、[#11409](https://github.com/earendil-works/pi/issues/11409) `pi update --extensions` 打印无法处理的 npm audit 输出。

---

## 4. 关键 PR 进展

窗口内更新的全部 9 个 PR 均已列出 —— 大多数当日关闭。

1. **[#11411](https://github.com/earendil-works/pi/pull/11411) — fix(tui)：全屏下保留 iTerm2 内联图片** · 已关闭
   停止在备用屏幕激活时禁用 iTerm2 图片能力；此前全屏下工具结果的图片块会回退为 `[Image: …]` 文本。

2. **[#11397](https://github.com/earendil-works/pi/pull/11397) — fix：当 provider id 含斜杠时解析 `provider/id`** · 已关闭
   `splitProviderPrefix()` 以第一个 `/` 分割，破坏了诸如 `llama-server=http://host:10800` 这类从 URL 派生的 id。（与贡献者 PR #11393 重复 —— 见痛点。）

3. **[#11393](https://github.com/earendil-works/pi/pull/11393) — fix(coding-agent)：同样的 provider-id 中含 `/` 的解析** · 已关闭
   社区为 [#11394](https://github.com/earendil-works/pi/issues/11394) 编写的修复；与机器人编写的 #11397 一同落地。

4. **[#11396](https://github.com/earendil-works/pi/pull/11396) — fix：当 agent 目录为符号链接时，将真实路径作为 npm prefix** · 已关闭
   `~/.pi` 符号链接到 dotfiles 仓库会导致 npm 写入 `../../../dotfiles/…` 的 lock 条目。常见的 dotfiles 配置，干净的修复。

5. **[#11399](https://github.com/earendil-works/pi/pull/11399) — fix：在 Windows 上规范化 `check-entry-graphs` 中的路径分隔符** · 已关闭
   Windows 上 `path.relative()` 返回 `\`，而允许 / 禁止模式使用 `/`，因此 `packages/telemetry/src/index.ts` 的允许规则从未匹配。

6. **[#11395](https://github.com/earendil-works/pi/pull/11395) — fix：在 `pi-test.sh --no-env` 中取消设置所有 provider 环境变量** · 已关闭
   手工维护的 `unset` 列表已与 `env-api-keys.ts` 脱节；`DEEPSEEK_API_KEY`、`MOONSHOT_API_KEY`、`ANTHROPIC_AUTH_TOKEN` 仍会泄漏到 "no-env" 测试运行中。

7. **[#11330](https://github.com/earendil-works/pi/pull/11330) — fix(coding-agent)：在 `pi.exec` 中保留被拆分的 UTF-8 输出** · 已关闭
   按块调用 `Buffer.toString()` 会损坏跨越块边界的字符（`你好` → `���好`）。现在对 stdout/stderr 分别使用独立的 `StringDecoder`。

8. **[#10718](https://github.com/earendil-works/pi/pull/10718) — fix(coding-agent)：在 `--export` 的 HTML 中包含系统提示词** · 开放
   CLI `--export` 与交互式 `/export` 出现差异。自 10 月 9 日起开放 —— 唯一仍在等待合并的非机器人 PR。

9. **[#11408](https://github.com/earendil-works/pi/pull/11408) — docs：添加源码学习路径（中文）** · 已关闭
   面向阅读代码库的中文贡献指南；表明非英语贡献者兴趣正在增长。

---

## 5. 热门 Discussion

**问答**
- **[#11404](https://github.com/earendil-works/pi/discussions/11404) — 官方 Pi code-agent Docker 沙箱？** · 👍1 · 0 条评论
  询问 Docker Hub 上的四个 `sbx/pi*` 镜像（`pi`、`pi-mixin`、`pi-image`、`pi-kit`）是官方还是社区构建。尚无回复 —— 很适合作为关于支持的沙箱 / 容器化路径的官方声明。

---

## 6. 功能请求趋势

- **MCP 作为一等、加固的客户端界面。** 请求集中在一致性和安全性：跨传输一致的 `maxMessageBytes`（[#11335](https://github.com/earendil-works/pi/issues/11335)）、通过 `inheritEnv: false` 的选择性环境隔离（[#11361](https://github.com/earendil-works/pi/issues/11361)）、可工作且可取消的 OAuth（[#11311](https://github.com/earendil-works/pi/issues/11311)），以及 elicitation 支持（以 no-action 关闭，[#10274](https://github.com/earendil-works/pi/issues/10274)）。
- **provider 无关的规范化层。** 反复出现的要求：强制转换并规范化不符合规范的 provider 行为：字符串化的 JSON 数组 / null tool 参数（[#11389](https://github.com/earendil-works/pi/issues/11389)）、Anthropic 兼容端点上的 `developer` role（[#11365](https://github.com/earendil-works/pi/issues/11365)）、为 GLM-5.3 提供的 Bedrock `reasoning_effort`（[#11354](https://github.com/earendil-works/pi/issues/11354)）、仅含签名的 reasoning chunk（[#9534](https://github.com/earendil-works/pi/issues/9534)）。
- **确定性的压缩和缓存行为。** 文件列表无限增长（[#11297](https://github.com/earendil-works/pi/issues/11297)）、依赖 provider 字符串的溢出检测（[#10885](https://github.com/earendil-works/pi/issues/10885)）、闲置缓存预热被静默禁用（[#10956](https://github.com/earendil-works/pi/issues/10956)）。
- **扩展 API 指南与卫生。** 持久偏好的规范模式（[#11410](https://github.com/earendil-works/pi/issues/11410)）、避免非 prompt 事件上的崩溃（[#11366](https://github.com/earendil-works/pi/issues/11366)）、惰性编译 typebox 校验器（[#11367](https://github.com/earendil-works/pi/issues/11367)）、本地路径包的正确显示名（[#11392](https://github.com/earendil-works/pi/issues/11392)）。
- **终端模拟器正确性作为明确契约。** tmux OSC 52 透传（[#10628](https://github.com/earendil-works/pi/issues/10628)）、`stripAnsi` 中的私有参数 CSI（[#11377](https://github.com/earendil-works/pi/issues/11377)）、OSC 133 与 OSC 7501 冲突（[#11017](https://github.com/earendil-works/pi/issues/11017)）、iTerm2 全屏图片（[#11411](https://github.com/earendil-works/pi/pull/11411)）。
- **沙箱化 / 容器化的 agent 执行** —— Docker Hub `sbx/pi*` 讨论（[#11404](https://github.com/earendil-works/pi/discussions/11404)）加上 `inheritEnv`（[#11361](https://github.com/earendil-works/pi/issues/11361)）指向对有成文隔离方案的需求。

---

## 7. 开发者痛点

- **无诊断界面的静默失败。** 闲置缓存预热停止时没有任何警告（[#10956](https://github.com/earendil-works/pi/issues/10956)）；MCP OAuth 卡顿数分钟且 `Ctrl+C` 被忽略（[#11311](https://github.com/earendil-works/pi/issues/11311)）；`-e npm:` 永远复用陈旧缓存（[#11375](https://github.com/earendil-works/pi/issues/11375)）。用户只有检查内部事件日志才能发现这些问题。
- **跨平台和文件系统边界情况主导了 bug 数量。** Windows 路径分隔符（[#11399](https://github.com/earendil-works/pi/pull/11399)）、符号链接的 agent 目录（[#11396](https://github.com/earendil-works/pi/pull/11396)）、独立 CR 的 grep 偏移（[#11398](https://github.com/earendil-works/pi/issues/11398)）、块边界处的拆分 UTF-8（[#11330](https://github.com/earendil-works/pi/pull/11330)）、本地路径包的显示（[#11392](https://github.com/earendil-works/pi/issues/11392)）。
- **终端集成的脆弱性。** 多份独立报告显示复制 / 粘贴和转义序列处理在 tmux、iTerm2 和 `Ctrl+Shift+C` 下出现故障 —— 包括升级到 1.3.0 后输入的提示词丢失（[#11379](https://github.com/earendil-works/pi/issues/11379)）。这是用户可见困扰的最大单一集群。
- **快速发布节奏与回归风险。** 提示词丢失的报告恰好在 1.3.0 之后出现，而会话中期的扩展重载会污染整个 Anthropic 会话（[#11376](https://github.com/earendil-works/pi/issues/11376)）。用户实际上是在要求针对扩展变更的会话级韧性。
- **分诊吞吐量高但令人困惑。** 许多 Issue 在一天内以 `[no-action]` / `[last-read, no-action]` 关闭，而为同一个 provider-id 中含 `/` 的 bug 开了重复的 PR（[#11393](https://github.com/earendil-works/pi/pull/11393) 与 [#11397](https://github.com/earendil-works/pi/pull/11397)）。外部贡献者无法了解究竟什么会被接受 —— 一个 `good-first-issue` / "已接受，欢迎 PR" 的标签约定会减少无用功。
- **工具噪音侵蚀信任。** `pi update --extensions` 报告 npm 漏洞，而建议的 `npm audit fix` 无法解决 —— 且可能会将第二份 Pi 安装到扩展树中（[#11409](https://github.com/earendil-works/pi/issues/11409)）。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区简报 — 2026-10-26

## 1. 今日亮点

nightly 线已推进至 **v0.26.1-nightly.20261025**，重点包括带自动批准模式的托管会话权限规则，以及加固后的 `serve` 令牌路径。与此同时，**v0.26.1 的发布扇出仍不完整**：SDK `0.1.23` 的发布在约 6 小时的集成测试后被取消，托管运行时发布则停在不可变发布预检阶段。CI 健康状况继续主导活动，评论数最多的前三个 issue 全部是主分支门禁失败或运行时交付跟踪。

---

## 2. 发布

**v0.26.1-nightly.20261025.8e28d5380c**

- `feat(managed)`：会话权限规则和**自动批准**（[#15448](https://github.com/QwenLM/qwen-code/pull/15448)）——让托管会话可以预先声明权限策略，而不是每次工具调用都弹窗询问。
- `fix(cli)`：加固 `serve` 令牌处理——收紧守护进程认证令牌的传递和验证方式。

> 注意：这是一个 nightly。稳定的 `v0.26.1` 版本本身已发布，但下游发布产物（SDK、托管运行时）仍被阻塞——见 issue [#15498](https://github.com/QwenLM/qwen-code/issues/15498) 和 [#15499](https://github.com/QwenLM/qwen-code/issues/15499)。

---

## 3. 热门 Issue

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724) — 主 CI 失败：Managed Agent 门禁未识别到测试（38 条评论，OPEN）**
   仓库中最活跃的单个讨论串。一个由机器人提交、带 `autofix/skip` 的失败，无法归因到任何测试，自 10 月 19 日以来每天更新。这反映出更深层的问题：门禁无法定位自身失败，因此该 issue 不断累积评论噪音，而不是收敛。

2. **[#13395](https://github.com/QwenLM/qwen-code/issues/13395) — tracking(runtime)：Kubernetes 工具运行时进展与跨平台交付门禁（23 条评论）**
   K8s 工具运行时长期运行的交付台账。最新更新指出有两个 S1 修复位于 [#15457](https://github.com/QwenLM/qwen-code/pull/15457)，尚未合并或 ACK，且有 6 个 CI 失败归因于该 PR。实际上，它是托管运行时发布阻塞的总括项。

3. **[#15498](https://github.com/QwenLM/qwen-code/issues/15498) — [P1] SDK 0.1.23 发布被集成超时阻塞（11 条评论）**
   `@qwen-code/sdk@0.1.23` 从未发布：Release SDK 任务在卡在 *Run SDK Integration Tests* 约六小时后被取消。这是发布流水线测试预算已超出其超时配置的最明确信号。

4. **[#15499](https://github.com/QwenLM/qwen-code/issues/15499) — [P2] 托管运行时 v0.26.1 停在不可变发布预检（4 条评论）**
   与上一条相伴的失败：托管运行时发布运行通过了 release 任务，但在发布前停止。同一版本上两个独立发布门禁失败，说明扇出链缺少统一的可观测性入口。

5. **[#15486](https://github.com/QwenLM/qwen-code/issues/15486) — [P1] 自动压缩后，下一次请求重复计算待处理工具结果 + 20K 填充（3 条评论）**
   一个具有真实用户影响的正确性 bug：当工具结果仍处于待处理状态时，压缩可能触发，于是下一次请求的大小估算将其计算两次，再加 20K 填充——导致回合结束或强制 `max_tokens: 1`。相对于其较低的评论数，严重性很高。

6. **[#15542](https://github.com/QwenLM/qwen-code/issues/15542) — [P1] Anthropic 提供商：JSON 侧查询在 Sonnet 5.5 / Opus 5.5 上返回 400（3 条评论）**
   侧查询发送 `tool_choice: {"type":"any"}`，较新的 Claude 模型会拒绝。这是 0.26.1 中一个干净、可复现的提供商兼容性破坏，会阻塞任何使用 Anthropic 进行结构化侧任务的人。

7. **[#15526](https://github.com/QwenLM/qwen-code/issues/15526) — [P2] warn 模式只发送一次循环提醒，随后 59 次相同调用无防护运行（3 条评论）**
   报告者在一次会话中观察到 70 次字节完全相同的 `read_file` 调用，其中 59 次发生在唯一一次警告之后。与 [#15537](https://github.com/QwenLM/qwen-code/issues/15537) 一起看，后者显示警告在发送给模型前可能被完全丢弃，而遥测却记录为已送达。这对社区是有力证据：循环检测需要强制执行，而不仅仅是提示。

8. **[#15545](https://github.com/QwenLM/qwen-code/issues/15545) — [P3] feat(context)：阈值触发的交接延续（5 条评论）**
   提议在可配置的上下文填充水平写入交接报告，并在新上下文中继续，而不是原地摘要。认为自动压缩会在长时间自主运行中造成“静默质量衰减”——论述充分，可能影响路线图。

9. **[#15567](https://github.com/QwenLM/qwen-code/issues/15567) — [P2] MCP 启动失败时诊断信息省略子进程 stderr（4 条评论）**
   缺失 shebang 解释器只表现为“disconnected connection”。可操作的错误被吞掉了。小修复，开发者体验收益巨大——MCP 设置是常见的首次运行绊脚石。

10. **[#8432](https://github.com/QwenLM/qwen-code/issues/8432) — [P2] 百炼个人 Token 计划模型列表不同步；图像/视频生成失败（6 条评论，1 👍）**
    自 8 月以来一直开放，并且仍是热门集合中唯一有反应的 issue。内置 `/auth` 中面向阿里云 ModelStudio 的模型目录与控制台漂移，导致图像/视频生成失败。反映出硬编码提供商目录的维护负担。

**同样值得关注：** [#15513](https://github.com/QwenLM/qwen-code/issues/15513)（无头重试分类——目前只有“Retrying provider attempt”，没有原因或计数器）、[#15552](https://github.com/QwenLM/qwen-code/issues/15552)（目录快照在 #14129 后取代 `UserPromptSubmit` 去重回执），以及 [#15527](https://github.com/QwenLM/qwen-code/issues/15527)（自动化扫描器行池的合并后残留）。

---

## 4. 关键 PR 进展

1. **[#15296](https://github.com/QwenLM/qwen-code/pull/15296) — feat(web-shell)：允许嵌入宿主选择工作区目录**
   嵌入方可以通过可选回调为连接的守护进程提供自己的目录选择器，并带有中止信号，优先于原生选择。为需要自定义 FS 抽象的第三方宿主解除阻塞。

2. **[#15566](https://github.com/QwenLM/qwen-code/pull/15566) — feat(core)：持久终端身份查找**
   为已完成的本地激活和收件箱记录增加不可变的派生索引，日志保持权威，索引记录可从经验证的日志状态重建。这是精确一次语义的基础。

3. **[#15569](https://github.com/QwenLM/qwen-code/pull/15569) — feat(managed-agent)：原生日志快照存储**
   PR2 快照协议：写入方发布一个精确已提交头部的命名快照，幂等重试，并在写入方接管后读取经过验证的有界页面。

4. **[#15394](https://github.com/QwenLM/qwen-code/pull/15394) — fix(managed-agent)：将 serve 移除的 Broker 对传给池 Harness**
   Kubernetes Harness 池现在注入 `QWEN_MANAGED_RUNTIME_BROKER_URL` / `_TOKEN`，与 `qwen serve` 在缺少标志时读取的内容一致。直接解决 K8s 交付路径中的配置接线缺口。

5. **[#15564](https://github.com/QwenLM/qwen-code/pull/15564) — feat(managed-agent)：预检 Harness 兼容性、遗留 worker、实时服务**
   `--check` 新增三项首次安装/升级检查，包括在 `/capabilities` 返回 200 后构建并关闭一个真实的 `HostedHarnessClient`，以按运行时实际方式协商。

6. **[#15466](https://github.com/QwenLM/qwen-code/pull/15466) — feat(serve)：用户范围 Skill 激活路由**
   `PUT /skills/:name/activation` 外加批量变体（最多 100 个名称），与 `PUT /extensions/activation` 对应。填补了 Skills 只能按工作区切换的空白。

7. **[#15030](https://github.com/QwenLM/qwen-code/pull/15030) — feat(cli)：提高工作区 skill 安装限制**
   将传输上限与磁盘上限拆分（此前自 7 月以来一直是单一的 6 MiB / 6 MiB / 每文件 2 MiB / 128 文件预算）。这是现实世界 skill 包长期存在的实际限制。

8. **[#15547](https://github.com/QwenLM/qwen-code/pull/15547) — feat(cli)：在 review build-test 中只运行与 diff 相关的测试**
   `qwen review build-test` 现在将变更文件及其引用方传给 `vitest related`，适用于 `test` 脚本为普通 `vitest run` 的任何工作区。直接针对评审循环延迟。

9. **[#15578](https://github.com/QwenLM/qwen-code/pull/15578) — feat(autofix-pr)：在后台运行无人值守 watch 的修复代理**
   这是继 #15510 和 #15548 之后 `/autofix-pr` 可用性系列的第三部分；在修复代理工作时保持会话可用。值得注意，因为项目正在自动化自身的维护。

10. **[#14230](https://github.com/QwenLM/qwen-code/pull/14230) — feat(web-shell)：在 /memory 中浏览托管的自动记忆**
    新增“自动记忆”标签页，按范围分组列出条目。补充了核心中落地的记忆工作，并让用户能看到 agent 持久化了什么。

**本窗口还合并/关闭了：** [#15490](https://github.com/QwenLM/qwen-code/pull/15490)（固定托管扩展包——已关闭）、[#15543](https://github.com/QwenLM/qwen-code/pull/15543)（bootstrap 设置服务账号 + MySQL，并具有正确的所有者/模式——已关闭）、[#15393](https://github.com/QwenLM/qwen-code/pull/15393)（web-shell 工作区目录设置——已关闭）。

---

## 5. 热门讨论

*本周期未提供讨论数据。*

---

## 6. 功能请求趋势

- **超越压缩的上下文生命周期。** [#15545](https://github.com/QwenLM/qwen-code/issues/15545) 是一组 issue（连同 [#15486](https://github.com/QwenLM/qwen-code/issues/15486)、[#15552](https://github.com/QwenLM/qwen-code/issues/15552)）的领头项，主张原地摘要会在长时间自主运行中静默降低质量。需求正从“更好地压缩”转向“干净地交接至新上下文”。
- **托管 agent / Kubernetes 运行时加固。** Issue [#13395](https://github.com/QwenLM/qwen-code/issues/13395)、[#15499](https://github.com/QwenLM/qwen-code/issues/15499) 和 PR [#15394](https://github.com/QwenLM/qwen-code/pull/15394)、[#15564](https://github.com/QwenLM/qwen-code/pull/15564)、[#15569](https://github.com/QwenLM/qwen-code/pull/15569) 形成了一股连贯的推进，朝向运维级部署：预检、快照存储、持久身份、池接线。
- **内部机制的可观测性。** 要求展示 *为什么* agent 重试（[#15513](https://github.com/QwenLM/qwen-code/issues/15513)）、*为什么* MCP 失败（[#15567](https://github.com/QwenLM/qwen-code/issues/15567)），以及循环警告是否实际送达（[#15537](https://github.com/QwenLM/qwen-code/issues/15537)）——一个一致主题：诊断信息应面向用户，而不只是遥测。
- **生态系统可扩展性。** Skill 安装限制（[#15030](https://github.com/QwenLM/qwen-code/pull/15030)）、用户范围 skill 激活（[#15466](https://github.com/QwenLM/qwen-code/pull/15466)）、固定扩展包（[#15490](https://github.com/QwenLM/qwen-code/pull/15490)）和外部上下文提供方（[#15541](https://github.com/QwenLM/qwen-code/issues/15541)）都指向让 Qwen Code 成为第三方能力的宿主。
- **可嵌入性。** [#15296](https://github.com/QwenLM/qwen-code/pull/15296) 和 [#14230](https://github.com/QwenLM/qwen-code/pull/14230) 显示对 Web Shell 作为其他产品嵌入对象的持续投入，而不仅仅是 UI。

---

## 7. 开发者痛点

- **发布扇出是最薄弱环节。** `v0.26.1` 已发布，但 SDK 和托管运行时没有。持续六小时却被取消的集成测试运行（[#15498](https://github.com/QwenLM/qwen-code/issues/15498)）、预检中止（[#15499](https://github.com/QwenLM/qwen-code/issues/15499)），以及 GitHub 托管 runner 上 37–50 分钟的发布后扇出（[#15465](https://github.com/QwenLM/qwen-code/issues/15465)）叠加成一个维护者明显不信任的发布流程。
- **CI 噪音正在挤占真实工作。** [#14724](https://github.com/QwenLM/qwen-code/issues/14724)（38 条评论）、[#15173](https://github.com/QwenLM/qwen-code/issues/15173)、[#13009](https://github.com/QwenLM/qwen-code/issues/13009)、[#13015](https://github.com/QwenLM/qwen-code/issues/13015)、[#14796](https://github.com/QwenLM/qwen-code/issues/14796)、[#14104](https://github.com/QwenLM/qwen-code/issues/14104)——开放 issue 中有很大一部分是机器人提交的主分支失败，其中几个标注“未识别到测试”。主机争用下的不稳定断言（[#15341](https://github.com/QwenLM/qwen-code/issues/15341)，1/44,637）和 Maven Central 对共享 `ecs-qwen` 池限流（[#15546](https://github.com/QwenLM/qwen-code/issues/15546)）是被点名的根因。
- **压缩后的 token 核算很脆弱。** [#15486](https://github.com/QwenLM/qwen-code/issues/15486) 是一个 P1 正确性 bug；压缩、待处理工具结果和大小估算之间的交互似乎规范不足，而不仅仅是存在 bug。
- **提供商特定行为不断出问题。** 较新 Anthropic 模型上的强制 `tool_choice`（[#15542](https://github.com/QwenLM/qwen-code/issues/15542)）和漂移的百炼模型目录（[#8432](https://github.com/QwenLM/qwen-code/issues/8432)）都源于客户端中对提供商能力的内置假设。
- **agent 循环护栏只是建议性的，并未强制执行。** [#15526](https://github.com/QwenLM/qwen-code/issues/15526) 和 [#15537](https://github.com/QwenLM/qwen-code/issues/15537) 一起描述了一个护栏：触发一次、可能被静默丢弃，并且无论如何都记录成功——运行长时间自主任务的用户才是注意到问题的人。
- **诊断信息隐藏了可操作的错误。** MCP 子进程 stderr（[#15567](https://github.com/QwenLM/qwen-code/issues/15567)）和无头提供商重试（[#15513](https://github.com/QwenLM/qwen-code/issues/15513)）都只显示通用消息，而有用的细节仍留在内部。

---

*生成自 github.com/QwenLM/qwen-code 2026-10-26 的活动。评论数和状态反映所提供快照。*

</details>

---
*本日报由 [agents-radar](https://github.com/fireKylinKing/agents-radar) 自动生成。*