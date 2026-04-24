# vixero-skills

**Aggressive token-efficient Agent Skills for Claude.** Built to practice what they preach.

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![Skills](https://img.shields.io/badge/skills-6-green.svg)](./skills/)

---

## Why this exists

The Agent Skills ecosystem exploded. Most skills in the wild are 1000–3000 tokens each — bloated with preamble, ceremonial politeness, redundant examples, and prose that should be tables. Every one of them loads into context competing for attention, and every one of them costs you.

**vixero-skills** is a curated, aggressively lean set of meta-skills for auditing, compressing, and composing other skills. Every skill in this repo is under 500 tokens. Every skill has been through its own `token-audit`.

> If a skill does not practice what it preaches, it shouldn't preach.

## Install

Download the repo, then in Claude.ai:

```
Settings → Features → Skills → Upload skill
```

Upload each folder from `skills/` as a separate zip. Or use them in Claude Code by placing the folders in `~/.claude/skills/`.

## The Pillar 1 bundle — token efficiency meta-skills

| Skill | Purpose | Top-level tokens¹ |
|-------|---------|--------------------|
| [token-audit](./skills/token-audit/) | Score a SKILL.md for waste across 6 axes | ~520 |
| [skill-compress](./skills/skill-compress/) | Rewrite verbose skills into lean form | ~560 |
| [lean-response](./skills/lean-response/) | Session-wide response compression | ~530 |
| [description-craft](./skills/description-craft/) | Fix frontmatter descriptions that don't trigger | ~620 |
| [skill-graph](./skills/skill-graph/) | Map conflicts, redundancy, composition across a collection | ~650 |
| [token-budget-check](./skills/token-budget-check/) | Pre-flight a prompt before sending | ~590 |

**Total bundle: ~3,470 tokens for six skills.** Many single skills in competing repos exceed this.

¹ Approximated at 4 chars / token. Real tokenization is often ~15% lower. Run `token-audit` on each skill for verified numbers.

## Usage patterns

**Auditing a skill:**
> "Audit this SKILL.md for token waste"  →  `token-audit` fires.

**Compressing after audit:**
> "Apply the cuts"  →  `skill-compress` fires with the audit output.

**Session-wide:**
> "Lean mode" at the start of a session  →  `lean-response` activates, no preamble for the rest of the session.

**Collection health check:**
> Paste multiple SKILL.md files, ask "do these overlap"  →  `skill-graph` fires.

**Before sending an expensive prompt:**
> "Pre-flight this prompt: {...}"  →  `token-budget-check` fires.

## Composition chains

Skills are designed to compose:

```
token-budget-check → (rewrite) → send prompt
token-audit → skill-compress → updated SKILL.md
skill-graph → (reveals redundancy) → skill-compress on the loser
description-craft → (any new skill before first ship)
```

## Design principles

Every skill in this repo follows these rules. Contributions must follow them too.

1. **Top-level SKILL.md under 500 tokens.** Heavier content goes in subfolders.
2. **Imperative voice only.** "Do X." Not "You should do X." Not "It is recommended that X be performed."
3. **Tables over prose** when information has >3 parallel items.
4. **One skill, one job.** Multi-purpose skills fire on the wrong tasks.
5. **Description is the load-gate.** See [description-craft](./skills/description-craft/SKILL.md).
6. **Hard rules are non-negotiable.** Guidance is negotiable; hard rules override.
7. **No emotional padding.** "Please", "kindly", "feel free" — deleted.
8. **No meta-narration.** Do not announce you are doing the thing. Just do it.

## Roadmap

- **Pillar 1 (this release)** — Token efficiency meta-skills ✅
- **Pillar 2** — Security & NIDS skills (companion to [pcap2tensor](https://github.com/Vix0007/pcap2tensor) and [arXiv:2604.02149](https://arxiv.org/abs/2604.02149))
- **Pillar 3** — Student & research workflow skills
- **Pillar 4** — ASEAN / Malaysia regional skills

## Contributing

Open a PR. Every new skill must:

1. Pass its own `token-audit` at score ≥ 75.
2. Have a frontmatter description that passes `description-craft`.
3. Be under 500 tokens at top level.
4. Declare its token count in the skill's README.

## Citation

If you use vixero-skills in research or tooling, attribution is appreciated:

```
vixero-skills (2026). Aggressive token-efficient Agent Skills for Claude.
Vickson Ferrel, Vixero Technology Enterprise.
https://github.com/Vix0007/vixero-skills
```

## License

MIT © Vickson Ferrel — [Vixero Technology Enterprise](https://vixdev.cloud)

---

**Built in Sarawak. For humans who pay for tokens.** 🛡️
