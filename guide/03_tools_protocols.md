# 03 · Tools、MCP、Skills 与 A2A

> 目标：理解 Agent 如何安全、标准化地连接外部能力，以及 Tool、Skill、MCP、A2A 各自解决什么问题。

## 1. Tool Calling 不是“函数名 + JSON”

一个生产级 Tool 至少包含：

```text
name
+ description
+ input schema
+ output contract
+ auth scope
+ timeout
+ retry policy
+ idempotency policy
+ side-effect level
+ audit metadata
```

## 2. Tool Schema 设计

好的 schema：

- 字段少而明确
- enum 替代自由文本
- required 清晰
- description 描述业务含义
- 不让模型生成服务器可以自己推导的字段
- 副作用参数显式化

Tool 选错或参数错，很多时候不是模型问题，而是 schema / description 设计问题。

## 3. Timeout / Retry / Idempotency

重试前先分类：

- transient：网络抖动、429、临时 5xx → 可以退避重试
- permanent：参数错误、权限不足 → 不应盲重试
- unknown outcome：请求超时但外部动作可能已成功 → 必须先查状态/幂等

副作用 Tool 应带 `idempotency_key` 或业务唯一键。

## 4. MCP 在 2026 年怎么理解

MCP 是 Agent ↔ Tool / Data / App 的标准连接层。

2026-07-28 规范的重要变化：

- protocol core 转向 stateless
- request 自描述
- header-based routing
- list result caching
- authorization hardening
- extensions 正式化
- Tasks 进入 extension
- legacy HTTP+SSE、Roots、Sampling、Logging 进入弃用路线

因此，生产 MCP Server 的设计应该更像普通可水平扩展 HTTP 服务，而不是依赖隐式 transport session。

## 5. MCP 与 Function Calling

```text
Function Calling = 模型如何表达“我要调用工具”
MCP = Agent Runtime 如何发现、连接和调用外部能力
```

两者不是竞争关系。

## 6. Skills

Skill 更适合封装“如何完成一类任务”的可复用知识：

```text
instructions
+ scripts
+ examples
+ templates
+ domain resources
```

Tool 是原子动作；Skill 是任务方法和能力包。

## 7. A2A

A2A 是 Agent ↔ Agent 的互操作协议，重点是：

- discovery
- capability description
- delegation
- task lifecycle
- streaming / async operation
- enterprise interoperability

2026 年更适合把它理解成**横向 Agent 协作层**；MCP 是**纵向工具连接层**。

## 8. Multi-Agent 什么时候值得做

只有存在明确收益时：

- independent context
- domain specialization
- security boundary
- parallel exploration
- cross-team / cross-vendor ownership

否则优先单 Agent + Tools / Skills。

## 9. Tool Gateway

企业 Agent 平台建议统一 Tool Gateway：

```text
Agent Runtime
   ↓
Tool Gateway
   ├─ schema validation
   ├─ auth
   ├─ rate limit
   ├─ timeout / retry
   ├─ audit
   ├─ approval
   └─ protocol adapters / MCP
```

不要让每个 Agent 自己实现安全和重试。

## 10. 高频面试题

1. Function Calling 和 MCP 有什么区别？
2. MCP 2026 stateless core 带来什么工程变化？
3. Tool 怎么做幂等？
4. Tool 超时但不知道是否成功怎么办？
5. Skill 和 Tool 的区别是什么？
6. A2A 和 MCP 各解决什么问题？
7. 什么时候 Multi-Agent 是过度设计？
8. 为什么企业需要 Tool Gateway？

## 11. 学完标准

你应该能设计一个有 schema、鉴权、重试、幂等、审计、审批能力的 Tool Gateway，并清楚 MCP / Skills / A2A 放在哪一层。