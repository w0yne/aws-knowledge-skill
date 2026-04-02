# aws-knowledge-skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

[中文版](./README.zh.md) | [English](./README.md)

基于 [AWS Knowledge MCP Server](https://awslabs.github.io/mcp/servers/aws-knowledge-mcp-server) 的 AWS 专家技能 —— 为人类和 AI Agent 打造。实时访问 AWS 文档、最佳实践、SOP 和区域可用性信息。

[安装](#安装与快速开始) · [Agent 技能](#agent-技能) · [工具](#工具) · [示例](#示例) · [贡献](#贡献)

## 为什么选择 aws-knowledge-skill？

- **Agent 原生设计** — 2 种技能变体（OpenClaw + Claude Code），Agent 零配置即可查询 AWS
- **始终保持最新** — 基于 AWS 官方远程 MCP 服务器，而非会过时的静态 Markdown
- **零认证** — 无需 AWS 账户或 API 密钥
- **智能路由** — 根据问题类型自动选择 6 种工具中最合适的
- **聚焦范围** — 仅提供 AWS 知识查询，不执行 CLI 命令或部署基础设施

## 工具

AWS Knowledge MCP Server 提供以下工具：

| 工具 | 描述 |
|------|------|
| `search_documentation` | 语义搜索所有 AWS 文档 + Agent SOP |
| `read_documentation` | 获取指定文档页面并转为 Markdown |
| `recommend` | 获取相关内容推荐 |
| `list_regions` | 列出所有 AWS 区域及标识 |
| `get_regional_availability` | 检查服务/API 在指定区域的可用性 |
| `retrieve_agent_sops` | 获取 AWS 任务的分步操作流程 |

## 安装与快速开始

### 环境要求

- Node.js（`npm`/`npx`）
- [mcporter](https://mcporter.dev)（仅 OpenClaw 技能需要）

### 快速开始（人类用户）

> **AI 助手请注意：** 如果你是 AI Agent 正在帮助用户安装，请直接跳到 [快速开始（AI Agent）](#快速开始ai-agent)。

#### OpenClaw

```bash
# 1. 安装 mcporter（如未安装）
npm install -g mcporter

# 2. 添加 AWS Knowledge MCP 服务器
mcporter config add aws-knowledge --transport http --url https://knowledge-mcp.global.api.aws

# 3. 安装技能（仅 openclaw/ 目录）
git clone https://github.com/w0yne/aws-knowledge-skill.git /tmp/aws-knowledge-skill
cp -r /tmp/aws-knowledge-skill/openclaw/ ~/.agents/skills/aws-knowledge/
rm -rf /tmp/aws-knowledge-skill

# 4. 验证
mcporter list aws-knowledge --schema
```

#### Claude Code

```bash
# 1. 复制 MCP 配置到项目
cp claude-code/mcp.json /path/to/your/project/.mcp.json

# 2. 复制技能到项目
cp -r claude-code/ /path/to/your/project/.claude/skills/aws-knowledge/
```

如果 MCP 客户端不支持 HTTP 传输，可在 `.mcp.json` 中使用 fastmcp stdio 代理：

```json
{
  "mcpServers": {
    "aws-knowledge": {
      "command": "uvx",
      "args": ["fastmcp", "run", "https://knowledge-mcp.global.api.aws"]
    }
  }
}
```

### 快速开始（AI Agent）

> 以下步骤面向 AI Agent 代替用户安装本技能。最终结果与手动安装完全一致。无需 git clone —— 直接写入文件即可。

#### OpenClaw Agent

**步骤 1 — 安装 mcporter（如未安装）**

```bash
which mcporter || npm install -g mcporter
```

**步骤 2 — 添加 AWS Knowledge MCP 服务器**

```bash
mcporter config add aws-knowledge --transport http --url https://knowledge-mcp.global.api.aws
```

**步骤 3 — 直接写入技能文件**

将 [`openclaw/SKILL.md`](openclaw/SKILL.md) 的内容写入 `~/.agents/skills/aws-knowledge/SKILL.md`。

将 [`openclaw/references/query-patterns.md`](openclaw/references/query-patterns.md) 的内容写入 `~/.agents/skills/aws-knowledge/references/query-patterns.md`。

> 从 `https://raw.githubusercontent.com/w0yne/aws-knowledge-skill/main/openclaw/SKILL.md` 和 `https://raw.githubusercontent.com/w0yne/aws-knowledge-skill/main/openclaw/references/query-patterns.md` 获取文件内容，然后写入磁盘。

**步骤 4 — 验证**

```bash
mcporter list aws-knowledge --schema
```

#### Claude Code Agent

**步骤 1 — 写入 MCP 服务器配置**

创建或合并到项目的 `.mcp.json`：

```json
{
  "mcpServers": {
    "aws-knowledge": {
      "url": "https://knowledge-mcp.global.api.aws",
      "type": "http"
    }
  }
}
```

如果不支持 HTTP 传输，使用 fastmcp stdio 代理：

```json
{
  "mcpServers": {
    "aws-knowledge": {
      "command": "uvx",
      "args": ["fastmcp", "run", "https://knowledge-mcp.global.api.aws"]
    }
  }
}
```

**步骤 2 — 直接写入技能文件**

将 [`claude-code/SKILL.md`](claude-code/SKILL.md) 的内容写入 `.claude/skills/aws-knowledge/SKILL.md`。

将 [`claude-code/references/query-patterns.md`](claude-code/references/query-patterns.md) 的内容写入 `.claude/skills/aws-knowledge/references/query-patterns.md`。

> 从 `https://raw.githubusercontent.com/w0yne/aws-knowledge-skill/main/claude-code/SKILL.md` 和 `https://raw.githubusercontent.com/w0yne/aws-knowledge-skill/main/claude-code/references/query-patterns.md` 获取文件内容，然后写入磁盘。

**步骤 3 — 验证**

调用一个工具确认技能已加载：

```
search_documentation(query="S3 bucket policy best practices")
```

## Agent 技能

| 技能 | 平台 | 描述 |
|------|------|------|
| `openclaw/` | OpenClaw | 通过 mcporter 的 AWS 专家 — 查询路由、工具调用、答案整合 |
| `claude-code/` | Claude Code | 通过原生 MCP 的 AWS 专家 — 相同行为，无需 mcporter |

两种技能共享相同结构：

```
<skill>/
├── SKILL.md                    # 技能定义（触发条件 + 工作流）
└── references/
    └── query-patterns.md       # 按领域分类的常用查询（计算、存储、安全等）
```

### 查询路由

技能会自动将问题路由到正确的工具：

| 问题类型 | 工具 |
|---|---|
| "如何..." / 最佳实践 | `search_documentation` |
| 查看特定文档页面 | `read_documentation` |
| 探索相关主题 | `recommend` |
| 某服务在某区域是否可用？ | `get_regional_availability` |
| 列出所有 AWS 区域 | `list_regions` |
| 分步操作指南 | `retrieve_agent_sops` |

### 工作流

```
用户问题
  │
  ▼
分类问题类型
  │
  ├─ "如何..."               → search_documentation
  ├─ "给我看...的文档"        → read_documentation
  ├─ "还有什么关于..."        → recommend
  ├─ "X 在某区域可用吗"       → get_regional_availability
  ├─ "列出所有区域"           → list_regions
  └─ "带我一步步..."          → retrieve_agent_sops
  │
  ▼
整合答案 + 来源链接 + 后续建议
```

## 示例

### 搜索文档

```bash
# OpenClaw（mcporter）
mcporter call aws-knowledge.search_documentation query="Lambda cold start optimization"

# Claude Code（原生 MCP）
search_documentation(query="Lambda cold start optimization")

# 测试（MCP Inspector）
npx @modelcontextprotocol/inspector https://knowledge-mcp.global.api.aws
```

### 检查区域可用性

```bash
mcporter call aws-knowledge.get_regional_availability service="Amazon Bedrock" region="ap-southeast-1"
```

### 获取 Agent SOP

```bash
mcporter call aws-knowledge.retrieve_agent_sops query="deploy containerized application ECS"
```

### 典型多工具流程

```
1. search_documentation(query="ECS Fargate vs EC2 launch type")
2. read_documentation(url="<首个结果 URL>")
3. recommend(url="<同一 URL>")
4. get_regional_availability(service="Amazon ECS", region="ap-southeast-1")
```

## 项目结构

```
aws-knowledge-skill/
├── README.md                           # English
├── README.zh.md                        # 中文
├── LICENSE                             # MIT
├── openclaw/                           # OpenClaw 技能（mcporter）
│   ├── SKILL.md                        # 技能定义
│   └── references/
│       └── query-patterns.md           # 按领域分类的查询示例
├── claude-code/                        # Claude Code 技能（原生 MCP）
│   ├── SKILL.md                        # 技能定义
│   ├── mcp.json                        # MCP 服务器配置
│   └── references/
│       └── query-patterns.md           # 按领域分类的查询示例
└── shared/
    └── tool-reference.md               # 详细工具文档
```

## 范围

本技能提供 **AWS 知识和文档访问**，不会：

- 执行 `aws` CLI 命令
- 管理 AWS 凭证或 IAM 策略
- 部署基础设施（请直接使用 CDK/Terraform）
- 以任何方式修改你的 AWS 账户

## 贡献

欢迎提交 Issue 和 PR。

## 许可证

[MIT](./LICENSE)
