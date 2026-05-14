# 06 — Error Handling: try, catch, throw

## Learning Objectives

By the end of this page, you will be able to:

- Distinguish between syntax errors, runtime errors, and logic errors
- Use `try/catch` to handle runtime errors gracefully
- Use `finally` to run cleanup code regardless of outcome
- Throw your own errors with `throw`
- Create and use custom `Error` classes
- Read and interpret error messages and stack traces
- Apply common error-handling patterns for production code

---

## The Three Types of Errors

### 1. Syntax Errors

Mistakes in the grammar of the language — caught before the code runs:

```javascript
const name = "Alex"   // ✅ valid
const name = "Alex    // ❌ SyntaxError: Unterminated string constant

function greet( {     // ❌ SyntaxError: Unexpected token '{'
  return "hello"
}
```

Syntax errors are caught by the JavaScript engine before execution. Your code editor (VS Code) will often underline them in red before you even run the code.

### 2. Runtime Errors

The code is grammatically valid, but something goes wrong during execution:

```javascript
const user = null
console.log(user.name)
// ❌ TypeError: Cannot read properties of null (reading 'name')

undeclaredVariable
// ❌ ReferenceError: undeclaredVariable is not defined

const obj = {}
obj.method()
// ❌ TypeError: obj.method is not a function

JSON.parse("not valid json")
// ❌ SyntaxError: Unexpected token 'o' in JSON
```

Runtime errors crash your program unless you handle them.

### 3. Logic Errors

The code runs without crashing, but produces the wrong result:

```javascript
// Bug: wrong formula for area of a circle
function circleArea(radius) {
  return Math.PI * radius   // ❌ should be radius ** 2
}

console.log(circleArea(5))  // 15.707... instead of 78.539...
```

Logic errors don't throw any error — they silently produce wrong answers. These are the hardest to find. Use `console.log` and tests to catch them.

---

## `try...catch`

`try...catch` lets you attempt code that might fail, and handle the failure gracefully rather than crashing:

```javascript
try {
  // Code that might throw an error
  const data = JSON.parse(userInput)
  console.log(data.name)
} catch (error) {
  // Runs if anything inside try throws
  console.error("Invalid input:", error.message)
}
```

Without `try...catch`, an error would crash the entire script. With it, execution continues after the `catch` block.

### How It Flows

```
try block starts
  ↓
  code runs normally...
  ↓
  ERROR THROWN ← ─────────────────┐
  ↓                               │
  (remaining try code skipped)    │
  ↓                               │
catch block runs ←────────────────┘
  ↓
code after try/catch continues
```

---

## The `Error` Object

When a runtime error is thrown (or you throw one manually), it creates an `Error` object with two key properties:

```javascript
try {
  null.property   // throws TypeError
} catch (error) {
  console.log(error.name)     // "TypeError"
  console.log(error.message)  // "Cannot read properties of null..."
  console.log(error.stack)    // full stack trace — where the error came from
}
```

### Built-in Error Types

| Error Type | When It Occurs |
|------------|---------------|
| `Error` | Generic base type |
| `TypeError` | Wrong type used (calling null, accessing .property on undefined) |
| `ReferenceError` | Using a variable that doesn't exist |
| `SyntaxError` | Invalid JSON, invalid JS syntax |
| `RangeError` | Number out of allowed range (e.g., `new Array(-1)`) |
| `URIError` | Malformed URI in `encodeURI()` |

---

## `finally`

Code in `finally` runs **no matter what** — whether the try succeeded or failed:

```javascript
function readData() {
  let connection = null

  try {
    connection = openDatabaseConnection()
    const data = connection.query("SELECT * FROM users")
    return data
  } catch (error) {
    console.error("Database error:", error.message)
    return null
  } finally {
    // Always close the connection, even if an error occurred
    if (connection) connection.close()
    console.log("Connection closed.")
  }
}
```

`finally` is ideal for cleanup — closing files, releasing locks, hiding loading spinners.

---

## `throw`: Creating Your Own Errors

You can throw any value as an error, but throwing an `Error` object (or a subclass) is best practice because it includes a stack trace:

```javascript
function divide(a, b) {
  if (b === 0) {
    throw new Error("Cannot divide by zero")
  }
  return a / b
}

try {
  const result = divide(10, 0)
} catch (error) {
  console.error(error.message)  // "Cannot divide by zero"
}
```

### Throwing Different Error Types

```javascript
function validateAge(age) {
  if (typeof age !== "number") {
    throw new TypeError(`Age must be a number, received: ${typeof age}`)
  }
  if (age < 0 || age > 120) {
    throw new RangeError(`Age must be between 0 and 120, received: ${age}`)
  }
  return true
}

try {
  validateAge("twenty-five")
} catch (error) {
  if (error instanceof TypeError) {
    console.log("Type problem:", error.message)
  } else if (error instanceof RangeError) {
    console.log("Range problem:", error.message)
  } else {
    throw error   // re-throw errors we didn't expect
  }
}
```

---

## Custom Error Classes

For larger applications, create custom error classes so you can distinguish your errors from built-in ones:

```javascript
class ValidationError extends Error {
  constructor(message, field) {
    super(message)           // call Error constructor with message
    this.name = "ValidationError"
    this.field = field       // custom property
  }
}

class NetworkError extends Error {
  constructor(message, statusCode) {
    super(message)
    this.name = "NetworkError"
    this.statusCode = statusCode
  }
}

function validateEmail(email) {
  if (!email.includes("@")) {
    throw new ValidationError("Invalid email format", "email")
  }
}

try {
  validateEmail("notanemail")
} catch (error) {
  if (error instanceof ValidationError) {
    console.log(`Field "${error.field}" failed: ${error.message}`)
    // Field "email" failed: Invalid email format
  }
}
```

---

## Reading Stack Traces

When an error occurs, the **stack trace** shows the call stack — which functions were called, in what order, leading to the error:

```
TypeError: Cannot read properties of null (reading 'name')
    at getUserName (app.js:15:20)       ← error happened here, line 15
    at displayProfile (app.js:8:22)    ← called by displayProfile, line 8
    at init (app.js:3:3)               ← called by init, line 3
    at app.js:1:1                      ← called at the top level, line 1
```

Read a stack trace **bottom to top** for execution order, **top to bottom** to find where the error actually happened. The top of the trace is where the error was thrown — usually where you'll fix it.

---

## Error Handling Patterns

### Pattern 1: Validation Before Execution

Check inputs before doing work — fail fast with clear messages:

```javascript
function createUser(name, email) {
  if (!name || typeof name !== "string") {
    throw new TypeError("name must be a non-empty string")
  }
  if (!email || !email.includes("@")) {
    throw new ValidationError("Invalid email", "email")
  }

  // Only reach here if inputs are valid
  return { id: Date.now(), name, email }
}
```

### Pattern 2: Wrap External Calls

Anything outside your control (user input, network, JSON) should be wrapped in try/catch:

```javascript
function safeParseJSON(text) {
  try {
    return JSON.parse(text)
  } catch (error) {
    console.warn("Invalid JSON:", text)
    return null
  }
}

const data = safeParseJSON('{"valid": true}')   // { valid: true }
const bad  = safeParseJSON("not json")          // null — no crash
```

### Pattern 3: Re-throw Unexpected Errors

Catch what you can handle; re-throw what you can't:

```javascript
async function fetchUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`)
    return await response.json()
  } catch (error) {
    if (error instanceof NetworkError) {
      // We know how to handle this
      return { error: "Network unavailable, please try again" }
    }
    // Unknown error — let it propagate
    throw error
  }
}
```

### Pattern 4: Error-First Results (Tuple Pattern)

Common in functional code — return `[error, result]`:

```javascript
async function safeRequest(url) {
  try {
    const response = await fetch(url)
    const data = await response.json()
    return [null, data]       // [error, result]
  } catch (error) {
    return [error, null]
  }
}

const [err, data] = await safeRequest("/api/users")
if (err) {
  console.error("Request failed:", err.message)
} else {
  console.log("Got data:", data)
}
```

---

## Check Your Understanding

1. What is the difference between a syntax error, a runtime error, and a logic error?
2. What happens to code inside a `try` block after an error is thrown?
3. When does `finally` run? Give an example of when it's useful.
4. Why should you throw `new Error("message")` instead of just throwing a string `"message"`?
5. What does `instanceof` check in a catch block, and why is it useful?

---

*Previous: [05 — Functions](05-functions.md)*
*Next: [07 — Arrays and Objects](07-arrays-and-objects.md)*
