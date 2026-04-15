---
tags:
- network
- auth
- browser
type: resource
status: active
created: 2026-03-03
updated: '2026-04-14'
source_type: migrated_note
source_path: 面试/基础/基础-Cookie 知识点详解.md
title: Cookie 知识点详解
---
> 相关总览：[[基础-CORS、CSRF、XSS：区别、联系与防御]]

## 前言

HTTP 是无状态协议，每次请求都是独立的。但实际应用中需要保持状态（如登录状态），因此引入了 **Cookie 和 Session** 体系。

## Cookie 基本概念

### 工作原理

```
客户端请求                    服务端响应
─────────                    ─────────
Request Headers              Response Headers
Cookie: name=value            Set-Cookie: name=value
                              Set-Cookie: sessionid=xxx
```

1. **服务端种 Cookie**：通过 `Set-Cookie` 响应头
2. **客户端携带 Cookie**：通过 `Cookie` 请求头自动携带
3. **浏览器存储**：以文本形式保存在硬盘

### Cookie vs Session

| 特性 | Cookie | Session |
|------|--------|---------|
| 存储位置 | 客户端浏览器 | 服务器 |
| 大小限制 | 单个 ≤4KB，约30个/域名 | 无限制 |
| 安全性 | 较低，可被篡改 | 较高，服务端控制 |

---

## Cookie 属性详解

| 属性 | 说明 |
|------|------|
| **Name/Value** | 键值对 |
| **Domain** | 域名，`.example.com` 可共享给子域名 |
| **Path** | 路径限制，通常为 `/` |
| **Expires/Max-Age** | 过期时间，不设则为 Session 级别 |
| **HttpOnly** | 禁止 JS 读取，防 XSS |
| **Secure** | 仅 HTTPS 可携带 |
| **SameSite** | 跨站请求限制：`Strict` / `Lax`(默认) / `None` |
| **Priority** | Chrome 提案，优先级 `Low`/`Medium`/`High`，超出时优先清除低优先级（Firefox 不支持） |

### SameSite 三种模式

| 值 | 行为 |
|----|------|
| **Strict** | 仅同站请求可携带 |
| **Lax** | 允许部分第三方请求（链接跳转、GET 表单） |
| **None** | 允许跨站（必须配合 Secure） |

---

## JavaScript 操作

### 检查浏览器是否启用 Cookie

```javascript
window.navigator.cookieEnabled  // true
```

### 读写 Cookie

```javascript
// 读取（返回所有 cookie 字符串）
document.cookie  // "name=value; name2=value2"

// 设置（一次只能设置一个）
document.cookie = 'name=value; expires=...; path=/; secure; samesite=lax'

// 删除（设为过去时间）
document.cookie = 'name=; expires=' + new Date(0) + '; path=/'

// 修改（重新赋值，注意 path/domain 必须一致）
document.cookie = 'name=newvalue; path=/'
```

> ⚠️ **注意**：`document.cookie` 无法读取设置了 `HttpOnly` 的 cookie

### 服务端读写 Cookie（Node.js/Express 示例）

```javascript
// 写 Cookie
res.setHeader('Set-Cookie', ['uid=123;maxAge: 900000; httpOnly: true']);
// 或使用 express
res.cookie("uid", '123', {maxAge: 900000, httpOnly: true});

// 读 Cookie
console.log.req.getHeader('Cookie'));  // 拿到所有 cookie
// 或 express
console.log(req.cookie.name);

> ⚠️ **注意**：`document.cookie` 无法读取设置了 `HttpOnly` 的 cookie

---

## 共享策略

Cookie 可被读取的条件：
- **Domain**：URL 的 host 与 Domain 一致或是其子域名
- **Path**：URL 的路径与 Path 匹配

**示例**：
```javascript
// Cookie: uid=1; Domain=.example.com; Path=/user

// ✓ 可读取
https://example.com/user/profile
https://a.example.com/user/list

// ✗ 不可读取
https://example.com/home
https://other.com/user
```

---

## 安全相关

### XSS 防护
**攻击**：恶意脚本窃取 cookie
**防护**：关键 cookie 设置 `HttpOnly`

### CSRF 防护
**攻击**：第三方网站利用用户登录状态发起请求
**防护**：
- 设置 `SameSite=Strict` 或 `Lax`
- 使用 CSRF Token
- 关键操作加验证码

---

## Cookie vs Storage

| 特性 | Cookie | localStorage | sessionStorage |
|------|--------|--------------|----------------|
| 操作方 | 服务器 + JS | 仅 JS | 仅 JS |
| 生命周期 | 可设置过期 | 永久（除非清除） | 会话级 |
| 大小 | ~4KB | ~5MB | ~5MB |
| 携带请求 | 每次自动携带 | 不携带 | 不携带 |

---

## CORS 跨域携带

跨域请求携带 Cookie 需满足：

1. **Cookie 设置**：`SameSite=None; Secure`
2. **服务端响应**：
   ```
   Access-Control-Allow-Origin: https://example.com
   Access-Control-Allow-Credentials: true
   ```
3. **客户端请求**：`xhr.withCredentials = true`

### 常见使用场景

#### 1️⃣ 单点登录（SSO）
```
登录中心            业务系统A              业务系统B
sso.com            app-a.com             app-b.com
   │                   │                      │
   ├─ 用户登录成功     │                      │
   ├─ 种 Cookie        │                      │
   │                   │                      │
   │◄──────────────────┼──────────────────────┤
   │   跨域携带 Cookie 验证登录状态              │
```
用户在 `sso.com` 登录后，访问 `app-a.com` 和 `app-b.com` 时需要跨域携带登录凭证。

#### 2️⃣ 前后端分离 + 跨域部署
```
前端              后端 API
example.com       api.example.com
   │                  │
   ├─────►  跨域请求  ─────►
        携带登录 Cookie
```

#### 3️⃣ 微服务架构
```
主站                用户服务              订单服务
example.com         user.api.com         order.api.com
   │                    │                    │
   └───────► 统一登录 ◄────────────────────────┘
                        │
                        └─ 跨域调用各微服务 API 时携带 Cookie
```

#### 4️⃣ 第三方授权登录
```
你的网站              OAuth 提供商
yourapp.com          auth.provider.com
   │                       │
   ├─ 点击"XX登录" ───────►
   │                       │
   │◄──────── 授权回调 ◄───┤
   │   携带授权 Cookie
```

### ⚠️ 易错点

```javascript
// ❌ 错误：Origin 不能是 *
// Access-Control-Allow-Origin: *

// ✅ 正确：必须是具体源
Access-Control-Allow-Origin: https://example.com

// ❌ fetch 默认不携带 Cookie
fetch('https://api.example.com/user')

// ✅ 需显式指定
fetch('https://api.example.com/user', {
  credentials: 'include'
})

// ✅ XMLHttpRequest
xhr.withCredentials = true
```

> 💡 **注意**：`Access-Control-Allow-Origin` 为 `*` 时，设置 `Allow-Credentials: true` 会导致请求失败。

### 安全注意事项

跨域携带 Cookie 增加了 **CSRF 风险**，建议配合：
- **CSRF Token**
- **SameSite** 策略（如果不需要真正的跨域，用 `Lax` 即可）
- **Origin** 精确匹配，不要用 `*`

---

## 编码规范

Cookie 的 name/value 包含特殊字符时需编码：

```javascript
// 推荐
document.cookie = 'name=' + encodeURIComponent('value=123&abc')

// 不推荐（不会编码 / 等字符）
document.cookie = 'name=' + encodeURI('value/123')
```

---

## 最佳实践

1. **减少 Cookie 数量和大小**：每次请求都会携带，影响性能
2. **关键信息设 HttpOnly**：防 XSS 窃取
3. **设置合理的 SameSite**：防 CSRF
4. **HTTPS 配合 Secure**：加密传输
5. **选择合适存储**：
   - Cookie：身份认证、A/B 分桶（每次请求必需）
   - localStorage：配置、缓存数据

---

## 补充说明

- **浏览器间 Cookie 不共享**：不同浏览器彼此独立，互不通信。一个浏览器登录后，切换到其他浏览器需要重新登录。

---

## 参考资料

- [面试不再怕：史上最全的cookie知识点详解 - 掘金](https://juejin.cn/post/6914109129267740686)
