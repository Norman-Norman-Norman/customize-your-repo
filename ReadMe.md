# The Definitive Guide to Customizing GitHub Copilot

*Published: February 5, 2026. File paths, configuration options, and feature availability may change as Copilot evolves—always verify against the [official documentation](https://code.visualstudio.com/docs/copilot).*

GitHub Copilot is more than autocomplete. When properly configured, it becomes a context-aware development partner that understands your architecture, follows your conventions, and produces code that passes review on the first try.

This guide is the complete reference for GitHub Copilot's six customization primitives—the configuration files and patterns that transform Copilot from a generic AI assistant into a team member who knows your codebase.

---

## Who This Guide Is For

This guide is written for developers and engineering leads who want to maximize the value of GitHub Copilot across their projects and teams. Whether you're a solo developer looking to reduce repetitive prompting, or a team lead standardizing AI-assisted workflows across an organization, you'll find actionable guidance here.

**Time investment:** Plan for 1-2 hours to read through the foundations and primitive documentation. The return on that investment compounds with every Copilot interaction thereafter.

---

## What You'll Learn

By the end of this guide, you will understand:

- **Why customization matters** — The difference between fighting Copilot and working with it
- **The six primitives** — What each configuration type does, when it activates, and when to use it
- **How primitives layer** — The mental model for how instructions, prompts, skills, agents, and MCP combine
- **Practical patterns** — Ready-to-use templates and real-world examples
- **Iteration strategies** — How to refine your configuration based on feedback
- **Measurement approaches** — How to track whether customization is working

You can start anywhere using the table of contents below, but if you're new to Copilot customization, start with [Part I: Foundations](docs/part-1-foundations.md).

---

## The Six Primitives

GitHub Copilot provides six customization primitives. Each serves a distinct purpose and loads at different points in your workflow:

| Primitive | Location | Purpose |
|-----------|----------|---------|
| [**Always-on Instructions**](docs/part-2-1-always-on-instructions.md) | `.github/copilot-instructions.md` or `AGENTS.md` | Global rules applied to every Copilot request |
| [**File-based Instructions**](docs/part-2-2-file-based-instructions.md) | `.github/instructions/*.instructions.md` | Rules that activate when working with specific file patterns |
| [**Prompts**](docs/part-2-3-prompts.md) | `.github/prompts/*.prompt.md` | Reusable task templates invoked as slash commands |
| [**Skills**](docs/part-2-4-skills.md) | `.github/skills/*/SKILL.md` | Procedural knowledge Copilot can discover and apply |
| [**Custom Agents**](docs/part-2-5-custom-agents.md) | `.github/agents/*.md` | Specialized AI personas with defined behaviors |
| [**MCP (Model Context Protocol)**](docs/part-2-6-mcp.md) | `.vscode/mcp.json` | Connections to external tools, APIs, and data sources |

Understanding when and how to use each primitive is the core of this guide.

---

## Guide Structure

### [Part I: Foundations](docs/part-1-foundations.md)

Start here. This section establishes the mental model for thinking about Copilot customization: why it matters, how the primitives layer together, strategies for iteration, and approaches for measuring success. The foundations section provides the conceptual framework that makes the rest of the guide actionable.

### [Part II: The Six Primitives](docs/part-2-primitives.md)

The heart of the guide. Each primitive receives comprehensive coverage including syntax, configuration options, practical examples, common patterns, and guidance on when to use it versus alternatives.

- [2.1 Always-on Instructions](docs/part-2-1-always-on-instructions.md) — Your codebase's global rules
- [2.2 File-based Instructions](docs/part-2-2-file-based-instructions.md) — Context-specific guidance
- [2.3 Prompts](docs/part-2-3-prompts.md) — Reusable slash commands
- [2.4 Skills](docs/part-2-4-skills.md) — Discoverable procedural knowledge
- [2.5 Custom Agents](docs/part-2-5-custom-agents.md) — Specialized AI personas
- [2.6 MCP](docs/part-2-6-mcp.md) — External tool integration

### [Part III: Reference](docs/part-3-reference.md)

Quick reference tables, starter templates, and a curated prompt library for common development tasks.

---

## Getting Started

Begin with [Part I: Foundations](docs/part-1-foundations.md) to understand the mental model, then work through the primitives in order. Each section builds on the previous, and the examples become more sophisticated as you progress.

For teams adopting Copilot customization, consider reading through the guide individually first, then convening to discuss which primitives and patterns fit your specific workflows.

---

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://code.visualstudio.com/docs/copilot)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [VS Code Source Code](https://github.com/microsoft/vscode) — The authoritative reference when documentation is unclear
- [product-brain](https://github.com/digitarald/product-brain) — A product management approach to workspace instructions
