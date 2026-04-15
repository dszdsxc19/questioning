---
tags:
- network
- http
- cache
type: resource
status: active
created: 2026-03-01
updated: '2026-04-14'
author:
- "[[HarryPangPang]]"
source_type: migrated_note
source_url: https://juejin.cn/post/6844903838768431118
source_path: 面试/基础/基础-HTTP缓存机制-强缓存与协商缓存.md
description: HTTP缓存机制主要在http响应头中设定，响应头中相关字段为Expires、Cache-Control、Last-Modified、Etag。详细介绍强缓存和协商缓存的区别与使用场景。
title: HTTP缓存机制 - 强缓存与协商缓存
published: 2019-05-06
---
## 什么是缓存？

浏览器缓存(Browser Caching)是浏览器对之前请求过的文件进行缓存，以便下一次访问时重复使用，**节省带宽，提高访问速度，降低服务器压力**。

HTTP 缓存机制主要在 HTTP 响应头中设定，相关字段为：
- **强缓存**：`Expires`、`Cache-Control`
- **协商缓存**：`Last-Modified`、`ETag`

---

## 浏览器缓存判断流程

### 第一次请求
浏览器向服务器请求资源，服务器返回资源并带上缓存策略的响应头。

### 第二次请求
浏览器根据缓存策略判断：
1. **强缓存命中** → 直接从本地读取（200 OK from memory/disk cache）
2. **强缓存未命中** → 发送请求到服务器验证（协商缓存）
3. **协商缓存命中** → 服务器返回 304，浏览器使用缓存
4. **协商缓存未命中** → 服务器返回 200 和新资源

---

## 缓存类别

### 1. 强缓存

浏览器不会向服务器发送任何请求，直接从本地缓存中读取文件。

**状态码表现**：
- `200 OK from memory cache` - 从内存缓存读取（浏览器关闭后失效）
- `200 OK from disk cache` - 从硬盘缓存读取（持久化）

**缓存优先级**：memory cache > disk cache > 网络请求

#### 强缓存 Header

| Header | 版本 | 说明 |
|--------|------|------|
| `Expires` | HTTP/1.0 | 绝对过期时间（GMT 时间） |
| `Cache-Control` | HTTP/1.1 | 相对过期时间，优先级高于 Expires |

**Cache-Control 常用值**：

| 值 | 说明 |
|-----|------|
| `max-age=<seconds>` | 资源可以被缓存多长时间（单位：秒） |
| `s-maxage=<seconds>` | 只针对代理服务器缓存 |
| `public` | 响应可被任何缓存区缓存 |
| `private` | 只能针对个人用户，不能被代理服务器缓存 |
| `no-cache` | 强制向服务器验证（仍会缓存，但每次都要验证） |
| `no-store` | 禁止一切缓存（真正的不缓存） |

> ⚠️ **注意**：`no-cache` 不是"不缓存"，而是"使用前必须验证"。

---

### 2. 协商缓存

向服务器发送请求，服务器根据请求头判断是否命中协商缓存：
- **命中**：返回 `304 Not Modified`，浏览器从缓存读取
- **未命中**：返回 `200 OK` 和新资源

#### 协商缓存 Header

| 组合 | 版本 | 说明 |
|------|------|------|
| `Etag` / `If-None-Match` | HTTP/1.1 | 通过文件 hash 判断是否修改 |
| `Last-Modified` / `If-Modified-Since` | HTTP/1.0 | 通过最后修改时间判断 |

**Etag 优先级高于 Last-Modified**

##### Etag / If-None-Match

- **Etag**：服务器生成返回给前端（通常由文件索引节点、大小、修改时间 Hash 得到）
- **If-None-Match**：浏览器再次请求时带上 Etag 值，服务器对比判断返回 200 或 304

##### Last-Modified / If-Modified-Since

- **Last-Modified**：服务器向浏览器发送资源的最后修改时间
- **If-Modified-Since**：浏览器再次请求时带上上次修改时间，服务器对比判断

> **为什么 Etag 优先级更高？**
> - Last-Modified 时间精度只能到秒
> - 文件内容未变但修改时间变了（如重新保存）
> - 文件内容变了但修改时间未变（虽然少见）

---

## 完整缓存流程图

```
┌─────────────┐
│ 浏览器请求  │
└──────┬──────┘
       │
       ▼
┌─────────────────────────────┐
│ 检查强缓存（Cache-Control）  │
│   max-age 未过期？           │
└──────┬──────────────────────┘
       │
    ┌──┴──┐
    │ 是  │ 否 → 发送请求（带 If-None-Match / If-Modified-Since）
    ▼     ▼
┌────────┐ ┌─────────────────────────────┐
│ 200    │ │ 服务器验证协商缓存            │
│ from   │ │   资源是否修改？              │
│ cache  │ └──────┬──────────────────────┘
└────────┘        │
               ┌──┴──┐
               │ 未改 │ 修改
               ▼      ▼
          ┌────────┐ ┌────────┐
          │ 304    │ │ 200    │
          │ 使用缓存│ │ 返回新资源│
          └────────┘ └────────┘
```

---

## 面试要点

1. **强缓存和协商缓存的区别**
   - 强缓存不请求服务器，协商缓存需要请求
   - 强缓存返回 200，协商缓存返回 304

2. **Cache-Control 和 Expires 的区别**
   - Expires 是 HTTP/1.0，Cache-Control 是 HTTP/1.1
   - Cache-Control 优先级更高

3. **Etag 和 Last-Modified 的区别**
   - Etag 精度更高，优先级更高
   - Last-Modified 只能精确到秒

4. **no-cache 和 no-store 的区别**
   - no-cache：使用前必须验证（仍缓存）
   - no-store：完全不缓存

---

## 实践建议

| 资源类型 | 缓存策略 |
|---------|---------|
| HTML | `no-cache`（每次验证，保证获取最新） |
| CSS/JS（带 hash） | `max-age=31536000`（长期缓存） |
| 图片/字体 | `max-age=86400`（1 天） |
| API 响应 | `no-cache` 或短时间 `max-age` |

---

## 参考文章

原文链接：[http面试必会的：强制缓存和协商缓存 - 掘金](https://juejin.cn/post/6844903838768431118)
