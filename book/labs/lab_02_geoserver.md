# Lab 2
# Open Web Standards: Publishing and Consuming a WMS/WFS with GeoServer
### GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 3 Focus:** OGC web service standards (WMS, WFS, WMTS). GeoServer as a self-hosted OGC implementation. Interoperability between open-source and proprietary stacks.

**This is your second graded lab** (Lab Exercises, Performance Task 2). There is no Topic Ideation checkpoint today. You'll pick that thread back up in **Topic Ideation B** (Week 5).

---

## Where We're Working: Silver City & Southwestern New Mexico

Same shared region as Lab 1: Silver City, Grant County, and the Gila National Forest, pulled from the **NM RGIS Clearinghouse** (https://rgis.unm.edu/). Last week you published a hosted feature layer directly into ArcGIS Online. This week you're standing on the other side of that same doorway, publishing into a server that is run explicitly for your class, then bringing that layer back into the same ArcGIS Online you already know.

## Learning Goals

By the end of this lab, you will be able to:
- Explain why an open standard like WMS/WFS lets a self-hosted server and a proprietary platform interoperate.
- Stand up a workspace on a live GeoServer instance and publish a real dataset as a WMS and a WFS layer.
- Consume that published layer from an open-source client and from the ArcGIS Online you used in Lab 1, and explain what does or doesn't work about each.
- Read a `GetCapabilities` document and connect it to what you saw in lecture.
- Compare this self-hosted publishing experience to Lab 1's hosted one and articulate what each trades away.

## How This Notebook Is Organized

Same as Lab 0 and Lab 1: the **Geo-Inquiry Process**. **Ask → Collect → Visualize → Create → Act.**


## Before You Begin

**This semester we're using a shared, always-on GeoServer instance instead of installing GeoServer on your own machine.** Your instructor has already published a running server on a DigitalOcean droplet, and you'll get your own workspace on it, the same way you might get your own folder on a shared drive. That means no Java or Docker install for you, and it means the server is actually reachable from the open internet, unlike a laptop on `localhost`. That second part matters once we get to the ArcGIS Online step below.

| Tool | Cost / Access | Install? | Role this semester |
|---|---|---|---|
| **Shared GeoServer instance** | Provided by instructor (DigitalOcean droplet) | No: you'll get a login and workspace name in class | Open-source pathway: self-hosted OGC services, all semester |
| **A sample dataset** | Free | Download from NM RGIS Clearinghouse | The data you'll publish today |
| **Class shapefile upload page** | Provided by instructor (same droplet) | No: you'll log in with the same class credentials | Getting your zipped shapefile from your laptop onto the server, before you publish it in GeoServer |
| **ArcGIS Online** | Provided via WNMU organizational account | None | Consuming your published service, the same platform you published to in Lab 1 |
| **QGIS** *(optional but recommended)* | Free | https://qgis.org/download/ | Handy for inspecting or reprojecting data before you publish it |
| **A modern browser with Dev Tools** | Free | Already set up in Lab 0 | Reading `GetCapabilities` and watching WMS/WFS requests |

**Links**
- Class GeoServer instance: https://138-68-249-92.sslip.io/geoserver
- Class shapefile upload page: https://138-68-249-92.sslip.io/upload
- NM RGIS Clearinghouse: https://rgis.unm.edu/
- GeoServer public demo (for reference, not what you'll publish to): https://data.geoserver.org/geoserver/web/

> **If our class GeoServer is unreachable today: we have a backup plan.** If the class droplet is down for any reason, we'll switch to a public federal GeoServer instance from the Multi-Resolution Land Characteristics (MRLC) Consortium `https://www.mrlc.gov/geoserver/web/?0`, a group of federal agencies that maintains the National Land Cover Database (NLCD) from Landsat imagery. On a backup day, you'll still complete the "consume" half of this lab exactly as written: pulling a GetCapabilities URL into ArcGIS Online (Screenshot 4) and into your own Leaflet page (Screenshot 5). What changes is that you won't be publishing your own layer into a workspace you control, so Screenshot 1 (GeoServer welcome/login), Screenshot 3 (your own layer in OpenLayers preview), and the "published to your own workspace" rubric criteria are waived for that session. If this happens, your instructor will confirm in class and post an updated Canvas note with the adjusted checklist and any extended deadline for revisiting the publish steps once our own server is back up.


## Step 1: Ask

Before publishing anything, sit with a couple of questions from lecture.

> **EQ3: What happens elsewhere in this system when I change one part of it, a tile size, a data format, a query, an interface choice, and who ends up reached, or excluded, on the other end?**
> 
> **Lab 02 Question: How can I publish and access a WFS or WMS layer outside of ArcGIS Online? What can this open-source option do/not do in comparison to the proprietary route?**

Take a minute and jot down your first-pass answers (edit the cell below):
- If you publish the *same* dataset as both a WMS and a WFS, what does each version let a user *do* that the other doesn't?
- GeoServer is free to install, but it has to run on a computer that's on and reachable. ArcGIS Online costs a license, but WNMU is always running it for you, as you already experienced in Lab 1. What does that tradeoff mean for who can realistically stand up a live geospatial service?

---


## Step 2: Collect

Overview: Log in to the class GeoServer instance for a quick exploration. Then grab a dataset to publish and add to our server.

<details>
<summary><b>Part 1: Open-source pathway - log in to the shared GeoServer instance. Click to expand</b></summary>

1. In your browser, go to the class GeoServer URL your instructor gave you (see Links above).
2. Log in with the credentials provided in class.
3. You should land on the GeoServer welcome page, the same interface anyone running their own instance would see. From here on, everything you do in Steps 3 and 4 is identical to a local install. The only difference is who is keeping the server running.
4. **Use your assigned workspace name** for everything you publish today (for example, `student01_giss366`). This is what keeps your layers separate from your classmates' on the same shared server.

> Curious what it takes to run this yourself? A local install path (native binary or Docker) is in the appendix at the end of this notebook if you want to try it outside of class. Not required for this lab.

</details>

<details>
<summary><b>Part 2: Get a dataset - NM RGIS Clearinghouse. Click to expand</b></summary>

1. Go to https://rgis.unm.edu/ and search for a small vector dataset covering Grant County or the Gila region. Trails, fire perimeters, and public land boundaries are good, manageable choices.
2. Download it as a **shapefile** (`.shp` and its sidecar files, usually zipped together as a single `.zip`).
3. **Leave the `.zip` file zipped.** Don't unzip it yourself — in Step 4 you'll upload this `.zip` as-is through the class upload page, which unzips it on the server for you.
4. *(Optional)* Open it in QGIS first just to confirm it looks right and note its coordinate reference system (CRS). You'll need to know this when you publish it.

</details>

📸 **Save a screenshot now:** the GeoServer welcome page after logging in. Label it *Screenshot 1*.

---


## Step 3: Visualize

Before publishing anything of your own, get oriented in GeoServer's interface and see what a `GetCapabilities` document actually looks like.

<details>
<summary><b>Explore GeoServer's built-in demo layers: click to expand</b></summary>

1. From the GeoServer welcome page, click **Layer Preview** in the left sidebar. Our GISS 366 Geoserver already has some demonstration layers from New Mexico published. This is a fast way to see the publish workflow's *output* before you've built one yourself.
2. Click **OpenLayers** next to any sample layer. This opens an interactive preview, similar to what your own layer will look like once published.
3. Now find that same layer's **WMS `GetCapabilities` URL**. Click **Layer Preview**, then look for the small format links, or go directly to `https://138-68-249-92.sslip.io/geoserver/wms?service=WMS&version=1.3.0&request=GetCapabilities`. Open it in a new tab.
4. Skim the raw XML. Find the list of `<Layer>` entries (this is the "menu" from lecture; note that on a shared instance, you'll see *everyone's* published layers listed here once your classmates start publishing too) and the supported `<Format>` types for `GetMap`.
5. Now edit that same URL, changing `GetCapabilities` to `GetMap` with a specific layer and bounding box (GeoServer's Layer Preview page generates one of these for you automatically; click **Common Formats → PNG** to see it in action). Compare: one URL gave you a menu, the other gave you a picture.

</details>

📸 **Save a screenshot now:** the raw `GetCapabilities` XML with at least one `<Layer>` entry visible. Label it *Screenshot 2*.

**What you noticed:**
- How many layers appeared in the `GetCapabilities` document?
- What image formats does `GetMap` support, based on what you saw?

---


## Step 4: Create

Now publish your own dataset, then try consuming it from both pathways.

<details>
<summary><b>Part 1: Open-source pathway - publish your dataset in GeoServer. Click to expand</b></summary>

Your dataset gets onto the server in two parts: first you **upload** the zipped shapefile through a small class upload page, then you **import and publish** it inside GeoServer itself. GeoServer's own file browser only sees files already sitting on the server, so the upload page is what bridges the gap between your laptop and the droplet.

**Part A — Upload your zipped shapefile**

1. Go to the class upload page (see Links above): `https://138-68-249-92.sslip.io/upload`
2. Log in with the same shared class credentials you used for GeoServer.
3. Type **your assigned workspace name**, exactly as it appears in GeoServer (for example, `student01_giss366`) — this determines where your file lands on the server, so a typo here means GeoServer won't be able to find it in Part B.
4. Choose your zipped shapefile (the `.zip` from Step 2 — still zipped, don't unzip it yourself) and click **Upload**.

**Part B — Import and publish it in GeoServer**

1. Go to `https://138-68-249-92.sslip.io/geoserver` and log in.
2. Go to **Import Data → Spatial Files**.
3. Click **Browse** next to the data directory field and navigate: **uploads → [your workspace] → [your file's folder]** (the folder will be named after your zip file, minus `.zip`). Make sure to not click any of the files within the folder, just be in the folder where the files are visible. Select it, then click **Next**.
4. GeoServer will detect the shapefile inside; check the box next to it, confirm the target workspace matches **your assigned workspace**, and click **Import**.
5. On the layer configuration page that follows:
   - Under **Data**, set the **Declared SRS** to match your data's CRS (QGIS told you this in Step 2, if you checked) and click **Compute from data** for the bounding boxes.
   - Under **Publishing**, note the default style applied. You can leave it, or explore GeoServer's built-in styles for something more legible (for example, a line style if your data is trails).
6.  Save. Go back to **Layer Preview**, find your new layer (filter by your workspace name if the list is long; remember, everyone in class is publishing to the same server), and open it in **OpenLayers** to confirm it renders.
7.  Your layer is now live as **both** WMS and WFS, automatically. Find both endpoints:
   - WMS `GetCapabilities`: `https://138-68-249-92.sslip.io/geoserver/wms?service=WMS&version=1.3.0&request=GetCapabilities`
   - WFS `GetCapabilities`: `https://138-68-249-92.sslip.io/geoserver/wfs?service=WFS&version=2.0.0&request=GetCapabilities`

> **If the shapefile doesn't appear when you Browse:** double-check the workspace name you typed on the upload page (Part A, step 3) exactly matches your GeoServer workspace name — a mismatch is the most common cause.

</details>

📸 **Save a screenshot now:** your published layer rendering in GeoServer's OpenLayers preview. Label it *Screenshot 3*.


<details>
<summary><b>Part 2: Proprietary pathway - consume it in ArcGIS Online. Click to expand</b></summary>

1. In ArcGIS Online, the same environment you published a hosted feature layer to in Lab 1 and practiced connecting to live services with in class, open Map Viewer and choose **Add → Add Layer from Web**.
2. Paste in your `GetCapabilities` URL from above. Choose **WMS OGC Web Service** (or **WFS**, if you'd rather bring in features instead of an image).
3. Because this is a real, always-on server rather than something running on your own laptop, this should actually connect. AGOL will parse the `GetCapabilities` document, list your published layer(s), and let you add one to the map.
4. Visualize the layer that you published on ArcGIS Online. If you selected WFS, practice changing the symbology. If you selected WMS, consider why and how you can't change any of these settings.

**If it doesn't connect on the first try**, check the following before assuming something's broken:
- Did you copy the full `GetCapabilities` URL, including your workspace name?
- Is the layer's style and SRS fully saved in GeoServer (above)?
- Try the WMS `GetCapabilities` URL directly in a new browser tab first. If it doesn't load there, AGOL won't be able to reach it either.

</details>

📸 **Save a screenshot now:** your layer successfully added in ArcGIS Online. Label it *Screenshot 4*.

---

<details>
<summary><b>Open-source pathway: consume it with a Leaflet WMS layer. Click to expand</b></summary>

This reuses the same `giss-366` folder and Live Server workflow from Lab 0, you're just pointing a small mapping page at a real, live WMS endpoint instead of a static basemap. The following code uses the Leaflet package, which we will dive into next week (week 4).

1. In VS Code, open your `giss-366` folder from Lab 0 (recreate it if you don't still have it).
2. Create a new file named `wms_test.html`.
3. Copy the code block below into `wms_test.html`, filling in the two placeholders (`[GEOSERVER-URL]` and `[your-workspace]:[your-layer]`) with your own values from above. Save the file.
4. In the file explorer, **right-click `wms_test.html` → Open With Live Server**. Your published layer should render as a raster tile layer inside MapLibre.
5. Pan and zoom. Open your browser's Dev Tools → Network tab and watch the requests fire, these are individual `GetMap` requests, the same request type you inspected by hand back in Step 3.

</details>

📸 **Save a screenshot now:** your WMS layer rendering inside your local MapLibre page. Label it *Screenshot 5*.

**Copy this into `wms_test.html`:**

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>GISS 366 – Lab 2 – WMS Layer</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <style>
    body { margin: 0; }
    #map { height: 100vh; width: 100vw; }
  </style>
</head>
<body>
  <div id="map"></div>
  <script>
    const map = L.map('map').setView([32.7701, -108.2803], 11); // Silver City -- swap to match your dataset's extent if it's elsewhere

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; OpenStreetMap contributors'
    }).addTo(map);

    L.tileLayer.wms('[GEOSERVER-URL]/wms', {
      layers: '[your-workspace]:[your-layer]',
      format: 'image/png',
      transparent: true
    }).addTo(map);
  </script>
</body>
</html>
```

> **If the layer doesn't appear:** First - check that you've entered in the [GEOSERVER-URL] and '[your-workspace]:[your-layer]' spelling exactly as they appear on our course website. Just like Lab 01 SQL querries, even a spelling mispelling can cause and eror. 
>
> Once you've confirmed spelling, 'open Dev Tools' → Console first. A CORS error there (something like *"blocked by CORS policy"*) points to a server-side setting on the GeoServer instance itself, not something fixable from your `.html` file (you've likely done everything right!). Flag it on the discussion board and send a message to Alex rather than troubleshooting alone. This is a geoserver setting configuration that Alex can fix and will be of benefit to everyone's mapping. This shouldn't be an issue with the leaflet example above, but please note this and be aware. 
> 
> This is a great examples of the limitations behind the client-server architecture of web mapping. Even if you (the client) has completed all building steps correctly, issues on the server end (data stores/the cloud) can inhibit your overall web map function. Consider how reliance on external infastructure might change the dynamic about who really is "in charge/in control" of the web maps that you build, and how self-hosting your own services might be one path towards further "data sovereignty."


## Step 5: Act

### What to turn in (graded: Lab Exercises)

1. Your five screenshots:
   - **Screenshot 1**: GeoServer welcome page (Collect)
   - **Screenshot 2**: raw `GetCapabilities` XML (Visualize)
   - **Screenshot 3**: your own layer in GeoServer's OpenLayers preview (Create)
   - **Screenshot 4**: your layer in ArcGIS Online (Create)
   - **Screenshot 5**: your layer rendering in your local MapLibre page via WMS (Create)
2. A short written reflection (4-6 sentences) answering:
   - Walk through, in your own words, what happened between your browser (via AGOL, or via your MapLibre page) and GeoServer for **one** of your requests today. What was requested, and what came back?
   - Revisit **EQ3**: what did publishing the *same* dataset as both WMS and WFS actually let you do differently between the two? Which one would you reach for if you needed to symbolize the data yourself in a client, versus just needing a quick reference layer?
   - Today's GeoServer instance is shared and always-on rather than something you installed yourself. Compare that to a hypothetical version of this lab where you ran GeoServer on your own laptop. What would have changed about the ArcGIS Online step, and who is responsible for that server staying up between now and finals week?
   - Now that you've published through both a hosted platform (Lab 1, AGOL) and a self-hosted server (Lab 02), which felt like it gave you more control, and which felt easier? Was that the tradeoff you predicted in Lab 1's EQ2 answer?
   - What's one thing that didn't work smoothly today, and what was your first troubleshooting move: docs, a web search of the exact error, or the course discussion board?

Post your screenshots and reflection to this week's submission space in Canvas.

---

## Lab 02 Rubric (40 pts)

>**Contingency note:** if class ran on the MRLC backup GeoServer (see "Before You Begin" above) because our own droplet was unreachable, Screenshot 1, Screenshot 3, and the "published to your own workspace" Process criterion below are waived for that submission. Your instructor will confirm in Canvas whether this applies and what, if anything, is due later once our class server is back up.

| Score Band | What It Looks Like |
|:---|:---|
| **Exceptional (7-8)** | Exceeding expectations; indicates mastery; in-depth understanding; higher-order thinking; inferences and extensions of learning objectives that go beyond what was taught; truly superb effort. |
| **Proficient (5-6)** | Meeting expectations; application of concepts; independently demonstrates understanding and thorough competency of learning objectives explicitly taught. |
| **Developing (3-4)** | Approaching expectations; demonstration of basic understanding without application and understanding of more complex ideas and processes; meets minimum requirements for satisfactory learning. |
| **Insufficient (1-2)** | Below expectations; partial or no demonstration of understanding and progress toward learning objectives; major errors and omissions present; inadequate for competency. |

<br>

| Criteria | Comments | Grade |
|:---|:---|:---:|
| **Content:** All five screenshots are present and correctly show what's asked. Screenshot 1 shows a successful login, Screenshot 2 shows readable `GetCapabilities` XML with a visible `<Layer>` entry, Screenshot 3 shows the student's own layer rendering in OpenLayers, Screenshot 4 shows that layer successfully added in ArcGIS Online, and Screenshot 5 shows that same layer rendering as a WMS source inside the student's own local MapLibre page. |  | / 8 |
| **Content:** The written reflection completely and directly answers all five prompts: what was requested and returned for one interaction today, the WMS versus WFS distinction tied to EQ3, the shared-server versus self-hosted comparison, the control-versus-ease comparison to Lab 1's EQ2, and a specific troubleshooting moment and method. The response shows real understanding, with specific detail and reasoning behind it. |  | / 8 |
| **Process:** The layer was published correctly to the student's assigned workspace, with an accurate Declared SRS, computed bounding box, and a deliberate style choice rather than an untouched default. Both the WMS and WFS endpoints are live and correctly referenced. |  | / 8 |
| **Process:** The reflection is evidence-based and specific, grounded in the student's actual `GetCapabilities` output and query results. It draws an explicit, reasoned connection between EQ3 and the WMS/WFS distinction, and revisits Lab 1's EQ2 when comparing hosted and self-hosted publishing. |  | / 8 |
| **Product:** Screenshots and written reflection are submitted in the requested format. Work is professionally presented, clearly organized and labeled, fully functional (screenshots are legible and match what's described), and free of errors. |  | / 8 |
| **Total** |  | **/ 40** |

---

### Lab 02 AI Policy - Level 2

This lab permits optional use of AI to support student learning. Permitted uses can include using AI to help troubleshoot GIS workflows, however, independent installation and use of AI agents to complete GIS tasks is not permitted at this time. AI may also be used to support general writing procedures (outlining a project, improving clarity or grammar, etc.), but all responses must remain original to each student as a sole responsible author. Disclosure of AI use is not required for this Level 2 assignment. Using AI is a choice with ethical considerations, and it is asked that students weigh those considerations accordingly as they complete this work.

### Looking Ahead

Week 4 picks up the sharing-decision thread directly, in **Discussion 1**: open data, community consent, and what "open" actually obligates a publisher to consider. Revisit your EQ answers from both Lab 1 and today before that discussion. Week 6 comes back to the tiling and cloud-native formats flagged in lecture; it's worth re-reading your EQ3 answer above once you get there, to see if a static, serverless approach changes what you think the tradeoffs are.

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

**Option A: Native installer**
1. Download the platform-specific installer or binary from https://geoserver.org/download/ (the "Platform Independent Binary" works everywhere if you have Java installed; installers exist for Windows and macOS).
2. Start GeoServer (on the binary, run `bin/startup.sh` or `bin/startup.bat`).
3. In your browser, go to `http://localhost:8080/geoserver`. You should see the GeoServer welcome page.
4. Log in with the default credentials (`admin` / `geoserver`).

**Option B: Docker**
1. Install Docker Desktop if you don't already have it: https://www.docker.com/products/docker-desktop/
2. Run: `docker run -p 8080:8080 kartoza/geoserver`
3. Once it finishes starting up, go to `http://localhost:8080/geoserver` and log in as above.

If you want to try connecting a locally installed instance to ArcGIS Online the way earlier semesters did, you'll need a tunneling tool like [ngrok](https://ngrok.com/) to expose `localhost` to the open internet. This is exactly the gap the class server closes for you.

## Appendix: MRLC backup options

Links:
GeoServer - `https://www.mrlc.gov/geoserver/web/?0`

```html
L.tileLayer.wms('https://www.mrlc.gov/geoserver/mrlc_display/wms', {
      layers: 'mrlc_display:NLCD_2021_Land_Cover_L48',
      format: 'image/png',
      transparent: true
    }).addTo(map);

```
