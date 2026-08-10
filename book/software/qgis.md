# QGIS

[QGIS](https://qgis.org/) is a free and open-source desktop Geographic Information System (GIS) that runs on Windows, macOS, and Linux. In this course, QGIS is used to prepare, inspect, edit, and export the geographic data (shapefiles, GeoJSON, CSVs, raster tiles, etc.) that you will later publish and style in your web maps.

## Installation

**Download:** [https://qgis.org/download/](https://qgis.org/download/)

Install the **Long Term Release (LTR)** version unless an assignment specifically asks you to use the latest release. The LTR is more stable and better documented, which matters when you're troubleshooting a lab on your own.

- **Windows:** Download the standalone installer (OSGeo4W network installer also works, but the standalone installer is simpler for most students).
- **macOS:** Download the `.dmg` for your chip (Apple Silicon or Intel). You may also need to install the separate "QGIS" and "GDAL" Framework packages linked on the download page — follow the on-page instructions in order.
- **Linux:** Use your distribution's QGIS repository (see the download page for Ubuntu/Debian, Fedora, and Arch instructions) rather than a generic package manager version, to ensure you get a current build.

## Why We Use It in a Web Mapping Course

Even though this is a Web GIS course, most real-world web mapping projects start with data that needs cleanup before it belongs on the open web. QGIS is where you will:

- Inspect and clean vector data (shapefiles, GeoPackages, GeoJSON)
- Reproject data into the coordinate systems web maps expect (typically **EPSG:4326** or **EPSG:3857**)
- Simplify complex geometries so they load quickly in a browser
- Join tabular/attribute data (CSV) to spatial features
- Export data into web-friendly formats: **GeoJSON**, **GeoPackage**, **CSV**, **PMTiles**, and **COG** (Cloud-Optimized GeoTIFF)
- Symbolize and preview data before deciding how to style it in Leaflet or MapLibre GL JS

## Key Panels and Tools

- **Layers Panel:** Manage and reorder the data layers in your project
- **Browser Panel:** Navigate your file system and connect to remote data sources (including WMS/WFS/WMTS services, relevant to our GeoServer labs)
- **Processing Toolbox** (`Ctrl+Alt+T`): Access hundreds of geoprocessing tools (clip, buffer, reproject, simplify, dissolve, etc.)
- **Attribute Table:** View and edit tabular data attached to features
- **Layer Styling Panel:** Symbolize layers before export or screenshot reference

## Exporting Data for the Web

`Right-click layer → Export → Save Features As...`

- Format: **GeoJSON**
- CRS: **EPSG:4326 – WGS 84** (the standard for Leaflet/MapLibre)
- Consider enabling **Coordinate Precision** limits to keep file sizes small for web delivery

## Useful Plugins

- **QuickMapServices** – adds basemaps for reference while editing
- **QGIS2Web** – exports a QGIS project as a basic Leaflet or OpenLayers web map (useful for prototyping, not a substitute for hand-coding your labs)

## Verify Your Setup

- [ ] QGIS opens without an error and shows a blank project
- [ ] `Layer → Add Layer → Add Vector Layer` lets you browse to and preview a file (any shapefile or GeoJSON works as a test)
- [ ] The **Processing Toolbox** opens with `Ctrl+Alt+T` and lists tools when you search "buffer"

If QGIS won't open on macOS, double-check that you installed the separate GDAL Framework package linked on the download page — this is the most common install issue on Mac.

## References

- [QGIS Documentation](https://docs.qgis.org/)
- [QGIS Training Manual](https://docs.qgis.org/latest/en/docs/training_manual/index.html)
