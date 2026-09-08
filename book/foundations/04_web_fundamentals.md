# Reading the Code You've Already Run

**Week 4 | Lecture + Lab 3: HTML & CSS Fundamentals**

---

## Learning Objectives

By the end of this unit, you will be able to:

- State the job each of HTML, CSS, and JavaScript does in a web page
- Read an HTML element (tag, attributes, contents) and predict what it adds to the page
- Write and modify common HTML elements: headings, paragraphs, lists, links, and images
- Read a CSS rule (selector, property, value) and predict what it changes on the page
- Use `id` and `class` attributes to target specific elements with CSS
- Set color, font, and text alignment with CSS
- Explain the CSS box model (content, padding, border, margin) and use `position` to place an element precisely on the page
- Recognize where this same literacy shows up in the ArcGIS Online pathway

**Textbook:** This lecture draws on Michael Dorman's open-access *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)* (IWM), Chapter 1 (HTML) and Chapter 2 (CSS). Section numbers are given throughout so you can find the full explanation and the live, downloadable version of each named example (for instance, `example-01-01.html`).

## Before We Start: You've Already Been Doing This

You've now run two working web pages in this course: `test_map.html` in Lab 0, and `wms_test.html` in Lab 2. You did this without a lecture on HTML or CSS first. That was deliberate. Today isn't your first exposure to this syntax. It's the first time we name what you've already been looking at.

We're going to open both files back up and read them line by line. The JavaScript in those files stays untouched today. That's Week 5's material, once HTML and CSS are solid underneath it.


## Why This Matters Even If You're Going the ArcGIS Online Route

This literacy shows up inside AGOL too:

- The **Embed widget** in Experience Builder takes raw HTML. You're about to get much faster at reading it.
- Widget **theming and layout** in Experience Builder uses CSS-flavored controls: color, spacing, positioning, the same vocabulary from today's CSS section.
- **Custom Experience Builder widgets** are built with JavaScript, HTML, and CSS via the developer edition and the ArcGIS Maps SDK. That's Week 5 territory, built on today's foundation.

Whichever pathway you lean toward for your final project, the next three weeks are foundational to both.

## Things You Can't Build Without This

*Example Links*
 - Link 1
 - Link 2

A few examples, on screen, that all have one thing in common: someone wrote HTML, CSS, or JavaScript by hand to build them.

- A custom Experience Builder widget doing something no out-of-the-box widget does
- A styled title or description box floating over a web map
- A hand-built Leaflet or MapLibre page like the ones you've already run

**These look great, right? If you want to build things like these, you need the coding basics. That's what we're developing over the next three weeks.**

---

## Part 0: Three Languages, Three Jobs

*(IWM Section 1.3)*

Every web page you'll build this semester is made of up to three languages, and each one has a distinct job:

- **HTML** (`.html`) determines page contents
- **CSS** (`.css`) determines presentation style
- **JavaScript** (`.js`) determines interactive behavior

A page can be built with HTML alone (that's all of today's examples). Add CSS and it gets styled. Add JavaScript and it gets interactive, reacting to clicks, loading data, building a map from a `<div>`. Keeping these three jobs separate in your head, structure, style, behavior, will make every error message you hit this semester easier to diagnose: first ask which of the three jobs is failing, then look in the matching file.

---

## Part 1: HTML, Structure and Contents

*(IWM Chapter 1)*

### The skeleton every page you've built follows

Every page you've built has followed the same skeleton (IWM Section 1.6.2):

```html
<!DOCTYPE html>
<html>
<head>
  <title>...</title>
</head>
<body>
  <div id="map"></div>
</body>
</html>
```

- `<!DOCTYPE html>` tells the browser this is an HTML5 document. It always comes first.
- `<html>` wraps the entire document.
- `<head>` holds information about the page (its title, and later its linked CSS and JS files) that isn't shown directly in the browser window.
- `<body>` holds everything that actually appears on screen.

**The single most important line in every map page you've written:**

```html
<div id="map"></div>
```

This empty box is the entire reason your maps have somewhere to live. This week the JavaScript that reaches into it (`L.map('map')`) is provided for you, but you now know exactly what it's reaching into, and why its `id` matters.

### Anatomy of an HTML element

*(IWM Section 1.5.1)*

An HTML element is a start tag, its content, and an end tag:

```html
<title>Web Mapping</title>
```

| Component | Example |
|---|---|
| Start tag | `<title>` |
| Content | `Web Mapping` |
| End tag | `</title>` |

Some elements are **empty**: they have a start tag only, no content, and no end tag. `<hr>` (a horizontal line) is one example.

Elements can also carry **attributes**, written inside the start tag as `name="value"`. The `<img>` element below has two attributes, `src` and `width`:

```html
<img src="images/leaflet.png" width="300px">
```

Attribute values go in quotes. Multiple attributes are separated by spaces. Not every attribute applies to every element: `src` is required for `<img>` but has no meaning for `<title>`.

### HTML comments

It's good practice to leave notes for yourself and for anyone else reading your code:

```html
<!-- This paragraph still needs a citation -->
```

Anything between `<!--` and `-->` is ignored by the browser. It exists only for humans reading the source.

### Example 1 (IWM `example-01-01.html`): the minimal page

Dorman's first example is a minimal HTML document, one heading and one paragraph (IWM Section 1.3):

```html
<!DOCTYPE html>
<html>
    <head>
        <title>A minimal web page</title>
    </head>
    <body>
        <h1>This is the main heading</h1>
        <p>This is a paragraph.</p>
    </body>
</html>
```

**Live in this notebook:** the cell below renders that exact page. Run it, then come back and change the heading text or add a second paragraph. That's Dorman's own suggested first exercise (IWM Section 1.10).


```python
from IPython.display import HTML, display

minimal_page = """
<div style="border:1px solid #999; padding:10px; font-family:sans-serif;">
    <h1>This is the main heading</h1>
    <p>This is a paragraph.</p>
</div>
"""

display(HTML(minimal_page))

# Try it: change the heading text above, or add a second <p> element, then re-run this cell.
```

### Block vs. inline elements

*(IWM Section 1.5.3)*

HTML elements behave in one of two ways:

- **Block-level elements** always start on a new line and stretch to fill the available width. Headings, paragraphs, and lists are block elements.
- **Inline elements** sit inside a line of text, the way a word sits among other words. Links, bold text, and images are inline elements.

| Type | Examples |
|---|---|
| Block | `<h1>`, `<p>`, `<ul>`, `<ol>`, `<div>` |
| Inline | `<a>`, `<b>`, `<i>`, `<img>`, `<span>` |

Whitespace note: browsers collapse multiple spaces and line breaks in your source code down to a single space when displaying text (IWM Section 1.6.7.1). If you want a visible line break, use `<br>`, not extra spaces in your source file.

### The elements you'll use most

*(IWM Sections 1.6.5, 1.6.6, 1.6.8, 1.6.9, 1.6.10)*

These are the elements you'll actually write and edit this week, in the lab and in your own map's description text.

| Element | Purpose | Example |
|---|---|---|
| `<h1>` ... `<h6>` | Section headings, largest to smallest | `<h1>Map Title</h1>` |
| `<p>` | A paragraph of text | `<p>This map shows...</p>` |
| `<b>`, `<i>` | Bold, italic text | `This is <b>bold</b>.` |
| `<ul>`, `<ol>`, `<li>` | Bullet list, numbered list, and their items | see below |
| `<a>` | A link, using the `href` attribute for the destination | `<a href="https://leafletjs.com">Leaflet</a>` |
| `<img>` | An image, using the `src` attribute for its location | `<img src="logo.png">` |
| `<div>` | A generic block-level container, used to group content | `<div id="description">...</div>` |
| `<span>` | A generic inline container, used to group a small piece of text | `<span class="highlight">note</span>` |

`<div>` and `<span>` don't do anything visible on their own. Their entire purpose is to give CSS (and later JavaScript) something to grab onto, which is exactly what `<div id="map">` has been doing all along.

### Example 2: lists and links

*(IWM Sections 1.6.8.1, 1.6.9.1)*

An unordered (bullet) list uses `<ul>` with `<li>` items inside it. Swap `<ul>` for `<ol>` and the same items become numbered:

```html
<ul>
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ul>
```

A link uses the `<a>` element with an `href` attribute pointing to its destination. Adding `target="_blank"` opens the link in a new tab:

```html
<a href="https://leafletjs.com" target="_blank">Leaflet</a>
```

**Live in this notebook:**


```python
list_and_link_demo = """
<div style="font-family:sans-serif; border:1px solid #999; padding:10px;">
  <p>Data sources used in this map:</p>
  <ul>
    <li>US Census TIGER/Line boundaries</li>
    <li>OpenStreetMap basemap tiles</li>
    <li>NOAA precipitation data</li>
  </ul>
  <p>Built with <a href="https://leafletjs.com" target="_blank">Leaflet</a>.</p>
</div>
"""

display(HTML(list_and_link_demo))

# Try it: change <ul> to <ol>, add a fourth list item, or point the link somewhere else.
```

### `id`, `class`, and `style`: the bridge to CSS

*(IWM Section 1.7)*

Every HTML element can carry three general-purpose attributes that don't change what the element is, only how you (and CSS) can refer to it later:

| Attribute | Purpose | Uniqueness |
|---|---|---|
| `id` | Uniquely identifies one element on the page | Must be unique. No two elements share an `id` |
| `class` | Groups multiple elements together | Many elements can share the same `class` |
| `style` | Applies a CSS rule directly to this one element | N/A, used sparingly |

```html
<h1 id="title">Map Title</h1>
<p class="note">Data current as of September 2026.</p>
<p class="note">Basemap tiles by OpenStreetMap contributors.</p>
```

Both paragraphs above share `class="note"`, so a single CSS rule targeting `.note` will style them both at once. Only one element on the page can carry `id="title"`. Hold on to this distinction. It's the first thing CSS asks about every element: are you targeting one specific element, or a group?

---

## Part 2: CSS, Appearance and Layout

*(IWM Chapter 2)*

### The anatomy of a CSS rule

A CSS rule always has the same three-part anatomy (IWM Section 2.3):

```
selector { property: value; }
```

Open `wms_test.html` from Lab 2 and find this block:

```css
body { margin: 0; }
#map { height: 100vh; width: 100vw; }
```

Read it out loud, in plain English, before naming the vocabulary:
- *"Select the body. Set its margin to zero."* Removes the default gap every browser puts around a page, so your map isn't sitting in a little frame.
- *"Select the element with id `map`. Set its height to 100% of the viewport height, its width to 100% of the viewport width."* This is the only reason your map fills the entire browser window instead of sitting in a small box.

**Linking method used here:** this is *embedded* CSS, a `<style>` block sitting inside `<head>`. The alternatives are *inline* CSS (a `style="..."` attribute on one element) and *external* CSS (a separate `.css` file pulled in with `<link rel="stylesheet" href="style.css">`). All three do the same job; they differ in where the rule lives (IWM Section 2.7). External CSS is the standard approach for real projects, since it keeps style code out of your HTML entirely and lets one CSS file style many pages. We'll use embedded CSS for most examples this week so HTML and CSS stay visible together while you're learning, and you'll move to an external file in the lab.

### Selectors: telling CSS which elements to style

*(IWM Section 2.4)*

| Selector type | Syntax | Targets |
|---|---|---|
| Type selector | `p { }` | Every `<p>` element on the page |
| Class selector | `.note { }` | Every element with `class="note"` |
| ID selector | `#title { }` | The one element with `id="title"` |

```css
p { color: black; }        /* every paragraph */
.note { color: gray; }     /* every element with class="note" */
#title { color: navy; }    /* the one element with id="title" */
```

This is exactly why `id` and `class` mattered a moment ago (IWM Section 1.7): they're what an ID or class selector is grabbing onto. When two rules conflict, the more specific selector wins. An ID selector overrides a type selector, and a rule written later in the file overrides an earlier, equally specific one (IWM Section 2.5).

### Example 3: color and text

*(IWM Sections 2.8.2, 2.8.3)*

CSS accepts color in a few formats. All are common in real code, so it helps to recognize each one:

| Method | Example | Notes |
|---|---|---|
| Named color | `color: DarkCyan;` | Easiest to read, limited palette |
| HEX | `color: #ee3e80;` | Six-digit code for red, green, blue |
| RGB / RGBA | `color: rgba(255, 0, 0, 0.8);` | Red, green, blue, plus optional transparency (the fourth value, 0 to 1) |

Text-related properties you'll reach for constantly:

```css
p {
    font-family: Arial, Verdana, sans-serif;
    font-size: 16px;
    font-style: italic;
    font-weight: bold;
    text-align: center;
}
```

`font-family` takes a list of options separated by commas. The browser uses the first one it has available, falling back down the list, which is why a generic family name like `sans-serif` is usually listed last as a safety net.

**Live in this notebook:**


```python
color_text_demo = """
<div style="font-family:sans-serif; border:1px solid #999; padding:10px;">
  <p style="color:DarkCyan; font-weight:bold;">Named color: DarkCyan</p>
  <p style="color:#ee3e80;">HEX color: #ee3e80</p>
  <p style="color:rgba(0,100,180,0.8); font-style:italic; text-align:center;">
     RGBA color, italic, centered
  </p>
</div>
"""

display(HTML(color_text_demo))

# Try it: change the HEX code, swap text-align to right or left, or add font-size.
```

### Example 4: the box model

*(IWM Section 2.8.4)*

Every HTML element lives inside an invisible box. CSS lets you control that box's size and boundaries:

- **Padding**, space between the content and the border
- **Border**, the edge of the box itself
- **Margin**, space outside the border, between this box and its neighbors

```css
div {
    height: 100px;
    width: 150px;
    background-color: powderblue;
    padding: 20px;
    margin: 5px;
    border: 1px solid black;
}
```

`border: 1px solid black;` is shorthand for setting `border-width`, `border-style`, and `border-color` all in one line (IWM Section 2.10.2). You'll see shorthand properties like this constantly; they're a convenience, not a different feature.

**Live in this notebook:**


```python
box_model_demo = """
<div style="font-family:sans-serif;">
  <div style="height:80px; width:140px; background-color:powderblue;
              padding:15px; margin:10px; border:2px solid #336;
              display:inline-block;">Box 1</div>
  <div style="height:80px; width:140px; background-color:#ffe775;
              padding:15px; margin:10px; border:2px solid #336;
              display:inline-block;">Box 2</div>
</div>
"""

display(HTML(box_model_demo))

# Try it: change the padding, margin, or border values above and re-run.
# Padding pushes the border outward. Margin pushes neighboring boxes away.
```

### Positioning a box on the page

*(IWM Section 2.8.4.6)*

By default, every block element sits in **normal flow**: each one stacks below the one before it. The `position` property lets you take an element out of that flow and place it exactly where you want.

| `position` value | Behavior |
|---|---|
| `static` (default) | Normal flow, stacks below the previous element |
| `relative` | Shifted from its normal-flow position, but its original space stays reserved |
| `absolute` | Placed relative to the nearest positioned ancestor, scrolls with the page |
| `fixed` | Placed relative to the browser viewport, stays put even when the page scrolls |

```css
.title-box {
    position: fixed;
    top: 10px;
    left: 20px;
}
```

This is the property that will let you place a title box in a fixed spot over your own map later this week, and it's also the reason `z-index` exists: when a positioned box overlaps other content, `z-index` controls which one renders on top (higher numbers sit in front). `z-index` only has an effect on elements that already have `position` set to something other than `static` (IWM Section 2.8.4.7).

**Live in this notebook:**


```python
position_demo = """
<div style="position:relative; height:180px; width:100%; background:#eee;
            font-family:sans-serif; border:1px solid #999;">
  <div style="position:static; background:#cde; padding:8px; margin:5px;
              display:inline-block;">static (normal flow)</div>
  <div style="position:absolute; top:70px; left:30px; background:#fdd;
              padding:8px; border:1px solid #900;">absolute, top:70px; left:30px;</div>
  <div style="position:absolute; top:100px; left:200px; background:#dfd;
              padding:8px; border:1px solid #090;">absolute, top:100px; left:200px;</div>
</div>
"""

display(HTML(position_demo))

# Try it: change the top:/left: values on either box, or switch position:absolute
# to position:fixed and imagine scrolling a page taller than your screen.
```

---

## Before Thursday's Lab: Git, VS Code, and GitHub Pages

Today's lecture covers HTML and CSS concepts. Thursday's lab is where three things happen for the first time in this course, all at once:

1. You connect VS Code to your own computer, not just the browser
2. You clone a GitHub repository and push a commit
3. You publish a page live on GitHub Pages

You'll also work through a few of Dorman's HTML and CSS examples directly, then apply what you learned today to customize the title and description text on your own Leaflet map page, styling it with the CSS properties from this lecture. You will not need to touch the map's JavaScript to do this.

None of that requires new HTML or CSS knowledge beyond today, but it does require lab time, not just reading. Come to lab with your GitHub account access confirmed (see homework) so we're not troubleshooting logins during coding time.

### Quick recap, in the order we covered it today

| Concept | Section | What it does |
|---|---|---|
| HTML / CSS / JS | 1.3 | Content / style / behavior, the three jobs |
| `<!DOCTYPE>`, `<html>`, `<head>`, `<body>` | 1.6.2 | The required skeleton of every page |
| Element, tag, attribute | 1.5.1 | The building block of HTML: `<img src="...">` |
| Block vs. inline | 1.5.3 | Whether an element stacks on its own line or sits inline |
| `<h1>`-`<h6>`, `<p>`, `<ul>`/`<ol>`/`<li>`, `<a>`, `<img>`, `<div>`, `<span>` | 1.6 | The elements you'll write and edit most |
| `id` / `class` / `style` | 1.7 | Lets CSS (and later JS) target one element or a group |
| CSS rule, selector, property, value | 2.3-2.4 | The anatomy CSS uses to style matched elements |
| `color`, `font-family`, `text-align` | 2.8.2-2.8.3 | Common color and text styling |
| `padding`, `border`, `margin` | 2.8.4 | The box model, space inside and around an element |
| `position`, `z-index` | 2.8.4.6-2.8.4.7 | Placing an element precisely and controlling overlap |

## Wrap-Up: Ungraded Reflection

Discussion 1 (Access & Equity) has moved to Week 5, where you'll have your own deployed Leaflet map to hold that question against. For today, a short, ungraded warm-up instead, jot a few sentences (edit the cell below):

- What's one line of code from `test_map.html` or `wms_test.html` that you can now explain, that you couldn't fully explain before today?
- Where else, in ArcGIS Online, in a lab, on any website, have you likely already been looking at HTML or CSS without recognizing it as such?

*Your answer here:*


## Looking Ahead

Week 5 takes the next step: building a Leaflet map from a completely blank file, HTML skeleton, linked CSS and JS, tile layer, and a GeoJSON layer, rather than editing code that was already written for you. This is also where you'll write your first JavaScript: variables, objects, function calls, and the DOM (Document Object Model) that lets JavaScript find and modify the very `id`-tagged elements you styled with CSS this week. Week 6 does the same build again in MapLibre GL JS, framed explicitly as the same architecture in a newer library, so you can see directly what transfers and what doesn't.

## Readings & Resources

- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 1: HTML
- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 2: CSS
- [MDN: HTML basics](https://developer.mozilla.org/en-US/docs/Learn/HTML)
- [MDN: CSS basics](https://developer.mozilla.org/en-US/docs/Learn/CSS)

---

## Lab 3: Reading Your Own Code (and Publishing It)

See [Lab 3](../labs/lab_03.md). Lab 3 includes the full HTML + CSS + pre-built-Leaflet-map capstone exercise, where you'll combine everything from today into one published page.
