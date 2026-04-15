---
title: "浏览器扩展 Agent 架构与跨页工具链路"
source: "Apple Notes 提炼"
source_notes: ["AN-29", "AN-45", "AN-48"]
type: capture
status: inbox
created: 2026-04-14
updated: 2026-04-14
tags:
  - inbox
source_type: migrated_note
source_path: "AI/9 - 浏览器扩展 Agent 架构与跨页工具链路.md"
---

# 浏览器扩展 Agent 架构与跨页工具链路

跨网页 Agent 的本质，不是单个页面会不会调工具，而是怎样建立一条稳定的跨页工具注册、发现和调用链路。

## 用户行为抽象

存在两类典型行为：

1. 用户在网页 A 打开聊天框，让 Agent 去网页 B 执行操作。
2. 用户在浏览器扩展里发指令，让 Agent 去网页 B 执行操作。

第二类本质上是第一类的更强版本，因为它把入口和执行页彻底拆开了。

## 数据流核心

- Page 无法直接调用浏览器扩展 API。
- Tab 间的 content script 也不能直接互相通信。
- Background Service Worker 必须成为中枢路由器。

因此稳定链路是：
`Page -> Content Script -> Background -> Content Script -> Page`

## 抽象层级

- Page：业务页面和页面内工具声明。
- Content Script：页面与扩展之间的桥。
- Background：连接管理、注册表、路由中心。
- Agent Runtime：最终消费工具、执行调用、回收结果。

## 关键工程问题

- Transport 适配：Page、content script、background 使用什么标准 transport 互联。
- 动态注册：页面关闭、Tab 关闭、worker 重启后如何恢复注册表。
- 幂等与去重：同一个页面既消费工具又注册工具时，如何避免循环调用。
- 生命周期：Background worker 会被系统回收，必须考虑重连和离线恢复。

## 状态管理原则

- 状态不可变并自上而下流动。
- 动作只负责触发变化。
- 事件承担通知职责。
- 订阅负责让组件响应变化。

这套原则不是 UI 细节，而是跨端、跨 tab 架构也成立的组织方式。

## 设计判断

- 不要自己发明底层通信协议，优先复用已有 transport 抽象。
- Background 应该被当作 Hub，而不是简单消息转发层。
- 真正难点不在“连上”，而在连接断开、重建、注册、清理时仍然保持一致性。

一句话总结：
跨页 Agent 的门槛不在模型，而在浏览器通信、连接治理和工具生命周期管理。
