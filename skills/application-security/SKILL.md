---
name: application-security
description: >-
  Assess application attack surface, trust boundaries, identity, authorization,
  injection, browser controls, SSRF, secrets, and evidence-based risk.
---

# Application Security

## Expert mental model
- Security failure occurs where untrusted input crosses privilege, data, or execution boundary.
- Authentication establishes claimed identity. Authorization decides permitted action on a specific resource.
- A control is real only when server-enforced, correctly scoped, observable, and failure-tested.
- Defense in depth reduces blast radius; no client, network, or UI control replaces server authorization.
- Threat likelihood depends on reachability, preconditions, attacker capability, and exploit reliability.
- Impact depends on confidentiality, integrity, availability, blast radius, recovery cost, and regulatory duty.

## Security discovery
- Map actors, credentials, roles, tenants, admin paths, and service identities.
- Inventory sensitive assets: PII, payment state, tokens, secrets, files, source, availability.
- Mark each trust boundary: browser to API, service to service, queue, worker, cloud metadata, third party.
- List entry points: HTTP, GraphQL, upload, webhook, queue, CLI, import, redirect, URL fetch.
- Identify privileged actions, object ownership rules, and state transitions.
- Record external dependencies, egress paths, logging sinks, and secret stores.

## Authentication versus authorization
- Authentication answers who or what presented valid credential.
- Authorization answers whether authenticated principal may perform this action on this object now.
- Authenticate at entry boundary; authorize at every sensitive business operation.
- Role check alone is insufficient when resource owner, tenant, status, or delegation matters.
- Use deny-by-default policy and explicit grants.
- Never trust client role, hidden field, URL ID, request header, or JWT claim without server validation.
- Log authorization denial with safe actor, target class, operation, and reason code.

## Session versus JWT
- Server session supports immediate revocation, small browser token, and centrally enforced idle policy.
- JWT reduces lookup for distributed verification but makes revocation, claim staleness, and key rotation harder.
- Use short-lived access tokens with audience, issuer, expiry, nonce or subject as needed.
- Do not put secrets or mutable authorization truth in JWT because it is readable and stale until expiry.
- Browser cookie session needs `Secure`, `HttpOnly`, `SameSite`, narrow Path, and correct Domain.
- Cookie-authenticated state change needs CSRF protection despite SameSite defense.
- Token in browser storage increases XSS extraction risk; choose architecture explicitly.

## Authorization implementation
- Central policy receives actor, action, resource, tenant, and relevant state.
- Load resource through authorized query where possible, not lookup then optional check.
- Enforce tenant condition in every data access path, including export, worker, admin, and cache.
- Use capability or signed download URL with narrow resource, action, expiry, and revocation policy.
- Check state transitions server-side: user cannot cancel shipped order by changing status parameter.
- Test horizontal IDOR, vertical privilege escalation, cross-tenant access, and bulk endpoint bypass.

## Injection classes
- SQL: parameterize values; allowlist identifiers, direction, and operators.
- Command: fixed executable and argument array; never concatenate shell fragment.
- XSS: use framework auto-escaping plus context-specific encoding; avoid unsafe HTML sinks.
- Template: render data through escaping engine; do not evaluate user template.
- LDAP, NoSQL, XPath, regex, and log injection need structured APIs and bounded input.
- Path traversal: canonicalize under fixed root, reject escape and unsafe symlink behavior.
- Deserialize only safe formats with strict schema and allowlisted types.

## Browser controls
- CSP limits executable script and load sources; use nonce or hash for unavoidable inline script.
- Start CSP in report-only mode, fix violations, then enforce restrictive policy.
- HSTS requires HTTPS readiness including subdomains if configured.
- CORS is browser read policy, not API authorization.
- Never use wildcard origin with credentials.
- Allow only required origins, methods, headers, and exposed headers.
- Clickjacking defense uses `frame-ancestors` CSP or `X-Frame-Options` compatibility header.
- Set `Referrer-Policy` to avoid leaking sensitive URL path or query.

## SSRF
- SSRF occurs when attacker controls server-side URL, host, redirect, DNS, or protocol target.
- Parse URL with standard parser and allowlist scheme, hostname, port, and path where possible.
- Resolve DNS then block loopback, link-local, private, multicast, metadata, and reserved ranges.
- Revalidate each redirect target; redirects can cross trust boundary.
- Disable unsupported schemes, proxy inheritance, credential forwarding, and file URLs.
- Apply short timeout, response-size cap, method restriction, and egress firewall.
- Do not trust hostname suffix checks alone; DNS rebinding and alternate IP notation exist.

## Secrets and sensitive data
- Store runtime secrets in managed secret system or environment injection with access policy.
- Separate keys and credentials by environment, service, and purpose.
- Rotate on exposure, employee change, scheduled lifecycle, and provider incident.
- Redact authorization headers, cookies, tokens, passwords, private keys, and sensitive PII in logs.
- Inventory where secret can leak: source, history, CI, build artifact, crash report, analytics, browser bundle.
- Encrypt data at rest where threat model requires it; access controls remain necessary.

## Severity framework
- Critical: reliable remote compromise, broad unauthorized sensitive data, or high-impact integrity loss with low preconditions.
- High: serious unauthorized access or integrity impact requiring limited preconditions.
- Medium: meaningful impact limited by authorization, complexity, or narrow scope.
- Low: defense-in-depth weakness or minor exposure without plausible material impact.
- Report reachability, attacker access, exploit steps, affected scope, impact, and confidence.
- CVSS informs but does not replace business impact and environmental context.

## Audit workflow
1. Define authorized scope, assets, users, and threat model.
2. Trace entry point to authorization, storage, side effect, and log path.
3. Probe access-control matrix with changed IDs, roles, tenant, method, and channel.
4. Review untrusted input sinks and outbound requests.
5. Inspect configuration, headers, dependencies, secrets, and observability.
6. Validate exploit safely with minimal access.
7. Report evidence, severity rationale, remediation, and verification.

## Failure modes juniors miss
- UI hides button but direct API still accepts action.
- Middleware authenticates route but worker or resolver skips object-level authorization.
- JWT role remains valid after employee role removal.
- CORS allowlist treats `example.com.attacker.test` as trusted by suffix bug.
- HTML sanitizer used in JavaScript URL or CSS context.
- SSRF blocklist misses redirect, IPv6, DNS rebinding, or metadata alias.
- Cache key lacks tenant and exposes one account's response to another.
- Error response differs for existing versus forbidden object and enables enumeration.

## Critique rubric
- Trust boundaries, identities, assets, and privileged flows are explicit.
- Authentication choice includes session, token lifetime, revocation, and browser threat tradeoff.
- Authorization occurs on server for every object, action, and tenant boundary.
- Input-to-sink paths use correct context-specific defenses.
- Browser controls are scoped and tested, not copied headers.
- SSRF design controls URL parsing, DNS, redirects, egress, timeout, and size.
- Secrets have storage, access, rotation, redaction, and exposure response.
- Findings contain evidence, reproducibility, impact, and a verifiable fix.

## Authoritative references
- https://cheatsheetseries.owasp.org/
- https://owasp.org/Top10/
- https://cwe.mitre.org/
- https://www.rfc-editor.org/rfc/rfc9700
- https://www.w3.org/TR/CSP3/

## Review evidence requirements
- Show request and authorization path for one allowed and one denied object access.
- Show token or session lifetime, storage, rotation, and revocation behavior.
- Show query or command sink receives structured parameters, not concatenated user input.
- Show CSP and CORS response headers from production-like deployment.
- Show SSRF policy handles redirect, DNS result, private range, and response size.
- Show secrets are absent from repository, build output, client bundle, and logs.
- Show audit event exists for privileged action and does not expose sensitive payload.
- Show critical control fails closed when policy service, cache, or dependency is unavailable.
- Show least-privilege identity permissions for service, database, storage, and CI.
- Show security finding retest blocks original path and parallel transport path.
