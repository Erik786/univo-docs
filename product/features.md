# 🎓 Feature Catalog

LXS Univo serves four audiences — **Learners**, **Coaches**, **Admins** and **Authors** — each with a purpose-built surface: the Learner app (`/app`), the Admin console (`/v2-admin`), the Auth service (`/auth`), and Open edX Studio for content creation.

{% hint style="success" %}
**One platform, four experiences.** Nobody wades through someone else's UI: learners learn, coaches coach, admins administer, authors author.
{% endhint %}

![LXS Univo admin dashboard — user, group, course and license management in one console](../.gitbook/assets/dashboard-admin.jpg)

## 🎓 Learner

| Feature | What it does |
|---|---|
| 📚 Courseware player | Sequential course navigation built on Open edX xBlocks: html, problem, SCORM, LTI, google-document, Vimeo video |
| 📈 Progress tracking | Per-unit and per-course completion state, synced to the GraphQL API in real time |
| 🏆 Certificates (PDF) | Downloadable PDF certificates on course completion |
| 💬 Coach chat | Real-time 1:1 messaging with an assigned coach (Pusher) |
| 📊 Analytics reports | Personal progress dashboards and exportable reports (Highcharts visualizations) |
| 📴 Offline download review | Downloaded course content can be reviewed offline — see [Offline-First](offline-first.md) |
| 🌍 Internationalization | 6 locales: `uk`, `fr` (default), `de`, `es-419`, `zh-CN`, `ar` |
| 🌗 Light/dark theme | User-selectable theme, persisted per account |

{% hint style="info" %}
💡 **Why it matters:** a learner who can message their coach, see their own progress and pick up where they left off on any device is a learner who finishes the course.
{% endhint %}

## 💬 Coach

| Feature | What it does |
|---|---|
| 💬 Coach chat | Real-time conversation with assigned learners over Pusher channels |
| 👀 Learner follow-up | Cohort view of learner progress, completion and activity for proactive support |

{% hint style="info" %}
💡 **Why it matters:** coaches intervene before learners drop out — the follow-up view surfaces who's stuck before they disengage.
{% endhint %}

## 🛠️ Admin

| Feature | What it does |
|---|---|
| 👥 User management | Create, edit, deactivate users; role assignment |
| 🧑‍🤝‍🧑 Group management | Organize learners into groups and cohorts |
| 📚 Course management | Assign courses, manage enrollments, monitor completion |
| 🗂️ Catalog management | Curate the course catalog visible to each audience |
| 🪪 Licenses | Allocate and track seat licenses per client organization |
| 👻 Ghost / impersonation | View the platform as a specific user for support purposes (fully audited) |
| ⚙️ Settings | Platform configuration per deployment |
| 🎨 Theming | Brand colors, logos and white-label options per client |

{% hint style="info" %}
💡 **Why it matters:** the admin console is where a platform earns its keep — licenses, catalogs and theming are self-service, so clients run their platform without filing tickets.
{% endhint %}

## ✍️ Author

| Feature | What it does |
|---|---|
| 🏗️ Studio | Full Open edX Studio for course authoring — see [Content Creation with Studio](studio.md) |
| 🧱 xBlock library | html, problem, scorm, lti, google-document, vimeo video components |
| 📦 SCORM import | Package and publish SCORM content directly into courses |

{% hint style="info" %}
💡 **Why it matters:** authors get the same battle-tested Studio that powers edX.org — no proprietary editor to learn, no content lock-in.
{% endhint %}

---

*Feature availability can vary per deployment and license tier. Contact your platform administrator for details.*

## What's next?

- 🌐 [Social Learning](social-learning.md) — chat, live sessions and community features in depth
- 📴 [Offline-First](offline-first.md) — how offline download review works
- 🏗️ [Content Creation with Studio](studio.md) — the author workflow end to end
