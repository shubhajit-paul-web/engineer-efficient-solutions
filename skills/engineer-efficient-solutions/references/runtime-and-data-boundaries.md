# Runtime and data boundaries

Read the sections relevant to the task. Inspect the actual language version, library, driver, database, and project conventions before relying on a guarantee. Do not introduce a different technology stack to satisfy this guide.

## Language and representation

| Boundary | Verify before implementation |
| --- | --- |
| Numbers | Integer width, overflow behavior, safe integer range, division/modulo semantics, NaN and infinity. Use checked operations or exact representations where the contract needs them. |
| Decimal values | Currency units, rounding rules, scale, and accumulated error. Avoid assuming binary floating point preserves exact decimal amounts. |
| Strings | Bytes versus code units, code points, or grapheme clusters; normalization, case folding, and locale. Define what a length, character, or equal key means. |
| Collections | Equality and hash semantics, identity versus structural keys, iteration guarantees, mutation during iteration, and whether views retain the original buffer. |
| Copying and slicing | Whether operations copy, share, allocate, or retain backing storage; include repeated concatenation and hidden conversion costs. |
| Sorting | Mutation, stability, comparator contract, auxiliary memory, and runtime-specific complexity. Do not infer sortedness from insertion order. |
| Iteration | Eager versus lazy operations, repeated enumeration, iterator invalidation, and cleanup of unfinished streams. |
| Recursion | Maximum input depth, stack bounds, and actual tail-call support. Avoid relying on assumed tail-call optimization. |
| Ownership | Aliasing, lifetimes, copy-on-write, mutability, and synchronization. A local change can affect callers through a shared reference. |
| Time | Duration versus wall-clock timestamp, monotonic clocks for elapsed time, time zones, daylight-saving boundaries, and interval endpoint rules. |

Prefer the native standard library when its semantics fit. Verify unfamiliar contracts in the installed implementation or official documentation; do not claim a runtime guarantee from memory when uncertain.

## Memory and streams

- Model peak retained bytes as well as element counts. Include object overhead when material, transient copies, decoded payloads, output buffers, indexes, and concurrent in-flight work.
- Stream or paginate when the source and consumer support it. Avoid collecting the entire stream in a helper after claiming bounded memory.
- Set limits from requirements or existing configuration. Specify reject, spill, pause, or cancellation behavior when limits are reached; do not silently omit valid results.
- Track state cardinality. A map that processes an unbounded stream can still grow without bound even when each record is processed once.
- Distinguish working memory from stored state and output. Externalizing state shifts cost to I/O and storage; it does not eliminate it.
- Propagate backpressure and release files, cursors, sockets, and buffers on error or early exit.
- For untrusted input, consider resource amplification: pathological regex backtracking, adversarial hash behavior, decompression growth, and oversized keys. Enforce the contract's input limits at the boundary; account for expanded data before claiming a bound.

## Databases and remote APIs

- Prefer indexed filtering, projection, aggregation, and ordering near the data when supported and semantically equivalent. Verify query plans or measured query behavior when access is available.
- Treat an index as an access-path tradeoff: include selectivity, column order, write amplification, and storage. Do not promise O(log n) end-to-end behavior for an arbitrary indexed query.
- Replace N+1 operations with batching where possible, respecting driver limits, payload size, authorization, missing rows, duplicate keys, and result ordering.
- Avoid unbounded `IN` lists and huge single transactions. Bound batches and pending requests without changing required atomicity.
- Choose pagination based on the contract. Stable ordering needs a unique tie-breaker. Cursor pagination requires a cursor compatible with that order and a defined consistency model; it does not imply snapshot consistency.
- Include bytes transferred, serialization, round trips, rate limits, and query count in performance reasoning.
- Preserve transactional boundaries, access controls, and error behavior when optimizing. Fetching more data into application memory can create a security issue as well as a performance issue.

## Concurrency and scheduling

- Reduce unnecessary work before adding parallelism. Separate I/O concurrency from CPU parallelism and account for the runtime's execution model.
- Bound queued and active work, not just worker count. Match concurrency to memory, connection pools, rate limits, and downstream capacity.
- Preserve result ordering when required. Define partial-failure behavior and release resources on cancellation.
- Treat shared mutable state, duplicate work, and check-then-act races explicitly. A process-local lock does not coordinate multiple processes.
- Preserve atomicity and idempotency where operations can be retried. Do not claim exactly-once effects merely because a local queue deduplicates IDs.
- Model serial portions, scheduling, contention, copying, and coordination costs before claiming parallel speedups.

## Caches and materialized state

- Cache only a demonstrated repeatable cost. Define key equality, scope, authorization context, freshness, invalidation, and update behavior.
- Bound size and retention. TTL is a freshness or expiry policy, not a complete memory bound when arrival rates are unbounded.
- Evaluate miss bursts, duplicate computation, eviction churn, negative caching, and stale writes when relevant.
- Distinguish process-local state from cross-instance state and persistence. Never rely on an in-memory cache as the authoritative source for a consequential durable invariant.
- Count cache construction, invalidation, retained keys/values, and hit rate when estimating benefit. Prefer simpler uncached work when it already meets the constraints.
