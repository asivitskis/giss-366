---
title: "Part 1: Define & Begin Data Collection"
---

# Part 1: Define & Begin Data Collection

**Due:** Week 11 · **Points:** 30 · **Format:** Written plan, 1–2 pages, submitted to Canvas

This is where your final project starts. You're designing a real WebGIS project around a question that has value to you, an employer, or a community — not re-running a lab. If you're building on a pilot study from an earlier lab, you need to meaningfully extend it, not resubmit it.

Answer each step below in complete sentences. Much of this text can move directly into your final report's Introduction (Part 5b), so it's worth writing it well now.

## Step 1. State your question

State the question you're answering or the problem you're addressing. **Your question must include the spatial extent of the problem** — a place, boundary, or region the question is scoped to.

> *Example: Is there a correlation between road conditions and the number of traffic accidents in Grant County, NM?*

## Step 2. Identify your audience and significance

Who is interested in this beyond our class? Who would fund this work, and why does it matter to them? This is also your first pass at the project's **ethics framing** — you'll return to it all semester:

- Who is this map's audience, and who is it *not* reaching?
- Who controls this data once it's published, and who agreed to that?

## Step 3. Describe your expected end product(s)

What will your resulting webmap(s) and chart(s) actually show? Be concrete about the output, not just the topic.

> *Example: This study will produce a webmap of Grant County with symbology highlighting high- to low-risk roads based on traffic data, alongside a graph showing the intersections most associated with accidents.*

## Step 4. Choose a preliminary platform direction

You don't have to finalize your stack yet — that justification is due in Part 2 — but name a working direction now so your data plan (Step 5) matches it.

```{admonition} ArcGIS Online pathway
:class: tip
Lean this way if your audience/partner org already works in Esri tools, you're on a tight timeline and want a no-code app builder, or your data collection depends on Field Maps / Survey123.
```

```{admonition} Open-source / cloud-native pathway
:class: tip
Lean this way if cost or licensing access matters to your audience, you want full control over hosting and styling, or your project's data-sovereignty case means keeping data off a proprietary vendor's servers. Think GeoServer, Leaflet/MapLibre GL JS, PostGIS, PMTiles/COG.
```

Mixed stacks are fine — note that intention here too.

## Step 5. Plan your data collection methodology

Specify the type(s) of spatial data you'll collect (point, line, or polygon) and the attributes you'll need for each feature. Your reader should be able to recreate your collection phase from this section alone.

Include one of the following:

- A **flowchart** of your collection procedure (built in whatever tool you like — Excel, PowerPoint, draw.io, etc. — and inserted as a figure), or
- A **spreadsheet skeleton** of the attributes you intend to collect, or
- For survey-based collection, a **visual map of your survey logic** (question order, skip rules).

```{admonition} AGOL: Field Maps / Survey123
:class: note
Plan your form fields and any survey skip-logic now, so your hosted feature layer schema is ready before you go into the field.
```

```{admonition} FOSS: mobile capture + GeoJSON
:class: note
Plan how you'll capture coordinates and attributes in the field (QField, Mergin Maps, a GPS unit + field sheet, GeoJSON.io, or a plain spreadsheet with lat/long) and how that will land in your repo or PostGIS table.
```

If you plan to augment your own data with an external dataset (e.g., trail data from a Forest Service portal), name the source here and note that you'll need full metadata for it later.

## Step 6. Begin data collection

Start collecting in the field. You should personally be responsible for at least half of any collected data, even if others help. If your project builds on a prior lab, collect at least as many new points as that lab required; if it's an entirely new project, aim for roughly the same volume as a typical lab assignment (~30 points is a reasonable default) — talk to your instructor if your project's scope suggests otherwise.

A redundant paper field sheet is a good idea if you're working somewhere with unreliable connectivity. GPS units are available to check out.

## Submitting Part 1

Upload this document (Steps 1–5 as prose, Step 6 as a short "collection in progress" note) to the Part 1 assignment page on Canvas. Feedback here is meant to sharpen your direction before you commit to an architecture in Part 2.
