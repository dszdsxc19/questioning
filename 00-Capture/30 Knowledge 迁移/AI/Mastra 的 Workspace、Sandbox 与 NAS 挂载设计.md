---
title: Mastra 的 Workspace、Sandbox 与 NAS 挂载设计
tags:
  - AI
  - Agent
  - Mastra
  - 基础设施
  - Workspace
  - Sandbox
created: 2026-04-01
updated: 2026-04-01
source: https://docs.mastra.ai
type: capture
status: inbox
source_type: migrated_note
source_path: "AI/Mastra 的 Workspace、Sandbox 与 NAS 挂载设计.md"
---

# Mastra 的 Workspace、Sandbox 与 NAS 挂载设计

> **核心概念**：Mastra 的 workspace 和 sandbox 是"包含关系"，不是平级系统。workspace 是 Agent 的工作环境容器，sandbox 是 workspace 里的命令执行能力。

---

## 📐 架构关系

```
Workspace（工作环境容器）
├── filesystem（文件系统）
├── sandbox（命令执行）← 这里
├── search（搜索）
├── skills（技能）
└── lsp（代码分析）
```

**理解方式**：
- **Workspace** = Agent 的完整工作台（类似 Claude Code 的项目目录 + 工具权限）
- **Sandbox** = Workspace 里的终端环境（类似 isolated container）

---

## 🏗️ Workspace 是什么

Workspace 是给 Agent 提供的受控运行环境，统一管理以下能力：

| 能力 | 说明 | 使用场景 |
|---|---|---|
| **文件系统** | 读/写文件 | 代码修改、配置更新、文档生成 |
| **命令执行** | 运行 shell 命令 | npm install、pytest、构建 |
| **搜索** | 代码/文本搜索 | 查找函数定义、定位问题 |
| **Skills** | 加载外部技能模块 | 扩展 Agent 能力 |
| **LSP** | 代码分析与跳转 | 理解代码结构、类型检查 |

**典型配置**：

```javascript
const workspace = new Workspace({
  filesystem: new LocalFilesystem({
    basePath: './workspace',
  }),
  sandbox: new LocalSandbox({
    workingDirectory: './workspace',
  }),
  skills: ['/skills'],
})
```

---

## 🐚 Sandbox 是什么

Sandbox 只负责一件事：**执行 shell 命令 / 运行代码**

**支持的典型操作**：
```bash
npm install
npm run build
pytest
node index.js
git status
```

**官方定义**：
> Sandbox: Command execution and background processes

**多种实现**：

| 类型 | 适用场景 |
|---|---|
| `LocalSandbox` | 本地开发环境 |
| `E2BSandbox` | 云端隔离容器 |
| `DockerSandbox` | Docker 容器环境 |

---

## 🔗 它们的关系

### 1. 包含关系

**Sandbox 是 Workspace 的一个子能力**，不是独立的主概念。

```javascript
// ✅ 正确理解
Workspace.sandbox = new LocalSandbox(...)

// ❌ 错误理解
Workspace 和 Sandbox 是两个平级系统
```

### 2. 为什么经常一起出现

因为很多 Agent 场景需要"读写 + 执行"配套：

**场景：代码修复 Agent**

```
第一步：读取代码
read_file("src/index.ts")
↓ 使用 workspace.filesystem

第二步：修改代码
write_file(...)
↓ 使用 workspace.filesystem

第三步：执行测试
npm test
↓ 使用 workspace.sandbox
```

**结论**：
- Workspace 提供文件能力
- Sandbox 提供执行能力
- 两者通常配套使用

---

## 🎯 灵活配置模式

### 模式 1：只用 Workspace，不配 Sandbox

```javascript
const workspace = new Workspace({
  filesystem: new S3Filesystem({
    bucket: 'my-bucket',
  }),
  // 没有 sandbox
})
```

**适用场景**：
- 文档 Agent（只读文档）
- 知识库 Agent（检索信息）
- 文件处理 Agent（格式转换）

**能力限制**：
- ✅ 读文件、写文件
- ❌ 执行命令

---

### 模式 2：只配 Sandbox

```javascript
const workspace = new Workspace({
  sandbox: new E2BSandbox({
    id: 'dev-sandbox',
  }),
})
```

**适用场景**：
- SQL 执行 Agent
- Python 代码运行 Agent
- Shell 自动化 Agent

**能力限制**：
- ✅ 执行命令
- ❌ 持久化文件读写

---

### 模式 3：完整配置（最常见）

```javascript
const workspace = new Workspace({
  filesystem: new LocalFilesystem({
    basePath: './workspace',
  }),
  sandbox: new LocalSandbox({
    workingDirectory: './workspace',
  }),
  skills: ['/skills'],
  search: new CodeSearch(),
})
```

**适用场景**：
- Coding Agent（代码生成 + 调试）
- 全栈开发 Agent（前后端一体化）
- DevOps Agent（部署 + 监控）

---

## 💾 NAS 挂载设计

### 设计思路

NAS（网络存储）挂载的本质是：**让远程存储像本地文件系统一样被 Workspace 访问**。

### 架构层次

```
Agent
  ↓
Workspace.filesystem
  ↓
Filesystem Adapter（适配层）
  ↓
NAS Mount Point（挂载点）
  ↓
Physical NAS（物理存储）
```

### 典型实现方案

#### 方案 1：OS 级挂载 + LocalFilesystem

```bash
# 系统层面先挂载 NAS
mount -t nfs nas.example.com:/data /mnt/nas

# Agent 配置
const workspace = new Workspace({
  filesystem: new LocalFilesystem({
    basePath: '/mnt/nas/project', // 指向 NAS 挂载点
  }),
})
```

**优点**：
- 配置简单，OS 处理所有网络细节
- 性能好，直接文件系统调用

**缺点**：
- 需要运维预先挂载
- 依赖特定 OS 支持

---

#### 方案 2：S3 + S3Filesystem（云原生）

```javascript
import { S3Filesystem } from '@mastra/filesystem';

const workspace = new Workspace({
  filesystem: new S3Filesystem({
    bucket: 'agent-workspace',
    region: 'us-east-1',
    prefix: 'projects/', // 可选的目录前缀
  }),
  sandbox: new E2BSandbox({
    // sandbox 也可以通过 s3fs 访问同一存储
  }),
})
```

**优点**：
- 云原生，无需本地挂载
- 天然支持多实例共享
- 权限体系成熟（IAM）

**缺点**：
- 依赖网络，延迟更高
- 成本相对较高

---

#### 方案 3：自定义 Adapter（高级）

```javascript
class NASFilesystem extends FilesystemAdapter {
  private nasClient: NASClient;

  constructor(config: NASConfig) {
    super();
    this.nasClient = new NASClient(config);
  }

  async readFile(path: string): Promise<string> {
    return await this.nasClient.read(path);
  }

  async writeFile(path: string, content: string): Promise<void> {
    return await this.nasClient.write(path, content);
  }
}

const workspace = new Workspace({
  filesystem: new NASFilesystem({
    endpoint: 'nas.example.com',
    credentials: { ... },
  }),
})
```

**适用场景**：
- 特定 NAS 协议（NFS/CIFS/自定义）
- 需要细粒度控制
- 企业内网存储

---

## 🎓 类比理解

### 类似 Claude Code 的设计

| Mastra | Claude Code | 说明 |
|---|---|---|
| **Workspace** | 项目目录 + 文件树 + 工具权限 | Agent 的工作边界 |
| **Sandbox** | 内置 terminal / isolated container | 命令执行环境 |
| **NAS 挂载** | Cloud workspace / remote repo | 远程存储访问 |

### 类似 Docker 的设计

| Mastra | Docker | 说明 |
|---|---|---|
| **Workspace** | Container | 隔离的运行环境 |
| **Sandbox** | Container 内的 shell | 命令执行入口 |
| **NAS 挂载** | Volume mount | 外部存储挂载 |

---

## 🔒 权限与安全设计

Mastra 的双层权限控制：

```javascript
tools: {
  EXECUTE_COMMAND: {
    requireApproval: true, // 命令执行需审批
  },
  WRITE_FILE: {
    requireApproval: false, // 文件写入自动允许
    allowedPaths: ['./workspace'], // 白名单
  },
}
```

**设计思想**：
1. **能访问什么** → Workspace 控制
2. **能执行什么** → Sandbox 控制
3. **是否需审批** → 工具级配置

这对生产环境至关重要。

---

## 📊 总结对比

| 维度 | Workspace | Sandbox |
|---|---|---|
| **定位** | 工作环境容器 | 命令执行模块 |
| **职责** | 统一管理所有能力 | 只负责执行命令 |
| **关系** | 父级概念 | 子级能力 |
| **可否独立** | 可以（只配文件系统） | 理论可以，但不推荐 |
| **典型场景** | 所有 Agent 需要配置 | 需要执行代码的 Agent |

**NAS 挂载核心要点**：
1. 本质是扩展 Workspace 的文件系统边界
2. 可以在 OS 层、适配层、云服务层实现
3. 需考虑性能、成本、共享性、安全性

---

## 📚 相关资源

- **Mastra 官方文档**：[https://docs.mastra.ai](https://docs.mastra.ai)
- **Claude Code**：[[Claude Code 安装与配置]]
- **Agent 设计模式**：[[Sub-agent 机制与使用场景]]
- **Skills 体系**：[[30 Knowledge/AI/武器库/Skills 库]]

---

*本文基于 Mastra 官方文档整理，结合 Claude Code/Codex 的实践经验进行类比说明。*
