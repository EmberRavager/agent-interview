# agent-interview

面向 `AI Agent / LLM Agent / Agent Runtime / Coding Agent` 工程师的系统化学习与面试资料。

> **2026-09 更新：学习主线已调整。**
>
> 不再把 `Prompt → RAG → ReAct → Multi-Agent → Fine-tuning` 当作默认主线，而是优先学习 `Agent Loop → Context → Tool/MCP/Skills → Runtime State → Durable Execution → Sandbox/HITL → Trace/Eval → Production Reliability`。

## 先看新版路线

- **2026 Agent Engineer 学习路线 V2**：[guide/19_agent_engineering_2026_v2.md](guide/19_agent_engineering_2026_v2.md)
- 在线学习首页：[https://emberravager.github.io/agent-interview/guide/](https://emberravager.github.io/agent-interview/guide/)
- 旧版完整题库仍然保留，适合查漏补缺，不建议从头机械背完。

## 为什么要更新

Agent 工程的重点已经明显从“会几个 Agent Pattern”转向“能不能把 Agent 做成可靠的软件系统”。

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

真正能区分 Demo 和生产系统的问题通常是：

- 模型这一轮到底看到了什么上下文？
- Tool 为什么会被调用，参数和权限怎么约束？
- Run 跑到一半挂了怎么恢复？
- 重试如何避免重复副作用？
- Shell / Browser / File Edit 如何进入沙箱？
- 什么操作需要 HITL？
- 线上失败如何被 Trace、归因并进入 Eval 回归集？
- 1000 个任务同时提交时 Queue、Worker、Backpressure 怎么设计？

## 2026 核心学习地图

| 优先级 | 模块 | 需要掌握的核心 |
|---|---|---|
| P0 | Agent Loop | tool calling、structured output、turn、budget、stop condition |
| P0 | Context Engineering | history、memory、retrieval、tool schema、token budget、compaction |
| P0 | Tool / MCP / Skills | schema、auth、timeout、retry、idempotency、tool discovery、skills |
| P0 | Runtime & State | Run、Job、Queue、Worker、Checkpoint、Resume、Cancel |
| P0 | Reliability | lease、heartbeat、fencing token、backpressure、dedup、compensation |
| P0 | Safety | sandbox、least privilege、HITL、audit、prompt injection defense |
| P0 | Observability & Eval | trace、replay、metrics、eval set、failure taxonomy |
| P1 | Coding / Browser Agent | shell、filesystem、apply patch、computer use、verification、harness |
| P1 | Protocol | MCP、A2A、enterprise integration |
| P2 | RAG | hybrid search、rerank、retrieval eval；按场景使用 |
| P2 | Multi-Agent | manager、handoff、agents-as-tools；先证明单 Agent 不够 |
| P2 | Fine-tuning / RL | context/tool/runtime/eval 优化后再考虑 |

## 7 天速刷路线 V2

| 天数 | 主题 | 目标 |
|---|---|---|
| Day 1 | Agent Loop + Tool Calling | 能不用框架写出一个最小 Agent Loop |
| Day 2 | Context Engineering | 能设计 Context Builder、预算和压缩策略 |
| Day 3 | Runtime + Durable Execution | 能画 Run / Job / Checkpoint / Resume 状态机 |
| Day 4 | Tool Governance + MCP + Skills | 能讲清权限、幂等、协议和能力封装 |
| Day 5 | Sandbox + Coding / Browser Agent | 理解搜索→行动→验证闭环与 Harness |
| Day 6 | Trace + Eval + Safety | 能把线上失败沉淀为 Eval Case |
| Day 7 | Production System Design | 完整设计一个支持长任务和并发的 Agent Runtime |

## 旧版题库怎么用

旧章节仍然有价值，但建议从“主线教材”改成“专题词典”。

### 核心专题

1. [基础概念](guide/01_fundamentals.md)
2. [LLM 底层原理](guide/02_llm_foundations.md)
3. [Agent 设计模式](guide/03_agent_patterns.md)
4. [工具调用与 Function Calling](guide/04_tool_calling.md)
5. [记忆系统设计](guide/05_memory_systems.md)
6. [RAG 与 Agentic RAG](guide/06_rag_and_agentic_rag.md)
7. [规划与推理](guide/07_planning_and_reasoning.md)
8. [多 Agent 协作](guide/08_multi_agent.md)
9. [系统设计与架构](guide/09_system_design.md)
10. [评估与可观测性](guide/10_evals_and_observability.md)
11. [安全与治理](guide/11_safety_and_governance.md)
12. [训练微调与对齐](guide/12_training_and_alignment.md)
13. [生产环境与 MLOps](guide/13_production_and_mlops.md)
14. [前沿热点](guide/14_frontier_topics.md)
15. [工程补充专题](guide/15_engineering_addendum.md)
16. [面试策略](guide/16_interview_strategy.md)
17. [2026 近期线上题库](guide/17_recent_agent_interview_questions_2026.md)
18. [分布式 / Redis / 高并发](guide/18_distributed_redis_high_concurrency.md)
19. [2026 Agent Engineer 学习路线 V2](guide/19_agent_engineering_2026_v2.md)

## 建议重点删除的学习焦虑

不需要为了成为 Agent 工程师而死背：

- 大量 Prompt 模板名
- 每一种 ReAct 变体
- 各种 Multi-Agent 角色扮演框架
- 过细的 MARL 算法
- 所有 RAG 变体名称
- 所有框架 API
- 所有模型榜单和发布日期

更应该深入：

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

## 适合什么岗位

- AI Agent 应用工程师
- Agent Runtime / Agent Platform 工程师
- Coding Agent 工程师
- LLM 应用工程师
- AI Infra / AI Platform 工程师
- 需要设计长任务、工具调用和智能工作流的后端工程师

## 资料结构

- [guide/19_agent_engineering_2026_v2.md](guide/19_agent_engineering_2026_v2.md)：新版学习主线
- [guide/](guide/)：专题拆分版资料
- [guide/index.html](guide/index.html)：GitHub Pages 在线学习首页
- [guide/AI_Agent_Interview_Guide_Combined.md](guide/AI_Agent_Interview_Guide_Combined.md)：旧版完整 Markdown 合集
- [ai_agent_interview_guide.md](ai_agent_interview_guide.md)：原始整合版

## 学到什么程度算合格

最终目标不是背完 150 道题，而是能够清楚地说：

> 我能把模型、Context、Tools、Skills、MCP、State、Worker、Sandbox、Permission、Checkpoint、Tracing、Eval 组合成一个可运行、可恢复、可审计、可扩展的 Agent 系统，并且知道每一层失败时应该怎么定位和治理。
