---
tags:
  - distributed-systems
  - saas
  - multi-tenant
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 多租户隔离设计（Multi-Tenant Isolation）

## Summary

多租户系统指一个系统实例同时服务多个客户，每个租户的数据、配置和权限相互隔离，但共享部分系统资源。

## 什么是多租户系统

典型场景：

- SaaS 平台
- 企业级 AI 平台
- DevOps 平台
- Agent 平台

## 核心问题

- 数据如何隔离
- 配置如何隔离
- 权限如何隔离
- 性能噪声如何控制

## Related

- [[微服务电商系统架构总览]]
- [[RBAC 权限模型设计]]

## Open Questions

- 哪些系统适合逻辑隔离，哪些必须物理隔离？
