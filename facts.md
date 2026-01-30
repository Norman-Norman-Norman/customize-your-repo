# Facts to Verify Against VS Code Source

This file lists factual claims from the documentation that should be verified against the VS Code source code at https://github.com/microsoft/vscode.

## File Locations

| # | Claim | Source Doc |
|---|-------|-----------|
| 1 | Always-on instructions file is at `.github/copilot-instructions.md` | part-2-1 |
| 2 | File-based instructions are at `.github/instructions/*.instructions.md` | part-2-2 |
| 3 | Prompt files are at `.github/prompts/*.prompt.md` | part-2-3 |
| 4 | Skills are at `.github/skills/*/SKILL.md` | part-2-4 |
| 5 | Custom agents are at `.github/agents/*.md` (or `**/*.agent.md` anywhere) | part-2-5 |
| 6 | MCP config is at `.vscode/mcp.json` | part-2-6 |

## Frontmatter Fields

| # | Claim | Source Doc |
|---|-------|-----------|
| 7 | File-based instructions support `applyTo` field for glob patterns | part-2-2 |
| 8 | File-based instructions support `name` and `description` fields | part-2-2 |
| 9 | Prompt files support `agent` field (modes: ask, edit, agent) | part-2-3 |
| 10 | Prompt files support `model` field for specifying AI model | part-2-3 |
| 11 | Prompt files support `tools` field for restricting available tools | part-2-3 |
| 12 | Prompt files support `argument-hint` field | part-2-3 |
| 13 | Custom agents support `handoffs` field for agent transitions | part-2-5 |
| 14 | Skills use `name`, `description`, `metadata`, `license`, `compatibility` fields | part-2-4 |

## Tool Names

| # | Claim | Source Doc |
|---|-------|-----------|
| 15 | Tool names include: `editFiles`, `createFile`, `runInTerminal` | part-2-3 |
| 16 | Tool names include: `search`, `readFile`, `usages` | part-2-5 |
| 17 | Tool names include: `fetch`, `githubRepo` | part-2-3, part-2-5 |
| 18 | Tool name: `changes` exists | part-2-5 |
| 19 | Tool name: `terminalLastCommand` exists | part-2-5 |
| 20 | Tool name: `runInTerminal` exists | part-2-5 |

## Behavior Claims

| # | Claim | Source Doc |
|---|-------|-----------|
| 21 | Always-on instructions load "at the start of every Copilot session" | part-2-1 |
| 22 | File-based instructions activate via glob pattern matching on `applyTo` | part-2-2 |
| 23 | Skills load on-demand via "description matching" | part-2-4 |
| 24 | Skill names must be lowercase, hyphens only, no consecutive hyphens | part-2-4 |
| 25 | Skill names are 1-64 characters | part-2-4 |
| 26 | Skill descriptions are 1-1024 characters | part-2-4 |

## VS Code Commands and UI

| # | Claim | Source Doc |
|---|-------|-----------|
| 27 | Debug via: "Chat: Diagnostics" and Developer commands | part-1, part-2-4 |
| 28 | Configure menu opens pickers with "New instruction file..." option | part-2-1 |
| 29 | Configure menu opens pickers with "New prompt file..." option | part-2-3 |
| 30 | Configure menu opens pickers with "Create new custom agent..." option | part-2-5 |

## MCP Configuration

| # | Claim | Source Doc |
|---|-------|-----------|
| 31 | MCP servers configured via `.vscode/mcp.json` file | part-2-6 |
| 32 | MCP config supports `command`, `args`, `env` fields | part-2-6 |

## Verification Status

| # | Status | Notes |
|---|--------|-------|
| 1 | ✅ VERIFIED | Correct |
| 2 | ✅ VERIFIED | Correct |
| 3 | ✅ VERIFIED | Correct |
| 4 | ✅ VERIFIED | Correct |
| 5 | ✅ FIXED | Was `.github/agents/*.agent.md`, corrected to `.github/agents/*.md` |
| 6 | ✅ FIXED | Was settings.json, corrected to `.vscode/mcp.json` |
| 7-14 | ✅ VERIFIED | All frontmatter fields correct |
| 15 | ✅ FIXED | `terminalCommand` → `runInTerminal` |
| 16 | ✅ VERIFIED | Correct |
| 17 | ✅ FIXED | `githubIssue` doesn't exist (use @github participant) |
| 18 | ✅ FIXED | `getChangedFiles` → `changes` |
| 19-20 | ✅ VERIFIED | Correct |
| 21-26 | ✅ VERIFIED | Correct |
| 27 | ✅ FIXED | Command doesn't exist, updated to "Chat: Diagnostics" |
| 28-30 | ✅ FIXED | Not nested submenus, they're quick pick items in dialogs |
| 31 | ✅ FIXED | Not a setting, uses `.vscode/mcp.json` file |
| 32 | ✅ VERIFIED | Fields correct, also supports `cwd`, `envFile`, `type` |
