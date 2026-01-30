# Customizing GitHub Copilot for Your Repository

**Stop fighting Copilot. Start configuring it.**

Every rejected suggestion, every "why did it use Redux?", every PR comment about wrong patterns—these aren't Copilot failures. They're context failures. Copilot doesn't know your team chose React Query. It doesn't know your API wrapper convention. It doesn't know `moment.js` is banned.

This guide fixes that. Six configuration primitives, one afternoon of setup, and Copilot becomes a team member who actually knows your codebase.

---

## 5-Minute Quick Start

Create `.github/copilot-instructions.md` with your essentials:

```markdown
# Project Context

React 18 + TypeScript + React Query for data fetching.
Vitest for testing. Tailwind for styling.

## Rules

- Use React Query for all API calls, never raw fetch or Redux
- All API responses use our `ApiResponse<T>` wrapper from `@/lib/api`
- Prefer named exports over default exports
- Use `date-fns` for dates, never `moment.js`
```

That's it. Copilot now follows your conventions in every chat and suggestion.

**Next steps:**
1. Add rules when you catch Copilot making the same mistake twice
2. Create a [prompt file](docs/part-2-3-prompts.md) for your most repeated task
3. Read [Part I: Foundations](docs/part-1-foundations.md) for the full mental model

---

## The Six Primitives

| Primitive | Location | Purpose |
|-----------|----------|---------|
| [Always-on Instructions](docs/part-2-1-always-on-instructions.md) | `.github/copilot-instructions.md` | Global rules for every request |
| [File-based Instructions](docs/part-2-2-file-based-instructions.md) | `.github/instructions/*.instructions.md` | Rules that activate for specific files |
| [Prompts](docs/part-2-3-prompts.md) | `.github/prompts/*.prompt.md` | Reusable slash commands (`/my-prompt`) |
| [Skills](docs/part-2-4-skills.md) | `.github/skills/*/SKILL.md` | Procedural knowledge Copilot can invoke |
| [Custom Agents](docs/part-2-5-custom-agents.md) | `.github/agents/*.md` | Specialized AI personas (`@reviewer`) |
| [MCP](docs/part-2-6-mcp.md) | `.vscode/mcp.json` | Connect external tools and APIs |

---

## Guide Structure

### [Part I: Foundations](docs/part-1-foundations.md)
The mental model—why customization matters, how primitives layer, iteration strategies, measuring success.

### [Part II: The Six Primitives](docs/part-2-primitives.md)
Deep dives into each primitive with syntax, examples, and best practices:
- [2.1 Always-on Instructions](docs/part-2-1-always-on-instructions.md)
- [2.2 File-based Instructions](docs/part-2-2-file-based-instructions.md)
- [2.3 Prompts](docs/part-2-3-prompts.md)
- [2.4 Skills](docs/part-2-4-skills.md)
- [2.5 Custom Agents](docs/part-2-5-custom-agents.md)
- [2.6 MCP](docs/part-2-6-mcp.md)

### [Part III: Reference](docs/part-3-reference.md)
Quick reference tables, starter templates, and a sample prompt library.

---

## Resources

- [GitHub Copilot Docs](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://code.visualstudio.com/docs/copilot)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [VS Code Source](https://github.com/microsoft/vscode) — the authoritative reference when docs are unclear
