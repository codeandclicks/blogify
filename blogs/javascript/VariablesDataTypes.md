# Variables and Data Types

Understanding variables and data types is the foundation of JavaScript programming. They allow you to store, manipulate, and work with information in Playwright automation scripts.

## What are variables?

Variables are **containers** that store data. Think of them as labeled boxes where you can put different types of information.

```javascript
let name = "Alice";     // A box labeled "name" containing "Alice"
let age = 25;           // A box labeled "age" containing 25
let isActive = true;    // A box labeled "isActive" containing true
```

## Declaring variables

JavaScript offers three ways to declare variables:

### `const` — cannot be reassigned (recommended)

Use `const` when the value won't change.

```javascript
const PI = 3.14159;
const username = "testuser";
const maxRetries = 3;

PI = 3.14; // ❌ Error! Cannot reassign const
```

**When to use:**
- Configuration values
- API endpoints
- Values that shouldn't change

### `let` — can be reassigned

Use `let` when the value will change.

```javascript
let count = 0;
count = 1;    // ✅ Works! Can reassign
count = 2;    // ✅ Works!

let message = "Loading...";
message = "Done!"; // ✅ Can change
```

**When to use:**
- Counters
- Loop variables
- Values that update

### `var` — old way (avoid!)

`var` is the old way of declaring variables. **Don't use it** in modern JavaScript.

```javascript
var oldStyle = "Avoid this"; // ❌ Use let or const instead
```

**Problems with var:**
- No block scope (leaks outside `{ }`)
- Can be redeclared (causes confusion)
- Hoisting issues

## Primitive data types

JavaScript has 7 primitive (basic) data types:

### 1. String — text values

Strings hold text. Use single quotes `' '`, double quotes `" "`, or backticks `` ` ` ``.

```javascript
const firstName = "Alice";
const lastName = 'Smith';
const greeting = `Hello, ${firstName}!`; // Template literal

console.log(greeting); // Hello, Alice!
```

**Common operations:**

```javascript
const name = "JavaScript";

console.log(name.length);           // 10 (length)
console.log(name.toUpperCase());    // JAVASCRIPT
console.log(name.toLowerCase());    // javascript
console.log(name.includes("Script")); // true
console.log(name.slice(0, 4));      // Java
```

### 2. Number — integers and decimals

JavaScript has only one number type for all numbers.

```javascript
const age = 25;              // Integer
const price = 19.99;         // Decimal
const negative = -10;        // Negative
const scientific = 1.5e6;    // 1,500,000
```

**Common operations:**

```javascript
const a = 10;
const b = 3;

console.log(a + b);     // 13 (addition)
console.log(a - b);     // 7 (subtraction)
console.log(a * b);     // 30 (multiplication)
console.log(a / b);     // 3.333... (division)
console.log(a % b);     // 1 (remainder)
console.log(a ** b);    // 1000 (exponent: 10³)
```

### 3. Boolean — true or false

Booleans represent yes/no, on/off, true/false.

```javascript
const isLoggedIn = true;
const hasError = false;
const isVisible = true;

if (isLoggedIn) {
  console.log("Welcome!");
}
```

**Common uses:**
- Conditions and comparisons
- Feature flags
- Status checks

### 4. Undefined — not assigned

When a variable is declared but not assigned a value.

```javascript
let username;
console.log(username); // undefined

let age;
console.log(age); // undefined
```

### 5. Null — intentionally empty

`null` means "no value" or "empty" on purpose.

```javascript
let selectedUser = null; // No user selected yet

if (selectedUser === null) {
  console.log("Please select a user");
}
```

**Difference between `null` and `undefined`:**
- `undefined` = variable exists but has no value yet
- `null` = explicitly set to "no value"

### 6. Symbol — unique identifier

Symbols create unique identifiers (advanced, rarely used in basic code).

```javascript
const id1 = Symbol('id');
const id2 = Symbol('id');

console.log(id1 === id2); // false (each symbol is unique)
```

### 7. BigInt — very large numbers

For numbers larger than `Number.MAX_SAFE_INTEGER` (9007199254740991).

```javascript
const bigNumber = 9007199254740991n; // Note the 'n' at the end
const anotherBig = BigInt("9007199254740991");

console.log(bigNumber + 1n); // 9007199254740992n
```

## Complex data types

### Arrays — ordered lists

Arrays store multiple values in order.

```javascript
const fruits = ["apple", "banana", "orange"];
const numbers = [1, 2, 3, 4, 5];
const mixed = [1, "text", true, null];

// Access elements by index (starts at 0)
console.log(fruits[0]);  // apple
console.log(fruits[1]);  // banana
console.log(fruits[2]);  // orange
```

**Common array operations:**

```javascript
const colors = ["red", "green", "blue"];

// Add elements
colors.push("yellow");        // Add to end
colors.unshift("purple");     // Add to start

// Remove elements
colors.pop();                 // Remove from end
colors.shift();               // Remove from start

// Other operations
console.log(colors.length);   // 3
console.log(colors.includes("red")); // true
console.log(colors.indexOf("green")); // 1
```

**Loop through arrays:**

```javascript
const browsers = ["Chrome", "Firefox", "Safari"];

for (const browser of browsers) {
  console.log(browser);
}
// Output: Chrome, Firefox, Safari
```

### Objects — key-value pairs

Objects store related data together using keys and values.

```javascript
const person = {
  name: "Alice",
  age: 25,
  city: "New York",
  isActive: true
};

// Access properties
console.log(person.name);      // Alice (dot notation)
console.log(person["age"]);    // 25 (bracket notation)

// Update properties
person.age = 26;
person.city = "Boston";

// Add new properties
person.email = "alice@example.com";
```

**Nested objects:**

```javascript
const user = {
  name: "Bob",
  address: {
    street: "123 Main St",
    city: "NYC",
    zip: "10001"
  }
};

console.log(user.address.city); // NYC
```

## Type checking

Use `typeof` to check the data type:

```javascript
console.log(typeof "hello");        // string
console.log(typeof 42);             // number
console.log(typeof true);           // boolean
console.log(typeof undefined);      // undefined
console.log(typeof null);           // object (JavaScript quirk!)
console.log(typeof [1, 2, 3]);      // object (arrays are objects)
console.log(typeof {name: "Alice"}); // object
```

## Type conversion

### String to number

```javascript
const text = "123";
const num = Number(text);     // 123
const num2 = parseInt(text);  // 123
const num3 = parseFloat("3.14"); // 3.14

console.log(typeof num); // number
```

### Number to string

```javascript
const age = 25;
const text = String(age);      // "25"
const text2 = age.toString();  // "25"

console.log(typeof text); // string
```

### To boolean

```javascript
console.log(Boolean(1));        // true
console.log(Boolean(0));        // false
console.log(Boolean("hello"));  // true
console.log(Boolean(""));       // false
console.log(Boolean(null));     // false
console.log(Boolean(undefined)); // false
```

## Playwright examples

### Storing test data

```javascript
// Configuration
const baseURL = "https://example.com";
const timeout = 30000;
const retries = 3;

// User credentials
const testUser = {
  username: "testuser",
  password: "password123",
  email: "test@example.com"
};

// Selectors
const selectors = {
  loginButton: "#login-btn",
  usernameField: "input[name='username']",
  passwordField: "input[name='password']"
};
```

### Working with test data arrays

```javascript
// Test multiple browsers
const browsers = ["chromium", "firefox", "webkit"];

for (const browser of browsers) {
  console.log(`Testing on ${browser}`);
  // Run tests
}

// Test multiple URLs
const pages = [
  "/home",
  "/about",
  "/contact"
];

for (const page of pages) {
  await page.goto(`${baseURL}${page}`);
  // Test the page
}
```

### Dynamic selectors

```javascript
const buttonId = "submit";
const selector = `#${buttonId}`; // "#submit"

await page.click(selector);
```

### Storing results

```javascript
let passedTests = 0;
let failedTests = 0;

// After each test
if (testPassed) {
  passedTests++;
} else {
  failedTests++;
}

console.log(`Results: ${passedTests} passed, ${failedTests} failed`);
```

## Best practices

1. **Use `const` by default** — only use `let` when you need to reassign
2. **Never use `var`** — it causes confusing scope issues
3. **Use descriptive names** — `userName` is better than `u`
4. **Use camelCase** — `firstName`, not `first_name` or `FirstName`
5. **Group related data in objects** — easier to manage
6. **Use arrays for lists** — better than `item1`, `item2`, `item3`

```javascript
// ❌ Bad
var x = "test";
var y = "user";
var z = true;

// ✅ Good
const testData = {
  name: "test",
  type: "user",
  isActive: true
};
```

## Common mistakes

### Forgetting to declare variables

```javascript
// ❌ Creates global variable accidentally
username = "Alice";

// ✅ Always declare with const or let
const username = "Alice";
```

### Confusing `=` with `===`

```javascript
// ❌ Assignment (sets value)
if (age = 18) {  // Always true!
  console.log("Adult");
}

// ✅ Comparison (checks value)
if (age === 18) {
  console.log("Adult");
}
```

### Not using the right data type

```javascript
// ❌ String when you need a number
const count = "5";
console.log(count + 1); // "51" (string concatenation)

// ✅ Use the correct type
const count = 5;
console.log(count + 1); // 6 (addition)
```

## Summary

**Variables:**
- `const` — cannot reassign (use by default)
- `let` — can reassign (use when needed)
- `var` — avoid!

**Primitive types:**
- **String** — text (`"hello"`)
- **Number** — all numbers (`42`, `3.14`)
- **Boolean** — true/false
- **Undefined** — not assigned
- **Null** — intentionally empty
- **Symbol** — unique identifiers
- **BigInt** — very large numbers

**Complex types:**
- **Array** — ordered list (`[1, 2, 3]`)
- **Object** — key-value pairs (`{ name: "Alice" }`)

**In Playwright:**
- Store selectors, URLs, credentials
- Use objects for test configuration
- Use arrays for multiple test cases
- Use strings for dynamic selectors

Master variables and data types, and you'll have a solid foundation for writing Playwright automation scripts!
