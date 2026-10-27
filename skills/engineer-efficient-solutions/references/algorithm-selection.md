# Algorithm selection

Use this as a decision aid, not a checklist to apply in full. Prefer a familiar primitive only after checking its assumptions. Combine structures when the operations demand it.

## Contents

- Selection by operation
- Correctness conditions
- Composition and infeasible requirements

## Selection by operation

| Required operation or structure | Candidate | Critical condition or common failure |
| --- | --- | --- |
| Membership, counts, grouping, deduplication | Set or map | Define key equality and normalization; bound distinct keys; preserve order separately if needed. Expected constant-time access is implementation-dependent. |
| Ordered lookup, predecessor, successor, dynamic range | Balanced ordered structure | Do not assume an ordinary map keeps sorted keys or supports logarithmic range access. |
| Repeated membership in a static dataset | Hash index or sorted data plus binary search | Include index construction and storage. Sorting may be better when ordered access also matters. |
| Dense bounded integer membership or counts | Bitmap or count array | Check domain width and integer limits; sparse domains can make this wasteful. |
| Ordered scan or pair matching | Sorting plus two pointers | Verify pointer moves cannot skip a solution; preserve original positions if required. Include sorting cost. |
| Fixed-length contiguous aggregate | Sliding window | Define window boundaries and update order. Ensure the aggregate supports removal or maintain additional state. |
| Variable-length contiguous constraint | Sliding window when the validity condition permits monotone moves | Negative numbers invalidate many sum-based window arguments. General signed subarray sums may need prefix sums plus a map or ordered structure. |
| Many static range sums or counts | Prefix aggregates | Require a valid inverse for subtraction; include preprocessing and update limitations. Prefix minima cannot generally answer arbitrary range minima by subtraction. |
| Range updates with final materialization | Difference array | Bound endpoints and restore by prefix accumulation; unsuitable for arbitrary online queries without another structure. |
| Online range queries and updates | Fenwick or segment tree | Match algebra and update type; a Fenwick sum structure does not automatically support arbitrary range operations. |
| Single minimum, maximum, count, or reduction | One pass | Do not sort a full collection to obtain a single extremum. |
| Small top-k from a large stream | Size-k heap | Usually O(n log(k+1)) time and O(k) state for 1 <= k <= n; define ties, k=0, k>n, and ordered output. Final sorting adds O(k log k). |
| One rank in an in-memory batch | Selection algorithm | Verify expected versus worst-case behavior, mutation, duplicates, and the library's actual guarantee. Sorting may be simpler for small inputs or multiple ordered results. |
| Next greater/smaller or window extrema | Monotonic stack/deque | Define equal-value handling and expired-index eviction. Prove each element enters and leaves a bounded number of times. |
| First valid value in ordered data or a monotone answer domain | Binary search | Prove predicate monotonicity, establish feasible bounds, and include predicate cost. Use an explicit interval convention. |
| Merge sorted streams | k-way merge with a heap | Requires sorted sources; include source buffers, output cost, tie order, and cancellation. |
| Reachability, components, dependency traversal | BFS/DFS | Track visited states appropriately; cover cycles, disconnected nodes, deep graphs, and adjacency representation costs. |
| Minimum edge count on an unweighted graph | BFS | Mark visited when enqueuing to avoid redundant expansion; do not use DFS to infer shortest paths. |
| Minimum weighted path | Dijkstra for nonnegative weights; alternatives for negative weights | Dijkstra is invalid with negative edges. Handle stale heap entries, unreachable nodes, overflow, and required negative-cycle behavior. |
| Dependency ordering | Topological sort | Requires a DAG; detect cycles explicitly. Use stable priority rules if a reproducible order is required. |
| Repeated connectivity under additions | Disjoint-set union | Path compression plus appropriate union policy gives amortized near-constant operations; ordinary DSU does not support arbitrary deletions or provide paths. |
| Intervals, overlap, occupancy, chronological events | Sort and sweep; heap for active intervals | Define open/closed endpoints, equal-time event ordering, time zone semantics, and cancellation. |
| Local optimal choices | Greedy | Require an exchange, cut, dominance, or equivalent argument. Test counterexamples; weighted interval scheduling generally needs DP rather than earliest-finish greedy. |
| Repeated subproblems with sufficient state | Memoization or bottom-up DP | Define state, transition, base case, dependency order, and reconstruction. Include number of states and transition cost; avoid unbounded memo tables. |
| Small constrained search or combinatorial choice | Backtracking, branch-and-bound, bitmask DP | Bound the state space and justify pruning. Include stack, copied state, and cancellation; never hide truncation of an exact result. |
| Prefix search over many strings | Sorted prefix range or trie | Compare memory and update costs; define Unicode, normalization, and ranking semantics. A trie is not automatically the best search index. |
| Very large or unbounded data | Incremental aggregation, external sort, partitioning, bounded windows | Streaming does not remove retained-state costs. Exact global distinctness still needs growing state, external storage, or a bounded domain. |
| Approximate membership or cardinality | Bloom filter or sketch | Use only when the error model is acceptable; do not treat probabilistic membership as exact deletion, authorization, or billing evidence. |

## Correctness conditions

### Binary search

Choose closed or half-open intervals and maintain the chosen invariant. Show that every iteration shrinks the interval, including adjacent bounds and duplicates. Define the all-false and all-true outcomes. Avoid midpoint overflow in runtimes where it applies. Searching a numeric answer takes predicate work times the number of search steps; the predicate itself may dominate.

### Windows and pointers

Explain why moving a boundary is safe and never needs reversal. Distinguish fixed-size windows, monotone validity windows, and problems requiring richer state. For signed sums, test a positive element followed by a negative element before assuming shrink-on-overflow is valid. Store indices when expiry or original positions matter.

### Graphs and recursion

Define graph direction, edge weights, adjacency representation, and the meaning of a repeated state. Marking a node globally visited is not equivalent to tracking the current recursion path for cycle detection. For implicit graphs, include neighbor-generation cost. Prefer iterative traversal when input depth can exceed the runtime stack. For BFS/DFS, O(v+e) assumes adjacency lists and bounded per-edge work.

### Dynamic programming

Include all history that can change future choices in the state. Prove the state is sufficient before optimizing it. Check dependency order before compressing dimensions; in-place updates can reuse a choice illegally. Recognize pseudopolynomial dependence on numeric values, not only the number of input records. Include reconstruction memory when the required output is a plan or path.

### Sorting, hashing, and comparators

Use a transitive, consistent ordering, with explicit null, NaN, locale, and tie behavior when relevant. Avoid subtraction comparators if overflow is possible. Do not mutate keys after insertion into hash-based collections. Treat hashing long or composite keys and expensive comparators as nonconstant work when their sizes matter.

## Composition and infeasible requirements

- Combine a map with an ordered list for LRU only when access recency is required; it does not solve expiry, persistence, or cross-process consistency.
- Combine a map with a heap for changing priorities only with a strategy for stale entries or indexed updates. Bound stale retained state.
- Separate immutable preprocessing from frequent queries; amortize construction over actual reuse, and define update invalidation.
- For exact large-scale ranking, consider query/index support before building an application-side heap over all remote rows.
- For many-to-many output, count the output cardinality before promising subquadratic runtime.
- For exponential problems, identify tractable parameters, bounded instances, decomposition, or explicitly agreed approximation. Do not label a heuristic optimal.
- Prefer adapting an established algorithm over inventing a new one. For a custom composition, state its invariant and challenge it with small exhaustive cases before relying on it.
