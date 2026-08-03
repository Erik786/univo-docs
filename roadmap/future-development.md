# Future Development

The platform's architecture was designed so that each of these directions plugs in **without a rewrite**. Here's what's on the horizon and how we'll get there.

## 🤖 AI-Powered Learning

**Vision:** every learner gets a personal tutor; every content team gets an assistant.

- **Content recommendation** — "the right content" for each learner, based on progress, goals and peer behavior. Pluggable into the GraphQL API as a new resolver; no UI rewrite needed (recommendations surface in the existing Explore page).
- **AI tutoring chat** — extends the existing Pusher chat with an AI participant. Learners already know the interface.
- **Content generation for authors** — Studio-side assistant: quiz generation, summaries, translations (FR ↔ MG matters for our markets).
- **Approach:** spike-first (1 R&D bet/month per our operating cadence), then graduate to roadmap.

## 📱 Mobile App

**Vision:** learning anytime, anywhere — truly.

- **React Native / Expo** — reuses ~80% of the existing React knowledge and components from `lms-mfe-app`
- **Offline-first from day one** — the mobile app and the offline web capability share the same sync engine (see below)
- **Same GraphQL API** — zero backend changes required

## 🔌 Offline-First & Sync

**Vision:** full learning continuity in low-connectivity regions (rural Madagascar first, then everywhere).

- **Phase 1** — course content caching: download a course, learn offline, progress syncs when back online (service workers + IndexedDB + sync queue)
- **Phase 2** — full offline journeys with conflict-free sync (evaluating CRDT-based engines: ElectricSQL, PowerSync)
- **Design constraint honored today:** all client state already flows through one API layer, so the sync engine has a single integration point

## 👥 Social Learning 2.0

**Vision:** from a course platform to a learning community — starting with networking for women entrepreneurs.

- **Thematic groups** — creation, membership, moderation; extends the existing Pusher chat, doesn't replace it
- **Learner directory & profiles** — opt-in networking between learners
- **Peer accountability** — study groups, shared goals, cohort analytics for coaches

## 🧩 Extensibility

- **LTI 1.3** — deeper third-party tool integration (already partially supported via xBlocks)
- **Public API expansion** — webhooks + REST facade over GraphQL for client integrations (HR systems, CRMs)
- **Marketplace readiness** — multi-tenant SaaS variant for smaller clients who don't need a dedicated deployment

---

*Each item links back to the public [Roadmap](README.md) once it graduates from exploration to committed.*
