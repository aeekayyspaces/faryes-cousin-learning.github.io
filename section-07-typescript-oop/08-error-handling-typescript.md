# 08 — Error Handling in TypeScript

## Learning Objectives

By the end of this page, you will be able to:

- Use TypeScript to make error handling explicit and type-safe
- Build custom typed error classes with discriminated unions
- Implement the `Result` type pattern to avoid exceptions for expected failures
- Use type narrowing to safely handle errors in catch blocks
- Apply exhaustive checks with `never`
- Handle errors in async TypeScript code

---

## The Problem with Plain JavaScript Error Handling

In JavaScript, `throw` can throw anything — a string, a number, an object, anything. The `catch` clause receives it as type `any`:

```javascript
try {
  riskyOperation()
} catch (error) {
  error.message   // hope it has .message... might crash too!
}
```

You have no idea what type the error is. TypeScript formalizes this.

---

## TypeScript's Catch Type: `unknown`

In TypeScript with `useUnknownInCatchVariables: true` (default in strict mode), the `catch` variable is typed as `unknown` — forcing you to check before using it:

```typescript
try {
  JSON.parse("invalid json{")
} catch (error) {
  // error is 'unknown' — must narrow before use
  error.message          // ✗ Object is of type 'unknown'

  if (error instanceof Error) {
    console.error(error.message)  // ✓ narrowed to Error
  }
}
```

This is safer than `any` — it forces you to handle the unknown type explicitly.

### The Safe Error Handler Helper

```typescript
function getErrorMessage(error: unknown): string {
  if (error instanceof Error) return error.message
  if (typeof error === "string") return error
  return "An unknown error occurred"
}

try {
  doSomething()
} catch (error) {
  console.error(getErrorMessage(error))
}
```

---

## Custom Error Classes

TypeScript makes it easy to create typed, hierarchy-aware error classes:

```typescript
// Base application error
class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly statusCode: number = 500
  ) {
    super(message)
    this.name = this.constructor.name
    // Fixes prototype chain in older environments:
    Object.setPrototypeOf(this, new.target.prototype)
  }
}

// Domain-specific errors
class ValidationError extends AppError {
  constructor(
    message: string,
    public readonly field: string
  ) {
    super(message, "VALIDATION_ERROR", 400)
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id: string | number) {
    super(`${resource} with id '${id}' not found`, "NOT_FOUND", 404)
  }
}

class AuthenticationError extends AppError {
  constructor(message = "Authentication required") {
    super(message, "AUTHENTICATION_REQUIRED", 401)
  }
}

class AuthorizationError extends AppError {
  constructor(action: string) {
    super(`Not authorized to perform: ${action}`, "AUTHORIZATION_DENIED", 403)
  }
}

// Usage with instanceof type narrowing:
try {
  await updateUser(id, data)
} catch (error) {
  if (error instanceof ValidationError) {
    showFieldError(error.field, error.message)
  } else if (error instanceof NotFoundError) {
    showToast("User not found")
  } else if (error instanceof AuthorizationError) {
    redirect("/unauthorized")
  } else if (error instanceof AppError) {
    showToast(`Error ${error.code}: ${error.message}`)
  } else {
    throw error   // unexpected — re-throw
  }
}
```

---

## Discriminated Unions for Error Types

For situations where errors represent distinct states (not just different messages), discriminated unions provide a cleaner, type-safe pattern than exception hierarchies:

```typescript
type SuccessResult<T> = {
  success: true
  data: T
}

type ErrorResult = {
  success: false
  error: {
    code: string
    message: string
  }
}

type Result<T> = SuccessResult<T> | ErrorResult
```

### Using the Result Type

```typescript
interface User {
  id: number
  name: string
  email: string
}

async function fetchUser(id: number): Promise<Result<User>> {
  try {
    const response = await fetch(`/api/users/${id}`)

    if (response.status === 404) {
      return {
        success: false,
        error: { code: "NOT_FOUND", message: `User ${id} not found` }
      }
    }

    if (!response.ok) {
      return {
        success: false,
        error: { code: "SERVER_ERROR", message: `HTTP ${response.status}` }
      }
    }

    const user: User = await response.json()
    return { success: true, data: user }

  } catch (err) {
    return {
      success: false,
      error: { code: "NETWORK_ERROR", message: "Network request failed" }
    }
  }
}

// Caller handles both paths explicitly:
const result = await fetchUser(42)

if (result.success) {
  displayUser(result.data)   // TypeScript knows: result.data is User
} else {
  showError(result.error.message)  // TypeScript knows: result.error exists
}
```

### Why Result Types Are Powerful

```typescript
// Forces the caller to handle both outcomes — can't accidentally ignore errors
const result = await fetchUser(42)
displayUser(result.data)   // ✗ Property 'data' does not exist on type 'ErrorResult'
                           // TypeScript won't let you access .data without checking success first
```

---

## Narrowing Error Unions

Use a **discriminant** (a literal type field) to narrow union types:

```typescript
type DatabaseError = {
  type: "database"
  query: string
  sqlError: string
}

type NetworkError = {
  type: "network"
  statusCode: number
  url: string
}

type ParseError = {
  type: "parse"
  raw: string
  position: number
}

type ServiceError = DatabaseError | NetworkError | ParseError

function handleServiceError(error: ServiceError): string {
  switch (error.type) {
    case "database":
      return `DB error in query '${error.query}': ${error.sqlError}`
    case "network":
      return `HTTP ${error.statusCode} at ${error.url}`
    case "parse":
      return `Parse failed at position ${error.position} in: ${error.raw}`
  }
  // TypeScript knows all cases are covered — no default needed
}
```

The `type` property is the **discriminant** — TypeScript uses it to know exactly which variant you're in.

---

## Exhaustive Checks with `never`

Use `never` to ensure all cases in a union are handled — TypeScript will error if you add a new variant and forget to handle it:

```typescript
function assertNever(x: never): never {
  throw new Error(`Unhandled case: ${JSON.stringify(x)}`)
}

type Shape = "circle" | "square" | "triangle"

function describeShape(shape: Shape): string {
  switch (shape) {
    case "circle":   return "A round shape"
    case "square":   return "A four-sided shape"
    case "triangle": return "A three-sided shape"
    default:
      return assertNever(shape)  // TypeScript verifies all cases are handled
  }
}

// If you add "hexagon" to the Shape type without updating the switch:
// ✗ Argument of type '"hexagon"' is not assignable to parameter of type 'never'
//   ← TypeScript catches the missing case immediately
```

---

## Error Handling in Async TypeScript

```typescript
// Async functions with proper error typing
async function loadDashboard(userId: number): Promise<DashboardData> {
  const [userResult, statsResult] = await Promise.allSettled([
    fetchUser(userId),
    fetchUserStats(userId)
  ])

  if (userResult.status === "rejected") {
    throw new NotFoundError("User", userId)
  }

  const user = userResult.value.success
    ? userResult.value.data
    : null

  if (!user) {
    throw new AppError("Failed to load user data", "LOAD_FAILED")
  }

  const stats = statsResult.status === "fulfilled" && statsResult.value.success
    ? statsResult.value.data
    : null   // stats are optional — continue without them

  return { user, stats }
}
```

### Global Error Boundary (Browser)

```typescript
// Catch unhandled promise rejections:
window.addEventListener("unhandledrejection", (event: PromiseRejectionEvent) => {
  const error = event.reason
  console.error("Unhandled promise rejection:", getErrorMessage(error))
  event.preventDefault()   // prevent default browser console error
})

// Catch synchronous errors:
window.addEventListener("error", (event: ErrorEvent) => {
  console.error("Uncaught error:", event.message, event.filename, event.lineno)
})
```

---

## Validation with Zod

For validating external data (API responses, user input) at the TypeScript boundary, **Zod** is the industry standard — it generates both runtime validation and TypeScript types:

```typescript
import { z } from "zod"

// Define schema once — generates TypeScript type automatically
const UserSchema = z.object({
  id: z.number(),
  name: z.string().min(1).max(100),
  email: z.string().email(),
  role: z.enum(["admin", "user", "moderator"]),
  age: z.number().min(0).max(120).optional()
})

// Infer the TypeScript type from the schema:
type User = z.infer<typeof UserSchema>
// Same as: { id: number; name: string; email: string; role: "admin" | "user" | "moderator"; age?: number }

// Safe parsing — returns success/error without throwing:
const result = UserSchema.safeParse(apiResponse)

if (result.success) {
  const user: User = result.data   // fully typed ✓
  processUser(user)
} else {
  console.error("Invalid user data:", result.error.issues)
  // [{ path: ["email"], message: "Invalid email" }]
}
```

Zod eliminates an entire class of bugs at the boundary between your typed TypeScript world and the untyped external world (APIs, localStorage, user input).

---

## Summary: Which Pattern to Use When

| Situation | Pattern |
|-----------|---------|
| Programming errors (bugs) | `throw new Error` — let them crash (loudly) |
| Expected failures (not found, unauthorized) | Custom error classes or `Result<T>` type |
| Validating external data | Zod schemas with `safeParse` |
| Multiple possible error types | Discriminated union + exhaustive switch |
| Async operations | `try/catch` with typed error classes |
| Library error handling | Always re-throw unexpected errors |

**Further Reading:**
- [TypeScript Error Handling — typescript.tv](https://typescript.tv/best-practices/error-handling-in-typescript/)
- [Zod Documentation](https://zod.dev/)
- [Never type — TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#the-never-type)
- [Discriminated Unions — TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#discriminated-unions)

---

## Check Your Understanding

1. Why is `catch (error: unknown)` safer than `catch (error: any)` in TypeScript?
2. What is a discriminated union? How does TypeScript use the discriminant field?
3. When would you choose a `Result<T>` type over throwing an exception?
4. What does `assertNever(x: never)` do, and why is it useful in switch statements?
5. What does Zod's `safeParse` return, and how is it different from `parse`?

---

*Previous: [07 — Testing Basics](07-testing-basics.md)*
*Next: [09 — Quiz and Checkpoints](09-quiz-and-checkpoints.md)*
