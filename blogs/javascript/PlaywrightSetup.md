# Playwright Installation & Setup

Playwright is easy to install and set up for end-to-end testing.

## Install Playwright Test

```bash
npm install --save-dev @playwright/test
```

- Installs Playwright and its test runner.
- The first run downloads Chromium, Firefox, and WebKit browsers.

## Initialize Project (Optional)

```bash
npx playwright install
```

- Installs browser binaries if not already present.

## Example: Minimal Setup

```bash
npm init -y
npm install --save-dev @playwright/test
npx playwright install
```

You are now ready to write and run Playwright tests!
