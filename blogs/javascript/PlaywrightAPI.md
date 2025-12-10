 # Playwright API Testing: CRUD, HTTP, Headers & Mocking

 Playwright can test APIs using its built-in request context. This section covers:
 - CRUD operations (Create, Read, Update, Delete)
 - HTTP status codes
 - Request & response structure
 - Headers
 - Mocking APIs
 - Nested API calls

 ---

 ## 1. HTTP Codes: What They Mean

 | Code | Meaning                | Typical Use                |
 |------|------------------------|----------------------------|
 | 200  | OK                     | Success (GET, PUT, DELETE) |
 | 201  | Created                | Resource created (POST)    |
 | 204  | No Content             | Success, no body (DELETE)  |
 | 400  | Bad Request            | Invalid input              |
 | 401  | Unauthorized           | Auth required/failed       |
 | 403  | Forbidden              | No permission              |
 | 404  | Not Found              | Resource missing           |
 | 409  | Conflict               | Duplicate, conflict        |
 | 500  | Internal Server Error  | Server crashed             |

 ---

 ## 2. Request & Response Structure

 ### Request
 - **Method:** GET, POST, PUT, DELETE, PATCH
 - **URL:** Endpoint (e.g., `/users/1`)
 - **Headers:** Metadata (e.g., `Content-Type`, `Authorization`)
 - **Body:** Data sent (for POST/PUT/PATCH)

 ### Response
 - **Status code:** Numeric result (see above)
 - **Headers:** Metadata (e.g., `Content-Type`)
 - **Body:** Data returned (usually JSON)

 ---

 ## 3. CRUD Examples with Playwright

 ### Setup: Create Request Context
 ```javascript
 const { request } = require('@playwright/test');

 let context;
 beforeAll(async () => {
   context = await request.newContext({
     baseURL: 'https://api.example.com',
     extraHTTPHeaders: {
       'Authorization': 'Bearer mytoken',
       'Content-Type': 'application/json'
     }
   });
 });
 afterAll(async () => {
   await context.dispose();
 });
 ```

 ### CREATE (POST)
 ```javascript
 const response = await context.post('/users', {
   data: { name: 'Alice', email: 'alice@example.com' }
 });
 expect(response.status()).toBe(201); // Created
 const user = await response.json();
 expect(user.name).toBe('Alice');
 ```

 ### READ (GET)
 ```javascript
 const response = await context.get('/users/1');
 expect(response.status()).toBe(200);
 const user = await response.json();
 expect(user.id).toBe(1);
 ```

 ### UPDATE (PUT)
 ```javascript
 const response = await context.put('/users/1', {
   data: { name: 'Alice Updated' }
 });
 expect(response.status()).toBe(200);
 const updated = await response.json();
 expect(updated.name).toBe('Alice Updated');
 ```

 ### DELETE (DELETE)
 ```javascript
 const response = await context.delete('/users/1');
 expect(response.status()).toBe(204); // No Content
 ```

 ---

 ## 4. Headers: Common Attributes

 | Header            | Purpose                                 |
 |-------------------|-----------------------------------------|
 | Content-Type      | Data format (e.g., application/json)    |
 | Authorization     | Auth info (e.g., Bearer token)          |
 | Accept            | Expected response format                 |
 | User-Agent        | Client info                             |
 | X-Request-ID      | Custom tracking ID                      |

 ### Example: Custom Headers
 ```javascript
 const response = await context.get('/users', {
   headers: {
     'Accept': 'application/json',
     'X-Request-ID': 'abc-123'
   }
 });
 expect(response.status()).toBe(200);
 ```

 ---

 ## 5. Mocking API Responses

 You can intercept and mock API calls in Playwright tests:

 ```javascript
 const { test, expect } = require('@playwright/test');

 test('mock GET /users', async ({ page, context }) => {
   await page.route('**/users', route => {
     route.fulfill({
       status: 200,
       contentType: 'application/json',
       body: JSON.stringify([{ id: 1, name: 'Mock User' }])
     });
   });
   await page.goto('https://myapp.com');
   // ... test UI that uses mocked /users API
 });
 ```

 ---

 ## 6. Nested API Calls Example

 Sometimes you need to chain API calls (e.g., create, then update, then delete):

 ```javascript
 const { test, expect, request } = require('@playwright/test');

 test('nested API calls', async ({ request }) => {
   // Create user
   const createRes = await request.post('https://api.example.com/users', {
     data: { name: 'Nested', email: 'nested@example.com' }
   });
   expect(createRes.status()).toBe(201);
   const user = await createRes.json();

   // Update user
   const updateRes = await request.put(`https://api.example.com/users/${user.id}`, {
     data: { name: 'Nested Updated' }
   });
   expect(updateRes.status()).toBe(200);

   // Delete user
   const deleteRes = await request.delete(`https://api.example.com/users/${user.id}`);
   expect(deleteRes.status()).toBe(204);
 });
 ```

 ---

 ## 7. Best Practices
 - Always check status codes and response bodies
 - Use headers for auth and content negotiation
 - Mock APIs for isolated UI tests
 - Chain/nest API calls for complex flows
 - Log request/response for debugging
