# Part V: Implementation Guide

[ Back to Guide](../ReadMe.md) | [ Part IV: Workflow Recipes](part-4-workflow-recipes.md)

---

## Quick Start: Org-Wide Rollout

### Day 1: Create Instructions File

Start with a minimal instructions file covering the essentials:

```markdown
# Copilot Instructions for [Project Name]

## Tech Stack
- [List primary technologies]

## Conventions
- [3-5 most important coding rules]

## Anti-Patterns
- [Top 3 things to avoid]
```

Commit to main branch. Verify it's loading by asking Copilot about coding standards.

### Week 1: Add Essential Prompts

Create 3-5 prompts for tasks the team does frequently:

1. **Component/module generator** — scaffold new code
2. **Test generator** — create tests for selected code
3. **Documentation generator** — add JSDoc/comments
4. **Bug fix assistant** — analyze and fix issues
5. **Code review prep** — self-review before PR

### Month 1: Implement Custom Agents

Add specialized agents for distinct workflows:

1. **Code Reviewer** — thorough review with conventions
2. **Security Reviewer** — vulnerability-focused review
3. **Architect** — high-level design discussions

### Quarter 1: Evaluate Advanced Features

Assess whether your workflows need:

- **Skills** — specialized capabilities (image processing, external APIs)
- **MCP servers** — live data from external systems
- **Sub-agents** — complex multi-step workflows

### Ongoing: Review and Iterate

Schedule quarterly reviews:

1. **Collect feedback** — what prompts are used most? What's missing?
2. **Update instructions** — remove deprecated rules, add new conventions
3. **Audit effectiveness** — is PR feedback decreasing?
4. **Share learnings** — document what works for the team

### Measuring Success

Track these metrics to evaluate customization ROI:

| Metric | How to Measure | Target |
|--------|---------------|--------|
| PR review cycles | Average comments per PR | Decrease by 30% |
| Time to first commit | Onboarding velocity | Decrease by 50% |
| Prompt usage | VS Code telemetry | >80% team adoption |
| Instruction file updates | Git commits | Monthly updates |

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

## Debugging and Troubleshooting

### Using Chat Debug View

The Chat Debug View is the most powerful tool for understanding what Copilot sees:

1. Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Run **"Developer: Show Chat Debug View"**
3. Dock the panel alongside your chat
4. Every request shows full context

**What You'll See:**
- Complete system prompt
- All loaded instructions
- Available skills and their descriptions
- MCP tools and their status
- Token usage per section

### Verifying Instructions Are Loaded

To confirm your instructions file is being read:

1. Open Copilot Chat
2. Ask: "What are the coding standards for this project?"
3. Check if response references your specific rules
4. Verify in Debug View that instructions appear

**If instructions aren't loading, check:**
- File is named exactly `copilot-instructions.md` (case-sensitive)
- File is in `.github/` directory at repo root
- VS Code workspace has the repository open
- Window was reloaded after creating file (`Developer: Reload Window`)

### Why Skills Don't Activate

Skills load based on description matching. If a skill isn't activating:

1. **Check the description** — Does it clearly describe WHEN to use the skill?
2. **Include trigger words** — Match language users would naturally use
3. **Verify in Debug View** — Skills appear as a list in every request's context
4. **Test explicitly** — Ask for something that clearly matches the description

**Effective description:**
```markdown
description: Resize, convert, compress, and batch-process images using ImageMagick. 
Use when the user mentions image optimization, format conversion, or bulk image operations.
```

**Ineffective description:**
```markdown
description: Image processing skill
```

### Troubleshooting MCP Connections

MCP server issues typically fall into these categories:

| Symptom | Likely Cause | Solution |
|---------|--------------|----------|
| Server doesn't appear | Config syntax error | Validate JSON in mcp.json |
| Connection refused | Server not running | Check `npx` command runs manually |
| Auth failures | Missing env vars | Verify tokens in environment |
| Timeout errors | Server too slow | Increase timeout or check server health |
| Tools missing | Wrong server version | Update to latest package |

**Debug MCP step-by-step:**
1. Run the server command manually in terminal
2. Check for error output
3. Verify environment variables are set
4. Test the server independently before using in Copilot

### Common Mistakes and Fixes

| Mistake | Why It Fails | Fix |
|---------|--------------|-----|
| Instructions file too long | Context overflow | Move specialized rules to file-based instructions |
| Conflicting rules | Unpredictable behavior | Review for contradictions, prioritize one approach |
| Vague prompt instructions | Inconsistent output | Add specific examples and constraints |
| Missing applyTo pattern | Instructions never load | Add glob pattern to frontmatter |
| Wrong file extension | VS Code doesn't recognize | Use `.instructions.md`, `.prompt.md`, `.agent.md` |
| Quotes in YAML | Parse error | Use proper YAML escaping or different quote style |

### Diagnostic Prompts

Use these prompts to debug your configuration:

> 💬 **Check instructions loading:**
> "What coding conventions should I follow in this project?"

> 💬 **Verify skill availability:**
> "What specialized capabilities do you have access to?"

> 💬 **Test MCP tools:**
> "What external tools can you access right now?"

> 💬 **Confirm agent understanding:**
> "Describe your current role and capabilities."

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

| Aspect | Prompt File | Custom Agent |
|--------|-------------|--------------|
| **Duration** | One-shot, specific task | Entire conversation |
| **Variables** | Yes, fill-in-the-blank | No, set at session start |
| **Purpose** | Reusable task templates | Persona/behavior settings |
| **State** | Stateless | Persists during conversation |
| **Example** | "Generate a test file" | "Act as a security reviewer" |

### Can multiple instructions files exist?

- **One always-on file:** `.github/copilot-instructions.md`
- **Multiple file-based:** `.github/instructions/*.instructions.md`
- **Organization-level:** Configured at org/enterprise level, merges with repo

File-based instructions supplement the always-on file for specific file patterns.

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

Specific enough to produce consistent results, flexible enough for reuse. Use variables (`{{variableName}}`) for dynamic elements.

### Can I use multiple agents together?

Sub-agents enable this pattern. One agent can hand off to another for specialized work:
- Main agent handles orchestration
- Sub-agents handle specific tasks in isolated context
- Results summarized back to main conversation

### What happens when primitives conflict?

Later-loaded context takes precedence:
1. Always-on instructions (base)
2. File-based instructions (override for patterns)
3. Custom agent (session override)
4. Prompt (request override)

When conflicts occur, be more specific in the higher-priority primitive.

### How do I migrate existing docs to instructions?

1. Start with existing README/CONTRIBUTING as source
2. Extract actionable rules (not background info)
3. Rewrite as direct instructions
4. Add examples showing good vs. bad
5. Remove prose — Copilot needs rules, not essays

---

[ Part IV: Workflow Recipes](part-4-workflow-recipes.md) | [Next: Part VI - Reference ](part-6-reference.md)
