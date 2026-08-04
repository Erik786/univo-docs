# Client Prerequisites

Everything a client must provide before a platform deployment can start. Gather these during the sales/onboarding phase, a missing item blocks the timeline.

## Checklist

| # | Item | Details | Why it's needed |
|---|---|---|---|
| 1 | **Dedicated AWS sub-account** | A separate AWS account (or sub-account under the client's organization) for the platform, with admin access for the deployment team | All infrastructure is client-owned and deployed into the client's account; isolation keeps billing, security, and blast radius clean |
| 2 | **IAM deployment role/user** | A role (preferred) or user with permissions for: EC2, VPC, RDS, DocumentDB, Lambda, API Gateway, S3, CloudFront, WAF, ALB, Secrets Manager, CloudWatch, IAM (scoped), ACM | Terraform and CodeBuild need to create and manage every layer of the stack; cross-account assume-role is the canonical CI path |
| 3 | **DNS domain + delegation** | A registered domain (e.g. `academy.client.com`) with DNS delegated (NS records) to Route53 in the platform account, or registrar access to create records | The platform serves multiple MFEs under one domain (`/app`, `/v2-admin`, `/auth`); CloudFront and ACM validation require DNS control |
| 4 | **SSL certificate (ACM)** | A validated ACM certificate in `us-east-1` covering the platform domain (and wildcard if subdomains are used) | CloudFront only accepts certificates from `us-east-1`; HTTPS is mandatory |
| 5 | **Brand assets** | Logo (SVG preferred, PNG fallback), favicon (`.ico`/PNG), color palette (primary/secondary hex values), fonts (license-compatible web fonts if custom) | Per-customer theming: login page, learner app, admin console, certificates, and `theme.css` are generated from these assets |
| 6 | **Support contact email** | A monitored mailbox (e.g. `support@client.com`) shown to learners on login and error pages | End users need a visible help channel; also used for platform notifications |
| 7 | **Technical contact** | Named person with access to DNS, AWS, and the ability to approve technical changes during deployment | Deployment weeks require fast decisions (DNS cutover, firewall rules, integration keys) |
| 8 | **Business sign-off contact** | Named person authorized to approve go-live, content acceptance, and invoicing milestones | Go-live is a business decision; validation sign-off gates the final cutover |
| 9 | **Google Workspace admin access** *(only if using the integration)* | A super-admin or delegated admin account plus consent to create a service account / OAuth client | Required to enable Google-based features (SSO, document embedding, calendar) |

## How to deliver

- Credentials and keys: via a secure channel (password manager share, Secrets Manager), **never by plain email or committed to a repository**.
- Brand assets: a single ZIP or shared drive folder with source files (SVG/vector preferred).
- Contacts: names, emails, phone numbers, and expected response times.

## After handover

The deployment team confirms each item, stores credentials in the config bucket/Secrets Manager, and schedules the deployment window (see the [Deployment Guide](deployment-guide.md)).
