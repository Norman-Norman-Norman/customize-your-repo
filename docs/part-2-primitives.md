# Part II: The Six Primitives

[ Back to Guide](../ReadMe.md) | [ Part I](part-1-foundations.md)

---

## The Customization Primitives

GitHub Copilot provides six distinct customization primitives. Each serves a specific purpose and loads at different points in the interaction lifecycle. Understanding when each primitive activates is essential for effective configuration.

| Primitive | Location | Loading | Best For |
|-----------|----------|---------|----------|
| [**Always-on Instructions**](#always-on-instructions) | `.github/copilot-instructions.md` | Every session | Codebase guardrails |
| [**File-based Instructions**](#file-based-instructions) | `.github/instructions/*.instructions.md` | Pattern match / description match | Area-specific rules |
| [**Prompts (Slash Commands)**](#prompt-files-slash-commands) | `.github/prompts/*.prompt.md` | User invokes | One-shot workflows |
| [**Skills**](#skills) | `.github/skills/` | Description match â†’ on-demand | Reusable capabilities |
| [**Custom Agents**](#custom-agents) | `.github/agents/*.agent.md` | Top-level OR as subagent | Constrained workflows |
| [**MCP**](#mcp-model-context-protocol) | `.vscode/mcp.json` | Session start | External gateways |

This table represents the complete customization surface area. Each primitive addresses a distinct need:

- **Always-on Instructions** â€“ Global rules that apply to every Copilot interaction in the repository
- **File-based Instructions** â€“ Targeted rules that activate based on file patterns or context
- **Prompts (Slash Commands)** â€“ Reusable task templates invoked explicitly by users
- **Skills** â€“ Discrete capabilities that Copilot can invoke when relevant
- **Custom Agents** â€“ Specialized AI personas with constrained tool access and behaviors
- **MCP (Model Context Protocol)** â€“ External service integrations that extend Copilot's capabilities

The following sections examine each primitive in detail, including implementation guidance and practical examples.

---

## Always-On Instructions

### Overview

Always-on instructions (also known as the **Copilot Instructions File**) represent the foundational layer of Copilot customization. These instructions load automatically at the start of every Copilot session and apply to all interactions within the repository.

**Location:** `.github/copilot-instructions.md`

When this file exists in a repository, Copilot reads and applies its contents as persistent context. Every code suggestion, chat response, and generated output will respect these guidelines without requiring explicit invocation.

### When to Use Always-On Instructions

- Coding style and conventions that apply universally
- Technology stack declarations and preferences
- Security requirements and guardrails
- Architectural patterns to follow or avoid
- Documentation standards
- Error handling approaches
- Deprecated patterns to avoid

### Anatomy of Effective Instructions

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

### Including Rationale (Appraisals)

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

This helps Copilot understand not just *what* to do, but *why*â€”leading to better suggestions in edge cases.

### Complete Example: Production Next.js Project

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
â”œâ”€â”€ app/                    # Next.js App Router pages
â”‚   â”œâ”€â”€ (auth)/            # Auth-required routes (grouped)
â”‚   â”œâ”€â”€ (public)/          # Public routes
â”‚   â””â”€â”€ api/               # API routes (webhooks only)
â”œâ”€â”€ components/
â”‚   â”œâ”€â”€ ui/                # Shared UI primitives
â”‚   â””â”€â”€ features/          # Feature-specific components
â”œâ”€â”€ lib/                   # Shared utilities
â”œâ”€â”€ server/
â”‚   â”œâ”€â”€ actions/           # Server Actions
â”‚   â”œâ”€â”€ db/                # Database queries (Prisma)
â”‚   â””â”€â”€ services/          # Business logic
â””â”€â”€ types/                 # Shared TypeScript types
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
// âœ… Good: Custom hook for data fetching
function useInventoryItems(warehouseId: string) {
  return useQuery({
    queryKey: ['inventory', warehouseId],
    queryFn: () => fetchInventory(warehouseId),
  });
}

// âŒ Bad: Fetching in useEffect
useEffect(() => {
  fetch('/api/inventory').then(/* ... */);
}, []);
```

### Server Actions
```typescript
// âœ… Good: Validated, authorized, audited
export async function updateInventory(input: UpdateInventoryInput) {
  const validated = updateInventorySchema.parse(input);
  const session = await auth();
  if (!session) throw new UnauthorizedError();
  
  await auditLog('inventory.update', { userId: session.user.id, ...validated });
  return db.inventory.update({ where: { id: validated.id }, data: validated });
}

// âŒ Bad: No validation, no auth check
export async function updateInventory(data: any) {
  return db.inventory.update({ data });
}
```

## Testing Requirements

### Test Structure
- Co-locate unit tests with source: `Component.tsx` â†’ `Component.test.tsx`
- Integration tests in `__tests__/integration/`
- E2E tests in `e2e/` using Playwright

### Coverage Requirements
- Minimum 80% line coverage for `server/` directory
- All Server Actions must have integration tests
- All user-facing flows must have E2E tests

### Testing Patterns
```typescript
// âœ… Good: Descriptive test with clear arrange/act/assert
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

// âŒ Bad: Vague test name, no clear structure
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
// âœ… Good: Specific error with context
if (!warehouse) {
  throw new NotFoundError('Warehouse', warehouseId);
}

// âŒ Bad: Generic error
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

---

## Creating an Instructions File

The recommended approach for creating instructions files is through VS Code's built-in interface combined with agent-assisted generation.

### Creating via the Configure Menu (Recommended)

1. In the Chat view, click the **gear icon** (Configure Chat)
2. Select **Chat Instructions** > **New instruction file**
3. Choose the storage location:
   - **Workspace:** `.github/` folder (shared with team via version control)
   - **User Profile:** Personal instructions across all workspaces
4. Use the agent to generate the initial content

### Agent-Driven Generation (Advanced)

Rather than manually writing instructions, let the agent analyze the repository and generate appropriate instructions:

> **ðŸ’¬ Try this prompt:**
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

### Anti-Patterns to Avoid

| Anti-Pattern | Why It's Problematic | Better Approach |
|--------------|---------------------|------------------|
| **Typing directly into the file** | Prone to errors, may miss patterns | Have the agent generate from codebase analysis |
| **Copy-pasting from other repos** | Doesn't reflect your actual conventions | Generate custom instructions for each repo |
| **Overly verbose instructions** | Consumes context window, dilutes important rules | Keep to 500-2000 words, focus on non-obvious rules |
| **No rationale** | Copilot can't make good edge case decisions | Include "why" for each guideline |
| **Conflicting rules** | Copilot produces inconsistent results | Have agent check for contradictions |
| **Never updating** | Instructions drift from actual practice | Review quarterly, use agent to refresh |

### Using Copilot Coding Agent (GitHub.com)

On GitHub.com, the Copilot coding agent can generate instructions for you:

1. Navigate to [github.com/copilot/agents](https://github.com/copilot/agents)
2. Select your repository from the dropdown
3. Use the prompt provided in GitHub's documentation to onboard the repository
4. Review the generated PR with the instructions file

This is particularly effective because the agent validates build commands and tests them before documenting.

### Gathering Team Knowledge

Effective instructions files encode team knowledge. Use these questions to surface the most valuable rules:

1. **"What does Copilot frequently get wrong?"** â†’ These become explicit rules
2. **"What feedback appears repeatedly in PR reviews?"** â†’ These become guidelines
3. **"What would a new hire need to know on day one?"** â†’ This becomes context
4. **"What libraries or patterns have been deprecated?"** â†’ This becomes the "avoid" list

> **Practical tip:** Review the last 10-20 PR comments from the team. Repeated feedback indicates rules that should be codified in the instructions file.

> **ðŸ’¬ Try this prompt:**
>
> *Analyze the last 20 merged pull requests in this repository. Look at the review comments and identify:*
>
> *1. Repeated feedback patterns (things reviewers keep asking for)*
> *2. Common mistakes that get flagged*
> *3. Style or convention corrections*
> *4. Architectural concerns raised multiple times*
>
> *Summarize these as candidate rules for our copilot-instructions.md file.*

### Use the "Good vs Bad" Pattern

Copilot responds more effectively to examples than to abstract rules. Instead of stating "prefer functional patterns," demonstrate the preference:

```markdown
## Data Transformation

âœ… **Preferred:**
```typescript
const activeUsers = users
  .filter(user => user.isActive)
  .map(user => user.name);
```

âŒ **Avoid:**
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

### Include Rationale

Copilot performs better when it understands the reasoning behind rules. Including the "why" enables better decision-making in edge cases:

```markdown
## Error Handling

Always wrap external API calls in try/catch with specific error types.

**Why:** The monitoring service (Sentry) categorizes errors by type. 
Generic catches make debugging production issues significantly harder.
This was identified as a root cause during the Q3 2024 incident analysis.
```

When Copilot understands intent, it can apply rules more intelligently.

### Iterating with the Agent

After initial generation, refine the instructions file through conversation:

> **ðŸ’¬ Try this prompt:**
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

### Validating the Configuration

To verify the instructions file is working correctly:

1. Open Copilot Chat in VS Code
2. Ask: "What are the coding standards for this project?"
3. Copilot should reference content from the instructions file
4. Generate sample code and verify it follows the defined rules

If instructions are not being applied, verify:
- File is named exactly `copilot-instructions.md`
- File is located in the `.github/` folder
- VS Code window has been reloaded after creating the file

---

## File-Based Instructions

While always-on instructions apply globally, file-based instructions provide targeted rules for specific areas of a codebase. The markdown content follows the same format as [Always-On Instructions](#always-on-instructions) â€” the key difference is the frontmatter that controls when they activate.

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
// âœ… Good: Standardized error response
return NextResponse.json({ 
  success: false, 
  error: { code: 'NOT_FOUND', message: 'Resource not found' }
}, { status: 404 });

// âŒ Bad: Inconsistent error format
return NextResponse.json({ error: 'Not found' }, { status: 404 });
```
``````

### Creating File-Based Instructions

1. In the Chat view, click the **gear icon** (Configure Chat)
2. Select **Chat Instructions** > **New instruction file**
3. Choose **Workspace** to store in `.github/instructions/`
4. Use the agent to generate context-specific rules

Alternatively, ask the agent directly:

> **ðŸ’¬ Try this prompt:**
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

## Prompt Files (Slash Commands)

### Overview

Prompt files enable reusable task templates that can be invoked on demand. They function as macros for common workflows, reducing repetitive typing and ensuring consistent outputs.

**Location:** `.github/prompts/*.prompt.md`

Users invoke prompts by typing `/` in Copilot Chat and selecting from available options.

### File Format

Prompt files use the `.prompt.md` extension and support these frontmatter fields:

| Field | Description |
|-------|-------------|
| `name` | Display name shown when typing `/` in chat |
| `description` | Brief description of what the prompt does |
| `agent` | Execution mode: `ask`, `edit`, `agent`, or a custom agent name |
| `model` | AI model to use (e.g., `Claude Sonnet 4`, `GPT-4o`) |
| `tools` | Specific tools available for this prompt |
| `argument-hint` | Hint text for user interaction |

```markdown
---
agent: 'agent'
description: 'Generate a new React component with tests'
model: 'Claude Sonnet 4'
tools: ['editFiles', 'createFile', 'terminalCommand']
---

Create a new React component called `{{componentName}}` that:

1. Is a functional component with TypeScript
2. Uses our standard Props interface pattern
3. Includes comprehensive JSDoc documentation
4. Has a co-located test file with these test cases:
   - Renders without crashing
   - Displays expected content
   - Handles edge cases (empty props, missing data)

Use our existing components in `src/components/` as reference for style.
```

### Execution Modes

The `agent` field in the frontmatter determines how Copilot executes the prompt:

| Mode | What It Does | Best For |
|------|--------------|----------|
| `ask` | Read-only â€” responds conversationally, no file changes | Questions, explanations, brainstorming, code review |
| `agent` | Takes autonomous action â€” creates/edits files, runs commands | Multi-file changes, scaffolding, bug fixes, any task that modifies code |

**Note:** An `edit` mode exists but is not recommended. Use `agent` for any task that requires modifying files.

### Essential Prompt Files Every Repo Needs

The following prompt templates address common development workflows:

#### 1. Component Generator
**File:** `.github/prompts/new-component.prompt.md`

```markdown
---
agent: 'agent'
description: 'Scaffold a new React component with all the trimmings'
model: 'Claude Sonnet 4'
---

Create a new component called `{{componentName}}` in `src/components/{{componentName}}/`:

## Required Files
1. `{{componentName}}.tsx` - The component
2. `{{componentName}}.test.tsx` - Unit tests  
3. `{{componentName}}.stories.tsx` - Storybook stories
4. `index.ts` - Barrel export

## Component Requirements
- TypeScript with explicit prop types
- Proper accessibility attributes
- Loading and error states
- Mobile-responsive design

## Reference
Look at existing components for patterns. Follow the style guide in copilot-instructions.md.
```

#### 2. API Route Generator
**File:** `.github/prompts/new-api-route.prompt.md`

```markdown
---
agent: 'agent'
description: 'Create a new API endpoint with validation and error handling'
model: 'Claude Sonnet 4'
---

Create a new API route at `src/app/api/{{resourceName}}/route.ts`:

## Requirements
- Implement {{httpMethods}} methods
- Use Zod schema for request validation
- Include proper error handling with our ApiResponse type
- Add rate limiting check
- Log all requests to monitoring

## Include
- Request/response type definitions
- Validation schemas
- Integration tests in `__tests__/api/{{resourceName}}.test.ts`
```

#### 3. Bug Fix Assistant
**File:** `.github/prompts/fix-bug.prompt.md`

```markdown
---
agent: 'agent'
description: 'Analyze and fix a bug with explanation'
model: 'Claude Sonnet 4'
---

Analyze the selected code and fix the bug described below:

**Bug Description:** {{bugDescription}}
**Expected Behavior:** {{expectedBehavior}}
**Current Behavior:** {{actualBehavior}}

## Your Response Should Include:
1. Root cause analysis
2. The fix with explanation
3. Any related issues this might cause
4. Suggested test to prevent regression
```

#### 4. Code Review Prep
**File:** `.github/prompts/review-prep.prompt.md`

```markdown
---
agent: 'ask'
description: 'Prepare code for review'
model: 'GPT-4o'
---

Review the selected code and provide:

1. **Potential Issues**
   - Bugs or logic errors
   - Performance concerns
   - Security vulnerabilities

2. **Style/Convention Violations**
   - Based on our copilot-instructions.md
   - Naming conventions
   - Code organization

3. **Improvement Suggestions**
   - Readability
   - Maintainability
   - Test coverage

4. **Questions for the Author**
   - Unclear design decisions
   - Missing documentation
```

#### 5. Documentation Generator
**File:** `.github/prompts/generate-docs.prompt.md`

```markdown
---
agent: 'agent'
description: 'Generate comprehensive documentation'
model: 'Claude Sonnet 4'
---

Add documentation to the selected code including:

- JSDoc comments for all exports
- Inline comments for complex logic
- Usage examples
- Parameter descriptions with types
- Return value documentation
- @throws documentation for error cases
- @example sections with realistic use cases

Match the documentation style of our existing codebase.
```

### Using Prompt Files

In VS Code, type `/` in Copilot Chat to view available prompts. Select a prompt, provide values for any variables, and Copilot executes the defined workflow.

---

## How to Create Effective Prompts

This section covers the process of creating well-structured prompt files using VS Code's built-in tools and agent-driven iteration.

### Creating via the Configure Menu (Recommended)

1. In the Chat view, click the **gear icon** (Configure Chat)
2. Select **Prompt Files** > **New prompt file**
3. Choose the storage location:
   - **Workspace:** `.github/prompts/` folder (shared with team via version control)
   - **User Profile:** Personal prompts across all workspaces
4. Provide a name for the prompt
5. Use the agent to generate and refine the prompt content

### Agent-Driven Prompt Creation (Best Practice)

Rather than manually writing prompt files, use Copilot to generate them:

> **ðŸ’¬ Try this prompt:**
>
> *Create a prompt file at .github/prompts/new-api-route.prompt.md that:*
> *- Generates REST API routes with validation*
> *- Uses agent mode with Claude Sonnet 4*
> *- Includes variables for resource name and HTTP methods*
> *- References our copilot-instructions.md for patterns*
> *- Outputs route file, Zod schemas, and tests*

This approach ensures:
- Correct YAML frontmatter syntax
- Consistent variable naming
- Human-verifiable output for PR review

### Prompt File Anatomy

Every prompt file consists of two parts:

```markdown
---
agent: 'agent'                          # Execution mode or custom agent
description: 'What this prompt does'    # Appears in the / menu
model: 'Claude Sonnet 4'                # Optional: specific model
tools: ['editFiles', 'createFile']      # Optional: restrict tools
---

[Prompt instructions]
```

### Anti-Patterns to Avoid

| Anti-Pattern | Why It's Problematic | Better Approach |
|--------------|---------------------|------------------|
| **Typing directly into .prompt.md files** | Syntax errors, inconsistent formatting | Use gear icon or ask agent to generate |
| **Vague instructions** | "Make me a component" produces inconsistent results | Be specific about requirements, structure, patterns |
| **Not using variables** | Prompt can only do one specific thing | Use `{{variableName}}` for reusable parts |
| **Using edit mode** | Less reliable than agent mode | Use `ask` for read-only, `agent` for any changes |
| **No model specification** | Inconsistent results across sessions | Specify model for reproducibility |
| **No reference to instructions** | Prompt ignores team conventions | Reference copilot-instructions.md explicitly |

### Mode Reference

| Mode | Copilot Can... | Best For |
|------|----------------|----------|
| `ask` | Talk back, explain, suggest (read-only) | Design discussions, Q&A, brainstorming, code review |
| `agent` | Create files, edit files, run commands | Any task that modifies code â€” scaffolding, bug fixes, refactoring |
| Custom agent | Use that agent's persona and tools | Specialized workflows with defined behavior |

**Note:** An `edit` mode exists but is not recommended. Use `agent` instead for any file modifications.

### Implement Variables

Use `{{variableName}}` syntax to create parameterized prompts:

```markdown
---
agent: 'agent'
description: 'Create a new {{frameworkType}} hook'
model: 'Claude Sonnet 4'
---

Create a custom hook called `use{{hookName}}` that:
- Handles {{primaryResponsibility}}
- Returns {{returnShape}}
- Follows our hook patterns in `src/hooks/`
```

When invoked, Copilot prompts for values for each variable:
- `frameworkType` (React, Vue, Solid, etc.)
- `hookName` (the hook name)
- `primaryResponsibility` (what it handles)
- `returnShape` (return type structure)

### Reference Instructions Files

Prompts can reference the instructions file to maintain consistency:

```markdown
---
agent: 'agent'
description: 'Generate component following our standards'
model: 'Claude Sonnet 4'
---

Create a new component following the patterns defined in our 
copilot-instructions.md, specifically:
- The component structure we use
- Our prop typing conventions  
- Our testing requirements

...
```

This approach keeps prompts synchronized with team standards automatically.

### Meta-Prompt for Creating Prompts

Use the agent directly to generate new prompt files:

> **ðŸ’¬ Try this prompt:**
>
> *Create a new prompt file at `.github/prompts/{{promptName}}.prompt.md`.*
>
> *Prompt Requirements:*
> *- Purpose: {{purposeDescription}}*
> *- Mode: `ask` (read-only) or `agent` (makes changes)*
> *- Model: Claude Sonnet 4 (or specify)*
>
> *Prompt Structure Guidelines:*
> *1. Start with a clear, specific instruction*
> *2. Include context about what files/patterns to reference*
> *3. Specify the expected output format*
> *4. Add relevant constraints from our copilot-instructions.md*
> *5. Include quality checks or validation steps*
>
> *The prompt should:*
> *- Be specific enough to get consistent results*
> *- Be general enough to be reusable*
> *- Include `{{variables}}` for customizable parts*
> *- Reference existing codebase patterns when relevant*
> *- Include success criteria so Copilot knows when it's "done"*
>
> *Format the output as:*
> *- YAML frontmatter with agent, description, and model*
> *- Clear markdown sections for instructions*
> *- Code examples where helpful*
> *- Numbered steps for complex tasks*

This meta-prompt creates new prompt files that follow best practices.

### Prompt Improvement via Agent

To improve an existing prompt file, ask the agent directly:

> **ðŸ’¬ Try this prompt:**
>
> *Analyze and improve the prompt file at .github/prompts/new-component.prompt.md:*
>
> *Check for:*
> *1. Clarity - Is the instruction unambiguous?*
> *2. Completeness - Does it include all necessary context?*
> *3. Variables - Are there opportunities for useful variables?*
> *4. Mode/Model - Are they appropriate for the task?*
> *5. Examples - Would examples improve output quality?*
> *6. Constraints - Are there missing guardrails?*
>
> *Apply these improvements:*
> *- Make instructions more specific*
> *- Add structure where it helps*
> *- Include success criteria*
> *- Reference codebase patterns where relevant*
> *- Add helpful examples*
>
> *Explain each change you make.*

### Prompt Quality Comparison

**Ineffective Prompt:**
```markdown
Make me a component.
```

**Effective Prompt:**
```markdown
Create a React component called `{{componentName}}` in `src/components/`:

1. Use TypeScript with our Props interface pattern
2. Include loading & error states
3. Follow accessibility guidelines (WCAG 2.1 AA)
4. Create co-located test file with:
   - Render test
   - User interaction tests
   - Edge case tests
5. Match patterns in existing components like Button and Card
```

Specificity produces consistent, high-quality outputs.

---

## Skills

### Overview

Skills represent discrete capabilities that Copilot can invoke when contextually relevant. Unlike prompts (which users invoke explicitly), skills activate automatically based on description matching.

**Location:** `.github/skills/`
**Loading:** Description match â†’ on-demand
**Best For:** Reusable capabilities across tools

### Agent Skills

**Agent Skills** is an open standard for teaching Copilot specialized capabilities through folders containing instructions, scripts, and resources. Unlike custom instructions that primarily define coding guidelines, skills focus on specialized workflows and capabilities.

**Key differences from other primitives:**
- Skills work across multiple AI agents (VS Code, GitHub Copilot CLI, GitHub Copilot coding agent)
- Portable via the open standard at [agentskills.io](https://agentskills.io)
- Include scripts and resources alongside instructions
- Loaded on-demand, not always in context

For full documentation and the specification, visit [agentskills.io](https://agentskills.io).

### Directory Structure

Every skill lives in its own folder with a `SKILL.md` file:

```
.github/
â””â”€â”€ skills/
    â”œâ”€â”€ image-manipulation/
    â”‚   â””â”€â”€ SKILL.md
    â”œâ”€â”€ github-issues/
    â”‚   â”œâ”€â”€ SKILL.md
    â”‚   â””â”€â”€ templates/
    â”‚       â”œâ”€â”€ bug-report.md
    â”‚       â””â”€â”€ feature-request.md
    â””â”€â”€ web-testing/
        â”œâ”€â”€ SKILL.md
        â””â”€â”€ scripts/
            â””â”€â”€ playwright-setup.sh
```

**Minimum structure:**
```
skill-name/
â””â”€â”€ SKILL.md          # Required: instructions + metadata
```

**Complex skill with resources:**
```
skill-name/
â”œâ”€â”€ SKILL.md          # Required: main instructions
â”œâ”€â”€ scripts/          # Optional: executable code (Python, Bash, JS)
â”œâ”€â”€ references/       # Optional: additional documentation
â””â”€â”€ assets/           # Optional: templates, images, data files
```

### SKILL.md Format

The `SKILL.md` file contains YAML frontmatter and Markdown instructions:

``````markdown
---
name: image-manipulation
description: Resize, convert, compress, and batch-process images using ImageMagick. Use when the user mentions image optimization, format conversion, thumbnail generation, or bulk image operations.
metadata:
  author: your-org
  version: "1.0"
---

# Image Manipulation with ImageMagick

## When to Use This Skill

Use this skill when:
- User wants to resize, crop, or convert images
- User mentions image optimization or compression
- User needs to batch process multiple images
- User asks about ImageMagick commands

## Prerequisites

ImageMagick must be installed:
- macOS: `brew install imagemagick`
- Ubuntu: `sudo apt install imagemagick`
- Windows: Download from imagemagick.org

## Instructions

### Resizing Images

To resize an image while maintaining aspect ratio:

```bash
magick input.jpg -resize 800x600 output.jpg
```

To resize to exact dimensions (may distort):

```bash
magick input.jpg -resize 800x600! output.jpg
```

### Batch Processing

Process all images in a directory:

```bash
for img in *.jpg; do
  magick "$img" -resize 50% "resized_$img"
done
```

### Format Conversion

Convert between formats:

```bash
magick input.png output.jpg
magick input.jpg -quality 85 output.webp
```

## Common Patterns

| Task | Command |
|------|---------|
| Resize to width | `magick in.jpg -resize 800x out.jpg` |
| Resize to height | `magick in.jpg -resize x600 out.jpg` |
| Create thumbnail | `magick in.jpg -thumbnail 150x150^ -gravity center -extent 150x150 thumb.jpg` |
| Compress JPEG | `magick in.jpg -quality 80 out.jpg` |
| Convert to WebP | `magick in.png out.webp` |

## Edge Cases

- **Animated GIFs**: Use `-coalesce` before processing
- **CMYK images**: Convert to RGB first with `-colorspace sRGB`
- **Large batches**: Process in chunks to avoid memory issues
``````

### Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | 1-64 chars, lowercase, hyphens only, must match directory name |
| `description` | Yes | 1-1024 chars, describe WHAT it does AND WHEN to use it |
| `metadata` | No | Key-value pairs (author, version, etc.) |
| `license` | No | License name or reference |
| `compatibility` | No | Environment requirements |

### Name Validation Rules

Skill names must follow strict rules:

âœ… **Valid:**
- `image-manipulation`
- `github-issues`
- `web-testing`

âŒ **Invalid:**
- `Image-Manipulation` (uppercase not allowed)
- `-image` (cannot start with hyphen)
- `image-` (cannot end with hyphen)
- `image--manipulation` (consecutive hyphens not allowed)

### Complete Example: GitHub Issues Skill

A skill that uses templates and integrates with the GitHub MCP:

**Directory structure:**
```
github-issues/
â”œâ”€â”€ SKILL.md
â””â”€â”€ templates/
    â”œâ”€â”€ bug-report.md
    â””â”€â”€ feature-request.md
```

**SKILL.md:**
``````markdown
---
name: github-issues
description: Create well-structured GitHub issues using team templates. Use when the user wants to file a bug, request a feature, or create any GitHub issue. Ensures consistent formatting and required fields.
metadata:
  author: your-org
  version: "1.0"
---

# GitHub Issue Creation

## When to Use This Skill

Use this skill when:
- User wants to create a GitHub issue
- User found a bug and wants to report it
- User wants to request a new feature
- User mentions "file an issue" or "create a ticket"

## Required Tools

This skill works with the GitHub MCP server. Ensure it's configured in `.vscode/mcp.json`.

## Instructions

### For Bug Reports

1. Gather information about the bug:
   - Steps to reproduce
   - Expected vs actual behavior
   - Environment details

2. Use the bug report template at `templates/bug-report.md`

3. Create the issue using the GitHub MCP `create_issue` tool

### For Feature Requests

1. Clarify the feature requirements
2. Use the feature template at `templates/feature-request.md`
3. Create the issue with appropriate labels

## Templates

### Bug Report Template

See [templates/bug-report.md](templates/bug-report.md):

```markdown
## Bug Description
{Brief description}

## Steps to Reproduce
1. 
2. 
3. 

## Expected Behavior
{What should happen}

## Actual Behavior
{What actually happens}

## Environment
- OS: 
- Version: 
- Browser (if applicable): 

## Screenshots
{If applicable}
```

### Feature Request Template

See [templates/feature-request.md](templates/feature-request.md):

```markdown
## Feature Description
{What feature is being requested}

## Use Case
{Why this feature is needed}

## Proposed Solution
{How it might work}

## Alternatives Considered
{Other approaches}
```
``````

### Bootstrap Skills with a Skill-Creator Skill

This is where skills get interesting: **skills can create skills.**

The agentskills.io specification defines a portable format for packaging agent capabilities. But writing SKILL.md files by hand â€” with correct frontmatter, proper name validation, good descriptions, organized sections â€” is tedious. The solution? A skill that knows how to create skills.

#### Why This Matters

The skill-creator pattern demonstrates the power of the skills model:

1. **Meta-capability**: The agent gains the ability to extend itself
2. **On-demand loading**: The skill-creator only loads when you're actually creating skills
3. **Consistency**: Every skill follows the same structure and best practices
4. **Velocity**: New skills take seconds instead of minutes

This is the difference between "AI that follows instructions" and "AI that builds its own capabilities."

#### What the Skill-Creator Handles

A well-designed skill-creator manages the entire skill lifecycle:

| Task | Manual Effort | With Skill-Creator |
|------|---------------|-------------------|
| Directory structure | Create folders, remember paths | Automatic |
| SKILL.md boilerplate | Look up format, copy template | Generated |
| Frontmatter validation | Check name rules, description length | Validated |
| Description quality | Remember best practices | Guided |
| Section organization | Decide structure | Standardized |
| Scripts/templates | Create subdirectories | Scaffolded |

#### Getting Started

Add a skill-creator skill to your repository:

```
.github/skills/
â””â”€â”€ create-agent-skill/
    â””â”€â”€ SKILL.md
```

The SKILL.md should include:
- The agentskills.io specification rules
- Name validation requirements (lowercase, hyphens, no consecutive)
- Description best practices (what + when + keywords)
- Section templates for common skill patterns
- Examples of good vs. poor skills

A [reference implementation](https://github.com/anthropics/skills) is available in the Anthropic skills repository.

#### Example: Creating a Skill in Seconds

With the skill-creator in your repo, bootstrapping new skills becomes conversational:

> ðŸ’¬ Try this prompt:
>
> `Create a skill for linting SQL queries`

The agent:
1. Recognizes the request matches the skill-creator's description
2. Loads the full skill-creator instructions
3. Creates `.github/skills/sql-linting/SKILL.md` with proper structure
4. Generates a description optimized for discovery
5. Adds sections for when to use, instructions, and examples

**Generated output:**
```markdown
---
name: sql-linting
description: Lint and format SQL queries for consistency and best practices. Use when the user wants to check SQL syntax, enforce naming conventions, or standardize query formatting.
metadata:
  author: your-org
  version: "1.0"
---

# SQL Linting

## When to Use This Skill

Use this skill when:
- User wants to check SQL queries for errors
- User mentions SQL formatting or style
- User asks about query optimization patterns
- User wants consistent SQL across the codebase

## Instructions

### Basic Linting

Check for common issues:
- Missing aliases on joined tables
- SELECT * in production queries
- Missing WHERE clauses on UPDATE/DELETE
...
```

#### Advanced: Skill-Creator Prompts

For more control, provide context in your prompt:

> ðŸ’¬ Try this prompt:
>
> `Create a skill for Kubernetes deployments. It should cover kubectl commands, common YAML patterns, and debugging pods. Include a scripts/ directory for helper scripts.`

The skill-creator uses your requirements to generate a more comprehensive skill:

```
kubernetes-deployments/
â”œâ”€â”€ SKILL.md
â””â”€â”€ scripts/
    â”œâ”€â”€ check-pod-status.sh
    â””â”€â”€ rollback-deployment.sh
```

#### The Recursive Pattern

Here's the elegant part: **the skill-creator is itself a skill.**

This means:
- It's only loaded into context when you're creating skills
- It doesn't consume tokens during normal coding tasks
- It follows the same spec it teaches
- You can improve it by editing its own SKILL.md

When you open your repository and ask "help me resize some images," the skill-creator isn't loaded. When you ask "create a skill for image resizing," it activates and guides the process.

#### Building a Skill Library

Teams using the skill-creator pattern report rapid capability growth:

**Week 1:** Add skill-creator to repo
**Week 2:** Create skills for common tasks (testing, deployment, documentation)
**Week 3:** Team members contribute domain-specific skills
**Month 2:** Library of 15-20 skills covering most workflows

Each skill is:
- Version-controlled alongside code
- Portable across AI agents (VS Code, GitHub CLI, Copilot coding agent)
- Discoverable via description matching
- Maintainable by the whole team

This is how you scale AI capabilities across an organization â€” not by writing longer instruction files, but by building composable skills that activate when needed.

### Why Skills Instead of Always-On Instructions?

A practical example from real usage: ImageMagick image processing.

**The Problem:**
Originally, ImageMagick instructions were in the always-on `copilot-instructions.md` file. This meant:
- Hundreds of lines about image processing were loaded for *every* request
- Simple CSS edits had to process context about `magick` commands
- The instructions consumed context window even when irrelevant

**The Solution:**
Moving ImageMagick to a skill means:
- The agent only sees a brief description until actually needed
- When you say "resize these images," the skill activates
- When you say "update the CSS," no image processing context is loaded

**Rule of Thumb:**
- **Always-on instructions**: Rules that apply to most/all coding tasks (style, testing patterns, architecture)
- **Skills**: Specialized capabilities used occasionally (image processing, specific integrations, complex workflows)

If you find yourself thinking "this is useful, but it doesn't need to be in context all the time" â€” that's a skill.

### How Skills Load: Description Matching

Unlike file-based instructions (which use `applyTo` patterns), skills load **on-demand via description matching**. Here's what happens under the hood:

1. **Every system prompt includes a list of available skills** â€” just their names and descriptions
2. **The agent decides which skills are relevant** based on matching the user's request to skill descriptions
3. **Only then is the full skill content loaded** into context

This makes skills lightweight while enabling powerful capabilities. However, it means **your skill's description is critical**:

```markdown
---
description: 'Use this skill when the user needs to resize, convert, or optimize images using ImageMagick. Handles batch processing of image files.'
---
```

**Description Best Practices:**
- Be explicit about **when** to load the skill (triggers)
- Be explicit about **what value** it provides (capabilities)
- Include key action words users might say ("resize", "convert", "optimize")
- Keep it scannable â€” the agent reads many descriptions to make a decision

**Ineffective description:**
```markdown
description: 'Image processing skill'
```

**Effective description:**
```markdown
description: 'Resize, convert, compress, and batch-process images using ImageMagick. Use when the user mentions image optimization, format conversion, or bulk image operations.'
```

### What the Spec Does (and Doesn't) Control

The [agentskills.io](https://agentskills.io) specification intentionally leaves certain decisions to the **host** (VS Code, GitHub CLI, coding agent):

| Controlled by Spec | Left to Host |
|-------------------|--------------|
| Skill file format (SKILL.md) | File system locations |
| Frontmatter fields | Installation process |
| Description requirements | Runtime environment |
| Resource references | Package management |
| Compatibility declarations | Discovery mechanism |

This design allows the same skill to work across different agents while each host optimizes for its environment. A skill written for VS Code will also work with the GitHub CLI and coding agent without modification.

**Compatibility Field:**
Skills can declare which environments they support:

```markdown
---
compatibility:
  - vscode
  - github-cli
  - coding-agent
---
```

This helps hosts decide whether to surface a skill in their environment.

### Debugging Skills: See What's Loaded

VS Code's **Chat Debug View** reveals exactly what skills and instructions are loaded for each request. This is invaluable for understanding why a skill did or didn't activate:

1. Open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Run **"Developer: Show Chat Debug View"**
3. Send a request in Copilot Chat
4. Click on the request in the debug panel to see:
   - Full system prompt
   - List of available skills (with descriptions)
   - List of available instructions
   - Which skills were loaded for this request

This view shows that skills appear as a list of descriptions in every system prompt. When you see a skill wasn't activated, check whether its description clearly matches what the user asked for.

### Skills vs. MCP Servers: When to Use Which

Skills and MCP servers are complementary, not competing. **You can and should use them together.** The question isn't "which one?" â€” it's "what does each contribute?"

- **MCP servers** provide *access* â€” authentication, API connections, external tool integration
- **Skills** provide *knowledge* â€” templates, conventions, workflows, domain expertise

Many of the best setups combine both: an MCP server handles the "how to connect" while a skill handles the "how we do things here."

#### The Key Question

When deciding between them, ask: **Does the capability require crossing a security boundary?**

| Factor | Use a Skill | Use an MCP Server | Use Both |
|--------|-------------|-------------------|----------|
| **Security boundary** | No auth needed, runs locally | Requires authentication/API keys | External access + team knowledge |
| **Tool availability** | Tool is commonly installed locally | Tool requires remote access | API access + workflow rules |
| **Context focus** | Enhances agent's existing capabilities | Provides entirely new capabilities | Capabilities + conventions |
| **Maintenance** | Self-contained, portable | Requires server deployment/hosting | Skill versioned with code |

#### Decision Guide

**Use a Skill when:**
- The capability leverages tools already installed on the developer's machine
- No authentication or API keys are required
- The operation stays within the local development environment
- You want portability across different AI agents

**Use an MCP Server when:**
- The capability requires authenticating to an external service
- The tool isn't commonly installed locally
- You need to cross organizational security boundaries
- Real-time data from external systems is required

**Use Both (Skill + MCP) when:**
- The MCP server provides API access, but your team has specific conventions
- You want consistent formatting, templates, or workflows across the team
- The skill encodes "how we do things here" while the MCP provides "access to do things"

#### Practical Examples

**Git Operations â†’ Skill**
```
Creating commits, branching, viewing history, staging changes
```
Git is almost universally installed on developer machines. A skill can invoke git commands directly without needing external authentication. The skill enhances what Copilot can do with tools that are already present.

**GitHub/Jira/Azure DevOps Issues â†’ Skill + MCP Server**
```
Creating issues, updating tickets, querying project status
```
This is an ideal hybrid case. The **MCP Server** handles authentication and API access to external services. The **Skill** provides team-specific knowledge: issue templates, required labels, field conventions, and workflow rules. Without the skill, the agent can create issues but won't know your team's formatting standards. Without the MCP server, the agent knows the standards but can't reach the API.

**File System Operations â†’ Skill**
```
Reading logs, searching directories, running local scripts
```
The file system is local and doesn't require authentication. A skill can provide enhanced file operations that work across different AI agents.

**Database Queries â†’ MCP Server**
```
Querying production data, fetching analytics, checking records
```
Databases typically require connection strings with credentials. An MCP server properly manages these secrets and provides secure access.

#### The Hybrid Pattern

In practice, many workflows combine both:

1. **Skill** handles local operations (git commit, file changes, running tests)
2. **MCP Server** handles external operations (create PR, update ticket, notify team)

Example workflow:
```
Developer: "Implement this feature and create a PR"

Skill: Creates branch, makes changes, commits locally
MCP Server: Pushes to GitHub, creates PR, links to Jira ticket
```

This pattern keeps local operations fast and portable while properly securing external integrations.

For more on building skills, visit [agentskills.io/home](https://agentskills.io/home).

---

## Custom Agents

### Overview

Custom Agents provide specialized AI personas with constrained tool access and defined behaviors. They can operate as top-level assistants or as subagents invoked by other workflows.

**Loading:** Top-level OR as subagent
**Best For:** Constrained workflows

**Location:** `.github/agents/*.agent.md`

### When to Use Custom Agents

- When Copilot should adopt a specific expert persona
- When different contexts require different AI behaviors
- When consistent response styles are needed across a team
- For specialized work like security review, architecture, or mentoring

### File Format

Custom Agent files use the `.agent.md` extension and support these frontmatter fields:

| Field | Description |
|-------|-------------|
| `name` | Display name in the agent picker |
| `description` | Shown as placeholder text in chat input |
| `tools` | List of tools available to this agent |
| `model` | AI model to use (e.g., `Claude Sonnet 4`, `GPT-4o`) |
| `handoffs` | Define transitions to other agents |
| `argument-hint` | Hint text for user interaction |

```markdown
---
name: 'Security Reviewer'
description: 'Reviews code with a focus on security vulnerabilities'
tools: ['search', 'readFile', 'usages']
model: 'Claude Sonnet 4'
---

You are a senior security engineer reviewing code for vulnerabilities.

## Your Expertise
- OWASP Top 10
- Common vulnerability patterns
- Secure coding practices
- Authentication and authorization
- Input validation and sanitization

## How You Respond
- Start with a security risk assessment (Critical/High/Medium/Low/Info)
- Cite specific vulnerability types (e.g., CWE-79 for XSS)
- Provide concrete fix recommendations
- Suggest additional security tests

## Always Check For
- SQL injection vectors
- XSS vulnerabilities  
- Insecure deserialization
- Broken access control
- Sensitive data exposure
- Security misconfiguration
```

### Recommended Custom Agents

The following agent configurations address common development scenarios:

#### 1. System Architect
**File:** `.github/agents/architect.agent.md`

```markdown
---
name: 'System Architect'
description: 'High-level design and architecture decisions'
tools: ['search', 'readFile', 'fetch', 'githubRepo']
model: 'Claude Sonnet 4'
handoffs:
  - label: 'Start Implementation'
    agent: 'agent'
    prompt: 'Implement the architecture outlined above.'
---

You are a principal software architect with 20 years of experience in 
distributed systems.

## Your Focus
- System design and scalability
- Technology selection
- Trade-off analysis
- Long-term maintainability
- Integration patterns

## How You Think
- Always consider the 5-year horizon
- Prefer boring technology that works
- Suggest incremental migration paths
- Consider team skill sets

## Response Style
- Start with the "why" before the "what"
- Include diagrams (Mermaid) when helpful
- List trade-offs explicitly
- Provide confidence level in recommendations
```

#### 2. Mentor
**File:** `.github/agents/mentor.agent.md`

```markdown
---
name: 'Patient Mentor'
description: 'Explains concepts thoroughly for learning'
tools: ['search', 'readFile', 'fetch']
model: 'GPT-4o'
---

You are a patient senior developer mentoring a junior team member.

## Your Approach
- Explain concepts from first principles
- Use analogies and real-world examples
- Never make the user feel bad for not knowing
- Celebrate curiosity

## Response Format
- Start with the simple explanation
- Add depth progressively
- Include "why this matters"
- Suggest resources for further learning

## Teaching Style
- Use the Socratic method when appropriate
- Provide hands-on exercises
- Connect new concepts to familiar ones
```

#### 3. Debug Specialist
**File:** `.github/agents/debugger.agent.md`

```markdown
---
name: 'Debug Detective'
description: 'Methodical bug hunting and diagnosis'
tools: ['search', 'readFile', 'usages', 'terminalLastCommand']
model: 'Claude Sonnet 4'
---

You are a systematic debugging expert who approaches problems methodically.

## Your Process
1. Reproduce the issue
2. Isolate variables
3. Form hypotheses
4. Test systematically
5. Verify the fix

## Questions You Always Ask
- What changed recently?
- Can you reproduce it consistently?
- What does the error message say?
- What have you already tried?

## Output Format
- Numbered diagnostic steps
- Expected vs. actual at each step
- Confidence level in diagnosis
- Verification steps after fix
```

#### 4. Code Reviewer
**File:** `.github/agents/reviewer.agent.md`

```markdown
---
name: 'Code Reviewer'
description: 'Thorough code review focused on quality'
tools: ['search', 'readFile', 'usages', 'getChangedFiles']
model: 'Claude Sonnet 4'
---

You are a meticulous code reviewer focused on code quality and team standards.

## Review Priorities (in order)
1. Correctness - Does it work?
2. Security - Is it safe?
3. Performance - Is it efficient?
4. Maintainability - Can others understand it?
5. Style - Does it follow conventions?

## Feedback Style
- Be specific and actionable
- Explain the "why"
- Differentiate must-fix vs. nice-to-have
- Acknowledge good patterns

## Format
- Use conventional comments: `nit:`, `suggestion:`, `question:`, `issue:`
- Include code examples for fixes
- Reference team standards when applicable
```

### Sub-Agents: Context Isolation for Complex Workflows

Custom agents can be invoked as **sub-agents** by other workflows. This pattern solves a critical problem: long-running, context-heavy tasks can cause the main conversation to lose focus or "forget" important details.

**Why Sub-Agents Matter:**
- Each sub-agent runs in isolated context
- Prevents context bleed between unrelated tasks
- Allows heavy workflows without overwhelming the main conversation
- Sub-agent returns a summary, not the full context

**The Pattern:**
```
Main conversation: "Implement this feature and create a PR"
â”œâ”€â”€ Sub-agent 1: Makes code changes (isolated context)
â”œâ”€â”€ Sub-agent 2: Runs tests (isolated context)  
â””â”€â”€ Sub-agent 3: Creates PR (isolated context)
```

Each sub-agent focuses on its specific task without inheriting irrelevant context from sibling tasks. The main agent orchestrates and receives summarized results.

**When to Use Sub-Agents:**
- Tasks that require deep, specialized context
- Workflows where different steps shouldn't influence each other
- Long-running operations that might otherwise cause context drift
- Batch operations (e.g., processing many files independently)

**Defining Handoffs:**
```markdown
---
name: 'Code Reviewer'
handoffs:
  - label: 'Implement Fixes'
    agent: 'agent'
    prompt: 'Implement the fixes identified in the review above.'
---
```

The `handoffs` field creates natural workflow transitions, where one agent can spawn another for specialized work.

### Prompt vs. Custom Agent Comparison

| Aspect | Prompt File | Custom Agent |
|--------|-------------|--------|
| **Duration** | One-shot, specific task | Entire conversation session |
| **Variables** | Yes, fill-in-the-blank | No, set at session start |
| **Purpose** | Reusable task templates | Persona/behavior settings |
| **State** | Stateless | Persists during conversation |
| **Example** | "Generate a new API route for users" | "Act as a security expert" |

**Selection guideline:**
- Use **prompts** for specific tasks ("do this thing")
- Use **custom agents** for behavioral changes ("be this way")

---

## Building Custom Agents

The recommended approach for creating custom agents is through VS Code's built-in interface combined with agent-assisted iteration.

### Creating via the Configure Menu (Recommended)

1. In the Chat view, click the **gear icon** (Configure Chat)
2. Select **Custom Agents** > **Create new custom agent**
3. Choose the storage location:
   - **Workspace:** `.github/agents/` folder (shared with team via version control)
   - **User Profile:** Personal agents across all workspaces
4. Provide a name for the agent
5. Use the agent itself to refine the configuration

### Agent-Driven Iteration (Best Practice)

Rather than manually editing agent files, use Copilot to generate and refine them:

> **ðŸ’¬ Try this prompt:**
>
> *Create a custom agent for security code review. It should:*
> *- Focus on OWASP Top 10 vulnerabilities*
> *- Use Claude Sonnet 4 for its reasoning capabilities*
> *- Have access to search, readFile, and usages tools*
> *- Include handoffs to an implementation agent after review*
>
> *Store the result in .github/agents/security-reviewer.agent.md*

This approach ensures:
- Proper YAML frontmatter syntax
- Consistent formatting
- Human-verifiable output that can be reviewed in PRs

### Anti-Patterns to Avoid

| Anti-Pattern | Why It's Problematic | Better Approach |
|--------------|---------------------|------------------|
| **Typing directly into .agent.md files** | Prone to syntax errors, hard to maintain consistency | Use the gear icon or ask the agent to generate/update |
| **Vague persona definitions** | "You are helpful" produces inconsistent results | Be specific about expertise, experience, and approach |
| **No tool restrictions** | Agent may use tools inappropriate for the persona | Explicitly list allowed tools in frontmatter |
| **Missing guardrails** | Persona drifts or produces off-target responses | Include "What You Never Do" section |
| **Overly broad scope** | "Expert in everything" dilutes expertise | Create multiple focused agents instead |
| **No handoffs defined** | Users must manually switch between related workflows | Define handoffs for natural workflow transitions |

### The Agent Formula

Effective custom agents include these elements:

```markdown
---
name: 'Display Name (keep it short!)'
description: 'What this mode does (shows in picker)'
---

# Who You Are
[The persona and expertise]

# How You Think  
[Your approach and methodology]

# How You Respond
[Output format and style]

# What You Always Do
[Consistent behaviors]

# What You Never Do
[Guardrails and limitations]
```

### Step 3: Define Specific Personas

Specificity in persona definition produces consistency in outputs.

**Too Vague:**
```markdown
You are a helpful assistant for reviewing code.
```

**Appropriately Specific:**
```markdown
You are a principal engineer with 20 years of experience across 
startups and enterprise. You've seen what works and what becomes 
technical debt. You're kind but directâ€”you won't sugarcoat issues 
but you always explain your reasoning.
```

### Step 4: Define Response Patterns

Specify how the agent should structure its responses:

```markdown
## How You Respond

1. **Start with the bottom line** - Lead with your recommendation
2. **Then the reasoning** - Explain why, cite evidence
3. **Then alternatives** - What else could work
4. **Then caveats** - What to watch out for
5. **End with next steps** - Make it actionable

Always use Mermaid diagrams for architecture discussions.
Always provide code examples, not just descriptions.
Never say "it depends" without then explaining what it depends ON.
```

### Step 5: Add Guardrails

Guardrails prevent the persona from producing off-target responses:

```markdown
## What You Never Do

- Never make up statistics or benchmarks
- Never recommend technology you can't justify
- Never ignore security implications
- Never give advice without considering the team's skill level
- Never forget to mention trade-offs
```

### Agent Creator Meta-Prompt

The following prompt generates new custom agent configurations through the agent:

> **ðŸ’¬ Try this prompt:**
>
> *Create a new custom agent at `.github/agents/{{modeName}}.agent.md`.*
>
> *Persona Details:*
> *- Role: {{roleDescription}}*
> *- Expertise area: {{expertiseArea}}*
> *- Personality: {{personalityTraits}}*
> *- Preferred model: Claude Sonnet 4 (or specify another)*
>
> *Generate an agent that includes:*
>
> *1. YAML Frontmatter*
>    *- name, description, tools, model*
>    *- handoffs to related agents if applicable*
>
> *2. Identity Section*
>    *- Specific background and experience*
>    *- Key expertise areas*
>    *- Personality traits*
>
> *3. Methodology Section*
>    *- How this persona approaches problems*
>    *- What questions they always ask*
>    *- Their decision-making framework*
>
> *4. Response Format Section*
>    *- Structure of typical responses*
>    *- When to use examples vs. explanations*
>    *- Preferred formatting (bullets, numbered, headers)*
>
> *5. Guardrails Section*
>    *- What this persona never does*
>    *- When to defer to other experts*
>    *- Limitations to acknowledge*
>
> *6. Signature Behaviors*
>    *- Unique phrases or approaches*
>    *- Consistent patterns users can expect*
>    *- Quality markers in responses*
>
> *Make the persona feel distinct and memorable.*

### Additional Agent Examples

**Rubber Duck (Problem Exploration):**

```markdown
---
name: 'Rubber Duck'
description: 'Helps you think through problems by asking questions'
tools: ['search', 'readFile']
model: 'GPT-4o'
---

You are a rubber duck. Your job is NOT to solve problemsâ€”it's to help 
the user solve them themselves by asking good questions.

## Your Technique
- Ask clarifying questions
- Repeat back what you heard
- Ask "what would happen if...?"
- Never give direct answers until asked explicitly
- Celebrate when the user has the breakthrough

## Phrases You Use
- "So what I'm hearing is..."
- "What have you tried so far?"
- "What would the simplest solution look like?"
- "What's stopping that from working?"
- "Interesting! What made you think of that?"
```

**The Devil's Advocate** â€“ Challenges every decision:

```markdown
---
name: "Devil's Advocate"
description: 'Challenges decisions to find weaknesses'
tools: ['search', 'readFile', 'fetch']
model: 'Claude Sonnet 4'
---

You argue the opposite position of whatever the user suggests.
Your job is to find holes in their reasoning.

## Your Approach
- "That's interesting, but have you considered..."
- "What happens when X fails?"
- "How would this scale to 10x the load?"
- "What would a skeptical stakeholder say?"

## Important
You're not being negativeâ€”you're being thorough.
After challenging, acknowledge good points.
End with "If you can address these, you've got a solid plan."
```

---

## MCP (Model Context Protocol)

### Overview

MCP (Model Context Protocol) provides external gateway capabilities for Copilot. While instructions, prompts, and custom agents provide Copilot with knowledge, MCP enables Copilot to take real actions and access live data from external systems.

**Loading:** Session start
**Best For:** External gateways

### Instructions vs. MCP Capabilities

These two primitives are complementary:

- **Instructions** = What Copilot KNOWS ("Use parameterized queries")
- **MCP** = What Copilot CAN DO (Execute a database query and return results)

A configuration might include an instruction stating "always use our inventory API" alongside an MCP server that enables Copilot to actually call that API.

### Configuring MCP Servers

MCP servers can be configured in VS Code settings or project configuration:

**VS Code Settings (.vscode/settings.json):**
```json
{
  "github.copilot.chat.mcpServers": {
    "database": {
      "command": "npx",
      "args": ["@your-org/db-mcp-server"],
      "env": {
        "DATABASE_URL": "${env:DATABASE_URL}"
      }
    },
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
      }
    }
  }
}
```

### Available MCP Servers

| Server | What It Does |
|--------|--------------|
| `@modelcontextprotocol/server-github` | GitHub API access (issues, PRs, repos) |
| `@modelcontextprotocol/server-postgres` | PostgreSQL queries |
| `@modelcontextprotocol/server-filesystem` | File system operations |
| `@modelcontextprotocol/server-fetch` | HTTP requests |
| `@modelcontextprotocol/server-puppeteer` | Browser automation |

### Example Use Cases

- **"What's the status of issue #234?"** â†’ GitHub MCP
- **"Show me all users who signed up this week"** â†’ Database MCP
- **"Test this API endpoint"** â†’ Fetch MCP
- **"Take a screenshot of the login page"** â†’ Puppeteer MCP

### Instructions vs. MCP Comparison

This is an important distinction:

| | Custom Instructions | MCP |
|-|---------------------|-----|
| **What** | Text context for AI | External tool access |
| **How** | Just knowledge | Actual capabilities |
| **Example** | "We use PostgreSQL" | Can query PostgreSQL |
| **Updates** | Manual | Real-time |

**Instructions provide knowledge. MCP provides capabilities.**

---



---

[ Part I: Foundations](part-1-foundations.md) | [Part III: Putting It All Together ](part-3-putting-it-together.md)
