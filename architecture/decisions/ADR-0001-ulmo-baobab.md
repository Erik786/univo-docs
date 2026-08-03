# ADR-0001: Déploiement Baobab directement sur Open edX Ulmo (pas de portage Teak)

**Date:** 2026-08-03 · **Status:** accepted
**Deciders:** CTO (hermes-cto-master) + Erik

## Context
La prod actuelle tourne sur Open edX Teak (Tutor v20, déc. 2025). La dernière release est Ulmo (Tutor v21). Un déploiement chez Baobab Banque est planifié. Deux options : porter Teak tel quel puis upgrader plus tard, ou déployer directement sur Ulmo.

## Decision
Déployer chez Baobab directement sur **Ulmo (Tutor v21)** — une seule migration au lieu d'un upgrade prod + un redéploiement.

## Alternatives considered
- Porter Teak puis upgrader — rejetée : double migration, double risque, double fenêtre de maintenance.
- Upgrader la prod actuelle d'abord — rejetée : risque sur une prod en service sans nécessité immédiate (Teak est stable et supportée).

## Consequences
- Positif : une seule migration ; le Studio bénéficie des derniers fixes upstream ; base pérenne pour 12-18 mois.
- Négatif/coût : le terraform doit être testé avec Tutor v21 (petits ajustements possibles).
- Suivi requis : valider le terraform plan sur compte sandbox avant l'arrivée du sous-compte Baobab.
