# Translations: structure and plan

English is the primary documentation language. GitBook supports space variants, so the French version will be a variant of this space, not a fork: both versions share the same page tree and URLs stay parallel.

## Current state

- All content is in English.
- No hard-coded locale assumptions were found in the docs: locale references describe the platform's own i18n support (react-i18next, 6 locales in the admin MFE), not the documentation language.
- The homepage (README.md) carries the line: *Documentation available in English, version française en cours.*

## Pages to translate FIRST (sales-facing)

These are the pages a prospect or client reads before a contract is signed. Translate them in this order:

1. `README.md` (homepage)
2. `product/for-clients.md` (if present) and the other sales-facing product pages
3. `product/features.md`
4. `roadmap/README.md`
5. `deployment/deployment-guide.md` (the "live in hours" claim is a sales argument)

## Pages to translate LATER (technical, developer-facing)

- `developer-guides/*`
- `api-reference/*`
- `architecture/*`
- `roadmap/future-development.md`, `roadmap/changelog.md`

Rationale: developers working on the platform read English; clients and decision-makers read French.

## Process

1. Create the French variant in GitBook (Space settings → Variants).
2. Translate pages in the order above, one page at a time.
3. Keep structure, tables, badges and links identical between variants; only prose changes.
4. When an English page is updated, flag the French counterpart for re-translation in the same commit message (e.g. `docs: features page update (FR re-translate needed)`).

*The full translation is a separate task; this note only prepares the ground.*
