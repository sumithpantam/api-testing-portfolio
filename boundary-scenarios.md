# Boundary API Scenarios

Boundary values should come from the API contract, product rules, or observed validation behavior. The values below are placeholders, not requirements for a real service.

| ID | Scenario | Example input | Expected checks |
| --- | --- | --- | --- |
| BND-01 | Minimum allowed numeric value | Field set to documented minimum | Accepted or rejected according to inclusive/exclusive rule; stored/returned value is correct. |
| BND-02 | Just below minimum | Minimum minus one representable unit | Rejected or handled per contract; no unintended state change. |
| BND-03 | Maximum allowed string length | String at documented maximum | Accepted if limit is inclusive; response preserves the allowed value. |
| BND-04 | One character over maximum | String just above documented maximum | Rejected or truncated only if explicitly specified; behavior is consistent and visible. |
| BND-05 | Empty versus omitted optional field | `"field": ""` versus no `field` key | Behavior follows documented distinction; errors identify the relevant field if rejected. |
| BND-06 | Empty result page | Filter or page beyond available records | Valid empty collection and pagination metadata, or contract-defined response. |
| BND-07 | Pagination edges | First page, last page, zero/negative/over-limit page size | Valid inputs work; invalid limits are rejected or bounded as documented. |

## Choosing values

Use equivalence partitions and boundary-value analysis to select the minimum, maximum, and values immediately around them. For dates, decimals, Unicode, and identifiers, use the formats and precision defined by the API contract.
