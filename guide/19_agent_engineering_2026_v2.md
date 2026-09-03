# 2026 Agent Engineer 学习路线 V2

> 更新时间：2026-09-03
>
> 目标：从“背 Agent 概念”升级为“能设计、实现、调试、恢复和治理一个真实 Agent Runtime”。

## 先说结论

2026 年再把 Agent 学习主线理解成 `Prompt → RAG → ReAct → Multi-Agent → Fine-tuning`，已经有些落后了。

现在更值得优先掌握的是：

```text
Model Capability
    ↓
Agent Loop
    ↓
Context Engineering
    ↓
Tool / MCP / Skills
    ↓
Runtime State + Durable Execution
    ↓
Sandbox / Permission / HITL
    ↓
Tracing + Eval + Replay
    ↓
Production Reliability
```

真正拉开 Agent 工程师差距的，通常不是“知道多少 Agent 名词”，而是能不能回答下面这些问题：

- 模型这一轮到底看到了什么？为什么？
- Tool 为什么会被选中？参数怎么校验？副作用怎么控制？
- Run 跑到第 8 步机器挂了，怎么从第 8 步继续？
- Tool 超时后重试，怎么避免重复发邮件、重复扣费、重复写库？
- Agent 能执行 Shell、浏览器、文件修改后，权限边界在哪里？
- 为什么线上成功率下降？是模型、Context、Tool、状态、网络还是策略的问题？
- 一次修复怎么变成长期 Eval Case，而不是下次再犯？

---

# 一、核心主线：8 个模块

## 1. Model & Agent Loop

### 必会

- LLM 输入输出、token、context window、structured output
- reasoning model 与普通模型的差异
- tool calling / function calling
- Agent Loop：`observe → decide → act → observe`
- stopping condition、max turns、budget
- streaming 与非 streaming

### 不需要过度投入

- 手写很复杂的 CoT Prompt
- 记大量早期 Prompt 技巧
- 把 ReAct 当作某个必须原样实现的模板

### 面试标准

你应该能解释：

> Agent 本质上不是一个 Prompt，而是一个带状态的模型执行循环。模型负责决策，Runtime 负责上下文、工具执行、状态、权限、恢复和可观测性。

---

## 2. Context Engineering

这是 2026 年最重要的 Agent 基础之一。

### 必会

- system / developer / user / tool result 的优先级和边界
- conversation history
- working memory
- retrieval context
- tool schema
- runtime state
- user / tenant / permission context
- token budget
- context trimming / compaction / summarization
- context caching
- stale context / conflicting context

### 重点理解

Prompt Engineering 问的是：

> “这句话怎么写？”

Context Engineering 问的是：

> “这一轮模型应该看到哪些信息，为什么是这些，顺序是什么，哪些必须原文保留，哪些可以压缩？”

### 一个生产级 Context Pipeline

```text
Trusted System Policy
    ↓
Agent Config Snapshot
    ↓
User / Tenant / Permission Context
    ↓
Conversation State
    ↓
Relevant Memory / Retrieval
    ↓
Tool Availability + Schemas
    ↓
Current User Input
    ↓
Token Budget / Compaction
    ↓
Model Input
```

---

## 3. Tools / MCP / Skills

Tool Calling 不能只学“JSON Schema 怎么定义”。

### Tool 必会

- schema validation
- timeout
- retry / backoff
- idempotency
- auth propagation
- permission scope
- rate limit
- circuit breaker
- tool result normalization
- side-effect classification
- approval before write

### MCP 必会

MCP 应该理解为 Agent 的标准能力连接层，而不是“另一种 Function Calling”。

重点：

- tools / resources / prompts
- transport 与 remote server
- authentication / authorization
- trust boundary
- tool discovery
- enterprise gateway
- long-running task abstraction

### 2026-07-28 MCP 需要知道的新变化

- 核心协议进一步转向 stateless
- request 自描述，减少 transport session 依赖
- header-based routing
- list result caching
- authorization hardening
- Tasks 进入 extension
- extensions 成为正式扩展机制
- legacy HTTP+SSE 等旧机制开始进入 deprecation 路线

### Skills 为什么变重要

Skill 更适合表达：

```text
instructions
+ scripts
+ templates
+ examples
+ domain resources
```

它解决的是“如何把程序化能力和领域操作经验打包给 Agent”，比把所有知识都塞进 System Prompt 更可维护。

---

## 4. Agent Runtime & Durable Execution

如果想做 Agent 平台 / Runtime，这是最该深入的一章。

### 一个 Run 至少应该有

```text
Run
├── run_id
├── agent_version / config_snapshot
├── status
├── input
├── current_step
├── model_usage
├── checkpoints
├── tool_calls
├── events
├── approvals
└── final_output / error
```

### 状态机

```text
PENDING
  ↓
RUNNING
  ├── WAITING_TOOL
  ├── WAITING_INPUT
  ├── WAITING_APPROVAL
  ├── RETRYING
  └── PAUSED
  ↓
SUCCEEDED / FAILED / CANCELLED / TIMED_OUT
```

### 必会

- checkpoint
- resume
- replay
- lease / heartbeat
- fencing token
- retry policy
- deterministic step boundary
- job queue
- worker pool
- cancellation
- timeout
- dead letter
- backpressure

### 幂等

所有有副作用的动作都要问：

> “同一个 step 被执行两次会发生什么？”

常见方案：

- idempotency key
- execution ledger
- external business key
- deduplication
- transactional outbox
- compensation

---

## 5. Sandbox / Computer Use / Coding Agent

2026 年 Agent 已经越来越像“有电脑权限的软件”。

### 必会能力

- filesystem
- shell
- browser / computer use
- apply patch
- code execution
- workspace
- snapshot
- network policy
- secrets isolation

### Coding Agent 的核心闭环

```text
Understand Task
    ↓
Search Repository
    ↓
Read Relevant Context
    ↓
Plan
    ↓
Edit
    ↓
Run Test / Lint / Build
    ↓
Inspect Failure
    ↓
Repair
    ↓
Diff / PR
```

真正重要的是 Harness，而不是让模型“一次写对”。

### Harness Engineering

Harness 可以理解成模型之外的“工作环境 + 规则 + 验证闭环”。

包括：

- repo instructions
- task files
- skills
- tools
- sandbox
- checkpoints
- tests
- linters
- browser verification
- progress artifacts
- session handoff

好的 Harness 会让中等模型表现得更稳定；差的 Harness 会让强模型也不断迷路。

---

## 6. Safety / Permission / HITL

Agent 从“生成内容”变成“执行动作”以后，安全模型必须升级。

### 建议按动作风险分级

| 风险 | 示例 | 策略 |
|---|---|---|
| Low | 搜索、读取、分析 | 自动执行 |
| Medium | 创建草稿、修改非关键数据 | 执行 + 审计 |
| High | 发消息、提交 PR、修改业务数据 | HITL / confirmation |
| Critical | 支付、删库、生产变更、批量外发 | 强审批 / 默认禁止 |

### 必会

- least privilege
- user / tenant isolation
- approval checkpoint
- tool allowlist
- parameter validation
- prompt injection defense
- secret isolation
- audit log
- replay
- policy enforcement

---

## 7. Observability & Eval

只看日志不叫 Agent 可观测性。

### Trace 至少应该能看到

```text
run
 ├── model turn
 │    ├── input context
 │    ├── output
 │    └── token / latency
 ├── tool call
 │    ├── args
 │    ├── result
 │    └── error / retry
 ├── handoff
 ├── approval
 └── final result
```

### 核心指标

- Task Completion Rate
- First-Pass Success Rate
- Tool Success Rate
- Avg Turns / Avg Steps
- Replan Rate
- Human Takeover Rate
- P50 / P95 Latency
- Cost per Task
- Retry Rate
- Resume Success Rate
- Unsafe Action Block Rate

### Eval Flywheel

```text
Production Failure
    ↓
Failure Taxonomy
    ↓
Add Eval Case
    ↓
Fix Prompt / Context / Tool / Runtime / Model
    ↓
Regression Eval
    ↓
Canary
    ↓
Production
```

这比“跑一次 benchmark”重要得多。

---

## 8. Production Agent System Design

### 你应该能画出的架构

```text
Client
  ↓
API / Auth / Tenant
  ↓
Run Admission
  ↓
Run + Job Store
  ↓
Queue
  ↓
Worker Pool
  ↓
Agent Runtime
  ├── Context Builder
  ├── Model Gateway
  ├── Tool Gateway
  ├── Sandbox
  ├── Checkpoint
  ├── Approval
  └── Event Bus
  ↓
Tracing / Eval / Metrics / Audit
```

### 高频系统设计问题

- 1000 个 Agent Run 同时提交怎么办？
- Worker concurrency 怎么定？
- 如何做 backpressure？
- 如何做 queue fairness？
- 如何支持长任务？
- Run 和 Job 为什么要分开？
- SSE 断开为什么不能导致任务取消？
- 如何恢复执行？
- 如何做 exactly-once effect，而不是幻想 exactly-once execution？
- Tool 调用失败怎么分类 retry？
- 多副本 Worker 怎么防止重复消费？
- 配置版本如何冻结？
- 如何灰度新 Prompt / Model / Tool？

---

# 二、从主线降级为“场景选修”的内容

## RAG

仍然重要，但不要再把 RAG 当成 Agent 的中心。

重点学：

- hybrid search
- rerank
- metadata filter
- query rewrite
- retrieval eval
- citation / grounding
- retrieval as tool

不用为了面试花大量时间背各种 RAG 变体名称。

## Multi-Agent

先问：

> 单 Agent + Tools 能不能解决？

只有这些情况再考虑多 Agent：

- clear domain specialization
- security boundary
- independent context
- parallel exploration
- organizational ownership boundary

重点掌握：

- manager / handoff
- agents-as-tools
- shared state vs isolated state
- delegation contract
- A2A

不要把“Agent 越多越高级”当成设计原则。

## Fine-tuning / RL

对多数 Agent 应用工程岗位，不应该排在主线前面。

先优化：

```text
Context
→ Tool
→ Runtime
→ Eval
→ Model choice
→ Prompt
```

这些都解决不了稳定性或成本问题，再评估 fine-tuning / distillation / RL。

---

# 三、协议层：MCP 与 A2A

## MCP

```text
Agent / Host
    ↓
MCP
    ↓
Tool / Resource / Enterprise System
```

解决纵向能力连接。

## A2A

```text
Agent A
   ↕
  A2A
   ↕
Agent B
```

解决跨 Agent 的发现、委派、任务生命周期和互操作。

2026 年 A2A 已进入 Agentic AI Foundation 体系，越来越适合从“协议和任务边界”而不是“多 Agent 聊天”角度理解。

---

# 四、2026 年不要再花太多时间死背的东西

以下内容知道概念即可：

- 大量 Prompt 模板名
- 每种 ReAct 变体
- 各种 Multi-Agent 角色扮演框架
- 过细的 MARL 算法
- 为了 Agent 而 Agentic RAG
- 只会比较 LangChain / CrewAI / AutoGen 谁更强
- 只背模型发布日期和榜单

真正应该深入的是：

```text
State
Context
Tool
Runtime
Reliability
Permission
Eval
Harness
```

---

# 五、推荐的 7 天学习路线

| Day | 主题 | 产出 |
|---|---|---|
| 1 | Agent Loop + Tool Calling | 手写一个最小 Agent Loop |
| 2 | Context Engineering | 设计 Context Builder + token budget |
| 3 | Runtime + State + Durable Execution | 画 Run / Job / Checkpoint 状态机 |
| 4 | Tool Governance + MCP + Skills | 实现一个有权限和幂等控制的工具 |
| 5 | Sandbox + Coding / Browser Agent | 做一次“搜索→修改→验证”的闭环 |
| 6 | Trace + Eval + Safety | 为失败案例建立 eval set |
| 7 | System Design | 完整讲一遍 Production Agent Runtime |

---

# 六、推荐的 4 周工程路线

## Week 1：能跑

- Agent Loop
- Tool Calling
- Structured Output
- Context Builder
- Streaming

目标：自己不用框架也能写一个最小 Runtime。

## Week 2：能稳定跑

- Run / Job
- Queue / Worker
- Retry / Timeout
- Idempotency
- Checkpoint / Resume
- Cancellation

目标：任务不会因为进程重启就全丢。

## Week 3：敢让它执行动作

- Permission
- HITL
- Sandbox
- Browser / Computer Use
- Coding Agent
- MCP / Skills

目标：Agent 可以执行真实动作，但动作可控。

## Week 4：知道它为什么失败

- Trace
- Metrics
- Eval
- Replay
- Failure Taxonomy
- Canary / Rollback
- Model Routing

目标：从“Demo 能跑”升级到“生产系统能维护”。

---

# 七、面试最值得准备的 20 个问题

1. Agent 和普通 Chat Completion 的本质区别是什么？
2. 你会怎么设计一个 Agent Loop？
3. Context Engineering 解决什么问题？
4. 为什么 Tool Schema 设计会直接影响成功率？
5. Tool 调用怎么做超时、重试和幂等？
6. MCP 和 Function Calling 的关系是什么？
7. MCP 和 A2A 分别解决什么问题？
8. Skill 和 Tool 有什么区别？
9. 长任务如何 checkpoint / resume？
10. Run 和 Job 为什么最好分开？
11. Worker 怎么防止重复执行？
12. SSE 断开后任务应该发生什么？
13. Agent 如何安全执行 Shell / Browser / File Edit？
14. Prompt Injection 怎么防？
15. HITL 应该放在哪些边界？
16. Agent Trace 应该记录什么？
17. 怎么评估一个 Agent 是否真的变好了？
18. 线上失败如何形成 Eval Flywheel？
19. 什么时候需要 Multi-Agent，什么时候不需要？
20. 设计一个支持 1000 个并发任务的 Agent Runtime。

---

# 八、最终能力标准

如果你能独立回答下面这句话，基本已经从“Agent 应用开发”进入“Agent 工程 / Runtime”视角：

> 我能把模型、Context、Tools、Skills、MCP、State、Worker、Sandbox、Permission、Checkpoint、Tracing、Eval 组合成一个可运行、可恢复、可审计、可扩展的 Agent 系统，并且知道每一层失败时应该怎么定位和治理。
