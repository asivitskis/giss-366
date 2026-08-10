---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
  jupytext_version: 1.16.2
---

# Setup Checklist (Before Week 1)

This page is your one-stop starting point for getting your computer ready for **GISS 366/368: WebMapping & WebGIS**. Work through the steps below **in order** — later steps sometimes depend on earlier ones (for example, Git needs to be installed before you can use it inside VS Code).

Budget about **45–60 minutes** total if everything goes smoothly. If you hit a snag anywhere, that's normal — flag it in office hours or on Canvas rather than getting stuck.

> **Don't wait until the night before Lab 0.** A couple of these steps (especially ArcGIS Online account access) may not be instant, so start this checklist at least a few days before the semester begins.

## 🎥 Video Walkthrough

<!--
Replace the src URL below with your recorded walkthrough link once available
(YouTube, Panopto, etc.). Example for YouTube:
<iframe width="560" height="315" src="https://www.youtube.com/embed/VIDEO_ID"
title="GISS 366 Setup Walkthrough" frameborder="0"
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
allowfullscreen></iframe>
-->

*A full video walkthrough of this checklist will be linked here — check back before the semester begins if it isn't posted yet.*

The written steps below work as a complete standalone guide even without the video, and as a reference to pause/rewind to while you watch.

---

## Step 1 — Confirm Your Hardware & Browser

- [ ] A laptop or desktop computer (Windows, macOS, or Linux) — personal or via a WNMU lab
- [ ] A reliable broadband internet connection
- [ ] A modern browser: **Chrome, Firefox, or Microsoft Edge** (Safari is not recommended for this course)

No installation needed for this step — just confirm you have what you need.

---

## Step 2 — Install VS Code + the Live Server Extension

VS Code is the editor you'll use for nearly every lab in this course, and Live Server is what lets you preview your web maps in a browser.

👉 **Full instructions:** [Visual Studio Code](vscode)

- [ ] Install VS Code
- [ ] Install the **Live Server** extension
- [ ] Verify: right-click any `.html` file → **"Open with Live Server"** opens it in your browser

---

## Step 3 — Install Git

Git is the version control tool you'll use to save and track changes to every project.

👉 **Full instructions:** [Git & GitHub](github)

- [ ] Install Git for your operating system
- [ ] Verify: `git --version` in a terminal prints a version number

---

## Step 4 — Create a GitHub Account

GitHub hosts your repositories and will host your published web maps via GitHub Pages.

👉 **Full instructions:** [Git & GitHub](github)

- [ ] Create a free account at [github.com](https://github.com/) using a professional username
- [ ] Configure Git locally with your name and email (`git config --global user.name` / `user.email`)
- [ ] Verify: you can log into github.com

---

## Step 5 — Install QGIS

QGIS is where you'll prepare and export the geographic data used in your web maps.

👉 **Full instructions:** [QGIS](qgis)

- [ ] Install the **Long Term Release (LTR)** version
- [ ] Verify: QGIS opens, and you can preview a vector layer via `Layer → Add Layer → Add Vector Layer`

---

## Step 6 — Confirm ArcGIS Online Access

This is the one step that isn't fully in your hands — it depends on WNMU provisioning your account.

👉 **Full instructions:** [ArcGIS Online](arcgis-online)

- [ ] Sign in at [arcgis.com](https://www.arcgis.com/) using your **WNMU** credentials (not a personal account)
- [ ] Verify: your organization's name appears in the top-right corner after signing in

**If this doesn't work, email your instructor now** — don't wait, since account setup can take a few days.

---

## Step 7 — Final Check

Run through this list one more time before Week 1:

- [ ] Browser ready
- [ ] VS Code installed with Live Server working
- [ ] Git installed and configured
- [ ] GitHub account created
- [ ] QGIS (LTR) installed
- [ ] ArcGIS Online sign-in confirmed

If every box is checked, you're ready for Lab 0. If something's still broken, that's exactly what Lab 0's dev environment setup time and office hours are for — come with your specific error message or a screenshot, and we'll sort it out together.

---

*You do **not** need prior programming or web development experience, and you don't need to install everything else mentioned in the syllabus (GeoServer, PostgreSQL/PostGIS, etc.) right now — those will be introduced with instructions when we get to them. See the [Software & Tools Overview](software_overview) page for the full picture of what's used across the semester.*
