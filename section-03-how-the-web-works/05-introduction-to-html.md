# 05 — Introduction to HTML: Building Your First Web Page

## Learning Objectives

By the end of this page, you will be able to:

- Explain what HTML is and what it does
- Write a valid HTML document with proper structure
- Use common HTML elements: headings, paragraphs, links, images, lists
- Understand the difference between block and inline elements
- Create a basic HTML form with inputs and a button
- Use semantic HTML elements to give structure and meaning

---

## What Is HTML?

**HTML (HyperText Markup Language)** is the standard language for creating web pages. It describes the *structure* and *content* of a web page using **elements** — building blocks that tell the browser what each piece of content is.

HTML does **not** control how things look (that's CSS) or how they behave (that's JavaScript). Its job is purely **structure and meaning**:

> "This is a heading. This is a paragraph. This is an image. This is a navigation menu. This is a form."

> **Analogy:** HTML is like the skeleton of a building — the walls, floors, and roof that give structure. CSS is the paint, wallpaper, and furniture that make it look good. JavaScript is the electricity and plumbing that make things work.

---

## HTML Elements, Tags, and Attributes

### Elements

An **element** is a piece of HTML content. Most elements have an **opening tag** and a **closing tag** that wrap the content:

```html
<p>This is a paragraph.</p>
```

- `<p>` — the opening tag
- `This is a paragraph.` — the content
- `</p>` — the closing tag (note the `/`)

Some elements are **self-closing** — they have no content and no closing tag:

```html
<img src="photo.jpg" alt="A photo">
<br>
<hr>
<input type="text">
<meta charset="UTF-8">
```

### Attributes

**Attributes** provide additional information about an element. They go inside the opening tag as `name="value"` pairs:

```html
<a href="https://github.com" target="_blank">Visit GitHub</a>
```

- `href` — the URL the link points to
- `target="_blank"` — open in a new tab

```html
<img src="images/logo.png" alt="Company logo" width="200">
```

- `src` — where the image file is
- `alt` — text description (shown if image fails to load; used by screen readers)
- `width` — display width in pixels

---

## The Structure of an HTML Document

Every HTML page has the same required structure:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
    <link rel="stylesheet" href="styles.css">
  </head>
  <body>
    <!-- Your page content goes here -->
    <h1>Hello, World!</h1>
    <p>This is my first web page.</p>
  </body>
</html>
```

### Breaking It Down

| Part | Purpose |
|------|---------|
| `<!DOCTYPE html>` | Tells the browser this is HTML5 (not older versions) |
| `<html lang="en">` | The root element; `lang="en"` helps screen readers and search engines |
| `<head>` | Metadata — information *about* the page, not visible content |
| `<meta charset="UTF-8">` | Character encoding — ensures special characters display correctly |
| `<meta name="viewport" ...>` | Tells mobile browsers how to scale the page |
| `<title>` | The page title shown in the browser tab and search results |
| `<link rel="stylesheet">` | Links to an external CSS file |
| `<body>` | All visible page content goes here |
| `<!-- comment -->` | HTML comments — not displayed, helpful for documentation |

---

## Text Elements

### Headings

Six levels of headings — `<h1>` (most important) through `<h6>` (least important):

```html
<h1>Main Page Title</h1>
<h2>Section Heading</h2>
<h3>Subsection</h3>
<h4>Sub-subsection</h4>
<h5>Minor heading</h5>
<h6>Smallest heading</h6>
```

> **Important:** Use headings for structure and hierarchy, not for making text big. There should be only **one `<h1>`** per page — it represents the main topic. Screen readers and search engines rely on heading hierarchy.

### Paragraphs

```html
<p>This is a paragraph of text. It can be as long as needed.</p>
<p>Each <code>&lt;p&gt;</code> creates a new paragraph with spacing above and below.</p>
```

### Bold and Italic

```html
<strong>This is important (bold by default)</strong>
<em>This is emphasized (italic by default)</em>

<!-- Purely visual — no semantic meaning: -->
<b>Bold text</b>
<i>Italic text</i>
```

Prefer `<strong>` and `<em>` — they carry semantic meaning (important, emphasized) on top of visual styling.

### Line Breaks and Horizontal Rules

```html
<p>Line one.<br>Line two in the same paragraph.</p>

<hr>  <!-- Horizontal divider line -->
```

---

## Links

The `<a>` (anchor) element creates hyperlinks — the foundation of the web:

```html
<!-- External link -->
<a href="https://www.example.com">Visit Example</a>

<!-- Open in new tab -->
<a href="https://github.com" target="_blank" rel="noopener noreferrer">GitHub</a>

<!-- Internal link (same site) -->
<a href="/about">About Us</a>

<!-- Link to anchor within the same page -->
<a href="#section-2">Jump to Section 2</a>

<!-- Email link -->
<a href="mailto:hello@example.com">Email Us</a>

<!-- Phone link (mobile) -->
<a href="tel:+15551234567">Call Us</a>
```

> **Note:** `rel="noopener noreferrer"` on external `target="_blank"` links is a security best practice — it prevents the new tab from accessing the opener page.

---

## Images

```html
<img src="images/hero.jpg" alt="A mountain landscape at sunset" width="800" height="400">
```

| Attribute | Required? | Purpose |
|-----------|----------|---------|
| `src` | Yes | Path or URL to the image file |
| `alt` | Yes | Text description (accessibility + SEO) |
| `width` | Recommended | Prevents layout shifts during load |
| `height` | Recommended | Same — browser reserves space before image loads |

**Common image formats:**
- **JPEG/JPG** — photos; good compression, no transparency
- **PNG** — graphics with transparency; larger than JPEG for photos
- **WebP** — modern format; smaller than JPEG/PNG with equal quality; wide browser support
- **SVG** — vector graphics; scalable without quality loss; great for icons and logos
- **GIF** — animations (mostly replaced by CSS animations or video)

---

## Lists

### Unordered List (bullets)

```html
<ul>
  <li>HTML</li>
  <li>CSS</li>
  <li>JavaScript</li>
</ul>
```

### Ordered List (numbered)

```html
<ol>
  <li>Open your code editor</li>
  <li>Write HTML</li>
  <li>Open in browser</li>
</ol>
```

### Nested Lists

```html
<ul>
  <li>Front End
    <ul>
      <li>HTML</li>
      <li>CSS</li>
      <li>JavaScript</li>
    </ul>
  </li>
  <li>Back End
    <ul>
      <li>Node.js</li>
      <li>Python</li>
    </ul>
  </li>
</ul>
```

---

## Block vs Inline Elements

Every HTML element has a default display behavior:

### Block Elements

- Take up the **full width** available
- Start on a **new line**
- Stack vertically
- Examples: `<p>`, `<div>`, `<h1>`–`<h6>`, `<ul>`, `<ol>`, `<li>`, `<header>`, `<main>`, `<section>`

### Inline Elements

- Only take up as much **width as their content**
- Flow **inline** with surrounding text
- Do not start on a new line
- Examples: `<a>`, `<span>`, `<strong>`, `<em>`, `<img>`, `<code>`, `<button>`

```html
<p>
  This paragraph is a block element.
  <a href="#">This link</a> is inline — it flows within the text.
  <strong>This bold text</strong> is also inline.
</p>
```

### `<div>` and `<span>`: Generic Containers

- `<div>` — a generic **block** container. No semantic meaning. Used for grouping block elements.
- `<span>` — a generic **inline** container. No semantic meaning. Used for styling or targeting a piece of text.

```html
<div class="card">
  <h2>Card Title</h2>
  <p>Card content with a <span class="highlight">highlighted word</span>.</p>
</div>
```

---

## Forms

Forms let users input data and submit it to a server.

```html
<form action="/submit" method="POST">

  <!-- Text input -->
  <label for="name">Your Name:</label>
  <input type="text" id="name" name="name" placeholder="Alex Rivera" required>

  <!-- Email input -->
  <label for="email">Email:</label>
  <input type="email" id="email" name="email" required>

  <!-- Password -->
  <label for="password">Password:</label>
  <input type="password" id="password" name="password" minlength="8" required>

  <!-- Dropdown select -->
  <label for="role">Role:</label>
  <select id="role" name="role">
    <option value="">-- Select --</option>
    <option value="developer">Developer</option>
    <option value="designer">Designer</option>
    <option value="manager">Manager</option>
  </select>

  <!-- Radio buttons (pick one) -->
  <fieldset>
    <legend>Experience Level:</legend>
    <input type="radio" id="beginner" name="level" value="beginner">
    <label for="beginner">Beginner</label>
    <input type="radio" id="intermediate" name="level" value="intermediate">
    <label for="intermediate">Intermediate</label>
  </fieldset>

  <!-- Checkbox -->
  <input type="checkbox" id="newsletter" name="newsletter" value="yes">
  <label for="newsletter">Subscribe to newsletter</label>

  <!-- Multiline text -->
  <label for="message">Message:</label>
  <textarea id="message" name="message" rows="4" cols="40"></textarea>

  <!-- Submit button -->
  <button type="submit">Send</button>

</form>
```

### Form Attributes

| Attribute | Purpose |
|-----------|---------|
| `action` | Where to send the form data (URL) |
| `method` | HTTP method — `GET` (data in URL) or `POST` (data in body) |
| `id` | Unique identifier; links `<label for="">` to its input |
| `name` | The key in the form data sent to the server |
| `required` | Field must be filled in before submitting |
| `placeholder` | Hint text shown when field is empty |

---

## Semantic HTML

**Semantic HTML** means using elements that describe the *meaning* of content, not just its appearance.

Compare:

```html
<!-- Non-semantic (old way) -->
<div id="header">
  <div id="nav">...</div>
</div>
<div id="main">
  <div class="article">...</div>
</div>
<div id="footer">...</div>
```

```html
<!-- Semantic (modern, correct way) -->
<header>
  <nav>...</nav>
</header>
<main>
  <article>...</article>
</main>
<footer>...</footer>
```

Both render identically in most browsers. But the semantic version:
- Helps **screen readers** navigate the page for visually impaired users
- Helps **search engines** understand page structure
- Makes code **easier to read** and maintain
- Is the **correct, modern approach**

### Common Semantic Elements

| Element | Meaning |
|---------|---------|
| `<header>` | Page or section header (logo, navigation) |
| `<nav>` | Navigation links |
| `<main>` | The main content of the page (only one per page) |
| `<article>` | A self-contained piece of content (blog post, news article) |
| `<section>` | A thematic grouping of content |
| `<aside>` | Related but secondary content (sidebar, pull quotes) |
| `<footer>` | Page or section footer (copyright, links) |
| `<figure>` | An image or diagram with a caption |
| `<figcaption>` | The caption for a `<figure>` |
| `<time>` | A date or time value |
| `<address>` | Contact information |

---

## Hands-On: Your First Web Page

Create a file called `about-me.html` in a new folder. Open it in any text editor (VS Code, Notepad, TextEdit).

Build an "About Me" page with the following content:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>About Me</title>
  </head>
  <body>

    <header>
      <nav>
        <a href="#about">About</a> |
        <a href="#skills">Skills</a> |
        <a href="#contact">Contact</a>
      </nav>
    </header>

    <main>

      <section id="about">
        <h1>Hi, I'm [Your Name]</h1>
        <p>
          I'm learning to build web applications. This is my first HTML page!
        </p>
        <img src="https://via.placeholder.com/200x200" alt="My photo">
      </section>

      <section id="skills">
        <h2>What I'm Learning</h2>
        <ul>
          <li>HTML — page structure</li>
          <li>CSS — styling and layout</li>
          <li>JavaScript — interactivity</li>
          <li>TypeScript — typed JavaScript</li>
          <li>React — UI framework</li>
        </ul>
      </section>

      <section id="contact">
        <h2>Get in Touch</h2>
        <form>
          <label for="your-name">Name:</label>
          <input type="text" id="your-name" name="name" placeholder="Your name" required>
          <br><br>
          <label for="your-email">Email:</label>
          <input type="email" id="your-email" name="email" placeholder="you@example.com" required>
          <br><br>
          <label for="your-message">Message:</label><br>
          <textarea id="your-message" name="message" rows="4" cols="40"></textarea>
          <br><br>
          <button type="submit">Send Message</button>
        </form>
      </section>

    </main>

    <footer>
      <p>&copy; 2026 [Your Name]. Built with HTML.</p>
    </footer>

  </body>
</html>
```

**To view it:** Open your file manager, navigate to where you saved `about-me.html`, and double-click it. It will open in your default browser. You have a working web page — no server needed.

> You'll continue improving this page in the next section when you add CSS styling!

**Solutions and variations:** [solutions/html-css-project-solutions.md](solutions/html-css-project-solutions.md)

**Further Reading:**
- [HTML Basics — MDN](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/HTML_basics)
- [HTML Reference — MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)
- [HTML Validator — W3C](https://validator.w3.org/) — Paste your HTML and check for errors
- [Semantic HTML — web.dev](https://web.dev/learn/html/semantic-html)

---

## Check Your Understanding

1. What are the three roles of HTML, CSS, and JavaScript respectively?
2. What is the purpose of the `<head>` element vs the `<body>` element?
3. What is the difference between a block element and an inline element?
4. Why should you use `<strong>` instead of `<b>` for important text?
5. What is semantic HTML and why does it matter?
6. In a form, what is the purpose of `<label for="id">` and why must it match the input's `id`?

---

*Previous: [04 — The DOM](04-the-dom.md)*
*Next: [06 — Introduction to CSS](06-introduction-to-css.md)*
