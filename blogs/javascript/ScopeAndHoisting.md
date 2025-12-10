# Scope and Hoisting

Scope and hoisting determine where variables can be accessed and how JavaScript processes your code. Understanding these concepts prevents confusing bugs and helps you write better code.

## What is scope?

Scope is the **area of your code** where a variable can be accessed. Think of it like rooms in a house — what's inside one room might not be visible from another.

```javascript
// Kitchen (global scope)
const cookName = "Alice";

function bedroom() {
  // Bedroom (function scope)
  const sleepName = "Bob";
  console.log(cookName);  // ✅ Can access kitchen
  console.log(sleepName); // ✅ Can access bedroom
}

console.log(cookName);  // ✅ Can access kitchen
console.log(sleepName); // ❌ Error! Can't access bedroom from kitchen
```

## Types of scope

### 1. Global scope

Variables declared outside any function — accessible **everywhere**.

```javascript
const globalVar = "I'm global!";

function test() {
  console.log(globalVar); // Works!
}

test();
console.log(globalVar); // Works!
```

### 2. Function scope

Variables declared inside a function — only accessible **inside that function**.

```javascript
function myFunction() {
  const localVar = "I'm local!";
  console.log(localVar); // ✅ Works inside function
}

myFunction();
console.log(localVar); // ❌ Error! Not accessible outside
```

### 3. Block scope

Variables declared inside `{ }` (with `let` or `const`) — only accessible **inside that block**.

```javascript
if (true) {
  let blockVar = "I'm in a block!";
  console.log(blockVar); // ✅ Works inside block
}

console.log(blockVar); // ❌ Error! Not accessible outside
```

## Let, Const, and Var

### `let` — block-scoped, can reassign

```javascript
let name = "Alice";
name = "Bob"; // ✅ Can change

if (true) {
  let name = "Charlie"; // Different variable
  console.log(name); // Charlie
}

console.log(name); // Bob (original)
```

### `const` — block-scoped, cannot reassign

```javascript
const age = 25;
age = 30; // ❌ Error! Can't reassign

const person = { name: "Alice" };
person.name = "Bob"; // ✅ Can modify object properties
person = {};         // ❌ Error! Can't reassign object
```

### `var` — function-scoped (avoid using!)

```javascript
if (true) {
  var oldStyle = "Visible outside!";
}

console.log(oldStyle); // ✅ Works (but confusing!)
```

**Why avoid `var`?**

```javascript
// Problem 1: No block scope
for (var i = 0; i < 3; i++) {
  // Code
}
console.log(i); // 3 (i leaked outside!)

// With let (better)
for (let j = 0; j < 3; j++) {
  // Code
}
console.log(j); // ❌ Error! (properly scoped)

// Problem 2: Hoisting confusion (explained below)
console.log(x); // undefined (not an error!)
var x = 5;
```

## Nested scopes

Inner scopes can access outer scopes, but not vice versa.

```javascript
const global = "Global";

function outer() {
  const outerVar = "Outer";
  
  function inner() {
    const innerVar = "Inner";
    
    console.log(global);    // ✅ Can access
    console.log(outerVar);  // ✅ Can access
    console.log(innerVar);  // ✅ Can access
  }
  
  inner();
  console.log(innerVar); // ❌ Error! Can't access inner
}

outer();
```

### Visual representation

```javascript
// Scope chain: inner → outer → global
const level1 = "Global";

function level2() {
  const level2Var = "Function";
  
  if (true) {
    const level3Var = "Block";
    // Can access: level3Var, level2Var, level1
  }
  
  // Can access: level2Var, level1
  // Cannot access: level3Var
}

// Can only access: level1
```

## What is hoisting?

Hoisting is JavaScript's behavior of **moving declarations to the top** of their scope before code runs.

### Function hoisting

```javascript
// You can call a function before it's declared
greet(); // Works!

function greet() {
  console.log("Hello!");
}
```

Behind the scenes, JavaScript does this:

```javascript
// JavaScript moves the declaration to the top
function greet() {
  console.log("Hello!");
}

greet(); // Now it makes sense!
```

### Variable hoisting with `var`

```javascript
console.log(x); // undefined (not an error!)
var x = 5;
console.log(x); // 5
```

What JavaScript actually does:

```javascript
var x; // Declaration is hoisted
console.log(x); // undefined (declared but not assigned)
x = 5; // Assignment stays in place
console.log(x); // 5
```

### `let` and `const` hoisting

They're hoisted but not initialized — creating the "temporal dead zone".

```javascript
console.log(x); // ❌ Error! Can't access before initialization
let x = 5;
```

```javascript
console.log(y); // ❌ Error! Can't access before initialization
const y = 10;
```

### Function expression hoisting

```javascript
greet(); // ❌ Error! Can't access before initialization

const greet = function() {
  console.log("Hello!");
};
```

## Temporal Dead Zone (TDZ)

The period between entering scope and the variable being declared.

```javascript
{
  // TDZ starts here
  console.log(x); // ❌ Error! In TDZ
  
  let x = 5; // TDZ ends here
  console.log(x); // ✅ Works
}
```

## Closures

A closure is when a function **remembers** variables from its outer scope, even after the outer function has finished.

### Basic closure

```javascript
function outer() {
  const message = "Hello";
  
  function inner() {
    console.log(message); // inner "closes over" message
  }
  
  return inner;
}

const myFunction = outer();
myFunction(); // "Hello" (still remembers message!)
```

### Why closures are useful

```javascript
function counter() {
  let count = 0;
  
  return function() {
    count++;
    return count;
  };
}

const increment = counter();
console.log(increment()); // 1
console.log(increment()); // 2
console.log(increment()); // 3
```

Each time you call `increment()`, it remembers the `count` variable!

### Practical closure example

```javascript
function createGreeter(greeting) {
  return function(name) {
    return `${greeting}, ${name}!`;
  };
}

const sayHello = createGreeter("Hello");
const sayHi = createGreeter("Hi");

console.log(sayHello("Alice")); // "Hello, Alice!"
console.log(sayHi("Bob"));      // "Hi, Bob!"
```

## Playwright examples

### Example 1: Scope in test functions

```javascript
// Global scope
const baseURL = "https://example.com";

test('Login test', async ({ page }) => {
  // Test function scope
  const username = "testuser";
  const password = "pass123";
  
  await page.goto(baseURL); // ✅ Can access global
  
  if (await page.isVisible('#login')) {
    // Block scope
    const loginBtn = '#login-btn';
    await page.fill('#username', username); // ✅ Can access function scope
    await page.fill('#password', password);
    await page.click(loginBtn); // ✅ Can access block scope
  }
  
  // console.log(loginBtn); // ❌ Error! Block scope
});
```

### Example 2: Closure for test configuration

```javascript
function createTestConfig(environment) {
  const baseURL = environment === 'prod' 
    ? 'https://example.com' 
    : 'https://staging.example.com';
  
  return {
    getURL: function(path) {
      return `${baseURL}${path}`; // Closure: remembers baseURL
    },
    
    getEnv: function() {
      return environment; // Closure: remembers environment
    }
  };
}

const prodConfig = createTestConfig('prod');
const stagingConfig = createTestConfig('staging');

console.log(prodConfig.getURL('/login'));    // https://example.com/login
console.log(stagingConfig.getURL('/login')); // https://staging.example.com/login
```

### Example 3: Private variables with closures

```javascript
function createTestCounter() {
  let passed = 0;
  let failed = 0;
  
  return {
    pass: function() {
      passed++;
    },
    
    fail: function() {
      failed++;
    },
    
    report: function() {
      return {
        passed: passed,
        failed: failed,
        total: passed + failed
      };
    }
  };
}

const testResults = createTestCounter();
testResults.pass();
testResults.pass();
testResults.fail();

console.log(testResults.report());
// { passed: 2, failed: 1, total: 3 }

// Cannot directly access passed or failed
console.log(testResults.passed); // undefined (protected by closure)
```

### Example 4: Retry mechanism with closure

```javascript
function createRetryHandler(maxRetries) {
  let attemptCount = 0;
  
  return async function(operation) {
    while (attemptCount < maxRetries) {
      try {
        await operation();
        console.log(`Success on attempt ${attemptCount + 1}`);
        return;
      } catch (error) {
        attemptCount++;
        console.log(`Attempt ${attemptCount} failed`);
        
        if (attemptCount >= maxRetries) {
          throw new Error(`Failed after ${maxRetries} attempts`);
        }
      }
    }
  };
}

const retry = createRetryHandler(3);

await retry(async () => {
  await page.click('#submit-btn');
});
```

## Common mistakes

### Mistake 1: Using `var` in loops

```javascript
// ❌ Problem
for (var i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i); // Prints: 3, 3, 3
  }, 1000);
}

// ✅ Solution with let
for (let i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i); // Prints: 0, 1, 2
  }, 1000);
}
```

### Mistake 2: Accessing variables before declaration

```javascript
// ❌ Error
console.log(name);
let name = "Alice";

// ✅ Correct
let name = "Alice";
console.log(name);
```

### Mistake 3: Expecting block scope with `var`

```javascript
// ❌ Confusing
if (true) {
  var x = 5;
}
console.log(x); // 5 (var is function-scoped)

// ✅ Better with let
if (true) {
  let y = 5;
}
console.log(y); // Error (let is block-scoped)
```

### Mistake 4: Not understanding closure memory

```javascript
// ❌ Memory leak risk
function createHugeArray() {
  const huge = new Array(1000000).fill('data');
  
  return function() {
    // Even though we don't use 'huge',
    // the closure keeps it in memory
    console.log("Hello");
  };
}

// ✅ Better - don't create unnecessary closures
function createFunction() {
  // Don't create huge array if not needed
  return function() {
    console.log("Hello");
  };
}
```

## Best practices

### 1. Use `const` by default

```javascript
const name = "Alice"; // Can't reassign (good!)
let age = 25;         // Can reassign if needed
// Avoid var
```

### 2. Declare variables at the top

```javascript
// ✅ Clear and readable
function test() {
  const a = 1;
  const b = 2;
  const c = 3;
  
  // Use variables
  console.log(a + b + c);
}
```

### 3. Keep scope as narrow as possible

```javascript
// ❌ Unnecessarily wide scope
const result = null;
if (condition) {
  result = calculate();
}

// ✅ Narrow scope
if (condition) {
  const result = calculate();
  // Use result here
}
```

### 4. Use closures for private data

```javascript
function createUser(name) {
  // Private variable
  let password = "secret";
  
  return {
    getName: () => name,
    checkPassword: (input) => input === password,
    // No way to directly access or modify password
  };
}
```

## Summary

**Scope:**
- **Global scope** — accessible everywhere
- **Function scope** — accessible inside function only
- **Block scope** — accessible inside `{ }` only (with `let`/`const`)

**Hoisting:**
- Functions are fully hoisted (can call before declaration)
- `var` is hoisted but initialized as `undefined`
- `let`/`const` are hoisted but not initialized (temporal dead zone)

**Closures:**
- Functions remember variables from outer scope
- Useful for private data and maintaining state
- Common in callbacks and event handlers

**Best practices:**
- Use `const` by default, `let` when needed, avoid `var`
- Declare variables at the top of their scope
- Keep scope as narrow as possible
- Use closures for encapsulation

Understanding scope and hoisting is crucial for writing reliable Playwright tests and avoiding mysterious bugs!
