# Architecture Decision Records

Architecture Decision Records (ADRs) capture the significant technical decisions made on the platform: what was decided, why, what alternatives were rejected, and what the consequences are. Each ADR is a short, dated, immutable record — when a decision changes, we write a new ADR rather than editing history.

## Index

| ADR | Date | Status | Decision |
|---|---|---|---|
| [ADR-0001](ADR-0001-ulmo-baobab.md) | 2026-08-03 | accepted | Deploy Baobab directly on Open edX Ulmo (no Teak port) |
| [ADR-0002](ADR-0002-region-af-south-1.md) | 2026-08-03 | accepted | AWS region af-south-1 (Cape Town) for the Baobab deployment |
| [ADR-0004](ADR-0004-scale-200M.md) | 2026-08-03 | accepted (amended) | Repeatable-deployment architecture trajectory |

### ADR-0001 — Open edX Ulmo for Baobab

Current production runs Open edX Teak (Tutor v20); the latest upstream release is Ulmo (Tutor v21). Rather than porting Teak to Baobab and upgrading later — two migrations, two maintenance windows, double the risk — the decision is to deploy Baobab directly on **Ulmo**: one migration, the latest upstream Studio fixes, and a stable base for 12–18 months. Cost: the Terraform must be validated against Tutor v21 on a sandbox account before deployment.

### ADR-0002 — AWS region af-south-1

The Baobab learners are in Madagascar. Compliance initially suggested eu-west-3 (Paris) and the original spec mentioned eu-north-1, but latency to end users was prioritized: **af-south-1 (Cape Town)** is the closest AWS region to Madagascar and keeps data on the African continent — a defensible sovereignty posture for a banking client. Fallback is eu-west-3 + CloudFront if specific services are unavailable in af-south-1; a validation `terraform plan` confirms service availability (DocumentDB, Lambda, SSM Session Manager) before commit.

### ADR-0004 — Repeatable deployment architecture

LXS Univo is a productized platform sold per deployment, so the architecture goal is **fast, repeatable, reliable deployments across many client instances** — not hyper-growth of a single instance. The strategy is "modular monolith+": every component can scale independently without rewrites, but complexity (microservices, Kubernetes) is only introduced when real load justifies it. The ADR defines four scale tiers (pilot ~250 USD/month → hyper 100K+ users) and five invariants that hold from day one: stateless everywhere, API-first, zero secrets in code, independently deployable MFEs, and measured unit cost per active learner.

## The ADR process

1. **Raise it.** Any significant decision — one that is expensive to reverse, affects multiple components, or a future team member would question — gets an ADR.
2. **Write it.** Copy the structure of the existing ADRs: Context (the forces at play), Decision (one sentence, unambiguous), Alternatives considered (with rejection reasons), Consequences (positive, negative, and required follow-ups).
3. **Number and date it.** Sequential `ADR-NNNN` numbering, `YYYY-MM-DD` date, a status (`proposed`, `accepted`, `superseded`, `rejected`), and named deciders.
4. **Review and accept.** Decisions are made by the CTO with the relevant stakeholders; the ADR records *who* decided.
5. **Never rewrite history.** If a decision changes, the old ADR is marked `superseded` and a new ADR is written. Amendments are appended and dated, as in ADR-0004.

ADRs live in this directory and are linked from the pages that depend on them, so the *why* is always one click from the *what*.
