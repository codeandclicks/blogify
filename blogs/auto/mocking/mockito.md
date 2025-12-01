# Mockito Framework (Java 17)

## Introduction

Mockito is the most popular Java mocking framework used in unit testing. It allows developers to create mock objects for dependencies, define their behavior, and verify interactions. This helps isolate the class under test and ensures reliable, repeatable unit tests.

## Key features

- **Mock creation:** create mock objects for interfaces or classes
- **Stubbing behavior:** define what a mock should return when a method is called
- **Verification:** check if methods were called, how many times, and with which arguments
- **Argument matchers:** flexible matching (`anyString()`, `eq("value")`)
- **Annotations:** `@Mock`, `@InjectMocks`, `@Spy` for cleaner test setup
- **Spies:** partial mocks that wrap real objects, allowing real method calls with selective stubbing
- **Exception throwing:** simulate error conditions with `thenThrow()`
- **BDD style:** support for given/when/then syntax for behavior-driven tests

## Setup

### Add dependency (Maven)

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.11.0</version>
    <scope>test</scope>
</dependency>
```

### Create a mock

```java
UserRepository mockRepo = Mockito.mock(UserRepository.class);
```

### Stub behavior

```java
when(mockRepo.findById("123"))
    .thenReturn(Optional.of(new User("123", "Mocked User")));
```

### Verify interactions

```java
verify(mockRepo, times(1)).findById("123");
```

## Examples

### Example 1: simple mock

```java
@Test
void testGetUserName() {
    UserRepository mockRepo = Mockito.mock(UserRepository.class);

    when(mockRepo.findById("123"))
        .thenReturn(Optional.of(new User("123", "Jojo")));

    UserService service = new UserService(mockRepo);

    String result = service.getUserName("123");

    assertEquals("Jojo", result);
    verify(mockRepo).findById("123");
}
```

**Explanation:**
- `mockRepo` simulates the repository
- Behavior is stubbed (`thenReturn`)
- Interaction is verified (`verify`)

### Example 2: using annotations

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    PaymentGateway paymentGateway;

    @Mock
    NotificationService notificationService;

    @InjectMocks
    OrderService orderService;

    @Test
    void testPlaceOrder() {
        when(paymentGateway.processPayment(100.0)).thenReturn("SUCCESS");

        orderService.placeOrder(100.0);

        verify(paymentGateway).processPayment(100.0);
        verify(notificationService).sendConfirmation(anyString());
    }
}
```

**Explanation:**
- `@Mock` creates mocks automatically
- `@InjectMocks` injects them into `OrderService`
- Cleaner setup compared to manual mock creation

### Example 3: throwing exceptions

```java
@Test
void testPaymentFailure() {
    PaymentGateway mockGateway = Mockito.mock(PaymentGateway.class);

    when(mockGateway.processPayment(200.0))
        .thenThrow(new RuntimeException("Payment failed"));

    OrderService service = new OrderService(mockGateway, null);

    assertThrows(RuntimeException.class, () -> service.placeOrder(200.0));
}
```

**Explanation:**
- Simulates a failure scenario
- Useful for testing error handling logic

## Benefits

- **Isolation:** test classes without real dependencies
- **Flexibility:** simulate success, failure, and edge cases
- **Verification:** ensure correct collaboration between objects
- **Readability:** fluent API makes tests easy to understand
- **Integration:** works seamlessly with JUnit 5 and TestNG

## Conclusion

Mockito is a powerful mocking framework for Java 17. Use it to create mocks, stub behavior, and verify interactions. It helps write clean, isolated, and reliable unit tests.

