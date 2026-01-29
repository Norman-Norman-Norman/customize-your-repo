---
name: create-agent-skill
description: Creates new Agent Skills following the agentskills.io specification. Use when the user wants to create a skill, build agent capabilities, package procedural knowledge, or create reusable instructions for AI agents. Handles SKILL.md generation, directory structure, frontmatter validation, and best practices.
metadata:
  author: customize-your-repo
  version: "1.0"
  spec-version: agentskills.io
---

# Create Agent Skill

## When to Use This Skill

Use this skill when:
- User wants to create a new agent skill
- User wants to package knowledge or workflows for AI agents
- User mentions "skill", "agent capability", or "reusable instructions"
- User wants to extend agent capabilities with domain expertise

## Directory Structure

Every skill requires this minimum structure:

```
skill-name/
└── SKILL.md          # Required: instructions + metadata
```

Optional directories for complex skills:

```
skill-name/
├── SKILL.md          # Required
├── scripts/          # Optional: executable code (Python, Bash, JS)
├── references/       # Optional: additional documentation
└── assets/           # Optional: templates, images, data files
```

**Important:** Place skills in `.github/skills/` for repository-level skills.

## SKILL.md Format

### Required Frontmatter

```yaml
---
name: skill-name
description: A description of what this skill does and when to use it.
---
```

### Frontmatter Field Rules

| Field | Required | Rules |
|-------|----------|-------|
| `name` | Yes | 1-64 chars, lowercase alphanumeric + hyphens only, no leading/trailing/consecutive hyphens, must match directory name |
| `description` | Yes | 1-1024 chars, describe WHAT it does AND WHEN to use it, include keywords for discovery |
| `license` | No | License name or reference to LICENSE file |
| `compatibility` | No | 1-500 chars, environment requirements if any |
| `metadata` | No | Key-value pairs for author, version, etc. |
| `allowed-tools` | No | Space-delimited list of pre-approved tools (experimental) |

### Name Validation

✅ **Valid names:**
- `pdf-processing`
- `code-review`
- `data-analysis`

❌ **Invalid names:**
- `PDF-Processing` (uppercase not allowed)
- `-pdf` (cannot start with hyphen)
- `pdf-` (cannot end with hyphen)
- `pdf--processing` (consecutive hyphens not allowed)

### Writing Good Descriptions

✅ **Good description:**
```yaml
description: Extracts text and tables from PDF files, fills PDF forms, and merges multiple PDFs. Use when working with PDF documents or when the user mentions PDFs, forms, or document extraction.
```

❌ **Poor description:**
```yaml
description: Helps with PDFs.
```

The description should:
1. State what the skill DOES (capabilities)
2. State WHEN to use it (trigger conditions)
3. Include keywords agents will match against

## Body Content Best Practices

After frontmatter, write Markdown instructions. Recommended sections:

1. **When to use this skill** - Clear trigger conditions
2. **Step-by-step instructions** - How to perform the task
3. **Examples** - Inputs and expected outputs
4. **Edge cases** - Common issues and how to handle them
5. **File references** - Links to scripts or references if needed

### Progressive Disclosure

Structure content for efficient context use:

| Level | Token Budget | Content |
|-------|--------------|---------|
| Metadata | ~100 tokens | `name` + `description` (loaded at startup for ALL skills) |
| Instructions | <5000 tokens | Full SKILL.md body (loaded when skill activates) |
| Resources | As needed | scripts/, references/, assets/ (loaded on demand) |

**Keep SKILL.md under 500 lines.** Move detailed reference material to separate files.

## Step-by-Step: Creating a New Skill

1. **Choose a name** - lowercase, hyphens only, descriptive
2. **Create directory** - `.github/skills/{skill-name}/`
3. **Write SKILL.md** with:
   - Valid frontmatter (name + description minimum)
   - Clear instructions in the body
   - Examples where helpful
4. **Add optional directories** if needed:
   - `scripts/` for executable code
   - `references/` for detailed documentation
   - `assets/` for templates and resources
5. **Validate** the skill structure

## Template

Use this template for new skills:

```markdown
---
name: {skill-name}
description: {What it does}. Use when {trigger conditions}.
metadata:
  author: {your-org}
  version: "1.0"
---

# {Skill Title}

## When to Use This Skill

Use this skill when:
- {Condition 1}
- {Condition 2}

## Instructions

{Step-by-step instructions for the agent}

## Examples

### Example 1: {Scenario}

**Input:** {What the user asks}

**Output:** {What the agent produces}

## Edge Cases

- {Edge case 1}: {How to handle}
- {Edge case 2}: {How to handle}
```

## File References

When referencing other files, use relative paths from skill root:

```markdown
See [the reference guide](references/REFERENCE.md) for details.

Run the extraction script:
scripts/extract.py
```

Keep references one level deep. Avoid deeply nested chains.

## Validation

After creating a skill, verify:
1. Directory name matches `name` field exactly
2. `name` follows naming rules (lowercase, hyphens, no consecutive)
3. `description` is non-empty and under 1024 characters
4. SKILL.md has valid YAML frontmatter
5. Body content is clear and actionable

## Reference

- Specification: https://agentskills.io/specification
- Examples: https://github.com/anthropics/skills
- Validation tool: https://github.com/agentskills/agentskills/tree/main/skills-ref
