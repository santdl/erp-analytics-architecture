# ADR-0002 — Enforce boundaries with a write-time static check

**Status:** Accepted

## Context

Five boundary rules (ADR-0001) written in a document are five rules that erode, particularly on a
codebase developed with AI assistance at high throughput, where the failure mode is not bad syntax
but a system that quietly stops having a shape.

## Decision

A static check parses every written file to an AST and blocks the write when an import violates a
boundary rule. Relative imports are resolved to their absolute form and `from package import
submodule` is expanded, because the most natural import styles would otherwise escape the check
entirely.

## Rejected

**Code review.** Two developers, no required review, and the rules are exactly the kind of thing a
reviewer does not notice.

**A CI lint job.** Catches it eventually, after the design has already been built on top of the
violation. Write time is the cheapest moment to fail.

**An import-linter configuration file.** Reasonable and would have worked; the custom check was
written because it also had to handle the deduplication subtlety below.

## Consequences

- Four of the five rules are mechanically enforced. The fifth is not, and the check says so in its
  own documentation rather than approximating it — a gate that cannot enforce a rule should declare
  that.
- It fails open on infrastructure problems, so it can never wedge an editing session.
- Message deduplication is scoped to a single statement, because a naive prefix dedup would swallow
  a genuine second violation. Silently swallowing a violation is the worst defect an architecture
  gate can have.
