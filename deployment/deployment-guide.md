# 🚀 Deployment Guide: The Delivery Playbook

The platform goes live in hours; the full enterprise rollout (infrastructure, course engine, MFEs, integrations, content, training, go-live) completes in 15 working days. Every phase has a verification gate.

{% hint style="success" %}
**The promise:** follow this playbook day by day and you finish week 3 with a validated, monitored, client-owned production platform, not a demo.
{% endhint %}

{% hint style="warning" %}
**Prerequisite gate:** all items in [Client Prerequisites](client-prerequisites.md) must be delivered before Day 1. A missing AWS sub-account or DNS delegation stalls everything downstream.
{% endhint %}

## 📅 Delivery timeline at a glance

| Phase | What happens | When |
|---|---|---|
| 📝 **Order** | Platform list confirmed, admin contacts named, brand assets (logo, colors) collected | **J0** |
| 🏗️ **Construction** | LMS configured, internal tests, branding and personalization applied | **J0 → J+2** |
| 📦 **Delivery** | Platform handed over, admin licenses and access codes issued | **J+3** |
| 🛠️ **Admin training** | Video-conference workshops: account setup, license allocation, test live class | **J+5** |
| 🎓 **Trainer training** | Workshops on course authoring: building paths, importing external content, quizzes | **J+10** |
| 🤝 **Ongoing support** | Continuous: support requests, personalization help, messaging configuration, for the life of the contract | **Continuous** |

{% hint style="info" %}
💡 **Dates flex to your calendar.** Every milestone above can shift to match your organization's schedule and the availability of your teams, the sequence stays, the dates adapt.
{% endhint %}

## 🏗️ Week 1, Infrastructure (Terraform)

1. **Day 1, Prerequisites & secrets.** Confirm prerequisites; create the client `.env` macros file; store credentials in Secrets Manager / config bucket. *Verify:* checklist signed off; `.env` uploaded to `s3://<config-bucket>/<env>/`.
2. **Day 2, Bootstrap apply** (once per account): VPC, ALB, WAF, SPA buckets, bastion. *Verify:* `terraform output` in the bootstrap dir returns VPC/subnet/SG ids.
3. **Day 3, Platform plan.** Run `./plan-generic-platform.sh`; review the diff with the team. *Verify:* plan shows only expected resources, no deletions.
4. **Day 4, Platform apply** via the CodeBuild pipeline (`buildspec.yml`) or `deploy-generic-platform.sh`. *Verify:* `terraform output` returns EC2 IP, CloudFront domain, ALB DNS; EC2 instance `running`.
5. **Day 5, Verify + document.** ALB target health, RDS/DocumentDB reachable, Secrets Manager populated, CloudWatch alarms live. *Verify:* `aws elbv2 describe-target-health` → all `healthy`; alarms in `OK` state.

{% code title="terminal, week 1" %}
```bash
cd infrastructure
./plan-generic-platform.sh       # dry run, always before apply
./deploy-generic-platform.sh     # apply (canonical path is CodeBuild)
terraform output                 # capture endpoints for week 2
```
{% endcode %}

{% hint style="info" %}
💡 **Plan before you apply, every time.** The CodeBuild pipeline is the canonical apply path, local applies are the escape hatch, not the norm.
{% endhint %}

## 🔌 Week 2, Course engine + MFEs + integrations

6. **Day 6, Install the course engine** (latest release) on the platform EC2 via the bootstrap scripts; configure domain, DB connections (RDS MySQL, DocumentDB). *Verify:* engine services up; `curl https://<domain>/heartbeat` → `OK`.
7. **Day 7, Course-engine configuration:** site config, theming, user creation, Studio access. *Verify:* login at `/login`; Studio reachable at the studio endpoint.
8. **Day 8, Build & deploy the three MFEs** (auth, app, admin) via their CodeBuild pipelines with the customer env. *Verify:* `curl -I https://<domain>/auth` `/app` `/v2-admin` → `200`; login flow works end to end.
9. **Day 9, Integrations:** Pusher (chat keys in MFE envs), Jitsi (`REACT_APP_JITSI_DOMAIN` / self-hosted Jitsi), Vimeo Pro (API token in platform config). *Verify:* send a chat message between two sessions; join a Jitsi room; play an embedded Vimeo video.
10. **Day 10, Email (SES) + transactional flows;** smoke-test registration, password reset, enrollment. *Verify:* reset email received; new user can enroll in a test course.

{% code title="terminal, week 2" %}
```bash
ssh <bastion> && ssh <platform-ec2>
tutor local status && tutor local logs --tail=100 lms
aws codebuild start-build --project-name <mfe-project>   # per MFE
```
{% endcode %}

{% hint style="info" %}
💡 **Test integrations with real flows, not config checks.** A chat message that actually arrives beats a Pusher key that merely exists.
{% endhint %}

## ✅ Week 3, Content, validation, handover, go-live

11. **Day 11, Content migration:** import course exports, upload assets to S3, verify SCORM/LTI packages. *Verify:* all courses visible in Studio; assets load over HTTPS.
12. **Day 12, User migration/creation;** coach and admin accounts; license assignment. *Verify:* test logins per role (learner, coach, admin).
13. **Day 13, Full validation checklist** (below) with the client's technical contact. *Verify:* every item checked, evidence captured.
14. **Day 14, Fixes + handover.** Resolve validation findings; runbook walkthrough with client ops. *Verify:* handover doc signed; client knows the runbooks.
15. **Day 15, Go-live:** DNS cutover to CloudFront, monitor dashboards, business sign-off. *Verify:* zero errors in CloudWatch for 4h post-cutover; sign-off email received.

### ✅ Validation checklist

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

### 🚦 Go-live

{% code title="terminal, cutover" %}
```bash
# Cutover: point the client domain at the CloudFront distribution
aws route53 change-resource-record-sets --hosted-zone-id <zone> --change-batch file://cutover.json
# Monitor
aws cloudwatch describe-alarms --state-value ALARM
```
{% endcode %}

{% hint style="warning" %}
⚠️ **Rollback plan:** revert the DNS record to the previous target. Keep TTL ≤ 300s on the cutover record so a rollback propagates in minutes, not hours.
{% endhint %}

## What's next?

- 📋 [Client Prerequisites](client-prerequisites.md), the gate before Day 1
- ⚙️ [Operations Runbooks](runbooks.md), what client ops uses after handover
- 🏗️ [System Architecture](../architecture/system-architecture.md), the platform you're deploying
