# The vixero-skills philosophy

## Every token earns its place.

Agent Skills live in Claude's context. Every skill your agent loads competes for attention, for accuracy, for money. A skill that spends 2,000 tokens to do the work of 300 is not a skill — it is a tax on every conversation that touches it.

This document is the spec every skill in this repo meets. PRs that violate it get sent back.

---

## 1. The description is the load-gate

Claude scans skill frontmatter every turn. The full SKILL.md body does not load unless the description triggers. A bad description means the skill never fires — all the work inside is dead.

**Good descriptions:**
- Start with a verb.
- Say *what* the skill does AND *when* to use it.
- Include synonym trigger keywords the user might actually type.
- Name the output shape.
- Fit in 100–300 characters.

**Bad descriptions:**
- "This skill helps with code review."  (vague, no triggers)
- "A comprehensive toolkit for..."  (no triggers, meta-fluff)
- Anything over 300 characters.
- Anything starting with "This skill".

## 2. Top-level is the budget

The top-level SKILL.md should be ≤ 500 tokens. Heavy reference material goes into subfolders loaded on demand by Claude when it reads the skill.

Hierarchical loading means a skill's *top* is cheap to hold in context, and its *depth* is paid for only when the task actually needs it.

## 3. Imperative voice only

| wrong | right |
|-------|-------|
| "You should consider running tests before committing." | "Run tests before committing." |
| "It is recommended that the input be validated." | "Validate input." |
| "Please make sure to check for edge cases." | "Check edge cases." |

Imperative is shorter. Imperative is clearer. Imperative removes the illusion of choice where there isn't any.

## 4. Tables beat prose for parallel information

Any time you have three or more parallel items — axes, categories, options, rules — a table is denser than prose. A paragraph listing six evaluation axes is 80 tokens. A table is 40. Same information.

## 5. One skill, one job

A skill called `dev-helper` with sections for linting, testing, debugging, and formatting will fire on every software task. That means it loads when the user actually wanted a specific, narrow skill. The broad skill starves the precise one.

If your skill does more than one job, split it. `lint-fix`, `test-run`, `debug-trace`, `format-apply` — four skills that fire on four distinct intents.

## 6. Hard rules are non-negotiable

A skill's "hard rules" section is the override. Everything else is negotiable. Hard rules cover:
- Output format contracts
- Safety guardrails
- Inviolable constraints (don't drop numbers, don't rewrite code, etc.)

Keep hard rules short. If there are twelve of them, most aren't hard.

## 7. No emotional padding

Delete every instance of:
- "Please"
- "Kindly"
- "Feel free to"
- "If you'd like"
- "Hope this helps"
- "Let me know if you need anything else"

None of these words carry information. All of them cost tokens.

This is not about being cold. Warmth in agents comes from:
- Answering the actual question
- Not wasting the user's time
- Giving correct information

Not from ceremonial phrases.

## 8. No meta-narration

Do not write:
- "Let me analyze this for you..."
- "I'll walk through each step..."
- "Before I begin, I want to note..."

Just do the thing. The user can see you did it.

## 9. Examples by reference

One good example in the SKILL.md. Extras go in `examples/` inside the skill folder. Claude loads them only when the task calls for them.

Five examples of the same pattern teach no more than one. They just cost five times more.

## 10. Measure or don't ship

Every skill in this repo declares its top-level token count. Every skill has been through its own `token-audit`. No exceptions.

This is how we know we are not lying about being lean.

---

## The anti-pattern catalog

Patterns we refuse:

| anti-pattern | example | fix |
|--------------|---------|-----|
| preamble | "Certainly! I'd be happy to help..." | delete |
| hedging | "This might possibly help with..." | delete or commit to the claim |
| self-narration | "Now I will analyze..." | delete, just analyze |
| double-listing | bullet list + paragraph saying the same | keep one |
| kitchen-sink example | 50-line example covering six cases | split into focused examples |
| advisory voice | "You may want to consider..." | imperative |
| over-acknowledgment | "That's a great question!" | delete |

---

## What we are not doing

We are not writing the shortest possible prompts that barely work. That is a different game — one where correctness gets sacrificed for length.

We are writing the **shortest prompts that work reliably and completely.** Lean ≠ lossy. Every token we cut is waste, not content. If a cut hurts correctness, we put it back.

The test: could a senior engineer read the skill and execute it perfectly? If yes, it is lean enough. If the skill has to apologize, hedge, or repeat itself to make itself understood — the writing is the problem, not the length.

---

**Ship lean. Mean well. Every token earns its place.** 🛡️
