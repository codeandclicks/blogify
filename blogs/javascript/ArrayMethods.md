# Array and Object Methods

Arrays and objects are everywhere in Playwright tests — lists of elements, test data, configuration. Mastering their methods makes your code cleaner and more powerful.

## Array Methods Overview

| Method | Purpose | Returns New Array? |
|--------|---------|-------------------|
| `map()` | Transform each element | ✅ Yes |
| `filter()` | Keep elements that pass test | ✅ Yes |
| `find()` | Find first matching element | ❌ No (returns element) |
| `forEach()` | Execute function on each | ❌ No (returns undefined) |
| `reduce()` | Reduce to single value | ❌ No (returns value) |
| `some()` | Check if any pass test | ❌ No (returns boolean) |
| `every()` | Check if all pass test | ❌ No (returns boolean) |
| `sort()` | Sort elements | ❌ No (mutates) |
| `slice()` | Extract portion | ✅ Yes |
| `concat()` | Merge arrays | ✅ Yes |

## map() - Transform Elements

Creates a new array by transforming each element.

```javascript
const numbers = [1, 2, 3, 4, 5];

// Double each number
const doubled = numbers.map(num => num * 2);
console.log(doubled);  // [2, 4, 6, 8, 10]

// Square each number
const squared = numbers.map(num => num ** 2);
console.log(squared);  // [1, 4, 9, 16, 25]
```

### Playwright Example: Extract Text from Elements

```javascript
async function getProductNames(page) {
  const products = await page.locator('.product-card').all();
  
  // Extract name from each product
  const names = await Promise.all(
    products.map(async product => {
      return await product.textContent('.product-name');
    })
  );
  
  return names;
}

// Usage
const names = await getProductNames(page);
console.log(names);  // ['Laptop', 'Mouse', 'Keyboard']
```

## filter() - Keep Matching Elements

Creates a new array with elements that pass a test.

```javascript
const numbers = [1, 2, 3, 4, 5, 6];

// Keep only even numbers
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens);  // [2, 4, 6]

// Keep numbers greater than 3
const greaterThan3 = numbers.filter(num => num > 3);
console.log(greaterThan3);  // [4, 5, 6]
```

### Playwright Example: Filter Visible Elements

```javascript
async function getVisibleProducts(page) {
  const allProducts = await page.locator('.product-card').all();
  
  // Keep only visible products
  const visibleProducts = [];
  for (const product of allProducts) {
    if (await product.isVisible()) {
      visibleProducts.push(product);
    }
  }
  
  return visibleProducts;
}

// Or filter test data
const users = [
  { name: 'Alice', active: true },
  { name: 'Bob', active: false },
  { name: 'Charlie', active: true }
];

const activeUsers = users.filter(user => user.active);
console.log(activeUsers);
// [{ name: 'Alice', active: true }, { name: 'Charlie', active: true }]
```

## find() - Find First Match

Returns the first element that passes a test.

```javascript
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' }
];

const user = users.find(u => u.id === 2);
console.log(user);  // { id: 2, name: 'Bob' }

const notFound = users.find(u => u.id === 999);
console.log(notFound);  // undefined
```

### Playwright Example: Find Specific Product

```javascript
async function findProductByName(page, targetName) {
  const products = await page.locator('.product-card').all();
  
  for (const product of products) {
    const name = await product.textContent('.product-name');
    if (name.trim() === targetName) {
      return product;
    }
  }
  
  return null;
}

// Usage
const laptop = await findProductByName(page, 'Laptop');
if (laptop) {
  await laptop.click();
}
```

## forEach() - Execute Function

Executes a function for each element. Doesn't return anything.

```javascript
const numbers = [1, 2, 3];

numbers.forEach(num => {
  console.log(num * 2);
});
// Output: 2, 4, 6

// With index
numbers.forEach((num, index) => {
  console.log(`Index ${index}: ${num}`);
});
// Index 0: 1
// Index 1: 2
// Index 2: 3
```

### Playwright Example: Click Multiple Elements

```javascript
async function clickAllCheckboxes(page) {
  const checkboxes = await page.locator('input[type="checkbox"]').all();
  
  // Click each checkbox
  for (const checkbox of checkboxes) {
    await checkbox.check();
  }
}
```

## reduce() - Reduce to Single Value

Reduces array to a single value by running a function.

```javascript
const numbers = [1, 2, 3, 4, 5];

// Sum all numbers
const sum = numbers.reduce((total, num) => total + num, 0);
console.log(sum);  // 15

// Find maximum
const max = numbers.reduce((max, num) => num > max ? num : max, numbers[0]);
console.log(max);  // 5
```

### Playwright Example: Count Element Types

```javascript
async function countElementTypes(page) {
  const elements = await page.locator('input').all();
  
  const counts = {};
  for (const element of elements) {
    const type = await element.getAttribute('type') || 'text';
    counts[type] = (counts[type] || 0) + 1;
  }
  
  return counts;
}

// Usage
const counts = await countElementTypes(page);
console.log(counts);
// { text: 5, checkbox: 3, radio: 2 }

// Or with test results
const testResults = [
  { name: 'test1', passed: true },
  { name: 'test2', passed: false },
  { name: 'test3', passed: true }
];

const summary = testResults.reduce((acc, test) => {
  if (test.passed) {
    acc.passed++;
  } else {
    acc.failed++;
  }
  return acc;
}, { passed: 0, failed: 0 });

console.log(summary);  // { passed: 2, failed: 1 }
```

## some() - Check If Any Match

Returns `true` if at least one element passes the test.

```javascript
const numbers = [1, 2, 3, 4, 5];

const hasEven = numbers.some(num => num % 2 === 0);
console.log(hasEven);  // true

const hasNegative = numbers.some(num => num < 0);
console.log(hasNegative);  // false
```

### Playwright Example: Check If Any Element Visible

```javascript
async function hasVisibleErrors(page) {
  const errors = await page.locator('.error-message').all();
  
  for (const error of errors) {
    if (await error.isVisible()) {
      return true;
    }
  }
  return false;
}

// Usage
if (await hasVisibleErrors(page)) {
  console.log('Form has errors');
}
```

## every() - Check If All Match

Returns `true` if all elements pass the test.

```javascript
const numbers = [2, 4, 6, 8];

const allEven = numbers.every(num => num % 2 === 0);
console.log(allEven);  // true

const allPositive = numbers.every(num => num > 0);
console.log(allPositive);  // true
```

### Playwright Example: Verify All Required Fields Filled

```javascript
async function areRequiredFieldsFilled(page) {
  const requiredFields = await page.locator('input[required]').all();
  
  for (const field of requiredFields) {
    const value = await field.inputValue();
    if (!value || value.trim() === '') {
      return false;
    }
  }
  return true;
}

// Usage
if (await areRequiredFieldsFilled(page)) {
  await page.click('#submit-button');
}
```

## sort() - Sort Elements

Sorts array in place. **Warning:** Mutates the original array!

```javascript
const numbers = [5, 2, 8, 1, 9];

// Sort ascending
numbers.sort((a, b) => a - b);
console.log(numbers);  // [1, 2, 5, 8, 9]

// Sort descending
numbers.sort((a, b) => b - a);
console.log(numbers);  // [9, 8, 5, 2, 1]

// Sort strings
const names = ['Charlie', 'Alice', 'Bob'];
names.sort();
console.log(names);  // ['Alice', 'Bob', 'Charlie']
```

### Playwright Example: Sort Products by Price

```javascript
const products = [
  { name: 'Laptop', price: 999 },
  { name: 'Mouse', price: 29 },
  { name: 'Keyboard', price: 79 }
];

// Sort by price ascending
const sorted = [...products].sort((a, b) => a.price - b.price);
console.log(sorted);
// [{ name: 'Mouse', price: 29 }, ...]

// Sort by name
const sortedByName = [...products].sort((a, b) => 
  a.name.localeCompare(b.name)
);
```

## Array Chaining

Combine multiple methods for powerful transformations.

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

const result = numbers
  .filter(num => num % 2 === 0)  // Keep evens: [2, 4, 6, 8, 10]
  .map(num => num * 2)            // Double them: [4, 8, 12, 16, 20]
  .reduce((sum, num) => sum + num, 0);  // Sum: 60

console.log(result);  // 60
```

### Playwright Example: Process Test Results

```javascript
const testResults = [
  { name: 'Login Test', duration: 1200, passed: true },
  { name: 'Checkout Test', duration: 3500, passed: false },
  { name: 'Search Test', duration: 800, passed: true },
  { name: 'Profile Test', duration: 2100, passed: true }
];

// Get average duration of passed tests
const avgDuration = testResults
  .filter(test => test.passed)
  .map(test => test.duration)
  .reduce((sum, duration, _, arr) => sum + duration / arr.length, 0);

console.log(`Average: ${avgDuration}ms`);  // Average: 1366.67ms
```

## Object Methods

### Object.keys() - Get Property Names

```javascript
const user = {
  name: 'Alice',
  age: 30,
  email: 'alice@example.com'
};

const keys = Object.keys(user);
console.log(keys);  // ['name', 'age', 'email']
```

### Object.values() - Get Property Values

```javascript
const user = {
  name: 'Alice',
  age: 30,
  email: 'alice@example.com'
};

const values = Object.values(user);
console.log(values);  // ['Alice', 30, 'alice@example.com']
```

### Object.entries() - Get Key-Value Pairs

```javascript
const user = {
  name: 'Alice',
  age: 30,
  email: 'alice@example.com'
};

const entries = Object.entries(user);
console.log(entries);
// [['name', 'Alice'], ['age', 30], ['email', 'alice@example.com']]

// Useful for iteration
Object.entries(user).forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});
// name: Alice
// age: 30
// email: alice@example.com
```

### Playwright Example: Fill Form from Object

```javascript
async function fillForm(page, formData) {
  for (const [field, value] of Object.entries(formData)) {
    await page.fill(`#${field}`, value.toString());
  }
}

// Usage
await fillForm(page, {
  username: 'testuser',
  email: 'test@example.com',
  age: '25'
});
```

### Object.assign() - Merge Objects

```javascript
const defaults = { theme: 'dark', timeout: 5000 };
const userSettings = { timeout: 10000 };

const config = Object.assign({}, defaults, userSettings);
console.log(config);
// { theme: 'dark', timeout: 10000 }

// Or with spread operator (preferred)
const config2 = { ...defaults, ...userSettings };
```

### Playwright Example: Merge Test Configuration

```javascript
const defaultConfig = {
  headless: true,
  timeout: 30000,
  slowMo: 0
};

const testConfig = {
  timeout: 60000,
  slowMo: 100
};

const finalConfig = { ...defaultConfig, ...testConfig };
console.log(finalConfig);
// { headless: true, timeout: 60000, slowMo: 100 }
```

## Practical Playwright Examples

### Example 1: Extract and Process Product Data

```javascript
async function getExpensiveProducts(page, minPrice) {
  const products = await page.locator('.product-card').all();
  
  const productData = await Promise.all(
    products.map(async product => ({
      name: await product.textContent('.product-name'),
      price: parseFloat(await product.textContent('.price').replace('$', ''))
    }))
  );
  
  return productData
    .filter(product => product.price >= minPrice)
    .sort((a, b) => b.price - a.price);
}

// Usage
const expensive = await getExpensiveProducts(page, 500);
console.log(expensive);
// [{ name: 'Laptop', price: 999 }, { name: 'Tablet', price: 599 }]
```

### Example 2: Validate Form Fields

```javascript
const requiredFields = [
  { id: 'username', label: 'Username' },
  { id: 'email', label: 'Email' },
  { id: 'password', label: 'Password' }
];

async function validateRequiredFields(page) {
  const errors = [];
  
  for (const field of requiredFields) {
    const value = await page.inputValue(`#${field.id}`);
    if (!value || value.trim() === '') {
      errors.push(`${field.label} is required`);
    }
  }
  
  return errors;
}

// Usage
const errors = await validateRequiredFields(page);
if (errors.length > 0) {
  console.log('Validation errors:', errors);
}
```

### Example 3: Check Test Coverage

```javascript
const testCases = [
  { name: 'Login', status: 'passed' },
  { name: 'Logout', status: 'passed' },
  { name: 'Register', status: 'failed' },
  { name: 'Reset Password', status: 'skipped' }
];

const summary = {
  total: testCases.length,
  passed: testCases.filter(t => t.status === 'passed').length,
  failed: testCases.filter(t => t.status === 'failed').length,
  skipped: testCases.filter(t => t.status === 'skipped').length
};

console.log(summary);
// { total: 4, passed: 2, failed: 1, skipped: 1 }
```

## Best Practices

✅ **Use `map()` to transform data** - More readable than loops

✅ **Use `filter()` to subset data** - Cleaner than manual filtering

✅ **Chain methods for complex operations** - filter → map → reduce

✅ **Use `some()` and `every()` for validation** - More expressive than loops

✅ **Prefer immutable methods** - map, filter, slice over sort, splice

✅ **Use spread `...` for copying** - Avoid mutations

## Common Mistakes to Avoid

### ❌ Forgetting to return in callbacks

```javascript
// Wrong
const doubled = numbers.map(num => { num * 2 });  // Returns undefined!

// Correct
const doubled = numbers.map(num => num * 2);
// Or with explicit return
const doubled = numbers.map(num => { return num * 2; });
```

### ❌ Mutating original array

```javascript
// Bad - mutates original
const numbers = [3, 1, 2];
numbers.sort();

// Good - creates copy
const numbers = [3, 1, 2];
const sorted = [...numbers].sort();
```

### ❌ Using forEach when map is better

```javascript
// Bad - manual array building
const doubled = [];
numbers.forEach(num => {
  doubled.push(num * 2);
});

// Good - use map
const doubled = numbers.map(num => num * 2);
```

## Quick Reference

```javascript
// Transform
array.map(item => item * 2)

// Filter
array.filter(item => item > 5)

// Find
array.find(item => item.id === 2)

// Loop
array.forEach(item => console.log(item))

// Reduce
array.reduce((acc, item) => acc + item, 0)

// Check
array.some(item => item > 10)    // Any match?
array.every(item => item > 0)    // All match?

// Sort
array.sort((a, b) => a - b)

// Object keys/values
Object.keys(obj)
Object.values(obj)
Object.entries(obj)
```

## Next Steps

Now that you master array and object methods:
- **Process Playwright element arrays** efficiently
- **Transform test data** with map and filter
- **Validate results** with every and some
- **Build powerful test utilities** with method chaining

These methods make your Playwright test code clean, readable, and powerful! 🚀
