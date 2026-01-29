# MCP (Model Context Protocol)

[← Custom Agents](part-2-5-custom-agents.md) | [Part II Overview](part-2-primitives.md)

---

## Overview

MCP (Model Context Protocol) provides external gateway capabilities for Copilot. While instructions, prompts, and custom agents provide Copilot with knowledge, MCP enables Copilot to take real actions and access live data from external systems.

**Loading:** Session start
**Best For:** External gateways

### Instructions vs. MCP Capabilities

These two primitives are complementary:

- **Instructions** = What Copilot KNOWS ("Use parameterized queries")
- **MCP** = What Copilot CAN DO (Execute a database query and return results)

A configuration might include an instruction stating "always use our inventory API" alongside an MCP server that enables Copilot to actually call that API.

### Configuring MCP Servers

MCP servers can be configured in VS Code settings or project configuration:

**VS Code Settings (.vscode/settings.json):**
```json
{
  "github.copilot.chat.mcpServers": {
    "database": {
      "command": "npx",
      "args": ["@your-org/db-mcp-server"],
      "env": {
        "DATABASE_URL": "${env:DATABASE_URL}"
      }
    },
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
      }
    }
  }
}
```

### Available MCP Servers

| Server | What It Does |
|--------|--------------|
| `@modelcontextprotocol/server-github` | GitHub API access (issues, PRs, repos) |
| `@modelcontextprotocol/server-postgres` | PostgreSQL queries |
| `@modelcontextprotocol/server-filesystem` | File system operations |
| `@modelcontextprotocol/server-fetch` | HTTP requests |
| `@modelcontextprotocol/server-puppeteer` | Browser automation |

### Example Use Cases

- **"What's the status of issue #234?"**  GitHub MCP
- **"Show me all users who signed up this week"**  Database MCP
- **"Test this API endpoint"**  Fetch MCP
- **"Take a screenshot of the login page"**  Puppeteer MCP

### Instructions vs. MCP Comparison

This is an important distinction:

| | Custom Instructions | MCP |
|-|---------------------|-----|
| **What** | Text context for AI | External tool access |
| **How** | Just knowledge | Actual capabilities |
| **Example** | "We use PostgreSQL" | Can query PostgreSQL |
| **Updates** | Manual | Real-time |

**Instructions provide knowledge. MCP provides capabilities.**

---

[← Custom Agents](part-2-5-custom-agents.md) | [Next: Part III - Putting It All Together →](part-3-putting-it-together.md)
