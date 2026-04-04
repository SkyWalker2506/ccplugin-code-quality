---
name: audit
description: "Code audit — security, cost, performance, cleanup. Scans project and reports findings."
argument-hint: "[security|cost|performance|cleanup|all]"
---

# /audit — Code Quality Audit

Scan the current project for issues and produce a structured report. This is a **read-only** operation — no files are modified.

## Usage

```
/audit                 → full audit (all categories)
/audit security        → hardcoded secrets, injection, insecure storage, Firebase rules
/audit cost            → paid service usage, large assets, unnecessary network calls
/audit performance     → widget rebuilds, memory leaks, cold start, large imports
/audit cleanup         → dead code, unused imports/deps, TODO/FIXME, .gitignore gaps
```

## Arguments

```
$ARGUMENTS
```

If a focus keyword is present use it; otherwise default to `all`.

## Execution

Run via background agent:

```python
Agent(
  prompt=<audit prompt below>,
  model="sonnet",
  run_in_background=True,
  description="project audit: <focus>"
)
```

### Agent Prompt

```
You are a code security and quality expert. Scan the current project.

Project root: current working directory
FOCUS: [user-provided focus, or "all"]

## RULES

- Read-only — do NOT edit or create files
- Max 25 tool calls — be efficient
- Use Grep/Glob for pattern search, Read to confirm findings
- No false positives — only report issues you are certain about

## CHECKLIST

### SECURITY (focus: security or all)

1. **Hardcoded secrets:** Grep for `apiKey`, `api_key`, `secret`, `password`, `token`, `credential` in source files
   - Is `.env` in `.gitignore`? Is `.env` committed?
   - Prod keys in `google-services.json` or `GoogleService-Info.plist`?
2. **Firebase security:** `firebase_options.dart` exposure, Firestore/RTDB rules defined?
3. **Insecure storage:** `SharedPreferences` storing tokens/passwords? `flutter_secure_storage` in use?
4. **Network:** HTTP (not HTTPS) endpoints? SSL pinning? User input in URLs?
5. **Platform:** `android:debuggable`, `allowBackup="true"`, `usesCleartextTraffic`, `NSAllowsArbitraryLoads`

### COST (focus: cost or all)

1. **Paid services:** Firebase services in use, free-tier risk, RevenueCat/IAP config, unnecessary Firebase calls
2. **Asset size:** Total `assets/` size, files >500KB, unused assets
3. **Dependencies:** Paid packages in pubspec.yaml, oversized deps

### PERFORMANCE (focus: performance or all)

1. **Widget rebuild:** `setState` count, missing `const`, Provider/Riverpod misuse
2. **Memory:** Undisposed controllers, open Hive boxes, `ListView` without `.builder`
3. **Startup:** `main.dart` init order, lazy loading
4. **Build size:** Unused imports, tree-shaking blockers

### CLEANUP (focus: cleanup or all)

1. **Unnecessary files:** Missing `.gitignore` entries, committed build artifacts
2. **Dead code:** Unimported Dart files, unused public symbols, commented-out blocks (>5 lines)
3. **Dependency hygiene:** Unused packages, pubspec lock consistency
4. **Code hygiene:** TODO/FIXME/HACK, empty tests, leftover `print()` statements

## REPORT FORMAT

Per finding:

### [LEVEL] Title
- **File:** path:line
- **Issue:** What is wrong
- **Risk:** What could happen
- **Fix:** What to do

Levels:
- RED CRITICAL — fix immediately (secret leak, security hole)
- ORANGE HIGH — fix soon (cost risk, performance issue)
- YELLOW MEDIUM — plan to fix
- BLUE INFO — improvement suggestion

End with summary table:

| # | Level | Category | File | Title |
|---|-------|----------|------|-------|

Overall score (1-10): 10 = clean, 1 = urgent intervention.
```

## Output

Show the report to the user when the agent completes. Highlight critical findings.

## Constraints

- Read-only — no file edits
- No Jira interaction — this is a code scan
- Reference every finding with file:line
- No speculative warnings — only confirmed issues
