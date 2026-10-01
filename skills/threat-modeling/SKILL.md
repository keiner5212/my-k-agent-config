---
name: threat-modeling
description: >-
  Build practical threat models from real data flows, trust boundaries, STRIDE,
  abuse cases, explicit risk decisions, controls, owners, and verification.
---

# Threat Modeling

## Expert mental model
- Threat model is a structured design review for how system can fail under hostile action.
- Diagram is input, not output. Value is prioritized threats tied to implementable controls.
- Security boundary changes when identity, privilege, network reachability, data classification, or administrative control changes.
- Threat is attacker action plus target, precondition, outcome, and realistic impact.
- Control exists only when implemented, owned, observable, and verified against threat.
- Model enough to guide decisions; exhaustive imagination without ranking is not useful.

## Scope before diagram
- Define feature, environment, release decision, and time horizon.
- Identify assets: credentials, PII, money, tenant isolation, source, configuration, uptime, audit evidence.
- Define security objectives: prevent unauthorized transfer, protect confidential document, retain auditability, resist abuse.
- Name actors: anonymous user, member, admin, support, service account, CI, vendor, attacker, insider.
- Record assumptions and dependencies that are outside scope but security-relevant.
- State attacker capability: internet user, authenticated low-privilege user, compromised partner, network attacker, malicious insider.

## DFD construction
- Draw external entities, processes, data stores, queues, object stores, and third-party services.
- Add directional labeled flows showing data and protocol, not just arrows.
- Include browser, admin portal, CLI, background worker, scheduled task, observability, CI, and cloud metadata where present.
- Mark trust boundary around each independent identity, network zone, tenant, process, cloud account, and vendor.
- Annotate flow with sensitive data, auth credential, encryption, and operation if it affects threat.
- Keep diagram at one feature level. Split overloaded architecture into smaller DFDs.
- Validate DFD with engineers and operator who know actual deployment.

## STRIDE per element
- Spoofing: attacker claims user, service, device, webhook, or key identity.
- Tampering: attacker alters request, event, database row, artifact, config, or cache value.
- Repudiation: actor denies action because event has weak identity, time, integrity, or retention.
- Information disclosure: unauthorized read through IDOR, cache, log, backup, error, response, or side channel.
- Denial of service: resource exhaustion, retry amplification, fanout, expensive query, queue poison, quota bypass.
- Elevation of privilege: low role invokes admin action, crosses tenant, or abuses confused deputy.
- Apply STRIDE to process, store, flow, and boundary; not every category applies equally.

## Abuse case method
1. Write attacker goal in concrete product terms.
2. Name entry point and trust boundary crossed.
3. List capability and preconditions.
4. Describe attack steps without unnecessary exploit detail.
5. State outcome, affected assets, blast radius, and detection opportunity.
6. Map prevention, detection, response, and recovery control.
7. Define verification evidence before closing threat.

## Common abuse prompts
- Can untrusted caller select another tenant, object ID, role, or operation?
- Can a retry, race, or reordered event cause duplicate state transition?
- Can user-supplied URL reach internal service or cloud metadata?
- Can upload become executable, overwrite another file, or exhaust parser?
- Can token be replayed, confused across audience, or remain valid after privilege removal?
- Can cache, export, batch path, or worker bypass interactive authorization?
- Can expensive filter, nested query, or queued job consume unbounded resource?
- Can log, error, analytics, or backup expose secret or sensitive data?

## Risk ranking
- Likelihood considers reachability, required access, complexity, exploit reliability, and attacker incentive.
- Impact considers confidentiality, integrity, availability, blast radius, detection delay, recovery cost, and legal duty.
- Use simple 1 to 5 scale only if each rating has written rationale.
- Risk is not severity label alone; record evidence and residual risk after control.
- Prioritize high impact plus plausible path before low-probability theoretical weakness.
- Do not treat CVSS as replacement for product and environment context.

## Control selection
- Prefer removing dangerous capability or narrowing trust boundary over detecting misuse later.
- Preventive: authz, validation, signature, isolation, rate limit, least privilege.
- Detective: audit event, anomaly alert, integrity check, access log.
- Responsive: revoke token, disable integration, quarantine upload, kill job, rotate key.
- Recovery: backup restore, compensating ledger, replay safe event, customer notification.
- One control can fail. High-value flows need independent layers.
- Assign each control owner, delivery point, verification, and operations runbook.

## When to stop
- Stop when all high-risk data flows and privileged operations have concrete threats and controls.
- Stop when residual high risks are accepted by accountable owner with review date.
- Do not enumerate every impossible combination once incremental insight is low.
- Revisit on new identity model, external integration, sensitive data class, privilege, protocol, or deployment boundary.
- Refresh after material incident or architecture change, not only annual compliance calendar.

## Implementation recipes
### Tenant export feature
1. Draw browser, export API, job queue, worker, object store, notification service.
2. Mark tenant and service identity boundaries.
3. Model IDOR, export authorization drift, signed URL leakage, queue replay, object-store prefix escape, DoS.
4. Add tenant-scoped authorized query, immutable job actor context, short signed URL, inbox idempotency, quotas.
5. Verify with cross-tenant, expired URL, replay, and oversized-export tests.

### Webhook receiver
1. Map public endpoint, signature verifier, parser, queue, worker, database.
2. Model spoofing, replay, parser DoS, event tampering, duplicate delivery, logging disclosure.
3. Add raw-body signature, timestamp window, size cap, strict schema, inbox, redaction, alert.
4. Verify altered body, old timestamp, duplicate event, and malformed large payload.

## Failure modes juniors miss
- DFD omits background job or support tool where authorization actually differs.
- Team models encryption but not who can invoke sensitive action.
- "Internal" network assumed trusted despite SSRF or compromised service.
- Mitigation says "use JWT" without audience, rotation, revocation, or authorization rule.
- Rate limit exists globally but expensive endpoint still starves other customers.
- Planned control is marked complete before test or monitoring proves it.
- Risk accepted without owner, expiry, or residual impact.
- Threat model lists controls without attacker goal or evidence of coverage.

## Critique rubric
- Scope, assets, objectives, attacker, assumptions, and exclusions are explicit.
- DFD reflects deployed system, sensitive flows, and real trust boundaries.
- STRIDE produces concrete abuse cases, not generic category labels.
- Each threat has precondition, impact, likelihood rationale, and affected asset.
- Control states prevention, detection, response, owner, and verification.
- Residual and accepted risk are distinct from mitigated risk.
- Model stops at decision usefulness and includes trigger for refresh.
- Engineering work items trace back to threat IDs and closure evidence.

## Authoritative references
- https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html
- https://www.threatmodelingmanifesto.org/
- https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats
- https://owasp.org/www-community/Threat_Modeling

## Threat model record template
- Threat ID: stable identifier for tracking.
- Asset: confidential data, integrity rule, capability, or availability target harmed.
- DFD element: process, flow, store, actor, or boundary where threat occurs.
- STRIDE class: primary category, with secondary category if it affects mitigation.
- Attacker: capability, access level, and incentive.
- Preconditions: what must be true before exploitation.
- Attack path: short sequence from entry to harmful outcome.
- Impact: confidentiality, integrity, availability, legal, and operational effect.
- Likelihood: rated with reachability, complexity, and reliability evidence.
- Existing controls: implemented safeguards and their known limitations.
- Proposed control: prevention, detection, response, or recovery action.
- Owner: accountable team or role.
- Verification: test, configuration proof, code review, or monitoring evidence.
- Residual risk: remaining impact after control and rationale for acceptance.
- Review trigger: release, integration, architecture change, incident, or expiry date.
