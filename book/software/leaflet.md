# Leaflet

[Leaflet](https://leafletjs.com/) is a lightweight, open-source JavaScript library for building interactive web maps. It's one of the most widely used libraries in web cartography because it's small, fast, mobile-friendly, and has a simple, well-documented API.

## No Installation Required

Leaflet is a **JavaScript library**, not a desktop application — you don't download and install it the way you install VS Code or QGIS. Instead, you include it directly in your HTML/JavaScript project, either by linking to a Content Delivery Network (CDN) or by installing it as an npm package if you're using a build tool.

### Option 1: CDN (recommended for this course)

Add the following to the `<head>` of your HTML file:

```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
```

This is the simplest approach for the HTML/CSS/JavaScript labs in this course and requires no build process — just open your file with the **Live Server** extension in VS Code (see the [Visual Studio Code](vscode) page).

### Option 2: npm (for more advanced/bundled projects)

```bash
npm install leaflet
```

```javascript
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
```

## Minimal Example

```html
<div id="map" style="height: 400px;"></div>
<script>
  const map = L.map('map').setView([32.7, -108.28], 13); // Silver City, NM

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors'
  }).addTo(map);

  L.marker([32.7, -108.28]).addTo(map)
    .bindPopup('Western New Mexico University');
</script>
```

## What We'll Use It For

- Building your first interactive web maps (Lab 3–4)
- Loading and styling **GeoJSON** data exported from QGIS
- Adding markers, popups, layers, and basic interactivity
- Comparing an open-source library workflow against the proprietary ArcGIS Online pathway

## Core Concepts to Know

| Concept | What it does |
| :---- | :---- |
| `L.map()` | Initializes the map and attaches it to a `<div>` |
| `L.tileLayer()` | Adds a basemap (raster tiles) |
| `L.marker()` / `L.circleMarker()` | Adds point features |
| `L.geoJSON()` | Loads and styles GeoJSON data |
| `.bindPopup()` | Attaches an interactive popup to a feature |
| `.addTo(map)` | Adds any layer or feature to the map |

## References

- [Leaflet Documentation](https://leafletjs.com/reference.html)
- [Leaflet Tutorials](https://leafletjs.com/examples.html)
- [Leaflet GitHub Repository](https://github.com/Leaflet/Leaflet)
