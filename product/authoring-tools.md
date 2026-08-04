# ✍️ Create Content with Your Favorite Tools

You don't create content *in* LXS unless you want to — you create it with the tools your team already knows, and LXS ingests the result. No vendor lock-in on content creation, ever.

{% hint style="success" %}
**TL;DR:** Articulate, Captivate and iSpring export SCORM → import via Studio. AI avatar videos from Synthesia/HeyGen upload through the Vimeo pipeline. Canva and Genially embed as HTML. Or author natively in Studio with xBlocks. Every path below is available today unless marked otherwise.
{% endhint %}

## 💡 Why an open ingestion model?

Most LMS vendors want you authoring inside their walled garden so you can never leave. LXS takes the opposite bet: **your content is your asset.** Keep your Articulate subscription, your Canva workflow, your AI video pipeline — LXS is the delivery, tracking and coaching layer on top.

## 🗺️ The ecosystem at a glance

| Tool | Best for | Output format | Integration path | Cost tier |
|---|---|---|---|---|
| **Articulate 360 / Rise** | Polished interactive courses, branching scenarios | SCORM 1.2/2004 `.zip` | SCORM import via Studio | 💰💰💰 Enterprise subscription |
| **Adobe Captivate** | Software simulations, responsive courses | SCORM `.zip` | SCORM import via Studio | 💰💰💰 Enterprise subscription |
| **iSpring Suite** | PowerPoint-based course conversion | SCORM `.zip` | SCORM import via Studio | 💰💰 Mid-range license |
| **Synthesia / HeyGen** | AI avatar presenter videos at scale | MP4 video | Upload via the Vimeo pipeline | 💰💰 Per-seat subscription |
| **H5P** | Open-source interactive widgets | H5P / embed | 🔍 Under evaluation — see [standards](../api-reference/standards.md) | 🆓 Open source |
| **Canva / Genially** | Visual explainers, infographics, interactive slides | Embed / HTML | Embed in a Studio HTML xBlock | 🆓–💰 Freemium |
| **Native Studio** | Assessments, discussions, structured courseware | xBlocks (HTML, problem, video, discussion) | Author directly — nothing to import | ✅ Included |

## 📦 SCORM tools — Articulate, Captivate, iSpring

These three cover the vast majority of corporate course production, and all follow the same path:

1. Author in your tool as usual.
2. Export a **SCORM 1.2 or 2004** package (`.zip`).
3. In **Studio**, add a SCORM unit to your course and upload the package.
4. Publish. Learners launch it in the app's SCORM renderer; completion and scores feed progress tracking and analytics.

{% hint style="info" %}
💡 **Tip:** prefer SCORM 2004 (4th edition) exports when your tool offers the choice — sequencing and score reporting are more reliable. Full details in [E-Learning Standards](../api-reference/standards.md).
{% endhint %}

## 🤖 AI avatar video — Synthesia & HeyGen

AI-generated presenter videos became the fastest way to produce training narration in 2025–26: type a script, pick an avatar and a language, get a broadcast-quality video in minutes. For programs like **AMPELA** — multilingual, content-heavy, constantly updated — this changes the economics of course production entirely.

**Integration path:** export the MP4 from Synthesia/HeyGen and add it to your course through LXS's **Vimeo video pipeline** — the same player, streaming quality and progress tracking as any other course video. No special handling, no iframe hacks.

{% hint style="success" %}
**Why it matters for AMPELA:** re-recording a 40-video curriculum in a new language goes from weeks of studio time to an afternoon of re-generating scripts.
{% endhint %}

## 🎨 Visual & interactive — Canva, Genially

Both tools publish embeddable HTML. Paste the embed into a Studio **HTML xBlock** and it renders inline in the course player — infographics, interactive slides, visual explainers.

{% hint style="warning" %}
**⚠️ Tracking caveat:** embedded third-party content displays beautifully but reports no scores. Pair it with a native problem xBlock if you need measurable completion.
{% endhint %}

## 🏗️ Native Studio authoring

For anything that needs grading, discussion or tight progress tracking, author natively with xBlocks:

- **HTML xBlock** — rich text, images, embeds
- **Problem xBlock** — multiple choice, numeric, open response; fully graded and reported
- **Video xBlock** — Vimeo-pipeline streaming with per-learner watch tracking
- **Discussion xBlock** — course forums tied to the unit

Native content is what coaches see in learner follow-up views and what analytics reports are built on.

## What's next?

- 🔌 [E-Learning Standards](../api-reference/standards.md) — the SCORM/LTI/xAPI support matrix
- ✍️ [Studio — Content Creation](studio.md) — deep dive on native authoring
- 🎓 [Feature Catalog](features.md) — what learners experience on the other end
