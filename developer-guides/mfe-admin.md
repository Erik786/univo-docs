# Admin Console (`lms-mfe-admin`)

Developer guide for the **administration panel micro-frontend** — a React SPA served under `/v2-admin` on each customer domain. Admins manage users, groups, catalogs, licenses, chat, and theming.

One of three sibling MFEs (`lms-mfe-app`, `lms-mfe-auth`, `lms-mfe-admin`), all talking to the same GraphQL backend and sharing session state via `localStorage` tokens.

## Architecture

```
┌────────────────┐   ┌────────────────┐   ┌────────────────────┐
│  lms-mfe-auth  │   │  lms-mfe-app   │   │  lms-mfe-admin     │  ← this app
│  (login/SSO)   │   │  (learner UI)  │   │  (admin panel)     │
└───────┬────────┘   └───────┬────────┘   └─────────┬──────────┘
        │  jwt / jwt-ghost in localStorage (shared)  │
        └────────────────────┼───────────────────────┘
                             ▼
                 GraphQL endpoint (REACT_APP_GQL_ENDPOINT)
                             ▲
        Pusher (chat events) │  AWS S3 (uploads + static assets)
```

- **State** — single Context + reducer store (`src/context/`): side effects in `action.jsx`, GraphQL documents in `query.jsx`. Requests go through an axios wrapper (`src/utils/api.jsx`) attaching the `jwt` from `localStorage`.
- **Auth flow** — no login UI here. `AuthProtector` fires a dashboard query on mount; on failure the browser redirects to `REACT_APP_AUTH_ENDPOINT`. Admins can "ghost" (impersonate) a learner, storing a `jwt-ghost` token consumed by `lms-mfe-app`.
- **Chat** — `pusher-js` subscription (`message` channel) feeds the admin inbox.
- **Uploads** — direct browser→S3 via `react-s3` (known tech debt; pre-signed URLs planned).
- **Theming** — MUI v5 light/dark themes; Tailwind utilities; react-i18next with 6 locales (`uk`, `fr`, `de`, `es-419`, `zh-CN`, `ar`), default `fr`.

## Tech stack

| Area | Choice |
|---|---|
| Framework | React 17, Create React App 5 + `react-app-rewired` |
| UI | MUI v5, Tailwind CSS 3, Sass, framer-motion |
| Routing | react-router-dom v6 (basename `/v2-admin`) |
| Data | axios → GraphQL over HTTP |
| State | React Context + useReducer |
| Editor | Froala WYSIWYG |
| Realtime | pusher-js 7 |
| Uploads | react-s3 → AWS S3 |
| Tables/charts | react-data-table-component, material-table, Highcharts |
| i18n | i18next / react-i18next (6 locales) |
| Forms | Formik + Yup |

## Quickstart

```bash
npm install
cp .env.example .env   # or fetch the env file from your config bucket
npm start              # dev server on http://localhost:3000/v2-admin
```

Node 18 (as pinned in `buildspec.yml`). You must log in through the auth MFE first — the app redirects there when no valid session exists.

## Environment variables

CRA-style `REACT_APP_*`, compiled in at build time. CI downloads `.env` from `s3://$BUCKET_CONF/$ENV_OBJECT_KEY`. Never commit a filled `.env`.

| Variable | Purpose |
|---|---|
| `REACT_APP_GQL_ENDPOINT` | GraphQL API endpoint (all data) |
| `REACT_APP_AUTH_ENDPOINT` | Auth MFE URL (redirect when unauthenticated) |
| `REACT_APP_APP_ENDPOINT` | Learner app URL (ghost handoff, sidebar link) |
| `REACT_APP_STUDIO_ENDPOINT` | Studio (course authoring) URL |
| `REACT_APP_ADMIN_BASE_PATH` | Router basename (`/v2-admin`) |
| `REACT_APP_SITE_NAME` / `REACT_APP_SITE_DISPLAY_NAME` | Tenant slug / display name (S3 paths, Pusher events) |
| `REACT_APP_S3_ENDPOINT` / `REACT_APP_S3_ASSET_ENDPOINT` | Base URLs for tenant assets (logos, icons, signatures) |
| `REACT_APP_S3_BUCKET_NAME` / `REACT_APP_S3_REGION` / `REACT_APP_S3_ACCESS_KEY` / `REACT_APP_S3_SECRET_KEY` | Direct uploads ⚠️ credentials ship to the browser — known tech debt |
| `REACT_APP_PUSHER_KEY` / `REACT_APP_PUSHER_CLUSTER` | Pusher chat connection |
| `REACT_APP_FROALA_KEY` | Froala editor license key |
| `REACT_APP_LOGO_URL` / `REACT_APP_FAVICON_URL` | Branding assets |
| `REACT_APP_JITSI_DOMAIN` | Jitsi domain for virtual meetings |
| `REACT_APP_BASE_URL` | Public base URL |

## Scripts

| Script | Description |
|---|---|
| `npm start` | Dev server |
| `npm run build` | Production build to `build/` (CRA ESLint runs during build) |
| `npm run build:dev` | Build sourcing `./.env.development` |
| `npm test` | Jest test runner (minimal coverage today) |
| `npm run upload*` | Manual S3 sync ⚠️ uses `--delete`; `upload_prod` goes straight to prod — use with care |

## Folder structure

```
├── buildspec.yml            # CodeBuild pipeline
├── config-overrides.js      # react-app-rewired webpack overrides
├── postcss.config.js, tailwind.config.js
├── public/                  # CRA static assets
└── src/
    ├── index.js             # bootstrap: router, context, i18n, AuthProtector
    ├── App.jsx              # route table (dashboard, users, catalog, chat, licenses, settings…)
    ├── AuthProtector.jsx    # session bootstrap + redirect-to-auth
    ├── context/             # index.jsx (reducer), action.jsx (side effects), query.jsx (GraphQL)
    ├── pages/               # feature screens (user, group, course, chat, license, tracking, theme…)
    ├── components/          # B* reusable blocks (tables, modals, pagination, badges…)
    ├── form/                # V* form controls (VDropZone, VFroala, VSelect…)
    ├── layouts/             # Header, SideNavbar, Footer, MainLayout
    ├── utils/               # api (axios), aws_s3, csv/html report exporters, string helpers
    ├── i18n/ + i18n.jsx     # locales: uk, fr, de, es-419, zh-CN, ar
    └── assets/              # light/dark MUI theme factories
```

## Common tasks

### Add a page / route

1. Create the screen under `src/pages/<feature>/` using existing `B*` components and `V*` form controls.
2. Register the route in `src/App.jsx`.
3. Add strings to **all 6 locales** in `src/i18n/messages` — they must stay in sync.
4. Verify light and dark themes.

### Add a GraphQL query

1. Add the document in `src/context/query.jsx`.
2. Add a side-effect function in `src/context/action.jsx` calling it via `src/utils/api.jsx`.
3. Wire state updates through the reducer in `src/context/index.jsx`.

### Run tests / lint

```bash
npm test         # Jest (add tests when touching src/utils or src/context)
npm run build    # CRA's ESLint runs during build — must pass clean
```

### Deploy to a customer

Canonical path is CI (CodeBuild via `buildspec.yml`): fetch customer `.env` from the config bucket → build → sync to `s3://<env>-common-apps-customers-bucket/<CUSTOMER>/v2-admin/ --delete` (prod assumes a cross-account STS role). The manual `upload*` scripts exist but bypass review — prefer CI.
