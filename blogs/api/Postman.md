# Postman — Step-by-step guide for API requests

## Introduction

Postman is an API platform used to design, test, and automate APIs. It supports HTTP, GraphQL, gRPC, WebSocket, and SOAP. This guide walks through creating requests (GET/POST), sending JSON/XML payloads, using query strings and form data, setting environments, parameter substitution, pre-request and test scripts, and running collections (UI or CLI).

## Workspaces, collections, and requests

### Create a workspace

- Navigate: Workspaces → Create Workspace.
- Configure: name, visibility (Personal/Team), purpose.
- Use workspaces to organize collections, environments, and monitors.

### Create a collection

- Action: New → Collection.
- Structure: Add folders to group related endpoints.
- Benefits: Shareable, runnable as a suite, versionable.

### Create a request

- Action: Inside a collection → Add Request.
- Configure: method (GET/POST/etc), URL, headers, body, auth.
- Save with a clear name and description for context.

## Sending GET and POST requests

### GET (retrieve data)

- Select method: `GET`.
- Enter URL, e.g. `https://api.example.com/users`.
- Add query params in the `Params` tab — Postman builds the `?key=value` string.
- Headers: optional (e.g., `Accept: application/json`).
- Send: click `Send` and inspect Status, Time, Size, Body.

### POST (create data)

- Select method: `POST`.
- Enter URL, e.g. `https://api.example.com/users`.
- Headers: set `Content-Type` to match the body (e.g., `application/json`).
- Body: choose `Body` tab → format (raw JSON, XML, form-data, etc.) → fill content.
- Send: click `Send` and verify status (e.g., `201 Created`), `Location` header, and response body.

## JSON, XML, query strings, and form data

### JSON payloads

- Body: `Body` → `raw` → `JSON`.
- Header: `Content-Type: application/json`.
- Tip: Use the Beautify button to format JSON and avoid syntax errors.

```json
{
  "name": "Jojo",
  "role": "Architect",
  "active": true
}
```

### XML payloads

- Body: `Body` → `raw` → `XML`.
- Header: `Content-Type: application/xml` or `text/xml`.

```xml
<user>
  <name>Jojo</name>
  <role>Architect</role>
  <active>true</active>
</user>
```

### Query string parameters

- Use the `Params` tab; Postman will append `?key=value&key2=value2` to the URL.
- Common uses: pagination (`page`, `limit`), filtering (`status`), sorting (`order`).

### Form data

- `multipart/form-data` (Body → `form-data`): 
-   use for file uploads and mixed text fields 
-   Postman sets boundaries automatically.
- `x-www-form-urlencoded` (Body → `x-www-form-urlencoded`): 
-   traditional web forms; server expects URL-encoded pairs
-   Header: `Content-Type: application/x-www-form-urlencoded`.

## Environments and variable substitution

### Create environments

- Action: Environments → Add → name (e.g., Dev/Staging/Prod).
- Add variables: `baseUrl`, `token`, `userId`, etc.
- Switch environment using the dropdown in the app to change context.

### Variable scopes

- **Global:** available everywhere (use sparingly).
- **Environment:** scoped per environment (recommended).
- **Collection:** scoped to a collection (useful for shared defaults).
- **Local:** temporary within scripts.

### Referencing variables

Use Handlebars syntax: `{{variableName}}`.

Examples:

- URL: `{{baseUrl}}/users/{{userId}}`
- Header: `Authorization: Bearer {{token}}`

### Initial vs Current values

- **Initial value:** synced for team/shared use and acts as a default.
- **Current value:** local-only — use for secrets during development.

## Pre-request and test (post-request) scripts

### Pre-request scripts (run before sending)

Purpose: compute dynamic headers, timestamps, HMAC signatures, or refresh tokens.

API: use the `pm` object (`pm.request`, `pm.environment`, `pm.variables`, `pm.sendRequest`).

Example — generate a timestamp and set an auth header:

```javascript
const ts = Date.now();
pm.environment.set("timestamp", ts);
pm.request.headers.add({ key: "X-Timestamp", value: String(ts) });
```

Example — refresh token if missing:

```javascript
if (!pm.environment.get("token")) {
  pm.sendRequest({
    url: pm.environment.get("authUrl"),
    method: "POST",
    header: { "Content-Type": "application/json" },
    body: { mode: "raw", raw: JSON.stringify({ user: "jojo", pass: "secret" }) }
  }, (err, res) => {
    if (!err) pm.environment.set("token", res.json().token);
  });
}
```

### Test scripts (run after receiving)

Purpose: assertions, response parsing, and variable extraction.

API: `pm.response`, `pm.expect`, `pm.test`, `pm.environment.set`.

Example — validate status and a JSON field:

```javascript
pm.test("Status is 200", () => pm.response.code === 200);
pm.test("Has name field", () => pm.response.json().name === "Jojo");
```

Example — extract value into an environment variable:

```javascript
const id = pm.response.json().id;
pm.environment.set("userId", id);
```

Example — validate XML (simple contains check):

```javascript
const xmlText = pm.response.text();
pm.test("Contains role tag", () => xmlText.includes("<role>Architect</role>"));
```

## Running collections and data-driven runs

### Collection Runner (UI)

- Open: Collection → Run.
- Configure: environment, iterations, delay, data file (CSV/JSON).
- Data file keys map to `{{variableName}}` used in requests.
- Results: per-iteration logs, failed tests summary.

### Newman (CLI) for CI/CD

- Install: `npm install -g newman` (or use `npx newman`).
- Run example:

```powershell
newman run collection.json -e environment.json -d data.csv -r cli,html
```

- Integrate in CI: GitHub Actions, Jenkins, Azure DevOps — use Newman exit codes to fail builds on test failures.

## Scheduling and monitoring

- **Monitors:** run collections on a schedule for uptime or contract checks.
- **Alerts:** configure notifications on failures (email, Slack, etc.).

## Feature tiers — practical comparison

| Tier | Collaboration | Automation & Scale | Governance & Security | Typical fit |
|---|---|---|---|---|
| Free | Personal workspaces, export/import | Collection Runner, basic monitors, limited mocks | Basic roles, limited recovery | Individuals, small experiments |
| Basic / Professional | Team workspaces, role-based invites, versioning | Higher monitor/mock quotas, better reporting | Role-based access control, longer history | Small–mid teams needing collaboration |
| Enterprise | Organization-wide workspaces, partner sharing | Advanced quotas, SSO, audit logs | Compliance, auditability, private networks, advanced RBAC | Large orgs with security & compliance needs |

## Common patterns and tips

- **Environment strategy:** Keep Dev/Staging/Prod environments with the same variable names; switch environments rather than editing URLs.
- **Parameter hygiene:** Use variables for URLs, headers, and bodies to avoid hardcoding.
- **Secrets management:** Use current values or Postman Vault; avoid committing secrets to version-controlled exports.
- **Assertions first:** Add tests for status, schema, and critical fields — fail fast.
- **Modular collections:** Group requests by feature and add setup/teardown requests for repeatable runs.

## End-to-end example flow (conceptual)

1. Setup environment: set `baseUrl`, `authUrl`, `token`.
2. Pre-request script: if `{{token}}` missing, request a token and set it.
3. POST create user (JSON): `POST {{baseUrl}}/users` — test extracts `{{userId}}`.
4. GET user (JSON): `GET {{baseUrl}}/users/{{userId}}` — assert name.
5. POST update user (XML): `POST {{baseUrl}}/users/{{userId}}/profile` — assert response contains `<role>`.
6. GET list with query: `GET {{baseUrl}}/users?page={{page}}&limit={{limit}}` — assert pagination.
7. POST form submission: `POST {{baseUrl}}/forms` with `x-www-form-urlencoded` — assert `200`.
8. Run collection with data: use CSV columns `name,role,page,limit` for multiple iterations.

## Conclusion

With environments, variables, scripts, and collections, Postman enables robust, repeatable API workflows. Use JSON and XML bodies as required, apply query strings and form data thoughtfully, and automate via the Collection Runner or Newman to bring your API tests into CI/CD.
