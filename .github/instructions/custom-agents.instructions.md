---
name: 'Custom Agents Documentation'
description: 'Guidelines for documenting custom agent configurations'
applyTo: '**/agents/**,**/*.agent.md'
---

# Custom Agents Documentation Guidelines

## What Are Custom Agents?

Custom Agents provide specialized AI personas with constrained tool access and defined behaviors. They can operate as top-level assistants or as subagents invoked by other workflows.

**Location:** `.github/agents/*.md` (any `.md` file except `README.md`) or `**/*.agent.md` anywhere

## When to Use Custom Agents

- When Copilot should adopt a specific expert persona
- When different contexts require different AI behaviors
- When consistent response styles are needed across a team
- For specialized work like security review, architecture, or mentoring

## File Format

```markdown
---
name: 'Display Name'
description: 'Shown in agent picker'
tools: ['search', 'readFile', 'usages']
model: 'Claude Opus 4.6'
handoffs:
  - label: 'Hand off label'
    agent: 'target-agent'
    prompt: 'Context for handoff'
---

[Agent instructions in markdown]
```

## Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | No | Display name in the agent picker |
| `description` | No | Placeholder text in chat input |
| `tools` | No | List of tools available to this agent |
| `model` | No | AI model to use (Claude Opus 4.6, GPT-5.2) |
| `handoffs` | No | Define transitions to other agents |

## The Agent Formula

Every effective agent includes:

```markdown
# Who You Are
[Persona and expertise]

# How You Think
[Approach and methodology]

# How You Respond
[Output format and style]

# What You Always Do
[Consistent behaviors]

# What You Never Do
[Guardrails and limitations]
```

## Prompt vs. Agent Comparison

| Aspect | Prompt File | Custom Agent |
|--------|-------------|--------------|
| Duration | One-shot task | Entire conversation |
| Variables | Yes | No |
| Purpose | Task templates | Persona/behavior |
| State | Stateless | Persists during conversation |

## Sub-Agents: Context Isolation

Custom agents can be invoked as sub-agents for context isolation:
- Each sub-agent runs in isolated context
- Prevents context bleed between unrelated tasks
- Sub-agent returns a summary, not full context

## Example Agents

**Security Reviewer:**
```markdown
---
name: 'Security Reviewer'
tools: ['search', 'readFile', 'usages', 'getChangedFiles']
model: 'Claude Opus 4.6'
---

You are a security engineer reviewing code for vulnerabilities.

## Your Expertise
- OWASP Top 10
- Common vulnerability patterns
- Authentication and authorization

## How You Respond
- Start with risk assessment (Critical/High/Medium/Low)
- Cite specific CWE IDs
- Provide concrete fix recommendations
```

**Rubber Duck:**
```markdown
---
name: 'Rubber Duck'
tools: ['search', 'readFile']
model: 'GPT-5.2'
---

You are a rubber duck. Your job is NOT to solve problems—it's to help the user solve them by asking good questions.

## Your Technique
- Ask clarifying questions
- Repeat back what you heard
- Never give direct answers until asked
```
