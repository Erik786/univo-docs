# System Architecture

LXS Univo is composed of cooperating, independently deployable systems: three React micro-frontends, a serverless GraphQL API, a mature open-source course engine, and managed data stores, all running in the client's own AWS account.

## High-level diagram

```
                        ┌─────────────────────────────────────┐
                        │           Learners / Admins          │
                        └───────────────┬─────────────────────┘
                                        │ HTTPS (CloudFront + WAF)
              ┌─────────────────────────┼─────────────────────────┐
              ▼                         ▼                         ▼
     ┌────────────────┐       ┌────────────────┐        ┌────────────────┐
     │  Learner App   │       │  Admin Console │        │  Auth Service  │
     │  (React MFE)   │       │  (React MFE)   │        │  (React MFE)   │
     │  /app          │       │  /v2-admin     │        │  /auth         │
     └───────┬────────┘       └───────┬────────┘        └───────┬────────┘
             │                        │                         │
             └────────────────────────┼─────────────────────────┘
                                      ▼  GraphQL over HTTPS (JWT)
                          ┌────────────────────┐        ┌────────────────┐
                          │   GraphQL API      │───────▶│  Course engine │
                          │   (AWS Lambda +    │        │  (EC2)         │
                          │   API Gateway)     │        │  courses, xBlocks│
                          └─────────┬──────────┘        └───────┬────────┘
            ┌───────────────────────┼───────────────────────────┤
            ▼                       ▼                           ▼
   ┌────────────────┐     ┌────────────────┐          ┌────────────────┐
   │  RDS MySQL     │     │  DocumentDB    │          │  S3            │
   │  (platform     │     │  (course       │          │  (assets,      │
   │   data)        │     │   content)     │          │   uploads)     │
   └────────────────┘     └────────────────┘          └────────────────┘

   Supporting services: Pusher (realtime chat), Jitsi (live sessions),
   Vimeo Pro (video hosting), SES (transactional email)
```

## Request flows

| Flow | Path |
|---|---|
| Static UI | Browser → CloudFront → S3 SPA bucket (per-customer prefix) |
| Data read/write | Browser → GraphQL API (Lambda behind API Gateway) → RDS MySQL / DocumentDB / course engine |
| Course content | GraphQL returns xBlock list → rendered by the learner app; SCORM/LTI/HTML rendered from stored content |
| Realtime chat | Browser ↔ Pusher (public `message` channel, per-user events) |
| File uploads | Browser → direct-to-S3 (per-tenant buckets/prefixes) |

## Authentication flow

1. The user signs in at the **auth MFE** (`/auth/login`).
2. The auth MFE calls the **GraphQL API** login mutation; on success it receives a **JWT** plus a role (`USER`, `STAFF`, `ADMIN`, `SUPERADMIN`).
3. For privileged roles, the auth MFE additionally establishes a session with the **course-engine LMS backend** (CSRF + `login_session` endpoints) so staff can use Studio and engine-administered features.
4. The JWT is stored in **`localStorage`**, shared by all MFEs under the same domain.
5. Every subsequent GraphQL request carries the JWT in the **`Authorization` header** (attached by a single axios wrapper in each MFE).
6. On bootstrap, the learner app and admin console validate the token with a dashboard query; on failure they redirect to `REACT_APP_AUTH_ENDPOINT`. The admin console's "ghost" (impersonation) feature stores a separate `jwt-ghost` token consumed by the learner app.

There is exactly one place where credentials are entered: the auth MFE. No other MFE has a login form.

## Data stores

| Store | Engine | Holds |
|---|---|---|
| RDS MySQL | AWS RDS | Course-engine platform data (users, enrollments, grades) |
| DocumentDB | AWS DocumentDB | Course content (MongoDB-compatible modulestore) |
| S3 | AWS S3 | Static MFE bundles, per-tenant assets, user uploads, logs |

## Realtime

Chat and notifications use **Pusher**. The MFEs subscribe with `pusher-js` to a public channel (`message`) and bind to per-user/per-site events derived from `REACT_APP_SITE_NAME`. Messages themselves are persisted via the GraphQL API; Pusher is the push channel only.

## Integrations

| Service | Purpose |
|---|---|
| Pusher | Realtime chat & notifications |
| Jitsi | Live/video sessions (`REACT_APP_JITSI_DOMAIN`) |
| Vimeo Pro | Course video hosting |
| AWS SES | Transactional email (password reset, notifications) |
| AWS S3 | Assets, uploads, static hosting |
| Stripe | Payments (dependency present, currently unused) |

See also: [Micro-frontends](micro-frontends.md) · [Infrastructure](infrastructure.md) · [Security](security.md)
