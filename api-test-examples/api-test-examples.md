# Apidog API Test Examples: GET, POST, and PUT

This guide shows how API tests could be organized in Apidog for a **hypothetical** resource service. Replace endpoint paths, credentials, fields, and expected results with values from an API you are authorized to test and its contract.

These are test examples and design guidance. They do not claim that these requests have been run against a real service.

## Example API assumptions

The sample service has these endpoints:

| Method | Example path | Purpose |
| --- | --- | --- |
| `GET` | `/api/v1/resources/{resourceId}` | Retrieve one resource |
| `POST` | `/api/v1/resources` | Create a resource |
| `PUT` | `/api/v1/resources/{resourceId}` | Replace or update a resource |

Assumed example response shape:

```json
{
  "id": "resource-123",
  "name": "Sample resource",
  "status": "active",
  "createdAt": "2026-01-15T10:30:00Z"
}
```

The actual API may use different status codes, required fields, update semantics, or response shapes. Confirm these in its contract before writing assertions.

## Suggested Apidog setup

1. Import or define the API contract in an Apidog project.
2. Create an environment for an authorized test system, with a variable such as `baseUrl`.
3. Store tokens and other secrets in the environment’s secret fields; do not commit them in Markdown or shared screenshots.
4. Define reusable test data such as a known `resourceId`, valid request body, and authorized test credentials.
5. For each request, add assertions for the expected status, headers, response content, and schema where supported by the project setup.
6. Keep positive, negative, boundary, and authorization scenarios as separate cases so failures are clear.

Example URL pattern:

```text
{{baseUrl}}/api/v1/resources/{{resourceId}}
```

Use Apidog’s configured authentication mechanism for the project (for example, bearer token if required by the API). Avoid copying real tokens into request examples.

## Authentication checks to reuse

Apply appropriate authentication checks to each protected endpoint (`GET`, `POST`, and `PUT`):

| Scenario | Setup | Expected result |
| --- | --- | --- |
| Valid credentials | Use an authorized test account/token | Request is authenticated; endpoint follows its documented permission rules. |
| Missing credentials | Remove the authorization value | Access is denied using the contract-defined status and error response; no protected data or state change occurs. |
| Invalid or expired credentials | Use a designated invalid/expired test token, if available | Access is denied; response does not reveal token details or internal implementation information. |
| Insufficient permissions | Use an account with a lower permitted role, if available | Restricted data or action is denied according to the authorization contract. |

Do not guess credentials or test against systems outside the authorized scope. Authentication confirms identity; authorization determines which resources and actions that identity may use.

## GET: retrieve a resource

### Positive case: existing resource

**Request**

```http
GET {{baseUrl}}/api/v1/resources/{{resourceId}}
Accept: application/json
Authorization: Bearer {{accessToken}}
```

**Apidog checks**

- Status equals the documented success status for a read.
- `Content-Type` indicates JSON when JSON is expected.
- Response body parses as JSON.
- `id` exists, is a non-empty string (if that is the contract), and equals the requested resource ID.
- Required fields such as `name` and `status` have the documented types and allowed values.
- `createdAt`, if present, follows the documented date-time format.
- The response contains no fields that the caller is not allowed to see.

### Negative and authorization cases

- Unknown `resourceId`: expect the contract-defined not-found behavior and error shape.
- Missing, invalid, or expired token: expect access denial; verify no resource data is returned.
- Authenticated user without access to this resource: verify object-level authorization behavior using approved test accounts and test data.
- Malformed identifier: expect documented validation behavior, without an unhandled server error.

## POST: create a resource

### Positive case: valid request body

**Request**

```http
POST {{baseUrl}}/api/v1/resources
Content-Type: application/json
Accept: application/json
Authorization: Bearer {{accessToken}}
```

```json
{
  "name": "Sample resource",
  "status": "active"
}
```

**Apidog checks**

- Status equals the documented creation status.
- Response content type is JSON when expected.
- Response body matches the create-response schema.
- A created identifier is returned if the contract specifies one.
- Returned `name` and `status` match the request or documented server-side behavior.
- Server-generated fields, such as `createdAt`, have valid formats and expected presence.
- If the API supports reading the new resource, a follow-up GET confirms it exists.

**Test data note:** use a unique or safely reusable name if duplicates are constrained. Record how to clean up created data where cleanup is supported.

### Negative and boundary cases

- Omit a required field (for example, `name`): expect a documented client validation response; verify no resource was created.
- Send a field with the wrong JSON type (for example, number instead of string): expect a clear validation response.
- Send an unsupported `status`: expect rejection or documented normalization.
- Send an empty string, whitespace-only value, and values at/around length limits defined by the contract.
- Send malformed JSON: expect safe rejection with no internal stack trace.
- Missing, invalid, or insufficient credentials: expect denial and no creation side effect.

## PUT: update a resource

Confirm whether the endpoint uses full replacement semantics or permits partial updates. Some APIs use `PATCH` for partial updates; follow the actual contract.

### Positive case: update an existing resource

**Request**

```http
PUT {{baseUrl}}/api/v1/resources/{{resourceId}}
Content-Type: application/json
Accept: application/json
Authorization: Bearer {{accessToken}}
```

```json
{
  "name": "Updated sample resource",
  "status": "inactive"
}
```

**Apidog checks**

- Status equals the documented update status.
- Response matches the documented update-response schema (or body behavior, if no content is returned).
- Updated fields match the request or contract-defined normalization.
- The resource identifier remains correct.
- A follow-up GET confirms the persisted state when supported.
- Unrelated fields behave according to the endpoint’s documented PUT semantics.

### Negative and authorization cases

- Unknown `resourceId`: expect documented not-found behavior; do not create a resource unless upsert is explicitly specified.
- Missing or invalid credentials: expect denial and verify that the resource remains unchanged.
- Authenticated user without permission to update this resource: expect authorization denial; verify no state change.
- Required field omitted or wrong type: expect validation failure and no partial update unless explicitly documented.
- Boundary values for editable fields: validate minimum, maximum, empty, and just-outside values from the contract.

## JSON schema and response validation

Use the schema defined by the API contract where available. A simplified illustrative schema for the sample resource might be:

```json
{
  "type": "object",
  "required": ["id", "name", "status"],
  "properties": {
    "id": { "type": "string", "minLength": 1 },
    "name": { "type": "string", "minLength": 1 },
    "status": { "type": "string", "enum": ["active", "inactive"] },
    "createdAt": { "type": "string", "format": "date-time" }
  }
}
```

This schema is only an example. Match required fields, nullability, formats, enums, and additional-property rules to the real contract.

In Apidog, link or configure the response schema for the relevant status response, then validate that:

- The body is valid JSON.
- Required fields are present.
- Field types, formats, lengths, and enumerated values conform to the schema.
- Null and missing values are handled according to the contract.
- Error responses are checked against their own error schema, not the success schema.

Schema validation complements behavioral assertions. Also verify specific values and relationships—for example, that the returned `id` matches the requested identifier and that an update changed the intended fields.

## Additional validation checklist

For each request, select checks relevant to the contract:

- **HTTP:** expected status and response time threshold only if the team has defined one.
- **Headers:** JSON content type and any documented cache, pagination, or correlation headers.
- **Body:** valid JSON, schema conformance, required fields, allowed values, and meaningful error structure.
- **Data:** request/response consistency, persistence after writes, and no unexpected side effects after rejected requests.
- **Security basics:** authentication and role/object access checks with authorized test accounts; no secrets or unrelated sensitive fields in responses.
- **Environment:** correct base URL and isolated test data; credentials stored as secrets and excluded from source control.

Avoid asserting exact values for intentionally variable fields (for example, timestamps) unless the test is specifically checking their format or range.

## Suggested Apidog test suite organization

```text
Resources
├── GET resource
│   ├── existing resource with valid access
│   ├── unknown resource ID
│   ├── missing or invalid authentication
│   └── insufficient resource access
├── POST resource
│   ├── valid body
│   ├── missing required field
│   ├── wrong field type or invalid value
│   ├── boundary values
│   └── missing or insufficient authentication
└── PUT resource
    ├── valid update
    ├── unknown resource ID
    ├── invalid body or boundary values
    └── missing or insufficient authentication
```

Keep the API contract, test account permissions, exact status codes, and cleanup rules authoritative. The examples above are a starting point for test design, not universal API requirements.
