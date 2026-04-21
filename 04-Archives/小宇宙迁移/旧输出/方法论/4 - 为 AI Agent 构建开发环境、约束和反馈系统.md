---
title: "为 AI Agent 构建开发环境、约束和反馈系统"
tags: [AI, Agent, 架构, 方法论, 工程范式, Harness Engineering]
source: "https://openai.com/index/harness-engineering/"
author: "OpenAI Engineering"
created: 2026-03-09
weight: high
series: AI时代工程师方法论
series_order: 4
---
系统设计的本质是：**从模糊的需求到可运行的系统**。

# 为 AI Agent 构建开发环境、约束和反馈系统

> **核心命题**：当 AI agent 可以写代码时，工程师的工作从"写代码"转为"构建让 agent 高效工作的环境"。
> 未来软件工程的竞争力在 **harness**，不是代码。

> 原文：[Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/) — OpenAI Engineering Blog

---

## 为什么这篇文章值得单独拆解

这不是一篇普通的技术博客。它通过一个**真实实验**，揭示了一个正在成型的新范式——

OpenAI 团队用 Codex agent，在 5 个月内，**从空仓库开始构建了一个完整产品**。约 100 万行代码，1500 个 PR，3 个工程师驱动 agent，产品真实上线并有用户使用。

**人类没有手写任何一行代码。**

这不是 demo，不是 POC。这是一个工程实践级别的验证：

> Agent 写代码已经不是"能不能"的问题，而是"怎么让它稳定地写"的问题。

答案就是 **Harness Engineering**。

---

## 一、什么是 Harness Engineering

Harness = 马具、安全带。
Harness Engineering = **为 AI agent 构建开发环境、约束和反馈系统，让 agent 可以稳定写代码。**

两种开发范式的对比：

| 维度 | 传统软件开发 | Harness Engineering |
|------|-------------|-------------------|
| 核心动作 | 工程师写代码 | 工程师设计环境 / 规则 / 工具 |
| 执行者 | 人 | AI agent |
| 测试 | 人写测试、手动跑 | Agent 自己跑测试、自己修 |
| 提交 | 人创建 PR | Agent 创建 PR |
| 修复 bug | 人改代码 | 人改环境，让 agent 下次自己写对 |

一句话总结分工：

```
Humans steer.
Agents execute.
```

人类负责**意图与系统设计**。Agent 负责**实现与执行**。

这和 [[AI时代的设计工作流]] 的核心思路完全一致：**人定规则 → AI 执行 → 人把关**。只不过设计工作流约束的是 UI 审美，Harness Engineering 约束的是整个软件开发过程。

---

## 二、实验数据

| 指标 | 数据 |
|------|------|
| 起点 | 空仓库 |
| 时间 | 5 个月 |
| 代码量 | ≈100 万行 |
| PR 数量 | 1500 个 |
| 工程师 | 3 人 |
| 人均 PR/天 | 3.5 |
| 产品状态 | 真实上线，有用户使用 |

最重要的规则：

> **人类不直接写代码。**

当 agent 出问题时，人类也不修代码，而是：

1. **改环境** — 让 agent 有更好的执行条件
2. **改工具** — 给 agent 更强的能力
3. **改规则** — 让 agent 知道边界在哪里
4. **改文档** — 让 agent 能读懂意图

目标只有一个：**让 agent 下次自己写对。**

---

## 三、工程师角色的根本转变

传统工程师的一天：

```
读需求 → 写代码 → 调试 → 提交 → Code Review
```

Agent 时代工程师的一天：

```
设计系统结构 → 拆解任务 → 提供上下文 → 构建自动验证 → 设计反馈循环
```

工程师更像：**AI 工程系统架构师**。

| 传统能力 | Agent 时代对应能力 |
|---------|------------------|
| 写算法 | 设计架构约束 |
| 调 CSS | 定义主题变量系统 |
| 写测试 | 设计自动验证管线 |
| Code Review | 构建 lint / CI 自动化规则 |
| 读文档 | **写文档给 agent 读** |

最后一条最反直觉——过去文档是写给人看的，现在文档是写给 agent 看的。

---

## 四、最核心原则：Agent Legibility

文章提出一个关键概念：

> **Agent Legibility** — 系统必须让 AI 能理解。

Agent 只能看到 repo 里的东西：

| ✅ Agent 能看到的 | ❌ Agent 看不到的 |
|-----------------|-----------------|
| 代码 | Slack 消息 |
| README | Notion 文档 |
| Schema | 人脑中的知识 |
| 文档 | 口头约定 |
| 脚本 | 白板上的架构图 |
| 测试 | "大家都知道"的潜规则 |

所以团队的做法是——**把所有知识都放进 repo**：

```
ARCHITECTURE.md   — 系统架构说明
SCHEMA.md         — 数据模型定义
RULES.md          — 开发规则约束
AGENTS.md         — Agent 行为指引
tests/            — 自动化测试
scripts/          — 构建与部署脚本
```

> 每一条口头约定，如果没有变成 repo 里的文件，agent 就不知道。
> 每一个"大家都知道"的惯例，如果没有写成规则，agent 就会违反。

**我们自己的实践就是证据。** 这个知识库的 `CLAUDE.md` 和 `.agents/skills/` 就是 Agent Legibility 的具体实现——把意图、规则、约束全部写成 agent 能读取的文件。[[AI时代的设计工作流]] 中用 `AGENTS.md` 约束 AI 的 UI 行为，也是完全一样的逻辑。

---

## 五、严格的架构约束

Agent 写代码速度极快。但如果没有约束，系统会迅速陷入混乱。

他们设计了**强架构规则**——每个业务模块固定层级：

```
Types          ← 类型定义
  ↓
Config         ← 配置
  ↓
Repo           ← 数据访问
  ↓
Service        ← 业务逻辑
  ↓
Runtime        ← 运行时
  ↓
UI             ← 界面
```

**铁律**：
- ✅ 只能向前依赖（上层依赖下层）
- ❌ 禁止跨层调用（UI 不能直接访问 Repo）
- ❌ 禁止反向依赖（Types 不能引用 Service）

关键：所有规则都**通过自动化 enforcement**——不是靠人记住，而是靠 lint 自动检查。Agent 违反规则时 CI 直接报错，agent 读到报错后自己修。

这和 [[3 - 产品设计的最高效率——每个功能只写一次]] 的"引擎化核心思路"异曲同工：**把稳定的执行框架沉淀下来，业务侧只写插件。**

---

## 六、给 Agent 可观测能力

为了让 agent 可以**自己 debug**，他们让 agent 能访问：

### UI 可观测

通过 Chrome DevTools Protocol：
- DOM snapshot — 页面结构
- Screenshot — 视觉截图
- 页面导航 — 模拟用户操作

### 服务可观测

Agent 能查询：
- **Logs** — 服务日志
- **Metrics** — 性能指标
- **Traces** — 请求链路

例如：agent 可以自己验证 `ensure service startup < 800ms`。

**意义**：Agent 不再是"写完就扔"——它能看到代码运行后的真实效果，形成完整的**写码 → 验证 → 修复**闭环。

---

## 七、Agent 的完整开发流程

最终系统可以做到——输入一个任务 prompt：

```
Fix login bug
```

Agent 自动完成：

```
1.  读取 repo，理解代码结构
2.  复现 bug
3.  录屏记录复现过程
4.  写修复代码
5.  跑测试
6.  再录验证视频
7.  创建 PR
8.  回复 Code Review 意见
9.  修复 CI 失败
10. Merge
```

**只有需要判断时才找人类。**

人类变成了"审批者"和"方向决策者"，不再是"执行者"。

---

## 八、代码熵：AI 写代码的新问题

Agent 会**复制已有模式**。

如果 repo 里有坏代码：

```
坏 pattern → 被 agent 学习 → 复制到新代码 → 扩散到整个 repo
```

这就是 **代码熵**。Agent 不会主动判断"这段代码是不是过时的"——它只会模仿。

解决办法是建立**持续清理机制**：

1. 定义 **golden rules**（黄金规则）— 什么是好代码的标准
2. Agent **定期扫描** repo — 找出违反规则的代码
3. 自动创建 **refactor PR** — 持续修复

这本质上是代码层面的**垃圾回收（Garbage Collection）**——技术债持续清理，而不是积累到爆炸。

> 对应到 [[2 - 囤积你懂得做的事]] 的逻辑：武器库需要**定期清理和更新**，过时的方案如果不移除，反而会误导 AI 产出错误的结果。囤积的不只是"加法"，还需要"减法"。

---

## 九、Merge 策略也改变了

| 维度 | 传统模式 | Agent 模式 |
|------|---------|-----------|
| Code Review | 严格人工审查 | 自动化检查 + 人审关键决策 |
| PR 生命周期 | 长（天级别） | 短（小时级别） |
| CI | 必须全绿才 merge | 快速 merge，错误快速修复 |
| 吞吐量 | 低 | 极高 |

原因很简单：

> Agent 的吞吐量极高。**等待成本高于修复成本。**

传统模式下一个 PR 等两天 review 是常态。Agent 模式下同样两天可以产出 7 个 PR。如果花两天等 review 而不是快速 merge + 快速修复，效率损失巨大。

---

## 十、企业级 Agent Runtime 架构

上面讲的是**理念**。下面进入**架构**——要让 Harness Engineering 规模化落地，背后需要一套完整的 Agent Runtime 系统。

### 五层架构总览

```
┌─────────────────────────────────────────┐
│              User / UI                  │  ← 用户交互层
├─────────────────────────────────────────┤
│           API Gateway                   │  ← 接入层
│     Agent Router  |  Session Manager    │
├─────────────────────────────────────────┤
│          Agent Runtime Core             │  ← 核心运行时
│  Reasoning Engine | Execution Loop      │
│  Context Manager  | Memory System       │
│  Reflection / Self-Critique             │
├─────────────────────────────────────────┤
│        Agent Harness / Environment      │  ← 执行环境层
│  Repository | Build | Test | Browser    │
│  Observability | Tool Registry          │
│  Sandbox | Policy Engine                │
├─────────────────────────────────────────┤
│           Infrastructure                │  ← 基础设施层
│  LLM Provider | Vector DB | Queue       │
│  Storage | Compute (K8s)                │
└─────────────────────────────────────────┘
```

---

### 第一层：User / UI — 用户交互层

用户发起请求的入口。

可以是：
- Web 界面（对话式）
- IDE 插件（Claude Code、Cursor、GitHub Copilot）
- CLI 工具
- API 调用

---

### 第二层：API Gateway — 接入层

三个核心组件：

**① API Gateway**

| 功能 | 说明 |
|------|------|
| 身份认证 | 验证请求合法性 |
| 限流 | 防止滥用 |
| 请求日志 | 审计追踪 |
| 路由 | 把请求分发到正确的 agent |

企业系统还需要：quota 管理、多租户隔离。

**② Agent Router**

根据任务类型选择 agent：

```
用户提问     → QA Agent
写代码       → Coding Agent
数据分析     → Analysis Agent
修复 bug     → Debug Agent
```

路由方式：
- 规则路由 — 简单匹配关键词
- LLM 路由 — 用小模型判断意图
- Embedding 路由 — 语义相似度匹配

**③ Session Manager**

管理用户会话，保存：
- 对话历史
- Agent 状态
- 工具调用结果

---

### 第三层：Agent Runtime Core — 核心运行时

这是整个系统最核心的部分。

**① Reasoning Engine — 推理引擎**

负责 LLM 推理、任务规划、思考步骤。

常见推理模式：

| 模式 | 特点 |
|------|------|
| ReAct | 推理 + 行动交替进行 |
| Plan-and-Execute | 先规划再执行 |
| Self-reflection | 执行后自我反思 |
| Tree-of-Thought | 多路径探索 |

**② Execution Loop — 执行循环**

Agent 的核心循环：

```
推理（Reason）
  ↓
选择工具（Choose Tool）
  ↓
执行工具（Execute）
  ↓
观察结果（Observe）
  ↓
重复 / 结束
```

每一步都有：`step_id`、`tool_calls`、`observations`，可追溯。

**③ Context Manager — 上下文管理**

控制 LLM 的上下文窗口：

| 功能 | 说明 |
|------|------|
| Prompt Assembly | 组装系统提示 + 用户指令 + 工具结果 |
| Context Compression | 超长上下文压缩 |
| History Trimming | 裁剪历史对话 |
| Memory Injection | 注入相关记忆 |

核心挑战是 **context window overflow**——上下文太长，模型效果急剧下降。

解决方法：
- Summary memory — 摘要压缩
- Retrieval memory — 按需检索
- Sliding window — 滑动窗口

**④ Memory System — 记忆系统**

| 类型 | 存储内容 | 技术 |
|------|---------|------|
| 短期记忆 | 当前任务上下文 | In-memory |
| 长期记忆 | 用户历史、偏好 | Postgres / Redis |
| 知识记忆 | 文档、代码语义 | Vector DB |

**⑤ Reflection / Self-Critique — 自我反思**

Agent 评估自己的结果：

```
代码写完了 → 测试通过了吗？
  ↓ 没通过
读取报错 → 分析原因 → 重写 → 再测
```

这是 Agent 区别于传统自动化脚本的关键——它能**自我纠错**。

---

### 第四层：Agent Harness / Environment — 执行环境层

Harness 是 agent 操作的世界。这一层是 Harness Engineering 的核心实现。

**① Tool Registry — 工具注册中心**

所有工具在这里注册：

```
工具名: search_code
Schema: { query: string, path?: string }
描述: "在代码库中搜索关键词"
权限: read-only
```

常见工具类型：API、Shell、Browser、Database。

**② Repository Interface — 代码仓库接口**

Agent 操作代码的能力：
- 搜索文件
- 读取文件
- 编辑文件
- 创建 PR
- Git 操作

**③ Build System — 构建系统**

Agent 写完代码后需要验证：编译、安装依赖、构建产物。

**④ Test Runner — 测试运行器**

Agent 必须能跑测试——单元测试、集成测试、端到端测试。
测试结果直接成为 agent 的 observation，驱动下一步决策。

**⑤ Browser Environment — 浏览器环境**

UI Agent 的核心能力：打开页面、点击、输入、截图。
技术：Playwright、Chrome DevTools Protocol。

**⑥ Sandbox — 沙箱**

防止 agent 做危险操作：
- ❌ 删除系统文件
- ❌ 访问敏感数据
- ❌ 执行 `rm -rf /`

实现方式：容器隔离、受限 shell、虚拟机。

**⑦ Observability System — 可观测系统**

Agent debugging 的数据来源：Logs、Metrics、Traces。

**⑧ Policy Engine — 策略引擎**

企业级治理规则：
- 禁止访问生产数据库
- 限制外部 API 调用
- Token 用量上限
- 审计日志记录

---

### 第五层：Infrastructure — 基础设施层

底层支撑：

| 组件 | 常见技术 |
|------|---------|
| LLM Provider | OpenAI / Anthropic / 本地部署 |
| Vector Database | Pinecone / Qdrant / Weaviate |
| 消息队列 | Kafka / Redis Streams |
| 存储 | S3 / 本地文件系统 |
| 计算 | Kubernetes / Docker |
| 数据库 | Postgres / Redis |

---

## 十一、真实产品对比

这个架构不是理论推演——很多公司正在构建类似系统：

### Runtime 类

| 产品 | 定位 | 特点 |
|------|------|------|
| LangGraph | Agent 编排框架 | DAG workflow，状态机 |
| Google ADK | Agent 开发工具包 | Google 生态集成 |
| OpenAI Agents SDK | Agent 运行时 | 原生支持 Codex |
| CrewAI | 多 Agent 协作 | 角色分工，团队模式 |

### Coding Runtime 类

| 产品 | 定位 | 特点 |
|------|------|------|
| Claude Code | AI 编码助手 | Terminal 原生，全工具链 |
| Devin | 自主编码 Agent | 全流程自动化 |
| SWE-agent | 开源编码 Agent | Princeton 研究项目 |
| Cursor / Windsurf | AI IDE | 编辑器内嵌 Agent |

### 平台型

| 产品 | 定位 |
|------|------|
| Dust | 企业 AI Agent 平台 |
| Adept | 通用 Agent 平台 |

**趋势**：Agent Runtime 正在变成 AI 应用的操作系统。

---

## 十二、能力地图：从理念到行动

把上面所有内容串起来，形成一张完整的**能力地图**：

```
┌─────────────────────────────────────────────────────────┐
│                    能力地图                               │
│              Harness Engineering                        │
├──────────────┬──────────────┬───────────────────────────┤
│   人的能力    │  环境的能力   │  Agent 的能力             │
├──────────────┼──────────────┼───────────────────────────┤
│ 架构设计      │ 代码仓库      │ 读代码                   │
│ 任务拆解      │ 构建系统      │ 写代码                   │
│ 意图表达      │ 测试系统      │ 跑测试                   │
│ 规则制定      │ CI/CD        │ 修 bug                   │
│ 审美判断      │ 约束规则      │ 创建 PR                  │
│ 质量把关      │ 可观测性      │ 回复 review              │
│ 方向决策      │ 沙箱隔离      │ 自我反思                 │
│ 知识沉淀      │ 文档系统      │ 复现问题                 │
│              │ 反馈循环      │ 录屏验证                 │
├──────────────┴──────────────┴───────────────────────────┤
│                                                         │
│  Humans steer.  Environment constrains.  Agents execute.│
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 对应到我们的实践

| Harness 概念 | 我们已有的实践 | 对应笔记 |
|-------------|-------------|---------|
| Agent Legibility | `CLAUDE.md` + `AGENTS.md` 让 AI 读懂项目 | 本知识库根目录 |
| 架构约束 | 主题变量系统约束 AI 写 UI | [[AI时代的设计工作流]] |
| 工具链 | Skills 库提供专业能力 | [[Skills 库]] |
| 知识沉淀 | 武器库 + 原子笔记 | [[2 - 囤积你懂得做的事]] |
| 平台思维 | 引擎化、配置化、可复用 | [[3 - 产品设计的最高效率——每个功能只写一次]] |
| 人的判断力 | 分清常量与变量，聚焦认知差 | [[1 - AI时代的常量与变量]] |

---

## 十三、核心结论

### 软件工程的重心正在迁移

```
过去：写代码
      ↓
现在：设计 agent 的工作环境
```

### 关键能力变成

1. **架构设计** — 让系统结构清晰，agent 能理解
2. **自动验证** — 让 agent 能自己知道"对不对"
3. **工具链** — 给 agent 足够的能力
4. **反馈循环** — 让 agent 能自我纠错
5. **约束系统** — 让 agent 在边界内发挥

### 本质

Harness Engineering 本质上是在回答一个问题：

> **如何把人脑中的隐性知识，变成 agent 可以读取的显性规则？**

这和 [[2 - 囤积你懂得做的事]] 的核心洞见高度一致——

```
知识囤积 × AI执行力 × 你的判断力 = 真正的能力杠杆
```

只不过 Harness Engineering 把"知识囤积"具体化为：
- 架构文档
- 约束规则
- 测试用例
- 可观测系统
- 反馈管线

而"你的判断力"具体化为：
- 系统设计决策
- 任务拆解策略
- 质量标准制定
- 方向把控

**未来不是"会不会写代码"决定你的价值，而是"能不能构建让 agent 高效工作的系统"决定你的价值。**

---

## 十四、为什么平台工程师需求会暴涨

这篇文章揭示了一个趋势：

> 工程师数量可能减少，但**平台工程师**需求暴涨。

因为 Harness Engineering 不是"prompt + LLM"那么简单。它需要的是一整套系统：

```
Agent Runtime
  +
Repo Design（仓库设计）
  +
Architecture Rules（架构规则）
  +
Observability（可观测性）
  +
Auto Evaluation（自动评估）
  +
Feedback Loop（反馈循环）
```

合在一起就是——**AI 软件工程平台**。

而构建这个平台的人，就是新时代的平台工程师。

他们不写业务代码——他们**建造让 agent 写业务代码的世界**。

---

## 关联思考

- [[0 - AI时代工程师方法论——系列总览]] — 本文是系列第 4 篇（执行环境），查看系列全貌
- [[5 - 从需求到上线——完整的系统设计方法论]] — 系列篇 5：Harness Engineering 是完整系统设计链路在 AI 时代的关键补充，设计文档不再只是给人看的资料，而是 Agent 的执行依据
- [[6 - 从 0 启动 AI 协作项目——实践手册]] — 系列篇 6：Harness Engineering 理论的具体落地步骤
- [[2 - 囤积你懂得做的事]] — 系列篇 2："知识囤积 × AI执行力 × 判断力"，Harness Engineering 把这个公式工程化了
- [[3 - 产品设计的最高效率——每个功能只写一次]] — 系列篇 3："引擎化、配置驱动"的设计哲学延伸到 Agent 系统设计
- [[1 - AI时代的常量与变量]] — 系列篇 1：构建 Harness 的能力——架构设计、约束系统、反馈循环——是 AI 永远替代不了的核心变量
- [[AI/武器库/Skills 库]] — Skills 就是 Tool Registry 的个人版本：把专业能力注册成 Agent 可调用的模块
