# Micro-Frontend Architecture

The LXS Univo UI layer is not one SPA, it is **three independent micro-frontends**, each a separate Create React App (React 17) codebase, built, tested, and deployed on its own. A bug or deploy in one never takes down the others.

## The three MFEs

| MFE | Repo | Served under | Role |
|---|---|---|---|
| Learner app | `lms-mfe-app` | `/app` | Courseware, progress, chat, certificates, analytics |
| Auth service | `lms-mfe-auth` | `/auth` | Login, forgot/reset password, JWT issuance |
| Admin console | `lms-mfe-admin` | `/v2-admin` | User/content administration, theming, reporting |

Each MFE is:

- an **independent CRA 5 build** (with `react-app-rewired` overrides), its own `package.json`, tests, and CI pipeline;
- deployed as **static files to S3**, one prefix per customer: `s3://<env>-common-apps-customers-bucket/<CUSTOMER>/<path>/`;
- served **behind CloudFront** under a single customer domain, so `/app`, `/auth`, and `/v2-admin` appear as one site to the user.

## How they cooperate

**One shared session.** The auth MFE writes the JWT to `localStorage` on the shared domain. The learner app and admin console never log in themselves, on bootstrap they validate the token against the GraphQL API and redirect to `REACT_APP_AUTH_ENDPOINT` when it is missing or expired. Every API call attaches the JWT as the `Authorization` header.

**One shared API.** All three MFEs talk exclusively to the same GraphQL endpoint (`REACT_APP_GQL_ENDPOINT`). No MFE couples directly to course-engine internals, which keeps engine upgrades safe.

**Cross-MFE navigation via env.** URLs of sibling MFEs (`REACT_APP_APP_ENDPOINT`, `REACT_APP_ADMIN_ENDPOINT`, `REACT_APP_STUDIO_ENDPOINT`) are compile-time configuration, so each customer deployment links to its own instances.

## Per-customer builds

There is one codebase per MFE, but **one build per customer**. Environment variables (`REACT_APP_*`) are inlined at build time, so each customer gets a bundle stamped with its own:

- site name and display name (`REACT_APP_SITE_NAME`, `REACT_APP_SITE_DISPLAY_NAME`),
- API endpoints and cross-MFE URLs,
- branding (logo, favicon, `theme.css` loaded from S3 asset buckets at runtime),
- light/dark theme tokens.

In CI (AWS CodeBuild), the pipeline downloads the customer's `.env` from a config bucket, builds, and syncs `build/` to the customer's S3 prefix. A manual multi-customer script (`cloudfront.sh` in `lms-mfe-app`) can loop over all customer sites in one pass.

## Why this pattern

1. **Independent deployability**, ship an admin-console fix without touching the learner app.
2. **Blast-radius isolation**, a broken MFE does not take down login or courseware.
3. **Per-customer customization**, theming, endpoints, and feature config differ per deployment while the codebase stays single.
4. **Team autonomy**, each MFE has its own state pattern (Context+reducer for app/admin, Redux for auth) and release cadence.

## Known trade-offs

- Shared `localStorage` JWT means token handling rules (storage, expiry, rotation) must be consistent across all three repos, this is a reviewed, security-sensitive area (see [Security](security.md)).
- Environment config is build-time, so rotating an endpoint requires a rebuild/redeploy of the affected MFEs (fully automated in CI).
