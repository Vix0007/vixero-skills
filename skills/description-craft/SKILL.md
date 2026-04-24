---
name: description-craft
description: Diagnose and rewrite a SKILL.md frontmatter description so Claude reliably triggers the skill at the right moment. Use when a skill fails to trigger, triggers on the wrong tasks, or when writing a new skill and asking "is this description good". Returns a diagnosis, a rewritten description, and why the rewrite fires better.
---

# description-craft

Fix the load-gate. The description is the only part of a skill Claude sees every turn — bad description means the skill never fires.

## Trigger on
- "my skill doesn't trigger"
- "is this description good"
- "help me write the description / frontmatter"
- "write a SKILL.md description for X"

## What a description must contain

1. **What it does.** Verb + object. "Rewrite verbose skills into lean form."
2. **When to use it.** Explicit trigger signals. "Use when the user asks to compress, shrink, tighten..."
3. **Trigger keywords.** Synonyms the user might actually type — not just the formal name. A skill called `threat-model` should mention STRIDE, PASTA, threat modeling, risk assessment.
4. **Output shape.** One phrase on what the user gets back. "Returns a ranked list of threats with mitigations."

## Diagnosis checklist

For the given description, mark each:

| check | pass / fail |
|-------|-------------|
| length 100–300 chars | |
| starts with a verb | |
| contains "Use when" or equivalent | |
| lists 3+ synonym triggers | |
| names the output / return value | |
| no meta-fluff ("This skill will...") | |
| no hedging ("might help with...") | |

Three or more fails → rewrite from scratch. One or two fails → patch.

## Rewrite pattern

```
{verb} {object}. Use when the user asks to {synonym1}, {synonym2}, {synonym3}, or {synonym4}. Returns {output-shape}.
```

## Output

```
diagnosis:
  length: {n} chars ({pass/fail})
  starts with verb: {pass/fail}
  has "Use when": {pass/fail}
  synonym count: {n} ({pass/fail})
  names output: {pass/fail}
  fluff: {pass/fail}

rewritten description:
{new description}

why it fires better:
- {reason 1}
- {reason 2}
```

## Hard rules
- Never write descriptions longer than 300 chars. Anthropic docs recommend specific and short.
- Never start with "This skill" — waste.
- Never use "helps with" — vague trigger, weak fire.
- If the skill does more than one thing, recommend splitting it. One skill, one job.
- The same skill name must appear nowhere in the description body — that is the `name` field's job.
