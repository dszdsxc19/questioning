---
tags:
  - distributed-systems
  - consistency
  - cap
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# CAP 定理

## Summary

在分布式系统中，网络分区发生时，系统无法同时满足强一致性和高可用性。P 几乎是必选项，所以实际是在 CP 和 AP 之间做取舍。

## 三个特性

- Consistency：每次读都返回最新写入值，或者返回错误
- Availability：每次请求都能收到非错误响应
- Partition Tolerance：网络分区发生时系统仍然运行

## 为什么只能选两个

分区是分布式系统的客观事实，因此 P 基本不能放弃。真正的选择是：

- CP：牺牲可用性，保证一致性
- AP：牺牲强一致，保证可用性

## 电商系统如何选择

- 支付核心数据：CP
- 库存扣减：CP
- 分布式锁：CP
- 商品详情：AP
- 购物车：AP
- 推荐系统：AP
- 搜索索引：AP

## PACELC

CAP 只讨论分区时的选择，PACELC 进一步补上“平时没分区时，也要在延迟和一致性之间做取舍”。

## Related

- [[微服务电商系统架构总览]]
- [[强一致性 vs 最终一致性]]

## Open Questions

- 你在真实业务里最常遇到的错误，是把 AP 系统幻想成 CP，还是把“最终一致”误解成“不需要补偿”？
