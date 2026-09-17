# ADR-0007 — Idempotent DDL instead of a migration tool

**Status:** Accepted, with a known expiry

## Context

Two developers, one environment, and schemas owned entirely by the ETL and rebuilt every load.

## Decision

Schema files re-applied on every run. Every statement must be `IF NOT EXISTS` or `CREATE OR
REPLACE`. Column changes on existing tables use a guarded conditional block that only runs when the
change is still needed.

## Rejected

**A migration tool.** The correct long-term answer. Rejected for now because the analytical schema
is disposable — it is rebuilt from source on every load — so most of what a migration tool protects
does not apply, and the user/permission schema changes rarely.

## Consequences

- No migration state to get out of sync, and a fresh database is one run away.
- No version history, no down-migrations, and column changes require hand-written guarded blocks.
- The guard on those blocks matters for a non-obvious reason: an unconditional `ALTER` would take
  an `ACCESS EXCLUSIVE` lock on every single load, and a slow dashboard query would queue behind it
  for no reason at all.

## Expiry

The first change that cannot be expressed as a guarded block, or the moment a second environment
exists. Both are close.
