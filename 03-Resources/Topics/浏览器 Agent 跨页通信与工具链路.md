---
tags:
  - ai
  - agent
  - browser
type: resource
status: active
created: 2026-04-21
updated: 2026-04-21
canonical: true
aliases:
  - 浏览器扩展 Agent 架构与跨页工具链路
---

# 浏览器 Agent 跨页通信与工具链路

## Summary

跨页浏览器 Agent 的难点不在模型，而在浏览器通信限制、连接治理和工具生命周期管理。

## 这页解决什么问题

这页用来回答三个稳定问题：

- 浏览器里跨页 Agent 的最小链路应该怎么画
- 为什么 background 不能只被当成消息转发层
- 跨页工具注册、发现、调用时，真正难的是哪几层

## 选用原则

收录到这页的内容，优先满足以下条件：

- 能解释 page、content script、background 各自职责
- 能帮助判断跨页通信故障到底卡在哪一层
- 能连接到工具注册、连接恢复和生命周期治理

## 核心内容

### 一句话定义

跨页浏览器 Agent，本质上是把“工具入口”和“执行页面”拆开后，仍然维持一条稳定的注册、发现和调用链路。

### 为什么不能直接互调

浏览器扩展环境里有三个基本限制：

- Page 不能直接调用扩展 API
- 不同 tab 里的 content script 不能直接互相通信
- background service worker 还会被系统回收

所以稳定链路通常不是点对点，而是：

`Page -> Content Script -> Background -> Content Script -> Page`

### 四层职责划分

- Page：业务页面与页面内工具声明
- Content Script：页面与扩展之间的桥
- Background：连接管理、注册表、路由中心
- Agent Runtime：消费工具、发起调用、回收结果

如果这四层职责不先分清，后面所有问题都会混在一起。

### 为什么 background 应该被当成 Hub

background 的价值不只是“帮你转一条消息”，而是维持全局一致性：

- 哪些页面当前可用
- 哪些工具已经注册
- 连接断开后如何恢复
- 调用结果应该路由回哪里

把它只当消息中转层，通常会在重连、清理和多 tab 一致性上出问题。

### 真正常见的工程问题

- Transport 适配：page、content script、background 是否通过统一 transport 抽象通信
- 动态注册：页面关闭、tab 关闭、worker 重启后，注册表如何恢复
- 幂等与去重：同一个页面既消费工具又注册工具时，如何避免循环调用
- 生命周期：连接断开、重新挂载、离线恢复时，状态是否还能保持一致

### 设计判断

- 不要自己发明底层通信协议，优先复用已有 transport 抽象
- background 应被当成 Hub，而不是简单的 forwarding layer
- 重点不在“能不能连上”，而在断开、重建、注册、清理时是否仍然一致

### 为什么这页和 Agent 编排不是同一个主题

Agent 编排关注的是主代理与子代理如何分工、委托和汇总。  
这页关注的是浏览器环境下，跨页工具链路怎样稳定运行。

前者是任务协调问题。  
后者是运行时通信与生命周期问题。

## Sources

- 来源：旧库《小宇宙》中的浏览器扩展 Agent 跨页链路迁移稿，已并入本页

## Related

- [[Agent 编排与 Sub-agent]]
- [[Agent 工作空间与 Sandbox]]

## Open Questions

- 你当前最容易出故障的，是 page 到 content script，还是 background 到执行页这段链路？
- 哪些注册信息必须持久化到 background 外部，才能避免 worker 回收后丢状态？
