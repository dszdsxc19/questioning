---
tags:
  - java
  - auth
  - spring
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
aliases:
  - Spring Security 与 JWT 实战笔记
reuse_value: medium
---

# Spring Security 与 JWT

## Summary

这页关注的是 Java Web 体系里，`Spring Security` 如何通过过滤器链统一处理认证与授权，以及 `JWT` 在其中扮演什么角色。

## 核心理解

- `Spring Security` 是安全框架，不等于 JWT 库。
- 它的 Web 支撑点是过滤器链。
- 登录成功后的“凭证机制”可以是 Session，也可以是 JWT。
- `JWT` 只是承载身份声明的 token 格式，不替代授权模型。

## 关键组件

- `SecurityContextHolder`：保存当前请求上下文中的认证信息
- `AuthenticationManager`：执行认证
- `UserDetailsService`：按用户名加载用户
- `PasswordEncoder`：负责密码加密与校验
- 自定义过滤器：负责从请求中提取 token 并恢复认证状态

## 适用边界

- 如果是前后端分离、无状态 API，JWT 很常见。
- 如果是传统服务端渲染系统，Session 往往更直接。
- 把 `Spring Security` 学成“背类名”，价值不高；关键是搞清统一过滤、认证恢复、权限校验这条链。

## Related

- [[认证、SSO 与 RBAC：从登录到权限校验的完整链路]]
- [[OAuth 2.0 核心流程]]
- [[RBAC 权限模型设计]]

## Open Questions

- 哪些场景下继续坚持 Session 会比 JWT 更省心？
- Spring Security 里哪些默认行为最容易在真实项目里被误用？
