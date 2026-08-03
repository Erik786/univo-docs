# Auth MFE (`lms-mfe-auth`)

Developer guide for the **authentication micro-frontend** — the login, forgot-password, and password-reset flows for the platform, served as a static bundle under `/auth` on each customer domain.

## Architecture

- Any protected MFE redirects unauthenticated users here (`/auth/login`, optionally with `?next=`).
- On successful sign-in the MFE:
  1. Authenticates against the **GraphQL API** (`REACT_APP_GQL_ENDPOINT`) → JWT + permission (`USER`, `STAFF`, `ADMIN`, `SUPERADMIN`).
  2. For privileged roles, additionally establishes a session with the Open edX LMS backend (`REACT_APP_BASE_URL`) via its CSRF + `login_session` endpoints.
  3. Redirects to the learner app (`USER`) or admin app (privileged roles).
- Password-reset links use `/auth/reset/:userId/:hash`, which forwards the parameters into the reset form.
- The bundle is **static** (CRA build output) deployed to per-customer S3 prefixes: `s3://<env>-common-apps-customers-bucket/<customer>/auth/`. Per-customer theming (logo, favicon, `theme.css`) loads from S3 at runtime via env vars.

Routes (`src/App.js`, basename `/auth`):

| Route | Purpose |
|---|---|
| `/` | Redirects to `/login` |
| `/login` | Multi-step login (email check → password → forgot/reset) |
| `/reset/:userId/:hash` | Password-reset entry point |
| `*` | 404 page |

## Tech stack

| Layer | Technology |
|---|---|
| UI | React 17, react-router-dom 6 |
| State | Redux 4 + redux-thunk, react-redux 8 |
| HTTP | axios (GraphQL via POST + REST for the LMS session) |
| Build | Create React App 5 + `react-app-rewired` (disables `crypto`/`stream` polyfills) |
| Misc | react-multi-carousel, `querystring` (form-encoding for the edX login POST) |
| Deploy | AWS CodeBuild → `aws s3 sync` |

## Prerequisites — OpenSSL legacy provider

**Node.js ≥ 17** (CI uses Node 24). `react-scripts` 5 uses webpack 4-era hashing, which **fails on OpenSSL 3** without the legacy provider. All `start`/`build` scripts set:

```bash
cross-env NODE_OPTIONS=--openssl-legacy-provider
```

If you run webpack outside these scripts, export it yourself:

```bash
export NODE_OPTIONS=--openssl-legacy-provider
```

Install with npm (`--legacy-peer-deps` required):

```bash
npm install --legacy-peer-deps
npm start          # http://localhost:3000/auth/login
```

## Environment variables

Compile-time `REACT_APP_*` values from `.env` (CI downloads it from the config bucket). Never commit real values.

| Variable | Description |
|---|---|
| `REACT_APP_SITE_NAME` | Customer/site slug; page title and theme CSS path |
| `REACT_APP_CONTACT_EMAIL` | Support email on the login page |
| `REACT_APP_LOGO_URL` / `REACT_APP_FAVICON_URL` | Branding assets |
| `REACT_APP_BASE_URL` | Open edX base URL — CSRF + `login_session` endpoints |
| `REACT_APP_APP_ENDPOINT` | Learner app URL (post-login redirect for `USER`) |
| `REACT_APP_ADMIN_ENDPOINT` | Admin app URL (post-login redirect for privileged roles) |
| `REACT_APP_GQL_ENDPOINT` | Platform GraphQL API (login, email-exists, reset) |
| `REACT_APP_S3_ASSET_ENDPOINT` | Bucket hosting carousel/illustration assets |
| `REACT_APP_S3_ENDPOINT` | Bucket hosting per-customer `theme.css` |
| `REACT_APP_TEST_JWT_TOKEN` | Static bearer token sent with GraphQL auth calls (⚠️ rotate; move to CI-fetched config) |

## Scripts

| Script | Description |
|---|---|
| `npm start` | Dev server (with `NODE_OPTIONS=--openssl-legacy-provider`) |
| `npm run build` | Production build to `build/` |
| `npm run build:dev` | Build sourcing `./.env.development` |
| `npm test` | CRA/Jest test runner |
| `npm run upload*` | Manual S3 sync (uses `--delete` — prefer CI) |
| `npm run eject` | CRA eject — avoid; `config-overrides.js` covers current needs |

## Folder structure

```
├── buildspec.yml          # CodeBuild pipeline (S3 deploy)
├── config-overrides.js    # react-app-rewired overrides
├── public/
│   └── index.html         # HTML shell; injects favicon + theme.css from env
└── src/
    ├── App.js             # Router (basename /auth)
    ├── messages.jsx       # EN/FR UI strings
    ├── i18n/              # i18n helpers
    ├── components/        # LoginCarousel, CarouselElement, Spinner
    ├── pages/             # Login, LoginFirstPage/SecondPage, ForgetPassword,
    │                      # ResetPassword, ResetPasswordReq, Page404
    └── redux/
        ├── store.jsx      # Redux store (thunk)
        ├── graphql.jsx    # GraphQL queries/mutations (login, email-exists, reset)
        ├── actions/       # login.action.jsx — axios calls + thunks
        └── reducers/      # auth reducer
```

## Common tasks

### Add a page / route

1. Create the page under `src/pages/`.
2. Register it in `src/App.js` (basename `/auth`).
3. Add strings to `src/messages.jsx` / `src/i18n/` for both EN and FR.

### Add a GraphQL query

1. Add the document in `src/redux/graphql.jsx`.
2. Add an action/thunk in `src/redux/actions/` and handle it in `src/redux/reducers/`.

### Run tests

```bash
npm test   # Jest; add React Testing Library coverage for any login/reset flow change
```

### Deploy to a customer

CI (CodeBuild via `buildspec.yml`): fetch `.env` from the config bucket → build → `aws s3 sync build/ s3://<env>-common-apps-customers-bucket/<CUSTOMER>/auth/ --delete` (prod assumes an STS role first).

## Security-sensitive area

Changes to credential handling, token storage, redirect logic, or the reset flow **require review**. Do not commit real `.env` values or AWS credentials. This app is the platform's front door — treat every change accordingly.
