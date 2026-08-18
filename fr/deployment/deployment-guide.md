# 🚀 Guide de déploiement : le playbook de livraison

La plateforme est en ligne en quelques heures ; le déploiement entreprise complet (infrastructure, moteur de cours, MFEs, intégrations, contenu, formation, mise en production) s'achève en 15 jours ouvrés. Chaque phase comporte une porte de vérification.

{% hint style="success" %}
**La promesse :** suivez ce playbook jour par jour et vous terminez la semaine 3 avec une plateforme de production validée, monitorée et détenue par le client, pas une démo.
{% endhint %}

{% hint style="warning" %}
**Porte des prérequis :** tous les éléments des [Prérequis client](../../deployment/client-prerequisites.md) doivent être livrés avant le Jour 1. Un sous-compte AWS ou une délégation DNS manquant bloque tout en aval.
{% endhint %}

## 📅 Calendrier de livraison en un coup d'œil

| Phase | Ce qui se passe | Quand |
|---|---|---|
| 📝 **Commande** | Liste des plateformes confirmée, contacts admin désignés, assets de marque (logo, couleurs) collectés | **J0** |
| 🏗️ **Construction** | LMS configuré, tests internes, branding et personnalisation appliqués | **J0 → J+2** |
| 📦 **Livraison** | Plateforme transférée, licences admin et codes d'accès émis | **J+3** |
| 🛠️ **Formation admin** | Ateliers en visioconférence : configuration des comptes, attribution des licences, test de classe en direct | **J+5** |
| 🎓 **Formation formateur** | Ateliers sur l'authoring de cours : construction de parcours, import de contenu externe, quiz | **J+10** |
| 🤝 **Support continu** | En continu : demandes de support, aide à la personnalisation, configuration de la messagerie, pour toute la durée du contrat | **En continu** |

{% hint style="info" %}
💡 **Les dates s'adaptent à votre calendrier.** Chaque jalon ci-dessus peut glisser pour correspondre au planning de votre organisation et à la disponibilité de vos équipes, la séquence reste, les dates s'adaptent.
{% endhint %}

## 🏗️ Semaine 1, Infrastructure (Terraform)

1. **Jour 1, Prérequis & secrets.** Confirmer les prérequis ; créer le fichier macros `.env` client ; stocker les credentials dans Secrets Manager / bucket de configuration. *Vérification :* checklist validée ; `.env` téléversé dans `s3://<config-bucket>/<env>/`.
2. **Jour 2, Bootstrap apply** (une fois par compte) : VPC, ALB, WAF, buckets SPA, bastion. *Vérification :* `terraform output` dans le répertoire bootstrap retourne les IDs VPC/sous-réseau/SG.
3. **Jour 3, Plan plateforme.** Exécuter `./plan-generic-platform.sh` ; réviser le diff avec l'équipe. *Vérification :* le plan ne montre que les ressources attendues, aucune suppression.
4. **Jour 4, Apply plateforme** via le pipeline CodeBuild (`buildspec.yml`) ou `deploy-generic-platform.sh`. *Vérification :* `terraform output` retourne l'IP EC2, le domaine CloudFront, le DNS ALB ; instance EC2 `running`.
5. **Jour 5, Vérifier + documenter.** Santé des cibles ALB, RDS/DocumentDB accessibles, Secrets Manager peuplé, alarmes CloudWatch actives. *Vérification :* `aws elbv2 describe-target-health` → tous `healthy` ; alarmes à l'état `OK`.

{% code title="terminal, semaine 1" %}
```bash
cd infrastructure
./plan-generic-platform.sh       # dry run, toujours avant apply
./deploy-generic-platform.sh     # apply (le chemin canonique est CodeBuild)
terraform output                 # capturer les endpoints pour la semaine 2
```
{% endcode %}

{% hint style="info" %}
💡 **Planifiez avant d'appliquer, à chaque fois.** Le pipeline CodeBuild est le chemin canonique d'apply, les apply locaux sont la porte de secours, pas la norme.
{% endhint %}

## 🔌 Semaine 2, Moteur de cours + MFEs + intégrations

6. **Jour 6, Installer le moteur de cours** (dernière release) sur l'EC2 plateforme via les scripts bootstrap ; configurer le domaine, les connexions DB (RDS MySQL, DocumentDB). *Vérification :* services moteur up ; `curl https://<domain>/heartbeat` → `OK`.
7. **Jour 7, Configuration du moteur de cours :** configuration du site, thème, création d'utilisateurs, accès Studio. *Vérification :* connexion sur `/login` ; Studio accessible à l'endpoint studio.
8. **Jour 8, Builder & déployer les trois MFEs** (auth, app, admin) via leurs pipelines CodeBuild avec l'env client. *Vérification :* `curl -I https://<domain>/auth` `/app` `/v2-admin` → `200` ; le flux de connexion fonctionne de bout en bout.
9. **Jour 9, Intégrations :** Pusher (clés chat dans les envs MFE), Jitsi (`REACT_APP_JITSI_DOMAIN` / Jitsi auto-hébergé), Vimeo Pro (token API dans la config plateforme). *Vérification :* envoyer un message chat entre deux sessions ; rejoindre une room Jitsi ; lire une vidéo Vimeo intégrée.
10. **Jour 10, E-mail (SES) + flux transactionnels ;** smoke-test inscription, réinitialisation mot de passe, inscription. *Vérification :* e-mail de réinitialisation reçu ; un nouvel utilisateur peut s'inscrire à un cours de test.

{% code title="terminal, semaine 2" %}
```bash
ssh <bastion> && ssh <platform-ec2>
tutor local status && tutor local logs --tail=100 lms
aws codebuild start-build --project-name <mfe-project>   # par MFE
```
{% endcode %}

{% hint style="info" %}
💡 **Testez les intégrations avec des flux réels, pas des vérifications de config.** Un message chat qui arrive vaut mieux qu'une clé Pusher qui existe simplement.
{% endhint %}

## ✅ Semaine 3, Contenu, validation, transfert, mise en production

11. **Jour 11, Migration de contenu :** importer les exports de cours, téléverser les assets sur S3, vérifier les packages SCORM/LTI. *Vérification :* tous les cours visibles dans Studio ; les assets se chargent en HTTPS.
12. **Jour 12, Migration/création d'utilisateurs ;** comptes coach et admin ; attribution des licences. *Vérification :* connexions de test par rôle (apprenant, coach, admin).
13. **Jour 13, Checklist de validation complète** (ci-dessous) avec le contact technique du client. *Vérification :* chaque élément coché, preuves capturées.
14. **Jour 14, Corrections + transfert.** Résoudre les constats de validation ; walkthrough des runbooks avec les ops client. *Vérification :* document de transfert signé ; le client connaît les runbooks.
15. **Jour 15, Mise en production :** bascule DNS vers CloudFront, surveillance des dashboards, validation métier. *Vérification :* zéro erreur dans CloudWatch pendant 4 h post-bascule ; e-mail de validation reçu.

### ✅ Checklist de validation

- [ ] Connexion, déconnexion, réinitialisation mot de passe (tous rôles)
- [ ] Lecture de cours : HTML, problèmes, SCORM, LTI, vidéo
- [ ] Suivi de progression, certificats (génération PDF + logo)
- [ ] Chat (apprenant ↔ coach) en temps réel
- [ ] Rejoindre une session en direct Jitsi
- [ ] Admin : gestion des utilisateurs, catalogue, licences, thème
- [ ] E-mails délivrés (inscription, réinitialisation, notifications)
- [ ] Sauvegardes configurées et un test de restauration exécuté
- [ ] Alarmes CloudWatch déclenchées vers les bonnes cibles e-mail/SNS
- [ ] Thèmes clair + sombre, toutes les locales supportées

### 🚦 Mise en production

{% code title="terminal, bascule" %}
```bash
# Bascule : pointer le domaine client vers la distribution CloudFront
aws route53 change-resource-record-sets --hosted-zone-id <zone> --change-batch file://cutover.json
# Surveiller
aws cloudwatch describe-alarms --state-value ALARM
```
{% endcode %}

{% hint style="warning" %}
⚠️ **Plan de rollback :** rétablir l'enregistrement DNS vers la cible précédente. Conserver un TTL ≤ 300 s sur l'enregistrement de bascule pour qu'un rollback se propage en minutes, pas en heures.
{% endhint %}

## Et ensuite ?

- 📋 [Prérequis client](../../deployment/client-prerequisites.md), la porte avant le Jour 1
- ⚙️ [Runbooks d'exploitation](../../deployment/runbooks.md), ce que les ops client utilisent après le transfert
- 🏗️ [Architecture système](../../architecture/system-architecture.md), la plateforme que vous déployez
