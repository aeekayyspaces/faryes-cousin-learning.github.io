# Solutions — 09 Section Quiz

Answer key for [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md).

---

## Part 1: Multiple Choice — Answer Key

| # | Answer | Explanation |
|---|--------|-------------|
| 1 | **B — `npm create vite@latest my-app -- --template react-ts`** | This is the correct Vite command for React + TypeScript. `create-react-app` (option C) is the older Meta tool, now deprecated. Options A and D use incorrect syntax. |
| 2 | **B — JSX requires a single root element** | Every component's `return` must have exactly one root element. Multiple top-level elements violate this. The fix is to wrap them in a `<div>`, `<section>`, or a Fragment `<>...</>`. This rule exists because JSX compiles to `React.createElement` calls, which produce one element per call. |
| 3 | **B — Helps React identify which items changed, were added, or removed** | React uses `key` to match elements between renders. Without stable, unique keys, React may re-render the wrong items, causing bugs with input state, animations, or excessive DOM updates. Keys must be unique among siblings and stable (not random). |
| 4 | **C — `items.push` mutates the original array; React sees the same reference** | React determines if state changed by comparing the new value to the old value with `Object.is()`. Mutating the existing array and passing the same reference means `Object.is(items, items)` is `true` — React thinks nothing changed and skips the re-render. The fix: `setItems([...items, item])` creates a new array with a new reference. |
| 5 | **C — `useEffect(() => { ... }, [])`** | An empty dependency array `[]` means "run this effect once after the first render and never again." This is the React equivalent of a "mount" lifecycle. No dependency array (option A) runs after every render. A dependency with a value (option B) runs when that value changes. `[null]` (option D) would run once (null never changes). |
| 6 | **C — Runs when the component unmounts (or before the effect runs again) to prevent memory leaks** | The cleanup function returned from `useEffect` prevents stale operations from continuing after a component is gone. Without cleanup, an interval would keep running and call `setState` on an unmounted component. Cleanup also runs before re-running an effect (when dependencies change) to cancel the previous effect before starting the new one. |
| 7 | **B — Updating a ref's `.current` does NOT trigger a re-render; updating state does** | This is the key distinction. `setCount(5)` triggers a re-render; `countRef.current = 5` does not. Use `useRef` when you need to store a value that persists across renders but whose change should not cause a re-render (e.g., timer IDs, previous values, DOM references). |
| 8 | **C — Each `.card` gets a unique generated name at build time** | CSS Modules transform class names like `.card` into something like `Card_card__3xN2p` at build time. Even if two components both define `.card`, the generated names are different. This eliminates the classic problem of global CSS class name collisions. |
| 9 | **B — `"btn"` — only the non-conditional class** | `clsx` filters out falsy values. `false && styles.active` evaluates to `false`, which clsx ignores. So only `styles.btn` (which is a truthy string) is included in the output. |
| 10 | **B — Moving state to a higher-level parent so multiple children can share it via props** | When two sibling components need to share the same piece of data, you move ("lift") that state to their nearest common ancestor. The ancestor holds the state and passes it down to both children as props, keeping them in sync. |
| 11 | **B — The updater form receives the guaranteed current state value** | React may batch multiple state updates. If you call `setState(count + 1)` three times in one event handler, all three may read the same stale `count`. With the updater form `setState(prev => prev + 1)`, each call receives the result of the previous call — so three calls correctly increment by 3. |
| 12 | **C — `npm run build`** | This runs the `build` script in `package.json` (typically `tsc && vite build`), which type-checks TypeScript and produces an optimized production bundle in the `dist/` folder. `npm run start` is a convention for development servers (not Vite). |
| 13 | **B — The component's form input value is driven by React state, synchronized on every `onChange`** | A controlled input has `value={stateVariable}` and `onChange={e => setState(e.target.value)}`. React is the single source of truth for the input's value. An uncontrolled input manages its own value in the DOM and is read via a ref when needed. |
| 14 | **C — Contains regeneratable files that would massively bloat the repo** | `node_modules/` is fully described by `package.json` and `package-lock.json`. Anyone cloning the repo runs `npm install` to restore it. Committing it would add hundreds of thousands of files and hundreds of MB to the repository. |
| 15 | **C — `useEffect(() => { initialize() }, [])`** | An empty dependency array makes the effect run once after the first render — equivalent to a component "mounting." `useState` with a lazy initializer (option A) initializes state once but doesn't run async code. `useMemo` (option D) runs synchronously during render, not after. |

---

## Part 2: Short Answer — Suggested Responses

### 1. The Virtual DOM

**What it is:** The Virtual DOM (VDOM) is a lightweight JavaScript object that mirrors the structure of the real DOM. React maintains this in-memory copy of what the UI should look like.

**The problem it solves:** Real DOM operations are expensive — changing a DOM node can trigger layout recalculation, painting, and compositing. Doing this many times per second causes visible lag.

**The three steps when state changes:**
1. **Re-render:** React calls the component function with new state, which returns new JSX — this builds a new Virtual DOM tree in memory (cheap — just creating JavaScript objects)
2. **Diff:** React compares the new VDOM tree against the previous one using a "reconciliation" algorithm, finding the minimal set of changes needed
3. **Commit:** React applies only the necessary changes to the real DOM (minimal, batched updates) and triggers a repaint

---

### 2. Unidirectional Data Flow

**Data flows one direction:** from parent components down to child components via props. Children cannot directly modify a parent's state.

**How children communicate upward:** The parent passes a **callback function as a prop**. The child calls this function (with any data as arguments) when something happens. The parent's callback updates the parent's state, triggering a re-render with the new data flowing back down.

```tsx
// Parent owns state; child calls the callback prop
function Parent() {
  const [value, setValue] = useState("")
  return <Child value={value} onChange={setValue} />
}

function Child({ value, onChange }) {
  return <input value={value} onChange={e => onChange(e.target.value)} />
}
```

**Why it's beneficial:** Makes data flow predictable and traceable. When a bug occurs, you always ask "which state changed, and which parent holds it?" — rather than chasing two-way bindings that can form cycles.

---

### 3. The useEffect Dependency Array

**What it is:** The second argument to `useEffect` — an array of values that the effect depends on. React re-runs the effect whenever any value in the array changes between renders.

**Why every reactive value must be listed:** Inside the effect, you close over variables from the component's render. If a variable changes but isn't in the dependency array, the effect still holds a reference to the old ("stale") value from the previous render — this is a "stale closure." The effect would use outdated data without knowing it.

**What happens if you omit a dependency:** The effect runs with stale values. Example: an effect that fetches `userId`'s data but doesn't list `userId` in deps — when `userId` changes, the effect doesn't re-run and continues showing the old user's data. ESLint's `react-hooks/exhaustive-deps` rule catches this.

---

### 4. Calculator App Architecture

**Components:**

- **`App`** — root component; owns all calculator state (`current`, `previous`, `operator`, `justEvaluated`, `hasError`); handles the `dispatch` function that processes all actions; manages the keyboard listener
- **`Display`** — presentational; receives `expression`, `current`, `hasError` as props; renders the two-line display; no logic of its own; adjusts font size based on number length
- **`ButtonGrid`** — presentational; defines the BUTTONS array mapping labels to action objects; renders all `CalcButton` instances; receives `onAction` callback prop
- **`CalcButton`** — presentational; renders a single button; accepts `variant`, `wide`, `label`, `onClick`; no logic — just applies CSS classes and calls onClick

**Data flow:**
- User click → `CalcButton.onClick` → `ButtonGrid.onAction(action)` → `App.dispatch(action)` → state update → React re-renders → `Display` shows new values

**Separation of concerns:**
- Calculator *logic* (math, history) lives in `Calculator.ts` — a plain TypeScript class, fully testable without React
- React *state* manages the current input string, operator, and previous value
- Components are purely presentational — they display data and fire callbacks

---

### 5. Custom Hooks

**What they are:** Custom hooks are functions that start with `use` and call built-in React hooks. They extract stateful logic out of components into reusable units.

**Why write one:** When the same combination of state and effects appears in multiple components, extract it into a custom hook to share the logic without copying code. Custom hooks make components thinner and the logic independently testable.

**Good scenario:** Multiple components across your app need to fetch data and manage loading/error states. Rather than repeating `useState(null)`, `useState(true)`, `useState(null)`, and the same `useEffect` fetch pattern in every component, you write:

```tsx
function useFetch<T>(url: string) {
  const [data, setData]       = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError]     = useState<Error | null>(null)
  // ... useEffect with fetch + abort controller ...
  return { data, loading, error }
}

// Now any component that needs to fetch just does:
const { data, loading, error } = useFetch<User>("/api/users/42")
```

The fetch logic is written once, tested once, and reused everywhere.

---

*Back to quiz: [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md)*
