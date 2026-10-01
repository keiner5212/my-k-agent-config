---
name: testing-patterns
description: >-
  Design high-signal deterministic tests across unit, integration, contract, and
  end-to-end layers while diagnosing flakes and protecting critical behavior.
---

# Testing Patterns

## Expert mental model
- Test suite is a decision system: fast trustworthy failure must guide engineering action.
- Test behavior at cheapest layer that can falsify relevant risk.
- Unit tests prove logic; integration tests prove boundary truth; end-to-end tests prove user journeys.
- Pyramid is cost model, not fixed ratio. Risk and architecture determine distribution.
- A mock proves what test author expected to call, not necessarily what system should do.
- Flake is nondeterminism. Retrying hides signal and adds queue time until root cause is fixed.

## Test strategy framework
- Pure domain rule or parser -> unit test.
- Database mapping, transaction, queue, serializer, HTTP client -> integration test.
- Consumer-provider compatibility across deploy boundary -> contract test.
- Login, checkout, destructive action, and critical revenue flow -> end-to-end test.
- Security authorization needs integration at real enforcement boundary.
- Do not duplicate every branch at every layer. Each test level covers distinct confidence.
- High-risk behavior deserves redundancy across logic and boundary layers.
- Low-risk view detail needs focused component test, not browser suite.

## What to mock
- Mock external collaborator only when it is outside unit and real use would be slow, nondeterministic, costly, or unavailable.
- Prefer fake for stable in-memory contract with meaningful behavior.
- Prefer stub for fixed dependency response needed to drive one scenario.
- Mock interaction only when invocation itself is behavior: audit event, payment request, message publish.
- Use real database or container for SQL, migration, lock, collation, and transaction behavior.
- Use real HTTP serialization layer when headers, encoding, status, or middleware matter.
- Do not mock framework internals, ORM query builder, or component private state.
- Mock time, random, UUID, clock, and network at a controllable boundary.

## Determinism rules
- Test owns its data, time, random seed, environment, and external state.
- Use fixed clock or injected time. Never assert current wall time with tolerance unless behavior requires it.
- Use deterministic ID generator in unit test; test production CSPRNG only through invariants.
- Wait on observed state, event, network completion, or locator condition, never arbitrary sleep.
- Test must run alone, in random order, and in parallel without shared mutable fixture.
- Reset database by transaction, schema, or unique namespace per test worker.
- Pin test service versions and make external API sandbox response explicit.

## Assertion craft
- Name test as condition and outcome: `rejects_expired_invite_without_creating_member`.
- Arrange minimum data that communicates rule; hide noisy construction in readable builder.
- Assert externally visible result, persisted state, emitted event, or side effect.
- Assert one behavior cluster. Multiple assertions are correct when they jointly define one contract.
- Avoid asserting irrelevant complete object when only three fields matter.
- Match errors by typed code or stable category, not fragile prose.
- Snapshot only stable meaningful rendering or structured output; review every diff semantically.
- Failure output should state expected behavior, actual result, and relevant input.

## Fixtures and builders
- Local literal is best for small scenario.
- Builder helps only when it makes valid defaults clear and overrides obvious.
- Factory that creates hidden database graph makes tests hard to reason about.
- Shared fixture must be immutable and domain-stable; otherwise keep it local.
- Do not use production dump as fixture without minimizing and sanitizing it.
- Explicitly name default assumptions such as currency, tenant, timezone, and role.
- Builder should reject invalid combination if test does not intentionally need invalid object.

## Contract tests
- Provider contract verifies actual response shape, status, errors, and semantics.
- Consumer contract captures client expectation without requiring shared test environment.
- Contract includes headers, auth behavior, nullable fields, pagination, error code, and version.
- Consumer-driven contracts help independently deployed teams, but do not replace integration tests.
- Generate examples from schema only if schema is implementation-validated.
- Treat OpenAPI or protobuf breaking diff as contract failure before release.

## End-to-end test discipline
- Cover few highest-value workflows and production integration seams.
- Use stable test accounts, isolated tenancy, deterministic cleanup, and visible diagnostics.
- Assert user outcome, not implementation URL or CSS selector when semantic role exists.
- Capture trace, video, screenshot, request logs, and console errors on failure.
- Parallelize by isolated data namespace, not shared global teardown.
- Avoid asserting third-party UI behavior; stub upstream only at boundary if allowed.

## Flake taxonomy
- Timing: race, missing await, asynchronous UI transition, eventual consistency.
- Shared state: reused account, database residue, global config, cache, port collision.
- Data: clock edge, locale, timezone, randomized order, stale fixture.
- Environment: CPU starvation, browser variance, network, third-party rate limit.
- Product bug: legitimate nondeterministic behavior, lost event, unhandled concurrency.
- Test bug: weak selector, arbitrary sleep, broad assertion, leaky cleanup.

## Flake investigation
1. Preserve failure artifacts and seed before retrying.
2. Classify by timing, state, data, environment, product, or test defect.
3. Reproduce with repetition and reduced concurrency where useful.
4. Replace sleep with observable readiness condition.
5. Isolate shared resource or inject deterministic collaborator.
6. Keep retry only for documented external transient, with visible failure rate.
7. Track flake rate until it reaches zero over meaningful runs.

## Coverage interpretation
- Line coverage finds unexecuted code, not missing behavior or assertions.
- Branch coverage helps reveal untested decisions but can be gamed.
- Mutation testing measures whether tests notice small behavior changes; use selectively on critical logic.
- Risk coverage asks whether failure modes, boundaries, authorization, and recovery are exercised.
- A high coverage suite with excessive mocks can miss every integration defect.

## Implementation recipes
### Regression bug test
1. Reproduce reported input and prior bad outcome at smallest useful layer.
2. Assert desired observable behavior.
3. Ensure test fails against pre-fix code when practical.
4. Implement fix.
5. Keep test focused on bug contract, not incidental implementation.

### HTTP integration test
1. Start application with production-like routing and serializer.
2. Seed isolated database state.
3. Send real HTTP request with auth context.
4. Assert status, problem error or response body, persisted state, and emitted message.
5. Clean through transaction or namespace.

## Failure modes juniors miss
- Test passes because mock accepts invalid request real API rejects.
- E2E test has hidden order dependency through reused account.
- Snapshot blesses broken accessibility or empty list state.
- Unit test covers branch but ignores transaction or serialization failure.
- Test clock differs from database clock at midnight or DST boundary.
- Retried flaky test masks real duplicate charge or race.
- Assertion checks call count but not customer-visible outcome.
- Shared fixture change breaks unrelated tests and obscures causal behavior.

## Critique rubric
- Test layer matches risk and real boundary.
- Scenario names contract and produces actionable failure message.
- Test controls time, random, state, I/O, and cleanup.
- Mocks model only necessary external boundary and do not duplicate implementation.
- Integration tests use real components where protocol or persistence semantics matter.
- Contract covers compatibility surface and version behavior.
- E2E suite stays small, critical, isolated, and diagnostic.
- Flakes are classified and fixed at source, never normalized by sleep or blind retry.

## Authoritative references
- https://martinfowler.com/articles/practical-test-pyramid.html
- https://testing-library.com/docs/guiding-principles
- https://playwright.dev/docs/best-practices
- https://pact.io/
- https://martinfowler.com/bliki/UnitTest.html
