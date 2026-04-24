---
name: token-audit
description: Score a SKILL.md, prompt, or system message for token waste. Use when the user asks to audit, measure, benchmark, score, or evaluate token efficiency of a skill, prompt, or agent instruction. Returns a 0-100 score, six-axis breakdown, and ranked specific cuts with token-savings estimates.
---

# token-audit

Audit input. Return structured waste report. No rewrites — invoke `skill-compress` for that.

## Trigger on
- "audit this skill / prompt / SKILL.md"
- "is this efficient"
- "score my skill"
- User pastes a SKILL.md or system prompt with audit intent

## Process

1. Count tokens. Rule: English prose ≈ 4 chars/token, code ≈ 3 chars/token, YAML ≈ 3.5 chars/token.
2. Score six axes, 0–10 each, deductions below:

| axis | deduction |
|------|-----------|
| preamble-bloat | −3 per "I'll help" / "Certainly" / "Sure" / "Of course" / "Let me" |
| passive-voice | −1 per "is recommended" / "should be done" / "can be performed" |
| prose-over-table | −5 if any list / table / matrix would cut 30%+ tokens |
| redundant-examples | −2 per duplicate-purpose example |
| ceremonial | −1 per "please" / "kindly" / "feel free" / "if you'd like" |
| desc-quality | −10 if frontmatter description < 40 chars, > 300 chars, or missing *when* to use |

3. Total = sum clamped ≥ 0. Score = (total / 60) × 100.

## Output

```
tokens: {n}
score: {0-100}

breakdown:
  preamble-bloat:     {n}/10
  passive-voice:      {n}/10
  prose-over-table:   {n}/10
  redundant-examples: {n}/10
  ceremonial:         {n}/10
  desc-quality:       {n}/10

top cuts (ranked by savings):
1. "{before}" → "{after}"  (−{n} tokens)
2. ...

projected savings: {n} tokens ({pct}%)
verdict: {lean | cut 20-40% | rewrite from scratch}
```

## Hard rules
- No softening. "Wastes 40 tokens" — not "may waste some tokens."
- No rewrites here. Report only.
- Always include absolute token count AND percentage.
- If score < 40 say "rewrite from scratch" — do not recommend incremental fixes.
- Fit output under 300 tokens for inputs under 2000 tokens.
