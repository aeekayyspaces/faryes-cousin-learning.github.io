# 08 — Your First JavaScript-Powered Website: DOM, Forms, and Fetch

## Learning Objectives

By the end of this page, you will be able to:

- Select and manipulate DOM elements with JavaScript
- Handle user events from forms and buttons
- Validate form input with JavaScript
- Make HTTP requests using the Fetch API
- Handle asynchronous code with `async/await`
- Parse and display JSON data from a real API
- Build a complete interactive web application

---

## Revisiting DOM Manipulation

We introduced the DOM in Section 3. Now we go deeper.

### Selecting Elements

```javascript
// Single element — returns first match:
document.getElementById("my-id")
document.querySelector("#my-id")          // CSS selector syntax
document.querySelector(".card")           // first .card element
document.querySelector("nav a")           // first <a> inside <nav>

// Multiple elements — returns NodeList (array-like):
document.querySelectorAll(".card")         // all .card elements
document.querySelectorAll("input[type='text']")

// Convert NodeList to real array for array methods:
const items = Array.from(document.querySelectorAll("li"))
items.map(li => li.textContent)
```

### Reading and Writing Content

```javascript
const h1 = document.querySelector("h1")

h1.textContent          // read text content (no HTML)
h1.textContent = "New Title"   // set — safe, won't parse HTML

h1.innerHTML            // read HTML inside
h1.innerHTML = "<em>Styled Title</em>"  // set — parses HTML
                        // ⚠️ Never use innerHTML with user input — XSS risk!

h1.innerText            // like textContent but respects CSS visibility
```

### Changing Attributes

```javascript
const img = document.querySelector("img")

img.src             // read src attribute
img.src = "new.jpg" // set src attribute

img.getAttribute("alt")         // "A photo"
img.setAttribute("alt", "New description")
img.removeAttribute("disabled")

// Input values:
const input = document.querySelector("input")
input.value         // the current value the user typed
input.value = ""    // clear the input
```

### Classes

```javascript
const card = document.querySelector(".card")

card.classList.add("highlight")        // add a class
card.classList.remove("highlight")     // remove
card.classList.toggle("expanded")      // add if absent, remove if present
card.classList.contains("highlight")   // true/false
card.classList.replace("old", "new")   // replace one class with another
```

### Creating and Inserting Nodes

```javascript
// Create:
const li = document.createElement("li")
li.textContent = "New item"
li.classList.add("list-item")

// Insert:
const ul = document.querySelector("ul")
ul.appendChild(li)              // append at end
ul.prepend(li)                  // insert at beginning
ul.insertBefore(li, ul.firstChild)  // before a specific node

// Modern — insert relative to a reference element:
const refItem = document.querySelector("#item-3")
refItem.before(li)    // insert before refItem
refItem.after(li)     // insert after refItem

// Remove:
li.remove()                     // remove the element itself
ul.removeChild(li)              // remove a specific child
```

---

## Events and Event Listeners

Events are the bridge between user actions and your JavaScript.

### Adding Event Listeners

```javascript
const button = document.querySelector("#submit-btn")

// addEventListener(eventType, callbackFunction)
button.addEventListener("click", function(event) {
  console.log("Button clicked!")
  console.log(event)    // the Event object — contains details about the event
})

// Arrow function version:
button.addEventListener("click", (e) => {
  console.log("Clicked at:", e.clientX, e.clientY)  // mouse coordinates
})
```

### The Event Object

```javascript
document.addEventListener("keydown", (event) => {
  console.log(event.key)        // "a", "Enter", "ArrowUp", etc.
  console.log(event.code)       // "KeyA", "Enter", "ArrowUp"
  console.log(event.ctrlKey)    // true if Ctrl is held
  console.log(event.shiftKey)   // true if Shift is held
})
```

### Common Event Types

| Event | Fires When |
|-------|-----------|
| `click` | Element is clicked |
| `dblclick` | Double-clicked |
| `mouseover` | Mouse enters element |
| `mouseout` | Mouse leaves element |
| `keydown` | Key is pressed |
| `keyup` | Key is released |
| `input` | Input value changes (every character) |
| `change` | Input value changes and loses focus |
| `submit` | Form is submitted |
| `focus` | Element receives focus |
| `blur` | Element loses focus |
| `load` | Resource finishes loading |
| `DOMContentLoaded` | DOM is fully parsed |

### Preventing Default Behavior

Some elements have default browser behavior. Use `preventDefault()` to stop it:

```javascript
// Prevent form from navigating to action URL:
form.addEventListener("submit", (e) => {
  e.preventDefault()
  // Now handle the form with JavaScript
})

// Prevent a link from navigating:
link.addEventListener("click", (e) => {
  e.preventDefault()
  console.log("Link clicked but not followed")
})
```

### Event Delegation

Instead of attaching listeners to each item, attach one listener to the parent:

```javascript
// ❌ Individual listeners — breaks when new items are added dynamically:
document.querySelectorAll(".item").forEach(item => {
  item.addEventListener("click", handleClick)
})

// ✅ Event delegation — one listener on parent, works for dynamic items too:
const list = document.querySelector("#task-list")
list.addEventListener("click", (e) => {
  if (e.target.matches(".delete-btn")) {
    e.target.closest("li").remove()
  }
  if (e.target.matches(".done-btn")) {
    e.target.closest("li").classList.toggle("done")
  }
})
```

---

## Form Handling and Validation

### Capturing Form Data

```javascript
const form = document.querySelector("#signup-form")

form.addEventListener("submit", (e) => {
  e.preventDefault()

  // Read individual fields:
  const name = document.querySelector("#name").value.trim()
  const email = document.querySelector("#email").value.trim()

  // Or read all fields at once with FormData:
  const formData = new FormData(form)
  const data = Object.fromEntries(formData)
  // { name: "Alex", email: "a@b.com", message: "Hello" }

  console.log(data)
})
```

### Client-Side Validation

```javascript
function validateForm(data) {
  const errors = {}

  if (!data.name || data.name.length < 2) {
    errors.name = "Name must be at least 2 characters"
  }

  if (!data.email || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(data.email)) {
    errors.email = "Please enter a valid email address"
  }

  if (!data.password || data.password.length < 8) {
    errors.password = "Password must be at least 8 characters"
  }

  return errors  // empty object = no errors
}

function showErrors(errors) {
  // Clear previous errors
  document.querySelectorAll(".error-msg").forEach(el => el.remove())

  for (const [field, message] of Object.entries(errors)) {
    const input = document.querySelector(`#${field}`)
    const errorEl = document.createElement("p")
    errorEl.className = "error-msg"
    errorEl.textContent = message
    errorEl.style.color = "red"
    input.after(errorEl)   // insert after the input
  }
}

form.addEventListener("submit", (e) => {
  e.preventDefault()
  const data = Object.fromEntries(new FormData(form))
  const errors = validateForm(data)

  if (Object.keys(errors).length > 0) {
    showErrors(errors)
    return
  }

  // No errors — proceed
  console.log("Form is valid:", data)
})
```

---

## Asynchronous JavaScript: Fetch and async/await

Fetching data from a server takes time. JavaScript handles this with **asynchronous** code — the rest of the program doesn't freeze while waiting.

### Promises: The Foundation

A **Promise** is an object representing a future value — it's either pending, fulfilled (resolved), or rejected:

```javascript
// Promise chain style:
fetch("https://api.example.com/users")
  .then(response => response.json())   // parse JSON
  .then(data => console.log(data))     // use data
  .catch(error => console.error(error))
```

### `async/await`: Cleaner Syntax

`async/await` makes asynchronous code look synchronous — far easier to read:

```javascript
async function getUsers() {
  try {
    const response = await fetch("https://api.example.com/users")
    const data = await response.json()
    console.log(data)
  } catch (error) {
    console.error("Failed to fetch users:", error)
  }
}

getUsers()
```

- `async` marks a function as asynchronous — it always returns a Promise
- `await` pauses execution inside the `async` function until the Promise resolves
- The function doesn't block — other code continues while the async function waits
- `try/catch` handles both network errors and JSON parse errors

### Checking Response Status

```javascript
async function fetchData(url) {
  const response = await fetch(url)

  if (!response.ok) {
    // response.ok is true for 200-299 status codes
    throw new Error(`HTTP ${response.status}: ${response.statusText}`)
  }

  return response.json()
}
```

---

## Project: Weather Card with Live Data

Build a complete weather widget using the [Open-Meteo API](https://open-meteo.com/) — free, no API key required.

### Create `weather.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Weather Card</title>
  <link rel="stylesheet" href="weather.css">
</head>
<body>

  <main class="container">
    <h1>Weather Lookup</h1>

    <form id="city-form">
      <div class="input-row">
        <input
          type="text"
          id="city-input"
          placeholder="Enter a city name..."
          autocomplete="off"
        >
        <button type="submit">Search</button>
      </div>
    </form>

    <div id="weather-card" class="weather-card hidden">
      <div class="weather-header">
        <h2 id="city-name">—</h2>
        <p id="weather-description" class="description">—</p>
      </div>
      <div class="weather-stats">
        <div class="stat">
          <span class="stat-value" id="temperature">—</span>
          <span class="stat-label">Temperature</span>
        </div>
        <div class="stat">
          <span class="stat-value" id="humidity">—</span>
          <span class="stat-label">Humidity</span>
        </div>
        <div class="stat">
          <span class="stat-value" id="wind-speed">—</span>
          <span class="stat-label">Wind Speed</span>
        </div>
      </div>
    </div>

    <p id="error-message" class="error hidden"></p>
    <p id="loading" class="loading hidden">Loading weather data...</p>
  </main>

  <script src="weather.js" defer></script>
</body>
</html>
```

### Create `weather.css`

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: 'Segoe UI', Arial, sans-serif;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
}

.container {
  width: 100%;
  max-width: 480px;
}

h1 {
  color: #e0e0ff;
  text-align: center;
  margin-bottom: 1.5rem;
  font-size: 1.75rem;
}

.input-row {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
}

input {
  flex: 1;
  padding: 0.75rem 1rem;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  background: rgba(255,255,255,0.1);
  color: white;
  border: 1px solid rgba(255,255,255,0.2);
}

input::placeholder { color: rgba(255,255,255,0.4); }
input:focus { outline: none; border-color: #a78bfa; }

button {
  padding: 0.75rem 1.25rem;
  background: #7c3aed;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: background 0.2s;
}

button:hover { background: #6d28d9; }

.weather-card {
  background: rgba(255,255,255,0.08);
  border: 1px solid rgba(255,255,255,0.15);
  border-radius: 16px;
  padding: 2rem;
  backdrop-filter: blur(10px);
}

.weather-header { text-align: center; margin-bottom: 1.5rem; }
.weather-header h2 { color: white; font-size: 1.75rem; margin-bottom: 0.25rem; }
.description { color: #a78bfa; font-size: 1rem; }

.weather-stats {
  display: flex;
  justify-content: space-around;
  gap: 1rem;
}

.stat {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.25rem;
}

.stat-value {
  font-size: 1.5rem;
  font-weight: 700;
  color: white;
}

.stat-label {
  font-size: 0.75rem;
  color: rgba(255,255,255,0.5);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.error { color: #f87171; text-align: center; font-size: 0.95rem; }
.loading { color: rgba(255,255,255,0.6); text-align: center; }
.hidden { display: none; }
```

### Create `weather.js`

```javascript
// WMO Weather Interpretation Codes
// https://open-meteo.com/en/docs#weathervariables
const weatherDescriptions = {
  0:  "Clear sky",
  1:  "Mainly clear",
  2:  "Partly cloudy",
  3:  "Overcast",
  45: "Foggy",
  48: "Depositing rime fog",
  51: "Light drizzle",
  53: "Moderate drizzle",
  55: "Dense drizzle",
  61: "Slight rain",
  63: "Moderate rain",
  65: "Heavy rain",
  71: "Slight snow",
  73: "Moderate snow",
  75: "Heavy snow",
  80: "Slight rain showers",
  81: "Moderate rain showers",
  82: "Violent rain showers",
  95: "Thunderstorm",
  99: "Thunderstorm with hail"
}

// --- DOM References ---
const form         = document.getElementById("city-form")
const cityInput    = document.getElementById("city-input")
const weatherCard  = document.getElementById("weather-card")
const cityName     = document.getElementById("city-name")
const description  = document.getElementById("weather-description")
const temperature  = document.getElementById("temperature")
const humidity     = document.getElementById("humidity")
const windSpeed    = document.getElementById("wind-speed")
const errorMsg     = document.getElementById("error-message")
const loading      = document.getElementById("loading")

// --- Utility Functions ---
function showElement(el)  { el.classList.remove("hidden") }
function hideElement(el)  { el.classList.add("hidden") }

function showError(msg) {
  hideElement(loading)
  hideElement(weatherCard)
  errorMsg.textContent = msg
  showElement(errorMsg)
}

function showLoading() {
  hideElement(weatherCard)
  hideElement(errorMsg)
  showElement(loading)
}

function showWeather(city, data) {
  hideElement(loading)
  hideElement(errorMsg)

  const current = data.current_weather
  const desc = weatherDescriptions[current.weathercode] ?? "Unknown conditions"

  cityName.textContent = city
  description.textContent = desc
  temperature.textContent = `${current.temperature}°C`
  humidity.textContent = `${data.hourly.relativehumidity_2m[0]}%`
  windSpeed.textContent = `${current.windspeed} km/h`

  showElement(weatherCard)
}

// --- API Functions ---
async function geocodeCity(city) {
  // Open-Meteo geocoding API — no key needed
  const url = `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}&count=1`
  const response = await fetch(url)

  if (!response.ok) {
    throw new Error("Geocoding service unavailable")
  }

  const data = await response.json()

  if (!data.results || data.results.length === 0) {
    throw new Error(`City "${city}" not found. Try a different name.`)
  }

  return data.results[0]   // { name, latitude, longitude, country }
}

async function fetchWeather(lat, lon) {
  const url = [
    "https://api.open-meteo.com/v1/forecast",
    `?latitude=${lat}`,
    `&longitude=${lon}`,
    "&current_weather=true",
    "&hourly=relativehumidity_2m",
    "&forecast_days=1"
  ].join("")

  const response = await fetch(url)

  if (!response.ok) {
    throw new Error("Weather service unavailable")
  }

  return response.json()
}

// --- Main Handler ---
async function handleSearch(e) {
  e.preventDefault()

  const city = cityInput.value.trim()
  if (!city) return

  showLoading()

  try {
    // Step 1: Geocode the city name to coordinates
    const location = await geocodeCity(city)

    // Step 2: Fetch weather for those coordinates
    const weather = await fetchWeather(location.latitude, location.longitude)

    // Step 3: Display results (use the official city name from geocoding)
    const displayName = `${location.name}, ${location.country}`
    showWeather(displayName, weather)

  } catch (error) {
    showError(error.message)
  }
}

// --- Event Listener ---
form.addEventListener("submit", handleSearch)
```

**To use it:** Open `weather.html` in your browser, type a city name (try "New York", "London", "Tokyo"), and click Search. The app geocodes the city, fetches current weather from a real API, and displays the results — all with code you wrote.

---

## What This Project Demonstrates

| Concept | Where Used |
|---------|-----------|
| DOM selection | All `document.getElementById()` calls |
| DOM manipulation | `showElement`, `hideElement`, updating text |
| Event listeners | `form.addEventListener("submit", ...)` |
| `preventDefault` | Stopping form from navigating |
| `async/await` | `handleSearch`, `geocodeCity`, `fetchWeather` |
| Error handling | `try/catch` around API calls |
| `throw new Error` | Custom error messages |
| `fetch` API | Both API calls |
| JSON | `response.json()` |
| Template literals | URL construction, display strings |
| Object lookup | `weatherDescriptions[code]` |
| Array destructuring | (used in geocode result) |
| `encodeURIComponent` | Safely encoding city names in URLs |

---

## Bonus: LocalStorage Persistence

Add this to `weather.js` to remember the last searched city:

```javascript
// Save on success:
function showWeather(city, data) {
  localStorage.setItem("lastCity", cityInput.value.trim())
  // ... rest of function
}

// Load on page start:
document.addEventListener("DOMContentLoaded", () => {
  const lastCity = localStorage.getItem("lastCity")
  if (lastCity) {
    cityInput.value = lastCity
    form.dispatchEvent(new Event("submit"))  // auto-search
  }
})
```

---

## Check Your Understanding

1. What is the difference between `textContent` and `innerHTML`? Why is `innerHTML` dangerous with user input?
2. What does `event.preventDefault()` do in a form's `submit` handler?
3. What is the difference between a Promise and `async/await`? Are they different things or related?
4. What happens if you call `await fetch(url)` and the network is down?
5. Why does the weather app call two separate APIs (geocoding and weather) instead of one?

---

*Previous: [07 — Arrays and Objects](07-arrays-and-objects.md)*
*Next: [09 — Quiz and Checkpoints](09-quiz-and-checkpoints.md)*
