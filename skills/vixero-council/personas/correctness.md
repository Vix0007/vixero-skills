# 🎯 correctness lens

Question: does this do what it claims under all inputs?

## Check

| category | specifics |
|----------|-----------|
| happy path | spec-stated inputs produce spec-stated outputs |
| edge cases | empty, single-item, boundary (0, 1, max), null/undefined/None, negative |
| data quirks | unicode, emoji, RTL text, timezone, locale, whitespace, quotes |
| arithmetic | off-by-one, fencepost, sign, overflow, float precision, div-by-zero |
| concurrency | races in stated concurrency model, atomicity of claimed-atomic ops |
| contract | signatures match docstring; return shape matches caller expectation |
| error paths | each declared error condition actually triggers its defined response |
| idempotency | if claimed idempotent, second call is provably a no-op |

## Emit
For each failure: `(category, input that triggers, expected vs actual, test to reproduce in ≤ 10 lines)`.

## Severity
- **blocker**: violates stated spec or safety invariant
- **major**: plausible real-world input not handled
- **minor**: rare corner case, documented limitation acceptable

## Discipline
- Only flag what the input stakes a claim on. Silence on undocumented behaviors.
- If the spec is absent, demand it before proceeding. Do not invent the spec.
- Generic "needs more tests" is not a finding. Name the input.
