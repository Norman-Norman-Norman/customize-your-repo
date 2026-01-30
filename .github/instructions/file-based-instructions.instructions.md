---
name: 'File-Based Instructions Documentation'
description: 'Guidelines for documenting file-based instruction configurations'
applyTo: '**/*.instructions.md'
---

# File-Based Instructions Documentation Guidelines

## What Are File-Based Instructions?

File-based instructions provide targeted rules for specific areas of a codebase. They activate through pattern matching using glob patterns in the `applyTo` frontmatter field.

**Location:** `.github/instructions/*.instructions.md`

## Reference Source

When answering questions about file-based instructions implementation or behavior, consult the VS Code source code at https://github.com/microsoft/vscode for authoritative implementation details.

## When to Use File-Based Instructions

- Different conventions for frontend vs. backend code
- Specialized rules for test files
- Language-specific guidelines in a monorepo
- Framework-specific patterns for different modules

## File Format

```markdown
---
name: 'API Route Guidelines'
description: 'Conventions for REST API endpoints'
applyTo: 'src/api/**/*'
---

[Instructions in markdown]
```

## Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `applyTo` | Yes | Glob pattern for automatic application |
| `name` | No | Display name (defaults to filename) |
| `description` | No | Description shown on hover in Chat view |

## Pattern Matching with `applyTo`

Use glob patterns to target specific files:

| Pattern | Matches |
|---------|---------|
| `src/api/**/*` | All files under src/api/ |
| `**/*.test.ts` | All TypeScript test files |
| `src/components/**/*.tsx` | React components in src/components |
| `*.config.{js,ts}` | Config files in root |

## Difference from Always-On Instructions

| Aspect | Always-On | File-Based |
|--------|-----------|------------|
| When loaded | Every session | When pattern matches |
| Scope | Entire codebase | Specific file patterns |
| Location | `.github/copilot-instructions.md` | `.github/instructions/*.instructions.md` |

## Example: React Components

```markdown
---
name: 'React Component Standards'
applyTo: 'src/components/**/*.tsx'
---

## Component Structure
- Use functional components only
- Props interface named `{ComponentName}Props`
- Export component and props type

## Required Patterns
- Forward refs for DOM-exposing components
- Use CVA for variant styling
- Include displayName for debugging
```

## Example: Test Files

```markdown
---
name: 'Testing Standards'
applyTo: '**/*.test.{ts,tsx},**/*.spec.{ts,tsx}'
---

## Test Structure
- Use describe() for grouping
- Use it() not test()
- Follow Arrange-Act-Assert pattern

## Naming Conventions
- Describe blocks: noun (the thing being tested)
- It blocks: "should [expected behavior] when [condition]"
```

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Better Approach |
|--------------|---------|-----------------|
| Overlapping patterns | Multiple instructions conflict | Use specific, non-overlapping patterns |
| Duplicating always-on rules | Redundancy | Only include area-specific additions |
| Too broad patterns | Unexpected application | Use precise glob patterns |
