# ESRI and the ArcGIS Online Environment

**Week 2 | Lecture + Lab 1: Hosted Feature Layer + POI Webmap in ArcGIS Online**

---

## Learning Objectives

By the end of this unit, you will be able to:

- Describe the core components of the ArcGIS Online platform: organizational accounts, licensing roles, Content, and Map Viewer, and how it differs from ArcGIS Pro and ArcGIS Enterprise
- Upload and publish geospatial data as a hosted feature layer in ArcGIS Online
- Configure layer properties, popups, and symbology in Map Viewer
- Build a simple point-of-interest webmap and share it with a defined audience
- Describe the cost, control, and access tradeoffs of hosted proprietary services

---

## ArcGIS Online: A Quick Orientation (and Refresher)

If you've taken an intro GIS course, ArcGIS Online (AGOL) probably isn't brand new to you. You've likely opened a map someone else built, maybe added a layer or two, and clicked around Map Viewer. That's a fine starting point, but it's a *consumer's* view of the platform. This week you're switching roles: from someone who opens maps to someone who publishes them. Before diving into hosted feature layers in 2.3, it's worth making sure everyone has the same mental map of what AGOL actually is.

**Where AGOL sits in the Esri product family.** Esri sells three related but distinct things, and it's easy to conflate them:

- **ArcGIS Pro**: desktop software installed on your own machine, used for heavier-duty analysis, cartography, and data prep. Nothing here is on the web by default.
- **ArcGIS Online (AGOL)**: what we're using this semester: a cloud-based, Esri-hosted SaaS platform. You sign in through a browser, and everything (storage, compute, hosting) runs on Esri's infrastructure. This is today's topic.
- **ArcGIS Enterprise**: the same general software stack as AGOL, but installed on servers *your organization* owns and runs. If AGOL is the hosted, Esri-run option, ArcGIS Enterprise is Esri's self-hosted option: the proprietary-world sibling to the GeoServer instance you'll stand up next week. Filing this away now will make Week 3's self-hosted/hosted comparison land more precisely: the divide isn't "open-source vs. Esri," it's "who runs the server," and Esri has an answer on both sides of that line.

**Organizational accounts and licensing roles.** You're not signing up for a personal AGOL account. You're signing into WNMU's *organization*, a shared instance with its own users, groups, and content, all under one subscription. Within an organization, every user has a **role** that determines what they're allowed to do:

| Role | Can do |
|---|---|
| **Viewer** | Open and interact with maps and apps shared with them; no publishing |
| **Editor** | Edit features in existing layers; still can't publish new ones |
| **Creator** | Publish hosted feature layers, build maps and apps. This is the minimum role for everything in this course |
| **Publisher** | Everything Creator can do, plus registering external data stores |
| **Administrator** | Manages the organization itself: users, roles, security settings |

If you can't publish a hosted feature layer in Lab 1, your account role is the first thing to check.

**The pieces you'll actually touch.** AGOL's interface can feel sprawling at first, but almost everything this semester routes through three places:

- **Content**: your (and your organization's) library of published *items*. An item might be a hosted feature layer, a web map, or a web app, each with its own metadata, sharing settings, and owner. Think of this as the AGOL equivalent of a file browser, except every "file" is also a live web service.
- **Map Viewer**: where you'll spend most of your time: adding layers, symbolizing them, configuring popups, and assembling a finished web map. This is today's main workspace.
- **Groups**: a way of bundling content and people together for sharing purposes (e.g., sharing a layer with just your project team rather than the whole organization or the public). We won't lean on this heavily today, but you'll see it again if you collaborate on the final project.

**If this is all familiar,** great: 2.3 is where things get new, publishing your *own* data as a hosted feature layer, rather than just viewing someone else's. **If this is mostly new,** also fine; everything from here forward assumes only what's in this section, not prior AGOL experience.

---

## Hosted Feature Layers: What They Are

When you publish a **hosted feature layer** in ArcGIS Online, your data doesn't sit on a server you administer. It's uploaded into Esri's cloud infrastructure and stored in a managed, multi-tenant data store that Esri operates on your behalf. Esri, not you, owns the uptime, the backups, the scaling, and the patching.

This is about the friendliest possible on-ramp to the idea of "publishing" a layer: you upload a file, and moments later it's a live, queryable web service. Next week (Week 3), you'll do this same kind of publishing yourself, by hand, on a self-hosted GeoServer instance. You'll own the machine, point it at a shapefile or a PostGIS table, and be responsible for keeping the WMS/WFS endpoints it generates online. Keep today's experience in mind; the contrast will be immediate.

For now, here's what "hosted" actually means in practice:

- **Persistence.** A hosted feature layer keeps running for as long as your organization holds a valid ArcGIS Online subscription and has enough **credits**, Esri's consumption-based currency that hosted storage and services draw against. (A self-hosted server, by contrast, keeps running for as long as *you* keep it running and paying for it. You'll feel that difference directly next week.)
- **Control.** With a hosted feature layer, the canonical copy of your data lives inside Esri's platform; you interact with it through the ArcGIS REST API and Map Viewer, not through direct file access. You never touch the raw files the way you will once you're managing your own GeoServer instance.
- **What happens if the license lapses.** If an ArcGIS Online organizational subscription expires or an account is deactivated, hosted content doesn't necessarily disappear immediately, but it does become inaccessible to the public and unusable for editing or new publishing, effectively frozen behind a paywall you no longer have access to. Next week, you'll see the opposite case: if you stop paying for a self-hosted *server*, the underlying shapefile or database is still just a file on disk, sitting wherever it always was, fully usable with any other GIS software.

None of this makes hosted feature layers a worse choice. For a class project, a small organization without IT staff, or a map that needs to go from zero to shared-with-the-world in ten minutes, the fact that Esri runs the server for you is the entire point. But it's worth naming clearly, because it's a tradeoff you'll meet again directly in Week 3, and once more with cloud-native formats in Week 6: convenience and managed infrastructure in exchange for giving up direct control of where the data actually lives.

| | **ArcGIS Online (hosted feature layer)** | **GeoServer (self-hosted, preview of Week 3)** |
|---|---|---|
| Who runs the server | Esri | You (or your organization's IT) |
| Where data physically lives | Esri's managed cloud data store | Wherever you put the shapefile/PostGIS database |
| Cost model | Subscription + consumable **credits** | Free software; you pay for your own hardware/hosting |
| If you stop paying | Content becomes inaccessible; you don't hold a portable local copy | Server goes down, but the original files are untouched and portable |
| Setup effort | Upload a file; AGOL infers schema and publishes automatically | You configure Store → Layer → Style by hand |
| Control over infrastructure | None: you interact only through Esri's interfaces and REST API | Full: you can migrate, back up, or inspect data directly |

The two aren't really competing on *capability* so much as on *who takes on the responsibility of running the server*, a tension you'll meet head-on next week, and again with cloud-native formats in Week 6.

## Uploading and Publishing Data

The publish-from-upload workflow in ArcGIS Online is about as compressed as publishing gets: upload a file, and AGOL handles schema detection, storage, and endpoint creation automatically.

**Formats AGOL accepts for upload:**

- **CSV**: a spreadsheet with either latitude/longitude columns, or street addresses AGOL can geocode into points during upload
- **Shapefile**: must be zipped (the .shp, .shx, .dbf, and .prj files together) before upload; AGOL will not accept the individual files loose
- **GeoJSON**: uploaded directly, geometry and attributes intact
- **File Geodatabase** (zipped) and **GPX** are also accepted, though CSV, shapefile, and GeoJSON cover most classroom use cases

**The workflow, step by step:**

1. In Map Viewer (or Content → New Item), choose **Add → Add Layer from File** and select your zipped shapefile, CSV, or GeoJSON.
2. AGOL inspects the file, infers the geometry type and field schema, and, for CSVs with address fields rather than coordinates, runs a geocoding pass to convert addresses into points, consuming credits in the process.
3. You're prompted to confirm the layer name and whether to publish it as a **hosted feature layer** (queryable, editable, symbolizable) versus adding it as a temporary file layer that only exists in this one map session.
4. Once published, AGOL generates an **Item** in your Content: a single published, requestable resource with its own metadata, sharing settings, and REST endpoint, generated automatically instead of configured by hand. Next week you'll meet GeoServer's parallel concept, a *Layer*, which you'll set up yourself.

**Where the data actually goes:** once published, your original CSV or shapefile is no longer the live copy. AGOL has ingested it into its own hosted feature service, backed by Esri's cloud data store, conceptually similar to how a database will act as a *Store* once you set up GeoServer next week, except here you never see or manage that underlying database directly. Every edit made afterward (in Map Viewer, in the Esri field apps, or via the REST API) writes to that hosted copy, not back to your original file.

## Configuring Popups, Symbology, and Sharing

Publishing a layer gets your data onto the map; it doesn't make that map *readable*. Two people can publish the same point-of-interest layer and end up with completely different maps depending on how they handle symbology and popups. Next week you'll do this same conceptual job by hand, writing an SLD file for a WMS layer in GeoServer; here it's all driven through a UI instead.

**Symbology in Map Viewer.** Click a layer's *Styles* pane and you'll typically choose from:

- **Location (single symbol)**: every feature drawn identically; good for a simple POI layer where the points themselves are the message
- **Unique values**: one color/icon per distinct category in a field (e.g., a different marker for "restaurant" vs. "park" vs. "trailhead")
- **Counts and amounts (graduated colors/symbols)**: symbol size or color intensity scales with a numeric field, the standard choropleth/proportional-symbol move

This is a friendlier on-ramp than writing SLD by hand, but it's doing the same conceptual job: deciding, at the server/platform level, how a feature should look before anyone requests it.

**Popups.** By default AGOL shows every attribute field in a raw popup table, rarely what you want in a finished map. Under a layer's *Popup* settings you can pick which fields display, give them human-readable aliases (`poi_type` → "Type"), reorder them, and format numbers, dates, or URLs. This is the layer of polish that turns "here's the data" into "here's a map someone unfamiliar with your fields can actually use."

**Basemap.** Map Viewer ships with a gallery of Esri basemaps (topographic, streets, imagery, a muted "light gray canvas" good for thematic overlays). Choosing one is mostly an aesthetic/contextual decision, but it's worth remembering that most of these are themselves services published by Esri: you're consuming a hosted layer the same way an external client would consume yours.

**Sharing.** Once the map looks right, the *Share* button offers three levels, and this is where the decision stops being cosmetic and starts being consequential:

- **Private (owner only)**: visible only to you, useful while a map is still a draft
- **Organization**: visible to everyone signed into your ArcGIS Online organization, but not the open internet
- **Public (everyone)**: visible to anyone with the link, discoverable in AGOL's public search, and embeddable elsewhere

The sharing level isn't a neutral technical setting bolted on at the end. It's a design decision made at publish time, the same way choosing between WMS and WFS will determine who can do what with a layer once you get to GeoServer next week. Choosing "Public" for a map with sensitive locations (a domestic violence shelter, a culturally significant site, an at-risk species' habitat) is a different act than choosing it for a public bus-stop inventory, even though the button looks identical.

## Who Can See This Map?

Setting a map's sharing level to "Public" feels like flipping a switch, but it's worth being precise about what actually happens: the data doesn't become public in some abstract, ownerless sense. It becomes publicly accessible *on Esri's servers, under Esri's terms of service, subject to Esri's uptime*. "Public" here means "public through a specific company's infrastructure," not "public" the way an open standard published from a self-hosted server, like the GeoServer instance you'll stand up next week, is public. The map is still, at every layer, running on someone else's platform, discoverable through someone else's search index, and governed by a Terms of Service document your organization didn't write.

That distinction matters most for exactly the kind of data where the stakes are highest:

- **Community and tribal data.** Indigenous communities and other groups practicing data sovereignty may have strong reasons *not* to want culturally sensitive locations sitting on a commercial vendor's cloud, regardless of the sharing setting. The concern isn't only "can strangers see this," it's "whose infrastructure holds this, and under what terms can that access be revoked or that data be used."
- **Sensitive locations.** Habitat data for an endangered species, the address of a shelter, or precise home locations in a community survey can all be technically "correct" to map and still cause real harm if shared publicly. Poaching, harassment, or exposure are real downstream risks, not hypothetical ones.
- **Persistence and revocability.** Because the data lives on Esri's platform (Section 2.3), the people who created a public map don't have unilateral control over its long-term availability. An account suspension, a lapsed license, or a platform policy change can affect access in ways a self-hosted server sitting on infrastructure you control would not. You'll be the one controlling that infrastructure yourself starting next week.

None of this means "never share publicly." Plenty of data (bus routes, public parks, zoning boundaries) is precisely the kind of information that *should* be as open and discoverable as possible. The point is that the sharing decision deserves the same scrutiny as any other design decision in this course: who is this data about, who benefits from it being visible, and who might be put at risk by that same visibility?

**Hold onto this question.** It's the seed for Discussion 1 in Week 4, where we'll dig into open data, community consent, and what "open" actually obligates a publisher to consider.

### 5-minute discussion (breakout or whole-class)

1. Who benefits most from an organization choosing a hosted, proprietary platform like AGOL to publish geospatial data, and who might be excluded or put at risk by that choice? Hold onto your answer: next week, once you've stood up your own GeoServer instance, we'll ask this same question about a self-hosted, open-standard alternative, and compare notes.
2. If your organization's ArcGIS Online subscription lapsed tomorrow, what would happen to a public map you'd shared widely? Compare that to what you'd guess would happen to a WMS layer served from a self-hosted server you personally administered. Which failure mode worries you more, and why?

---

## Readings & Resources

- [Esri Learn: Publish a hosted feature layer](https://learn.arcgis.com/en/projects/get-started-with-arcgis-online/)
- [ArcGIS Online: Share your map (Esri documentation)](https://doc.arcgis.com/en/arcgis-online/share-maps/share-map.htm)
- [NM RGIS data clearinghouse](https://rgis.unm.edu): find a NM dataset to use in Lab 1

---

## Preparing for Lab 1: Publishing a Hosted Feature Layer and POI Webmap

For Thursday's course, your job is to find a public GIS layer of interest that you'd want to share on a web map. You can bring any dataset that you'd like, as long as it you're OK sharing it publically on the WNMU ArcOnline platform. 

Here's a few options below: 

### NM Public Data Option
1. Go to https://rgis.unm.edu/ and search for a small **point** dataset covering Grant County or the Gila region — trailheads, campgrounds, historic markers, or fire lookout towers are all good, manageable choices.
2. Download it as a **shapefile** (zip the `.shp` + sidecar files together) *or*, 
3. Save the file somewhere you can find it and be able to upload during our first Lab 1.

### Personal Data Option
1. If you'd rather build your own dataset, make a simple CSV with `name`, `latitude`, `longitude`, and one or two descriptive columns (`type`, `notes`). This could be a handful of real places you know in the region works fine. Consider how you might want to symbolize this data when uploaded and develop accordingly. 

See [Lab 1](../labs/lab_01.md).
