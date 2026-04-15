---
title: "Karpathy 的极简 GPT 系列：从 minGPT 到 microGPT"
tags: [AI, LLM, GPT, Transformer, 原理, Karpathy]
created: 2026-03-06
type: capture
status: inbox
updated: 2026-04-14
source_type: migrated_note
source_path: "AI/原理/Karpathy 的极简 GPT 系列：从 minGPT 到 microGPT.md"
---

# Karpathy 的极简 GPT 系列：从 minGPT 到 microGPT

- [ ] #todo 📖 待读：完整阅读这篇关于 Karpathy GPT 系列的文章，理解从 minGPT 到 microGPT 的演进历程 📅 2026-03-07

> *"I cannot simplify this any further."*
> — Andrej Karpathy，2026年2月

Andrej Karpathy 用了将近十年时间，反复把 GPT 拆解、重写、再提炼——从 300 行的 PyTorch 教学代码，到能跑 ChatGPT 的完整流水线，最终归结为 243 行零依赖的纯 Python。这不是一条线性的"进化"路径，而是围绕同一个核心问题的反复追问：**一个语言模型，最少需要什么？**

---

## 项目全景

| 项目 | 时间 | 定位 | 规模 | 链接 |
|---|---|---|---|---|
| **minGPT** | ~2020 | 教育性 PyTorch 实现 | ~300 行 | [GitHub](https://github.com/karpathy/minGPT) |
| **nanoGPT** | 2022 末 | 兼顾可读与实用 | ~600 行 | [GitHub](https://github.com/karpathy/nanoGPT) |
| **nanochat** | 2025 | 完整 ChatGPT 全栈 | ~8000 行 | [GitHub](https://github.com/karpathy/nanochat) |
| **microGPT** | 2026.02 | 返璞归真，零依赖 | 243 行 | [GitHub Gist](https://gist.github.com/karpathy/8627fe009c40f57531cb18360106ce95) |

另有来自 [growingSWE](https://growingswe.com/blog/microgpt) 的 microGPT 交互式解析，可逐步执行代码并查看每一层的中间向量。

---

## 一、minGPT（~2020）：第一次"拆箱"

### 背景

OpenAI 在 2018 年发布 GPT-1，2019 年发布 GPT-2。Karpathy 在 OpenAI 工作期间（后来转到 Tesla），写下了 minGPT——目的很单纯：把 GPT 讲清楚。

> "GPT is not a complicated model and this implementation is appropriately about 300 lines of code."

当时市面上的 GPT 实现要么过于工程化，要么缺乏解释。minGPT 想做一个「可以被人类读懂」的版本。

### 代码结构

```
mingpt/
  model.py    # Transformer 模型定义（核心，约300行）
  bpe.py      # Byte Pair Encoding，与 OpenAI GPT 对齐
  trainer.py  # 与模型无关的 PyTorch 训练样板
```

### 核心思路

模型接收一个 token 序列，输出下一个 token 的概率分布。"The majority of the complexity is just being clever with batching for efficiency."——复杂度几乎都在批处理的效率技巧上，而非模型本身。

### 为什么"半存档"了？

minGPT 被大量引用：课程、书籍、博客，各种地方都在用。这反而让 Karpathy **不敢大改**——一改就破坏了下游引用链。他想要一个更激进的重写版本：既保留可读性，又能真正跑起来大型训练任务。于是有了 nanoGPT。

---

## 二、nanoGPT（2022 末）：长出"牙齿"

> "A rewrite of minGPT that prioritizes teeth over education."

### 设计哲学转变

minGPT 是"先教育，后效率"；nanoGPT 是"**既简洁，也要能跑出真实结果**"。

| 维度 | minGPT | nanoGPT |
|---|---|---|
| 首要目标 | 可读性、教育性 | 效率 + 可复现工业基准 |
| PyTorch 版本 | 标准 | PyTorch 2.0（`torch.compile`） |
| Flash Attention | ✗ | ✓（PyTorch ≥ 2.0 自动启用） |
| GPT-2 完整复现 | 部分 | ✓（124M on OpenWebText） |
| 文件数 | 3 | 2（`model.py` + `train.py`，各约300行） |

### 关键实现细节

**GPT-2 架构**（`model.py`，约 300 行）：
- Decoder-only Transformer，12 层，d_model = 768
- Pre-LayerNorm（LayerNorm 移到每个子模块输入端）
- 残差路径初始化：权重按 `1/√N` 缩放（N = 残差层数）

**效率优化**：
- 词汇量从 50257 填充到 **50304**（最近的 64 的倍数），矩阵运算更高效
- Flash Attention：当 PyTorch ≥ 2.0 时自动启用
- `torch.compile` 加速

**训练规模**：
- 单节点 8×A100（40GB），约 4 天，完整复现 GPT-2 (124M)
- 也可加载 OpenAI 预训练权重做微调

---

## 三、nanochat（2025）：完整的 ChatGPT 全栈

> "Unlike nanoGPT which only covered pretraining, nanochat is a minimal, from scratch, full-stack training/inference pipeline of a simple ChatGPT clone."

### 解决 nanoGPT 的局限

nanoGPT 只做了预训练（pretraining）。但一个真正的 ChatGPT 还需要：SFT（监督微调）、对齐、评估、推理、对话界面。nanochat 把这些都打包进来了。

### 功能覆盖

- Tokenization（训练 tokenizer，使用少量 Rust）
- Pretraining（预训练）
- Finetuning（微调/SFT）
- Evaluation（评估）
- Inference（推理，支持 fp16）
- Chat UI（网页对话界面）

### 单一复杂度旋钮

nanochat 的核心设计理念：**只暴露一个超参数 `--depth`**（Transformer 层数），其他所有超参数（宽度、注意力头数、学习率、训练步数、weight decay 等）都**自动计算**到最优值。

```bash
# 示例：训练 12 层的模型（相当于 GPT-1 规模）
python train.py --depth 12
```

这让 nanochat 能够系统地研究 **scaling laws**：depth 是唯一的"计算预算旋钮"，depth 越大，模型越强，结果单调递增，为"大规模训练外推"提供了可靠的置信度。

### 成本参考

| 计算预算 | 训练时间 | 模型水平 |
|---|---|---|
| ~$15–48（spot 实例） | ~2 小时（8×H100） | GPT-2 级别 |
| ~$73（2026年1月更新后） | ~3 小时 | GPT-2 级别，含最新优化 |
| ~$1000 | ~41.6 小时 | 能做简单数学/代码/选择题 |

> 对比：GPT-2 在 2019 年训练成本约 $43,000。

### 代码规模

约 8000 行（Python 为主，少量 Rust 用于 tokenizer 训练）。这已经不是"nano"级别了，而是一个真正的工程项目——但仍然保持 **maximally forkable**：设计目标是让你 fork 后就能直接改。

### 未来

nanochat 将成为 Karpathy 在 Eureka Labs 开设的 **LLM101n** 课程的压轴项目——一门本科水平的 LLM 构建课程。

---

## 四、microGPT（2026.02）：返璞归真

> "Train and inference GPT in 243 lines of pure, dependency-free Python. This is the full algorithmic content of what is needed. Everything else is just for efficiency. I cannot simplify this any further."
> — Karpathy，2026年2月（X / Twitter）

Karpathy 称其为一个 **"art project"**（艺术项目）。发布于 2026 年 2 月 11 日，以 GitHub Gist 的形式公开。

### 零依赖

```python
import os, math, random, argparse
# 就这四个。没有 PyTorch，没有 NumPy，没有任何 ML 库。
```

### 五个核心组件

microGPT 内含一个完整语言模型所需的全部算法内容，分为五个部分：

#### 1. 数据集（Dataset）
32,000 个人名，每个名字是一个"文档"。整个训练集就是这些名字。

#### 2. 字符级 Tokenizer
- 没有 BPE，没有 SentencePiece
- 26 个字母，各对应一个整数 ID
- 加一个特殊 token：`<BOS>`（Beginning of Sequence）
- 词汇表大小：**27**
- "The integer token values themselves have no meaning — token 4 isn't 'more' than token 2."

#### 3. 手写 Autograd 引擎（Value 类）

```python
class Value:
    # 包裹一个浮点标量
    # 追踪正向计算图
    # 反向传播时走链式法则
```

每一个模型中的权重、偏置、中间激活值——都是一个 `Value` 对象。原理与 PyTorch 的 autograd 完全相同，只是没有 C++ 后端、没有 CUDA kernel、没有那 200 万行代码。

#### 4. GPT-2-like 架构（仅 4,192 个参数）

`gpt()` 函数实现单步 token 前向传播，包含：

| 模块 | microGPT 做法 | 标准 GPT-2 |
|---|---|---|
| Normalization | **RMSNorm** | LayerNorm |
| Biases | **无** | 有 |
| 激活函数 | **ReLU²（Squared ReLU）** | GeLU |
| KV Cache | ✓ | ✓ |
| 残差连接 | ✓ | ✓ |
| 位置编码 | Token + Position Embedding | 同 |

> "microGPT has just 4,192 parameters, while GPT-4 class models have hundreds of billions. Overall it's a very similar-looking Transformer neural network — just much wider and much deeper."

#### 5. Adam 优化器

手写 Adam，维护每个参数的：
- **一阶矩**（梯度的指数移动平均，momentum）
- **二阶矩**（梯度平方的指数移动平均，adaptive learning rate）

学习率：从 0.01 线性衰减到 0，训练 1000 步。

### 训练循环（概述）

```
重复 1000 次：
  1. 随机选一个名字
  2. 字符级 tokenize
  3. 前向传播（每个位置输出 logit）
  4. 计算交叉熵损失，取均值
  5. 反向传播，获取所有参数的梯度
  6. Adam 更新参数
```

### 为什么慢到不实用？

因为每个浮点操作都是 Python 对象级别的。没有向量化，没有 GPU。Rust 移植版比 CPython 快 **440 倍**；C++ 移植版快 **219 倍**。但这不是重点——重点是那 243 行代码可以被任何人读懂。

### 这一切从何而来？

microGPT 是 Karpathy "Zero to Hero" 教学项目线的终点（也是起点）：

```
micrograd（标量 autograd）
  → makemore（字符级语言模型）
    → minGPT（完整 GPT PyTorch 实现）
      → nanoGPT（实用化重写）
        → nanochat（全栈 ChatGPT）
          → microGPT（蒸馏为最小本质）
```

---

## 五、growingSWE 的交互式解析

链接：[MicroGPT Explained Interactively | growingSWE](https://growingswe.com/blog/microgpt)

这篇文章将 microGPT 的 `gpt()` 函数做成了**可交互的逐步执行体验**：每一行代码执行后，都可以看到中间向量的实际数值。

文章重点解析的几个概念：

**残差连接**
> "The residual connections (the 'Add' steps) are load-bearing. Without them, gradients would shrink to near-zero by the time they reach the early layers."

残差连接给梯度提供了一条"高速公路"，这是深层网络能被训练的根本原因。

**Tokenization 的本质**
token 本身没有大小关系，每个 token 只是一个离散符号——就像给每个字母分配一种颜色。生产级 tokenizer（如 tiktoken）用 BPE 来提高效率，词汇量约 100,000，但原理完全一样。

**Adam 的直觉**
- 梯度方向一致的参数 → 步长更大
- 梯度反复震荡的参数 → 步长更小
这比朴素的梯度下降聪明很多，尤其在 batch size 极小时（microGPT 每次只看一个名字）。

---

## 核心洞察：为什么反复极简化？

Karpathy 所有这些项目背后有一个一致的信念：**理解比性能更重要，至少在学习阶段如此。**

一旦你能在脑子里完整运行一个 GPT——知道每一个数字从哪来、到哪去——那些"工业级"系统就不再神秘。PyTorch 的 autograd 只是一个更快的 `Value` 类；Flash Attention 只是把 softmax+matmul 融合了；GPT-4 只是 microGPT 扩大了几十亿倍。

> "The math is identical, just corresponds to many scalars processed in parallel."

---

## 参考资料

- [growingSWE: MicroGPT Explained Interactively](https://growingswe.com/blog/microgpt)
- [karpathy/minGPT on GitHub](https://github.com/karpathy/minGPT)
- [karpathy/nanoGPT on GitHub](https://github.com/karpathy/nanoGPT)
- [karpathy/nanochat on GitHub](https://github.com/karpathy/nanochat)
- [microgpt.py GitHub Gist](https://gist.github.com/karpathy/8627fe009c40f57531cb18360106ce95)
- [Karpathy 博客：microgpt](http://karpathy.github.io/2026/02/12/microgpt/)
- [Neural Networks: Zero to Hero](https://karpathy.ai/zero-to-hero.html)
