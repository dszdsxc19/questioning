---
tags:
  - ai
  - tools
  - skills
type: resource
status: active
created: 2026-03-05
updated: 2026-04-14
canonical: true
aliases:
  - AI Skills 库
---

# Skills 库

## Summary

这是一个长期维护的 Skills 索引页，用来沉淀值得复用的高效 skill，降低未来查找、安装和选型的时间成本。

## 这页解决什么问题

记录这类内容，不是为了“收藏更多”，而是为了复用沉淀。

这页的主要用途是：

- 把分散在不同仓库、网站和对话里的 skill 收到一个稳定入口
- 在下次需要时，快速找到合适的 skill，而不是重新到处搜索
- 逐步区分哪些 skill 真正高频、有效，哪些只是一次性新鲜感

## 选用原则

收录到这页的 skill，优先满足以下条件：

- 有明确用途，能回答“它帮我解决什么问题”
- 能明显节省时间，或者提升输出质量
- 适合重复调用，而不是只适合一次性尝试
- 来源清楚，仓库或文档可追溯
- 与已有条目边界清晰，不制造重复收录

## 全局速查表

| Skill | 分类 | 一句话用途 |
|---|---|---|
| `frontend-design` | UI/UX 设计 | 创作有视觉个性的前端界面 |
| `ui-ux-pro-max` | UI/UX 设计 | 海量设计资源库：风格、配色、字体、UX 准则 |
| `brainstorming` | UI/UX 设计 | 把模糊想法对话式地变成设计规范 |
| `next-best-practices` | 前端规范 | 编写或审查 Next.js 代码时的最佳实践 |
| `vercel-react-best-practices` | 前端规范 | React + Vercel 技术栈最佳实践 |
| `web-design-guidelines` | 前端规范 | 检查前端代码是否符合 Web 工程规范 |
| `copywriting` | 内容创作 | 高转化率营销文案写作 |
| `browser-use` | 自动化 | 浏览器自动化，持久 Session，多步骤工作流 |
| `baoyu-infographic` | 视觉创作 | 信息图表与数据可视化自动生成 |
| `baoyu-cover-image` | 视觉创作 | 文章封面图或首图设计 |
| `baoyu-comic` | 视觉创作 | 四格漫画与连环画创作 |
| `baoyu-article-illustrator` | 视觉创作 | 文章配图与插图生成 |
| `documentation-writer` | 内容创作 | 技术文档、README、API 文档写作 |
| `obsidian-markdown` | Obsidian | 创建和编辑 Obsidian Flavored Markdown |
| `obsidian-bases` | Obsidian | 创建和编辑 Obsidian Bases 数据库 |
| `json-canvas` | Obsidian | 创建和编辑 JSON Canvas 文件 |
| `obsidian-cli` | Obsidian | 通过 Obsidian CLI 与 vault 交互 |
| `defuddle` | 网页提取 | 从网页提取干净的 markdown |

## 分类索引

以下分类是为了帮助快速定位，不是为了制造复杂层级。新增条目时，优先沿用已有分类；只有在现有分类明显失效时，再考虑重组。

### UI / UX 设计

这类 skill 用于界面风格、设计规范和从想法到界面的落地过程。

### `frontend-design`

创建独特、生产级前端界面，拒绝同质化的默认审美。

- 适合：需要做出有记忆点的界面时，包含主题、字体、视觉层级等整体设计
- 不适合：只是加一个按钮或做小改动
- 来源：`anthropics/skills`

```bash
npx skills add https://github.com/anthropics/skills --skill frontend-design
```

### `ui-ux-pro-max`

综合设计智库：风格、配色、字体、UX 准则和图表方案。

- 适合：选配色方案、定字体组合、找 UX 规范参考
- 覆盖：UI 审美和 UX 体验双线
- 来源：`nextlevelbuilder/ui-ux-pro-max-skill`

```bash
npx skills add https://github.com/nextlevelbuilder/ui-ux-pro-max-skill --skill ui-ux-pro-max
```

### `brainstorming`

通过自然对话，把模糊想法转成更清晰的设计规范。

- 适合：项目初期只有方向但还没有具体落地方案
- 节奏：一次推进一个问题，逐步收敛
- 来源：`obra/superpowers`

```bash
npx skills add https://github.com/obra/superpowers --skill brainstorming
```

### 前端开发规范

这类 skill 用于代码实现和质量检查，不负责审美本身。

### `next-best-practices`

编写或审查 Next.js 代码时自动套用最佳实践。

- 适合：日常写 Next.js 项目
- 注意：有 `file-conventions.md` 附属文档，安装后一并阅读
- 来源：`vercel-labs/next-skills`

```bash
npx skills add https://github.com/vercel-labs/next-skills --skill next-best-practices
```

### `vercel-react-best-practices`

React + Vercel 技术栈的最佳实践规范。

- 适合：纯 React 项目或部署在 Vercel 上的项目
- 与 `next-best-practices` 互补，React 层面更通用
- 来源：`vercel-labs/agent-skills`

```bash
npx skills add https://github.com/vercel-labs/agent-skills --skill vercel-react-best-practices
```

### `web-design-guidelines`

检查前端代码是否符合 Web 工程规范。

- 适合：Code Review 场景，让 AI 扫描已写代码的合规性
- 注意：这是工程质量检查，不是设计审美检查
- 来源：`vercel-labs/agent-skills`

```bash
npx skills add https://github.com/vercel-labs/agent-skills --skill web-design-guidelines
```

### 内容创作

这类 skill 用于生成对外表达，不用于存放个人最终判断。

### `copywriting`

高转化率营销文案专家模式。

- 适合：落地页、邮件、广告语、CTA、产品介绍
- 来源：`coreyhaines31/marketingskills`

```bash
npx skills add https://github.com/coreyhaines31/marketingskills --skill copywriting
```

### `documentation-writer`

技术文档、README、API 文档写作。

- 适合：项目文档、API 参考、使用指南
- 来源：`github/awesome-copilot`

```bash
npx skills add https://github.com/github/awesome-copilot --skill documentation-writer
```

### 自动化

这类 skill 用于跨页面、跨步骤的重复 Web 任务。

### `browser-use`

浏览器自动化：持久 Session + 多步骤工作流。

- 适合：跨页面操作、填表、抓数据、自动化重复 Web 任务
- 亮点：Session 跨命令持久，不用每次重新打开浏览器
- 来源：`browser-use/browser-use`

```bash
npx skills add https://github.com/browser-use/browser-use --skill browser-use
```

### 视觉创作

这类 skill 用于图像、封面、信息图和漫画等视觉输出。

### `baoyu-infographic`

信息图表与数据可视化自动生成。

- 适合：将数据、流程、概念转化成信息图表
- 来源：`baoyu/baoyu-skills`

```bash
npx skills add https://github.com/baoyu/baoyu-skills --skill baoyu-infographic
```

### `baoyu-cover-image`

文章封面图或首图设计。

- 适合：为文章、帖子、演讲稿生成封面图
- 来源：`baoyu/baoyu-skills`

```bash
npx skills add https://github.com/baoyu/baoyu-skills --skill baoyu-cover-image
```

### `baoyu-comic`

四格漫画与连环画创作。

- 适合：用漫画形式讲故事、解释概念、制作教程
- 来源：`baoyu/baoyu-skills`

```bash
npx skills add https://github.com/baoyu/baoyu-skills --skill baoyu-comic
```

### `baoyu-article-illustrator`

文章配图与插图生成。

- 适合：为文章段落生成配套插图或示意图
- 来源：`baoyu/baoyu-skills`

```bash
npx skills add https://github.com/baoyu/baoyu-skills --skill baoyu-article-illustrator
```

### Obsidian 相关

这类 skill 用于与 Obsidian vault 交互，包括 Markdown、Bases、Canvas 和 CLI 操作。

### `obsidian-markdown`

创建和编辑 Obsidian Flavored Markdown（`.md`）文件。

- 适合：需要创建或编辑包含 wikilinks、嵌入、callouts、properties 等 Obsidian 特有语法的 Markdown 文件
- 来源：`kepano/obsidian-skills`

```bash
npx skills add git@github.com:kepano/obsidian-skills.git --skill obsidian-markdown
```

### `obsidian-bases`

创建和编辑 Obsidian Bases（`.base`）数据库文件。

- 适合：需要创建包含视图、过滤器、公式和摘要的 Obsidian 数据库
- 来源：`kepano/obsidian-skills`

```bash
npx skills add git@github.com:kepano/obsidian-skills.git --skill obsidian-bases
```

### `json-canvas`

创建和编辑 JSON Canvas 文件（`.canvas`）。

- 适合：需要创建包含节点、边、分组和连接的思维导图或可视化画布
- 来源：`kepano/obsidian-skills`

```bash
npx skills add git@github.com:kepano/obsidian-skills.git --skill json-canvas
```

### `obsidian-cli`

通过 Obsidian CLI 与 vault 交互。

- 适合：插件开发、主题开发、命令行操作 Obsidian
- 来源：`kepano/obsidian-skills`

```bash
npx skills add git@github.com:kepano/obsidian-skills.git --skill obsidian-cli
```

### `defuddle`

从网页提取干净的 markdown，去除杂乱内容以节省 tokens。

- 适合：需要抓取网页内容并转换为干净的 markdown 格式
- 来源：`kepano/obsidian-skills`

```bash
npx skills add git@github.com:kepano/obsidian-skills.git --skill defuddle
```

## 选用策略

这部分不是再次罗列清单，而是回答“我现在该用哪个 skill 处理什么问题”。

```text
想法阶段      -> brainstorming
定风格/配色   -> ui-ux-pro-max -> frontend-design
写 Next.js    -> next-best-practices
改完想检查    -> web-design-guidelines
写 React      -> vercel-react-best-practices
写文案        -> copywriting
写文档        -> documentation-writer
批量 Web 操作 -> browser-use
信息图表      -> baoyu-infographic
文章配图      -> baoyu-article-illustrator / baoyu-cover-image
漫画创作      -> baoyu-comic
```

## Sources

- Skills 库地址：https://skills.sh/
- 官方指南：[[Skills 最佳实践指南索引]]
- 相关来源仓库：
  - `anthropics/skills`
  - `nextlevelbuilder/ui-ux-pro-max-skill`
  - `obra/superpowers`
  - `vercel-labs/next-skills`
  - `vercel-labs/agent-skills`
  - `coreyhaines31/marketingskills`
  - `github/awesome-copilot`
  - `browser-use/browser-use`
  - `baoyu/baoyu-skills`
  - `kepano/obsidian-skills`

## Related

- [[Skill 机制与渐进式披露]]
- [[AI 辅助设计工作流]]
- [[Prompt 设计原则]]

## Open Questions

- 哪些 skill 实际复用频率高，值得保留在主表中？
- 哪些 skill 只是“看起来有趣”，但长期并不值得维护？
- 哪些 skill 已经过时，或者已被更好的替代方案覆盖？
- 哪些 skill 应该升级为单独工具页，而不是继续停留在总表里？
