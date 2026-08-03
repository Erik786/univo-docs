# ADR-0003: Orchestration multi-agents via kanban natif Hermes (pas de bus fichiers custom)

**Date:** 2026-08-03 · **Status:** accepted
**Deciders:** CTO + Erik

## Context
L'équipe d'agents (lead-dev, qa-reviewer, futurs) doit être orchestrée de façon bidirectionnelle et automatique. Un bus handoff custom (fichiers JSON + watcher Telegram) avait été construit en phase 1. Limitation : le CTO ne pouvait pas lead de A à Z sans relais humain.

## Decision
Adopter le **kanban natif Hermes** (SQLite board + dispatcher embarqué gateway + spawn automatique des profils + dépendances entre tâches) comme système d'orchestration principal. Le bus handoff reste comme couche de notification urgente.

## Alternatives considered
- Continuer avec le bus handoff custom — rejetée : dispatch manuel, pas de spawn à la demande, relais humain obligatoire.
- Construire une queue SQLite custom — rejetée : réinventer le kanban natif qui fait déjà tout (claims atomiques, retries, heartbeat, deps).

## Consequences
- Positif : orchestration bidirectionnelle complète ; agents spawmés à la demande (RAM/CPU plats — clé pour scaler à 15 agents sur 2 CPU) ; Erik relégué des relais aux décisions business.
- Négatif/coût : dépendance au dispatcher dans la gateway CTO (SPOF acceptable : Restart=always systemd).
- Suivi requis : watchdog cron 30 min (créé) ; métriques hebdo depuis kanban.db.
