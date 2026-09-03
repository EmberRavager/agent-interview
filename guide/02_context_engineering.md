# 02 · Context Engineering

> 目标：掌握 Agent 最核心的输入工程。2026 年很多 Agent 失败并不是模型不够强，而是上下文装配错误。

## 1. Prompt Engineering vs Context Engineering

Prompt Engineering 关注“指令怎么写”；Context Engineering 关注“模型这一轮到底看到什么”。

一个生产级上下文通常包含：

```text
System Policy
+ Agent Config Snapshot
+ User / Tenant / Permission Context
+ Conversation History
+ Working State
+ Memory
+ Retrieval
+ Tool Schemas
+ Current Input
```

## 2. Context Builder

不要把上下文拼接散落在业务代码里。建议抽象成明确的 pipeline：

```text
collect → filter → rank → compact → validate → assemble
```

关注：

- trusted / untrusted source
- priority
- freshness
- token cost
- duplication
- conflict
- sensitivity

## 3. Memory 重新理解

Memory 不是“上向量库就完事”。至少分：

- conversation history：近期对话
- working memory：当前 Run 状态
- episodic memory：历史任务经历
- semantic memory：稳定事实
- user preference：长期偏好

写入长期记忆前要有筛选策略，避免把临时信息、错误信息和敏感信息永久保存。

## 4. RAG 在 2026 的位置

RAG 是 Context Source，不是 Agent 架构中心。

重点：

- hybrid retrieval
- metadata filter
- query rewrite
- rerank
- source freshness
- grounding / citation
- retrieval evaluation

不需要背大量 RAG 变体名字。

## 5. Context Compaction

长任务一定会遇到上下文膨胀。

常见策略：

1. 丢弃低价值历史
2. 对旧对话做摘要
3. Tool Result 只保留必要字段
4. 保留关键决策和业务主键
5. 长文档按需 retrieval
6. 将稳定状态放 Run State，不反复塞自然语言

## 6. Prompt Injection 本质

外部网页、邮件、文档、Tool Result 都是**数据，不是指令来源**。

核心原则：

- distinguish instructions from data
- isolate untrusted content
- never let retrieved text change permission policy
- high-risk action requires policy/HITL

## 7. Context Caching

适合缓存：

- 稳定 System Policy
- 大型工具目录
- 静态领域知识
- 长文档前缀

但要关注版本与失效，否则缓存会把旧策略带入新 Run。

## 8. 高频面试题

1. Context Engineering 为什么比 Prompt Engineering 更重要？
2. Memory 和 RAG 有什么区别？
3. 长对话怎么压缩？
4. Tool Result 要不要全部塞回模型？
5. 如何处理冲突上下文？
6. Prompt Injection 如何防？
7. 为什么 Context Builder 应该独立成模块？

## 9. 学完标准

你应该能设计一个带 `priority / token budget / trust level / compaction` 的 Context Builder，而不是简单做字符串拼接。