---
created: 2026-04-07
updated: 2026-04-07
tags:
  - AI
  - Agent
  - Mastra
  - OpenTelemetry
  - Tracing
  - 可观测性
  - GenAI
  - SemanticConventions
series:
  - Mastra 框架深入
  - AI 可观测性
type: capture
status: inbox
source_type: migrated_note
source_path: "AI/AI Tracing 语义规范与 Mastra 实现.md"
---

# AI Tracing 语义规范与 Mastra 实现

## 概述

**Mastra**（mastra.ai）是一个 TypeScript AI Agent 框架，它的 observability / AI Tracing 底层完全基于 **OpenTelemetry**，并严格遵循官方的 **GenAI Semantic Conventions**（目前基于 v1.38.0 等版本）。

## GenAI Semantic Conventions

### 什么是 gen_ai.*

**GenAI Semantic Conventions** 是 OpenTelemetry 官方为 LLM、Agent、GenAI 操作定义的标准命名和属性规范。目的就是让各种 observability 后端能统一识别和展示 AI traces，而不用各自发明一套。

**属性前缀**：`gen_ai.*`

### 常见属性示例

| 属性 | 说明 |
|------|------|
| `gen_ai.operation.name` | 操作名称（如 chat、completion） |
| `gen_ai.request.model` | 请求的模型名称 |
| `gen_ai.usage.input_tokens` | 输入 token 数量 |
| `gen_ai.usage.output_tokens` | 输出 token 数量 |
| `gen_ai.system` | AI 系统标识（如 OpenAI、Anthropic） |
| `gen_ai.request.id` | 请求 ID |
| `gen_ai.response.id` | 响应 ID |
| `gen_ai.agent.*` | Agent 相关属性 |
| `gen_ai.workflow.*` | Workflow 相关属性 |

## Mastra 中的 Span 类型

### Span 命名示例

| Span 名称 | 说明 |
|-----------|------|
| `chat {model}` | LLM 调用 |
| `invoke_agent {agent_id}` | Agent 调用 |
| `execute_tool {tool_name}` | 工具执行 |
| `invoke_workflow {workflow_id}` | Workflow 调用 |

### Mastra 内部 Span 类型映射

| Mastra 类型 | 对应的 GenAI 操作 |
|------------|------------------|
| `MODEL_GENERATION` | chat / completion 操作 |
| `AGENT_RUN` | Agent 运行（带 `gen_ai.agent.*` 属性） |
| `WORKFLOW_RUN` | Workflow 运行（带 `gen_ai.workflow.*` 属性） |
| `TOOL_EXECUTION` | 工具执行 |

## 为什么这个规范重要

### 1. 跨平台兼容性

遵循 GenAI Semantic Conventions 的 trace 可以被各种平台统一识别：

- **Datadog** - AI Monitoring
- **New Relic** - MLOps
- **SigNoz** - OpenTelemetry 原生支持
- **LangSmith** - LangChain 专用
- **MLflow** - ML 实验跟踪
- **Sentry** - 错误监控 + AI tracing
- **Phoenix** - Arize AI 可观测性
- **ARMS / SLS** - 阿里云可观测性平台（通过 OTLP）

### 2. 标准化的数据模型

所有平台都理解 `gen_ai.*` 属性，无需为每个平台单独适配。

### 3. 无厂商锁定

使用 OpenTelemetry + 标准语义约定，可以随时切换后端存储。

## Mastra 的实现

### Exporter / Bridge

Mastra 提供：
- **OpenTelemetry Exporter** - 直接导出到 OTLP 兼容后端
- **各种平台 Bridge** - 转换到平台专用格式（如 LangSmith、Datadog）

### 配置示例（概念）

```typescript
import { Mastra } from '@mastra/core';

const mastra = new Mastra({
  observability: {
    otel: {
      serviceName: 'my-agent-app',
      exporters: {
        otlp: {
          endpoint: process.env.OTLP_ENDPOINT,
          headers: {
            'Authorization': `Bearer ${process.env.OTLP_API_KEY}`
          }
        },
        // 其他 exporters...
      }
    }
  }
});
```

## 官方参考

- **Mastra 文档**：[OpenTelemetry exporter / bridge 使用](https://mastra.ai/docs/observability)
- **OpenTelemetry GenAI 规范**：https://opentelemetry.io/docs/specs/semconv/gen-ai/
- **当前版本**：v1.38.0（2026）

## 实践建议

### 1. 开发环境

使用 Jaeger 或 Tempo 本地查看 traces：
```bash
docker run -p 16686:16686 -p 4318:4318 jaegertracing/all-in-one
```

### 2. 生产环境

根据团队情况选择：
- **纯开源**：LGTM Stack（Loki + Grafana + Tempo + Mimir）
- **云托管**：ARMS + SLS（阿里云）、Datadog、New Relic
- **AI 专用**：LangSmith、Phoenix

### 3. 关键指标

关注这些 GenAI 特定指标：
- Token 使用量（成本控制）
- Latency（per-span 和 total）
- Error rate（LLM API 错误、Tool 执行失败）
- Agent 决策路径

## 总结

你在 Mastra trace 里看到的 `gen_ai.*` 属性，不是 Mastra 发明的协议，而是 OpenTelemetry 为了让 AI tracing 标准化而定的"行业通用语义"。

**Mastra 只是严格遵守它**，所以你的 trace 能无缝对接各种 OTEL 兼容的平台。

## 相关文章

- [[可观测性工具栈对比-开源与云服务]]
- [[Mastra 的 Workspace、Sandbox 与 NAS 挂载设计]]
- [[Sub-agent 机制与使用场景]]
