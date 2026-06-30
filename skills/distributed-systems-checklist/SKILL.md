---
name: distributed-systems-checklist
description: Use to build or review a robust distributed system.
---

# Distributed Systems Checklist

Entities:

- The system: runtime behavior (persists, sheds, expires, isolates, bounds…)
- The design / The team: decisions made up front (states assumptions, names invariants, defines SLOs)
- Peers: cross-system contracts (agree on, define)
- Operators: incident-time human actions (trip, toggle, drain, gather evidence)
- A named owner: accountability during recovery

## Robustness and Resumability

- The design states expected latency, tolerances, and timeouts.
- The design states expected traffic loads, tolerances, and behavior when they deviate.
- The system persists state durably, so work resumes after interruption or outage.
- The system resumes incomplete operations.
- The system applies admission control or backpressure to govern how fast deferred work re-enters the active set.
  e.g. backoff, jitter, bulkhead, and scheduling.
- The system can shed work early to prevent overload, where deliberately permitted.
- Each operation carries an identity, minted at a defined point, which makes duplicate submission _safe_.
  e.g. externally-supplied id or name, or system-provided.
- The system keeps identities stable across operations that should be retried, but distinct across operations that should stay separate.
- Querying an operation by identity returns a rich status — unknown, in progress, or complete with outcome.
  This makes lost responses _recoverable_.
- The system treats notifications as optimizations and relies on polling to guarantee delivery.

## Data Lifecycle

- The system retains operation records, idempotency keys, events, logs, and reconciliation evidence for defined windows.
- The system handles client retries that arrive after retention expiry.
- The system honors privacy and deletion requirements for durable workflow state.

## Degradation Behavior

- The system sheds functionality in a deliberate, predefined order under load or dependency failure.
- The system defines whether degradation is automatic or operator-driven.
- Degraded responses carry a marker distinguishing them from normal ones.
- The system returns stale, partial, cached, approximate, or best-effort results only where deliberately permitted.

## Dependency Contracts (defending against what we depend on)

- For each dependency, the system defines circuit-breaker, fallback, retry, and timeout behavior.
  Fallback terminology: fail-open = continue; fail-closed = stop.
- The system assigns trust and lifetime to each dependency's responses, e.g. authoritative, cacheable, advisory.
- The system responds to a dependency outage with replay, reconciliation, or deliberate inaction.

## Peer Contracts (mutual promises between systems)

- Peers agree on ordering and delivery semantics.
- Peers state their consistency guarantee explicitly (probably eventual); see also Invariants.
- Peers define atomicity for effects spanning two or more systems.
- Peers agree on schema and version compatibility.

## Failure Boundaries

- No single tenant, region, shard, queue, partition, or dependency can exhaust capacity for everyone.
- Bulkheads separate online traffic, background work, retries, backfills, and admin jobs.
- The system handles partial dependency degradation, not just full outage.

## Invariants

- The design names the correctness properties that must always hold, even during failures.
- The system enforces each invariant synchronously, asynchronously, or only by reconciliation.
- The design names what may be temporarily wrong.

## Reconciliation (the path back to health)

- The system provides a defined reconciliation mechanism.
- Reconciliation runs with an owner, schedule, scope, and safety limits.
- Reconciliation identifies inconsistent state.
- Reconciliation distinguishes “needs repair” from “valid terminal divergence.”
- Repair actions are idempotent and auditable.

## Time

- The design states its clock-skew assumptions.
- The system propagates deadlines where needed, or deliberately does not.
- The system expires leases and sessions on an explicit schedule.
- The system enforces explicit TTLs and retention windows.
- The system bounds how long retries remain valid.
- The system bounds operation-identity lifetime, or deliberately keeps it unbounded.
- The system defines whether stale messages/events can still mutate state.

## Cancellation And Supersession

- The system can cancel in-flight work.
- Newer work can supersede older work.
- The system handles cancelled work that completes anyway.
- The system resolves cancellation/completion races safely.

## Observability

- The team defines SLOs or success criteria.
- The system emits metrics for saturation, errors, latency, queue age, retry rate, dropped work, duplicate work, and reconciliation drift.
- Logs and traces carry operation identity.
- Alerts fire on user impact or invariant risk, not just process health.
- Dashboards expose backlog and recovery progress.

## Operational Controls (knobs for use during an incident)

- Operators can trip kill switches.
- Operators can toggle feature flags.
- Operators can adjust rate limits.
- Operators can drain, pause, and resume queues.
- Operators can control backfills.
- Operators can manually replay work.
- Admin operations leave audit trails.

## Deployment And Migration Safety

- Rolling deploys preserve backward and forward compatibility.
- The system behaves correctly when two versions run concurrently.
- Database and schema migrations apply in a safe, defined order.
- Event, protobuf, and schema formats evolve compatibly.
- Deploys are safe to roll back.
- Old workers process new messages, and new workers process old messages.

## Security And Abuse

- The system enforces authorization boundaries on operation identity.
- The system protects against replay attacks.
- The system isolates tenants.
- The system bounds the cardinality of keys, queues, retries, and payload sizes.
- The system protects polling endpoints from abuse.

## Testing

- Contract tests verify peer semantics.
- Failure-injection tests cover dependency outages, duplicate delivery, lost responses, delayed events, and partial writes.

## Human Ownership During Recovery

- A named owner holds the component during incidents.
- A named owner handles reconciliation failures.
- A runbook describes important situations, workflows, and resources.
- Defined decisions require human approval.
- Operators gather defined evidence before declaring recovery complete.
