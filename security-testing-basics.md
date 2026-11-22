# API Security Testing Basics

This section reflects beginner-level learning and safe test design. It is not a penetration-testing report or a claim of advanced security expertise. Perform checks only on systems you own or have explicit permission to test, within the approved scope.

## Learning focus

The OWASP API Security Top 10 is a useful reference for understanding common API risk areas. For a beginner portfolio, focus on recognizing questions to ask and validating expected controls with authorized test accounts and non-sensitive data.

## Safe, basic scenarios

| Focus | Safe test question |
| --- | --- |
| Object-level authorization | With two authorized test users, can one access only the objects permitted to that user? |
| Authentication | Do protected endpoints deny requests without valid authentication, as specified? |
| Function-level authorization | Are role-restricted actions unavailable to a role that should not perform them? |
| Input validation | Are invalid lengths, types, and formats rejected consistently without exposing internals? |
| Sensitive data exposure | Does the response contain only fields needed by the caller and allowed by the contract? |
| Resource limits | Are documented pagination and payload limits enforced safely? |
| Error handling | Do errors avoid stack traces, secrets, and unnecessary implementation details? |

## Guardrails

- Use a designated test environment and accounts supplied for the work.
- Do not attempt destructive actions, denial-of-service behavior, credential guessing, or access outside the approved scope.
- Use synthetic data; do not copy real tokens or personal data into examples or reports.
- Stop and report unexpected access or impact rather than exploring further.
- Treat a checklist result as a limited observation, not proof that an API is secure.

## Reference

OWASP API Security project: [OWASP API Security Top 10](https://owasp.org/www-project-api-security/)
