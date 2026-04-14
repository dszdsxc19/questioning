---
tags:
  - database
  - mysql
  - transaction
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# MySQL 四种隔离级别

## Summary

事务隔离级别定义了一个事务能看到其他事务多少变化。理解它，先不要背术语，先分清脏读、不可重复读、幻读三类并发问题。

## 三类问题

- 脏读：读到了别人未提交的数据
- 不可重复读：同一事务两次读同一行，结果不同
- 幻读：同一事务两次范围查询，结果集多出或少了行

## 四种级别

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
|------|------|------|------|
| Read Uncommitted | 可能 | 可能 | 可能 |
| Read Committed | 解决 | 可能 | 可能 |
| Repeatable Read | 解决 | 解决 | 理论上可能 |
| Serializable | 解决 | 解决 | 解决 |

## MySQL 的实践重点

- InnoDB 默认是 `Repeatable Read`
- `Read Committed` 每次 `SELECT` 都生成新 `Read View`
- `Repeatable Read` 事务内复用同一快照
- InnoDB 通过 `Next-Key Lock` 处理 RR 下的幻读问题

## Related

- [[数据库知识索引]]
- [[MVCC（多版本并发控制）]]

## Open Questions

- 业务里什么时候值得主动降到 `Read Committed`？
- “默认 RR” 这个选择在今天的 MySQL 生态里还有哪些历史包袱？
