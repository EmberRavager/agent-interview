# 07 · Observability、Tracing 与 Evals

> 目标：知道 Agent 为什么失败，并把失败转成可持续改进资产。

## 1. Agent Trace 应该长什么样

```text
Run
├─ context build
├─ model turn
│  ├─ input summary / hash
│  ├─ output
│  └─ token / latency / model
├─ tool call
│  ├─ args
│  ├─ result
│  ├─ latency
│  └─ retry
├─ handoff
├─ approval
├─ checkpoint / resume
└─ final result
```

只打印日志远远不够。

## 2. 关键指标

至少准备：

- Task Completion Rate
- First-Pass Success Rate
- Tool Success Rate
- Avg Turns / Steps
- Retry Rate
- Replan Rate
- Resume Success Rate
- Human Takeover Rate
- P50 / P95 Latency
- Cost per Task
- Unsafe Action Block Rate

## 3. Failure Taxonomy

线上失败要分类，不然无法优化：

- model reasoning error
- context missing / stale / conflict
- wrong tool selection
- invalid tool args
- tool infrastructure failure
- permission failure
- state / resume failure
- grounding failure
- policy block
- user input insufficient

## 4. Eval 不只是“答案准确率”

Agent Eval 要评：

- final answer quality
- task completion
- tool selection
- tool args
- trajectory quality
- number of unnecessary steps
- safety
- latency
- cost

## 5. Offline Eval

适合：

- prompt/context 改动回归
- model upgrade
- tool schema change
- routing strategy change
- safety policy change

评测集优先来自真实失败案例，而不是只靠人工想象。

## 6. Online Eval

生产观察：

- canary
- A/B
- sampled human review
- user feedback
- shadow traffic
- anomaly detection

不要仅因为离线分数提高就全量上线。

## 7. Eval Flywheel

```text
Production Failure
→ classify
→ add eval case
→ reproduce
→ fix
→ regression eval
→ canary
→ production
```

这是 Agent 系统持续变好的核心闭环。

## 8. Replay

理想的 Trace 应尽量支持：

- 重建当时配置版本
- 重建输入与关键 Context
- 重放 Tool 结果或使用 mock
- 对比不同 model / prompt / policy

Replay 对线上事故定位和 Eval 数据生成都非常重要。

## 9. LLM-as-Judge

可以用，但不能迷信：

- judge 本身需要校准
- 高风险指标要有人类 gold set
- 结构化任务优先 deterministic evaluator
- judge prompt / model 也要版本化

## 10. 高频面试题

1. Agent 可观测性和普通服务有什么不同？
2. 怎么衡量 Agent 是否真的变好了？
3. Eval 数据从哪里来？
4. LLM-as-Judge 有什么问题？
5. 为什么要保存 trajectory？
6. Prompt 改完如何避免回归？
7. Replay 系统怎么设计？

## 11. 学完标准

你应该能把一次线上失败从 Trace 中定位到具体层，并把它沉淀成一个以后自动回归的 Eval Case。