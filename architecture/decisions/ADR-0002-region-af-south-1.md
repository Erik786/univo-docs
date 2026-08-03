# ADR-0002: Région AWS af-south-1 (Cape Town) pour le déploiement Baobab

**Date:** 2026-08-03 · **Status:** accepted
**Deciders:** CTO + Erik

## Context
Les utilisatrices (femmes entrepreneuses malgaches) sont à Madagascar. Le prerequis Baobab suggérait eu-west-3 (Paris) pour la conformité ; la spec d'origine mentionnait eu-north-1. Erik a tranché : latence prioritaire.

## Decision
Région principale **af-south-1 (Cape Town)** — la plus proche de Madagascar. Fallback : eu-west-3 + CloudFront si des services manquent dans la région.

## Alternatives considered
- eu-west-3 (Paris) — rejetée comme région principale : latence Europe-Madagascar élevée, les apprenantes ne sont pas en Europe. Reste le fallback conformité.
- eu-north-1 (Stockholm) — rejetée : encore plus loin, aucun avantage.

## Consequences
- Positif : meilleure latence pour la cible ; décision assumée et défendable face au RSSI (données bancaires en Afrique, souveraineté régionale).
- Négatif/coût : af-south-1 n'a pas tous les services AWS et est légèrement plus chère — vérifier DocumentDB, Lambda, SSM Session Manager au terraform plan.
- Suivi requis : terraform plan de validation des services disponibles (tâche kanban à créer).
