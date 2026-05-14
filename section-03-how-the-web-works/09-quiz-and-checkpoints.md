# 09 — Section Quiz and Checkpoints

## Section 3 Learning Objectives Review

Before taking this quiz, confirm you can:

- [ ] Explain DNS record types and trace a full domain name resolution
- [ ] Describe the client-server model and the request-response cycle
- [ ] Explain the critical rendering path from HTML bytes to pixels
- [ ] Describe what the DOM is and how JavaScript uses it
- [ ] Write valid HTML with proper structure, semantic elements, and forms
- [ ] Write CSS using selectors, the box model, Flexbox, and media queries
- [ ] Add JavaScript to a page and respond to DOM events
- [ ] Describe what HTML5 and CSS3 added and why each mattered

---

## Part 1: Multiple Choice

Answer key: [solutions/09-quiz-solutions.md](solutions/09-quiz-solutions.md)

---

**1.** In the domain `api.store.example.co.uk`, what is the second-level domain?

- A) `api`
- B) `store`
- C) `example`
- D) `co`

---

**2.** What does a DNS `CNAME` record do?

- A) Maps a hostname to an IPv6 address
- B) Maps a hostname to another hostname (an alias)
- C) Identifies the mail server for the domain
- D) Provides a text description of the domain owner

---

**3.** Which of the following is NOT a render-blocking resource by default?

- A) A CSS stylesheet in `<head>`
- B) A `<script>` tag without `defer` or `async`
- C) A `<script defer>` tag in `<head>`
- D) An `<style>` block in `<head>`

---

**4.** What is the correct order of the critical rendering path?

- A) CSSOM → DOM → Render Tree → Layout → Paint → Composite
- B) DOM → CSSOM → Render Tree → Layout → Paint → Composite
- C) DOM → CSSOM → Layout → Render Tree → Paint → Composite
- D) Render Tree → DOM → CSSOM → Layout → Paint → Composite

---

**5.** A developer wants to animate a card sliding in from the left. Which CSS property is most performant?

- A) `left: -300px` transitioning to `left: 0`
- B) `margin-left: -300px` transitioning to `margin-left: 0`
- C) `transform: translateX(-300px)` transitioning to `transform: translateX(0)`
- D) `width: 0` transitioning to `width: 300px`

---

**6.** What is the DOM?

- A) The original HTML source file sent from the server
- B) A database where the browser stores cached web pages
- C) The browser's live, in-memory tree representation of the page
- D) The stylesheet that controls how elements are positioned

---

**7.** Which CSS selector has the highest specificity?

- A) `p`
- B) `.intro`
- C) `#main-title`
- D) `div p`

---

**8.** In the CSS box model, which property creates space **inside** the border (between the border and the content)?

- A) Margin
- B) Padding
- C) Gap
- D) Inset

---

**9.** What does `box-sizing: border-box` change about how width is calculated?

- A) It includes the margin in the element's width
- B) It includes the padding and border in the element's width
- C) It makes all child elements inherit their parent's width
- D) It changes pixels to rem units automatically

---

**10.** Which HTML element should be used for the main navigation links of a website?

- A) `<div id="navigation">`
- B) `<menu>`
- C) `<nav>`
- D) `<links>`

---

**11.** What is the purpose of the `alt` attribute on an `<img>` element?

- A) It specifies the image title shown on hover
- B) It provides alternative text for screen readers and when the image fails to load
- C) It sets the image loading priority
- D) It defines a fallback background color

---

**12.** What does `display: flex` do to an element's children?

- A) Makes children invisible until hovered
- B) Stacks children vertically like a column
- C) Places children in a flexible row (or column) layout that can distribute space
- D) Makes children overlap each other

---

**13.** The HTML5 `<canvas>` element:

- A) Embeds a PDF viewer in the page
- B) Provides a JavaScript-drawable surface for 2D graphics
- C) Creates an inline frame for embedding another web page
- D) Defines the drawing area for CSS grid

---

**14.** What problem did the formation of WHATWG solve?

- A) The need for a free Certificate Authority for HTTPS certificates
- B) Fragmentation caused by browser vendors ignoring web standards
- C) The W3C's move toward strict XHTML 2.0 at the expense of backward compatibility
- D) The lack of a mobile-specific version of the HTML specification

---

**15.** Which of these is a correct use of CSS custom properties?

- A) `--brand-color = #7c3aed;`
- B) `:root { brand-color: #7c3aed; }`
- C) `:root { --brand-color: #7c3aed; }` and then `color: var(--brand-color);`
- D) `$brand-color: #7c3aed;` (this is Sass, not native CSS)

---

## Part 2: Short Answer

Suggested answers: [solutions/09-quiz-solutions.md](solutions/09-quiz-solutions.md)

**1.** Explain the difference between the DOM and the HTML source. When and why might they differ?

**2.** Describe the CSS cascade. If a `<p>` element is targeted by three rules — an element rule, a class rule, and an inline style — which wins, and why?

**3.** Explain what happens in the browser between when it receives the first byte of HTML and when the user sees a fully rendered page.

**4.** What is semantic HTML? Give three examples of a semantic element, what it means, and what it replaces.

**5.** Explain the difference between Flexbox and CSS Grid. Give a scenario where you would choose each.

---

## Part 3: Code Challenges

Solutions: [solutions/html-css-project-solutions.md](solutions/html-css-project-solutions.md)

---

### Challenge 1: Build a Styled Card Component

Create a new file `card.html`. Build a product card with:
- A placeholder image at the top
- A product name as an `<h2>`
- A short description in a `<p>`
- A price in a `<span>`
- A "Buy Now" button

Style it with CSS so that:
- The card has a white background, rounded corners, and a subtle shadow
- The image fills the card width
- The button is full-width, has a colored background, and changes on hover
- The card is centered on the page with `max-width: 320px`

---

### Challenge 2: Build a Navigation Bar

Create `navbar.html`. Build a horizontal navigation bar with:
- A logo/site name on the left
- Navigation links on the right: Home, About, Blog, Contact

Requirements:
- Use `<header>` and `<nav>` semantically
- Use Flexbox for layout
- Links should have no underline by default and change color on hover
- The navbar should have a background color that spans the full width
- At screen widths below 600px, stack the logo and links vertically

---

### Challenge 3: Responsive Two-Column Layout

Create `layout.html`. Build a page with:
- A header spanning full width
- A main area with a sidebar (left, 30% wide) and main content (right, 70%)
- A footer spanning full width

Requirements:
- Use CSS Grid for the overall layout
- Use `grid-template-areas` for named regions
- On mobile (below 768px), the sidebar should stack above the main content (full width)
- Include at least 3 items in the sidebar as a list of links
- Include a mock article in the main area with an `<h1>`, two `<p>` tags, and an image

---

### Challenge 4: HTML Form with Validation

Create `form.html`. Build a user registration form with:
- Name (text, required)
- Email (email type, required)
- Password (password type, minlength="8", required)
- Date of Birth (date type)
- Preferred Language (dropdown: English, Spanish, French, Other)
- Agreement to Terms (checkbox, required)
- Submit button

Requirements:
- Use `<label>` elements linked to each input with `for`/`id`
- Use a `<fieldset>` with `<legend>` to group the form
- Style the form so it looks clean and centered
- Add a short `<script>` that listens to the submit event and prevents default, then shows an alert: "Account created for [name]!"

---

### Challenge 5: DOM Manipulation with JavaScript

Create `interactive.html`. Build a simple interactive to-do list:

HTML structure:
- An `<input>` field to type a task
- An "Add" button
- An empty `<ul>` to hold tasks

JavaScript behavior:
- When "Add" is clicked (or Enter is pressed), add the typed text as a new `<li>` to the list
- Each `<li>` should have a "Done" button that crosses out the text when clicked
- The input should clear after adding a task
- If the input is empty, don't add anything (no empty tasks)

Style it with basic CSS so it looks presentable.

---

## Completion Checklist

Check each item once complete:

- [ ] I can explain how a browser resolves `blog.example.com` to an IP and renders the page
- [ ] I understand the critical rendering path and what render-blocking means
- [ ] I can write valid, semantic HTML with proper structure
- [ ] I understand CSS selectors, specificity, and the cascade
- [ ] I can use the box model to control spacing and layout
- [ ] I can create a layout using Flexbox and CSS Grid
- [ ] I can write a media query for responsive design
- [ ] I can add JavaScript to a page and respond to button clicks
- [ ] I understand what HTML5 and CSS3 added to the web platform
- [ ] I completed all five code challenges

---

*Ready for the next section? → [Section 4: JavaScript Fundamentals](../section-04-javascript-fundamentals/01-devtools-and-the-console.md)*

*Review sources: [10 — Sources](10-sources.md)*
