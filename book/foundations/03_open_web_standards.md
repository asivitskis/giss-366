# Open Web Standards

**Week 3 | Lecture + Lab 2: Publishing & Consuming a WMS/WFS**

---

## Learning Objectives

By the end of this unit, you will be able to:

- Explain what OGC web service standards are and why they matter for interoperability
- Distinguish between WMS (Web Map Service), WFS (Web Feature Service), and WMTS
- Describe how GeoServer implements OGC standards as a self-hosted service
- Consume a WMS/WFS endpoint in a web client

---

## Why Standards Matter

Imagine a state agency publishes a hazard layer in a proprietary Esri file geodatabase, a county publishes parcels as a shapefile, and a university lab publishes vegetation data out of PostGIS. Three different formats, three different pieces of software required just to open them. Before any of those data can be layered together in one map, someone has to convert, re-project, and re-export it — by hand, every time the source data changes. That's the interoperability problem: when every vendor and every dataset speaks its own dialect, data doesn't move, it gets stuck in silos.

The **Open Geospatial Consortium (OGC)** exists to solve exactly this. OGC is an international standards body (not a company, not a piece of software) that publishes open, vendor-neutral specifications for how geospatial data should be requested and delivered over the web. "Open" here has a specific, practical meaning: the specification is public, free to implement, and not owned or controlled by any single company. Esri didn't invent WMS, and neither did the maintainers of GeoServer or MapServer — all of them independently *implement* the same OGC specification, which is precisely why a WMS layer published from GeoServer can be added to an ArcGIS Online map without anyone writing a converter.

This is the same client-server loop from Week 1 — a request goes out, a response comes back — but now with an agreed-upon *grammar* for what that request and response look like, regardless of which software is on either end. That agreement is what turns a pile of incompatible files into an interoperable ecosystem, and it's the reason a self-hosted open-source stack and a proprietary one can sit side-by-side in this course and still talk to each other.

## WMS, WFS, and WMTS

OGC has published dozens of specifications over the years, but three form the backbone of "classic" OGC web services, and each answers a different question about what the client actually wants back:

| | **WMS** (Web Map Service) | **WFS** (Web Feature Service) | **WMTS** (Web Map Tile Service) |
|---|---|---|---|
| What you get back | A rendered **image** (PNG/JPEG) of the map, drawn to your exact bounding box and size | Raw **vector features** — geometry + attributes, as GML or GeoJSON | Pre-rendered **image tiles**, cut to a fixed grid and cached ahead of time |
| Can you query attributes? | No — it's a picture, not data | Yes — this *is* the data | No — same limitation as WMS |
| Who styles it? | The server (styling is baked into the image) | The client (you get raw data, you decide how to draw it) | The server, at tile-creation time |
| Performance | Rendered on-the-fly, every request — slower under heavy load | Depends on dataset size and query complexity | Fast — the hard work already happened before anyone asked |
| Best use case | Quick reference/basemap layers where you don't need to inspect features | Anywhere you need to click a feature, filter by attribute, or run analysis client-side | High-traffic basemaps that don't change often |

Notice the pattern from Week 1 repeating here: WMS is conceptually a **raster tile**, generated to order instead of pre-cut; WFS is the **data itself**, the way a vector tile hands raw geometry to the browser to style; and WMTS is a **raster tile**, pre-baked and cached the way Google Maps basemaps always have been. The vocabulary is new, but the underlying tradeoff — pre-rendered and fast vs. raw and flexible — is one you already have a mental model for.

**When would you reach for each?** If you just need a visual reference layer sitting under your own data and nobody will ever click on it, WMS is simplest. If your users need to query, filter, symbolize, or edit the features themselves, you need WFS. If you're serving a basemap to thousands of concurrent users and the underlying data barely changes, WMTS's pre-caching earns its keep.

<svg viewBox="0 0 760 300" xmlns="http://www.w3.org/2000/svg" style="max-width:100%; height:auto; font-family:-apple-system, Helvetica, Arial, sans-serif;">
  <defs>
    <marker id="arrow2" markerWidth="10" markerHeight="10" refX="8" refY="3" orient="auto" markerUnits="strokeWidth">
      <path d="M0,0 L0,6 L9,3 z" fill="#444"/>
    </marker>
  </defs>

  <text x="380" y="22" text-anchor="middle" font-size="14" font-weight="bold" fill="#333">
    Same client, same server — three different requests
  </text>

  <rect x="20" y="50" width="150" height="220" rx="10" fill="#eaf2fb" stroke="#3b6ea5" stroke-width="2"/>
  <text x="95" y="165" text-anchor="middle" font-size="15" font-weight="bold" fill="#1f3d5c">Client</text>
  <text x="95" y="185" text-anchor="middle" font-size="11" fill="#1f3d5c">(browser or GIS app)</text>

  <rect x="590" y="50" width="150" height="220" rx="10" fill="#fdeee0" stroke="#c17a2e" stroke-width="2"/>
  <text x="665" y="165" text-anchor="middle" font-size="15" font-weight="bold" fill="#6b3d0f">GeoServer</text>
  <text x="665" y="185" text-anchor="middle" font-size="11" fill="#6b3d0f">(OGC endpoint)</text>

  <line x1="172" y1="80" x2="588" y2="80" stroke="#444" stroke-width="1.5" marker-end="url(#arrow2)"/>
  <text x="380" y="72" text-anchor="middle" font-size="11" fill="#333">GetMap (WMS) → request a picture</text>
  <text x="380" y="97" text-anchor="middle" font-size="11" fill="#0a7a3d">← PNG image, already drawn</text>

  <line x1="172" y1="150" x2="588" y2="150" stroke="#444" stroke-width="1.5" marker-end="url(#arrow2)"/>
  <text x="380" y="142" text-anchor="middle" font-size="11" fill="#333">GetFeature (WFS) → request the data</text>
  <text x="380" y="167" text-anchor="middle" font-size="11" fill="#0a7a3d">← GeoJSON/GML features + attributes</text>

  <line x1="172" y1="220" x2="588" y2="220" stroke="#444" stroke-width="1.5" marker-end="url(#arrow2)"/>
  <text x="380" y="212" text-anchor="middle" font-size="11" fill="#333">GetTile (WMTS) → request a cached tile</text>
  <text x="380" y="237" text-anchor="middle" font-size="11" fill="#0a7a3d">← pre-baked PNG tile, instantly</text>

  <text x="380" y="288" text-anchor="middle" font-size="11" fill="#666">
    Same data, same server — the request you send determines whether you get a picture, raw data, or a cached tile.
  </text>
</svg>

**Live demo idea (in class):** GeoServer's own public demo instance exposes a `GetCapabilities` document — the "menu" every OGC service publishes listing every layer it offers and what operations are supported. Pull one up and look at the raw XML together; then swap `GetCapabilities` for `GetMap` in the same URL and watch the response change from a menu to an actual image. This is EQ1 again, one layer down the stack.

## GeoServer as an OGC Implementation

**GeoServer** is a free, open-source Java application that sits between your spatial data and the web, and speaks OGC out of the box. It doesn't store data itself — instead, it connects *out* to a data source (a shapefile, a folder of GeoTIFFs, a PostGIS database) and exposes that source as WMS, WFS, and WMTS endpoints without you writing any server code. Conceptually, it plays the same role in the open-source stack that ArcGIS Server / ArcGIS Enterprise plays on the proprietary side: both take data that started out sitting in a file or database and turn it into something a browser can request over HTTP.

The publishing workflow in GeoServer follows a consistent chain, and it's worth knowing the vocabulary before Lab 1:

- **Store** — the connection to your actual data (a shapefile, a PostGIS table, a raster folder)
- **Layer** — a store, published and made queryable/requestable as an OGC resource
- **Style** — an SLD (Styled Layer Descriptor) file that tells GeoServer how to symbolize a layer when it's requested via WMS
- **Workspace** — a namespace that groups related layers together, similar to a folder

Once a layer is published, GeoServer automatically generates that `GetCapabilities` document from the demo above, and any WMS/WFS-aware client — GeoServer's own preview, QGIS, a Leaflet map, or ArcGIS Online — can discover and consume it without any custom integration work. That's the interoperability payoff from 2.1, made concrete.

> **Looking ahead:** WMS/WFS/WMTS are the standards that made "any client can talk to any server" possible in the first place, and GeoServer remains a dominant way to stand up self-hosted, enterprise-grade OGC services today — for live databases, frequently-updated data, or organizations that need a queryable service rather than a static file. But the OGC family itself has kept evolving: the newer **OGC API** suite (OGC API - Features, OGC API - Tiles) re-expresses these same ideas as simpler REST/JSON APIs, and — running in parallel to services entirely — **cloud-native formats** like vector tiles, PMTiles, and COGs let a browser fetch exactly the bytes it needs straight from static storage, with no server running at all. We'll dig into that shift in Week 6. Think of this week as the *why standards let systems interoperate* lesson; Week 6 is the *here's where serving static, cloud-native data is heading* lesson. Understanding WMS/WFS first gives you the vocabulary to see clearly what the cloud-native approach is actually trading away (a live, queryable server) for what it gains (near-zero hosting cost, no server to maintain).

## Consuming Services in ArcGIS Online

You already spent last week inside ArcGIS Online's Map Viewer publishing your own hosted feature layer — interoperability isn't just a GeoServer feature, though; it flows both directions. ArcGIS Online can consume external WMS and WFS endpoints directly: from a map in AGOL, **Add &#8594; Layer from Web**, choose the service type, and paste in the GeoServer endpoint URL. AGOL parses the same `GetCapabilities` document any other client would, lists the available layers, and lets you add one to your map — no export, no conversion, no Esri-specific format required on the GeoServer side.

This is the dual-stack philosophy from Week 1 made concrete: a layer published once from a self-hosted, open-source server can be consumed by a proprietary platform, and vice versa (AGOL can also publish its own WMS/WFS endpoints from a hosted feature layer, consumable by GeoServer, QGIS, or a hand-built Leaflet map). Neither stack has to "win" — the standard is the thing that lets them coexist on the same project.

### 5-minute discussion (breakout or whole-class)

1. Who benefits most from an agency choosing to publish through an open standard like WMS/WFS instead of a proprietary-only format — and who might still be excluded even when the data is technically open?
2. If GeoServer requires a server that's always running, and Week 6's cloud-native formats need only static storage, what's the tradeoff an organization is actually making when it picks one over the other? (Revisit this one after Week 6 — see if your answer changes.)

---

## Readings & Resources

- [OGC Standards overview (ogc.org)](https://www.ogc.org/standards/)
- [GeoServer documentation: Getting Started](https://docs.geoserver.org/latest/en/user/gettingstarted/index.html)
- [WMS reference (OGC)](https://www.ogc.org/standard/wms/)
- [WFS reference (OGC)](https://www.ogc.org/standard/wfs/)
- [GeoServer public demo (GetCapabilities in the wild)](https://data.geoserver.org/geoserver/web/)

---

## Lab 2: Publishing & Consuming a WMS/WFS

See [Lab 2](../labs/lab_02.md).
