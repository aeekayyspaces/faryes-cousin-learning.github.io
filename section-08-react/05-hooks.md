# 05 — React Hooks: useState, useEffect, useRef, and More

## Learning Objectives

By the end of this page, you will be able to:

- Use `useEffect` to synchronize React with the outside world
- Understand the dependency array and write correct dependencies
- Clean up side effects to prevent memory leaks
- Use `useRef` to access DOM elements and persist mutable values
- Use `useMemo` and `useCallback` for performance optimization
- Write a custom hook to reuse stateful logic

---

## What Are Hooks?

**Hooks** are functions that let you use React features inside function components. They all start with `use`.

React's built-in hooks:

| Hook | Purpose |
|------|---------|
| `useState` | Local state (covered in page 04) |
| `useEffect` | Side effects after render |
| `useRef` | Mutable ref / DOM access |
| `useMemo` | Memoize expensive calculations |
| `useCallback` | Memoize function references |
| `useContext` | Read context values |
| `useReducer` | Complex state logic |
| `useId` | Unique accessible IDs |

### Rules of Hooks

1. **Only call hooks at the top level** — not inside `if`, loops, or nested functions
2. **Only call hooks inside React function components** (or custom hooks)

```tsx
// ❌ Conditional hook — breaks the rules
if (condition) {
  const [value, setValue] = useState(0)
}

// ✅ Hook at top level, condition inside
const [value, setValue] = useState(0)
if (condition) {
  // use value here
}
```

---

## `useEffect` — Synchronizing with the Outside World

`useEffect` runs code **after** the component renders. Use it for:
- Fetching data from an API
- Setting up a subscription or event listener
- Updating the document title
- Integrating with non-React libraries

```tsx
import { useEffect } from "react"

useEffect(() => {
  // side effect code here
  return () => {
    // optional cleanup — runs before the next effect or unmount
  }
}, [/* dependency array */])
```

### The Dependency Array

The second argument controls when the effect runs:

```tsx
// No dependency array — runs after EVERY render (rarely needed)
useEffect(() => {
  console.log("Rendered!")
})

// Empty array — runs ONCE after the first render (mount)
useEffect(() => {
  console.log("Component mounted!")
}, [])

// With dependencies — runs after first render AND whenever deps change
useEffect(() => {
  console.log(`userId changed to: ${userId}`)
}, [userId])
```

> **The key rule:** Every reactive value used inside the effect (state, props, anything that can change) must be listed in the dependency array. ESLint's `react-hooks/exhaustive-deps` rule enforces this.

### Fetching Data

```tsx
import { useState, useEffect } from "react"

interface User {
  id: number
  name: string
  email: string
}

function UserProfile({ userId }: { userId: number }) {
  const [user, setUser]       = useState<User | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError]     = useState<string | null>(null)

  useEffect(() => {
    // Reset state when userId changes
    setLoading(true)
    setError(null)

    const controller = new AbortController()

    async function fetchUser() {
      try {
        const res = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`, {
          signal: controller.signal
        })
        if (!res.ok) throw new Error(`HTTP ${res.status}`)
        const data: User = await res.json()
        setUser(data)
      } catch (err) {
        if (err instanceof Error && err.name !== "AbortError") {
          setError(err.message)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchUser()

    // Cleanup — cancel fetch if userId changes before it completes
    return () => controller.abort()
  }, [userId])  // re-run whenever userId changes

  if (loading) return <p>Loading user {userId}...</p>
  if (error)   return <p className="error">Error: {error}</p>
  if (!user)   return null

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  )
}
```

### Document Title

```tsx
function PageTitle({ title }: { title: string }) {
  useEffect(() => {
    const previous = document.title
    document.title = `${title} | My App`
    return () => {
      document.title = previous  // restore when component unmounts
    }
  }, [title])

  return null  // this component renders nothing
}
```

### Event Listeners

```tsx
function KeyboardShortcuts({ onSave }: { onSave: () => void }) {
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if ((e.ctrlKey || e.metaKey) && e.key === "s") {
        e.preventDefault()
        onSave()
      }
    }

    window.addEventListener("keydown", handleKeyDown)
    return () => window.removeEventListener("keydown", handleKeyDown)  // cleanup!
  }, [onSave])  // onSave is a dependency

  return null
}
```

The cleanup function is critical here — without it, every time the component re-renders, a new listener gets added and the old one leaks.

---

## `useRef` — Mutable Values and DOM Access

`useRef` returns an object `{ current: value }` that:
- **Persists across renders** (unlike regular variables that reset)
- **Doesn't trigger re-renders when changed** (unlike state)

Two primary uses:

### 1. Accessing DOM Elements

```tsx
import { useRef, useEffect } from "react"

function SearchInput() {
  const inputRef = useRef<HTMLInputElement>(null)

  // Auto-focus on mount
  useEffect(() => {
    inputRef.current?.focus()
  }, [])

  const handleClear = () => {
    if (inputRef.current) {
      inputRef.current.value = ""
      inputRef.current.focus()
    }
  }

  return (
    <div>
      <input ref={inputRef} type="search" placeholder="Search..." />
      <button onClick={handleClear}>Clear</button>
    </div>
  )
}
```

The `ref` prop tells React to put the actual DOM element into `inputRef.current` after mounting.

### 2. Storing Mutable Values Without Re-rendering

```tsx
function Timer() {
  const [seconds, setSeconds] = useState(0)
  const intervalRef = useRef<ReturnType<typeof setInterval> | null>(null)

  const start = () => {
    if (intervalRef.current) return  // already running
    intervalRef.current = setInterval(() => {
      setSeconds(s => s + 1)
    }, 1000)
  }

  const stop = () => {
    if (intervalRef.current) {
      clearInterval(intervalRef.current)
      intervalRef.current = null
    }
  }

  // Clean up on unmount
  useEffect(() => () => stop(), [])

  return (
    <div>
      <p>{seconds}s</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </div>
  )
}
```

The interval ID is stored in a ref — we need it to cancel the interval, but it doesn't need to be displayed or cause re-renders.

### Tracking Previous Values

```tsx
function usePrevious<T>(value: T): T | undefined {
  const prevRef = useRef<T | undefined>(undefined)

  useEffect(() => {
    prevRef.current = value
  }, [value])

  return prevRef.current  // value from the previous render
}

// Usage:
function Counter() {
  const [count, setCount] = useState(0)
  const prevCount = usePrevious(count)

  return (
    <p>
      Current: {count} | Previous: {prevCount ?? "none"}
    </p>
  )
}
```

---

## `useMemo` — Memoizing Expensive Calculations

`useMemo` caches the result of an expensive calculation and only recalculates when dependencies change:

```tsx
import { useMemo } from "react"

function ProductList({ products, searchQuery }: {
  products: Product[]
  searchQuery: string
}) {
  // Only re-filter when products or searchQuery change
  const filteredProducts = useMemo(
    () => products.filter(p =>
      p.name.toLowerCase().includes(searchQuery.toLowerCase())
    ),
    [products, searchQuery]
  )

  return <ul>{filteredProducts.map(p => <li key={p.id}>{p.name}</li>)}</ul>
}
```

**When to use `useMemo`:**
- Expensive computations (sorting large arrays, complex filtering, heavy calculations)
- When the calculation runs every render and visibly slows things down

**Don't over-use it** — most calculations are fast enough that memoization adds more overhead than it saves.

---

## `useCallback` — Memoizing Functions

`useCallback` returns a stable function reference that only changes when its dependencies change. Useful when passing callbacks to memoized child components:

```tsx
import { useCallback } from "react"

function Parent() {
  const [count, setCount] = useState(0)
  const [items, setItems] = useState<string[]>([])

  // Without useCallback: new function on every Parent render → Child always re-renders
  // With useCallback: same function reference → Child skips unnecessary re-renders
  const handleDelete = useCallback((id: string) => {
    setItems(prev => prev.filter(item => item !== id))
  }, [])  // no deps — function never changes

  return (
    <>
      <p>Count: {count}</p>
      <button onClick={() => setCount(c => c + 1)}>+</button>
      <ItemList items={items} onDelete={handleDelete} />
    </>
  )
}
```

---

## Custom Hooks

A **custom hook** is a function starting with `use` that calls other hooks. It lets you extract and reuse stateful logic across components.

### `useLocalStorage`

```tsx
import { useState, useEffect } from "react"

function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    try {
      const stored = localStorage.getItem(key)
      return stored !== null ? (JSON.parse(stored) as T) : initialValue
    } catch {
      return initialValue
    }
  })

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value))
  }, [key, value])

  return [value, setValue] as const
}

// Usage — same API as useState, but persisted!
function ThemeToggle() {
  const [theme, setTheme] = useLocalStorage<"light" | "dark">("theme", "light")

  return (
    <button onClick={() => setTheme(t => t === "light" ? "dark" : "light")}>
      Currently: {theme}
    </button>
  )
}
```

### `useFetch`

```tsx
function useFetch<T>(url: string) {
  const [data, setData]     = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError]   = useState<Error | null>(null)

  useEffect(() => {
    const controller = new AbortController()
    setLoading(true)
    setError(null)

    fetch(url, { signal: controller.signal })
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`)
        return res.json() as Promise<T>
      })
      .then(setData)
      .catch(err => {
        if (err.name !== "AbortError") setError(err)
      })
      .finally(() => setLoading(false))

    return () => controller.abort()
  }, [url])

  return { data, loading, error }
}

// Usage:
function UserCard({ id }: { id: number }) {
  const { data: user, loading, error } = useFetch<User>(
    `https://jsonplaceholder.typicode.com/users/${id}`
  )

  if (loading) return <p>Loading...</p>
  if (error)   return <p>Error: {error.message}</p>
  if (!user)   return null
  return <h2>{user.name}</h2>
}
```

### `useWindowSize`

```tsx
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  })

  useEffect(() => {
    const handleResize = () => setSize({
      width: window.innerWidth,
      height: window.innerHeight
    })

    window.addEventListener("resize", handleResize)
    return () => window.removeEventListener("resize", handleResize)
  }, [])

  return size
}

// Usage:
function ResponsiveComponent() {
  const { width } = useWindowSize()
  return <p>{width < 768 ? "Mobile" : "Desktop"} view</p>
}
```

---

## Check Your Understanding

1. What are the three forms of the `useEffect` dependency array? What does each do?
2. What is the cleanup function in `useEffect` and why is it important?
3. How is `useRef` different from `useState`? When would you choose one over the other?
4. What does `useCallback` optimize? When does it actually help?
5. What makes a custom hook a "hook" rather than a regular utility function?

---

*Previous: [04 — State and Props](04-state-and-props.md)*
*Next: [06 — Styling in React](06-styling-in-react.md)*
