# QGIS

[QGIS](https://qgis.org/) is a free, open-source desktop GIS application. In this course you'll use it to prepare, inspect, edit, and export the geographic data (shapefiles, GeoJSON, CSVs) that feeds into your web maps.

Install the **Long Term Release (LTR)** unless you're told otherwise — it's more stable than the latest release, which matters more for coursework than having the newest features.

## Installation

We'll install QGIS through **OSGeo4W** rather than the standalone QGIS installer. OSGeo4W bundles QGIS together with **GDAL**, the geospatial data library our later scripting labs rely on, so getting both from the same installer keeps their versions in sync and avoids conflicts.

1. **Download the installer.** Go to [trac.osgeo.org/osgeo4w](https://trac.osgeo.org/osgeo4w/) and download `osgeo4w-setup.exe`. (macOS/Linux: see the note below.)
2. **Choose Advanced Install.** Run the installer and select **Advanced Install**, not Express — Express only installs a fixed package set and won't let you confirm GDAL is included. Choose **Install from Internet**, accept the default download directory, and pick a nearby download site when prompted.
3. **Select your packages.** In the package list, search for and set each of these to **Install** (click the version column until it shows a version number instead of "Skip"):

   | Package | Category | Why |
   | :---- | :---- | :---- |
   | `qgis-ltr` | Desktop | The Long Term Release of QGIS (use plain `qgis` only if told to use the latest release) |
   | `gdal` | Libs | Command-line GDAL/OGR tools, kept in sync with QGIS's version |
   | `python3-gdal` | Libs | Python bindings for GDAL — needed for later scripting labs |

   Selecting `qgis-ltr` will automatically pull in its own required dependencies — you don't need to hand-pick every library.
4. **Finish the install.** Click through to install. This downloads and installs everything — budget 10–20 minutes depending on your connection. Leave it running in the background.
5. **Verify GDAL from the OSGeo4W Shell.** From the Start Menu, open **OSGeo4W Shell** (not a regular command prompt — it sets up the environment paths GDAL needs) and run:

   ```
   gdalinfo --version
   ```

   You should see a version string like `GDAL 3.x.x`. If you instead see "command not found," the `gdal` package likely wasn't selected in Step 3 — rerun the installer and check it.
6. **Launch QGIS.** From the Start Menu, look for **QGIS Desktop** under the OSGeo4W folder (not a bare "QGIS" shortcut — the OSGeo4W version ensures it's using the GDAL you just installed).

> **macOS / Linux:** OSGeo4W is Windows-only. On a Mac, install QGIS from [qgis.org/download](https://qgis.org/download/) (the official macOS installer bundles GDAL already) or via Homebrew with `brew install qgis`. On Linux, install via your distribution's package manager or the [QGIS APT/DNF repositories](https://qgis.org/download/), which include GDAL as a dependency.

## Verify Your Setup

- QGIS opens and shows the standard interface
- `Layer → Add Layer → Add Vector Layer` successfully adds a sample vector layer
- `gdalinfo --version` returns a version string from the OSGeo4W Shell (Windows) or your terminal (Mac/Linux)
- The Processing Toolbox lists GDAL algorithms (`Settings → Options → Processing → Providers`, confirm GDAL is enabled)

> **Troubleshooting:** If QGIS opens but the Processing Toolbox is missing GDAL tools, check that GDAL is enabled under Processing Providers. If `gdalinfo` isn't recognized outside the OSGeo4W Shell, that's expected — regular Command Prompt / PowerShell don't have OSGeo4W's paths set up automatically.

## References

- [QGIS Documentation](https://docs.qgis.org/)
- [OSGeo4W Documentation](https://trac.osgeo.org/osgeo4w/)
- [GDAL Documentation](https://gdal.org/)
