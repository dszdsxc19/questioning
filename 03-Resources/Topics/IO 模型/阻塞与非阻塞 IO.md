---
tags:
  - systems
  - io
  - operating-system
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 阻塞与非阻塞 IO

## Summary

阻塞与非阻塞描述的是：调用方线程在发起 IO 之后，等待期间会不会被挂起。

## 区别

- 阻塞 IO：线程会睡眠等待，直到数据就绪
- 非阻塞 IO：数据未就绪时立即返回，常见错误码是 `EAGAIN`

## 关键判断

- 非阻塞不等于高性能，它只是解除线程等待。
- 真正让单线程高并发成立的，是非阻塞配合 [[IO 多路复用与 epoll]]。
- 如果只有非阻塞、没有多路复用，就容易退化成 CPU 空转轮询。

## Related

- [[IO 模型知识地图]]
- [[同步与异步 IO]]
- [[Redis 高并发架构串讲]]

## Open Questions

- 在真实服务里，什么时候“多线程阻塞 IO”反而比事件驱动更简单且足够好？
