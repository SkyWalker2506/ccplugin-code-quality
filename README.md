# code-quality — Claude Code Plugin

by [Musab Kara](https://linkedin.com/in/musab-kara-85580612a) · [GitHub](https://github.com/SkyWalker2506)

Claude Code plugin that bundles code quality, auditing, config refinement, memory cleanup, and indexing functionality.

## Install

```bash
claude plugin install code-quality@musabkara-claude-marketplace
```

## Features

### `/audit` — Code Audit
Scan your project for security vulnerabilities, cost risks, performance issues, and cleanup opportunities. Automatically detects your project's language stack (Flutter, JS/TS, Python, Go) and runs only relevant checks.

```
/audit                 # Full audit (all categories)
/audit security        # Hardcoded secrets, injection, insecure storage
/audit cost            # Paid service usage, large assets, unnecessary calls
/audit performance     # Rebuilds, memory leaks, cold start
/audit cleanup         # Dead code, unused deps, TODO/FIXME, .gitignore gaps
```

**Supported stacks:** Flutter/Dart, JavaScript/TypeScript, Python, Go (language-agnostic security/cleanup for all others)

### `/refine` — Config Refiner
Analyze and tighten Claude Code configuration files without changing behavior.

```
/refine                # Refine current project config
/refine global         # Refine global ~/.claude/ config
/refine all            # Refine everything
```

### `/index` — jCodeMunch Indexing
Index your project with jCodeMunch for symbol-aware code navigation.

```
/index                 # Index (create or update)
/index force           # Rebuild index from scratch
```

### `/memory-prune` — Memory Cleanup
Scan Claude memory files under `~/.claude/projects/` and clean up stale, duplicate, or orphaned entries.

```
/memory-prune             # Interactive: list findings, ask per item
/memory-prune --dry-run   # List only, no deletions
/memory-prune --auto      # Delete all stale automatically (no prompts)
```

**Staleness criteria:**
- Outdated references (superseded libraries, old API versions)
- Duplicate content (same topic covered in a newer file)
- Orphaned project (project directory no longer exists)
- Empty/trivial content (<3 meaningful lines)
- Superseded decisions (newer feedback explicitly reverses it)

### `/code-quality` — Combined Workflow
Run the full quality pipeline in one command: index with jCodeMunch, audit all categories, and optionally refine config.

```
/code-quality              # Index + full audit
/code-quality --refine     # Index + audit + refine project config
/code-quality --no-index   # Audit only (skip indexing)
```

## MCP Dependencies

- **jcodemunch** — Required for `/index`, enhances `/audit` with symbol-aware scanning. Installed via `uvx jcodemunch-mcp`.

## License

MIT

## Part of

- [claude-config](https://github.com/SkyWalker2506/claude-config) — Multi-Agent OS for Claude Code (134 agents, local-first routing)
- [Plugin Marketplace](https://github.com/SkyWalker2506/claude-marketplace) — Browse & install all 18 plugins
- [ClaudeHQ](https://github.com/SkyWalker2506/ClaudeHQ) — Claude ecosystem HQ
