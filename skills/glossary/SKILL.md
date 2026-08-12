---
name: glossary
description: Maintain the project glossary (docs/glossary.md) — the canonical vocabulary for a repo's domain. Use when a term is coined, sharpened, or disputed, or when a writing skill needs canonical vocabulary.
credits: Adapted from [Matt Pocock](https://github.com/mattpocock/skills)'s domain-modeling skill, with the ADR half dropped.
---

# Glossary

A glossary is the project's dictionary: the canonical term for each concept, defined once, used everywhere.
Code, specs, and conversation use the glossary's terms.
Writing skills treat glossary entries as definitions the reader can reach, so documents use the terms without re-explaining them (see the plain-english skill).

## The file

One glossary per repo, at `docs/GLOSSARY.md`.
If the repo already keeps a glossary under another name (for example a legacy `docs/concepts.md`), treat that file as the glossary rather than creating a second one.
Create the file lazily — when the first term is resolved, not before.

## Entry format

```md
### Quote

A priced snapshot of a purchase, minted by `CreateQuote` and redeemable at most once.

*Aliases to avoid*: offer, estimate.
```

- Each term is a heading, so other documents can deep-link its anchor (e.g. `glossary.md#tombstone`).
- Be opinionated. When multiple words exist for the same concept, pick the best one and list the others in the *Aliases to avoid* line.
- Keep definitions tight: one or two sentences saying what the thing IS, not what it does.
- Only include terms specific to this project. General programming concepts (timeouts, retries, error types) don't belong, even when the project uses them heavily.
- The glossary holds language and nothing else: no implementation detail, no decisions, no specs.
- Group terms under subheadings when natural clusters emerge; a flat list is fine until then.

## The practice

The glossary stays current because it is updated during the conversation, never batched:

- When a vague term becomes canonical, write the entry immediately, in the same change.
- When someone uses a term that conflicts with an entry, challenge it: "The glossary defines cancellation as X, but you seem to mean Y — which is it?"
- When someone uses a fuzzy or overloaded word, propose the canonical term: "You're saying 'account' — do you mean the Customer or the User?"
- When the code contradicts the glossary, surface it rather than silently picking a side.
