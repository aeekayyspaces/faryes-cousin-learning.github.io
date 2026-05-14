# Solutions — Code Challenges

Reference solutions for challenges in [06 — Quiz and Checkpoints](../06-quiz-and-checkpoints.md).

---

## Challenge 1: Delegated Interactive List

### `list.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Task Manager</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: 'Segoe UI', Arial, sans-serif; background: #f4f4f8; padding: 2rem; }
    .app { max-width: 520px; margin: 0 auto; background: white; border-radius: 12px; padding: 1.5rem; box-shadow: 0 4px 16px rgba(0,0,0,0.1); }
    h1 { margin-bottom: 1rem; font-size: 1.4rem; }
    .add-row { display: flex; gap: 0.5rem; margin-bottom: 1rem; }
    .add-row input { flex: 1; padding: 0.6rem 0.75rem; border: 1.5px solid #d1d5db; border-radius: 6px; font-size: 0.95rem; }
    .add-row select { padding: 0.6rem; border: 1.5px solid #d1d5db; border-radius: 6px; font-size: 0.9rem; }
    .add-row button { padding: 0.6rem 1rem; background: #7c3aed; color: white; border: none; border-radius: 6px; cursor: pointer; }
    .count { font-size: 0.85rem; color: #6b7280; margin-bottom: 0.75rem; }
    ul { list-style: none; }
    li { display: flex; align-items: center; gap: 0.75rem; padding: 0.75rem 0; border-bottom: 1px solid #f3f4f6; }
    li.done .task-text { text-decoration: line-through; color: #9ca3af; }
    .dot { width: 10px; height: 10px; border-radius: 50%; flex-shrink: 0; }
    li[data-priority="low"]    .dot { background: #10b981; }
    li[data-priority="medium"] .dot { background: #f59e0b; }
    li[data-priority="high"]   .dot { background: #ef4444; }
    .task-text { flex: 1; font-size: 0.95rem; }
    .btn-done, .btn-del { padding: 0.25rem 0.6rem; font-size: 0.8rem; border-radius: 4px; cursor: pointer; border: 1px solid; }
    .btn-done { background: #ecfdf5; color: #065f46; border-color: #a7f3d0; }
    .btn-del  { background: #fef2f2; color: #991b1b; border-color: #fecaca; }
  </style>
</head>
<body>
<div class="app">
  <h1>Task Manager</h1>
  <div class="add-row">
    <input type="text" id="task-input" placeholder="New task...">
    <select id="priority">
      <option value="low">Low</option>
      <option value="medium" selected>Medium</option>
      <option value="high">High</option>
    </select>
    <button id="add-btn">Add</button>
  </div>
  <p class="count" id="count"></p>
  <ul id="task-list"></ul>
</div>

<template id="task-template">
  <li>
    <span class="dot"></span>
    <span class="task-text"></span>
    <button class="btn-done">Done</button>
    <button class="btn-del">Delete</button>
  </li>
</template>

<script>
  const taskInput    = document.getElementById("task-input")
  const priorityEl   = document.getElementById("priority")
  const addBtn       = document.getElementById("add-btn")
  const taskList     = document.getElementById("task-list")
  const countEl      = document.getElementById("count")
  const taskTemplate = document.getElementById("task-template")

  function updateCount() {
    const total  = taskList.querySelectorAll("li").length
    const done   = taskList.querySelectorAll("li.done").length
    countEl.textContent = `${total - done} of ${total} tasks remaining`
  }

  function addTask() {
    const text = taskInput.value.trim()
    if (!text) return

    const clone = taskTemplate.content.cloneNode(true)
    const li = clone.querySelector("li")
    li.dataset.priority = priorityEl.value
    li.querySelector(".task-text").textContent = text
    taskList.appendChild(li)

    taskInput.value = ""
    taskInput.focus()
    updateCount()
  }

  addBtn.addEventListener("click", addTask)
  taskInput.addEventListener("keydown", e => { if (e.key === "Enter") addTask() })

  // ONE delegated listener for all buttons — including future ones
  taskList.addEventListener("click", e => {
    const li = e.target.closest("li")
    if (!li) return

    if (e.target.matches(".btn-done")) {
      li.classList.toggle("done")
      e.target.textContent = li.classList.contains("done") ? "Undo" : "Done"
    }

    if (e.target.matches(".btn-del")) {
      li.remove()
    }

    updateCount()
  })
</script>
</body>
</html>
```

**Key technique:** A single `taskList.addEventListener("click", ...)` handles Done and Delete for all tasks — current and future. No re-attaching needed when tasks are added.

---

## Challenge 2: Real-Time Search with Abort

### `search.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Country Search</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: 'Segoe UI', Arial, sans-serif; background: #f4f4f8; padding: 2rem; }
    .container { max-width: 560px; margin: 0 auto; }
    h1 { margin-bottom: 1rem; }
    input { width: 100%; padding: 0.75rem 1rem; font-size: 1rem; border: 1.5px solid #d1d5db; border-radius: 8px; margin-bottom: 1rem; }
    input:focus { outline: none; border-color: #7c3aed; }
    .status { color: #6b7280; font-size: 0.9rem; margin-bottom: 0.75rem; min-height: 1.4em; }
    .error  { color: #dc2626; }
    ul { list-style: none; }
    li { display: flex; align-items: center; gap: 0.75rem; padding: 0.6rem 0; border-bottom: 1px solid #e5e7eb; }
    img { width: 40px; height: 26px; object-fit: cover; border-radius: 3px; border: 1px solid #e5e7eb; }
    .name { flex: 1; font-weight: 600; font-size: 0.95rem; }
    .pop  { font-size: 0.8rem; color: #6b7280; }
  </style>
</head>
<body>
<div class="container">
  <h1>Country Search</h1>
  <input type="search" id="search" placeholder="Type a country name...">
  <p id="status" class="status"></p>
  <ul id="results"></ul>
</div>
<script>
  const searchEl  = document.getElementById("search")
  const statusEl  = document.getElementById("status")
  const resultsEl = document.getElementById("results")
  let controller  = new AbortController()

  function debounce(fn, ms) {
    let t
    return (...args) => { clearTimeout(t); t = setTimeout(() => fn(...args), ms) }
  }

  async function search(query) {
    query = query.trim()
    resultsEl.innerHTML = ""
    if (!query) { statusEl.textContent = ""; return }

    // Cancel any pending request
    controller.abort()
    controller = new AbortController()

    statusEl.textContent = "Searching..."
    statusEl.className = "status"

    try {
      const res = await fetch(
        `https://restcountries.com/v3.1/name/${encodeURIComponent(query)}?fields=name,flags,population`,
        { signal: controller.signal }
      )

      if (res.status === 404) {
        statusEl.textContent = "No countries found."
        return
      }

      if (!res.ok) throw new Error(`HTTP ${res.status}`)

      const countries = await res.json()
      statusEl.textContent = `Found ${countries.length} result${countries.length !== 1 ? "s" : ""}`

      const fragment = document.createDocumentFragment()
      for (const c of countries.slice(0, 20)) {  // cap at 20
        const li = document.createElement("li")
        li.innerHTML = `
          <img src="${c.flags?.svg ?? c.flags?.png}" alt="Flag of ${c.name.common}">
          <span class="name">${c.name.common}</span>
          <span class="pop">${c.population.toLocaleString()}</span>
        `
        fragment.appendChild(li)
      }
      resultsEl.appendChild(fragment)

    } catch (err) {
      if (err.name === "AbortError") return   // expected — new search cancelled this one
      statusEl.textContent = `Error: ${err.message}`
      statusEl.className = "status error"
    }
  }

  searchEl.addEventListener("input", debounce(e => search(e.target.value), 300))
</script>
</body>
</html>
```

---

## Challenge 3: Multi-Step Form

### `multistep.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Registration</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: 'Segoe UI', Arial, sans-serif; background: #f4f4f8; display: flex; justify-content: center; padding: 3rem 1rem; }
    .wizard { background: white; border-radius: 12px; padding: 2rem; width: 100%; max-width: 480px; box-shadow: 0 4px 16px rgba(0,0,0,0.1); }
    .progress-bar-wrap { background: #e5e7eb; border-radius: 4px; height: 6px; margin-bottom: 1.5rem; }
    .progress-bar { height: 100%; background: #7c3aed; border-radius: 4px; transition: width 0.3s ease; }
    .step-label { font-size: 0.8rem; color: #6b7280; margin-bottom: 1rem; }
    h2 { margin-bottom: 1rem; font-size: 1.2rem; }
    .field { display: flex; flex-direction: column; gap: 0.2rem; margin-bottom: 0.9rem; }
    label { font-size: 0.85rem; font-weight: 600; color: #374151; }
    input { padding: 0.6rem 0.75rem; border: 1.5px solid #d1d5db; border-radius: 6px; font-size: 1rem; }
    input:focus { outline: none; border-color: #7c3aed; }
    input.invalid { border-color: #dc2626; }
    .err { font-size: 0.8rem; color: #dc2626; min-height: 1em; }
    .step-content.hidden { display: none; }
    .review p { margin-bottom: 0.5rem; font-size: 0.95rem; }
    .review strong { color: #374151; }
    .nav { display: flex; justify-content: space-between; margin-top: 1.5rem; }
    button { padding: 0.65rem 1.25rem; border-radius: 6px; font-size: 0.95rem; cursor: pointer; border: none; }
    .btn-primary { background: #7c3aed; color: white; }
    .btn-secondary { background: #f3f4f6; color: #374151; border: 1px solid #d1d5db; }
    .btn-primary:disabled { background: #a78bfa; cursor: not-allowed; }
    .success { text-align: center; padding: 2rem 0; }
    .success h2 { color: #065f46; }
  </style>
</head>
<body>
<div class="wizard">
  <div class="progress-bar-wrap">
    <div class="progress-bar" id="progress-bar" style="width: 33%"></div>
  </div>
  <p class="step-label" id="step-label">Step 1 of 3</p>

  <!-- Step 1 -->
  <div class="step-content" data-step="1">
    <h2>Account Details</h2>
    <div class="field"><label for="f-name">Full Name</label><input id="f-name" type="text" name="name" required minlength="2"><span class="err" id="e-name"></span></div>
    <div class="field"><label for="f-email">Email</label><input id="f-email" type="email" name="email" required><span class="err" id="e-email"></span></div>
  </div>

  <!-- Step 2 -->
  <div class="step-content hidden" data-step="2">
    <h2>Choose a Username</h2>
    <div class="field"><label for="f-username">Username (3–20 chars, alphanumeric)</label><input id="f-username" type="text" name="username" required minlength="3" maxlength="20" pattern="[a-zA-Z0-9]+"><span class="err" id="e-username"></span></div>
    <div class="field"><label for="f-pw">Password (min 8 chars)</label><input id="f-pw" type="password" name="password" required minlength="8"><span class="err" id="e-pw"></span></div>
    <div class="field"><label for="f-cpw">Confirm Password</label><input id="f-cpw" type="password" name="confirm" required><span class="err" id="e-cpw"></span></div>
  </div>

  <!-- Step 3: Review -->
  <div class="step-content hidden review" data-step="3">
    <h2>Review Your Details</h2>
    <div id="review-content"></div>
  </div>

  <!-- Success -->
  <div class="step-content hidden success" data-step="4">
    <h2>Account Created! 🎉</h2>
    <p>Welcome aboard.</p>
  </div>

  <div class="nav">
    <button class="btn-secondary hidden" id="btn-back">Back</button>
    <button class="btn-primary" id="btn-next">Next →</button>
  </div>
</div>
<script>
  let step = 1
  const MAX_STEP = 3
  const formData = {}

  const backBtn = document.getElementById("btn-back")
  const nextBtn = document.getElementById("btn-next")

  const validators = {
    name:     (v) => v.trim().length >= 2 ? null : "Name must be at least 2 characters",
    email:    (v) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v) ? null : "Enter a valid email",
    username: (v) => /^[a-zA-Z0-9]{3,20}$/.test(v) ? null : "3–20 alphanumeric characters only",
    password: (v) => v.length >= 8 ? null : "Password must be at least 8 characters",
    confirm:  (v) => v === document.getElementById("f-pw").value ? null : "Passwords do not match",
  }

  const errorEls = { name: "e-name", email: "e-email", username: "e-username", password: "e-pw", confirm: "e-cpw" }
  const inputEls = { name: "f-name", email: "f-email", username: "f-username", password: "f-pw", confirm: "f-cpw" }

  function validateField(name) {
    const input = document.getElementById(inputEls[name])
    const errEl = document.getElementById(errorEls[name])
    const err   = validators[name]?.(input.value)
    if (errEl) errEl.textContent = err ?? ""
    input.classList.toggle("invalid", !!err)
    return !err
  }

  function getStepFields(s) {
    if (s === 1) return ["name", "email"]
    if (s === 2) return ["username", "password", "confirm"]
    return []
  }

  function updateUI() {
    document.querySelectorAll(".step-content").forEach(el => el.classList.add("hidden"))
    document.querySelector(`[data-step="${step}"]`).classList.remove("hidden")

    backBtn.classList.toggle("hidden", step <= 1)
    nextBtn.textContent = step === MAX_STEP ? "Create Account" : "Next →"
    nextBtn.classList.toggle("hidden", step > MAX_STEP)

    document.getElementById("step-label").textContent = `Step ${Math.min(step, 3)} of 3`
    document.getElementById("progress-bar").style.width = `${(Math.min(step, 3) / 3) * 100}%`
  }

  function showReview() {
    const html = Object.entries(formData)
      .filter(([k]) => k !== "confirm")
      .map(([k, v]) => `<p><strong>${k.charAt(0).toUpperCase() + k.slice(1)}:</strong> ${k === "password" ? "••••••••" : v}</p>`)
      .join("")
    document.getElementById("review-content").innerHTML = html
  }

  nextBtn.addEventListener("click", async () => {
    const fields = getStepFields(step)
    const allValid = fields.every(f => validateField(f))
    if (!allValid) { document.querySelector(".invalid")?.focus(); return }

    fields.forEach(f => {
      const input = document.getElementById(inputEls[f])
      if (input) formData[f] = input.value
    })

    if (step === MAX_STEP) {
      nextBtn.disabled = true
      nextBtn.textContent = "Creating account..."
      await new Promise(r => setTimeout(r, 1500))   // simulate API call
      step = 4
      updateUI()
      return
    }

    step++
    if (step === 3) showReview()
    updateUI()
  })

  backBtn.addEventListener("click", () => {
    step--
    updateUI()
  })
</script>
</body>
</html>
```

---

## Challenge 4: Drag-and-Drop File Upload

### `upload.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>File Upload</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: 'Segoe UI', Arial, sans-serif; background: #f4f4f8; padding: 2rem; }
    .container { max-width: 560px; margin: 0 auto; }
    .drop-zone {
      border: 2px dashed #d1d5db;
      border-radius: 12px;
      padding: 3rem 2rem;
      text-align: center;
      cursor: pointer;
      transition: border-color 0.2s, background 0.2s;
    }
    .drop-zone.dragging { border-color: #7c3aed; background: #f5f3ff; }
    .drop-zone label { color: #7c3aed; cursor: pointer; text-decoration: underline; }
    .drop-zone input { display: none; }
    .preview-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 0.75rem; margin-top: 1.5rem; }
    .preview-item { background: white; border-radius: 8px; overflow: hidden; box-shadow: 0 2px 6px rgba(0,0,0,0.08); position: relative; }
    .preview-item img { width: 100%; height: 100px; object-fit: cover; display: block; }
    .preview-info { padding: 0.4rem 0.5rem; font-size: 0.75rem; color: #6b7280; }
    .btn-remove { position: absolute; top: 4px; right: 4px; background: rgba(0,0,0,0.55); color: white; border: none; border-radius: 50%; width: 22px; height: 22px; cursor: pointer; font-size: 0.75rem; line-height: 22px; text-align: center; }
    .error { color: #dc2626; font-size: 0.85rem; margin-top: 0.25rem; }
    .btn-upload { margin-top: 1rem; width: 100%; padding: 0.75rem; background: #7c3aed; color: white; border: none; border-radius: 8px; font-size: 1rem; cursor: pointer; }
    .btn-upload:disabled { background: #a78bfa; cursor: not-allowed; }
    .log { margin-top: 1rem; background: #1e1e2e; color: #cdd6f4; padding: 1rem; border-radius: 8px; font-family: monospace; font-size: 0.8rem; max-height: 150px; overflow-y: auto; }
  </style>
</head>
<body>
<div class="container">
  <h1 style="margin-bottom: 1rem;">Image Upload</h1>

  <div class="drop-zone" id="drop-zone">
    <p>Drag images here, or <label for="file-input">browse files</label></p>
    <p style="font-size:0.8rem;color:#9ca3af;margin-top:0.5rem">Max 5 MB per image</p>
    <input type="file" id="file-input" accept="image/*" multiple>
  </div>

  <div class="preview-grid" id="preview-grid"></div>
  <button class="btn-upload hidden" id="btn-upload">Upload All</button>
  <div class="log hidden" id="log"></div>
</div>
<script>
  const dropZone  = document.getElementById("drop-zone")
  const fileInput = document.getElementById("file-input")
  const previewGrid = document.getElementById("preview-grid")
  const btnUpload = document.getElementById("btn-upload")
  const logEl     = document.getElementById("log")

  const MAX_SIZE = 5 * 1024 * 1024   // 5 MB
  let selectedFiles = []

  function handleFiles(files) {
    for (const file of files) {
      if (!file.type.startsWith("image/")) {
        alert(`"${file.name}" is not an image.`); continue
      }
      if (file.size > MAX_SIZE) {
        alert(`"${file.name}" exceeds 5 MB.`); continue
      }
      selectedFiles.push(file)
      addPreview(file)
    }
    btnUpload.classList.toggle("hidden", selectedFiles.length === 0)
  }

  function addPreview(file) {
    const reader = new FileReader()
    reader.onload = e => {
      const div = document.createElement("div")
      div.className = "preview-item"
      div.dataset.name = file.name
      div.innerHTML = `
        <img src="${e.target.result}" alt="${file.name}">
        <div class="preview-info">${file.name}<br>${(file.size/1024).toFixed(1)} KB</div>
        <button class="btn-remove" data-name="${file.name}">✕</button>
      `
      previewGrid.appendChild(div)
    }
    reader.readAsDataURL(file)
  }

  previewGrid.addEventListener("click", e => {
    if (!e.target.matches(".btn-remove")) return
    const name = e.target.dataset.name
    selectedFiles = selectedFiles.filter(f => f.name !== name)
    e.target.closest(".preview-item").remove()
    btnUpload.classList.toggle("hidden", selectedFiles.length === 0)
  })

  dropZone.addEventListener("dragover", e => { e.preventDefault(); dropZone.classList.add("dragging") })
  dropZone.addEventListener("dragleave", () => dropZone.classList.remove("dragging"))
  dropZone.addEventListener("drop", e => {
    e.preventDefault()
    dropZone.classList.remove("dragging")
    handleFiles(Array.from(e.dataTransfer.files))
  })
  fileInput.addEventListener("change", e => handleFiles(Array.from(e.target.files)))

  btnUpload.addEventListener("click", async () => {
    btnUpload.disabled = true
    btnUpload.textContent = "Uploading..."
    logEl.classList.remove("hidden")
    logEl.textContent = ""

    const fd = new FormData()
    for (const file of selectedFiles) fd.append("files", file, file.name)

    try {
      logEl.textContent += "Sending to httpbin.org/post...\n"
      const res = await fetch("https://httpbin.org/post", { method: "POST", body: fd })
      const data = await res.json()
      logEl.textContent += `Status: ${res.status}\n`
      logEl.textContent += `Files received: ${Object.keys(data.files ?? {}).join(", ") || "(httpbin doesn't store files, but the request succeeded)"}\n`
      logEl.textContent += "Upload complete!"
    } catch (err) {
      logEl.textContent += `Error: ${err.message}\n`
    } finally {
      btnUpload.disabled = false
      btnUpload.textContent = "Upload All"
    }
  })
</script>
</body>
</html>
```

---

## Challenge 5 Notes: Country Explorer Extensions

### URL-persisted search

```javascript
// On filter change — update URL:
function syncURL() {
  const params = new URLSearchParams()
  if (searchInput.value) params.set("search", searchInput.value)
  if (regionFilter.value) params.set("region", regionFilter.value)
  history.pushState({}, "", params.toString() ? `?${params}` : location.pathname)
}

// On load — read URL:
const params = new URLSearchParams(location.search)
if (params.get("search")) searchInput.value = params.get("search")
if (params.get("region")) regionFilter.value = params.get("region")

// Handle browser back/forward:
window.addEventListener("popstate", () => {
  const p = new URLSearchParams(location.search)
  searchInput.value = p.get("search") ?? ""
  regionFilter.value = p.get("region") ?? ""
  renderList()
})
```

### Clipboard share button

```javascript
document.querySelector("#btn-share")?.addEventListener("click", async () => {
  try {
    await navigator.clipboard.writeText(location.href)
    const btn = document.querySelector("#btn-share")
    btn.textContent = "Copied!"
    setTimeout(() => { btn.textContent = "Share" }, 2000)
  } catch (err) {
    // Clipboard API requires user interaction and HTTPS
    alert("Copy this URL: " + location.href)
  }
})
```

---

*Back to challenges: [06 — Quiz and Checkpoints](../06-quiz-and-checkpoints.md)*
