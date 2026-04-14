---
tags:
  - systems
  - io
  - linux
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# IO 多路复用与 epoll

## Summary

IO 多路复用的核心价值是：把“哪个 fd 就绪了”交给操作系统判断，用户程序只处理就绪事件，从而避免多线程阻塞或无意义轮询。

## Linux 三代机制

- `select`：有 fd 数量限制，返回后还要遍历
- `poll`：去掉数量限制，但仍要线性遍历
- `epoll`：内核维护就绪队列，只返回 ready fd

## 关键判断

- `epoll` 的价值不是“所有场景都更快”，而是非常适合大量连接、少量活跃事件的网络服务。
- `LT` 更安全，`ET` 更省通知次数，但要求读到 `EAGAIN` 为止。
- Redis 和 Nginx 之所以能单线程扛很多连接，核心支点之一就是 `epoll`。

## Related

- [[IO 模型知识地图]]
- [[文件描述符与 Socket]]
- [[事件循环 Event Loop]]

## Open Questions

- 在连接规模不大时，`epoll` 的复杂度是否值得？
- `LT` 和 `ET` 的工程风险主要体现在哪些容易漏读、漏写的地方？
