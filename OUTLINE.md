# The Definitive Guide to Customizing Your Repo for GitHub Copilot
## Complete Outline

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

### The Three Pillars of Copilot Success

Copilot's output quality depends on three factors:

| Pillar | Impact | What You Control |
|--------|--------|------------------|
| **Model Selection** | The underlying reasoning capability | Choose the right model for the task (Claude Sonnet 4 for complex reasoning, GPT-4o for speed) |
| **Codebase Quality** | How well Copilot can understand your code | Write clean, well-documented, modular code |
| **Repository Configuration** | The context and rules Copilot operates with | **This guide** — the six customization primitives |

Your **model selection** matters — different models excel at different tasks. Claude Sonnet 4 handles complex multi-step reasoning exceptionally well. GPT-4o provides faster responses for simpler tasks. Choosing the right model for your workflow amplifies everything else.

Your **codebase quality** matters — a well-structured codebase with clear naming, small functions, and comprehensive tests gives Copilot better context to work with. Messy, tangled code confuses AI just as much as it confuses human developers.

But even with the best model and cleanest codebase, **repository configuration** is what transforms Copilot from a generic assistant into a team-aware partner. This guide is the complete reference for setting up your repository for the best possible outcomes with GitHub Copilot.

---

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

### Part I: Foundations
1. [Why Customize?](#why-customize)
   - 1.1 The Cost of Not Customizing
   - 1.2 The "New Developer" Mental Model
   - 1.3 ROI of Customization
   - 1.4 The 80/20 Rule
2. [The Customization Primitives Overview](#the-customization-primitives-overview)
   - 2.1 The Six Primitives At a Glance
   - 2.2 How They Work Together
   - 2.3 What Each Primitive Does Best
3. [Understanding Context and Loading](#understanding-context-and-loading)
   - 3.1 When Each Primitive Loads
   - 3.2 Context Window Considerations
   - 3.3 The Loading Lifecycle
   - 3.4 Debugging What's Loaded
4. [Complete Setup Checklist](#complete-setup-checklist)
   - 4.1 Prerequisites
   - 4.2 Repository Setup (Step-by-Step)
   - 4.3 VS Code Configuration
   - 4.4 Team Onboarding
   - 4.5 Verification Steps

### Part II: The Six Primitives

5. [Always-On Instructions](#1-always-on-instructions)
   - 5.1 Overview & Location
   - 5.2 When to Use
   - 5.3 Anatomy of Effective Instructions
   - 5.4 Including Rationale (Appraisals)
   - 5.5 Complete Production Example
   - 5.6 Creating via VS Code Configure Menu
   - 5.7 Agent-Driven Generation
   - 5.8 Using Copilot Coding Agent (GitHub.com)
   - 5.9 Gathering Team Knowledge
   - 5.10 The "Good vs Bad" Pattern
   - 5.11 Iterating with the Agent
   - 5.12 Validating the Configuration
   - 5.13 Anti-Patterns to Avoid
   - 5.14 Sample Prompts for Creation

6. [File-Based Instructions](#2-file-based-instructions)
   - 6.1 Overview & Location
   - 6.2 File Format & Frontmatter Fields
   - 6.3 Pattern Matching with `applyTo`
   - 6.4 Use Cases (Frontend vs Backend, Tests, Monorepos)
   - 6.5 Complete Examples
   - 6.6 Creating File-Based Instructions
   - 6.7 Anti-Patterns to Avoid
   - 6.8 Sample Prompts for Creation

7. [Prompt Files (Slash Commands)](#3-prompt-files-slash-commands)
   - 7.1 Overview & Location
   - 7.2 File Format & Frontmatter Fields
   - 7.3 Execution Modes (`ask` vs `agent`)
   - 7.4 Essential Prompts Every Repo Needs
   - 7.5 Using Prompt Files
   - 7.6 Creating via Configure Menu
   - 7.7 Agent-Driven Prompt Creation
   - 7.8 Implementing Variables
   - 7.9 Referencing Instructions Files
   - 7.10 Meta-Prompt for Creating Prompts
   - 7.11 Prompt Quality Comparison
   - 7.12 Anti-Patterns to Avoid
   - 7.13 Complete Prompt Library (15+ Examples)

8. [Skills](#4-skills)
   - 8.1 Overview & Location
   - 8.2 What Are Agent Skills?
   - 8.3 The agentskills.io Specification
   - 8.4 Directory Structure
   - 8.5 SKILL.md Format & Frontmatter
   - 8.6 Name Validation Rules
   - 8.7 Complete Example: GitHub Issues Skill
   - 8.8 Bootstrap Skills with a Skill-Creator Skill
   - 8.9 Why Skills Instead of Always-On Instructions?
   - 8.10 How Skills Load: Description Matching
   - 8.11 What the Spec Controls (and Doesn't)
   - 8.12 Debugging Skills: See What's Loaded
   - 8.13 Skills vs. MCP Servers: When to Use Which
   - 8.14 Complete Skill Examples (5+ Skills)
   - 8.15 Sample Prompts for Skill Creation

9. [Custom Agents](#5-custom-agents)
   - 9.1 Overview & Location
   - 9.2 When to Use Custom Agents
   - 9.3 File Format & Frontmatter Fields
   - 9.4 Recommended Custom Agents (8+ Examples)
   - 9.5 Sub-Agents: Context Isolation for Complex Workflows
   - 9.6 Defining Handoffs
   - 9.7 Prompt vs. Custom Agent Comparison
   - 9.8 Creating via Configure Menu
   - 9.9 Agent-Driven Iteration
   - 9.10 The Agent Formula
   - 9.11 Defining Specific Personas
   - 9.12 Defining Response Patterns
   - 9.13 Adding Guardrails
   - 9.14 Agent Creator Meta-Prompt
   - 9.15 Anti-Patterns to Avoid
   - 9.16 Sample Prompts for Agent Creation

10. [MCP (Model Context Protocol)](#6-mcp-model-context-protocol)
    - 10.1 Overview
    - 10.2 Instructions vs. MCP Capabilities
    - 10.3 Configuring MCP Servers
    - 10.4 Available MCP Servers
    - 10.5 Example Use Cases
    - 10.6 When to Use MCP vs. Other Primitives
    - 10.7 Complete Configuration Examples
    - 10.8 Troubleshooting MCP

### Part III: Putting It All Together

11. [The Decision Matrix](#the-decision-matrix)
    - 11.1 Use Always-On Instructions When...
    - 11.2 Use File-Based Instructions When...
    - 11.3 Use Prompt Files When...
    - 11.4 Use Skills When...
    - 11.5 Use Custom Agents When...
    - 11.6 Use MCP When...
    - 11.7 Decision Flow Chart
    - 11.8 Combining Primitives
    - 11.9 Common Scenarios & Solutions

12. [Interaction & Layering](#interaction-and-layering)
    - 12.1 How Primitives Stack
    - 12.2 Context Window Considerations
    - 12.3 Priority and Conflict Resolution
    - 12.4 From Global to Specific
    - 12.5 Visual Diagrams

13. [Real-World Examples](#real-world-examples)
    - 13.1 React Component Library
    - 13.2 Full-Stack SaaS Application
    - 13.3 Python Data Pipeline
    - 13.4 Monorepo with Multiple Languages
    - 13.5 Enterprise Security-Focused Setup
    - 13.6 Open Source Project
    - 13.7 Mobile App (React Native)
    - 13.8 CLI Tool

### Part IV: Workflow Recipes

14. [Workflow Recipes](#workflow-recipes)
    - 14.1 Overview: Combining Primitives for Real Workflows
    - **Issue Tracking & Ticket Management**
    - 14.2 GitHub Issues Workflow
    - 14.3 Jira Integration Workflow
    - 14.4 Azure DevOps Work Items Workflow
    - 14.5 Linear Issues Workflow
    - **Pull Request & Code Review Workflows**
    - 14.6 PR Creation with Linked Tickets
    - 14.7 Automated Code Review Assistant
    - 14.8 PR Description Generator from Commits
    - 14.9 Security-Focused PR Review
    - **Deployment & Release Workflows**
    - 14.10 Staging Deployment Workflow
    - 14.11 Production Deployment Checklist
    - 14.12 Rollback Assistant
    - 14.13 Release Notes Generator
    - 14.14 Changelog Generator
    - **CI/CD Workflows**
    - 14.15 GitHub Actions Workflow Generator
    - 14.16 Azure Pipelines Generator
    - 14.17 Build Failure Analyzer
    - 14.18 Test Failure Triage
    - **Documentation Workflows**
    - 14.19 API Documentation Generator
    - 14.20 README Generator
    - 14.21 ADR (Architecture Decision Record) Creator
    - 14.22 Runbook Generator
    - **Testing Workflows**
    - 14.23 Unit Test Generator
    - 14.24 Integration Test Scaffolder
    - 14.25 E2E Test Creator (Playwright/Cypress)
    - 14.26 Test Coverage Gap Analyzer
    - **Database Workflows**
    - 14.27 Migration Generator
    - 14.28 Seed Data Creator
    - 14.29 Query Optimizer
    - 14.30 Schema Documentation
    - **Communication & Notifications**
    - 14.31 Slack Notification Composer
    - 14.32 Teams Message Formatter
    - 14.33 Incident Report Generator
    - 14.34 Status Update Composer
    - **Sprint & Project Management**
    - 14.35 Sprint Planning Assistant
    - 14.36 Estimation Helper
    - 14.37 Dependency Mapper
    - 14.38 Technical Debt Tracker

### Part V: Implementation Guide

15. [Quick Start: Org-Wide Rollout](#quick-start-org-wide-rollout)
    - 15.1 Day 1: Create Instructions File
    - 15.2 Week 1: Add Essential Prompts
    - 15.3 Month 1: Implement Custom Agents
    - 15.4 Quarter 1: Evaluate MCP & Skills
    - 15.5 Ongoing: Review and Iterate
    - 15.6 Measuring Success

16. [Best Practices](#best-practices)
    - 16.1 Version Control All Customizations
    - 16.2 Start Small and Iterate
    - 16.3 Provide Examples (Good vs Bad)
    - 16.4 Explain Rationale
    - 16.5 Maintain Focus (Word Count Guidelines)
    - 16.6 Test Prompts Thoroughly
    - 16.7 Review Quarterly
    - 16.8 Gather Team Feedback
    - 16.9 Document Your Customizations

17. [Debugging & Troubleshooting](#debugging-and-troubleshooting)
    - 17.1 Using Chat Debug View
    - 17.2 Verifying Instructions Are Loaded
    - 17.3 Why Skills Don't Activate
    - 17.4 Troubleshooting MCP Connections
    - 17.5 Common Mistakes and Fixes
    - 17.6 Diagnostic Prompts

18. [Frequently Asked Questions](#frequently-asked-questions)
    - 18.1 Instructions vs. inline prompts?
    - 18.2 MCP vs. instructions?
    - 18.3 Prompts vs. custom agents?
    - 18.4 Multiple instructions files?
    - 18.5 How are customizations shared?
    - 18.6 VS Code settings vs. repo files?
    - 18.7 How do instructions interact with selected code?
    - 18.8 Ideal length for instructions file?
    - 18.9 How specific should prompts be?
    - 18.10 Can I use multiple agents together?
    - 18.11 What happens when primitives conflict?
    - 18.12 How do I migrate existing docs to instructions?

### Part VI: Reference

19. [Quick Reference Tables](#quick-reference-tables)
    - 19.1 The Six Primitives At a Glance
    - 19.2 Frontmatter Field Reference
    - 19.3 Execution Modes Reference
    - 19.4 Tool Access Reference
    - 19.5 File Location Reference
    - 19.6 Glob Pattern Reference

20. [Templates & Starter Files](#templates-and-starter-files)
    - 20.1 Starter copilot-instructions.md
    - 20.2 Starter File-Based Instruction
    - 20.3 Starter Prompt File
    - 20.4 Starter SKILL.md
    - 20.5 Starter Custom Agent
    - 20.6 Starter MCP Configuration

21. [Sample Prompt Library](#sample-prompt-library)
    - 21.1 Prompts for Creating Instructions
    - 21.2 Prompts for Creating Prompts
    - 21.3 Prompts for Creating Skills
    - 21.4 Prompts for Creating Agents
    - 21.5 Prompts for Debugging
    - 21.6 Prompts for Iteration

22. [Resources](#resources)
    - Official Documentation
    - Community Resources (Awesome Copilot)
    - agentskills.io Specification
    - Model Context Protocol Documentation

23. [Summary](#summary)

---

## Detailed Section Breakdown

### Part I: Foundations

#### 1. Why Customize?

- The "new developer" mental model
- What Copilot needs to succeed (context, conventions, examples)
- ROI of customization (reduced PR friction, consistent code)
- The 80/20 rule: Small configuration investment, large productivity gains

#### 2. The Customization Primitives Overview

The master reference table showing all six primitives:

| Primitive | Location | Loading | Best For |
|-----------|----------|---------|----------|
| Always-on Instructions | `.github/copilot-instructions.md` | Every session | Codebase guardrails |
| File-based Instructions | `.github/instructions/*.instructions.md` | Pattern match | Area-specific rules |
| Prompts (Slash Commands) | `.github/prompts/*.prompt.md` | User invokes | One-shot workflows |
| Skills | `.github/skills/` | Description match | Reusable capabilities |
| Custom Agents | `.github/agents/*.agent.md` | Top-level/subagent | Constrained workflows |
| MCP | `.vscode/mcp.json` | Session start | External gateways |

#### 3. Understanding Context and Loading

- When each primitive loads into context
- Context window limitations and optimization
- How primitives combine and stack
- The loading lifecycle diagram

#### 4. Complete Setup Checklist

Step-by-step setup for teams adopting GitHub Copilot customization:

**Prerequisites:**
- GitHub Copilot license (individual, business, or enterprise)
- VS Code with GitHub Copilot extension installed
- Repository with `.github/` directory

**Repository Setup - Day 1:**
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
│   ├── prompts/                      # Step 3: Add reusable workflows
│   ├── skills/                       # Step 4: Add specialized capabilities
│   └── agents/                       # Step 5: Add personas
├── .vscode/
│   ├── mcp.json                      # Step 6: Configure integrations
│   └── settings.json                 # VS Code settings
└── [your code...]
```

**Verification Steps:**
1. Open repository in VS Code
2. Open Copilot Chat (Ctrl+Shift+I / Cmd+Shift+I)
3. Ask: "What coding standards should I follow?"
4. Verify response references your instructions file
5. Type `/` to see available prompts
6. Run "Developer: Show Chat Debug View" to inspect loaded context

---

### Part II: The Six Primitives

*(Each section follows the established pattern from the current ReadMe, with expanded coverage as outlined above)*

---

### Part III: Putting It All Together

#### 10. The Decision Matrix

Decision flow chart:
```
Is it a general rule for all code?
├── YES → Always-On Instructions
└── NO → Does it apply to specific file patterns?
    ├── YES → File-Based Instructions
    └── NO → Is it a repeatable task?
        ├── YES → Prompt File
        └── NO → Is it a specialized capability?
            ├── YES → Skill
            └── NO → Do you need a specific AI persona?
                ├── YES → Custom Agent
                └── NO → Do you need external data/actions?
                    ├── YES → MCP
                    └── NO → Direct conversation
```

#### 11. Interaction & Layering

How primitives stack:
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

---

### Part IV: Workflow Recipes

This section provides comprehensive, copy-paste-ready recipes for common enterprise workflows. Each recipe shows which primitives to combine and includes complete implementation examples.

#### 14. Workflow Recipes

##### 14.1 Overview: Combining Primitives

Most real-world workflows combine multiple primitives:

| Recipe Type | Primitives Used |
|-------------|-----------------|
| Issue Tracking | MCP (API access) + Skill (templates/conventions) + Prompt (user invocation) |
| Deployments | Agent (persona) + Prompt (checklist) + MCP (execution) |
| Code Review | Agent (reviewer persona) + Instructions (standards) |
| Documentation | Prompt (generator) + Instructions (style guide) |

---

#### Issue Tracking & Ticket Management

##### 14.2 GitHub Issues Workflow

**Goal:** Create well-formatted GitHub issues with templates and automatic labeling

**Primitives Used:**
- MCP Server: `@modelcontextprotocol/server-github`
- Skill: `github-issues/` with templates
- Prompt: `/create-issue.prompt.md`

**MCP Configuration (.vscode/mcp.json):**
```json
{
  "servers": {
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

**Skill (github-issues/SKILL.md):**
```markdown
---
name: github-issues
description: Create and manage GitHub issues with team templates. Use when user wants to file a bug, request a feature, or create any GitHub issue.
---

# GitHub Issue Creation

## Bug Report Template
- Title: [BUG] Short description
- Labels: bug, needs-triage
- Required sections: Steps to Reproduce, Expected vs Actual, Environment

## Feature Request Template
- Title: [FEATURE] Short description
- Labels: enhancement, needs-discussion
- Required sections: Problem Statement, Proposed Solution, Alternatives

## Task Template
- Title: [TASK] Short description
- Labels: task
- Required sections: Description, Acceptance Criteria, Dependencies
```

**Prompt (.github/prompts/create-issue.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create a GitHub issue with proper template'
model: 'Claude Sonnet 4'
---

Create a GitHub issue for this repository:

**Issue Type:** {{issueType}} (bug/feature/task)
**Summary:** {{summary}}

1. Use the appropriate template from the github-issues skill
2. Apply correct labels based on issue type
3. Fill in all required sections
4. Assign to the appropriate team if specified
5. Link to related issues if mentioned
```

##### 14.3 Jira Integration Workflow

**Goal:** Create Jira tickets with proper fields, link to PRs, and track progress

**Primitives Used:**
- MCP Server: Jira MCP server
- Skill: `jira-workflows/` with issue type definitions
- Agent: Product owner persona for acceptance criteria

**MCP Configuration (.vscode/mcp.json):**
```json
{
  "servers": {
    "jira": {
      "command": "npx",
      "args": ["@your-org/jira-mcp-server"],
      "env": {
        "JIRA_URL": "${env:JIRA_URL}",
        "JIRA_EMAIL": "${env:JIRA_EMAIL}",
        "JIRA_API_TOKEN": "${env:JIRA_API_TOKEN}",
        "JIRA_PROJECT_KEY": "${env:JIRA_PROJECT_KEY}"
      }
    }
  }
}
```

**Skill (jira-workflows/SKILL.md):**
```markdown
---
name: jira-workflows
description: Create and manage Jira issues following team conventions. Use when user mentions Jira, tickets, stories, epics, or sprint work.
---

# Jira Issue Management

## Issue Types
| Type | Use For | Required Fields |
|------|---------|-----------------|
| Story | User-facing features | Acceptance Criteria, Story Points, Sprint |
| Bug | Defects | Steps to Reproduce, Severity, Environment |
| Task | Technical work | Description, Estimate |
| Epic | Large initiatives | Business Value, Target Release |
| Spike | Research/investigation | Time Box, Questions to Answer |

## Standard Fields
- **Project:** Always use ${JIRA_PROJECT_KEY}
- **Labels:** Use kebab-case (frontend, backend, api, database)
- **Components:** Match our service architecture
- **Fix Version:** Only set for bugs

## Acceptance Criteria Format
Use Given/When/Then format:
```
Given [precondition]
When [action]
Then [expected result]
```

## Linking Conventions
- Blocks/is blocked by: Technical dependencies
- Relates to: Same feature area
- Is caused by/causes: Bug relationships
```

**Prompt (.github/prompts/create-jira-ticket.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create a Jira ticket with all required fields'
model: 'Claude Sonnet 4'
---

Create a Jira {{issueType}} ticket:

**Title:** {{title}}
**Description:** {{description}}

Follow the jira-workflows skill conventions:
1. Use the correct issue type template
2. Add appropriate labels and components
3. Write acceptance criteria in Given/When/Then format
4. Estimate story points using Fibonacci (1, 2, 3, 5, 8, 13)
5. Link to related tickets if mentioned
6. Set priority based on business impact
```

**Agent (.github/agents/product-owner.agent.md):**
```markdown
---
name: 'Product Owner'
description: 'Writes acceptance criteria and defines requirements'
tools: ['search', 'readFile', 'fetch']
model: 'Claude Sonnet 4'
---

You are a product owner helping define clear requirements.

## Your Focus
- User value and business outcomes
- Clear, testable acceptance criteria
- Edge cases and error scenarios
- Definition of Done

## Acceptance Criteria Style
Always use Given/When/Then:
- Given: Setup and preconditions
- When: The action being tested
- Then: Observable outcome

## Questions You Ask
- Who is the user?
- What problem does this solve?
- How will we know it's working?
- What could go wrong?
```

##### 14.4 Azure DevOps Work Items Workflow

**Goal:** Create and manage Azure DevOps work items with proper hierarchy

**Primitives Used:**
- MCP Server: Azure DevOps MCP server
- Skill: `ado-workflows/` with work item templates
- Instructions: Always-on rules for ADO conventions

**MCP Configuration (.vscode/mcp.json):**
```json
{
  "servers": {
    "azure-devops": {
      "command": "npx",
      "args": ["@your-org/ado-mcp-server"],
      "env": {
        "ADO_ORG_URL": "${env:ADO_ORG_URL}",
        "ADO_PROJECT": "${env:ADO_PROJECT}",
        "ADO_PAT": "${env:ADO_PAT}"
      }
    }
  }
}
```

**Skill (ado-workflows/SKILL.md):**
```markdown
---
name: ado-workflows
description: Create Azure DevOps work items following team standards. Use when user mentions ADO, work items, user stories, PBIs, or Azure DevOps.
---

# Azure DevOps Work Item Management

## Work Item Hierarchy
```
Epic
└── Feature
    └── Product Backlog Item (PBI) / User Story
        └── Task
```

## Work Item Types

### Epic
- **When:** Quarter-level initiatives
- **Required:** Business value, target iteration range
- **Tags:** epic, [team-name]

### Feature
- **When:** Deliverable increments (1-3 sprints)
- **Required:** Acceptance criteria, business value
- **Parent:** Must link to Epic

### PBI / User Story
- **When:** Sprint-sized work
- **Required:** Description, acceptance criteria, story points
- **Format:** "As a [user], I want [goal], so that [benefit]"
- **Parent:** Must link to Feature

### Bug
- **When:** Defects in production or staging
- **Required:** Repro steps, expected vs actual, severity
- **Severity Levels:**
  - 1 - Critical: System down, data loss
  - 2 - High: Major feature broken, no workaround
  - 3 - Medium: Feature impaired, workaround exists
  - 4 - Low: Minor issue, cosmetic

### Task
- **When:** Technical subtasks
- **Required:** Description, remaining work estimate
- **Parent:** Must link to PBI/Bug

## Area Paths
Use: [Project]\\[Team]\\[Component]
Example: MyProject\\Backend\\API

## Iteration Paths
Format: [Project]\\[Year]\\Sprint [Number]
Example: MyProject\\2026\\Sprint 15

## Standard Tags
- `tech-debt` - Technical debt items
- `security` - Security-related
- `performance` - Performance improvements
- `accessibility` - A11y requirements
- `documentation` - Docs updates needed
```

**Prompt (.github/prompts/create-ado-workitem.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create an Azure DevOps work item'
model: 'Claude Sonnet 4'
---

Create an Azure DevOps {{workItemType}}:

**Title:** {{title}}
**Details:** {{details}}

Follow the ado-workflows skill:
1. Use the correct work item type
2. Set appropriate area and iteration paths
3. Write user stories in "As a/I want/So that" format
4. Include acceptance criteria
5. Link to parent items
6. Add relevant tags
7. Estimate using team's standard scale
```

##### 14.5 Linear Issues Workflow

**Goal:** Create Linear issues with proper project, team, and cycle assignment

**MCP Configuration (.vscode/mcp.json):**
```json
{
  "servers": {
    "linear": {
      "command": "npx",
      "args": ["@linear/mcp-server"],
      "env": {
        "LINEAR_API_KEY": "${env:LINEAR_API_KEY}"
      }
    }
  }
}
```

**Prompt (.github/prompts/create-linear-issue.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create a Linear issue with proper workflow state'
model: 'Claude Sonnet 4'
---

Create a Linear issue:

**Title:** {{title}}
**Team:** {{teamName}}
**Priority:** {{priority}} (Urgent/High/Medium/Low/No Priority)

Include:
1. Clear description with context
2. Acceptance criteria as a checklist
3. Appropriate labels (bug, feature, improvement, task)
4. Link to related issues
5. Assign to current cycle if ready for work
```

---

#### Pull Request & Code Review Workflows

##### 14.6 PR Creation with Linked Tickets

**Goal:** Create PRs that auto-link to tickets and follow templates

**Prompt (.github/prompts/create-pr.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create a well-documented PR from current changes'
model: 'Claude Sonnet 4'
---

Create a pull request for the current branch:

**Ticket:** {{ticketId}}
**Type:** {{changeType}} (feature/bugfix/refactor/chore)

## PR Creation Steps
1. Analyze the changes on this branch
2. Generate a descriptive title following: `[{{ticketId}}] Brief description`
3. Fill out the PR template with:
   - Summary of changes
   - Testing performed
   - Screenshots if UI changes
   - Breaking changes if any
4. Add appropriate reviewers based on CODEOWNERS
5. Link the ticket in the description
6. Apply labels: {{changeType}}, ready-for-review

## PR Description Template
### Summary
[What changed and why]

### Ticket
[{{ticketId}}](link-to-ticket)

### Changes
- Change 1
- Change 2

### Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

### Screenshots
[If applicable]

### Checklist
- [ ] Code follows project conventions
- [ ] Tests pass locally
- [ ] Documentation updated
```

##### 14.7 Automated Code Review Assistant

**Agent (.github/agents/code-reviewer.agent.md):**
```markdown
---
name: 'Code Reviewer'
description: 'Reviews code for quality, security, and best practices'
tools: ['search', 'readFile', 'usages', 'getChangedFiles']
model: 'Claude Sonnet 4'
---

You are a senior engineer performing thorough code review.

## Review Checklist (in order)
1. **Correctness** - Does the code do what it's supposed to?
2. **Security** - Any vulnerabilities introduced?
3. **Performance** - Any obvious inefficiencies?
4. **Maintainability** - Will future devs understand this?
5. **Tests** - Adequate coverage?
6. **Style** - Follows team conventions?

## Feedback Format
Use these prefixes:
- `🔴 BLOCKER:` Must fix before merge
- `🟡 SUGGESTION:` Should consider fixing
- `🔵 NIT:` Minor style/preference
- `❓ QUESTION:` Need clarification
- `✅ PRAISE:` Good pattern worth noting

## Review Comment Template
```
[EMOJI PREFIX]: [Brief title]

**Location:** [file:line]
**Issue:** [What's wrong]
**Suggestion:** [How to fix]
**Why:** [Rationale]
```

## Always Check
- Hardcoded secrets or credentials
- SQL injection vulnerabilities
- Missing input validation
- Unhandled error cases
- Missing tests for new code
- Breaking changes to public APIs
```

##### 14.8 PR Description Generator from Commits

**Prompt (.github/prompts/generate-pr-description.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate PR description from commit history'
model: 'Claude Sonnet 4'
---

Generate a PR description by analyzing:

1. The commit messages on this branch
2. The actual code changes
3. Any linked tickets mentioned in commits

## Output Format
### Summary
[2-3 sentence overview of all changes]

### Changes by Category
#### Features
- [New capabilities added]

#### Bug Fixes
- [Issues resolved]

#### Refactoring
- [Code improvements]

#### Other
- [Documentation, tests, chores]

### Technical Details
[Notable implementation decisions]

### Testing Notes
[How to verify the changes]

### Related
- Closes #[issue numbers from commits]
- Related to #[mentioned issues]
```

##### 14.9 Security-Focused PR Review

**Agent (.github/agents/security-reviewer.agent.md):**
```markdown
---
name: 'Security Reviewer'
description: 'Reviews code with focus on security vulnerabilities'
tools: ['search', 'readFile', 'usages', 'getChangedFiles']
model: 'Claude Sonnet 4'
---

You are a security engineer reviewing code for vulnerabilities.

## Security Checklist

### Authentication & Authorization
- [ ] Auth checks on all protected routes
- [ ] Session handling is secure
- [ ] Tokens properly validated
- [ ] Permissions enforced server-side

### Input Validation
- [ ] All user input sanitized
- [ ] SQL parameterized queries
- [ ] XSS prevention in place
- [ ] File uploads validated

### Data Protection
- [ ] Sensitive data encrypted at rest
- [ ] PII properly handled
- [ ] Secrets not in code
- [ ] Logging doesn't leak data

### API Security
- [ ] Rate limiting implemented
- [ ] CORS configured correctly
- [ ] API keys rotatable
- [ ] Error messages don't leak info

## Severity Classification
- **CRITICAL:** Direct exploitation possible
- **HIGH:** Exploitation with some effort
- **MEDIUM:** Limited impact or requires user action
- **LOW:** Theoretical or minimal impact

## Report Format
For each finding:
- **Vulnerability:** [CWE-ID] Description
- **Location:** [file:line]
- **Risk:** [Severity] - [Impact description]
- **Remediation:** [Specific fix]
- **Reference:** [OWASP/CWE link]
```

---

#### Deployment & Release Workflows

##### 14.10 Staging Deployment Workflow

**Prompt (.github/prompts/deploy-staging.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Deploy current branch to staging with verification'
model: 'Claude Sonnet 4'
---

Deploy to staging environment:

## Pre-Deployment Checklist
1. [ ] All tests passing locally
2. [ ] No unresolved merge conflicts
3. [ ] Feature flags configured
4. [ ] Database migrations reviewed

## Deployment Steps
1. Verify CI pipeline is green
2. Check for pending migrations
3. Trigger deployment to staging
4. Monitor deployment logs
5. Run smoke tests

## Post-Deployment Verification
1. Hit health check endpoint
2. Verify key user flows:
   - Login
   - Core feature 1
   - Core feature 2
3. Check error monitoring (Sentry/DataDog)
4. Verify metrics dashboards

## Rollback Criteria
Rollback immediately if:
- Health checks fail
- Error rate spikes >5%
- P95 latency increases >50%
- Critical user flow broken
```

##### 14.11 Production Deployment Checklist

**Prompt (.github/prompts/deploy-production.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Production deployment checklist and execution'
model: 'Claude Sonnet 4'
---

Production deployment for: {{releaseVersion}}

## Pre-Deployment (T-1 day)
- [ ] Release notes finalized
- [ ] Stakeholder sign-off obtained
- [ ] Rollback plan documented
- [ ] On-call schedule confirmed
- [ ] Support team notified

## Pre-Deployment (T-1 hour)
- [ ] Staging verified stable
- [ ] No active incidents
- [ ] Team availability confirmed
- [ ] Deployment window confirmed
- [ ] Monitoring dashboards open

## Deployment Steps
1. Enable maintenance mode (if needed)
2. Backup current state
3. Run database migrations
4. Deploy new version
5. Verify health checks
6. Run smoke tests
7. Disable maintenance mode
8. Monitor for 30 minutes

## Post-Deployment
- [ ] All health checks green
- [ ] Error rates normal
- [ ] Key metrics stable
- [ ] Announce completion to stakeholders
- [ ] Update deployment docs

## Rollback Procedure
If critical issues detected:
1. Announce rollback decision
2. Enable maintenance mode
3. Rollback deployment
4. Rollback migrations (if safe)
5. Verify previous version
6. Post-incident review scheduled
```

##### 14.12 Rollback Assistant

**Prompt (.github/prompts/rollback.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Guide through production rollback'
model: 'Claude Sonnet 4'
---

Initiating rollback due to: {{rollbackReason}}

## Immediate Actions
1. Confirm rollback decision with tech lead
2. Note current version: {{currentVersion}}
3. Target rollback version: {{targetVersion}}

## Rollback Steps
### 1. Communication
- Alert team in #deployments channel
- Notify support team
- Update status page

### 2. Stop the Bleeding
- Enable maintenance mode if needed
- Stop accepting new requests

### 3. Execute Rollback
```bash
# Commands will vary by deployment system
kubectl rollout undo deployment/{{serviceName}}
# OR
flyctl releases rollback {{targetVersion}}
# OR  
vercel rollback
```

### 4. Verify
- Health checks passing
- Error rates returning to normal
- Key flows working

### 5. Communicate
- Update status page
- Team notification
- Schedule post-mortem

## Post-Rollback
- [ ] Document timeline
- [ ] Preserve logs for analysis
- [ ] Create incident ticket
- [ ] Schedule retrospective
```

##### 14.13 Release Notes Generator

**Prompt (.github/prompts/generate-release-notes.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate release notes from merged PRs'
model: 'Claude Sonnet 4'
---

Generate release notes for version {{version}}:

**Date Range:** {{startDate}} to {{endDate}}

## Analysis Steps
1. List all merged PRs in range
2. Categorize by type (feature, fix, improvement)
3. Extract user-facing changes
4. Note breaking changes
5. Credit contributors

## Output Format

# Release {{version}}

📅 **Release Date:** [date]

## 🚀 New Features
- **Feature Name** - Brief description ([#PR](link))

## 🐛 Bug Fixes
- Fixed issue with X ([#PR](link))

## 💔 Breaking Changes
- `oldMethod()` renamed to `newMethod()`

## 🔧 Improvements
- Performance improvement in X
- Updated dependency Y to version Z

## 📚 Documentation
- Added guide for X

## 🙏 Contributors
Thanks to @contributor1, @contributor2

## Upgrade Notes
[Any special upgrade instructions]
```

##### 14.14 Changelog Generator

**Prompt (.github/prompts/update-changelog.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Update CHANGELOG.md following Keep a Changelog format'
model: 'Claude Sonnet 4'
---

Update CHANGELOG.md for version {{version}}:

## Format (Keep a Changelog)
```markdown
## [{{version}}] - {{date}}

### Added
- New features

### Changed  
- Changes to existing functionality

### Deprecated
- Features to be removed

### Removed
- Removed features

### Fixed
- Bug fixes

### Security
- Vulnerability fixes
```

## Instructions
1. Read current CHANGELOG.md
2. Analyze commits since last version
3. Categorize changes appropriately
4. Write user-friendly descriptions
5. Add links to PRs/issues
6. Update [Unreleased] section to new version
7. Add new [Unreleased] section at top
```

---

#### CI/CD Workflows

##### 14.15 GitHub Actions Workflow Generator

**Prompt (.github/prompts/create-gha-workflow.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate GitHub Actions workflow'
model: 'Claude Sonnet 4'
---

Create a GitHub Actions workflow for: {{workflowType}}

**Workflow Types:**
- `ci` - Continuous Integration (test, lint, build)
- `cd` - Continuous Deployment
- `release` - Release automation
- `scheduled` - Scheduled tasks

## CI Workflow Template
```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm run test
      - run: npm run build
```

## Requirements
1. Use latest action versions
2. Cache dependencies
3. Run in parallel where possible
4. Use matrix for multi-version testing
5. Include status checks
```

##### 14.16 Azure Pipelines Generator

**Prompt (.github/prompts/create-azure-pipeline.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate Azure Pipelines YAML'
model: 'Claude Sonnet 4'
---

Create an Azure Pipeline for: {{pipelineType}}

## Pipeline Template
```yaml
trigger:
  branches:
    include:
      - main
  paths:
    exclude:
      - 'docs/**'
      - '*.md'

pool:
  vmImage: 'ubuntu-latest'

variables:
  - group: 'common-variables'

stages:
  - stage: Build
    jobs:
      - job: BuildJob
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '20.x'
          - script: npm ci
          - script: npm run build

  - stage: Test
    dependsOn: Build
    jobs:
      - job: TestJob
        steps:
          - script: npm test

  - stage: Deploy
    dependsOn: Test
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
    jobs:
      - deployment: DeployJob
        environment: '{{environment}}'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying..."
```

## Include
1. Build caching
2. Artifact publishing
3. Environment approvals
4. Variable groups
```

##### 14.17 Build Failure Analyzer

**Prompt (.github/prompts/analyze-build-failure.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Analyze and fix CI build failures'
model: 'Claude Sonnet 4'
---

Analyze the build failure:

**Build URL:** {{buildUrl}}
**Error Log:**
```
{{errorLog}}
```

## Analysis Steps
1. Parse the error message
2. Identify the failing step
3. Check for common causes:
   - Dependency issues
   - Test failures
   - Linting errors
   - Build configuration
   - Environment differences

## Output Format
### Root Cause
[What caused the failure]

### Affected Files
- [file1.ts]
- [file2.ts]

### Fix
[Specific changes needed]

### Prevention
[How to prevent this in future]
```

##### 14.18 Test Failure Triage

**Prompt (.github/prompts/triage-test-failures.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Triage and categorize test failures'
model: 'Claude Sonnet 4'
---

Triage test failures from CI run:

## Failure Categories
- **Flaky:** Intermittent, passes on retry
- **Environment:** CI-specific issue
- **Regression:** Code change broke test
- **Test Bug:** Test itself is wrong
- **Dependency:** External service issue

## For Each Failure
1. Read the test file
2. Read the code being tested
3. Analyze the error message
4. Check recent changes to related files
5. Categorize the failure
6. Recommend action

## Output Format
| Test | Category | Cause | Action |
|------|----------|-------|--------|
| test_name | Regression | PR #123 changed X | Fix in code |
```

---

#### Documentation Workflows

##### 14.19 API Documentation Generator

**Prompt (.github/prompts/generate-api-docs.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate OpenAPI/Swagger documentation'
model: 'Claude Sonnet 4'
---

Generate API documentation for: {{apiPath}}

## Output Format (OpenAPI 3.0)
```yaml
openapi: 3.0.0
info:
  title: {{apiTitle}}
  version: 1.0.0

paths:
  /endpoint:
    get:
      summary: Brief description
      description: Detailed description
      parameters:
        - name: param
          in: query
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Response'
```

## Include
1. All endpoints in the path
2. Request/response schemas
3. Authentication requirements
4. Error responses
5. Example values
6. Rate limiting info
```

##### 14.20 README Generator

**Prompt (.github/prompts/generate-readme.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate or update project README'
model: 'Claude Sonnet 4'
---

Generate a README.md for this project:

## Required Sections
1. **Title & Badges** - Project name, CI status, version
2. **Description** - What it does, why it exists
3. **Quick Start** - Minimal steps to get running
4. **Installation** - Detailed setup
5. **Usage** - Common use cases with examples
6. **API Reference** - If applicable
7. **Configuration** - Environment variables, options
8. **Contributing** - How to contribute
9. **License** - License information

## Analysis Steps
1. Read package.json/requirements.txt
2. Look at existing documentation
3. Find example usage in tests
4. Check for existing README sections to preserve
```

##### 14.21 ADR (Architecture Decision Record) Creator

**Prompt (.github/prompts/create-adr.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create an Architecture Decision Record'
model: 'Claude Sonnet 4'
---

Create ADR for: {{decisionTitle}}

## ADR Format
```markdown
# ADR-{{number}}: {{title}}

## Status
Proposed | Accepted | Deprecated | Superseded by ADR-X

## Context
[What is the issue? What forces are at play?]

## Decision
[What is the change we're making?]

## Consequences
### Positive
- [Benefits]

### Negative
- [Drawbacks]

### Risks
- [What could go wrong]

## Alternatives Considered
### Option A: [Name]
- Pros: 
- Cons: 

### Option B: [Name]
- Pros:
- Cons:

## References
- [Links to relevant docs, discussions]
```

## Save to: `docs/adr/{{number}}-{{slug}}.md`
```

##### 14.22 Runbook Generator

**Prompt (.github/prompts/create-runbook.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create operations runbook for a service'
model: 'Claude Sonnet 4'
---

Create runbook for: {{serviceName}}

## Runbook Template

# {{serviceName}} Operations Runbook

## Service Overview
- **Purpose:** What it does
- **Dependencies:** What it needs
- **Criticality:** How important

## Health Checks
- **Endpoint:** `/health`
- **Expected:** 200 OK
- **Frequency:** Every 30s

## Common Issues

### Issue: High Latency
**Symptoms:** P95 > 500ms
**Diagnosis:**
1. Check database connections
2. Check downstream services
3. Check resource usage

**Resolution:**
1. Scale horizontally
2. Clear cache
3. Restart pods

### Issue: Error Rate Spike
**Symptoms:** 5xx errors > 1%
**Diagnosis:**
1. Check logs
2. Check recent deployments
3. Check dependencies

**Resolution:**
1. Rollback if recent deploy
2. Check dependency status
3. Enable circuit breaker

## Alerting
| Alert | Threshold | Action |
|-------|-----------|--------|
| High Error Rate | >1% 5xx | Page on-call |
| High Latency | P95 >1s | Investigate |

## Contacts
- **On-call:** #oncall-{{team}}
- **Escalation:** @{{manager}}
```

---

#### Testing Workflows

##### 14.23 Unit Test Generator

**Prompt (.github/prompts/generate-unit-tests.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate unit tests for selected code'
model: 'Claude Sonnet 4'
---

Generate unit tests for the selected code.

## Test Requirements
1. Test all public functions/methods
2. Test happy path
3. Test edge cases:
   - Empty inputs
   - Null/undefined
   - Boundary values
   - Invalid types
4. Test error conditions
5. Mock external dependencies

## Test Structure
```typescript
describe('FunctionName', () => {
  describe('happy path', () => {
    it('should [expected behavior] when [condition]', () => {
      // Arrange
      // Act
      // Assert
    });
  });

  describe('edge cases', () => {
    it('should handle empty input', () => {});
    it('should handle null', () => {});
  });

  describe('error cases', () => {
    it('should throw when [condition]', () => {});
  });
});
```

## Coverage Goals
- Statements: 80%+
- Branches: 75%+
- Functions: 90%+
```

##### 14.24 Integration Test Scaffolder

**Prompt (.github/prompts/scaffold-integration-tests.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Scaffold integration tests for API endpoints'
model: 'Claude Sonnet 4'
---

Create integration tests for: {{apiEndpoint}}

## Test Scenarios
1. **Success cases** - Valid requests return expected data
2. **Auth cases** - Unauthorized returns 401, forbidden returns 403
3. **Validation cases** - Invalid input returns 400 with errors
4. **Not found cases** - Missing resources return 404
5. **Conflict cases** - Duplicate creation returns 409

## Test Structure
```typescript
describe('{{method}} {{endpoint}}', () => {
  describe('authenticated requests', () => {
    it('returns data for valid request', async () => {
      const response = await request(app)
        .{{method}}('{{endpoint}}')
        .set('Authorization', `Bearer ${validToken}`)
        .send(validPayload);
      
      expect(response.status).toBe(200);
      expect(response.body).toMatchObject(expectedShape);
    });
  });

  describe('authentication', () => {
    it('returns 401 without token', async () => {});
    it('returns 401 with invalid token', async () => {});
    it('returns 403 without permission', async () => {});
  });

  describe('validation', () => {
    it('returns 400 for missing required fields', async () => {});
    it('returns 400 for invalid field types', async () => {});
  });
});
```
```

##### 14.25 E2E Test Creator (Playwright/Cypress)

**Prompt (.github/prompts/create-e2e-test.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Create E2E test for user flow'
model: 'Claude Sonnet 4'
---

Create E2E test for: {{userFlow}}

## Playwright Example
```typescript
import { test, expect } from '@playwright/test';

test.describe('{{userFlow}}', () => {
  test.beforeEach(async ({ page }) => {
    // Setup: login, navigate, etc.
  });

  test('completes successfully', async ({ page }) => {
    // Step 1: Action
    await page.click('[data-testid="button"]');
    
    // Step 2: Verification
    await expect(page.locator('.success')).toBeVisible();
    
    // Step 3: Assert final state
    await expect(page).toHaveURL(/success/);
  });

  test('handles errors gracefully', async ({ page }) => {
    // Test error scenarios
  });
});
```

## Include
1. Page Object Model if complex
2. Test data factories
3. Cleanup in afterEach
4. Screenshots on failure
5. Network request interception if needed
```

##### 14.26 Test Coverage Gap Analyzer

**Prompt (.github/prompts/analyze-coverage.prompt.md):**
```markdown
---
agent: 'ask'
description: 'Analyze test coverage and identify gaps'
model: 'Claude Sonnet 4'
---

Analyze test coverage for: {{targetPath}}

## Analysis Steps
1. List all public functions/exports
2. Find corresponding test files
3. Check which functions are tested
4. Identify untested code paths
5. Prioritize gaps by risk

## Output Format
### Coverage Summary
- Files analyzed: X
- Functions found: Y
- Functions tested: Z
- Coverage: Z/Y%

### Critical Gaps (High Risk)
| File | Function | Risk | Reason |
|------|----------|------|--------|
| user.ts | validateEmail | High | Input validation |

### Medium Gaps
[Functions with moderate complexity]

### Low Priority Gaps
[Simple getters, utilities]

### Recommended Next Tests
1. [Most impactful test to add]
2. [Second priority]
3. [Third priority]
```

---

#### Database Workflows

##### 14.27 Migration Generator

**Prompt (.github/prompts/create-migration.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate database migration'
model: 'Claude Sonnet 4'
---

Create migration for: {{migrationDescription}}

## Migration Requirements
1. Include both up and down
2. Use transactions
3. Consider data migration if schema change
4. Add indexes for new columns used in WHERE/JOIN
5. Set appropriate defaults

## Prisma Example
```typescript
-- CreateTable
CREATE TABLE "{{tableName}}" (
    "id" TEXT NOT NULL PRIMARY KEY,
    "createdAt" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updatedAt" TIMESTAMP(3) NOT NULL,
    -- columns
);

-- CreateIndex
CREATE INDEX "{{tableName}}_{{column}}_idx" ON "{{tableName}}"("{{column}}");
```

## Safety Checks
- [ ] Migration is reversible
- [ ] No data loss on down migration
- [ ] Indexes won't lock table too long
- [ ] Default values set for NOT NULL columns
```

##### 14.28 Seed Data Creator

**Prompt (.github/prompts/create-seed-data.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate database seed data'
model: 'Claude Sonnet 4'
---

Create seed data for: {{tableName}}

## Requirements
1. Realistic data (use faker/casual)
2. Relationships maintained
3. Edge cases included
4. Performance data (optional)

## Seed Structure
```typescript
import { PrismaClient } from '@prisma/client';
import { faker } from '@faker-js/faker';

const prisma = new PrismaClient();

async function main() {
  // Clear existing data
  await prisma.{{tableName}}.deleteMany();

  // Create seed data
  const items = Array.from({ length: 50 }, () => ({
    id: faker.string.uuid(),
    name: faker.person.fullName(),
    email: faker.internet.email(),
    createdAt: faker.date.past(),
  }));

  await prisma.{{tableName}}.createMany({ data: items });
}

main()
  .catch(console.error)
  .finally(() => prisma.$disconnect());
```
```

##### 14.29 Query Optimizer

**Prompt (.github/prompts/optimize-query.prompt.md):**
```markdown
---
agent: 'ask'
description: 'Analyze and optimize database query'
model: 'Claude Sonnet 4'
---

Optimize this query:
```sql
{{query}}
```

## Analysis
1. Check for missing indexes
2. Look for N+1 patterns
3. Check join efficiency
4. Analyze WHERE clause selectivity
5. Consider query plan

## Output
### Current Issues
- [Issue 1]
- [Issue 2]

### Recommended Indexes
```sql
CREATE INDEX idx_name ON table(column);
```

### Optimized Query
```sql
[Optimized version]
```

### Expected Improvement
- Before: ~Xms
- After: ~Yms
```

##### 14.30 Schema Documentation

**Prompt (.github/prompts/document-schema.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate database schema documentation'
model: 'Claude Sonnet 4'
---

Document the database schema.

## Output Format
# Database Schema

## Tables

### {{tableName}}
**Description:** What this table stores

| Column | Type | Nullable | Default | Description |
|--------|------|----------|---------|-------------|
| id | UUID | No | uuid_generate_v4() | Primary key |

**Indexes:**
- `idx_name` on (column1, column2)

**Foreign Keys:**
- `column` → `other_table.id`

## Relationships
[Mermaid ER diagram]

## Common Queries
[Example queries with explanations]
```

---

#### Communication & Notifications

##### 14.31 Slack Notification Composer

**Prompt (.github/prompts/compose-slack-notification.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Compose Slack notification for team updates'
model: 'GPT-4o'
---

Compose Slack message for: {{notificationType}}

## Notification Types

### Deployment
```json
{
  "blocks": [
    {
      "type": "header",
      "text": { "type": "plain_text", "text": "🚀 Deployment Notification" }
    },
    {
      "type": "section",
      "fields": [
        { "type": "mrkdwn", "text": "*Environment:*\n{{env}}" },
        { "type": "mrkdwn", "text": "*Version:*\n{{version}}" }
      ]
    },
    {
      "type": "section",
      "text": { "type": "mrkdwn", "text": "*Changes:*\n{{changes}}" }
    }
  ]
}
```

### Incident
```json
{
  "blocks": [
    {
      "type": "header",
      "text": { "type": "plain_text", "text": "🔥 Incident Alert" }
    },
    {
      "type": "section",
      "fields": [
        { "type": "mrkdwn", "text": "*Severity:*\n{{severity}}" },
        { "type": "mrkdwn", "text": "*Status:*\n{{status}}" }
      ]
    }
  ]
}
```
```

##### 14.32 Teams Message Formatter

**Prompt (.github/prompts/format-teams-message.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Format message for Microsoft Teams'
model: 'GPT-4o'
---

Format Teams message for: {{messageType}}

## Adaptive Card Template
```json
{
  "type": "AdaptiveCard",
  "version": "1.4",
  "body": [
    {
      "type": "TextBlock",
      "size": "Large",
      "weight": "Bolder",
      "text": "{{title}}"
    },
    {
      "type": "FactSet",
      "facts": [
        { "title": "Status:", "value": "{{status}}" },
        { "title": "Updated:", "value": "{{timestamp}}" }
      ]
    }
  ],
  "actions": [
    {
      "type": "Action.OpenUrl",
      "title": "View Details",
      "url": "{{detailsUrl}}"
    }
  ]
}
```
```

##### 14.33 Incident Report Generator

**Prompt (.github/prompts/generate-incident-report.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Generate post-incident report'
model: 'Claude Sonnet 4'
---

Generate incident report:

**Incident:** {{incidentSummary}}
**Duration:** {{startTime}} to {{endTime}}

## Report Template

# Incident Report: {{incidentTitle}}

## Summary
- **Severity:** P1/P2/P3/P4
- **Duration:** X hours Y minutes
- **Impact:** [User-facing impact]
- **Root Cause:** [One sentence]

## Timeline
| Time | Event |
|------|-------|
| HH:MM | Issue first detected |
| HH:MM | Alert triggered |
| HH:MM | Investigation started |
| HH:MM | Root cause identified |
| HH:MM | Fix deployed |
| HH:MM | Monitoring confirmed resolution |

## Root Cause Analysis
[Detailed explanation]

## Impact
- X users affected
- Y requests failed
- Z revenue impact

## Resolution
[What fixed it]

## Lessons Learned
### What went well
- [Positive]

### What went poorly
- [Negative]

## Action Items
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [Action] | @name | Date | Open |

## Prevention
[How to prevent recurrence]
```

##### 14.34 Status Update Composer

**Prompt (.github/prompts/compose-status-update.prompt.md):**
```markdown
---
agent: 'ask'
description: 'Compose status update for stakeholders'
model: 'GPT-4o'
---

Compose status update:

**Audience:** {{audience}} (executives/team/stakeholders)
**Topic:** {{topic}}

## Format by Audience

### Executive Summary
- 3 bullet points max
- Business impact focus
- Clear ask/decision needed

### Team Update
- Technical details ok
- Blockers highlighted
- Next steps clear

### Stakeholder Update
- Progress vs. plan
- Risks and mitigations
- Timeline updates

## Template
**TL;DR:** [One sentence summary]

**Progress:**
- ✅ Completed: [items]
- 🔄 In Progress: [items]
- ⏳ Upcoming: [items]

**Blockers:**
- [Any blockers and mitigation]

**Timeline:**
- On track / At risk / Delayed

**Needs:**
- [Any asks from the audience]
```

---

#### Sprint & Project Management

##### 14.35 Sprint Planning Assistant

**Agent (.github/agents/scrum-master.agent.md):**
```markdown
---
name: 'Scrum Master'
description: 'Assists with sprint planning and ceremonies'
tools: ['search', 'readFile', 'fetch']
model: 'Claude Sonnet 4'
---

You are an experienced Scrum Master facilitating sprint planning.

## Sprint Planning Process
1. Review velocity (last 3 sprints average)
2. Assess team capacity (PTO, meetings)
3. Pull from prioritized backlog
4. Break down large items
5. Assign story points
6. Commit to sprint goal

## Questions to Ask
- What's our velocity trend?
- Any PTO this sprint?
- What's the sprint goal?
- Any dependencies on other teams?
- Technical debt to address?

## Capacity Calculation
```
Available points = (team size × days × focus factor) - (meetings + PTO)
Typical: 6-8 points per dev per sprint
```

## Output Format
### Sprint {{number}} Plan

**Goal:** [Sprint goal]
**Capacity:** X points
**Committed:** Y points

| Item | Points | Owner | Dependencies |
|------|--------|-------|--------------|
```

##### 14.36 Estimation Helper

**Prompt (.github/prompts/estimate-work.prompt.md):**
```markdown
---
agent: 'ask'
description: 'Help estimate work items'
model: 'Claude Sonnet 4'
---

Estimate: {{workDescription}}

## Estimation Approach
1. Break into subtasks
2. Identify complexity factors
3. Consider unknowns
4. Compare to past work
5. Apply contingency

## Complexity Factors
- [ ] New technology
- [ ] External dependencies
- [ ] Data migration
- [ ] Security requirements
- [ ] Performance requirements
- [ ] Multiple environments

## Story Points (Fibonacci)
| Points | Complexity | Time Equivalent |
|--------|------------|-----------------|
| 1 | Trivial | Hours |
| 2 | Simple | 1 day |
| 3 | Standard | 2-3 days |
| 5 | Complex | 1 week |
| 8 | Very Complex | 1-2 weeks |
| 13 | Epic-sized | Multiple sprints |

## Output
### Estimate for: {{title}}

**Recommended:** X points

**Breakdown:**
- Subtask 1: Y points
- Subtask 2: Z points

**Risks:**
- [Unknowns that could affect estimate]

**Comparable Work:**
- [Similar past items for reference]
```

##### 14.37 Dependency Mapper

**Prompt (.github/prompts/map-dependencies.prompt.md):**
```markdown
---
agent: 'ask'
description: 'Map dependencies for a feature or project'
model: 'Claude Sonnet 4'
---

Map dependencies for: {{featureName}}

## Dependency Types
1. **Technical** - Code/system dependencies
2. **Team** - Work from other teams
3. **External** - Third-party dependencies
4. **Data** - Data availability
5. **Infrastructure** - Environment needs

## Output Format
### Dependency Map: {{featureName}}

```mermaid
graph TD
    A[{{featureName}}] --> B[Dependency 1]
    A --> C[Dependency 2]
    B --> D[Sub-dependency]
```

### Dependency Details
| Dependency | Type | Owner | Status | Risk |
|------------|------|-------|--------|------|
| [Name] | Technical | @team | Ready | Low |

### Critical Path
[Sequence of dependencies that determines timeline]

### Risks
| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
```

##### 14.38 Technical Debt Tracker

**Prompt (.github/prompts/track-tech-debt.prompt.md):**
```markdown
---
agent: 'agent'
description: 'Document and prioritize technical debt'
model: 'Claude Sonnet 4'
---

Document technical debt in: {{codeArea}}

## Debt Categories
- **Architecture** - Design issues
- **Code Quality** - Messy/duplicated code
- **Testing** - Missing coverage
- **Documentation** - Outdated docs
- **Dependencies** - Outdated libraries
- **Performance** - Known slow spots
- **Security** - Known vulnerabilities

## Prioritization Matrix
| Impact | Effort: Low | Effort: Medium | Effort: High |
|--------|-------------|----------------|--------------|
| High   | Do First    | Schedule Soon  | Plan Carefully |
| Medium | Quick Win   | Backlog        | Maybe Later |
| Low    | If Time     | Probably Not   | Don't Do |

## Output Format
### Tech Debt: {{area}}

| Item | Category | Impact | Effort | Priority |
|------|----------|--------|--------|----------|
| [Description] | Code Quality | High | Low | Do First |

### Recommended Actions
1. [Highest priority item with approach]
2. [Second priority]

### Debt Trends
- Increasing/Stable/Decreasing
- [Analysis of why]
```

---

### Part V: Implementation Guide

#### 15. Quick Start: Org-Wide Rollout

**Day 1:** Create `.github/copilot-instructions.md`
- Tech stack declaration
- 5 most important coding conventions
- Top 3 anti-patterns to avoid

**Week 1:** Add essential prompt files
- Component generator
- Bug fix assistant
- Documentation generator

**Month 1:** Implement custom agents
- Code reviewer
- Security reviewer
- Architect for design discussions

**Quarter 1:** Evaluate advanced features
- Skills for specialized capabilities
- MCP servers for external integrations
- Sub-agent patterns for complex workflows

---

### Part VI: Reference

#### 19. Quick Reference Tables

**The Six Primitives At a Glance:**

| What You Want | Use This | Location |
|---------------|----------|----------|
| Global coding rules | Always-On Instructions | `.github/copilot-instructions.md` |
| Rules for specific files | File-Based Instructions | `.github/instructions/*.instructions.md` |
| Reusable task template | Prompt File | `.github/prompts/*.prompt.md` |
| Specialized capability | Skill | `.github/skills/*/SKILL.md` |
| AI persona/behavior | Custom Agent | `.github/agents/*.agent.md` |
| External tool access | MCP Server | `.vscode/mcp.json` |

**Frontmatter Quick Reference:**

| Primitive | Required Fields | Optional Fields |
|-----------|-----------------|-----------------|
| File-Based Instructions | `applyTo` | `name`, `description` |
| Prompt Files | (none) | `agent`, `description`, `model`, `tools` |
| Skills | `name`, `description` | `metadata`, `license`, `compatibility` |
| Custom Agents | (none) | `name`, `description`, `tools`, `model`, `handoffs` |

---

## Summary

| Objective | Solution |
|-----------|----------|
| Set permanent coding rules | Always-On Instructions |
| Rules for specific file types | File-Based Instructions |
| Create a reusable task | Prompt File |
| Add specialized capabilities | Skill |
| Define AI persona/behavior | Custom Agent |
| Connect to external data/tools | MCP Server |

---

## File Structure Reference

```
your-repo/
├── .github/
│   ├── copilot-instructions.md          # Always-on instructions
│   ├── instructions/
│   │   ├── api-routes.instructions.md   # File-based instructions
│   │   ├── react-components.instructions.md
│   │   └── tests.instructions.md
│   ├── prompts/
│   │   ├── new-component.prompt.md      # Prompt files
│   │   ├── fix-bug.prompt.md
│   │   └── generate-docs.prompt.md
│   ├── skills/
│   │   ├── create-agent-skill/          # Skills
│   │   │   └── SKILL.md
│   │   ├── github-issues/
│   │   │   ├── SKILL.md
│   │   │   └── templates/
│   │   └── image-manipulation/
│   │       └── SKILL.md
│   └── agents/
│       ├── architect.agent.md           # Custom agents
│       ├── reviewer.agent.md
│       └── security.agent.md
├── .vscode/
│   ├── mcp.json                         # MCP configuration
│   └── settings.json
└── [your code...]
```

---

*This outline represents the complete structure for the definitive guide on customizing GitHub Copilot for your repository.*
