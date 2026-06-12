## 十五、2026 工程补充专题（10 题）

### 1. 什么是 Context Engineering？为什么现在比 Prompt Engineering 更重要？

**一句话：** Prompt Engineering 关注“提示词怎么写”，Context Engineering 关注“模型在这一轮到底看到了什么信息、以什么顺序看到、哪些该裁剪、哪些必须保留”。

| 维度 | Prompt Engineering | Context Engineering |
|------|--------------------|---------------------|
| 关注点 | 指令模板 | 运行时上下文装配 |
| 核心对象 | System / User Prompt | Prompt + Tool Schema + Memory + State + Retrieval + Policies |
| 问题本质 | 表达清楚 | 信息架构正确 |
| 工程重点 | 文案优化 | 上下文预算、排序、压缩、隔离、缓存 |

**高分回答：**
- 很多 Agent 失败不是 Prompt 写得差，而是给了错误、过期、冲突或过载的上下文
- 上下文工程的关键是：`选什么`、`按什么顺序放`、`哪些摘要`、`哪些原文保留`
- 真正生产级系统通常会把上下文构造成一个 pipeline，而不是单个字符串

**标准回答示例：**

> 我理解 Context Engineering 比 Prompt Engineering 更重要，是因为很多系统失败不是不会说，而是看错了信息。真正难的是该给什么上下文、按什么顺序给、哪些该压缩、哪些必须保留原文。

---

### 2. Durable Execution（可恢复执行）怎么设计？

**核心目标：** Agent 任务运行几分钟到几小时，进程挂了、模型超时、工具失败后，不能从头再来。

**典型设计：**
- 每一步执行后写入 checkpoint：当前计划、已完成步骤、工具输出、预算消耗
- 用任务状态机管理生命周期：`queued → running → waiting_tool → paused → completed / failed`
- 让工具调用带 `idempotency_key`，恢复时避免重复执行扣费或重复写库
- 把长任务拆成可重放的 step，而不是一个大 while loop

**面试官最想听：**
- 你如何从第 7 步恢复，而不是重跑前 6 步
- 你如何区分“可重试失败”和“不可重试失败”
- 你如何防止恢复后重复发邮件、重复下单、重复写数据库

**标准回答示例：**

> 可恢复执行的核心不是加个 checkpoint 这么简单，而是把长任务设计成可中断、可重放、可恢复的状态机。否则一旦任务跑到第 7 步挂了，就只能从头再来。

---

### 3. Computer Use Agent 和传统 API Agent 的差异是什么？

| 维度 | API Agent | Computer Use Agent |
|------|-----------|-------------------|
| 交互对象 | 结构化 API | GUI / 浏览器 / 桌面 |
| 观察 | JSON / 文本 | 截图、DOM、可访问性树、控件状态 |
| 动作 | HTTP 调用 | 点击、输入、拖拽、快捷键 |
| 失败根因 | 接口/参数错误 | 页面变化、元素漂移、视觉歧义、登录态失效 |
| 风险 | 数据错误 | 误点击、误提交、误支付 |

**结论：** Computer Use 是“弱结构化环境中的 Agent”，因此比 API Agent 更依赖验证、回退和权限确认。

**标准回答示例：**

> 两者最大的差异在于环境结构化程度不同，API 是强结构化，GUI 是弱结构化。弱结构化环境下，验证和权限确认就变得比“能不能操作”本身更重要。

---

### 4. Browser Agent 的 Grounding 为什么难？怎么做稳？

**难点：** 用户说“点右上角的导出”，模型必须准确映射到某个真实元素，但页面上可能有多个“导出”、隐藏按钮、懒加载 DOM、不同语言文案。

**常见做法：**
- 多信号融合：截图 + DOM + 可访问性树 + 可点击区域
- 候选排序：先找语义匹配元素，再结合位置、可见性、层级做 rerank
- 操作前验证：元素是否可见、是否可交互、是否被遮挡
- 操作后验证：URL 是否变化、DOM 是否更新、toast 是否出现、目标文件是否生成

**面试金句：** Web Agent 的核心不是“会操作”，而是“知道自己操作的是不是对的”。

**标准回答示例：**

> Browser Agent 的 grounding 难点在于自然语言目标和页面元素之间没有天然一一对应关系，所以必须做多信号融合和操作后验证。否则你以为点的是导出，实际上可能点成了删除。

---

### 5. 为什么 Agent Runtime 必须做权限分级和沙箱？

**因为 Agent 的风险不在“回答错一句话”，而在“把错的动作执行了”。**

**最少要有三层：**
- `read-only`：查询类工具，如搜索、读库、读文件
- `write-with-confirmation`：发消息、改工单、提交 PR、写数据库
- `high-risk-blocked-or-HITL`：支付、删库、批量发信、生产变更

**工程措施：**
- 工具白名单 + 参数 Schema 校验
- 每次调用绑定用户身份、租户、审批上下文
- 沙箱隔离文件系统、网络、凭证和执行权限
- 所有高风险动作都有审计日志和可回放记录

**标准回答示例：**

> 我会把权限分级理解成 Agent 从“会回答”走向“会行动”之后的基本盘。没有沙箱和分级，能力越强，系统反而越危险。

---

### 6. Agent 的幂等性（Idempotency）为什么这么重要？

**典型事故：** 模型超时重试了一次，结果发了两封邮件、创建了两张工单、扣了两次款。

**解决思路：**
- 所有副作用工具调用都带 `idempotency_key`
- 外部系统返回业务主键后，写入执行日志，后续重试先查重
- 对“生成内容”和“执行动作”分离：先生成草稿，再确认发送
- 设计补偿逻辑，例如撤销工单、回滚库存、发送更正消息

**判断标准：** “同一个 step 在网络抖动、进程重启、用户刷新后重复执行，系统结果是否仍然正确？”

**标准回答示例：**

> 幂等性的重要性在于 Agent 天然会重试、恢复、并发执行，如果没有幂等，重复发消息、重复下单、重复写库几乎是迟早的事。它本质上是可靠性问题。

---

### 7. 什么时候要做模型路由（Model Routing）？怎么答才专业？

**不要默认所有任务都上最强模型。**

**常见路由策略：**
- 简单问答 / 分类 / 抽取 → 小模型
- 复杂规划 / 多工具决策 / 高风险判断 → 推理模型
- 代码修复 / SQL 生成 / 文档结构化 → 领域更强的专用模型
- 超长上下文任务 → 长上下文模型或检索后压缩

**路由信号：**
- 任务类型
- 预算上限
- 延迟 SLA
- 历史失败率
- 是否涉及工具调用/推理/多模态

**加分点：** Router 本身也要评估，不然只是把复杂性从 Prompt 挪到了路由层。

**标准回答示例：**

> 模型路由不是为了炫多模型，而是为了在质量、延迟和成本之间做动态平衡。好的路由器能让贵模型只用在真正需要它的地方。

---

### 8. 为什么说 Eval Flywheel 比单次 Benchmark 更重要？

**单次 benchmark 只能告诉你“现在几分”，flywheel 才能让系统持续变好。**

```
线上失败案例 → 归因分类 → 构造评测集 → 修 prompt / tool / memory / router → 回归测试 → 再上线
```

**真正有价值的评测集来源：**
- 真实用户失败轨迹
- 高风险边界样本
- 回归 bug case
- 合成的对抗样本

**面试高分点：** 评估不仅评“答案对不对”，还要评 `tool success rate`、`task completion rate`、`replan rate`、`human takeover rate`

**标准回答示例：**

> 我会把 Eval Flywheel 理解成把线上失败变成持续改进资产，而不是一次性 benchmark。没有这个闭环，系统每次优化都像在盲飞。

---

### 9. Agent 的状态存储应该选 Snapshot 还是 Event Sourcing？

| 方案 | 优点 | 缺点 | 适用场景 |
|------|------|------|----------|
| Snapshot | 读快、恢复简单 | 丢失中间过程细节 | 短任务、低审计要求 |
| Event Sourcing | 可审计、可回放、易归因 | 设计复杂、回放成本高 | 长任务、合规、调试、评测平台 |

**实践建议：**
- 在线执行用 snapshot 提高恢复速度
- 审计/回放保留 event log
- 两者结合最常见：`snapshot for serve, events for truth`

**标准回答示例：**

> 两者本质上是服务效率和审计可追踪性的取舍，snapshot 恢复快，event sourcing 更适合回放和归因。很多成熟系统最后其实会两者结合。

---

### 10. 2026 年 AI Agent 面试最容易被追问的工程指标有哪些？

**不要只说模型准确率。至少准备以下指标：**

- `Task Completion Rate`：任务最终完成率
- `Tool Success Rate`：工具调用成功率
- `First-Pass Success Rate`：无需重试直接成功的比例
- `Avg Steps / Replan Rate`：平均步数、重规划率
- `P95 Latency / Cost per Task`：时延和单任务成本
- `Human Takeover Rate`：转人工率
- `Unsafe Action Block Rate`：高风险动作被护栏拦截比例

**面试官为什么爱问这个：**
- 因为这能区分“做过 demo”还是“真的上线过”
- 真正的生产经验，最后都会落到成功率、成本、时延、风险和恢复能力上

---

### 附：Claude Code（CC）、Codex 和其他主流 AI 编码软件的主要区别

> 面试里如果被问到“你用过哪些 AI coding agent，它们有什么差别”，不要只回答“模型不一样”，要从**产品定位、运行环境、自治程度、上下文能力、生态集成、治理能力**来讲。

**更重要的是：不要用“谁更强”来答，要用“谁更适合什么工作方式”来答。**

**如果这里的 `CC` 指的是 `Claude Code`，可以这样答：**

| 产品 | 更偏向的定位 | 主要形态 | 典型强项 | 更适合的场景 |
|------|--------------|----------|----------|--------------|
| Claude Code | `coding agent` | Terminal / IDE / Desktop / Web | 代码库理解、跨文件改动、命令执行、MCP、`CLAUDE.md`、skills/hooks、跨端连续工作 | 让一个较自主的编码 Agent 长时间干活 |
| Codex | `coding agent + agent platform` | App / IDE / CLI / Web | Worktrees、local environments、in-app browser、computer use、automations、plugins、skills、subagents、GitHub/Slack/Linear 集成 | 既做编码，又做更广义的工具编排和长任务执行 |
| Cursor | `IDE-first AI coding environment` | 桌面 IDE + CLI + cloud agents | Tab 自动补全、代码库索引、IDE 内交互、并行 cloud agents、多模型选择 | 重度 IDE 用户，希望“边写边改边审”一体化 |
| GitHub Copilot | `GitHub-native AI workflow layer` | GitHub / IDE / CLI / cloud agent | GitHub 工作流、PR/issue 关联、云端 agent、企业控制面、审计、MCP allowlist | 团队已经深度在 GitHub 上协作，希望把 AI 直接嵌进现有研发流程 |

**怎么用一句话区分：**
- **Claude Code**：更像“能读代码、改代码、跑命令、接 MCP 的自主编码同事”
- **Codex**：更像“编码 Agent + 通用 Agent runtime”，产品面更宽，强调本地环境、浏览器、自动化、子代理和插件化
- **Cursor**：更像“以 IDE 为中心的 AI 开发环境”，人机协作体验和编辑流很强
- **GitHub Copilot**：更像“嵌在 GitHub 与 IDE 工作流里的 AI 加速层”，尤其适合 PR、issue、代码审查和企业治理

**面试高分回答角度：**

1. **交互中心不同**
- Cursor 更偏 IDE 中心
- Claude Code 和 Codex 更偏 agent/session 中心
- Copilot 更偏 GitHub 工作流中心

2. **自治程度不同**
- Tab/补全类产品偏“人主导，AI 辅助”
- Claude Code、Codex、Cursor cloud agents 这类更偏“你下目标，Agent 自己拆解执行”
- 面试时可以把它们放到前面“L2-L3 自主性分级”那一题里去讲

3. **上下文与记忆机制不同**
- Claude Code 强调 `CLAUDE.md`、auto memory、skills、hooks
- Codex 强调 `AGENTS.md`、skills、plugins、subagents、conversation state/automation
- Cursor 强调代码库索引、规则、IDE 内上下文和多模型选择
- Copilot 更强在 GitHub 上下文、issue/PR/repo 流程和组织级控制

4. **生态集成方向不同**
- Claude Code：MCP 很强，适合接外部工具和知识源
- Codex：MCP、connectors、browser/computer use、Slack/Linear/GitHub 更全
- Copilot：GitHub 原生最强，企业里治理和权限落地更顺
- Cursor：编辑器和 agent 体验强，但更偏“开发环境产品”而不是“通用企业 agent 平台”

5. **企业治理关注点不同**
- Copilot 很强调企业控制面、审计和 agent 管理
- Codex 也很强调 permissions、sandboxing、review、安全插件和自动化
- Claude Code 强调 permission modes、session 管理、routines 和可扩展治理
- 真正选型时，不只是看“会不会写代码”，还要看日志、权限、审批、合规和可回放

**一个比较稳的面试总结：**

> 如果团队想要的是“最顺手的 IDE 体验”，我会优先看 Cursor；如果要“把 AI 深度嵌到 GitHub 研发流程”，Copilot 很自然；如果要“高自主编码 Agent + MCP 扩展能力”，Claude Code 很强；如果要“编码之外再往通用 Agent 平台、自动化和多工具编排走”，Codex 的产品形态更宽。

**注意：** 这题没有绝对正确答案。高分不在于你站队谁，而在于你能把比较维度讲清楚，并且能根据团队场景给出选型理由。

**标准回答示例：**

> 如果让我比较 Claude Code、Codex、Cursor 和 Copilot，我不会简单说谁更强，而会先看它们的工作中心在哪里。Cursor 更偏 IDE 内的人机协作，Copilot 更强在 GitHub 工作流和企业治理，Claude Code 更像高自主编码 Agent，Codex 则更像把编码 Agent 和通用 Agent runtime 结合得更深，所以最后选型不应该按品牌站队，而应该按团队的开发方式、权限要求和自动化深度来定。

---
