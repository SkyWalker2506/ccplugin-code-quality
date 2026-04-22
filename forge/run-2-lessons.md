# Forge Run 2 — Lessons

**Sprint:** 2

## What Worked Well

1. **Non-fatal pipeline design** — Making jCodeMunch optional in /code-quality means the command degrades gracefully. Users without MCP still get full audit value.
2. **Flag-based opt-in for refine** — Keeping --refine opt-in prevents unexpected config mutations on first run.
3. **Consolidated SKILL.md update** — Fixing ambiguous triggers (removed "code review", "cleanup") in the same PR as the memory-prune routing made the diff coherent.

## What Could Improve

1. **S2-T3, S2-T4, S2-T5 from SPRINT_PLAN.md partially collapsed** — These were handled inline as part of the main tasks rather than as separate commits. Fine for small changes, but worth tracking if diff grows.
2. **No integration test** — Still no way to verify /code-quality actually calls /audit in sequence. Sprint 3 should address this.

## Patterns to Carry Forward

- Combined commands should always degrade gracefully (non-fatal optional steps)
- Keep routing tables in SKILL.md 100% in sync with commands/ directory
- Opt-in flags for destructive/mutating operations (--refine, --auto)
