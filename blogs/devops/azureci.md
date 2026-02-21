<!-- Docsify: Azure DevOps + Playwright CI guide -->

# 🚀 Azure DevOps CI for Playwright (Beginner‑Friendly)

This guide shows a simple, working Azure Pipelines YAML for running Playwright tests and publishing HTML/JUnit results.

---

## 🧩 Goals

Your pipeline should:

- Pull code from your repository
- Install Node and project dependencies
- Install Playwright browsers
- Run Playwright tests
- Publish test results and the HTML report

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

## 🛠 Azure DevOps Requirements

- Use an Ubuntu agent (`ubuntu-latest`) for simplicity
- Azure Pipelines built‑in `NodeTool` task or install Node manually
- `Publish Test Results` task to upload JUnit XML
- `Publish Pipeline Artifact` or `PublishBuildArtifacts` to publish HTML report

Playwright runs fine in headless mode on Azure agents; no GUI is required for most CI scenarios.

---

## 📄 Minimal Pipeline (single-stage)

Create `azure-pipelines.yml` with this minimal, working pipeline:

```yaml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

steps:
  - task: NodeTool@0
    inputs:
      versionSpec: '18.x'
    displayName: 'Install Node.js'

  - script: npm ci
    displayName: 'Install dependencies'

  - script: npx playwright install --with-deps
    displayName: 'Install Playwright browsers'

  - script: npx playwright test --reporter=junit
    displayName: 'Run Playwright tests'

  - task: PublishTestResults@2
    inputs:
      testResultsFiles: '**/results.xml'
      testRunTitle: 'Playwright Test Results'
    displayName: 'Publish JUnit Test Results'

  - task: PublishPipelineArtifact@1
    inputs:
      targetPath: 'playwright-report'
      artifact: 'playwright-report'
    displayName: 'Publish HTML Report'
```

Notes:

- Use `npm ci` for deterministic installs in CI.
- Ensure Playwright test output produces JUnit XML at a path matching `**/results.xml` (adjust if needed).

---

## ✅ Stage‑based Pipeline (recommended for clarity)

This version splits work into stages for readability and better control:

```yaml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

stages:
  - stage: InstallNode
    jobs:
      - job: InstallNodeJob
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '18.x'

  - stage: InstallDependencies
    dependsOn: InstallNode
    jobs:
      - job: InstallDependenciesJob
        steps:
          - script: npm ci

  - stage: InstallBrowsers
    dependsOn: InstallDependencies
    jobs:
      - job: InstallBrowsersJob
        steps:
          - script: npx playwright install --with-deps

  - stage: RunTests
    dependsOn: InstallBrowsers
    jobs:
      - job: RunTestsJob
        steps:
          - script: npx playwright test --reporter=junit
          - task: PublishTestResults@2
            inputs:
              testResultsFiles: '**/results.xml'
              testRunTitle: 'Playwright Test Results'

  - stage: PublishReport
    dependsOn: RunTests
    jobs:
      - job: PublishReportJob
        steps:
          - task: PublishPipelineArtifact@1
            inputs:
              targetPath: 'playwright-report'
              artifact: 'playwright-report'
```

---

## 🧠 What Each Step/Stage Does

| Stage               | Purpose                                      |
|---------------------|----------------------------------------------|
| InstallNode         | Installs Node.js on the agent                |
| InstallDependencies | Runs `npm ci` to install packages            |
| InstallBrowsers     | Installs Playwright browser binaries         |
| RunTests            | Executes Playwright tests, generates JUnit   |
| PublishReport       | Uploads the HTML report as a pipeline artifact|

---

## 📊 Viewing Results in Azure DevOps

- **Test Results:** Pipelines → Run → Tests (shows JUnit results)
- **HTML Report:** Pipelines → Run → Artifacts → `playwright-report` → `index.html`

Download the artifact and open `index.html` locally if the web preview is not available.

---

## 🎯 Optional Enhancements

- Parallel Playwright execution using `npx playwright test --parallel`
- Matrix builds to run across browsers (Chromium/Firefox/WebKit)
- Trigger on pull requests or branch filters
- Slack/Teams notifications on pipeline status
- Dockerized agents for reproducible environments

---

## Quick local commands

```bash
npm ci
npx playwright test
npx playwright show-report
```
