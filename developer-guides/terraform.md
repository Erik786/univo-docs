# Infrastructure (Terraform)

Developer guide for the `lms-terraform` repository — the entire AWS stack as code: VPC, ALB, WAF, CloudFront, S3 static hosting, EC2 (Open edX / Tutor), RDS MySQL, DocumentDB, Lambda GraphQL API services, and a bastion host.

## Two-layer design

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
│  EC2 (Tutor/Open edX)                                          │
│  Lambda GraphQL services + API Gateway · cron Lambdas          │
│  CloudFront distribution for static MFEs (admin/app/auth)      │
│  Optional dedicated RDS MySQL / DocumentDB · Secrets Manager   │
│  CloudWatch alarms                                             │
└────────────────────────────────────────────────────────────────┘
```

The three React MFEs are built separately and synced to the SPA buckets; CloudFront serves them behind the shared WAF.

## Repository layout

```
infrastructure/
├── terraform/
│   ├── bootstrap/            # per-account shared resources
│   ├── platform/             # per-platform resources
│   │   └── modules/lambda_service/
│   └── change_logs.txt
├── environment/
│   ├── dev/  common/bootstrap/   generic/platform/   # backend.tf + terraform.tfvars
│   └── prod/ common/bootstrap/   generic/platform/
├── deploy-*.sh / plan-*.sh / destroy-*.sh   # macro-substitution deploy scripts
├── ec2-bootstrap-scripts/
└── buildspec.yml / destroy.platform.buildspec.yml
```

`environment/<env>/generic/platform/` files contain `__MACRO__` placeholders that deploy scripts substitute from a `.env` file before running Terraform.

## Prerequisites

- Terraform ≥ 1.x with AWS provider `>= 5.0`
- AWS CLI v2 with an SSO profile
- An S3 state bucket per account/region: **private, versioning ON, default encryption** — state contains sensitive values
- Build artifacts published: MFE builds in the SPA bucket, Lambda zips in the artifacts bucket, a valid AMI id

## Deployment workflow

### 1. Bootstrap (once per AWS account)

```bash
cp -r infrastructure/environment/dev/common/bootstrap infrastructure/environment/dev/<account>/bootstrap
# Edit backend.tf (unique state key, bucket, region, profile) and terraform.tfvars
cd infrastructure/environment/dev/<account>/bootstrap
terraform init && terraform apply
```

### 2. Deploy a platform (canonical path: CodeBuild)

The `buildspec.yml` pipeline:

1. Downloads the customer `.env` from the config bucket (`s3://$BUCKET_CONF/$APP_ENV/.env.$CUSTOMER`)
2. Assumes the prod role when `APP_ENV=prod`
3. Runs `deploy-generic-platform.sh`: copies `environment/$APP_ENV/generic/platform/` + `terraform/platform/` into a scratch `builds/` dir, substitutes `__MACROS__` in `terraform.tfvars` / `backend.tf`, then applies

Local equivalent:

```bash
cd infrastructure
cp .env.$CUSTOMER .env          # obtained securely — never committed
./deploy-generic-platform.sh
```

## Key variables

| Variable | Purpose |
|---|---|
| `env`, `app_name`, `platform_name` | Naming/resource prefixing |
| `vpc_id`, `vpc_private_subnets_ids`, `vpc_public_subnets_ids` | From bootstrap outputs |
| `management_security_group_id`, `alb_security_group_id` | From bootstrap |
| `ec2_ami`, `ec2_instance_type`, `subnet_index` | Platform instance placement |
| `docdb_*`, `mysql_db_*` | Data layer endpoints & credentials (**never commit real values**) |
| `lambda_api_build_id` | Artifact version to deploy |
| `alarms_email`, `admin_email` | Notifications |

## Common tasks

### Deploy a new customer

1. Create `.env.<CUSTOMER>` with the customer's macros (obtain securely, never commit) and upload it to the config bucket.
2. Ensure artifacts exist: MFE builds at `<env>-common-apps-customers-bucket/<platform_id>/`, Lambda zips in the artifacts bucket, valid AMI.
3. Trigger the CodeBuild pipeline (or run `deploy-generic-platform.sh` locally).
4. Verify: `terraform output`, ALB target health, CloudFront distribution deployed, login flow works end-to-end.

### Plan-only (dry run)

```bash
cd infrastructure
cp .env.$CUSTOMER .env
./plan-generic-platform.sh
```

Use this before every apply and for change review.

### Destroy a platform

```bash
./destroy-generic-platform.sh        # locally
# or run destroy.platform.buildspec.yml in CodeBuild
```

Destroy removes customer data — require explicit business sign-off and a completed backup first.

### Rotate a secret

1. Generate the new credential (DB password, API key, token).
2. Update the value in AWS Secrets Manager (rendered by `platform/instances-app.tf`) and in the customer `.env` in the config bucket.
3. Re-apply the platform layer so dependent resources pick up the change: `./plan-generic-platform.sh` then `deploy-generic-platform.sh`.
4. Restart/redeploy affected services (EC2 user services, Lambdas) and verify health.

## Rules

- One environment = one state file. Never share a state key between platforms.
- Do not hand-edit files under `builds/` — scratch dir regenerated by deploy scripts.
- Never commit `.env`, `*.pem`, or filled `terraform.tfvars` containing real credentials.
- All S3 buckets set the four public-access-block flags; CloudFront enforces `TLSv1.2_2021`; CSRF caching is disabled on the distribution.
