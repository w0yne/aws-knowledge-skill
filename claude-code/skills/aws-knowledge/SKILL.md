---
name: aws-knowledge
description: >
  AWS expert powered by the AWS Knowledge MCP Server. Provides real-time access to AWS
  documentation, best practices, SOPs, and regional availability via MCP tools. Use when
  the user asks about AWS services (S3, EC2, Lambda, CDK, CloudFormation, ECS, EKS, RDS,
  DynamoDB, Bedrock, SageMaker, IAM, VPC, etc.), AWS architecture patterns, deployment
  guidance, troubleshooting, regional availability, cost optimization, security hardening,
  or any "how do I do X on AWS" question. Also activates for CDK/CloudFormation
  infrastructure-as-code questions and AWS Well-Architected guidance.
---

# AWS Knowledge Skill

Query the AWS Knowledge MCP Server for real-time AWS expertise.

## Prerequisites

The MCP server must be configured in your project's `.mcp.json`:

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

No authentication required.

## Query Routing

Route each question to the right tool:

| Question Type | Tool | Example |
|---|---|---|
| General "how to" / best practices | `search_documentation` | "How to secure an S3 bucket" |
| Read a specific doc page | `read_documentation` | "Show me the Lambda pricing page" |
| Explore related topics | `recommend` | "What else should I know about VPC peering" |
| Is a service available in a region | `get_regional_availability` | "Is Bedrock available in ap-southeast-1" |
| List all AWS regions | `list_regions` | "What regions does AWS have" |
| Step-by-step task walkthrough | `retrieve_agent_sops` | "Walk me through deploying to ECS" |

## Tool Calling

Call MCP tools directly:

```
# Search documentation (most common)
search_documentation(query="Lambda cold start optimization best practices")

# Filter by topic for targeted results
search_documentation(query="encryption at rest", topic="security")

# Read a specific documentation page
read_documentation(url="https://docs.aws.amazon.com/...")

# Get recommendations for related content
recommend(url="https://docs.aws.amazon.com/...")

# List all AWS regions
list_regions()

# Check regional availability
get_regional_availability(service="Amazon Bedrock", region="ap-southeast-1")

# Get step-by-step SOPs
retrieve_agent_sops(query="deploy containerized application to ECS")
```

## Workflow

1. **Classify the question** — determine which tool to call (see routing table)
2. **Call the tool** — use appropriate parameters
3. **Read deeper if needed** — use `read_documentation` on URLs from search results
4. **Synthesize the answer** — combine findings into a clear response
5. **Include sources** — always cite doc URLs so the user can verify
6. **Offer follow-ups** — suggest related topics or deeper dives

## Tips

- **Search first, don't guess.** Even for common topics, search to get the latest guidance.
- **Use SOPs for procedures.** If the user wants to DO something (deploy, configure, troubleshoot), check `retrieve_agent_sops` first — these are tested step-by-step workflows.
- **Check regional availability before recommending.** Don't assume a service is available everywhere.
- **Combine tools.** Typical flow: `search_documentation` → pick best result → `read_documentation` → `recommend` for related reading.
- **Topic filtering.** Use the `topic` parameter to narrow broad searches.

## Domain Patterns

For common query patterns organized by AWS domain, see `references/query-patterns.md`.
