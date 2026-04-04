---
name: index
description: "Index the current project with jCodeMunch. Enables automatic index updates on session start."
argument-hint: "[force] — force rebuilds the index from scratch"
---

# /index — jCodeMunch Project Indexing

Index the current working directory with jCodeMunch. Also activates automatic index updates — subsequent Claude sessions in this directory will silently refresh the index.

## Usage

```
/index          → index (update if exists, create if not) + enable auto-update
/index force    → delete existing index, rebuild from scratch
```

## Arguments

```
$ARGUMENTS
```

If `force` is present, rebuild from scratch. Otherwise, incremental update.

## Workflow

### 1. Check jCodeMunch MCP

Verify jCodeMunch MCP is connected. If not:
```
jCodeMunch MCP is not connected. Cannot index.
Check global settings.json for jcodemunch definition, add if missing.
```

### 2. Index

```python
# Resolve repo
resolve_repo(path=os.getcwd())

# Index
index_folder(path=os.getcwd())
```

For `force` mode:
```python
# Force re-index from scratch
index_folder(path=os.getcwd(), force=True)
```

### 3. Create/Update Marker

```bash
mkdir -p .claude && { date -u +%FT%TZ; git rev-parse HEAD 2>/dev/null; } > .claude/jcodemunch_indexed
```

This marker signals the migration hook that the project is indexed and should be refreshed when HEAD changes.

### 4. Output

```
jCodeMunch index complete.
- Project: [directory name]
- Auto-update: active (silently refreshed each session)
- Manual update: /index
```

## Notes

- First-time indexing may take 10-30s on large projects
- Subsequent updates are incremental — only changed files, very fast
- `.claude/jcodemunch_indexed` should be in `.gitignore` (local state)
