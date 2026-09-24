# Lab 5


# MapLibre GL JS in Practice: How Much Can We Customize a Web Map?
### GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 6 Focus:** MapLibre GL JS. In Lab 4 you wrote a Leaflet `map.js` from a blank file. Today you build **five different interactive maps** with MapLibre GL JS, a WebGL-based library with native support for vector tiles, data-driven styling, and 3D. Every map is a single, self-contained HTML file loaded from a CDN. This is your fifth graded lab, and the first time you will style data with expressions, extrude 3D buildings, and publish a multi-map gallery.

You are welcome to start from the sample code in the Week 6 lecture notebook and the [GISS 366 MapLibre Examples repo](https://github.com/asivitskis/GISS366-MapLibre-Examples). However, **each map you submit must be customized so that it is clearly your own** (different location, different colors and styling, different data choices). A map that is a copy of a lecture example with nothing changed will not receive full credit.


## Before You Begin

The tools are the same as Labs 3 and 4. Confirm you still have them before proceeding.

| Tool | Role today |
|---|---|
| **A GitHub account** | Already confirmed in Lab 3 |
| **VS Code, with the Git/GitHub extension enabled** | Cloning the Lab 5 template and editing files locally |
| **Git** | Same install as Lab 3 |
| **VS Code + Live Server** | Previewing each map locally as you build it. Live Server runs on `localhost`, which is also what allows the Geolocate control and your local GeoJSON files to work |
| **The Lab 5 template repository** | Provided by your instructor; contains a `docs/` folder with a gallery page, five map files, and a `data/` folder of New Mexico datasets |
| **A modern web browser with developer tools** | Press `F12` to open the Console. It is your first stop whenever a map does not appear |
| **The Week 6 lecture notebook** and the **[GISS 366 MapLibre Examples repo](https://github.com/asivitskis/GISS366-MapLibre-Examples)** | Your source of sample code for every exercise |
| **[MapLibre GL JS documentation](https://maplibre.org/maplibre-gl-js/docs/)** and **[MapLibre Style Specification](https://maplibre.org/maplibre-style-spec/)** | Your first stops when a property or expression does not work as expected |
| **A few locations of your own** | You will need real coordinates for several of your maps (see Step 1) |

You already know how to clone a repo, edit locally, preview with Live Server, and publish via GitHub Pages. That workflow does not change today. There are two differences:

1. **Every map is one self-contained HTML file.** There is no separate `map.js`, and no local `js/` or `css/` folders. MapLibre loads from a CDN.
2. **GitHub Pages will publish from the `/docs` folder, not the root.** This makes your gallery page the front door of your site.


## Step 1: Ask

This Lab will ask you to build 5 seperate MapLibre maps.

Before opening the template repositoryuse this time to prep your data and write down some first-pass answers:
- To start thinking about how you want to customize each map, pick a place for each of the maps below, and find its approximate coordinates (a quick search of "[place name] coordinates" will get you close enough). **Write them in MapLibre order: `[longitude, latitude]`.** You can center your maps anywhere but Silver City, NM; as this was the example we used throughout the lecture.
  - **Map 1:** a city to view that you'd like to view in a tilted and rotated fashion.
  - **Map 2:** a location where you'd want a map with lots of controls.
  - **Map 4:** four points of your own (favorite places, trailheads, places you have lived) and one *numeric* attribute for each (a rating, a year, an elevation).
  - **Map 5:** a large, dense city for 3D buildings to explore. Building-height data in OpenStreetMap is far more complete in big cities (New York, Chicago, New Dehli, Addis Ababa, Tokyo) than in small towns.
- Map 3 uses the New Mexico datasets in your template repo (counties, roads, populated places). Which two do you think would make the most interesting combination, and why?
- Revewing all the five maps that you will be as described above (view and basemap styles, controls, data layers, popups and legend, 3D buildings), which do you predict will take the **most** code? Which the **least**?

> **Lab 5 Question: How much of a MapLibre map do you think you can customize, and what do you expect will limit you (the basemap, the data, the browser, your own code)?**


## Step 2: Collect

### Part A: Confirm access

Same as Labs 3 and 4: confirm you can log in to GitHub, and that you have the link to the Lab 5 template repository (shared via Canvas prior to lab).

- https://github.com/asivitskis/giss366-lab05-template.git

### Part B: Clone the repo

This is the same process as Labs 3 and 4; if it is still fresh, skim and move on.

**Step 0: Create your own copy of the template repository.**

1. In a browser, go to the template repository URL above.
2. Make sure you are signed into your GitHub account.
3. Click **Use this template** → **Create a new repository**.
4. Name it something like `giss-366-lab5`, under **your own** account.
5. Set the repository to **Public** (free GitHub accounts can only publish GitHub Pages from public repositories).
6. Click **Create repository**, then copy the URL of **this new repository** (not the original template).

**Clone your own copy into VS Code:**

1. Open VS Code, no files open (File → New Window if needed).
2. Open the **Source Control** panel and click **Clone Repository**.
3. Paste your new repository's URL, and choose a save location near your other `giss-366` folders.
4. Choose **Open** when prompted.

### Part C: Explore what you cloned

**All of your work happens inside the `docs/` folder.**

```
your-repo/
├── LICENSE
├── README.md
└── docs/
    ├── index.html
    ├── map1.html
    ├── map2.html
    ├── map3.html
    ├── map4.html
    ├── map5.html
    └── data/
        ├── mora_county_roads.geojson
        ├── nm_counties.geojson
        ├── nm_populated_places.geojson
```

| File / folder | What it is | Will you edit it today? |
|---|---|---|
| `docs/index.html` | Your **gallery page**: a title and a list of five links, one per map, each with a placeholder description | **Yes** (Step 4, Part 1) |
| `docs/map1.html` – `docs/map5.html` | One file per exercise. Each will hold a complete MapLibre map | **Yes.** This is the bulk of today's work (Step 3) |
| `docs/data/nm_counties.geojson`, `mora_county_roads.geojson`, `nm_populated_places.geojson` | Public New Mexico datasets from the [New Mexico Resource Geographic Information System (RGIS)](https://rgis.unm.edu/). You will load them in Map 3 | No (use them as-is) |
| `README.md` | The repository landing page, with placeholders for your description and gallery link | **Yes** (Step 4, Part 2) |
| `LICENSE` | Repository license | No |

**Why `docs/`?** GitHub Pages can publish either your repository's root or a folder named `docs`. Your gallery lives in `docs/` so that, once Pages is pointed there, your `index.html` becomes the home page of your site and README/LICENSE stay out of the way. You will set this up in Step 4.

### Part D: Refer back to the lecture starter template

Every map begins from the same minimal page from lecture Part 2 (Example Map 6-2). Open `docs/map1.html` (and the others). If they contain placeholder content, replace it with this starter and build on it.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Map</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://unpkg.com/maplibre-gl@^6.10.0/dist/maplibre-gl.css" />
    <style>
        body { margin: 0; }
        #map { position: absolute; top: 0; right: 0; bottom: 0; left: 0; }
    </style>
</head>
<body>
    <div id="map"></div>
    <script type="module">
        import * as maplibregl from 'https://unpkg.com/maplibre-gl@^6.10.0/dist/maplibre-gl.mjs';

        const map = new maplibregl.Map({
            container: 'map',
            style: 'https://tiles.openfreemap.org/styles/positron',
            center: [-100, 40],   // [lng, lat]
            zoom: 3
        });
        window.map = map;
    </script>
</body>
</html>
```

Compare this to the Leaflet skeleton from Lab 4. The CDN `<link>` and module `import` replace your `css/` and `js/` folders, and the map object is built with `new maplibregl.Map({...})` instead of `L.map(...)`.

**Things to remember before you start**

- **Coordinates are `[longitude, latitude]`**, the opposite of Leaflet.
- **Add sources, layers, and controls inside `map.on('load', ...)`.**
- **Every layer `id` must be unique** within a map.
- **Layers draw in the order they are added.** Later layers appear on top of earlier ones.
- **Local data files (`data/...`) will not load if you double-click the HTML file.** Browsers block this. Right-click the file in VS Code and choose **Open with Live Server**, or test on your GitHub Pages URL.
- **Open your browser's developer console (`F12`)** whenever something does not appear. Most problems (typos, wrong layer type, blocked data URLs) show up there as red errors.


## Step 3: Visualize

This is the bulk of the lab. You will build **five maps, one per exercise**, each in its own file inside `docs/`. Work on one map at a time:

1. Start from the lecture starter template (Step 2, Part D).
2. Add one feature at a time, and **preview in Live Server after each addition** rather than writing everything and debugging at once.
3. When a map is finished, **commit it** (Source Control panel → stage → commit message → commit) so your repository history shows your progress. You will push everything in Step 4.

### Customization Requirement

To keep every submission original:

- **Location:** Maps 1, 2, 4, and 5 must be centered on a location of *your* choosing. **Do not use Silver City, NM (the lecture example location).** Each map should also use a *different* location or theme from your other maps where practical.
- **Styling:** Choose your own colors, sizes, opacities, and expression breakpoints. Exact values from lecture examples (for example `#e25822`, `royalblue`, `#088`) is not enough to count as customization.
- **Comment block:** At the top of the `<script>` in each file, add a short comment listing **at least three things you customized** compared with the lecture code:

```js
/* CUSTOMIZATIONS
   1. Centered on Lisbon, Portugal instead of the lecture location
   2. Changed extrusion color ramp to a purple-to-yellow gradient
   3. Added a second popup field showing building height
*/
```

The OpenFreeMap style URLs you will use are listed in **Appendix A**.


### Exercise 1: Basic Map, Camera View & Basemap Selection

*Lecture reference: Part 2, Basic MapLibre Map with CDN (Example Map 6-2)*

**File:** `docs/map1.html`

- Create a map centered on a **city of your choice** with an appropriate zoom level, a **pitch** between 40 and 60 degrees, and a **non-zero bearing**, so the view opens tilted and rotated.
- Update the basemap tiles to something other than `positron`. Review review Appendix A at the end of this lab document for options, or refer back to the lecture notes.
- Add a `title` for the page and a small text label on the map naming the city.

**Optional extension:** add a button that uses `map.flyTo(...)` to have the map zoom to your city upon loading.

<details>
<summary><b>Common bugs to check for, if something is off. Click to expand</b></summary>

- **The map is centered in the wrong part of the world.** Did you swap longitude and latitude? MapLibre uses `[lng, lat]`.
- **The dropdown does nothing.** Each `<option>` value must be a complete style URL. Attach your listener with `addEventListener('change', ...)` inside the module script.
- **The dropdown or label is hidden behind the map.** Position it with `position: absolute` and a `z-index` (the lecture's legend CSS uses `z-index: 1`).
- **The basemap is blank after switching.** Check the exact style URL. OpenFreeMap's dark style is `.../styles/dark`, not `dark-matter`.

</details>


### Exercise 2: Adding Map Controls

*Lecture reference: Part 3, Map Controls (Example Map 6-3)*

**File:** `docs/map2.html`

- Create a map centered on a location of your choice and add the following controls:
  - **NavigationControl** at the top left.
  - **FullscreenControl** at the top right.
  - **ScaleControl** at the bottom left.
  - **Terra Draw** control at the top left, with modes for **point, linestring, and polygon**, plus **at least one additional mode** from the lecture list (`rectangle`, `circle`, `freehand`, and or your preference).


<details>
<summary><b>Common bugs to check for, if something is off. Click to expand</b></summary>

- **The draw toolbar does not appear and the console says `MaplibreTerradrawControl is not defined`.** The plugin's `<script>` and `<link>` tags are missing or mistyped. Compare them against the lecture, and keep the plugin script a classic tag (no `type="module"`).
- **Controls overlap each other.** Several controls at `'top-left'` stack in the order you add them. This is expected; it is a layout choice, not a bug.
- **`draw.getAll()` returns an empty result.** You must draw at least one feature before you log it.

</details>


### Exercise 3: Overlaying Data Layers

*Lecture references: Part 4 (Markers & Geometries), Part 5 (Data Sources), Part 7 (Raster and WMS layers)*

**File:** `docs/map3.html`

This map combines vector data and a raster overlay.

**Part A: GeoJSON layers**

- Add **at least two GeoJSON layers with different geometry types** (for example polygons + lines, or polygons + points). Use the New Mexico datasets already in your `docs/data/` folder (`nm_counties.geojson`, `mora_county_roads.geojson`, `nm_populated_places.geojson`) and load them with relative paths such as `data: 'data/nm_counties.geojson'`. You may also bring in additional GeoJSON from another source, as long as you confirm it loads in the browser (raw files from `raw.githubusercontent.com` generally do) and cite its source in your README.
- Match each layer `type` to its geometry (`fill`, `line`, `circle`) and style it with appropriate `paint` properties. For polygons, add a separate `line` layer for the outline (as shown in lecture Part 4).
- Add a default `maplibregl.Marker` at a place of interest in your map area.

**Part B: Raster overlay**

- Add **one raster layer** on top of your basemap, using either the **USGS Topo XYZ tile source** from the lecture or a **WMS source** (note that a WMS layer only displays where the service has coverage, so center your map accordingly).
- Add the raster layer **before** your vector layers so your vector data stays visible on top. If it makes sense, make sure it is partially transparent (`'raster-opacity'`) so the basemap or vector data is still readable.

**Optional challenge:** add a checkbox that toggles the raster layer on and off using `map.setLayoutProperty('your-layer-id', 'visibility', 'none')` (and `'visible'` to show it again).

<details>
<summary><b>Common bugs to check for, if something is off. Click to expand</b></summary>

- **Nothing shows, and the console has a failed request for your GeoJSON.** Check the file path. It is relative to the HTML file, so it should look like `data/nm_counties.geojson` (not `docs/data/...`). Also make sure you are viewing the page through Live Server, not by double-clicking the file.
- **Your map is not looking at the data.** The provided datasets are in New Mexico. Center your map there (roughly `[-106.1, 34.5]` at zoom 6, as in the lecture recap) or use `fitBounds([[-109.05, 31.33], [-103.00, 37.00]])`.
- **Nothing appears, and there is no fetch error.** The layer `type` does not match the geometry (for example, a `fill` layer pointed at line data). Check *Anatomy of a layer* in the lecture.
- **The expression has no effect.** Property names in `['get', '...']` are case-sensitive and must match the file exactly.
- **The raster layer covers your vector data.** Layers draw in the order they were added. Add the raster first.
- **The WMS layer is blank.** The service probably has no coverage where your map is centered.

</details>


### Exercise 4: Popups

*Lecture reference: Part 6, Popups & Dynamic Options (Example Map 6-6) and the Quick Recap example*

**File:** `docs/map4.html`

- Build your own GeoJSON `FeatureCollection` directly in the script with **four points of your choice** (for example favorite restaurants, hiking trailheads, or places you have lived). Each feature needs at least three properties, including a **`name`** and one **numeric** property (for example `rating`, `year`, or `elevation`).
- Style the points with a `circle` layer.
- Add a **click popup** on the points that displays the name and at least two other properties, and change the **cursor to a pointer** when hovering over a point (following the lecture pattern).

**Optional Challenge**  coloring your points by the numeric property using a **`step`** or **`interpolate`** expression. Or, try to add a legend (see lecture notes).

<details>
<summary><b>Common bugs to check for, if something is off. Click to expand</b></summary>

- **Clicking a point does nothing.** The `map.on('click', 'your-layer-id', ...)` listener must use the exact `id` of your circle layer and be registered inside `map.on('load', ...)`.
- **The points appear in the wrong place.** Check `[lng, lat]` order in every feature's `coordinates`.
- **The popup says `undefined`.** The name inside `e.features[0].properties.___` must match your property name exactly.


</details>


### Exercise 5: Working with 3D Buildings

*Lecture reference: Part 8, 3D Extrusions (Example Map 6-8)*

**File:** `docs/map5.html`

- Set up a 3D map centered on a **city of your choice** with a zoom of about 15 to 17, a **pitch of at least 45 degrees**, and a non-zero bearing. Include `canvasContextAttributes: { antialias: true }`.
- Add an extruded building layer from the OpenFreeMap vector source using `fill-extrusion`, with height driven by `render_height`, and insert it beneath the label layer as in the lecture code.
- Use a **custom color gradient of at least three stops** tied to `render_height`. Your stops must be different from the lecture example, and the height breakpoints should make sense for the city you chose.
- Add a `NavigationControl` with `visualizePitch: true` so the user can see and reset the tilt.

**Tip:** OpenStreetMap building-height data is much more complete in large, dense cities (for example New York, Chicago, Toronto, London, Tokyo) than in small towns, where many buildings default to a low, uniform height. If your extrusions all look the same, try a denser city.

<details>
<summary><b>Common bugs to check for, if something is off. Click to expand</b></summary>

- **No buildings appear.** The lecture layer has `minzoom: 15`. Zoom in closer than that.
- **Buildings look flat.** The map has a pitch of 0. Set a pitch of 45 or more, or drag with the right mouse button to tilt.
- **All buildings are the same color.** Your color stops do not match the range of heights in your city. A small-town gradient (3, 6, 12 meters) will not spread across skyscrapers; use larger breakpoints such as 10, 30, and 100.
- **The lecture code is copied but nothing changes.** The center and zoom are still the lecture's. Update them for your own city.

</details>


## Step 4: Create

Your five maps are built. Now turn your repository into a published gallery.

<details>
<summary><b>Part 1: Update your gallery page (<code>docs/index.html</code>). Click to expand</b></summary>

Open `docs/index.html`. It looks like this:

```html
<!DOCTYPE html>
<html>
<head>
  <title>GISS 366 Lab 05</title>
</head>
<body>
  <h1>[update your title here]</h1>
  <ul>
    <li><a href="map1.html">[update map description here]</a></li>
    <li><a href="map2.html">[update map description here]</a></li>
    <li><a href="map3.html">[update map description here]</a></li>
    <li><a href="map4.html">[update map description here]</a></li>
    <li><a href="map5.html">[update map description here]</a></li>
  </ul>
</body>
</html>
```

1. Replace `[update your title here]` with a title for your gallery (for example, "Lisbon and Beyond: MapLibre Web Maps").
2. Replace each `[update map description here]` with a **short description of that map**: a phrase or sentence naming the location and what the map shows. **Do not change the `href` links.**
3. Preview in Live Server, and click each link to confirm it opens the right map.

Example:

```html
<li><a href="map1.html">Tilted view of Lisbon with a dark/light basemap switcher</a></li>
```

**Common bug:** if a description mentions something your map does not actually do (or omits something it does), the grader will notice. Keep each description in sync with the map behind the link.

</details>

<details>
<summary><b>Part 2: Update your <code>README.md</code>. Click to expand</b></summary>

Open `README.md`. It looks like this:

```markdown
# GISS 366 Lab 05
[update your description here]

## Web Map Gallery

Gallery Link: [insert your github pages link]

---

All maps are hosted via **GitHub Pages** and open directly in the browser. All files within data folder are sourced from publicly available [New Mexico Resource Geographic Information System](https://rgis.unm.edu/), accessed 09/24/2026.
```

1. Replace `[update your description here]` with a **two to three sentence description** of your lab: what you built and which locations or themes you chose.
2. Replace `[insert your github pages link]` with your live GitHub Pages URL. You will get this URL in Part 4, so you can come back and fill this in afterward. It has this form: `https://<your-username>.github.io/<your-repo-name>/`
3. If you added any data beyond the files provided in `docs/data/`, add a sentence at the bottom naming the source of that data.

</details>

<details>
<summary><b>Part 3: Commit and push. Click to expand</b></summary>

Same process as Labs 3 and 4:

1. **Stage and commit**, using VS Code's Source Control panel. Stage `docs/index.html`, `docs/map1.html` through `docs/map5.html`, and `README.md` (plus any data files you added). Write a commit message (for example, `Build five MapLibre maps for Lab 5`), and commit.
2. **Push**, using Sync Changes / Push.
3. On GitHub, refresh your repository and confirm your updated files appear.

</details>

<details>
<summary><b>Part 4: Publish with GitHub Pages from the <code>/docs</code> folder. Click to expand</b></summary>

This is the one step that is different from Lab 4: you will publish from the `docs` folder instead of the root.

1. In your repository on GitHub, go to **Settings** → **Pages**.
2. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
3. Under **Branch**, select `main` and change the folder from `/ (root)` to **`/docs`**, then click **Save**.
4. Wait a minute or two, then refresh the Pages settings screen. Your site URL will appear at the top:

   ```
   https://<your-username>.github.io/<your-repo-name>/
   ```

   Because you chose the `/docs` folder, this URL opens your `index.html` gallery page directly, and each map is available at `.../map1.html`, `.../map2.html`, and so on.
5. Go back to your `README.md` and paste this URL in as your **Gallery Link**. Commit and push again.

**Final check.** Open your Pages URL in a **private/incognito browser window** and confirm:
- The gallery page loads with your title and descriptions.
- **All five map links open and display correctly**, and match what you saw in Live Server (local data layers, popups, and controls included).

**If your site shows your README or a 404 instead of your gallery,** return to Settings → Pages and confirm the folder is set to `/docs`. After each push, it can take a minute or two for the live site to update.

</details>


## Step 5: Act

### What to turn in (graded: Lab Exercises)

1. The link to your GitHub repository (Collect, Part B).
2. The link to your published GitHub Pages gallery (Create, Part 4).
3. A short written reflection (~300 words) answering:
   - Revisit the **Lab 5 Question** and your Step 1 predictions. How much of a MapLibre map were you able to customize, and what limits did you actually run into? Which exercise took more (or less) code than you expected?
   - Name one specific bug you hit while building your maps, and how you found and fixed it (console error, swapped coordinates, mismatched layer type, a legend that did not match the expression, etc.).
   - Name the customization that you thought turned out the best, and explain why.
   - Compare today to Lab 4: what could you do with MapLibre that you could not have done with Leaflet, and what felt harder?

Confirm all five links open correctly in an incognito window before you submit. Submit your GitHub links and written reflection to this week's submission space in Canvas.


---

## Lab 5 Rubric (40 pts)


---

### Lab 5 AI Policy: Level 2

This lab permits optional use of AI to support student learning. Permitted uses can include using AI to help troubleshoot GIS workflows and debug JavaScript errors (for example, pasting a console error message to ask what it means), however, independent installation and use of AI agents to complete GIS tasks is not permitted at this time. AI may also be used to support general writing procedures (outlining a project, improving clarity or grammar, etc.), but all code and written responses must remain original to each student as a sole responsible author. Disclosure of AI use is not required for this Level 2 assignment. Using AI is a choice with ethical considerations, and it is asked that students weigh those considerations accordingly as they complete this work.

---

### Resources

- Week 6 lecture notebook: *MapLibre GL JS*
- [GISS 366 MapLibre Examples Repo](https://github.com/asivitskis/GISS366-MapLibre-Examples)
- [MapLibre GL JS Documentation](https://maplibre.org/maplibre-gl-js/docs/)
- [MapLibre GL JS Examples Gallery](https://maplibre.org/maplibre-gl-js/docs/examples/)
- [MapLibre Style Specification](https://maplibre.org/maplibre-style-spec/) (every `paint` / `layout` property and expression)
- [OpenFreeMap](https://openfreemap.org)
- [MDN Web Docs](https://developer.mozilla.org/)
- Dorman, M. *[Introduction to Web Mapping](https://bgu-geography.com/web-mapping/maplibre.html)*, Chapter 14: MapLibre GL JS
- [GitHub Docs: Configuring a publishing source for your GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)


## Appendix A: OpenFreeMap Basemap Styles

*(Use with any exercise that asks for a basemap. All of these are free, require no account, and need no API key.)*

Set the map's `style` property to one of these URLs, or pass one to `map.setStyle(...)`:

| Style | URL |
|---|---|
| Positron (light) | `https://tiles.openfreemap.org/styles/positron` |
| Liberty | `https://tiles.openfreemap.org/styles/liberty` |
| Bright | `https://tiles.openfreemap.org/styles/bright` |
| Dark | `https://tiles.openfreemap.org/styles/dark` |
| Fiord (dark blue) | `https://tiles.openfreemap.org/styles/fiord` |

**Notes**
- MapTiler-hosted styles (`streets`, `satellite`, `hybrid`, `topo`) also work with MapLibre, but they require an API key. This lab does not need one, and you should never commit a real key to a public repository.

