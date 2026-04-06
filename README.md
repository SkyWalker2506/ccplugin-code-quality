# code-quality — Claude Code Plugin

by [Musab Kara](https://linkedin.com/in/musab-kara-85580612a) · [GitHub](https://github.com/SkyWalker2506)

Claude Code plugin that bundles code quality, auditing, config refinement, and indexing functionality.

## Install

```bash
claude plugin install code-quality@musabkara-claude-marketplace
```

## Features

### `/audit` — Code Audit
Scan your project for security vulnerabilities, cost risks, performance issues, and cleanup opportunities.

```
/audit                 # Full audit (all categories)
/audit security        # Hardcoded secrets, injection, insecure storage
/audit cost            # Paid service usage, large assets, unnecessary calls
/audit performance     # Widget rebuilds, memory leaks, cold start
/audit cleanup         # Dead code, unused deps, TODO/FIXME, .gitignore gaps
```

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

## MCP Dependencies

- **jcodemunch** — Required for `/index`, enhances `/audit` with symbol-aware scanning. Installed via `uvx jcodemunch-mcp`.

## License

MIT

## Part of

- [claude-config](https://github.com/SkyWalker2506/claude-config) — Multi-Agent OS for Claude Code (134 agents, local-first routing)
- [Plugin Marketplace](https://github.com/SkyWalker2506/claude-marketplace) — Browse & install all 18 plugins
- [ClaudeHQ](https://github.com/SkyWalker2506/ClaudeHQ) — Claude ecosystem HQ
