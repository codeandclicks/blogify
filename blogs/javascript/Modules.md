# JavaScript Modules

Modules help you organize code into separate, reusable files. Instead of writing everything in one massive file, modules let you split code into logical pieces and import only what you need.

## Why Use Modules?

| Benefit | Description |
|---------|-------------|
| **Organization** | Keep related code together in separate files |
| **Reusability** | Use the same code across multiple files |
| **Maintainability** | Easier to find and fix bugs |
| **Namespace** | Avoid variable name conflicts |
| **Lazy Loading** | Load code only when needed |

Think of modules like a toolbox: instead of carrying all tools everywhere, you grab only what you need for each job.

## Export: Sharing Code

### Named Exports

Export multiple values from a module.

```javascript
// utils.js
export const API_URL = 'https://api.example.com';
export const TIMEOUT = 5000;

export function add(a, b) {
  return a + b;
}

export function multiply(a, b) {
  return a * b;
}

export class Calculator {
  constructor() {
    this.result = 0;
  }
}
```

### Default Export

Export one main value from a module.

```javascript
// logger.js
export default function log(message) {
  console.log(`[LOG] ${message}`);
}

// Or with class
export default class Logger {
  log(message) {
    console.log(`[LOG] ${message}`);
  }
}
```

### Mixing Named and Default Exports

```javascript
// config.js
export const environment = 'production';
export const debug = false;

export default {
  apiUrl: 'https://api.example.com',
  timeout: 5000
};
```

## Import: Using Code

### Importing Named Exports

```javascript
// Import specific items
import { add, multiply } from './utils.js';

console.log(add(2, 3));      // 5
console.log(multiply(4, 5)); // 20

// Import with alias
import { add as addition } from './utils.js';
console.log(addition(2, 3)); // 5

// Import all as namespace
import * as Utils from './utils.js';
console.log(Utils.add(2, 3));      // 5
console.log(Utils.TIMEOUT);        // 5000
```

### Importing Default Exports

```javascript
// Import default (can use any name)
import log from './logger.js';
log('Hello World');

import Logger from './logger.js';
const logger = new Logger();
logger.log('Hello');
```

### Mixing Imports

```javascript
// Import both default and named
import config, { environment, debug } from './config.js';

console.log(config.apiUrl);  // https://api.example.com
console.log(environment);    // production
```

## Module Patterns for Playwright

### Pattern 1: Page Object Module

```javascript
// pages/loginPage.js
export class LoginPage {
  constructor(page) {
    this.page = page;
    this.usernameInput = '#username';
    this.passwordInput = '#password';
    this.loginButton = '#login-btn';
  }

  async navigate() {
    await this.page.goto('https://example.com/login');
  }

  async login(username, password) {
    await this.page.fill(this.usernameInput, username);
    await this.page.fill(this.passwordInput, password);
    await this.page.click(this.loginButton);
  }

  async getErrorMessage() {
    return await this.page.textContent('.error-message');
  }
}

export const LOGIN_URL = 'https://example.com/login';
```

**Usage:**

```javascript
// tests/login.test.js
import { LoginPage, LOGIN_URL } from '../pages/loginPage.js';

async function testLogin(page) {
  const loginPage = new LoginPage(page);
  await loginPage.navigate();
  await loginPage.login('testuser', 'password123');
}
```

### Pattern 2: Helper Functions Module

```javascript
// helpers/waitHelpers.js
export async function waitForElement(page, selector, timeout = 5000) {
  try {
    await page.waitForSelector(selector, { timeout });
    return true;
  } catch {
    return false;
  }
}

export async function waitForText(page, text, timeout = 5000) {
  try {
    await page.waitForFunction(
      text => document.body.textContent.includes(text),
      text,
      { timeout }
    );
    return true;
  } catch {
    return false;
  }
}

export async function waitForUrl(page, urlPattern, timeout = 5000) {
  try {
    await page.waitForURL(urlPattern, { timeout });
    return true;
  } catch {
    return false;
  }
}
```

**Usage:**

```javascript
// tests/checkout.test.js
import { waitForElement, waitForText } from '../helpers/waitHelpers.js';

async function testCheckout(page) {
  await page.click('#checkout-btn');
  
  const loaded = await waitForText(page, 'Order Summary');
  if (loaded) {
    console.log('Checkout page loaded');
  }
}
```

### Pattern 3: Configuration Module

```javascript
// config/testConfig.js
export const config = {
  baseUrl: 'https://example.com',
  timeout: 30000,
  headless: true,
  slowMo: 0
};

export const users = {
  admin: {
    username: 'admin@example.com',
    password: 'Admin@123'
  },
  regular: {
    username: 'user@example.com',
    password: 'User@123'
  }
};

export const selectors = {
  login: {
    username: '#username',
    password: '#password',
    submitButton: 'button[type="submit"]'
  },
  dashboard: {
    welcomeMessage: '.welcome-message',
    logoutButton: '#logout'
  }
};

export default config;
```

**Usage:**

```javascript
// tests/admin.test.js
import config, { users, selectors } from '../config/testConfig.js';

async function testAdminLogin(page) {
  await page.goto(config.baseUrl);
  await page.fill(selectors.login.username, users.admin.username);
  await page.fill(selectors.login.password, users.admin.password);
  await page.click(selectors.login.submitButton);
}
```

### Pattern 4: Test Data Module

```javascript
// data/testData.js
export const validUsers = [
  { name: 'John Doe', email: 'john@example.com', age: 30 },
  { name: 'Jane Smith', email: 'jane@example.com', age: 25 }
];

export const invalidEmails = [
  'notanemail',
  '@example.com',
  'test@',
  'test..test@example.com'
];

export function generateUser(overrides = {}) {
  return {
    name: 'Test User',
    email: `test${Date.now()}@example.com`,
    age: 25,
    ...overrides
  };
}

export function generateRandomString(length = 10) {
  return Math.random().toString(36).substring(2, length + 2);
}
```

**Usage:**

```javascript
// tests/registration.test.js
import { generateUser, invalidEmails } from '../data/testData.js';

async function testRegistration(page) {
  const user = generateUser({ age: 35 });
  
  await page.fill('#name', user.name);
  await page.fill('#email', user.email);
  await page.fill('#age', user.age.toString());
}

async function testInvalidEmails(page) {
  for (const email of invalidEmails) {
    await page.fill('#email', email);
    await page.click('#submit');
    // Verify error message appears
  }
}
```

### Pattern 5: Assertion Helpers Module

```javascript
// helpers/assertions.js
export async function assertElementVisible(page, selector) {
  const isVisible = await page.isVisible(selector);
  if (!isVisible) {
    throw new Error(`Element ${selector} is not visible`);
  }
}

export async function assertTextEquals(page, selector, expectedText) {
  const actualText = await page.textContent(selector);
  if (actualText !== expectedText) {
    throw new Error(`Expected "${expectedText}", got "${actualText}"`);
  }
}

export async function assertUrlContains(page, urlPart) {
  const currentUrl = page.url();
  if (!currentUrl.includes(urlPart)) {
    throw new Error(`URL ${currentUrl} does not contain ${urlPart}`);
  }
}

export async function assertElementCount(page, selector, expectedCount) {
  const count = await page.locator(selector).count();
  if (count !== expectedCount) {
    throw new Error(`Expected ${expectedCount} elements, found ${count}`);
  }
}
```

**Usage:**

```javascript
// tests/search.test.js
import { 
  assertElementVisible, 
  assertTextEquals,
  assertElementCount 
} from '../helpers/assertions.js';

async function testSearch(page) {
  await page.fill('#search', 'laptop');
  await page.click('#search-button');
  
  await assertElementVisible(page, '.search-results');
  await assertTextEquals(page, '.results-count', '10 results');
  await assertElementCount(page, '.product-card', 10);
}
```

## Project Structure Example

```
playwright-tests/
├── tests/
│   ├── login.test.js
│   ├── checkout.test.js
│   └── search.test.js
├── pages/
│   ├── loginPage.js
│   ├── homePage.js
│   └── checkoutPage.js
├── helpers/
│   ├── waitHelpers.js
│   ├── assertions.js
│   └── dataHelpers.js
├── data/
│   ├── testData.js
│   └── credentials.js
├── config/
│   └── testConfig.js
└── package.json
```

## ES6 vs CommonJS

### ES6 Modules (Modern - Recommended)

```javascript
// Export
export const value = 42;
export default function() {}

// Import
import myFunction, { value } from './module.js';
```

### CommonJS (Node.js Legacy)

```javascript
// Export
module.exports = { value: 42 };
module.exports.value = 42;

// Import
const { value } = require('./module.js');
```

**For Playwright:** Use ES6 modules with `.js` extension or configure package.json:

```json
{
  "type": "module"
}
```

## Best Practices

✅ **One file per module** - Keep modules focused and small

✅ **Use named exports for utilities** - Makes it clear what you're importing

✅ **Use default export for main class** - Page objects, test suites

✅ **Group related functions** - Put all wait helpers in one module

✅ **Use descriptive filenames** - `loginPage.js`, not `page1.js`

✅ **Avoid circular dependencies** - Module A imports B, B imports A = ❌

✅ **Keep imports at the top** - Makes dependencies clear

## Common Mistakes to Avoid

### ❌ Missing file extension

```javascript
// Wrong
import { helper } from './helpers/utils'; // May not work

// Correct
import { helper } from './helpers/utils.js';
```

### ❌ Mixing default and named exports confusingly

```javascript
// Confusing
export default class Page {}
export const helper = () => {};

// Better - be consistent
export class Page {}
export const helper = () => {};
```

### ❌ Exporting too much

```javascript
// Bad - exposing internals
export function internalHelper() {}
export function publicHelper() {}

// Good - only export what's needed
function internalHelper() {}
export function publicHelper() {}
```

## Quick Reference

```javascript
// Named exports
export const value = 42;
export function func() {}
export class MyClass {}

// Default export
export default function() {}

// Import named
import { value, func } from './module.js';

// Import default
import myFunc from './module.js';

// Import all
import * as Module from './module.js';

// Import with alias
import { value as myValue } from './module.js';

// Mix
import defaultExport, { named1, named2 } from './module.js';
```

## Next Steps

Now that you understand modules:
- **Organize your Playwright tests** into page objects and helpers
- **Create reusable utilities** for common operations
- **Share configuration** across multiple test files
- **Build a maintainable test framework**

Clean module organization makes your Playwright tests easier to write, read, and maintain! 📦
