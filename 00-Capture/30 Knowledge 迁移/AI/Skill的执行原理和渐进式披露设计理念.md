---
created: 2026-03-31
tags:
  - AI
  - Agent
  - Skill
  - 设计理念
  - 渐进式披露
reference: https://chatgpt.com/share/69cb7c8c-e29c-83e8-925b-122dcc415cde
type: capture
status: inbox
updated: 2026-04-14
source_type: migrated_note
source_path: "AI/Skill的执行原理和渐进式披露设计理念.md"
---

# Skill的执行原理和渐进式披露设计理念

昨天面试字节面试官问我 Skill 的执行机制，不是很熟悉，了解了一下，发现这种思想还是很值得学习的。

使用参考 [[Skills 最佳实践指南索引]], 有完整的使用指南。这篇是关于 Skill 是怎么生效的。

**相关笔记**：[[Sub-agent 机制与使用场景]]

我的回答只涉及到最本质的核心，但是缺少具体的细节。

我的回答：

> 首先，Claude code要能够发现注册的 skills，所有有一个注册的地方，注册之后，要有地方能够获取到，获取到之后注入到 prompt 里。简而言之，注册，发现，调用。

但是我今天看了具体的细节之后，我发现我的原理理解，以及工程优化都缺失，回答不及格。

## Overview

```
用户输入
   ↓
LLM（看到 available_skills）
   ↓
生成 tool_call: skill(sql_optimizer)
   ↓
runtime 拦截
   ↓
读取 SKILL.md
   ↓
注入 system prompt
   ↓
LLM 再推理（已经具备 skill 能力）
   ↓
调用普通 tools（db_query / explain）
   ↓
返回结果
```

## 一句话回答：本质是什么

Skill 本质就是把 可复用的能力 抽象为 标准化单元，让大模型能够 **按需发现，加载，执行**。

先一句话定义：

 > skill = 一组可复用的能力封装 = prompt + tools + workflow + knowledge
 
 里面包括：
 
SKILL.md, scripts, references, assets.

SKILL.md 里包含 元数据 和 正文。

## 核心实现流程

### **注册（Registry）**

这是最核心的。本质就是维护一个 **skill registry**。类似：

```ts
interface Skill {
  name: string
  description: string
  tools: string[]
  contentPath?: string
}

class SkillRegistry {
  private skills = new Map<string, Skill>()

  register(skill: Skill) {
    this.skills.set(skill.name, skill)
  }

  get(name: string) {
    return this.skills.get(name)
  }

  list() {
    return [...this.skills.values()]
  }
}
```

#### 常见注册方式
##### **方式1：代码注册**

```ts
registry.register({
  name: "sql_optimizer",
  description: "optimize slow sql",
  tools: ["db_query"]
})
```

##### **方式2：文件扫描注册**

启动时扫描：
```ts
.skills/
   sql/
      SKILL.md
   rag/
      SKILL.md
```

启动时：
```ts
fs.readdir(".skills")
```

读取 metadata：
```ts
---
name: sql_optimizer
description: optimize sql
---
```

然后注册进 registry。

这个模式特别像插件系统。

##### **方式3：远程注册（MCP / Tool Server）**

这是现在最先进的。从远程拉取，然后动态挂载。

### **读取（Discover / Retrieve）** - 渐进式读取

skill 不可能全部塞上下文。否则 token 爆炸。所以一定是：

> 先轻量 discover
> 再按需 load

也就是，Agent 启动时，一般只读摘要，然后大模型自己决定要调用哪一个。

那么大模型怎么调用呢？这就涉及到后面要讲的 Skill 其实也是 tool，通过 tool_call 获取 Skill 内容。

![[Pasted image 20260331151133.png]]
##### 语义召回

如果Skill 很多，几百个, 那么可以通过向量召回对应的 Skill 摘要。但是这个 Claude code 应该没有应用。

Claude code 的 MCP 有类似的实现，当 MCP 工具描述总大小超过上下文的 10% 时，Claude Code 会自动切换到动态搜索模式，只按需加载需要的工具定义（而不是一次性 preload 所有）。

细节可以查看：[# How Claude Code Actually Works: A Systems-Level Deep Dive](https://karaxai.com/posts/how-claude-code-works-systems-deep-dive/)


## 执行，怎么放到上下文 - 三层渐进式加载模型

本质都是 Context Engineering，所以怎么设置到上下文是一门学问。

###  **第一层：只放 skill summary**

先放一个摘要, 类似这样：
```ts
<Available skills>

1. sql_optimizer

2. crm_sales_analysis

3. pdf_rag_parser
   
</Available skills>
```

### **Level 2：技能正文（命中后加载）**

用户问：

```ts
帮我优化这条 SQL
```

然后模型就知道要调用 sql_optimizer 这个 skill，就会加载这个 Skill 的 SKILL.md 到上下文里。

怎么加载的呢？看看OpenCode的实现，直接提供一个 **内置工具：skill**

```ts
skill("sql_optimizer")
```

这个工具的功能就是：

```ts
function skill(name) {
  const content = loadSkillFile(name)   // 读取 SKILL.md
  injectToContext(content)             // 注入上下文
}
```

所以当模型觉得应该调用的时候：

```ts
tool_call: skill("sql_optimizer")
```

### **runtime 拦截 tool call**

然后 agent runtime 可以拦截 **tool call**，然后内置实现。

### **动态注入上下文**

```ts
messages.push({
  role: "system",
  content: skillContent
})
```

就注入到上下文里了。


### # **关键的工程优化**

开源实现里有一个细节很重要：

> ❗ skill 不会重复注入

```ts
if (!context.has(skill)) {
  inject(skill)
}
```

否则会：

上下文爆炸。

## 总结

和懒加载的机制很像。Harness engineering 和 context engineering 才是决定模型能力的关键，已经不是基础模型了。
