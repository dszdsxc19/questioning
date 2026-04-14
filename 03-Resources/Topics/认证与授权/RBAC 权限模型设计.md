---
tags:
  - auth
  - authorization
  - rbac
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# RBAC 权限模型设计

## Summary

RBAC 的核心不是“概念多”，而是用 `User -> Role -> Permission` 这层稳定中介来降低权限系统的维护复杂度。

## 核心模型

```text
User -> Role -> Permission
```

- 用户不直接绑权限
- 角色是稳定中间层
- 用户最终权限是多个角色权限的并集

## 数据建模

典型最小集合：

- `user`
- `role`
- `permission`
- `user_role`
- `role_permission`

如果前端菜单也要控制展示，可以再维护 `menu` 或 `route` 表，但那不是 RBAC 的必需部分。

## 工程判断

- 权限标识建议用 `resource:action`，例如 `user:create`、`order:read`。
- 登录时可以把角色与权限汇总成集合，写入缓存或令牌，避免每个请求都查全套表。
- 不要满代码写 `role === 'admin'` 这种硬编码判断；这会把角色耦合成业务逻辑分支。

## Related

- [[认证、SSO 与 RBAC：从登录到权限校验的完整链路]]
- [[OAuth 2.0 核心流程]]
- [[Spring Security 与 JWT]]

## Open Questions

- 多租户系统下，角色是全局的、租户级的，还是两层并存？
- 权限集合应该进 JWT，还是只放用户标识后端实时查询？
