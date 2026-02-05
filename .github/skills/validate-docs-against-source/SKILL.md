```skill
---
name: validate-docs-against-source
description: Validates documentation content against official source repositories and documentation. Use when updating Copilot customization documentation, editing content about GitHub Copilot features, committing changes to docs, or when the user mentions verifying facts, checking sources, or validating accuracy.
metadata:
  author: customize-your-repo
  version: "1.2"
  spec-version: agentskills.io
---

# Validate Documentation Against Source

## When to Use This Skill

Use this skill when:
- User updates content in documentation files about GitHub Copilot
- User edits files describing Copilot primitives (instructions, prompts, agents, skills, MCP)
- User explicitly asks to verify facts or check accuracy
- User mentions "validate", "verify", "check source", or "fact-check"
- Content references file paths, settings, or configuration options
- **Before any commit** that includes changes to documentation files
- User is preparing a PR with documentation changes

## Pre-Commit Validation (Required)

**This skill MUST run before committing documentation changes.**

When the user attempts to commit changes to files in `docs/` or any `*.md` files that document Copilot features:

1. Automatically identify all claims that reference official features
2. Validate against primary sources
3. **If discrepancies are found:** Prompt the user before proceeding

### Pre-Commit Workflow

```
User: "commit these changes" / "git commit" / prepares commit

Agent:
1. Detect documentation files in staged changes
2. Run validation on changed content
3. If all clear → Proceed with commit
4. If issues found → Prompt user (see below)
```

## Primary Sources

Always validate against these authoritative sources:

| Topic | Primary Source |
|-------|----------------|
| VS Code Copilot features | https://code.visualstudio.com/docs/copilot |
| Custom instructions | https://code.visualstudio.com/docs/copilot/customization/custom-instructions |
| Prompt files | https://code.visualstudio.com/docs/copilot/customization/prompt-files |
| Custom agents | https://code.visualstudio.com/docs/copilot/customization/custom-agents |
| Agent skills | https://code.visualstudio.com/docs/copilot/customization/agent-skills |
| MCP servers | https://code.visualstudio.com/docs/copilot/customization/mcp-servers |
| GitHub Copilot docs | https://docs.github.com/en/copilot |
| Repository instructions | https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot |
| Agent Skills spec | https://agentskills.io/specification |
| Source code (VS Code) | https://github.com/microsoft/vscode |
| Source code (Copilot Chat) | https://github.com/microsoft/vscode-copilot-chat |

## Version Tracking (Required)

**Always identify and report the version context for all validated content.**

### Determining Current GA Versions

Before validating, fetch current GA (Generally Available) versions:

| Product | Version Source | How to Check |
|---------|---------------|--------------|
| VS Code | https://code.visualstudio.com/updates | Look for "Version X.YZ" at top |
| Copilot Chat Extension | VS Code Marketplace or release tags | Check `microsoft/vscode-copilot-chat` releases |
| GitHub Copilot (web) | https://github.blog/changelog | Search for Copilot announcements |

### Version Status Categories

Mark each documented feature with its version status:

| Status | Meaning | Action |
|--------|---------|--------|
| 🟢 **GA** | Feature is in current stable release | Document normally |
| 🟡 **Preview** | Feature is in VS Code Insiders or preview | Add clear "Preview" label |
| 🔵 **Future** | Feature announced but not yet released | Add prominent warning |
| 🔴 **Deprecated** | Feature has been removed or replaced | Update or remove content |

### Version Callout Format

**Always include version context in validation reports:**

```markdown
## Version Context

**Validated against:**
- VS Code: {version} (GA/Insiders)
- Copilot Chat Extension: v{version}
- Documentation date: {date on docs page}

**Content references:**
- GA features: {count}
- Preview features: {count} ⚠️
- Future features: {count} ⚠️
```

### Future Version Warnings

**When content references features not yet in GA, add a prominent warning:**

```markdown
⚠️ **FUTURE VERSION REFERENCE DETECTED**

The following content references features not yet generally available:

| Feature | Current Status | Expected GA |
|---------|---------------|-------------|
| {feature} | Insiders only | {if known} |
| {feature} | Announced | {if known} |

**This documentation describes upcoming functionality.**
Readers using the stable VS Code release may not have access to these features.

**Options:**
1. **Add version badge** - Mark section with "Available in VS Code Insiders" or "Coming soon"
2. **Move to future section** - Separate from GA content
3. **Remove for now** - Document only when GA
4. **Keep as-is** - Proceed with clear labeling (requires explicit confirmation)
```

### Version Mismatch Prompt

When documented version doesn't match GA:

```
⚠️ **Version Mismatch Detected**

Your documentation appears to reference **VS Code {doc_version}** features,
but the current GA release is **VS Code {ga_version}**.

| Feature | Documented As | Actually Available In |
|---------|--------------|----------------------|
| {feature} | GA | Insiders {version}+ |
| {setting} | Default: {value} | Default changed in {version} |

**How would you like to proceed?**

1. **Update to GA** - Adjust content to match current stable release
2. **Add version note** - Keep content but add "Requires VS Code {version}+"
3. **Split content** - Separate GA features from preview/future features
4. **Confirm as-is** - Document is intentionally forward-looking
```

### Checking Version Information

When validating, always check:

1. **VS Code release notes** - https://code.visualstudio.com/updates
   - Features are listed by version with "New in {version}" markers
   
2. **Copilot Chat changelog** - Check GitHub releases
   - Extension version determines available features
   
3. **Documentation page dates** - Most VS Code docs show last updated date
   - Bottom of page or in page metadata
   
4. **GitHub docs versioning** - Some pages have version selectors
   - Ensure you're viewing the current version, not fptc (future)

### Version in Commit Messages

When committing documentation that references specific versions:

```
docs: update always-on instructions for VS Code 1.109

- Verified against VS Code 1.109 GA release
- Added /init command (new in 1.108)
- Updated setting names per 1.107 changes
```

## Validation Workflow

### Step 0: Determine Current GA Versions (Always First)

Before any content validation:

1. Fetch https://code.visualstudio.com/updates to get current VS Code GA version
2. Check Copilot Chat extension version from marketplace or releases
3. Note the documentation page's last-updated date
4. Record these in the validation report header

### Step 1: Identify Claims to Validate

Scan the content for:
- **File paths** (e.g., `.github/copilot-instructions.md`)
- **Setting names** (e.g., `github.copilot.chat.codeGeneration.useInstructionFiles`)
- **Feature descriptions** (e.g., "instructions are applied to inline suggestions")
- **Frontmatter fields** (e.g., `applyTo`, `description`)
- **Behavioral claims** (e.g., "Copilot prioritizes personal instructions over repository instructions")

### Step 2: Fetch Current Documentation

Use web fetch tools to retrieve the current official documentation:

1. Fetch the relevant VS Code docs page for the topic
2. Fetch the relevant GitHub docs page if applicable
3. Search source repositories for implementation details if needed

### Step 3: Compare and Flag Discrepancies

For each claim, determine:
- ✅ **Confirmed** - Claim matches official documentation for current GA version
- ⚠️ **Outdated** - Information was accurate but has changed
- ❌ **Incorrect** - Claim contradicts official documentation
- ❓ **Unverifiable** - Cannot find authoritative source
- 🔵 **Future** - Feature exists but only in preview/Insiders (not GA)

### Step 4: Report Findings

Provide a summary:

```markdown
## Validation Report

**File:** {filename}
**Date:** {current date}

### Version Context
| Product | GA Version | Doc References |
|---------|------------|----------------|
| VS Code | {version} | {version if mentioned} |
| Copilot Chat | v{version} | {version if mentioned} |

**Version Status:** 🟢 All GA / 🟡 Contains Preview / 🔵 Contains Future

### Findings

| Claim | Status | Version | Notes |
|-------|--------|---------|-------|
| {claim 1} | ✅/⚠️/❌/❓/🔵 | GA/Preview/Future | {explanation} |
| {claim 2} | ✅/⚠️/❌/❓/🔵 | GA/Preview/Future | {explanation} |

### Version Warnings

{If any 🔵 Future items exist:}
⚠️ This documentation references {N} features not yet in GA release.
See "Future Version Warnings" section for details.

### Recommended Updates

1. {Update 1}
2. {Update 2}
```

## User Prompts for Challenges

**When validation finds issues (⚠️, ❌, or ❓), you MUST prompt the user before proceeding.**

### Prompt Templates

#### When Discrepancies Found (Pre-Commit)

```
⚠️ **Validation found {N} issue(s) in your documentation changes:**

| Issue | Claim | Problem |
|-------|-------|---------|
| 1 | "{claim}" | {what's wrong} |
| 2 | "{claim}" | {what's wrong} |

**How would you like to proceed?**

1. **Fix now** - Update the content to match official documentation
2. **Commit anyway** - Proceed with current content (not recommended)
3. **Review details** - Show full validation report with sources
4. **Cancel** - Abort the commit to investigate further
```

#### When Source Cannot Be Verified

```
❓ **Unable to verify {N} claim(s) against official sources:**

- "{claim 1}" - No authoritative source found
- "{claim 2}" - Documentation page unavailable

**Options:**

1. **Add citation** - Mark these claims with [needs verification]
2. **Remove claims** - Delete unverifiable statements
3. **Proceed anyway** - Commit with unverified content
4. **Research more** - I'll search additional sources
```

#### When Content is Outdated

```
⚠️ **{N} item(s) may be outdated:**

| Item | Current Doc Says | Your Content Says |
|------|------------------|-------------------|
| {setting/path} | {official value} | {your value} |

**This likely changed in a recent VS Code/Copilot update.**

1. **Update to current** - Apply the official values
2. **Keep as-is** - Your content may be intentionally different
3. **Show diff** - Compare full context from both sources
```

### Decision Recording

After user responds, record the decision:

```markdown
<!-- Validation: {date} -->
<!-- Decision: {user choice} -->
<!-- Reviewer: {user/auto} -->
```

This creates an audit trail for future reference.

## Blocking Commits

If critical issues are found (❌ status), **strongly recommend** blocking the commit:

```
🛑 **Critical accuracy issue detected**

The claim "{claim}" directly contradicts official documentation:
- **Your content:** {what you wrote}
- **Official docs:** {what docs say}
- **Source:** {url}

This could mislead readers. I recommend fixing before committing.

**Proceed anyway?** (type "yes" to override)
```

Only proceed if user explicitly confirms.

## What to Validate

### Always Check

- File paths for configuration files
- VS Code setting names and their default values
- Frontmatter field names and allowed values
- Feature availability (which features apply to which contexts)
- Priority/precedence rules
- Required vs optional configurations

### Common Pitfalls

| Claim Type | Common Issue |
|------------|--------------|
| File paths | Locations may change between versions |
| Settings | Settings may be deprecated or renamed |
| Feature scope | Features may apply to Chat only, not inline suggestions |
| Syntax | Frontmatter fields or glob patterns may have specific rules |
| Defaults | Default values for settings change |

## Example: Validating Always-On Instructions

**Content being validated:**
> Custom instructions are applied to all Copilot interactions including inline suggestions.

**Validation steps:**
1. Fetch https://code.visualstudio.com/docs/copilot/customization/custom-instructions
2. Search for "inline suggestions" or "ghost text"
3. Find: "Custom instructions are not taken into account for inline suggestions as you type"

**Result:** ❌ Incorrect

**Recommended fix:** Update to state that custom instructions only affect Copilot Chat, not inline suggestions.

## Handling Unverifiable Claims

If a claim cannot be verified:
1. Check if it's an opinion or recommendation (doesn't need verification)
2. Search source repositories for implementation
3. Flag for manual review with note about what was checked
4. Consider adding "[needs citation]" or removing the claim

## Integration with Updates

When content is updated:
1. **Before committing:** Run validation on changed sections
2. **After fetching sources:** Compare key facts
3. **On discrepancy:** Either update content or flag for human review
4. **Always:** Add or update citation links (format: `[*](https://...)`) for verifiable claims

## Quick Reference: Key Settings

These settings are frequently referenced and should be validated:

| Setting | Purpose | Default |
|---------|---------|---------|
| `github.copilot.chat.codeGeneration.useInstructionFiles` | Enable `.github/copilot-instructions.md` | Check docs |
| `chat.instructionsFilesLocations` | Folders for `.instructions.md` files | `.github/instructions` |
| `chat.useAgentsMdFile` | Enable `AGENTS.md` support | Check docs |
| `chat.useAgentSkills` | Enable skills loading | Check docs |
| `chat.includeApplyingInstructions` | Auto-apply instructions with `applyTo` | Check docs |

**Note:** Always fetch current documentation to verify default values, as they may change.
```