# Always-On Instructions

[← Part I: Foundations](part-1-foundations.md) | [Part II Overview](part-2-primitives.md)

*Published: February 5, 2026. This guide serves as a primer for GitHub Copilot customization. File paths, configuration options, and feature availability may change as Copilot evolves—always verify against the [official documentation](https://code.visualstudio.com/docs/copilot).*

---

## Overview

Always-on instructions (also known as the **Copilot Instructions File**) represent the foundational layer of Copilot customization. These instructions load automatically at the start of every Copilot session and apply to all interactions within the repository.

**Location:** `.github/copilot-instructions.md` or `.github/AGENTS.md` [*](https://code.visualstudio.com/docs/copilot)

> **New in VS Code 1.109:** Both `copilot-instructions.md` and `AGENTS.md` are now recognized as workspace instruction files. The `/init` command can discover and update either format.

**Required Setting:** `github.copilot.chat.codeGeneration.useInstructionFiles` must be enabled in VS Code settings for the instructions file to be applied. [*](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)

When this file exists in a repository and the setting is enabled, Copilot reads and applies its contents as persistent context. Chat responses and code generation will respect these guidelines without requiring explicit invocation.

**Note:** Custom instructions are *not* applied to inline suggestions (ghost text) as you type in the editor—they only affect Copilot Chat interactions.

## When to Use Always-On Instructions

- Coding style and conventions that apply universally
- Technology stack declarations and preferences
- Security requirements and guardrails
- Architectural patterns to follow or avoid
- Documentation standards
- Error handling approaches
- Deprecated patterns to avoid

## Anatomy of Effective Instructions

A well-structured instructions file typically includes the following sections:

```markdown
# Copilot Instructions for [Your Project Name]

## Tech Stack
- TypeScript (strict mode, always)
- React 18 with functional components only
- TanStack Query for data fetching
- Zod for validation
- Tailwind CSS for styling

## Code Style
- Use named exports, not default exports
- Prefer `const` over `let`; never use `var`
- All functions should have explicit return types
- Use early returns to reduce nesting
- Maximum function length: 50 lines

## File Organization
- Co-locate tests with source files (`Component.tsx` + `Component.test.tsx`)
- Use barrel exports (`index.ts`) for public APIs only
- Keep components under 200 lines; extract hooks if larger

## Security
- Never commit secrets or API keys
- Always sanitize user input
- Use parameterized queries for any database operations
- Validate all external data with Zod schemas

## What NOT to Do
- Don't use `any` type (use `unknown` and narrow)
- Don't use class components
- Don't use moment.js (use date-fns instead)
- Don't write inline CSS
```

## Including Rationale (Appraisals)

Instructions files become more effective when they include the reasoning behind rules. This context helps Copilot make better decisions in edge cases:

```markdown
## Why We Made These Choices

### Why TypeScript Strict Mode?
We had three production bugs in Q2 2024 that would have been caught by strict 
null checks. The 30-minute investment per feature to properly type things 
saves us hours of debugging.

### Why No Class Components?
Our team benchmarks showed functional components with hooks reduced our bundle 
size by 15% and made testing significantly easier.
```

This helps Copilot understand not just *what* to do, but *why*—leading to better suggestions in edge cases.

## Complete Example: Production Next.js Project

``````markdown
# Project Guidelines for Copilot

## Project Overview
This is a B2B SaaS platform for inventory management. 
We prioritize reliability over cutting-edge features.

## Architecture
- Next.js 14 App Router
- PostgreSQL with Prisma ORM
- NextAuth.js for authentication
- Stripe for payments
- Deployed on Vercel

### Directory Structure
```
src/
+-- app/                    # Next.js App Router pages
—   +-- (auth)/            # Auth-required routes (grouped)
—   +-- (public)/          # Public routes
—   +-- api/               # API routes (webhooks only)
+-- components/
—   +-- ui/                # Shared UI primitives
—   +-- features/          # Feature-specific components
+-- lib/                   # Shared utilities
+-- server/
—   +-- actions/           # Server Actions
—   +-- db/                # Database queries (Prisma)
—   +-- services/          # Business logic
+-- types/                 # Shared TypeScript types
```

### Data Flow
1. Client components call Server Actions for mutations
2. Server Components fetch data directly via Prisma
3. TanStack Query for client-side cache invalidation
4. Never use Route Handlers except for webhooks

## Coding Standards

### TypeScript
- Enable all strict checks
- No `any` types except in test mocks
- Prefer interfaces over types for object shapes
- Use discriminated unions for complex state
- Export types from `types/` directory, not inline

### React Patterns
```typescript
// ? Good: Custom hook for data fetching
function useInventoryItems(warehouseId: string) {
  return useQuery({
    queryKey: ['inventory', warehouseId],
    queryFn: () => fetchInventory(warehouseId),
  });
}

// ? Bad: Fetching in useEffect
useEffect(() => {
  fetch('/api/inventory').then(/* ... */);
}, []);
```

### Server Actions
```typescript
// ? Good: Validated, authorized, audited
export async function updateInventory(input: UpdateInventoryInput) {
  const validated = updateInventorySchema.parse(input);
  const session = await auth();
  if (!session) throw new UnauthorizedError();
  
  await auditLog('inventory.update', { userId: session.user.id, ...validated });
  return db.inventory.update({ where: { id: validated.id }, data: validated });
}

// ? Bad: No validation, no auth check
export async function updateInventory(data: any) {
  return db.inventory.update({ data });
}
```

## Testing Requirements

### Test Structure
- Co-locate unit tests with source: `Component.tsx` → `Component.test.tsx`
- Integration tests in `__tests__/integration/`
- E2E tests in `e2e/` using Playwright

### Coverage Requirements
- Minimum 80% line coverage for `server/` directory
- All Server Actions must have integration tests
- All user-facing flows must have E2E tests

### Testing Patterns
```typescript
// ? Good: Descriptive test with clear arrange/act/assert
describe('updateInventory', () => {
  it('should update quantity and log audit event', async () => {
    // Arrange
    const item = await createTestInventoryItem({ quantity: 10 });
    
    // Act
    await updateInventory({ id: item.id, quantity: 15 });
    
    // Assert
    const updated = await db.inventory.findUnique({ where: { id: item.id } });
    expect(updated?.quantity).toBe(15);
    expect(await getAuditLogs('inventory.update')).toHaveLength(1);
  });
});

// ? Bad: Vague test name, no clear structure
test('inventory works', async () => {
  const result = await updateInventory({ id: '1', quantity: 5 });
  expect(result).toBeTruthy();
});
```

### Mocking Guidelines
- Use `vi.mock()` for external services only
- Never mock Prisma in integration tests (use test database)
- Reset mocks in `beforeEach`, not `afterEach`

## Error Handling

### Error Types
Use our custom error hierarchy:
- `AppError` - Base class for all application errors
- `ValidationError` - Invalid input (400)
- `UnauthorizedError` - Not authenticated (401)
- `ForbiddenError` - Not authorized (403)  
- `NotFoundError` - Resource doesn't exist (404)

### Error Pattern
```typescript
// ? Good: Specific error with context
if (!warehouse) {
  throw new NotFoundError('Warehouse', warehouseId);
}

// ? Bad: Generic error
if (!warehouse) {
  throw new Error('Not found');
}
```

## Security Guidelines

### Authentication
- All `/app/(auth)/` routes require session
- Use `auth()` from NextAuth, never roll your own
- Session checks happen in Server Components, not middleware

### Authorization
- All queries must include `organizationId` filter
- Use `assertCanAccess(resource, session)` helper
- Never trust client-provided organization IDs

### Data Validation
- Validate ALL external input with Zod schemas
- Schemas live in `lib/schemas/` 
- Reuse schemas between client and server

## Performance Guidelines

### Database
- Always use `select` to limit returned fields
- Use `include` sparingly (prefer separate queries)
- Add indexes for any field used in WHERE clauses

### React
- Use `React.memo()` only when profiler shows need
- Prefer Server Components for static content
- Use `loading.tsx` for Suspense boundaries

## Dependencies

### Approved Libraries
- Date handling: `date-fns` (NOT moment.js)
- Forms: `react-hook-form` + `zod`
- Styling: Tailwind CSS only
- Icons: `lucide-react`

### Deprecated (Do Not Use)
- `moment.js` - Use `date-fns` instead
- `axios` - Use native `fetch`
- `lodash` - Use native methods or `es-toolkit`
- Class components - Use functional components

## Git Conventions

### Branch Naming
- `feature/INV-123-add-bulk-import`
- `fix/INV-456-quantity-calculation`
- `chore/update-dependencies`

### Commit Messages
Follow Conventional Commits:
- `feat(inventory): add bulk import endpoint`
- `fix(auth): handle expired session redirect`
- `test(warehouse): add integration tests for transfer`
``````

For a product management approach to workspace instructions, see [product-brain](https://github.com/digitarald/product-brain) — a template that structures AI interactions around product requirements, user stories, and feature specifications.

---

## Creating an Instructions File

The recommended approach for creating instructions files is through VS Code's built-in interface combined with agent-assisted generation.

## Using the /init Command (Fastest)

The quickest way to generate instructions for your workspace:

1. Open Copilot Chat
2. Type `/init` in the chat input box
3. Review the generated `.github/copilot-instructions.md` file
4. Make any necessary edits and save

The `/init` command follows a structured workflow:

1. **Discovery** — Searches for existing AI conventions in your workspace (such as `copilot-instructions.md` or `AGENTS.md`)
2. **Analysis** — Examines your project structure and coding patterns
3. **Generation** — Creates comprehensive workspace instructions tailored to your project

> **New in VS Code 1.109:** The `/init` command is implemented as a contributed prompt file, meaning you can customize its behavior by modifying the underlying prompt in your workspace.

## Creating via the Configure Menu

1. In the Chat view, click the **gear icon** (Configure Chat)
2. Select **Generate Chat Instructions** to auto-generate, or
3. Select **Chat Instructions** > **New instruction file...** for manual creation
4. Select the storage location:
   - **Workspace:** `.github/` folder [*](https://code.visualstudio.com/docs/copilot) (shared with team via version control)
   - **User Profile:** Personal instructions across all workspaces
5. Author the instructions or let the agent generate initial content

## Agent-Driven Generation (Advanced)

Rather than manually writing instructions, let the agent analyze the repository and generate appropriate instructions:

> **💬 Try this prompt:**
>
> *Analyze this repository and create a .github/copilot-instructions.md file that:*
>
> *1. Documents the tech stack and architecture*
> *2. Identifies coding conventions from existing code*
> *3. Notes patterns that should be followed*
> *4. Lists patterns that should be avoided*
> *5. Includes testing requirements*
> *6. Adds rationale for each guideline*
>
> *Examine the package.json, existing source files, and any documentation to ensure accuracy. Format as a human-readable markdown file I can review and refine.*

This approach ensures:
- Instructions reflect actual codebase patterns
- Consistent formatting and structure
- Human-verifiable output for PR review
- No syntax errors or typos

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Problematic | Better Approach |
|--------------|---------------------|------------------|
| **Typing directly into the file** | Prone to errors, may miss patterns | Have the agent generate from codebase analysis |
| **Copy-pasting from other repos** | Doesn't reflect your actual conventions | Generate custom instructions for each repo |
| **Overly verbose instructions** | Consumes context window, dilutes important rules | Keep to 500-2000 words, focus on non-obvious rules |
| **No rationale** | Copilot can't make good edge case decisions | Include "why" for each guideline |
| **Conflicting rules** | Copilot produces inconsistent results | Have agent check for contradictions |
| **Never updating** | Instructions drift from actual practice | Review quarterly, use agent to refresh |

## Using Copilot Coding Agent (GitHub.com)

On GitHub.com, the Copilot coding agent can generate instructions for you:

1. Navigate to [github.com/copilot/agents](https://github.com/copilot/agents)
2. Select your repository from the dropdown
3. Use the prompt provided in GitHub's documentation to onboard the repository
4. Review the generated PR with the instructions file

This is particularly effective because the agent validates build commands and tests them before documenting.

## Gathering Team Knowledge

Effective instructions files encode team knowledge. Use these questions to surface the most valuable rules:

1. **"What does Copilot frequently get wrong?"** — These become explicit rules
2. **"What feedback appears repeatedly in PR reviews?"** — These become guidelines
3. **"What would a new hire need to know on day one?"** — This becomes context
4. **"What libraries or patterns have been deprecated?"** — This becomes the "avoid" list

> **Practical tip:** Review the last 10-20 PR comments from the team. Repeated feedback indicates rules that should be codified in the instructions file.

> **💬 Try this prompt:**
>
> *Analyze the last 20 merged pull requests in this repository. Look at the review comments and identify:*
>
> *1. Repeated feedback patterns (things reviewers keep asking for)*
> *2. Common mistakes that get flagged*
> *3. Style or convention corrections*
> *4. Architectural concerns raised multiple times*
>
> *Summarize these as candidate rules for our copilot-instructions.md file.*

## Use the "Good vs Bad" Pattern

Copilot responds more effectively to examples than to abstract rules. Instead of stating "prefer functional patterns," demonstrate the preference:

```markdown
## Data Transformation

? **Preferred:**
```typescript
const activeUsers = users
  .filter(user => user.isActive)
  .map(user => user.name);
```

? **Avoid:**
```typescript
let activeUsers = [];
for (let i = 0; i < users.length; i++) {
  if (users[i].isActive) {
    activeUsers.push(users[i].name);
  }
}
```

**Why:** Declarative patterns are easier to read and less error-prone. 
Our eslint config will flag the imperative version anyway.
```

## Include Rationale

Copilot performs better when it understands the reasoning behind rules. Including the "why" enables better decision-making in edge cases:

```markdown
## Error Handling

Always wrap external API calls in try/catch with specific error types.

**Why:** The monitoring service (Sentry) categorizes errors by type. 
Generic catches make debugging production issues significantly harder.
This was identified as a root cause during the Q3 2024 incident analysis.
```

When Copilot understands intent, it can apply rules more intelligently.

## Iterating with the Agent

After initial generation, refine the instructions file through conversation:

> **💬 Try this prompt:**
>
> *Review the .github/copilot-instructions.md file and:*
>
> *1. Check for any contradictions between rules*
> *2. Add examples for the most important conventions*
> *3. Include rationale where it's missing*
> *4. Remove any rules that are standard/obvious*
> *5. Ensure the tech stack section is accurate*
>
> *Make the changes and explain what you updated.*

This creates a feedback loop where the agent refines its own guidelines based on human review.

## Validating the Configuration

To verify the instructions file is working correctly:

1. Open Copilot Chat in VS Code
2. Ask: "What are the coding standards for this project?"
3. Copilot should reference content from the instructions file
4. Generate sample code and verify it follows the defined rules

If instructions are not being applied, verify:
- The `github.copilot.chat.codeGeneration.useInstructionFiles` setting is enabled
- File is named exactly `copilot-instructions.md` or `AGENTS.md`
- File is located in the `.github/` folder [*](https://code.visualstudio.com/docs/copilot)
- VS Code window has been reloaded after creating the file

**Using Diagnostics:** Right-click in the Chat view and select **Diagnostics** to see all loaded instruction files, custom agents, prompt files, and skills—along with any errors.

## Cross-Editor Compatibility

The `.github/copilot-instructions.md` file is recognized by GitHub Copilot across multiple environments:
- VS Code
- Visual Studio
- GitHub.com (Copilot Chat, coding agent, code review)

This means the same instructions file works whether your team uses different editors or interacts with Copilot on the web.

## Instruction Priority

When multiple types of custom instructions exist, Copilot applies them in this priority order:

1. **Personal instructions** (highest priority)
2. **Repository instructions** (`.github/copilot-instructions.md` or `AGENTS.md`)
3. **Organization instructions** (lowest priority)

All relevant instructions are provided to Copilot, but higher-priority instructions take precedence when conflicts occur.

## Organization-Wide Instructions

> **New in VS Code 1.109:** Organization-level custom instructions are now supported in VS Code.

If your GitHub organization has configured custom instructions for Copilot, they are automatically applied to your chat sessions, ensuring consistent guidance across your team. This feature is enabled by default.

**Setting:** `github.copilot.chat.organizationInstructions.enabled`

Set to `false` to disable organization instructions if you need to override them for a specific workspace.

Organization instructions are particularly valuable for:
- Enforcing company-wide coding standards
- Maintaining compliance requirements across repositories
- Sharing best practices without duplicating instructions in every repo

---

[← Part I: Foundations](part-1-foundations.md) | [Next: File-Based Instructions →](part-2-2-file-based-instructions.md)
