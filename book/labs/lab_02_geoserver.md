# Lab 2
# Open Web Standards — Publishing & Consuming a WMS/WFS with GeoServer
### GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 3 Focus:** OGC web service standards (WMS, WFS, WMTS) · GeoServer as a self-hosted OGC implementation · Interoperability between open-source and proprietary stacks

**This is your second graded lab** (Lab Exercises, Performance Task 2). There is no Topic Ideation checkpoint today — you'll pick that thread back up in **Topic Ideation B** (Week 5).

---

## Where We're Working: Silver City & Southwestern New Mexico

Same shared region as Lab 1 — Silver City, Grant County, and the Gila National Forest, pulled from the **NM RGIS Clearinghouse** (https://rgis.unm.edu/). Last week you published a hosted feature layer directly into ArcGIS Online. This week you're standing on the other side of that same doorway: publishing into a server you (or your instructor) run, then trying to bring that layer back into the same ArcGIS Online you already know.

## Learning Goals

By the end of this lab, you will be able to:
- Explain why an open standard like WMS/WFS lets a self-hosted server and a proprietary platform interoperate *(K02)*
- Stand up a local GeoServer instance and publish a real dataset as a WMS **and** a WFS layer *(S02)*
- Consume that published layer from an open-source client *and* consume it from the ArcGIS Online you used in Lab 1, and explain what does (or doesn't) work about each *(K01, K02)*
- Read a `GetCapabilities` document and connect it to what you saw in lecture *(K02)*
- Compare this self-hosted publishing experience to Lab 1's hosted one and articulate what each trades away *(K02)*

## How This Notebook Is Organized

Same as Lab 0 and Lab 1 — the **Geo-Inquiry Process**: **Ask → Collect → Visualize → Create → Act.**

## Before You Begin

**This semester we're using a shared, always-on GeoServer instance rather than installing GeoServer on your own machine.** Your instructor has already published a running server; you'll get your own workspace on it, the same way you might get your own folder on a shared drive. This means no Java/Docker install, and — unlike a laptop on `localhost` — it's actually reachable from the open internet, which matters once we get to the ArcGIS Online step.

| Tool | Cost / Access | Install? | Role this semester |
|---|---|---|---|
| **Shared GeoServer instance** | Provided by instructor | No — you'll get a login and workspace name in class | Open-source pathway: self-hosted OGC services, all semester |
| **A sample dataset** | Free | Download from NM RGIS Clearinghouse | The data you'll publish today |
| **ArcGIS Online** | Provided via WNMU organizational account | None | Consuming your published service — the same platform you published to in Lab 1 |
| **QGIS** *(optional but recommended)* | Free | https://qgis.org/download/ | Handy for inspecting/reprojecting data before you publish it |
| **A modern browser with Dev Tools** | Free | Already set up in Lab 0 | Reading `GetCapabilities` and watching WMS/WFS requests |

**Links**
- Class GeoServer instance: `[INSTRUCTOR: paste your server URL here, e.g. https://geoserver.giss366.wnmu.edu/geoserver]`
- NM RGIS Clearinghouse: https://rgis.unm.edu/
- GeoServer public demo (for reference, not what you'll publish to): https://data.geoserver.org/geoserver/web/

> **A note on why this looks different from a typical "install the software" lab:** Part of this week's lesson (EQ3) is about what it takes to keep a service *running* and *reachable*, not just installed. Rather than have everyone individually fight Java versions and then discover their laptop is invisible to the open internet, you're experiencing the "always-on, professionally hosted" side of that tradeoff directly — the same way you already experienced first-hand in Lab 1, publishing straight into ArcGIS Online. You'll still do the actual publishing work yourself (Step 4) — you're just not the one keeping the lights on for the server underneath it.


## Step 1: Ask

Before installing anything, sit with a couple of questions from lecture.

> **EQ3 — What happens elsewhere in this system when I change one part of it — a tile size, a data format, a query, an interface choice — and who ends up reached, or excluded, on the other end?**

Take a minute and jot down your first-pass answers (edit the cell below):
- If you publish the *same* dataset as both a WMS and a WFS, what does each version let a user *do* that the other doesn't?
- GeoServer is free to install, but it has to run on a computer that's on and reachable. ArcGIS Online costs a license, but WNMU is always running it for you — as you already experienced in Lab 1. What does that tradeoff mean for who can realistically stand up a live geospatial service?

**Your answers (double-click this cell to edit):**

-
-

---


## Step 2: Collect

Log in to the class GeoServer instance and grab a dataset to publish.

<details>
<summary><b>Open-source pathway — log in to the shared GeoServer instance: click to expand</b></summary>

1. In your browser, go to the class GeoServer URL your instructor gave you (see Links above).
2. Log in with the credentials provided in class.
3. You should land on the GeoServer welcome page — same interface anyone running their own instance would see. From here on, everything you do (Steps 3-4) is identical to a local install; the only difference is *who* is keeping the server running.
4. **Use your assigned workspace name** for everything you publish today (e.g., `smith_giss366`) — this is what keeps your layers separate from your classmates' on the same shared server.

> Curious what it takes to run this yourself? A local install path (native binary or Docker) is in the appendix at the end of this notebook if you want to try it outside of class — not required for this lab.

</details>

<details>
<summary><b>Get a dataset — NM RGIS Clearinghouse: click to expand</b></summary>

1. Go to https://rgis.unm.edu/ and search for a small vector dataset covering Grant County or the Gila region — trails, fire perimeters, and public land boundaries are good, manageable choices.
2. Download it as a **shapefile** (`.shp` + its sidecar files, usually zipped together).
3. Unzip it somewhere you can find it — you'll upload this into GeoServer in Step 4.
4. *(Optional)* Open it in QGIS first just to confirm it looks right and note its coordinate reference system (CRS) — you'll need to know this when you publish it.

</details>

📸 **Save a screenshot now** — the GeoServer welcome page after logging in. Label it *Screenshot 1*.

---

## Step 3: Visualize

Before publishing anything of your own, get oriented in GeoServer's interface and see what a `GetCapabilities` document actually looks like.

<details>
<summary><b>Explore GeoServer's built-in demo layers: click to expand</b></summary>

1. From the GeoServer welcome page, click **Layer Preview** in the left sidebar. GeoServer ships with a handful of sample layers (states, populated places, etc.) already published — this is a fast way to see the publish workflow's *output* before you've built one yourself.
2. Click **OpenLayers** next to any sample layer — this opens an interactive preview, similar to what your own layer will look like once published.
3. Now find that same layer's **WMS `GetCapabilities` URL**. Click **Layer Preview**, then look for the small format links, or go directly to `[GEOSERVER-URL]/wms?service=WMS&version=1.3.0&request=GetCapabilities` (swap in the class server URL). Open it in a new tab.
4. Skim the raw XML. Find: the list of `<Layer>` entries (this is the "menu" from lecture — note that on a shared instance, you'll see *everyone's* published layers listed here once your classmates start publishing too), and the supported `<Format>` types for `GetMap`.
5. Now edit that same URL, changing `GetCapabilities` to `GetMap` with a specific layer and bounding box (GeoServer's Layer Preview page generates one of these for you automatically — click **Common Formats → PNG** to see it in action). Compare: one URL gave you a menu, the other gave you a picture.

</details>

📸 **Save a screenshot now** — the raw `GetCapabilities` XML with at least one `<Layer>` entry visible. Label it *Screenshot 2*.

**What you noticed (double-click to edit):**
- How many layers appeared in the `GetCapabilities` document?
- What image formats does `GetMap` support, based on what you saw?

---

## Step 4: Create

Now publish your own dataset, then try consuming it from both pathways.

<details>
<summary><b>Open-source pathway — publish your dataset in GeoServer: click to expand</b></summary>

1. In GeoServer, go to **Stores** → **Add new Store** → **Shapefile**. Point it at the shapefile you downloaded in Step 2, assign it to **your assigned workspace** (not a new one — everyone shares the same GeoServer instance, so your workspace is what keeps your layer namespaced to you), and save.
2. GeoServer will detect the layer inside your store and prompt you to **publish** it. Click **Publish**.
3. On the layer configuration page:
   - Under **Data**, set the **Declared SRS** to match your data's CRS (QGIS told you this in Step 2, if you checked) and click **Compute from data** for the bounding boxes.
   - Under **Publishing**, note the default style applied — you can leave it, or explore GeoServer's built-in styles for something more legible (e.g., a line style if your data is trails).
4. Save. Go back to **Layer Preview**, find your new layer (filter by your workspace name if the list is long — remember, everyone in class is publishing to the same server), and open it in **OpenLayers** to confirm it renders.
5. Your layer is now live as **both** WMS and WFS, automatically. Find both endpoints:
   - WMS `GetCapabilities`: `[GEOSERVER-URL]/[your-workspace]/wms?service=WMS&version=1.3.0&request=GetCapabilities`
   - WFS `GetCapabilities`: `[GEOSERVER-URL]/[your-workspace]/wfs?service=WFS&version=2.0.0&request=GetCapabilities`

</details>

📸 **Save a screenshot now** — your published layer rendering in GeoServer's OpenLayers preview. Label it *Screenshot 3*.

<details>
<summary><b>Consume your WFS from Python: click to expand</b></summary>

Run the code cell below (edit the URL first to match your workspace/layer name) to pull your own published features straight out of GeoServer as GeoJSON — no GeoServer preview page involved, just the raw WFS request.

</details>


```python
# Consume your own published WFS layer directly, the way any client would
# Edit GEOSERVER_URL, WORKSPACE, and LAYER_NAME to match what you published above.

import requests
import geopandas as gpd
from io import StringIO

GEOSERVER_URL = "link"  # <-- change to the class server URL
WORKSPACE = "your_workspace"   # <-- change to your assigned workspace name
LAYER_NAME = "your_layer"      # <-- change to your published layer name

wfs_url = (
    f"{GEOSERVER_URL}/{WORKSPACE}/ows"
    f"?service=WFS&version=2.0.0&request=GetFeature"
    f"&typeName={WORKSPACE}:{LAYER_NAME}&outputFormat=application/json"
)

try:
    resp = requests.get(wfs_url, timeout=10)
    resp.raise_for_status()
    gdf = gpd.read_file(StringIO(resp.text))
    print(f"Pulled {len(gdf)} features straight from your own WFS endpoint.")
    display(gdf.head())
    gdf.plot(figsize=(6, 6))
except Exception as e:
    print("Couldn't reach the WFS endpoint yet -- confirm the class GeoServer URL is correct and the")
    print("workspace/layer names above match what you published, then re-run this cell.")
    print(f"Error detail: {e}")
```

<details>
<summary><b>Proprietary pathway — consume it from ArcGIS Online: click to expand</b></summary>

1. In ArcGIS Online — the same environment you published a hosted feature layer to in Lab 1 — open Map Viewer and choose **Add → Add Layer from Web**.
2. Choose **WMS OGC Web Service** (or **WFS**, if you'd rather bring in features instead of an image), and paste in your `GetCapabilities` URL from Step 4.
3. Because this is a real, always-on server rather than something running on your own laptop, this should actually connect — AGOL will parse the `GetCapabilities` document, list your published layer(s), and let you add one to the map.

**If it doesn't connect on the first try**, before assuming something's broken, check:
- Did you copy the full `GetCapabilities` URL, including your workspace name?
- Is the layer's style/SRS fully saved in GeoServer (Step 4)?
- Try the WMS `GetCapabilities` URL directly in a new browser tab first — if it doesn't load there, AGOL won't be able to reach it either.

</details>

📸 **Save a screenshot now** — your layer successfully added in ArcGIS Online. Label it *Screenshot 4*.

**Reflection prompt for later:** in this lab last year, before a shared always-on server, this step was expected to fail for most students, because everyone was running GeoServer on their own laptop's `localhost` — invisible to anything outside that machine. Today it (probably) worked. That difference *is* the lesson: what changed wasn't the standard, it was who's responsible for keeping the server reachable. Hang onto that thought for Step 5.

---

## Step 5: Act

### What to turn in (graded — Lab Exercises)

1. Your four screenshots:
   - **Screenshot 1** — GeoServer welcome page (Collect)
   - **Screenshot 2** — raw `GetCapabilities` XML (Visualize)
   - **Screenshot 3** — your own layer in GeoServer's OpenLayers preview (Create)
   - **Screenshot 4** — your layer in ArcGIS Online (Create)
2. Your completed Python cell output (the pulled GeoJSON feature count and the small plot).
3. A short written reflection (4-6 sentences) answering:
   - Walk through, in your own words, what happened between your browser (or AGOL, or the Python cell) and GeoServer for **one** of your requests today — what was requested, and what came back?
   - Revisit **EQ3**: what did publishing the *same* dataset as both WMS and WFS actually let you do differently between the two? Which one would you reach for if you needed to symbolize the data yourself in a client, versus just needing a quick reference layer?
   - Today's GeoServer instance is shared and always-on rather than something you installed yourself. Compare that to a hypothetical version of this lab where you ran GeoServer on your own laptop: what would have changed about the ArcGIS Online step, and who is responsible for that server staying up between now and finals week?
   - Now that you've published through both a hosted platform (Lab 1, AGOL) and a self-hosted server (today), which felt like it gave you more control, and which felt easier? Was that the tradeoff you predicted in Lab 1's EQ2 answer?
   - What's one thing that didn't work smoothly today, and what was your first troubleshooting move — docs, a web search of the exact error, or the course discussion board *(K16)*?

Post your screenshots, code output, and reflection to this week's submission space in Canvas.

### Looking Ahead

Week 4 picks up the sharing-decision thread directly, in **Discussion 1** — open data, community consent, and what "open" actually obligates a publisher to consider; revisit your EQ answers from both Lab 1 and today before that discussion. Week 6 comes back to the tiling and cloud-native formats we flagged in lecture — worth re-reading your EQ3 answer above once you get there, to see if a static, serverless approach changes what you think the tradeoffs are.

---
### Resources

- GeoServer documentation: https://docs.geoserver.org/latest/en/user/
- GeoServer download: https://geoserver.org/download/
- NM RGIS Clearinghouse: https://rgis.unm.edu/
- OGC WMS standard: https://www.ogc.org/standard/wms/
- OGC WFS standard: https://www.ogc.org/standard/wfs/
- `geopandas` documentation: https://geopandas.org/

---

## Appendix: Running GeoServer Yourself (Optional, Not Required)

Curious what it takes to be the one keeping the server up, instead of just publishing to it? This is optional, outside of class scope, and not graded.

**Option A — Native installer**
1. Download the platform-specific installer/binary from https://geoserver.org/download/ (the "Platform Independent Binary" works everywhere if you have Java installed; installers exist for Windows/macOS).
2. Start GeoServer (on the binary, run `bin/startup.sh` or `bin/startup.bat`).
3. In your browser, go to `http://localhost:8080/geoserver`. You should see the GeoServer welcome page.
4. Log in with the default credentials (`admin` / `geoserver`).

**Option B — Docker**
1. Install Docker Desktop if you don't already have it: https://www.docker.com/products/docker-desktop/
2. Run: `docker run -p 8080:8080 kartoza/geoserver`
3. Once it finishes starting up, go to `http://localhost:8080/geoserver` and log in as above.

If you want to try connecting a locally-installed instance to ArcGIS Online the way earlier semesters did, you'll need a tunneling tool like [ngrok](https://ngrok.com/) to expose `localhost` to the open internet — this is exactly the gap the class server closes for you.

