# Verification

Select checks that can expose a plausible bug. Scale effort to impact and uncertainty. Use the project's test framework; avoid new infrastructure for a small change.

## Contract-driven cases

Choose relevant cases rather than copying the entire list into every test suite:

| Dimension | Cases that often reveal failures |
| --- | --- |
| Cardinality | Empty, singleton, minimum valid size, just below/at/above a threshold, maximum supported size. |
| Ordering | Sorted, reverse-sorted, all equal, ties at a cutoff, out-of-order arrival, stable-order requirements. |
| Values | Duplicates, missing values, zero, negatives, extreme values, long keys, and invalid data where accepted at the boundary. |
| Structure | Deep chain, wide graph, cycles, disconnected components, duplicate edges, overlapping intervals, and equal endpoints. |
| Distribution | All-distinct keys, one hot key, skewed groups, clustered timestamps, and worst-case candidate counts. |
| Text/numbers | Overflow boundaries, unsafe integer conversions, decimal rounding, multibyte text, combining characters, and normalization. |
| State/lifecycle | Repeated calls, mutation of input, retries, cancellation, exhausted streams, partial failure, and stale state. |
| Resources | Large valid input, bounded buffers under slow consumption, queue growth, and retained state after completion. |

Use malformed-input tests at the layer responsible for validation. Do not add defensive checks at every internal call when a trustworthy upstream contract already guarantees them.

## Correctness evidence

- State the invariant, initialization, preservation, and termination briefly for subtle logic. This is a correctness summary, not a request for a lengthy reasoning transcript.
- Use table-driven examples for externally visible boundaries and regressions.
- For nontrivial optimized logic, compare against a deliberately simple independent implementation on small inputs when practical.
- Exhaustively enumerate tiny domains when boundary interactions matter. Cap enumeration and document the explored domain.
- For generated cases, fix the seed, retain a failing input, and avoid using the same algorithm for both the candidate and oracle.
- Use domain properties when an oracle is impractical. Verify, for example, that sorting preserves the multiset and establishes order; grouping neither loses nor duplicates records; and a returned path uses valid edges with the reported cost.
- Check optimality separately from validity. A valid route may still be longer than the minimum. Use small brute-force cases or a trusted reference where needed.
- Use metamorphic relations only when the contract supports them. Permutation invariance is invalid when arrival order matters; duplicate insertion can change counts and ranking.
- Inspect mutability and state leakage by reusing inputs and instances when relevant. Ensure repeated calls preserve the intended contract.

For a bug fix, demonstrate a meaningful regression case. If safe and practical, show that it fails before the fix and passes afterward. Do not rewrite the test expectation to accommodate accidental behavior changes.

## Complexity audit

Trace costs through the actual code:

1. Identify each loop's maximum iterations and the operations within it.
2. Include comparator, hash/key construction, copying, allocation, and neighbor-generation costs where material.
3. Include preprocessing, reconstruction, result materialization, and retained state.
4. Distinguish expected from worst-case bounds and explain the assumption that permits the expected bound.
5. For data access, account for query count, scanned/transferred data, and per-request batching.
6. For concurrency, include queued work and total work as well as critical-path latency.

Do not multiply loop bounds blindly: monotone pointers or amortized stack operations can make nested-looking code linear. Conversely, a concise library call inside a loop can conceal quadratic work.

## Performance evidence

- Reproduce the issue and determine whether CPU, allocations, database access, network, or contention dominates. Use profiling or query diagnostics when available and proportionate.
- Compare the baseline and candidate on the same machine, runtime mode, input, output contract, and concurrency. Warm up managed runtimes when applicable and control cache state consistently.
- Use representative sizes and distributions, plus the growth dimension that motivated the change. Keep benchmarks bounded and free of production side effects.
- Separate setup from steady-state work unless setup is part of the user's operation. Consume equivalent outputs so dead-code elimination or lazy evaluation does not invalidate results.
- Repeat enough to assess noise. State the metric, conditions, and sample count; do not report a tail percentile from an inadequate sample.
- Measure peak memory or allocation behavior when memory is the target. Faster elapsed time alone does not demonstrate bounded memory.
- Report measured values separately from complexity analysis. If no baseline execution or profiler is available, describe the unmeasured expectation without inventing numbers.
- Run relevant correctness checks after optimizing. Stop once evidence is sufficient for the change; do not turn a local fix into an unrelated benchmark project.

## Honest result

Report what was executed, what passed or failed, what was inspected only, and any material remaining uncertainty. Passing selected tests is evidence within their scope, not a proof that every input or deployment is safe.
