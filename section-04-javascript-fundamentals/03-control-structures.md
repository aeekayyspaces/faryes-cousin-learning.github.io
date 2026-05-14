# 03 — Control Structures: if/else, switch, and Ternary

## Learning Objectives

By the end of this page, you will be able to:

- Write `if`, `else if`, and `else` blocks to branch on conditions
- Identify truthy and falsy values in JavaScript
- Use the `switch` statement for multi-branch comparisons
- Write the ternary operator for concise conditional expressions
- Use short-circuit evaluation for conditional logic
- Combine `&&` and `||` effectively

---

## The `if` Statement

The `if` statement runs a block of code only if a condition is `true`:

```javascript
const temperature = 28

if (temperature > 25) {
  console.log("It's hot outside!")
}
// "It's hot outside!"
```

The condition inside `(...)` is evaluated as a boolean. If it's truthy, the block runs.

### `else` — When the Condition Is False

```javascript
const hour = 14

if (hour < 12) {
  console.log("Good morning!")
} else {
  console.log("Good afternoon!")
}
// "Good afternoon!"
```

### `else if` — Multiple Branches

```javascript
const score = 78

if (score >= 90) {
  console.log("Grade: A")
} else if (score >= 80) {
  console.log("Grade: B")
} else if (score >= 70) {
  console.log("Grade: C")
} else if (score >= 60) {
  console.log("Grade: D")
} else {
  console.log("Grade: F")
}
// "Grade: C"
```

Conditions are evaluated top to bottom. The **first** matching branch runs; all others are skipped.

---

## Truthy and Falsy Values

This is one of the most important JavaScript concepts to internalize.

In JavaScript, every value has an inherent boolean quality. When used in an `if` condition, JavaScript **coerces** the value to `true` or `false`.

### Falsy Values (Only 6 of them)

These evaluate to `false` in a boolean context:

| Value | Type |
|-------|------|
| `false` | boolean |
| `0` | number |
| `""` (empty string) | string |
| `null` | null |
| `undefined` | undefined |
| `NaN` | number |

```javascript
if (0)         { } // doesn't run
if ("")        { } // doesn't run
if (null)      { } // doesn't run
if (undefined) { } // doesn't run
if (NaN)       { } // doesn't run
if (false)     { } // doesn't run
```

### Truthy Values (Everything Else)

Everything that is not falsy is **truthy** — including some surprises:

```javascript
if (1)          { console.log("runs") }   // any non-zero number
if ("hello")    { console.log("runs") }   // any non-empty string
if ([])         { console.log("runs") }   // empty array! ← common surprise
if ({})         { console.log("runs") }   // empty object! ← common surprise
if ("0")        { console.log("runs") }   // string "0" is truthy (not 0)
if ("false")    { console.log("runs") }   // string "false" is truthy (not false)
```

> **The two biggest surprises:** An empty array `[]` and an empty object `{}` are **truthy** in JavaScript. Don't try to check `if (myArray)` to see if an array has items — it's always truthy. Use `if (myArray.length > 0)` instead.

### Practical Use

```javascript
// Check if a username was entered
const username = prompt("Enter your name:")  // returns "" if user presses OK without typing

if (username) {
  console.log(`Welcome, ${username}!`)
} else {
  console.log("You didn't enter a name.")
}
// "" is falsy, so the else branch runs if input is empty
```

---

## Comparison with Objects and Arrays

When comparing non-primitive types, `===` checks **reference identity** — not content:

```javascript
const a = [1, 2, 3]
const b = [1, 2, 3]

a === b    // false — two different arrays in memory (different references)
a === a    // true — same reference

const c = a
c === a    // true — c points to the same array
```

To compare array/object content, use `JSON.stringify()` for simple cases, or a library like Lodash's `isEqual` for deep comparison.

---

## The `switch` Statement

`switch` compares one expression against multiple possible values. It's cleaner than long `else if` chains when branching on a single value:

```javascript
const day = "Tuesday"

switch (day) {
  case "Monday":
    console.log("Start of the work week")
    break
  case "Tuesday":
  case "Wednesday":
  case "Thursday":
    console.log("Midweek grind")
    break
  case "Friday":
    console.log("TGIF!")
    break
  case "Saturday":
  case "Sunday":
    console.log("Weekend!")
    break
  default:
    console.log("Unknown day")
}
// "Midweek grind"
```

### Critical: The `break` Statement

Without `break`, execution **falls through** to the next case:

```javascript
const num = 1

switch (num) {
  case 1:
    console.log("One")
    // no break — falls through!
  case 2:
    console.log("Two")
    break
  case 3:
    console.log("Three")
}
// Logs: "One" then "Two" (fallthrough!)
```

Fallthrough is occasionally useful (stacking cases as shown with Tuesday/Wednesday/Thursday above), but accidental fallthrough is a common bug. Always add `break` unless you intentionally want fallthrough.

### `switch` Uses Strict Equality

`switch` uses `===` for comparisons — so `switch ("1")` will NOT match `case 1`.

---

## The Ternary Operator

The ternary operator is a compact `if/else` in a single expression:

```javascript
condition ? valueIfTrue : valueIfFalse
```

```javascript
const age = 20
const status = age >= 18 ? "adult" : "minor"
console.log(status)   // "adult"

// Equivalent if/else:
let status2
if (age >= 18) {
  status2 = "adult"
} else {
  status2 = "minor"
}
```

Use ternary for simple value-based conditionals. Avoid nesting ternaries — they become unreadable:

```javascript
// ❌ Hard to read:
const grade = score >= 90 ? "A" : score >= 80 ? "B" : score >= 70 ? "C" : "F"

// ✅ Better as if/else for more than 2 branches:
let grade
if      (score >= 90) grade = "A"
else if (score >= 80) grade = "B"
else if (score >= 70) grade = "C"
else                  grade = "F"
```

---

## Logical Operators for Conditionals

Because `&&` and `||` short-circuit, they can replace simple `if` statements:

```javascript
// Run a function only if a condition is true:
isLoggedIn && showDashboard()   // if isLoggedIn is truthy, call showDashboard()

// Same as:
if (isLoggedIn) showDashboard()

// Provide a fallback value:
const display = userName || "Guest"

// Same as:
const display = userName ? userName : "Guest"
```

This pattern is very common in React JSX — rendering something conditionally:

```jsx
// In React (preview — we'll cover this in Section 8):
{isLoggedIn && <UserDashboard />}
```

---

## Practical Example: Input Validation

Let's put these tools together in a real scenario:

```javascript
function validateAge(input) {
  const age = Number(input)

  if (input === "" || input === null || input === undefined) {
    return "Age is required."
  }

  if (isNaN(age)) {
    return "Age must be a number."
  }

  if (age < 0 || age > 120) {
    return "Age must be between 0 and 120."
  }

  if (!Number.isInteger(age)) {
    return "Age must be a whole number."
  }

  return null  // null = no error
}

console.log(validateAge(""))       // "Age is required."
console.log(validateAge("abc"))    // "Age must be a number."
console.log(validateAge("-5"))     // "Age must be between 0 and 120."
console.log(validateAge("3.5"))    // "Age must be a whole number."
console.log(validateAge("25"))     // null — valid!
```

---

## Check Your Understanding

1. List all six falsy values in JavaScript.
2. Is an empty array `[]` truthy or falsy? How would you correctly check if an array has at least one item?
3. What is fallthrough in a `switch` statement, and how do you prevent it?
4. Rewrite this using the ternary operator:
   ```javascript
   let label
   if (count === 1) {
     label = "item"
   } else {
     label = "items"
   }
   ```
5. What does `user && user.name` return if `user` is `null`? If `user` is `{name: "Alex"}`?

---

*Previous: [02 — Variables, Data Types, and Operators](02-variables-data-types-operators.md)*
*Next: [04 — Loops](04-loops.md)*
