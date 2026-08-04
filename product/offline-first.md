# Offline-First Learning

Connectivity should never decide who gets to learn. This page describes what works **today** and what's committed on the [roadmap](../roadmap/README.md).

## ✅ Available today

| Capability | Description |
|---|---|
| Course downloads | Learners can download course content for later review |
| Reviewing downloaded content | Downloaded materials remain readable in the app without a connection, useful for travel and low-bandwidth moments |
| PDF report export | Progress and analytics reports export as PDF, so records can be kept and shared offline |

## 🔨 Current scope, honestly

Today's offline support is **review-oriented**: it covers reading downloaded content and exporting reports. It is **not yet** a full offline learning mode. In particular:

- Progress made while offline does **not** sync back automatically yet
- Interactive content (problems, SCORM playback, chat) requires a connection
- Downloads are manual, per course, there is no background prefetch

## 📅 On the roadmap

| Phase | Scope | Target |
|---|---|---|
| Offline-First, Phase 1 | Course content caching: download a course, learn offline, progress syncs automatically when back online (service workers + IndexedDB + sync queue) | Q4 2026 |
| Offline-First, Phase 2 | Full learning journeys offline with conflict-free sync (evaluating CRDT-based engines: ElectricSQL, PowerSync) | 2027 |

The architecture is already prepared for this: all client state flows through a single GraphQL API layer, giving the future sync engine one integration point. The planned mobile app will share the same sync engine. See [Future Development](../roadmap/future-development.md) for details.
