# Lab 0
# Practice Lab - Getting Started with GIS
### GISS/GEOG 361/363 · Introduction to Geographic Information Science

**Unit 1 Focus:** What is GIS? GISystems vs. GIScience · Open source vs. proprietary software · Modern GIS architecture

*This is a practice lab — ungraded. The goal is to get comfortable navigating GIS software before Week 2, when your first graded lab and your Place-Based Geospatial Inquiry Project both begin.*

---

## Where We're Working: Silver City & Southwestern New Mexico

Throughout the semester, GISS/GEOG 361/363 will use the WNMU home region — Silver City, Grant County, and the Gila National Forest — as a shared "practice place" for labs, before you choose your own place for your Place-Based Geospatial Inquiry Project starting in Week 2. Working with one real, richly documented region lets everyone compare notes on the *same* landscape while learning the tools.

<!-- Optional: add a local photo here, e.g. <img src="images/week1_hero.jpg" alt="Gila National Forest near Silver City, NM" width="650px"> -->

## Learning Goals

By the end of this practice lab, you will be able to:
- Explain the difference between GISystems (the software/hardware) and GIScience (the discipline) *(K01)*
- Open, navigate, and add a basemap and a data layer in **GeoLibre** (web), and complete a full mapping workflow in **one desktop platform of your choice** — QGIS (open-source) or ArcGIS Pro (proprietary) *(S01, S10)*
- Compare the experience of a browser-based tool and a desktop tool, and start forming your own view on open-source vs. proprietary trade-offs *(EQ1, A01)*
- Locate Silver City, NM and the Gila region in each platform, and produce one labeled map

## How This Notebook Is Organized: the Geo-Inquiry Process

This course structures every lab around the **Geo-Inquiry Process** (National Geographic Education): **Ask → Collect → Visualize → Create → Act.** It's the same five-step structure you'll see all semester, so getting familiar with it now — on something as low-stakes as "open some software" — pays off later when the stakes (and the data) get bigger.

## A Note on the Platforms and Tracks

Every step below opens in **GeoLibre** — a free, browser-based tool with nothing to install, so everyone starts from the same place. After that, the lab splits into two **tracks**, and you'll pick **one** and stay in it through Visualize and Create:

- 🗺️ **Track Q — QGIS** (free, open-source, install required)
- 💼 **Track A — ArcGIS Pro** (provided via WNMU license, Windows or virtual desktop)

One full track is a complete, real GIS workflow, and that's the point. Choosing a tool based on what you have access to, rather than which one is "the real GIS," is exactly the habit this course wants you to build. If you have access to both platforms and you're curious, running the second track afterward is a great *optional* way to build the comparison the reflection questions ask about.

## Before You Begin

| Platform | Cost / Access | Install? | Role in this lab |
|---|---|---|---|
| **GeoLibre** | Free, no account | None — runs in your browser (desktop/mobile apps also available) | Everyone's starting point (Collect) |
| **QGIS** *(Track Q)* | Free, open-source | Download & install ([qgis.org](https://qgis.org/download/)) | Full workflow, works on any OS |
| **ArcGIS Pro** *(Track A)* | Provided via WNMU license | Windows only — install locally, or use the campus lab / virtual desktop | Full workflow, industry-standard |

**Choosing a track:** Track Q (QGIS) works for every student, on any computer, at no cost — pick it if you're not sure yet. Track A (ArcGIS Pro) needs a Windows machine or the campus lab/virtual desktop; pick it if that access is already set up for you. Neither track is the "backup" option — the syllabus treats them as equally real, equally supported GIS.

**Links**
- GeoLibre (web app): https://web.geolibre.app/ *(or the live viewer at https://viewer.geolibre.app/)*
- GeoLibre docs & tutorials: https://geolibre.app/
- QGIS download: https://qgis.org/download/
- ArcGIS Pro / ArcGIS Online: access details posted in the course LMS (Week 1 announcement)
- NM RGIS Clearinghouse (our regional data source all semester): https://rgis.unm.edu/

If you're running this notebook locally rather than in a hosted environment (Colab / Binder / JupyterHub), the only Python package this *specific* notebook needs is optional. If you are curious about the small amount of Python behind a few of the steps below, it is collected in an optional appendix at the very end of this notebook, so it will not get in your way here.

## Step 1: Ask

Before opening any software, sit with a couple of questions.

> **What is GIS, really?** A *Geographic Information **System*** is the software/hardware you click through — QGIS, ArcGIS Pro, GeoLibre. **Geographic Information *Science*** is the broader discipline underneath it: the ideas about space, data, and representation that stay true no matter which system you're using *(K01, EU1)*.

> **EQ1 — What does this technology let us do, where are its limits, and who is excluded when using it costs money?**

Take a minute and jot down your first-pass answers (edit the cell below):
- What do you already associate with "GIS" or "mapping software," even if you've never used it?
- If a powerful GIS tool costs several thousand dollars a year to license, who does — and doesn't — get to use it?
- Southwestern New Mexico (Grant County, the Gila region) is a real place with real people. What might a first-time visitor's map of this region get wrong, or leave out?

We'll come back to that last question all semester — it's the seed of the GeoEthics strand of this course.

**Your answers (double-click this cell to edit):**

-
-
-

---

## Step 2️: Collect

"Collecting" today means: get oriented, open a basemap, and load one ready-made data layer — all inside GeoLibre, before you branch into your track. No downloads to your own computer yet — that's Week 4's lab (K04, S03).

<details>
<summary><b>GeoLibre (Web) — click to expand</b></summary>

1. Open **https://web.geolibre.app/** in a new browser tab (no login needed).
2. When prompted, choose the **Beginner** interface — you can change this later in Settings → Interface.
3. Use the **basemap** control to switch between a street map and satellite/aerial imagery.
4. Use the search box (or pan/zoom manually) to navigate to **Silver City, New Mexico**.
5. Use **Add Data → URL** (or drag-and-drop) to load this public, cloud-native world-countries layer — nothing to download first, GeoLibre streams it directly:
   `https://data.source.coop/giswqs/opengeos/countries.parquet`
6. Zoom from the **United States**, to **New Mexico**, to **Grant County / Silver City**. Notice that the level of visual detail available doesn't really change — you're just choosing how far to zoom.
7. Use the **Identify** tool on the United States polygon to see what attribute data comes attached to it.

</details>

*Optional:* the code cell below embeds the same web app right in this notebook, so you can try it without leaving the page.


```python
# OPTIONAL — view the GeoLibre web app right inside this notebook.
# You can also just open https://web.geolibre.app/ in a normal browser tab — that always works,
# and is the more reliable option if your notebook environment blocks embedded pages.

from IPython.display import IFrame

IFrame(src="https://web.geolibre.app/", width="100%", height=600)
```

> If the embedded map above doesn't load (some hosted notebook environments block embedded pages), that's normal — just open **https://web.geolibre.app/** directly in a browser tab instead.

**Save a screenshot now** — your GeoLibre view of Silver City with the countries layer loaded. Save it somewhere you'll find it again and label it *Screenshot 1*; you'll turn it in during the Act step.

---

## 🔀 Checkpoint: Choose Your Track

From here, pick **Track Q (QGIS)** or **Track A (ArcGIS Pro)** and stay in that track through both Visualize and Create. Expand only the track you're using — you can leave the other one collapsed and come back to it later if you want the optional comparison.

> **Not sure which to pick?** Track Q works for everyone, everywhere, for free — a safe default. Track A is a great pick if ArcGIS Pro access is already set up for you through the campus lab, a virtual desktop, or your own Windows machine.

## Step 3: Visualize

Now let's load a *real, curated* dataset and see what your track's platform brings to the table.

<details>
<summary><b>Track Q — QGIS: click to expand</b></summary>

1. Open QGIS and start a **New Project**. Add a basemap using **XYZ Tiles** (built in) or install the **QuickMapServices** plugin (Plugins → Manage and Install Plugins) for one-click satellite/street basemaps.
2. Check the **CRS indicator** in the bottom-right status bar to see what coordinate system your project is in (we'll dig into this in Week 3).
3. Zoom from the whole country down to Grant County, NM, the same way you did in GeoLibre.
4. **Signature feature:** use **Layer → Add Layer → Add ArcGIS Feature Server Layer**, and search the connection dialog for a public wildfire or forest-related service (try the keyword "wildfire" or "forest"). Add one layer near the Gila region. This works because many federal and state agencies publish open data through Esri's map-service format — QGIS can read it even though it isn't Esri software. That's a small, real example of *open standards* letting different tools share the same data.
5. Open the new layer's **attribute table** (right-click the layer → Open Attribute Table) to see what information came attached to it.

</details>

<details>
<summary><b>Track A — ArcGIS Pro: click to expand</b></summary>

1. Open ArcGIS Pro and start a **New Map** project. In the **Map** tab, use **Basemap** to add a Topographic or World Imagery basemap (streamed from ArcGIS Online).
2. Zoom from the country level down to Grant County, NM.
3. **Signature feature:** in the **Catalog** pane, open **Living Atlas** and search for a wildfire-risk or forest-type layer relevant to the Gila region. Add it to your map — Living Atlas is a curated library of ready-made layers that ships with ArcGIS Online, one of the things a license gets you.
4. Lower the layer's transparency partway so the basemap still shows through, then use the **Explore/Identify** tool to click a feature and see its attributes.

</details>

📸 **Save a screenshot now** — your track's map with the new layer visible. Label it *Screenshot 2*.


## Step 4: Create

Time to make something, however small — one labeled marker at Western New Mexico University / downtown Silver City, in the same track you just used.

<details>
<summary><b>Track Q — QGIS: click to expand</b></summary>

1. Create a new point layer (**Layer → Create Layer → New Shapefile/GeoPackage Layer**) and digitize a single point at WNMU / downtown Silver City.
2. Label it (e.g., "WNMU — GISS 361"), then open **Project → New Print Layout** to lay out and export a simple PNG.

</details>

<details>
<summary><b>Track A — ArcGIS Pro: click to expand</b></summary>

1. Add a point via **Edit → Create Features**, placing a marker at WNMU / downtown Silver City.
2. Label it, then use **Share → Export Map** (or build a **Layout**) to export a simple image.

</details>

📸 **Save a screenshot now** — your labeled marker map. Label it *Screenshot 3* — this is the main image you'll turn in for Act.

## Step 5: Act

The "Act" step is about sharing what you made and reflecting on what it means — not just producing an image and moving on.

### What to turn in (practice — ungraded)

1. Your three screenshots:
   - **Screenshot 1** — GeoLibre, oriented on Silver City with the countries layer loaded (Collect).
   - **Screenshot 2** — your track's map with its new layer visible (Visualize).
   - **Screenshot 3** — your track's labeled marker map (Create).
2. A short written reflection (3–5 sentences) answering:
   - Which felt more intuitive: the zero-install browser tool (GeoLibre) or your desktop track (QGIS or ArcGIS Pro)? Why?
   - What could your desktop track do that GeoLibre couldn't — or the other way around?
   - Revisit **EQ1**: GeoLibre and QGIS cost nothing to use; ArcGIS Pro is licensed through WNMU. Now that you've worked hands-on with at least one free tool and (maybe) one licensed one, has your answer changed about who gets left out when a GIS tool costs money? If you only had access to the free tools all semester, would you still be able to do real GIS work?
   - *(Optional, if you tried the second track too)* What was one thing that felt easier in the track you didn't use for Create?

Post your screenshots + reflection to this week's discussion space in Canvas. This is a practice activity, not graded — but it sets you up for **Lab 1** next week.

### Looking Ahead

Next week you'll start **Project Topic Ideation A** — the first low-stakes checkpoint toward your semester-long Place-Based Geospatial Inquiry Project. Start thinking now about a place that matters to you (it doesn't have to be Silver City!) and a question about it you're genuinely curious about.

---
### 📎 Resources

- GeoLibre — https://geolibre.app/ (docs & tutorials) · https://web.geolibre.app/ (live app) · https://github.com/opengeos/GeoLibre
- QGIS — https://qgis.org/
- ArcGIS Pro / Online — see course LMS for WNMU access details
- NM RGIS Clearinghouse — https://rgis.unm.edu/
- `leafmap` (Python) — https://leafmap.org/

---
## Appendix: Python Code (Optional)

Everything above this line is all you need for this practice lab. The cells below are optional, and their code is collapsed by default. They show the small amount of Python running quietly behind a few of the steps above, in case you are curious.

Nothing here is required, and nothing here is graded. You are welcome to open and run these cells, skip them entirely, or come back to them later in the semester once we start writing Python together in class.


```python
# OPTIONAL -- environment check
# This notebook is mostly a guided walkthrough of software you'll open outside of Jupyter
# (QGIS, ArcGIS Pro, and GeoLibre's web app). The only Python package used later in this
# notebook is `leafmap`. If a later cell errors on `import leafmap`, uncomment and run:
# !pip install leafmap -q

import sys
print(f"Python {sys.version.split()[0]} ready.")
```

> If the embedded map above doesn't load (some hosted notebook environments block embedded pages), that's normal — just open **https://web.geolibre.app/** directly in a browser tab instead.

### Behind Step 3 (Visualize): A First Look with leafmap

Later in the semester you will build maps with Python directly. This cell is just a preview: it draws a simple map centered on Silver City using `leafmap`, an open-source Python mapping library we will use again later in the course.


```python
# OPTIONAL -- a first taste of Python-based mapping (we'll do much more with this later in the semester)
# If leafmap isn't installed, uncomment the line below:
# !pip install leafmap -q

import leafmap

silver_city = (32.7701, -108.2803)  # (latitude, longitude)

m = leafmap.Map(center=silver_city, zoom=11)
m.add_basemap("Esri.WorldImagery")
m
```

### Behind Step 4 (Create): Adding a Marker and Saving a Map

This is the same idea as Step 4 above -- marking WNMU / downtown Silver City and saving an image -- done here in a few lines of Python instead of by hand in GeoLibre, QGIS, or ArcGIS Pro.


```python
# OPTIONAL -- add a labeled marker and save a shareable HTML map
m.add_marker(location=silver_city, popup="Western New Mexico University -- GISS 361/363")
m.to_html("week1_first_map.html")
print("Saved week1_first_map.html -- open it in a browser to see your first exported map.")
```
