# 08 · Production System Design

> 目标：能把 Agent 当作一个真正的分布式生产系统来设计，而不是一个挂在 API 后面的 while loop。

## 1. 推荐总架构

```text
Client
  ↓
API / Auth / Tenant
  ↓
Admission Control
  ↓
Run Store + Job Store
  ↓
Queue
  ↓
Worker Pool
  ↓
Agent Runtime
  ├─ Context Builder
  ├─ Model Gateway
  ├─ Tool Gateway
  ├─ Sandbox
  ├─ Checkpoint
  ├─ Approval
  └─ Event Publisher
  ↓
Tracing / Eval / Metrics / Audit
```

## 2. Admission Control

入口先决定“能不能接”，而不是所有请求都立即执行。

需要：

- tenant quota
- user quota
- idempotency key
- queue capacity
- priority
- request validation
- frozen config snapshot

## 3. Config Snapshot

Run 开始时冻结：

- agent version
- prompt version
- model config
- tools / skills versions
- policy version
- routing config

否则任务执行一半配置改变，恢复后行为不可解释。

## 4. Queue 与 Worker

Worker concurrency 不是越大越好，受以下因素约束：

- model provider rate limit
- tool connection pool
- CPU / memory
- sandbox capacity
- database connections
- downstream SLA

需要测量吞吐和 P95，而不是拍一个数字。

## 5. Fairness

B 端平台尤其要避免一个大客户占满 Worker：

- per-tenant concurrency
- weighted fair queue
- priority class
- reservation
- rate limit

## 6. Model Gateway

统一处理：

- provider adapter
- model routing
- timeout
- retry
- fallback
- token / cost accounting
- rate limit
- caching
- trace metadata

业务代码不要到处直接调模型 SDK。

## 7. Tool Gateway

统一处理：

- discovery
- schema
- auth
- timeout / retry
- idempotency
- MCP
- audit
- policy / approval

## 8. Event 与 SSE

推荐：

```text
Runtime → durable event log → SSE/WebSocket observer
```

而不是：

```text
HTTP request → while executing → directly write SSE only
```

前者支持断线重连、回放和多观察者。

## 9. 数据库事务边界

避免长事务包住模型调用或 Tool 调用。

典型模式：

1. 短事务 claim job
2. commit
3. 执行外部调用
4. 短事务写结果 / next state

外部副作用与数据库一致性可结合 idempotency、outbox、ledger、compensation。

## 10. 灰度与回滚

Agent 发布对象不只是代码：

- prompt
- model
- tool
- skill
- policy
- context strategy

这些都应该可版本化、可灰度、可回滚，并进入 Eval 回归。

## 11. 高频系统设计题

1. 设计一个支持 1000 个并发 Run 的 Agent 平台。
2. 如何设计 Queue 与 Worker concurrency？
3. 如何保证多租户公平？
4. 配置为什么要做 snapshot？
5. SSE 断线如何恢复？
6. 如何做模型路由与 fallback？
7. 数据库事务和长工具调用怎么拆？
8. Prompt / Model / Tool 如何灰度？
9. 如何控制单任务成本？
10. 如何避免一个 Agent Run 无限执行？

## 12. 学完标准

你应该能在白板上完整设计一个高并发、长任务、可恢复、多租户 Agent Runtime，并回答容量、故障、成本、安全和发布问题。