# The Definitive Guide to Customizing Your Repo for GitHub Copilot

*Transform GitHub Copilot from a generic assistant into a team-aware coding partner.*

---

## Table of Contents

### [Part I: Foundations](docs/part-1-foundations.md)
1. Why Customize?
2. The Customization Primitives Overview
3. Understanding Context and Loading
4. Complete Setup Checklist

### [Part II: The Six Primitives](docs/part-2-primitives.md)
5. [Always-On Instructions](docs/part-2-1-always-on-instructions.md)
6. [File-Based Instructions](docs/part-2-2-file-based-instructions.md)
7. [Prompt Files (Slash Commands)](docs/part-2-3-prompts.md)
8. [Skills](docs/part-2-4-skills.md)
9. [Custom Agents](docs/part-2-5-custom-agents.md)
10. [MCP (Model Context Protocol)](docs/part-2-6-mcp.md)

### [Part III: Putting It All Together](docs/part-3-putting-it-together.md)
11. The Decision Matrix
12. Interaction and Layering
13. Real-World Examples

### [Part IV: Workflow Recipes](docs/part-4-workflow-recipes.md)
14. Issue Tracking & Ticket Management
15. Pull Request & Code Review Workflows
16. Deployment & Release Workflows
17. Testing Workflows
18. Documentation Workflows
19. Database Workflows
20. CI/CD Workflows
21. Communication & Notifications
22. Sprint & Project Management

### [Part V: Implementation Guide](docs/part-5-implementation-guide.md)
23. Quick Start: Org-Wide Rollout
24. Best Practices
25. Debugging and Troubleshooting
26. Frequently Asked Questions

### [Part VI: Reference](docs/part-6-reference.md)
27. Quick Reference Tables
28. Templates and Starter Files
29. Sample Prompt Library
30. Resources
31. Summary

---

## Quick Start

New to Copilot customization? Start here:

1. **[Part I: Foundations](docs/part-1-foundations.md)**  Understand the primitives and set up your repository
2. **[Part II: The Six Primitives](docs/part-2-primitives.md)**  Deep dive into each customization option
3. **[Part IV: Workflow Recipes](docs/part-4-workflow-recipes.md)**  Copy-paste ready recipes for common workflows

## The Six Primitives at a Glance

| Primitive | Location | Best For |
|-----------|----------|----------|
| [**Always-on Instructions**](docs/part-2-1-always-on-instructions.md) | `.github/copilot-instructions.md` | Codebase guardrails, tech stack |
| [**File-based Instructions**](docs/part-2-2-file-based-instructions.md) | `.github/instructions/*.instructions.md` | Area-specific rules |
| [**Prompts (Slash Commands)**](docs/part-2-3-prompts.md) | `.github/prompts/*.prompt.md` | Reusable task templates |
| [**Skills**](docs/part-2-4-skills.md) | `.github/skills/*/SKILL.md` | Specialized capabilities |
| [**Custom Agents**](docs/part-2-5-custom-agents.md) | `.github/agents/*.agent.md` | AI personas and workflows |
| [**MCP**](docs/part-2-6-mcp.md) | `.vscode/mcp.json` | External tool integrations |

---

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://code.visualstudio.com/docs/copilot)
- [Awesome Copilot Repository](https://github.com/github/awesome-copilot)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/)

---

*For the complete guide, navigate to any part using the Table of Contents above.*
