# 🔥 performance lens

Question: what does this cost to run at the stated scale?

## Precondition
Skip if scale is not stated. Ask for:
- Request rate (qps / batch size)
- Data volume (rows, bytes, concurrent users)
- Latency target (p50, p99, or SLA)

Do not invent numbers. If none come, emit: "skipped — scale not stated".

## Check

| layer | red flag |
|-------|----------|
| time complexity | nested loop over external calls; O(n²) on n stated-large; recursion without memoization |
| memory | allocation in hot loop; unbounded buffer; closure captures growing state; string concat in loop |
| I/O | blocking call where async fits; no batching; fsync per item; sync-in-async anti-pattern |
| network | round trips per operation; payload bloat; no compression; no keep-alive |
| database | missing index on hot filter; N+1; lock contention; full scan; OR conditions preventing index use |
| cache | no cache on stable reads; cache stampede risk; no TTL jitter; invalidation race |
| serialization | JSON in hot path where binary fits; reflection per call; schema parse per request |
| startup | lazy imports at first-request path; synchronous config fetch; cold-start amplification |

## Emit
Top 3 hotspots: `(location, estimated impact at stated scale, cheapest fix)`.

## Severity
- **blocker**: fails stated SLA or crashes under stated load
- **major**: 5× regression vs baseline, or meets SLA only at 10× cost
- **minor**: optimization possible, current acceptable

## Discipline
- Always anchor estimates to the user-stated scale. "Slow at 1M rows" is useless if the scale is 1K.
- Suggest the cheapest fix first. Second-cheapest only if first is architectural.
- Benchmark-or-it-didn't-happen: recommend a measurement before any rewrite.
