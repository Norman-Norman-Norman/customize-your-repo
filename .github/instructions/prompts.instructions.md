---
name: 'Prompts Documentation'
description: 'Guidelines for documenting prompt files (slash commands)'
applyTo: '**/prompts/**,**/*.prompt.md'
---

# Prompts Documentation Guidelines

## What Are Prompt Files?

Prompt files enable reusable task templates that can be invoked on demand. They function as macros for common workflows, reducing repetitive typing and ensuring consistent outputs.

**Location:** `.github/prompts/*.prompt.md`

Users invoke prompts by typing `/` in Copilot Chat and selecting from available options.

## File Format

```markdown
---
agent: 'agent'
description: 'What this prompt does'
model: 'Claude Opus 4.6'
tools: ['editFiles', 'createFile', 'terminalCommand']
---

[Prompt instructions with {{variables}}]
```

## Frontmatter Fields

| Field | Description |
|-------|-------------|
| `name` | Display name shown when typing `/` |
| `description` | Brief description of what the prompt does |
| `agent` | Execution mode: `ask`, `agent`, or custom agent name |
| `model` | AI model to use |
| `tools` | Specific tools available for this prompt |
| `argument-hint` | Hint text for user interaction |

## Execution Modes

| Mode | What It Does | Best For |
|------|--------------|----------|
| `ask` | Read-only — responds conversationally | Questions, explanations, code review |
| `agent` | Takes autonomous action — creates/edits files | Scaffolding, bug fixes, refactoring |

**Note:** An `edit` mode exists but is not recommended. Use `agent` for any file modifications.

## Variable Syntax

Use `{{variableName}}` for parameterized prompts:

```markdown
Create a new component called `{{componentName}}` in `src/components/`:
- Use TypeScript with our Props interface pattern
- Include loading and error states
```

When invoked, Copilot prompts for values for each variable.

## Prompt Quality Guidelines

**❌ Ineffective:**
```markdown
Make me a component.
```

**✅ Effective:**
```markdown
Create a React component called `{{componentName}}` in `src/components/`:

1. Use TypeScript with our Props interface pattern
2. Include loading & error states
3. Follow accessibility guidelines (WCAG 2.1 AA)
4. Create co-located test file with:
   - Render test
   - User interaction tests
   - Edge case tests
5. Match patterns in existing components
```

## Essential Prompts Every Repo Needs

1. **Component Generator** - Scaffold new components with tests
2. **API Route Generator** - Create endpoints with validation
3. **Bug Fix Assistant** - Analyze and fix with explanation
4. **Code Review Prep** - Prepare code for review
5. **Documentation Generator** - Add JSDoc and inline comments

## Referencing Instructions

Prompts can reference the instructions file for consistency:

```markdown
Create a new component following the patterns defined in our 
copilot-instructions.md, specifically:
- The component structure we use
- Our prop typing conventions
- Our testing requirements
```
