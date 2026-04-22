# Forge Run 1 — Lessons

**Sprint:** 1

## What Worked Well

1. **Parallel issue creation** — All 8 GitHub issues across 3 sprints created in one batch. Clean labels, clear acceptance criteria.
2. **Language detection via file markers** — Using config files (pubspec.yaml, package.json, go.mod) as language signals is simple and reliable without requiring shell commands.
3. **Graceful fallback** — When stack is unknown, running only security+cleanup checks is a safe default that still provides value.

## What Could Improve

1. **plugin.json schema** — Claude Code's actual plugin.json spec should be verified against docs before assuming field names. Used `name`/`description`/`argument-hint` based on existing commands/*.md frontmatter — may need adjustment if spec differs.
2. **Test coverage** — Still zero tests. Should add smoke test stubs in Sprint 3.
3. **Max tool calls in audit** — Bumped from 25 to 30 to account for expanded checklist. May need further tuning for very large projects.

## Patterns to Carry Forward

- Gate language-specific content behind detected stack — always
- Use STEP 0 pattern (detection before action) for any command that needs context
- Keep plugin.json version bumped on every meaningful change
