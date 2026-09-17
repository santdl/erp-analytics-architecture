# ADR-0005 — In-process scheduler over an orchestrator

**Status:** Accepted, with a known expiry

## Context

One pipeline, one daily schedule, one operator, and a single small host.

## Decision

A scheduler loop inside the ETL container: daily run, catch-up on boot if the last good load is
stale, two fixed retries, sliced sleeping, graceful shutdown.

## Rejected

**Airflow or Dagster.** The right tool the moment there is a second pipeline. Today it would be more
infrastructure than the thing it orchestrates, on a host that cannot comfortably carry it.

**Cron on the host.** Splits the schedule away from the deployment unit, so deploying the container
no longer deploys its behaviour, and the container stops being self-contained.

## Consequences

- The schedule travels with the image. One deployment unit.
- Recovery after an outage is automatic.
- No DAG, no backfill command, no per-stage retry, no run-history UI beyond the load ledger. A
  single failed stage cannot be re-run on its own — only the whole load.

## Expiry

The second pipeline. At that point two hand-rolled schedulers is strictly worse than one
orchestrator, and the migration should happen before the second one is written, not after.
