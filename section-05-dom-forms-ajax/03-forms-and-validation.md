# 03 — Forms and Validation: Capturing and Verifying User Input

## Learning Objectives

By the end of this page, you will be able to:

- Capture form data with `FormData` and convert it to a plain object
- Use the Constraint Validation API for HTML5 built-in validation
- Build a custom real-time validation system with clear user feedback
- Handle file inputs and read file contents
- Build a multi-step form wizard
- Write accessible form markup with proper ARIA attributes
- Prevent double submissions and manage form state

---

## The `FormData` API

`FormData` is the most convenient way to read all fields from a form at once:

```javascript
const form = document.querySelector("#signup-form")

form.addEventListener("submit", e => {
  e.preventDefault()

  const fd = new FormData(form)

  // Read individual values:
  const username = fd.get("username")
  const role     = fd.get("role")

  // Read all values for a multi-select or checkbox group:
  const interests = fd.getAll("interests")   // returns an array

  // Convert to a plain object:
  const data = Object.fromEntries(fd)
  // { username: "alex", email: "a@b.com", role: "developer" }

  console.log(data)
})
```

### `FormData` with File Uploads

`FormData` handles file inputs automatically — the file binary data is included when you send it via `fetch`:

```javascript
const fd = new FormData(form)   // includes files from <input type="file">

await fetch("/api/upload", {
  method: "POST",
  body: fd   // don't set Content-Type — fetch sets it automatically with boundary
})
```

### Building `FormData` Manually

```javascript
const fd = new FormData()
fd.append("username", "alex")
fd.append("avatar", fileInput.files[0], "avatar.jpg")
fd.append("tags", "javascript")
fd.append("tags", "css")   // append multiple values for same key
```

---

## HTML5 Constraint Validation API

Modern browsers have built-in form validation. Using it properly means less JavaScript:

```html
<form id="reg-form" novalidate>   <!-- novalidate = disable browser UI, keep JS API -->
  <input type="email"    name="email"    required>
  <input type="password" name="password" minlength="8" required>
  <input type="number"   name="age"      min="18" max="120" required>
  <input type="url"      name="website"  pattern="https://.*">
  <input type="text"     name="zipcode"  pattern="[0-9]{5}">
</form>
```

### Checking Validity

```javascript
const emailInput = document.querySelector("[name='email']")

emailInput.validity.valid        // true if all constraints pass
emailInput.validity.valueMissing // true if required and empty
emailInput.validity.typeMismatch // true if type="email" but not a valid email
emailInput.validity.tooShort     // true if minlength not met
emailInput.validity.tooLong      // true if maxlength exceeded
emailInput.validity.rangeUnderflow  // true if below min
emailInput.validity.rangeOverflow   // true if above max
emailInput.validity.patternMismatch // true if pattern doesn't match
emailInput.validity.customError     // true if a custom error is set

emailInput.validationMessage    // browser's default error message
emailInput.checkValidity()      // returns true/false
```

### Custom Error Messages with `setCustomValidity`

Override the browser's generic messages with helpful, specific ones:

```javascript
const passwordInput = document.querySelector("[name='password']")
const confirmInput  = document.querySelector("[name='confirm']")

function checkPasswordMatch() {
  if (passwordInput.value !== confirmInput.value) {
    confirmInput.setCustomValidity("Passwords do not match")
  } else {
    confirmInput.setCustomValidity("")   // clear the error
  }
}

passwordInput.addEventListener("input", checkPasswordMatch)
confirmInput.addEventListener("input", checkPasswordMatch)
```

---

## Real-Time Validation System

Validation that waits until submit is frustrating. Show errors as soon as the user leaves a field (`blur` event) and clear them as the user fixes input (`input` event):

```html
<form id="contact-form" novalidate>
  <div class="field-group">
    <label for="name">Full Name</label>
    <input type="text" id="name" name="name" required minlength="2">
    <span class="field-error" aria-live="polite"></span>
  </div>

  <div class="field-group">
    <label for="email">Email Address</label>
    <input type="email" id="email" name="email" required>
    <span class="field-error" aria-live="polite"></span>
  </div>

  <div class="field-group">
    <label for="message">Message</label>
    <textarea id="message" name="message" required minlength="10"></textarea>
    <span class="field-error" aria-live="polite"></span>
  </div>

  <button type="submit">Send Message</button>
</form>
```

```css
.field-group { display: flex; flex-direction: column; gap: 0.25rem; margin-bottom: 1rem; }
.field-error { font-size: 0.8rem; color: #dc2626; min-height: 1.2em; }
.field-group input.invalid, .field-group textarea.invalid {
  border-color: #dc2626;
  outline-color: #dc2626;
}
.field-group input.valid { border-color: #16a34a; }
```

```javascript
const validators = {
  name: value => {
    if (!value)             return "Name is required."
    if (value.length < 2)   return "Name must be at least 2 characters."
    return null
  },
  email: value => {
    if (!value)             return "Email is required."
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) return "Please enter a valid email."
    return null
  },
  message: value => {
    if (!value)             return "Message is required."
    if (value.length < 10)  return `Message too short (${value.length}/10 chars).`
    return null
  }
}

function validateField(input) {
  const name = input.name
  const error = validators[name]?.(input.value.trim()) ?? null

  const errorEl = input.closest(".field-group").querySelector(".field-error")

  if (error) {
    errorEl.textContent = error
    input.classList.add("invalid")
    input.classList.remove("valid")
    input.setAttribute("aria-invalid", "true")
    return false
  } else {
    errorEl.textContent = ""
    input.classList.remove("invalid")
    input.classList.add("valid")
    input.removeAttribute("aria-invalid")
    return true
  }
}

// Validate on blur (when user leaves the field)
document.querySelectorAll("#contact-form input, #contact-form textarea").forEach(input => {
  input.addEventListener("blur", () => validateField(input))
  // Clear error as user types to fix it
  input.addEventListener("input", () => {
    if (input.classList.contains("invalid")) validateField(input)
  })
})

// Validate all on submit
document.querySelector("#contact-form").addEventListener("submit", e => {
  e.preventDefault()

  const inputs = e.target.querySelectorAll("input, textarea")
  const allValid = Array.from(inputs).every(input => validateField(input))

  if (!allValid) {
    // Focus the first invalid input
    e.target.querySelector(".invalid")?.focus()
    return
  }

  submitForm(new FormData(e.target))
})
```

---

## Handling File Inputs

### Basic File Selection

```html
<input type="file" id="avatar" name="avatar" accept="image/*">
<input type="file" id="attachments" name="attachments" multiple accept=".pdf,.doc,.docx">
```

```javascript
const fileInput = document.querySelector("#avatar")

fileInput.addEventListener("change", e => {
  const files = Array.from(e.target.files)   // FileList → array

  for (const file of files) {
    console.log(file.name)     // "photo.jpg"
    console.log(file.size)     // bytes
    console.log(file.type)     // "image/jpeg"
    console.log(file.lastModified)  // timestamp
  }
})
```

### Image Preview Before Upload

```javascript
fileInput.addEventListener("change", e => {
  const file = e.target.files[0]
  if (!file || !file.type.startsWith("image/")) return

  const reader = new FileReader()
  reader.onload = (event) => {
    document.querySelector("#preview").src = event.target.result
  }
  reader.readAsDataURL(file)
})
```

### Drag and Drop File Upload

```html
<div id="drop-zone" class="drop-zone">
  <p>Drag files here, or <label for="file-input">click to browse</label></p>
  <input type="file" id="file-input" class="sr-only" multiple>
</div>
```

```javascript
const dropZone = document.querySelector("#drop-zone")
const fileInput = document.querySelector("#file-input")

// Prevent browser from opening the file
dropZone.addEventListener("dragover", e => {
  e.preventDefault()
  dropZone.classList.add("dragging")
})

dropZone.addEventListener("dragleave", () => {
  dropZone.classList.remove("dragging")
})

dropZone.addEventListener("drop", e => {
  e.preventDefault()
  dropZone.classList.remove("dragging")

  const files = Array.from(e.dataTransfer.files)
  handleFiles(files)
})

fileInput.addEventListener("change", e => {
  handleFiles(Array.from(e.target.files))
})

function handleFiles(files) {
  for (const file of files) {
    console.log(`Received: ${file.name} (${(file.size / 1024).toFixed(1)} KB)`)
  }
}
```

---

## Multi-Step Form Wizard

Long forms are intimidating. Break them into steps with a progress indicator:

```html
<form id="wizard" novalidate>

  <div class="steps-indicator">
    <span class="step active" data-step="1">Account</span>
    <span class="step" data-step="2">Profile</span>
    <span class="step" data-step="3">Confirm</span>
  </div>

  <!-- Step 1 -->
  <div class="step-content" data-step="1">
    <h2>Create Account</h2>
    <input type="email"    name="email"    placeholder="Email" required>
    <input type="password" name="password" placeholder="Password" minlength="8" required>
  </div>

  <!-- Step 2 -->
  <div class="step-content hidden" data-step="2">
    <h2>Your Profile</h2>
    <input type="text" name="displayName" placeholder="Display name" required>
    <select name="role">
      <option value="developer">Developer</option>
      <option value="designer">Designer</option>
    </select>
  </div>

  <!-- Step 3 -->
  <div class="step-content hidden" data-step="3">
    <h2>Review and Submit</h2>
    <div id="summary"></div>
  </div>

  <div class="wizard-nav">
    <button type="button" id="btn-back" class="hidden">Back</button>
    <button type="button" id="btn-next">Next</button>
    <button type="submit" id="btn-submit" class="hidden">Create Account</button>
  </div>
</form>
```

```javascript
let currentStep = 1
const totalSteps = 3
const formData = {}

function goToStep(step) {
  // Hide current, show next
  document.querySelector(`.step-content[data-step="${currentStep}"]`).classList.add("hidden")
  document.querySelector(`.step-content[data-step="${step}"]`).classList.remove("hidden")

  // Update indicator
  document.querySelectorAll(".step").forEach(el => {
    el.classList.toggle("active", Number(el.dataset.step) === step)
    el.classList.toggle("completed", Number(el.dataset.step) < step)
  })

  // Update nav buttons
  document.querySelector("#btn-back").classList.toggle("hidden", step === 1)
  document.querySelector("#btn-next").classList.toggle("hidden", step === totalSteps)
  document.querySelector("#btn-submit").classList.toggle("hidden", step !== totalSteps)

  // On last step, show summary
  if (step === totalSteps) renderSummary()

  currentStep = step
}

document.querySelector("#btn-next").addEventListener("click", () => {
  // Validate current step's fields before advancing
  const currentFields = document.querySelectorAll(
    `.step-content[data-step="${currentStep}"] input, .step-content[data-step="${currentStep}"] select`
  )
  const allValid = Array.from(currentFields).every(f => f.checkValidity())
  if (!allValid) {
    currentFields.forEach(f => f.reportValidity())
    return
  }

  // Collect data from current step
  currentFields.forEach(f => { if (f.name) formData[f.name] = f.value })

  goToStep(currentStep + 1)
})

document.querySelector("#btn-back").addEventListener("click", () => {
  goToStep(currentStep - 1)
})

function renderSummary() {
  document.querySelector("#summary").innerHTML = Object.entries(formData)
    .map(([key, val]) => `<p><strong>${key}:</strong> ${escapeHTML(String(val))}</p>`)
    .join("")
}

document.querySelector("#wizard").addEventListener("submit", async e => {
  e.preventDefault()
  // Submit formData to server
  console.log("Submitting:", formData)
})
```

---

## Preventing Double Submissions

A user clicking Submit twice before the server responds can create duplicate records. Always disable the button during submission:

```javascript
async function submitForm(data) {
  const submitBtn = document.querySelector("[type='submit']")

  // Disable and show loading state
  submitBtn.disabled = true
  submitBtn.textContent = "Sending..."

  try {
    const response = await fetch("/api/contact", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(data)
    })

    if (!response.ok) throw new Error(`Server error: ${response.status}`)

    showSuccess("Message sent!")
    form.reset()
  } catch (error) {
    showError(error.message)
  } finally {
    // Always re-enable, whether success or failure
    submitBtn.disabled = false
    submitBtn.textContent = "Send Message"
  }
}
```

---

## Accessible Form Patterns

### Associate Labels with Inputs

```html
<!-- Method 1: for/id -->
<label for="email">Email Address</label>
<input type="email" id="email" name="email">

<!-- Method 2: wrap -->
<label>
  Email Address
  <input type="email" name="email">
</label>
```

### Required Field Indication

```html
<!-- Visual asterisk + aria-required for screen readers -->
<label for="name">Name <span aria-hidden="true">*</span></label>
<input type="text" id="name" name="name" required aria-required="true">
```

### Error Association with `aria-describedby`

```html
<label for="email">Email</label>
<input type="email" id="email" name="email"
       aria-describedby="email-error"
       aria-invalid="true">
<span id="email-error" class="error" role="alert">
  Please enter a valid email address.
</span>
```

`aria-describedby` links the input to its error message — screen readers announce the error when the user focuses the input. `aria-invalid="true"` signals to assistive technology that the field has an error. `role="alert"` causes screen readers to announce the error immediately when it appears.

---

## Check Your Understanding

1. What does `Object.fromEntries(new FormData(form))` give you, and when does it fall short?
2. What is the difference between using HTML5 built-in validation (`required`, `pattern`) and building your own JavaScript validation?
3. Why is it important to disable the submit button during form submission?
4. What does `aria-describedby` do on an input element?
5. What is `setCustomValidity("")` used for?

---

*Previous: [02 — Events Deep Dive](02-events-deep-dive.md)*
*Next: [04 — Fetch and AJAX](04-fetch-and-ajax.md)*
