# Lab 1
# ESRI and the ArcGIS Online Environment — Hosted Feature Layer + POI Webmap
### GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 2 Focus:** Hosted feature layers on a proprietary platform · Publishing from upload in ArcGIS Online · Symbology, popups, and sharing as design decisions · Your first look at the self-hosted vs. hosted tradeoff

**This is your first graded lab** (Lab Exercises, Performance Task 1) and it also opens **Project Topic Ideation A** — the first of three short checkpoints that build toward your Week 11 final project.

---

## Where We're Working: Silver City & Southwestern New Mexico

We're staying with the same shared region from Lab 0 — Silver City, Grant County, and the Gila National Forest — pulled from the **NM RGIS Clearinghouse** (https://rgis.unm.edu/). One shared place means we can compare notes on what published, and what broke, before you strike out on your own place for the final project.

## Learning Goals

By the end of this lab, you will be able to:
- Upload a point dataset and publish it as a hosted feature layer in ArcGIS Online *(S02)*
- Configure symbology and popups so the layer is actually readable to someone unfamiliar with your fields *(S02)*
- Build a point-of-interest webmap and make a deliberate, justified choice about its sharing level *(K02, K01)*
- Query your own hosted feature layer's REST endpoint directly — in Lab 2 you'll do this again with a WFS endpoint on a self-hosted GeoServer, so pay attention to what this request/response actually looks like *(K02)*
- Take your first concrete step on the semester's final project: naming a real place and audience *(PK2 — Topic Ideation A)*

## How This Notebook Is Organized

Same as Lab 0 — the **Geo-Inquiry Process**: **Ask → Collect → Visualize → Create → Act.**

## A Note on Today's Two Deliverables

Today you're doing two different things that happen to share a notebook: (1) the graded lab itself — publishing a hosted feature layer and building a webmap in ArcGIS Online — and (2) a short, low-stakes **Topic Ideation A** checkpoint, where you start naming a real place and audience for your Week 11 final project. Topic Ideation A isn't graded on the quality of your idea — it exists so that you're not starting your final project topic from zero in Week 11. You'll revisit and refine it in Topic Ideation B (Week 5) and C (Week 9).

## Before You Begin

**This semester you have access to ArcGIS Online through WNMU's organizational account** — no separate signup or trial needed, and no install. Your instructor has already set up your organization; you just need credentials, the same way you'll get a login to a shared server next week.

| Tool | Cost / Access | Install? | Role this semester |
|---|---|---|---|
| **ArcGIS Online (WNMU org account)** | Provided via WNMU organizational account | No — sign in at your org's AGOL URL with your WNMU credentials | Proprietary pathway: hosted feature layers and Map Viewer, all semester |
| **A point-of-interest dataset** | Free | Download from NM RGIS Clearinghouse (or build your own CSV) | The data you'll publish today |
| **A modern browser with Dev Tools** | Free | Already set up in Lab 0 | Inspecting a hosted layer's REST endpoint |
| **`requests` / `geopandas`** *(Python)* | Free, already installed | Already set up in Lab 0 | Querying your hosted layer directly, Step 4 |

**Links**
- WNMU ArcGIS Online organization: `[INSTRUCTOR: paste your org's AGOL URL here, e.g. https://wnmu.maps.arcgis.com]`
- NM RGIS Clearinghouse: https://rgis.unm.edu/
- Esri Learn — Publish a hosted feature layer: https://learn.arcgis.com/en/projects/get-started-with-arcgis-online/

> **A note on why this will look easier than Lab 2:** AGOL infers your schema and publishes automatically the moment you upload a file, so today the technical friction stays low and the harder questions live at the *design* layer — how you symbolize the data, what you let people click on, and, in Step 4, who you let see it and why. Hang onto that "this was pretty painless" feeling: in Lab 2 you'll stand up the same kind of publishing on a self-hosted GeoServer instance, by hand, and EQ3 there will ask you to unpack exactly what AGOL was doing for you automatically today.

> **[INSTRUCTOR NOTE — before class]** Confirm every student has an active WNMU AGOL account before class (Named User license, Creator or Publisher role — anything below that can't publish hosted feature layers). If your org uses SSO, test the login flow once yourself beforehand; a broken SSO redirect is a much harder thing to debug live on the first lab of the semester.

## Step 1: Ask

Before uploading anything, sit with a couple of questions from lecture.

> **EQ2 — When data moves from a file you control into a platform someone else operates, what do you gain, what do you give up, and who ends up bearing that tradeoff?**

Take a minute and jot down your first-pass answers (edit the cell below):
- AGOL does most of the publishing work automatically the moment you upload a file — no manually configuring a store, a layer, and a style by hand. What does that convenience cost you, if anything?
- If your WNMU AGOL account were deactivated the day after graduation, what would happen to a map you'd published and shared publicly? Compare that to what would happen to the shapefile sitting on your own hard drive.
- Think of a real dataset — POI, survey points, anything — where you'd hesitate before setting its AGOL sharing level to "Public." What's the actual risk, and to whom?
- Pick (even loosely) a place and an audience you might want to build your final project around. It's okay if this changes completely by Week 5.

**Your answers (double-click this cell to edit):**

-
-
-
-

---


## Step 2: Collect

Log in to ArcGIS Online and get a point-of-interest dataset ready to publish.

<details>
<summary><b>Log in to the WNMU ArcGIS Online organization: click to expand</b></summary>

1. In your browser, go to your WNMU org's AGOL URL (see Links above).
2. Sign in with your WNMU credentials.
3. You should land on your organization's home page. Click **Content** in the top bar — this is where every item you publish today (and every item your classmates publish) will show up. You'll see a self-hosted version of this same idea in Lab 2, when GeoServer's Layer Preview list plays the same role.

</details>

<details>
<summary><b>Get a point-of-interest dataset — NM RGIS Clearinghouse or your own CSV: click to expand</b></summary>

1. Go to https://rgis.unm.edu/ and search for a small **point** dataset covering Grant County or the Gila region — trailheads, campgrounds, historic markers, or fire lookout towers are all good, manageable choices.
2. Download it as a **shapefile** (zip the `.shp` + sidecar files together) *or*, if you'd rather build your own, make a simple CSV with `name`, `latitude`, `longitude`, and one or two descriptive columns (`type`, `notes`) — a handful of real places you know in the region works fine.
3. Save the file somewhere you can find it — you'll upload it into AGOL in Step 4.

</details>

📸 **Save a screenshot now** — your organization's AGOL Content page after logging in. Label it *Screenshot 1*.

---

## Step 3: Visualize

Before publishing anything of your own, look at what a hosted feature layer's REST endpoint actually looks like.

<details>
<summary><b>Inspect an existing hosted feature layer: click to expand</b></summary>

1. In AGOL, search Content (or the public gallery) for any existing hosted feature layer — your instructor may point you at a class example, or search the public gallery for something like `wildfire perimeters`.
2. Open its **Item Details** page. This page — title, description, tags, sharing level, thumbnail — is a single published, requestable resource with its own metadata.
3. Scroll to the **URL** field on the Item Details page (usually labeled something like `.../FeatureServer`). Open that URL in a new tab — this is the layer's REST endpoint, a "menu" that lists what's published here and how to ask for it. (In Lab 2 you'll meet a close relative of this idea: GeoServer's `GetCapabilities` document.)
4. From that REST endpoint page, click into a specific layer (e.g., `/0`), then find the **Query** link near the bottom. Set `Where` to `1=1` and `Format` to `GeoJSON`, then run the query — note the shape of what comes back. You'll pull a WFS equivalent yourself in Lab 2's Python cell, and it's worth comparing the two once you do.

</details>

📸 **Save a screenshot now** — the REST endpoint's Item Details or Query page for the layer you inspected. Label it *Screenshot 2*.

**What you noticed (double-click to edit):**
- What metadata does the Item Details page show you, beyond just a list of layers?
- Is the underlying `/query` response format familiar? Where have you seen that shape of data before?

---


## Step 4: Create

Now publish your own dataset, style it, and turn it into a webmap.

<details>
<summary><b>Publish your dataset as a hosted feature layer: click to expand</b></summary>

1. From **Content**, click **New item** → **Your device**, and select the file you gathered in Step 2 (zipped shapefile or CSV).
2. If you uploaded a CSV with address fields instead of lat/long, AGOL will prompt you to geocode it — otherwise it should detect the coordinate fields automatically. Confirm the field mapping looks right.
3. Give the layer a clear title (include your name or initials, e.g. `POI_TrailheadsGila_jsmith`) so it's easy to find in a shared organization, and click **Publish** → **Hosted Feature Layer**.
4. Open the new layer in **Map Viewer**.

</details>

<details>
<summary><b>Configure symbology and popups: click to expand</b></summary>

1. In Map Viewer, open the **Styles** pane for your layer. If your data has a meaningful category column (e.g., `type`), try **Unique values**; otherwise a single clear symbol is fine.
2. Open **Popup** settings. Turn off any fields that are noise (IDs, raw codes), give the remaining fields readable aliases, and reorder them so the most important information is at the top.
3. Pick a basemap from the gallery that doesn't visually fight with your point symbols.
4. Save the map (**Save and open** → give it a title, tags, and a one-sentence summary).

</details>

📸 **Save a screenshot now** — your layer, styled with a working popup, in Map Viewer. Label it *Screenshot 3*.

<details>
<summary><b>Decide on a sharing level: click to expand</b></summary>

1. Click **Share map**. Before clicking anything, reread your Step 1 answer about a dataset where "Public" would give you pause.
2. Choose a sharing level for *this* map — **Private**, **Organization**, or **Public** — and be ready to justify it in your Step 5 reflection. There's no single correct answer here as long as it's a considered one, not a default click.

</details>

📸 **Save a screenshot now** — the Share settings dialog showing the level you chose. Label it *Screenshot 4*.

<details>
<summary><b>Query your own hosted layer from Python: click to expand</b></summary>

Run the code cell below (edit the URL first to match your own published layer) to pull your own hosted features straight out of AGOL's REST endpoint as GeoJSON — the hosted-layer equivalent of the WFS pull you did in Lab 1.

To get your layer's URL: open its **Item Details** page in AGOL and copy the URL shown under **URL** (it should end in `/FeatureServer/0`).

</details>


```python
# Consume your own published hosted feature layer directly, the way any client would
# Edit LAYER_URL to match the FeatureServer URL from your own layer's Item Details page.

import requests
import geopandas as gpd
from io import StringIO

LAYER_URL = "https://services.arcgis.com/XXXXXXXXXXXX/arcgis/rest/services/POI_TrailheadsGila_yourname/FeatureServer/0"  # <-- change to your layer's URL

query_url = f"{LAYER_URL}/query"
params = {
    "where": "1=1",
    "outFields": "*",
    "f": "geojson"
}

try:
    resp = requests.get(query_url, params=params, timeout=10)
    resp.raise_for_status()
    gdf = gpd.read_file(StringIO(resp.text))
    print(f"Pulled {len(gdf)} features straight from your own hosted feature layer.")
    display(gdf.head())
    gdf.plot(figsize=(6, 6))
except Exception as e:
    print("Couldn't reach the layer yet -- confirm LAYER_URL above is the full FeatureServer/0 URL")
    print("from your layer's Item Details page, and that the layer's sharing level allows this query.")
    print(f"Error detail: {e}")
```

---

## Step 5: Act

### What to turn in (graded — Lab Exercises)

1. Your four screenshots:
   - **Screenshot 1** — your organization's AGOL Content page (Collect)
   - **Screenshot 2** — REST endpoint Item Details or Query page you inspected (Visualize)
   - **Screenshot 3** — your styled layer with working popup in Map Viewer (Create)
   - **Screenshot 4** — the Share settings dialog showing your chosen sharing level (Create)
2. Your completed Python cell output (the pulled GeoJSON feature count and the small plot).
3. A short written reflection (4-6 sentences) answering:
   - Walk through, in your own words, what happened between your Python cell and AGOL for that query — what was requested, and what came back? Hang onto this output — you'll compare it to a WFS pull in Lab 2.
   - Revisit **EQ2**: what did you gain by publishing through a hosted platform like AGOL, and what do you expect you'd give up on a self-hosted setup instead? Be specific — not "convenience" in the abstract, but *what* became easier and *what* you think you lose control over. (You'll find out for real in Lab 2.)
   - Explain the sharing level you chose for Screenshot 4. What could go wrong if you'd chosen a more open setting, and for whom? What would you lose by choosing a more restrictive one?
   - What's one thing that didn't work smoothly today, and what was your first troubleshooting move — docs, a web search of the exact error, or the course discussion board *(K16)*?

### Project Topic Ideation A (checkpoint — low-stakes, not graded on idea quality)

In 2-3 sentences each:
- What place are you drawn to for your final project? It doesn't need to be Grant County or anything geospatial you've worked with in this course — it can be anywhere that matters to you.
- Who's the audience for a web map of that place — and who is deliberately *not* the audience?
- What's one question that audience might actually want a map to answer for them?

You'll revisit this in **Topic Ideation B** (Week 5) once you've got more of the toolkit under your belt.

Post your screenshots, code output, and reflection to this week's submission space in Canvas.

### Looking Ahead

Week 3 shifts to the self-hosted pathway — standing up a GeoServer instance and publishing the same kind of layer as a WMS/WFS, then trying to consume it back into the ArcGIS Online you just spent today in. Week 4 picks up the sharing-decision thread directly, in **Discussion 1** — open data, community consent, and what "open" actually obligates a publisher to consider. Revisit your Step 1 and sharing-level answers from today before that discussion; they're your starting position. Week 6 comes back to self-hosted vs. hosted one more time, from the cloud-native/static-file angle — worth comparing your EQ2 answer to how you think about that tradeoff once no server is running at all.

---
### Resources

- Esri Learn — Publish a hosted feature layer: https://learn.arcgis.com/en/projects/get-started-with-arcgis-online/
- ArcGIS Online: Share your map (Esri documentation): https://doc.arcgis.com/en/arcgis-online/share-maps/share-map.htm
- ArcGIS REST API — Feature Service: https://developers.arcgis.com/rest/services-reference/enterprise/feature-service/
- NM RGIS Clearinghouse: https://rgis.unm.edu/
- `geopandas` documentation: https://geopandas.org/

---

## Appendix: Publishing Without an Organizational Account (Optional, Not Required)

Curious what this looks like without WNMU's org account backing you? This is optional, outside of class scope, and not graded.

**Esri's free ArcGIS Online public account**
1. Sign up at https://www.arcgis.com/ with a free personal account — note that free accounts have a **credit cap** and cannot publish hosted feature layers from arbitrary shapefile/CSV uploads the way a paid Creator/Publisher role can; you're limited mostly to viewing and lightweight map-building.
2. For actual hosted-layer publishing outside an organization, Esri's **ArcGIS Developer** program (https://developers.arcgis.com/) gives a free tier with a small credit allowance, intended for testing the REST API rather than classroom-scale publishing.

Either path makes the credit-and-subscription model from Section 3.1 concrete fast — you'll hit the free tier's ceiling far sooner than you would on WNMU's organizational account, which is precisely the "who's paying for this and what happens if they stop" question this week's lecture raised.
