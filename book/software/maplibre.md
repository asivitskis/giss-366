# MapLibre GL JS

[MapLibre GL JS](https://maplibre.org/) is an open-source JavaScript library for building interactive maps using **vector tiles** and GPU-accelerated rendering. It's a community-driven fork of Mapbox GL JS (created after Mapbox moved to a proprietary license), and it has become a leading open-source choice for modern, high-performance, vector-based web mapping.

## No Installation Required

Like Leaflet, MapLibre GL JS is a **JavaScript library**, not a desktop application. You include it in your web mapping project via a CDN or npm — you don't "install" it onto your computer.

### Option 1: CDN (recommended for this course)

Add the following to the `<head>` of your HTML file:

```html
<script src="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.js"></script>
<link href="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.css" rel="stylesheet" />
```

This works well for the HTML/CSS/JavaScript labs in this course and can be previewed locally with the **Live Server** extension in VS Code (see the [Visual Studio Code](vscode) page).

### Option 2: npm (for more advanced/bundled projects)

```bash
npm install maplibre-gl
```

```javascript
import maplibregl from 'maplibre-gl';
import 'maplibre-gl/dist/maplibre-gl.css';
```

## Minimal Example

```html
<div id="map" style="height: 400px;"></div>
<script>
  const map = new maplibregl.Map({
    container: 'map',
    style: 'https://demotiles.maplibre.org/style.json', // free demo vector style
    center: [-108.28, 32.7], // Silver City, NM
    zoom: 12
  });

  new maplibregl.Marker()
    .setLngLat([-108.28, 32.7])
    .setPopup(new maplibregl.Popup().setText('Western New Mexico University'))
    .addTo(map);
</script>
```

## MapLibre vs. Leaflet

| | Leaflet | MapLibre GL JS |
| :---- | :---- | :---- |
| Rendering | DOM/Canvas, raster tiles by default | WebGL (GPU-accelerated), vector tiles by default |
| Best for | Simple, lightweight maps and quick prototypes | Smooth zoom/rotation, large datasets, custom vector styling |
| Learning curve | Gentler | Steeper, but more powerful |
| Data format | GeoJSON, raster tile layers | Vector tiles (MVT), GeoJSON sources, style specification (JSON) |

We introduce both libraries so you can make an informed choice of tool based on a project's needs — part of the course's broader goal of treating open-source and proprietary/alternative stacks as parallel, legitimate options.

## What We'll Use It For

- Exploring **vector tiles** and modern cloud-native formats (PMTiles, COG) later in the course
- Custom map styling beyond what raster tile basemaps allow
- Building more advanced, performant interactive maps for your final project if your project calls for it

## References

- [MapLibre GL JS Documentation](https://maplibre.org/maplibre-gl-js/docs/)
- [MapLibre Style Specification](https://maplibre.org/maplibre-style-spec/)
- [MapLibre GitHub Repository](https://github.com/maplibre/maplibre-gl-js)
