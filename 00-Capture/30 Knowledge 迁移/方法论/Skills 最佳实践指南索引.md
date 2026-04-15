---
title: Skills 最佳实践指南索引
tags:
  - Skills
  - Claude
  - 最佳实践
  - 方法论
  - AI编程
created: 2026-03-31
updated: 2026-03-31
source: Anthropic 官方文档
source_path: https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf
type: 索引
status: inbox
source_type: migrated_note
---

# Skills 最佳实践指南索引

> **来源**：Anthropic 官方发布的《The Complete Guide to Building Skills for Claude》
> **用途**：构建 Claude Skills 的完整参考指南，涵盖规划、设计、测试、分发全流程

## 📄 文档信息

- **标题**：The Complete Guide to Building Skills for Claude
- **格式**：PDF
- **链接**：https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf
- **语言**：英文
- **状态**：官方最新文档

---

## 📑 内容结构

### 1. Fundamentals（基础）
- 什么是 Skill
- 核心设计原则（渐进式披露、可组合性、可移植性）
- Skills + MCP 的协同模式

### 2. Planning and Design（规划与设计）
- 用例定义方法
- 三大常见类别：
  - Category 1: 文档与资产创建
  - Category 2: 工作流自动化
  - Category 3: MCP 增强
- 技术要求与文件结构
- YAML frontmatter 最佳实践

### 3. Testing and Iteration（测试与迭代）
- 三级测试方法：
  - 手动测试（Claude.ai）
  - 脚本测试（Claude Code）
  - 程序化测试（Skills API）
- 触发测试、功能测试、性能对比
- 使用 skill-creator 技能迭代

### 4. Distribution and Sharing（分发与分享）
- 个人与组织级分发
- API 集成方式
- GitHub 托管最佳实践

### 5. Patterns and Troubleshooting（模式与故障排查）
- 五大常见模式：
  - Pattern 1: 顺序工作流编排
  - Pattern 2: 多 MCP 协调
  - Pattern 3: 迭代优化
  - Pattern 4: 上下文感知工具选择
  - Pattern 5: 领域特定智能
- 常见问题与解决方案

### 6. Resources and References（资源与参考）
- 官方文档链接
- 示例 Skills 库
- 工具与实用程序

---

## 🎯 适用场景

需要查阅本文档的情况：

| 场景 | 对应章节 |
|------|----------|
| 首次创建 Skill | Chapter 1-2 |
- Skill 触发不准确 | Chapter 2（frontmatter）+ Chapter 5（故障排查）
- 需要协调多个 MCP | Pattern 2: Multi-MCP coordination
- 工作流需要优化 | Pattern 3: Iterative refinement
- 想要测试 Skill 质量 | Chapter 3: Testing and iteration
- 准备发布 Skill | Chapter 4: Distribution and sharing |
| 遇到具体错误 | Chapter 5: Troubleshooting |

---

## 🔗 相关资源

- **Skills 库索引**：[[Skills 库]]
- **skill-creator 技能**：Claude.ai 内置，可用于生成和审查 Skills
- **官方示例仓库**：https://github.com/anthropics/skills
- **MCP 文档**：Model Context Protocol 官方文档

---

## 💡 使用建议

1. **首次阅读**：完整通读 Chapter 1-2，理解核心概念
2. **实践时**：边做边查 Chapter 5 的 Patterns 部分
3. **遇到问题**：直接跳到 Troubleshooting 对应章节
4. **发布前**：检查 Reference A: Quick checklist

---

*本文档为索引笔记，详细内容请查阅原始 PDF 链接*
