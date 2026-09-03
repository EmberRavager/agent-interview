# 05 · Harness、Coding Agent 与 Computer Use

> 目标：理解 2026 年高自治 Agent 的关键不只是模型，而是模型之外的 Harness。

## 1. Harness Engineering

Harness = Agent 工作环境 + 规则 + 工具 + 验证 + 恢复机制。

典型组成：

```text
repo / workspace instructions
+ skills
+ tools
+ sandbox
+ filesystem
+ shell
+ browser
+ tests / lint / build
+ checkpoints
+ progress artifacts
+ session handoff
```

高质量 Harness 能显著减少模型迷路、重复搜索和无验证修改。

## 2. Coding Agent 的闭环

```text
Understand
→ Search
→ Read
→ Plan
→ Edit
→ Test
→ Inspect failure
→ Repair
→ Review diff
```

关键不在“一次生成正确代码”，而在持续执行和验证。

## 3. Repository Instructions

Coding Agent 需要明确项目级约束：

- architecture boundaries
- commands
- style / lint
- test strategy
- forbidden areas
- dependency policy
- definition of done

AGENTS.md / repo instructions 的价值在于让不同 Agent 获取稳定的项目规则，而不是每轮重新 Prompt。

## 4. Search Before Write

大型代码库里，直接改代码通常是不可靠的。

建议：

1. 找入口
2. 找调用链
3. 找 contract / schema
4. 找类似实现
5. 找 tests
6. 再修改

## 5. Sandbox

Sandbox 至少考虑：

- filesystem isolation
- process isolation
- network policy
- resource quota
- secret isolation
- workspace snapshot
- cleanup

执行 Shell 的 Agent 本质上已经获得了“程序权限”，不能只靠 Prompt 约束。

## 6. Browser / Computer Use

Computer Use 的核心难点是：

```text
perception → grounding → action → verification
```

观察信号可能包括：

- screenshot
- DOM
- accessibility tree
- URL
- focused element
- page state

## 7. Verification

任何高价值动作都应该验证结果：

- URL 是否变化
- DOM 是否出现目标状态
- toast 是否成功
- 文件是否生成
- 数据是否写入
- test 是否通过

“执行了 click”不是成功，“目标状态被验证”才是成功。

## 8. Progress Artifact

长任务不要只依赖上下文记忆。可以持久化：

- todo / plan
- completed steps
- discovered facts
- changed files
- test results
- unresolved blockers

这对 compaction、handoff、resume 都很重要。

## 9. 高频面试题

1. Harness Engineering 是什么？
2. Coding Agent 为什么要 Search Before Write？
3. 怎么让 AI 写代码更可靠？
4. Sandbox 应隔离什么？
5. Browser Agent 的 grounding 为什么难？
6. 为什么动作后验证很重要？
7. 长 Coding Agent 如何跨 session 继续？

## 10. 学完标准

你应该能设计一个具备 workspace、shell、file edit、test、snapshot 和验证闭环的 Coding Agent Harness。