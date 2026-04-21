---
tags:
  - ai
  - agent
  - infrastructure
type: resource
status: active
created: 2026-04-20
updated: 2026-04-20
canonical: true
aliases:
  - Mastra 的 Workspace、Sandbox 与 NAS 挂载设计
---

# Agent 工作空间与 Sandbox

## Summary

Agent 工作空间的核心不是“能跑命令”，而是为 Agent 提供一个受控的工作环境；sandbox 只是其中负责执行命令的一层能力。

## 这页解决什么问题

这页用来澄清三个容易混淆的问题：

- workspace 和 sandbox 是什么关系
- 什么情况下只需要文件系统，什么情况下必须加入命令执行
- 远程存储、NAS 或对象存储接入时，应该挂在哪一层

## 选用原则

收录到这页的内容，优先满足以下条件：

- 能解释 Agent 工作环境的能力边界
- 能帮助判断文件系统、执行环境、远程存储分别属于哪一层
- 不被某个单一框架的 API 细节绑死

## 核心内容

### 一句话定义

`workspace = Agent 的受控工作环境`

它通常包含：

- 文件系统
- 命令执行环境
- 搜索或代码检索
- 技能或外部能力接入

而 `sandbox` 更像其中负责运行命令、启动进程和执行代码的子能力。

### 为什么 workspace 和 sandbox 不能混为一谈

如果把两者混成一个概念，就很容易误判系统边界：

- 文件读写能力不等于命令执行能力
- 能执行 shell 不等于有可持久化的工作目录
- 远程存储接入也不等于自动拥有隔离执行环境

更稳的理解是：

- workspace 回答“Agent 在什么环境里工作”
- sandbox 回答“Agent 在哪里执行命令”

### 常见三种配置模式

1. 只有文件系统，没有 sandbox  
适合文档处理、知识整理、只读检索类 Agent。

2. 只有 sandbox，没有持久文件系统  
适合一次性代码执行、SQL 运行、短生命周期任务。

3. 文件系统 + sandbox 同时存在  
适合 coding agent、调试 agent、需要读写再执行的任务。

### NAS / 对象存储应该挂在哪一层

远程存储的本质是文件系统接入问题，而不是命令执行问题。

更准确地说：

- NAS 挂载、S3、对象存储、远程仓库同步更接近 `workspace.filesystem`
- 本地 shell、容器、云端隔离环境更接近 `workspace.sandbox`

如果把远程存储误当成 sandbox 能力，后续会把权限、持久化和执行隔离混在一起。

### 为什么这对 Agent 架构判断重要

一旦 Agent 开始长期工作、修改文件、运行测试或调用外部工具，工作环境边界就会直接影响：

- 权限控制
- 持久化策略
- 成本
- 调试方式
- 安全模型

很多“Agent 不稳定”问题，根本不是模型问题，而是工作环境模型一开始就画错了。

## Sources

- 来源：旧库《小宇宙》中的 workspace / sandbox 迁移稿，已并入本页

## Related

- [[Agent 编排与 Sub-agent]]
- [[AI 可观测性]]

## Open Questions

- 你当前最常用的 Agent 场景，更像“文档型工作空间”还是“代码执行型工作空间”？
- 哪些能力应该留在 workspace，哪些能力应该通过外部工具按需接入？
