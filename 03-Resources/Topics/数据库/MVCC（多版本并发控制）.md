---
tags:
  - database
  - mysql
  - concurrency
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# MVCC（多版本并发控制）

## Summary

MVCC 通过“版本链 + Read View”让读操作读取历史版本，从而减少锁竞争，是 InnoDB 高并发读能力的基础。

## 关键对象

- `DB_TRX_ID`：当前版本最后由哪个事务写出
- `DB_ROLL_PTR`：指向旧版本的入口
- `undo log`：保存旧版本数据
- `Read View`：定义当前事务能看见哪些版本

## 先把三层对象分开

- 行记录：保存当前版本和隐藏字段
- `undo log`：保存历史版本
- `Read View`：当前事务观察系统时的可见性规则

把这三者混在一起，是学 MVCC 时最常见的错误。

## 核心价值

- 读写不必总是互相阻塞
- 可重复读依赖同一事务内复用快照
- 旧版本既能服务回滚，也能服务快照读

## Related

- [[数据库知识索引]]
- [[MySQL 四种隔离级别]]
- [[MySQL 三大日志：redo log、undo log、binlog]]

## Open Questions

- MVCC 解决了哪些并发问题，哪些问题仍然必须靠锁来处理？
- 在不同隔离级别下，Read View 的生成时机会怎样改变系统行为？
