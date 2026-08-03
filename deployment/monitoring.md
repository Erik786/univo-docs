# Monitoring & Reliability

How a Univo platform is watched in production, what alerts fire, and the reliability target we hold each deployment to.

## Reliability target

| Target | Allowed downtime |
|--------|------------------|
| 99% monthly availability | 7h 18m per month |

Availability is measured monthly at the public entry point (the learner app URL) and reported in the weekly metrics. Planned maintenance windows announced in advance are excluded.

## Health checks

| Layer | Check | Where |
|-------|-------|-------|
| Edge | CloudFront distribution returning 2xx/3xx on `/app`, `/auth`, `/v2-admin` | External probe (uptime monitor) |
| Load balancer | ALB target health: all targets `healthy` | CloudWatch: `HealthyHostCount` |
| Open edX (Tutor) | HTTP 200 on the LMS `/heartbeat` endpoint | ALB health check path + external probe |
| GraphQL API | Lambda invocation errors and duration | CloudWatch Lambda metrics |
| Database | RDS MySQL `DatabaseConnections`, CPU, free storage | CloudWatch RDS metrics |
| DocumentDB | Cluster CPU, connections, free storage | CloudWatch DocDB metrics |

A failing heartbeat on any critical path (site unreachable, heartbeat down, DB exhausted) is a SEV1/SEV2 — see [Runbooks](runbooks.md).

## Alerting

CloudWatch alarms are provisioned by Terraform (platform layer). Recommended alarm set:

| Alarm | Threshold | Severity |
|-------|-----------|----------|
| ALB healthy hosts < desired | < 1 for 2 min | SEV2 |
| LMS heartbeat failing | 2 consecutive failures | SEV1 |
| Lambda error rate | > 5% of invocations over 5 min | SEV2 |
| RDS free storage | < 10 GB | SEV3 (page ops) |
| RDS/DocDB CPU | > 80% for 15 min | SEV3 |
| WAF blocked-requests spike | > 10x baseline over 5 min | investigate |

Alarm delivery: CloudWatch → SNS → notification channel (email/Telegram webhook). The exact channel is wired per client at deployment time.

## Dashboards

A single CloudWatch dashboard per environment aggregates: ALB request count and latency, Lambda invocations/errors/duration, RDS and DocumentDB health, and CloudFront request/cache-hit rates. It is the first screen to open during an incident.

## Logs

| Source | Destination | Retention |
|--------|-------------|-----------|
| Open edX / Tutor services | `tutor logs` on the platform EC2 (see [Tutor Operations](tutor-operations.md)) | local rotation |
| Lambda API services | CloudWatch Logs, one group per function | 30 days (recommended) |
| ALB access logs | S3 logs bucket (bootstrap layer) | per bucket lifecycle |
| CloudFront / WAF logs | S3 logs bucket | per bucket lifecycle |

## Operational habits

- Backup restore test once per quarter — an unrestored backup does not exist.
- One game day per quarter on staging: deliberately break something and run the runbook.
- Review the availability number monthly; any month under 99% gets a written explanation in the weekly report.
