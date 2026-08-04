# Future Development

The platform's architecture was designed so that each of these directions plugs in **without a rewrite**. Here's what's on the horizon, and how the existing stack (real-time chat, GraphQL API, micro-frontends) gets us there.

## 🤖 AI-first learning

**Vision:** every learner gets a personal tutor; every content team gets an assistant.

### AI Tutor (Q4 2026)

- **What:** a conversational assistant inside the existing chat, answering learner questions from course content, citing sources, adapting to the learner's level.
- **Why it matters:** learners get unstuck immediately, coaches keep their time for high-value follow-ups.
- **How we get there:** the assistant joins the existing chat as a participant; course content is indexed and served through the GraphQL API; ships as an MFE addition, no rewrite.

### AI Course Content Creation (Q4 2026)

- **What:** Studio-side assistant for authors: quiz generation from source material, lesson summaries, translation FR/MG, learning objective suggestions.
- **Why it matters:** authoring time drops from days to hours; the author reviews and validates instead of starting from a blank page.
- **How we get there:** a new Studio panel calling an AI service through the existing API layer; human validation stays mandatory before publishing.

### Adaptive learning (2027)

- **What:** AI-personalized course sequencing based on performance and goals.
- **Why it matters:** each learner sees the content they need next, not a fixed linear path.
- **How we get there:** completion and quiz data already exist; a recommendation service plugs into the GraphQL API as a new resolver, surfacing in the existing learner UI.

### AI voice + avatar content (2027)

- **What:** integration path for AI-generated video lessons (Synthesia, HeyGen).
- **Why it matters:** authors produce and update video lessons by editing text, without a recording studio.
- **How we get there:** generated videos enter through the existing video pipeline (hosted video, SCORM), no new delivery mechanism needed.

### Predictive analytics (2027)

- **What:** dropout risk detection and completion forecasting for coaches.
- **Why it matters:** coaches act on disengagement risk before it becomes dropout.
- **How we get there:** engagement events already flow through the platform; a scoring service feeds risk indicators into the existing admin dashboards.

**Approach for all AI items:** spike-first (1 R&D bet per month), then graduate to the committed roadmap.

## 📱 Mobile App (Q4 2026)

**Vision:** learning anytime, anywhere, truly.

- **React Native**, reuses most of the existing React components and knowledge
- **Offline-first from day one**, the mobile app and the offline web capability share the same sync engine
- **Same GraphQL API**, zero backend changes required

## 🌐 Offline-First & Sync (2027)

**Vision:** full learning continuity in low-connectivity regions, then everywhere.

- Course content caching first: download a course, learn offline, progress syncs when back online
- Full offline journeys with conflict-free sync (evaluating CRDT-based engines: ElectricSQL, PowerSync)
- **Design constraint honored today:** all client state already flows through one API layer, so the sync engine has a single integration point

## 👥 Social Learning 2.0 (Q4 2026)

**Vision:** from a course platform to a learning community.

- **Thematic groups**, creation, membership, moderation; extends the existing chat, doesn't replace it
- **Learner directory & profiles**, opt-in networking between learners
- **Peer accountability**, study groups, shared goals, cohort analytics for coaches

## 🎯 Micro-learning engine (2027)

**Vision:** progress in 5-minute moments.

- Adaptive bite-sized paths with spaced repetition
- Daily learning moments via push notifications
- Course content is already structured in small units; the engine sequences them from progress data

## 🧩 Extensibility

- **LTI 1.3**, deeper third-party tool integration (already partially supported via xBlocks)
- **Public API expansion**, webhooks + REST facade over GraphQL for client integrations (HR systems, CRMs)
- **Marketplace readiness**, multi-tenant SaaS variant for smaller clients who don't need a dedicated deployment

---

*Each item links back to the public [Roadmap](README.md) once it graduates from exploration to committed.*
