# Contributing

Standards for contributing to any LXS Univo repository (MFEs, Terraform, docs).

## Commit messages — Conventional Commits

Format: `<type>(<optional scope>): <short imperative summary>`

| Type | Use for | Example |
|---|---|---|
| `feat` | New functionality | `feat(board): add SCORM completion tracking` |
| `fix` | Bug fixes | `fix(auth): redirect to ?next= after login` |
| `docs` | Documentation only | `docs: add terraform plan-only instructions` |
| `chore` | Tooling, deps, housekeeping | `chore: bump axios to 0.27.2` |
| `refactor` | Code change, no behavior change | `refactor(context): split action.jsx by domain` |
| `test` | Adding/fixing tests | `test(login): cover reset-link expiry` |
| `style` | Formatting only | `style: run eslint --fix` |
| `perf` | Performance improvements | `perf(dashboard): memoize course list` |

Keep commits focused: one logical change per commit.

## Branch & PR flow

1. Branch from `main`: `git checkout -b feat/my-change main`
2. Keep the branch small and rebased on `main`.
3. Open a PR with a clear description: what, why, how tested, screenshots for UI changes.
4. **QA review is required before merge** — every PR needs at least one reviewer approval; UI changes need a QA pass on a deployed build or local run.
5. Squash-merge once approved; delete the branch.

## Before opening a PR

```bash
npm run lint     # must pass (or npm run build, which runs CRA ESLint)
npm test         # must pass; add tests for logic changes in utils/context/redux
```

- Tests live next to sources (`*.test.jsx`). Cover login/reset flows, permission logic, and data transforms.
- No `console.log` debugging left in commits.

## Security rules

- **Never commit** `.env` files, `*.pem` keys, AWS credentials, JWTs, or filled `terraform.tfvars`. Environment config is distributed via the S3 config bucket, not git.
- **Sanitize HTML**: never inject API/user content with `dangerouslySetInnerHTML` without sanitizing first. Follow the escaping pattern in `src/pages/board/problems/RenderResponsePrompt.jsx` (lms-mfe-app).
- Changes to credential handling, token storage, redirect logic, or the password-reset flow require explicit review.
- Direct-to-S3 upload credentials (`REACT_APP_S3_*`) are known tech debt — do not expand their use; pre-signed URLs are the target design.

## Design standards

- **MUI first**: use the `sx` prop and theme tokens (spacing, palette, typography) rather than hardcoded values.
- **Tailwind utilities** for layout and one-off spacing, consistent with existing usage.
- **Light + dark themes**: every visual change must be verified against both themes (`src/assets/theme*`).
- **i18n**: no hardcoded UI strings. All user-facing text goes through i18next; keep **all locales in sync** (admin MFE: `uk`, `fr`, `de`, `es-419`, `zh-CN`, `ar`; auth MFE: EN/FR).
- Component conventions: reusable blocks `B*`, form controls `V*`.
