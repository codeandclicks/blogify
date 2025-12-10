# Objects and Classes

Objects and classes are the foundation of organizing code in JavaScript. They help you group related data and functions together — essential for building maintainable Playwright test frameworks.

## Objects: Grouping Data

Think of an object like a real-world object — a car has properties (color, brand) and actions (start, stop).

### Creating Objects

#### Object Literal (Most Common)

```javascript
const user = {
  name: 'John Doe',
  email: 'john@example.com',
  age: 30,
  isActive: true
};

console.log(user.name);   // John Doe
console.log(user.email);  // john@example.com
```

#### Accessing Properties

```javascript
const product = {
  id: 101,
  name: 'Laptop',
  price: 999.99
};

// Dot notation
console.log(product.name);  // Laptop

// Bracket notation (useful for dynamic keys)
console.log(product['price']);  // 999.99

const key = 'id';
console.log(product[key]);  // 101
```

#### Adding and Modifying Properties

```javascript
const person = {
  name: 'Alice'
};

// Add new property
person.age = 25;
person.email = 'alice@example.com';

// Modify existing property
person.name = 'Alice Smith';

console.log(person);
// { name: 'Alice Smith', age: 25, email: 'alice@example.com' }
```

#### Deleting Properties

```javascript
const user = {
  name: 'Bob',
  password: 'secret123',
  email: 'bob@example.com'
};

delete user.password;

console.log(user);
// { name: 'Bob', email: 'bob@example.com' }
```

### Object Methods

Objects can contain functions (called methods).

```javascript
const calculator = {
  add: function(a, b) {
    return a + b;
  },
  // Shorthand syntax (ES6)
  subtract(a, b) {
    return a - b;
  },
  multiply: (a, b) => a * b
};

console.log(calculator.add(5, 3));       // 8
console.log(calculator.subtract(10, 4)); // 6
console.log(calculator.multiply(3, 7));  // 21
```

### The `this` Keyword

`this` refers to the object the method belongs to.

```javascript
const user = {
  firstName: 'John',
  lastName: 'Doe',
  getFullName() {
    return `${this.firstName} ${this.lastName}`;
  }
};

console.log(user.getFullName());  // John Doe
```

**Warning:** Arrow functions don't have their own `this`!

```javascript
const person = {
  name: 'Alice',
  // Regular function - 'this' works
  greet1() {
    return `Hello, ${this.name}`;
  },
  // Arrow function - 'this' doesn't work as expected
  greet2: () => {
    return `Hello, ${this.name}`;  // 'this' is undefined!
  }
};

console.log(person.greet1());  // Hello, Alice
console.log(person.greet2());  // Hello, undefined
```

## Classes: Object Blueprints

Classes are templates for creating objects with shared structure and behavior.

### Basic Class

```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  greet() {
    return `Hello, I'm ${this.name}`;
  }

  getEmail() {
    return this.email;
  }
}

// Create instances
const user1 = new User('Alice', 'alice@example.com');
const user2 = new User('Bob', 'bob@example.com');

console.log(user1.greet());     // Hello, I'm Alice
console.log(user2.getEmail());  // bob@example.com
```

### Constructor

The `constructor` method initializes object properties when you create an instance.

```javascript
class Product {
  constructor(name, price, quantity = 1) {
    this.name = name;
    this.price = price;
    this.quantity = quantity;
  }

  getTotalPrice() {
    return this.price * this.quantity;
  }
}

const product = new Product('Laptop', 999.99, 2);
console.log(product.getTotalPrice());  // 1999.98
```

### Class Methods

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }

  getPerimeter() {
    return 2 * (this.width + this.height);
  }

  isSquare() {
    return this.width === this.height;
  }
}

const rect = new Rectangle(10, 20);
console.log(rect.getArea());      // 200
console.log(rect.getPerimeter()); // 60
console.log(rect.isSquare());     // false
```

### Getters and Setters

Control how properties are accessed and modified.

```javascript
class User {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // Getter - access like a property
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // Setter - set like a property
  set fullName(name) {
    const [first, last] = name.split(' ');
    this.firstName = first;
    this.lastName = last;
  }
}

const user = new User('John', 'Doe');
console.log(user.fullName);  // John Doe (calls getter)

user.fullName = 'Jane Smith';  // Calls setter
console.log(user.firstName);   // Jane
console.log(user.lastName);    // Smith
```

### Static Methods

Methods that belong to the class, not instances.

```javascript
class MathUtils {
  static add(a, b) {
    return a + b;
  }

  static multiply(a, b) {
    return a * b;
  }

  static PI = 3.14159;
}

// Call on class, not instance
console.log(MathUtils.add(5, 3));     // 8
console.log(MathUtils.multiply(4, 7)); // 28
console.log(MathUtils.PI);             // 3.14159

// Can't call on instance
const util = new MathUtils();
// util.add(5, 3);  // Error!
```

### Inheritance

Classes can inherit from other classes.

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a sound`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);  // Call parent constructor
    this.breed = breed;
  }

  speak() {
    return `${this.name} barks!`;
  }

  fetch() {
    return `${this.name} fetches the ball`;
  }
}

const dog = new Dog('Buddy', 'Golden Retriever');
console.log(dog.speak());   // Buddy barks!
console.log(dog.fetch());   // Buddy fetches the ball
console.log(dog.breed);     // Golden Retriever
```

## Classes in Playwright: Page Object Model

### Example 1: Basic Page Object

```javascript
class LoginPage {
  constructor(page) {
    this.page = page;
    // Locators
    this.usernameInput = '#username';
    this.passwordInput = '#password';
    this.loginButton = 'button[type="submit"]';
    this.errorMessage = '.error-message';
  }

  async navigate() {
    await this.page.goto('https://example.com/login');
  }

  async login(username, password) {
    await this.page.fill(this.usernameInput, username);
    await this.page.fill(this.passwordInput, password);
    await this.page.click(this.loginButton);
  }

  async getErrorMessage() {
    return await this.page.textContent(this.errorMessage);
  }

  async isLoginButtonVisible() {
    return await this.page.isVisible(this.loginButton);
  }
}

// Usage in test
const loginPage = new LoginPage(page);
await loginPage.navigate();
await loginPage.login('testuser', 'password123');
```

### Example 2: Page Object with Getters

```javascript
class HomePage {
  constructor(page) {
    this.page = page;
  }

  // Getters for locators
  get searchBox() {
    return this.page.locator('#search-input');
  }

  get searchButton() {
    return this.page.locator('button[type="submit"]');
  }

  get productCards() {
    return this.page.locator('.product-card');
  }

  async search(query) {
    await this.searchBox.fill(query);
    await this.searchButton.click();
  }

  async getProductCount() {
    return await this.productCards.count();
  }

  async getProductNames() {
    const cards = await this.productCards.all();
    const names = [];
    for (const card of cards) {
      names.push(await card.textContent('.product-name'));
    }
    return names;
  }
}
```

### Example 3: Inheritance in Page Objects

```javascript
// Base page with common functionality
class BasePage {
  constructor(page) {
    this.page = page;
  }

  async navigate(url) {
    await this.page.goto(url);
  }

  async getTitle() {
    return await this.page.title();
  }

  async waitForElement(selector, timeout = 5000) {
    await this.page.waitForSelector(selector, { timeout });
  }

  async takeScreenshot(name) {
    await this.page.screenshot({ path: `${name}.png` });
  }
}

// Login page extends base page
class LoginPage extends BasePage {
  constructor(page) {
    super(page);
    this.url = 'https://example.com/login';
  }

  async navigateToLogin() {
    await this.navigate(this.url);
  }

  async login(username, password) {
    await this.page.fill('#username', username);
    await this.page.fill('#password', password);
    await this.page.click('#login-button');
  }
}

// Dashboard page extends base page
class DashboardPage extends BasePage {
  constructor(page) {
    super(page);
    this.url = 'https://example.com/dashboard';
  }

  async navigateToDashboard() {
    await this.navigate(this.url);
  }

  async getWelcomeMessage() {
    return await this.page.textContent('.welcome-message');
  }
}

// Usage
const loginPage = new LoginPage(page);
await loginPage.navigateToLogin();
await loginPage.login('user', 'pass');
await loginPage.takeScreenshot('after-login');  // Inherited method
```

### Example 4: Component Objects

```javascript
// Reusable component
class SearchBar {
  constructor(page) {
    this.page = page;
    this.input = '#search-input';
    this.button = '#search-button';
  }

  async search(query) {
    await this.page.fill(this.input, query);
    await this.page.click(this.button);
  }

  async clearSearch() {
    await this.page.fill(this.input, '');
  }
}

// Page using the component
class ProductPage {
  constructor(page) {
    this.page = page;
    this.searchBar = new SearchBar(page);  // Composition
  }

  async searchForProduct(name) {
    await this.searchBar.search(name);
    await this.page.waitForSelector('.search-results');
  }

  async getResults() {
    return await this.page.locator('.product-card').all();
  }
}

// Usage
const productPage = new ProductPage(page);
await productPage.searchForProduct('laptop');
```

### Example 5: Test Data Class

```javascript
class TestUser {
  constructor(name, email, password) {
    this.name = name;
    this.email = email;
    this.password = password;
  }

  static createDefault() {
    return new TestUser(
      'Test User',
      `test${Date.now()}@example.com`,
      'Test@123'
    );
  }

  static createAdmin() {
    return new TestUser(
      'Admin User',
      'admin@example.com',
      'Admin@123'
    );
  }

  getCredentials() {
    return {
      email: this.email,
      password: this.password
    };
  }
}

// Usage
const user1 = TestUser.createDefault();
const user2 = TestUser.createAdmin();

await loginPage.login(user1.email, user1.password);
```

## Object Comparison Gotchas

```javascript
const obj1 = { name: 'Alice' };
const obj2 = { name: 'Alice' };
const obj3 = obj1;

console.log(obj1 === obj2);  // false (different references)
console.log(obj1 === obj3);  // true (same reference)

// To compare values, use JSON.stringify or libraries like lodash
console.log(JSON.stringify(obj1) === JSON.stringify(obj2));  // true
```

## Best Practices

✅ **Use classes for Page Objects** - Organize test code cleanly

✅ **One page = one class** - LoginPage, HomePage, CheckoutPage

✅ **Store locators as properties** - Easy to update in one place

✅ **Use inheritance for shared functionality** - BasePage for common methods

✅ **Use getters for dynamic locators** - Computed on access

✅ **Keep classes focused** - Single responsibility principle

✅ **Use static methods for utilities** - MathUtils.add(), TestData.generate()

## Common Mistakes to Avoid

### ❌ Forgetting `new` keyword

```javascript
class User {
  constructor(name) {
    this.name = name;
  }
}

// Wrong
const user = User('Alice');  // Error or unexpected behavior

// Correct
const user = new User('Alice');
```

### ❌ Forgetting `this` keyword

```javascript
class Counter {
  constructor() {
    this.count = 0;  // Correct
  }

  increment() {
    count++;  // Wrong! Should be this.count++
  }
}
```

### ❌ Using arrow functions for methods with `this`

```javascript
class User {
  constructor(name) {
    this.name = name;
  }

  // Wrong - arrow function doesn't bind 'this'
  greet = () => {
    return `Hello, ${this.name}`;  // May not work as expected
  }

  // Correct
  greet() {
    return `Hello, ${this.name}`;
  }
}
```

## Quick Reference

```javascript
// Object literal
const obj = { key: 'value' };

// Access properties
obj.key
obj['key']

// Class definition
class MyClass {
  constructor(param) {
    this.property = param;
  }

  method() {
    return this.property;
  }

  static staticMethod() {
    return 'static';
  }
}

// Create instance
const instance = new MyClass('value');

// Inheritance
class Child extends Parent {
  constructor(param) {
    super(param);
  }
}
```

## Next Steps

Now that you understand objects and classes:
- **Build Page Object Models** for your Playwright tests
- **Create reusable component classes** for common UI elements
- **Organize test data** with classes
- **Use inheritance** to share common functionality

Classes make your Playwright test framework clean, maintainable, and scalable! 🏗️
