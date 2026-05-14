# 04 — State and Props: How Data Flows in React

## Learning Objectives

By the end of this page, you will be able to:

- Define props and write correctly typed prop interfaces
- Pass and receive props including functions and children
- Define state with `useState` and update it correctly
- Explain the difference between props and state
- Update state immutably (arrays and objects)
- Lift state up when multiple components need shared data
- Build controlled form inputs

---

## Props: Data from Parent to Child

**Props** (short for "properties") are the inputs a component receives from its parent. They're immutable from the child's perspective — a child reads props but never modifies them.

### Defining and Using Props

```tsx
// Define a TypeScript interface for props:
interface ButtonProps {
  label: string
  onClick: () => void
  variant?: "primary" | "secondary" | "danger"
  disabled?: boolean
}

// Destructure props in the function signature:
function Button({ label, onClick, variant = "primary", disabled = false }: ButtonProps) {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {label}
    </button>
  )
}

// Usage:
<Button label="Save" onClick={() => console.log("saved")} />
<Button label="Delete" onClick={handleDelete} variant="danger" />
<Button label="Loading..." onClick={() => {}} disabled />
```

### The `children` Prop

Any JSX placed between a component's opening and closing tags is passed as `children`:

```tsx
interface CardProps {
  title: string
  children: React.ReactNode    // anything renderable
}

function Card({ title, children }: CardProps) {
  return (
    <div className="card">
      <h2 className="card-title">{title}</h2>
      <div className="card-body">{children}</div>
    </div>
  )
}

// Usage:
<Card title="User Profile">
  <img src={user.avatar} alt={user.name} />
  <p>{user.bio}</p>
  <Button label="Follow" onClick={handleFollow} />
</Card>
```

### Passing Functions as Props (Callbacks)

This is how children communicate up to parents:

```tsx
interface SearchBarProps {
  onSearch: (query: string) => void
  placeholder?: string
}

function SearchBar({ onSearch, placeholder = "Search..." }: SearchBarProps) {
  return (
    <input
      type="search"
      placeholder={placeholder}
      onChange={e => onSearch(e.target.value)}
      className="search-input"
    />
  )
}

// Parent:
function App() {
  const handleSearch = (query: string) => {
    console.log("Searching for:", query)
  }

  return <SearchBar onSearch={handleSearch} />
}
```

---

## State: Data That Changes

**State** is data that belongs to a component and can change over time. When state changes, React re-renders the component to reflect the new state.

### `useState` Hook

```tsx
import { useState } from "react"

const [stateValue, setStateFunction] = useState(initialValue)
```

- `stateValue` — the current value
- `setStateFunction` — a function to update the value
- `initialValue` — what the state starts as (only used on first render)

```tsx
function Counter() {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  )
}
```

### TypeScript with `useState`

TypeScript infers the type from the initial value:

```tsx
const [count, setCount] = useState(0)           // number
const [name, setName] = useState("")            // string
const [isOpen, setIsOpen] = useState(false)     // boolean

// Explicit type when inference isn't enough:
const [user, setUser] = useState<User | null>(null)
const [items, setItems] = useState<string[]>([])
const [status, setStatus] = useState<"idle" | "loading" | "error">("idle")
```

---

## Props vs State

| | Props | State |
|---|-------|-------|
| Defined by | Parent | The component itself |
| Mutable? | No (immutable from child) | Yes (via setter) |
| Who owns it? | Parent | This component |
| Changes trigger re-render? | Yes (when parent re-renders) | Yes (when setter called) |
| Purpose | Configure the component | Track changing data |

> **Rule of thumb:** If the data comes from outside the component, it's a prop. If it's internal data the component tracks and changes, it's state.

---

## Updating State Correctly

### Never Mutate State Directly

React detects state changes by comparing the new value to the old reference. Mutating the existing value won't trigger a re-render:

```tsx
// ❌ WRONG — mutates the array directly, React doesn't see a change
const addItem = (item: string) => {
  items.push(item)      // mutates the original array
  setItems(items)       // same reference — React ignores it
}

// ✅ CORRECT — create a new array
const addItem = (item: string) => {
  setItems([...items, item])         // spread creates a new array
  // or: setItems(prev => [...prev, item])
}
```

### Immutable Updates for Arrays

```tsx
const [tasks, setTasks] = useState<Task[]>([])

// Add:
setTasks(prev => [...prev, newTask])

// Remove:
setTasks(prev => prev.filter(t => t.id !== id))

// Update one item:
setTasks(prev =>
  prev.map(t => t.id === id ? { ...t, completed: true } : t)
)

// Sort (sort mutates, so copy first):
setTasks(prev => [...prev].sort((a, b) => a.title.localeCompare(b.title)))
```

### Immutable Updates for Objects

```tsx
const [user, setUser] = useState<User>({ name: "Alex", age: 25, email: "" })

// Update one field:
setUser(prev => ({ ...prev, email: "alex@example.com" }))

// Update nested:
setUser(prev => ({
  ...prev,
  address: {
    ...prev.address,
    city: "New York"
  }
}))
```

### The Updater Function Pattern

When new state depends on previous state, use the **updater function** form — it receives the guaranteed-current state:

```tsx
// ❌ Stale closure risk — count might be stale if multiple updates batch:
setCount(count + 1)

// ✅ Updater function — always gets the current value:
setCount(prev => prev + 1)

// Batching example — both increments apply:
const increment3 = () => {
  setCount(prev => prev + 1)
  setCount(prev => prev + 1)
  setCount(prev => prev + 1)   // count goes up by 3, not 1
}
```

---

## Lifting State Up

When two sibling components need to share the same data, **lift the state up** to their common ancestor and pass it down via props:

```
Before: Each component has its own state (they can't share)
        TemperatureInput (°C) ←── celsius state
        TemperatureInput (°F) ←── fahrenheit state

After: State lives in parent; passed down as props
        Calculator ←── temp state + handlers
         ├── TemperatureInput (°C) ← prop: value, onChange
         └── TemperatureInput (°F) ← prop: value, onChange
```

```tsx
function TemperatureConverter() {
  const [celsius, setCelsius] = useState(0)
  const fahrenheit = celsius * (9 / 5) + 32

  return (
    <div>
      <label>
        Celsius:
        <input
          type="number"
          value={celsius}
          onChange={e => setCelsius(Number(e.target.value))}
        />
      </label>
      <label>
        Fahrenheit:
        <input
          type="number"
          value={fahrenheit}
          onChange={e => setCelsius((Number(e.target.value) - 32) * (5 / 9))}
        />
      </label>
      <p>{celsius}°C = {fahrenheit.toFixed(1)}°F</p>
    </div>
  )
}
```

---

## Controlled Components (Forms)

In React, form inputs should be **controlled** — their value is driven by React state, not the DOM:

```tsx
function LoginForm() {
  const [email, setEmail]       = useState("")
  const [password, setPassword] = useState("")
  const [error, setError]       = useState<string | null>(null)

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    if (!email.includes("@")) {
      setError("Please enter a valid email")
      return
    }
    setError(null)
    console.log("Submitting:", { email, password })
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          value={email}                              // controlled: value = state
          onChange={e => setEmail(e.target.value)}   // update state on change
        />
      </div>
      <div>
        <label htmlFor="password">Password</label>
        <input
          id="password"
          type="password"
          value={password}
          onChange={e => setPassword(e.target.value)}
        />
      </div>
      {error && <p className="error">{error}</p>}
      <button type="submit">Log In</button>
    </form>
  )
}
```

With a controlled input:
- `value={email}` — React owns the displayed value
- `onChange` — updates state when the user types
- The input is always in sync with state — no need to read `.value` manually

---

## Derived State

Sometimes what looks like state is just a computed value. Don't store derived values in state — derive them from existing state:

```tsx
// ❌ Redundant state — fullName is derived from firstName + lastName
const [firstName, setFirstName] = useState("")
const [lastName, setLastName]   = useState("")
const [fullName, setFullName]   = useState("")   // unnecessary!

// On firstName change, also update fullName — error-prone

// ✅ Derive it:
const [firstName, setFirstName] = useState("")
const [lastName, setLastName]   = useState("")
const fullName = `${firstName} ${lastName}`.trim()   // computed on every render, always correct
```

---

## Check Your Understanding

1. What is the difference between props and state?
2. Why must you never directly mutate state? What should you do instead?
3. What does "controlled component" mean? Why is it preferred?
4. When should you lift state up? Give an example.
5. What is the updater function form of `setState` and when should you use it?

---

*Previous: [03 — JSX](03-jsx.md)*
*Next: [05 — React Hooks](05-hooks.md)*
