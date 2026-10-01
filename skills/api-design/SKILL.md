---
name: api-design
description: >-
  Design durable REST, GraphQL, gRPC, and webhook contracts with compatibility,
  error, pagination, idempotency, security, and documentation discipline.
---

# API Design

## Expert mental model
- API is a long-lived promise to independently deployed clients.
- Optimize for caller intent, consistency, and migration cost, not database shape.
- Every exposed field, enum value, error code, and ordering becomes compatibility surface.
- Transport success does not mean business success; contract must express both.
- Design for retries, partial failure, stale reads, and delayed asynchronous completion.
- OpenAPI or protobuf is executable contract, not documentation generated after the fact.

## Contract discovery
- Who calls this: first-party UI, mobile, partner, server, script, or webhook consumer?
- What authorization, tenancy, and data minimization rules apply?
- Is operation read, state transition, command, long-running job, or event notification?
- Does caller need exact current data, eventually consistent view, or job receipt?
- Will caller retry? Can network duplicate request? How long can response be cached?
- Which fields are stable business concepts versus implementation details?

## Resource modeling
- Model durable business nouns: `/orders`, `/customers`, `/invoices`.
- Collection path names are plural lowercase nouns; avoid verbs for ordinary CRUD.
- Use subresources only when lifecycle depends on parent: `/orders/{id}/items`.
- Use explicit action endpoint for non-CRUD command: `/orders/{id}:cancel`.
- An action needs clear state precondition, authorization, idempotency, and result model.
- Do not expose table schema blindly. API representation is client-oriented projection.
- Use immutable external ID when possible; never leak sequential IDs as authorization control.
- Represent relationships by link or ID according to client need and expansion cost.

## HTTP semantics
- GET, HEAD, OPTIONS are safe. GET must never cause business mutation.
- PUT replaces known representation and is idempotent when contract supports full replacement.
- PATCH applies partial change; define JSON Patch or merge semantics explicitly.
- POST creates under collection or invokes non-idempotent command.
- DELETE is idempotent at resource level; repeated delete can return 204 or documented 404.
- 201 means resource created. Include `Location` when meaningful.
- 202 means accepted for async work. Return job resource and polling or callback plan.
- 409 means current state conflicts. 422 means valid syntax but domain validation fails.
- 429 includes `Retry-After`; 503 is temporary service unavailability, not validation error.

## Error contract: RFC 7807
- Return `application/problem+json` for API failures.
- Include stable `type` URI or code, `title`, HTTP `status`, and safe `detail`.
- Add `instance` or request correlation ID for support lookup.
- Field validation uses machine-readable field path, rule code, and message.
- Never depend on display message for program behavior; clients use stable type or code.
- Do not expose SQL, stack trace, internal IDs, secrets, or authorization existence information.
- Document each error with condition, status, retryability, and recovery action.

## Pagination and filtering
- Cursor pagination for large or high-churn collection.
- Cursor encodes last sort values and tie-breaker, is opaque and signed or validated.
- Stable ordering includes unique tie-breaker: `created_at DESC, id DESC`.
- Cursor invalidation rules must be documented for changed filters or expired cursors.
- `limit` has default and maximum, commonly 20 to 100 depending on item weight.
- Offset suits small, static, administrative lists; it slows deeply and shifts under inserts.
- Filtering parameters use explicit allowlist, types, operators, and repeated-value semantics.
- Return `next_cursor` only when another page exists; avoid total count unless cost is acceptable.

## Versioning framework
- Add optional response field safely. Clients must ignore unknown fields.
- Add optional request field with server default safely.
- Enum expansion is breaking for clients that assume exhaustive values.
- Rename, remove, change type, change unit, alter default, or tighten validation is breaking.
- Prefer expand-contract: add new field, dual-write or derive, migrate clients, deprecate, remove later.
- URI versioning makes breaking boundary visible but doubles maintenance.
- Header or media-type versioning is precise but harder for tooling and proxies.
- Avoid versioning for additive changes; version only when compatibility cannot be preserved.

## Idempotency
- Require `Idempotency-Key` for payment, provisioning, external POST, and retryable command.
- Scope uniqueness by account or tenant plus operation.
- Store normalized payload hash and completed status/result for retry window.
- Same key plus same input returns same logical result.
- Same key plus differing input returns 409 or documented idempotency mismatch.
- In-progress duplicate returns existing operation status or waits bounded time.
- Idempotency is still needed behind a proxy even if client "does not retry".

## Webhooks
- Webhook is at-least-once asynchronous message to untrusted receiver.
- Include immutable event ID, type, occurred timestamp, schema version, data, and delivery ID.
- Sign raw body with HMAC or asymmetric signature; specify algorithm and key rotation.
- Use timestamp plus bounded replay window, commonly 5 minutes, where protocol permits.
- Receiver verifies signature before parsing or processing body.
- Retry on network failure and 5xx with exponential backoff; do not retry normal 2xx.
- Consumer deduplicates event ID and tolerates out-of-order delivery.
- Publish event reference or minimal data when sensitive payload makes replay risky.

## REST, GraphQL, and gRPC
- REST fits cacheable resources, broad client interoperability, HTTP tooling, and simple reads.
- GraphQL fits varied client read shapes and graph traversal with strong query governance.
- GraphQL requires depth, complexity, persisted-query, authorization-per-resolver, and N+1 controls.
- gRPC fits internal low-latency typed service calls and streaming, not general browser compatibility.
- Do not choose GraphQL to avoid resource design or gRPC to hide unstable domain model.
- Separate public API stability needs from internal service convenience.

## OpenAPI contract practice
- Write schema for request, success, errors, headers, and examples before implementation.
- Mark required versus nullable correctly; absent and null mean different things.
- Constrain strings with format, length, pattern only when validation is real.
- Define enums carefully and document unknown-value behavior for generated clients.
- Reuse components for shared domain shapes, not vague generic envelopes.
- Validate implementation and examples against specification in CI.
- Diff specs for breaking change detection before release.

## Implementation recipes
### Create async export
1. POST `/exports` with validated request and idempotency key.
2. Return 202 plus `export` job ID and status URL.
3. Worker transitions job through explicit states.
4. GET job returns pending, completed result link, or safe failure problem.
5. Optional webhook emits signed `export.completed` event.

### Cursor list
1. Allowlist filter and sort options.
2. Decode opaque cursor matching exact query shape.
3. Query indexed tuple greater or less than cursor values.
4. Fetch `limit + 1`.
5. Return first `limit` and encode next tuple if extra exists.

## Failure modes juniors miss
- API returns database fields whose semantics later need change.
- 200 with `{ error: ... }` defeats proxies, tooling, and clients.
- Offset causes duplicate or missing items during active insertion.
- Idempotency key in cache only fails after restart or concurrent requests.
- Webhook signature verifies parsed JSON rather than original raw bytes.
- GraphQL query flexibility becomes denial of service without complexity limits.
- Adding enum value breaks generated clients with exhaustive switch.
- OpenAPI says nullable while server rejects null, making generated SDK wrong.

## Critique rubric
- Resource and action names map to business intent, not storage implementation.
- Method, status, preconditions, and async semantics match HTTP contract.
- Schema is explicit about required, nullable, defaults, bounds, and compatibility.
- Errors follow RFC 7807 with stable machine codes and safe diagnostics.
- Lists have bounded limits, deterministic order, and cursor strategy when needed.
- Mutations state replay behavior and idempotency retention.
- Webhooks document signature, retries, duplicate, order, and version behavior.
- OpenAPI or protobuf is complete, reviewed, example-backed, and implementation-validated.

## Authoritative references
- https://www.rfc-editor.org/rfc/rfc9110
- https://www.rfc-editor.org/rfc/rfc9457
- https://spec.openapis.org/oas/latest.html
- https://google.aip.dev/
- https://graphql.org/learn/
- https://protobuf.dev/programming-guides/proto3/
