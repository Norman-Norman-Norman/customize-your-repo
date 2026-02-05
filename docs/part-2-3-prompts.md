# Prompt Files (Slash Commands)

[? File-Based Instructions](part-2-2-file-based-instructions.md) | [Part II Overview](part-2-primitives.md)

---

## Overview

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
| `model` | AI model to use (e.g., `Claude Opus 4.6`, `GPT-5.2`) |
| `tools` | Specific tools available for this prompt |
| `argument-hint` | Hint text for user interaction |

```markdown
---
agent: 'agent'
description: 'Generate a new React component with tests'
model: 'Opus 4.6'
tools: ['editFiles', 'createFile', 'runInTerminal']
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
| `ask` | Read-only — responds conversationally, no file changes | Questions, explanations, brainstorming, code review |
| `agent` | Takes autonomous action — creates/edits files, runs commands | Multi-file changes, scaffolding, bug fixes, any task that modifies code |

**Note:** An `edit` mode exists but is not recommended. Use `agent` for any task that requires modifying files.

### Essential Prompt Files Every Repo Needs

The following prompt templates address common development workflows:

#### 1. Component Generator
**File:** `.github/prompts/new-component.prompt.md`

```markdown
---
agent: 'agent'
description: 'Scaffold a new React component with all the trimmings'
model: 'Opus 4.6'
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
model: 'Opus 4.6'
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
model: 'Opus 4.6'
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
model: 'Opus 4.6'
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
model: 'Opus 4.6'
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

#### 6. GitHub Issue Creator (MCP Integration)
**File:** `.github/prompts/create-issue.prompt.md`

This example demonstrates how prompts can call MCP tools directly. With the GitHub MCP server configured, prompts can create issues, PRs, and interact with GitHub programmatically.

```markdown
---
agent: 'agent'
description: 'Create a GitHub issue from a bug report or feature request'
model: 'Opus 4.6'
tools: ['githubRepo']
---

Create a GitHub issue based on the following:

**Type:** {{issueType}}  <!-- bug, feature, enhancement, documentation -->
**Title:** {{issueTitle}}
**Description:** {{issueDescription}}

## Instructions

1. Format the issue body with proper markdown:
   - For bugs: Include "Steps to Reproduce", "Expected Behavior", "Actual Behavior", and "Environment"
   - For features: Include "Problem Statement", "Proposed Solution", and "Alternatives Considered"

2. Apply appropriate labels based on issue type:
   - bug ? `bug`, `needs-triage`
   - feature ? `enhancement`, `needs-discussion`
   - documentation ? `documentation`

3. Use the GitHub MCP server to create the issue in this repository

4. Return the issue URL when complete
```

**How it works:** When the GitHub MCP server is configured in `.vscode/mcp.json`, prompts with `agent` mode can invoke GitHub operations. The agent interprets the instructions and calls the appropriate MCP tool (like `mcp_github_issue_write`) to create the issue.

#### 7. Web Research Assistant (Fetch Tool)
**File:** `.github/prompts/research.prompt.md`

This example shows how prompts can use the built-in `fetch` tool to retrieve information from the web and synthesize it into actionable insights.

```markdown
---
agent: 'agent'
description: 'Research a topic and summarize findings'
model: 'Opus 4.6'
tools: ['fetch']
---

Research the following topic and provide a summary:

**Topic:** {{researchTopic}}
**Focus Areas:** {{focusAreas}}
**Depth:** {{depth}}  <!-- quick overview, detailed analysis, comprehensive report -->

## Instructions

1. Identify 3-5 authoritative sources for this topic:
   - Official documentation sites
   - Reputable tech blogs and publications
   - GitHub repositories or discussions

2. Use the fetch tool to retrieve content from these sources

3. Synthesize the information into a structured summary:
   - **Overview:** 2-3 sentence summary
   - **Key Points:** Bullet list of important findings
   - **Code Examples:** If applicable, include relevant snippets
   - **Best Practices:** Recommendations based on research
   - **Sources:** List URLs consulted

4. Highlight any conflicting information or areas of uncertainty

5. Provide actionable next steps based on the research
```

**How it works:** The `fetch` tool is a built-in capability that allows the agent to retrieve webpage content. When combined with prompts, it enables research workflows that gather external information and synthesize it for your specific context.

#### 8. Skill Creator with Live Documentation (Advanced)
**File:** `.github/prompts/create-skill.prompt.md`

This advanced example demonstrates how to create a prompt that fetches authoritative documentation before generating a skill. By pulling the latest specs from official sources, the generated skill always follows current best practices.

```markdown
---
agent: 'agent'
description: 'Create a new Agent Skill with live documentation lookup'
model: 'Opus 4.6'
tools: ['fetch', 'createFile']
---

Create a new Agent Skill for: **{{skillPurpose}}**

## Phase 1: Gather Current Documentation

Before creating anything, fetch the latest specifications from authoritative sources:

1. **Agent Skills Specification** — Fetch from agentskills.io:
   - https://agentskills.io/spec — Core specification
   - https://agentskills.io/home — Overview and examples

2. **VS Code Copilot Documentation** — Fetch from code.visualstudio.com:
   - https://code.visualstudio.com/docs/copilot/copilot-customization — Customization overview
   - https://code.visualstudio.com/docs/copilot/copilot-extensibility-overview — Extensibility patterns

3. **GitHub Copilot Documentation** — Fetch from docs.github.com:
   - https://docs.github.com/en/copilot/customizing-copilot — Customization guide

Synthesize these sources to understand:
- Required SKILL.md frontmatter fields
- Name validation rules (lowercase, hyphens, no consecutive hyphens)
- Description best practices (what + when + keywords)
- Recommended section structure

## Phase 2: Create the Skill

Based on the fetched documentation and the user's purpose, create:

### Directory Structure
```
.github/skills/{{skill-name}}/
+-- SKILL.md
+-- [any supporting files if needed]
```

### SKILL.md Requirements

**Frontmatter:**
- `name`: Lowercase with hyphens, matches directory name
- `description`: 1-1024 chars, explicitly states WHAT and WHEN to use
- `metadata`: Include author and version

**Content Sections:**
1. **When to Use This Skill** — Trigger conditions (bulleted list)
2. **Prerequisites** — Required tools, dependencies, setup
3. **Instructions** — Step-by-step guidance with code examples
4. **Common Patterns** — Table of frequent use cases
5. **Edge Cases** — Gotchas and how to handle them

## Phase 3: Validate

Before finalizing, verify:
- [ ] Name follows validation rules from agentskills.io spec
- [ ] Description includes action keywords users would say
- [ ] Instructions are specific enough for the agent to execute
- [ ] Examples use realistic, copy-paste-ready code
- [ ] No placeholder text remains

## Output

1. Create the skill directory and SKILL.md file
2. Report which documentation sources were consulted
3. Summarize key decisions made based on the spec
4. Suggest a test prompt to verify the skill activates correctly
```

**How it works:** This prompt combines multiple capabilities:
- **Fetch** retrieves live documentation, ensuring the skill follows the latest spec
- **CreateFile** generates the skill structure
- **Multi-phase workflow** separates research from creation for better results

The result is a skill created with full awareness of current best practices, not just training data.

### Using Prompt Files

In VS Code, type `/` in Copilot Chat to view available prompts. Select a prompt, provide values for any variables, and Copilot executes the defined workflow.

---

## How to Create Effective Prompts

This section covers the process of creating well-structured prompt files using VS Code's built-in tools and agent-driven iteration.

### Creating via the Configure Menu (Recommended)

1. In the Chat view, click the **gear icon** (Configure Chat)
2. Select an option that opens the prompt file picker
3. Choose **New prompt file...** from the picker
4. Select the storage location:
   - **Workspace:** `.github/prompts/` folder (shared with team via version control)
   - **User Profile:** Personal prompts across all workspaces
5. Provide a name for the prompt
6. Use the agent to generate and refine the prompt content

### Agent-Driven Prompt Creation (Best Practice)

Rather than manually writing prompt files, use Copilot to generate them:

> **?? Try this prompt:**
>
> *Create a prompt file at .github/prompts/new-api-route.prompt.md that:*
> *- Generates REST API routes with validation*
> *- Uses agent mode with Claude Opus 4.6*
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
model: 'Opus 4.6'                # Optional: specific model
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
| `agent` | Create files, edit files, run commands | Any task that modifies code — scaffolding, bug fixes, refactoring |
| Custom agent | Use that agent's persona and tools | Specialized workflows with defined behavior |

**Note:** An `edit` mode exists but is not recommended. Use `agent` instead for any file modifications.

### Implement Variables

Use `{{variableName}}` syntax to create parameterized prompts:

```markdown
---
agent: 'agent'
description: 'Create a new {{frameworkType}} hook'
model: 'Opus 4.6'
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
model: 'Opus 4.6'
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

> **?? Try this prompt:**
>
> *Create a new prompt file at `.github/prompts/{{promptName}}.prompt.md`.*
>
> *Prompt Requirements:*
> *- Purpose: {{purposeDescription}}*
> *- Mode: `ask` (read-only) or `agent` (makes changes)*
> *- Model: Opus 4.6 (or specify)*
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

> **?? Try this prompt:**
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

[? File-Based Instructions](part-2-2-file-based-instructions.md) | [Next: Skills ?](part-2-4-skills.md)
