# Playwright Parallel Execution

Playwright can run tests in parallel to speed up your test suite.

## How Parallelism Works
- Each test file runs in a separate worker process.
- Tests within a file can run in parallel if you use `test.describe.parallel`.

## Enable Parallel Execution
- By default, Playwright runs test files in parallel.
- Control the number of workers in `playwright.config.js`:

```javascript
// playwright.config.js
module.exports = {
  workers: 4, // Number of parallel workers
};
```

## Example: Parallel Test Suite
```javascript
const { test, expect } = require('@playwright/test');

test.describe.parallel('Parallel Suite', () => {
  test('test 1', async ({ page }) => {
    await page.goto('https://example.com/1');
    await expect(page).toHaveTitle(/Example/);
  });
  test('test 2', async ({ page }) => {
    await page.goto('https://example.com/2');
    await expect(page).toHaveTitle(/Example/);
  });
});
```

## Run Tests in Parallel
```bash
npx playwright test
```

## Best Practices
- Avoid shared state between tests
- Use unique test data per test
- Use fixtures for isolation
