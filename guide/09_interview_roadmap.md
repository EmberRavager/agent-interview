# 09 · 2026 面试路线与技术雷达

> 更新时间：2026-09-03。目标：把知识转换成能在面试中表达、能在项目中落地的能力。

## 1. 2026 最值得准备的方向

### P0

- Agent Loop
- Context Engineering
- Tool Calling / MCP / Skills
- Run / Job / Queue / Worker
- Durable Execution
- Idempotency
- Sandbox / Permission / HITL
- Tracing / Eval / Replay
- Production System Design

### P1

- Coding Agent / Harness Engineering
- Browser / Computer Use
- A2A
- Model Routing
- Multi-tenant governance
- cost / latency optimization

### P2

- RAG advanced variants
- Multi-Agent patterns
- fine-tuning / distillation / RL

P2 不是没价值，而是不应该比 Runtime、Context、Safety 更早学。

## 2. 当前技术雷达

### Adopt / 深入

- structured outputs
- tool calling
- Context Builder
- MCP
- Skills
- durable state
- eval flywheel
- sandbox
- HITL
- coding harness

### Trial / 重点观察

- A2A cross-vendor interoperability
- MCP extensions / Tasks / Apps
- agent-to-agent enterprise orchestration
- adaptive model routing
- long-horizon coding agents

### Use with reason

- Multi-Agent
- GraphRAG
- browser-only automation
- self-improving loops

### Avoid hype-driven design

- 为了“Agentic”把简单流程改成多 Agent
- 每个任务都上最强 reasoning model
- 只依赖 Prompt 做权限控制
- 把向量数据库当 Memory 的全部
- 把 SSE 当任务执行器
- 声称分布式系统轻易实现 exactly-once execution

## 3. 20 个核心面试问题

1. Agent 和普通 LLM 调用的本质区别是什么？
2. 你怎么设计 Agent Loop？
3. Context Engineering 为什么重要？
4. Memory、RAG、Run State 有什么区别？
5. Tool Schema 如何影响成功率？
6. Tool 如何做 retry / timeout / idempotency？
7. MCP 和 Function Calling 的关系是什么？
8. MCP 2026 stateless core 对部署有什么影响？
9. Skill 和 Tool 有什么区别？
10. A2A 和 MCP 分别解决什么？
11. Run 和 Job 为什么分开？
12. checkpoint / resume 怎么设计？
13. lease / heartbeat / fencing token 分别干什么？
14. 为什么 SSE 断开不能等于任务失败？
15. Coding Agent 的 Harness 包含哪些东西？
16. Prompt Injection 如何防？
17. HITL 放在哪些动作边界？
18. Agent Eval 怎么做？
19. 1000 个并发 Run 如何做 backpressure 和 fairness？
20. 设计一套生产级 Agent Runtime。

## 4. 项目表达模板

不要只说：

> 我做了一个 Agent，可以调用工具。

更好的表达：

```text
业务目标
→ 为什么需要 Agent
→ Runtime 架构
→ Context 如何构造
→ Tool 如何治理
→ State / Resume 如何实现
→ 稳定性措施
→ Eval / Metrics
→ 最终指标
```

例如指标可以说：

- completion rate
- P95 latency
- tool success rate
- retry rate
- cost/task
- resume success rate
- human takeover rate

## 5. 框架怎么学

不要以“背 API”为目标。

选一个主框架验证概念即可，例如：

- OpenAI Agents SDK
- LangGraph
- 自研 Runtime

然后把同一个问题问清楚：

- loop 在哪里？
- state 在哪里？
- checkpoint 在哪里？
- tool governance 在哪里？
- HITL 怎么 pause/resume？
- tracing 怎么做？

会迁移概念，比会一个框架更重要。

## 6. 推荐 7 天路线

| Day | 学习 | 实践 |
|---|---|---|
| 1 | Agent Loop | 手写最小 loop |
| 2 | Context Engineering | 实现 Context Builder |
| 3 | Tools / MCP / Skills | 做 Tool Gateway 原型 |
| 4 | Runtime | 设计 Run / Job / Worker / Resume |
| 5 | Harness / Sandbox | 做搜索→修改→测试闭环 |
| 6 | Safety / Eval | HITL + eval case |
| 7 | System Design | 完整白板讲解生产 Runtime |

## 7. 推荐 4 周路线

### Week 1 · 能跑

Agent Loop、Tool Calling、Structured Output、Context Builder。

### Week 2 · 能稳定跑

Queue、Worker、Retry、Timeout、Idempotency、Checkpoint、Resume。

### Week 3 · 敢执行动作

Sandbox、Permission、HITL、Coding / Browser Agent、MCP / Skills。

### Week 4 · 能维护

Trace、Metrics、Eval、Replay、Canary、Model Routing、Failure Taxonomy。

## 8. 最终能力标准

当你能自然讲出下面这段话时，说明学习方向基本正确：

> 我不把 Agent 看成一个 Prompt 或一个框架，而把它看成一个带模型决策能力的分布式执行系统。我会从 Context、Tool Contract、State、Durable Execution、Permission、Sandbox、Tracing 和 Eval 去设计它，并用可靠性、成本、延迟和任务完成率来衡量它。