# Error Handling in JavaScript

Errors happen — pages don't load, elements don't appear, network requests fail. Good error handling makes your Playwright tests robust and easier to debug.

## Why Error Handling Matters

| Without Error Handling | With Error Handling |
|------------------------|---------------------|
| Tests crash and stop | Tests recover or log useful info |
| Hard to debug | Clear error messages |
| No cleanup happens | Resources are properly cleaned up |
| Unreliable test results | Stable, predictable tests |

Think of error handling like airbags in a car: you hope you never need them, but they save you when things go wrong.

## Types of Errors

### Syntax Errors
Code that JavaScript can't understand.

```javascript
// Missing closing parenthesis
console.log('Hello';  // SyntaxError

// Wrong syntax
if true {  // SyntaxError - missing parentheses
  console.log('Hi');
}
```

### Runtime Errors
Code runs but fails during execution.

```javascript
// Variable doesn't exist
console.log(nonExistentVariable);  // ReferenceError

// Wrong data type
const num = 42;
num.toUpperCase();  // TypeError: num.toUpperCase is not a function

// Division by zero is NOT an error in JavaScript
console.log(10 / 0);  // Infinity (not an error!)
```

### Logical Errors
Code runs without errors but produces wrong results.

```javascript
// Wrong calculation
function calculateTotal(price, quantity) {
  return price + quantity;  // Should be price * quantity
}

// Bug: 10 + 5 = 15, but we wanted 10 * 5 = 50
```

## Try-Catch-Finally

### Basic Try-Catch

```javascript
try {
  // Code that might fail
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  // Handle the error
  console.error('Something went wrong:', error.message);
}

console.log('Program continues...');
```

### With Finally

```javascript
let file;
try {
  file = openFile('data.txt');
  processFile(file);
} catch (error) {
  console.error('File error:', error.message);
} finally {
  // Always runs - whether error or not
  if (file) {
    closeFile(file);
  }
  console.log('Cleanup complete');
}
```

### Catching Specific Errors

```javascript
try {
  JSON.parse('{invalid json}');
} catch (error) {
  if (error instanceof SyntaxError) {
    console.error('Invalid JSON format');
  } else if (error instanceof TypeError) {
    console.error('Type error occurred');
  } else {
    console.error('Unknown error:', error);
  }
}
```

## Throwing Errors

### Throwing Built-in Errors

```javascript
function divide(a, b) {
  if (b === 0) {
    throw new Error('Cannot divide by zero');
  }
  return a / b;
}

try {
  console.log(divide(10, 0));
} catch (error) {
  console.error(error.message);  // Cannot divide by zero
}
```

### Custom Error Classes

```javascript
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = 'ValidationError';
  }
}

class NetworkError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.name = 'NetworkError';
    this.statusCode = statusCode;
  }
}

function validateEmail(email) {
  if (!email.includes('@')) {
    throw new ValidationError('Invalid email format');
  }
}

try {
  validateEmail('notanemail');
} catch (error) {
  if (error instanceof ValidationError) {
    console.error('Validation failed:', error.message);
  }
}
```

## Error Handling in Playwright

### Example 1: Handling Missing Elements

```javascript
async function clickElement(page, selector) {
  try {
    await page.click(selector, { timeout: 5000 });
    console.log(`Clicked: ${selector}`);
  } catch (error) {
    console.error(`Failed to click ${selector}:`, error.message);
    // Take screenshot for debugging
    await page.screenshot({ path: `error-${Date.now()}.png` });
    throw error; // Re-throw to fail the test
  }
}

// Usage
try {
  await clickElement(page, '#submit-button');
} catch (error) {
  console.log('Test failed, but handled gracefully');
}
```

### Example 2: Retry Logic with Error Handling

```javascript
async function retryOperation(operation, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await operation();
    } catch (error) {
      console.log(`Attempt ${i + 1} failed: ${error.message}`);
      
      if (i === maxRetries - 1) {
        throw new Error(`Failed after ${maxRetries} attempts: ${error.message}`);
      }
      
      // Wait before retrying
      await new Promise(resolve => setTimeout(resolve, 1000));
    }
  }
}

// Usage
try {
  await retryOperation(async () => {
    await page.goto('https://flaky-website.com');
    await page.waitForSelector('.main-content');
  });
} catch (error) {
  console.error('Navigation failed after retries:', error.message);
}
```

### Example 3: Graceful Degradation

```javascript
async function getProductPrice(page) {
  try {
    return await page.textContent('.price');
  } catch (error) {
    console.warn('Price not found, using default');
    return 'N/A';
  }
}

async function scrapeProduct(page) {
  const data = {
    title: 'Unknown',
    price: 'N/A',
    rating: 'N/A'
  };

  try {
    data.title = await page.textContent('.product-title');
  } catch (error) {
    console.warn('Title not found');
  }

  try {
    data.price = await page.textContent('.price');
  } catch (error) {
    console.warn('Price not found');
  }

  try {
    data.rating = await page.textContent('.rating');
  } catch (error) {
    console.warn('Rating not found');
  }

  return data;
}
```

### Example 4: Network Error Handling

```javascript
async function fetchWithRetry(page, url, maxRetries = 3) {
  let lastError;

  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await page.goto(url, {
        waitUntil: 'networkidle',
        timeout: 30000
      });

      if (response.status() >= 400) {
        throw new Error(`HTTP ${response.status()}: ${response.statusText()}`);
      }

      return response;
    } catch (error) {
      lastError = error;
      console.log(`Attempt ${i + 1} failed: ${error.message}`);

      if (i < maxRetries - 1) {
        await page.waitForTimeout(2000 * (i + 1)); // Exponential backoff
      }
    }
  }

  throw new Error(`Failed to load ${url} after ${maxRetries} attempts: ${lastError.message}`);
}

// Usage
try {
  await fetchWithRetry(page, 'https://example.com');
  console.log('Page loaded successfully');
} catch (error) {
  console.error('Navigation failed:', error.message);
  await page.screenshot({ path: 'navigation-error.png' });
}
```

### Example 5: Test Cleanup with Finally

```javascript
async function testLogin(page) {
  let loggedIn = false;

  try {
    // Navigate to login page
    await page.goto('https://example.com/login');

    // Fill credentials
    await page.fill('#username', 'testuser');
    await page.fill('#password', 'password123');
    await page.click('#login-button');

    // Wait for dashboard
    await page.waitForSelector('.dashboard');
    loggedIn = true;

    // Run test assertions
    const welcomeText = await page.textContent('.welcome-message');
    if (!welcomeText.includes('testuser')) {
      throw new Error('Login verification failed');
    }

    console.log('Login test passed');
  } catch (error) {
    console.error('Test failed:', error.message);
    await page.screenshot({ path: 'login-error.png' });
    throw error;
  } finally {
    // Always logout and cleanup
    if (loggedIn) {
      try {
        await page.click('#logout-button');
        console.log('Logged out successfully');
      } catch (logoutError) {
        console.error('Logout failed:', logoutError.message);
      }
    }
  }
}
```

## Error Handling Patterns

### Pattern 1: Error Wrapper

```javascript
class TestError extends Error {
  constructor(message, context = {}) {
    super(message);
    this.name = 'TestError';
    this.context = context;
    this.timestamp = new Date().toISOString();
  }
}

async function safeClick(page, selector, context = {}) {
  try {
    await page.click(selector);
  } catch (error) {
    throw new TestError(`Failed to click element: ${selector}`, {
      ...context,
      selector,
      url: page.url(),
      originalError: error.message
    });
  }
}

// Usage
try {
  await safeClick(page, '#submit', { step: 'form submission' });
} catch (error) {
  console.error(error.message);
  console.error('Context:', error.context);
  console.error('Time:', error.timestamp);
}
```

### Pattern 2: Error Recovery

```javascript
async function fillFormWithFallback(page) {
  try {
    // Try modern selector
    await page.fill('[data-testid="email"]', 'test@example.com');
  } catch (error) {
    console.warn('Modern selector failed, trying legacy selector');
    try {
      // Fallback to old selector
      await page.fill('#email', 'test@example.com');
    } catch (fallbackError) {
      throw new Error('All selectors failed for email field');
    }
  }
}
```

### Pattern 3: Error Aggregation

```javascript
async function validateForm(page) {
  const errors = [];

  // Check all fields
  try {
    await page.waitForSelector('#name', { timeout: 2000 });
  } catch {
    errors.push('Name field not found');
  }

  try {
    await page.waitForSelector('#email', { timeout: 2000 });
  } catch {
    errors.push('Email field not found');
  }

  try {
    await page.waitForSelector('#phone', { timeout: 2000 });
  } catch {
    errors.push('Phone field not found');
  }

  if (errors.length > 0) {
    throw new Error(`Form validation failed:\n${errors.join('\n')}`);
  }
}
```

## Async Error Handling

### Promises with Catch

```javascript
page.goto('https://example.com')
  .then(() => page.click('#button'))
  .then(() => console.log('Success'))
  .catch(error => {
    console.error('Error:', error.message);
  })
  .finally(() => {
    console.log('Operation complete');
  });
```

### Async/Await with Try-Catch (Recommended)

```javascript
async function testFlow() {
  try {
    await page.goto('https://example.com');
    await page.click('#button');
    console.log('Success');
  } catch (error) {
    console.error('Error:', error.message);
  } finally {
    console.log('Operation complete');
  }
}
```

### Promise.allSettled for Multiple Operations

```javascript
async function checkMultipleElements(page, selectors) {
  const results = await Promise.allSettled(
    selectors.map(selector => page.waitForSelector(selector, { timeout: 2000 }))
  );

  const found = [];
  const missing = [];

  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      found.push(selectors[index]);
    } else {
      missing.push(selectors[index]);
    }
  });

  return { found, missing };
}

// Usage
const { found, missing } = await checkMultipleElements(page, [
  '#header',
  '#main-content',
  '#footer',
  '#missing-element'
]);

console.log('Found:', found);
console.log('Missing:', missing);
```

## Best Practices

✅ **Always catch errors in test code** - Prevents cryptic failures

✅ **Provide context in error messages** - Include selector, URL, step

✅ **Take screenshots on errors** - Visual debugging is faster

✅ **Use finally for cleanup** - Always logout, close modals, reset state

✅ **Don't catch and ignore silently** - Log or re-throw

✅ **Create custom error classes** - Makes error handling clearer

✅ **Validate before risky operations** - Check if element exists first

## Common Mistakes to Avoid

### ❌ Swallowing errors

```javascript
// Bad
try {
  await page.click('#button');
} catch (error) {
  // Nothing - error disappears!
}

// Good
try {
  await page.click('#button');
} catch (error) {
  console.error('Click failed:', error.message);
  throw error; // Re-throw to fail test
}
```

### ❌ Not handling async errors

```javascript
// Bad - error won't be caught
try {
  setTimeout(() => {
    throw new Error('Async error');
  }, 1000);
} catch (error) {
  console.error('This won\'t run');
}

// Good
async function asyncOperation() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject(new Error('Async error'));
    }, 1000);
  });
}

try {
  await asyncOperation();
} catch (error) {
  console.error('Caught:', error.message);
}
```

### ❌ Generic error messages

```javascript
// Bad
throw new Error('Failed');

// Good
throw new Error(`Failed to click element '#submit' on page ${page.url()}`);
```

## Quick Reference

```javascript
// Basic try-catch
try {
  riskyOperation();
} catch (error) {
  console.error(error.message);
}

// With finally
try {
  operation();
} catch (error) {
  handleError(error);
} finally {
  cleanup();
}

// Throw error
throw new Error('Something failed');

// Custom error
class MyError extends Error {
  constructor(message) {
    super(message);
    this.name = 'MyError';
  }
}

// Async error handling
try {
  await asyncOperation();
} catch (error) {
  handleError(error);
}
```

## Next Steps

Now that you understand error handling:
- **Wrap risky Playwright operations** in try-catch
- **Create custom error classes** for your test framework
- **Add retry logic** for flaky operations
- **Take screenshots** on test failures
- **Always clean up** in finally blocks

Robust error handling makes your Playwright tests reliable and easier to debug! 🛡️
