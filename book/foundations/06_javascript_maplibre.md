# MapLibre GL JS


## Learning Objectives

By the end of this unit, you will be able to:

- Explain what MapLibre GL JS adds over Leaflet (vector tiles, WebGL rendering, 3D), and build a complete interactive map in a single CDN-loaded HTML file
- Initialize a map with a chosen center, zoom, pitch, bearing, and basemap style, and add navigation, geolocate, fullscreen, and draw controls
- Add GeoJSON, clustered, raster, and WMS layers using `addSource`/`addLayer`, styled with data-driven expressions (`interpolate`, `step`, `get`)
- Add markers and popups, and build a simple HTML/CSS legend, since MapLibre GL JS has no built-in legend widget
- Build 3D extrusions and terrain, and control the camera with `fitBounds`, `flyTo`, and `maxBounds`

## Notebook Overview: From Static Basemaps to Interactive, Data-Driven Web Maps

So far your maps have been built with Leaflet, loaded from local `js/` and `css/` folders alongside a `map.js` file. This unit introduces **MapLibre GL JS**, a different mapping engine with native support for vector tiles and 3D. We'll build every example as a **single self-contained HTML file**, loading MapLibre straight from a CDN, so you can go from a blank file to a working interactive map without setting up a project folder at all.

*This lecture was developed with reference to the open-source MapLibre lecture notebook from Dr. Qiusheng Wu's [geog-510](https://github.com/giswqs/geog-510) course materials, adapted here for MapLibre GL JS.*

## Part 1: Why MapLibre GL JS?

Leaflet renders vector graphics as SVG or Canvas, both are strictly 2D, and both treat "layers" as pre-rendered image tiles or drawn shapes. MapLibre GL JS instead renders everything through **WebGL**, talking directly to your computer's GPU. Two consequences follow directly from that:

1. **Vector tiles are native.** Instead of downloading pre-drawn PNG tiles, MapLibre downloads compact binary tiles full of geometry and attributes, and styles them live, in the browser. That's why changing a fill color in MapLibre is instant, no new tiles to generate, unlike the raster tile workflows you've already used with Leaflet.
2. **3D is possible.** Buildings can be extruded, terrain can be given real elevation, and the camera can pitch and rotate, none of which Leaflet can do.


## Part 2: Basic MapLibre Map with CDN

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


## Part 4: Adding Markers & Geometries

Similar to Leaflet, MapLibre is able to add and draw geometries. Check out the Part 4 example to modify and explore these options. Remember to update your map center and Zoom accordingly for each new addition. 

### Default Marker

```js
const marker = new maplibregl.Marker()
    .setLngLat([-108.2833, 32.7764])
    .addTo(map);

```

For more complex geometries, MapLibre always separates where the data comes from (a source) from how it’s drawn (a layer). You add both inside the `'load'` listener, right alongside your controls.

### Lines

```js
map.on('load', () => {
    map.addSource('route', {
        'type': 'geojson',
        'data': {
            'type': 'Feature',
            'properties': {},
            'geometry': {
                'type': 'LineString',
                'coordinates': [
                    [-108.2750622, 32.7796674],
                    [-108.272518, 32.7823097],
                    [-108.270348, 32.7830646],
                    [-108.266831, 32.7831904],
                    [-108.2612936, 32.7851407],
                    [-108.2548583, 32.7871537],
                    [-108.2487223, 32.7874683],
                    [-108.2382965, 32.7864721],
                    [-108.2046233, 32.783012],
                    [-108.1890922, 32.7805237],
                    [-108.1822079, 32.7798317],
                    [-108.1756229, 32.7793913],
                    [-108.1696366, 32.7804608],
                    [-108.1594598, 32.7822223],
                    [-108.1510789, 32.7820965],
                    [-108.1481606, 32.7786992],
                    [-108.1479361, 32.7736659],
                    [-108.1483102, 32.7706458],
                    [-108.1478887, 32.7662977],
                    [-108.1448956, 32.7598794],
                    [-108.143399, 32.7585579],
                    [-108.1397323, 32.7578028],
                    [-108.1377868, 32.758432],
                    [-108.1349433, 32.7590613],
                    [-108.1314263, 32.7590613]
                ]
            }
        }
    });
    map.addLayer({
        'id': 'route',
        'type': 'line',
        'source': 'route',
        'layout': {
            'line-join': 'round',
            'line-cap': 'round'
        },
        'paint': {
            'line-color': '#888',
            'line-width': 8
        }
    });
});
```

### Polygons

```js
map.on('load', () => {
    map.addSource('maine', {
        'type': 'geojson',
        'data': {
            'type': 'Feature',
            'geometry': {
                'type': 'Polygon',
                'coordinates': [
                    [
                        [-108.2853603, 32.77102],
                        [-108.2982235, 32.7688568],
                        [-108.3009621, 32.76132],
                        [-108.294323, 32.7538525],
                        [-108.2866051, 32.7528754],
                        [-108.2824557, 32.7575514],
                        [-108.280381, 32.7606222],
                        [-108.2810449, 32.7701128],
                        [-108.2853603, 32.77102]
                    ]
                ]
            }
        }
    });
    map.addLayer({
        'id': 'maine',
        'type': 'fill',
        'source': 'maine',
        'layout': {},
        'paint': {
            'fill-color': '#088',
            'fill-opacity': 0.8
        }
    });
});
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


## Part 6: Popups

### Pop-Ups

Simple pop-ups can be added like default markers.
```js
const popup = new maplibregl.Popup({closeOnClick: false})
    .setLngLat([-96, 37.8])
    .setHTML('<h1>Hello World!</h1>')
    .addTo(map);
```

### Popups on click

Custom popups on click require additional event listeners. (See [MapLibre Popup Example](https://maplibre.org/maplibre-gl-js/docs/examples/display-a-popup-on-click/))
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
                        name: 'Point 1'
                    },
                    geometry: {
                        type: 'Point',
                        coordinates: [-108.2812252, 32.7746687]
                    }
                },
                {
                    type: 'Feature',
                    properties: {
                        name: 'Point 2'
                    },
                    geometry: {
                        type: 'Point',
                        coordinates: [-107.7564321, 32.2613517]
                    }
                },
                {
                    type: 'Feature',
                    properties: {
                        name: 'Point 3'
                    },
                    geometry: {
                        type: 'Point',
                        coordinates: [-107.1580646, 32.6698278]
                    }
                },
                {
                    type: 'Feature',
                    properties: {
                        name: 'Point 4'
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
            .setHTML(`<strong>${name}</strong>`)
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


## Part 7: Raster and WMS layers

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


## Part 8: 3D Extrusions

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


## Part 9: Camera & Interaction Control; 3D Terrain

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
                    'fill-opacity': 0.8
                }
            });

            map.on('click', 'nm_counties_layer', function (e) {
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

### Exercise 5: Clustering Points
Adapt the earthquake clustering example to a dataset of your choice (or keep the earthquake data, but change the `clusterRadius`/`clusterMaxZoom` values and the `step` color/radius breakpoints). Confirm that clicking a cluster zooms in appropriately, and that clicking an individual point still opens a popup.

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

