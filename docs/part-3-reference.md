# Part III: Reference

[← Back to Guide](../ReadMe.md) | [← Part II: The Six Primitives](part-2-primitives.md)

---

## Quick Reference Tables

### The Six Primitives at a Glance

| What You Want | Use This | Location |
|---------------|----------|----------|
| Global coding rules | Always-On Instructions | `.github/copilot-instructions.md` |
| Rules for specific files | File-Based Instructions | `.github/instructions/*.instructions.md` |
| Reusable task template | Prompt File | `.github/prompts/*.prompt.md` |
| Specialized capability | Skill | `.github/skills/*/SKILL.md` |
| AI persona/behavior | Custom Agent | `.github/agents/*.md` |
| External tool access | MCP Server | `.vscode/mcp.json` |

### Frontmatter Field Reference

**File-Based Instructions:**
| Field | Required | Description |
|-------|----------|-------------|
| `applyTo` | Yes | Glob pattern for automatic application |
| `name` | No | Display name (defaults to filename) |
| `description` | No | Description shown on hover |

**Prompt Files:**
| Field | Required | Description |
|-------|----------|-------------|
| `agent` | No | Execution mode: `ask`, `agent`, or agent name |
| `description` | No | Brief description for `/` menu |
| `model` | No | AI model to use |
| `tools` | No | Specific tools available |

**Skills (SKILL.md):**
| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | 1-64 chars, lowercase, hyphens only |
| `description` | Yes | 1-1024 chars, WHAT it does + WHEN to use |
| `metadata` | No | Key-value pairs (author, version) |

**Custom Agents:**
| Field | Required | Description |
|-------|----------|-------------|
| `name` | No | Display name in agent picker |
| `description` | No | Placeholder text in chat input |
| `tools` | No | List of available tools |
| `model` | No | AI model to use |
| `handoffs` | No | Transitions to other agents |

### Execution Modes Reference

| Mode | Copilot Can... | Best For |
|------|----------------|----------|
| `ask` | Respond conversationally (read-only) | Q&A, explanations, code review |
| `agent` | Create/edit files, run commands | Any task that modifies code |
| Custom agent | Use that agent's persona and tools | Specialized workflows |

### Tool Access Reference

| Tool | Description |
|------|-------------|
| `search` | Search codebase |
| `readFile` | Read file contents |
| `editFiles` | Modify existing files |
| `createFile` | Create new files |
| `usages` | Find symbol usages |
| `terminalCommand` | Run terminal commands |
| `fetch` | HTTP requests |
| `githubRepo` | GitHub API access |
| `getChangedFiles` | Get PR/branch changes |

### Glob Pattern Reference

| Pattern | Matches |
|---------|---------|
| `*` | All files in current directory |
| `**` | All files in all directories |
| `*.ts` | All .ts files in current directory |
| `**/*.ts` | All .ts files recursively |
| `src/**/*.ts` | All .ts files under src/ |
| `**/*.{ts,tsx}` | All .ts and .tsx files |
| `**/tests/**` | All files in any tests/ directory |

---

## Templates and Starter Files

### Starter copilot-instructions.md

```markdown
# Copilot Instructions for [Project Name]

## Project Overview
[One paragraph describing what this project does]

## Tech Stack
- [Primary language/framework]
- [Database]
- [Key libraries]

## Code Style
- [Rule 1]
- [Rule 2]
- [Rule 3]

## Architecture
[Brief description of how code is organized]

## Testing
- [Testing framework]
- [Coverage requirements]
- [Where tests live]

## What NOT to Do
- Don't [anti-pattern 1]
- Don't [anti-pattern 2]
- Don't [anti-pattern 3]
```

### Starter File-Based Instruction

```markdown
---
name: 'React Components'
description: 'Conventions for React component development'
applyTo: 'src/components/**/*.tsx'
---

# React Component Guidelines

## Component Structure
- Functional components only
- Props interface above component
- Custom hooks in separate files

## Naming
- PascalCase for components
- camelCase for hooks (use- prefix)

## Patterns
[Add specific patterns here]
```

### Starter Prompt File

```markdown
---
agent: 'agent'
description: 'Brief description of what this prompt does'
model: 'Claude Sonnet 4'
---

[Clear instruction of what to do]

**Input:** {{variable1}}

## Requirements
1. [Requirement 1]
2. [Requirement 2]
3. [Requirement 3]

## Output Format
[Describe expected output]

## Reference
[Point to relevant files or patterns in codebase]
```

### Starter SKILL.md

```markdown
---
name: skill-name
description: What this skill does and when to use it. Use when the user mentions [trigger words].
metadata:
  author: your-org
  version: "1.0"
---

# Skill Name

## When to Use This Skill
Use this skill when:
- [Trigger condition 1]
- [Trigger condition 2]

## Instructions
[Detailed instructions for the capability]

## Examples
[Show example usage]

## Common Patterns
| Task | Approach |
|------|----------|
| [Task 1] | [How to do it] |
```

### Starter Custom Agent

```markdown
---
name: 'Agent Name'
description: 'What this agent specializes in'
tools: ['search', 'readFile']
model: 'Claude Sonnet 4'
---

You are [specific persona description].

## Your Expertise
- [Area 1]
- [Area 2]

## How You Respond
- [Response style 1]
- [Response style 2]

## What You Always Do
- [Consistent behavior 1]
- [Consistent behavior 2]

## What You Never Do
- [Guardrail 1]
- [Guardrail 2]
```

### Starter MCP Configuration

```json
{
  "servers": {
    "server-name": {
      "command": "npx",
      "args": ["@package/mcp-server"],
      "env": {
        "API_TOKEN": "${env:API_TOKEN}"
      }
    }
  }
}
```

---

## Sample Prompt Library

### Prompts for Creating Instructions

> 💬 **Generate instructions from codebase:**
>
> Analyze this repository and create a `.github/copilot-instructions.md` file that documents:
> 1. The tech stack and architecture
> 2. Coding conventions found in existing code
> 3. Patterns that should be followed
> 4. Anti-patterns to avoid
> 5. Testing requirements
> Include rationale for each guideline.

> 💬 **Improve existing instructions:**
>
> Review `.github/copilot-instructions.md` and:
> 1. Check for contradictions
> 2. Add examples for important rules
> 3. Include rationale where missing
> 4. Remove obvious/standard rules
> 5. Verify accuracy against codebase

### Prompts for Creating Prompts

> 💬 **Create a new prompt file:**
>
> Create a prompt file at `.github/prompts/{{name}}.prompt.md` that:
> - Uses agent mode with Claude Sonnet 4
> - Includes variables for: {{variables}}
> - References our copilot-instructions.md
> - Has clear success criteria

### Prompts for Creating Skills

> 💬 **Create a new skill:**
>
> Create a skill at `.github/skills/{{name}}/SKILL.md` following the agentskills.io spec:
> - Description optimized for discovery
> - Clear trigger conditions
> - Step-by-step instructions
> - Practical examples

### Prompts for Creating Agents

> 💬 **Create a custom agent:**
>
> Create a custom agent at `.github/agents/{{name}}.agent.md` with:
> - Specific expertise area
> - Clear behavioral guidelines
> - Response format specification
> - Appropriate tool restrictions
> - Guardrails for consistency

### Prompts for Debugging

> 💬 **Check what's loaded:**
>
> What instructions, skills, and tools are currently active in this conversation?

> 💬 **Trace a problem:**
>
> I expected [X] but got [Y]. Walk me through:
> 1. What rules you're following
> 2. What context you have
> 3. Why you made this choice

---

## Cross-Reference: Primitives by Use Case

| Use Case | Primary Primitive | Supporting Primitives |
|----------|-------------------|----------------------|
| Enforce coding standards | [Always-On Instructions](part-2-1-always-on-instructions.md) | File-Based Instructions for exceptions |
| Generate components | [Prompts](part-2-3-prompts.md) | Instructions for conventions |
| Code review | [Custom Agents](part-2-5-custom-agents.md) | Instructions for standards |
| External API access | [MCP](part-2-6-mcp.md) | Skills for conventions |
| Domain expertise | [Skills](part-2-4-skills.md) | Instructions for context |
| File-type specific rules | [File-Based Instructions](part-2-2-file-based-instructions.md) | Always-On for global rules |

---

## Resources

### Official Documentation

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://code.visualstudio.com/docs/copilot)
- [Awesome Copilot Repository](https://github.com/github/awesome-copilot) — Community examples, prompts, and resources
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) — Building custom integrations
- [Agent Skills Specification](https://agentskills.io) — Portable skill format

### Community Resources

The [Awesome Copilot](https://github.com/github/awesome-copilot) repository provides:
- Production instruction files
- Tested prompt templates
- Community custom agents
- MCP server examples
- Integration patterns

### Implementation Roadmap

| Timeline | Action | Outcome |
|----------|--------|---------|
| Day 1 | Create `copilot-instructions.md` with 5 key rules | Immediate convention enforcement |
| Week 1 | Add 2-3 prompt files for repeated tasks | Consistent task automation |
| Month 1 | Implement custom agents for specialized workflows | Role-based AI assistance |
| Quarter 1 | Evaluate MCP servers and Skills | External integrations, portable capabilities |

---

## Summary

| Objective | Solution | Learn More |
|-----------|----------|------------|
| Set permanent coding rules | Instructions file | [Always-On Instructions](part-2-1-always-on-instructions.md) |
| Rules for specific file types | File-based instructions | [File-Based Instructions](part-2-2-file-based-instructions.md) |
| Create a reusable task | Prompt file | [Prompts](part-2-3-prompts.md) |
| Specialized capability | Skill | [Skills](part-2-4-skills.md) |
| Define AI persona/behavior | Custom Agent | [Custom Agents](part-2-5-custom-agents.md) |
| Connect to external data/tools | MCP integration | [MCP](part-2-6-mcp.md) |

---

*For additional examples and community contributions, visit [Awesome Copilot](https://github.com/github/awesome-copilot).*

---

[← Part II: The Six Primitives](part-2-primitives.md) | [Back to Guide →](../ReadMe.md)
