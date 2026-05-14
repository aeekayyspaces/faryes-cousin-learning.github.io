# Solutions — Code Challenges

Reference solutions for the code challenges in [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md).

---

## Challenge 1: FizzBuzz

```javascript
function fizzBuzz(n) {
  for (let i = 1; i <= n; i++) {
    if (i % 3 === 0 && i % 5 === 0) {
      console.log("FizzBuzz")
    } else if (i % 3 === 0) {
      console.log("Fizz")
    } else if (i % 5 === 0) {
      console.log("Buzz")
    } else {
      console.log(i)
    }
  }
}

fizzBuzz(15)
```

**Alternative — ternary chain:**

```javascript
function fizzBuzz(n) {
  for (let i = 1; i <= n; i++) {
    const output =
      i % 15 === 0 ? "FizzBuzz" :
      i % 3  === 0 ? "Fizz"     :
      i % 5  === 0 ? "Buzz"     :
      i
    console.log(output)
  }
}
```

**Alternative — string concatenation (avoids repeating the modulo check):**

```javascript
function fizzBuzz(n) {
  for (let i = 1; i <= n; i++) {
    let output = ""
    if (i % 3 === 0) output += "Fizz"
    if (i % 5 === 0) output += "Buzz"
    console.log(output || i)
  }
}
```

This third approach scales elegantly — adding a new divisor (e.g., 7 → "Jazz") requires just one more `if` line.

---

## Challenge 2: Data Transformation Pipeline

```javascript
const users = [
  { id: 1, name: "Alice",   age: 28, role: "admin",     active: true  },
  { id: 2, name: "Bob",     age: 17, role: "user",      active: true  },
  { id: 3, name: "Carol",   age: 34, role: "moderator", active: false },
  { id: 4, name: "David",   age: 22, role: "user",      active: true  },
  { id: 5, name: "Eve",     age: 31, role: "admin",     active: true  },
]

// 1. Active users aged 18+
const eligibleUsers = users.filter(u => u.active && u.age >= 18)
console.log(eligibleUsers.map(u => u.name))
// ["Alice", "David", "Eve"]

// 2. Map to { name, role }
const simplified = eligibleUsers.map(({ name, role }) => ({ name, role }))
console.log(simplified)
// [
//   { name: "Alice", role: "admin" },
//   { name: "David", role: "user" },
//   { name: "Eve",   role: "admin" }
// ]

// 3. Count by role
const roleCounts = eligibleUsers.reduce((tally, user) => {
  tally[user.role] = (tally[user.role] ?? 0) + 1
  return tally
}, {})
console.log(roleCounts)
// { admin: 2, user: 1 }

// 4. Oldest active user
const oldest = users
  .filter(u => u.active)
  .reduce((oldest, user) => user.age > oldest.age ? user : oldest)
console.log(oldest.name, oldest.age)  // "Eve", 31
```

**Key insights:**
- `filter` returns a new array — chain on it with `.map()` or `.reduce()`
- Object destructuring in `.map(({ name, role }) => ...)` is clean shorthand
- `reduce` without an initial value starts with the first element as the accumulator — valid for finding min/max

---

## Challenge 3: Function Factory

```javascript
function makeValidator(rules) {
  return function validate(data) {
    const errors = {}

    for (const [field, rule] of Object.entries(rules)) {
      const error = rule(data[field])
      if (error !== null) {
        errors[field] = error
      }
    }

    return errors
  }
}

// Usage:
const validateUser = makeValidator({
  name:  value => value && value.length >= 2 ? null : "Name must be at least 2 characters",
  email: value => value && value.includes("@") ? null : "Must be a valid email",
  age:   value => value >= 18 ? null : "Must be 18 or older"
})

console.log(validateUser({ name: "Alice", email: "a@b.com", age: 25 }))
// {} — no errors

console.log(validateUser({ name: "Al", email: "a@b.com", age: 16 }))
// { age: "Must be 18 or older" }

console.log(validateUser({ name: "X", email: "notvalid", age: 16 }))
// { name: "Name must be at least 2 characters", email: "Must be a valid email", age: "Must be 18 or older" }
```

**Why this demonstrates closures:** `makeValidator` returns a function that closes over `rules`. Each time `validate` is called, it still has access to the `rules` object from its creation context — even though `makeValidator` has long since returned.

**Extension — check if valid:**
```javascript
function isValid(errors) {
  return Object.keys(errors).length === 0
}

const errors = validateUser({ name: "Alice", email: "a@b.com", age: 25 })
if (isValid(errors)) {
  console.log("Form is valid!")
} else {
  console.log("Errors:", errors)
}
```

---

## Challenge 4: Interactive Todo App (Upgrade)

### `todo.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Todo App</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: 'Segoe UI', Arial, sans-serif;
      background: #f4f4f8;
      display: flex;
      justify-content: center;
      padding: 3rem 1rem;
    }

    .app {
      background: white;
      border-radius: 12px;
      box-shadow: 0 4px 16px rgba(0,0,0,0.1);
      padding: 2rem;
      width: 100%;
      max-width: 480px;
    }

    h1 { font-size: 1.5rem; margin-bottom: 1.25rem; color: #1a1a2e; }

    .add-row {
      display: flex;
      gap: 0.5rem;
      margin-bottom: 1rem;
    }

    .add-row input {
      flex: 1;
      padding: 0.6rem 0.75rem;
      border: 1.5px solid #d1d5db;
      border-radius: 6px;
      font-size: 0.95rem;
    }

    .add-row input:focus { outline: none; border-color: #7c3aed; }

    .priority-select {
      padding: 0.6rem;
      border: 1.5px solid #d1d5db;
      border-radius: 6px;
      font-size: 0.9rem;
    }

    .add-btn {
      padding: 0.6rem 1rem;
      background: #7c3aed;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-size: 0.95rem;
    }

    .add-btn:hover { background: #6d28d9; }

    .filters {
      display: flex;
      gap: 0.5rem;
      margin-bottom: 1rem;
    }

    .filter-btn {
      padding: 0.4rem 0.75rem;
      background: #f3f4f6;
      border: 1px solid #e5e7eb;
      border-radius: 20px;
      cursor: pointer;
      font-size: 0.85rem;
    }

    .filter-btn.active {
      background: #7c3aed;
      color: white;
      border-color: #7c3aed;
    }

    .task-count {
      font-size: 0.85rem;
      color: #6b7280;
      margin-bottom: 0.75rem;
    }

    #task-list { list-style: none; }

    #task-list li {
      display: flex;
      align-items: center;
      gap: 0.75rem;
      padding: 0.75rem 0.5rem;
      border-bottom: 1px solid #f3f4f6;
    }

    #task-list li.hidden { display: none; }

    .priority-dot {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      flex-shrink: 0;
    }

    .priority-low    .priority-dot { background: #10b981; }
    .priority-medium .priority-dot { background: #f59e0b; }
    .priority-high   .priority-dot { background: #ef4444; }

    .task-text { flex: 1; font-size: 0.95rem; }
    .task-text.done { text-decoration: line-through; color: #9ca3af; }

    .done-btn, .delete-btn {
      padding: 0.25rem 0.6rem;
      border-radius: 4px;
      cursor: pointer;
      font-size: 0.8rem;
      border: 1px solid;
    }

    .done-btn   { background: #ecfdf5; color: #065f46; border-color: #a7f3d0; }
    .delete-btn { background: #fef2f2; color: #991b1b; border-color: #fecaca; }

    .empty-msg { color: #9ca3af; text-align: center; padding: 2rem 0; font-size: 0.9rem; }
  </style>
</head>
<body>
  <div class="app">
    <h1>My Tasks</h1>

    <div class="add-row">
      <input type="text" id="task-input" placeholder="Add a task...">
      <select id="priority-select" class="priority-select">
        <option value="low">Low</option>
        <option value="medium" selected>Medium</option>
        <option value="high">High</option>
      </select>
      <button id="add-btn" class="add-btn">Add</button>
    </div>

    <div class="filters">
      <button class="filter-btn active" data-filter="all">All</button>
      <button class="filter-btn" data-filter="active">Active</button>
      <button class="filter-btn" data-filter="completed">Completed</button>
    </div>

    <p class="task-count" id="task-count"></p>

    <ul id="task-list">
      <li class="empty-msg" id="empty-msg">No tasks yet. Add one above!</li>
    </ul>
  </div>

  <script src="todo.js" defer></script>
</body>
</html>
```

### `todo.js`

```javascript
// --- State ---
let tasks = JSON.parse(localStorage.getItem("tasks") ?? "[]")
let currentFilter = "all"

// --- DOM ---
const taskInput     = document.getElementById("task-input")
const prioritySelect = document.getElementById("priority-select")
const addBtn        = document.getElementById("add-btn")
const taskList      = document.getElementById("task-list")
const taskCountEl   = document.getElementById("task-count")
const emptyMsg      = document.getElementById("empty-msg")

// --- Persistence ---
function saveTasks() {
  localStorage.setItem("tasks", JSON.stringify(tasks))
}

// --- Rendering ---
function render() {
  // Remove all task items (not empty-msg)
  taskList.querySelectorAll("li:not(#empty-msg)").forEach(li => li.remove())

  const activeTasks = tasks.filter(t => !t.done)
  taskCountEl.textContent = `${activeTasks.length} task${activeTasks.length !== 1 ? "s" : ""} remaining`

  const filtered = tasks.filter(task => {
    if (currentFilter === "active")    return !task.done
    if (currentFilter === "completed") return task.done
    return true
  })

  emptyMsg.style.display = filtered.length === 0 ? "block" : "none"

  filtered.forEach(task => {
    const li = document.createElement("li")
    li.className = `priority-${task.priority}`
    li.dataset.id = task.id

    li.innerHTML = `
      <span class="priority-dot"></span>
      <span class="task-text ${task.done ? "done" : ""}">${escapeHTML(task.text)}</span>
      <button class="done-btn">${task.done ? "Undo" : "Done"}</button>
      <button class="delete-btn">Delete</button>
    `

    taskList.appendChild(li)
  })
}

// XSS prevention — escape user input before inserting as HTML
function escapeHTML(str) {
  return str
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;")
}

// --- Actions ---
function addTask() {
  const text = taskInput.value.trim()
  if (!text) return

  tasks.push({
    id: Date.now(),
    text,
    priority: prioritySelect.value,
    done: false
  })

  saveTasks()
  render()
  taskInput.value = ""
  taskInput.focus()
}

function toggleDone(id) {
  const task = tasks.find(t => t.id === id)
  if (task) task.done = !task.done
  saveTasks()
  render()
}

function deleteTask(id) {
  tasks = tasks.filter(t => t.id !== id)
  saveTasks()
  render()
}

// --- Event Listeners ---
addBtn.addEventListener("click", addTask)

taskInput.addEventListener("keydown", e => {
  if (e.key === "Enter") addTask()
})

// Event delegation for done/delete buttons
taskList.addEventListener("click", e => {
  const li = e.target.closest("li[data-id]")
  if (!li) return
  const id = Number(li.dataset.id)

  if (e.target.matches(".done-btn"))   toggleDone(id)
  if (e.target.matches(".delete-btn")) deleteTask(id)
})

// Filter buttons
document.querySelector(".filters").addEventListener("click", e => {
  if (!e.target.matches(".filter-btn")) return

  document.querySelectorAll(".filter-btn").forEach(btn => btn.classList.remove("active"))
  e.target.classList.add("active")
  currentFilter = e.target.dataset.filter
  render()
})

// --- Initial Render ---
render()
```

---

## Challenge 5: Weather App Enhancements

### Key additions to `weather.js`

**1. Five-day forecast:**

```javascript
// Updated API URL — add daily data:
const url = [
  "https://api.open-meteo.com/v1/forecast",
  `?latitude=${lat}&longitude=${lon}`,
  "&current_weather=true",
  "&hourly=relativehumidity_2m,apparent_temperature",
  "&daily=temperature_2m_max,temperature_2m_min,weathercode",
  "&forecast_days=5",
  "&timezone=auto"
].join("")

// Render forecast:
function renderForecast(daily) {
  const forecastEl = document.getElementById("forecast")
  forecastEl.innerHTML = ""

  daily.time.forEach((date, i) => {
    const day = new Date(date).toLocaleDateString("en-US", { weekday: "short" })
    const max = daily.temperature_2m_max[i]
    const min = daily.temperature_2m_min[i]
    const desc = weatherDescriptions[daily.weathercode[i]] ?? "—"

    const card = document.createElement("div")
    card.className = "forecast-day"
    card.innerHTML = `
      <strong>${i === 0 ? "Today" : day}</strong>
      <span class="desc">${desc}</span>
      <span>${max}° / ${min}°</span>
    `
    forecastEl.appendChild(card)
  })
}
```

**2. Unit toggle (C/F):**

```javascript
let unit = localStorage.getItem("weatherUnit") ?? "C"

function toFahrenheit(celsius) {
  return ((celsius * 9/5) + 32).toFixed(1)
}

function formatTemp(celsius) {
  return unit === "C"
    ? `${celsius}°C`
    : `${toFahrenheit(celsius)}°F`
}

document.getElementById("unit-toggle").addEventListener("click", () => {
  unit = unit === "C" ? "F" : "C"
  localStorage.setItem("weatherUnit", unit)
  // Re-render with stored weather data (cache lastWeather in a variable)
  if (lastWeather) showWeather(lastCityName, lastWeather)
})
```

**3. Loading skeleton HTML:**

```html
<div id="skeleton" class="skeleton hidden">
  <div class="skeleton-title"></div>
  <div class="skeleton-subtitle"></div>
  <div class="skeleton-stats">
    <div class="skeleton-stat"></div>
    <div class="skeleton-stat"></div>
    <div class="skeleton-stat"></div>
  </div>
</div>
```

```css
.skeleton-title, .skeleton-subtitle, .skeleton-stat {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
  border-radius: 4px;
}

@keyframes shimmer {
  0%   { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

---

*Back to challenges: [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md)*
