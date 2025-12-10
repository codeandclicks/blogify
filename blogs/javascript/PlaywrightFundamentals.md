# Playwright Fundamentals

Connect your JavaScript and Node.js knowledge to real browser automation! This guide covers the essentials for getting started with Playwright.

---

## 1. Installation and Setup

Install Playwright Test (includes browsers and test runner):

```bash
npm install --save-dev @playwright/test
```

- This installs Playwright and its test runner.
- The first time you run Playwright, it will download Chromium, Firefox, and WebKit.

---

## 2. Launching Browsers

Playwright supports three browsers out of the box:
- **Chromium** (Chrome, Edge)
- **Firefox**
- **WebKit** (Safari)

### Example: Launch a Browser

```javascript
const { chromium, firefox, webkit } = require('playwright');

(async () => {
  const browser = await chromium.launch(); // or firefox.launch(), webkit.launch()
  const page = await browser.newPage();
  await page.goto('https://example.com');
  await browser.close();
})();
```

### With Playwright Test Runner

```javascript
// tests/example.spec.js
const { test, expect } = require('@playwright/test');

test('basic test', async ({ page }) => {
  await page.goto('https://example.com');
  await expect(page).toHaveTitle(/Example/);
});
```

---

## 3. Selectors

Playwright supports multiple selector types:
- **CSS selectors**: `.btn`, `#id`, `div > span`
- **Text selectors**: `text=Login`, `text="Sign up"`
- **XPath selectors**: `xpath=//button[@type='submit']`
- **Playwright locators**: `page.getByRole('button', { name: 'Submit' })`, `page.getByTestId('login-btn')`

### Examples

```javascript
await page.click('button#submit');           // CSS
await page.click('text=Sign In');            // Text
await page.click('xpath=//a[@href="/home"]'); // XPath
await page.getByRole('button', { name: 'Login' }).click(); // Playwright locator
```

---

## 4. Page Interactions

Interact with web pages just like a user:

| Action      | Example Code |
|-------------|-------------|
| Click       | `await page.click('#login')` |
| Fill Input  | `await page.fill('#username', 'user')` |
| Type Text   | `await page.type('#search', 'laptop')` |
| Hover       | `await page.hover('.menu')` |
| Wait For    | `await page.waitForSelector('.loaded')` |
| Select      | `await page.selectOption('#country', 'IN')` |
| Check/Uncheck | `await page.check('#agree')` |

### Example: Login Flow

```javascript
await page.goto('https://example.com/login');
await page.fill('#username', 'testuser');
await page.fill('#password', 'password123');
await page.click('button[type="submit"]');
await page.waitForSelector('.dashboard');
```

---

## 5. Assertions (Validation)

Use Playwright Test's built-in assertions for robust validation.

### Common Assertions

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

---

## 6. Screenshots and Tracing

### Screenshots

- Capture screenshots for debugging or reporting:

```javascript
await page.screenshot({ path: 'screenshot.png' });
```

- Full page screenshot:

```javascript
await page.screenshot({ path: 'full.png', fullPage: true });
```

### Tracing

- Record a trace for debugging test failures:

```javascript
const { test } = require('@playwright/test');

test.use({ trace: 'on' });

test('my test', async ({ page }) => {
  // ... test steps ...
});
```

- After a test run, view the trace:

```bash
npx playwright show-trace trace.zip
```

---

## 7. Typical Playwright Test Structure

```javascript
const { test, expect } = require('@playwright/test');

test.describe('Login Suite', () => {
  test('valid login', async ({ page }) => {
    await page.goto('https://example.com/login');
    await page.fill('#username', 'user');
    await page.fill('#password', 'pass');
    await page.click('button[type="submit"]');
    await expect(page).toHaveURL(/dashboard/);
  });
});
```

---

## 8. Best Practices

✅ Use Playwright Test runner for structure and assertions

✅ Prefer Playwright locators for robust selectors

✅ Use `await` for all async actions

✅ Take screenshots on failure for debugging

✅ Use tracing for hard-to-reproduce bugs

✅ Organize tests in suites and use `beforeEach` for setup

---

## 9. Common Mistakes

❌ Forgetting to `await` Playwright actions

❌ Using brittle selectors (e.g., absolute XPaths)

❌ Not cleaning up after tests (e.g., closing browsers)

❌ Ignoring test runner features (use Playwright Test for best experience)

---

## Quick Reference

```bash
# Install Playwright Test
npm install --save-dev @playwright/test

# Run all tests
npx playwright test

# Show trace after failure
npx playwright show-trace trace.zip
```

---

Playwright makes browser automation fast, reliable, and fun. Master these fundamentals to build powerful end-to-end tests! 🧑‍💻
