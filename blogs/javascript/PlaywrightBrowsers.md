# Launching Browsers with Playwright

Playwright supports three browsers out of the box:
- **Chromium** (Chrome, Edge)
- **Firefox**
- **WebKit** (Safari)

## Launching a Browser (Standalone)

```javascript
const { chromium, firefox, webkit } = require('playwright');

(async () => {
  const browser = await chromium.launch(); // or firefox.launch(), webkit.launch()
  const page = await browser.newPage();
  await page.goto('https://example.com');
  await browser.close();
})();
```

## Launching with Playwright Test Runner

```javascript
// tests/example.spec.js
const { test, expect } = require('@playwright/test');

test('open example.com', async ({ page }) => {
  await page.goto('https://example.com');
  await expect(page).toHaveTitle(/Example/);
});
```

## Headless vs Headed Mode

- **Headless (default):** Runs browser in the background (no UI)
- **Headed:** Shows the browser window

```javascript
const browser = await chromium.launch({ headless: false });
```

## Example: Launch All Browsers

```javascript
const { chromium, firefox, webkit } = require('playwright');

(async () => {
  for (const browserType of [chromium, firefox, webkit]) {
    const browser = await browserType.launch();
    const page = await browser.newPage();
    await page.goto('https://example.com');
    console.log(await page.title());
    await browser.close();
  }
})();
```
