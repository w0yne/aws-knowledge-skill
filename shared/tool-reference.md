# AWS Knowledge MCP Server — Tool Reference

Detailed documentation for all 6 tools provided by the AWS Knowledge MCP Server (`https://knowledge-mcp.global.api.aws`).

## search_documentation

Semantic search across all AWS documentation and agent SOPs.

**Parameters:**
- `query` (string, required) — Search query in natural language
- `topic` (string, optional) — Filter by topic for more targeted results

**Returns:** Ranked list of documentation snippets with URLs, titles, and relevance scores.

**Best for:** General questions, "how do I...", best practices, error resolution, architecture guidance.

**Tips:**
- Use natural language, not keywords — "how to optimize Lambda cold starts" beats "lambda cold start"
- Add `topic` to narrow broad queries — e.g., topic="security" for security-specific results
- Results include URLs — use `read_documentation` to get the full page

---

## read_documentation

Fetch and convert a specific AWS documentation page to markdown.

**Parameters:**
- `url` (string, required) — Full URL of the AWS documentation page

**Returns:** Full page content as markdown.

**Best for:** Deep-diving into a specific topic, reading full guides, getting complete API references.

**Tips:**
- Use URLs from `search_documentation` results
- Good for getting complete configuration examples and parameter lists
- Content is converted to markdown for easy parsing

---

## recommend

Get content recommendations related to a documentation page.

**Parameters:**
- `url` (string, required) — URL of the documentation page to get recommendations for

**Returns:** List of related documentation pages with titles and URLs.

**Best for:** Exploring a topic further, finding related guides, discovering adjacent services.

**Tips:**
- Feed this a URL from a previous search or read
- Useful for building comprehensive answers that cover related topics
- Good for "what else should I know about X" questions

---

## list_regions

List all AWS regions with their identifiers and display names.

**Parameters:** None

**Returns:** Complete list of AWS regions (e.g., `us-east-1` → "US East (N. Virginia)").

**Best for:** Region planning, listing available deployment targets.

---

## get_regional_availability

Check whether a specific AWS service, feature, API, or CloudFormation resource is available in a given region.

**Parameters:**
- `service` (string, required) — AWS service name (e.g., "Amazon Bedrock", "AWS Lambda")
- `region` (string, optional) — AWS region identifier (e.g., "us-west-2", "ap-southeast-1")

**Returns:** Availability status for the requested service in the specified region(s).

**Best for:** Pre-deployment checks, multi-region architecture planning, service selection.

**Tips:**
- Use official service names (e.g., "Amazon S3" not just "S3")
- Omit region to get availability across all regions
- Always check before recommending a service for a specific region

---

## retrieve_agent_sops

Retrieve complete step-by-step Standard Operating Procedures for common AWS tasks.

**Parameters:**
- `query` (string, required) — Description of the task or workflow

**Returns:** Detailed, tested step-by-step instructions for completing the task.

**Best for:** Deployment procedures, troubleshooting workflows, infrastructure setup, security hardening.

**Tips:**
- SOPs are tested workflows — prefer these over ad-hoc instructions
- Great for operational tasks: "deploy X", "troubleshoot Y", "set up Z"
- Results are structured with clear steps, commands, and verification checks
