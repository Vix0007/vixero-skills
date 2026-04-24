---
name: skill-graph
description: Map how multiple SKILL.md files interact, conflict, or compose. Use when the user pastes a list of skills, a skills directory structure, or asks to audit a skill collection for redundancy, trigger-keyword overlap, or composition gaps. Returns a conflict matrix, redundancy flags, and recommended merges or splits.
---

# skill-graph

Analyze a skills collection as a system. Find overlaps, gaps, and composition patterns.

## Trigger on
- "audit my skills folder / collection"
- "do these skills conflict"
- "which skills overlap"
- User pastes two or more SKILL.md frontmatters together
- "how do my skills work together"

## Input shapes accepted

1. Multiple pasted SKILL.md files
2. A list of `name: description` pairs
3. A directory listing (names only) — treat as partial analysis, warn about missing descriptions

## Analysis passes

### Pass 1 — trigger keyword extraction
For each skill, extract the trigger verbs and synonym nouns from its description.

### Pass 2 — overlap matrix
Build a matrix. Two skills overlap when ≥ 2 trigger keywords match AND the stated output is similar. Score 0–3:

| score | meaning |
|-------|---------|
| 0 | disjoint |
| 1 | minor keyword overlap, different output |
| 2 | significant overlap — user intent ambiguous |
| 3 | redundant — merge or delete one |

### Pass 3 — composition chains
Flag skills that naturally compose. If skill A's output is skill B's input, note it.
Example: `token-audit` → `skill-compress` → `token-budget-check`.

### Pass 4 — coverage gaps
Identify intent categories the collection does not cover. Compare trigger verbs against common agentic task verbs (draft, review, audit, compose, generate, plan, debug, refactor, summarize, translate, explain, compare, rank).

## Output

```
skills: {n}
total trigger keywords: {n}
unique trigger keywords: {n} ({ratio}% unique — higher is healthier)

overlap matrix (only scores ≥ 2 shown):
  {skill-a} × {skill-b}: {score}  — {reason}
  ...

composition chains:
  {a} → {b} → {c}
  {d} → {e}

redundancy flags:
  MERGE: {a} + {b}  (overlap 3)
  SPLIT: {x}  (does >1 job)

coverage gaps:
  {missing-intent-1}
  {missing-intent-2}
```

## Hard rules
- Do not rewrite skills. That is `skill-compress`'s job.
- When trigger keywords conflict, recommend specificity in descriptions, not deletion of skills.
- If the collection has < 3 skills, return a lightweight report and suggest growing the collection before graphing.
- If two skills score 3 (redundant), pick the leaner one by token count to keep. Name both in the output.
