---
tags:
  - ai
  - agent
  - skills
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
canonical: true
aliases:
  - Skill的执行原理和渐进式披露设计理念
---

# Skill 机制与渐进式披露

## Summary

Skill 的本质不是一段 prompt，而是一种按需发现、按需加载、按需执行的能力封装。

## 这页解决什么问题

这页用来澄清两个经常被混淆的问题：

- Skill 到底是“知识包”还是“执行机制”
- 为什么 Skill 不能一次性全部塞进上下文，而必须采用渐进式披露

## 选用原则

收录到这页的内容，优先满足以下条件：

- 能解释 skill registry、discover、load、execute 的最小闭环
- 能帮助判断何时该做 skill，何时只需要普通 prompt 或工具
- 能说明 token 预算与上下文治理为什么决定了 skill 设计

## 核心内容

### 一句话定义

`skill = 可复用能力单元 = prompt + workflow + tools + supporting knowledge`

它通常由 `SKILL.md` 及其脚本、参考材料、模板等组成，但真正关键的是运行时机制，而不是文件名。

### 最小执行闭环

1. 注册：系统维护一个 skill registry
2. 发现：运行时只暴露 skill 摘要，而不是全部正文
3. 加载：模型或 runtime 判断需要某个 skill 后，再读取详细内容
4. 执行：把 skill 内容注入当前上下文，再调用普通工具完成任务

### 常见注册方式

- 代码注册：在程序启动时显式注册 skill 元数据
- 文件扫描：扫描 `.skills/` 等目录并读取 `SKILL.md`
- 远程注册：通过外部服务、插件市场或 MCP 风格机制动态挂载

文件扫描模式最像插件系统：启动时发现目录，读取元数据，写入 registry。

### 为什么需要渐进式披露

- 所有 skill 全量预加载会迅速耗尽上下文
- 大多数 skill 在单次任务中根本不会被调用
- 摘要先暴露、正文后加载，能把 token 用在真正相关的能力上

### 一个更实用的三层理解

1. 摘要层：只告诉模型有哪些 skill、各自做什么
2. 正文层：真正需要时才读取 `SKILL.md`
3. 扩展层：只有在任务需要时才继续读取脚本、模板、参考资料

这就是渐进式披露的工程价值：先 discover，再 load，再深入具体材料。

### Skill 适合解决的问题

- 某类任务会反复出现
- 需要稳定流程，而不是一次性灵感发挥
- 除了 prompt，还需要附带脚本、模板、参考资料
- 希望把“怎么做”抽成可复用标准件

### Skill 不适合解决的问题

- 只是一段临时提示词
- 任务边界太模糊，无法抽象成稳定 workflow
- 没有明显复用价值

### 与工具的区别

- 工具提供原子能力，如搜索、读文件、执行命令
- Skill 提供能力编排，告诉模型在什么场景下如何使用这些工具

### 与普通 Prompt 的区别

- 普通 prompt 往往是一段临时指令
- skill 是一套可发现、可装配、可扩展的能力单元
- 当 prompt 已经需要脚本、模板和明确工作流配合时，它通常已经越过了“普通 prompt”的边界

### 与 Sub-agent 的关系

- Skill 解决“能力如何封装和调用”
- Sub-agent 解决“任务如何隔离与委托”
- 两者可以组合，但不是同一层抽象

## Sources

- 旧笔记：`/Users/admin/Desktop/mine/小宇宙/30 Knowledge/AI/Skill的执行原理和渐进式披露设计理念.md`
- 现有资源：[[Skills 库]]

## Related

- [[Skills 库]]
- [[Agent 编排与 Sub-agent]]
- [[Prompt 设计原则]]

## Open Questions

- 什么时候应该把一组 prompt 升级成 skill，而不是继续保留为手工模板？
- 在 skill 很多时，摘要召回与显式命名触发各自的边界是什么？
- Skill 的最佳粒度是围绕任务、角色，还是围绕工具集合来定义？
