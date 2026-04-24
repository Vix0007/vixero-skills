---
name: surgical-diff
description: Review a code diff for scope creep, drive-by changes, and style drift. Use when the user pastes a diff, asks "review this diff", "did I change too much", "is this change surgical", or wants to audit an AI-generated patch before merging. Returns flagged lines, scope analysis, a minimal-diff proposal, and a verdict.
---

# surgical-diff

Audit a diff against its stated intent. Every changed line must trace to the request.

## Trigger on
- "review this diff"
- "is this surgical"
- "did I change too much"
- "audit this patch"
- "scope-check this PR"
- User pastes a diff plus a statement of what the change was supposed to do

## Input shapes

| shape | handling |
|-------|----------|
| unified diff + stated intent | full analysis |
| diff only, no intent | ask for intent first, do not guess |
| file-before + file-after | treat as implicit diff |
| PR description + diff | extract intent from description |
| commit message + diff | extract intent from subject line |

## Analysis passes

### Pass 1 — scope trace
For each changed line: does this line solve the stated request? Mark yes / no / indirect.

### Pass 2 — drift detection

Universal drift flags:

| drift type | examples |
|------------|----------|
| style | quote style changes, whitespace, trailing commas |
| type theatre | type hints added to unrelated functions |
| doc theatre | docstrings added to unrelated functions |
| rename | variable / function renames not required by fix |
| scope creep | new features adjacent to the fix |
| unauthorized delete | pre-existing dead code removed |
| dependency creep | new import not needed by the fix |
| test theatre | unrelated tests added alongside the fix |

Language-specific drift:

| language | flag when you see |
|----------|-------------------|
| Python | f-string conversion of unrelated % or .format() lines; `list` → `List` type annotation backport on unrelated functions |
| TypeScript | `any` → specific type on unrelated signatures; `interface` → `type` or reverse |
| JavaScript | `var` → `const/let` on unrelated lines; `function` → arrow on unrelated |
| Go | error wrap reformat on unrelated errors; `interface{}` → `any` swap on unrelated |
| Rust | `unwrap()` → `?` conversion on unrelated call sites |
| Java | `var` local-type inference backport on unrelated locals |

### Pass 3 — orphan check

After the edit, are imports / variables / functions orphaned?

| orphan type | action |
|-------------|--------|
| Import your edit orphaned | remove |
| Variable your edit orphaned | remove |
| Function your edit orphaned | remove |
| Orphan that pre-existed | leave. flag only if user asks about it |

## Output

```
stated intent: {one line}
lines changed: {n}
lines tracing to intent: {n} ({pct}%)

drift flags:
  style: {line numbers}
  type-theatre: {line numbers}
  doc-theatre: {line numbers}
  rename: {line numbers}
  scope-creep: {line numbers + description}
  unauthorized-delete: {line numbers}
  dependency-creep: {line numbers}
  language-specific: {type + line numbers}
  orphans-created: {list — remove}
  orphans-untouched: {list — leave, informational}

minimal diff (only lines tracing to intent):
```diff
{the subset}
```

verdict: {surgical | minor drift | scope creep | rewrite needed}
reasoning: {one line}
```

## Verdict thresholds

| tracing % | verdict |
|-----------|---------|
| ≥ 95 | surgical |
| 80–94 | minor drift (acceptable with flags) |
| 50–79 | scope creep (split into multiple PRs) |
| < 50 | rewrite needed (start from stated intent again) |

## Hard rules
- Never recommend deleting pre-existing dead code unless user asked.
- Style drift is a flag, not an error. Flag it, let the user decide.
- If stated intent is missing, do not analyze. Ask for it first.
- If the diff is under 5 lines, skip and say "too small to drift".
- Type hints on NEW code do not flag. Type hints added to UNRELATED code do.
- Tests added that cover the stated fix do NOT flag. Tests unrelated to the fix do.
- Whitespace-only diffs (reformat commits) are flagged as their own category — suggest splitting into a separate PR.
- Never "fix" the diff yourself. Report and propose. The user decides.
