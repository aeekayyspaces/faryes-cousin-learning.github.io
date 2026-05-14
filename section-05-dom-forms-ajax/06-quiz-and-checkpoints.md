# 06 — Section Quiz and Checkpoints

## Section 5 Learning Objectives Review

Before taking this quiz, confirm you can:

- [ ] Traverse the DOM using `closest()`, `parentElement`, `children`, and siblings
- [ ] Build dynamic lists efficiently with `DocumentFragment` and `<template>`
- [ ] Explain event bubbling and capturing and use `stopPropagation` vs `preventDefault`
- [ ] Apply event delegation to handle dynamic elements with one listener
- [ ] Create and dispatch custom events
- [ ] Use debounce and throttle appropriately
- [ ] Capture form data with `FormData` and validate it with real-time feedback
- [ ] Use `AbortController` to cancel in-flight fetch requests
- [ ] Make parallel requests with `Promise.all`
- [ ] Explain what CORS is and why it exists
- [ ] Handle loading states, errors, and empty states in a fetch-driven UI

---

## Part 1: Multiple Choice

Answer key: [solutions/06-quiz-solutions.md](solutions/06-quiz-solutions.md)

---

**1.** What does `e.target.closest(".card")` return if the click target is a `<button>` inside a `<div class="card">`?

- A) The `<button>` element itself
- B) The `<div class="card">` element
- C) `null`
- D) The document root

---

**2.** In which order do these listeners fire when the inner `<button>` is clicked?

```javascript
document.addEventListener("click", () => console.log("A"), { capture: true })
document.querySelector("div").addEventListener("click", () => console.log("B"))
document.querySelector("button").addEventListener("click", () => console.log("C"))
```

- A) A, B, C
- B) C, B, A
- C) A, C, B
- D) B, C, A

---

**3.** What is the key advantage of using `DocumentFragment` when inserting many elements?

- A) It allows asynchronous DOM operations
- B) It batches all insertions into a single DOM update, reducing repaints
- C) It automatically prevents XSS when setting innerHTML
- D) It creates a deep copy of existing DOM nodes

---

**4.** What does `e.stopPropagation()` do that `e.preventDefault()` does NOT?

- A) Cancels the browser's default behavior for the event
- B) Prevents the event from travelling to parent elements
- C) Removes all other event listeners from the target element
- D) Prevents the event from being dispatched at all

---

**5.** You attach a `click` listener to a `<ul>` instead of to each `<li>`. Inside the handler, you use `e.target.closest("li")`. A user clicks a `<span>` inside a `<li>`. What does `e.target.closest("li")` return?

- A) The `<span>` element
- B) The `<ul>` element
- C) The `<li>` element containing the `<span>`
- D) `null`

---

**6.** What is the purpose of `aria-live="polite"` on a form error message element?

- A) It delays showing the error until the user clicks Submit
- B) It announces the element's content to screen readers when it changes
- C) It prevents the element from receiving keyboard focus
- D) It visually hides the element until JavaScript shows it

---

**7.** What does `Object.fromEntries(new FormData(form))` NOT correctly capture?

- A) Text input values
- B) Select dropdown values
- C) Multiple checked checkboxes with the same `name`
- D) Textarea values

---

**8.** You call `fetch("/api/data")` and the server returns a `503 Service Unavailable`. Which statement is true?

- A) The Promise is rejected with a network error
- B) The Promise resolves; `response.ok` is `false`; `response.status` is `503`
- C) The browser automatically retries the request
- D) An exception is thrown before the Promise resolves

---

**9.** What does `AbortController.abort()` do to an in-flight `fetch`?

- A) Sends a cancel signal to the server to stop processing
- B) Rejects the fetch Promise with an `AbortError` on the client
- C) Pauses the request until `resume()` is called
- D) Closes the network connection without error

---

**10.** Which code correctly makes two fetch calls in parallel and waits for both?

- A)
  ```javascript
  const a = await fetch("/a")
  const b = await fetch("/b")
  ```
- B)
  ```javascript
  const [a, b] = await Promise.all([fetch("/a"), fetch("/b")])
  ```
- C)
  ```javascript
  const a = fetch("/a")
  const b = fetch("/b")
  await a; await b
  ```
- D)
  ```javascript
  Promise.all([fetch("/a"), fetch("/b")]).then(([a, b]) => {})
  ```

---

**11.** CORS prevents a browser from reading a cross-origin response unless the server:

- A) Has a valid HTTPS certificate
- B) Is on the same IP address as the requesting page
- C) Returns an `Access-Control-Allow-Origin` header permitting the origin
- D) Uses HTTP/2 or later

---

**12.** A `debounce` function fires the wrapped function:

- A) Once per animation frame (every ~16ms)
- B) After a pause in calls — only when no new calls arrive within the delay period
- C) At a fixed maximum rate regardless of call frequency
- D) Only on the first call and then ignores subsequent calls

---

**13.** You want to detect when an image enters the viewport to lazy-load it. Which API is most appropriate?

- A) `MutationObserver`
- B) `ResizeObserver`
- C) `IntersectionObserver`
- D) `PerformanceObserver`

---

**14.** What is layout thrashing?

- A) Inserting too many elements at once, overflowing the DOM
- B) Alternating DOM reads and writes in a loop, forcing repeated layout recalculations
- C) Setting CSS properties that trigger hardware compositing incorrectly
- D) Applying CSS transitions to too many elements simultaneously

---

**15.** A `CustomEvent` is dispatched with `{ bubbles: true, detail: { count: 5 } }`. A parent element listens for this event. How does the parent access `count`?

- A) `event.count`
- B) `event.data.count`
- C) `event.detail.count`
- D) `event.payload.count`

---

## Part 2: Short Answer

Suggested answers: [solutions/06-quiz-solutions.md](solutions/06-quiz-solutions.md)

**1.** Explain event delegation. Why is it preferable to attaching listeners to each individual list item?

**2.** What is the difference between debounce and throttle? Give one concrete use case for each.

**3.** Explain why `fetch` does not automatically throw for HTTP error status codes, and show how to handle this correctly.

**4.** What is CORS? Why is it a browser concern rather than a server concern? How does a server enable cross-origin access?

**5.** Walk through what happens when a user submits the real-time validation form from page 03, including what fires, what runs, what shows on screen, and what happens if there's an error vs. success.

---

## Part 3: Code Challenges

Solutions: [solutions/code-challenges-solutions.md](solutions/code-challenges-solutions.md)

---

### Challenge 1: Delegated Interactive List

Create `list.html`. Build a task manager that:
- Has an `<input>` and "Add" button at the top
- Displays tasks in a `<ul>` using a `<template>` element for the card structure
- Each task card has: task text, a "Done" toggle button, a "Delete" button, and a priority badge (Low/Medium/High shown as a colored dot)
- Uses **one** `click` listener on the `<ul>` for all task interactions (delegation)
- Tasks added after initial render also work with the delegated listener
- Shows a count of incomplete tasks

---

### Challenge 2: Real-Time Search with Abort

Create `search.html`. Build a live search interface that:
- Has a text input for searching
- Fetches from `https://restcountries.com/v3.1/name/{query}?fields=name,flags,population` on each keystroke (debounced 300ms)
- Shows a loading indicator while fetching
- Cancels the previous request if a new keystroke arrives before it completes
- Displays results as a list of flag + name + population
- Shows "No countries found" for a 404 response (REST Countries returns 404 for no matches)
- Shows a friendly error message for network failures

---

### Challenge 3: Multi-Step Form

Build a 3-step registration form:
- **Step 1:** Name (required, min 2 chars) + Email (required, valid format)
- **Step 2:** Username (required, 3–20 chars, alphanumeric only using pattern) + Password (min 8 chars) + Confirm Password (must match)
- **Step 3:** Review page showing all entered values, with a "Back" button and "Create Account" submit button
- A progress bar at the top shows current step (e.g., Step 2 of 3)
- Each "Next" button validates only the current step's fields before advancing
- Clicking "Back" restores the previous step's values
- On submit, disable the button, wait 1.5s (simulate an API call), then show a success message

---

### Challenge 4: Drag-and-Drop File Upload with Preview

Build `upload.html`:
- A styled drop zone that accepts image files
- Visual feedback: border changes color when dragging over it
- On file drop or file input selection:
  - Validate file type (must be image/*)
  - Validate file size (max 5 MB)
  - Show an image preview
  - Show filename, file size in KB
  - Show a "Remove" button
- Support multiple files — show a grid of previews
- A "Upload" button that uses `FormData` + `fetch` to POST to `https://httpbin.org/post` and logs the response

---

### Challenge 5: Country Explorer Extension

Extend the Country Explorer from page 05 with:
1. **Comparison mode** — a "Compare" toggle on each card. When two cards are toggled, a comparison panel appears at the bottom showing the two countries side by side (flag, name, population, area, languages)
2. **URL-persisted search** — when the user types in the search box or changes the region filter, update the URL query string (e.g., `?search=germany&region=Europe`) using `history.pushState`. On page load, read the URL params and pre-fill the controls
3. **Clipboard share** — a "Share" button on the detail view copies the current URL to clipboard using `navigator.clipboard.writeText()`, then briefly shows "Copied!" tooltip

---

## Completion Checklist

- [ ] I can traverse the DOM using `closest()` and sibling/parent properties
- [ ] I understand event bubbling and can explain when to use `stopPropagation` vs `preventDefault`
- [ ] I can implement event delegation for dynamic content
- [ ] I can create and listen for custom DOM events
- [ ] I can write a `debounce` function from scratch
- [ ] I can capture and validate form data, showing errors inline
- [ ] I can cancel `fetch` requests with `AbortController`
- [ ] I can use `Promise.all` for parallel requests
- [ ] I can explain CORS to someone who's never heard of it
- [ ] I built the Country Explorer and it works end-to-end
- [ ] I completed at least 3 of the 5 code challenges

---

*Ready for the next section? → [Section 6: Version Control with Git](../section-06-git/01-why-version-control.md)*

*Review sources: [07 — Sources](07-sources.md)*
