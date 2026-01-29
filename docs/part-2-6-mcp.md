# MCP (Model Context Protocol)

[← Custom Agents](part-2-5-custom-agents.md) | [Part II Overview](part-2-primitives.md)

---

## Overview

MCP (Model Context Protocol) provides external gateway capabilities for Copilot. While instructions, prompts, and custom agents provide Copilot with knowledge, MCP enables Copilot to take real actions and access live data from external systems.

**Loading:** Session start
**Best For:** External gateways

> **Scope:** This section covers how GitHub Copilot *consumes* MCP servers — configuration, tool discovery, and invocation. It does not cover MCP server security, authentication implementation, or building custom MCP servers. For those topics, see the [MCP specification](https://modelcontextprotocol.io).

### How MCP Servers Expose Tools

MCP servers expose capabilities as **tools** that Copilot can discover and invoke. When Copilot starts a session with an MCP server configured, it queries the server for its available tools.

**The discovery flow:**

1. **Copilot connects** to the MCP server at session start
2. **Server responds** with a list of tools, each with:
   - Tool name (e.g., `create_issue`, `query_database`)
   - Description (helps Copilot decide when to use it)
   - Input schema (JSON Schema defining required parameters)
3. **Copilot adds tools** to its available capabilities
4. **During conversation**, Copilot matches user requests to tool descriptions
5. **When invoked**, Copilot constructs the parameters and calls the tool

**Example tool definition (from server's perspective):**

```json
{
  "name": "create_issue",
  "description": "Create a new GitHub issue in a repository",
  "inputSchema": {
    "type": "object",
    "properties": {
      "owner": { "type": "string", "description": "Repository owner" },
      "repo": { "type": "string", "description": "Repository name" },
      "title": { "type": "string", "description": "Issue title" },
      "body": { "type": "string", "description": "Issue body (markdown)" }
    },
    "required": ["owner", "repo", "title"]
  }
}
```

**What Copilot sees:** A tool called `create_issue` that it can call when users want to create GitHub issues. Copilot reads the description and schema to understand when and how to use it.

**What you see:** When Copilot decides to use the tool, it shows you the tool name and parameters before execution, giving you a chance to approve or modify.

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
