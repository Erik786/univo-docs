# Security

Security is designed into the platform at every layer, and was independently verified by a full-stack security audit in August 2026. This page summarizes the security model and the audit outcome in client-appropriate terms.

## Authentication & authorization

- **JWT-based sessions.** The auth MFE is the only place credentials are entered. On login, the GraphQL API issues a JWT with a role (`USER`, `STAFF`, `ADMIN`, `SUPERADMIN`); the token is stored in `localStorage` and sent as the `Authorization` header on every API request.
- **Centralized validation.** Learner app and admin console validate the token on bootstrap and redirect to the auth MFE when it is missing or expired. Logout and session expiry are handled centrally.
- **Staff engine sessions.** Privileged roles additionally receive a course-engine `login_session` cookie (CSRF-protected) for Studio access, separate from the platform JWT.
- **Impersonation is explicit.** The admin "ghost" feature uses a distinct token (`jwt-ghost`), keeping impersonated sessions identifiable.

## Secrets management (post-audit standard)

| Rule | Implementation |
|---|---|
| No secrets in git | All credentials removed from repositories; git history scrubbed where secrets had been committed |
| Centralized storage | Platform secrets rendered into **AWS Secrets Manager**, granted to EC2/Lambda via scoped IAM roles |
| Per-environment config | MFE `.env` files live in a restricted config bucket and are fetched by CI at build time, never committed |
| Rotation | All credentials exposed at any point in history were rotated during the audit remediation |
| State protection | Terraform state buckets are private, versioned, and encrypted (state contains sensitive values) |

## Network & edge protection

- **AWS WAF** in front of CloudFront, shared across platforms.
- **Encryption in transit:** TLS 1.2 minimum enforced on CloudFront (`TLSv1.2_2021`); all API traffic over HTTPS.
- **Encryption at rest:** RDS, DocumentDB, and S3 use AWS-managed encryption; S3 buckets have all four public-access-block flags set.
- **Private data layer:** databases sit in private subnets, reachable only from the application tier; operations access goes through a bastion host.

## August 2026 security audit

An independent, full-read security audit covered all four platform repositories (learner app, auth MFE, admin console, and Terraform infrastructure). The audit deliberately probed for credential exposure, injection, XSS, access-control, and infrastructure misconfiguration issues.

**Findings: 75 total**

| Severity | Count | Status |
|---|---|---|
| Critical | 10 | All fixed |
| High | 19 | All fixed |
| Medium | 27 | All fixed |
| Low | 19 | All fixed |

**Remediation highlights:**

- Every one of the 75 findings was fixed and verified, none were deferred or accepted as residual risk.
- All credentials that had ever appeared in repositories were **rotated**.
- **Git history was scrubbed** to remove historical secrets.
- Secrets handling was re-architected around AWS Secrets Manager and CI-fetched environment files (see table above).
- Infrastructure guardrails were tightened: encrypted and versioned state, restricted egress, S3 versioning, and WAF coverage.

Detailed findings documents are internal engineering material; this page is the client-facing summary. The audit process is now part of the delivery playbook, each new deployment inherits the post-audit posture by construction.

## Ongoing practices

- Security-sensitive code paths (credential handling, token storage, redirect logic, password reset, HTML rendering of user/API content) require mandatory review.
- User- and API-supplied HTML is sanitized before rendering; unsafe injection patterns are banned by contribution guidelines.
- CloudWatch alarms and centralized logs provide detection; SES notifications alert the operations team.
- Re-audit is scheduled at each major architecture tier change (see [ADR-0004](decisions/ADR-0004-scale-200M.md)).
