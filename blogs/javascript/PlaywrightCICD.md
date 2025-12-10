# Playwright CI/CD Integration

Run Playwright tests automatically in your CI/CD pipelines for continuous quality.

## Example: GitHub Actions Workflow
```yaml
name: Playwright Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
      - name: Install dependencies
        run: npm ci
      - name: Install browsers
        run: npx playwright install --with-deps
      - name: Run Playwright tests
        run: npx playwright test
      - name: Upload test results
        uses: actions/upload-artifact@v3
        with:
          name: playwright-report
          path: playwright-report
```

## Example: Azure Pipelines
```yaml
- task: NodeTool@0
  inputs:
    versionSpec: '20.x'
- script: npm ci
- script: npx playwright install --with-deps
- script: npx playwright test
```

## Best Practices
- Use `npx playwright install --with-deps` in CI
- Upload reports and traces as artifacts
- Run tests on every push and pull request
- Use matrix builds for multiple OS/browser versions
