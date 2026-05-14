# 09 — Section Quiz and Checkpoints

## Section 8 Learning Objectives Review

Before taking this quiz, confirm you can:

- [ ] Install Node.js and create a Vite + React + TypeScript project
- [ ] Explain what React is and what the Virtual DOM does
- [ ] Write JSX following all its rules
- [ ] Define and use props with TypeScript interfaces
- [ ] Manage state with `useState` and update arrays/objects immutably
- [ ] Use `useEffect` with correct dependencies and cleanup
- [ ] Use `useRef` to access DOM elements and store mutable values
- [ ] Create CSS Modules and conditionally apply class names with `clsx`
- [ ] Build the complete Calculator app with keyboard support
- [ ] Build a production bundle with `npm run build`
- [ ] Deploy to Vercel or GitHub Pages

---

## Part 1: Multiple Choice

Answer key: [solutions/09-quiz-solutions.md](solutions/09-quiz-solutions.md)

---

**1.** What command creates a new Vite + React + TypeScript project?

- A) `npm init react-app my-app --template typescript`
- B) `npm create vite@latest my-app -- --template react-ts`
- C) `npx create-react-app my-app --typescript`
- D) `vite new react my-app --ts`

---

**2.** What is wrong with this JSX?

```tsx
return (
  <h1>Title</h1>
  <p>Content</p>
)
```

- A) `h1` and `p` are not valid JSX elements
- B) JSX requires a single root element — wrap both in `<>...</>` or a `<div>`
- C) String content cannot be placed directly inside JSX tags
- D) The `return` keyword cannot be used with JSX

---

**3.** What is the `key` prop used for in list rendering?

- A) It's the CSS class name for each list item
- B) It helps React identify which items have changed, been added, or been removed for efficient updates
- C) It sets the tab order for keyboard navigation
- D) It uniquely identifies the component type in the virtual DOM

---

**4.** What is wrong with this state update?

```tsx
const [items, setItems] = useState(["a", "b", "c"])
const addItem = (item: string) => {
  items.push(item)
  setItems(items)
}
```

- A) `push` is not a valid array method in TypeScript
- B) You must use `setItems` before `push`, not after
- C) `items.push` mutates the original array; React sees the same reference and won't re-render
- D) `useState` cannot hold string arrays

---

**5.** Which `useEffect` runs **only once** after the component first mounts?

- A) `useEffect(() => { ... })`
- B) `useEffect(() => { ... }, [prop])`
- C) `useEffect(() => { ... }, [])`
- D) `useEffect(() => { ... }, [null])`

---

**6.** What does the cleanup function returned from `useEffect` do?

```tsx
useEffect(() => {
  const id = setInterval(() => tick(), 1000)
  return () => clearInterval(id)
}, [])
```

- A) Cancels the effect before it first runs
- B) Runs after every render to reset the interval
- C) Runs when the component unmounts (or before the effect runs again) to prevent memory leaks
- D) Runs only if an error occurs during the effect

---

**7.** What is the primary difference between `useRef` and `useState`?

- A) `useRef` can only hold DOM elements; `useState` can hold any value
- B) Updating a ref's `.current` does NOT trigger a re-render; updating state does
- C) `useState` is synchronous; `useRef` is asynchronous
- D) `useRef` works across components; `useState` is local only

---

**8.** In CSS Modules, if two different components each have a `.card` class, what happens?

- A) Both classes collide — the second one overrides the first
- B) TypeScript throws a compilation error about duplicate class names
- C) Each `.card` class gets a unique generated name at build time — no collision
- D) Only one component can use `.card` — the other must use a different name

---

**9.** What does `clsx(styles.btn, active && styles.active)` produce when `active` is `false`?

- A) `"btn active"` — both classes applied
- B) `"btn"` — only the non-conditional class
- C) `""` — empty string, clsx removes all classes when any condition is false
- D) A TypeScript error — `&&` cannot be used inside `clsx`

---

**10.** What is "lifting state up" in React?

- A) Promoting a component from a child to a parent component
- B) Moving state to a higher-level parent component so multiple children can share it via props
- C) Using `useContext` instead of `useState` for global state
- D) Converting local state to a server-side variable

---

**11.** What does the updater function form `setState(prev => prev + 1)` do differently from `setState(count + 1)`?

- A) Nothing — they are equivalent
- B) The updater form receives the guaranteed current state value, preventing stale closure bugs when multiple updates batch
- C) The updater form delays the state update until the next render
- D) The updater form only works with primitive values

---

**12.** What command builds a production-ready bundle in a Vite project?

- A) `npm run start`
- B) `npm run export`
- C) `npm run build`
- D) `vite compile`

---

**13.** In React, a controlled component is one where:

- A) The component is wrapped in React's `<StrictMode>` for safety
- B) The component's form input value is driven by React state, synchronized on every `onChange`
- C) The component only receives props and has no internal state
- D) The component is managed by a parent's `ref`

---

**14.** Why should `node_modules/` be excluded from your Git repository?

- A) Git cannot track binary files like those in `node_modules/`
- B) `node_modules/` contains secret API keys
- C) It contains thousands of regeneratable files that would massively bloat the repo; anyone clones and runs `npm install` to recreate it
- D) GitHub blocks repositories larger than 50 MB

---

**15.** Which hook would you use to run a function automatically when a component is first added to the DOM?

- A) `useState(() => initialize())`
- B) `useRef` with a callback ref
- C) `useEffect(() => { initialize() }, [])`
- D) `useMemo(() => initialize(), [])`

---

## Part 2: Short Answer

Suggested answers: [solutions/09-quiz-solutions.md](solutions/09-quiz-solutions.md)

**1.** Explain the Virtual DOM. What problem does it solve, and what are the three steps React takes when state changes?

**2.** Explain React's unidirectional data flow. How do child components communicate "upward" when they need to signal a change?

**3.** What is the dependency array in `useEffect`? Why must every reactive value used inside the effect be listed? What happens if you omit a dependency?

**4.** Describe the component architecture of the calculator app. What components exist, what are their responsibilities, and how do they communicate?

**5.** What is a custom hook and why would you write one? Give an example scenario where extracting a custom hook is the right solution.

---

## Part 3: Code Challenges

Solutions: [solutions/code-challenges-solutions.md](solutions/code-challenges-solutions.md)

---

### Challenge 1: useEffect and Fetch

Build a `CryptoTicker` component that:
- Accepts a `coinId: string` prop (e.g., `"bitcoin"`, `"ethereum"`)
- Fetches the current price from `https://api.coingecko.com/api/v3/simple/price?ids={coinId}&vs_currencies=usd` every 30 seconds (use `setInterval` in `useEffect`)
- Displays the coin name and current USD price
- Shows a loading skeleton on first load
- Shows an error message if the fetch fails
- Properly cleans up the interval and cancels in-flight requests when the `coinId` prop changes or the component unmounts

---

### Challenge 2: Custom Hook — `useDebounce`

Implement a `useDebounce<T>(value: T, delay: number): T` custom hook that returns the debounced value of the input.

Then build a `LiveSearch` component that:
- Has a text input
- Uses `useDebounce` with 400ms delay on the input value
- Fetches results from `https://restcountries.com/v3.1/name/{query}?fields=name,flags` only when the debounced value changes (and is non-empty)
- Renders results as a dropdown list of flag images + country names
- Shows "Searching..." while fetching and "No results" for 404

---

### Challenge 3: Add History to the Calculator

Extend the calculator app from page 07 with a history panel:

1. Add a `HistoryPanel` component that:
   - Receives `history: HistoryEntry[]` and `onClear: () => void` as props
   - Renders each entry as `expression = result`
   - Has a "Clear History" button
   - Shows "No history yet" when empty
   - Has a maximum visible height with scroll (CSS: `max-height: 200px; overflow-y: auto`)

2. Add a toggle button in the App to show/hide the history panel

3. Clicking a history entry populates the display with that entry's result

---

### Challenge 4: React + TypeScript Todo App

Build a complete Todo application with:

**Components:**
- `TodoApp` — root, owns all state
- `TodoInput` — input + priority dropdown + Add button
- `TodoList` — renders the list with filter tabs (All / Active / Done)
- `TodoItem` — individual item with done toggle, delete, and priority dot

**Features:**
- Add tasks with a priority (Low / Medium / High)
- Toggle tasks complete/incomplete
- Delete tasks
- Filter by All / Active / Done
- Task count: "3 tasks remaining"
- Persist state to `localStorage` using a `useLocalStorage` custom hook
- TypeScript types for all props and state

---

### Challenge 5: Deploy and Share

1. Complete the calculator project from page 07 (or one of the above challenges)
2. Push the code to a new GitHub repository
3. Deploy to Vercel (or GitHub Pages)
4. Share the live URL — your first deployed React application!

Write a brief `README.md` covering:
- What the app does
- Technologies used (React, TypeScript, Vite)
- How to run locally (`npm install && npm run dev`)
- A link to the live demo

---

## Course Completion Checklist

You have completed all 8 sections of the curriculum. Verify you can do each of these:

**Section 1 — Computing Fundamentals**
- [ ] Explain CPU, memory, and the OS to someone with no technical background

**Section 2 — Networking**
- [ ] Describe what happens when you type a URL and press Enter, from DNS to rendered page

**Section 3 — The Web**
- [ ] Build a styled HTML page from scratch; explain the DOM, CSS cascade, and critical rendering path

**Section 4 — JavaScript**
- [ ] Write functions, loops, async/await, and fetch an API without help

**Section 5 — DOM, Forms, AJAX**
- [ ] Build a real, interactive app that fetches live data and handles all states

**Section 6 — Git**
- [ ] Create a repository, commit code, create branches, resolve conflicts, open a PR

**Section 7 — TypeScript and OOP**
- [ ] Write typed classes with inheritance, write tests with Vitest

**Section 8 — React**
- [ ] Build and deploy a React + TypeScript app with hooks, components, and styling

---

*Review sources: [10 — Sources](10-sources.md)*
