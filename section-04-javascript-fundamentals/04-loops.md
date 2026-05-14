# 04 — Loops: Repeating Work Efficiently

## Learning Objectives

By the end of this page, you will be able to:

- Use `for`, `while`, and `do...while` loops
- Iterate over arrays with `for...of` and `forEach`
- Iterate over object keys with `for...in`
- Transform arrays with `map`, filter them with `filter`, and aggregate with `reduce`
- Use `break` and `continue` to control loop flow
- Choose the right loop for a given situation

---

## Why Loops?

Without loops, repetitive work requires repetitive code:

```javascript
// Without a loop — absurd:
console.log("Hello 1")
console.log("Hello 2")
console.log("Hello 3")
// ... 97 more lines ...
console.log("Hello 100")

// With a loop — clean:
for (let i = 1; i <= 100; i++) {
  console.log(`Hello ${i}`)
}
```

> **Analogy:** A loop is like a factory assembly line. Instead of hiring 100 workers to each do one task, you have one worker do the same task 100 times in sequence. The instructions are written once and repeated.

---

## The `for` Loop

The classic loop — best when you know exactly how many iterations you need:

```javascript
for (initialization; condition; update) {
  // body
}
```

```javascript
for (let i = 0; i < 5; i++) {
  console.log(`Iteration ${i}`)
}
// Iteration 0
// Iteration 1
// Iteration 2
// Iteration 3
// Iteration 4
```

Breaking it down:
- `let i = 0` — runs once before the loop starts
- `i < 5` — checked before each iteration; loop stops when false
- `i++` — runs after each iteration

### Counting Backwards

```javascript
for (let i = 10; i >= 1; i--) {
  console.log(i)
}
// 10, 9, 8, ... 1
```

### Iterating Over an Array by Index

```javascript
const fruits = ["apple", "banana", "cherry", "date"]

for (let i = 0; i < fruits.length; i++) {
  console.log(`${i}: ${fruits[i]}`)
}
// 0: apple
// 1: banana
// 2: cherry
// 3: date
```

### Nested Loops

```javascript
// Multiplication table (3x3)
for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 3; col++) {
    process.stdout?.write(`${row * col}\t`) || console.log(`${row},${col}: ${row * col}`)
  }
}
```

---

## The `while` Loop

Best when you don't know in advance how many iterations you need — the loop continues as long as a condition is true:

```javascript
let count = 0

while (count < 5) {
  console.log(`Count: ${count}`)
  count++
}
```

### Practical Use: Reading Input Until Valid

```javascript
// Simulating: keep asking until a positive number is entered
let number = -1
while (number <= 0) {
  number = parseFloat(prompt("Enter a positive number:"))
  if (isNaN(number)) number = -1  // reset if non-numeric
}
console.log(`You entered: ${number}`)
```

### Infinite Loop Warning

If the condition never becomes false, the loop runs forever and crashes the browser tab:

```javascript
// ❌ INFINITE LOOP — do not run:
while (true) {
  console.log("This never stops!")
  // missing: a way to exit
}

// ✅ Controlled infinite loop with break:
while (true) {
  const input = prompt("Type 'quit' to exit:")
  if (input === "quit") break
  console.log(`You typed: ${input}`)
}
```

---

## The `do...while` Loop

Like `while`, but the body runs **at least once** — the condition is checked after the first iteration:

```javascript
let attempts = 0

do {
  console.log(`Attempt ${attempts + 1}`)
  attempts++
} while (attempts < 3)
// Attempt 1
// Attempt 2
// Attempt 3
```

Use `do...while` when you need to run the body at least once before checking (e.g., prompting for input before validating it).

---

## `for...of` — Iterating Over Arrays

`for...of` is the modern, clean way to loop over any **iterable** (arrays, strings, Maps, Sets):

```javascript
const colors = ["red", "green", "blue"]

for (const color of colors) {
  console.log(color)
}
// red
// green
// blue
```

No index tracking, no `.length` check — just the values. Use this over a classic `for` loop whenever you don't need the index.

### Getting Both Index and Value with `entries()`

```javascript
for (const [index, color] of colors.entries()) {
  console.log(`${index}: ${color}`)
}
// 0: red
// 1: green
// 2: blue
```

### Iterating Over a String

```javascript
for (const char of "Hello") {
  console.log(char)
}
// H, e, l, l, o
```

---

## `for...in` — Iterating Over Object Keys

```javascript
const person = {
  name: "Alex",
  age: 25,
  city: "New York"
}

for (const key in person) {
  console.log(`${key}: ${person[key]}`)
}
// name: Alex
// age: 25
// city: New York
```

> **Caution:** `for...in` also iterates over inherited properties from the prototype chain. For plain objects (created with `{}`), this is usually fine. For safety with complex objects, use `Object.keys(obj)` or `Object.entries(obj)` instead.

### `Object.keys()`, `Object.values()`, `Object.entries()`

```javascript
const car = { make: "Toyota", model: "Camry", year: 2023 }

Object.keys(car)     // ["make", "model", "year"]
Object.values(car)   // ["Toyota", "Camry", 2023]
Object.entries(car)  // [["make", "Toyota"], ["model", "Camry"], ["year", 2023]]

// Iterate with for...of + Object.entries for safe, clean syntax:
for (const [key, value] of Object.entries(car)) {
  console.log(`${key} → ${value}`)
}
```

---

## `forEach` — Array Method Loop

`forEach` calls a function once for each element of an array:

```javascript
const numbers = [1, 2, 3, 4, 5]

numbers.forEach(function(number) {
  console.log(number * 2)
})
// 2, 4, 6, 8, 10

// Arrow function version (cleaner):
numbers.forEach(n => console.log(n * 2))

// With index:
numbers.forEach((number, index) => {
  console.log(`[${index}] ${number}`)
})
```

`forEach` cannot be stopped early — there's no `break`. Use a regular `for` or `for...of` if you need to exit early.

---

## Array Methods: `map`, `filter`, `reduce`

These three methods transform arrays functionally — they never modify the original array, they return new ones.

### `map` — Transform Every Element

Creates a new array by applying a function to every element:

```javascript
const numbers = [1, 2, 3, 4, 5]

const doubled = numbers.map(n => n * 2)
console.log(doubled)   // [2, 4, 6, 8, 10]
console.log(numbers)   // [1, 2, 3, 4, 5] ← original unchanged

const names = ["alice", "bob", "carol"]
const capitalized = names.map(name => name.charAt(0).toUpperCase() + name.slice(1))
console.log(capitalized)   // ["Alice", "Bob", "Carol"]

// Transform objects:
const users = [
  { id: 1, firstName: "Alice", lastName: "Smith" },
  { id: 2, firstName: "Bob",   lastName: "Jones" }
]
const fullNames = users.map(user => `${user.firstName} ${user.lastName}`)
console.log(fullNames)   // ["Alice Smith", "Bob Jones"]
```

### `filter` — Keep Elements That Match a Condition

Creates a new array with only the elements where the function returns `true`:

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8]

const evens = numbers.filter(n => n % 2 === 0)
console.log(evens)   // [2, 4, 6, 8]

const big = numbers.filter(n => n > 5)
console.log(big)     // [6, 7, 8]

// Real-world example:
const products = [
  { name: "Laptop",   price: 999,  inStock: true  },
  { name: "Phone",    price: 699,  inStock: false },
  { name: "Tablet",   price: 449,  inStock: true  },
  { name: "Monitor",  price: 299,  inStock: true  }
]

const available = products.filter(p => p.inStock)
const affordable = products.filter(p => p.price < 500 && p.inStock)

console.log(available.map(p => p.name))   // ["Laptop", "Tablet", "Monitor"]
console.log(affordable.map(p => p.name))  // ["Tablet", "Monitor"]
```

### `reduce` — Aggregate an Array to a Single Value

`reduce` is the most powerful but most complex. It processes each element, accumulating a result:

```javascript
// Signature: array.reduce(callback, initialValue)
// callback receives: (accumulator, currentValue) => newAccumulator

const numbers = [1, 2, 3, 4, 5]

// Sum all numbers:
const sum = numbers.reduce((total, n) => total + n, 0)
console.log(sum)   // 15

// Trace what happens:
// total=0,  n=1  → return 1
// total=1,  n=2  → return 3
// total=3,  n=3  → return 6
// total=6,  n=4  → return 10
// total=10, n=5  → return 15

// Find maximum:
const max = numbers.reduce((highest, n) => n > highest ? n : highest, -Infinity)
console.log(max)   // 5

// Count occurrences:
const votes = ["alice", "bob", "alice", "carol", "bob", "alice"]
const tally = votes.reduce((counts, vote) => {
  counts[vote] = (counts[vote] ?? 0) + 1
  return counts
}, {})
console.log(tally)   // { alice: 3, bob: 2, carol: 1 }
```

### Chaining Methods

Because `map` and `filter` return new arrays, you can chain them:

```javascript
const result = products
  .filter(p => p.inStock)
  .filter(p => p.price < 800)
  .map(p => p.name)

console.log(result)   // ["Tablet", "Monitor"]
```

---

## `break` and `continue`

### `break` — Exit the Loop Early

```javascript
const numbers = [3, 7, 2, 9, 1, 5]

// Find the first number greater than 6
for (const n of numbers) {
  if (n > 6) {
    console.log(`Found: ${n}`)
    break   // stop immediately — don't check the rest
  }
}
// "Found: 7"
```

### `continue` — Skip This Iteration

```javascript
// Print only even numbers
for (let i = 0; i < 10; i++) {
  if (i % 2 !== 0) continue   // skip odd numbers
  console.log(i)
}
// 0, 2, 4, 6, 8
```

---

## Choosing the Right Loop

| Situation | Best Choice |
|-----------|------------|
| Known number of iterations with index | `for` |
| Unknown number of iterations | `while` |
| Must run at least once | `do...while` |
| Iterate array values (no index needed) | `for...of` |
| Iterate object properties | `for...in` / `Object.entries()` |
| Run side effects on each array item | `forEach` |
| Transform each item into a new value | `map` |
| Keep items matching a condition | `filter` |
| Aggregate to a single value | `reduce` |

---

## Check Your Understanding

1. What is the difference between `for...of` and `for...in`?
2. What happens if you use `break` inside a `forEach` callback?
3. Given `const nums = [4, 1, 7, 2, 9]`, use `filter` to get only numbers greater than 3, then `map` to double them.
4. Explain what `reduce` does. What is the second argument to `reduce` and why is it important?
5. Rewrite this loop using `map`:
   ```javascript
   const lengths = []
   for (const word of ["cat", "elephant", "ox"]) {
     lengths.push(word.length)
   }
   ```

---

*Previous: [03 — Control Structures](03-control-structures.md)*
*Next: [05 — Functions](05-functions.md)*
