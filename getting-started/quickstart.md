# ⚡ Get running in 5 minutes

Run all three LXS Univo micro-frontends (MFEs) on your machine, install, start, log in. Every command below is copy-pasteable.

{% hint style="success" %}
**The promise:** by the end of this page you have the learner app, admin console and auth service running locally, and you're logged in end to end.
{% endhint %}

## The three apps

| MFE | Repo | Local URL | Role |
|---|---|---|---|
| 🎓 Learner app | `lms-mfe-app` | http://localhost:3000/app | Where learners take courses |
| 🔐 Auth service | `lms-mfe-auth` | http://localhost:3000/auth | Login, password reset, JWT issuance |
| 🛠️ Admin console | `lms-mfe-admin` | http://localhost:3000/v2-admin | Platform administration |

{% hint style="warning" %}
**Always log in through the auth MFE first.** Only `/auth` has a login UI, the learner app and admin console validate the JWT in `localStorage` on bootstrap and bounce you to `/auth` when it's missing or expired.
{% endhint %}

## ✅ Prerequisites

- **Node.js 18+**, CI uses Node 18 for admin, Node 24 for app/auth; anything 18+ works locally
- **npm with `--legacy-peer-deps`**, the React 17-era dependency tree has peer ranges modern npm rejects by default
- **An `.env` file per MFE**, not in git; copy your environment's template into each repo root (ask your team lead for the dev template). **Never commit it.**

{% hint style="info" %}
**Auth MFE only:** `react-scripts` 5 needs OpenSSL 3 compatibility on modern Node. The repo's `start`/`build` scripts already set `NODE_OPTIONS=--openssl-legacy-provider`; if you invoke webpack outside those scripts, export it yourself.
{% endhint %}

## 🚀 Start the apps

{% tabs %}
{% tab title="🔐 1. Auth (start here)" %}
{% code title="terminal" %}
```bash
cd lms-mfe-auth
npm install --legacy-peer-deps
npm start
```
{% endcode %}

Open http://localhost:3000/auth/login and sign in with your dev account. A successful login stores the JWT in `localStorage` (shared across all MFEs on the same domain) and redirects you to the app matching your role.
{% endtab %}

{% tab title="🎓 2. Learner app" %}
{% code title="terminal" %}
```bash
cd lms-mfe-app
npm install --legacy-peer-deps
npm start
```
{% endcode %}

Served at http://localhost:3000/app. On bootstrap it validates the JWT; without a valid token it redirects to `REACT_APP_AUTH_ENDPOINT`.
{% endtab %}

{% tab title="🛠️ 3. Admin console" %}
{% code title="terminal" %}
```bash
cd lms-mfe-admin
npm install --legacy-peer-deps
npm start
```
{% endcode %}

Served at http://localhost:3000/v2-admin. Requires an admin/staff account logged in through the auth MFE.
{% endtab %}
{% endtabs %}

## 🔧 Environment files

All variables are CRA-style `REACT_APP_*`, inlined at build time. The essentials for local development:

| Variable | Purpose |
|---|---|
| `REACT_APP_GQL_ENDPOINT` | GraphQL API endpoint (all data) |
| `REACT_APP_AUTH_ENDPOINT` | Auth MFE URL, redirect target when unauthenticated |
| `REACT_APP_APP_ENDPOINT` / `REACT_APP_ADMIN_ENDPOINT` | Cross-MFE URLs (post-login redirect, impersonation handoff) |
| `REACT_APP_SITE_NAME` | Tenant/site slug (Pusher events, S3 paths, theming) |
| `REACT_APP_PUSHER_KEY` / `REACT_APP_PUSHER_CLUSTER` | Realtime chat connection |
| `REACT_APP_BASE_URL` | Course-engine LMS base URL (staff session, asset rewriting) |

💡 Prefer sourcing env vars from a file rather than a committed `.env`? The repos also support `npm run build:dev` (sources `./.env.development`) and `npm run build:prod` (sources `./.env.production`), see each repo's README.

## ✅ Tests and lint

{% code title="terminal" %}
```bash
npm test           # Jest + React Testing Library (watch mode)
npm run lint       # ESLint (lms-mfe-app)
```
{% endcode %}

## 🐛 Troubleshooting

{% hint style="warning" %}
**`npm install` fails with peer-dependency errors** → you forgot `--legacy-peer-deps`.

**Auth MFE crashes with `error:0308010C:digital envelope routines::unsupported`** → set `NODE_OPTIONS=--openssl-legacy-provider`.

**App bounces you back to login immediately** → your JWT is missing/expired, or `REACT_APP_AUTH_ENDPOINT` / `REACT_APP_GQL_ENDPOINT` in `.env` points at the wrong environment.

**Logged in but admin console redirects away** → your account role is `USER`; admin/staff roles only.
{% endhint %}

## What's next?

- 🧭 [Platform Overview](platform-overview.md), why the platform is built this way
- 🏗️ [System Architecture](../architecture/system-architecture.md), how the pieces fit together
- 🔧 [MFE Configuration](../configuration/mfe-configuration.md), the full environment variable reference
