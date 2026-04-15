---
title: "生成高质量可编辑PPT的提示词模板"
description: "通过分离内容生成与视觉绘制，实现既有质感又能随意修改文字的完美PPT"
source: "https://baoyu.io/blog/most-valuable-prompts-for-high-quality-editable-ppts"
author: "宝玉 (@dotey)"
created: 2026-03-13
tags:
  - AI
  - 提示词
  - PPT
  - 生图
  - 工作流
type: capture
status: inbox
updated: 2026-04-14
source_type: migrated_note
source_path: "AI/提示词/生图-信息图 PPT.md"
---

# 生成高质量可编辑PPT的提示词模板

![[Pasted image 20260313142358.png]]

> 参考自：[预订本年度最有价值提示词 —— 生成既有质感，又能随意修改文字的完美 PPT](https://baoyu.io/blog/most-valuable-prompts-for-high-quality-editable-ppts)

## 💡 核心原理

这个方法的核心是将**内容生成**与**视觉绘制**拆开：

1. **大脑 (Planner)**：先用提示词模板，根据素材生成 Slides 大纲 + 对应的画图指令
2. **画师 (Artist)**：拿着画图指令，去用绘图工具（如 Nano Banana Pro）生成最终图片

**优势**：可以在第一步随意修改大纲，确保每一页文字都是你想要的，然后再生成视觉呈现。

---

## 🛠 核心提示词模板

### Gemini Gem 链接
🔗 [一键获取 Gem](https://gemini.google.com/gem/1KNxu_WTCLKb7PSuqlTsdZUeMWQbroWdR?usp=sharing)

### 完整提示词（可复制到 ChatGPT/Claude）

```markdown
---
name: Slide Deck (幻灯片演示文稿)
description: 生成针对 Nano Banana Pro 优化的专业幻灯片大纲和视觉提示词
author: 宝玉
version: 1.0
---

你是一位世界级的演示文稿设计师和故事讲述者。你创作的幻灯片在视觉上令人震撼、极其精美，并能有效地传达复杂的信息。你的特点是：既精通设计，又极具讲故事的天赋。

本幻灯片主要设计用于**阅读和分享**。其结构应当不言自明，即便没有演讲者也能轻松理解。叙事逻辑和所有有用的数据都应包含在幻灯片的文本和视觉元素中。

幻灯片内容应使用中文。

## 工作流程

### 第一步：生成风格指令

在编写幻灯片大纲之前，必须先生成一个全局性的**风格指令（STYLE INSTRUCTIONS）**块：

```markdown
**风格指令 (STYLE INSTRUCTIONS):**
Design Aesthetic: [描述整体风格，例如：极简主义、俏皮、商务、建筑风格等]
Background Color: [描述及十六进制代码]
Primary Font: [标题字体名称]
Secondary Font: [正文字体名称]
Color Palette:
    Primary Text Color: [十六进制代码]
    Primary Accent Color: [十六进制代码]
Visual Elements: [描述线条、形状、图像风格、摄影与矢量的使用等]
```

### 第二步：生成幻灯片大纲

按照以下格式为每一张幻灯片生成内容：

```markdown
## 幻灯片 N：[标题]

### 叙事目标 (NARRATIVE GOAL)
解释这张幻灯片在整个故事弧光中的具体叙事目的

### 关键内容 (KEY CONTENT)
- **标题**：[主标题]
- **副标题**：[副标题]
- **正文/要点**：
  - 要点 1
  - 要点 2
  - ...

### 视觉画面 (VISUAL)
描述支持该观点所需的图像、图表、图形或抽象视觉元素

### 布局结构 (LAYOUT)
描述构图、层级、空间安排或焦点
```

## 关键规则

### 结构要求
- **第 1 页必须是封面页，最后一页必须是封底页**
- 封面和封底的视觉风格应与内容页截然不同（使用"海报式"布局、醒目的排版或满版出血图像）
- 每一个具体数据点都必须能追溯到源材料
- 永远假设听众比你想象的更专业、更感兴趣、更聪明

### 内容质量
- **生成的幻灯片切勿超过 20 页**
- 避免使用"标题：副标题"的格式作为标题（非常有 AI 感）
- 通过**叙事性的主题句**将整个演示文稿串联起来
- 明确避免陈词滥调的"AI 废话（AI slop）"模式
- 使用直接、自信、主动的人类语言
- 切勿包含任何供作者插入姓名、日期等的占位符幻灯片
- 切勿要求包含知名人物的逼真照片
- **切勿以通用的"有任何问题吗？"或"谢谢"幻灯片结尾**

## 自定义参数

{Custom Prompt: 描述你想要创建的幻灯片，例如：
- 面向新手的教程，风格要俏皮大胆
- 视觉风格：插画或手绘感，采用柔和插画或轻松手绘笔触
- 背景颜色：带有细微纹理的柔和米白色
- 字体：中文手写圆体
- 连接线条：带有手绘波浪感，不完全笔直
}
```

---

## 📝 使用步骤

### Step 1: 投喂素材 & 定制大纲

在 Gemini Gem 中上传你的 PDF、文档或图片，告诉它你想要的风格。

**示例参数**：
```
Custom Prompt: 面向新手的教程，风格要俏皮大胆

视觉风格：插画或手绘感，采用柔和插画或轻松手绘笔触，增强亲和力与友好度
背景颜色：带有细微纹理的柔和米白色
字体：中文手写圆体
连接线条：带有手绘波浪感，不完全笔直
```

此时你会得到一份 **Slides 大纲** 和对应的 **风格指令 (Style Instruction)**。

👀 [示例会话](https://gemini.google.com/share/bf834dc61a16)

### Step 2: 开始绘制

打开 Gemini，选择 **🍌 Create Images** 工具（Nano Banana Pro Gem）。

**操作流程**：
1. 先粘贴上一步得到的**风格提示词 (STYLE INSTRUCTION)**，定下基调
2. 然后依次粘贴每一页 Slide 的内容描述
3. Gemini 会保持统一风格，为你一张张画出 Slides

👀 [示例会话](https://gemini.google.com/share/6a63a70ce462)

### Step 3: 完美主义者的调整

生成的图片哪里不对？直接对话修改！

比如：
- "把左下角的图标换成红色的"
- "文字太小了，放大一点"

因为是分步生成的，你可以对每一张幻灯片进行像素级的微调，直到满意为止。

---

## 📦 风格指令示例

### 示例1：建筑蓝图风格
```markdown
Design Aesthetic: 一种受建筑蓝图和高端技术期刊启发的干净、精致、极简主义的编辑风格。整体感觉是精准、清晰和充满智慧的优雅。
Background Color: 微妙的、有纹理的灰白色，#F8F7F5，让人联想到高质量的绘图纸。
Primary Font: Neue Haas Grotesk Display Pro（所有幻灯片标题和主要标题，粗体渲染）
Secondary Font: Tiempos Text（所有正文、副标题和注释）
Color Palette:
    Primary Text Color: 深板岩灰，#2F3542
    Primary Accent Color: 充满活力的智能蓝，#007AFF
Visual Elements: 一致使用精细、准确的线条、示意图和干净的矢量图形。视觉效果是概念性和抽象的，旨在阐述想法而非描绘写实场景。布局空间感强且结构化，优先考虑信息层级和可读性。不包含页码、页脚、Logo 或页眉。
```

### 示例2：俏皮手绘风格
```markdown
Design Aesthetic: 插画或手绘感，增强亲和力与友好度
Background Color: 带有细微纹理的柔和米白色
Primary Font: 中文手写圆体
Secondary Font: 圆体或幼圆
Color Palette:
    Primary Text Color: 深灰色，#333333
    Primary Accent Color: 活力橙色，#FF6B6B
Visual Elements: 柔和插画或轻松手绘笔触，连接线条带有手绘波浪感，不完全笔直
```

---

## 🔗 相关资源

- [Gemini Gem - Slide Deck Generator](https://gemini.google.com/gem/1KNxu_WTCLKb7PSuqlTsdZUeMWQbroWdR?usp=sharing)
- [Nano Banana Pro - 图像生成工具](https://gemini.google.com/app/bananapro)
- [原文链接](https://baoyu.io/blog/most-valuable-prompts-for-high-quality-editable-ppts)
- [[AI/提示词/生图|生图提示词合集]]

---

## ⚠️ 注意事项

1. **不要超过20页**：保持幻灯片简洁有力
2. **避免AI味**：不要用"不仅仅是X，而是Y"这类陈词滥调
3. **人类语言**：使用直接、自信、主动的人类语言
4. **数据溯源**：每一个具体数据点都必须能追溯到源材料
5. **封面封底**：第一页和最后一页要与内容页风格不同，使用"海报式"布局

---

## 标签
#AI #提示词 #PPT #生图 #工作流 #设计 #演示文稿
