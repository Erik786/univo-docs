# 🔌 E-Learning Standards — Support Matrix

LXS Univo is built on Open edX, so it speaks the standards the e-learning industry actually uses. This page shows exactly which ones, where they work, and how to use them — with an honest status for each.

{% hint style="success" %}
**TL;DR:** SCORM packages and LTI tools run in courses today. IMS Common Cartridge and QTI content imports through Studio. xAPI and LTI Provider mode are on the [roadmap](../roadmap/README.md) — we never claim support we don't have.
{% endhint %}

## 📊 Support matrix

| Standard | What it is (one line) | LXS status | How you use it |
|---|---|---|---|
| **SCORM 1.2 / 2004** | The classic ZIP package format for self-contained courses from authoring tools | ✅ **Supported** | Import the package into a course via Studio; learners launch it in the app's SCORM renderer |
| **IMS Common Cartridge** | A package format for moving whole courses between LMS platforms | ✅ **Supported** | Import/export courses through Open edX Studio's course import/export |
| **QTI** | A standard format for quizzes and question banks | ✅ **Supported** | Import quizzes into Studio via Open edX quiz import; questions become native problem xBlocks |
| **LTI 1.3** | A protocol for embedding external learning tools (labs, simulators, graders) inside a course | 🟡 **Partial** | The LTI xBlock embeds external tools into courses today; LTI *Provider* mode (other platforms embedding LXS content) is on the roadmap |
| **xAPI (Tin Can)** | A learning-record protocol that captures rich learner activity into a Learning Record Store | 🗺️ **Roadmap** | Planned as the backbone of advanced analytics — see [roadmap](../roadmap/README.md) |
| **H5P** | An open format for interactive HTML5 content (quizzes, timelines, interactive video) | 🔍 **Under evaluation** | Not yet integrated; evaluation tracked on the roadmap |

{% hint style="warning" %}
**Accuracy matters.** Statuses above are verified against the codebase — the SCORM and LTI renderers ship in the learner app (`lms-mfe-app`). "Partial" means consumer-side works, provider-side doesn't. "Roadmap" means it is not in the product today.
{% endhint %}

## 📦 SCORM — bring your existing courses

SCORM is how most organizations already own content: a `.zip` exported from Articulate, Captivate or iSpring.

**How it works in LXS:**

1. In **Studio**, open (or create) your course and add a SCORM unit.
2. Upload the `.zip` package exported from your authoring tool.
3. Publish — the unit appears in the learner app's courseware player, which renders SCORM content in its own sandboxed frame.
4. Completion and scores flow back through the xBlock and surface in learner progress and analytics.

{% hint style="info" %}
💡 **Why it matters:** your existing Articulate/Captivate library migrates without being rebuilt. See [Authoring Tools](../product/authoring-tools.md) for the tool-by-tool paths.
{% endhint %}

## 🧰 IMS Common Cartridge — move whole courses

Common Cartridge (`.imscc`) is the standard for porting an entire course structure between platforms. Open edX Studio's **course import/export** handles it: export from your previous LMS, import in Studio, and the course outline lands as native units.

## ❓ QTI — move question banks

QTI packages carry quizzes and item banks. Studio's quiz import converts them into native Open edX problem xBlocks, so imported questions behave like any other LXS problem: grading, analytics and coach follow-up included.

## 🔗 LTI 1.3 — embed external tools (partial)

LTI lets a course launch an external tool — a coding lab, a physics simulator, an external grader — inside the course frame, with the learner's identity passed securely.

**Today (consumer mode):** the LTI xBlock embeds external LTI 1.3 tools into LXS courses. Add the tool's launch parameters in Studio; learners use it without leaving the app.

**Roadmap (provider mode):** letting *other* platforms embed LXS content via LTI is planned but not shipped.

## 🧪 xAPI — rich learning analytics (roadmap)

xAPI records statements like "learner X completed simulation Y in 4 minutes" into a Learning Record Store. It is the standard we plan to use for deep, cross-activity analytics beyond completion/scores. Not available today — tracked on the [roadmap](../roadmap/README.md).

## 🧩 H5P — interactive content (under evaluation)

H5P is a popular open library of interactive widgets. We are evaluating integration options (embed vs. LTI). If interactive H5P content is a requirement for your deployment, tell your LXS contact — it feeds the prioritization.

## What's next?

- ✍️ [Authoring Tools ecosystem](../product/authoring-tools.md) — how content from Articulate, Captivate, Synthesia & co. lands in LXS
- 🎓 [Feature Catalog](../product/features.md) — everything the platform ships today
- 🗺️ [Product Roadmap](../roadmap/README.md) — where xAPI, LTI Provider and H5P stand
