# 07 — Introduction to JavaScript: Making Pages Come Alive

## Learning Objectives

By the end of this page, you will be able to:

- Explain what JavaScript is and what role it plays alongside HTML and CSS
- Describe where JavaScript runs (browser, server)
- Add a script to an HTML page in three ways
- Write a simple script using `console.log` and an alert
- Perform basic DOM manipulation — change content, change styles, respond to a click
- Understand that Section 4 is the full JavaScript deep dive

> **Note:** This is a preview. We'll cover JavaScript fully in Section 4. The goal here is to see how JavaScript fits into the web picture before we go deep.

---

## The Three Layers of the Web

HTML, CSS, and JavaScript play distinct and complementary roles:

| Layer | Technology | Responsibility |
|-------|-----------|---------------|
| Structure | HTML | What content exists and what it means |
| Presentation | CSS | How it looks — colors, fonts, layout |
| Behavior | JavaScript | What it does — interactions, data, logic |

> **Analogy:** A restaurant menu as a metaphor for a web page — HTML is the printed words on the menu (the content). CSS is the design, fonts, and layout of the menu card (the appearance). JavaScript is the waiter who can update the specials board in real time, show you a larger version of a dish photo when you hover, or remove a sold-out item without reprinting the entire menu.

---

## What Is JavaScript?

**JavaScript (JS)** is a programming language — unlike HTML (markup) and CSS (stylesheet language), it has the full power of a programming language:

- **Variables** to store data
- **Functions** to reuse code
- **Conditionals** (`if/else`) to make decisions
- **Loops** to repeat operations
- **Objects and arrays** to organize data

JavaScript was created in 1995 by Brendan Eich at Netscape in just **10 days**. Despite its rushed origins, it became the **only** programming language that runs natively in web browsers — and therefore one of the most widely used languages in the world.

### Where Does JavaScript Run?

**In the browser (client-side):**
- Built into every web browser
- Runs in a sandboxed environment — can't access your files or system
- Has access to the DOM, browser APIs (location, camera, storage)

**On the server (Node.js):**
- Node.js lets JavaScript run outside the browser — on servers, your terminal, IoT devices
- Same language, different environment and APIs
- Powers much of the modern web backend (Express.js, Next.js, etc.)

We'll focus on browser JavaScript in Section 4. Node.js comes up in Sections 7 and 8.

---

## Adding JavaScript to a Page

### Method 1: Inline (in an event attribute)

```html
<button onclick="alert('Hello!')">Click Me</button>
```

Works but mixes behavior with structure. Not recommended for anything beyond a quick demo.

### Method 2: Internal Script Block

```html
<body>
  <h1>My Page</h1>

  <!-- Content here -->

  <!-- Script at the bottom of body -->
  <script>
    console.log("The page has loaded!");
    alert("Welcome to my page!");
  </script>
</body>
```

Good for small examples. Place `<script>` just before `</body>` so the DOM is ready before the script runs.

### Method 3: External Script File (Recommended)

```html
<head>
  <script src="script.js" defer></script>
</head>
```

```javascript
// script.js
console.log("Hello from an external script!");
```

Best practice for anything real. Use `defer` so the script downloads in parallel with HTML parsing but runs only after parsing is complete.

---

## Your First Script: Hello World

Create `script.js` in the same folder as your `about-me.html`:

```javascript
// This is a comment — the browser ignores it
console.log("Hello, World!");
```

Link it in your HTML:
```html
<script src="script.js" defer></script>
```

Open the page in your browser, then open DevTools (`F12` / `Cmd+Option+I`) and click the **Console** tab.

You'll see: `Hello, World!`

The **Console** is your JavaScript playground — you can type JavaScript directly into it and see results immediately. We'll use it a lot in Section 4.

---

## Basic JavaScript Concepts (Preview)

### Variables

```javascript
let name = "Alex";           // can be changed later
const age = 25;              // cannot be changed (constant)

console.log(name);           // Alex
console.log("Age:", age);    // Age: 25
```

### Interacting with the User

```javascript
// Show a dialog box
alert("Welcome to my site!");

// Ask for input
let userName = prompt("What's your name?");
console.log("Hello, " + userName + "!");

// Ask yes/no
let confirmed = confirm("Are you sure you want to leave?");
// confirmed is true or false
```

### Manipulating the DOM

This is where JavaScript and HTML meet:

```javascript
// Select an element by its ID
const heading = document.getElementById("site-title");

// Change its text
heading.textContent = "Welcome, " + name + "!";

// Change its style
heading.style.color = "purple";
```

### Responding to Events

```javascript
const button = document.getElementById("greet-btn");

button.addEventListener("click", function() {
  const name = prompt("What's your name?");
  document.getElementById("greeting").textContent = "Hello, " + name + "!";
});
```

```html
<button id="greet-btn">Greet Me</button>
<p id="greeting"></p>
```

Click the button → prompt appears → you type a name → the paragraph updates. No page reload. This is the power of JavaScript and the DOM working together.

---

## Hands-On: Add Interactivity to Your About Me Page

Open your `about-me.html` and `script.js`. Add a small interaction — a greeting message when the form is submitted.

Add the `id="contact-form"` attribute to your form:
```html
<form id="contact-form">
  ...
</form>
```

Add a response area below the form:
```html
<div id="form-response" style="display: none; color: green; margin-top: 1rem;">
  Thanks for your message! I'll get back to you soon.
</div>
```

Then in `script.js`:

```javascript
const form = document.getElementById("contact-form");
const response = document.getElementById("form-response");

form.addEventListener("submit", function(event) {
  // Prevent the form from actually sending (we have no server)
  event.preventDefault();

  // Show the thank-you message
  response.style.display = "block";

  // Clear the form
  form.reset();
});
```

Now when you fill in the form and click "Send Message," instead of navigating away, the page shows a thank-you message. No server required.

---

## What's Coming in Section 4

This was a taste. Section 4 is the full JavaScript curriculum:

- Variables, data types, and operators
- Control flow: `if/else`, `switch`, loops (`for`, `while`, `forEach`)
- Functions, parameters, return values
- Arrays and objects
- Error handling (`try/catch`)
- Full DOM manipulation
- Events and event handling
- Forms and validation
- The Fetch API and AJAX

By the end of Section 4, you'll be building fully interactive web pages that communicate with servers.

**Further Reading:**
- [JavaScript Basics — MDN](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/JavaScript_basics)
- [JavaScript First Steps — MDN](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/First_steps)
- [The Modern JavaScript Tutorial — javascript.info](https://javascript.info/) — One of the best JS learning resources

---

## Check Your Understanding

1. What is the difference between HTML, CSS, and JavaScript in terms of their roles?
2. Why is it recommended to place `<script>` tags at the bottom of `<body>`, or use the `defer` attribute?
3. What does `console.log()` do, and how do you see its output?
4. What is an event listener, and why is it more flexible than using an `onclick` attribute?
5. What does `event.preventDefault()` do in a form's submit event listener?

---

*Previous: [06 — Introduction to CSS](06-introduction-to-css.md)*
*Next: [08 — HTML5, CSS3, and the Evolution of the Web](08-html5-css3-evolution.md)*
