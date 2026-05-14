# 03 — How Browsers Work: From Bytes to Pixels

## Learning Objectives

By the end of this page, you will be able to:

- Name the major components inside a web browser
- Describe the critical rendering path step by step
- Explain how HTML is parsed into a DOM tree
- Explain how CSS is parsed into a CSSOM tree
- Describe the layout, paint, and composite steps
- Understand why render-blocking resources slow page loads
- Use browser DevTools to inspect the network waterfall

---

## What Is a Browser?

A **web browser** is a software application that retrieves resources from servers, processes them, and presents them to you as interactive visual pages.

Modern browsers are extraordinarily complex — some of the most sophisticated software ever written. Chrome, Firefox, Safari, and Edge each have millions of lines of code and teams of hundreds of engineers.

### Major Browsers and Their Rendering Engines

Each browser has a **rendering engine** (also called a layout engine) that does the heavy work of turning HTML and CSS into pixels:

| Browser | Rendering Engine | JavaScript Engine |
|---------|----------------|-------------------|
| Chrome | Blink | V8 |
| Edge | Blink | V8 |
| Opera | Blink | V8 |
| Firefox | Gecko | SpiderMonkey |
| Safari | WebKit | JavaScriptCore |

> **Key Point:** Most of the web runs on Blink (Chrome's engine), which is a fork of WebKit. Safari still uses WebKit. This means there are really only two major rendering engines in widespread use today — a consolidation that has implications for web standards.

---

## Browser Architecture: The Key Components

```
┌─────────────────────────────────────────────────────────┐
│                    BROWSER                               │
│                                                          │
│  ┌─────────────────┐   ┌────────────────────────────┐   │
│  │   Browser UI    │   │      Rendering Engine      │   │
│  │ (address bar,   │   │ ┌──────────┐ ┌──────────┐  │   │
│  │  tabs, buttons) │   │ │  HTML    │ │  CSS     │  │   │
│  └─────────────────┘   │ │  Parser  │ │  Parser  │  │   │
│                         │ └──────────┘ └──────────┘  │   │
│  ┌─────────────────┐   │ ┌──────────┐ ┌──────────┐  │   │
│  │ Networking      │   │ │  Layout  │ │  Paint   │  │   │
│  │ (HTTP/HTTPS,    │   │ │  Engine  │ │  Engine  │  │   │
│  │  DNS, cache)    │   │ └──────────┘ └──────────┘  │   │
│  └─────────────────┘   └────────────────────────────┘   │
│                                                          │
│  ┌─────────────────┐   ┌────────────────────────────┐   │
│  │ JavaScript      │   │  Data Storage              │   │
│  │ Engine          │   │  (Cookies, localStorage,   │   │
│  │ (V8, Spider-    │   │   IndexedDB, Cache API)    │   │
│  │  Monkey, etc.)  │   └────────────────────────────┘   │
│  └─────────────────┘                                     │
└─────────────────────────────────────────────────────────┘
```

- **Browser UI:** The address bar, back/forward buttons, bookmarks, tabs — the chrome around the web page
- **Rendering Engine:** Parses HTML and CSS, builds the visual representation
- **Networking:** Handles all HTTP requests, DNS lookups, caching, TLS
- **JavaScript Engine:** Parses, compiles, and executes JavaScript code
- **Data Storage:** Persists cookies, local storage, cached resources across sessions

---

## The Critical Rendering Path

The **critical rendering path** is the sequence of steps the browser takes to convert HTML, CSS, and JavaScript into pixels on the screen. Understanding this is fundamental to web performance.

```
HTML Bytes
    │
    ▼
1. Parse HTML → DOM Tree
    │
2. Parse CSS → CSSOM Tree
    │
    ├── (DOM + CSSOM) → Render Tree
    │
3. Layout (Reflow) → Box positions and sizes
    │
4. Paint → Pixels to layers
    │
5. Composite → Layers assembled, sent to GPU, displayed
    ▼
Pixels on Screen
```

Let's walk through each step.

---

## Step 1: Parsing HTML → The DOM Tree

When the browser receives HTML bytes from the server, it must convert them into something it can work with.

### Bytes → Characters → Tokens → Nodes → DOM

The process has several micro-steps:

**1. Encoding:** Raw bytes (e.g., `3C 68 31 3E`) are decoded based on the `charset` declared in the HTML (usually UTF-8) into characters (`<h1>`).

**2. Tokenizing:** The stream of characters is broken into **tokens** — meaningful units like start tags, end tags, attributes, and text:
```
<h1 class="title">Hello</h1>
→ [StartTag: h1, class="title"] [Text: "Hello"] [EndTag: h1]
```

**3. Tree Construction:** Tokens become **nodes** and are assembled into a tree — the **DOM (Document Object Model)**:

```html
<html>
  <head>
    <title>My Page</title>
  </head>
  <body>
    <h1 class="title">Hello</h1>
    <p>Welcome.</p>
  </body>
</html>
```

Becomes:

```
Document
└── html
    ├── head
    │   └── title
    │       └── "My Page"  (text node)
    └── body
        ├── h1 [class="title"]
        │   └── "Hello"
        └── p
            └── "Welcome."
```

Each box in this tree is a **node**. The DOM is a live, in-memory representation of the page that JavaScript can read and modify in real time.

### HTML Parsing Is Forgiving

Unlike most programming languages, HTML parsers are intentionally tolerant of errors. If you forget a closing tag, the browser tries to fix it. If you put a `<p>` inside a `<table>`, the browser may move it. This is by design — the web would break constantly if every malformed HTML page crashed.

> **Analogy:** The HTML parser is like a very patient editor who receives a manuscript full of typos and formatting errors. Rather than throwing it back, the editor silently fixes the obvious mistakes and does their best with the rest.

---

## Step 2: Parsing CSS → The CSSOM Tree

While parsing HTML, the browser encounters `<link rel="stylesheet">` tags and `<style>` blocks. It fetches and parses CSS to build the **CSSOM (CSS Object Model)** — a tree similar in structure to the DOM but representing styling rules.

```css
body { font-size: 16px; }
h1 { color: navy; font-size: 2em; }
h1.title { font-weight: bold; }
p { margin: 1em 0; }
```

Becomes a tree of computed style rules associated with selectors.

### CSS Is Render-Blocking

**Important:** The browser will NOT render any content until it has finished downloading and parsing all CSS. This is called **render-blocking** behavior.

**Why?** Because if the browser started painting before CSS was ready, you'd see an ugly flash of unstyled content (FOUC) before styles applied. It's better to wait.

**Implication for developers:** CSS files in `<head>` block rendering. Keeping CSS lean and loading it efficiently is critical to fast page loads.

---

## Step 3: Building the Render Tree

The browser combines the DOM and CSSOM into a **Render Tree** — which contains only the nodes that will actually be painted (i.e., visible elements with their computed styles).

- `display: none` nodes are **excluded** from the render tree (they exist in the DOM but not the render tree)
- `visibility: hidden` nodes are **included** (they take up space but are transparent)
- `<head>` content is excluded (no visual output)
- Text nodes that are visible are included

Each node in the render tree has its computed style — all CSS rules resolved, inherited values applied, percentages calculated.

---

## Step 4: Layout (Reflow)

The browser now knows **what** to draw and **how it looks** (from the render tree), but not **where** to draw it.

**Layout** (also called **reflow**) calculates the exact position and size of every element on the page — in CSS pixels.

- Where does this `<div>` start on the page?
- How wide is this paragraph?
- How tall does this container need to be to fit its children?

Layout uses the **box model** — every element is a rectangular box with content, padding, border, and margin. (We'll cover this in detail in the CSS section.)

Layout is expensive. Anything that changes an element's size or position — adding text, changing font size, resizing the window — triggers a reflow of the affected elements.

---

## Step 5: Paint

**Paint** converts the render tree (with computed positions and styles) into actual pixels on one or more **layers**:

- Backgrounds
- Text
- Images
- Borders, shadows

The browser may split the page into multiple **paint layers** for performance — elements that animate or change frequently get their own layer so they can be updated independently without repainting everything.

Paint is the step where CSS properties like `color`, `background`, `border`, `box-shadow`, and `border-radius` are applied.

---

## Step 6: Compositing

**Compositing** assembles all the painted layers in the correct order and sends the final result to the GPU to be displayed on screen.

The GPU takes the layers and composites them — similar to how photo editing software merges image layers — and outputs the final frame.

**GPU-accelerated properties** — properties that only require compositing, not layout or paint — are the fastest to animate: `transform`, `opacity`. This is why CSS animations using `transform: translateX()` are smooth while `width` or `top` animations can be janky.

> **Performance mental model:**
> - Changing `color` or `background` → triggers **paint** (fast)
> - Changing `width`, `height`, `margin`, `padding` → triggers **layout** + paint (slow)
> - Changing `transform`, `opacity` → only triggers **compositing** (very fast — GPU-only)

---

## JavaScript and the Rendering Pipeline

JavaScript can read and modify the DOM and CSSOM, which means it can trigger any step in the pipeline.

### JavaScript Is Also Render-Blocking (by Default)

When the browser encounters a `<script>` tag, it **pauses HTML parsing**, downloads the script, executes it, and only then continues parsing.

**Why?** Because JavaScript can modify the DOM (via `document.write()`, or `innerHTML`) — so the browser can't safely continue building the DOM until the script has run.

**Solutions:**
- `<script defer>` — download in parallel, execute after HTML is fully parsed
- `<script async>` — download in parallel, execute as soon as downloaded (doesn't wait for HTML)
- Put scripts at the end of `<body>` — HTML is parsed first, then scripts run

Best practice for modern web development: use `defer` on all scripts in `<head>`.

---

## Seeing It in Action: DevTools Network Tab

The best way to understand all of this is to watch it happen:

1. Open Chrome or Firefox
2. Press `F12` (Windows) or `Cmd+Option+I` (macOS) to open DevTools
3. Click the **Network** tab
4. Check the **Disable cache** checkbox
5. Navigate to any website (try `https://example.com`)

You'll see:
- Each request as a row
- The **waterfall** — a timeline showing when each request started and how long it took
- Color coding: blue = HTML, purple = CSS, yellow = JS, green = images
- The total number of requests and page load time at the bottom

Try loading a complex site like `https://nytimes.com` and count how many requests it makes. You may be surprised.

**Further Reading:**
- [How Browsers Work — web.dev (Google)](https://web.dev/articles/howbrowserswork) — The definitive deep-dive article
- [Critical Rendering Path — web.dev](https://web.dev/learn/performance/understanding-the-critical-path)
- [How the Web Works — MDN](https://developer.mozilla.org/en-US/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works)
- [Rendering Performance — web.dev](https://web.dev/articles/rendering-performance)

---

## Check Your Understanding

1. Why does the browser need to parse both HTML and CSS before it can render anything?
2. What is the difference between the DOM and the Render Tree?
3. A developer wants to animate an element sliding across the screen smoothly. Should they animate `left` (position) or `transform: translateX()`? Why?
4. What does "render-blocking" mean? Which resources are render-blocking by default?
5. What does the `defer` attribute on a `<script>` tag do, and why is it the recommended approach?

---

*Previous: [02 — Clients and Servers](02-clients-and-servers.md)*
*Next: [04 — The DOM](04-the-dom.md)*
