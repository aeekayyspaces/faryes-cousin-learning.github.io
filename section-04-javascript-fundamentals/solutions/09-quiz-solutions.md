# Solutions — 09 Section Quiz

Answer key for [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md).

---

## Part 1: Multiple Choice — Answer Key

| # | Answer | Explanation |
|---|--------|-------------|
| 1 | **B — 5** | Numbers are primitives, copied by **value**. `y = x` copies the value `5` into `y`. Changing `y` to `10` has no effect on `x`. If `x` and `y` were objects or arrays, they would share a reference and the answer would be different. |
| 2 | **C — `0`** | The only falsy values are: `false`, `0`, `""`, `null`, `undefined`, `NaN`. `"0"` (a non-empty string) is truthy. `[]` (an empty array) is truthy. `{}` (an empty object) is truthy. Only the number `0` is falsy among these options. |
| 3 | **C — `true`, `false`** | `==` (loose equality) coerces types: the string `"1"` is converted to the number `1`, so `1 == "1"` is `true`. `===` (strict equality) never coerces: `1` (number) and `"1"` (string) are different types, so it returns `false`. |
| 4 | **A — `[20, 40]`** | `filter(n => n % 2 === 0)` keeps only even numbers: `[2, 4]`. Then `map(n => n * 10)` multiplies each by 10: `[20, 40]`. The chained methods build a pipeline — filter first, then transform the filtered results. |
| 5 | **C — A function that retains access to variables from its enclosing scope after that scope has finished** | A closure is formed when an inner function captures variables from its outer function's scope. The outer function may have returned, but the inner function still holds a reference to those variables. |
| 6 | **C — 8** | `makeAdder(5)` returns an arrow function `x => x + n` with `n` closed over as `5`. When `add5(3)` is called, `x` is `3` and `n` is `5` from the closure. `3 + 5 = 8`. |
| 7 | **C — `const` prevents reassignment but does not make objects immutable** | `const arr = []` means you can't do `arr = []` (reassignment). But `arr.push(1)` works fine — you're modifying the object the variable points to, not changing which object it points to. To make an object immutable, use `Object.freeze()`. |
| 8 | **B — `[0, 2, 4]`** | `Array.from({length: 3}, (_, i) => i * 2)` creates an array of length 3 and calls the mapping function for each index. `i` is 0, 1, 2. `i * 2` gives 0, 2, 4. The `_` is the element value (undefined since there are no values) — conventionally named `_` to indicate it's unused. |
| 9 | **B — The remaining code in the `try` block is skipped; the `catch` block runs** | When an error is thrown inside `try`, JS immediately jumps to the `catch` block. Any code after the throw in the `try` block is skipped. `finally` runs after `catch` (not before). The script continues after the whole `try/catch/finally` block. |
| 10 | **B — Provides syntax for working with Promises that reads like synchronous code** | `async/await` is syntactic sugar over Promises — it uses the same underlying mechanism. It does not create real threads (JavaScript is single-threaded), does not cache responses, and does not replace `try/catch`. It just makes Promise-based code easier to write and read. |
| 11 | **B — Prevents the browser's default behavior — navigating to the form's `action` URL** | When a form is submitted, the browser's default behavior is to send the form data to the URL in the `action` attribute and navigate there. `preventDefault()` stops this so JavaScript can handle the submission instead (validating, making a fetch request, etc.). |
| 12 | **C — `map`** | `map` always returns a new array of the same length — one output element for each input element. `filter` returns a shorter array. `reduce` returns a single value. `find` returns a single element (or undefined). |
| 13 | **B — 99** | Objects are passed by **reference**. `const copy = obj` does not create a new object — both `copy` and `obj` point to the same object in memory. When `copy.a = 99` modifies the object, `obj.a` reflects the change because they refer to the same object. |
| 14 | **B — `??` falls back only for `null` or `undefined`; `||` falls back for any falsy value** | This distinction matters when `0` or `""` are valid values. `score ?? "N/A"` returns `"N/A"` only if `score` is `null` or `undefined` — if `score` is `0`, it returns `0`. `score || "N/A"` returns `"N/A"` for `0`, `""`, `false`, `null`, or `undefined`. |
| 15 | **C — `const res = await fetch(url); const data = await res.json()`** | `fetch()` returns a Promise that resolves to a `Response` object. You must `await` it to get the response. Then `res.json()` also returns a Promise (it reads the stream asynchronously) — you must `await` that too to get the parsed data. Option B is missing the second `await`, returning a Promise instead of the data. |

---

## Part 2: Short Answer — Suggested Responses

### 1. `arr2 = arr; arr2.push(3)` — what logs?

It logs `[1, 2, 3]`.

Arrays in JavaScript are objects, and objects are passed by **reference**. `const arr2 = arr` does not copy the array — it creates a second variable that points to the **same array** in memory. When you call `arr2.push(3)`, you're modifying the underlying array. Since `arr` and `arr2` both point to that same array, `arr` reflects the change.

To create an independent copy: `const arr2 = [...arr]` (spread) or `arr.slice()`.

---

### 2. `map` vs `forEach`

Both iterate over every element of an array. The difference:

**`map`** returns a **new array** of transformed values. Use it when you want to transform data:
```javascript
const doubled = [1, 2, 3].map(n => n * 2)  // [2, 4, 6]
```

**`forEach`** returns `undefined` — it's for **side effects** only (logging, updating the DOM, mutating external state). Use it when you don't need a transformed array:
```javascript
[1, 2, 3].forEach(n => console.log(n))  // logs each; returns undefined
```

Rule of thumb: if you use the return value, use `map`. If you just want to do something for each element, use `forEach`.

---

### 3. Scope example

```javascript
const globalVar = "I'm global"    // global scope — accessible everywhere

function myFunction() {
  const funcVar = "I'm function-scoped"   // function scope — only inside myFunction

  if (true) {
    const blockVar = "I'm block-scoped"   // block scope — only inside this { }
    let alsoBlock = "also block-scoped"
    console.log(blockVar)    // ✅ accessible here
    console.log(funcVar)     // ✅ accessible — inner scope can see outer
    console.log(globalVar)   // ✅ accessible — inner scope can see global
  }

  console.log(funcVar)       // ✅ accessible
  // console.log(blockVar)   // ❌ ReferenceError — outside the block
}

// console.log(funcVar)       // ❌ ReferenceError — outside the function
console.log(globalVar)        // ✅ accessible
```

JavaScript uses **lexical scoping** — scope is determined by where code is written, not where it's called from. Inner scopes can access outer scopes (scope chain), but not the reverse.

---

### 4. `async/await` and its relationship to Promises

A **Promise** is an object representing a value that will be available in the future. It has three states: pending, fulfilled, or rejected.

`async/await` is syntactic sugar over Promises — it provides a cleaner way to write Promise-based code that looks sequential:

```javascript
async function fetchAndParseUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`)

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`)
    }

    const user = await response.json()
    return user
  } catch (error) {
    console.error("Failed to fetch user:", error.message)
    return null
  }
}
```

- `async` makes the function return a Promise implicitly
- `await` pauses execution inside the async function until the awaited Promise resolves
- Other code continues running while the async function is paused
- `try/catch` catches both rejected Promises and thrown errors

---

### 5. `null` vs `undefined`

**`undefined`** means "not yet assigned" — it's the default value of uninitialized variables, missing function parameters, and missing object properties:
```javascript
let x               // undefined — declared but not assigned
function f(a) { }
f()                 // parameter a is undefined — not passed
const obj = {}
obj.missing         // undefined — property doesn't exist
```

**`null`** is an **intentional** empty value — a developer explicitly assigned it to indicate "no value here":
```javascript
let selectedUser = null   // intentionally empty — no user selected yet
let result = null         // will be assigned if the operation succeeds
```

The key distinction: `undefined` is the JavaScript engine's default. `null` is the programmer's explicit choice.

---

*Back to quiz: [09 — Quiz and Checkpoints](../09-quiz-and-checkpoints.md)*
