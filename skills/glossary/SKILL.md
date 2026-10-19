---
name: glossary
description: Maintain the project glossary (docs/glossary.md) — the canonical vocabulary for a repo's domain. Use when a term is coined, sharpened, or disputed, or when a writing skill needs canonical vocabulary.
credits: Adapted from [Matt Pocock](https://github.com/mattpocock/skills)'s domain-modeling skill, with its architecture-decision-record half dropped.
---

# Glossary

The glossary is the project's dictionary and index: the canonical term for each concept, defined once, used everywhere.
Specs, plans, code, tests, and conversation use the glossary's terms.
All important concepts are defined in the glossary.
Documents may use glossary entries without explaining them; a link to the glossary provides the explanation.
Each term in the glossary points to where it is defined.

## The file

One glossary per repo, at `docs/GLOSSARY.md`, unless the AGENTS.md says to locate it elsewhere.
Create the file lazily — when the first term is resolved, not before.

Entries sit in alphabetical order under their subheading, and the table of contents lists every entry.
When you add an entry, add its line to the table of contents in the same change.

## Entry format

```md
### Quote

A priced snapshot of one purchase selection (`checkout.quote`), redeemable at most once.

*Defined in: [Quote — `Quote` and `TaxLine`](../../../docs/specs/checkout/quote.md#quote-and-taxline).*

*Aliases to avoid: offer, estimate.*
```

- Each term is a heading, so other documents can deep-link its anchor (e.g. `glossary.md#tombstone`).
- The *Defined in* line names the one spec section that owns the definition; see [One owning section](#one-owning-section).
- Be opinionated. When multiple words exist for the same concept, pick the best one and list the others in the *Aliases to avoid* line.
- Keep definitions tight: one or two sentences saying what the thing IS, not what it does.
- Only include terms specific to this project. General programming concepts (timeouts, retries, error types) don't belong, even when the project uses them heavily.
- The glossary holds language and nothing else: no implementation detail, no decisions, no specs.
- Group terms under subheadings when natural clusters emerge; a flat list is fine until then.

## Meaning, not mechanism

An entry says what a term means, so a reader can follow a sentence that uses it.
It does not say how the system produces the thing, where it is stored, what its defaults are, or why it was decided that way.
Mechanism is the spec's job, and the entry's *Defined in* line names the section that does it.

An entry keeps:

- What kind of thing the term names: an instant, a row, a role, a process, a word for something the consumer does.
- What tells it apart from its nearest neighbours, so a writer picks the right term.
- One contrast, when readers plausibly confuse it with another term.
- At most one code identifier, and only the term's own name in code (`checkout.quote` for Quote, `late_cancel_window_h` for Late-Cancel Window) — not the RPC, step, or table that produces or stores it.

One or two sentences; a third only for the contrast.

The test for a sentence is whether it answers "what is it?" or "how does it work?".
A sentence is mechanism when it names the column, table, RPC, saga step, config value, code path, or default that produces, stores, or reads the thing; when it narrates a computation or a sequence of events; or when it argues for a decision.
Cut it and read the entry again: if the entry still identifies the concept, the sentence was mechanism.
A cut sentence that states a fact no spec states is a fact in the wrong file, not a fact to delete — move it to the spec section the entry links to, in the same change or a follow-up, and say so.

## One owning section

A definition lives in one spec section, and every other document refers to it.
The *Defined in* line is how the glossary records which section that is, so a writer who needs the rule knows where to read it, and a writer who wants to state the rule knows where it goes.

- Every entry under General Concepts ends with one *Defined in* line naming exactly one section: a heading anchor, not a file, so the reader lands on the definition.
- That section is the term's `###` entry under the owning spec's `## Definitions` section, whose heading is the glossary heading word for word, so the anchor is the glossary anchor. Where the owning spec has no Definitions section yet, the line names the section that states the rule, and giving that spec a Definitions section repoints the line.
- The owner is the spec whose tests pin the rule, and there is exactly one such test list. That is usually the spec that produces the thing, not one that consumes it.
- An optional *See also* line lists sections that consume the term. It never carries the definition.
- Any spec may restate the term's meaning so its reader is not sent elsewhere. Only the owning section states the rule: the formula, the boundary, the default.
- When no section defines the term yet, write `*Defined in: tbd.*` and keep the entry. That is not a blocker: the repo-audit skill counts `tbd` entries down. Resolving one means finding or writing the owning section.

The sections after the entries (Relationships, Tags and Verticals, Flagged Ambiguities, Known Complications) are notes rather than entries and carry no *Defined in* line.

## The practice

The glossary stays current because it is updated during the conversation, never batched:

- When a vague term becomes canonical, write the entry immediately, in the same change.
- When someone uses a term that conflicts with an entry, challenge it: "The glossary defines cancellation as X, but you seem to mean Y — which is it?"
- When someone uses a fuzzy or overloaded word, propose the canonical term: "You're saying 'account' — do you mean the Customer or the User?"
- When the code contradicts the glossary, surface it rather than silently picking a side.
- When a spec first defines a term whose entry says `tbd`, point the entry at that section in the same change.

To realign an existing spec with the glossary — harvest missing terms, compress entries, report drift and conflicts — use the spec-maintenance skill.
