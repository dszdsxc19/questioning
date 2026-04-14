---
tags:
  - systems
  - runtime
  - event-loop
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 事件循环 Event Loop

## Summary

事件循环是一种调度机制：持续等待事件、分发事件、执行回调，用少量线程处理大量 IO 密集型任务。

## 基本结构

```text
wait_for_events
-> dispatch
-> handle
-> process_timers
```

## 核心判断

- Event Loop 不是某门语言专属语法糖，而是一套运行时调度模型。
- 单线程高并发成立的前提是：大量时间花在等待 IO，而不是 CPU 计算。
- 一旦业务逻辑变成重 CPU 任务，事件循环会被卡死，必须拆到线程池、进程池或其他执行单元。

## Related

- [[IO 模型知识地图]]
- [[IO 多路复用与 epoll]]
- [[Redis 高并发架构串讲]]

## Open Questions

- Node.js 的 phase、microtask、macrotask 是否值得单独拆成更细的运行时页面？
