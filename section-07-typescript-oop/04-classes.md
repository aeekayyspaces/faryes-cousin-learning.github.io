# 04 — Classes: Fields, Methods, and Constructors

## Learning Objectives

By the end of this page, you will be able to:

- Write a complete TypeScript class with typed properties and a constructor
- Use access modifiers: `public`, `private`, `protected`, `readonly`
- Apply TypeScript constructor parameter shorthand
- Define static members
- Write getters and setters
- Chain method calls with the builder pattern

---

## Class Anatomy

```typescript
class ClassName {
  // Properties (fields)
  propertyName: type

  // Constructor
  constructor(parameters: types) {
    this.propertyName = parameters
  }

  // Methods
  methodName(parameters: types): returnType {
    // implementation
  }
}

// Creating an instance:
const instance = new ClassName(arguments)
```

---

## A Complete Example: `Task`

```typescript
class Task {
  // Properties
  id: number
  title: string
  description: string
  completed: boolean
  createdAt: Date

  // Constructor — runs when you call `new Task(...)`
  constructor(title: string, description: string = "") {
    this.id = Date.now()
    this.title = title
    this.description = description
    this.completed = false
    this.createdAt = new Date()
  }

  // Methods
  complete(): void {
    this.completed = true
    console.log(`Task "${this.title}" marked complete.`)
  }

  reopen(): void {
    this.completed = false
  }

  toString(): string {
    const status = this.completed ? "✓" : "○"
    return `[${status}] ${this.title}`
  }
}

// Usage:
const task1 = new Task("Build homepage")
const task2 = new Task("Write tests", "Unit tests for auth module")

task1.complete()
console.log(task1.toString())   // [✓] Build homepage
console.log(task2.toString())   // [○] Write tests
```

---

## Access Modifiers

Access modifiers control which code can read or write a class member.

### `public` (Default)

Accessible everywhere — from inside the class, from subclasses, and from external code:

```typescript
class Car {
  public make: string    // same as just: make: string
  public model: string

  constructor(make: string, model: string) {
    this.make = make
    this.model = model
  }
}

const car = new Car("Honda", "Civic")
car.make = "Toyota"    // ✓ accessible from outside
```

### `private` — Class Only

Only accessible from within the same class. Not accessible from subclasses or external code:

```typescript
class BankAccount {
  private balance: number
  private transactionLog: string[]

  constructor(initialBalance: number) {
    this.balance = initialBalance
    this.transactionLog = [`Opened with $${initialBalance}`]
  }

  deposit(amount: number): void {
    this.validateAmount(amount)              // ✓ private method called from inside
    this.balance += amount
    this.transactionLog.push(`+$${amount}`)
  }

  getBalance(): number {
    return this.balance
  }

  private validateAmount(amount: number): void {
    if (amount <= 0) throw new Error("Amount must be positive")
  }
}

const acct = new BankAccount(100)
acct.deposit(50)                  // ✓
acct.balance = 1000000            // ✗ Property 'balance' is private
acct.validateAmount(50)           // ✗ Property 'validateAmount' is private
acct.getBalance()                 // ✓ 150
```

### `protected` — Class and Subclasses

Like `private`, but accessible from subclasses (explored more in the next page):

```typescript
class Vehicle {
  protected fuelLevel: number = 100

  refuel(amount: number): void {
    this.fuelLevel = Math.min(100, this.fuelLevel + amount)
  }
}

class Car extends Vehicle {
  drive(distance: number): void {
    this.fuelLevel -= distance * 0.08    // ✓ subclass can access protected
    console.log(`Fuel remaining: ${this.fuelLevel.toFixed(1)}%`)
  }
}

const car = new Car()
car.drive(100)
car.fuelLevel   // ✗ Property 'fuelLevel' is protected
```

### `readonly` — Set Once

Can be set in the constructor but never reassigned afterward:

```typescript
class User {
  readonly id: string
  readonly createdAt: Date
  name: string

  constructor(name: string) {
    this.id = crypto.randomUUID()    // set once
    this.createdAt = new Date()       // set once
    this.name = name
  }
}

const user = new User("Alex")
user.name = "Alex Rivera"    // ✓ writable
user.id = "new-id"           // ✗ Cannot assign to 'id' because it is a read-only property
```

---

## Constructor Parameter Shorthand

TypeScript has a powerful shorthand that declares and initializes properties directly in the constructor signature. This eliminates boilerplate:

```typescript
// Long form (verbose):
class Point {
  x: number
  y: number

  constructor(x: number, y: number) {
    this.x = x
    this.y = y
  }
}

// TypeScript shorthand — identical result, much less code:
class Point {
  constructor(
    public x: number,
    public y: number
  ) {}   // empty body — TypeScript generates the assignments automatically
}

// With different modifiers:
class Product {
  constructor(
    public readonly id: string,
    public name: string,
    private price: number,
    protected category: string = "general"
  ) {}
}

const p = new Product("abc-123", "Laptop", 999)
p.id       // ✓ "abc-123"
p.name     // ✓ "Laptop"
p.price    // ✗ private
```

The shorthand prefix (`public`, `private`, `protected`, `readonly`) is what triggers the automatic property creation. Without a modifier prefix, the parameter is just a regular constructor parameter.

---

## Static Members

`static` members belong to the **class itself**, not to instances. Accessed via the class name, not `this`:

```typescript
class Counter {
  private static instanceCount: number = 0
  readonly id: number

  constructor() {
    Counter.instanceCount++
    this.id = Counter.instanceCount
  }

  static getCount(): number {
    return Counter.instanceCount
  }

  static reset(): void {
    Counter.instanceCount = 0
  }
}

const a = new Counter()   // id: 1
const b = new Counter()   // id: 2
const c = new Counter()   // id: 3

Counter.getCount()   // 3
a.getCount()         // ✗ Property 'getCount' does not exist on type 'Counter'
```

**Common uses for static members:**
- Factory methods (alternative constructors)
- Counters, caches, configuration shared across all instances
- Utility methods that don't need instance data (`Math.floor`, etc.)

### Static Factory Methods

A **factory method** is a static method that creates and returns an instance — useful when construction is complex or you want descriptive names:

```typescript
class Color {
  private constructor(
    private readonly r: number,
    private readonly g: number,
    private readonly b: number
  ) {}

  // Factory methods — named constructors:
  static fromRGB(r: number, g: number, b: number): Color {
    if ([r, g, b].some(v => v < 0 || v > 255)) {
      throw new RangeError("RGB values must be 0–255")
    }
    return new Color(r, g, b)
  }

  static fromHex(hex: string): Color {
    const clean = hex.replace("#", "")
    const r = parseInt(clean.slice(0, 2), 16)
    const g = parseInt(clean.slice(2, 4), 16)
    const b = parseInt(clean.slice(4, 6), 16)
    return new Color(r, g, b)
  }

  toHex(): string {
    return `#${[this.r, this.g, this.b].map(v => v.toString(16).padStart(2, "0")).join("")}`
  }
}

const red  = Color.fromRGB(255, 0, 0)
const blue = Color.fromHex("#0000ff")
console.log(red.toHex())    // "#ff0000"
console.log(blue.toHex())   // "#0000ff"

new Color(255, 0, 0)   // ✗ Constructor is private — must use factory methods
```

---

## Getters and Setters

Getters and setters look like property accesses but run code:

```typescript
class Temperature {
  private _celsius: number

  constructor(celsius: number) {
    this._celsius = celsius
  }

  // Getter — read like a property
  get fahrenheit(): number {
    return this._celsius * (9 / 5) + 32
  }

  // Setter — validate before assigning
  set celsius(value: number) {
    if (value < -273.15) {
      throw new RangeError("Temperature below absolute zero is not possible")
    }
    this._celsius = value
  }

  get celsius(): number {
    return this._celsius
  }
}

const temp = new Temperature(100)
temp.fahrenheit    // 212 — getter called like a property, no ()
temp.celsius = 0   // setter called with assignment syntax
temp.celsius       // 0

temp.celsius = -300   // ✗ throws RangeError
```

> **Convention:** Private backing fields often use an underscore prefix (`_celsius`) to distinguish them from the public getter/setter name (`celsius`).

---

## Method Chaining (Builder Pattern)

Return `this` from methods to enable fluent chaining:

```typescript
class QueryBuilder {
  private table: string = ""
  private conditions: string[] = []
  private limitValue: number | null = null
  private columns: string[] = ["*"]

  from(table: string): this {
    this.table = table
    return this
  }

  select(...columns: string[]): this {
    this.columns = columns
    return this
  }

  where(condition: string): this {
    this.conditions.push(condition)
    return this
  }

  limit(n: number): this {
    this.limitValue = n
    return this
  }

  build(): string {
    const where = this.conditions.length
      ? ` WHERE ${this.conditions.join(" AND ")}`
      : ""
    const lim = this.limitValue !== null ? ` LIMIT ${this.limitValue}` : ""
    return `SELECT ${this.columns.join(", ")} FROM ${this.table}${where}${lim}`
  }
}

const query = new QueryBuilder()
  .from("users")
  .select("id", "name", "email")
  .where("active = true")
  .where("age >= 18")
  .limit(10)
  .build()

console.log(query)
// SELECT id, name, email FROM users WHERE active = true AND age >= 18 LIMIT 10
```

---

## Complete Example: `TaskManager`

```typescript
class TaskManager {
  private tasks: Task[] = []
  private nextId: number = 1

  add(title: string, description?: string): Task {
    const task = new Task(this.nextId++, title, description ?? "")
    this.tasks.push(task)
    return task
  }

  complete(id: number): boolean {
    const task = this.findById(id)
    if (!task) return false
    task.complete()
    return true
  }

  delete(id: number): boolean {
    const index = this.tasks.findIndex(t => t.id === id)
    if (index === -1) return false
    this.tasks.splice(index, 1)
    return true
  }

  getAll(): Task[] { return [...this.tasks] }

  getPending(): Task[] { return this.tasks.filter(t => !t.completed) }

  getCompleted(): Task[] { return this.tasks.filter(t => t.completed) }

  private findById(id: number): Task | undefined {
    return this.tasks.find(t => t.id === id)
  }

  get stats(): { total: number; completed: number; pending: number } {
    return {
      total: this.tasks.length,
      completed: this.tasks.filter(t => t.completed).length,
      pending: this.tasks.filter(t => !t.completed).length
    }
  }
}
```

---

## Check Your Understanding

1. What is the difference between `private` and `protected`?
2. What does the TypeScript constructor shorthand do? Show the short and long forms.
3. When would you use a `static` method? Give an example.
4. What is a getter? How is it accessed differently from a regular method?
5. Write a `Circle` class with a `private readonly radius`, a constructor, an `area()` method, and a `circumference` getter.

---

*Previous: [03 — OOP Concepts](03-oop-concepts.md)*
*Next: [05 — Inheritance and Interfaces](05-inheritance-and-interfaces.md)*
