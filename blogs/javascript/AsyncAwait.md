# Promises and Async/Await

Asynchronous programming is the foundation of Playwright automation. Understanding promises and async/await is **critical** for writing reliable, maintainable test scripts.

## What is Asynchronous Programming?

Think of asynchronous code like ordering food at a restaurant:
- **Synchronous (blocking):** You wait at the counter until your food is ready, doing nothing else
- **Asynchronous (non-blocking):** You order, get a number, and can do other things while waiting

```javascript
// Synchronous - blocks execution
console.log("Start");
// Imagine waiting 5 seconds here...
console.log("End");

// Asynchronous - doesn't block
console.log("Start");
setTimeout(() => console.log("Middle"), 2000);
console.log("End");
// Output: Start, End, Middle (after 2 seconds)
```

## Promises

A **Promise** is an object representing the eventual completion or failure of an asynchronous operation.

### Promise States

| State | Description |
|-------|-------------|
| **Pending** | Initial state, neither fulfilled nor rejected |
| **Fulfilled** | Operation completed successfully |
| **Rejected** | Operation failed |

### Creating a Promise

```javascript
const myPromise = new Promise((resolve, reject) => {
  const success = true;
  
  if (success) {
    resolve("Operation successful!"); // Fulfilled
  } else {
    reject("Operation failed!"); // Rejected
  }
});
```

### Using Promises with .then() and .catch()

```javascript
myPromise
  .then(result => {
    console.log(result); // "Operation successful!"
    return "Next step";
  })
  .then(nextResult => {
    console.log(nextResult); // "Next step"
  })
  .catch(error => {
    console.error(error); // Handles any rejection
  })
  .finally(() => {
    console.log("Always executes");
  });
```

### Real-World Promise Example

```javascript
function fetchUserData(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (userId) {
        resolve({ id: userId, name: "Alice" });
      } else {
        reject("User ID is required");
      }
    }, 1000);
  });
}

fetchUserData(123)
  .then(user => console.log(user))
  .catch(error => console.error(error));
```

## Async/Await

**Async/await** is syntactic sugar over promises, making asynchronous code look and behave more like synchronous code.

### Basic Syntax

```javascript
async function myFunction() {
  const result = await someAsyncOperation();
  return result;
}
```

**Rules:**
- `async` keyword before function makes it return a Promise
- `await` can only be used inside `async` functions
- `await` pauses execution until the Promise resolves

### Converting Promises to Async/Await

```javascript
// Using Promises
function getUserData() {
  return fetchUserData(123)
    .then(user => {
      console.log(user);
      return user;
    })
    .catch(error => {
      console.error(error);
    });
}

// Using Async/Await (much cleaner!)
async function getUserData() {
  try {
    const user = await fetchUserData(123);
    console.log(user);
    return user;
  } catch (error) {
    console.error(error);
  }
}
```

### Multiple Async Operations

**Sequential (one after another):**

```javascript
async function sequential() {
  const user = await fetchUser();        // Wait 1 second
  const posts = await fetchPosts(user);  // Wait 1 second
  const comments = await fetchComments(); // Wait 1 second
  // Total: 3 seconds
  return { user, posts, comments };
}
```

**Parallel (all at once):**

```javascript
async function parallel() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(),        // Start all three
    fetchPosts(),       // at the same time
    fetchComments()
  ]);
  // Total: 1 second (whichever takes longest)
  return { user, posts, comments };
}
```

### Promise.all vs Promise.race

```javascript
// Promise.all - waits for ALL promises
async function loadAll() {
  const results = await Promise.all([
    fetch('/api/users'),
    fetch('/api/posts'),
    fetch('/api/comments')
  ]);
  // All must succeed, or one rejection fails all
  return results;
}

// Promise.race - returns when FIRST promise settles
async function loadFirst() {
  const result = await Promise.race([
    fetch('/api/fast'),
    fetch('/api/slow')
  ]);
  // Returns whichever completes first
  return result;
}
```

## Error Handling with Async/Await

### Try/Catch

```javascript
async function handleErrors() {
  try {
    const data = await riskyOperation();
    console.log(data);
  } catch (error) {
    console.error("Something went wrong:", error.message);
  } finally {
    console.log("Cleanup code here");
  }
}
```

### Handling Multiple Errors

```javascript
async function multipleOperations() {
  try {
    const user = await fetchUser();
    console.log("User:", user);
  } catch (error) {
    console.error("Failed to fetch user:", error);
    return; // Exit early
  }
  
  try {
    const posts = await fetchPosts();
    console.log("Posts:", posts);
  } catch (error) {
    console.error("Failed to fetch posts:", error);
    // Continue despite error
  }
}
```

## Playwright Examples

### Example 1: Basic Page Navigation

```javascript
const { chromium } = require('playwright');

async function navigateToPage() {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  
  try {
    await page.goto('https://example.com');
    const title = await page.title();
    console.log('Page title:', title);
  } catch (error) {
    console.error('Navigation failed:', error);
  } finally {
    await browser.close();
  }
}

navigateToPage();
```

### Example 2: Waiting for Elements

```javascript
async function waitForLogin(page) {
  try {
    // Wait for button to appear (returns Promise)
    await page.waitForSelector('#login-button', { timeout: 5000 });
    
    // Click button (returns Promise)
    await page.click('#login-button');
    
    // Wait for navigation (returns Promise)
    await page.waitForURL('**/dashboard');
    
    console.log('Login successful!');
  } catch (error) {
    console.error('Login failed:', error.message);
    throw error; // Re-throw to handle upstream
  }
}
```

### Example 3: Parallel Actions

```javascript
async function fillFormFast(page) {
  // Fill multiple fields in parallel
  await Promise.all([
    page.fill('#firstName', 'John'),
    page.fill('#lastName', 'Doe'),
    page.fill('#email', 'john@example.com'),
    page.fill('#phone', '555-0100')
  ]);
  // All fields filled simultaneously!
  
  await page.click('#submit');
}
```

### Example 4: Retry Logic with Async/Await

```javascript
async function retryOperation(operation, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      const result = await operation();
      return result; // Success!
    } catch (error) {
      console.log(`Attempt ${i + 1} failed:`, error.message);
      
      if (i === maxRetries - 1) {
        throw error; // Last attempt failed
      }
      
      // Wait before retry
      await new Promise(resolve => setTimeout(resolve, 1000));
    }
  }
}

// Usage in Playwright
async function clickWithRetry(page, selector) {
  return retryOperation(async () => {
    await page.click(selector);
  });
}
```

### Example 5: Sequential Test Steps

```javascript
async function runTest() {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  
  try {
    // Each step waits for previous to complete
    await page.goto('https://example.com');
    console.log('✓ Navigated to site');
    
    await page.fill('#username', 'testuser');
    console.log('✓ Entered username');
    
    await page.fill('#password', 'password123');
    console.log('✓ Entered password');
    
    await page.click('#login');
    console.log('✓ Clicked login');
    
    await page.waitForSelector('.dashboard');
    console.log('✓ Dashboard loaded');
    
  } catch (error) {
    console.error('Test failed:', error);
  } finally {
    await browser.close();
  }
}
```

## Common Mistakes to Avoid

### ❌ Forgetting await

```javascript
// Wrong - doesn't wait!
async function wrong() {
  const page = browser.newPage(); // Missing await!
  page.goto('https://example.com'); // Error: page is a Promise
}

// Correct
async function correct() {
  const page = await browser.newPage();
  await page.goto('https://example.com');
}
```

### ❌ Using await in non-async function

```javascript
// Wrong - syntax error!
function wrong() {
  await page.click('#button'); // Error: await only in async
}

// Correct
async function correct() {
  await page.click('#button');
}
```

### ❌ Not handling errors

```javascript
// Wrong - unhandled rejection crashes
async function risky() {
  await mightFail(); // If this fails, program crashes
}

// Correct
async function safe() {
  try {
    await mightFail();
  } catch (error) {
    console.error('Handled:', error);
  }
}
```

## Best Practices

✅ **Always use async/await in Playwright** - All Playwright actions return Promises

✅ **Use try/catch for error handling** - Don't let unhandled rejections crash your tests

✅ **Use Promise.all for parallel operations** - Speeds up tests significantly

✅ **Add timeouts for reliability** - Don't wait forever for elements

✅ **Use meaningful variable names** - Makes async code easier to follow

## Quick Reference

```javascript
// Basic async function
async function example() {
  const result = await someAsyncOperation();
  return result;
}

// Error handling
async function safe() {
  try {
    await riskyOperation();
  } catch (error) {
    console.error(error);
  }
}

// Parallel execution
const [a, b, c] = await Promise.all([opA(), opB(), opC()]);

// Sequential execution
const a = await opA();
const b = await opB();
const c = await opC();

// Timeout
await Promise.race([
  operation(),
  new Promise((_, reject) => 
    setTimeout(() => reject('Timeout'), 5000)
  )
]);
```

## Next Steps

Now that you understand async/await, you're ready to:
- **Write Playwright tests** with proper asynchronous handling
- **Handle API calls** in your test scripts
- **Implement retry logic** for flaky operations
- **Optimize test performance** with parallel execution

Master async/await and you'll write better, more reliable Playwright automation! 🎭
