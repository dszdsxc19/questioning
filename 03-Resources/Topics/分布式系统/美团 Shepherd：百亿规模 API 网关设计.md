---
tags:
  - distributed-systems
  - api-gateway
  - architecture
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 美团 Shepherd：百亿规模 API 网关设计

## Summary

Shepherd 这篇材料的价值，不在于背美团内部名词，而在于理解统一网关如何承接鉴权、限流、协议转换、动态路由和异步化线程模型。

## 为什么要做统一网关

微服务拆分之后，每个团队都在自己的应用里重复实现鉴权、限流、参数校验、监控日志和协议转换，导致重复造轮子和稳定性参差不齐。

Shepherd 的目标是：业务研发通过配置方式快速对外开放能力，而不是每次都重复写一套 Web 接入层。

## 整体架构

- 控制面：管理平台、监控中心
- 配置中心：统一配置下发
- 数据面：真正接收请求和执行业务编排的网关服务

## 关键技术实现

- 动态路由：MAP + 前缀树
- DSL 配置驱动
- HTTP -> RPC 泛化调用
- 全链路异步化
- 快慢线程池隔离

## Related

- [[微服务电商系统架构总览]]
- [[分布式链路追踪与 TraceId]]

## Open Questions

- 统一网关里哪些横切能力应该平台化，哪些应该留给业务服务自己实现？
