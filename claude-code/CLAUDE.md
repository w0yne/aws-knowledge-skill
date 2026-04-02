# AWS Knowledge

This project has access to the AWS Knowledge MCP Server for real-time AWS documentation and guidance.

## When to Use

Use the AWS Knowledge MCP tools whenever you need to:
- Answer AWS-related questions (services, architecture, deployment)
- Look up API parameters, configuration options, or service limits
- Check if a service is available in a specific region
- Follow step-by-step procedures for AWS tasks
- Get best practices for security, cost, or performance

## How to Use

### Search first, don't guess
Even for topics you know well, search for the latest guidance:
```
search_documentation(query="S3 bucket policy best practices")
```

### Use SOPs for step-by-step tasks
When the user wants to DO something (deploy, configure, troubleshoot):
```
retrieve_agent_sops(query="deploy containerized application to ECS")
```

### Check regional availability before recommending
Don't assume a service is available in every region:
```
get_regional_availability(service="Amazon Bedrock", region="ap-southeast-1")
```

### Read full docs when search results aren't enough
Drill into specific documentation pages:
```
read_documentation(url="https://docs.aws.amazon.com/...")
```

### Combine tools for thorough answers
Typical flow: search → read top result → recommend related content

## Rules

1. **Always cite sources** — include doc URLs in your answers
2. **Search before answering** — don't rely on training data for AWS specifics
3. **Prefer SOPs for procedures** — they're tested step-by-step workflows
4. **Check availability for region-specific advice** — use get_regional_availability
5. **Stay focused** — this is for AWS knowledge; don't use these tools for non-AWS questions
