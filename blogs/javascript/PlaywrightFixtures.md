# Playwright Fixtures & Hooks

Fixtures and hooks help you manage setup and teardown logic for your tests.

## What are Fixtures?
- Fixtures provide reusable test context (e.g., browser, page, test data).
- Playwright Test has built-in fixtures like `page`, `browser`, `context`.
- You can define custom fixtures for things like login, test data, etc.

### Example: Using Built-in Fixtures
```javascript
const { test, expect } = require('@playwright/test');

test('uses page fixture', async ({ page }) => {
  await page.goto('https://example.com');
  await expect(page).toHaveTitle(/Example/);
});
```

### Example: Custom Fixture
```javascript
const { test as base, expect } = require('@playwright/test');

const test = base.extend({
  loginUser: async ({ page }, use) => {
    await page.goto('https://example.com/login');
    await page.fill('#username', 'user');
    await page.fill('#password', 'pass');
    await page.click('button[type="submit"]');
    await use(page);
  }
});

test('dashboard loads', async ({ loginUser }) => {
  await loginUser.waitForSelector('.dashboard');
});
```

## Hooks
- Hooks run before/after tests or suites.
- Use for setup, teardown, cleanup, etc.

### Example: beforeEach/afterEach
```javascript
const { test } = require('@playwright/test');

test.beforeEach(async ({ page }) => {
  await page.goto('https://example.com');
});

test.afterEach(async ({ page }) => {
  // Cleanup logic
});
```

### Example: beforeAll/afterAll
```javascript
test.beforeAll(async () => {
  // Global setup
});

test.afterAll(async () => {
  // Global teardown
});
```

## Best Practices
- Use fixtures for reusable logic
- Use hooks for setup/cleanup
- Keep tests isolated and stateless
