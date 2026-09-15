# JavaScript & Leaflet

## Week 5 | Lecture + Lab 4: JavaScript Fundamentals & Building a Leaflet Map from Scratch

**Unit 5 Focus:** This is the week we'll dive directly into the `map.js` file. You'll learn the JavaScript you need, then use it to build a Leaflet map from a blank file: HTML skeleton, a tile layer, your own markers/lines/polygons, popups, and a custom description control, published live on GitHub Pages in Lab 4.

---

## Learning Objectives

By the end of this unit, you will be able to:

- State what JavaScript adds on top of HTML and CSS (behavior, beyond structure or style)
- Define variables with `let`, and identify JavaScript's core data types: string, number, boolean, array, object
- Read and write nested object/array data using dot and bracket notation
- Write a function, call it, and explain the difference between a parameter and an argument
- Use `if`/`else` and a `for` loop (or `.forEach`) to repeat or condition code
- Explain the DOM, and use `document.getElementById` and `.innerHTML` to read and modify page content
- Bind an event listener so code runs in response to a user click
- Build a Leaflet map from an empty HTML file: `L.map`, `L.tileLayer`, and at least one vector layer type (`L.marker`, `L.polyline`, or `L.polygon`)
- Attach a popup to a layer, and build a custom `L.control` description panel
- Publish a from-scratch Leaflet map to GitHub Pages

**Textbook:** This lecture draws on Dorman's (2020) open-access *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)* (IWM), Chapter 3 (JavaScript Basics), Chapter 4 (JavaScript Interactivity), and Chapter 6 (Leaflet). Section numbers are given throughout so you can find the full explanation and live examples.

## Before We Start: What Changes This Week

In Lab 3, `map.js` was provided to run in the background. Every geographic thing on that map, its center, its markers, its behavior, was already written for you; HTML and CSS only controlled the page dressing around it. This week that file is a blank page you fill in yourself. The good news: your GitHub, VS Code, and Live Server workflow from Lab 3 carries over unchanged. The only new thing is what goes inside `map.js`.

---

## Part 1: What JavaScript Is, and Where It Runs

*(IWM Section 3.2–3.4)*

When we practiced last week with HTML and CSS, we were essentially modifying data formats. These tell the browser what to display and how to style it. JavaScript is fundamentally different because it's a **programming language**, giving the browser a much wider range of instructions than a data format can: running calculations, showing or hiding content based on user input, or loading fresh data from an external source.

A key distinction: **client-side** vs. **server-side**. This course, and this book, focuses on client-side code, JavaScript that runs in the browser, on the visitor's own computer, rather than on a remote server. There are two situations where your client-side code runs:

- **On page load**: e.g., the script that builds your map runs the moment the page opens
- **On user interaction**: e.g., a function runs only when the user clicks something

You can experiment with JavaScript directly using your browser's **JavaScript console** (Chrome: `Ctrl+Shift+J`, or `F12` → Console tab). Type an expression, hit Enter, see the result. This is a good scratchpad for testing a line of Leaflet code before committing it to `map.js`.

---

> ### Try It in Your Console
>
> Open your browser's JS console right now (`F12` → Console, or `Ctrl+Shift+J`; `Cmd+Option+J` on Mac) and type each line below one at a time, pressing Enter after each. A blank tab works fine. What do you notice after you type each option?
>
> ```js
> 2 + 2
> ```
> ```js
> "Silver City"
> ```
> ```js
> console.log("Hello world.")
> ```
>
> The console echoes back whatever a line evaluates to. `console.log(...)` prints on purpose; a bare expression like `2 + 2` gets echoed automatically. We'll keep using our console to explore JS basics.

---


## Part 2: Variables and Data Types

*(IWM Section 3.5–3.6)*

### Declaring variables
A JavaScript variable is something that holds values. There are several ways to define variables in JavaScript, using the following keywords:

 - `var`
 - `let`
 - `const`

We'll use `let` throughout this course (not `var`, which you'll see in older code, or `const`, for values that never change):

```js
let lat = 32.7701;
let lng = -108.2803;
```

Every statement ends in `;`. Comments use `//` (single line) or `/* ... */` (multi-line).

### The data types you'll actually use

| Type | Example | Where it shows up in a map |
|---|---|---|
| **Number** | `31.26`, `-10.2` | Latitude, longitude, zoom level, radius |
| **String** | `"Silver City"`, `'#872bc0'` | Popup text, colors, tile server URLs |
| **Boolean** | `true`, `false` | A layer's visibility, a toggle state |
| **Array** | `[32.77, -108.28]` | A single coordinate pair; a list of coordinate pairs for a line or polygon |
| **Object** | `{color: "red", weight: 4}` | The "options" object every Leaflet function accepts |

Arrays are indexed from `0`:

```js
let coords = [31.262218, 34.801472];
coords[0];  // 31.262218 (latitude)
coords[1];  // 34.801472 (longitude)
```

Objects use `key: value` pairs, accessed with dot or bracket notation:

```js
let style = {color: "red", weight: 4, fillColor: "yellow"};
style.color;       // "red"
style["weight"];   // 4
```

**This matters immediately in Leaflet**, because every Leaflet function that draws a shape takes exactly this shape of input: an array (or array of arrays) for the geometry, and an object for the styling options. Arrays and objects can nest inside each other, which is exactly how a line's coordinate list or a marker's popup options are structured.

Use `typeof` to check what you're working with (`typeof "a"` → `'string'`, `typeof [1,2]` → `'object'`), and `Array.isArray(x)` to tell an array apart from a plain object.

---

> ### Try It in Your Console
>
> Write these in one at a time and predict the output before you hit Enter.
>
> **Variables and arrays:**
> ```js
> let lat = 31.262218;
> let lng = 34.801472;
> let coords = [lat, lng];
> coords[0];
> coords[1];
> ```
>
> **Objects:**
> ```js
> let style = {color: "red", weight: 4, fillColor: "yellow"};
> style.color;
> style["weight"];
> ```
>
> **`typeof` and then data example:**
> ```js
> typeof "a";
> typeof 5;
> typeof true;
> typeof [1, 2];
> ```
> That last one returns `'object'`, not `'array'`. Arrays don't get their own `typeof` category in JavaScript.
>
> **actual array check:**
> ```js
> Array.isArray([1, 2]);
> Array.isArray({a: 1});
> ```

---


## Part 3: Functions

*(IWM Section 3.7–3.8)*

A function packages up a block of code so you can run it repeatedly without retyping it:

```js
function greeting(name) {
    console.log("Hello, " + name + "!");
}

greeting("Student");  // Prints "Hello, Alex!"
```

`name` here is a **parameter**; `"Student"` is the **argument** you pass in when you call the function. `console.log(...)` prints to the console, the same tool you'll use throughout the lab to check that a variable holds what you think it holds before it ends up on the map.

You'll also see **anonymous functions** (no name, defined inline) constantly in Leaflet code, especially with event listeners and controls:

```js
document.getElementById("btn").addEventListener("click", function() {
    console.log("Clicked!");
});
```

Objects can hold functions too, in which case they're called **methods**, accessed the same way as any other property: `car.start()`. Leaflet layer objects work exactly this way, `.addTo()`, `.bindPopup()`, and `.onAdd()` are all methods you'll call throughout this lab.

---

> ### Try It in Your Console
>
> **A named function:**
> ```js
> function greeting(name) {
>     console.log("Hello, " + name + "!");
> }
> greeting("Student");
> greeting("Silver City");
> ```
>
> **The same idea, anonymous** (the style Leaflet event listeners use):
> ```js
> let sayHi = function(name) {
>     console.log("Hi, " + name);
> };
> sayHi("Grant County");
> ```
>
> Notice `greeting` and `sayHi` do the same job two different ways. Leaflet almost always uses the anonymous style, defined right where it's needed instead of named and called separately.

---


## Part 4: Conditionals and Loops

*(IWM Section 3.10)*

**Conditionals** run code only when something is true:

```js
if (LandCover === "Vegetation") {
    color = "green";
} else if (LandCover === "Water") {
    color = "blue";
} else {
    color = "grey";
}
```

Use `===` (strict equality: same value *and* type), not `==`, to avoid unexpected type-conversion bugs.

**Loops** repeat a block of code. The standard `for` loop:

```js
for (let i = 0; i < 5; i++) {
    console.log("Number: " + i);
}
```

For arrays specifically, `.forEach` is usually cleaner:

```js
let cities = ["Silver City", "Hurley", "Santa Clara"];
cities.forEach(function(city) {
    console.log(city);
});
```

You won't need either of these for a bare-minimum map with a handful of hand-placed markers. They become useful the moment you want to add *several* features from a list without repeating the same `L.marker(...)` line over and over, which is exactly the kind of "customizable" behavior worth reaching for once your basic map works.

---

## Part 5: A First Look at JSON

*(IWM Section 3.11)*

You'll notice that a JavaScript object like `{type: "Feature", geometry: {...}}` looks almost identical to the **GeoJSON** format used to describe spatial features. That's not a coincidence: GeoJSON is just JSON (JavaScript Object Notation) written to describe geometry and attributes together. `JSON.parse()` turns a JSON string into a usable JavaScript object; `JSON.stringify()` does the reverse. We won't load external GeoJSON files until Chapter 7; recognizing the object/array shape now will make GeoJSON far less mysterious when it shows up.

---

## Part 6: The DOM and Making Pages Interactive

*(IWM Section 4.2–4.4)*

Everything in Part 1–5 ran "in isolation," with no connection to an actual web page. The bridge between JavaScript and page content is the **Document Object Model (DOM)**: the browser's live, in-memory representation of your HTML, which JavaScript can read and change.

Two DOM tools you already have context for from Lab 3:

```js
document.getElementById("title");            // Selects the element with id="title"
document.getElementById("title").innerHTML;  // Reads its HTML contents
document.getElementById("title").innerHTML = "My New Map";  // Rewrites it
```

This is the same `.innerHTML` technique Leaflet itself uses internally when you build a custom description control (Part 8 below), and it's the mechanism that makes CSS rules on `#title` visibly change your Lab 3 page even though you never opened `map.js`: CSS styles whatever the DOM currently contains, regardless of which technology put it there.

**Event listeners** connect a DOM element to a function that runs on user action:

```js
document.getElementById("myButton").addEventListener("click", function() {
    console.log("Button clicked!");
});
```

Leaflet's map, marker, and layer objects all support this same `.addEventListener`-style pattern for things like clicks on the map itself (Section 6.9); useful to know exists, even if this week's lab centers on the more common `.bindPopup()` shortcut instead.

---

> ### Try It in Your Console
>
> This requires a published webpage open (not a blank tab) so there's a DOM to reach into.
>
> Open any website, then run:
> ```js
> document.body.style.backgroundColor = "lightyellow";
> ```
> The page changes color immediately. This is JavaScript reaching into the live DOM, the same mechanism `.innerHTML` uses. Refresh the page to undo it.
>
> **Tie this back to your own Lab 3 page** Open your published Lab 3 map (it has an element with `id="title"`), then run:
> ```js
> document.getElementById("title");
> document.getElementById("title").innerHTML;
> document.getElementById("title").innerHTML = "My New Map";
> ```
> The title text on the page changes, live, without editing any file. This is the exact tool a custom `L.control` panel uses to build its content later tonight.

---


## Part 7: Building a Leaflet Map From Scratch

*(IWM Section 6.5)*

This is the payoff for everything above. A Leaflet map needs, at minimum:

**1. The Leaflet library, loaded in `<head>`:**

```html
<link rel="stylesheet" href="css/leaflet.css">
<script src="js/leaflet.js"></script>
```

**2. A `<div>` to hold the map, sized to fill the screen:**

```html
<div id="map"></div>
```

```css
body { padding: 0; margin: 0; }
html, body, #map { height: 100%; width: 100%; }
```

**3. A map object, created in your own script, pointed at that `<div>`:**

```js
let map = L.map("map", {center: [31.262218, 34.801472], zoom: 13});
```

The `center` is a `[lat, lng]` array (note: **latitude first**, unlike the `[lng, lat]` order GeoJSON uses, a very common source of "why is my map in the ocean" bugs). `zoom` is a single number; higher means more zoomed in.

**4. A tile layer, the visual basemap underneath everything else:**

```js
L.tileLayer(
    "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
    {attribution: '&copy; OpenStreetMap contributors'}
).addTo(map);
```

`{z}`, `{x}`, `{y}`, and `{s}` are placeholders Leaflet fills in automatically as you pan and zoom. Swap this URL to try a different provider, without changing anything else about your map. Every layer type in Leaflet follows the same pattern you see here: **create the layer, then call `.addTo(map)`.**

---

## Part 8: Adding Your Own Vector Layers

*(IWM Section 6.6–6.8)*

Once the tile layer is in place, add foreground features on top of it. All three take a coordinate array (or array of arrays) plus an optional styling object:

**Markers**: a single point, default blue pin icon:

```js
let pnt = L.marker([31.262218, 34.801472]).addTo(map);
```

**Circle markers**: a point drawn as a customizable circle (radius in *pixels*, so it stays constant size at any zoom):

```js
L.circleMarker([31.262, 34.800], {radius: 12, color: "black", fillColor: "red"}).addTo(map);
```

**Lines**: an array of `[lat, lng]` points, connected in order:

```js
let line = L.polyline(
    [[31.2627, 34.8005], [31.2621, 34.8008]],
    {color: "red", weight: 5}
).addTo(map);
```

**Polygons**: same idea, but closed and fillable (don't repeat the first point at the end):

```js
let pol = L.polygon(
    [[31.2631, 34.8037], [31.2625, 34.8031], [31.2617, 34.8036]],
    {color: "red", fillColor: "yellow", weight: 4}
).addTo(map);
```

### Popups: Attach Info to Any Layer

Any layer object created this way has a `.bindPopup()` method. HTML tags work inside the string:

```js
line.bindPopup("This is the path from <b>our department</b> to the <b>library</b>.");
```

### A custom description panel (`L.control`)

This is the Leaflet-native version of the title/description boxes you hand-built with HTML and CSS in Lab 3, and it uses the exact `.innerHTML` technique from Part 6:

```js
let description = L.control({position: "bottomleft"});
description.onAdd = function() {
    let div = L.DomUtil.create("div", "description");
    div.innerHTML =
        "<p><b>My Map Title</b></p><hr>" +
        "<p>A short description of what this map shows.</p>";
    return div;
};
description.addTo(map);
```

Style the `.description` class in your `<style>` block or CSS file exactly the way you styled `#title` and `#description` in Lab 3, position, background, font, border, all still fair game.

---

## Quick Recap: The Minimum Viable Map

Putting Parts 7–8 together, here is the entire skeleton you're filling in for Lab 4:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Map</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <link rel="stylesheet" href="css/leaflet.css">
    <script src="js/leaflet.js"></script>
    <style>
        body { padding: 0; margin: 0; }
        html, body, #map { height: 100%; width: 100%; }
    </style>
</head>
<body>
    <div id="map"></div>
    <script src="map.js"></script>
</body>
</html>
```

```js
// map.js
let map = L.map("map", {center: [YOUR_LAT, YOUR_LNG], zoom: 13});

L.tileLayer(
    "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
    {attribution: '&copy; OpenStreetMap contributors'}
).addTo(map);

// Your markers, lines, and/or polygons go here, each ending in .addTo(map)
// Your description L.control goes here
```

| Concept | Section | What it does |
|---|---|---|
| `let`, data types | 3.5–3.6 | Store and shape the numbers, text, and coordinate arrays your map runs on |
| Functions & methods | 3.7–3.8 | Package repeatable logic; `.addTo()`, `.bindPopup()` are methods |
| Conditionals & loops | 3.10 | Repeat or condition code; useful once you add several features |
| DOM, `.innerHTML` | 4.2–4.3 | Read/write page content; how `L.control` panels get their text |
| Event listeners | 4.4 | Run code in response to a click |
| `L.map`, `L.tileLayer` | 6.5 | The map object and its basemap |
| `L.marker` / `L.polyline` / `L.polygon` | 6.6 | Your own foreground features |
| `.bindPopup`, `L.control` | 6.7–6.8 | Interactive info on click; a custom description panel |

## Wrap-Up: Discussion 1 (Access & Equity)

Discussion 1 lands this week, held against the map you build and publish yourself in Lab 4, not a hypothetical one. As you finish your build, keep a running note of anything you notice about bandwidth, licensing, device access, or no-login availability, you'll draw on it directly.

## Looking Ahead

Week 6 rebuilds this same map in **MapLibre GL JS**, framed explicitly as "same architecture, newer library," so you can see directly what transfers from this week and what doesn't. Chapter 7 (GeoJSON) is the natural next step beyond this week's hand-typed coordinate arrays, worth a look if you want your Week 5/Lab 4 map to scale beyond a handful of manually placed features.

## Readings & Resources

- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 3: JavaScript Basics
- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 4: JavaScript Interactivity
- Dorman, M. *[Introduction to Web Mapping](https://geobgu.xyz/web-mapping/)*, Chapter 6: Leaflet
- [MDN: JavaScript basics](https://developer.mozilla.org/en-US/docs/Learn/JavaScript)
- [Leaflet documentation](https://leafletjs.com/reference.html)
- [Leaflet Provider Demo](https://leaflet-extras.github.io/leaflet-providers/preview/) (alternative basemaps)

---

## Lab 4: Your First Full Custom Web Map

See Lab 4. Lab 4 takes this lecture's minimum-viable-map skeleton and has you build, style, and publish your own version from a fresh GitHub template, with Discussion 1 tied directly to what you ship.
