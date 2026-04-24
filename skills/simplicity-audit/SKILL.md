---
name: simplicity-audit
description: Review code for premature abstraction, speculative features, and over-engineering. Use when the user asks "is this overcomplicated", "simplify this", "does this need all these layers", "review for over-engineering", "flatten this", or pastes a file asking for a complexity review. Returns a complexity ratio, flagged patterns, and a flattened rewrite for the worst offender.
---

# simplicity-audit

Flag complexity not earned by a stated requirement.

## Trigger on
- "is this overcomplicated"
- "simplify this"
- "does this need X"
- "review for over-engineering"
- "flatten this"
- "too many layers"

## Universal patterns to flag

| pattern | flag when |
|---------|-----------|
| single-use abstraction | ABC / Protocol / Strategy used in exactly one place |
| speculative parameters | flags / options never called from anywhere |
| defensive error handling | try/except for conditions that cannot occur |
| premature generalization | generic machinery serving one concrete case |
| config theatre | settings file holding values that never change |
| wrapper theatre | wrapper whose only method calls the underlying |
| dataclass theatre | class with 2 fields, no methods, used once |
| builder theatre | builder pattern when a constructor has 3 args |
| factory theatre | factory returning one concrete type 100% of the time |
| manager theatre | XManager / XHandler wrapping a function that could be the function |
| enum-for-two | Enum with exactly two values used as bool |
| indirection for mockability | interface existing only to let tests mock |
| premature async | async/await on code that never actually awaits anything |
| generic-for-one | generic type parameter only ever instantiated with one concrete type |

## Language-specific patterns

| language | pattern |
|----------|---------|
| Python | Union[X, X], bare Any on parameters, unused TypeVar, `__init__` that only assigns, ABC with one implementation |
| TypeScript | `any` spreading through signatures, `interface I { x: T }` + `class C implements I` (used once), enum when literal union works |
| Java | getter/setter on every field with no logic, factory class returning one concrete type, XxxService + XxxServiceImpl pair with no second impl |
| Go | interface satisfied by one struct, empty struct wrapping one field, constructor returning `&T{}` with no logic |
| Rust | trait with one impl, wrapping `Arc<Mutex<T>>` when `Arc<T>` works, Builder for 2-field structs |

## Process

1. Scan file for each pattern.
2. For each hit, estimate lines saved by flattening.
3. Compute complexity ratio: `(lines serving stated purpose) / (total lines)`.
4. Pick the worst offender by savings. Write its minimal equivalent.
5. Rank all flags by absolute line savings.

## Output

```
file: {name}
language: {lang}
total lines: {n}
lines serving stated purpose: {n}
complexity ratio: {pct}

flagged patterns (ranked by savings):
1. {pattern} at line {n}: {description} — save ~{n} lines
2. {pattern} at line {n}: {description} — save ~{n} lines
3. ...

worst offender — minimal rewrite:
```{lang}
{the flattened version}
```

verdict: {lean | minor overbuild | significant overbuild | rewrite from scratch}
reasoning: {one line}
```

## Verdict thresholds

| complexity ratio | verdict |
|------------------|---------|
| ≥ 70% | lean (may still have minor flags) |
| 50–69% | minor overbuild (apply top 2 flags) |
| 30–49% | significant overbuild (apply all flags) |
| < 30% | rewrite from scratch (bones are wrong) |

## Hard rules
- Complexity is suspect, not forbidden. If user states a requirement needing it, accept.
- Tests and fixtures never flag — test scaffolding is not production complexity.
- Type hints do not flag unless they are theatre (Union[X,X], unused TypeVar, bare Any on parameters).
- Never simplify code whose purpose you cannot read. Ask first.
- If complexity ratio > 70%, return "lean" and stop. Do not invent flags to fill output.
- If the file is a library / framework / SDK, apply looser thresholds — generalization there may be earned.
- Abstractions explicitly marked as extension points (`# extension point` comments, plugin interfaces) do not flag.
- If the file is under 30 lines, skip and say "too small to overbuild".
- When proposing a rewrite, preserve public signatures. The caller's code must not change.
