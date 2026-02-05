```skill
---
name: editor
description: Reviews and critiques documentation for quality, accuracy, and consistency. Use when files are modified, when reviewing content before commit, or when the user asks for editorial review. Identifies issues with grammar, formatting, broken links, outdated information, and style violations.
metadata:
  author: customize-your-repo
  version: "1.0"
  spec-version: agentskills.io
---

# Editor Skill

## When to Use This Skill

Use this skill when:
- A documentation file has been modified
- User asks to review or proofread content
- Before committing changes to documentation
- User mentions "review", "edit", "check", or "proofread"
- Validating content against style guidelines

## Review Checklist

For every file reviewed, check the following categories:

### 1. Accuracy
- [ ] Technical claims are verifiable against official documentation
- [ ] Code examples are syntactically correct
- [ ] File paths and configuration options exist
- [ ] Links are valid and point to correct destinations
- [ ] Version numbers and dates are current

### 2. Consistency
- [ ] Terminology is consistent throughout the document
- [ ] Formatting follows established patterns
- [ ] Heading hierarchy is logical (H2 → H3 → H4)
- [ ] Code block language identifiers are correct
- [ ] Emoji and symbol usage is consistent

### 3. Completeness
- [ ] All sections have content (no empty placeholders)
- [ ] Examples include both good and bad patterns where applicable
- [ ] Cross-references to related sections exist
- [ ] Table of contents matches actual sections (if present)

### 4. Clarity
- [ ] Sentences are clear and direct
- [ ] Jargon is explained or avoided
- [ ] Instructions are actionable
- [ ] Ambiguous pronouns are clarified

### 5. Style
- [ ] Professional, third-person tone
- [ ] No first person ("I think...")
- [ ] No filler phrases ("In order to...", "It is important to note that...")
- [ ] Active voice preferred

### 6. Formatting
- [ ] Markdown renders correctly
- [ ] Tables are properly formatted
- [ ] Code blocks use appropriate syntax highlighting
- [ ] Lists are parallel in structure
- [ ] No trailing whitespace or extra blank lines

## Issue Severity Levels

Report issues using these severity levels:

| Severity | Icon | Description | Action Required |
|----------|------|-------------|-----------------|
| **Critical** | 🔴 | Factually incorrect, broken functionality, security issue | Must fix before publish |
| **Major** | 🟠 | Confusing content, missing important information, broken links | Should fix |
| **Minor** | 🟡 | Style inconsistencies, typos, formatting issues | Nice to fix |
| **Suggestion** | 🔵 | Improvements that would enhance quality | Consider |

## Output Format

Present findings in this format:

```markdown
## Editorial Review: {filename}

### Summary
{Brief overview of document quality and main issues found}

### Critical Issues 🔴
- **Line {N}:** {Issue description}
  - **Problem:** {What's wrong}
  - **Fix:** {How to correct it}

### Major Issues 🟠
- **Line {N}:** {Issue description}
  - **Problem:** {What's wrong}
  - **Fix:** {How to correct it}

### Minor Issues 🟡
- {Issue and suggested fix}

### Suggestions 🔵
- {Enhancement suggestion}

### Verdict
{PASS | NEEDS REVISION | REQUIRES MAJOR CHANGES}
```

## Instructions

When reviewing a file:

1. **Read the full content** to understand context and purpose
2. **Check against the review checklist** systematically
3. **Verify external references** - fetch documentation if claims need validation
4. **Note line numbers** for each issue found
5. **Provide specific fixes** - not just "this is wrong" but "change X to Y"
6. **Prioritize issues** by severity
7. **Give a verdict** on whether the content is ready to publish

## Example Review

### Input
User modifies `docs/part-2-1-always-on-instructions.md`

### Output

```markdown
## Editorial Review: part-2-1-always-on-instructions.md

### Summary
Generally well-structured document covering always-on instructions. Found 1 critical issue (outdated setting name), 2 major issues (broken link, missing section), and 3 minor formatting inconsistencies.

### Critical Issues 🔴
- **Line 18:** Setting name may be outdated
  - **Problem:** `github.copilot.chat.codeGeneration.useInstructionFiles` - verify this setting still exists in current VS Code
  - **Fix:** Fetch VS Code documentation to confirm current setting name

### Major Issues 🟠
- **Line 45:** Internal link not working
  - **Problem:** Link to `#creating-files` section doesn't match any heading
  - **Fix:** Update to `#creating-an-instructions-file`

### Minor Issues 🟡
- Line 67: Inconsistent heading level (H4 should be H3)
- Line 123: Code block missing language identifier
- Line 201: Extra blank line

### Suggestions 🔵
- Consider adding a "Quick Start" section for users who want minimal setup
- The example at line 89 could benefit from comments

### Verdict
NEEDS REVISION - Fix critical and major issues before publishing
```

## Edge Cases

- **Empty files:** Report as critical issue, suggest content structure
- **Binary files:** Skip review, note that file is not reviewable
- **Very large files (>1000 lines):** Review in sections, note if file should be split
- **Non-English content:** Note language and skip style checks that don't apply
- **Generated files:** Check if file should be in .gitignore instead of reviewed

## Auto-Trigger Conditions

This skill should activate automatically when:
- Any `.md` file in `docs/` is modified
- `ReadMe.md` or `README.md` is modified
- Any file matching `*.instructions.md` is modified
- User explicitly requests a review

## Validation Sources

When verifying technical accuracy, check against:
- https://code.visualstudio.com/docs/copilot (VS Code Copilot docs)
- https://docs.github.com/en/copilot (GitHub Copilot docs)
- Source code and configuration files in the repository
```
