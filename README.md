# aws-knowledge-skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An AWS expert skill powered by the [AWS Knowledge MCP Server](https://awslabs.github.io/mcp/servers/aws-knowledge-mcp-server) — built for humans, AI agents, and coding assistants. Get real-time access to AWS documentation, best practices, SOPs, and regional availability without static reference files that go stale.

[Install](#installation) · [OpenClaw](#openclaw-skill) · [Claude Code](#claude-code) · [Tools](#available-tools) · [Examples](#examples) · [Contributing](#contributing)

## Why aws-knowledge-skill?

- **Always Up-to-Date** — Powered by AWS's official remote MCP server, not static markdown files
- **Zero Auth Required** — No AWS account or API keys needed for knowledge queries
- **Two Flavors** — OpenClaw skill (via mcporter) and Claude Code MCP config
- **Expert Behavior** — Not just doc search; routes queries intelligently across 6 specialized tools
- **Agent-Native** — Designed for AI agents to use effectively with minimal token overhead

## Available Tools

The AWS Knowledge MCP Server provides 6 tools:

| Tool | Description | Use When |
|------|-------------|----------|
| `search_documentation` | Semantic search across all AWS docs + agent SOPs | General questions, "how do I..." |
| `read_documentation` | Fetch a specific doc page as markdown | Deep-diving into a topic |
| `recommend` | Get related content recommendations | Exploring a topic further |
| `list_regions` | List all AWS regions with identifiers | Region planning |
| `get_regional_availability` | Check service/API availability per region | Before deploying to a region |
| `retrieve_agent_sops` | Get step-by-step AWS task workflows | Deployment, troubleshooting, setup |

## Installation

### OpenClaw Skill

#### Option 1 — ClawHub (recommended)

```bash
npx clawhub install aws-knowledge-skill
```

#### Option 2 — From source

```bash
# Clone into your skills directory
git clone https://github.com/w0yne/aws-knowledge-skill.git ~/.agents/skills/aws-knowledge-skill
```

#### Prerequisites

The OpenClaw skill uses [mcporter](https://mcporter.dev) to communicate with the MCP server.

```bash
# Install mcporter if not already installed
npm install -g mcporter

# Add the AWS Knowledge MCP server
mcporter config add aws-knowledge \
  --transport http \
  --url https://knowledge-mcp.global.api.aws
```

Verify it works:

```bash
mcporter list aws-knowledge --schema
```

### Claude Code

#### Step 1 — Add MCP server config

Copy `claude-code/mcp.json` to your project root as `.mcp.json`, or add to your existing config:

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

If your client doesn't support HTTP transport, use the fastmcp stdio proxy:

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

#### Step 2 — Install the skill

Copy the `claude-code/` folder into your project's skills directory:

```bash
cp -r claude-code/ /path/to/your/project/.claude/skills/aws-knowledge/
```

Or clone and symlink:

```bash
git clone https://github.com/w0yne/aws-knowledge-skill.git
ln -s $(pwd)/aws-knowledge-skill/claude-code /path/to/your/project/.claude/skills/aws-knowledge
```

### Cursor / VS Code

Use the one-click install links:

- **Cursor:** [Install](https://cursor.com/en/install-mcp?name=aws-knowledge-mcp&config=eyJ1cmwiOiJodHRwczovL2tub3dsZWRnZS1tY3AuZ2xvYmFsLmFwaS5hd3MifQ==)
- **VS Code:** [Install](https://vscode.dev/redirect/mcp/install?name=aws-knowledge-mcp&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fknowledge-mcp.global.api.aws%22%7D)

## OpenClaw Skill

### When It Triggers

The skill activates when you mention:
- AWS services (S3, EC2, Lambda, CDK, CloudFormation, ECS, RDS, etc.)
- AWS architecture, best practices, or deployment patterns
- Regional availability or service coverage
- AWS troubleshooting or error resolution

### How It Works

```
User Question
     │
     ▼
┌─────────────┐
│ Classify     │ → What type of question is this?
│ Question     │
└──────┬──────┘
       │
       ├─ "How do I..."  ──────────→ search_documentation
       ├─ "Show me the docs for..." → read_documentation
       ├─ "What else about..."  ───→ recommend
       ├─ "Is X available in..." ──→ get_regional_availability
       ├─ "List all regions"  ─────→ list_regions
       └─ "Walk me through..."  ───→ retrieve_agent_sops
              │
              ▼
       Synthesize answer with
       source links + follow-ups
```

### Example Usage (OpenClaw)

```
User: How should I set up a VPC for a production workload?

Agent: [calls mcporter call aws-knowledge.search_documentation query="VPC production setup best practices"]
       [reads top result with read_documentation]
       [synthesizes answer with architecture guidance + links]
```

## Claude Code Skill

The `claude-code/` directory is a complete skill with its own `SKILL.md`, `references/`, and MCP config.
It uses native MCP tool calls (no mcporter dependency).

## Examples

### Search Documentation

```bash
# Via mcporter (OpenClaw)
mcporter call aws-knowledge.search_documentation query="Lambda cold start optimization"

# Via MCP Inspector (testing)
npx @modelcontextprotocol/inspector https://knowledge-mcp.global.api.aws
```

### Check Regional Availability

```bash
mcporter call aws-knowledge.get_regional_availability \
  service="Amazon Bedrock" \
  region="ap-southeast-1"
```

### Get Agent SOPs

```bash
mcporter call aws-knowledge.retrieve_agent_sops \
  query="deploy containerized application ECS"
```

### List Regions

```bash
mcporter call aws-knowledge.list_regions
```

## Quick Start (AI Agent)

> If you are an AI agent setting this up, follow these steps:

**Step 1 — Install mcporter and add the server**

```bash
npm install -g mcporter
mcporter config add aws-knowledge --transport http --url https://knowledge-mcp.global.api.aws
```

**Step 2 — Verify**

```bash
mcporter list aws-knowledge --schema
```

**Step 3 — Start calling tools**

```bash
# Search
mcporter call aws-knowledge.search_documentation query="S3 bucket policy best practices"

# Read a specific doc
mcporter call aws-knowledge.read_documentation url="https://docs.aws.amazon.com/..."

# Check availability
mcporter call aws-knowledge.get_regional_availability service="AWS Lambda" region="eu-west-1"
```

**Step 4 — Synthesize results into a clear answer with source links**

## Project Structure

```
aws-knowledge-skill/
├── README.md                         # This file
├── LICENSE                           # MIT
├── openclaw/                         # OpenClaw skill (uses mcporter)
│   ├── SKILL.md                      # Skill definition
│   └── references/
│       └── query-patterns.md         # Domain-specific query examples
├── claude-code/                      # Claude Code skill (native MCP)
│   ├── SKILL.md                      # Skill definition
│   ├── mcp.json                      # MCP server config (copy to .mcp.json)
│   └── references/
│       └── query-patterns.md         # Domain-specific query examples
└── shared/
    └── tool-reference.md             # Detailed tool documentation
```

## Compatibility

| Platform | Method | Status |
|----------|--------|--------|
| OpenClaw | mcporter skill | ✅ |
| Claude Code | MCP config | ✅ |
| Cursor | MCP config | ✅ |
| VS Code (Copilot) | MCP config | ✅ |
| Windsurf | MCP config | ✅ |
| Any MCP client | HTTP transport | ✅ |

## Contributing

Issues and PRs welcome. This skill is intentionally focused — it wraps the AWS Knowledge MCP Server and provides intelligent query routing. It does NOT:

- Execute `aws` CLI commands (use [aws-infra](https://clawhub.ai/skills/aws-infra) for that)
- Manage AWS credentials or IAM
- Deploy infrastructure (use CDK/Terraform directly)

## License

[MIT](./LICENSE)
