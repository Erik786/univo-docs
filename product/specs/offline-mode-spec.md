# Offline Mode — Engineering Specification

> Companion to [Offline-First Learning](../offline-first.md). That page is the client-facing "why and scope"; this document is the "what and how" for implementation. Status: draft spec, Phase 1 target Q4 2026.

## 1. Goal and non-goals

**Goal:** a learner downloads a course once, learns fully offline, and their progress syncs automatically and safely when connectivity returns. No lost work, no conflicts that require the learner to resolve anything.

**Non-goals for Phase 1:**
- No real-time collaboration offline (chat, live sessions stay online-only)
- No SCORM/xAPI interactive playback offline (Phase 2)
- No background prefetch of courses the learner did not explicitly download

## 2. Architecture overview

The platform already funnels all client state through a single GraphQL API. Offline mode inserts three client-side components and one server-side endpoint family:

- **Service worker**: intercepts app and content requests, serves cached responses when offline, queues mutations.
- **IndexedDB store**: the durable on-device copy of course content and pending progress events.
- **Sync queue**: an ordered, persistent queue of progress mutations recorded while offline.
- **Server sync endpoints** (new GraphQL mutations/queries): accept batched progress events with idempotent application, and serve content manifests for download.

The future mobile app reuses the same sync engine and the same server endpoints; only the storage driver differs (native SQLite instead of IndexedDB).

## 3. Data model

### What gets cached per downloaded course
| Entity | Source | Cached | Notes |
|---|---|---|---|
| Course structure (outline, sections, units) | GraphQL `course(id)` | Yes | Versioned by `contentVersion` |
| Static HTML/text xBlocks | GraphQL unit content | Yes | Rendered offline |
| Video + audio assets | CDN (assets subdomain) | Optional per learner | Respects storage quota |
| Progress events (completion, time-on-task) | recorded client-side | Queue only | Synced, not displayed from cache |
| Problem/assessment attempts | recorded client-side | Queued, marked provisional | Server re-validates on sync |
| Certificates / grades | server-computed | No (read-only online) | Offline shows last-known snapshot |

### Storage entries (IndexedDB object stores)
- `courses` — keyed by `courseId`, holds structure + `contentVersion`
- `assets` — keyed by URL hash, holds blobs with size + last-access time (for eviction)
- `syncQueue` — auto-increment, holds serialized mutations with `clientEventId`, `createdAt`, `attempts`
- `meta` — sync state: `lastSyncAt`, per-course `syncCursor`

## 4. Sync semantics

**Event identity:** every progress event carries a client-generated `clientEventId` (UUID). The server deduplicates on `(userId, clientEventId)`, making retries and replays idempotent.

**Ordering:** the queue is drained FIFO per course. Events for different courses may sync in parallel.

**Delta protocol:** the client sends events newer than its `syncCursor`; the server returns a new `syncCursor` and any server-side state the client missed (e.g. coach feedback posted while the learner was offline).

**Conflict resolution:**
- Completion/progress: **last-write-wins per (userId, blockId, field)** using server receipt time for the merged value. Completion is monotonic: once `completed=true`, it is never reverted by an older event.
- Attempts/scores: **highest score retained**, all attempts kept for the record.
- No interactive conflict resolution is ever surfaced to the learner in Phase 1.

**Failure handling:** failed events retry with exponential backoff (max 6 attempts) and then park in a `failed` state flagged in the UI, never silently dropped.

## 5. Server API deltas (GraphQL)

New mutations and queries, all authenticated and scoped to the calling user:

- `syncProgress(input: SyncBatchInput!): SyncBatchResult!`
  - Input: `courseId`, `baseCursor`, `events: [ProgressEventInput!]!` (each with `clientEventId`, `blockId`, `type`, `payload`, `clientTimestamp`)
  - Result: `newCursor`, `applied: Int`, `conflicts: [ConflictSummary!]`, `serverUpdates: [ServerEvent!]`
- `courseManifest(courseId: ID!): CourseManifest!`
  - Returns `contentVersion`, the list of units, asset URLs with sizes and hashes, for download and integrity checking.
- `downloadedCourses: [DownloadedCourseInfo!]!`
  - Server view of what the learner has registered as downloaded (for cross-device awareness in Phase 2).

All new types versioned under the existing GraphQL schema; no breaking change to current queries.

## 6. Storage quota and eviction

- Per-device budget configurable per deployment (default 2 GB).
- Eviction is LRU on the `assets` store, never on `syncQueue` or unsynced progress.
- The learner sees per-course size and can remove a download to free space.

## 7. UX states

- Download: progress bar with pause/resume; integrity check against manifest hashes.
- Offline indicator: persistent banner when offline, listing which courses are available.
- Sync status: per-course "synced / syncing / N changes pending / sync failed" state.
- Provisional attempts are visually marked until the server confirms them.

## 8. Acceptance criteria (Phase 1)

1. Learner downloads a course online, goes offline, completes units; on reconnect, all progress appears server-side with no manual action. Verified across 20 consecutive offline sessions in QA.
2. Duplicate sync (same queue replayed) produces no duplicate or reverted progress (idempotency proven by test).
3. An offline completion never reverts an existing online completion.
4. Storage quota is enforced; LRU eviction never deletes unsynced progress.
5. A failed sync is visible to the learner and recoverable without data loss.
6. Content integrity: a corrupted cached asset is detected via manifest hash and re-fetched on next online session.

## 9. Open decisions (to lock before build)

- Choice of sync transport for Phase 2 (CRDT engine: ElectricSQL vs PowerSync) — Phase 1 uses the simpler queue above.
- Whether problem re-validation on sync can change a score the learner already saw (flagged in UX).
- Per-deployment storage budget default.

> Related: [Offline-First Learning](../offline-first.md), [Future Development](../../roadmap/future-development.md).
