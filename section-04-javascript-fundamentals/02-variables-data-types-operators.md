# 02 — Variables, Data Types, and Operators

## Learning Objectives

By the end of this page, you will be able to:

- Declare variables with `let`, `const`, and understand why `var` is avoided
- Identify all JavaScript primitive data types
- Use `typeof` to inspect a value's type
- Explain the difference between `==` and `===`
- Work with strings: template literals, common methods
- Work with numbers: arithmetic, the `Math` object
- Use comparison and logical operators
- Understand `null`, `undefined`, and nullish coalescing (`??`)

---

## Variables: Naming Data

A **variable** is a named container for a value. You use variables to store data so you can reference and manipulate it later.

### `let` — Reassignable Variable

```javascript
let age = 25
console.log(age)   // 25

age = 26           // reassigning is fine
console.log(age)   // 26
```

### `const` — Constant (Cannot Be Reassigned)

```javascript
const name = "Alex"
console.log(name)  // Alex

name = "Jordan"    // ❌ TypeError: Assignment to constant variable
```

Use `const` by default. Switch to `let` only when you know you need to reassign. This makes your code easier to reason about — if something is `const`, you know it never changes.

> **Rule of thumb:** Default to `const`. Use `let` when you need to reassign. Never use `var`.

### Why Not `var`?

`var` is the old way to declare variables. It has several confusing behaviors:

```javascript
// var leaks out of blocks (if, for, etc.)
if (true) {
  var leaked = "I escaped the block!"
}
console.log(leaked)  // "I escaped the block!" ← surprising and dangerous

// let stays inside its block
if (true) {
  let contained = "I stay inside"
}
console.log(contained)  // ❌ ReferenceError: contained is not defined
```

`var` is also **hoisted** — the declaration is moved to the top of the function during compilation, causing confusing bugs. Modern JavaScript (`let` and `const`) fixes this. Avoid `var`.

### Variable Naming Rules

```javascript
// ✅ Valid
let firstName = "Alex"       // camelCase — standard for JS
let userAge = 25
let _private = true          // underscore prefix — convention for "private"
let $element = null          // $ prefix — convention in jQuery/frameworks
const MAX_SIZE = 100         // SCREAMING_SNAKE_CASE for constants

// ❌ Invalid
let 1stName = "Alex"         // can't start with a number
let my-name = "Alex"         // hyphens not allowed
let let = "Alex"             // can't use reserved keywords
```

---

## The 7 Primitive Data Types

JavaScript has 7 **primitive** types — simple, immutable values.

### 1. String

A sequence of characters, enclosed in single quotes, double quotes, or backticks:

```javascript
const single   = 'Hello'
const double   = "World"
const template = `Hello, ${double}!`   // template literal — interpolates variables

console.log(template)  // "Hello, World!"
```

**Template literals** (backticks) are the modern way. They support:
- **Interpolation:** Embed any expression with `${...}`
- **Multi-line strings:** Just press Enter — no `\n` needed

```javascript
const user = "Alex"
const score = 98

// Old way:
console.log("Hello, " + user + "! You scored " + score + " points.")

// Modern way (much cleaner):
console.log(`Hello, ${user}! You scored ${score} points.`)
console.log(`You scored ${score > 90 ? "an A" : "below an A"}`)  // expression
```

**Common string methods:**

```javascript
const str = "  Hello, World!  "

str.length              // 18 (length is a property, not a method)
str.trim()              // "Hello, World!" — removes whitespace from both ends
str.toUpperCase()       // "  HELLO, WORLD!  "
str.toLowerCase()       // "  hello, world!  "
str.includes("World")   // true
str.startsWith("  Hel") // true
str.endsWith("!  ")     // true
str.indexOf("World")    // 9 (position of first match, -1 if not found)
str.replace("World", "JavaScript")  // "  Hello, JavaScript!  "
str.slice(2, 7)         // "Hello" (start index, end index — end not included)
str.split(", ")         // ["  Hello", "World!  "] (split into array)
str.padStart(20, "*")   // "**  Hello, World!  " (pad to length 20)
"  abc  ".trim().split("") // ["a", "b", "c"]
```

### 2. Number

JavaScript has only **one** number type — it covers both integers and decimals (IEEE 754 double-precision floating point):

```javascript
const integer = 42
const decimal = 3.14
const negative = -7
const large = 1_000_000    // underscore separator — purely visual, ignored by JS
const scientific = 1.5e6   // 1,500,000

// Special number values:
Infinity        // 1 / 0 === Infinity
-Infinity       // -1 / 0 === -Infinity
NaN             // "Not a Number" — result of invalid math: Number("abc")
```

**Arithmetic operators:**

```javascript
10 + 3    // 13
10 - 3    // 7
10 * 3    // 30
10 / 3    // 3.3333... (always decimal division)
10 % 3    // 1 (remainder — "modulo")
10 ** 3   // 1000 (exponentiation — 10 to the power of 3)
```

**The floating-point gotcha:**

```javascript
0.1 + 0.2    // 0.30000000000000004 ← not 0.3!
```

This is an IEEE 754 floating-point representation issue — not a JavaScript bug. All languages using this standard have it. For financial calculations, use integers (cents, not dollars) or a library like `decimal.js`.

**`Math` object:**

```javascript
Math.round(3.6)       // 4 — rounds to nearest integer
Math.floor(3.9)       // 3 — rounds down
Math.ceil(3.1)        // 4 — rounds up
Math.abs(-5)          // 5 — absolute value
Math.max(1, 5, 3)     // 5
Math.min(1, 5, 3)     // 1
Math.sqrt(16)         // 4 — square root
Math.pow(2, 8)        // 256 — same as 2 ** 8
Math.PI               // 3.141592653589793
Math.random()         // random decimal between 0 (inclusive) and 1 (exclusive)

// Random integer between min and max (inclusive):
function randomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min
}
randomInt(1, 6)   // simulates a die roll
```

**Number methods:**

```javascript
(3.14159).toFixed(2)   // "3.14" — returns a string
(1234567).toLocaleString()  // "1,234,567"
Number("42")           // 42 — convert string to number
Number("abc")          // NaN
parseInt("42px")       // 42 — parses integer from start of string
parseFloat("3.14abc")  // 3.14
```

### 3. Boolean

Exactly two values: `true` or `false`.

```javascript
const isLoggedIn = true
const hasPermission = false

console.log(typeof isLoggedIn)   // "boolean"
```

### 4. `undefined`

A variable that has been declared but not yet assigned a value:

```javascript
let score
console.log(score)         // undefined
console.log(typeof score)  // "undefined"
```

Also the return value of a function that doesn't return anything:

```javascript
function doSomething() {
  console.log("done")
  // no return statement
}
const result = doSomething()  // logs "done"
console.log(result)           // undefined
```

### 5. `null`

An explicit absence of a value. Unlike `undefined` (accidental emptiness), `null` is **intentional** emptiness:

```javascript
let selectedUser = null    // we know there's no user yet — intentional
```

> **Distinction:** `undefined` means "I haven't been assigned yet." `null` means "I've been assigned the value of nothing."

**The infamous `null` bug:**

```javascript
typeof null    // "object" ← this is a 25-year-old bug in JavaScript, not fixed to avoid breaking the web
```

Always check for null explicitly: `value === null`, not `typeof value === "object"`.

### 6. BigInt

For integers larger than `Number.MAX_SAFE_INTEGER` (2⁵³ - 1):

```javascript
const huge = 9007199254740993n    // append 'n' to make a BigInt
typeof huge                       // "bigint"
```

Rarely needed in everyday programming.

### 7. Symbol

A unique, immutable identifier. Used as object property keys to avoid collisions:

```javascript
const id = Symbol("id")
typeof id    // "symbol"
```

Rarely encountered until advanced library/framework work.

---

## The `typeof` Operator

`typeof` returns a string describing the type of a value:

```javascript
typeof "hello"       // "string"
typeof 42            // "number"
typeof true          // "boolean"
typeof undefined     // "undefined"
typeof null          // "object" ← the bug (should be "null")
typeof {}            // "object"
typeof []            // "object" (arrays are objects in JS)
typeof function(){}  // "function"
typeof Symbol()      // "symbol"
typeof 42n           // "bigint"
```

---

## Type Coercion: `==` vs `===`

JavaScript will automatically convert types in some operations — called **type coercion**. This causes subtle bugs.

### `==` (Loose Equality) — Coerces Types Before Comparing

```javascript
1 == "1"       // true  ← "1" is converted to number 1
0 == false     // true  ← false is converted to 0
null == undefined  // true
"" == false    // true
0 == ""        // true
```

These comparisons are hard to reason about. Two values that "look" different are considered equal.

### `===` (Strict Equality) — No Coercion

```javascript
1 === "1"       // false  ← different types, no conversion
0 === false     // false
null === undefined  // false
"" === false    // false
```

**Always use `===` and `!==`.** The loose equality operators (`==`, `!=`) are a source of bugs. Strict equality is predictable.

```javascript
// The only common exception: checking for null OR undefined at once
value == null   // true if value is null OR undefined (intentional loose check)
// Equivalent to: value === null || value === undefined
```

---

## Comparison Operators

```javascript
5 > 3      // true  — greater than
5 < 3      // false — less than
5 >= 5     // true  — greater than or equal
5 <= 4     // false — less than or equal
5 === 5    // true  — strict equal
5 !== 4    // true  — strict not equal
```

---

## Logical Operators

```javascript
true && true     // true  — AND: both must be true
true && false    // false
false || true    // true  — OR: at least one must be true
false || false   // false
!true            // false — NOT: inverts boolean
!false           // true
```

### Short-Circuit Evaluation

`&&` and `||` are **short-circuit** operators — they stop evaluating as soon as the result is known:

```javascript
// && stops at first falsy value (returns it), or returns last value
false && doSomething()   // false — doSomething() is NEVER called
true && "hello"          // "hello" — returns last evaluated value

// || stops at first truthy value (returns it), or returns last value
true  || doSomething()   // true — doSomething() is NEVER called
null  || "default"       // "default" — returns first truthy value
false || 0 || "fallback" // "fallback"
```

This pattern is commonly used for default values:

```javascript
const username = inputValue || "Guest"   // if inputValue is falsy, use "Guest"
```

---

## Nullish Coalescing (`??`)

`||` treats any falsy value (`0`, `""`, `false`) as "missing." Sometimes `0` or `""` are valid values. `??` only falls back for `null` or `undefined`:

```javascript
const score = 0
const displayScore = score || "No score"   // "No score" ← wrong! 0 is valid
const displayScore2 = score ?? "No score"  // 0 ← correct!

const name = ""
const displayName = name || "Anonymous"    // "Anonymous"
const displayName2 = name ?? "Anonymous"   // "" ← empty string is intentional
```

---

## Optional Chaining (`?.`)

Access deeply nested properties without crashing if something along the path is null or undefined:

```javascript
const user = {
  profile: {
    address: {
      city: "New York"
    }
  }
}

// Without optional chaining — crashes if profile or address is missing:
user.profile.address.city        // "New York"
user.settings.theme              // ❌ TypeError: Cannot read property 'theme' of undefined

// With optional chaining — returns undefined instead of crashing:
user.profile?.address?.city      // "New York"
user.settings?.theme             // undefined ← safe

// Works with methods too:
user.profile?.getAvatar?.()      // undefined if getAvatar doesn't exist
```

---

## Compound Assignment Operators

Shortcuts for modifying a variable:

```javascript
let x = 10

x += 5    // x = x + 5  → 15
x -= 3    // x = x - 3  → 12
x *= 2    // x = x * 2  → 24
x /= 4    // x = x / 4  → 6
x **= 2   // x = x ** 2 → 36
x %= 5    // x = x % 5  → 1
x++       // x = x + 1 (post-increment) → 2
x--       // x = x - 1 (post-decrement) → 1
++x       // (pre-increment) → 2
```

---

## Console Practice: Try These

Run each of these in the DevTools Console and verify the results:

```javascript
// String interpolation
const item = "coffee"
const price = 4.50
console.log(`One ${item} costs $${price.toFixed(2)}`)

// Nullish coalescing
const userInput = null
const greeting = `Hello, ${userInput ?? "Stranger"}!`
console.log(greeting)

// Optional chaining
const config = { server: { port: 3000 } }
console.log(config.server?.port)      // 3000
console.log(config.database?.host)    // undefined (no crash)

// Type checking
console.log(typeof null)              // "object" — the famous bug
console.log(null === null)            // true — correct way to check
```

---

## Check Your Understanding

1. What is the difference between `let` and `const`? When would you use each?
2. Why is `var` avoided in modern JavaScript?
3. What does `0.1 + 0.2` produce in JavaScript, and why?
4. What is the difference between `undefined` and `null`?
5. Why should you always use `===` instead of `==`?
6. What does `score ?? "N/A"` return if `score` is `0`? What about `score || "N/A"`? Why are they different?

---

*Previous: [01 — DevTools and the Console](01-devtools-and-the-console.md)*
*Next: [03 — Control Structures](03-control-structures.md)*
