---
name: be-code-review
description: Use this skill to guide an adversarial code review of a diff for "back-end" services.
model: sonnet
---

# Back-end Code Review

This skill describes what a review of a **diff** judges: whether the change causes failures, and whether it breaks the project's specs and conventions.
It says what the review looks for and how it classifies what it finds.
The skill or prompt that runs the review decides how to report the findings, for example as comments on a pull request or as one part of a larger assessment.

## Scope

The diff is both the subject of the review and its limit.
Every question below can be answered from the changed lines.
The Efficiency and Testability questions ask what the changes *introduced* and whether the *new or modified* code is tested, so without a diff the review has no point at which it is finished.
This skill applies only to a diff.

Every file in the diff is in scope: build pins, configuration, and migrations as well as code.
A moved version pin or a changed default changes a contract, so it gets the same scrutiny as a changed function.

The review judges what tools cannot.
A defect that a linter, formatter, type checker, or architecture test already catches is outside its scope.

Two other skills judge what a diff cannot show:

- `distributed-design-review` judges the system as a whole: its failure modes, its contracts with peers, and its recovery paths.
- `repo-audit` judges the repository as it stands: inconsistencies between one place and another, and drift that built up across many diffs, none of which introduced it alone.

## Findings

The result of the review is a list of findings.
A file with nothing wrong in it contributes nothing to that list.
Each finding is one of two kinds:

- a **violation**: the code is wrong, or it breaks a documented standard;
- a **judgement call**: a reasonable reviewer could disagree.

## Pillars

The review judges the changed lines against each of the following questions, which this skill calls pillars.

- **Adherence to Spec.** Does the code do what the spec says, and does the spec cover everything the code does?
   - A decision in the code that the spec does not account for is a finding.
   - When the diff changes a spec, the spec is under review as well; see [Spec Consistency](#spec-consistency).
   - When the spec names an exact identifier, such as a proto package or type, a column, a metric name, or a version, the diff must use that exact string.
   - Note that specs, code, and migrations may be in separate diffs.

- **Correctness.** Does the code do what it is meant to do, without bugs or logical errors?
   - A constraint, validation, or guard is correct only if it rejects every input it must reject: an absent value, a null, an empty value, and a partial value.
   - A new or changed entrypoint is correct only if it enforces every business invariant that the other entrypoints enforce on the same data.
   - A database constraint does not make up for a missing check because it surfaces as an internal error.

- **Distribution.** Does the code follow good distributed-systems practice?
   - Does it state the latencies and timeouts it expects?
   - Does it use circuit breakers on high-traffic API call paths?
   - Does it support idempotency and resumability?
   - Does it keep working while it is being deployed? Until the deploy finishes, servers running the previous build keep writing state that this diff's code reads.

- **Maintainability.** Is the code clear, modular, and structured the way the code around it is, so the next person can change it?

- **Readability.** Does the code have a comment wherever one is needed, and does it follow the project's coding style?

- **Efficiency.** Do the changes introduce a performance bottleneck or waste a resource?

- **Security.** Do the changes introduce a vulnerability or an insecure practice?

- **Edge Cases and Error Handling.** Does the code handle the edge cases and errors it can meet?

- **Testability.** Do tests cover the new and modified code?
   - A Testability finding names the test cases that would improve coverage or robustness.

## When to Suggest a Design Review

The four Distribution questions go as deep as a review of a diff should.
The review also judges whether the change needs a `distributed-design-review` as well.
It does when the change does any of the following:

- it calls something this service does not own, or stops calling it;
- it adds work that finishes after the response, such as work that is queued, retried, or scheduled;
- it changes a schema, an event format, or a wire contract;
- it changes how the service behaves under load or when a dependency fails.

When the change needs one, the result of the review includes a suggestion to also run `distributed-design-review`.

## Project Conventions

`AGENTS.md` (steerage) and `CONTRIBUTING.md` (conventions) hold the project's standing rules, and both apply to every change.
When the diff touches something a rule governs, such as a DAO method, a metric, a domain import, a name with a unit suffix, or a version pin, the diff must follow that rule.
This review exists to catch the rules that live only in prose.

A diff that breaks a stated convention is a **violation**, the same as a diff that breaks its spec.
The finding quotes the rule and points at the line that breaks it.

## Spec Consistency

*Trigger: the diff adds or changes a document under `docs/specs/`.*

The Adherence to Spec pillar asks whether the code matches its spec.
This section asks whether the changed spec matches the specs around it.
A spec that contradicts its neighbours is wrong in the same way that code which contradicts its spec is wrong.
The diff that introduces the contradiction is the cheapest place to fix it, because the author still has both documents in mind.

The review compares the changed spec with its **neighbours** only:

- the specs it links to, and the specs that link to it;
- the glossary, for any term the spec defines, uses, or redefines;
- any spec that describes the same peer, contract, table, or lifecycle.

Among the neighbours, the review looks for four kinds of defect:

- **Contradicted claims.** Two specs describe the same call, state machine, or guarantee differently.
- **Terms that drifted.** A spec uses a word with a meaning the glossary does not give it, or gives it differently.
- **Restated decisions with different values.** The same threshold, default, or rate appears in two places, and the two copies now disagree.
- **Stale claims about other documents.** A conformance note, a status line, or a "see X" describes something that is no longer true.

A contradiction is a **violation** even when the review cannot tell which side is wrong; the author decides that.
The finding names both sides and quotes each.
When two specs only emphasize different things, or when the same content appears in two places but the copies still agree, the finding is a **judgement call**.
