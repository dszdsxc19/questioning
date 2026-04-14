---
tags:
  - inbox
  - ai
  - migration
type: capture
status: inbox
created: 2026-04-14
updated: 2026-04-14
---

# AI 旧库迁移待判定

## Summary

这页记录旧 `30 Knowledge/AI` 中暂不进入正式 wiki 主网络的内容，避免它们被静默遗忘，也避免低价值内容直接污染 `03-Resources`。

## Sources

- 来源目录：`/Users/admin/Desktop/mine/小宇宙/30 Knowledge/AI`

## Related

- [[Prompt 设计原则]]
- [[Agent 编排与 Sub-agent]]
- [[AI 可观测性]]

## Open Questions

- 哪些条目未来值得被重新提炼，而不是永久留在待判定区？
- 这些内容里，哪些只是时效刺激，哪些其实藏着可以抽象出的长期问题？

## 待判定清单

### 不迁入正式 vault

- `AGENTS.md`
  - 旧目录导航页，结构逻辑已经被当前 vault 替代
- `提示词/AGENTS.md`
  - 旧提示词库规范，不应与当前 vault 规则并存
- `车辆-上海1000辆车统计-2026.md`
  - 与当前 AI 主题网络无关
- `马斯克预警：留给旧世界的时间只剩 2000 天，中国握着唯一的“王牌”.md`
  - 强时效、强观点、弱验证，不适合作为长期参考页
- `AGI-Next 全明星对话速记：从 Scaling、Agent 到自学习.md`
  - 更像事件摘录而不是稳定概念页

### 暂不迁，除非后续确认高复用价值

- `提示词/系统-NotebookLM.md`
  - 平台绑定强，且当前内容过薄
- `提示词/生图-漫画脚本.md`
  - 除非你高频使用，否则维护成本高于收益
- `提示词/写作-技术文章.md`
  - 需要先判断是否真在重复节省时间

### 已吸收进主题页，不再单独迁入

- `Sub-agent 机制与使用场景.md`
  - 已并入 [[Agent 编排与 Sub-agent]]
- `Skill的执行原理和渐进式披露设计理念.md`
  - 已并入 [[Skill 机制与渐进式披露]]
- `大模型与 Agent 测试方法：从结果评估到过程评估.md`
  - 已并入 [[Agent 评估]]
- `AI Tracing 语义规范与 Mastra 实现.md`
  - 已并入 [[AI 可观测性]]
- `工作流/AI时代的设计工作流.md`
  - 已并入 [[AI 辅助设计工作流]]
- `12 - Prompt 资产：质检、意图识别与任务编排.md`
  - 已并入 [[Prompt 设计原则]]

### 已迁入 Prompt 资源库

- `提示词/编程-前端-按钮组件.md`
  - 已迁入 [[前端组件提示词]]
- `提示词/生图-小红书风格.md`
  - 已迁入 [[小红书图文提示词]]
- `提示词/生图-信息图 PPT.md`
  - 已迁入 [[可编辑 PPT 提示词]]
- `提示词/生图-风格提取.md`
  - 已迁入 [[图像风格提取提示词]]

### 建议后续再处理

- `安装配置/Claude Code 安装与配置.md`
  - 如果要保留，应重写为当前有效且稳定的工具概念页
- `安装配置/Claude Code 五件套配置.md`
  - 适合拆成工具关系说明，而不是继续保留旧教程
- `Mastra 的 Workspace、Sandbox 与 NAS 挂载设计.md`
  - 目前只作为 [[AI 可观测性]] 的补充来源，后续可视需要拆成单独框架页
- `9 - 浏览器扩展 Agent 架构与跨页工具链路.md`
  - 当前只作为 [[Agent 编排与 Sub-agent]] 的补充来源，后续若浏览器 Agent 成为重点，可升级为独立页
- `原理/Karpathy 的极简 GPT 系列：从 minGPT 到 microGPT.md`
  - 有长期学习价值，但与本轮 Agent/Workflow 主线关联较弱，可下一轮处理
