---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
  jupytext_version: 1.16.2
---

# Software & Tools Overview

This course uses a mix of desktop software, web-based platforms, and JavaScript libraries. You will **not** need to install everything — some tools live on your computer, others run in your browser. This page covers what to install before the course begins and what you'll encounter later in the semester.

## Install Before the Course Begins

- **[Visual Studio Code](vscode)** — *required.* Primary code editor for HTML, CSS, JavaScript, GeoJSON, and other web GIS files. Also install the **Live Server** extension to preview web maps locally.
- **[QGIS](qgis)** — *required.* Free, open-source desktop GIS for preparing, inspecting, editing, and exporting the geographic data used in your web maps. Install the **Long Term Release (LTR)** version unless told otherwise.
- **[Git](github)** — *required.* Version control for tracking changes to your projects and working with repositories on GitHub.

## Web-Based Accounts

No desktop installation needed — just an account and a browser.

- **[ArcGIS Online](arcgis-online)** — *required account.* Cloud-based GIS platform for publishing data, creating hosted feature layers and web maps, and exploring no-code and SDK-based app development. Sign in with your **WNMU** account. You do **not** need ArcGIS Pro unless specifically instructed.
- **[GitHub](github)** — *required account.* Hosts your Git repositories and published web projects, and connects to the Git software on your computer.

## Web Mapping Libraries

Leaflet and MapLibre GL JS are JavaScript libraries, not desktop applications — you won't install them like QGIS or VS Code. Instead, you'll include them directly in your HTML/JavaScript project code.

- **[Leaflet](leaflet)** — for building interactive maps and working with GeoJSON.
- **[MapLibre GL JS](maplibre)** — for vector tiles, styling, and more advanced web mapping workflows.

## What You Do *Not* Need to Install

HTML, CSS, JavaScript, Leaflet, and MapLibre GL JS are not separate installations — you'll work with all of them from inside VS Code as part of your web mapping projects. Other tools mentioned later in the syllabus (GeoServer, PostgreSQL/PostGIS, etc.) will come with setup instructions when we get to them.

You do **not** need prior programming or web development experience — HTML, CSS, JavaScript, and web GIS development concepts are introduced as the course goes.

---

## Setup Checklist (Before Week 1)

Work through these in order — later steps depend on earlier ones (Git needs to be installed before you can use it in VS Code). Budget **45–60 minutes** total.

1. **Browser & hardware** — a laptop or desktop (personal or a WNMU lab) and a modern browser: Chrome, Firefox, or Edge. Safari isn't recommended.
2. **VS Code + Live Server** — install VS Code and the **Live Server** extension. Verify: right-click any `.html` file → *Open with Live Server*.
3. **Git** — install for your OS. Verify: `git --version` prints a version number.
4. **GitHub account** — create one with a professional username, then configure Git locally (`git config --global user.name` / `user.email`).
5. **QGIS (LTR)** — install and verify by adding a vector layer (`Layer → Add Layer → Add Vector Layer`).
6. **ArcGIS Online** — sign in at [arcgis.com](https://www.arcgis.com/) with your **WNMU** credentials. Verify: your organization's name appears top-right. This depends on WNMU provisioning your account, so start it early — if it doesn't work, email your instructor right away rather than waiting.

> **Don't wait until the night before Lab 0.** ArcGIS Online access especially can take a few days to come through, so start this checklist a few days before the semester begins. If something's still broken by Week 1, that's what Lab 0's setup time and office hours are for.
asdas 