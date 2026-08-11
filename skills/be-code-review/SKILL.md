---
name: be-code-review
description: Use this skill to guide an adversarial code review of a diff for "back-end" services.
---

# Back-end Code Review

This reviews a **diff**. Every pillar below is answerable by reading the changed lines. Questions about the system as a whole — its failure modes, its contracts with peers, its recovery paths — belong to `distributed-design-review`.

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

   Three questions are the right depth for a diff. Hand off to `distributed-design-review` when the change:
   - calls something this service does not own, or stops calling it;
   - adds work that is queued, retried, scheduled, or otherwise finishes after the response;
   - alters a schema, event format, or wire contract;
   - alters what happens under load or when a dependency fails.

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

## Reporting

Report findings, not coverage. A pillar with nothing to say gets no section.

Mark each finding as a **violation** — the code is wrong, or breaks a documented standard — or a **judgement call**, where a reasonable reviewer could disagree. Skip anything a linter, formatter, or type checker already catches.
