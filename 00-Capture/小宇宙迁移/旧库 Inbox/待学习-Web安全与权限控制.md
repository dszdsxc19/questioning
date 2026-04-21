# 待学习：Web安全与权限控制

> 创建时间：2026-03-23
> 状态：待学习
> 主题：计算机 / 安全

## 学习清单

### Web 安全基础

- [ ] **CSRF（跨站请求伪造）**
  - 原理与攻击场景
  - 防御措施（CSRF Token、SameSite Cookie 等）
  - 与 XSS 的区别

- [ ] **XSS（跨站脚本攻击）**
  - 反射型 XSS
  - 存储型 XSS
  - DOM 型 XSS
  - 防御措施（输入验证、输出编码、CSP）

### 权限控制体系

- [ ] **Token**
  - Token 的基本概念
  - Access Token vs Refresh Token
  - Token 的存储与传输

- [ ] **JWT（JSON Web Token）**
  - JWT 结构（Header、Payload、Signature）
  - JWT vs Session
  - JWT 的安全注意事项
  - 实践场景

- [ ] **OAuth 2.0**
  - 授权流程与角色
  - 授权码模式、隐式模式、密码模式、客户端模式
  - 实际应用场景（第三方登录等）

- [ ] **SAML（Security Assertion Markup Language）**
  - SAML 原理
  - SAML vs OAuth
  - 企业级应用场景

- [ ] **SSO（单点登录）**
  - SSO 的实现方式
  - CAS、OAuth、SAML 在 SSO 中的应用
  - 常见开源方案

### 权限模型

- [ ] **RBAC（基于角色的访问控制）**
  - 基本概念（User、Role、Permission）
  - RBAC 实现方案
  - 常见坑点与最佳实践

- [ ] （可选）ABAC（基于属性的访问控制）
- [ ] （可选）PBAC（基于策略的访问控制）

## 学习资源

（待补充）

## 笔记链接

- [[基础-CORS、CSRF、XSS：区别、联系与防御]]

---

## 元数据

```yaml
topic: Web安全
subtopics:
  - CSRF
  - XSS
  - JWT
  - OAuth
  - SAML
  - SSO
  - RBAC
status: todo
priority: high
```
