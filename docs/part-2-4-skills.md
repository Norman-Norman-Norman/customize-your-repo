# Skills

[? Prompt Files](part-2-3-prompts.md) | [Part II Overview](part-2-primitives.md)

---

## Overview

Skills represent discrete capabilities that Copilot can invoke when contextually relevant. Unlike always-on instructions (which consume context on every request), **skills only load into context when their description matches the user's request**. This keeps context lean for routine tasks while enabling deep domain knowledge when needed.

Unlike prompts (which users invoke explicitly via `/`), skills activate automatically based on description matching.

**Location:** `.github/skills/`
**Loading:** Description match  on-demand
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
+-- skills/
    +-- image-manipulation/
    —   +-- SKILL.md
    +-- github-issues/
    —   +-- SKILL.md
    —   +-- templates/
    —       +-- bug-report.md
    —       +-- feature-request.md
    +-- web-testing/
        +-- SKILL.md
        +-- scripts/
            +-- playwright-setup.sh
```

**Minimum structure:**
```
skill-name/
+-- SKILL.md          # Required: instructions + metadata
```

**Complex skill with resources:**
```
skill-name/
+-- SKILL.md          # Required: main instructions
+-- scripts/          # Optional: executable code (Python, Bash, JS)
+-- references/       # Optional: additional documentation
+-- assets/           # Optional: templates, images, data files
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

? **Valid:**
- `image-manipulation`
- `github-issues`
- `web-testing`

? **Invalid:**
- `Image-Manipulation` (uppercase not allowed)
- `-image` (cannot start with hyphen)
- `image-` (cannot end with hyphen)
- `image--manipulation` (consecutive hyphens not allowed)

### Complete Example: GitHub Issues Skill

A skill that uses templates and integrates with the GitHub MCP:

**Directory structure:**
```
github-issues/
+-- SKILL.md
+-- templates/
    +-- bug-report.md
    +-- feature-request.md
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

`templates/bug-report.md`:

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

`templates/feature-request.md`:

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

The agentskills.io specification defines a portable format for packaging agent capabilities. But writing SKILL.md files by hand — with correct frontmatter, proper name validation, good descriptions, organized sections — is tedious. The solution? A skill that knows how to create skills.

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
+-- create-agent-skill/
    +-- SKILL.md
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

> ?? Try this prompt:
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

> ?? Try this prompt:
>
> `Create a skill for Kubernetes deployments. It should cover kubectl commands, common YAML patterns, and debugging pods. Include a scripts/ directory for helper scripts.`

The skill-creator uses your requirements to generate a more comprehensive skill:

```
kubernetes-deployments/
+-- SKILL.md
+-- scripts/
    +-- check-pod-status.sh
    +-- rollback-deployment.sh
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

This is how you scale AI capabilities across an organization — not by writing longer instruction files, but by building composable skills that activate when needed.

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

If you find yourself thinking "this is useful, but it doesn't need to be in context all the time" — that's a skill.

### Skills vs. File-Based Instructions: Overlapping Territory

Here's the honest truth: **skills and file-based instructions have significant overlap, and that's okay.**

Both primitives emerged from real needs as the AI coding assistant space evolved. File-based instructions use glob patterns (`applyTo: 'src/api/**/*'`) to load context when working on matching files. Skills use description matching to load context when the user's intent matches. Sometimes the same knowledge could reasonably live in either place.

**There is no definitively "right" answer.** As of January 2026, this is still an emerging space. The boundaries between primitives are fuzzy, and that's by design — it gives teams flexibility to organize knowledge in ways that match their workflows.

#### When They Overlap

Consider "API development guidelines." You could implement this as:

**File-based instruction** (`.github/instructions/api-routes.instructions.md`):
```yaml
applyTo: 'src/api/**/*'
```
Activates when editing files in `src/api/`.

**Skill** (`.github/skills/api-development/SKILL.md`):
```yaml
description: 'Guidelines for building REST APIs. Use when creating endpoints, handling authentication, or designing API responses.'
```
Activates when the user asks about API design, regardless of which file is open.

Both approaches work. Neither is wrong.

#### Practical Guidance (Not Rules)

| Scenario | Consider | Reasoning |
|----------|----------|-----------|
| Knowledge tied to specific files/folders | File-based instruction | Glob patterns match the context naturally |
| Knowledge tied to user intent/task | Skill | Description matching aligns with what the user is trying to do |
| Need portability across AI agents | Skill | agentskills.io spec works in VS Code, GitHub CLI, coding agent |
| Need supporting files (templates, scripts) | Skill | Skills are directories; instructions are single files |
| Want automatic activation by file location | File-based instruction | `applyTo` is deterministic |
| Want automatic activation by conversation | Skill | Description matching is semantic |

#### The Experimentation Mindset

Our recommendation: **try both and see what works for your team.**

Some patterns that teams have found useful:
- Use file-based instructions for "rules when editing X" (linting rules for tests, conventions for components)
- Use skills for "how to do Y" (workflows, multi-step processes, domain knowledge)
- Start with file-based instructions (simpler), graduate to skills when you need portability or supporting files

If you're unsure, start somewhere. You can always refactor later — these are just markdown files in version control. The cost of experimenting is low, and you'll learn what works for your specific codebase and team.

**The goal isn't to pick the "correct" primitive. The goal is to get useful context to the AI when it needs it.** If your current approach does that, it's working.

#### A Mental Model for Choosing

When you have new knowledge to encode, ask yourself these questions:

```
Is this needed on EVERY request?
+-- Yes ? Always-on instructions (.github/copilot-instructions.md)
—         BUT check if your instructions file is getting overloaded.
—         If it's huge, consider moving specialized content elsewhere.
—
+-- No ? Is this reusable across multiple contexts/files?
         +-- Yes ? Skill (.github/skills/)
         —         Skills shine when the same knowledge applies
         —         in multiple places throughout the repo.
         —
         +-- No ? File-based instruction (.github/instructions/)
                  Good for single-purpose rules tied to specific
                  file patterns that won't be needed elsewhere.
```

**Our current recommendation (January 2026):** Start with skills as your default. Skills are:
- Portable across AI agents (VS Code, GitHub CLI, coding agent)
- Only loaded when relevant (keeps context lean)
- Self-contained directories (can include templates, scripts, examples)
- Easy to share across repos or with the community

Use always-on instructions for the core stuff that truly applies everywhere — your tech stack, universal coding conventions, security requirements. Use file-based instructions when you have rules that are genuinely file-pattern-specific and won't be reused.

> **?? Editor's Note:** We're still learning what works best. The primitives overlap because this space is evolving rapidly. GitHub and the community are actively experimenting with these patterns. What we recommend today may shift as we learn more. The best approach is to try things, see what helps your team, and share what you learn.

### How Skills Load: Description Matching

Unlike file-based instructions (which use `applyTo` patterns), skills load **on-demand via description matching**. Here's what happens under the hood:

1. **Every system prompt includes a list of available skills** — just their names and descriptions
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
- Keep it scannable — the agent reads many descriptions to make a decision

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

VS Code's **Chat Diagnostics** reveals exactly what skills and instructions are loaded. This is invaluable for understanding why a skill did or didn't activate:

1. In the Chat view, click the **gear icon** (Configure Chat)
2. Select **Diagnostics**
3. Review the loaded configuration showing:
   - List of available skills (with descriptions)
   - List of available instructions
   - Configuration status and any errors

For deeper inspection, use **Developer: Log Chat Input History** or **Developer: Inspect Chat Model** from the Command Palette.

When you see a skill wasn't activated, check whether its description clearly matches what the user asked for.

### Skills vs. MCP Servers: When to Use Which

Skills and MCP servers are complementary, not competing. **You can and should use them together.** The question isn't "which one?" — it's "what does each contribute?"

- **MCP servers** provide *access* — authentication, API connections, external tool integration
- **Skills** provide *knowledge* — templates, conventions, workflows, domain expertise

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

**Git Operations ? Skill**

Git is almost universally installed on developer machines. A skill can invoke git commands directly without needing external authentication.

**Directory:** `.github/skills/git-workflow/SKILL.md`

```
---
name: git-workflow
description: Execute Git operations including commits, branching, rebasing, and history analysis. Use when the user wants to commit changes, create branches, view git history, resolve merge conflicts, or perform interactive rebases.
---

# Git Workflow

## When to use this skill

Use this skill when:
- User wants to commit, stage, or unstage changes
- User needs to create, switch, or delete branches
- User asks about git history or blame
- User wants to rebase, cherry-pick, or resolve conflicts

## How to commit changes

**Always run tests before committing.** This ensures broken code doesn't enter the repository.

1. Run tests first: `npm test` (or `yarn test`, `pnpm test`, `pytest`, `go test ./...`)
2. If tests pass, stage changes: `git add -A` (all) or `git add -p` (interactive)
3. Commit with conventional format: `git commit -m "feat(scope): description"`
4. For fixups: `git commit --amend`

If tests fail, fix the issues before committing. Never skip tests.

## How to run tests

Detect and run the project's test command:

- Node.js: `npm test` or check `package.json` scripts
- Python: `pytest` or `python -m pytest`
- Go: `go test ./...`
- Rust: `cargo test`
- Java/Kotlin: `./gradlew test` or `mvn test`

For quick validation: `npm run lint && npm test`

## How to manage branches

Create and switch: `git checkout -b feature/branch-name`
Delete merged: `git branch -d branch-name`
List all: `git branch -a`

## How to view history

Recent commits: `git log --oneline -20`
File history: `git log --follow -p filename`
Who changed a line: `git blame filename`

## How to rebase

Interactive rebase: `git rebase -i HEAD~n`
Resolve conflicts: Edit files, then `git add` and `git rebase --continue`
```

---

**Jira ? Skill + MCP Server**

This is an ideal hybrid case. The **MCP Server** handles authentication and Jira API access. The **Skill** provides team-specific templates, workflows, and conventions.

**Directory:** `.github/skills/jira-workflow/`

```
jira-workflow/
+-- SKILL.md              # Main skill instructions and metadata
+-- templates/
    +-- bug.md            # Bug report template
    +-- story.md          # User story template
    +-- epic.md           # Epic template
    +-- task.md           # Task template
```

**SKILL.md:**

```
---
name: jira-workflow
description: Create and manage Jira issues following team conventions. Use when the user wants to file a bug, create a story, plan an epic, or manage sprint work. Ensures consistent formatting and required fields.
---

# Jira Workflow

## When to use this skill

Use this skill when:
- User wants to create a Jira ticket
- User found a bug and wants to report it
- User wants to create a user story or epic
- User mentions "file a ticket", "create a Jira", or "plan a sprint"

## Required tools

This skill requires the Jira MCP server for API access. The skill provides conventions; the MCP server provides the connection.

## Issue type mapping

Read the appropriate template based on issue type:

| Issue Type | Template | Jira Type | Default Priority |
|------------|----------|-----------|------------------|
| Bug | templates/bug.md | Bug | High |
| Story | templates/story.md | Story | Medium |
| Epic | templates/epic.md | Epic | Medium |
| Task | templates/task.md | Task | Medium |

## Team conventions

- All stories must have acceptance criteria
- Epics must link to their child stories
- Bugs must include reproduction steps
- Use story points: 1, 2, 3, 5, 8, 13
- Sprint prefix: `SPRINT-XX:`

## Instructions

1. Determine issue type from user's description
2. Read the matching template from templates/
3. Fill in the template with user-provided details
4. Set appropriate fields (priority, story points, sprint)
5. Use the Jira MCP server to create the issue
6. Return the issue key and URL to the user
```

**templates/story.md:**

```
## User Story

**As a** [type of user]
**I want** [goal or desire]
**So that** [benefit or value]

## Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Technical Notes

[Implementation details, dependencies, or constraints]

## Story Points

[1, 2, 3, 5, 8, or 13]

## Definition of Done

- [ ] Code complete and reviewed
- [ ] Tests passing
- [ ] Documentation updated
```

**templates/epic.md:**

```
## Epic Summary

[High-level description of the initiative]

## Business Value

[Why this matters to the organization]

## Success Metrics

- Metric 1: [target]
- Metric 2: [target]

## Child Stories

- [ ] Story 1: [description]
- [ ] Story 2: [description]
- [ ] Story 3: [description]

## Dependencies

[External teams, systems, or blockers]

## Timeline

- Start: [date]
- Target completion: [date]
```

**Creating the issue:**

Once you've built the issue content from the template, use the MCP tool to create it:

```
Use mcp_jira_create_issue with:
- project: "MYPROJECT"
- issueType: "Story"
- summary: [title from template]
- description: [formatted template content]
- priority: "Medium"
- customFields: { "story_points": 5 }
```

**Why both?** The MCP server handles Jira authentication and API calls. The skill ensures every ticket follows team conventions — consistent formatting, required fields, proper story point values. The skill is your process; the MCP is your connection.

---

**File System Operations ? Skill**

The file system is local and doesn't require authentication. A skill provides enhanced operations that work across different AI agents.

**Directory:** `.github/skills/file-operations/SKILL.md`

```
---
name: file-operations
description: Advanced file system operations including log analysis, directory searching, and batch file processing. Use when the user needs to search files, analyze logs, find patterns across directories, or run local scripts.
---

# File System Operations

## When to use this skill

Use this skill when:
- User wants to search for files by name or content
- User needs to analyze log files for errors or patterns
- User wants to batch rename or process files

## How to analyze logs

Find errors: `grep -r "ERROR\|FATAL" logs/ --include="*.log" | tail -50`
Errors with context: `grep -B 2 -A 5 "ERROR" app.log`

## How to search directories

Find by name: `find . -name "*.ts" -type f`
Find by content: `grep -rl "searchTerm" src/`
Find large files: `find . -size +10M -type f`
Recent files: `find . -mtime -1 -type f`

## How to batch process files

Rename extension: `for f in *.jpeg; do mv "$f" "${f%.jpeg}.jpg"; done`
Delete node_modules: `find . -name "node_modules" -type d -prune -exec rm -rf {} +`
Count lines: `wc -l filename`
Disk usage: `du -sh directory`
```

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

[? Prompt Files](part-2-3-prompts.md) | [Next: Custom Agents ?](part-2-5-custom-agents.md)
