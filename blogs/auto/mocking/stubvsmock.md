# Stubs vs Mocks in Java (Java 17 + Mockito)

## Introduction

When writing unit tests, we often need to isolate the code under test from external dependencies such as databases, APIs, or messaging systems.

- **Stub:** provides predefined responses
- **Mock:** simulates behavior and allows verification of interactions

Both are test doubles, but they serve different purposes.

## What is a stub?

**Definition:** A stub is a lightweight object that provides predefined responses to method calls.

**Purpose:** It isolates the code under test by replacing dependencies with simple, static behavior.

**Characteristics:**
- Returns hard-coded values
- Does not validate how methods are called
- Simplest form of test double

**Use cases:**
- When testing logic that depends on external systems (e.g., APIs, databases)
- Example: returning a fixed user object instead of querying a real database

## What is a mock?

**Definition:** A mock is a dynamic test double that can simulate behavior and verify interactions.

**Purpose:** It checks whether the code under test calls its dependencies correctly.

**Characteristics:**
- Can be programmed to return values or throw exceptions
- Records method calls for verification
- Often created using frameworks like Mockito or EasyMock

**Use cases:**
- When you need to validate behavior (e.g., was `save()` called once?)
- Example: ensuring a service calls `repository.save()` after processing data


## Real-world scenario

Imagine we are building an e-commerce application.

The `OrderService` depends on a `PaymentGateway` and a `NotificationService`.

In tests, we don't want to hit real payment APIs or send real emails. We use stubs for predictable data and mocks to verify interactions.

## Stub example (Java 17)

Stubbed `PaymentGateway` always returns `"SUCCESS"`:

```java
class StubPaymentGateway implements PaymentGateway {
    @Override
    public String processPayment(double amount) {
        return "SUCCESS"; // fixed response
    }
}

@Test
void testOrderPlacementWithStub() {
    PaymentGateway stubGateway = new StubPaymentGateway();
    OrderService service = new OrderService(stubGateway, null);

    String result = service.placeOrder(100.0);

    assertEquals("Order placed with payment SUCCESS", result);
}
```

### Explanation

- Stub returns a fixed `"SUCCESS"` response
- We only care about the output of `OrderService`
- No verification of how `processPayment()` was called

## Mock example (Java 17 + Mockito)

```java
@Test
void testOrderPlacementWithMock() {
    // Create mocks
    PaymentGateway mockGateway = Mockito.mock(PaymentGateway.class);
    NotificationService mockNotification = Mockito.mock(NotificationService.class);

    // Define behavior
    when(mockGateway.processPayment(100.0)).thenReturn("SUCCESS");

    OrderService service = new OrderService(mockGateway, mockNotification);

    // Call method under test
    service.placeOrder(100.0);

    // Verify interactions
    verify(mockGateway, times(1)).processPayment(100.0);
    verify(mockNotification, times(1)).sendConfirmation(anyString());
}
```

### Explanation

- Mock created with `Mockito.mock()`
- Behavior defined using `when(...).thenReturn(...)`
- After execution, we verify that:
  - `processPayment()` was called once
  - `sendConfirmation()` was triggered
- This ensures correct collaboration between objects

## Stub vs Mock comparison

| Aspect | Stub | Mock (Mockito) |
|---|---|---|
| Purpose | Provide fixed responses | Simulate + verify interactions |
| Complexity | Simple | More powerful |
| Verification | None | Yes (method calls, arguments) |
| Use case | Isolate logic from external dependencies | Validate collaboration between objects |

## Mockito features

- **Mock creation:** `Mockito.mock(Class.class)`
- **Behavior stubbing:** `when(...).thenReturn(...)`
- **Verification:** `verify(mock).method(...)`
- **Argument matchers:** `anyString()`, `eq("value")`
- **Annotations:** `@Mock`, `@InjectMocks` for cleaner setup
- **Spies:** partial mocks that wrap real objects

## Conclusion

- Use stubs when you only need predictable outputs (e.g., payment always succeeds)
- Use mocks when you want to verify interactions (e.g., confirmation email is sent)
- Together, they help you write isolated, reliable unit tests in Java 17