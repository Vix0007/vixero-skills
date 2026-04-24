# 🧩 integration lens

Question: does this fit the codebase and contracts it touches?

## Check

| dimension | red flag |
|-----------|----------|
| consistency | naming, errors, logging, config diverge from existing patterns in the repo |
| contracts | public API shape changes without versioning; consumers not enumerated |
| dependencies | new import unjustified; license incompatible; transitive risk; version pin missing |
| cross-cutting | missing observability, tracing, structured logging, feature flag, i18n hook |
| deploy surface | migration ordering wrong; no rollback path; no feature gate for staged rollout |
| config | hard-coded where config exists; env var sprawl; secrets checked in |
| module boundary | reaches past the layer it belongs in; circular import risk |
| test harness | new code path missing fixtures; test-only helpers in prod path |
| docs surface | README, CHANGELOG, API docs, ADR — any that need update and don't have it |
| versioning | semver implications of the change not stated |

## Emit
For each incompatibility: `(dimension, what breaks, required sibling change, blast radius)`.

## Severity
- **blocker**: breaks consumers silently; no rollback path; security-critical config gap
- **major**: inconsistency that compounds across future work; skipped cross-cutting concern
- **minor**: stylistic mismatch; non-critical doc gap

## Discipline
- Check the repo's ADRs / conventions before flagging style. House style trumps industry style.
- "Should follow X" without citing where X is established in this codebase is speculation. Cite.
- Not every change needs versioning discussion — only public-API-adjacent ones.
