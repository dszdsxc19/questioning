---
tags:
  - database
  - mysql
  - logging
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# MySQL 三大日志：redo log、undo log、binlog

## Summary

这三类日志分别服务三件事：崩溃恢复、回滚与 MVCC、主从复制。最常见的错误不是不会背定义，而是把它们的职责边界混掉。

## 职责分工

| 日志 | 解决的问题 | 所在层次 |
|------|------------|----------|
| `redo log` | 崩溃恢复、持久性 | InnoDB 存储引擎层 |
| `undo log` | 回滚、历史版本 | InnoDB 事务系统 |
| `binlog` | 主从复制、审计、逻辑恢复 | MySQL Server 层 |

## 核心判断

- 主从复制依赖 `binlog`，不是 `redo log`
- `undo log` 是回滚与 MVCC 的基础，不是复制链路中心
- 两阶段提交的必要性，来自 `redo log` 和 `binlog` 分属不同层次

## Related

- [[数据库知识索引]]
- [[MVCC（多版本并发控制）]]
- [[MySQL 四种隔离级别]]

## Open Questions

- 两阶段提交具体在崩溃恢复时如何保证一致性？
- 行格式 `binlog`、语句格式 `binlog`、混合格式分别影响什么？
