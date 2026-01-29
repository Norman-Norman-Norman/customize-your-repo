# The Definitive Guide to GitHub Copilot Primitives

This is the complete reference for GitHub Copilot's six customization primitives — the configuration files that transform Copilot from a generic assistant into a team-aware coding partner.

**What you'll learn:**
- How each primitive works and when it loads
- When to use each one (and when not to)
- How primitives interact and layer together
- Ready-to-use templates

---

## Quick Start: The Adoption Path

Get productive fast with this step-by-step path:

| Step | Action | Outcome |
|------|--------|--------|
| 1 | Create `.github/copilot-instructions.md` with tech stack + top 3-5 rules | Copilot follows your conventions |
| 2 | Add one prompt file for a repeated task | Consistent, reusable workflow |
| 3 | Expand instructions based on friction | Fewer PR corrections |
| 4 | Add file-based instructions for specialized areas | Right rules for right files |
| 5 | Create prompts for team workflows | Shared productivity gains |
| 6 | Add custom agents for specialized roles | Focused personas (reviewer, architect) |
| 7 | Evaluate Skills and MCP for advanced needs | External integrations, portable capabilities |

---

## The Six Primitives

| Primitive | Location | Best For |
|-----------|----------|----------|
| [**Always-on Instructions**](docs/part-2-1-always-on-instructions.md) | `.github/copilot-instructions.md` | Codebase guardrails, tech stack |
| [**File-based Instructions**](docs/part-2-2-file-based-instructions.md) | `.github/instructions/*.instructions.md` | Area-specific rules |
| [**Prompts (Slash Commands)**](docs/part-2-3-prompts.md) | `.github/prompts/*.prompt.md` | Reusable task templates |
| [**Skills**](docs/part-2-4-skills.md) | `.github/skills/*/SKILL.md` | Specialized capabilities |
| [**Custom Agents**](docs/part-2-5-custom-agents.md) | `.github/agents/*.agent.md` | AI personas and workflows |
| [**MCP**](docs/part-2-6-mcp.md) | `.vscode/mcp.json` | External tool integrations |

---

## Table of Contents

### [Part I: Foundations](docs/part-1-foundations.md)
Why customize, the mental model, how primitives load, iteration and measurement

### [Part II: The Six Primitives](docs/part-2-primitives.md)
Deep dive into each primitive with examples and best practices

### [Part III: Reference](docs/part-3-reference.md)
Quick reference tables, starter templates, sample prompt library

---

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://code.visualstudio.com/docs/copilot)
- [Awesome Copilot Repository](https://github.com/github/awesome-copilot)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/)
