---
name: refine
description: "Refine CLAUDE.md, SKILL.md, settings.json, and hooks — remove duplication, resolve conflicts, tighten content."
argument-hint: "[global|project|all] [opus|sonnet|haiku]"
---

# /refine — Config & Docs Refiner

Analyze and refine Claude Code configuration files (CLAUDE.md, SKILL.md, settings.json, hooks, scripts). Removes duplication, resolves conflicts, fixes inconsistencies, and tightens content without changing behavior.

## Usage

```
/refine                → refine current project (default = project)
/refine global         → refine ~/.claude/ + ~/Projects/ root-level config
/refine all            → global + every project under ~/Projects/
/refine sonnet         → refine this project with sonnet
/refine all haiku      → refine everything with haiku
```

## Arguments

```
$ARGUMENTS
```

**Scope:** `global` or `all` keyword sets the scope. Default: `project`.
**Model:** `opus`, `sonnet`, or `haiku` keyword sets the model. Default: `opus`.

## What It Does

- Detects duplicate rules/instructions across files
- Identifies conflicting directives (e.g., model=opus vs model=sonnet)
- Replaces hardcoded paths, keys, project names with parameters
- Merges repeated skill templates
- Fixes settings.json inconsistencies
- Presents diffs for approval before applying

## What It Does NOT Do

- Add new features, skills, or hooks
- Change existing behavior (only expresses the same thing more cleanly)
- Delete files without approval
- Touch project source code (lib/, src/, test/)

## Scope Files

### `/refine global`
- `~/.claude/CLAUDE.md`, `settings.json`, `settings.local.json`, `mcp.json`
- `~/.claude/skills/**/SKILL.md`
- `~/Projects/CLAUDE.md`

### `/refine project`
- `$(pwd)/CLAUDE.md`
- `$(pwd)/.claude/settings.json`, `settings.local.json`
- `$(pwd)/.mcp.json`
- `$(pwd)/.claude/skills/**/SKILL.md`
- `$(pwd)/docs/CLAUDE*.md`, `$(pwd)/docs/*.md` (config-related)
- `$(pwd)/scripts/*.sh`

### `/refine all`
Global scope + project scope for every `~/Projects/*/`.

## Workflow

### Step 1 — Scan

Read all in-scope files. For each, check:

| Check | Description |
|-------|-------------|
| **Duplication** | Same rule in multiple files — which is authoritative? |
| **Conflict** | Two files say contradictory things |
| **Hardcode** | Project name, path, Jira key, Firebase ID hardcoded |
| **Stale content** | Referenced file/function no longer exists, old TODO |
| **Bloat** | Unnecessarily verbose explanation, examples, repetition |
| **MCP clash** | Same MCP in settings.json + mcp.json with different config |
| **Permission inconsistency** | Same pattern in both allow and ask |
| **Hook duplication** | Same hook at global and project level |
| **Skill duplication** | Multiple skills with near-identical prompts |

### Step 2 — Report

Present findings with diff previews and ask for approval.

### Step 3 — Apply

1. **Backup** — For any JSON file with >50 lines to be edited, first copy it:
   ```bash
   cp <file> <file>.bak
   ```

2. **Apply** — Apply approved changes via Edit tool.

3. **Validate JSON** — After editing any `.json` file, validate immediately:
   ```bash
   python3 -m json.tool <file> > /dev/null && echo "valid" || echo "INVALID — restore from .bak"
   ```
   Alternative if python3 not available:
   ```bash
   jq . <file> > /dev/null && echo "valid" || echo "INVALID — restore from .bak"
   ```

4. **Rollback** — If validation fails:
   ```bash
   cp <file>.bak <file>
   ```
   Then report the failure and do not attempt further edits to that file.

5. **Cleanup** — Remove `.bak` files after successful validation:
   ```bash
   rm -f <file>.bak
   ```

## Constraints

- Max 50 tool calls per agent
- No file deletion without approval
- Source code (lib/, src/, test/) is out of scope
- `/refine all` processes max 10 projects; reports the rest
- JSON files must pass parse validation after edits
