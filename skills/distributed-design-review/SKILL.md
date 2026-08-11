---
name: distributed-design-review
description: Review the design of a distributed system — its failure modes, contracts, invariants, and recovery paths. Use when writing or reviewing a design doc, spec, or TDD, or when assessing an existing system's robustness. Not for reviewing a diff; use be-code-review for that.
---

# Distributed Design Review

This reviews a **design**, not a diff. Most items here are answered by a document, an architecture, or an owner — not by changed lines. Pointed at a pull request, it produces noise.

Entities:

- The system: runtime behavior (persists, sheds, expires, isolates, bounds…)
- The design / The team: decisions made up front (states assumptions, names invariants, defines SLOs)
- Peers: cross-system contracts (agree on, define)
- Operators: incident-time human actions (trip, toggle, drain, gather evidence)
- A named owner: accountability during recovery

## How to use this

### 1. Scope it

Read the design, then pick the sections that apply. Each section carries a trigger; if the trigger is absent, skip the whole section. Most reviews touch three or four. Invariants, Observability, and Testing always apply.

### 2. Resolve each item to one of three outcomes

- **Satisfied** — the design answers it.
- **Deliberately not applicable** — the design does not need it, and you can say why.
- **Gap** — the design should answer it and does not.

The middle outcome is a real answer and often the most common one. A design that consciously declines a mechanism is not deficient, and saying so is the point of naming the mechanism.

### 3. Report only gaps

Ranked worst first, capped at ten. More than ten means the design needs a conversation, not a list. Never report satisfied items, and name skipped sections once rather than enumerating what was in them.

Items marked *opinion* are house defaults rather than neutral checks. Raise a departure as a question, not a gap.

## Operation Identity

*Trigger: callers can submit the same work twice.* Referenced by Time, Observability, and Security below.

- Each operation carries an identity, minted at a defined point, which makes duplicate submission _safe_.
   e.g. externally-supplied id or name, or system-provided.
- The identity stays stable across operations that should be retried, and distinct across operations that should stay separate.
- Querying an operation by identity returns a rich status — unknown, in progress, or complete with outcome.
   This makes lost responses _recoverable_.

## Invariants

*Always applies.*

- The design names the correctness properties that must always hold, even during failures.
- The design names what may be temporarily wrong.
- The system enforces each invariant synchronously, asynchronously, or only by reconciliation.

## Robustness and Resumability

*Trigger: work can be interrupted between starting and finishing.*

- The design states expected latency, tolerances, and timeouts.
- The design states expected traffic loads, tolerances, and behavior when they deviate.
- The system persists state durably, so work resumes after interruption or outage.
- The system resumes incomplete operations.
- The system applies admission control or backpressure to govern how fast deferred work re-enters the active set.
   e.g. backoff, jitter, bulkhead, and scheduling.
- The system can shed work early to prevent overload, where deliberately permitted.
- *Opinion:* notifications are optimizations; polling is what guarantees delivery.

## Dependency Contracts

Defending against what we depend on. *Trigger: the design calls something it does not own.*

- For each dependency, the system defines circuit-breaker, fallback, retry, and timeout behavior.
   Fallback terminology: fail-open = continue; fail-closed = stop.
- The system assigns trust and lifetime to each dependency's responses, e.g. authoritative, cacheable, advisory.
- The system responds to a dependency outage with replay, reconciliation, or deliberate inaction.
- The system handles partial degradation of a dependency, not just full outage.

## Peer Contracts

Mutual promises between systems. *Trigger: an effect spans two or more systems.*

- Peers agree on ordering and delivery semantics.
- Peers state their consistency guarantee explicitly; see also Invariants.
   *Opinion:* it is eventual unless someone paid for otherwise.
- Peers define atomicity for effects spanning two or more systems.
- Peers agree on schema and version compatibility.

## Degradation Behavior

*Trigger: the design has a load or dependency failure mode worth naming.*

- The system sheds functionality in a deliberate, predefined order.
- The system defines whether degradation is automatic or operator-driven.
- Degraded responses carry a marker distinguishing them from normal ones.
- The system returns stale, partial, cached, approximate, or best-effort results only where deliberately permitted.

## Failure Boundaries

*Trigger: capacity is shared across tenants, regions, shards, queues, or partitions.*

- No single tenant, region, shard, queue, partition, or dependency can exhaust capacity for everyone.
- Bulkheads separate online traffic, background work, retries, backfills, and admin jobs.

## Reconciliation

The path back to health. *Trigger: any invariant is enforced asynchronously or by reconciliation.*

- The system provides a defined reconciliation mechanism.
- Reconciliation runs with an owner, schedule, scope, and safety limits.
- Reconciliation identifies inconsistent state, and distinguishes “needs repair” from “valid terminal divergence.”
- Repair actions are idempotent and auditable.

## Time and Lifetimes

*Trigger: anything expires, retries, or is retained.*

- The design states its clock-skew assumptions.
- The system propagates deadlines where needed, or deliberately does not.
- The system bounds the lifetime of everything that expires — leases, sessions, TTLs, retry validity, operation identities — or deliberately keeps each unbounded.
- The system retains operation records, idempotency keys, events, logs, and reconciliation evidence for defined windows.
- The system handles client retries that arrive after retention expiry.
- The system defines whether stale messages and events can still mutate state.
- The system honors privacy and deletion requirements for durable workflow state.

## Cancellation and Supersession

*Trigger: work is long-running or queued.*

- The system can cancel in-flight work.
- Newer work can supersede older work.
- The system handles cancelled work that completes anyway, and resolves cancellation/completion races safely.

## Deployment and Migration Safety

*Trigger: the system rolls out incrementally, or alters a schema or wire format.*

- Rolling deploys preserve backward and forward compatibility.
- The system behaves correctly when two versions run concurrently: old workers process new messages, and new workers process old messages.
- Database and schema migrations apply in a safe, defined order.
- Event, protobuf, and schema formats evolve compatibly.
- Deploys are safe to roll back.

## Security and Abuse

*Trigger: the system is multi-tenant, or exposes endpoints beyond its own callers.*

- The system enforces authorization boundaries on operation identity.
- The system protects against replay attacks.
- The system isolates tenants.
- The system bounds the cardinality of keys, queues, retries, and payload sizes.
- The system protects polling endpoints from abuse.

## Observability

*Always applies.*

- The team defines SLOs or success criteria.
- The system emits metrics for saturation, errors, latency, queue age, retry rate, dropped work, duplicate work, and reconciliation drift.
- Logs and traces carry operation identity.
- Alerts fire on user impact or invariant risk, not just process health.
- Dashboards expose backlog and recovery progress.

## Testing

*Always applies.*

- Contract tests verify peer semantics.
- Failure-injection tests cover dependency outages, duplicate delivery, lost responses, delayed events, and partial writes.

## Appendix: Launch Readiness

Gate-time, not review-time. These are answered by an organization rather than a design, so a review that reports them as gaps is really just reporting that the system has not launched yet. Check them before a system carries real traffic; otherwise skip the appendix entirely.

### Operational controls

Knobs for use during an incident.

- Operators can trip kill switches.
- Operators can toggle feature flags.
- Operators can adjust rate limits.
- Operators can drain, pause, and resume queues.
- Operators can control backfills.
- Operators can manually replay work.
- Admin operations leave audit trails.

### Human ownership during recovery

- A named owner holds the component during incidents.
- A named owner handles reconciliation failures.
- A runbook describes important situations, workflows, and resources.
- Defined decisions require human approval.
- Operators gather defined evidence before declaring recovery complete.
