---
tags:
  - distributed-systems
  - tracing
  - observability
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 分布式链路追踪与 TraceId

## Summary

TraceId 是一次完整请求的全局唯一标识符。它让分散在多个服务中的日志、调用和耗时记录，重新归并到同一条请求链路上。

## 问题从哪里来

单体时代，一次请求只在一个进程里完成，一行日志就能定位问题。微服务时代，同一请求会经过网关、订单服务、库存服务、优惠券服务、通知服务等多个节点，日志之间天然断裂。

## TraceId 与 SpanId

- TraceId：标识整条链路
- SpanId：标识链路中的单个调用节点
- ParentSpanId：帮助还原树状调用关系

## 由谁生成、怎么传递

最常见的生成点是 API Gateway：

- 入口唯一
- 业务服务只负责透传
- 格式统一

同步调用通常通过 HTTP Header 传递；异步调用通常通过消息头或消息体字段传递。

## 分布式 ID

TraceId 本质上是一个分布式唯一 ID，需要满足：

- 全局唯一
- 高性能
- 最好趋势递增
- 不强依赖中心节点

常见方案包括 UUID、数据库自增 ID、Snowflake 等。

## Related

- [[微服务电商系统架构总览]]
- [[可观测性工具栈对比：开源与云服务（2026）]]
- [[阿里云 ARMS 与 SLS 可观测性实践]]

## Open Questions

- 你的系统里更需要“能串起来的日志”，还是“完整 span 语义和拓扑视图”？
