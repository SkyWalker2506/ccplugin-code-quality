# ccplugin-code-quality — Master Analysis

**Date:** 2026-04-22  
**Analyst:** Jarvis (Sonnet 4.6)  
**Repo:** SkyWalker2506/ccplugin-code-quality

---

## 1. Project Overview

`ccplugin-code-quality` is a Claude Code plugin that provides three core slash commands for code quality management:

| Command | Purpose |
|---------|---------|
| `/audit` | Security, cost, performance, cleanup scanning |
| `/refine` | CLAUDE.md / settings.json / hooks refinement |
| `/index` | jCodeMunch project indexing |

A `code-quality` skill auto-routes user intent to the correct command.

---

## 2. Current State

### File Structure

```
.claude-plugin/plugin.json      — Plugin manifest (name, version, keywords)
commands/
  audit.md                      — /audit command (security/cost/perf/cleanup)
  index.md                      — /index command (jCodeMunch)
  memory-prune.md               — /memory-prune command (added later)
  refine.md                     — /refine command (config refinement)
skills/
  code-quality/SKILL.md         — Auto-trigger routing skill
README.md                       — User-facing docs
CLAUDE.md                       — Redirector to claude-config
.mcp.json                       — MCP configuration
```

### Git History Highlights

- Initial plugin: audit, refine, index (9524246)
- Added LICENSE and author info
- Added `/memory-prune` command (2e768d9)
- Removed CDS design.json from plugin scope (803d3cd)

---

## 3. Strengths

1. **Clear command separation** — Each command has a dedicated `.md` file with well-structured prompts
2. **Auto-routing skill** — `code-quality/SKILL.md` cleanly routes user intent with trigger keywords
3. **Read-only audit** — `/audit` explicitly read-only with good checklist structure
4. **Structured report format** — RED/ORANGE/YELLOW/BLUE severity levels consistent

---

## 4. Gaps & Issues

### 4.1 Missing Commands in Skill Routing

`SKILL.md` routing table does NOT include `/memory-prune` even though the command exists. Users saying "clean up memory files" get no route.

### 4.2 `/audit` is Flutter-Specific

The audit checklist hardcodes Flutter/Dart concepts:
- `flutter_secure_storage`, `SharedPreferences`
- `firebase_options.dart`, `.plist`
- `widget rebuild`, `setState`, `const`
- `pubspec.yaml`, `pubspec lock`

This makes `/audit` useless for non-Flutter projects (JS, Python, Go, etc.).

### 4.3 No `code-quality` Command Self-Audit

The plugin has no `/code-quality` command that provides an overall health check combining audit + index + refine in a single workflow.

### 4.4 README Missing `/memory-prune`

README.md does not document `/memory-prune` despite it being a full command with its own `.md` file.

### 4.5 `plugin.json` Missing `commands` Field

`plugin.json` has no `commands` declaration. Claude Code plugin spec expects a `commands` array listing available commands.

### 4.6 SKILL.md Triggers Too Broad

Trigger `"code review"` is ambiguous and conflicts with general PR review intent. `"cleanup"` alone is too broad.

### 4.7 `/refine` Missing Validation Step

`/refine` says "Validate JSON after edits" but provides no concrete validation command or fallback.

### 4.8 No Tests

Zero test coverage. No way to verify commands behave as documented after changes.

### 4.9 No `memory-prune` Skill Routing

The `code-quality` skill does not have memory-prune in its routing table despite the command existing.

---

## 5. Opportunities

1. **Language-agnostic audit** — Add JS/TS, Python, Go checklists to `/audit`
2. **`/memory-prune` in SKILL.md** — Add routing + triggers for memory cleanup
3. **Self-describing plugin.json** — Add `commands` array to plugin manifest
4. **Combined `/code-quality` command** — One command that runs audit → index → refine
5. **README completeness** — Document all 4 commands, add MCP setup instructions

---

## 6. Risk Assessment

| Area | Risk | Severity |
|------|------|----------|
| Flutter-only audit | Users on other stacks get empty/wrong results | HIGH |
| Missing memory-prune routing | Feature is invisible to skill auto-trigger | MEDIUM |
| plugin.json missing commands | Marketplace listing incomplete | MEDIUM |
| No tests | Regressions undetected | MEDIUM |
| Broad triggers | Wrong command invoked | LOW |

---

## 7. Recommended Sprint Focus

**Sprint 1 — Core fixes:** Language-agnostic audit, README update, plugin.json commands field  
**Sprint 2 — Feature parity:** memory-prune in skill routing, combined code-quality command  
**Sprint 3 — Quality:** Trigger refinement, validation improvements, test stubs

---

## 8. Metrics

| Metric | Value |
|--------|-------|
| Commands | 4 (audit, refine, index, memory-prune) |
| Skills | 1 (code-quality) |
| Routing gaps | 1 (memory-prune not in skill) |
| Flutter-specific checklist items | ~20 |
| README documented commands | 3 of 4 |
| Test files | 0 |
| plugin.json completeness | ~70% |
