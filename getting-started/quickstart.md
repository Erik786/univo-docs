# Quickstart — Local Development

Get the three LXS Univo micro-frontends (MFEs) running on your machine. Each MFE is an independent Create React App project; you install and start them separately.

| MFE | Repo | Local path | Role |
|---|---|---|---|
| Learner app | `lms-mfe-app` | http://localhost:3000/app | Where learners take courses |
| Auth service | `lms-mfe-auth` | http://localhost:3000/auth | Login, password reset, JWT issuance |
| Admin console | `lms-mfe-admin` | http://localhost:3000/v2-admin | Platform administration |

> **Important:** only the auth MFE can log you in. The learner app and admin console have no login UI — on bootstrap they validate the JWT in `localStorage` and redirect you to `/auth` when it is missing or expired. Always log in through the auth MFE first.

## Prerequisites

- **Node.js** — Node 18+ works for all three apps (CI uses Node 18 for admin, Node 24 for app/auth).
- **npm** with the `--legacy-peer-deps` flag — the React 17-era dependency tree has peer ranges that modern npm rejects by default.
- **An `.env` file per MFE** — environment config is not in git. In CI it is downloaded from a config bucket; locally, copy your environment's template into the repo root as `.env` (ask your team lead for the dev template). Never commit it.
- **For `lms-mfe-auth` only:** OpenSSL 3 compatibility. `react-scripts` 5 fails on modern Node without the legacy provider. The repo's `start`/`build` scripts already set `NODE_OPTIONS=--openssl-legacy-provider`; if you invoke webpack outside those scripts, export it yourself:

```bash
export NODE_OPTIONS=--openssl-legacy-provider
```

## 1. Auth MFE (start here)

```bash
cd lms-mfe-auth
npm install --legacy-peer-deps
npm start
```

Open http://localhost:3000/auth/login and sign in with your dev account. A successful login stores the JWT in `localStorage` (shared across all MFEs on the same domain) and redirects you to the app matching your role.

## 2. Learner app

```bash
cd lms-mfe-app
npm install --legacy-peer-deps
npm start
```

Served at http://localhost:3000/app. On bootstrap it validates the JWT; without a valid token it redirects to `REACT_APP_AUTH_ENDPOINT`.

## 3. Admin console

```bash
cd lms-mfe-admin
npm install --legacy-peer-deps
npm start
```

Served at http://localhost:3000/v2-admin. Requires an admin/staff account logged in through the auth MFE.

## Environment files

All variables are CRA-style `REACT_APP_*`, inlined at build time. The essential ones for local development:

| Variable | Purpose |
|---|---|
| `REACT_APP_GQL_ENDPOINT` | GraphQL API endpoint (all data) |
| `REACT_APP_AUTH_ENDPOINT` | Auth MFE URL — redirect target when unauthenticated |
| `REACT_APP_APP_ENDPOINT` / `REACT_APP_ADMIN_ENDPOINT` | Cross-MFE URLs (post-login redirect, impersonation handoff) |
| `REACT_APP_SITE_NAME` | Tenant/site slug (Pusher events, S3 paths, theming) |
| `REACT_APP_PUSHER_KEY` / `REACT_APP_PUSHER_CLUSTER` | Realtime chat connection |
| `REACT_APP_BASE_URL` | Open edX LMS base URL (staff session, asset rewriting) |

If you prefer sourcing env vars from a file rather than a committed `.env`, the repos also support `npm run build:dev` (sources `./.env.development`) or `npm run build:prod` (sources `./.env.production`) — see each repo's README.

## Tests and lint

```bash
npm test           # Jest + React Testing Library (watch mode)
npm run lint       # ESLint (lms-mfe-app)
```

## Common pitfalls

- **`npm install` fails with peer-dependency errors** → you forgot `--legacy-peer-deps`.
- **Auth MFE build crashes with `error:0308010C:digital envelope routines::unsupported`** → set `NODE_OPTIONS=--openssl-legacy-provider`.
- **App bounces you back to login immediately** → your JWT is missing/expired, or `REACT_APP_AUTH_ENDPOINT` / `REACT_APP_GQL_ENDPOINT` in `.env` points at the wrong environment.
- **Logged in but admin console redirects away** → your account role is `USER`; admin/staff roles only.

Next: read the [System Architecture](../architecture/system-architecture.md) to understand how the pieces fit together.
