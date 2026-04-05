---
name: memory-prune
description: "Scan ~/.claude/projects/ memory files — list stale/unused entries, prompt user to delete or keep each."
argument-hint: "[--dry-run|--auto]"
---

# /memory-prune — Memory Cleanup

Scan all memory files under `~/.claude/projects/` and identify stale, duplicate, or orphaned entries. Present findings to the user and apply their choices (delete / keep).

## Usage

```
/memory-prune             → interactive: list findings, ask per item
/memory-prune --dry-run   → list only, no deletions
/memory-prune --auto      → delete all stale automatically (no prompts)
```

## Arguments

```
$ARGUMENTS
```

If `--dry-run` is present, report only — do not delete anything.
If `--auto` is present, delete all identified stale files without prompting.
Otherwise, prompt the user interactively for each finding.

## Execution

Run via background agent:

```python
Agent(
  prompt=<memory-prune prompt below>,
  model="sonnet",
  run_in_background=True,
  description="memory prune scan"
)
```

### Agent Prompt

```
You are a memory maintenance assistant. Your job is to scan Claude memory files,
identify stale or low-value entries, and help the user clean them up.

MODE: [dry-run | auto | interactive — from $ARGUMENTS, default: interactive]

## SCAN TARGETS

1. `~/.claude/projects/` — all subdirectories, all `memory/*.md` files
2. `~/.claude/projects/*/memory/MEMORY.md` — the index file in each project

## STALENESS CRITERIA

Mark a memory file as STALE if ANY of the following apply:

- **Outdated reference:** mentions a library, API, tool, or version that has been
  superseded or removed (check surrounding memory files for contradictions)
- **Duplicate content:** another memory file in the same project covers the same
  topic with newer or more complete information
- **Orphaned project:** the project directory the memory belongs to no longer
  exists on disk (parent path is missing)
- **Empty / trivial:** file body is fewer than 3 meaningful lines after the
  frontmatter, or contains only placeholder text
- **Superseded decision:** frontmatter `type: feedback` and a newer feedback file
  explicitly reverses or replaces the decision described

Do NOT mark as stale:
- Files referenced in an active MEMORY.md index (unless another criterion applies)
- Files modified within the last 14 days
- Files with `type: reference` that contain non-trivial factual content

## RULES

- Read files — do NOT delete anything unless MODE is `auto` or user confirms
- Max 40 tool calls — be efficient; scan broadly before reading details
- Report every finding with file path + reason
- For MEMORY.md index files: after deletions, remove the corresponding index lines
  and rewrite the file (do not leave broken links)

## REPORT FORMAT

For each stale file found:

### [STALE] <short filename>
- **Path:** ~/.claude/projects/.../memory/<file>.md
- **Reason:** <one of the staleness criteria above>
- **Last modified:** <date if available>
- **Action:** [PENDING | DELETED | KEPT]

After listing all findings, if MODE is `interactive`:
  Ask the user: "Delete these N files? Reply with file numbers to keep (e.g. 1,3)
  or press Enter to delete all."
  Apply choices: delete unprotected files, keep protected ones.

If MODE is `auto`:
  Delete all stale files immediately and report what was removed.

If MODE is `dry-run`:
  Report only — no deletions.

## POST-CLEANUP

After applying deletions:
1. For each affected project, rewrite `memory/MEMORY.md` — remove lines whose
   target files were deleted, keep all others intact
2. Print a summary:

```
Memory prune complete.
Scanned : N files across M projects
Stale   : X identified
Deleted : Y files
Kept    : Z files (user choice or --dry-run)
```
```

## Output

Show the report to the user when the agent completes. Highlight any MEMORY.md
index files that were updated.

## Constraints

- Never delete files outside `~/.claude/projects/`
- Never delete `MEMORY.md` index files themselves — only update their contents
- Confirm before deleting in interactive mode
- `--dry-run` always takes precedence over `--auto`
