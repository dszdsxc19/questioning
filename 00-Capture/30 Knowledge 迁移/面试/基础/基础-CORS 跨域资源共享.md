---
tags:
  - inbox
type: capture
status: inbox
created: 2026-04-14
updated: 2026-04-14
source_type: migrated_note
source_path: "面试/基础/基础-CORS 跨域资源共享.md"
---

# CORS 跨域资源共享

> 先看总览：[[基础-CORS、CSRF、XSS：区别、联系与防御]]

## 1. 什么是跨域

**同源策略**（Same-Origin Policy）是浏览器的一种安全机制，限制从一个源加载的文档或脚本如何与来自另一个源的资源进行交互。

**同源**的定义：协议、域名、端口三者完全相同。

```
✓ 同源：http://example.com/api  与  http://example.com/home
✗ 跨域：http://example.com      与  https://example.com  (协议不同)
✗ 跨域：http://a.com            与  http://b.com        (域名不同)
✗ 跨域：http://example.com:80   与  http://example.com:8080 (端口不同)
```

---

## 2. CORS 简介

**CORS**（Cross-Origin Resource Sharing，跨域资源共享）是 W3C 标准，允许服务器声明哪些源可以访问资源。

### 核心思想

- 浏览器**自动携带** Origin 头
- 服务器**决定**是否允许跨域（返回相应的 CORS 头）
- 浏览器**验证**响应头，决定是否暴露响应内容给 JS

> ⚠️ **注意**：CORS 是浏览器机制，服务端调用（如 curl、Postman）不受跨域限制。

---

## 3. 简单请求（Simple Request）

满足以下所有条件的请求被视为简单请求，**不会触发预检**：

### 3.1 方法限制

只允许以下方法：
- `GET`
- `HEAD`
- `POST`

### 3.2 头部限制

只允许以下字段：
- `Accept`
- `Accept-Language`
- `Content-Language`
- `Content-Type`（仅限以下值）
- `Last-Event-ID`
- `DPR`、`Downlink`、`Save-Data`、`Viewport-Width`、`Width`

### 3.3 Content-Type 限制

`Content-Type` 只能是：
- `text/plain`
- `multipart/form-data`
- `application/x-www-form-urlencoded`

### 3.4 请求示例

```http
POST /api/login HTTP/1.1
Host: api.example.com
Origin: http://localhost:3000
Content-Type: application/x-www-form-urlencoded

username=test&password=123456
```

### 3.5 响应示例

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://localhost:3000
Access-Control-Allow-Credentials: true
Content-Type: application/json

{"token": "xxx"}
```

---

## 4. 预检请求（Preflight Request）

**不满足**简单请求条件的，会先发送 `OPTIONS` 预检请求。

### 4.1 触发条件

满足以下**任一**条件即触发预检：

| 条件类型 | 示例 |
|---------|------|
| 方法不是 GET/HEAD/POST | `PUT`、`DELETE`、`PATCH` |
| 自定义请求头 | `X-Requested-With: XMLHttpRequest` |
| Content-Type 不在允许范围内 | `Content-Type: application/json` |
| 请求头包含字段映射到简单请求之外 | `Authorization` |

### 4.2 预检请求流程

```
浏览器                          服务器
  │                              │
  │─── OPTIONS (预检) ───────────>│
  │                              │
  │<──── 200 OK (允许) ───────────│
  │                              │
  │─── GET/POST (实际请求) ──────>│
  │                              │
  │<──── 200 OK (响应数据) ────────│
```

### 4.3 预检请求示例

```http
OPTIONS /api/users HTTP/1.1
Host: api.example.com
Origin: http://localhost:3000
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: Content-Type, Authorization
```

### 4.4 预检响应示例

```http
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: http://localhost:3000
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400
```

---

## 5. 常见 CORS 响应头

| 响应头 | 说明 |
|--------|------|
| `Access-Control-Allow-Origin` | 允许的源（`*` 表示所有） |
| `Access-Control-Allow-Methods` | 允许的 HTTP 方法 |
| `Access-Control-Allow-Headers` | 允许的请求头 |
| `Access-Control-Allow-Credentials` | 是否允许携带 Cookie |
| `Access-Control-Expose-Headers` | 允许客户端读取的响应头 |
| `Access-Control-Max-Age` | 预检结果缓存时间（秒） |

---

## 6. 携带凭证的请求

### 6.1 前端配置

```javascript
fetch('http://api.example.com/user', {
  credentials: 'include'  // Cookie
  // credentials: 'same-origin'  // 同源携带
})
```

### 6.2 服务端配置

```javascript
// ⚠️ 携带凭证时，Allow-Origin 不能是 *
res.setHeader('Access-Control-Allow-Origin', 'http://localhost:3000')
res.setHeader('Access-Control-Allow-Credentials', 'true')
```

---

## 7. 服务端配置示例

### Node.js (Express)

```javascript
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', 'http://localhost:3000')
  res.header('Access-Control-Allow-Methods', 'GET,POST,PUT,DELETE,OPTIONS')
  res.header('Access-Control-Allow-Headers', 'Content-Type,Authorization')
  res.header('Access-Control-Allow-Credentials', 'true')
  res.header('Access-Control-Max-Age', '86400')

  if (req.method === 'OPTIONS') {
    return res.sendStatus(204)
  }
  next()
})
```

### 使用 cors 中间件

```javascript
const cors = require('cors')

app.use(cors({
  origin: ['http://localhost:3000', 'https://example.com'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization']
}))
```

### Nginx 配置

```nginx
location /api/ {
    add_header Access-Control-Allow-Origin http://localhost:3000;
    add_header Access-Control-Allow-Methods 'GET, POST, PUT, DELETE, OPTIONS';
    add_header Access-Control-Allow-Headers 'Content-Type, Authorization';
    add_header Access-Control-Allow-Credentials true;

    if ($request_method = 'OPTIONS') {
        return 204;
    }

    proxy_pass http://backend;
}
```

---

## 8. 面试常见问题

### Q1: 为什么需要预检请求？

**答**：保护服务器资源，避免跨域请求对服务器造成意外影响。简单请求是安全的（只读或表单提交），而复杂请求（如 DELETE）可能有副作用。

### Q2: 预检请求的缓存机制？

**答**：通过 `Access-Control-Max-Age` 头指定缓存时间（秒），浏览器会缓存预检结果，避免每次请求都发送 OPTIONS。

### Q3: JSONP 和 CORS 的区别？

| 对比项 | JSONP | CORS |
|--------|-------|------|
| 支持 | 仅 GET | 所有 HTTP 方法 |
| 兼容性 | 老浏览器 | 现代浏览器 |
| 安全性 | 有 XSS 风险 | 更安全 |
| 实现 | script 标签 | 标准化 HTTP 头 |

### Q4: 开发环境如何解决跨域？

**方案一**：开发服务器代理（如 Vite、webpack-dev-server）
```javascript
// vite.config.js
export default {
  server: {
    proxy: {
      '/api': 'http://api.example.com'
    }
  }
}
```

**方案二**：浏览器插件（仅开发环境）

---

## 9. 相关链接

- [MDN - CORS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS)
- [Fetch 规范 - CORS 协议](https://fetch.spec.whatwg.org/#http-cors-protocol)

---

**标签**：`#前端` `#HTTP` `#浏览器` `#安全` `#跨域`

