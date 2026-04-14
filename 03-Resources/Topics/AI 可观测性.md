---
tags:
  - ai
  - observability
  - tracing
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
canonical: true
aliases:
  - AI Tracing 语义规范与 Mastra 实现
  - GenAI 可观测性
---

# AI 可观测性

## Summary

AI 可观测性的关键，不是“多打一层日志”，而是用统一语义把模型调用、工具执行、Agent 决策与工作流轨迹纳入可分析的 trace。

## 这页解决什么问题

这页用来澄清为什么 Agent 系统的调试、评估和上线监控，需要一套比普通应用日志更接近执行轨迹的可观测性模型。

## 选用原则

收录到这页的内容，优先满足以下条件：

- 能解释 `gen_ai.*` 这类统一语义为何重要
- 能连接评估、调试、成本控制和平台迁移
- 不被某个单一框架绑死

## 核心内容

### 一句话定义

AI 可观测性是对模型、工具、Agent 与工作流执行轨迹的结构化记录与分析能力。

### 为什么日志不够

普通日志能告诉你“发生过什么”，但很难回答：

- 为什么答案突然变差
- 哪一步工具调用导致失败
- 成本和延迟是在什么节点失控
- 某次版本更新具体破坏了哪段轨迹

### Tracing 的价值

Trace 能把一次任务拆成多个 span，例如：

- 模型调用
- Agent 调用
- 工具执行
- Workflow 执行

这让你能同时看到：

- 输入输出 token
- 延迟
- 错误点
- 决策路径

### 在 GenAI 场景里通常要看什么

- `request.model` 或等价字段：本次调用用了哪个模型
- 输入与输出 token：成本和复杂度是否异常
- span 层级：一次任务里哪些步骤属于模型、工具、Agent、Workflow
- 错误类型：是模型失败、工具失败，还是编排失败

如果这些信息没有统一记录，后续评估和排障会严重依赖猜测。

### 统一语义的重要性

如果不同框架和平台都能理解同一套语义属性，数据才具备迁移性和可比较性。

对 GenAI 场景，统一语义的价值在于：

- 降低平台锁定
- 支持跨系统分析
- 让评估、监控、排障使用同一底层数据模型

### 与 Agent 评估的关系

- 评估回答“系统是否变好”
- 可观测性回答“系统为什么变成这样”

过程评估越深入，对 tracing 的依赖越强。

### 框架页与主题页的边界

像 Mastra 这类框架的 tracing 实现值得记录，但应作为主题页的实现例子，而不是替代主题本身。

### 以 Mastra 为例可以学到什么

- 它把 AI tracing 建在 OpenTelemetry 之上，而不是自造一套黑盒协议
- span 可以自然映射到模型调用、工具执行、Agent 运行、Workflow 运行
- 这说明真正重要的不是某个框架名字，而是它有没有采用可迁移的数据模型

## Sources

- 旧笔记：`/Users/admin/Desktop/mine/小宇宙/30 Knowledge/AI/AI Tracing 语义规范与 Mastra 实现.md`
- 旧笔记：`/Users/admin/Desktop/mine/小宇宙/30 Knowledge/AI/Mastra 的 Workspace、Sandbox 与 NAS 挂载设计.md`

## Related

- [[Agent 评估]]
- [[Agent 编排与 Sub-agent]]

## Open Questions

- 对你当前的 AI 工作流，最有价值的 span 粒度应该细到什么程度？
- 你更需要这套数据支持调试、成本控制，还是支持长期评估？
- 哪些框架实现细节值得单独保留为补充页，而不是继续塞进主题总页？
