---
title: "Part 2: Architecture & Data Check"
---

# Part 2: Architecture & Data Check

**Due:** Week 12 · **Points:** 40 · **Format:** Preliminary webmap/app + written notes, submitted to Canvas

By now you should have field data coming in and a direction from Part 1. This part locks in your architecture, gets your data properly hosted, and gives you a working (if unpolished) webmap to build on in Part 3.

## Step 1. Justify your stack

Formally commit to ArcGIS Online, an open-source/cloud-native stack, or a documented mix. This isn't just a preference statement — connect the choice to your Part 1 audience and the second ethics question:

- What does your platform and service choice cost your audience, in money, bandwidth, or vendor lock-in?

Write a short paragraph (~5–8 sentences) that names your stack and defends it against at least one alternative you considered and rejected.

## Step 2. Set up your hosted data/services

```{admonition} ArcGIS Online pathway
:class: tip
Create hosted feature layer(s) in a **Project** folder in your AGOL account. Choose a basemap appropriate to your data (OSM, Topographic, Aerial Imagery). Share the webmap.
```

```{admonition} Open-source / cloud-native pathway
:class: tip
Load your data into PostGIS and publish it through GeoServer as a WMS/WFS, **or** convert it to a static format (GeoJSON, FlatGeobuf, PMTiles/COG) served from a public repo or object storage. Stand up a minimal Leaflet or MapLibre GL JS page that draws from that service.
```

If you're mixing stacks (e.g., publishing through GeoServer but building in Experience Builder), set up both halves now and note the handoff point between them.

## Step 3. Finish data collection and load it in

Complete your field collection from Part 1. Bring the data fully into your webmap/app — as a hosted feature layer, a database table, or a static file your app is reading from. Confirm the layer renders and that its sharing/access settings match your intended audience.

## Step 4. Review data quality

Open your attribute table (or query your database/GeoJSON) and confirm you have the expected number of features and that no attributes are empty or malformed. Correct anything broken now — Part 3's analysis will silently propagate any data errors you leave in place.

## Step 5. Draft your Background Information section

Write a first draft of the Background section for your final report (≤5 sentences): general context on the problem, the assumptions/simplifications you're making given your time and resource constraints, and — if useful — a background figure with a numbered caption.

## Submitting Part 2

Include in your submission:

- Your stack justification paragraph (Step 1)
- Your webmap/app URL, or a link to your public repo + service endpoint (Step 2–3)
- A short note on data quality checks performed (Step 4)
- Your Background Information draft (Step 5)
