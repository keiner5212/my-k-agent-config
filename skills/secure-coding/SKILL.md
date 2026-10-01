---
name: secure-coding
description: >-
  Implement safe boundaries for untrusted data, resource access, files, secrets,
  cryptography, dependencies, errors, and denial-of-service controls.
---

# Secure Coding

## Expert mental model
- Security bugs are data crossing into authority, interpreter, storage, or network sink without correct control.
- Validate structure at entry, authorize action at use, encode at output context, and bound resource consumption throughout.
- Keep untrusted data structured until a safe library API consumes it.
- Fail closed for validation, integrity, and authorization. Fail safely for availability.
- A secure primitive becomes insecure when wrong key, nonce, scope, lifetime, or error handling surrounds it.

## Boundary inventory
- Inputs: HTTP body, query, header, cookie, path, queue message, file, webhook, CLI, database legacy data.
- Sinks: SQL, shell, template, DOM, browser URL, filesystem, serializer, redirect, HTTP client, log, cache.
- Authority: object access, tenant, role, payment, deletion, admin, secret read, network egress.
- Resource: payload bytes, list length, nesting, regex work, decompression, timeout, retry, concurrency.
- Treat each external service response and database value as untrusted when crossing a security boundary.

## Input validation layers
- Transport validation checks parseable encoding, content type, and size before allocation or parsing.
- Schema validation checks type, required fields, range, enum, format, and maximum length.
- Domain validation checks invariant, ownership, state transition, and cross-field rule.
- Canonicalize before policy when equivalent representations exist, then validate canonical form.
- Allowlist accepted values whenever practical; blocklists miss alternate encoding and novel forms.
- Reject unknown fields for security-sensitive commands when mass assignment is possible.
- Validation error must not reveal hidden resource or policy detail.

## Output handling
- HTML text uses framework escaping.
- HTML attribute uses attribute encoding and quoted attributes.
- JavaScript uses data serialization, never string interpolation into executable code.
- URL uses parsed structured builder with scheme and host policy.
- CSS and SQL need their own APIs; no universal sanitizer exists.
- Redirect target uses allowlisted relative paths or fixed trusted origins.
- Logs use structured fields and redaction, not concatenated untrusted string.

## Database and command safety
- Parameterize every value in SQL, NoSQL query, LDAP query, and search query API.
- Allowlist dynamic identifiers, sort columns, operators, and direction before constructing query structure.
- Use least-privilege database role per service and operation where feasible.
- Fixed executable plus argument vector for process launch; avoid shell entirely.
- If shell is unavoidable, do not interpolate untrusted data and constrain environment, cwd, privilege, and timeout.
- Transaction protects integrity but not authorization; enforce both.
- Avoid `SELECT *` for sensitive tables and filter tenant in query itself.

## Authorization in code
- Load resource with actor and tenant scope, then decide action from current state.
- Check every write and read of sensitive object, including export, batch, job, and cache warm path.
- Do not authorize once then reuse unscoped ID in later asynchronous work.
- Carry stable actor and tenant context into queue messages only when needed; re-authorize worker operation.
- Prevent mass assignment with request DTO or field allowlist separate from persistence model.
- Deny unknown action, unknown role, unknown tenant, and missing ownership by default.

## Files and uploads
- Enforce request and decompressed size before buffering complete file.
- Generate storage name; do not use user filename as path or authorization identity.
- Check extension, declared type, magic bytes, and actual parser result for allowed formats.
- Store outside web root or private object store; serve through authorization-aware download.
- Normalize and resolve path under fixed root; reject traversal and symlink escape.
- Scan or quarantine files according to threat model before making available.
- Do not render user-uploaded HTML, SVG, archive, office macro, or media metadata without isolation.
- Apply archive entry count, path, depth, and decompressed-size limits to prevent zip bombs.

## Serialization and parsing
- Prefer JSON with strict schema to language object deserialization.
- Allowlist message type and version; reject unknown polymorphic type metadata.
- Bound recursion, collection length, string length, and parser work.
- XML parser disables external entity and DTD processing unless explicitly needed and secured.
- Verify signature before trusting webhook event type or payload fields.
- Treat parser exceptions as untrusted-input errors, not application crashes.

## Crypto usage in application code
- Use maintained high-level library APIs, not homegrown primitives.
- CSPRNG generates tokens, salts, nonces, and keys; ordinary PRNG never does.
- Use AEAD for confidential data and verify authentication before use.
- Passwords use Argon2id or approved bcrypt library with unique salt and tuned cost.
- Compare secret values with constant-time library function when comparison is attacker-observable.
- Separate keys by purpose, environment, and tenant when threat model requires it.
- Do not write keys, tokens, plaintext, nonce, ciphertext, or auth failures to logs.

## Dependency hygiene
- Lock direct and transitive versions with reproducible build.
- Inventory dependencies and maintain SBOM when product risk requires it.
- Check advisories for reachability, exploit condition, and affected runtime path.
- Update or remove reachable critical exposure promptly; document accepted residual risk.
- Verify package integrity and registry source; pin CI actions and build tools by immutable revision when possible.
- Avoid abandoned package for auth, crypto, parser, serialization, and privileged boundary.
- Scan secret and dependency changes in CI but treat scanner finding as triage input.

## Error and logging rules
- User response is stable, safe, actionable, and does not reveal stack, query, secret, or permission detail.
- Internal log includes operation, correlation ID, safe subject ID, outcome, and error class.
- Redact token, cookie, password, key, connection URL, sensitive PII, and raw request body by default.
- Log auth failure, privilege change, secret access, policy denial, suspicious upload, and integrity failure.
- Rate-limit repeated logging to avoid attacker-driven cost and log denial of service.
- Protect log access, retention, integrity, and deletion policy.

## Denial-of-service controls
- Cap input bytes, pagination limit, nested depth, regex complexity, upload count, and response size.
- Set connection, read, write, request, job, and dependency deadlines.
- Use bounded queues and concurrency; reject or shed optional work under saturation.
- Rate limit by principal, credential, IP, endpoint, and costly resource as applicable.
- Avoid catastrophic regular expressions and unbounded recursion.
- Decompress and parse in sandbox or worker with resource limit for hostile file formats.

## Implementation recipes
### Safe object update
1. Parse bounded request body.
2. Validate command DTO with allowed writable fields.
3. Load object scoped by actor and tenant.
4. Authorize transition from current state.
5. Apply domain validation.
6. Persist transactionally and audit safe event.
7. Return minimal response.

### Safe download
1. Resolve generated storage ID, not user path.
2. Authorize actor against record and tenant.
3. Read from private storage with size limit.
4. Set safe content type, disposition, and anti-sniff header.
5. Stream with deadline and audit access.

## Failure modes juniors miss
- Validated input becomes unsafe after later URL decode or format conversion.
- Generic sanitizer is used across HTML, SQL, URL, and shell contexts.
- Authorization check happens before object reload and TOCTOU changes state.
- File type trusts client MIME, extension, or magic bytes alone.
- Parser accepts huge nested JSON or compressed payload and exhausts memory.
- Secret exists only in CI log or frontend source map, not repository source.
- Dependency scanner result is ignored because package is transitive and reachability is unknown.
- Catch-all error converts integrity failure into successful partial operation.

## Critique rubric
- Every input has schema, domain, ownership, and resource bounds.
- Every sink uses its native structured API or correct context encoding.
- Resource access is scoped by actor, object, tenant, and state.
- Files have content, path, storage, serving, and decompression controls.
- Parsing and deserialization constrain type, depth, size, and external resolution.
- Crypto uses approved library primitives with correct key and nonce lifecycle.
- Dependency risk has version, reachability, provenance, and remediation evidence.
- Errors and logs support operations without leaking sensitive material.

## Authoritative references
- https://cheatsheetseries.owasp.org/
- https://cwe.mitre.org/
- https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/
- https://owasp.org/www-community/attacks/Path_Traversal
- https://www.rfc-editor.org/rfc/rfc9110
