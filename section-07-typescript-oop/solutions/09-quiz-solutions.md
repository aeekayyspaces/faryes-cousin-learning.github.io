# Solutions — 09 Section Quiz

Answer key for [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md).

---

## Part 1: Multiple Choice — Answer Key

| # | Answer | Explanation |
|---|--------|-------------|
| 1 | **C — JavaScript** | TypeScript compiles to plain JavaScript. No special runtime is needed — browsers and Node.js run the output just like any other JavaScript. TypeScript is entirely a development-time tool that disappears at runtime. |
| 2 | **A — `function check(items: string[]): boolean`** | `string[]` is the correct TypeScript syntax for an array of strings. Arrow (`->`) is not TypeScript syntax for return types — TypeScript uses `:`. `[string]` is a tuple with one string element, not an array. |
| 3 | **B — `string[]`** | `.map(n => n.toString())` transforms each `number` into a `string`. TypeScript infers the return type of `map` from its callback's return type. The callback `n => n.toString()` returns a `string`, so `map` returns `string[]`. |
| 4 | **A — `any` disables type checking; `unknown` requires narrowing before use** | `any` tells TypeScript "I don't care about the type — allow any operations." `unknown` says "I don't know the type yet, but you must check before using it." `unknown` is the safe version. |
| 5 | **C — 2** | Both `new Counter()` calls increment the static `count`. `Counter.getCount()` returns the current static `count`, which is `2` after two instances. Note: `a.getCount()` would fail (static methods are accessed via the class name). |
| 6 | **C — `protected`** | `protected` allows access within the class and all subclasses, but not from outside (external code). `private` blocks subclasses too. `public` allows all access. `readonly` controls mutability, not access. |
| 7 | **B — Declares properties and assigns constructor arguments automatically** | The access modifier prefix (`public`, `private`, etc.) in the constructor parameter list tells TypeScript to: (1) declare a property with that name and modifier on the class, and (2) assign the argument to it. The constructor body can be empty. |
| 8 | **B — A type is compatible if it has the required properties, regardless of name** | TypeScript's structural typing means compatibility is based on *shape* (what properties and methods something has), not *name* (what it's declared as). This is also called "duck typing" — if it has the right shape, it's compatible. |
| 9 | **B — Accidentally shadowing a method that doesn't exist in the parent class** | Without `override`, TypeScript would silently allow you to write a method that happens to have the same name as a parent method — even if you mistyped the name. `override` ensures the method actually exists in the parent; if you misspell it, TypeScript errors. |
| 10 | **B — Runs before each test, ensuring clean state** | `beforeEach` runs its callback before every single `it` test in the enclosing `describe`. It's essential for test isolation — if test A modifies shared state, test B should start from a clean slate, not from A's leftovers. |
| 11 | **B — The wrapper prevents the error from propagating, so `.toThrow()` can catch and assert on it** | If you write `expect(fn()).toThrow()`, JavaScript evaluates `fn()` first — and if it throws, the error propagates before Vitest can catch it. Wrapping in `() => fn()` passes a *function reference* to `expect`; Vitest calls it inside a try/catch to intercept the throw. |
| 12 | **C — TypeScript errors — `data` only exists on the success variant** | `Result<T>` is a union: `{ success: true; data: T } | { success: false; error: string }`. On the raw union type, `data` only exists on one variant. TypeScript won't let you access `result.data` directly — you must first narrow with `if (result.success)` to guarantee you're in the success branch. |
| 13 | **C — `@param`** | The correct TSDoc/JSDoc tag for documenting a parameter is `@param name - description`. `@arg` is sometimes used but not standard. `@parameter` and `@input` are not valid JSDoc tags. |
| 14 | **B — A union where each variant has a common literal-typed field for narrowing** | A discriminated union has a shared property (the "discriminant") with a unique literal type in each variant. TypeScript narrows the type based on which literal value the discriminant has. Example: `type: "circle" | "rectangle"` — checking `shape.type === "circle"` narrows to the circle variant. |
| 15 | **C — `PascalCase`** | TypeScript interfaces, classes, and type aliases use `PascalCase` (e.g., `UserProfile`, `BankAccount`). Variables and functions use `camelCase`. Constants often use `SCREAMING_SNAKE_CASE`. Files use `kebab-case`. |

---

## Part 2: Short Answer — Suggested Responses

### 1. Four Pillars of OOP — Original Analogy

*(Using a hospital as the analogy — consistent across all four pillars)*

**Encapsulation — The Medical Record:**
A patient's medical records are kept inside the hospital's secure system (the class). Doctors access them only through authorized channels — the reception desk, the doctor's portal (methods). You can't just walk in and edit files yourself. The internal storage details (which database, file format) are hidden. You interact through a controlled interface.

**Abstraction — The Doctor:**
When you see a doctor, you describe your symptoms and they prescribe treatment. You don't need to know how they diagnose — their years of medical knowledge, the diagnostic algorithms, the drug interaction databases they consult. The complex process is hidden; you work with a simple interface: "describe symptoms → get treatment."

**Inheritance — Specialist Doctors:**
A `Cardiologist` *is-a* `Doctor` — they inherit all the general doctor behaviors (examine patient, write prescriptions, take notes) but specialize with additional abilities (read ECGs, perform cardiac procedures). A `Pediatrician` also inherits from `Doctor` but specializes differently. Base behaviors come from the parent, specializations added in the child.

**Polymorphism — "Schedule an appointment":**
You can call `scheduleAppointment()` on a `GeneralPractitioner`, a `Cardiologist`, or an `EmergencyDoctor` — the same method, but each handles it differently (GP: books 2 weeks out; cardiologist: 3 months; emergency: immediate). Code that works with any `Doctor` can call `scheduleAppointment()` without knowing which specialty it's dealing with.

---

### 2. Abstract Class vs Interface

**Interface:**
- Pure contract — declares *what* must exist, not *how*
- No implementation allowed (in TypeScript interfaces)
- A class can implement multiple interfaces simultaneously
- Best when defining a **capability** shared across unrelated classes

**Abstract Class:**
- Partial implementation — provides shared code AND declares abstract requirements
- Can have a constructor, access modifiers, concrete methods
- A class can only extend one abstract class
- Best when there's **shared implementation** to inherit plus contract requirements

**Scenario for Interface:**
```typescript
interface Exportable {
  toJSON(): object
  toPDF(): Blob
}
// A BlogPost, an Invoice, and a UserProfile can all be Exportable
// — they're unrelated types that share a capability
```

**Scenario for Abstract Class:**
```typescript
abstract class Report {
  protected data: unknown[]

  constructor(data: unknown[]) { this.data = data }

  abstract generate(): string    // must be implemented

  save(filename: string): void { // shared behavior
    // write to file
  }
}
// SalesReport and InventoryReport both inherit save() and must implement generate()
```

---

### 3. `Result<T>` vs Exceptions

**Use exceptions for:**
- Programming errors (bugs that should never happen)
- Truly unexpected failures (hardware crash, out-of-memory)
- Situations where the caller can't reasonably recover

**Use `Result<T>` for:**
- Expected failures that are part of normal program flow
- When callers *must* handle the error case (no forgetting)
- When you want the function signature to document failure modes

**Advantages of `Result<T>`:**
1. **Explicit in the type signature** — callers know the function can fail without reading docs
2. **Forces handling** — `result.data` without checking `result.success` is a TypeScript error
3. **No hidden control flow** — exceptions can jump the stack invisibly; `Result<T>` stays in normal flow
4. **Composable** — easy to transform and pipe results

**Example:** `fetchUser(id)` returns `Result<User>`. The return type in the function signature documents that "not found" is a normal outcome, not an exceptional one. TypeScript ensures callers handle both success and failure.

---

### 4. `never` and Exhaustive Checks

`assertNever(x: never)` is a function that accepts `never` as its parameter. TypeScript's `never` type represents a value that can never occur.

In a `switch` on a union type, after handling all variants, TypeScript narrows the type of the remaining value to `never` — because all variants have been handled. If you pass this value to `assertNever`, TypeScript verifies it actually is `never` — compilation succeeds.

If you add a new variant to the union without adding a case to the switch, TypeScript can't narrow the remaining value to `never` — it's now the unhandled new type. TypeScript errors on `assertNever(shape)` saying the new type is not assignable to `never`. **You get a compile-time error immediately, not a silent runtime bug.**

---

### 5. TDD Red-Green-Refactor + `clamp` Tests

**Red-Green-Refactor:**
1. **Red:** Write a failing test for behavior that doesn't exist. Seeing it fail confirms the test is actually testing something.
2. **Green:** Write the *minimum* code to make the test pass — no extras.
3. **Refactor:** Clean up the implementation while keeping tests green.

**`clamp` Tests (just the `it` blocks — failing before implementation):**

```typescript
describe("clamp", () => {
  it("returns the value when within range", () => {
    expect(clamp(5, 0, 10)).toBe(5)
  })

  it("returns min when value is below min", () => {
    expect(clamp(-5, 0, 10)).toBe(0)
  })

  it("returns max when value is above max", () => {
    expect(clamp(15, 0, 10)).toBe(10)
  })

  it("returns min when value equals min (boundary)", () => {
    expect(clamp(0, 0, 10)).toBe(0)
  })

  it("returns max when value equals max (boundary)", () => {
    expect(clamp(10, 0, 10)).toBe(10)
  })

  it("throws when min is greater than max", () => {
    expect(() => clamp(5, 10, 0)).toThrow()
  })
})
```

**Implementation after Red phase:**
```typescript
function clamp(value: number, min: number, max: number): number {
  if (min > max) throw new RangeError(`min (${min}) cannot exceed max (${max})`)
  return Math.min(Math.max(value, min), max)
}
```

---

*Back to quiz: [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md)*
