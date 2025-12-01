# Rest Assured with Java 17 and Maven

## Introduction
Rest Assured is a Java library for automating REST API testing. It simplifies HTTP requests, response validation, and integrates seamlessly with JUnit/TestNG. With Java 17 + Maven, you can build scalable API automation frameworks.

## Project structure
Example layout for a small Rest Assured project:

```text
restassured-demo/
├── pom.xml
└── src
    ├── main
    │    └── java
    │         └── com.example.api   # Optional helpers
    └── test
         └── java
              └── com.example.api.tests  # Rest Assured test cases
```

- `pom.xml` — Maven dependencies (Rest Assured, JUnit/TestNG)
- `src/test/java` — Test classes in package `com.example.api.tests`

## Setup

1. Install Java 17 and configure `JAVA_HOME`.
2. Create a Maven project: `mvn archetype:generate` (or use your preferred archetype).
3. Add Rest Assured and JUnit 5 dependencies in `pom.xml`.
4. Use JUnit 5 for modern test execution and lifecycle annotations.

## Core scenarios
Short reference table for common Rest Assured operations:

| # | Scenario | Example
|---:|---|---|
| 1 | GET Request | `given().when().get("/users").then().statusCode(200);` |
| 2 | POST Request | `given().body("{\"name\":\"James\"}").when().post("/users").then().statusCode(201);` |
| 3 | PUT Request | `given().body("{\"job\":\"Architect\"}").when().put("/users/2").then().statusCode(200);` |
| 4 | PATCH Request | `given().body("{\"job\":\"Engineer\"}").when().patch("/users/2").then().statusCode(200);` |
| 5 | DELETE Request | `given().when().delete("/users/2").then().statusCode(204);` |
| 6 | Query Params | `given().queryParam("page", 2).when().get("/users").then().statusCode(200);` |
| 7 | Path Params | `given().pathParam("id", 2).when().get("/users/{id}").then().statusCode(200);` |
| 8 | Headers | `given().header("Content-Type","application/json").when().get("/users").then().statusCode(200);` |
| 9 | Auth (Basic) | `given().auth().basic("user","pass").when().get("/secure").then().statusCode(200);` |
| 10 | Auth (Bearer) | `given().auth().oauth2("token").when().get("/secure").then().statusCode(200);` |
| 11 | Response Extraction | ``String name = get("/users/2").then().extract().path("data.first_name");`` |
| 12 | Request Spec | ``RequestSpecification req = new RequestSpecBuilder().setBaseUri("https://api").build();`` |
| 13 | Response Spec | ``ResponseSpecification res = new ResponseSpecBuilder().expectStatusCode(200).build();`` |
| 14 | Logging | `given().log().all().when().get("/users").then().log().all();` |
| 15 | Negative Testing | `given().when().get("/invalid").then().statusCode(404);` |

## Examples
Simple GET example using static imports from Rest Assured:

```java
import static io.restassured.RestAssured.*;

public class UserTests {
    @Test
    void listUsers() {
        given()
        .when()
            .get("/users")
        .then()
            .statusCode(200);
    }
}
```

Response extraction example:

```java
String name = get("/users/2").then().extract().path("data.first_name");
```

## Complex scenario — API chaining
Common workflow: Login → Get token → Fetch profile → Update profile → Validate → Cleanup

```java
// Login and extract token
String token = given()
    .body("{\"username\":\"jojo\",\"password\":\"secret\"}")
    .post("/login")
    .then()
    .statusCode(200)
    .extract().path("token");

// Use token to fetch profile
given().auth().oauth2(token)
    .get("/profile")
    .then().statusCode(200);

// Update profile
given().auth().oauth2(token)
    .body("{\"job\":\"Architect\"}")
    .put("/profile/2")
    .then().statusCode(200);
```

## Execution

- Run tests from your IDE (right-click the test class → Run).
- Run via Maven (PowerShell / Bash):

```powershell
mvn test
```

For CI, run `mvn -DskipTests=false test` or configure your pipeline to execute the test phase.

## Best practices

- **Atomic tests:** Keep tests focused — one validation per test.
- **Reusable specs:** Centralize base URI, authentication, headers, and common expectations using `RequestSpecBuilder` and `ResponseSpecBuilder`.
- **Organize suites:** Group tests by endpoint or feature for maintainability.
- **Reporting:** Add Allure or Extent Reports for readable test output and historical data.
- **Mock dependencies:** Use WireMock to isolate tests from flaky external services.
- **Negative testing:** Include error/edge-case tests to validate API robustness.

## Conclusion

This page provides a concise Docsify-friendly reference for using Rest Assured with Java 17 and Maven: project layout, setup steps, a quick scenarios table, examples for common flows, and recommended best practices.

If you'd like, I can preview this page locally with `docsify serve` and tweak any rendering issues.