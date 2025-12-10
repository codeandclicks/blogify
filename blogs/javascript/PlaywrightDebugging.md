# Playwright Screenshots & Tracing

Playwright makes debugging and reporting easy with screenshots and tracing.

## Screenshots

- Capture screenshots for debugging or reporting:

```javascript
await page.screenshot({ path: 'screenshot.png' });
```

- Full page screenshot:

```javascript
await page.screenshot({ path: 'full.png', fullPage: true });
```

## Example: On Test Failure

```javascript
try {
  await page.click('#submit');
} catch (error) {
  await page.screenshot({ path: 'error.png' });
  throw error;
}
```

## Tracing

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

## Best Practices
- Take screenshots on failure for debugging
- Use tracing for hard-to-reproduce bugs
- Store artifacts for reporting
