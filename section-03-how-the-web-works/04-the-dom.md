# 04 — The DOM: The Browser's Model of Your Page

## Learning Objectives

By the end of this page, you will be able to:

- Define the DOM and explain what problem it solves
- Describe the tree structure of the DOM with nodes and their types
- Use browser DevTools to explore the live DOM
- Explain the difference between the HTML source and the live DOM
- Understand how JavaScript uses the DOM to make pages interactive
- Describe what DOM events are

---

## What Is the DOM?

**DOM** stands for **Document Object Model**. It is the browser's in-memory, structured representation of a web page — built from parsed HTML and kept live while the page is open.

The DOM is:
1. A **tree of nodes** representing every element, attribute, and piece of text on the page
2. A **programming interface** — JavaScript can read it, modify it, add to it, and delete from it
3. **Live** — changes to the DOM instantly update what you see on the page (without a reload)

> **Analogy:** Think of the DOM as the architectural blueprint of a building — but a magical blueprint that is always kept in sync with the real building. When a contractor (JavaScript) adds a room (DOM node), the blueprint updates immediately. When you look at the blueprint, you see the current state of the building — not what it looked like when it was first drawn.

The original HTML file is like the architect's original sketch. The DOM is the up-to-date blueprint. They start identical but can diverge as JavaScript modifies the page.

---

## The DOM as a Tree

The DOM is structured as a tree — a hierarchy where every node has exactly one parent (except the root) and can have zero or more children.

### Node Types

| Node Type | What It Represents | Example |
|-----------|-------------------|---------|
| **Document** | The root of the entire DOM | `document` |
| **Element** | An HTML element (tag) | `<h1>`, `<div>`, `<img>` |
| **Text** | The text content inside an element | `"Hello World"` |
| **Attribute** | An attribute on an element | `class="title"`, `href="/about"` |
| **Comment** | An HTML comment | `<!-- this is a comment -->` |

### Example: HTML to DOM Tree

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>My Page</title>
  </head>
  <body>
    <header>
      <h1 class="site-title">Welcome</h1>
    </header>
    <main>
      <p>Hello, <strong>world</strong>!</p>
      <img src="photo.jpg" alt="A photo">
    </main>
  </body>
</html>
```

DOM tree:

```
Document
└── html [lang="en"]
    ├── head
    │   ├── meta [charset="UTF-8"]
    │   └── title
    │       └── "My Page"  ← TextNode
    └── body
        ├── header
        │   └── h1 [class="site-title"]
        │       └── "Welcome"  ← TextNode
        └── main
            ├── p
            │   ├── "Hello, "  ← TextNode
            │   ├── strong
            │   │   └── "world"  ← TextNode
            │   └── "!"  ← TextNode
            └── img [src="photo.jpg", alt="A photo"]
```

Notice:
- Every HTML element becomes an **Element node**
- The text inside elements (including whitespace) becomes **Text nodes**
- Attributes like `class`, `src`, `alt` are properties of their Element nodes

---

## HTML Source vs Live DOM

A crucial distinction: **the HTML source file and the live DOM are not the same thing after JavaScript runs.**

Consider this:
1. The server sends HTML with an empty `<div id="app"></div>`
2. JavaScript runs and fills it with a complete navigation menu, articles, and a footer
3. The DOM now contains hundreds of nodes — but the original HTML source still just says `<div id="app"></div>`

This is how **React, Vue, and Angular** work — they start with nearly empty HTML and build the entire page in the DOM using JavaScript. If you "view source" (Ctrl+U) on a React app, you might see very little. But if you look at the live DOM in DevTools, you see the fully rendered page.

> **Rule of thumb:** "View Source" shows what the server sent. "Inspect Element" shows the live DOM — including all changes made by JavaScript.

---

## Exploring the DOM in DevTools

The best way to understand the DOM is to poke at it.

### Opening DevTools

- **Chrome/Edge:** `F12` or `Cmd+Option+I` (macOS) → Elements tab
- **Firefox:** `F12` → Inspector tab
- **Safari:** Enable Developer menu in Safari Preferences → `Cmd+Option+I` → Elements

### What You'll See

The Elements panel shows the live DOM tree. You can:

**Expand and collapse nodes:** Click the triangle next to any element to see its children.

**Hover over elements:** The corresponding element is highlighted on the page.

**Right-click an element on the page → "Inspect":** DevTools jumps to that element in the DOM tree.

**Edit HTML live:** Double-click any text or tag in DevTools and type. The page updates instantly — your changes are live (until you reload, which will reset from the server).

**Delete nodes:** Click a node and press Delete.

**Computed styles:** With a node selected, look at the "Computed" tab to see the final resolved CSS properties.

---

## JavaScript and the DOM

The DOM isn't just for viewing — it's a **programming interface**. JavaScript can interact with every node.

We'll dive deep into this in Section 4 (JavaScript), but here's a conceptual preview:

### Selecting Elements

```javascript
// Find an element by its ID
const title = document.getElementById("site-title");

// Find elements by CSS class
const allCards = document.querySelectorAll(".card");

// Find the first element matching a CSS selector
const firstLink = document.querySelector("nav a");
```

### Reading and Changing Content

```javascript
// Read text content
console.log(title.textContent);  // "Welcome"

// Change text
title.textContent = "Hello, World!";

// Change HTML inside an element
document.getElementById("app").innerHTML = "<p>New content</p>";
```

### Changing Styles

```javascript
// Add a CSS class
title.classList.add("highlight");

// Remove a CSS class
title.classList.remove("hidden");

// Toggle (add if not present, remove if present)
title.classList.toggle("active");

// Set an inline style directly
title.style.color = "red";
```

### Creating and Adding Nodes

```javascript
// Create a new element
const newParagraph = document.createElement("p");
newParagraph.textContent = "This was added by JavaScript!";

// Add it to the DOM
document.body.appendChild(newParagraph);
```

### Removing Nodes

```javascript
const element = document.getElementById("old-banner");
element.remove();
```

Every time you call these methods, the browser immediately updates the render tree, re-runs layout if necessary, repaints, and you see the change on screen.

---

## DOM Events

The DOM also provides a system for **events** — notifications when something happens on the page.

| Event | When It Fires |
|-------|--------------|
| `click` | User clicks an element |
| `mouseover` | Mouse moves over an element |
| `keydown` | Key is pressed |
| `submit` | A form is submitted |
| `change` | An input value changes |
| `load` | A resource (page, image) finishes loading |
| `DOMContentLoaded` | HTML is parsed and DOM is ready |
| `scroll` | User scrolls the page |
| `resize` | Browser window is resized |

You attach an **event listener** to a DOM node and provide a function to run when the event fires:

```javascript
const button = document.getElementById("my-button");

button.addEventListener("click", function() {
  alert("Button clicked!");
});
```

This is how all interactivity on the web works — a user action fires a DOM event, your JavaScript code runs, it modifies the DOM, and the user sees the result.

> **Analogy:** DOM events are like motion sensors in a building. Each sensor monitors a specific kind of activity (click = door button pressed, mouseover = someone walking near a sensor). When triggered, they run a preprogrammed response (open the door, turn on the lights). Your event listeners are the programmed responses.

---

## The Virtual DOM (React Concept Preview)

Libraries like React introduced the concept of a **Virtual DOM** — a lightweight JavaScript object that mirrors the real DOM.

**Why?** Directly manipulating the real DOM is slow if done carelessly. Updating hundreds of DOM nodes individually triggers hundreds of layout and paint cycles.

React's approach:
1. Keep a virtual copy of the DOM in memory
2. When data changes, compute what the new DOM *should* look like
3. **Diff** the new virtual DOM against the previous one — find only what changed
4. Apply only the minimal set of changes to the real DOM

This makes React apps fast even with complex, frequently-updating UIs.

We'll work with the real DOM in Section 4 (JavaScript) and the virtual DOM approach in Section 8 (React).

**Further Reading:**
- [Introduction to the DOM — MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)
- [DOM Enlightenment — free book](https://domenlightenment.com/) — Deep dive into the DOM API
- [What is the DOM? — CSS-Tricks](https://css-tricks.com/dom/)
- [Understanding the Virtual DOM — LogRocket](https://blog.logrocket.com/virtual-dom-react/)

---

## Check Your Understanding

1. What is the DOM? How does it differ from the HTML source file?
2. List the four main types of DOM nodes and give an example of each.
3. If JavaScript adds elements to the DOM after the page loads, will "View Source" show those elements? Explain.
4. In your own words, describe what `document.querySelector(".card")` does.
5. What is the difference between `textContent` and `innerHTML`?
6. Why was the concept of a Virtual DOM introduced, and what problem does it solve?

---

*Previous: [03 — How Browsers Work](03-how-browsers-work.md)*
*Next: [05 — Introduction to HTML](05-introduction-to-html.md)*
