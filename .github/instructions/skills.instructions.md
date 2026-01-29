---
name: 'Skills Documentation'
description: 'Guidelines for documenting Agent Skills following the agentskills.io specification'
applyTo: '**/skills/**,**/skill*'
---

# Skills Documentation Guidelines

## What Are Agent Skills?

Agent Skills is an open standard for teaching Copilot specialized capabilities through folders containing instructions, scripts, and resources. Skills are portable via the [agentskills.io](https://agentskills.io) specification.

## Directory Structure

Every skill lives in its own folder with a `SKILL.md` file:

```
.github/skills/
├── skill-name/
│   ├── SKILL.md          # Required: instructions + metadata
│   ├── scripts/          # Optional: executable code
│   ├── references/       # Optional: additional documentation
│   └── assets/           # Optional: templates, images, data
```

## SKILL.md Format

Required frontmatter fields:
- `name`: 1-64 chars, lowercase, hyphens only, must match directory name
- `description`: 1-1024 chars, describe WHAT it does AND WHEN to use it

Optional frontmatter:
- `metadata`: Key-value pairs (author, version)
- `license`: License name or reference
- `compatibility`: Environment requirements (vscode, github-cli, coding-agent)

## Name Validation Rules

✅ **Valid:** `image-manipulation`, `github-issues`, `web-testing`
❌ **Invalid:** `Image-Manipulation` (uppercase), `-image` (starts with hyphen), `image--test` (consecutive hyphens)

## Description Best Practices

The description is critical for skill discovery. Include:
- WHAT the skill does
- WHEN to use it (trigger words)
- Key action words users might say

```markdown
# ❌ Ineffective
description: 'Image processing skill'

# ✅ Effective
description: 'Resize, convert, compress, and batch-process images using ImageMagick. Use when the user mentions image optimization, format conversion, or bulk image operations.'
```

## How Skills Load

Unlike file-based instructions (pattern matching), skills load on-demand via description matching:
1. System prompt includes list of available skills (names + descriptions)
2. Agent decides which skills are relevant based on user request
3. Only then is full skill content loaded into context

## Skills vs. MCP: When to Use Which

| Factor | Use a Skill | Use MCP |
|--------|-------------|---------|
| Security boundary | No auth needed | Requires API keys |
| Tool availability | Locally installed | Remote access |
| Portability | Across AI agents | Host-specific |

Many workflows combine both: MCP for API access + Skill for conventions/templates.

## Example Skill Structure

```markdown
---
name: github-issues
description: Create well-structured GitHub issues using team templates. Use when the user wants to file a bug, request a feature, or create any GitHub issue.
metadata:
  author: your-org
  version: "1.0"
---

# GitHub Issue Creation

## When to Use This Skill
[Trigger conditions]

## Instructions
[Step-by-step procedures]

## Templates
[Reusable templates]
```
