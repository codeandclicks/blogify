# ES6+ Features

ES6 (ECMAScript 2015) revolutionized JavaScript with modern features that make code cleaner, shorter, and more powerful. These features are essential for modern development and Playwright automation.

## Template literals

Template literals make string formatting easier using backticks (`` ` ``) instead of quotes.

### Basic usage

```javascript
// Old way with concatenation
const name = "Alice";
const greeting = "Hello, " + name + "!";

// New way with template literals
const greeting = `Hello, ${name}!`;
```

### Expressions inside strings

```javascript
const a = 5;
const b = 10;

console.log(`${a} + ${b} = ${a + b}`);
// Output: 5 + 10 = 15
```

### Multi-line strings

```javascript
// Old way - awkward
const oldPoem = "Roses are red,\n" +
                "Violets are blue,\n" +
                "JavaScript is awesome,\n" +
                "And so are you!";

// New way - natural
const newPoem = `Roses are red,
Violets are blue,
JavaScript is awesome,
And so are you!`;
```

### Playwright examples

```javascript
// Build dynamic selectors
const buttonId = 'submit';
const selector = `#${buttonId}`;
await page.click(selector);

// Log test results
const testName = 'Login Test';
const duration = 1.5;
console.log(`Test "${testName}" completed in ${duration}s`);

// Generate URLs
const env = 'staging';
const path = '/login';
const url = `https://${env}.example.com${path}`;
await page.goto(url);
```

## Destructuring

Extract values from arrays or objects into variables.

### Array destructuring

```javascript
// Old way
const colors = ['red', 'green', 'blue'];
const first = colors[0];
const second = colors[1];

// New way
const [first, second, third] = ['red', 'green', 'blue'];
console.log(first);  // red
console.log(second); // green
console.log(third);  // blue
```

### Skip elements

```javascript
const [first, , third] = ['red', 'green', 'blue'];
console.log(first); // red
console.log(third); // blue (skipped green)
```

### Object destructuring

```javascript
// Old way
const person = { name: 'Alice', age: 25, city: 'NYC' };
const name = person.name;
const age = person.age;

// New way
const { name, age, city } = { name: 'Alice', age: 25, city: 'NYC' };
console.log(name); // Alice
console.log(age);  // 25
console.log(city); // NYC
```

### Rename while destructuring

```javascript
const { name: userName, age: userAge } = { name: 'Alice', age: 25 };
console.log(userName); // Alice
console.log(userAge);  // 25
```

### Default values

```javascript
const { name = 'Guest', age = 0 } = { name: 'Alice' };
console.log(name); // Alice
console.log(age);  // 0 (default, since age wasn't in object)
```

### Nested destructuring

```javascript
const user = {
  name: 'Alice',
  address: {
    city: 'NYC',
    zip: '10001'
  }
};

const { name, address: { city, zip } } = user;
console.log(city); // NYC
console.log(zip);  // 10001
```

### Playwright examples

```javascript
// Destructure page and browser from test
test('My test', async ({ page, browser }) => {
  await page.goto('https://example.com');
});

// Destructure locator results
const { width, height } = await page.locator('#box').boundingBox();
console.log(`Size: ${width}x${height}`);

// Destructure environment config
const { baseURL, timeout, retries } = config;
await page.goto(baseURL);
```

## Spread operator (`...`)

Expands arrays or objects.

### Array spreading

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// Old way
const combined = arr1.concat(arr2);

// New way
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]
```

### Copy arrays

```javascript
const original = [1, 2, 3];
const copy = [...original];

copy.push(4);
console.log(original); // [1, 2, 3] (unchanged)
console.log(copy);     // [1, 2, 3, 4]
```

### Object spreading

```javascript
const person = { name: 'Alice', age: 25 };
const address = { city: 'NYC', country: 'USA' };

// Combine objects
const fullPerson = { ...person, ...address };
console.log(fullPerson);
// { name: 'Alice', age: 25, city: 'NYC', country: 'USA' }
```

### Override properties

```javascript
const defaults = { color: 'blue', size: 'medium' };
const custom = { ...defaults, color: 'red' };

console.log(custom);
// { color: 'red', size: 'medium' } (color overridden)
```

### Playwright examples

```javascript
// Merge test configurations
const baseConfig = { timeout: 30000, retries: 2 };
const customConfig = { ...baseConfig, timeout: 60000 };

// Clone locator array
const buttons = await page.locator('button').all();
const buttonsCopy = [...buttons];

// Combine test data
const defaultUser = { username: 'test', role: 'user' };
const adminUser = { ...defaultUser, role: 'admin' };
```

## Rest operator (`...`)

Collects multiple elements into an array.

### Function parameters

```javascript
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3));       // 6
console.log(sum(1, 2, 3, 4, 5)); // 15
```

### Mixed parameters

```javascript
function introduce(greeting, ...names) {
  console.log(greeting);
  names.forEach(name => console.log(`- ${name}`));
}

introduce('Welcome:', 'Alice', 'Bob', 'Charlie');
// Welcome:
// - Alice
// - Bob
// - Charlie
```

### Array destructuring with rest

```javascript
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(first);  // 1
console.log(second); // 2
console.log(rest);   // [3, 4, 5]
```

### Object destructuring with rest

```javascript
const person = { name: 'Alice', age: 25, city: 'NYC', country: 'USA' };
const { name, age, ...location } = person;

console.log(name);     // Alice
console.log(age);      // 25
console.log(location); // { city: 'NYC', country: 'USA' }
```

### Playwright examples

```javascript
// Accept multiple selectors
async function clickAll(...selectors) {
  for (const selector of selectors) {
    await page.click(selector);
  }
}

await clickAll('#btn1', '#btn2', '#btn3');

// Flexible test data
async function fillForm(required, ...optional) {
  await page.fill('#required', required);
  
  for (const field of optional) {
    await page.fill(`#${field.name}`, field.value);
  }
}
```

## Default parameters

Provide default values for function parameters.

### Basic usage

```javascript
function greet(name = 'Guest') {
  console.log(`Hello, ${name}!`);
}

greet('Alice'); // Hello, Alice!
greet();        // Hello, Guest!
```

### Multiple defaults

```javascript
function createUser(name = 'Anonymous', role = 'user', active = true) {
  return { name, role, active };
}

console.log(createUser());
// { name: 'Anonymous', role: 'user', active: true }

console.log(createUser('Alice', 'admin'));
// { name: 'Alice', role: 'admin', active: true }
```

### Expressions as defaults

```javascript
function calculate(a, b = a * 2) {
  return a + b;
}

console.log(calculate(5));    // 15 (5 + 10)
console.log(calculate(5, 3)); // 8 (5 + 3)
```

### Playwright examples

```javascript
async function login(username, password, remember = false) {
  await page.fill('#username', username);
  await page.fill('#password', password);
  
  if (remember) {
    await page.check('#remember-me');
  }
  
  await page.click('#login-btn');
}

// Use with default
await login('user', 'pass123');

// Override default
await login('user', 'pass123', true);
```

## Arrow functions

Shorter syntax for functions (covered in Functions.md, but worth mentioning in ES6).

```javascript
// Traditional
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => a + b;

// Array methods with arrows
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
```

## Enhanced object literals

### Property shorthand

```javascript
const name = 'Alice';
const age = 25;

// Old way
const person = { name: name, age: age };

// New way (property names match variable names)
const person = { name, age };
```

### Method shorthand

```javascript
// Old way
const obj = {
  greet: function() {
    console.log('Hello');
  }
};

// New way
const obj = {
  greet() {
    console.log('Hello');
  }
};
```

### Computed property names

```javascript
const fieldName = 'email';
const fieldValue = 'test@example.com';

const user = {
  name: 'Alice',
  [fieldName]: fieldValue // Dynamic property name
};

console.log(user);
// { name: 'Alice', email: 'test@example.com' }
```

### Playwright examples

```javascript
// Build test config
const timeout = 30000;
const retries = 3;
const headless = true;

const config = { timeout, retries, headless };

// Dynamic selectors
const fieldName = 'username';
const testData = {
  [fieldName]: 'testuser',
  [`${fieldName}_label`]: 'Username:'
};
```

## Let and const

### `let` — block-scoped, reassignable

```javascript
let count = 0;
count = 1; // ✅ Can reassign

if (true) {
  let blockVar = 'inside';
  console.log(blockVar); // ✅ Works
}
console.log(blockVar); // ❌ Error! Block-scoped
```

### `const` — block-scoped, cannot reassign

```javascript
const PI = 3.14159;
PI = 3; // ❌ Error! Can't reassign

const arr = [1, 2, 3];
arr.push(4);  // ✅ Can modify contents
arr = [];     // ❌ Can't reassign array itself
```

## Promises (brief intro, detailed in async/await section)

Handle asynchronous operations.

```javascript
// Create a promise
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Done!');
  }, 1000);
});

// Use the promise
myPromise.then(result => {
  console.log(result); // Done! (after 1 second)
});
```

## Optional chaining (`?.`) - ES2020

Safely access nested properties.

```javascript
const user = {
  name: 'Alice',
  address: {
    city: 'NYC'
  }
};

// Old way - risky
const country = user.address.country; // Error if address is undefined

// Old way - safe but verbose
const country = user && user.address && user.address.country;

// New way - clean and safe
const country = user?.address?.country; // undefined (no error)
```

### Playwright examples

```javascript
// Safely access element properties
const text = await page.locator('#optional')?.textContent();

// Chain method calls safely
const boundingBox = await page.locator('#box')?.boundingBox();
const width = boundingBox?.width ?? 0;
```

## Nullish coalescing (`??`) - ES2020

Return right value only if left is `null` or `undefined`.

```javascript
// With OR operator
const value1 = 0 || 10;      // 10 (0 is falsy)
const value2 = '' || 'test'; // 'test' ('' is falsy)

// With nullish coalescing
const value3 = 0 ?? 10;      // 0 (0 is not null/undefined)
const value4 = '' ?? 'test'; // '' ('' is not null/undefined)
```

### Playwright examples

```javascript
// Provide fallback values
const timeout = process.env.TIMEOUT ?? 30000;
const retries = config.retries ?? 3;

// Use with optional chaining
const title = await page.locator('h1')?.textContent() ?? 'No title';
```

## Practical Playwright examples

### Example 1: Configuration builder

```javascript
function createPlaywrightConfig(customConfig = {}) {
  const defaults = {
    timeout: 30000,
    retries: 2,
    headless: true,
    baseURL: 'https://example.com'
  };
  
  return { ...defaults, ...customConfig };
}

// Use with defaults
const config1 = createPlaywrightConfig();

// Override specific settings
const config2 = createPlaywrightConfig({ 
  timeout: 60000, 
  headless: false 
});
```

### Example 2: Dynamic form filler

```javascript
async function fillForm(formData) {
  for (const [field, value] of Object.entries(formData)) {
    const selector = `#${field}`;
    const element = page.locator(selector);
    
    if (await element?.isVisible()) {
      await element.fill(value);
      console.log(`Filled ${field}: ${value}`);
    }
  }
}

const userData = {
  username: 'testuser',
  email: 'test@example.com',
  phone: '1234567890'
};

await fillForm(userData);
```

### Example 3: Test data generator

```javascript
function generateTestUsers(count = 3) {
  return [...Array(count)].map((_, i) => ({
    id: i + 1,
    username: `user${i + 1}`,
    email: `user${i + 1}@test.com`,
    role: i === 0 ? 'admin' : 'user'
  }));
}

const users = generateTestUsers(5);
console.log(users);
// [
//   { id: 1, username: 'user1', email: 'user1@test.com', role: 'admin' },
//   { id: 2, username: 'user2', email: 'user2@test.com', role: 'user' },
//   ...
// ]
```

### Example 4: Flexible page navigator

```javascript
async function navigateToPage(page, path, options = {}) {
  const { 
    baseURL = 'https://example.com',
    waitFor = 'load',
    timeout = 30000 
  } = options;
  
  const fullURL = `${baseURL}${path}`;
  
  await page.goto(fullURL, { 
    waitUntil: waitFor,
    timeout 
  });
  
  console.log(`Navigated to: ${fullURL}`);
}

// Use with defaults
await navigateToPage(page, '/login');

// Custom options
await navigateToPage(page, '/dashboard', {
  waitFor: 'networkidle',
  timeout: 60000
});
```

## Summary

**ES6+ features make your code:**
- **Cleaner:** template literals, arrow functions
- **Safer:** let/const, optional chaining
- **More flexible:** destructuring, spread/rest operators
- **Easier to read:** default parameters, enhanced objects

**Most useful for Playwright:**
- **Template literals:** build dynamic selectors and URLs
- **Destructuring:** extract test data and configuration
- **Spread operator:** merge configs, clone arrays
- **Default parameters:** flexible test functions
- **Optional chaining:** safely access properties
- **Nullish coalescing:** provide fallback values

These features are essential for modern JavaScript development and will make your Playwright tests cleaner and more maintainable!
