---
name: performance-optimization
description: >-
  Diagnose and improve browser, service, database, queue, and infrastructure
  performance from evidence while preserving correctness and cost control.
---

# Performance Optimization

## Expert mental model
- Performance is user outcome under representative load, not a local microbenchmark.
- Tail latency compounds across dependencies; p99 is often where outages begin.
- Throughput is bounded by the most saturated resource, not CPU alone.
- Queueing grows nonlinearly near saturation. Running at 95 percent utilization is fragile.
- Optimize critical path first. Work outside user wait path may still affect capacity.
- Faster wrong answer, stale authorization, or dropped write is a regression.

## Measurement contract
- State user journey, population, device, network, payload, and load shape.
- Record baseline date, build, environment, cache state, sample size, and percentile.
- Choose one primary metric plus guardrails for errors, cost, memory, and correctness.
- Compare equivalent workloads. Warm local cache is not production evidence.
- Change one material cause per measurement cycle.
- Keep result only when repeatable and statistically meaningful for the target workload.

## Metrics that matter
- Browser: LCP, INP, CLS at 75th percentile from real users.
- API: request rate, p50/p95/p99 duration, error rate, dependency duration.
- Database: rows read, buffers, lock wait, query time, plan shape, connection wait.
- Worker: queue depth, age of oldest message, service time, retry rate, dead-letter rate.
- Runtime: CPU, heap, GC pause, event-loop lag, thread pool saturation, file descriptors.
- Cache: hit rate, eviction rate, load latency, key cardinality, stale read rate.
- Cost: bytes transferred, compute per request, database IOPS, third-party calls.

## Bottleneck classification
- Network-bound: waterfall has idle waits, large transfer, serial requests, or slow origin.
- CPU-bound: flamegraph has wide hot stack, high process CPU, long tasks.
- Database-bound: trace waits on query, plan scans too much, lock or pool wait grows.
- Contention-bound: lock, mutex, connection pool, worker queue, or rate limiter dominates.
- Memory-bound: GC pressure, swapping, cache churn, allocation hot path.
- Dependency-bound: one external service dominates critical path or retries amplify load.
- Identify one first-order bottleneck before discussing optimization technique.

## Backend workflow
1. Slice p95 and p99 latency by endpoint, region, tenant class, and dependency.
2. Open representative trace and locate largest exclusive wait.
3. Confirm with logs, metrics, profile, or database plan.
4. Change narrow cause, such as batching, index, response shape, or call elimination.
5. Re-run same load and compare tail latency, errors, saturation, and cost.
6. Stop if next gain is below business value or raises reliability risk.

## Database craft
- Run `EXPLAIN ANALYZE` on representative parameter values, not only one easy case.
- Compare estimated rows to actual rows; major mismatch means stale statistics or correlation.
- Index supports WHERE, JOIN, ORDER BY, and often selected columns for index-only scan.
- Composite index order follows equality filters, range filter, then ordering requirements.
- Extra index costs writes, storage, vacuum, cache residency, and migration time.
- N+1 query count grows with returned rows. Batch keys, preload relations, or join deliberately.
- Fetch only needed columns. Large JSON or blob transfer often dominates query time.
- Offset becomes expensive at deep pages; use seek cursor with indexed deterministic order.
- Lock waits can be faster query issue or transaction scope issue; inspect both.

## Caching framework
- Cache target needs measured reuse, expensive source, and acceptable staleness.
- Cache location choices: browser, CDN, edge, application memory, distributed cache, database.
- Put cache nearest reusable audience without leaking authorization-scoped data.
- Cache key includes all output-varying inputs: identity scope, tenant, locale, version, query.
- TTL bounds staleness only when stale data is safe.
- Invalidation on write requires durable event or explicit recovery, not best-effort process call.
- Protect source against stampede with coalescing, leases, stale-while-revalidate, and jitter.
- Measure miss penalty and eviction before declaring hit rate success.

## Frontend craft
- LCP usually depends on server response, render-blocking assets, hero image, and main-thread work.
- INP comes from long JavaScript task, expensive event handler, layout thrash, or render cascade.
- CLS comes from missing dimensions, late content insertion, font swap, or ad/embed allocation.
- Use browser performance trace before adding memoization.
- Reduce critical JavaScript through server rendering, code splitting, and removing unused dependency.
- Prioritize only truly above-fold image or font; over-prioritization destroys scheduling.
- Avoid layout reads after writes in loop; batch reads then writes.
- Use virtualization only after long list measurement and preserve accessibility.

## Concurrency and queues
- Little's Law: concurrent work equals arrival rate times latency.
- Increasing concurrency helps only until shared resource saturates.
- Bound worker and request fanout concurrency; unbounded parallelism causes collapse.
- Backpressure means reject, queue, shed optional work, or slow producer before overload.
- Queue depth alone hides trouble; track oldest age and completion rate.
- Retry storms can turn partial dependency failure into full outage.
- Use exponential backoff plus jitter, bounded attempts, and retry budgets.

## Flamegraph reading
- Width means sampled time, not source line importance.
- Start with widest actionable stack on affected workload.
- Look for repeated serialization, regex, allocation, JSON, logging, and lock waits.
- Compare before and after profiles under equivalent workload.
- Do not optimize tiny self-time while waiting on I/O dominates wall time.

## Budgets
- Set SLO from user need, then derive component budgets.
- Example: 2 s page budget cannot allow 1.8 s API plus 1 s browser work.
- Typical Core Web Vitals targets: LCP <= 2.5 s, INP <= 200 ms, CLS <= 0.1.
- Set payload, image, JavaScript, query-count, and dependency-call budgets per route.
- Error budget makes performance change safe to roll out and regressions visible.

## Implementation recipes
### Fix N+1
1. Trace one list request and count queries.
2. Fetch parent IDs once.
3. Batch child rows with `WHERE child.parent_id IN (...)`.
4. Map children by parent ID in memory.
5. Re-check query plan and p95 under expected page size.

### Add index
1. Capture slow query and actual plan.
2. Verify filter selectivity and order requirement.
3. Create narrow index concurrently where database supports it.
4. Re-run plan with production-like values.
5. Monitor write cost and unused-index evidence.

## Failure modes juniors miss
- Average improved while p99 and timeout rate worsened.
- Cache masks database issue but misses still overload source.
- Added index is unused because column order or cast prevents matching.
- More connection pool slots increase database context switching and lock contention.
- Compression saves bytes but increases CPU on already CPU-bound service.
- Memoization adds comparator cost and fails because props are unstable.
- Synthetic lab test passes while low-end devices fail field INP.
- Retrying slow dependency increases its load and worsens recovery.

## Critique rubric
- Baseline, target, workload, and guardrail metrics are named.
- Root bottleneck has trace, profile, plan, or field-data evidence.
- Change attacks critical path rather than cosmetic microbenchmark.
- Database work includes actual plan and write-side tradeoff.
- Cache design includes scope, invalidation, stampede, and stale policy.
- Browser work includes real Web Vitals and execution, not bundle size alone.
- Load control covers deadlines, concurrency, backpressure, and retries.
- Result reports percentile, error, saturation, cost, and rollback observation.

## Authoritative references
- https://sre.google/sre-book/monitoring-distributed-systems/
- https://web.dev/articles/vitals
- https://developer.chrome.com/docs/devtools/performance
- https://www.postgresql.org/docs/current/using-explain.html
- https://www.brendangregg.com/flamegraphs.html
