# Agent Engineering Handbook · 2026

面向 2026 年 `AI Agent / Agent Runtime / Coding Agent / AI Platform` 工程师的学习与面试手册。

## 核心观点

不要再按下面的旧路线学习：

```text
Prompt → RAG → ReAct → Multi-Agent → Fine-tuning
```

新版主线：

```text
Agent Loop
→ Context Engineering
→ Tools / MCP / Skills
→ Runtime & Durable Execution
→ Harness / Sandbox
→ Safety / HITL
→ Tracing / Evals
→ Production System Design
```

## 章节

1. [Agent 基础与模型能力](guide/01_agent_foundations.md)
2. [Context Engineering](guide/02_context_engineering.md)
3. [Tools、MCP、Skills 与 A2A](guide/03_tools_protocols.md)
4. [Agent Runtime 与 Durable Execution](guide/04_runtime_durable_execution.md)
5. [Harness、Coding Agent 与 Computer Use](guide/05_harness_coding_agents.md)
6. [Safety、Permission 与 HITL](guide/06_safety_permission_hitl.md)
7. [Observability、Tracing 与 Evals](guide/07_observability_evals.md)
8. [Production System Design](guide/08_production_system_design.md)
9. [2026 面试路线与技术雷达](guide/09_interview_roadmap.md)

## 建议学习顺序

先读 [总览](guide/00_overview.md)，然后按 01 → 09 顺序学习。

每一章都只保留三类内容：

- **必须理解的核心模型**
- **生产工程问题**
- **高频面试问题**

不再追求“150+ 题”，目标是把真正高频、长期有效的能力学透。

## 你最终应该能做什么

你应该能设计并解释：

```text
Client
→ API / Auth / Tenant
→ Admission
→ Run + Job
→ Queue / Worker
→ Agent Runtime
   ├─ Context Builder
   ├─ Model Gateway
   ├─ Tool Gateway
   ├─ Sandbox
   ├─ Checkpoint / Resume
   ├─ Approval
   └─ Events
→ Trace / Eval / Metrics / Audit
```

并能回答：

- 任务为什么失败？
- 任务如何恢复？
- Tool 如何避免重复副作用？
- 1000 个并发任务如何排队？
- Agent 如何安全获得 Shell / Browser 权限？
- Prompt / Model / Tool 改动后如何防止回归？

线上阅读：<https://emberravager.github.io/agent-interview/guide/>
