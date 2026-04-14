---
tags:
  - systems
  - redis
  - architecture
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# Redis 高并发架构串讲

## Summary

Redis 的高并发，不是因为“单线程神奇”，而是因为它把网络等待、事件通知和命令执行拆到了最合适的位置：非阻塞 socket、`epoll`、Event Loop、纯内存操作。

## 一条主线

```text
非阻塞 socket
-> epoll 只返回就绪 fd
-> Event Loop 分发读写事件
-> 命令执行主要是纯内存操作
-> 再把结果写回客户端
```

## 关键判断

- Redis 的瓶颈首先不是线程数，而是网络与内存访问模型。
- 单线程模型避免了上下文切换和锁竞争，但只适合 IO 密集、单次计算很短的任务。
- “Redis 单线程高并发”这个答案如果不落到 `fd / epoll / event loop`，基本等于没回答。

## Related

- [[IO 模型知识地图]]
- [[阻塞与非阻塞 IO]]
- [[同步与异步 IO]]
- [[IO 多路复用与 epoll]]
- [[事件循环 Event Loop]]
- [[文件描述符与 Socket]]

## Open Questions

- Redis 6 引入多线程 I/O 后，哪些旧结论需要加限定语？
- 这套解释怎样迁移到 Nginx、Node.js 或 Kafka 网络模型上？
