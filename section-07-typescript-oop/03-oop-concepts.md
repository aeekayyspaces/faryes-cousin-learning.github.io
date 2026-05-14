# 03 — Object-Oriented Programming: Modeling the Real World

## Learning Objectives

By the end of this page, you will be able to:

- Explain what Object-Oriented Programming (OOP) is and why it was invented
- Describe the four core pillars: Encapsulation, Abstraction, Inheritance, Polymorphism
- Explain how classes act as blueprints and objects as instances
- Map real-world entities to classes and their properties/behaviors
- Compare OOP to procedural and functional programming approaches

---

## What Is OOP?

**Object-Oriented Programming (OOP)** is a way of structuring code around **objects** — bundles of related data (properties) and behavior (methods) that model things from the real world or problem domain.

Instead of writing a program as a sequence of instructions that act on separate pieces of data, OOP groups data and the code that operates on it together into reusable, self-contained units.

> **Analogy:** Think about how a car works. A car has data: make, model, color, fuel level, current speed. It also has behaviors: accelerate, brake, refuel, honk the horn. In OOP, we create a `Car` *class* that bundles all of this together. Each individual car (your Honda, your friend's Toyota) is an *instance* of the `Car` class — it has the same structure, but its own specific data.

---

## Why OOP? The Problem It Solves

Before OOP, programs were written procedurally — as sequences of instructions with data stored in separate variables. As programs grew, this became unmanageable:

```javascript
// Procedural approach — data scattered, no structure
let user1Name = "Alice"
let user1Email = "alice@example.com"
let user1Balance = 500

let user2Name = "Bob"
let user2Email = "bob@example.com"
let user2Balance = 350

function deposit(balance, amount) {
  return balance + amount
}

user1Balance = deposit(user1Balance, 100)
```

Problems:
- Data and functions are disconnected
- No concept of "this user's deposit" — `balance` is just a number
- Hard to add new features (what if each user needs a transaction history?)
- Scales poorly — 1,000 users means 3,000+ variables

```typescript
// OOP approach — data and behavior bundled
class BankAccount {
  balance: number
  owner: string
  transactions: number[]

  constructor(owner: string, initial: number) {
    this.owner = owner
    this.balance = initial
    this.transactions = [initial]
  }

  deposit(amount: number): void {
    this.balance += amount
    this.transactions.push(amount)
    console.log(`${this.owner} deposited $${amount}`)
  }

  getHistory(): number[] {
    return [...this.transactions]
  }
}

const alice = new BankAccount("Alice", 500)
alice.deposit(100)    // Alice deposited $100
alice.getHistory()    // [500, 100]
```

Now `alice` is a self-contained entity. You can create 1,000 accounts with `new BankAccount(...)` — all sharing the same structure, each with their own data.

---

## Classes and Objects

A **class** is a blueprint — it describes what data an object will have and what it can do.

An **object** (or **instance**) is a concrete thing created from the blueprint. Each instance has the same structure but its own independent data.

```
CLASS (Blueprint)                INSTANCES (Objects)
─────────────────                ──────────────────
Car                              myCar = { make: "Honda", color: "blue", speed: 0 }
  Properties:                    friendsCar = { make: "Toyota", color: "red", speed: 45 }
    make: string
    color: string
    speed: number
  Methods:
    accelerate()
    brake()
```

Real-world things that map naturally to classes:

| Real-world thing | Class | Properties | Methods |
|-----------------|-------|-----------|---------|
| Bank account | `BankAccount` | owner, balance | deposit(), withdraw() |
| Email message | `Email` | from, to, subject, body | send(), reply() |
| Blog post | `BlogPost` | title, content, author, publishedAt | publish(), archive() |
| Shopping cart | `Cart` | items, userId | addItem(), removeItem(), checkout() |
| Legal contract | `Contract` | parties, terms, signedDate | sign(), terminate(), renew() |
| Government bill | `Legislation` | title, sponsor, votes | introduce(), amend(), pass() |

OOP lets you model any domain — not just physical things, but abstract concepts like contracts, transactions, workflows, or events.

---

## The Four Pillars of OOP

### 1. Encapsulation — Keep Related Things Together and Hide Internals

**Encapsulation** means bundling data and the methods that operate on it into one unit, and **hiding** the internal details from the outside world.

Think of a vending machine: you interact with it through a simple interface (buttons, coin slot, dispensing slot). You don't need to know — and can't accidentally mess up — the internal mechanics.

```typescript
class BankAccount {
  private balance: number    // hidden — only the class can touch this
  private transactions: Transaction[]

  constructor(initial: number) {
    this.balance = initial
    this.transactions = []
  }

  // Public interface — the only way the outside world can interact:
  deposit(amount: number): void {
    if (amount <= 0) throw new Error("Amount must be positive")
    this.balance += amount
  }

  withdraw(amount: number): void {
    if (amount > this.balance) throw new Error("Insufficient funds")
    this.balance -= amount
  }

  getBalance(): number {
    return this.balance   // read-only access — can't be set from outside
  }
}

const account = new BankAccount(500)
account.deposit(100)
account.balance = 10000    // ✗ Error: Property 'balance' is private
```

**Why it matters:** External code can't put the account into an invalid state. All mutations go through methods that can enforce rules (no negative deposits, no overdrafts).

### 2. Abstraction — Show What Matters, Hide What Doesn't

**Abstraction** means showing only the relevant details and hiding complexity. You interact with a simple interface; the complexity is hidden.

```typescript
class EmailService {
  // Public — what callers care about:
  sendWelcomeEmail(user: User): Promise<void> {
    const email = this.buildWelcomeTemplate(user)
    return this.dispatch(email)
  }

  // Private — callers don't need to know about these:
  private buildWelcomeTemplate(user: User): EmailPayload { ... }
  private dispatch(email: EmailPayload): Promise<void> { ... }
  private handleRetry(email: EmailPayload, attempt: number): Promise<void> { ... }
}

// Caller only sees:
emailService.sendWelcomeEmail(newUser)
// No need to know about templates, retry logic, SMTP connections, etc.
```

**Why it matters:** Callers work at the level of concepts they care about — not implementation details they shouldn't need to know.

### 3. Inheritance — Build on What Already Exists

**Inheritance** lets a new class (child/subclass) inherit the properties and methods of an existing class (parent/superclass), extending or overriding them.

```typescript
class Animal {
  name: string
  constructor(name: string) { this.name = name }
  speak(): string { return `${this.name} makes a sound` }
}

class Dog extends Animal {
  breed: string
  constructor(name: string, breed: string) {
    super(name)           // call parent constructor
    this.breed = breed
  }
  speak(): string {       // override parent method
    return `${this.name} barks!`
  }
  fetch(): void { console.log(`${this.name} fetches the ball`) }
}

class Cat extends Animal {
  speak(): string { return `${this.name} meows!` }
}

const dog = new Dog("Rex", "Labrador")
dog.speak()    // "Rex barks!"
dog.fetch()    // "Rex fetches the ball"
dog.name       // "Rex" — inherited from Animal

const cat = new Cat("Whiskers")
cat.speak()    // "Whiskers meows!"
```

**Why it matters:** Write common behavior once in the parent class. Subclasses inherit it and customize what's different. The `Dog` and `Cat` classes both get `name` and the base structure for free.

**Inheritance caveat:** Overuse causes rigid hierarchies that are hard to change. Modern OOP wisdom: *prefer composition over inheritance* — build complex behaviors by combining small, focused objects rather than deep class hierarchies.

### 4. Polymorphism — One Interface, Many Shapes

**Polymorphism** means the same method name can behave differently depending on the object it's called on.

```typescript
// Different shapes, same method name:
abstract class Shape {
  abstract area(): number
  abstract perimeter(): number

  describe(): string {
    return `Area: ${this.area().toFixed(2)}, Perimeter: ${this.perimeter().toFixed(2)}`
  }
}

class Circle extends Shape {
  constructor(private radius: number) { super() }
  area(): number { return Math.PI * this.radius ** 2 }
  perimeter(): number { return 2 * Math.PI * this.radius }
}

class Rectangle extends Shape {
  constructor(private width: number, private height: number) { super() }
  area(): number { return this.width * this.height }
  perimeter(): number { return 2 * (this.width + this.height) }
}

class Triangle extends Shape {
  constructor(private base: number, private height: number, private sideA: number, private sideB: number) { super() }
  area(): number { return 0.5 * this.base * this.height }
  perimeter(): number { return this.base + this.sideA + this.sideB }
}

// Polymorphism in action — same call, different behavior:
const shapes: Shape[] = [
  new Circle(5),
  new Rectangle(4, 6),
  new Triangle(3, 4, 5, 5)
]

for (const shape of shapes) {
  console.log(shape.describe())   // each calls its own area() and perimeter()
}
```

**Why it matters:** Code that works with `Shape` works with *any* shape — current or future. Add a `Pentagon` class tomorrow: the loop above works without any changes.

---

## OOP vs Other Paradigms

| Paradigm | Organizes code around | Good for |
|----------|----------------------|---------|
| **Procedural** | Sequences of steps | Scripts, simple automation |
| **Object-Oriented** | Objects (data + behavior) | Complex domains, UI systems, games |
| **Functional** | Pure functions and data transforms | Data pipelines, React components |

Most real-world programs blend paradigms. TypeScript/React code is commonly object-oriented for domain models (classes for User, Order, Product) and functional for UI components (pure functions that take props and return JSX).

---

## Check Your Understanding

1. What is the difference between a class and an object (instance)?
2. Explain encapsulation with a real-world analogy that is NOT a vending machine or bank account.
3. Why is polymorphism useful? What would code look like without it?
4. What does "prefer composition over inheritance" mean?
5. Map the concept of a "social media post" to a class: what properties would it have? What methods?

---

*Previous: [02 — TypeScript Basics](02-typescript-basics.md)*
*Next: [04 — Classes, Fields, Methods, and Constructors](04-classes.md)*
