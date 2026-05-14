# 09 — Section Quiz and Checkpoints

## Section 7 Learning Objectives Review

Before taking this quiz, confirm you can:

- [ ] Explain what TypeScript is and the problem it solves
- [ ] Annotate variables, function parameters, and return types
- [ ] Use type inference, union types, literal types, and generics
- [ ] Distinguish between `any`, `unknown`, `never`, and `void`
- [ ] Define interfaces and type aliases and explain when to use each
- [ ] Write a class with a constructor, typed fields, and methods
- [ ] Apply `public`, `private`, `protected`, and `readonly` correctly
- [ ] Use constructor parameter shorthand
- [ ] Extend a class, call `super`, and override methods
- [ ] Write and implement an interface
- [ ] Create and use an abstract class
- [ ] Write JSDoc comments for functions and classes
- [ ] Set up and run Vitest tests with `describe`, `it`, and `expect`
- [ ] Use `Result<T>` and discriminated unions for typed error handling

---

## Part 1: Multiple Choice

Answer key: [solutions/09-quiz-solutions.md](solutions/09-quiz-solutions.md)

---

**1.** TypeScript compiles to:

- A) Machine code that runs directly on the CPU
- B) WebAssembly
- C) JavaScript that runs in browsers and Node.js
- D) A custom bytecode that requires the TypeScript runtime

---

**2.** Which annotation correctly types a function that takes a string array and returns a boolean?

- A) `function check(items: string[]): boolean`
- B) `function check(items: Array<string>) -> bool`
- C) `function check(items: [string]) => boolean`
- D) `function check(items: string[]) -> boolean`

---

**3.** What does TypeScript infer as the type of `result` here?

```typescript
const result = [1, 2, 3].map(n => n.toString())
```

- A) `any[]`
- B) `string[]`
- C) `number[]`
- D) `(string | number)[]`

---

**4.** What is the difference between `unknown` and `any`?

- A) `any` disables all type checking; `unknown` requires you to narrow the type before using it
- B) `unknown` is for function return types only; `any` can be used anywhere
- C) They are identical — `unknown` is just a newer name for `any`
- D) `unknown` allows all operations; `any` requires type assertions first

---

**5.** What is the output of this TypeScript code?

```typescript
class Counter {
  private static count = 0
  readonly id: number

  constructor() {
    Counter.count++
    this.id = Counter.count
  }

  static getCount(): number { return Counter.count }
}

const a = new Counter()
const b = new Counter()
console.log(Counter.getCount())
```

- A) `0`
- B) `1`
- C) `2`
- D) `undefined`

---

**6.** Which access modifier allows a subclass to access a member but prevents external code from doing so?

- A) `public`
- B) `private`
- C) `protected`
- D) `readonly`

---

**7.** What does the TypeScript constructor shorthand do?

```typescript
class Point {
  constructor(public x: number, private y: number) {}
}
```

- A) Creates a function named `Point` that returns `{ x, y }`
- B) Declares class properties `x` and `y` and assigns the constructor arguments to them automatically
- C) Creates `x` and `y` as local variables inside the constructor only
- D) Copies the arguments into a separate `args` object

---

**8.** What is structural typing in TypeScript?

- A) Types must be explicitly declared with `class` or `interface` to be compatible
- B) A type is compatible with another if it has at least the required properties, regardless of name
- C) Types are organized in a strict hierarchy from primitive to complex
- D) Only named types created with `type` keyword can be used structurally

---

**9.** What error does the `override` keyword help catch?

- A) Accidentally using a method that belongs to a different class
- B) Accidentally shadowing a method that doesn't exist in the parent class
- C) Using a deprecated method
- D) Calling a private method from outside the class

---

**10.** In Vitest, what is the purpose of `beforeEach`?

- A) Runs once before all tests in the file
- B) Runs before each test in the current `describe` block, ensuring a clean state for every test
- C) Imports test dependencies before the test suite starts
- D) Marks a test as required — it must pass before other tests can run

---

**11.** What does `expect(() => fn()).toThrow()` test that `expect(fn()).toThrow()` would not?

- A) Nothing — they are equivalent
- B) The arrow function wrapper prevents the error from propagating to Vitest's runner, so `.toThrow()` can catch and assert on it
- C) The arrow function makes the test asynchronous
- D) Only the wrapped version tests TypeScript typed errors

---

**12.** In this Result type pattern, what does TypeScript know about `result.data` if you access it directly without checking `result.success` first?

```typescript
type Result<T> = { success: true; data: T } | { success: false; error: string }
```

- A) It's typed as `T`
- B) It's typed as `T | undefined`
- C) TypeScript errors — `data` only exists on the success variant, not on the union
- D) It's typed as `unknown`

---

**13.** Which JSDoc tag marks a parameter as documented?

- A) `@arg`
- B) `@parameter`
- C) `@param`
- D) `@input`

---

**14.** What is a discriminated union?

- A) A union of classes that share the same interface
- B) A union of object types where each variant has a common literal-typed field used for narrowing
- C) A union type where one variant is always an error type
- D) A union of primitive types like `string | number`

---

**15.** What naming convention should a TypeScript interface use?

- A) `snake_case` like `user_profile`
- B) `SCREAMING_SNAKE_CASE` like `USER_PROFILE`
- C) `PascalCase` like `UserProfile`
- D) `camelCase` like `userProfile`

---

## Part 2: Short Answer

Suggested answers: [solutions/09-quiz-solutions.md](solutions/09-quiz-solutions.md)

**1.** Explain the four pillars of OOP (Encapsulation, Abstraction, Inheritance, Polymorphism) using a single consistent analogy of your choosing (not a car or vending machine).

**2.** What is the difference between an abstract class and an interface? Give a scenario where you'd choose each.

**3.** When would you use a `Result<T>` type instead of throwing an exception? What advantages does it provide?

**4.** Explain how `never` is used for exhaustive type checking in a switch statement. What happens when you add a new case to the union without updating the switch?

**5.** Describe the TDD Red-Green-Refactor cycle. Write a test (failing, just the `it` block) for a function `clamp(value, min, max)` that constrains a number within a range.

---

## Part 3: Code Challenges

Solutions: [solutions/code-challenges-solutions.md](solutions/code-challenges-solutions.md)

> **Environment:** Use the [TypeScript Playground](https://www.typescriptlang.org/play) for quick experiments. For running tests (Challenge 4), use a local Node.js setup or [StackBlitz](https://stackblitz.com) with a Vite + TypeScript template.

---

### Challenge 1: A Type-Safe Shopping Cart

Build a type-safe shopping cart using TypeScript classes and interfaces.

**Requirements:**

```typescript
interface CartItem {
  productId: string
  name: string
  price: number
  quantity: number
}

class ShoppingCart {
  // Implement:
  // - addItem(product: { id, name, price }, quantity: number): void
  //   - If the product already exists, increase its quantity
  // - removeItem(productId: string): void
  //   - Throws NotFoundError if productId doesn't exist
  // - updateQuantity(productId: string, quantity: number): void
  //   - Throws NotFoundError if productId doesn't exist
  //   - Removes the item if quantity <= 0
  // - clear(): void
  // - getItems(): readonly CartItem[]
  // - get itemCount(): number  (total quantity of all items)
  // - get subtotal(): number   (sum of price * quantity)
  // - get isEmpty(): boolean
}
```

Test it with:
```typescript
const cart = new ShoppingCart()
cart.addItem({ id: "laptop", name: "ThinkPad", price: 999 }, 1)
cart.addItem({ id: "mouse", name: "Logitech MX", price: 79 }, 2)
cart.addItem({ id: "laptop", name: "ThinkPad", price: 999 }, 1)  // should add to existing

console.log(cart.itemCount)   // 4 (2 laptops + 2 mice)
console.log(cart.subtotal)    // 2156 (999*2 + 79*2)
```

---

### Challenge 2: Shape Hierarchy

Implement a complete shape hierarchy with TypeScript.

```typescript
// Base:  abstract class Shape with abstract area() and perimeter()
// Also:  a describe() method that returns a string (not abstract — shared)

// Implement:
//   - Circle(radius: number)
//   - Rectangle(width: number, height: number)
//   - Triangle(base: number, height: number, sideA: number, sideB: number, sideC: number)
//   - RegularPolygon(sides: number, sideLength: number)
//     area = (sides * sideLength² * cot(π/sides)) / 4

// Also write:
//   - function totalArea(shapes: Shape[]): number
//   - function largestShape(shapes: Shape[]): Shape
```

Include a `static fromJSON(data: unknown)` factory on each shape that uses `instanceof Error` to guard against invalid input.

---

### Challenge 3: Generic Data Structures

Implement two type-safe generic data structures:

**Stack\<T\>** — Last In, First Out (LIFO):
```typescript
class Stack<T> {
  push(item: T): void
  pop(): T          // throws if empty
  peek(): T         // throws if empty
  isEmpty(): boolean
  get size(): number
  toArray(): T[]
}
```

**Queue\<T\>** — First In, First Out (FIFO):
```typescript
class Queue<T> {
  enqueue(item: T): void
  dequeue(): T      // throws if empty
  front(): T        // peek without removing — throws if empty
  isEmpty(): boolean
  get size(): number
}
```

Test with at least three different types: `Stack<number>`, `Stack<string>`, `Queue<{ id: number; name: string }>`.

---

### Challenge 4: Write Tests for a Validator

Given this validator:

```typescript
// validator.ts
export type ValidationResult = { valid: true } | { valid: false; errors: string[] }

export function validateRegistration(data: {
  username: string
  email: string
  password: string
  confirmPassword: string
  age: number
}): ValidationResult {
  const errors: string[] = []

  if (!data.username || data.username.length < 3) {
    errors.push("Username must be at least 3 characters")
  }
  if (!/^[a-zA-Z0-9_]+$/.test(data.username)) {
    errors.push("Username may only contain letters, numbers, and underscores")
  }
  if (!data.email.includes("@")) {
    errors.push("Invalid email address")
  }
  if (data.password.length < 8) {
    errors.push("Password must be at least 8 characters")
  }
  if (data.password !== data.confirmPassword) {
    errors.push("Passwords do not match")
  }
  if (data.age < 18) {
    errors.push("Must be 18 or older")
  }

  return errors.length === 0
    ? { valid: true }
    : { valid: false, errors }
}
```

Write a complete Vitest test suite with at least:
- One test for a fully valid input
- At least 6 tests for individual validation failures
- One test for multiple simultaneous failures

---

### Challenge 5: The Calculator App (TypeScript OOP Version)

Build a calculator class that will serve as the logic layer for the React calculator in Section 8.

```typescript
type Operation = "+" | "-" | "*" | "/"

interface CalculationRecord {
  operandA: number
  operator: Operation
  operandB: number
  result: number
  timestamp: Date
}

class Calculator {
  // Implement:
  // - add(a, b): number
  // - subtract(a, b): number
  // - multiply(a, b): number
  // - divide(a, b): number — throws DivisionByZeroError for b === 0
  // - calculate(a: number, op: Operation, b: number): number
  // - get history(): readonly CalculationRecord[]
  // - clearHistory(): void
  // - lastResult(): number | null  — returns null if no calculations yet

  // BONUS:
  // - percentage(value: number): number  — value / 100
  // - squareRoot(value: number): number  — throws for negative input
  // - negate(value: number): number
}

class DivisionByZeroError extends Error {
  constructor() {
    super("Cannot divide by zero")
    this.name = "DivisionByZeroError"
  }
}
```

Write at least 10 Vitest tests covering all operations, edge cases, and the history feature.

---

## Completion Checklist

- [ ] I can explain what TypeScript is and why it's used
- [ ] I can annotate types in all common situations
- [ ] I understand `any` vs `unknown` and use `unknown` in catch blocks
- [ ] I can write a class with full TypeScript typing
- [ ] I understand and can apply all four access modifiers
- [ ] I can use abstract classes and interfaces to define contracts
- [ ] I write JSDoc comments for public functions and classes
- [ ] I can set up Vitest and write unit tests with `describe`, `it`, `expect`
- [ ] I can write typed custom error classes
- [ ] I understand and can implement the `Result<T>` pattern
- [ ] I completed all five code challenges

---

*Ready for the next section? → [Section 8: React and Building Your First App](../section-08-react/01-environment-setup.md)*

*Review sources: [10 — Sources](10-sources.md)*
