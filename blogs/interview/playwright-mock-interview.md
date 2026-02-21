# Senior Playwright Engineer — Mock Interview (Ecommerce Domain)

---

> **Format:** Interviewer Q&A | Focus: Playwright, Automation, CI/CD, Ecommerce

---

**Interviewer:**
> Welcome, thanks for joining today. Let’s start with a quick introduction. Tell me a bit about your background and experience with Playwright.

**Candidate:**
> Thank you. I’m a Senior SDET with around 7+ years in automation and 3+ years specializing in Playwright.
> I’ve built multiple automation frameworks from scratch, mostly for ecommerce platforms — product catalog, cart, checkout, payments, and order management.
> My focus is on reliability, parallel execution, API + UI integration, and CI/CD optimization.

---

### 1. Can you explain Playwright’s architecture and why browser contexts are important?

**Candidate:**
- Playwright has a client–server architecture:
  - Test code communicates with the Playwright driver
  - The driver talks to browser engines (Chromium, WebKit, Firefox)
  - Inside each browser, we can create multiple browser contexts (like isolated incognito sessions)
- **Benefits:**
  - Parallel execution without launching multiple browsers
  - Clean test isolation
  - Faster test runs
  - For ecommerce, contexts are perfect for simulating multiple users (e.g., buyer and seller workflows)

---

### 2. What are the biggest advantages of Playwright over Selenium?

**Candidate:**
- Auto‑wait for elements to be actionable
- Network interception built‑in
- Browser contexts for fast parallelism
- Native mobile emulation
- Web‑first assertions
- Cross‑browser parity including WebKit
- These reduce flakiness significantly, which is crucial for ecommerce flows like checkout

---

### 3. Show me how you would mock a payment API during checkout.

**Candidate:**
```ts
await page.route('**/payment/charge', route =>
  route.fulfill({
    status: 200,
    body: JSON.stringify({
      status: 'success',
      transactionId: 'TXN12345'
    })
  })
);
await page.getByRole('button', { name: 'Pay Now' }).click();
await expect(page.getByText('Payment Successful')).toBeVisible();
```
> This removes dependency on third‑party gateways and stabilizes CI runs.

---

### 4. How do you structure your Playwright framework?

**Candidate:**
- **Test Layer:** Readable, business‑focused tests, uses fixtures for setup
- **Page Object Layer:** Encapsulates UI actions
  ```ts
  class ProductPage {
    constructor(private page: Page) {}
    addToCart = () => this.page.getByRole('button', { name: 'Add to Cart' });
    async addItem() { await this.addToCart().click(); }
  }
  ```
- **API Layer:** Used for setup/teardown (create cart, login, create product)
- **Utilities:** Wait helpers, network mocks, data generators
- **Config Layer:** Environment switching, browser/project configs, global fixtures

---

### 5. How do you handle authentication for SSO or MFA flows?

**Candidate:**
- Avoid UI login in CI; use API login + storage state
```ts
const token = await request.post('/auth/login', {
  data: { user: 'qa', pass: 'qa123' }
}).then(r => r.json());
await context.addCookies([
  { name: 'session', value: token.jwt, domain: 'shop.com', path: '/' }
]);
await context.storageState({ path: 'auth.json' });
```
- This reduces login time from ~20 seconds to ~2 seconds and avoids MFA issues

---

### 6. How do you debug flaky tests?

**Candidate:**
- Run with inspector: `npx playwright test --debug`
- Add `page.pause()` to inspect DOM
- Capture console logs:
  ```ts
  page.on('console', msg => console.log(msg.text()));
  ```
- Capture network failures:
  ```ts
  page.on('requestfailed', req => console.log(req.url()));
  ```
- Enable video + screenshot on failure
- Reproduce locally with same environment variables
- Most flakiness comes from timing, animations, or unstable selectors

---

### 7. How do you test infinite scroll product lists?

**Candidate:**
```ts
while (await page.locator('.loader').isVisible()) {
  await page.mouse.wheel(0, 800);
}
await expect(page.locator('.product-item')).toHaveCount(50);
```
> This is common in ecommerce product listing pages.

---

### 8. How do you test mobile web using Playwright?

**Candidate:**
```ts
import { devices } from '@playwright/test';
test.use(devices['iPhone 13']);
test('mobile checkout', async ({ page }) => {
  await page.goto('/checkout');
  await expect(page.getByRole('button', { name: 'Pay Now' })).toBeVisible();
});
```
> This sets viewport, touch, user agent, and more.

---

### 9. How do you integrate Playwright tests into CI/CD?

**Candidate:**
- Install dependencies
- Run `npx playwright install`
- Run tests in parallel
- Upload HTML/Allure reports
- Store screenshots/videos as artifacts
- Run cross‑browser matrix (Chromium, Firefox, WebKit)
- Trigger nightly visual regression suite
- Use environment variables to switch between QA/Staging/Prod

---

### 10. Final question — describe a challenging Playwright issue you solved.

**Candidate:**
- **Problem:** Checkout test failing only in CI, passed locally
- **Root cause:** Search API intermittently returned 500 errors in CI
- **Approach:**
  - Captured network logs
  - Added listeners for failed requests
  - Mocked search API in CI
  - Added retry logic for flaky backend calls
  - Reported backend caching issue to dev team
- **Result:**
  - Test stabilized
  - Backend team fixed the root cause
  - CI reliability improved significantly

---

**Interviewer:**
> Thanks, that was excellent. You clearly have strong command of Playwright and automation architecture.

**Candidate:**
> Thank you — I enjoyed the discussion.