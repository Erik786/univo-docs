# Product Roadmap

LXS Univo evolves with every client deployment. This roadmap is public and committed, it's what you can rely on when choosing the platform.

## ✅ Shipped (current)

| Capability | Status |
|---|---|
| Learner, Admin & Auth micro-frontends | ✅ In production |
| Course engine (Studio, LMS, SCORM) | ✅ In production |
| Real-time chat (Pusher) | ✅ In production |
| Live sessions (Jitsi) | ✅ In production |
| Video courses (Vimeo Pro) | ✅ In production |
| SCORM support | ✅ In production |
| AWS automated infrastructure | ✅ Terraform |
| World-class security baseline | ✅ Hardened Aug 2026 (full audit) |

## 🔨 In progress, Q3 2026

| Item | Description | Target |
|---|---|---|
| **First enterprise deployment** | Rollout at a leading African financial institution (financial education platform for women entrepreneurs) | 3 weeks from AWS access |
| **Public documentation (this GitBook)** | Full platform docs + API reference | Q3 |
| **Pre-signed S3 uploads** | Removes direct browser credentials, security hardening | Q3 |
| **Google Auth** | Optional SSO alongside local auth (conflict-tested) | Q3 |

## 📅 Next, Q4 2026

| Item | Description | Value |
|---|---|---|
| **Social Learning 2.0** | Enriched chat: thematic groups, learner networking, presence, built on existing Pusher chat | Engagement & retention |
| **Studio Reliability Program** | Hardened content creation: monitoring, save-failure elimination, SCORM pipeline improvements | Content team productivity |
| **Offline-First (phase 1)** | Course content available offline with automatic sync, for low-connectivity regions | Reach: rural learners |

## 🔭 Future, 2027

| Item | Description | Value |
|---|---|---|
| **Offline-First (phase 2)** | Full learning journeys offline, conflict-free sync (CRDT-based) | True "learning anywhere" |
| **AI Learning Assistant** | Content recommendation ("the right content"), tutoring chat, auto-summaries, built on our learning data | Personalization at scale |
| **Mobile App** | React Native app, synced with desktop (reuses 80% of existing React codebase) | Anytime, anywhere |
| **Marketplace / Multi-tenant SaaS** | Optional hosted offering for smaller clients | New revenue stream |

## How we prioritize

Every roadmap item is scored on:
1. **Client value**, does it help deploy and sell?
2. **Learner impact**, does it improve outcomes?
3. **Engineering leverage**, does it make the next deployment faster?

Roadmap items graduate from exploration → committed only after a technical spike validates feasibility.

*Last updated: 2026-08-03*
