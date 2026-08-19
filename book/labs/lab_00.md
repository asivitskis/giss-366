# Lab 0
# Environment Setup: ArcGIS Online & Open-Source Web GIS
## GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 1 Focus:** What is Web GIS? · Client-server architecture · Proprietary (ArcGIS Online) and open-source/cloud-native pathways, side by side

*This is a practice lab, ungraded. The goal is a working dual-stack environment before Lab 1's graded work begins next week.*

---

## Where We're Working: Silver City & Southwestern New Mexico

This course uses the WNMU home region including Silver City, Grant County, and the Gila National Forest as a shared reference point for early labs. You'll pick your own place and audience for the semester's Public Web GIS Inquiry starting in Week 11, but for now, one shared, real place lets everyone compare notes while getting the tools working.

## Learning Goals

By the end of this practice lab, you will be able to:
- Explain, in your own words, what happens between a browser and a server when a web map loads *(K01)*
- Sign in to WNMU's **ArcGIS Online** organizational account and confirm it works
- Set up the beginnings of an **open-source web GIS dev environment**: a GitHub account, VS Code, and a working local Python install that you'll build on all semester
- Use your browser's **Developer Tools** to watch a real web map make network requests, connecting today's lecture to something you can see for yourself
- Know your first move when you get stuck on setup: docs, then search the exact error, then the course discussion board *(K16, A01)*

## How This Notebook Is Organized: the Geo-Inquiry Process

Like GISS 361/363, every lab in this course follows the **Geo-Inquiry Process** (National Geographic Education): **Ask → Collect → Visualize → Create → Act.** You've likely seen this process before. Here it's applied to environment setup instead of a mapping question, about as low-stakes an application as it gets.

## A Note on "Tech Stacks"

This course treats proprietary (ArcGIS Online) and open-source/cloud-native (GitHub, static hosting, MapLibre/Leaflet) publishing as **parallel, equally real pathways** that you'll use *both* of, all semester. Learning both can position you well for future geospatial career applications. Today, you're setting up **both** environments, side by side. Some labs later on will let you choose which stack to go deeper on, but the infrastructure for both starts today.

## Before You Begin

| Tool | Cost / Access | Install? | Role this semester |
|---|---|---|---|
| **ArcGIS Online** | Provided via WNMU organizational account | None; runs in your browser | Proprietary pathway: Lab 2 onward |
| **GitHub** | Free account | None; web-based, plus a local `git` install later | Hosts your open-source pathway work and (eventually) your final project site |
| **VS Code** | Free | Download & install ([code.visualstudio.com](https://code.visualstudio.com/)) | Where you'll write HTML/CSS/JS starting Week 4 |
| **A modern browser with Dev Tools** | Free | Chrome recommended, Firefox fine | How you'll inspect every web map's network traffic all semester |
| **Python 3** | Free | Usually already on your machine, or via this notebook's host | Built-in local web server later; `leafmap`/`folium` for quick previews |

**Links**
- ArcGIS Online sign-in: access details are posted in the course LMS (Week 1 announcement). Request access this week if you don't have it yet.
- GitHub: https://github.com/join
- VS Code download: https://code.visualstudio.com/download
- Course content site (bookmark this): https://asivitskis.github.io/giss-366
- NM RGIS Clearinghouse (our regional data source all semester): https://rgis.unm.edu/

If you're running this notebook locally rather than in a hosted environment (Colab / Binder / JupyterHub), the only Python package this *specific* notebook needs is optional (`leafmap`). If you're curious about the small amount of Python behind the last couple of steps, it's collected in an optional appendix at the very end, so it won't get in your way here.

## Step 1: Ask

Before opening any software, sit with a couple of questions from our lecture.

> **The big idea:** every web map you'll ever use is a **client** (your browser) asking a **server** (or a file sitting in cloud storage) for something, and getting a response back. That loop, and who gets left out of it, is what this course keeps coming back to.

> **EQ1: What is actually happening between my browser and the server when this map loads, and who is excluded when the license, bandwidth, or device it requires isn't available to them?**

Take a minute and jot down your first-pass answers (edit the cell below):
- Before tonight, what did you picture happening "under the hood" when a web map loaded, if you pictured anything at all?
- ArcGIS Online requires an institutional license; GitHub Pages hosting is free to anyone with an account. What kind of person, project, or place might that cost difference actually determine: able to publish a web map, or not?
- Southwestern New Mexico (Grant County, the Gila region) is a real place with real people and, like any rural, resource-limited area, real gaps in broadband access. Who might a web map of this region simply fail to reach, no matter how well-designed it is?

We'll come back to that last question all semester. It's the seed of the Digital GeoEthics strand running through this course.

**Your answers (double-click this cell to edit):**

-
-
-

---

## Step 2: Collect

"Collecting" today means getting your accounts and access in place, nothing built yet.

<details>
<summary><b>Proprietary pathway: ArcGIS Online (click to expand)</b></summary>

1. Go to the ArcGIS Online sign-in link posted in the course LMS (Week 1 announcement).
2. Sign in with your WNMU credentials. If you don't have access yet, request it now. This is the single most common Lab 0 delay, so don't wait until Thursday.
3. Once signed in, find your **Content** page and your **Organization** page in the top navigation. You don't need to do anything with them yet, just confirm they load.
4. Check your profile settings for your **display name** and **organization role** (Viewer, Creator, etc.). Note your role below; it determines what you can publish later in the course.

</details>

<details>
<summary><b>Open-source pathway: GitHub (click to expand)</b></summary>

1. If you don't already have one, create a free account at https://github.com/join.
2. Once signed in, visit the course content site: https://asivitskis.github.io/giss-366. This is where lecture notebooks and lab instructions will live all semester (Canvas is for grading and submission only).
3. Confirm you can see your GitHub username in the top-right corner when signed in.

</details>

**Your ArcGIS Online organization role (double-click to edit):**

-

---

## Step 3: Visualize

Now let's make the client-server loop into something you can actually *see*, using your browser's built-in **Developer Tools**.

<details>
<summary><b>Watching a web map talk to a server (click to expand)</b></summary>

1. Open a web map in a new browser tab: either **your ArcGIS Online home page** (it loads a basemap by default) or a public example like https://earth.nullschool.net.
2. Open **Developer Tools**:
   - Chrome/Edge: right-click anywhere on the page → **Inspect**, or press `F12`
   - Firefox: right-click → **Inspect**, or press `F12`
3. Click the **Network** tab inside Dev Tools. Refresh the page.
4. Watch the list fill in. Pan or zoom the map and keep watching; you should see new requests appear for each new area you bring into view.
5. Click on a couple of individual requests. Look for:
   - The **request URL**: does it look like a request for a *tile* (a small square image or block of data), a style file, or something else?
   - The **response size**, usually a few KB per tile, not megabytes. This is exactly the "chop the world into small requestable pieces" idea from tonight's lecture.
   - The **type/format**: `png`/`jpg` (raster tile), or something like `pbf`/`mvt` (vector tile)?

This is the same request/response loop from tonight's diagram (`GET tile → response: a few KB of data`) happening in real time, dozens of times, every time you touch a web map.

</details>

📸 **Save a screenshot now** of your Network tab with at least a few tile requests visible. Label it *Screenshot 1*; you'll turn it in during Act.

**What you noticed (double-click to edit):**
- Request URL pattern:
- Raster or vector tiles (or both)?
- Roughly how big was one tile response?

---

## Step 4: Create

Time to make something small in each environment, just enough to prove both pathways are working.

<details>
<summary><b>Proprietary pathway: ArcGIS Online (click to expand)</b></summary>

1. From your ArcGIS Online home page, choose **Map** (or **Create → Map**) to open the Map Viewer.
2. Use the basemap gallery to pick any basemap you like. Topographic and Imagery are good starting points.
3. Use the search bar to navigate to **Silver City, New Mexico**.
4. Save the map (**Save → Save As**) with a title like `GISS 366 – Lab 0 – [Your Name]`. Saving is what confirms your account can actually create and store content, not just view it.

</details>

<details>
<summary><b>Open-source pathway: dev environment (click to expand)</b></summary>

1. Install **VS Code** if you haven't already: https://code.visualstudio.com/download.
2. Open VS Code and install the **Python** extension (Extensions icon in the sidebar → search "Python" → Install).
3. On GitHub, create a new repository. Name it something like `giss-366-labs`. You can make it public or private; either is fine for now. This will hold your work all semester.
4. Confirm Python 3 is available on your machine. The code cell below does this check for you if you're running this notebook locally.

</details>

📸 **Save a screenshot now** of your saved ArcGIS Online map of Silver City. Label it *Screenshot 2*; this is the main image you'll turn in for Act.

---


```python
# OPTIONAL: environment check
# This notebook is mostly a guided walkthrough of tools you'll open outside of Jupyter
# (ArcGIS Online, GitHub, VS Code, and your browser's Dev Tools). The only Python package
# used later in this notebook is `leafmap`. If a later cell errors on `import leafmap`, uncomment and run:
# !pip install leafmap -q

import sys
print(f"Python {sys.version.split()[0]} ready.")
```

## Step 5: Act

The "Act" step is about confirming both environments work and reflecting on what today's setup means for the semester ahead, not just checking boxes.

### What to turn in (practice, ungraded)

1. Your two screenshots:
   - **Screenshot 1**: your browser's Network tab showing tile requests (Visualize).
   - **Screenshot 2**: your saved ArcGIS Online map of Silver City (Create).
2. A short written reflection (3–5 sentences) answering:
   - Before tonight, did you think of a web map as something "static" (like a picture) or something "live" (constantly asking a server for more)? Has that changed?
   - Revisit **EQ1**: now that you've set up an account that costs WNMU an organizational license (ArcGIS Online) *and* an account that's free to anyone (GitHub), what's one concrete way that cost difference could shape who gets to publish a web map about a place like Grant County?
   - What's one thing that didn't work smoothly today, and what was your first troubleshooting move: docs, a web search of the exact error, or the course discussion board *(K16)*?

Post your screenshots and reflection to this week's discussion space in Canvas. This is a practice activity, not graded, but it's a hard prerequisite for Lab 1.

### Looking Ahead

Next week's **Lab 1** has you publishing and consuming a live WMS/WFS service with GeoServer, your first taste of the open standards that let proprietary and open-source tools talk to each other. Having both environments working today is what makes that possible.

---
### Resources

- Course content site: https://asivitskis.github.io/giss-366
- ArcGIS Online: access via course LMS
- GitHub: https://github.com/
- VS Code: https://code.visualstudio.com/
- NM RGIS Clearinghouse: https://rgis.unm.edu/
- Dorman, M. *Introduction to Web Mapping*, Preface
- `leafmap` (Python): https://leafmap.org/

---
## Appendix: Python Code (Optional)

Everything above this line is all you need for this practice lab. The cells below are optional and collapsed by default in most notebook viewers. They give a first, tiny taste of the Python-based mapping you'll do later in the open-source pathway.

Nothing here is required, and nothing here is graded.


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


    Map(center=[32.7701, -108.2803], controls=(ZoomControl(options=['position', 'zoom_in_text', 'zoom_in_title', '…



```python
# OPTIONAL -- save this first map as a shareable HTML file, and confirm you can find it afterward
m.to_html("week1_first_map.html")
print("Saved week1_first_map.html -- open it in a browser to see your first exported web map.")
```
