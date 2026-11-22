# API Test Strategy

## Purpose

Describe a repeatable, risk-aware approach to testing REST APIs. This strategy is a portfolio example and should be adapted to the service, its contract, and the team's release process.

## Scope

For each endpoint in scope, consider:

- Request method, path, query parameters, headers, and body
- Authentication and authorization behavior, where applicable
- HTTP status and response headers
- Response body fields, data types, constraints, and relationships
- Error response consistency and useful validation messages
- Test data setup, cleanup, isolation, and environment configuration
- Regression impact on related endpoints and workflows

## Test levels and coverage

| Area | Questions to answer |
| --- | --- |
| Positive | Does a valid request produce the documented outcome? |
| Negative | Are missing, malformed, invalid, or unauthorized requests handled safely? |
| Boundary | What happens at minimum, maximum, empty, and just-outside limits? |
| Contract | Do status, headers, required fields, types, and allowed values match the API contract? |
| Regression | Do existing workflows still behave correctly after a change? |
| Security basics | Are access-control and input-handling expectations checked within authorized scope? |

## Test design workflow

1. Review the API contract and clarify behavior that is not specified.
2. Identify endpoint risks, dependencies, roles, and important data states.
3. Select representative valid, invalid, and boundary inputs.
4. Define setup and cleanup so cases are repeatable and do not depend on execution order.
5. Assert both transport-level behavior and meaningful response content.
6. Record actual results, defects, and any environment limitations.
7. Re-run impacted cases as regression coverage after changes.

## Validation checklist

- The response status is expected for the scenario.
- Relevant headers are present and have appropriate values (for example, content type).
- The response body is valid JSON when JSON is expected.
- Required fields exist and have the expected types and constraints.
- Returned values correspond to the request and known test data.
- Error responses do not expose unnecessary internal details.
- State-changing requests have an explicit setup and cleanup plan.

## Environment and test data

Keep base URLs, credentials, and environment-specific values outside committed examples. Use isolated, non-production test data where possible. Avoid hard-coded secrets and personal data. Make test data unique where collisions are possible, and document cleanup for created resources. Treat unavailable, unstable, or shared environments as test limitations in the results.

## Regression selection

After a change, select tests based on affected endpoints, shared authentication or validation behavior, downstream consumers, and critical user workflows. Include a small stable smoke set and broader targeted regression coverage as risk requires. This portfolio does not claim a particular CI or execution setup.

## Reporting

For each finding, capture the endpoint and method, sanitized request, expected and actual result, environment, and reproducibility notes. Never include credentials, tokens, or sensitive user data in reports.
