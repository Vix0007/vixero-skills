---
name: vixero-council
description: Convene a 6-lens council (correctness, adversary, maintainer, performance, integration, contrarian) with a judge that synthesizes a verdict. Use when the user says "convene council", "council review", "review from all angles", or needs multi-lens review before a high-stakes commit. Returns lens findings, surfaced disagreements, minority report, verdict with calibrated confidence.
---

# vixero-council

Six independent lenses review the input. Judge synthesizes. Disagreements preserved. Groupthink blocked by protocol.

## Trigger on
- "convene council" / "council review"
- "review from all angles" / "attack from every perspective"
- Before: architecture choice, public API, migration, refactor > 500 lines, production deploy

## Scope
- **review mode**: code / patch / design doc / proposal → six-lens critique
- **advisory mode**: decision question → six-lens recommendation
- For task-scale single-lens reviews use `surgical-diff`, `simplicity-audit`, `goal-criteria`

## Protocol (strict order)

1. Read input. Classify mode. Record subject.
2. For each lens in order, open `./personas/{lens}.md`, apply, emit findings. **No lens reads another's output before emitting.** Anti-contamination is mandatory.
3. Run judge synthesis AFTER all six emit.
4. Render canonical output block.

## The six lenses

| # | lens | question | emoji | file |
|---|------|----------|-------|------|
| 1 | correctness | does it do what it claims under all inputs? | 🎯 | `personas/correctness.md` |
| 2 | adversary | how does a motivated attacker break it? | 🗡️ | `personas/adversary.md` |
| 3 | maintainer | what costs time in 6 months? | 🔧 | `personas/maintainer.md` |
| 4 | performance | what does it cost at stated scale? | 🔥 | `personas/performance.md` |
| 5 | integration | does it fit the contracts it touches? | 🧩 | `personas/integration.md` |
| 6 | contrarian | what if the premise is wrong? | 🎭 | `personas/contrarian.md` |

## Severity tiers

| tier | meaning |
|------|---------|
| blocker | ship breaks production, security, or data |
| major | high debt or user harm; ship only with tracked follow-up |
| minor | nits, style, future refinement |

## Judge ⚖️
1. Consolidate findings; dedupe cross-lens hits; credit strongest framing.
2. Surface disagreements — do NOT flatten.
3. Decide: `SHIP | SHIP WITH FIXES | REWORK | REJECT`.
4. Confidence: 0–100, **rounded to 10s**. No 73%, no 87%.
5. Top-3 concrete actions before ship.
6. Minority report: strongest dissenting lens, named.

## Output

```
subject: {one line}
mode: {review | advisory}

--- LENS FINDINGS ---
correctness 🎯: blockers: {...} major: {...} minor: {...}
adversary 🗡️:   (same shape)
maintainer 🔧:  (same shape)
performance 🔥: (same shape or "skipped — scale not stated")
integration 🧩: (same shape)
contrarian 🎭:  position: {...} | objection: {...}

--- DISAGREEMENTS ---
- {lens A} X, {lens B} Y. Trade-off: {what}

--- VERDICT ⚖️ ---
decision: {SHIP | SHIP WITH FIXES | REWORK | REJECT}
confidence: {0-100, rounded to 10}
reasoning: {3-5 imperative lines}

top-3 actions:
1. ... 2. ... 3. ...

minority report: lens: {which} | position: {one line}
```

## Hard rules
- Each lens runs independently; no lens reads another's emission first.
- Emit "none" when nothing found — never invent findings.
- Judge cannot downgrade a blocker; only the raising lens can retract.
- If ≥ 3 lenses emit blockers → verdict is REJECT regardless.
- Confidence rounds to 10s; no false precision.
- Performance lens skips if scale absent — ask, don't invent targets.
- Contrarian never votes; it reframes. Judge may elevate it to minority report.
- Input < 30 lines or < 3 paragraphs → refuse: "too small, use a single lens".
- Advisory mode: lenses recommend; judge picks one path AND names second-best.

## Composes with
`assumption-surface` → advisory mode (interpret before review)
`goal-criteria` → defines correctness lens target
`surgical-diff` · `simplicity-audit` → pre-filters before council
`karpathy-coding` → applies if council recommends code changes
