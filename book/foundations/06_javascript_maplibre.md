# MapLibre GL JS


## Learning Objectives

By the end of this unit, you will be able to:

- Explain what MapLibre GL JS adds over Leaflet (vector tiles, WebGL rendering, 3D), and build a complete interactive map in a single CDN-loaded HTML file
- Initialize a map with a chosen center, zoom, pitch, bearing, and basemap style, and add navigation, geolocate, fullscreen, and draw controls
- Add GeoJSON, clustered, raster, and WMS layers using `addSource`/`addLayer`, styled with data-driven expressions (`interpolate`, `step`, `get`)
- Add markers and popups, and build a simple HTML/CSS legend, since MapLibre GL JS has no built-in legend widget
- Build 3D extrusions and terrain, and control the camera with `fitBounds`, `flyTo`, and `maxBounds`

### Notebook Overview: From Static Basemaps to Interactive, Data-Driven Web Maps

So far your maps have been built with Leaflet, loaded from local `js/` and `css/` folders alongside a `map.js` file. This unit introduces **MapLibre GL JS**, a different mapping engine with native support for vector tiles and 3D. We'll build every example as a **single self-contained HTML file**, loading MapLibre straight from a CDN, so you can go from a blank file to a working interactive map without setting up a project folder at all.

*This lecture was developed with reference to the open-source MapLibre lecture notebook from Dr. Qiusheng Wu's [geog-510](https://github.com/giswqs/geog-510) course materials, adapted here for MapLibre GL JS. All code examples are built with reference to [MapLibre Examples](https://maplibre.org/maplibre-gl-js/docs/examples/).*

Examples of all maps from the sections below can be found hosted at this [GISS 366 MapLibre Examples Repo](https://github.com/asivitskis/GISS366-MapLibre-Examples)

### Why MapLibre GL JS?

Leaflet renders vector graphics as SVG or Canvas, both are strictly 2D, and both treat "layers" as pre-rendered image tiles or drawn shapes. MapLibre GL JS instead renders everything through **WebGL**, talking directly to your computer's GPU. Two consequences follow directly from that:

1. **Vector tiles are native.** Instead of downloading pre-drawn PNG tiles, MapLibre downloads compact binary tiles full of geometry and attributes, and styles them live, in the browser. That's why changing a fill color in MapLibre is instant, no new tiles to generate, unlike the raster tile workflows you've already used with Leaflet.
2. **3D is possible.** Buildings can be extruded, terrain can be given real elevation, and the camera can pitch and rotate, none of which Leaflet can do.


## Basic MapLibre Map with CDN

Example Map 6-2: [Basic MapLibre Example](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_2_BasicMap.html)

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

For MapLibre, we're going to skip the folder structure entirely. [unpkg](https://unpkg.com) hosts the MapLibre GL JS library and stylesheet directly, so a `<link>` tag plus a small ES module `<script>` replace the whole `js/` and `css/` folders. This is the "CDN / No bundler" setup from [MapLibre's own installation docs](https://maplibre.org/maplibre-gl-js/docs/#__tabbed_1_6):

```html
<link rel="stylesheet" href="https://unpkg.com/maplibre-gl@^6.10.0/dist/maplibre-gl.css" />
<script type="module">
    import * as maplibregl from 'https://unpkg.com/maplibre-gl@^6.10.0/dist/maplibre-gl.mjs';
</script>
```

The `^6.10.0` is a semver range, not a fixed version, MapLibre will serve the newest compatible `6.x` release automatically. The library is loaded as an **ES module** here (`type="module"`, `import ... from`), not as a classic script. That has one consequence worth knowing up front: variables declared at the top level of a module script (like `map` below) are *not* attached to `window`, so they won't be visible if you try to poke at them from the browser console the way you could with a classic script. We'll add one line to work around that.

Everything else, the map `<div>`, its CSS, and the JavaScript that builds the map, can live in that same `index.html`, inside `<style>` and `<script>` tags. No `map.js` to link, no local library files to keep track of. Here's the complete minimal file:

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
            center: [-100, 40],   // [lng, lat] -- note the order, opposite of Leaflet's [lat, lng]!
            zoom: 3,
            pitch: 0,
            bearing: 0
        });
        window.map = map;   // exposes map to the browser console for debugging
    </script>
</body>
</html>
```

Save that as `index.html`, open it in a browser (no local server needed for this simple version), and you have a working map. As the map grows more complex later in this lecture, you're welcome to move the `<script>` contents into a separate `map.js` if you prefer (keep `type="module"` on the `<script>` tag that loads it), the CDN approach and the folder approach aren't mutually exclusive, but for MapLibre you have the option of staying in one file for as long as it stays manageable.

> **Why `window.map = map`?** Inside a module script, top-level variables are scoped to that module, not attached to `window` the way classic-script variables are. Without this line, typing `map.getZoom()` in your browser's dev console would throw `map is not defined`. Adding it back gives you the same live-debugging workflow you're used to.

> **Watch out:** MapLibre (like GeoJSON) always writes coordinates as `[longitude, latitude]`. Leaflet's `L.marker([lat, lng])` uses the opposite order. Mixing these up is one of the most common web-mapping bugs, in either library.


### Choosing a basemap style

The `style` property is a URL pointing at a JSON document that describes every source and layer in the basemap. [OpenFreeMap](https://openfreemap.org) hosts several free ones with no API key required:

```js
style: 'https://tiles.openfreemap.org/styles/positron'   // light, label-heavy
style: 'https://tiles.openfreemap.org/styles/liberty'    // OSM-style default
style: 'https://tiles.openfreemap.org/styles/bright'
```

MapTiler-hosted styles (`streets`, `satellite`, `hybrid`, `topo`). You need a free API key appended to the URL, however you should be cautious to never publically expose your API key or other visitors/clients could steal this information. If you're interested to investigate MapTiler, please proceed with caution. 

```js
const MAPTILER_KEY = 'YOUR_KEY_HERE';   // never commit a real key to a public repo
style: `https://api.maptiler.com/maps/streets-v2/style.json?key=${MAPTILER_KEY}`
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


## Map Controls

Example Map 6-3: [MapLibre Map with Controls](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_3_MapControls.html)

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

Drawing shapes on the map needs one more CDN library, the **[terra-draw](https://maplibre.org/maplibre-gl-js/docs/examples/draw-polygon-with-mapbox-gl-draw/#__tabbed_1_2)** plugin, again loaded with plain `<script>`/`<link>` tags, no local install required:

```html
<script src="https://cdn.jsdelivr.net/npm/@watergis/maplibre-gl-terradraw@1.0.1/dist/maplibre-gl-terradraw.umd.js"></script>
<link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/@watergis/maplibre-gl-terradraw@1.0.1/dist/maplibre-gl-terradraw.css"
/>
```

> Note this stays a **classic** (non-`module`) script tag on purpose, that's how the plugin attaches `terra-draw` to the global `window`. Your main map code, in the `type="module"` script, can still read that global fine; module scoping only restricts what a module *creates* at its own top level, not what it can read.

```js
const draw = new MaplibreTerradrawControl.MaplibreTerradrawControl({
        modes: [
            // 'render', comment this to always show drawing tool
            'point',
            'linestring',
            'polygon',
            'select',
            'delete-selection',
            'delete',
            'download'
        ],
        open: true,
    });
    map.addControl(draw, 'top-left');
```

Multiple additional drawing options include the following, try some of these out:

```js
'rectangle',
'circle',
'freehand',
'angled-rectangle',
'sensor',
'sector',
```

Access what the user drew with `draw.getSelected()` (selected only) or `draw.getAll()` (everything).


## Adding Markers & Geometries

Example Map 6-4: [Simple MapLibre Styled Point Map](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_4_AddingGeometry.html)

Similar to Leaflet, MapLibre is able to add and draw geometries. Check out the Part 4 example to modify and explore these options. Remember to update your map center and Zoom accordingly for each new addition. 

### Default Marker

```js
const marker = new maplibregl.Marker()
    .setLngLat([-108.2833, 32.7764])
    .addTo(map);

```
For more complex geometries, MapLibre separates where the data comes from (a **source**) from how it's drawn (a **layer**). Add both inside the `'load'` listener, alongside your controls. All examples below go inside `map.on('load', () => { ... })`.

### Anatomy of a layer

Every `addLayer` call is an object with the same top-level keys:

| Key | What it does |
|---|---|
| `id` | A unique name for this layer (no two layers can share one) |
| `source` | Which source's data to draw |
| `type` | *How* to draw it: `circle`, `line`, or `fill` |
| `layout` | Structural options (e.g. `line-cap`, `line-join`, `visibility`) |
| `paint` | Visual options (color, size, opacity) |

The `type` must match the geometry in your source:

| Geometry | Layer `type` | Common `paint` properties |
|---|---|---|
| Point | `circle` | `circle-radius`, `circle-color`, `circle-opacity`, `circle-stroke-width`, `circle-stroke-color` |
| LineString | `line` | `line-color`, `line-width`, `line-opacity`, `line-dasharray` |
| Polygon | `fill` | `fill-color`, `fill-opacity`, `fill-outline-color` |

Every property is listed in the [MapLibre Style Spec](https://maplibre.org/maplibre-style-spec/layers/). Once you know these five keys, you can style any layer in this lab.

### Points

Markers are quick, but a `circle` layer is what you'll use with real datasets.

```js
map.on('load', () => {
    map.addSource('site-source', {
        type: 'geojson',
        data: { type: 'Feature', properties: {}, geometry: { type: 'Point', coordinates: [-108.2833, 32.7764] } }
    });
    map.addLayer({
        id: 'site-circle', type: 'circle', source: 'site-source',
        paint: { 'circle-radius': 10, 'circle-color': '#e25822', 'circle-stroke-width': 2, 'circle-stroke-color': '#ffffff' }
    });
});
```

**Try it:** change the radius, then add `'circle-opacity': 0.5`.

### Lines

```js
map.on('load', () => {
    map.addSource('route-source', {
        type: 'geojson',
        data: { type: 'Feature', properties: {}, geometry: { type: 'LineString', coordinates: [
            [-108.2750622, 32.7796674], [-108.270348, 32.7830646], [-108.2612936, 32.7851407],
            [-108.2487223, 32.7874683], [-108.2046233, 32.783012], [-108.1822079, 32.7798317],
            [-108.1594598, 32.7822223], [-108.1510789, 32.7820965]
        ] } }
    });
    map.addLayer({
        id: 'route-line', type: 'line', source: 'route-source',
        layout: { 'line-join': 'round', 'line-cap': 'round' },
        paint: { 'line-color': '#888', 'line-width': 8 }
    });
});
```

**Try it:** change `line-width`, add `'line-dasharray': [2, 2]`, and set `line-cap` to `'butt'`.

### Polygons

One source can feed more than one layer. Here the same polygon gets a fill and a separate outline (`fill-outline-color` is always 1px wide, so a `line` layer is the way to get a thicker border). Here's an example webmap that uses this stacked layer, single source technique [link.](https://asivitskis.github.io/OpenMaps/CBC_demo_map.html)

```js
map.on('load', () => {
    map.addSource('park-source', {
        type: 'geojson',
        data: { type: 'Feature', properties: {}, geometry: { type: 'Polygon', coordinates: [[
            [-108.2853603, 32.77102], [-108.2982235, 32.7688568], [-108.3009621, 32.76132],
            [-108.294323, 32.7538525], [-108.2866051, 32.7528754], [-108.2824557, 32.7575514],
            [-108.280381, 32.7606222], [-108.2810449, 32.7701128], [-108.2853603, 32.77102]
        ]] } }
    });
    map.addLayer({
        id: 'park-fill', type: 'fill', source: 'park-source',
        paint: { 'fill-color': '#088', 'fill-opacity': 0.8 }
    });
    map.addLayer({
        id: 'park-outline', type: 'line', source: 'park-source',   // same source, second layer
        paint: { 'line-color': '#044', 'line-width': 3 }
    });
});
```

**Try it:** change `fill-opacity` and `fill-color`, then the outline's `line-width`.

## Data Sources

So far every `data` value you've written has been an inline JavaScript object, typed directly into the script. That works for a quick example, but it doesn't scale, and it isn't how real maps get their data. This section covers where a GeoJSON source's data can actually live, and introduces one new kind of source that isn't a file at all.

Recall the guiding question from Lab 0: what's the difference between Web Mapping and WebGIS? The two static sources below (local and remote) are still Web Mapping, since the browser is just fetching a fixed file. The ArcGIS Online source at the end of this section crosses into WebGIS territory, since the browser is querying a live, filterable service instead.

**The source changes, but the layer doesn't.** When adding these sources, pick the layer `type` that matches your data's geometry (see *Anatomy of a layer*), then restyle with `paint`:

| If your data contains... | Use `type` | Try styling with |
|---|---|---|
| Points | `circle` | `circle-radius`, `circle-color` |
| Lines | `line` | `line-color`, `line-width` |
| Polygons | `fill` | `fill-color`, `fill-opacity` |

Layer `id`s must be unique, so give each new layer its own name.

### Static files: Local Data Source

Example Map 6-5-1: [Local Data Loaded onto MapLibre](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_5a_Local_Data.html)
*view the file structure at this [repo](https://github.com/asivitskis/GISS366-MapLibre-Examples/tree/main/docs)*

In the MapLibre Examples file, a `data` is included within the docs folder that has a selection of public data downloaded in the geojson format from rgis. These files include:
 - `nm_populated places.geojson`
 - `mora_county_roads.geojson`
 - `nm_counties.geojson`

You'll use the same `addSource` call for each example. And with the `addLayer` component, you include how the object is identified and painted. Below is a code snipped to load the example point data. Try swapping out the file path for the other example datasets, modify the add layer example from part [add part here] to update their styling.

```js
// A file sitting in a data/ folder next to this HTML file
map.on('load', () => {
        map.addSource('cities', {
            type: 'geojson',
            data: 'data/nm_populated_places.geojson'
        });

        map.addLayer({
            id: 'cities-points',
            type: 'circle',
            source: 'cities',
            paint: {
                'circle-radius': 4,
                'circle-color': '#007cbf'
            }
        });
```
### Static Files: Remotely Hosted

Example Map 6-5-2: [MapLibre Map with Data from Hosted Source](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_5b_Hosted_Data.html)

You can use the same same `addSource` call for static datasets that are hosted remotely. Remember to update style features accordingly.

Here are some example datasets we can use from public test data hosting environments [GeoJson XYZ](https://geojson.xyz/) and [OpenGeos Data Repository](https://github.com/opengeos/data)

 - point: https://d2ad6b4ur7yvpq.cloudfront.net/naturalearth-3.3.0/ne_110m_populated_places_simple.geojson
 - line: https://d2ad6b4ur7yvpq.cloudfront.net/naturalearth-3.3.0/ne_50m_rivers_lake_centerlines.geojson
 - polygon: https://github.com/opengeos/data/blob/main/raster/basin.geojson

Simply update the `data` section of your `.addSource` call to reflect the statically hosted geojson. Then make sure to update the style features when you use `map.addLayer`

```js
// Example for adding a remote GeoJson
map.addSource('update this name', {
    type: 'geojson',
    data: '[put your desired URL here]'
});
```

> **CORS note:** a remote GeoJSON URL only works if the server hosting it allows cross-origin requests. GitHub Pages does this by default, which is one reason it's a convenient place to host course data; some other hosts won't, and the fetch will fail silently in the console with a CORS error rather than a missing-file error.

### Live data: an ArcGIS Online feature service

Example Map 6-5-3: [Adding Hosted Feature Service to MapLibre](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_5c_ESRI_Rest.html)

Both examples above are static files: the browser downloads the whole thing once and MapLibre draws it. An ArcGIS Online **feature service** is different. Instead of a file, you're pointing at a live, queryable API, the same kind of endpoint you worked with in Lab 1 when you published your own hosted feature layer.

The MapLibre ArcGIS plugin allows you to easily access and work with the ArcGIS Basemap Styles service, feature services, and vector tile services. To use the library you must first install the MapLibre GL JS library in your project. See the full ESRI documentation [here](https://developers.arcgis.com/maplibre-gl-js/api-reference/).

**1. Add the MapLibre ArcGIS plugin via an import map, alongside MapLibre GL JS itself:**

```html
<script type="importmap">
  {
    "imports": {
      "maplibre-gl": "https://unpkg.com/maplibre-gl@6.10.0/dist/maplibre-gl.mjs",
      "@esri/maplibre-arcgis": "https://unpkg.com/@esri/maplibre-arcgis@1.3.1/dist/esm/maplibre-arcgis.min.js"
    }
  }
</script>
```

**2. Import both libraries in your module script:**

```javascript
import * as maplibregl from 'maplibre-gl';
import * as maplibreArcGIS from '@esri/maplibre-arcgis';
```

**3. Once the map has loaded, point `FeatureLayer.fromUrl()` at the service's REST endpoint and add it to the map:**
Remember, if your feature service has more than one layer. you need to specific which layer you are requesting at the end of the URL. Upate the `/0` to match the correct layer index. 

```javascript
map.on('load', async () => {
    const ptService = "YOUR_FEATURE_SERVICE_URL/FeatureServer/0";
    const layer = await maplibreArcGIS.FeatureLayer.fromUrl(ptService);
    layer.addSourcesAndLayersTo(map);
});
```

That's the whole pattern: `fromUrl()` fetches the schema and features, `addSourcesAndLayersTo()` wires up a MapLibre source and a default-styled layer in one call. 

#### Custom styling a feature service

The default renderer from `addSourcesAndLayersTo()` is fine for a quick look, but for full control over paint properties, split the process into two steps: add the source, then write your own layer. Swap in `addSourcesTo()` instead if you want to write your own `map.addLayer()` call for custom styling, same as with a local or GitHub-hosted GeoJSON.

**1. Use `addSourcesTo()` instead of `addSourcesAndLayersTo()`.** This creates the MapLibre source (querying the live feature service) but skips the default layer:

```javascript
const earthquakes = await maplibreArcGIS.FeatureLayer.fromUrl(ptService);
earthquakes.addSourcesTo(map);
```

**2. Write your own `map.addLayer()` call, spreading in `earthquakes.layer`.** This gives you the correct `id` and `source` already wired up, so you only need to set `type` and `paint`:

```javascript
map.addLayer({
    ...earthquakes.layer,
    type: 'circle',
    paint: {
        'circle-radius': 6,
        'circle-color': '#e25822',
        'circle-stroke-width': 1,
        'circle-stroke-color': '#ffffff'
    }
});
```
This is the same pattern you already know from styling a local or GitHub-hosted GeoJSON source: a `type`, a `paint` block, standard MapLibre paint properties. The only difference is where the `id`/`source` values come from. Swap in your own examples.

### Querying a feature service
Just like in Lab 01, we can use a query to dynamically querry the layer for only the data that we want to display:
Because you're pointing at a live API rather than a static file, you can ask the service to filter and trim the data before it ever reaches the browser. Pass a `query` option as the second argument to `fromUrl()`:

```javascript
const earthquakes = await maplibreArcGIS.FeatureLayer.fromUrl(ptService, {
    query: {
        outFields: ['mag', 'depth', 'time'],
        where: 'mag > 4'
    }
});

earthquakes.addSourcesTo(map);


**Key difference from a static GeoJSON:** the feature service stays live. Change the `where` clause in a query option and re-run `fromUrl()`, and you get fresh data with no change to your styling code.

## Popups & Dynamic Options

Example Map 6-6: [Simple MapLibre Map with Pop-Ups](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_6_PopUps.html) 

### Pop-Ups

Simple pop-ups can be added like default markers.

Just like we learned in Week 4 - HTML is the langauge controlling the content displayed within our web interface. Popups will use the .setHTML properties to indicate what you want displayed. 

```js
const popup = new maplibregl.Popup({closeOnClick: false})
    .setLngLat([-96, 37.8])
    .setHTML('<h1>Hello World!</h1>')
    .addTo(map);
```

### Popups on click

Custom popups on click require additional event listeners. (See [MapLibre Popup Example](https://maplibre.org/maplibre-gl-js/docs/examples/display-a-popup-on-click/))

Simple pop-ups can be added by identifying your layer's `id`, and setting the HTML properties to your layer's attributes. For example change the `Name` and the `properties.NAME` to reflect your feature.

```js
// Add your points as GeoJSON
    map.addSource('places', {
        type: 'geojson',
        data: {
            type: 'FeatureCollection',
            features: [
                {
                    type: 'Feature',
                    properties: {
                        name: 'Point 1',
                        rating: 1
                    },
                    geometry: {
                        type: 'Point',
                        coordinates: [-108.2812252, 32.7746687]
                    }
                },
                {
                    type: 'Feature',
                    properties: {
                        name: 'Point 2',
                        rating: 2
                    },
                    geometry: {
                        type: 'Point',
                        coordinates: [-107.7564321, 32.2613517]
                    }
                },
                {
                    type: 'Feature',
                    properties: {
                        name: 'Point 3',
                        rating: 3
                    },
                    geometry: {
                        type: 'Point',
                        coordinates: [-107.1580646, 32.6698278]
                    }
                },
                {
                    type: 'Feature',
                    properties: {
                        name: 'Point 4',
                        rating: 4
                    },
                    geometry: {
                        type: 'Point',
                        coordinates: [-107.2211734, 33.1976318]
                    }
                }
            ]
        }
    });

    // Add a layer showing the points
    map.addLayer({
        id: 'places',
        type: 'circle',
        source: 'places',
        paint: {
            'circle-radius': 8,
            'circle-color': '#1978A5',
            'circle-stroke-color': '#FFFFFF',
            'circle-stroke-width': 2
        }
    });

    // When a point is clicked, open a popup
    map.on('click', 'places', (e) => {

        const coordinates = e.features[0].geometry.coordinates.slice();
        const name = e.features[0].properties.name;

        // Keep popup attached to the correct copy of the point
        while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
            coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
        }

        new maplibregl.Popup()
            .setLngLat(coordinates)
            .setHTML(`<strong>${name}</strong>`) // This is where the pop-up content is identified. 
            .addTo(map);
    });

    // Change cursor to pointer when hovering over a point
    map.on('mouseenter', 'places', () => {
        map.getCanvas().style.cursor = 'pointer';
    });

    // Change cursor back when leaving a point
    map.on('mouseleave', 'places', () => {
        map.getCanvas().style.cursor = '';
    });

```
#### Customizing Popup Text

We can modify the .setHTML expression to change how we want a popup to be visualized. This is all done with HTML styling code.

For multiline popups we can use `<br>` tags to introduce a line break between called properties. See this [HTML Tutorial](https://www.w3schools.com/tags/tag_br.asp) for additional reference. 

Complex popups in MapLibre can be challenge, see these [Stacker Overflow](https://stackoverflow.com/questions/54731968/how-do-i-display-multiple-geojson-properties-in-a-popup-using-mapbox-gl) or [GitHub Discussion]() threads for real life examples of people working through these considerations.

Below is a quick example of displaying multiple Geojson properties for the sample `nm_populated_places.geojson` dataset within the tutorial `/data` folder. 

```js

// note the <br> tag within the .setHTML expression which adds a line break and new property

    map.on('click', 'cities-points', function (e) {
                new maplibregl.Popup()
                    .setLngLat(e.lngLat)
                    .setHTML(`Name: ${e.features[0].properties.FEATURE_NA}<br>
                    County Name: ${e.features[0].properties.COUNTY_NAM}`)
                    .addTo(map);
            });
```

**Try it:** for the Pop-Ups on click example above (6.6.2), how might you use a `<br>` tag within the .setHTML expression to also include the `rating` example property within the pop up?

## Raster and WMS layers

Example Map 6-7: [Adding Raster Layers to MapLibre](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_7_Raster.html)

### XYZ raster tiles

You can add raster data like xyz tile layers on top of your standard basemaps. 
```js
map.on('load', () => {
    map.addSource('usgs-topo', {
        type: 'raster',
        tiles: ['https://basemap.nationalmap.gov/arcgis/rest/services/USGSTopo/MapServer/tile/{z}/{y}/{x}'],
        tileSize: 256,
        attribution: 'USGS'
    });
    map.addLayer({ id: 'usgs-topo-layer', type: 'raster', source: 'usgs-topo' });
});
```

### WMS layers

A WMS server is added the same way as XYZ tiles, MapLibre has no dedicated WMS type, it just builds a tiled `GetMap` request URL for you:

```js
const wmsUrl = 'https://img.nj.gov/imagerywms/Natural2015';

map.on('load', () => {
            map.addSource('nj-imagery', {
                type: 'raster',
                tiles: [
                    `${wmsUrl}?bbox={bbox-epsg-3857}&format=image/png&service=WMS&version=1.1.1` +
                    `&request=GetMap&srs=EPSG:3857&transparent=true&width=256&height=256&layers=Natural2015&styles=`
                ],
                tileSize: 256
            });

            map.addLayer({
                id: 'nj-imagery-layer',
                type: 'raster',
                source: 'nj-imagery'
            });
        });
```

This example points at a New Jersey imagery service because it is a live, no-key-required WMS that reliably works from a browser. New Mexico's RGIS clearinghouse (rgis.unm.edu) hosts some of its own data as WMS/WFS services too; once you pick a specific RGIS layer, check its GetCapabilities document for the exact layer name and swap it into this same URL pattern.

We can also add WMS from our own GeoServer. Change the code to use your layer's correct `id` and `source`.

```js
const wmsUrl = 'https://138-68-249-92.sslip.io/geoserver/ows';

map.on('load', () => {
            map.addSource('gc-roads', {
                type: 'raster',
                tiles: [
                    `${wmsUrl}?bbox={bbox-epsg-3857}&format=image/png&service=WMS&version=1.3.0` +
                    `&request=GetMap&crs=EPSG:3857&transparent=true&width=256&height=256&layers=instructor_giss366:gc_roads&styles=`
                ],
                tileSize: 256
            });

            map.addLayer({
                id: 'gc-roads-layer',
                type: 'raster',
                source: 'gc-roads'
            });
        });

```


## 3D Extrusions

Example Map 3-8: [WNMU 3D Building Map](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_8_3D_Extrusions.html)

This is the one category of map that Leaflet genuinely cannot produce, extrusion needs the WebGL/3D pipeline that only MapLibre (or another WebGL-based library) provides.

Switching a polygon layer from flat to extruded is smaller than it looks: change `type` to `'fill-extrusion'`, and add a `'fill-extrusion-height'` paint property alongside the color. 


### Building footprints from vector tiles

Real building extrusions come from a vector tile source keyed to a `render_height` property, with height computed so buildings only appear once you're zoomed in close:

```js
import * as maplibregl from 'https://unpkg.com/maplibre-gl@6.10.0/dist/maplibre-gl.mjs';

const map = new maplibregl.Map({
    style: `https://tiles.openfreemap.org/styles/bright`,
    center: [-108.2833, 32.7764],
    zoom: 15.5,
    pitch: 45,
    bearing: -17.6,
    container: 'map',
    canvasContextAttributes: {antialias: true}
});

// The 'building' layer in the streets vector source contains building-height
// data from OpenStreetMap.
map.on('load', () => {
    // Insert the layer beneath any symbol layer.
    const layers = map.getStyle().layers;

    let labelLayerId;
    for (let i = 0; i < layers.length; i++) {
        if (layers[i].type === 'symbol' && layers[i].layout['text-field']) {
            labelLayerId = layers[i].id;
            break;
        }
    }

    map.addSource('openfreemap', {
        url: `https://tiles.openfreemap.org/planet`,
        type: 'vector',
    });

    map.addLayer(
        {
            'id': '3d-buildings',
            'source': 'openfreemap',
            'source-layer': 'building',
            'type': 'fill-extrusion',
            'minzoom': 15,
            'filter': ['!=', ['get', 'hide_3d'], true],
            'paint': {
                'fill-extrusion-color': [
                    'interpolate',
                    ['linear'],
                    ['get', 'render_height'], 3, 'lightgray', 6, 'royalblue', 12, 'lightblue'
                ],
                'fill-extrusion-height': [
                    'interpolate',
                    ['linear'],
                    ['zoom'],
                    15,
                    0,
                    16,
                    ['get', 'render_height']
                ],
                'fill-extrusion-base': ['case',
                    ['>=', ['get', 'zoom'], 16],
                    ['get', 'render_min_height'], 0
                ]
            }
        },
        labelLayerId
    );
});
```


## 3D Terrain; Camera & Interaction Control

Example Map 3-9: [Silver City 3D Terrain Map Demo](https://asivitskis.github.io/GISS366-MapLibre-Examples/6_9_3D_Terrain.html#12/32.7764/-108.2833/-17.6/70)

### Custom Camera Controls

Use the following commands to help set defeault zoom levels and make dynamic adjustments. 

```js
// Fit to a bounding box, e.g. after loading a GeoJSON file
map.fitBounds([[-109.05, 31.33], [-103.00, 37.00]], { padding: 20 });   // roughly New Mexico's extent

// Fly smoothly to a new location
map.flyTo({ center: [-108.2803, 32.7701], zoom: 12, essential: true });   // Silver City, NM

// Prevent the user from panning outside a region
let map2 = new maplibregl.Map({
    container: 'map',
    style: 'https://tiles.openfreemap.org/styles/positron',
    center: [-106.1, 34.5],
    zoom: 6,
    maxBounds: [[-109.05, 31.33], [-103.00, 37.00]]   // roughly New Mexico's extent
});

// Disable scroll-to-zoom
map.scrollZoom.disable();

// Read the cursor's coordinates live
map.on('mousemove', (e) => {
    document.getElementById('coords').innerText =
        `${e.lngLat.lng.toFixed(4)}, ${e.lngLat.lat.toFixed(4)}`;
});
```
### Building a 3D Terrain View

MapLibre is able to display 3D terrain through straightforward additions of DEM and hillshade layers. Review this full [MapLibre Tutorial](https://maplibre.org/maplibre-gl-js/docs/examples/3d-terrain/) for the base code. Update center locations, view orientations, and terrain exaggeration for customization.

```js
import * as maplibregl from 'https://unpkg.com/maplibre-gl@6.11.0/dist/maplibre-gl.mjs';

const map = new maplibregl.Map({
    container: 'map',
    zoom: 12,
    center: [-108.2833, 32.7764],
    pitch: 70,
    hash: true,
    style: {
        version: 8,
        sources: {
            osm: {
                type: 'raster',
                tiles: ['https://a.tile.openstreetmap.org/{z}/{x}/{y}.png'],
                tileSize: 256,
                attribution: '&copy; OpenStreetMap Contributors',
                maxzoom: 19
            },
            // Use a different source for terrain and hillshade layers, to improve render quality
            terrainSource: {
                type: 'raster-dem',
                url: 'https://tiles.mapterhorn.com/tilejson.json'
            },
            hillshadeSource: {
                type: 'raster-dem',
                url: 'https://tiles.mapterhorn.com/tilejson.json'
            }
        },
        layers: [
            {
                id: 'osm',
                type: 'raster',
                source: 'osm'
            },
            {
                id: 'hills',
                type: 'hillshade',
                source: 'hillshadeSource',
                layout: {visibility: 'visible'},
                paint: {'hillshade-shadow-color': '#473B24'}
            }
        ],
        terrain: {
            source: 'terrainSource',
            exaggeration: 1
        },
        sky: {}
    },
    maxZoom: 18,
    maxPitch: 85
});

map.addControl(
    new maplibregl.NavigationControl({
        visualizePitch: true,
        showZoom: true,
        showCompass: true
    })
);

map.addControl(
    new maplibregl.TerrainControl({
        source: 'terrainSource',
        exaggeration: 1
    })
);
```


## Quick Recap: A Complete MapLibre Page

Putting the pieces above together, here's a full page combining a styled basemap, a GeoJSON choropleth layer, a popup, and a legend. It's still just **one file**, no folders, no separate `map.js`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My MapLibre Map</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://unpkg.com/maplibre-gl@^6.10.0/dist/maplibre-gl.css" />
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

    <script type="module">
        import * as maplibregl from 'https://unpkg.com/maplibre-gl@^6.10.0/dist/maplibre-gl.mjs';

        const map = new maplibregl.Map({
            container: 'map',
            style: 'https://tiles.openfreemap.org/styles/positron',
            center: [-106.1, 34.5],
            zoom: 6
        });
        window.map = map;   // exposes map to the browser console for debugging

        map.on('load', function () {
            map.addControl(new maplibregl.NavigationControl(), 'top-left');

            map.addSource('nm_counties', { type: 'geojson', data: 'data/nm_counties.geojson' });
            map.addLayer({
                id: 'nm_counties_layer',
                type: 'fill',
                source: 'nm_counties',
                paint: {
                    'fill-color': [
                        'interpolate', ['linear'], ['get', 'density'],
                        0, '#fef0d9', 5, '#fdcc8a', 20, '#fc8d59', 75, '#e34a33', 200, '#b30000'
                    ],
                    'fill-outline-color': 'black',
                    'fill-opacity': 0.3
                }
            });

            map.on('click', 'nm_counties_layer', function (e) {
                new maplibregl.Popup()
                    .setLngLat(e.lngLat)
                    .setHTML(`Name: ${e.features[0].properties.NAME}`)
                    .addTo(map);
            });
        });
    </script>
</body>
</html>
```

Everything we need, library, styling, data, and interactivity, lives in this one `index.html`. As a project grows (more layers, more custom controls), it's reasonable to split the `<script>` block out into its own `map.js` file, exactly the way your Leaflet repos already do, but nothing about MapLibre *requires* that split the way local library files did.


## Exercises

### Exercise 1: Basic Map & Basemap Styles
Initialize a map centered on a country of your choice with an appropriate zoom level, using the `positron` OpenFreeMap style. Then add a `<select>` dropdown (or just a second script block) that switches the map's style to `liberty` when clicked, using `map.setStyle(...)`.

### Exercise 2: Customizing the Map View
Create a 3D view of a city of your choice: an appropriate `zoom`, `pitch`, and `bearing`, using the `liberty` style. If you have a MapTiler key, try swapping in `3d-satellite` or `3d-topo` MapTiler styles and compare.

### Exercise 3: Map Controls
Add a `GeolocateControl` (top-left), a `FullscreenControl` (top-right), and a Draw control configured for points, lines, and polygons (top-left) to a map of your choice. Try enabling one of the extra Terra Draw modes (`rectangle`, `circle`, or `freehand`) and log `draw.getAll()` to the console after drawing something.

### Exercise 4: Markers, Lines & Polygons
Add a default `Marker` at a location of your choice. Then, using `addSource`/`addLayer`, draw a `LineString` representing a route you know well and a `Polygon` representing a boundary (a park, campus, or neighborhood). Style the line and polygon with colors and opacity that make sense together.

### Exercise 5: Adding geometries
Add any data set from the local geojson examples or ArcGIS online feature services to a map of your choice. For ArcGIS online, practice setting a custom query with an appropraite `where` clause to customize the data request of your map.

### Exercise 6: Popups & Legend
Build a small GeoJSON `FeatureCollection` of 4-5 points of your choice, and wire up click-popups for them following the Part 6 pattern (including the cursor change on hover). Then add an HTML/CSS legend, like the one in the Quick Recap example, explaining what the points represent.

### Exercise 7: Raster & WMS Overlay
Add a buildings layer and a roads layer for a New Mexico town of your choice to a map with sensible styling. Use your own extract from RGIS or OpenStreetMap if you have one ready. If not, these two ready-made GeoJSON files work as a stand-in while you get your own data sorted:
- Buildings: `https://github.com/opengeos/datasets/releases/download/places/nyc_buildings.geojson`
- Roads: `https://github.com/opengeos/datasets/releases/download/places/nyc_roads.geojson`

Then, separately, add the USGS Topo XYZ raster layer from Part 7 to a satellite-style basemap, with a layer-visibility toggle control.

### Exercise 8: 3D Buildings
Build a 3D map centered on a city of your choice, and add extruded building footprints (Part 8) using a custom color gradient tied to building height.

### Exercise 9: Camera Control & 3D Terrain
Using the Part 9 techniques, build a map that `fitBounds` to a region of your choice on load, then add a button that `flyTo`s a specific landmark within that region. Separately, adapt the 3D terrain example to your own region of interest, and add a `TerrainControl` so users can toggle the exaggeration.


## Readings & Resources

- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 14: MapLibre GL JS
- [MapLibre GL JS Documentation](https://maplibre.org/maplibre-gl-js/docs/)
- [MapLibre GL JS Examples Gallery](https://maplibre.org/maplibre-gl-js/docs/examples/)
- [OpenFreeMap](https://openfreemap.org): free, no-API-key vector basemap styles
- [Mapbox Style Specification](https://maplibre.org/maplibre-style-spec/): the reference for every `paint`/`layout` property and expression used above (MapLibre's style spec is a fork of this one)


## Appendix A: Clustering Points

Clustering is a property of the **source**, and it's drawn with three separate layers: clusters, cluster counts, and unclustered points. The example below uses earthquake epicenters across the U.S. (see [Maplibre Tutorial](https://maplibre.org/maplibre-gl-js/docs/examples/create-and-style-clusters/))

```js
const map = new maplibregl.Map({
    container: 'map',
    style: 'https://demotiles.maplibre.org/style.json',
    center: [-103.59179687498357, 40.66995747013945],
    zoom: 3,
    fadeDuration: 0 // this is in order for the text and circles to move "as-one"
});

map.on('load', () => {
    // Add a new source from our GeoJSON data and
    // set the 'cluster' option to true. GL-JS will
    // add the point_count property to your source data.
    map.addSource('earthquakes', {
        type: 'geojson',
        // Point to GeoJSON data. This example visualizes all M1.0+ earthquakes
        // from 12/22/15 to 1/21/16 as logged by USGS' Earthquake hazards program.
        data: 'https://maplibre.org/maplibre-gl-js/docs/assets/earthquakes.geojson',
        cluster: true,
        clusterMaxZoom: 14, // Max zoom to cluster points on
        clusterRadius: 50 // Radius of each cluster when clustering points (defaults to 50)
    });

    map.addLayer({
        id: 'clusters',
        type: 'circle',
        source: 'earthquakes',
        filter: ['has', 'point_count'],
        paint: {
            // Use step expressions (https://maplibre.org/maplibre-style-spec/#expressions-step)
            // with three steps to implement three types of circles:
            //   * Blue, 20px circles when point count is less than 100
            //   * Yellow, 30px circles when point count is between 100 and 750
            //   * Pink, 40px circles when point count is greater than or equal to 750
            'circle-color': [
                'step',
                ['get', 'point_count'],
                '#51bbd6',
                100,
                '#f1f075',
                750,
                '#f28cb1'
            ],
            'circle-radius': [
                'step',
                ['get', 'point_count'],
                20,
                100,
                30,
                750,
                40
            ]
        }
    });

    map.addLayer({
        id: 'cluster-count',
        type: 'symbol',
        source: 'earthquakes',
        filter: ['has', 'point_count'],
        layout: {
            'text-field': '{point_count_abbreviated}',
            'text-font': ['Noto Sans Regular'],
            'text-size': 12
        }
    });

    map.addLayer({
        id: 'unclustered-point',
        type: 'circle',
        source: 'earthquakes',
        filter: ['!', ['has', 'point_count']],
        paint: {
            'circle-color': '#11b4da',
            'circle-radius': 4,
            'circle-stroke-width': 1,
            'circle-stroke-color': '#fff'
        }
    });

    // inspect a cluster on click
    map.on('click', 'clusters', async (e) => {
        const features = map.queryRenderedFeatures(e.point, {
            layers: ['clusters']
        });
        const clusterId = features[0].properties.cluster_id;
        const zoom = await map.getSource('earthquakes').getClusterExpansionZoom(clusterId);
        map.easeTo({
            center: features[0].geometry.coordinates,
            zoom
        });
    });

    // When a click event occurs on a feature in
    // the unclustered-point layer, open a popup at
    // the location of the feature, with
    // description HTML from its properties.
    map.on('click', 'unclustered-point', (e) => {
        const coordinates = e.features[0].geometry.coordinates.slice();
        const mag = e.features[0].properties.mag;
        let tsunami;

        if (e.features[0].properties.tsunami === 1) {
            tsunami = 'yes';
        } else {
            tsunami = 'no';
        }

        // Ensure that if the map is zoomed out such that
        // multiple copies of the feature are visible, the
        // popup appears over the copy being pointed to.
        while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
            coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
        }

        new maplibregl.Popup()
            .setLngLat(coordinates)
            .setHTML(
                `magnitude: ${mag}<br>Was there a tsunami?: ${tsunami}`
            )
            .addTo(map);
    });

    map.on('mouseenter', 'clusters', () => {
        map.getCanvas().style.cursor = 'pointer';
    });
    map.on('mouseleave', 'clusters', () => {
        map.getCanvas().style.cursor = '';
    });
});
```

The `['step', ...]` expression buckets clusters by point count and colors/sizes them accordingly.

