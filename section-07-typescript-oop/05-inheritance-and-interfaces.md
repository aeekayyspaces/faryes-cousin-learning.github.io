# 05 — Inheritance and Interfaces

## Learning Objectives

By the end of this page, you will be able to:

- Extend a class and use the `super` keyword
- Override methods and call the parent implementation
- Write abstract classes that define a contract without full implementation
- Implement interfaces on classes
- Explain the difference between interface and type alias for object shapes
- Extend interfaces with `extends`
- Apply polymorphism through interfaces and abstract classes

---

## Extending Classes

The `extends` keyword creates a **subclass** that inherits all properties and methods of the parent (superclass):

```typescript
class Animal {
  constructor(
    public readonly name: string,
    public readonly species: string
  ) {}

  eat(food: string): void {
    console.log(`${this.name} eats ${food}`)
  }

  toString(): string {
    return `${this.name} (${this.species})`
  }
}

class Dog extends Animal {
  constructor(name: string, public readonly breed: string) {
    super(name, "Canis lupus familiaris")  // call parent constructor FIRST
  }

  fetch(item: string): void {
    console.log(`${this.name} fetches the ${item}!`)
  }
}

const dog = new Dog("Rex", "Labrador")
dog.eat("kibble")       // inherited from Animal ✓
dog.fetch("ball")       // Dog-specific ✓
dog.toString()          // "Rex (Canis lupus familiaris)" — inherited ✓
dog.name                // "Rex" — inherited property ✓
dog.breed               // "Labrador" — Dog property ✓

dog instanceof Dog      // true
dog instanceof Animal   // true — Dog IS-A Animal
```

### Rules for `super`

- You **must** call `super(...)` before accessing `this` in a subclass constructor
- `super` in a method calls the parent's version of that method
- If the subclass has no constructor, the parent's constructor is used automatically

---

## Method Overriding

A subclass can **override** (replace) a parent method with a different implementation:

```typescript
class Shape {
  constructor(public color: string = "black") {}

  area(): number {
    return 0
  }

  describe(): string {
    return `A ${this.color} shape with area ${this.area().toFixed(2)}`
  }
}

class Circle extends Shape {
  constructor(public readonly radius: number, color?: string) {
    super(color)
  }

  override area(): number {                       // 'override' keyword confirms intentional override
    return Math.PI * this.radius ** 2
  }
}

class Rectangle extends Shape {
  constructor(
    public readonly width: number,
    public readonly height: number,
    color?: string
  ) {
    super(color)
  }

  override area(): number {
    return this.width * this.height
  }
}

const shapes: Shape[] = [
  new Circle(5, "red"),
  new Rectangle(4, 6, "blue"),
  new Circle(3)
]

for (const s of shapes) {
  console.log(s.describe())
  // "A red shape with area 78.54"
  // "A blue shape with area 24.00"
  // "A black shape with area 28.27"
}
```

The `describe()` method in `Shape` calls `this.area()` — but because `this` is a `Circle` or `Rectangle`, the overridden version runs. This is **polymorphism in action**.

### Calling the Parent Method

Use `super.methodName()` to call the parent's version before or after adding new behavior:

```typescript
class Logger {
  log(message: string): void {
    console.log(`[${new Date().toISOString()}] ${message}`)
  }
}

class PrefixedLogger extends Logger {
  constructor(private prefix: string) { super() }

  override log(message: string): void {
    super.log(`[${this.prefix}] ${message}`)  // add prefix, then call parent
  }
}

const logger = new PrefixedLogger("AUTH")
logger.log("User logged in")
// [2026-05-07T14:00:00.000Z] [AUTH] User logged in
```

---

## Abstract Classes

An **abstract class** is a class that:
1. **Cannot be instantiated directly** (you can't `new AbstractClass()`)
2. Can define **abstract methods** — declared but not implemented
3. Subclasses **must implement** all abstract methods

Abstract classes define a contract that all subclasses must honor:

```typescript
abstract class Notification {
  constructor(
    protected recipient: string,
    protected message: string
  ) {}

  // Abstract — subclasses must implement
  abstract send(): Promise<void>

  // Concrete — all subclasses inherit this as-is
  protected formatMessage(): string {
    return `To: ${this.recipient}\n${this.message}`
  }

  async sendWithRetry(maxAttempts = 3): Promise<void> {
    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
      try {
        await this.send()   // calls the subclass's send()
        return
      } catch (err) {
        if (attempt === maxAttempts) throw err
        await new Promise(r => setTimeout(r, 1000 * attempt))
      }
    }
  }
}

class EmailNotification extends Notification {
  async send(): Promise<void> {
    console.log(`Sending email: ${this.formatMessage()}`)
    // await emailProvider.send(...)
  }
}

class SMSNotification extends Notification {
  async send(): Promise<void> {
    console.log(`Sending SMS to ${this.recipient}: ${this.message}`)
    // await smsProvider.send(...)
  }
}

class PushNotification extends Notification {
  constructor(
    recipient: string,
    message: string,
    private deviceToken: string
  ) {
    super(recipient, message)
  }

  async send(): Promise<void> {
    console.log(`Push to ${this.deviceToken}: ${this.message}`)
  }
}

// Cannot instantiate abstract class:
new Notification("alex", "hello")  // ✗ Cannot create an instance of an abstract class

// Can use as a type (polymorphism):
const notifications: Notification[] = [
  new EmailNotification("alex@example.com", "Welcome!"),
  new SMSNotification("+1555000000", "Your code: 4821"),
  new PushNotification("alex@example.com", "New message!", "token-abc")
]

// Same call, different behavior:
for (const n of notifications) {
  await n.sendWithRetry()
}
```

---

## Interfaces on Classes

An interface can be used as a contract that a class promises to fulfill using `implements`:

```typescript
interface Serializable {
  serialize(): string
  deserialize(data: string): void
}

interface Printable {
  print(): void
}

// A class can implement multiple interfaces:
class Document implements Serializable, Printable {
  constructor(
    private title: string,
    private content: string
  ) {}

  serialize(): string {
    return JSON.stringify({ title: this.title, content: this.content })
  }

  deserialize(data: string): void {
    const parsed = JSON.parse(data)
    this.title = parsed.title
    this.content = parsed.content
  }

  print(): void {
    console.log(`=== ${this.title} ===\n${this.content}`)
  }
}

// Use the interface as a type — works with anything that implements it:
function saveToStorage(item: Serializable): void {
  localStorage.setItem("doc", item.serialize())
}

function displayItem(item: Printable): void {
  item.print()
}

const doc = new Document("Hello", "World content")
saveToStorage(doc)    // ✓ Document implements Serializable
displayItem(doc)      // ✓ Document implements Printable
```

### Interface vs Abstract Class

| | Interface | Abstract Class |
|---|-----------|---------------|
| Can have implementation | No (TypeScript) | Yes |
| Can have constructor | No | Yes |
| A class can `implement` multiple | Yes | No (only `extends` one) |
| Can have access modifiers | No (all public) | Yes |
| Use when | Defining a contract/capability | Defining shared implementation + contract |

---

## Interface Extension

Interfaces can extend other interfaces, combining their requirements:

```typescript
interface Named {
  name: string
}

interface Dated {
  createdAt: Date
  updatedAt: Date
}

interface Identifiable {
  id: string
}

// Combine multiple interfaces:
interface Entity extends Named, Dated, Identifiable {
  type: string
}

// An object satisfying Entity must have all fields:
const post: Entity = {
  id: "post-123",
  name: "My First Post",
  type: "blog_post",
  createdAt: new Date(),
  updatedAt: new Date()
}
```

---

## Structural Typing (Duck Typing)

TypeScript uses **structural typing** — a type is compatible if it has the required shape, regardless of what it's declared as:

```typescript
interface HasLength {
  length: number
}

function printLength(thing: HasLength): void {
  console.log(`Length: ${thing.length}`)
}

// All of these work because they have a .length property:
printLength("hello")          // string has .length ✓
printLength([1, 2, 3])        // array has .length ✓
printLength({ length: 42 })   // plain object ✓

// A class that was never declared to implement HasLength:
class Pipeline {
  length: number = 0
  constructor() { this.length = 0 }
  addStage(): void { this.length++ }
}

const p = new Pipeline()
printLength(p)   // ✓ Pipeline has .length — TypeScript accepts it
```

> **"If it walks like a duck and quacks like a duck, it's a duck."** TypeScript cares about *what a type can do*, not what it's called. This is fundamentally different from languages like Java or C# which use nominal typing.

---

## Putting It Together: A Shape Hierarchy

```typescript
// Interface defines the contract
interface Drawable {
  draw(ctx: CanvasRenderingContext2D): void
}

// Abstract base provides shared implementation
abstract class Shape implements Drawable {
  constructor(
    public color: string,
    public x: number,
    public y: number
  ) {}

  abstract area(): number
  abstract perimeter(): number
  abstract draw(ctx: CanvasRenderingContext2D): void

  moveTo(x: number, y: number): this {
    this.x = x
    this.y = y
    return this
  }

  describe(): string {
    return `${this.constructor.name} at (${this.x},${this.y}) ` +
           `— area: ${this.area().toFixed(2)}`
  }
}

class Circle extends Shape {
  constructor(color: string, x: number, y: number, public radius: number) {
    super(color, x, y)
  }

  override area(): number { return Math.PI * this.radius ** 2 }
  override perimeter(): number { return 2 * Math.PI * this.radius }

  override draw(ctx: CanvasRenderingContext2D): void {
    ctx.beginPath()
    ctx.arc(this.x, this.y, this.radius, 0, 2 * Math.PI)
    ctx.fillStyle = this.color
    ctx.fill()
  }
}

class Rectangle extends Shape {
  constructor(
    color: string, x: number, y: number,
    public width: number, public height: number
  ) { super(color, x, y) }

  override area(): number { return this.width * this.height }
  override perimeter(): number { return 2 * (this.width + this.height) }

  override draw(ctx: CanvasRenderingContext2D): void {
    ctx.fillStyle = this.color
    ctx.fillRect(this.x, this.y, this.width, this.height)
  }
}

// Use polymorphically — works for any Shape
function renderAll(shapes: Shape[], ctx: CanvasRenderingContext2D): void {
  for (const shape of shapes) {
    shape.draw(ctx)
    console.log(shape.describe())
  }
}
```

---

## Check Your Understanding

1. When does a subclass constructor *require* calling `super()`?
2. What is the difference between an abstract class and an interface?
3. Can a class `extends` two classes simultaneously? Can it `implements` two interfaces?
4. What is structural typing? How does it differ from nominal typing?
5. What does the `override` keyword add — would the code still work without it?

---

*Previous: [04 — Classes](04-classes.md)*
*Next: [06 — Commenting, Documentation, and Code Style](06-documentation-and-style.md)*
