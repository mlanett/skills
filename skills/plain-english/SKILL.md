---
name: plain-english
description: Write in plain english.
credits: Inspired by [Jacob Kaplan-Moss](https://jacobian.org/2009/nov/11/technical-style/), Strunk & White's Elements of Style, and [Matt Pocock](https://github.com/mattpocock/skills)'s writing-beats skill.
---

# Plain English Technical Writing Style Guide

Make the point the unit of composition.
A point does one thing: it sets up context, states a claim, asks a question, or gives an example. Then it stops and the next point begins.
A point can be one sentence, one paragraph, or several paragraphs when it is a self-contained example or argument.
If a point needs five paragraphs and three subheadings, it is really two points; split it.
Order the points so that each one builds on the ones before it, and the reader can read straight through without going back.

Write in short paragraphs; avoid sentence fragments and staccato sentences.

Do not explain too much. Make every word tell.

State each fact fully once, in its home section; elsewhere, link to it rather than restating it.
A summary map, a self-contained entry (an open question, a scenario), or a brief top-level statement above the detail section may legitimately restate.

Use varied structure (lists, code blocks, Mermaid diagrams for workflows).
Write new lines unwrapped; let them go long so the editor can render it properly.
When editing an existing document, keep its wrapping style so the diff shows only real changes.

Strongly avoid tables due to formatting and readability issues. Only use if the data is clearly tabular, few columns, and narrow.
A table has failed when its cells hold prose, or when it needs notes underneath re-explaining its rows; convert it to a list and fold the notes into the items.

Make the structure visual: lots of section headers, callouts for especially important information.

Be conversational; write close to how you would talk. Prefer the standard to the offbeat.

Use definite, specific, concrete language; ground complex operations with examples and scenarios.
Avoid metaphors, similes, and high levels of abstraction, especially mathematical abstractions (axis, dimensions, slices).

Use common vocabulary.
Clarify ambiguous words by using dedicated terminology, and use the terms consistently.

Explain every concept before you use it.
When a concept has a name, give the idea and the name at the same time.
Decide before you write which concepts the reader brings and which the document explains.
When a point needs a concept you have not explained, either explain it in an earlier point or add it to what the reader must bring.

When editing an existing document, treat a term as already explained when the document defines it, links to a definition of it, or shares it with the surrounding documents as standard vocabulary.
A project glossary (see the glossary skill) defines its terms for every document in the repo.
Explain a term only when no document the reader can reach defines it.

Treat named things as identifiers: decision titles, section names, quoted phrases from other documents, code-formatted terms, and recurring structural labels (like a spec's "Why." rationale paragraphs).
Reword the prose around them; keep the names themselves verbatim, since other documents refer to them.

For plans and proposals, prefer first person plural and future tense ("we will…").
For specs, prefer third person and present tense ("it does…").

Use the active voice; it is more vigorous and direct than passive voice.

Choose a suitable design and hold to it.

Make assertions positive.
Avoid self-negation ("it's not X, it's Y") unless there is strong reason to believe that the reader is thinking about the wrong concept.
The test is whether a reader would plausibly assume the negated thing; in specs, many contrasts pass it.
What fails the test: the same negation repeated within a sentence or paragraph, negation as pure emphasis, and leading with the negative when the positive claim should come first.
Always keep normative language (must, must not, shall, never) at full strength when rephrasing, so that we do not change any contracts.

Revise and rewrite.
