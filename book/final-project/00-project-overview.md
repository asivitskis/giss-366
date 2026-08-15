---
title: Final Project — Public Web GIS Inquiry
---

# Final Project: Public Web GIS Inquiry

**Weight:** 35% of course grade (350 pts) · **Weeks 11–16**

Beginning Week 11, you will design, build, and deploy a public-facing webmap and technical report grounded in a real audience and question. Projects are encouraged to engage with real-world issues and community partners. Topics could include emergency response, environmental monitoring, public health, planning, journalism, or community organizing.

You are explicitly invited to name who your map's audience is and is not reaching, and what data-sovereignty or access tradeoffs your architecture choices made. **The goal is not a flawless deployment but a meaningful, well-documented product.** Select rubric items will be co-designed with the class for each deliverable. We'll share more about the assessment structure as we approach these milestones. 

This project is built to work equally well with a proprietary stack (ArcGIS Online) or an open-source/cloud-native stack (GeoServer, Leaflet or MapLibre GL JS, PostGIS, PMTiles/COG). Every part of the guidance below gives you both pathways. You may also mix stacks. For example, publishing data through GeoServer and building your app in Experience Builder, or hosting on ArcGIS Online while styling vector tiles in MapLibre.

## Milestones

| Wk | Milestone | Deliverable | Points | Guide |
| --- | --- | --- | --- | --- |
| 2 | Ideation Topic A | In-class reflection | ungraded | *(in Week 2 lecture)* |
| 9 | Ideation Topic B | In-class reflection | ungraded | *(in Week 9 lecture)* |
| 11 | Part 1 — Define & Begin Data Collection | Written plan (1–2 pp) | 40 | [Part 1: Define & Collect](01-part1-define-collect.md) |
| 12 | Part 2 — Architecture & Data Check | Map + notes | 50 | [Part 2: Architecture & Data Check](02-part2-architecture-data.md) |
| 13 | Part 3 — Editing & Analysis | Working app | 50 | [Part 3: Analysis & Editing](03-part3-analysis-editing.md) |
| 14 | Part 4 — Results & Interpretation | Draft report | 50 | [Part 4: Results & Interpretation](04-part4-results-interpretation.md) |
| 15 | Part 5a — Presentation | Slide deck | 60 | [Part 5a: Presentation](05-part5a-presentation.md) |
| 16 | Part 5b — Final Submission | Full package | 100 | [Part 5b: Final Submission](06-part5b-final-submission.md) |

Each part builds directly on the one before it — nothing here asks you to redo prior work, only to extend and refine it. Skipping a step tends to show up as scramble two weeks later.

## Choosing your stack

You'll commit to a primary platform in Part 1 and justify it in Part 2. Use this as a starting framework, not a rulebook:

- **Lean ArcGIS Online if:** your audience or partner org already works in the Esri ecosystem, you need a no-code app builder on a tight timeline, or your data collection depends on Field Maps/Survey123.
- **Lean open-source/cloud-native if:** cost or licensing access matters to your audience, you want full control over hosting and styling, or your project's ethics case (see below) involves keeping data off a proprietary vendor's servers.
- **Mixed stacks are fair game** — document why in your architecture justification (Part 2).

## Ethics and access, throughout

Because this map goes public, every part of the guidance below asks you to return to the same three questions:

1. Who is this map's audience, and who is it *not* reaching?
2. What does your platform and service choice cost your audience, in money, bandwidth, or vendor lock-in?
3. Who controls this data once it's published, and who agreed to that?

Part 3 pairs directly with Discussion 4 (Digital Ethics Case Study, Week 13) — bring your project's own tradeoffs into that conversation.

## Getting help

Office hours (Tu 10–12, Wed/Th 12–2) are your fastest path to unblocking a stuck architecture decision or a broken service. Bring the error, not just the symptom.
