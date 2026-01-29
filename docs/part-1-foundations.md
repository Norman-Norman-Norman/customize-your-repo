# Part I: Foundations

[← Back to Guide](../ReadMe.md)

---

## Introduction

Imagine your first day at a new company. Someone hands you a laptop, points at a million lines of code, and says "add a feature to checkout." No architecture docs. No style guide. No one to ask why the last three developers quit.

You start reading. In one file, 2+2 equals 5. In another file, 2+2 equals 0. Someone invented their own math system three years ago and never documented it. The function called `save()` actually deletes things. The `UserService` doesn't handle users—that's in `AccountManager`, except when it's in `ProfileHandler`, except on Tuesdays.

None of this matches anything you learned. It's not wrong, exactly—it's *bespoke*. Someone built a custom framework on top of a custom ORM on top of a custom router, and the only person who understood it left for a startup in 2019.

Now someone asks you to make a change. You do your best. You give them something reasonable based on everything you've read. They get mad. *"Why would you use Redux? We use React Query! It's obvious!"* 

It wasn't obvious. It was in a file you hadn't opened yet.

**That's Copilot's reality—*every single time you open a file.***

It's not hallucinating. It's doing exactly what you'd do: making the best possible guess from incomplete context. The suggestions you reject? Those are reasonable choices for a codebase Copilot hasn't been taught to understand. The "wrong" answers? They're only wrong because Copilot doesn't know your team decided to do things differently.

This guide fixes that.

**The right mental model: Copilot is a new developer on your team.**

Think about what makes code easy for a new team member to work with:
- Easy to read, well-formed code
- Small, modular functions with clear responsibilities
- Good naming conventions (not everything called `i`, `temp`, or `data`)
- Comprehensive tests that document expected behavior
- Clear architectural patterns

These same qualities make code easier for Copilot to understand and extend correctly. Code that's maintainable for humans is maintainable for AI. Code that confuses junior developers will confuse Copilot too.

### The Three Pillars of Copilot Success

Copilot's output quality depends on three factors:

| Pillar | Impact | What You Control |
|--------|--------|------------------|
| **Model Selection** | The underlying reasoning capability | Choose the right model for the task (Claude Sonnet 4 for complex reasoning, GPT-4o for speed) |
| **Codebase Quality** | How well Copilot can understand your code | Write clean, well-documented, modular code |
| **Repository Configuration** | The context and rules Copilot operates with | **This guide**  the six customization primitives |

Your **model selection** matters  different models excel at different tasks. Claude Sonnet 4 handles complex multi-step reasoning exceptionally well. GPT-4o provides faster responses for simpler tasks. Choosing the right model for your workflow amplifies everything else.

Your **codebase quality** matters  a well-structured codebase with clear naming, small functions, and comprehensive tests gives Copilot better context to work with. Messy, tangled code confuses AI just as much as it confuses human developers.

But even with the best model and cleanest codebase, **repository configuration** is what transforms Copilot from a generic assistant into a team-aware partner. This guide is the complete reference for setting up your repository for the best possible outcomes with GitHub Copilot.

---

This guide is structured in two parts:

**Part 1: Tuning Copilot for Your Current Codebase**
This guide focuses on the customization primitives that help Copilot understand your existing codebaseyour conventions, patterns, and preferences. These tools work with your code as it exists today.

**Part 2: Refactoring for Better AI Collaboration** *(Coming Soon)*
The companion guide will cover how to refactor and restructure code so AI agents have an easier time understanding and modifying it. Better code organization benefits both human developers and AI assistants.

When properly configured, Copilot can:

- Respect team coding conventions automatically
- Follow architectural patterns without prompting
- Avoid deprecated libraries and anti-patterns
- Generate code that passes PR review on the first try

This guide provides a comprehensive walkthrough of every customization primitive available in GitHub Copilot. By the end, development teams will have the knowledge to transform Copilot from a generic assistant into a context-aware team member that understands the nuances of their specific codebase.

---

## Why Customize?

### The Cost of Not Customizing

Without customization, every Copilot interaction starts from zero. The AI doesn't know:
- Your team uses React Query, not Redux
- Tests should use Vitest, not Jest
- All API responses must follow your envelope format
- Certain libraries are deprecated

This information gets repeated in prompts, ignored in suggestions, or caught in PR review. Each instance costs time.

### The "New Developer" Mental Model

The most useful way to think about Copilot is as a highly capable new developer who joined your team five minutes ago.

Like any new developer, Copilot:
- Has strong general knowledge but doesn't know your specific patterns
- Will write correct code that doesn't match your conventions
- Makes reasonable assumptions that may conflict with team decisions
- Improves dramatically when given context about how things work here

The customization primitives are your onboarding documentation for this AI team member.

### ROI of Customization

Teams that configure Copilot extensively report measurable improvements:

| Metric | Without Customization | With Customization |
|--------|----------------------|-------------------|
| PR review friction | High — repeated convention corrections | Low — code matches standards |
| Code consistency | Variable — depends on who's prompting | Consistent — same patterns everywhere |
| Onboarding time | Long — new devs reinvent prompts | Fast — prompts and agents ready |
| Context switching | Frequent — explain stack each time | Minimal — AI knows the codebase |

The investment is front-loaded: a few hours of configuration produces ongoing returns across every Copilot interaction.

### The 80/20 Rule

Most teams will get 80% of the value from 20% of the configuration:

1. **Always-on instructions file** (day 1) — Define tech stack, conventions, anti-patterns
2. **3-5 prompt files** (week 1) — Automate common tasks: component scaffolding, test generation, documentation
3. **1-2 custom agents** (month 1) — Code reviewer, architect for design discussions

Skills, MCP, and advanced configurations provide additional value for specific use cases, but the foundation above transforms most workflows immediately.

---

## The Customization Primitives Overview

GitHub Copilot provides six distinct customization primitives. Each serves a specific purpose and loads at different points in the interaction lifecycle.

### The Six Primitives at a Glance

| Primitive | Location | Loading | Best For |
|-----------|----------|---------|----------|
| [**Always-on Instructions**](part-2-primitives.md#always-on-instructions) | `.github/copilot-instructions.md` | Every session | Codebase guardrails |
| [**File-based Instructions**](part-2-primitives.md#file-based-instructions) | `.github/instructions/*.instructions.md` | Pattern match | Area-specific rules |
| [**Prompts (Slash Commands)**](part-2-primitives.md#prompt-files-slash-commands) | `.github/prompts/*.prompt.md` | User invokes | One-shot workflows |
| [**Skills**](part-2-primitives.md#skills) | `.github/skills/*/SKILL.md` | Description match | Reusable capabilities |
| [**Custom Agents**](part-2-primitives.md#custom-agents) | `.github/agents/*.agent.md` | Top-level/subagent | Constrained workflows |
| [**MCP**](part-2-primitives.md#mcp-model-context-protocol) | `.vscode/mcp.json` | Session start | External gateways |

### How They Work Together

These primitives form a layered system:

```
┌─────────────────────────────────────────┐
│           User's Message                │
├─────────────────────────────────────────┤
│     Custom Agent (if activated)         │
├─────────────────────────────────────────┤
│    Prompt Template (if invoked)         │
├─────────────────────────────────────────┤
│   Skills (loaded by description match)  │
├─────────────────────────────────────────┤
│  File-Based Instructions (if matched)   │
├─────────────────────────────────────────┤
│       Always-On Instructions            │
├─────────────────────────────────────────┤
│    MCP Tools (available throughout)     │
└─────────────────────────────────────────┘
```

Each layer adds specificity. Always-on instructions provide the foundation; file-based instructions add context for specific file types; prompts define complete tasks; custom agents modify behavior for entire sessions.

### What Each Primitive Does Best

| Primitive | Best For | Not Ideal For |
|-----------|----------|---------------|
| **Always-on Instructions** | Universal rules, tech stack, conventions | Specialized capabilities, external integrations |
| **File-based Instructions** | Language-specific rules, area conventions | Global rules, task automation |
| **Prompts** | Repeatable tasks, team workflows | Persistent behavior changes |
| **Skills** | Specialized capabilities, cross-tool knowledge | Simple rules, always-needed context |
| **Custom Agents** | Personas, constrained workflows, specialized roles | One-off tasks |
| **MCP** | External APIs, live data, tool integration | Knowledge, conventions, patterns |

---

## Understanding Context and Loading

### When Each Primitive Loads

Understanding when each primitive enters Copilot's context is essential for effective configuration:

| Primitive | When It Loads | Context Persistence |
|-----------|---------------|---------------------|
| Always-on Instructions | Session start, every request | Entire session |
| File-based Instructions | When working on matching files | While pattern matches |
| Prompts | When explicitly invoked (`/promptname`) | Single request |
| Skills | When description matches user intent | Single request |
| Custom Agents | When activated by user | Until switched |
| MCP | Session start (tools available) | Entire session |

### Context Window Considerations

Every primitive consumes tokens from the context window. Overloading instructions reduces space for actual code and conversation.

**Guidelines:**
- Always-on instructions: 500-2000 words
- File-based instructions: 200-500 words each
- Prompt files: As long as needed (single-use)
- Skills: 500-3000 words (loaded on-demand)
- Custom agents: 200-1000 words

If Copilot seems to "forget" rules from instructions, the file may be too long. Move specialized content to file-based instructions or skills.

### The Loading Lifecycle

```
1. Session Start
   ├── Load always-on instructions
   ├── Load MCP server configurations
   └── Initialize available tools

2. User Opens File
   ├── Check applyTo patterns
   └── Load matching file-based instructions

3. User Sends Message
   ├── Check for prompt invocation (/name)
   ├── Match skills by description
   ├── Check for custom agent activation
   └── Combine all relevant context

4. Generate Response
   └── Apply all loaded context to response
```

### Debugging What's Loaded

VS Code provides a debug view to see exactly what context is active:

1. Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Run **"Developer: Show Chat Debug View"**
3. Send a message in Copilot Chat
4. Click on the request to expand details

The debug view shows:
- System prompt content
- List of active instructions
- Available skills and tools
- Token usage breakdown

This is invaluable for understanding why Copilot is or isn't following certain rules.

---

## Complete Setup Checklist

### Prerequisites

Before configuring repository customizations:

- [ ] GitHub Copilot license (individual, business, or enterprise)
- [ ] VS Code with GitHub Copilot extension (v1.0+)
- [ ] Repository with write access
- [ ] VS Code workspace trust enabled for the repository

### Repository Setup (Step-by-Step)

**Day 1: Create Foundation**

```bash
# Create the customization directories
mkdir -p .github/instructions
mkdir -p .github/prompts
mkdir -p .github/skills
mkdir -p .github/agents
mkdir -p .vscode

# Create starter files
touch .github/copilot-instructions.md
touch .vscode/mcp.json
```

**File Structure After Setup:**

```
your-repo/
├── .github/
│   ├── copilot-instructions.md      # Step 1: Create this first
│   ├── instructions/                 # Step 2: Add file-specific rules
│   │   └── .gitkeep
│   ├── prompts/                      # Step 3: Add reusable workflows
│   │   └── .gitkeep
│   ├── skills/                       # Step 4: Add specialized capabilities
│   │   └── .gitkeep
│   └── agents/                       # Step 5: Add personas
│       └── .gitkeep
├── .vscode/
│   ├── mcp.json                      # Step 6: Configure integrations
│   └── settings.json                 # VS Code settings
└── [your code...]
```

### VS Code Configuration

Add these settings to `.vscode/settings.json` for optimal Copilot usage:

```json
{
  "github.copilot.chat.codeGeneration.useInstructionFiles": true,
  "chat.promptFiles": true,
  "chat.customAgents.enabled": true
}
```

### Team Onboarding

When rolling out to a team:

1. **Document the customizations** — Add a section to README.md explaining what's configured
2. **Provide prompt examples** — Show team members how to invoke custom prompts
3. **Create a feedback channel** — Collect suggestions for new prompts and instruction improvements
4. **Review quarterly** — Instructions drift as codebases evolve

### Verification Steps

After setup, verify everything works:

1. **Instructions loading:**
   - Open Copilot Chat
   - Ask: "What coding standards should I follow?"
   - Response should reference content from instructions file

2. **Prompts available:**
   - Type `/` in chat
   - Custom prompts should appear in list

3. **Debug view working:**
   - Run "Developer: Show Chat Debug View"
   - Send a message
   - Verify you can see loaded context

If any verification fails, check:
- File names match exactly (case-sensitive)
- Files are in correct directories
- VS Code window was reloaded after creating files

---

[Next: Part II - The Six Primitives →](part-2-primitives.md)
