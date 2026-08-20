# Lab 0
# Environment Setup: ArcGIS Online & Open-Source Web GIS
## GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 1 Focus:** What is Web GIS? · Client-server architecture · Proprietary (ArcGIS Online) and open-source/cloud-native pathways, side by side

*This is a practice lab, ungraded. The goal is a working dual-stack environment before Lab 1's graded work begins next week.*

---

## Learning Goals

By the end of this practice lab, you will be able to:
- Explain, using your own setup as evidence, the difference between **Web Mapping** and **WebGIS**.
- Explain, in your own words, what happens between a browser and a server when a web map loads.
- Use your browser's **Developer Tools** to watch a real web map make network requests, connecting tonight's lecture to something you can see for yourself.
- Sign in to WNMU's **ArcGIS Online** organizational account and confirm you can create and save your own content.
- Set up the beginnings of an **open-source web GIS dev environment**: a GitHub account, VS Code with its first extensions, and a working local Python install that you'll build on all semester.
- Get a small interactive **MapLibre** web map running locally in VS Code as your first hands-on touch of the open-source pathway.
- Know your first move when you get stuck on setup: docs, then search the exact error, then the course discussion board.

## How This Notebook Is Organized: the Geo-Inquiry Process

<img src="https://blog.education.nationalgeographic.org/wp-content/uploads/2017/10/geoinquiry.jpg?w=720"
     alt="Geo-Inquiry Process"
     style="float: left; width: 200px; margin: 0 24px 12px 0;">

Our Labs in this course follows the [**Geo-Inquiry Process**](https://education.nationalgeographic.org/resource/geo-inquiry-resources/) Developed by National Geographic Education. The Geo-Inquiry Process relies on using a geographic perspective, offering a unique lens to analyze space, place, and the interconnections between both the human and natural world. Using both a geographic perspective and this structure, we can begin to connect complex components, see patterns, and make connections offer contribute in valuable ways to communities,.

 You've likely seen this process before. Here, we're applying it to our environmental setup instead of a mapping question.

<div style="clear: both;"></div>

## Where We're Working: Silver City & Southwestern New Mexico

This course uses the WNMU home region including Silver City, Grant County, and the Gila National Forest as a shared reference point for early labs. You'll pick your own place and audience for the semester's Public Web GIS Inquiry starting in Week 11, but for now, one shared, real place lets everyone compare notes while getting the tools working.

## A Note on "Tech Stacks"

This course treats proprietary (ArcGIS Online) and open-source/cloud-native (GitHub, static hosting, MapLibre/Leaflet) publishing as **parallel, equally real pathways** that you'll use *both* of, all semester. Learning both can position you well for future geospatial career applications. Today, you're setting up **both** environments, side by side. Some labs later on will let you choose which stack to go deeper on, but the infrastructure for both starts today.

## Before You Begin

| Tool | Cost / Access | Install? | Role this semester |
|---|---|---|---|
| **ArcGIS Online** | Provided via WNMU organizational account | None; runs in your browser | Proprietary pathway: Lab 1 onward |
| **GitHub** | Free account | None; web-based for now, a local `git` install comes later | Hosts your open-source pathway work and (eventually) your final project site |
| **VS Code** | Free | Download & install ([code.visualstudio.com](https://code.visualstudio.com/)), plus the **Python** and **Live Preview** extensions | Where you'll write HTML/CSS/JS all semester — tonight's the first time you'll open it |
| **A modern browser with Dev Tools** | Free | Chrome recommended, Firefox fine | How you'll inspect every web map's network traffic all semester |
| **Python 3** | Free | Usually already on your machine, or via this notebook's host | Backup local web server if you skip Live Preview; `leafmap`/`folium` for the optional appendix |

**Links**
- WNMU ArcGIS Online sign-in: Request access this week if you don't have it yet.
- GitHub: https://github.com/join
- VS Code download: https://code.visualstudio.com/download
- Course content site (optional): https://asivitskis.github.io/giss-366
- NM RGIS Clearinghouse (our regional data source all semester): https://rgis.unm.edu/
- MapLibre GL JS docs: https://maplibre.org/maplibre-gl-js/docs/
- OpenFreeMap (free hosted basemap tiles, no account needed): https://openfreemap.org/quick_start/

## Step 1: Ask

### What is the difference between Web Mapping and WebGIS?

This question came up in lecture. Before you open any software tonight, just document your intial thoughts.

**Your first-pass answers:**
- Before tonight, what did you picture happening "under the hood" when a web map loaded, if you pictured anything at all?
- Web Mapping vs. WebGIS, in your own words right now, before we build anything:

We'll test and refine this all semester, including a running thread on who gets left out of these maps by device, bandwidth, or account access (the seed of the Digital GeoEthics strand in this course).

---


## Step 2: Collect

Tonight we'll walk through this together: every account and install you need, live, so nobody's troubleshooting alone. Nothing gets built yet.

<details>
<summary><b>Proprietary pathway: ArcGIS Online (click to expand)</b></summary>

1. Go to the ArcGIS Online sign-in link posted in the course LMS (Week 1 announcement).
2. Sign in with your WNMU credentials. If you don't have access yet, request it now. This is the single most common Lab 0 delay, so don't wait until Thursday.
3. Once signed in, find your **Content** page and your **Organization** page in the top navigation. You don't need to do anything with them yet, just confirm they load.
4. Check your profile settings for your **display name** and **organization role** (Viewer, Creator, etc.). Note your role below; it determines what you can publish later in the course.

</details>

<details>
<summary><b>Open-source pathway: GitHub (click to expand)</b></summary>

1. If you don't already have one, create a free account at https://github.com/join.
2. Confirm you can see your GitHub username in the top-right corner when signed in.

</details>

<details>
<summary><b>Open-source pathway: VS Code + extensions (click to expand)</b></summary>

1. Install **VS Code** if you haven't already: https://code.visualstudio.com/download.
2. Open VS Code and install two extensions (Extensions icon in the sidebar, or `Ctrl+Shift+X` / `Cmd+Shift+X`):
   - **Python** - search "Python" (by Microsoft), Install. You'll use this starting Week 8.
   - **Live Server** - search "Live Server" (by Ritwick Dey), Install. This is what lets you view an HTML file as a live local web page, with no server setup of your own.
3.  Somewhere on your computer, create a new file labeled `giss-366`. Your documents folder works fine. This will be our base of operations for the full semester.

</details>

**For future personal reference, note down your ArcGIS Online organization role.**

---

## Step 3: Visualize

Accounts and tools are in place. Now let's build something and use it to start answering our guiding question.

### A working distinction

Here's a rough working distinction, one we'll test and refine all semester:

- **Web Mapping** is a map as a *product*: something built for and displayed on the web, for viewing. A basemap someone can pan and zoom is web mapping, even if it looks polished and professional.
- **WebGIS** is the fuller *system* behind that map: a client-server setup that lets you not just view spatial data but query, analyze, or edit it, usually through some kind of GIS service (a feature service, WMS/WFS, a spatial database) rather than just static tiles.

Every WebGIS shows you a web map. Not every web map is a WebGIS. There isn't a single technical switch that turns a web map into WebGIS. Instead, we'll explore how maps become increasingly connected to geographic data, services, and computational processes. Keep this in mind as we go forward.

### Part A - A simple web map in ArcGIS Online

<details>
<summary><b>Making a fresh ArcGIS Online map (click to expand)</b></summary>

1. From your ArcGIS Online home page, choose **Map** (or **Create → Map**) to open the Map Viewer.
2. Use the basemap gallery to pick any basemap you like. Topographic and Imagery are good starting points.
3. Use the search bar to navigate to **Silver City, New Mexico**.
4. (OPTIONAL) Save the map (**Save → Save As**) with a title like `GISS 366 – Lab 0 – [Your Name]`. Saving is what confirms your account can actually create and store content, not just view it.

</details>

**Is this Web Mapping, or WebGIS?** Right now, this map only displays a basemap. You're viewing spatial data, not querying, analyzing, or editing anything. By the working distinction above, that makes it **Web Mapping**: a map product built for and shared on the web. The platform underneath it (ArcGIS Online) is capable of much more, which you'll start using in Lab 1.

### Part B - See the client-server exchange with Dev Tools

Every web map, no matter how simple, is quietly running the architecture that WebGIS is built on top of. Let's make that visible on the map you just made.

<details>
<summary><b>Watching a web map talk to a server (click to expand)</b></summary>

1. Go back to your saved ArcGIS Online map (or reopen one of the two lecture example maps from Ask).
2. Open **Developer Tools**:
   - Chrome/Edge: right-click anywhere on the page → **Inspect**, or press `F12`
   - Firefox: right-click → **Inspect**, or press `F12`
3. Click the **Network** tab inside Dev Tools. Refresh the page.
4. Watch the list fill in. Pan or zoom the map and keep watching; you should see new requests appear for each new area you bring into view.
5. Click on a couple of individual requests. Look for:
   - The **request URL**: does it look like a request for a *tile* (a small square image or block of data), a style file, or something else?
   - The **response size**, usually a few KB per tile, not megabytes. This is exactly the "chop the world into small requestable pieces" idea from tonight's lecture.
   - The **type/format**: `png`/`jpg` (raster tile), or something like `pbf`/`mvt` (vector tile)?

This is the **client-server architecture** underneath everything we're calling "web GIS" this semester: your browser (the client) asking a server for small pieces of data, over and over, and rendering what comes back. This is what separates plain web mapping from WebGIS. What the server is sending back, how this information is being initially stored and served, and eventually how we might query, edit, and anlyze this data.

</details>

**Save a screenshot now** of your Network tab with at least a few tile requests visible. Label it *Screenshot 1*; you'll turn it in during Act.

**Write down a few things you noticed. Some examples could include:**
- Request URL pattern:
- Raster or vector tiles (or both)?
- Roughly how big was one tile response?

---


## Step 4: Create

Now let's build toward WebGIS from the open-source side: hand-author the client half of that same request/response architecture you just watched happen to the ArcGIS Online map.

<details>
<summary><b>Running your first MapLibre map (click to expand)</b></summary>

1. Somewhere on your computer, create a new file labeled `giss-366`. Your documents folder works fine. This will be our base of operations for the full semester.
2. Open this folder in VSCode - either by **right click → Open with Code** or opening the file within the VS Code program.   
2. Create a new file in your `giss-366` folder named `test_map.html`.
3. Copy the full code block below and paste it into `test_map.html`. Save the file.
4. In the file explorer, **right-click `test_map.html` → Open With Live Server** (this is the Live Preview extension). This html file will then load in your browser with an interactive map centered on Silver City.
5. Try panning and zooming. Use your browser's developer tools too notice how this is the exact same tile request/response loop you watched in Dev Tools during Visualize, just running locally now, driven by code you pasted in yourself.
6. Find the `style:` line and try swapping in one of the other basemap options listed in the code block below. Save the file; Live Preview should refresh automatically.
7. Optional: swap the `center:` coordinates to a place of your choosing (remember, MapLibre wants `[longitude, latitude]`, the reverse of the `(latitude, longitude)` order you'll see elsewhere this semester).

</details>

**Save a screenshot now** of your local MapLibre map running in LiveServer. Label it *Screenshot 2*; you'll turn it in during Act.

**Is this Web Mapping, or WebGIS?** Same answer as Part A, for the same reason: this page displays a basemap and a marker, nothing more (no querying, no live data, no editing). It's web mapping, just built by hand instead of through a platform's interface. It's a real step toward WebGIS, though: you're now authoring the client side of the exact architecture WebGIS depends on. Lab 1's feature-service work is where that architecture starts carrying more than a basemap.

**Copy this into `test_map.html`:**

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>GISS 366 – Lab 0 – MapLibre Test</title>
  <!-- MapLibre GL JS docs: https://maplibre.org/maplibre-gl-js/docs/ -->
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
    const map = new maplibregl.Map({
      container: 'map',
      style: 'https://tiles.openfreemap.org/styles/liberty', // <-- swap this string
      center: [-108.2803, 32.7701], // <-- optional: swap to your own place. MapLibre wants [longitude, latitude]
      zoom: 12
    });

    // MapLibre Map object + methods reference:
    // https://maplibre.org/maplibre-gl-js/docs/API/classes/Map/

    map.addControl(new maplibregl.NavigationControl());

    new maplibregl.Marker()
      .setLngLat([-108.2803, 32.7701]) // <-- if you moved the center above, move this marker to match
      .setPopup(new maplibregl.Popup().setText("WNMU / Silver City"))
      .addTo(map);
  </script>
</body>
</html>
```

```html
<!DOCTYPE html>
<html>

<body>
  <script>
    // Try swapping the "style" line below for a different look.
    // OpenFreeMap's free hosted styles (no key, no account): https://openfreemap.org/quick_start/
    //   'https://tiles.openfreemap.org/styles/liberty'   <- general-purpose, labeled
    //   'https://tiles.openfreemap.org/styles/bright'    <- higher-contrast, saturated
    //   'https://tiles.openfreemap.org/styles/positron'  <- light, minimal, good for overlays
    //   'https://tiles.openfreemap.org/styles/dark'      <- dark background, good for data viz
    //
    // Full style spec reference (what you can change and how):
    // https://maplibre.org/maplibre-style-spec/
  </script>
</body>
</html>

## Step 5: Act

The "Act" step is about confirming both environments work and reflecting on what today's setup means for the semester ahead, not just checking boxes.

### OPTIONAL What to turn in (practice, ungraded)

1. Your three screenshots:
   - **Screenshot 1**: your saved ArcGIS Online map of Silver City (Visualize, Part A) with your browser's Network tab showing tile requests (Visualize, Part B)
   - **Screenshot 2**: your local MapLibre map running in VS Code (Create).
2. A short written reflection (4–6 sentences) answering:
   - **Answer our guiding question**: in your own words now, what is the difference between Web Mapping and WebGIS? Point to something concrete from tonight (for example the AGOL map, the Dev Tools exchange, or the MapLibre page) as evidence.
   - Before tonight, did you think of a web map as something "static" (like a picture) or something "live" (constantly asking a server for more)? Has that changed?
   - Revisit **EQ1**: you now have an account that costs WNMU an organizational license (ArcGIS Online), an account that's free to anyone (GitHub), and a local map you built with tools that needed no account at all (VS Code + MapLibre + OpenFreeMap). What's one concrete way that spread of cost and access could shape who gets to publish a web map about a place like Grant County?
   - What's one thing that didn't work smoothly today, and what was your first troubleshooting move: docs, a web search of the exact error, or the course discussion board?

Post your screenshots and reflection to this week's assignment space in Canvas. This is a practice activity, not graded, but it's a hard prerequisite for Lab 1.

### Looking Ahead

Next week's **Lab 1** has you publishing a hosted feature layer and building a web map in ArcGIS Online. The first time a map you build will cross from web mapping into WebGIS, since you'll be working with data that can be queried, not just tiles for display. The week after, **Lab 2** moves to the open-source side: publishing and consuming a live WMS/WFS service with GeoServer, your first taste of the open standards that let proprietary and open-source tools talk to each other. Having both environments working today is what makes both of those possible.

---
### Resources

- Course content site: https://asivitskis.github.io/giss-366
- ArcGIS Online: access via course LMS
- GitHub: https://github.com/
- VS Code: https://code.visualstudio.com/
- MapLibre GL JS: https://maplibre.org/maplibre-gl-js/docs/
- OpenFreeMap: https://openfreemap.org/quick_start/
- NM RGIS Clearinghouse: https://rgis.unm.edu/
- Dorman, M. *Introduction to Web Mapping*, Preface
- `leafmap` (Python): https://leafmap.org/

---
## Appendix: Python Code (Optional)

Everything above this line is all you need for this practice lab. The cells below are optional and collapsed by default in most notebook viewers. They give a first, tiny taste of the Python-based mapping you'll do later in the open-source pathway — a different route to a similar result as the MapLibre map you just built by hand.

Nothing here is required, and nothing here is graded.


```python
# OPTIONAL -- a first taste of Python-based mapping (we'll do much more with this later in the semester)
# If leafmap isn't installed, uncomment the line below:
# !pip install leafmap -q

import leafmap

silver_city = (32.7701, -108.2803)  # (latitude, longitude)

m = leafmap.Map(center=silver_city, zoom=11)
m.add_basemap("Esri.WorldImagery")
m
```


```python
# OPTIONAL -- save this first map as a shareable HTML file, and confirm you can find it afterward
m.to_html("week1_first_map.html")
print("Saved week1_first_map.html -- open it in a browser to see your first exported web map.")
```
