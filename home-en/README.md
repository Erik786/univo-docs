---
description: 'Learning anytime, anywhere: the right content, on a platform you own.'
icon: house
layout:
  width: wide
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: false
  outline:
    visible: false
  pagination:
    visible: false
  metadata:
    visible: false
  tags:
    visible: true
  actions:
    visible: true
---

# LXS Univo

<button type="button" class="button primary" data-action="ask" data-icon="gitbook-assistant">Ask a question...</button>

<a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/getting-started/for-clients" class="button primary" data-icon="rocket-launch">Get started</a><a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/architecture/system-architecture" class="button secondary" data-icon="sitemap">Architecture</a><a href="https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/s/eCzPmE1qny5DTuC8eUOB/" class="button secondary" data-icon="books">Docs</a><a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/roadmap" class="button secondary" data-icon="map">Roadmap</a>

&#x20;

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="image">Cover image</th></tr></thead><tbody><tr><td><h4><i class="fa-graduation-cap" style="color:$primary;">:graduation-cap:</i></h4></td><td><strong>Learner-first</strong></td><td>Fast, mobile-first experience: courses, chat, certificates, live sessions.</td><td><a href="https://app.gitbook.com/o/DaDQkGCM7r2TghFSBFLQ/s/yE16Xb3IemPxJWydtPOj/">Documentation</a></td><td><a href=".gitbook/assets/Capture d’écran 2026-08-04 à 20.02.21.png">Capture d’écran 2026-08-04 à 20.02.21.png</a></td></tr><tr><td><h4><i class="fa-cloud" style="color:$primary;">:cloud:</i></h4></td><td><strong>Live in hours</strong></td><td>Platform deployed in your AWS account in hours. Full rollout in 3 weeks.</td><td><a href="https://app.gitbook.com/o/DaDQkGCM7r2TghFSBFLQ/s/yE16Xb3IemPxJWydtPOj/">Documentation</a></td><td><a href=".gitbook/assets/Capture d’écran 2026-08-04 à 20.02.31.png">Capture d’écran 2026-08-04 à 20.02.31.png</a></td></tr><tr><td><h4><i class="fa-lock" style="color:$primary;">:lock:</i></h4></td><td><strong>You own it</strong></td><td>Your infrastructure, your data, your keys. Zero per-seat license fees.</td><td><a href="https://app.gitbook.com/o/DaDQkGCM7r2TghFSBFLQ/s/M9ty6FYa3j98VSBHF9LN/">API Reference</a></td><td data-object-fit="cover"><a href=".gitbook/assets/Capture d’écran 2026-08-04 à 20.02.40.png">Capture d’écran 2026-08-04 à 20.02.40.png</a></td></tr></tbody></table>

&#x20;

### 🚀 Live in hours, not months

LXS Univo is delivered as infrastructure-as-code in **your own AWS account**. The same playbook runs for a small pilot and for a full enterprise rollout.

{% stepper %}
{% step %}
#### Check the prerequisites

AWS account, domain names, branding assets and admin contacts: everything to prepare before day one.

[Client prerequisites](https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/deployment-and-operations/client-prerequisites)
{% endstep %}

{% step %}
#### Provision the infrastructure

Terraform creates the AWS foundation (EC2, RDS, DocumentDB, CloudFront, WAF) in a versioned, repeatable way.

[Infrastructure (AWS)](https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/architecture/infrastructure)
{% endstep %}

{% step %}
#### Deploy the platform

Course engine, React micro-frontends, GraphQL API and realtime services are deployed and wired together.

[Deployment guide](https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/deployment-and-operations/deployment-guide)
{% endstep %}

{% step %}
#### Cut over DNS and go live

Run the go-live checklist, hand over the admin console, then onboard coaches and learners.

[Runbooks](https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/deployment-and-operations/runbooks)
{% endstep %}
{% endstepper %}

{% hint style="success" %}
A full enterprise rollout, branding, content and coach training included, typically completes in **3 weeks**.
{% endhint %}

### 🧭 Explore the platform

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>🎓 Learner App</strong></td><td>Mobile-first courseware player with progress tracking, certificates and coach chat.</td><td><a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/developer-guides/mfe-app">Learner App (lms-mfe-app)</a></td></tr><tr><td><strong>🎥 Virtual Class</strong></td><td>Live cohort sessions on integrated Jitsi, hosted inside the platform.</td><td><a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/product/features">Features</a></td></tr><tr><td><strong>💬 Social Learning</strong></td><td>Real-time chat, course comments, groups and peer networking, built in.</td><td><a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/product/social-learning">Social Learning</a></td></tr><tr><td><strong>📴 Offline-First</strong></td><td>Learning that keeps working when the network does not.</td><td><a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/product/offline-first">Offline-First Learning</a></td></tr><tr><td><strong>✍️ Studio &#x26; Authoring</strong></td><td>Create courses with Studio and the wider authoring ecosystem.</td><td><a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/product/studio">Studio, Content Creation</a></td></tr><tr><td><strong>🏗️ Architecture</strong></td><td>React micro-frontends, GraphQL on Lambda, Terraform on AWS.</td><td><a href="https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/architecture/system-architecture">System Architecture</a></td></tr></tbody></table>

### ⭐ What you get

{% columns %}
{% column %}
**For learners and coaches**

* Fast, mobile-first React interface, not legacy LMS UX
* Courseware player with progress tracking and certificates
* Real-time coach chat, course comments and thematic groups
* Live cohort sessions hosted inside the platform
* Offline-first learning on the roadmap for unstable connectivity
{% endcolumn %}

{% column %}
**For IT and delivery teams**

* Runs in your AWS account: your data, your keys
* Terraform-managed infrastructure, versioned and repeatable
* GraphQL API on serverless AWS Lambda
* Runbooks, monitoring and reliability guidance included
* No per-seat license fees
{% endcolumn %}
{% endcolumns %}

### 🏗️ Platform at a glance

| Layer          | Technology                                                     |
| -------------- | -------------------------------------------------------------- |
| Learner app    | React 17 SPA (micro-frontend)                                  |
| Admin console  | React 17 SPA (micro-frontend)                                  |
| Auth service   | React 17 SPA (micro-frontend)                                  |
| Course engine  | Mature open-source course engine (Studio, LMS, xBlocks, SCORM) |
| API layer      | GraphQL on serverless AWS Lambda                               |
| Realtime       | Pusher (chat and notifications)                                |
| Video          | Vimeo Pro and Jitsi (live sessions)                            |
| Infrastructure | Terraform on AWS (EC2, RDS, DocumentDB, CloudFront, WAF)       |

### 💡 Why LXS Univo

* **Proven in production.** Serving learners today, not a prototype.
* **Live in hours.** A documented, repeatable delivery playbook.
* **Modern experience.** Fast, mobile-first React interfaces.
* **Social learning built in.** Chat, groups and peer networking, not bolted on.
* **Offline-ready roadmap.** Learning continues with unstable connectivity.
* **You own it.** Your infrastructure, your data, your keys, every layer.

### ❓ Frequently asked

<details>

<summary>Where is the platform hosted?</summary>

In your own AWS account. You keep the infrastructure, the data and the keys, and there are no per-seat license fees.

</details>

<details>

<summary>How long does a full rollout take?</summary>

The platform itself is deployed in hours. A complete enterprise rollout, including branding, content and coach training, typically takes three weeks.

</details>

<details>

<summary>Which e-learning standards are supported?</summary>

The course engine is built on Studio, xBlocks and SCORM. See [E-Learning Standards](https://app.gitbook.com/s/eCzPmE1qny5DTuC8eUOB/api-reference/standards) for the details.

</details>

<details>

<summary>Is the documentation available in French?</summary>

Yes. The sales-facing pages (what you get, features, roadmap, deployment guide) are available in French in the Documentation under "Français". Technical pages (architecture, developer guides, operations, API) are in English.

</details>

### 📚 Keep reading

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>📖 Documentation</strong></td><td>Start with the platform overview and the getting-started guides.</td><td><a href="https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/sites/site_dXKPU/s/eCzPmE1qny5DTuC8eUOB/getting-started/readme">https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/sites/site_dXKPU/s/eCzPmE1qny5DTuC8eUOB/getting-started/readme</a></td></tr><tr><td><strong>⚡ Quickstart, local dev</strong></td><td>Run the stack locally and make your first change.</td><td><a href="https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/sites/site_dXKPU/s/eCzPmE1qny5DTuC8eUOB/getting-started/quickstart">https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/sites/site_dXKPU/s/eCzPmE1qny5DTuC8eUOB/getting-started/quickstart</a></td></tr><tr><td><strong>🔌 GraphQL API</strong></td><td>Endpoints, schema and integration patterns.</td><td><a href="https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/sites/site_dXKPU/s/eCzPmE1qny5DTuC8eUOB/api-reference/graphql">https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/sites/site_dXKPU/s/eCzPmE1qny5DTuC8eUOB/api-reference/graphql</a></td></tr><tr><td><strong>🗺️ Product roadmap</strong></td><td>See what is shipping next, and what is being explored.</td><td><a href="https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/sites/site_dXKPU/s/eCzPmE1qny5DTuC8eUOB/roadmap/roadmap">https://app.gitbook.com/o/etJyRgm4K52TNnDqf8GO/sites/site_dXKPU/s/eCzPmE1qny5DTuC8eUOB/roadmap/roadmap</a></td></tr></tbody></table>

&#x20;

&#x20;

&#x20;
