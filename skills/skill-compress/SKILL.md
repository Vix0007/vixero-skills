---
name: skill-compress
description: Rewrite a verbose SKILL.md or prompt into its lean equivalent without changing behavior. Use when the user asks to compress, shrink, tighten, minify, rewrite-lean, or optimize the token count of a skill or prompt. Preserves triggers, output format, and hard rules. Reports before/after token counts.
---

# skill-compress

Rewrite input at lowest token count that preserves behavior. Do not invent new behavior.

## Trigger on
- "compress this skill / prompt"
- "make this leaner / tighter"
- "rewrite for fewer tokens"
- After `token-audit` when user asks to apply the cuts

## Rules, in order

1. **Frontmatter is sacred.** Never change `name`. Only improve `description` if it fails the description-craft checklist. Keep description 100–250 chars.
2. **Strip preamble.** Delete "I'll help", "Certainly", "Let me", "Sure", "Of course".
3. **Delete ceremony.** "Please", "kindly", "feel free", "if you'd like".
4. **Imperative voice.** "Do X" not "You should do X" or "It is recommended that X be done."
5. **Prose → table** when a table saves 30%+ tokens for the same information.
6. **Lists over paragraphs** for enumerations of three or more.
7. **Collapse examples.** Keep one best example per concept. Move extras to an `examples/` subfolder referenced by path.
8. **Merge redundant sections.** If "Guidelines" and "Rules" say similar things, merge.
9. **Code blocks stay.** Never compress code. Compression errors in code cost more than tokens saved.
10. **Output format blocks stay verbatim.** These are contracts.

## Output

Return two sections:

### 1. Rewritten SKILL.md
Full content, ready to paste.

### 2. Diff summary
```
before: {n} tokens
after:  {n} tokens
cut:    {n} tokens ({pct}%)

preserved:
- trigger keywords
- output format
- hard rules
- behavior

changed:
- {specific change 1}
- {specific change 2}
```

## Hard rules
- Never drop a hard rule. Rewrite it shorter.
- Never drop a trigger keyword from the description.
- If cut < 15% after compression, append: "Already lean. Structural rewrite needed for more savings."
- If user says "more aggressive" after first pass, apply `lean-response` register as well: drop all softening, fragment sentences are fine.
