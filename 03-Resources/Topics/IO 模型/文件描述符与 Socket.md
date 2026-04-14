---
tags:
  - systems
  - network
  - linux
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 文件描述符与 Socket

## Summary

文件描述符是 Unix 世界里“打开资源的统一句柄”；Socket 则是网络通信端点，属于一种特殊文件，因此也能被统一纳入 `read/write/epoll` 这套模型。

## 关键判断

- `fd` 不是“文件路径”，而是进程持有的资源句柄。
- `listen_fd` 负责等新连接，`client_fd` 负责与具体客户端传输数据。
- `epoll` 监听的对象本质上是这些 `fd`，不是抽象意义上的“连接”。

## Related

- [[IO 模型知识地图]]
- [[IO 多路复用与 epoll]]
- [[Redis 高并发架构串讲]]

## Open Questions

- 哪些 `fd` 类型天然适合 `epoll`，哪些看起来能监听但其实没意义？
