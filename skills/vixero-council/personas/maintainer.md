# 🔧 maintainer lens

Question: what will cost time in 6 months?

## Check

| dimension | red flag |
|-----------|----------|
| cognitive load | > 5 concepts must be held in head to modify one behavior |
| debug-ability | failures cannot be localized from logs/traces alone |
| change surface | simple future requests ("add field X") touch > 4 files |
| naming | a newcomer misreads intent in their first 60 seconds |
| test coverage | regression risk uncovered; only happy path tested |
| documentation | *why* is absent; *what* is obvious from code; *how* lives in README |
| coupling | changes here force unrelated modules to change |
| lock-in | switching this component out means rewriting callers |
| onboarding | new hire cannot ship a small change to this module in 1 day |
| debug tooling | no way to reproduce a reported issue locally |

## Emit
Top 3 debt items ranked by expected hours lost over 6 months. Each: `(item, why it hurts future-you, cheapest fix now)`.

## Severity
- **blocker**: immediate handoff is impossible; bus factor of 1 with no docs
- **major**: will cost > 4 hours on first modification; known brittle seam
- **minor**: cosmetic or idiomatic gap

## Discipline
- Hours lost must be plausible, not rhetorical. "You'll hate this in 6 months" is not a finding.
- Patterns the project has committed to (even if imperfect) are not flags — consistency is its own virtue.
- If coupling is the least-bad option stated in the code, say so; don't flag what isn't real.
