# ADR-0004: Architecture trajectory — repeatable deployments, not startup scale

**Date:** 2026-08-03 · **Status:** accepted (amended 2026-08-03: business model corrected)
**Deciders:** CTO + Erik (elite mandate)

## Context
~~The startup targets a 200M€ valuation~~ **CORRECTED:** LXS Univo is a productized platform sold per deployment (~75K€/client, Baobab = #1). The architecture goal is NOT hyper-growth of one instance — it's **fast, repeatable, reliable deployments across many client instances**.

## Decision
**Stratégie "modular monolith+" :** chaque brique doit pouvoir scaler indépendamment SANS réécriture, mais on n'introduit de la complexité (microservices, k8s) que lorsque la charge la justifie.

### Trajectoire par palier

| Palier | Utilisatrices | Architecture | Coût mensuel cible |
|---|---|---|---|
| Pilote Baobab | 100-1K | EC2 unique + RDS + DocumentDB (actuel) | ~250$ |
| Croissance | 1K-10K | EC2 auto-scaled ×2-3, RDS multi-AZ, Redis cache, CloudFront total | ~800$ |
| Scale | 10K-100K | ECS/EKS pour les MFE + API, edX reste EC2 (il ne scale pas horizontalement facilement), SQS pour async | ~3K$ |
| Hyper | 100K+ | Réévaluation complète — avec les revenus pour le financer | n/a |

### Invariants à respecter DÈS MAINTENANT (coûtent rien, payent au scale)
1. **Stateless partout** : aucune donnée utilisateur sur le disque des instances (tout en S3/RDS)
2. **API-first** : les MFE ne parlent qu'aux APIs — jamais de couplage direct à edX internals
3. **Config en env/Secrets Manager** : zéro secret en code (l'audit l'a rappelé douloureusement)
4. **Modularité des MFE** : chaque micro-frontend déployable indépendamment (déjà le cas ✅)
5. **Coût unitaire mesuré** : $/utilisatrice active/mois suivi dès le pilote (indicateur clé pour les investisseurs)

## Alternatives considered
- Microservices dès maintenant — rejetée : tue la vélocité d'une équipe de 3 agents + 2 humains. La complexité doit être achetée avec de la croissance réelle.
- Serverless total — rejetée : edX/Tutor ne se serverless-ise pas ; hybride pragmatique.

## Consequences
- Positif : pas de réécriture bloquante à prévoir ; histoire de scale crédible pour les investisseurs.
- Négatif : les paliers 2+ demanderont du devops dédié (profil `devops` à créer au palier 2).
- Suivi : réévaluer la trajectoire à chaque palier atteint.
