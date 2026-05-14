# 02 — Events Deep Dive: Propagation, Delegation, and Custom Events

## Learning Objectives

By the end of this page, you will be able to:

- Explain event propagation: bubbling and capturing phases
- Use `stopPropagation` and `preventDefault` correctly and know the difference
- Apply event delegation to handle dynamic elements efficiently
- Create and dispatch custom events
- Remove event listeners cleanly to avoid memory leaks
- Use debounce and throttle to handle high-frequency events
- Manage keyboard events for accessibility

---

## Event Propagation: Bubbling and Capturing

When you click an element, the event doesn't just fire on that element — it travels through the DOM in three phases:

```
             CAPTURING PHASE (down)
Document
  └── html
        └── body
              └── div#container
                    └── ul#list
                          └── li.item   ← 2. TARGET PHASE (event fires here)
              ↑
             BUBBLING PHASE (up)
```

### Phase 1: Capturing (top → target)

The event travels **down** from the document root to the target element. Event listeners attached with `{ capture: true }` fire during this phase.

### Phase 2: Target

The event fires on the element that was actually clicked.

### Phase 3: Bubbling (target → top)

The event travels **up** from the target back to the document root. Most event listeners fire during this phase (the default).

```javascript
document.addEventListener("click", e => console.log("document (bubble)"))
document.querySelector("ul").addEventListener("click", e => console.log("ul (bubble)"))
document.querySelector("li").addEventListener("click", e => console.log("li (target)"))

// Click the li → logs:
// "li (target)"
// "ul (bubble)"
// "document (bubble)"
```

### Capturing Listeners

Add `{ capture: true }` to fire during the down-phase instead:

```javascript
document.addEventListener("click", e => console.log("document CAPTURE"), { capture: true })

// Click the li → logs:
// "document CAPTURE"   ← fires first, during capture
// "li (target)"
// "ul (bubble)"
// "document (bubble)"
```

**Practical use:** Capture phase is rarely needed in application code. It's used in framework internals and for intercepting events before they reach a target (e.g., blocking all clicks on a disabled overlay).

---

## `stopPropagation` vs `preventDefault`

These two are often confused. They do completely different things.

### `stopPropagation()` — Stop the Event Traveling

Prevents the event from bubbling up (or capturing down) any further:

```javascript
document.querySelector(".card").addEventListener("click", e => {
  openCard()
})

document.querySelector(".card .delete-btn").addEventListener("click", e => {
  e.stopPropagation()   // prevent the click from also triggering openCard()
  deleteCard()
})
```

Without `stopPropagation()`, clicking the delete button would also trigger the card's click handler (because the click bubbles up).

### `stopImmediatePropagation()` — Also Stop Other Listeners on the Same Element

If multiple listeners are on the same element, `stopPropagation` still lets other listeners on the same element fire. `stopImmediatePropagation` stops those too:

```javascript
button.addEventListener("click", e => {
  e.stopImmediatePropagation()
  console.log("First listener — stops everything")
})

button.addEventListener("click", e => {
  console.log("Second listener — never runs")
})
```

### `preventDefault()` — Stop the Browser's Default Action

Prevents the browser's built-in behavior for the event, but does NOT stop propagation:

| Element / Event | Default behavior prevented |
|-----------------|--------------------------|
| `<form>` submit | Navigating to action URL |
| `<a>` click | Following the href |
| `<input type="checkbox">` click | Toggling checked state |
| `keydown` on Enter in input | Submitting the form |
| `contextmenu` | Showing right-click menu |
| `wheel` | Scrolling the page |
| `drag` | Browser drag behavior |

```javascript
form.addEventListener("submit", e => {
  e.preventDefault()    // stop navigation
  // handle with JavaScript instead
})

link.addEventListener("click", e => {
  e.preventDefault()    // stop navigation
  handleInternalNavigation(link.href)
})
```

> **Memory rule:** `preventDefault` = stop the **browser's action**. `stopPropagation` = stop the **event's travel**.

---

## Event Delegation (Deep Dive)

We introduced delegation in Section 4. Here's the full picture.

### The Problem It Solves

```javascript
// ❌ Problems:
//  1. Doesn't work for items added later (dynamic content)
//  2. 1000 items = 1000 listeners = memory waste
document.querySelectorAll(".item").forEach(item => {
  item.addEventListener("click", handleClick)
})

// ✅ One listener handles everything — past and future items
document.querySelector("#container").addEventListener("click", e => {
  const item = e.target.closest(".item")
  if (!item) return     // clicked somewhere else in the container
  handleClick(item)
})
```

### Checking the Target

```javascript
list.addEventListener("click", e => {
  // Check what was actually clicked:
  if (e.target.matches(".btn-delete")) {
    e.target.closest("li").remove()
    return
  }

  if (e.target.matches(".btn-edit")) {
    startEditing(e.target.closest("li"))
    return
  }

  if (e.target.closest(".card-header")) {
    toggleExpand(e.target.closest(".card"))
    return
  }
})
```

### Delegation with Data Attributes

Store the action and data on the element — the handler reads them:

```html
<ul id="actions">
  <li><button data-action="edit"   data-id="42">Edit</button></li>
  <li><button data-action="delete" data-id="42">Delete</button></li>
  <li><button data-action="share"  data-id="42">Share</button></li>
</ul>
```

```javascript
document.querySelector("#actions").addEventListener("click", e => {
  const btn = e.target.closest("[data-action]")
  if (!btn) return

  const { action, id } = btn.dataset

  const handlers = {
    edit:   () => editItem(id),
    delete: () => deleteItem(id),
    share:  () => shareItem(id),
  }

  handlers[action]?.()   // call the appropriate handler if it exists
})
```

---

## Custom Events

You can create and dispatch your own events — a clean way for components to communicate without tight coupling.

```javascript
// Dispatch a custom event:
function addToCart(product) {
  cart.push(product)

  // Notify anyone who's listening
  const event = new CustomEvent("cart:updated", {
    bubbles: true,           // let it bubble up
    detail: {                // custom data payload
      product,
      cartSize: cart.length
    }
  })

  document.dispatchEvent(event)
}

// Listen anywhere in the app:
document.addEventListener("cart:updated", e => {
  const { product, cartSize } = e.detail
  updateCartBadge(cartSize)
  showToast(`${product.name} added to cart!`)
})
```

Custom events decouple components — the cart module doesn't need to know who's listening to updates. The badge updater and toast notification each listen independently.

### Dispatching from a Specific Element

```javascript
const input = document.querySelector("#search-input")

// Dispatch from the element (bubbles up to document)
input.dispatchEvent(new CustomEvent("search:changed", {
  bubbles: true,
  detail: { query: input.value }
}))

// Listen on a containing element
document.querySelector("#search-panel").addEventListener("search:changed", e => {
  filterResults(e.detail.query)
})
```

---

## Removing Event Listeners (Cleanup)

If you add event listeners and never remove them, you can cause **memory leaks** — especially in single-page applications where components are mounted and unmounted repeatedly.

### The Problem with Arrow Functions

```javascript
// ❌ Can't remove — the function reference is lost
element.addEventListener("click", () => doSomething())
element.removeEventListener("click", () => doSomething())  // different function!
```

### Named Function Reference

```javascript
// ✅ Save the reference to remove it later
function handleClick() { doSomething() }

element.addEventListener("click", handleClick)
// ... later:
element.removeEventListener("click", handleClick)
```

### The `{ once: true }` Option

Automatically removes the listener after it fires once:

```javascript
button.addEventListener("click", () => {
  showWelcomeModal()
}, { once: true })   // fires once, then self-removes

// Useful for: one-time setup, welcome messages, tutorial steps
```

### AbortController for Listener Groups

Remove many listeners at once:

```javascript
const controller = new AbortController()
const { signal } = controller

button.addEventListener("click",   handler1, { signal })
input.addEventListener("keydown",  handler2, { signal })
window.addEventListener("resize",  handler3, { signal })

// Remove all three at once:
controller.abort()
```

This is especially useful in frameworks — use one `AbortController` per component, abort it when the component unmounts.

---

## Debounce and Throttle

High-frequency events (scroll, resize, keyup, mousemove) can fire hundreds of times per second. Running expensive work on every event is a performance problem.

### Debounce — Wait Until Quiet

Only run after a pause in events. Perfect for search-as-you-type:

```javascript
function debounce(fn, delay) {
  let timer
  return function(...args) {
    clearTimeout(timer)
    timer = setTimeout(() => fn.apply(this, args), delay)
  }
}

const searchInput = document.querySelector("#search")
const handleSearch = debounce((e) => {
  fetchResults(e.target.value)   // only called after 300ms of inactivity
}, 300)

searchInput.addEventListener("input", handleSearch)
```

> **Analogy:** An elevator doesn't leave the moment one person gets on. It waits a few seconds — if nobody else gets on in that time, it departs. Each new arrival restarts the wait.

### Throttle — Limit the Rate

Ensure the function runs at most once per interval. Perfect for scroll handlers:

```javascript
function throttle(fn, limit) {
  let lastRun = 0
  return function(...args) {
    const now = Date.now()
    if (now - lastRun >= limit) {
      lastRun = now
      fn.apply(this, args)
    }
  }
}

const handleScroll = throttle(() => {
  updateProgressBar(window.scrollY)   // at most every 100ms
}, 100)

window.addEventListener("scroll", handleScroll)
```

> **Analogy:** A speed governor on a truck — it can accelerate, but will never exceed a maximum speed regardless of how hard you push the pedal.

---

## Keyboard Events and Accessibility

Well-built web applications are operable by keyboard, not just mouse.

### Listening for Specific Keys

```javascript
document.addEventListener("keydown", e => {
  if (e.key === "Escape") closeModal()
  if (e.key === "Enter") submitForm()
  if (e.key === "ArrowDown") moveSelectionDown()
  if (e.key === "?") openHelp()

  // Combinations:
  if (e.ctrlKey && e.key === "s") {
    e.preventDefault()   // prevent browser's Save dialog
    saveDocument()
  }
})
```

### Keyboard Navigation Pattern (Tab and Arrow Keys)

Custom widgets (dropdowns, carousels, menus) need to be keyboard navigable:

```javascript
const items = document.querySelectorAll(".menu-item")
let currentIndex = 0

function focusItem(index) {
  items[currentIndex].setAttribute("tabindex", "-1")
  currentIndex = (index + items.length) % items.length   // wrap around
  items[currentIndex].setAttribute("tabindex", "0")
  items[currentIndex].focus()
}

document.querySelector(".menu").addEventListener("keydown", e => {
  if (e.key === "ArrowDown") { e.preventDefault(); focusItem(currentIndex + 1) }
  if (e.key === "ArrowUp")   { e.preventDefault(); focusItem(currentIndex - 1) }
  if (e.key === "Home")      { e.preventDefault(); focusItem(0) }
  if (e.key === "End")       { e.preventDefault(); focusItem(items.length - 1) }
})
```

### Focus Management

When opening a modal, focus should move into it. When closing, it should return to the trigger:

```javascript
function openModal(modal, triggerButton) {
  modal.classList.remove("hidden")
  modal.removeAttribute("inert")

  // Focus the first focusable element inside the modal
  const firstFocusable = modal.querySelector("button, input, a, [tabindex='0']")
  firstFocusable?.focus()

  // Close on Escape
  const closeOnEsc = e => {
    if (e.key === "Escape") closeModal(modal, triggerButton, closeOnEsc)
  }
  document.addEventListener("keydown", closeOnEsc)
}

function closeModal(modal, triggerButton, escListener) {
  modal.classList.add("hidden")
  modal.setAttribute("inert", "")    // inert prevents focus and interaction
  document.removeEventListener("keydown", escListener)
  triggerButton?.focus()             // return focus to where it came from
}
```

---

## Check Your Understanding

1. Describe the three phases of event propagation. In which phase do most listeners fire?
2. What is the difference between `stopPropagation()` and `preventDefault()`?
3. Why is event delegation more efficient than attaching listeners to every list item?
4. What is a custom event and when would you use one instead of calling a function directly?
5. What is the difference between debounce and throttle? Give a use case for each.

---

*Previous: [01 — DOM Manipulation](01-dom-manipulation.md)*
*Next: [03 — Forms and Validation](03-forms-and-validation.md)*
