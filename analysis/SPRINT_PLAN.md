# ccplugin-code-quality — Sprint Plan

**Date:** 2026-04-22  
**Forge Runs:** 3  
**Based on:** MASTER_ANALYSIS.md

---

## Sprint 1 — Core Fixes

**Goal:** Fix critical gaps: language-agnostic audit, README completeness, plugin.json commands field.

| # | Task | File(s) | Priority |
|---|------|---------|----------|
| S1-T1 | Make `/audit` language-agnostic (JS/TS, Python, Go sections) | `commands/audit.md` | HIGH |
| S1-T2 | Add `/memory-prune` to README with usage examples | `README.md` | HIGH |
| S1-T3 | Add `commands` array to `plugin.json` | `.claude-plugin/plugin.json` | MEDIUM |
| S1-T4 | Add JS/TS specific checklist items to audit (npm, package.json, env leaks) | `commands/audit.md` | MEDIUM |
| S1-T5 | Fix audit report format — add language-detection step | `commands/audit.md` | MEDIUM |

---

## Sprint 2 — Feature Parity

**Goal:** Add memory-prune routing to skill, introduce combined `/code-quality` command.

| # | Task | File(s) | Priority |
|---|------|---------|----------|
| S2-T1 | Add `/memory-prune` routing + triggers to `SKILL.md` | `skills/code-quality/SKILL.md` | HIGH |
| S2-T2 | Create `/code-quality` combined command (audit → index → refine) | `commands/code-quality.md` | HIGH |
| S2-T3 | Add `code-quality` command to `plugin.json` commands array | `.claude-plugin/plugin.json` | MEDIUM |
| S2-T4 | Add `code-quality` trigger routing to SKILL.md | `skills/code-quality/SKILL.md` | MEDIUM |
| S2-T5 | Update README with combined workflow docs | `README.md` | LOW |

---

## Sprint 3 — Quality & Polish

**Goal:** Refine triggers, improve validation, add test stubs, tighten skill routing.

| # | Task | File(s) | Priority |
|---|------|---------|----------|
| S3-T1 | Refine SKILL.md triggers — remove ambiguous "code review", "cleanup" | `skills/code-quality/SKILL.md` | MEDIUM |
| S3-T2 | Add JSON validation command to `/refine` | `commands/refine.md` | MEDIUM |
| S3-T3 | Add Python-specific audit checklist (requirements.txt, venv, secrets) | `commands/audit.md` | MEDIUM |
| S3-T4 | Add smoke test stub files for each command | `tests/` | LOW |
| S3-T5 | Add `.gitignore` entries for `.jira-state/` | `.gitignore` | LOW |

---

## Branch Strategy

- Sprint 1: `feat/s1-language-agnostic-audit`
- Sprint 2: `feat/s2-memory-prune-routing`
- Sprint 3: `feat/s3-quality-polish`

All PRs target `main`. No force push.
