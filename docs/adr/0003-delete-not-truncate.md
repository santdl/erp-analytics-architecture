# ADR-0003 — `DELETE`, not `TRUNCATE`, when rebuilding the analytical layer

**Status:** Accepted

## Context

The analytical layer is rebuilt completely on every load. Users read those same tables at any hour,
and loads can be triggered outside the scheduled window.

## Decision

Empty the tables with `DELETE` inside the rebuild transaction.

## Rejected

**`TRUNCATE`.** Faster, and the obvious choice. It takes an `ACCESS EXCLUSIVE` lock, so any
dashboard query queues behind the rebuild; and it does not respect MVCC, so a transaction holding
an earlier snapshot suddenly sees an empty table. A user loading a page mid-rebuild would see
zeros, with no error anywhere.

**Rebuild into a shadow schema and swap.** Correct, and the right answer at a larger volume. More
moving parts than the current scale justifies.

## Consequences

- A reader during the rebuild sees the entire previous model until the commit.
- `DELETE` is slower and leaves dead tuples for autovacuum. Measured as acceptable at this volume;
  the shadow-swap option is the documented next step if it stops being.
- Pinned by a concurrency test, because a future performance optimization would otherwise undo this
  quietly and nobody would see the consequence until a user did.
