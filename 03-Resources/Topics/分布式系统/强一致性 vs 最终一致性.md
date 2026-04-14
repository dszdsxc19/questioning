---
tags:
  - distributed-systems
  - consistency
  - architecture
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 强一致性 vs 最终一致性

## Summary

强一致性和最终一致性讨论的是：系统中的多个副本或多个服务，在状态变化之后，要在多大时间尺度上保持一致。

## 核心区别

- 强一致性：一旦写入成功，之后所有读都必须看到新值
- 最终一致性：允许短时间不一致，但要求最终收敛

## 场景理解

支付、库存扣减、分布式锁等场景通常更偏强一致；商品展示、推荐、搜索、日志这类场景通常接受最终一致。

## Related

- [[微服务电商系统架构总览]]
- [[CAP 定理]]
- [[Saga 分布式事务]]

## Open Questions

- 哪些业务其实只需要“用户感知上的一致”，并不需要真正的强一致？
