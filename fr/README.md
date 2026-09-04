# 📚 LXS Univo

**Apprenez où que vous soyez, quand vous le souhaitez : le bon contenu, sur une plateforme qui vous appartient.** Le LMS éprouvé en production, en ligne en quelques heures.

[![Get started](https://img.shields.io/badge/%F0%9F%9A%80_Get%20started-FE7B49?style=for-the-badge)](../getting-started/quickstart.md)
[![View on GitHub](https://img.shields.io/badge/View_on_GitHub-181717?style=for-the-badge&logo=github)](https://github.com/Erik786/univo-docs)

*Documentation disponible en français, English version in progress.*

---

## Explorez la plateforme

<table>
  <tr>
    <td width="50%">
      <h3>🎓 Application apprenant</h3>
      <p>Interface React rapide et mobile-first. Lecteur de contenus pédagogiques, suivi de progression, certificats, chat avec les coachs, le tout dans <code>/app</code>.</p>
      <p><a href="../developer-guides/mfe-app.md">Guide développeur →</a></p>
      <img src="../.gitbook/assets/dashboard-admin.jpg" alt="Tableau de bord administrateur LXS Univo" />
    </td>
    <td width="50%">
      <h3>🎥 Classe virtuelle</h3>
      <p>Sessions en direct sur Jitsi intégré. Les coachs animent des appels vidéo de cohorte directement dans la plateforme, sans outil externe.</p>
      <p><a href="../product/social-learning.md">Apprentissage social →</a></p>
      <img src="../.gitbook/assets/virtual-class.jpg" alt="Classe virtuelle LXS Univo" />
    </td>
  </tr>
  <tr>
    <td width="50%">
      <h3>🏗️ Architecture</h3>
      <p>Micro-frontends React, GraphQL sur AWS Lambda, infrastructure gérée par Terraform. Vous maîtrisez chaque couche.</p>
      <p><a href="../architecture/system-architecture.md">Vue d'ensemble →</a></p>
    </td>
    <td width="50%">
      <h3>☁️ En ligne en quelques heures</h3>
      <p>Playbook de livraison reproductible : prérequis, Terraform apply, déploiement plateforme, bascule DNS, checklist de mise en production.</p>
      <p><a href="deployment/deployment-guide.md">Guide de déploiement →</a></p>
    </td>
  </tr>
  <tr>
    <td width="50%">
      <h3>🌱 Apprentissage social</h3>
      <p>Chat en temps réel avec les coachs, commentaires de cours, sessions en direct, fonctionnalités communautaires intégrées nativement.</p>
      <p><a href="../product/social-learning.md">Voir les fonctionnalités →</a></p>
    </td>
    <td width="50%">
      <h3>🗺️ Roadmap</h3>
      <p>Apprentissage offline-first, groupes thématiques, réseautage entre apprenants, découvrez les prochaines évolutions de la plateforme.</p>
      <p><a href="roadmap/README.md">Roadmap publique →</a></p>
    </td>
  </tr>
</table>

---

## Pourquoi LXS Univo ?

- **Éprouvé en production** : au service d'apprenants aujourd'hui, pas un prototype
- **En ligne en quelques heures** : un playbook de livraison reproductible et documenté ; déploiement entreprise complet en 3 semaines incluant branding, contenu et formation
- **Expérience moderne** : interfaces React rapides et mobile-first (pas une UX LMS legacy)
- **Apprentissage social intégré** : chat en temps réel, groupes, réseautage entre pairs
- **Roadmap offline-ready** : l'apprentissage continue même avec une connectivité instable
- **Votre infrastructure** : déployé dans VOTRE compte AWS, vous maîtrisez tout

## Vue d'ensemble de la plateforme

| Composant | Technologie |
|---|---|
| Application apprenant | React 17 SPA (micro-frontend) |
| Console d'administration | React 17 SPA (micro-frontend) |
| Service d'authentification | React 17 SPA (micro-frontend) |
| Moteur de cours | Moteur open-source mature (Studio, LMS, xBlocks, SCORM) |
| Couche API | GraphQL (AWS Lambda serverless) |
| Temps réel | Pusher (chat & notifications) |
| Vidéo | Vimeo Pro + Jitsi (sessions en direct) |
| Infrastructure | Terraform sur AWS (EC2, RDS, DocumentDB, CloudFront, WAF) |

## Cartographie de la documentation

- **Nouveau ici ?** Commencez par la [Vue d'ensemble de la plateforme](../getting-started/platform-overview.md)
- **Client ?** Consultez [Ce que vous obtenez](getting-started/for-clients.md) et les [Prérequis client](../deployment/client-prerequisites.md)
- **Développeur ?** Accédez au [Quickstart](../getting-started/quickstart.md) et aux [Guides développeur](../developer-guides/mfe-app.md)
- **Opérations ?** Rendez-vous sur [Déploiement & Runbooks](deployment/deployment-guide.md)
- **Où allons-nous ?** Lisez la [Roadmap produit](roadmap/README.md)

---

*LXS Univo est développé et maintenu par Index Group.
