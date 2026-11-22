# Positive API Scenarios

These generic cases describe expected behavior for a hypothetical resource API. Replace placeholders with the actual contract before execution.

| ID | Scenario | Example request | Expected checks |
| --- | --- | --- | --- |
| POS-01 | Retrieve an existing resource | `GET /resources/{known-id}` | Expected success status; JSON body; identifier matches; required fields have documented types. |
| POS-02 | Create a resource with valid required fields | `POST /resources` with valid body | Contract-defined creation status; response identifies the created resource; returned fields match submitted or server-generated values. |
| POS-03 | Update an existing resource with valid values | `PUT /resources/{known-id}` with valid body | Contract-defined success status; updated fields match; unrelated fields remain consistent with the contract. |
| POS-04 | List resources with supported pagination | `GET /resources?limit={supported-limit}` | Success status; list structure is valid; page size and pagination metadata follow the contract. |
| POS-05 | Use a valid optional filter | `GET /resources?status={allowed-value}` | Success status; returned records satisfy the filter; empty results remain valid if no record matches. |

## Execution notes

- Use known, isolated test data and document its origin.
- Validate response content as well as status.
- Clean up resources created by a case when the environment and contract permit it.
