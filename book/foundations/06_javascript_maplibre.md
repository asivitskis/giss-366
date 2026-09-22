# MapLibre GL JS


## Lecture: From Static Basemaps to Interactive, Data-Driven Web Maps

**Unit focus:** So far your maps have been built with Leaflet, loaded from local `js/` and `css/` folders alongside a `map.js` file. This unit introduces **MapLibre GL JS**, a different mapping engine with native support for vector tiles and 3D. We'll build every example as a **single self-contained HTML file**, loading MapLibre straight from a CDN, so you can go from a blank file to a working interactive map without setting up a project folder at all.

*This lecture was developed with reference to the open-source MapLibre lecture notebook from Dr. Qiusheng Wu's [geog-510](https://github.com/giswqs/geog-510) course materials, adapted here for MapLibre GL JS.*


## Learning Objectives

By the end of this unit, you will be able to:

- Explain what MapLibre GL JS adds over Leaflet (vector tiles, WebGL rendering, 3D)
- Build a complete interactive map in a single HTML file, using CDN links instead of a local `js/`/`css/` folder
- Initialize a MapLibre map with a chosen center, zoom, pitch, bearing, and basemap style
- Add navigation, geolocate, fullscreen, and draw controls
- Add GeoJSON, raster, and WMS layers using `addSource` / `addLayer`
- Write data-driven styling expressions (`interpolate`, `step`, `case`, `get`) to build choropleth and heatmap layers
- Cluster point data, customize marker icons, and add popups
- Build 3D extrusions (buildings, choropleths) and control the camera (`fitBounds`, `flyTo`, `maxBounds`)
- Build a simple HTML/CSS legend or color bar, since MapLibre GL JS has no built-in legend widget


## Part 1: Why MapLibre GL JS?

Leaflet renders vector graphics as SVG or Canvas, both are strictly 2D, and both treat "layers" as pre-rendered image tiles or drawn shapes. MapLibre GL JS instead renders everything through **WebGL**, talking directly to your computer's GPU. Two consequences follow directly from that:

1. **Vector tiles are native.** Instead of downloading pre-drawn PNG tiles, MapLibre downloads compact binary tiles full of geometry and attributes, and styles them live, in the browser. That's why changing a fill color in MapLibre is instant, no new tiles to generate, unlike the raster tile workflows you've already used with Leaflet.
2. **3D is possible.** Buildings can be extruded, terrain can be given real elevation, and the camera can pitch and rotate, none of which Leaflet can do.


## Part 2: One File, One Map — Setting Up with a CDN

Your Leaflet repos have followed this shape:

```
my-map/
├── index.html
├── css/
│   └── leaflet.css
├── js/
│   └── leaflet.js
└── map.js
```

For MapLibre, we're going to skip the folder structure entirely. [unpkg](https://unpkg.com) hosts the MapLibre GL JS library and stylesheet directly, so a `<script>` and `<link>` tag pointed at unpkg replace the whole `js/` and `css/` folders:

```html
<script src="https://unpkg.com/maplibre-gl@latest/dist/maplibre-gl.js"></script>
<link href="https://unpkg.com/maplibre-gl@latest/dist/maplibre-gl.css" rel="stylesheet">
```

Everything else, the map `<div>`, its CSS, and the JavaScript that builds the map, can live in that same `index.html`, inside `<style>` and `<script>` tags. No `map.js` to link, no local library files to keep track of. Here's the complete minimal file:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Map</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://unpkg.com/maplibre-gl@latest/dist/maplibre-gl.js"></script>
    <link href="https://unpkg.com/maplibre-gl@latest/dist/maplibre-gl.css" rel="stylesheet">
    <style>
        body { margin: 0; }
        #map { position: absolute; top: 0; right: 0; bottom: 0; left: 0; }
    </style>
</head>
<body>
    <div id="map"></div>

    <script>
        let map = new maplibregl.Map({
            container: 'map',
            style: 'https://tiles.openfreemap.org/styles/positron',
            center: [-100, 40],   // [lng, lat] -- note the order, opposite of Leaflet's [lat, lng]!
            zoom: 3,
            pitch: 0,
            bearing: 0
        });
    </script>
</body>
</html>
```

Save that as `index.html`, open it in a browser (no local server needed for this simple version), and you have a working map. As the map grows more complex later in this lecture, you're welcome to move the `<script>` contents into a separate `map.js` if you prefer, the CDN approach and the folder approach aren't mutually exclusive, but for MapLibre you have the option of staying in one file for as long as it stays manageable.

> **Watch out:** MapLibre (like GeoJSON) always writes coordinates as `[longitude, latitude]`. Leaflet's `L.marker([lat, lng])` uses the opposite order. Mixing these up is one of the most common web-mapping bugs, in either library.


### Choosing a basemap style

The `style` property is a URL pointing at a JSON document that describes every source and layer in the basemap. [OpenFreeMap](https://openfreemap.org) hosts several free ones with no API key required:

```js
style: 'https://tiles.openfreemap.org/styles/positron'   // light, label-heavy
style: 'https://tiles.openfreemap.org/styles/liberty'    // OSM-style default
style: 'https://tiles.openfreemap.org/styles/bright'
```

MapTiler-hosted styles (`streets`, `satellite`, `hybrid`, `topo`) need a free API key appended to the URL:

```js
const MAPTILER_KEY = 'YOUR_KEY_HERE';   // never commit a real key to a public repo
style: `https://api.maptiler.com/maps/streets-v2/style.json?key=${MAPTILER_KEY}`
```

### A background-color-only style

You can also hand-write a minimal style JSON directly, with no external tiles at all:

```js
let map = new maplibregl.Map({
    container: 'map',
    style: {
        version: 8,
        sources: {},
        layers: [{
            id: 'background',
            type: 'background',
            paint: { 'background-color': 'lightgray' }
        }]
    },
    center: [-100, 40],
    zoom: 3
});
```


> ### Try It
>
> Open any MapLibre GL JS example page (e.g. the [MapLibre docs examples](https://maplibre.org/maplibre-gl-js/docs/examples/)), open your browser console, and run:
>
> ```js
> map.getStyle().name
> map.getZoom()
> map.setZoom(10)
> map.setStyle('https://tiles.openfreemap.org/styles/dark-matter')
> ```
>
> Watch the basemap swap out live.


## Part 3: Map Controls

Controls are added inside a `'load'` event listener, so the map object exists first:

```js
map.on('load', function () {
    map.addControl(new maplibregl.NavigationControl(), 'top-left');
    map.addControl(new maplibregl.GeolocateControl({
        positionOptions: { enableHighAccuracy: true },
        trackUserLocation: true
    }), 'top-left');
    map.addControl(new maplibregl.FullscreenControl(), 'top-right');
    map.addControl(new maplibregl.ScaleControl(), 'bottom-left');
});
```

| Control | What it does |
|---|---|
| `NavigationControl` | zoom buttons + compass |
| `GeolocateControl` | centers the map on the user's location |
| `FullscreenControl` | expands the map to fill the screen |
| `ScaleControl` | shows a distance scale bar |

### Draw control

Drawing shapes on the map needs one more CDN library, the **mapbox-gl-draw** plugin, again loaded with plain `<script>`/`<link>` tags, no local install required:

```html
<script src="https://unpkg.com/@mapbox/mapbox-gl-draw@1.4.3/dist/mapbox-gl-draw.js"></script>
<link rel="stylesheet" href="https://unpkg.com/@mapbox/mapbox-gl-draw@1.4.3/dist/mapbox-gl-draw.css">
```

```js
let draw = new MapboxDraw({
    displayControlsDefault: false,
    controls: { polygon: true, line_string: true, point: true, trash: true }
});
map.addControl(draw, 'top-left');
```

To pre-load existing features, call `.set()` after the control is added:

```js
draw.set(myFeatureCollection);
```

Access what the user drew with `draw.getSelected()` (selected only) or `draw.getAll()` (everything).


## Part 4: Adding GeoJSON Layers

MapLibre always separates **where the data comes from** (a source) from **how it's drawn** (a layer). You add both inside the `'load'` listener, right alongside your controls.

### Points (circles)

```js
map.addSource('cities', { type: 'geojson', data: 'data/cities.geojson' });
map.addLayer({
    id: 'cities-layer',
    type: 'circle',
    source: 'cities',
    paint: {
        'circle-radius': 6,
        'circle-color': '#3182bd',
        'circle-stroke-width': 1,
        'circle-stroke-color': 'white'
    }
});
```

### Lines

```js
map.addSource('route', { type: 'geojson', data: 'data/route.geojson' });
map.addLayer({
    id: 'route-layer',
    type: 'line',
    source: 'route',
    layout: { 'line-join': 'round', 'line-cap': 'round' },
    paint: { 'line-color': '#e6550d', 'line-width': 3 }
});
```

### Polygons

```js
map.addSource('stat_data', { type: 'geojson', data: 'data/stat.geojson' });
map.addLayer({
    id: 'stat_layer',
    type: 'fill',
    source: 'stat_data',
    paint: {
        'fill-color': 'blue',
        'fill-outline-color': 'black',
        'fill-opacity': 0.5
    }
});
```

### Mixed-geometry GeoJSON

A single GeoJSON `FeatureCollection` can mix points, lines, and polygons. Add **one source** and **three layers**, each filtered to its geometry type:

```js
map.addSource('mixed', { type: 'geojson', data: 'data/mixed.geojson' });

map.addLayer({ id: 'mixed-fill', type: 'fill', source: 'mixed',
    filter: ['==', ['geometry-type'], 'Polygon'],
    paint: { 'fill-color': '#a1d99b', 'fill-opacity': 0.6 } });

map.addLayer({ id: 'mixed-line', type: 'line', source: 'mixed',
    filter: ['==', ['geometry-type'], 'LineString'],
    paint: { 'line-color': '#31a354', 'line-width': 2 } });

map.addLayer({ id: 'mixed-point', type: 'circle', source: 'mixed',
    filter: ['==', ['geometry-type'], 'Point'],
    paint: { 'circle-color': '#006d2c', 'circle-radius': 5 } });
```

### Local vs. remote data

The `data` value can be a URL string (as above) or an inline JavaScript object:

```js
map.addSource('inline', {
    type: 'geojson',
    data: { type: 'FeatureCollection', features: [ /* ... */ ] }
});
```


## Part 5: Clustering Points

Clustering is a property of the **source**, and it's drawn with three separate layers: clusters, cluster counts, and unclustered points.

```js
map.addSource('earthquakes', {
    type: 'geojson',
    data: 'data/earthquakes.geojson',
    cluster: true,
    clusterMaxZoom: 14,
    clusterRadius: 50
});

map.addLayer({
    id: 'clusters',
    type: 'circle',
    source: 'earthquakes',
    filter: ['has', 'point_count'],
    paint: {
        'circle-color': ['step', ['get', 'point_count'], '#51bbd6', 100, '#f1f075', 750, '#f28cb1'],
        'circle-radius': ['step', ['get', 'point_count'], 20, 100, 30, 750, 40]
    }
});

map.addLayer({
    id: 'cluster-count',
    type: 'symbol',
    source: 'earthquakes',
    filter: ['has', 'point_count'],
    layout: { 'text-field': ['get', 'point_count_abbreviated'], 'text-size': 12 }
});

map.addLayer({
    id: 'unclustered-point',
    type: 'circle',
    source: 'earthquakes',
    filter: ['!', ['has', 'point_count']],
    paint: { 'circle-color': '#11b4da', 'circle-radius': 6, 'circle-stroke-width': 1, 'circle-stroke-color': '#fff' }
});
```

The `['step', ...]` expression buckets clusters by point count and colors/sizes them accordingly.


## Part 6: Custom Marker Icons

Using a custom icon image is a two-step process: **load** the image into the map's sprite, then **reference** it in a symbol layer.

```js
map.loadImage('img/custom-marker.png', (error, image) => {
    if (error) throw error;
    map.addImage('custom-marker', image);

    map.addSource('points', { type: 'geojson', data: 'data/points.geojson' });
    map.addLayer({
        id: 'points-layer',
        type: 'symbol',
        source: 'points',
        layout: {
            'icon-image': 'custom-marker',
            'icon-size': 0.5,
            'icon-allow-overlap': true   // otherwise MapLibre hides overlapping icons by default
        }
    });
});
```

### HTML markers

If you just need one or two draggable, DOM-based pins (rather than a data-driven layer of thousands), MapLibre also has a `Marker` class that behaves much like Leaflet's `L.marker()`:

```js
new maplibregl.Marker({ color: 'red' })
    .setLngLat([-100, 40])
    .addTo(map);
```

Use **layers** (as above) for large or data-driven point sets, they're rendered on the GPU and stay fast at any scale. Use `Marker` objects (this section) only for a handful of one-off pins, since each one is a real DOM element.


## Part 7: Data-Driven Styling & Popups

### Expressions

MapLibre's "expression language" is how you write data-driven styling. The most common four:

| Expression | Purpose |
|---|---|
| `['get', 'density']` | read a GeoJSON property |
| `['interpolate', ['linear'], ['get', 'density'], 0, '#fef0d9', 14965, '#b30000']` | smooth color/size ramp |
| `['step', ['get', 'count'], 'small', 10, 'medium', 100, 'large']` | discrete buckets |
| `['case', ['==', ['get', 'type'], 'water'], 'blue', 'gray']` | if/else logic |

A choropleth fill, replacing a flat `'fill-color': 'blue'`:

```js
'fill-color': [
    'interpolate', ['linear'], ['get', 'density'],
    0,     '#fef0d9',
    865,   '#fdcc8a',
    2287,  '#fc8d59',
    7754,  '#e34a33',
    14965, '#b30000'
]
```

### Popups on click

```js
map.on('click', 'stat_layer', function (e) {
    let coordinates = e.lngLat;
    let props = e.features[0].properties;
    let description = `<strong>${props.SHEM_YISHUV}</strong><br>${Math.round(props.density)} people/km²`;

    new maplibregl.Popup()
        .setLngLat(coordinates)
        .setHTML(description)
        .addTo(map);
});

// Optional: swap the cursor to a pointer over clickable features
map.on('mouseenter', 'stat_layer', () => { map.getCanvas().style.cursor = 'pointer'; });
map.on('mouseleave', 'stat_layer', () => { map.getCanvas().style.cursor = ''; });
```

### Restyling a layer after the fact

You can restyle a live map with no reload needed:

```js
map.setPaintProperty('stat_layer', 'fill-color', 'orange');
```

### Label styling and placement

```js
map.addLayer({
    id: 'city-labels',
    type: 'symbol',
    source: 'cities',
    layout: {
        'text-field': ['upcase', ['get', 'name']],
        'text-variable-anchor': ['top', 'bottom', 'left', 'right'],  // let MapLibre pick the least-crowded side
        'text-radial-offset': 0.5,
        'text-justify': 'auto'
    }
});
```


> ### Try It
>
> On a live MapLibre example page, run each of these one at a time and watch the map respond immediately:
>
> ```js
> map.setPaintProperty('water', 'fill-color', 'orange');
> map.setLayoutProperty('water', 'visibility', 'none');
> map.setLayoutProperty('water', 'visibility', 'visible');
> ```


## Part 8: Raster, WMS & Heatmaps

### XYZ raster tiles

```js
map.addSource('usgs-topo', {
    type: 'raster',
    tiles: ['https://basemap.nationalmap.gov/arcgis/rest/services/USGSTopo/MapServer/tile/{z}/{y}/{x}'],
    tileSize: 256,
    attribution: 'USGS'
});
map.addLayer({ id: 'usgs-topo-layer', type: 'raster', source: 'usgs-topo' });
```

### WMS layers

A WMS server is added the same way as XYZ tiles, MapLibre has no dedicated WMS type, it just builds a tiled `GetMap` request URL for you:

```js
const wmsUrl = 'https://img.nj.gov/imagerywms/Natural2015';
map.addSource('nj-imagery', {
    type: 'raster',
    tiles: [
        `${wmsUrl}?bbox={bbox-epsg-3857}&format=image/png&service=WMS&version=1.1.1` +
        `&request=GetMap&srs=EPSG:3857&transparent=true&width=256&height=256&layers=Natural2015`
    ],
    tileSize: 256
});
map.addLayer({ id: 'nj-imagery-layer', type: 'raster', source: 'nj-imagery' });
```

### Cloud Optimized GeoTIFFs (COGs)

Browser-side MapLibre GL JS has **no built-in COG reader**. A COG has to be tiled by a server first (e.g. [titiler](https://developmentseed.org/titiler/) or a cloud provider), then added exactly like the XYZ example above, pointed at the tiler's `{z}/{x}/{y}` endpoint instead of a plain file path.

### Heatmaps

```js
map.addSource('quakes', { type: 'geojson', data: 'data/earthquakes.geojson' });
map.addLayer({
    id: 'quakes-heat',
    type: 'heatmap',
    source: 'quakes',
    paint: {
        'heatmap-weight': ['interpolate', ['linear'], ['get', 'mag'], 0, 0, 6, 1],
        'heatmap-intensity': 1,
        'heatmap-radius': 20,
        'heatmap-opacity': 0.8
    }
});
```


## Part 9: 3D Extrusions

This is the one category of map that Leaflet genuinely cannot produce, extrusion needs the WebGL/3D pipeline that only MapLibre (or another WebGL-based library) provides.

Switching a polygon layer from flat to extruded is smaller than it looks: change `type` to `'fill-extrusion'`, and add a `'fill-extrusion-height'` paint property alongside the color:

```js
map.addLayer({
    id: 'towns_layer',
    type: 'fill-extrusion',
    source: 'towns_data',
    paint: {
        'fill-extrusion-color': [
            'interpolate', ['linear'], ['get', 'density'],
            0,     '#fef0d9',
            865,   '#fdcc8a',
            2287,  '#fc8d59',
            7754,  '#e34a33',
            14965, '#b30000'
        ],
        'fill-extrusion-height': ['get', 'density'],
        'fill-extrusion-opacity': 0.9,
        'fill-extrusion-base': 0
    }
});
```

Give the map some `pitch` (e.g. `pitch: 60`) when you initialize it, or the extrusion will be invisible from directly overhead.

### Building footprints from vector tiles

Real building extrusions come from a vector tile source keyed to a `render_height` property, with height computed so buildings only appear once you're zoomed in close:

```js
map.addSource('openmaptiles', {
    type: 'vector',
    url: `https://api.maptiler.com/tiles/v3/tiles.json?key=${MAPTILER_KEY}`
});
map.addLayer({
    id: '3d-buildings',
    source: 'openmaptiles',
    'source-layer': 'building',
    type: 'fill-extrusion',
    minzoom: 15,
    paint: {
        'fill-extrusion-color': ['interpolate', ['linear'], ['get', 'render_height'], 0, 'lightgray', 200, 'royalblue', 400, 'lightblue'],
        'fill-extrusion-height': ['interpolate', ['linear'], ['zoom'], 15, 0, 16, ['get', 'render_height']],
        'fill-extrusion-base': ['case', ['>=', ['get', 'zoom'], 16], ['get', 'render_min_height'], 0]
    }
});
```


## Part 10: Camera & Interaction Control

```js
// Fit to a bounding box, e.g. after loading a GeoJSON file
map.fitBounds([[-125, 24], [-66, 49]], { padding: 20 });

// Fly smoothly to a new location
map.flyTo({ center: [-122.4194, 37.7749], zoom: 12, essential: true });

// Prevent the user from panning outside a region
let map2 = new maplibregl.Map({
    container: 'map',
    style: 'https://tiles.openfreemap.org/styles/positron',
    center: [34.8, 31.3],
    zoom: 8,
    maxBounds: [[34.0, 29.3], [35.9, 33.4]]   // roughly Israel's extent
});

// Disable scroll-to-zoom
map.scrollZoom.disable();

// Read the cursor's coordinates live
map.on('mousemove', (e) => {
    document.getElementById('coords').innerText =
        `${e.lngLat.lng.toFixed(4)}, ${e.lngLat.lat.toFixed(4)}`;
});
```


## Part 11: Legends, Color Bars & Overlays

MapLibre GL JS has **no built-in legend or color bar widget** — a legend is just an HTML `<div>` you position over the map with CSS, the same DOM-overlay technique you've already used for other on-page content.

```html
<div id="legend">
    <strong>Population Density</strong><br>
    <span style="background:#fef0d9">&nbsp;&nbsp;&nbsp;</span> 0–865<br>
    <span style="background:#fdcc8a">&nbsp;&nbsp;&nbsp;</span> 865–2287<br>
    <span style="background:#fc8d59">&nbsp;&nbsp;&nbsp;</span> 2287–7754<br>
    <span style="background:#e34a33">&nbsp;&nbsp;&nbsp;</span> 7754–14965<br>
</div>
```

```css
#legend {
    position: absolute;
    bottom: 30px; right: 10px;
    background: white;
    padding: 10px;
    font: 12px sans-serif;
    border-radius: 4px;
    box-shadow: 0 1px 4px rgba(0,0,0,0.3);
    z-index: 1;
}
```

A logo or attribution image works the same way, an `<img>` inside an absolutely-positioned `<div>` layered over the `#map` div.


## Quick Recap: A Complete MapLibre Page

Putting the pieces above together, here's a full page combining a styled basemap, a GeoJSON choropleth layer, a popup, and a legend — still just **one file**, no folders, no separate `map.js`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My MapLibre Map</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://unpkg.com/maplibre-gl@latest/dist/maplibre-gl.js"></script>
    <link href="https://unpkg.com/maplibre-gl@latest/dist/maplibre-gl.css" rel="stylesheet">
    <style>
        body { margin: 0; }
        #map { position: absolute; top: 0; right: 0; bottom: 0; left: 0; }
        #legend {
            position: absolute; bottom: 30px; right: 10px;
            background: white; padding: 10px; font: 12px sans-serif;
            border-radius: 4px; box-shadow: 0 1px 4px rgba(0,0,0,0.3); z-index: 1;
        }
    </style>
</head>
<body>
    <div id="map"></div>
    <div id="legend"><strong>Density</strong><br>low → high</div>

    <script>
        let map = new maplibregl.Map({
            container: 'map',
            style: 'https://tiles.openfreemap.org/styles/positron',
            center: [34.8, 31.3],
            zoom: 8
        });

        map.on('load', function () {
            map.addControl(new maplibregl.NavigationControl(), 'top-left');

            map.addSource('stat_data', { type: 'geojson', data: 'data/stat.geojson' });
            map.addLayer({
                id: 'stat_layer',
                type: 'fill',
                source: 'stat_data',
                paint: {
                    'fill-color': [
                        'interpolate', ['linear'], ['get', 'density'],
                        0, '#fef0d9', 865, '#fdcc8a', 2287, '#fc8d59', 7754, '#e34a33', 14965, '#b30000'
                    ],
                    'fill-outline-color': 'black',
                    'fill-opacity': 0.8
                }
            });

            map.on('click', 'stat_layer', function (e) {
                new maplibregl.Popup()
                    .setLngLat(e.lngLat)
                    .setHTML(`Density: ${Math.round(e.features[0].properties.density)}`)
                    .addTo(map);
            });
        });
    </script>
</body>
</html>
```

Everything the student needs, library, styling, data, and interactivity, lives in this one `index.html`. As a project grows (more layers, more custom controls), it's reasonable to split the `<script>` block out into its own `map.js` file, exactly the way your Leaflet repos already do, but nothing about MapLibre *requires* that split the way local library files did.


## Exercises

### Exercise 1: Basic Map & Basemap Styles
Initialize a map centered on a country of your choice with an appropriate zoom level, using the `positron` OpenFreeMap style. Then add a `<select>` dropdown (or just a second script block) that switches the map's style to `liberty` when clicked, using `map.setStyle(...)`.

### Exercise 2: Customizing the Map View
Create a 3D view of a city of your choice: an appropriate `zoom`, `pitch`, and `bearing`, using the `liberty` style. If you have a MapTiler key, try swapping in `3d-satellite` or `3d-topo` MapTiler styles and compare.

### Exercise 3: Map Controls
Add a `GeolocateControl` (top-left), a `FullscreenControl` (top-right), and a Draw control configured for points, lines, and polygons (top-left) to a map of your choice.

### Exercise 4: Overlaying Data Layers
Add the NYC buildings and NYC roads GeoJSON layers to a map with sensible styling:
- Buildings: `https://github.com/opengeos/datasets/releases/download/places/nyc_buildings.geojson`
- Roads: `https://github.com/opengeos/datasets/releases/download/places/nyc_roads.geojson`

Then, separately, add the USGS Topo XYZ raster layer from Part 8 to a satellite-style basemap, with a layer-visibility toggle control.

### Exercise 5: 3D Buildings
Build a 3D map centered on a city of your choice, and add extruded building footprints (Part 9) using a custom color gradient tied to building height.

### Exercise 6: Legends & Overlays
Add a custom marker icon (Part 6) plus a text label to your map, and build an HTML/CSS legend (Part 11) describing what the icon represents.


## Readings & Resources

- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 14: MapLibre GL JS
- [MapLibre GL JS Documentation](https://maplibre.org/maplibre-gl-js/docs/)
- [MapLibre GL JS Examples Gallery](https://maplibre.org/maplibre-gl-js/docs/examples/)
- [OpenFreeMap](https://openfreemap.org) — free, no-API-key vector basemap styles
- [Mapbox Style Specification](https://maplibre.org/maplibre-style-spec/) — the reference for every `paint`/`layout` property and expression used above (MapLibre's style spec is a fork of this one)

