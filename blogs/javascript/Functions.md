# Functions

Functions are reusable blocks of code that perform specific tasks. Instead of writing the same code multiple times, you write it once in a function and call it whenever needed.

## What is a function?

Think of a function like a recipe:
- **Ingredients** = Parameters (inputs)
- **Instructions** = Function body (code)
- **Finished dish** = Return value (output)

```javascript
// A simple function
function greet() {
  console.log("Hello!");
}

greet(); // Call the function
// Output: Hello!
```

## Function declarations

The traditional way to create functions.

### Basic syntax

```javascript
function functionName() {
  // Code to run
}
```

### Example

```javascript
function sayHello() {
  console.log("Hello, World!");
}

sayHello(); // Output: Hello, World!
```

### Functions with parameters

Parameters let you pass data into functions.

```javascript
function greet(name) {
  console.log("Hello, " + name + "!");
}

greet("Alice");  // Output: Hello, Alice!
greet("Bob");    // Output: Hello, Bob!
```

### Multiple parameters

```javascript
function add(a, b) {
  console.log(a + b);
}

add(5, 3);   // Output: 8
add(10, 20); // Output: 30
```

### Playwright example

```javascript
function clickButton(buttonId) {
  await page.click(`#${buttonId}`);
  console.log(`Clicked button: ${buttonId}`);
}

clickButton('submit-btn');
clickButton('cancel-btn');
```

## Return values

Functions can **return** a value back to where they were called.

```javascript
function add(a, b) {
  return a + b; // Send result back
}

const sum = add(5, 3); // sum = 8
console.log(sum);      // Output: 8
```

### Without return

```javascript
function greet(name) {
  console.log("Hello, " + name);
  // No return statement
}

const result = greet("Alice");
console.log(result); // Output: undefined
```

### With return

```javascript
function multiply(a, b) {
  return a * b;
}

const result = multiply(4, 5);
console.log(result); // Output: 20
```

### Early return

```javascript
function checkAge(age) {
  if (age < 18) {
    return "Too young"; // Exit immediately
  }
  
  return "Old enough";
}

console.log(checkAge(15)); // Too young
console.log(checkAge(25)); // Old enough
```

### Playwright example

```javascript
async function isElementVisible(selector) {
  const element = page.locator(selector);
  return await element.isVisible();
}

const visible = await isElementVisible('#login-btn');
if (visible) {
  console.log("Button is visible!");
}
```

## Function expressions

Store a function in a variable.

```javascript
const greet = function(name) {
  console.log("Hello, " + name);
};

greet("Alice"); // Output: Hello, Alice!
```

### Difference from declarations

```javascript
// ✅ Declaration - can call before definition
sayHi(); // Works!

function sayHi() {
  console.log("Hi!");
}

// ❌ Expression - must define before calling
sayBye(); // Error!

const sayBye = function() {
  console.log("Bye!");
};
```

## Arrow functions

Modern, shorter syntax introduced in ES6.

### Basic syntax

```javascript
// Traditional function
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => {
  return a + b;
};

// Even shorter (implicit return)
const add = (a, b) => a + b;
```

### Examples

```javascript
// No parameters
const greet = () => console.log("Hello!");

// One parameter (parentheses optional)
const square = x => x * x;
const square = (x) => x * x; // Same thing

// Multiple parameters (parentheses required)
const add = (a, b) => a + b;

// Multiple lines (need curly braces and return)
const calculate = (a, b) => {
  const sum = a + b;
  return sum * 2;
};
```

### When to use arrow functions

```javascript
// ✅ Good for simple operations
const double = x => x * 2;

// ✅ Good for callbacks
const numbers = [1, 2, 3];
const doubled = numbers.map(n => n * 2);

// ❌ Not good for methods (different 'this' behavior)
const person = {
  name: "Alice",
  greet: () => {
    console.log(this.name); // Won't work as expected
  }
};
```

### Playwright example

```javascript
// Traditional function
async function clickAndWait(selector) {
  await page.click(selector);
  await page.waitForTimeout(1000);
}

// Arrow function (shorter)
const clickAndWait = async (selector) => {
  await page.click(selector);
  await page.waitForTimeout(1000);
};
```

## Default parameters

Provide default values when parameters aren't passed.

```javascript
function greet(name = "Guest") {
  console.log("Hello, " + name);
}

greet("Alice"); // Hello, Alice
greet();        // Hello, Guest (uses default)
```

### Multiple defaults

```javascript
function createUser(name = "Anonymous", age = 0, country = "Unknown") {
  return {
    name: name,
    age: age,
    country: country
  };
}

createUser("Alice", 25, "USA"); // All provided
createUser("Bob", 30);          // country = "Unknown"
createUser();                   // All defaults
```

### Playwright example

```javascript
async function fillForm(username, password = "default123") {
  await page.fill('#username', username);
  await page.fill('#password', password);
}

fillForm("testuser", "mypass"); // Use mypass
fillForm("testuser");           // Use default123
```

## Rest parameters

Collect multiple arguments into an array.

```javascript
function sum(...numbers) {
  let total = 0;
  for (const num of numbers) {
    total += num;
  }
  return total;
}

console.log(sum(1, 2, 3));       // 6
console.log(sum(1, 2, 3, 4, 5)); // 15
```

### Mixed parameters

```javascript
function introduce(greeting, ...names) {
  console.log(greeting);
  for (const name of names) {
    console.log("- " + name);
  }
}

introduce("Welcome:", "Alice", "Bob", "Charlie");
// Output:
// Welcome:
// - Alice
// - Bob
// - Charlie
```

## Callback functions

A function passed as an argument to another function.

```javascript
function processUser(name, callback) {
  console.log("Processing: " + name);
  callback();
}

processUser("Alice", function() {
  console.log("Done!");
});

// Output:
// Processing: Alice
// Done!
```

### With arrow functions

```javascript
processUser("Bob", () => {
  console.log("Finished!");
});
```

### Real example

```javascript
const numbers = [1, 2, 3, 4, 5];

// forEach with callback
numbers.forEach(function(num) {
  console.log(num * 2);
});

// Or with arrow function
numbers.forEach(num => console.log(num * 2));
```

### Playwright example

```javascript
async function retryOperation(operation, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      await operation(); // Call the callback function
      return; // Success, exit
    } catch (error) {
      console.log(`Attempt ${i + 1} failed`);
    }
  }
  throw new Error("All retries failed");
}

// Usage
await retryOperation(async () => {
  await page.click('#submit-btn');
});
```

## Anonymous functions

Functions without names — often used as callbacks.

```javascript
// Named function
function greet() {
  console.log("Hello");
}

// Anonymous function
const numbers = [1, 2, 3];
numbers.forEach(function(num) { // No name
  console.log(num);
});
```

## IIFE (Immediately Invoked Function Expression)

A function that runs immediately when defined.

```javascript
(function() {
  console.log("I run immediately!");
})();

// With arrow function
(() => {
  console.log("I also run immediately!");
})();
```

### Why use IIFE?

```javascript
// Create private scope
(function() {
  const secret = "hidden";
  console.log(secret); // Works here
})();

console.log(secret); // Error: secret is not defined
```

## Higher-order functions

Functions that accept or return other functions.

### Function that accepts a function

```javascript
function repeat(times, action) {
  for (let i = 0; i < times; i++) {
    action(i);
  }
}

repeat(3, (num) => {
  console.log("Count: " + num);
});
// Output: Count: 0, Count: 1, Count: 2
```

### Function that returns a function

```javascript
function multiplier(factor) {
  return function(num) {
    return num * factor;
  };
}

const double = multiplier(2);
const triple = multiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

## Practical Playwright examples

### Example 1: Reusable login function

```javascript
async function login(username, password) {
  await page.goto('https://example.com/login');
  await page.fill('#username', username);
  await page.fill('#password', password);
  await page.click('#login-btn');
  await page.waitForURL('**/dashboard');
  console.log("Login successful!");
}

// Use it in tests
await login("testuser", "password123");
```

### Example 2: Wait for element with retry

```javascript
async function waitForElement(selector, maxWait = 5000) {
  const startTime = Date.now();
  
  while (Date.now() - startTime < maxWait) {
    const isVisible = await page.locator(selector).isVisible();
    if (isVisible) {
      return true;
    }
    await page.waitForTimeout(500);
  }
  
  return false;
}

// Usage
const found = await waitForElement('#submit-btn', 10000);
if (found) {
  await page.click('#submit-btn');
}
```

### Example 3: Extract data with custom function

```javascript
async function extractTableData(tableSelector) {
  const rows = await page.locator(`${tableSelector} tr`).all();
  const data = [];
  
  for (const row of rows) {
    const cells = await row.locator('td').allTextContents();
    data.push(cells);
  }
  
  return data;
}

// Usage
const tableData = await extractTableData('#users-table');
console.log(tableData);
```

### Example 4: Form filler with validation

```javascript
async function fillFormField(selector, value, validate = true) {
  await page.fill(selector, value);
  
  if (validate) {
    const actualValue = await page.inputValue(selector);
    if (actualValue !== value) {
      throw new Error(`Failed to fill ${selector}`);
    }
  }
  
  console.log(`Filled ${selector}: ${value}`);
}

// Usage
await fillFormField('#email', 'test@example.com');
await fillFormField('#phone', '1234567890', false); // No validation
```

### Example 5: Screenshot helper

```javascript
async function takeScreenshot(name, fullPage = false) {
  const timestamp = Date.now();
  const filename = `${name}-${timestamp}.png`;
  
  await page.screenshot({
    path: `screenshots/${filename}`,
    fullPage: fullPage
  });
  
  console.log(`Screenshot saved: ${filename}`);
  return filename;
}

// Usage
await takeScreenshot('login-page');
await takeScreenshot('dashboard', true); // Full page
```

## Best practices

- **Use descriptive names** — `calculateTotal()` is better than `calc()`
- **Keep functions small** — each function should do one thing well
- **Use arrow functions** for short callbacks and simple operations
- **Always return something** — return `null` or `undefined` explicitly if needed
- **Document complex functions** — add comments explaining what they do
- **Avoid too many parameters** — if you need more than 3-4, consider using an object

```javascript
// ❌ Too many parameters
function createUser(name, age, email, phone, address, city, country) {
  // ...
}

// ✅ Use an object instead
function createUser(userData) {
  const { name, age, email, phone, address, city, country } = userData;
  // ...
}
```

## Common mistakes

### Forgetting to call the function

```javascript
function greet() {
  console.log("Hello!");
}

greet;   // ❌ Does nothing (forgot parentheses)
greet(); // ✅ Runs the function
```

### Forgetting async/await in Playwright

```javascript
// ❌ Won't work properly
function clickButton() {
  page.click('#btn'); // Missing await!
}

// ✅ Correct
async function clickButton() {
  await page.click('#btn');
}
```

### Confusing return and console.log

```javascript
function add(a, b) {
  console.log(a + b); // Prints but doesn't return
}

const result = add(2, 3); // result = undefined
console.log(result);      // undefined

// ✅ Use return
function add(a, b) {
  return a + b;
}

const result = add(2, 3); // result = 5
```

## Summary

Functions are essential for writing clean, reusable code:

- **Function declarations** — traditional way
- **Function expressions** — stored in variables
- **Arrow functions** — modern, concise syntax
- **Parameters** — pass data into functions
- **Return values** — get results back
- **Callbacks** — functions as arguments
- **Default parameters** — provide fallback values

In Playwright, you'll use functions to:
- Organize test code
- Reuse common actions (login, navigation)
- Handle retries and errors
- Extract and validate data
- Create custom helpers

Master functions and your code will be more organized, readable, and maintainable!
