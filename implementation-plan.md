# Implementation Plan: First Draft

*Writing "The Definitive Guide to Customizing Your Repo for GitHub Copilot"*

---

## Overview

This plan breaks down the work to transform [OUTLINE.md](OUTLINE.md) into a complete first draft in [ReadMe.md](ReadMe.md). The guide covers GitHub Copilot's six customization primitives across six parts.

**Target:** ~3,500-4,500 lines of comprehensive documentation  
**Actual:** 5,416 lines (22,244 words) — First draft complete ✅  
**Source:** OUTLINE.md (2,500+ lines of structured outline)

---

## Phase 1: Foundation & Structure

### 1.1 Setup Document Skeleton

**Status:** ✅ Complete  
**Estimated Effort:** 30 minutes  
**Dependencies:** None

**Tasks:**
- [x] Create/update ReadMe.md with title and introduction
- [x] Build complete Table of Contents with anchor links
- [x] Add all Part headers (I-VI)
- [x] Add all section headers (##) matching outline
- [x] Verify all internal links resolve correctly

**Acceptance Criteria:**
- Document has proper markdown structure
- All 23+ sections have headers
- ToC links work when clicked

---

### 1.2 Write Introduction

**Status:** ✅ Complete  
**Estimated Effort:** 45 minutes  
**Dependencies:** 1.1

**Tasks:**
- [x] Write the "new developer mental model" opening
- [x] Create the Three Pillars table
- [x] Explain Model Selection, Codebase Quality, Repository Configuration
- [x] Add the "two parts" structure note
- [x] List the "when properly configured" benefits

**Content Sources:**
- OUTLINE.md lines 7-65
- Official docs for accuracy verification

---

## Phase 2: Part I - Foundations

### 2.1 Why Customize?

**Status:** ✅ Complete  
**Estimated Effort:** 1 hour  
**Dependencies:** 1.2

**Sections to Write:**
- [x] 1.1 The Cost of Not Customizing (concrete examples)
- [x] 1.2 The "New Developer" Mental Model (expanded)
- [x] 1.3 ROI of Customization (metrics, examples)
- [x] 1.4 The 80/20 Rule (what matters most)

**Research Required:**
- Fetch VS Code Copilot docs for accurate feature claims
- Verify configuration options are current

---

### 2.2 The Customization Primitives Overview

**Status:** ☐ Complete   
**Estimated Effort:** 45 minutes  
**Dependencies:** 2.1

**Sections to Write:**
- [x] 2.1 Six Primitives At a Glance (comparison table)
- [x] 2.2 How They Work Together (diagram)
- [x] 2.3 What Each Primitive Does Best

**Key Table:** Location | Loading | Purpose | Best For

---

### 2.3 Understanding Context and Loading

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** 2.2

**Sections to Write:**
- [x] 3.1 When Each Primitive Loads
- [x] 3.2 Context Window Considerations
- [x] 3.3 The Loading Lifecycle
- [x] 3.4 Debugging What's Loaded

**Key Diagrams:**
- Loading lifecycle flowchart
- Context window budget visualization

---

### 2.4 Complete Setup Checklist

**Status:** ☐ Complete   
**Estimated Effort:** 45 minutes  
**Dependencies:** 2.3

**Sections to Write:**
- [x] 4.1 Prerequisites (VS Code version, extension, etc.)
- [x] 4.2 Repository Setup (step-by-step with verification)
- [x] 4.3 VS Code Configuration (settings.json)
- [x] 4.4 Team Onboarding (sharing instructions)
- [x] 4.5 Verification Steps (confirm it works)

---

## Phase 3: Part II - The Six Primitives

### 3.1 Always-On Instructions

**Status:** ☐ Complete   
**Estimated Effort:** 2 hours  
**Dependencies:** Phase 2

**Sections to Write:**
- [x] 5.1 Overview & Location
- [x] 5.2 When to Use
- [x] 5.3 Anatomy of Effective Instructions
- [x] 5.4 Including Rationale (Appraisals)
- [x] 5.5 Complete Production Example
- [x] 5.6 Creating via VS Code Configure Menu
- [x] 5.7 Agent-Driven Generation
- [x] 5.8-5.14 Creation workflows, patterns, anti-patterns

**Key Examples:**
- Minimal starter template
- Complete production example
- Good vs Bad patterns

**Research Required:**
- Verify VS Code menu paths are current
- Check frontmatter fields (if any)

---

### 3.2 File-Based Instructions

**Status:** ☐ Complete   
**Estimated Effort:** 1.5 hours  
**Dependencies:** 3.1

**Sections to Write:**
- [x] 6.1 Overview & Location
- [x] 6.2 File Format & Frontmatter Fields
- [x] 6.3 Pattern Matching with `applyTo`
- [x] 6.4 Use Cases (Frontend, Backend, Tests, Monorepos)
- [x] 6.5-6.8 Examples, creation, anti-patterns

**Key Examples:**
- React component instructions
- API endpoint instructions
- Test file instructions
- Monorepo package instructions

**Research Required:**
- Verify all frontmatter fields: `description`, `applyTo`, `glob`
- Test glob pattern edge cases

---

### 3.3 Prompt Files

**Status:** ☐ Complete   
**Estimated Effort:** 2.5 hours  
**Dependencies:** 3.2

**Sections to Write:**
- [x] 7.1 Overview & Location
- [x] 7.2 File Format & Frontmatter Fields
- [x] 7.3 Execution Modes (`ask` vs `agent`)
- [x] 7.4 Essential Prompts Every Repo Needs
- [x] 7.5-7.12 Usage, variables, references, anti-patterns
- [x] 7.13 Complete Prompt Library (15+ examples)

**Key Examples:**
- Code review prompt
- Test generation prompt
- Documentation prompt
- Refactoring prompt
- Bug fix prompt

**Research Required:**
- Verify frontmatter: `description`, `mode`, `tools`
- Check variable syntax `{{variableName}}`
- Verify `#file:` and `#sym:` reference syntax

---

### 3.4 Skills

**Status:** ☐ Complete   
**Estimated Effort:** 2 hours  
**Dependencies:** 3.3

**Sections to Write:**
- [x] 8.1-8.3 Overview, definition, agentskills.io spec
- [x] 8.4-8.6 Directory structure, SKILL.md format, naming rules
- [x] 8.7 Complete Example: GitHub Issues Skill
- [x] 8.8 Bootstrap with skill-creator skill
- [x] 8.9-8.13 Why skills, loading, debugging, vs MCP
- [x] 8.14-8.15 Complete examples (5+), sample prompts

**Key Examples:**
- GitHub Issues skill
- Deployment skill
- Database migration skill
- Testing skill
- Documentation skill

**Research Required:**
- Review agentskills.io specification
- Verify SKILL.md frontmatter fields
- Test description-based loading behavior

---

### 3.5 Custom Agents

**Status:** ☐ Complete   
**Estimated Effort:** 2 hours  
**Dependencies:** 3.4

**Sections to Write:**
- [x] 9.1-9.3 Overview, when to use, file format
- [x] 9.4 Recommended Agents (8+ examples)
- [x] 9.5-9.6 Sub-agents, handoffs
- [x] 9.7 Prompt vs Agent comparison
- [x] 9.8-9.16 Creation, personas, patterns, guardrails

**Key Examples:**
- Code Reviewer agent
- Security Auditor agent
- Documentation Writer agent
- Refactoring Expert agent
- Onboarding Guide agent
- Tech Lead agent
- Performance Analyst agent
- Accessibility Expert agent

**Research Required:**
- Verify frontmatter: `description`, `tools`
- Check sub-agent configuration syntax
- Verify tool access options

---

### 3.6 MCP (Model Context Protocol)

**Status:** ☐ Complete   
**Estimated Effort:** 1.5 hours  
**Dependencies:** 3.5

**Sections to Write:**
- [x] 10.1 Overview
- [x] 10.2 Instructions vs. MCP Capabilities
- [x] 10.3 Configuring MCP Servers
- [x] 10.4 Available MCP Servers
- [x] 10.5-10.8 Use cases, decision guide, examples, troubleshooting

**Key Examples:**
- GitHub MCP server config
- Database MCP server config
- Custom API MCP server config

**Research Required:**
- Fetch latest MCP documentation
- Verify mcp.json location and format
- List currently available MCP servers

---

## Phase 4: Part III - Putting It All Together

### 4.1 The Decision Matrix

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** Phase 3

**Sections to Write:**
- [x] 11.1-11.6 "Use X When..." for each primitive
- [x] 11.7 Decision Flow Chart (ASCII/Mermaid)
- [x] 11.8 Combining Primitives
- [x] 11.9 Common Scenarios & Solutions

**Key Deliverables:**
- Decision flowchart diagram
- Scenario-to-primitive mapping table

---

### 4.2 Interaction and Layering

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** 4.1

**Sections to Write:**
- [x] 12.1 How Primitives Stack
- [x] 12.2 Context Window Considerations
- [x] 12.3 Priority and Conflict Resolution
- [x] 12.4 From Global to Specific
- [x] 12.5 Visual Diagrams

**Key Diagrams:**
- Stacking order visualization
- Context budget breakdown
- Conflict resolution flowchart

---

### 4.3 Real-World Examples

**Status:** ☐ Complete   
**Estimated Effort:** 2 hours  
**Dependencies:** 4.2

**Sections to Write:**
- [x] 13.1 React Component Library
- [x] 13.2 Full-Stack SaaS Application
- [x] 13.3 Python Data Pipeline
- [x] 13.4 Monorepo with Multiple Languages
- [x] 13.5 Enterprise Security-Focused Setup
- [x] 13.6 Open Source Project
- [x] 13.7 Mobile App (React Native)
- [x] 13.8 CLI Tool

**Per Example Include:**
- Directory structure
- Key instructions file
- 2-3 prompts
- Custom agent (if applicable)
- MCP configuration (if applicable)

---

## Phase 5: Part IV - Workflow Recipes

### 5.1 Issue & PR Workflows

**Status:** ☐ Complete   
**Estimated Effort:** 2 hours  
**Dependencies:** Phase 4

**Recipes to Write:**
- [x] 14.2-14.5 Issue tracking (GitHub, Jira, Azure DevOps, Linear)
- [x] 14.6-14.9 PR workflows (creation, review, security)

**Per Recipe Include:**
- Primitives used
- Complete configuration
- Example invocation
- Expected output

---

### 5.2 Deployment & Release Workflows

**Status:** ☐ Complete   
**Estimated Effort:** 1.5 hours  
**Dependencies:** 5.1

**Recipes to Write:**
- [x] 14.10-14.14 Deployment, rollback, release notes, changelog

---

### 5.3 CI/CD & Testing Workflows

**Status:** ☐ Complete   
**Estimated Effort:** 2 hours  
**Dependencies:** 5.2

**Recipes to Write:**
- [x] 14.15-14.18 CI/CD generators, failure analysis
- [x] 14.23-14.26 Testing (unit, integration, E2E, coverage)

---

### 5.4 Documentation & Database Workflows

**Status:** ☐ Complete   
**Estimated Effort:** 1.5 hours  
**Dependencies:** 5.3

**Recipes to Write:**
- [x] 14.19-14.22 API docs, README, ADR, runbooks
- [x] 14.27-14.30 Database migrations, seeds, optimization

---

### 5.5 Communication & Sprint Workflows

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** 5.4

**Recipes to Write:**
- [x] 14.31-14.34 Slack, Teams, incidents, status updates
- [x] 14.35-14.38 Sprint planning, estimation, dependencies, tech debt

---

## Phase 6: Part V - Implementation Guide

### 6.1 Quick Start: Org-Wide Rollout

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** Phase 5

**Sections to Write:**
- [x] 15.1 Day 1: Create Instructions File
- [x] 15.2 Week 1: Add Essential Prompts
- [x] 15.3 Month 1: Implement Custom Agents
- [x] 15.4 Quarter 1: Evaluate MCP & Skills
- [x] 15.5 Ongoing: Review and Iterate
- [x] 15.6 Measuring Success

---

### 6.2 Best Practices

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** 6.1

**Sections to Write:**
- [x] 16.1-16.9 All best practices with examples

---

### 6.3 Debugging and Troubleshooting

**Status:** ☐ Complete   
**Estimated Effort:** 1.5 hours  
**Dependencies:** 6.2

**Sections to Write:**
- [x] 17.1-17.6 Debug view, verification, skills, MCP, common fixes

**Key Deliverables:**
- Problem-solution table
- Diagnostic prompts collection

---

### 6.4 FAQ

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** 6.3

**Questions to Answer:**
- [x] 18.1-18.12 All FAQ items from outline

---

## Phase 7: Part VI - Reference

### 7.1 Quick Reference Tables

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** Phase 6

**Tables to Create:**
- [x] 19.1 Six Primitives At a Glance
- [x] 19.2 Frontmatter Field Reference
- [x] 19.3 Execution Modes Reference
- [x] 19.4 Tool Access Reference
- [x] 19.5 File Location Reference
- [x] 19.6 Glob Pattern Reference

---

### 7.2 Templates and Starter Files

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** 7.1

**Templates to Create:**
- [x] 20.1 Starter copilot-instructions.md
- [x] 20.2 Starter File-Based Instruction
- [x] 20.3 Starter Prompt File
- [x] 20.4 Starter Skill
- [x] 20.5 Starter Custom Agent
- [x] 20.6 Starter MCP Configuration

---

### 7.3 Sample Prompt Library

**Status:** ☐ Complete   
**Estimated Effort:** 1.5 hours  
**Dependencies:** 7.2

**Prompts to Include:**
- [x] Code generation prompts (5+)
- [x] Review prompts (3+)
- [x] Documentation prompts (3+)
- [x] Testing prompts (3+)
- [x] Refactoring prompts (3+)

---

### 7.4 Resources & Summary

**Status:** ☐ Complete   
**Estimated Effort:** 30 minutes  
**Dependencies:** 7.3

**Sections to Write:**
- [x] Official documentation links
- [x] Community resources
- [x] Summary and next steps

---

## Phase 8: Review & Polish

### 8.1 Structural Review

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** Phase 7

**Tasks:**
- [x] Verify all ToC links work
- [x] Check all internal cross-references
- [x] Ensure consistent heading hierarchy
- [x] Verify code block language identifiers

---

### 8.2 Content Review

**Status:** ☐ Complete   
**Estimated Effort:** 2 hours  
**Dependencies:** 8.1

**Tasks:**
- [x] Verify technical accuracy against official docs
- [x] Check all file paths and locations
- [x] Validate frontmatter field names
- [x] Test example configurations
- [x] Remove any duplicated content

---

### 8.3 Final Polish

**Status:** ☐ Complete   
**Estimated Effort:** 1 hour  
**Dependencies:** 8.2

**Tasks:**
- [x] Consistent formatting throughout
- [x] Spell check
- [x] Remove TODO markers
- [x] Add any missing examples
- [x] Final read-through

---

## Summary

| Phase | Sections | Estimated Time |
|-------|----------|----------------|
| 1. Foundation & Structure | Introduction, ToC | 1.25 hours |
| 2. Part I: Foundations | Sections 1-4 | 3.5 hours |
| 3. Part II: Six Primitives | Sections 5-10 | 11.5 hours |
| 4. Part III: Putting It Together | Sections 11-13 | 4 hours |
| 5. Part IV: Workflow Recipes | Section 14 (all) | 8 hours |
| 6. Part V: Implementation Guide | Sections 15-18 | 4.5 hours |
| 7. Part VI: Reference | Sections 19-23 | 4 hours |
| 8. Review & Polish | Final pass | 4 hours |
| **Total** | | **~40 hours** |

---

## Execution Strategy

### Recommended Order

1. **Phase 1-2 first** — Establishes structure and context
2. **Phase 3 is critical** — Core content, most time investment
3. **Phase 4 builds on Phase 3** — References all primitives
4. **Phase 5 provides practical value** — Real-world applications
5. **Phase 6-7 support** — Reference material
6. **Phase 8 last** — Polish after content complete

### Parallelization Opportunities

These sections can be written independently once Phase 2 is complete:
- Section 5 (Always-On) and Section 10 (MCP)
- Section 8 (Skills) and Section 9 (Agents)
- All workflow recipes in Phase 5

### Research Gates

Do not proceed past these points without fetching current documentation:

| Gate | What to Verify | Sources |
|------|----------------|---------|
| Before Section 5 | Instructions file format | VS Code docs |
| Before Section 6 | Frontmatter fields | VS Code docs |
| Before Section 7 | Prompt syntax, modes | VS Code docs |
| Before Section 8 | agentskills.io spec | agentskills.io |
| Before Section 10 | MCP configuration | GitHub docs |

---

## Notes

- **Keep sections focused** — Each primitive section should be self-contained
- **Use consistent examples** — Reuse the same hypothetical project across examples where possible
- **Include rationale** — Explain "why" not just "how"
- **Add breadcrumbs** — Cross-reference related sections
- **Test everything** — Verify configurations actually work before documenting
