# Solutions — 09 Section Quiz

Answer key for [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md).

---

## Part 1: Multiple Choice — Answer Key

| # | Answer | Explanation |
|---|--------|-------------|
| 1 | **C — `example`** | Domain names read right-to-left in terms of hierarchy. `uk` is the ccTLD, `co` is a second-level TLD (common in the UK), `example` is the registrable second-level domain, `store` is a subdomain, and `api` is a sub-subdomain. The SLD that is registered and paid for is `example`. |
| 2 | **B — Maps a hostname to another hostname (an alias)** | A CNAME record creates an alias. `www.example.com CNAME example.com` means "www.example.com points to wherever example.com points." It doesn't map to an IP directly — it delegates to another name that eventually resolves to an IP. |
| 3 | **C — A `<script defer>` tag in `<head>`** | `defer` tells the browser to download the script in parallel with HTML parsing and execute it only after parsing is complete. This means it does NOT block rendering. In contrast, regular `<script>`, render-blocking CSS, and `<style>` blocks all block rendering. |
| 4 | **B — DOM → CSSOM → Render Tree → Layout → Paint → Composite** | This is the correct critical rendering path order. The browser must build both the DOM (from HTML) and CSSOM (from CSS), then combine them into the Render Tree, then calculate positions (Layout), then draw pixels (Paint), then assemble layers (Composite). |
| 5 | **C — `transform: translateX(-300px)` transitioning to `transform: translateX(0)`** | `transform` is a compositing-only property — the browser can hand it entirely to the GPU without triggering layout or paint. Animating `left`, `margin-left`, or `width` all trigger layout recalculation (the most expensive step), making animations janky. |
| 6 | **C — The browser's live, in-memory tree representation of the page** | The DOM is built from the parsed HTML and kept live in memory. It can be modified by JavaScript at any time, and those changes are immediately reflected on screen. It is not the original HTML file, not a database, and not a stylesheet. |
| 7 | **C — `#main-title`** | ID selectors have a specificity of 100 points — the highest of these options. Class selectors (`.intro`) score 10. Element selectors (`p`, `div p`) score 1 or 2. Inline styles score 1000, but no inline style is among the options. |
| 8 | **B — Padding** | Padding is the space between the content edge and the border edge — inside the element, between the content and the visible border. Margin is outside the border (between elements). Gap is a Flexbox/Grid property for space between children. |
| 9 | **B — It includes the padding and border in the element's width** | By default, CSS uses `content-box` sizing: a 300px element with 20px padding is actually 340px wide. `border-box` makes the width include padding and border, so a 300px element with 20px padding has 260px of content area inside a 300px total width. |
| 10 | **C — `<nav>`** | `<nav>` is the semantic HTML5 element specifically for navigation links. `<div id="navigation">` has no semantic meaning. `<menu>` is for context menus (not site navigation). `<links>` is not a valid HTML element. |
| 11 | **B — Provides alternative text for screen readers and when the image fails to load** | The `alt` attribute is critical for accessibility (screen readers read it aloud) and resilience (shown when images can't load). The title attribute is for hover tooltips. `loading` controls lazy loading. No alt attribute sets a background color. |
| 12 | **C — Places children in a flexible row (or column) layout that can distribute space** | `display: flex` creates a flex container. Children become flex items that can grow, shrink, and be aligned along the main axis (row by default) and cross axis. It doesn't hide children, doesn't force a column by default, and doesn't cause overlap. |
| 13 | **B — Provides a JavaScript-drawable surface for 2D graphics** | `<canvas>` is a blank drawing surface. JavaScript uses the Canvas API (`getContext("2d")`) to draw shapes, paths, text, and images on it. It's not for PDF viewing (that's `<embed>`), iframes (that's `<iframe>`), or CSS grid. |
| 14 | **C — The W3C's move toward strict XHTML 2.0 at the expense of backward compatibility** | Browser vendors (Apple, Mozilla, Opera) broke away specifically because W3C was developing XHTML 2.0, which would have broken all existing web pages by not being backward compatible. WHATWG chose to evolve HTML5 from existing practices. |
| 15 | **C — `:root { --brand-color: #7c3aed; }` and then `color: var(--brand-color);`** | CSS custom properties must be declared with `--` prefix in a rule block (`:root` for global scope). They are accessed with `var(--property-name)`. Option A uses incorrect `=` syntax. Option B omits the `--` prefix. Option D is Sass syntax (`$`), not native CSS. |

---

## Part 2: Short Answer — Suggested Responses

### 1. DOM vs HTML Source — when and why they differ

The **HTML source** is the raw text file sent from the server. The **DOM** is the browser's live, in-memory tree representation built from parsing that HTML.

They differ in two main scenarios:

**After JavaScript runs:** JavaScript can add, modify, or remove DOM nodes after the page loads. A React application might receive a nearly empty HTML shell (`<div id="root"></div>`) from the server and then build hundreds of DOM nodes using JavaScript. "View Source" shows the original shell; DevTools shows the full live DOM.

**After browser error correction:** If HTML has errors (unclosed tags, invalid nesting), the browser's forgiving parser fixes them when building the DOM. The DOM may differ from the source even before JavaScript runs.

---

### 2. CSS Cascade — three rules on the same `<p>`

When three rules target the same element, **specificity** determines the winner — not order.

- Element rule (`p { ... }`) — specificity: 1
- Class rule (`.intro { ... }`) — specificity: 10
- Inline style (`style="..."`) — specificity: 1000

**The inline style wins** with specificity 1000. Even if the element rule is declared last in the stylesheet, it has specificity 1 — far less than an inline style.

Among external stylesheet rules, the class rule (10) beats the element rule (1) regardless of source order. Order only matters when **specificity is equal**.

---

### 3. From first byte of HTML to rendered page

1. **HTML bytes arrive** → browser's HTML parser begins tokenizing
2. **Parser builds DOM** — encounters `<link rel="stylesheet">` → pauses rendering, fetches CSS file
3. **CSS bytes arrive** → CSS parser builds CSSOM; HTML parsing may continue
4. **Parser encounters `<script>`** without `defer` → pauses HTML parsing, fetches and executes JS
5. **DOM and CSSOM both complete** → browser builds **Render Tree** (visible nodes with computed styles)
6. **Layout (Reflow)** → browser calculates position and size of every render tree node
7. **Paint** → browser rasterizes elements onto layers (colors, text, borders, shadows)
8. **Composite** → GPU assembles layers in correct stacking order and displays final frame
9. **Page is visible** — additional resources (images, fonts) may still be loading and trigger repaints as they arrive

---

### 4. Semantic HTML — three examples

**Semantic HTML** means using elements that describe the meaning of their content, not just wrap it in a generic container.

| Semantic Element | Meaning | Replaces |
|----------------|---------|---------|
| `<nav>` | Navigation links | `<div id="nav">` or `<div class="navigation">` |
| `<article>` | A self-contained, independently distributable piece of content (blog post, news article) | `<div class="article">` |
| `<footer>` | The footer of the page or a section — copyright, contact info, secondary links | `<div id="footer">` |

**Why it matters:**
- Screen readers use semantic elements to help visually impaired users navigate ("jump to main content," "list all navigation links")
- Search engines use them to understand page structure, improving SEO
- Developer experience — code is self-documenting

---

### 5. Flexbox vs Grid — when to use each

**Flexbox** is one-dimensional — it distributes items along a single axis (row or column). Use it when:
- Laying out a horizontal navigation bar
- Centering a single item vertically and horizontally
- Distributing buttons evenly in a row
- Aligning items in a card's content area

**CSS Grid** is two-dimensional — it controls both rows AND columns simultaneously. Use it when:
- Building an overall page layout (header, sidebar, main content, footer)
- Creating a uniform card grid (3 columns of cards, wrapping)
- Any layout where you need to place items at the intersection of rows and columns

**Rule of thumb:** If you're thinking in one direction (a row of things), use Flexbox. If you're thinking in two directions (rows AND columns), use Grid. Many modern designs use Grid for the page-level layout and Flexbox for component-level layouts within each grid area.

---

*Back to quiz: [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md)*
