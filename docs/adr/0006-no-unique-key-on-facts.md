# ADR-0006 — No unique key on the fact tables

**Status:** Accepted

## Context

The budget source contains rows that are identical across every extracted column. Separately, the
ledger source was duplicating rows for entries that exist in two currencies.

Two problems that look identical in a row count, with opposite correct fixes.

## Decision

No unique key and no `DISTINCT` on any fact table. The currency duplication is filtered at the
source query instead.

## Rejected

**A surrogate or natural unique key with deduplication.** The reflex answer on finding duplicate
rows. Here the business rule *sums* identical budget lines, so deduplicating silently deletes
budget — and the ledger also contains legitimately repeated rows (dozens of identical bank fees
posted on the same day) that a dedup would destroy too.

## Consequences

- The tables cannot enforce uniqueness, so a bug producing genuine duplicates would not be caught
  by a constraint. The parity harness is what catches that class instead.
- The reasoning is written in the schema file itself, next to the tables, because the next person
  to see duplicate rows will reach for `DISTINCT` and needs to find the argument before the keyboard.
