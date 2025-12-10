# Node.js Basics for Playwright

Playwright runs on Node.js, so understanding Node.js is essential for writing and running your tests. This guide covers the fundamentals you need to be productive.

---

## 1. What is Node.js?

- **Node.js** is a JavaScript runtime that lets you run JavaScript code outside the browser (on your computer/server).
- It uses the **V8 engine** (the same as Chrome) to execute JS code.
- Enables server-side scripting, automation, and running tools like Playwright.

**Analogy:** If the browser is a movie theater for JavaScript, Node.js is a home theater — you can run your own shows, scripts, and automations!

---

## 2. Running JavaScript with Node.js

- Save your code in a `.js` file (e.g., `test.js`).
- Run it in the terminal:

```bash
node test.js
```

- You can use all standard JavaScript plus Node.js built-in modules (like `fs`, `path`, etc.).

---

## 3. NPM: Node Package Manager

- **NPM** is the default package manager for Node.js.
- Lets you install, update, and manage third-party libraries (like Playwright, lodash, etc.).

### Key Commands

| Command | Purpose |
|---------|---------|
| `npm init` | Create a new `package.json` file |
| `npm install <package>` | Install a package locally |
| `npm install -g <package>` | Install a package globally |
| `npm uninstall <package>` | Remove a package |
| `npm update` | Update all packages |
| `npm list` | List installed packages |

### Example: Install Playwright

```bash
npm init -y                # Create package.json quickly
npm install --save-dev playwright
```

- Packages are saved in the `node_modules` folder.
- Dependencies are listed in `package.json`.

---

## 4. package.json: Project Metadata & Scripts

- `package.json` describes your project and its dependencies.
- You can define **scripts** to automate tasks.

### Example `package.json`

```json
{
  "name": "playwright-tests",
  "version": "1.0.0",
  "scripts": {
    "test": "node tests/login.test.js",
    "e2e": "playwright test"
  },
  "devDependencies": {
    "playwright": "^1.40.0"
  }
}
```

### Running Scripts

```bash
npm run test    # Runs: node tests/login.test.js
npm run e2e     # Runs: playwright test
```

---

## 5. File System Basics (fs module)

Node.js lets you read and write files — useful for test data, logs, screenshots, etc.

### Reading a File

```javascript
const fs = require('fs');

// Synchronous (blocking)
const data = fs.readFileSync('data.txt', 'utf-8');
console.log(data);

// Asynchronous (non-blocking)
fs.readFile('data.txt', 'utf-8', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

### Writing a File

```javascript
const fs = require('fs');

// Synchronous
fs.writeFileSync('output.txt', 'Hello, Node!');

// Asynchronous
fs.writeFile('output.txt', 'Hello, Node!', err => {
  if (err) throw err;
  console.log('File written!');
});
```

### Example: Save Test Data

```javascript
const fs = require('fs');
const testResults = [
  { name: 'Login Test', passed: true },
  { name: 'Checkout Test', passed: false }
];

fs.writeFileSync('results.json', JSON.stringify(testResults, null, 2));
```

---

## 6. Playwright + Node.js: Typical Workflow

1. **Write tests** in `.js` or `.ts` files.
2. **Install Playwright** and dependencies with NPM.
3. **Run tests** using `node` or `npx playwright test`.
4. **Read/write files** for test data, screenshots, logs, etc.

---

## 7. Best Practices

✅ Use `npm init` to start every project

✅ Keep dependencies up to date (`npm update`)

✅ Use scripts in `package.json` for repeatable tasks

✅ Prefer async file operations for performance

✅ Store test data and results in files for traceability

---

## 8. Common Mistakes

❌ Forgetting to run `npm install` before using a package

❌ Editing `node_modules` directly (never do this)

❌ Not using `package.json` to track dependencies

❌ Using synchronous file operations in performance-critical code

---

## Quick Reference

```bash
# Run a JS file
node myscript.js

# Install a package
npm install lodash

# Run a script from package.json
npm run test

# Read a file (sync)
const data = fs.readFileSync('file.txt', 'utf-8');

# Write a file (async)
fs.writeFile('file.txt', 'Hello!', err => { ... });
```

---

Node.js is the engine that powers Playwright. Master these basics to automate with confidence! 🚀
