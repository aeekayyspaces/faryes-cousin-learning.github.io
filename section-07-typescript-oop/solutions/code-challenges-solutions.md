# Solutions — Code Challenges

Reference solutions for [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md).

---

## Challenge 1: Type-Safe Shopping Cart

```typescript
// cart.ts

class NotFoundError extends Error {
  constructor(productId: string) {
    super(`Product '${productId}' not found in cart`)
    this.name = "NotFoundError"
  }
}

interface CartItem {
  productId: string
  name: string
  price: number
  quantity: number
}

interface Product {
  id: string
  name: string
  price: number
}

class ShoppingCart {
  private items: Map<string, CartItem> = new Map()

  addItem(product: Product, quantity: number = 1): void {
    if (quantity <= 0) throw new RangeError("Quantity must be positive")

    const existing = this.items.get(product.id)
    if (existing) {
      existing.quantity += quantity
    } else {
      this.items.set(product.id, {
        productId: product.id,
        name: product.name,
        price: product.price,
        quantity
      })
    }
  }

  removeItem(productId: string): void {
    if (!this.items.has(productId)) throw new NotFoundError(productId)
    this.items.delete(productId)
  }

  updateQuantity(productId: string, quantity: number): void {
    if (!this.items.has(productId)) throw new NotFoundError(productId)
    if (quantity <= 0) {
      this.items.delete(productId)
    } else {
      this.items.get(productId)!.quantity = quantity
    }
  }

  clear(): void {
    this.items.clear()
  }

  getItems(): readonly CartItem[] {
    return Array.from(this.items.values())
  }

  get itemCount(): number {
    let total = 0
    for (const item of this.items.values()) total += item.quantity
    return total
  }

  get subtotal(): number {
    let total = 0
    for (const item of this.items.values()) total += item.price * item.quantity
    return total
  }

  get isEmpty(): boolean {
    return this.items.size === 0
  }
}

// Test:
const cart = new ShoppingCart()
cart.addItem({ id: "laptop", name: "ThinkPad", price: 999 }, 1)
cart.addItem({ id: "mouse", name: "Logitech MX", price: 79 }, 2)
cart.addItem({ id: "laptop", name: "ThinkPad", price: 999 }, 1)  // adds to existing

console.log(cart.itemCount)   // 4
console.log(cart.subtotal)    // 2156
console.log(cart.getItems())
// [{ productId: "laptop", name: "ThinkPad", price: 999, quantity: 2 },
//  { productId: "mouse", name: "Logitech MX", price: 79, quantity: 2 }]
```

**Key design decisions:**
- `Map<string, CartItem>` for O(1) lookup by productId — better than an array for this use case
- `getItems()` returns `readonly CartItem[]` — callers can't mutate the internal collection
- Quantity update of `<= 0` removes the item — consistent with cart UX
- `NotFoundError` extends `Error` with a descriptive name for `instanceof` checks

---

## Challenge 2: Shape Hierarchy

```typescript
// shapes.ts

abstract class Shape {
  abstract area(): number
  abstract perimeter(): number

  describe(): string {
    return `${this.constructor.name}: area=${this.area().toFixed(2)}, perimeter=${this.perimeter().toFixed(2)}`
  }
}

class Circle extends Shape {
  constructor(public readonly radius: number) {
    super()
    if (radius <= 0) throw new RangeError("Radius must be positive")
  }

  area(): number { return Math.PI * this.radius ** 2 }
  perimeter(): number { return 2 * Math.PI * this.radius }

  static fromJSON(data: unknown): Circle {
    if (typeof data !== "object" || data === null || !("radius" in data))
      throw new TypeError("Invalid Circle data")
    return new Circle((data as { radius: number }).radius)
  }
}

class Rectangle extends Shape {
  constructor(
    public readonly width: number,
    public readonly height: number
  ) {
    super()
    if (width <= 0 || height <= 0) throw new RangeError("Dimensions must be positive")
  }

  area(): number { return this.width * this.height }
  perimeter(): number { return 2 * (this.width + this.height) }

  static fromJSON(data: unknown): Rectangle {
    if (typeof data !== "object" || data === null) throw new TypeError("Invalid Rectangle data")
    const { width, height } = data as { width: number; height: number }
    return new Rectangle(width, height)
  }
}

class Triangle extends Shape {
  constructor(
    public readonly base: number,
    public readonly height: number,
    public readonly sideA: number,
    public readonly sideB: number,
    public readonly sideC: number
  ) { super() }

  area(): number { return 0.5 * this.base * this.height }
  perimeter(): number { return this.sideA + this.sideB + this.sideC }
}

class RegularPolygon extends Shape {
  constructor(
    public readonly sides: number,
    public readonly sideLength: number
  ) {
    super()
    if (sides < 3) throw new RangeError("Polygon must have at least 3 sides")
  }

  area(): number {
    return (this.sides * this.sideLength ** 2) / (4 * Math.tan(Math.PI / this.sides))
  }
  perimeter(): number { return this.sides * this.sideLength }
}

function totalArea(shapes: Shape[]): number {
  return shapes.reduce((sum, shape) => sum + shape.area(), 0)
}

function largestShape(shapes: Shape[]): Shape {
  if (shapes.length === 0) throw new Error("No shapes provided")
  return shapes.reduce((largest, shape) =>
    shape.area() > largest.area() ? shape : largest
  )
}

// Test:
const shapes: Shape[] = [
  new Circle(5),
  new Rectangle(4, 6),
  new Triangle(3, 4, 5, 5, 5),
  new RegularPolygon(6, 4)
]

shapes.forEach(s => console.log(s.describe()))
console.log("Total area:", totalArea(shapes).toFixed(2))
console.log("Largest:", largestShape(shapes).constructor.name)
```

---

## Challenge 3: Generic Data Structures

```typescript
// stack.ts

class EmptyCollectionError extends Error {
  constructor(collection: string) {
    super(`Cannot perform operation on empty ${collection}`)
    this.name = "EmptyCollectionError"
  }
}

class Stack<T> {
  private items: T[] = []

  push(item: T): void {
    this.items.push(item)
  }

  pop(): T {
    if (this.isEmpty) throw new EmptyCollectionError("Stack")
    return this.items.pop()!
  }

  peek(): T {
    if (this.isEmpty) throw new EmptyCollectionError("Stack")
    return this.items[this.items.length - 1]
  }

  get isEmpty(): boolean { return this.items.length === 0 }
  get size(): number { return this.items.length }

  toArray(): T[] { return [...this.items] }
}

class Queue<T> {
  private items: T[] = []

  enqueue(item: T): void {
    this.items.push(item)
  }

  dequeue(): T {
    if (this.isEmpty) throw new EmptyCollectionError("Queue")
    return this.items.shift()!
  }

  front(): T {
    if (this.isEmpty) throw new EmptyCollectionError("Queue")
    return this.items[0]
  }

  get isEmpty(): boolean { return this.items.length === 0 }
  get size(): number { return this.items.length }
}

// Tests:
const numStack = new Stack<number>()
numStack.push(1); numStack.push(2); numStack.push(3)
console.log(numStack.pop())   // 3 (LIFO)
console.log(numStack.peek())  // 2
console.log(numStack.size)    // 2

const strStack = new Stack<string>()
strStack.push("a"); strStack.push("b")
console.log(strStack.toArray())  // ["a", "b"]

const queue = new Queue<{ id: number; name: string }>()
queue.enqueue({ id: 1, name: "Alice" })
queue.enqueue({ id: 2, name: "Bob" })
console.log(queue.dequeue().name)  // "Alice" (FIFO)
console.log(queue.front().name)    // "Bob"

// Error cases:
const empty = new Stack<number>()
try { empty.pop() } catch (e) {
  console.log(e instanceof EmptyCollectionError)  // true
}
```

---

## Challenge 4: Validator Test Suite

```typescript
// validator.test.ts
import { describe, it, expect } from "vitest"
import { validateRegistration } from "./validator"

const valid = {
  username: "alex_99",
  email: "alex@example.com",
  password: "SecurePass1",
  confirmPassword: "SecurePass1",
  age: 25
}

describe("validateRegistration", () => {

  it("returns valid for correct input", () => {
    const result = validateRegistration(valid)
    expect(result.valid).toBe(true)
  })

  describe("username validation", () => {
    it("rejects username shorter than 3 characters", () => {
      const result = validateRegistration({ ...valid, username: "ab" })
      expect(result.valid).toBe(false)
      if (!result.valid) {
        expect(result.errors).toContain("Username must be at least 3 characters")
      }
    })

    it("rejects username with special characters", () => {
      const result = validateRegistration({ ...valid, username: "alex!" })
      expect(result.valid).toBe(false)
    })

    it("accepts username with underscores", () => {
      const result = validateRegistration({ ...valid, username: "alex_99" })
      expect(result.valid).toBe(true)
    })
  })

  describe("email validation", () => {
    it("rejects email without @", () => {
      const result = validateRegistration({ ...valid, email: "notanemail" })
      expect(result.valid).toBe(false)
      if (!result.valid) {
        expect(result.errors).toContain("Invalid email address")
      }
    })
  })

  describe("password validation", () => {
    it("rejects password shorter than 8 characters", () => {
      const result = validateRegistration({ ...valid, password: "short", confirmPassword: "short" })
      expect(result.valid).toBe(false)
      if (!result.valid) {
        expect(result.errors).toContain("Password must be at least 8 characters")
      }
    })

    it("rejects mismatched passwords", () => {
      const result = validateRegistration({ ...valid, confirmPassword: "DifferentPass1" })
      expect(result.valid).toBe(false)
      if (!result.valid) {
        expect(result.errors).toContain("Passwords do not match")
      }
    })
  })

  describe("age validation", () => {
    it("rejects age below 18", () => {
      const result = validateRegistration({ ...valid, age: 17 })
      expect(result.valid).toBe(false)
      if (!result.valid) {
        expect(result.errors).toContain("Must be 18 or older")
      }
    })

    it("accepts exactly age 18", () => {
      const result = validateRegistration({ ...valid, age: 18 })
      expect(result.valid).toBe(true)
    })
  })

  it("returns multiple errors when multiple fields fail", () => {
    const result = validateRegistration({
      username: "x",       // too short
      email: "bademail",   // no @
      password: "short",   // too short
      confirmPassword: "different",  // no match
      age: 16              // under 18
    })
    expect(result.valid).toBe(false)
    if (!result.valid) {
      expect(result.errors.length).toBeGreaterThanOrEqual(3)
    }
  })
})
```

---

## Challenge 5: Calculator Class

```typescript
// calculator.ts

type Operation = "+" | "-" | "*" | "/"

interface CalculationRecord {
  operandA: number
  operator: Operation
  operandB: number
  result: number
  timestamp: Date
}

class DivisionByZeroError extends Error {
  constructor() {
    super("Cannot divide by zero")
    this.name = "DivisionByZeroError"
    Object.setPrototypeOf(this, new.target.prototype)
  }
}

class NegativeSquareRootError extends Error {
  constructor(value: number) {
    super(`Cannot take square root of negative number: ${value}`)
    this.name = "NegativeSquareRootError"
  }
}

class Calculator {
  private _history: CalculationRecord[] = []

  private record(a: number, op: Operation, b: number, result: number): number {
    this._history.push({ operandA: a, operator: op, operandB: b, result, timestamp: new Date() })
    return result
  }

  add(a: number, b: number): number { return this.record(a, "+", b, a + b) }
  subtract(a: number, b: number): number { return this.record(a, "-", b, a - b) }
  multiply(a: number, b: number): number { return this.record(a, "*", b, a * b) }

  divide(a: number, b: number): number {
    if (b === 0) throw new DivisionByZeroError()
    return this.record(a, "/", b, a / b)
  }

  calculate(a: number, op: Operation, b: number): number {
    switch (op) {
      case "+": return this.add(a, b)
      case "-": return this.subtract(a, b)
      case "*": return this.multiply(a, b)
      case "/": return this.divide(a, b)
    }
  }

  percentage(value: number): number { return value / 100 }

  squareRoot(value: number): number {
    if (value < 0) throw new NegativeSquareRootError(value)
    return Math.sqrt(value)
  }

  negate(value: number): number { return -value }

  get history(): readonly CalculationRecord[] { return this._history }

  clearHistory(): void { this._history = [] }

  lastResult(): number | null {
    return this._history.length === 0
      ? null
      : this._history[this._history.length - 1].result
  }
}

// calculator.test.ts
import { describe, it, expect, beforeEach } from "vitest"
import { Calculator, DivisionByZeroError } from "./calculator"

describe("Calculator", () => {
  let calc: Calculator

  beforeEach(() => { calc = new Calculator() })

  it("adds two numbers", () => expect(calc.add(3, 4)).toBe(7))
  it("subtracts two numbers", () => expect(calc.subtract(10, 3)).toBe(7))
  it("multiplies two numbers", () => expect(calc.multiply(4, 5)).toBe(20))
  it("divides two numbers", () => expect(calc.divide(10, 2)).toBe(5))

  it("throws DivisionByZeroError for division by zero", () => {
    expect(() => calc.divide(5, 0)).toThrow(DivisionByZeroError)
  })

  it("calculate() dispatches to the correct operation", () => {
    expect(calc.calculate(10, "+", 5)).toBe(15)
    expect(calc.calculate(10, "*", 3)).toBe(30)
  })

  it("records history for each calculation", () => {
    calc.add(1, 2)
    calc.multiply(3, 4)
    expect(calc.history.length).toBe(2)
  })

  it("lastResult returns null before any calculation", () => {
    expect(calc.lastResult()).toBeNull()
  })

  it("lastResult returns the most recent result", () => {
    calc.add(10, 5)
    calc.subtract(20, 7)
    expect(calc.lastResult()).toBe(13)
  })

  it("clearHistory empties the history", () => {
    calc.add(1, 2)
    calc.clearHistory()
    expect(calc.history.length).toBe(0)
    expect(calc.lastResult()).toBeNull()
  })

  it("percentage converts a number to its percentage", () => {
    expect(calc.percentage(50)).toBe(0.5)
    expect(calc.percentage(100)).toBe(1)
  })

  it("squareRoot returns the square root", () => {
    expect(calc.squareRoot(16)).toBe(4)
    expect(calc.squareRoot(0)).toBe(0)
  })

  it("squareRoot throws for negative input", () => {
    expect(() => calc.squareRoot(-1)).toThrow()
  })

  it("negate returns the negated value", () => {
    expect(calc.negate(5)).toBe(-5)
    expect(calc.negate(-3)).toBe(3)
    expect(calc.negate(0)).toBe(0)
  })
})
```

---

*Back to challenges: [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md)*
