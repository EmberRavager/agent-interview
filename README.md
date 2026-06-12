# agent-interview

面向 `AI Agent / LLM Agent 工程师` 面试准备的系统化学习资料仓库。

这份仓库的目标不是堆术语，而是把高频面试题整理成更容易复习、更容易表达、也更接近真实工程回答的版本。内容尽量覆盖从概念理解到系统设计、从框架认知到生产实践的完整链路。

## 这份仓库适合谁

- 准备 `AI Agent`、`LLM Agent`、`AI 应用工程`、`Agent 平台` 相关岗位面试的人
- 已经学过一些概念，但回答时容易变成“背定义”的人
- 想把题库整理成长期复习资料，而不只是一次性面经的人

## 你会看到什么

- `核心结论`：先给出这道题最应该抓住的判断
- `理解重点`：解释为什么这么理解，而不是只列名词
- `标准回答示例`：给出更像真实面试表达的一段话答案
- `工程化视角`：尽量把问题落到稳定性、可观测性、权限、恢复、成本、延迟等实际约束上

## 快速开始

- 线上访问地址：[https://emberravager.github.io/agent-interview/guide/](https://emberravager.github.io/agent-interview/guide/)
- 想先看全局框架：读 [guide/00_overview.md](guide/00_overview.md)
- 想按体系系统复习：从 [guide/01_fundamentals.md](guide/01_fundamentals.md) 顺序看到 [guide/16_interview_strategy.md](guide/16_interview_strategy.md)
- 想直接网页阅读：打开 [guide/index.html](guide/index.html)
- 想看拆分版目录：读 [guide/README.md](guide/README.md)

## 学习路线图

### 7 天速刷路线

| 天数 | 重点章节 | 目标 |
|------|----------|------|
| Day 1 | [基础概念](guide/01_fundamentals.md) + [LLM 底层原理](guide/02_llm_foundations.md) | 建立 Agent、LLM、Chatbot、RAG 的边界感 |
| Day 2 | [Agent 设计模式](guide/03_agent_patterns.md) + [规划与推理](guide/07_planning_and_reasoning.md) | 掌握 ReAct、Plan-and-Execute、Reflection、Replanning 等核心模式 |
| Day 3 | [工具调用与 Function Calling](guide/04_tool_calling.md) + [记忆系统设计](guide/05_memory_systems.md) | 理解工具 schema、权限边界、短期记忆、长期记忆与上下文压缩 |
| Day 4 | [RAG 与 Agentic RAG](guide/06_rag_and_agentic_rag.md) | 能讲清传统 RAG、Agentic RAG、多轮检索、重排与评估 |
| Day 5 | [多 Agent 协作](guide/08_multi_agent.md) + [系统设计与架构](guide/09_system_design.md) | 训练系统设计题，重点关注编排、状态、恢复、幂等和成本 |
| Day 6 | [评估与可观测性](guide/10_evals_and_observability.md) + [安全与治理](guide/11_safety_and_governance.md) + [生产环境与 MLOps](guide/13_production_and_mlops.md) | 补齐生产落地能力，包括日志、指标、权限、灰度、回滚和评测闭环 |
| Day 7 | [前沿热点](guide/14_frontier_topics.md) + [工程补充专题](guide/15_engineering_addendum.md) + [面试策略建议](guide/16_interview_strategy.md) | 整理高频追问，把书面答案转成能说出口的项目化表达 |

### 4 周系统复习路线

| 周次 | 复习重点 | 建议产出 |
|------|----------|----------|
| 第 1 周 | 基础概念、LLM 原理、Agent Loop、常见失败模式 | 整理 20 个一句话核心结论 |
| 第 2 周 | Tool Calling、Memory、RAG、Planning、多 Agent | 每章挑 3 道题练成 1 分钟口头回答 |
| 第 3 周 | 系统设计、Evals、Observability、Safety、MLOps | 准备 2 套完整 Agent 系统设计回答模板 |
| 第 4 周 | 前沿热点、工程专题、面试策略、个人项目复盘 | 把自己的项目经历改写成 STAR + 架构 + 指标版本 |

## 仓库结构

- [guide/](guide/)
  主学习资料目录，按主题拆分，适合持续补充、精修和做专项版本
- [guide/index.html](guide/index.html)
  网页阅读版，适合浏览整套内容
- [guide/AI_Agent_Interview_Guide_Combined.md](guide/AI_Agent_Interview_Guide_Combined.md)
  拆分资料重新整合后的完整 Markdown 版
- [ai_agent_interview_guide.md](ai_agent_interview_guide.md)
  原始整合版 Markdown
- [ai_agent_interview_guide.html](ai_agent_interview_guide.html)
  原始整合版 HTML
- [guide/_TEMPLATE.md](guide/_TEMPLATE.md)
  后续补题、改题时建议遵循的标准模板

## 章节覆盖

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
14. [2025-2026 前沿热点](guide/14_frontier_topics.md)
15. [2026 工程补充专题](guide/15_engineering_addendum.md)
16. [面试策略建议](guide/16_interview_strategy.md)

## 这份资料的特点

- 不只讲“是什么”，也尽量讲“为什么这样设计”
- 不只讲框架名，也尽量讲控制流、状态、恢复、护栏这些底层问题
- 补充了 `Claude Code`、`Codex`、`Cursor`、`Copilot` 等工具差异对比
- 尽量把答案写成“能在面试里直接说出来”的形式，而不是纯概念摘录
- 已拆分成多个 Markdown 文件，后续维护成本更低

## 推荐使用方式

- 第一轮：先看总览和基础章节，建立整体知识地图
- 第二轮：按目标岗位重点深挖高频章节，比如 `Agent 设计`、`Tool Calling`、`Memory`、`RAG`、`System Design`
- 第三轮：重点练“标准回答示例”，把书面材料转成口头表达
- 第四轮：结合自己的项目经历，把仓库内容替换成你自己的案例和指标

## 后续可以继续增强的方向

- 给每章再补 `追问树`
- 增加更贴近项目复盘的 `案例题`
- 针对不同岗位做专项版本，比如 `平台工程`、`应用工程`、`Coding Agent`
- 增加英文版回答模板或双语版本

## 说明

- 当前网页版适合直接阅读，Markdown 拆分版适合继续维护
- 如果你是第一次进入这个仓库，建议优先从 [guide/README.md](guide/README.md) 和 [guide/index.html](guide/index.html) 开始