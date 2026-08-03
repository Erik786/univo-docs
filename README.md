# 📚 LXS Univo — Documentation

**Learning anytime, anywhere — with the right content.** Product, platform & deployment documentation for LXS Univo, the production-proven LMS built on Open edX, deployable per client in 3 weeks.

[![Docs](https://img.shields.io/badge/Docs-live-success?logo=gitbook&logoColor=white)](https://github.com/Erik786/univo-docs)
[![GitBook](https://img.shields.io/badge/GitBook-synced-blue?logo=gitbook&logoColor=white)](https://github.com/Erik786/univo-docs)
[![Open edX](https://img.shields.io/badge/Open%20edX-Ulmo-02262B)](https://openedx.org)
[![React](https://img.shields.io/badge/React-17-61DAFB?logo=react&logoColor=white)](https://react.dev)
[![Terraform](https://img.shields.io/badge/IaC-Terraform-7B42BC?logo=terraform&logoColor=white)](https://terraform.io)
[![License](https://img.shields.io/badge/License-Proprietary-red)](#)

---

## 🧭 Part of LXS Univo

| Repo | Role |
|---|---|
| 🎓 [lms-mfe-app](https://github.com/Erik786/lms-mfe-app) | Learner app (`/app`) |
| 🛠️ [lms-mfe-admin](https://github.com/Erik786/lms-mfe-admin) | Admin panel (`/v2-admin`) |
| 🔑 [lms-mfe-auth](https://github.com/Erik786/lms-mfe-auth) | Auth MFE (`/auth`) |
| 🏗️ [lms-terraform](https://github.com/Erik786/lms-terraform) | AWS infrastructure as code |
| 👉 **[univo-docs](https://github.com/Erik786/univo-docs)** | Product & platform documentation (this repo) |

🌐 **Published site:** this repo syncs automatically to GitBook — the rendered documentation is the public LXS Univo docs site.

---

## 🚀 Why LXS Univo?

- **Proven in production** — serving learners today, not a prototype
- **Deployed in 3 weeks** — a repeatable, documented delivery playbook
- **Modern experience** — fast, mobile-first React interfaces (not legacy LMS UX)
- **Social learning built-in** — real-time chat, groups, peer networking
- **Offline-ready roadmap** — learning continues even with unstable connectivity
- **Your infrastructure** — deployed in YOUR AWS account, you own everything

## 🏗️ Platform at a glance

| Component | Technology |
|---|---|
| Learner app | React 17 SPA (micro-frontend) |
| Admin console | React 17 SPA (micro-frontend) |
| Auth service | React 17 SPA (micro-frontend) |
| Course engine | Open edX (Studio, LMS, xBlocks, SCORM) |
| API layer | GraphQL (serverless AWS Lambda) |
| Realtime | Pusher (chat & notifications) |
| Video | Vimeo Pro + Jitsi (live sessions) |
| Infrastructure | Terraform on AWS (EC2, RDS, DocumentDB, CloudFront, WAF) |

## 🗺️ Documentation map

- **New here?** Start with the [Platform Overview](getting-started/platform-overview.md)
- **A client?** See [What You Get](getting-started/for-clients.md) and [Client Prerequisites](deployment/client-prerequisites.md)
- **A developer?** Jump to the [Quickstart](getting-started/quickstart.md) and [Developer Guides](developer-guides/mfe-app.md)
- **Operations?** Go to [Deployment & Runbooks](deployment/deployment-guide.md)
- **Where are we going?** Read the [Product Roadmap](roadmap/README.md)

## 📖 How this documentation works

- **GitHub is the source of truth.** Every page in this repo is plain markdown, reviewed like code.
- **GitBook syncs automatically.** A GitHub → GitBook integration publishes every merge to `main` to the public docs site — no manual publishing step.
- **Contributing:**
  1. Create a branch, edit or add markdown pages (keep links relative, e.g. `getting-started/quickstart.md`).
  2. Open a Pull Request — content and structure (including `SUMMARY.md`, the GitBook table of contents) are reviewed there.
  3. Merge to `main` → the site updates itself within minutes.
- New page? Add it to `SUMMARY.md` or it won't appear in the GitBook navigation.

## 🌱 Clients & references

First enterprise deployment: **Baobab Banque Madagascar** (AMPELA Connect — financial education platform for women entrepreneurs).

---

*LXS Univo is developed and maintained by Pantheon Group. For sales inquiries: contact Erik / Joel Mamihery.*
