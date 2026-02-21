# Playwright STAR Stories — Detailed Explanations & Code Samples

---

## ⭐ 1. Checkout Flow Reliability
**Situation:**
E-commerce checkout flow had frequent UI changes due to A/B testing, causing Playwright tests to break weekly.

**Task:**
Stabilize automation so it could run reliably across Chrome, Firefox, and WebKit.

**Action:**
- Introduced `data-test-id` attributes for stable selectors:
  ```html
  <button data-test-id="checkout">Checkout</button>
  ```
  ```ts
  await page.getByTestId('checkout').click();
  ```
- Used auto-wait patterns:
  ```ts
  await page.waitForURL(/checkout/);
  await page.waitForResponse(res => res.url().includes('/order') && res.status() === 200);
  ```
- Created Page Object Model (POM):
  ```ts
  class CheckoutPage {
    constructor(private page: Page) {}
    async completeCheckout() {
      await this.page.getByTestId('checkout').click();
    }
  }
  ```
- Used tracing and video for debugging:
  ```ts
  await page.tracing.start({ screenshots: true, snapshots: true });
  await page.tracing.stop({ path: 'trace.zip' });
  await page.video().saveAs('checkout.mp4');
  ```

**Result:**
Flakiness dropped from 38% to under 3%, and cross-browser coverage increased from 1 to 3 browsers without extra maintenance.

---

## ⭐ 2. Handling Dynamic Pricing & Flash Sales
**Situation:**
Flash sales caused product prices to update every few seconds, breaking tests due to mismatched expected values.

**Task:**
Make tests resilient to dynamic pricing.

**Action:**
- Used API mocks to freeze price responses:
  ```ts
  await page.route('**/prices', route => route.fulfill({ body: JSON.stringify({ price: 99 }) }));
  ```
- For real-time tests, validated price format, not exact value:
  ```ts
  const priceText = await page.locator('.price').textContent();
  expect(priceText).toMatch(/^\$\d+/);
  ```
- Added retry logic for WebSocket-driven updates:
  ```ts
  await expect(page.locator('.price')).toHaveText(/\$\d+/, { timeout: 5000 });
  ```

**Result:**
Reduced false failures by 90%, and flash-sale tests became stable enough for CI.

---

## ⭐ 3. Cart Synchronization Across Devices
**Situation:**
Cart items weren’t syncing between desktop and mobile web.

**Task:**
Automate cross-device cart validation.

**Action:**
- Used Playwright’s multi-context feature:
  ```ts
  const desktop = await browser.newContext();
  const mobile = await browser.newContext({ viewport: { width: 375, height: 812 } });
  ```
- Seeded cart data via API:
  ```ts
  await request.post('/cart', { data: { item: 'Shoes' } });
  ```
- Validated cart state via network interception and UI:
  ```ts
  desktop.on('request', req => {
    if (req.url().includes('/cart')) console.log('Desktop cart:', req.postData());
  });
  ```

**Result:**
Found a caching bug in the cart microservice, reducing cart-related support tickets by 40%.

---

## ⭐ 4. Payment Gateway Integration
**Situation:**
Real payment gateways (Stripe/PayPal) couldn’t be hit in automation.

**Task:**
Simulate payment success/failure without real transactions.

**Action:**
- Mocked gateway responses:
  ```ts
  await page.route('**/stripe/**', route => route.fulfill({ status: 200, body: '{}' }));
  await page.route('**/paypal/**', route => route.fulfill({ status: 200, body: '{}' }));
  ```
- Created reusable mocks for success, decline, and 3-D Secure:
  ```ts
  await page.route('**/payment', route => {
    const req = route.request();
    if (req.postData().includes('decline')) {
      return route.fulfill({ status: 402, body: 'Declined' });
    }
    return route.fulfill({ status: 200, body: 'Success' });
  });
  ```
- Added contract tests to ensure mocks matched real API schemas:
  ```ts
  const schema = require('./payment-schema.json');
  expect(response.body()).toMatchSchema(schema);
  ```

**Result:**
Payment tests ran 5× faster, achieving 95% coverage without real gateways.

---

## ⭐ 5. Search & Product Listing Optimization
**Situation:**
Search results changed frequently due to ranking algorithms.

**Task:**
Create stable tests for search, filters, and sorting.

**Action:**
- Validated behavior, not exact product order:
  ```ts
  const products = await page.locator('.product').allTextContents();
  expect(products).toContain('Shoes');
  ```
- Used API seeding for predictable datasets:
  ```ts
  await request.post('/seed', { data: { products: ['Shoes', 'Bags'] } });
  ```
- Component-level tests for filters:
  ```ts
  await page.getByRole('button', { name: 'Filter' }).click();
  await expect(page.locator('.filtered')).toBeVisible();
  ```

**Result:**
Search test runtime dropped from 14 minutes to 4 minutes, flakiness below 5%.

---

## ⭐ 6. Visual Regression for Product Pages
**Situation:**
Marketing frequently updated product images and layouts.

**Task:**
Detect unintended UI changes early.

**Action:**
- Integrated Playwright + Percy for visual snapshots:
  ```ts
  await percySnapshot(page, 'Product Page');
  ```
- Set up threshold-based diffs:
  ```ts
  await page.screenshot({ path: 'product.png' });
  // Compare with baseline using threshold
  ```
- Tagged tests to run only on PRs affecting UI:
  ```ts
  if (process.env.PR_UI) await runVisualTests();
  ```

**Result:**
Caught 7 major UI regressions before release in the first quarter.

---

## ⭐ 7. API + UI Hybrid Testing
**Situation:**
Full UI journeys were slow and flaky.

**Task:**
Speed up tests without losing coverage.

**Action:**
- Used API calls to create users, add items to cart, set inventory:
  ```ts
  await request.post('/users', { data: { name: 'TestUser' } });
  await request.post('/cart', { data: { item: 'Shoes' } });
  ```
- Started UI tests from mid-journey states:
  ```ts
  await page.goto('/cart');
  await page.getByTestId('checkout').click();
  ```
- Playwright fixtures to reuse API tokens:
  ```ts
  export const test = base.extend({
    apiToken: async ({}, use) => {
      const token = await getApiToken();
      await use(token);
    }
  });
  ```

**Result:**
Reduced end-to-end suite runtime from 2 hours to 28 minutes.

---

## ⭐ 8. Multi-Step User Journey
**Situation:**
Browse → cart → checkout → order flow was long and brittle.

**Task:**
Make it maintainable and modular.

**Action:**
- Modular POMs for each step:
  ```ts
  class BrowsePage {}
  class CartPage {}
  class CheckoutPage {}
  ```
- Test data builders:
  ```ts
  function buildProduct(name) { return { name, price: 99 }; }
  ```
- Fixtures to share state:
  ```ts
  export const test = base.extend({
    cart: async ({}, use) => {
      const cart = await createCart();
      await use(cart);
    }
  });
  ```

**Result:**
Maintenance effort dropped by 60%, onboarding new QA engineers became faster.

---

## ⭐ 9. Authentication & Session Management
**Situation:**
Login involved OTP and MFA, making UI automation slow.

**Task:**
Bypass repetitive login steps.

**Action:**
- API login to generate session tokens:
  ```ts
  const token = await request.post('/login', { data: { user: 'test', otp: '123456' } });
  ```
- Stored authenticated state:
  ```ts
  await context.storageState({ path: 'storageState.json' });
  ```
- Refreshed tokens automatically in CI:
  ```ts
  if (isCI) await refreshToken();
  ```

**Result:**
Login time dropped from 18 seconds to under 1 second, saving hours per CI run.

---

## ⭐ 10. Performance Bottlenecks
**Situation:**
Users reported slow checkout performance.

**Task:**
Use Playwright to identify bottlenecks.

**Action:**
- Captured network timings:
  ```ts
  await page.tracing.start({ screenshots: true });
  await page.goto('/checkout');
  await page.tracing.stop({ path: 'trace.zip' });
  ```
- Flagged slow API calls:
  ```ts
  page.on('response', res => {
    if (res.timing().responseEnd - res.timing().requestStart > 500) {
      console.log('Slow API:', res.url());
    }
  });
  ```
- Performance assertions:
  ```ts
  expect(await page.locator('.checkout-time').textContent()).toBeLessThan('2s');
  ```

**Result:**
Engineering optimized two APIs, reducing checkout time by 22%.

---

## ⭐ 11. Test Data Management
**Situation:**
Inventory and coupon data changed daily, breaking tests.

**Task:**
Create stable, reusable test data.

**Action:**
- Test data microservice:
  ```ts
  await request.post('/test-data', { data: { type: 'product', name: 'Shoes' } });
  ```
- Fetch fresh data before tests:
  ```ts
  const product = await request.get('/test-data?type=product');
  ```
- Cleanup jobs:
  ```ts
  await request.post('/cleanup', { data: { inventory: true } });
  ```

**Result:**
Eliminated 80% of data-related failures.

---

## ⭐ 12. Micro-Frontend Architecture Challenges
**Situation:**
E-commerce site used micro-frontends deployed independently.

**Task:**
Ensure Playwright tests didn’t break with each independent release.

**Action:**
- Contract tests for shared events/APIs:
  ```ts
  await page.route('**/events', route => route.fulfill({ body: JSON.stringify({ event: 'cart-add' }) }));
  ```
- Component-level Playwright tests:
  ```ts
  await page.getByTestId('micro-cart').click();
  ```
- Feature-flag-aware selectors:
  ```ts
  if (featureFlagEnabled('newCart')) {
    await page.getByTestId('new-cart').click();
  }
  ```

**Result:**
Reduced integration failures by 50%, teams deployed independently without breaking main flow.

---

## ⭐ 13. Accessibility Testing
**Situation:**
Accessibility issues were found late in the cycle.

**Task:**
Shift accessibility testing left.

**Action:**
- Integrated axe-core with Playwright:
  ```ts
  import { injectAxe, checkA11y } from 'axe-playwright';
  await injectAxe(page);
  await checkA11y(page);
  ```
- Accessibility checks in PR pipelines:
  ```yaml
  jobs:
    - run: npx playwright test --tag accessibility
  ```
- Dashboards for WCAG violations:
  ```ts
  // Export violations to dashboard
  ```

**Result:**
Reduced accessibility bugs in production by 70%.

---

## ⭐ 14. CI/CD Pipeline Optimization
**Situation:**
Playwright tests slowed down CI by over 40 minutes.

**Task:**
Optimize execution.

**Action:**
- Enabled parallel workers and test sharding:
  ```ts
  // playwright.config.ts
  workers: 4,
  shard: { current: 1, total: 4 }
  ```
- Tag-based execution:
  ```ts
  test('critical checkout', { tags: ['critical'] }, async () => { ... });
  ```
- Cached browser binaries:
  ```yaml
  - uses: actions/cache
    with:
      path: ~/.cache/ms-playwright
      key: ${{ runner.os }}-playwright
  ```

**Result:**
Pipeline time dropped from 52 minutes to 17 minutes.

---

## ⭐ 15. Handling Real-Time Inventory Updates
**Situation:**
Inventory updated in real time, causing tests to fail when items went out of stock.

**Task:**
Stabilize inventory-dependent tests.

**Action:**
- Mocked inventory API for deterministic tests:
  ```ts
  await page.route('**/inventory', route => route.fulfill({ body: JSON.stringify({ stock: 10 }) }));
  ```
- For real-time tests, validated UI behavior when stock changed:
  ```ts
  await page.locator('.stock').waitFor({ state: 'visible' });
  ```
- Retries for WebSocket-driven updates:
  ```ts
  await expect(page.locator('.stock')).toHaveText('In Stock', { timeout: 5000 });
  ```

**Result:**
Achieved 100% stability for inventory tests and caught a real-time sync bug before release.

---