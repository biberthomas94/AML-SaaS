# Threat Model (MVP)

## Assets
- PII (customer identity data)
- Transaction history
- SAR drafts and evidence
- Credentials and access tokens

## Actors
- Internal users (analyst, reviewer, admin)
- External adversaries (internet-based)
- Malicious insiders

## Entry Points & Risks (STRIDE-lite)
- **Spoofing:** Compromised credentials → Mitigate with OIDC, MFA (optional), RBAC.
- **Tampering:** Audit logs or SARs altered → Mitigate with append-only log + hash chaining.
- **Repudiation:** Denial of actions → Mitigate with detailed audit (actor, time, before/after, IP).
- **Information Disclosure:** PII leakage → Mitigate with encryption-at-rest, no PII in logs, least privilege.
- **DoS:** API overload → Rate limiting, pagination.
- **Elevation of Privilege:** Privilege misuse → Enforce role checks server-side; deny-by-default.

## Controls
- RBAC middleware, centralized
- Secrets Manager (no secrets in code)
- Input validation on ingestion endpoints
- Security scanning in CI (Semgrep/OWASP ZAP light)
- S3 bucket policies for evidence
