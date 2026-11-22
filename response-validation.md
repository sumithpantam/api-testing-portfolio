# API Response Validation Examples

The following checks are generic. Expected values must come from the API contract and scenario data.

## HTTP status

Check that the status matches the outcome being tested. For example, a successful read, resource creation, invalid request, missing resource, or denied request may each use different contract-defined status codes. Do not assume every API uses identical conventions.

## Headers

- Check `Content-Type` when the endpoint is expected to return JSON.
- Check pagination or caching headers only when they are part of the contract.
- Do not log authorization headers or cookies in shared reports.

## JSON body and schema

Example shape checks for a hypothetical resource response:

```json
{
  "id": "resource-123",
  "name": "Example",
  "active": true
}
```

Possible assertions:

- Body parses as JSON.
- `id` is present, non-empty, and has the documented type.
- `name` is a string and follows documented length rules.
- `active` is a boolean.
- Required fields and nullability match the contract.
- No unexpected sensitive fields are returned.

## Data and behavior

Compare returned values with the request and test data where appropriate. For state-changing operations, verify the resulting state through an authorized read or supported cleanup workflow. Avoid brittle assertions on fields that are intentionally generated, such as timestamps, unless their format or range is under test.
