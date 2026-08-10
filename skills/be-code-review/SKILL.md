---
name: be-code-review
description: Use this skill to guide an adversarial code review for "back-end" services.
---

# Back-end Code Review

Analyze the code changes based on the following pillars:

- Adherence to Spec:
  Does it do what the spec says it does. Is it all covered by the spec? Does it make extra surprising decisions?

- Correctness:
  Does the code achieve its stated purpose without bugs or logical errors?

- Distribution:
  Does this code embody good principles for distributed systems, including:
  - Definition of expected latencies and timeouts.
  - Use of circuit breakers in hot API call paths.
  - Support for idempotency and resumability.

- Maintainability:
  Is the code clean, well-structured, and easy to understand and modify in the future?
  Consider factors like code clarity, modularity, and adherence to established design patterns.

- Readability:
  Is the code well-commented (where necessary) and consistently formatted according to our project's coding style guidelines?

- Efficiency:
  Are there any obvious performance bottlenecks or resource inefficiencies introduced by the changes?

- Security:
  Are there any potential security vulnerabilities or insecure coding practices?

- Edge Cases and Error Handling:
  Does the code appropriately handle edge cases and potential errors?

- Testability:
  Is the new or modified code adequately covered by tests (even if preflight checks pass)?
  Suggest additional test cases that would improve coverage or robustness.
