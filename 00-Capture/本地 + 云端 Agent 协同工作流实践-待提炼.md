---
tags:
  - inbox
  - ai
  - agent
type: capture
status: inbox
created: 2026-04-14
updated: 2026-04-14
---

# 本地 + 云端 Agent 协同工作流实践-待提炼

## 原始记录

多 Agent 编排策略：本地 + 云端协同工作流指南。

云端 Agent：云服务器 Docker 环境，适合 24/7 持续运行、爬虫、定时任务和长期存储。

本地 Agent：本地电脑上的轻量 Agent，或 Cursor/Codex 自身，适合低延迟、隐私和本地文件操作。

补充修正：

- 云服务器不一定要用 Docker
- 对于云服务器，直接按 OpenClaw 官方安装文档安装，也可以获得更直接的系统访问与数据获取能力
- Docker 更像一种部署方式，而不是这套协同工作流的前提条件

任务分配的初步原则：

- 需要 24/7、爬虫、定时、长期存储，用 cloud agent
- 需要本地文件、隐私、实时硬件，用 local agent

一个当前实践方案：

- 先在云服务器安装 OpenClaw，Docker 只是可选实现方式
- 本地通过 `claw.sh` 或 alias 调用服务器上的 OpenClaw
- 在 Cursor/Codex 中把 `claw` 封装成自定义 Tool 或 sub-agent
- 在服务器端再封装成 Skill，让云端 Agent 可以自主执行定时任务或爬虫

当前认为的组合：

- `claw.sh`（本地 alias）
- Cursor/Codex 自定义 Tool（sub-agent）
- 服务器端自定义 Skill

这样做的意图：

- 本地 Codex/Cursor 可以直接下达“让服务器去爬取、监控、浏览器执行”的任务
- 服务器端 OpenClaw 自己也能通过 Skill 执行定时爬虫
- 把 2 核 4G 云服务器主要用作持续执行层，避免把爬虫压在本地机器上

## 当前实现细节

### 1. 本地 `claw` 封装

在本地新建 `~/bin/claw`：

```bash
#!/bin/bash
# claw.sh - 本地调用服务器 OpenClaw 的万能封装
ssh 你的用户名@你的服务器IP "cd ~/openclaw && docker compose exec -i openclaw-cli openclaw agent --prompt \"\$*\" --no-stream"
```

然后：

```bash
chmod +x ~/bin/claw
export PATH="$HOME/bin:$PATH"
source ~/.zshrc
```

测试示例：

```bash
claw "用浏览器去 591 抓台北市最近 5 条新上架的房价，总结后存到 ~/workspace/房价.csv"
```

### 2. 作为 Codex / Cursor / WorkBuddy 的 sub-agent

在 Cursor 设置中新增 Tool：

- 名称：`claw_server`
- Command：`~/bin/claw "{{prompt}}"`
- Description：调用云服务器上的 OpenClaw Agent 执行爬虫、定时任务、浏览器操作等

期望效果：

- 在本地 IDE 中直接说“用 `claw_server` 帮我监控某电商价格，如果降价 10% 就通知我”
- 由本地工具转发到服务器执行，再把结果返回

### 3. 封装为 OpenClaw Skill

在服务器上进入：

```bash
cd ~/.openclaw/workspace/skills
mkdir -p scraper
cd scraper
```

新建 `SKILL.md`：

```markdown
---
name: advanced_web_scraper
version: 1.0
description: 智能浏览器爬虫，支持 591、Amazon、PTT、论坛等，支持定时、总结、存 CSV/JSON
requires: [browser, playwright]
---

# 技能说明
我是一个专业的网页爬虫技能，可以：
- 用 Playwright 绕过反爬
- AI 自动清洗数据
- 支持 cron 定时
- 结果存 workspace 并推送 Telegram
```

安装方式：

```bash
docker compose exec openclaw-cli openclaw skills install ./scraper
```

目标效果：

- 服务器端 OpenClaw 自己能调用 `advanced_web_scraper`
- 以后可以直接要求它每天定时执行爬虫与总结

## 参考来源

- OpenClaw 官方安装文档：https://docs.openclaw.ai/install

从当前文档看，安装方式至少包括：

- 官方安装脚本
- `install-cli.sh` 本地前缀安装
- `npm` / `pnpm` / `bun`
- 源码安装
- Docker / Podman / Nix / Ansible 等容器或包管理路径

因此，这条实践后续提炼时需要区分：

- 哪些是“OpenClaw 的官方安装事实”
- 哪些是“我当前偏好的部署方式”
- 哪些是“本地 + 云端协同工作流的稳定原则”

## 待判断

- 这条实践里，哪些部分只是当前实现细节，哪些部分能提炼成长期有效的方法论？
- 它更适合并入 `[[Agent 编排与 Sub-agent]]`，还是以后拆成独立主题页？
- 是否还需要单独形成一篇对外表达的项目文章？
