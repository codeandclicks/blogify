# Playwright Selectors

Selectors help you find and interact with elements on the page. Playwright supports several types:

## 1. CSS Selectors

```javascript
await page.click('button#submit');
await page.fill('input[name="email"]', 'test@example.com');
```

## 2. Text Selectors

```javascript
await page.click('text=Sign In');
await page.click('text="Forgot password?"');
```

## 3. XPath Selectors

```javascript
await page.click('xpath=//button[@type="submit"]');
```

## 4. Playwright Locators (Recommended)

- More robust and readable.

```javascript
await page.getByRole('button', { name: 'Login' }).click();
await page.getByTestId('login-btn').click();
```

## Example: Multiple Selectors

```javascript
// CSS
await page.click('.btn-primary');

// Text
await page.click('text=Continue');

// XPath
await page.click('xpath=//a[@href="/home"]');

// Playwright locator
await page.getByRole('link', { name: 'Home' }).click();
```

## Best Practices
- Prefer Playwright locators for stability
- Avoid brittle selectors (e.g., absolute XPath)
- Use data-testid attributes for automation
