# Negative API Scenarios

These are test-design prompts for a hypothetical resource API. The contract determines the exact expected status and error format.

| ID | Scenario | Example request | Expected checks |
| --- | --- | --- | --- |
| NEG-01 | Required field omitted | `POST /resources` with one required field missing | Contract-defined client error; clear field-level validation; no unintended resource created. |
| NEG-02 | Invalid field type | `POST /resources` with a number where a string is required | Request rejected according to contract; error body is structured and does not reveal internals. |
| NEG-03 | Malformed JSON | `POST /resources` with invalid JSON syntax | Request rejected safely; response follows documented error conventions. |
| NEG-04 | Unknown resource identifier | `GET /resources/{unknown-id}` | Contract-defined not-found behavior; response does not expose unrelated data. |
| NEG-05 | Unsupported method | Use a method not supported on a known path | Contract-defined method error; allowed methods may be communicated where appropriate. |
| NEG-06 | Missing or invalid authentication | Call a protected endpoint without valid credentials | Access is denied as specified; no protected data is returned. |
| NEG-07 | Invalid query parameter value | `GET /resources?status={unsupported-value}` | Contract-defined validation behavior; response is stable and understandable. |

## Execution notes

Use only authorized environments and non-sensitive data. Check for side effects after rejected state-changing requests; a failed response should not silently create or alter data.
