# Part III: Putting It All Together

[ Back to Guide](../ReadMe.md) | [ Part II](part-2-primitives.md)

---

## The Decision Matrix

The following guidelines help determine which primitive to use for a given requirement:

### Use Always-On Instructions When...
- Coding standards apply to ALL code in the repository
- Technology preferences need to be enforced consistently
- Security guidelines must always be active
- Architectural patterns should be followed or avoided
- Certain patterns need to be deprecated

**Summary:** Global rules that apply universally.

### Use Prompt Files When...
- A task is repeatable (scaffolding, documentation, etc.)
- The task requires variable input
- Workflows need to be shared across a team
- The task is specific and bounded

**Summary:** Reusable task automation.

### Use Custom Agents When...
- Copilot should adopt a specific expert persona
- Different contexts require different behaviors
- Consistent response styles are needed
- Specialized work requires focused expertise (security, architecture)

**Summary:** Behavioral and persona changes.

### Use MCP When...
- Real-time external data is required
- Copilot needs to take actions outside VS Code
- Integration with external services is needed
- Live system information is required

**Summary:** External capabilities and integrations.

### Decision Flow Chart

```
Is it a general rule for all code?
├── YES → Always-On Instructions
└── NO → Is it a repeatable task?
    ├── YES → Prompt File
    └── NO → Do you need a specific AI behavior?
        ├── YES → Custom Agent
        └── NO → Do you need external data/actions?
            ├── YES → MCP
            └── NO → Direct conversation
```

---

## Interaction and Layering

### How Primitives Stack

When Copilot processes a request, it combines context from multiple primitives. Understanding the stack helps predict behavior:

```
┌─────────────────────────────────────────┐
│           User's Message                │  ← Your question/request
├─────────────────────────────────────────┤
│     Custom Agent (if activated)         │  ← Overrides behavior
├─────────────────────────────────────────┤
│    Prompt Template (if invoked)         │  ← Task-specific instructions
├─────────────────────────────────────────┤
│   Skills (loaded by description match)  │  ← Specialized capabilities
├─────────────────────────────────────────┤
│  File-Based Instructions (if matched)   │  ← Area-specific rules
├─────────────────────────────────────────┤
│       Always-On Instructions            │  ← Foundation rules
├─────────────────────────────────────────┤
│    MCP Tools (available throughout)     │  ← External capabilities
└─────────────────────────────────────────┘
```

### Context Window Considerations

All loaded primitives consume tokens from the context window. When context overflow occurs:
- Instructions may be truncated
- Recent chat history may be dropped
- Copilot may "forget" earlier rules

**Budget Guidelines:**
| Primitive | Target Length | Priority |
|-----------|---------------|----------|
| Always-on Instructions | 500-2000 words | High — always loaded |
| File-based Instructions | 200-500 words each | Medium — pattern-specific |
| Skills | 500-3000 words | Low — loaded on-demand |
| Custom Agents | 200-1000 words | High — shapes all responses |

### Priority and Conflict Resolution

When primitives contain conflicting guidance, later-loaded context takes precedence:

1. **Always-on instructions** (base layer)
2. **File-based instructions** (override for matched patterns)
3. **Custom agent** (session-wide override)
4. **Prompt** (single-request override)

**Example conflict:**
- Instructions say: "Use Jest for testing"
- File-based instruction for `*.spec.ts` says: "Use Vitest for testing"
- **Result:** Vitest wins when working on spec files

### From Global to Specific

Design your customizations to flow from general to specific:

| Level | What Goes Here | Example |
|-------|---------------|---------|
| **Always-on** | Universal rules | "Use TypeScript strict mode" |
| **File-based** | Pattern-specific | "React components use JSX syntax" |
| **Skill** | Specialized capability | "How to deploy to production" |
| **Agent** | Session behavior | "Act as a security reviewer" |
| **Prompt** | Single task | "Generate tests for this function" |

This layering means you rarely need to repeat rules. The foundation applies everywhere; specializations build on top.

### Combining Multiple Primitives

Complex workflows often combine several primitives:

**Example: Security-Focused PR Review**
1. **Always-on instructions** — coding standards, error handling patterns
2. **File-based instructions** — API-specific security rules for `api/**`
3. **Security Reviewer agent** — vulnerability-focused persona
4. **PR Review prompt** — structured review process

The result: a security-focused review that follows team conventions and produces consistent structured output.

---

## Frequently Asked Questions

### When should instructions be used vs. inline prompts?

**Instructions:** Rules that should ALWAYS apply
**Inline Prompts:** Context specific to the current conversation

If the same context is being copy-pasted into every Copilot conversation, it belongs in the instructions file.

### Do MCP capabilities replace the need for instructions?

No. These primitives are complementary:
- Instructions = What Copilot KNOWS
- MCP = What Copilot CAN DO

An instruction might state "always use our inventory API at `/api/inventory`" while an MCP server enables Copilot to actually query that API.

### What is the difference between prompts and custom agents?

- **Prompt** = A specific task to execute ("Generate a component called X")
- **Custom Agent** = A persona/behavior for the entire conversation ("Act as a security reviewer")

These can be combined: activate a custom agent, then run a prompt. The persona affects how the task is executed.

### Can multiple instructions files exist?

Copilot looks for `.github/copilot-instructions.md` at the repository level. Organization-level instructions can be set at the org/enterprise level and will merge with repository-level instructions.

### Do prompts and custom agents work together?

Yes. A custom agent sets the behavioral context and a prompt provides specific tasks:
1. Activate "Security Reviewer" custom agent
2. Run "Review PR" prompt
3. Receive security-focused PR feedback

### How are customizations shared with a team?

Commit the `.github/` folder to version control. When pushed to the main branch, all team members automatically receive the same customizations.

### When should VS Code settings be used vs. repository files?

| Location | Use Case |
|----------|----------|
| Repo `.github/` | Team-wide, project-specific |
| VS Code User Settings | Personal preferences across all projects |
| VS Code Workspace Settings | Project-specific, not for team sharing |

### How do instructions interact with selected code?

When code is selected and a question is asked, Copilot:
1. Analyzes the selection as primary context
2. Applies instructions as guidelines
3. Executes prompts if invoked

Instructions function as a "lens" through which Copilot interprets the selected code.

### Does Copilot automatically read the instructions file?

Yes. If `.github/copilot-instructions.md` exists in a repository, Copilot automatically reads and applies it. No additional configuration is required.

### What is the ideal length for an instructions file?

Target **500-2000 words**. This provides comprehensive coverage without overwhelming the context window. Focus on:
- Non-obvious patterns and conventions
- Common mistakes Copilot makes
- Requirements that frequently cause PR friction

### How specific should prompts be?

Prompts should be specific enough to produce consistent results while remaining flexible enough for reuse. Use variables (`{{variableName}}`) for dynamic elements.

---

## Real-World Examples

### Example 1: A React Component Library

**Instructions (.github/copilot-instructions.md):**
```markdown
# Component Library Guidelines

## Design System
We extend Radix UI primitives. Never build accessibility from scratch.

## Required for Every Component
- TypeScript props interface extending `ComponentPropsWithoutRef`
- Forwarded refs
- Compound components pattern for complex ones
- CVA for variant styling
- Full ARIA support

## Naming Conventions
- Component files: PascalCase.tsx
- Props: ComponentNameProps
- Ref: ComponentNameRef
- Context: ComponentNameContext

## Example Pattern
```tsx
const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, ...props }, ref) => {
    return (
      <button
        className={cn(buttonVariants({ variant, size }), className)}
        ref={ref}
        {...props}
      />
    )
  }
)
Button.displayName = "Button"
```
```

**Component Generator Prompt (.github/prompts/new-primitive.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create a new Radix-based component'
model: 'Claude Sonnet 4'
---

Create a new component `{{componentName}}` that extends Radix UI's `{{radixPrimitive}}`:

1. Create `{{componentName}}.tsx` with compound components pattern
2. Create `{{componentName}}.test.tsx` with accessibility tests
3. Create `{{componentName}}.stories.tsx` with all variants
4. Export from components index

Follow the patterns in our existing Button and Dialog components.
```

### Example 2: A Full-Stack SaaS Application

**Instructions (.github/copilot-instructions.md):**
```markdown
# Platform Development Guidelines

## Architecture
- Next.js 14 App Router
- PostgreSQL with Prisma
- NextAuth v5 for auth
- Stripe for billing
- Vercel deployment

## Folder Structure
```
src/
├── app/           # Next.js App Router
├── components/    # React components
├── lib/           # Utility functions
├── server/        # Server-only code
│   ├── db/        # Database queries
│   └── services/  # Business logic
└── types/         # TypeScript types
```

## Data Fetching Rules
- Use Server Components for initial data
- Use TanStack Query for client-side mutations
- Never fetch in useEffect

## API Design
- Server Actions for mutations
- Route Handlers for webhooks only
- tRPC for complex client-server communication

## Multi-tenancy
- All queries must include `organizationId` filter
- Use `getServerSession()` for auth context
- Never expose data across orgs
```

**API Generator Prompt (.github/prompts/new-server-action.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create a new server action with proper auth and validation'
model: 'Claude Sonnet 4'
---

Create a new server action `{{actionName}}` in `src/server/actions/{{domain}}.ts`:

## Implementation
1. Add Zod input schema
2. Wrap with `authAction()` for auth check
3. Include organization context
4. Add audit logging
5. Handle errors with our AppError pattern

## Related Updates
- Add types to `src/types/{{domain}}.ts`
- Add to actions index
- Create test in `__tests__/actions/{{actionName}}.test.ts`
```

### Example 3: A Python Data Pipeline

**Instructions (.github/copilot-instructions.md):**
```markdown
# Data Pipeline Standards

## Python Version
3.11+ with strict type hints

## Core Libraries
- pandas 2.x for data manipulation
- polars for large datasets (>1GB)
- pydantic for data validation
- pytest for testing
- loguru for logging

## Code Style
- Google-style docstrings
- Black formatting (line length 88)
- isort for imports
- mypy strict mode

## Error Handling
```python
from pipeline.errors import PipelineError, DataValidationError

def process_data(df: pd.DataFrame) -> pd.DataFrame:
    try:
        # processing logic
    except ValueError as e:
        raise DataValidationError(f"Invalid data format: {e}") from e
```

## Testing Requirements
- 90% coverage minimum
- Fixtures for all test data
- Parametrized tests for edge cases
- Integration tests for full pipeline runs
```

---



---

[ Part II: The Six Primitives](part-2-primitives.md) | [Part IV: Workflow Recipes ](part-4-workflow-recipes.md)
