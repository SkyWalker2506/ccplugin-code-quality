---
name: code-quality
description: "Auto-trigger skill for code quality auditing, config refinement, and jCodeMunch indexing."
triggers:
  - "audit"
  - "code audit"
  - "security audit"
  - "cost audit"
  - "performance audit"
  - "cleanup"
  - "dead code"
  - "refine"
  - "refine config"
  - "refine claude"
  - "index"
  - "jcodemunch"
  - "index project"
  - "code quality"
  - "code review"
  - "scan for issues"
  - "find vulnerabilities"
  - "unused imports"
  - "unused dependencies"
---

# Code Quality Skill

This skill activates when the user mentions code auditing, config refinement, or project indexing. It routes to the appropriate command.

## Routing

| User Intent | Command |
|-------------|---------|
| Audit code, scan for issues, security check, find vulnerabilities, dead code, cleanup | `/audit` |
| Refine CLAUDE.md, clean up config, tighten settings, fix duplication | `/refine` |
| Index project, jCodeMunch, symbol search setup | `/index` |

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
| "Do a full code quality check" | `/audit all` |

## Dependencies

- **jCodeMunch MCP** — required for `/index`, useful for `/audit` (symbol-aware scanning)
- Commands are defined in `commands/audit.md`, `commands/refine.md`, `commands/index.md`
