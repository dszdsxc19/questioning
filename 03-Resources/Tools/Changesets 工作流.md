---
tags:
  - tools
  - release
  - monorepo
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
canonical: true
aliases:
  - Changeset 版本发布工作流
reuse_value: high
---

# Changesets 工作流

## Summary

`Changesets` 是一个面向 monorepo 的版本管理与发布工具。它把“记录变更”“升级版本”“生成 changelog”“实际发布”拆开，避免把版本决策和发布动作混成一次性脚本。

## 核心流程

1. 开发阶段运行 `changeset add`，为变更创建记录。
2. PR 合并后，变更记录跟随代码进入主分支。
3. 发布前运行 `changeset version`，统一更新版本号和 `CHANGELOG.md`。
4. 构建产物后，执行实际发布。

## 关键命令

```bash
# 创建变更记录
yarn changeset add

# 生成正式版本号和 changelog
yarn changeset version

# 生成预发布版本号
yarn changeset version --snapshot alpha
```

## 使用判断

- 适合多包仓库，需要显式记录每次改动对哪些包有影响。
- 适合团队协作，希望把“版本决策”前置到 PR，而不是发布当天临时判断。
- 不适合只有单个包、且发版频率极低的小仓库；那种场景手动维护版本号可能更省事。

## 常见误区

- 不要把 `add` 和 `version` 混成同一步。`add` 发生在开发阶段，`version` 发生在发布前。
- 不要把项目私有脚本误当成 Changesets 通用能力。比如 `--since @ai-buddy/sdk` 属于当前仓库约束，不是工具本身的核心概念。
- 预发布版本只是版本策略，不替代测试和发布校验。

## Related

- 暂无内部关联页

## Open Questions

- 什么时候值得在个人项目里也用 Changesets，而不是保持手动版本管理？
- 预发布版本、正式版本和发布分支策略之间，最稳的团队协作边界是什么？
