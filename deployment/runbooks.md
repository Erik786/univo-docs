# Incident Response & Runbooks

Client-facing incident response method (SRE/Google-style, small-team adapted) plus concrete first-response runbooks.

## Severity levels

| Severity | Definition | Examples | Response time |
|---|---|---|---|
| **SEV1** | Platform down or data loss | Site unreachable, database corruption, all logins failing | Immediate, all hands |
| **SEV2** | Major feature degraded | Studio not saving, login broken for some users, chat down | < 1 hour |
| **SEV3** | Minor degradation, workaround exists | One report slow, cosmetic issue, single integration flaky | < 24 hours |

## Roles (even for a small team)

| Role | Responsibility |
|---|---|
| **Incident Commander (IC)** | Coordinates, decides, communicates. Does **not** debug personally. |
| **Ops** | Executes the technical diagnosis and fix. |
| **Scribe** | Keeps a written timeline in real time (essential for the post-mortem). |

One person can hold two roles on a small team, but the IC must never be the only debugger.

## SEV1 / SEV2 protocol

1. **Declare**: "SEV-N declared at HH:MM, symptom X", in the dedicated incident channel (e.g. the incident Telegram/Slack group).
2. **Stabilize before repairing**: rollback > fix-forward. Restore service FIRST; root-cause later.
3. **Update every 15–30 minutes**, even if "nothing new", silence is the worst signal.
4. **Close**: service restored + verified + exit communication sent.

## Post-mortem (mandatory for SEV1/SEV2, within 72h)

Format:

1. Factual **timeline** (from the Scribe's notes)
2. **Root cause** (5 whys, blameless)
3. What went well
4. **Corrective actions with owner + deadline**, tracked as kanban tasks

## Prevention, must exist BEFORE an incident

- [ ] Healthchecks on all services (ALB target health, course-engine `/heartbeat`, databases)
- [ ] CloudWatch alerts → SNS → chat channel (or a watchdog cron meanwhile)
- [ ] Runbooks maintained (this page)
- [ ] Backups tested, a backup never restored is a backup that doesn't exist; quarterly restore test
- [ ] Game day once per quarter: deliberately break something in staging

**Availability target: 99%** = max 7h18m downtime/month, measured monthly and reported in weekly metrics.

## Runbooks

### Site down

1. Check ALB target health: `aws elbv2 describe-target-health --target-group-arn <arn>`, all targets unhealthy → instance-level problem.
2. Check the instance: `aws ec2 describe-instance-status --instance-ids <id>`; SSH via bastion if reachable.
3. On the instance: `tutor local status`, are containers up? `tutor local logs --tail=100 caddy lms`.
4. Check disk space (`df -h`) and memory (`free -m`), full disk is a common cause.
5. Fastest restore: restart services (`tutor local restart`) or reboot the instance; if a recent deploy preceded the outage, **roll back the deploy** rather than debug live.
6. Escalate to CloudFront/WAF checks only if the origin is healthy but users still can't reach the site.

### Database full

1. Confirm: RDS → CloudWatch `FreeStorageSpace` metric, or `df -h` on self-hosted; check DocumentDB storage metrics similarly.
2. Immediate relief: purge binary logs (`PURGE BINARY LOGS BEFORE NOW();`), drop temp tables, clear old session/tracking tables.
3. Grow storage: `aws rds modify-db-instance --db-instance-identifier <id> --allocated-storage <larger> --apply-immediately` (storage autoscaling should be enabled).
4. Longer term: identify the largest tables (`information_schema`), set retention policies for logs/tracking data.

### Studio not saving

1. Reproduce and capture the browser error (network tab: 4xx vs 5xx, CSRF failures).
2. Check CMS logs: `tutor local logs --tail=200 cms`, look for CSRF, permission, or Mongo errors.
3. Verify DocumentDB/Mongo connectivity from the instance, "not saving" is often a content-DB connection failure.
4. Check disk space on the instance and DocumentDB storage.
5. Restart CMS: `tutor local restart cms`. If tied to a recent MFE/platform change, roll that back first.

### SSL certificate expired

1. Confirm: `openssl s_client -connect <domain>:443 -servername <domain> </dev/null 2>/dev/null | openssl x509 -noout -dates`.
2. CloudFront/ACM cert: request or revalidate in ACM (**us-east-1**), then associate to the distribution. DNS-validation certs renew automatically if the validation CNAME still exists, check it first.
3. Instance-level cert (Caddy): `tutor local restart caddy` triggers re-issuance; check `tutor local logs caddy` for ACME errors (port 80 reachability, rate limits).
4. After renewal, hard-refresh and verify the new expiry date; add a CloudWatch/external expiry alert (>14 days warning).
