---
name: backend-patterns
description: >-
  Design reliable server systems: boundaries, transactions, async work, caches,
  pools, failure handling, and production observability.
---

# Backend Patterns

## Expert mental model
- Backend is a state transition system under partial failure.
- Request success is not business success until required state is durable.
- Networks can delay, duplicate, reorder, lose, or complete requests after timeout.
- Local transaction provides atomicity for local data, never distributed atomicity.
- Queue delivery is normally at-least-once. Make consumers safe under replay.
- Cache is derived replicated state. It trades freshness for latency and cost.
- Service boundaries cost latency, deployment, tracing, versioning, and coordination.
- Start from data ownership and invariants, not framework layers.

## Discovery questions
- What state changes? Which invariant must hold? Which data owner enforces it?
- Which side effects leave process: event, email, payment, HTTP, file write?
- Is stale read acceptable? State maximum age and user-visible consequence.
- Can caller retry, worker redeliver, or events arrive out of order?
- What peak RPS, payload size, fanout, hot key, and batch duration exist?
- What happens on crash after local write but before external side effect?

## Boundary decision framework
- Layered module: transport -> application -> domain -> infrastructure.
- Choose it for one deployable, shared transaction, small team, simple data ownership.
- Modular monolith: feature modules with private internals in one process and database.
- Prefer modular monolith until independent release, scale, ownership, or fault isolation is measured.
- Split service only when it owns its data and has an independent operational reason.
- Shared database across services makes coupling hidden and schema changes unsafe.
- Do not split for folder size, fashion, or speculative scale.
- Cross-module calls use application contracts, never another module's tables.

## Transaction craft
- One transaction protects one business invariant and authoritative tables.
- Keep transaction short. No HTTP, queue publish, user wait, or slow CPU inside.
- Use unique constraints for facts that must never duplicate.
- Use row locks, optimistic version columns, or serializable isolation for concurrent writes.
- At READ COMMITTED, repeated query can differ. At SERIALIZABLE, retry aborts.
- Lock rows in stable order to reduce deadlocks; retry selected deadlock victims.
- Every transaction has deadline and rollback on all non-success paths.
- Cursor ordering needs immutable, indexed, deterministic tie-breaker.

## Outbox and inbox
- Do not publish after commit without recovery: process crash loses event.
- Do not publish before commit: rollback emits false event.
- Transactional outbox writes business change and immutable event in one transaction.
- Relay publishes record then marks it delivered after broker acknowledgement.
- Relay can duplicate; outbox is not exactly-once.
- Inbox persists event ID under unique constraint before consumer applies effect.
- Deduplication key scopes to producer and message ID, not payload hash alone.
- Keep event schema immutable; add optional fields instead of rewriting meaning.

## Idempotency recipe
1. Require key for externally retryable POST or command.
2. Scope key by tenant, actor, and operation.
3. Persist canonical payload fingerprint and response status.
4. Enforce unique key in database, not process memory.
5. Same key and fingerprint returns stored response.
6. Same key and different fingerprint returns conflict.
7. Retain for 24 hours or longest legitimate retry window.

## Queue semantics
- Assume at-least-once unless broker contract proves end-to-end guarantee.
- Acknowledge only after durable effect. Early acknowledgement loses work on crash.
- Handler idempotency uses inbox, unique constraint, or guarded state transition.
- Ordering is usually partition-local, never global. Choose key deliberately.
- Retry transient failure with exponential backoff and full jitter.
- Cap attempts, then dead-letter poison messages with failure context.
- Do not retry validation, auth, schema, or deterministic business rejection.
- Visibility timeout exceeds p99 execution time plus margin; extend known long jobs.
- Record event ID, correlation ID, causation ID, attempt, and producer version.

## Cache framework
- Cache only measured expensive reads with explicit freshness policy.
- Cache-aside: read cache, load miss, write DB, then invalidate cache.
- Write-through simplifies reads but adds write path dependency and latency.
- TTL is an eviction mechanism, not a correctness invalidation strategy.
- Key includes tenant, locale, authorization scope, schema version, and query shape.
- Permission revocation, delete, price, and aggregate changes are invalidation hard cases.
- Do not stale-cache authorization or money state without explicit safe design.
- Prevent stampede with request coalescing, lease, stale-while-revalidate, or early refresh.
- Negative-cache misses briefly only if object creation and enumeration are safe.

## External dependency rules
- Configure connect, request, idle, and total deadline for every client.
- Downstream deadline must fit inside caller deadline with cleanup margin.
- Retry only idempotent work or work protected by stable idempotency key.
- Two or three attempts is usually enough. More retries amplify outage load.
- Bulkheads isolate optional work from critical dependencies.
- Circuit breaker needs a measured fallback or load-shedding action.
- Propagate cancellation and deadline through every dependency.
- Cap fanout concurrency. One request must never create unbounded work.

## Database and pooling
- Parameterize values. Allowlist SQL identifiers and sort directions.
- Index predicate, join, and order of measured hot queries.
- Confirm index with actual query plan and production-like cardinality.
- N+1 means query count grows with result rows. Batch, preload, or join.
- Offset pagination gets slower at deep pages and races with inserts.
- Pool size is not throughput dial. Excess connections increase contention and tail latency.
- Set pool acquisition timeout below request deadline; fail fast under saturation.
- Close rows, cursors, statements, and transactions on every path.

## Observability
- Golden signals: latency, traffic, errors, saturation.
- Report p50, p95, p99. Average hides worst user experience.
- RED: request rate, error rate, duration. USE: utilization, saturation, errors.
- Propagate W3C trace context through HTTP, queues, and jobs.
- Structured logs contain operation, outcome, latency, request ID, and safe actor ID.
- Metric labels must have bounded cardinality. Never use user or request IDs as labels.
- Alert on user impact and error-budget burn, not one noisy resource gauge.
- Dashboards show dependency latency and pool wait next to endpoint latency.

## Implementation recipes
### Create order
1. Validate command and authorize account.
2. Begin transaction.
3. Lock or version-check inventory.
4. Insert order and reservation.
5. Insert immutable `OrderCreated` outbox record.
6. Commit.
7. Relay publishes; consumer records inbox ID before charging or sending.

### Cache-aside read
1. Build scoped, versioned key.
2. Return hit if fresh enough.
3. Coalesce concurrent miss loaders.
4. Query authoritative store with deadline.
5. Write bounded value with TTL.
6. Commit writes then invalidate related keys via outbox.

## Failure modes juniors miss
- Timeout does not prove remote operation failed.
- Exactly-once is business property, never a broker toggle.
- Pre-check before insert races; unique constraint enforces invariant.
- Long transaction waits on remote service while holding locks.
- Lost cache invalidation leaves stale data without outbox recovery.
- Shared service database prevents independent change and incident isolation.
- Autoscaling consumers raises duplicate concurrency and downstream pressure.
- Cache hit rate can look good while authorization data is dangerously stale.

## Critique rubric
- Boundaries align with data ownership and transaction need.
- Every write names invariant, concurrency control, and replay behavior.
- Every side effect has outbox or compensating recovery plan.
- Every network call has deadline, retry class, and bounded concurrency.
- Cache states key scope, TTL, invalidation, owner, and stale-read policy.
- Queue handler is idempotent, observable, and poison-message safe.
- Hot query has measured plan and pool saturation evidence.
- Dashboard exposes p95/p99, error rate, saturation, and dependency latency.

## Authoritative references
- https://microservices.io/patterns/data/transactional-outbox.html
- https://microservices.io/patterns/communication-style/idempotent-consumer.html
- https://www.postgresql.org/docs/current/transaction-iso.html
- https://sre.google/sre-book/monitoring-distributed-systems/
- https://opentelemetry.io/docs/specs/otel/trace/api/
