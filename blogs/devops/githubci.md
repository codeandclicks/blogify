<!--
  Docsify: GitHub CI/CD for Playwright
  This file is formatted for Docsify (clear headings, code blocks, lists, table).
-->

# 🌱 What Is GitHub CI/CD? (Beginner‑Friendly)

Think of GitHub CI/CD as a robot helper that automatically works on your project every time you update your code.

- **CI = Continuous Integration**  
  Automatically checks your code whenever you push changes.
- **CD = Continuous Deployment**  
  Automatically publishes or deploys something after the checks pass.

> **CI/CD is automation that runs your tests and publishes your results without you doing anything manually.**

---

## 🧩 Why Do We Need CI/CD for Playwright?

Playwright is a testing tool. CI/CD helps you:

- **Run tests automatically** — No need to run tests on your laptop every time.
- **Catch bugs early** — If something breaks, GitHub tells you immediately.
- **Share reports easily** — Your HTML report can be published to GitHub Pages.
- **Keep your main branch clean** — Pull requests get tested before merging.
- **Work like a real QA engineer** — Follows professional automation practices.

---

## 🏗 How CI/CD Works (Using Your YAML File)

Your workflow file:

```text
.github/workflows/playwright-ci-cd.yml
```

tells GitHub: “When someone pushes code, run these steps.”

Use the headings below to navigate this page in Docsify.

### 📘 Step 1 — GitHub Notices a Change

```yaml
on:
  push:
    branches: [ main ]
  pull_request:
```

This means:
- If you push to `main`, run the pipeline
- If someone opens a pull request, run the pipeline

GitHub is watching your repo for changes.

### 🧱 Step 2 — Build Stage (Prepare Everything)

This is the test job. Typical steps:

1. **Checkout the code** — GitHub downloads your repository into the runner.
2. **Install Node.js** — Playwright requires Node to run.
3. **Install dependencies** — Installs packages from `package.json`.
4. **Install Playwright browsers** — Downloads Chromium, Firefox, WebKit.
5. **Run the tests** — Playwright executes your test files.
6. **Save the HTML report** — Store the report as an artifact for later use.

This completes the **CI (Continuous Integration)** section.

---

## 🚀 Step 3 — Deploy Stage (Publish the Report)

This is the deploy job. It typically runs only when:
- The test job completes successfully
- The branch is `main`

Typical deploy steps:

1. **Download the test report** — Retrieve the artifact created by the test job.
2. **Deploy to GitHub Pages** — Publish the HTML report to the `gh-pages` branch.

Hosted URL example:

```text
https://<your-username>.github.io/<your-repo>/
```

This completes the **CD (Continuous Deployment)** section.

---

### 🧠 Putting It All Together (Simple Analogy)

Imagine you run a bakery:

- **CI = Checking the dough** — Every time someone adds ingredients (code), the robot checks:
  - Is the dough good?
  - Are there mistakes?
  - Does it bake correctly?
- **CD = Putting the cake in the display window** — If everything is good, the robot:
  - Packs the cake
  - Places it in the shop window (GitHub Pages)

You don’t have to do anything manually.

---

## 🎉 Final Summary for Beginners

| Concept   | Meaning                      | In Your Pipeline                        |
|-----------|------------------------------|-----------------------------------------|
| CI        | Automatically test code      | Playwright tests run on every push      |
| CD        | Automatically deploy results | HTML report published to GitHub Pages   |
| Workflow  | A set of instructions        | Your YAML file                          |
| Job       | A group of steps             | test and deploy                         |
| Artifact  | A saved file                 | Playwright HTML report                  |
| Runner    | GitHub’s virtual machine     | `ubuntu-latest`                         |

Your pipeline is a full CI/CD system that:
- Builds the environment
- Runs Playwright tests
- Publishes the HTML report online

All automatically.

---

### Example: Minimal Playwright workflow (copy into `.github/workflows/playwright.yml`)

```yaml
name: Playwright Tests

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright Browsers
        run: npx playwright install --with-deps

      - name: Run Playwright tests
        run: npx playwright test

      - name: Upload Playwright report
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report
          path: playwright-report/
```
