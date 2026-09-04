# Social Learning 2.0 — Engineering Specification

> Companion to [Social Learning](../social-learning.md). That page documents what ships today; this document specifies the phase-2 build the client requested: learner-to-learner chat, add friends, and communities. Status: draft spec.

## 1. Goal and non-goals

**Goal:** turn the platform from a content library into a social learning space. Learners find each other, form friendships and groups, and learn from each other inside the platform, not on external tools.

**Requested scope (client):**
1. Chat between all learners (not only learner-to-coach)
2. Add friends / build a learner network
3. Communities: thematic groups learners create and join to learn from each other

**Non-goals for this phase:** public open forums visible outside the deployment, federation across deployments, voice/video group calls (live sessions already exist via Jitsi and stay coach-led).

## 2. Why the current chat foundation must change

Today, coach chat runs on a **single public Pusher `message` channel**. That design cannot carry social features:

- A public channel gives every connected client every message; there is no per-recipient routing and no permission boundary. Direct messages between two learners would be broadcast to all.
- There is no membership or group concept to gate who sees what.

Social Learning 2.0 therefore replaces the single public channel with **authenticated private and presence channels**, while keeping Pusher as the transport. The 1:1 coach chat migrates onto the same channel model, so there is one chat architecture, not two.

## 3. Channel model (Pusher)

| Channel | Pattern | Purpose |
|---|---|---|
| `private-user-{userId}` | private | direct messages addressed to one learner |
| `private-dm-{conversationId}` | private | a 1:1 conversation, members only |
| `private-group-{groupId}` | private | community/group messages, members only |
| `presence-group-{groupId}` | presence | group messages + who is online |
| `presence-global` | presence | optional deployment-wide online presence |

All private/presence channels require server-side auth via the Pusher auth endpoint, which checks membership before signing. Message payloads carry `messageId`, `conversationId`, `senderId`, `sentAt`; history is persisted via the GraphQL API, Pusher is delivery only.

## 4. Data model

- **Conversation**: `id`, `type` (`dm` | `group`), `createdAt`, optional `groupId`
- **ConversationMember**: `conversationId`, `userId`, `role` (`member` | `moderator` | `owner`), `joinedAt`, `lastReadAt`
- **Message**: `id`, `conversationId`, `senderId`, `body`, `sentAt`, `editedAt`, `deletedAt`
- **Friendship**: `userId`, `friendId`, `status` (`pending` | `accepted` | `blocked`), `createdAt`
- **Group (community)**: `id`, `slug`, `name`, `description`, `topic`, `visibility` (`open` | `request-to-join` | `invite-only`), `createdBy`, `createdAt`
- **GroupMember**: `groupId`, `userId`, `role` (`owner` | `moderator` | `member`), `status` (`active` | `pending` | `banned`), `joinedAt`

A group's `visibility` and the member `status` drive both who can read and which channels the auth endpoint will sign.

## 5. Feature behavior

**Learner-to-learner chat:** any learner can start a DM with another learner, subject to privacy settings and blocks. Creating a DM creates a `Conversation` + two `ConversationMember` rows and opens `private-dm-{id}`.

**Add friends:** a friend request creates a `pending` Friendship; the recipient accepts or declines. Accepted friends are surfaced for quick DM start and (optionally) presence visibility. Blocking severs the channel and hides presence both ways.

**Communities:** a learner creates a group (owner), others join per the group visibility. Each group has a default chat channel plus its discussion threads. Owners/moderators can edit details, pin messages, remove members, and moderate content.

## 6. Moderation and safety

- Report action on any message, routed to moderators/admins with the message context.
- Moderators can delete messages (soft delete, `deletedAt`) and remove/ban group members.
- Rate limiting on message send and friend requests to blunt spam.
- Admins have a global view for abuse review; all moderation actions are logged.

## 7. Notifications

- In-app unread badges per conversation and per group (from `lastReadAt`).
- Optional email/push digest for unread DMs and group mentions, throttled.
- `@mention` in a group triggers a targeted notification.

## 8. Migration from current chat

- Existing coach-chat history is preserved: coach conversations are re-homed as `type=dm` Conversations; their messages map onto the Message model.
- The public `message` channel is retired after cutover; clients fall back to polling history until they upgrade.
- Cutover is per deployment, behind a feature flag, with a rollback path.

## 9. Acceptance criteria

1. Two learners exchange DMs; messages deliver in real time to the intended recipient only, and persist across reload. Verified with 50 concurrent DM pairs in load test.
2. A message on `private-dm-{id}` or `private-group-{id}` is never deliverable to a non-member (auth endpoint rejects; proven by negative test).
3. Friend request accept/decline/block all behave per spec; blocked users cannot open a channel or see presence.
4. Group visibility rules are enforced for open, request-to-join and invite-only groups.
5. Reported content reaches a moderator queue and a moderation action is logged and reversible.
6. Historical coach-chat messages remain visible after migration (no data loss in cutover).

## 10. Open decisions (to lock before build)

- Presence scope: deployment-wide presence or friends/groups only.
- Push notification provider for mobile (aligns with the offline-mode mobile app).
- Message retention policy per deployment (banking compliance may require retention + export).
- Whether communities support file/image sharing in this phase or text only.

> Related: [Social Learning](../social-learning.md), [Offline Mode spec](./offline-mode-spec.md), [Future Development](../../roadmap/future-development.md).
