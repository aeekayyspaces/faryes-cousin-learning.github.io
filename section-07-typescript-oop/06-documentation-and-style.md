# 06 — Commenting, Documentation, and Code Style

## Learning Objectives

By the end of this page, you will be able to:

- Explain when comments add value and when they create noise
- Write JSDoc/TSDoc comments for functions and classes
- Set up Prettier for automatic code formatting
- Configure ESLint for TypeScript
- Apply consistent naming conventions
- Organize a TypeScript file effectively

---

## The Philosophy of Code Comments

The first principle of good commenting: **code should explain itself**.

Well-named variables, functions, and types are self-documenting:

```typescript
// ❌ Comment explains WHAT — the code already says that
// Multiply by 0.08 to get tax
const tax = price * 0.08

// ✓ No comment needed — reads like English
const tax = price * TAX_RATE

// ❌ Comment explains WHAT a loop does
// Loop through users and check active status
for (const user of users) { ... }

// ✓ No comment needed
const activeUsers = users.filter(user => user.isActive)
```

### When Comments ARE Valuable

**1. Explaining WHY, not WHAT:**

```typescript
// Using 0.1s delay here rather than immediate because the browser
// needs a frame to complete the CSS transition before focus management.
// Removing this delay causes a visual flicker on Safari.
setTimeout(() => modal.focus(), 100)
```

**2. Non-obvious constraints or workarounds:**

```typescript
// parseInt is used instead of Number() here because the API
// returns mixed values like "42px" that Number() would parse as NaN.
const value = parseInt(raw, 10)
```

**3. Documenting a complex algorithm:**

```typescript
// Binary search: compare target to midpoint, eliminate half the array
// each iteration. O(log n) time complexity.
```

**4. TODO/FIXME markers:**

```typescript
// TODO: Replace with proper authentication once auth service is ready
// FIXME: This crashes when inventory.items is null — needs null check
// HACK: Temporary workaround for upstream bug #1234 — remove after v2 upgrade
```

---

## JSDoc and TSDoc

**JSDoc** comments start with `/**` (two stars) and appear directly above a function, class, or property. Your editor reads them and shows them in autocomplete tooltips.

**TSDoc** is a superset of JSDoc designed for TypeScript — the same syntax, with some extensions.

### Function Documentation

```typescript
/**
 * Calculates the compound interest on a principal amount.
 *
 * @param principal - The starting amount in dollars
 * @param rate      - Annual interest rate as a decimal (e.g., 0.05 for 5%)
 * @param years     - Number of years the money compounds
 * @param n         - Number of times interest compounds per year (default: 12)
 * @returns         The total amount including interest
 *
 * @example
 * ```typescript
 * compoundInterest(1000, 0.05, 10)    // 1647.01
 * compoundInterest(500, 0.03, 5, 4)  // 580.42
 * ```
 */
function compoundInterest(
  principal: number,
  rate: number,
  years: number,
  n: number = 12
): number {
  return principal * Math.pow(1 + rate / n, n * years)
}
```

### Class Documentation

```typescript
/**
 * Manages a collection of tasks with support for filtering and statistics.
 *
 * @example
 * ```typescript
 * const manager = new TaskManager()
 * manager.add("Write tests")
 * manager.add("Deploy to production")
 * manager.completeById(1)
 * console.log(manager.stats)  // { total: 2, completed: 1, pending: 1 }
 * ```
 */
class TaskManager {
  /** @internal */
  private tasks: Task[] = []

  /**
   * Adds a new task to the manager.
   * @param title       - Short description of the task
   * @param priority    - Task priority level (default: "medium")
   * @returns           The newly created Task
   * @throws {Error}    If title is empty
   */
  add(title: string, priority: Priority = "medium"): Task {
    if (!title.trim()) throw new Error("Task title cannot be empty")
    const task = new Task(this.nextId++, title, priority)
    this.tasks.push(task)
    return task
  }

  /**
   * Number of tasks in each status category.
   * @readonly
   */
  get stats(): TaskStats {
    return {
      total: this.tasks.length,
      completed: this.tasks.filter(t => t.completed).length,
      pending: this.tasks.filter(t => !t.completed).length
    }
  }
}
```

### Common TSDoc Tags

| Tag | Purpose |
|-----|---------|
| `@param name - description` | Document a parameter |
| `@returns description` | Document the return value |
| `@throws {ErrorType} condition` | Document when an error is thrown |
| `@example` | Code example (in a fenced code block) |
| `@deprecated message` | Mark as deprecated — editor shows strikethrough |
| `@since version` | When the API was introduced |
| `@see {@link OtherClass}` | Link to related documentation |
| `@internal` | Mark as not part of the public API |
| `@remarks` | Additional details beyond the summary |

---

## Prettier — Automatic Formatting

**Prettier** reformats your code automatically — consistent indentation, quote style, line length — without debate. You never manually format again.

### Setup in a Project

```bash
npm install --save-dev prettier
```

Create `.prettierrc` in the project root:

```json
{
  "semi": false,
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "trailingComma": "es5",
  "arrowParens": "avoid"
}
```

Run on a file or directory:

```bash
npx prettier --write src/
npx prettier --check src/   # check without changing (useful in CI)
```

### VS Code Integration

Install the **Prettier - Code formatter** extension. Add to `.vscode/settings.json`:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true
}
```

Now your code auto-formats every time you save. No more manual alignment.

---

## ESLint — Catching Code Quality Issues

**ESLint** analyzes your code for bugs, style issues, and bad practices. It's more than formatting — it catches logic problems too.

### Setup for TypeScript

```bash
npm install --save-dev eslint @eslint/js typescript-eslint
```

Create `eslint.config.mjs`:

```javascript
import eslint from "@eslint/js"
import tseslint from "typescript-eslint"

export default tseslint.config(
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
  {
    rules: {
      "@typescript-eslint/no-explicit-any": "error",      // no any
      "@typescript-eslint/no-unused-vars": "warn",         // unused variables
      "@typescript-eslint/explicit-function-return-type": "off",
      "no-console": "warn",                                // warn on console.log
      "prefer-const": "error",                             // use const when possible
    }
  }
)
```

Run:

```bash
npx eslint src/
npx eslint src/ --fix   # auto-fix fixable issues
```

---

## Naming Conventions

Consistent naming makes code predictable and readable:

### Variables and Functions — `camelCase`

```typescript
const userName = "Alex"
let currentScore = 0
const isLoggedIn = true

function getUserById(id: number): User | null { ... }
const handleSubmit = (e: Event): void => { ... }
```

### Classes and Types — `PascalCase`

```typescript
class BankAccount { ... }
interface UserProfile { ... }
type PaymentMethod = "card" | "cash" | "crypto"
enum Direction { North, South, East, West }
```

### Constants — `SCREAMING_SNAKE_CASE` (for true constants)

```typescript
const MAX_RETRY_ATTEMPTS = 3
const API_BASE_URL = "https://api.example.com"
const TAX_RATE = 0.08
```

### Private Members — No Forced Convention

TypeScript's `private` keyword makes `_prefix` conventions unnecessary:

```typescript
// ✓ Just use private keyword — no underscore needed
class Counter {
  private count: number = 0
}

// Common convention when using a getter/setter pair:
class Temperature {
  private _celsius: number = 0  // backing field uses _ to distinguish from getter name
  get celsius(): number { return this._celsius }
  set celsius(v: number) { this._celsius = v }
}
```

### Files — `kebab-case.ts`

```
bank-account.ts
task-manager.ts
user-service.ts
auth-utils.ts
```

### Boolean Variables — `is`, `has`, `can`, `should` prefix

```typescript
const isLoggedIn = true
const hasPermission = false
const canEdit = user.role === "admin"
const shouldNotify = preferences.notifications === "all"
```

---

## File Organization

A well-organized TypeScript file:

```typescript
// 1. Imports (external first, then internal)
import { z } from "zod"
import type { User, UserRole } from "./types"
import { validateEmail } from "./utils"

// 2. Constants
const DEFAULT_ROLE: UserRole = "user"
const MAX_NAME_LENGTH = 100

// 3. Types and interfaces specific to this module
interface CreateUserOptions {
  sendWelcomeEmail?: boolean
}

// 4. The main class or function(s)
export class UserService {
  // ...
}

// 5. Helper functions (private/unexported)
function sanitizeName(name: string): string {
  return name.trim().slice(0, MAX_NAME_LENGTH)
}
```

### Keep Files Focused

Each file should have one primary responsibility:

```
src/
├── models/
│   ├── user.ts           ← User class + related types
│   ├── task.ts           ← Task class + related types
│   └── index.ts          ← re-export barrel
├── services/
│   ├── user-service.ts   ← business logic for users
│   └── task-service.ts
├── utils/
│   ├── validation.ts     ← validation helpers
│   └── formatting.ts     ← display formatting
└── types/
    └── index.ts          ← shared TypeScript types
```

---

## A Note on Over-Documentation

Don't document what's obvious:

```typescript
// ❌ Noise — the code already says exactly this
/** Gets the user's name. */
get name(): string { return this._name }

/** @param amount The amount to add */
deposit(amount: number): void { ... }

// ✓ Document the non-obvious:
/**
 * Deposits funds, but silently ignores deposits below $0.01 to avoid
 * floating-point rounding issues in ledger reconciliation.
 */
deposit(amount: number): void { ... }
```

Good documentation focuses on **intent, constraints, side effects, and exceptions** — things a reader can't immediately derive from reading the code.

**Further Reading:**
- [TSDoc — Official Documentation](https://tsdoc.org/)
- [Prettier — Why Prettier?](https://prettier.io/docs/en/why-prettier)
- [ESLint — Getting Started](https://eslint.org/docs/latest/use/getting-started)
- [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html)

---

## Check Your Understanding

1. What is the difference between a `/* */` comment and a `/** */` JSDoc comment?
2. What does Prettier do that ESLint does NOT?
3. What naming convention should you use for a TypeScript class? For a boolean variable?
4. When should you write a comment explaining WHY instead of WHAT?
5. You have a function that always throws an error if the input is empty. Which JSDoc tag documents this?

---

*Previous: [05 — Inheritance and Interfaces](05-inheritance-and-interfaces.md)*
*Next: [07 — Testing Basics](07-testing-basics.md)*
