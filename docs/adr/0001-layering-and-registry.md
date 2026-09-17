# ADR-0001 — Layered architecture with a module registry

**Status:** Accepted

## Context

The platform had to serve multiple business domains to one audience through one login, and to grow
a second delivery channel (a chat bot) later. A single-domain application would have been faster to
build and would have had to be taken apart the first time a second domain arrived.

## Decision

Three layers — core, channels, domains — with a composition root as the only module that knows all
three. Domains register themselves; channels read the registry and never name a domain. Core holds
channel-created objects as opaque references so it does not depend on the channel.

## Rejected

**A single application package with modules by convention.** Cheaper on day one. The dependency
graph then grows by accident rather than by decision, and the first shared helper between two
domains fuses them permanently.

**A plugin system with dynamic discovery.** More flexible than needed for a handful of domains, and
it trades an explicit registration list for a source of runtime surprises.

## Consequences

- Authorization is written once and both channels call the same function.
- Adding a domain touches the composition root and nothing else.
- The registry is mutable global state populated at boot with nothing preventing a later mutation.
  The guarantee is convention, and that is documented rather than claimed otherwise.
