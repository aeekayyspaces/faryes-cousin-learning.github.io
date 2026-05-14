# 01 — DOM Manipulation: Going Deeper

## Learning Objectives

By the end of this page, you will be able to:

- Traverse the DOM tree using parent, child, and sibling relationships
- Build dynamic HTML efficiently using `DocumentFragment`
- Use the `<template>` element to define reusable HTML structures
- Observe DOM changes with `MutationObserver`
- Detect element visibility with `IntersectionObserver`
- Avoid layout thrashing by batching reads and writes

---

## Section 4 Recap

In Section 4 we covered the basics: selecting elements, reading/writing content, changing classes, creating nodes, and inserting them. This section goes deeper — the patterns professionals use when building real applications.

---

## DOM Traversal

You don't always know the exact selector for an element. Often you need to navigate **relative** to an element you already have.

### Parent

```javascript
const item = document.querySelector(".menu-item")

item.parentElement        // immediate parent element
item.closest(".menu")     // nearest ancestor matching a CSS selector (walks up)
item.closest("form")      // find the containing form from any descendant
```

`closest()` is invaluable in event delegation — given `e.target` (which might be a deeply nested child), find the relevant ancestor:

```javascript
list.addEventListener("click", e => {
  const card = e.target.closest(".card")   // works wherever inside .card you click
  if (!card) return
  openDetail(card.dataset.id)
})
```

### Children

```javascript
const ul = document.querySelector("ul")

ul.children              // HTMLCollection of element children (no text nodes)
ul.firstElementChild     // first child element
ul.lastElementChild      // last child element
ul.childElementCount     // number of child elements
```

### Siblings

```javascript
const item = document.querySelector("li.active")

item.nextElementSibling      // next sibling element
item.previousElementSibling  // previous sibling element
```

### A Practical Traversal

```javascript
// Given a "delete" button inside a list item, remove the whole item:
document.addEventListener("click", e => {
  if (!e.target.matches(".btn-delete")) return
  e.target.closest("li").remove()
})
```

No need to know the item's ID or store a reference in advance — traverse from the click target.

---

## Building Dynamic HTML Efficiently

### The Naive Way — Slow

Setting `innerHTML` in a loop triggers a layout and repaint on every iteration:

```javascript
// ❌ Slow — 100 DOM insertions, 100 repaints
const list = document.querySelector("#list")
for (const item of data) {
  list.innerHTML += `<li>${item.name}</li>`
}
```

### DocumentFragment — Batch Insertions

A `DocumentFragment` is a lightweight, off-screen DOM container. Build everything in it first, then insert it once — one repaint:

```javascript
// ✅ Fast — 1 DOM insertion, 1 repaint
const fragment = document.createDocumentFragment()

for (const item of data) {
  const li = document.createElement("li")
  li.textContent = item.name
  li.dataset.id = item.id
  fragment.appendChild(li)
}

document.querySelector("#list").appendChild(fragment)
// The fragment itself disappears; only its children are inserted
```

> **Analogy:** Building furniture off-site in a workshop, then delivering the finished pieces to the room — rather than hammering and sawing inside the room 100 times.

### String Concatenation + `innerHTML` (For Static HTML)

When the HTML structure is fixed and the data is safe (not user input), building a string and setting `innerHTML` once is clean and fast:

```javascript
const items = data.map(item =>
  `<li class="card" data-id="${item.id}">
     <h3>${escapeHTML(item.name)}</h3>
     <p>${escapeHTML(item.description)}</p>
   </li>`
).join("")

document.querySelector("#list").innerHTML = items
```

**Always escape user-provided data** before inserting into `innerHTML` to prevent XSS:

```javascript
function escapeHTML(str) {
  return String(str)
    .replace(/&/g,  "&amp;")
    .replace(/</g,  "&lt;")
    .replace(/>/g,  "&gt;")
    .replace(/"/g,  "&quot;")
    .replace(/'/g,  "&#39;")
}
```

---

## The `<template>` Element

The `<template>` element lets you define reusable HTML in the document that is **not rendered** until you clone it with JavaScript. It's great for repeating card structures:

```html
<!-- Define once in HTML — not visible on the page -->
<template id="card-template">
  <li class="card">
    <img class="card-img" src="" alt="">
    <div class="card-body">
      <h3 class="card-title"></h3>
      <p class="card-text"></p>
      <button class="btn-view">View Details</button>
    </div>
  </li>
</template>
```

```javascript
const template = document.getElementById("card-template")

function createCard(item) {
  // Clone the template content
  const clone = template.content.cloneNode(true)  // true = deep clone

  // Fill in the data
  clone.querySelector(".card-img").src = item.imageUrl
  clone.querySelector(".card-img").alt = item.name
  clone.querySelector(".card-title").textContent = item.name
  clone.querySelector(".card-text").textContent = item.description
  clone.querySelector(".btn-view").dataset.id = item.id

  return clone
}

const fragment = document.createDocumentFragment()
for (const item of data) {
  fragment.appendChild(createCard(item))
}
document.querySelector("#card-list").appendChild(fragment)
```

**Advantages over string templates:**
- No XSS risk (you set `.textContent`, not `innerHTML`)
- The HTML structure is visible in the source — easier to read
- Complex nested structures are easier to fill in by selecting within the clone

---

## MutationObserver: Watching the DOM Change

`MutationObserver` fires a callback whenever the DOM changes — useful for reacting to changes made by third-party code, animations, or asynchronous operations you don't control.

```javascript
const target = document.querySelector("#dynamic-content")

const observer = new MutationObserver((mutations) => {
  for (const mutation of mutations) {
    if (mutation.type === "childList") {
      console.log("Children changed:", mutation.addedNodes, mutation.removedNodes)
    }
    if (mutation.type === "attributes") {
      console.log(`Attribute "${mutation.attributeName}" changed`)
    }
  }
})

observer.observe(target, {
  childList: true,     // watch for added/removed children
  subtree: true,       // watch all descendants, not just direct children
  attributes: true,    // watch for attribute changes
  characterData: true  // watch for text content changes
})

// Stop observing:
observer.disconnect()
```

**Practical use cases:**
- Detect when a third-party library has finished rendering
- Auto-run code when a specific element appears in the DOM
- Build custom devtools or analytics
- React to chat messages added by another script

---

## IntersectionObserver: Detecting Visibility

`IntersectionObserver` tells you when an element enters or exits the viewport — without polling with `setInterval` or listening to scroll events.

### Lazy Loading Images

```javascript
const images = document.querySelectorAll("img[data-src]")

const observer = new IntersectionObserver((entries) => {
  for (const entry of entries) {
    if (entry.isIntersecting) {
      const img = entry.target
      img.src = img.dataset.src          // swap in the real source
      img.removeAttribute("data-src")
      observer.unobserve(img)            // stop watching once loaded
    }
  }
}, { rootMargin: "200px" })             // start loading 200px before visible

images.forEach(img => observer.observe(img))
```

### Infinite Scroll / Load More

```javascript
const sentinel = document.querySelector("#load-more-sentinel")

const observer = new IntersectionObserver((entries) => {
  if (entries[0].isIntersecting) {
    loadNextPage()
  }
})

observer.observe(sentinel)
```

Place a `<div id="load-more-sentinel">` at the bottom of your list. When the user scrolls to it, load the next page automatically.

### Scroll-Triggered Animations

```javascript
const sections = document.querySelectorAll(".fade-in-section")

const observer = new IntersectionObserver((entries) => {
  for (const entry of entries) {
    entry.target.classList.toggle("visible", entry.isIntersecting)
  }
}, { threshold: 0.15 })   // trigger when 15% of element is visible

sections.forEach(s => observer.observe(s))
```

```css
.fade-in-section {
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.5s ease, transform 0.5s ease;
}
.fade-in-section.visible {
  opacity: 1;
  transform: translateY(0);
}
```

---

## Avoiding Layout Thrashing

**Layout thrashing** occurs when JavaScript alternates between reading layout properties (like `offsetWidth`, `getBoundingClientRect`) and writing to the DOM, forcing the browser to recalculate layout repeatedly.

```javascript
// ❌ Layout thrashing — read, write, read, write in a loop
const items = document.querySelectorAll(".item")
for (const item of items) {
  const height = item.offsetHeight           // READ  (forces layout calc)
  item.style.height = height * 2 + "px"     // WRITE (invalidates layout)
}
// Each iteration forces the browser to recalculate layout

// ✅ Batched — read all, then write all
const heights = Array.from(items).map(item => item.offsetHeight)  // READ all
for (let i = 0; i < items.length; i++) {
  items[i].style.height = heights[i] * 2 + "px"                  // WRITE all
}
// Browser recalculates layout once, then applies all writes
```

> **Rule:** Batch DOM reads together, then batch DOM writes together. Never interleave them in a loop.

**Properties that trigger layout recalculation (use sparingly in loops):**
`offsetTop`, `offsetLeft`, `offsetWidth`, `offsetHeight`, `clientWidth`, `clientHeight`, `scrollTop`, `scrollLeft`, `getBoundingClientRect()`, `getComputedStyle()`

---

## Check Your Understanding

1. What does `element.closest(".card")` do? How is it different from `querySelector(".card")`?
2. Why is `DocumentFragment` faster than appending elements one at a time?
3. What is the advantage of using a `<template>` element over building HTML strings?
4. What does `MutationObserver` watch for? Give a real-world use case.
5. What is layout thrashing and how do you prevent it?

---

*Next: [02 — Events Deep Dive](02-events-deep-dive.md)*
