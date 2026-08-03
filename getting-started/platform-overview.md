# Platform Overview

LXS Univo is a complete learning platform composed of **5 cooperating systems**, each independently deployable and replaceable.

## The big picture

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
                          ┌────────────────────┐
                          │   GraphQL API      │
                          │   (AWS Lambda)     │
                          └─────────┬──────────┘
            ┌───────────────────────┼────────────────────────┐
            ▼                       ▼                        ▼
   ┌────────────────┐     ┌────────────────┐       ┌────────────────┐
   │  Open edX      │     │  MySQL (RDS)   │       │  DocumentDB    │
   │  (courses,     │     │  (platform     │       │  (course       │
   │   Studio,      │     │   data)        │       │   content)     │
   │   xBlocks)     │     │                │       │                │
   └────────────────┘     └────────────────┘       └────────────────┘

   Supporting services: Pusher (realtime chat), Jitsi (live sessions),
   Vimeo Pro (video hosting), S3 (assets), SES (transactional email)
```

## Design principles

1. **Micro-frontends** — learner, admin and auth UIs are separate SPAs, deployed independently under one domain. A bug in one never takes down the others.
2. **API-first** — every UI talks exclusively to the GraphQL API. No UI couples directly to Open edX internals, which keeps edX upgrades safe.
3. **Client-owned infrastructure** — everything deploys into the client's AWS account. You own the data, the infra, and the keys.
4. **Batteries included** — chat, live sessions, video, SCORM, certificates, analytics are built-in, not plugins to buy.

## Components

| Component | Repo | Role |
|---|---|---|
| Learner app | `lms-mfe-app` | Where learners take courses, chat, track progress |
| Admin console | `lms-mfe-admin` | Platform administration, user & content management |
| Auth service | `lms-mfe-auth` | Login, registration, password reset, JWT issuance |
| Course engine | Open edX (Tutor) | Courseware, Studio authoring, xBlocks, SCORM |
| Infrastructure | `lms-terraform` | The entire AWS stack as code |
