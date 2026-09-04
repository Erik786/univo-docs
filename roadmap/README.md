# Product Roadmap

LXS Univo evolves with every client deployment. This roadmap is public and committed, it's what you can rely on when choosing the platform. It is organized AI-first: artificial intelligence is the next major investment axis, built on the foundations already in production (real-time chat, GraphQL API, micro-frontend architecture).

Legend: ✅ live · 🔨 in progress · 🔭 exploration

## ✅ NOW: in production

| Capability | Status |
|---|---|
| Learner, Admin & Auth micro-frontends | ✅ Live |
| Course engine (Studio, LMS, SCORM) | ✅ Live |
| Real-time chat | ✅ Live |
| Live sessions (Jitsi) | ✅ Live |
| Video courses (Vimeo Pro) | ✅ Live |
| Certificates & Open Badges | ✅ Live |
| AWS automated infrastructure | ✅ Live, Terraform |
| Security baseline | ✅ Hardened Aug 2026 (full audit) |

## 🔨 NEXT: Q4 2026

### 🤖 AI Tutor

A conversational learning assistant living inside the existing chat. It answers learner questions using the course content itself, cites its sources, and adapts its explanations to the learner's level.

- **Benefit:** a learner stuck on a lesson at 10pm gets an immediate, sourced answer instead of waiting for a coach.
- **How we get there:** the real-time chat already exists, the AI tutor joins it as a participant. Course content is indexed server-side and exposed to the assistant through the existing GraphQL API; the MFE architecture lets us ship this as a new chat participant without touching the rest of the platform.
- **Status:** 🔨 in progress

### 🤖 AI Course Content Creation

A Studio-side AI assistant for content authors: quiz generation from source material, lesson summaries, content translation (FR/MG), learning objective suggestions.

- **Benefit:** an author turns a 30-page source document into a draft quiz and summary in minutes, and reviews instead of writing from scratch.
- **How we get there:** Studio is already a React MFE, the assistant plugs in as a new panel. Content is submitted to an AI service over the existing API layer; the author always validates before publishing.
- **Status:** 🔨 in progress

### 📱 Mobile App

React Native app that syncs with the desktop platform, offline-first learning.

- **Benefit:** learners continue their courses on the bus, in the field, with or without connectivity.
- **How we get there:** React Native reuses most of the existing React codebase and the same GraphQL API, zero backend changes required.
- **Status:** 🔭 exploration

### 👥 Social Learning 2.0

Thematic groups, learner networking, presence, built on the existing chat.

- **Benefit:** learners help each other between sessions, cohorts stay engaged between live classes.
- **How we get there:** extends the existing real-time chat infrastructure, it doesn't replace it.
- **Status:** 🔭 exploration

## 🔭 FUTURE: 2027

### 🎯 Micro-learning engine

Adaptive bite-sized paths, spaced repetition, daily learning moments via push notifications.

- **Benefit:** a learner keeps progressing in 5-minute sessions that fit a working day.
- **How we get there:** course content is already structured in small units; the engine sequences them based on progress data served by the GraphQL API.
- **Status:** 🔭 exploration

### 🧠 Adaptive learning

AI-personalized course sequencing based on learner performance and goals.

- **Benefit:** two learners starting the same course follow different paths: each one sees the content they need next, in the right order.
- **How we get there:** completion and quiz data already exist in the platform; a recommendation service plugs into the GraphQL API as a new resolver, and surfaces in the existing learner interface.
- **Status:** 🔭 exploration

### 🎙️ AI voice + avatar content

Integration path for AI-generated video lessons (Synthesia, HeyGen).

- **Benefit:** an author produces a video lesson without a studio, and updates it by editing text instead of re-recording.
- **How we get there:** the platform already plays hosted video (Vimeo) and SCORM packages; generated videos enter through the existing video pipeline.
- **Status:** 🔭 exploration

### 📊 Predictive analytics

Dropout risk detection, completion forecasting for coaches.

- **Benefit:** a coach sees which learners are at risk this week and can act before they disengage.
- **How we get there:** engagement and progress events already flow through the platform; a scoring service consumes them and displays risk indicators in the existing admin dashboards.
- **Status:** 🔭 exploration

### 🌐 Offline-first full sync

CRDT-based synchronization for full learning journeys offline.

- **Benefit:** learning truly anywhere: full courses, quizzes and progress tracking without connectivity, syncing automatically when back online.
- **How we get there:** all client state already flows through a single API layer, giving the sync engine one integration point. Mobile app and offline web share the same sync engine.
- **Status:** 🔭 exploration

## How we prioritize

Every roadmap item is scored on:
1. **Client value**, does it help deploy and sell?
2. **Learner impact**, does it improve outcomes?
3. **Engineering leverage**, does it make the next deployment faster?

Roadmap items graduate from 🔭 exploration to 🔨 in progress only after a technical spike validates feasibility. No launch dates are committed beyond quarter granularity.

