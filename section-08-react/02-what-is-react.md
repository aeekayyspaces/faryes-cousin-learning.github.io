# 02 — What Is React? Components and the Virtual DOM

## Learning Objectives

By the end of this page, you will be able to:

- Explain what React is and the problem it was built to solve
- Describe what the Virtual DOM is and why it exists
- Define a React component and explain why components are the fundamental unit
- Understand React's unidirectional data flow
- Compare React's model to direct DOM manipulation

---

## The Problem React Solves

Before React (and similar libraries), building interactive UIs meant directly manipulating the DOM with JavaScript:

```javascript
// Traditional DOM manipulation — scattered, hard to maintain
function updateUserProfile(user) {
  document.getElementById("username").textContent = user.name
  document.getElementById("avatar").src = user.avatarUrl
  document.getElementById("bio").textContent = user.bio
  document.getElementById("follow-count").textContent = user.followers
  // What if user is null? What if some elements don't exist yet?
  // What if this runs concurrently with another update?
}
```

As applications grew, this approach became unmaintainable:
- State was scattered everywhere — in the DOM, in variables, in event handlers
- Keeping the UI in sync with data required complex coordination
- Testing was nearly impossible
- Code that worked for one UI element had to be duplicated for similar ones

### React's Core Insight

> "Rather than imperatively manipulating the DOM, **describe what the UI should look like for any given state**, and let React figure out the most efficient way to update the DOM to match."

```tsx
// React's way — declarative
function UserProfile({ user }: { user: User }) {
  return (
    <div className="profile">
      <img src={user.avatarUrl} alt={user.name} />
      <h2>{user.name}</h2>
      <p>{user.bio}</p>
      <span>{user.followers} followers</span>
    </div>
  )
}
```

You describe *what* the UI should look like given `user`. React handles *how* to update the DOM to match.

---

## What Is React?

**React** is a JavaScript library (not a full framework) for building user interfaces, developed and maintained by Meta (Facebook).

- Released in 2013
- Powers Facebook, Instagram, WhatsApp Web, Airbnb, Netflix's UI, and thousands of other apps
- Not opinionated about routing, state management, or server communication — you choose those
- The most widely used UI library in the world

### React Is a Library, Not a Framework

React handles the **view layer** only — rendering UI components. For a complete application you typically add:

| Need | Common Solution |
|------|----------------|
| Routing | React Router, TanStack Router |
| Global state | Zustand, Redux Toolkit, Jotai |
| Data fetching | TanStack Query, SWR |
| Forms | React Hook Form, Formik |
| Full-stack | Next.js, Remix |

---

## The Virtual DOM

The **Virtual DOM (VDOM)** is React's in-memory representation of what the real DOM should look like.

### Why Not Update the Real DOM Directly?

DOM operations are expensive — every time you change the DOM, the browser may recalculate layout, repaint, and composite. Doing this many times rapidly causes visible jank.

### React's Process

```
1. Your data changes (state update)

2. React re-renders the component tree in memory
   (creates a new Virtual DOM tree — cheap, just JavaScript objects)

3. React DIFFS the new VDOM against the previous VDOM
   (finds the minimal set of real DOM changes needed)

4. React COMMITS only the necessary changes to the real DOM
   (minimal, batched real DOM updates)
```

> **Analogy:** React is like an architect who revises a blueprint (Virtual DOM) before sending instructions to the construction crew (real DOM). Revising paper is instant. Construction is expensive. You want the construction crew to do minimal, precise work — not tear down and rebuild walls because you moved one door.

### React 18: Concurrent Rendering

React 18 introduced **concurrent rendering** — React can now interrupt, pause, and resume rendering work. This allows React to prioritize urgent updates (user input) over less urgent ones (background data) without blocking the UI.

---

## Components: The Fundamental Unit

A **component** is a reusable, self-contained piece of UI — a function that accepts **props** (inputs) and returns **JSX** (the UI description).

Everything in React is a component, and complex UIs are built by composing small components together.

```
App
├── Header
│   ├── Logo
│   └── Navigation
│       ├── NavItem
│       ├── NavItem
│       └── NavItem
├── Main
│   ├── ProductList
│   │   ├── ProductCard
│   │   ├── ProductCard
│   │   └── ProductCard
│   └── Sidebar
│       └── FilterPanel
└── Footer
```

Each box is a component. Small components are easy to build, test, and reuse. Large UIs are composed from small ones.

### Your First Component

```tsx
// A component is a function that returns JSX
function Greeting() {
  return <h1>Hello, World!</h1>
}

// With props (typed with TypeScript):
interface GreetingProps {
  name: string
  enthusiastic?: boolean
}

function Greeting({ name, enthusiastic = false }: GreetingProps) {
  return (
    <h1>
      Hello, {name}{enthusiastic ? "!!!" : "."}
    </h1>
  )
}

// Use it:
<Greeting name="Alex" />             // Hello, Alex.
<Greeting name="Alex" enthusiastic /> // Hello, Alex!!!
```

### Rules of Components

1. **Component names must start with a capital letter** — `Greeting`, not `greeting`. Lowercase names are treated as HTML elements.
2. **Must return a single root element** (or a Fragment `<>...</>` to avoid an extra wrapper).
3. **Must be pure** — given the same props and state, always return the same JSX. No side effects in the render function.

---

## Unidirectional Data Flow

In React, data flows in **one direction**: from parent to child via props.

```
        App
        │
        │ passes data down via props
        ▼
    ParentComponent
        │
        │ passes data down via props
        ▼
    ChildComponent
```

Children **cannot** push data up to parents directly. Instead, parents pass **callback functions** as props — children call them to signal events:

```tsx
function Parent() {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>Count: {count}</p>
      <Child onIncrement={() => setCount(c => c + 1)} />
    </div>
  )
}

function Child({ onIncrement }: { onIncrement: () => void }) {
  return <button onClick={onIncrement}>+</button>
}
```

> **Why unidirectional?** It makes data flow predictable and debuggable. You always know: data comes from above, events signal above. No two-way bindings that create hard-to-trace loops.

---

## React's Rendering Model

When you call `setState` (or update state with `useState`), React schedules a **re-render**:

1. React calls your component function again
2. The function returns new JSX
3. React diffs the new JSX against the previous render
4. React updates only what changed in the real DOM

This means your component function may run many times. It must be **pure** — no side effects during rendering (no `fetch`, no `setTimeout`, no direct DOM access). Side effects belong in `useEffect` (covered in page 05).

### What Triggers a Re-render?

- State changes (`useState` setter called)
- Props change (parent re-renders and passes new values)
- Context updates
- Parent component re-renders (even if props didn't change)

React's rendering is **fast** — calling a function that returns JSX objects is cheap. The expensive part (real DOM updates) is minimized by the diff algorithm.

---

## React vs Vanilla DOM Manipulation

| Concern | Vanilla JS | React |
|---------|-----------|-------|
| UI updates | Manually call `element.textContent = ...` | Re-render the component; React handles DOM |
| Data and UI sync | Your responsibility — easy to get out of sync | Automatic — UI is always derived from state |
| Code organization | Functions operating on DOM elements | Components encapsulating data + UI + behavior |
| Reuse | Copy-paste or complex DOM factories | Import and use components anywhere |
| Testability | Hard — requires a browser DOM | Easy — components are pure functions |

**Further Reading:**
- [React Documentation — Main Concepts](https://react.dev/learn)
- [Why React? — React Blog](https://react.dev/blog/2023/03/22/react-labs-what-we-have-been-working-on-march-2023)
- [How React Works — Under the Hood](https://react.dev/learn/preserving-and-resetting-state)

---

## Check Your Understanding

1. What does "declarative UI" mean? How is it different from "imperative" DOM manipulation?
2. What is the Virtual DOM and what problem does it solve?
3. What is a React component? What does it accept and what does it return?
4. What does "unidirectional data flow" mean in React? Why is it beneficial?
5. What triggers a component to re-render?

---

*Previous: [01 — Environment Setup](01-environment-setup.md)*
*Next: [03 — JSX: HTML Inside TypeScript](03-jsx.md)*
