---
name: 'MCP Documentation'
description: 'Guidelines for documenting Model Context Protocol configurations'
applyTo: '**/mcp.json,**/mcp/**'
---

# MCP (Model Context Protocol) Documentation Guidelines

## What Is MCP?

MCP (Model Context Protocol) provides external gateway capabilities for Copilot. While instructions provide knowledge, MCP enables Copilot to take real actions and access live data from external systems.

**Location:** `.vscode/mcp.json`

## Instructions vs. MCP

| | Instructions | MCP |
|-|--------------|-----|
| What | Text context for AI | External tool access |
| How | Just knowledge | Actual capabilities |
| Example | "We use PostgreSQL" | Can query PostgreSQL |
| Updates | Manual | Real-time |

**Instructions provide knowledge. MCP provides capabilities.**

## Configuration Format

```json
{
  "servers": {
    "server-name": {
      "command": "npx",
      "args": ["@package/mcp-server"],
      "env": {
        "API_KEY": "${env:API_KEY}"
      }
    }
  }
}
```

## Available MCP Servers

| Server | Capabilities |
|--------|--------------|
| `@modelcontextprotocol/server-github` | GitHub API (issues, PRs, repos) |
| `@modelcontextprotocol/server-postgres` | PostgreSQL queries |
| `@modelcontextprotocol/server-filesystem` | File system operations |
| `@modelcontextprotocol/server-fetch` | HTTP requests |
| `@modelcontextprotocol/server-puppeteer` | Browser automation |

## When to Use MCP vs. Skills

| Factor | Use MCP | Use Skill |
|--------|---------|-----------|
| Security boundary | Requires auth/API keys | No auth needed |
| Tool availability | Remote access needed | Locally installed |
| Real-time data | Required | Not needed |
| Portability | Host-specific | Across AI agents |

## Common Use Cases

- **"What's the status of issue #234?"** → GitHub MCP
- **"Show users who signed up this week"** → Database MCP
- **"Test this API endpoint"** → Fetch MCP
- **"Screenshot the login page"** → Puppeteer MCP

## Combined Pattern: MCP + Skill

Many workflows combine both:
- **MCP Server** handles authentication and API access
- **Skill** provides team templates, conventions, and workflow rules

Example:
```
Developer: "Create a Jira ticket for this bug"

Skill: Provides ticket template, required fields, labels
MCP: Authenticates and creates the ticket via API
```

## Environment Variables

Use VS Code's variable syntax for secrets:
```json
{
  "env": {
    "GITHUB_TOKEN": "${env:GITHUB_TOKEN}",
    "DATABASE_URL": "${env:DATABASE_URL}"
  }
}
```

Never commit actual secrets to the repository.

## Troubleshooting

1. **Server won't start:** Check command and args are correct
2. **Auth failures:** Verify environment variables are set
3. **Missing capabilities:** Ensure server package is installed
4. **Timeout issues:** Check network connectivity to external services

## Example Configuration

```json
{
  "servers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
      }
    },
    "jira": {
      "command": "npx",
      "args": ["@your-org/jira-mcp-server"],
      "env": {
        "JIRA_URL": "${env:JIRA_URL}",
        "JIRA_EMAIL": "${env:JIRA_EMAIL}",
        "JIRA_API_TOKEN": "${env:JIRA_API_TOKEN}"
      }
    }
  }
}
```
