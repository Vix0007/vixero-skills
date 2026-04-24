---
name: goal-criteria
description: Transform a vague task into verifiable success criteria with a checkable plan. Use when the user says "define success", "what should I check", "how do I know it works", "how do I test this", or hands over a loose imperative like "fix the auth system", "make it faster", or "clean this up". Returns a plan where each step pairs with a verification method, plus a rollback criterion.
---

# goal-criteria

Convert imperatives into loops the executor can run until green.

## Trigger on
- "define success"
- "what's the verification"
- "how do I know it's done"
- "what should I check"
- "how do I test this"
- "when is this finished"
- Any task stated as a bare imperative without a check

## Imperative → verifiable mapping

| vague | verifiable |
|-------|------------|
| fix the bug | test reproducing it, make it pass |
| add validation | test set of invalid inputs, all rejected with expected errors |
| make it faster | benchmark before, target number from user, benchmark after |
| refactor X | existing test suite green before AND after, no new failures |
| improve UX | measurable: clicks reduced, load time, error rate |
| clean up code | linter green, tests green, coverage ≥ baseline |
| add feature F | acceptance test stating F's behavior, passes |
| secure the endpoint | test each attack class (injection, auth bypass, rate), all return expected 4xx |
| document the API | every public function has docstring, linter passes |
| handle errors better | test each error path, user sees defined error message |
| optimize the query | EXPLAIN ANALYZE before/after, runtime at user-stated scale |
| migrate to X | all existing tests pass on X, behavior parity smoke test |
| make it scalable | load test at user-stated concurrency, p99 latency target |
| reduce dependencies | dependency count before/after, all tests still pass |
| support feature F in Y | Y-specific acceptance test, existing Y tests still pass |

## Process

1. Restate the task in one line.
2. Extract the verb and the object.
3. Ask: what observable condition proves the verb was applied to the object successfully?
4. If no observable condition exists, task is unverifiable as stated. Block until criteria defined.
5. Decompose into steps where each step has a verifier.
6. Add a rollback criterion: what observation says "revert this change".

## Block conditions

Refuse to produce a plan when:
- Success depends on a number the user has not provided (latency, coverage, size)
- Success is purely subjective ("looks good", "feels right")
- Multiple competing goals are stated without priority ("fast AND small AND elegant")
- The verb is undefined for the object ("fix the design" on a non-broken design)

When blocking, name the missing piece explicitly.

## Output

```
task: {restatement}
verb: {the action}
object: {the thing}

success criteria:
- {criterion} → check via {command / test / metric}
- {criterion} → check via {...}

plan:
1. {step} → verify: {check that runs}
2. {step} → verify: {check that runs}
3. {step} → verify: {check}

rollback criterion: {observation that says revert}

blocked on: {none | list of unknowns preventing criteria}
```

## Worked example

Input: "make the search faster"

```
task: reduce search latency
verb: reduce
object: search latency

blocked on:
- no baseline stated (current p50/p99?)
- no target stated (faster by how much?)
- no scope stated (all queries / specific ones / cold cache?)

cannot produce plan. ask:
1. Current latency (p50, p99) under what load?
2. Target latency?
3. Scope: all queries or a specific slow class?
```

Input with answers: "search p99 is 800ms at 50 qps, target p99 ≤ 200ms on current queries"

```
task: reduce search p99 from 800ms to ≤ 200ms at 50 qps
verb: reduce
object: p99 latency

success criteria:
- p99 ≤ 200ms at 50 qps → check via load test (wrk, 60s run)
- no regression in correctness → existing search tests green
- no regression on other endpoints → full integration suite green

plan:
1. Capture baseline: load test current system → verify: baseline file saved
2. Profile to find hot path → verify: flamegraph or profiler output
3. Apply top-1 optimization → verify: load test p99 ≤ 200ms
4. If still over, apply next → verify: load test until target met or options exhausted

rollback criterion: correctness test fails, or other-endpoint p99 regresses > 10%

blocked on: none
```

## Hard rules
- Never accept "make it work" or "make it better" as success. Extract a check or block.
- Each step's verify must be automatable (test, linter, benchmark, curl, EXPLAIN) — not "looks good".
- Before AND after verification are both required for refactors and optimizations.
- If criteria require a number, user must provide it. Do not invent targets.
- Multi-step tasks without per-step verification are rejected. Split them first.
- Rollback criterion is mandatory for production changes, optional for local work.
- If the plan would exceed 7 steps, recommend decomposing into multiple tasks.
