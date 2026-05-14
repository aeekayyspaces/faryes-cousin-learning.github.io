# 04 — Fetch and AJAX: Talking to Servers

## Learning Objectives

By the end of this page, you will be able to:

- Explain what AJAX is and how it changed the web
- Configure `fetch` with method, headers, and body
- Handle different response types: JSON, text, blob
- Use `AbortController` to cancel requests and implement timeouts
- Make parallel requests with `Promise.all`
- Explain CORS and why it exists
- Handle loading states, errors, and retries properly
- Send authentication tokens in headers

---

## What Is AJAX?

**AJAX (Asynchronous JavaScript and XML)** is a technique for making HTTP requests from JavaScript without reloading the page — letting the server respond with data that JavaScript uses to update the DOM.

### Before AJAX: Full Page Reloads

Every interaction required reloading the entire page — the server rendered full HTML and sent it down. Clicking "next page" in search results caused a full reload. Submitting a form navigated away.

### The AJAX Revolution (2004–2005)

Google Maps (2005) showed the world what AJAX could do — drag the map and new tiles loaded without any page reload. Suddenly, web apps could feel like desktop apps.

Early AJAX used `XMLHttpRequest` (XHR):

```javascript
// The old way — XMLHttpRequest
const xhr = new XMLHttpRequest()
xhr.open("GET", "/api/data")
xhr.onload = () => {
  if (xhr.status === 200) {
    const data = JSON.parse(xhr.responseText)
  }
}
xhr.onerror = () => console.error("Network error")
xhr.send()
```

Modern code uses `fetch` — cleaner and Promise-based.

---

## The Fetch API: Full Configuration

### GET Request (Default)

```javascript
const response = await fetch("https://api.example.com/users")
const users = await response.json()
```

### POST with JSON Body

```javascript
const response = await fetch("https://api.example.com/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Accept": "application/json"
  },
  body: JSON.stringify({
    name: "Alex Rivera",
    email: "alex@example.com"
  })
})

const newUser = await response.json()
```

### PUT — Replace a Resource

```javascript
await fetch(`/api/users/${id}`, {
  method: "PUT",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(updatedUser)
})
```

### PATCH — Partial Update

```javascript
await fetch(`/api/users/${id}`, {
  method: "PATCH",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ email: "newemail@example.com" })
})
```

### DELETE

```javascript
await fetch(`/api/users/${id}`, {
  method: "DELETE"
})
```

---

## Response Handling

`fetch` only rejects its Promise on **network errors** (no connection, DNS failure). HTTP error status codes (4xx, 5xx) resolve normally with `response.ok === false`. Always check:

```javascript
async function fetchJSON(url, options = {}) {
  const response = await fetch(url, options)

  if (!response.ok) {
    const errorText = await response.text()
    throw new Error(`HTTP ${response.status}: ${errorText}`)
  }

  return response.json()
}
```

### Response Types

| Method | Returns | Use For |
|--------|---------|---------|
| `response.json()` | Object/Array | API data |
| `response.text()` | String | HTML, plain text, XML |
| `response.blob()` | Blob | Images, files, binary data |
| `response.formData()` | FormData | Form data from server |
| `response.arrayBuffer()` | ArrayBuffer | Binary data, audio, video |

```javascript
// Download and display an image:
const response = await fetch("/api/avatar/123")
const blob = await response.blob()
const url = URL.createObjectURL(blob)
document.querySelector("#avatar").src = url

// Read response headers:
response.headers.get("Content-Type")   // "application/json"
response.headers.get("X-Request-Id")   // custom header
response.status                         // 200, 404, 500, etc.
response.statusText                     // "OK", "Not Found"
response.url                            // final URL (after redirects)
response.redirected                     // true if there was a redirect
```

---

## Sending Authentication

### Bearer Token (JWT / OAuth)

The most common authentication pattern for APIs:

```javascript
const token = localStorage.getItem("authToken")

const response = await fetch("/api/profile", {
  headers: {
    "Authorization": `Bearer ${token}`
  }
})
```

### API Key in Header

```javascript
const response = await fetch("https://api.example.com/data", {
  headers: {
    "X-API-Key": "your-api-key-here"
  }
})
```

### Cookies (Automatic)

By default, `fetch` does NOT send cookies cross-origin. Use `credentials`:

```javascript
// Same-origin: include cookies automatically
fetch("/api/data", { credentials: "same-origin" })   // default

// Cross-origin: send cookies to trusted origin
fetch("https://api.mysite.com/data", { credentials: "include" })
```

---

## AbortController: Cancelling Requests

Networks are unreliable. Users navigate away. Searches fire rapidly. You need to be able to cancel in-flight requests.

### Cancelling a Request

```javascript
let controller = new AbortController()

async function search(query) {
  // Cancel any previous request
  controller.abort()
  controller = new AbortController()

  try {
    const response = await fetch(`/api/search?q=${encodeURIComponent(query)}`, {
      signal: controller.signal
    })
    const results = await response.json()
    displayResults(results)
  } catch (error) {
    if (error.name === "AbortError") {
      // This is expected — request was cancelled by a new search
      return
    }
    showError(error.message)
  }
}

// As user types, cancel previous search and start new one:
searchInput.addEventListener("input", debounce(e => search(e.target.value), 300))
```

### Timeout Pattern

```javascript
async function fetchWithTimeout(url, options = {}, timeoutMs = 5000) {
  const controller = new AbortController()
  const timeoutId = setTimeout(() => controller.abort(), timeoutMs)

  try {
    const response = await fetch(url, {
      ...options,
      signal: controller.signal
    })
    return response
  } catch (error) {
    if (error.name === "AbortError") {
      throw new Error(`Request timed out after ${timeoutMs}ms`)
    }
    throw error
  } finally {
    clearTimeout(timeoutId)
  }
}

// Usage:
const response = await fetchWithTimeout("/api/data", {}, 3000)
```

---

## Parallel Requests with `Promise.all`

```javascript
// Sequential — slow! Waits for each before starting the next:
const users    = await fetchJSON("/api/users")
const products = await fetchJSON("/api/products")
const stats    = await fetchJSON("/api/stats")

// Parallel — all three fire simultaneously:
const [users, products, stats] = await Promise.all([
  fetchJSON("/api/users"),
  fetchJSON("/api/products"),
  fetchJSON("/api/stats")
])
```

`Promise.all` rejects if **any** promise rejects. Use `Promise.allSettled` if you want results even when some fail:

```javascript
const results = await Promise.allSettled([
  fetchJSON("/api/users"),
  fetchJSON("/api/might-fail"),
  fetchJSON("/api/products")
])

for (const result of results) {
  if (result.status === "fulfilled") {
    console.log("Got:", result.value)
  } else {
    console.warn("Failed:", result.reason.message)
  }
}
```

---

## CORS: Why Some Requests Are Blocked

**CORS (Cross-Origin Resource Sharing)** is a browser security mechanism that restricts web pages from making requests to a different origin than the one that served the page.

An **origin** is: `protocol + domain + port`. These are different origins:
- `https://mysite.com` and `https://api.mysite.com` — different subdomain
- `http://mysite.com` and `https://mysite.com` — different protocol
- `https://mysite.com` and `https://mysite.com:8080` — different port

### Why It Exists

Without CORS, malicious JavaScript on `evil.com` could make requests to `yourbank.com` using your stored cookies — silently transferring money. CORS prevents this.

### How It Works

The browser adds an `Origin` header to cross-origin requests. The server responds with `Access-Control-Allow-Origin` to indicate whether the request is permitted:

```
Request:  Origin: https://myapp.com
Response: Access-Control-Allow-Origin: https://myapp.com   ← allowed
Response: Access-Control-Allow-Origin: *                   ← anyone can use
```

If the server doesn't include the header, the browser **blocks the response** — the request was made, but JavaScript can't read the result.

### CORS Is a Browser Thing

CORS is enforced by the **browser** — it does NOT apply to server-to-server requests. `curl` and Node.js ignore CORS. Only browsers enforce it.

**Public APIs** (like Open-Meteo) include `Access-Control-Allow-Origin: *` so any website can use them. Private APIs restrict this to their own domain.

### Preflight Requests

For non-simple requests (custom headers, JSON body, DELETE/PATCH), the browser first sends an `OPTIONS` preflight to ask if the request is allowed:

```
OPTIONS /api/users
Origin: https://myapp.com
Access-Control-Request-Method: DELETE
Access-Control-Request-Headers: Authorization

→ 204 No Content
   Access-Control-Allow-Origin: https://myapp.com
   Access-Control-Allow-Methods: GET, POST, DELETE
   Access-Control-Allow-Headers: Authorization
```

If the preflight succeeds, the actual request is sent.

---

## Loading States and UI Feedback

Requests take time. Always give the user feedback:

```javascript
async function loadData() {
  const container = document.querySelector("#results")
  const loadingEl = document.querySelector("#loading")
  const errorEl   = document.querySelector("#error")

  // Reset state
  container.innerHTML = ""
  errorEl.classList.add("hidden")
  loadingEl.classList.remove("hidden")

  try {
    const data = await fetchJSON("/api/items")
    renderItems(data)
  } catch (error) {
    errorEl.textContent = `Failed to load: ${error.message}`
    errorEl.classList.remove("hidden")
  } finally {
    loadingEl.classList.add("hidden")
  }
}
```

### Skeleton Screens

Better than spinners for content-heavy layouts — show the shape of the content before it arrives:

```css
.skeleton {
  background: linear-gradient(90deg,
    #f0f0f0 25%, #e8e8e8 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
  border-radius: 4px;
}

.skeleton-title   { height: 1.5rem; width: 60%; }
.skeleton-text    { height: 1rem; width: 100%; margin-bottom: 0.5rem; }
.skeleton-avatar  { height: 40px; width: 40px; border-radius: 50%; }

@keyframes shimmer {
  from { background-position: 200% 0; }
  to   { background-position: -200% 0; }
}
```

---

## Retry with Exponential Backoff

For flaky networks or temporarily unavailable services, retry with increasing delays:

```javascript
async function fetchWithRetry(url, options = {}, maxRetries = 3) {
  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      const response = await fetchWithTimeout(url, options)
      if (!response.ok) throw new Error(`HTTP ${response.status}`)
      return response
    } catch (error) {
      if (attempt === maxRetries) throw error   // give up

      // Exponential backoff: 300ms, 600ms, 1200ms...
      const delay = 300 * Math.pow(2, attempt)
      console.warn(`Attempt ${attempt + 1} failed. Retrying in ${delay}ms...`)
      await new Promise(resolve => setTimeout(resolve, delay))
    }
  }
}
```

---

## A Reusable API Client

In real applications, wrap `fetch` in a module to avoid repeating headers and base URLs everywhere:

```javascript
// api.js
const BASE_URL = "https://api.example.com"

async function request(path, options = {}) {
  const token = localStorage.getItem("token")

  const response = await fetch(`${BASE_URL}${path}`, {
    ...options,
    headers: {
      "Content-Type": "application/json",
      "Accept": "application/json",
      ...(token && { "Authorization": `Bearer ${token}` }),
      ...options.headers   // allow caller to override
    }
  })

  if (response.status === 401) {
    // Token expired — redirect to login
    localStorage.removeItem("token")
    window.location.href = "/login"
    return
  }

  if (!response.ok) {
    const error = await response.json().catch(() => ({ message: response.statusText }))
    throw new Error(error.message || `HTTP ${response.status}`)
  }

  if (response.status === 204) return null   // No Content
  return response.json()
}

export const api = {
  get:    (path)             => request(path),
  post:   (path, body)       => request(path, { method: "POST",   body: JSON.stringify(body) }),
  put:    (path, body)       => request(path, { method: "PUT",    body: JSON.stringify(body) }),
  patch:  (path, body)       => request(path, { method: "PATCH",  body: JSON.stringify(body) }),
  delete: (path)             => request(path, { method: "DELETE" })
}

// Usage:
const users = await api.get("/users")
const user  = await api.post("/users", { name: "Alex", email: "a@b.com" })
await api.delete(`/users/${id}`)
```

---

## Check Your Understanding

1. Why does `fetch` not throw an error for a `404` or `500` response?
2. What problem does `AbortController` solve in a search-as-you-type feature?
3. What is CORS and why does it exist? Can you bypass it by using Node.js on the server?
4. What is the difference between `Promise.all` and `Promise.allSettled`?
5. Why is it important to show loading states when making fetch requests?

---

*Previous: [03 — Forms and Validation](03-forms-and-validation.md)*
*Next: [05 — Project: Country Explorer](05-project-country-explorer.md)*
