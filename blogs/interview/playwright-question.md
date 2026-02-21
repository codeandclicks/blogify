# Playwright Interview Questions & Answers

---

## 🎯 SECTION 1 — Core Playwright Fundamentals (10 Questions)

### 1. Explain Playwright’s architecture and how browser contexts work
Playwright uses a client–server architecture:
- Your test runs in Node.js (client).
- Playwright launches a browser server (Chromium, Firefox, WebKit).
- Communication happens over a WebSocket protocol.

**Browser Contexts**
A browser context is a lightweight, isolated browser session. Each context has:
- Separate cookies
- Separate local/session storage
- Separate cache
- Independent pages/tabs

This allows parallel, isolated tests without launching multiple browsers.

```ts
const context = await browser.newContext();
const page = await context.newPage();
```

### 2. What are the advantages of Playwright over Selenium?
- Auto‑wait for elements, navigation, network
- Browser contexts → faster parallelism
- Native support for Chrome, Firefox, WebKit
- Network mocking built‑in
- Shadow DOM, iframes, downloads, uploads supported out of the box
- Faster execution (no WebDriver layer)
- Modern selectors (role, testId, text)

### 3. How does Playwright’s auto‑wait mechanism work internally?
Playwright waits for:
- Element to be attached to DOM
- Element to be visible
- Element to be stable (no movement)
- Element to be enabled
- Element to receive events

Before performing actions like click, fill, type. This prevents flaky tests without manual waits.

### 4. What is the difference between locator and page.$?
| Feature      | locator() | page.$() |
|-------------|-----------|----------|
| Auto‑wait   | ✅ Yes    | ❌ No    |
| Retry       | ✅ Yes    | ❌ No    |
| Recommended | ✅ Yes    | ❌ Deprecated |
| Finds       | Future elements | Current DOM snapshot |

> Use locator always.

### 5. How do you handle dynamic elements that appear/disappear?
```ts
await expect(page.getByText('Added to cart')).toBeVisible({ timeout: 5000 });
```
Or wait for specific conditions:
```ts
await page.waitForSelector('.toast', { state: 'visible' });
await page.waitForSelector('.toast', { state: 'hidden' });
```

### 6. Explain strict mode and how to fix strict mode violations
Strict mode ensures one locator = one element.
If multiple matches exist, Playwright throws:
> "strict mode violation: locator resolved to multiple elements"

**Fix:**
- Use more specific selectors
- Use nth()
- Use has or hasText

```ts
page.locator('.item', { hasText: 'Shoes' }).click();
```

### 7. How do you handle shadow DOM elements?
```ts
await page.locator('my-element').locator(':shadow .btn').click();
```
Playwright automatically pierces shadow roots.

### 8. How do you handle iframes in Playwright?
```ts
const frame = page.frameLocator('#payment-frame');
await frame.getByText('Pay Now').click();
```
frameLocator auto‑waits for the iframe to load.

### 9. How do you handle multi‑tab workflows?
```ts
const [newPage] = await Promise.all([
  context.waitForEvent('page'),
  page.getByText('Open Offer').click()
]);

await newPage.getByRole('button', { name: 'Buy' }).click();
```

### 10. How do you test responsive UI across breakpoints?
```ts
test.use({ viewport: { width: 375, height: 812 } }); // mobile

test('desktop', async ({ page }) => {
  await page.setViewportSize({ width: 1440, height: 900 });
});
```

---

## 🎯 SECTION 2 — Selectors & Locators (5 Questions)

### 11. Explain different selector types and when to use each
- Role selectors → accessibility‑based, stable
- Text selectors → quick content targeting
- CSS selectors → structure‑based
- XPath → avoid unless necessary
- Test IDs → most stable for automation
- Chained locators → parent → child

### 12. Why are role selectors recommended?
- Based on ARIA roles
- Stable across UI changes
- Match how screen readers interpret the UI
- Auto‑wait + retries

```ts
page.getByRole('button', { name: 'Login' });
```

### 13. How do you locate elements inside a list or grid?
```ts
await page.locator('.product-item').nth(2).click();
```
Or:
```ts
page.getByRole('listitem').nth(3);
```

### 14. How do you filter locators using has and hasText?
```ts
page.locator('.card', { hasText: 'Shoes' }).click();
```
Or:
```ts
page.locator('.card', { has: page.locator('.price-tag') });
```

### 15. How do you create custom test IDs for stable selectors?
**HTML:**
```html
<button data-testid="add-to-cart">Add</button>
```
**Playwright:**
```ts
page.getByTestId('add-to-cart').click();
```

---

## 🎯 SECTION 3 — Waiting, Timing, and Reliability (5 Questions)

### 16. How do you wait for network idle?
```ts
await page.waitForLoadState('networkidle');
```

### 17. How do you wait for a specific API call?
```ts
await page.waitForResponse(
  res => res.url().includes('/inventory') && res.status() === 200
);
```

### 18. How do you handle flaky animations?
```ts
await page.addStyleTag({ content: '* { animation: none !important; }' });
```

### 19. How do you increase timeouts globally and per test?
**Global:**
```ts
// playwright.config.ts
timeout: 30000,
expect: { timeout: 5000 }
```
**Per test:**
```ts
test.setTimeout(20000);
```

### 20. How do you debug timeout issues?
- Use PWDEBUG=1
- Add page.pause()
- Enable tracing
- Check selector correctness
- Use locator.highlight()

---

## 🎯 SECTION 4 — Network Interception & Mocking (10 Questions)

### 21. How do you mock REST API responses?
```ts
await page.route('**/products', route =>
  route.fulfill({
    status: 200,
    body: JSON.stringify({ items: [] })
  })
);
```

### 22. How do you mock GraphQL?
```ts
await page.route('**/graphql', async route => {
  const req = route.request();
  const body = req.postDataJSON();

  if (body.operationName === 'GetProducts') {
    return route.fulfill({
      body: JSON.stringify({ data: { products: [] } })
    });
  }

  route.continue();
});
```

### 23. How do you modify request headers?
```ts
await page.route('**/*', route => {
  route.continue({
    headers: {
      ...route.request().headers(),
      'x-test': '1'
    }
  });
});
```

### 24. How do you block analytics or tracking scripts?
```ts
await page.route('**/analytics/**', route => route.abort());
```

### 25. How do you capture all network requests?
```ts
page.on('request', req => console.log(req.url()));
```

### 26. How do you capture failed requests?
```ts
page.on('requestfailed', req => {
  console.log('FAILED:', req.url(), req.failure());
});
```

### 27. How do you mock file uploads?
```ts
await page.setInputFiles('input[type=file]', 'tests/files/sample.pdf');
```

### 28. How do you test rate limiting (429 errors)?
```ts
await page.route('**/checkout', route =>
  route.fulfill({ status: 429, body: 'Too Many Requests' })
);
```

### 29. How do you test retry logic in the UI?
Mock first failure → then success:
```ts
let count = 0;

await page.route('**/inventory', route => {
  count++;
  if (count === 1)
    return route.fulfill({ status: 500 });
  return route.fulfill({ status: 200, body: '{}' });
});
```

### 30. How do you validate API + UI consistency?
```ts
const api = await request.get('/products');
const ui = await page.locator('.product').allTextContents();

expect(ui.length).toBe(api.json().items.length);
```

---

## 🎯 SECTION 5 — Authentication & State Management (5 Questions)

### 31. How do you bypass login using storage state?
```ts
await context.storageState({ path: 'auth.json' });
```
Then:
```ts
test.use({ storageState: 'auth.json' });
```

### 32. How do you handle SSO or MFA login flows?
- Use headed mode + page.pause()
- Capture storageState
- Reuse state in all tests
- For MFA: mock backend token exchange

### 33. How do you inject cookies before navigation?
```ts
await context.addCookies([
  { name: 'auth', value: '123', domain: 'site.com', path: '/' }
]);
```

### 34. How do you test logout functionality?
- Click logout
- Assert cookies cleared
- Assert redirected to login

```ts
await expect(context.cookies()).resolves.toHaveLength(0);
```

### 35. How do you share authenticated state across tests?
Use a global setup file:
```ts
export default async () => {
  const browser = await chromium.launch();
  const context = await browser.newContext();
  const page = await context.newPage();

  await login(page);

  await context.storageState({ path: 'auth.json' });
};
```

---

## 🎯 SECTION 6 — Ecommerce‑Specific Scenarios (10 Questions)

### 36. How do you test infinite scroll product lists?
```ts
await page.mouse.wheel(0, 800);
await expect(page.locator('.product-item')).toHaveCount(40);
```

### 37. How do you test cart updates in real time (WebSockets)?
- Listen to WebSocket messages
- Assert UI updates

```ts
page.on('websocket', ws => {
  ws.on('framereceived', data => console.log(data));
});
```

### 38. How do you test checkout flows with multiple steps?
- Use test.step()
- Validate each step
- Mock APIs for reliability

### 39. How do you test coupon code validation?
```ts
await page.fill('#coupon', 'SAVE10');
await page.click('#apply');
await expect(page.getByText('Coupon applied')).toBeVisible();
```

### 40. How do you test payment gateway redirects?
```ts
const [popup] = await Promise.all([
  context.waitForEvent('page'),
  page.click('#pay')
]);

await popup.getByText('Confirm Payment').click();
```

### 41. How do you mock payment provider APIs?
```ts
await page.route('**/stripe/**', route =>
  route.fulfill({ status: 200, body: '{}' })
);
```

### 42. How do you test product filtering and sorting?
```ts
await page.getByText('Price: Low to High').click();
const prices = await page.locator('.price').allTextContents();
```

### 43. How do you test inventory updates?
- Mock inventory API → assert UI refresh.

### 44. How do you test order confirmation emails (API)?
```ts
const res = await request.get('/emails?user=123');
expect(res.json().subject).toContain('Order Confirmation');
```

### 45. How do you test abandoned cart flows?
- Add items
- Close browser
- Reopen with same storage state
- Assert cart persists

---

## 🎯 SECTION 7 — Framework Design & Architecture (10 Questions)

### 46. Explain your Playwright framework architecture
Typical structure:
```
tests/
pages/
fixtures/
utils/
config/
global-setup.ts
```
- Page Objects → encapsulate UI
- Fixtures → reusable setup
- Utils → API helpers, data generators
- Config → environments, retries, reporters

### 47. How do you implement Page Object Model in Playwright?
```ts
class CartPage {
  constructor(private page: Page) {}

  checkoutBtn = this.page.getByRole('button', { name: 'Checkout' });

  async checkout() {
    await this.checkoutBtn.click();
  }
}
```

### 48. How do you design reusable fixtures?
```ts
export const test = base.extend({
  cartPage: async ({ page }, use) => {
    await use(new CartPage(page));
  }
});
```

### 49. How do you structure tests for readability?
- AAA pattern (Arrange, Act, Assert)
- Use fixtures
- Use Page Objects
- Use test.step()

### 50. How do you handle test data management?
- Use JSON/YAML files
- Use API calls to seed data
- Use random data generators (Faker)
- Clean up after tests

### 51. How do you integrate API + UI tests?
- Use Playwright’s request fixture
- Seed data via API
- Validate UI against API

### 52. How do you run tests in parallel safely?
- Use browser contexts, not shared state
- Avoid writing to same DB records
- Use unique test data per test

### 53. How do you run tests across multiple environments?
```ts
// playwright.config.ts
use: {
  baseURL: process.env.ENV === 'prod'
    ? 'https://prod.com'
    : 'https://staging.com'
}
```

### 54. How do you integrate Allure or HTML reports?
```ts
reporter: [['html'], ['allure-playwright']]
```

### 55. How do you implement CI/CD with Playwright?
- Use GitHub Actions / Azure DevOps / Jenkins
- Install browsers
- Run tests in parallel
- Upload reports + artifacts
- Use Docker for consistency

---

## 🎯 SECTION 8 — Debugging, Logging, and Troubleshooting (5 Questions)

### 56. How do you debug tests using Playwright Inspector?
```bash
PWDEBUG=1 npx playwright test
```
Or:
```ts
await page.pause();
```

### 57. How do you capture console logs and fail on errors?
```ts
page.on('console', msg => {
  if (msg.type() === 'error') throw new Error(msg.text());
});
```

### 58. How do you capture screenshots and videos?
**Config:**
```ts
use: {
  screenshot: 'only-on-failure',
  video: 'retain-on-failure'
}
```
**Per test:**
```ts
await page.screenshot({ path: 'error.png' });
```

### 59. How do you debug flaky tests?
- Use tracing
- Use inspector
- Add locator debugging (highlight())
- Remove animations
- Mock network
- Check selector stability

### 60. How do you analyze network logs to find root causes?
- Use page.on('request') and page.on('response')
- Use tracing viewer
- Use HAR recording
- Validate API failures causing UI failures

---