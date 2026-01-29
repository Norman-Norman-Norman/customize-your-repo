---
name: 'Always-On Instructions Documentation'
description: 'Guidelines for documenting the main copilot-instructions.md file'
applyTo: '**/copilot-instructions.md'
---

# Always-On Instructions Documentation Guidelines

## What Are Always-On Instructions?

Always-on instructions represent the foundational layer of Copilot customization. These instructions load automatically at the start of every Copilot session and apply to all interactions within the repository.

**Location:** `.github/copilot-instructions.md`

## When to Use Always-On Instructions

- Coding style and conventions that apply universally
- Technology stack declarations and preferences
- Security requirements and guardrails
- Architectural patterns to follow or avoid
- Documentation standards
- Error handling approaches
- Deprecated patterns to avoid

## Anatomy of Effective Instructions

A well-structured instructions file includes:

```markdown
# Copilot Instructions for [Project Name]

## Tech Stack
[Technologies, frameworks, versions]

## Code Style
[Naming, formatting, patterns]

## File Organization
[Directory structure, conventions]

## Security
[Security requirements, guardrails]

## What NOT to Do
[Anti-patterns, deprecated libraries]
```

## Including Rationale (Appraisals)

Include the "why" behind rules for better edge case decisions:

```markdown
## Why TypeScript Strict Mode?
We had three production bugs in Q2 2024 that would have been 
caught by strict null checks. The 30-minute investment per 
feature saves hours of debugging.
```

## The "Good vs Bad" Pattern

Copilot responds better to examples than abstract rules:

```markdown
## Data Transformation

✅ **Preferred:**
```typescript
const activeUsers = users.filter(u => u.isActive).map(u => u.name);
```

❌ **Avoid:**
```typescript
let activeUsers = [];
for (let i = 0; i < users.length; i++) {
  if (users[i].isActive) activeUsers.push(users[i].name);
}
```

**Why:** Declarative patterns are easier to read.
```

## Word Count Guidelines

Target **500-2000 words**. Focus on:
- Non-obvious patterns and conventions
- Common mistakes Copilot makes
- Requirements that cause PR friction

If exceeding 3000 words, consider:
- Moving details to file-based instructions
- Creating skills for specialized capabilities
- Linking to external documentation

## Gathering Team Knowledge

Surface valuable rules by asking:
1. "What does Copilot frequently get wrong?"
2. "What feedback appears repeatedly in PR reviews?"
3. "What would a new hire need to know on day one?"
4. "What libraries or patterns have been deprecated?"

## Validation

To verify instructions are working:
1. Open Copilot Chat
2. Ask: "What are the coding standards for this project?"
3. Verify response references your instructions
4. Generate sample code and check it follows rules

If not applied, verify:
- File is named exactly `copilot-instructions.md`
- File is in `.github/` folder
- VS Code window has been reloaded
