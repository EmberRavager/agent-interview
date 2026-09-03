# AI Agent 工程师学习总览（2026-09）

> 这一版不再按“知识点数量”组织，而按一个 Agent 从模型决策到生产运行的真实链路组织。

## 2026 的主线已经变了

旧的学习顺序通常是：

```text
Prompt → RAG → ReAct → Memory → Multi-Agent → Fine-tuning
```

现在更建议：

```text
Agent Loop
  ↓
Context Engineering
  ↓
Tool / MCP / Skills
  ↓
Runtime State
  ↓
Durable Execution
  ↓
Sandbox / Permission / HITL
  ↓
Tracing / Eval / Replay
  ↓
Production Reliability
```

原因很简单：模型越来越强之后，真正限制 Agent 质量的往往不再是“有没有某个 Pattern”，而是上下文、工具、状态、权限、恢复和评测闭环。

---

## P0：必须深入

### 1. Agent Loop

- model turn
- tool calling
- structured output
- stop condition
- budget / max turns
- streaming

### 2. Context Engineering

- system policy
- history
- working state
- retrieval / memory
- tool schemas
- token budget
- compaction / caching

### 3. Tools / MCP / Skills

- schema validation
- timeout / retry
- idempotency
- auth / permission
- tool discovery
- MCP
- reusable skills

### 4. Runtime & Durable Execution

- Run / Job
- Queue / Worker
- checkpoint / resume
- retry / cancel / timeout
- lease / heartbeat / fencing token
- event log / replay

### 5. Safety & Action Governance

- sandbox
- least privilege
- HITL
- prompt injection defense
- audit
- secret isolation

### 6. Observability & Eval

- trace
- tool span
- task completion rate
- cost / latency
- failure taxonomy
- eval set
- regression
- canary

---

## P1：强烈建议

### Coding / Browser / Computer Use Agent

重点不在“会不会点网页、会不会写代码”，而在：

```text
observe → act → verify → recover
```

以及 Harness：

- workspace
- shell
- filesystem
- skills
- tests
- browser verification
- session handoff
- progress artifact

### 协议层

- MCP：Agent ↔ Tool / Resource
- A2A：Agent ↔ Agent

---

## P2：按岗位和场景选修

### RAG

仍然重要，但降级成一种 Context Source / Tool。重点学检索质量和评测，不需要背所有 RAG 变体。

### Multi-Agent

先证明单 Agent + Tools 不够，再引入 manager / handoff / agents-as-tools / A2A。

### Fine-tuning / RL

应用工程岗位通常放在 Context、Tool、Runtime、Eval、Model Routing 优化之后。

---

## 推荐阅读顺序

1. [2026 Agent Engineer 学习路线 V2](19_agent_engineering_2026_v2.md)
2. [工具调用](04_tool_calling.md)
3. [系统设计](09_system_design.md)
4. [评估与可观测性](10_evals_and_observability.md)
5. [安全与治理](11_safety_and_governance.md)
6. [工程补充专题](15_engineering_addendum.md)
7. [分布式 / 高并发 / Redis](18_distributed_redis_high_concurrency.md)
8. 其余专题按需查阅

---

## 判断自己是否学到位

不要问“我背了多少道题”，问自己能不能设计下面这个系统：

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
Trace / Eval / Metrics / Audit
```

如果你能解释每一层为什么存在、怎么失败、怎么恢复、怎么观测，就已经进入真正的 Agent 工程视角。
