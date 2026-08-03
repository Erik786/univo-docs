# Deployment Guide

Three-week playbook for deploying a new customer platform, end to end. Prerequisite: all items in [Client Prerequisites](client-prerequisites.md) are delivered.

## Week 1 — Infrastructure (Terraform)

| Day | Activity | Verification |
|---|---|---|
| 1 | Confirm prerequisites; create client `.env` macros file; store credentials in Secrets Manager / config bucket | Checklist signed off; `.env` uploaded to `s3://<config-bucket>/<env>/` |
| 2 | **Bootstrap apply** (once per account): VPC, ALB, WAF, SPA buckets, bastion | `terraform output` in the bootstrap dir returns VPC/subnet/SG ids |
| 3 | **Platform plan**: `./plan-generic-platform.sh`; review the diff with the team | Plan shows only expected resources; no deletions |
| 4 | **Platform apply**: CodeBuild pipeline (`buildspec.yml`) or `deploy-generic-platform.sh` | `terraform output` returns EC2 IP, CloudFront domain, ALB DNS; EC2 instance `running` |
| 5 | Verify + document: ALB target health, RDS/DocumentDB reachable, Secrets Manager populated, CloudWatch alarms live | `aws elbv2 describe-target-health` → all `healthy`; alarms in `OK` state |

Key commands:

```bash
cd infrastructure
./plan-generic-platform.sh       # dry run — always before apply
./deploy-generic-platform.sh     # apply (canonical path is CodeBuild)
terraform output                 # capture endpoints for week 2
```

## Week 2 — Open edX (Tutor Ulmo) + MFEs + integrations

| Day | Activity | Verification |
|---|---|---|
| 6 | Install Tutor (Ulmo release) on the platform EC2 via the bootstrap scripts; configure domain, DB connections (RDS MySQL, DocumentDB) | `tutor local status` → services up; `curl https://<domain>/heartbeat` → `OK` |
| 7 | Open edX configuration: site config, theming, user creation, Studio access | Login at `/login`; Studio reachable at the studio endpoint |
| 8 | Build & deploy the three MFEs (auth, app, admin) via their CodeBuild pipelines with the customer env | `curl -I https://<domain>/auth` `/app` `/v2-admin` → `200`; login flow works end-to-end |
| 9 | **Integrations**: Pusher (chat keys in MFE envs), Jitsi (`REACT_APP_JITSI_DOMAIN` / self-hosted Jitsi), Vimeo Pro (API token in platform config) | Send a chat message between two sessions; join a Jitsi room; play an embedded Vimeo video |
| 10 | Email (SES) + transactional flows; smoke-test registration, password reset, enrollment | Reset email received; new user can enroll in a test course |

Key commands:

```bash
ssh <bastion> && ssh <platform-ec2>
tutor local status && tutor local logs --tail=100 lms
aws codebuild start-build --project-name <mfe-project>   # per MFE
```

## Week 3 — Content, validation, handover, go-live

| Day | Activity | Verification |
|---|---|---|
| 11 | Content migration: import course exports, upload assets to S3, verify SCORM/LTI packages | All courses visible in Studio; assets load over HTTPS |
| 12 | User migration/creation; coach and admin accounts; license assignment | Test logins per role (learner, coach, admin) |
| 13 | **Full validation checklist** (below) with the client's technical contact | Every item checked, evidence captured |
| 14 | Fixes from validation; runbook walkthrough + handover session with client ops | Handover doc signed; client knows the runbooks |
| 15 | **Go-live**: DNS cutover to CloudFront, monitor dashboards, business sign-off | Zero errors in CloudWatch for 4h post-cutover; sign-off email received |

### Validation checklist

- [ ] Login, logout, password reset (all roles)
- [ ] Course playback: HTML, problems, SCORM, LTI, video
- [ ] Progress tracking, certificates (PDF generation + logo)
- [ ] Chat (learner ↔ coach) in real time
- [ ] Jitsi live session join
- [ ] Admin: user management, catalog, licenses, theming
- [ ] Emails delivered (registration, reset, notifications)
- [ ] Backups configured and one restore test executed
- [ ] CloudWatch alarms firing to the right email/SNS targets
- [ ] Light + dark themes, all supported locales

### Go-live

```bash
# Cutover: point the client domain at the CloudFront distribution
aws route53 change-resource-record-sets --hosted-zone-id <zone> --change-batch file://cutover.json
# Monitor
aws cloudwatch describe-alarms --state-value ALARM
```

Rollback plan: revert the DNS record to the previous target; TTL ≤ 300s on the cutover record.
