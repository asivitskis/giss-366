# What is Web GIS?

**GISS 366/368 | Week 1 | Lecture Resources + Lab 0 Preparation**

---


## Learning Objectives

By the end of this unit, you will be able to:

- Describe what Web GIS is and how it differs from desktop GIS
- Explain the client-server model underlying every web map — what a browser requests, and what a server (or a file in cloud storage) sends back
- Compare the proprietary (ArcGIS) and open-source/cloud-native publishing landscapes as **parallel pathways** through this course, not a primary tool and a fallback
- Explain how *where* a web map's data lives (a managed platform, a self-hosted server, or static cloud storage) shapes its cost, control, and who can access it
- Know where to go when you get stuck on a web GIS problem — documentation, forums, and the course community
- Leave tonight with a clear list of what to set up before Thursday's Lab 0


---


## 1.1 From Desktop to Web

For most of GIS's history, geographic data lived inside desktop software — ArcMap, ArcGIS Pro, QGIS — running on a single computer. If you wanted to share a map, you shared a file: a shapefile, a geodatabase, a printed PDF. The data and the software that could read it had to sit on the same machine, or at least the same local network. That's a real limitation. A field ecologist's beautifully built map of rare plant observations was really only visible to whoever could open the right software with the right file.

Web mapping changes the delivery mechanism, not the underlying discipline. A **web map** is an interactive display of geographic information delivered as a web page — reachable from any device with a browser and an internet connection, no specialized software required. That accessibility is the whole point: your audience is no longer "people with ArcGIS Pro installed," it's *anyone with a link*.

The architectural shift that makes this possible is often summarized as **going from files to services**:

| | Desktop GIS | Web GIS |
|---|---|---|
| Data | A local file you open (`.shp`, `.gdb`) | A service or resource you *request* over the network |
| Software | Installed on your machine | Runs partly in the browser, partly on a server (or in a static file) |
| Audience | Whoever has the software + the file | Whoever has a browser + a link |
| Availability | Only while you have the file open | Live, as long as the service/host is up |

Web maps are also **interactive**: viewers can turn layers on and off, zoom to what they care about, click a feature to inspect its attributes, and — in some cases — submit or edit content themselves. And critically, most web maps are *powered by* the web rather than just *published on* it: at least some of what you see is being loaded live from somewhere else (a tile server, an API, a cloud storage bucket) every time you interact with the map. That dependency on the network is exactly what we unpack next.

Web maps now show up everywhere: data journalism (ship-traffic trackers, election maps), real-time dashboards (live weather, flight tracking, wildfire monitoring), searchable data catalogs, computational tools (route planners, solar-position calculators), and collaborative/crowdsourced platforms like OpenStreetMap. By the end of this course you'll have built examples across several of these categories yourself.


## 1.2 Client-Server Architecture

Every web map you have ever used is powered by the same basic exchange: a **client** (your browser) asks a **server** (or a file sitting in storage) for something, and gets a response back. Understanding this loop is the single most important idea in this course — it's the foundation Essential Question EQ1 asks you to keep interrogating all semester: *what is actually happening between my browser and the server when this map loads?*

**Why tiles?** Your browser can't just load a 500 MB shapefile and render it instantly — that would freeze the page. Long ago, the solution the mapping industry converged on (Google popularized it) was to chop the world into small square **tiles** that change at each zoom level, and load only the tiles currently in view. As you pan, the browser requests the tiles for the new area; as you zoom, it requests a new, more (or less) detailed set. Already-seen tiles get cached locally, so revisiting an area doesn't require another trip to the server.

There are two flavors of tile:

- **Raster tiles** — pre-rendered PNG/JPEG images, baked at creation time. Fast to display, but the styling is fixed: to change a road's color, someone has to re-render the entire tile set. No built-in interactivity — you'd need a separate layer just to make a tile clickable. Classic example: older Google Maps basemaps; still common in parts of the Esri ecosystem.
- **Vector tiles** — small packets of raw geometry and attribute data (in a format called protocol buffers), sent to the browser *without* any styling baked in. The browser applies styling rules on the fly, which means colors, filters, hover effects, and click behavior can all change instantly, with no server round-trip. This is what nearly every modern web map — including the ones you'll build in this course — uses under the hood.

**Where do the tiles live?** Historically, tiles had to be served by a running server — a computer that's always on, listening for requests (think: a GeoServer instance, or Google's own infrastructure). That's different from **storage**, which is just a file sitting somewhere on the web with nothing actively "running." One of the biggest recent shifts in web GIS is the emergence of formats like **PMTiles**, which package an entire tile set into a single file that can sit in plain cloud storage. The browser fetches only the specific bytes it needs using an HTTP *range request* — no server required at all. We'll return to this in Week 6, but it's worth knowing now: the gap between "I need a server" and "I need a file" has been closing fast, and it's reshaping what a hobbyist or a small budget can build.

Here's the request/response loop in its simplest form:


<svg viewBox="0 0 760 260" xmlns="http://www.w3.org/2000/svg" style="max-width:100%; height:auto; font-family: -apple-system, Helvetica, Arial, sans-serif;">
  <defs>
    <marker id="arrow" markerWidth="10" markerHeight="10" refX="8" refY="3" orient="auto" markerUnits="strokeWidth">
      <path d="M0,0 L0,6 L9,3 z" fill="#444"/>
    </marker>
  </defs>

  <!-- Browser box -->
  <rect x="30" y="70" width="180" height="110" rx="10" fill="#eaf2fb" stroke="#3b6ea5" stroke-width="2"/>
  <text x="120" y="115" text-anchor="middle" font-size="16" font-weight="bold" fill="#1f3d5c">Browser</text>
  <text x="120" y="138" text-anchor="middle" font-size="12" fill="#1f3d5c">(the client)</text>
  <text x="120" y="160" text-anchor="middle" font-size="11" fill="#1f3d5c">HTML + CSS + JS</text>

  <!-- Server/storage box -->
  <rect x="550" y="70" width="180" height="110" rx="10" fill="#fdeee0" stroke="#c17a2e" stroke-width="2"/>
  <text x="640" y="108" text-anchor="middle" font-size="16" font-weight="bold" fill="#6b3d0f">Tile server</text>
  <text x="640" y="128" text-anchor="middle" font-size="16" font-weight="bold" fill="#6b3d0f">or static storage</text>
  <text x="640" y="152" text-anchor="middle" font-size="11" fill="#6b3d0f">(GeoServer, S3, PMTiles file...)</text>

  <!-- Arrow: request -->
  <line x1="212" y1="105" x2="546" y2="105" stroke="#444" stroke-width="2" marker-end="url(#arrow)"/>
  <text x="380" y="90" text-anchor="middle" font-size="12" fill="#333">GET tile: zoom 11, x=602, y=70</text>

  <!-- Arrow: response -->
  <line x1="546" y1="150" x2="212" y2="150" stroke="#444" stroke-width="2" marker-end="url(#arrow)"/>
  <text x="380" y="170" text-anchor="middle" font-size="12" fill="#333">Response: a few KB of tile data</text>

  <!-- Caption -->
  <text x="380" y="235" text-anchor="middle" font-size="12" fill="#666">Every pan and zoom repeats this loop — the browser asks for only the tiles it needs, and caches what it already has.</text>
</svg>


**Live demo idea (in class):** open [openstreetmap.org](https://www.openstreetmap.org), open your browser's Developer Tools (F12 or right-click → Inspect), switch to the **Network** tab, and pan/zoom the map. Watch the individual tile requests fire in real time — this is EQ1 made visible. We'll do this together tonight if time allows; it's also a great one to repeat on your own before Lab 0.


### Example 1: a live open-source web map

The cell below uses [folium](https://python-visualization.github.io/folium/) (a Python wrapper around the open-source **Leaflet** JavaScript library) to build an actual interactive, tile-based web map — right here in the notebook. Run it, then pan and zoom: every move you make is triggering the exact request/response loop diagrammed above. If you have `folium` installed, this will render inline; otherwise, `pip install folium` first.



```python
# A minimal open-source, tile-based web map — Leaflet under the hood via folium
# This is the same request/response pattern shown in the diagram above, just built with a few lines of Python.

import folium

m = folium.Map(
    location=[32.7754, -108.2829],  # WNMU Campus, Silver City NM
    zoom_start=12,
    tiles="OpenStreetMap",
)

m

```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe srcdoc="&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;

    &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot; /&gt;

        &lt;script&gt;
            L_NO_TOUCH = false;
            L_DISABLE_3D = false;
        &lt;/script&gt;

    &lt;style&gt;html, body {width: 100%;height: 100%;margin: 0;padding: 0;}&lt;/style&gt;
    &lt;style&gt;#map {position:absolute;top:0;bottom:0;right:0;left:0;}&lt;/style&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://code.jquery.com/jquery-3.7.1.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap-glyphicons.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.2.0/css/all.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css&quot;/&gt;

            &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no&quot; /&gt;
            &lt;style&gt;
                #map_70df0809bca673ce65327d398df37c6a {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
                .leaflet-container { font-size: 1rem; }
            &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;


            &lt;div class=&quot;folium-map&quot; id=&quot;map_70df0809bca673ce65327d398df37c6a&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;


            var map_70df0809bca673ce65327d398df37c6a = L.map(
                &quot;map_70df0809bca673ce65327d398df37c6a&quot;,
                {
                    center: [32.7754, -108.2829],
                    crs: L.CRS.EPSG3857,
                    ...{
  &quot;zoom&quot;: 12,
  &quot;zoomControl&quot;: true,
  &quot;preferCanvas&quot;: false,
}

                }
            );





            var tile_layer_c16b543e160babdbf227dd20b5fd0c2c = L.tileLayer(
                &quot;https://tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {
  &quot;minZoom&quot;: 0,
  &quot;maxZoom&quot;: 19,
  &quot;maxNativeZoom&quot;: 19,
  &quot;noWrap&quot;: false,
  &quot;attribution&quot;: &quot;\u0026copy; \u003ca href=\&quot;https://www.openstreetmap.org/copyright\&quot;\u003eOpenStreetMap\u003c/a\u003e contributors&quot;,
  &quot;subdomains&quot;: &quot;abc&quot;,
  &quot;detectRetina&quot;: false,
  &quot;tms&quot;: false,
  &quot;opacity&quot;: 1,
}

            );


            tile_layer_c16b543e160babdbf227dd20b5fd0c2c.addTo(map_70df0809bca673ce65327d398df37c6a);

&lt;/script&gt;
&lt;/html&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



### Example 2: an embedded ArcGIS Online web map/app

ArcGIS Online web maps and apps can be embedded directly in a page (or a notebook) as an `<iframe>`. In ArcGIS Online, open a web map or app, choose **Share → Embed**, and copy the generated `<iframe>` code — it will look like the placeholder below. Swap in a live public web map's item ID before lecture.

> **UPDATES INCOMING FROM WNMU AGOL:** potential Survey123 class layer + hosted and embedded AGOL map.


<iframe
  src="https://www.arcgis.com/apps/Embed/index.html?webmap=YOUR_ITEM_ID_HERE&extent=-98,25,-71,49&zoom=true&scale=true"
  width="100%" height="450" frameborder="0" style="border:1px solid #ccc; border-radius:8px;" allowfullscreen>
</iframe>


### 5-minute discussion (breakout or whole-class)

Compare the two maps you just interacted with — one open-source, one proprietary.

1. What did each one *feel* like to use? Faster, slower, more/less polished?
2. Neither map required you to install anything — but each one still depends on infrastructure you can't see. What do you think that infrastructure is, for each?
3. **EQ1 preview:** who might be excluded from using either of these maps — by device, bandwidth, or account access — and does that differ between the two?

Keep this question in the back of your mind all semester — Discussion 1 in Week 4 comes back to it directly.


## 1.3 The Modern Web GIS Landscape

There are two broad ways to build and publish a web map today, and this course treats both as **parallel, professionally-valued pathways** — not a primary tool and an optional workaround. You'll get hands-on with both stacks across the semester.

| | Proprietary (Esri) | Open-source / cloud-native |
|---|---|---|
| Core tools | ArcGIS Online, ArcGIS Enterprise, Experience Builder, ArcGIS Maps SDK | GeoServer, MapLibre GL JS, Leaflet, PMTiles, Cloud-Optimized GeoTIFF, GeoParquet |
| Typical workflow | Publish from ArcGIS Pro → ArcGIS Online → optionally build a no-code app in Experience Builder | Export/convert data (e.g., GeoJSON → PMTiles) → build a map with a JS library → host as static files |
| Cost model | Subscription / organizational license, credit-based | Free and open-source tools; hosting cost only (often ~free on GitHub Pages) |
| Governance | Centralized: one portal manages users, data access, and permissions | You build governance yourself — nothing is managed for you by default |
| Strongest fit | Organizations already on Esri; ESRI-exclusive tools (Network Analyst, Living Atlas); non-technical teams who need drag-and-drop app building; compliance environments requiring certified infrastructure | Budget-constrained projects, portfolios, full creative/technical control, no account or licensing required |

Neither column is "the real one" and the other a shortcut. A practitioner who only knows one stack is stuck when the budget, the client, or the compliance requirements change. And in practice, the two pathways often **coexist** on the same project: an analysis pipeline in PostGIS/Python can feed both an ArcGIS Online layer *and* a custom MapLibre app from the same underlying data — same data, two different channels, depending on the audience.

This dual-stack philosophy is why nearly every lab in this course — starting in Week 2 — offers an open-source/cloud-native version and a proprietary version of the same core skill.


## 1.4 The Final Project: Public Web GIS Inquiry

Starting in **Week 11**, you'll design, build, and deploy your own public-facing web map and technical report, grounded in a real place, a real question, and — ideally — a real audience beyond this class. It's introduced this early, in Week 1, for a reason: **every design decision you make this semester will eventually be tested against that audience.**

A few things to start thinking about now (no need to decide anything yet — Topic Ideation checkpoints in Weeks 2, 5, and 9 will walk you through this incrementally):

- What place do you want to work with?
- Who is the audience — and just as importantly, who is *not* the audience, and why?
- What question is this map actually trying to answer for that audience?

You'll choose your deployment platform later — ArcGIS Online, GitHub Pages + MapLibre/Leaflet, or another approved stack — but the audience question comes first, and it comes from you, not the tool.

---


## Readings & Resources

- [What is GIS? (Esri overview)](https://www.esri.com/en-us/what-is-gis/overview)
- Dorman, M. *Introduction to Web Mapping* — Preface (course reading; on the concept of web maps and why JavaScript underlies nearly all of them)
- [MapLibre GL JS documentation: Getting Started](https://maplibre.org/maplibre-gl-js/docs/)
- [Leaflet Quick Start Guide](https://leafletjs.com/examples/quick-start/)

---


## Before Thursday: Prep for Lab 0

Lab 0 is **ungraded**, but it's a hard prerequisite — it exists to make sure everyone has a working environment before graded work starts in Week 2. Please complete the following on your own time before Thursday's lab session:

- [ ] Confirm you can log in to the department's **ArcGIS Online** organizational account (or request access if you don't have it yet)
- [ ] Create a free **GitHub** account, if you don't already have one
- [ ] Install a plain-text code editor — **Visual Studio Code** is recommended
- [ ] Verify you have a modern browser with working Developer Tools (Chrome recommended for this course; Firefox is fine too)
- [ ] Verify Python 3 is available on your machine (we'll use its built-in local web server occasionally, plus `folium` for exercises like the one above)
- [ ] Revisit the **Network tab demo** from tonight on your own — try it on a different web map (e.g., your local GIS department's site, or [earth.nullschool.net](https://earth.nullschool.net)) and notice how the tile requests differ
- [ ] Skim Fu, Ch. 1 and the Leaflet Quick Start guide linked above

This is also your first chance to practice **K16**: if you get stuck on setup, the first move isn't to struggle silently — try the tool's official documentation, then a web search of the exact error message, then the course discussion board. Getting comfortable asking for help in public GIS/dev spaces is a skill this course builds deliberately, starting now.

---


## Lab 0: Environment Setup

See [Lab 0](../labs/lab_00.md) — this lab is **ungraded** (Prior Knowledge Diagnostic PK1). It confirms you have a working dual-stack environment — ArcGIS Online access *and* an open-source dev setup — before the graded work begins in Week 2.

