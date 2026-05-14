# 07 — Project: Build a Calculator App in React + TypeScript

## Overview

This is the capstone project for the entire course. You will build a fully functional calculator application using everything you've learned:

- **TypeScript** for types, classes, and OOP (Section 7)
- **React** with hooks and components (Sections 4–6 of this section)
- **CSS Modules** for scoped styling
- **Vitest** for testing

The finished app supports addition, subtraction, multiplication, division, percentage, sign toggle, keyboard input, and preserves history.

---

## Project Structure

```
my-calculator/
├── src/
│   ├── calculator/
│   │   ├── Calculator.ts          ← the core logic class (from Section 7)
│   │   └── Calculator.test.ts     ← logic tests
│   ├── components/
│   │   ├── Display.tsx            ← shows current input and result
│   │   ├── Display.module.css
│   │   ├── ButtonGrid.tsx         ← renders the button layout
│   │   ├── ButtonGrid.module.css
│   │   ├── CalcButton.tsx         ← individual button component
│   │   └── CalcButton.module.css
│   ├── App.tsx                    ← main app, owns state
│   ├── App.module.css
│   ├── main.tsx
│   └── index.css
```

---

## Step 1: Set Up the Project

```bash
npm create vite@latest my-calculator -- --template react-ts
cd my-calculator
npm install
npm install --save-dev vitest @vitest/ui clsx
npm run dev
```

Update `vite.config.ts` to enable testing:

```typescript
// vite.config.ts
import { defineConfig } from "vite"
import react from "@vitejs/plugin-react"

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: "jsdom"
  }
})
```

Add to `package.json` scripts:
```json
"test": "vitest",
"test:ui": "vitest --ui"
```

---

## Step 2: The Calculator Logic

Create `src/calculator/Calculator.ts`. This reuses and extends the `Calculator` class from Section 7:

```typescript
// src/calculator/Calculator.ts

export type Operation = "+" | "-" | "×" | "÷"

export interface HistoryEntry {
  expression: string
  result: string
}

export class DivisionByZeroError extends Error {
  constructor() {
    super("Cannot divide by zero")
    this.name = "DivisionByZeroError"
    Object.setPrototypeOf(this, new.target.prototype)
  }
}

export class Calculator {
  private history: HistoryEntry[] = []

  evaluate(a: string, op: Operation, b: string): string {
    const numA = parseFloat(a)
    const numB = parseFloat(b)

    if (isNaN(numA) || isNaN(numB)) return "Error"

    let result: number

    switch (op) {
      case "+": result = numA + numB; break
      case "-": result = numA - numB; break
      case "×": result = numA * numB; break
      case "÷":
        if (numB === 0) throw new DivisionByZeroError()
        result = numA / numB
        break
    }

    const formatted = this.formatResult(result)
    this.history.push({
      expression: `${a} ${op} ${b}`,
      result: formatted
    })
    return formatted
  }

  private formatResult(value: number): string {
    if (!isFinite(value)) return "Error"
    // Avoid floating point noise: 0.1 + 0.2 = 0.30000000000004
    const rounded = parseFloat(value.toPrecision(12))
    // If it's an integer, don't show decimal point
    return Number.isInteger(rounded) ? rounded.toString() : rounded.toString()
  }

  getHistory(): readonly HistoryEntry[] {
    return this.history
  }

  clearHistory(): void {
    this.history = []
  }
}
```

---

## Step 3: Test the Logic

Create `src/calculator/Calculator.test.ts`:

```typescript
import { describe, it, expect, beforeEach } from "vitest"
import { Calculator, DivisionByZeroError } from "./Calculator"

describe("Calculator", () => {
  let calc: Calculator
  beforeEach(() => { calc = new Calculator() })

  it("adds correctly", () => expect(calc.evaluate("1", "+", "2")).toBe("3"))
  it("subtracts correctly", () => expect(calc.evaluate("10", "-", "4")).toBe("6"))
  it("multiplies correctly", () => expect(calc.evaluate("6", "×", "7")).toBe("42"))
  it("divides correctly", () => expect(calc.evaluate("10", "÷", "2")).toBe("5"))
  it("handles decimals", () => expect(calc.evaluate("0.1", "+", "0.2")).toBe("0.3"))
  it("throws on division by zero", () => {
    expect(() => calc.evaluate("5", "÷", "0")).toThrow(DivisionByZeroError)
  })
  it("records history", () => {
    calc.evaluate("2", "+", "3")
    calc.evaluate("10", "÷", "2")
    expect(calc.getHistory()).toHaveLength(2)
    expect(calc.getHistory()[0].expression).toBe("2 + 3")
  })
})
```

Run `npm test` — all tests should pass before building the UI.

---

## Step 4: Global Styles

Replace `src/index.css`:

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --bg:          #1a1a2e;
  --surface:     #16213e;
  --surface-2:   #0f3460;
  --primary:     #7c3aed;
  --primary-dk:  #6d28d9;
  --text:        #e0e0ff;
  --text-muted:  #8892b0;
  --btn-num:     #1e2a4a;
  --btn-op:      #7c3aed;
  --btn-special: #2a3a5e;
  --btn-equals:  #7c3aed;
  --radius:      12px;
  --font: 'Segoe UI', system-ui, sans-serif;
}

body {
  font-family: var(--font);
  background: var(--bg);
  color: var(--text);
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1rem;
}
```

---

## Step 5: The `CalcButton` Component

```tsx
// src/components/CalcButton.tsx
import clsx from "clsx"
import styles from "./CalcButton.module.css"

export type ButtonVariant = "number" | "operator" | "special" | "equals"

interface CalcButtonProps {
  label: string
  onClick: () => void
  variant?: ButtonVariant
  wide?: boolean          // the "0" key spans two columns
  "aria-label"?: string
}

export function CalcButton({
  label,
  onClick,
  variant = "number",
  wide = false,
  "aria-label": ariaLabel
}: CalcButtonProps) {
  return (
    <button
      className={clsx(
        styles.btn,
        styles[variant],
        wide && styles.wide
      )}
      onClick={onClick}
      aria-label={ariaLabel ?? label}
    >
      {label}
    </button>
  )
}
```

```css
/* src/components/CalcButton.module.css */
.btn {
  font-size: 1.35rem;
  font-weight: 500;
  border: none;
  border-radius: var(--radius);
  padding: 0;
  height: 72px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  user-select: none;
  transition: filter 0.1s ease, transform 0.1s ease;
  color: var(--text);
}

.btn:hover  { filter: brightness(1.15); }
.btn:active { transform: scale(0.95); filter: brightness(0.9); }

.number   { background: var(--btn-num); }
.operator { background: var(--btn-op); }
.special  { background: var(--btn-special); color: var(--text-muted); }
.equals   { background: var(--btn-op); font-size: 1.75rem; }

.wide { grid-column: span 2; }
```

---

## Step 6: The `Display` Component

```tsx
// src/components/Display.tsx
import styles from "./Display.module.css"

interface DisplayProps {
  expression: string    // e.g. "42 +"
  current: string       // what's currently being typed/shown large
  hasError: boolean
}

export function Display({ expression, current, hasError }: DisplayProps) {
  // Shrink font if number is long
  const fontSize =
    current.length > 12 ? "1.6rem" :
    current.length > 9  ? "2rem"   :
    current.length > 6  ? "2.5rem" : "3rem"

  return (
    <div className={styles.display}>
      <div className={styles.expression} aria-label="expression">
        {expression || " "} {/* non-breaking space when empty */}
      </div>
      <div
        className={`${styles.current} ${hasError ? styles.error : ""}`}
        style={{ fontSize }}
        aria-label="current value"
        aria-live="polite"
      >
        {current}
      </div>
    </div>
  )
}
```

```css
/* src/components/Display.module.css */
.display {
  background: var(--surface);
  border-radius: var(--radius);
  padding: 1rem 1.25rem;
  text-align: right;
  margin-bottom: 0.75rem;
  min-height: 100px;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  gap: 0.25rem;
}

.expression {
  font-size: 0.9rem;
  color: var(--text-muted);
  min-height: 1.3em;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.current {
  font-size: 3rem;
  font-weight: 300;
  color: var(--text);
  transition: font-size 0.1s ease;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.error { color: #f87171; }
```

---

## Step 7: The `ButtonGrid` Component

```tsx
// src/components/ButtonGrid.tsx
import { CalcButton } from "./CalcButton"
import type { ButtonVariant } from "./CalcButton"
import type { Operation } from "../calculator/Calculator"
import styles from "./ButtonGrid.module.css"

interface ButtonDef {
  label: string
  variant: ButtonVariant
  wide?: boolean
  ariaLabel?: string
  action: Action
}

type Action =
  | { type: "digit";    value: string }
  | { type: "decimal" }
  | { type: "operator"; value: Operation }
  | { type: "equals" }
  | { type: "clear" }
  | { type: "sign" }
  | { type: "percent" }
  | { type: "backspace" }

interface ButtonGridProps {
  onAction: (action: Action) => void
}

const BUTTONS: ButtonDef[] = [
  { label: "AC",  variant: "special",  action: { type: "clear" } },
  { label: "+/-", variant: "special",  action: { type: "sign" },    ariaLabel: "toggle sign" },
  { label: "%",   variant: "special",  action: { type: "percent" }, ariaLabel: "percent" },
  { label: "÷",   variant: "operator", action: { type: "operator", value: "÷" } },

  { label: "7",   variant: "number",   action: { type: "digit", value: "7" } },
  { label: "8",   variant: "number",   action: { type: "digit", value: "8" } },
  { label: "9",   variant: "number",   action: { type: "digit", value: "9" } },
  { label: "×",   variant: "operator", action: { type: "operator", value: "×" } },

  { label: "4",   variant: "number",   action: { type: "digit", value: "4" } },
  { label: "5",   variant: "number",   action: { type: "digit", value: "5" } },
  { label: "6",   variant: "number",   action: { type: "digit", value: "6" } },
  { label: "-",   variant: "operator", action: { type: "operator", value: "-" } },

  { label: "1",   variant: "number",   action: { type: "digit", value: "1" } },
  { label: "2",   variant: "number",   action: { type: "digit", value: "2" } },
  { label: "3",   variant: "number",   action: { type: "digit", value: "3" } },
  { label: "+",   variant: "operator", action: { type: "operator", value: "+" } },

  { label: "0",   variant: "number",   wide: true, action: { type: "digit", value: "0" } },
  { label: ".",   variant: "number",   action: { type: "decimal" } },
  { label: "=",   variant: "equals",   action: { type: "equals" } },
]

export function ButtonGrid({ onAction }: ButtonGridProps) {
  return (
    <div className={styles.grid}>
      {BUTTONS.map((btn) => (
        <CalcButton
          key={btn.label}
          label={btn.label}
          onClick={() => onAction(btn.action)}
          variant={btn.variant}
          wide={btn.wide}
          aria-label={btn.ariaLabel}
        />
      ))}
    </div>
  )
}
```

```css
/* src/components/ButtonGrid.module.css */
.grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 0.6rem;
}
```

---

## Step 8: The App (State and Logic)

```tsx
// src/App.tsx
import { useState, useEffect, useCallback } from "react"
import { Display } from "./components/Display"
import { ButtonGrid } from "./components/ButtonGrid"
import { Calculator, DivisionByZeroError } from "./calculator/Calculator"
import type { Operation } from "./calculator/Calculator"
import styles from "./App.module.css"

type Action =
  | { type: "digit";    value: string }
  | { type: "decimal" }
  | { type: "operator"; value: Operation }
  | { type: "equals" }
  | { type: "clear" }
  | { type: "sign" }
  | { type: "percent" }
  | { type: "backspace" }

interface CalcState {
  current: string         // number being typed
  previous: string        // first operand
  operator: Operation | null
  justEvaluated: boolean  // true after pressing =
  hasError: boolean
}

const INITIAL_STATE: CalcState = {
  current: "0",
  previous: "",
  operator: null,
  justEvaluated: false,
  hasError: false
}

const calc = new Calculator()

function App() {
  const [state, setState] = useState<CalcState>(INITIAL_STATE)

  const displayExpression = state.operator
    ? `${state.previous} ${state.operator}`
    : ""

  const dispatch = useCallback((action: Action) => {
    setState(prev => {
      switch (action.type) {
        case "digit": {
          if (prev.hasError) return prev
          const start = prev.justEvaluated || prev.current === "0"
            ? action.value
            : prev.current + action.value
          // Limit to 12 digits
          if (start.replace(".", "").replace("-", "").length > 12) return prev
          return { ...prev, current: start, justEvaluated: false }
        }

        case "decimal": {
          if (prev.hasError) return prev
          if (prev.current.includes(".")) return prev
          const start = prev.justEvaluated ? "0." : prev.current + "."
          return { ...prev, current: start, justEvaluated: false }
        }

        case "operator": {
          if (prev.hasError) return INITIAL_STATE
          // Chain operators: if we already have an operator and a second operand, evaluate first
          if (prev.operator && !prev.justEvaluated && prev.previous) {
            try {
              const result = calc.evaluate(prev.previous, prev.operator, prev.current)
              return { current: result, previous: result, operator: action.value, justEvaluated: false, hasError: false }
            } catch {
              return { ...INITIAL_STATE, hasError: true, current: "Error" }
            }
          }
          return {
            ...prev,
            operator: action.value,
            previous: prev.current,
            justEvaluated: false
          }
        }

        case "equals": {
          if (!prev.operator || prev.hasError) return prev
          // Repeat last operation if = pressed again
          const a = prev.justEvaluated ? prev.current : prev.previous
          const b = prev.current
          try {
            const result = calc.evaluate(a, prev.operator, b)
            return { current: result, previous: a, operator: prev.operator, justEvaluated: true, hasError: false }
          } catch (err) {
            const msg = err instanceof DivisionByZeroError ? "÷0 Error" : "Error"
            return { ...INITIAL_STATE, current: msg, hasError: true }
          }
        }

        case "clear":
          return INITIAL_STATE

        case "sign":
          if (prev.current === "0" || prev.hasError) return prev
          return { ...prev, current: prev.current.startsWith("-")
            ? prev.current.slice(1)
            : "-" + prev.current
          }

        case "percent":
          if (prev.hasError) return prev
          return { ...prev, current: String(parseFloat(prev.current) / 100) }

        case "backspace":
          if (prev.hasError) return INITIAL_STATE
          if (prev.current.length <= 1) return { ...prev, current: "0" }
          return { ...prev, current: prev.current.slice(0, -1) }

        default:
          return prev
      }
    })
  }, [])

  // Keyboard support
  useEffect(() => {
    const handleKey = (e: KeyboardEvent) => {
      if (e.key >= "0" && e.key <= "9") dispatch({ type: "digit", value: e.key })
      else if (e.key === ".")                dispatch({ type: "decimal" })
      else if (e.key === "+")                dispatch({ type: "operator", value: "+" })
      else if (e.key === "-")                dispatch({ type: "operator", value: "-" })
      else if (e.key === "*")                dispatch({ type: "operator", value: "×" })
      else if (e.key === "/") { e.preventDefault(); dispatch({ type: "operator", value: "÷" }) }
      else if (e.key === "Enter" || e.key === "=") dispatch({ type: "equals" })
      else if (e.key === "Escape")           dispatch({ type: "clear" })
      else if (e.key === "Backspace")        dispatch({ type: "backspace" })
      else if (e.key === "%")                dispatch({ type: "percent" })
    }

    window.addEventListener("keydown", handleKey)
    return () => window.removeEventListener("keydown", handleKey)
  }, [dispatch])

  return (
    <main className={styles.container}>
      <h1 className={styles.title}>Calculator</h1>
      <div className={styles.calc}>
        <Display
          expression={displayExpression}
          current={state.current}
          hasError={state.hasError}
        />
        <ButtonGrid onAction={dispatch} />
      </div>
    </main>
  )
}

export default App
```

```css
/* src/App.module.css */
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.title {
  font-size: 0.9rem;
  font-weight: 500;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--text-muted);
}

.calc {
  background: var(--surface-2);
  border-radius: 20px;
  padding: 1.25rem;
  width: 100%;
  max-width: 340px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.4);
}
```

---

## Step 9: Run and Test

```bash
npm run dev     # open http://localhost:5173
npm test        # verify Calculator logic tests pass
```

Test these scenarios manually:
- `7 + 8 = ` → 15
- `10 ÷ 0 =` → Error (then press AC to reset)
- Type a number then press keyboard `+` key
- Press `Escape` to clear
- `50 %` → 0.5
- Long number (12+ digits should not grow)

---

## Features Recap

| Feature | Implementation |
|---------|---------------|
| Basic operations | `Calculator.evaluate()` |
| Operator chaining | Evaluated before applying new operator |
| Repeat last operation | Re-applies `previous op current` on each `=` |
| Error handling | `DivisionByZeroError` → display "÷0 Error" |
| Keyboard input | `useEffect` with `keydown` listener |
| Dynamic font size | Inline `style={{ fontSize }}` from display length |
| Accessible | `aria-label` on buttons, `aria-live` on display |

---

*Previous: [06 — Styling in React](06-styling-in-react.md)*
*Next: [08 — Deploying Your App](08-deploying-your-app.md)*
