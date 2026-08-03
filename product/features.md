# Feature Catalog

LXS Univo serves four audiences: **Learners**, **Coaches**, **Admins** and **Authors**. Each audience has its own surface — the Learner app (`/app`), the Admin console (`/v2-admin`), the Auth service (`/auth`), and Open edX Studio for content creation.

## 🎓 Learner

| Feature | Description |
|---|---|
| Courseware player | Sequential course navigation built on Open edX xBlocks: html, problem, SCORM, LTI, google-document, Vimeo video |
| Progress tracking | Per-unit and per-course completion state, synced to the GraphQL API in real time |
| Certificates (PDF) | Downloadable PDF certificates on course completion |
| Coach chat | Real-time 1:1 messaging with an assigned coach (Pusher) |
| Analytics reports | Personal progress dashboards and exportable reports (Highcharts visualizations) |
| Offline download review | Downloaded course content can be reviewed offline; see [Offline-First](offline-first.md) |
| Internationalization | 6 locales: `uk`, `fr` (default), `de`, `es-419`, `zh-CN`, `ar` |
| Light/dark theme | User-selectable theme, persisted per account |

## 💬 Coach

| Feature | Description |
|---|---|
| Coach chat | Real-time conversation with assigned learners over Pusher channels |
| Learner follow-up | Cohort view of learner progress, completion and activity for proactive support |

## 🛠️ Admin

| Feature | Description |
|---|---|
| User management | Create, edit, deactivate users; role assignment |
| Group management | Organize learners into groups and cohorts |
| Course management | Assign courses, manage enrollments, monitor completion |
| Catalog management | Curate the course catalog visible to each audience |
| Licenses | Allocate and track seat licenses per client organization |
| Ghost / impersonation | Admins can view the platform as a specific user for support purposes (fully audited) |
| Settings | Platform configuration per deployment |
| Theming | Brand colors, logos and white-label options per client |

## ✍️ Author

| Feature | Description |
|---|---|
| Studio | Full Open edX Studio for course authoring — see [Content Creation with Studio](studio.md) |
| xBlock library | html, problem, scorm, lti, google-document, vimeo video components |
| SCORM import | Package and publish SCORM content directly into courses |

---

*Feature availability can vary per deployment and license tier. Contact your platform administrator for details.*
