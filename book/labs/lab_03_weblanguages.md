# Lab 3
# HTML & CSS in Practice: Publishing Your First Map Page
### GISS/GEOG 366/368 · Web Mapping & Web GIS

**Unit 4 Focus:** HTML and CSS only. The map's JavaScript (`map.js`) is provided and stays untouched today. Reading, writing, and modifying your own JavaScript starts in Week 5, when you build a Leaflet map from a blank file. This is your third graded lab. 

## Before You Begin

Today adds new tools you have not used yet in this course, on top of ones you already have. Verify that you have these tools installed before proceeding. 

| Tool | Role today |
|---|---|
| **A GitHub account** | Confirmed working in this week's homework, before lab |
| **VS Code, with the Git/GitHub extension enabled** | Cloning the starter repository and editing files locally |
| **Git** | Installed alongside VS Code, or separately; VS Code will prompt you if it's missing |
| **VS Code + Live Server** | Previewing your edits in a browser before you publish |
| **The Lab 3 starter repository** | Provided by your instructor; contains the files you'll edit today, listed in Step 2 |
| **A GitHub Pages-enabled repository** | Where your finished page will live, publicly, at a URL you can share |
| **[MDN Web Docs](https://developer.mozilla.org/)** | Your first stop, before the discussion board, when something doesn't work |

You do not need to download Leaflet yourself today. The starter repository already includes the Leaflet library files the provided map page links to.

## Step 1: Ask

Check out this link of a basic Leaflet Map template, hosted on GitHub Pages: https://asivitskis.github.io/giss366-lab03-template/

For Lab 03, you will be modifying this exact template to start building a customized web map of your own. You'll begin to investigate the source code files that go into deploying this map.

Jot your first-pass answer before continuing with the lab instructions:
- Look at the page for a minute. Try sorting what you see into two rough piles: things that feel tied to the actual geography (the map itself, markers, behavior) and everything else (content, descriptions, etc.). Which parts do you think you'll be able to modify with your current knowledge and skill level? You'll check where the real line falls once you open the source code in Step 2.
- You are about to publish a web page with an accesible URL for the first time in this course. What is one thing about that (version control, accessibility, etc.) that feels different from saving a file to your own `giss-366` folder on your personal computer?

Keep that sorting in mind as you consider:

> **Lab 3 Question: All of the "geographic" components of this map are controlled by JavaScript. However, if you never touch a single line of JavaScript today, how many components of this map will you be able to have control over with HTML and CSS?**

## Step 2: Collect

### Part A: Confirm access

Before opening VS Code, confirm two things:
1. You can log in to GitHub in a browser.
2. You have the link to the Lab 3 starter repository. This was shared via a Canvas Message prior to Thursday's Lab Session. And, it can also be found below:
 - https://github.com/asivitskis/giss366-lab03-template

Make sure that you have access to these items above. Everything after this point assumes both are working.

<details>
<summary><b>Part B: Make your own copy of the repository, then clone it in VS Code. Click to expand</b></summary>

This is the first time you'll connect VS Code to a GitHub repository directly, rather than downloading files by hand. The steps below use VS Code's built-in Git support, no command line required.

**Step 0: Create your own copy of the template repository.**

1. In a browser, go to the template repository URL: https://github.com/asivitskis/giss366-lab03-template
2. Make sure you are signed into your GitHub account.
3. Click the green **Use this template** button, then choose **Create a new repository**.
4. Name it whatever you like (for example, `giss-366-lab3`), and make sure it's being created under **your own** GitHub account, not your instructor's.
5. Click **Create repository**. This gives you a full, independent copy that you own and can push to.
6. Copy the URL of **this new repository** (the one you just created), not the original starter repo. You'll paste it into VS Code in the next step.

**Now clone your own copy into VS Code:**

1. Open VS Code. Make sure no files are open. Click File > "New Window" if needed.
2. Open the **Source Control** panel (the icon that looks like a branching line, in the left sidebar).
3. Click **Clone Repository**.
4. Paste the URL of **your own repository** from Step 0, then choose a location on your computer to save it, ideally near your existing `giss-366` folder.
5. When VS Code asks, choose **Open** to open the cloned folder as your workspace.
6. If this is the first time Git has asked for your identity on this computer, VS Code will prompt you for your name and email. Use the same email associated with your GitHub account.

If VS Code reports that Git is not installed, install it from [git-scm.com](https://git-scm.com/downloads), restart VS Code, and try again from Step 3.

If you don't see a **Use this template** button on the starter repository page, you're likely looking at the wrong repository. Check in with Alex before proceeding, since cloning the original directly will prevent you from publishing your own page later.

</details>

### Part C: Explore what you cloned

Open the folder in VS Code's file explorer. You should see something like this:

| File / folder | What it is | Will you edit it today? |
|---|---|---|
| `index.html` | The full map page: structure, style, and the link to the map script (built on Dorman's `example-02-11.html` pattern) | Yes, HTML and CSS only |
| `README.md` | A standard, explanatory landing page that provides a description for your repository. | **Optional edits** |
| `map.js` | The JavaScript that builds the actual Leaflet map | **No** |
| `css/leaflet.css` | The Leaflet library's own stylesheet, located within a CSS folder. | No |
| `js/leaflet.js` | The Leaflet library itself, located within the js folder. | No |

Two files are yours to change today. Two are not. Keep that distinction in view for the rest of the lab; it's the whole point of Part 3 in Step 4.

## Step 3: Visualize

### 3.1 Structure alone: reading `index.html` style content

*(Dorman, M. Introduction to Web Mapping, Section 2.10.1)*

Before opening the full, styled file, check out this brief copy of its content. This is the plain HTML that is built into the map that you will be reproducing. It include a title and a description box, before any positioning or color is added:

```html
<h1 id="title">[update this] Leaflet Quick Start Guide</h1>
<div id="description">
    <h2>About this map</h2>
    <p>This is the final result of the
        <a href="https://leafletjs.com/examples/quick-start/"
            target="_blank">Leaflet Quick Start Guide</a>.
    The map demonstrates Leaflet basics, such as setting up a Leaflet map,
    adding markers, polylines and popups.</p>
    <p>The placement and styling of the title and description boxes is
    done using CSS. Check out the source code of this page to see how.</p>
    <p>Map authored by: [insert your name here]</p>
</div>
```

Two `id` attributes appear here, `id="title"` and `id="description"` (recall IWM Section 1.7). Nothing about their position or appearance is decided yet. That is entirely CSS's job, coming next.

**Live in this notebook**, this is what the content above looks like with zero CSS applied, stacked in plain normal flow:


```python
from IPython.display import HTML, display

structure_only_demo = '''
<div style="font-family:sans-serif; border:1px solid #999; padding:10px;">
  <h1 id="title">[update this] Leaflet Quick Start Guide</h1>
  <div id="description">
      <h2>About this map</h2>
      <p>This is the final result of the
          <a href="https://leafletjs.com/examples/quick-start/"
              target="_blank">Leaflet Quick Start Guide</a>.
      The map demonstrates Leaflet basics, such as setting up a Leaflet map,
      adding markers, polylines and popups.</p>
      <p>The placement and styling of the title and description boxes is
      done using CSS. Check out the source code of this page to see how.</p>
      <p>Map authored by: [insert your name here]</p>
  </div>
</div>
'''

display(HTML(structure_only_demo))
```

### 3.2 Adding position: reading `example-02-09` style CSS

*(Dorman, M. Introduction to Web Mapping, Section 2.10.2)*

Now add the CSS that pulls the title and description out of normal flow and pins each one to a fixed spot in the viewport:

```css
#title {
    position: fixed;
    top: 10px;
    left: 55px;
    margin-top: 0;
    padding: 10px 15px;
    background-color: rgba(255, 255, 255, 0.5);
    border: 1px solid grey;
    border-radius: 3px;
}
#description {
    position: fixed;
    bottom: 20px;
    left: 10px;
    width: 280px;
    padding: 0px 15px;
    background-color: rgba(255, 255, 255, 0.7);
    border: 1px solid grey;
    border-radius: 3px;
}
```

Name what's doing the work here, using vocabulary from lecture: two ID selectors (`#title`, `#description`), `position: fixed` to leave normal flow, `top`/`left`/`bottom` offsets to place each box, and `padding`, `border`, and `border-radius` from the box model to make each one readable.

**Live in this notebook:**


```python
positioned_demo = '''
<div style="position:relative; height:260px; width:100%; background:#ddd;
            font-family:sans-serif; overflow:hidden; border:1px solid #999;">
  <div style="position:absolute; top:10px; left:20px; margin-top:0;
              padding:10px 15px; background-color:rgba(255,255,255,0.5);
              border:1px solid grey; border-radius:3px;">
    <strong>Leaflet Quick Start Guide</strong>
  </div>
  <div style="position:absolute; bottom:15px; left:10px; width:260px;
              padding:0px 15px; background-color:rgba(255,255,255,0.7);
              border:1px solid grey; border-radius:3px;">
    <h4>About this map</h4>
    <p style="font-size:0.85em;">Positioned with CSS, still no map behind it yet.</p>
  </div>
</div>
'''

display(HTML(positioned_demo))

# Compare this to 3.1 above: same HTML content, same id attributes,
# completely different layout, because of CSS alone.
```

### 3.3 The full pattern: `example-02-11.html`

*(Dorman, M. Introduction to Web Mapping, Sections 2.10.3-2.10.4)*

This is the same file you already have in your cloned repository, saved as `index.html`, built on Dorman's `example-02-11.html` pattern. It adds two final layers on top of what you just saw: custom fonts and colors (Section 2.10.3), and the map itself, loaded by `map.js` and placed behind the title and description using `z-index` (Section 2.10.4).

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Map description on web map</title>
        <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
        <link href="https://fonts.googleapis.com/css?family=Noto+Sans" rel="stylesheet">
        <link href="https://fonts.googleapis.com/css?family=Lora" rel="stylesheet">
        <link rel="stylesheet" href="css/leaflet.css">
        <script src="js/leaflet.js"></script>
        <style>
            body {
                margin: 0;
                padding: 0;
                background-color: "whitesmoke";
                font-family: "Noto Sans", sans-serif;
                color: #3d3d3d;
            }
            #title {
                position: fixed;
                margin-top: 0;
                top: 10px;
                left: 55px;
                font-size: 2em;
                font-family: "Lora", serif;
                letter-spacing: .04em;
                padding: 10px 15px;
                background-color: rgba(255, 255, 255, 0.5);
                border: 1px solid grey;
                border-radius: 3px;
                z-index: 800;
            }
            #description h2 {
                font-family: "Lora", serif;
                letter-spacing: .04em;
            }
            #map {
                position: absolute;
                top: 0;
                bottom: 0;
                width: 100%;
            }
            #description {
                position: fixed;
                bottom: 20px;
                left: 10px;
                width: 280px;
                padding: 0px 15px;
                background-color: rgba(255, 255, 255, 0.7);
                border: 1px solid grey;
                border-radius: 3px;
                z-index: 800;
            }
            #description p {
                font-size: .9em;
                line-height: 1.5em;
            }
            #description a {
                color: #005daa;
                text-decoration: none;
            }
            #description a:hover {
                text-decoration: underline;
            }
        </style>
    </head>
    <body>
        <h1 id="title">[update this] Leaflet Quick Start Guide</h1>
        <div id="map"></div>
        <div id="description">
            <h2>About this map</h2>
            <p>This is the final result of the <a href="https://leafletjs.com/examples/quick-start/" target="_blank">Leaflet Quick Start Guide</a>. 
                The map demonstrates Leaflet basics, such as setting up a Leaflet map, adding markers, polylines and popups.</p>
            <p>The placement and styling of the title and description boxes is done using CSS. 
                Check out the source code of this page to see how.</p>
            <p>Map authored by: [insert your name here]</p>
        </div>
        <script src="map.js"></script>
    </body>
</html>
```

A few things worth pointing out before you go editing anything:

- `#map` uses `position: absolute` with `top: 0; bottom: 0; width: 100%;`, which is what makes it fill the whole browser window behind everything else.
- `z-index: 800` on `#title` and `#description` is what keeps them visible on top of the map instead of underneath it (IWM Section 2.8.4.7).
- Two Google Fonts are linked in the `<head>` (`Noto Sans`, `Lora`) and then referenced by name inside the `<style>` block, an external resource plus an internal rule working together.
- The very last line, `<script src="map.js"></script>`, is the one line in this entire file connected to JavaScript. Everything above it is HTML and CSS.

**Do not open `map.js` expecting to edit it today.** Open it only to read it, briefly, so you know what it's doing:

```js
let map = L.map("map").setView([51.505, -0.09], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
}).addTo(map);

L.marker([51.5, -0.09])
    .addTo(map)
    .bindPopup("<b>Hello world!</b><br>I am a popup.")
    .openPopup();

L.circle([51.508, -0.11], 500, {
    color: "red",
    fillColor: "#f03",
    fillOpacity: 0.5
}).addTo(map).bindPopup("I am a circle.");

L.polygon([
    [51.509, -0.08],
    [51.503, -0.06],
    [51.51, -0.047]
]).addTo(map).bindPopup("I am a polygon.");
```

You can read this now: a variable (`map`), a couple of object literals (the tile layer and circle options), and a chain of function calls. You are not expected to write this yet. That's Week 5.

## Step 4: Create

Three parts. The first builds the pattern by hand in a scratch file. The second has you customized the real map file. The third publishes the real file from your cloned repository.

<details>
<summary><b>Part 1: Build the structure-plus-position pattern yourself. Click to expand</b></summary>

In your cloned repository folder, create a new file named `warmup.html`. Type (don't copy-paste) a full HTML document that:

1. Has the standard skeleton: `<!DOCTYPE html>`, `<html>`, `<head>` with a `<title>`, and `<body>`.
2. Inside `<body>`, includes the structure from Step 3.1: an `<h1 id="title">` and a `<div id="description">` containing an `<h2>`, at least one `<p>`, and the link to the Leaflet Quick Start Guide.
3. Inside `<head>`, add a `<style>` block containing the CSS from Step 3.2: the `#title` and `#description` rules using `position: fixed`.

Open `warmup.html` with Live Server. Confirm your title and description boxes are positioned in fixed spots, matching what you saw rendered in Step 3.2, with no map behind them yet (there isn't one; you haven't added a `#map` div or a script tag in this file, and that's expected).

Leave `warmup.html` in your repository folder. You'll stage and push it along with `index.html` in Part 3.

</details>

<details>
<summary><b>Part 2: Customize the real capstone file. Click to expand</b></summary>

Open `index.html` from your cloned repository. This is the complete file shown in Step 3.3. Make the following changes:

**HTML changes** (inside `<body>`, leave `<script src="map.js"></script>` exactly where it is):
- Change the `<h1 id="title">` text to a title of your choosing.
- Rewrite the paragraphs inside `<div id="description">` to describe your own interests, planned project topic, or a data source you're considering for Week 5, rather than describing the Leaflet Quick Start Guide.
- Add a bulleted list (`<ul>`/`<li>`) inside the description box, naming two or three things (data sources, questions, places), using the list pattern from lecture.
- Change the last line, "Map authored by...", to your own name.

**CSS changes** (inside the `<style>` block only):
- Change at least one color: the `background-color` of `#title` or `#description`, or the `border` color.
- Change at least one font choice, either swap `font-family` on `#title` for a different value, or link a different Google Font in `<head>` and reference it.
- Change the position of one box by editing its `top`, `left`, or `bottom` value.
- Add `class="note"` to one of your paragraphs, and add a new CSS rule, `.note { }`, that styles it differently from the others (smaller font, italic, a different color), to practice the `id` vs. `class` distinction from lecture.

**What not to change:** `map.js`, the `<script src="js/leaflet.js"></script>` line, the `<link rel="stylesheet" href="css/leaflet.css">` line, or the `#map` div itself. 

Changing the map's location, markers, or data is this the focus for Week 5. . This is the domain of JavaScript, and it is controlled within the `map.js`. Leave this file exactly as it is for now (any changes may interupt the function of your final script).

Preview your edits with Live Server before moving on. You'll push this file to GitHub in Part 3 and submit it directly, so make sure it's in the state you want graded before you commit.

</details>

<details>
<summary><b>Part 3: Publish to GitHub Pages. Click to expand</b></summary>

Your first push to GitHub happens here. If any step below prompts you for GitHub sign-in, use the same account confirmed in Step 2.

**1. Stage and commit your changes**, using VS Code's Source Control panel:
- Open the Source Control panel. You should see `index.html` (your customizations) and `warmup.html`  listed as changes.
- Click the `+` next to each file to stage it, or stage all changes at once.
- Type a commit message describing what you did, for example: `Customize title, description, and styling for Lab 3`.
- Click the checkmark (Commit).

**2. Push your commit**, using the **Sync Changes** button (or **Push**) in the Source Control panel. This sends your commit from your computer up to GitHub. This is the first time you've done this in the course; if VS Code asks you to confirm your GitHub identity again, do so.

**3. Enable GitHub Pages**, in your browser:
- Go to your repository on github.com.
- Open **Settings → Pages**.
- Under **Build and deployment**, set the source branch to `main` and the folder to `/ (root)`.
- Save, and wait a minute or two for GitHub to build your site.

**4. Find your published URL.** GitHub will show it on the same Settings → Pages screen once the build finishes, in the form `https://[your-username].github.io/[your-repo-name]/`. Open it in a browser.

Congratulations! You just published your first open-source web-map.

</details>

## Step 5: Act

### What to turn in (graded: Lab Exercises)

1. Your `warmup.html` file, hand-typed and pushed to your repository (Create, Part 1).
2. Your customized `index.html` file, pushed to your repository (Create, Part 2).
3. The link to your published GitHub Pages URL (Create, Part 3).
4. The link to your GitHub repository where the page and the files are hosted (Collect, Part B)
5. A short written reflection (3-4 sentences) answering:
   - Revisit your Step 1 guess. Now that you've built it, how much of the finished page's look and content came from HTML and CSS alone, with zero JavaScript written by you?
   - In your own words, why does changing `#title`'s CSS rule change what the page looks like, even though `map.js` was never opened for editing?
   - Which part of today felt more unfamiliar: the Git/VS Code/GitHub Pages workflow, or the HTML/CSS editing itself? What would make the less familiar one easier next time?

Submit your GitHub links and written reflection to this week's submission space in Canvas. Since `warmup.html` and `index.html` are both pushed to your repository, they'll be graded directly from GitHub, either from the repository's file view or, for `index.html`, from the live Pages URL itself.

---

## Lab 3 Rubric (40 pts)

| Score Band | What It Looks Like |
|:---|:---|
| **Exceptional (7-8)** | Exceeding expectations; indicates mastery; in-depth understanding; higher-order thinking; inferences and extensions of learning objectives that go beyond what was taught; truly superb effort. |
| **Proficient (5-6)** | Meeting expectations; application of concepts; independently demonstrates understanding and thorough competency of learning objectives explicitly taught. |
| **Developing (3-4)** | Approaching expectations; demonstration of basic understanding without application and understanding of more complex ideas and processes; meets minimum requirements for satisfactory learning. |
| **Insufficient (1-2)** | Below expectations; partial or no demonstration of understanding and progress toward learning objectives; major errors and omissions present; inadequate for competency. |

<br>

| Criteria | Comments | Grade |
|:---|:---|:---:|
| **Content:** `warmup.html` and the customized `index.html` are both present in the pushed repository and correctly show what's asked, the hand-built warmup page and the customized, styled map page. | | / 8 |
| **Content:** The written reflection is complete, directly and specifically answering all three prompts, including an explanation of why CSS changes alone reshape the page. | | / 8 |
| **Process:** The published page loads correctly at its GitHub Pages URL. `map.js` and the Leaflet library files were left untouched. The HTML and CSS changes meet all bullet points in Step 4, Part 2. | | / 8 |
| **Process:** The reflection is evidence-based and specific, grounded in the student's own before-and-after comparison of the page rather than general statements, and names a specific troubleshooting moment and how it was resolved. | | / 8 |
| **Product:** `warmup.html`, `index.html`, the GitHub Pages link, and the reflection are submitted in the requested format, clearly labeled, legible, and free of errors. | | / 8 |
| **Total** | | **/ 40** |

---

### Lab 3 AI Policy - Level 2

This lab permits optional use of AI to support student learning. Permitted uses can include using AI to help troubleshoot GIS workflows, however, independent installation and use of AI agents to complete GIS tasks is not permitted at this time. AI may also be used to support general writing procedures (outlining a project, improving clarity or grammar, etc.), but all responses must remain original to each student as a sole responsible author. Disclosure of AI use is not required for this Level 2 assignment. Using AI is a choice with ethical considerations, and it is asked that students weigh those considerations accordingly as they complete this work.

### Looking Ahead

Week 5 is the real from-scratch build: a Leaflet map starting from a completely blank file, HTML skeleton, linked CSS and JS, tile layer, and a GeoJSON layer of your own, using the region and data you've been working with all semester. This is also where Discussion 1 (Access & Equity) lands, held against a map you built and published yourself.

---
### Resources

- [MDN Web Docs](https://developer.mozilla.org/)
- [GitHub Docs: Configuring a publishing source for your GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
- [Leaflet documentation](https://leafletjs.com/reference.html) (for context only; you are reading `map.js`, not writing Leaflet code today)
- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 1 (HTML), Section 1.7 (`id`, `class`, `style`)
- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 2 (CSS), Section 2.10 ("Map description example," the source of today's `example-02-08`, `example-02-09`, and `example-02-11`)
