# Playwright Page Interactions

Interact with web pages just like a user!

## Common Actions

| Action      | Example Code |
|-------------|-------------|
| Click       | `await page.click('#login')` |
| Fill Input  | `await page.fill('#username', 'user')` |
| Type Text   | `await page.type('#search', 'laptop')` |
| Hover       | `await page.hover('.menu')` |
| Wait For    | `await page.waitForSelector('.loaded')` |
| Select      | `await page.selectOption('#country', 'IN')` |
| Check/Uncheck | `await page.check('#agree')` |

## Example: Login Flow

```javascript
await page.goto('https://example.com/login');
await page.fill('#username', 'testuser');
await page.fill('#password', 'password123');
await page.click('button[type="submit"]');
await page.waitForSelector('.dashboard');
```

## Example: Dropdown and Checkbox

```javascript
await page.selectOption('#country', 'US');
await page.check('#terms');
```

## Example: Hover and Type

```javascript
await page.hover('.menu-item');
await page.type('#search', 'Playwright');
```

## Best Practices
- Always use `await` for async actions
- Wait for elements before interacting
- Use robust selectors
