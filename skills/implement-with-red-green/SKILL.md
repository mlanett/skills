---
name: implement-with-red-green
description: Use this skill when implementing code from a spec or an implementation plan.
reference: https://x.com/trq212/status/2056418157305454805
---

# Implement with Red/Green

- Use Red/Green TDD.

- Run tests after each major implementation step.

- Commit after tests pass.

- Maintain a running notes file that captures anything I should know about how the implementation interprets the spec, including:

  - Design decisions: choices you made where the spec was ambiguous
  - Deviations: places where you intentionally departed from the spec, and why
  - Tradeoffs: alternatives you considered and why you picked what you did
  - Open questions: anything you'd want me to confirm or revise

- Name this file to match the relevant spec, e.g. `specs/myspec-notes.md`.
