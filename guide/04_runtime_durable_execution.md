# 04 · Agent Runtime 与 Durable Execution

> 目标：从“会调用模型”升级到“能实现一个可恢复、可扩展的 Agent Runtime”。

## 1. Run 和 Job 为什么要分开

```text
Run = 用户视角的一次任务
Job = 为推进 Run 而产生的一次可调度执行
```

一个 Run 可能经历多个 Job：首次执行、工具完成后的 resume、人工审批后的 resume、失败重试等。

这样可以避免把一次长任务绑定在单个进程生命周期上。

## 2. 推荐状态机

```text
PENDING
  ↓
RUNNING
  ├─ WAITING_TOOL
  ├─ WAITING_INPUT
  ├─ WAITING_APPROVAL
  ├─ RETRYING
  └─ PAUSED
  ↓
SUCCEEDED / FAILED / CANCELLED / TIMED_OUT
```

状态必须可持久化，不能只放内存。

## 3. Durable Execution

核心目标：

> 任务执行到第 N 步机器挂了，恢复时从正确边界继续，而不是从头重跑。

需要：

- checkpoint
- deterministic step boundary
- execution ledger
- resume token / generation
- persisted state
- idempotent side effects

## 4. Checkpoint 存什么

至少包括：

- config snapshot
- current step / turn
- relevant context state
- tool call state
- completed side effects
- budget usage
- pending approval / input
- retry metadata

不要简单序列化整个 Python 对象图作为唯一恢复机制。

## 5. Worker Queue

典型结构：

```text
API → Run Store → Job Queue → Worker Pool → Runtime
```

Worker 领取任务时关注：

- atomic claim
- lease
- heartbeat
- visibility timeout
- retry count
- dead letter

## 6. Lease 与 Fencing Token

Lease 解决“谁当前拥有任务”；Fencing Token 解决“旧 Worker 复活后继续写”的问题。

每次重新领取任务生成更大的 generation/token，写状态时校验 token，只允许最新 owner 写入。

## 7. Exactly-once 怎么回答

不要轻易说“实现 exactly-once execution”。分布式环境更现实的是：

```text
at-least-once execution
+ idempotency
+ deduplication
= exactly-once effect（业务效果尽量一次）
```

## 8. Cancellation

取消不是改一个数据库状态就结束。

需要传播到：

- model request
- tool call
- sandbox process
- child job
- downstream task

同时避免已完成副作用被错误“回滚”。

## 9. Backpressure

1000 个请求同时进来，不等于要立刻启动 1000 个模型调用。

需要：

- admission control
- queue limit
- tenant quota
- concurrency limit
- priority / fairness
- rate limit
- degradation

## 10. 高频面试题

1. Run 和 Job 为什么分开？
2. 长任务如何 checkpoint / resume？
3. Worker 挂了怎么恢复？
4. Lease 和 heartbeat 有什么区别？
5. Fencing Token 解决什么问题？
6. 如何避免重复消费造成副作用？
7. exactly-once 为什么难？
8. 1000 个 Agent 请求同时提交怎么办？
9. SSE 断开后为什么 Run 不应该停止？

## 11. 学完标准

你应该能画出一套 `Run + Job + Queue + Worker + Checkpoint + Ledger + Lease + Resume` 架构，并解释每个失败场景如何恢复。