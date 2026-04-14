---
tags:
  - systems
  - io
  - index
type: index
status: active
created: 2026-04-14
updated: 2026-04-14
canonical: true
reuse_value: high
---

# IO 模型知识地图

## Summary

这页是 IO 模型主题的导航页。主问题不是定义本身，而是：Redis 单线程为什么还能处理高并发连接。

## 知识点地图

```text
阻塞 / 非阻塞
      ↓
IO 多路复用
      ↓
事件循环
      ↓
文件描述符与 Socket

同步 / 异步 作为另一条独立维度横切其上
```

## 阅读顺序

1. [[阻塞与非阻塞 IO]]
2. [[同步与异步 IO]]
3. [[文件描述符与 Socket]]
4. [[IO 多路复用与 epoll]]
5. [[事件循环 Event Loop]]
6. [[Redis 高并发架构串讲]]

## Related

- [[数据库知识索引]]

## Open Questions

- 这组知识更该从操作系统抽象切入，还是从 Redis/Node.js 这类具体系统切入？
- `io_uring` 值不值得单独开一页，还是先留在“同步/异步 IO”里？
