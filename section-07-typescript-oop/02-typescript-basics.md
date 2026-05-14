# 02 — TypeScript Basics: Types, Inference, and Interfaces

## Learning Objectives

By the end of this page, you will be able to:

- Annotate variables, parameters, and return types
- Use type inference to let TypeScript infer types automatically
- Work with primitive types, arrays, tuples, and object types
- Create reusable type aliases and interfaces
- Use union types, literal types, and optional properties
- Understand `any`, `unknown`, `never`, and `void`
- Write basic generic functions

---

## Type Annotations

A **type annotation** is a colon followed by a type name, added to a variable or function parameter:

```typescript
let name: string = "Alex"
let age: number = 25
let active: boolean = true
let nothing: null = null
let missing: undefined = undefined
```

### Function Annotations

```typescript
function add(a: number, b: number): number {
  return a + b
}

const greet = (name: string): string => {
  return `Hello, ${name}!`
}

// Functions that don't return a value:
function logMessage(msg: string): void {
  console.log(msg)
}
```

---

## Type Inference — Let TypeScript Figure It Out

TypeScript is smart. When you assign a value at declaration, TypeScript infers the type automatically — you don't need to write it:

```typescript
let name = "Alex"        // TypeScript infers: string
let age = 25             // TypeScript infers: number
let scores = [90, 85, 92] // TypeScript infers: number[]

// TypeScript still protects you:
name = 42    // ✗ Type 'number' is not assignable to type 'string'
```

**Best practice:** Let TypeScript infer where it's obvious. Annotate:
- Function parameters (TypeScript cannot infer these)
- Return types of exported functions (makes the API explicit)
- Variables initialized to `null` or where the type isn't obvious

```typescript
// ✓ Annotation needed — TypeScript can't infer what this will hold
let currentUser: User | null = null

// ✗ Redundant — TypeScript already knows it's a string
let greeting: string = "Hello"   // annotation is noise
let greeting = "Hello"            // cleaner
```

---

## Primitive Types

```typescript
const name: string = "Alex Rivera"
const age: number = 25
const isAdmin: boolean = false
const score: number = 98.5
const nothing: null = null
const missing: undefined = undefined
const uniqueKey: symbol = Symbol("id")
const bigNumber: bigint = 9007199254740993n
```

---

## Arrays

```typescript
const numbers: number[] = [1, 2, 3, 4, 5]
const names: string[] = ["Alice", "Bob", "Carol"]
const flags: boolean[] = [true, false, true]

// Equivalent generic syntax:
const numbers2: Array<number> = [1, 2, 3]

// Array of objects:
const users: { name: string; age: number }[] = [
  { name: "Alice", age: 28 },
  { name: "Bob", age: 32 }
]
```

---

## Tuples

A **tuple** is an array with a fixed number of elements of specific types at specific positions:

```typescript
const point: [number, number] = [10, 20]
const nameAge: [string, number] = ["Alex", 25]
const rgb: [number, number, number] = [255, 128, 0]

// Named tuples (more readable):
const point2: [x: number, y: number] = [10, 20]

// Common use case — function returning multiple values:
function getMinMax(nums: number[]): [number, number] {
  return [Math.min(...nums), Math.max(...nums)]
}

const [min, max] = getMinMax([3, 1, 4, 1, 5, 9])
console.log(min, max)  // 1  9
```

---

## Object Types

```typescript
// Inline object type annotation:
const user: { name: string; age: number; email: string } = {
  name: "Alex",
  age: 25,
  email: "alex@example.com"
}

// Optional properties with ?:
const config: {
  host: string;
  port?: number;        // optional — may be undefined
  debug?: boolean;
} = { host: "localhost" }   // port and debug not required
```

---

## Type Aliases

A **type alias** gives a name to any type — especially useful for object shapes you use repeatedly:

```typescript
type User = {
  id: number
  name: string
  email: string
  role: "admin" | "user" | "moderator"
  createdAt: Date
}

type Point = {
  x: number
  y: number
}

type ID = string | number    // alias for a union type

// Use everywhere:
function getUser(id: ID): User | null { ... }
function distance(p1: Point, p2: Point): number { ... }
```

---

## Interfaces

An **interface** defines the shape of an object. Interfaces and type aliases are very similar; interfaces have some extra powers (declaration merging, clearer error messages for objects):

```typescript
interface Product {
  id: number
  name: string
  price: number
  inStock: boolean
  category?: string        // optional property
  readonly sku: string     // readonly — can't be changed after creation
}

const laptop: Product = {
  id: 1,
  name: "ThinkPad",
  price: 999,
  inStock: true,
  sku: "TP-001"
}

laptop.sku = "NEW-SKU"   // ✗ Cannot assign to 'sku' because it is a read-only property
laptop.price = 899       // ✓ price is writable
```

### Type Alias vs Interface — When to Use Each

| | Type Alias | Interface |
|---|-----------|-----------|
| Object shapes | ✓ | ✓ (preferred for public APIs) |
| Primitives, unions, tuples | ✓ | ✗ |
| Extending | `&` intersection | `extends` keyword |
| Declaration merging | ✗ | ✓ (two declarations auto-merge) |
| Error messages | Inline | Named (clearer) |

**Rule of thumb:** Use `interface` for class shapes and public-facing object APIs. Use `type` for everything else (unions, tuples, mapped types, aliases for primitives).

---

## Union Types

A value can be one of several types:

```typescript
let id: string | number = "abc-123"
id = 42    // also fine

function formatId(id: string | number): string {
  if (typeof id === "number") {
    return `ID-${id.toString().padStart(6, "0")}`
  }
  return id.toUpperCase()
}

console.log(formatId(42))        // "ID-000042"
console.log(formatId("abc"))     // "ABC"
```

TypeScript **narrows** the type inside each branch — `id.padStart()` is available only in the `string` branch.

---

## Literal Types

Restrict a value to a specific set of allowed values:

```typescript
type Direction = "north" | "south" | "east" | "west"
type Status = "pending" | "active" | "inactive" | "deleted"
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6

function move(direction: Direction): void {
  console.log(`Moving ${direction}`)
}

move("north")   // ✓
move("up")      // ✗ Argument of type '"up"' is not assignable to type 'Direction'
```

---

## Special Types

### `any` — Opt Out of Type Checking (Avoid)

```typescript
let data: any = "hello"
data = 42           // fine
data.something()    // fine — no error even if it would crash at runtime
```

`any` disables TypeScript's protection entirely. Avoid it. If you find yourself writing `any`, think harder about the actual type.

### `unknown` — Safer Any

```typescript
let input: unknown = getUserInput()  // could be anything

input.toUpperCase()  // ✗ Error — you can't use unknown without narrowing

// Must narrow before use:
if (typeof input === "string") {
  input.toUpperCase()  // ✓ TypeScript now knows it's a string
}
```

Use `unknown` instead of `any` when the type truly isn't known (like parsing JSON or receiving data from an API).

### `void` — No Return Value

```typescript
function logError(msg: string): void {
  console.error(msg)
  // no return statement
}
```

### `never` — Unreachable Code

`never` represents a value that never occurs — used for functions that always throw or have exhaustive switch statements:

```typescript
function fail(message: string): never {
  throw new Error(message)
  // code after this is unreachable — TypeScript knows this
}

// Exhaustive check:
function assertUnreachable(x: never): never {
  throw new Error(`Unhandled case: ${x}`)
}

type Shape = "circle" | "square" | "triangle"

function area(shape: Shape): number {
  switch (shape) {
    case "circle":   return Math.PI * 5 ** 2
    case "square":   return 5 * 5
    case "triangle": return 0.5 * 5 * 5
    default:
      return assertUnreachable(shape)  // if you add a new Shape, TypeScript will error here
  }
}
```

---

## Generics — Type Parameters

**Generics** let you write functions and types that work with any type while still being type-safe:

```typescript
// Without generics — loses type information:
function first(arr: any[]): any {
  return arr[0]
}
const x = first([1, 2, 3])   // x is type 'any' — not useful

// With generics — preserves type:
function first<T>(arr: T[]): T {
  return arr[0]
}
const x = first([1, 2, 3])      // x is type 'number' ✓
const y = first(["a", "b"])     // y is type 'string' ✓
```

`T` is a **type parameter** — a placeholder that gets filled in with the actual type at the call site.

### Common Generic Patterns

```typescript
// Identity function:
function identity<T>(value: T): T {
  return value
}

// Generic wrapper:
type ApiResponse<T> = {
  data: T
  status: number
  message: string
}

// Usage:
const userResponse: ApiResponse<User> = {
  data: { id: 1, name: "Alex", email: "a@b.com" },
  status: 200,
  message: "Success"
}

// Generic with constraint — only types that have a .length property:
function logLength<T extends { length: number }>(item: T): void {
  console.log(`Length: ${item.length}`)
}
logLength("hello")     // ✓ strings have .length
logLength([1, 2, 3])   // ✓ arrays have .length
logLength(42)          // ✗ numbers don't have .length
```

---

## Type Narrowing

TypeScript tracks what type a variable *must be* at each point in the code:

```typescript
function processInput(input: string | number | null): string {
  // input is: string | number | null

  if (input === null) {
    return "No input"
    // TypeScript knows: input is null here
  }

  // TypeScript knows: input is string | number here (null eliminated)

  if (typeof input === "string") {
    return input.toUpperCase()
    // TypeScript knows: input is string here
  }

  // TypeScript knows: input is number here (string and null eliminated)
  return input.toFixed(2)
}
```

**Narrowing operators:**
- `typeof` — narrows primitives
- `instanceof` — narrows class instances
- `in` — checks if property exists in object
- Equality checks (`=== null`, `=== "value"`)
- Truthiness checks (`if (value)`)

---

## Putting It Together

```typescript
interface BlogPost {
  id: number
  title: string
  content: string
  author: string
  publishedAt: Date | null    // null = draft
  tags: string[]
  status: "draft" | "published" | "archived"
}

type CreatePostInput = Omit<BlogPost, "id" | "publishedAt">

function createPost(input: CreatePostInput): BlogPost {
  return {
    ...input,
    id: Date.now(),
    publishedAt: input.status === "published" ? new Date() : null
  }
}

function getPostsByStatus(
  posts: BlogPost[],
  status: BlogPost["status"]   // ← indexing a type
): BlogPost[] {
  return posts.filter(p => p.status === status)
}
```

**Further Reading:**
- [TypeScript Handbook — Basic Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [TypeScript Handbook — Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [TypeScript Handbook — Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [TypeScript Playground](https://www.typescriptlang.org/play)

---

## Check Your Understanding

1. When should you write a type annotation vs. let TypeScript infer the type?
2. What is the difference between `any` and `unknown`? Why should `any` be avoided?
3. Write a type alias `Color` that only allows `"red"`, `"green"`, or `"blue"`.
4. What is a generic type parameter? Give an example of when you'd need one.
5. What is type narrowing? Show an example using `typeof`.

---

*Previous: [01 — Why TypeScript?](01-why-typescript.md)*
*Next: [03 — Object-Oriented Programming](03-oop-concepts.md)*
