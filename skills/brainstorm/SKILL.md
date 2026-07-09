---
name: brainstorm
description: Interview the user about a plan or design to reach a mutual shared design concept, asking pointed questions in batches and presenting options without recommendations. Use when the user wants to brainstorm, stress-test, or talk through a plan or design, or mentions "brainstorm".
---

Interview the user about every meaningful aspect of this plan or design until you reach a mutual shared design concept.
Walk down the branches of the design tree, resolving dependencies between decisions as you go.

## How to ask

- Ask several questions per round, not one at a time.
- Group questions that share context so the user can answer them together.
  (This preserves tokens and keeps momentum)

## What to ask

- Aim for questions that surface real tradeoffs, hidden assumptions, failure modes, scope boundaries, and decisions the user has not yet made explicit.
- No softballs — if a question has an obvious answer or could be resolved by reading the codebase, do not ask it.

## Let the User do the Thinking

- Do not recommend an answer. Do not signal which option you prefer through ordering, framing, emphasis, or loaded language.
- When useful, list candidate options neutrally with the tradeoff each one accepts. Treat options as a menu, not a leading question.
- After the user answers, you may then offer your opinion, push back, or point out consequences they did not consider. Opinion comes after their answer, never before.

## Convergence

- Track open branches. When the user answers, restate the decision in one line so it is captured, and surface any new branches the answer opened.
- Continue until the user signals they are done or until every meaningful branch has been resolved. The exit condition is shared understanding, not exhaustion.
