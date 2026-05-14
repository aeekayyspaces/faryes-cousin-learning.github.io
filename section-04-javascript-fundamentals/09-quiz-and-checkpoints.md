# 09 — Section Quiz and Checkpoints

## Section 4 Learning Objectives Review

Before taking this quiz, confirm you can:

- [ ] Use the DevTools Console as an interactive JavaScript environment
- [ ] Declare variables with `let` and `const` and explain the difference
- [ ] Identify all primitive data types and use `typeof`
- [ ] Explain and use `===` vs `==`
- [ ] Write `if/else`, `switch`, and ternary operators correctly
- [ ] Understand truthy and falsy values
- [ ] Use `for`, `while`, `for...of`, `forEach`, `map`, `filter`, and `reduce`
- [ ] Write function declarations, expressions, and arrow functions
- [ ] Use default parameters, rest parameters, and the spread operator
- [ ] Explain closures and scope
- [ ] Use `try/catch/finally` and `throw` for error handling
- [ ] Work with arrays and objects including nested data structures
- [ ] Make HTTP requests with `fetch` and `async/await`
- [ ] Handle forms, validate input, and update the DOM

---

## Part 1: Multiple Choice

Answer key: [solutions/09-quiz-solutions.md](solutions/09-quiz-solutions.md)

---

**1.** What does the following code log?

```javascript
let x = 5
let y = x
y = 10
console.log(x)
```

- A) 10
- B) 5
- C) undefined
- D) ReferenceError

---

**2.** Which of these values is **falsy** in JavaScript?

- A) `"0"` (the string containing zero)
- B) `[]` (an empty array)
- C) `0` (the number zero)
- D) `{}` (an empty object)

---

**3.** What is the output of:

```javascript
console.log(1 == "1")
console.log(1 === "1")
```

- A) `false`, `false`
- B) `true`, `true`
- C) `true`, `false`
- D) `false`, `true`

---

**4.** What does this return?

```javascript
const result = [1, 2, 3, 4, 5].filter(n => n % 2 === 0).map(n => n * 10)
```

- A) `[20, 40]`
- B) `[10, 20, 30, 40, 50]`
- C) `[2, 4]`
- D) `[1, 3, 5]`

---

**5.** What is a closure?

- A) A function that has no return value
- B) A function that can only be called once
- C) A function that retains access to variables from its enclosing scope after that scope has finished
- D) A function that is defined inside a loop

---

**6.** What does this code output?

```javascript
function makeAdder(n) {
  return x => x + n
}
const add5 = makeAdder(5)
console.log(add5(3))
```

- A) 5
- B) 3
- C) 8
- D) ReferenceError: n is not defined

---

**7.** Which statement about `const` is correct?

- A) A `const` array cannot have elements added to it
- B) A `const` variable can never be changed in any way
- C) A `const` declaration prevents the variable from being reassigned, but does not make objects immutable
- D) `const` variables are hoisted and initialized to `undefined`

---

**8.** What does `Array.from({length: 3}, (_, i) => i * 2)` produce?

- A) `[0, 1, 2]`
- B) `[0, 2, 4]`
- C) `[2, 4, 6]`
- D) `[1, 2, 3]`

---

**9.** What happens to execution in a `try` block when an error is thrown?

- A) The entire script stops running
- B) The remaining code in the `try` block is skipped; the `catch` block runs
- C) The error is silently ignored and execution continues
- D) The `finally` block runs before the `catch` block

---

**10.** What does `async/await` do?

- A) Runs code on a separate CPU thread for performance
- B) Provides syntax for working with Promises that reads like synchronous code
- C) Caches fetch responses automatically
- D) Replaces `try/catch` with a promise-based error system

---

**11.** What does `event.preventDefault()` do in a form's submit handler?

- A) Removes all form validation
- B) Prevents the browser's default behavior — navigating to the form's `action` URL
- C) Clears all form fields
- D) Stops the event from reaching any other listeners

---

**12.** Which array method returns a new array of the same length as the original?

- A) `filter`
- B) `reduce`
- C) `map`
- D) `find`

---

**13.** What is the output?

```javascript
const obj = { a: 1, b: 2 }
const copy = obj
copy.a = 99
console.log(obj.a)
```

- A) 1 — `obj` is unchanged
- B) 99 — both `copy` and `obj` reference the same object
- C) undefined
- D) ReferenceError

---

**14.** What does the `??` operator do differently from `||`?

- A) `??` only works with strings; `||` works with all types
- B) `??` falls back only for `null` or `undefined`; `||` falls back for any falsy value including `0` and `""`
- C) `??` is stricter — it uses `===` while `||` uses `==`
- D) There is no difference

---

**15.** Given `fetch` returns a Promise, which code correctly reads the JSON response?

- A) `const data = fetch(url).json()`
- B) `const res = await fetch(url); const data = res.json()`
- C) `const res = await fetch(url); const data = await res.json()`
- D) `const data = await fetch(url)`

---

## Part 2: Short Answer

Suggested answers: [solutions/09-quiz-solutions.md](solutions/09-quiz-solutions.md)

**1.** Explain what happens when you run: `const arr = [1, 2]; const arr2 = arr; arr2.push(3); console.log(arr)`. What does it log and why?

**2.** What is the difference between `map` and `forEach`? When would you use each?

**3.** Explain the concept of scope with a code example showing global scope, function scope, and block scope.

**4.** What is `async/await` and how does it relate to Promises? Write a function using `async/await` that fetches a URL and returns the parsed JSON, handling errors with `try/catch`.

**5.** What is the difference between `null` and `undefined`? Give one example of when each naturally occurs.

---

## Part 3: Code Challenges

Solutions: [solutions/code-challenges-solutions.md](solutions/code-challenges-solutions.md)

---

### Challenge 1: FizzBuzz

Write a function `fizzBuzz(n)` that logs numbers from 1 to `n`, but:
- For multiples of 3, log "Fizz"
- For multiples of 5, log "Buzz"
- For multiples of both, log "FizzBuzz"
- Otherwise log the number

```javascript
fizzBuzz(15)
// 1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, 11, Fizz, 13, 14, FizzBuzz
```

---

### Challenge 2: Data Transformation Pipeline

Given this array of user data:

```javascript
const users = [
  { id: 1, name: "Alice",   age: 28, role: "admin",     active: true  },
  { id: 2, name: "Bob",     age: 17, role: "user",      active: true  },
  { id: 3, name: "Carol",   age: 34, role: "moderator", active: false },
  { id: 4, name: "David",   age: 22, role: "user",      active: true  },
  { id: 5, name: "Eve",     age: 31, role: "admin",     active: true  },
]
```

Using `filter`, `map`, and `reduce`:
1. Filter to only active users who are 18 or older
2. Map to create objects with `{ name, role }` only
3. Use `reduce` to count how many users have each role
4. Find the oldest active user

---

### Challenge 3: Function Factory

Write a function `makeValidator(rules)` that takes an object of validation rules and returns a validation function. When the returned function is called with a data object, it runs all rules and returns an errors object.

```javascript
const validateUser = makeValidator({
  name:  value => value && value.length >= 2 ? null : "Name must be at least 2 characters",
  email: value => value && value.includes("@") ? null : "Must be a valid email",
  age:   value => value >= 18 ? null : "Must be 18 or older"
})

validateUser({ name: "Al", email: "a@b.com", age: 16 })
// { age: "Must be 18 or older" }  ← only age failed

validateUser({ name: "X", email: "notvalid", age: 16 })
// { name: "Name must be at least 2 characters", email: "...", age: "..." }
```

---

### Challenge 4: Interactive Todo App (Upgrade)

Extend the to-do list from Section 3 with:
- A task **priority level** (dropdown: Low / Medium / High) when adding a task
- Color-coding the list items by priority (green/yellow/red)
- A **filter bar** with three buttons (All / Active / Completed) that shows/hides tasks
- A task **count** display: "3 tasks remaining"
- Tasks that persist across page refreshes using `localStorage`

---

### Challenge 5: Weather App Enhancements

Extend the Weather Card from page 08 with:
1. A **5-day forecast** section showing high/low temperatures for the next 5 days (use `daily=temperature_2m_max,temperature_2m_min` in the API URL)
2. A **"Feels like"** temperature displayed in the card (use `current_weather=true` and `hourly=apparent_temperature`)
3. **Unit toggle** — a button to switch between Celsius and Fahrenheit (store preference in localStorage)
4. **Loading skeleton** — while fetching, show animated placeholder boxes instead of the "Loading..." text

---

## Completion Checklist

- [ ] I can use the DevTools console to run and debug JavaScript
- [ ] I understand all primitive types, `===` vs `==`, and type coercion
- [ ] I can write `if/else`, `switch`, and ternary correctly
- [ ] I understand truthy/falsy and can predict which values are which
- [ ] I can use all loop types and know when to use each
- [ ] I can write functions in all three syntaxes and explain the differences
- [ ] I understand closures and scope chains
- [ ] I can use `try/catch/finally` and `throw` appropriately
- [ ] I can work with nested arrays and objects including destructuring
- [ ] I can build an interactive page with forms, validation, and DOM manipulation
- [ ] I can make API calls with `fetch` and `async/await` and handle errors
- [ ] I completed all five code challenges

---

*Ready for next section? → [Section 5: DOM, Forms, and AJAX Deep Dive](../section-05-dom-forms-ajax/01-dom-manipulation.md)*

*Review sources: [10 — Sources](10-sources.md)*
