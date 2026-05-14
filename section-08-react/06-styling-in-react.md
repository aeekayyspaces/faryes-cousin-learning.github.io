# 06 — Styling in React

## Learning Objectives

By the end of this page, you will be able to:

- Apply inline styles with the `style` prop
- Create and use CSS Modules for scoped styling
- Use global CSS for base/reset styles
- Conditionally apply class names
- Apply responsive and dynamic styles
- Understand the landscape of styling approaches in React

---

## The Styling Landscape

React has no built-in styling opinion — many approaches exist:

| Approach | Best For | Tradeoffs |
|----------|---------|-----------|
| **Global CSS** | Resets, base styles | No scoping — class names can collide |
| **CSS Modules** | Component styles | Scoped, no conflicts, co-located with component |
| **Inline styles** | Truly dynamic styles (from JS values) | No pseudo-classes, no media queries |
| **CSS-in-JS** (styled-components) | Large teams, design systems | Runtime cost, larger bundle |
| **Tailwind CSS** | Rapid UI, utility-first | HTML becomes verbose; excellent for some teams |

For this section we'll focus on **CSS Modules** + **global CSS** — the approach used in the calculator project, and a solid foundation for real applications.

---

## Global CSS

`src/index.css` is imported in `main.tsx` and applies to the entire application. Use it for:
- CSS reset / normalize
- CSS custom properties (variables)
- Base typography
- Utility classes used everywhere

```css
/* src/index.css */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

:root {
  --color-primary: #7c3aed;
  --color-primary-dark: #6d28d9;
  --color-surface: #ffffff;
  --color-background: #f4f4f8;
  --color-text: #111827;
  --color-text-muted: #6b7280;
  --radius: 8px;
  --shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  --font-sans: 'Segoe UI', system-ui, Arial, sans-serif;
}

body {
  font-family: var(--font-sans);
  background-color: var(--color-background);
  color: var(--color-text);
  line-height: 1.6;
}

button { cursor: pointer; }
a { color: var(--color-primary); }
```

---

## CSS Modules

**CSS Modules** transform class names at build time so they're unique to each component. No naming conflicts, even if two components use the same class name.

### Creating a CSS Module

Create a file named `ComponentName.module.css`:

```css
/* src/components/Card.module.css */
.card {
  background: var(--color-surface);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  padding: 1.5rem;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.card:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.12);
}

.title {
  font-size: 1.25rem;
  font-weight: 700;
  margin-bottom: 0.75rem;
  color: var(--color-text);
}

.body {
  color: var(--color-text-muted);
  font-size: 0.9rem;
  line-height: 1.7;
}

.featured {
  border: 2px solid var(--color-primary);
}
```

### Using the Module

```tsx
// src/components/Card.tsx
import styles from "./Card.module.css"

interface CardProps {
  title: string
  children: React.ReactNode
  featured?: boolean
}

function Card({ title, children, featured = false }: CardProps) {
  return (
    <div className={`${styles.card} ${featured ? styles.featured : ""}`}>
      <h2 className={styles.title}>{title}</h2>
      <div className={styles.body}>{children}</div>
    </div>
  )
}
```

The generated class name in the browser is something like `Card_card__3xN2p` — unique, never conflicts.

### `clsx` — Conditional Class Names

The `clsx` utility (or the built-in `classnames` package) makes conditional classes cleaner:

```bash
npm install clsx
```

```tsx
import clsx from "clsx"
import styles from "./Button.module.css"

interface ButtonProps {
  variant?: "primary" | "secondary" | "danger"
  size?: "sm" | "md" | "lg"
  disabled?: boolean
  fullWidth?: boolean
  children: React.ReactNode
  onClick?: () => void
}

function Button({
  variant = "primary",
  size = "md",
  disabled = false,
  fullWidth = false,
  children,
  onClick
}: ButtonProps) {
  return (
    <button
      className={clsx(
        styles.button,
        styles[variant],          // styles.primary, styles.secondary, etc.
        styles[size],             // styles.sm, styles.md, etc.
        fullWidth && styles.fullWidth,
        disabled && styles.disabled
      )}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  )
}
```

---

## Inline Styles

Use the `style` prop for styles that depend on JavaScript values — not as a general styling approach:

```tsx
// ✅ Good use — value comes from data/state
function ProgressBar({ value }: { value: number }) {
  return (
    <div className={styles.track}>
      <div
        className={styles.fill}
        style={{ width: `${value}%` }}              // JS value
        role="progressbar"
        aria-valuenow={value}
        aria-valuemin={0}
        aria-valuemax={100}
      />
    </div>
  )
}

// ✅ Good use — color from data
function PriorityDot({ color }: { color: string }) {
  return (
    <span
      className={styles.dot}
      style={{ backgroundColor: color }}
    />
  )
}

// ❌ Bad use — should be in a CSS file
<button style={{ backgroundColor: "#7c3aed", color: "white", padding: "8px 16px" }}>
  Click me
</button>
```

**Inline style syntax:**
- Uses a JavaScript object, not a string
- Properties are camelCase: `backgroundColor`, `fontSize`, `marginTop`
- Values are strings (or numbers for unitless values): `"16px"`, `"1rem"`, `2` (for z-index)

---

## Dynamic Theming with CSS Variables

CSS variables set in `:root` can be overridden in JavaScript to implement themes:

```tsx
function ThemeProvider({ theme }: { theme: "light" | "dark" }) {
  useEffect(() => {
    const root = document.documentElement
    if (theme === "dark") {
      root.style.setProperty("--color-background", "#1a1a2e")
      root.style.setProperty("--color-surface", "#16213e")
      root.style.setProperty("--color-text", "#e0e0ff")
    } else {
      root.style.removeProperty("--color-background")
      root.style.removeProperty("--color-surface")
      root.style.removeProperty("--color-text")
    }
  }, [theme])

  return null
}
```

Or use a `data-theme` attribute and define variables in CSS:

```css
:root { --bg: #f4f4f8; --text: #111827; }
[data-theme="dark"] { --bg: #1a1a2e; --text: #e0e0ff; }
```

```tsx
useEffect(() => {
  document.documentElement.dataset.theme = theme
}, [theme])
```

---

## Responsive Design in React

Apply standard CSS media queries inside CSS Modules:

```css
/* Card.module.css */
.grid {
  display: grid;
  grid-template-columns: 1fr;     /* mobile: single column */
  gap: 1rem;
}

@media (min-width: 640px) {
  .grid { grid-template-columns: repeat(2, 1fr); }
}

@media (min-width: 1024px) {
  .grid { grid-template-columns: repeat(3, 1fr); }
}
```

For complex responsive logic in components, use the `useWindowSize` custom hook from page 05.

---

## Tailwind CSS (Brief Overview)

**Tailwind CSS** is a utility-first CSS framework — you apply classes directly in JSX:

```tsx
// Tailwind — no separate CSS file needed
function Card({ title }: { title: string }) {
  return (
    <div className="bg-white rounded-lg shadow-md p-6 hover:-translate-y-1 transition-transform">
      <h2 className="text-xl font-bold text-gray-900 mb-3">{title}</h2>
    </div>
  )
}
```

Popular for speed of development. Not used in this course's project (we use CSS Modules), but worth knowing it exists.

---

## Check Your Understanding

1. What problem do CSS Modules solve that plain CSS does not?
2. When is inline `style={}` the right choice vs. putting styles in a `.module.css` file?
3. What does `clsx` do? Show how you'd conditionally apply an `active` class.
4. What is the double `{{}}` in `style={{ color: "red" }}`?
5. How do you implement dark mode in React using CSS custom properties?

---

*Previous: [05 — Hooks](05-hooks.md)*
*Next: [07 — Project: Calculator App](07-project-calculator.md)*
