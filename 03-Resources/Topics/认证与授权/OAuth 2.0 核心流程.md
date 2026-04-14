---
tags:
  - auth
  - oauth
  - protocol
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
aliases:
  - OAuth 2.0 核心流程笔记
reuse_value: high
---

# OAuth 2.0 核心流程

## Summary

`OAuth 2.0` 解决的是“资源所有者如何授权第三方应用访问受限资源”，核心产物是短期、可撤销、可限定范围的 `access token`。

## 它不解决什么

- 不直接解决“用户是谁”，那是 `OIDC` 更对题的部分。
- 不直接决定“用户能做什么”，那属于业务授权模型，例如 [[RBAC 权限模型设计]]。

## 核心角色

- 资源所有者：数据真正的拥有者
- 客户端：请求资源的第三方应用
- 授权服务器：颁发令牌
- 资源服务器：持有受保护资源

## 为什么令牌比密码更合适

- 有时效
- 可撤销
- 可限定 scope
- 不需要把主账号密码直接交给第三方

## 授权码流程

1. 客户端把用户导向授权服务器。
2. 用户完成登录并同意授权。
3. 授权服务器回传一次性 `code`。
4. 客户端后端拿 `code + client_secret` 换取 `access token`。

它比把 token 直接发到前端更安全，因为真正敏感的换 token 步骤发生在后端。

## Related

- [[认证、SSO 与 RBAC：从登录到权限校验的完整链路]]
- [[Spring Security 与 JWT]]

## Open Questions

- 什么时候应该从“理解 OAuth”切换到“优先理解 OIDC”？
- 在前后端分离系统里，token 存储位置如何在安全性和可用性之间平衡？
