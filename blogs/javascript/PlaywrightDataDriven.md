# Playwright Data-driven Testing

Data-driven testing lets you run the same test with different data sets.

## Parameterized Tests

Use `test.describe` and `forEach` to run tests with multiple data sets.

### Example: Table-driven Test
```javascript
const { test, expect } = require('@playwright/test');

const users = [
  { username: 'user1', password: 'pass1' },
  { username: 'user2', password: 'pass2' },
];

users.forEach(({ username, password }) => {
  test(`login as ${username}`, async ({ page }) => {
    await page.goto('https://example.com/login');
    await page.fill('#username', username);
    await page.fill('#password', password);
    await page.click('button[type="submit"]');
    await expect(page).toHaveURL(/dashboard/);
  });
});
```

## Using test.step for Data

```javascript
test('search products', async ({ page }) => {
  const products = ['laptop', 'phone', 'tablet'];
  for (const product of products) {
    await test.step(`search for ${product}`, async () => {
      await page.fill('#search', product);
      await page.click('#search-btn');
      await expect(page.locator('.results')).toBeVisible();
    });
  }
});
```

## External Data Files

You can load data from JSON or CSV files:

```javascript
const data = require('./testdata.json');

data.forEach(({ input, expected }) => {
  test(`input: ${input}`, async ({ page }) => {
    // ...
  });
});
```

## Best Practices
- Keep test data separate from test logic
- Use parameterized tests for coverage
- Validate all data-driven scenarios
