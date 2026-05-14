# 07 — Testing Basics: Writing Your First Tests

## Learning Objectives

By the end of this page, you will be able to:

- Explain why tests matter and what types of tests exist
- Set up Vitest for TypeScript testing
- Write unit tests with `describe`, `it`, and `expect`
- Use common matchers: `toBe`, `toEqual`, `toThrow`, `toBeTruthy`
- Test asynchronous code
- Understand test-driven development (TDD)
- Organize and run a test suite

---

## Why Test?

Without tests, every change you make carries risk. Did the refactor break something? Did the new feature break an old one? You won't know until a user reports a bug.

With tests, you know immediately. The feedback loop shrinks from hours (or weeks) to seconds.

> **Analogy:** Tests are like a safety net for a trapeze artist. You can still fall — but the net catches you before you hit the ground. Without the net, you walk the line extremely carefully, making small, hesitant moves. With the net, you can swing boldly because recovery from a mistake is cheap and quick.

### What Tests Give You

- **Confidence to refactor** — change internal implementation without fear if tests still pass
- **Documentation** — tests show exactly how functions should behave
- **Regression protection** — ensure a fixed bug never comes back
- **Design pressure** — code that's hard to test is often poorly designed
- **Fast feedback** — catch bugs immediately, not in production

---

## Types of Tests

| Type | Tests | Speed | Example |
|------|-------|-------|---------|
| **Unit** | Individual functions or classes in isolation | Very fast (ms) | "Does `validateEmail()` return false for `'notanemail'`?" |
| **Integration** | Multiple components working together | Medium | "Does submitting the form call the API with the right data?" |
| **End-to-End (E2E)** | The full application in a real browser | Slow (s) | "Does a user logging in and placing an order complete successfully?" |

This section focuses on **unit tests** — the foundation. Fast, focused, and the best first tool to learn.

---

## Setting Up Vitest

**Vitest** is a fast, modern test framework designed for TypeScript and modern JavaScript. It requires Node.js.

> **Note:** Full environment setup (Node.js, npm) is covered in Section 8. For now, you can run these examples using an online environment like [StackBlitz](https://stackblitz.com) or [CodeSandbox](https://codesandbox.io) — create a new Vite + TypeScript project.

### Install

```bash
npm install --save-dev vitest
```

Add to `package.json`:

```json
{
  "scripts": {
    "test":    "vitest",
    "test:run": "vitest run"
  }
}
```

- `npm test` — runs tests in watch mode (re-runs on file changes)
- `npm run test:run` — runs once and exits (for CI)

### Your First Test File

Create `src/math.ts`:

```typescript
export function add(a: number, b: number): number {
  return a + b
}

export function divide(a: number, b: number): number {
  if (b === 0) throw new Error("Cannot divide by zero")
  return a / b
}
```

Create `src/math.test.ts` (test files live next to the code they test):

```typescript
import { describe, it, expect } from "vitest"
import { add, divide } from "./math"

describe("add", () => {
  it("adds two positive numbers", () => {
    expect(add(2, 3)).toBe(5)
  })

  it("adds negative numbers", () => {
    expect(add(-1, -2)).toBe(-3)
  })

  it("returns the first number when adding zero", () => {
    expect(add(5, 0)).toBe(5)
  })
})

describe("divide", () => {
  it("divides two numbers", () => {
    expect(divide(10, 2)).toBe(5)
  })

  it("throws when dividing by zero", () => {
    expect(() => divide(10, 0)).toThrow("Cannot divide by zero")
  })
})
```

Run:
```bash
npm test
```

Output:
```
✓ src/math.test.ts
  ✓ add
    ✓ adds two positive numbers
    ✓ adds negative numbers
    ✓ returns the first number when adding zero
  ✓ divide
    ✓ divides two numbers
    ✓ throws when dividing by zero
```

---

## Test Structure: `describe`, `it`, `expect`

### `describe` — Group Related Tests

Groups tests for the same function or class. Descriptions appear in the output:

```typescript
describe("UserValidator", () => {
  describe("validateEmail", () => {
    it("...") // passes
    it("...") // fails
  })

  describe("validatePassword", () => {
    it("...") // passes
  })
})
```

### `it` (or `test`) — A Single Test Case

Each `it` should test one specific behavior. Name it as a sentence: "it [does something] when [condition]":

```typescript
it("returns null when the user does not exist", () => { ... })
it("throws AuthError when token is expired", () => { ... })
it("sends a welcome email after registration", () => { ... })
```

### `expect` — Make an Assertion

`expect(value).matcher(expected)` — asserts something about a value:

```typescript
expect(result).toBe(42)
```

If the assertion fails, the test fails with a helpful message:
```
AssertionError: expected 41 to be 42
```

---

## Common Matchers

### Equality

```typescript
// toBe — strict equality (===), used for primitives
expect(1 + 1).toBe(2)
expect("hello".toUpperCase()).toBe("HELLO")

// toEqual — deep equality, used for objects and arrays
expect({ a: 1, b: 2 }).toEqual({ a: 1, b: 2 })
expect([1, 2, 3]).toEqual([1, 2, 3])

// NOT: use .not. to negate any matcher
expect(2).not.toBe(3)
expect({}).not.toBe({})   // different references — passes
```

### Truthiness

```typescript
expect(true).toBeTruthy()
expect(1).toBeTruthy()
expect("hello").toBeTruthy()

expect(false).toBeFalsy()
expect(0).toBeFalsy()
expect(null).toBeFalsy()
expect("").toBeFalsy()

expect(null).toBeNull()
expect(undefined).toBeUndefined()
expect("hello").toBeDefined()
```

### Numbers

```typescript
expect(0.1 + 0.2).toBeCloseTo(0.3)   // for floating point
expect(5).toBeGreaterThan(3)
expect(5).toBeGreaterThanOrEqual(5)
expect(3).toBeLessThan(5)
```

### Strings

```typescript
expect("Hello World").toContain("World")
expect("Hello").toMatch(/^H/)           // regex match
expect("Hello").toMatch("ell")          // substring match
```

### Arrays

```typescript
expect([1, 2, 3]).toContain(2)
expect([1, 2, 3]).toHaveLength(3)
expect(["a", "b"]).toContainEqual("a")
```

### Objects

```typescript
const user = { id: 1, name: "Alex", email: "a@b.com" }

expect(user).toHaveProperty("name")
expect(user).toHaveProperty("name", "Alex")   // also checks value
expect(user).toMatchObject({ name: "Alex" })  // partial match — ignores id and email
```

### Errors

```typescript
// toThrow — test that a function throws
expect(() => divide(10, 0)).toThrow()
expect(() => divide(10, 0)).toThrow("Cannot divide by zero")
expect(() => divide(10, 0)).toThrow(Error)
```

---

## Testing TypeScript Classes

```typescript
// src/bank-account.ts
export class BankAccount {
  private balance: number

  constructor(initial: number = 0) {
    if (initial < 0) throw new Error("Initial balance cannot be negative")
    this.balance = initial
  }

  deposit(amount: number): void {
    if (amount <= 0) throw new Error("Deposit amount must be positive")
    this.balance += amount
  }

  withdraw(amount: number): void {
    if (amount <= 0) throw new Error("Withdrawal amount must be positive")
    if (amount > this.balance) throw new Error("Insufficient funds")
    this.balance -= amount
  }

  getBalance(): number {
    return this.balance
  }
}
```

```typescript
// src/bank-account.test.ts
import { describe, it, expect, beforeEach } from "vitest"
import { BankAccount } from "./bank-account"

describe("BankAccount", () => {
  let account: BankAccount

  // beforeEach creates a fresh account before every test — tests don't share state
  beforeEach(() => {
    account = new BankAccount(100)
  })

  describe("constructor", () => {
    it("initializes with the given balance", () => {
      const acct = new BankAccount(500)
      expect(acct.getBalance()).toBe(500)
    })

    it("defaults to 0 when no initial balance is given", () => {
      const acct = new BankAccount()
      expect(acct.getBalance()).toBe(0)
    })

    it("throws when initial balance is negative", () => {
      expect(() => new BankAccount(-100)).toThrow("Initial balance cannot be negative")
    })
  })

  describe("deposit", () => {
    it("increases the balance by the deposited amount", () => {
      account.deposit(50)
      expect(account.getBalance()).toBe(150)
    })

    it("allows multiple deposits", () => {
      account.deposit(50)
      account.deposit(25)
      expect(account.getBalance()).toBe(175)
    })

    it("throws when deposit amount is zero", () => {
      expect(() => account.deposit(0)).toThrow("Deposit amount must be positive")
    })

    it("throws when deposit amount is negative", () => {
      expect(() => account.deposit(-10)).toThrow("Deposit amount must be positive")
    })
  })

  describe("withdraw", () => {
    it("decreases the balance", () => {
      account.withdraw(30)
      expect(account.getBalance()).toBe(70)
    })

    it("throws when balance is insufficient", () => {
      expect(() => account.withdraw(200)).toThrow("Insufficient funds")
    })

    it("allows withdrawing the exact balance", () => {
      account.withdraw(100)
      expect(account.getBalance()).toBe(0)
    })
  })
})
```

---

## Setup and Teardown

```typescript
import { describe, it, expect, beforeEach, afterEach, beforeAll, afterAll } from "vitest"

describe("Database tests", () => {
  beforeAll(async () => {
    // Runs once before all tests in this describe block
    await db.connect()
  })

  afterAll(async () => {
    // Runs once after all tests in this describe block
    await db.disconnect()
  })

  beforeEach(() => {
    // Runs before EACH test
    db.clearTestData()
  })

  afterEach(() => {
    // Runs after EACH test
    jest.clearAllMocks()
  })
})
```

---

## Testing Async Code

```typescript
import { describe, it, expect } from "vitest"
import { fetchUser } from "./user-service"

describe("fetchUser", () => {
  it("returns a user when found", async () => {
    const user = await fetchUser(1)
    expect(user).toMatchObject({
      id: 1,
      name: expect.any(String)   // we don't know the exact name, but it must be a string
    })
  })

  it("throws when user is not found", async () => {
    await expect(fetchUser(99999)).rejects.toThrow("User not found")
    //    ↑ await the assertion for async throws
  })
})
```

---

## Test-Driven Development (TDD)

**TDD** is a workflow where you write the test *before* writing the implementation:

```
Red → Green → Refactor

1. RED:    Write a failing test (the feature doesn't exist yet)
2. GREEN:  Write the minimum code to make the test pass
3. REFACTOR: Clean up the code, keep tests green
```

### TDD Example

**Step 1 — Write the test first (RED):**

```typescript
// formatCurrency.test.ts
import { formatCurrency } from "./formatCurrency"

it("formats 1234.5 as $1,234.50", () => {
  expect(formatCurrency(1234.5)).toBe("$1,234.50")
})
```

Run the test — it fails because `formatCurrency` doesn't exist yet.

**Step 2 — Write minimum code (GREEN):**

```typescript
// formatCurrency.ts
export function formatCurrency(amount: number): string {
  return "$" + amount.toLocaleString("en-US", {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  })
}
```

Test passes. ✓

**Step 3 — Add more cases, refactor:**

```typescript
it("handles zero", () => expect(formatCurrency(0)).toBe("$0.00"))
it("handles negative amounts", () => expect(formatCurrency(-50)).toBe("-$50.00"))
```

TDD drives you to think about edge cases *before* coding, and gives you a test suite automatically.

---

## What NOT to Test

- Trivial getters that only return a private field
- Third-party library behavior (test your code, not theirs)
- Implementation details — test behavior, not how it's achieved internally
- Every possible input combination — focus on boundary cases and representative inputs

---

## Check Your Understanding

1. What is the difference between a unit test and an integration test?
2. What is `beforeEach` used for? Why is it important that tests don't share state?
3. What does `expect(() => fn()).toThrow()` test? Why is the function wrapped in an arrow function?
4. What does `toEqual` do differently than `toBe`?
5. Describe the TDD Red-Green-Refactor cycle. Why might writing tests first improve code design?

---

*Previous: [06 — Documentation and Style](06-documentation-and-style.md)*
*Next: [08 — Error Handling in TypeScript](08-error-handling-typescript.md)*
