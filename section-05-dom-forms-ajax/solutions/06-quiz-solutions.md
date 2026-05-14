# Solutions — 06 Section Quiz

Answer key for [06 — Quiz and Checkpoints](../06-quiz-and-checkpoints.md).

---

## Part 1: Multiple Choice — Answer Key

| # | Answer | Explanation |
|---|--------|-------------|
| 1 | **B — The `<div class="card">` element** | `closest()` walks *up* the DOM from the element it's called on, returning the first ancestor (or self) matching the selector. Called on `e.target` (the `<button>`), it finds the nearest `.card` ancestor — the `<div>`. |
| 2 | **C — A, C, B** | Listener A has `{ capture: true }`, so it fires during the *down* (capture) phase — first. Then C fires on the `<button>` itself (target phase). Then B fires on the `<div>` during the *up* (bubble) phase — last. |
| 3 | **B — Batches all insertions into a single DOM update** | `DocumentFragment` is off-screen. You append all nodes to it in memory, then do one `appendChild(fragment)` — one layout recalculation, one repaint. Each individual `appendChild` to a live DOM node triggers reflow/repaint. |
| 4 | **B — Prevents the event from travelling to parent elements** | `stopPropagation()` stops bubbling (or capturing). `preventDefault()` stops the browser's *default action* (form submission, link navigation, etc.) but does NOT stop the event from travelling. |
| 5 | **C — The `<li>` element** | `closest("li")` starts at the click target (`<span>`) and walks up: `<span>` → `<li>`. The `<li>` matches, so it's returned. `closest` always walks up from the starting element, so clicking anywhere inside a `<li>` reaches the `<li>`. |
| 6 | **B — Announces content to screen readers when it changes** | `aria-live="polite"` makes a screen reader announce the element's updated text content the next time the user is idle (without interrupting). Used for error messages that appear dynamically — screen reader users hear the error without having to navigate to it. |
| 7 | **C — Multiple checked checkboxes with the same `name`** | `Object.fromEntries(new FormData(form))` only keeps the *last* value for duplicate keys. Multiple checkboxes with `name="interests"` would only keep one. Use `fd.getAll("interests")` for multi-value fields. |
| 8 | **B — Resolves; `response.ok` is `false`; status is `503`** | `fetch` only rejects on *network failures* (no connection, DNS failure). HTTP error responses (4xx, 5xx) resolve the Promise normally. You must check `response.ok` or `response.status` yourself. |
| 9 | **B — Rejects the fetch Promise with an `AbortError` on the client** | `abort()` causes the `fetch` Promise to reject with a `DOMException` named `"AbortError"`. It does not communicate with the server — the server may continue processing. You handle this in `catch` by checking `error.name === "AbortError"`. |
| 10 | **B — `const [a, b] = await Promise.all([fetch("/a"), fetch("/b")])`** | `Promise.all` starts both fetches simultaneously. Awaiting it waits for *both* to finish. Option A is sequential (waits for `/a` before starting `/b`). Option C starts both but then awaits them sequentially — same duration as sequential. Option D is missing `await`, so the code after it runs before the fetches complete. |
| 11 | **C — Returns an `Access-Control-Allow-Origin` header permitting the origin** | CORS is enforced by the browser. The browser checks the response headers — if the server includes `Access-Control-Allow-Origin: *` (or the specific requesting origin), the browser allows the response. Otherwise, the browser blocks it even though the request was made. |
| 12 | **B — After a pause in calls — only when no new calls arrive within the delay** | Debounce resets the timer on every call. The wrapped function only fires when calls stop for the specified delay. Throttle (not debounce) fires at a fixed rate. Option A describes `requestAnimationFrame`. Option D describes a once-only wrapper. |
| 13 | **C — `IntersectionObserver`** | `IntersectionObserver` fires when an element enters or exits the viewport (or any scroll container). It's designed specifically for lazy loading, infinite scroll, and scroll-triggered animations — without expensive scroll event polling. `MutationObserver` watches DOM structure changes. |
| 14 | **B — Alternating DOM reads and writes, forcing repeated recalculations** | Layout thrashing happens when a read (like `offsetWidth`) follows a write (like `style.width = ...`), forcing the browser to recompute layout before it can answer the read. In a loop, this can happen hundreds of times. Batch all reads, then batch all writes. |
| 15 | **C — `event.detail.count`** | `CustomEvent` stores custom data in `event.detail`. `event.count` would be `undefined`. `event.data` is not a standard property. |

---

## Part 2: Short Answer — Suggested Responses

### 1. Event delegation — explanation and advantages

Event delegation is a pattern where you attach **one** event listener to a parent element instead of individual listeners on each child. The listener uses `e.target` (the actual clicked element) and `closest()` to determine what was interacted with.

**Why it's preferable:**

1. **Works for dynamic content:** Listeners on existing elements don't fire for newly added elements. A listener on the parent fires for all children — even ones added after the listener was attached.
2. **Memory efficiency:** 1,000 list items with individual listeners = 1,000 listener objects in memory. One listener on the parent = 1 listener.
3. **Simpler code:** One listener function handles all similar interactions, rather than the same function attached many times.

**Example:**
```javascript
// One listener handles all current and future list items:
document.querySelector("#task-list").addEventListener("click", e => {
  if (e.target.matches(".btn-delete")) e.target.closest("li").remove()
})
```

---

### 2. Debounce vs Throttle

**Debounce** fires the function only after calls have stopped for a specified delay. Each new call restarts the timer.
- **Use case:** Search-as-you-type. You want to query the API only when the user pauses, not on every keystroke.
- The function fires 300ms after the *last* keystroke.

**Throttle** fires the function at most once per interval, regardless of how many calls arrive.
- **Use case:** Window resize handler or scroll handler updating a progress bar. You want updates at most every 100ms — not 60 times per second.
- The function fires immediately, then ignores further calls for 100ms.

| | Debounce | Throttle |
|---|---|---|
| Fires when | Calls stop | On a fixed interval |
| Good for | Search input, auto-save | Scroll, resize, mouse move |

---

### 3. Fetch and HTTP errors

`fetch` only rejects its Promise on *network errors* — complete failure to make the request (no internet, DNS failure, server unreachable). HTTP error status codes (400, 404, 500) are valid HTTP responses — the server responded, just with an error. `fetch` resolves the Promise in these cases.

To handle HTTP errors correctly:

```javascript
async function fetchJSON(url, options = {}) {
  const response = await fetch(url, options)

  if (!response.ok) {
    // response.ok is false for 4xx and 5xx
    const errorBody = await response.text()
    throw new Error(`HTTP ${response.status}: ${errorBody}`)
  }

  return response.json()
}
```

This ensures you handle both network failures (Promise rejection) and server errors (resolved but !ok) with `try/catch`.

---

### 4. CORS

**CORS (Cross-Origin Resource Sharing)** is a browser security policy that restricts JavaScript from reading responses from a different origin (different domain, protocol, or port) than the page it runs on.

**Why it's a browser concern, not a server concern:** CORS is enforced by the browser — not the server. The server always receives and processes the request. The browser checks the response headers, and if the server didn't include `Access-Control-Allow-Origin` permitting the origin, the browser refuses to give the response to JavaScript. Tools like `curl` and `node-fetch` ignore CORS entirely.

**Why it exists:** To prevent malicious websites from making authenticated requests to other sites on your behalf. Without CORS, `evil.com` could run JavaScript that calls `yourbank.com/transfer` using your stored cookies.

**How to enable cross-origin access:** The server adds the response header:
```
Access-Control-Allow-Origin: https://myapp.com   // specific origin
Access-Control-Allow-Origin: *                   // any origin (for public APIs)
```

For requests with credentials (cookies, Authorization), `*` is not allowed — the specific origin must be listed.

---

### 5. Real-time validation form — full walkthrough

When a user fills in the form and clicks Submit:

1. **`submit` event fires** on the form element
2. **`e.preventDefault()`** prevents the browser from navigating to the form's `action` URL
3. JavaScript reads all `input` and `textarea` elements in the form
4. **`validateField(input)` runs for each field:**
   - Calls the appropriate validator function from the `validators` object
   - If the validator returns an error string: sets `.error-msg` text, adds `.invalid` class to input, sets `aria-invalid="true"`
   - If valid: clears `.error-msg`, adds `.valid` class, removes `aria-invalid`
5. **If any field failed:** `Array.every()` returns `false`. The code focuses the first `.invalid` element so the user's attention goes there. Returns early — no submission.
6. **If all fields valid:** Calls `submitForm()` with the form data
7. **`submitForm()`** disables the submit button (prevents double-submit) and shows "Sending..."
8. Sends a `fetch` POST request with the JSON data
9. **On success:** Shows success message, calls `form.reset()`
10. **On failure:** Shows error message with the server's error text
11. **`finally` block:** Re-enables the submit button regardless of outcome

During editing, the `blur` event on each input triggers `validateField` — the user sees errors field by field as they complete them. The `input` event clears errors as the user types to fix them (so errors disappear immediately once correct, without waiting for another blur).

---

*Back to quiz: [06 — Quiz and Checkpoints](../06-quiz-and-checkpoints.md)*
