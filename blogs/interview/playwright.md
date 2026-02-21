# Playwright Interview Guide

> Mastering browser automation, testing, and best practices with Playwright

---

## Overview

### What is Playwright?
Playwright is an open‑source end‑to‑end browser automation and testing library for Chromium, WebKit, and Firefox. It offers:
- Single, consistent API across languages (JS/TS, Python, Java, .NET)
- Built‑in auto‑waiting for actions
- Network interception and mocking
- Mobile emulation
- Parallel test execution

---

## Playwright vs Selenium

| Attribute            | Playwright                                 | Selenium                        |
|----------------------|--------------------------------------------|----------------------------------|
| Browser engines      | Chromium, WebKit, Firefox                  | Browser drivers via WebDriver    |
| Auto‑waiting         | Built‑in auto‑wait for actions              | Manual waits often required      |
| Network interception | Native request/response interception/mocking| Limited; needs proxy tooling     |
| Parallelism          | Lightweight browser contexts, test workers  | Parallel via grid/external runners|
| Mobile emulation     | Built‑in device descriptors                 | Limited; relies on driver/device |
| Language bindings    | JS/TS, Python, Java, .NET                  | Many languages via WebDriver     |

**Key advantages:**
- Auto‑wait and resilient actions reduce flakiness
- Single API across engines (including WebKit for Safari)
- Built‑in network interception and mocking

---

## Architecture

**Core components:**
- Client bindings: language SDKs (Node, Python, Java, .NET)
- Driver/server: automation driver communicating with browser engines
- Browser engines: Chromium, WebKit, Firefox
- Browser contexts: lightweight, isolated contexts for parallelism/session isolation

---

## Selectors and Element Targeting

**Supported selector types:**
- CSS selectors: `page.locator('button.submit')`
- Text selectors: `page.getByText('Sign in')`
- Role selectors: `page.getByRole('button', { name: 'Add to cart' })` (accessibility‑first)
- XPath: `page.locator('//div[@id="x"]')`
- Data/test attributes: `[data-test="checkout"]` (recommended for stability)
- Chained locators/filters: `page.locator('ul').locator('li').first()`

**Best practice for ecommerce:**
Prefer role, text, and data‑test selectors for product lists, cart actions, and checkout flows to reduce brittleness.

---

## Waiting Strategies & Async/Await

**Wait types and handling:**
- Auto‑wait: Playwright auto‑waits for elements to be actionable
- Explicit waits: `await page.waitForSelector(selector, { state: 'visible' })`
- Load state waits: `await page.waitForLoadState('networkidle')`
- Network waits: `await page.waitForResponse(urlOrPredicate)`
- Route-based waits: use `page.route` to intercept/wait for mocked responses

**Why async/await?**
Playwright APIs return promises. `async/await` makes asynchronous flows linear/readable and ensures actions complete before assertions.

**page.evaluate vs context.evaluate**
- `page.evaluate(fn)`: Executes inside the page DOM/frame context
- `page.context().evaluate(fn)`: Executes in browser context scope (shared across pages)

---

## Page Objects, Iframes, Authentication, File Uploads

**Page Object Model (POM):**
- Encapsulate locators/actions in classes (e.g., ProductPage, CartPage, CheckoutPage)
- Expose high‑level methods (e.g., `addToCart()`, `completeCheckout()`) for readable/maintainable tests

**Iframes:**
- `const frame = page.frame({ name: 'frameName' })` or `page.frameLocator('iframe#id')` then interact with `frame.locator('selector')`

**Authentication pop‑ups:**
- HTTP Basic Auth: pass credentials when creating context:
```js
const context = await browser.newContext({ httpCredentials: { username: 'user', password: 'pass' } });
```
- Dialog popups: handle via `page.on('dialog', dialog => dialog.accept())` or bypass UI by setting session cookies/tokens

**File uploads:**
```js
await page.setInputFiles('input[type=file]', 'tests/fixtures/image.png');
```

---

## Parallel Testing, Network Capture, Mobile Emulation, Permissions

**Parallel testing:**
- Use Playwright Test runner workers: `npx playwright test --workers=4` or configure workers in `playwright.config.ts`
- Use multiple browser contexts for isolated sessions

**Capture network requests/responses:**
```js
page.on('request', request => { /* inspect request */ });
page.on('response', response => { /* inspect response */ });
```
Intercept and mock:
```js
await page.route('**/payments/**', route => route.fulfill({ status: 200, body: '{}' }));
```

**Mobile emulation:**
```js
import { devices } from '@playwright/test';
const iPhone = devices['iPhone 13'];
const context = await browser.newContext({ ...iPhone });
```
This sets viewport, user agent, and touch support.

**Browser permissions:**
```js
const context = await browser.newContext({ permissions: ['geolocation'] });
```

---

## Debugging, Timeouts, Strict Mode, Assertions, Reports

**Troubleshooting failures:**
- Run tests headed locally: `npx playwright test --headed` or `PWDEBUG=1 npx playwright test`
- Capture artifacts: configure `screenshot: 'only-on-failure'`, `video: 'retain-on-failure'` in config
- Inspect console/network: `page.on('console', ...)`, `page.on('requestfailed', ...)`

**Increase test timeout:**
- Global: `test.setTimeout(60_000)` or `timeout` in `playwright.config.ts`
- Per test: `test('name', async () => { ... }, { timeout: 120_000 })`

**Strict mode violation:**
- Occurs when a locator matches multiple elements. Resolve with `first()`, `nth()`, or more specific selectors

**Assertions:**
- Use Playwright Test `expect` assertions: `toHaveText`, `toBeVisible`, `toHaveURL`, `toHaveAttribute`, `toContainText`, `toHaveScreenshot` (visual checks)

**Reports and artifacts:**
- Built‑in reporters: html, list, junit, json. Configure multiple reporters in `playwright.config.ts`
- Allure integration via community reporter plugin and by attaching screenshots/videos in hooks

---

## Dropdowns, POM Implementation, Hooks, Retries, Grouping, Envs

**Select dropdown value:**
- Native `<select>`:
```js
await page.selectOption('select#size', 'L');
```
- Custom dropdown: open then click option text:
```js
await page.click('#dropdown');
await page.click('text=Large');
```

**Verify dropdown values:**
```js
const values = await page.$$eval('select#size option', opts => opts.map(o => o.value));
```

**POM example (TypeScript):**
```ts
// pages/ProductPage.ts
import { Page } from '@playwright/test';
export class ProductPage {
  constructor(private page: Page) {}
  addToCartBtn = () => this.page.locator('button#add-to-cart');
  async addToCart() { await this.addToCartBtn().click(); }
  async price() { return this.page.locator('.price').textContent(); }
}
```

**Hooks:**
- `test.beforeAll`, `test.beforeEach`, `test.afterEach`, `test.afterAll` for setup/teardown

**Group tests:**
- Use `test.describe('Checkout', () => { ... })` and run with filters

**Environment configuration:**
- Use environment variables and `playwright.config.ts` projects or CI matrix to run tests against QA, Staging, Prod by switching `baseURL` and credentials

---

## Automatic Reruns, Data Driven Testing, Visual Testing, Multiple Sessions, Reports

**Automatic reruns:**
- Configure retries in config or run with `npx playwright test --retries=2`

**Data driven testing:**
- Parameterize tests with arrays/loops or `test.describe.parallel` and `test.each` patterns

**Visual testing:**
- Use `expect(page).toHaveScreenshot('baseline.png')` for screenshot comparisons or integrate with Applitools/Percy

**Opening multiple browser sessions:**
```js
const ctx1 = await browser.newContext();
const ctx2 = await browser.newContext();
const page1 = await ctx1.newPage();
const page2 = await ctx2.newPage();
```

**Multiple report types:**
- Configure reporters array in `playwright.config.ts`: `reporter: [['html'], ['junit', { outputFile: 'results.xml' }]]`

---

## API Testing with Playwright (Sample Code)

Playwright supports API testing using `APIRequestContext` or the `request` fixture.

**GET and POST sample (TypeScript):**
```ts
import { test, expect } from '@playwright/test';
const getResp = await request.get('https://api.example.com/products');
expect(getResp.status()).toBe(200);
const products = await getResp.json();
const postResp = await request.post('https://api.example.com/cart', {
  data: { productId: products[0].id, qty: 1 }
});
expect(postResp.status()).toBe(201);
```

**PUT, PATCH, DELETE sample:**
```ts
test('PUT PATCH DELETE', async ({ request }) => {
  const put = await request.put('https://api.example.com/cart/123', { data: { qty: 2 } });
  expect(put.status()).toBe(200);
  const patch = await request.patch('https://api.example.com/cart/123', { data: { qty: 3 } });
  expect(patch.status()).toBe(200);
  const del = await request.delete('https://api.example.com/cart/123');
  expect(del.status()).toBe(204);
});
```

**Query parameters:**
- Append to URL or build query string:
```js
await request.get('https://api.example.com/search?category=shoes&page=2');
```

---

## Code Samples: Interactions, Drag & Drop, Mouse, Keyboard, Skip

**Drag and Drop:**
```ts
await page.locator('#source').dragTo(page.locator('#target'));
// Or using mouse
await page.hover('#source');
await page.mouse.down();
await page.mouse.move(300, 200);
await page.mouse.up();
```

**Mouse actions:**
- `page.mouse.move(x, y)`, `page.mouse.click(x, y)`, `page.mouse.down()`, `page.mouse.up()`

**Keyboard actions:**
- `await page.keyboard.type('hello')`, `await page.keyboard.press('Enter')`, `await page.keyboard.down('Control')`

**Skip test:**
- `test.skip(condition, 'reason')` or `test('name', async () => {}, { skip: true })`

---

## Visual Testing, Artifacts, and CI

**Screenshots and video in reports:**
Configure in `playwright.config.ts`:
```ts
use: {
  screenshot: 'only-on-failure',
  video: 'retain-on-failure'
}
```
Attach artifacts to Allure or custom reporters in hooks.

**Codegen purpose:**
- `npx playwright codegen <url>` records interactions and generates starter scripts for robust selectors

**Make tests more readable:**
- Use page objects, descriptive test titles, `test.step` for logical grouping, and small focused tests

**Upgrade Playwright:**
- Update packages: `npm i -D @playwright/test@latest playwright@latest` then run `npx playwright install` to update browser binaries

**Running tests headless:**
- Default in CI. To run headed: `npx playwright test --headed` or set `headless: false` in `browserType.launch()`

**Run specific spec:**
- `npx playwright test tests/cart.spec.ts` or filter by title with `-g`

---

## Mock Interview STAR Examples (Ecommerce Domain)

### Example 1 — Reducing Checkout Flakiness

- **Situation:** Checkout tests failing intermittently in CI due to third‑party payment latency
- **Task:** Stabilize checkout automation to avoid blocking releases
- **Action:** Replaced brittle selectors with role/test‑id selectors, stubbed payment provider responses in CI using `page.route`, added `waitForResponse` for order confirmation, and enabled video capture on failure
- **Result:** Flaky failures dropped by 85% and CI reruns decreased significantly

### Example 2 — Building a Scalable Playwright Framework

- **Situation:** Multiple teams needed consistent cross‑browser tests
- **Task:** Create a maintainable framework supporting parallel runs and multiple environments
- **Action:** Implemented layered architecture with fixtures, page objects, API service clients, environment projects in config, and CI matrix for browsers. Added Allure reporting and nightly visual regression.
- **Result:** Reduced duplicate code and maintenance overhead, improved test reliability and team adoption.