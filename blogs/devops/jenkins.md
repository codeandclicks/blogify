<!-- Docsify: Jenkins + Playwright CI/CD guide -->

# 🚀 Jenkins CI/CD for Playwright (Beginner‑Friendly)

Playwright is a powerful end‑to‑end testing framework, and Jenkins is a widely used CI/CD tool. Combined, they let you run automated browser tests on every code change.

This page shows a simple, working Jenkins pipeline for Playwright and explains each component.

---

## 🧩 What You’re Trying to Achieve

You want Jenkins to:

- Pull your Playwright test code from a Git repository (GitHub/GitLab/Bitbucket)
- Install Node.js and project dependencies
- Install Playwright browsers
- Run Playwright tests
- Publish test results (HTML report, JUnit XML, etc.)

This is the basic CI pipeline for browser automation.

---

## 📁 Example Project Structure

```
my-playwright-project/
├── tests/
│   └── example.spec.js
├── package.json
└── playwright.config.js
```

Example `package.json` scripts:

```json
{
  "scripts": {
    "test": "playwright test",
    "test:report": "playwright show-report"
  }
}
```

---

## 🛠️ Jenkins Setup Requirements

- Node.js installed on the Jenkins agent (or use the NodeJS plugin)
- Git plugin
- HTML Publisher plugin (optional but useful)
- A Jenkins agent with GUI support if you require non‑headless runs (headless is fine for most cases)

Install Playwright browsers on the agent (can be done inside the pipeline):

```bash
npx playwright install --with-deps
```

---

## 🧪 Sample Playwright Test (`tests/example.spec.js`)

```js
const { test, expect } = require('@playwright/test');

test('basic test', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  await expect(page).toHaveTitle(/Playwright/);
});
```

---

## 🧱 Jenkins Pipeline (Jenkinsfile) — Beginner Friendly

Copy this into a `Jenkinsfile` at the repository root for a minimal working pipeline:

```groovy
pipeline {
  agent any

  stages {
    stage('Checkout Code') {
      steps {
        git branch: 'main', url: 'https://github.com/your/repo.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm ci'
      }
    }

    stage('Install Playwright Browsers') {
      steps {
        sh 'npx playwright install --with-deps'
      }
    }

    stage('Run Playwright Tests') {
      steps {
        sh 'npx playwright test --reporter=junit'
      }
      post {
        always {
          junit 'playwright-report/*.xml'
        }
      }
    }

    stage('Publish HTML Report') {
      steps {
        publishHTML(target: [
          reportDir: 'playwright-report',
          reportFiles: 'index.html',
          reportName: 'Playwright Test Report'
        ])
      }
    }
  }
}
```

Notes:

- Use `npm ci` in CI for deterministic installs.
- Adjust the `git` URL and branch to match your repo.
- If your Jenkins agent runs on Windows, replace `sh` steps with the appropriate `bat` commands.

---

## 🧠 What This Pipeline Does

| Stage               | Purpose                                      |
|---------------------|----------------------------------------------|
| Checkout Code       | Pulls your test code from Git                |
| Install Dependencies| Installs Playwright and Node modules         |
| Install Browsers    | Downloads Chromium, Firefox, WebKit          |
| Run Tests           | Executes Playwright tests (JUnit XML output) |
| Publish Report      | Displays HTML report inside Jenkins          |

---

## 📊 Viewing Results in Jenkins

After the pipeline runs:

- Go to the build and open **Playwright Test Report** to view the HTML report.
- Check **Test Results** (JUnit) for detailed test case passes/failures.

This gives you a full CI testing dashboard in Jenkins.

---

## 🎯 Optional Enhancements

- Parallel test execution (split tests across agents)
- Slack/Teams notifications for build/test status
- Dockerized Playwright execution for consistent agents
- Trigger tests on pull requests only
- Run tests on multiple OS (Linux, Windows, macOS)

---

## Example: Quick local commands

Run tests locally:

```bash
npm ci
npx playwright test
npx playwright show-report
```
