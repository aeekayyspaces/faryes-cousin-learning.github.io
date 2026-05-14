# 05 — Functions: Reusable, Composable Code

## Learning Objectives

By the end of this page, you will be able to:

- Write function declarations, function expressions, and arrow functions
- Define parameters with default values and rest parameters
- Return values and understand what happens without a return statement
- Explain scope: global, function, and block scope
- Describe closures and why they matter
- Use callback functions with array methods
- Write and use higher-order functions

---

## What Is a Function?

A **function** is a named, reusable block of code that performs a specific task. Functions are the primary tool for avoiding repetition and organizing programs.

> **Analogy:** A function is a recipe card in a cookbook. The recipe has a name ("Chocolate Cake"), takes inputs (flour, eggs, chocolate), describes what to do with them, and produces an output (the cake). Any time you want chocolate cake, you follow the same recipe. You don't rewrite it from scratch each time.

---

## Function Declarations

The classic, most common syntax:

```javascript
function greet(name) {
  return `Hello, ${name}!`
}

const message = greet("Alex")
console.log(message)   // "Hello, Alex!"
```

**Anatomy:**
- `function` keyword
- Name (`greet`)
- **Parameters** in parentheses — `name` (placeholders for the values that will be passed in)
- **Body** in curly braces
- `return` sends a value back to the caller

### Calling a Function

```javascript
greet("Alice")    // "Hello, Alice!"
greet("Bob")      // "Hello, Bob!"
greet()           // "Hello, undefined!" — parameter missing, gets undefined
```

### Functions Without a Return

If there's no `return` statement, the function returns `undefined`:

```javascript
function logMessage(msg) {
  console.log(msg)
  // no return
}

const result = logMessage("Hello")
console.log(result)   // undefined
```

---

## Function Expressions

Store a function in a variable:

```javascript
const add = function(a, b) {
  return a + b
}

console.log(add(3, 4))   // 7
```

Function expressions behave like any other value — they can be passed to other functions, stored in arrays, returned from functions.

**Key difference from declarations:** Function declarations are **hoisted** (available before their definition). Function expressions are not:

```javascript
// ✅ Works — declaration is hoisted
greet("Alex")
function greet(name) { return `Hi, ${name}` }

// ❌ Crashes — expression is not hoisted
greet("Alex")     // ReferenceError: Cannot access 'greet' before initialization
const greet = function(name) { return `Hi, ${name}` }
```

---

## Arrow Functions

The modern, concise syntax for functions (ES6, 2015). Arrow functions are now the default choice for most situations:

```javascript
// Full form:
const multiply = (a, b) => {
  return a * b
}

// Implicit return — if the body is a single expression, you can omit {} and return:
const multiply = (a, b) => a * b

// Single parameter — can omit parentheses:
const double = n => n * 2

// No parameters — empty parens required:
const greet = () => "Hello!"

// Returning an object literal — wrap in parentheses (otherwise {} is treated as a block):
const makeUser = (name, age) => ({ name, age })
console.log(makeUser("Alex", 25))   // { name: "Alex", age: 25 }
```

### When to Use Arrow Functions

Arrow functions are shorter and cleaner for callbacks and simple functions. They're the default choice in modern JavaScript:

```javascript
const numbers = [1, 2, 3, 4, 5]

// Old style:
const doubled = numbers.map(function(n) { return n * 2 })

// Arrow function:
const doubled = numbers.map(n => n * 2)
```

**One important difference:** Arrow functions do not have their own `this` binding. For methods on objects or classes, use regular function syntax. This becomes relevant in Section 7 (OOP).

---

## Parameters and Arguments

**Parameters** are the named placeholders in the function definition.
**Arguments** are the actual values passed when calling the function.

```javascript
function greet(name, greeting) {   // ← parameters
  return `${greeting}, ${name}!`
}

greet("Alex", "Hello")   // ← arguments
// "Hello, Alex!"
```

### Default Parameters

Provide a fallback value when an argument is not passed (or is `undefined`):

```javascript
function greet(name, greeting = "Hello") {
  return `${greeting}, ${name}!`
}

greet("Alex")            // "Hello, Alex!" — uses default
greet("Alex", "Hi")      // "Hi, Alex!" — uses provided value
greet("Alex", undefined) // "Hello, Alex!" — undefined triggers default
greet("Alex", null)      // "null, Alex!" — null does NOT trigger default
```

### Rest Parameters (`...`)

Collect any number of arguments into an array:

```javascript
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0)
}

sum(1, 2, 3)         // 6
sum(1, 2, 3, 4, 5)   // 15

// Rest must be last parameter:
function logAll(label, ...items) {
  console.log(label + ":", items.join(", "))
}

logAll("Fruits", "apple", "banana", "cherry")
// "Fruits: apple, banana, cherry"
```

### Spread Operator (`...`)

The `...` spread operator does the reverse — **spreads** an array into individual arguments:

```javascript
const nums = [5, 2, 8, 1, 9]

Math.max(...nums)    // 9 — same as Math.max(5, 2, 8, 1, 9)

// Copy arrays:
const original = [1, 2, 3]
const copy = [...original]        // [1, 2, 3] — a new array

// Merge arrays:
const merged = [...original, 4, 5]  // [1, 2, 3, 4, 5]

// Copy objects:
const user = { name: "Alex", age: 25 }
const updated = { ...user, age: 26, role: "Admin" }
// { name: "Alex", age: 26, role: "Admin" }
```

---

## Scope

**Scope** determines where a variable is accessible.

### Global Scope

Variables declared outside any function or block are globally accessible:

```javascript
const appName = "MyApp"   // global

function init() {
  console.log(appName)    // accessible — global
}

init()
console.log(appName)      // accessible everywhere
```

### Function Scope

Variables declared inside a function are only accessible within that function:

```javascript
function calculate() {
  const result = 42   // function-scoped
  return result
}

console.log(result)   // ❌ ReferenceError — result doesn't exist here
```

### Block Scope (`let` and `const`)

`let` and `const` are scoped to the block (`{ }`) they're declared in:

```javascript
if (true) {
  let blockVar = "inside"
  const CONST = 42
  console.log(blockVar)   // "inside" ✅
}

console.log(blockVar)     // ❌ ReferenceError
console.log(CONST)        // ❌ ReferenceError
```

This is why `let` and `const` are preferred over `var` — they behave intuitively.

### The Scope Chain

When JavaScript looks up a variable, it starts in the current scope and moves outward until it finds it (or reaches global scope):

```javascript
const x = "global"

function outer() {
  const x = "outer"
  
  function inner() {
    const x = "inner"
    console.log(x)   // "inner" — found in own scope first
  }
  
  inner()
  console.log(x)     // "outer"
}

outer()
console.log(x)       // "global"
```

---

## Closures

A **closure** is a function that "remembers" the variables from its enclosing scope, even after that scope has finished executing.

> **Analogy:** Imagine a backpack you're given when you leave home. Even after you leave (the outer function finishes), you still carry everything that was packed inside (the enclosed variables). The closure carries its environment with it.

```javascript
function makeCounter() {
  let count = 0    // this variable is "closed over"

  return function() {
    count++
    return count
  }
}

const counter = makeCounter()
console.log(counter())   // 1
console.log(counter())   // 2
console.log(counter())   // 3

const counter2 = makeCounter()
console.log(counter2())  // 1  ← independent counter
```

`count` is private to `makeCounter`. No code outside can access it directly. Only the returned function can increment it.

### Practical Closure: Making Functions with Memory

```javascript
function makeMultiplier(factor) {
  return n => n * factor   // factor is "closed over"
}

const double = makeMultiplier(2)
const triple = makeMultiplier(3)

console.log(double(5))   // 10
console.log(triple(5))   // 15
console.log(double(7))   // 14
```

---

## Higher-Order Functions and Callbacks

A **higher-order function** is a function that either:
- Takes another function as an argument, or
- Returns a function

A **callback** is a function passed as an argument to another function, to be called later.

```javascript
// setTimeout takes a callback:
setTimeout(() => {
  console.log("This runs after 1 second")
}, 1000)

// Array methods take callbacks:
const nums = [3, 1, 4, 1, 5, 9]
const sorted = nums.sort((a, b) => a - b)  // sort with custom comparison
console.log(sorted)  // [1, 1, 3, 4, 5, 9]

// Your own higher-order function:
function repeat(fn, times) {
  for (let i = 0; i < times; i++) {
    fn(i)
  }
}

repeat(i => console.log(`Hello #${i + 1}`), 3)
// Hello #1
// Hello #2
// Hello #3
```

---

## Destructuring

Unpack values from arrays or objects into variables — a pattern you'll use constantly:

```javascript
// Array destructuring:
const [first, second, ...rest] = [10, 20, 30, 40, 50]
console.log(first)   // 10
console.log(second)  // 20
console.log(rest)    // [30, 40, 50]

// Object destructuring:
const user = { name: "Alex", age: 25, city: "New York" }
const { name, age, city } = user
console.log(name)    // "Alex"

// Rename while destructuring:
const { name: userName, age: userAge } = user

// Default values:
const { name: n, role = "Member" } = user
console.log(role)    // "Member" — not in object, uses default

// In function parameters (very common pattern):
function greetUser({ name, age = 0 }) {
  return `${name} is ${age} years old`
}

greetUser({ name: "Alice", age: 30 })   // "Alice is 30 years old"
greetUser({ name: "Bob" })              // "Bob is 0 years old"
```

---

## Check Your Understanding

1. What is the difference between a function declaration and a function expression? Which is hoisted?
2. What does a function return if there is no `return` statement?
3. What is a closure? Give a real-world use case.
4. What is the difference between rest parameters (`...args`) and the spread operator (`...arr`)?
5. Write a higher-order function `applyTwice(fn, value)` that applies `fn` to `value` twice. Test it with `n => n + 3` and value `10`.

---

*Previous: [04 — Loops](04-loops.md)*
*Next: [06 — Error Handling](06-error-handling.md)*
