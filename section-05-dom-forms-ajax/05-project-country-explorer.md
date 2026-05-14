# 05 — Project: Country Explorer

## Learning Objectives

By building this project you will:

- Wire together everything from this section: DOM manipulation, events, forms, and Fetch
- Build a search-and-filter interface driven entirely by real API data
- Implement a detail view with routing via URL hash
- Use `localStorage` to persist favorites across sessions
- Handle all loading, error, and empty states
- Apply debouncing, event delegation, and `AbortController` in a real context

---

## What You're Building

A **Country Explorer** — a searchable, filterable catalog of every country in the world, powered by the free [REST Countries API](https://restcountries.com) (no key required).

**Features:**
- Live search as you type (debounced)
- Filter by region (Africa, Americas, Asia, Europe, Oceania)
- Sort by name or population
- Click a country card to see full details
- Favorite/unfavorite countries (saved to localStorage)
- Back/forward navigation with URL hash routing

---

## File Structure

```
country-explorer/
├── index.html
├── style.css
└── app.js
```

---

## `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Country Explorer</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>

  <!-- LIST VIEW -->
  <div id="list-view">
    <header class="site-header">
      <h1>Country Explorer</h1>
      <span id="fav-count" class="fav-badge hidden"></span>
    </header>

    <div class="controls">
      <div class="search-wrap">
        <input type="search" id="search-input" placeholder="Search countries...">
      </div>
      <div class="filters">
        <select id="region-filter">
          <option value="">All Regions</option>
          <option value="Africa">Africa</option>
          <option value="Americas">Americas</option>
          <option value="Asia">Asia</option>
          <option value="Europe">Europe</option>
          <option value="Oceania">Oceania</option>
        </select>
        <select id="sort-select">
          <option value="name">Sort: Name</option>
          <option value="population-desc">Sort: Population ↓</option>
          <option value="population-asc">Sort: Population ↑</option>
        </select>
        <button id="fav-toggle" class="btn-outline">Show All</button>
      </div>
    </div>

    <p id="result-count" class="result-count"></p>

    <!-- Loading skeletons -->
    <div id="loading-grid" class="card-grid">
      ${Array(8).fill('<div class="card skeleton-card"><div class="skeleton sk-img"></div><div class="skeleton sk-title"></div><div class="skeleton sk-text"></div></div>').join("")}
    </div>

    <div id="card-grid" class="card-grid hidden"></div>

    <p id="no-results" class="no-results hidden">No countries match your search.</p>
    <p id="fetch-error" class="fetch-error hidden"></p>
  </div>

  <!-- DETAIL VIEW -->
  <div id="detail-view" class="hidden">
    <button id="btn-back" class="btn-back">← Back</button>
    <div id="detail-content"></div>
  </div>

  <!-- CARD TEMPLATE -->
  <template id="country-card-template">
    <article class="card" data-code="">
      <img class="card-flag" src="" alt="">
      <div class="card-body">
        <h2 class="card-name"></h2>
        <p class="card-stat"><span class="label">Population</span><span class="val pop"></span></p>
        <p class="card-stat"><span class="label">Region</span><span class="val region"></span></p>
        <p class="card-stat"><span class="label">Capital</span><span class="val capital"></span></p>
      </div>
      <button class="btn-fav" aria-label="Add to favourites" aria-pressed="false">♡</button>
    </article>
  </template>

  <script src="app.js" defer></script>
</body>
</html>
```

---

## `style.css`

```css
/* =====================
   Reset & Variables
   ===================== */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --bg: #f0f0f5;
  --surface: #ffffff;
  --text: #111827;
  --text-muted: #6b7280;
  --primary: #7c3aed;
  --primary-dark: #6d28d9;
  --border: #e5e7eb;
  --shadow: 0 2px 8px rgba(0,0,0,0.08);
  --radius: 8px;
}

body {
  font-family: 'Segoe UI', Arial, sans-serif;
  background: var(--bg);
  color: var(--text);
  min-height: 100vh;
}

/* =====================
   Header
   ===================== */
.site-header {
  background: var(--text);
  color: white;
  padding: 1.25rem 2rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  position: sticky;
  top: 0;
  z-index: 10;
}

.site-header h1 { font-size: 1.3rem; }

.fav-badge {
  background: var(--primary);
  color: white;
  font-size: 0.75rem;
  padding: 0.2rem 0.5rem;
  border-radius: 20px;
  font-weight: 700;
}

/* =====================
   Controls
   ===================== */
.controls {
  max-width: 1200px;
  margin: 1.5rem auto 1rem;
  padding: 0 1.5rem;
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  align-items: center;
}

.search-wrap { flex: 1; min-width: 200px; }

#search-input {
  width: 100%;
  padding: 0.65rem 1rem;
  border: 1.5px solid var(--border);
  border-radius: var(--radius);
  font-size: 0.95rem;
  background: var(--surface);
}

#search-input:focus { outline: none; border-color: var(--primary); }

.filters { display: flex; flex-wrap: wrap; gap: 0.5rem; }

select, .btn-outline {
  padding: 0.65rem 1rem;
  border: 1.5px solid var(--border);
  border-radius: var(--radius);
  font-size: 0.9rem;
  background: var(--surface);
  cursor: pointer;
}

.btn-outline:hover { border-color: var(--primary); color: var(--primary); }
.btn-outline.active { background: var(--primary); color: white; border-color: var(--primary); }

/* =====================
   Cards Grid
   ===================== */
.result-count {
  max-width: 1200px;
  margin: 0 auto 0.75rem;
  padding: 0 1.5rem;
  font-size: 0.85rem;
  color: var(--text-muted);
}

.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  gap: 1.25rem;
  max-width: 1200px;
  margin: 0 auto 3rem;
  padding: 0 1.5rem;
}

.card {
  background: var(--surface);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  overflow: hidden;
  cursor: pointer;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
  position: relative;
}

.card:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 20px rgba(0,0,0,0.12);
}

.card-flag {
  width: 100%;
  height: 140px;
  object-fit: cover;
  display: block;
}

.card-body { padding: 1rem; }
.card-name { font-size: 1rem; font-weight: 700; margin-bottom: 0.5rem; }
.card-stat { display: flex; justify-content: space-between; font-size: 0.82rem; margin-bottom: 0.2rem; }
.label { color: var(--text-muted); }

.btn-fav {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
  background: rgba(0,0,0,0.45);
  border: none;
  border-radius: 50%;
  width: 32px;
  height: 32px;
  cursor: pointer;
  font-size: 1rem;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  transition: background 0.15s;
}

.btn-fav:hover           { background: rgba(0,0,0,0.65); }
.btn-fav[aria-pressed="true"] { background: #ef4444; }
.btn-fav[aria-pressed="true"]::after { content: "♥"; }

/* =====================
   Skeleton Loading
   ===================== */
.skeleton { background: linear-gradient(90deg,#e5e7eb 25%,#d1d5db 50%,#e5e7eb 75%); background-size: 200%; animation: shimmer 1.5s infinite; border-radius: 4px; }
.sk-img   { height: 140px; border-radius: 0; }
.sk-title { height: 1rem; width: 70%; margin: 1rem; }
.sk-text  { height: 0.75rem; width: 90%; margin: 0 1rem 0.75rem; }
@keyframes shimmer { from { background-position: 200% 0; } to { background-position: -200% 0; } }

/* =====================
   Messages
   ===================== */
.no-results, .fetch-error {
  text-align: center;
  padding: 3rem;
  color: var(--text-muted);
}
.fetch-error { color: #dc2626; }

/* =====================
   Detail View
   ===================== */
#detail-view {
  max-width: 900px;
  margin: 0 auto;
  padding: 2rem 1.5rem;
}

.btn-back {
  background: var(--surface);
  border: 1.5px solid var(--border);
  padding: 0.5rem 1.25rem;
  border-radius: var(--radius);
  cursor: pointer;
  font-size: 0.9rem;
  margin-bottom: 2rem;
}
.btn-back:hover { background: var(--bg); }

.detail-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 3rem;
  align-items: start;
}

.detail-flag { width: 100%; border-radius: var(--radius); box-shadow: var(--shadow); }
.detail-name { font-size: 2rem; font-weight: 800; margin-bottom: 1.5rem; }
.detail-section { margin-bottom: 1rem; }
.detail-section h3 { color: var(--text-muted); font-size: 0.8rem; text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 0.5rem; }
.detail-section p  { font-size: 1rem; }
.borders { display: flex; flex-wrap: wrap; gap: 0.5rem; margin-top: 0.25rem; }
.border-tag { padding: 0.3rem 0.75rem; border: 1px solid var(--border); border-radius: 4px; font-size: 0.85rem; cursor: pointer; background: var(--surface); }
.border-tag:hover { background: var(--primary); color: white; border-color: var(--primary); }
.detail-fav { margin-top: 1.5rem; padding: 0.6rem 1.25rem; background: var(--primary); color: white; border: none; border-radius: var(--radius); cursor: pointer; font-size: 0.9rem; }
.detail-fav:hover { background: var(--primary-dark); }

@media (max-width: 640px) {
  .detail-grid { grid-template-columns: 1fr; }
}

/* =====================
   Utilities
   ===================== */
.hidden { display: none !important; }
```

---

## `app.js`

```javascript
// ============================================================
// STATE
// ============================================================
let allCountries  = []          // raw API data
let favourites    = new Set(JSON.parse(localStorage.getItem("favourites") ?? "[]"))
let showFavsOnly  = false
let abortCtrl     = new AbortController()

// ============================================================
// DOM REFERENCES
// ============================================================
const listView      = document.getElementById("list-view")
const detailView    = document.getElementById("detail-view")
const cardGrid      = document.getElementById("card-grid")
const loadingGrid   = document.getElementById("loading-grid")
const noResults     = document.getElementById("no-results")
const fetchError    = document.getElementById("fetch-error")
const resultCount   = document.getElementById("result-count")
const searchInput   = document.getElementById("search-input")
const regionFilter  = document.getElementById("region-filter")
const sortSelect    = document.getElementById("sort-select")
const favToggle     = document.getElementById("fav-toggle")
const favCount      = document.getElementById("fav-count")
const detailContent = document.getElementById("detail-content")
const cardTemplate  = document.getElementById("country-card-template")

// ============================================================
// API
// ============================================================
async function fetchCountries() {
  abortCtrl.abort()
  abortCtrl = new AbortController()

  try {
    const response = await fetch(
      "https://restcountries.com/v3.1/all?fields=cca3,name,flags,population,region,capital,borders,languages,currencies,area,timezones,subregion",
      { signal: abortCtrl.signal }
    )

    if (!response.ok) throw new Error(`HTTP ${response.status}`)

    allCountries = await response.json()
    allCountries.sort((a, b) => a.name.common.localeCompare(b.name.common))

    hideElement(loadingGrid)
    showElement(cardGrid)
    renderList()

  } catch (err) {
    if (err.name === "AbortError") return
    hideElement(loadingGrid)
    fetchError.textContent = `Failed to load countries: ${err.message}. Try refreshing.`
    showElement(fetchError)
  }
}

// ============================================================
// FILTERING & SORTING
// ============================================================
function getFilteredCountries() {
  const query  = searchInput.value.trim().toLowerCase()
  const region = regionFilter.value
  const sort   = sortSelect.value

  let filtered = allCountries.filter(c => {
    const nameMatch    = c.name.common.toLowerCase().includes(query)
    const regionMatch  = !region || c.region === region
    const favMatch     = !showFavsOnly || favourites.has(c.cca3)
    return nameMatch && regionMatch && favMatch
  })

  if (sort === "population-desc") filtered.sort((a, b) => b.population - a.population)
  if (sort === "population-asc")  filtered.sort((a, b) => a.population - b.population)
  if (sort === "name")            filtered.sort((a, b) => a.name.common.localeCompare(b.name.common))

  return filtered
}

// ============================================================
// RENDERING
// ============================================================
function renderList() {
  const countries = getFilteredCountries()

  cardGrid.innerHTML = ""
  noResults.classList.add("hidden")

  if (countries.length === 0) {
    showElement(noResults)
    resultCount.textContent = ""
    return
  }

  const fragment = document.createDocumentFragment()
  for (const country of countries) {
    fragment.appendChild(createCard(country))
  }
  cardGrid.appendChild(fragment)

  const total = allCountries.length
  resultCount.textContent =
    countries.length === total
      ? `Showing all ${total} countries`
      : `Showing ${countries.length} of ${total} countries`

  updateFavBadge()
}

function createCard(country) {
  const clone = cardTemplate.content.cloneNode(true)
  const card  = clone.querySelector(".card")

  card.dataset.code = country.cca3
  card.querySelector(".card-flag").src = country.flags?.svg ?? country.flags?.png ?? ""
  card.querySelector(".card-flag").alt = `Flag of ${country.name.common}`
  card.querySelector(".card-name").textContent = country.name.common
  card.querySelector(".pop").textContent   = formatNumber(country.population)
  card.querySelector(".region").textContent = country.region   ?? "—"
  card.querySelector(".capital").textContent = country.capital?.[0] ?? "—"

  const favBtn = card.querySelector(".btn-fav")
  const isFav  = favourites.has(country.cca3)
  favBtn.setAttribute("aria-pressed", String(isFav))
  favBtn.setAttribute("aria-label", isFav ? "Remove from favourites" : "Add to favourites")

  return clone
}

function renderDetail(code) {
  const country = allCountries.find(c => c.cca3 === code)
  if (!country) return showList()

  const isFav = favourites.has(country.cca3)
  const langs = country.languages ? Object.values(country.languages).join(", ") : "—"
  const curr  = country.currencies
    ? Object.values(country.currencies).map(c => `${c.name} (${c.symbol ?? ""})`).join(", ")
    : "—"

  const bordersHTML = country.borders?.length
    ? country.borders.map(b =>
        `<button class="border-tag" data-code="${b}">${getCountryName(b)}</button>`
      ).join("")
    : "<span>None</span>"

  detailContent.innerHTML = `
    <div class="detail-grid">
      <img class="detail-flag" src="${country.flags?.svg ?? ""}" alt="Flag of ${escapeHTML(country.name.common)}">
      <div>
        <h2 class="detail-name">${escapeHTML(country.name.common)}</h2>
        <div class="detail-section">
          <h3>Official Name</h3><p>${escapeHTML(country.name.official)}</p>
        </div>
        <div class="detail-section">
          <h3>Population</h3><p>${formatNumber(country.population)}</p>
        </div>
        <div class="detail-section">
          <h3>Region / Subregion</h3><p>${escapeHTML(country.region)} / ${escapeHTML(country.subregion ?? "—")}</p>
        </div>
        <div class="detail-section">
          <h3>Capital</h3><p>${escapeHTML(country.capital?.[0] ?? "—")}</p>
        </div>
        <div class="detail-section">
          <h3>Area</h3><p>${country.area ? formatNumber(country.area) + " km²" : "—"}</p>
        </div>
        <div class="detail-section">
          <h3>Languages</h3><p>${escapeHTML(langs)}</p>
        </div>
        <div class="detail-section">
          <h3>Currencies</h3><p>${escapeHTML(curr)}</p>
        </div>
        <div class="detail-section">
          <h3>Bordering Countries</h3>
          <div class="borders">${bordersHTML}</div>
        </div>
        <button class="detail-fav" data-code="${country.cca3}">
          ${isFav ? "★ Saved to Favourites" : "☆ Add to Favourites"}
        </button>
      </div>
    </div>
  `
}

function getCountryName(code) {
  return allCountries.find(c => c.cca3 === code)?.name.common ?? code
}

// ============================================================
// FAVOURITES
// ============================================================
function toggleFav(code) {
  if (favourites.has(code)) {
    favourites.delete(code)
  } else {
    favourites.add(code)
  }
  localStorage.setItem("favourites", JSON.stringify([...favourites]))
  updateFavBadge()

  // Update any card currently showing this country
  const card = cardGrid.querySelector(`[data-code="${code}"]`)
  if (card) {
    const isFav = favourites.has(code)
    const btn = card.querySelector(".btn-fav")
    btn.setAttribute("aria-pressed", String(isFav))
    btn.setAttribute("aria-label", isFav ? "Remove from favourites" : "Add to favourites")
  }

  // Update detail view button if open
  const detailBtn = detailContent.querySelector(`.detail-fav[data-code="${code}"]`)
  if (detailBtn) {
    detailBtn.textContent = favourites.has(code) ? "★ Saved to Favourites" : "☆ Add to Favourites"
  }
}

function updateFavBadge() {
  if (favourites.size > 0) {
    favCount.textContent = `★ ${favourites.size}`
    showElement(favCount)
  } else {
    hideElement(favCount)
  }
}

// ============================================================
// NAVIGATION (hash routing)
// ============================================================
function showDetail(code) {
  location.hash = `#country/${code}`
}

function showList() {
  location.hash = ""
}

function handleRoute() {
  const hash = location.hash

  if (hash.startsWith("#country/")) {
    const code = hash.replace("#country/", "")
    showElement(detailView)
    hideElement(listView)
    renderDetail(code)
  } else {
    hideElement(detailView)
    showElement(listView)
  }
}

window.addEventListener("hashchange", handleRoute)

// ============================================================
// EVENT LISTENERS
// ============================================================

// Search — debounced
searchInput.addEventListener("input", debounce(() => renderList(), 250))

// Region and sort filters
regionFilter.addEventListener("change", () => renderList())
sortSelect.addEventListener("change",   () => renderList())

// Favourites toggle button
favToggle.addEventListener("click", () => {
  showFavsOnly = !showFavsOnly
  favToggle.textContent = showFavsOnly ? "Show All" : "★ Favourites"
  favToggle.classList.toggle("active", showFavsOnly)
  renderList()
})

// Card grid — event delegation
cardGrid.addEventListener("click", e => {
  // Fav button click
  const favBtn = e.target.closest(".btn-fav")
  if (favBtn) {
    e.stopPropagation()   // prevent card click firing
    toggleFav(favBtn.closest(".card").dataset.code)
    return
  }

  // Card click → go to detail
  const card = e.target.closest(".card")
  if (card) showDetail(card.dataset.code)
})

// Detail view — back button and border tags
document.getElementById("btn-back").addEventListener("click", showList)

detailContent.addEventListener("click", e => {
  if (e.target.matches(".border-tag")) {
    showDetail(e.target.dataset.code)
  }
  if (e.target.matches(".detail-fav")) {
    toggleFav(e.target.dataset.code)
  }
})

// ============================================================
// UTILITIES
// ============================================================
function debounce(fn, delay) {
  let timer
  return (...args) => {
    clearTimeout(timer)
    timer = setTimeout(() => fn(...args), delay)
  }
}

function formatNumber(n) {
  return n?.toLocaleString() ?? "—"
}

function escapeHTML(str) {
  return String(str ?? "")
    .replace(/&/g, "&amp;").replace(/</g, "&lt;")
    .replace(/>/g, "&gt;").replace(/"/g, "&quot;")
}

function showElement(el) { el?.classList.remove("hidden") }
function hideElement(el) { el?.classList.add("hidden") }

// ============================================================
// BOOT
// ============================================================
handleRoute()
fetchCountries()
```

---

## What This Project Demonstrates

| Concept | Where Used |
|---------|-----------|
| `fetch` with `AbortController` | `fetchCountries()` |
| Error handling + loading state | Loading skeleton → error message |
| `DocumentFragment` + `<template>` | `createCard()` |
| Event delegation | Card grid clicks (fav + detail nav) |
| Debounce | Search input |
| `localStorage` persistence | Favourites set |
| Custom filter/sort pipeline | `getFilteredCountries()` |
| Hash-based routing | `showDetail()` / `handleRoute()` |
| XSS prevention | `escapeHTML()` used on all API data |
| `closest()` traversal | Finding card from fav button click |
| `Promise`-based `fetch` | API calls |
| `Set` data structure | Efficient favourites lookup |

---

## Exercises and Extensions

After building the base app, try adding:

1. **Keyboard navigation** — Arrow keys move between visible cards; Enter opens detail
2. **Comparison mode** — Select two countries and show a side-by-side stats table
3. **Share button** — Copy the current URL (with hash) to clipboard using `navigator.clipboard.writeText()`
4. **Dark mode** — Toggle CSS custom property values; save preference to localStorage
5. **Offline support** — Cache the API response in localStorage and show stale data if fetch fails

---

## Check Your Understanding

1. How does hash-based routing work without a server? What fires when the hash changes?
2. Why does clicking the fav button call `e.stopPropagation()` before toggling the favourite?
3. Why is `Set` a better data structure than an `Array` for storing favourites?
4. The XSS risk in this app comes from displaying API data. Which function prevents it, and what does it do?
5. Why does the app re-create a new `AbortController` each time `fetchCountries` is called?

---

*Previous: [04 — Fetch and AJAX](04-fetch-and-ajax.md)*
*Next: [06 — Quiz and Checkpoints](06-quiz-and-checkpoints.md)*
