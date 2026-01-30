# Part II: The Six Primitives

[? Back to Guide](../ReadMe.md) | [? Part I: Foundations](part-1-foundations.md)

---

## The Customization Primitives

GitHub Copilot provides six distinct customization primitives. Each serves a specific purpose and loads at different points in the interaction lifecycle. Understanding when each primitive activates is essential for effective configuration.

| Primitive | Location | Loading | Best For |
|-----------|----------|---------|----------|
| [**Always-on Instructions**](part-2-1-always-on-instructions.md) | `.github/copilot-instructions.md` | Every session | Codebase guardrails |
| [**File-based Instructions**](part-2-2-file-based-instructions.md) | `.github/instructions/*.instructions.md` | Pattern match / description match | Area-specific rules |
| [**Prompts (Slash Commands)**](part-2-3-prompts.md) | `.github/prompts/*.prompt.md` | User invokes | One-shot workflows |
| [**Skills**](part-2-4-skills.md) | `.github/skills/` | Description match  on-demand | Reusable capabilities |
| [**Custom Agents**](part-2-5-custom-agents.md) | `.github/agents/*.md` | Top-level OR as subagent | Constrained workflows |
| [**MCP**](part-2-6-mcp.md) | `.vscode/mcp.json` | Session start | External gateways |

This table represents the complete customization surface area. Each primitive addresses a distinct need:

- [**Always-on Instructions**](part-2-1-always-on-instructions.md)  Global rules that apply to every Copilot interaction in the repository
- [**File-based Instructions**](part-2-2-file-based-instructions.md)  Targeted rules that activate based on file patterns or context
- [**Prompts (Slash Commands)**](part-2-3-prompts.md)  Reusable task templates invoked explicitly by users
- [**Skills**](part-2-4-skills.md)  Discrete capabilities that Copilot can invoke when relevant
- [**Custom Agents**](part-2-5-custom-agents.md)  Specialized AI personas with constrained tool access and behaviors
- [**MCP (Model Context Protocol)**](part-2-6-mcp.md)  External service integrations that extend Copilot's capabilities

---

## Quick Navigation

### 1. [Always-On Instructions](part-2-1-always-on-instructions.md)
The foundational layer  rules that apply to every Copilot interaction in your repository. Define your tech stack, coding conventions, security requirements, and anti-patterns here.

### 2. [File-Based Instructions](part-2-2-file-based-instructions.md)
Targeted rules that activate based on file patterns. Use these for language-specific conventions, area-specific rules, or different standards for different parts of your codebase.

### 3. [Prompt Files (Slash Commands)](part-2-3-prompts.md)
Reusable task templates invoked with `/promptname`. Create prompts for component scaffolding, test generation, documentation, and any repeatable workflow.

### 4. [Skills](part-2-4-skills.md)
Discrete capabilities that Copilot loads on-demand when relevant. Package specialized knowledge  like Prisma migrations or GitHub issue templates  into reusable skills.

### 5. [Custom Agents](part-2-5-custom-agents.md)
Specialized AI personas with constrained behaviors and tool access. Build code reviewers, architects, mentors, and other role-specific assistants.

### 6. [MCP (Model Context Protocol)](part-2-6-mcp.md)
External service integrations that extend Copilot's capabilities. Connect to databases, APIs, ticketing systems, and any external tool your workflow requires.

---

[? Part I: Foundations](part-1-foundations.md) | [Next: Part III - Reference ?](part-3-reference.md)
