# Integrations

LXS Univo integrates best-in-class services instead of reinventing them. The table below lists each integration, its role, and the environment variables used to configure it. **Never commit real values, all secrets live in the Secrets Manager.**

| Integration | Role | Env vars | Notes |
|---|---|---|---|
| **Pusher** | Realtime chat (coach ↔ learner) | `REACT_APP_PUSHER_KEY`, `REACT_APP_PUSHER_CLUSTER` | Public `message` channel; message history persisted via the GraphQL API |
| **Jitsi** | Live video sessions | `REACT_APP_JITSI_DOMAIN` | Self-hostable or Jitsi-as-a-Service; sessions open in-app |
| **Vimeo Pro** | Video hosting & streaming |, (embed via `vimeo` xBlock) | Player rendered with `@u-wave/react-vimeo`; no raw video stored in course assets |
| **Froala** | WYSIWYG rich-text editor | `REACT_APP_FROALA_KEY` | Used by the `html` xBlock editor in Studio surfaces |
| **Google Workspace** | Document embedding |, (via `google-document` xBlock) | Embeds Docs, Sheets and Slides directly in courseware |
| **AWS S3** | Assets & file uploads |, (server-side) | Direct-to-S3 browser uploads are being replaced by pre-signed URLs (post-audit hardening, Q3 2026) |
| **Stripe** | Payments |, | Dependency present in the codebase but currently unused, planned, see roadmap |

## Configuration principles

1. **No secrets in the repo.** Since the August 2026 audit, all credentials are stored in the Secrets Manager and injected at deploy time.
2. **`REACT_APP_*` variables are public by design**, they ship to the browser. They hold publishable keys and domains only, never private credentials.
3. **Per-deployment values.** Each client deployment (own domain) gets its own set of integration credentials, managed independently.

## Requesting a new integration

The GraphQL API is the single integration point. A public API expansion (webhooks + REST facade) for HR systems and CRMs is on the [roadmap](../roadmap/future-development.md). Contact the platform team to discuss requirements.
