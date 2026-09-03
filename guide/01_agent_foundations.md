# 01 · Agent 基础与模型能力

> 目标：理解 2026 年的 Agent 到底是什么，不再从框架名和 Pattern 名开始学习。

## 1. Agent 的最小定义

Agent = **模型决策 + 状态 + 工具 + 执行循环 + 停止条件**。

```text
Input
  ↓
Build Context
  ↓
Model decides
  ├─ final answer → stop
  └─ tool call
       ↓
    execute tool
       ↓
    append observation
       ↓
    next turn
```

普通 Chat Completion 主要完成一次生成；Agent Runtime 则要管理多轮决策、动作和状态。

## 2. 2026 年模型能力重点

必须理解：

- reasoning / non-reasoning model
- structured outputs
- tool calling
- multimodal input
- long context
- prompt caching
- streaming
- model routing
- latency / cost / quality trade-off

不需要死背模型发布日期和榜单。

## 3. ReAct 还重要吗？

重要的是思想，不是模板。

ReAct 的核心是：

```text
reason → act → observe → continue
```

现在很多模型和 SDK 已经原生支持工具循环，因此不需要手写一大段 `Thought/Action/Observation` Prompt。面试时应强调：**Pattern 是控制流思想，Runtime 才是工程实现。**

## 4. Planner 要不要单独存在？

不是默认需要。

优先级：

1. 单模型直接决策
2. 必要时生成短计划
3. 复杂长任务再拆 Planner / Executor
4. 真有独立领域或安全边界时再考虑 Multi-Agent

## 5. Structured Output 为什么重要

生产系统不要依赖“模型大概会输出 JSON”。

需要：

- schema validation
- enum / required fields
- parse failure handling
- retry with correction
- versioned contract

## 6. Agent Loop 的关键边界

必须限制：

- `max_turns`
- token budget
- time budget
- tool call budget
- cost budget
- recursion / delegation depth

否则 Agent 很容易进入无限循环或成本失控。

## 7. Streaming 的正确理解

Streaming 是交互通道，不应该成为任务生命周期本身。

```text
Run lifecycle ≠ SSE connection lifecycle
```

SSE 断开后，长任务通常应该继续运行；客户端重新连接后通过事件回放或状态查询恢复观察。

## 8. 高频面试题

1. Agent 和 Chatbot 的本质区别是什么？
2. Agent Loop 怎么设计？
3. ReAct 现在还有没有必要？
4. 为什么要限制 max turns？
5. Structured Output 为什么比自然语言解析可靠？
6. Streaming 为什么不能和任务生命周期绑定？
7. Planner 什么时候需要、什么时候是过度设计？

## 9. 学完标准

你应该能不用 LangChain / CrewAI，自己写出一个包含 `model → tool → observation → next turn` 的最小 Agent Runtime。