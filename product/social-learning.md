# Social Learning

Learning sticks when it's social. LXS Univo ships with real-time communication built in — not bolted on.

## 💬 Coach Chat

Every learner can be assigned a coach. Chat is **real-time**, powered by **Pusher** over a public `message` channel — messages arrive instantly on both sides with no polling.

- 1:1 conversations between a learner and their coach
- Message history persisted via the GraphQL API
- Available directly in the Learner app (`/app`) — no external tool required

## 🗨️ Course Comments & Replies

Each course unit supports threaded discussion:

- Learners comment on course content in context
- Replies nest under the original comment
- Coaches and admins can participate to unblock learners

## 🎥 Live Sessions (Jitsi)

Live video sessions run on an integrated **Jitsi** instance:

- Coaches schedule and host live sessions with their cohort
- Sessions open inside the platform — no separate account or download
- Configured per deployment via the Jitsi domain setting

## 🧭 Guides & Onboarding

- In-app guides walk new learners through the platform on first login
- Coaches get a dedicated onboarding flow covering chat, follow-up and live sessions
- Admins can re-trigger onboarding for any user from the Admin console

## 🔭 Where this is going

Social Learning 2.0 is on the [public roadmap](../roadmap/README.md): thematic groups, learner networking and presence — all built on the existing Pusher chat foundation. See [Future Development](../roadmap/future-development.md).
