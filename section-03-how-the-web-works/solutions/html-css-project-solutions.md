# Solutions — HTML/CSS Project and Code Challenges

Reference solutions for the hands-on work in [05 — Introduction to HTML](../05-introduction-to-html.md), [06 — Introduction to CSS](../06-introduction-to-css.md), [07 — Introduction to JavaScript](../07-introduction-to-javascript.md), and the code challenges in [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md).

---

## About Me Page — Complete Solution

This is the completed `about-me.html` and `styles.css` from the Section 5–7 hands-on exercises, incorporating HTML structure, CSS styling, and JavaScript interactivity.

### `about-me.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>About Me — Alex Rivera</title>
    <link rel="stylesheet" href="styles.css">
  </head>
  <body>

    <header>
      <div class="header-inner">
        <span class="logo">Alex Rivera</span>
        <nav>
          <a href="#about">About</a>
          <a href="#skills">Skills</a>
          <a href="#contact">Contact</a>
        </nav>
      </div>
    </header>

    <main>

      <section id="about">
        <h1>Hi, I'm Alex Rivera</h1>
        <p>
          I'm learning to build web applications — starting with the
          foundations of HTML, CSS, and JavaScript, working up to
          TypeScript and React.
        </p>
        <img src="https://via.placeholder.com/160x160/7c3aed/ffffff?text=AR"
             alt="Alex Rivera"
             width="160" height="160">
      </section>

      <section id="skills">
        <h2>What I'm Learning</h2>
        <ul>
          <li><strong>HTML</strong> — Page structure and semantic markup</li>
          <li><strong>CSS</strong> — Styling, layout, and responsive design</li>
          <li><strong>JavaScript</strong> — Interactivity and DOM manipulation</li>
          <li><strong>TypeScript</strong> — Typed JavaScript for larger projects</li>
          <li><strong>React</strong> — Component-based UI framework</li>
        </ul>
      </section>

      <section id="contact">
        <h2>Get in Touch</h2>
        <form id="contact-form">
          <label for="your-name">Name:</label>
          <input type="text" id="your-name" name="name"
                 placeholder="Your name" required>

          <label for="your-email">Email:</label>
          <input type="email" id="your-email" name="email"
                 placeholder="you@example.com" required>

          <label for="your-message">Message:</label>
          <textarea id="your-message" name="message" rows="4"></textarea>

          <button type="submit">Send Message</button>
        </form>
        <div id="form-response" class="form-response hidden">
          Thanks for your message! I'll get back to you soon.
        </div>
      </section>

    </main>

    <footer>
      <p>&copy; 2026 Alex Rivera. Built with HTML, CSS, and JavaScript.</p>
    </footer>

    <script src="script.js" defer></script>
  </body>
</html>
```

### `styles.css`

```css
/* ===========================
   Base Reset and Variables
   =========================== */
:root {
  --primary: #7c3aed;
  --primary-dark: #6d28d9;
  --primary-light: #a78bfa;
  --bg: #f4f4f8;
  --surface: #ffffff;
  --text: #1a1a2e;
  --text-muted: #6b7280;
  --border: #e5e7eb;
  --shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  --radius: 8px;
}

*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Segoe UI', Arial, sans-serif;
  font-size: 16px;
  line-height: 1.6;
  color: var(--text);
  background-color: var(--bg);
}

/* ===========================
   Header
   =========================== */
header {
  background-color: var(--text);
  padding: 1rem 2rem;
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-inner {
  max-width: 800px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  color: var(--primary-light);
  font-weight: 700;
  font-size: 1.1rem;
}

nav {
  display: flex;
  gap: 1.5rem;
}

nav a {
  color: #e0e0ff;
  text-decoration: none;
  font-weight: 500;
  font-size: 0.95rem;
  transition: color 0.2s ease;
}

nav a:hover {
  color: var(--primary-light);
}

/* ===========================
   Main Layout
   =========================== */
main {
  max-width: 800px;
  margin: 2rem auto;
  padding: 0 1rem;
}

section {
  background: var(--surface);
  padding: 2rem;
  border-radius: var(--radius);
  margin-bottom: 1.5rem;
  box-shadow: var(--shadow);
}

/* ===========================
   Typography
   =========================== */
h1 {
  font-size: 2.2rem;
  color: var(--text);
  margin-bottom: 1rem;
}

h2 {
  font-size: 1.5rem;
  color: var(--primary);
  margin-bottom: 1rem;
}

p {
  margin-bottom: 1rem;
  color: var(--text-muted);
  max-width: 60ch;
}

/* ===========================
   About Section
   =========================== */
#about img {
  border-radius: 50%;
  border: 4px solid var(--primary-light);
  display: block;
  margin-top: 1.5rem;
}

/* ===========================
   Skills List
   =========================== */
ul {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

li {
  padding: 0.5rem 0;
  border-bottom: 1px solid var(--border);
}

li:last-child {
  border-bottom: none;
}

/* ===========================
   Form
   =========================== */
form {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

label {
  font-weight: 600;
  color: #374151;
  font-size: 0.9rem;
}

input,
textarea {
  padding: 0.6rem 0.75rem;
  border: 1.5px solid var(--border);
  border-radius: 4px;
  font-size: 1rem;
  font-family: inherit;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

input:focus,
textarea:focus {
  outline: none;
  border-color: var(--primary);
  box-shadow: 0 0 0 3px rgba(124, 58, 237, 0.15);
}

textarea {
  resize: vertical;
  min-height: 100px;
}

button[type="submit"] {
  background-color: var(--primary);
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  align-self: flex-start;
  transition: background-color 0.2s ease, transform 0.1s ease;
}

button[type="submit"]:hover {
  background-color: var(--primary-dark);
}

button[type="submit"]:active {
  transform: scale(0.98);
}

/* ===========================
   Form Response
   =========================== */
.form-response {
  margin-top: 1rem;
  padding: 0.75rem 1rem;
  background: #ecfdf5;
  color: #065f46;
  border-radius: 4px;
  border-left: 4px solid #10b981;
}

.hidden {
  display: none;
}

/* ===========================
   Footer
   =========================== */
footer {
  text-align: center;
  padding: 2rem;
  color: var(--text-muted);
  font-size: 0.875rem;
}

/* ===========================
   Responsive
   =========================== */
@media (min-width: 600px) {
  main {
    padding: 0 2rem;
  }

  h1 {
    font-size: 2.8rem;
  }
}
```

### `script.js`

```javascript
const form = document.getElementById("contact-form");
const response = document.getElementById("form-response");

form.addEventListener("submit", function(event) {
  event.preventDefault();  // prevent page reload / HTTP submission

  // Show the success message
  response.classList.remove("hidden");

  // Scroll to it
  response.scrollIntoView({ behavior: "smooth", block: "nearest" });

  // Clear the form
  form.reset();
});
```

---

## Challenge 1: Styled Card Component

### `card.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Product Card</title>
  <style>
    body {
      background: #f4f4f8;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      margin: 0;
      font-family: 'Segoe UI', Arial, sans-serif;
    }

    .card {
      background: white;
      border-radius: 12px;
      box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
      overflow: hidden;
      max-width: 320px;
      width: 100%;
    }

    .card img {
      width: 100%;
      height: 200px;
      object-fit: cover;
      display: block;
    }

    .card-body {
      padding: 1.25rem;
    }

    .card-body h2 {
      font-size: 1.25rem;
      margin-bottom: 0.5rem;
      color: #1a1a2e;
    }

    .card-body p {
      color: #6b7280;
      font-size: 0.9rem;
      margin-bottom: 1rem;
      line-height: 1.5;
    }

    .price {
      font-size: 1.5rem;
      font-weight: 700;
      color: #7c3aed;
      display: block;
      margin-bottom: 1rem;
    }

    .btn-buy {
      width: 100%;
      padding: 0.75rem;
      background: #7c3aed;
      color: white;
      border: none;
      border-radius: 6px;
      font-size: 1rem;
      cursor: pointer;
      transition: background 0.2s ease;
    }

    .btn-buy:hover {
      background: #6d28d9;
    }
  </style>
</head>
<body>
  <div class="card">
    <img src="https://via.placeholder.com/320x200/7c3aed/ffffff?text=Product"
         alt="Wireless Headphones">
    <div class="card-body">
      <h2>Wireless Headphones Pro</h2>
      <p>Premium noise-cancelling headphones with 30-hour battery life and studio-quality sound.</p>
      <span class="price">$149.99</span>
      <button class="btn-buy">Buy Now</button>
    </div>
  </div>
</body>
</html>
```

---

## Challenge 2: Navigation Bar

### `navbar.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Navigation Bar</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    body { font-family: 'Segoe UI', Arial, sans-serif; }

    header {
      background: #1a1a2e;
      padding: 0 2rem;
    }

    .navbar {
      max-width: 1100px;
      margin: 0 auto;
      display: flex;
      justify-content: space-between;
      align-items: center;
      height: 60px;
    }

    .logo {
      color: #a78bfa;
      font-size: 1.2rem;
      font-weight: 700;
      text-decoration: none;
    }

    nav {
      display: flex;
      gap: 2rem;
    }

    nav a {
      color: #e0e0ff;
      text-decoration: none;
      font-size: 0.95rem;
      padding: 0.25rem 0;
      border-bottom: 2px solid transparent;
      transition: color 0.2s, border-color 0.2s;
    }

    nav a:hover {
      color: #a78bfa;
      border-bottom-color: #a78bfa;
    }

    /* Mobile: stack vertically */
    @media (max-width: 600px) {
      .navbar {
        flex-direction: column;
        height: auto;
        padding: 1rem 0;
        gap: 1rem;
      }

      nav {
        gap: 1.5rem;
      }
    }
  </style>
</head>
<body>
  <header>
    <div class="navbar">
      <a href="#" class="logo">MySite</a>
      <nav>
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Blog</a>
        <a href="#">Contact</a>
      </nav>
    </div>
  </header>
  <main style="padding: 2rem; color: #333;">
    <h1>Page Content Here</h1>
    <p>Resize the browser window to see the responsive navbar.</p>
  </main>
</body>
</html>
```

---

## Challenge 3: Responsive Two-Column Layout

### `layout.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Two-Column Layout</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: 'Segoe UI', Arial, sans-serif; background: #f4f4f8; color: #1a1a2e; }

    .page {
      display: grid;
      grid-template-areas:
        "header"
        "sidebar"
        "main"
        "footer";
      min-height: 100vh;
    }

    /* Desktop: sidebar left, main right */
    @media (min-width: 768px) {
      .page {
        grid-template-columns: 250px 1fr;
        grid-template-rows: auto 1fr auto;
        grid-template-areas:
          "header  header"
          "sidebar main"
          "footer  footer";
      }
    }

    header {
      grid-area: header;
      background: #1a1a2e;
      color: white;
      padding: 1rem 2rem;
    }

    .sidebar {
      grid-area: sidebar;
      background: white;
      padding: 1.5rem;
      border-right: 1px solid #e5e7eb;
    }

    .sidebar h2 { font-size: 1rem; text-transform: uppercase; letter-spacing: 1px; color: #6b7280; margin-bottom: 1rem; }

    .sidebar ul { list-style: none; }
    .sidebar li { margin-bottom: 0.5rem; }
    .sidebar a { color: #7c3aed; text-decoration: none; }
    .sidebar a:hover { text-decoration: underline; }

    main {
      grid-area: main;
      padding: 2rem;
      max-width: 700px;
    }

    main h1 { margin-bottom: 1rem; }
    main p  { color: #4b5563; line-height: 1.7; margin-bottom: 1rem; }
    main img { width: 100%; border-radius: 8px; margin: 1rem 0; }

    footer {
      grid-area: footer;
      background: #1a1a2e;
      color: #9ca3af;
      text-align: center;
      padding: 1rem;
      font-size: 0.875rem;
    }
  </style>
</head>
<body>
  <div class="page">
    <header><h1 style="font-size:1.2rem;">My Blog</h1></header>

    <aside class="sidebar">
      <h2>Categories</h2>
      <ul>
        <li><a href="#">Technology</a></li>
        <li><a href="#">Design</a></li>
        <li><a href="#">Programming</a></li>
      </ul>
    </aside>

    <main>
      <h1>Getting Started with Web Development</h1>
      <img src="https://via.placeholder.com/700x300/7c3aed/ffffff?text=Article+Image"
           alt="Coding on a laptop">
      <p>
        Web development is one of the most exciting and accessible fields in technology.
        With just a text editor and a browser, you can start building websites that
        anyone in the world can access.
      </p>
      <p>
        The three core technologies — HTML, CSS, and JavaScript — work together to
        create everything you see on the web. Learning them in order, with practice
        at each step, is the most effective path.
      </p>
    </main>

    <footer><p>&copy; 2026 My Blog</p></footer>
  </div>
</body>
</html>
```

---

## Challenge 4: HTML Form with Validation

### `form.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Registration Form</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: 'Segoe UI', Arial, sans-serif; background: #f4f4f8; display: flex; justify-content: center; padding: 2rem 1rem; }

    form {
      background: white;
      padding: 2rem;
      border-radius: 8px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.08);
      width: 100%;
      max-width: 480px;
    }

    fieldset { border: none; }
    legend { font-size: 1.5rem; font-weight: 700; margin-bottom: 1.5rem; color: #1a1a2e; }

    .field { display: flex; flex-direction: column; gap: 0.25rem; margin-bottom: 1rem; }
    label { font-size: 0.875rem; font-weight: 600; color: #374151; }

    input, select {
      padding: 0.6rem 0.75rem;
      border: 1.5px solid #d1d5db;
      border-radius: 4px;
      font-size: 1rem;
      font-family: inherit;
    }

    input:focus, select:focus {
      outline: none;
      border-color: #7c3aed;
      box-shadow: 0 0 0 3px rgba(124, 58, 237, 0.15);
    }

    .checkbox-row {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      margin-bottom: 1.5rem;
    }

    .checkbox-row input { width: auto; }
    .checkbox-row label { font-weight: 400; font-size: 0.9rem; }

    button {
      width: 100%;
      padding: 0.75rem;
      background: #7c3aed;
      color: white;
      border: none;
      border-radius: 4px;
      font-size: 1rem;
      cursor: pointer;
    }

    button:hover { background: #6d28d9; }
  </style>
</head>
<body>
  <form id="reg-form">
    <fieldset>
      <legend>Create Account</legend>

      <div class="field">
        <label for="reg-name">Full Name</label>
        <input type="text" id="reg-name" name="name" placeholder="Alex Rivera" required>
      </div>

      <div class="field">
        <label for="reg-email">Email Address</label>
        <input type="email" id="reg-email" name="email" placeholder="you@example.com" required>
      </div>

      <div class="field">
        <label for="reg-password">Password (min 8 characters)</label>
        <input type="password" id="reg-password" name="password" minlength="8" required>
      </div>

      <div class="field">
        <label for="reg-dob">Date of Birth</label>
        <input type="date" id="reg-dob" name="dob">
      </div>

      <div class="field">
        <label for="reg-language">Preferred Language</label>
        <select id="reg-language" name="language">
          <option value="en">English</option>
          <option value="es">Spanish</option>
          <option value="fr">French</option>
          <option value="other">Other</option>
        </select>
      </div>

      <div class="checkbox-row">
        <input type="checkbox" id="reg-terms" name="terms" required>
        <label for="reg-terms">I agree to the Terms of Service</label>
      </div>

      <button type="submit">Create Account</button>
    </fieldset>
  </form>

  <script>
    document.getElementById("reg-form").addEventListener("submit", function(e) {
      e.preventDefault();
      const name = document.getElementById("reg-name").value;
      alert("Account created for " + name + "!");
    });
  </script>
</body>
</html>
```

---

## Challenge 5: DOM Manipulation — To-Do List

### `interactive.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>To-Do List</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: 'Segoe UI', Arial, sans-serif; background: #f4f4f8; display: flex; justify-content: center; padding: 3rem 1rem; }

    .todo-app {
      background: white;
      border-radius: 12px;
      box-shadow: 0 4px 16px rgba(0,0,0,0.1);
      padding: 2rem;
      width: 100%;
      max-width: 420px;
    }

    h1 { font-size: 1.5rem; margin-bottom: 1.5rem; color: #1a1a2e; }

    .input-row {
      display: flex;
      gap: 0.5rem;
      margin-bottom: 1.5rem;
    }

    .input-row input {
      flex: 1;
      padding: 0.6rem 0.75rem;
      border: 1.5px solid #d1d5db;
      border-radius: 6px;
      font-size: 1rem;
    }

    .input-row input:focus {
      outline: none;
      border-color: #7c3aed;
    }

    .input-row button {
      padding: 0.6rem 1rem;
      background: #7c3aed;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-size: 1rem;
    }

    .input-row button:hover { background: #6d28d9; }

    #task-list { list-style: none; }

    #task-list li {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0.75rem 0;
      border-bottom: 1px solid #e5e7eb;
    }

    #task-list li:last-child { border-bottom: none; }

    .task-text { flex: 1; }
    .task-text.done {
      text-decoration: line-through;
      color: #9ca3af;
    }

    .done-btn {
      padding: 0.25rem 0.6rem;
      font-size: 0.8rem;
      background: #ecfdf5;
      color: #065f46;
      border: 1px solid #a7f3d0;
      border-radius: 4px;
      cursor: pointer;
    }

    .done-btn:hover { background: #d1fae5; }
  </style>
</head>
<body>
  <div class="todo-app">
    <h1>My To-Do List</h1>

    <div class="input-row">
      <input type="text" id="task-input" placeholder="Add a new task..." />
      <button id="add-btn">Add</button>
    </div>

    <ul id="task-list"></ul>
  </div>

  <script>
    const input = document.getElementById("task-input");
    const addBtn = document.getElementById("add-btn");
    const taskList = document.getElementById("task-list");

    function addTask() {
      const text = input.value.trim();
      if (!text) return;  // don't add empty tasks

      const li = document.createElement("li");

      const span = document.createElement("span");
      span.textContent = text;
      span.className = "task-text";

      const doneBtn = document.createElement("button");
      doneBtn.textContent = "Done";
      doneBtn.className = "done-btn";
      doneBtn.addEventListener("click", function() {
        span.classList.toggle("done");
        doneBtn.textContent = span.classList.contains("done") ? "Undo" : "Done";
      });

      li.appendChild(span);
      li.appendChild(doneBtn);
      taskList.appendChild(li);

      input.value = "";
      input.focus();
    }

    addBtn.addEventListener("click", addTask);

    // Also add task when Enter is pressed in the input
    input.addEventListener("keydown", function(event) {
      if (event.key === "Enter") {
        addTask();
      }
    });
  </script>
</body>
</html>
```

### Key concepts demonstrated in Challenge 5:

- `document.createElement()` — creates new DOM nodes in memory
- `element.appendChild()` — inserts a node into the DOM tree
- `classList.toggle()` — adds or removes a class, returning the new state
- Multiple event listeners — both click and keydown
- Input validation with early return
- Closure — the `doneBtn` click handler references `span` from the outer `addTask` function scope

---

*Back to HTML exercises: [05 — Introduction to HTML](../05-introduction-to-html.md)*
*Back to CSS exercises: [06 — Introduction to CSS](../06-introduction-to-css.md)*
*Back to quiz: [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md)*
