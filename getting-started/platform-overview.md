# 🧭 Why LXS Univo?

A complete, production-grade learning platform you own end to end: live in **your** AWS account in hours, with no per-seat license fees.

{% hint style="success" %}
**The promise:** learners, coaches, admins and authors each get a purpose-built surface; you keep the code, the data and the keys. Everything below is running in production today.
{% endhint %}

## ⚡ The 30-second version

LXS Univo is **5 cooperating systems**, each independently deployable and replaceable:

| Component | Repo | What it does |
|---|---|---|
| 🎓 Learner app | `lms-mfe-app` | Where learners take courses, chat with coaches, track progress |
| 🛠️ Admin console | `lms-mfe-admin` | User management, catalog, licenses, theming |
| 🔐 Auth service | `lms-mfe-auth` | Login, registration, password reset, JWT issuance |
| 📚 Course engine | The course engine (Studio, LMS) | Courseware, Studio authoring, xBlocks, SCORM/LTI |
| 🏗️ Infrastructure | `lms-terraform` | The entire AWS stack as code, auditable by your IT team |

## 💡 Why not a legacy LMS?

| | **LXS Univo** | Moodle | Docebo / SaaS LMS | Custom build |
|---|---|---|---|---|
| **You own the infrastructure** | ✅ Your AWS account | ⚠️ Self-host or MoodleCloud | ❌ Vendor cloud | ✅ (if you finish it) |
| **Per-user license fees** | ✅ None | ✅ None | ❌ Per-seat pricing | ✅ None |
| **Modern UX (React SPA)** | ✅ 3 dedicated apps | ❌ Theme-based | ⚠️ Fixed vendor UI | ✅ (years of work) |
| **Course engine maturity** | ✅ Proven engine, mature ecosystem | ✅ Mature | ✅ Mature | ❌ Build it yourself |
| **Chat + live sessions built-in** | ✅ Pusher + Jitsi | ⚠️ Plugins | ⚠️ Add-ons | ❌ DIY |
| **Time to production** | 🚀 Hours (proven playbook) | Weeks to months | Weeks (but locked in) | 12+ months |
| **Exit cost** | ✅ Zero: you own everything | ⚠️ Migration effort | ❌ Data export only | ✅ |

{% hint style="info" %}
**The short version:** SaaS LMSes rent you a platform; LXS Univo hands you one. Moodle gives you the engine but not the experience. A custom build gives you both, eventually.
{% endhint %}

## 🏗️ The big picture

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
   │  Course engine │     │  MySQL (RDS)   │       │  DocumentDB    │
   │  (courses,     │     │  (platform     │       │  (course       │
   │   Studio,      │     │   data)        │       │   content)     │
   │   xBlocks)     │     │                │       │                │
   └────────────────┘     └────────────────┘       └────────────────┘

   Supporting services: Pusher (realtime chat), Jitsi (live sessions),
   Vimeo Pro (video hosting), S3 (assets), SES (transactional email)
```

## ✅ Design principles

1. **🧩 Micro-frontends**: learner, admin and auth UIs are separate SPAs, deployed independently under one domain. A bug in one never takes down the others.
2. **🔌 API-first**: every UI talks exclusively to the GraphQL API. No UI couples directly to course-engine internals, so engine upgrades stay safe.
3. **🔒 Client-owned infrastructure**: everything deploys into the client's AWS account. You own the data, the infra and the keys.
4. **🔋 Batteries included**: chat, live sessions, video, SCORM, certificates and analytics are built-in, not plugins to buy.

{% hint style="warning" %}
**One thing to know up front:** only the auth MFE can log you in. The learner app and admin console validate the shared JWT on bootstrap and redirect to `/auth` when it's missing or expired. This is by design: one login surface, one security boundary.
{% endhint %}

## What's next?

- ⚡ [Quickstart](quickstart.md): run all three MFEs locally in 5 minutes
- 🤝 [For Clients](for-clients.md): what you get, what it costs, how delivery works
- 🏗️ [System Architecture](../architecture/system-architecture.md): how the pieces fit together
