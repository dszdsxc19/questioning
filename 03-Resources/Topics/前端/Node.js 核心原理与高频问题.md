---
tags:
- frontend
- nodejs
- runtime
type: resource
status: active
created: 2026-04-14
updated: '2026-04-14'
source_type: migrated_note
source_path: 面试/基础/基础-Node.js 核心原理与高频面试题.md
---
# Node.js 核心原理与高频面试题

> Node.js 面试里，真正高频考的不是 API 背诵，而是**运行时原理 + 服务端工程能力**。
> 偏 SDK / 全栈 / AI 系统方向，重点掌握下面这套，基本覆盖 80% 面试。

---

## 一、一句话说清 Node.js 是什么（面试开场）

**标准回答**：

> Node.js 是基于 V8 引擎的 JavaScript 运行时，主要用于服务端开发。
> 它采用事件驱动、非阻塞 I/O 和单线程事件循环模型，特别适合高并发 I/O 场景。

---

## 二、Node.js 核心架构（最重要）

### 2.1 架构图

```
JS Code
   ↓
V8 Engine
   ↓
Node Runtime APIs
   ↓
libuv
   ↓
OS / Thread Pool
```

### 2.2 V8 引擎

**职责**：
- 解析 JavaScript 代码
- 编译为机器码
- 执行代码
- 垃圾回收

**示例**：

```javascript
console.log("hello");  // V8 执行这段代码
```

### 2.3 libuv（高频考点）

这是 Node 真正的核心，也是面试必问。

**职责**：
- Event Loop（事件循环）
- 文件 I/O
- 网络 I/O
- Thread Pool（线程池）
- Timers（定时器）

---

## 三、单线程为什么还能高并发（必考）

### 3.1 核心原理

**标准回答**：

> JavaScript 主线程是单线程执行
> 但 I/O 操作不会阻塞主线程
> Node 会把 I/O 交给 OS 或 libuv thread pool
> 完成后再通过 event loop 回调执行结果

### 3.2 代码示例

```javascript
const fs = require("fs");

fs.readFile("a.txt", () => {
  console.log("done");
});

console.log("next");
```

**输出**：

```
next
done
```

**为什么？**

因为 `readFile` 被异步交给系统处理，主线程继续往下执行。

这就是 **non-blocking I/O**（非阻塞 I/O）的核心。

---

## 四、Event Loop（面试最终 Boss）

### 4.1 一句话理解

> Event Loop 负责不断检查队列，并把可执行任务放到主线程执行。

你可以把它理解成一个**无限循环的调度器**。

### 4.2 执行顺序（必考）

```javascript
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

Promise.resolve().then(() => {
  console.log("3");
});

console.log("4");
```

**结果**：

```
1
4
3
2
```

### 4.3 为什么这样？

**同步代码先执行**：
- `console.log("1")` → 输出 1
- `console.log("4")` → 输出 4

**然后微任务（Microtask）**：
- `Promise.then()` → 输出 3

**最后宏任务（Macrotask）**：
- `setTimeout()` → 输出 2

**重点记忆**：
- `Promise` 属于 **microtask**
- `setTimeout` 属于 **macrotask**

### 4.4 Node 特有高频题：process.nextTick()

```javascript
process.nextTick(() => {
  console.log("tick");
});
```

`process.nextTick()` 的优先级比 `Promise` **更高**。

**执行顺序**：

```
nextTick > Promise > setTimeout > setImmediate
```

**完整示例**：

```javascript
console.log("start");

setTimeout(() => console.log("timeout"), 0);

setImmediate(() => console.log("immediate"));

Promise.resolve().then(() => console.log("promise"));

process.nextTick(() => console.log("nextTick"));

console.log("end");
```

**输出**：

```
start
end
nextTick
promise
timeout
immediate
```

---

## 五、CommonJS 和 ESM（必考）

### 5.1 CommonJS

```javascript
const fs = require("fs");

module.exports = {};
```

**特点**：
- 同步加载
- Node 默认历史方案
- 运行时加载模块

### 5.2 ESM

```javascript
import fs from "fs";

export default {};
```

**特点**：
- 静态模块系统
- 支持 tree shaking
- 编译时确定模块依赖

### 5.3 面试标准回答

> CommonJS 是**运行时同步加载**
> ESM 是**静态分析模块系统**，支持 tree shaking

---

## 六、Streams（高频，大厂常考）

### 6.1 为什么需要 Stream？

比如读取 5GB 文件。

**错误方式**：

```javascript
// ❌ 会一次性读入内存，可能 OOM
fs.readFile("big.log", (err, data) => {
  console.log(data);
});
```

**正确方式**：

```javascript
// ✅ 边读边处理，节省内存
const stream = fs.createReadStream("big.log");

stream.on("data", chunk => {
  console.log(chunk);
});
```

### 6.2 Stream 的优点

- **节省内存**：不需要一次性加载全部数据
- **边读边处理**：数据流式处理
- **高性能**：支持 backpressure 控制流速

### 6.3 面试标准话术

> Stream 适合处理大文件和网络传输
> 支持 backpressure 控制流速，避免生产者压垮消费者

### 6.4 实用示例：管道流

```javascript
const fs = require("fs");

// 读取大文件并压缩
const readStream = fs.createReadStream("input.log");
const writeStream = fs.createWriteStream("output.log.gz");
const zlib = require("zlib");
const gzip = zlib.createGzip();

// 管道连接
readStream
  .pipe(gzip)
  .pipe(writeStream);
```

---

## 七、Buffer（高频）

### 7.1 为什么需要 Buffer？

因为网络传输和文件都是**二进制**数据。

### 7.2 基本用法

```javascript
const buf = Buffer.from("hello");

console.log(buf);
// 输出：<Buffer 68 65 6c 6c 6f>
```

### 7.3 本质

Buffer 是**二进制数据容器**，用于：

- Socket 通信
- 文件流
- 图片处理
- 音视频处理

### 7.4 实用示例

```javascript
// 字符串转 Buffer
const buf = Buffer.from("hello");

// Buffer 转字符串
const str = buf.toString();

// 拼接 Buffer
const buf1 = Buffer.from("hello");
const buf2 = Buffer.from("world");
const buf3 = Buffer.concat([buf1, buf2]);
```

---

## 八、Thread Pool（高频进阶）

### 8.1 Node 真的是单线程吗？

**标准回答**：

> JS 执行线程是单线程
> 底层 I/O 和计算任务可通过线程池处理

### 8.2 libuv 线程池

libuv 默认提供 **4 个线程**（可通过 `UV_THREADPOOL_SIZE` 调整）。

**负责**：
- `fs` 文件操作
- `crypto` 加密解密
- `dns` DNS 查询
- `compression` 压缩解压

### 8.3 示例

```javascript
const crypto = require("crypto");

// pbkdf2 会走线程池
crypto.pbkdf2("secret", "salt", 100000, 512, "sha512", () => {
  console.log("done");
});

console.log("next");
// 输出：next（然后稍后输出 done）
```

---

## 九、为什么 Node 不适合 CPU 密集（必考）

### 9.1 问题示例

```javascript
// CPU 密集任务会卡死 event loop
while (true) {
  // 计算...
}
```

一旦主线程被 CPU 任务占用，所有请求都无法处理。

### 9.2 不适合的场景

- 图像处理
- 大量数学计算
- AI 推理（大模型）
- 视频编码

### 9.3 解决方案

1. **Worker Threads**（多线程）

```javascript
const { Worker } = require("worker_threads");

new Worker("./heavy-task.js");
```

2. **独立服务**（Go / Java / Rust）

Node 提供 API 网关，把计算任务交给专门服务。

### 9.4 面试话术

> Node 适合 I/O 密集型场景（API 服务、代理、网关）
> 不适合 CPU 密集型场景，可用 Worker Threads 或独立服务解决

---

## 十、面试高频题速记

### 10.1 Q1：Node 为什么高并发？

**回答**：
> 单线程事件循环 + 非阻塞 I/O + libuv

### 10.2 Q2：Promise 和 setTimeout 谁先执行？

**回答**：
> Promise 先执行，因为属于微任务（microtask）
> setTimeout 属于宏任务（macrotask）

### 10.3 Q3：Node 是单线程吗？

**回答**：
> JS 主线程单线程
> 底层存在线程池处理 I/O 和计算任务

### 10.4 Q4：为什么 Node 适合 API 服务？

**回答**：
> API 请求本质是 I/O 密集型（数据库查询、缓存读写、外部 API 调用）
> Node 非阻塞模型天然适合，一个线程可处理大量并发连接

### 10.5 Q5：process.nextTick 和 Promise 的区别？

**回答**：
> process.nextTick 优先级更高，在微任务队列之前执行
> Promise.then() 在微任务队列中执行

### 10.6 Q6：Event Loop 的执行阶段？

**回答**：
> 1. Timers（setTimeout、setInterval）
> 2. Pending callbacks
> 3. Idle, prepare
> 4. Poll（获取新 I/O 事件）
> 5. Check（setImmediate）
> 6. Close callbacks

---

## 十一、项目追问方向

如果面试官对你的基础回答满意，下一步会深入**工程实践**：

### 11.1 Express / Koa 中间件机制

- 洋葱模型（Koa）
- 中间件执行顺序
- 错误处理

### 11.2 HTTP 请求链路

- DNS 查询 → TCP 握手 → TLS 握手 → 请求发送
- Keep-Alive 连接复用
- HTTP/2 多路复用

### 11.3 鉴权与安全

- JWT / Session
- OAuth 2.0
- CSRF / XSS 防护
- 速率限制（Rate Limiting）

### 11.4 性能优化

- Cluster 集群模式
- 缓存策略（Redis）
- 数据库连接池
- 日志与监控（Sentry、Prometheus）

---

## 十二、记忆清单

| 概念 | 核心要点 |
|------|---------|
| **Node.js** | V8 + 事件驱动 + 非阻塞 I/O |
| **Event Loop** | 微任务 > 宏任务，nextTick > Promise |
| **libuv** | 事件循环 + 线程池 + 跨平台 I/O |
| **Stream** | 节省内存，适合大文件和网络传输 |
| **Buffer** | 二进制数据容器 |
| **CommonJS vs ESM** | 运行时同步 vs 静态分析 + tree shaking |
| **适用场景** | I/O 密集（API），不适合 CPU 密集 |

---

## 标签

#Node.js #后端 #面试 #事件循环 #运行时原理

