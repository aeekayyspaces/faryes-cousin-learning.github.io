# 07 — Arrays and Objects: Organizing Data

## Learning Objectives

By the end of this page, you will be able to:

- Create, read, and modify arrays and objects
- Use key array methods: `push`, `pop`, `splice`, `find`, `some`, `every`, `flat`
- Build and traverse nested data structures
- Understand that objects and arrays are passed by reference
- Use destructuring, spread, and computed property names
- Recognize JSON and convert between JSON strings and JavaScript objects

---

## Arrays: Ordered Lists

An **array** is an ordered, indexed collection of values. Any value can go in an array — numbers, strings, objects, even other arrays.

```javascript
const fruits = ["apple", "banana", "cherry"]

// Access by index (zero-based):
fruits[0]    // "apple"
fruits[1]    // "banana"
fruits[2]    // "cherry"
fruits[3]    // undefined — out of bounds
fruits[-1]   // undefined — negative indexes don't work in JS

// Length:
fruits.length   // 3
```

### Creating Arrays

```javascript
const empty   = []
const numbers = [1, 2, 3, 4, 5]
const mixed   = [42, "hello", true, null, { name: "Alex" }]

// Array constructor (less common):
const zeros = new Array(5).fill(0)      // [0, 0, 0, 0, 0]
const range = Array.from({ length: 5 }, (_, i) => i + 1)  // [1, 2, 3, 4, 5]
```

---

## Modifying Arrays

### Adding and Removing Elements

```javascript
const arr = [1, 2, 3]

arr.push(4)        // add to end → [1, 2, 3, 4]
arr.push(5, 6)     // add multiple → [1, 2, 3, 4, 5, 6]

arr.pop()          // remove from end → [1, 2, 3, 4, 5]  (returns removed: 6)

arr.unshift(0)     // add to beginning → [0, 1, 2, 3, 4, 5]
arr.shift()        // remove from beginning → [1, 2, 3, 4, 5]  (returns removed: 0)
```

### `splice` — Insert or Remove Anywhere

```javascript
const arr = ["a", "b", "c", "d"]

// splice(startIndex, deleteCount, ...itemsToInsert)
arr.splice(1, 0, "x")      // insert at index 1 → ["a", "x", "b", "c", "d"]
arr.splice(2, 1)            // remove 1 at index 2 → ["a", "x", "c", "d"]
arr.splice(1, 2, "y", "z") // replace 2 at index 1 → ["a", "y", "z", "d"]
```

### Finding Elements

```javascript
const nums = [10, 20, 30, 40, 50]

nums.indexOf(30)          // 2  (index of first match, -1 if not found)
nums.includes(30)         // true
nums.find(n => n > 25)    // 30 (first element where condition is true)
nums.findIndex(n => n > 25) // 2 (index of first match)
```

### Testing Conditions

```javascript
const numbers = [2, 4, 6, 8, 10]

numbers.every(n => n % 2 === 0)   // true — ALL are even
numbers.some(n => n > 8)          // true — at LEAST ONE is > 8
numbers.some(n => n > 100)        // false — NONE are > 100
```

### Copying and Combining

```javascript
const a = [1, 2, 3]
const b = [4, 5, 6]

// Combine — does not modify originals:
const combined = a.concat(b)        // [1, 2, 3, 4, 5, 6]
const combined2 = [...a, ...b]      // same result with spread

// Copy (shallow):
const copy = [...a]                 // [1, 2, 3] — new array
const copy2 = a.slice()             // same result

// Slice a portion:
const mid = [1, 2, 3, 4, 5].slice(1, 4)  // [2, 3, 4] — index 1 to 3
```

### Sorting

```javascript
const letters = ["banana", "apple", "cherry"]
letters.sort()          // ["apple", "banana", "cherry"] — alphabetical
                        // ⚠️ sort() MODIFIES the original array

const nums = [10, 3, 25, 1, 8]
nums.sort()             // [1, 10, 25, 3, 8] ← wrong! Lexicographic sort
nums.sort((a, b) => a - b)   // [1, 3, 8, 10, 25] ← correct! Numeric sort
nums.sort((a, b) => b - a)   // [25, 10, 8, 3, 1]  ← descending
```

### Flattening Nested Arrays

```javascript
const nested = [1, [2, 3], [4, [5, 6]]]

nested.flat()     // [1, 2, 3, 4, [5, 6]] — one level deep
nested.flat(2)    // [1, 2, 3, 4, 5, 6]  — two levels deep
nested.flat(Infinity)  // fully flatten, any depth
```

### Joining to a String

```javascript
["one", "two", "three"].join(", ")   // "one, two, three"
["one", "two", "three"].join("")     // "onetwothree"
["one", "two", "three"].join(" | ")  // "one | two | three"
```

---

## Objects: Key-Value Stores

An **object** is an unordered collection of **key-value pairs** (also called properties). Keys are strings (or Symbols); values can be anything.

```javascript
const person = {
  name: "Alex",
  age: 25,
  city: "New York",
  isStudent: false
}
```

### Accessing Properties

```javascript
// Dot notation — use when the key is a valid identifier
person.name    // "Alex"
person.age     // 25

// Bracket notation — use when the key is dynamic or has special characters
person["name"]         // "Alex"
const key = "age"
person[key]            // 25
person["birth year"]   // works with spaces/special chars
```

### Modifying Objects

```javascript
person.age = 26              // modify existing property
person.email = "a@b.com"    // add new property
delete person.isStudent      // remove a property
```

### Checking for Properties

```javascript
"name" in person             // true
"salary" in person           // false
person.hasOwnProperty("name")  // true (own property, not inherited)
```

### Computed Property Names

Use a variable as a property name:

```javascript
const field = "username"
const obj = {
  [field]: "alexrivera",    // { username: "alexrivera" }
  [`${field}_count`]: 5     // { username_count: 5 }
}
```

### Shorthand Property Names

When the variable name matches the key:

```javascript
const name = "Alex"
const age = 25

// Old way:
const user = { name: name, age: age }

// Shorthand (ES6):
const user = { name, age }   // same result
```

### Methods in Objects

A property whose value is a function is called a **method**:

```javascript
const calculator = {
  value: 0,
  add(n) {
    this.value += n
    return this
  },
  subtract(n) {
    this.value -= n
    return this
  },
  result() {
    return this.value
  }
}

calculator.add(10).add(5).subtract(3).result()   // 12
```

---

## Nested Data Structures

Real-world data is often nested — objects containing arrays of objects:

```javascript
const library = {
  name: "City Library",
  books: [
    {
      title: "The Pragmatic Programmer",
      author: { first: "David", last: "Thomas" },
      tags: ["programming", "career"],
      available: true
    },
    {
      title: "Clean Code",
      author: { first: "Robert", last: "Martin" },
      tags: ["programming", "best-practices"],
      available: false
    }
  ]
}

// Access nested data:
library.books[0].title               // "The Pragmatic Programmer"
library.books[0].author.last         // "Thomas"
library.books[0].tags[1]             // "career"

// Find available books:
const available = library.books.filter(book => book.available)
console.log(available.map(b => b.title))  // ["The Pragmatic Programmer"]
```

---

## Reference vs. Value

This is critical to understand for debugging. Primitive values (numbers, strings, booleans) are copied by value. Objects and arrays are copied by **reference**:

```javascript
// Primitives — copy by value:
let a = 5
let b = a
b = 10
console.log(a)   // 5 — unchanged

// Objects — copy by reference:
const obj1 = { count: 5 }
const obj2 = obj1       // obj2 points to the SAME object
obj2.count = 10
console.log(obj1.count)  // 10 ← obj1 changed! Both point to the same object

// Arrays — same:
const arr1 = [1, 2, 3]
const arr2 = arr1
arr2.push(4)
console.log(arr1)   // [1, 2, 3, 4] ← arr1 changed!
```

### Making True Copies

```javascript
// Shallow copy — works for non-nested objects/arrays:
const original = { name: "Alex", scores: [90, 85, 92] }
const shallowCopy = { ...original }

shallowCopy.name = "Jordan"  // only affects shallowCopy
original.name                // "Alex" ← safe

shallowCopy.scores.push(100) // ← mutates the SAME scores array!
original.scores              // [90, 85, 92, 100] ← affected!

// Deep copy — use structuredClone() (modern) or JSON parse/stringify:
const deepCopy = structuredClone(original)
// or:
const deepCopy2 = JSON.parse(JSON.stringify(original))
// (JSON method loses functions, Dates, undefined — use structuredClone for real code)
```

---

## JSON: JavaScript Object Notation

**JSON** is a text format for representing structured data. It looks like a JavaScript object literal but has stricter rules:

- All keys must be double-quoted strings
- No trailing commas
- No comments
- No functions
- No `undefined`

```json
{
  "name": "Alex",
  "age": 25,
  "hobbies": ["coding", "hiking"],
  "address": {
    "city": "New York",
    "zip": "10001"
  }
}
```

### Converting Between JSON and JavaScript

```javascript
// JavaScript object → JSON string:
const user = { name: "Alex", age: 25 }
const jsonString = JSON.stringify(user)
console.log(jsonString)   // '{"name":"Alex","age":25}'
console.log(typeof jsonString)  // "string"

// Pretty-print with indentation:
JSON.stringify(user, null, 2)
// {
//   "name": "Alex",
//   "age": 25
// }

// JSON string → JavaScript object:
const parsed = JSON.parse('{"name":"Alex","age":25}')
console.log(parsed.name)   // "Alex"
console.log(typeof parsed)  // "object"

// Handle parse errors:
try {
  JSON.parse("invalid json}")
} catch (e) {
  console.error("Bad JSON:", e.message)
}
```

JSON is the universal data exchange format of the web. Every API you'll call returns JSON.

---

## Check Your Understanding

1. What is the difference between `push` and `unshift`? Between `pop` and `shift`?
2. What does `nums.sort()` do without a comparator function, and why is it a problem for numbers?
3. Write a one-liner using `filter` and `map` to get the lengths of all strings in `["cat", "elephant", "ox", "bear"]` that are longer than 3 characters.
4. What is the reference vs. value distinction? Write an example that demonstrates the problem and how to fix it with a shallow copy.
5. What is JSON and how do you convert a JavaScript object to a JSON string and back?

---

*Previous: [06 — Error Handling](06-error-handling.md)*
*Next: [08 — Your First JavaScript-Powered Website](08-your-first-js-website.md)*
