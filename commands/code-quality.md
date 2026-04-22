---
name: code-quality
description: "Combined code quality workflow — index → audit → optional refine. Single unified report."
argument-hint: "[--no-index|--no-refine|--refine]"
---

# /code-quality — Combined Quality Workflow

Run the full code quality pipeline in a single command: index the project with jCodeMunch, scan for issues, and optionally refine config files. Produces a unified report.

## Usage

```
/code-quality              → index + audit (all categories), no refine
/code-quality --refine     → index + audit + refine current project config
/code-quality --no-index   → skip indexing, audit only
/code-quality --no-refine  → same as default (explicit)
```

## Arguments

```
$ARGUMENTS
```

Parse flags:
- `--refine` → also run `/refine project` after audit
- `--no-index` → skip jCodeMunch indexing step
- `--no-refine` → skip refine (default behavior)

## Execution

Run sequentially — each step depends on the previous completing successfully.

```python
Agent(
  prompt=<pipeline prompt below>,
  model="sonnet",
  run_in_background=True,
  description="code-quality pipeline"
)
```

### Agent Prompt

```
You are a code quality pipeline agent. Run the following steps in order.

FLAGS: [parse from $ARGUMENTS]
  - skip_index: --no-index present
  - run_refine: --refine present

PROJECT ROOT: current working directory

---

## STEP 1 — LANGUAGE DETECTION

Detect primary language(s):
- `pubspec.yaml` → Flutter/Dart
- `package.json` → JavaScript/TypeScript
- `requirements.txt` / `pyproject.toml` → Python
- `go.mod` → Go
- File extensions in src/lib/root as fallback

Report:
```
Stack: [detected stack]
```

---

## STEP 2 — INDEX (skip if --no-index)

Check if jCodeMunch MCP is available. If yes:
1. Call `resolve_repo(path=os.getcwd())`
2. Call `index_folder(path=os.getcwd())` (incremental)
3. Report: "jCodeMunch index: updated"

If jCodeMunch not available:
Report: "jCodeMunch index: skipped (MCP not connected)"

---

## STEP 3 — AUDIT

Run a focused code quality scan. Use jCodeMunch symbol data if available from Step 2.

Perform all 4 audit categories for the detected stack:

### SECURITY
- Hardcoded secrets (grep: apiKey, api_key, secret, password, token)
- .env in .gitignore?
- Stack-specific: see full /audit checklist per language

### COST
- Paid API calls without rate limiting
- Large assets (>500KB)
- Stack-specific cost risks

### PERFORMANCE
- Memory leaks, unreleased resources
- Stack-specific performance issues

### CLEANUP
- Dead code, TODO/FIXME, unused imports
- Missing .gitignore entries
- Stack-specific hygiene

Max 30 tool calls for audit. Be efficient — grep broadly, read to confirm.

---

## STEP 4 — REFINE (only if --refine)

Scan config files in current project:
- `CLAUDE.md`, `.claude/settings.json`, `.mcp.json`
- Identify: duplication, conflicts, stale content, bloat

Present a diff summary. Apply improvements.

---

## UNIFIED REPORT

Output a single consolidated report:

```
# Code Quality Report — [project name]
Date: [today]
Stack: [detected]
jCodeMunch: [updated | skipped]

## Audit Findings

[findings grouped by category]

## Summary Table

| # | Level | Category | File | Issue |
|---|-------|----------|------|-------|

## Overall Score: X/10

## Refine Changes (if --refine)
[list of config changes made]
```

Levels: RED CRITICAL / ORANGE HIGH / YELLOW MEDIUM / BLUE INFO

---

## RULES

- Read-only for audit — do NOT edit source files
- jCodeMunch failure is non-fatal — continue with audit
- Max 40 tool calls total across all steps
- Refine only touches config files (CLAUDE.md, settings.json, .mcp.json)
```

## Output

Show the unified report when the agent completes. Highlight critical findings.

## Constraints

- Source code (lib/, src/, test/) is read-only
- Refine only runs if `--refine` flag is present
- jCodeMunch indexing failure is non-fatal
- Max 40 tool calls total
