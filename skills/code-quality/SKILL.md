---
name: code-quality
description: "Auto-trigger skill for code quality auditing, config refinement, memory cleanup, and jCodeMunch indexing."
triggers:
  - "audit"
  - "code audit"
  - "security audit"
  - "cost audit"
  - "performance audit"
  - "code cleanup"
  - "clean up code"
  - "cleanup dead code"
  - "dead code"
  - "refine"
  - "refine config"
  - "refine claude"
  - "index"
  - "jcodemunch"
  - "index project"
  - "code quality"
  - "scan for issues"
  - "find vulnerabilities"
  - "unused imports"
  - "unused dependencies"
  - "memory prune"
  - "prune memory"
  - "clean memory files"
  - "stale memory"
  - "prune memory files"
  - "claude memory cleanup"
  - "memory cleanup"
---

# Code Quality Skill

This skill activates when the user mentions code auditing, config refinement, memory cleanup, or project indexing. It routes to the appropriate command.

## Routing

| User Intent | Command |
|-------------|---------|
| Audit code, scan for issues, security check, find vulnerabilities, dead code, code cleanup | `/audit` |
| Refine CLAUDE.md, clean up config, tighten settings, fix duplication | `/refine` |
| Index project, jCodeMunch, symbol search setup | `/index` |
| Memory prune, clean memory files, stale memory, memory cleanup | `/memory-prune` |
| Full code quality workflow, combined check | `/code-quality` |

## Behavior

1. **Detect intent** from the user message using the trigger keywords above
2. **Route** to the matching command
3. If ambiguous, ask the user which command they want

## Auto-trigger Examples

| User says | Routes to |
|-----------|-----------|
| "Audit this project for security issues" | `/audit security` |
| "Scan for dead code and unused deps" | `/audit cleanup` |
| "Refine my CLAUDE.md" | `/refine project` |
| "Clean up all config files" | `/refine all` |
| "Index this project" | `/index` |
| "Set up jCodeMunch" | `/index` |
| "Do a full code quality check" | `/code-quality` |
| "Clean up stale memory files" | `/memory-prune` |
| "Prune memory" | `/memory-prune` |
| "Remove old Claude memory entries" | `/memory-prune --auto` |

## Dependencies

- **jCodeMunch MCP** — required for `/index`, useful for `/audit` (symbol-aware scanning)
- Commands are defined in `commands/audit.md`, `commands/refine.md`, `commands/index.md`, `commands/memory-prune.md`, `commands/code-quality.md`
