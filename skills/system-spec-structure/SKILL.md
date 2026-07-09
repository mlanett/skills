---
name: system-spec-structure
description: Apply a consistent, approachable structure to design system specs and architecture docs so a teammate can build a mental model top-down. Use whenever the user asks to organize, reorganize, restructure, or structure a spec; when creating a new spec from scratch; when the user complains a spec is hard to read, scattered, or buries the architecture; when asked to make a spec more approachable to a team; or when the user mentions spec sections like "Goals", "Decisions", "Summaries", or asks to convert rejected-alternatives prose into architectural decisions.
---

# System Spec Structure

## The structure

Sections in order. The spec reads top-down: high-level first, then details, then operational tail.

### Header

- Table of contents.

### Open Questions

Let's put this at the top so that people who are familiar with the proposal can focus on the important immediate decisions.

### High-level (read top-down)

- Goals: three subsections:
  - *Problem*: what real-world need this addresses.
  - *Workflow*: the user-visible sequence the system supports.
  - *Proposed system*: a one-paragraph sketch of what we are building to support the workflow.

- Concepts: important models, endpoints, workflows. One line each.

- Overview: a single or few happy-path diagram(s) (sequence or flowchart) showing major components, endpoints, and how the workflow flows through them.
  A reader should be able to glance at this and gain an understanding of the shape of the system before reading anything else.

- Summaries — the important artifacts at a glance: the wire types (proto block), an endpoints table (one line each), and the database DDL. No surrounding prose; the reader scans these to anchor what comes next. The detailed sections below explain them.

### Status

- Status and Future Plans.
- References: sibling specs, upstream/downstream specs, foundational types.

### Detailed proposal (reference material)

In this order:

- Decisions: *architectural* decisions only. Each decision is its own subsection with:
  - The decision itself, stated declaratively in the heading.
  - Why: motivation, with any rejected alternatives folded in as rationale.
  - Tradeoff: what we are giving up and why that is acceptable.

  Keep this section scannable. If more than ~6 decisions accumulate here, you have probably mixed architectural decisions with smaller policy decisions; push the smaller ones inline near their detail.

- Models and data types: request/response messages, wire types, internal sealed types, identifier formats. Forward-looking notes (e.g. "Future: campaigns") fit at the end of this section.
- Endpoints: one subsection per endpoint or service. Describe behavior at the contract level; link out to *Processes and workflows* for full flows. Cross-service boundary endpoints (e.g. the REST contract with another service) belong here too.
- Data storage: schema DDL with annotations. Smaller schema-related policy decisions stay inline next to the schema they govern, not in *Decisions*.
- Trust boundaries: identity, currency, tenancy, any other "what do we trust upstream to have validated" topics.
- Processes and workflows: full diagrams with all paths (lifecycle sequence diagrams, flowcharts showing every error branch, predicates as pseudocode). Authoritative step-by-step semantics live here; the step list mirrors the diagram branch by branch.
- Robustness: what can go wrong (latency, growth, partial-failure windows, upstream misbehavior) and how the design handles it.
- Testability: how the design itself supports verification (stubs, sealed types for exhaustive matching, fixture replay of upstream payloads, contract-level assertions). Not the test list — the test list goes in *Tests*.
- Debuggability: how operational problems are diagnosed and recovered (preserved payloads, decodable identifiers, indexed audit queries, the absence of half-states, what *cannot* be reconstructed from the database and why that is acceptable).

### Tail (operational)

- Metrics: counters and tags, with brief descriptions and what action a non-zero rate suggests.
- Configuration: knobs and defaults.
- Implementation: technology choices, package/domain placement, ArchUnit or other architectural rules, rollout notes.
- Tests: test list in "given – when – then" format. Each test references the spec section it pins down.
- Related documents: sibling specs and upstream/downstream references.

## Where decisions live

- *Decisions* (architectural): choices that shape the system's structure — what kind of artifact is issued, which service owns a boundary, what is authoritative versus advisory, whether something is persisted at all, what transport a boundary uses.
- Inline (policy): choices that govern a single artifact and only make sense next to it.

Test: if someone read the decision without the detailed artifact, would it still make sense and be debatable on its own? If yes → *Decisions*. If no → inline.

Rejected alternatives become the *Why* of the relevant decision, not their own section.

## Cross-domain content

If a spec needs to specify behavior owned by a different domain, extract that content into a sibling spec owned by that domain. Frame it as "Domain B must expose Y to support Z." The originating spec carries one line and a link.

## Diagrams

- Always Mermaid, not ASCII.
- *Overview* gets one happy-path diagram. Resist showing error branches here — they belong in *Processes and workflows*.
- *Processes and workflows* shows full lifecycles, including draft or future regions visually distinguished (e.g. with `rect rgb(...)` shading) so readers see where the current scope ends.
- Flowcharts in *Processes and workflows* should terminate every branch at a labeled outcome (success or specific error), and the step list below the flowchart should walk the same branches in the same order.

## Naming and formatting conventions

- Section headings are sentence case, not Title Case ("Trust boundaries", not "Trust Boundaries").
- Use real Markdown headings (`####`), not bold-as-heading (`Heading`) — bold-as-heading is harder to scan and does not appear in the table of contents.
- Subsections under *Decisions* state the decision as their heading (e.g. "`total_price` is authoritative; breakdown identity is not enforced"), so the table of contents reads as a list of choices.

## Common reorganization moves

When restructuring an existing spec, this sequence is usually faster than reading top-to-bottom and rewriting in place:

1. Inventory decisions. Skim the doc and list every "we do X because Y" claim, whether in a Rejected-Alternatives section, embedded prose, or a footnote. Classify each as architectural (goes in *Decisions*) or inline policy (stays next to its artifact).
2. Locate the diagrams. Identify the happy-path-worthy diagram for *Overview*; identify the full-path diagrams for *Processes and workflows*. If a happy-path diagram does not exist, create one by stripping error branches from a full diagram.
3. Extract the artifacts. Pull the proto messages, endpoints, and DDL into *Summaries*. The detailed *Models* / *Endpoints* / *Data storage* sections then explain them; the summary section just shows them.
4. Spot cross-domain content. Anything specifying behavior of another domain → propose a sibling spec and reduce the originating section to a one-line link.
5. Write Goals last (often). Existing specs frequently lack a clean problem statement because the author was deep in the design when they wrote it. Drafting *Goals* after seeing the rest reorganized is often easier than drafting it first.

## Notes

Drop sections that have nothing to say.
