## 九、系统设计与架构（10 题）

### 1. 设计一个企业级 AI Agent 平台（多租户）。

**核心模块：**

```
┌─────────────────────────────────────────────┐
│                  API Gateway                  │
│         (Auth / Rate Limit / Routing)         │
├──────────┬──────────┬──────────┬─────────────┤
│ Agent    │ Tool     │ Memory   │ Observability│
│ Runtime  │ Platform │ Service  │              │
│ (编排)    │ (工具市场)│ (记忆)    │ (Tracing)    │
├──────────┴──────────┴──────────┴─────────────┤
│              Multi-Tenant Isolation           │
│    (数据隔离 / 权限隔离 / 资源配额隔离)        │
└─────────────────────────────────────────────┘
```

**关键设计：**
- Agent 定义可复用（模版市场）
- 工具可插拔（插件化，租户级开启/关闭）
- 权限分级（Admin / Developer / User）
- 资源配额（每租户 Token 上限、工具调用上限）

**标准回答示例：**

> 我设计企业级 Agent 平台时，首先考虑的不是模型选型，而是租户隔离、权限、工具治理和可观测性。因为平台一旦面向多团队，工程边界比单点智能更重要。

---

### 2. 设计一个万级并发 Agent 服务。

**架构：**

```
负载均衡 (Nginx/Envoy)
    ↓
API Gateway (限流+鉴权+路由)
    ↓
Message Queue (Kafka) ← 异步解耦
    ↓
Agent Worker Pool (水平扩展)
    ├── Worker-1 (GPU Node)
    ├── Worker-2 (GPU Node)
    └── ...
    ↓
Shared State (Redis Cluster) + Vector DB
```

**水平扩展要点：**
- Agent 实例无状态（状态外挂到 Redis/DB）
- 工具调用异步化（非阻塞 I/O）
- GPU 池化管理（Model-as-a-Service）
- 热更新（滚动发布 + 金丝雀）

**标准回答示例：**

> 做万级并发 Agent 服务，本质上是异步任务系统加推理调度系统，所以我会优先讲队列、状态机、缓存和限流。模型只是其中一环，不是全部。

---

### 3. 设计一个智能 BI Agent（NL → SQL → 图表 → 解读）。

**流程：**

```
用户自然语言问题
    ↓
Schema Understanding Agent（理解表结构、字段含义）
    ↓
SQL Generation Agent（生成 + 校验 SQL）
    ↓
SQL Executor（执行，超时/错误处理）
    ↓
Data Analysis Agent（分析结果，判断是否需要可视化）
    ↓
Visualization Agent（选图表类型 + 生成代码）
    ↓
Insight Agent（自然语言解读数据洞察）
    ↓
最终输出：图表 + 文字解读
```

**面试加分：** 提到 SQL 安全——禁止 DROP/DELETE/UPDATE，限制查询行数和执行时间。

**标准回答示例：**

> BI Agent 的难点不在“生成 SQL”，而在于如何把自然语言、库表语义、权限控制和结果解释串起来。真正上线时，SQL 安全和结果可解释比炫酷更重要。

---

### 4. 设计一个多模态客服 Agent（截图+文本）。

```
用户输入（文本 + 截图）
    ↓
Input Router
    ├── 纯文本 → Text Analysis Agent
    ├── 纯截图 → Vision Agent (OCR + 界面理解)
    └── 混合   → Text Agent + Vision Agent 并行
    ↓
Intent Router（路由到对应业务 Agent）
    ├── 退款 Agent
    ├── 物流 Agent
    ├── 产品咨询 Agent
    └── ...
    ↓
Response Generator（整合结果 + 生成回复）
```

**标准回答示例：**

> 多模态客服 Agent 我会重点讲输入对齐、截图理解、知识检索和转人工机制。因为客服类系统核心不是会不会答，而是稳不稳、错了能不能兜底。

---

### 5. 如何设计 Agent 的灰度发布和回滚？

**金丝雀发布：**

```
100% 流量
    ├── 95% → Agent v1（稳定版）
    └── 5%  → Agent v2（金丝雀）
            ↓ 监控 30 分钟
        ┌───┴───┐
    正常         异常
     ↓            ↓
  扩大到 50%   自动回滚到 v1
     ↓
  扩大到 100%
```

**回滚包内容：** `{model_version, prompt_version, tool_versions, graph_version}` 原子切换。

**标准回答示例：**

> Agent 灰度发布不能只切模型版本，还要连同 prompt、工具版本和流程图一起回滚。因为 Agent 是组合系统，单回一个模型往往解决不了问题。

---

### 6. LLM 推理服务如何做到高吞吐低延迟？

| 技术 | 效果 |
|------|------|
| vLLM / TGI | PagedAttention，KV 缓存复用，吞吐提升 10x+ |
| Continuous Batching | 动态组批，不等整批满 |
| Speculative Decoding | 小模型草稿 + 大模型校验，延迟降低 2-3x |
| Quantization (AWQ/GPTQ) | 减少显存，提升吞吐 |
| Prefix Caching | 相同 System Prompt 复用 KV 缓存 |
| Tensor Parallel | 多 GPU 张量并行推理 |

**标准回答示例：**

> 我会把这个问题拆成模型服务优化、上下文优化和调度优化三层。比如 continuous batching、KV cache 复用、量化和 prefix caching，这样回答更像做过推理服务。

---

### 7. 设计一个支持流式输出（SSE）的 Agent 架构。

```
Client ←── SSE Stream ──→ Agent Server
                              ↓
                         SSEManager
                              ↓
              ┌───────────────┼───────────────┐
         Thought       Tool Call       Final Answer
         (立即推)      (推调用状态)     (逐步推token)
```

**关键：** 每个 Thought / Action / Observation 都即时推送给前端，用户感知「Agent 在思考」而非空白等待。

**标准回答示例：**

> 流式输出的核心价值不只是体验更丝滑，而是把长任务的中间状态暴露给用户和上层系统。这样用户知道它卡在哪，也更容易插入中断和确认点。

---

### 8. 如何设计 Agent 的灾难恢复（DR）？

| 层级 | 措施 |
|------|------|
| 数据层 | 向量库/状态存储多区域备份 |
| 服务层 | Agent Worker 多可用区部署 |
| 任务层 | 任务状态持久化（中间 checkpoint），可从中断点恢复 |
| 降级层 | 主 Agent 不可用时，降级为简化版（静态回复 + 转人工） |

**标准回答示例：**

> Agent 的 DR 不只是备份数据，更重要的是任务能不能从中断点恢复。因为很多任务本身就是长时执行，从头重跑可能比服务挂掉更糟。

---

### 9. 设计一个 Agent 评测平台。

```
Test Case Manager (Golden Task Set 管理)
    ↓
Agent Under Test (被测版本)
    ↓
Evaluator Pipeline
    ├── Auto Metrics (成功率/步数/延迟/成本)
    ├── LLM-as-Judge (答案质量评分)
    └── Human Review (抽样人工评审)
    ↓
Report Generator (对比基线版本)
    ↓
Gate (指标达标 → 通过，否则阻塞)
```

**标准回答示例：**

> 评测平台的价值不只是跑 benchmark，而是把线上失败样本沉淀成回归资产。这样它才是持续改进系统的一部分，而不是一次性看分工具。

---

### 10. 设计一个支持离线+在线混合执行的 Agent 系统。

**场景：** 用户离线时提交任务，Agent 后台执行，上线后查看结果。

**架构：**

```
用户提交任务 → Task Queue → Agent Worker 异步执行
                               ↓
                         Result Store (DB/COS)
                               ↓
                    用户上线 → Notification → 查看结果
```

**关键：** 
- 任务状态实时同步（PENDING → RUNNING → COMPLETED / FAILED）
- 中间产物持久化（便于断点续传）
- 支持取消和修改未执行的任务

**标准回答示例：**

> 混合执行系统的关键是把长任务和实时交互拆开，让需要即时反馈的部分在线做，重计算和长流程放后台跑。这样才能同时兼顾体验和成本。

---
