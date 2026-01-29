# File-Based Instructions

[← Always-On Instructions](part-2-1-always-on-instructions.md) | [Part II Overview](part-2-primitives.md)

---

### Overview

File-based instructions activate through pattern matching using glob patterns in the `applyTo` frontmatter field. This allows teams to define area-specific rules that only apply when working in relevant contexts.

**Location:** `.github/instructions/*.instructions.md`

**Use Cases:**
- Different conventions for frontend vs. backend code
- Specialized rules for test files
- Language-specific guidelines in a monorepo
- Framework-specific patterns for different modules

### File Format

File-based instructions use the `.instructions.md` extension with YAML frontmatter:

| Field | Description |
|-------|-------------|
| `description` | Description shown on hover in Chat view |
| `name` | Display name (defaults to filename) |
| `applyTo` | Glob pattern for automatic application |

### Example

**File:** `.github/instructions/api-routes.instructions.md`

``````markdown
---
name: 'API Route Guidelines'
description: 'Conventions for REST API endpoints'
applyTo: 'src/api/**/*'
---

# Instructions for API Routes

## API-Specific Rules
- All routes must include authentication middleware
- Use standardized error response format
- Include OpenAPI documentation comments
- Rate limiting is required for public endpoints

## Response Format
All API responses must use this shape:
```typescript
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: { code: string; message: string; };
}
```

## Error Handling
```typescript
// ✅ Good: Standardized error response
return NextResponse.json({ 
  success: false, 
  error: { code: 'NOT_FOUND', message: 'Resource not found' }
}, { status: 404 });

// ❌ Bad: Inconsistent error format
return NextResponse.json({ error: 'Not found' }, { status: 404 });
```
``````

### Creating File-Based Instructions

1. In the Chat view, click the **gear icon** (Configure Chat)
2. Select **Chat Instructions** > **New instruction file**
3. Choose **Workspace** to store in `.github/instructions/`
4. Use the agent to generate context-specific rules

Alternatively, ask the agent directly:

> **💬 Try this prompt:**
>
> *Create a file-based instruction at .github/instructions/react-components.instructions.md that applies to src/components/\*\*/\* and includes our React component conventions. Analyze existing components for patterns to document.*

### Anti-Patterns to Avoid

| Anti-Pattern | Why It's Problematic | Better Approach |
|--------------|---------------------|------------------|
| **Overlapping applyTo patterns** | Multiple instructions may conflict | Use specific, non-overlapping patterns |
| **Duplicating always-on rules** | Redundancy, potential conflicts | Only include area-specific additions |
| **Too broad patterns** | Instructions apply unexpectedly | Use precise glob patterns |
| **No description** | Hard to understand what rules apply | Add clear description in frontmatter |

File-based instructions complement always-on instructions by providing contextual specialization.

---

[← Always-On Instructions](part-2-1-always-on-instructions.md) | [Next: Prompt Files →](part-2-3-prompts.md)
