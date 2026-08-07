---
title: "Part 3: Editing & Analysis"
---

# Part 3: Editing & Analysis

**Due:** Week 13 · **Points:** 50 · **Format:** Working app, submitted to Canvas

This week your webmap becomes an actual application: real spatial analysis layered in, and the interactivity/styling that makes it legible to someone outside this class. This part pairs directly with **Discussion 4 (Digital Ethics Case Study)** — bring your own project's tradeoffs into that conversation.

## Step 1. Draft your analysis methods

Determine what analysis your problem and data actually call for — overlays? distance/proximity? hotspot detection? Identify the specific tool(s) you'll use and why. **You must use a minimum of 2 analysis tools.** A purely visual read of the map ("the points look clustered downtown") does not count as spatial analysis.

Write a first draft of your report's Methods section: what each tool does, what data layers you fed it, and why it was the right choice for your goal.

```{admonition} ArcGIS Online pathway
:class: tip
Draw from the [AGOL Geoprocessing toolkit](http://enterprise.arcgis.com/en/portal/latest/administer/windows/configure-the-portal-to-perform-analysis.htm) categories: Summarize Data, Find Locations, Analyze Patterns, Use Proximity, Manage Data.
```

```{admonition} Open-source / cloud-native pathway
:class: tip
Equivalent tools include PostGIS spatial SQL (`ST_Buffer`, `ST_Intersects`, `ST_ClusterKMeans`, `ST_DWithin`), QGIS Processing run before publishing, or client-side analysis with Turf.js. Document the exact function calls or processing steps as your "methods," the same way AGOL students document their tool names.
```

## Step 2. Perform the analysis

Run your chosen tools against your Part 2 data. Save the resulting layers back into your webmap/app and symbolize them appropriately — this is a result someone should be able to read at a glance, not a raw output layer.

## Step 3. Build interactivity and styling ("editing")

This is where your app moves from "data on a map" to something a public audience can actually use:

- Popups/attribute displays for your key layers
- A legend and coherent symbology
- Basic layout/UI (layer toggles, a title, attribution)
- A quick accessibility pass — contrast, readable labels, keyboard/zoom behavior (this previews Lab 9's accessibility audit)

## Step 4. Save and confirm deployment

Save all changes and confirm your webmap/app is shared and publicly reachable at the URL you're about to submit — not just visible to you while logged in.

## Submitting Part 3

- Your Methods section draft (Step 1)
- Your updated, publicly accessible webmap/app URL (Steps 2–4)
- A one-paragraph note on what changed since Part 2
