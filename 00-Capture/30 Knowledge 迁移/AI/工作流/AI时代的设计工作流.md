---
title: AI 时代的设计工作流
tags:
  - 工作流
  - 设计系统
  - Tailwind
  - CSS变量
  - AI编程
created: 2026-03-05
updated: 2026-03-05
type: capture
status: inbox
source_type: migrated_note
source_path: "AI/工作流/AI时代的设计工作流.md"
---
# 🎨 AI 时代的设计工作流

> **核心命题**：AI 写代码很快，但写出来的 UI 风格飘忽、千篇一律。
> 解法是给 AI 建一套**约束系统**，让它在你的审美框架内发挥，而不是放飞自我。

---

## 一、重新理解"设计"在 AI 时代的角色

传统设计流程：设计师出图 → 开发还原。
AI 时代的设计流程：**人定规则 → AI 执行 → 人审美把关**。

你不再需要亲手写每一行 CSS，但你需要：
1. **建立审美判断力**（能区分好看和不好看）
2. **把审美翻译成规则**（主题系统、约束文件）
3. **选对工具放大 AI 的执行力**（Skills + 组件库）

---

## 二、审美资产积累

审美不是玄学，是可以系统建立的资产。

### 2.1 用 Skills 激活 AI 的审美模式

| 阶段 | 使用的 Skill | 作用 |
|---|---|---|
| 想法模糊期 | `/brainstorming` | 逐步对话，把感觉变成设计规范 |
| 选风格/配色 | `/ui-ux-pro-max` | 从97套配色、57套字体中找方向 |
| 落地实现 | `/frontend-design` | 指导 AI 产出有个性的生产级界面 |
| 代码检查 | `/web-design-guidelines` | 检查代码质量，不是审美 |

> 详细 Skill 说明见 [[Skills 库]]

### 2.2 选对组件库

AI 对不同组件库的熟悉程度差异巨大。
优先选 **AI 读得懂、改得稳**的库：

```
shadcn/ui         → AI 最熟悉，怎么改都不崩，必选基础层
Origin UI         → shadcn 的动画进阶版，细节更好
Aceternity UI     → 要特效时叠加，复制粘贴即用
Magic UI          → Bento Grid 首选
```

> 完整选型指南见 [[UI组件库精选]]

---

## 三、主题系统设计（核心）

### 3.1 为什么需要主题系统

**没有主题系统时：**
```css
/* 分散在各处的硬编码 */
.button { background: #6366f1; }
.header { color: #1e1b4b; }
.card   { border-color: #e5e7eb; }
/* 换个主色调 = 全局搜索替换，改崩概率 100% */
```

**有主题系统时：**
```css
.button { background: var(--color-primary); }
.header { color: var(--color-text-primary); }
.card   { border-color: var(--color-border); }
/* 换主色调 = 改一个变量，全局生效 */
```

### 3.2 主题文件结构

在项目中创建 `styles/theme.css`（也可以放在 `app/globals.css` 里）：

```css
/* ============================================
   THEME SYSTEM - 主题变量总控文件
   修改此文件会影响整个项目的视觉风格
   ============================================ */

:root {

  /* ── 颜色 ──────────────────────────────── */

  /* 品牌色 */
  --color-primary:         oklch(58% 0.24 260);
  --color-primary-hover:   oklch(50% 0.24 260);
  --color-primary-subtle:  oklch(95% 0.05 260);

  /* 背景层次 */
  --color-bg:              oklch(98% 0.008 260);
  --color-surface:         oklch(100% 0 0);
  --color-surface-raised:  oklch(97% 0.01 260);

  /* 文字层次 */
  --color-text-primary:    oklch(15% 0.015 260);
  --color-text-secondary:  oklch(45% 0.02 260);
  --color-text-muted:      oklch(65% 0.02 260);

  /* 边框 */
  --color-border:          oklch(90% 0.01 260);
  --color-border-subtle:   oklch(94% 0.008 260);

  /* 语义色 */
  --color-success:         oklch(60% 0.18 145);
  --color-warning:         oklch(75% 0.18 85);
  --color-error:           oklch(58% 0.22 25);


  /* ── 字体 ──────────────────────────────── */

  --font-sans:  'Inter', 'Noto Sans SC', system-ui, sans-serif;
  --font-mono:  'JetBrains Mono', 'Fira Code', monospace;
  --font-serif: 'Lora', 'Noto Serif SC', Georgia, serif;

  --text-xs:    0.75rem;   /* 12px */
  --text-sm:    0.875rem;  /* 14px */
  --text-base:  1rem;      /* 16px */
  --text-lg:    1.125rem;  /* 18px */
  --text-xl:    1.25rem;   /* 20px */
  --text-2xl:   1.5rem;    /* 24px */
  --text-3xl:   1.875rem;  /* 30px */
  --text-4xl:   2.25rem;   /* 36px */

  --leading-tight:  1.25;
  --leading-normal: 1.5;
  --leading-relaxed: 1.75;


  /* ── 间距 ──────────────────────────────── */

  --space-1:  0.25rem;   /* 4px  */
  --space-2:  0.5rem;    /* 8px  */
  --space-3:  0.75rem;   /* 12px */
  --space-4:  1rem;      /* 16px */
  --space-5:  1.25rem;   /* 20px */
  --space-6:  1.5rem;    /* 24px */
  --space-8:  2rem;      /* 32px */
  --space-10: 2.5rem;    /* 40px */
  --space-12: 3rem;      /* 48px */
  --space-16: 4rem;      /* 64px */
  --space-20: 5rem;      /* 80px */
  --space-24: 6rem;      /* 96px */


  /* ── 圆角 ──────────────────────────────── */

  --radius-sm:   0.25rem;    /* 4px  */
  --radius-md:   0.5rem;     /* 8px  */
  --radius-lg:   0.75rem;    /* 12px */
  --radius-xl:   1rem;       /* 16px */
  --radius-2xl:  1.5rem;     /* 24px */
  --radius-full: 9999px;


  /* ── 阴影 ──────────────────────────────── */

  --shadow-sm: 0 1px 2px oklch(0% 0 0 / 0.05);
  --shadow-md: 0 4px 6px oklch(0% 0 0 / 0.07), 0 1px 3px oklch(0% 0 0 / 0.06);
  --shadow-lg: 0 10px 15px oklch(0% 0 0 / 0.08), 0 4px 6px oklch(0% 0 0 / 0.05);
  --shadow-xl: 0 20px 25px oklch(0% 0 0 / 0.1), 0 8px 10px oklch(0% 0 0 / 0.05);


  /* ── 动画 ──────────────────────────────── */

  --duration-fast:   150ms;
  --duration-normal: 250ms;
  --duration-slow:   400ms;
  --ease-out:        cubic-bezier(0.16, 1, 0.3, 1);
  --ease-spring:     cubic-bezier(0.34, 1.56, 0.64, 1);
}


/* 暗色模式 */
.dark, [data-theme="dark"] {
  --color-primary:         oklch(65% 0.22 260);
  --color-bg:              oklch(12% 0.01 260);
  --color-surface:         oklch(16% 0.012 260);
  --color-surface-raised:  oklch(20% 0.012 260);
  --color-text-primary:    oklch(95% 0.008 260);
  --color-text-secondary:  oklch(70% 0.015 260);
  --color-text-muted:      oklch(50% 0.015 260);
  --color-border:          oklch(28% 0.015 260);
  --color-border-subtle:   oklch(22% 0.01 260);
}
```

### 3.3 接入 Tailwind（让 AI 用类名调用变量）

在 `tailwind.config.js` 中映射变量，让 AI 可以用熟悉的 Tailwind 类名，但底层走 CSS 变量：

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary:          'var(--color-primary)',
        'primary-hover':  'var(--color-primary-hover)',
        'primary-subtle': 'var(--color-primary-subtle)',
        bg:               'var(--color-bg)',
        surface:          'var(--color-surface)',
        'surface-raised': 'var(--color-surface-raised)',
        'text-primary':   'var(--color-text-primary)',
        'text-secondary': 'var(--color-text-secondary)',
        'text-muted':     'var(--color-text-muted)',
        border:           'var(--color-border)',
        'border-subtle':  'var(--color-border-subtle)',
        success:          'var(--color-success)',
        warning:          'var(--color-warning)',
        error:            'var(--color-error)',
      },
      fontFamily: {
        sans:  ['var(--font-sans)'],
        mono:  ['var(--font-mono)'],
        serif: ['var(--font-serif)'],
      },
      borderRadius: {
        sm:   'var(--radius-sm)',
        md:   'var(--radius-md)',
        lg:   'var(--radius-lg)',
        xl:   'var(--radius-xl)',
        '2xl': 'var(--radius-2xl)',
      },
      boxShadow: {
        sm: 'var(--shadow-sm)',
        md: 'var(--shadow-md)',
        lg: 'var(--shadow-lg)',
        xl: 'var(--shadow-xl)',
      },
    },
  },
}
```

**效果**：AI 写 `bg-primary` 就是用变量，改颜色只需改 `theme.css`，全局生效。

---

## 四、用 AGENTS.md 约束 AI 行为

在**每个代码项目根目录**放一个 `AGENTS.md`（Claude Code 每次启动会自动读取），写入 UI 开发规则：

```markdown
## UI 开发规则

**必读文件**：开始写任何 UI 代码前，先读 `styles/theme.css`。

### 强制约束

| 属性 | ❌ 禁止 | ✅ 必须用 |
|---|---|---|
| 颜色 | `#fff` `rgb()` `hsl()` `bg-gray-900` | `var(--color-*)` 或映射的 Tailwind 类 |
| 字体大小 | `text-[14px]` 任意值 | `var(--text-*)` 或标准 Tailwind 字号 |
| 圆角 | `rounded-[8px]` 任意值 | `rounded-md` / `rounded-lg` 等映射值 |
| 阴影 | 内联 `box-shadow` | `shadow-sm` / `shadow-md` 等映射值 |

### 组件库选用顺序

1. **shadcn/ui** — 基础组件首选
2. **Origin UI** — 需要动画细节时
3. **Aceternity UI** — 需要视觉特效时（Hero区域）

### 暗色模式

所有颜色变量已覆盖暗色模式，无需写 `dark:` 前缀颜色类，切换 `.dark` 类自动生效。
```

---

## 五、完整工作流：从想法到产品

```
1. 想法模糊
   └→ /brainstorming  把模糊感觉对话成具体规范

2. 定风格
   └→ /ui-ux-pro-max  从配色库/字体库选定基础方向
      ↓
      修改 styles/theme.css 里的颜色、字体变量

3. 选组件库
   └→ 参考【UI组件库精选】选型
      → 基础用 shadcn/ui，特效按需叠加

4. 开始开发
   └→ /frontend-design  激活审美模式
      → AI 读取 AGENTS.md 约束 + theme.css 变量
      → 所有 UI 代码走主题变量，不硬编码

5. 检查质量
   └→ /web-design-guidelines  扫描代码规范
      → /vercel-react-best-practices 或 /next-best-practices

6. 迭代调整
   └→ 改风格：只改 theme.css
      改组件：在 shadcn 基础上局部修改
      改文案：/copywriting
```

---

## 六、主题切换的进阶用法

### 多品牌/多主题

```css
/* 只需覆盖变量，所有组件自动跟着变 */
[data-theme="ocean"] {
  --color-primary: oklch(58% 0.22 220);   /* 蓝色系 */
}
[data-theme="forest"] {
  --color-primary: oklch(52% 0.18 145);   /* 绿色系 */
}
[data-theme="sunset"] {
  --color-primary: oklch(62% 0.24 30);    /* 橙红系 */
}
```

```tsx
// 一行切换整套主题
<body data-theme="ocean">
```

---

## 关联资源

- [[Skills 库]] — 每个阶段用哪个 Skill
- [[UI组件库精选]] — 组件库详细选型
- [[AGENTS.md]] — 知识库全局入口

---

*最后更新：2026-03-05*
