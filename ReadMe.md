# The Definitive Guide to Customizing Your Repo for GitHub Copilot

*Transform GitHub Copilot from a generic assistant into a team-aware coding partner.*

---

## Introduction

GitHub Copilot probably isn't hallucinating. It's making a best effort based on its context—just like you would if someone dropped you into a million-line codebase and asked you to make changes after an hour of reading code.

**The right mental model: Copilot is a new developer on your team.**

Think about what makes code easy for a new team member to work with:
- Easy to read, well-formed code
- Small, modular functions with clear responsibilities
- Good naming conventions (not everything called `i`, `temp`, or `data`)
- Comprehensive tests that document expected behavior
- Clear architectural patterns

These same qualities make code easier for Copilot to understand and extend correctly. Code that's maintainable for humans is maintainable for AI. Code that confuses junior developers will confuse Copilot too.

This guide is structured in two parts:

**Part 1: Tuning Copilot for Your Current Codebase**
This guide focuses on the customization primitives that help Copilot understand your existing codebase—your conventions, patterns, and preferences. These tools work with your code as it exists today.

**Part 2: Refactoring for Better AI Collaboration** *(Coming Soon)*
The companion guide will cover how to refactor and restructure code so AI agents have an easier time understanding and modifying it. Better code organization benefits both human developers and AI assistants.

When properly configured, Copilot can:

- Respect team coding conventions automatically
- Follow architectural patterns without prompting
- Avoid deprecated libraries and anti-patterns
- Generate code that passes PR review on the first try

This guide provides a comprehensive walkthrough of every customization primitive available in GitHub Copilot. By the end, development teams will have the knowledge to transform Copilot from a generic assistant into a context-aware team member that understands the nuances of their specific codebase.

---

## Table of Contents

1. [The Customization Primitives](#the-customization-primitives)
2. [Always-On Instructions](#always-on-instructions)
3. [File-Based Instructions](#file-based-instructions)
4. [Prompt Files (Slash Commands)](#prompt-files-slash-commands)
5. [How to Create Effective Prompts](#how-to-create-effective-prompts)
6. [Skills](#skills)
7. [Custom Agents](#custom-agents)
8. [MCP (Model Context Protocol)](#mcp-model-context-protocol)
9. [The Decision Matrix](#the-decision-matrix)
10. [Frequently Asked Questions](#frequently-asked-questions)
11. [Real-World Examples](#real-world-examples)
12. [Best Practices](#best-practices)
13. [Resources](#resources)

---

## The Customization Primitives

GitHub Copilot provides six distinct customization primitives. Each serves a specific purpose and loads at different points in the interaction lifecycle. Understanding when each primitive activates is essential for effective configuration.

| Primitive | Location | Loading | Best For |
|-----------|----------|---------|----------|
| [**Always-on Instructions**](#always-on-instructions) | `.github/copilot-instructions.md` | Every session | Codebase guardrails |
| [**File-based Instructions**](#file-based-instructions) | `.github/instructions/*.instructions.md` | Pattern match / description match | Area-specific rules |
| [**Prompts (Slash Commands)**](#prompt-files-slash-commands) | `.github/prompts/*.prompt.md` | User invokes | One-shot workflows |
| [**Skills**](#skills) | `.github/skills/` | Description match → on-demand | Reusable capabilities |
| [**Custom Agents**](#custom-agents) | `.github/agents/*.agent.md` | Top-level OR as subagent | Constrained workflows |
| [**MCP**](#mcp-model-context-protocol) | `.vscode/mcp.json` | Session start | External gateways |

This table represents the complete customization surface area. Each primitive addresses a distinct need:

- **Always-on Instructions** – Global rules that apply to every Copilot interaction in the repository
- **File-based Instructions** – Targeted rules that activate based on file patterns or context
- **Prompts (Slash Commands)** – Reusable task templates invoked explicitly by users
- **Skills** – Discrete capabilities that Copilot can invoke when relevant
- **Custom Agents** – Specialized AI personas with constrained tool access and behaviors
- **MCP (Model Context Protocol)** – External service integrations that extend Copilot's capabilities

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

This helps Copilot understand not just *what* to do, but *why*—leading to better suggestions in edge cases.

### Complete Example: Production Next.js Project

The following example demonstrates a comprehensive instructions file for a production application:

```markdown
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

## Coding Standards

### TypeScript
- Enable all strict checks
- No `any` types except in test mocks
- Prefer interfaces over types for object shapes
- Use discriminated unions for complex state

### React Patterns
```typescript
// ✅ Good: Custom hook for data fetching
function useInventoryItems(warehouseId: string) {
  return useQuery({
    queryKey: ['inventory', warehouseId],
    queryFn: () => fetchInventory(warehouseId),
  });
}

// ❌ Bad: Fetching in useEffect
useEffect(() => {
  fetch('/api/inventory').then(/* ... */);
}, []);
```

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

### Agent-Driven Generation (Best Practice)

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

1. **"What does Copilot frequently get wrong?"** → These become explicit rules
2. **"What feedback appears repeatedly in PR reviews?"** → These become guidelines
3. **"What would a new hire need to know on day one?"** → This becomes context
4. **"What libraries or patterns have been deprecated?"** → This becomes the "avoid" list

> **Practical tip:** Review the last 10-20 PR comments from the team. Repeated feedback indicates rules that should be codified in the instructions file.

### Use the "Good vs Bad" Pattern

Copilot responds more effectively to examples than to abstract rules. Instead of stating "prefer functional patterns," demonstrate the preference:

```markdown
## Data Transformation

✅ **Preferred:**
```typescript
const activeUsers = users
  .filter(user => user.isActive)
  .map(user => user.name);
```

❌ **Avoid:**
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

### Bootstrap: Auto-Generate Instructions via Agent

For a quick start, ask the agent to analyze your codebase and generate a complete instructions file:

> **💬 Try this prompt:**
>
> *Analyze this codebase and generate a comprehensive .github/copilot-instructions.md file.*
>
> *Your Analysis Should Cover:*
> *1. Detect the tech stack - Look at package.json, requirements.txt, go.mod, etc.*
> *2. Identify patterns - What coding patterns are consistently used?*
> *3. Find conventions - Naming, file structure, import organization*
> *4. Spot testing patterns - How are tests structured and named?*
> *5. Check for config files - ESLint, Prettier, tsconfig settings that imply preferences*
>
> *Output Format:*
> *Generate a complete `.github/copilot-instructions.md` file with:*
> *- Project overview (inferred from README or package description)*
> *- Tech stack (from dependencies)*
> *- Code style rules (from linter configs + observed patterns)*
> *- Good vs Bad examples (from actual code in the repo)*
> *- Testing requirements (from test files structure)*
> *- Architecture notes (from folder structure)*
>
> *Make the instructions specific to THIS codebase, not generic best practices.*

This approach works well because:
- Agent can explore the full codebase dynamically
- Results are specific to each repository
- Human can review and refine the generated output
- No manual analysis required

### Quick Start: Roll Out Instructions Across All Repos

For organizations wanting to bootstrap Copilot Instructions Files across multiple repositories, use the GitHub MCP server combined with an agent to automate the rollout:

> **💬 Try this prompt:**
>
> *Help me roll out Copilot Instructions Files across my organization:*
>
> *1. First, check if there's already a tracking issue for "copilot instructions" in our main repo*
>
> *2. Search for all repositories in my org (owner:MY-ORG-NAME)*
>
> *3. For each repository that doesn't have a .github/copilot-instructions.md file:*
>    *- Create a tracking issue with:*
>      *- Title: "Add Copilot Instructions File"*
>      *- Body: Include a template for the instructions file and link to our standards doc*
>      *- Labels: ["enhancement", "copilot"]*
>
> *4. Optionally, assign the issues to the appropriate code owners*

> **💬 Alternative: Create a PR directly for each repo:**
>
> *For each repository in my org (owner:MY-ORG-NAME) that lacks a .github/copilot-instructions.md:*
>
> *1. Check if the file already exists*
> *2. If missing, create a new branch called "add-copilot-instructions"*
> *3. Add a starter instructions file based on our template*
> *4. Create a pull request with:*
>    *- Title: "Add Copilot Instructions File"*
>    *- Body: Explain what the file does and link to documentation*
>    *- Base: main*

This MCP-powered approach enables:
- **Bulk operations** across dozens or hundreds of repos
- **Tracking visibility** via issues in each repository
- **Consistent templates** applied organization-wide
- **Code owner involvement** through assignments and PR reviews

---

## File-Based Instructions

While always-on instructions apply globally, file-based instructions provide targeted rules for specific areas of a codebase.

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

### Example Structure

**File:** `.github/instructions/api-routes.instructions.md`

```markdown
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
```

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
| `ask` | Responds conversationally | Questions, explanations, brainstorming |
| `edit` | Modifies the current file/selection | Refactoring, bug fixes, enhancements |
| `agent` | Takes autonomous action across files | Multi-file changes, scaffolding, complex tasks |

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
agent: 'edit'
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
agent: 'edit'
description: 'Generate comprehensive documentation'
model: 'GPT-4o'
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

> **💬 Try this prompt:**
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
| **Ignoring mode selection** | Wrong mode causes unexpected behavior | Match mode to task (ask/edit/agent) |
| **No model specification** | Inconsistent results across sessions | Specify model for reproducibility |
| **No reference to instructions** | Prompt ignores team conventions | Reference copilot-instructions.md explicitly |

### Mode Reference

| Mode | Copilot Can... | Best For |
|------|----------------|----------|
| `ask` | Talk back, explain, suggest | Design discussions, Q&A, brainstorming |
| `edit` | Modify selected code only | Refactoring, fixing, enhancing a specific section |
| `agent` | Create files, edit multiple files, run commands | Scaffolding, multi-file changes, complex tasks |
| Custom agent | Use that agent's persona and tools | Specialized workflows with defined behavior |

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

> **💬 Try this prompt:**
>
> *Create a new prompt file at `.github/prompts/{{promptName}}.prompt.md`.*
>
> *Prompt Requirements:*
> *- Purpose: {{purposeDescription}}*
> *- Mode: {{mode:ask|edit|agent}}*
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

> **💬 Try this prompt:**
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

**Loading:** Description match → on-demand
**Best For:** Reusable capabilities across tools

### Agent Skills (Preview)

VS Code Insiders includes **Agent Skills** (Preview) — an open standard for teaching Copilot specialized capabilities through folders containing instructions, scripts, and resources. Unlike custom instructions that primarily define coding guidelines, skills focus on specialized workflows and capabilities.

**Key differences from other primitives:**
- Skills work across multiple AI agents (VS Code, GitHub Copilot CLI, GitHub Copilot coding agent)
- Portable via the open standard at [agentskills.io/home](https://agentskills.io/home)
- Include scripts and resources alongside instructions

For full documentation, examples, and the skill specification, visit [agentskills.io/home](https://agentskills.io/home).

Agent Skills is currently available in VS Code Insiders. As this feature matures, skills will become a primary mechanism for extending Copilot capabilities.

### Skills vs. MCP Servers: When to Use Which

This distinction is still emerging, but a clear pattern is developing. The key question: **Does the capability require crossing a security boundary?**

| Factor | Use a Skill | Use an MCP Server |
|--------|-------------|-------------------|
| **Security boundary** | No auth needed, runs locally | Requires authentication/API keys |
| **Tool availability** | Tool is commonly installed locally | Tool requires remote access |
| **Context focus** | Enhances agent's existing capabilities | Provides entirely new capabilities |
| **Maintenance** | Self-contained, portable | Requires server deployment/hosting |

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

#### Practical Examples

**Git Operations → Skill**
```
Creating commits, branching, viewing history, staging changes
```
Git is almost universally installed on developer machines. A skill can invoke git commands directly without needing external authentication. The skill enhances what Copilot can do with tools that are already present.

**GitHub/Jira/Azure DevOps Issues → MCP Server**
```
Creating issues, updating tickets, querying project status
```
These operations require authenticating to external services via API tokens. Even though the `gh` CLI might be installed locally, creating issues requires crossing the security boundary to GitHub's API. An MCP server properly handles authentication, rate limiting, and API access.

**File System Operations → Skill**
```
Reading logs, searching directories, running local scripts
```
The file system is local and doesn't require authentication. A skill can provide enhanced file operations that work across different AI agents.

**Database Queries → MCP Server**
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

> **💬 Try this prompt:**
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
technical debt. You're kind but direct—you won't sugarcoat issues 
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

> **💬 Try this prompt:**
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

You are a rubber duck. Your job is NOT to solve problems—it's to help 
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

**The Devil's Advocate** – Challenges every decision:

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
You're not being negative—you're being thorough.
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

- **"What's the status of issue #234?"** → GitHub MCP
- **"Show me all users who signed up this week"** → Database MCP
- **"Test this API endpoint"** → Fetch MCP
- **"Take a screenshot of the login page"** → Puppeteer MCP

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

## Best Practices

### 1. Version Control All Customizations

The `.github/` customizations should be treated as code:
- Review changes in pull requests
- Link to architectural decisions (ADRs, RFCs)
- Update when patterns evolve

### 2. Start Small and Iterate

Avoid attempting to write comprehensive instructions immediately. Begin with:
- The three most common mistakes Copilot makes
- Critical conventions
- Security requirements

Expand as friction points are identified.

### 3. Provide Examples

Copilot learns more effectively from examples than abstract rules:

```markdown
## Code Style

```typescript
// ✅ Preferred
const result = items.filter(isValid).map(transform);

// ❌ Avoid
let result = [];
for (let i = 0; i < items.length; i++) {
  if (isValid(items[i])) {
    result.push(transform(items[i]));
  }
}
```
```

### 4. Explain Rationale

Provide context for requirements:

```markdown
## Date Handling

Use `date-fns` instead of `moment.js`.

**Rationale:** moment.js adds 300KB to the bundle and uses mutable APIs. 
date-fns is tree-shakeable (saving approximately 180KB) and its immutable 
API eliminates a class of timezone-related bugs.
```

### 5. Maintain Focus

If an instructions file exceeds 3000 words, consider:
- Moving detailed examples to prompt files
- Creating custom agents for specialized use cases
- Linking to external documentation

### 6. Test Prompts Thoroughly

Before sharing prompts with a team:
1. Execute them 3-5 times with varying inputs
2. Verify output consistency
3. Have a team member test without guidance

### 7. Review Quarterly

Codebases evolve; customizations should as well:
- Remove deprecated patterns
- Add new conventions
- Incorporate team feedback

---

## Resources

### Official Documentation

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [Awesome Copilot Repository](https://github.com/github/awesome-copilot) - Community examples, prompts, and resources
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) - Building custom integrations

### Community Resources

The [Awesome Copilot](https://github.com/github/awesome-copilot) repository provides:
- Production instruction files
- Tested prompt templates
- Community custom agents
- MCP server examples
- Integration patterns

### Implementation Roadmap

1. **Immediate:** Create `.github/copilot-instructions.md` with five key coding conventions
2. **Week 1:** Add 2-3 prompt files for frequently repeated tasks
3. **Month 1:** Implement custom agents for specialized workflows
4. **Quarter 1:** Evaluate MCP servers for external integrations

---

## Summary

| Objective | Solution |
|-----------|----------|
| Set permanent coding rules | Instructions file |
| Create a reusable task | Prompt file |
| Define AI persona/behavior | Custom Agent |
| Connect to external data/tools | MCP integration |

Organizations that customize GitHub Copilot for their specific workflows and codebases report significantly higher productivity gains compared to default configurations.

The investment in writing comprehensive instructions eliminates repetitive code review feedback. Each prompt created becomes a workflow available to the entire team. Custom agents accelerate specialized work. MCP integrations bring real-world data directly into development conversations.

GitHub Copilot provides substantial value in its default configuration. When customized for a specific repository—with knowledge of patterns, preferences, architectural decisions, and organizational conventions—it transforms from a general-purpose tool into a context-aware development partner.

---

*For additional examples and community contributions, visit [Awesome Copilot](https://github.com/github/awesome-copilot).*
