---
name: be-code-review
description: Use this skill to guide an adversarial code review of a diff for "back-end" services.
---

# Back-end Code Review

This reviews a **diff**, and looks for failures this pull request causes. You can answer every pillar below by reading the changed lines. Questions about the system as a whole — its failure modes, its contracts with peers, its recovery paths — belong to `distributed-design-review`. Questions about the repository as it stands — inconsistencies between one place and another, accumulated drift no single diff introduced — belong to `repo-audit`.

Every file in the diff is a subject of the review: build pins, configuration, and migrations as much as code. A moved version pin or a changed default is a contract change and gets the same scrutiny as a changed function. This rule directs attention, not reporting — a routine file earns silence (see [Reporting](#reporting)).

## Pillars

Review the changed lines against each pillar.

- **Adherence to Spec.** Does the code do what the spec says, and does the spec cover all of it? Name any decision the spec does not account for. When the diff changes a spec, the spec is under review too — see [Spec Consistency](#spec-consistency). A dedicated migration pull request may document a column's fill and read contract without implementing it, so look for that code in the pull requests beside it. When the spec names an exact identifier — a proto package or type, a column, a metric name, a version — search the diff for that string. A named identifier is checkable by exact match, and a mismatch is a finding even when the behavior looks right.

- **Correctness.** Does the code achieve its stated purpose, free of bugs and logical errors? Execute every constraint, validation, or guard the diff adds against the inputs it must reject: absent, null, empty, and partial values, one conjunct at a time. SQL `CHECK` passes when it evaluates to NULL, so a condition on a missing JSON key admits the row it looks like it rejects. For each entrypoint the diff adds or changes, list the business invariants sibling entrypoints enforce on the same data, and check the new path enforces the same set. Falling through to a database constraint is a gap, not a defense: the rejection surfaces as an internal error, and silent coercions (rounding, truncation) pass it entirely.

- **Distribution.** Does the code follow good distributed-systems practice?
   - Does it state the latencies and timeouts it expects?
   - Does it use circuit breakers in hot API call paths?
   - Does it support idempotency and resumability?
   - Does it survive its own rollout? While this change deploys, the previous build keeps writing state that this diff's code reads. Name the states only that window creates, and check that every read path the diff adds has an exit for them. This asks what this pull request breaks on its way in; migration safety as a design question belongs to `distributed-design-review`.

- **Maintainability.** Is the code clear, modular, and structured the way the code around it is, so the next person can change it?

- **Readability.** Does the code carry a comment wherever one is needed, and follow the project's coding style?

- **Efficiency.** Do the changes introduce a performance bottleneck or waste a resource?

- **Security.** Do the changes introduce a vulnerability or an insecure practice?

- **Edge Cases and Error Handling.** Does the code handle the edge cases and errors it can meet?

- **Testability.** Do tests cover the new and modified code? Passing preflight checks does not settle this. Suggest the test cases that would improve coverage or robustness.

## Hand Off to a Design Review

Those four distribution questions are the right depth for a diff. Hand off to `distributed-design-review` when the change:

- calls something this service does not own, or stops calling it;
- adds work that is queued, retried, scheduled, or otherwise finishes after the response;
- alters a schema, event format, or wire contract;
- alters what happens under load or when a dependency fails.

## Spec Consistency

*Trigger: the diff adds or changes a document under `docs/specs/`.*

Adherence to Spec asks whether the code matches its spec. This section asks whether the spec matches the specs around it. A spec that contradicts its neighbours is wrong in the same way code that contradicts its spec is wrong, and the diff that introduces the contradiction is where it is cheapest to fix, while the author still has both documents in mind.

Bound the review to the changed spec's **neighbours**, not to the spec tree:

- the specs it links to, and the specs that link to it;
- the glossary, for any term it defines, uses, or redefines;
- any spec describing the same peer, contract, table, or lifecycle.

Within that neighbourhood, look for:

- **Contradicted claims.** Two specs describing the same call, state machine, or guarantee differently. A peer contract that one document calls synchronous and another gives an asynchronous fallback is the same defect as code that ignores its spec.
- **Terms that drifted.** A word used with a meaning the glossary does not carry, or carries differently. The `writing-specs` skill requires canonical vocabulary and a glossary update in the same change.
- **Restated decisions with different values.** The same threshold, default, or rate written twice and now disagreeing. Prefer a reference over a second copy.
- **Stale claims about other documents.** A conformance note, status line, or "see X" that describes a state of affairs no longer true.

Report a contradiction as a **violation** even when you cannot tell which side is wrong — that judgement belongs to the author. Name both sides and quote each. Report a **judgement call** where two specs merely emphasize differently, or where duplication has not yet diverged.

Checking every spec against every other spec is a sweep, and belongs to `repo-audit`. This section covers the neighbourhood of one change.

## Reporting

Report findings, not coverage. A pillar with nothing to say gets no section.

Report each defect once, under the most applicable pillar.

Mark each finding as a **violation** — the code is wrong, or breaks a documented standard — or a **judgement call**, where a reasonable reviewer could disagree. Skip anything a linter, formatter, or type checker already catches.

## Scope Boundary

The diff is both the subject of this review and the bound on it. Two pillars are defined in terms of it: Efficiency asks what the changes *introduced*, and Testability asks about *new or modified* code. A review with no diff therefore has no stopping condition. Point this skill at a diff, and use `repo-audit` for a whole repository, since it carries its own bounding mechanism.
