---
name: token-budget-check
description: Pre-flight check a prompt, message, or instruction for token cost and waste before it is sent. Use when the user asks to check, estimate, pre-flight, size-up, or budget a prompt before sending. Returns an estimated token count, a waste breakdown, and a ranked list of cuts with a proposed lean rewrite.
---

# token-budget-check

Estimate cost. Flag waste. Propose the leanest version that keeps intent.

## Trigger on
- "check this prompt before I send"
- "how many tokens will this use"
- "pre-flight this"
- "can you make this cheaper"
- User pastes a prompt explicitly asking for budget / size analysis

## Scope boundary

| skill | scope |
|-------|-------|
| token-budget-check | prompts, messages, single instructions |
| token-audit | SKILL.md files (has frontmatter) |
| skill-compress | SKILL.md rewrites |
| lean-response | whole-session response behavior |

If input is a SKILL.md (has YAML frontmatter), redirect to `token-audit` — do not duplicate work.

## Process

1. Estimate tokens. English prose ≈ 4 chars/token, code ≈ 3 chars/token.
2. Classify input: one-shot instruction / multi-step request / reference-heavy prompt / chat continuation.
3. Find waste in three categories:

| category | flags |
|----------|-------|
| restatement | repeats context, re-explains what was just said |
| padding | "in order to", "at this point in time", "due to the fact that" |
| over-specification | lists edge cases the model handles by default |

4. Rank cuts by absolute savings.
5. Produce a lean rewrite.

## Output

```
tokens: {n}
classification: {shape}

waste:
  restatement: {n} tokens
  padding:     {n} tokens
  over-spec:   {n} tokens
  total:       {n} tokens ({pct}%)

top cuts:
1. "{phrase}" → "{short}"  (−{n})
2. ...

lean rewrite:
"""
{rewritten prompt}
"""

before: {n} / after: {n} / saved: {n} ({pct}%)
```

## Hard rules
- Never strip concrete constraints (numbers, names, file paths, formats). Strip only waste.
- If input < 100 tokens, skip analysis and say "already lean" — the audit itself would cost more than any savings.
- Preserve tone markers the user chose deliberately (formal register, specific voice).
- If the rewrite would change meaning, keep the original phrase and flag it instead.
- Return the lean rewrite in a code block so the user can copy it cleanly.
