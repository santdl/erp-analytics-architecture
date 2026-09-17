# ADR-0004 — Full refresh over incremental ingestion

**Status:** Accepted, with a known expiry

## Context

The source is a hosted ERP with accounting data that is corrected retroactively: an entry from three months ago can change today. Volumes are moderate.

## Decision

Extract the whole window every run, replace the raw layer, rebuild the analytical layer.

## Rejected

**Watermarked incremental load.** The natural answer, and wrong here without more work:
retroactive corrections mean a high-water mark on the entry date misses exactly the edits that
matter most. Doing it correctly requires a modification timestamp that the source does not reliably
provide, or change data capture, which is not available on this source.

**Change data capture.** Not available.

## Consequences

- Idempotent by construction. Re-running produces the same state, which makes recovery trivial.
- Retroactive corrections are picked up for free.
- Everything is materialized in Python memory, single-threaded, with no chunking. There is no
  measured row ceiling.

## Expiry

Revisit at roughly a million rows in the largest fact table, or when the load stops fitting
comfortably in its window — whichever comes first. The replacement is a modification-timestamp
watermark with a periodic full reconciliation, not a pure incremental.
