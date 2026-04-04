# ccplugin-code-quality

Claude Code plugin that bundles code quality, auditing, and indexing functionality.

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

## Installation

```bash
claude plugin add SkyWalker2506/ccplugin-code-quality
```

Or clone and link locally:

```bash
git clone https://github.com/SkyWalker2506/ccplugin-code-quality.git
cd ccplugin-code-quality
claude plugin link .
```

## Structure

```
.claude-plugin/
  plugin.json          # Plugin manifest
commands/
  audit.md             # /audit command definition
  refine.md            # /refine command definition
  index.md             # /index command definition
skills/
  code-quality/
    SKILL.md           # Auto-trigger skill for routing
.mcp.json              # jCodeMunch MCP server config
```

## MCP Dependencies

- **jcodemunch** — Required for `/index`, enhances `/audit` with symbol-aware scanning. Installed via `uvx jcodemunch-mcp`.

## License

MIT
