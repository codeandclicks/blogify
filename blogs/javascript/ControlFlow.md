# Control Flow

Control flow determines the order in which your code executes. Instead of running line by line, you can make decisions, repeat actions, and create logic based on conditions.

## What is control flow?

Think of control flow like giving directions:
- **If** it's raining, take an umbrella
- **Otherwise**, wear sunglasses
- **Repeat** walking until you reach home

In JavaScript, we use similar logic to control how our program runs.

## If/Else statements

The `if` statement runs code only when a condition is true.

### Basic if statement

```javascript
const age = 18;

if (age >= 18) {
  console.log("You can vote!"); // This runs
}
```

### If/Else

```javascript
const temperature = 15;

if (temperature > 25) {
  console.log("It's hot! Wear shorts.");
} else {
  console.log("It's cool! Wear a jacket.");
}
// Output: "It's cool! Wear a jacket."
```

### If/Else if/Else

```javascript
const score = 75;

if (score >= 90) {
  console.log("Grade: A");
} else if (score >= 80) {
  console.log("Grade: B");
} else if (score >= 70) {
  console.log("Grade: C");
} else {
  console.log("Grade: F");
}
// Output: "Grade: C"
```

### Playwright example

```javascript
// Check if element exists before clicking
const button = page.locator('#submit-btn');
const isVisible = await button.isVisible();

if (isVisible) {
  await button.click();
  console.log("Button clicked");
} else {
  console.log("Button not found, skipping...");
}
```

## Switch statements

`switch` is useful when you have many possible values to check against one variable.

### Basic switch

```javascript
const day = "Monday";

switch (day) {
  case "Monday":
    console.log("Start of the work week");
    break;
  case "Friday":
    console.log("Almost weekend!");
    break;
  case "Saturday":
  case "Sunday":
    console.log("It's the weekend!");
    break;
  default:
    console.log("It's a regular day");
}
// Output: "Start of the work week"
```

### Why use `break`?

Without `break`, execution continues to the next case:

```javascript
const fruit = "apple";

switch (fruit) {
  case "apple":
    console.log("Red fruit");
    // Missing break!
  case "banana":
    console.log("Yellow fruit");
    break;
}
// Output: "Red fruit" AND "Yellow fruit" (both run!)
```

### Playwright example

```javascript
// Handle different page states
const status = await page.locator('.status').textContent();

switch (status) {
  case "loading":
    await page.waitForTimeout(2000);
    break;
  case "error":
    console.log("Page error detected");
    throw new Error("Test failed");
  case "success":
    console.log("Page loaded successfully");
    break;
  default:
    console.log("Unknown status");
}
```

## Loops

Loops repeat code multiple times until a condition is met.

### For loop

Best when you know **how many times** to repeat.

```javascript
// Count from 1 to 5
for (let i = 1; i <= 5; i++) {
  console.log("Count: " + i);
}
// Output: Count: 1, Count: 2, Count: 3, Count: 4, Count: 5
```

**Parts of a for loop:**
1. `let i = 1` — starting point
2. `i <= 5` — condition to continue
3. `i++` — what happens after each loop

### Loop through an array

```javascript
const fruits = ["apple", "banana", "orange"];

for (let i = 0; i < fruits.length; i++) {
  console.log(fruits[i]);
}
// Output: apple, banana, orange
```

### Playwright example

```javascript
// Click multiple buttons
for (let i = 1; i <= 3; i++) {
  await page.click(`#button-${i}`);
  console.log(`Clicked button ${i}`);
}
```

## While loop

Best when you **don't know** how many times to repeat — continue until condition is false.

```javascript
let count = 0;

while (count < 3) {
  console.log("Count: " + count);
  count++;
}
// Output: Count: 0, Count: 1, Count: 2
```

### Be careful with infinite loops!

```javascript
// ❌ BAD - This never stops!
let x = 0;
while (x < 5) {
  console.log(x);
  // Forgot to increment x!
}

// ✅ GOOD - This stops
let y = 0;
while (y < 5) {
  console.log(y);
  y++; // Don't forget this!
}
```

### Playwright example

```javascript
// Wait until element appears (with timeout protection)
let attempts = 0;
const maxAttempts = 10;

while (attempts < maxAttempts) {
  const isVisible = await page.locator('#loading').isVisible();
  
  if (!isVisible) {
    break; // Exit loop when loading is gone
  }
  
  await page.waitForTimeout(500);
  attempts++;
}
```

## Do...While loop

Runs **at least once**, then checks the condition.

```javascript
let number = 0;

do {
  console.log("Number: " + number);
  number++;
} while (number < 3);
// Output: Number: 0, Number: 1, Number: 2
```

The difference:

```javascript
// While loop - might not run at all
let x = 10;
while (x < 5) {
  console.log("This never prints");
}

// Do-While - runs at least once
let y = 10;
do {
  console.log("This prints once"); // Runs!
} while (y < 5);
```

## For...of loop

**Best for arrays** — simpler and cleaner than traditional for loops.

```javascript
const colors = ["red", "green", "blue"];

for (const color of colors) {
  console.log(color);
}
// Output: red, green, blue
```

### Comparison with regular for loop

```javascript
// Traditional for loop
const fruits = ["apple", "banana", "orange"];
for (let i = 0; i < fruits.length; i++) {
  console.log(fruits[i]);
}

// For...of loop (easier!)
for (const fruit of fruits) {
  console.log(fruit);
}
```

### Playwright example

```javascript
// Test multiple URLs
const urls = [
  'https://example.com/page1',
  'https://example.com/page2',
  'https://example.com/page3'
];

for (const url of urls) {
  await page.goto(url);
  console.log(`Testing: ${url}`);
  
  const title = await page.title();
  console.log(`Title: ${title}`);
}
```

## For...in loop

**Best for objects** — loop through object keys.

```javascript
const person = {
  name: "Alice",
  age: 25,
  city: "New York"
};

for (const key in person) {
  console.log(`${key}: ${person[key]}`);
}
// Output:
// name: Alice
// age: 25
// city: New York
```

### Playwright example

```javascript
// Test form fields
const formData = {
  username: "testuser",
  email: "test@example.com",
  password: "secret123"
};

for (const field in formData) {
  await page.fill(`#${field}`, formData[field]);
  console.log(`Filled ${field}: ${formData[field]}`);
}
```

## Break and Continue

### Break — exit the loop immediately

```javascript
for (let i = 1; i <= 10; i++) {
  if (i === 5) {
    break; // Stop the loop
  }
  console.log(i);
}
// Output: 1, 2, 3, 4 (stops at 5)
```

### Continue — skip to next iteration

```javascript
for (let i = 1; i <= 5; i++) {
  if (i === 3) {
    continue; // Skip 3
  }
  console.log(i);
}
// Output: 1, 2, 4, 5 (skips 3)
```

### Playwright example

```javascript
// Click only visible buttons
const buttons = await page.locator('button').all();

for (const button of buttons) {
  const isVisible = await button.isVisible();
  
  if (!isVisible) {
    continue; // Skip hidden buttons
  }
  
  await button.click();
  console.log("Clicked visible button");
}
```

## Nested loops

Loops inside loops — useful for 2D data or combinations.

```javascript
// Print a grid
for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 3; col++) {
    console.log(`Row ${row}, Col ${col}`);
  }
}
// Output: Row 1 Col 1, Row 1 Col 2, Row 1 Col 3, Row 2 Col 1...
```

### Playwright example

```javascript
// Test all browser and device combinations
const browsers = ['chromium', 'firefox', 'webkit'];
const devices = ['desktop', 'mobile'];

for (const browser of browsers) {
  for (const device of devices) {
    console.log(`Testing ${browser} on ${device}`);
    // Run tests for this combination
  }
}
```

## Practical Playwright examples

### Example 1: Find and click the first visible button

```javascript
const buttons = await page.locator('button').all();

for (const button of buttons) {
  const isVisible = await button.isVisible();
  
  if (isVisible) {
    await button.click();
    console.log("Clicked the first visible button");
    break; // Stop after first click
  }
}
```

### Example 2: Retry mechanism

```javascript
let success = false;
let attempts = 0;
const maxRetries = 3;

while (!success && attempts < maxRetries) {
  try {
    await page.click('#submit-btn');
    success = true;
    console.log("Click successful");
  } catch (error) {
    attempts++;
    console.log(`Attempt ${attempts} failed, retrying...`);
    await page.waitForTimeout(1000);
  }
}

if (!success) {
  throw new Error(`Failed after ${maxRetries} attempts`);
}
```

### Example 3: Validate multiple form fields

```javascript
const fields = ['username', 'email', 'password'];
let allValid = true;

for (const field of fields) {
  const value = await page.inputValue(`#${field}`);
  
  if (value === '') {
    console.log(`Error: ${field} is empty`);
    allValid = false;
  }
}

if (allValid) {
  console.log("All fields are valid!");
  await page.click('#submit');
} else {
  console.log("Please fill all fields");
}
```

### Example 4: Navigate through pagination

```javascript
let currentPage = 1;
const maxPages = 5;

while (currentPage <= maxPages) {
  console.log(`Scraping page ${currentPage}`);
  
  // Extract data from current page
  const items = await page.locator('.item').count();
  console.log(`Found ${items} items`);
  
  // Go to next page
  const nextButton = page.locator('#next-page');
  const isEnabled = await nextButton.isEnabled();
  
  if (!isEnabled) {
    break; // No more pages
  }
  
  await nextButton.click();
  currentPage++;
}
```

## Best practices

- **Use `for...of`** for arrays (cleaner than traditional for loops)
- **Use `for...in`** for object keys
- **Always include `break`** in switch statements (unless you want fall-through)
- **Avoid infinite loops** — ensure your condition will eventually be false
- **Use meaningful variable names** — `i` is okay for simple loops, but `userIndex` is clearer
- **Keep loops simple** — if a loop is too complex, break it into functions

## Common mistakes

### Forgetting to increment

```javascript
// ❌ Infinite loop!
let i = 0;
while (i < 5) {
  console.log(i);
  // Missing i++
}
```

### Off-by-one errors

```javascript
// ❌ Skips the last element
const arr = [1, 2, 3, 4, 5];
for (let i = 0; i < arr.length - 1; i++) {
  console.log(arr[i]); // Prints 1,2,3,4 (missing 5)
}

// ✅ Correct
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]); // Prints 1,2,3,4,5
}
```

### Using `var` instead of `let`

```javascript
// ❌ Unexpected behavior
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 3, 3, 3 (all print 3!)

// ✅ Correct
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2 (correct!)
```

## Summary

Control flow gives you power to:
- **Make decisions** with `if/else` and `switch`
- **Repeat actions** with `for`, `while`, and `for...of` loops
- **Control execution** with `break` and `continue`

In Playwright testing, you'll use these to:
- Check element states before actions
- Retry failed operations
- Loop through test data
- Handle different scenarios
- Navigate paginated content

Master control flow and you'll be able to write flexible, robust automation scripts!
