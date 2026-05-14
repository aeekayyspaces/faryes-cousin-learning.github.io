# 08 — HTML5, CSS3, and the Evolution of the Web

## Learning Objectives

By the end of this page, you will be able to:

- Describe the major versions of HTML and what changed in each
- Explain what HTML5 added and why it mattered
- Use HTML5 semantic elements, form inputs, audio, and video
- Describe the history of CSS and what CSS3 introduced
- Use CSS variables, transitions, animations, and Grid layout
- Understand what W3C and WHATWG are and how web standards are made
- Apply modern CSS to a real project

---

## The Evolution of HTML

### HTML 1.0 — 1993

The very first version of HTML, described by Tim Berners-Lee. It had around **20 elements**:
- Headings (`<h1>`–`<h6>`)
- Paragraphs (`<p>`)
- Links (`<a href>`)
- Lists (`<ul>`, `<ol>`, `<li>`)
- Images (`<img>`) — added shortly after

No CSS. No forms. No tables. Just text with links and basic structure. This was enough to revolutionize information sharing.

### HTML 2.0 — 1995

The first formal standard (RFC 1866). Added:
- Forms (`<form>`, `<input>`, `<select>`, `<textarea>`)
- Tables (basic)

### HTML 3.2 — 1997 (W3C)

- Tables with full features
- Applets (Java in the browser — now gone)
- `<font>` and `<center>` tags for visual formatting (now deprecated)
- This era marked the beginning of "tag soup" — HTML used to control appearance, which CSS should handle

### HTML 4.01 — 1999

A major revision:
- Strict, Transitional, and Frameset document types
- CSS encouraged (deprecated visual tags like `<font>`)
- Scripting and accessibility improvements
- Frames (`<frameset>`) — popular in late 90s for sidebar navigation, now gone

### XHTML 1.0 — 2000

An attempt to reformulate HTML as XML:
- Required lowercase tags
- All attributes must be quoted
- All elements must be closed (even `<br />`)
- XHTML 2.0 was planned but abandoned in 2009 — too strict, breaking millions of existing web pages

### The Browser Wars and Fragmentation: 1990s–2000s

During the 1990s, Microsoft and Netscape competed aggressively. Each added proprietary HTML tags that only worked in their browser. Developers wrote code for "best viewed in Netscape Navigator" or "best viewed in Internet Explorer 6."

Internet Explorer 6 (released 2001) reached 95% market share and Microsoft largely abandoned web standards development for years. The web stagnated. Developers had to write separate codebases for different browsers.

### WHATWG and the Return to Progress

In 2004, Apple, Mozilla, and Opera engineers — frustrated with W3C's direction toward XHTML 2.0 — formed the **WHATWG** (Web Hypertext Application Technology Working Group) and began working on HTML5 based on existing practices rather than theoretical purity.

The W3C eventually adopted the WHATWG spec and HTML5 became the new standard.

### HTML5 — 2014 (Official) / 2008 (Draft)

HTML5 is not just a new version — it's a complete platform for web applications.

---

## What HTML5 Added

### Semantic Elements

HTML5 introduced meaningful structural elements (we covered these in Section 5):

```html
<header>   <nav>      <main>
<article>  <section>  <aside>
<footer>   <figure>   <figcaption>
<time>     <mark>     <details>
<summary>  <dialog>
```

These replaced the endless `<div id="header">`, `<div class="nav">` patterns.

### New Form Input Types

HTML5 drastically improved forms with new `type` values on `<input>`:

```html
<!-- Email — validated automatically, shows email keyboard on mobile -->
<input type="email" placeholder="you@example.com">

<!-- URL — validated, appropriate keyboard on mobile -->
<input type="url" placeholder="https://example.com">

<!-- Number — with min/max/step; shows number keyboard on mobile -->
<input type="number" min="0" max="100" step="5" value="50">

<!-- Range slider -->
<input type="range" min="0" max="100" value="50">

<!-- Date picker -->
<input type="date">

<!-- Time picker -->
<input type="time">

<!-- Color picker -->
<input type="color" value="#7c3aed">

<!-- Search field (styled differently by some browsers) -->
<input type="search" placeholder="Search...">

<!-- Phone — appropriate keyboard on mobile -->
<input type="tel">
```

These inputs provide built-in validation, appropriate mobile keyboards, and native UI controls — without any JavaScript.

### Audio and Video (Native Media)

Before HTML5, playing audio or video required **Adobe Flash** or Windows Media Player plugins. HTML5 made media first-class:

```html
<!-- Audio player -->
<audio controls>
  <source src="song.mp3" type="audio/mpeg">
  <source src="song.ogg" type="audio/ogg">
  Your browser doesn't support audio.
</audio>

<!-- Video player -->
<video controls width="640" height="360" poster="thumbnail.jpg">
  <source src="video.mp4" type="video/mp4">
  <source src="video.webm" type="video/webm">
  Your browser doesn't support video.
</video>
```

Multiple `<source>` elements provide fallbacks — the browser uses the first format it supports.

### Canvas — Draw with JavaScript

`<canvas>` provides a JavaScript-drawable surface for 2D graphics:

```html
<canvas id="myCanvas" width="400" height="300"></canvas>
```

```javascript
const canvas = document.getElementById("myCanvas");
const ctx = canvas.getContext("2d");

// Draw a filled rectangle
ctx.fillStyle = "#7c3aed";
ctx.fillRect(50, 50, 200, 100);

// Draw text
ctx.fillStyle = "white";
ctx.font = "24px Arial";
ctx.fillText("Hello Canvas!", 70, 110);

// Draw a circle
ctx.beginPath();
ctx.arc(300, 150, 60, 0, 2 * Math.PI);
ctx.fillStyle = "#10b981";
ctx.fill();
```

Used for: games, data visualizations, image editing, signatures.

### Local Storage and Session Storage

Store data in the browser without cookies:

```javascript
// Store data
localStorage.setItem("username", "alex");
localStorage.setItem("theme", "dark");

// Retrieve data
const username = localStorage.getItem("username");

// Remove data
localStorage.removeItem("theme");

// Clear everything
localStorage.clear();
```

- `localStorage` — persists until explicitly cleared (even after browser restart)
- `sessionStorage` — cleared when the tab is closed

Useful for: saving user preferences, caching data, offline capabilities.

### Geolocation API

```javascript
navigator.geolocation.getCurrentPosition(function(position) {
  console.log("Latitude:", position.coords.latitude);
  console.log("Longitude:", position.coords.longitude);
});
```

The browser asks the user for permission before sharing location.

### Web Workers

Run JavaScript in a background thread — without blocking the main thread:

```javascript
// main.js
const worker = new Worker("worker.js");
worker.postMessage({ data: bigArray });
worker.onmessage = function(e) {
  console.log("Worker result:", e.data);
};

// worker.js
onmessage = function(e) {
  const result = heavyCalculation(e.data.data);
  postMessage(result);
};
```

Useful for CPU-intensive work (image processing, cryptography, data analysis) that would otherwise freeze the UI.

---

## HTML Living Standard

An important modern note: HTML is now a **Living Standard** maintained by WHATWG rather than a versioned specification. There is no "HTML6" — the standard evolves continuously. New features are added as browsers implement them.

---

## The Evolution of CSS

### CSS1 — 1996

The first CSS standard:
- Fonts, colors, margins, padding
- Basic selectors (element, class, ID)
- 50 properties total

### CSS2 — 1998 / CSS 2.1 — 2011

- Positioned layout (`position: absolute/relative/fixed`)
- Z-index
- Media types (screen, print)
- More selectors

### CSS3 — 2011 to Present

CSS3 is **not a monolithic version** — it is a collection of independent **modules**, each developed and released on its own timeline. The term "CSS3" loosely refers to all modern CSS features introduced after CSS2.1.

Major CSS3 modules include:
- Selectors Level 3
- Media Queries Level 3 (responsive design)
- Flexbox
- Grid Layout
- Transitions and Animations
- Transforms
- Custom Properties (Variables)
- Gradients
- Box Shadow, Text Shadow
- Border Radius
- Web Fonts (`@font-face`)

---

## CSS Variables (Custom Properties)

One of the most powerful CSS3 additions — define values once, reuse everywhere:

```css
:root {
  --primary-color: #7c3aed;
  --secondary-color: #10b981;
  --font-size-base: 16px;
  --spacing-unit: 8px;
  --border-radius: 8px;
  --shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.button {
  background: var(--primary-color);
  padding: calc(var(--spacing-unit) * 1.5) calc(var(--spacing-unit) * 3);
  border-radius: var(--border-radius);
}

.card {
  border-radius: var(--border-radius);
  box-shadow: var(--shadow);
}

/* Theme switching with one change */
.dark-theme {
  --primary-color: #a78bfa;
  --background: #1a1a2e;
  --text: #e0e0ff;
}
```

Change `--primary-color` in one place and everything using it updates. This makes theming, design systems, and dark mode trivial.

---

## CSS Transitions

Animate property changes smoothly:

```css
.button {
  background-color: #7c3aed;
  transform: scale(1);
  transition: background-color 0.2s ease, transform 0.1s ease;
}

.button:hover {
  background-color: #6d28d9;
  transform: scale(1.05);
}
```

The `transition` property says: "When `background-color` changes, animate it over 0.2s. When `transform` changes, animate it over 0.1s."

No JavaScript needed — the browser handles the animation.

### Transition syntax:
```css
transition: property duration timing-function delay;

/* Multiple transitions */
transition: color 0.2s ease, opacity 0.3s ease-in-out 0.1s;
```

---

## CSS Animations

For more complex, ongoing animations:

```css
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.card {
  animation: fadeIn 0.4s ease-out forwards;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}

.loading-spinner {
  animation: spin 1s linear infinite;
}
```

---

## CSS Grid Layout

**CSS Grid** is the most powerful layout system in CSS — designed for two-dimensional layouts (rows AND columns simultaneously):

```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);   /* 3 equal columns */
  grid-template-rows: auto;
  gap: 16px;
}

/* Or a more complex layout: */
.page-layout {
  display: grid;
  grid-template-columns: 250px 1fr;        /* sidebar + main */
  grid-template-rows: auto 1fr auto;       /* header, content, footer */
  grid-template-areas:
    "header  header"
    "sidebar main"
    "footer  footer";
  min-height: 100vh;
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }
.footer  { grid-area: footer; }
```

> **Flexbox vs Grid:** Use **Flexbox** for one-dimensional layouts (a row of buttons, a navigation bar). Use **Grid** for two-dimensional layouts (a full page layout, a card grid, a data table).

---

## Web Fonts

Before CSS3, you could only use "web-safe" fonts installed on most computers (Arial, Georgia, Times New Roman). CSS3's `@font-face` and services like Google Fonts changed this:

```html
<!-- Google Fonts (fastest, most popular) -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
```

```css
body {
  font-family: 'Inter', sans-serif;
}
```

Or host your own fonts:
```css
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/myfont.woff2') format('woff2'),
       url('/fonts/myfont.woff')  format('woff');
  font-weight: 400;
  font-style: normal;
}
```

---

## Advanced CSS Features (CSS4 and Beyond)

CSS continues to evolve. Recently added features:

### Container Queries

Like media queries, but respond to the **parent container's size** instead of the viewport:

```css
.card-container {
  container-type: inline-size;
}

@container (min-width: 400px) {
  .card {
    flex-direction: row;
  }
}
```

### CSS Nesting (New)

Write nested rules like Sass without a preprocessor:

```css
.card {
  padding: 1rem;
  
  &:hover {
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
  }
  
  & h2 {
    color: navy;
  }
}
```

### Logical Properties

Write styles that adapt to text direction (left-to-right vs right-to-left):

```css
/* Instead of: */
margin-left: 1rem;
padding-right: 1rem;

/* Use: */
margin-inline-start: 1rem;   /* left in LTR, right in RTL */
padding-inline-end: 1rem;
```

---

## Web Standards Organizations

### W3C — World Wide Web Consortium

Founded by Tim Berners-Lee in 1994. Develops web standards including:
- HTML (historical)
- CSS
- SVG
- Accessibility guidelines (WCAG)
- Web APIs

Standards go through a multi-year process: Working Draft → Candidate Recommendation → Recommendation (official standard).

### WHATWG — Web Hypertext Application Technology Working Group

Founded in 2004 by browser vendors. Maintains:
- **HTML Living Standard** (the active HTML spec, continuously updated)
- **Fetch** (the modern fetch API spec)
- **URL** standard

WHATWG and W3C coordinate but have different processes. For HTML, the WHATWG Living Standard is the authoritative spec that browsers implement.

### TC39 — Technical Committee 39 (JavaScript)

Governs the **ECMAScript** standard (the specification JavaScript implements). Introduces new JavaScript features through a proposal process:
- Stage 0: Strawman
- Stage 1: Proposal
- Stage 2: Draft
- Stage 3: Candidate
- Stage 4: Finished (included in next ECMAScript release)

Browsers implement Stage 3+ proposals. New ECMAScript editions release annually. ES2015 (ES6) was the landmark edition that modernized JavaScript significantly.

**Further Reading:**
- [HTML5 Features — MDN](https://developer.mozilla.org/en-US/docs/Web/HTML)
- [CSS3 — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS)
- [CSS Grid Complete Guide — CSS-Tricks](https://css-tricks.com/snippets/css/complete-guide-grid/)
- [Can I Use](https://caniuse.com/) — Check browser support for any HTML/CSS/JS feature
- [W3C](https://www.w3.org/)
- [WHATWG](https://whatwg.org/)

---

## Check Your Understanding

1. Why was HTML5 a significant departure from previous HTML versions?
2. What problem did the WHATWG's formation solve, and why did they abandon the XHTML 2.0 approach?
3. What is the advantage of HTML5's native `<video>` and `<audio>` elements over plugins like Flash?
4. What is a CSS variable and why is it useful for theming?
5. What is the difference between CSS Transitions and CSS Animations?
6. When would you use CSS Grid versus Flexbox?

---

*Previous: [07 — Introduction to JavaScript](07-introduction-to-javascript.md)*
*Next: [09 — Quiz and Checkpoints](09-quiz-and-checkpoints.md)*
