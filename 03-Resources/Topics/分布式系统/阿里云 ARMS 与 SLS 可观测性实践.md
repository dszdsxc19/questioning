---
tags:
  - distributed-systems
  - observability
  - alicloud
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: medium
---

# 阿里云 ARMS 与 SLS 可观测性实践

## Summary

这页讨论的是工程实践里的职责分工：ARMS 更适合作为应用监控和调用链入口，SLS 更适合作为日志排障和原始数据分析入口。

## 可观测性的三件套

- Metrics：看趋势
- Traces：看链路
- Logs：看细节

## 阿里云产品分工

### ARMS

- 应用性能监控
- 业务指标
- 服务拓扑
- 分布式追踪
- 应用指标
- 告警

### SLS

- 日志采集
- 检索分析
- 可视化
- 告警

## 标准排查路径

1. 去 ARMS 看趋势和异常时间段
2. 去 ARMS 看 Trace，确认哪一跳慢或失败
3. 去 SLS 用 TraceId 查明细日志

## Related

- [[分布式链路追踪与 TraceId]]
- [[可观测性工具栈对比：开源与云服务（2026）]]

## Open Questions

- 团队真正缺的是工具本身，还是统一的“先看哪里，再下钻哪里”的排障路径？
