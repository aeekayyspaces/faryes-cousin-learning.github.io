# 06 — Introduction to CSS: Making It Look Good

## Learning Objectives

By the end of this page, you will be able to:

- Explain what CSS is and how it connects to HTML
- Write CSS using element, class, and ID selectors
- Understand the cascade and specificity
- Use the box model to control spacing and sizing
- Apply common CSS properties: color, typography, spacing, borders
- Create a basic layout using Flexbox
- Write a simple media query for responsive design

---

## What Is CSS?

**CSS (Cascading Style Sheets)** is the language that controls how HTML elements look — their colors, fonts, sizes, positions, and animations.

Without CSS, every web page looks like a plain text document with blue links and a default serif font. CSS transforms that skeleton into a polished visual experience.

> **Analogy:** If HTML is the skeleton of a building, CSS is everything that makes it livable and attractive — the paint colors, flooring, furniture, lighting, and window treatments. The structure doesn't change; the appearance transforms completely.

A key word in the name is **cascading** — when multiple rules could apply to the same element, CSS has a defined system (the cascade) to determine which rule wins. We'll cover this shortly.

---

## Three Ways to Add CSS to a Page

### 1. External Stylesheet (Recommended)

Create a separate `.css` file and link it from your HTML `<head>`:

```html
<!-- in index.html -->
<head>
  <link rel="stylesheet" href="styles.css">
</head>
```

```css
/* in styles.css */
h1 {
  color: navy;
}
```

**Best practice.** Keeps HTML and CSS separate. One CSS file can style many HTML pages. Browsers cache the CSS file — it loads once and is reused on every page.

### 2. Internal Style Block

Write CSS in a `<style>` tag in the `<head>`:

```html
<head>
  <style>
    h1 {
      color: navy;
    }
  </style>
</head>
```

Useful for small one-page projects or prototyping. Not ideal for multi-page sites.

### 3. Inline Styles

Apply styles directly to an HTML element with the `style` attribute:

```html
<h1 style="color: navy; font-size: 2rem;">Page Title</h1>
```

Highest specificity. Hard to maintain — styles are scattered through HTML. Use sparingly, only when necessary.

---

## CSS Syntax

A CSS **rule** has two parts: a **selector** and a **declaration block**:

```css
selector {
  property: value;
  property: value;
}
```

Example:
```css
h1 {
  color: navy;
  font-size: 2rem;
  font-weight: bold;
}
```

- `h1` — the selector (which elements to style)
- `color`, `font-size`, `font-weight` — properties
- `navy`, `2rem`, `bold` — values
- Each `property: value` pair is a **declaration**, ending with `;`

---

## Selectors

Selectors determine **which** HTML elements a rule applies to.

### Element Selector

Targets all elements of a given type:

```css
p {
  line-height: 1.6;
}

h2 {
  color: #333;
}
```

### Class Selector

Targets elements with a specific `class` attribute. Uses `.` prefix:

```css
.card {
  background: white;
  border-radius: 8px;
  padding: 1rem;
}

.highlight {
  background-color: yellow;
}
```

```html
<div class="card">I'm a card!</div>
<p>This word is <span class="highlight">highlighted</span>.</p>
```

One element can have multiple classes: `<div class="card featured">` — both `.card` and `.featured` rules apply.

### ID Selector

Targets the one element with a specific `id` attribute. Uses `#` prefix:

```css
#site-title {
  font-size: 3rem;
  color: rebeccapurple;
}
```

```html
<h1 id="site-title">My Website</h1>
```

IDs should be unique — only one element per page should have a given ID.

> **Best practice:** Prefer **classes** over IDs for styling. IDs have high specificity (making them hard to override) and can only be used once per page. Use classes — they're reusable and more flexible.

### Descendant Selector

Targets elements **inside** another element (any depth):

```css
nav a {
  color: white;
  text-decoration: none;
}
```

This styles `<a>` elements only when they are inside a `<nav>`.

### Direct Child Selector (`>`)

```css
ul > li {
  color: navy;
}
```

Targets `<li>` elements that are **direct children** of `<ul>` — not grandchildren.

### Multiple Selectors

Apply the same rule to multiple selectors by separating with commas:

```css
h1, h2, h3 {
  font-family: 'Georgia', serif;
}
```

### Pseudo-Classes

Style elements based on their **state**:

```css
a:hover {
  color: orange;
  text-decoration: underline;
}

button:active {
  transform: scale(0.98);
}

input:focus {
  outline: 2px solid blue;
}

li:first-child {
  font-weight: bold;
}

li:nth-child(even) {
  background: #f0f0f0;
}
```

### Pseudo-Elements

Style a specific **part** of an element:

```css
p::first-line {
  font-weight: bold;
}

p::before {
  content: "→ ";
  color: navy;
}
```

---

## The Cascade and Specificity

When multiple CSS rules could apply to the same element, the browser uses a priority system to decide which one wins.

### Cascade Order (Last Rule Wins — at Equal Specificity)

```css
p { color: red; }
p { color: blue; }  /* This wins — it comes later */
```

### Specificity

More specific selectors override less specific ones, regardless of order:

| Selector Type | Specificity Points |
|--------------|-------------------|
| Inline style | 1000 |
| ID (`#id`) | 100 |
| Class (`.class`), pseudo-class, attribute | 10 |
| Element (`p`, `h1`), pseudo-element | 1 |

Examples:
```css
p           { color: black; }   /* specificity: 1 */
.text       { color: blue; }    /* specificity: 10 — wins over element */
#intro      { color: green; }   /* specificity: 100 — wins over class */
```

```html
<p id="intro" class="text">What color am I?</p>
<!-- Answer: green — the ID rule has highest specificity -->
```

> **Analogy:** Think of specificity like VIP levels at a club. General admission (element selectors) gets in, but members (class selectors) go ahead of them, and VIPs (ID selectors) go ahead of everyone. The most specific rule always wins.

### `!important` (Use Sparingly)

```css
p { color: red !important; }
```

`!important` overrides everything, including higher-specificity rules. It's a last resort — overusing it makes CSS impossible to maintain.

### Inheritance

Many CSS properties are **inherited** — child elements inherit values from their parent:

```css
body {
  font-family: 'Arial', sans-serif;
  color: #333;
}
/* All text elements inside body will use this font and color
   unless overridden */
```

Properties like `color`, `font-family`, `font-size` are inherited.
Properties like `border`, `margin`, `padding`, `background` are NOT inherited.

---

## Colors in CSS

CSS offers many ways to specify colors:

```css
/* Named colors */
color: red;
color: navy;
color: rebeccapurple;

/* Hex (red, green, blue) */
color: #ff0000;   /* red */
color: #1a1a2e;   /* dark navy */
color: #6bcb77;   /* soft green */

/* RGB */
color: rgb(255, 0, 0);          /* red */
color: rgb(26, 26, 46);         /* dark navy */

/* RGBA (with transparency: 0 = invisible, 1 = solid) */
color: rgba(0, 0, 0, 0.5);     /* 50% transparent black */

/* HSL (hue 0-360, saturation %, lightness %) */
color: hsl(220, 90%, 50%);     /* bright blue */
color: hsl(0, 0%, 20%);        /* dark gray */

/* CSS custom property (variable) */
:root { --brand-color: #5b21b6; }
color: var(--brand-color);
```

---

## The Box Model

Every HTML element is a rectangular box. The **box model** describes four areas around that box:

```
┌─────────────────────────────────┐  ← margin edge
│           MARGIN                │
│  ┌───────────────────────────┐  │  ← border edge
│  │          BORDER           │  │
│  │  ┌─────────────────────┐  │  │  ← padding edge
│  │  │       PADDING       │  │  │
│  │  │  ┌───────────────┐  │  │  │  ← content edge
│  │  │  │    CONTENT    │  │  │  │
│  │  │  │  (text, img)  │  │  │  │
│  │  │  └───────────────┘  │  │  │
│  │  └─────────────────────┘  │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

- **Content** — the actual text, image, or child elements
- **Padding** — space between content and border (inside the element)
- **Border** — a visible edge around the padding
- **Margin** — space outside the border (between this element and its neighbors)

```css
.box {
  width: 300px;
  height: 200px;

  padding: 20px;             /* all sides */
  padding: 10px 20px;        /* top/bottom left/right */
  padding: 10px 20px 10px 20px; /* top right bottom left (clockwise) */

  border: 2px solid navy;
  border-radius: 8px;        /* rounded corners */

  margin: 16px;
  margin: 16px auto;         /* center horizontally with auto left/right */
}
```

### `box-sizing: border-box`

By default, `width` and `height` apply to the **content** only. So a `300px` wide element with `20px` padding is actually `340px` wide (300 + 20 + 20).

`box-sizing: border-box` makes `width` and `height` include padding and border — much more intuitive:

```css
/* Apply to everything — a nearly universal best practice */
*, *::before, *::after {
  box-sizing: border-box;
}
```

---

## Typography

```css
body {
  font-family: 'Inter', Arial, sans-serif; /* list of fallbacks */
  font-size: 16px;                          /* base size */
  line-height: 1.6;                         /* 1.6x font-size — comfortable reading */
  color: #1a1a2e;
}

h1 {
  font-size: 2.5rem;    /* rem = relative to root font size */
  font-weight: 700;     /* 400 = regular, 700 = bold */
  letter-spacing: -0.5px;
  margin-bottom: 0.5em; /* em = relative to current font-size */
}

p {
  font-size: 1rem;
  max-width: 65ch;      /* ch = width of "0" character; ~65 chars per line is ideal */
  margin-bottom: 1rem;
}

.subtitle {
  font-size: 1.25rem;
  color: #666;
  font-style: italic;
}
```

### Units Reference

| Unit | What It's Relative To | Use Case |
|------|----------------------|----------|
| `px` | Screen pixels (absolute) | Borders, fixed sizes |
| `rem` | Root (`<html>`) font size | Font sizes, spacing |
| `em` | Current element's font size | Component-relative spacing |
| `%` | Parent element | Widths, heights |
| `vw` / `vh` | Viewport width / height | Full-screen layouts |
| `ch` | Width of the "0" character | Line length control |

---

## Layouts: Flexbox

**Flexbox** (Flexible Box Layout) is the modern way to arrange elements in one direction (row or column).

```css
.container {
  display: flex;             /* activate flexbox */
  flex-direction: row;       /* row (default) or column */
  justify-content: space-between; /* alignment on main axis */
  align-items: center;       /* alignment on cross axis */
  gap: 16px;                 /* space between items */
}
```

```html
<div class="container">
  <div class="item">One</div>
  <div class="item">Two</div>
  <div class="item">Three</div>
</div>
```

### `justify-content` values (horizontal in a row):

| Value | Effect |
|-------|--------|
| `flex-start` | Pack items to the left (default) |
| `flex-end` | Pack items to the right |
| `center` | Center items |
| `space-between` | First item at start, last at end, even space between |
| `space-around` | Even space around each item |
| `space-evenly` | Exactly equal spacing everywhere |

### `align-items` values (vertical in a row):

| Value | Effect |
|-------|--------|
| `flex-start` | Align to top |
| `flex-end` | Align to bottom |
| `center` | Center vertically |
| `stretch` | Stretch to fill container height (default) |

### Flex item properties:

```css
.item {
  flex: 1;           /* grow and shrink equally; distribute space */
}
.sidebar {
  flex: 0 0 250px;   /* don't grow, don't shrink, fixed 250px */
}
.main-content {
  flex: 1;           /* take up all remaining space */
}
```

> **Analogy:** Flexbox is like packing boxes into a shelf. You tell the shelf how many boxes to put in a row, how far apart they should be, and whether they should stretch to fill the shelf height. The boxes adjust automatically.

---

## Responsive Design and Media Queries

A **responsive** website adapts to different screen sizes — desktop, tablet, phone.

**Media queries** apply CSS only when certain conditions are met:

```css
/* Default styles (mobile-first: small screens first) */
.container {
  padding: 16px;
  flex-direction: column;
}

/* Tablet (768px and wider) */
@media (min-width: 768px) {
  .container {
    padding: 32px;
    flex-direction: row;
  }
}

/* Desktop (1024px and wider) */
@media (min-width: 1024px) {
  .container {
    max-width: 1200px;
    margin: 0 auto;
  }
}
```

---

## Hands-On: Style Your About Me Page

Take the `about-me.html` from the previous section and create a `styles.css` file in the same folder. Link it in your HTML `<head>`:

```html
<link rel="stylesheet" href="styles.css">
```

Now add this CSS in `styles.css`:

```css
/* Reset and base styles */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Segoe UI', Arial, sans-serif;
  font-size: 16px;
  line-height: 1.6;
  color: #1a1a2e;
  background-color: #f4f4f8;
}

/* Header and navigation */
header {
  background-color: #1a1a2e;
  padding: 1rem 2rem;
}

nav {
  display: flex;
  gap: 1.5rem;
}

nav a {
  color: #e0e0ff;
  text-decoration: none;
  font-weight: 500;
}

nav a:hover {
  color: #a78bfa;
}

/* Main content */
main {
  max-width: 800px;
  margin: 2rem auto;
  padding: 0 1rem;
}

/* Sections */
section {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  margin-bottom: 1.5rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

/* Headings */
h1 {
  font-size: 2.2rem;
  color: #1a1a2e;
  margin-bottom: 1rem;
}

h2 {
  font-size: 1.5rem;
  color: #4c1d95;
  margin-bottom: 1rem;
}

/* Profile image */
img {
  border-radius: 50%;
  border: 4px solid #a78bfa;
  display: block;
  margin-top: 1.5rem;
}

/* Lists */
ul {
  padding-left: 1.5rem;
}

li {
  margin-bottom: 0.5rem;
}

/* Form styles */
form {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

label {
  font-weight: 600;
  color: #374151;
}

input,
textarea {
  padding: 0.5rem 0.75rem;
  border: 1px solid #d1d5db;
  border-radius: 4px;
  font-size: 1rem;
  font-family: inherit;
}

input:focus,
textarea:focus {
  outline: none;
  border-color: #7c3aed;
  box-shadow: 0 0 0 3px rgba(124, 58, 237, 0.15);
}

button {
  background-color: #7c3aed;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  align-self: flex-start;
}

button:hover {
  background-color: #6d28d9;
}

/* Footer */
footer {
  text-align: center;
  padding: 2rem;
  color: #6b7280;
  font-size: 0.875rem;
}

/* Responsive: stack on mobile */
@media (min-width: 600px) {
  main {
    padding: 0 2rem;
  }
}
```

Refresh your browser — you now have a styled personal page!

**Full solution with variations:** [solutions/html-css-project-solutions.md](solutions/html-css-project-solutions.md)

**Further Reading:**
- [CSS Basics — MDN](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics)
- [CSS Reference — MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference)
- [Flexbox Froggy](https://flexboxfroggy.com/) — Interactive game to learn Flexbox
- [CSS Tricks — Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [The CSS Box Model — MDN](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model)

---

## Check Your Understanding

1. What is the difference between a class selector (`.card`) and an ID selector (`#header`)? When should you use each?
2. A `<p>` element has `color: red` from an element rule and `color: blue` from a class rule. Which wins and why?
3. Describe the four parts of the CSS box model.
4. What does `box-sizing: border-box` change, and why is it almost universally applied?
5. Using Flexbox, how would you center a set of items both horizontally and vertically?
6. What is a media query used for? Write one that applies styles only on screens wider than 768px.

---

*Previous: [05 — Introduction to HTML](05-introduction-to-html.md)*
*Next: [07 — Introduction to JavaScript](07-introduction-to-javascript.md)*
