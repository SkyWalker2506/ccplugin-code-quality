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
/audit security        → hardcoded secrets, injection, insecure storage
/audit cost            → paid service usage, large assets, unnecessary network calls
/audit performance     → rebuilds, memory leaks, cold start, large imports
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

## STEP 0 — LANGUAGE DETECTION

Before auditing, detect the project's primary language(s):

1. Check for these files in project root and subdirectories:
   - `pubspec.yaml` → Flutter/Dart
   - `package.json` → JavaScript/TypeScript (Node, React, Vue, etc.)
   - `requirements.txt` or `pyproject.toml` or `setup.py` → Python
   - `go.mod` → Go
   - `Cargo.toml` → Rust
   - `pom.xml` or `build.gradle` → Java/Kotlin
   - `*.csproj` → C#/.NET

2. Also check file extensions in `src/`, `lib/`, root:
   - `.dart` → Flutter/Dart
   - `.ts`, `.tsx`, `.js`, `.jsx` → JavaScript/TypeScript
   - `.py` → Python
   - `.go` → Go

3. Report detected stack at top of output:
   ```
   Detected stack: [Flutter | JS/TS | Python | Go | Unknown]
   ```

4. Only run checklists relevant to the detected stack. If stack is Unknown,
   run SECURITY and CLEANUP sections only (language-agnostic).

## RULES

- Read-only — do NOT edit or create files
- Max 30 tool calls — be efficient
- Use Grep/Glob for pattern search, Read to confirm findings
- No false positives — only report issues you are certain about
- Skip checklist items that don't apply to the detected stack

---

## CHECKLIST

### SECURITY (focus: security or all) — ALL STACKS

1. **Hardcoded secrets:** Grep for `apiKey`, `api_key`, `secret`, `password`, `token`, `credential`,
   `private_key`, `ACCESS_KEY`, `SECRET_KEY` in source files
   - Is `.env` in `.gitignore`? Is `.env` committed?
   - Are any key values (not variable names) actually hardcoded?

2. **Network:** HTTP (not HTTPS) endpoints in source? User input used directly in URLs?

3. **Dependency vulnerabilities:** Outdated lock files? Known-vulnerable version pins?

---

### SECURITY — FLUTTER/DART ONLY

4. **Firebase security:** `firebase_options.dart` exposure, Firestore/RTDB rules defined?
5. **Insecure storage:** `SharedPreferences` storing tokens/passwords? `flutter_secure_storage` in use?
6. **Platform:** `android:debuggable`, `allowBackup="true"`, `usesCleartextTraffic`, `NSAllowsArbitraryLoads`
7. **SSL pinning:** Any SSL pinning implemented?

---

### SECURITY — JS/TS ONLY

4. **`.env` exposure:** `.env*` files in `.gitignore`? `dotenv` in use?
5. **eval/injection:** `eval()`, `Function()`, `innerHTML` with user data?
6. **npm audit:** Any `audit-level: critical` in `.npmrc`?
7. **CORS:** `*` CORS policy in server code?
8. **XSS/CSRF:** User input rendered without sanitization?

---

### SECURITY — PYTHON ONLY

4. **Pickle/exec:** `pickle.loads`, `exec()`, `eval()` with user input?
5. **SQL injection:** Raw f-string SQL queries?
6. **Flask/Django:** `DEBUG=True` in production? `SECRET_KEY` hardcoded?
7. **Dependencies:** `pip install` without pinned versions?

---

### SECURITY — GO ONLY

4. **SQL injection:** `fmt.Sprintf` in SQL queries?
5. **Goroutine leaks:** Goroutines started without cancel/timeout?
6. **Error handling:** Errors silently ignored with `_`?

---

### COST (focus: cost or all)

#### ALL STACKS
1. **Unnecessary network calls:** Polling without backoff, infinite retry loops?
2. **Asset size:** Total `assets/` or `public/` or `static/` size, files >500KB, unused assets

#### FLUTTER ONLY
3. **Firebase services:** Firebase services in use, free-tier risk, RevenueCat/IAP config
4. **Dependencies:** Paid packages in pubspec.yaml, oversized deps

#### JS/TS ONLY
3. **Bundle size:** Large npm packages (moment.js, lodash full import)?
4. **API calls:** Client-side keys for paid APIs (OpenAI, Stripe, etc.)?

#### PYTHON ONLY
3. **Paid APIs:** Hardcoded paid API calls (OpenAI, AWS, GCP) without rate limiting?

---

### PERFORMANCE (focus: performance or all)

#### FLUTTER ONLY
1. **Widget rebuild:** `setState` count, missing `const`, Provider/Riverpod misuse
2. **Memory:** Undisposed controllers, open Hive boxes, `ListView` without `.builder`
3. **Startup:** `main.dart` init order, lazy loading
4. **Build size:** Unused imports, tree-shaking blockers

#### JS/TS ONLY
1. **Bundle:** Dynamic imports used? Large synchronous imports at top level?
2. **Memory:** Event listeners not removed on unmount? Closures holding large data?
3. **React-specific:** Missing `key` props, excessive re-renders, missing `useCallback`/`useMemo`?
4. **Async:** Unhandled promise rejections? Blocking `await` in loops?

#### PYTHON ONLY
1. **I/O:** Synchronous file I/O in async context? Blocking DB calls?
2. **Memory:** Large objects held in global scope? Generator vs list?
3. **N+1 queries:** ORM queries inside loops?

#### GO ONLY
1. **Goroutine leaks:** Goroutines without cancellation context?
2. **Allocation:** Excessive heap allocation in hot paths?
3. **Mutex contention:** Lock held across I/O operations?

---

### CLEANUP (focus: cleanup or all) — ALL STACKS

1. **Unnecessary files:** Missing `.gitignore` entries, committed build artifacts
2. **Dead code:** Unimported files, unused public symbols, commented-out blocks (>5 lines)
3. **Code hygiene:** TODO/FIXME/HACK, leftover debug print/console.log/fmt.Println statements
4. **Dependency hygiene:** Unused packages, lock file consistency

#### FLUTTER ONLY
5. **pubspec:** Unused packages, pubspec lock consistency

#### JS/TS ONLY
5. **node_modules in git:** `node_modules/` committed?
6. **package.json:** Scripts referencing non-existent files?

#### PYTHON ONLY
5. **venv in git:** `venv/` or `.venv/` committed?
6. **requirements:** `requirements.txt` vs actual imports mismatch?

---

## REPORT FORMAT

Start with:
```
Detected stack: [stack name]
Audit focus: [focus]
```

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
- Skip checklist sections for stacks not detected in the project
