# ADR-0008 — Platform administration is not a module role

**Status:** Accepted

## Context

Users hold a role within each module they are granted. Separately, a few people administer the
platform itself — creating users, granting modules, setting access scopes.

Modelling both as "roles" is tempting and looks like tidying.

## Decision

Platform administration is its own boolean attribute, checked by its own function, and is never
derived from a module role. Being an administrator *of a module* grants nothing on the
administration screen.

## Rejected

**A single role enumeration covering both.** Fewer concepts, one lookup, and it means that whoever
can be made an administrator of any module can grant themselves every module. That is privilege
escalation wearing the costume of a refactor.

## Consequences

- Two checks instead of one, and a test that fails if they are ever unified.
- Default values fail closed: the administration attribute defaults to false, so any future code
  path that forgets to set it denies rather than grants.
- The module role column is currently a placeholder: the values are modelled and nothing branches
  on them yet. That is why I do not describe this system as having RBAC. A role that nothing
  enforces reads stronger than it is.
