---
name: assumption-surface
description: Extract hidden assumptions from a task request before any work begins. Use when the user says "before you start", "think this through", "what would you assume", "what am I missing", "is this clear enough to build", or when a one-line request triggers multiple implementation choices. Returns ranked assumptions by blast radius, minimum questions to proceed, and candidate interpretations.
---

# assumption-surface

Force assumptions into the open. Do not start work while high-blast gaps remain.

## Trigger on
- "before you start"
- "what would you assume"
- "what am I missing"
- "think this through first"
- "is this clear enough to build"
- Any request where scope, format, data source, or target is not specified

## Blast radius

| blast | meaning | handling |
|-------|---------|----------|
| high | wrong guess → rework everything | mandatory question |
| medium | wrong guess → section rework | recommended question |
| low | wrong guess → easy patch | proceed with stated default |

## Common assumption categories

| category | example |
|----------|---------|
| scope | all rows / filtered subset / just new ones |
| format | JSON / CSV / XML / proprietary |
| delivery | file / API response / email / webhook |
| performance | best-effort / bounded latency / hard SLA |
| concurrency | single user / many users / batch |
| failure mode | fail loud / fail silent / degrade |
| data source | prod DB / staging / cached / user-provided |
| auth | authenticated / public / service-to-service |
| target env | dev / staging / prod / all three |
| persistence | in-memory / disk / database |
| existing code | greenfield / extend existing / replace |

## Process

1. Read the request. List every decision the implementer must make that was not stated.
2. Map each to a category above.
3. For each, note the default most LLMs would assume silently.
4. Rank by blast radius.
5. Extract questions for high + medium. Low proceeds with a noted default.
6. If no answer comes, generate 2–3 candidate interpretations ranked by likelihood.

## Output

```
request: {one-line summary}

assumptions (high blast):
- {category}: {assumption} → default: {typical guess} → ask: {question}
- ...

assumptions (medium):
- {category}: {assumption} → default: {guess} → ask: {question}

assumptions (low — proceeding with defaults):
- {category}: {default}

minimum questions to proceed:
1. {question}
2. {question}

interpretations if no answer comes:
A. {reading 1} → implies {concrete choice chain}
B. {reading 2} → implies {concrete choice chain}
C. (optional) {reading 3}

recommended path: {A | B | C | hold for answers}
reasoning: {one line on why this path}
```

## Worked example

Input: "add a way to export users"

```
request: export users functionality

assumptions (high blast):
- scope: all users vs filtered subset → default: all → ask: scope?
- delivery: file download vs API response → default: API → ask: how delivered?
- fields: which columns → default: id+name+email → ask: which fields (privacy)?

assumptions (medium):
- format: JSON vs CSV → default: JSON → ask: format?
- volume: thousands vs millions → default: thousands → ask: scale?

assumptions (low — proceeding with defaults):
- persistence: no caching needed
- auth: admin-only endpoint

minimum questions to proceed:
1. Scope + field set (privacy-critical)
2. Delivery mechanism
3. Expected volume (affects pagination / streaming choice)

interpretations if no answer:
A. Admin API endpoint → paginated JSON, id+email+name, admin-auth
B. One-off data dump → CSV file, all fields, run via script

recommended path: A
reasoning: API is safer default — paginated, auth-bound, does not need ops support
```

## Hard rules
- List assumptions before writing code, prose, or structure.
- If zero high-blast assumptions exist, say "no hidden assumptions" and proceed.
- Never exceed 5 minimum questions. More = request is too broad, recommend decomposition.
- Never ask about style preferences unless the task is style-bound (writing, naming, UI).
- Defaults come from project context first, general conventions second.
- If the request is one sentence and hits 3+ high-blast assumptions, flag "request underspecified" at the top.
- Never proceed on low-blast defaults if user explicitly asked you to enumerate everything.
