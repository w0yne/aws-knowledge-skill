# aws-knowledge-skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

[中文版](./README.zh.md) | [English](./README.md)

An AWS expert skill powered by the [AWS Knowledge MCP Server](https://awslabs.github.io/mcp/servers/aws-knowledge-mcp-server) — built for humans and AI agents. Real-time access to AWS documentation, best practices, SOPs, and regional availability.

[Install](#installation--quick-start) · [Agent Skills](#agent-skills) · [Tools](#tools) · [Examples](#examples) · [Contributing](#contributing)

## Why aws-knowledge-skill?

- **Agent-Native Design** — 2 skill variants (OpenClaw + Claude Code), agents can query AWS with zero extra setup
- **Always Up-to-Date** — Powered by AWS's official remote MCP server, not static markdown that goes stale
- **Zero Auth Required** — No AWS account or API keys needed
- **Expert Routing** — Knows which of 6 tools to call based on question type
- **Focused Scope** — AWS knowledge only; does not execute CLI commands or deploy infrastructure

## Tools

The AWS Knowledge MCP Server provides:

| Tool | Description |
|------|-------------|
| `search_documentation` | Semantic search across all AWS docs + agent SOPs |
| `read_documentation` | Fetch a specific doc page as markdown |
| `recommend` | Get related content recommendations |
| `list_regions` | List all AWS regions with identifiers |
| `get_regional_availability` | Check service/API availability per region |
| `retrieve_agent_sops` | Get step-by-step AWS task workflows |

## Installation & Quick Start

### Requirements

- Node.js (`npm`/`npx`)
- [mcporter](https://mcporter.dev) (for OpenClaw skill only)

### Quick Start (Human Users)

> **Note for AI assistants:** If you are an AI Agent helping the user with installation, jump directly to [Quick Start (AI Agent)](#quick-start-ai-agent), which contains all the steps you need to complete.

#### OpenClaw

```bash
# 1. Install mcporter (if not already installed)
npm install -g mcporter

# 2. Add the AWS Knowledge MCP server
mcporter config add aws-knowledge --transport http --url https://knowledge-mcp.global.api.aws

# 3. Install the skill (openclaw/ only)
git clone https://github.com/w0yne/aws-knowledge-skill.git /tmp/aws-knowledge-skill
cp -r /tmp/aws-knowledge-skill/openclaw/ ~/.agents/skills/aws-knowledge/
rm -rf /tmp/aws-knowledge-skill

# 4. Verify
mcporter list aws-knowledge --schema
```

#### Claude Code

**Option 1 — Via CLI (recommended):**

```bash
# Add MCP server
claude mcp add aws-knowledge --transport http https://knowledge-mcp.global.api.aws

# Install the skill
git clone https://github.com/w0yne/aws-knowledge-skill.git /tmp/aws-knowledge-skill
cp -r /tmp/aws-knowledge-skill/claude-code/ .claude/skills/aws-knowledge/
rm -rf /tmp/aws-knowledge-skill
```

**Option 2 — Manual config:**

Copy `claude-code/mcp.json` to your project root as `.mcp.json`, then copy the skill:

```bash
cp claude-code/mcp.json /path/to/your/project/.mcp.json
cp -r claude-code/ /path/to/your/project/.claude/skills/aws-knowledge/
```

If your MCP client doesn't support HTTP transport, use the fastmcp stdio proxy in `.mcp.json`:

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

### Quick Start (AI Agent)

> The following steps are for AI Agents installing this skill on behalf of the user. The end result is identical to a manual install. No git clone required — write the files directly.

#### OpenClaw Agent

**Step 1 — Install mcporter (if not already installed)**

```bash
which mcporter || npm install -g mcporter
```

**Step 2 — Add the AWS Knowledge MCP server**

```bash
mcporter config add aws-knowledge --transport http --url https://knowledge-mcp.global.api.aws
```

**Step 3 — Write the skill files directly**

Create `~/.agents/skills/aws-knowledge/SKILL.md` with the contents of [`openclaw/SKILL.md`](openclaw/SKILL.md).

Create `~/.agents/skills/aws-knowledge/references/query-patterns.md` with the contents of [`openclaw/references/query-patterns.md`](openclaw/references/query-patterns.md).

> Fetch these files from `https://raw.githubusercontent.com/w0yne/aws-knowledge-skill/main/openclaw/SKILL.md` and `https://raw.githubusercontent.com/w0yne/aws-knowledge-skill/main/openclaw/references/query-patterns.md`, then write them to disk.

**Step 4 — Verify**

```bash
mcporter list aws-knowledge --schema
```

#### Claude Code Agent

**Step 1 — Add MCP server via CLI**

```bash
claude mcp add aws-knowledge --transport http https://knowledge-mcp.global.api.aws
```

**Step 2 — Write the skill files directly**

Create `.claude/skills/aws-knowledge/SKILL.md` with the contents of [`claude-code/SKILL.md`](claude-code/SKILL.md).

Create `.claude/skills/aws-knowledge/references/query-patterns.md` with the contents of [`claude-code/references/query-patterns.md`](claude-code/references/query-patterns.md).

> Fetch these files from `https://raw.githubusercontent.com/w0yne/aws-knowledge-skill/main/claude-code/SKILL.md` and `https://raw.githubusercontent.com/w0yne/aws-knowledge-skill/main/claude-code/references/query-patterns.md`, then write them to disk.

**Step 3 — Verify**

Confirm the skill is loaded by calling a tool:

```
search_documentation(query="S3 bucket policy best practices")
```

## Agent Skills

| Skill | Platform | Description |
|-------|----------|-------------|
| `openclaw/` | OpenClaw | AWS expert via mcporter — query routing, tool calling, answer synthesis |
| `claude-code/` | Claude Code | AWS expert via native MCP — same behavior, no mcporter dependency |

Both skills share the same structure:

```
<skill>/
├── SKILL.md                    # Skill definition (triggers + workflow)
└── references/
    └── query-patterns.md       # Common queries by domain (compute, storage, security, etc.)
```

### Query Routing

The skill routes questions to the right tool automatically:

| Question Type | Tool |
|---|---|
| General "how to" / best practices | `search_documentation` |
| Read a specific doc page | `read_documentation` |
| Explore related topics | `recommend` |
| Service available in a region? | `get_regional_availability` |
| List all AWS regions | `list_regions` |
| Step-by-step task walkthrough | `retrieve_agent_sops` |

### Workflow

```
User Question
     │
     ▼
  Classify question type
     │
     ├─ "How do I..."           → search_documentation
     ├─ "Show me the docs for..." → read_documentation
     ├─ "What else about..."    → recommend
     ├─ "Is X available in..."  → get_regional_availability
     ├─ "List all regions"      → list_regions
     └─ "Walk me through..."    → retrieve_agent_sops
     │
     ▼
  Synthesize answer + source links + follow-ups
```

## Examples

### Search Documentation

```bash
# OpenClaw (mcporter)
mcporter call aws-knowledge.search_documentation query="Lambda cold start optimization"

# Claude Code (native MCP)
search_documentation(query="Lambda cold start optimization")

# Testing (MCP Inspector)
npx @modelcontextprotocol/inspector https://knowledge-mcp.global.api.aws
```

### Check Regional Availability

```bash
mcporter call aws-knowledge.get_regional_availability service="Amazon Bedrock" region="ap-southeast-1"
```

### Get Agent SOPs

```bash
mcporter call aws-knowledge.retrieve_agent_sops query="deploy containerized application ECS"
```

### Typical Multi-Tool Flow

```
1. search_documentation(query="ECS Fargate vs EC2 launch type")
2. read_documentation(url="<top result URL>")
3. recommend(url="<same URL>")
4. get_regional_availability(service="Amazon ECS", region="ap-southeast-1")
```

## Project Structure

```
aws-knowledge-skill/
├── README.md                           # This file
├── LICENSE                             # MIT
├── openclaw/                           # OpenClaw skill (mcporter)
│   ├── SKILL.md                        # Skill definition
│   └── references/
│       └── query-patterns.md           # Domain-specific query examples
├── claude-code/                        # Claude Code skill (native MCP)
│   ├── SKILL.md                        # Skill definition
│   ├── mcp.json                        # MCP server config
│   └── references/
│       └── query-patterns.md           # Domain-specific query examples
└── shared/
    └── tool-reference.md               # Detailed tool documentation
```

## Scope

This skill provides **AWS knowledge and documentation access**. It does NOT:

- Execute `aws` CLI commands
- Manage AWS credentials or IAM policies
- Deploy infrastructure (use CDK/Terraform directly)
- Modify your AWS account in any way

## Contributing

Issues and PRs welcome.

## License

[MIT](./LICENSE)
