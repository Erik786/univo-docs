# Infrastructure

The entire AWS stack is defined as code in the `lms-terraform` repository. Everything deploys into the **client's own AWS account**, the client owns the data, the infrastructure, and the keys.

## Two-layer Terraform design

```
┌────────────────────────────────────────────────────────────────┐
│  BOOTSTRAP  (once per AWS account)                             │
│  VPC · subnets · NAT · ALB (+SG) · WAF (CloudFront-scope)      │
│  S3 logs bucket · SPA buckets · CloudFront Function · Bastion  │
│  Optional shared RDS / DocumentDB                              │
└────────────────────────────────────────────────────────────────┘
        │ outputs consumed via tfvars (SG ids, subnets, OAI…)
        ▼
┌────────────────────────────────────────────────────────────────┐
│  PLATFORM  (once per deployed customer platform)               │
│  EC2 (Tutor/Open edX, single instance)                         │
│  Lambda GraphQL services + API Gateway · cron Lambdas          │
│  CloudFront distribution for static MFEs (admin/app/auth)      │
│  Optional dedicated RDS MySQL / DocumentDB · Secrets Manager   │
│  CloudWatch alarms                                             │
└────────────────────────────────────────────────────────────────┘
```

The **bootstrap** layer creates account-wide shared resources once; the **platform** layer stamps out an isolated customer platform on top of it. One environment = one Terraform state file, state keys are never shared between platforms. The canonical deploy path is an AWS CodeBuild pipeline that fetches the customer's `.env` from a config bucket, substitutes placeholders in `terraform.tfvars`/`backend.tf`, and applies.

## Components

| Component | AWS service | Notes |
|---|---|---|
| Network | VPC, public/private subnets, NAT gateway | Per-account, from bootstrap |
| Edge | CloudFront + CloudFront Function | Serves the three MFEs; TLS 1.2 minimum; CSRF endpoint caching disabled |
| Web protection | AWS WAF (CloudFront scope) | Shared across platforms |
| Load balancing | ALB | Fronts the Open edX EC2 instance |
| Course engine | EC2 (Tutor / Open edX) | Single instance per platform at pilot scale |
| Platform data | RDS MySQL | Shared (bootstrap) or dedicated (platform) |
| Course content | DocumentDB | MongoDB-compatible modulestore |
| API | Lambda + API Gateway | GraphQL services, versioned zip artifacts |
| Scheduled jobs | Lambda (cron) | Housekeeping, reports, sync jobs |
| Static hosting | S3 | SPA buckets (public access blocked), logs bucket, artifacts bucket |
| Access | Bastion host | SSM/SSH entry point for operations |
| Secrets | Secrets Manager | Platform secrets rendered here, granted to EC2/Lambda via IAM |
| Observability | CloudWatch | Alarms with email notification |

## Security posture (infra level)

- All S3 buckets set the four public-access-block flags.
- CloudFront enforces `TLSv1.2_2021`.
- Terraform state buckets are private, versioned, and encrypted (state contains sensitive values).
- No secrets in git: credentials live in Secrets Manager and per-customer `.env` files fetched at deploy time (see [Security](security.md)).

## Monthly cost estimate, pilot deployment

Indicative cost for a single pilot platform (100–1,000 active learners, single EC2, modest traffic). Actual figures vary by region and usage.

| Item | Sizing assumption | Est. USD/month |
|---|---|---|
| EC2 (Open edX / Tutor) | 1 × t3.large, 24/7 | ~60 |
| RDS MySQL | db.t3.medium, single-AZ | ~50 |
| DocumentDB | db.t3.medium, 1 instance | ~55 |
| ALB | 1 ALB + modest LCU usage | ~20 |
| NAT gateway | 1 NAT + data processing | ~35 |
| CloudFront + S3 | static hosting + learner traffic | ~10 |
| Lambda + API Gateway | GraphQL + cron, low volume | ~5 |
| Misc (WAF, Secrets Manager, CloudWatch, backups) | baseline rules + alarms | ~15 |
| **Total** | | **~250** |

Pilot deployments land in the **225–300 USD/month** range depending on region (e.g. af-south-1 runs slightly above average) and data transfer. Scaling tiers beyond the pilot are defined in [ADR-0004](decisions/ADR-0004-scale-200M.md).

## Operations at a glance

```bash
# Bootstrap (once per account)
cd infrastructure/environment/<env>/<account>/bootstrap
terraform init && terraform apply

# Deploy a customer platform (canonical path: CodeBuild; local equivalent:)
cd infrastructure
cp .env.$CUSTOMER .env          # obtained securely, never committed
./deploy-generic-platform.sh    # or plan- / destroy- variants
```
