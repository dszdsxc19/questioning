---
tags:
  - frontend
  - ui
  - tools
type: resource
status: active
created: 2026-03-04
updated: 2026-04-14
canonical: true
aliases:
  - UI 组件库收藏
  - Tailwind UI 组件库
---

# UI 组件库选型

## Summary

这是一个长期维护的 UI 组件库选型索引页，用来沉淀常用且审美在线的组件库，降低未来做界面时的搜索与决策成本。

## 这页解决什么问题

记录这些 UI 库，不是为了囤积链接，而是为了在需要做界面时，能够更快完成选型。

这页的主要用途是：

- 把分散的 UI 组件库整理成一个稳定入口
- 按场景快速定位合适的库，而不是每次重新搜索
- 区分哪些库适合长期维护，哪些更适合做视觉亮点或快速 demo

## 选型原则

收录到这页的组件库，优先满足以下条件：

- 风格边界明确，能回答“它适合什么场景”
- 审美稳定，不只是短期流行元素堆砌
- AI 易于理解和修改，适合协作式开发
- 生态、文档或代码质量足够可靠
- 和已有条目有清晰差异，不是同类库的重复堆叠

## 全局速查表

| 组件库 | 分类 | 一句话用途 |
|---|---|---|
| `Aceternity UI` | 视觉炸裂组 | 适合做有强烈视觉冲击的落地页与 Hero 区域 |
| `Magic UI` | 视觉炸裂组 | 擅长 Bento Grid、渐变光晕和模块化展示 |
| `HeroUI` | 视觉炸裂组 | 适合应用内组件、表单和高级感微交互 |
| `shadcn/ui` | 基石组 | 长期维护最稳，AI 最容易读懂和修改 |
| `Radix Themes` | 基石组 | 无障碍和语义结构强，适合企业级应用 |
| `Origin UI` | 基石组 | 在 shadcn 基础上补充更多动画和交互细节 |
| `ui.ibelick` | 小众精品组 | 微交互质量高，适合个人项目和作品集 |
| `Cult UI` | 小众精品组 | 黑白灰残酷主义，适合独特调性的产品 |
| `Mantine` | 功能扩展组 | 组件多、功能全，适合复杂后台和完整应用 |
| `DaisyUI` | 功能扩展组 | 上手快、主题多，适合原型和快速 demo |

## 分类索引

以下分类服务于选型效率，而不是追求完整 taxonomy。新增条目时，优先判断它是否真的代表新的设计语言或使用场景。

### 视觉炸裂组

适合追求第一眼冲击力和展示感的界面。

### `Aceternity UI`

- 风格：赛博朋克、科幻感
- 招牌特效：极光背景、光束扫射、粒子轨迹、3D 卡片悬浮
- 适用场景：AI 产品落地页、SaaS Hero 区域、黑客松 Demo
- 亮点：组件可复制粘贴即用，对 AI 协作友好
- 链接：https://ui.aceternity.com/components

### `Magic UI`

- 风格：硅谷创业风、克制的动感
- 招牌特效：Bento Grid、粒子字体、渐变光晕
- 适用场景：产品主页、功能展示区、定价页
- 亮点：模块化布局能力强，拆开可重组
- 链接：https://magicui.design

### `HeroUI`

- 原名：`NextUI`
- 风格：玻璃拟态、高级感微交互
- 招牌特效：Ripple 点击涟漪、平滑过渡、模糊背景
- 适用场景：中后台管理、应用内组件、表单场景
- 亮点：主题系统成熟，配合动画库很灵活
- 链接：https://www.heroui.com

### 基石组

适合长期维护、稳定迭代和 AI 协作修改。

### `shadcn/ui`

- 风格：简洁、现代、黑白主色
- 核心价值：行业事实标准，AI 对它的代码理解最稳定
- 适用场景：任何需要长期维护的项目
- 亮点：代码直接进入项目，控制权高，生态成熟
- 链接：https://ui.shadcn.com

### `Radix Themes`

- 风格：瑞士设计风、强调排版和秩序
- 核心价值：无障碍与语义化结构强
- 适用场景：企业级应用、文档站点、无障碍要求高的产品
- 亮点：底层规范强，适合对交互一致性要求高的场景
- 链接：https://www.radix-ui.com/themes/docs/overview/getting-started

### `Origin UI`

- 风格：shadcn 进阶版，动画更细腻
- 核心价值：在稳固底座上补足更丰富的交互细节
- 适用场景：不想从零配置 shadcn 动画的场景
- 亮点：组件数量多，表单和输入细节尤其强
- 链接：https://originui.com

### 小众精品组

适合强调调性和记忆点，而不是追求通用标准答案。

### `ui.ibelick`

- 风格：独立开发者手工感、微交互细腻
- 核心价值：Hover 和 Focus 等细节打磨质量高
- 适用场景：个人项目、作品集、独立产品
- 亮点：代码质量高，值得直接学习实现方式
- 链接：https://ui.ibelick.com

### `Cult UI`

- 风格：黑白灰残酷主义、极简工业感
- 核心价值：反流行设计语言，容易建立差异化
- 适用场景：Web3、工具类产品、追求独特调性的 SaaS
- 亮点：组件不多，但设计语言很强
- 链接：https://www.cult-ui.com

### 功能扩展组

适合解决复杂应用场景，而不是只提供视觉亮点。

### `Mantine`

- 风格：功能优先、专业中性
- 核心价值：组件覆盖广，适合快速搭建完整应用
- 适用场景：Dashboard、管理后台、需要较完整组件集的应用
- 亮点：内置 hooks 丰富，不强依赖 Tailwind
- 链接：https://mantine.dev

### `DaisyUI`

- 风格：活泼、多主题
- 核心价值：Tailwind 的语义化组件层，上手成本低
- 适用场景：快速原型、静态站点、需要多主题切换的产品
- 亮点：内置主题多，切换成本低
- 链接：https://daisyui.com

## 快速选型指南

| 场景 | 推荐 |
|---|---|
| AI 产品落地页，要震撼 | `Aceternity UI` / `Magic UI` |
| 长期维护的 SaaS，AI 协作 | `shadcn/ui` + `Origin UI` |
| 快速出 Demo，要好看 | `HeroUI` / `DaisyUI` |
| 企业级，无障碍合规 | `Radix Themes` |
| 个人品牌，要记忆点 | `ui.ibelick` / `Cult UI` |
| 复杂后台，功能为王 | `Mantine` |
| Web3 或暗黑美学 | `Cult UI` |

## 组合策略

这部分回答的不是“哪个库最好”，而是“如何组合使用更合适”。

```text
基础结构   -> shadcn/ui
动效加成   -> Aceternity UI
布局亮点   -> Magic UI
细节打磨   -> ui.ibelick
主题系统   -> DaisyUI
```

核心原则：组件库是工具，不是枷锁。优先用稳定底座打底，再从其他库中有选择地借用特效、布局或细节。

## Sources

- Aceternity UI：https://ui.aceternity.com/components
- Magic UI：https://magicui.design
- HeroUI：https://www.heroui.com
- shadcn/ui：https://ui.shadcn.com
- Radix Themes：https://www.radix-ui.com/themes/docs/overview/getting-started
- Origin UI：https://originui.com
- ui.ibelick：https://ui.ibelick.com
- Cult UI：https://www.cult-ui.com
- Mantine：https://mantine.dev
- DaisyUI：https://daisyui.com

## Related

- [Skills 库](</Users/admin/Desktop/questioning/03-Resources/Tools/Skills 库.md>)
- [[AI 辅助设计工作流]]
- [[Prompt 设计原则]]

## Open Questions

- 哪些库只是视觉强，但长期维护成本过高？
- 哪些库最适合作为 AI 协作开发的默认底座？
- 哪些库值得单独拆出学习页，而不是继续只留在选型清单里？
- 未来是否需要按“落地页 / 应用内组件 / 后台系统”重排分类？
