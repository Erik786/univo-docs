# Roadmap produit

LXS Univo évolue à chaque déploiement client. Cette roadmap est publique et engagée, c'est sur quoi vous pouvez compter en choisissant la plateforme. Elle est organisée AI-first : l'intelligence artificielle est le prochain axe d'investissement majeur, construit sur les fondations déjà en production (chat en temps réel, API GraphQL, architecture micro-frontend).

Légende : ✅ en production · 🔨 en cours · 🔭 exploration

## ✅ NOW : en production

| Capacité | Statut |
|---|---|
| Micro-frontends Apprenant, Admin & Auth | ✅ En production |
| Moteur de cours (Studio, LMS, SCORM) | ✅ En production |
| Chat en temps réel | ✅ En production |
| Sessions en direct (Jitsi) | ✅ En production |
| Cours vidéo (Vimeo Pro) | ✅ En production |
| Certificats & Open Badges | ✅ En production |
| Infrastructure AWS automatisée | ✅ En production, Terraform |
| Baseline sécurité | ✅ Durcie août 2026 (audit complet) |

## 🔨 NEXT : T4 2026

### 🤖 Tuteur IA

Un assistant d'apprentissage conversationnel vivant dans le chat existant. Il répond aux questions des apprenants en utilisant le contenu du cours lui-même, cite ses sources et adapte ses explications au niveau de l'apprenant.

- **Bénéfice :** un apprenant bloqué sur une leçon à 22 h obtient une réponse immédiate et sourcée au lieu d'attendre un coach.
- **Comment nous y arrivons :** le chat en temps réel existe déjà, le tuteur IA le rejoint comme participant. Le contenu des cours est indexé côté serveur et exposé à l'assistant via l'API GraphQL existante ; l'architecture MFE nous permet de livrer cela comme un nouveau participant au chat sans toucher au reste de la plateforme.
- **Statut :** 🔨 en cours

### 🤖 Création de contenu de cours par IA

Un assistant IA côté Studio pour les auteurs de contenu : génération de quiz à partir de documents sources, résumés de leçons, traduction de contenu (FR/MG), suggestions d'objectifs pédagogiques.

- **Bénéfice :** un auteur transforme un document source de 30 pages en un brouillon de quiz et un résumé en quelques minutes, et révise au lieu d'écrire à partir de zéro.
- **Comment nous y arrivons :** Studio est déjà un MFE React, l'assistant se branche comme un nouveau panneau. Le contenu est soumis à un service IA via la couche API existante ; l'auteur valide toujours avant publication.
- **Statut :** 🔨 en cours

### 📱 Application mobile

Application React Native qui se synchronise avec la plateforme desktop, apprentissage offline-first.

- **Bénéfice :** les apprenants continuent leurs cours dans le bus, sur le terrain, avec ou sans connectivité.
- **Comment nous y arrivons :** React Native réutilise la majeure partie du codebase React existant et la même API GraphQL, aucun changement backend requis.
- **Statut :** 🔭 exploration

### 👥 Apprentissage social 2.0

Groupes thématiques, réseautage entre apprenants, présence, construit sur le chat existant.

- **Bénéfice :** les apprenants s'entraident entre les sessions, les cohortes restent engagées entre les cours en direct.
- **Comment nous y arrivons :** étend l'infrastructure de chat en temps réel existante, ne la remplace pas.
- **Statut :** 🔭 exploration

## 🔭 FUTURE : 2027

### 🎯 Moteur de micro-learning

Parcours adaptatifs en petites doses, répétition espacée, moments d'apprentissage quotidiens via notifications push.

- **Bénéfice :** un apprenant continue de progresser en sessions de 5 minutes qui s'adaptent à une journée de travail.
- **Comment nous y arrivons :** le contenu des cours est déjà structuré en petites unités ; le moteur les séquence en fonction des données de progression servies par l'API GraphQL.
- **Statut :** 🔭 exploration

### 🧠 Apprentissage adaptatif

Séquencement de cours personnalisé par IA en fonction des performances et des objectifs de l'apprenant.

- **Bénéfice :** deux apprenants commençant le même cours suivent des parcours différents : chacun voit le contenu dont il a besoin ensuite, dans le bon ordre.
- **Comment nous y arrivons :** les données de complétion et de quiz existent déjà dans la plateforme ; un service de recommandation se branche sur l'API GraphQL comme un nouveau resolver, et s'affiche dans l'interface apprenant existante.
- **Statut :** 🔭 exploration

### 🎙️ Contenu IA voix + avatar

Chemin d'intégration pour des leçons vidéo générées par IA (Synthesia, HeyGen).

- **Bénéfice :** un auteur produit une leçon vidéo sans studio, et la met à jour en éditant du texte au lieu de réenregistrer.
- **Comment nous y arrivons :** la plateforme joue déjà de la vidéo hébergée (Vimeo) et des packages SCORM ; les vidéos générées entrent par le pipeline vidéo existant.
- **Statut :** 🔭 exploration

### 📊 Analytique prédictive

Détection du risque de décrochage, prévision de complétion pour les coachs.

- **Bénéfice :** un coach voit quels apprenants sont à risque cette semaine et peut agir avant qu'ils ne se désengagent.
- **Comment nous y arrivons :** les événements d'engagement et de progression circulent déjà dans la plateforme ; un service de scoring les consomme et affiche des indicateurs de risque dans les tableaux de bord admin existants.
- **Statut :** 🔭 exploration

### 🌐 Synchronisation offline-first complète

Synchronisation basée sur CRDT pour des parcours d'apprentissage complets hors ligne.

- **Bénéfice :** apprendre vraiment n'importe où : cours complets, quiz et suivi de progression sans connectivité, synchronisation automatique dès le retour en ligne.
- **Comment nous y arrivons :** tout l'état client transite déjà par une seule couche API, offrant au moteur de synchronisation un point d'intégration unique. Application mobile et web offline partagent le même moteur de synchronisation.
- **Statut :** 🔭 exploration

## Comment nous priorisons

Chaque élément de la roadmap est évalué sur :

1. **Valeur client**, est-ce que cela aide à déployer et à vendre ?
2. **Impact apprenant**, est-ce que cela améliore les résultats ?
3. **Levier d'ingénierie**, est-ce que cela rend le prochain déploiement plus rapide ?

Les éléments de la roadmap passent de 🔭 exploration à 🔨 en cours uniquement après qu'un spike technique a validé la faisabilité. Aucune date de lancement n'est engagée au-delà de la granularité du trimestre.

*Dernière mise à jour : 2026-08-04*
