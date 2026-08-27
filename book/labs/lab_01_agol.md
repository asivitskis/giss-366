# Lab 1
# ESRI and the ArcGIS Online Environment: Hosted Feature Layer + POI Webmap
### GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 2 Focus:** Hosted feature layers on a proprietary platform. Publishing from upload in ArcGIS Online. Symbology, popups, and sharing as design decisions. Your first look at the self-hosted vs. hosted tradeoff.

**This is your first graded lab** (Lab Exercises, Performance Task 1). It also opens **Project Topic Ideation A**, the first of three short checkpoints that build toward your Week 11 final project.

---

## Learning Goals

By the end of this lab, you will be able to:
- Upload a geospatial dataset and publish it as a hosted feature layer in ArcGIS Online
- Configure symbology and popups so the layer is readable to someone unfamiliar with your fields 
- Build a point-of-interest webmap and make a deliberate, justified choice about its sharing level 
- Query your own hosted feature layer's REST endpoint directly.
- Take your first concrete step on the semester's final project by naming a real place and audience 

## How This Notebook Is Organized

Same as Lab 0: the **Geo-Inquiry Process**. **Ask → Collect → Visualize → Create → Act.**

## A Note on Today's Two Deliverables

Today you're doing two things that happen to share one notebook. The first is the graded lab itself: publishing a hosted feature layer and building a webmap in ArcGIS Online. The second is a short, low-stakes **Topic Ideation A** checkpoint, where you start naming a real place and audience for your Week 11 final project. Topic Ideation A isn't graded on how good your idea is. It's there so you aren't starting from zero in Week 11. You'll revisit and refine it in Topic Ideation B (Week 5) and C (Week 9).

## Before You Begin

**This semester you have access to ArcGIS Online through WNMU's organizational account.** No separate signup or trial is needed, and there's nothing to install. Your instructor has already set up your organization. You just need credentials, the same way you'll get a login to a shared server next week.

| Tool | Cost / Access | Install? | Role this semester |
|---|---|---|---|
| **ArcGIS Online (WNMU org account)** | Provided via WNMU organizational account | No: sign in at your org's AGOL URL with your WNMU credentials | Proprietary pathway: hosted feature layers and Map Viewer, all semester |
| **A point-of-interest dataset** | Free | Download from NM RGIS Clearinghouse (or build your own CSV) | The data you'll publish today |
| **A modern browser with Dev Tools** | Free | Already set up in Lab 0 | Inspecting a hosted layer's REST endpoint |
| **`requests` / `geopandas`** *(Python)* | Free, already installed | Already set up in Lab 0 | Querying your hosted layer directly, Step 4 |

You've also already been added to the **GISS 366** group in the WNMU AGOL organization, so it's ready to use as a sharing option in Step 4. No need to request access.

**Links**
- WNMU ArcGIS Online organization: https://wnmugis.maps.arcgis.com/home/index.html
- NM RGIS Clearinghouse: https://rgis.unm.edu/
- Esri Learn, Publish a hosted feature layer: https://learn.arcgis.com/en/projects/get-started-with-arcgis-online/

## Step 1: Ask

Before uploading anything, sit with a couple of questions from lecture.

> **EQ2: When data moves from a file you control into a platform someone else operates, what do you gain, what do you give up, and who ends up bearing that tradeoff?**

Take a minute and jot down your first-pass answers to these reflection questions (no submission required, just some thoughts before you dive into the actual lab):
- AGOL does most of the publishing work automatically the moment you upload a file. You don't manually configure a store, a layer, and a style by hand. What does that convenience cost you, if anything?
- If your WNMU AGOL account were deactivated the day after graduation, what would happen to a map you'd published and shared publicly? Compare that to what would happen to the shapefile sitting on your own hard drive.
- Think of a real dataset (POI, survey points, anything) where you'd hesitate before setting its AGOL sharing level to "Public." What's the actual risk, and to whom?

---

## Step 2: Collect Data

Log in to ArcGIS Online and get a point-of-interest dataset ready to publish.

<details>
<summary><b>Log in to the WNMU ArcGIS Online organization: click to expand</b></summary>

1. In your browser, go to your WNMU org's AGOL URL (see Links above).
2. Sign in with your WNMU credentials.
3. You should land on your organization's home page. Click **Content** in the top bar. This is where every item you publish today, and every item your classmates publish, will show up. You'll see a self-hosted version of this same idea in Lab 2, when we use an open source server set up to accomplish a similar workflow.

</details>

<details>
<summary><b>Get a point-of-interest dataset: NM RGIS Clearinghouse or your own CSV. Click to expand</b></summary>

1. Go to https://rgis.unm.edu/ and search for a small **point** dataset covering Grant County or the Gila region. Trailheads, earthquake epicenters, wind turbines, or anything similar might be good choices.
2. Download it as a **shapefile** (zipped `.shp` file and its sidecar files together). Or, if you'd rather build your own, make a simple CSV with `name`, `latitude`, `longitude`, and one or two descriptive columns (`type`, `notes`). A handful of real places you know in the region works fine.
3. Save the file somewhere you can find it. You'll upload it into AGOL in Step 4.


---

## Step 3: Visualize

Now publish your own dataset and turn it into a readable, styled map.

<details>
<summary><b>Publish your dataset as a hosted feature layer: click to expand</b></summary>

1. From **Content**, click **New item → Your device**, and select the file you gathered in Step 2 (zipped shapefile or CSV).
2. If you uploaded a CSV with address fields instead of lat/long, AGOL will prompt you to geocode it. Otherwise, it should detect the coordinate fields automatically. Confirm the field mapping looks right.
3. Give the layer a clear title, with the following format `Lab 01 - [layer name] - [yourname]`, so it is easy to find in our share organization. For example, our demonstration earthquake layer is named `Lab 01 - NM Earthquakes - Asivitskis`.  
4. Click **Publish → Hosted Feature Layer**.
5. Share this hosted feature service with the `GISS 366/566 Fall 2026` Group (if you are OK sharing this data with classmates).
6. Open the new layer in **Map Viewer**.

</details>

<details>
<summary><b>Configure symbology and popups: click to expand</b></summary>

1. In Map Viewer, open the **Styles** pane for your layer. If your data has a meaningful category column (for example, `type` or `mag`), try **Unique values**. Otherwise, a single clear symbol works fine.
2. Open **Popup** settings. Turn off any fields that are noise (IDs, raw codes), give the remaining fields readable aliases, and reorder them so the most important information is at the top.
3. Pick a basemap from the gallery that doesn't visually compete with your point symbols.
4. Save the map (**Save and open →** give it a title, tags, and a one-sentence summary).

---

## Step 4: Create

Let's actually create some products to share our data. First, you'll create a shareable linke for your symbolized web map. Then, you'll construct a query against your own hosted feature layer's REST endpoint, the same way any client would ask for your data. This is also your first real look at what a REST endpoint actually is.

</details>

<details>
<summary><b>Step 4.1 - Publish & Share your map: click to expand</b></summary>

1. Click **Share map**. Before clicking anything, reread your Step 1 answer about a dataset where "Public" would give you pause.
2. Choose a sharing level for *this* map: **Private**, the **GISS 366** group, your **Organization**, or **Public**. Be ready to justify it in your Step 5 reflection. There's no single correct answer here, as long as it's a considered choice and not a default click.

</details>

**Copy your web map's link now.** Open the map's **Item Details** page, or use **Share map → Link**, and copy the URL. You'll submit this link as part of your final graded assignment in Step 5.

*If you chose Private:* also add Alex as an individual with view access under **Share map → People**, or your we won't be able to open the link to grade it. Note that you did this in your Step 5 reflection.

<details>
<summary><b>Step 4.2 - Query your own hosted feature layer through its REST endpoint: click to expand</b></summary>

1. Open your layer's **Item Details** page (find it under **Content**). This page (title, description, tags, sharing level, thumbnail) is a single published, requestable resource with its own metadata.
2. Scroll to the **URL** field (usually labeled something like `.../FeatureServer`). Open that URL in a new tab. This is your layer's REST endpoint, a "menu" that lists what's published here and how to ask for it. (In Lab 2, you'll meet a close relative of this idea: GeoServer's `GetCapabilities` document.)
3. From that REST endpoint page, click into your layer (for example, `/0`), then find the **Query** link near the bottom. Set the following properties:
   * **Where:** `1=1`
   * **OutFields:** `*`
   * **Format:** `GeoJSON`
   * Click **Query (GET)**
4. Now try a real query instead of `1=1`. Swap the **Where** clause for an actual filter on one of your own fields (for example, `type='trailhead'`), and run it again. 
5. Finally, try setting the `Return Count Only` option to `True`. This will provide a count of the features within your specific query, and exclude any geometry.

   Note the shape of what comes back.

   *For a fuller walkthrough of querying a hosted feature layer through its REST endpoint, see Esri's tutorial, "Manage a hosted feature layer and feature service" (Esri Developers, n.d.; full citation in Resources below).*

</details>

📸 **Save a screenshot now:** your query output result for your own layer. Label it *Screenshot 4*.

**What did you notice?**
- Is the underlying `/query` response format familiar? Where have you seen that shape of data before?
- What changed in the result when you swapped `1=1` for a real filter on your own data?

---

## Step 5: Act

### What to turn in (graded: Lab Exercises)

1. A link to your shared web map (Step 3). Confirm it is either set to public or shared with Alex's account indiviudally.
2. Your screenshot of the query output you constructed against your own layer's REST endpoint (Step 4).
3. A short written reflection (4-6 sentences) answering:
   - Walk through, in your own words, what you asked your layer's REST endpoint for and what came back. What specific field did you query, what was the result, what happened when you set the 'return county only' option to true?
   - Explain the sharing level you chose for Screenshot 3 (Private, the GISS 366 group, Organization, or Public). What could go wrong if you'd chosen a more open setting, and for whom? What would you lose by choosing a more restrictive one?
   - What's one thing that didn't work smoothly today, and what was your first troubleshooting move: docs, a web search of the exact error, or the course discussion board?
   - What might you use this workflow for in the future? 

Post your web map link, screenshot, and reflection to this week's submission space in Canvas.

---

## Lab 01 Rubric (40 pts)

Grading applies **only** to the three graded submission items above (web map link + sharing, REST endpoint query screenshot, and written reflection). Project Topic Ideation A is required but is **not graded** on idea quality.

| Score Band | What It Looks Like |
|:---|:---|
| **Exceptional (7–8)** | Exceeding expectations; indicates mastery; in-depth understanding; higher-order thinking; inferences and extensions of learning objectives that go beyond what was taught; truly superb effort. |
| **Proficient (5–6)** | Meeting expectations; application of concepts; independently demonstrates understanding and thorough competency of learning objectives explicitly taught. |
| **Developing (3–4)** | Approaching expectations; demonstration of basic understanding without application and understanding of more complex ideas and processes; meets minimum requirements for satisfactory learning. |
| **Insufficient (1–2)** | Below expectations; partial or no demonstration of understanding and progress toward learning objectives; major errors and omissions present; inadequate for competency. |

<br>

| Criteria | Comments | Grade |
|:---|:---|:---:|
| **Content:** The hosted feature layer's web map link and REST endpoint query screenshot are both complete and correctly executed. The map link works and is shared at an appropriate level (Public, or Private plus shared with Alex), and the screenshot shows a correctly constructed query against the student's own layer, including the specific field queried and the result of setting "return county only" to true. |  | / 8 |
| **Content:** The written reflection completely and directly answers all four prompts: what was queried at the REST endpoint and what came back (including the "return county only" behavior), the sharing-level choice and its tradeoffs, a specific troubleshooting moment and method, and a genuine future use case. The response shows real understanding, with specific detail and reasoning behind it. |  | / 8 |
| **Process:** Symbology, popup configuration (field visibility, aliases, ordering), and basemap choice for the hosted feature layer reflect the student's own deliberate reasoning. Choices are explained or evident, not left as untouched defaults, and demonstrate independent, competent application of the AGOL publishing and styling workflow taught in the lab. |  | / 8 |
| **Process:** The reflection is evidence-based and specific, grounded in the student's actual query results and REST endpoint behavior. It draws an explicit, reasoned connection between the chosen sharing level and its real consequences or tradeoffs (EQ2), treating Steps 3 and 4 as connected parts of one workflow. |  | / 8 |
| **Product:** The web map link, REST endpoint screenshot, and written reflection are submitted in the requested format. Work is professionally presented, clearly organized and labeled, fully functional (link opens without error, screenshot is legible and shows the query results), and free of errors. |  | / 8 |
| **Total** |  | **/ 40** |

---

#### Project Topic Ideation A (checkpoint: low-stakes, not graded on idea quality)

In 2-3 sentences each:
- What place or idea are you drawn to for your final project? It doesn't need to be Grant County or anything geospatial you've worked with in this course. It can be anywhere or anything that matters to you.
- Who's the audience for a web map or web geospatial application of that place or topic?
- What's one question that audience might actually want a map or geospatial tool to answer for them?

You'll revisit this in **Topic Ideation B** (Week 5), once you've got more of the toolkit under your belt.


### Lab 01 AI Policy - Level 2

This lab permits optional use of AI to support student learning. Permitted uses can include using AI to help troubleshoot GIS workflows, however, independent installation and use of AI agents to complete GIS tasks is not permitted at this time. AI may also be used to support general writing procedures (outlining a project, improving clarity or grammar, etc.), but all responses must remain original to each student as a sole responsible author. Disclosure of AI use is not required for this Level 2 assignments. Using AI is a choice with ethical considerations, and it is asked that students weigh those considerations accordingly as they complete this work.



---
### Resources

- Esri Developers. (n.d.). *Manage a hosted feature layer and feature service* [Tutorial]. ArcGIS Developers. https://developers.arcgis.com/documentation/portal-and-data-services/data-services/tutorials/tools/manage-a-hosted-feature-layer-and-feature-service/
- Esri Learn, Publish a hosted feature layer: https://learn.arcgis.com/en/projects/get-started-with-arcgis-online/
- ArcGIS Online: Share your map (Esri documentation): https://doc.arcgis.com/en/arcgis-online/share-maps/share-map.htm
- ArcGIS REST API, Feature Service: https://developers.arcgis.com/rest/services-reference/enterprise/feature-service/
- NM RGIS Clearinghouse: https://rgis.unm.edu/

---

## Appendix: Optional Extension: Your Hosted Feature Layer in MapLibre (Optional, Not Required)

In Lab 0, you built `test_map.html`: a MapLibre page showing a basemap and one hardcoded marker. This extension modifies that same file to pull your own hosted feature layer straight out of AGOL's REST endpoint and draw it as real points on the map, no AGOL interface involved at all.

This is optional, ungraded, and not required to complete Lab 1. It's here for anyone who wants another hands-on look at the open-source pathway before Lab 2.

**One requirement:** a browser fetching your layer directly like this needs to reach it without logging in, so your layer has to be shared **Public**. Group and Organization sharing won't work here since the request carries no AGOL credentials. If you chose a more restrictive sharing level in Step 3 for good reasons, that's a legitimate call. Skip this extension, or just describe in a sentence or two what would need to change for this approach to work instead.

<details>
<summary><b>Run it: click to expand</b></summary>

1. In your `giss-366` folder from Lab 0, create a new file named `test_map_extension.html`.
2. Copy the code block below and paste it in. Save the file.
3. Edit the `LAYER_URL` line near the top to match your own layer's `FeatureServer/0` URL (Item Details page, same one you used in Step 4's Python cell).
4. **Right-click `test_map_extension.html` → Open With Live Server.**
5. Your points should appear on the map. Click one to see its attributes in a popup.
6. If nothing loads, open Dev Tools → Console. A `403` or CORS error almost always means the layer isn't shared Public yet.

</details>

**Copy this into `test_map_extension.html`:**

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>GISS 366 - Lab 1 Extension - MapLibre + Your Hosted Feature Layer</title>
  <script src="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.js"></script>
  <link href="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.css" rel="stylesheet" />
  <style>
    body { margin: 0; }
    #map { height: 100vh; width: 100vw; }
  </style>
</head>
<body>
  <div id="map"></div>
  <script>
    // Paste your own layer's FeatureServer/0 URL below.
    // Find it on your layer's Item Details page in AGOL, under "URL".
    // The layer must be shared Public for this to work.
    // Make sure to add "/0" to the end of your link to select the actual data layer.
    const LAYER_URL = "[add your URL here]";
    const QUERY_URL = `${LAYER_URL}/query?where=1=1&outFields=*&f=geojson`;

    const map = new maplibregl.Map({
      container: 'map',
      style: 'https://tiles.openfreemap.org/styles/liberty', // <-- swap this string if you'd like
      center: [-108.2803, 32.7701], // <-- our test map was centered on Silver City, you may need to change these coords.
      zoom: 8
    });

    map.addControl(new maplibregl.NavigationControl());

    map.on('load', () => {
      fetch(QUERY_URL)
        .then(res => res.json())
        .then(geojson => {
          map.addSource('my-layer', { type: 'geojson', data: geojson });

          map.addLayer({
            id: 'my-layer-points',
            type: 'circle',
            source: 'my-layer',
            paint: {
              'circle-radius': 6,
              'circle-color': '#2563eb',
              'circle-stroke-width': 1,
              'circle-stroke-color': '#ffffff'
            }
          });

          map.on('click', 'my-layer-points', (e) => {
            const props = e.features[0].properties;
            const rows = Object.entries(props).map(([k, v]) => `<b>${k}</b>: ${v}`).join('<br>');
            new maplibregl.Popup()
              .setLngLat(e.lngLat)
              .setHTML(rows)
              .addTo(map);
          });
        })
        .catch(err => {
          console.error('Could not load your layer.', err);
          alert('Could not load your layer. Confirm LAYER_URL is correct and the layer is shared Public.');
        });
    });
  </script>
</body>
</html>
```

---

## Appendix: Publishing Without an Organizational Account (Optional, Not Required)

Curious what this looks like without WNMU's org account backing you? This is optional, outside of class scope, and not graded.

**Esri's free ArcGIS Online public account**
1. Sign up at https://www.arcgis.com/ with a free personal account. Free accounts have a **credit cap** and cannot publish hosted feature layers from arbitrary shapefile/CSV uploads the way a paid Creator/Publisher role can. You're limited mostly to viewing and lightweight map-building.
2. For actual hosted-layer publishing outside an organization, Esri's **ArcGIS Developer** program (https://developers.arcgis.com/) gives a free tier with a small credit allowance, intended for testing the REST API rather than classroom-scale publishing.

Either path makes the credit-and-subscription model from Section 2.3 concrete fast. You'll hit the free tier's ceiling far sooner than you would on WNMU's organizational account, which is precisely the "who's paying for this and what happens if they stop" question this week's lecture raised.
