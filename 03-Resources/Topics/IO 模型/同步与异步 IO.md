---
tags:
  - systems
  - io
  - concurrency
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 同步与异步 IO

## Summary

同步与异步描述的不是“线程会不会等”，而是“数据准备好以后，谁来完成拷贝，以及结果怎么通知调用方”。

## 两条维度不要混

| 维度 | 它描述的问题 |
|------|--------------|
| 阻塞 / 非阻塞 | 等待期间线程是否挂起 |
| 同步 / 异步 | 数据拷贝与完成通知由谁负责 |

## 关键判断

- Linux 传统 `read/write + epoll` 本质上仍然是同步 IO。
- 异步 IO 的理想形态是：发起请求后立即返回，由内核完成等待和拷贝，再主动通知你。
- Redis 的组合是“同步非阻塞 IO + epoll”，不是异步 IO。

## Related

- [[IO 模型知识地图]]
- [[阻塞与非阻塞 IO]]
- [[IO 多路复用与 epoll]]

## Open Questions

- `io_uring` 应该被理解为“真正异步 IO”的主线，还是新的运行时接口？
