---
name: cryptography-basics
description: >-
  Select and operate standard cryptographic primitives, protocols, and key
  lifecycles without inventing cryptography or weakening library guarantees.
---

# Cryptography Basics

## Expert mental model
- Cryptography protects a precise property against a stated attacker, not "data security" in general.
- Algorithm choice is only one part: key source, nonce discipline, associated data, protocol, storage, and rotation determine safety.
- Use a high-level maintained library that owns dangerous details whenever possible.
- Encoding is not encryption. Hashing is not password storage. Encryption without authentication is unsafe.
- Key compromise, replay, downgrade, metadata leakage, and endpoint compromise remain possible after correct primitive use.
- Design key lifecycle before writing ciphertext or issuing token; migration after data accumulates is expensive.

## Define goal first
- Password verification: resist offline guessing after database theft.
- Confidentiality: attacker cannot read plaintext.
- Integrity: attacker cannot modify data undetected.
- Authenticity: verifier knows holder of signing or MAC key produced message.
- Key agreement: peers derive shared secret without sending it.
- Random identifier: unpredictable capability or anti-CSRF token.
- Transport: protect channel against active network attacker.
- Do not use crypto to solve authorization, injection, availability, or endpoint malware.

## Choose table
| Need | Choose | Notes |
|---|---|---|
| Password hash | Argon2id | Unique salt; tune memory, iterations, parallelism |
| Password fallback | bcrypt | Use supported library; 72-byte input behavior matters |
| AEAD encryption | AES-256-GCM | Unique nonce per key; hardware acceleration common |
| AEAD encryption | ChaCha20-Poly1305 | Good portable choice without AES acceleration |
| Digital signature | Ed25519 | Modern simple default for new signing systems |
| Key agreement | X25519 | Combine result with HKDF before use |
| Symmetric derivation | HKDF-SHA-256 or SHA-512 | Use explicit salt and context info |
| Message MAC | HMAC-SHA-256 | Separate key and canonical bytes |
| Secure random | OS CSPRNG | Use library secure random API |
| Transport | TLS 1.3 | TLS 1.2 only when compatibility requires |

## Never use
- Never write cipher, MAC, signature, padding, nonce, or key derivation scheme.
- Never use ECB, unauthenticated CBC, raw RSA encryption, RC4, DES, 3DES, MD5, or SHA-1 for new protection.
- Never reuse AEAD nonce with same key.
- Never store passwords with plain SHA-256, reversible encryption, or fast generic hash alone.
- Never use `Math.random`, time, counter, UUID v1, or ordinary PRNG for secret.
- Never compare secret authentication value with ordinary equality when constant-time API exists.
- Never select JWT algorithm from untrusted token header without fixed allowlist.
- Never put private key, long-lived API key, or master key in source, browser, image, or log.

## AEAD recipe
1. Generate random data-encryption key from CSPRNG or obtain from KMS.
2. Generate unique nonce for that key according to library contract.
3. Include immutable context as associated authenticated data: record ID, version, tenant, purpose.
4. Encrypt with AES-GCM or ChaCha20-Poly1305.
5. Store algorithm version, key ID, nonce, ciphertext, and tag.
6. On read, select key by key ID and verify authentication before using plaintext.
7. Reject any authentication failure without distinguishing reason.

## Nonce discipline
- AES-GCM nonce reuse under same key can reveal plaintext and permit forgery.
- Random 96-bit nonce is acceptable only while collision probability remains safely negligible for volume.
- Counter nonce requires durable monotonic state and no rollback across process or restore.
- If uniqueness cannot be guaranteed, use misuse-resistant construction offered by reviewed library and document tradeoff.
- Nonce is normally public but must be stored with ciphertext.
- Never derive nonce from timestamp alone or reset counter on restart.

## Password storage
- Use Argon2id library with per-password random salt and calibrated cost.
- Calibration target is product-specific: consume acceptable server memory and roughly 100 to 500 ms verification under expected load.
- Recalibrate on actual deployment hardware; memory cost matters more than iteration count for GPU resistance.
- Store encoded algorithm, version, parameters, salt, and hash using library format.
- Add optional server-side pepper held outside database only when rotation and availability plan exists.
- Rehash on successful login when stored parameters are below current policy.
- Rate limit login and recovery separately; strong KDF alone does not stop online guessing.

## Signatures and MACs
- Use signature when verifier must not hold signing key or multiple parties verify independently.
- Use HMAC when producer and verifier share secret and key distribution is acceptable.
- Sign or MAC canonical bytes, not language object serialization with unstable field order.
- Bind algorithm, version, key ID, audience, context, and expiry in signed data.
- Verify before parsing trusted claims or performing privileged work.
- Separate signing keys from encryption and MAC keys even if same KMS stores them.
- Plan public-key distribution, revocation, rotation, and overlap period.

## Key agreement and KDF
- X25519 creates shared secret, not directly application encryption key.
- Feed shared secret into HKDF with salt and context label describing protocol and direction.
- Use different HKDF info for client-to-server and server-to-client keys.
- Authenticate peer key using signature, certificate, or pre-established trust; unauthenticated DH permits MITM.
- Delete ephemeral secrets after session establishment where runtime permits.
- Do not reuse one long-term DH key as universal encryption key without protocol review.

## Key management
- Key hierarchy: root or KMS key wraps key-encryption key, which wraps data-encryption keys.
- Envelope encryption limits blast radius and enables rotation without reencrypting all data immediately.
- Assign every key an owner, purpose, environment, algorithm, creation date, rotation policy, and revocation procedure.
- Enforce least privilege: service decrypts only data it serves, not all environment secrets.
- Use KMS or HSM for high-value root and signing keys where operational model supports it.
- Backup and disaster recovery need protected copies and restoration test.
- Rotation needs key ID with ciphertext, read old/write new behavior, metrics, and retirement criteria.

## TLS assumptions
- TLS protects transport between validated endpoints, not malicious endpoint or application authorization failure.
- Verify hostname and certificate chain; do not disable certificate validation.
- Use current platform TLS configuration rather than hand-built cipher suite list unless compliance requires.
- Pinning has operational failure cost; use only with managed rotation plan.
- Avoid secrets in URL query even with TLS because logs, history, and referrers may expose them.
- Mutual TLS authenticates connection identity but does not replace per-request authorization.

## Tokens
- Opaque random token is often simpler than self-contained JWT when immediate revocation matters.
- JWT validation fixes accepted algorithm, issuer, audience, expiry, not-before, and key source.
- Treat `kid` as lookup hint only; prevent path or URL injection through key ID.
- Keep access tokens short lived and refresh tokens revocable, rotated, and device-bound where appropriate.
- Do not encrypt JWT merely to hide mutable authorization; enforce authorization at server state.

## Failure modes juniors miss
- AEAD encrypts data but associated data omits tenant, allowing ciphertext swap across accounts.
- Key rotation changes writer but reader does not retain old key ID.
- Random nonce collision risk ignored at high volume.
- Password cost set locally then overloads production login during traffic spike.
- Signature checks a parsed and reserialized object, not original signed bytes.
- X25519 shared secret used directly without HKDF and peer authentication.
- TLS validation disabled "for development" and reaches production.
- JWT accepted with wrong audience by a different service.

## Critique rubric
- Security goal, attacker, and protected boundary are explicit.
- Standard primitive and maintained high-level library match goal.
- Nonce, salt, randomness, associated data, and validation semantics are correct.
- Keys have owner, purpose separation, storage, access policy, rotation, recovery, and revocation.
- Password design includes KDF parameters, login abuse controls, and upgrade path.
- Signature or MAC input is canonical and bound to context, expiry, and audience.
- TLS and token design name residual endpoint and revocation limitations.
- Negative tests cover altered ciphertext, wrong key, expiry, replay, and rotation.

## Authoritative references
- https://www.rfc-editor.org/rfc/rfc9106
- https://www.rfc-editor.org/rfc/rfc8439
- https://www.rfc-editor.org/rfc/rfc7748
- https://www.rfc-editor.org/rfc/rfc5869
- https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html
- https://csrc.nist.gov/projects/key-management
