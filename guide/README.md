# Agent Engineering Handbook · 2026

> 更新时间：2026-09-03

这套资料已经完全重构，不再保留旧的“150+ 面试题百科”结构。

## 学习地图

```text
01 Agent Foundations
        ↓
02 Context Engineering
        ↓
03 Tools / MCP / Skills / A2A
        ↓
04 Runtime / Durable Execution
        ↓
05 Harness / Coding / Computer Use
        ↓
06 Safety / Permission / HITL
        ↓
07 Observability / Evals
        ↓
08 Production System Design
        ↓
09 Interview Roadmap
```

## 章节入口

- [00 · 总览](00_overview.md)
- [01 · Agent 基础与模型能力](01_agent_foundations.md)
- [02 · Context Engineering](02_context_engineering.md)
- [03 · Tools、MCP、Skills 与 A2A](03_tools_protocols.md)
- [04 · Agent Runtime 与 Durable Execution](04_runtime_durable_execution.md)
- [05 · Harness、Coding Agent 与 Computer Use](05_harness_coding_agents.md)
- [06 · Safety、Permission 与 HITL](06_safety_permission_hitl.md)
- [07 · Observability、Tracing 与 Evals](07_observability_evals.md)
- [08 · Production System Design](08_production_system_design.md)
- [09 · 2026 面试路线与技术雷达](09_interview_roadmap.md)

## 学习原则

1. 先理解控制流和系统边界，再学框架 API。
2. RAG、Multi-Agent、Fine-tuning 都不是默认中心。
3. Tool 必须和权限、幂等、重试、审计一起学。
4. 长任务必须考虑 checkpoint / resume。
5. Coding / Browser Agent 必须强调 sandbox 与 verification。
6. 所有线上失败都应该进入 Eval Flywheel。
7. 最终用 completion rate、latency、cost、reliability 和 safety 衡量系统。
