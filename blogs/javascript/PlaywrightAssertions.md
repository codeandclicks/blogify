# Playwright Assertions

Assertions validate that your application behaves as expected. Playwright Test provides powerful built-in assertions.

## Common Assertions

```javascript
const { test, expect } = require('@playwright/test');

test('login success', async ({ page }) => {
  await page.goto('https://example.com/login');
  await page.fill('#username', 'user');
  await page.fill('#password', 'pass');
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL(/dashboard/);
  await expect(page.locator('.welcome')).toHaveText(/Welcome/);
  await expect(page.locator('.profile-pic')).toBeVisible();
});
```

## Example: Text and Visibility

```javascript
await expect(page.locator('h1')).toHaveText('Dashboard');
await expect(page.locator('.alert')).toBeVisible();
```

## Example: URL and Title

```javascript
await expect(page).toHaveURL(/dashboard/);
await expect(page).toHaveTitle(/Example/);
```

## Best Practices
- Use Playwright Test's expect for all validations
- Prefer specific assertions (e.g., toHaveText, toBeVisible)
- Add assertions after every important step
