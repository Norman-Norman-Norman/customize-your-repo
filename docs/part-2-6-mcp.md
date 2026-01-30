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

MCP servers are configured in dedicated `mcp.json` files:

**Workspace Configuration (.vscode/mcp.json):**
```json
{
  "servers": {
    "my-mcp-server": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@example/mcp-server"],
      "env": {
        "API_KEY": "${env:API_KEY}"
      }
    }
  }
}
```

**User Configuration:** Use Command Palette > **"MCP: Open User Configuration"** to edit `~/.vscode/mcp.json`.

**Additional fields for stdio servers:** `cwd` (working directory), `envFile` (path to .env file).

**For HTTP/SSE servers:**
```json
{
  "servers": {
    "remote-server": {
      "type": "sse",
      "url": "https://example.com/mcp",
      "headers": { "Authorization": "Bearer ${env:TOKEN}" }
    }
  }
}
```

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

### MCP vs. Skills: Complementary, Not Competing

MCP servers and skills serve different purposes, but teams sometimes wonder which to use. The short answer: **use both together.**

- **MCP servers** provide *access* — authentication, API connections, external integrations
- **Skills** provide *knowledge* — templates, conventions, workflows, domain expertise

The best setups combine them: an MCP server handles "how to connect to Jira" while a skill handles "how our team formats Jira tickets."

**Quick guidance:**
- Need to authenticate or call external APIs? → MCP server
- Need to encode team conventions or workflows? → Skill
- Need both access AND conventions? → Use both

For a detailed exploration with practical examples (Git, Jira, file operations), see [Skills vs. MCP Servers: When to Use Which](part-2-4-skills.md#skills-vs-mcp-servers-when-to-use-which).

---

[← Custom Agents](part-2-5-custom-agents.md) | [Next: Part III - Reference →](part-3-reference.md)
