---
# Tavily MCP server configuration for agentic workflows.
# Used by workflows that need web search capabilities.
# Consumer example:
#   imports:
#     - an-lee/workflows/agentic/shared/mcp/tavily.md@v1

import-schema:
  api-key:
    type: string
    required: true
    description: Tavily API key
---

## Tavily Search Tool

Use Tavily for web searches when you need current information.

```markdown
tavily_search({
  query: string,
  max_results?: number  # default: 5
})
```

## When to Use

- Researching unknown libraries or frameworks
- Looking up documentation
- Finding code examples
- Verifying current best practices

## Rate Limits

- Free tier: 1000 searches/month
- Use `max_results: 3` for simple lookups to conserve quota
