# Lab 4

# JavaScript & Leaflet in Practice: Building and Publishing Your Own Web Map
### GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 5 Focus:** JavaScript and Leaflet, from a blank file. Unlike Lab 3, there is no provided `map.js` today; you write it. This is your fourth graded lab, and the first time you've authored the map's actual behavior yourself.

## Before You Begin

The tools are the same as Lab 3. Confirm you still have them before proceeding.

| Tool | Role today |
|---|---|
| **A GitHub account** | Already confirmed in Lab 3 |
| **VS Code, with the Git/GitHub extension enabled** | Cloning the Lab 4 template and editing files locally |
| **Git** | Same install as Lab 3 |
| **VS Code + Live Server** | Previewing your map locally as you write JavaScript, before pushing |
| **The Lab 4 starter repository** | Provided by your instructor; a fresh template, similar in structure to the Lab 3 template but with an empty `map.js` |
| **A region and a few locations of your own** | You'll need real (or realistic) coordinates to place on your map (see Step 1) |
| **[MDN Web Docs](https://developer.mozilla.org/)** and **[Leaflet documentation](https://leafletjs.com/reference.html)** | Your first stops when something doesn't work |

You already know how to clone a repo, edit locally, preview with Live Server, and publish via GitHub Pages from Lab 3. That workflow doesn't change today. What's new is the file you're actually writing code into.

## Step 1: Ask

Open this reference map for a minute: https://leafletjs.com/examples/quick-start/


Before opening the starter repository, jot your first-pass answers:
- Pick a place that matters to your project topic, your hometown, a field site, a neighborhood you've mapped before in this course. What is its approximate latitude and longitude? (A quick search of "[place name] coordinates" will get you close enough.)
- List two or three additional nearby points, paths, or areas you might want to mark on your map, or build a polygon from. This is your rough content plan for Step 4.
- In Lab 3, you never opened `map.js`. Looking at the quick-start map above, take a guess: how many lines of JavaScript do you think it takes to get the basemap loaded and one marker on screen?

> **Lab 4 Question: In Lab 3, changing `#title`'s CSS rule changed the page without ever touching `map.js`. Today, `map.js` is the file that puts anything geographic on the page at all. Why do you think that distinction exists between the three languages?**

## Step 2: Collect

### Part A: Confirm access

Same as Lab 3: confirm you can log in to GitHub, and that you have the link to the Lab 4 starter repository (shared via Canvas prior to lab).

- https://github.com/asivitskis/giss366-lab04-template

### Part B: Clone the repo

This is the same process as Lab 3; if it's still fresh, skim and move on.

**Step 0: Create your own copy of the template repository.**

1. In a browser, go to the template repository URL above.
2. Make sure you're signed into your GitHub account.
3. Click **Use this template** → **Create a new repository**.
4. Name it something like `giss-366-lab4`, under **your own** account.
5. Click **Create repository**.
6. Copy the URL of **this new repository** (not the original template).

**Clone your own copy into VS Code:**

1. Open VS Code, no files open (File → New Window if needed).
2. Open the **Source Control** panel and click **Clone Repository**.
3. Paste your new repository's URL, choose a save location near your other `giss-366` folders.
4. Choose **Open** when prompted.

### Part C: Explore what you cloned

| File / folder | What it is | Will you edit it today? |
|---|---|---|
| `index.html` | The full map page skeleton: `<head>`, Leaflet CSS/JS links, an empty `<div id="map">`, and a link to `map.js` | Minimal; mostly title text and `<style>` tweaks, similar to Lab 03 |
| `map.js` | **Empty.** This is where all of today's work happens | **Yes** |
| `README.md` | Standard repository landing page | Optional |
| `css/leaflet.css` | Leaflet's own stylesheet | No |
| `js/leaflet.js` | The Leaflet library itself | No |
| `images/` | A folder for any marker icons or logo images you want to add | Optional |

## Step 3: Visualize

### 3.1 The minimum a Leaflet map needs

*(Dorman, M. Introduction to Web Mapping, Sections 6.5.2–6.5.11)*

Open `index.html` in your cloned repository. Review the following features:
 - It already includes the Leaflet library and an empty map <div>. 
 - It also includes the same <stlye> elements within the <head> section and the and <div id="description"> section within the <body> that you learned to customize in Lab 03. We'll give you the opportunity to replicate your customization or expand it further for this lab.

If you were to mentally remove those features, the skeleton below is the minimum viable code needed to build a basic Leaflet map. notice the different components and where they sit. 

```html
<!DOCTYPE html>
<html>
<head>
    <title>[update this] My Map</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <link rel="stylesheet" href="css/leaflet.css">
    <script src="js/leaflet.js"></script>
    <style>
        body { padding: 0; margin: 0; }
        html, body, #map { height: 100%; width: 100%; }
    </style>
</head>
<body>
    <div id="map"></div>
    <script src="map.js"></script>
</body>
</html>
```

Return to your new `index.html` file. Try to open this index file version with LiveServer.

You'll see the title bar and description panel from the template, but the space between them will be blank. That's because `<div id="map">` is empty, and `map.js`, the file that's supposed to fill it, doesn't exist yet. That's your job in Step 4. You'll come back to the `#title` and `#description` content and styling in Part 3, once there's actually a map underneath them to describe.

### 3.2 Review JS Basics - Reading the shape of a Leaflet call

Every Leaflet layer you'll write today follows the same two-part shape: **create the layer, then add it to the map.**

```js
L.tileLayer(url, options).addTo(map);
L.marker([lat, lng]).addTo(map);
L.polyline([[lat, lng], [lat, lng]], options).addTo(map);
```

The `map` variable itself has to exist before any of these calls run, which means the very first line of your `map.js` is always going to be an `L.map(...)` call. Everything else in the file depends on that variable.

### 3.3 The full target file

Here's the complete pattern your `map.js` will follow by the end of Step 4 (values are placeholders, yours will use your own coordinates and styling):

```js
// 1. Create the map, centered on your chosen location and zoom level.
let map = L.map("map", {center: [YOUR_LAT, YOUR_LNG], zoom: XX});

// 2. Add a basemap tile layer
L.tileLayer(
    "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
    {attribution: '&copy; OpenStreetMap contributors'}
).addTo(map);

// 3. Add at least one marker, line, or polygon
let pnt = L.marker([lat coord, lng coord]).addTo(map);
pnt.bindPopup("Something worth saying about this location.");

```
Beyond this basic basic JS code, you can add additional customization options (different basemaps, feature controls, scale bars, etc.).
Skim this before writing your own; you'll fill in each numbered section separately in Step 4.

## Step 4: Create

<details>
<summary><b>Part 1: Center your map and add a tile layer. Click to expand</b></summary>

Open `map.js` (currently empty) in VS Code.

1. Look up the approximate latitude and longitude for the place you want to use as your map center.
2. Write the first line of `map.js`, replacing `YOUR_LAT, YOUR_LNG` with your coordinates to center map object there:
   ```js
   let map = L.map("map", {center: [YOUR_LAT, YOUR_LNG], zoom: XX});
   ```
3. For the Zoom level `XX`, choose a number between 20 (all the way zoomed out) and 1 (all the way zoomed in). 
4. Below it, add an `L.tileLayer` call to bring in an OpenStreetMap basemap (Lecture Part 7). Don't forget `.addTo(map)` at the end.
5. Save, then open `index.html` with Live Server. You should see a basemap, centered and zoomed where you specified, with nothing else on it yet.

**Try it:** Change only the `zoom` number and refresh. Then change only the `center` array. Confirm you understand which number controls which behavior before moving on.

</details>

<details>
<summary><b>Part 2: Add your own self-built vector layers. Click to expand</b></summary>

Using the coordinates you listed in Step 1, add **at least two** of the following (mix and match; you don't need all three):

- **A marker** at one point of interest, with `.bindPopup()` containing at least one sentence and one HTML tag (`<b>`, `<a>`, etc.)
- **A line** connecting two or more of your points, with a custom `color` and `weight`
- **A polygon** outlining an area relevant to your topic, with a custom `color` and `fillColor`

For each one, follow the create-then-`.addTo(map)` pattern from Step 3.2. Check the map in Live Server after each addition, rather than writing all three and debugging at once.

An additional appendix has been linked at the bottom of this lab for a quick review of adding polygons.

**Common early bugs to check for, if nothing appears:**
- Did you swap latitude and longitude? Leaflet coordinate arrays are `[lat, lng]`.
- Is every statement inside `map.js` ending in a semicolon?
- Does every opening `{` or `(` have a matching closing `}` or `)`? (Check your browser's console: `F12` → Console tab, for a red error message pointing at the exact line.)

</details>

<details>
<summary><b>Part 3: Update your title bar and description panel. Click to expand</b></summary>

This is the same `#title` and `#description` HTML/CSS from Lab 3, just pointed at your new map. In `index.html`:

1. Update the `<h1 id="title">` text to name today's map.
2. Rewrite the contents of `<div id="description">`:
   - Replace the "About this map" text with one or two sentences about what your map shows and why.
   - Add a short list (`<ul>`/`<li>`) of the layers you added in Part 2.
   - Update "Map authored by:" with your name, and remove any other leftover placeholder text.
3. Optional: adjust the CSS in the `<style>` block (`background-color`, `padding`, `border-radius`, `font-family`, positioning, and so on) to give this map its own visual identity, the same way you customized these properties in Lab 3.

**Common bug:** if your description text mentions a layer you didn't actually add in Part 2 (or vice versa), the grader will notice, keep the panel's content in sync with your actual `map.js`.

</details>

<details>
<summary><b>Part 4: Title and polish. Click to expand</b></summary>

Back in `index.html`:
- Update `<title>` to name your map.
- Confirm the `<meta name="viewport">` line is present (it should already be in the template); this keeps your map usable on mobile.

Preview the whole thing once more in Live Server before moving to Step 5. This is the version that gets pushed and graded.

</details>

<details>
<summary><b>Part 5: Customize and add additional data. Click to expand</b></summary>

Pick **at least one** of the following options. Each is a small, self-contained addition, pick whichever sounds most useful for your topic, or work through more than one if you have time. In your Step 5 reflection, you'll name the option you chose and justify why.

**Option A: Swap in a custom basemap**

1. Browse **Appendix B** (or the full gallery at the [leaflet-providers preview](https://leaflet-extras.github.io/leaflet-providers/preview/index.html) if you don't mind registering for a key) and pick one you like (e.g., a terrain, satellite, or grayscale basemap).
2. In `index.html`, add the plugin script tag *above* your `map.js` script tag:
   ```html
   <script src="https://unpkg.com/leaflet-providers/leaflet-providers.js"></script>
   ```
3. In `map.js`, replace your existing `L.tileLayer(...)` call with the provider shorthand, using the exact name shown in Appendix B or on the preview page:
   ```js
   L.tileLayer.provider("OpenTopoMap").addTo(map);
   ```

**Common bugs:** 
 - if you keep your old `L.tileLayer(...)` call *and* add the new one, both will load, one stacked on top of the other, and you'll only see whichever loaded last. Delete or comment out the original before adding the replacement.
 - **401 error after publishin):** if your basemap works in Live Server but shows a `401` error in the browser console (`F12` → Console) once published to GitHub Pages, that provider requires an API key and domain whitelisting you haven't set up. Switch to a provider from Appendix B instead of troubleshooting keys.
  
**More explanation:** Many basemaps on the leaflet-providers preview site (Stadia/Stamen, Mapbox, Thunderforest, CartoDB, HERE, Jawg, MapTiler, TomTom) require you to sign up for a free account and paste an API key or access token into your code. Without one, the tiles will fail to load, often with a `401` error in the browser console, and this will usually happen only *after* you publish to GitHub Pages, since some providers (like Stadia) quietly allow unauthenticated requests from `localhost` during local testing. To avoid signup and key management entirely, choose one of the basemaps listed in **Appendix B** at the end of this lab; every layer name in that list works on GitHub Pages with zero setup.

**Option B: Add a WMS layer from your GeoServer lab**

1. In `map.js`, add:
   ```js
   L.tileLayer.wms("https://YOUR-GEOSERVER-URL/geoserver/wms", {
     layers: "workspace:layer_name",
     format: "image/png",
     transparent: true
   }).addTo(map);
   ```
2. Get your URL and `workspace:layer_name` from the GeoServer Layer Preview tab (the same place you previewed this layer during the GeoServer lab).
3. **Common bug:** a blank or gray tile usually means the layer's bounding box doesn't overlap your map's `center`. Re-check the layer's extent in GeoServer's Layer Preview before assuming your code is wrong.

**Option C: Add a layer control to toggle layers on and off**

If you added a second basemap (Option A) or want viewers to be able to hide/show your Part 2 layers, give each layer a name and pass them to `L.control.layers()`:

```js
let baseMaps = { "Streets": osmLayer, "Terrain": terrainLayer };
let overlayMaps = { "My Points": markerLayer };
L.control.layers(baseMaps, overlayMaps).addTo(map);
```

**Common bug:** this only works if each layer was saved to its own variable (`let osmLayer = L.tileLayer(...)`, without `.addTo(map)` chained on) rather than added directly. Go back and split any chained calls into two lines: create the variable, then add the ones you want visible by default with `.addTo(map)` on the next line.

**Option D: Add a scale bar**

The easiest addition on this list, one line:

```js
L.control.scale().addTo(map);
```

By default this shows both metric and imperial. To show only one, pass `{metric: true, imperial: false}` as an argument.


</details>

<details>
<summary><b>Part 6: Publish to GitHub Pages. Click to expand</b></summary>

Same three-step process as Lab 3:

1. **Stage and commit**, using VS Code's Source Control panel. Stage `index.html` and `map.js`, write a commit message (e.g., `Build custom Leaflet map for Lab 4`), and commit.
2. **Push**, using Sync Changes / Push.
3. **Enable GitHub Pages**, in your repository's Settings → Pages (source: `main` branch, `/ (root)` folder), if it isn't already enabled from the template.
4. **Find your published URL** on the same Settings → Pages screen once the build finishes.

Open the published URL and confirm your map, markers, popups, and description panel all appear exactly as they did in Live Server.

</details>

## Step 5: Act

### What to turn in (graded: Lab Exercises)

1. Your completed `map.js`, pushed to your repository (Create, Parts 1–3).
2. Your updated `index.html`, pushed to your repository (Create, Parts 3–4).
3. The link to your published GitHub Pages URL (Create, Part 6).
4. The link to your GitHub repository (Collect, Part B).
5. A short written reflection (~300 words) answering:
   - Revisit your Step 1 guess about line count. How close were you, and what surprised you about how little (or how much) code it took?
   - Name one specific bug you hit while writing `map.js` today, and how you found and fixed it (console error, mismatched bracket, swapped coordinates, etc.).
   - Name which Part 4.5 customization option you added (A, B, C, or D), and explain why you chose it over the others.
   - Compare today to Lab 3: which felt more like "real programming" to you, and why?

Submit your GitHub links and written reflection to this week's submission space in Canvas.

---

## Lab 4 Rubric (40 pts)

| Score Band | What It Looks Like |
|:---|:---|
| **Exceptional (7-8)** | Exceeding expectations; indicates mastery; in-depth understanding; higher-order thinking; inferences and extensions of learning objectives that go beyond what was taught; truly superb effort. |
| **Proficient (5-6)** | Meeting expectations; application of concepts; independently demonstrates understanding and thorough competency of learning objectives explicitly taught. |
| **Developing (3-4)** | Approaching expectations; demonstration of basic understanding without application and understanding of more complex ideas and processes; meets minimum requirements for satisfactory learning. |
| **Insufficient (1-2)** | Below expectations; partial or no demonstration of understanding and progress toward learning objectives; major errors and omissions present; inadequate for competency. |

<br>

| Criteria | Comments | Grade |
|:---|:---|:---:|
| **Content:** `map.js` correctly initializes a map, adds a tile layer, and adds at least two vector layers (marker/line/polygon) with a working popup, and includes one working part 5 customization option. | | / 8 |
| **Content:** The `#title` and `#description` elements in `index.html` have been updated with content specific to this map (not leftover Lab 3 or template placeholder text), and reflect the layers actually present in `map.js`. | | / 8 |
| **Process:** The published GitHub Pages URL loads correctly and matches the local Live Server preview; the repository history shows a real commit/push, not just a single upload. | | / 8 |
| **Process:** The written reflection is specific and evidence-based: it names an actual bug encountered and how it was resolved, and names the Part 5 customization chosen with a genuine reason for choosing it. | | / 8 |
| **Product:** `map.js`, `index.html`, the GitHub Pages link, the repository link, and the reflection are all submitted, clearly labeled, and free of errors. | | / 8 |
| **Total** | | **/ 40** |

---

### Lab 4 AI Policy: Level 2

This lab permits optional use of AI to support student learning. Permitted uses can include using AI to help troubleshoot GIS workflows and debug JavaScript errors (for example, pasting a console error message to ask what it means), however, independent installation and use of AI agents to complete GIS tasks is not permitted at this time. AI may also be used to support general writing procedures (outlining a project, improving clarity or grammar, etc.), but all code and written responses must remain original to each student as a sole responsible author. Disclosure of AI use is not required for this Level 2 assignment. Using AI is a choice with ethical considerations, and it is asked that students weigh those considerations accordingly as they complete this work.

---
### Resources

- [MDN Web Docs](https://developer.mozilla.org/)
- [Leaflet documentation](https://leafletjs.com/reference.html)
- [Leaflet Provider Demo](https://leaflet-extras.github.io/leaflet-providers/preview/) (alternative basemaps, if you'd like to swap the default OpenStreetMap tiles)
- [GitHub Docs: Configuring a publishing source for your GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)

## Appendix: Adding Your Own Vector Layers

*(IWM Section 6.6–6.8)*

Once the tile layer is in place, add foreground features on top of it. All three take a coordinate array (or array of arrays) plus an optional styling object:

**Markers**: a single point, default blue pin icon:

```js
let pnt = L.marker([31.262218, 34.801472]).addTo(map);
```

**Circle markers**: a point drawn as a customizable circle (radius in *pixels*, so it stays constant size at any zoom):

```js
L.circleMarker([31.262, 34.800], {radius: 12, color: "black", fillColor: "red"}).addTo(map);
```

**Lines**: an array of `[lat, lng]` points, connected in order:

```js
let line = L.polyline(
    [[31.2627, 34.8005], [31.2621, 34.8008]],
    {color: "red", weight: 5}
).addTo(map);
```

**Polygons**: same idea, but closed and fillable (don't repeat the first point at the end):

```js
let pol = L.polygon(
    [[31.2631, 34.8037], [31.2625, 34.8031], [31.2617, 34.8036]],
    {color: "red", fillColor: "yellow", weight: 4}
).addTo(map);
```

**Transparency**: is set with the `fillOpacity` whereas, `opacity` controls the outline/stroke transparency. Both take a value from 0 (fully transparent) to 1 (fully opaque), and both work on polygon, circleMarker, and polyline (though polyline has no fill, so only opacity matters there), to note `fillOpacity` default is 0.2. Check out these few examples below:
```js
// Fully opaque fill
L.polygon(coords, {color: "red", fillColor: "yellow", fillOpacity: 1}).addTo(map);

// Half-transparent fill
L.polygon(coords, {color: "red", fillColor: "yellow", fillOpacity: 0.5}).addTo(map);

// Nearly invisible fill, outline only
L.polygon(coords, {color: "red", fillColor: "yellow", fillOpacity: 0.05}).addTo(map);
```

## Appendix B: No-Signup, No-API-Key Basemaps

*(Use with Part 5, Option A. Every basemap below loads on GitHub Pages with no account, no API key, and no domain whitelisting.)*

If you don't want to deal with signups or key management, stick to layer names from this list when using `L.tileLayer.provider("...")`. These are confirmed to work anonymously, on any domain, as of this writing (providers occasionally change their terms, so it's worth a quick visual spot-check against the [preview gallery](https://leaflet-extras.github.io/leaflet-providers/preview/index.html) if a layer ever stops loading).

**Standard / street map**
- `OpenStreetMap.Mapnik` (the default look; same as the basemap you already added in Part 1)
- `OpenStreetMap.HOT`
- `Esri.WorldStreetMap`

**Topographic / terrain**
- `OpenTopoMap`
- `Esri.WorldTopoMap`
- `Esri.WorldTerrain`
- `Esri.WorldShadedRelief`
- `USGS.USTopo` *(US coverage only)*
- `USGS.USImageryTopo` *(US coverage only)*

**Satellite / imagery**
- `Esri.WorldImagery`
- `USGS.USImagery` *(US coverage only)*

**Physical / reference**
- `Esri.OceanBasemap`
- `Esri.WorldPhysical`
- `Esri.NatGeoWorldMap`
- `Esri.WorldGrayCanvas`

**Specialty**
- `CyclOSM`

**Usage notes**

- Esri layers ask you to agree to their terms of service, but no API key is required in your code, they load fine anonymously for classroom use.
- The `USGS.*` layers only render tiles within the United States; if your map area is international, skip these and use one of the global options instead.
- These are all free to use but not unlimited. Fine for lab traffic, but don't hammer any single provider with heavy production-scale requests.
- If a layer you want isn't on this list (Stadia/Stamen, Mapbox, CartoDB, Thunderforest, HERE, Jawg, MapTiler, TomTom), it requires registration and an API key pasted into your `L.tileLayer.provider()` options; that's a valid choice too, just budget extra setup time and don't leave your key hardcoded in a public repo if the provider asks you to keep it private.
