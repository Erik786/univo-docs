# Changelog

All notable changes to the platform are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/): each release lists changes under **Added**, **Changed**, **Fixed** and **Security**.

## [v2026.08], August 2026

### Security

- **Full security audit completed.** 75 findings identified and fixed across all 4 platform repositories.
- **Credential rotation.** Every secret and API key was rotated as part of the audit remediation.
- **History scrub.** Git history was scrubbed of all previously committed credentials.
- **Secrets moved to Secrets Manager.** No secrets remain in source control; all are injected at deploy time.

### Added

- **Public documentation launch.** This GitBook: platform overview, getting-started guides, product docs, API reference and public roadmap.

---

## Future entries

New releases are appended at the top of this page using this template:

```markdown
## [vYYYY.MM], Month YYYY

### Added
- New features and capabilities.

### Changed
- Changes to existing behavior.

### Fixed
- Bug fixes.

### Security
- Vulnerability fixes and hardening.
```
