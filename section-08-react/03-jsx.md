# 03 — JSX: HTML Inside TypeScript

## Learning Objectives

By the end of this page, you will be able to:

- Explain what JSX is and how it compiles
- Write JSX following all its rules
- Embed JavaScript expressions inside JSX
- Render lists with `.map()` and use `key` props correctly
- Use conditional rendering patterns
- Work with JSX Fragments
- Understand TypeScript types for JSX

---

## What Is JSX?

**JSX** (JavaScript XML) is a syntax extension that lets you write HTML-like markup directly inside JavaScript/TypeScript. It's what makes React components readable.

```tsx
// This is JSX — it looks like HTML but it's TypeScript:
const element = <h1 className="title">Hello, World!</h1>
```

JSX is **not HTML**. It's **not a string**. It's syntactic sugar that Vite/TypeScript transforms into `React.createElement()` calls:

```tsx
// JSX:
const element = <h1 className="title">Hello</h1>

// Compiles to:
const element = React.createElement("h1", { className: "title" }, "Hello")
```

You never call `React.createElement` directly — JSX handles it. But understanding this transform explains why JSX behaves the way it does.

---

## JSX Rules (Different from HTML)

### 1. Return a Single Root Element

Every component must return one root element. Wrap multiple elements in a `<div>`, `<section>`, or a **Fragment**:

```tsx
// ❌ Two root elements — invalid
return (
  <h1>Title</h1>
  <p>Subtitle</p>
)

// ✅ Wrapped in a div
return (
  <div>
    <h1>Title</h1>
    <p>Subtitle</p>
  </div>
)

// ✅ Wrapped in a Fragment (no extra DOM element)
return (
  <>
    <h1>Title</h1>
    <p>Subtitle</p>
  </>
)
```

### 2. Close All Tags

JSX requires every tag to be explicitly closed — including self-closing HTML elements:

```tsx
// ❌ HTML allows these unclosed — JSX does not
<br>
<img src="photo.jpg">
<input type="text">

// ✅ JSX requires closing or self-closing
<br />
<img src="photo.jpg" />
<input type="text" />
```

### 3. `className` Instead of `class`

Because `class` is a reserved word in JavaScript, JSX uses `className`:

```tsx
// ❌ HTML attribute
<div class="card">

// ✅ JSX attribute
<div className="card">
```

### 4. `htmlFor` Instead of `for`

`for` is also a reserved word:

```tsx
// ❌
<label for="email">Email</label>

// ✅
<label htmlFor="email">Email</label>
```

### 5. CamelCase Attributes

Most HTML attributes become camelCase in JSX:

| HTML | JSX |
|------|-----|
| `onclick` | `onClick` |
| `onchange` | `onChange` |
| `tabindex` | `tabIndex` |
| `readonly` | `readOnly` |
| `maxlength` | `maxLength` |
| `autocomplete` | `autoComplete` |
| `stroke-width` | `strokeWidth` |

Exception: `data-*` and `aria-*` attributes keep their hyphenated form.

### 6. Comments in JSX

```tsx
return (
  <div>
    {/* This is a JSX comment */}
    <p>Content</p>
    {/* Multi-line
        comment */}
  </div>
)
```

---

## Embedding Expressions

Use `{}` to embed any JavaScript expression inside JSX:

```tsx
const name = "Alex"
const score = 98
const isLoggedIn = true

return (
  <div>
    <h1>Hello, {name}!</h1>
    <p>Score: {score}</p>
    <p>Grade: {score >= 90 ? "A" : "B"}</p>
    <p>Status: {isLoggedIn ? "Logged in" : "Guest"}</p>
    <p>Today: {new Date().toLocaleDateString()}</p>
    <p>Math: {2 + 2}</p>
    <img src={`/avatars/${name.toLowerCase()}.jpg`} alt={name} />
  </div>
)
```

**What can go in `{}`:**
- Variables, expressions, function calls
- Ternary operators
- Template literals
- Array methods like `.map()`

**What cannot go in `{}`:**
- Statements (`if`, `for`, `while`) — use expressions or move logic outside the JSX
- Object literals without a wrapper (e.g., `style={{ color: "red" }}` uses double braces — outer `{}` is JSX expression, inner `{}` is the JavaScript object)

---

## Conditional Rendering

### Ternary Operator — Show One Thing or Another

```tsx
function Alert({ type, message }: { type: "success" | "error"; message: string }) {
  return (
    <div className={type === "success" ? "alert-success" : "alert-error"}>
      {type === "success" ? "✓" : "✗"} {message}
    </div>
  )
}
```

### Logical AND — Show Something or Nothing

```tsx
function UserGreeting({ isLoggedIn, username }: { isLoggedIn: boolean; username?: string }) {
  return (
    <header>
      <h1>My App</h1>
      {isLoggedIn && <p>Welcome back, {username}!</p>}
      {!isLoggedIn && <a href="/login">Sign in</a>}
    </header>
  )
}
```

> **Gotcha:** `{count && <Component />}` renders `0` if `count` is `0` (0 is falsy but renders as a number). Use `{count > 0 && <Component />}` or `{!!count && <Component />}`.

### Early Return — For Complex Conditions

```tsx
function UserCard({ user }: { user: User | null }) {
  if (!user) {
    return <p className="empty">No user found.</p>
  }

  return (
    <div className="user-card">
      <img src={user.avatar} alt={user.name} />
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  )
}
```

---

## List Rendering with `.map()`

```tsx
interface Product {
  id: number
  name: string
  price: number
}

function ProductList({ products }: { products: Product[] }) {
  if (products.length === 0) {
    return <p>No products available.</p>
  }

  return (
    <ul className="product-list">
      {products.map(product => (
        <li key={product.id} className="product-item">
          <span>{product.name}</span>
          <strong>${product.price.toFixed(2)}</strong>
        </li>
      ))}
    </ul>
  )
}
```

### The `key` Prop

React needs a **unique `key`** on each item in a list to efficiently track changes:

```tsx
{items.map(item => (
  <li key={item.id}>{item.name}</li>   // ✅ stable unique ID
))}

{items.map((item, index) => (
  <li key={index}>{item.name}</li>    // ⚠️ use index only if list never reorders/inserts
))}
```

**Why `key` matters:** React uses keys to match elements between renders. Without stable keys, React may re-render the wrong elements, causing bugs with input focus, animations, or component state.

**Rules for keys:**
- Must be unique among siblings (not globally unique)
- Must be stable — don't use `Math.random()` as a key
- Prefer database IDs; use index only as a last resort for static, never-reordered lists

---

## Fragments

Fragments let you return multiple elements without adding an extra DOM node:

```tsx
// Short syntax — most common
function TableRow({ name, value }: { name: string; value: string }) {
  return (
    <>
      <dt>{name}</dt>
      <dd>{value}</dd>
    </>
  )
}

// Long syntax — needed when you need to pass a key prop to a fragment
{items.map(item => (
  <React.Fragment key={item.id}>
    <dt>{item.name}</dt>
    <dd>{item.value}</dd>
  </React.Fragment>
))}
```

---

## TypeScript Types for JSX

```tsx
import type { ReactNode, ReactElement, FC } from "react"

// ReactNode — anything renderable: JSX, string, number, null, arrays...
interface ContainerProps {
  children: ReactNode
  className?: string
}

// FC (FunctionComponent) — type for a component function
const Container: FC<ContainerProps> = ({ children, className }) => (
  <div className={className}>{children}</div>
)

// JSX.Element / ReactElement — specifically JSX expressions
function wrap(element: ReactElement): ReactElement {
  return <div className="wrapper">{element}</div>
}
```

**Common event types:**

```tsx
// Mouse events
onClick:    (e: React.MouseEvent<HTMLButtonElement>) => void
onMouseOver: (e: React.MouseEvent<HTMLDivElement>) => void

// Keyboard events
onKeyDown: (e: React.KeyboardEvent<HTMLInputElement>) => void

// Form events
onChange:  (e: React.ChangeEvent<HTMLInputElement>) => void
onSubmit:  (e: React.FormEvent<HTMLFormElement>) => void

// Focus events
onFocus:   (e: React.FocusEvent<HTMLInputElement>) => void
onBlur:    (e: React.FocusEvent<HTMLInputElement>) => void
```

---

## A Complete Component Example

```tsx
// src/components/TaskList.tsx
import type { FC } from "react"

interface Task {
  id: number
  title: string
  completed: boolean
  priority: "low" | "medium" | "high"
}

interface TaskListProps {
  tasks: Task[]
  onToggle: (id: number) => void
  onDelete: (id: number) => void
}

const PRIORITY_COLORS: Record<Task["priority"], string> = {
  low: "#10b981",
  medium: "#f59e0b",
  high: "#ef4444"
}

const TaskList: FC<TaskListProps> = ({ tasks, onToggle, onDelete }) => {
  if (tasks.length === 0) {
    return <p className="empty-state">No tasks yet. Add one above!</p>
  }

  return (
    <ul className="task-list">
      {tasks.map(task => (
        <li
          key={task.id}
          className={`task-item ${task.completed ? "completed" : ""}`}
        >
          <span
            className="priority-dot"
            style={{ backgroundColor: PRIORITY_COLORS[task.priority] }}
            aria-label={`${task.priority} priority`}
          />
          <span
            className="task-title"
            onClick={() => onToggle(task.id)}
          >
            {task.title}
          </span>
          <button
            className="btn-delete"
            onClick={() => onDelete(task.id)}
            aria-label={`Delete ${task.title}`}
          >
            ✕
          </button>
        </li>
      ))}
    </ul>
  )
}

export default TaskList
```

---

## Check Your Understanding

1. What does JSX compile to? Why does this explain why JSX needs a single root element?
2. Name three HTML attributes that change their name in JSX and explain why.
3. Why are `key` props necessary on list items? What problem does React have without them?
4. What is the `&&` pattern in JSX, and what is the gotcha with falsy numbers?
5. What is the difference between `ReactNode` and `ReactElement`?

---

*Previous: [02 — What Is React?](02-what-is-react.md)*
*Next: [04 — State and Props](04-state-and-props.md)*
