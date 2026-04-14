# 06-Synthesis AGENTS

这个目录优先存放高纯度个人智慧、方法论、长期判断与跨领域整合页面。

这里的 Markdown 笔记在遵守 vault 根级 `AGENTS.md` 的前提下，补充以下局部要求：

## Frontmatter

- 必须包含统一最小字段集：`tags`、`type`、`status`、`created`、`updated`
- 必须包含 `objective_function`

推荐格式：

```yaml
---
tags:
  - synthesis
type: synthesis
status: draft
created: YYYY-MM-DD
updated: YYYY-MM-DD
objective_function: 这篇笔记最终在优化什么
---
```

## objective_function 的用途

- 用来约束作者：这篇笔记最终要优化什么
- 用来区分这页是在追求判断清晰、结构稳定、行动转化，还是别的结果
- 它不是正文内容，优先放在 frontmatter，而不是正文小节

## 写法要求

- 写成一句清晰陈述，不要写成空泛口号
- 优先描述“这篇笔记最终在优化什么”
- 如果只是记录素材、临时摘录或粗糙草稿，不要为了补字段而写空话
- `objective_function` 应该能帮助作者在写作时判断什么该保留，什么该删去

## 结构策略

在设计任何目录的下级结构时，优先采用低复杂度策略：

1. 先只建一级
2. 当某个目录下大概超过 15 到 30 个页面，并且检索开始变差，再考虑加第二级
3. 第二级也按稳定概念分，不按短期兴趣分

这个策略的目标是控制层级膨胀，避免因为“看起来更系统”而过早细分目录。
