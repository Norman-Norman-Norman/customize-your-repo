# Copilot Instructions for customize-your-repo

## Project Overview

This repository contains "The Definitive Guide to Customizing Your Repo for GitHub Copilot" — a comprehensive documentation guide covering GitHub Copilot's six customization primitives. This is a documentation project, not a code project.

## Content Guidelines

### Tone and Voice
- Use professional, third-person tone throughout
- Write for an audience of experienced developers and team leads
- Be direct and concise — avoid filler phrases
- Match the existing document's voice and structure

### Accuracy Requirements
- All technical claims must align with official documentation:
  - https://code.visualstudio.com/docs/copilot
  - https://docs.github.com/en/copilot
- **Always fetch the latest documentation before answering questions about Copilot features** — your training data may be outdated
- Use the Microsoft docs tools to search and fetch from code.visualstudio.com
- Use the fetch_webpage tool for docs.github.com/en/copilot pages
- Never invent frontmatter fields, tool names, or configuration options

### The Six Primitives

When discussing customization options, reference the correct primitive:

| Primitive | Location | Purpose |
|-----------|----------|---------|
| Always-on Instructions | `.github/copilot-instructions.md` | Global codebase rules |
| File-based Instructions | `.github/instructions/*.instructions.md` | Pattern-matched rules |
| Prompts | `.github/prompts/*.prompt.md` | Reusable task templates |
| Skills | `.github/skills/` | Portable capabilities |
| Custom Agents | `.github/agents/*.md` | Specialized personas |
| MCP | `.vscode/mcp.json` | External integrations |

### Detailed Topic References

For in-depth documentation guidelines on each primitive, refer to the topic-specific instruction files:

| Topic | Instruction File | Applies To |
|-------|------------------|------------|
| Always-on Instructions | `.github/instructions/always-on-instructions.instructions.md` | `**/copilot-instructions.md` |
| File-based Instructions | `.github/instructions/file-based-instructions.instructions.md` | `**/*.instructions.md` |
| Prompts | `.github/instructions/prompts.instructions.md` | `**/*.prompt.md` |
| Skills | `.github/instructions/skills.instructions.md` | `**/skills/**,**/SKILL.md` |
| Custom Agents | `.github/instructions/custom-agents.instructions.md` | `**/*.agent.md` |
| MCP | `.github/instructions/mcp.instructions.md` | `**/mcp.json,**/mcp/**` |

These files automatically activate when working on related content.

### Structure Conventions
- Use H2 (`##`) for main sections
- Use H3 (`###`) for subsections
- Include practical examples with ✅/❌ patterns where helpful
- Add rationale ("Why") for recommendations
- Use tables for comparisons and quick reference

### Formatting Rules
- **Code examples**: Use fenced code blocks with language identifiers (```typescript, ```markdown, etc.)
- **User prompts**: Format as blockquotes with the "💬 Try this prompt:" header pattern
- **Everything else**: Use normal markdown formatting (no blockquotes, no special containers)
- Don't use blockquotes for general explanatory text, tips, or notes
- Don't use special formatting for section introductions or descriptions

### What NOT to Do
- Don't use first person ("I think...")
- Don't include outdated information (moment.js examples, class components, etc.)
- Don't expose internal tool names (like `mcp_github_*`) in user-facing prompts
- Don't duplicate content that exists elsewhere in the document
- Don't add sections without updating the Table of Contents

## Document Maintenance

When editing ReadMe.md:
1. Check Table of Contents matches actual sections
2. Verify all internal links work
3. Ensure code examples use modern patterns
4. Keep file under 2500 lines — split if growing too large

## Commit Message Style

Use descriptive commit messages that explain what changed:
- Good: "Add Quick Start section for org-wide rollout"
- Bad: "Update readme"
