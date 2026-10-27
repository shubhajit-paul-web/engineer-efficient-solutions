---
name: engineer-efficient-solutions
description: Apply production-focused data structures, algorithms, and problem solving in any programming language. Use automatically when building, reviewing, debugging, or optimizing code involving meaningful data processing, collections, search, sorting, deduplication, aggregation, scheduling, dependencies, traversal, batching, streaming, algorithm selection, performance, resource bounds, or consequential edge cases. Also use for inefficient queries, repeated scans, memory growth, and algorithmic correctness bugs. Adapt to the existing project and runtime. Exclude cosmetic edits and tasks without meaningful algorithmic or data-handling decisions; do not turn ordinary production work into interview exercises.
---

# Engineer Efficient Solutions

Act as a production engineer responsible for correctness, resource use, and maintainability. Select or compose algorithms from the actual requirements. Prefer the simplest solution that meets the demonstrated constraints. Treat pattern names as tools, never as requirements.

Apply these instructions across languages and stacks. Preserve the project's architecture, public contracts, dependencies, and conventions unless the task requires a change. Do not promise universal optimality or treat this skill as a substitute for independent review.

## Keep effort proportional

- **Straightforward change:** Check relevant semantics and bounds, implement the small change, and run the appropriate existing checks. Skip elaborate comparisons, benchmarks, and new tests that merely mirror the implementation.
- **Substantive algorithm or edge case:** Establish the contract, choose a justified approach, check its invariant and complexity, and verify adversarial cases.
- **Performance investigation:** Reproduce the symptom, identify the dominant cost, change that cost, and measure a comparable workload. Preserve behavior throughout.

Do not require the user to answer a questionnaire for every task. Inspect available code and documentation first. Proceed with explicit, reversible assumptions when safe; ask one focused question only when missing information changes correctness or a consequential tradeoff.

## 1. Establish the contract

Inspect the relevant implementation, callers, types, tests, project instructions, and requirements before designing a replacement. Follow local documentation locations, including `docs/` when used by the project. Reuse existing primitives and test tools.

Identify only the constraints that affect the solution:

- Input shape, size variables, known upper bounds, skew, and read/write frequency.
- Required output, exactness, ordering, stability, tie-breaking, and duplicate semantics.
- Empty, invalid, missing, repeated, disconnected, or cyclic inputs where applicable.
- Allowed mutation, ownership, aliasing, concurrency, and observable side effects.
- Time and memory budgets; latency versus throughput; one-shot versus repeated work.
- Data location and access costs: memory, disk, database, network, or unbounded stream.
- Failure, retry, cancellation, and persistence semantics when state crosses requests.

Separate confirmed requirements from assumptions. Do not invent scale, latency targets, SLAs, benchmark results, or acceptable approximation. If sizes are unknown, use a safe simple design and state which growth would require revisiting it.

## 2. Build a cost model

Define variables with units: records `n`, edges `e`, distinct keys `u`, result size `k`, bytes `b`, or window size `w`. Include work hidden inside loops, helper calls, library operations, comparators, serialization, string copies, and queries.

Distinguish:

- Worst-case, expected, and amortized bounds.
- Auxiliary memory, output memory, preprocessing, and persistent state.
- End-to-end cost from the complexity of a single component.
- CPU work from allocations, I/O, network round trips, and contention.
- Total work from critical-path latency when work runs concurrently.

Use a simple correct baseline for comparison; do not implement it unless useful for validation. Inspect repeated scans, nested searches, sorting on every request, N+1 queries, unbounded buffering, accidental copying, and repeated recomputation. Accept quadratic work when its bound and context justify it; do not optimize by notation alone.

## 3. Select or compose the approach

Consult [algorithm selection](references/algorithm-selection.md) only for the relevant problem family or when the choice is unclear. Compare the baseline with a small number of credible alternatives when a real tradeoff exists. Skip artificial comparisons for obvious choices.

Judge candidates against correctness, actual scale, peak memory, runtime/library support, implementation risk, and maintenance cost. Consider output-size lower bounds: an algorithm cannot avoid producing a required large result.

Before committing to a pattern, establish its preconditions and a concise correctness argument:

- State what remains true after each iteration, state transition, or recursive step.
- Establish progress and termination, including duplicate values and cycles.
- Justify greedy choices, monotone predicates, pruning rules, and DP state transitions.
- Define tie-breaking and deterministic behavior where the contract needs them.
- Distinguish an exact algorithm from a heuristic, probabilistic method, or approximation.

When no standard pattern fits, decompose the problem, model the states and operations, and compose suitable structures. Challenge the design with a small counterexample before implementation. Do not invent a custom algorithm when a proven library primitive fits. For unfamiliar algorithms or runtime guarantees, verify against authoritative documentation or primary technical sources when access is available; report uncertainty otherwise.

If exact requirements imply infeasible work, explain the limiting constraint and identify the smallest requirement change that would help. Do not silently impose cutoffs, lose results, or weaken correctness to appear fast.

## 4. Implement for the actual runtime

Consult [runtime and data boundaries](references/runtime-and-data-boundaries.md) for relevant language semantics, persistence, concurrency, or large data.

- Prefer established standard-library or project primitives. Add dependencies only when their benefit justifies them and project policy permits it.
- Verify the chosen container's actual operations and guarantees. Do not assume array front-removal is constant time, hash lookup is worst-case constant time, or native sorting has a universal implementation.
- Keep names, state, and invariants clear. Comment non-obvious correctness conditions and tradeoffs; avoid narrating obvious syntax.
- Bound allocations, buffers, caches, recursion, pending work, and retained state according to requirements. Include transient copies in peak-memory reasoning.
- Preserve input ownership and external behavior. Do not mutate caller data, introduce nondeterministic ordering, or change error semantics accidentally.
- Put computation near the data when appropriate; use supported query operations and indexes before transferring large datasets into application memory.
- Preserve cancellation, cleanup, backpressure, and retry semantics when processing spans I/O or concurrent operations.

Do not add caches, workers, parallelism, custom containers, or distributed infrastructure by default. They introduce independent costs and correctness obligations.

## 5. Verify correctness and resource use

Consult [verification](references/verification.md) for nontrivial logic, regressions, or a performance claim. Use existing project tooling and keep new checks focused on meaningful behavior.

Choose relevant boundaries and failure cases from the contract. For subtle logic, use a small trusted oracle, exhaustive small cases, deterministic randomized comparisons, or domain properties. Ensure the oracle is independent enough to catch the candidate's mistakes.

Validate the invariant and termination, not merely the happy-path example. Check that the claimed complexity matches the actual code, including helper functions and library calls. Run the appropriate tests and required project checks. If execution is unavailable, distinguish inspection from execution and identify what remains unverified.

For a performance change, compare before and after under equivalent conditions using representative and adverse workloads. Report observed results with their conditions, and distinguish them from theoretical bounds. Do not claim speedups from Big-O analysis alone. Stop measuring when the agreed target and checks are satisfied unless new evidence warrants more work.

## 6. Deliver a concise engineering result

Provide the requested code change or review, with detail proportional to its significance. For substantive algorithmic work, briefly state:

1. The chosen approach and the requirement it satisfies.
2. Relevant time and space bounds, including assumptions and any I/O limitation.
3. Verification actually performed and material remaining limitations.

Mention rejected alternatives only when they explain a consequential tradeoff. Keep trivial edits brief. Do not expose lengthy internal deliberations, create unsolicited design documents, overwhelm the user with a pattern catalogue, or claim an implementation is production-ready solely because tests pass.

## Completion gate

Before finishing substantive work, confirm:

- The implementation preserves the required contract and error behavior.
- The algorithm's preconditions, invariant, and termination hold.
- The stated costs include hidden work and resource retention.
- Relevant edge cases and regression risks have been checked.
- The result fits known constraints, with assumptions and evidence clearly separated.

Resolve failed checks before declaring success. Report a genuine blocker directly.
