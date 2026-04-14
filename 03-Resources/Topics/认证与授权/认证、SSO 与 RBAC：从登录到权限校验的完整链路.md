---
tags:
  - auth
  - security
  - architecture
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
canonical: true
reuse_value: high
---

# 认证、SSO 与 RBAC：从登录到权限校验的完整链路

## Summary

这页是认证与授权主题的主线页。它解决的不是某个协议细节，而是把 `Authentication`、`SSO`、`OIDC`、`OAuth 2.0`、`Session`、`JWT`、`RBAC` 放回一条真实业务链路里。

## 一条完整链路

```text
用户访问系统
-> 发现未登录
-> 跳转统一身份系统
-> 身份系统完成认证
-> 业务系统拿到登录态凭证
-> 识别用户身份
-> 查询角色与权限
-> 后端做接口权限校验
-> 前端按 permissions 控制展示
```

## 先把三件事分开

| 名词 | 解决的问题 |
|------|------------|
| 认证 | 你是谁 |
| 授权 | 你能做什么 |
| 登录态 | 系统怎么记住你已经登录 |

继续往下分：

- `SSO` 解决多个系统之间如何复用登录结果。
- `OIDC / SAML` 是常见的单点登录实现方案。
- `OAuth 2.0` 解决第三方应用如何受控访问资源。
- `RBAC` 解决登录后如何管理权限。
- `Cookie / Session / Token / JWT` 解决登录结果如何保存和传递。

## 关键判断

- `SSO` 不等于权限系统。它优先回答“你是谁”，不直接回答“你能做什么”。
- `OAuth 2.0` 不等于登录协议。它的中心是授权，不是身份。
- `OIDC` 是建立在 `OAuth 2.0` 之上的身份层，更适合现代 Web SSO。
- `JWT` 只是令牌格式，不是权限模型；权限模型通常仍由 [[RBAC 权限模型设计]] 决定。

## Related

- [[OAuth 2.0 核心流程]]
- [[RBAC 权限模型设计]]
- [[Spring Security 与 JWT]]

## Open Questions

- 在企业系统里，哪些权限应该下沉到统一身份系统，哪些必须保留在业务系统内？
- OIDC claims 和业务系统自己的权限缓存之间，边界怎么划分最稳？
