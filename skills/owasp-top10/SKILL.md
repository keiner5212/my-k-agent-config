---
name: owasp-top10
description: >-
  Audit OWASP Top 10 categories through attack evidence, concrete remediation,
  verification, and prioritized risk rather than generic scanner checklists.
---

# OWASP Top 10

## Expert mental model
- OWASP Top 10 is a taxonomy for common web risk, not a complete security program.
- A category is not a confirmed finding. Confirm reachability, exploit path, impact, and scope.
- Fix root control failure across all equivalent paths, not only reported endpoint.
- A secure change requires negative verification proving exact attack path is blocked.
- Map category to CWE when useful, but describe product-specific risk in plain terms.

## Audit workflow
1. Define authorized scope, identities, assets, environments, and application entry points.
2. Trace sensitive operations through route, resolver, worker, store, cache, and third party.
3. Check each category using code, configuration, dependency, and runtime evidence.
4. Validate exploit safely using minimum data and lowest privilege.
5. Rate likelihood and impact with written rationale.
6. Remediate root cause and re-test original plus equivalent paths.
7. Record residual risk, owner, and monitoring where full remediation is deferred.

## Severity evidence
- Critical: low-precondition remote compromise, broad data exposure, or destructive integrity loss.
- High: material unauthorized access or control with limited preconditions.
- Medium: meaningful impact constrained by role, complexity, or narrow blast radius.
- Low: defense-in-depth gap with low realistic impact.
- Report attack preconditions, proof, affected asset, affected users, exploit reliability, and recovery cost.
- Scanner output without reachability is triage signal, not severity evidence.

## A01 Broken Access Control
- Detect: modify object ID, tenant ID, role, method, batch input, export filter, or worker message.
- Detect: hidden UI control works through direct API or alternate GraphQL resolver.
- Detect: cache key or signed URL lacks tenant, user, object, action, or expiry scope.
- Remediate: deny by default and authorize server-side for actor, action, object, tenant, and state.
- Remediate: query resources through authorization scope instead of load-then-optional-check.
- Remediate: enforce limits on bulk operation, pagination, export, and admin endpoints.
- Verify: cross-tenant, horizontal IDOR, vertical privilege, direct route, job, and cache tests fail closed.

## A02 Cryptographic Failures
- Detect: plaintext sensitive data, weak or custom crypto, missing TLS validation, secret in client or log.
- Detect: password fast hash, AEAD nonce reuse risk, hard-coded key, missing key ID or rotation.
- Remediate: TLS 1.3 where possible; AES-GCM or ChaCha20-Poly1305; Argon2id for passwords.
- Remediate: managed key storage, purpose separation, CSPRNG, key rotation, redaction, and data minimization.
- Verify: altered ciphertext rejected, expired or wrong key rejected, rotation reads old data, secrets absent from artifacts.

## A03 Injection
- Detect: untrusted string reaches SQL, shell, template, HTML, path, LDAP, NoSQL, regex, or log sink.
- Detect: dynamic sort, filter, column, command, URL, or template fragment built by concatenation.
- Remediate: parameterized query APIs, fixed process arguments, allowlisted structure, context-specific output encoding.
- Remediate: strict schema, size limits, safe parser, and no user-controlled interpreter invocation.
- Verify: payload remains data, not executable syntax, across every context and alternate encoding.

## A04 Insecure Design
- Detect: no abuse case for money, privilege, recovery, quota, state transition, or multi-tenant feature.
- Detect: client enforces invariant, retry duplicates action, or product has no fraud or resource policy.
- Remediate: threat model data flows, define state machine, transaction invariant, idempotency, quota, and safe failure.
- Remediate: add business rule tests and operational detection before launch.
- Verify: replay, race, state bypass, quota exhaustion, and privilege abuse scenarios are rejected or contained.

## A05 Security Misconfiguration
- Detect: debug mode, default credentials, open storage, broad IAM, directory listing, verbose errors, old protocol.
- Detect: permissive CORS, missing HSTS/CSP where applicable, exposed admin, unpatched runtime, unsafe feature flag.
- Remediate: hardened baseline config, least privilege, environment separation, secure headers, deployment review.
- Remediate: configuration as code with drift detection and secret injection.
- Verify: external scan and authenticated configuration check match intended exposure.

## A06 Vulnerable and Outdated Components
- Detect: unsupported runtime, unpatched direct or transitive dependency, malicious package, unpinned build action.
- Detect: vulnerable feature is reachable through application runtime path.
- Remediate: update, replace, remove, or disable reachable component; pin version and verify provenance.
- Remediate: maintain SBOM, dependency inventory, patch SLA, and exception process.
- Verify: lockfile resolves safe version, affected path regression check passes, scanner finding is closed with evidence.

## A07 Identification and Authentication Failures
- Detect: account enumeration, weak reset, credential stuffing exposure, session fixation, missing MFA for sensitive change.
- Detect: long-lived token, missing audience/issuer validation, token in URL or local storage without XSS tradeoff.
- Remediate: rate limit, generic response, MFA, secure password recovery, session rotation, short token lifetime.
- Remediate: secure cookie flags, CSRF defense for cookie auth, server revocation and device/session management.
- Verify: enumeration, replay, fixation, expired token, revoked session, and brute-force controls behave as designed.

## A08 Software and Data Integrity Failures
- Detect: unverified update, CI artifact, package, webhook, deserialization, or configuration import.
- Detect: pipeline trusts mutable tag, unsigned release, arbitrary class type, or untrusted build output.
- Remediate: signature or checksum verification, provenance, immutable pin, protected CI, strict schema, type allowlist.
- Remediate: transactional outbox/inbox for state integrity where asynchronous events affect business data.
- Verify: altered artifact, changed signature, replayed event, unknown type, and compromised dependency path are rejected.

## A09 Security Logging and Monitoring Failures
- Detect: missing auth, permission, secret, integrity, admin, and suspicious-input events.
- Detect: logs leak credentials, lack correlation, can be modified, have no alert or retention path.
- Remediate: structured security event with actor, action, target, outcome, timestamp, correlation ID, safe reason.
- Remediate: protect access and integrity, centralize retention, alert on actionable pattern, define response runbook.
- Verify: simulated denial, privilege change, suspicious upload, and secret access produce useful safe alert.

## A10 Server-Side Request Forgery
- Detect: user controls URL, hostname, redirect, webhook callback, image fetch, import, proxy, or metadata request.
- Detect: only hostname string check exists; private IP, IPv6, DNS rebinding, redirect, protocol remain unrestricted.
- Remediate: allowlist scheme, host, port, path; resolve and block private, loopback, link-local, metadata ranges.
- Remediate: revalidate redirects; disable unnecessary protocols; enforce egress firewall, timeout, size, method limits.
- Verify: loopback, metadata, private IP, redirect chain, DNS change, IPv6 literal, and alternate encoding are blocked.

## Cross-category failure modes
- Client-side control is assumed equivalent to server enforcement.
- Fix protects web route but GraphQL, batch, worker, CLI, or admin route remains vulnerable.
- Generic sanitizer is applied to unrelated contexts.
- Security header is copied without matching browser feature or deployment flow.
- Dependency finding is dismissed without checking runtime reachability.
- Logs become new sensitive-data store while solving observability.
- Threat is marked fixed after code merge without configuration or end-to-end verification.

## Critique rubric
- Finding has exact category, CWE where known, location, evidence, scope, and severity rationale.
- Exploit path and business impact are demonstrated without unnecessary sensitive access.
- Remediation removes root cause and covers equivalent pathways.
- Fix includes least privilege, safe failure, and operational monitoring where relevant.
- Negative verification reproduces prior attack and confirms rejection.
- Dependencies, configuration, code, and runtime behavior are all considered.
- Accepted risk has accountable owner, residual impact, expiry, and review trigger.

## Authoritative references
- https://owasp.org/Top10/
- https://owasp.org/www-project-web-security-testing-guide/
- https://cheatsheetseries.owasp.org/
- https://cwe.mitre.org/
- https://owasp.org/ASVS/

## Release gate evidence
- Access control tests cover role, owner, tenant, direct object ID, and alternate endpoint.
- Encryption, password, token, and key settings meet approved platform standard.
- Injection tests exercise each interpreter and dynamic query structure in changed code.
- Threat model exists for new sensitive flow, state machine, external integration, or costly operation.
- Production configuration review covers debug mode, IAM, storage, headers, CORS, and secrets.
- Dependency and runtime inventory identifies reachable known critical exposure.
- Authentication tests cover login, recovery, MFA, rate limit, session, and token revocation.
- Artifact, webhook, event, and deserialization paths have integrity verification.
- Security event telemetry is safely emitted, retained, alertable, and tied to response owner.
- URL-fetching capability has allowlist, egress, redirect, DNS, timeout, and size enforcement.
- Release owner records exceptions with residual impact, expiry, and compensating control.
