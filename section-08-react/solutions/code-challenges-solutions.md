# Solutions — Code Challenges

Reference solutions for [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md).

---

## Challenge 1: `CryptoTicker` with Polling

```tsx
// src/components/CryptoTicker.tsx
import { useState, useEffect, useRef } from "react"

interface PriceData {
  [coinId: string]: { usd: number }
}

interface CryptoTickerProps {
  coinId: string
  refreshInterval?: number
}

export function CryptoTicker({ coinId, refreshInterval = 30_000 }: CryptoTickerProps) {
  const [price, setPrice]     = useState<number | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError]     = useState<string | null>(null)
  const controllerRef = useRef<AbortController | null>(null)

  useEffect(() => {
    async function fetchPrice() {
      controllerRef.current?.abort()
      controllerRef.current = new AbortController()

      try {
        const url = `https://api.coingecko.com/api/v3/simple/price?ids=${coinId}&vs_currencies=usd`
        const res = await fetch(url, { signal: controllerRef.current.signal })
        if (!res.ok) throw new Error(`HTTP ${res.status}`)
        const data: PriceData = await res.json()
        setPrice(data[coinId]?.usd ?? null)
        setError(null)
      } catch (err) {
        if (err instanceof Error && err.name !== "AbortError") {
          setError(err.message)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchPrice()
    const interval = setInterval(fetchPrice, refreshInterval)

    return () => {
      clearInterval(interval)
      controllerRef.current?.abort()
    }
  }, [coinId, refreshInterval])   // re-run if coin changes

  if (loading) return <div className="skeleton" style={{ width: 200, height: 40 }} />

  if (error) return (
    <div style={{ color: "#ef4444" }}>
      Failed to load {coinId}: {error}
    </div>
  )

  return (
    <div>
      <strong style={{ textTransform: "capitalize" }}>{coinId}</strong>:{" "}
      {price !== null
        ? `$${price.toLocaleString("en-US", { minimumFractionDigits: 2 })}`
        : "Unavailable"
      }
    </div>
  )
}
```

**Key patterns demonstrated:**
- `useRef` for the `AbortController` (mutable, no re-render needed)
- Cleanup: cancels interval AND aborts in-flight fetch
- `coinId` in deps array: when coinId changes, old interval is cleared and new one starts

---

## Challenge 2: `useDebounce` + `LiveSearch`

```tsx
// src/hooks/useDebounce.ts
import { useState, useEffect } from "react"

export function useDebounce<T>(value: T, delay: number): T {
  const [debounced, setDebounced] = useState<T>(value)

  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay)
    return () => clearTimeout(timer)   // clear timer if value changes before delay
  }, [value, delay])

  return debounced
}
```

```tsx
// src/components/LiveSearch.tsx
import { useState, useEffect } from "react"
import { useDebounce } from "../hooks/useDebounce"

interface Country {
  name: { common: string }
  flags: { svg: string; png: string }
}

export function LiveSearch() {
  const [query, setQuery]         = useState("")
  const [results, setResults]     = useState<Country[]>([])
  const [loading, setLoading]     = useState(false)
  const [error, setError]         = useState<string | null>(null)
  const [notFound, setNotFound]   = useState(false)

  const debouncedQuery = useDebounce(query, 400)

  useEffect(() => {
    if (!debouncedQuery.trim()) {
      setResults([]); setError(null); setNotFound(false); return
    }

    const controller = new AbortController()
    setLoading(true); setError(null); setNotFound(false)

    fetch(
      `https://restcountries.com/v3.1/name/${encodeURIComponent(debouncedQuery)}?fields=name,flags`,
      { signal: controller.signal }
    )
      .then(res => {
        if (res.status === 404) { setNotFound(true); return [] }
        if (!res.ok) throw new Error(`HTTP ${res.status}`)
        return res.json() as Promise<Country[]>
      })
      .then(data => setResults(data))
      .catch(err => {
        if (err.name !== "AbortError") setError(err.message)
      })
      .finally(() => setLoading(false))

    return () => controller.abort()
  }, [debouncedQuery])

  return (
    <div style={{ maxWidth: 400, position: "relative" }}>
      <input
        type="search"
        value={query}
        onChange={e => setQuery(e.target.value)}
        placeholder="Search countries..."
        style={{ width: "100%", padding: "0.5rem" }}
      />
      {loading && <p>Searching...</p>}
      {notFound && <p>No results for "{debouncedQuery}"</p>}
      {error && <p style={{ color: "red" }}>Error: {error}</p>}
      {results.length > 0 && (
        <ul style={{ listStyle: "none", border: "1px solid #ccc", borderRadius: 4, padding: 0 }}>
          {results.slice(0, 8).map(c => (
            <li key={c.name.common} style={{ display: "flex", alignItems: "center", gap: 8, padding: "0.5rem" }}>
              <img src={c.flags.svg ?? c.flags.png} alt="" width={24} height={16} style={{ objectFit: "cover" }} />
              {c.name.common}
            </li>
          ))}
        </ul>
      )}
    </div>
  )
}
```

---

## Challenge 3: History Panel for the Calculator

```tsx
// src/components/HistoryPanel.tsx
import type { HistoryEntry } from "../calculator/Calculator"
import styles from "./HistoryPanel.module.css"

interface HistoryPanelProps {
  history: readonly HistoryEntry[]
  onClear: () => void
  onSelect: (result: string) => void
}

export function HistoryPanel({ history, onClear, onSelect }: HistoryPanelProps) {
  return (
    <div className={styles.panel}>
      <div className={styles.header}>
        <h2 className={styles.title}>History</h2>
        {history.length > 0 && (
          <button onClick={onClear} className={styles.clearBtn}>Clear</button>
        )}
      </div>
      {history.length === 0 ? (
        <p className={styles.empty}>No history yet</p>
      ) : (
        <ul className={styles.list}>
          {[...history].reverse().map((entry, i) => (
            <li key={i} className={styles.entry} onClick={() => onSelect(entry.result)}>
              <span className={styles.expression}>{entry.expression}</span>
              <span className={styles.result}>= {entry.result}</span>
            </li>
          ))}
        </ul>
      )}
    </div>
  )
}
```

```css
/* src/components/HistoryPanel.module.css */
.panel { background: var(--surface); border-radius: var(--radius); overflow: hidden; margin-top: 0.75rem; }
.header { display: flex; justify-content: space-between; align-items: center; padding: 0.75rem 1rem; border-bottom: 1px solid rgba(255,255,255,0.1); }
.title { font-size: 0.85rem; font-weight: 600; color: var(--text-muted); }
.clearBtn { background: none; border: none; color: var(--text-muted); font-size: 0.8rem; cursor: pointer; padding: 0.2rem 0.5rem; }
.clearBtn:hover { color: var(--text); }
.list { list-style: none; max-height: 200px; overflow-y: auto; }
.entry { display: flex; justify-content: space-between; padding: 0.5rem 1rem; cursor: pointer; font-size: 0.85rem; }
.entry:hover { background: rgba(255,255,255,0.05); }
.expression { color: var(--text-muted); }
.result { color: var(--text); font-weight: 600; }
.empty { color: var(--text-muted); text-align: center; padding: 1rem; font-size: 0.85rem; }
```

**In `App.tsx`, add:**
```tsx
const [showHistory, setShowHistory] = useState(false)

// In the state, add a selectedResult handler
const handleSelectHistory = (result: string) => {
  setState(prev => ({ ...prev, current: result, justEvaluated: true }))
}

// In JSX, below ButtonGrid:
<button onClick={() => setShowHistory(h => !h)} className={styles.historyToggle}>
  {showHistory ? "Hide History" : "History"}
</button>
{showHistory && (
  <HistoryPanel
    history={calc.getHistory()}
    onClear={() => { calc.clearHistory(); forceUpdate() }}
    onSelect={handleSelectHistory}
  />
)}
```

---

## Challenge 4: React + TypeScript Todo App

```tsx
// src/hooks/useLocalStorage.ts
import { useState, useEffect } from "react"

export function useLocalStorage<T>(key: string, initial: T) {
  const [value, setValue] = useState<T>(() => {
    try {
      const stored = localStorage.getItem(key)
      return stored ? (JSON.parse(stored) as T) : initial
    } catch { return initial }
  })

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value))
  }, [key, value])

  return [value, setValue] as const
}
```

```tsx
// src/TodoApp.tsx
import { useState } from "react"
import { useLocalStorage } from "./hooks/useLocalStorage"

type Priority = "low" | "medium" | "high"
type Filter = "all" | "active" | "done"

interface Todo {
  id: number
  text: string
  done: boolean
  priority: Priority
}

const PRIORITY_COLORS: Record<Priority, string> = {
  low: "#10b981", medium: "#f59e0b", high: "#ef4444"
}

export function TodoApp() {
  const [todos, setTodos] = useLocalStorage<Todo[]>("todos", [])
  const [filter, setFilter] = useState<Filter>("all")

  const addTodo = (text: string, priority: Priority) => {
    if (!text.trim()) return
    setTodos(prev => [...prev, { id: Date.now(), text: text.trim(), done: false, priority }])
  }

  const toggleTodo = (id: number) =>
    setTodos(prev => prev.map(t => t.id === id ? { ...t, done: !t.done } : t))

  const deleteTodo = (id: number) =>
    setTodos(prev => prev.filter(t => t.id !== id))

  const filtered = todos.filter(t => {
    if (filter === "active") return !t.done
    if (filter === "done")   return t.done
    return true
  })

  const remaining = todos.filter(t => !t.done).length

  return (
    <div style={{ maxWidth: 480, margin: "2rem auto", fontFamily: "sans-serif" }}>
      <h1>My Tasks</h1>
      <TodoInput onAdd={addTodo} />
      <div style={{ display: "flex", gap: 8, margin: "1rem 0" }}>
        {(["all", "active", "done"] as Filter[]).map(f => (
          <button key={f} onClick={() => setFilter(f)}
            style={{ padding: "4px 12px", fontWeight: filter === f ? 700 : 400 }}>
            {f.charAt(0).toUpperCase() + f.slice(1)}
          </button>
        ))}
      </div>
      <p style={{ fontSize: "0.85rem", color: "#666" }}>{remaining} task{remaining !== 1 ? "s" : ""} remaining</p>
      <ul style={{ listStyle: "none", padding: 0, marginTop: "0.75rem" }}>
        {filtered.map(todo => (
          <TodoItem key={todo.id} todo={todo} onToggle={toggleTodo} onDelete={deleteTodo}
            priorityColor={PRIORITY_COLORS[todo.priority]} />
        ))}
      </ul>
    </div>
  )
}

interface TodoInputProps {
  onAdd: (text: string, priority: Priority) => void
}

function TodoInput({ onAdd }: TodoInputProps) {
  const [text, setText]         = useState("")
  const [priority, setPriority] = useState<Priority>("medium")

  const submit = () => { onAdd(text, priority); setText("") }

  return (
    <div style={{ display: "flex", gap: 8 }}>
      <input value={text} onChange={e => setText(e.target.value)}
        onKeyDown={e => e.key === "Enter" && submit()}
        placeholder="Add a task..." style={{ flex: 1, padding: "0.5rem" }} />
      <select value={priority} onChange={e => setPriority(e.target.value as Priority)}>
        <option value="low">Low</option>
        <option value="medium">Medium</option>
        <option value="high">High</option>
      </select>
      <button onClick={submit}>Add</button>
    </div>
  )
}

interface TodoItemProps {
  todo: { id: number; text: string; done: boolean }
  priorityColor: string
  onToggle: (id: number) => void
  onDelete: (id: number) => void
}

function TodoItem({ todo, priorityColor, onToggle, onDelete }: TodoItemProps) {
  return (
    <li style={{ display: "flex", alignItems: "center", gap: 8, padding: "0.5rem 0",
                 borderBottom: "1px solid #eee" }}>
      <span style={{ width: 10, height: 10, borderRadius: "50%",
                     background: priorityColor, flexShrink: 0 }} />
      <span onClick={() => onToggle(todo.id)}
        style={{ flex: 1, cursor: "pointer",
                 textDecoration: todo.done ? "line-through" : "none",
                 color: todo.done ? "#9ca3af" : "inherit" }}>
        {todo.text}
      </span>
      <button onClick={() => onDelete(todo.id)}
        style={{ background: "none", border: "none", cursor: "pointer", color: "#9ca3af" }}>
        ✕
      </button>
    </li>
  )
}
```

---

## Challenge 5: Deploy and Share

No code solution needed — this is a process challenge. Here's the complete workflow:

```bash
# 1. In your project directory:
git init
echo "node_modules/\ndist/\n.env.local" > .gitignore
git add .
git commit -m "feat: complete calculator app with keyboard support"

# 2. Create repo on GitHub (github.com → + → New repository → my-calculator)
git remote add origin git@github.com:YOURUSERNAME/my-calculator.git
git branch -M main
git push -u origin main

# 3. Deploy to Vercel:
# Go to vercel.com → Add New Project → Import from GitHub
# Select your repo → Deploy (automatic detection of Vite)
# Live URL appears: https://my-calculator-yourusername.vercel.app
```

**README.md template:**
```markdown
# My Calculator

A fully functional calculator built with React + TypeScript, styled with CSS Modules.

## Features
- Basic arithmetic operations (+, -, ×, ÷)
- Percentage and sign toggle
- Keyboard input support
- Error handling (division by zero)
- Calculation history

## Tech Stack
- React 18 + TypeScript
- Vite (build tool)
- CSS Modules (styling)
- Vitest (testing)

## Run Locally
```bash
npm install
npm run dev         # development server
npm run build       # production build
npm test            # run tests
```

## Live Demo
[View on Vercel](https://my-calculator-yourusername.vercel.app)
```

---

*Back to challenges: [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md)*
