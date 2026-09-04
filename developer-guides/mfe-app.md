# Learner App (`lms-mfe-app`)

Developer guide for the **learner-facing micro-frontend**, a React SPA served under `/app` on each customer domain. Learners take courses (HTML, problems, SCORM, LTI, video), track progress, earn certificates, chat with coaches, and review analytics.

Each customer deployment is a separate build of the same codebase, differentiated only by an `.env` file.

## Architecture

```
┌────────────┐   JWT (Authorization header)   ┌──────────────────┐
│  This app  │ ─────────────────────────────▶ │  GraphQL API     │
│  (React    │   REACT_APP_GQL_ENDPOINT       │                  │
│   SPA)     │ ◀───────────────────────────── │                  │
└─────┬──────┘                                 └──────────────────┘
      │  Pusher (public channel "message")          ▲
      ├──────────────────────────────┐              │ course content / SCORM / LTI
      ▼                              ▼              │ rendered from raw HTML
┌────────────┐                ┌────────────┐  ┌─────┴───────────┐
│  Pusher    │                │ S3 buckets │  │ Auth service    │
│  (chat)    │                │ (assets &  │  │ (AUTH_ENDPOINT) │
└────────────┘                │  uploads)  │  └─────────────────┘
                              └────────────┘
```

- **Routing**, `react-router-dom` v6 under basename `REACT_APP_APP_BASE_PATH` (`/app`).
- **State**, custom Context + reducer store (`src/context/`); no Redux. GraphQL documents live in `src/context/graphql`.
- **API**, all calls go through one axios wrapper (`src/utils/api.jsx`) that attaches the JWT from `localStorage`.
- **Auth flow**, the app never logs in itself; on bootstrap it validates the token and redirects to `REACT_APP_AUTH_ENDPOINT` on failure.
- **Chat**, `pusher-js` subscribes to channel `message` and binds per-user events (`src/layouts/Header.jsx`).
- **Courseware**, xBlocks rendered by type (`html` / `problem` / `scorm` / `lti` / vimeo) in `src/pages/board/`; markdown/HTML via `marked` in `src/pages/common/HtmlRenderer.jsx`.
- **PDF / certificates**, `html2canvas` + `jspdf` + `react-to-pdf`.

## Tech stack

| Area | Choice |
|---|---|
| Framework | React 17, Create React App 5 + `react-app-rewired` |
| UI | MUI v5, Tailwind CSS 3, styled-components, Sass |
| Routing | react-router-dom v6 |
| State | React Context + reducer |
| API | GraphQL over HTTP via axios |
| Forms | Formik + Yup |
| Realtime | pusher-js |
| Reporting | Highcharts, html2canvas, jspdf, react-to-pdf |
| i18n | i18next / react-i18next (`src/i18n`) |
| Testing | Jest + React Testing Library |
| Linting | ESLint (`.eslintrc.js`) |

## Quickstart

```bash
npm install --legacy-peer-deps   # React 17-era peer ranges require this flag
npm start                        # dev server, app served under /app
```

Node: CI uses Node 24 (`buildspec.yml`); any modern Node works locally.

## Environment variables

All variables are CRA-style `REACT_APP_*` inlined at build time. CI downloads the `.env` from `s3://$BUCKET_CONF/$ENV_OBJECT_KEY`. Never commit a filled `.env`.

| Variable | Purpose |
|---|---|
| `REACT_APP_GQL_ENDPOINT` | GraphQL API endpoint for all data |
| `REACT_APP_AUTH_ENDPOINT` | Redirect target when the token is invalid/expired |
| `REACT_APP_BASE_URL` | Platform base URL (logout, asset rewriting) |
| `REACT_APP_APP_BASE_PATH` | Router basename (`/app`) |
| `REACT_APP_ADMIN_ENDPOINT` / `REACT_APP_STUDIO_ENDPOINT` | Links to admin / studio |
| `REACT_APP_SITE_NAME` / `REACT_APP_SITE_DISPLAY_NAME` | Customer site ID (Pusher events, S3 paths, certificates) |
| `REACT_APP_PUSHER_KEY` / `REACT_APP_PUSHER_CLUSTER` | Pusher chat connection |
| `REACT_APP_S3_BUCKET_NAME` / `REACT_APP_S3_REGION` / `REACT_APP_S3_ACCESS_KEY` / `REACT_APP_S3_SECRET_KEY` | S3 upload configuration (pre-signed URL migration in progress, see Contributing) |
| `REACT_APP_S3_ASSET_ENDPOINT` | CDN/S3 base for static assets |
| `REACT_APP_JITSI_DOMAIN` | Jitsi base URL for live sessions |
| `REACT_APP_FROALA_KEY` | Froala WYSIWYG license key |
| `REACT_APP_LOGO_URL` | Customer logo (certificates) |
| `REACT_APP_CONTACT_EMAIL` | Support contact |

## Scripts

| Script | Description |
|---|---|
| `npm start` | Dev server |
| `npm run build` | Production build to `build/` |
| `npm run build:prod` | Build sourcing `./.env.production` |
| `npm test` | Jest + React Testing Library |
| `npm run lint` | ESLint over `src/**/*.js(x)` |
| `npm run upload*` | Manual S3 sync (⚠️ uses `--delete`, never run against prod without review) |

## Folder structure

```
├── buildspec.yml          # CodeBuild pipeline (env fetch, build, S3 sync, prod assume-role)
├── cloudfront.sh          # Manual multi-customer build+deploy script
├── config-overrides.js    # react-app-rewired webpack fallbacks
└── src/
    ├── index.js           # Entry: providers (i18n, router, context)
    ├── App.jsx            # Routes + theme provider
    ├── AuthProtector.jsx  # Terms gate + bootstrap session check
    ├── routes.jsx         # Sidenav route definitions
    ├── context/           # Store: action.jsx, GraphQL documents, reducer
    ├── layouts/           # Header (Pusher chat wiring), MainLayout, Footer
    ├── pages/             # dashboard, assigned, explore, course, board (courseware player),
    │                      # chat, analysis (PDF reports), profile, settings, guide, common
    ├── components/        # Reusable B* components (tables, modals, PDF exporter)
    ├── form/              # V* form controls (VButton, VPassword, VFroala, …)
    ├── utils/             # api.jsx (axios+JWT), aws_s3.jsx, string, toast
    ├── i18n/              # Translations
    └── assets/            # Light/dark MUI themes
```

## Common tasks

### Add a page / route

1. Create the page component under `src/pages/<feature>/`.
2. Register the route in `src/App.jsx` (router table) and, if it appears in navigation, in `src/routes.jsx`.
3. Add UI strings to all locales in `src/i18n/`, never hardcode strings.
4. Style with MUI `sx`/theme tokens or existing Tailwind utilities; verify both light and dark themes.

### Add a GraphQL query

1. Add the query/mutation document in `src/context/graphql/`.
2. Add an action creator in `src/context/action.jsx` that calls it via `src/utils/api.jsx` (JWT is attached automatically).
3. Dispatch from the page via context; handle loading and error states.

### Run tests

```bash
npm test        # Jest watch mode; tests live next to sources (*.test.jsx)
npm run lint    # must pass before opening a PR
```

### Deploy to a customer

The canonical path is CI (CodeBuild via `buildspec.yml`): fetch customer `.env` from the config bucket → build → `aws s3 sync build/ s3://<env>-common-apps-customers-bucket/<CUSTOMER>/app/ --delete` (with prod STS assume-role when `APP_ENV=prod`). Manual deploys (`cloudfront.sh`, `upload*` scripts) exist but use `--delete`, only run them with review.
